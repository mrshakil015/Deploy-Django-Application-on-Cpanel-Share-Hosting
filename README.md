# Deploy-Django-Application-on-Cpanel-Share-Hosting

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
    STATIC_URL = '/static/'
    STATIC_ROOT = os.path.join(BASE_DIR, 'static')
    MEDIA_URL = '/media/'
    MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
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
    
    STATIC_URL = '/static/'
    STATIC_ROOT = os.path.join(BASE_DIR, 'static')
    MEDIA_URL = '/media/'
    MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
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
    

## ✅ Step-02: Logged-in into the Cpanel Dashboard

## ✅ Step-03:  Create Sub-Domain (if needed):

- After login search `Domains`
- From the `Domains` Page click to the `Create A New Domain` button.
- Then create and submit

```python
#Ex: test.yourmaindomain.com
```

## ✅ Step-04:  Setup Application:

- Go to `Setup Python App` page
- Click `Create Application` button
- `Python Version:`  Select Python Version
- `Application root:`  Enter project directory name. Where you upload your project files.
- `Application URL:` Select the `Domain or Subdomain`
- Then hit the `Create` button.

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
    

`Alert:` Must be copy the Database Name, Username and Password. Because need to configure database on the settings.py

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

## ✅ Step-06:  Upload Project Files

- Go to `File Manager` page
- Find the Application root directory.
- Upload the project file as `zip`
- Edit the `passenger_wsgi.py` and add below line and save it.
    
    ```python
    from yourProjectname.wsgi import application
    ```
    
    `Note:` Must be change the `yourProjectname` based on your project name