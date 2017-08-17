### About workshops

##### 1. How many workshops are there in the DB? (= mentions of)
```sparql
SELECT COUNT (distinct ?ws) 
WHERE { ?ws a grz-owl:WorkshopMention } 
```

##### 2. Who has a workshop?
```sparql
SELECT ?role (COUNT (distinct ?pm) AS ?PersonMentionWithWorkshop)
WHERE 
{?pm a common:PersonMention ; grz-owl:hasRole ?role; grz-owl:hasWorkshop ?ws . 
 ?ws a grz-owl:WorkshopMention
}
GROUP BY ?role 
ORDER BY DESC (COUNT (distinct ?pm))
 
  ```

##### 3. How many masters are associated with a workshop or *not*?
```sparql
SELECT ?MasterWithWorkshop ?MasterWithoutWorkshop
WHERE 
{ 
  {SELECT (COUNT (distinct ?m) AS ?MasterWithWorkshop)
  WHERE 
  {?m a common:PersonMention ; grz-owl:hasRole grz-owl:Master ; grz-owl:hasWorkshop ?ws . 
   ?ws a grz-owl:WorkshopMention} 
  }
  
  {SELECT (COUNT (distinct ?m) AS ?MasterWithoutWorkshop)
  WHERE 
  {?m a common:PersonMention ; grz-owl:hasRole grz-owl:Master .
  FILTER (NOT EXISTS{ ?m grz-owl:hasWorkshop ?ws . })}
  }
} 
```

##### 4. How many workshops have and have *not* the information of their places?
```spaqrl
SELECT ?WorkshopWithPlaceInfo ?WorkshopWithoutPlaceInfo
WHERE 
{ 
  {SELECT COUNT (distinct ?ws) AS ?WorkshopWithPlaceInfo 
  WHERE {?ws a grz-owl:WorkshopMention ; grz-owl:hasLocation ?pl} 
  }
  
  {SELECT COUNT (distinct ?ws) AS ?WorkshopWithoutPlaceInfo 
  WHERE {?ws a grz-owl:WorkshopMention . FILTER (NOT EXISTS{?ws grz-owl:hasLocation ?pl} ) }
  }
}
```

###### with time window filter
```sparql
SELECT ?WorkshopWithPlaceInfo ?WorkshopWithoutPlaceInfo
WHERE 
{ 
  {SELECT COUNT (distinct ?ws) AS ?WorkshopWithPlaceInfo 
  WHERE 
  {
      ?ws a grz-owl:WorkshopMention ; grz-owl:hasLocation ?pl ; ^grz-owl:hasWorkshop ?master.
      ?master core:isMentionedIn/sem:hasTimeStamp ?myDate .
      BIND(IF(?myDate = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?myDate) ) AS ?date)
      BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).   
      FILTER (?year > 1550 AND ?year < 1598)
  } 
  }
  
  {SELECT COUNT (distinct ?ws) AS ?WorkshopWithoutPlaceInfo 
  WHERE 
  {
    ?ws a grz-owl:WorkshopMention ; ^grz-owl:hasWorkshop ?master.
    ?master core:isMentionedIn/sem:hasTimeStamp ?myDate .
    BIND(IF(?myDate = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?myDate) ) AS ?date)
    BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
    FILTER (?year > 1550 AND ?year < 1598)
    FILTER (NOT EXISTS{?ws grz-owl:hasLocation ?pl} ) }
  }
}
```

##### 5. Give me all workshop mentions, their location information, their insigna and the year they were mentioned.
```sparql
# to filter by date add:  FILTER (?myDate < "1653-03-15"^^xsd:dateTime AND ?myDate > "1630-03-15"^^xsd:dateTime)
SELECT ?workshop  (STR(?i) AS ?insigna) ?locTranscript ?locStandard STR(?labelParish) AS ?parishLabel STR(?labelSestiere) AS ?pestiereLabel ?date
WHERE 
{
  ?pl a common:PlaceMention . 
  ?workshop a grz-owl:WorkshopMention ; grz-owl:hasLocation ?pl  ; ^grz-owl:hasWorkshop ?master .
  OPTIONAL { ?pl common:transcript ?locTranscript ; common:standardForm ?locStandard}
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere/rdfs:label ?labelSestiere .}
  OPTIONAL { ?workshop grz-owl:insigna ?i }
  ?master core:isMentionedIn ?contract .
  ?contract sem:hasTimeStamp ?myDate .
  BIND(IF(?myDate = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?myDate) ) AS ?date) 
} 
ORDER BY ASC(?locTranscript)
```

##### 6. What is the distribution of workshops per parishes? per sestiere ?

###### parishes
```sparql
SELECT ?labelParish (COUNT (distinct ?workshop) AS ?NbWorkshop) STR(?labelSestiere) AS ?labelSestiere
WHERE
{
  ?workshop a grz-owl:WorkshopMention .
  ?workshop grz-owl:hasLocation/common:inParish ?parish . 
  ?parish rdfs:label ?labelParish ; common:inSestiere/rdfs:label ?labelSestiere .
}
GROUP BY ?labelParish ?labelSestiere
ORDER BY DESC (COUNT (distinct ?workshop))
```

###### sestiere
```sparql
SELECT STR(?labelSestiere) AS ?labelSestiere  (COUNT (distinct ?workshop) AS ?NbWorkshop)
WHERE
{
  ?workshop a grz-owl:WorkshopMention .
  ?workshop grz-owl:hasLocation/common:inSestiere/rdfs:label ?labelSestiere .
}
GROUP BY  ?labelSestiere
ORDER BY DESC (COUNT (distinct ?workshop))
```

##### 7. How many workshop have the indication if an insigna?
SELECT ?WorkshopWithInsigna ?WorkshopWithoutInsigna
WHERE 
{ 
  {SELECT COUNT (distinct ?ws) AS ?WorkshopWithInsigna 
  WHERE {?ws a grz-owl:WorkshopMention ; grz-owl:insigna ?i} 
  }
  
  {SELECT COUNT (distinct ?ws) AS ?WorkshopWithoutInsigna 
  WHERE {?ws a grz-owl:WorkshopMention . FILTER (NOT EXISTS{?ws grz-owl:insigna ?i} ) }
  }
}

##### 8. How many workshop mentions per insigna? (=> hint for workshop entities)
```sparql
SELECT ?insigna (COUNT (distinct ?ws) AS ?NbWorkshopMentions)
WHERE 
{ ?ws a grz-owl:WorkshopMention ; grz-owl:insigna ?insigna .} 
GROUP BY ?insigna
ORDER BY DESC (COUNT (distinct ?ws) )
```

  
  
  
  

