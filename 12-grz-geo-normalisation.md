### 12. About geographical information

##### 1. For map creation (collab Rouen and Riccardo)

```sparql
SELECT  ?apprenticeName ?transcriptGeoOrigins  ?standardGeoOrigins ?transcriptProfMaster  ?standardProfMaster ?year ?month ?day
WHERE 
{ 
  ?contract a grz-owl:Contract; sem:hasTimeStamp ?date .
  ?contract core:hasMention ?apprentice , ?master .
  ?apprentice a common:PersonMention; grz-owl:hasGeographicalOrigin ?GeoOrigins ; grz-owl:hasRole grz-owl:Apprentice ; grz-owl:hasName/rdfs:label ?apprenticeName. 
  ?master a common:PersonMention; grz-owl:hasRole grz-owl:Master ; grz-owl:hasProfession ?prof .
  ?prof common:transcript ?transcriptProfMaster .
  OPTIONAL {?prof common:standardForm ?standardProfMaster .}
  ?GeoOrigins common:transcript ?transcriptGeoOrigins .
  OPTIONAL { ?GeoOrigins  common:standardForm ?standardGeoOrigins . }

  BIND (year(?date) AS ?year).
  BIND (month(?date) AS ?month).
  BIND (day(?date) AS ?day).
  FILTER (?year > 1500 AND ?year < 1599)
} 
ORDER BY ASC(?year)
```

##### 2. Get all locations, with indication of what they qualify.
```sparql
SELECT ?pl ?qualified ?trans ?standard ?labelParish ?sestiere
WHERE 
{
  ?pl a common:PlaceMention . 
  {?pl ^grz-owl:hasLocation ?x .} UNION {?pl ^grz-owl:hasGeographicalOrigin ?y .}  UNION {?pl ^grz-owl:hasResidence ?z .}
  OPTIONAL { ?pl common:transcript ?trans }
  OPTIONAL { ?pl common:standardForm ?standard }
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere/rdfs:label ?sestiere .}
  BIND(xsd:string(IF(bound(?x), "workshopLoc", "geoOrigins")) AS ?temp) .
  BIND(xsd:string(IF(bound(?z), "residence", "")) AS ?temp2) .
  BIND(xsd:string(IF(?temp2 = "", ?temp, ?temp2)) AS ?qualified) .
} 
```

##### 3. Get all locations, with indication of what they qualify (distinct)
```sparql
SELECT distinct ?qualified ?trans ?standard ?labelParish ?sestiere
WHERE 
{
  ?pl a common:PlaceMention . 
  {?pl ^grz-owl:hasLocation ?x .} UNION {?pl ^grz-owl:hasGeographicalOrigin ?y .}  UNION {?pl ^grz-owl:hasResidence ?z .}
  OPTIONAL { ?pl common:transcript ?trans }
  OPTIONAL { ?pl common:standardForm ?standard }
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere/rdfs:label ?sestiere .}
  BIND(xsd:string(IF(bound(?x), "workshopLoc", "geoOrigins")) AS ?temp) .
  BIND(xsd:string(IF(bound(?z), "residence", "")) AS ?temp2) .
  BIND(xsd:string(IF(?temp2 = "", ?temp, ?temp2)) AS ?qualified) .
} 
GROUP BY ?qualified ?trans ?standard ?labelParish ?sestiere
```

##### 4. Get locations as object of grz-owl:geographicalOrigins (of apprentice mainly)
```sparql
SELECT distinct STRAFTER(STR(?pl), "garzoni/") AS ?GeoOriginPlaceEntity ?Transcript ?Standard STR(?labelParish) AS ?ParishLabel STR(?sestiere) AS ?SestiereLabel
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasGeographicalOrigin ?y .
  OPTIONAL { ?pl common:transcript ?Transcript }
  OPTIONAL { ?pl common:standardForm ?Standard }
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere/rdfs:label ?sestiere .}
} 
GROUP BY ?Transcript ?Standard ?labelParish ?sestiere
ORDER BY ASC(?Transcript)

```


##### 5. Get locations as object of grz-owl:hasResidence (of masters mainly)
```sparql
SELECT distinct STRAFTER(STR(?pl), "garzoni/") AS ?ResidencePlaceEntity ?Transcript ?Standard STR(?labelParish) AS ?ParishLabel STR(?sestiere) AS ?SestiereLabel
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasResidence ?y .
  OPTIONAL { ?pl common:transcript ?Transcript }
  OPTIONAL { ?pl common:standardForm ?Standard }
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere/rdfs:label ?sestiere .}
} 
GROUP BY ?Transcript ?Standard ?labelParish ?sestiere
ORDER BY ASC(?Transcript)
```

##### 6. Get locations as object of grz-owl:hasLocation (of Workshops)
```sparql
SELECT distinct STRAFTER(STR(?pl), "garzoni/") AS ?WorkshopPlaceEntity ?Transcript ?Standard STR(?labelParish) AS ?ParishLabel STR(?sestiere) AS ?SestiereLabel
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasLocation ?y .
  OPTIONAL { ?pl common:transcript ?Transcript }
  OPTIONAL { ?pl common:standardForm ?Standard }
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere/rdfs:label ?sestiere .}
} 
GROUP BY ?Transcript ?Standard ?labelParish ?sestiere
ORDER BY ASC(?Transcript)
```
##### 7. Get distinct standardforms from geoOrigins
```sparql
SELECT distinct STR(?standard)
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasGeographicalOrigin ?y .
  ?pl common:standardForm ?standard 
} 
ORDER BY ASC(?standard)
```

##### 8. Get parishes with their labels.
```sparql
SELECT ?p ?lp ?alt1 ?alt2
WHERE 
{
  ?p a common:Parish . 
  ?p rdfs:label ?lp .
  OPTIONAL {
    ?p skos:altLabel ?alt1.
    ?p skos:altLabel ?alt2.  
    FILTER ( ?alt1 != ?alt2 )
    }
} 
GROUP BY ?p
ORDER BY ASC (?lp)
