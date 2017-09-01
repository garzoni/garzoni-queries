#### 06. About Masters

##### 01. What is the total number of masters? (between date X and date Y? or before/after date X)
Please see question 1 in file 05-grz-apprentices.md and replace grz-owl:Apprentice by grz-owl:Master.

##### 02. How many masters have more than x apprentice? (api:06_masters_02_nb_masters_with_several_app)
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT COUNT distinct ?master 
WHERE  {  ?master a common:Person; common:hasApprentice ?appStatement . }
GROUP BY ?master 
HAVING (COUNT (distinct ?appStatement) > 1)
```

##### 03. Get the list of masters having more than x apprentice. (api:06_masters_03_masters_with_several_app)
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?master ?masterName COUNT (distinct ?appStatement) AS ?numberApp
WHERE  {  ?master a common:Person; common:hasApprentice ?appStatement ; rdfs:label ?masterName }
GROUP BY ?master ?masterName
HAVING (COUNT (distinct ?appStatement) > 1)
ORDER BY DESC(?numberApp)
```

##### 04. Get the list of masters having more than x apprentice, with time window  (api:06_masters_04_nb_masters_with_several_app_withTW)
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?master ?masterName COUNT (distinct ?appStatement) AS ?numberApp
WHERE  
{  
  ?master a common:Person; common:hasApprentice ?appStatement ; rdfs:label ?masterName .
  ?appStatement sem:hasBeginTimeStamp ?date .
  FILTER (year(?date) > 1600 AND year(?date) < 1610)
}
GROUP BY ?master ?masterName
HAVING (COUNT (distinct ?appStatement) > 1)
ORDER BY DESC(?numberApp)
```

##### 05. How many apprentice do masters have on average in their careers? (api:06_masters_05_avg_nbApp_in_master_careers)
```sparql
SELECT AVG (?numberApp)
WHERE
{
  SELECT ?master COUNT (distinct ?appStatement) AS ?numberApp
  WHERE {?master a common:Person; common:hasApprentice ?appStatement }
  GROUP BY ?master
}
```

###### To consider only masters with more than 1 app, add:   

```sparql
GROUP BY ?master   
HAVING (COUNT (distinct ?appStatement) > 1)
```

##### 07. How many apprentice do masters have on average in their careers, with time window and given a certain profession category? (api:06_masters_05_avg_nbApp_in_master_careers_with_prof_x_withTW)

```sparql
?appStatement sem:hasBeginTimeStamp ?date .
FILTER (year(?date) > 1600 AND year(?date) < 1740)
```

###### TO UPDATE WHEN PROF THESAURUS READY - according to profession category 
```sparql
SELECT AVG (?numberApp)
WHERE
{
  SELECT ?master (COUNT (distinct ?stmt) AS ?numberApp)
  WHERE 
  {
    ?master grz-owl:hasApprentice ?stmt .
    ?master grz-owl:hasProfession/rdf:value ?prof .
    ?prof grz-owl:professionCategory "stampa" .
    ?master grz-owl:hasProfession/rdf:value ?prof .
  }
  GROUP BY ?master
}
```

#####  TO UPDATE Distribution of number of apprentice(s) per master given a time windows and a profession category: (half ok, param implem)
```sparql
SELECT  ?numberApp COUNT (distinct ?master)
WHERE
{
  SELECT ?master ?numberApp
  WHERE
  {
    SELECT ?master (COUNT (distinct ?app) AS ?numberApp)
    WHERE
    {
      ?master a grz-owl:Person . 
      ?master grz-owl:hasApprentice ?appStatement .
      ?appStatement rdf:value ?app .
      ?appStatement sem:hasBeginTimeStamp ?date .
      ?master grz-owl:hasProfession/rdf:value ?lexEntryProf .
      ?lexEntryProf grz-owl:professionCategory "stampa" .
      FILTER (year(?date) > 1600 AND year(?date) < 1640)
    }
    GROUP BY ?master
  }
GROUP BY ?numberApp
}
ORDER BY ASC (?numberApp)
```

##### 08. How many masters have how many apprentices? (api:06_masters_08_nbApp_per_master)
``` sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#> 

SELECT  ?numberApp ( COUNT (distinct ?master) AS ?NbMasters)
WHERE
{
  SELECT ?master ?numberApp
  WHERE
  {
    SELECT ?master COUNT (distinct ?appStatement) AS ?numberApp
    WHERE {?master a common:Person; common:hasApprentice ?appStatement }
    GROUP BY ?master
  }
  GROUP BY ?numberApp
}
ORDER BY ASC (?numberApp)
```

##### 09. How many masters have how many apprentices, with time window ? (api:06_masters_09_nbApp_per_master_withTW)
``` sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#> 

SELECT  ?numberApp ( COUNT (distinct ?master) AS ?NbMasters)
WHERE
{
  SELECT ?master ?numberApp
  WHERE
  {
    SELECT ?master COUNT (distinct ?appStatement) AS ?numberApp
    WHERE {
      ?master a common:Person; common:hasApprentice ?appStatement. 
      ? appStatement sem:hasBeginTimeStamp ?date .
      FILTER (year(?date) > 1600 AND year(?date) < 1700) }
      GROUP BY ?master
  }
  GROUP BY ?numberApp
}
ORDER BY ASC (?numberApp)
```

##### 11. Given a master with URL x, give the timeline of his students' enrolment. (api:06_masters_11_app_timeline_for_master_with_URL_x)
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?app ?date
WHERE
{
  <http://data.dhlab.epfl.ch/garzoni/490933da-c839-4942-8f13-98e069a022f5-personEntity> common:hasApprentice ?appStatement .
  ?appStatement rdf:value ?app .
  ?appStatement sem:hasBeginTimeStamp ?date .
}
GROUP BY ?app
ORDER BY ASC(?date)
```

##### 12. Given a master with name x, give the timeline of his students' enrolment. (api:06_masters_12_app_timeline_for_master_with_name_x)
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?app ?date
WHERE
{
  ?m a common:Person ; rdfs:label 'Guido di Quinti'@ita ; common:hasApprentice ?appStatement .
  ?appStatement rdf:value ?app .
  ?appStatement sem:hasBeginTimeStamp ?date .
}
GROUP BY ?app
ORDER BY ASC(?date)
```


