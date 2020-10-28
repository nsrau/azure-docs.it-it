---
title: Copiare e trasformare i dati in Azure SQL Istanza gestita
description: Informazioni su come copiare e trasformare i dati in Istanza gestita SQL di Azure usando Azure Data Factory.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.custom: seo-lt-2019
ms.date: 10/15/2020
ms.openlocfilehash: 805b6ed649a3ce301a3246ce1f672475ed47b9ea
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92636460"
---
# <a name="copy-and-transform-data-in-azure-sql-managed-instance-by-using-azure-data-factory"></a>Copiare e trasformare i dati in Istanza gestita SQL di Azure usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Azure SQL Istanza gestita e usare il flusso di dati per trasformare i dati in Istanza gestita SQL di Azure. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore SQL Istanza gestita è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

Per l'attività di copia, questo connettore del database SQL di Azure supporta le funzioni seguenti:

- Copia dei dati usando l'autenticazione SQL e l'autenticazione del token dell'applicazione Azure Active Directory (Azure AD) con un'entità servizio o identità gestite per le risorse di Azure.
- Come origine, il recupero dei dati tramite una query SQL o un stored procedure. È anche possibile scegliere di eseguire la copia parallela da SQL MI source, vedere la sezione [copia parallela da SQL mi](#parallel-copy-from-sql-mi) per informazioni dettagliate.
- Come sink, creazione automatica della tabella di destinazione se non esiste in base allo schema di origine. Aggiunta di dati a una tabella o richiamo di una stored procedure con la logica personalizzata durante la copia.

>[!NOTE]
> SQL Istanza gestita [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine) non è attualmente supportato da questo connettore. Per aggirare il contenuto, è possibile usare un [connettore ODBC generico](connector-odbc.md) e un driver ODBC SQL Server tramite un runtime di integrazione self-hosted. Per altre informazioni, vedere l'articolo relativo all' [uso always Encrypted](#using-always-encrypted) . 

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
| type | La proprietà Type deve essere impostata su **AzureSqlMI** . | Sì |
| connectionString |Questa proprietà specifica le informazioni di **ConnectionString** necessarie per connettersi a SQL istanza gestita usando l'autenticazione di SQL. Per altre informazioni, vedere gli esempi seguenti. <br/>La porta predefinita è 1433. Se si usa Istanza gestita SQL con un endpoint pubblico, specificare in modo esplicito la porta 3342.<br> È anche possibile inserire una password in Azure Key Vault. Se si tratta dell'autenticazione SQL, estrarre la `password` configurazione dalla stringa di connessione. Per ulteriori informazioni, vedere l'esempio JSON che segue la tabella e [archivia le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Azure Data Factory o [fare riferimento a un segreto archiviato nel Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio |
| tenant | Specificare le informazioni sul tenant, ad esempio il nome di dominio o l'ID tenant, in cui risiede l'applicazione. Recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio |
| azureCloudType | Per l'autenticazione dell'entità servizio, specificare il tipo di ambiente cloud di Azure in cui è registrata l'applicazione Azure AD. <br/> I valori consentiti sono **AzurePublic** , **AzureChina** , **AzureUsGovernment** e **AzureGermany** . Per impostazione predefinita, viene usato l'ambiente cloud del data factory. | No |
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

Per usare l'autenticazione token dell'applicazione di Azure AD basata sull'entità servizio, seguire questa procedura:

1. Seguire i passaggi per eseguire il [provisioning di un amministratore Azure Active Directory per il istanza gestita](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-managed-instance).

2. [Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) nel portale di Azure. Prendere nota del nome dell'applicazione e dei valori seguenti che definiscono il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

3. [Creare gli account di accesso](/sql/t-sql/statements/create-login-transact-sql) per l'identità gestita Azure Data Factory. In SQL Server Management Studio (SSMS) connettersi all'istanza gestita usando un account SQL Server che è un **amministratore** di sistema. Eseguire il codice T-SQL seguente nel database **master** :

    ```sql
    CREATE LOGIN [your application name] FROM EXTERNAL PROVIDER
    ```

4. [Creare utenti di database indipendente](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) per l'identità gestita del Azure Data Factory. Connettersi al database da cui o in cui si vuole copiare i dati ed eseguire il codice T-SQL seguente: 
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER
    ```

5. Concedere le autorizzazioni necessarie per la Data Factory identità gestita come si fa normalmente per gli utenti SQL e altri. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](/sql/t-sql/statements/alter-role-transact-sql).

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

2. [Creare gli account di accesso](/sql/t-sql/statements/create-login-transact-sql) per l'identità gestita Azure Data Factory. In SQL Server Management Studio (SSMS) connettersi all'istanza gestita usando un account SQL Server che è un **amministratore** di sistema. Eseguire il codice T-SQL seguente nel database **master** :

    ```sql
    CREATE LOGIN [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

3. [Creare utenti di database indipendente](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities) per l'identità gestita del Azure Data Factory. Connettersi al database da cui o in cui si vuole copiare i dati ed eseguire il codice T-SQL seguente: 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER
    ```

4. Concedere le autorizzazioni necessarie per la Data Factory identità gestita come si fa normalmente per gli utenti SQL e altri. Eseguire il codice seguente. Per altre opzioni, vedere [questo documento](/sql/t-sql/statements/alter-role-transact-sql).

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
| type | La proprietà Type del set di dati deve essere impostata su **AzureSqlMITable** . | Sì |
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

>[!TIP]
>Per caricare i dati da SQL MI in modo efficiente usando il partizionamento dei dati, vedere altre informazioni da [copia parallela da SQL mi](#parallel-copy-from-sql-mi).

Per copiare dati da SQL Istanza gestita, nella sezione origine dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **SqlMISource** . | Sì |
| sqlReaderQuery |Questa proprietà usa la query SQL personalizzata per leggere i dati. Un esempio è `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Questa proprietà definisce il nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. |No |
| storedProcedureParameters |Questi parametri sono relativi alla stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e l'uso di maiuscole e minuscole dei parametri devono corrispondere a quelli dei parametri della stored procedure. |No |
| isolationLevel | Specifica il comportamento di blocco della transazione per l'origine SQL. I valori consentiti sono: **ReadCommitted** , **READUNCOMMITTED** , **RepeatableRead** , **Serializable** , **snapshot** . Se non specificato, viene usato il livello di isolamento predefinito del database. Per altre informazioni dettagliate, vedere [questo documento](/dotnet/api/system.data.isolationlevel). | No |
| partitionOptions | Specifica le opzioni di partizionamento dei dati utilizzate per caricare i dati da SQL MI. <br>I valori consentiti sono: **None** (impostazione predefinita), **PhysicalPartitionsOfTable** e **DynamicRange** .<br>Quando è abilitata un'opzione di partizione (ovvero non `None` ), il grado di parallelismo per caricare simultaneamente i dati da SQL mi è controllato dall' [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) impostazione dell'attività di copia. | No |
| partitionSettings | Specifica il gruppo di impostazioni per il partizionamento dei dati. <br>Applicare quando l'opzione partition non è `None` . | No |
| **_In `partitionSettings` :_* _ | | |
| partitionColumnName | Specificare il nome della colonna di origine _ *nel tipo Integer o data/datetime* * che verrà utilizzato dal partizionamento dell'intervallo per la copia parallela. Se non è specificato, l'indice o la chiave primaria della tabella vengono rilevati automaticamente e utilizzati come colonna della partizione.<br>Si applica quando l'opzione di partizione è `DynamicRange`. Se si utilizza una query per recuperare i dati di origine, associare  `?AdfDynamicRangePartitionCondition ` la clausola WHERE. Per un esempio, vedere la sezione [copia parallela da database SQL](#parallel-copy-from-sql-mi) . | No |
| partitionUpperBound | Valore massimo della colonna di partizione per la suddivisione dell'intervallo di partizioni. Questo valore viene usato per decidere lo stride della partizione, non per filtrare le righe nella tabella. Tutte le righe della tabella o del risultato della query verranno partizionate e copiate. Se non è specificato, l'attività di copia rileva automaticamente il valore.  <br>Si applica quando l'opzione di partizione è `DynamicRange`. Per un esempio, vedere la sezione [copia parallela da database SQL](#parallel-copy-from-sql-mi) . | No |
| partitionLowerBound | Valore minimo della colonna di partizione per la suddivisione dell'intervallo di partizioni. Questo valore viene usato per decidere lo stride della partizione, non per filtrare le righe nella tabella. Tutte le righe della tabella o del risultato della query verranno partizionate e copiate. Se non è specificato, l'attività di copia rileva automaticamente il valore.<br>Si applica quando l'opzione di partizione è `DynamicRange`. Per un esempio, vedere la sezione [copia parallela da database SQL](#parallel-copy-from-sql-mi) . | No |

**Tenere presente quanto segue:**

- Se viene specificato **sqlReaderQuery** per **SqlMISource** , l'attività di copia esegue la query sull'origine SQL istanza gestita per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** , se la stored procedure accetta parametri.
- Se non si specifica la proprietà **sqlReaderQuery** o **sqlReaderStoredProcedureName** , per creare una query vengono usate le colonne definite nella sezione "structure" del codice JSON del set di dati. La query `select column1, column2 from mytable` viene eseguita su SQL istanza gestita. Se la definizione del set di dati non include "structure", vengono selezionate tutte le colonne della tabella.

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
| type | La proprietà Type del sink dell'attività di copia deve essere impostata su **SqlMISink** . | Sì |
| preCopyScript |Questa proprietà specifica una query SQL per l'attività di copia da eseguire prima di scrivere i dati in SQL Istanza gestita. Viene richiamata solo una volta per ogni esecuzione della copia. È possibile usare questa proprietà per pulire i dati precaricati. |No |
| tableOption | Specifica se [creare automaticamente la tabella di sink](copy-activity-overview.md#auto-create-sink-tables) se non esiste in base allo schema di origine. La creazione automatica della tabella non è supportata quando il sink specifica stored procedure. I valori consentiti sono: `none` (impostazione predefinita), `autoCreate`. |No |
| sqlWriterStoredProcedureName | Il nome della stored procedure che definisce come applicare i dati di origine in una tabella di destinazione. <br/>Questa stored procedure viene *richiamata per batch* . Per le operazioni che vengono eseguite una sola volta e non hanno nulla a che fare con i dati di origine, ad esempio DELETE o TRUNCATE, usare la `preCopyScript` Proprietà.<br>Vedere l'esempio da [richiamare un stored procedure da un sink SQL](#invoke-a-stored-procedure-from-a-sql-sink). | No |
| storedProcedureTableTypeParameterName |Nome del parametro del tipo di tabella specificato nell'stored procedure.  |No |
| sqlWriterTableType |Nome del tipo di tabella da utilizzare nel stored procedure. Nel corso dell'attività di copia, i dati spostati vengono resi disponibili in una tabella temporanea di questo tipo. Il codice della stored procedure può quindi unire i dati di cui è in corso la copia con i dati esistenti. |No |
| storedProcedureParameters |Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |
| writeBatchSize |Numero di righe da inserire nella tabella SQL *per batch* .<br/>I valori consentiti sono integer per il numero di righe. Per impostazione predefinita, Azure Data Factory determina in modo dinamico le dimensioni del batch appropriate in base alle dimensioni della riga.  |No |
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

## <a name="parallel-copy-from-sql-mi"></a>Copia parallela da SQL MI

Il connettore Istanza gestita di Azure SQL nell'attività di copia fornisce il partizionamento dei dati predefinito per copiare i dati in parallelo. È possibile trovare le opzioni di partizionamento dei dati nella scheda **origine** dell'attività di copia.

![Screenshot delle opzioni di partizione](./media/connector-sql-server/connector-sql-partition-options.png)

Quando si Abilita la copia partizionata, l'attività di copia esegue query parallele sull'origine SQL MI per caricare i dati in base alle partizioni. Il grado di parallelismo è controllato dall'impostazione [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) sull'attività di copia. Se, ad esempio, si imposta `parallelCopies` su quattro, data factory genera ed esegue contemporaneamente quattro query in base all'opzione di partizione specificata e alle impostazioni e ogni query recupera una porzione di dati da SQL mi.

Si consiglia di abilitare la copia parallela con il partizionamento dei dati, specialmente quando si caricano grandi quantità di dati da SQL MI. Di seguito sono riportate le configurazioni consigliate per i diversi scenari: Quando si copiano dati in un archivio dati basato su file, è consigliabile scrivere in una cartella come più file (specificare solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

| Scenario                                                     | Impostazioni consigliate                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Caricamento completo da tabelle di grandi dimensioni, con partizioni fisiche.        | **Opzione partition** : partizioni fisiche della tabella. <br><br/>Durante l'esecuzione, Data Factory rileva automaticamente le partizioni fisiche e copia i dati in base alle partizioni. <br><br/>Per verificare se la tabella contiene o meno una partizione fisica, è possibile fare riferimento a [questa query](#sample-query-to-check-physical-partition). |
| Caricamento completo da tabelle di grandi dimensioni, senza partizioni fisiche, con una colonna integer o DateTime per il partizionamento dei dati. | **Opzioni di partizione** : Partizione a intervalli dinamici.<br>**Colonna partizione** (facoltativo): specificare la colonna utilizzata per partizionare i dati. Se non è specificato, viene utilizzata la colonna di chiave primaria o di indice.<br/>Limite **superiore** della partizione e **limite inferiore della partizione** (facoltativo): specificare se si vuole determinare lo stride della partizione. Questa operazione non è relativa al filtraggio delle righe nella tabella. tutte le righe della tabella verranno partizionate e copiate. Se non è specificato, l'attività di copia rileva automaticamente i valori.<br><br>Se, ad esempio, la colonna di partizione "ID" ha valori compresi tra 1 e 100 e si imposta il limite inferiore su 20 e il limite superiore come 80, con la copia parallela 4, Data Factory recupera i dati di 4 partizioni-ID nell'intervallo <= 20, [21, 50], [51, 80] e >= 81 rispettivamente. |
| Caricare una grande quantità di dati tramite una query personalizzata, senza partizioni fisiche, con una colonna integer o date/DateTime per il partizionamento dei dati. | **Opzioni di partizione** : Partizione a intervalli dinamici.<br>**Query** : `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Colonna di partizione** : Specificare la colonna usata per partizionare i dati.<br>Limite **superiore** della partizione e **limite inferiore della partizione** (facoltativo): specificare se si vuole determinare lo stride della partizione. Non per filtrare le righe nella tabella, tutte le righe nel risultato della query verranno partizionate e copiate. Se non è specificato, l'attività di copia rileva automaticamente il valore.<br><br>Durante l'esecuzione, Data Factory sostituisce `?AdfRangePartitionColumnName` con il nome della colonna e gli intervalli di valori effettivi per ogni partizione e invia a SQL mi. <br>Se, ad esempio, la colonna di partizione "ID" ha valori compresi tra 1 e 100 e si imposta il limite inferiore su 20 e il limite superiore come 80, con la copia parallela 4, Data Factory recupera i dati di 4 partizioni-ID nell'intervallo <= 20, [21, 50], [51, 80] e >= 81 rispettivamente. <br><br>Di seguito sono riportate altre query di esempio per scenari diversi:<br> 1. eseguire una query sull'intera tabella: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. eseguire una query da una tabella con la selezione delle colonne e i filtri aggiuntivi della clausola WHERE: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. eseguire una query con sottoquery: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. query con partizione in sottoquery: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Procedure consigliate per caricare i dati con l'opzione di partizione:

1. Scegliere una colonna distinta come colonna di partizione, ad esempio chiave primaria o chiave univoca, per evitare l'asimmetria dei dati. 
2. Se la tabella include una partizione predefinita, usare l'opzione di partizione "partizioni fisiche della tabella" per ottenere prestazioni migliori.  
3. Se si usa Azure Integration Runtime per copiare i dati, è possibile impostare "[Data Integration Unit (DIU)](copy-activity-performance-features.md#data-integration-units)" di dimensioni maggiori (>4) per usare più risorse di elaborazione. Controllare gli scenari applicabili.
4. "[Degree of copy parallelism](copy-activity-performance-features.md#parallel-copy)" controlla i numeri di partizione, impostando questo numero su un valore troppo grande a volte, è consigliabile impostare questo numero come (Diu o numero di nodi IR self-hosted) * (da 2 a 4).

**Esempio: caricamento completo da tabelle di grandi dimensioni con partizioni fisiche**

```json
"source": {
    "type": "SqlMISource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Esempio: query con partizione a intervalli dinamici**

```json
"source": {
    "type": "SqlMISource",
    "query": "SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column (optional) to decide the partition stride, not as data filter>",
        "partitionLowerBound": "<lower_value_of_partition_column (optional) to decide the partition stride, not as data filter>"
    }
}
```

### <a name="sample-query-to-check-physical-partition"></a>Query di esempio per controllare la partizione fisica

```sql
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, pf.name AS PartitionFunctionName, c.name AS ColumnName, iif(pf.name is null, 'no', 'yes') AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id 
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id 
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id 
LEFT JOIN sys.partition_schemes ps ON i.data_space_id = ps.data_space_id 
LEFT JOIN sys.partition_functions pf ON pf.function_id = ps.function_id 
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Se la tabella contiene una partizione fisica, verrà visualizzato "HasPartition" come "Yes" come indicato di seguito.

![Risultato della query SQL](./media/connector-azure-sql-database/sql-query-result.png)

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

**Opzione 1:** Quando si dispone di una grande quantità di dati da copiare, è possibile eseguire il caricamento bulk di tutti i record in una tabella di staging usando l'attività di copia, quindi eseguire un'attività stored procedure per applicare un'istruzione [merge](/sql/t-sql/statements/merge-transact-sql) o Insert/Update in un'unica immagine. 

L'attività di copia attualmente non supporta in modo nativo il caricamento dei dati in una tabella temporanea di database. Esiste una modalità avanzata per configurarla con una combinazione di più attività. vedere ottimizzare gli [scenari di Upsert bulk del database SQL](https://github.com/scoriani/azuresqlbulkupsert). Di seguito viene illustrato un esempio di utilizzo di una tabella permanente come gestione temporanea.

In Azure Data Factory, ad esempio, è possibile creare una pipeline con un' **attività di copia** concatenata a un' **attività di stored procedure** . Il primo copia i dati dall'archivio di origine in una tabella di staging di SQL Istanza gestita di Azure, ad esempio **UpsertStagingTable** , come nome della tabella nel set di dati. Il secondo richiama quindi un stored procedure per unire i dati di origine dalla tabella di staging alla tabella di destinazione e pulire la tabella di staging.

![Upsert](./media/connector-azure-sql-database/azure-sql-database-upsert.png)

Nel database definire un stored procedure con la logica di MERGE, come nell'esempio seguente, a cui fa riferimento l'attività stored procedure precedente. Si supponga che la destinazione sia la tabella **Marketing** con tre colonne: **profileid** , **stato** e **categoria** . Eseguire Upsert in base alla colonna **profileid** .

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

Quando si copiano dati in SQL Istanza gestita, è anche possibile configurare e richiamare una stored procedure specificata dall'utente con parametri aggiuntivi in ogni batch della tabella di origine. La funzionalità stored procedure sfrutta i [parametri con valori di tabella](/dotnet/framework/data/adonet/sql/table-valued-parameters).

È possibile usare una stored procedure quando non si possono usare i meccanismi di copia predefiniti. Un esempio è quando si desidera applicare un'ulteriore elaborazione prima dell'inserimento finale dei dati di origine nella tabella di destinazione. Alcuni esempi di elaborazione aggiuntivi sono quando si desidera unire le colonne, cercare valori aggiuntivi e inserirli in più di una tabella.

Nell'esempio seguente viene illustrato come usare una stored procedure per eseguire un'operazione upsert in una tabella del database SQL Server. Si supponga che i dati di input e la tabella di **Marketing** sink includano tre colonne: **profileid** , **state** e **Category** . Eseguire Upsert in base alla colonna **profileid** e applicarla solo a una categoria specifica denominata "producta".

1. Nel database definire il tipo di tabella con lo stesso nome di **sqlWriterTableType** . Lo schema del tipo di tabella è identico allo schema restituito dai dati di input.

    ```sql
    CREATE TYPE [dbo].[MarketingType] AS TABLE(
        [ProfileID] [varchar](256) NOT NULL,
        [State] [varchar](256) NOT NULL,
        [Category] [varchar](256) NOT NULL
    )
    ```

2. Nel database definire il stored procedure con lo stesso nome di **sqlWriterStoredProcedureName** . che gestisce i dati di input dell'origine specificata e li unisce nella tabella di output. Il nome del parametro del tipo di tabella nella stored procedure corrisponde a **TableName** definito nel set di dati.

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

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Quando si trasformano i dati nel flusso di dati di mapping, è possibile leggere e scrivere nelle tabelle da Azure SQL Istanza gestita. Per altre informazioni, vedere la [trasformazione origine](data-flow-source.md) e la [trasformazione sink](data-flow-sink.md) nei flussi di dati per mapping.

> [!NOTE]
> Il connettore Istanza gestita SQL di Azure nel flusso di dati di mapping è attualmente disponibile come anteprima pubblica. È possibile connettersi all'endpoint pubblico di SQL Istanza gestita ma non ancora all'endpoint privato.

### <a name="source-transformation"></a>Trasformazione origine

La tabella seguente elenca le proprietà supportate dall'origine Istanza gestita SQL di Azure. È possibile modificare queste proprietà nella scheda **Opzioni di origine** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Tabella | Se si seleziona tabella come input, il flusso di dati recupera tutti i dati dalla tabella specificata nel set di dati. | No | - |- |
| Query | Se si seleziona query come input, specificare una query SQL per recuperare i dati dall'origine, che esegue l'override di qualsiasi tabella specificata nel set di dati. L'uso delle query è un ottimo modo per ridurre le righe per il test o le ricerche.<br><br>La clausola **Order by** non è supportata, ma è possibile impostare un'istruzione SELECT from completa. È possibile usare anche funzioni di tabella definite dall'utente. **Select * from udfGetData ()** è una funzione definita dall'utente in SQL che restituisce una tabella che è possibile utilizzare nel flusso di dati.<br>Esempio di query: `Select * from MyTable where customerId > 1000 and customerId < 2000`| No | string | query |
| Dimensioni dei batch | Specificare le dimensioni del batch per suddividere i dati di grandi dimensioni in letture. | No | Integer | batchSize |
| Livello di isolamento | Scegliere uno dei seguenti livelli di isolamento:<br>-Read Committed<br>-Read uncommitted (impostazione predefinita)<br>-Lettura ripetibile<br>-Serializzabile<br>-None (ignora il livello di isolamento) | No | <small>READ_COMMITTED<br/>READ_UNCOMMITTED<br/>REPEATABLE_READ<br/>SERIALIZABLE<br/>NESSUNO</small> |isolationLevel |

#### <a name="azure-sql-managed-instance-source-script-example"></a>Esempio di script di origine di Azure SQL Istanza gestita

Quando si usa Istanza gestita SQL di Azure come tipo di origine, lo script del flusso di dati associato è:

```
source(allowSchemaDrift: true,
    validateSchema: false,
    isolationLevel: 'READ_UNCOMMITTED',
    query: 'select * from MYTABLE',
    format: 'query') ~> SQLMISource
```

### <a name="sink-transformation"></a>Trasformazione sink

La tabella seguente elenca le proprietà supportate dal sink di Istanza gestita SQL di Azure. È possibile modificare queste proprietà nella scheda **Opzioni sink** .

| Nome | Descrizione | Obbligatoria | Valori consentiti | Proprietà script flusso di dati |
| ---- | ----------- | -------- | -------------- | ---------------- |
| Update (metodo) | Specificare le operazioni consentite per la destinazione del database. Per impostazione predefinita, sono consentiti solo gli inserimenti.<br>Per aggiornare, Upsert o eliminare righe, è necessaria una [trasformazione alter Row](data-flow-alter-row.md) per contrassegnare le righe per tali azioni. | Sì | `true` o `false` | cancellabile <br/>inseribile <br/>aggiornabile <br/>upsertable |
| Colonne chiave | Per gli aggiornamenti, Upsert ed eliminazioni, è necessario impostare le colonne chiave per determinare la riga da modificare.<br>Il nome della colonna scelto come chiave verrà usato come parte del successivo aggiornamento, Upsert, DELETE. Pertanto, è necessario selezionare una colonna esistente nel mapping del sink. | No | Array | chiavi |
| Ignora scrittura colonne chiave | Se non si desidera scrivere il valore nella colonna chiave, selezionare "Ignora scrittura colonne chiave". | No | `true` o `false` | skipKeyWrites |
| azione Tabella |Determina se ricreare o rimuovere tutte le righe dalla tabella di destinazione prima della scrittura.<br>- **None** : nessuna azione verrà eseguita nella tabella.<br>- **Ricrea** : la tabella viene eliminata e ricreata. Questa opzione è obbligatoria se si crea una nuova tabella in modo dinamico.<br>- **Truncate** : tutte le righe della tabella di destinazione vengono rimosse. | No | `true` o `false` | ricreare<br/>truncate |
| Dimensioni dei batch | Consente di specificare il numero di righe scritte in ogni batch. Dimensioni batch più grandi migliorano l'ottimizzazione della compressione e della memoria, ma rischiano di causare eccezioni di memoria insufficiente durante la memorizzazione nella cache dei dati. | No | Integer | batchSize |
| Script pre e post SQL | Specificare gli script SQL a più righe che verrà eseguito prima (pre-elaborazione) e dopo (post-elaborazione) i dati vengono scritti nel database sink. | No | string | preSQLs<br>postSQLs |

#### <a name="azure-sql-managed-instance-sink-script-example"></a>Esempio di script di sink di Azure SQL Istanza gestita

Quando si usa SQL di Azure Istanza gestita come tipo di sink, lo script del flusso di dati associato è:

```
IncomingStream sink(allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:true,
    updateable:true,
    upsertable:true,
    keys:['keyColumn'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true) ~> SQLMISink
```

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per altre informazioni sulle proprietà, vedere [Attività GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-sql-managed-instance"></a>Mapping dei tipi di dati per SQL Istanza gestita

Quando i dati vengono copiati in e da SQL Istanza gestita usando l'attività di copia, i mapping seguenti vengono usati dai tipi di dati SQL Istanza gestita per Azure Data Factory i tipi di dati provvisori. Per informazioni su come l'attività di copia esegue il mapping dello schema e del tipo di dati di origine al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

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
| Xml |string |

>[!NOTE]
> Per i tipi di dati che vengono mappati al tipo provvisorio decimale, attualmente l'attività di copia supporta la precisione fino a 28. Se si hanno dati che richiedono una precisione maggiore di 28, è consigliabile convertirli in una stringa in una query SQL.

## <a name="using-always-encrypted"></a>Uso di Always Encrypted

Quando si copiano dati da/in Azure SQL Istanza gestita con [Always Encrypted](/sql/relational-databases/security/encryption/always-encrypted-database-engine), usare il [connettore odbc generico](connector-odbc.md) e SQL Server driver ODBC tramite Integration Runtime self-hosted. Questo connettore Azure SQL Istanza gestita non supporta ora Always Encrypted. 

Più in particolare:

1. Configurare un Integration Runtime self-hosted se non è disponibile. Per informazioni dettagliate, vedere l'articolo relativo alla [Integration Runtime self-hosted](create-self-hosted-integration-runtime.md) .

2. Scaricare il driver ODBC a 64 bit per SQL Server da [qui](/sql/connect/odbc/download-odbc-driver-for-sql-server)e installarlo nel computer Integration Runtime. Per ulteriori informazioni sul funzionamento di questo driver, [utilizzare always Encrypted con ODBC driver for SQL Server](/sql/connect/odbc/using-always-encrypted-with-the-odbc-driver#using-the-azure-key-vault-provider).

3. Creare un servizio collegato con tipo ODBC per connettersi al database SQL, fare riferimento agli esempi seguenti:

    - Per utilizzare **l'autenticazione SQL** : specificare la stringa di connessione ODBC come indicato di seguito e selezionare autenticazione di **base** per impostare il nome utente e la password.

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>
        ```

    - Per usare **l'autenticazione Data Factory identità gestita** : 

        1. Seguire gli stessi [prerequisiti](#managed-identity) per creare l'utente del database per l'identità gestita e concedere il ruolo appropriato nel database.
        2. In servizio collegato specificare la stringa di connessione ODBC come indicato di seguito e selezionare autenticazione **anonima** come indicato nella stringa di connessione stessa `Authentication=ActiveDirectoryMsi` .

        ```
        Driver={ODBC Driver 17 for SQL Server};Server=<serverName>;Database=<databaseName>;ColumnEncryption=Enabled;KeyStoreAuthentication=KeyVaultClientSecret;KeyStorePrincipalId=<servicePrincipalKey>;KeyStoreSecret=<servicePrincipalKey>; Authentication=ActiveDirectoryMsi;
        ```

4. Creare il set di dati e l'attività di copia con il tipo ODBC di conseguenza. Per altre informazioni, vedere [connettore ODBC](connector-odbc.md) .

## <a name="next-steps"></a>Passaggi successivi
Per un elenco di archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).