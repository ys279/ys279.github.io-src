Title: SQLite3 schema to store Spotify Song data  in 3NF
Date: 2020-10-07 20:55
headline:Yiping's Blog 4



Create a SQLite3 schema to store this data in  3rd normal form (3NF), and populate the tables. Use an SQL query to find the names of all playlists that contain instrumentals. 

## Download and baic info of  Spotify Song data


```python
import pandas as pd
import numpy as np
```


```python
df = pd.read_csv("https://raw.githubusercontent.com/rfordatascience/tidytuesday/master/data/2020/2020-01-21/spotify_songs.csv")
```

look at the data


```python
np.shape(df)
```




    (32833, 23)




```python
list(df.columns) 
```




    ['track_id',
     'track_name',
     'track_artist',
     'track_popularity',
     'track_album_id',
     'track_album_name',
     'track_album_release_date',
     'playlist_name',
     'playlist_id',
     'playlist_genre',
     'playlist_subgenre',
     'danceability',
     'energy',
     'key',
     'loudness',
     'mode',
     'speechiness',
     'acousticness',
     'instrumentalness',
     'liveness',
     'valence',
     'tempo',
     'duration_ms']



## 1NF : Split composite entries

There is no composite entries, so no action take for 1NF

## 2NF

- Break partial dependencies
    - Identify candidate PK for each row
    - If there is a composite PK, see if other columns have partial dependencies

candidate PK :
- track_id for track table
- track_album_id for album table
- playlist_id for playlist table  

A table that relates playlistid to trackid  
A table that relates album_id to trackid  

other table such as subgenre will save for 3NF


```python
track =  df.drop(df.columns[4:11], axis=1).drop_duplicates()
```


```python
#  track_id is a primary key for track table
track.loc[:,"track_id"].value_counts().max()
```




    1




```python
album = df.iloc[:,4:7].drop_duplicates()
```


```python
# album_id is a primary key for album table
album.loc[:,"track_album_id"].value_counts().max()
```




    1




```python
playlist = df.loc[:,["playlist_id","playlist_name","playlist_genre","playlist_subgenre"]]
playlist = playlist.drop_duplicates()
playlist.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>playlist_id</th>
      <th>playlist_name</th>
      <th>playlist_genre</th>
      <th>playlist_subgenre</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>Pop Remix</td>
      <td>pop</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>70</th>
      <td>37i9dQZF1DWZQaaqNMbbXa</td>
      <td>Dance Pop</td>
      <td>pop</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>167</th>
      <td>37i9dQZF1DX2ENAPP1Tyed</td>
      <td>Dance Room</td>
      <td>pop</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>223</th>
      <td>37i9dQZF1DWSJHnPb1f0X3</td>
      <td>Cardio</td>
      <td>pop</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>272</th>
      <td>37i9dQZF1DX6pH08wMhkaI</td>
      <td>Dance Pop Hits</td>
      <td>pop</td>
      <td>dance pop</td>
    </tr>
  </tbody>
</table>
</div>




```python
# playlist_ id is not a primary key for playlist table
playlist.loc[:,"playlist_id"].value_counts().max()
```




    3




```python
track_album = df.loc[:,["track_id","track_album_id"]].drop_duplicates()
```


```python
#track_id is a primary key for track_album table
track_album.loc[:,"track_id"].value_counts().max()
```




    1




```python
track_playlist = df.loc[:,["track_id","playlist_id"]].drop_duplicates()
```


```python
print(track_playlist.loc[:,"track_id"].value_counts().max())
print(track_playlist.loc[:,"playlist_id"].value_counts().max())
print(track_playlist.loc[:,["track_id","playlist_id"]].value_counts().max())
#there no single primary key in "playlist_id" table
```

    8
    100
    1


### 3NF

- Remove transitive dependencies  
Mainly deal with playlist table

- A table that relates playlist id with name
- A table that relates genre and subgenre
- A table that relates playlist to subgenre



```python
playlist_name = df.loc[:,["playlist_id","playlist_name"]].drop_duplicates()
```


```python
#playlist_id is a primary key for playlist_name table
playlist_name.loc[:,"playlist_id"].value_counts().max()
```




    1




```python
playlist_genre = df.loc[:,["playlist_genre","playlist_subgenre"]].drop_duplicates()
```


```python
#playlist_subgenre is a PK for playlist_genre table
playlist_genre.loc[:,"playlist_subgenre"].value_counts().max()
```




    1




```python
playlist_subgenre = df.loc[:,['playlist_id','playlist_subgenre']].drop_duplicates()
```


```python
# no single PK for playlist_subgenre
playlist_subgenre.loc[:,['playlist_id','playlist_subgenre']].value_counts().max()
```




    1



## Final tables


```python
track.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_id</th>
      <th>track_name</th>
      <th>track_artist</th>
      <th>track_popularity</th>
      <th>danceability</th>
      <th>energy</th>
      <th>key</th>
      <th>loudness</th>
      <th>mode</th>
      <th>speechiness</th>
      <th>acousticness</th>
      <th>instrumentalness</th>
      <th>liveness</th>
      <th>valence</th>
      <th>tempo</th>
      <th>duration_ms</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6f807x0ima9a1j3VPbc7VN</td>
      <td>I Don't Care (with Justin Bieber) - Loud Luxur...</td>
      <td>Ed Sheeran</td>
      <td>66</td>
      <td>0.748</td>
      <td>0.916</td>
      <td>6</td>
      <td>-2.634</td>
      <td>1</td>
      <td>0.0583</td>
      <td>0.1020</td>
      <td>0.000000</td>
      <td>0.0653</td>
      <td>0.518</td>
      <td>122.036</td>
      <td>194754</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0r7CVbZTWZgbTCYdfa2P31</td>
      <td>Memories - Dillon Francis Remix</td>
      <td>Maroon 5</td>
      <td>67</td>
      <td>0.726</td>
      <td>0.815</td>
      <td>11</td>
      <td>-4.969</td>
      <td>1</td>
      <td>0.0373</td>
      <td>0.0724</td>
      <td>0.004210</td>
      <td>0.3570</td>
      <td>0.693</td>
      <td>99.972</td>
      <td>162600</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1z1Hg7Vb0AhHDiEmnDE79l</td>
      <td>All the Time - Don Diablo Remix</td>
      <td>Zara Larsson</td>
      <td>70</td>
      <td>0.675</td>
      <td>0.931</td>
      <td>1</td>
      <td>-3.432</td>
      <td>0</td>
      <td>0.0742</td>
      <td>0.0794</td>
      <td>0.000023</td>
      <td>0.1100</td>
      <td>0.613</td>
      <td>124.008</td>
      <td>176616</td>
    </tr>
    <tr>
      <th>3</th>
      <td>75FpbthrwQmzHlBJLuGdC7</td>
      <td>Call You Mine - Keanu Silva Remix</td>
      <td>The Chainsmokers</td>
      <td>60</td>
      <td>0.718</td>
      <td>0.930</td>
      <td>7</td>
      <td>-3.778</td>
      <td>1</td>
      <td>0.1020</td>
      <td>0.0287</td>
      <td>0.000009</td>
      <td>0.2040</td>
      <td>0.277</td>
      <td>121.956</td>
      <td>169093</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1e8PAfcKUYoKkxPhrHqw4x</td>
      <td>Someone You Loved - Future Humans Remix</td>
      <td>Lewis Capaldi</td>
      <td>69</td>
      <td>0.650</td>
      <td>0.833</td>
      <td>1</td>
      <td>-4.672</td>
      <td>1</td>
      <td>0.0359</td>
      <td>0.0803</td>
      <td>0.000000</td>
      <td>0.0833</td>
      <td>0.725</td>
      <td>123.976</td>
      <td>189052</td>
    </tr>
  </tbody>
</table>
</div>




```python
album.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_album_id</th>
      <th>track_album_name</th>
      <th>track_album_release_date</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>2oCs0DGTsRO98Gh5ZSl2Cx</td>
      <td>I Don't Care (with Justin Bieber) [Loud Luxury...</td>
      <td>2019-06-14</td>
    </tr>
    <tr>
      <th>1</th>
      <td>63rPSO264uRjW1X5E6cWv6</td>
      <td>Memories (Dillon Francis Remix)</td>
      <td>2019-12-13</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1HoSmj2eLcsrR0vE9gThr4</td>
      <td>All the Time (Don Diablo Remix)</td>
      <td>2019-07-05</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1nqYsOef1yKKuGOVchbsk6</td>
      <td>Call You Mine - The Remixes</td>
      <td>2019-07-19</td>
    </tr>
    <tr>
      <th>4</th>
      <td>7m7vv9wlQ4i0LFuJiE2zsQ</td>
      <td>Someone You Loved (Future Humans Remix)</td>
      <td>2019-03-05</td>
    </tr>
  </tbody>
</table>
</div>




```python
track_album.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_id</th>
      <th>track_album_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6f807x0ima9a1j3VPbc7VN</td>
      <td>2oCs0DGTsRO98Gh5ZSl2Cx</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0r7CVbZTWZgbTCYdfa2P31</td>
      <td>63rPSO264uRjW1X5E6cWv6</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1z1Hg7Vb0AhHDiEmnDE79l</td>
      <td>1HoSmj2eLcsrR0vE9gThr4</td>
    </tr>
    <tr>
      <th>3</th>
      <td>75FpbthrwQmzHlBJLuGdC7</td>
      <td>1nqYsOef1yKKuGOVchbsk6</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1e8PAfcKUYoKkxPhrHqw4x</td>
      <td>7m7vv9wlQ4i0LFuJiE2zsQ</td>
    </tr>
  </tbody>
</table>
</div>




```python
track_playlist.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>track_id</th>
      <th>playlist_id</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>6f807x0ima9a1j3VPbc7VN</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0r7CVbZTWZgbTCYdfa2P31</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1z1Hg7Vb0AhHDiEmnDE79l</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
    </tr>
    <tr>
      <th>3</th>
      <td>75FpbthrwQmzHlBJLuGdC7</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
    </tr>
    <tr>
      <th>4</th>
      <td>1e8PAfcKUYoKkxPhrHqw4x</td>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
    </tr>
  </tbody>
</table>
</div>




```python
playlist_name.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>playlist_id</th>
      <th>playlist_name</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>Pop Remix</td>
    </tr>
    <tr>
      <th>70</th>
      <td>37i9dQZF1DWZQaaqNMbbXa</td>
      <td>Dance Pop</td>
    </tr>
    <tr>
      <th>167</th>
      <td>37i9dQZF1DX2ENAPP1Tyed</td>
      <td>Dance Room</td>
    </tr>
    <tr>
      <th>223</th>
      <td>37i9dQZF1DWSJHnPb1f0X3</td>
      <td>Cardio</td>
    </tr>
    <tr>
      <th>272</th>
      <td>37i9dQZF1DX6pH08wMhkaI</td>
      <td>Dance Pop Hits</td>
    </tr>
  </tbody>
</table>
</div>




```python
playlist_genre.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>playlist_genre</th>
      <th>playlist_subgenre</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>pop</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>1298</th>
      <td>pop</td>
      <td>post-teen pop</td>
    </tr>
    <tr>
      <th>2427</th>
      <td>pop</td>
      <td>electropop</td>
    </tr>
    <tr>
      <th>3835</th>
      <td>pop</td>
      <td>indie poptimism</td>
    </tr>
    <tr>
      <th>5507</th>
      <td>rap</td>
      <td>hip hop</td>
    </tr>
  </tbody>
</table>
</div>




```python
playlist_subgenre.head()
```




<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>playlist_id</th>
      <th>playlist_subgenre</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>37i9dQZF1DXcZDD7cfEKhW</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>70</th>
      <td>37i9dQZF1DWZQaaqNMbbXa</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>167</th>
      <td>37i9dQZF1DX2ENAPP1Tyed</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>223</th>
      <td>37i9dQZF1DWSJHnPb1f0X3</td>
      <td>dance pop</td>
    </tr>
    <tr>
      <th>272</th>
      <td>37i9dQZF1DX6pH08wMhkaI</td>
      <td>dance pop</td>
    </tr>
  </tbody>
</table>
</div>



## Connect python with SQLITE


```python
import sqlite3
```


```python
con = sqlite3.connect('spotify.db')

# Create tables in the database
track.to_sql(name='track', con=con,if_exists='replace', index = False)
album.to_sql(name='album', con=con,if_exists='replace', index = False)
track_album.to_sql(name='track_album', if_exists='replace',con=con, index = False)
track_playlist.to_sql(name='track_playlist',if_exists='replace', con=con, index = False)
playlist_name.to_sql(name='playlist_name',if_exists='replace', con=con, index = False)
playlist_genre.to_sql(name='playlist_genre',if_exists='replace', con=con, index = False)
playlist_subgenre.to_sql(name='playlist_subgenre',if_exists='replace', con=con, index = False)

```


```python
%load_ext sql
```

    The sql extension is already loaded. To reload it, use:
      %reload_ext sql



```python
%sql sqlite:///spotify.db
```




    'Connected: @spotify.db'




```python
%%sql
SELECT * from sqlite_master WHERE type='table'
```

       sqlite:///data/spotify.db
     * sqlite:///spotify.db
    Done.





<table>
    <tr>
        <th>type</th>
        <th>name</th>
        <th>tbl_name</th>
        <th>rootpage</th>
        <th>sql</th>
    </tr>
    <tr>
        <td>table</td>
        <td>track</td>
        <td>track</td>
        <td>2</td>
        <td>CREATE TABLE &quot;track&quot; (<br>&quot;track_id&quot; TEXT,<br>  &quot;track_name&quot; TEXT,<br>  &quot;track_artist&quot; TEXT,<br>  &quot;track_popularity&quot; INTEGER,<br>  &quot;danceability&quot; REAL,<br>  &quot;energy&quot; REAL,<br>  &quot;key&quot; INTEGER,<br>  &quot;loudness&quot; REAL,<br>  &quot;mode&quot; INTEGER,<br>  &quot;speechiness&quot; REAL,<br>  &quot;acousticness&quot; REAL,<br>  &quot;instrumentalness&quot; REAL,<br>  &quot;liveness&quot; REAL,<br>  &quot;valence&quot; REAL,<br>  &quot;tempo&quot; REAL,<br>  &quot;duration_ms&quot; INTEGER<br>)</td>
    </tr>
    <tr>
        <td>table</td>
        <td>album</td>
        <td>album</td>
        <td>1041</td>
        <td>CREATE TABLE &quot;album&quot; (<br>&quot;track_album_id&quot; TEXT,<br>  &quot;track_album_name&quot; TEXT,<br>  &quot;track_album_release_date&quot; TEXT<br>)</td>
    </tr>
    <tr>
        <td>table</td>
        <td>track_album</td>
        <td>track_album</td>
        <td>1367</td>
        <td>CREATE TABLE &quot;track_album&quot; (<br>&quot;track_id&quot; TEXT,<br>  &quot;track_album_id&quot; TEXT<br>)</td>
    </tr>
    <tr>
        <td>table</td>
        <td>track_playlist</td>
        <td>track_playlist</td>
        <td>1734</td>
        <td>CREATE TABLE &quot;track_playlist&quot; (<br>&quot;track_id&quot; TEXT,<br>  &quot;playlist_id&quot; TEXT<br>)</td>
    </tr>
    <tr>
        <td>table</td>
        <td>playlist_name</td>
        <td>playlist_name</td>
        <td>2152</td>
        <td>CREATE TABLE &quot;playlist_name&quot; (<br>&quot;playlist_id&quot; TEXT,<br>  &quot;playlist_name&quot; TEXT<br>)</td>
    </tr>
    <tr>
        <td>table</td>
        <td>playlist_genre</td>
        <td>playlist_genre</td>
        <td>2160</td>
        <td>CREATE TABLE &quot;playlist_genre&quot; (<br>&quot;playlist_genre&quot; TEXT,<br>  &quot;playlist_subgenre&quot; TEXT<br>)</td>
    </tr>
    <tr>
        <td>table</td>
        <td>playlist_subgenre</td>
        <td>playlist_subgenre</td>
        <td>2161</td>
        <td>CREATE TABLE &quot;playlist_subgenre&quot; (<br>&quot;playlist_id&quot; TEXT,<br>  &quot;playlist_subgenre&quot; TEXT<br>)</td>
    </tr>
</table>



## Use SQL query to find the names of all playlists that contain instrumentals


```python
%%sql
SELECT distinct playlist_name.playlist_name
FROM track
INNER JOIN track_playlist 
    ON track_playlist.track_id = track.track_id
INNER JOIN playlist_name 
    ON track_playlist.playlist_id = playlist_name.playlist_id
WHERE track.instrumentalness > 0.5
```

       sqlite:///data/spotify.db
     * sqlite:///spotify.db
    Done.





<table>
    <tr>
        <th>playlist_name</th>
    </tr>
    <tr>
        <td>Pop Remix</td>
    </tr>
    <tr>
        <td>Dance Room</td>
    </tr>
    <tr>
        <td>Pop Warmup 130 BPM</td>
    </tr>
    <tr>
        <td>Dance Pop</td>
    </tr>
    <tr>
        <td>Dance Pop Tunes</td>
    </tr>
    <tr>
        <td>Pop / Dance</td>
    </tr>
    <tr>
        <td>Most Popular 2020 TOP 50</td>
    </tr>
    <tr>
        <td>post-teen alternative, indie, pop (large variety)</td>
    </tr>
    <tr>
        <td>Todo Éxitos</td>
    </tr>
    <tr>
        <td>Charts 2020 🔥Top 2020🔥Hits 2020🔥Summer 2020🔥Pop 2020🔥Popular Music🔥Clean Pop 2020🔥Sing Alongs</td>
    </tr>
    <tr>
        <td>2020 Hits &amp; 2019  Hits – Top Global Tracks 🔥🔥🔥</td>
    </tr>
    <tr>
        <td>Pop Hits 2020</td>
    </tr>
    <tr>
        <td>Music&amp;Other Drugs</td>
    </tr>
    <tr>
        <td>90s Dance Hits</td>
    </tr>
    <tr>
        <td>CHRISTIAN ELECTRO / DANCE / EDM</td>
    </tr>
    <tr>
        <td>Christian Dance Party</td>
    </tr>
    <tr>
        <td>Pop Dance Hits</td>
    </tr>
    <tr>
        <td>Ultimate Indie Presents... Best Indie Tracks of the 2010s</td>
    </tr>
    <tr>
        <td>Soul Coffee (The Best Neo-Soul Mixtape ever)</td>
    </tr>
    <tr>
        <td>permanent wave</td>
    </tr>
    <tr>
        <td>TUNES DANCE AND POP</td>
    </tr>
    <tr>
        <td>Great Pops</td>
    </tr>
    <tr>
        <td>ELECTROPOP</td>
    </tr>
    <tr>
        <td>Pop Inglés (2020 - 2010s)💙 Música En Inglés 2010s</td>
    </tr>
    <tr>
        <td>Dr. Q&#x27;s Prescription Playlist💊</td>
    </tr>
    <tr>
        <td>BALLARE - رقص</td>
    </tr>
    <tr>
        <td>post teen pop</td>
    </tr>
    <tr>
        <td>Electro Pop | Electropop</td>
    </tr>
    <tr>
        <td>Trance Party 2019 by FUTURE TRANCE</td>
    </tr>
    <tr>
        <td>Pop EDM</td>
    </tr>
    <tr>
        <td>Electropop Hits  2017-2020</td>
    </tr>
    <tr>
        <td>This Is: Javiera Mena</td>
    </tr>
    <tr>
        <td>ElectroPop 2020</td>
    </tr>
    <tr>
        <td>Electropop - Pop</td>
    </tr>
    <tr>
        <td>This Is Janelle Monáe</td>
    </tr>
    <tr>
        <td>ELECTROPOP🐹</td>
    </tr>
    <tr>
        <td>Electropop And Play</td>
    </tr>
    <tr>
        <td>Mix ElectroPop//ElectroHouse// DeepHouse 2020</td>
    </tr>
    <tr>
        <td>EDM - pop remixes</td>
    </tr>
    <tr>
        <td>ELECTROPOP EN ESPAÑOL</td>
    </tr>
    <tr>
        <td>Maxi Pop  GOLD (New Wave, Electropop, Synth Pop...)</td>
    </tr>
    <tr>
        <td>The Sound of Permanent Wave</td>
    </tr>
    <tr>
        <td>Gothic / Industrial / Mittelalter / EBM / Futurepop / Gothik / Electropop</td>
    </tr>
    <tr>
        <td>Permanent Wave</td>
    </tr>
    <tr>
        <td>80&#x27;s Songs | Top 💯 80s Music Hits</td>
    </tr>
    <tr>
        <td>GTA V - Radio Mirror Park</td>
    </tr>
    <tr>
        <td>10er Playlist</td>
    </tr>
    <tr>
        <td>Chillout &amp; Remixes 💜</td>
    </tr>
    <tr>
        <td>Today&#x27;s Hits (Clean)</td>
    </tr>
    <tr>
        <td>POPTIMISM</td>
    </tr>
    <tr>
        <td>The Sound of Indie Poptimism</td>
    </tr>
    <tr>
        <td>Indie/Jazz Poptimism</td>
    </tr>
    <tr>
        <td>Neo-Soul Guitar</td>
    </tr>
    <tr>
        <td>Lo-Fi Beats</td>
    </tr>
    <tr>
        <td>House/Electro/Progressive/Disco/Lofi/Synthwave</td>
    </tr>
    <tr>
        <td>The Edge of Indie Poptimism</td>
    </tr>
    <tr>
        <td>2019 in Indie Poptimism</td>
    </tr>
    <tr>
        <td>Indie Poptimism</td>
    </tr>
    <tr>
        <td>A Loose Definition of Indie Poptimism</td>
    </tr>
    <tr>
        <td>Happy EDM</td>
    </tr>
    <tr>
        <td>The Pulse of Indie Poptimism</td>
    </tr>
    <tr>
        <td>Deep Electronic Music 2020 &amp; Progressive House</td>
    </tr>
    <tr>
        <td>Dancefloor Beats</td>
    </tr>
    <tr>
        <td>Electro Posé - Discoveries</td>
    </tr>
    <tr>
        <td>Indie Poptimism!</td>
    </tr>
    <tr>
        <td>indie poptimism</td>
    </tr>
    <tr>
        <td>Trap Nation 🔊</td>
    </tr>
    <tr>
        <td>Deep-deep Bubble Pop</td>
    </tr>
    <tr>
        <td>Jazz Vibes</td>
    </tr>
    <tr>
        <td>Sunny Beats</td>
    </tr>
    <tr>
        <td>Lush Lofi</td>
    </tr>
    <tr>
        <td>Lofi Hip-Hop</td>
    </tr>
    <tr>
        <td>Southern California Hip Hop Primer</td>
    </tr>
    <tr>
        <td>90&#x27;s Gangster Rap</td>
    </tr>
    <tr>
        <td>90&#x27;s Southern Hip Hop</td>
    </tr>
    <tr>
        <td>90s-2000s Southern Hip Hop / Crunk</td>
    </tr>
    <tr>
        <td>◤ Hip Hop Dance Music – Urban – Trap – Breaking Locking Popping Bopping – WOD – World of Dance</td>
    </tr>
    <tr>
        <td>Badass Rock</td>
    </tr>
    <tr>
        <td>Minitruckin Playlist</td>
    </tr>
    <tr>
        <td>Hip-Hop &#x27;n RnB</td>
    </tr>
    <tr>
        <td>Big Room House | Festival Bangers</td>
    </tr>
    <tr>
        <td>HIP&amp;HOP</td>
    </tr>
    <tr>
        <td>Fitness Workout Electro | House | Dance | Progressive House</td>
    </tr>
    <tr>
        <td>Jeff Seid Electro House</td>
    </tr>
    <tr>
        <td>BIG-ROOM NEVER DIES !</td>
    </tr>
    <tr>
        <td>Alex Workout</td>
    </tr>
    <tr>
        <td>Contemporary Hip Hop</td>
    </tr>
    <tr>
        <td>3rd Coast Classics</td>
    </tr>
    <tr>
        <td>90s Hiphop / Gangsta Rap</td>
    </tr>
    <tr>
        <td>Gangsta Rap 💎 Rap Party</td>
    </tr>
    <tr>
        <td>RAP Gangsta</td>
    </tr>
    <tr>
        <td>RUSSIAN Gangster Rap</td>
    </tr>
    <tr>
        <td>90s Gangsta Rap / Top Hip-hop Classics</td>
    </tr>
    <tr>
        <td>Locker Room</td>
    </tr>
    <tr>
        <td>Rap Party 24/7 Radio / Gangsta Rap</td>
    </tr>
    <tr>
        <td>&lt; DARK TRAP &gt;</td>
    </tr>
    <tr>
        <td>Trapperz Brasil</td>
    </tr>
    <tr>
        <td>Trap Nation</td>
    </tr>
    <tr>
        <td>Trap Mojito</td>
    </tr>
    <tr>
        <td>Arabic Trap</td>
    </tr>
    <tr>
        <td>Sad Trap</td>
    </tr>
    <tr>
        <td>Electro House 2020</td>
    </tr>
    <tr>
        <td>♥ EDM LOVE 2020</td>
    </tr>
    <tr>
        <td>This Is Guns N&#x27; Roses</td>
    </tr>
    <tr>
        <td>The Black Album</td>
    </tr>
    <tr>
        <td>City Pop 1985 シティーポップ</td>
    </tr>
    <tr>
        <td>Japanese Funk/Soul/NEO/Jazz/Acid</td>
    </tr>
    <tr>
        <td>The Cranberries Best Of</td>
    </tr>
    <tr>
        <td>Classic Rock Drive</td>
    </tr>
    <tr>
        <td>Vault: Def Leppard Greatest Hits</td>
    </tr>
    <tr>
        <td>80s Pop &amp; Rock Hits and Album Tracks</td>
    </tr>
    <tr>
        <td>Rock and Rios</td>
    </tr>
    <tr>
        <td>Progressive Rock / Metal - Rock /Metal  Progresivo</td>
    </tr>
    <tr>
        <td>House Of The Rising Sun</td>
    </tr>
    <tr>
        <td>Rock Classics</td>
    </tr>
    <tr>
        <td>Hard Rock Cafe Classics</td>
    </tr>
    <tr>
        <td>Hard Rock Classics 1967-1991 (Party Edition)</td>
    </tr>
    <tr>
        <td>Classic Rock</td>
    </tr>
    <tr>
        <td>Coldplay – Ghost Stories (Deluxe Edition)</td>
    </tr>
    <tr>
        <td>70s Pop &amp; Rock Hits and Deep Tracks</td>
    </tr>
    <tr>
        <td>L&#x27; ALBUM ROCK</td>
    </tr>
    <tr>
        <td>The Queen - La Discografia Completa</td>
    </tr>
    <tr>
        <td>Soda Stereo – El Ultimo Concierto</td>
    </tr>
    <tr>
        <td>The Sound of Album Rock</td>
    </tr>
    <tr>
        <td>Classic Rock Now</td>
    </tr>
    <tr>
        <td>Soft Rock Drive</td>
    </tr>
    <tr>
        <td>The 1950s/1960s/1970s/1980s/1990s/2000s/2010s with pop/r&amp;b/soul/boogie/dance/jazz/hip hop/hop/rap.</td>
    </tr>
    <tr>
        <td>Supernatural Classic Rock</td>
    </tr>
    <tr>
        <td>Classic Rock Legends</td>
    </tr>
    <tr>
        <td>Classic Rock 70s 80s 90s, Rock Classics - 70s Rock, 80s Rock, 90s Rock Rock  Classicos</td>
    </tr>
    <tr>
        <td>Southern Rock/Classic Rock</td>
    </tr>
    <tr>
        <td>80s / Classic Rock</td>
    </tr>
    <tr>
        <td>Afro Psychedelica</td>
    </tr>
    <tr>
        <td>Classic Rock Retrogamer</td>
    </tr>
    <tr>
        <td>Workday: Rock Classics</td>
    </tr>
    <tr>
        <td>Classic Rock Greatest Hits</td>
    </tr>
    <tr>
        <td>Blues Rock</td>
    </tr>
    <tr>
        <td>70&#x27;s Classic Rock</td>
    </tr>
    <tr>
        <td>Classic Rock Radio</td>
    </tr>
    <tr>
        <td>Classic Rock Playlist.</td>
    </tr>
    <tr>
        <td>Permanent wave 🌊</td>
    </tr>
    <tr>
        <td>keg party jukebox</td>
    </tr>
    <tr>
        <td>Permanent Wave CHDB</td>
    </tr>
    <tr>
        <td>HARD ROCK CAFE</td>
    </tr>
    <tr>
        <td>②⓪①⑨ mixed</td>
    </tr>
    <tr>
        <td>I didn’t know perm stood for permanent (wave)</td>
    </tr>
    <tr>
        <td>Modern Indie Rock // Alternative Rock / Garage Rock / Pop Punk / Grunge / Britpop / Pop Rock</td>
    </tr>
    <tr>
        <td>&quot;Permanent Wave&quot;</td>
    </tr>
    <tr>
        <td>Rock Hard</td>
    </tr>
    <tr>
        <td>Hard Rock</td>
    </tr>
    <tr>
        <td>Hard Rock Workout</td>
    </tr>
    <tr>
        <td>This Is Scorpions</td>
    </tr>
    <tr>
        <td>Hard Rock Workout!</td>
    </tr>
    <tr>
        <td>Classic Hard Rock</td>
    </tr>
    <tr>
        <td>Workout Hard Rock</td>
    </tr>
    <tr>
        <td>HARD ROCK Vibes</td>
    </tr>
    <tr>
        <td>New Hard Rock</td>
    </tr>
    <tr>
        <td>70s Hard Rock</td>
    </tr>
    <tr>
        <td>2000&#x27;s hard rock</td>
    </tr>
    <tr>
        <td>EDM 2020 House &amp; Dance</td>
    </tr>
    <tr>
        <td>Tropical House</td>
    </tr>
    <tr>
        <td>Waves Pop and EDM</td>
    </tr>
    <tr>
        <td>Vibra Tropical</td>
    </tr>
    <tr>
        <td>Tropical Vibes</td>
    </tr>
    <tr>
        <td>Orgulho Tropical</td>
    </tr>
    <tr>
        <td>Tropical Nights</td>
    </tr>
    <tr>
        <td>Tropical House 2020</td>
    </tr>
    <tr>
        <td>Tropical Morning</td>
    </tr>
    <tr>
        <td>EDM TROPICAL</td>
    </tr>
    <tr>
        <td>F**KIN PERFECT</td>
    </tr>
    <tr>
        <td>INDIE POP! TUNES</td>
    </tr>
    <tr>
        <td>URBAN NATION</td>
    </tr>
    <tr>
        <td>ALPAS Music Festival</td>
    </tr>
    <tr>
        <td>SINULOG Festival 2020</td>
    </tr>
    <tr>
        <td>Electro Vibes</td>
    </tr>
    <tr>
        <td>Reggaeton Classics</td>
    </tr>
    <tr>
        <td>latin hip hop</td>
    </tr>
    <tr>
        <td>Latin Hip Hop/Freestyle</td>
    </tr>
    <tr>
        <td>Latin Village 2019</td>
    </tr>
    <tr>
        <td>80&#x27;s Freestyle/Disco Dance Party (Set Crossfade to 4-Seconds)</td>
    </tr>
    <tr>
        <td>LATIN FLOW MIX - Música Cristiana🎵</td>
    </tr>
    <tr>
        <td>HIP-HOP: Latin Rap [&#x27;89-present]</td>
    </tr>
    <tr>
        <td>Top Urban Underground</td>
    </tr>
    <tr>
        <td>Urban contemporary</td>
    </tr>
    <tr>
        <td>urban CONTEMPORARY</td>
    </tr>
    <tr>
        <td>Urban Contemporary</td>
    </tr>
    <tr>
        <td>Ultimate Indie Presents... Best Tracks of 2019</td>
    </tr>
    <tr>
        <td>Bluegrass Covers</td>
    </tr>
    <tr>
        <td>Latest Hits 2020 - Pop, Hip Hop &amp; RnB</td>
    </tr>
    <tr>
        <td>Smooth Hip Hop</td>
    </tr>
    <tr>
        <td>Fresh Essentials</td>
    </tr>
    <tr>
        <td>New Jack Swing</td>
    </tr>
    <tr>
        <td>New Jack Swing/ R&amp;B Hits: 1987 - 2002</td>
    </tr>
    <tr>
        <td>Swingbeat (old skool), New Jack Swing, R&amp;B, Hip Hop, Urban</td>
    </tr>
    <tr>
        <td>New Jack City</td>
    </tr>
    <tr>
        <td>90s R&amp;B - The BET Planet Groove/Midnight Love Mix</td>
    </tr>
    <tr>
        <td>Ultimate Throwbacks Collection</td>
    </tr>
    <tr>
        <td>80s-90s R&amp;B / New Jack Swing / Funk / Dance / Soul</td>
    </tr>
    <tr>
        <td>R&amp;B 80&#x27;s/90&#x27;s/00&#x27;s</td>
    </tr>
    <tr>
        <td>Neo Soul Music</td>
    </tr>
    <tr>
        <td>Neo-Soul</td>
    </tr>
    <tr>
        <td>NEO SOUL GUITAR</td>
    </tr>
    <tr>
        <td>Neo Soul / Modern Jazz / Smooth Hiphop</td>
    </tr>
    <tr>
        <td>NEO-soul</td>
    </tr>
    <tr>
        <td>Groovy // Funky // Neo-Soul</td>
    </tr>
    <tr>
        <td>Neo-Soul / Soulful R&amp;B</td>
    </tr>
    <tr>
        <td>Neo Soul 2019</td>
    </tr>
    <tr>
        <td>NEO FUNK AND SOUL</td>
    </tr>
    <tr>
        <td>Neo Soul</td>
    </tr>
    <tr>
        <td>Neo-Jazz Soul RnB &amp; Afro</td>
    </tr>
    <tr>
        <td>Neo-Soul Essentials</td>
    </tr>
    <tr>
        <td>Brand New EDM</td>
    </tr>
    <tr>
        <td>Electro House Top Tracks</td>
    </tr>
    <tr>
        <td>Big Room EDM - by Spinnin&#x27; Records</td>
    </tr>
    <tr>
        <td>EDM House &amp; Dance</td>
    </tr>
    <tr>
        <td>Hands Up‏‏​​   ‍</td>
    </tr>
    <tr>
        <td>EDM 2019</td>
    </tr>
    <tr>
        <td>Nasty Bits</td>
    </tr>
    <tr>
        <td>Epic Bass Drops | Best House Mixes</td>
    </tr>
    <tr>
        <td>Techno House 2020 👽 Best Collection 👻 Top DJ’s Electronic Music - Deep House - Trance - Tech House - Dance - Electro Pop</td>
    </tr>
    <tr>
        <td>Electro Swing Top 100</td>
    </tr>
    <tr>
        <td>Electro Swing</td>
    </tr>
    <tr>
        <td>Electro House</td>
    </tr>
    <tr>
        <td>PAROOKAVILLE - Big Room</td>
    </tr>
    <tr>
        <td>ELECTRO HOUSE 2020</td>
    </tr>
    <tr>
        <td>New House   ‍</td>
    </tr>
    <tr>
        <td>@deniceemoberg EDM - POP REMIXES</td>
    </tr>
    <tr>
        <td>big boom room — TOMORROWLAND EDC EDM BIG ROOM AMF ADE DANCE TRANCE HARDWELL TIESTO</td>
    </tr>
    <tr>
        <td>Epic Bass Drops</td>
    </tr>
    <tr>
        <td>Big Room Beast</td>
    </tr>
    <tr>
        <td>Crossfit‏‏​​   ‍</td>
    </tr>
    <tr>
        <td>Electro House - by Spinnin&#x27; Records</td>
    </tr>
    <tr>
        <td>Big Room House / Bigroom</td>
    </tr>
    <tr>
        <td>💊ELECTRO-HOUSE-TECH💊</td>
    </tr>
    <tr>
        <td>Club Mix 2020 🍹</td>
    </tr>
    <tr>
        <td>House Electro 2019</td>
    </tr>
    <tr>
        <td>🔊BASSBOOSTED🔊⚡ELECTRO HOUSE⚡🔥EDM CAR MUSIC2018/2019🔥</td>
    </tr>
    <tr>
        <td>Big Room EDM</td>
    </tr>
    <tr>
        <td>2011-2014 House</td>
    </tr>
    <tr>
        <td>Trending EDM by Nik Cooper</td>
    </tr>
    <tr>
        <td>Bounce United</td>
    </tr>
    <tr>
        <td>Sick Big Room House Drops | EZUMI</td>
    </tr>
    <tr>
        <td>Festival Music 2019 - Warm Up Music (EDM, Big Room &amp; Progressive House)</td>
    </tr>
    <tr>
        <td>[EAS]: Festival Big Room</td>
    </tr>
    <tr>
        <td>Big Room House</td>
    </tr>
    <tr>
        <td>Gym (Melbourne Bounce/Progressive House)</td>
    </tr>
    <tr>
        <td>Big Room 2019</td>
    </tr>
    <tr>
        <td>Fresh EDM | Progressive House | Electro House | Trap | Deep House | Electronic | Future House/Bass</td>
    </tr>
    <tr>
        <td>Pop EDM Remixes</td>
    </tr>
    <tr>
        <td>Pop Hits 2000-2019</td>
    </tr>
    <tr>
        <td>Tastemakers Ball  -  EDM - POP and FUN</td>
    </tr>
    <tr>
        <td>EDM/POP</td>
    </tr>
    <tr>
        <td>Selected House</td>
    </tr>
    <tr>
        <td>Vocal House</td>
    </tr>
    <tr>
        <td>Electro/Progressive/Club House</td>
    </tr>
    <tr>
        <td>Underground Party | Hypnotic | Minimal | Acid | Big Room | Tech | Liquid</td>
    </tr>
</table>



### Total number of the playlist names  that contain instrumentals is 256.


```python

```
