# Using Box's Python SDK

Using Box's Python SDK, particularly on a headless Ubuntu server, can be confusing to set up, so this code and README should help get you started.

# Part one: Setup

## Step 1: Make a Box App
1. Go to [Box Developer Console](https://duke.app.box.com/developers/console) and sign in to your account

2. Click `Create Platform App`, then name your app something (e.g. Box SDK for Ubuntu). For app type, select `Oauth 2.0`.

3. Scroll to `OAuth 2.0 Redirect URIs`, and enter add this URI (copy it in, then press Add): `http://localhost:3000/callback`


## Step 2: Save your Client information in your project folder

1. Right above the redirect URIs is the `OAuth 2.0 Credentials`, including your "Client ID" and "Client Secret". Both of these (but mostly the Client Secret) are sensitive information and should not be shared. These should also NEVER be uploaded to a Github repo. Copy them into a file named `.env` with the following structure. If you already have a `.env` file, in your project folder, feel free to just add these as new lines:

```Python
CLIENT_ID="[your client id]"
CLIENT_SECRET="[your client secret]"
CALLBACK_HOSTNAME = "localhost"
CALLBACK_PORT = 3000
REDIRECT_URI='http://localhost:3000/callback'
BOX_TOKEN_CACHE_FILE = "[absolute path to your project folder]/.oauth.tk"
```

2. In your project folder, if you don't already have a file named `.gitignore`, make one. This will allow you to choose files that are never committed to your git repo.

3. In the `.gitignore` file, add the following lines:
```
# environment variable files
*.env

# Token files
*.tk
```

# Part 2: Using the client

## Step 1: Load your environment variables in from the `.env` file. 
If using the cookiecutter data science (CCDS) template, the `config.py` file should already load in your `.env` files. Otherwise, `pip install python-dotenv`, and use the `load_dotenv` function before trying to use the Box SDK client. For example

```Python
from dotenv import load_dotenv
load_dotenv() # looks for a file named ".env", so if it's named something else, give it the file path
```

## Step 2: Create the client
IMPORTANT: if this is being used on a remote server (e.g. over SSH), it is important that you are using VSCode. VSCode has automatic port-forwarding that will allow authentication to occur in your browser. 

Import the ConfigOAuth class and the get_client_oauth function

```Python
from diabetes_watch_2.utils.box_oauth import ConfigOAuth, get_client_oauth

conf = ConfigOAuth()
client = get_client_oauth(conf)
```

If you need to authenticate, VSCode might warn you that a URL is trying to open in your browser (accept it). Sign in to your Box account to authenticate. 

## Step 3: Test the client

You can test that the client worked by running the following code:
```Python
me=client.users.get_user_me()
print(f"\nHello, I'm {me.name} ({me.login}) [{me.id}]")
```

# Sources

The code and instructions were pieced together from the following sources:

* https://github.com/box-community/box-python-gen-workshop/blob/main/utils/box_client_oauth.py

    * This workshop has a ton of handy code for using the Python SDK. The actual documentation for the SDK (on github) is very incomplete, skipping a bunch of steps. 

* Some tutorial that I can't find right now

    * There was some box tutorial on their website that guided you through creating a new Box App, but I can't find it. The important information was included above (Use OAuth 2.0, get your client ID/Secret, use localhost:3000) 