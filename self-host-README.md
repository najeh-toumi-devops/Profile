# Self-hosting `github-readme-stats`

This folder contains instructions and minimal Docker files to run a self-hosted instance of `github-readme-stats`.

Why self-host?
- Avoid public service rate limits
- Ensure `count_private=true` actually returns private contributions by supplying a GitHub token

Files included:
- `Dockerfile` — clones the upstream repo and runs the app
- `docker-compose.yml` — quick local run with environment variable wiring

Environment variables
- `GITHUB_TOKEN` (recommended) — a GitHub personal access token with `repo` scope to include private contributions. Export it locally or store it in your deployment platform's secrets.
- `PORT` — port to expose (default `3000`).

Run locally (recommended for testing):

1. Build the image:

```powershell
docker build -t github-readme-stats:local .
```

2. Run (passing your token):

```powershell
docker run -e GITHUB_TOKEN=$env:GITHUB_TOKEN -p 3000:3000 github-readme-stats:local
```

Or with `docker-compose` (create a `.env` file with `GITHUB_TOKEN=...`):

```powershell
docker compose up -d --build
```

Visit `http://localhost:3000/api?username=YOUR_USERNAME&count_private=true` to verify.

Deploying to a platform
- Vercel: Connect the repository to Vercel and set the `GITHUB_TOKEN` secret in the project settings. Vercel will build the app from the repo (no Docker required) or you can deploy the Docker image using Vercel's Docker support.
- Heroku (using container registry):
  1. `heroku login`
  2. `heroku container:login`
  3. `docker build -t registry.heroku.com/<app-name>/web .`
  4. `docker push registry.heroku.com/<app-name>/web`
  5. `heroku container:release web -a <app-name>`
  6. Set `GITHUB_TOKEN` in Heroku config vars.

GitHub Actions / Automated deploy
- You can create a workflow to build and push the image to a container registry (Heroku, Docker Hub) or trigger a Vercel deployment. If you want, I can add a workflow template matching your chosen provider.

Security note
- Keep `GITHUB_TOKEN` secret. Give it the minimal scope required (repo access if you need private stats). Avoid committing tokens to the repository.

Questions or next step: tell me whether you want an automated deploy workflow for Heroku, Vercel, or Docker Hub and I'll add it.
