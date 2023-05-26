Login-secure 
============

This repository contains a JSON API for user registration and login.

## Getting Started

The easiest way to run the API is using `docker-compose`. Running `docker-compose up` in the root directory should build the application and start the API and the database. The application by default will be running on port 8080.

## Generating JWTs
Authorisation is handled using [JSON Web Tokens](https://jwt.io). By default, the signing key used is `SuperSecret`. A helper tool in `./cmd/token/` is provided to generate arbitrary tokens at need.

Running `make` inside the root directory will provide two binaries inside the `./bin/` directory, respectively `login` and `token`. You can use the `token` binary to generate arbitrary tokens, for example:

```bash
./token       
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6ZmFsc2UsImVtYWlsIjoiY29vbGJldEBpZG9udGV4aXN0Lm5vcGUifQ.QASdrnofzItwCyrXIQhA_qgAU6NXdLx8kufN2YEHsTI
```

You can use the `-admin` flag to generate a token with `admin` set to `true` and the `-key` flag to modify the signign string.

NOTE: The generation of the token via the `token` binary is OUT OF SCOPE for this exercise.

## Interacting with the API

The API has 4 endpoints:

* GET /users
* GET /me
* POST /register
* POST /login

Both `/register` and `/login` are unauthenticated endpoints. `/me` requires authentication and fetches own information from the database. `/users` requires authentication AND requires admin access (i.e., `admin` set to true in the JWT).

In order to interact with the authenticated endpoints, you will need to set the `Authorization` header to `Bearer $JWT_TOKEN`.

The JWT token can be generated using the tool as explained above, or through the `login` endpoint.

### Register
It is possible to register a new user by issuing a request such as:

```
curl localhost:8080/register -d '{"email":"coolbet@cool.bet", "password": "pass", "name": "Jon", "surname": "Doe"}'

{"id":"","email":"coolbet@cool.bet","password":"pass","name":"Jon","surname":"Doe","admin":false}
```

### Login

It is possible to perform a 'login' in the system by issuing a request such as:

```bash
curl localhost:8080/login  -d '{"email":"coolbet@cool.bet", "password": "pass"}'

{"token":"eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6ZmFsc2UsImVtYWlsIjoiY29vbGJldEBjb29sLmJldCJ9.XNGaiqSfZji8_PJDAHnt6oeXC_I5VdXJkIhwc6JNMCk"}
```

In the response it is possible to see the JWT token that can be used for following requests.

### Me

It is possible to fetch information about own user issuing a request such as:

```
curl localhost:8080/me -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6ZmFsc2UsImVtYWlsIjoiY29vbGJldEBjb29sLmJldCJ9.XNGaiqSfZji8_PJDAHnt6oeXC_I5VdXJkIhwc6JNMCk'

{"id":"1","email":"coolbet@cool.bet","password":"","name":"Jon","surname":"Doe","admin":false}
```

### Users

It is possible -for admins only- to fetch the list of all users issuing a request such as:

```
./bin/token -admin
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiZW1haWwiOiJjb29sYmV0QGlkb250ZXhpc3Qubm9wZSJ9.6U9YdAQw4FJlSNnOMSb7K-zDJ05UQb22VWRP4QI6Zok 

curl localhost:8080/users -H 'Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJhZG1pbiI6dHJ1ZSwiZW1haWwiOiJjb29sYmV0QGlkb250ZXhpc3Qubm9wZSJ9.6U9YdAQw4FJlSNnOMSb7K-zDJ05UQb22VWRP4QI6Zok%'

{"users":[{"id":"1","email":"coolbet@cool.bet","password":"","name":"Jon","surname":"Doe","admin":false}]}
```
