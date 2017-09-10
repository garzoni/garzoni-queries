### 11. About profession normalisation

##### 01. Give me information on number of profession mentions: total, with or without transcript (TR, NoTR), standardForm (SF, NoSF), category (CAT, NoCat) (api:11_profNorm_01_info_prof_mention_numbers)
```sparql
#+ tags:
#+   - profession normalisation 

SELECT ?totalProfMention ?ProfMention_TR_SF_CAT ?ProfMention_TR_SF_NoCAT ?ProfMention_TR_NoSF_NoCAT  ?ProfMention_Suggested_TR_SF_CAT ?ProfMention_NoTR_SF_NoCAT ?ProfMention_TR_NoSF_CAT
WHERE 
{ 
{SELECT (COUNT (distinct ?prof) AS ?totalProfMention) 
 WHERE { ?prof a grz-owl:ProfessionMention .}}

{SELECT (COUNT (distinct ?prof_TR_SF_CAT) AS ?ProfMention_TR_SF_CAT)
 WHERE {?prof_TR_SF_CAT a grz-owl:ProfessionMention; common:transcript ?t ; common:standardForm ?sf ; grz-owl:professionCategory ?cat .}}

{SELECT (COUNT (distinct ?prof_TR_SF_CAT_Sugg) AS ?ProfMention_Suggested_TR_SF_CAT)
 WHERE {?prof_TR_SF_CAT_Sugg a grz-owl:ProfessionMention; common:transcript ?t ; common:suggestedStandardForm ?sf ; grz-owl:suggestedProfessionCategory ?cat .}}

{SELECT (COUNT (distinct ?prof_TR_SF_NoCat) AS ?ProfMention_TR_SF_NoCAT)
 WHERE {?prof_TR_SF_NoCat a grz-owl:ProfessionMention; common:transcript ?t ; common:standardForm ?sf .
 FILTER NOT EXISTS {?prof_TR_SF_NoCat grz-owl:professionCategory ?cat .}}}

{SELECT (COUNT (distinct ?prof_TR_NoSF_NoCat) AS ?ProfMention_TR_NoSF_NoCAT)
 WHERE {?prof_TR_NoSF_NoCat a grz-owl:ProfessionMention; common:transcript ?t .
 FILTER NOT EXISTS {?prof_TR_NoSF_NoCat grz-owl:professionCategory ?cat .}
 FILTER NOT EXISTS {?prof_TR_NoSF_NoCat common:standardForm ?sf .}}}

{SELECT (COUNT (distinct ?prof_NoTR_SF_NoCat) AS ?ProfMention_NoTR_SF_NoCAT)
 WHERE {?prof_NoTR_SF_NoCat a grz-owl:ProfessionMention; common:standardForm ?sf .
 FILTER NOT EXISTS {?prof_NoTR_SF_NoCat grz-owl:professionCategory ?cat .} 
 FILTER NOT EXISTS {?prof_NoTR_SF_NoCat common:transcript ?t .}}}

{SELECT (COUNT (distinct ?prof_TR_NoSF_CAT) AS ?ProfMention_TR_NoSF_CAT)
 WHERE {?prof_TR_NoSF_CAT a grz-owl:ProfessionMention; common:transcript ?t; grz-owl:professionCategory ?cat .
 FILTER NOT EXISTS {?prof_TR_NoSF_CAT  common:standardForm ?sf . }}}
}
```

##### 02. Display TR/SF/CAT of all profession mentions, grouped and alphabetically ordered by transcripts (api:11_profNorm_02_tr_sf_cat_prof_mentions)
```sparql
#+ tags:
#+   - profession normalisation 

SELECT ?ProfTrancript ?ProfStandardFrom ?ProfCategory
WHERE 
{
  ?prof a grz-owl:ProfessionMention; common:transcript ?ProfTrancript .
  OPTIONAL {?prof common:standardForm ?ProfStandardFrom .}
  OPTIONAL {?prof grz-owl:professionCategory ?ProfCategory .}
}
GROUP BY ?ProfTrancript
ORDER BY ASC(?ProfTrancript)
```

##### 03. Give me the number of unique transcripts that do not have SF nor CAT. (api:11_profNorm_03_nb_profTrans_NOsf_NOcat)
```sparql
#+ tags:
#+   - profession normalisation 

SELECT (COUNT (distinct ?ProfTR) AS ?NumberUniqueOrphanTR)
WHERE
{
	 ?prof a grz-owl:ProfessionMention; common:transcript ?ProfTR .
	 FILTER NOT EXISTS {?prof grz-owl:professionCategory ?cat .}
	 FILTER NOT EXISTS {?prof common:standardForm ?sf .}
}
```

##### 04. Give me the list of unique transcripts that do not have SF nor CAT. (api:11_profNorm_04_profTrans_NOsf_NOcat)
```sparql
#+ tags:
#+   - profession normalisation 

SELECT  distinct ?ProfTR
WHERE 
{
	?prof a grz-owl:ProfessionMention; common:transcript ?ProfTR .
	 FILTER NOT EXISTS {?prof grz-owl:professionCategory ?cat .}
	 FILTER NOT EXISTS {?prof common:standardForm ?sf .}
}
ORDER BY ASC(?ProfTR)
```

##### 05. Give me the profession transcripts of Master and Apprentices when different (still duplicates, for unique across master and app profd, should do a uniq on the results) (api:11_profNorm_05_diff_profTrans_of_master_and_app)
```sparql
#+ tags:
#+   - profession normalisation 
 
SELECT ?year (?trm AS ?transcriptMaster) (?tra AS ?transcriptApprentice)
WHERE
{
	 ?master a common:PersonMention; grz-owl:hasRole grz-owl:Master ; grz-owl:hasProfession ?profMaster ; core:isMentionedIn ?contract  . 
	 ?app a common:PersonMention; grz-owl:hasRole grz-owl:Apprentice ; grz-owl:hasProfession ?profApp ; core:isMentionedIn ?contract.  
	 ?contract a grz-owl:Contract ; sem:hasTimeStamp ?date .
	 BIND (year(?date) AS ?year)
	 ?profMaster common:transcript ?TRMaster .
	 ?profApp common:transcript ?TRApp .
	 BIND (lcase(str(?TRMaster)) AS ?trm )
	 BIND (lcase(str(?TRApp)) AS ?tra )
	 FILTER (?trm != ?tra)
}
GROUP BY (?year)
ORDER BY ASC(?year)
```

##### 06. With link towards DHCanvas. (api:11_profNorm_06_diff_profTrans_of_master_and_app_withDHCLink)
```sparql
#+ tags:
#+   - profession normalisation 
 
SELECT ?year (?trm AS ?transcriptMaster) (?tra AS ?transcriptApprentice) ?dhLink
WHERE
{
	 ?master a common:PersonMention; grz-owl:hasRole grz-owl:Master ; grz-owl:hasProfession ?profMaster ; core:isMentionedIn ?contract  . 
	 ?app a common:PersonMention; grz-owl:hasRole grz-owl:Apprentice ; grz-owl:hasProfession ?profApp ; core:isMentionedIn ?contract.  
	 ?contract a grz-owl:Contract ; sem:hasTimeStamp ?date ; ^edm:realizes ?page.
         ?page a meta:Page; meta:isImagedBy ?x . ?x iiif:service ?dhLink .
	 BIND (year(?date) AS ?year)
	 ?profMaster common:transcript ?TRMaster .
	 ?profApp common:transcript ?TRApp .
	 BIND (lcase(str(?TRMaster)) AS ?trm )
	 BIND (lcase(str(?TRApp)) AS ?tra )
	 FILTER (?trm != ?tra)
}
GROUP BY (?year)
ORDER BY ASC(?year)
```

##### 07. Get the list of profession categories (existing in dataset) (api:11_profNorm_07_profCat)
```sparql
#+ tags:
#+   - profession normalisation 

SELECT distinct ?profcat
WHERE {?prof grz-owl:professionCategory ?profcat .}
```

##### 08. Get the list of transcripts (TR), standard forms (SF), profession categories, AND suggestedStandardForms and suggested profession categories (api:11_profNorm_08_prof_tr_sf_cat_suggestedSF_suggestedCat)
```sparql
#+ tags:
#+   - profession normalisation 

SELECT ?ProfTranscript ?ProfStandardFrom ?ProfCategory ?SuggestedStandardForm ?SuggestedProfCategory
WHERE 
{
  ?prof a grz-owl:ProfessionMention; common:transcript ?ProfTranscript .
  OPTIONAL {?prof common:standardForm ?ProfStandardFrom .}
  OPTIONAL {?prof common:suggestedStandardForm ?SuggestedStandardForm .}
  OPTIONAL {?prof grz-owl:professionCategory ?ProfCategory .}
  OPTIONAL {?prof grz-owl:suggestedProfessionCategory ?SuggestedProfCategory .}
}
GROUP BY ?ProfTranscript
ORDER BY ASC(?ProfTranscript)
```

