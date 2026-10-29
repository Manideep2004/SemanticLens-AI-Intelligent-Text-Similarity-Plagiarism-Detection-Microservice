# SemanticLens AI

SemanticLens AI is a small REST service that checks how similar two texts are. It uses spaCy for language processing, Flask for the API, MongoDB for user data, and Docker Compose to run everything together.

This is useful for a simple plagiarism-style check: send two sentences or paragraphs, and the service returns a score between 0 and 1. Closer to 1 means the texts are more alike.

Each user starts with a limited number of tokens. Every `/detect` call spends one token. Tokens can be restored with the admin refill endpoint.

## Project layout

```text
.
├── docker-compose.yml   # starts the web API and MongoDB
├── db/
│   └── Dockerfile       # MongoDB image
└── web/
    ├── Dockerfile       # Flask app image
    ├── app.py           # API logic
    └── requirements.txt # Python packages
```

## Endpoints

Base URL after startup: `http://localhost:5001`

| What it does | Path | Method | JSON body | What you get |
|--------------|------|--------|-----------|--------------|
| Create an account | `/register` | POST | `username`, `password` | `200` if the user is created, `301` if that username already exists |
| Score two texts | `/detect` | POST | `username`, `password`, `text1`, `text2` | `200` plus a `ratio`, `301` unknown user, `302` wrong password, `303` no tokens left |
| Add tokens | `/refill` | POST | `username`, `admin_pw`, `refill` | `200` if tokens are updated, `301` unknown user, `304` wrong admin password |

The admin password used by `/refill` is `abc123`.

## What you need installed

Only Docker Desktop (or Docker Engine plus Compose) is required on your machine. Python packages and the spaCy English model are installed when the web image is built.

## Run the service

From the project root:

```bash
docker compose up --build -d
```

Confirm both containers are up:

```bash
docker compose ps
```

You should see a `web` service mapped to port `5001` and a `db` service for MongoDB.

## Try the API

Create a user:

```bash
curl -X POST http://localhost:5001/register \
  -H "Content-Type: application/json" \
  -d '{"username":"alice","password":"alice123"}'
```

Compare two texts:

```bash
curl -X POST http://localhost:5001/detect \
  -H "Content-Type: application/json" \
  -d '{"username":"alice","password":"alice123","text1":"Machine learning is fun","text2":"Machine learning is very fun"}'
```

Restore tokens:

```bash
curl -X POST http://localhost:5001/refill \
  -H "Content-Type: application/json" \
  -d '{"username":"alice","admin_pw":"abc123","refill":20}'
```

## Sample responses

Successful registration:

```json
{"msg":"You successfully signed up for the API","status":200}
```

Successful similarity check:

```json
{"msg":"Similarity score calculated successfully","ratio":0.89,"status":200}
```

Successful refill:

```json
{"msg":"Refilled successfully","status":200}
```

## Stop the service

```bash
docker compose down
```

To also drop the MongoDB volume:

```bash
docker compose down -v
```
