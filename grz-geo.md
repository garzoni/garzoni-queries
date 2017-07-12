##### For map creation (collab Rouen and Riccardo)

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

##### Get all locations, with indication of what they qualify
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

##### Get all locations, with indication of what they qualify (distinct)
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

##### Get locations as object of grz-owl:geographicalOrigins (of apprentice mainly)
```sparql
SELECT distinct  ?transcript ?standard ?labelParish ?sestiere
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasGeographicalOrigin ?y .
  OPTIONAL { ?pl common:transcript ?transcript }
  OPTIONAL { ?pl common:standardForm ?standard }
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere/rdfs:label ?sestiere .}
} 
GROUP BY ?transcript ?standard ?labelParish ?sestiere
ORDER BY ASC(?standard)
```


##### Get locations as object of grz-owl:hasResidence (of masters mainly)
```sparql
SELECT distinct  ?transcript ?standard ?parish ?sestiere
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasResidence ?y .
  OPTIONAL { ?pl common:transcript ?transcript }
  OPTIONAL { ?pl common:standardForm ?standard }
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?parish; common:inSestiere/rdfs:label ?sestiere .}
} 
GROUP BY ?transcript ?standard ?parish ?sestiere
ORDER BY ASC(?standard)
```

##### Get locations as object of grz-owl:hasLocation (of Workshops)
```sparql
SELECT distinct ?transcript ?standard ?parish ?sestiere
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasLocation ?y .
  OPTIONAL { ?pl common:transcript ?transcript }
  OPTIONAL { ?pl common:standardForm ?standard }
  OPTIONAL { ?pl common:inParish/rdfs:label ?parish; common:inSestiere/rdfs:label ?sestiere .}
} 
GROUP BY ?transcript ?standard ?parish ?sestiere
ORDER BY ASC(?standard)
```
##### Get distinct standardforms from geoOrigins
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
