# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

TaskMaster is a personal task management application with a Tkinter desktop GUI and Flask-based REST API. The codebase has significant architectural issues including duplicated implementations, hardcoded credentials, and mixed concerns that require careful refactoring.

## Architecture

The application has two parallel implementations that need consolidation:

```
main.py          # Primary entry point with both GUI and API mixed together
├── Task class    # Data model
├── TaskGUI       # Tkinter interface (embedded)
├── Flask routes  # API endpoints (embedded, /api/*)
└── backup_database()  # Backup utility

task_gui.py      # Alternative GUI implementation (TaskManagerGUI)
api_server.py    # Alternative API implementation (different routes)
database.py      # DatabaseManager class (unused in main.py)
config.py        # Configuration with hardcoded secrets
utils.py         # Utility functions
```

### Key Issues
- **main.py** mixes GUI, API, and business logic in a single file
- **config.py** contains hardcoded credentials (admin/password123, API keys)
- **api_server.py** has different routes and port (8080 vs 5000) than main.py
- **task_gui.py** has its own database connection logic, bypassing main.py
- No separation between data access, business logic, and presentation layers

## Commands

```bash
# Install dependencies
pip install -r requirements.txt

# Run desktop GUI (uses main.py implementation)
python main.py gui

# Run REST API server (uses main.py implementation)
python main.py api

# Database backup
python main.py backup

# Run tests
python -m pytest test.py -v

# Check database structure
python check_db.py
```

## Coding Style Questions

1. **Why is main.py both GUI and API?** The original design placed everything in one file for simplicity, but this violates separation of concerns. The API routes use `/api/*` prefix while api_server.py uses root `/tasks`.

2. **Inconsistent naming:** The Task class uses `desc` for description while database columns use `description`. Spacing around operators is inconsistent.

3. **Error handling:** Most functions catch exceptions but only print errors rather than raising or logging properly.

4. **Database connections:** Multiple files create their own SQLite connections instead of sharing a connection pool from database.py.

5. **Global state:** `db_connection`, `current_user`, and `DEBUG_MODE` are global variables that reduce testability.

## Dependency Modules

| Module | Purpose | Version |
|--------|---------|---------|
| Flask | Web framework for API | 3.0.0 |
| tkinter | Desktop GUI (standard library) | - |
| sqlite3 | Database (standard library) | - |
| requests | HTTP client | (latest) |
| pytest | Testing framework | 6.0.0 |

**Note:** requirements.txt includes unused dependencies (numpy, pandas, matplotlib, beautifulsoup4) that should be removed.

## Execution Environment

- **Python:** 3.10+ (as specified in README)
- **Database:** SQLite (local file `tasks.db`)
- **GUI:** Tkinter (bundled with Python)
- **OS:** Cross-platform (developed on macOS)

**Important:** After installing dependencies, initialize the database:
```python
python main.py gui  # or `api` - both call connect_db() on startup
```

## Framework Questions

1. **Flask API routes conflict:** Running `python main.py api` starts Flask on port 5000 with `/api/tasks` routes, while `python api_server.py` uses port 8080 with `/tasks` routes. They will conflict if both run simultaneously.

2. **Tkinter threading:** The GUI runs on the main thread. If API is also needed, consider running Flask in a separate thread:
   ```python
   # Current architecture doesn't support simultaneous GUI + API
   ```

3. **Database initialization:** The `tasks` table schema differs between implementations. Some use `status` column, others use `operation`. Use `check_db.py` to verify current schema.

## Common Development Tasks

**Add a new task field:**
1. Update `CREATE TABLE` in both main.py (line 24-26) and database.py (line 19-27)
2. Update Task class `__init__` in main.py
3. Update add_task function parameters
4. Update GUI input forms

**Fix the duplicated GUI:**
- main.py has TaskGUI with listbox-based interface
- task_gui.py has TaskManagerGUI with treeview-based interface
- Consolidation needed before new features

**Security issues to address:**
- Remove hardcoded credentials from config.py (lines 3, 9-10, 15)
- Remove hardcoded API_KEY from main.py (line 18)
- Use environment variables instead