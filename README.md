# Tourism Track Recommender System using Geotagged Social Media Data
### The tools used are pgAdmin for Postgresql and PostGIS

### Description of the Use Case
In this project, we will develop a recommender system that will propose or recommend tourist 
walks for people visiting Northern Territory. The project case study is called a Tourism 
Track Recommender System. We will be using a variety of data like social media data, spatial data,
and reviews. 

#### Tourist Spots and Walks
These are sample constructed tourist walks locations in Australia.

##### Darwin
![alt text](https://github.com/KarlRetumban/Test/blob/main/Darwin.PNG)


##### Katherine
![alt text](https://github.com/KarlRetumban/Test/blob/main/Katherine.PNG)

##### Jabiru
![alt text](https://github.com/KarlRetumban/Test/blob/main/Jabiru.PNG)


The above constructed touristic walks are polygons we created to simulate a particular tourist spots, destinations or iteniraries. These can be replaced by other spots like malls, coffee shops, restaurant and other places where people mostly go.




## Recommend a touristic walk for user
Given the location of the user, we recommend the nearest touristic walk.

The current location of "Sydney Robbs" is at (132.2566860127223,-14.4567381247164642).

![alt text](https://github.com/KarlRetumban/Test/blob/main/user_location_quin.PNG)


Then we get the nearest recommended touristic walk using the code below.

~~~sql
SELECT gid, name, ST_Distance(ST_SetSRID(ST_MakePoint(132.2566860127223,-14.456738124716464), 4326),geom) AS distance, geom
FROM spatial.tracklog
ORDER BY ST_Distance(ST_SetSRID(ST_MakePoint(132.2566860127223,-14.4567381247164642), 4326),geom)
LIMIT 1
~~~

![alt text](https://github.com/KarlRetumban/Test/blob/main/user_recommended_quin.PNG)



### Recommend the same tourist spot to social connections

Using the code below, we can extract the social media connection of the user and recommend the same tourist walk for future itineraries.

~~~sql
--C. Retrieve friendship/social media links for a specific person 

SELECT user_id, user_name, user_id2 as userid_socialmedia_link, user_name2 as socialmedia_link, linktype
FROM socmed.socmed_link
WHERE user_name = 'Sydney Robbs'
UNION
SELECT user_id2, user_name2, user_id as userid_socialmedia_link, user_name  as socialmedia_link, linktype
FROM socmed.socmed_link
WHERE user_name2 = 'Sydney Robbs'

~~~

Below are the social media conneections we extraced.

![alt text](https://github.com/KarlRetumban/Test/blob/main/userlinks_recommended.PNG)

_____________

#### Code for creating Polygons

~~~sql

--Create new schema
CREATE SCHEMA spatial
 AUTHORIZATION postgres;

--Create new spatial table for tracklog polygons
DROP TABLE IF EXISTS spatial.tracklog;
CREATE TABLE spatial.tracklog(
 gid serial,
 name character varying(100),
 PRIMARY KEY (gid));
SELECT AddGeometryColumn('spatial','tracklog','geom',4326,'POLYGON',2);

--Insert data for tracklogs
INSERT INTO spatial.tracklog (name, geom)
VALUES ('Katherine Farm Walk',ST_GeomFromText('POLYGON((
					132.20990961104252 -14.494677614903566,		
					132.1976341815744 -14.50061989215034,		
					132.19297798418992 -14.507996290436783,		
					132.191073176169 -14.51537244299242,		
					132.19721089090308 -14.518855540693,		
					132.20673493100767 -14.517626218353492,		
					132.21731719779055 -14.513733319221895,		
					132.22726452856648 -14.509225666263795,		
					132.2291693365874 -14.501439504091243,		
					132.22514807520992 -14.489145006656651,		
					132.2171055524549  -14.485046689313315,		
					132.20969796570688 -14.487505688808564,		
					132.2020787336232 -14.489964661035595,		
					132.20990961104252 -14.494677614903566))',4326)),
					
		('Darwin City Stroll',ST_GeomFromText('POLYGON((				  
					130.8371570588316 -12.452050507236658,
					130.8397787165021 -12.453656769216735,
					130.84383971563875 -12.452753248076545,
					130.84620434804745 -12.452652856644521,
					130.84671839857106 -12.455413606870385,
					130.84769509456595 -12.459579774310022,
					130.84964848655574 -12.463444713168531,
					130.8494428663463 -12.464749744445655,
					130.84522765205256 -12.46831345023022,
					130.8411152478635 -12.465904189713676,
					130.83725986893631 -12.462742001297988,
					130.8353578819989 -12.460985213288318,
					130.83402135063744 -12.458274717009486,
					130.8371570588316 -12.452050507236658))',4326)),
										
		('Jabiru Park Walk',ST_GeomFromText('POLYGON((				  
					132.8322140191649 -12.654624064057252,
					132.84193204761544 -12.652555264022999,
					132.85182676749235 -12.652382863263156,
					132.85765758456265 -12.659451198908918,
					132.85642074457806 -12.66531259699281,
					132.85518390459345 -12.670311918292175,
					132.8498831618022 -12.671346248387534,
					132.84917639609674 -12.675655911926306,
					132.847409481833 -12.675828296951538,
					132.8467027161275 -12.68134455615783,
					132.83928167621983 -12.685136915044797,
					132.82443959640446 -12.68841208861648,
					132.81330803654296 -12.682034080157155,
					132.8322140191649 -12.654624064057252))',4326))
									;
~~~


