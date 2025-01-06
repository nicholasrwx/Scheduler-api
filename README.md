# Interview Scheduler API

## DB Setup

- Install postgres 15.x ( provide the default password `development` )
- Connect to postgres `psql -U postgres` ( enter default password if required )
- Create role with `Create USER development;` ( grants Login privileges )
- Ensure new role exists with `\du` command
- Create a database with `CREATE DATABASE scheduler_development;`
- Disconnect from postgres with `\q`
- Connect with `psql -U development -d scheduler_development` to ensure new user can login and
  connect to db

## API Setup

- Install dependencies with `npm install`.
- Copy the `.env.example` file to `.env.development` and fill in PostgreSQL configuration. The `node-postgres` library uses these environment variables by default.

```
PGHOST=localhost
PGUSER=development
PGDATABASE=scheduler_development
PGPASSWORD=development
PGPORT=5432
```

## Additonal Postgres Commands For Accessibility Issues
  Postgres Users are refered to as "Roles with Login Privileges"

  *Creating a role:*
  - `CREATE USER username;` // has default login privileges
  - `CREATE USER username WITH PASSWORD 'supersecretpassword';`
  - `CREATE ROLE username;` // doesn't have default login privileges
  - `CREATE ROLE username LOGIN WITH PASSWORD 'supersecretpassword';`

  *Add a privilege to a role:*
  - `ALTER ROLE your_user WITH LOGIN;` // provide login privilege

  *Grant connect privilege on the database:*
  - `GRANT CONNECT ON DATABASE scheduler_development TO development;`

  *Grant a user access to the custom database schema:*
  -  `GRANT USAGE ON SCHEMA scheduler_development TO development;`

  *Grant explicit privileges on schema:*
  - `GRANT SELECT, INSERT, UPDATE, DELETE ON ALL TABLES IN SCHEMA scheduler_development TO
    development;`

  *Ensure user has privileges on future tables and sequences:*
  - `ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT, INSERT, UPDATE, DELETE ON TABLES TO development;`
  - `ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT USAGE, SELECT ON SEQUENCES TO development;`

  *Grant a user access to the postgres public schema:*
  - `GRANT USAGE ON SCHEMA public TO development;`
  - `GRANT USAGE ON SCHEMA public TO postgres;`

  *Grant a user superuser privileges:*
  - `ALTER ROLE development WITH SUPERUSER;`
  
## Seeding

- Run the development server with `npm start`.

Both of these achieve the same result.

- Make a `GET` request to `/api/debug/reset` with `curl http://localhost:8001/api/debug/reset`.
- Use the browser to navigate to `http://localhost:8001/api/debug/reset`.

The `development` data is random. Each time we seed we expect to see different appointments.

## Run The Server

Running the server normally
```sh
npm start
```

Running the server so it returns an error when saving/deleting for testing the client's error handling capabilities
```sh
npm run error
```

## Api

### Days

`GET /api/days`

Response

```json
[
  {
    "id": 1,
    "name": "Monday",
    "appointments": [1, 2],
    "interviewers": [1, 2],
    "spots": 0
  }
]
```

### Appointments

`GET /api/appointments`

Response:

```json
{
  "1": {
    "id": 1,
    "time": "12pm",
    "interview": {
      "student": "Lydia Miller-Jones",
      "interviewer": 1
    }
  },
  "2": {
    "id": 2,
    "time": "1pm",
    "interview": {
      "student": "Archie Cohen",
      "interviewer": 2
    }
  }
}
```

`PUT /api/appointments/:id`

Body:

```json
{
  "interview": {
    "student": String,
    "interviewer": Number
  }
}
```

`DELETE /api/appointments/:id`

### Interviewers

`GET /api/interviewers`

Response:

```json
{
  "1": {
    "id": 1,
    "name": "Sylvia Palmer",
    "avatar": "https://i.imgur.com/LpaY82x.png"
  },
  "2": {
    "id": 2,
    "name": "Tori Malcolm",
    "avatar": "https://i.imgur.com/Nmx0Qxo.png"
  }
}
```
