# Functional Areas

## new for 2018

IDCFunc2018.csv


## FunctionalArea
~~~
LOAD csv with headers from "file:///IDCFunc2018.csv" as IDC
MERGE (fa:FunctionalArea {name:lower(IDC.FunctionalArea)})
~~~

~~~
CREATE INDEX ON :IDCUseCase(name)
~~~


## StrategicPriorities	
- (fa)-[:INCLUDES]->(sp)

~~~
LOAD csv with headers from "file:///IDCFunc2018.csv" AS IDC
MERGE (sp:IDCStrategicPriorities {name:lower(IDC.StrategicPriorities)})
WITH IDC, sp
MATCH (fa:FunctionalArea {name:lower(IDC.FunctionalArea)})
MERGE (fa)-[:INCLUDES]->(sp)
RETURN fa,sp
~~~

## Programs

- (sp)--(p)

~~~
LOAD csv with headers from "file:///IDCFunc2018.csv" AS IDC
MERGE (p:IDCProgram {name:lower(IDC.Programs)})
WITH IDC, p
MATCH (sp:IDCStrategicPriorities {name:lower(IDC.StrategicPriorities)})
MERGE (sp)-[:CONTAIN]->(p)
RETURN sp,p
~~~

## Use Case

(p)-[:ADDRESS]->(uc)


~~~
LOAD csv with headers from "file:///IDCFunc2018.csv" AS IDC
MERGE (uc:IDCUseCase {name:lower(IDC.UseCase),Summary:lower(IDC.UseCaseSummary)})
with uc, IDC
MATCH (p:IDCProgram {name:lower(IDC.Programs)})
MERGE (p)-[:ADDRESS]->(uc)
~~~


##  technology
(uc)-[:REALIZEDBY]->(t)

** update the technologyDeployed column to replace `;` with `,` and `and` with `,` and `, ,` with `,` and ` , ` with `,`

~~~
LOAD csv with headers from "file:///IDCFunc2018.csv" AS IDCtech
MATCH (uc:IDCUseCase {name:lower(IDCtech.UseCase)})
with uc, SPLIT(IDCtech.TechnologyDeployed,",") AS word
UNWIND range (0,size(word)-2) as i 
MERGE (t:IDCTechnology {name: word[i]}) 
MERGE (uc)-[:REALIZEDBY]->(t)
~~~
