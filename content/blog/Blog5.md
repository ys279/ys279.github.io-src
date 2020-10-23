Title: Download and extract Starwars information
Date: 2020-10-22 20:55
headline:Yiping's Blog 5


Using the requests library, download all the people in the Star Wars universe using the Star Wars API (https://swapi.dev/documentation). Show the name of the oldest person and list the titles of all the films they appeared.

![Pelican](../images/2.png)


```python
import requests
import os
import pandas as pd
import numpy as np
```

## First step is to check the info of the data


```python
base_url = 'http://swapi.dev/api/'
resp = requests.get(os.path.join(base_url, 'people'))
data = resp.json()
data["count"]
```




    82



We found that there are 82 Star Wars characters in the dataset, but find there may some missing vaules in the dataset.


```python
people1 = requests.get('https://swapi.dev/api/people/1')
print(people1.status_code)
people3 = requests.get('https://swapi.dev/api/people/3')
print(people3.status_code)
```

    200
    200


For a successful request, the status code is 200,and we can find which is not 200 to find out the unsuccessful request and  also use  loop to append each successful record .


```python
people = []
i = 1
while len(people) < 82:
    data = requests.get('http://swapi.dev/api/people/{}/'.format(i))
    if data.status_code == 200:
        people.append(data.json())
    else:
        pass
    i+=1
```

## Then convert this to a panda Dataframe


```python
df = pd.DataFrame(people)
```


```python
df.head()
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
      <th>name</th>
      <th>height</th>
      <th>mass</th>
      <th>hair_color</th>
      <th>skin_color</th>
      <th>eye_color</th>
      <th>birth_year</th>
      <th>gender</th>
      <th>homeworld</th>
      <th>films</th>
      <th>species</th>
      <th>vehicles</th>
      <th>starships</th>
      <th>created</th>
      <th>edited</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Luke Skywalker</td>
      <td>172</td>
      <td>77</td>
      <td>blond</td>
      <td>fair</td>
      <td>blue</td>
      <td>19BBY</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/vehicles/14/, http://swa...</td>
      <td>[http://swapi.dev/api/starships/12/, http://sw...</td>
      <td>2014-12-09T13:50:51.644000Z</td>
      <td>2014-12-20T21:17:56.891000Z</td>
      <td>http://swapi.dev/api/people/1/</td>
    </tr>
    <tr>
      <th>1</th>
      <td>C-3PO</td>
      <td>167</td>
      <td>75</td>
      <td>n/a</td>
      <td>gold</td>
      <td>yellow</td>
      <td>112BBY</td>
      <td>n/a</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[http://swapi.dev/api/species/2/]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-10T15:10:51.357000Z</td>
      <td>2014-12-20T21:17:50.309000Z</td>
      <td>http://swapi.dev/api/people/2/</td>
    </tr>
    <tr>
      <th>2</th>
      <td>R2-D2</td>
      <td>96</td>
      <td>32</td>
      <td>n/a</td>
      <td>white, blue</td>
      <td>red</td>
      <td>33BBY</td>
      <td>n/a</td>
      <td>http://swapi.dev/api/planets/8/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[http://swapi.dev/api/species/2/]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-10T15:11:50.376000Z</td>
      <td>2014-12-20T21:17:50.311000Z</td>
      <td>http://swapi.dev/api/people/3/</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Darth Vader</td>
      <td>202</td>
      <td>136</td>
      <td>none</td>
      <td>white</td>
      <td>yellow</td>
      <td>41.9BBY</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[]</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/starships/13/]</td>
      <td>2014-12-10T15:18:20.704000Z</td>
      <td>2014-12-20T21:17:50.313000Z</td>
      <td>http://swapi.dev/api/people/4/</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Leia Organa</td>
      <td>150</td>
      <td>49</td>
      <td>brown</td>
      <td>light</td>
      <td>brown</td>
      <td>19BBY</td>
      <td>female</td>
      <td>http://swapi.dev/api/planets/2/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/vehicles/30/]</td>
      <td>[]</td>
      <td>2014-12-10T15:20:09.791000Z</td>
      <td>2014-12-20T21:17:50.315000Z</td>
      <td>http://swapi.dev/api/people/5/</td>
    </tr>
  </tbody>
</table>
</div>




```python
np.shape(df)
```




    (82, 16)



So finish downloading all 82 people in the Star Wars universe using the Star Wars API

## Next step :Show the name of the oldest person and list the titles of all the films they appeared.

Use the birth year to show who is the oldest,we found in people description that The birth year of the person, using the in-universe standard of BBY or ABY - Before the Battle of Yavin or After the Battle of Yavin. So if we want to find the oldest people, we have to find the people with BBY and largest number.

first deal with na


```python
df["birth_year"] = df["birth_year"].fillna("unknown")
```

extract people with birth year contain BBY , and we have to find a way to find the largest number with BBY. If we delete BBY and only count the number would be easier


```python
BBY = df[df["birth_year"].str.contains("BBY")]
BBY.head()
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
      <th>name</th>
      <th>height</th>
      <th>mass</th>
      <th>hair_color</th>
      <th>skin_color</th>
      <th>eye_color</th>
      <th>birth_year</th>
      <th>gender</th>
      <th>homeworld</th>
      <th>films</th>
      <th>species</th>
      <th>vehicles</th>
      <th>starships</th>
      <th>created</th>
      <th>edited</th>
      <th>url</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>Luke Skywalker</td>
      <td>172</td>
      <td>77</td>
      <td>blond</td>
      <td>fair</td>
      <td>blue</td>
      <td>19BBY</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/vehicles/14/, http://swa...</td>
      <td>[http://swapi.dev/api/starships/12/, http://sw...</td>
      <td>2014-12-09T13:50:51.644000Z</td>
      <td>2014-12-20T21:17:56.891000Z</td>
      <td>http://swapi.dev/api/people/1/</td>
    </tr>
    <tr>
      <th>1</th>
      <td>C-3PO</td>
      <td>167</td>
      <td>75</td>
      <td>n/a</td>
      <td>gold</td>
      <td>yellow</td>
      <td>112BBY</td>
      <td>n/a</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[http://swapi.dev/api/species/2/]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-10T15:10:51.357000Z</td>
      <td>2014-12-20T21:17:50.309000Z</td>
      <td>http://swapi.dev/api/people/2/</td>
    </tr>
    <tr>
      <th>2</th>
      <td>R2-D2</td>
      <td>96</td>
      <td>32</td>
      <td>n/a</td>
      <td>white, blue</td>
      <td>red</td>
      <td>33BBY</td>
      <td>n/a</td>
      <td>http://swapi.dev/api/planets/8/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[http://swapi.dev/api/species/2/]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-10T15:11:50.376000Z</td>
      <td>2014-12-20T21:17:50.311000Z</td>
      <td>http://swapi.dev/api/people/3/</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Darth Vader</td>
      <td>202</td>
      <td>136</td>
      <td>none</td>
      <td>white</td>
      <td>yellow</td>
      <td>41.9BBY</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/1/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[]</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/starships/13/]</td>
      <td>2014-12-10T15:18:20.704000Z</td>
      <td>2014-12-20T21:17:50.313000Z</td>
      <td>http://swapi.dev/api/people/4/</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Leia Organa</td>
      <td>150</td>
      <td>49</td>
      <td>brown</td>
      <td>light</td>
      <td>brown</td>
      <td>19BBY</td>
      <td>female</td>
      <td>http://swapi.dev/api/planets/2/</td>
      <td>[http://swapi.dev/api/films/1/, http://swapi.d...</td>
      <td>[]</td>
      <td>[http://swapi.dev/api/vehicles/30/]</td>
      <td>[]</td>
      <td>2014-12-10T15:20:09.791000Z</td>
      <td>2014-12-20T21:17:50.315000Z</td>
      <td>http://swapi.dev/api/people/5/</td>
    </tr>
  </tbody>
</table>
</div>




```python
BBY.loc[:,"birth_year"] = BBY['birth_year'].str.replace('BBY', '').astype('float')
```



```python
BBY.sort_values('birth_year', ascending = False).head(1)
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
      <th>name</th>
      <th>height</th>
      <th>mass</th>
      <th>hair_color</th>
      <th>skin_color</th>
      <th>eye_color</th>
      <th>birth_year</th>
      <th>gender</th>
      <th>homeworld</th>
      <th>films</th>
      <th>species</th>
      <th>vehicles</th>
      <th>starships</th>
      <th>created</th>
      <th>edited</th>
      <th>url</th>
      <th>yearnum</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>18</th>
      <td>Yoda</td>
      <td>66</td>
      <td>17</td>
      <td>white</td>
      <td>green</td>
      <td>brown</td>
      <td>896.0</td>
      <td>male</td>
      <td>http://swapi.dev/api/planets/28/</td>
      <td>[http://swapi.dev/api/films/2/, http://swapi.d...</td>
      <td>[http://swapi.dev/api/species/6/]</td>
      <td>[]</td>
      <td>[]</td>
      <td>2014-12-15T12:26:01.042000Z</td>
      <td>2014-12-20T21:17:50.345000Z</td>
      <td>http://swapi.dev/api/people/20/</td>
      <td>896.0</td>
    </tr>
  </tbody>
</table>
</div>



## So Yoda is the oldest people

## Next step is find the titles of all the films Yoda appeared.

we can look into the film info by request the film api in the dataframe


```python
yoda = BBY_sorted.iloc[0,:].films
```


```python
film_url = [requests.get(film).json() for film in yoda]
films = pd.DataFrame(film_url)
```


```python
films.title
```




    0    The Empire Strikes Back
    1         Return of the Jedi
    2         The Phantom Menace
    3       Attack of the Clones
    4        Revenge of the Sith
    Name: title, dtype: object



Above is the list of film titles that Yoda appears in.
