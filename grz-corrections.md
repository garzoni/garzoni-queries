##### 01. Get DHClinks of contract segments with more than 1 contract mention
```sparql
SELECT ?DHCLink ?masterLabel
WHERE {
  ?contract a grz-owl:Contract; grz-owl:doubleContract 1 ; ^edm:realizes ?page ; core:hasMention ?pm.
  ?page a meta:Page; meta:isImagedBy/iiif:service ?DHCLink .
  ?pm grz-owl:hasRole grz-owl:Master ; grz-owl:hasName/rdfs:label ?masterLabel . 
} 
```




