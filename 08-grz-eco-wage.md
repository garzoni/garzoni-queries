### 08. About wages

##### How many salaries are progressive vs. non-progressive?
```sparql
SELECT COUNT (distinct ?salNonProg) AS ?NonProgressive COUNT (distinct ?salProg) as ?Progressive
WHERE 
{
  ?salProg a grz-owl:FinancialCondition ; grz-owl:conditionType grz-owl:SingleSalary .
  ?salNonProg a grz-owl:FinancialCondition ; grz-owl:conditionType grz-owl:ProgressiveSalary .
}
```
