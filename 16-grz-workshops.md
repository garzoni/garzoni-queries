### 16. About workshops

##### 01. How many workshop mentions are there? (api:16_workshops_01_nb_workshops)
```sparql
#+ tags:
#+   - workshop

SELECT (COUNT (distinct ?ws) AS ?NbWorkshop )
WHERE { ?ws a grz-owl:WorkshopMention } 
```

##### 02. Who has a workshop? (api:16_workshops_02_workshop_owner_roles)
```sparql
#+ tags:
#+   - workshop

SELECT ?role (COUNT (distinct ?pm) AS ?PersonMentionWithWorkshop)
WHERE 
{?pm a common:PersonMention ; grz-owl:hasRole ?role; grz-owl:hasWorkshop ?ws . 
 ?ws a grz-owl:WorkshopMention
}
GROUP BY ?role 
ORDER BY DESC (?PersonMentionWithWorkshop)
 
  ```

##### 03. How many masters are associated with a workshop or *not*? (api:16_workshops_03_nb_master_with_wo_workshop)
```sparql
#+ tags:
#+   - workshop

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

##### 04. How many workshops have and have *not* the information of their places? (api:16_workshops_04_nb_workshop_with_wo_place)
```sparql
#+ tags:
#+   - workshop

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

##### 05. Idem as 4), with time window filter. (api:16_workshops_05_nb_workshop_with_wo_place_withTW)
```sparql
#+ tags:
#+   - workshop
#+ params: ?_date_start ?_date_end

SELECT ?WorkshopWithPlaceInfo ?WorkshopWithoutPlaceInfo
WHERE 
{ 
  {SELECT (COUNT (distinct ?ws) AS ?WorkshopWithPlaceInfo)
  WHERE 
  {
      ?ws a grz-owl:WorkshopMention ; grz-owl:hasLocation ?pl ; ^grz-owl:hasWorkshop ?master.
      ?master core:isMentionedIn/sem:hasTimeStamp ?myDate .
      BIND(IF(?myDate = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?myDate) ) AS ?date)
      BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).   
      FILTER (?year > 1550)
      FILTER (?year < 1598)
  } 
  }
  
  {SELECT (COUNT (distinct ?ws) AS ?WorkshopWithoutPlaceInfo)
  WHERE 
  {
    ?ws a grz-owl:WorkshopMention ; ^grz-owl:hasWorkshop ?master.
    ?master core:isMentionedIn/sem:hasTimeStamp ?myDate .
    BIND(IF(?myDate = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?myDate) ) AS ?date)
    BIND(IF(?myDate != "NO DATE", year(?myDate), "NODATE") AS ?year).
    FILTER (?year > 1550
    FILTER (?year < 1598)
    FILTER (NOT EXISTS{?ws grz-owl:hasLocation ?pl} ) }
  }
}
```

##### 06. Give me all workshop mentions, their location information, their insigna and the year they were mentioned. (api:16_workshops_06_allInfos_workshop)
```sparql
#+ tags:
#+   - workshop
# N.B. : to filter by date add:  FILTER (?myDate < "1653-03-15"^^xsd:dateTime AND ?myDate > "1630-03-15"^^xsd:dateTime)

SELECT ?workshop  (STR(?i) AS ?insigna) ?locTranscript ?locStandard STR(?labelParish) AS ?parishLabel STR(?labelSestiere) AS ?pestiereLabel ?date
WHERE 
{
  ?pl a common:PlaceMention . 
  ?workshop a grz-owl:WorkshopMention ; grz-owl:hasLocation ?pl  ; ^grz-owl:hasWorkshop ?master .
  OPTIONAL { ?pl common:transcript ?locTranscript ; common:standardForm ?locStandard}
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere ?s . ?s rdfs:label ?labelSestiere .}
  OPTIONAL { ?workshop grz-owl:insigna ?i }
  ?master core:isMentionedIn ?contract .
  ?contract sem:hasTimeStamp ?myDate .
  BIND(IF(?myDate = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?myDate) ) AS ?date) 
} 
ORDER BY ASC(?locTranscript)
```

##### 07. What is the distribution of workshops per parishes? (api:16_workshops_07_distrib_workshop_perParish)
```sparql
#+ tags:
#+   - workshop

SELECT ?labelParish (COUNT (distinct ?workshop) AS ?NbWorkshop) (STR(?labelSestiere) AS ?labelSestiere)
WHERE
{
  ?workshop a grz-owl:WorkshopMention .
  ?workshop grz-owl:hasLocation/common:inParish ?parish . 
  ?parish rdfs:label ?labelParish ; common:inSestiere/rdfs:label ?labelSestiere .
}
GROUP BY ?labelParish ?labelSestiere
ORDER BY DESC (COUNT (distinct ?workshop))
```

##### 08. What is the distribution of workshops per sestiere? (api:16_workshops_08_distrib_workshop_perSestiere)
```sparql
#+ tags:
#+   - workshop

SELECT (STR(?labelSestiere) AS ?labelSestiere)  (COUNT (distinct ?workshop) AS ?NbWorkshop)
WHERE
{
  ?workshop a grz-owl:WorkshopMention ; grz-owl:hasLocation ?l . ?l common:inSestiere ?s. ?s rdfs:label ?labelSestiere .
}
GROUP BY  ?labelSestiere
ORDER BY DESC (?NbWorkshop)
```

##### 09. How many workshop have the indication if an insigna? (api:16_workshops_09_nb_workshop_withInsigna)
```sparql
#+ tags:
#+   - workshop

SELECT ?WorkshopWithInsigna ?WorkshopWithoutInsigna
WHERE 
{ 
  {SELECT (COUNT (distinct ?ws) AS ?WorkshopWithInsigna)
  WHERE {?ws a grz-owl:WorkshopMention ; grz-owl:insigna ?i} 
  }
  
  {SELECT (COUNT (distinct ?ws) AS ?WorkshopWithoutInsigna)
  WHERE {?ws a grz-owl:WorkshopMention . FILTER (NOT EXISTS{?ws grz-owl:insigna ?i} ) }
  }
}
```

##### 10. How many workshop mentions per insigna? (hint for workshop entities) (api:16_workshops_09_nb_workshop_mentions_withInsigna)
```sparql
#+ tags:
#+   - workshop

SELECT ?insigna (COUNT (distinct ?ws) AS ?NbWorkshopMentions)
WHERE 
{ ?ws a grz-owl:WorkshopMention ; grz-owl:insigna ?insigna .} 
GROUP BY ?insigna
ORDER BY DESC (COUNT (distinct ?ws) )
```

  
  
  
  

