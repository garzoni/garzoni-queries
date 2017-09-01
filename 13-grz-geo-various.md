### 13. About various geo information

##### 01. Who has the indication of a residence? (api:13_geoVar_01_who_has_residence)
```sparql
SELECT ?role (COUNT (distinct ?pm) AS ?PersonMentionWithResidence)
WHERE {?pm a common:PersonMention ; grz-owl:hasRole ?role; grz-owl:hasResidence ?res .}
GROUP BY ?role 
ORDER BY DESC (COUNT (distinct ?pm))
```

##### 02. Who has the indication of a geographical origin? (api:13_geoVar_01_who_has_geoOrig)
```sparql
SELECT ?role (COUNT (distinct ?pm) AS ?PersonMentionWithGeoOrigins)
WHERE {?pm a common:PersonMention ; grz-owl:hasRole ?role; grz-owl:hasGeographicalOrigin ?res .}
GROUP BY ?role 
ORDER BY DESC (COUNT (distinct ?pm))
```

##### 03. Get all place entities, with their number of mentions.
```sparql
SELECT ?ple ?parish ?sestiere ?label ?geoNameREF COUNT (?plm)
WHERE {
  ?ple a common:Place ; core:referredBy ?plm .
  OPTIONAL {?ple common:inParish ?parish ; common:inSestiere ?sestiere .}
  OPTIONAL {?ple grz-owl:suggestedGeonames ?geoNameREF}
  OPTIONAL {?ple rdfs:label ?label}
} 
GROUP BY ?ple ?parish ?sestiere ?label ?geoNameREF 
ORDER BY DESC(COUNT (?plm))
```
