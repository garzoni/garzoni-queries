#### 06. About Masters

##### 1. What is the total number of masters? (between date X and date Y? or before/after date X)
Please see question 1 in file 05-grz-apprentices.md and replace grz-owl:Apprentice by grz-owl:Master.

##### 2. How many masters have more than x apprentice?
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT COUNT distinct ?master 
WHERE  {  ?master a common:Person; common:hasApprentice ?appStatement . }
GROUP BY ?master 
HAVING (COUNT (distinct ?appStatement) > 1)
```

##### 3. Get the list of masters having more than x apprentice.
```sparql
PREFIX core: <http://vocab.dhlab.epfl.ch/data-core#>
PREFIX common: <http://vocab.dhlab.epfl.ch/data-common#>

SELECT ?master ?masterName COUNT (distinct ?appStatement) AS ?numberApp
WHERE  {  ?master a common:Person; common:hasApprentice ?appStatement ; rdfs:label ?masterName }
GROUP BY ?master ?masterName
HAVING (COUNT (distinct ?appStatement) > 1)
ORDER BY DESC(?numberApp)
```
###### With a time window 
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

##### 4. How many apprentice do masters have on average in their careers?
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

###### With a time window, add:   

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

##### 5. How many masters have how many apprentices?
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

###### To add a time window, add this in the innermost SELECT:

```
? appStatement sem:hasBeginTimeStamp ?date .
FILTER (year(?date) > 1600 AND year(?date) < 1700)
```

###### TODO: add filter by profession category


##### 6. Given a master, give the timeline of his students' enrolement.
###### with master's URL
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

###### with master's name
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


