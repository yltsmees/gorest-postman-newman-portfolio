# GoREST API Tests (Postman + Newman)

This repo contains an API testing portfolio project built with **Postman** and executed with **Newman**.  
It covers a realistic CRUD flow using the **GoREST public API** and generates an **HTML test report** in CI.

## What’s inside

- Postman Collection: `GoREST API Portfolio.postman_collection.json`
- Postman Environment (token omitted): `GoREST - Portfolio.postman_environment_NO_TOKEN.json`
- CI: GitHub Actions workflow runs Newman and uploads an HTML report artifact

## Test scope

### 00_Setup
- **GET** all users (sanity)

### 01_Users
- **POST** create a user (randomized email)
- **GET** created user by id

### 02_Posts
- **POST** create a post
- **GET** created post by id
- **GET** all posts for the created user
- **PATCH** update post
- **DELETE** delete post
- **GET** deleted post (negative, expects “Resource not found”)

### 03_Comments
- **POST** create a comment
- **GET** comment by id
- **GET** list comments for post

### 90_Auth Negative
- Create user without token (expects 401/403)
- Create post without token (expects 401/403)

### 95_Cleanup
- Cleanup requests to remove created data (when applicable)

## How it works

The collection saves created IDs into environment variables, for example:
- `user_id`
- `post_id`
- `comment_id`

The workflow injects the token at runtime via `--env-var`, so the environment file stays safe to publish.

## Run locally

### 1) Install Newman
```bash
npm i -g newman newman-reporter-htmlextra
```

### 2) Run collection (CLI + HTML report)
```bash
mkdir -p newman

newman run "GoREST API Portfolio.postman_collection.json" \
  -e "GoREST - Portfolio.postman_environment_NO_TOKEN.json" \
  --env-var "token=PASTE_YOUR_GOREST_TOKEN_HERE" \
  -r cli,htmlextra \
  --reporter-htmlextra-export "newman/report.html"
```

Open:
- `newman/report.html`

## Run in GitHub Actions (CI)

This project uses a **self-hosted runner** because some public APIs may block GitHub-hosted runner IPs.

### Required secret
Add this repository secret:

- `GOREST_TOKEN` = your GoREST personal access token

Path:
- Repo → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

### Workflow output
Each run uploads an artifact:
- `newman-report` (contains `report.html`)

## Notes

- The environment JSON in this repo has an **empty token** by design.
- Tests are named with numeric prefixes to keep execution order readable and stable.
- This is a portfolio project: the goal is clean structure, reliable assertions, and clear reporting.
