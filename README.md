# Mimic Database installation with Docker

What we are going to do here is to break down every steps required for setting up a working environment. 

The goal is:
* To ease your life so you don't lose too much type trying to figure out non-important stuff (non-related to our main goal)
* To have a common environment for all team members, so communication and workflow can spread more easily.

The database will be run inside a Docker container. You'll have access to this container through ports mapping.

If you don't know anything about Docker, you may want to start by following the quick [Get Started](https://docs.docker.com/get-started/) tutorial. Moreover, [this blog post](https://blog.docker.com/2016/03/containers-are-not-vms/) should explain why a Docker container is useful, and what are the differences between a container and a classic virtual machine.

Starting from now, we assume that [Docker is installed](https://docs.docker.com/install/) on your machine, as well as [docker-compose](https://docs.docker.com/compose/install/), a program we will use to build the Docker container.

## 1. Clone the Github repository on your machine

    $ git clone git@github.com:edouardtheron/diafoirus-mimic.git
    $ cd diafoirus-mimic
    
## 2. Get the data
Now, move to the directory we'll use for storing the data
    
    $ cd mimic_data/csv
    
`NOTE` For the following command, your access to the database must first have been granted by **Physionet.org**.

**Warning:** replace `{EMAIL}` with the email you used to log in physionet.org.

    $ wget --user {EMAIL} --ask-password -P mimic_data -A csv.gz -m -p -E -k -K -np -nd https://physionet.org/works/MIMICIIIClinicalDatabase/files/
    
*You will be prompted to enter your physionet.org password.*

This will download all the `csv.gz` (compressed CSV) files that composed the database. The compressed CSVs are about 6 GB in total, so it may take a while!

---

If the automatic dowloads with `wget` fails on the very big files, you can [download each of them manually here](https://physionet.org/works/MIMICIIIClinicalDatabase/files/) `right clic -> save target` (access restricted to granted users only, of course)

Then go back to the root of our directory

    $ cd ../..
    
## 3. Build the image and run the container

This is the magic command :)

    $ sudo docker-compose up --build

**This can take a while**, since it has to copy all the data to the container's system. 

When it's done, the container should be up and running with a seeded postgres on port `5432`. You're now free to connect to it.

If you want to connect to the database through the terminal keep reading, otherwise **congratulations, you are all set!**

`NOTE` You can stop the container using `CTRL-C` 
## 4. Restart the container
When you reboot your machine and you want to relauch the container, you can use the `docker-compose` command we used earlier, but add the `-d` flag to run the container as *daemon* (= as a background task)

    $ sudo docker-compose up --build -d

----------------
The following steps are optionnal, or are just provided as complementary observations.

### Log into the container's shell

    $ sudo docker exec -it mimic /bin/bash
    
You should have a shell access to the container. 

`NOTE` You are now executing command **inside** the container, and not on your local machine anymore. You can exit the container with `CTRL-C`.
    
### Connect to postgres

    $ psql -U mimic
    
### Run SQL queries
To list all the tables within the database

    $ \d 
    
If you don't see anything returning from this command, there is a problem :)

Otherwise, congratulations! You can start querying the database, for example...
  
    $ SELECT row_id FROM admissions limit10;

... should return a list of the first 10 indexes of the `admissions` table.

`NOTE` don't forget the `;` at the end of any SQL queries...
