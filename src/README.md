# Mergington High School Activities API

A super simple FastAPI application that allows students to view and sign up for extracurricular activities.

## Features

- View all available extracurricular activities
- Sign up for activities

## Getting Started

1. Install the dependencies:

   ```
   pip install -r ../requirements.txt
   ```

2. Run the application:

   ```
   SESSION_SECRET="replace-with-a-long-random-value" \
   STAFF_USERNAME="staff" \
   STAFF_PASSWORD_HASH="pbkdf2_sha256$600000$unique-salt$base64-derived-hash" \
   uvicorn app:app --reload
   ```

3. Open your browser and go to:
   - API documentation: http://localhost:8000/docs
   - Alternative documentation: http://localhost:8000/redoc

## API Endpoints

| Method | Endpoint                                                          | Description                                                         |
| ------ | ----------------------------------------------------------------- | ------------------------------------------------------------------- |
| GET    | `/activities`                                                     | Get all activities with their details and current participant count |
| POST   | `/activities/{activity_name}/signup?email=student@mergington.edu` | Sign up for an activity                                             |
| DELETE | `/activities/{activity_name}/unregister?email=student@mergington.edu` | Unregister a student (staff only)                              |
| POST   | `/auth/login` | Start a staff or administrator session                               |
| POST   | `/auth/logout` | End the current session                                              |

## Staff Authentication

Viewing activities and participants is public. Registering or unregistering a student requires a staff or administrator session. Configure credentials outside the repository with these environment variables:

- `SESSION_SECRET`: a long random value used to sign session cookies. Set `SECURE_COOKIES=true` when serving over HTTPS.
- `STAFF_USERNAME`: the staff account name.
- `STAFF_PASSWORD_HASH`: a PBKDF2-SHA256 hash in the format `pbkdf2_sha256$iterations$salt$base64-derived-hash`.
- `STAFF_ROLE`: either `staff` (default) or `administrator`.

Generate a password hash without writing the password to source files:

```bash
python -c 'import base64, hashlib, os; password = input("Password: ").encode(); salt = os.urandom(16).hex(); digest = hashlib.pbkdf2_hmac("sha256", password, salt.encode(), 600000); print(f"pbkdf2_sha256$600000${salt}${base64.b64encode(digest).decode()}")'
```

## Data Model

The application uses a simple data model with meaningful identifiers:

1. **Activities** - Uses activity name as identifier:

   - Description
   - Schedule
   - Maximum number of participants allowed
   - List of student emails who are signed up

2. **Students** - Uses email as identifier:
   - Name
   - Grade level

All data is stored in memory, which means data will be reset when the server restarts.
