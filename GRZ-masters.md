### Garzoni People Search : about Masters

##### Get masters having more than 1 apprentice
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?master
WHERE  {  ?master a common:Person; common:hasApprentice ?appStatement . }
GROUP BY ?master
HAVING (COUNT (distinct ?appStatement) > 1)
```

##### How many master have more than 1,2,n apprentice
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT count distinct (?master)
WHERE
{
  SELECT ?master COUNT (distinct ?appStatement) 
  WHERE {?master a common:Person; common:hasApprentice ?appStatement }
  GROUP BY ?master
  HAVING (COUNT (distinct ?appStatement) > 1)
}

```

##### Get number of apprentices per masters (if > 1)
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?master ?numberApp
WHERE
{
  SELECT ?master COUNT (distinct ?appStatement) AS ?numberApp
  WHERE {?master a common:Person; common:hasApprentice ?appStatement }
  GROUP BY ?master
  HAVING (COUNT (distinct ?appStatement) > 1)
}
ORDER BY DESC(?numberApp)
```

##### How many apprentice do masters have on average in their careers?
```sparql
SELECT AVG (?numberApp)
WHERE
{
  SELECT ?master COUNT (distinct ?appStatement) AS ?numberApp
  WHERE {?master a common:Person; common:hasApprentice ?appStatement }
  GROUP BY ?master
}
```

To consider only masters with more than 1 app, add:   

```sparql
GROUP BY ?master   
HAVING (COUNT (distinct ?appStatement) > 1)`
```

With a time window, add:   

```sparql
?appStatement sem:hasBeginTimeStamp ?date .
FILTER (year(?date) > 1600 AND year(?date) < 1740)
```

##### TO UPDATE How many apprentice do masters have on average in their “careers”  according to profession category => histogram
```sparql
SELECT AVG (?numberApp)
WHERE
{
  SELECT ?master (COUNT (distinct ?stmt) AS ?numberApp)
  WHERE 
  {
    ?master grz-owl:has_apprentice ?stmt .
    ?master grz-owl:profession/grz-owl:value ?prof .
    ?prof grz-owl:professionCategory "stampa" .
    ?master grz-owl:profession/grz-owl:value ?prof .
  }
  GROUP BY ?master
}
```

#####  How many masters have how many apprentices?
``` sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#> 

SELECT  ?numberApp COUNT (distinct ?master)
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

To add a time window, add this in the innermost select:

```
? appStatement sem:hasBeginTimeStamp ?date .
FILTER (year(?date) > 1600 AND year(?date) < 1700)
```

TODO: add filter by profession category

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
      ?master grz-owl:has_apprentice ?appStatement .
      ?appStatement grz-owl:value ?app .
      ?appStatement sem:hasBeginTimeStamp ?date .
      ?master grz-owl:profession ?professionStatement .
      ?professionStatement grz-owl:value ?lexEntryProf .
      ?lexEntryProf grz-owl:professionCategory "stampa" .
      FILTER (year(?date) > 1600 AND year(?date) < 1640)
    }
    GROUP BY ?master
  }
GROUP BY ?numberApp
}
ORDER BY ASC (?numberApp)
```

##### Given a master, give the timeline of his students' enrolement
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?app ?date
WHERE
{
  <http://localhost:8080/garzoni-data-dhc/490933da-c839-4942-8f13-98e069a022f5-personEntity> common:hasApprentice ?appStatement .
  ?appStatement rdf:value ?app .
  ?appStatement sem:hasBeginTimeStamp ?date .
}
GROUP BY ?app
ORDER BY ASC(?date) 
```


