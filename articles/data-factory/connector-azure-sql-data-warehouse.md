---
title: Copiare e trasformare i dati in Azure sinapsi Analytics
description: Informazioni su come copiare dati da e verso Azure sinapsi Analytics e trasformare i dati in Azure sinapsi Analytics usando Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/12/2019
ms.openlocfilehash: c23bba777a7a29f572764914cfa505d2b668683d
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830412"
---
# <a name="copy-and-transform-data-in-azure-synapse-analytics-formerly-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiare e trasformare i dati in Azure sinapsi Analytics (in precedenza Azure SQL Data Warehouse) usando Azure Data Factory 

> [!div class="op_single_selector" title1="Selezionare la versione del servizio Data Factory in uso:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versione corrente](connector-azure-sql-data-warehouse.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Azure sinapsi Analytics e usare il flusso di dati per trasformare i dati in Azure Data Lake Storage Gen2. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Azure sinapsi Analytics è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con tabella della [matrice di origine/sink supportata](copy-activity-overview.md)
- [Mapping del flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

Per l'attività di copia, questo connettore Azure sinapsi Analytics supporta le funzioni seguenti:

- La copia di dati tramite l'autenticazione SQL e l'autenticazione token dell'applicazione Azure Active Directory (Azure AD) con entità servizio o identità gestite per le risorse di Azure.
- Come origine, il recupero di dati tramite query SQL o stored procedure.
- Come sink, caricare i dati utilizzando l'istruzione di [base](#use-polybase-to-load-data-into-azure-sql-data-warehouse) o di [copia](#use-copy-statement) (anteprima) o BULK INSERT. Per migliorare le prestazioni di copia, è consigliabile usare l'istruzione di base o di copia (anteprima).

> [!IMPORTANT]
> Se si copiano i dati tramite il runtime di integrazione di Azure Data Factory, configurare un [firewall del server SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) per consentire ai servizi di Azure di accedere al server.
> Se si copiano i dati tramite un runtime di integrazione self-hosted, configurare il firewall del server SQL di Azure per consentire l'intervallo IP appropriato. Questo intervallo include l'IP del computer usato per la connessione ad Azure sinapsi Analytics.

## <a name="get-started"></a>Inizia oggi stesso

> [!TIP]
> Per ottenere prestazioni ottimali, usare la polibase per caricare i dati in Azure sinapsi Analytics. La sezione [use polibase to load data to Azure sinapsi Analytics](#use-polybase-to-load-data-into-azure-sql-data-warehouse) contiene informazioni dettagliate. Per una procedura dettagliata con un caso d'uso, vedere [caricare 1 TB in Azure sinapsi Analytics in meno di 15 minuti con Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che definiscono Data Factory entità specifiche di un connettore Azure sinapsi Analytics.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per un servizio collegato di Azure sinapsi Analytics sono supportate le proprietà seguenti:

| Proprietà            | Description                                                  | Obbligatorio                                                     |
| :------------------ | :----------------------------------------------------------- | :----------------------------------------------------------- |
| type                | La proprietà type deve essere impostata su **AzureSqlDW**.             | Sì                                                          |
| connectionString    | Specificare le informazioni necessarie per connettersi all'istanza di Azure sinapsi Analytics per la proprietà **ConnectionString** . <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire la password/chiave entità servizio in Azure Key Vault e, se si tratta dell'autenticazione SQL, estrarre la configurazione `password` dalla stringa di connessione. Vedere gli esempi JSON sotto la tabella e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. | Sì                                                          |
| servicePrincipalId  | Specificare l'ID client dell'applicazione.                         | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| tenant              | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| connectVia          | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No                                                           |

Per altri tipi di autenticazione, fare riferimento alle sezioni seguenti relative, rispettivamente, ai prerequisiti e agli esempi JSON:

- [Autenticazione SQL](#sql-authentication)
- Autenticazione token dell'applicazione Azure AD: [entità servizio](#service-principal-authentication)
- Autenticazione token dell'applicazione Azure AD: [identità gestite per le risorse di Azure](#managed-identity)

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

4. **Concedere all'entità servizio le autorizzazioni necessarie**, come si fa di norma per gli utenti SQL o altri utenti. Eseguire il codice riportato di seguito oppure fare riferimento a [più opzioni.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017) Se si desidera utilizzare la polibase per caricare i dati, acquisire informazioni sull' [autorizzazione necessaria](#required-database-permission)per il database.

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Configurare un servizio collegato di Azure sinapsi Analytics** in Azure Data Factory.


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

### <a name="managed-identity"></a>Autenticazione di identità gestite per le risorse di Azure

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la factory specifica. È possibile usare questa identità gestita per l'autenticazione di Azure sinapsi Analytics. La factory designata può accedere ai dati e copiarli da o nel data warehouse tramite questa identità.

Per utilizzare l'autenticazione di identità gestita, attenersi alla seguente procedura:

1. **[Effettuare il provisioning di un amministratore di Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** per il server SQL Azure nel portale di Azure, se non è già stato effettuato. L'amministratore di Azure AD può essere un utente di Azure AD o gruppo di Azure AD. Se si concede al gruppo con identità gestita un ruolo di amministratore, ignorare i passaggi 3 e 4. L'amministratore avrà accesso completo al database.

2. **[Creare utenti di database indipendente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** per l'identità gestita del data factory. Connettersi al data warehouse da o in cui si vogliono copiare i dati usando strumenti come SSMS, con un'identità di Azure AD che abbia almeno l'autorizzazione ALTER ANY USER. Eseguire il comando in T-SQL seguente. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Concedere le autorizzazioni necessarie per la data factory identità gestita** come si fa normalmente per gli utenti SQL e altri. Eseguire il codice riportato di seguito oppure fare riferimento a [più opzioni.](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017) Se si desidera utilizzare la polibase per caricare i dati, acquisire informazioni sull' [autorizzazione necessaria](#required-database-permission)per il database.

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Configurare un servizio collegato di Azure sinapsi Analytics** in Azure Data Factory.

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

Per il set di dati di analisi delle sinapsi di Azure sono supportate le proprietà seguenti:

| Proprietà  | Description                                                  | Obbligatorio                    |
| :-------- | :----------------------------------------------------------- | :-------------------------- |
| type      | La proprietà **type** del set di dati deve essere impostata su **AzureSqlDWTable**. | Sì                         |
| schema | Nome dello schema. |No per l'origine, Sì per il sink  |
| tabella | Nome della tabella o della vista. |No per l'origine, Sì per il sink  |
| tableName | Nome della tabella o della vista con schema. Questa proprietà è supportata per compatibilità con le versioni precedenti. Per il nuovo carico di lavoro, usare `schema` e `table`. | No per l'origine, Sì per il sink |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink di analisi delle sinapsi di Azure.

### <a name="azure-synapse-analytics-as-the-source"></a>Analisi delle sinapsi di Azure come origine

Per copiare dati da Azure sinapsi Analytics, impostare la proprietà **Type** nell'origine dell'attività di copia su **SqlDWSource**. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà                     | Description                                                  | Obbligatorio |
| :--------------------------- | :----------------------------------------------------------- | :------- |
| type                         | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **SqlDWSource**. | Sì      |
| sqlReaderQuery               | Usare la query SQL personalizzata per leggere i dati. Esempio: `select * from MyTable`. | No       |
| sqlReaderStoredProcedureName | Nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. | No       |
| storedProcedureParameters    | Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No       |

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

### <a name="azure-sql-data-warehouse-as-sink"></a>Azure sinapsi Analytics come sink

Azure Data Factory supporta tre modi per caricare i dati in SQL Data Warehouse.

![Opzioni di copia del sink di SQL DW](./media/connector-azure-sql-data-warehouse/sql-dw-sink-copy-options.png)

- [USA polibase](#use-polybase-to-load-data-into-azure-sql-data-warehouse) 
- [USA istruzione COPY (anteprima)](#use-copy-statement)
- USA inserimento bulk

Il modo più rapido e scalabile per caricare i dati consiste nell'usare l'istruzione di [base](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) o di [copia](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (anteprima).

Per copiare dati in Azure SQL Data Warehouse, impostare il tipo di sink nell'attività di copia su **SqlDWSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà          | Description                                                  | Obbligatorio                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| type              | La proprietà **type** del sink dell'attività di copia deve essere impostata su **SqlDWSink**. | Sì                                           |
| allowPolyBase     | Indica se utilizzare la polibase per caricare i dati in SQL Data Warehouse. `allowCopyCommand` e `allowPolyBase` non possono essere entrambi true. <br/><br/>Per informazioni su vincoli e dettagli, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) .<br/><br/>I valori consentiti sono **True** e **False** (predefinito). | No.<br/>Applicare quando si usa la codebase.     |
| polyBaseSettings  | Gruppo di proprietà che è possibile specificare quando la proprietà `allowPolybase` è impostata su **true**. | No.<br/>Applicare quando si usa la codebase. |
| allowCopyCommand | Indica se utilizzare l' [istruzione Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (anteprima) per caricare i dati in SQL data warehouse. `allowCopyCommand` e `allowPolyBase` non possono essere entrambi true. <br/><br/>Vedere [usare l'istruzione Copy per caricare i dati nella sezione Azure SQL data warehouse](#use-copy-statement) per i vincoli e i dettagli.<br/><br/>I valori consentiti sono **True** e **False** (predefinito). | No.<br>Applicare quando si usa la copia. |
| copyCommandSettings | Gruppo di proprietà che è possibile specificare quando `allowCopyCommand` proprietà è impostata su TRUE. | No.<br/>Applicare quando si usa la copia. |
| writeBatchSize    | Numero di righe da inserire nella tabella SQL **per batch**.<br/><br/>Il valore consentito è **integer** (numero di righe). Per impostazione predefinita, Data Factory determina in modo dinamico le dimensioni del batch appropriate in base alle dimensioni della riga. | No.<br/>Applicare quando si usa BULK INSERT.     |
| writeBatchTimeout | Tempo di attesa per il completamento dell'operazione di inserimento batch prima del timeout.<br/><br/>Il valore consentito è **timespan**. Ad esempio: "00:30:00" (30 minuti). | No.<br/>Applicare quando si usa BULK INSERT.        |
| preCopyScript     | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati in Azure SQL Data Warehouse ad ogni esecuzione. Usare questa proprietà per pulire i dati precaricati. | No                                            |
| tableOption | Specifica se creare automaticamente la tabella di sink se non esiste in base allo schema di origine. La creazione automatica della tabella non è supportata quando la copia di gestione temporanea è configurata nell'attività di copia. I valori consentiti sono: `none` (impostazione predefinita), `autoCreate`. |No |
| disableMetricsCollection | Data Factory raccoglie le metriche, ad esempio SQL Data Warehouse DWU per l'ottimizzazione delle prestazioni di copia e consigli. Se si è interessati a questo comportamento, specificare `true` per disattivarlo. | No (il valore predefinito è `false`) |

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

L'uso di [polibase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) è un modo efficiente per caricare una grande quantità di dati in Azure sinapsi Analytics con una velocità effettiva elevata. L'uso di PolyBase consente un miglioramento significativo della velocità effettiva rispetto al meccanismo BULKINSERT predefinito. Per una procedura dettagliata con un caso d'uso, vedere [caricare 1 TB in Azure sinapsi Analytics](v1/data-factory-load-sql-data-warehouse.md).

* Se i dati di origine si trova in un **BLOB di Azure, Azure Data Lake storage Gen1 o Azure Data Lake storage Gen2**e il **formato è compatibile con la polibase**, è possibile usare l'attività di copia per richiamare direttamente la polibase per consentire Azure SQL data warehouse estrarre i dati dall'origine. Per maggiori dettagli, vedere **[Copia diretta tramite PolyBase](#direct-copy-by-using-polybase)** .
* Se l'archivio e il formato dei dati di origine non sono supportati in origine da PolyBase, usare la funzionalità **[copia di staging tramite PolyBase](#staged-copy-by-using-polybase)** . La funzionalità copia di staging assicura inoltre una migliore velocità effettiva, I dati vengono convertiti automaticamente in un formato compatibile con polibase, i dati vengono archiviati nell'archivio BLOB di Azure, quindi viene chiamato il metodo di base per caricare i dati in SQL Data Warehouse.

>[!TIP]
>Altre informazioni sulle [procedure consigliate per l'uso di polibase](#best-practices-for-using-polybase).

Le impostazioni di base seguenti sono supportate in `polyBaseSettings` nell'attività di copia:

| Proprietà          | Description                                                  | Obbligatorio                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| rejectValue       | Specifica il numero o la percentuale di righe che è possibile rifiutare prima che la query abbia esito negativo.<br/><br/>Per altre informazioni sulle opzioni di rifiuto di PolyBase, vedere la sezione Argomenti in [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>I valori consentiti sono 0 (predefinito), 1, 2 e così via. | No                                            |
| rejectType        | Indica se l'opzione **rejectValue** viene specificata come valore letterale o come percentuale.<br/><br/>I valori consentiti sono **Value** (predefinito) e **Percentage**. | No                                            |
| rejectSampleValue | Determina il numero di righe da recuperare prima che PolyBase ricalcoli la percentuale di righe rifiutate.<br/><br/>I valori consentiti sono 1, 2 e così via. | Sì se **rejectType** è **percentage**. |
| useTypeDefault    | Specifica come gestire valori mancanti nei file di testo delimitato quando PolyBase recupera i dati dal file di testo.<br/><br/>Per altre informazioni su questa proprietà, vedere la sezione Arguments (Argomenti) in [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>I valori consentiti sono **True** e **False** (predefinito).<br><br> | No                                            |

### <a name="direct-copy-by-using-polybase"></a>Copia diretta tramite PolyBase

SQL Data Warehouse polibase supporta direttamente BLOB, Azure Data Lake Storage Gen1 e Azure Data Lake Storage Gen2 di Azure. Se i dati di origine soddisfano i criteri descritti in questa sezione, usare la polibase per copiare direttamente dall'archivio dati di origine ad Azure sinapsi Analytics. In caso contrario, usare la [copia di staging tramite PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Per copiare i dati in modo efficiente per SQL Data Warehouse, è possibile ottenere altre informazioni da [Azure Data Factory rende ancora più semplice e pratico individuare informazioni approfondite dai dati quando si usa data Lake Store con SQL data warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se i requisiti non vengono soddisfatti, Azure Data Factory controlla le impostazioni e usa automaticamente il meccanismo BULKINSERT per lo spostamento dei dati.

1. Il **servizio collegato di origine** è con i tipi e i metodi di autenticazione seguenti:

    | Tipo di archivio dati di origine supportato                             | Tipo di autenticazione di origine supportato                        |
    | :----------------------------------------------------------- | :---------------------------------------------------------- |
    | [BLOB di Azure](connector-azure-blob-storage.md)                | Autenticazione con chiave dell'account, autenticazione di identità gestita |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Autenticazione di un'entità servizio                            |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Autenticazione con chiave dell'account, autenticazione di identità gestita |

    >[!IMPORTANT]
    >Se l'archiviazione di Azure è configurata con l'endpoint del servizio VNet, è necessario usare l'autenticazione di identità gestita. vedere l' [effetto dell'uso di endpoint del servizio VNet con archiviazione di Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Per informazioni sulle configurazioni necessarie, vedere la sezione Data Factory dall' [autenticazione di identità gestita da BLOB di Azure](connector-azure-blob-storage.md#managed-identity) e dall' [autenticazione Azure Data Lake storage Gen2 gestita](connector-azure-data-lake-storage.md#managed-identity) , rispettivamente.

2. Il **formato dei dati di origine** è di **parquet**, **ORC**o **testo delimitato**, con le configurazioni seguenti:

   1. Il percorso della cartella non contiene il filtro con caratteri jolly.
   2. Il nome file è vuoto o punta a un singolo file. Se si specifica il nome del file con caratteri jolly nell'attività di copia, è possibile `*` o `*.*`.
   3. `rowDelimiter` è **default**, **\n**, **\r\n** **o.**
   4. `nullValue` viene lasciato come predefinito o è impostato su una **stringa vuota** ("") e `treatEmptyAsNull` viene lasciato come predefinito o impostato su true.
   5. `encodingName` viene lasciato come predefinito o impostato su **UTF-8**.
   6. `quoteChar`, `escapeChar`e `skipLineCount` non sono specificati. La riga di intestazione Skip support, che può essere configurata come `firstRowAsHeader` in ADF.
   7. `compression` può essere **no compression**, **GZip** o **Deflate**.

3. Se l'origine è una cartella, `recursive` nell'attività di copia deve essere impostata su true.

4. `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`e `modifiedDateTimeEnd` non sono specificati.

>[!NOTE]
>Se l'origine è una cartella, Nota: polibase recupera i file dalla cartella e tutte le relative sottocartelle e non recupera i dati dai file per i quali il nome del file inizia con una sottolineatura (_) o un punto (.), come descritto [qui-argomento della posizione](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest#arguments-2).

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

Quando i dati di origine non sono compatibili in modo nativo con la polibase, abilitare la copia dei dati tramite un'istanza temporanea di archiviazione BLOB di Azure (non può essere archiviazione Premium di Azure). In questo caso, Azure Data Factory converte automaticamente i dati per soddisfare i requisiti di formato dei dati di polibase. Richiama quindi la polibase per caricare i dati in SQL Data Warehouse. Infine, pulisce i dati temporanei dall'archiviazione BLOB. Per informazioni dettagliate sulla copia dei dati tramite un'istanza di Archiviazione BLOB di Azure di staging, vedere [Copia di staging](copy-activity-performance.md#staged-copy).

Per usare questa funzionalità, creare un [servizio collegato di archiviazione BLOB di Azure](connector-azure-blob-storage.md#linked-service-properties) che fa riferimento all'account di archiviazione di Azure con l'archivio BLOB provvisorio. Specificare quindi le proprietà `enableStaging` e `stagingSettings` per l'attività di copia come illustrato nel codice seguente.

>[!IMPORTANT]
>Se l'archiviazione di Azure di staging è configurata con l'endpoint del servizio VNet, è necessario usare l'autenticazione di identità gestita. vedere l' [effetto dell'uso degli endpoint di servizio VNet con archiviazione di Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Informazioni sulle configurazioni richieste in Data Factory dall' [autenticazione di identità gestita da BLOB di Azure](connector-azure-blob-storage.md#managed-identity).

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

Le sezioni seguenti illustrano le procedure consigliate, oltre a quelle indicate in [procedure consigliate per l'analisi delle sinapsi di Azure](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

#### <a name="required-database-permission"></a>Autorizzazione database obbligatoria

Per usare PolyBase, l'utente che carica i dati in SQL Data Warehouse deve disporre dell'[autorizzazione "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) nel database di destinazione. Un modo per ottenere questo risultato consiste nell'aggiungere l'utente come membro del ruolo **db_owner**. Informazioni su come eseguire questa operazione sono disponibili in [Panoramica di SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

#### <a name="row-size-and-data-type-limits"></a>Limitazioni alle dimensioni delle righe e al tipo di dati

Le operazioni di caricamento di PolyBase sono limitate alle righe inferiori a 1 MB, Non può essere usato per il caricamento in VARCHR (MAX), NVARCHAR (MAX) o VARBINARY (MAX). Per altre informazioni, vedere [Limiti di capacità di SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Quando i dati di origine hanno righe di dimensioni superiori a 1 MB, è consigliabile suddividere verticalmente le tabelle di origine in tabelle più piccole. Assicurarsi che le dimensioni massime di ogni riga non superino il limite previsto. Le tabelle più piccole possono quindi essere caricate usando la polibase e unite in Azure sinapsi Analytics.

In alternativa, per i dati con colonne di questo tipo, è possibile usare l'opzione non di base per caricare i dati usando ADF, disattivando l'impostazione "Consenti polibase".

#### <a name="sql-data-warehouse-resource-class"></a>Classe di risorse di SQL Data Warehouse

Per ottenere la migliore velocità effettiva possibile, assegnare una classe di risorse più ampia all'utente che carica i dati in SQL Data Warehouse tramite PolyBase.

#### <a name="polybase-troubleshooting"></a>Risoluzione dei problemi di PolyBase

**Caricamento in una colonna decimale**

Se i dati di origine sono in formato testo o in altri archivi compatibili non di base (usando la copia di staging e la polibase) e contiene un valore vuoto da caricare in SQL Data Warehouse colonna decimale, è possibile che venga raggiunto l'errore seguente:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

La soluzione consiste nell'deselezionare l'opzione "**Usa il tipo predefinito**" (false) nelle impostazioni di base del sink dell'attività di copia->. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" è una configurazione nativa di base, che specifica come gestire i valori mancanti in file di testo delimitati quando la polibase recupera i dati dal file di testo. 

**`tableName` in Azure sinapsi Analytics**

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

Il valore NULL è una forma speciale di valore predefinito. Se la colonna ammette valori Null, i dati di input nel BLOB per tale colonna possono essere vuoti, ma non possono essere mancanti dal set di dati di input. La polibase inserisce NULL per i valori mancanti in Azure sinapsi Analytics.

## <a name="use-copy-statement"></a>Usare l'istruzione COPY per caricare i dati in Azure SQL Data Warehouse (anteprima)

SQL Data Warehouse [istruzione Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest) (anteprima) supporta direttamente il caricamento di dati dal **BLOB di Azure e Azure Data Lake storage Gen2**. Se i dati di origine soddisfano i criteri descritti in questa sezione, è possibile scegliere di usare l'istruzione COPY in ADF per caricare i dati in Azure SQL Data Warehouse. Azure Data Factory controlla le impostazioni e non riesce a eseguire l'attività di copia se i criteri non vengono soddisfatti.

>[!NOTE]
>Attualmente Data Factory supportata solo la copia dalle origini compatibili con le istruzioni COPY indicate di seguito.

L'utilizzo dell'istruzione COPY supporta la configurazione seguente:

1. Il **servizio collegato di origine e il formato** sono con i tipi e i metodi di autenticazione seguenti:

    | Tipo di archivio dati di origine supportato                             | Formato supportato           | Tipo di autenticazione di origine supportato                         |
    | :----------------------------------------------------------- | -------------------------- | :----------------------------------------------------------- |
    | [BLOB di Azure](connector-azure-blob-storage.md)                | [Testo delimitato](format-delimited-text.md)             | Autenticazione con chiave dell'account, autenticazione della firma di accesso condiviso, autenticazione dell'entità servizio, autenticazione dell'identità gestita |
    | &nbsp;                                                       | [Parquet](format-parquet.md)                    | Autenticazione della chiave dell'account, autenticazione della firma di accesso condiviso |
    | &nbsp;                                                       | [ORC](format-orc.md)                        | Autenticazione della chiave dell'account, autenticazione della firma di accesso condiviso |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | [Testo delimitato](format-delimited-text.md)<br/>[Parquet](format-parquet.md)<br/>[ORC](format-orc.md) | Autenticazione con chiave dell'account, autenticazione basata su entità servizio, autenticazione identità gestita |

    >[!IMPORTANT]
    >Se l'archiviazione di Azure è configurata con l'endpoint del servizio VNet, è necessario usare l'autenticazione di identità gestita. vedere l' [effetto dell'uso di endpoint del servizio VNet con archiviazione di Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Per informazioni sulle configurazioni necessarie, vedere la sezione Data Factory dall' [autenticazione di identità gestita da BLOB di Azure](connector-azure-blob-storage.md#managed-identity) e dall' [autenticazione Azure Data Lake storage Gen2 gestita](connector-azure-data-lake-storage.md#managed-identity) , rispettivamente.

2. Le impostazioni di formato sono le seguenti:

   1. Per **parquet**: `compression` non può essere di **compressione**, di **blocco**o **gzip**.
   2. Per **ORC**: `compression` non può essere **una compressione, una** **zlib**o un **blocco**.
   3. Per **testo delimitato**:
      1. `rowDelimiter` viene impostato in modo esplicito come **singolo carattere** o " **\r\n**", il valore predefinito non è supportato.
      2. `nullValue` viene lasciato come predefinito o è impostato su una **stringa vuota** ("").
      3. `encodingName` viene lasciato come predefinito o impostato su **UTF-8 o UTF-16**.
      4. `escapeChar` deve essere uguale `quoteChar`e non è vuoto.
      5. `skipLineCount` viene lasciato come predefinito o impostato su 0.
      6. `compression` non può essere di **compressione** o **gzip**.

3. Se l'origine è una cartella, `recursive` nell'attività di copia deve essere impostata su true.

4. `wildcardFolderPath`, `wildcardFilename`, `modifiedDateTimeStart`e `modifiedDateTimeEnd` non sono specificati.

Le impostazioni dell'istruzione COPY seguenti sono supportate in `allowCopyCommand` nell'attività di copia:

| Proprietà          | Description                                                  | Obbligatorio                                      |
| :---------------- | :----------------------------------------------------------- | :-------------------------------------------- |
| defaultValues | Specifica i valori predefiniti per ogni colonna di destinazione in SQL DW.  I valori predefiniti nella proprietà sovrascrivono il set di vincoli predefinito nel data warehouse e la colonna Identity non può avere un valore predefinito. | No |
| additionalOptions | Opzioni aggiuntive che verranno passate all'istruzione COPY di SQL DW direttamente nella clausola "with" nell' [istruzione Copy](https://docs.microsoft.com/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest). Racchiudere il valore necessario per allinearsi ai requisiti dell'istruzione COPY. | No |

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


## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).

## <a name="getmetadata-activity-properties"></a>Proprietà dell'attività GetMetadata

Per informazioni dettagliate sulle proprietà, controllare l' [attività GetMetadata](control-flow-get-metadata-activity.md) 

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapping dei tipi di dati per Azure SQL Data Warehouse

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Quando si trasformano i dati nel flusso di dati di mapping, è possibile leggere e scrivere nelle tabelle di Azure sinapsi Analytics. Per ulteriori informazioni, vedere trasformazione [origine](data-flow-source.md) e [trasformazione sink](data-flow-sink.md) in mapping di flussi di dati.

### <a name="source-transformation"></a>Trasformazione origine

Le impostazioni specifiche di Azure sinapsi Analytics sono disponibili nella scheda **Opzioni di origine** della trasformazione origine. 

**Input:** Consente di specificare se puntare l'origine a una tabella (equivalente a ```Select * from <table-name>```) oppure immettere una query SQL personalizzata.

**Query**: se si seleziona query nel campo di input, immettere una query SQL per l'origine. Questa impostazione esegue l'override di qualsiasi tabella scelta nel set di dati. Le clausole **Order by** non sono supportate in questo punto, ma è possibile impostare un'istruzione SELECT from completa. È anche possibile usare funzioni di tabella definite dall'utente. **Select * from udfGetData ()** è una funzione definita dall'utente in SQL che restituisce una tabella. Questa query produrrà una tabella di origine che è possibile utilizzare nel flusso di dati. L'utilizzo di query è anche un ottimo modo per ridurre le righe per il test o per le ricerche. 

* Esempio SQL: ```Select * from MyTable where customerId > 1000 and customerId < 2000```

**Dimensioni batch**: immettere le dimensioni del batch per suddividere i dati di grandi dimensioni in letture.

**Livello di isolamento**: il valore predefinito per le origini SQL nel flusso di dati di mapping è READ UNCOMMITTED. È possibile modificare il livello di isolamento in uno dei seguenti valori:
* Read Committed
* Read Uncommitted
* Repeatable Read
* Serializable
* Nessuno (ignora il livello di isolamento)

![Livello di isolamento](media/data-flow/isolationlevel.png "Livello di isolamento")

### <a name="sink-transformation"></a>Sink-trasformazione

Le impostazioni specifiche di Azure sinapsi Analytics sono disponibili nella scheda **Impostazioni** della trasformazione sink.

**Metodo di aggiornamento:** Determina quali operazioni sono consentite nella destinazione del database. Per impostazione predefinita, vengono consentiti solo gli inserimenti. Per aggiornare, Upsert o eliminare righe, è necessaria una trasformazione alter-Row per contrassegnare le righe per tali azioni. Per gli aggiornamenti, Upsert ed eliminazioni, è necessario impostare una colonna chiave o colonne per determinare la riga da modificare.

**Azione tabella:** Determina se ricreare o rimuovere tutte le righe dalla tabella di destinazione prima della scrittura.
* None: nessuna azione verrà eseguita nella tabella.
* Ricrea: la tabella viene eliminata e ricreata. Obbligatorio se si crea una nuova tabella in modo dinamico.
* TRUNCATE: tutte le righe della tabella di destinazione vengono rimosse.

**Abilita staging:** Determina se usare o meno la [polibase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide?view=sql-server-ver15) durante la scrittura in Azure sinapsi Analytics

**Dimensioni batch**: controlla il numero di righe scritte in ogni bucket. Dimensioni di batch più grandi migliorano la compressione e l'ottimizzazione della memoria, ma rischiano le eccezioni di memoria durante la memorizzazione nella cache dei dati.

**Script pre e post SQL**: immettere gli script SQL a più righe che verrà eseguito prima (pre-elaborazione) e dopo (post-elaborazione) i dati vengono scritti nel database sink

![script pre e post-elaborazione SQL](media/data-flow/prepost1.png "Script di elaborazione SQL")

## <a name="data-type-mapping-for-azure-synapse-analytics"></a>Mapping dei tipi di dati per Azure sinapsi Analytics

Quando si copiano dati da o ad Azure sinapsi Analytics, vengono usati i mapping seguenti dai tipi di dati di analisi delle sinapsi di Azure per Azure Data Factory tipi di dati provvisori. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

>[!TIP]
>Vedere l'articolo [tipi di dati della tabella nell'analisi di sinapsi di Azure](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) sui tipi di dati supportati da SQL DW e le soluzioni alternative per quelli non supportati.

| Tipo di dati di analisi delle sinapsi di Azure    | Tipo di dati provvisorio di Data Factory |
| :------------------------------------ | :----------------------------- |
| bigint                                | Int64                          |
| BINARY                                | Byte[]                         |
| bit                                   | Boolean                        |
| char                                  | String, Char[]                 |
| Data                                  | Data e ora                       |
| Datetime                              | Data e ora                       |
| datetime2                             | Data e ora                       |
| Datetimeoffset                        | DateTimeOffset                 |
| Decimal                               | Decimal                        |
| FILESTREAM attribute (varbinary(max)) | Byte[]                         |
| Float                                 | Double                         |
| image                                 | Byte[]                         |
| int                                   | Int32                          |
| money                                 | Decimal                        |
| NCHAR                                 | String, Char[]                 |
| NUMERIC                               | Decimal                        |
| NVARCHAR                              | String, Char[]                 |
| real                                  | Singolo                         |
| rowversion                            | Byte[]                         |
| smalldatetime                         | Data e ora                       |
| SMALLINT                              | Int16                          |
| SMALLMONEY                            | Decimal                        |
| time                                  | TimeSpan                       |
| TINYINT                               | Byte                           |
| UNIQUEIDENTIFIER                      | GUID                           |
| varbinary                             | Byte[]                         |
| varchar                               | String, Char[]                 |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati e formati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
