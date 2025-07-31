# Deploy-Django-Application-on-Cpanel-Share-Hosting

## Context
- [Step-01: Prepare Django Project](#step-01-prepare-django-project)
- [Step-02: Logging Into the Cpanel](#-step-02-logged-in-into-the-cpanel-dashboard)
- [Step-03: Create Subdomain](#-step-03--create-sub-domain-if-needed)
- [Step-04: Setup Application](#-step-04--setup-application)
- [Step-05: Setup MySQL Database](#-step-05--setup-mysql-database-if-you-want)
- [Step-06: Upload Project Files](#-step-06--upload-project-files)
- [Step-07: Final Setup](#-step-07--final-setup)
    - [If You Have Terminal Access](#if-you-have-terminal)
    - [If You Don't Have Terminal Access](#if-you-dont-have-terminal-access)

## ✅ Step-01: Prepare Django Project

### 🔹Modify `settings.py`

- Make sure `DEBUG = False` in `settings.py`
- Set `APPOWED_HOSTS` to your domain or used `*` to accept any domain
    
    ```python
    ALLOWED_HOSTS = ["yourdomain.com"] # you can used multiple domain or subdomain
    
    # or
    ALLOWED_HOSTS = ["*"]
    ```
    
- Integrate`whitenoise`  inside the `INSTALLED_APPS and MIDDLEWARE` in `settings.py`:
    
    ```python
    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'whitenoise.runserver_nostatic', # This must be added before 'django.contrib.staticfiles'
        'django.contrib.staticfiles',
        'portfolioapp',
    ]
    ```
    
    ```python
    MIDDLEWARE = [
        'django.middleware.security.SecurityMiddleware',
        'django.contrib.sessions.middleware.SessionMiddleware',
        'whitenoise.middleware.WhiteNoiseMiddleware', # This must be added here after SecurityMiddleware & SessionMiddleware
        'django.middleware.common.CommonMiddleware',
        'django.middleware.csrf.CsrfViewMiddleware',
        'django.contrib.auth.middleware.AuthenticationMiddleware',
        'django.contrib.messages.middleware.MessageMiddleware',
        'django.middleware.clickjacking.XFrameOptionsMiddleware',
    ]
    ```
    
- Set `Static & Media Files` root in `settings.py`
    
    ```python
    STATIC_URL = 'static/'
    MEDIA_URL = '/media/'
    STATIC_ROOT = BASE_DIR / 'staticfiles/'
    MEDIA_ROOT = BASE_DIR / 'media/'
    STATICFILES_STORAGE = 'whitenoise.storage.CompressedStaticFilesStorage'
    ```
    
- Final Overview of the `settings.py`
    
    ```python
    DEBUG = False
    ALLOWED_HOSTS = ["yourdomain.com"]
    
    INSTALLED_APPS = [
        'django.contrib.admin',
        'django.contrib.auth',
        'django.contrib.contenttypes',
        'django.contrib.sessions',
        'django.contrib.messages',
        'whitenoise.runserver_nostatic', # This must be added before 'django.contrib.staticfiles'
        'django.contrib.staticfiles',
        'portfolioapp',
    ]
    
    MIDDLEWARE = [
        'django.middleware.security.SecurityMiddleware',
        'django.contrib.sessions.middleware.SessionMiddleware',
        'whitenoise.middleware.WhiteNoiseMiddleware', # This must be added here after SecurityMiddleware & SessionMiddleware
        'django.middleware.common.CommonMiddleware',
        'django.middleware.csrf.CsrfViewMiddleware',
        'django.contrib.auth.middleware.AuthenticationMiddleware',
        'django.contrib.messages.middleware.MessageMiddleware',
        'django.middleware.clickjacking.XFrameOptionsMiddleware',
    ]
    
    STATIC_URL = 'static/'
    MEDIA_URL = '/media/'
    STATIC_ROOT = BASE_DIR / 'staticfiles/'
    MEDIA_ROOT = BASE_DIR / 'media/'
    STATICFILES_STORAGE = 'whitenoise.storage.CompressedStaticFilesStorage'
    ```
    

### 🔹Modify `urls.py`

- Modify main project directory `urls.py` file.
    
    ```python
    from django.contrib import admin
    from django.conf import settings
    from django.conf.urls.static import static
    from django.views.static import serve
    from django.urls import path, include, re_path
    
    urlpatterns = [
        path('admin/', admin.site.urls),
        # Other paths...
    ]
    urlpatterns+=re_path(r'^static/(?P<path>.*)$', serve, {'document_root': settings.STATIC_ROOT}),
    urlpatterns+=re_path(r'^media/(?P<path>.*)$', serve, {'document_root': settings.MEDIA_ROOT}),
    ```
    

### 🔹Prepare `requirements.txt` file

- Add all required the packages and their version that you used in this project. Also must be add `gunicorn, whitenoise` packages for development. Otherwise some time you fetch lots of issue for static and media file after deployment.:
    
    ```python
    Django==5.2.3
    gunicorn==23.0.0
    pillow==10.3.0
    whitenoise==6.9.0
    ```
📒[Go To Context](#context)

## ✅ Step-02: Logged-in into the Cpanel Dashboard

## ✅ Step-03:  Create Sub-Domain (if needed):

- After login search `Domains`
![image.png](/docs-img/image-3.png)
- From the `Domains` Page click to the `Create A New Domain` button.
- Then create and submit

    ```python
    #Ex: test.yourmaindomain.com
    ```

📒[Go To Context](#context)
## ✅ Step-04:  Setup Application:

- Go to `Setup Python App` page
![image.png](/docs-img/image-4.png)
- Click `Create Application` button
- `Python Version:`  Select Python Version
- `Application root:`  Enter project directory name. Where you upload your project files.
- `Application URL:` Select the `Domain or Subdomain`
- Then hit the `Create` button.
![image.png](/docs-img/image-4.1.png)

📒[Go To Context](#context)

## ✅ Step-05:  Setup MySQL Database (If you want)

### 🔹Setup Database on the Cpanel

- Go to `Manage My Databases`
- On the Create New Database section set the Database name then hit the `Create Database` button
    
    ![image.png](/docs-img/image-5.1.png)
    
- Then scroll down and find `Add New User` section. Here set the database `username, password`
    
    ![image.png](/docs-img/image-5.2.png)
    
- Again scroll down and find `Add User To Database` section. Then select `User and Database`
    
    ![image.png](/docs-img/image-5.3.png)
    
- After Add it redirect to the `Manage User Privileges` from here select `ALL PRIVILEGES` then click `Make Changes` button.
    
    ![image.png](/docs-img/image-5.4.png)
    

`**🚨 Alert:**` Must be copy the Database Name, Username and Password. Because need to configure database on the settings.py

### 🔹Database Configuration

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'your_database_name',
        'USER': 'your_database_username',
        'PASSWORD': 'database_user_password',
        'HOST': 'yourhostdomain.com',
        'PORT': '3306',
    }
}
```

📒[Go To Context](#context)

## ✅ Step-06:  Upload Project Files

- Go to `File Manager` page
- Find the Application root directory.
- Upload the project file as `zip`
- Edit the `passenger_wsgi.py` and add below line and save it.
    
    ```python
    from yourProjectname.wsgi import application
    ```
    
    `**🚨 Note:**` Must change the `yourProjectname` based on your project name

📒[Go To Context](#context)

## ✅ Step-07:  Final Setup

- Go to `Setup Python App` page
- Select your app and click Edit Icon

### 🔹If You Have Terminal:

- From the application copy the `virtual environment` command
- Open the Terminal  and Paste the command
- Then run the below command:
- Install requirements file:
    
    ```bash
    pip install -r requirements.txt
    ```
    
- Migration and Migrate the database:
    
    ```bash
    python manage.py makemigrations
    ```
    
    ```bash
    python manage.py migrate
    ```
    
- Then Apply collect static command to collects all static files from apps and puts them into one central directory for easy serving in production:
    
    ```python
    python manage.py collectstatic
    ```

📒[Go To Context](#context)

### 🔹If You Don’t Have Terminal Access:

- From the Application Setup Page apply the command manually
- Write and Add `requirements.txt` into this section and click `Run Pip Install`. Note: Sometimes it send error message first try. If you fetch error then run again.

    ![image.png](/docs-img/image-7.1.png)

- Then into this section run other command and click `Run Script` button without `py or python`:

    ![image.png](/docs-img/image-7.2.png)

    After that click the `RESTART` button. Now you can visit your application.

📒[Go To Context](#context)