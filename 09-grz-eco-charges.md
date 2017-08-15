### 09. About charges

##### 1. What kind of charges is there and with which frequency?
```sparql
SELECT (str(?tr) AS ?Charge) ( COUNT (?ch) AS ?NbCharge ) 
WHERE 
{
  ?ch a grz-owl:ChargeMention .
  ?ch common:transcript ?tr .
}
GROUP BY (str(?tr))
ORDER BY DESC ( COUNT (?ch))
```

##### 2. How many charge have a location or not?

##### 3. Overview of the locations of charges
```sparql
SELECT STR(?chargeTranscript) AS ?chargeTranscript STR(?locationTranscript) AS ?locationTranscript ?parish ?sestriere
WHERE
{
  ?charge  a grz-owl:ChargeMention .
  ?charge common:transcript ?chargeTranscript .
  ?charge  grz-owl:hasLocation ?location .
  ?location common:transcript ?locationTranscript .
  OPTIONAL {?location common:inParish ?chargeParish . ?chargeParish rdfs:label ?parish }
  OPTIONAL {?location common:inSestriere ?chargeSestriere  . ?chargeSestriere rdfs:label ?sestriere}
}
```

##### 3. What is the role of the persons having a charge?
