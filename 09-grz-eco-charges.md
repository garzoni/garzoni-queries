### 09. About charges

##### 01. What kind of charges is there and with which frequency? (api:09_charge_01_charges_with_freq)
```sparql
#+ tags:
#+   - charges

SELECT (str(?tr) AS ?Charge) ( COUNT (?ch) AS ?NbCharge ) 
WHERE 
{
  ?ch a grz-owl:ChargeMention .
  ?ch common:transcript ?tr .
}
GROUP BY (str(?tr))
ORDER BY DESC ( COUNT (?ch))
```

##### 02. How many charge have a location or not? (api:09_charge_02_charges_with_loc)
```sparql
#+ tags:
#+   - charges

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

##### 03. Overview of the locations of charges (api:09_charge_03_charge_overview)
```sparql
#+ tags:
#+   - charges 

SELECT (STR(?chargeTranscript) AS ?chargeTranscript) (STR(?locationTranscript) AS ?locationTranscript) ?parish ?sestriere
WHERE
{
  ?charge  a grz-owl:ChargeMention .
  ?charge common:transcript ?chargeTranscript .
  ?charge  grz-owl:hasLocation ?location .
  ?location common:transcript ?locationTranscript .
  OPTIONAL {?location common:inParish ?chargeParish . ?chargeParish rdfs:label ?parish ; common:inSestriere ?chargeSestriere  .    ?chargeSestriere rdfs:label ?sestriere}
}
```

##### 04. What is the role of the persons having a charge? (api:09_charge_04_role_of_pers_with_charge)
```sparql
#+ tags:
#+   - charges 

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

##### 05. Give me information about ‘worksFor’ property. (api:09_charge_05_info_worksFor)
```sparql
#+ tags:
#+   - charges 

SELECT  (STR(?p2) AS ?WorkFor) (COUNT (?p1) AS ?NbPersonMentions)
WHERE { ?p1 grz-owl:worksFor ?p2 }
GROUP BY (STR(?p2))
ORDER BY DESC ( COUNT (?p1))
```
