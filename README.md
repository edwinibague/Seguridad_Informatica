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

   The proposed solution its the creation of a schema using a central table to almacenate the commond fields like the time mark and event type. Wiht the data source create all tables wiht a foreing key that has reference to central table. <br />
   The propose model are showed in the next image, this is a mothel create in SQLServer:

   
   ![Model SQL Server create to look the realtional tables](https://github.com/edwinibague/Seguridad_Informatica/assets/47603314/a2ef4a5a-f820-44bc-b8d0-f4813ed01b40)

   This model make a form to hava a ordered data and its most easy make search in real time only with a SQL querys, but if its needed a search by blocks, its necesary use a techniques to extract information use python scrips, its possible use libraries like pyspark to create partitions of data making dataframes and use a small blocks of data. This form  allows make search by blocks and in real time. <br />

   The selection of a SQL model or realtional model its basicaly by its the most easy to undestand and its knowledge by the author, but other considerations have been taken, for example, the cost of maintenance, scalability, implementation dificult and others. To understand the consideration that have its create the next table: <br />

| Feature | Relational Database | Data Warehouse | Data Lake |
|:----------:|:----------:|:----------:|:----------:|
| Data structure | Structured | Structured | Unstructured |
| Consultations | Efficient for structured queries | Optimized for complex analyzes | Ad hoc, requires preprocessing |
| Scalability | Limited for large volumes in real time | Highly scalable | Highly scalable |
| Flexibility | Less flexible for schema changes | More flexible for schema changes | Highly flexible |
| Costs | Moderate implementation and maintenance | Expensive implementation and maintenance | Moderate implementation, storage costs based on volume |
| Latency | Low | Moderate | High, requires preprocessing |
| Implementation difficulty | Moderate | High | Low|

2. Data ingestion and transformation: <br />
   To abordate this point its necesary implementate a combination of techniques and tools that at moment to work togueder can guarantee the sincronitation of search indice. This can be archived wiht a integral aproach:
   
   1. **identify change events in data:** <br />
      in SQl server its possible capture and track events of change using the funtion CdC this funtion provide a mehcanism to capture changes. other implementation its trigger-bassed monitoring this to monitorin all changes in data, the triggers can be configurate to execute especific actions like send notifications or update external sistems when changes are maked in data.<br />
      
   2. **Implement mechanism of change notification; **<br />
      the notifications CDC can be use to take alerts every time that its capture a change in data, this notifications can start the proces to update the indice. The notifications bassed on trigers can be configurate, when this trigger is activate for any change in data, its possible send notifications or menssage to another comunication chanel. <br />
      
   3. **indice update proces:** <br />
      the batch udate when a notification its taken can be realized in the indice update, in this momment its necesary identify all data afected and update all index entries. to update in real time are necesary use a stream procesing framework like apache Kafka or Apache Spark Streaming, this can process  a change notifications continue and update index in real time.<br />
      
   4.  **Maintenance of search index:**<br />
       to prevent any potencial discrepancies and ensure data consistency its posible use a periodically perform a full rebuild of the search index. Implement incremental updates to the index, focusing anly on the modified data recods, rather that rebuilding the entire index each time.<br />
       
   5.  **Chose an appropiate search engine;**<br />
       A consideration that have its a search monitors like elasticsearch or Apache Solr, that have capacitiers to index, search and sincronizate. Other consideration is data base NOSQL like MongoDB or Cassandra that provide  functionalities to integrate search and can use large volumens of data. <br />
       
   6.  **Consider Cloud-based Search services:**<br />
       in Cloud services are other considerations that are posible, Amazon elasticSearch service or Azure Search can provide a gestionate serach, escalability and all in real time. <br />
       
   7.  **Handle data conflicts and errors:**<br />
       Implement retry mechanism to handle temporaly failures or errors during change notification or index update processes. Develop resolution strategies to address potential discrepancies betwenen the SQL Server data and the search index. <br />
       
   8.  **Monitor and optimize Performance:**<br />
       Monitor the performance of the indexing process, including indexing time, resouce utilitation , and error rates. Optimize indexing strategies based on data volume, change frecuency, and search patterns to ensure efficient indexing and search performance. <br />
       
  The following pseudocode show the form to make a data ingestion using python.<br />
  

   ```python
   def update_search_index():
    # Establish connection to SQL Server
    sql_conn = connect_to_sql_server()

    # Capture data change events
    change_events = capture_change_events(sql_conn)

    # Process change events
    for event in change_events:
        # Identify affected data records
        affected_records = identify_affected_records(event)

        # Update search index
        update_index_with_records(affected_records)

    # Close SQL Server connection
    sql_conn.close()
    def capture_change_events(sql_conn):
      # Use SQL Server CDC or trigger-based monitoring to capture change events
      # Return a list of change events
      pass

    def identify_affected_records(event):
      # Based on the change event type (insert, update, delete), identify the affected data records
      # Return a list of affected records
      pass

    def update_index_with_records(affected_records):
        # Connect to the chosen search engine (Elasticsearch, Solr, etc.)
        search_engine_conn = connect_to_search_engine()

    # For each affected record, update the corresponding entry in the search index
    for record in affected_records:
        update_search_index_entry(record, search_engine_conn)

    # Close search engine connection
    search_engine_conn.close()

    def update_search_index_entry(record, search_engine_conn):
        # Extract relevant data from the record
        data = extract_indexable_data(record)

    # Update the corresponding entry in the search index using the extracted data
    search_engine_conn.update_index_entry(data)

    # Schedule the update_search_index function to run periodically or in response to change notifications
    schedule_index_update()


   ```
  <br />
  On this Pseudocode only show a simpificate form to implementate and in the real implementation can change. <br />

  

