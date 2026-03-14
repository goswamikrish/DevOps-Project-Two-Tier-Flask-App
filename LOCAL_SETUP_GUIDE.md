# Local Setup Guide

This document details the exact steps taken to configure, troubleshoot, and successfully run the Two-Tier Flask Application (Flask + MySQL) on your local Windows machine. 

---

## 1. Creating a Virtual Environment
**What was done:** A Python virtual environment (`venv`) was created inside the project directory.
**Command:** `python -m venv venv`
**Why:** Virtual environments are essential in Python to isolate project dependencies from the system-wide Python installation. This ensures that the specific versions of libraries required for this project do not interfere with other projects on your machine.

## 2. Updating Application Configuration (`app.py`)
**What was done:** The default fallback values for MySQL connection parameters in `app.py` were updated.
**Changes made in `app.py`:**
- `app.config['MYSQL_USER']` fallback changed from `'default_user'` to `'root'`
- `app.config['MYSQL_PASSWORD']` fallback changed from `'default_password'` to `'root'`
- `app.config['MYSQL_DB']` fallback changed from `'default_db'` to `'devops'`
**Why:** The `docker-compose.yml` file is configured to spin up a MySQL container with the root user, root password, and a database named `devops`. By updating `app.py` to match these as fallbacks, you can safely run the Flask app locally without needing to manually define environment variables in your command line every time.

## 3. Resolving Python 3.14 Compatibility Issues (`requirement.txt`)
**What was done:** The versions of `Flask`, `Werkzeug`, and `Flask-MySQLdb` were upgraded in your `requirement.txt` file by removing strict outdated version locks.
**Changes made in `requirement.txt`:**
- `Flask==2.0.1` -> `Flask>=3.0.0`
- `Werkzeug==2.2.2` -> `Werkzeug>=3.0.0`
- `Flask-MySQLdb==0.2.0` -> `Flask-MySQLdb>=2.0.0`
**Why:** 
1. Older versions of Flask and Werkzeug rely on `pkgutil.get_loader`, which was deprecated and completely removed in Python 3.14. Using `Flask>=3.0.0` and `Werkzeug>=3.0.0` resolves this fatal crash.
2. Older versions of `Flask-MySQLdb` rely on `_app_ctx_stack`, which was removed in `Flask 3.0.0`. Upgrading to `Flask-MySQLdb>=2.0.0` ensures compatibility with the newer version of Flask.

## 4. Installing the Dependencies
**What was done:** All required packages were installed inside the active virtual environment.
**Command:** `.\venv\Scripts\pip install -r requirement.txt`
**Why:** This installs the exact libraries the codebase needs (like `Flask` to render the web server and `Flask-MySQLdb` to interact with the database).

## 5. Spinning up the Database Container
**What was done:** The MySQL database was launched in the background via Docker Compose.
**Command:** `docker-compose up -d mysql`
**Why:** The application requires a running MySQL database to store messages. The `-d` flag runs the container (defined in `docker-compose.yml`) in detached mode (in the background) so the terminal remains accessible to start the Flask app. *(Note: Docker Desktop must be running for this command to work on Windows).*

## 6. Starting the Flask Server
**What was done:** The Python web server was executed utilizing the virtual environment's executable.
**Command:** `.\venv\Scripts\python app.py`
**Why:** This runs the backend logic inside `app.py`. The server runs locally on host `0.0.0.0` at port `5000` (`http://127.0.0.1:5000`). Debug mode is intentionally left active in your codebase, which allows the server to hot-reload dynamically if you make further code changes.

---

### Verifying the Fix
Once both the database and the Flask server are running, any data you submit via the front end (`http://127.0.0.1:5000`) will now successfully be parsed by the Python backend via the `submit` route, inserted directly into the Dockerized MySQL database, and permanently stored.
