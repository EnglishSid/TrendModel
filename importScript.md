
## Industry
~~~~
LOAD csv with headers from "file:///idc.csv" as IDC
MERGE (i:Industry {name:lower(IDC.Industry)})
~~~~

## Mission

- (i)-[:HAS]->(m)

~~~~
LOAD csv with headers from "file:///idc.csv" as IDC
MERGE (m:Mission {name:lower(IDC.Mission)})
WITH IDC, m
MATCH (i:Industry {name:lower(IDC.Industry)})
MERGE (i)-[:HAS]->(m)
RETURN i,m
~~~~

## Strategic Priorities

(m)-[:HAS]->(sp)

~~~
LOAD csv with headers from "file:///idc.csv" AS IDC
MERGE (sp:StrategicPriorities {name:lower(IDC.StrategicPriorities)})
WITH IDC, sp
MATCH (m:Mission {name:lower(IDC.Mission)})
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
MERGE (p:Program {name:lower(IDC.Programs)})
WITH IDC, p
MATCH (i:Industry {name:lower(IDC.Industry)})--(m:Mission {name:lower(IDC.Mission)})--(sp:StrategicPriorities {name:lower(IDC.StrategicPriorities)})
MERGE (sp)-[:CONTAIN]->(p)
RETURN sp,p
~~~


## Use Case

(p)-[:HAS]->(uc)

~~~
LOAD csv with headers from "file:///idc.csv" AS IDC
MERGE (uc:UseCase {name:lower(IDC.UseCase),CurrentSituation:lower(IDC.CurrentSituation)})
with uc, IDC
MATCH (i:Industry {name:lower(IDC.Industry)})--(m:Mission {name:lower(IDC.Mission)})--(sp:StrategicPriorities {name:lower(IDC.StrategicPriorities)})--(p:Program {name:lower(IDC.Programs),industry:lower(IDC.Industry)})
MERGE (p)-[:ADDRESS]->(uc)
RETURN p,uc
~~~

### Revision - removed the currentSituation

~~~
LOAD csv with headers from "file:///idc.csv" AS IDC
MERGE (uc:UseCase {name:lower(IDC.UseCase)})
with uc, IDC
MATCH (p:Program {name:lower(IDC.Programs)})
MERGE (p)-[:ADDRESS]->(uc)
~~~


##  technology

LOAD csv with headers from "file:///idctech.csv" AS IDCtech
MERGE (t:Technology {name:lower(IDCtech.t1)})
with t, IDCtech
MATCH (uc:UseCase {name:lower(IDCtech.UseCase)})
MERGE (uc)-[:REALIZEDBY]->(t)
RETURN uc,t

---

# Indexes

`CREATE INDEX ON :Industry(name)`
`CREATE INDEX ON :Mission(name)`
`CREATE INDEX ON :StrategicPriorities(name)`
`CREATE INDEX ON :Program(name)`
`CREATE INDEX ON :Technology(name)`

---

# Some calculation queries

Add Use Case Count to Technologies
~~~
MATCH (t:Technology)--(uc:UseCase)
WITH t, t.name as TechnologyName, collect(t) as nodelist, Count(*) as count
set t.UCCount=count
~~~


Add Program Count to Use Cases
~~~
MATCH (p:Program)--(uc:UseCase)
WITH uc, uc.name as UseCaseName, collect(uc) as nodelist, Count(*) as count
set uc.PCount=count
~~~


## multiple them up for the views

~~~
match (t:Technology)
set t.UCCountFactor=t.UCCount*10
~~~

~~~
match (u:UseCase)
set u.PCountFactor=u.PCount*10
~~~

