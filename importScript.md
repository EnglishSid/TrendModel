
## Industry
~~~~
LOAD csv with headers from "file:///idc.csv" as IDC
MERGE (i:IDCIndustry {name:lower(IDC.Industry)})
~~~~

## Mission

- (i)-[:HAS]->(m)

~~~~
LOAD csv with headers from "file:///idc.csv" as IDC
MERGE (m:IDCMission {name:lower(IDC.Mission)})
WITH IDC, m
MATCH (i:IDCIndustry {name:lower(IDC.Industry)})
MERGE (i)-[:HAS]->(m)
RETURN i,m
~~~~

## Strategic Priorities

(m)-[:INCLUDES]->(sp)

~~~
LOAD csv with headers from "file:///idc.csv" AS IDC
MERGE (sp:IDCStrategicPriorities {name:lower(IDC.StrategicPriorities)})
WITH IDC, sp
MATCH (m:IDCMission {name:lower(IDC.Mission)})
MERGE (m)-[:INCLUDES]->(sp)
RETURN m,sp
~~~

## Programs

(i)--(m)-->(sp)--(p)


~~~
LOAD csv with headers from "file:///idc.csv" AS IDC
MERGE (p:Program {name:lower(IDC.Programs),industry:lower(IDC.Industry)})
WITH IDC, p
MATCH (i:Industry {name:lower(IDC.Industry)})--(m:Mission {name:lower(IDC.Mission)})--(sp:StrategicPriorities {name:lower(IDC.StrategicPriorities)})
MERGE (sp)-[:CONTAIN]->(p)
RETURN sp,p
~~~

### Revision

~~~
LOAD csv with headers from "file:///idc.csv" AS IDC
MERGE (p:IDCProgram {name:lower(IDC.Programs)})
WITH IDC, p
MATCH (i:IDCIndustry {name:lower(IDC.Industry)})--(m:IDCMission {name:lower(IDC.Mission)})--(sp:IDCStrategicPriorities {name:lower(IDC.StrategicPriorities)})
MERGE (sp)-[:CONTAIN]->(p)
RETURN sp,p
~~~


## Use Case

(p)-[:HAS]->(uc)

~~~
LOAD csv with headers from "file:///idc.csv" AS IDC
MERGE (uc:IDCUseCase {name:lower(IDC.UseCase),CurrentSituation:lower(IDC.CurrentSituation)})
with uc, IDC
MATCH (i:IDCIndustry {name:lower(IDC.Industry)})--(m:IDCMission {name:lower(IDC.Mission)})--(sp:IDCStrategicPriorities {name:lower(IDC.StrategicPriorities)})--(p:IDCProgram {name:lower(IDC.Programs),IDCIndustry:lower(IDC.Industry)})
MERGE (p)-[:ADDRESS]->(uc)
RETURN p,uc
~~~

### Revision - removed the currentSituation

~~~
LOAD csv with headers from "file:///idc.csv" AS IDC
MERGE (uc:IDCUseCase {name:lower(IDC.UseCase)})
with uc, IDC
MATCH (p:IDCProgram {name:lower(IDC.Programs)})
MERGE (p)-[:ADDRESS]->(uc)
~~~


##  technology

LOAD csv with headers from "file:///idctech.csv" AS IDCtech
MERGE (t:IDCTechnology {name:lower(IDCtech.t1)})
with t, IDCtech
MATCH (uc:IDCUseCase {name:lower(IDCtech.UseCase)})
MERGE (uc)-[:REALIZEDBY]->(t)
RETURN uc,t

---

# Some calculation queries

Add Use Case Count to Technologies
~~~
MATCH (t:IDCTechnology)--(uc:IDCUseCase)
WITH t, t.name as TechnologyName, collect(t) as nodelist, Count(*) as count
set t.UCCount=count
~~~


Add Program Count to Use Cases
~~~
MATCH (p:IDCProgram)--(uc:IDCUseCase)
WITH uc, uc.name as UseCaseName, collect(uc) as nodelist, Count(*) as count
set uc.PCount=count
~~~


## multiple them up for the views

~~~
match (t:IDCTechnology)
set t.UCCountFactor=t.UCCount*10
~~~

~~~
match (u:IDCUseCase)
set u.PCountFactor=u.PCount*10
~~~

---
## Person 2 Industry

~~~
LOAD csv with headers from "file:///P2I.csv" as IDC
MERGE (p:Person {name:lower(IDC.Person)})
WITH IDC, p
MATCH (i:IDCIndustry {name:lower(IDC.VERTICALS)})
MERGE (p)-[:ASSIGNED]->(i)
RETURN i,p
~~~


---
# Indexes

CREATE INDEX ON :Person(name)
CREATE INDEX ON :IDCTechnology(name)
CREATE INDEX ON :IDCMission(name)
CREATE INDEX ON :IDCIndustry(name)
CREATE INDEX ON :IDCProgram(name)
CREATE INDEX ON :IDCStrategicPriorities(name)
CREATE INDEX ON :IDCUseCase(name)





