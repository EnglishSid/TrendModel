
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
MERGE (sp:StrategicPriorities {name:IDC.StrategicPriorities})
WITH IDC, sp
MATCH (m:Mission {name:IDC.Mission})
MERGE (m)-[:HAS]->(sp)
RETURN m,sp
~~~

## Programs

(i)--(m)-->(sp)--(p)


~~~
LOAD csv with headers from "file:///idc.csv" AS IDC
MERGE (p:Program {name:lower(IDC.Programs),industry:lower(IDC.Industry)})
WITH IDC, p
MATCH (i:Industry {name:lower(IDC.Industry)})--(m:Mission {name:lower(IDC.Mission)})--(sp:StrategicPriorities {name:lower(IDC.StrategicPriorities)})
MERGE (sp)-[:HAS]->(p)
RETURN sp,p
~~~

## Use Case

(p)-[:HAS]->(uc)

~~~
LOAD csv with headers from "file:///idc.csv" AS IDC
MERGE (uc:UseCase {name:lower(IDC.UseCase),CurrentSituation:lower(IDC.CurrentSituation)})
with uc, IDC
MATCH (i:Industry {name:lower(IDC.Industry)})--(m:Mission {name:lower(IDC.Mission)})--(sp:StrategicPriorities {name:lower(IDC.StrategicPriorities)})--(p:Program {name:lower(IDC.Programs),industry:lower(IDC.Industry)})
MERGE (p)-[:HAS]->(uc)
RETURN p,uc
})
~~~

##  technology

LOAD csv with headers from "file:///idctech.csv" AS IDCtech
MERGE (t:Technology {name:lower(IDCtech.t1)})
with t, IDCtech
MATCH (uc:UseCase {name:lower(IDCtech.UseCase)})
MERGE (uc)-[:REALIZEDBY]->(t)
RETURN uc,t