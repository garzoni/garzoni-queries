#### For map creation (collab Rouen and Riccardo)

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
