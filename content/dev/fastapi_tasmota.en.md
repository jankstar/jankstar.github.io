---
title: "fastapi Server / mqtt client / pyrc - PWA"
date: 2024-12-27T11:32:28+02:00
weight: 1
description: "Python fastapi Server with mqtt client to receive data from MT681 device via tasmota SmartMeterReader and FritzBox SmartMeter devices (py_hab)"
---

This `fastapi` server enables access to a sqlite DB (`sqlalchemy`) with SmartMeter data from an mqtt broker and the SmartMeter data from a FritzBox. 
The database therefore contains the measurement data and devices and this data is made available via `pydantic` and `fastapi`.
In addition, the `fastapi` server starts a scheduler (`apscheduler`) in a background process - the SmartMeter data of the `tasmota` SmartMeter from `bitShake` with connection to the electricity meter and also the SmartMeter devices of the FritzBox are read out via background processes using an mqtt client. This read data is periodically written to the sqlite DB and is then available again via the `fastapi` server.
In addition, a reorganization job is started that deletes redundant data and performs data aggregation or normalization.
To ensure that the scheduler and job information is available to all work processes of the `fastapi` server, an `rpyc` server and client are also implemented locally, i.e. the background job starts the scheduler and the `rpyc` server and makes this data available locally to the `fastapi` processes.

## Installation - clone and pip 

Please get standard installation of python on your system and install this server as follows:<br>
```shell
git clone https://github.com/jankstar/py_hab.git
cd py_hab
python3 -m venv env
pip install -r requirements.txt 
./start.sh
```
 
## Environtment - .env for defines.py
We use '.env' for defines:
```txt
FRITZBOX_URL = "http://xxx.xxx.xxx.xxx"
USERNAME = "*******"
PASSWORD = "*******"
DATABASE = 'sqlite:///datenbank.db'
MQTT_SERVER = "xxx.xxx.xxx.xxx"
MQTT_PORT = 1883
MQTT_CLIENT_ID = "*******"
SLEEP_TIME = 600
WAIT_TIME = 10
TIMEZONE = 'Europe/Berlin'

FRONTEND_URL= 'http://xxx.xxx.xxx.xxx:8000'
RPYX_PORT=8001

# -- SESSION --
# Secret phrase for session encryption
SESSION_SECRET=xxxxxxxxxxx
```

This file will be used in ```define.py```:
```python
FRITZBOX_URL = os.getenv('FRITZBOX_URL',"http://0.0.0.0")
USERNAME = os.getenv('USERNAME', "") 
PASSWORD = os.getenv('PASSWORD', "")
DATABASE = os.getenv('DATABASE','sqlite:///datenbank.db')
MQTT_SERVER = os.getenv('MQTT_SERVER',"0.0.0.0")
MQTT_PORT = int(os.getenv('MQTT_PORT',1883)) #1883
MQTT_CLIENT_ID = os.getenv('MQTT_CLIENT_ID', "TEST") 
SLEEP_TIME = int(os.getenv('SLEEP_TIME',600)) #600
WAIT_TIME = int(os.getenv('WAIT_TIME',10)) #10
TIMEZONE = os.getenv('TIMEZONE', 'Europe/Berlin')
SESSION_SECRET = os.getenv('SESSION_SECRET', 'secret')
FRONTEND_URL = os.getenv('FRONTEND_URL',"http://localhost:8000")
RPYX_PORT = int(os.getenv('RPYX_PORT',8001))
DEFAULT_LIMIT = 200
MODE = 'NO_DEBUG' #'DEBUG'
```

## REST api Server - fastapi and pydantic

The ```Model.py``` defines the structure for the API and automatically converts from the DB structure.

```python
class MeasurementData(Base):
    '''Measurement as sqlalchemy model'''
    __table__ = Table('measurement_data', Base.metadata,
        Column('id', String, primary_key=True), # key id, we use UUID
        Column('device', String),      # device
        Column('topic', String),       # topic of mqtt
        Column('entity', String),      # sensor
        Column('sub_entity', String),  # sensor sub device
        Column('time', String),        # timestamp of measurement
        Column('amount', Float),       # value of measurement
    )

    __table_args__ = (
            Index('idx_01', 'device', 'sub_entity', 'time'),
        )
    
    def __repr__(self):
        return f"<measurement(topic='{self.topic}', time={self.time}, device={self.device}, amount={self.amount}, )>"

class Measurement(BaseModel):
    '''Measurement as pydantic model'''
    id: str
    device: str
    topic: str
    entity: str
    sub_entity: str
    time: str
    amount: float

    model_config = ConfigDict(from_attributes=True)

class DeviceData(Base):
    '''Device as sqlalchemy model'''
    __table__ = Table('device_data', Base.metadata,
        Column('device', String, primary_key=True),     # device
        Column('sub_entity', String, primary_key=True), # sensor sub device
        Column('device_name', String),
        Column('product_name', String),
        Column('manufacturer', String),
        Column('firmware_version', String),
        Column('unit', String),               # unit of measurement
        Column('cumulative', Boolean),        # if measurement is cumulative
    )

    def __repr__(self):
        return f"<device(device={self.device}, device_name={self.device_name}, unit={self.unit})>"

class Device(BaseModel):
    '''Device as pydantic model'''
    device: str
    sub_entity: str
    device_name: str
    product_name: str
    manufacturer: str
    firmware_version: str
    unit: str
    cumulative: bool

    model_config = ConfigDict(from_attributes=True)    

```
There is an indicator for distinguishing cumulative measurements, e.g. power consumption, i.e. this value increases with each measurement. In contrast, the power or temperature is the value at the exact time of the measurement.<br>
So we have an DB structure and an pydantic structure for the fastapi api json response. 

## Database and sqlalchemy

We use sqlite db - so we can only run the sync driver and sync session make.

```python 
def get_db_session_sync():
    '''get_db_session    start and get database Session'''
    return sessionmaker(bind=get_db_sync())

def get_db_sync():
    '''get_db_sync    start and get database connection'''
    return create_engine(DATABASE,
                         pool_size=20,
                         max_overflow=30)
```
The ```.env``` define ```DATABASE = os.getenv('DATABASE','sqlite:///datenbank.db')```. The function ```get_db_session_sync()``` is used as ```db: Session = Depends(get_db_session_sync)``` at endpint-function. In the fastapi endpoint we execute sql to grep data and move it to ```response_model```.
```python
@router.get("/api/mesurement/{device}/{sub_entity}", response_model=list[Measurement])
async def get_measurement_by_device_sub_entity(device: str, sub_entity: str,limit: int = DEFAULT_LIMIT, offset: int = 0, db: Session = Depends(get_db_session_sync)):
    '''get_measurement get measurement data by device and sub_entity from database'''

    try:
        session = db()
        last_data = session.scalars(select(MeasurementData)
                                    .where(and_(MeasurementData.device == device, MeasurementData.sub_entity == sub_entity))
                                    .order_by(MeasurementData.time.desc())
                                    .offset(offset)
                                    .limit(limit)).all()
        session.close()
        result = [Measurement.model_validate(data) for data in last_data]
    except Exception as e:
        logger.error("Error while getting mesurement by device and sub_entity: " + str(e))
        raise HTTPException(status_code=404, detail="No Items found")

    return result
```
The result line ```result = [Measurement.model_validate(data) for data in last_data] ``` converts the data from DB to pydantic as json ```response_model=list[Measurement] ``` .


## Scheduler - apscheduler and rpyc

We use ```rpyc``` server for the scheduler ```apscheduler```.  If we start the fastapi server via unicorn with worker, we need only one scheduler and all worker needs access to the scheduler. The solution is stand alone ```rpyc``` server for the ```apscheduler```. The ```lifespan``` from ```fastapi``` is cheching ```fcntl.lockf()``` via file and starts one task für the server.
```python
@asynccontextmanager
async def lifespan(app: FastAPI):
    # Code for Startup
    app.server = None
    app.scheduler = None
    app.scheduler_task = None
    try:
        #we need only one scheduler

        # +++++++
        _ = open("/tmp/fastapi.lock","w")
        _fd = _.fileno()
        fcntl.lockf(_fd,fcntl.LOCK_EX|fcntl.LOCK_NB)
        # +++++++

        app.scheduler_task =  asyncio.create_task(background_process(app))

    except BlockingIOError:
            pass

    app.logger.info("Server starts...")

    yield
    # Code  for Shutdown
    if app.server != None: app.server.close()
    if app.scheduler != None: app.scheduler.shutdown()
    if app.scheduler_task != None: app.scheduler_task.cancel(msg='Shutting down...')
    app.logger.info("Server shuts down...")
```
The ```asyncio.create_task(background_process(app))``` starts the server in background.
The ```rpyc.server``` is quit simple, we define an services class for the remote access from fastapi async anedpont.
```python
async def background_process(app: FastAPI):
    '''background_process() starts the scheduler ansd the rpyc-server'''
    app.logger.info("background_process() started")

    app.scheduler = BackgroundScheduler()
    app.scheduler.configure(
        misfire_grace_time=300,  # 5 Minuten
        coalesce=True
    )
    
    # Add background task
    app.scheduler.add_job(periodic_task, 'date', id='periodic_task_1', run_date=datetime.now() + timedelta(seconds=3))
    app.scheduler.add_job(periodic_task, 'interval', id='periodic_task_2', seconds=SLEEP_TIME)
    app.scheduler.add_job(reorganisation_task, 'interval', id='reorganisation_task', seconds=60*60*24)
    app.scheduler.start()

    protocol_config = {"allow_public_attrs": True, "allow_pickle": True}
    app.server = ThreadedServer(
       SchedulerService, port=RPYX_PORT, protocol_config=protocol_config
    )

    try:
        app.server._start_in_thread()
        #blocking server start
        while True:
            await asyncio.sleep(1)
    except Exception as e:
        app.logger.error("Error while Server stopped with: "+str(e))  
       
    app.servers.close()
    app.servers = None

    app.scheduler.shutdown()
    app.scheduler = None
```
If we starts services remotely, we need ```{"allow_public_attrs": True, "allow_pickle": True} ``` elsewhere only string parameters will work. 
In this example we starts fix job for the mqtt client and reading FritzBox. The second job cleans up redundant data and performs periodic summarizations.

Fastapi endpint ```/jobs``` is getting the jobs via remote access.
```python
@app.get("/jobs")
async def get_jobs():
    jobs = []
    try:
        conn = rpyc.connect("localhost", RPYX_PORT)
        conn_jobs  = conn.root.get_jobs() 
        jobs = copy.deepcopy(conn_jobs) # deep copy required for build response
        conn.close() 
    except Exception as e:
        app.logger.error("Error while getting jobs: " + str(e))
        raise HTTPException(status_code=404, detail="No Items found")
        

    return [{"id": f"{obj.id}", "func": f"{obj.func.__name__}", "trigger": f"{obj.trigger}", "next_run_time": f"{obj.next_run_time}"} for obj in jobs]

```

## mqtt client

The sensor sends the measured values every 6 seconds via the ```tasmota``` server via mqtt broker. Background job ```periodic_task()`` is started every 10 minutes via the scheduler ```apscheduler``` and then waits 10 seconds to receive one measurement. The received data is then stored in the SQLite DB.
```python
def periodic_task():
    '''
    start mqtt client and save data to database one time
    '''

    logger = logging.getLogger("py_hab")
    engine = get_db_sync()
    Base.metadata.create_all(engine)
    Session = sessionmaker(bind=engine)

   
    mqttc = mqtt.Client(mqtt.CallbackAPIVersion.VERSION2,protocol=mqtt.MQTTv5, client_id=MQTT_CLIENT_ID)
    properties = Properties(PacketTypes.CONNECT)
    properties.SessionExpiryInterval = 3600  # Sets the session runtime to 1 hour

    userdata = {
        'engine': engine,
        'session': Session,
        'logger': logger
    }

    mqttc._userdata = userdata
    mqttc.on_connect = on_connect
    mqttc.on_message = on_message

    try:
          
        mqttc.connect(MQTT_SERVER, MQTT_PORT, 60, clean_start=False, properties=properties)
        mqttc.loop_start()
        time.sleep(WAIT_TIME)  # if mqtt does not buffer, all measurement messages are read from these 10 sec 
        mqttc.disconnect()
        mqttc.loop_stop()
        if (USERNAME != "" and PASSWORD != ""):
            logger.info("Check FritzBox ...")
            connect_fritzbox(userdata)



    except (KeyboardInterrupt, SystemExit):
        pass
    finally:
        mqttc.disconnect()
        mqttc.loop_stop()
```
Only in the period between `mqttc.loop_start()` and `mqttc.loop_stop()` are the messages from the broker received and saved in the database. The `tasmota` server only sends `qos=0` messages that are not buffered. Two event functions are registered: `on_connect()` for establishing the connection and `on_message()` for processing the messages. The structure ```_userdata``` is passed to the event functions and contains the DB session maker and the logger.

## FritzBox 

The code is from
[FritzBox forum](https://www.ip-phone-forum.de/threads/aha-http-interface.318229/)
We need user/password on FritzBox with smartMeter credentials.
The temperature from the radiator controller is read and stored in the DB. The battery status and the opening or closing of the window contacts are also stored.

(2024/12/27)<br>
[Index](https://jankstar.de/index)