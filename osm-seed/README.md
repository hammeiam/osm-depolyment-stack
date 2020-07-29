# osm-seed

Easily installation for the OpenStreetMap software stack.

# Requirements

You will need `docker` and `docker-compose` installed on your system

  - Installing `docker`: https://docs.docker.com/install/
  - Installing `docker-compose`: https://docs.docker.com/compose/install/

# Installation

## Installing osm-seed

Clone osm-seed repo:

```
git clone https://github.com/developmentseed/osm-seed.git
cd osm-seed 
```

Copy env files:

```
cp .env.example .env
cp .env-tiler.example .env-tiler
```

In `.env` file, under `# Container: populate-dbapi`, add a url for `URL_FILE_TO_IMPORT` for downloadnig the data

For example, israel data:
```
URL_FILE_TO_IMPORT=http://download.geofabrik.de/asia/israel-and-palestine-latest.osm.pbf
```
Comment everything under  `##Container osm-processor`

Build and run docker-compose: 

```
docker-compose build
docker-compose up
```

Once `docker-compose` is running, you should be able to access a local instance of the OpenStreetMap website on `http://localhost:80`

NOTE that downloading israel data is a process that run behind the scenes, so not all the data should appear right away (its take almost 20 minutes)

## Setup pgAdmin4

Create a pgAdmin4 containr: 

```
docker run -p 5000:80 -e 'PGADMIN_DEFAULT_EMAIL=$EMAIL' -e 'PGADMIN_DEFAULT_PASSWORD=$PASSWORD' -d dpage/pgadmin4
```

Now you should be able to access a local instance of the pgAdmin4 website on `http://localhost:5000`

Log in with your email and password

## Create osm user

Go to the osm website on your local machine `http://localhost:80`

click `sign up` on top right of the window. Follow the instractions and create a new user. At the end of the process you should redirect to `http://localhost/user/save` and see Application error on screen.

Go to your local pgAdmine instance on `http://localhost:5000` and create a new server with the following information:

* NAME: openstreetmap
* HOST: your postgres container ip.
* PORT: 5432
* USERNAME: postgres
* PASSWORD: 1234 

Click on openstreetmap database and find USERS table

Find your user and change the status column to `active`

Go back to your openstreetmap application log in with your activated user.

## Configure ID editor

Click on your user name on top right of the website and click `My Settings`

Under the header `My Settings` click `oauth settings`

Click `Register your application` and fill the form with the following information:

* Name: WHATEVERYOUWANT
* Main Application URL: http://localhost

Check all the boxes and click Register.

Copy the `Consumer Key` from the screen and open `application.yml` on directory: `osm-seed/openstreetmap-website/config/application.yml`

Go to `default_editor` and make sure the value is "id"
Uncomment the line `id_key` below an paste the `Consumer Key` to its value.

save!

Build and run docker-compose for the second time:

```
docker-compose build
docker-compose up
```

### Done. now you can edit in openstreetmap!