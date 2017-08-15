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
```sparql
SELECT ?NbChargeWithoutLoc ?NbChargeWithLoc
WHERE
{
{ SELECT (COUNT (?chargeWO) AS ?NbChargeWithoutLoc)
WHERE{ ?chargeWO  a grz-owl:ChargeMention .
FILTER (NOT EXISTS  {?chargeWO  grz-owl:hasLocation ?location })}}

{ SELECT (COUNT (?chargeWith) AS ?NbChargeWithLoc)
WHERE{ ?chargeWith  a grz-owl:ChargeMention ; grz-owl:hasLocation ?location }}

}
```

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

##### 4. What is the role of the persons having a charge?
```sparql
SELECT ?role (COUNT (distinct ?charge) AS ?NbPersonWithCharge )
WHERE
{
?pm a common:PersonMention.
?pm grz-owl:hasRole ?role .
?pm grz-owl:hasCharge ?charge .
?charge a grz-owl:ChargeMention .
}
GROUP BY (?role)
```

##### 5. Give me information about ‘worksFor’ property.
```sparql
SELECT  str(?p2) AS ?WorkFor (COUNT (?p1) AS ?NbPersonMentions)
WHERE 
{
  ?p1 grz-owl:worksFor ?p2.
}
GROUP BY (str(?p2))
ORDER BY DESC ( COUNT (?p1))
```
