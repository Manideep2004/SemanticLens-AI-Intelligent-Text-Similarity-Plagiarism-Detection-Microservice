# SemanticLens AI

Intelligent text similarity and plagiarism-check API, deployed with Docker Compose.

The API compares two pieces of text using NLP (spaCy) and returns a similarity score.

## API endpoints

| Resource | Path | Method | Parameters | Status |
|----------|------|--------|------------|--------|
| Register a user | `/register` | POST | `username`, `password` | `200` OK, `301` invalid username |
| Detect similarity | `/detect` | POST | `username`, `password`, `text1`, `text2` | `200` returns similarity, `301` invalid username, `302` invalid password, `303` out of tokens |
| Refill tokens | `/refill` | POST | `username`, `admin_pw`, `refill` | `200` OK, `301` invalid username, `304` invalid admin password |

Admin password for refill: `abc123`

## Requirements

- [Docker](https://www.docker.com/)
- Docker Compose (included with Docker Desktop)

spaCy, Flask, PyMongo, and bcrypt are installed automatically inside the Docker image.

## How to run

1. Open a terminal in this project folder.

2. Start the app:
```bash
docker compose up --build -d
```

3. Check that both services are running:
```bash
docker compose ps
```

The API is available at `http://localhost:5001`

4. Register a user:
```bash
curl -X POST http://localhost:5001/register \
  -H "Content-Type: application/json" \
  -d '{"username":"alice","password":"alice123"}'
```

5. Compare two texts:
```bash
curl -X POST http://localhost:5001/detect \
  -H "Content-Type: application/json" \
  -d '{"username":"alice","password":"alice123","text1":"Machine learning is fun","text2":"Machine learning is very fun"}'
```

6. Refill tokens:
```bash
curl -X POST http://localhost:5001/refill \
  -H "Content-Type: application/json" \
  -d '{"username":"alice","admin_pw":"abc123","refill":20}'
```

7. Stop the app:
```bash
docker compose down
```

## Example output

Register:
```json
{"msg":"You successfully signed up for the API","status":200}
```

Detect:
```json
{"msg":"Similarity score calculated successfully","ratio":0.89,"status":200}
```

Refill:
```json
{"msg":"Refilled successfully","status":200}
```

A higher `ratio` means the two texts are more similar.
