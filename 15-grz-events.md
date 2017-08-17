### 15. About events


##### 1. How many apprenticeships have which duration (in number of months)?
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

##### 2. What is the average duration of an apprenticeship?
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

##### 3. What is the average duration of an apprenticeship per profession category ?
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

##### 3. What is the average duration of a contract in profession category X per year?
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
