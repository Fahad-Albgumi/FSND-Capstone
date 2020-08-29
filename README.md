# FSND: Capstone Project

## Covered Topics

This is the last project of the `Udacity-Full-Stack-Nanodegree` Course. It covers following technical topics in 1 app:

1. Database modeling with `postgres` & `sqlalchemy` (see `models.py`)
2. API to performance CRUD Operations on database with `Flask` (see `app.py`)
3. Automated testing with `Unittest` (see `test_app`)
4. Authorization & Role based Authentification with `Auth0` (see `auth.py`)
5. Deployment on `Heroku`



## Start Project locally

Make sure you `cd` into the correct folder (with all app files) before following the setup steps. Also, you need the latest version of [Python 3](https://www.python.org/downloads/) and [postgres](https://www.postgresql.org/download/) installed on your machine.

To start and run the local development server,

1. Initialize and activate a virtualenv:

```
$ virtualenv --no-site-packages env_capstone
$ source env_capstone/scripts/activate
```

1. Install the dependencies:

```
$ pip install -r requirements.txt
```

Running this project locally means that it can´t access `Herokus` env variables. To fix this, you need to edit a few informations in `config.py`, so it can correctly connect to a local database

1. Change database config so it can connect to your local postgres database

- Open `config.py` with your editor of choice.
- Here you can see this dict:

```
database_setup = {
   "database_name_production" : "agency",
   "user_name" : "postgres", # default postgres user name
   "password" : "testpassword123", # if applicable. If no password, just type in None
   "port" : "localhost:5432" # default postgres port
}
```

- Just change `user_name`, `password` and `port` to whatever you choose while installing postgres.

> *tip*: `user_name` usually defaults to `postgres` and `port` always defaults to `localhost:5432` while installing postgres, most of the time you just need to change the `password`.

1. Setup Auth0 If you only want to test the API (i.e. Project Reviewer), you can simply take the existing bearer tokens in `config.py`.

If you already know your way around `Auth0`, just insert your data into `config.py` => auth0_config.

FYI: Here are the steps I followed to enable authentication:

1. Run the development server:

```
$ python app.py
```

1. (optional) To execute tests, run

```
$ python test_app.py
```

If you choose to run all tests, it should give this response if everything went fine:

```
$ python test_app.py
.........................
----------------------------------------------------------------------
Ran 25 tests in 18.132s

OK
```

## API Documentation



Here you can find all existing endpoints, which methods can be used, how to work with them & example responses you´ll get.

Additionally, common pitfalls & error messages are explained, if applicable.

### Base URL

***[https://fahadalbgumicapstone.herokuapp.com](https://fahadalbgumicapstone.herokuapp.com/)\***

### Authentication

Please see authentication near the bottom of the page.

### Available Endpoints

Here is a short table about which resources exist and which method you can use on them.

```
                      Allowed Methods
   Endpoints    |  GET |  POST |  DELETE | PATCH  |
                |------|-------|---------|--------|
  /actors       |  [x] |  [x]  |   [x]   |   [x]  |   
  /movies       |  [x] |  [x]  |   [x]   |   [x]  |   
```

### How to work with each endpoint

Each resource documentation is clearly structured:

1. Description in a few words
2. `curl` example that can directly be used in terminal
3. More descriptive explanation of input & outputs.
4. Required permission
5. Example Response.
6. Error Handling (`curl` command to trigger error + error response)

# 

### 1. GET /actors

Query paginated actors.

```
$ curl -X GET https://fahadalbgumicapstone.herokuapp.com/actors?page1
```

- Fetches a list of dictionaries of examples in which the keys are the ids with all available fields
- Request Arguments:
  - **integer** `page` (optional, 10 actors per page, defaults to `1` if not given)
- Request Headers: **None**
- Requires permission: `read:actors`
- Returns:
  1. List of dict of actors with following fields:
     - **integer** `id`
     - **string** `name`
     - **string** `gender`
     - **integer** `age`
  2. **boolean** `success`

#### Example response

```
{
    "actors": [
        {
            "age": 41,
            "gender": "male",
            "id": 0,
            "name": "Kevin Hart"
        },
        {
            "age": 30,
            "gender": "female",
            "id": 2,
            "name": "Margot Robbie"
        },
        {
            "age": 48,
            "gender": "male",
            "id": 1,
            "name": "Dwayne Johnson"
        }
    ],
    "success": true
}
```

#### Errors

If you try fetch a page which does not have any actors, you will encounter an error which looks like this:

```
$ curl -X GET https://fahadalbgumicapstone.herokuapp.com/actors?page123124
```

will return

```
{
  "error": 404,
  "message": "no actors found in database.",
  "success": false
}
```

# 

### 2. POST /actors

Insert new actor into database.

```
$ curl -X POST https://fahadalbgumicapstone.herokuapp.com/actors
```

- Request Arguments: **None**
- Request Headers: (*application/json*) 1. **string** `name` (*required) 2. **integer** `age` (*required) 3. **string** `gender`
- Requires permission: `create:actors`
- Returns:
  1. **integer** `id from newly created actor`
  2. **boolean** `success`

#### Example response

```
{
    "created": 5,
    "success": true
}
```

#### Errors

If you try to create a new actor without a required field like `name`, it will throw a `422` error:

```
$ curl -X GET https://fahadalbgumicapstone.herokuapp.com/actors?page123124
```

will return

```
{
  "error": 422,
  "message": "no name provided.",
  "success": false
}
```

# 

### 3. PATCH /actors

Edit an existing Actor

```
$ curl -X PATCH https://fahadalbgumicapstone.herokuapp.com/actors/1
```

- Request Arguments: **integer** `id from actor you want to update`
- Request Headers: (*application/json*) 1. **string** `name` 2. **integer** `age` 3. **string** `gender`
- Requires permission: `edit:actors`
- Returns:
  1. **integer** `id from updated actor`
  2. **boolean** `success`
  3. List of dict of actors with following fields:
     - **integer** `id`
     - **string** `name`
     - **string** `gender`
     - **integer** `age`

#### Example response

```
{
    "actor": [
        {
            "age": 30,
            "gender": "Other",
            "id": 1,
            "name": "Test Actor"
        }
    ],
    "success": true,
    "updated": 1
}
```

#### Errors

If you try to update an actor with an invalid id it will throw an `404`error:

```
$ curl -X PATCH https://fahadalbgumicapstone.herokuapp.com/actors/125
```

will return

```
{
  "error": 404,
  "message": "Actor with id 125 not found in database.",
  "success": false
}
```

Additionally, trying to update an Actor with already existing field values will result in an `422` error:

```
{
  "error": 422,
  "message": "provided field values are already set. No update needed.",
  "success": false
}
```

# 

### 4. DELETE /actors

Delete an existing Actor

```
$ curl -X DELETE https://fahadalbgumicapstone.herokuapp.com/actors/1
```

- Request Arguments: **integer** `id from actor you want to delete`
- Request Headers: `None`
- Requires permission: `delete:actors`
- Returns:
  1. **integer** `id from deleted actor`
  2. **boolean** `success`

#### Example response

```
{
    "deleted": 5,
    "success": true
}
```

#### Errors

If you try to delete actor with an invalid id, it will throw an `404`error:

```
$ curl -X DELETE https://fahadalbgumicapstone.herokuapp.com/actors/125
```

will return

```
{
  "error": 404,
  "message": "Actor with id 125 not found in database.",
  "success": false
}
```

# 

### 5. GET /movies

Query paginated movies.

```
$ curl -X GET https://fahadalbgumicapstone.herokuapp.com/movies?page1
```

- Fetches a list of dictionaries of examples in which the keys are the ids with all available fields
- Request Arguments:
  - **integer** `page` (optional, 10 movies per page, defaults to `1` if not given)
- Request Headers: **None**
- Requires permission: `read:movies`
- Returns:
  1. List of dict of movies with following fields:
     - **integer** `id`
     - **string** `name`
     - **date** `release_date`
  2. **boolean** `success`

#### Example response

```
{
    "movies": [
        {
            "id": 0,
            "release_date": "Sat, 20 Aug 1994 00:00:00 GMT",
            "title": "The Shawshank Redemption"
        },
        {
            "id": 1,
            "release_date": "Tue, 14 Mar 1972 00:00:00 GMT",
            "title": "The Godfather"
        }
    ],
    "success": true
}
```

#### Errors

If you try fetch a page which does not have any movies, you will encounter an error which looks like this:

```
$ curl -X GET https://fahadalbgumicapstone.herokuapp.com/movies?page123124
```

will return

```
{
  "error": 404,
  "message": "no movies found in database.",
  "success": false
}
```

# 

### 6. POST /movies

Insert new Movie into database.

```
$ curl -X POST https://fahadalbgumicapstone.herokuapp.com/movies
```

- Request Arguments: **None**
- Request Headers: (*application/json*) 1. **string** `title` (*required) 2. **date** `release_date` (*required)
- Requires permission: `create:movies`
- Returns:
  1. **integer** `id from newly created movie`
  2. **boolean** `success`

#### Example response

```
{
    "created": 5,
    "success": true
}
```

#### Errors

If you try to create a new movie without a requiered field like `name`, it will throw a `422` error:

```
$ curl -X GET https://fahadalbgumicapstone.herokuapp.com/movies?page123124
```

will return

```
{
  "error": 422,
  "message": "no name provided.",
  "success": false
}
```

# 

### 7. PATCH /movies

Edit an existing Movie

```
$ curl -X PATCH https://fahadalbgumicapstone.herokuapp.com/movies/1
```

- Request Arguments: **integer** `id from movie you want to update`
- Request Headers: (*application/json*) 1. **string** `title` 2. **date** `release_date`
- Requires permission: `edit:movies`
- Returns:
  1. **integer** `id from updated movie`
  2. **boolean** `success`
  3. List of dict of movies with following fields:
     - **integer** `id`
     - **string** `title`
     - **date** `release_date`

#### Example response

```
{
    "created": 1,
    "movie": [
        {
            "id": 1,
            "release_date": "Sun, 16 Feb 2020 00:00:00 GMT",
            "title": "Test Movie 123"
        }
    ],
    "success": true
}
```

#### Errors

If you try to update an movie with an invalid id it will throw an `404`error:

```
$ curl -X PATCH https://fahadalbgumicapstone.herokuapp.com/movies/125
```

will return

```
{
  "error": 404,
  "message": "Movie with id 125 not found in database.",
  "success": false
}
```

Additionally, trying to update an Movie with already existing field values will result in an `422` error:

```
{
  "error": 422,
  "message": "provided field values are already set. No update needed.",
  "success": false
}
```

# 

### 8. DELETE /movies

Delete an existing movie

```
$ curl -X DELETE https://fahadalbgumicapstone.herokuapp.com/movies/1
```

- Request Arguments: **integer** `id from movie you want to delete`
- Request Headers: `None`
- Requires permission: `delete:movies`
- Returns:
  1. **integer** `id from deleted movie`
  2. **boolean** `success`

#### Example response

```
{
    "deleted": 5,
    "success": true
}
```

#### Errors

If you try to delete movie with an invalid id, it will throw an `404`error:

```
$ curl -X DELETE https://fahadalbgumicapstone.herokuapp.com/movies/125
```

will return

```
{
  "error": 404,
  "message": "Movie with id 125 not found in database.",
  "success": false
}
```

# 

## Authentication

All API Endpoints are decorated with Auth0 permissions. To use the project locally, you need to config Auth0 accordingly

### Auth0 for locally use

#### Create an App & API

1. Login to https://manage.auth0.com/
2. Click on Applications Tab
3. Create Application
4. Give it a name like `Music` and select "Regular Web Application"
5. Go to Settings and find `domain`. Copy & paste it into config.py => auth0_config['AUTH0_DOMAIN'] (i.e. replace `"dev-qgzdp5vm.eu.auth0.com"`)
6. Click on API Tab
7. Create a new API:
   1. Name: `Music`
   2. Identifier `Music`
   3. Keep Algorithm as it is
8. Go to Settings and find `Identifier`. Copy & paste it into config.py => auth0_config['API_AUDIENCE'] (i.e. replace `"Music"`)

#### Create Roles & Permissions

1. Before creating `Roles & Permissions`, you need to `Enable RBAC` in your API (API => Click on your API Name => Settings = Enable RBAC => Save)
2. Also, check the button `Add Permissions in the Access Token`.
3. First, create a new Role under `Users and Roles` => `Roles` => `Create Roles`
4. Give it a descriptive name like `Casting Assistant`.
5. Go back to the API Tab and find your newly created API. Click on Permissions.
6. Create & assign all needed permissions accordingly
7. After you created all permissions this app needs, go back to `Users and Roles` => `Roles` and select the role you recently created.
8. Under `Permissions`, assign all permissions you want this role to have.

# 

### Auth0 to use existing API

If you want to access the real, temporary API, bearer tokens for all 3 roles are included in the `config.py` file.

## Existing Roles

They are 3 Roles with distinct permission sets:

1. Casting Assistant:

- GET /actors (view:actors): Can see all actors
- GET /movies (view:movies): Can see all movies

1. Casting Director (everything from Casting Assistant plus)

- POST /actors (create:actors): Can create new Actors
- PATCH /actors (edit:actors): Can edit existing Actors
- DELETE /actors (delete:actors): Can remove existing Actors from database
- PATCH /movies (edit:movies): Can edit existing Movies

1. Exectutive Dircector (everything from Casting Director plus)

- POST /movies (create:movies): Can create new Movies
- DELETE /movies (delete:movies): Can remove existing Motives from database

In your API Calls, add them as Header, with `Authorization` as key and the `Bearer token` as value. Don´t forget to also prepend `Bearer` to the token (seperated by space).

For example: (Bearer token for `Executive Director`)

```
{
    "Authorization": "Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCIsImtpZCI6IkpJTWdXaW5pM2U0bzZsSDlnQTExWiJ9.eyJpc3MiOiJodHRwczovL2Rldi1xZ3pkcDV2bS5ldS5hdXRoMC5jb20vIiwic3ViIjoiYXV0aDB8NWYyZWM2ZGIyNmI3YzYwMDNkNDNiNTY5IiwiYXVkIjoiTXVzaWMiLCJpYXQiOjE1OTg2ODc1MjcsImV4cCI6MTU5ODY5NDcyNywiYXpwIjoiZzFTdmdMcFJONnNqOEEwWFEyajJpamVpQU9idTBTMG4iLCJzY29wZSI6IiIsInBlcm1pc3Npb25zIjpbImNyZWF0ZTphY3RvcnMiLCJjcmVhdGU6bW92aWVzIiwiZGVsZXRlOmFjdG9ycyIsImRlbGV0ZTptb3ZpZXMiLCJlZGl0OmFjdG9ycyIsImVkaXQ6bW92aWVzIiwicmVhZDphY3RvcnMiLCJyZWFkOm1vdmllcyJdfQ.DO6SncvPUkyziBVnXUN9AUpOiBGMavH0pTS21ogKq6RqkAO1E16iYEfOlod4c06hkbC_mbZWbiCU7uINuMVFpxOuJNjQZGL6fflCS5wti1bEz2w24vgCnaWcRy4LvAOMdTck5yjmoCPWLyODTkJqZ1JXF5s7fz_LeR9EcRK9XTJedjSEjOtLExhfw7gtg0aCYN56RrLUt1QHHRZiidQvFx75I04E1qn5jJOEpO1V2W-rsKEMLGEkUsfjmy-ZlmOEDJ_A0DP7egZnMLwy7nkmVQ750R7nQwWNiMLWh1EHrD64GtAB1lhJz7_GQ3GDPzVD9Pd6F6_OwCHeSsxQ7tzi0A"
}
```