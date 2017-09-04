##### 01. Get DHClinks of contract segments with more than 1 contract mention
```sparql
SELECT ?DHCLink ?masterLabel
WHERE {
  ?contract a grz-owl:Contract; grz-owl:doubleContract 1 ; ^edm:realizes ?page ; core:hasMention ?pm.
  ?page a meta:Page; meta:isImagedBy/iiif:service ?DHCLink .
  ?pm grz-owl:hasRole grz-owl:Master ; grz-owl:hasName/rdfs:label ?masterLabel . 
} 
```

##### 02. For invalid dates: get DHCLinks from contract uuids
```sparql
SELECT ?DHCLink ?masterLabel
WHERE {
  ?contract a grz-owl:Contract; dhc:uuid ?uuid ; ^edm:realizes ?page  .
  ?page a meta:Page; meta:isImagedBy/iiif:service ?DHCLink .
  OPTIONAL {?contract core:hasMention ?pm . 
  ?pm grz-owl:hasRole grz-owl:Master ; grz-owl:hasName/rdfs:label ?masterLabel . }
  VALUES ?uuid { "f53a7bba-6cba-4814-aabd-18783e18c6fe" "3cfee430-df3d-4a1a-975b-5b53f18167b3" "907f3900-34b8-484a-a304-80256b3d70be" "ddfbb8ff-17ea-4772-9b43-0dfadf1223a1" }
} 
```

##### 03. For invalid dates: get DHCLinks from event uuids
```sparql
SELECT ?DHCLink ?masterLabel
WHERE {
  ?event dhc:uuid ?uuid ; core:isMentionedIn ?contract .
  ?contract a grz-owl:Contract; dhc:uuid ?uuid ; ^edm:realizes ?page  .
  ?page a meta:Page; meta:isImagedBy/iiif:service ?DHCLink .
  OPTIONAL {?contract core:hasMention ?pm . 
  ?pm grz-owl:hasRole grz-owl:Master ; grz-owl:hasName/rdfs:label ?masterLabel . }
  VALUES ?uuid { "66e2d743-272a-45f6-87ec-1b58f609b103" "9caad726-43ad-4b1c-96f1-a8981b2e51a7" "0e22192a-cc10-45f5-9c9f-0f27c78d91c5" "cbf4ac08-441b-47cb-bf85-3327be5e2ecb" "d6f94616-a8a1-4d43-9557-c24f21986816" "d27a523d-ecf5-45bb-9e87-ccccac04e973" "cd4b61a3-82f3-4f6a-ac1c-9d112900417a" "039ad0c9-1409-4836-ad6a-5f13a0e3c5dc" "acd2640c-bab9-4604-8538-e9e2de3045d6 " "d360adad-695c-4889-9a05-ebd8d29a6eaa" "0a10fe9e-c1a9-46bf-8d8b-3bc16ac9c2f0" "f6c75bec-c947-4ed6-9418-36a27645c5a3" "1c447ac6-75fc-449b-8554-46e1ab9001e1" "d92e7cd3-d478-4ea1-b3b7-adb082fc6fde" "a50d4f6f-15bb-453b-baae-17e9255d17da" "a54bdfbc-a540-48ba-9372-33b2ac57691a" "a54bdfbc-a540-48ba-9372-33b2ac57691a" }
} 
```




