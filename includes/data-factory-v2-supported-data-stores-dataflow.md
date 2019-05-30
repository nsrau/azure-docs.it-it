---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 05/24/2019
ms.author: jingwang
ms.openlocfilehash: 3a7cc1ac3940104993e6643e92da923ad1c6a14a
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/27/2019
ms.locfileid: "66248776"
---
| Category | Archivio dati | Supportato come un [attività di copia](../articles/data-factory/copy-activity-overview.md) origine | Supportato come un [attività di copia](../articles/data-factory/copy-activity-overview.md) sink | Supportato dal [runtime di integrazione di Azure](../articles/data-factory/concepts-integration-runtime.md#azure-integration-runtime) | Supportato dal [runtime di integrazione self-hosted](../articles/data-factory/concepts-integration-runtime.md#self-hosted-integration-runtime) | Supportato da [flusso di dati](../articles/data-factory/concepts-data-flow-overview.md)
|:--- |:--- |:--- |:--- |:--- |:--- |
| **Azure** |[Archiviazione BLOB di Azure](../articles/data-factory/connector-azure-blob-storage.md) |✓ |✓ |✓ |✓  | ✓ <br> <small>Formati supportati: Testo delimitato, Parquet</small> |
| &nbsp; |[Azure Cosmos DB (API SQL)](../articles/data-factory/connector-azure-cosmos-db.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[API di Azure Cosmos DB per MongoDB](../articles/data-factory/connector-azure-cosmos-db-mongodb-api.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Esplora dati di Azure](../articles/data-factory/connector-azure-data-explorer.md) |✓ |✓ |✓ |✓ ||
| &nbsp; |[Azure Data Lake Storage Gen1](../articles/data-factory/connector-azure-data-lake-store.md) |✓ |✓ |✓ |✓  |✓ <br> <small>Formati supportati: Testo delimitato, Parquet</small> |
| &nbsp; |[Azure Data Lake Storage Gen2](../articles/data-factory/connector-azure-data-lake-storage.md) |✓ |✓ |✓ |✓  |✓ <br> <small>Formati supportati: Testo delimitato, Parquet</small> |
| &nbsp; |[Database di Azure per MariaDB](../articles/data-factory/connector-azure-database-for-mariadb.md) |✓ | |✓ |✓  ||
| &nbsp; |[Database di Azure per MySQL](../articles/data-factory/connector-azure-database-for-mysql.md) |✓ | |✓ |✓  ||
| &nbsp; |[Database di Azure per PostgreSQL](../articles/data-factory/connector-azure-database-for-postgresql.md) |✓ | |✓ |✓  ||
| &nbsp; |[Archiviazione file di Azure](../articles/data-factory/connector-azure-file-storage.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Database SQL di Azure](../articles/data-factory/connector-azure-sql-database.md) |✓ |✓ |✓ |✓  |✓  |
| &nbsp; |[Istanza gestita di database SQL di Azure](../articles/data-factory/connector-azure-sql-database-managed-insance.md) | |✓ |✓ |✓  ||
| &nbsp; |[Azure SQL Data Warehouse](../articles/data-factory/connector-azure-sql-data-warehouse.md) |✓ |✓ |✓ |✓  |✓  |
| &nbsp; |[Indice di Ricerca di Azure](../articles/data-factory/connector-azure-search.md) | |✓ |✓ |✓  ||
| &nbsp; |[Archiviazione tabelle di Azure](../articles/data-factory/connector-azure-table-storage.md) |✓ |✓ |✓ |✓  ||
| **Database** |[Amazon Redshift](../articles/data-factory/connector-amazon-redshift.md) |✓ | |✓ |✓  ||
| &nbsp; |[DB2](../articles/data-factory/connector-db2.md) |✓ | |✓ |✓  ||
| &nbsp; |[Drill (anteprima)](../articles/data-factory/connector-drill.md) |✓ | |✓ |✓  ||
| &nbsp; |[Google BigQuery](../articles/data-factory/connector-google-bigquery.md) |✓ | |✓ |✓  ||
| &nbsp; |[Greenplum](../articles/data-factory/connector-greenplum.md) |✓ | |✓ |✓  ||
| &nbsp; |[HBase](../articles/data-factory/connector-hbase.md) |✓ | |✓ |✓  ||
| &nbsp; |[Hive](../articles/data-factory/connector-hive.md) |✓ | |✓ |✓  ||
| &nbsp; |[Apache Impala (anteprima)](../articles/data-factory/connector-impala.md) |✓ | |✓ |✓  ||
| &nbsp; |[Informix](../articles/data-factory/connector-odbc.md#ibm-informix-source) |✓ | | |✓  ||
| &nbsp; |[MariaDB](../articles/data-factory/connector-mariadb.md) |✓ | |✓ |✓  ||
| &nbsp; |[Microsoft Access](../articles/data-factory/connector-odbc.md#microsoft-access-source) |✓ | | |✓  ||
| &nbsp; |[MySQL](../articles/data-factory/connector-mysql.md) |✓ | |✓ |✓  ||
| &nbsp; |[Netezza](../articles/data-factory/connector-netezza.md) |✓ | |✓ |✓  ||
| &nbsp; |[Oracle](../articles/data-factory/connector-oracle.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Phoenix](../articles/data-factory/connector-phoenix.md) |✓ | |✓ |✓  ||
| &nbsp; |[PostgreSQL](../articles/data-factory/connector-postgresql.md) |✓ | |✓ |✓  ||
| &nbsp; |[Presto (anteprima)](../articles/data-factory/connector-presto.md) |✓ | |✓ |✓  ||
| &nbsp; |[SAP Business Warehouse Open Hub](../articles/data-factory/connector-sap-business-warehouse-open-hub.md) |✓ | | |✓  ||
| &nbsp; |[SAP Business Warehouse via MDX](../articles/data-factory/connector-sap-business-warehouse.md) |✓ | | |✓  ||
| &nbsp; |[SAP HANA](../articles/data-factory/connector-sap-hana.md) |✓ |✓ | |✓  ||
| &nbsp; |[Tabella SAP](../articles/data-factory/connector-sap-table.md) |✓ |✓ | |✓  ||
| &nbsp; |[Spark](../articles/data-factory/connector-spark.md) |✓ | |✓ |✓  ||
| &nbsp; |[SQL Server](../articles/data-factory/connector-sql-server.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Sybase](../articles/data-factory/connector-sybase.md) |✓ | | |✓  ||
| &nbsp; |[Teradata](../articles/data-factory/connector-teradata.md) |✓ | | |✓  ||
| &nbsp; |[Vertica](../articles/data-factory/connector-vertica.md) |✓ | |✓ |✓  ||
| **NoSQL** |[Cassandra](../articles/data-factory/connector-cassandra.md) |✓ | |✓ |✓  ||
| &nbsp; |[Couchbase (anteprima)](../articles/data-factory/connector-couchbase.md) |✓ | |✓ |✓  ||
| &nbsp; |[MongoDB](../articles/data-factory/connector-mongodb.md) |✓ | |✓ |✓  ||
| **File** |[Amazon S3](../articles/data-factory/connector-amazon-simple-storage-service.md) |✓ | |✓ |✓  ||
| &nbsp; |[File system](../articles/data-factory/connector-file-system.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[FTP](../articles/data-factory/connector-ftp.md) |✓ | |✓ |✓  ||
| &nbsp; |[Google Cloud Storage](../articles/data-factory/connector-google-cloud-storage.md) |✓ | |✓ |✓  ||
| &nbsp; |[HDFS](../articles/data-factory/connector-hdfs.md) |✓ | |✓ |✓  ||
| &nbsp; |[SFTP](../articles/data-factory/connector-sftp.md) |✓ | |✓ |✓  ||
| **Protocollo generico** |[HTTP generico](../articles/data-factory/connector-http.md) |✓ | |✓ |✓  ||
| &nbsp; |[OData generico](../articles/data-factory/connector-odata.md) |✓ | |✓ |✓  ||
| &nbsp; |[ODBC generico](../articles/data-factory/connector-odbc.md) |✓ |✓ | |✓  ||
| &nbsp; |[REST generico](../articles/data-factory/connector-rest.md) |✓ | |✓ |✓  ||
| **Servizi e app** |[Amazon Marketplace Web Service (anteprima)](../articles/data-factory/connector-amazon-marketplace-web-service.md) |✓ | |✓ |✓  ||
| &nbsp; |[Common Data Service per le app](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Concur (anteprima)](../articles/data-factory/connector-concur.md) |✓ | |✓ |✓  ||
| &nbsp; |[Dynamics 365](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Dynamics AX (Anteprima)](../articles/data-factory/connector-dynamics-ax.md) |✓ | |✓ |✓  ||
| &nbsp; |[Dynamics CRM](../articles/data-factory/connector-dynamics-crm-office-365.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Google AdWords (Anteprima)](../articles/data-factory/connector-google-adwords.md) |✓ | |✓ |✓  ||
| &nbsp; |[HubSpot (anteprima)](../articles/data-factory/connector-hubspot.md) |✓ | |✓ |✓  ||
| &nbsp; |[Jira (anteprima)](../articles/data-factory/connector-jira.md) |✓ | |✓ |✓  ||
| &nbsp; |[Magento (anteprima)](../articles/data-factory/connector-magento.md) |✓ | |✓ |✓  ||
| &nbsp; |[Marketo (anteprima)](../articles/data-factory/connector-marketo.md) |✓ | |✓ |✓  ||
| &nbsp; |[Office 365](../articles/data-factory/connector-office-365.md) |✓ | |✓ |✓  ||
| &nbsp; |[Oracle Eloqua (anteprima)](../articles/data-factory/connector-oracle-eloqua.md) |✓ | |✓ |✓  ||
| &nbsp; |[Oracle Responsys (anteprima)](../articles/data-factory/connector-oracle-responsys.md) |✓ | |✓ |✓  ||
| &nbsp; |[Oracle Service Cloud (anteprima)](../articles/data-factory/connector-oracle-service-cloud.md) |✓ | |✓ |✓  ||
| &nbsp; |[PayPal (anteprima)](../articles/data-factory/connector-paypal.md) |✓ | |✓ |✓  ||
| &nbsp; |[QuickBooks (anteprima)](../articles/data-factory/connector-quickbooks.md) |✓ | |✓ |✓  ||
| &nbsp; |[Salesforce](../articles/data-factory/connector-salesforce.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Cloud del servizio Salesforce](../articles/data-factory/connector-salesforce.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[Salesforce Marketing Cloud (anteprima)](../articles/data-factory/connector-salesforce-marketing-cloud.md) |✓ | |✓ |✓  ||
| &nbsp; |[SAP Cloud for Customer (C4C)](../articles/data-factory/connector-sap-cloud-for-customer.md) |✓ |✓ |✓ |✓  ||
| &nbsp; |[SAP ECC](../articles/data-factory/connector-sap-ecc.md) |✓ | |✓ |✓ ||
| &nbsp; |[ServiceNow](../articles/data-factory/connector-servicenow.md) |✓ | |✓ |✓  ||
| &nbsp; |[Shopify (anteprima)](../articles/data-factory/connector-shopify.md) |✓ | |✓ |✓  ||
| &nbsp; |[Square (anteprima)](../articles/data-factory/connector-square.md) |✓ | |✓ |✓  ||
| &nbsp; |[Tabella Web (tabella HTML)](../articles/data-factory/connector-web-table.md) |✓ | | |✓  ||
| &nbsp; |[Xero (anteprima)](../articles/data-factory/connector-xero.md) |✓ | |✓ |✓  ||
| &nbsp; |[Zoho (anteprima)](../articles/data-factory/connector-zoho.md) |✓ | |✓ |✓  ||

> [!NOTE]
> Se un connettore è contrassegnato come *Anteprima*, è possibile provarlo e inviare commenti e suggerimenti.  Se si vuole accettare una dipendenza dai connettori in versione di anteprima nella propria soluzione, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/).
