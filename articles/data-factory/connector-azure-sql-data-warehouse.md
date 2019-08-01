---
title: Copiare dati in e da Azure SQL Data Warehouse usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da archivi di origine supportati in Azure SQL Data Warehouse o da SQL Data Warehouse in archivi di sink supportati usando Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: jingwang
ms.openlocfilehash: 24ee419e5c6eb4b8c148c61c232d2ab7ab07c74b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67449584"
---
# <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiare dati da o in Azure SQL Data Warehouse usando Azure Data Factory 
> [!div class="op_single_selector" title1="Selezionare la versione del servizio Data Factory in uso:"]
> * [Version1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versione corrente](connector-azure-sql-data-warehouse.md)

Questo articolo illustra come copiare dati da e verso Azure SQL Data Warehouse. Per altre informazioni su Azure Data Factory, vedere l'[articolo introduttivo](introduction.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Blob di Azure è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [supportata matrice di origine/sink](copy-activity-overview.md) tabella
- [Mapping di flusso di dati](concepts-data-flow-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)
- [Attività GetMetadata](control-flow-get-metadata-activity.md)

In particolare, il connettore Azure SQL Data Warehouse supporta queste funzioni:

- La copia di dati tramite l'autenticazione SQL e l'autenticazione token dell'applicazione Azure Active Directory (Azure AD) con entità servizio o identità gestite per le risorse di Azure.
- Come origine, il recupero di dati tramite query SQL o stored procedure.
- Come sink, il caricamento di dati tramite PolyBase o un inserimento bulk. Per ottimizzare le prestazioni di copia, è consigliabile usare PolyBase.

> [!IMPORTANT]
> Se si copiano i dati tramite il runtime di integrazione di Azure Data Factory, configurare un [firewall del server SQL di Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) per consentire ai servizi di Azure di accedere al server.
> Se si copiano i dati tramite un runtime di integrazione self-hosted, configurare il firewall del server SQL di Azure per consentire l'intervallo IP appropriato. Questo intervallo include l'indirizzo IP del computer usato per connettersi al database SQL di Azure.

## <a name="get-started"></a>Attività iniziali

> [!TIP]
> Per ottenere prestazioni ottimali, usare PolyBase per caricare i dati in Azure SQL Data Warehouse. Vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse) per i dettagli. Per la procedura dettagliata con un caso d'uso, vedere [Caricare 1 TB di dati in Azure SQL Data Warehouse in meno di 15 minuti con Azure Data Factory](load-azure-sql-data-warehouse.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che definiscono entità di Data Factory specifiche per il connettore Azure SQL Data Warehouse.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Azure SQL Data Warehouse sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureSqlDW**. | Sì |
| connectionString | Specificare le informazioni necessarie per connettersi all'istanza di Azure SQL Data Warehouse per la proprietà **connectionString**. <br/>Contrassegnare questo campo come SecureString per archiviare la chiave in modo sicuro in Data Factory. È anche possibile inserire la password/chiave entità servizio in Azure Key Vault e, se si tratta dell'autenticazione SQL, estrarre la configurazione `password` dalla stringa di connessione. Vedere gli esempi JSON sotto la tabella e l'articolo [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md) per altri dettagli. | Sì |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No |

Per altri tipi di autenticazione, fare riferimento alle sezioni seguenti relative, rispettivamente, ai prerequisiti e agli esempi JSON:

- [Autenticazione SQL](#sql-authentication)
- Autenticazione del token dell'applicazione Azure AD: [Entità servizio](#service-principal-authentication)
- Autenticazione del token dell'applicazione Azure AD: [Identità gestite per le risorse di Azure](#managed-identity)

>[!TIP]
>Se viene restituito l'errore con codice "UserErrorFailedToConnectToSqlServer" e un messaggio quale "Il limite di sessioni per il database è XXX ed è stato raggiunto.", aggiungere `Pooling=false` alla stringa di connessione e riprovare.

### <a name="sql-authentication"></a>Autenticazione in SQL

#### <a name="linked-service-example-that-uses-sql-authentication"></a>Esempio di servizio collegato tramite l'autenticazione SQL

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
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
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            },
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

Per usare l'autenticazione token dell'applicazione Azure AD basata sull'entità servizio, seguire questa procedura:

1. **[Creare un'applicazione Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application)** nel portale di Azure. Prendere nota del nome dell'applicazione e dei valori seguenti che definiscono il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

2. **[Effettuare il provisioning di un amministratore di Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** per il server SQL di Azure nel portale di Azure, se l'operazione non è già stata eseguita. L'amministratore di Azure AD può essere un utente o un gruppo di Azure AD. Se si concede al gruppo con identità gestita un ruolo di amministratore, ignorare i passaggi 3 e 4. L'amministratore avrà accesso completo al database.

3. **[Creare utenti del database indipendente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** per l'entità servizio. Connettersi al data warehouse da o in cui si vogliono copiare i dati usando strumenti come SSMS, con un'identità di Azure AD che abbia almeno l'autorizzazione ALTER ANY USER. Eseguire il comando in T-SQL seguente:
  
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

4. **Concedere all'entità servizio le autorizzazioni necessarie**, come si fa di norma per gli utenti SQL o altri utenti. Eseguire il codice seguente, o fare riferimento alle opzioni ulteriori [qui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se si desidera usare PolyBase per caricare i dati, Scopri i [necessaria l'autorizzazione di database](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your application name];
    ```

5. **Configurare un servizio collegato ad Azure SQL Data Warehouse** in Azure Data Factory.


#### <a name="linked-service-example-that-uses-service-principal-authentication"></a>Esempio di servizio collegato tramite l'autenticazione basata su entità servizio

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            },
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

Una data factory può essere associata a un'[identità gestita per le risorse di Azure](data-factory-service-identity.md), che rappresenta la factory specifica. È possibile usare questa identità gestita per l'autenticazione di Azure SQL Data Warehouse. La factory designata può accedere ai dati e copiarli da o nel data warehouse tramite questa identità.

Per usare l'autenticazione identità gestita, seguire questa procedura:

1. **[Effettuare il provisioning di un amministratore di Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** per il server SQL di Azure nel portale di Azure, se l'operazione non è già stata eseguita. L'amministratore di Azure AD può essere un utente o un gruppo di Azure AD. Se si concede al gruppo con identità gestita un ruolo di amministratore, ignorare i passaggi 3 e 4. L'amministratore avrà accesso completo al database.

2. **[Creare utenti del database indipendente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)**  per l'identità di gestiti di Data Factory. Connettersi al data warehouse da o in cui si vogliono copiare i dati usando strumenti come SSMS, con un'identità di Azure AD che abbia almeno l'autorizzazione ALTER ANY USER. Eseguire il comando in T-SQL seguente. 
  
    ```sql
    CREATE USER [your Data Factory name] FROM EXTERNAL PROVIDER;
    ```

3. **Concedere le autorizzazioni necessarie di identità gestite di Data Factory** come si farebbe normalmente per gli utenti SQL e altri utenti. Eseguire il codice seguente, o fare riferimento alle opzioni ulteriori [qui](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?view=sql-server-2017). Se si desidera usare PolyBase per caricare i dati, Scopri i [necessaria l'autorizzazione di database](#required-database-permission).

    ```sql
    EXEC sp_addrolemember db_owner, [your Data Factory name];
    ```

5. **Configurare un servizio collegato ad Azure SQL Data Warehouse** in Azure Data Factory.

**Esempio:**

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;Connection Timeout=30"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Azure SQL Data Warehouse.

Per copiare dati da o verso Azure SQL Data Warehouse, sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del set di dati deve essere impostata su **AzureSqlDWTable**. | Sì |
| tableName | Nome della tabella o vista nell'istanza di Azure SQL Data Warehouse a cui fa riferimento il servizio collegato. | No per l'origine, Sì per il sink |

#### <a name="dataset-properties-example"></a>Esempio di proprietà dei set di dati

```json
{
    "name": "AzureSQLDWDataset",
    "properties":
    {
        "type": "AzureSqlDWTable",
        "linkedServiceName": {
            "referenceName": "<Azure SQL Data Warehouse linked service name>",
            "type": "LinkedServiceReference"
        },
        "schema": [ < physical schema, optional, retrievable during authoring > ],
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Azure SQL Data Warehouse.

### <a name="azure-sql-data-warehouse-as-the-source"></a>Azure SQL Data Warehouse come origine

Per copiare dati da Azure SQL Data Warehouse, impostare la proprietà **type** nell'origine dell'attività di copia su **SqlDWSource**. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **SqlDWSource**. | Sì |
| sqlReaderQuery | Usare la query SQL personalizzata per leggere i dati. Esempio: `select * from MyTable`. | No |
| sqlReaderStoredProcedureName | Nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. | No |
| storedProcedureParameters | Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No |

### <a name="points-to-note"></a>Punti da notare

- Se per **SqlSource** è specificata la proprietà **sqlReaderQuery**, l'attività di copia esegue questa query nell'origine Azure SQL Data Warehouse per ottenere i dati. In alternativa, è possibile specificare una stored procedure. Indicare i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se accettati dalla stored procedure).
- Se non si specifica né **sqlReaderQuery** né **sqlReaderStoredProcedureName**, le colonne definite nella sezione della **struttura** del set di dati JSON vengono usate per creare una query. `select column1, column2 from mytable` viene eseguito in Azure SQL Data Warehouse. Se la definizione del set di dati non include la **struttura**, vengono selezionate tutte le colonne della tabella.

#### <a name="sql-query-example"></a>Esempio di query SQL

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

#### <a name="stored-procedure-example"></a>Esempio di stored procedure

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

### <a name="azure-sql-data-warehouse-as-sink"></a> Azure SQL Data Warehouse come sink

Per copiare dati in Azure SQL Data Warehouse, impostare il tipo di sink nell'attività di copia su **SqlDWSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del sink dell'attività di copia deve essere impostata su **SqlDWSink**. | Sì |
| allowPolyBase | Indica se usare PolyBase, quando applicabile, invece del meccanismo BULKINSERT. <br/><br/> È consigliabile caricare dati in SQL Data Warehouse tramite PolyBase. Per informazioni su vincoli e dettagli, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse).<br/><br/>I valori consentiti sono **True** e **False** (predefinito).  | No |
| polyBaseSettings | Gruppo di proprietà che è possibile specificare quando la proprietà **allowPolybase** è impostata su **true**. | No |
| rejectValue | Specifica il numero o la percentuale di righe che è possibile rifiutare prima che la query abbia esito negativo.<br/><br/>Per altre informazioni sulle opzioni di rifiuto di PolyBase, vedere la sezione Argomenti in [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>I valori consentiti sono 0 (predefinito), 1, 2 e così via. |No |
| rejectType | Indica se l'opzione **rejectValue** viene specificata come valore letterale o come percentuale.<br/><br/>I valori consentiti sono **Value** (predefinito) e **Percentage**. | No |
| rejectSampleValue | Determina il numero di righe da recuperare prima che PolyBase ricalcoli la percentuale di righe rifiutate.<br/><br/>I valori consentiti sono 1, 2 e così via. | Sì se **rejectType** è **percentage**. |
| useTypeDefault | Specifica come gestire i valori mancanti nei file con testo delimitato quando PolyBase recupera dati dal file di testo.<br/><br/>Per altre informazioni su questa proprietà, vedere la sezione Arguments (Argomenti) in [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>I valori consentiti sono **True** e **False** (predefinito).<br><br>**Visualizzare [suggerimenti](#polybase-troubleshooting) correlate a questa impostazione.** | No |
| writeBatchSize | Numero di righe nella tabella SQL inserimenti **per ogni batch**. Si applica solo se non viene usato PolyBase.<br/><br/>Il valore consentito è **integer** (numero di righe). Per impostazione predefinita, Data Factory di determinare in modo dinamico le dimensioni del batch appropriato in base alla dimensione di riga. | No |
| writeBatchTimeout | Tempo di attesa per il completamento dell'operazione di inserimento batch prima del timeout. Si applica solo se non viene usato PolyBase.<br/><br/>Il valore consentito è **timespan**. Esempio: "00:30:00" (30 minuti). | No |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati in Azure SQL Data Warehouse ad ogni esecuzione. Usare questa proprietà per pulire i dati precaricati. | No |

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

Per altre informazioni su come usare PolyBase per caricare in modo efficiente in SQL Data Warehouse, vedere la sezione successiva.

## <a name="use-polybase-to-load-data-into-azure-sql-data-warehouse"></a>Usare PolyBase per caricare dati in Azure SQL Data Warehouse

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) consente di caricare in modo efficiente grandi quantità di dati in Azure SQL Data Warehouse con una velocità effettiva elevata. L'uso di PolyBase consente un miglioramento significativo della velocità effettiva rispetto al meccanismo BULKINSERT predefinito. Vedere [Informazioni di riferimento sulle prestazioni](copy-activity-performance.md#performance-reference) per un confronto dettagliato. Per la procedura dettagliata con un caso d'uso, vedere [Caricare 1 TB di dati in Azure SQL Data Warehouse](v1/data-factory-load-sql-data-warehouse.md).

* Se i dati di origine si trova in **Blob di Azure, Azure Data Lake archiviazione Gen1 o Azure Data Lake Storage Gen2**e il **formato è compatibile con PolyBase**, è possibile usare attività di copia per richiamare direttamente PolyBase per consentire ad Azure SQL Data Warehouse il pull dei dati dall'origine. Per maggiori dettagli, vedere **[Copia diretta tramite PolyBase](#direct-copy-by-using-polybase)** .
* Se l'archivio e il formato dei dati di origine non sono supportati in origine da PolyBase, usare la funzionalità **[copia di staging tramite PolyBase](#staged-copy-by-using-polybase)** . La funzionalità copia di staging assicura inoltre una migliore velocità effettiva, converte automaticamente i dati nel formato compatibile con PolyBase e li archivia in Archiviazione BLOB di Azure. Vengono quindi caricati i dati in SQL Data Warehouse.

>[!TIP]
>Altre informazioni, vedere [procedure consigliate per l'uso di PolyBase](#best-practices-for-using-polybase).

### <a name="direct-copy-by-using-polybase"></a>Copia diretta tramite PolyBase

PolyBase di SQL Data Warehouse supporta direttamente i Blob di Azure, Azure Data Lake archiviazione Gen1 e Gen2 di archiviazione di Azure Data Lake. Se i dati di origine soddisfino i criteri descritti in questa sezione, è possibile usare PolyBase per copiare direttamente dall'archivio dati di origine in Azure SQL Data Warehouse. In caso contrario, usare la [copia di staging tramite PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Per copiare dati in modo efficiente in SQL Data Warehouse, altre informazioni, vedere [Azure Data Factory rende ancora più facile e comodo ottenere informazioni approfondite dai dati quando si usa Data Lake Store con SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/).

Se i requisiti non vengono soddisfatti, Azure Data Factory controlla le impostazioni e usa automaticamente il meccanismo BULKINSERT per lo spostamento dei dati.

1. Il **servizio collegato di origine** è con i seguenti tipi e metodi di autenticazione:

    | Tipo di archivio dati di origine supportata | Tipo di autenticazione di origine supportato |
    |:--- |:--- |
    | [Azure Blob](connector-azure-blob-storage.md) | Autenticazione con chiave account, l'autenticazione identità gestita |
    | [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) | Autenticazione di un'entità servizio |
    | [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) | Autenticazione con chiave account, l'autenticazione identità gestita |

    >[!IMPORTANT]
    >Se l'archiviazione di Azure è configurata con l'endpoint del servizio rete virtuale, è necessario usare l'autenticazione identità gestita, vedere [impatto dell'uso di endpoint del servizio rete virtuale con archiviazione di Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Scopri le configurazioni necessarie in Data Factory da [Blob di Azure - autenticazione identità gestita](connector-azure-blob-storage.md#managed-identity) e [Gen2 di archiviazione di Azure Data Lake - autenticazione identità gestita](connector-azure-data-lake-storage.md#managed-identity) sezione rispettivamente.

2. Il **formato dei dati di origine** è di **Parquet**, **ORC**, oppure **testo delimitato**, con le configurazioni seguenti:

   1. Percorso della cartella non contengono filtro con caratteri jolly.
   2. Nome del file fa riferimento a un singolo file o è `*` o `*.*`.
   3. `rowDelimiter` deve essere **\n**.
   4. `nullValue` è impostato su una **stringa vuota** ("") o sul valore predefinito e `treatEmptyAsNull` è impostato sul valore predefinito o su true.
   5. `encodingName` è impostato su **utf-8**, ovvero il valore predefinito.
   6. `quoteChar`, `escapeChar`, e `skipLineCount` non sono specificati. Il supporto di PolyBase ignora la riga di intestazione che può essere configurata come `firstRowAsHeader` nella data factory di Azure.
   7. `compression` può essere **no compression**, **GZip** o **Deflate**.

```json
"activities":[
    {
        "name": "CopyFromAzureBlobToSQLDataWarehouseViaPolyBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "BlobDataset",
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
                "type": "BlobSource",
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

Quando i dati di origine non soddisfano i criteri della sezione precedente, abilitare la copia dei dati tramite un'istanza di Archiviazione BLOB di Azure di staging provvisoria. Non può essere Archiviazione Premium di Azure. In questo caso, Azure Data Factory esegue automaticamente trasformazioni sui dati in modo che soddisfino i requisiti di formato dei dati di PolyBase. Quindi usa PolyBase per caricare i dati in SQL Data Warehouse. Infine, pulisce i dati temporanei dall'archiviazione BLOB. Per informazioni dettagliate sulla copia dei dati tramite un'istanza di Archiviazione BLOB di Azure di staging, vedere [Copia di staging](copy-activity-performance.md#staged-copy).

Per usare questa funzionalità, creare un [servizio collegato di archiviazione Blob di Azure](connector-azure-blob-storage.md#linked-service-properties) che fa riferimento all'account di archiviazione di Azure con l'archivio blob provvisorio. Quindi specificare il `enableStaging` e `stagingSettings` le proprietà dell'attività di copia come illustrato nel codice seguente.

>[!IMPORTANT]
>Se l'archiviazione di Azure di staging è configurato con l'endpoint del servizio rete virtuale, è necessario usare l'autenticazione identità gestita, vedere [impatto dell'uso di endpoint del servizio rete virtuale con archiviazione di Azure](../sql-database/sql-database-vnet-service-endpoint-rule-overview.md#impact-of-using-vnet-service-endpoints-with-azure-storage). Scopri le configurazioni necessarie in Data Factory da [Blob di Azure - autenticazione identità gestita](connector-azure-blob-storage.md#managed-identity).

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

## <a name="best-practices-for-using-polybase"></a>Procedure consigliate per l'uso di PolyBase

Le sezioni seguenti forniscono procedure consigliate in aggiunta a quelle descritte in [Procedure consigliate per Azure SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-best-practices.md).

### <a name="required-database-permission"></a>Autorizzazione database obbligatoria

Per usare PolyBase, l'utente che carica i dati in SQL Data Warehouse deve disporre dell'[autorizzazione "CONTROL"](https://msdn.microsoft.com/library/ms191291.aspx) nel database di destinazione. Un modo per ottenere questo risultato consiste nell'aggiungere l'utente come membro del ruolo **db_owner**. Informazioni su come eseguire questa operazione sono disponibili in [Panoramica di SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-manage-security.md#authorization).

### <a name="row-size-and-data-type-limits"></a>Limitazioni alle dimensioni delle righe e al tipo di dati

Le operazioni di caricamento di PolyBase sono limitate alle righe inferiori a 1 MB, Non è utilizzabile per essere caricate in varchr, nvarchar (max) o varbinary (max). Per altre informazioni, vedere [Limiti di capacità di SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Quando i dati di origine hanno righe di dimensioni superiori a 1 MB, è consigliabile suddividere verticalmente le tabelle di origine in tabelle più piccole. Assicurarsi che le dimensioni massime di ogni riga non superino il limite previsto. Le tabelle più piccole possono essere quindi caricate usando PolyBase e unite in Azure SQL Data Warehouse.

In alternativa, per i dati con colonne di tipo wide, è possibile usare non PolyBase per caricare i dati usando Azure Data factory, disattivando l'opzione "allow PolyBase" impostazione.

### <a name="polybase-troubleshooting"></a>Risoluzione dei problemi di PolyBase

**Il caricamento a una colonna decimale**

Se i dati di origine sono testo in formato o altri compatibile con PolyBase non archivia (usando una copia di staging e PolyBase) e contiene un valore vuoto per essere caricata nella colonna Decimal di SQL Data Warehouse, è possibile riscontrare l'errore seguente:

```
ErrorCode=FailedDbOperation, ......HadoopSqlException: Error converting data type VARCHAR to DECIMAL.....Detailed Message=Empty string can't be converted to DECIMAL.....
```

La soluzione consiste nel deselezionare "**tipo di utilizzo predefinito**" opzione (false) nel sink dell'attività Copia -> Impostazioni di PolyBase. "[USE_TYPE_DEFAULT](https://docs.microsoft.com/sql/t-sql/statements/create-external-file-format-transact-sql?view=azure-sqldw-latest#arguments
)" è una configurazione di PolyBase nativa che specifica come gestire valori mancanti nei file di testo delimitato quando PolyBase recupera i dati dal file di testo. 

**Altro**

Per problemi di PolyBase più noti, vedere [risoluzione dei problemi di Azure SQL Data Warehouse PolyBase carico](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md#polybase).

### <a name="sql-data-warehouse-resource-class"></a>Classe di risorse di SQL Data Warehouse

Per ottenere la migliore velocità effettiva possibile, assegnare una classe di risorse più ampia all'utente che carica i dati in SQL Data Warehouse tramite PolyBase.

### <a name="tablename-in-azure-sql-data-warehouse"></a>**tableName** in Azure SQL Data Warehouse

La tabella seguente fornisce alcuni esempi di come specificare la proprietà **tableName** nel set di dati JSON, mostrando diverse combinazioni di nomi di schema e di tabella.

| Schema di database | Nome tabella | Proprietà JSON **tableName** |
| --- | --- | --- |
| dbo | MyTable | MyTable o dbo.MyTable o [dbo].[MyTable] |
| dbo1 | MyTable | dbo1.MyTable o [dbo1].[MyTable] |
| dbo | My.Table | [My.Table] o [dbo].[My.Table] |
| dbo1 | My.Table | [dbo1].[My.Table] |

Se viene visualizzato l'errore seguente, il problema potrebbe essere costituito dal valore specificato per la proprietà **tableName**. Per informazioni sul modo corretto di specificare i valori per la proprietà JSON **tableName**, vedere la tabella precedente.

```
Type=System.Data.SqlClient.SqlException,Message=Invalid object name 'stg.Account_test'.,Source=.Net SqlClient Data Provider
```

### <a name="columns-with-default-values"></a>Colonne con valori predefiniti

Attualmente, la funzionalità PolyBase in Data Factory accetta solo lo stesso numero di colonne disponibili nella tabella di destinazione. Nel caso di una tabella con quattro colonne di cui una definita con un valore predefinito, ad esempio, i dati di input devono comunque contenere quattro colonne. Un set di dati di input con tre colonne restituisce un errore simile al messaggio seguente:

```
All columns of the table must be specified in the INSERT BULK statement.
```

Il valore NULL è una forma speciale di valore predefinito. Se la colonna ammette valori Null, i dati di input nel BLOB per tale colonna possono essere vuoti, ma non possono essere mancanti dal set di dati di input. PolyBase inserisce NULL per i valori mancanti in Azure SQL Data Warehouse.

## <a name="mapping-data-flow-properties"></a>Mapping delle proprietà del flusso di dati

Informazioni dettagliate dal [trasformazione sorgente](data-flow-source.md) e [sink trasformazione](data-flow-sink.md) nel Mapping di flusso di dati.

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapping dei tipi di dati per Azure SQL Data Warehouse

Quando si copiano i dati da o in Azure SQL Data Warehouse, vengono usati i mapping seguenti tra i tipi di dati di Azure SQL Data Warehouse e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

>[!TIP]
>Fare riferimento a [tipi di dati in Azure SQL Data Warehouse le tabelle](../sql-data-warehouse/sql-data-warehouse-tables-data-types.md) articolo su SQL Data Warehouse supportati i tipi di dati e soluzioni alternative per quelli non supportati.

| Tipo di dati di Azure SQL Data Warehouse | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| bigint | Int64 |
| binary | Byte[] |
| bit | Boolean |
| char | String, Char[] |
| date | DateTime |
| Datetime | DateTime |
| datetime2 | DateTime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| FILESTREAM attribute (varbinary(max)) | Byte[] |
| Float | Double |
| image | Byte[] |
| int | Int32 |
| money | Decimal |
| nchar | String, Char[] |
| numeric | Decimal |
| nvarchar | String, Char[] |
| real | Single |
| rowversion | Byte[] |
| smalldatetime | DateTime |
| smallint | Int16 |
| smallmoney | Decimal |
| time | TimeSpan |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte[] |
| varchar | String, Char[] |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati e formati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
