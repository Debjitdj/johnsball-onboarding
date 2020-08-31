# Johnsball Onboarding
Info for next IT officer of St. John's Ball.

# Recommendation
I recommend learning about [Git](https://try.github.io/) and [Django](https://www.djangoproject.com/start/).
We are using heroku to host our website for free, and git for deployment and version control.
Django is a python based webapp framework, which is used to create the entire website.

## Requirements
First we need to have:
* A [Github](https://github.com/) account 
* Install [Python 3](https://www.python.org/downloads/) and pip3
* Install [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli)

## Steps
These steps are mainly for Linux and Windows. On Mac, some changes need to be done, which can be easily found online.

### (1) Install [VirtualEnv](https://docs.python.org/3/library/venv.html)
After installing python3, we need to install VirtualEnv. This is really important to keep the website deployment clean. Otherwise our local python packages will cause problems when we will try to update the website from our computer.

### (2) Get the website source code

Open a terminal and navigate to a folder where we want to keep the source code of the John's Ball website. Now follow these instructions.

First login to Heroku. Use this command and follow the instructions
```
$ heroku login
```
Then clone the repository and enter inside the source directory
```
$ heroku git:clone -a johnsball
$ cd johnsball
```
Now we have got a directory called `johnsball` which contains the source code of the john's ball website.

### (3) Create a VirtualEnv
This step is different for different operating systems. Sometimes it also depends on the OS versions. So I will suggest to check online how to do it. This is how it sould work for Ubuntu 16:
First make sure we have pip3 installed
```
$ sudo apt-get install python3-pip
```
Make sure virtualenv is installed
```
$ sudo pip3 install virtualenv 
```
**we are using `pip3` to make sure virtualenv is installed for python3. If we don't have python2 installed, it may not work. Then we have to use `pip` without that `3`**

Now crate a virtual environment called `venv` (If we change this name, make sure we add that to the .gitignore file, otherwise this entire folder will be added to the project, which may cause problems later). *Note: this variable is not a part of the project and should not be added. This is just to make our life easier*
Make sure we are inside the `johnsball` directory
```
$ virtualenv venv 
```

### (4) Start the VirtualEnv
start the virtualenv `venv`, that we just created
```
$ source venv/bin/activate
```
Now we will see `(venv)` at the beginning of each line on our terminal, which means python3 is using this virtualenv to separate the python packages for this project from our local pre-installed packages. This is really useful to make sure we are using the right version of each package, instead of using any pre-installed python packages.

### (5) Install python packages
This is a **one Time** step. We only have to install the packages once when we do it first time or if we want to use a different computer.
**Before doing this step, make sure our virtualenv is activated (previous step)**
Inside the `johnsball` directory, there is a `requirements.txt` file which contains a list of python packages needed for this project. **This is REALLY IMPORTANT that this file is maintained properly. If we need to use a new python package, make sure we add that to this file. THIS FILE IS USED BY THE SERVER TO DETERMINE WHICH PYTHON PACKAGES ARE NEEDED FOR THIS PROJECT**

To Install the packages, execute this command:
```
$ pip3 install -r requirements.txt
```
Now all these packages are installed under our virtualenv. So these packages will not be available if the virtualenv `venv` is not activated or deleted.
If the package installation fails for some reason, I will recommand to search the error message online or check on stackoverflow. If we still don't find anything, cantact me.
A common error is like this:

```
Error: pg_config executable not found.
    
    pg_config is required to build psycopg2 from source.  Please add the directory
    containing pg_config to the $PATH or specify the full executable path with the
    option:
    
        python setup.py build_ext --pg-config /path/to/pg_config build ...
    
    or with the pg_config option in 'setup.cfg'.

```
An easy fix will be to install `libpq-dev` by doing `$ sudo apt-get install libpq-dev`. For more information about this error, [check here](https://stackoverflow.com/questions/11618898/pg-config-executable-not-found).

### (6) Add local settings
Add this `local.py` file inside `/johnsball/johnsball/settings/` where production.py already exist. This local.py is added to the .gitignore so this file is not part of the project and **make sure is never goes to the server**.
production.py contains all the app settings for the live website, which is different from the local settings.


## Start the Web App
Finally we can start the web app server locally to see our changes before we actually make the changes live.
```
$ python3 manage.py runserver
```
**Just like `pip3`, we are using `python3` to avoid python version conflict**
This command will start a local server under port `8000`, which means our computer is working like a server for this web app. Don't close the terminal or don't press CONTROL-C  
Now open a browser(ideally Google Chrome) and go to http://127.0.0.1:8000/
**https will not work locally, so always use http to run the project locally**
Remember, this uses a local database, which is currently empty, so we are not gonna see anything on the committee page. We can create some new local entires by going to http://127.0.0.1:8000/admin, but to login, we need the super user password. For this project, I have already created a super user, we can change the password locally (as we are running the server locally). This user and password has **nothing to do with the live website**

### Change password of the Local Super User
Stop the server by pressing CONTROL-C and execute this command:
```
$ python3 manage.py changepassword admin
```
`admin` is the user name, and now we can set new password. Then start the local server again and go to http://127.0.0.1:8000/admin and login using username `admin` and the password we selected


## Updating the source code

There are many things on the website that we may want to modify, like:

### (1) Modify existing pages or add new pages

The templates for all the web pages are inside `johnsball/mainsite/templates/mainsite`. These templates uses some common segments from `johnsball/mainsite/templates/mainsite/includes`.

Templates are rendered by python methods from `johnsball/mainsite/views.py`. While rendering the template, we can pass some values as variables which can be used in the template to keep things dynamic.

The `johnsball/mainsite/url.py` defines the navigation of the main website, and we can declare which python method from `views.py` will be called when user naviagtes to a particular url.

### (2) Configure Zoho account 

Under `johnsball/johnsball/settings/`, there is `production.py` which contains all the settings for the live website. At the bottom of the file, the settings for the zoho service can be found. 

We have a `contact us` form on the website, which is using zoho to send the submitted form to `president@johnsball.com`. There is a `contact_us` python function in `johnsball/mainsite/views.py` which handles the form submissions.

### (3) Modify static files

This is going to be a bit confusing. But if we follow these instructions one by one, it shouldn't be that hard. Here, by static files, we mean javascript, CSS, images, GIFs etc files which don't normally need modifications once the website is live. Ideally these static files should be kept on a seperate sevrer with CDN service to serve these files to the user which improved the user experience a lot. But as we can't afford that, we are using `whitenoise` to host these static files on the same server where we keep the source code.

There are two main directories that we have to deal with, `johnsball/static` (which is added in the .gitignore so that it doesn't go to the server) and `johnsball/static_cdn` (which goes on the server and served the static files). The `static` folder works as a temporary folder that helps us to create compressed versions of the js and css files.

First time when we pull the entire source code locally, we will not see this `static` folder, as it's not part of the main project. We don't have to worry about it now.

In the `static_cdn` folder, we can see `CACHE`, which is just a compressed version of the JS and CSS files we have from the last deployment. `DELETE` this `CACHE` folder for now, we will create it later.

We can make all the modifications on the `static_cdn` folder we need. Then run the local server and test if all the changes are working properly. When we are done with changes, we have to prepare these static files for deployment.

we have to create this `johnsball/static` folder now, and copy all the content from `johnsball/static_cdn` to this new `johnsball/static` folder. So both `static` and `static_cdn` now have the same content. Now from the terminal stop the local server and run:
```
python3 manage.py compress
```
This will collect all the JS and CSS files from the `static` folder and compress them and create the `CACHE` folder inside `static_cdn` folder. We have to make sure this `static` folder is included in the .gitignore file, so that we accidentally don't push it to the server. Now the static files are ready for deplyment (deployment steps are described later). This `CACHE` folder serves the compressed js and css files, which helps our users to load our website faster. *If this CACHE folder is missing, the webpages will not find any js or css and the pages will look broken.*

### (4) Add entries for the committee page

To add/update a committe member details on the website, we need to go to `johnsball.com/admin` (Basically, `whatever.domain.name/admin`) and login with the credentials. Then click on `Roles` and we will see a list of existing roles that we can edit, or we can click on `ADD ROLE +` to add a new role. Currently we have the details of the 2020 committee, so if we are going to use the same website for next ball, then we can just change the information from these role entries and we will be good to go.

Two things which are not intuitive here:
* **Order:** this is the ordering of the committee member entries on the committee page. If we think one entry needs to be higher on the page, we can simply decrease the `order` value of that entry. These values don't need to be consecutive, they only need to be Integers. So, if one entry needs to be at the bottom of the page, we can give it an order `100`, which will be bigger than all other values. Similarly, negative numbers are also allowed. An entry with `-100` order will come before an entry of `0` order.
* **Photo:** This is just the name of the photo with the extension (eg: `Debjit.jpg`), which should be already present on the server. Under `/johnsball/static_cdn/media/committee/`, all the photos of the committee members should be there already (after all the photo editing and resizing). If we rename any of the photo there, we also have to update that on the website by going to `johnsball.com/admin`. If we replace a photo but keep the same name, the website will start showing the new photo. Updating these photos can be done the same way we update static files or contents of the website.

If we want to modify the way these entries are stored in the database, we have to modify `johnsball/mainsite/models.py` and run django migration. We can find it online easily how to do that.

## Updating the website

After we make any changes to the source code, we have to follow these steps to deploy the changes to the server (This will not affect the online database, but will affect the static files)
First add the changes to git
```
$ git add <file we have modified or added>
```
we can also use `$ git add .` under our root directory (which is the top level `johnsball` directory) to add all the changes to git, but this it more risky as we may add some unwanted files or changes

Now commit our changes:
```
$ git commit -m "A nice commit message that describes the changes we have made. Note that these quotes are needed"
```
Now we have to push these changes to our server.
Make sure we are logged into heroku on this terminal, then execute this:
```
$ git push heroku master
```
This may take some time, but when this is done, go to https://johnsball.herokuapp.com/ and we will see our changes there.