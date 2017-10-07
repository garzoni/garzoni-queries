#### 01. Query Valentina on painters
```sparql
SELECT ?c ?Date ?contractDurationInMonths ?AppEventPresent ?masterLabel ?masterForename ?masterQuondam1 ?masterPatro1 ?masterQuondam2 ?masterPatro2 ?masterSurname?masterProfessionTranscript ?masterProfessionCategory ?masterGender ?appLabel ?appProfessionTranscript ?appGeoOriginsTranscript ?appAge ?appAgeText ?appDetails ?SalaryInGoodOrMoney ?SalaryType ?SalaryPaidBy ?SalaryPeriodization ?SalaryPartialAmount ?SalaryTotalAmount ?SalaryCurrency ?SalaryPresent ?contractDetails ?financialCondDetails ?appEventDetails ?DHCLink
WHERE
{
    ?master a common:PersonMention ; grz-owl:hasRole grz-owl:Master ; grz-owl:hasProfession ?profMaster ; grz-owl:hasName ?masterAppellation .
    ?masterAppellation rdfs:label ?labelM . 

    {?profMaster grz-owl:professionCategory ?masterProfCat .
     ?profMaster common:transcript ?masterProfTranscript .
    VALUES ?masterProfCat {"ricamatore" "fabbricatore di casse" "decorazione e mestieri dell'arte"}
    }
    UNION
    {
      ?profMaster grz-owl:professionCategory ?masterProfCat .
      ?profMaster common:transcript ?masterProfTranscript .
      ?masterProfTranscript bif:contains "'ceselado*' OR 'cori*' OR 'cuori*' OR 'cuoridor*' OR 'depento*' OR 'desegnado*' OR 'dessegnado*' OR 'figure*' OR 'indorado*' OR 'indorato*'  OR 'madone*' OR 'madonne*' OR 'mascare*' OR 'maschare*' OR 'maschere*' OR 'miniado*' OR 'pito*' OR 'pitto*' OR 'recamado*' OR 'ricamado*' OR 'recamato*' OR 'ricamato*' OR 'cassele*' OR 'casele*' OR 'coffane*' OR 'cofane*' OR 'targe*' OR 'targhe*' OR 'cartole*' OR 'cortine*' OR 'Ceselado*' OR 'Cori*' OR 'Cuori*' OR 'Cuoridor*' OR 'Depento*' OR 'Desegnado*' OR 'Dessegnado*' OR 'Figure*' OR 'Indorado*' OR 'Indorato*'  OR 'Madone*' OR 'Madonne*' OR 'Mascare*' OR 'Maschare*' OR 'Maschere*' OR 'Miniado*' OR 'Pito*' OR 'Pitto*' OR 'Recamado*' OR 'Ricamado*' OR 'Recamato*' OR 'Ricamato*' OR 'Cassele*' OR 'Casele*' OR 'Coffane*' OR 'Cofane*' OR 'Targe*' OR 'Targhe*' OR 'Cartole*' OR 'Cortine*' " .
    }

    OPTIONAL {?master foaf:gender ?mg }
    ?master core:isMentionedIn ?c .
    ?c a grz-owl:Contract ; core:hasMention ?app .
    OPTIONAL {?c grz-owl:details ?cDetails}




OPTIONAL {
       ?c core:hasMention ?event .
       ?event a common:EventMention .
       ?event sem:eventType <http://vocab.dhlab.epfl.ch/garzoni#Apprenticeship> .
       ?event common:duration ?contractDuration .
       OPTIONAL {?event grz-owl:details ?eDetails }
}


    OPTIONAL {
         ?c grz-owl:hasCondition ?finCond .
         ?finCond a <http://vocab.dhlab.epfl.ch/garzoni#FinancialCondition> .
         ?finCond grz-owl:conditionType ?findConType .
         OPTIONAL {?finCond grz-owl:paidBy ?PaidBy }
         OPTIONAL {?finCond grz-owl:paidInGoods ?GoodOrMoney }
         OPTIONAL {?finCond grz-owl:partialAmount ?PartialAmount }
         OPTIONAL {?finCond grz-owl:totalAmount ?TotalAmount }
         OPTIONAL {?finCond grz-owl:currencyUnit ?Currency }
         OPTIONAL {?finCond grz-owl:periodization ?Periodization}
         OPTIONAL {?finCond grz-owl:details ?finconDetails}
    }



    OPTIONAL {?masterAppellation core:hasComponent ?msurname . 
                ?msurname a grz-owl:Surname .
                ?msurname common:transcript|rdf:value ?trmsurname.
                BIND(STR(?trmsurname) AS ?masterSurname)}
    OPTIONAL {?masterAppellation core:hasComponent ?mforename .
                ?mforename a grz-owl:Forename .
                ?mforename common:transcript|rdf:value ?trmforename.
                BIND(STR(?trmforename) AS ?masterForename)}
    OPTIONAL {?masterAppellation core:hasComponent ?mpatro1 .
                ?mpatro1 a grz-owl:Patronymic1 .
                ?mpatro1 common:transcript|rdf:value ?trmpatro1.
                BIND(STR(?trmpatro1) AS ?masterPatro1)}
    OPTIONAL {?masterAppellation core:hasComponent ?mpatro2 .
                ?mpatro2 a grz-owl:Patronymic2 .
                ?mpatro2 common:transcript|rdf:value ?trmpatro2.
                BIND(STR(?trmpatro2) AS ?masterPatro2)}

    OPTIONAL {?masterAppellation grz-owl:quondam1 ?mq1 . BIND(STR(?mq1) AS ?masterQuondam1)}
    OPTIONAL {?masterAppellation grz-owl:quondam2 ?mq2 . BIND(STR(?mq2) AS ?masterQuondam2)}


    ?app a common:PersonMention ; grz-owl:hasRole grz-owl:Apprentice .
    ?app grz-owl:hasName/rdfs:label ?labelA .
    OPTIONAL {?app grz-owl:hasGeographicalOrigin/common:transcript ?GeoOriginsTranscript .}
    OPTIONAL {?app grz-owl:hasProfession/common:transcript ?appProfTranscript }
    OPTIONAL {?app foaf:age ?appAge }
    OPTIONAL {?app grz-owl:ageText ?at }
    OPTIONAL {?app grz-owl:details ?aDetails }


    ?c ^edm:realizes ?page .
    ?page a meta:Page; meta:isImagedBy ?x . ?x iiif:service ?DHCLink .
    ?c sem:hasTimeStamp ?date .
    BIND(IF(?date = "0"^^<http://www.w3.org/2001/XMLSchema#gYear>,"NO DATE", xsd:dateTime(?date) ) AS ?myDate)


   BIND(STR(?cDetails) AS ?contractDetails)
   BIND(STR(?eDetails) AS ?appEventDetails)
   BIND(STR(?finconDetails) AS ?financialCondDetails)
   BIND(STR(?contractDuration) AS ?contractDurationInMonths)
   BIND(STR(?aDetails) AS ?appDetails)
   BIND(STR(?labelM) AS ?masterLabel)
   BIND(STR(?labelA) AS ?appLabel)
   BIND(STR(?mg) AS ?masterGender)
   BIND(STR(?masterProfTranscript) AS ?masterProfessionTranscript)
   BIND(STR(?masterProfCat) AS ?masterProfessionCategory)
   BIND(STR(?GeoOriginsTranscript) AS ?appGeoOriginsTranscript)
   BIND(STR(?appProfTranscript) AS ?appProfessionTranscript)
   BIND(STR(?at) AS ?appAgeText)

   BIND(STRBEFORE(STR(?myDate), "T") AS ?Date)
   BIND(STRAFTER(STR(?findConType), "garzoni#") AS ?SalaryType)
   BIND(STR(?GoodOrMoney) AS ?SalaryInGoodOrMoney)
   BIND(STR(?TotalAmount) AS ?SalaryTotalAmount)
   BIND(STR(?PartialAmount) AS ?SalaryPartialAmount)
   BIND(STRAFTER(STR(?Currency), "garzoni#")  AS ?SalaryCurrency)
   BIND(STRAFTER(STR(?Periodization), "garzoni#")  AS ?SalaryPeriodization)
   BIND(STRAFTER(STR(?PaidBy), "garzoni#")  AS ?SalaryPaidBy)
   BIND((IF(bound(?finCond) ,"salary", "noSalary"))  AS ?SalaryPresent)
   BIND((IF(bound(?event) ,"appEvent", "noAppEvent"))  AS ?AppEventPresent)
}


```
