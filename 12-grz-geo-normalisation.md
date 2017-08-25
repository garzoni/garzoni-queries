### 12. About geographical information

##### 01. For map creation (collab Rouen and Riccardo)

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

##### 02. Get all locations, with indication of what they qualify.
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

##### 03. Get all locations, with indication of what they qualify (distinct)
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

##### 04. Get locations as object of grz-owl:geographicalOrigins (of apprentice mainly)
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

##### 05. Get locations as object of grz-owl:geographicalOrigins, with lowercase distinct transcript and standard form and without place entity url.
```sparql
SELECT distinct ?TranscriptGeoOrigin  ?Standard STR(?labelParish) AS ?labelParish STR(?labelSestiere) AS ?labelSestiere
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasGeographicalOrigin ?y .
  OPTIONAL { ?pl common:transcript ?tr . BIND(str(lcase(?tr)) AS ?TranscriptGeoOrigin)}
  OPTIONAL { ?pl common:standardForm ?st . BIND(str(lcase(?st)) AS ?Standard)}
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere/rdfs:label ?labelSestiere .}
} 
GROUP BY ?TranscriptGeoOrigin ?Standard ?labelParish ?labelSestiere
ORDER BY ASC(?Transcript)
```

###### 06. Get locations as object of grz-owl:geographicalOrigins, with lowercase distinct standard form and without transcript (=> for place entity creation)
```sparql 
SELECT ?pl ?Standard ?parish ?sestiere 
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasGeographicalOrigin ?y .
  OPTIONAL { ?pl common:transcript ?tr . BIND(str(lcase(?tr)) AS ?Transcript)}
  OPTIONAL { ?pl common:standardForm ?st . BIND(str(lcase(?st)) AS ?Standard)}
  OPTIONAL { ?pl common:inParish ?parish. ?parish common:inSestiere ?sestiere .}
} 
ORDER BY ASC(?Transcript)
```

##### 07. Get locations as object of grz-owl:hasResidence (of masters mainly)
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

##### 08. Get locations as object of grz-owl:hasResidence (of masters mainly), with lowercase distinct transcripts and standard forms and without place entity urls.
```sparql
SELECT distinct ?TranscriptResidence  ?Standard STR(?labelParish) AS ?labelParish STR(?labelSestiere) AS ?labelSestiere
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasResidence ?y .
  OPTIONAL { ?pl common:transcript ?tr . BIND(str(lcase(?tr)) AS ?TranscriptResidence)}
  OPTIONAL { ?pl common:standardForm ?st . BIND(str(lcase(?st)) AS ?Standard)}
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere/rdfs:label ?labelSestiere .}
} 
GROUP BY ?TranscriptResidence ?Standard ?labelParish ?labelSestiere
ORDER BY ASC(?Transcript)
```

##### 09. Get locations as object of grz-owl:hasLocation (of Workshop Mentions and Charge Mentions)
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


##### 10. Get locations as object of grz-owl:hasLocation (of Workshop Mentions and Charge Mentions), with lowercase distinct transcripts and standard forms and without place entity urls.
```sparql
SELECT distinct ?TranscriptLocation  ?Standard STR(?labelParish) AS ?labelParish STR(?labelSestiere) AS ?labelSestiere
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasLocation ?y .
  OPTIONAL { ?pl common:transcript ?tr . BIND(str(lcase(?tr)) AS ?TranscriptLocation)}
  OPTIONAL { ?pl common:standardForm ?st . BIND(str(lcase(?st)) AS ?Standard)}
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere/rdfs:label ?labelSestiere .}
} 
GROUP BY ?TranscriptLocation ?Standard ?labelParish ?labelSestiere
ORDER BY ASC(?Transcript)
```


##### 11. Get distinct standardforms from geoOrigins property.
```sparql
SELECT distinct STR(?standard) AS ?p
WHERE { ?pl a common:PlaceMention ; ^grz-owl:hasGeographicalOrigin ?y ; common:standardForm ?standard } 
ORDER BY ASC(?standard)
```

###### 12. Get distinct standardforms from residence property.
```sparql
SELECT distinct STR(?standard) AS ?p
WHERE { ?pl a common:PlaceMention ; ^grz-owl:hasResidence ?y ; common:standardForm ?standard } 
ORDER BY ASC(?standard)
```

###### 13. Get distinct standardforms from location property.
```sparql
SELECT distinct STR(?standard) AS ?p
WHERE { ?pl a common:PlaceMention ; ^grz-owl:hasLocation ?y ; common:standardForm ?standard } 
ORDER BY ASC(?standard)
```

##### 14. Get parishes with their labels.
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
