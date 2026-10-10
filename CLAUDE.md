# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A small Express REST API (users + health check) with an in-memory data store, used as the starter project for the Claude Code course.

## Commands

- `npm run dev` — start the API with auto-reload on http://localhost:3000 (`PORT` overrides)
- `npm test` — run all tests (Node's built-in `node:test` runner + supertest)
- `node --test tests/users.test.js` — run one test file
- `node --test --test-name-pattern="404" tests/users.test.js` — run only tests whose name matches (flags go before the file path)
- `npm run lint` — ESLint (`eslint:recommended`)

CI (`.github/workflows/ci.yml`, Node 22) runs `npm run lint` then `npm test` on every push and PR — both must pass.

## Conventions

- CommonJS only: use `require` / `module.exports`, not `import` / `export` (ESLint is configured with `sourceType: "script"`).
- Tests use `node:test` and `node:assert`, not Jest or Mocha; hit endpoints with `supertest` against the exported `app`, never a live port.
- Routes never touch data directly — read and write through functions exported from `db/store.js`.
- Errors are JSON shaped as `{ "error": "<message>" }` with the matching status (400 for invalid input, 404 for missing resources); successful creates return 201.
- Each route handler gets a one-line comment naming the method, path, and behavior (e.g. `// GET /users/:id — ...`).


## Architecture

- `server.js` builds the Express app, mounts one router per resource (`/users`, `/health`), and exports `app`. It only calls `listen()` when run directly (`require.main === module`), which is what lets tests import it.
- `routes/` — one file per resource, each exporting an `express.Router()`. A new resource means a new file here plus an `app.use(...)` line in `server.js`.
- `db/store.js` — in-memory stand-in for a database; state lives at module level, resets on restart, and is shared across all tests in a run (tests that create users affect later reads).
- The app does not load `.env` (no dotenv); only `process.env.PORT` is read.
