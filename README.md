# From the Other Side

A minimal Node.js platform for sharing ghostly encounters. It serves a static website, exposes a JSON API for reading and uploading sightings, and broadcasts a live news feed using Server‑Sent Events (SSE).

## Features

- Static site: Serves pages from `public/` including home, sightings list, upload form, and a beta news feed.
- JSON API: `GET /api` returns all sightings; `POST /api` creates a new sighting.
- Input sanitization: Cleans user‑submitted text via `sanitize-html` to reduce XSS risk.
- Persistent storage: Appends new sightings to `data/data.json`.
- Event‑driven alerts: Emits `sighting-added` and logs an alert for the location.
- Live news (SSE): `GET /api/news` streams random headlines to the client in real time.

## Tech & Node.js Concepts

- Node core modules: `http`, `fs/promises`, `path`, `events`.
- ES Modules: `type: "module"` in `package.json`, `import` syntax, and `import.meta.dirname`.
- HTTP server + routing: Branching on `req.url` and `req.method` in `server.js`.
- Static file serving: Safe path resolution + content type detection (`utils/serveStatic.js`, `utils/getContentType.js`).
- JSON body parsing: Async iteration over request stream (`utils/parseJSONBody.js`).
- Input sanitization: `sanitize-html` cleans string fields (`utils/sanitizeInput.js`).
- EventEmitter: `sighting-added` flow (`events/sightingEvents.js`, `utils/createAlert.js`).
- Server‑Sent Events (SSE): Streaming endpoint in `handleNews()`.

## Project Structure

```
server.js
package.json
public/
	index.html, index.css, index.js
	sightings.html
	upload-sighting.html, upload-sighting.js
	news.html, news.js
	images/
handlers/routeHandlers.js
utils/
	addNewSighting.js
	createAlert.js
	getContentType.js
	getData.js
	parseJSONBody.js
	sanitizeInput.js
	sendResponse.js
	serveStatic.js
events/sightingEvents.js
data/data.json
data/stories.js
```

## API

- GET /api
  - Returns an array of sightings from `data/data.json`.
- POST /api
  - Accepts JSON `{ location, timeStamp, title, text }`. Sanitizes and appends to `data/data.json`. Returns the created object.
- GET /api/news
  - SSE endpoint that periodically sends `{ event: "news-update", story }` messages.

## Setup & Run

Prerequisite: Node.js 18+ (Node 20+ recommended).

```bash
npm install
npm start
# Open http://localhost:8000
```

Optional:

```bash
npx nodemon server.js
```

## Deployment

Recommended platform: Render (Web Service)

- Push the project to GitHub.
- On Render, create a new Web Service from the repo.
- Build Command: `npm install`
- Start Command: `npm start`
- Ensure Node version (e.g., 20) if needed.
- The server listens on `process.env.PORT || 8000` and will use the `PORT` provided by Render.

Alternatives:

- Railway: Similar setup; use `npm start` and ensure `PORT` is honored.
- Fly.io: Deploy with a Dockerfile; supports long‑lived Node processes and custom ports.

Note: SSE requires a persistent server; avoid serverless/edge runtimes that may close connections.

## License

ISC (see `package.json`).
