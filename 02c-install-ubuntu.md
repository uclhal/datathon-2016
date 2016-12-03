---
title: "Workshop 01: Linux setup and installation"
author: "Tom Pollard & Alistair Johnson"
date: "September 9, 2016"
output:
  html_document:
    fig_caption: no
    fig_height: 7
    fig_width: 10
---

## Install PostgreSQL

This comes standard on most flavours of Ubuntu - you should be using either 14.04 LTS or 16.04 LTS, which both come with PostgreSQL out of the box.
Check the version with `psql --version`. You should get something like `psql (PostgreSQL) 9.5.4`.

## Configure PostgreSQL

In order to use postgres, we will need to allow ourselves to log in using a password. Postgres controls these permissions in a plain text file called "pg_hba.conf". Let's open it up: `sudo nano /etc/postgresql/9.5/main/pg_hba.conf` - ** Note you may have PostgreSQL 9.4 instead, so double check the path.

Scroll down to the bottom, until you see:

```
# DO NOT DISABLE!
# If you change this first entry you will need to make sure that the
# database superuser can access the database using some other method.
# Noninteractive access to all databases is required during automatic
# maintenance (custom daily cronjobs, replication, and similar tasks).
#
# Database administrative login by Unix domain socket
local   all             postgres                                peer
```

Add in under that row:

```
local   all             postgres                                password
```

You'll then need to restart the server:

```
sudo /etc/init.d/postgresql restart
```

<!--
## Install Valentina

While we have a database engine installed (PostgreSQL), we need a front end user interface to navigate it. I prefer Valentina Studio though there are other options. There is a guide for installing the "Server" and the "Studio", you only need the studio so you can skip half way through the video: http://valentina-db.com/docs/dokuwiki/v6/doku.php?id=valentina:video:vs:vs

If you prefer not to watch the video, it's pretty straightforward to do. First download Valentina:

http://valentina-db.com/en/studio/download/current
Look for "Velentina Studio Linux 64 DEB"

Either run the file from the folder you downloaded it to, or run from terminal: `sudo dpkg -i vstudio_x64_6_lin.deb`

Test that it worked! Look for "VStudio" in your software explorer.

You will need to fill in a form the first time you start the program as they e-mail (free) serials that are required to register the software.

-->

We'll also need to install pgAdmin3:

```
sudo apt-get install pgadmin3
```

## Install MIMIC on PostgreSQL

We will use a mini, demo version of MIMIC for the purposes of this installation workshop. While publicly accessible, the MIMIC-III demo is still considered sensitive data and you must agree to a limited data use agreement prior to downloading the data. This requires registering for a PhysioNetWorks account here: https://physionet.org/users/login.shtml

Once you have done that, follow the instructions on this page to download `mimiciii_demo-postgres_9_5.backup`: https://physionet.org/works/MIMICIIIClinicalDatabaseDemo/

We will restore the data from backup using pgAdmin3 - a graphical user interface which allows access to MIMIC-III. Launch pgAdmin3 either from the Software center or from the terminal (`pgadmin3`). You should see a window like this:

![PgAdmin3](./img/pgadmin.png)

Click the icon that looks like an electrical plug (top left) - this will open a screen to connect to a new server. Fill in the following details, replacing <USERNAME> with your account username:

![Be sure to replace <USERNAME> with your account username](./img/pgadmin_settings.png)

You should successfully connect to the database. If you look at the bottom of the window, there is a small amount of text which says "Connecting to the database... Done."

![Note the "Connecting to the database... Done" at the bottom](./img/pgadmin_connected.png)

Expand the server list by clicking the sideways arrow next to "Server Groups", then the arrow next to "mimic". Right click "Databases" -> "New Database...."

![Right click and select "New Database..."](./img/pgadmin_newdb.png)

Fill in the details as below using your username instead of <USERNAME>:

![Be sure to replace <USERNAME> with your account username](./img/pgadmin_dbsettings_blank.png)

For example, my username was `alistairewj`, so I've filled that in here:

![Example settings with username alistairewj](./img/pgadmin_dbsettings.png)

Click OK to create the database. You should see it appear now with a red "x" over the icon. Now, right click "mimiciii", and click "Restore..."

![Click "Restore..."](./img/pgadmin_restore.png)

Click the three dots and navigate to the `mimiciii_demo-postgres_9_5.backup` file you've downloaded, usually in your Downloads folder. Here's an example of what mine looks like (my username is `alistairewj`, yours will differ):

![Example settings for restoring the data](./img/pgadmin_restore_settings.png)

You don't have to worry about any of the options - just click "Restore". Once the restore has finished (the last line will read "Process terminated with exit code ..."), close out the dialog box. Right click mimic and click "Refresh". Then click the arrow next to "mimiciii", "Schemas", "mimiciii_demo", and finally "Tables". You should see a large number of tables such as "admissions", "callout", "caregivers", etc.

![Image of a successful installation of the MIMIC-III demo](./img/pgadmin_success.png)

If your screen looks like the above, you have successfully installed the MIMIC-III demo!


## (Optional) Install a graphical tool for exploring MIMIC-III

You have already used pgAdmin3 - this is a particular "front end" software for exploring the data. However, it is not the only one. You can try DBeaver as an alternative: http://dbeaver.jkiss.org/

Download the community version for Ubuntu/Debian and run:

```
sudo dpkg -i dbeaver-ce_3.7.3_amd64.deb
```

Note that both DBeaver and PgAdmin3 connect to the same underlying database, so if you imported the data under PgAdmin3, you can still access it via DBeaver.


## Install R and RStudio

R is an open source software tool for data analysis which is used among many statisticians and data scientists. We'll also want a graphical user interface to use with R, and for that purpose we'll use RStudio. To download all of these, run the following:

```
sudo apt-get install r-base r-base-dev
```

Next, we will install a graphical interface for R called RStudio. We'll download the 64-bit version - though also we need some dependencies.

```
wget https://download1.rstudio.org/rstudio-0.99.903-amd64.deb
sudo apt-get install libjpeg62 libgstreamer0.10-0 libgstreamer-plugins-base0.10-0
sudo dpkg -i rstudio-0.99.903-amd64.deb
```

Test that everything works by running RStudio. You should end up with a screen similar to the following:

![Example of RStudio](./img/R.png)

If you see something similar, you are in great shape. Now we will install various packages for R which we'll need for the course - these packages allow you to run useful analyses such as regressions, diagnostic tests, etc, all of which we will use later in the course. With RStudio open, you should see one of the sub-windows is called the "Console" - we'll be typing commands into this. Type the following:

![Example of RStudio](./img/r_install_package.png)

```R
install.packages("knitr")
```

The first time your un this command, you may be asked what mirror you would like to use. Most of them are fine, but **do not pick an https (secure) mirror**. This can bug out and there is little advantage to downloading these packages securely anyway.

If the above worked, you should be greeted with the following message:

```
The downloaded source packages are in
        <path>
```

If so, great! Install the remaining packages by typing each line into the console, one line at a time:

```R
install.packages("RPostgreSQL")
install.packages("tableone")
install.packages("Hmisc")
install.packages("dplyr")
install.packages("devtools")
install.packages("caret")
install.packages("rmarkdown")
install.packages("knitr")
install.packages("sjPlot")
```

## (Optional) Install a text editor

For the first few workshops, it will be convenient to have a text editor as we will not be using RStudio. We recommend using Atom as your text editor as it's free, open source, and integrates well with Git. You can download Atom here: https://atom.io/

Other perfectly suitable options include:

* Notepad++: https://notepad-plus-plus.org
* Sublime Text: https://www.sublimetext.com

## (Optional) Install Git

We may use Git to download some software and scripts that we need. Git is a version control system which facilitates collaboration in developing projects.

```
sudo apt-get install git
```

## (Optional) Create GitHub account

GitHub is an online platform for hosting Git repositories with various useful extra features. Much of the code used throughout this course will be mirrored on GitHub. Create an account on GitHub now: https://github.com/
