### 12. About geographical information

##### 01. Get all locations, with indication of what they qualify. (api:12_geoNorm_01_loc_with_targets)
```sparql
#+ tags:
#+   - location normalisation

SELECT ?pl ?qualified ?trans ?standard ?labelParish ?sestiere
WHERE 
{
  ?pl a common:PlaceMention . 
  {?pl ^grz-owl:hasLocation ?x .} UNION {?pl ^grz-owl:hasGeographicalOrigin ?y .}  UNION {?pl ^grz-owl:hasResidence ?z .}
  OPTIONAL { ?pl common:transcript ?trans }
  OPTIONAL { ?pl common:standardForm ?standard }
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere ?s . ?s rdfs:label ?sestiere .}
  BIND(xsd:string(IF(bound(?x), "workshopLoc", "geoOrigins")) AS ?temp) .
  BIND(xsd:string(IF(bound(?z), "residence", "")) AS ?temp2) .
  BIND(xsd:string(IF(?temp2 = "", ?temp, ?temp2)) AS ?qualified) .
} 
```

##### 02. Get distinct locations, with indication of what they qualify. (api:12_geoNorm_02_loc_with_distinct_targets)
```sparql
#+ tags:
#+   - location normalisation

SELECT distinct ?qualified ?trans ?standard ?labelParish ?sestiere
WHERE 
{
  ?pl a common:PlaceMention . 
  {?pl ^grz-owl:hasLocation ?x .} UNION {?pl ^grz-owl:hasGeographicalOrigin ?y .}  UNION {?pl ^grz-owl:hasResidence ?z .}
  OPTIONAL { ?pl common:transcript ?trans }
  OPTIONAL { ?pl common:standardForm ?standard }
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere ?s . ?s rdfs:label ?sestiere .}
  BIND(xsd:string(IF(bound(?x), "workshopLoc", "geoOrigins")) AS ?temp) .
  BIND(xsd:string(IF(bound(?z), "residence", "")) AS ?temp2) .
  BIND(xsd:string(IF(?temp2 = "", ?temp, ?temp2)) AS ?qualified) .
} 
GROUP BY ?qualified ?trans ?standard ?labelParish ?sestiere
```

##### 03. Get locations as object of grz-owl:geographicalOrigins (i.e. of apprentice mainly) (api:12_geoNorm_03_loc_geoOrigins)
```sparql
#+ tags:
#+   - location normalisation

SELECT distinct (STRAFTER(STR(?pl), "garzoni/") AS ?GeoOriginPlaceEntity) ?Transcript ?Standard (STR(?labelParish) AS ?ParishLabel) (STR(?sestiere) AS ?SestiereLabel)
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasGeographicalOrigin ?y .
  OPTIONAL { ?pl common:transcript ?Transcript }
  OPTIONAL { ?pl common:standardForm ?Standard }
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere ?s . ?s rdfs:label ?sestiere .}
} 
GROUP BY ?Transcript ?Standard ?labelParish ?sestiere
ORDER BY ASC(?Transcript)
```

##### 04. Get locations as object of grz-owl:geographicalOrigins, with distinct lowercase transcripts and standard forms, and without place entity url. (api:12_geoNorm_04_loc_geoOrigins_distinct)
```sparql
#+ tags:
#+   - location normalisation

SELECT distinct ?TranscriptGeoOrigin  ?Standard (STR(?labelParish) AS ?labelParish) (STR(?labelSestiere) AS ?labelSestiere)
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasGeographicalOrigin ?y .
  OPTIONAL { ?pl common:transcript ?tr . BIND(str(lcase(?tr)) AS ?TranscriptGeoOrigin)}
  OPTIONAL { ?pl common:standardForm ?st . BIND(str(lcase(?st)) AS ?Standard)}
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere ?s . ?s rdfs:label ?labelSestiere .}
} 
GROUP BY ?TranscriptGeoOrigin ?Standard ?labelParish ?labelSestiere
ORDER BY ASC(?Transcript)
```

##### 05. Get locations as object of grz-owl:geographicalOrigins, with distinct lowercase standard forms and without transcripts (for place entity creation) (api:12_geoNorm_05_loc_geoOrigins_distinct_noTrans)
```sparql
#+ tags:
#+   - location normalisation

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


##### 06. Get locations as object of grz-owl:hasResidence (i.e. of masters mainly) (api:12_geoNorm_06_loc_residence)
```sparql
#+ tags:
#+   - location normalisation

SELECT distinct (STRAFTER(STR(?pl), "garzoni/") AS ?ResidencePlaceEntity) ?Transcript ?Standard (STR(?labelParish) AS ?ParishLabel) (STR(?sestiere) AS ?SestiereLabel)
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasResidence ?y .
  OPTIONAL { ?pl common:transcript ?Transcript }
  OPTIONAL { ?pl common:standardForm ?Standard }
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere ?s . ?s rdfs:label ?sestiere .}
} 
GROUP BY ?Transcript ?Standard ?labelParish ?sestiere
ORDER BY ASC(?Transcript)
```

##### 07. Get locations as object of grz-owl:hasResidence (of masters mainly), with distinct lowercase transcripts and standard forms, and without place entity urls. (api:12_geoNorm_07_loc_residence_distinct)
```sparql
#+ tags:
#+   - location normalisation

SELECT distinct ?TranscriptResidence  ?Standard (STR(?labelParish) AS ?labelParish) (STR(?labelSestiere) AS ?labelSestiere)
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasResidence ?y .
  OPTIONAL { ?pl common:transcript ?tr . BIND(str(lcase(?tr)) AS ?TranscriptResidence)}
  OPTIONAL { ?pl common:standardForm ?st . BIND(str(lcase(?st)) AS ?Standard)}
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere ?s . ?s rdfs:label ?labelSestiere .}
} 
GROUP BY ?TranscriptResidence ?Standard ?labelParish ?labelSestiere
ORDER BY ASC(?Transcript)
```

##### 08. Get locations as object of grz-owl:hasLocation (of Workshop Mentions and Charge Mentions) (api:12_geoNorm_08_loc_location)
```sparql
#+ tags:
#+   - location normalisation

SELECT distinct (STRAFTER(STR(?pl), "garzoni/") AS ?WorkshopPlaceEntity) ?Transcript ?Standard (STR(?labelParish) AS ?ParishLabel) (STR(?sestiere) AS ?SestiereLabel)
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasLocation ?y .
  OPTIONAL { ?pl common:transcript ?Transcript }
  OPTIONAL { ?pl common:standardForm ?Standard }
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere ?s . ?s rdfs:label ?sestiere .}
} 
GROUP BY ?Transcript ?Standard ?labelParish ?sestiere
ORDER BY ASC(?Transcript)
```


##### 09. Get locations as object of grz-owl:hasLocation (of Workshop Mentions and Charge Mentions), with lowercase distinct transcripts and standard forms and without place entity urls. (api:12_geoNorm_09_loc_location_distinct)
```sparql
#+ tags:
#+   - location normalisation

SELECT distinct ?TranscriptLocation ?Standard (STR(?labelParish) AS ?labelParish) (STR(?labelSestiere) AS ?labelSestiere)
WHERE 
{
  ?pl a common:PlaceMention . 
  ?pl ^grz-owl:hasLocation ?y .
  OPTIONAL { ?pl common:transcript ?tr . BIND(str(lcase(?tr)) AS ?TranscriptLocation)}
  OPTIONAL { ?pl common:standardForm ?st . BIND(str(lcase(?st)) AS ?Standard)}
  OPTIONAL { ?pl common:inParish ?parish. ?parish rdfs:label ?labelParish; common:inSestiere ?s . ?s rdfs:label ?labelSestiere .}
} 
GROUP BY ?TranscriptLocation ?Standard ?labelParish ?labelSestiere
ORDER BY ASC(?Transcript)
```


##### 10. Get distinct standardforms from geoOrigins property. (api:12_geoNorm_10_geoOrigins_distinct_sf)
```sparql
#+ tags:
#+   - location normalisation

SELECT distinct (STR(?standard) AS ?p)
WHERE { ?pl a common:PlaceMention ; common:standardForm ?standard . ?y grz-owl:hasGeographicalOrigin ?pl } 
ORDER BY ASC(?standard)
```

##### 11. Get distinct standardforms from residence property. (api:12_geoNorm_11_residence_distinct_sf)
```sparql
#+ tags:
#+   - location normalisation

SELECT distinct (STR(?standard) AS ?p)
WHERE { ?pl a common:PlaceMention ; common:standardForm ?standard . ?y grz-owl:hasResidence ?pl } 
ORDER BY ASC(?standard)
```

##### 12. Get distinct standardforms from location property. (api:12_geoNorm_12_location_distinct_sf)
```sparql
#+ tags:
#+   - location normalisation

SELECT distinct (STR(?standard) AS ?p)
WHERE { ?pl a common:PlaceMention ; common:standardForm ?standard . ?y grz-owl:hasLocation ?pl } 
ORDER BY ASC(?standard)
```

##### 13. Get all parishes with their labels. (api:13_geoNorm_14_parishes)
```sparql
#+ tags:
#+   - location normalisation

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
```
