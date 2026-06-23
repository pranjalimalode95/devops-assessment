# DevOps Assessment Application

A simple "Hello World" full-stack application built with **Django** (Backend) and **React with Vite** (Frontend).

## Project Overview

- **Backend**: Django 6.0 (REST API)
- **Frontend**: React (Vite, TypeScript, Lucide Icons)
- **Styling**: Premium custom CSS with dark/light mode support.
- **Communication**: REST API using Axios with CORS enabled.

## Getting Started

### Prerequisites
- Python 3.10+
- Node.js 18+
- npm 9+

### Backend Setup (Django)

1. Navigate to the backend directory:
   ```bash
   cd backend
   ```
2. Create and activate a virtual environment:
   ```bash
   python3 -m venv venv
   source venv/bin/activate  # On Windows: venv\Scripts\activate
   ```
3. Install dependencies:
   ```bash
   pip install django django-cors-headers psycopg2-binary
   ```
4. Run the development server:
   ```bash
   python manage.py runserver
   ```
   The backend will be available at `http://localhost:8000/api/hello/`.

### Frontend Setup (React/Vite)

1. Navigate to the frontend directory:
   ```bash
   cd frontend
   ```
2. Install dependencies:
   ```bash
   npm install
   ```
3. Run the development server:
   ```bash
   npm run dev
   ```
   The frontend will be available at `http://localhost:5173/`.

## Architecture Decisions
- **Vite**: Used for its superior development experience and fast build times.
- **Django**: Chosen for its robustness and ease of setting up a structured API.
- **CORS**: Configured in Django to allow the React frontend to fetch data during local development.
- **Responsive Design**: Custom CSS ensures the application looks premium on all screen sizes and supports dark mode.

## Assumptions

This project makes several assumptions that should be understood before deployment:

### Infrastructure & Environment
- **Docker & Docker Compose**: Assumes Docker and Docker Compose are installed and available on the host system.
- **Port Availability**: Assumes ports `8000` (backend) and `3000` (frontend) are available and not in use by other services.
- **Network Configuration**: Assumes services can communicate via `localhost` when running in Docker Compose.

### Backend Assumptions
- **Python Version**: Uses Python 3.14-slim (note: this may be a future version; verify compatibility with your environment).
- **Database**: Uses SQLite (Django's default database) stored in `db.sqlite3` within the container. **Note**: This is not suitable for production and data will be lost when the container is removed unless volumes are configured.
- **Debug Mode**: `DEBUG=true` is set in `docker-compose.yml` for development purposes. **This should be changed to `false` for production deployments.**
- **Superuser Creation**: Automatically creates a Django superuser on container startup with default credentials:
  - Username: `admin`
  - Email: `admin@example.com`
  - Password: `admin123`
  - **Security Note**: These default credentials should be changed in production.
- **CORS Configuration**: `CORS_ALLOW_ALL_ORIGINS = True` is set in Django settings, allowing requests from any origin. **This should be restricted in production.**
- **Secret Key**: Django secret key is hardcoded in `settings.py`. **This should be moved to environment variables for production.**
- **Migrations**: Database migrations run automatically on container startup via the Dockerfile CMD.

### Frontend Assumptions
- **Node Version**: Uses Node.js 20-alpine for building the frontend.
- **API URL**: The `VITE_API_URL` is set to `http://localhost:8000` at build time in `docker-compose.yml`. This means:
  - The frontend expects the backend to be accessible at `http://localhost:8000` from the browser's perspective.
  - If deploying to a different environment, this URL must be updated and the frontend rebuilt.
- **Web Server**: Uses Nginx Alpine to serve the built static files on port 80 (mapped to port 3000 on the host).
- **Build Process**: Frontend is built during Docker image creation, not at runtime.

### Security Assumptions
- **Development Environment**: This configuration is intended for development/demo purposes only.
- **Default Credentials**: Default superuser credentials are acceptable for local development but must be changed for any production use.
- **No Authentication**: The API endpoints do not require authentication (based on current implementation).
- **CORS Policy**: Wide-open CORS policy is acceptable for development but must be restricted for production.

### Deployment Assumptions
- **Service Dependencies**: The `docker-compose.yml` shows `frontend` depends on `backend`, ensuring the backend starts first.
- **Container Communication**: Services communicate via Docker's internal network, but the frontend's API URL points to `localhost:8000`, which works when accessing from the host browser.
- **Data Persistence**: No volumes are configured for database persistence. Container removal will result in data loss.
- **No Health Checks**: No health check endpoints or container health checks are configured in the current setup.

### Production Readiness
**This application is NOT production-ready** and requires the following changes before production deployment:
1. Set `DEBUG=False` in environment variables
2. Configure a production database (PostgreSQL, MySQL, etc.) instead of SQLite
3. Use environment variables for sensitive data (secret key, database credentials, etc.)
4. Restrict CORS to specific allowed origins
5. Change default superuser credentials
6. Configure proper volumes for data persistence
7. Add health checks and proper logging
8. Use a production WSGI server (Gunicorn, uWSGI) instead of Django's development server
9. Configure proper reverse proxy and SSL/TLS
10. Update `VITE_API_URL` to match production backend URL
