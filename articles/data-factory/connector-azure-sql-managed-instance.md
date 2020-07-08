---
title: Copiare dati da e verso Istanza gestita SQL di Azure
description: Informazioni su come spostare i dati da e verso Istanza gestita SQL di Azure usando Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 05/29/2020
ms.openlocfilehash: 907579d44575de56f95e0828c3313d0d1682b29c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85513889"
---
# <a name="copy-data-to-and-from-azure-sql-managed-instance-by-using-azure-data-factory"></a>Copiare dati da e verso Istanza gestita SQL di Azure usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e verso Istanza gestita SQL di Azure. Si basa sull'articolo [Panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale dell'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore SQL Istanza gestita è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

È possibile copiare dati da SQL Istanza gestita a qualsiasi archivio dati di sink supportato. È anche possibile copiare dati da qualsiasi archivio dati di origine supportato a SQL Istanza gestita. Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, questo connettore SQL Istanza gestita supporta:

- Copia dei dati usando l'autenticazione SQL e l'autenticazione del token dell'applicazione Azure Active Directory (Azure AD) con un'entità servizio o identità gestite per le risorse di Azure.
- Come origine, il recupero dei dati tramite una query SQL o un stored procedure.
- Come sink, creazione automatica della tabella di destinazione se non esiste in base allo schema di origine. Aggiunta di dati a una tabella o richiamo di una stored procedure con la logica personalizzata durante la copia.

>[!NOTE]
> SQL Istanza gestita [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current) non è attualmente supportato da questo connettore. Per aggirare il contenuto, è possibile usare un [connettore ODBC generico](connector-odbc.md) e un driver ODBC SQL Server tramite un runtime di integrazione self-hosted. Per altre informazioni, vedere l'articolo relativo all' [uso always Encrypted](#using-always-encrypted) . 

## <a name="prerequisites"></a>Prerequisiti

Per accedere all' [endpoint pubblico](../azure-sql/managed-instance/public-endpoint-overview.md)di SQL istanza gestita, è possibile usare una Azure Data Factory runtime di integrazione di Azure gestito. Assicurarsi di abilitare l'endpoint pubblico e consentire anche il traffico dell'endpoint pubblico nel gruppo di sicurezza di rete in modo che Azure Data Factory possa connettersi al database. Per ulteriori informazioni, vedere le [linee guida](../azure-sql/managed-instance/public-endpoint-configure.md).

Per accedere all'endpoint privato di SQL Istanza gestita, configurare un [runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md) in grado di accedere al database. Se si esegue il provisioning del runtime di integrazione self-hosted nella stessa rete virtuale dell'istanza gestita, verificare che il computer del runtime di integrazione si trovi in una subnet diversa da quella dell'istanza gestita. Se si effettua il provisioning del runtime di integrazione self-hosted in una rete virtuale diversa da quella dell'istanza gestita, è possibile usare un peering di rete virtuale o una rete virtuale per la connessione di rete virtuale. Per altre informazioni, vedere [Connettere l'applicazione a Istanza gestita di SQL di Azure](../azure-sql/managed-instance/connect-application-instance.md).

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire Azure Data Factory entità specifiche del connettore SQL Istanza gestita.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato SQL Istanza gestita sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su **AzureSqlMI**. | Sì |
| connectionString |Questa proprietà specifica le informazioni di **ConnectionString** necessarie per connettersi a SQL istanza gestita usando l'autenticazione di SQL. Per altre informazioni, vedere gli esempi seguenti. <br/>La porta predefinita è 1433. Se si usa Istanza gestita SQL con un endpoint pubblico, specificare in modo esplicito la porta 3342.<br> È anche possibile inserire una password in Azure Key Vault. Se si tratta dell'autenticazione SQL, estrarre la `password` configurazione dalla stringa di connessione. Per ulteriori informazioni, vedere l'esempio JSON che segue la tabella e [archivia le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Azure Data Factory o [fare riferimento a un segreto archiviato nel Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio |
| tenant | Specificare le informazioni sul tenant, ad esempio il nome di dominio o l'ID tenant, in cui risiede l'applicazione. Recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio |
| connectVia | Questo [runtime di integrazione](concepts-integration-runtime.md) viene usato per connettersi all'archivio dati. È possibile usare un runtime di integrazione self-hosted o un runtime di integrazione di Azure se l'istanza gestita ha un endpoint pubblico e consente a Azure Data Factory di accedervi. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |Sì |

Per altri tipi di autenticazione, fare riferimento alle sezioni seguenti relative, rispettivamente, ai prerequisiti e agli esempi JSON:

- [Autenticazione SQL](#sql-authentication)
- [Autenticazione del token dell'applicazione Azure AD: entità servizio](#service-principal-authentication)
- [Autenticazione del token dell'applicazione Azure AD: identità gestite per le risorse di Azure](#managed-identity)

### <a name="sql-authentication"></a>Autenticazione SQL

**Esempio 1: usare l'autenticazione SQL**

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

**Esempio 2: usare l'autenticazione SQL con una password in Azure Key Vault**

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

1. Seguire i passaggi per eseguire il [provisioning di un amministratore Azure Active Directory per il istanza gestita](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) nel portale di Azure. Prendere nota del nome dell'applicazione e dei valori seguenti che definiscono il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

3. [Creare gli account di accesso](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) per l'identità gestita Azure Data Factory. In SQL Server Management Studio (SSMS) connettersi all'istanza gestita usando un account SQL Server che è un **amministratore**di sistema. Eseguire il codice T-SQL seguente nel database **master**:

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Creare utenti di database indipendente](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) per l'identità gestita del Azure Data Factory. Connettersi al database da cui o in cui si vuole copiare i dati ed eseguire il codice T-SQL seguente: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Concedere le autorizzazioni necessarie per la Data Factory identità gestita come si fa normalmente per gli utenti SQL e altri. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your application name]
    ```

6. Configurare un servizio collegato SQL Istanza gestita in Azure Data Factory.

**Esempio: usare l'autenticazione basata su entità servizio**

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Autenticazione di identità gestite per le risorse di Azure

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la data factory specifica. È possibile usare questa identità gestita per l'autenticazione di SQL Istanza gestita. La factory specificata può accedere e copiare i dati dal database o nel database usando questa identità.

Per utilizzare l'autenticazione di identità gestita, attenersi alla seguente procedura.

1. Seguire i passaggi per eseguire il [provisioning di un amministratore Azure Active Directory per il istanza gestita](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Creare gli account di accesso](https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current) per l'identità gestita Azure Data Factory. In SQL Server Management Studio (SSMS) connettersi all'istanza gestita usando un account SQL Server che è un **amministratore**di sistema. Eseguire il codice T-SQL seguente nel database **master**:

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Creare utenti di database indipendente](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) per l'identità gestita del Azure Data Factory. Connettersi al database da cui o in cui si vuole copiare i dati ed eseguire il codice T-SQL seguente: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Concedere le autorizzazioni necessarie per la Data Factory identità gestita come si fa normalmente per gli utenti SQL e altri. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](https://docs.microsoft.com/sql/t-sql/statements/alter-role-transact-sql?view=azuresqldb-mi-current).

    ```sql
    ALTER ROLE [role name e.g. db_owner] ADD MEMBER [your Data Factory name]
    ```

5. Configurare un servizio collegato SQL Istanza gestita in Azure Data Factory.

**Esempio: usa l'autenticazione dell'identità gestita**

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

Per un elenco completo delle sezioni e delle proprietà disponibili per definire set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati di SQL Istanza gestita.

Per copiare dati da e verso Istanza gestita SQL, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su **AzureSqlMITable**. | Sì |
| schema | Nome dello schema. |No per l'origine, Sì per il sink  |
| tabella | Nome della tabella/vista. |No per l'origine, Sì per il sink  |
| tableName | Nome della tabella/vista con schema. Questa proprietà è supportata per garantire la compatibilità con le versioni precedenti. Per i nuovi carichi di lavoro, usare `schema` e `table`. | No per l'origine, Sì per il sink |

**Esempio**

```json
{
    "name": "AzureSqlMIDataset",
    "properties":
    {
        "type": "AzureSqlMITable",
        "linkedServiceName": {
            "referenceName": "<SQL Managed Instance linked service name>",
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

Per un elenco completo delle sezioni e delle proprietà disponibili per definire le attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di SQL Istanza gestita.

### <a name="sql-managed-instance-as-a-source"></a>Istanza gestita SQL come origine

Per copiare dati da SQL Istanza gestita, nella sezione origine dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **SqlMISource**. | Sì |
| sqlReaderQuery |Questa proprietà usa la query SQL personalizzata per leggere i dati. Un esempio è `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Questa proprietà definisce il nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No |
| storedProcedureParameters |Questi parametri sono relativi alla stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e l'uso di maiuscole e minuscole dei parametri devono corrispondere a quelli dei parametri della stored procedure. |No |
| isolationLevel | Specifica il comportamento di blocco della transazione per l'origine SQL. I valori consentiti sono: **ReadCommitted** (impostazione predefinita), **ReadUncommitted**, **RepeatableRead**, **Serializable**, **Snapshot**. Per altre informazioni dettagliate, vedere [questo documento](https://docs.microsoft.com/dotnet/api/system.data.isolationlevel). | No |

**Tenere presente quanto segue:**

- Se viene specificato **sqlReaderQuery** per **SqlMISource**, l'attività di copia esegue la query sull'origine SQL istanza gestita per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters**, se la stored procedure accetta parametri.
- Se non si specifica la proprietà **sqlReaderQuery** o **sqlReaderStoredProcedureName**, per creare una query vengono usate le colonne definite nella sezione "structure" del codice JSON del set di dati. La query `select column1, column2 from mytable` viene eseguita su SQL istanza gestita. Se la definizione del set di dati non include "structure", vengono selezionate tutte le colonne della tabella.

**Esempio: usare una query SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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

**Esempio: usare un stored procedure**

```json
"activities":[
    {
        "name": "CopyFromAzureSqlMI",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SQL Managed Instance input dataset name>",
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

### <a name="sql-managed-instance-as-a-sink"></a>Istanza gestita SQL come sink

> [!TIP]
> Scopri di più sui comportamenti di scrittura supportati, sulle configurazioni e sulle procedure consigliate, dalla [procedura consigliata per il caricamento di dati in SQL istanza gestita](#best-practice-for-loading-data-into-sql-managed-instance).

Per copiare dati in SQL Istanza gestita, nella sezione sink dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del sink dell'attività di copia deve essere impostata su **SqlMISink**. | Sì |
| preCopyScript |Questa proprietà specifica una query SQL per l'attività di copia da eseguire prima di scrivere i dati in SQL Istanza gestita. Viene richiamata solo una volta per ogni esecuzione della copia. È possibile usare questa proprietà per pulire i dati precaricati. |No |
| tableOption | Specifica se creare automaticamente la tabella di sink, se non esiste, in base allo schema di origine. La creazione automatica della tabella non è supportata quando il sink specifica stored procedure o la copia temporanea è configurata nell'attività di copia. I valori consentiti sono: `none` (impostazione predefinita), `autoCreate`. |No |
| sqlWriterStoredProcedureName | Il nome della stored procedure che definisce come applicare i dati di origine in una tabella di destinazione. <br/>Questa stored procedure viene *richiamata per batch*. Per le operazioni che vengono eseguite una sola volta e non hanno nulla a che fare con i dati di origine, ad esempio DELETE o TRUNCATE, usare la `preCopyScript` Proprietà.<br>Vedere l'esempio da [richiamare un stored procedure da un sink SQL](#invoke-a-stored-procedure-from-a-sql-sink). | No |
| storedProcedureTableTypeParameterName |Nome del parametro del tipo di tabella specificato nell'stored procedure.  |No |
| sqlWriterTableType |Nome del tipo di tabella da utilizzare nel stored procedure. Nel corso dell'attività di copia, i dati spostati vengono resi disponibili in una tabella temporanea di questo tipo. Il codice della stored procedure può quindi unire i dati di cui è in corso la copia con i dati esistenti. |No |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |
| writeBatchSize |Numero di righe da inserire nella tabella SQL *per batch*.<br/>I valori consentiti sono integer per il numero di righe. Per impostazione predefinita, Azure Data Factory determina in modo dinamico le dimensioni del batch appropriate in base alle dimensioni della riga.  |No |
| writeBatchTimeout |Questa proprietà specifica il tempo di attesa per l'operazione di inserimento batch da completare prima del timeout.<br/>I valori consentiti sono per l'intervallo di tempo. Ad esempio, "00:30:00" corrisponde a 30 minuti. |No |

**Esempio 1: accodare i dati**

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
                "referenceName": "<SQL Managed Instance output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SqlMISink",
                "tableOption": "autoCreate",
                "writeBatchSize": 100000
            }
        }
    }
]
```

**Esempio 2: richiamare un stored procedure durante la copia**

Per ulteriori informazioni, vedere [richiamare un stored procedure da un sink SQL mi](#invoke-a-stored-procedure-from-a-sql-sink).

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
                "referenceName": "<SQL Managed Instance output dataset name>",
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

## <a name="best-practice-for-loading-data-into-sql-managed-instance"></a>Procedura consigliata per il caricamento di dati in SQL Istanza gestita

Quando si copiano dati in SQL Istanza gestita, potrebbe essere necessario un comportamento di scrittura diverso:

- [Accoda](#append-data): i dati di origine hanno solo i nuovi record.
- [Upsert](#upsert-data): i dati di origine sono inserimenti e aggiornamenti.
- [Overwrite](#overwrite-the-entire-table): si desidera ricaricare ogni volta l'intera tabella della dimensione.
- [Scrivi con logica personalizzata](#write-data-with-custom-logic): ho bisogno di ulteriore elaborazione prima dell'inserimento finale nella tabella di destinazione. 

Vedere le rispettive sezioni per la configurazione in Azure Data Factory e procedure consigliate.

### <a name="append-data"></a>Accodare dati

L'accodamento dei dati è il comportamento predefinito del connettore di sink di SQL Istanza gestita. Azure Data Factory esegue un inserimento bulk per scrivere in modo efficiente nella tabella. È possibile configurare l'origine e il sink di conseguenza nell'attività di copia.

### <a name="upsert-data"></a>Eseguire l'upsert dei dati

**Opzione 1:** Quando si dispone di una grande quantità di dati da copiare, è possibile eseguire il caricamento bulk di tutti i record in una tabella di staging usando l'attività di copia, quindi eseguire un'attività stored procedure per applicare un'istruzione [merge](https://docs.microsoft.com/sql/t-sql/statements/merge-transact-sql?view=azuresqldb-mi-current) o Insert/Update in un'unica immagine. 

L'attività di copia attualmente non supporta in modo nativo il caricamento dei dati in una tabella temporanea di database. Esiste una modalità avanzata per configurarla con una combinazione di più attività. vedere ottimizzare gli [scenari di Upsert bulk del database SQL](https://github.com/scoriani/azuresqlbulkupsert). Di seguito viene illustrato un esempio di utilizzo di una tabella permanente come gestione temporanea.

In Azure Data Factory, ad esempio, è possibile creare una pipeline con un' **attività di copia** concatenata a un' **attività di stored procedure**. Il primo copia i dati dall'archivio di origine in una tabella di staging di SQL Istanza gestita di Azure, ad esempio **UpsertStagingTable**, come nome della tabella nel set di dati. Il secondo richiama quindi un stored procedure per unire i dati di origine dalla tabella di staging alla tabella di destinazione e pulire la tabella di staging.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Nel database definire un stored procedure con la logica di MERGE, come nell'esempio seguente, a cui fa riferimento l'attività stored procedure precedente. Si supponga che la destinazione sia la tabella **Marketing** con tre colonne: **profileid**, **stato**e **categoria**. Eseguire Upsert in base alla colonna **profileid** .

```sql
CREATE PROCEDURE [dbo].[spMergeData]
AS
BEGIN
    MERGE TargetTable AS target
    USING UpsertStagingTable AS source
    ON (target.[ProfileID] = source.[ProfileID])
    WHEN MATCHED THEN
        UPDATE SET State = source.State
    WHEN NOT matched THEN
        INSERT ([ProfileID], [State], [Category])
      VALUES (source.ProfileID, source.State, source.Category);
    
    TRUNCATE TABLE UpsertStagingTable
END
```

**Opzione 2:** È possibile scegliere di [richiamare un stored procedure all'interno dell'attività di copia](#invoke-a-stored-procedure-from-a-sql-sink). Questo approccio esegue ogni batch, come regolato dalla `writeBatchSize` proprietà, nella tabella di origine anziché utilizzare BULK INSERT come approccio predefinito nell'attività di copia.

### <a name="overwrite-the-entire-table"></a>Sovrascrivi l'intera tabella

È possibile configurare la proprietà **preCopyScript** in un sink dell'attività di copia. In questo caso, per ogni attività di copia eseguita, Azure Data Factory esegue prima lo script. Esegue quindi la copia per inserire i dati. Per sovrascrivere l'intera tabella con i dati più recenti, ad esempio, specificare uno script per eliminare prima tutti i record prima di eseguire il caricamento bulk dei nuovi dati dall'origine.

### <a name="write-data-with-custom-logic"></a>Scrivere dati con logica personalizzata

I passaggi per scrivere i dati con la logica personalizzata sono simili a quelli descritti nella sezione relativa ai [dati di Upsert](#upsert-data) . Quando è necessario applicare un'elaborazione aggiuntiva prima dell'inserimento finale dei dati di origine nella tabella di destinazione, è possibile caricare in una tabella di staging, quindi richiamare stored procedure attività o richiamare una stored procedure nel sink dell'attività di copia per applicare i dati.

## <a name="invoke-a-stored-procedure-from-a-sql-sink"></a><a name="invoke-a-stored-procedure-from-a-sql-sink"></a> Richiamare una stored procedure da un sink SQL

Quando si copiano dati in SQL Istanza gestita, è anche possibile configurare e richiamare una stored procedure specificata dall'utente con parametri aggiuntivi in ogni batch della tabella di origine. La funzionalità stored procedure sfrutta i [parametri con valori di tabella](https://msdn.microsoft.com/library/bb675163.aspx).

È possibile usare una stored procedure quando non si possono usare i meccanismi di copia predefiniti. Un esempio è quando si desidera applicare un'ulteriore elaborazione prima dell'inserimento finale dei dati di origine nella tabella di destinazione. Alcuni esempi di elaborazione aggiuntivi sono quando si desidera unire le colonne, cercare valori aggiuntivi e inserirli in più di una tabella.

Nell'esempio seguente viene illustrato come usare una stored procedure per eseguire un'operazione upsert in una tabella del database SQL Server. Si supponga che i dati di input e la tabella di **Marketing** sink includano tre colonne: **profileid**, **state**e **Category**. Eseguire Upsert in base alla colonna **profileid** e applicarla solo a una categoria specifica denominata "producta".

1. Nel database definire il tipo di tabella con lo stesso nome di **sqlWriterTableType**. Lo schema del tipo di tabella è identico allo schema restituito dai dati di input.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Nel database definire il stored procedure con lo stesso nome di **sqlWriterStoredProcedureName**. che gestisce i dati di input dell'origine specificata e li unisce nella tabella di output. Il nome del parametro del tipo di tabella nella stored procedure corrisponde a **TableName** definito nel set di dati.

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

3. In Azure Data Factory definire la sezione **sink SQL mi** nell'attività di copia come indicato di seguito:

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

## <a name="data-type-mapping-for-sql-managed-instance"></a>Mapping dei tipi di dati per SQL Istanza gestita

Quando i dati vengono copiati in e da SQL Istanza gestita, i mapping seguenti vengono usati dai tipi di dati SQL Istanza gestita per Azure Data Factory tipi di dati provvisori. Per informazioni su come l'attività di copia esegue il mapping dello schema e del tipo di dati di origine al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

| Tipo di dati SQL Istanza gestita | Tipo di dati provvisorio di Azure Data Factory |
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
| timestamp |Byte[] |
| TINYINT |Int16 |
| UNIQUEIDENTIFIER |Guid |
| varbinary |Byte[] |
| varchar |String, Char[] |
| Xml |Xml |

>[!NOTE]
> Per i tipi di dati che vengono mappati al tipo provvisorio decimale, attualmente l'attività di copia supporta la precisione fino a 28. Se si hanno dati che richiedono una precisione maggiore di 28, è consigliabile convertirli in una stringa in una query SQL.

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per altre informazioni sulle proprietà, vedere [Attività GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="using-always-encrypted"></a>Uso di Always Encrypted

Quando si copiano dati da/in Azure SQL Istanza gestita con [Always Encrypted](https://docs.microsoft.com/sql/relational-databases/security/encryption/always-encrypted-database-engine?view=azuresqldb-mi-current), usare il [connettore odbc generico](connector-odbc.md) e SQL Server driver ODBC tramite Integration Runtime self-hosted. Questo connettore Azure SQL Istanza gestita non supporta ora Always Encrypted. 

Più in particolare:

1. Configurare un Integration Runtime self-hosted se non è disponibile. Per informazioni dettagliate, vedere l'articolo relativo alla [Integration Runtime self-hosted](create-self-hosted-integration-runtime.md) .

2. Scaricare il driver ODBC a 64 bit per SQL Server da [qui](https://docs.microsoft.com/sql/connect/odbc/download-odbc-driver-for-sql-server?view=azuresqldb-mi-current)e installarlo nel computer Integration Runtime. Per ulteriori informazioni sul funzionamento di questo driver, [utilizzare always Encrypted con ODBC driver for SQL Server](https://docs.microsoft.com/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver?view=azuresqldb-mi-current#using-the-azure-key-vault-provider).

3. Creare un servizio collegato con tipo ODBC per connettersi al database SQL, fare riferimento agli esempi seguenti:

    - Per utilizzare **l'autenticazione SQL**: specificare la stringa di connessione ODBC come indicato di seguito e selezionare autenticazione di **base** per impostare il nome utente e la password.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Per usare **l'autenticazione Data Factory identità gestita**: 

        1. Seguire gli stessi [prerequisiti](#managed-identity) per creare l'utente del database per l'identità gestita e concedere il ruolo appropriato nel database.
        2. In servizio collegato specificare la stringa di connessione ODBC come indicato di seguito e selezionare autenticazione **anonima** come indicato nella stringa di connessione stessa `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Creare il set di dati e l'attività di copia con il tipo ODBC di conseguenza. Per altre informazioni, vedere [connettore ODBC](connector-odbc.md) .

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
