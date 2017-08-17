### 13. About various geo information

##### 1. Who has the indication of a residence ?
```sparql
SELECT ?role (COUNT (distinct ?pm) AS ?PersonMentionWithResidence)
WHERE {?pm a common:PersonMention ; grz-owl:hasRole ?role; grz-owl:hasResidence ?res .}
GROUP BY ?role 
ORDER BY DESC (COUNT (distinct ?pm))
```

##### 2. Who has the indication of a geographical origin ?
```sparql
SELECT ?role (COUNT (distinct ?pm) AS ?PersonMentionWithGeoOrigins)
WHERE {?pm a common:PersonMention ; grz-owl:hasRole ?role; grz-owl:hasGeographicalOrigin ?res .}
GROUP BY ?role 
ORDER BY DESC (COUNT (distinct ?pm))
```
