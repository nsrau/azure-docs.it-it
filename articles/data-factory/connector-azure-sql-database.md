---
title: Copiare e trasformare i dati nel database SQL di AzureCopy and transform data in Azure SQL Database
description: Informazioni su come copiare dati da e verso il database SQL di Azure e trasformare i dati nel database SQL di Azure usando Azure Data Factory.Learn how to and from Azure SQL Database, and transform data in Azure SQL Database by using Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: db55e685fb50c89eb850e1b9ee9dcf13d20fb614
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417534"
---
# <a name="copy-and-transform-data-in-azure-sql-database-by-using-azure-data-factory"></a>Copiare e trasformare i dati nel database SQL di Azure usando Azure Data FactoryCopy and transform data in Azure SQL Database by using Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione di Azure Data Factory in uso:Select the version of Azure Data Factory that you're using:"]
> * [Versione 1](v1/data-factory-azure-sql-connector.md)
> * [Versione corrente](connector-azure-sql-database.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e nel database SQL di Azure e usare flusso di dati per trasformare i dati nel database SQL di Azure.This article outlines how to use Copy Activity in Azure Data Factory to copy data from and to Azure SQL Database, and use Data Flow to transform data in Azure SQL Database. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di database SQL di Azure è supportato per le attività seguenti:This Azure SQL Database connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con la tabella [di matrice di origine/sink supportataCopy activity with supported source/sink matrix](copy-activity-overview.md) table
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

Per l'attività di copia, questo connettore di database SQL di Azure supporta queste funzioni:For Copy activity, this Azure SQL Database connector supports these functions:

- Copia dei dati tramite l'autenticazione SQL e l'autenticazione del token dell'applicazione di Azure Active Directory (Azure AD) con un'entità servizio o identità gestite per le risorse di Azure.Copying data by using SQL authentication and Azure Active Directory (Azure AD) Application token authentication with a service principal or managed identities for Azure resources.
- Come origine, il recupero dei dati tramite una query SQL o una stored procedure.
- Come sink, l'aggiunta di dati a una tabella di destinazione o la chiamata di una stored procedure con logica personalizzata durante la copia.

>[!NOTE]
>Il database SQL di Azure [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-current) non è supportato da questo connettore. Per risolvere il problema, è possibile utilizzare un [connettore ODBC generico](connector-odbc.md) e un driver ODBC di SQL Server tramite un runtime di integrazione self-hosted. Seguire [queste indicazioni](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-current) con il download del driver ODBC e le configurazioni delle stringhe di connessione.

> [!IMPORTANT]
> Se si copiano dati usando il runtime di integrazione di Azure Data Factory, configurare un firewall di [Azure SQL Server](https://docs.microsoft.com/azure/sql-database/sql-database-firewall-configure) in modo che i servizi di Azure possano accedere al server.
> Se si copiano dati utilizzando un runtime di integrazione self-hosted, configurare il firewall di Sql Server di Azure per consentire l'intervallo IP appropriato. Questo intervallo include l'indirizzo IP del computer usato per connettersi al database SQL di Azure.This range includes the machine's IP that's used to connect to Azure SQL Database.

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Nelle sezioni seguenti vengono fornite informazioni dettagliate sulle proprietà usate per definire le entità di Azure Data Factory specifiche di un connettore del database SQL di Azure.The following sections provide details about properties that are used to define Azure Data Factory entities specific to an Azure SQL Database connector.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per un servizio collegato al database SQL di Azure sono supportate queste proprietà:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **AzureSqlDatabase**. | Sì |
| connectionString | Specificare le informazioni necessarie per connettersi all'istanza del database SQL di Azure per la proprietà **connectionString.Specify** information needed to connect to the Azure SQL Database instance for the connectionString property. <br/>È anche possibile inserire una password o una chiave dell'entità servizio nell'insieme di credenziali delle chiavi di Azure.You can also put a password or service principal key in Azure Key Vault. Se si tratta di autenticazione SQL, estrarre la `password` configurazione dalla stringa di connessione. Per altre informazioni, vedere l'esempio JSON che segue la tabella e [Archiviare le credenziali in Archiviazione delle chiavi](store-credentials-in-key-vault.md)di Azure.For more information, see the JSON example following the table and Store credentials in Azure Key Vault . | Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì, quando si usa l'autenticazione di Azure AD con un'entità servizioYes, when you use Azure AD authentication with a service principal |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Azure Data Factory o fare riferimento a [un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'autenticazione di Azure AD con un'entità servizioYes, when you use Azure AD authentication with a service principal |
| tenant | Specificare le informazioni sul tenant, ad esempio il nome di dominio o l'ID tenant, in cui risiede l'applicazione. Recuperarlo passando il mouse nell'angolo superiore destro del portale di Azure.Retrieve it by hovering the mouse in the upper-right corner of the Azure portal. | Sì, quando si usa l'autenticazione di Azure AD con un'entità servizioYes, when you use Azure AD authentication with a service principal |
| connectVia | Questo [runtime di integrazione](concepts-integration-runtime.md) viene usato per connettersi all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted se l'archivio dati si trova in una rete privata. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No |

Per altri tipi di autenticazione, fare riferimento alle sezioni seguenti relative, rispettivamente, ai prerequisiti e agli esempi JSON:

- [Autenticazione SQL](#sql-authentication)
- [Autenticazione token dell'applicazione Azure AD: entità servizioAzure AD application token authentication: Service principal](#service-principal-authentication)
- [Autenticazione token dell'applicazione Azure AD: identità gestite per le risorse di Azure](#managed-identity)

>[!TIP]
>Se si verifica un errore con il codice di errore "UserErrorFailedToConnectToSqlServer" e un messaggio del `Pooling=false` tipo "Il limite di sessione per il database è XXX ed è stato raggiunto", aggiungere alla stringa di connessione e riprovare.

### <a name="sql-authentication"></a>Autenticazione SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Esempio di servizio collegato tramite l'autenticazione SQL

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Password in Azure Key Vault** 

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Per usare l'autenticazione token dell'applicazione di Azure AD basata sull'entità servizio, seguire questa procedura:

1. [Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) nel portale di Azure. Prendere nota del nome dell'applicazione e dei valori seguenti che definiscono il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. [Effettuare](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) il provisioning di un amministratore di Azure Active Directory per Sql Server di Azure nel portale di Azure, se non è già stato fatto. L'amministratore di Azure AD deve essere un utente o un gruppo di Azure AD, ma non può essere un'entità servizio. Questo passaggio viene eseguito in modo che, nel passaggio successivo, sia possibile usare un'identità di Azure AD per creare un utente di database indipendente per l'entità servizio.

3. [Creare utenti del database indipendente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) per l'entità servizio. Connettersi al database da o a cui si desidera copiare i dati usando strumenti come SQL Server Management Studio, con un'identità di Azure AD con almeno l'autorizzazione ALTER ANY USER. Eseguire il codice T-SQL seguente: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. Concedere all'entità servizio le autorizzazioni necessarie, come si fa di norma per gli utenti SQL o altri utenti. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your application name];
    ```

5. Configurare un servizio collegato al database SQL di Azure in Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Esempio di servizio collegato tramite l'autenticazione basata su entità servizio

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<service principal key>"
            },
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Identità gestite per l'autenticazione delle risorse di AzureManaged identities for Azure resources authentication

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare questa identità gestita per l'autenticazione del database SQL di Azure.You can use this managed identity for Azure SQL Database authentication. La factory specificata può accedere e copiare i dati dal database o nel database usando questa identità.

Per utilizzare l'autenticazione dell'identità gestita, attenersi alla seguente procedura.

1. [Effettuare](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server) il provisioning di un amministratore di Azure Active Directory per Sql Server di Azure nel portale di Azure, se non è già stato fatto. L'amministratore di Azure AD può essere un utente di Azure AD o un gruppo di Azure AD. Se si concede al gruppo con identità gestita un ruolo di amministratore, ignorare i passaggi 3 e 4. L'amministratore ha accesso completo al database.

2. [Creare utenti](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) di database indipendente per l'identità gestita di Azure Data Factory.Create contained database users for the Azure Data Factory managed identity. Connettersi al database da o a cui si desidera copiare i dati usando strumenti come SQL Server Management Studio, con un'identità di Azure AD con almeno l'autorizzazione ALTER ANY USER. Eseguire il codice T-SQL seguente: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. Concedere all'identità gestita Data Factory le autorizzazioni necessarie come di consueto per gli utenti SQL e altri utenti. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017).

    ```sql
    ALTER ROLE [role name] ADD MEMBER [your Data Factory name];
    ```

4. Configurare un servizio collegato al database SQL di Azure in Azure Data Factory.

**Esempio**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per definire i dataset, vedere [Set di dati](https://docs.microsoft.com/azure/data-factory/concepts-datasets-linked-services). 

Le proprietà seguenti sono supportate per il set di dati del database SQL di Azure:The following properties are supported for Azure SQL Database dataset:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del dataset deve essere impostata su **AzureSqlTable**. | Sì |
| schema | Nome dello schema. |No per l'origine, Sì per il sink  |
| tabella | Nome della tabella/vista. |No per l'origine, Sì per il sink  |
| tableName | Nome della tabella/vista con schema. Questa proprietà è supportata per compatibilità con le versioni precedenti. Per un nuovo `schema` `table`carico di lavoro, utilizzare e . | No per l'origine, Sì per il sink |

#### <a name="dataset-properties-example"></a>Esempio di proprietà dei set di dati

```json
{
    "name": "AzureSQLDbDataset",
    "properties":
    {
        "type": "AzureSqlTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Database linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink del database SQL di Azure.

### <a name="azure-sql-database-as-the-source"></a>Database SQL di Azure come origine

Per copiare i dati dal database SQL di Azure, nella sezione **dell'origine** dell'attività di copia sono supportate le proprietà seguenti:To copy data from Azure SQL Database, the following properties are supported in the copy activity source section:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **AzureSqlSource**. Il tipo "SqlSource" è ancora supportato per la compatibilità con le versioni precedenti. | Sì |
| sqlReaderQuery | Questa proprietà usa la query SQL personalizzata per leggere i dati. Un esempio è `select * from MyTable`. | No |
| sqlReaderStoredProcedureName | Nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. | No |
| storedProcedureParameters | Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole/minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |
| isolationLevel | Specifica il comportamento di blocco delle transazioni per l'origine SQL. I valori consentiti sono: **ReadCommitted** (impostazione predefinita), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Fare riferimento a [questo documento](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) per ulteriori dettagli. | No |

**Punti da notare:**

- Se si specifica **sqlReaderQuery** per **AzureSqlSource**, l'attività di copia esegue la query sull'origine del database SQL di Azure per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters**, se la stored procedure accetta parametri.
- Se non si specifica **sqlReaderQuery** o **sqlReaderStoredProcedureName**, le colonne definite nella sezione "structure" del set di dati JSON vengono utilizzate per costruire una query. La `select column1, column2 from mytable` query viene eseguita nel database SQL di Azure.The query runs against Azure SQL Database. Se la definizione del set di dati non include "structure", vengono selezionate tutte le colonne della tabella.

#### <a name="sql-query-example"></a>Esempio di query SQL

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

#### <a name="stored-procedure-example"></a>Esempio di stored procedure

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL Database input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "AzureSqlSource",
                "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
                "storedProcedureParameters": {
                    "stringData": { "value": "str3" },
                    "identifier": { "value": "$$Text.Format('{0:yyyy}', <datetime parameter>)", "type": "Int"}
                }
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="stored-procedure-definition"></a>Definizione della stored procedure

```sql
CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
(
    @stringData varchar(20),
    @identifier int
)
AS
SET NOCOUNT ON;
BEGIN
     select *
     from dbo.UnitTestSrcTable
     where dbo.UnitTestSrcTable.stringData != stringData
    and dbo.UnitTestSrcTable.identifier != identifier
END
GO
```

### <a name="azure-sql-database-as-the-sink"></a>Database SQL di Azure come sink

> [!TIP]
> Per altre informazioni sui comportamenti di scrittura, le configurazioni e le procedure consigliate supportati, vedere Procedure consigliate per il caricamento dei dati nel database SQL di Azure.Learn more about the supported write behaviors, configurations, and best practices from [Best practice for loading data into Azure SQL Database](#best-practice-for-loading-data-into-azure-sql-database).

Per copiare i dati nel database SQL di Azure, nella sezione relativa al sink dell'attività di copia sono supportate le proprietà seguenti:To copy data to Azure SQL Database, the following properties are supported in the copy activity **sink** section:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del sink dell'attività di copia deve essere impostata su **AzureSqlSink**. Il tipo "SqlSink" è ancora supportato per la compatibilità con le versioni precedenti. | Sì |
| writeBatchSize | Numero di righe da inserire nella tabella SQL *per batch.*<br/> Il valore consentito è **integer** (numero di righe). Per impostazione predefinita, Azure Data Factory determina dinamicamente le dimensioni del batch appropriate in base alle dimensioni della riga. | No |
| writeBatchTimeout | Tempo di attesa per il completamento dell'operazione di inserimento batch prima del timeout.<br/> Il valore consentito è **timespan**. Un esempio è "00:30:00" (30 minuti). | No |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati nel database SQL di Azure.Specify a SQL query for the copy activity to run before writing data into Azure SQL Database. Viene richiamata solo una volta per ogni esecuzione della copia. Usare questa proprietà per pulire i dati precaricati. | No |
| sqlWriterStoredProcedureName | Il nome della stored procedure che definisce come applicare i dati di origine in una tabella di destinazione. <br/>Questa stored procedure viene *richiamata per batch*. Per le operazioni che vengono eseguite una sola volta e non hanno `preCopyScript` nulla a che fare con i dati di origine, ad esempio eliminare o troncare, utilizzare la proprietà . | No |
| storedProcedureTableTypeParameterName |Nome del parametro del tipo di tabella specificato nella stored procedure.  |No |
| sqlWriterTableType |Nome del tipo di tabella da utilizzare nella stored procedure. Nel corso dell'attività di copia, i dati spostati vengono resi disponibili in una tabella temporanea di questo tipo. Il codice della stored procedure può quindi unire i dati di cui è in corso la copia con i dati esistenti. |No |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |
| Tableoption | Specifica se creare automaticamente la tabella sink se non esiste in base allo schema di origine. La creazione automatica della tabella non è supportata quando il sink specifica che la stored procedure o la copia in fasi è configurata nell'attività di copia. I valori `none` consentiti `autoCreate`sono: (predefinito), . |No |
| disableMetricsCollection (insieme di disableMetricsCollection) | Data Factory raccoglie metriche come le DGU del database SQL di Azure per l'ottimizzazione delle prestazioni di copia e i consigli. Se si è interessati `true` a questo comportamento, specificare di disattivarlo. | No (il valore predefinito è `false`) |

**Esempio 1: Aggiungere datiExample 1: Append data**

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Esempio 2: Richiamare una stored procedure durante la copiaExample 2: Invoke a stored procedure during copy**

Per altre informazioni, vedere [Richiamare una stored procedure da un sink SQL](#invoke-a-stored-procedure-from-a-sql-sink).

```json
"activities":[
    {
        "name": "CopyToAzureSQLDatabase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Azure SQL Database output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "AzureSqlSink",
                "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
                "storedProcedureTableTypeParameterName": "MyTable",
                "sqlWriterTableType": "MyTableType",
                "storedProcedureParameters": {
                    "identifier": { "value": "1", "type": "Int" },
                    "stringData": { "value": "str1" }
                }
            }
        }
    }
]
```

## <a name="best-practice-for-loading-data-into-azure-sql-database"></a>Procedura consigliata per il caricamento di dati nel database SQL di AzureBest practice for loading data into Azure SQL Database

Quando si copiano dati nel database SQL di Azure, potrebbe essere necessario un comportamento di scrittura diverso:When you copy data into Azure SQL Database, you might require different write behavior:

- [Append](#append-data): I dati di origine conpossonore solo nuovi record.
- [Upsert](#upsert-data): I miei dati di origine hanno sia inserti che aggiornamenti.
- [Sovrascrivi](#overwrite-the-entire-table): Voglio ricaricare ogni volta un'intera tabella delle dimensioni.
- [Scrivere con logica personalizzata:](#write-data-with-custom-logic)è necessaria un'ulteriore elaborazione prima dell'inserimento finale nella tabella di destinazione.

Fare riferimento alle rispettive sezioni su come configurare in Azure Data Factory e sulle procedure consigliate.

### <a name="append-data"></a>Accodare dati

L'aggiunta di dati è il comportamento predefinito di questo connettore sink del database SQL di Azure.Appending data is the default behavior of this Azure SQL Database sink connector. Azure Data Factory esegue un inserimento bulk per scrivere nella tabella in modo efficiente. È possibile configurare l'origine e il sink di conseguenza nell'attività di copia.

### <a name="upsert-data"></a>Eseguire l'upsert dei dati

**Opzione 1:** Quando si dispone di una grande quantità di dati da copiare, utilizzare il seguente approccio per eseguire un upsert: 

- In primo luogo, utilizzare una [tabella temporanea con ambito database](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=azuresqldb-current#database-scoped-global-temporary-tables-azure-sql-database) per eseguire il caricamento bulk di tutti i record utilizzando l'attività di copia. Poiché le operazioni sulle tabelle temporanee con ambito database non vengono registrate, è possibile caricare milioni di record in pochi secondi.
- Eseguire un'attività di stored procedure in Azure Data Factory per applicare un'istruzione [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) o INSERT/UPDATE. Utilizzare la tabella temporanea come origine per eseguire tutti gli aggiornamenti o gli inserimenti come singola transazione. In questo modo, il numero di round trip e operazioni di log viene ridotto. Al termine dell'attività della stored procedure, la tabella temporanea può essere troncata per essere pronta per il ciclo di ripresa successivo.

Ad esempio, in Azure Data Factory è possibile creare una pipeline con **un'attività di copia** concatenata a un'attività Stored **procedure**. Precedente copia i dati dall'archivio di origine in una tabella temporanea del database SQL di Azure, ad esempio **#UpsertTempTable ,** come nome della tabella nel set di dati. Quindi quest'ultima richiama una stored procedure per unire i dati di origine dalla tabella temporanea nella tabella di destinazione e pulire la tabella temporanea.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Nel database definire una stored procedure con logica MERGE, come nell'esempio seguente, a cui si fa riferimento dall'attività precedente della stored procedure. Si supponga che la destinazione sia la tabella **Marketing** con tre colonne: **ProfileID**, **State**e **Category**. Eseguire l'upsert in base alla colonna **ProfileID.**

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING ##UpsertTempTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE ##UpsertTempTable
END
```

**Opzione 2:** È inoltre possibile scegliere di [richiamare una stored procedure all'interno dell'attività](#invoke-a-stored-procedure-from-a-sql-sink)di copia. Questo approccio esegue ogni batch `writeBatchSize` (come regolato dalla proprietà ) nella tabella di origine anziché utilizzare l'inserimento bulk come approccio predefinito nell'attività di copia.

### <a name="overwrite-the-entire-table"></a>Sovrascrivere l'intera tabella

È possibile configurare la proprietà **preCopyScript** nel sink dell'attività di copia. In questo caso, per ogni attività di copia eseguita, Azure Data Factory esegue prima lo script. Quindi esegue la copia per inserire i dati. Ad esempio, per sovrascrivere l'intera tabella con i dati più recenti, specificare uno script per eliminare tutti i record prima di caricare in blocco i nuovi dati dall'origine.

### <a name="write-data-with-custom-logic"></a>Scrivere dati con logica personalizzataWrite data with custom logic

I passaggi per scrivere dati con logica personalizzata sono simili a quelli descritti nella sezione Dati [Upsert.](#upsert-data) Quando è necessario applicare l'elaborazione aggiuntiva prima dell'inserimento finale dei dati di origine nella tabella di destinazione, per la scalabilità di grandi dimensioni, è possibile eseguire una delle due operazioni seguenti:When you need to apply extra processing before the final insertion of source data into the destination table, for large scale, you can do one of two things:

- Caricare in una tabella temporanea con ambito database e quindi richiamare una stored procedure. 
- Richiamare una stored procedure durante la copia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Richiamare una stored procedure da un sink SQL

Quando si copiano dati nel database SQL di Azure, è anche possibile configurare e richiamare una stored procedure specificata dall'utente con parametri aggiuntivi in ogni batch della tabella di origine. La funzionalità della stored procedure sfrutta i parametri con valori di [tabella.](https://msdn.microsoft.com/library/bb675163.aspx)

È possibile usare una stored procedure quando non si possono usare i meccanismi di copia predefiniti. Un esempio è quando si desidera applicare un'elaborazione aggiuntiva prima dell'inserimento finale dei dati di origine nella tabella di destinazione. Alcuni esempi di elaborazione aggiuntivi sono quando si desidera unire le colonne, cercare valori aggiuntivi e inserire in più di una tabella.

L'esempio seguente illustra come usare una stored procedure per eseguire un'operazione di upsert in una tabella del database SQL di Azure. Si supponga che i dati di input e la tabella **marketing** sink dispongano ognuna di tre colonne: **ProfileID**, **State**e **Category**. Eseguire l'upsert in base alla colonna **ProfileID** e applicarla solo per una categoria specifica denominata "ProductA".

1. Nel database definire il tipo di tabella con lo stesso nome di **sqlWriterTableType**. Lo schema del tipo di tabella è identico allo schema restituito dai dati di input.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Nel database definire la stored procedure con lo stesso nome **di sqlWriterStoredProcedureName**. che gestisce i dati di input dell'origine specificata e li unisce nella tabella di output. Il nome del parametro del tipo di tabella nella stored procedure è lo stesso **tableName** definito nel set di dati.

    ```sql
    CREATE PROCEDURE spOverwriteMarketing @Marketing [dbo].[MarketingType] READONLY, @category varchar(256)
    AS
    BEGIN
    MERGE [dbo].[Marketing] AS target
    USING @Marketing AS source
    ON (target.ProfileID = source.ProfileID and target.Category = @category)
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT MATCHED THEN
        INSERT (ProfileID, State, Category)
        VALUES (source.ProfileID, source.State, source.Category);
    END
    ```

3. In Azure Data Factory definire la sezione **del sink SQL** nell'attività di copia come segue:In Azure Data Factory, define the SQL sink section in the copy activity as follows:

    ```json
    "sink": {
        "type": "AzureSqlSink",
        "sqlWriterStoredProcedureName": "spOverwriteMarketing",
        "storedProcedureTableTypeParameterName": "Marketing",
        "sqlWriterTableType": "MarketingType",
        "storedProcedureParameters": {
            "category": {
                "value": "ProductA"
            }
        }
    }
    ```

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Quando si trasformano i dati nel mapping del flusso di dati, è possibile leggere e scrivere nelle tabelle del database SQL di Azure.When transforming data in mapping data flow, you can read and write to tables from Azure SQL Database. Per altre informazioni, vedere la [trasformazione dell'origine](data-flow-source.md) e la [trasformazione sink](data-flow-sink.md) nel mapping dei flussi di dati.

### <a name="source-transformation"></a>Trasformazione della fonte

Le impostazioni specifiche del database SQL di Azure sono disponibili nella scheda **Opzioni di origine** della trasformazione di origine. 

**Ingresso:** Selezionare se puntare l'origine in ```Select * from <table-name>```corrispondenza di una tabella (equivalente di ) o immettere una query SQL personalizzata.

**Query**: se si seleziona Query nel campo di immissione, immettere una query SQL per l'origine. Questa impostazione esegue l'override di qualsiasi tabella scelta nel set di dati. **Le** clausole Order By non sono supportate in questo caso, ma è possibile impostare un'istruzione SELECT FROM completa. È inoltre possibile utilizzare funzioni di tabella definite dall'utente. **select - from udfGetData()** è una funzione definita dall'utente in SQL che restituisce una tabella. Questa query produrrà una tabella di origine che è possibile usare nel flusso di dati. L'utilizzo delle query è anche un ottimo modo per ridurre le righe per il test o per le ricerche. 

* Esempio SQL:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Dimensione batch**: Immettere una dimensione batch per suddividere i dati di grandi dimensioni in letture.

**Livello di isolamento:** l'impostazione predefinita per le origini SQL nel mapping del flusso di dati è la lettura senza commit. È possibile modificare il livello di isolamento in uno dei valori seguenti:You can change the isolation level here to one of these values:
* Read Committed
* Read Uncommitted
* Repeatable Read
* Serializable
* Nessuno (ignora livello di isolamento)

![Livello di isolamento](media/data-flow/isolationlevel.png "Livello di isolamento")

### <a name="sink-transformation"></a>Trasformazione del lanondo

Le impostazioni specifiche del database SQL di Azure sono disponibili nella scheda **Impostazioni** della trasformazione sink.

**Metodo di aggiornamento:** Determina quali operazioni sono consentite nella destinazione del database. L'impostazione predefinita è consentire solo gli inserimenti. Per aggiornare, eseguire l'upsert o eliminare le righe, è necessaria una trasformazione di modifica della riga per contrassegnare le righe per tali azioni. Per gli aggiornamenti, gli upsert e le eliminazioni, è necessario impostare una o più colonne chiave per determinare quale riga modificare.

![Colonne chiave](media/data-flow/keycolumn.png "Colonne chiave")

Il nome della colonna scelto come chiave qui verrà utilizzato da ADF come parte del successivo aggiornamento, upsert, delete. Pertanto, è necessario selezionare una colonna esistente nel mapping di sink. Se non si desidera scrivere il valore in questa colonna chiave, fare clic su "Ignora scrittura colonne chiave".

**Azione tabella:** Determina se ricreare o rimuovere tutte le righe dalla tabella di destinazione prima della scrittura.
* Nessuno: non verrà eseguita alcuna azione per la tabella.
* Ricrea: la tabella verrà eliminata e ricreata. Obbligatorio se si crea una nuova tabella in modo dinamico.
* Tronca: tutte le righe della tabella di destinazione verranno rimosse.

**Dimensioni batch**: Controlla il numero di righe scritte in ogni bucket. Dimensioni dei batch maggiori migliorano la compressione e l'ottimizzazione della memoria, ma rischiano di ridurre le eccezioni di memoria durante la memorizzazione dei dati nella cache.

**Script Pre e Post SQL**: Immettere script SQL su più righe che verranno eseguiti prima (pre-elaborazione) e dopo (post-elaborazione) i dati vengono scritti nel database sink

![pre e post script di elaborazione SQL](media/data-flow/prepost1.png "Script di elaborazione SQL")

## <a name="data-type-mapping-for-azure-sql-database"></a>Mapping dei tipi di dati per il database SQL di Azure

Quando i dati vengono copiati dal database SQL di Azure o nel database SQL di Azure, i mapping seguenti vengono usati dai tipi di dati del database SQL di Azure ai tipi di dati provvisori di Azure Data Factory.When data is copied from or to Azure SQL Database, the following mappings are used from Azure SQL Database data types to Azure Data Factory interim data types. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipi di dati del database SQL di Azure | Tipo di dati provvisorio di Azure Data Factory |
|:--- |:--- |
| bigint |Int64 |
| BINARY |Byte[] |
| bit |Boolean |
| char |String, Char[] |
| Data |Datetime |
| Datetime |Datetime |
| datetime2 |Datetime |
| Datetimeoffset |DateTimeOffset |
| Decimal |Decimal |
| FILESTREAM attribute (varbinary(max)) |Byte[] |
| Float |Double |
| image |Byte[] |
| INT |Int32 |
| money |Decimal |
| NCHAR |String, Char[] |
| ntext |String, Char[] |
| NUMERIC |Decimal |
| NVARCHAR |String, Char[] |
| real |Single |
| rowversion |Byte[] |
| smalldatetime |Datetime |
| SMALLINT |Int16 |
| SMALLMONEY |Decimal |
| sql_variant |Oggetto |
| text |String, Char[] |
| time |TimeSpan |
|  timestamp |Byte[] |
| TINYINT |Byte |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> Per i tipi di dati associati al tipo provvisorio Decimal, Azure Data Factory supporta attualmente la precisione fino a 28. Se si dispone di dati con precisione maggiore di 28, è consigliabile eseguire la conversione in una stringa nella query SQL.

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per informazioni dettagliate sulle proprietà, controllare [Attività GetMetadataTo](control-flow-get-metadata-activity.md) learn details about the properties, check GetMetadata activity 

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [Formati e archivi dati supportati.](copy-activity-overview.md#supported-data-stores-and-formats)
