# nodejs-demo-app: CI/CD with GitHub Actions and Docker

A small Node.js (Express) app with an automated CI/CD pipeline. On every push to `main`, GitHub Actions tests the code, builds a Docker image, and pushes it to Docker Hub.

## Pipeline flow

```
push to main -> test (npm test) -> build Docker image -> push to Docker Hub
```

Workflow file: `.github/workflows/main.yml`

| Job | What it does |
|-----|--------------|
| `test` | Checks out code, sets up Node.js 20, installs dependencies, runs Jest tests |
| `build-and-push` | Runs only if `test` passes (`needs: test`). Logs in to Docker Hub, builds the image, pushes `latest` and a commit-SHA tag |

## Project structure

```
app.js                      Express app (routes: /, /health)
server.js                   Starts the server on port 3000
test/app.test.js            Jest + Supertest tests
Dockerfile                  Image definition (node:20-alpine)
.github/workflows/main.yml  CI/CD pipeline
```

## Setup

1. Create a Docker Hub access token (Account Settings > Security > New Access Token).
2. In the GitHub repo go to Settings > Secrets and variables > Actions and add:
   - `DOCKERHUB_USERNAME`: your Docker Hub username
   - `DOCKERHUB_TOKEN`: the access token (not your password)
3. Push to `main`. The pipeline runs automatically from the Actions tab.

## Run locally

```bash
npm install
npm test
npm start                   # http://localhost:3000

docker build -t nodejs-demo-app .
docker run -p 3000:3000 nodejs-demo-app
```

## Notes

- Credentials are stored as encrypted GitHub Secrets and never appear in the code.
- Each image is tagged with `latest` and the commit SHA, so any deployment can be traced or rolled back.
- Screenshots of a successful run are in the `screenshots/` folder.
