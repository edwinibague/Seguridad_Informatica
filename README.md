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

  
3. Data Analysis: <br />
   we have a diferent esenarios, now are show the solutions:<br />
   - the top users wiht failed authentication attemps:<br />
     ```SQL
        SELECT
          username,
          COUNT(*) AS failed_attempts
        FROM authentication_logs
        WHERE success = FALSE
        GROUP BY username
        ORDER BY failed_attempts DESC
        LIMIT 10;
     ```
    <br />

    - Most frecuenti access computers in remote sessions: <br />
      ```SQL
          SELECT
              remote_computer,
              COUNT(*) AS login_count
          FROM remote_login_logs
          GROUP BY remote_computer
          ORDER BY login_count DESC;
      ```
      <br />

   - The red flow to find anomalies in the data pakages or bytes recound: <br />
     ```Python
     def analyze_network_traffic_for_anomalies():
         # Connect to the network traffic data source (e.g., network monitoring tool)
         network_data_source = connect_to_network_data_source()

         # Define thresholds for normal packet size and byte count
         normal_packet_size_threshold = 1500  # Bytes
         normal_byte_count_threshold = 1000000  # Bytes per second

         # Analyze network traffic data
         for packet in network_data_source.get_packets():
             packet_size = packet.get_size()
             byte_count = packet.get_byte_count()

             # Check for anomalies in packet size
             if packet_size > normal_packet_size_threshold:
                 print(f"Anomaly detected: Large packet size ({packet_size} bytes)")

             # Check for anomalies in byte count
             if byte_count > normal_byte_count_threshold:
                 print(f"Anomaly detected: High byte count ({byte_count} bytes/second)")

         # Close the connection to the network data source
         network_data_source.close()

      analyze_network_traffic_for_anomalies()

     ```
     <br />

   - Correlation of DNS and authentication registration: <br />
     ```Python
     def correlate_dns_and_authentication_logs_for_threat_detection(dns_log_table, authentication_log_table, domain_name_column, client_ip_address_column, timestamp_column, success_column):
         # Connect to the database
         connection = connect_to_database()

         # Define correlation time window (e.g., 1 hour)
         correlation_window_seconds = 3600  # Seconds in an hour

         # Retrieve DNS lookups within the time window
         dns_lookups = retrieve_dns_lookups_from_table(connection, dns_log_table, domain_name_column, client_ip_address_column, timestamp_column, correlation_window_seconds)

         # Retrieve authentication events within the time window
         authentication_events = retrieve_authentication_events_from_table(connection, authentication_log_table, client_ip_address_column, timestamp_column, success_column, correlation_window_seconds)

         # Correlate DNS lookups with authentication events
         for dns_lookup in dns_lookups:
             client_ip_address = dns_lookup.get_client_ip_address()
             lookup_domain_name = dns_lookup.get_domain_name()
             lookup_timestamp = dns_lookup.get_timestamp()

             for authentication_event in authentication_events:
                 event_timestamp = authentication_event.get_timestamp()
                 event_user = authentication_event.get_user()
                 event_success = authentication_event.get_success()

                 # Check if authentication event occurred within a reasonable time frame
                 if abs(lookup_timestamp - event_timestamp) <= correlation_window_seconds / 2:
                     # If authentication failed and domain is suspicious, flag for investigation
                     if not event_success and is_suspicious_domain(lookup_domain_name):
                         print(f"Potential threat detected: Failed authentication for user '{event_user}' after DNS lookup for '{lookup_domain_name}'")

         # Close the database connection
         connection.close()

      # Replace placeholder functions with actual implementations for retrieving data from tables
      def retrieve_dns_lookups_from_table(connection, table_name, domain_name_column, client_ip_address_column, timestamp_column, time_window_seconds):
          # Implement logic to fetch DNS lookup records from the specified table within the given time window
          # Use SQL queries to retrieve data from the table based on the provided column names and time window
          pass

      def retrieve_authentication_events_from_table(connection, table_name, client_ip_address_column, timestamp_column, success_column, time_window_seconds):
          # Implement logic to extract authentication events from the specified table within the given time window
          # Use SQL queries to retrieve data from the table based on the provided column names and time window
          pass

      # Replace placeholder function with actual implementation for suspicious domain checks
      def is_suspicious_domain(domain_name):
          # Implement logic to determine whether a given domain name is considered suspicious
          # This may involve checking against blocklists, reputation databases, or other threat intelligence sources
          pass

     ```
     <br />

4. Handling Scale and Performance:<br />
   The solution that its propouse in this case is funtional in short scale but its posible that in future with terabytes of data, the solution hava problems, thouse error are normal because a relacional data base have problems to management a large quantities of data, but in another hand its posible scale the solution if use cloud, the cloud services like Amazon, Azure o Google, have the capacities to use the large quantities of data, is posible use tools like Apache Spark that provide a solution with particionet data. In this order its posible make a hibrid solution using a data lake with SQL relational, in this case is possible use and for this reazon Apache Spark can provide the tools, its posible in python wiht a library like pySpark. <br />


5. Security and Compliance:<br />
   To protect the confidential data, especialy the cibersecurity event data, is necesary implement comprehensive security mesure that covering all aspecs phisics, logics and administrative to garantee the information security. <br />
   The phisics access control, restringering the phisiscs access to servers, data centers and devices tha have storage or procesing confidencial data. The implementation of measure security like biometric sistems can reduce the probability to have access of thirt part. In adition is posible protect in case of natural desasters, the implementation the plans to protect the sistems in case of eventuaities like fires, earcuake and others, for this reazon a redundatian security copies or sistems like respald sources are a good form to protect the data in a phisiscs form.<br />
   In logic security cases, is necesary use access control like authentication to acces the data, two factor authentication, multifactor or roles baset in user permissions.Use a data encriptation, use an encriptation algorithms and cifrate keys. Implement solutions to ciber-atacs like DOS, using anti malware softwares, firewalls, IDS and IPS to protect the sistems of data. And other form is network secmentation, use VPNs to acces a data or isolate systems can protect the confidencial data. <br />
   The administrative gestion security can be addressed with security capacitations for all employes, this medide can be use periodicaly to have more practices to protect the data. the implementation of one security team its a good form to prevent and mitigate a risk security, this teanm can provide the gestion, monitoring and register ald security data, on this way the team can take proccess to give answer for all incidents. <br />

   In Colombia, is necesary take in hands the laws and regulations that are aplicables, in this case in Colombia have a law 1273 of 2009, this stablisc the dispocition of cybercrime. The law 1581 of 2012, stabliched the personal data protection(LGPD). And the ciber Security nacional marck, that establich a a best practices and directrices to protect the infraestructure and informatic sistems of the country. <br />

   This is not a global point of view, all countries have their own restrictions and laws for using data, and the international framework depends on the type of data they use and the way this data is collected.
