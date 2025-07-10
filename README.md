# Chat Server Application

## Description

A chat server application that stores users and administrators in a PostgreSQL database. The server is written in C++ using Qt6. Supports running via Docker or manually on Linux.

---

## Architecture

- **Server** (`Server`) — the main executable file implementing chat logic, authentication, and client handling.
- **Database** — PostgreSQL, with `users` and `admins` tables.
- **Docker** — for easy deployment (`docker-compose.yml`, `Dockerfile`).

---

## Quick Start

### Running with Docker

1. Install [Docker](https://docs.docker.com/get-docker/) and [Docker Compose](https://docs.docker.com/compose/install/).
2. In the project directory, run:
   ```sh
   docker-compose up --build
   ```
   This will start two containers: the database and the server.

### Manual Run (without Docker)

1. Install dependencies:
   - Qt6 (core, gui, widgets, network, sql, sql-psql)
   - PostgreSQL
2. Build the server:
   ```sh
   make
   ```
3. Run the server:
   ```sh
   ./Server
   ```

---

## Port Forwarding and Firewall Setup (Windows/WSL) - If necessary!

To allow external access to the server, run the following in an administrator PowerShell:

```sh
netsh interface portproxy add v4tov4 listenport=8080 listenaddress=0.0.0.0 connectport=8080 connectaddress=172.22.26.96
netsh advfirewall firewall add rule name="WSL2 8080" dir=in action=allow protocol=TCP localport=8080
```
- `172.22.26.96` — the IP address of your WSL/virtual machine, replace with your own if necessary.

---

## Working with the Database

### Table Structure

**admins**
- `id` SERIAL PRIMARY KEY
- `username` VARCHAR(50) NOT NULL UNIQUE
- `password_hash` VARCHAR(255) NOT NULL

**users**
- `id` SERIAL PRIMARY KEY
- `username` VARCHAR(50) NOT NULL UNIQUE
- `surname` TEXT
- `age` INTEGER
- `post` TEXT
- `password_hash` VARCHAR(255)

### Adding Administrators and Users

> **Important!** Adding users and administrators is done manually via the console by the server administrator.

#### Enabling Password Hashing in PostgreSQL

To use password hashing directly in PostgreSQL (e.g., with bcrypt), you need to enable the `pgcrypto` extension:

```sql
CREATE EXTENSION IF NOT EXISTS pgcrypto;
```

This allows you to use the `crypt` and `gen_salt` functions for secure password hashing.

#### Example Commands for PostgreSQL

1. Enter psql:
   ```sh
   docker exec -it <db_container_name> psql -U postgres -d user_information
   ```
   or if the database is on the local machine:
   ```sh
   psql -U postgres -d user_information
   ```

2. Add an administrator:
   ```sql
   INSERT INTO admins (username, password_hash) VALUES ('admin1', crypt('your_password', gen_salt('md5')));
   ```

3. Add a user:
   ```sql
   INSERT INTO users (username, surname, age, post, password_hash) VALUES ('user1', 'Ivanov', 30, 'manager', crypt('user_password', gen_salt('md5')));
   ```

4. To update a user's password:
   ```sql
   UPDATE users SET password_hash = crypt('new_password', gen_salt('bf')) WHERE username = 'user1';
   ```

**The password is securely hashed using bcrypt via the `crypt` function.**

---

## Client Application

### Installation

The client application is installed using the provided setup application (`ChatSetup`).

### Server Connection Setup

Before connecting, the client must add the server details (server IP and port) in the options menu. These details are provided by the server administrator.

### Starting a Chat

To start a chat:
1. Select a user in the right-hand list with the mouse.
2. A context menu will appear; choose **Start Chat**.
3. A chat window will open for communication.

---

## Authentication

- On login, the user or administrator enters their username and password.
- The server checks the username and password hash against the corresponding table.
- If the data matches, access is granted.

---

## FAQ

- The server listens on port 8080 (can be changed in docker-compose).
- For GUI clients to work, X11 forwarding is required (see volumes in docker-compose).
- All changes in the database (adding/removing users and admins) are performed only manually via SQL.

---

## Contacts and Support

For any questions, please contact the server administrator. 

---

## Additionally

This is not the final version of the app, and it will continue to be updated in the future!
