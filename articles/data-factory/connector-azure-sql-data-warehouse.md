---
title: Copiare e trasformare i dati in Azure Synapse AnalyticsCopy and transform data in Azure Synapse Analytics
description: Informazioni su come copiare i dati da e verso Azure Synapse Analytics e trasformare i dati in Azure Synapse Analytics usando Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/25/2020
ms.openlocfilehash: 7fb1560fb9be809d816dde7dd69f1ec8afe5649f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417570"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiare e trasformare i dati in Azure Synapse Analytics (in precedenza Azure SQL Data Warehouse) usando Azure Data FactoryCopy and transform data in Azure Synapse Analytics (formerly Azure SQL Data Warehouse) by using Azure Data Factory 

> [!div class="op_single_selector" title1="Selezionare la versione del servizio Data Factory in uso:"]
> * [Versione1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versione corrente](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Analisi synapse di Azure e usare il flusso di dati per trasformare i dati in Archiviazione di Azure Data Lake Gen2.This article outlines how to use Copy Activity in Azure Data Factory to copy data from and to Azure Synapse Analytics, and use Data Flow to transform data in Azure Data Lake Storage Gen2. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).


[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Azure SQL Data Warehouse e usare Flusso di dati per trasformare i dati in Azure Data Lake Storage Gen2.This article outlines how to use Copy Activity in Azure Data Factory to copy data from and to Azure SQL Data Warehouse, and use Data Flow to transform data in Azure Data Lake Storage Gen2. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Analisi synapse di Azure e usare il flusso di dati per trasformare i dati in Archiviazione di Azure Data Lake Gen2.This article outlines how to use Copy Activity in Azure Data Factory to copy data from and to Azure Synapse Analytics, and use Data Flow to transform data in Azure Data Lake Storage Gen2. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di Analisi Synapse di Azure è supportato per le attività seguenti:This Azure Synapse Analytics connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con la tabella [di matrice di origine/sink supportataCopy activity with supported source/sink matrix](copy-activity-overview.md) table
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

Per l'attività Copia, questo connettore di Analisi Synapse di Azure supporta queste funzioni:For Copy activity, this Azure Synapse Analytics connector supports these functions:

- La copia di dati tramite l'autenticazione SQL e l'autenticazione token dell'applicazione Azure Active Directory (Azure AD) con entità servizio o identità gestite per le risorse di Azure.
- Come origine, il recupero di dati tramite query SQL o stored procedure.
- Come sink, caricare i dati utilizzando [l'istruzione PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) o [COPY](#use-copy-statement) (anteprima) o l'inserimento bulk. Si consiglia l'istruzione PolyBase o COPY (anteprima) per migliorare le prestazioni di copia.

> [!IMPORTANT]
> Se si copiano i dati tramite il runtime di integrazione di Azure Data Factory, configurare un [firewall del server SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) per consentire ai servizi di Azure di accedere al server.
> Se si copiano i dati tramite un runtime di integrazione self-hosted, configurare il firewall del server SQL di Azure per consentire l'intervallo IP appropriato. Questo intervallo include l'indirizzo IP del computer usato per connettersi ad Azure Synapse Analytics.This range includes the machine's IP that is used to connect to Azure Synapse Analytics.

## <a name="get-started"></a>Introduzione

> [!TIP]
> To achieve best performance, use PolyBase to load data into Azure Synapse Analytics. La sezione [Usare PolyBase per caricare i dati in Analisi synapse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) di Azure include dettagli. Per una procedura dettagliata con un caso di utilizzo, vedere Caricare 1 TB in Analisi synapse di Azure in meno di 15 minuti con Azure Data Factory.For a walkthrough with a use case, see [Load 1 TB into Azure Synapse Analytics under 15 minutes with Azure Data Factory.](load-azure-sql-data-warehouse.md)

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Nelle sezioni seguenti vengono fornite informazioni dettagliate sulle proprietà che definiscono le entità di Data Factory specifiche di un connettore di Analisi synapse di Azure.The following sections provide details about properties that define Data Factory entities specific to an Azure Snapse Analytics connector.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Le proprietà seguenti sono supportate per un servizio collegato Azure Synapse Analytics:The following properties are supported for an Azure Synapse Analytics linked service:

| Proprietà            | Descrizione                                                  | Obbligatoria                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | La proprietà type deve essere impostata su **AzureSqlDW**.             | Sì                                                          |
| connectionString    | Specificare le informazioni necessarie per connettersi all'istanza di Azure Synapse Analytics per la proprietà **connectionString.** <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire la password/chiave entità servizio in Azure Key Vault e, se si tratta dell'autenticazione SQL, estrarre la configurazione `password` dalla stringa di connessione. Vedere gli esempi JSON sotto la tabella e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. | Sì                                                          |
| servicePrincipalId  | Specificare l'ID client dell'applicazione.                         | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| tenant              | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| connectVia          | Runtime [di integrazione](concepts-integration-runtime.md) da utilizzare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No                                                           |

Per altri tipi di autenticazione, fare riferimento alle sezioni seguenti relative, rispettivamente, ai prerequisiti e agli esempi JSON:

- [Autenticazione SQL](#sql-authentication)
- Autenticazione token dell'applicazione Azure AD: [entità servizio](#service-principal-authentication)
- Autenticazione token dell'applicazione Azure AD: [identità gestite per le risorse di AzureAzure](#managed-identity) AD application token authentication: Managed identities for Azure resources

>[!TIP]
>Se viene restituito l'errore con codice "UserErrorFailedToConnectToSqlServer" e un messaggio quale "Il limite di sessioni per il database è XXX ed è stato raggiunto.", aggiungere `Pooling=false` alla stringa di connessione e riprovare.

### <a name="sql-authentication"></a>Autenticazione SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Esempio di servizio collegato tramite l'autenticazione SQL

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

**Password in Azure Key Vault:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30",
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Per usare l'autenticazione token dell'applicazione Azure AD basata sull'entità servizio, seguire questa procedura:

1. **[Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** nel portale di Azure. Prendere nota del nome dell'applicazione e dei valori seguenti che definiscono il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. **[Effettuare il provisioning di un amministratore di Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** per il server SQL Azure nel portale di Azure, se non è già stato effettuato. L'amministratore di Azure AD può essere un utente di Azure AD o gruppo di Azure AD. Se si concede al gruppo con identità gestita un ruolo di amministratore, ignorare i passaggi 3 e 4. L'amministratore avrà accesso completo al database.

3. **[Creare utenti del database indipendente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** per l'entità servizio. Connettersi al data warehouse da o in cui si vogliono copiare i dati usando strumenti come SSMS, con un'identità di Azure AD che abbia almeno l'autorizzazione ALTER ANY USER. Eseguire il codice T-SQL seguente:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Concedere all'entità servizio le autorizzazioni necessarie**, come si fa di norma per gli utenti SQL o altri utenti. Eseguire il codice seguente o fare riferimento ad altre opzioni [qui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se si desidera utilizzare PolyBase per caricare i dati, apprendere [l'autorizzazione di database necessaria.](#required-database-permission)

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Configurare un servizio collegato Analisi synapse di Azure** in Azure Data Factory.Configure an Azure Synapse Analytics linked service in Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Esempio di servizio collegato tramite l'autenticazione basata su entità servizio

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la factory specifica. È possibile usare questa identità gestita per l'autenticazione di Azure Synapse Analytics.You can use this managed identity for Azure Synapse Analytics authentication. La factory designata può accedere ai dati e copiarli da o nel data warehouse tramite questa identità.

Per utilizzare l'autenticazione dell'identità gestita, attenersi alla seguente procedura:

1. **[Effettuare il provisioning di un amministratore di Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** per il server SQL Azure nel portale di Azure, se non è già stato effettuato. L'amministratore di Azure AD può essere un utente di Azure AD o gruppo di Azure AD. Se si concede al gruppo con identità gestita un ruolo di amministratore, ignorare i passaggi 3 e 4. L'amministratore avrà accesso completo al database.

2. **[Creare utenti](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** di database indipendente per l'identità gestita di Data Factory.Create contained database users for the Data Factory Managed Identity. Connettersi al data warehouse da o in cui si vogliono copiare i dati usando strumenti come SSMS, con un'identità di Azure AD che abbia almeno l'autorizzazione ALTER ANY USER. Eseguire il comando in T-SQL seguente. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Concedere all'identità gestita di Data Factory le autorizzazioni necessarie** come di consueto per gli utenti SQL e altri utenti. Eseguire il codice seguente o fare riferimento ad altre opzioni [qui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se si desidera utilizzare PolyBase per caricare i dati, apprendere [l'autorizzazione di database necessaria.](#required-database-permission)

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Configurare un servizio collegato Analisi synapse di Azure** in Azure Data Factory.Configure an Azure Synapse Analytics linked service in Azure Data Factory.

**Esempio:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di](concepts-datasets-linked-services.md) dati. 

Le proprietà seguenti sono supportate per il set di dati di Azure Synapse Analytics:The following properties are supported for Azure Synapse Analytics dataset:

| Proprietà  | Descrizione                                                  | Obbligatoria                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | La proprietà **type** del dataset deve essere impostata su **AzureSqlDWTable**. | Sì                         |
| schema | Nome dello schema. |No per l'origine, Sì per il sink  |
| tabella | Nome della tabella/vista. |No per l'origine, Sì per il sink  |
| tableName | Nome della tabella/vista con schema. Questa proprietà è supportata per compatibilità con le versioni precedenti. Per un nuovo `schema` `table`carico di lavoro, utilizzare e . | No per l'origine, Sì per il sink |

#### <a name="dataset-properties-example"></a>Esempio di proprietà dei set di dati

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure Synapse Analytics linked service name>",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione fornisce un elenco delle proprietà supportate dall'origine e dal sink di Azure Synapse Analytics.This section provides a list of properties supported by the Azure Synapse Analytics source and sink.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics come origine

Per copiare i dati da Azure Synapse Analytics, impostare la proprietà **type** nell'origine dell'attività di copia **su SqlDWSource**. Nella sezione Copia origine attività sono supportate le proprietà seguenti:The following properties are supported in the Copy Activity **source** section:

| Proprietà                     | Descrizione                                                  | Obbligatoria |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | La proprietà **type** dell'origine dell'attività di copia deve essere impostata **su SqlDWSource**. | Sì      |
| sqlReaderQuery               | Usare la query SQL personalizzata per leggere i dati. Esempio: `select * from MyTable`. | No       |
| sqlReaderStoredProcedureName | Nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. | No       |
| storedProcedureParameters    | Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No       |
| isolationLevel | Specifica il comportamento di blocco delle transazioni per l'origine SQL. I valori consentiti sono: **ReadCommitted** (impostazione predefinita), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Fare riferimento a [questo documento](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) per ulteriori dettagli. | No |

**Esempio: uso della query SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Esempio: uso della stored procedure**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure SQL DW input dataset name>",
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
                "type": "SqlDWSource",
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

**Stored procedure di esempio:**

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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a>Analisi synapse di Azure come sinkAzure Synapse Analytics as sink

Azure Data Factory supports three ways to load data into SQL Data Warehouse.

![Opzioni di copia sink SQL DW](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Utilizzare PolyBase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [Utilizzare l'istruzione COPY (anteprima)Use COPY statement (preview)](#use-copy-statement)
- Usa inserimento bulk

Il modo più veloce e scalabile per caricare i dati è tramite [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) o [l'istruzione COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (anteprima).

Per copiare dati in Azure SQL Data Warehouse, impostare il tipo di sink nell'attività di copia su **SqlDWSink**. Nella sezione Sink attività di copia sono supportate le proprietà seguenti:The following properties are supported in the Copy Activity **sink** section:

| Proprietà          | Descrizione                                                  | Obbligatoria                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | La proprietà **type** del sink Attività di copia deve essere impostata su **SqlDWSink**. | Sì                                           |
| allowPolyBase     | Indica se utilizzare PolyBase per caricare i dati in SQL Data Warehouse. `allowCopyCommand`e `allowPolyBase` non possono essere entrambi veri. <br/><br/>Per informazioni su vincoli e dettagli, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) .<br/><br/>I valori consentiti sono **True** e **False** (predefinito). | No.<br/>Applicare quando si utilizza PolyBase.Apply when using PolyBase.     |
| polyBaseSettings  | Gruppo di proprietà che è `allowPolybase` possibile specificare quando la proprietà è impostata su **true**. | No.<br/>Applicare quando si utilizza PolyBase.Apply when using PolyBase. |
| allowCopyCommand (comando AllowCopyCommand) | Indica se utilizzare [l'istruzione COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (anteprima) per caricare i dati in SQL Data Warehouse. `allowCopyCommand`e `allowPolyBase` non possono essere entrambi veri. <br/><br/>Per i vincoli e i dettagli, vedere [Usare l'istruzione COPY per caricare i dati nella](#use-copy-statement) sezione Azure SQL Data Warehouse.See Use COPY statement to load data into Azure SQL Data Warehouse section for constraints and details.<br/><br/>I valori consentiti sono **True** e **False** (predefinito). | No.<br>Applicare quando si utilizza COPY. |
| copyCommandSettings (impostazioni di comando) | Gruppo di proprietà che è `allowCopyCommand` possibile specificare quando la proprietà è impostata su TRUE. | No.<br/>Applicare quando si utilizza COPY. |
| writeBatchSize    | Numero di righe da inserire nella tabella SQL **per batch.**<br/><br/>Il valore consentito è **integer** (numero di righe). Per impostazione predefinita, Data Factory determina dinamicamente le dimensioni del batch appropriate in base alle dimensioni della riga. | No.<br/>Applicare quando si utilizza l'inserimento bulk.     |
| writeBatchTimeout | Tempo di attesa per il completamento dell'operazione di inserimento batch prima del timeout.<br/><br/>Il valore consentito è **timespan**. Ad esempio: "00:30:00" (30 minuti). | No.<br/>Applicare quando si utilizza l'inserimento bulk.        |
| preCopyScript     | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati in Azure SQL Data Warehouse ad ogni esecuzione. Usare questa proprietà per pulire i dati precaricati. | No                                            |
| Tableoption | Specifica se creare automaticamente la tabella sink se non esiste in base allo schema di origine. La creazione automatica della tabella non è supportata quando la copia in fasi è configurata nell'attività di copia. I valori `none` consentiti `autoCreate`sono: (predefinito), . |No |
| disableMetricsCollection (insieme di disableMetricsCollection) | Data Factory raccoglie metriche quali DKU di SQL Data Warehouse per l'ottimizzazione delle prestazioni di copia e suggerimenti. Se si è interessati `true` a questo comportamento, specificare di disattivarlo. | No (il valore predefinito è `false`) |

#### <a name="sql-data-warehouse-sink-example"></a>Esempio di sink di SQL Data Warehouse

```json
"sink": {
    "type": "SqlDWSink",
    "allowPolyBase": true,
    "polyBaseSettings":
    {
        "rejectType": "percentage",
        "rejectValue": 10.0,
        "rejectSampleValue": 100,
        "useTypeDefault": true
    }
}
```

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Usare PolyBase per caricare dati in Azure SQL Data Warehouse

L'uso di [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) è un modo efficiente per caricare una grande quantità di dati in Analisi Synapse di Azure con una velocità effettiva elevata. L'uso di PolyBase consente un miglioramento significativo della velocità effettiva rispetto al meccanismo BULKINSERT predefinito. Per una procedura dettagliata con un caso d'uso, vedere [Caricare 1 TB in Analisi synapse](v1/data-factory-load-sql-data-warehouse.md)di Azure.

* Se i dati di origine si trova in BLOB di **Azure, Azure Data Lake Storage Gen1 o Azure Data Lake Storage Gen2**e il **formato è compatibile con PolyBase**, è possibile usare l'attività di copia per richiamare direttamente PolyBase per consentire ad Azure SQL Data Warehouse di estrarre i dati dall'origine. Per maggiori dettagli, vedere **[Copia diretta tramite PolyBase](#direct-copy-by-using-polybase)**.
* Se l'archivio e il formato dei dati di origine non sono supportati in origine da PolyBase, usare la funzionalità **[copia di staging tramite PolyBase](#staged-copy-by-using-polybase)**. La funzionalità copia di staging assicura inoltre una migliore velocità effettiva, Converte automaticamente i dati nel formato compatibile con PolyBase, archivia i dati nell'archiviazione BLOB di Azure., quindi chiama PolyBase per caricare i dati in SQL Data Warehouse.

>[!TIP]
>Ulteriori informazioni sulle [procedure consigliate per l'utilizzo di PolyBase](#best-practices-for-using-polybase).

Le seguenti impostazioni PolyBase `polyBaseSettings` sono supportate in nell'attività di copia:

| Proprietà          | Descrizione                                                  | Obbligatoria                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Specifica il numero o la percentuale di righe che è possibile rifiutare prima che la query abbia esito negativo.<br/><br/>Per ulteriori informazioni sulle opzioni di rifiuto polyBase, vedere la sezione Arguments di [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>I valori consentiti sono 0 (predefinito), 1, 2 e così via. | No                                            |
| rejectType        | Indica se l'opzione **rejectValue** viene specificata come valore letterale o come percentuale.<br/><br/>I valori consentiti sono **Valore** (impostazione predefinita) e **Percentuale**. | No                                            |
| rejectSampleValue | Determina il numero di righe da recuperare prima che PolyBase ricalcoli la percentuale di righe rifiutate.<br/><br/>I valori consentiti sono 1, 2 e così via. | Sì se **rejectType** è **percentage**. |
| useTypeDefault    | Specifica come gestire valori mancanti nei file di testo delimitato quando PolyBase recupera i dati dal file di testo.<br/><br/>Per altre informazioni su questa proprietà, vedere la sezione Arguments (Argomenti) in [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>I valori consentiti sono **True** e **False** (predefinito).<br><br> | No                                            |

### <a name="direct-copy-by-using-polybase"></a>Copia diretta tramite PolyBase

SQL Data Warehouse PolyBase supporta direttamente BLOB di Azure, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2. Se i dati di origine soddisfano i criteri descritti in questa sezione, usare PolyBase per copiare direttamente dall'archivio dati di origine ad Azure Synapse Analytics.If your source data meets the criteria described in this section, use PolyBase to copy directly from the source data store to Azure Synapse Analytics. In caso contrario, usare la [copia di staging tramite PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Per copiare i dati in modo efficiente in SQL Data Warehouse, altre informazioni da [Azure Data Factory rende ancora più semplice e conveniente scoprire informazioni dettagliate dai dati quando si usa Data Lake Store con SQL Data Warehouse.](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/)

Se i requisiti non vengono soddisfatti, Azure Data Factory controlla le impostazioni e usa automaticamente il meccanismo BULKINSERT per lo spostamento dei dati.

1. Il **servizio collegato di origine** è con i tipi e i metodi di autenticazione seguenti:The source linked service is with the following types and authentication methods:

    | Tipo di archivio dati di origine supportatoSupported source data store type                             | Tipo di autenticazione di origine supportato                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [BLOB Azure](connector-azure-blob-storage.md)                | Autenticazione con chiave dell'account, autenticazione dell'identità gestita |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Autenticazione di un'entità servizio                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Autenticazione con chiave dell'account, autenticazione dell'identità gestita |

    >[!IMPORTANT]
    >Se Archiviazione di Azure è configurata con l'endpoint del servizio VNet, è necessario usare l'autenticazione delle identità gestite: fare riferimento [all'impatto dell'uso degli endpoint del servizio VNet con archiviazione di Azure.If](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)your Azure Storage is configured with VNet service endpoint, you must use managed identity authentication - refer to Impact of using VNet service Endpoints with Azure storage. Informazioni sulle configurazioni necessarie in Data Factory da BLOB di [Azure - autenticazione dell'identità gestita](connector-azure-blob-storage.md#managed-identity) e Azure Data Lake Storage Gen2 - sezione relativa rispettivamente sull'autenticazione [dell'identità gestita.](connector-azure-data-lake-storage.md#managed-identity)

2. Il formato dei dati di **origine** è **Parquet**, **ORC**o **Testo delimitato**, con le seguenti configurazioni:

   1. Il percorso della cartella non contiene filtri con caratteri jolly.
   2. Il nome del file è vuoto o punta a un singolo file. Se si specifica il nome del file `*` con `*.*`caratteri jolly nell'attività di copia, può essere solo o .
   3. `rowDelimiter`è **l'impostazione predefinita**, **n**, n , **n**, n , o **.**
   4. `nullValue`viene lasciato come predefinito o impostato su `treatEmptyAsNull` una stringa **vuota** (""), e viene lasciato come predefinito o impostato su true.
   5. `encodingName`viene lasciato come predefinito o impostato su **utf-8**.
   6. `quoteChar`, `escapeChar`, `skipLineCount` e non sono specificati. PolyBase supporta la riga di intestazione skip, che può essere configurata come `firstRowAsHeader` in ADF.
   7. `compression` può essere **no compression**, **GZip** o **Deflate**.

3. Se l'origine è `recursive` una cartella, nell'attività di copia deve essere impostata su true.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` e non sono specificati.

>[!NOTE]
>Se l'origine è una cartella, PolyBase recupera i file dalla cartella e da tutte le relative sottocartelle e non recupera i dati dai file per i quali il nome del file inizia con una sottolineatura (_) o un punto (.), come documentato [qui - argomento LOCATION](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            }
        }
    }
]
```

### <a name="staged-copy-by-using-polybase"></a>Copia di staging tramite PolyBase

Quando i dati di origine non sono compatibili in modo nativo con PolyBase, abilitare la copia dei dati tramite un'istanza di archiviazione BLOB di Azure con gestione temporanea provvisoria (non può essere Archiviazione Premium di Azure). In this case, Azure Data Factory automatically converts the data to meet the data format requirements of PolyBase. Quindi richiama PolyBase per caricare i dati in SQL Data Warehouse. Infine, pulisce i dati temporanei dall'archiviazione BLOB. Per informazioni dettagliate sulla copia dei dati tramite un'istanza di Archiviazione BLOB di Azure di staging, vedere [Copia di staging](copy-activity-performance-features.md#staged-copy).

Per usare questa funzionalità, creare un [servizio collegato Archiviazione BLOB](connector-azure-blob-storage.md#linked-service-properties) di Azure che fa riferimento all'account di archiviazione di Azure con l'archiviazione BLOB provvisoria. Specificare `enableStaging` quindi `stagingSettings` le proprietà e per l'attività di copia, come illustrato nel codice seguente.

>[!IMPORTANT]
>Se l'endpoint del servizio Di gestione virtuale è configurato con Archiviazione di Azure, è necessario usare l'autenticazione delle identità gestite: fare riferimento [all'impatto dell'uso degli endpoint del servizio VNet con Archiviazione di Azure.If](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)your staging Azure Storage is configured with VNet service endpoint, you must use managed identity authentication - refer to Impact of using VNet service Endpoints with Azure storage. Informazioni sulle configurazioni necessarie in Data Factory da [Azure Blob - autenticazione dell'identità gestita](connector-azure-blob-storage.md#managed-identity).

```json
"activities":[
    {
        "name": "CopyFromSQLServerToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "SQLServerDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "SqlSource",
            },
            "sink": {
                "type": "SqlDWSink",
                "allowPolyBase": true
            },
            "enableStaging": true,
            "stagingSettings": {
                "linkedServiceName": {
                    "referenceName": "MyStagingBlob",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>Procedure consigliate per l'uso di PolyBase

Nelle sezioni seguenti vengono fornite le procedure consigliate oltre a quelle menzionate in [Procedure consigliate per Analisi synapse](../synapse-analytics/sql/best-practices-sql-pool.md)di Azure.

#### <a name="required-database-permission"></a>Autorizzazione database obbligatoria

Per usare PolyBase, l'utente che carica i dati in SQL Data Warehouse deve disporre dell'[autorizzazione "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) nel database di destinazione. Un modo per ottenere questo risultato consiste nell'aggiungere l'utente come membro del ruolo **db_owner**. Informazioni su come eseguire questa operazione sono disponibili in [Panoramica di SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Limitazioni alle dimensioni delle righe e al tipo di dati

Le operazioni di caricamento di PolyBase sono limitate alle righe inferiori a 1 MB, Non può essere utilizzato per caricare in VARCHR(MAX), NVARCHAR(MAX) o VARBINARY(MAX). Per altre informazioni, vedere [Limiti di capacità di SQL Data Warehouse](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Quando i dati di origine hanno righe di dimensioni superiori a 1 MB, è consigliabile suddividere verticalmente le tabelle di origine in tabelle più piccole. Assicurarsi che le dimensioni massime di ogni riga non superino il limite previsto. Le tabelle più piccole possono quindi essere caricate usando PolyBase e unite insieme in Analisi synapse di Azure.The smaller tables can then be loaded by using PolyBase and merged together in Azure Synapse Analytics.

In alternativa, per i dati con colonne così ampie, è possibile utilizzare non PolyBase per caricare i dati utilizzando ADF, disattivando l'impostazione "allow PolyBase".

#### <a name="sql-data-warehouse-resource-class"></a>Classe di risorse di SQL Data Warehouse

Per ottenere la migliore velocità effettiva possibile, assegnare una classe di risorse più ampia all'utente che carica i dati in SQL Data Warehouse tramite PolyBase.

#### <a name="polybase-troubleshooting"></a>Risoluzione dei problemi di PolyBase

**Caricamento nella colonna Decimale**

Se i dati di origine sono in formato testo o altri archivi non compatibili con PolyBase (utilizzando la copia in fasi e PolyBase) e contiene un valore vuoto da caricare nella colonna Decimale di SQL Data Warehouse, è possibile che venga visualizzato il seguente errore:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

La soluzione consiste nel deselezionare l'opzione "**Usa tipo predefinito**" (come false) nel sink di attività di copia -> impostazioni PolyBase. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" è una configurazione nativa PolyBase, che specifica come gestire i valori mancanti nei file di testo delimitati quando PolyBase recupera i dati dal file di testo. 

**`tableName`in Azure Synapse Analytics**

La tabella seguente fornisce alcuni esempi di come specificare la proprietà **tableName** nel set di dati JSON, mostrando diverse combinazioni di nomi di schema e di tabella.

| Schema di database | Nome tabella | Proprietà JSON **tableName**               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable o dbo.MyTable o [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable o [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] o [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

Se viene visualizzato l'errore seguente, il problema potrebbe essere costituito dal valore specificato per la proprietà **tableName**. Per informazioni sul modo corretto di specificare i valori per la proprietà JSON **tableName**, vedere la tabella precedente.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Colonne con valori predefiniti**

Attualmente, la funzionalità PolyBase in Data Factory accetta solo lo stesso numero di colonne disponibili nella tabella di destinazione. Nel caso di una tabella con quattro colonne di cui una definita con un valore predefinito, ad esempio, i dati di input devono comunque contenere quattro colonne. Un set di dati di input con tre colonne restituisce un errore simile al messaggio seguente:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Il valore NULL è una forma speciale di valore predefinito. Se la colonna ammette valori Null, i dati di input nel BLOB per tale colonna possono essere vuoti, ma non possono essere mancanti dal set di dati di input. PolyBase inserisce NULL per i valori mancanti in Azure Synapse Analytics.

## <a name="use-copy-statement-to-load-data-into-azure-sql-data-warehouse-preview"></a><a name="use-copy-statement"></a>Usare l'istruzione COPY per caricare dati in Azure SQL Data Warehouse (anteprima)Use COPY statement to load data into Azure SQL Data Warehouse (preview)

[L'istruzione COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (anteprima) del data warehouse SQL supporta direttamente il caricamento dei dati da Azure Blob e Azure Data Lake **Storage Gen2.** Se i dati di origine soddisfano i criteri descritti in questa sezione, è possibile scegliere di usare l'istruzione COPY in ADF per caricare i dati in Azure SQL Data Warehouse.If your source data meets the criteria described in this section, you can choose to use COPY statement in ADF to load data into Azure SQL Data Warehouse. Azure Data Factory controlla le impostazioni e non riesce l'esecuzione dell'attività di copia se i criteri non vengono soddisfatti.

>[!NOTE]
>Attualmente Data Factory supporta solo la copia da fonti compatibili con l'istruzione COPY menzionate di seguito.

L'utilizzo dell'istruzione COPY supporta la configurazione seguente:

1. Il **servizio collegato di origine e** il formato sono con i tipi e i metodi di autenticazione seguenti:The source linked service and format are with the following types and authentication methods:

    | Tipo di archivio dati di origine supportatoSupported source data store type                             | Formato supportato           | Tipo di autenticazione di origine supportato                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [BLOB Azure](connector-azure-blob-storage.md)                | [Testo delimitato](format-delimited-text.md)             | Autenticazione con chiave account, autenticazione della firma di accesso condiviso, autenticazione dell'entità servizio, autenticazione dell'identità gestita |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Autenticazione con chiave dell'account, autenticazione della firma di accesso condivisoAccount key authentication, shared access signature authentication |
    | &nbsp;                                                       | [Orco](format-orc.md)                        | Autenticazione con chiave dell'account, autenticazione della firma di accesso condivisoAccount key authentication, shared access signature authentication |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Testo delimitato](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[Orco](format-orc.md) | Autenticazione con chiave account, autenticazione dell'entità servizio, autenticazione dell'identità gestita |

    >[!IMPORTANT]
    >Se Archiviazione di Azure è configurata con l'endpoint del servizio VNet, è necessario usare l'autenticazione delle identità gestite: fare riferimento [all'impatto dell'uso degli endpoint del servizio VNet con archiviazione di Azure.If](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage)your Azure Storage is configured with VNet service endpoint, you must use managed identity authentication - refer to Impact of using VNet service Endpoints with Azure storage. Informazioni sulle configurazioni necessarie in Data Factory da BLOB di [Azure - autenticazione dell'identità gestita](connector-azure-blob-storage.md#managed-identity) e Azure Data Lake Storage Gen2 - sezione relativa rispettivamente sull'autenticazione [dell'identità gestita.](connector-azure-data-lake-storage.md#managed-identity)

2. Le impostazioni di formato sono con le seguenti:

   1. Per **Parquet**: `compression` non può essere **compressione**, **Snappy**, o **G-ip**.
   2. Per **ORC** `compression` : non **```zlib```** può essere **alcuna compressione**, o **Snappy**.
   3. Per **testo delimitato**:
      1. `rowDelimiter`viene impostato in modo esplicito come **carattere singolo** o "**,** il valore predefinito non è supportato.
      2. `nullValue`viene lasciato come predefinito o impostato su **una stringa vuota** ("").
      3. `encodingName`viene lasciato come predefinito o impostato su **utf-8 o utf-16**.
      4. `escapeChar`deve essere `quoteChar`uguale a e non è vuoto.
      5. `skipLineCount`viene lasciato come predefinito o impostato su 0.
      6. `compression`può essere **nessuna compressione** o **G.ip**.

3. Se l'origine è `recursive` una cartella, nell'attività di copia deve essere impostata su true.

4. `wildcardFolderPath`, `wildcardFilename` `modifiedDateTimeStart`, `modifiedDateTimeEnd` `additionalColumns` e non sono specificati.

Nell'attività di copia `allowCopyCommand` sono supportate le seguenti impostazioni dell'istruzione COPY:

| Proprietà          | Descrizione                                                  | Obbligatoria                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| valori predefiniti | Specifica i valori predefiniti per ogni colonna di destinazione in SQL DW.  I valori predefiniti nella proprietà sovrascrivono il vincolo DEFAULT impostato nel data warehouse e la colonna Identity non può avere un valore predefinito. | No |
| opzioni aggiuntive | Opzioni aggiuntive che verranno passate all'istruzione SQL DW COPY direttamente nella clausola "With" [nell'istruzione COPY](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Racchiudere il valore in base alle esigenze per l'allineamento ai requisiti dell'istruzione COPY. | No |

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaCOPY",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "ParquetDataset",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "AzureSQLDWDataset",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "ParquetSource",
                "storeSettings":{
                    "type": "AzureBlobStorageReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "SqlDWSink",
                "allowCopyCommand": true, 
                "copyCommandSettings": {
                    "defaultValues": [ 
                        { 
                            "columnName": "col_string", 
                            "defaultValue": "DefaultStringValue" 
                        }
                    ],
                    "additionalOptions": { 
                        "MAXERRORS": "10000", 
                        "DATEFORMAT": "'ymd'" 
                    }
                }
            },
            "enableSkipIncompatibleRow": true
        }
    }
]
```


## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per informazioni dettagliate sulle proprietà, controllare [Attività GetMetadataTo](control-flow-get-metadata-activity.md) learn details about the properties, check GetMetadata activity 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapping dei tipi di dati per Azure SQL Data Warehouse

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Quando si trasformano i dati nel mapping del flusso di dati, è possibile leggere e scrivere nelle tabelle di Azure Synapse Analytics.When transforming data in mapping data flow, you can read and write to tables from Azure Synapse Analytics. Per altre informazioni, vedere la [trasformazione dell'origine](data-flow-source.md) e la [trasformazione sink](data-flow-sink.md) nel mapping dei flussi di dati.

### <a name="source-transformation"></a>Trasformazione della fonte

Le impostazioni specifiche di Analisi synapse di Azure sono disponibili nella scheda **Opzioni di origine** della trasformazione di origine. 

**Ingresso:** Selezionare se puntare l'origine in ```Select * from <table-name>```corrispondenza di una tabella (equivalente di ) o immettere una query SQL personalizzata.

**Query**: se si seleziona Query nel campo di immissione, immettere una query SQL per l'origine. Questa impostazione esegue l'override di qualsiasi tabella scelta nel set di dati. **Le** clausole Order By non sono supportate in questo caso, ma è possibile impostare un'istruzione SELECT FROM completa. È inoltre possibile utilizzare funzioni di tabella definite dall'utente. **select - from udfGetData()** è una funzione definita dall'utente in SQL che restituisce una tabella. Questa query produrrà una tabella di origine che è possibile usare nel flusso di dati. L'utilizzo delle query è anche un ottimo modo per ridurre le righe per il test o per le ricerche. 

* Esempio SQL:```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Dimensione batch**: Immettere una dimensione batch per suddividere i dati di grandi dimensioni in letture. Nei flussi di dati, ADF utilizzerà questa impostazione per impostare la memorizzazione nella cache columnar Spark.In data flows, ADF will use this setting to set Spark columnar caching. Questo è un campo di opzione che utilizzerà le impostazioni predefinite di Spark se viene lasciato vuoto.

**Livello di isolamento:** l'impostazione predefinita per le origini SQL nel mapping del flusso di dati è la lettura senza commit. È possibile modificare il livello di isolamento in uno dei valori seguenti:You can change the isolation level here to one of these values:
* Read Committed
* Read Uncommitted
* Repeatable Read
* Serializable
* Nessuno (ignora livello di isolamento)

![Livello di isolamento](media/data-flow/isolationlevel.png "Livello di isolamento")

### <a name="sink-transformation"></a>Trasformazione del lanondo

Le impostazioni specifiche di Analisi synapse di Azure sono disponibili nella scheda **Impostazioni** della trasformazione sink.

**Metodo di aggiornamento:** Determina quali operazioni sono consentite nella destinazione del database. L'impostazione predefinita è consentire solo gli inserimenti. Per aggiornare, eseguire l'upsert o eliminare le righe, è necessaria una trasformazione di modifica della riga per contrassegnare le righe per tali azioni. Per gli aggiornamenti, gli upsert e le eliminazioni, è necessario impostare una o più colonne chiave per determinare quale riga modificare.

**Azione tabella:** Determina se ricreare o rimuovere tutte le righe dalla tabella di destinazione prima della scrittura.
* Nessuno: non verrà eseguita alcuna azione per la tabella.
* Ricrea: la tabella verrà eliminata e ricreata. Obbligatorio se si crea una nuova tabella in modo dinamico.
* Tronca: tutte le righe della tabella di destinazione verranno rimosse.

**Abilita gestione temporanea:** Determina se usare o meno [PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) durante la scrittura in Azure Synapse Analytics

**Dimensioni batch**: Controlla il numero di righe scritte in ogni bucket. Dimensioni dei batch maggiori migliorano la compressione e l'ottimizzazione della memoria, ma rischiano di ridurre le eccezioni di memoria durante la memorizzazione dei dati nella cache.

**Script Pre e Post SQL**: Immettere script SQL su più righe che verranno eseguiti prima (pre-elaborazione) e dopo (post-elaborazione) i dati vengono scritti nel database sink

![pre e post script di elaborazione SQL](media/data-flow/prepost1.png "Script di elaborazione SQL")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapping dei tipi di dati per Azure Synapse AnalyticsData type mapping for Azure Synapse Analytics

Quando si copiano dati da o in Analisi synapse di Azure, i mapping seguenti vengono usati dai tipi di dati di Azure Synapse Analytics ai tipi di dati provvisori di Azure Data Factory.When you copy data from or to Azure Synapse Analytics, the following mappings are used from Azure Synapse Analytics data types to Azure Data Factory interim data types. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

>[!TIP]
>Fare riferimento all'articolo [Tipi di dati di tabella nell'articolo di Analisi synapse](../synapse-analytics/sql/develop-tables-data-types.md) di Azure sui tipi di dati supportati da SQL DW e le soluzioni alternative per quelli non supportati.

| Tipo di dati di Azure Synapse AnalyticsAzure Synapse Analytics data type    | Tipo di dati provvisorio di Data Factory |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| BINARY                                | Byte[]                         |
| bit                                   | Boolean                        |
| char                                  | String, Char[]                 |
| Data                                  | Datetime                       |
| Datetime                              | Datetime                       |
| datetime2                             | Datetime                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| image                                 | Byte[]                         |
| INT                                   | Int32                          |
| money                                 | Decimal                        |
| NCHAR                                 | String, Char[]                 |
| NUMERIC                               | Decimal                        |
| NVARCHAR                              | String, Char[]                 |
| real                                  | Single                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | Datetime                       |
| SMALLINT                              | Int16                          |
| SMALLMONEY                            | Decimal                        |
| time                                  | TimeSpan                       |
| TINYINT                               | Byte                           |
| UNIQUEIDENTIFIER                      | Guid                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
