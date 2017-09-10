### 15. About events

##### 1. How many event mentions of which type are there ? (api:15_event_01_nb_event_per_type)
```sparql
#+ tags:
#+   - events

SELECT ?EventType (COUNT (distinct ?e) AS ?NbEvents)
WHERE 
{ ?e a common:EventMention ; sem:eventType ?EventType .}
GROUP BY ?EventType
```

##### 2. How many apprenticeships have which duration (in number of months)? (api:15_event_02_distrib_appship_per_duration)
```sparql
#+ tags:
#+   - events

SELECT ?DurationMonth (COUNT (distinct?appship) AS ?NbAppShip)
WHERE 
{
 ?appship sem:eventType grz-owl:Apprenticeship ; common:duration  ?dur . 
 BIND(xsd:integer(?dur) AS ?DurationMonth)
} 
GROUP BY ?durInt
ORDER BY ?durInt
```

##### 3. What is the average duration of an apprenticeship? (api:15_event_03_appship_avg_duration)
```sparql
#+ tags:
#+   - events
# N.B.: excluding weird durations

SELECT (AVG (?durInt) AS ?AvgAppshipDuration )
WHERE 
{
  ?appship sem:eventType grz-owl:Apprenticeship ; common:duration  ?dur . 
  BIND(xsd:integer(?dur) AS ?durInt)
  FILTER (( ?durInt < 300 ) )
} 
```

##### 4. What is the average duration of an apprenticeship per profession category ? (api:15_event_04_appship_avg_duration_profCat)
```sparql
#+ tags:
#+   - events

SELECT ?profCat (AVG (?durInt) AS ?AvgAppshipDuration )
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

##### 5. What is the average duration of a contract in profession category X per year? (api:15_event_05_appship_avg_duration_profCatX)
```sparql
#+ tags:
#+   - events
#+ params: ?_profCategory

SELECT ?year (AVG (?durInt) AS ?AppshipDurationAvg)
WHERE 
{
  ?appship sem:eventType grz-owl:Apprenticeship ; common:duration  ?dur ; core:isMentionedIn ?contract .
  ?contract sem:hasTimeStamp ?date ; core:hasMention ?pm.
  ?pm grz-owl:hasRole grz-owl:Master ; grz-owl:hasProfession ?p . ?p grz-owl:professionCategory 'mercante' .

  BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate) 
  BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).

  BIND(xsd:integer(?dur) AS ?durInt)
  FILTER (( ?durInt < 300 ) )
}
GROUP BY ?year
ORDER BY ASC(?year)
```

##### 6. How many flees are there, with and without denunciation date? (api:15_event_06_nb_flee_with_wo_denun)
```sparql
#+ tags:
#+   - events

SELECT ?FleeWithDenunciationDate ?FleeWithoutDenunciationDate
WHERE 
{
 {SELECT (COUNT (distinct ?e)  AS ?FleeWithDenunciationDate )
 WHERE {?e sem:eventType grz-owl:Flee ; grz-owl:denunciationDate ?denunDate .}}

 {SELECT (COUNT (distinct ?e)  AS ?FleeWithoutDenunciationDate )
 WHERE {?e sem:eventType grz-owl:Flee . FILTER( NOT EXISTS { ?e grz-owl:denunciationDate ?denunDate } )}}
}
```

##### 7. How many flees are there per year ? (api:15_event_07_nb_flee_perYear)
```sparql
#+ tags:
#+   - events
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

##### 8. How many breaches of contract are there per year ? (api:15_event_08_nb_breaches_perYear)
```sparql
#+ tags:
#+   - events
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

##### 9. On which days contracts are registered? (api:15_event_09_day_contract)
```sparql
#+ tags:
#+   - events

SELECT ?day (COUNT (distinct ?c)  AS ?NbContracts)
WHERE 
{ ?c a grz-owl:Contract; time:dayOfWeek ?day }
GROUP BY ?day
ORDER BY ASC(?day)
```

