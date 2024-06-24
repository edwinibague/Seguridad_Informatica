# Informatic_Security
technical test ciber-security events

this document its create to solve a multi-source Cyber-security Events Analysis, on this doccument the objetive is obtain a solution whit a pseudo implementation of knowledge on data modeling and other abilities.
now I show the solution to differents task.

## Multi-Source Cyber-Security Events Analysis

**Objective:**<br />
  Develop a data model that can integrate multiple sources of cybersecurity event data and provide insights into potential security breaches, user behavior, and system vulnerabilities.
 
**Data Description:**<br />
  You have access to a set of compressed files that represent different types of event data from a corporate network. These include authentication events, process start and stop events, network flow data, DNS lookups, and red teaming events indicating simulated attacks.
 
Data: https://csr.lanl.gov/data/cyber1/
 
**Tasks:**

1. Data Modeling:
   - Design a schema that integrates all these data sources into a unified model. This model should facilitate querying across different data types and support both batch and real-time analytics.
   - Explain your choice of database/data store (e.g., relational database, NoSQL, data warehouse) and the reasoning behind the selection of this technology.
2. Data Ingestion and Transformation:
   - Write a pseudo-code or diagram to show how you would ingest these data sources into the proposed data model.
   - Describe any transformations you would apply to the data during the ingestion process.
3. Data Analysis:<br />
  Write SQL queries or pseudocode for the following analysis tasks:
   -  Identify the top 10 users with the highest number of failed authentication attempts.
   -  Determine the most commonly accessed computers via remote logins.
   -  Analyze network flow to find any anomalies in data packets or byte counts.
   -  Correlate DNS lookup data with authentication logs to detect unusual network behaviors possibly indicative of cybersecurity threats.
4. Handling Scale and Performance:
   -  Discuss how your solution scales with an increase in data volume (from gigabytes to terabytes).
   -  Suggest strategies for performance optimization.
5. Security and Compliance:
   -  Outline the security measures necessary to protect this sensitive data.
   -  Discuss any potential compliance issues that might arise when handling cybersecurity event data and how you would address them.
 
Evaluation Criteria:
  - Clarity and completeness of the data model.
  - Efficiency and scalability of the ingestion and transformation processes.
  - Depth of analysis capability demonstrated by the queries.
  - Understanding of data security and compliance in handling sensitive information.

## Nulti-source Cyber-Security Events Analysis Solution

1. Data Modeling:<br />
   To Modeling the data its necesary know all data we have, at this way the data in the data-set its order by the next form:
   - Auth.txt.gz:<br />
     Authentication events, the data are in separate lines for the format: "Time, user@domain, source computer, target computer, authentication type, login type, authentication orientation, success/failure".
   - proc.txt.gz: <br />
     Process events, the data its regitered at the format: "Time, user@domain, computer, process name, start/end".
   - flows.txt.gz: <br />
     Red flows, every event in the red flow has the next format: "Time, Duration, source computer, source port, target computer, target port".
   - dns.txt.gz: <br />
     DNS lookup has the format: "Time, source computer, solve computer".
   - redteam.txt.gz: <br />
     RedTeam events has the format "time, user@domain, source computer, target computer".
   <br />
   Every event type its registered in separate lines and the values are delimitate by commas. The field wihtout value has a representation by an interrogation sing('?').

   the proposed solution its the creation of a schema using a central table to almacenate the commond fields like the time mark and event type. Wiht the data source create all tables wiht a foreing key that has reference to central table. <br />
   The propose model are showed in the next image, this is a mothel create in SQLServer:

   !¡
   
