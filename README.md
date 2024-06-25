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
   Each event type is logged on separate lines and the values ​​are delimited by commas. The field without a value is represented by a question mark.('?').

   The proposed solution is to create a schema using a central table to store common fields such as timestamp and event type. With the data source, you create all tables with a foreign key that references the central table. <br />
   The proposed model is shown in the following image, this is a model created in SQL Server:

   
   ![Model SQL Server create to look the realtional tables](https://github.com/edwinibague/Seguridad_Informatica/assets/47603314/a2ef4a5a-f820-44bc-b8d0-f4813ed01b40)

   This model creates a way to have ordered data and the easiest thing is to perform searches in real time only with SQL queries, but if a block search is needed, it is necessary to use techniques to extract information, use Python scripts, it is possible to use libraries like pyspark to partition data, create data frames, and use small blocks of data. In this way it allows searching by blocks and in real time. <br />

   The selection of an SQL model or relational model is given because it is easier to understand and is known to the author, but other considerations have been taken, for example, the cost of maintenance, scalability, difficulty of implementation and others. To understand the consideration that has been taken, the following table has been created: <br />

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
   To address this point, it is necessary to implement a combination of techniques and tools that, when working together, can guarantee the synchronization of the search indexes. This can be done with a comprehensive approach:
   
   1. **identify change events in data:** <br />
      In the SQL server it is possible to capture and track change events using the CDC function. This feature provides a mechanism to capture changes. Another implementation is trigger based monitoring to monitor all changes to the data, triggers can be configured to execute specific actions such as sending notifications or updating external systems when changes are made to the data.<br />
      
   2. **Implement mechanism of change notification; **<br />
      CDC notifications can be used to receive alerts whenever a change in data is captured, these notifications can start the index update process. Trigger based notifications can be configured, when this trigger is activated by any change in data, it is possible to send notifications or messages to another communication channel. <br />
      
   3. **indice update proces:** <br />
      Batch update when a notification is received can be done in the index update, at this time it is necessary to identify all affected data and update all index entries. To update in real time you need to use a stream processing framework like Apache Kafka or Apache Spark Streaming, this can process change notifications, continue and update the index in real time.<br />
      
   4.  **Maintenance of search index:**<br />
       To avoid potential discrepancies and ensure data consistency, a complete rebuild of the search index can be performed periodically. Implement incremental index updates, focusing only on changed data records, rather than rebuilding the entire index each time.<br />
       
   5.  **Chose an appropiate search engine;**<br />
       One consideration to have are search monitors such as elasticsearch or Apache Solr, which have indexing, searching, and synchronization capabilities. Another consideration is NoSQL databases such as MongoDB or Cassandra that provide functionality to integrate searches and can use large volumes of data. <br />
       
   6.  **Consider Cloud-based Search services:**<br />
       In cloud services there are other considerations that are possible, Amazon elasticSearch or Azure Search service can provide managed search, scalability and all in real time. <br />
       
   7.  **Handle data conflicts and errors:**<br />
       Implement a retry mechanism to handle temporary failures or errors during the change notification or index update processes. Develop resolution strategies to address potential discrepancies between SQL Server data and the search index. <br />
       
   8.  **Monitor and optimize Performance:**<br />
       Monitor the performance of the indexing process, including indexing time, resource utilization, and error rates. Optimize indexing strategies based on data volume, change frequency, and search patterns to ensure efficient indexing and search performance. <br />
       
  The following pseudocode shows how to perform data ingestion using Python.<br />
  

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
   The solution proposed in this case is functional on a short scale but it is possible that in the future with terabytes of data the solution will have problems, errors are normal because a relational database has problems handling large amounts of data, but for On the other hand, it is possible to scale the solution if the cloud is used. Cloud services such as Amazon, Azure or Google have the ability to use large amounts of data. It is possible to use tools such as Apache Spark that provide a solution with participation data. . In this order it is possible to carry out a hybrid solution using a data lake with relational SQL, in this case its use is possible and for this Apache Spark can provide the tools, it is possible to use python with a library like pySpark. <br />


5. Security and Compliance:<br />
   To protect sensitive data, especially cybersecurity event data, it is necessary to implement comprehensive security measures that cover all physical, logical and administrative aspects to ensure information security. <br />
   Physical access control, restricting physical access to servers, data centers and devices that store or process confidential data. Implementing security measures such as biometric systems can reduce the likelihood of third-party access. It is also possible to protect in the event of natural disasters, the implementation of plans to protect systems in case of eventualities such as fires, earthquakes and others, for this reason a redundant backup copy or systems as backup sources are a good way to protect data in physical form.<br />
   In logical security cases, it is necessary to use access controls such as authentication to access data, two-factor authentication, multi-factor, or roles based on user permissions. Use data encryption, encryption algorithms and encrypted keys. Implement solutions to cyber attacks such as DOS, using anti-malware software, firewalls, IDS and IPS to protect data systems. And another way is network segmentation, using VPN to access data or isolate systems can protect sensitive data. <br />
   Administrative management security can be addressed with security training for all employees, this measure can be used periodically to have more practices to protect data. The implementation of a security team is a good way to prevent and mitigate a security risk, this team can provide the management, monitoring and recording of all security data, in this way the team can take the process to respond to all incidents. <br />

   In Colombia, is necesary take in hands the laws and regulations that are aplicables, in this case in Colombia have a law 1273 of 2009, this stablisc the dispocition of cybercrime. The law 1581 of 2012, stabliched the personal data protection(LGPD). And the ciber Security nacional marck, that establich a a best practices and directrices to protect the infraestructure and informatic sistems of the country. <br />

   This is not a global point of view, all countries have their own restrictions and laws for using data, and the international framework depends on the type of data they use and the way this data is collected.
