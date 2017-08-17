### 13. About various geo information

##### 1. Who has a residence ?
```sparql
SELECT ?role (COUNT (distinct ?pm) AS ?PersonMentionWithResidence)
WHERE 
{?pm a common:PersonMention ; grz-owl:hasRole ?role; grz-owl:hasResidence ?res . 
}
GROUP BY ?role 
ORDER BY DESC (COUNT (distinct ?pm))
```
