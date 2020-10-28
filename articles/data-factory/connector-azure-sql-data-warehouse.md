---
title: Copiare e trasformare i dati in Azure Synapse Analytics
description: Informazioni su come copiare dati in e da Azure Synapse Analytics e come trasformare i dati in Azure Synapse Analytics usando Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 10/12/2020
ms.openlocfilehash: 79bc9a238b7c36392ff2ba519078713089156f6e
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92638211"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-sql-data-warehouse-by-using-azure-data-factory"></a>Copiare e trasformare i dati in Azure sinapsi Analytics (in precedenza SQL Data Warehouse) usando Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione del servizio Data Factory in uso:"]
>
> - [Versione 1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> - [Versione corrente](connector-azure-sql-data-warehouse.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

In questo articolo viene illustrato come usare l'attività di copia in Azure Data Factory per copiare dati da e in Azure Synapse Analytics e come usare Flusso di dati per trasformare i dati in Azure Data Lake Storage Gen2. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di Azure Synapse Analytics è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con la tabella della [matrice di origine/sink supportati](copy-activity-overview.md)
- [Flusso di dati per mapping](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

Per l'attività di copia, questo connettore di Azure Synapse Analytics supporta le funzioni seguenti:

- La copia di dati tramite l'autenticazione SQL e l'autenticazione token dell'applicazione Azure Active Directory (Azure AD) con entità servizio o identità gestite per le risorse di Azure.
- Come origine, il recupero di dati tramite query SQL o stored procedure. Per informazioni dettagliate, è anche possibile scegliere di eseguire la copia parallela da un'origine di analisi [sinapsi di Azure](#parallel-copy-from-synapse-analytics) .
- Come sink, il caricamento di dati tramite [PolyBase](#use-polybase-to-load-data-into-azure-synapse-analytics), un'[istruzione COPY](#use-copy-statement) (anteprima) o un inserimento bulk. Per migliorare le prestazioni di copia, è consigliabile usare PolyBase o l'istruzione COPY (anteprima). Il connettore supporta inoltre la creazione automatica della tabella di destinazione se non esiste in base allo schema di origine.

> [!IMPORTANT]
> Se si copiano i dati usando Azure Data Factory Integration Runtime, configurare una [regola del firewall a livello di server](../azure-sql/database/firewall-configure.md) in modo che i servizi di Azure possano accedere al [server SQL logico](../azure-sql/database/logical-servers.md).
> Se si copiano i dati usando un runtime di integrazione self-hosted, configurare il firewall in modo da consentire l'intervallo di indirizzi IP appropriato. Questo intervallo include l'indirizzo IP del computer usato per connettersi ad Azure Synapse Analytics.

## <a name="get-started"></a>Introduzione

> [!TIP]
> Per ottenere prestazioni ottimali, usare PolyBase per caricare i dati in Azure Synapse Analytics. Per i dettagli, vedere la sezione [Usare PolyBase per caricare dati in Azure Synapse Analytics](#use-polybase-to-load-data-into-azure-synapse-analytics). Per una procedura dettagliata con un caso d'uso, vedere [Caricare 1 TB di dati in Azure Synapse Analytics in meno di 15 minuti con Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti forniscono informazioni dettagliate sulle proprietà che definiscono entità di Data Factory specifiche per un connettore di Azure Synapse Analytics.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per un servizio collegato ad Azure Synapse Analytics sono supportate le proprietà seguenti:

| Proprietà            | Descrizione                                                  | Obbligatoria                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | La proprietà type deve essere impostata su **AzureSqlDW** .             | Sì                                                          |
| connectionString    | Specificare le informazioni necessarie per connettersi all'istanza di Azure Synapse Analytics per la proprietà **connectionString** . <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire la password/chiave entità servizio in Azure Key Vault e, se si tratta dell'autenticazione SQL, estrarre la configurazione `password` dalla stringa di connessione. Vedere gli esempi JSON sotto la tabella e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. | Sì                                                          |
| servicePrincipalId  | Specificare l'ID client dell'applicazione.                         | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| tenant              | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| azureCloudType | Per l'autenticazione dell'entità servizio, specificare il tipo di ambiente cloud di Azure in cui è registrata l'applicazione Azure AD. <br/> I valori consentiti sono **AzurePublic** , **AzureChina** , **AzureUsGovernment** e **AzureGermany** . Per impostazione predefinita, viene usato l'ambiente cloud del data factory. | No |
| connectVia          | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No                                                           |

Per altri tipi di autenticazione, fare riferimento alle sezioni seguenti relative, rispettivamente, ai prerequisiti e agli esempi JSON:

- [Autenticazione SQL](#sql-authentication)
- Autenticazione del token dell'applicazione Azure AD: [Entità servizio](#service-principal-authentication)
- Autenticazione del token dell'applicazione Azure AD: [Identità gestite per le risorse di Azure](#managed-identity)

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

1. **[Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)** nel portale di Azure. Prendere nota del nome dell'applicazione e dei valori seguenti che definiscono il servizio collegato:

   - ID applicazione
   - Chiave applicazione
   - ID tenant

2. Eseguire il **[provisioning di un amministratore Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** per il server nel portale di Azure se non è già stato fatto. L'amministratore di Azure AD può essere un utente di Azure AD o gruppo di Azure AD. Se si concede al gruppo con identità gestita un ruolo di amministratore, ignorare i passaggi 3 e 4. L'amministratore avrà accesso completo al database.

3. **[Creare utenti del database indipendente](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** per l'entità servizio. Connettersi al data warehouse da o in cui si vogliono copiare i dati usando strumenti come SSMS, con un'identità di Azure AD che abbia almeno l'autorizzazione ALTER ANY USER. Eseguire il codice T-SQL seguente:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Concedere all'entità servizio le autorizzazioni necessarie** , come si fa di norma per gli utenti SQL o altri utenti. Eseguire il codice seguente o vedere altre opzioni [qui](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Se si vuole usare PolyBase per caricare i dati, vedere le informazioni sull'[autorizzazione necessaria per il database](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Configurare un servizio collegato ad Azure Synapse Analytics** in Azure Data Factory.

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

### <a name="managed-identities-for-azure-resources-authentication"></a><a name="managed-identity"></a>Autenticazione di identità gestite per le risorse di Azure

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la factory specifica. È possibile usare questa identità gestita per l'autenticazione di Azure Synapse Analytics. La factory designata può accedere ai dati e copiarli da o nel data warehouse tramite questa identità.

Per usare l'autenticazione tramite identità gestita, eseguire questa procedura:

1. Eseguire il **[provisioning di un amministratore Azure Active Directory](../azure-sql/database/authentication-aad-configure.md#provision-azure-ad-admin-sql-database)** per il server nel portale di Azure, se non è già stato fatto. L'amministratore di Azure AD può essere un utente di Azure AD o gruppo di Azure AD. Se si concede al gruppo con identità gestita un ruolo di amministratore, ignorare i passaggi 3 e 4. L'amministratore avrà accesso completo al database.

2. **[Creare utenti del database indipendente](../azure-sql/database/authentication-aad-configure.md#create-contained-users-mapped-to-azure-ad-identities)** per l'identità gestita di Data Factory. Connettersi al data warehouse da o in cui si vogliono copiare i dati usando strumenti come SSMS, con un'identità di Azure AD che abbia almeno l'autorizzazione ALTER ANY USER. Eseguire il comando in T-SQL seguente.
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Concedere le autorizzazioni necessarie per l'identità gestita di Data Factory** , come si fa normalmente per gli utenti SQL e altri utenti. Eseguire il codice seguente o vedere altre opzioni [qui](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql). Se si vuole usare PolyBase per caricare i dati, vedere le informazioni sull'[autorizzazione necessaria per il database](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

4. **Configurare un servizio collegato ad Azure Synapse Analytics** in Azure Data Factory.

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md).

Per un set di dati di Azure Synapse Analytics sono supportate le proprietà seguenti:

| Proprietà  | Descrizione                                                  | Obbligatoria                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | La proprietà **type** del set di dati deve essere impostata su **AzureSqlDWTable** . | Sì                         |
| schema | Nome dello schema. |No per l'origine, Sì per il sink  |
| tabella | Nome della tabella/vista. |No per l'origine, Sì per il sink  |
| tableName | Nome della tabella/vista con schema. Questa proprietà è supportata per garantire la compatibilità con le versioni precedenti. Per i nuovi carichi di lavoro, usare `schema` e `table`. | No per l'origine, Sì per il sink |

### <a name="dataset-properties-example"></a>Esempio di proprietà dei set di dati

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di Azure Synapse Analytics.

### <a name="azure-synapse-analytics-as-the-source"></a>Azure Synapse Analytics come origine

>[!TIP]
>Per caricare in modo efficiente i dati da Azure sinapsi Analytics usando il partizionamento dei dati, vedere la [copia parallela da sinapsi Analytics](#parallel-copy-from-synapse-analytics).

Per copiare dati da Azure Synapse Analytics, impostare la proprietà **type** nell'origine dell'attività di copia su **SqlDWSource** . Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà                     | Descrizione                                                  | Obbligatoria |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **SqlDWSource** . | Sì      |
| sqlReaderQuery               | Usare la query SQL personalizzata per leggere i dati. Esempio: `select * from MyTable`. | No       |
| sqlReaderStoredProcedureName | Nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. | No       |
| storedProcedureParameters    | Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No       |
| isolationLevel | Specifica il comportamento di blocco della transazione per l'origine SQL. I valori consentiti sono: **ReadCommitted** , **READUNCOMMITTED** , **RepeatableRead** , **Serializable** , **snapshot** . Se non specificato, viene usato il livello di isolamento predefinito del database. Per altre informazioni dettagliate, vedere [questo documento](/dotnet/api/system.data.isolationlevel). | No |
| partitionOptions | Specifica le opzioni di partizionamento dei dati usate per caricare i dati da Azure sinapsi Analytics. <br>I valori consentiti sono: **None** (impostazione predefinita), **PhysicalPartitionsOfTable** e **DynamicRange** .<br>Quando è abilitata un'opzione di partizione (ovvero non `None` ), il grado di parallelismo per caricare simultaneamente i dati da un'analisi di sinapsi di Azure è controllato dall' [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) impostazione dell'attività di copia. | No |
| partitionSettings | Specifica il gruppo di impostazioni per il partizionamento dei dati. <br>Applicare quando l'opzione partition non è `None` . | No |
| **_In `partitionSettings` :_* _ | | |
| partitionColumnName | Specificare il nome della colonna di origine _ *nel tipo Integer o data/datetime* * che verrà utilizzato dal partizionamento dell'intervallo per la copia parallela. Se non è specificato, l'indice o la chiave primaria della tabella vengono rilevati automaticamente e utilizzati come colonna della partizione.<br>Si applica quando l'opzione di partizione è `DynamicRange`. Se si utilizza una query per recuperare i dati di origine, associare  `?AdfDynamicRangePartitionCondition ` la clausola WHERE. Per un esempio, vedere la sezione [copia parallela da database SQL](#parallel-copy-from-synapse-analytics) . | No |
| partitionUpperBound | Valore massimo della colonna di partizione per la suddivisione dell'intervallo di partizioni. Questo valore viene usato per decidere lo stride della partizione, non per filtrare le righe nella tabella. Tutte le righe della tabella o del risultato della query verranno partizionate e copiate. Se non è specificato, l'attività di copia rileva automaticamente il valore.  <br>Si applica quando l'opzione di partizione è `DynamicRange`. Per un esempio, vedere la sezione [copia parallela da database SQL](#parallel-copy-from-synapse-analytics) . | No |
| partitionLowerBound | Valore minimo della colonna di partizione per la suddivisione dell'intervallo di partizioni. Questo valore viene usato per decidere lo stride della partizione, non per filtrare le righe nella tabella. Tutte le righe della tabella o del risultato della query verranno partizionate e copiate. Se non è specificato, l'attività di copia rileva automaticamente il valore.<br>Si applica quando l'opzione di partizione è `DynamicRange`. Per un esempio, vedere la sezione [copia parallela da database SQL](#parallel-copy-from-synapse-analytics) . | No |

**Esempio: uso della query SQL**

```json
"activities":[
    {
        "name": "CopyFromAzureSQLDW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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
                "referenceName": "<Azure Synapse Analytics input dataset name>",
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

### <a name="azure-synapse-analytics-as-sink"></a><a name="azure-sql-data-warehouse-as-sink"></a> Azure Synapse Analytics come sink

Azure Data Factory supporta tre modi per caricare i dati in Azure sinapsi Analytics.

![Opzioni di copia del sink di Azure sinapsi Analytics](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [Usare PolyBase](#use-polybase-to-load-data-into-azure-synapse-analytics)
- [Usare l'istruzione COPY (anteprima)](#use-copy-statement)
- Usare l'inserimento bulk

Il modo più rapido e scalabile per caricare dati è offerto da [PolyBase](/sql/relational-databases/polybase/polybase-guide) o dall'[istruzione COPY](/sql/t-sql/statements/copy-into-transact-sql) (anteprima).

Per copiare dati in Azure sinapsi Analytics, impostare il tipo di sink nell'attività di copia su **SqlDWSink** . Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà          | Descrizione                                                  | Obbligatoria                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | La proprietà **type** del sink dell'attività di copia deve essere impostata su **SqlDWSink** . | Sì                                           |
| allowPolyBase     | Indica se usare la polibase per caricare i dati in Azure sinapsi Analytics. `allowCopyCommand` e `allowPolyBase` non possono essere entrambi true. <br/><br/>Per i vincoli e i dettagli, vedere [usare la codebase per caricare i dati nella sezione analisi delle sinapsi di Azure](#use-polybase-to-load-data-into-azure-synapse-analytics) .<br/><br/>I valori consentiti sono **True** e **False** (predefinito). | No.<br/>Applicare quando si usa PolyBase.     |
| polyBaseSettings  | Gruppo di proprietà che è possibile specificare quando la proprietà `allowPolybase` è impostata su **true** . | No.<br/>Applicare quando si usa PolyBase. |
| allowCopyCommand | Indica se usare l' [istruzione Copy](/sql/t-sql/statements/copy-into-transact-sql) (anteprima) per caricare i dati in Azure sinapsi Analytics. `allowCopyCommand` e `allowPolyBase` non possono essere entrambi true. <br/><br/>Vedere [usare l'istruzione Copy per caricare i dati nella sezione analisi di Azure sinapsi](#use-copy-statement) per i vincoli e i dettagli.<br/><br/>I valori consentiti sono **True** e **False** (predefinito). | No.<br>Applicare quando si usa COPY. |
| copyCommandSettings | Gruppo di proprietà che è possibile specificare quando la proprietà `allowCopyCommand` è impostata su TRUE. | No.<br/>Applicare quando si usa COPY. |
| writeBatchSize    | Numero di righe da inserire nella tabella SQL **per batch** .<br/><br/>Il valore consentito è **integer** (numero di righe). Per impostazione predefinita, Data Factory determina in modo dinamico le dimensioni del batch appropriate in base alle dimensioni della riga. | No.<br/>Applicare quando si usa l'inserimento bulk.     |
| writeBatchTimeout | Tempo di attesa per il completamento dell'operazione di inserimento batch prima del timeout.<br/><br/>Il valore consentito è **timespan** . Esempio: "00:30:00" (30 minuti). | No.<br/>Applicare quando si usa l'inserimento bulk.        |
| preCopyScript     | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati in Azure sinapsi Analytics a ogni esecuzione. Usare questa proprietà per pulire i dati precaricati. | No                                            |
| tableOption | Specifica se [creare automaticamente la tabella di sink](copy-activity-overview.md#auto-create-sink-tables) se non esiste in base allo schema di origine. I valori consentiti sono: `none` (impostazione predefinita), `autoCreate`. |No |
| disableMetricsCollection | Data Factory raccoglie metriche come Azure sinapsi Analytics DWU per l'ottimizzazione delle prestazioni di copia e consigli. Se questo comportamento non è desiderato, specificare `true` per disattivarlo. | No (il valore predefinito è `false`) |

#### <a name="azure-synapse-analytics-sink-example"></a>Esempio di sink di analisi delle sinapsi di Azure

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

## <a name="parallel-copy-from-synapse-analytics"></a>Copia parallela dall'analisi delle sinapsi

Il connettore Azure sinapsi Analytics nell'attività di copia fornisce il partizionamento dei dati predefinito per copiare i dati in parallelo. È possibile trovare le opzioni di partizionamento dei dati nella scheda **origine** dell'attività di copia.

![Screenshot delle opzioni di partizione](./media/connector-sql-server/connector-sql-partition-options.png)

Quando si Abilita la copia partizionata, l'attività di copia esegue query parallele sull'origine di analisi sinapsi di Azure per caricare i dati in base alle partizioni. Il grado di parallelismo è controllato dall'impostazione [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) sull'attività di copia. Se, ad esempio, si imposta `parallelCopies` su quattro, data factory genera ed esegue contemporaneamente quattro query in base all'opzione di partizione specificata e alle impostazioni e ogni query recupera una porzione di dati da Azure sinapsi Analytics.

Si consiglia di abilitare la copia parallela con il partizionamento dei dati, specialmente quando si caricano grandi quantità di dati da Azure sinapsi Analytics. Di seguito sono riportate le configurazioni consigliate per i diversi scenari: Quando si copiano dati in un archivio dati basato su file, è consigliabile scrivere in una cartella come più file (specificare solo il nome della cartella), nel qual caso le prestazioni sono migliori rispetto alla scrittura in un singolo file.

| Scenario                                                     | Impostazioni consigliate                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Caricamento completo da tabelle di grandi dimensioni, con partizioni fisiche.        | **Opzione partition** : partizioni fisiche della tabella. <br><br/>Durante l'esecuzione, Data Factory rileva automaticamente le partizioni fisiche e copia i dati in base alle partizioni. <br><br/>Per verificare se la tabella contiene o meno una partizione fisica, è possibile fare riferimento a [questa query](#sample-query-to-check-physical-partition). |
| Caricamento completo da tabelle di grandi dimensioni, senza partizioni fisiche, con una colonna integer o DateTime per il partizionamento dei dati. | **Opzioni di partizione** : Partizione a intervalli dinamici.<br>**Colonna partizione** (facoltativo): specificare la colonna utilizzata per partizionare i dati. Se non è specificato, viene utilizzata la colonna di chiave primaria o di indice.<br/>Limite **superiore** della partizione e **limite inferiore della partizione** (facoltativo): specificare se si vuole determinare lo stride della partizione. Questa operazione non è relativa al filtraggio delle righe nella tabella. tutte le righe della tabella verranno partizionate e copiate. Se non è specificato, l'attività di copia rileva automaticamente i valori.<br><br>Se, ad esempio, la colonna di partizione "ID" ha valori compresi tra 1 e 100 e si imposta il limite inferiore su 20 e il limite superiore come 80, con la copia parallela 4, Data Factory recupera i dati di 4 partizioni-ID nell'intervallo <= 20, [21, 50], [51, 80] e >= 81 rispettivamente. |
| Caricare una grande quantità di dati tramite una query personalizzata, senza partizioni fisiche, con una colonna integer o date/DateTime per il partizionamento dei dati. | **Opzioni di partizione** : Partizione a intervalli dinamici.<br>**Query** : `SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`.<br>**Colonna di partizione** : Specificare la colonna usata per partizionare i dati.<br>Limite **superiore** della partizione e **limite inferiore della partizione** (facoltativo): specificare se si vuole determinare lo stride della partizione. Non per filtrare le righe nella tabella, tutte le righe nel risultato della query verranno partizionate e copiate. Se non è specificato, l'attività di copia rileva automaticamente il valore.<br><br>Durante l'esecuzione, Data Factory sostituisce `?AdfRangePartitionColumnName` con il nome della colonna e gli intervalli di valori effettivi per ogni partizione e Invia ad Azure sinapsi Analytics. <br>Se, ad esempio, la colonna di partizione "ID" ha valori compresi tra 1 e 100 e si imposta il limite inferiore su 20 e il limite superiore come 80, con la copia parallela 4, Data Factory recupera i dati di 4 partizioni-ID nell'intervallo <= 20, [21, 50], [51, 80] e >= 81 rispettivamente. <br><br>Di seguito sono riportate altre query di esempio per scenari diversi:<br> 1. eseguire una query sull'intera tabella: <br>`SELECT * FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition`<br> 2. eseguire una query da una tabella con la selezione delle colonne e i filtri aggiuntivi della clausola WHERE: <br>`SELECT <column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 3. eseguire una query con sottoquery: <br>`SELECT <column_list> FROM (<your_sub_query>) AS T WHERE ?AdfDynamicRangePartitionCondition AND <your_additional_where_clause>`<br> 4. query con partizione in sottoquery: <br>`SELECT <column_list> FROM (SELECT <your_sub_query_column_list> FROM <TableName> WHERE ?AdfDynamicRangePartitionCondition) AS T`
|

Procedure consigliate per caricare i dati con l'opzione di partizione:

1. Scegliere una colonna distinta come colonna di partizione, ad esempio chiave primaria o chiave univoca, per evitare l'asimmetria dei dati. 
2. Se la tabella include una partizione predefinita, usare l'opzione di partizione "partizioni fisiche della tabella" per ottenere prestazioni migliori.
3. Se si usa Azure Integration Runtime per copiare i dati, è possibile impostare "[Data Integration Unit (DIU)](copy-activity-performance-features.md#data-integration-units)" di dimensioni maggiori (>4) per usare più risorse di elaborazione. Controllare gli scenari applicabili.
4. "[Degree of copy parallelism](copy-activity-performance-features.md#parallel-copy)" controlla i numeri di partizione, impostando questo numero su un valore troppo grande a volte, è consigliabile impostare questo numero come (Diu o numero di nodi IR self-hosted) * (da 2 a 4).
5. Nota: l'analisi delle sinapsi di Azure può eseguire un massimo di 32 query al momento. l'impostazione di "grado di parallelismo della copia" troppo grande può causare un problema di limitazione delle richieste sinapsi.

**Esempio: caricamento completo da tabelle di grandi dimensioni con partizioni fisiche**

```json
"source": {
    "type": "SqlDWSource",
    "partitionOption": "PhysicalPartitionsOfTable"
}
```

**Esempio: query con partizione a intervalli dinamici**

```json
"source": {
    "type": "SqlDWSource",
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
SELECT DISTINCT s.name AS SchemaName, t.name AS TableName, c.name AS ColumnName, CASE WHEN c.name IS NULL THEN 'no' ELSE 'yes' END AS HasPartition
FROM sys.tables AS t
LEFT JOIN sys.objects AS o ON t.object_id = o.object_id
LEFT JOIN sys.schemas AS s ON o.schema_id = s.schema_id
LEFT JOIN sys.indexes AS i ON t.object_id = i.object_id
LEFT JOIN sys.index_columns AS ic ON ic.partition_ordinal > 0 AND ic.index_id = i.index_id AND ic.object_id = t.object_id
LEFT JOIN sys.columns AS c ON c.object_id = ic.object_id AND c.column_id = ic.column_id
LEFT JOIN sys.types AS y ON c.system_type_id = y.system_type_id
WHERE s.name='[your schema]' AND t.name = '[your table name]'
```

Se la tabella contiene una partizione fisica, verrà visualizzato "HasPartition" come "Yes".

## <a name="use-polybase-to-load-data-into-azure-synapse-analytics"></a>Usare la polibase per caricare i dati in Azure sinapsi Analytics

[PolyBase](/sql/relational-databases/polybase/polybase-guide) consente di caricare in modo efficiente grandi quantità di dati in Azure Synapse Analytics con una velocità effettiva elevata. L'uso di PolyBase consente un miglioramento significativo della velocità effettiva rispetto al meccanismo BULKINSERT predefinito. Per una procedura dettagliata con un caso d'uso, vedere [Caricare 1 TB di dati in Azure Synapse Analytics](v1/data-factory-load-sql-data-warehouse.md).

- Se i dati di origine si trova in un **BLOB di Azure, Azure Data Lake storage Gen1 o Azure Data Lake storage Gen2** e il **formato è compatibile con la polibase** , è possibile usare l'attività di copia per richiamare direttamente la polibase per consentire ad Azure sinapsi Analytics di estrarre i dati dall'origine. Per maggiori dettagli, vedere **[Copia diretta tramite PolyBase](#direct-copy-by-using-polybase)** .
- Se l'archivio e il formato dei dati di origine non sono supportati in origine da PolyBase, usare la funzionalità **[copia di staging tramite PolyBase](#staged-copy-by-using-polybase)** . La funzionalità copia di staging assicura inoltre una migliore velocità effettiva, Converte automaticamente i dati in un formato compatibile con polibase, archivia i dati nell'archiviazione BLOB di Azure e quindi chiama la polibase per caricare i dati in Azure sinapsi Analytics.

> [!TIP]
> Per altre informazioni, vedere [Procedure consigliate per l'uso di PolyBase](#best-practices-for-using-polybase).

Le impostazioni di PolyBase seguenti sono supportate in `polyBaseSettings` nell'attività di copia:

| Proprietà          | Descrizione                                                  | Obbligatoria                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Specifica il numero o la percentuale di righe che è possibile rifiutare prima che la query abbia esito negativo.<br/><br/>Per altre informazioni sulle opzioni di rifiuto di PolyBase, vedere la sezione Argomenti in [CREATE EXTERNAL TABLE (Transact-SQL)](/sql/t-sql/statements/create-external-table-transact-sql). <br/><br/>I valori consentiti sono 0 (predefinito), 1, 2 e così via. | No                                            |
| rejectType        | Indica se l'opzione **rejectValue** viene specificata come valore letterale o come percentuale.<br/><br/>I valori consentiti sono **Value** (predefinito) e **Percentage** . | No                                            |
| rejectSampleValue | Determina il numero di righe da recuperare prima che PolyBase ricalcoli la percentuale di righe rifiutate.<br/><br/>I valori consentiti sono 1, 2 e così via. | Sì se **rejectType** è **percentage** . |
| useTypeDefault    | Specifica come gestire valori mancanti nei file di testo delimitato quando PolyBase recupera i dati dal file di testo.<br/><br/>Per altre informazioni su questa proprietà, vedere la sezione Arguments (Argomenti) in [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](/sql/t-sql/statements/create-external-file-format-transact-sql).<br/><br/>I valori consentiti sono **True** e **False** (predefinito).<br><br> | No                                            |

### <a name="direct-copy-by-using-polybase"></a>Copia diretta tramite PolyBase

La polibase Azure sinapsi Analytics supporta direttamente BLOB, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2 di Azure. Se i dati di origine soddisfano i criteri descritti in questa sezione, usare PolyBase per copiare direttamente dall'archivio dati di origine ad Azure Synapse Analytics. In caso contrario, usare la [copia di staging tramite PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Per copiare i dati in modo efficiente in Azure sinapsi Analytics, è possibile ottenere altre informazioni da [Azure Data Factory rende ancora più semplice e pratico l'individuazione di informazioni dettagliate dai dati quando si usa data Lake Store con analisi delle sinapsi di Azure](/archive/blogs/azuredatalake/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse).

Se i requisiti non vengono soddisfatti, Azure Data Factory controlla le impostazioni e usa automaticamente il meccanismo BULKINSERT per lo spostamento dei dati.

1. Il **servizio collegato all'origine** ha i tipi e i metodi di autenticazione seguenti:

    | Tipo di archivio dati di origine supportato                             | Tipo di autenticazione di origine supportato                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [BLOB di Azure](connector-azure-blob-storage.md)                | Autenticazione della chiave dell'account, autenticazione tramite identità gestita |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Autenticazione di un'entità servizio                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Autenticazione della chiave dell'account, autenticazione tramite identità gestita |

    >[!IMPORTANT]
    >Se l'archiviazione di Azure è configurata con l'endpoint del servizio VNet, è necessario usare l'autenticazione tramite identità gestita. Vedere [Impatto dell'uso degli endpoint di servizio di rete virtuale con Archiviazione di Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Per informazioni sulle configurazioni necessarie in Data Factory, vedere rispettivamente le sezioni [Archiviazione BLOB di Azure - autenticazione dell'identità gestita](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2 - autenticazione dell'identità gestita](connector-azure-data-lake-storage.md#managed-identity).

2. Il **formato dei dati di origine** è **Parquet** , **ORC** o **Testo delimitato** , con le configurazioni seguenti:

   1. Il percorso della cartella non contiene il filtro con caratteri jolly.
   2. Il nome file è vuoto o punta a un singolo file. Se si specifica il nome file con caratteri jolly nell'attività di copia, può essere solo `*` o `*.*`.
   3. `rowDelimiter` è **impostazione predefinita** , **\n** , **\r\n** o **\r** .
   4. `nullValue` è impostato sul valore predefinito o su una **stringa vuota** ("") e `treatEmptyAsNull` è impostato sul valore predefinito o su true.
   5. `encodingName` è impostato sul valore predefinito o su **utf-8** .
   6. `quoteChar`, `escapeChar` e `skipLineCount` non sono specificati. Il supporto di PolyBase ignora la riga di intestazione che può essere configurata come `firstRowAsHeader` in Azure Data Factory.
   7. `compression` può essere **no compression** , **GZip** o **Deflate** .

3. Se l'origine è una cartella, `recursive` nell'attività di copia deve essere impostato su true.

4. `wildcardFolderPath` , `wildcardFilename` , `modifiedDateTimeStart` , `modifiedDateTimeEnd` , `prefix` `enablePartitionDiscovery` e `additionalColumns` non sono specificati.

>[!NOTE]
>Se l'origine è una cartella, PolyBase recupera i file dalla cartella e da tutte le relative sottocartelle e non recupera i dati dai file il cui nome inizia con un carattere di sottolineatura (_) o un punto (.), come documentato nella sezione [Argomenti](/sql/t-sql/statements/create-external-table-transact-sql#arguments-2) in relazione all'argomento LOCATION.

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

Quando i dati di origine non sono compatibili in modo nativo con la polibase, abilitare la copia dei dati tramite un BLOB di Azure di staging provvisorio o Azure Data Lake Storage Gen2 (non può essere l'archiviazione Premium di Azure). In questo caso, Azure Data Factory converte automaticamente i dati in modo che soddisfino i requisiti di formato dei dati di PolyBase. Richiama quindi la polibase per caricare i dati in Azure sinapsi Analytics. Infine, pulisce i dati temporanei dall'archiviazione. Per informazioni dettagliate sulla copia di dati tramite una gestione temporanea, vedere [copia](copy-activity-performance-features.md#staged-copy) di staging.

Per usare questa funzionalità, creare un [servizio collegato di archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties) o [Azure Data Lake storage Gen2 servizio collegato](connector-azure-data-lake-storage.md#linked-service-properties) con la **chiave dell'account o l'autenticazione dell'identità gestita** che fa riferimento all'account di archiviazione di Azure come archivio provvisorio.

>[!IMPORTANT]
>Se l'istanza di staging di Archiviazione di Azure è configurata con l'endpoint del servizio VNet, è necessario usare l'autenticazione tramite identità gestita. Vedere [Impatto dell'uso degli endpoint di servizio di rete virtuale con Archiviazione di Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Informazioni sulle configurazioni richieste in Data Factory dall'autenticazione di [identità gestita da BLOB di Azure](connector-azure-blob-storage.md#managed-identity) e dall' [autenticazione di identità gestita da Azure Data Lake storage Gen2](connector-azure-data-lake-storage.md#managed-identity).

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
                    "referenceName": "MyStagingStorage",
                    "type": "LinkedServiceReference"
                }
            }
        }
    }
]
```

### <a name="best-practices-for-using-polybase"></a>Procedure consigliate per l'uso di PolyBase

Le sezioni seguenti illustrano le procedure consigliate in aggiunta a quelle descritte in [Procedure consigliate per Azure Synapse Analytics](../synapse-analytics/sql/best-practices-sql-pool.md).

#### <a name="required-database-permission"></a>Autorizzazione database obbligatoria

Per usare la polibase, l'utente che carica i dati in Azure sinapsi Analytics deve disporre dell' [autorizzazione "Control"](/sql/relational-databases/security/permissions-database-engine) per il database di destinazione. Un modo per ottenere questo risultato consiste nell'aggiungere l'utente come membro del ruolo **db_owner** . Per informazioni su come eseguire questa operazione, vedere [Panoramica di Azure sinapsi Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Limitazioni alle dimensioni delle righe e al tipo di dati

Le operazioni di caricamento di PolyBase sono limitate alle righe inferiori a 1 MB, e non è possibile usare PolyBase per caricare dati in VARCHR(MAX), NVARCHAR(MAX), or VARBINARY(MAX). Per altre informazioni, vedere [limiti di capacità del servizio di analisi delle sinapsi di Azure](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Quando i dati di origine hanno righe di dimensioni superiori a 1 MB, è consigliabile suddividere verticalmente le tabelle di origine in tabelle più piccole. Assicurarsi che le dimensioni massime di ogni riga non superino il limite previsto. Le tabelle più piccole possono essere quindi caricate usando PolyBase e unite in Azure Synapse Analytics.

In alternativa, per i dati con colonne di grandi dimensioni, è possibile usare un'opzione diversa da PolyBase per caricare i dati tramite Azure Data Factory, disattivando l'impostazione "Allow PolyBase" (Consenti PolyBase).

#### <a name="azure-synapse-analytics-resource-class"></a>Classe di risorse di analisi di sinapsi di Azure

Per ottenere la migliore velocità effettiva possibile, assegnare una classe di risorse più ampia all'utente che carica i dati in Azure sinapsi Analytics tramite la polibase.

#### <a name="polybase-troubleshooting"></a>Risoluzione dei problemi di PolyBase

**Caricamento nella colonna Decimal**

Se i dati di origine sono in formato testo o in altri archivi compatibili non di base (usando la copia di staging e la polibase) e contiene un valore vuoto da caricare nella colonna decimal di Azure sinapsi Analytics, è possibile che venga raggiunto l'errore seguente:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

La soluzione consiste nel deselezionare l'opzione " **Use type default** " (Tipo di uso predefinito) (false) nel sink dell'attività di copia -> PolyBase Settings (Impostazioni PolyBase). "[USE_TYPE_DEFAULT](/sql/t-sql/statements/create-external-file-format-transact-sql#arguments)" è una configurazione nativa di PolyBase che specifica come gestire valori mancanti nei file di testo delimitato quando PolyBase recupera i dati dal file di testo.

**`tableName` in Azure Synapse Analytics**

La tabella seguente fornisce alcuni esempi di come specificare la proprietà **tableName** nel set di dati JSON, mostrando diverse combinazioni di nomi di schema e di tabella.

| Schema di database | Nome tabella | Proprietà JSON **tableName**               |
| --------- | ---------- | ----------------------------------------- |
| dbo       | MyTable    | MyTable o dbo.MyTable o [dbo].[MyTable] |
| dbo1      | MyTable    | dbo1.MyTable o [dbo1].[MyTable]          |
| dbo       | My.Table   | [My.Table] o [dbo].[My.Table]            |
| dbo1      | My.Table   | [dbo1].[My.Table]                         |

Se viene visualizzato l'errore seguente, il problema potrebbe essere costituito dal valore specificato per la proprietà **tableName** . Per informazioni sul modo corretto di specificare i valori per la proprietà JSON **tableName** , vedere la tabella precedente.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

**Colonne con valori predefiniti**

Attualmente, la funzionalità PolyBase in Data Factory accetta solo lo stesso numero di colonne disponibili nella tabella di destinazione. Nel caso di una tabella con quattro colonne di cui una definita con un valore predefinito, ad esempio, i dati di input devono comunque contenere quattro colonne. Un set di dati di input con tre colonne restituisce un errore simile al messaggio seguente:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Il valore NULL è una forma speciale di valore predefinito. Se la colonna ammette valori Null, i dati di input nel BLOB per tale colonna possono essere vuoti, ma non possono essere mancanti dal set di dati di input. PolyBase inserisce NULL per i valori mancanti in Azure Synapse Analytics.

## <a name="use-copy-statement-to-load-data-into-azure-synapse-analytics-preview"></a><a name="use-copy-statement"></a> Usare l'istruzione COPY per caricare i dati in Azure sinapsi Analytics (anteprima)

L' [istruzione Copy](/sql/t-sql/statements/copy-into-transact-sql) (anteprima) di Azure sinapsi Analytics supporta direttamente il caricamento di dati dal **BLOB di Azure e Azure Data Lake storage Gen2** . Se i dati di origine soddisfano i criteri descritti in questa sezione, è possibile scegliere di usare l'istruzione COPY in ADF per caricare i dati in Azure sinapsi Analytics. Azure Data Factory controlla le impostazioni e, se i criteri non vengono soddisfatti, l'esecuzione dell'attività di copia non riesce.

>[!NOTE]
>Attualmente Data Factory supporta la copia solo dalle origini compatibili con l'istruzione COPY indicate di seguito.

L'uso dell'istruzione COPY supporta la configurazione seguente:

1. Il **servizio collegato all'origine e il formato** hanno i tipi e i metodi di autenticazione seguenti:

    | Tipo di archivio dati di origine supportato                             | Formato supportato           | Tipo di autenticazione di origine supportato                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [BLOB di Azure](connector-azure-blob-storage.md)                | [Testo delimitato](format-delimited-text.md)             | Autenticazione della chiave dell'account, autenticazione con firma di accesso condiviso, autenticazione basata su entità servizio, autenticazione tramite identità gestita |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Autenticazione della chiave dell'account, autenticazione con firma di accesso condiviso |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Autenticazione della chiave dell'account, autenticazione con firma di accesso condiviso |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Testo delimitato](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Autenticazione della chiave dell'account, autenticazione basata su entità servizio, autenticazione tramite identità gestita |

    >[!IMPORTANT]
    >Se l'archiviazione di Azure è configurata con l'endpoint del servizio VNet, è necessario usare l'autenticazione tramite identità gestita. Vedere [Impatto dell'uso degli endpoint di servizio di rete virtuale con Archiviazione di Azure](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Per informazioni sulle configurazioni necessarie in Data Factory, vedere rispettivamente le sezioni [Archiviazione BLOB di Azure - autenticazione dell'identità gestita](connector-azure-blob-storage.md#managed-identity) e [Azure Data Lake Storage Gen2 - autenticazione dell'identità gestita](connector-azure-data-lake-storage.md#managed-identity).

2. Le impostazioni del formato sono le seguenti:

   1. Per **Parquet** : `compression` può essere **no compression** , **Snappy** o **GZip** .
   2. Per **ORC** : `compression` può essere **no compression** , **```zlib```** o **Snappy** .
   3. Per **Testo delimitato** :
      1. `rowDelimiter` è impostato in modo esplicito come **single character** o " **\r\n** ", il valore predefinito non è supportato.
      2. `nullValue` è impostato sul valore predefinito o su **empty string** ("").
      3. `encodingName` è impostato sul valore predefinito o su **utf-8 o utf-16** .
      4. `escapeChar` deve essere uguale a `quoteChar` e non è vuoto.
      5. `skipLineCount` è impostato sul valore predefinito o su 0.
      6. `compression` può essere **no compression** o **GZip** .

3. Se l'origine è una cartella, `recursive` nell'attività di copia deve essere impostato su true e `wildcardFilename` deve essere `*`. 

4. `wildcardFolderPath` , `wildcardFilename` (diverso da `*` ), `modifiedDateTimeStart` , `modifiedDateTimeEnd` , `prefix` `enablePartitionDiscovery` e `additionalColumns` non sono specificati.

Le impostazioni dell'istruzione COPY seguenti sono supportate in `allowCopyCommand` nell'attività di copia:

| Proprietà          | Descrizione                                                  | Obbligatoria                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | Specifica i valori predefiniti per ogni colonna di destinazione in Azure sinapsi Analytics.  I valori predefiniti nella proprietà sovrascrivono il vincolo DEFAULT impostato nel data warehouse e la colonna Identity non può avere un valore predefinito. | No |
| additionalOptions | Opzioni aggiuntive che verranno passate a un'istruzione COPY di Azure sinapsi Analytics direttamente nella clausola "with" nell' [istruzione Copy](/sql/t-sql/statements/copy-into-transact-sql). Racchiudere il valore tra virgolette come previsto dai requisiti dell'istruzione COPY. | No |

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

## <a name="mapping-data-flow-properties"></a>Proprietà del flusso di dati per mapping

Durante la trasformazione dei dati in un flusso di dati per mapping è possibile leggere e scrivere in tabelle di Azure Synapse Analytics. Per altre informazioni, vedere la [trasformazione origine](data-flow-source.md) e la [trasformazione sink](data-flow-sink.md) nei flussi di dati per mapping.

### <a name="source-transformation"></a>Trasformazione origine

Le impostazioni specifiche di Azure Synapse Analytics sono disponibili nella scheda **Source Options** (Opzioni origine) della trasformazione origine.

**Input** di Consente di specificare se puntare l'origine a una tabella (equivalente a ```Select * from <table-name>``` ) o immettere una query SQL personalizzata.

**Abilita gestione temporanea** Si consiglia di usare questa opzione nei carichi di lavoro di produzione con origini sinapsi DW. Quando si esegue un'attività del flusso di dati con origini Synapase da una pipeline, ADF richiede un account di archiviazione del percorso di gestione temporanea che verrà usato per il caricamento di dati di staging. Si tratta del meccanismo più veloce per caricare i dati da sinapsi DW.

**Query** : se si seleziona Query nel campo di input, immettere una query SQL per l'origine. Questa impostazione esegue l'override di qualsiasi tabella scelta nel set di dati. Le clausole **Order By** non sono supportate, ma è possibile impostare un'istruzione SELECT FROM completa. È possibile usare anche funzioni di tabella definite dall'utente. **select * from udfGetData()** è un UDF in SQL che restituisce una tabella. Questa query produrrà una tabella di origine che può essere usata nel flusso di dati. L'uso di query è anche un ottimo modo per ridurre le righe per i test o le ricerche.

Esempio SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Dimensioni batch** : Immettere una dimensione batch per suddividere dati di grandi dimensioni in letture. Nei flussi di dati, Azure Data Factory userà questa impostazione per impostare la memorizzazione nella cache a colonne Spark. Si tratta di un campo di opzione, che utilizzerà le impostazioni predefinite di Spark se viene lasciato vuoto.

**Livello di isolamento** : Il valore predefinito per le origini SQL nel flusso di dati per mapping è Read Uncommitted. È possibile cambiare il livello di isolamento in uno dei valori seguenti:

- Read Committed
- Read Uncommitted
- Repeatable Read
- Serializable *-None (ignora il livello di isolamento)

![Livello di isolamento](media/data-flow/isolationlevel.png "Livello di isolamento")

### <a name="sink-transformation"></a>Trasformazione sink

Le impostazioni specifiche di Azure Synapse Analytics sono disponibili nella scheda **Impostazioni** della trasformazione sink.

**Update method** (Metodo di aggiornamento): determina le operazioni consentite nella destinazione del database. Per impostazione predefinita, sono consentiti solo gli inserimenti. Per eseguire operazioni di aggiornamento, upsert o eliminazione di righe, è necessaria una trasformazione alter-row che applichi alle righe i tag corrispondenti alle azioni. Per le operazioni di aggiornamento, upsert ed eliminazione è necessario impostare una o più colonne chiave per determinare quale riga modificare.

**Table action** (Azione tabella): determina se ricreare o rimuovere tutte le righe dalla tabella di destinazione prima della scrittura.

- None (Nessuna): non verrà eseguita alcuna azione sulla tabella.
- Recreate table (Ricrea tabella): la tabella verrà eliminata e ricreata. Questa opzione è obbligatoria se si crea una nuova tabella in modo dinamico.
- Truncate table (Tronca tabella): verranno rimosse tutte le righe della tabella di destinazione.

**Enable staging** (Abilita staging): determina se usare o meno [PolyBase](/sql/relational-databases/polybase/polybase-guide) per la scrittura in Azure Synapse Analytics.

**Dimensioni batch** : controlla il numero di righe scritte in ogni bucket. Dimensioni batch più grandi migliorano l'ottimizzazione della compressione e della memoria, ma rischiano di causare eccezioni di memoria insufficiente durante la memorizzazione nella cache dei dati.

**Pre and Post SQL scripts** (Pre-script e post-script SQL): immettere script SQL a più righe che verranno eseguiti prima (pre-elaborazione) e dopo (post-elaborazione) la scrittura dei dati nel database sink.

![Script di pre-elaborazione e post-elaborazione](media/data-flow/prepost1.png "Script di elaborazione SQL")

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per altre informazioni sulle proprietà, vedere [Attività GetMetadata](control-flow-get-metadata-activity.md)

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapping dei tipi di dati per Azure Synapse Analytics

Quando si copiano i dati da o in Azure Synapse Analytics, vengono usati i mapping seguenti dai tipi di dati di Azure Synapse Analytics ai tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

>[!TIP]
>Vedere l'articolo [tipi di dati della tabella nell'analisi di sinapsi](../synapse-analytics/sql/develop-tables-data-types.md) di Azure sui tipi di dati supportati da Azure sinapsi Analytics e le soluzioni alternative per quelli non supportati.

| Tipo di dati di Azure Synapse Analytics    | Tipo di dati provvisorio di Data Factory |
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