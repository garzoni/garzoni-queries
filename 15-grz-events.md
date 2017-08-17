### 15. About events

##### 1. How many events of which type are there ?
```sparql
SELECT ?EventType COUNT (distinct ?e) AS ?NbEvents
WHERE 
{
 ?e sem:eventType ?EventType .
}
GROUP BY ?EventType
```

##### 2. How many apprenticeships have which duration (in number of months)?
```sparql
SELECT ?dur COUNT (distinct?appship)
WHERE 
{
?appship sem:eventType grz-owl:Apprenticeship ; common:duration  ?dur . 
BIND(xsd:integer(?dur) AS ?durInt)
} 
GROUP BY ?dur
ORDER BY ?dur
```

##### 3. What is the average duration of an apprenticeship?
N.B.: excluding weird durations
```sparql
SELECT AVG (?durInt)
WHERE 
{
?appship sem:eventType grz-owl:Apprenticeship ; common:duration  ?dur . 
BIND(xsd:integer(?dur) AS ?durInt)
 FILTER (( ?durInt < 300 ) )
} 
```

##### 4. What is the average duration of an apprenticeship per profession category ?
```sparql
SELECT ?profCat AVG (?durInt)
WHERE 
{
  ?appship sem:eventType grz-owl:Apprenticeship ; common:duration  ?dur ; core:isMentionedIn ?contract .
  ?contract sem:hasTimeStamp ?date ; core:hasMention ?pm.
  ?pm grz-owl:hasRole grz-owl:Master ; grz-owl:hasProfession/grz-owl:professionCategory ?profCat .
  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(xsd:integer(?dur) AS ?durInt)
  FILTER (( ?durInt < 300 ) )
}
GROUP BY ?profCat
ORDER BY DESC(AVG (?durInt))
```

##### 5. What is the average duration of a contract in profession category X per year?
SELECT ?year (AVG (?durInt) AS ?AppshipDurationAvg)
WHERE 
{
  ?appship sem:eventType grz-owl:Apprenticeship ; common:duration  ?dur ; core:isMentionedIn ?contract .
  ?contract sem:hasTimeStamp ?date ; core:hasMention ?pm.
  ?pm grz-owl:hasRole grz-owl:Master ; grz-owl:hasProfession/grz-owl:professionCategory 'mercante' .

  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).

  BIND(xsd:integer(?dur) AS ?durInt)
  FILTER (( ?durInt < 300 ) )
}
GROUP BY ?year
ORDER BY ASC(?year)

##### 6. How many flees are there in total ?
```sparql
SELECT COUNT (distinct ?e) AS ?NbFlees
WHERE 
{?e sem:eventType grz-owl:Flee .}
```

###### With and without denunciation date
```sparql
SELECT ?FleeWithDenunciationDate ?FleeWithoutDenunciationDate
WHERE 
{
 {SELECT (COUNT (distinct ?e)  AS ?FleeWithDenunciationDate )
 WHERE {?e sem:eventType grz-owl:Flee ; grz-owl:denunciationDate ?denunDate .}}

 {SELECT (COUNT (distinct ?e)  AS ?FleeWithoutDenunciationDate )
 WHERE {?e sem:eventType grz-owl:Flee . FILTER( NOT EXISTS { ?e grz-owl:denunciationDate ?denunDate } )}}
}
```

##### 7. How many flees are there per year ?
```sparql
# PB WITH DATE
SELECT ?year (COUNT (distinct ?e)  AS ?NbFlees)
WHERE 
{
 ?e sem:eventType grz-owl:Flee ; grz-owl:denunciationDate ?denunDate .
 BIND(IF(?denunDate = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?denunDate) ) AS ?myDate)  
 BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
}
GROUP BY ?year
```


##### 7. How many breaches of contract are there per year ?
```sparql
# PB WITH DATE
SELECT ?year (COUNT (distinct ?e)  AS ?NbFlees)
WHERE 
{
 ?e sem:eventType grz-owl:BreachOfContract ; sem:hasEndTimeStamp ?date .
 BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate)  
 BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
}
GROUP BY ?year
```
