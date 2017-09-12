### 17. About annotation process

##### 1. Annotations by a certain person per year/month
```sparql
SELECT ?year ?month (COUNT (distinct ?c) AS ?NbContracts)
WHERE 
{ ?oa a oa:Annotation ; oa:motivatedBy oa:linking ; oa:hasBody ?c ; prov:wasGeneratedFrom ?activity .
  ?activity  prov:startedAtTime ?date ; prov:wasAssociatedWith/rdfs:label 'Francesca Zugno' .
  ?c a grz-owl:Contract . 
  BIND(year(?date) AS ?year) 
  BIND(month(?date) AS ?month) }
GROUP BY ?year ?month
ORDER BY ?year ?month
```

##### idem with days
```sparql
SELECT ?date (COUNT (distinct ?c) AS ?NbContracts)
WHERE 
{ ?oa a oa:Annotation ; oa:motivatedBy oa:linking ; oa:hasBody ?c ; prov:wasGeneratedFrom ?activity .
  ?activity  prov:startedAtTime ?d ; prov:wasAssociatedWith/rdfs:label 'Francesca Zugno' .
  ?c a grz-owl:Contract . 
  BIND(STRBEFORE(STR(?d), "T") AS ?date) 
}
GROUP BY ?date
ORDER BY ?date
```

##### 2. Average contract annotation per day for a certain person, for all annotators.

###### for a person 
```sparql
SELECT AVG (?NbContracts)
WHERE
{
  SELECT ?date (COUNT (distinct ?c) AS ?NbContracts)
  WHERE 
  { ?oa a oa:Annotation ; oa:motivatedBy oa:linking ; oa:hasBody ?c ; prov:wasGeneratedFrom ?activity .
    ?activity  prov:startedAtTime ?d ; prov:wasAssociatedWith/rdfs:label 'Francesca Zugno' .
    ?c a grz-owl:Contract . 
    BIND(STRBEFORE(STR(?d), "T") AS ?date) 
  }
  GROUP BY ?date
  ORDER BY ?date
}
```

###### for all the team together
```sparql
SELECT AVG (?NbContracts)
WHERE
{
SELECT ?date (COUNT (distinct ?c) AS ?NbContracts)
WHERE 
{ ?oa a oa:Annotation ; oa:motivatedBy oa:linking ; oa:hasBody ?c ; prov:wasGeneratedFrom ?activity .
  ?activity  prov:startedAtTime ?d  .
  ?c a grz-owl:Contract . 
  BIND(STRBEFORE(STR(?d), "T") AS ?date) 
}
GROUP BY ?date
ORDER BY ?date
}
```

###### Average Number of annotated contracts per month
```sparql
SELECT ?year ?month (AVG (?NbContracts) AS ?AvgAnnotatedContracts)
WHERE
{
SELECT ?year ?month (COUNT (distinct ?c) AS ?NbContracts)
WHERE 
{ ?oa a oa:Annotation ; oa:motivatedBy oa:linking ; oa:hasBody ?c ; prov:wasGeneratedFrom ?activity .
  ?activity  prov:startedAtTime ?d  .
  ?c a grz-owl:Contract . 
  BIND(STRBEFORE(STR(?d), "T") AS ?date) 
  BIND(year(?d) AS ?year)
  BIND(month(?d) AS ?month)
}
GROUP BY ?year ?month
ORDER BY ?year ?month
}
```

##### 3. Total number of contract per person so far.
```sparql
SELECT ?Collaborator (COUNT (distinct ?c) AS ?NbContracts)
WHERE 
{ ?oa a oa:Annotation ; oa:motivatedBy oa:linking ; oa:hasBody ?c ; prov:wasGeneratedFrom ?activity .
  ?activity  prov:startedAtTime ?date ; prov:wasAssociatedWith/rdfs:label ?Collaborator .
  ?c a grz-owl:Contract . 
}
GROUP BY ?Collaborator 
ORDER BY ASC(?Collaborator)
```
