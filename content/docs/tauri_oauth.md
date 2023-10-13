---
title: "Rust: Tauri + OAuth2 + Google"
date: 2023-07-16T13:38:28+02:00
type: docs
weight: 202310
---
# Tauri oauth2 example (Tauri + Vue 3 + TypeScript)

## summary
This example is a Tauri app with a Vue 3 / TS client and for an authentication of the user via a Google account/email, i.e., the Google login window opens and the user must log in and agree to the transfer of his profile data (name and email).
The user data and the refresh token are stored, so that subsequently at the start of the app an automatic login takes place.

Attention: this application is not intended for productive use, because user data and token are not encrypted, but stored in plain text! <br>

## 1 Code / Example
The code can be found at: [tauri_oauth](https://github.com/jankstar/tauri_oauth)

```npm run tauri dev```

## 2 Preparation

## 2.1 Google GOOGLE_CLIENT_ID/GOOGLE_CLIENT_SECRET
For an authentication via a Google account we need a Google Client ID and a Cliebt Secret key for the API gmail and oauth2 for our application. For this purpose there are instructions ``https://cloud.google.com/docs/authentication`` at Google.

The settings are done via ``https://console.cloud.google.com/`` in the item 'Enable API and services/ceredentials' and 'OAuth2 client IDs'.  Here are some settings to consider if we want to authenticate with Tauri.


We are using authentication a web app, our application is running in Tauri on a server on the one hand, on the other hand we need our own server for the redirect. This redirect URI must be specified there 'Authorized redirect URIs'.
In my example I use for the server ``http://localhost:1420`` (this is in the tari.conf.json) and for the redirect ``http://127.0.0.1:1421`` - so the port 1421. This URL is used in Tauri and must be authenticated with Google for the request.

The Client ID and Client Secrete data can be found on the page.

## 2.2 ``.env`` File
A ``.env`` file with the following data is needed in the ```src-tauri``` directory:
```
GOOGLE_CLIENT_ID=<CLIENT_ID>
GOOGLE_CLIENT_SECRET=>CLIENT_SECRET>
```
For the application, the Google Client/Secrete must be created and written to the file, otherwise the program will not work.

## 2.3 Google Playground
The google oauth2 functions can be tested via the URL "https://developers.google.com/oauthplayground/".

## 3 Tauri App
## 3.1 User, UserData and AppData
In addition to authentication via OAuth2, the access token should also query user data from Google. 
If the values ``profile email`` are requested as ``scope`` in the request, the following structure is available:
```rust
#[derive(serde::Serialize, serde::Deserialize, Debug, Clone, Default)]
/// # User
/// struct for the google user data
pub struct User {
    pub id: String,
    pub email: String,
    pub verified_email: bool,
    pub name: String,
    pub given_name: String,
    pub family_name: String,
    pub picture: String,
    pub locale: String,
}
...
```
The request and the passing of the ``scope`` is described below.

The app should also record a refresh token in addition to the user data:
```rust
#[derive(serde::Serialize, serde::Deserialize, Debug, Clone, Default)]
/// # UserData
/// are the central data of the application and are stored in a local file and <br>
/// read with the start of the server or initialized if the file does not yet exist.
/// - user
/// - refresh_token
pub struct UserData {
    pub user: User,
    pub refresh_token: Option<oauth2::RefreshToken>,
}
...
```
This class is also used to read and set the user data, the token and the save as file. 
Attention: this function is only a test implementation and not intended for productive use. 
With the refresh token, an automatic attempt is made to log in again when the application is started, i.e. if this works, the user remains logged in right away.

The app itself gets the structure ``AppData`` via ``manage``:
```rust
/// # AppData
/// is managed via the tauri app
pub struct AppData {
    pub user_data: Mutex<UserData>,
    pub logged_in: Mutex<bool>,
    //pub db: Mutex<SqliteConnection>,
}
```
The structure contains the user data and an indicator whether a user is logged in, i.e. whether the login worked. This data must be encapsulated via a mutex because of the asynchronous processing.

## 3.2 The ``main`` function: manage AppData and ExitRequested Event
The ``main`` routine contains the tauri-builder with the ``manage`` of the app data.
An invoke-handler ``js2rs`` is registered. At this point a handler of the event ExitRequested can also be registered.

```rust
fn main() {
    tracing_subscriber::fmt::init();

    let app = tauri::Builder::default()
        .manage(AppData {
            user_data: UserData::init_user_data().into(), //the user data
            logged_in: false.into(),                      //log in state
                                                          //db: establish_connection(&database_name).into(),
        }) // AppData to manage
        .invoke_handler(tauri::generate_handler![js2rs])
        .build(tauri::generate_context!())   
        .unwrap()
        .run(move |_app_handler, _event| {
            if let RunEvent::ExitRequested { api, .. } = &_event {
                println!("Exit requested: {:?}", _event);
                // Keep the event loop running even if all windows are closed
                // This allow us to catch tray icon events when there is no window
                //api.prevent_exit();
              }            
        });

        //.expect("error while running tauri application");
}
```

## 3.4 Tauri: rust from/to Vue.js
The transfer of data from/to rust or from/to Vue.js is done as a json string.
In my example two commands are implemented as string: 
``get_user``: liefert die Daten zum User bzw. führt ein Login durch, wenn kein User eingeloggt ist.

``logout``: deletes the user data, the tokens and the ``logged_in`` flag in the app and file.
Note: There is also the possibility to log out the Google user via the token, this function is available but not implemented here. For this reason 
the user is immediately available at a new login, because the browser still has the cookies, the user is still logged in there.

```rust
// A function that sends a message from Rust to JavaScript via a Tauri Event
pub fn rs2js<R: tauri::Runtime>(message: String, manager: &impl tauri::Manager<R>) {
    let mut sub_message = message.clone();
    sub_message.truncate(50);
    info!(?sub_message, "rs2js");
    match manager.emit_all("rs2js", message) {
        Ok(_) => {}
        Err(err) => {
            error!(?err);
        }
    };
}
```

```rust
/// The Tauri command that gets called when Tauri `invoke` JavaScript API is called
#[tauri::command(async)]
async fn js2rs(
    window: tauri::Window,
    message: String,
    app_data: tauri::State<'_, AppData>,
) -> Result<String, String> {
    info!(message, "message_handler: ");

    if message == "get_user" {
        //get the data from the mutex
        let mut user_data = app_data.user_data.lock().await;
        let mut logged_in = app_data.logged_in.lock().await;

        if logged_in.clone() != true {
            match window.get_window("main") {
                Some(main_window) => {
                    main_window.hide();

                    *logged_in = user_data.log_in(&window).await;

                    main_window.show();
                }
                _ => {}
            };
        }

        return Ok(json!(user_data.user).to_string());
    }

    if message == "logout" {
        //get the data from the mutex
        let mut user_data = app_data.user_data.lock().await;
        let mut logged_in = app_data.logged_in.lock().await;

        //clear all user data
        user_data.user = User::new();
        user_data.refresh_token = None;
        user_data.save_me();
        *logged_in = false;

        *logged_in = user_data.log_in(&window).await;

        return Ok(json!(user_data.user).to_string());
    }

    //return else
    Ok("".to_string())
}
```

## 3.5 Call ``log_in()``and ``get_token`` for OAuth2
The solution used here is taken from [oauth2 crate](https://github.com/ramosbugs/oauth2-rs/blob/main/examples/google.rs) and slightly optimised.

The ``log_in()`` function first fetches the access token via OAuth2 and then the user data for authentication. Only if both actions were successful, ``true`` is returned.

Match the ``get_token()`` function:
```rust
    pub async fn log_in(&mut self, window: &tauri::Window) -> bool {
        let l_do: i32 = 'block: {
            let (l_access_token, l_refresh_token) =
                match get_token(&window, self.user.email.clone(), self.refresh_token.clone()).await
                {
                    Ok(token) => token,
                    Err(e) => {
                        error!("error - Access token could not be retrieved {}", e);

                        self.user.name = "".to_string();
                        self.user.email = "".to_string();
                        self.refresh_token = None;

                        self.save_me();

                        return false;
                    }
                };


    ...
```
The login via ``get_toek()`` can use the email address or suggest it in the mask. If there is a refresh token, this can be used first. For this case a login is not necessary. These values are passed.

```rust
...
    //get the google client ID and the client secret from .env file
    dotenv().ok();

    let google_client_id = ClientId::new(std::env::var("GOOGLE_CLIENT_ID")?);
    let google_client_secret = ClientSecret::new(std::env::var("GOOGLE_CLIENT_SECRET")?);
    let auth_url = AuthUrl::new("https://accounts.google.com/o/oauth2/v2/auth".to_string())?; //.expect("Invalid authorization endpoint URL");
    let token_url = TokenUrl::new("https://www.googleapis.com/oauth2/v3/token".to_string())?; //.expect("Invalid token endpoint URL");

    // Set up the config for the Google OAuth2 process.
    let client = BasicClient::new(
        google_client_id,
        Some(google_client_secret),
        auth_url,
        Some(token_url),
    )
    // This example will be running its own server at http://127.0.0.1:1421
    // See below for the server implementation.
    .set_redirect_uri(
        RedirectUrl::new("http://127.0.0.1:1421".to_string())?, //.expect("Invalid redirect URL"),
    )
    // Google supports OAuth 2.0 Token Revocation (RFC-7009)
    .set_revocation_uri(
        RevocationUrl::new("https://oauth2.googleapis.com/revoke".to_string())?, //.expect("Invalid revocation endpoint URL"),
    ); //.set_introspection_uri(introspection_url);
...
```

At this point, the client is initialised for the OAuth2 call with the URLs : <br>
``auth_url`` - Authentication <br>
``token_url`` - Token endpoint <br>
``redirect_uri`` - the Redirect URL, which is an additional server that accepts the token. This URL must be authenticated for the Google client. <br>
``revocation_uri``- URL for revoking the token <br>

If the refresh token is transferred, the authentication token is first requested with this token:
```rust
...
    if refresh_token.is_some() {
        println!("get_token() refresh_token found");

        match client
        .exchange_refresh_token(&refresh_token.unwrap().clone())
        .request_async(async_http_client)
        .await {
            Ok(token_response) => {
                let access_token = token_response.access_token().clone();
                let refresh_token = token_response.refresh_token().cloned();
                return Ok((access_token, refresh_token));
            },
            Err(_) => {},
        };
        println!("get_token() refresh_token not valid, login required");
    }
...
```
In this case, no new login is necessary. 

A CSRF token is required for the login. The permissions requested from the application must be specified via ``Scope``. Here, ``profile`` is required for the user data and ``email`` for the email address. If, for example, the email is to be queried via the Google Email API, the scope ``https://mail.google.com`` is required.

```rust
    // Google supports Proof Key for Code Exchange (PKCE - https://oauth.net/2/pkce/).
    // Create a PKCE code verifier and SHA-256 encode it as a code challenge.
    let (pkce_code_challenge, pkce_code_verifier) = PkceCodeChallenge::new_random_sha256();

    // Generate the authorization URL to which we'll redirect the user.
    let (authorize_url, csrf_state) = client
        .authorize_url(CsrfToken::new_random)
        // This example is requesting access to the "gmail" features and the user's profile.
        //.add_scope(Scope::new("https://mail.google.com".into()))
        .add_scope(Scope::new("profile email".into()))
        .add_extra_param("access_type", "offline")
        .add_extra_param("login_hint", email)
        //.add_extra_param("prompt", "none")
        .set_pkce_challenge(pkce_code_challenge)
        .url();

    println!("The authorization URL is:\n{}\n", authorize_url.to_string());
...
```
This ``authorize_url`` is now started in a new Tauri window:
```rust
...
    let handle = window.app_handle();

    let login_window = tauri::WindowBuilder::new(
        &handle,
        "Google_Login", /* the unique window label */
        tauri::WindowUrl::External(
            authorize_url.to_string().parse()?, //.expect("error WindowBuilder WindowUrl parse"),
        ),
    )
    .build()?; //.expect("error WindowBuilder build");
    login_window.set_title("Google Login");
    login_window.set_always_on_top(true);
...
```
Now, of course, a server must be started on the redirected URL, which then reads out the ``code`` and ``state`` from the URL or the ``error`` in the event of an error:
```rust
...
    // A very naive implementation of the redirect server.
    let listener = std::net::TcpListener::bind("127.0.0.1:1421")?; //.expect("error TcpListener bind");
    let local_addr = listener.local_addr()?;
...
    //this is blocking listener! we use guard schedule for time out
    for stream in listener.incoming() {
        let _ = login_window.is_visible()?; //check if login_window is visible

        if let Ok(mut stream) = stream {
            info!("listener stream");

            let code;
            let state;
            let errorinfo;
            {
                let mut reader = BufReader::new(&stream);

                let mut request_line = String::new();
                reader.read_line(&mut request_line)?;

                let redirect_url = match request_line.split_whitespace().nth(1) {
                    Some(url_data) => url_data,
                    _ => {
                        login_window.close()?;
                        break;
                    }
                };
                println!("redirect_url: \n{}", redirect_url.clone());
                let url = url::Url::parse(&("http://localhost".to_string() + redirect_url))?;

                use std::borrow::Cow;
                //extract code from url
                let code_pair = url
                    .query_pairs()
                    .find(|pair| {
                        let &(ref key, _) = pair;
                        key == "code"
                    })
                    .unwrap_or((Cow::from(""), Cow::from("")));

                let (_, value) = code_pair;
                code = AuthorizationCode::new(value.into_owned());

                //extract state from url
                let state_pair = url
                    .query_pairs()
                    .find(|pair| {
                        let &(ref key, _) = pair;
                        key == "state"
                    })
                    .unwrap_or((Cow::from(""), Cow::from("")));

                let (_, value) = state_pair;
                state = CsrfToken::new(value.into_owned());

                //extract error from url
                let errorinfo_pair = url
                    .query_pairs()
                    .find(|pair| {
                        let &(ref key, _) = pair;
                        key == "error"
                    })
                    .unwrap_or((Cow::from(""), Cow::from("")));

                let (_, value) = errorinfo_pair;
                errorinfo = String::from(value.into_owned());
            }
...

            // Exchange the code with a token.
            let token_response = match client
                .exchange_code(code)
                .set_pkce_verifier(pkce_code_verifier)
                .request_async(async_http_client)
                .await
            {
                Ok(res) => res,
                Err(err) => {
                    login_window.close()?;
                    Err("--  no permission --")?
                }
            };
...
```
With ``code`` and the ``pkce_code_verifier`` the access token is requested.

The Tauri window for the login must be closed at the right moment, furthermore, if the window is closed (``CloseRequested``) or if it times out, the login process must be aborted:
```rust
...
    let timer = timer::Timer::new();

    let _guard = timer.schedule_with_delay(chrono::Duration::seconds(25), move || {
        //the time out as connect to close server
        let _ = std::net::TcpStream::connect(local_addr); 
    });

    login_window.on_window_event(move |event| {
        if let WindowEvent::CloseRequested { api, .. } = &event {
        info!("event close-requested");
        let _ = std::net::TcpStream::connect(local_addr); //connect to server to close it
        };
    });

...
```
The timer or the event ``CloseRequested`` connects to the server, which then causes the login to be aborted.

The following answer provides ``StandardTokenResponse``:
```
StandardTokenResponse {
    access_token: AccessToken([redacted]),
    token_type: Bearer,
    expires_in: Some(
        3599,
    ),
    refresh_token: Some(
        RefreshToken([redacted]),
    ),
    scopes: Some(
        [
            Scope(
                "https://www.googleapis.com/auth/userinfo.profile",
            ),
            Scope(
                "https://www.googleapis.com/auth/userinfo.email",
            ),
        ],
    ),
    extra_fields: EmptyExtraTokenFields,
}
```
## 3.6 Vue.js Client
The client was not implemented further for this example, only a ``get_user`` is started as ``invoke(..)`` when the app is ``created``, name and email are displayed and the commands ``get_user()`` or ``logout()`` can be issued via a button. 
```js
  methods: {
    async on_login() {
      const that = this;
      invoke("js2rs", {
        message: "get_user"
      }).then((data: any) => {
        try {
          let me_data = JSON.parse(data)
          that.me.name = me_data.name ? me_data.name : "";
          that.me.email = me_data.email ? me_data.email : "";
        } catch (err) {
          console.error(err);
        }
        that.loading = false;
      });
    },
...
<template>
  <div>
    Name: {{ me.name }} <br>
    Email: {{ me.email }}
  </div>
  <div>
    <button v-if="me.email == ''"  type="button" @click="on_login()" style="background-color: green;">Log in</button>
    <button v-if="me.email != ''"  type="button" @click="on_logout()" style="background-color: red;">Log out</button>
    <button type="button" @click="on_end()" style="background-color: black;">End</button>
  </div>
</template>
...
```

## 4 Conclusion
Setting up OAuth2 authentication for Google and using it in Tauri is not easy, but it can be done. This is a simple way to link a Tauri app to a Google account or email. 

The Rust Crate for OAuth provides nice examples, but you have to search and try out the details. 
All in all, the functions are ready for productive use.

(2023/10/01)