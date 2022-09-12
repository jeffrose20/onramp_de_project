# Onramp Data Engineering Take Home Project

## Overview
Congratulations for making it this far in the interview process for the Vanguard Apprenticeship at Onramp!

This project seeks to better inform the the Onramp team of your Data Engineering skill set - specifically in regards to data ingestion, transformation, 
storage, and analytics.  It will also help prepare you for your interview with Vanguard by mimicking a Data Engineering use case.  You will have until <due_date> 
to complete this project.  The expected duration for this project is 30 hours (this time could vary based on your experience level, but this project 
should be a significant effort for all candidates).  If you are lacking in certain skills required, it may take you longer so please plan accordingly.

### Description
You will be tasked with ingestion, transformation, storage, and analytics of Spotify data.  You will pick 20 of your favorite musicians / artists
and pull data using Python from the Spotify API for them including artist info, albums, and songs.  You will be storing this data in a SQLite
database on your local machine.  Once the data is stored into tables, you will create views in order to join the data together and make it useful for analytics.
You will create SQLite views that will display some basic analytics such as top songs by artist, top songs by album, top albums by artist, etc.  We are asking you to 
organize the data as if you were preparing it to be used by members a Data Science / Analytics team.  Try to imagine how you can make the data and code clear, 
easy to understand, and accessible for others.

Tools Used: Python, SQLite3, Spotipy, Pandas

## Project Requirements

### Ingestion
You will need to pull Spotify data for 20 of your favorite artists.  For each artist you choose, you will also pull data for their albums.  For the albums
you pull down, also get the data for the songs in those albums.  To give a ballpark of how much data we are expecting, consider that you have 20 artists that each
have 6 albums which each have 10 songs.  This would be 20 * 6 * 10 = 1200 songs.  Using this rule of thumb, plan to have at least 1000 songs in your database.

You will start out by using the Spotify API to get your data.  To make your life easier, you can use the existing Python package called `spotipy`.  This is not a
requirement, however.  If you prefer, you can use the `requests` module, or any method you like to pull down the data.  We just ask that you do this using Python.

### Transformation
Your output data for this project is expected to be deduplicated, consistent, and relatively free of null / blank values.  In order to ensure data quality,
you will need to complete transformations on the data in Python before you insert any of it into SQLite.  There are many ways to do this, and we will leave 
it up to you on how to do so.  Some recommendations for this would be to use Pandas, base Python structures, or any other Python package you prefer.  Please 
note that some columns may contain nulls while there are other columns that should NEVER have null values (i.e. an ID / URI column).  It will be your 
responsibility to have a clear understanding of the data sets you are working with to make these judgement calls.

Another key point is that the API will sometimes provide multiple data points when you only want to store one.  For instance, if you do an artist search, 
the API could provide any number of genres.  Since we are only storing one of them in the database, you need to consider how to pull out just the first value.
An example of this is the artist, Foo Fighters, has 6 genres listed.  However, for simplicity we are only storing one value.  In this example, you would just
grab the first genre value which is "alternative metal."

### Storage
After your data is transformed and you are confident in it's quality, you need to insert it into your database.  You will need to create a SQLite database
and create tables to store your data in.  There are an endless number of data points provided by spotify, so you will be directed on which data fields to store
and in which format:

#### Artist
https://spotipy.readthedocs.io/en/master/#spotipy.client.Spotify.search

|    column    |   datatype   |                              example                             |
|:------------:|:------------:|:----------------------------------------------------------------:|
|   artist_id  |  varchar(50) |                      7jy3rLJdDQY21OgRLCZ9sD                      |
|  artist_name | varchar(255) |                           Foo Fighters                           |
| external_url | varchar(100) |      https://open.spotify.com/artist/7jy3rLJdDQY21OgRLCZ9sD      |
|     genre    | varchar(100) |                         alternative metal                        |
|   image_url  | varchar(100) | https://i.scdn.co/image/ab6761610000e5eb9a43b87b50cd3d03544bb3e5 |
|   followers  |      int     |                             10156976                             |
|  popularity  |      int     |                                77                                |
|     type     |  varchar(50) |                              artist                              |
|  artist_uri  | varchar(100) |               spotify:artist:7jy3rLJdDQY21OgRLCZ9sD              |

#### Album
https://spotipy.readthedocs.io/en/master/#spotipy.client.Spotify.artist_albums
*When using the above function, be sure to filter by country = 'US' to make your search more direct*
|    column    |   datatype   |                              example                             |
|:------------:|:------------:|:----------------------------------------------------------------:|
|   album_id   |  varchar(50) |                      2FfewmvnA0wctMD64KjOxP                      |
|  album_name  | varchar(255) |                            Dream Widow                           |
| external_url | varchar(100) |       https://open.spotify.com/album/2FfewmvnA0wctMD64KjOxP      |
|   image_url  | varchar(100) | https://i.scdn.co/image/ab67616d0000b273a57abaeb967f055948170bd6 |
| release_date |     date     |                            2022-03-25                            |
| total_tracks |      int     |                                 8                                |
|     type     |  varchar(50) |                               album                              |
|   album_uri  | varchar(100) |               spotify:album:2FfewmvnA0wctMD64KjOxP               |
|   artist_id  |  varchar(50) |                      7jy3rLJdDQY21OgRLCZ9sD                      |

#### Track
https://spotipy.readthedocs.io/en/master/#spotipy.client.Spotify.album_tracks
|    column    |   datatype   |                        example                        |
|:------------:|:------------:|:-----------------------------------------------------:|
|   track_id   |  varchar(50) |                 5k8kaD41vSP6l0Jhe9HzmY                |
| song_name    | varchar(255) |                         Encino                        |
| external_url | varchar(100) | https://open.spotify.com/track/5k8kaD41vSP6l0Jhe9HzmY |
|  duration_ms |      int     |                         98293                         |
|   explicit   |    Boolean   |                          TRUE                         |
|  disc_number |      int     |                           1                           |
|     type     |  varchar(50) |                         track                         |
|   song_uri   | varchar(100) |          spotify:track:5k8kaD41vSP6l0Jhe9HzmY         |
|   album_id   |  varchar(50) |                 2FfewmvnA0wctMD64KjOxP                |

#### Track Features
https://spotipy.readthedocs.io/en/master/#spotipy.client.Spotify.audio_features
|      column      |   datatype   |                example               |
|:----------------:|:------------:|:------------------------------------:|
|     track_id     |  varchar(50) |        5k8kaD41vSP6l0Jhe9HzmY        |
|   danceability   |    double    |                 0.277                |
|      energy      |    double    |                 0.992                |
| instrumentalness |    double    |                 0.836                |
|     liveness     |    double    |                 0.272                |
|     loudness     |    double    |                -6.237                |
|    speechiness   |    double    |                0.0856                |
|       tempo      |    double    |                103.494               |
|       type       |  varchar(50) |            audio_features            |
|      valence     |    double    |                 0.148                |
|     song_uri     | varchar(100) | spotify:track:5k8kaD41vSP6l0Jhe9HzmY |

### Analytics / Visualisation
It is not enough to take data and store it in a database, you have to consider how to make it useful to others.  At this point in the project,
you should have 4 tables in your database.  Think about how to effectively join this data together.  You will create a series of views to make 
the data easier to work with.  As a minimum, you should create views for the following:
- Top songs by artist in terms of duration_ms
- Top artists in the database by # of followers
- Top songs by artist in terms of tempo

Additionally, think of at least 2 additional views you would like to create that you think could be useful for a data scientist
or data analyst.

In addition to views, you will also need to create 3 data visualisations using Python.  You can use any module you want for this.
Python has a number of visualization modules such as matplotlib and seaborn.  You can create any visualisation you think would 
help increase insight and understanding into the data.  The `track_features` table has a number of numeric fields that you could
use for this.

## Environment Setup / Prerequisites

### Getting Spotify Authentication Credentials 
1. Got to https://developer.spotify.com/dashboard/ and login with your existing Spotify account (if you don't have one, you can create one for free to use).
2. Once logged in, click on the Dashboard Tab and then click "Create App" and create an app called "onramp_project."
3. Once inside your app / project, you will see **Client ID** and **Client Secret** near the top left.  These are the values you will need to access the api to pull data.
4. Click "Edit Settings" on your app and add http://localhost:8888/callback for the "Redirect URIs" field. 

Alternatively, you can follow along with this tutorial:  https://www.youtube.com/watch?v=3RGm4jALukM

### Setting Environment Variables
As a general rule, you should never hard code your API keys within any of your scripts.  If you do this, you are making yourself vulnerable from a security point of view.
NEVER store API keys on github or anywhere else that is not secure.  A way around this is to set them as environment variables.  
A primer on this topic including code examples for Unix and Windows operating systems can be viewed here: https://en.wikipedia.org/wiki/Environment_variable
Specifially, you will need to set environment variables for the following:
        - SPOTIPY_CLIENT_ID
        - SPOTIPY_CLIENT_SECRET
        - SPOTIPY_REDIRECT_URI
        
### SQLite3 Primer
In order to make your experience simpler, you will be using SQLite3 as your database for this project.  This is a tool that you will probably not use directly in
a professional setting, but we will use it here so you don't have to spend time installing / configuring RDBMS software.  SQLite3 comes pre-installed with Python
as a base package and is able to store your entire database within a single file (with a .db extension).  Part of your submission for this project will be a 
`spotify.db` SQLite file.  This file will contain all of your tables and views that will comprise the bulk of your project.  A primer on this tool is available
here: https://docs.python.org/3/library/sqlite3.html
Make sure you are familiar with creating / opening a db file, creating tables, running queries, and creating views.

### JSON / Dictionaries Primer
API Data is often served up in JSON format, and this use case is no exception.  As a result, make sure you are comfortable working with dictionaries in Python, 
which are essentially equivalent to JSON in this use case.  A good primer on this topic is here: https://automatetheboringstuff.com/2e/chapter5/
The API data you will be working with is highly nested in nature, meaning that data structures will appear within other data structures.  This can get confusing
at times, but one way you can help yourself is by using a JSON visualisation tool.  For instance, the pprint module in Python can be used to print 
JSON / dictionaries in a human readable format.
