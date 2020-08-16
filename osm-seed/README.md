# osm-seed

Easy installation for the OpenStreetMap software stack.

# Requirements

You will need `docker` and `docker-compose` installed on your system

  - Installing `docker`: https://docs.docker.com/install/
  - Installing `docker-compose`: https://docs.docker.com/compose/install/

# Installation

## Installing osm-seed

Clone the osm-seed repo:

```
git clone https://github.com/developmentseed/osm-seed.git
cd osm-seed 
```

Copy env files:

```
cp .env.example .env
cp .env-tiler.example .env-tiler
```

In `.env` file, under `# Container: populate-dbapi`, add the url for `URL_FILE_TO_IMPORT` for downloading the pbf data to import into the db.
you can find files in the following link: [geofabrik](https://download.geofabrik.de/index.html)

For example, to import israel's data:
```
URL_FILE_TO_IMPORT=http://download.geofabrik.de/asia/israel-and-palestine-latest.osm.pbf
```
Comment everything under  `##Container osm-processor`
```
##Container osm-processor
# STORAGE
#URL_FILE_TO_PROCESS=https://s3.amazonaws.com/osmseed-staging/pbf/dc.pbf
#OSM_FILE_ACTION=simple_pbf
```

Build and run the app using docker-compose: 

```
docker-compose build
docker-compose up
```

Once `docker-compose` is running, you should be able to access a local instance of the OpenStreetMap website on `http://localhost`

NOTE that downloading the data and populating the db is a process that runs behind the scenes, so not all the data should appear right away (it can take up to 20 minutes for israel).

## Setup pgAdmin4
pgAdmin4 is a web based UI for managing postgreSQL.

1. Create a pgAdmin4 container: 

```
docker run -p 5000:80 --network="osm-seed_default" -e 'PGADMIN_DEFAULT_EMAIL=$EMAIL' -e 'PGADMIN_DEFAULT_PASSWORD=$PASSWORD' -d dpage/pgadmin4
```

Now you should be able to access a local instance of the pgAdmin4 website on `http://localhost:5000`

2. Log in with the email and password from the previous command.

3. Right click on servers at the top left corner and click on create :arrow_right:  new server.

4. fill the form with the following information.

> * NAME: openstreetmap
> * HOST: db
> * PORT: 5432
> * USERNAME: postgres
> * PASSWORD: 1234

**Note**: If you changed the default values in the .env file, change the values above accordingly.

## Create OSM user

1. Go to the OSM website on your local machine `http://localhost`.
2. click `sign up` button on top right of the window.
3. Follow the instructions and create a new user.

At the end of the process you should be redirected to `http://localhost/user/save` and see Application error on screen.

4. On pgAdmin, Click on databases :arrow_right: openstreetmap :arrow_right: schemas :arrow_right: public :arrow_right: tables, and find the USERS table.

5. right click the USERS table and click View/Edit data :arrow_right: All Rows.

6. Find your user at the bottom table, double click the status column, and change the text to `active`.

7. Click execute (or press F6).

8. Go back to your openstreetmap application log in with your activated user.


## Configure ID editor

1. Click on your user name on top right of the website and click `My Settings`.

2. Under the header `My Settings` click `oauth settings`.

3. Click `Register your application` and fill the form with the following information:

> * Name: WHATEVERYOUWANT
> * Main Application URL: http://localhost

4. Check all the boxes and click Register.

5. Copy the `Consumer Key` from the screen.

6. Open `application.yml` on directory: `osm-seed/openstreetmap-website/config/application.yml`

7. Make sure the value of the `default_editor` field is `id`.
```yaml
# Default editor
default_editor: "id"
```

8. Uncomment the line `id_key` below an paste the `Consumer Key` as its value.
```yaml
# OAuth consumer key for iD
id_key: "YOUR_CONSUMER_KEY"
```
9. save!

10. Build and run docker-compose for the second time:

```
docker-compose build
docker-compose up
```

### Done. now you can edit in openstreetmap :sunglasses: 
