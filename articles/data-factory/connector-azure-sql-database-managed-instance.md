---
title: Copiare dati da e verso l'istanza gestita del database SQL di AzureCopy data to and from Azure SQL Database Managed Instance
description: Informazioni su come spostare i dati da e verso l'Istanza gestita di database SQL di Azure con Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 03/12/2020
ms.openlocfilehash: 11f4005e802e2a584b21903bfead2c6b9701f065
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80238747"
---
# <a name="copy-data-to-and-from-azure-sql-database-managed-instance-by-using-azure-data-factory"></a>Copiare dati da e verso l'Istanza gestita di database SQL di Azure con Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e verso l'Istanza gestita di database SQL di Azure. Si basa sull'articolo [Panoramica dell'attività](copy-activity-overview.md) di copia che presenta una panoramica generale dell'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore dell'istanza gestita del database SQL di Azure è supportato per le attività seguenti:This Azure SQL Database Managed Instance connector is supported for the following activities:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

È possibile copiare dati dall'Istanza gestita di database SQL di Azure a qualsiasi archivio dati sink supportato. È anche possibile copiare dati da qualsiasi archivio dati di origine supportato all'istanza gestita. Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare il connettore dell'Istanza gestita di database SQL di Azure supporta:

- Copia dei dati tramite l'autenticazione SQL e l'autenticazione del token dell'applicazione di Azure Active Directory (Azure AD) con un'entità servizio o identità gestite per le risorse di Azure.Copying data by using SQL authentication and Azure Active Directory (Azure AD) Application token authentication with a service principal or managed identities for Azure resources.
- Come origine, il recupero dei dati tramite una query SQL o una stored procedure.
- Come sink, l'accodamento di dati a una tabella di destinazione o la chiamata a una stored procedure con logica personalizzata durante la copia.

>[!NOTE]
>Istanza gestita database SQL di Azure [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) non è ora supportata da questo connettore. Per risolvere il problema, è possibile utilizzare un [connettore ODBC generico](connector-odbc.md) e un driver ODBC di SQL Server tramite un runtime di integrazione self-hosted. Seguire [queste indicazioni](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current) con il download del driver ODBC e le configurazioni delle stringhe di connessione.

## <a name="prerequisites"></a>Prerequisiti

Per accedere all'endpoint pubblico dell'istanza gestita del database SQL di Azure, è possibile usare un runtime di integrazione di Azure Data Factory gestito da Azure Data Factory.To access the Azure SQL Database Managed Instance [public endpoint,](../sql-database/sql-database-managed-instance-public-endpoint-securely.md)you can use an Azure Data Factory managed Azure managed Azure integration runtime. Assicurarsi di abilitare l'endpoint pubblico e consentire anche il traffico di endpoint pubblici nel gruppo di sicurezza di rete in modo che Azure Data Factory possa connettersi al database. Per ulteriori informazioni, vedere [queste linee guida](../sql-database/sql-database-managed-instance-public-endpoint-configure.md).

Per accedere all'endpoint privato dell'istanza gestita del database SQL di Azure, configurare un runtime di [integrazione self-hosted](create-self-hosted-integration-runtime.md) in grado di accedere al database. Se si esegue il provisioning del runtime di integrazione self-hosted nella stessa rete virtuale dell'istanza gestita, assicurarsi che il computer del runtime di integrazione si trovi in una subnet diversa rispetto all'istanza gestita. Se si esegue il provisioning del runtime di integrazione self-hosted in una rete virtuale diversa rispetto all'istanza gestita, è possibile usare un peering di rete virtuale o una rete virtuale alla connessione di rete virtuale. Per altre informazioni, vedere [Connettere un'applicazione a Istanza gestita di database SQL di Azure](../sql-database/sql-database-managed-instance-connect-app.md).

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Nelle sezioni seguenti vengono fornite informazioni dettagliate sulle proprietà usate per definire le entità di Azure Data Factory specifiche del connettore dell'istanza gestita del database SQL di Azure.The following sections provide details about properties that are used to define Azure Data Factory entities specific to the Azure SQL Database Managed Instance connector.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato dell'Istanza gestita di database SQL di Azure sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureSqlMI**. | Sì |
| connectionString |Questa proprietà specifica le informazioni **connectionString** necessarie per connettersi all'istanza gestita tramite l'autenticazione SQL. Per altre informazioni, vedere gli esempi seguenti. <br/>La porta predefinita è 1433. Se si usa l'istanza gestita del database SQL di Azure con un endpoint pubblico, specificare in modo esplicito la porta 3342.If you're using Azure SQL Database Managed Instance with a public endpoint, explicitly specify port 3342.<br> È anche possibile inserire una password in Archiviazione delle chiavi di Azure.You also can put a password in Azure Key Vault. Se si tratta di autenticazione SQL, estrarre la `password` configurazione dalla stringa di connessione. Per altre informazioni, vedere l'esempio JSON che segue la tabella e [Archiviare le credenziali in Archiviazione delle chiavi](store-credentials-in-key-vault.md)di Azure.For more information, see the JSON example following the table and Store credentials in Azure Key Vault . |Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì, quando si usa l'autenticazione di Azure AD con un'entità servizioYes, when you use Azure AD authentication with a service principal |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Azure Data Factory o fare riferimento a [un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'autenticazione di Azure AD con un'entità servizioYes, when you use Azure AD authentication with a service principal |
| tenant | Specificare le informazioni sul tenant, ad esempio il nome di dominio o l'ID tenant, in cui risiede l'applicazione. Recuperarlo passando il mouse nell'angolo superiore destro del portale di Azure.Retrieve it by hovering the mouse in the upper-right corner of the Azure portal. | Sì, quando si usa l'autenticazione di Azure AD con un'entità servizioYes, when you use Azure AD authentication with a service principal |
| connectVia | Questo [runtime di integrazione](concepts-integration-runtime.md) viene usato per connettersi all'archivio dati. È possibile usare un runtime di integrazione self-hosted o un runtime di integrazione di Azure se l'istanza gestita ha un endpoint pubblico e consente ad Azure Data Factory di accedervi. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |Sì |

Per altri tipi di autenticazione, fare riferimento alle sezioni seguenti relative, rispettivamente, ai prerequisiti e agli esempi JSON:

- [Autenticazione SQL](#sql-authentication)
- [Autenticazione token dell'applicazione Azure AD: entità servizioAzure AD application token authentication: Service principal](#service-principal-authentication)
- [Autenticazione token dell'applicazione Azure AD: identità gestite per le risorse di Azure](#managed-identity)

### <a name="sql-authentication"></a>Autenticazione SQL

**Esempio 1: utilizzare l'autenticazione SQLExample 1: use SQL authentication**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;Password=<password>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 2: usare l'autenticazione SQL con una password nell'insieme di credenziali delle chiavi di AzureExample 2: use SQL authentication with a password in Azure Key Vault**

```json
{
    "name": "AzureSqlMILinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;Integrated Security=False;User ID=<username>;",
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

1. Seguire i passaggi per eseguire il provisioning di un amministratore di [Azure Active Directory per l'istanza gestita.](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)

2. [Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) nel portale di Azure. Prendere nota del nome dell'applicazione e dei valori seguenti che definiscono il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

3. Creare account di accesso per l'identità gestita di Azure Data [Factory.Create logins](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) for the Azure Data Factory managed identity. In SQL Server Management Studio (SSMS) connettersi all'istanza gestita con un account SQL Server di tipo **sysadmin**. Eseguire il codice T-SQL seguente nel database **master**:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Creare utenti](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) di database indipendente per l'identità gestita di Azure Data Factory.Create contained database users for the Azure Data Factory managed identity. Connettersi al database da cui o in cui si vuole copiare i dati ed eseguire il codice T-SQL seguente: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Concedere all'identità gestita Data Factory le autorizzazioni necessarie come di consueto per gli utenti SQL e altri utenti. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Configurare un servizio collegato Istanza gestita database SQL di Azure in Azure Data Factory.Configure an Azure SQL Database Managed Instance linked service in Azure Data Factory.

**Esempio: usare l'autenticazione dell'entità servizioExample: use service principal authentication**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;",
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

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare questa identità gestita per l'autenticazione dell'istanza gestita del database SQL di Azure.You can use this managed identity for Azure SQL Database Managed Instance authentication. La factory specificata può accedere e copiare i dati dal database o nel database usando questa identità.

Per utilizzare l'autenticazione dell'identità gestita, attenersi alla seguente procedura.

1. Seguire i passaggi per eseguire il provisioning di un amministratore di [Azure Active Directory per l'istanza gestita.](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-managed-instance)

2. Creare account di accesso per l'identità gestita di Azure Data [Factory.Create logins](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) for the Azure Data Factory managed identity. In SQL Server Management Studio (SSMS) connettersi all'istanza gestita con un account SQL Server di tipo **sysadmin**. Eseguire il codice T-SQL seguente nel database **master**:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Creare utenti](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities) di database indipendente per l'identità gestita di Azure Data Factory.Create contained database users for the Azure Data Factory managed identity. Connettersi al database da cui o in cui si vuole copiare i dati ed eseguire il codice T-SQL seguente: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Concedere all'identità gestita Data Factory le autorizzazioni necessarie come di consueto per gli utenti SQL e altri utenti. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Configurare un servizio collegato Istanza gestita database SQL di Azure in Azure Data Factory.Configure an Azure SQL Database Managed Instance linked service in Azure Data Factory.

**Esempio: utilizza l'autenticazione dell'identità gestitaExample: uses managed identity authentication**

```json
{
    "name": "AzureSqlDbLinkedService",
    "properties": {
        "type": "AzureSqlMI",
        "typeProperties": {
            "connectionString": "Data Source=<hostname,port>;Initial Catalog=<databasename>;"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per definire set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati dell'Istanza gestita di database SQL di Azure.

Per copiare dati da e verso l'istanza gestita del database SQL di Azure, sono supportate le proprietà seguenti:To copy data to and from Azure SQL Database Managed Instance, the following properties are supported:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **AzureSqlMITable**. | Sì |
| schema | Nome dello schema. |No per l'origine, Sì per il sink  |
| tabella | Nome della tabella/vista. |No per l'origine, Sì per il sink  |
| tableName | Nome della tabella/vista con schema. Questa proprietà è supportata per compatibilità con le versioni precedenti. Per un nuovo `schema` `table`carico di lavoro, utilizzare e . | No per l'origine, Sì per il sink |

**Esempio**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<Managed Instance linked service name>",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per definire le attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink dell'Istanza gestita di database SQL di Azure.

### <a name="azure-sql-database-managed-instance-as-a-source"></a>Istanza gestita di database SQL di Azure come origine

Per copiare i dati dall'istanza gestita del database SQL di Azure, nella sezione dell'origine dell'attività di copia sono supportate le proprietà seguenti:To copy data from Azure SQL Database Managed Instance, the following properties are supported in the copy activity source section:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata **su SqlMISource**. | Sì |
| sqlReaderQuery |Questa proprietà usa la query SQL personalizzata per leggere i dati. Un esempio è `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Questa proprietà definisce il nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No |
| storedProcedureParameters |Questi parametri sono relativi alla stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e l'uso di maiuscole e minuscole dei parametri devono corrispondere a quelli dei parametri della stored procedure. |No |
| isolationLevel | Specifica il comportamento di blocco delle transazioni per l'origine SQL. I valori consentiti sono: **ReadCommitted** (impostazione predefinita), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Fare riferimento a [questo documento](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel) per ulteriori dettagli. | No |

**Tenere presente quanto segue:**

- Se si specifica **sqlReaderQuery** per **SqlMISource**, l'attività di copia esegue la query sull'origine dell'istanza gestita per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters**, se la stored procedure accetta parametri.
- Se non si specifica la proprietà **sqlReaderQuery** o **sqlReaderStoredProcedureName**, per creare una query vengono usate le colonne definite nella sezione "structure" del codice JSON del set di dati. La query `select column1, column2 from mytable` viene eseguita sull'istanza gestita. Se la definizione del set di dati non include "structure", vengono selezionate tutte le colonne della tabella.

**Esempio: utilizzare una query SQLExample: Use a SQL query**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "type": "SqlMISource",
                "sqlReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

**Esempio: utilizzare una stored procedureExample: Use a stored procedure**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Managed Instance input dataset name>",
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
                "type": "SqlMISource",
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

**Definizione della stored procedure**

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

### <a name="azure-sql-database-managed-instance-as-a-sink"></a>Istanza gestita di database SQL di Azure come sink

> [!TIP]
> Per altre informazioni sui comportamenti di scrittura, le configurazioni e le procedure consigliate supportati, vedere Procedure consigliate per il caricamento dei [dati nell'istanza gestita](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)del database SQL di Azure.

Per copiare i dati nell'istanza gestita del database SQL di Azure, nella sezione relativa al sink dell'attività di copia sono supportate le proprietà seguenti:To copy data to Azure SQL Database Managed Instance, the following properties are supported in the copy activity sink section:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **SqlMISink**. | Sì |
| writeBatchSize |Numero di righe da inserire nella tabella SQL *per batch.*<br/>I valori consentiti sono integer per il numero di righe. Per impostazione predefinita, Azure Data Factory determina dinamicamente le dimensioni del batch appropriate in base alle dimensioni della riga.  |No |
| writeBatchTimeout |Questa proprietà specifica il tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br/>I valori consentiti sono per l'intervallo di tempo. Ad esempio, "00:30:00" corrisponde a 30 minuti. |No |
| preCopyScript |Questa proprietà specifica una query SQL per l'attività di copia da eseguire prima di scrivere i dati nell'istanza gestita. Viene richiamata solo una volta per ogni esecuzione della copia. È possibile usare questa proprietà per pulire i dati precaricati. |No |
| sqlWriterStoredProcedureName | Il nome della stored procedure che definisce come applicare i dati di origine in una tabella di destinazione. <br/>Questa stored procedure viene *richiamata per batch*. Per le operazioni che vengono eseguite una sola volta e non hanno `preCopyScript` nulla a che fare con i dati di origine, ad esempio eliminare o troncare, utilizzare la proprietà . | No |
| storedProcedureTableTypeParameterName |Nome del parametro del tipo di tabella specificato nella stored procedure.  |No |
| sqlWriterTableType |Nome del tipo di tabella da utilizzare nella stored procedure. Nel corso dell'attività di copia, i dati spostati vengono resi disponibili in una tabella temporanea di questo tipo. Il codice della stored procedure può quindi unire i dati di cui è in corso la copia con i dati esistenti. |No |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |
| Tableoption | Specifica se creare automaticamente la tabella sink se non esiste in base allo schema di origine. La creazione automatica della tabella non è supportata quando il sink specifica che la stored procedure o la copia in fasi è configurata nell'attività di copia. I valori `none` consentiti `autoCreate`sono: (predefinito), . |No |

**Esempio 1: Aggiungere datiExample 1: Append data**

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "writeBatchSize": 100000,
                "tableOption": "autoCreate"
            }
        }
    }
]
```

**Esempio 2: Richiamare una stored procedure durante la copiaExample 2: Invoke a stored procedure during copy**

Per ulteriori informazioni, [vedere Richiamare una stored procedure da un sink MI SQL.](#invoke-a-stored-procedure-from-a-sql-sink)

```json
"activities":[
    {
        "name": "CopyToAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
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

## <a name="best-practice-for-loading-data-into-azure-sql-database-managed-instance"></a>Procedura consigliata per il caricamento di dati nell'istanza gestita del database SQL di AzureBest practice for loading data into Azure SQL Database Managed Instance

Quando si copiano dati nell'istanza gestita del database SQL di Azure, potrebbe essere necessario un comportamento di scrittura diverso:When you copy data into Azure SQL Database Managed Instance, you might require different write behavior:

- [Append](#append-data): I dati di origine conpossonore solo nuovi record.
- [Upsert](#upsert-data): I miei dati di origine hanno sia inserti che aggiornamenti.
- [Sovrascrivi](#overwrite-the-entire-table): Voglio ricaricare ogni volta l'intera tabella delle dimensioni.
- [Scrivere con logica personalizzata:](#write-data-with-custom-logic)è necessaria un'ulteriore elaborazione prima dell'inserimento finale nella tabella di destinazione. 

Vedere le rispettive sezioni per informazioni su come configurare Azure Data Factory e le procedure consigliate.

### <a name="append-data"></a>Accodare dati

L'aggiunta di dati è il comportamento predefinito di questo connettore sink dell'istanza gestita del database SQL di Azure.Appending data is the default behavior of this Azure SQL Database Managed Instance sink connector. Azure Data Factory esegue un inserimento bulk per scrivere nella tabella in modo efficiente. È possibile configurare l'origine e il sink di conseguenza nell'attività di copia.

### <a name="upsert-data"></a>Eseguire l'upsert dei dati

**Opzione 1:** Quando si dispone di una grande quantità di dati da copiare, utilizzare il seguente approccio per eseguire un upsert: 

- In primo luogo, utilizzare una [tabella temporanea](https://docs.microsoft.com/sql/t-sql/statements/create-table-transact-sql?view=sql-server-2017#temporary-tables) per eseguire il caricamento bulk di tutti i record utilizzando l'attività di copia. Poiché le operazioni su tabelle temporanee non vengono registrate, è possibile caricare milioni di record in pochi secondi.
- Eseguire un'attività di stored procedure in Azure Data Factory per applicare un'istruzione [MERGE](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-current) o INSERT/UPDATE. Utilizzare la tabella temporanea come origine per eseguire tutti gli aggiornamenti o gli inserimenti come singola transazione. In questo modo, il numero di round trip e operazioni di log viene ridotto. Al termine dell'attività della stored procedure, la tabella temporanea può essere troncata per essere pronta per il ciclo di ripresa successivo.

Ad esempio, in Azure Data Factory è possibile creare una pipeline con **un'attività di copia** concatenata a un'attività Stored **procedure**. Precedentemente copia i dati dall'archivio di origine in una tabella temporanea, ad **esempio, #UpsertTempTable**, come nome della tabella nel set di dati. Quindi quest'ultima richiama una stored procedure per unire i dati di origine dalla tabella temporanea nella tabella di destinazione e pulire la tabella temporanea.

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

**Opzione 2:** È inoltre possibile scegliere [di richiamare una stored procedure all'interno di un'attività](#invoke-a-stored-procedure-from-a-sql-sink)di copia. Questo approccio esegue ogni riga nella tabella di origine anziché usare l'inserimento bulk come approccio predefinito nell'attività di copia, che non è appropriato per l'upsert di grandi dimensioni.

### <a name="overwrite-the-entire-table"></a>Sovrascrivere l'intera tabella

È possibile configurare la proprietà **preCopyScript** in un sink di attività di copia. In questo caso, per ogni attività di copia eseguita, Azure Data Factory esegue prima lo script. Quindi esegue la copia per inserire i dati. Ad esempio, per sovrascrivere l'intera tabella con i dati più recenti, specificare uno script per eliminare tutti i record prima di caricare in blocco i nuovi dati dall'origine.

### <a name="write-data-with-custom-logic"></a>Scrivere dati con logica personalizzataWrite data with custom logic

I passaggi per scrivere dati con logica personalizzata sono simili a quelli descritti nella sezione Dati [Upsert.](#upsert-data) Quando è necessario applicare l'elaborazione aggiuntiva prima dell'inserimento finale dei dati di origine nella tabella di destinazione, per la scalabilità di grandi dimensioni, è possibile eseguire una delle due operazioni seguenti:When you need to apply extra processing before the final insertion of source data into the destination table, for large scale, you can do one of two things: 

- Caricare in una tabella temporanea e quindi richiamare una stored procedure.
- Richiamare una stored procedure durante la copia.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Richiamare una stored procedure da un sink SQL

Quando si copiano dati nell'istanza gestita del database SQL di Azure, è anche possibile configurare e richiamare una stored procedure specificata dall'utente con parametri aggiuntivi. La funzionalità della stored procedure sfrutta i parametri con valori di [tabella.](https://msdn.microsoft.com/library/bb675163.aspx)

> [!TIP]
> La chiamata di una stored procedure elabora i dati riga per riga anziché utilizzando un'operazione in blocco, che non è consigliabile per la copia su larga scala. Per altre informazioni, vedere Procedura consigliata per il caricamento dei [dati nell'istanza gestita del database SQL](#best-practice-for-loading-data-into-azure-sql-database-managed-instance)di Azure.Learn more from Best practice for loading data into Azure SQL Database Managed Instance .

È possibile usare una stored procedure quando non si possono usare i meccanismi di copia predefiniti. Un esempio è quando si desidera applicare un'elaborazione aggiuntiva prima dell'inserimento finale dei dati di origine nella tabella di destinazione. Alcuni esempi di elaborazione aggiuntivi sono quando si desidera unire le colonne, cercare valori aggiuntivi e inserire dati in più di una tabella.

Nell'esempio seguente viene illustrato come usare una stored procedure per eseguire un'operazione upsert in una tabella del database SQL Server. Si supponga che i dati di input e la tabella **marketing** sink dispongano ognuna di tre colonne: **ProfileID**, **State**e **Category**. Eseguire l'upsert in base alla colonna **ProfileID** e applicarla solo per una categoria specifica denominata "ProductA".

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

3. In Azure Data Factory definire la sezione **del sink MI SQL** nell'attività di copia come segue:In Azure Data Factory, define the SQL MI sink section in the copy activity as follows:

    ```json
    "sink": {
        "type": "SqlMISink",
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

## <a name="data-type-mapping-for-azure-sql-database-managed-instance"></a>Mapping dei tipi di dati per l'Istanza gestita di database SQL di Azure

Quando si copiano dati da o verso l'Istanza gestita di database SQL di Azure, vengono usati i mapping seguenti tra i tipi di dati dell'Istanza gestita di database SQL di Azure e i tipi di dati provvisori di Azure Data Factory. Per informazioni su come l'attività di copia esegue il mapping dello schema e del tipo di dati di origine al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

| Tipi di dati dell'Istanza gestita di database SQL di Azure | Tipo di dati provvisorio di Azure Data Factory |
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
| TINYINT |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> Per i tipi di dati associati al tipo provvisorio Decimal, Azure Data Factory supporta attualmente la precisione fino a 28. Se si hanno dati che richiedono una precisione maggiore di 28, è consigliabile convertirli in una stringa in una query SQL.

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per informazioni dettagliate sulle proprietà, controllare [Attività GetMetadataTo](control-flow-get-metadata-activity.md) learn details about the properties, check GetMetadata activity 

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati supportati.](copy-activity-overview.md#supported-data-stores-and-formats)
