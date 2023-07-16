---
title: "Rust: Tauri + Diesel + SQLite"
date: 2023-07-16T13:38:28+02:00
type: docs
weight: 1
---
# Rust: Tauri + Diesel + SQLite

Small documentation, tips and experiences for accessing a SQLite database from Rust with the ORM library `diesel`.  
Complete and possibly extended code can be found in the repository [archive_cat](https://github.com/jankstar/archive_cat).

## Requirements

I use the following library
```toml
[build-dependencies]
tauri-build = { version = "1.3", features = [] }

[dependencies]
tauri = { version = "1.4.1", features = ["shell-open", "window-close"] }
serde = { version = "1.0", features = ["derive"] }

serde_json = { version = "1.0", features = ["raw_value"] }
home = "0.5.5"
diesel = { version = "2.1.0", features = ["sqlite", "64-column-tables"] }
dotenvy = "0.15.7"
chrono = "0.4.26"
url = "2.4.0"
tokio = { version = "1.28.2", features = ["full"] }
tracing = "0.1.37"
tracing-subscriber = "0.3.17"
base64 = "0.21.2"

```
If necessary, install the libraries with ``cargo add xxx``.

For the tauri installation please look here [TAURI](https://tauri.app/) and for `diesel` here [diesel.rs](https://diesel.rs/).

   ***

## Connection with the database {#Connection}

The usual way is the access via a ``.env`` file. The access is somewhat dependent on the database, i.e., there are also databases that support pool-connections.
For SQLite, the ``SqliteConnection::establish`` function should be used.

Example 1.1 `database.rs`:
```rust
use std::env;

use diesel::prelude::*;
use dotenvy::dotenv;
use tracing::info;

pub fn establish_connection() -> SqliteConnection {
    info!("start establish_connection()",);
    
    dotenv().ok();

    let database_url = env::var("DATABASE_URL").expect("DATABASE_URL must be set");
    SqliteConnection::establish(&database_url)
        .unwrap_or_else(|_| panic!("Error connecting to the database: {}", database_url))
}
```
Here the library ``dotenvy`` is taken for loading the ``.env`` file. The variable here has the name ``DATABASE_URL``.

Example `.env`
```
DATABASE_URL=sqlite:///Users/jankstar/tauri_database.db
```
The database connection is then used as follows:
```rust
use crate::database::*;
...
    let mut conn = establish_connection();
...
```
Alternatively, the user's `home` directory is accessed.

Example 1.2 `database.rs`
```rust
use diesel::prelude::*;
use tracing::info;
use home::home_dir;

pub fn establish_connection(database_filename: &str) -> SqliteConnection {
    info!("start establish_connection()",);

    let home_dir = home_dir().unwrap_or("").to_string(); 

    let database_url = format!("sqlite://{}/{}",home_dir.to_string_lossy(), database_filename);

    SqliteConnection::establish(&database_url)
        .unwrap_or_else(|_| panic!("Error connecting to the database: {}", database_url))
}
```
The database connection is then used as follows:
```rust
use crate::database::*;
...
    let mut conn = establish_connection("tauri_database.db");
...
```

   ***


## Model and schema {#Model_and_Schema}
The model defines the structure and the schema the database table. 
There is also the possibility to generate the rust objects from the database. 
I proceeded like this, but I had to adjust certain fields and settings, so here I present the working result.

Example 2.1 `models.rs`
```rust
use chrono::NaiveDateTime;
use diesel::{ Insertable, Queryable, Selectable, Table};
use serde::{Deserialize, Serialize};

#[derive(Serialize, Deserialize, Debug, Selectable, Insertable, Queryable)]
#[diesel(table_name = crate::schema::document)]
pub struct Document {
    pub id: String,
    pub subject: String,
    pub status: String,
    pub date: String,
    pub sender_name: Option<String>,
    pub sender_addr: Option<String>,
    pub recipient_name: Option<String>,
    pub recipient_addr: Option<String>,
    pub from: Option<String>,
    pub to: Option<String>,
    pub body: Option<String>,
    pub document_type: Option<String>,
    pub metadata: Option<String>,
    pub category: Option<String>,
    pub amount: Option<f64>,
    pub currency: Option<String>,
    pub template_name: Option<String>,
    pub doc_data: Option<String>,
    pub input_path: Option<String>,
    pub langu: Option<String>,
    pub num_pages: Option<f64>,
    pub protocol: Option<String>,
    pub sub_path: Option<String>,
    pub filename: Option<String>,
    pub file_extension: Option<String>,
    pub file: Option<String>,
    pub base64: Option<String>,
    pub ocr_data: Option<String>,
    pub jpg_file: Option<String>,
    pub parent_document: Option<String>,
    pub created_at: String,
    pub updated_at: String,
    pub deleted_at: Option<String>,
}
```
`schema.rs`
```rust
table! {
    document (id) {
        id -> Text,
        subject -> Text,
        status -> Text,
        date -> Timestamp,
        sender_name -> Nullable<Text>,
        sender_addr -> Nullable<Text>,
        recipient_name -> Nullable<Text>,
        recipient_addr -> Nullable<Text>,
        from -> Nullable<Text>,
        to -> Nullable<Text>,
        body -> Nullable<Text>,
        #[sql_name = "type"]
        document_type -> Nullable<Text>,
        metadata -> Nullable<Text>,
        category -> Nullable<Text>,
        amount -> Nullable<Double>,
        currency -> Nullable<Text>,
        template_name -> Nullable<Text>,
        doc_data -> Nullable<Text>,
        input_path -> Nullable<Text>,
        langu -> Nullable<Text>,
        num_pages -> Nullable<Double>,
        protocol -> Nullable<Text>,
        sub_path -> Nullable<Text>,
        filename -> Nullable<Text>,
        file_extension -> Nullable<Text>,
        file -> Nullable<Text>,
        base64 -> Nullable<Text>,
        ocr_data -> Nullable<Text>,
        jpg_file -> Nullable<Text>,
        parent_document -> Nullable<Text>,
        #[sql_name = "createdAt"]
        created_at -> Timestamp,
        #[sql_name = "updatedAt"]
        updated_at -> Timestamp,
        #[sql_name = "deletedAt"]
        deleted_at -> Nullable<Timestamp>,
    }
}
```

In this example, more than 32 fields have been defined in the `document` table. For this case, the `features` "64-column-tables" must be set in the `diesel`:
`Cargo.toml`
```toml
...
diesel = { version = "2.1.0", features = ["sqlite", "64-column-tables"] }
...
```
In addition, field names that are not defined snake_case or correspond to invalid syntax in rust, e.g. `type`, must be mapped.
```rust
 #[sql_name = "type"]
 document_type -> Nullable<Text>,
```
Here the database field `type` is mapped to the rust field `document_type`.

   ***

## 3 Selection of data {#Selection}
For the selection of data, a distinction must be made between two variants - on the one hand, a table can be accessed like an "object", on the other hand, an SQL-like query is available under `DSL`. This must be differentiated already when including the libraries.

Example 3.1 Read a document for a document ID:
```rust
use crate::database::*;
use crate::models::*;
use crate::schema;                // like document
use crate::schema::document::dsl; // like dsl::document
use crate::schema::Response;

use diesel::prelude::*;
use serde_json::json;
use tracing::{info, warn, error};
use tracing_subscriber;

...
        let mut conn = establish_connection("tauri_database.db");

        let my_document = match dsl::document   
            .filter(dsl::id.eq(my_query.id))
            .select(Document::as_select())
            .first::<Document>(&mut conn)
        {
            Ok(record) => record,
            Err(err) => {
                error!(?err, "Error: ");

                return Response {
                    dataname: data,
                    data: "[]".to_string(),
                    error: format!("{}", err),
                };
            }
        };

...
```
In the example exactly one document with a certain ID is selected. The fields of the selection are specified in `.select()`, in the example all fields. The type of the result must be specified after `.first` and must necessarily match the fields from `.select`. Here it is very easy to define and select a subset of the fields using a separate structure. The definition of the structure has the advantage that also an object and if necessary a vector to this object can be defined.

Example 3.2 - select only certain fields:
`models.rs`
```rust
#[derive(Serialize, Deserialize, Debug, Selectable, Queryable)]
#[diesel(table_name = crate::schema::document)]
pub struct  DocumentFile { 
    pub id: String,
    pub sub_path: Option<String>,
    pub filename: Option<String>,
    pub file_extension: Option<String>,
    pub file: Option<String>,
    pub base64: Option<String>,
}
```
```rust
...
        let mut conn = establish_connection("tauri_database.db");

        let my_document = match dsl::document
            .filter(dsl::id.eq(my_query.id))
            .select(DocumentFile::as_select())
            .first::<DocumentFile>(&mut conn)
        {
            Ok(record) => record,
            Err(err) => {
                error!(?err, "Error: ");

                return Response {
                    dataname: data,
                    data: "[]".to_string(),
                    error: format!("{}", err),
                };
            }
        };
...
```

An elegant variant for a dynamic selection is the definition `query` as `BoxedDsl`. This can be used to combine and dynamically generate `order_by()` or `filter()` sorts.
In the following example, a URL is parsed dynamically and the selection conditions are built depending on the URL parameters. The notation is along the lines of `solr'.

For example 3.3:
```html
http://localhost:8080/get_document?q=body:*Apple*&sort=date%20desc&rows=50
```
In this example, the string `Apple` is to be searched for in the field `body`. The records are sorted by the field `date` in descending order and the first 50 records are selected.

Example 3.4:
```rust
use crate::database::*;
use crate::models::*;
use crate::schema;
use crate::schema::document::dsl;
use crate::schema::Response;

use diesel::prelude::*;
use serde_json::json;
use tracing::info;
use tracing_subscriber;
use url::Url;
...
    let parsed_url = match Url::parse(&my_query_url) {
        Ok(result) => result,
        Err(err) => {
            return Response {
            dataname: path,
            data: "[]".to_string(),
            error: err.to_string(),
        }}
    };

    let mut query = dsl::document.into_boxed();

    let mut limit = 50;

    let mut search: String; // = "".to_string();

     //loop via URL parameter
    for pair in parsed_url.query_pairs() {
        info!(?pair, "document url pair");
        //------------------------------------
        if pair.0 == "rows" {
            //limit of rows parameter
            match pair.1.parse::<i64>() {
                Ok(v) => {
                    limit = v;
                }
                _ => {}
            };
        }
        //------------------------------------
        if pair.0 == "sort" {
            //sort parameter
            let mut sort_field_iter = pair.1.split_whitespace();
            let sort_field_name =  sort_field_iter.next().unwrap_or(r#""#);
            let sort_field_order =  sort_field_iter.next().unwrap_or(r#""#);
            match sort_field_name {
                "date" => {
                    if sort_field_order == "desc" {
                        query = query.order_by(dsl::date.desc());
                    } else {
                        query = query.order_by(dsl::date.asc());
                    }
                }
                "subject" => {
                    if sort_field_order == "desc" {
                        query = query.order_by(dsl::subject.desc())
                    } else {
                        query = query.order_by(dsl::subject.asc())
                    }
                }
                "status" => {
                    if sort_field_order == "desc" {
                        query = query.order_by(dsl::status.desc());
                    } else {
                        query = query.order_by(dsl::status.asc());
                    }
                }
                "amount" => {
                    if sort_field_order == "desc" {
                        query = query.order_by(dsl::amount.desc());
                    } else {
                        query = query.order_by(dsl::amount.asc());
                    }
                }
                _ => query = query.order_by(dsl::date.desc()),
            }
        }
        //------------------------------------
        if pair.0 == "q" {
            //where parameter
            let mut filter_field_iter = pair.1.split(':');
            let filter_field_name = filter_field_iter.next().unwrap_or(r#""#);
            let filter_field_match =  filter_field_iter.next().unwrap_or(r#""#);
            //the `*`from the transfer string into placeholder `%`for the selection
            search = String::from(str::replace(&filter_field_match, "*", "%"));
            match filter_field_name {
                "body" => query = query.filter(dsl::body.like(search)),
                "subject" => query = query.filter(dsl::subject.like(search)),
                "status" => query = query.filter(dsl::status.like(search)),
                "date" => query = query.filter(dsl::date.eq(search)),
                "amount" => {
                    //Conversion of the transfer string into a number
                    query = query.filter(dsl::amount.eq(search.parse::<f64>().unwrap_or(0_f64)))},
                "sender_name" => query = query.filter(dsl::sender_name.like(search)),
                "recipient_name" => query = query.filter(dsl::recipient_name.like(search)),
                "category" => query = query.filter(dsl::category.like(search)),

                _ => {}
            };
        }
    }

    let mut conn = establish_connection("tauri_database.db");

    match query
        .limit(limit)
        .filter(dsl::deleted_at.is_null())
        .select(DocumentSmall::as_select())
        .load::<DocumentSmall>(&mut conn)
    {
        Ok(result) => Response {
            dataname: path,
            data: json!(&result).to_string(),
            error: String::from(""),
        },
        Err(err) => Response {
            dataname: path,
            data: "[]".to_string(),
            error: err.to_string(),
        },
    }
...
```
The coding is not completely dynamic, because the used fields must have been defined in the structures of the database `schema.rs` and `models.modelrs`.
Only the selection statemnt is dynamically assembled. In the example all conversions were also checked for validity by `match`, so that no `panic` is triggered, especially with transfer values from users. 

### function `debug_query` extracts the statement for the output {#debug_query}
There is also a function to output the generated SQL statement. In this case you separate selection and execution.

Example 3.5:
```rust
...
use diesel::debug_query;
use crate::diesel::sqlite::Sqlite;
use diesel::prelude::*;
use tracing::{error, info, warn};
use tracing_subscriber;
...

        let mut conn = establish_connection("tauri_database.db");

        let exec_query = dsl::document
            .filter(dsl::id.eq(my_query.id))
            .select(DocumentFile::as_select());
        info!("debug sql\n{}", debug_query::<Sqlite, _>(&exec_query));

        let my_document = match exec_query.first::<DocumentFile>(&mut conn) {
            Ok(record) => record,
            Err(err) => {
                error!(?err, "Error: ");

                return Response {
                    dataname: data,
                    data: "[]".to_string(),
                    error: format!("{}", err),
                };
            }
        };
```
The function `debug_query` extracts the statement for the output. Afterwards the execution and further processing of the data takes place.

### sql functions `count(*)` or `sum(amount)` {#count_sum}
The standard functions in SQL for `count(*)` or `sum(x)` are available in `diesel`. But I only got the function `count(*)` to work, so here is the variant as native `sql`.

Example 3.6:
```rust
...
use crate::diesel::sqlite::Sqlite;
use crate::schema::document::dsl;
use diesel::debug_query;
use diesel::prelude::*;
use diesel::dsl::sql;
use diesel::sql_types::Double;
...

                let operation: &str;
                if path.as_str() == "chart_count" {       //parameter to control statement
                    operation = "count(id) AS count";     //count documents
                } else {
                    operation = "sum(amount) AS sum";     //sum the amount
                }

                let exec_query = document::table
                    .into_boxed()
                    .filter(
                        dsl::deleted_at
                            .is_null()
                            .and(dsl::date.le(local_start.to_string()))       //date from/to start datetime
                            .and(dsl::date.ge(local_end.to_string()))
                            .and(dsl::category.like(format!("%{}%", query)))  //query contains the selected category
                            .and(dsl::amount.is_not_null()),
                    )
                    .select(sql::<Double>(operation));

                info!("debug first sql\n{}", debug_query::<Sqlite, _>(&exec_query));
 
                let y_value = exec_query.first::<f64>(&mut conn).unwrap_or(0_f64);

```
It is a bit strange, but in the `where` condition the datatype is required to be `dubble`, because my field `amount` is of this type and the return value is `f64` - the values are converted between FromSql/ToSql and the application.

   ***

## 4 Read PDF file to base64 conversion {#read_file}
In my example the name and path of the PDF file is in the SQLite database. In the examples shown above the file name from field `file` and also the path from field `sub_path` is read to the document ID exactly for one document. The files are in the `home` directory in a main directory `MAIN_PATH` belonging to the program and in this then in a `FILE_PATH`. 

Example 4.1 Definition of constants for the directories to be used in the `home` directory:
`database.rs`
```rust
pub const MAIN_PATH: &str = r#"archive"#;
pub const DATABASE_NAME: &str = r#"tauri_database.db"#;
pub const FILE_PATH: &str = r#"data"#;
```
After the selection of the data from the database the following determination of the `home` directory takes place:

Example 4.2:
```rust
        info!(?my_document.id, "select document id" );
        info!(?my_document.sub_path, "select document subpath" );

        use home::home_dir;
        let home_dir = match home_dir() {
            Some(result) => result,
            None => {
                return Response {
                    dataname: data,
                    data: "[]".to_string(),
                    error: r#"no pdf found"#.to_string(),
                };
            }
        };

        let filename = my_document.file.unwrap_or("".to_string());
        if filename.is_empty() {
            return Response {
                dataname: data,
                data: "[]".to_string(),
                error: r#"no pdf found"#.to_string(),
            };
        }

        //Build PDF Filenames
        let pdf_file = format!(
            "{}/{}/{}/{}{}",
            home_dir.to_str().unwrap_or("").to_string(),
            MAIN_PATH,
            FILE_PATH,
            my_document.sub_path.unwrap_or("".to_string()),
            filename
        );
        info!(?pdf_file, "select document file");

```
At the end `pdf_file` contains the complete path for accessing the file, so that now the file can be opened and loaded as binary into a variable `list_of_chunks` of type `Vec<u8>`.

Example 4.3:
```rust
        //open file by name
        let mut file = match std::fs::File::open(&pdf_file) {
            Ok(file) => file,
            Err(err) => {
                error!(?err, "Error: ");

                return Response {
                    dataname: data,
                    data: "[]".to_string(),
                    error: format!("{}", err),
                };
            }
        };
        info!(?filename, "open file by name ");

        //Read PDF as binary file
        use std::io::{self, Read, Seek, SeekFrom};
        let mut list_of_chunks = Vec::new();
        let chunk_size = 0x4000;

        loop {
            let mut chunk = Vec::with_capacity(chunk_size);
            let n = match file
                .by_ref()
                .take(chunk_size as u64)
                .read_to_end(&mut chunk)
            {
                Ok(data) => data,
                Err(err) => {
                    info!(?err, "error file read");
                    break;
                }
            };
            if n == 0 {
                break;
            }
            for ele in chunk {
                list_of_chunks.push(ele);
            }
            if n < chunk_size {
                break;
            }
        }
```
The data from the PDF file is now in `list_of_chunks` and is converted to `base64` and returned.

Example 4.4:
```rust
       if list_of_chunks.len() != 0 {
            //binary encode to base64
            use base64::{engine::general_purpose, Engine as _};
            let base64_data = general_purpose::STANDARD_NO_PAD.encode(list_of_chunks);

            return Response {
                dataname: data,
                data: json!(&base64_data).to_string(),
                error: "".to_string(),
            };
```
   ***

## 5 Communication between Tauri and Vue {#Communication}
Finally an info about the communication between Tauri and Vue. For this I can recommend the very good documentation at [Rob Donnelly](https://rfdonnelly.github.io/posts/tauri-async-rust-process/).
I have implemented this variant. The transfer from/to Vue is always done as `string` in the end, so all structures have to be converted in and out as `json` string.

Example 5.1 Vue javascript file to call Tauri
```javascript
  import { invoke } from "@tauri-apps/api/tauri";
...
      invoke("js2rs", {
        message: JSON.stringify({
          path: "category",
          query: "?json=true",
          data: "category",
        }),
      });
...
```
In my example, the `message` field is transmitted as a JSON string and then needs to be parsed in Tauri into the `path`, `query` and `data` components.

Example 5.2 Tauri command  for the `invoke`:
```rust
#[tauri::command]
async fn js2rs(message: String, state: tauri::State<'_, AsyncProcInputTx>) -> Result<(), String> {
    let mut sub_message = message.clone();
    sub_message.truncate(50);
    info!(?sub_message, "js2rs");
    let async_proc_input_tx = state.inner.lock().await;
    async_proc_input_tx.send(message).await.map_err(|e| {
        println!("{}", e.to_string());
        e.to_string()
    })
}
```
Here the string is sent into the channel `async_proc_input_tx` to be processed one after the other.

Example 5.2. 
```rust
async fn async_process_model(
    mut input_rx: mpsc::Receiver<String>,
    output_tx: mpsc::Sender<String>,
) -> Result<(), Box<dyn std::error::Error + Send + Sync>> {
    while let Some(input) = input_rx.recv().await {
        let mut parse_error = false;
        let my_input_data: Receiver = match serde_json::from_str(input.as_str()) {
            Ok(data) => data,
            Err(err) => {
                parse_error = true;
                let my_output_data = Response {
                    dataname: "".to_string(),
                    data: "[]".to_string(),
                    error: err.to_string()
                };
                let output = json!(my_output_data).to_string();
                match output_tx.send(output).await {
                    _ => {}
                }
                Receiver {
                    path: "".to_string(),
                    query: "".to_string(),
                    data: "[]".to_string()
                }
            }
        };

        if !parse_error {
            let my_output_data: Response =
                message_handler(my_input_data.path, my_input_data.query, my_input_data.data).await;
            let output = json!(my_output_data).to_string();
            match output_tx.send(output).await {
                _ => {}
            }
        } 
    }

    Ok(())
}
```
There are the following structures in my example that are converted between Tauri and Vue:

Example 5.3. from `schema.rs`.
```rust
#[derive(serde::Serialize, Debug)]
pub struct Response {
    pub dataname: String,
    pub data: String,
    pub error: String,
}

#[derive(serde::Serialize, serde::Deserialize, Debug)]
pub struct Receiver {
    pub path: String,
    pub query: String,
    pub data: String,
}
```

   ***

## 6 Data structures in the Tauri server `app_data` {#app_data}
In the Tauri - Vue communication, the tauri-handler contains a Stauts parameter that controls the processing of the channel.
Similarly, in my application I need central information that the server manages and that should be available in the handlers, i.e., read, used or changed there and persistently saved. I decided to use a json file in the home directory. With the start of the server the data is read or, if the file does not exist, initialized and if the user maintains the data from the Vue application, the data should be written into the corresponding file.

First we need the data structure and the functions for reading, changing and saving.

Example 6.1 `main.rs`
```rust
#[derive(serde::Serialize, serde::Deserialize, Debug)]
pub struct AppData {
    pub main_path: String,
    pub email: String,
    pub name: String,
    pub clone_dir: String,
}

impl AppData {

    //constructor from `app_data` as clone() 
    pub fn new(app_data: &AppData) -> Self {
        info!("AppData new()");

        AppData {
            main_path: app_data.main_path.clone(),
            email: app_data.email.clone(),
            name: app_data.name.clone(),
            clone_dir: app_data.clone_dir.clone(),
        }
    }

    //constructor from file 
    pub fn init_app_data() -> Self {
        info!("AppData init_app_data()");

        let home_dir = home_dir().unwrap_or("".into());

        let file_and_path = format!(
            "{}/{}",
            home_dir.to_str().unwrap_or("").to_string(),
            database::APP_DATA_FILENAME
        );

        use std::fs::read_to_string;
        let app_data_string = read_to_string(file_and_path).unwrap_or("".to_string());

        let app_data = match serde_json::from_str(&app_data_string) {
            Ok(result) => result,
            Err(err) => {
                error!(?err, "Error: ");
                AppData {
                    main_path: database::MAIN_PATH.to_string(),
                    email: "".to_string(),
                    name: "".to_string(),
                    clone_dir: "".to_string(),
                }
            }
        };
        return app_data;
    }

    pub fn set(&mut self, main_path: String, email: String, name: String, clone_dir: String) {
        self.main_path = main_path;
        self.email = email;
        self.name = name;
        self.clone_dir = clone_dir;
        self.save_me();
    }

    pub fn save_me(&self) {
        info!("AppData save_me()");

        let home_dir = home_dir().unwrap_or("".into());

        let file_and_path = format!(
            "{}/{}",
            home_dir.to_str().unwrap_or("").to_string(),
            database::APP_DATA_FILENAME
        );

        let app_data_json = json!(self).to_string();

        match fs::write(file_and_path, app_data_json) {
            Ok(_) => {}
            Err(err) => {
                error!(?err, "Error: ");
            }
        };
    }
}
```
There are two `constuctor` here - once with `new(data)` a new object is created over the passed data, on the other hand with the function `init_app_data()`, here we read from the file and generate the object. In both cases the instance of `AppData` is returned.

The file itself with the data from `AppData` is always read or written into the `home` directory with the name defined as from `APP_DATA_FILENAME`.
There is now the function `set(data)` which also calls `save_me()` and finally saves the data as a `json` file.

In the `main` routine in the tauri server, the object can be passed via `manage()` and is then available optinally as a parameter in dan tauri-handlers.

Example 6.2:
```rust
fn main() {
    tracing_subscriber::fmt::init();

    generate_directory_database();

    let (async_proc_input_tx, async_proc_input_rx) = mpsc::channel(1);
    let (async_proc_output_tx, mut async_proc_output_rx) = mpsc::channel(1);

    tauri::Builder::default()
        .manage(AsyncProcInputTx {                         // Mutex to manage
            inner: Mutex::new(async_proc_input_tx),
        })
        .manage(AppData::init_app_data())                  // AppData to manage
        .invoke_handler(tauri::generate_handler![js2rs])   // tauri handler
        .setup(|app| {
            tauri::async_runtime::spawn(async move {
                async_process_model(async_proc_input_rx, async_proc_output_tx).await
            });

            let app_handle = app.handle();
            tauri::async_runtime::spawn(async move {
                loop {
                    if let Some(output) = async_proc_output_rx.recv().await {
                        rs2js(output, &app_handle);
                    }
                }
            });

            Ok(())
        })
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
```
The tauri handler `js2rs` can now receive the new parameter:

Example 6.2:
```rust
#[tauri::command]
async fn js2rs(
    message: String,
    state: tauri::State<'_, AsyncProcInputTx>,
    app_data: tauri::State<'_, AppData>,
) -> Result<(), String> {
    let mut sub_message = message.clone();
    sub_message.truncate(50);
    info!(?sub_message, "js2rs");

    let async_proc_input_tx = state.inner.lock().await;
    async_proc_input_tx
        .send((message, AppData::new(app_data.inner())))
        .await
        .map_err(|e| {
            println!("{}", e.to_string());
            e.to_string()
        })
}
```
Because at this point processing does not yet take place, but the data is first passed into a channel for asynchronous processing, the Message and AppData must be passed as tuples and the type of the Input parameter must also be adjusted.

Example 6.2:
```rust
...
struct AsyncProcInputTx {
    inner: Mutex<mpsc::Sender<(String, AppData)>>,
}
...
async fn async_process_model(
    mut input_rx: mpsc::Receiver<(String, AppData)>,     //input tuple with AppData
    output_tx: mpsc::Sender<String>,
) -> Result<(), Box<dyn std::error::Error + Send + Sync>> {
    while let Some((message, app_data)) = input_rx.recv().await {
        let mut parse_error = false;
        let my_message_data: Receiver = match serde_json::from_str(message.as_str()) {
            Ok(data) => data,
            Err(err) => {
                parse_error = true;
                let my_output_data = Response {
                    dataname: "".to_string(),
                    data: "[]".to_string(),
                    error: err.to_string(),
                };
                let output = json!(my_output_data).to_string();
                match output_tx.send(output).await {
                    _ => {}
                }
                Receiver {
                    path: "".to_string(),
                    query: "".to_string(),
                    data: "[]".to_string(),
                }
            }
        };

        if !parse_error {
            let my_output_data: Response = message_handler(
                app_data,                                     //app_data as parameter
                my_message_data.path,
                my_message_data.query,
                my_message_data.data,
            )
            .await;
            let output = json!(my_output_data).to_string();
            match output_tx.send(output).await {
                _ => {}
            }
        }
    }

    Ok(())
}
```
To do this, the channel and also the `async_process_model` function must be adapted accordingly and the `app_Data` parameter extended.

In the tauri handler itself the object AppData can then be used. In my example there is `user` for reading and `save_user` for saving from the application.

Example 6.3.:
```rust
#[tauri::command(async)]
async fn message_handler(
    //window: tauri::Window,
    //database: tauri::State<'_, Database>,
    mut app_data: AppData,
    path: String,
    query: String,
    data: String,
) -> Response {
    let message = format!(
        "path: {}, query: {}, data: {}",
        path.as_str().clone(),
        query.as_str().clone(),
        data.as_str().clone()
    );
    info!(message, "message_handler");
    io::stdout().flush().unwrap();

    match path.as_str() {
        //----
        "user" => {
            let home_dir = home_dir().unwrap();
            let message = format!("Your home directory, probably: {}", home_dir.display());
            info!(message, "message_handler");

            let my_data = json!( UserData {
                email: app_data.email,
                name: app_data.name,
                path_name: app_data.main_path,
                clone_path: app_data.clone_dir,
                avatar: "".to_string()
            }).to_string();

            Response {
                dataname: "me".to_string(),
                data: my_data,
                error: "".to_string(),
            }
        }
        //----
        "save_user" => {
            let my_save_user_data: SaveUserCommand = match serde_json::from_str(&data) {
                Ok(result) => result,
                Err(err) => {
                    error!(?err, "Error: ");

                    return Response {
                        dataname: data,
                        data: "[]".to_string(),
                        error: format!("{}", err),
                    };
                }
            };

            app_data.set(
                my_user_data.path_name.clone(),
                my_user_data.email.clone(),
                my_user_data.name.clone(),
                my_user_data.clone_path.clone(),
            );

            let my_data = json!(my_user_data).to_string();

            Response {
                dataname: "me".to_string(),
                data: my_data,
                error: "".to_string(),
            }
        }

...

    }
}
```
The `save_user` for the function `set(data)` off, which then also writes the data to the `home` directory, so that after a restart this data can be read.

On the client side in Vue, simply start the `invoke` with the data:

Example 6.4 `MainLayout.vue`:
```javascript
  import { invoke } from "@tauri-apps/api/tauri";

...

      saveDialogMe() {
        console.log(`MainLayout saveDialogMe()`);

        this.me.name = this.dialogMeData.name || "";
        this.me.email = this.dialogMeData.email || "";
        this.me.path_name = this.dialogMeData.path_name || "";
        this.me.clone_path = this.dialogMeData.clone_path || "";
        this.me.avatar = this.getGravatarURL(this.me.email);
        invoke("js2rs", {
          message: JSON.stringify({
          path: "save_user",
          query: "",
          data: JSON.stringify(this.me),
        })});
        this.dialogMe = false;
      },
...
```
The `avatar` is determined on the client, that must be released for the access in the Tauri `tauri.conf.json`.

   ***

## 7 Conclusion {#Conclusion}

Rust is like any other programming language: you have to study it to understand it. Some features allow elegant programming, others are incomprehensible and make it difficult to use. There is always light and shadow. For the error messages of the compiler you quickly get a feeling what you did wrong, it is merciless. The language could be more understandable and coherent in my opinion, other programming languages are much better.

Dynamic parameters and also the forced error handling are really good. It creates a robust program, even if it takes a little longer.
The channels reminded me of golang, but the powerful `promise` from JavaScript is not nearly reached.

Some of the libraries are still in their infancy, it will take some time to establish stability for productive use, but the path is the right one.
In some places, one wishes for better documentation and more examples - which is what I tried to do with this post.

Thanks to the hardworking developers of `diesel`and `tauri`- it was fun and I will continue down the path because you can't process media data in Electron for example and that runs in tauri, that's my next project.

[Index](https://jankstar.github.io/index)

