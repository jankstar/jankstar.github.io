---
title: "2.1 System Requirements and Installation"
description: "Installing Tryton: trytond and web client sao, trytond.conf, protecting passwords, PostgreSQL database, initial setup, server, worker and cron."
weight: 10
---

This page describes the installation of the Tryton server (trytond) and the web client **sao**, the configuration and the setup of the database. Required are Python 3.13, Node.js with npm, git and a PostgreSQL server with a database user `tryton`.

{{% callout style="warning" title="For development systems only" %}}
The descriptions on this page cover a development or test system. Productive operation requires additional measures (among others securing server, database and access, encryption, backups). Every operator is responsible for the security of their own systems.
{{% /callout %}}

## Install the Tryton Server

1. Create and enter the project directory:

   ```sh
   cd /path/to/your/project/tryton
   ```

2. Create a new Python virtual environment:

   ```sh
   python3.13 -m venv .venv
   ```

3. Activate the environment:

   ```sh
   source .venv/bin/activate
   ```

4. Install Tryton with all modules via the `requirements.txt`:

   ```sh
   pip install -r requirements.txt
   ```

## Install the Web Client sao

1. Enter the project directory; sao is installed in a directory of its own below it:

   ```sh
   cd /path/to/your/project
   git clone --single-branch --branch 8.0 https://github.com/tryton/sao.git
   ```

   The version of sao (here 8.0) must match the version of trytond.

2. Install the web app in the directory with Node:

   ```sh
   cd sao
   npm install --production --legacy-peer-deps
   ```

3. Build the web app with Grunt:

   ```sh
   npx grunt
   ```

## Configure the Server

### trytond.conf

The file `trytond.conf` holds the database connection, the web server with the sao directory and the settings of the module, e.g.:

```ini
[database]
# SQLite instead of PostgreSQL:
# uri = sqlite://
# path = /path/to/the/databases
uri = postgresql://tryton:<password>@localhost:5432/tryton

[attachment]
# filestore = True      (False = content stored directly in the database)
# store_prefix =        (sub-directory, default: name of the database)
# retention_days = 90   (deleted files are only removed after x days)

[web]
listen = 0.0.0.0:8000
# sao directory from the installation above
root = /path/to/your/project/sao

[queue]
worker = True

[bus]
allow_subscribe = True

[real_estate]
genesis_token = <access key>
```

- **[queue] worker** runs background tasks, e.g. **Ausführen** (Execute) of an adjustment run.
- **[bus] allow_subscribe** is needed for notifications in the client.
- **[real_estate] genesis_token** is the access key for GENESIS-Online (Destatis) used to fetch the price indices, see [6.7.2 Index Rent]({{< relref "/tryton-real-estate/vertraege/mietanpassungen/indexmiete" >}}).

{{% callout style="warning" %}}
Comments go on a line of their own starting with `#`. A comment after a value (`root = /path  # comment`) is read as part of the value. Password and access key belong only in the local file, not in a repository.
{{% /callout %}}

### Protecting Passwords

Passwords and access keys do not have to be in `trytond.conf`:

- **Database without password:** if Tryton and PostgreSQL run on the same machine, trytond connects via the Unix socket (`uri = postgresql:///`); PostgreSQL checks the operating system user via `peer` (entry `local … peer` in `pg_hba.conf`).
- **`~/.pgpass`:** the URI only contains `postgresql://tryton@localhost:5432/`, the password is in the file `~/.pgpass` of the user running trytond (`localhost:5432:*:tryton:<password>`, permissions `600`).
- **Environment variables:** trytond takes values from variables named `TRYTOND_<SECTION>__<OPTION>`, e.g. `TRYTOND_DATABASE__URI` or `TRYTOND_REAL_ESTATE__GENESIS_TOKEN`. They are set in the start script, e.g. from the Mac keychain (`security find-generic-password … -w`) or a password manager.
- **Containers:** mount secrets at runtime as *Docker secrets* or Kubernetes `Secret` and set the environment variables from them – never write them into the image or Dockerfile.

In general `trytond.conf` belongs only to the user running trytond (permissions `600`) and never into a repository. The database user only needs rights on its own database.

### logconf.conf

Logging is defined in a file of its own, here rotating daily in `tryton.log` (30 files) and additionally to the console:

```ini
[formatters]
keys=simple

[handlers]
keys=rotate,console

[loggers]
keys=root

[formatter_simple]
format=[%(asctime)s] %(levelname)s:%(name)s:%(message)s
datefmt=%a %b %d %H:%M:%S %Y

[handler_rotate]
class=handlers.TimedRotatingFileHandler
args=('tryton.log', 'D', 1, 30)
formatter=simple

[handler_console]
class=StreamHandler
formatter=simple
args=(sys.stdout,)

[logger_root]
level=INFO
handlers=rotate,console
```

## Manage the Database

The database is managed with the PostgreSQL tools; user and name (here both `tryton`) must match the `trytond.conf`.

{{% callout style="warning" title="Caution" %}}
Run the following actions with care – dropping and restoring in particular overwrite data irrevocably.
{{% /callout %}}

| Action | Command |
|--------|---------|
| Create the database | `createdb -U tryton tryton` |
| Drop the database | `dropdb -U tryton tryton` |
| Back up the database | `pg_dump -U tryton -Fc -d tryton > tryton_$(date +%Y%m%d_%H%M%S).dump` |
| Restore the database | `pg_restore -U tryton -d tryton tryton_<date_time>.dump` |

The backup creates a dump in PostgreSQL format with the database name and date/time in the file name.

trytond is now installed, the configuration is set and an empty database is set up.

## Initialise the Database and Start the Server

1. On a new database `trytond-admin` creates all tables and initialises Tryton:

   ```sh
   trytond-admin -c trytond.conf -d tryton --all
   ```

   The shell asks for the e-mail address and the password of the user **admin**:

   ```text
   email for "admin" of "tryton" (empty for none): <email>
   password for "admin" of "tryton": <password>
   password confirmation for "admin": <password>
   ```

2. Start the server:

   ```sh
   .venv/bin/trytond -c trytond.conf --logconf logconf.conf -d tryton --dev
   ```

   `--dev` is the development mode (among other things, changed files are reloaded); for production start without `--dev`.

3. Open **http://localhost:8000/#tryton** in the browser and log in as **admin**.

## Initial Setup in the Client

After the first login the wizard **Module Configuration** guides through the initial setup. The forms are in English at first.

1. **Log in** with database `tryton`, user **admin** and the password from `trytond-admin`.

   ![Login](erst-01-login.png)

2. The wizard **Module Configuration** starts; continue with **OK**.

   ![Module Configuration](erst-02-modulkonfiguration.png)

3. **Configure Languages:** mark the languages whose translations are to be loaded, e.g. English and German, then **Load**.

   ![Configure Languages](erst-03-sprachen.png)

4. **Configure Modules:** mark the required modules, e.g. `real_estate` – dependent modules are activated as well –, then **Activate**.

   ![Configure Modules](erst-04-module-aktivieren.png)

5. **Perform Pending Activation/Upgrade** lists all modules to be activated (here 73); start with **OK**. The server creates the tables, the log shows the registered models. Finally the client reports “The modules have been upgraded / activated.”

   ![Modules to activate](erst-05-aktivierung-liste.png)

   ![Server log during activation](erst-06-aktivierung-log.png)

   ![Activation done](erst-07-aktivierung-fertig.png)

6. The **Module Configuration** continues; go on with **OK**.

   ![Continue Module Configuration](erst-08-konfiguration-start.png)

7. **Configure Company:** after the note the company is created. In the field **Party** enter a new party with name, language and address and save it; choose currency (Euro) and timezone (Europe/Berlin). Employees are optional. Create with **Add**.

   ![Configure Company – note](erst-09-unternehmen-info.png)

   ![Configure Company](erst-10-unternehmen.png)

   ![Party of the company](erst-11-unternehmen-partei.png)

8. The progress is shown; continue with **Next**.

   ![Progress](erst-12-konfiguration-fortschritt.png)

9. **Create Chart of Accounts from Template:** after the note choose company and account template, e.g. **Kontenrahmen der Wohnungswirtschaft (WoWi)**, then set the default accounts for receivables, payables, revenue and expense and create them with **Create**.

   ![Chart of accounts – note](erst-13-kontenplan-info.png)

   ![Chart of accounts – choose template](erst-14-kontenplan-vorlage.png)

   ![Chart of accounts – default accounts](erst-15-kontenplan-konten.png)

10. “The configuration is done.” – finish with **OK**.

    ![Configuration done](erst-16-konfiguration-fertig.png)

The menu with **Immobilien** (real estate) is now available. The user menu at the top right (**Einstellungen…**, preferences) allows, among other things, setting the user's language.

![Menu after the initial setup](erst-17-menue.png)

Further modules can be activated later, see [2.2 Activate Modules]({{< relref "/tryton-real-estate/system/module" >}}).

## Starting Server, Worker and Cron

For full operation three processes run side by side, each in its own terminal in the project directory:

| Process | Task |
|---------|------|
| Server (`trytond`) | Login and use via the client |
| Worker (`trytond-worker`) | Background processing (queue), e.g. **Ausführen** (Execute) of an adjustment run; requires `[queue] worker = True` |
| Cron (`trytond-cron`) | Scheduled jobs, e.g. fetching the price indices, see [2.6 Jobs and Periodic Tasks]({{< relref "/tryton-real-estate/system/jobs" >}}) |

The **server** is started as described above:

```sh
.venv/bin/trytond -c trytond.conf --logconf logconf.conf -d tryton --dev
```

The **worker** for background processing:

```sh
export TRYTOND_CONFIG=/path/to/your/project/tryton/trytond.conf
.venv/bin/trytond-worker -c trytond.conf --logconf logconf.conf -d tryton --dev
```

The **cron** for scheduled jobs:

```sh
export TRYTOND_CONFIG=/path/to/your/project/tryton/trytond.conf
.venv/bin/trytond-cron -c trytond.conf --logconf logconf.conf -d tryton --dev
```

If the worker is not running, background tasks stay in the queue; if the cron is not running, no jobs are executed.
