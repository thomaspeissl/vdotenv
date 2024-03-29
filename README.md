# dotenv
People configure their app variables via JSON, YAML, or even gitignored .v files. I personally found env files to work the best, especially with [docker-compose](https://docs.docker.com/compose/environment-variables/#the-env_file-configuration-option).

Further reading:
[12 factor apps](https://12factor.net/config)

## Features

- fully compatible with docker-compose .env
- useful helper function dotenv.get()
- dotenv.required() method to let people know what variables are needed
- automatically create missing .env file with blank required fields when working with dotenv.required() for an easy setup
- support `export` keyword so you can `source .env` in your project specific shell scripts

## Usage
Create a file called .env in the root folder of your application.
Add it to your .gitignore file. (best practice)
Fill it with key=value pairs.

```shell
POSTGRES_HOST=localhost
POSTGRES_USER=admin
POSTGRES_PASSWORD=postgres_password_goes_here
POSTGRES_DB=admin

JWT_SECRET=jwt_secret_goes_here
export NODE_ENV=development
```

Then in your v source:
```v
module main

import thomaspeissl.dotenv
import os

fn main() {
    // load .env environment file
    dotenv.load()
    // optional check if required keys have values - error if something is missing
    // this also creates the .env file with the requested variables for an easy setup
    dotenv.required('POSTGRES_HOST', 'POSTGRES_USER', 'POSTGRES_PASSWORD', 'POSTGRES_DB')
    // you can use build-in os.getenv()
    println(os.getenv('POSTGRES_HOST'))
    // you can also use dotenv.get() if you need fallback handling
    secret := dotenv.get('JWT_SECRET') or {
        'default_dev_token' // default, not found, or simply the same on all environments
    }
    println(secret)
}
```

## Syntax rules
These syntax rules apply to the .env file:

- dotenv expects each line in an env file to be in VAR=VAL format.
- Lines beginning with # are processed as comments and ignored.
- Blank lines are ignored.
- There is no special handling of quotation marks. This means that they are part of the VAL.
- Environment variables may not contain whitespace.
- Since docker-compose 1.26+ also allow "export VAL=VAR" 

Note that there is also another dotenv module with more relaxed syntax rules (eg. inline comments) available 
https://vpm.vlang.io/mod/zztkm.vdotenv.  
We cannot relax these rules because we would lose docker .env compatibility.

## Installation

### Install and use dotenv module as a dependency via v.mod (recommended)

Run "v init" to auto-generate your v.mod file.
```shell
v init
```
Then edit the dependencies in your v.mod file to look like this: 
```v
dependencies: ['thomaspeissl.dotenv']
```
And install with:
```shell
v install
```
To update your dependencies later just run "v install" again.

### Or via VPM:
```shell
v install thomaspeissl.dotenv
```

### Or through Git:
```shell
git clone https://github.com/thomaspeissl/vdotenv.git ~/.vmodules/thomaspeissl/dotenv
```

## Test with docker-compose
Clone this repository and execute this commands while in the cloned folder.
```shell
docker-compose run --rm v
println(os.getenv('POSTGRES_HOST'))
```
This should print "localhost".


# Module documentation

## Contents
- [fallback_get](#fallback_get)
- [get](#get)
- [load](#load)
- [load_file](#load_file)
- [must_get](#must_get)
- [require](#require)
- [required](#required)

## fallback_get
```v
fn fallback_get(key string, fallback string) string
```

use fallback_get if you prefer traditional fallback handling

[[Return to contents]](#Contents)

## get
```v
fn get(key string) !string
```

get is an alternative to os.getenv when you need fallback handling

[[Return to contents]](#Contents)

## load
```v
fn load()
```

load parses the .env environment file

[[Return to contents]](#Contents)

## load_file
```v
fn load_file(filename string)
```

loads_file parses the given .env environment file

[[Return to contents]](#Contents)

## must_get
```v
fn must_get(key string) string
```

must_get errors out if key does not exist

[[Return to contents]](#Contents)

## require
```v
fn require(required_keys ...string)
```

require loads and checks for gives keys

[[Return to contents]](#Contents)

## required
```v
fn required(required_keys ...string)
```

required checks if given keys have values - errors out if something is missing - also creates the .env file with the given variables for an easy setup

[[Return to contents]](#Contents)

#### Powered by vdoc. Generated on: 23 Feb 2022 16:39:37

## License
[AGPL-3.0](LICENSE)
