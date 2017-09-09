#### 06. About Masters

##### 01. What is the total number of masters? (api:06_masters_01_nb_master_entities)
```sparql
SELECT (COUNT(distinct ?pe) AS ?NbMasterEntity)
WHERE
{ ?pe a common:Person ; grz-owl:hasRole ?r . ?r rdf:value grz-owl:Master .}
```

##### 02. How many masters have more than x apprentice? (api:06_masters_02_nb_masters_with_several_app)
```sparql
#+ tags:
#+   - masters

SELECT (COUNT (distinct ?master) AS ?NbMaster)
WHERE  {  ?master a common:Person; common:hasApprentice ?appStatement . }
GROUP BY ?master
HAVING(?NbMaster > 1)
```

##### 03. Get the list of masters having more than x apprentice. (api:06_masters_03_masters_with_several_app)
```sparql
#+ tags:
#+   - masters

SELECT ?master ?masterName (COUNT (distinct ?appStatement) AS ?numberApp)
WHERE  {  ?master a common:Person; common:hasApprentice ?appStatement ; rdfs:label ?masterName }
GROUP BY ?master ?masterName
HAVING(?numberApp > 1)
ORDER BY DESC(?numberApp)
```

##### 04. Get the list of masters having more than x apprentice, with time window  (api:06_masters_04_nb_masters_with_several_app_withTW)
```sparql
#+ tags:
#+   - masters
#+ params:  ?_date_start ?_date_end

SELECT ?master ?masterName (COUNT (distinct ?appStatement) AS ?numberApp)
WHERE
{
  ?master a common:Person; common:hasApprentice ?appStatement ; rdfs:label ?masterName .
  ?appStatement sem:hasBeginTimeStamp ?date .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate)
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year)
  FILTER (?year > ?_date_start)
  FILTER (?year < ?_date_end)
}
GROUP BY ?master ?masterName
HAVING(?numberApp > 1)
ORDER BY DESC(?numberApp)
```

##### 05. How many apprentice do masters have on average in their careers? (api:06_masters_05_avg_nbApp_in_master_careers)
```sparql
#+ tags:
#+   - masters

SELECT (AVG (?numberApp) AS ?AvgNbApp)
WHERE
{
  SELECT ?master (COUNT (distinct ?appStatement) AS ?numberApp)
  WHERE {?master a common:Person; common:hasApprentice ?appStatement }
  GROUP BY ?master
}
```

##### 06. How many apprentice do masters with more than one apprentice have on average in their careers? (api:06_masters_06_avg_nbApp_in_master_careers_more_app)
```sparql
#+ tags:
#+   - masters

SELECT (AVG (?numberApp) AS ?AvgNbApp)
WHERE
{
  SELECT ?master (COUNT (distinct ?appStatement) AS ?numberApp)
  WHERE {?master a common:Person; common:hasApprentice ?appStatement }
  GROUP BY ?master
  HAVING(?numberApp > 1)
}
```

##### 07. How many apprentice do masters have on average in their careers, with time window and given a certain profession category? (api:06_masters_07_avg_nbApp_in_master_careers_with_prof_x_withTW)

```sparql
#+ tags:
#+   - masters
#+ params:  ?_date_start ?_date_end ?_prof_cat

SELECT (AVG (?numberApp) AS ?AvgNbApp)
WHERE
{
  {SELECT ?master (COUNT (distinct ?appStatement) AS ?numberApp)
  WHERE {
    ?master a common:Person; common:hasApprentice ?appStatement .
    ?appStatement sem:hasBeginTimeStamp ?date .
    BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate)
    BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year)
    ?master grz-owl:hasProfession ?profStatement .
    ?profStatement rdf:value ?prof .
    ?prof grz-owl:professionCategory "stampa" .
    FILTER (?year > ?_date_start)
    FILTER (?year < ?_date_end)
    }
  GROUP BY ?master}
}
```

##### 08. How many masters have how many apprentices? (api:06_masters_08_nbApp_per_master)
```sparql
#+ tags:
#+   - masters

SELECT  ?numberApp ( COUNT (distinct ?master) AS ?NbMasters)
WHERE
{
  SELECT ?master ?numberApp
  WHERE
  {
    SELECT ?master (COUNT (distinct ?appStatement) AS ?numberApp)
    WHERE {?master a common:Person; common:hasApprentice ?appStatement }
    GROUP BY ?master
  }
  GROUP BY ?numberApp
}
ORDER BY ASC (?numberApp)
```

##### 09. How many masters have how many apprentices, with time window ? (api:06_masters_09_nbApp_per_master_withTW)
```sparql
#+ tags:
#+   - masters
#+ params:  ?_date_start ?_date_end

SELECT  ?numberApp ( COUNT (distinct ?master) AS ?NbMasters)
WHERE
{
  SELECT ?master ?numberApp
  WHERE
  {
    SELECT ?master (COUNT (distinct ?appStatement) AS ?numberApp)
    WHERE {
      ?master a common:Person; common:hasApprentice ?appStatement.
      ?appStatement sem:hasBeginTimeStamp ?date .
      FILTER (year(?date) > 1600)
      FILTER (year(?date) < 1700)
      }
      GROUP BY ?master
  }
  GROUP BY ?numberApp
}
ORDER BY ASC (?numberApp)
```

##### 10. Given a master with URL x, give the timeline of his students' enrolment. (api:06_masters_10_app_timeline_for_master_with_URL_x)
```sparql
#+ tags:
#+   - masters

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

##### 11. Given a master with name x, give the timeline of his students' enrolment. (api:06_masters_11_app_timeline_for_master_with_name_x)
```sparql
#+ tags:
#+   - masters

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


