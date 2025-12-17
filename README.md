# GoREST API Portfolio (Postman + Newman)

This repo contains a **Postman collection** that tests the **GoREST public REST API** and a **Newman** setup that runs the collection locally or in GitHub Actions and produces an **HTML report**.

## Collection structure (current)

### 00_Setup
- **GET** `00_01_Get ALL Users (sanity)`

### 01_Users
- **POST** `01_01_Create a user`
- **GET** `01_02_Get created user by ID`

### 02_Posts
- **POST** `02_01_Create a post`
- **GET** `02_02_Get created post by ID`
- **GET** `02_03_Get ALL posts created by the user`
- **PATCH** `02_04_Update post`

### 03_Comments
- **POST** `03_01_Create a comment`
- **GET** `03_02_Get comment by ID`
- **GET** `03_03_Get list of comments for post`

### 90_Auth Negative
- **POST** `90_01_Create user without token` (expects 401/403)
- **POST** `90_02_Create post without token` (expects 401/403)

### 95_Cleanup
- **DELETE** `95_01_Delete a comment`
- **GET** `95_02_Get deleted comment`
- **DELETE** `95_03_Delete a post`
- **GET** `95_04_Get deleted post`
- **DELETE** `95_05_Delete user`
- **GET** `95_06_Get_deleted_user`

## Files

- Collection: `GoREST API Portfolio.postman_collection.json`
- Environment (token intentionally empty): `GoREST - Portfolio.postman_environment_NO_TOKEN.json`

## Environment variables

Configured in the Postman environment:

- `base_url` = `https://gorest.co.in/public/v2`
- `token` (empty in the exported environment, injected at runtime)
- `user_id`
- `post_id`
- `comment_id`

The collection stores IDs from create calls into the environment so later requests can reference them.

## Token handling (safe for GitHub)

This repo keeps the exported environment **token empty**.  
You provide the token at runtime:

- Locally: via `--env-var token=...`
- CI: via GitHub Actions secret `GOREST_TOKEN`

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

## GitHub Actions (CI)

This project is set up to run on a **self-hosted runner** (useful if the API blocks GitHub-hosted runner IPs).

### Required secret
Add a repository secret:

- `GOREST_TOKEN` = your GoREST personal access token

Path:
- Repo → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

### Report artifact
Each workflow run uploads:
- `newman-report` (contains `newman/report.html`)

## Notes

- Numeric prefixes keep request order readable and stable in runs.
- `90_Auth Negative` requests should use **No Auth** (by design).
- Cleanup runs at the end to reduce leftover test data.

## Author

Üllar Kappak
