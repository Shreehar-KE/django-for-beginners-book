# Django Full Stack App deployment using Koyeb
## 1. Register for a Koyeb Account
- Sign Up using GitHub(use primary account, newly created account will be banned)
## 2. Create a PostgreSQL Database on Koyeb
- [Dashboard](https://app.koyeb.com/?service_type=database&step=serviceType) -> Create a Database service -> Service Config -> Create
## 3. Integrate database configuration settings
- Database Service -> Overview -> Connection Details -> Django -> Copy `DATABASES`
- Replace the default `DATABASES` config with Koyeb's in your project's `settings.py`
- `pip install psycopg2-binary` to install the postgres adapter
- `python manage.py migrate` to migrate the database
- `python manage.py createsuperuser` to create a superuser 
## 4. Installing important packages
- `pip install gunicorn`
- `pip install whitenoise`
## 5. Deployment preparation
- Define `STATICFILES_DIRS` to your *static* folder in your `settings.py`
  - `STATICFILES_DIRS = [BASE_DIR / 'static']`
- Define `STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')` in your `settings.py`
- Config [whitenoise](https://whitenoise.readthedocs.io/en/stable/#quickstart-for-django-apps) in your `settings.py`
  - Add the middleware
  - Add the `staticfiles` backend in `STORAGES` for caching & compression
- `pip freeze > requirements.txt`
- In your `settings.py`
  - set `DEBUG = False`
  - set `ALLOWED_HOSTS = ['*']` - temporary
## 6. Configure Environment variables
- Create an `.env` file in the same folder as the`settings.py` with values such as:
  - `SECRET_KEY`, DB credentials like `NAME`, `USER`, `PASSWORD`, `HOST`
  - use this format `variable-name=value`, no quotes required for values
  - If needed use `python3 -c "import secrets; print(secrets.token_urlsafe())"` in the terminal to generate a new key
- In `settings.py` use `os.environ.get('env-variable-name')` to access those values
## 7. Push your application to GitHub with Git
- Create a new GitHub repository for the project
- Create a `.gitignore` file in your project folder to exclude content like `.venv/` folder, `.env` file, etc
- Upload the project to the GitHub repository
## 8. Deploying to Koyeb
- [Dashboard](https://app.koyeb.com/services/new?service_type=web&step=serviceType) -> Choose GitHub -> Install GitHub app if not done already
  - Select Project Repository under Import Project
  - Choose Instance & Region
  - Under Review & Deploy
    - Builder -> Choose Buildpack -> Override Run command -> `gunicorn your-project-name.wsgi`, project-name is found in `settings.py` as `WSGI_APPLICATION`
    - Environment Variables -> Drag & Drop the `.env` file 
    - Deploy
- After Initial Deployment
  -  Copy the Public URL from the service overview
  -  Set `ALLOWED_HOSTS = ['unique-url.koyeb.app']`, no need for the http prefix
  -  Push the changes to GitHub repository & Re-deploy