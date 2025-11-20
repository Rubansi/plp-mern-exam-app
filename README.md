# Quiz-App

A simple Quiz / Results tracker built as a bootcamp project. This repository contains a Node.js + Express backend and a React + Vite frontend. The backend exposes authentication and results APIs; the frontend is a small React app that consumes those endpoints.

## Table of contents
- Project overview
- Tech stack
- Features
- Prerequisites
- Quick start
  - Backend
  - Frontend
- Environment variables
- API endpoints (summary)
- Project structure
- Development notes
- Contributing
- License

## Project overview

This project implements user authentication and result tracking for quizzes. Users can register/login and save quiz results (title, technology, level, total questions, correct/wrong answers). The backend calculates score and performance automatically.

## Tech stack

- Backend: Node.js, Express, Mongoose (MongoDB)
- Frontend: React (Vite)
- Auth: JSON Web Tokens (JWT)
- Utilities: axios (frontend), bcryptjs (password hashing)

## Features

- User registration and login
- Protected endpoints for creating and listing results
- Result model computes score (%) and performance category (Excellent, Good, Average, Needs Work)
- Simple React app to authenticate and view/save results

## Prerequisites

- Node.js (v16+ recommended)
- npm or yarn
- A MongoDB database (MongoDB Atlas or local)

## Quick start

Clone the repository and install dependencies for backend and frontend.

1) Clone

```bash
git clone <your-repo-url> Quiz-App
cd Quiz-App
```

2) Backend

```bash
cd backend
npm install
# Start the backend (development with nodemon)
npm run start
```

The backend listens on port defined by `PORT` (defaults to 5000). You should see `Server Started on http://localhost:5000` in console.

3) Frontend

Open a second terminal and run:

```bash
cd frontend
npm install
npm run dev
```

Vite dev server will start (usually at http://localhost:5173). The frontend expects the backend API under `/api` (CORS is enabled on the backend by default).

## Environment variables

The repo currently contains a hard-coded MongoDB connection string in `backend/config/db.js`. For production or local development you should replace that with environment variables.

Create a `.env` file in the `backend` folder (and add `.env` to `.gitignore`) and set values like:

```text
# Example approach: single URI
MONGODB_URI=mongodb+srv://<username>:<password>@<cluster>/<dbname>?retryWrites=true&w=majority

# Optional
PORT=5000
JWT_SECRET=your_jwt_secret_here
```

Or follow the commented guidance inside `backend/config/db.js` and store components such as `MONGO_USER`, `MONGO_PASS`, `MONGO_CLUSTER`, `MONGO_DB` and build the URI in code.

Make sure your IP is whitelisted in Atlas (or use 0.0.0.0/0 for quick testing) and that the DB user has appropriate privileges.

## API endpoints (summary)

Base URL: http://localhost:5000 (or `http://localhost:${PORT}` if you set PORT)

- Authentication (public)
  - POST /api/auth/register
    - Request body: { name, email, password }
    - Response: typically user data + token (depends on controller implementation)
  - POST /api/auth/login
    - Request body: { email, password }
    - Response: token and user data

- Results (protected — require Authorization: Bearer <token>)
  - POST /api/results
    - Create a new result. Body example:
      {
        title: "Frontend Quiz",
        technology: "react",
        level: "intermediate",
        totalQuestions: 10,
        correct: 8
      }
    - The backend computes `score` (percentage) and `performance` automatically before saving.
  - GET /api/results
    - Lists results for the authenticated user (implementation may include all or user-specific results depending on controller logic).

Note: The server exposes `GET /` which returns `API Working`.

## Data shapes (models)

- User
  - name: string
  - email: string (unique)
  - password: string (hashed)

- Result
  - user: ObjectId (ref User)
  - title: string
  - technology: string (enum: html, css, js, react, node, mongodb, java, python, cpp, bootstrap)
  - level: string (enum: basic, intermediate, advanced)
  - totalQuestions: number
  - correct: number
  - wrong: number
  - score: number (computed %)
  - performance: enum (Excellent, Good, Average, Needs Work)

## Project structure (key files)

- backend/
  - index.js — Express app entrypoint, applies middleware and registers routes
  - package.json — backend dependencies & scripts (`start` uses nodemon)
  - config/db.js — Mongoose connection helper (update to use .env for credentials)
  - controllers.js/ — contains `userController.js` and `resultController.js` (business logic)
  - models/ — `userModel.js`, `resultModel.js` (Mongoose schemas)
  - routes/ — `userRoutes.js` (auth) and `resultRoutes.js` (results)
  - middlewares/auth.js — JWT authentication middleware used to protect result endpoints

- frontend/
  - package.json — frontend dependencies and scripts (`dev`, `build`, `preview`)
  - src/ — React application source (components, pages, assets)

## Development notes

- The backend currently uses `nodemon` via `npm run start` for hot-reload in development. For production you can run `node index.js` or add a `start:prod` script.
- CORS is enabled in the backend; if your frontend runs on a different host/port, requests should work out-of-the-box.
- Protected routes expect a JWT token, typically returned by login/register controllers. Include it in requests as:

  Authorization: Bearer <token>

## Tests

There are no automated tests in this repository yet. Recommended next steps:

- Add unit tests for controllers and model hooks (e.g., score computation)
- Add integration tests for auth and protected endpoints using a test database

## Deployment

Backend:
- Host on platforms such as Render, Heroku, Railway, or Vercel (Serverless) — set environment variables (MONGODB_URI, JWT_SECRET, PORT) in the host dashboard.

Frontend:
- Build with `npm run build` and deploy static assets to Netlify, Vercel, GitHub Pages, or any static host. Configure the frontend to use your deployed backend URL as the API base (environment variable or runtime config).

## Contributing

Contributions are welcome. Suggested workflow:

1. Fork the repo
2. Create a branch: `git checkout -b feat/your-feature`
3. Make changes and add tests where possible
4. Open a pull request with a clear description of what you changed

## License

This project does not currently declare a license. Add a LICENSE file if you plan to make this public and want a specific license.

## Contact / Help

If you need help setting up MongoDB Atlas, follow the instructions commented inside `backend/config/db.js`. The code file contains step-by-step instructions for creating an Atlas user, whitelisting IP, and forming the connection string.

---
