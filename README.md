# Deploy-on-render this is the process to deploy your django website on render

## What is Render?
  Render is a cloud host, I chose it because it makes deploying and updating a web app very simple.

1. Download and Install Git : https://git-scm.com/downloads
2. Create an account on Github : https://github.com/
3. Open Terminal

4. Add local hosted code to Github (you can can check this article to do that - https://docs.github.com/en/get-started/importing-your-projects-to-github/importing-source-code-to-github/adding-locally-hosted-code-to-github)
   
5. Install gunicorn or waitress - This will be our production server as we can not use development server which we were using by runing python manage.py runserver. Waitress is meant to be a production-quality pure-Python WSGI server with very acceptable performance. For More: https://docs.pylonsproject.org/projects/waitress/en/latest/ 
     ```javascript
      pip install waitress
     ```
6. Run wsgi.py file using waitress to test everything works fine on Local Machine (Before Pushing to Render)
     ```javascript
      waitress-serve --port=8000 inner_project_folder_name.wsgi:application
     ```
7. You will get a link in terminal just open it. If everything works then you will be able to see your project running on Web Browser
8. If you get an error: Disallowed Host Invalid HTTP_HOST header then do below change in Django's Settings.py file and re-run wsgi.py file as Step 7
     ```python
      ALLOWED_HOSTS = ['*']
     ```       
9. If you have static files must include STATIC_ROOT in Django's settings.py file
    ```python
      STATIC_ROOT = BASE_DIR / "static"
    ```
10. Install whitenoise - WhiteNoise allows your web app to serve its own static files, making it a self-contained unit that can be deployed anywhere without relying on nginx, Amazon S3 or any other external service. (Especially useful on Heroku, OpenShift and other PaaS providers.) For More: http://whitenoise.evans.io/en/stable/
    ```javascript
      pip install whitenoise
    ```
11. Open Django's settings.py file and Add Whitenoise Middleware
    ```python
      MIDDLEWARE = [
        # 'django.middleware.security.SecurityMiddleware',
        'whitenoise.middleware.WhiteNoiseMiddleware',
        # ...
      ]
    ```
12. Bundle all requirements
    ```javascript
      pip freeze > requirements.txt
    ```

13. Run below command
    ```javascript
      git add .
      git commit -m "any comment"
      git push REMOTE-NAME BRANCH-NAME
    ```
 14. Getting Started on Render
      Go to https://render.com/ and hit Sign Up. Create a new account and go to the Dashboard. You should see a few options - click on New Web Service.
      ![render_screeny](https://user-images.githubusercontent.com/100860551/209301297-daac7ea6-bb7d-4ff4-bda3-192681593708.jpg)
      
 15. Render will ask you to connect with your Git provider of choice, if you haven't already. Once that is done, pick out the repo that you have been working
     ![render-pick-repo-1024x151](https://user-images.githubusercontent.com/100860551/209303091-ac384a74-fa05-4291-b895-af23187e2deb.png)

 16. Now, you are being taken to a configuration menu. In this menu, we are declaring how the render server should initialize the application, when a change is made to the git repository. Important is that we tell Render to install all of our dependencies that we stored in the requirements.txt file we created earlier and start the application with Waitress.
    ![render-django-setup-1024x486](https://user-images.githubusercontent.com/100860551/209303241-0bd5c765-65b3-425e-92e6-650c836e93b9.png)
 
 17. Choose the cheapest plan. Then, hit the build button and watch how render goes through the process of installing the app. You'll notice that after the build process, Render created a subdomain for us where we can view the application.This is where we run into a problem:
     ![disallowed_host-1024x69](https://user-images.githubusercontent.com/100860551/209303545-564495a1-5e06-4522-96ce-2d1cfd286724.jpg)
 18. If you are seeing this error, there are two problems:

      You didn't add the Render domain to your allowed hosts (obviously) <BR />
      You have debug mode enabled in production <BR />
      This is fixed easily by going to back to your settings.py and making the following changes: 
```javascript
      DEBUG = False
      ALLOWED_HOSTS = ['render_app_name.onrender.com', 'localhost']
```
19. Now, push this new version via Git and notice how Render automatically updates the server!  <BR /> You can also use a manual deploy if that does not work.
    ![render-app-running-1024x450](https://user-images.githubusercontent.com/100860551/209305090-866113a4-8f2e-4056-98c8-776102075a73.png)

20. Voila! The app is running. I hope you enjoyed this quick introduction to deployments with Django and Render. Obviously, there are still quite a few ways we can improve upon this, like using environment variables to dynamically toggle Debug mode in our settings.py based on whether we are in production mode or not. 


