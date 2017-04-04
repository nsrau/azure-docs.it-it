L'attività di copia in Data Factory esegue la copia dei dati da un archivio dati di origine a un archivio dati sink. Data Factory supporta gli archivi dati seguenti. I dati da qualsiasi origine possono essere scritti in qualsiasi sink. Fare clic su un archivio dati per informazioni su come copiare dati da e verso tale archivio.

| Categoria | Archivio dati | Supportato come origine | Supportato come sink |
|:--- |:--- |:--- |:--- |
| **Azure** |[Archivio BLOB di Azure](../articles/data-factory/data-factory-azure-blob-connector.md) |✓  |✓  |
| &nbsp; |[Archivio Data Lake di Azure](../articles/data-factory/data-factory-azure-datalake-connector.md) |✓  |✓  |
| &nbsp; |[Azure DocumentDB](../articles/data-factory/data-factory-azure-documentdb-connector.md) |✓ |✓  |
| &nbsp; |[Database SQL di Azure](../articles/data-factory/data-factory-azure-sql-connector.md) |✓  |✓  |
| &nbsp; |[Azure SQL Data Warehouse](../articles/data-factory/data-factory-azure-sql-data-warehouse-connector.md) |✓  |✓  |
| &nbsp; |[Indice di Ricerca di Azure](../articles/data-factory/data-factory-azure-search-connector.md) | |✓  |
| &nbsp; |[Archivio tabelle di Azure](../articles/data-factory/data-factory-azure-table-connector.md) |✓  |✓  |
| **Database** |[Amazon Redshift](../articles/data-factory/data-factory-amazon-redshift-connector.md) |✓  | |
| &nbsp; |[DB2](../articles/data-factory/data-factory-onprem-db2-connector.md)* |✓  | |
| &nbsp; |[MySQL](../articles/data-factory/data-factory-onprem-mysql-connector.md)* |✓  | |
| &nbsp; |[Oracle](../articles/data-factory/data-factory-onprem-oracle-connector.md)* |✓  |✓  |
| &nbsp; |[PostgreSQL](../articles/data-factory/data-factory-onprem-postgresql-connector.md)* |✓  | |
| &nbsp; |[SAP Business Warehouse](../articles/data-factory/data-factory-sap-business-warehouse-connector.md)* |✓  | |
| &nbsp; |[SAP HANA](../articles/data-factory/data-factory-sap-hana-connector.md)* |✓  | |
| &nbsp; |[SQL Server](../articles/data-factory/data-factory-sqlserver-connector.md)* |✓  |✓  |
| &nbsp; |[Sybase](../articles/data-factory/data-factory-onprem-sybase-connector.md)* |✓  | |
| &nbsp; |[Teradata](../articles/data-factory/data-factory-onprem-teradata-connector.md)* |✓  | |
| **NoSQL** |[Cassandra](../articles/data-factory/data-factory-onprem-cassandra-connector.md)* |✓  | |
| &nbsp; |[MongoDB](../articles/data-factory/data-factory-on-premises-mongodb-connector.md)* |✓  | |
| **File** |[Amazon S3](../articles/data-factory/data-factory-amazon-simple-storage-service-connector.md) |✓  | |
| &nbsp; |[File system](../articles/data-factory/data-factory-onprem-file-system-connector.md)* |✓  |✓  |
| &nbsp; |[FTP](../articles/data-factory/data-factory-ftp-connector.md) |✓  | |
| &nbsp; |[HDFS](../articles/data-factory/data-factory-hdfs-connector.md)* |✓  | |
| &nbsp; |[SFTP](../articles/data-factory/data-factory-sftp-connector.md) |✓  | |
| **Altro** |[HTTP generico](../articles/data-factory/data-factory-http-connector.md) |✓  | |
| &nbsp; |[OData generico](../articles/data-factory/data-factory-odata-connector.md) |✓  | |
| &nbsp; |[ODBC generico](../articles/data-factory/data-factory-odbc-connector.md)* |✓  | |
| &nbsp; |[Salesforce](../articles/data-factory/data-factory-salesforce-connector.md) |✓  | |
| &nbsp; |[Tabella Web (tabella da HTML)](../articles/data-factory/data-factory-web-table-connector.md) |✓  | |
| &nbsp; |[GE Historian](../articles/data-factory/data-factory-odbc-connector.md#ge-historian-store)* |✓  | | |

> [!NOTE]
> Gli archivi dati contrassegnati da un asterisco (*) possono essere locali o in IaaS di Azure e richiederanno l'installazione del [Gateway di gestione dati](../articles/data-factory/data-factory-data-management-gateway.md) in un computer IaaS locale o in Azure.
>
>
