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
ms.devlang: na
ms.topic: conceptual
ms.date: 07/28/2018
ms.author: jingwang
ms.openlocfilehash: ef1bd613943543f78d358064f4abefc6fa31b63e
ms.sourcegitcommit: 3d0295a939c07bf9f0b38ebd37ac8461af8d461f
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/06/2018
ms.locfileid: "43842336"
---
#  <a name="copy-data-to-or-from-azure-sql-data-warehouse-by-using-azure-data-factory"></a>Copiare dati da o in Azure SQL Data Warehouse usando Azure Data Factory 
> [!div class="op_single_selector" title1="Select the version of Data Factory service you're using:"]
> * [Versione 1](v1/data-factory-azure-sql-data-warehouse-connector.md)
> * [Versione corrente](connector-azure-sql-data-warehouse.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati in o da Azure SQL Data Warehouse. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta informazioni generali sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Azure SQL Data Warehouse in qualsiasi archivio dati di sink supportato. È anche possibile copiare dati da qualsiasi archivio dati di origine supportato in Azure SQL Data Warehouse. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Azure SQL Data Warehouse supporta queste funzioni:

- La copia di dati tramite l'autenticazione SQL e l'autenticazione token dell'applicazione Azure Active Directory (Azure AD) con entità servizio o identità del servizio gestita.
- Come origine, il recupero di dati tramite query SQL o stored procedure.
- Come sink, il caricamento di dati tramite PolyBase o un inserimento bulk. Per ottimizzare le prestazioni di copia, è consigliabile usare PolyBase.

> [!IMPORTANT]
> Si noti che PolyBase supporta solo l'autenticazione SQL, non l'autenticazione Azure AD.

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

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **AzureSqlDW**. | Yes |
| connectionString | Specificare le informazioni necessarie per connettersi all'istanza di Azure SQL Data Warehouse per la proprietà **connectionString**. Contrassegnare questo campo come **SecureString** per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| servicePrincipalKey | Specificare la chiave dell'applicazione. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì, quando si usa l'autenticazione Azure AD con un'entità servizio. |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No  |

Per altri tipi di autenticazione, fare riferimento alle sezioni seguenti relative, rispettivamente, ai prerequisiti e agli esempi JSON:

- [Autenticazione SQL](#sql-authentication)
- Autenticazione token dell'applicazione Azure AD: [entità servizio](#service-principal-authentication)
- Autenticazione token dell'applicazione Azure AD: [identità del servizio gestita](#managed-service-identity-authentication)

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

### <a name="service-principal-authentication"></a>Autenticazione di un'entità servizio

Per usare l'autenticazione token dell'applicazione Azure AD basata sull'entità servizio, seguire questa procedura:

1. **[Creare un'applicazione Azure Active Directory](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application)** nel portale di Azure. Prendere nota del nome dell'applicazione e dei valori seguenti che definiscono il servizio collegato:

    - ID applicazione
    - Chiave applicazione
    - ID tenant

1. **[Effettuare il provisioning di un amministratore di Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** per il server SQL di Azure nel portale di Azure, se l'operazione non è già stata eseguita. L'amministratore di Azure AD può essere un utente o un gruppo di Azure AD. Se si concede il ruolo di amministratore al gruppo con l'identità del servizio gestita, ignorare i passaggi 3 e 4. L'amministratore avrà accesso completo al database.

1. **[Creare utenti del database indipendente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** per l'entità servizio. Connettersi al data warehouse da o in cui si vogliono copiare i dati usando strumenti come SSMS, con un'identità di Azure AD che abbia almeno l'autorizzazione ALTER ANY USER. Eseguire il comando in T-SQL seguente:
    
    ```sql
    CREATE USER [your application name] FROM EXTERNAL PROVIDER;
    ```

1. **Concedere all'entità servizio le autorizzazioni necessarie**, come si fa di norma per gli utenti SQL o altri utenti. Eseguire il codice seguente:

    ```sql
    EXEC sp_addrolemember [role name], [your application name];
    ```

1. **Configurare un servizio collegato ad Azure SQL Data Warehouse** in Azure Data Factory.


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

### <a name="managed-service-identity-authentication"></a>Autenticazione basata su identità del servizio gestita

Una data factory può essere associata a un'[identità del servizio gestita](data-factory-service-identity.md), che rappresenta la factory specifica. È possibile usare questa identità del servizio per l'autenticazione di Azure SQL Data Warehouse. La factory designata può accedere ai dati e copiarli da o nel data warehouse tramite questa identità.

> [!IMPORTANT]
> Si noti che PolyBase non è attualmente supportato per l'autenticazione tramite l'identità del servizio gestita.

Per usare l'autenticazione token dell'applicazione Azure AD basata sull'identità del servizio gestita, seguire questa procedura:

1. **Creare un gruppo in Azure AD.** Aggiungere l'identità del servizio gestita della factory come membro del gruppo.

    1. Trovare l'identità del servizio della data factory nel portale di Azure. Accedere alle **proprietà** della data factory. Copiare l'ID IDENTITÀ DEL SERVIZIO.

    1. Installare il [modulo Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2). Accedere usando il comando `Connect-AzureAD`. Eseguire i comandi seguenti per creare un gruppo e aggiungere come membro l'identità del servizio gestita della data factory.
    ```powershell
    $Group = New-AzureADGroup -DisplayName "<your group name>" -MailEnabled $false -SecurityEnabled $true -MailNickName "NotSet"
    Add-AzureAdGroupMember -ObjectId $Group.ObjectId -RefObjectId "<your data factory service identity ID>"
    ```

1. **[Effettuare il provisioning di un amministratore di Azure Active Directory](../sql-database/sql-database-aad-authentication-configure.md#provision-an-azure-active-directory-administrator-for-your-azure-sql-database-server)** per il server SQL di Azure nel portale di Azure, se l'operazione non è già stata eseguita.

1. **[Creare utenti del database indipendente](../sql-database/sql-database-aad-authentication-configure.md#create-contained-database-users-in-your-database-mapped-to-azure-ad-identities)** per il gruppo di Azure AD. Connettersi al data warehouse da o in cui si vogliono copiare i dati usando strumenti come SSMS, con un'identità di Azure AD che abbia almeno l'autorizzazione ALTER ANY USER. Eseguire il comando in T-SQL seguente. 
    
    ```sql
    CREATE USER [your Azure AD group name] FROM EXTERNAL PROVIDER;
    ```

1. **Concedere al gruppo di Azure AD le autorizzazioni necessarie**, come si fa di norma per gli utenti SQL e altri utenti. Ad esempio, eseguire il codice seguente.

    ```sql
    EXEC sp_addrolemember [role name], [your Azure AD group name];
    ```

1. **Configurare un servizio collegato ad Azure SQL Data Warehouse** in Azure Data Factory.

#### <a name="linked-service-example-that-uses-msi-authentication"></a>Esempio di servizio collegato tramite l'autenticazione dell'identità del servizio gestita

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

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](https://docs.microsoft.com/en-us/azure/data-factory/concepts-datasets-linked-services). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Azure SQL Data Warehouse.

Per copiare dati da o in Azure SQL Data Warehouse, impostare la proprietà **type** del set di dati su **AzureSqlDWTable**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del set di dati deve essere impostata su **AzureSqlDWTable**. | Yes |
| tableName | Nome della tabella o vista nell'istanza di Azure SQL Data Warehouse a cui fa riferimento il servizio collegato. | Yes |

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

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **SqlDWSource**. | Yes |
| SqlReaderQuery | Usare la query SQL personalizzata per leggere i dati. Esempio: `select * from MyTable`. | No  |
| sqlReaderStoredProcedureName | Nome della stored procedure che legge i dati dalla tabella di origine. L'ultima istruzione SQL deve essere un'istruzione SELECT nella stored procedure. | No  |
| storedProcedureParameters | Parametri per la stored procedure.<br/>I valori consentiti sono coppie nome-valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. | No  |

### <a name="points-to-note"></a>Punti da notare

- Se per **SqlSource** è specificata la proprietà **sqlReaderQuery**, l'attività di copia esegue questa query nell'origine Azure SQL Data Warehouse per ottenere i dati. In alternativa, è possibile specificare una stored procedure. Indicare i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se accettati dalla stored procedure).
- Se non si specifica né **sqlReaderQuery** né **sqlReaderStoredProcedureName**, le colonne definite nella sezione della **struttura** del set di dati JSON vengono usate per creare una query. `select column1, column2 from mytable` viene eseguito in Azure SQL Data Warehouse. Se la definizione del set di dati non include la **struttura**, vengono selezionate tutte le colonne della tabella.
- Quando si usa **sqlReaderStoredProcedureName** è necessario specificare un valore fittizio per la proprietà **tableName** nel set di dati JSON.

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

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del sink dell'attività di copia deve essere impostata su **SqlDWSink**. | Yes |
| allowPolyBase | Indica se usare PolyBase, quando applicabile, invece del meccanismo BULKINSERT. <br/><br/> È consigliabile caricare dati in SQL Data Warehouse tramite PolyBase. Per informazioni su vincoli e dettagli, vedere la sezione [Usare PolyBase per caricare dati in Azure SQL Data Warehouse](#use-polybase-to-load-data-into-azure-sql-data-warehouse).<br/><br/>I valori consentiti sono **True** e **False** (predefinito).  | No  |
| polyBaseSettings | Gruppo di proprietà che è possibile specificare quando la proprietà **allowPolybase** è impostata su **true**. | No  |
| rejectValue | Specifica il numero o la percentuale di righe che è possibile rifiutare prima che la query abbia esito negativo.<br/><br/>Per altre informazioni sulle opzioni di rifiuto di PolyBase, vedere la sezione Argomenti in [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx). <br/><br/>I valori consentiti sono 0 (predefinito), 1, 2 e così via. |No  |
| rejectType | Indica se l'opzione **rejectValue** viene specificata come valore letterale o come percentuale.<br/><br/>I valori consentiti sono **Value** (predefinito) e **Percentage**. | No  |
| rejectSampleValue | Determina il numero di righe da recuperare prima che PolyBase ricalcoli la percentuale di righe rifiutate.<br/><br/>I valori consentiti sono 1, 2 e così via. | Sì se **rejectType** è **percentage**. |
| useTypeDefault | Specifica come gestire i valori mancanti nei file con testo delimitato quando PolyBase recupera dati dal file di testo.<br/><br/>Per altre informazioni su questa proprietà, vedere la sezione Arguments (Argomenti) in [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx).<br/><br/>I valori consentiti sono **True** e **False** (predefinito). | No  |
| writeBatchSize | Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge **writeBatchSize**. Si applica solo se non viene usato PolyBase.<br/><br/>Il valore consentito è **integer** (numero di righe). | No. Il valore predefinito è 10000. |
| writeBatchTimeout | Tempo di attesa per il completamento dell'operazione di inserimento batch prima del timeout. Si applica solo se non viene usato PolyBase.<br/><br/>Il valore consentito è **timespan**. Ad esempio: "00:30:00" (30 minuti). | No  |
| preCopyScript | Specificare una query SQL per l'attività di copia da eseguire prima di scrivere i dati in Azure SQL Data Warehouse ad ogni esecuzione. Usare questa proprietà per pulire i dati precaricati. | No  | (#repeatability-during-copy). | Istruzione di query. | No  |

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

[PolyBase](https://docs.microsoft.com/sql/relational-databases/polybase/polybase-guide) consente di caricare in modo efficiente grandi quantità di dati in Azure SQL Data Warehouse con una velocità effettiva elevata. L'uso di PolyBase consente un miglioramento significativo della velocità effettiva rispetto al meccanismo BULKINSERT predefinito. Vedere [Informazioni di riferimento sulle prestazioni](copy-activity-performance.md#performance-reference) per un confronto dettagliato. Per la procedura dettagliata con un caso d'uso, vedere [Caricare 1 TB di dati in Azure SQL Data Warehouse](https://docs.microsoft.com/en-us/azure/data-factory/v1/data-factory-load-sql-data-warehouse).

* Se il formato dei dati di origine è Archiviazione BLOB di Azure o Azure Data Lake Store ed è compatibile con PolyBase, copiare direttamente in Azure SQL Data Warehouse usando PolyBase. Per maggiori dettagli, vedere **[Copia diretta tramite PolyBase](#direct-copy-by-using-polybase)**.
* Se l'archivio e il formato dei dati di origine non sono supportati in origine da PolyBase, usare la funzionalità **[copia di staging tramite PolyBase](#staged-copy-by-using-polybase)**. La funzionalità copia di staging assicura inoltre una migliore velocità effettiva, converte automaticamente i dati nel formato compatibile con PolyBase e li archivia in Archiviazione BLOB di Azure. Vengono quindi caricati i dati in SQL Data Warehouse.

> [!IMPORTANT]
> Si noti che PolyBase non è attualmente supportato per l'autenticazione token dell'applicazione Azure AD basata sull'identità del servizio gestita.

### <a name="direct-copy-by-using-polybase"></a>Copia diretta tramite PolyBase

PolyBase di SQL Data Warehouse supporta direttamente Archiviazione BLOB di Azure e Azure Data Lake Store. Usa un'entità servizio come origine e ha requisiti di formato di file specifici. Se i dati di origine soddisfano i criteri descritti in questa sezione, usare PolyBase per copiare direttamente dall'archivio dati di origine ad Azure SQL Data Warehouse. In caso contrario, usare la [copia di staging tramite PolyBase](#staged-copy-by-using-polybase).

> [!TIP]
> Nell'articolo [Azure Data Factory makes it even easier and convenient to uncover insights from data when using Data Lake Store with SQL Data Warehouse](https://blogs.msdn.microsoft.com/azuredatalake/2017/04/08/azure-data-factory-makes-it-even-easier-and-convenient-to-uncover-insights-from-data-when-using-data-lake-store-with-sql-data-warehouse/) (Con Azure Data Factory è ancora più semplice e pratico individuare informazioni utili sui dati quando si usa Data Lake Store con SQL Data Warehouse) sono indicate altre informazioni utili per copiare i dati in modo efficace da Data Lake Store a SQL Data Warehouse.

Se i requisiti non vengono soddisfatti, Azure Data Factory controlla le impostazioni e usa automaticamente il meccanismo BULKINSERT per lo spostamento dei dati.

1. Il **servizio collegato di origine** è di tipo archiviazione BLOB di Azure (**AzureBLobStorage**/**AzureStorage**) con autenticazione mediante chiave dell'account o Azure Data Lake Storage Gen1 (**AzureDataLakeStore**) con autenticazione mediante entità servizio.
2. Il tipo di **set di dati di input** è **AzureBlob** o **AzureDataLakeStoreFile**. Il tipo di formato nelle proprietà `type` è **OrcFormat**, **ParquetFormat** o **TextFormat**, con le configurazioni seguenti:

   1. `fileName` non contiene il filtro con caratteri jolly.
   2. `rowDelimiter` deve essere **\n**.
   3. `nullValue` è impostato su **una stringa vuota** ("") o come valore predefinito e `treatEmptyAsNull` non è impostato su false.
   4. `encodingName` è impostato su **utf-8**, ovvero il valore predefinito.
   5. `escapeChar`, `quoteChar` e `skipLineCount` non sono specificati. Il supporto di PolyBase ignora la riga di intestazione che può essere configurata come `firstRowAsHeader` nella data factory di Azure.
   6. `compression` può essere **no compression**, **GZip** o **Deflate**.

    ```json
    "typeProperties": {
       "folderPath": "<blobpath>",
       "format": {
           "type": "TextFormat",
           "columnDelimiter": "<any delimiter>",
           "rowDelimiter": "\n",
           "nullValue": "",
           "encodingName": "utf-8",
           "firstRowAsHeader": <any>
       },
       "compression": {
           "type": "GZip",
           "level": "Optimal"
       }
    },
    ```

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

Per usare questa funzionalità, creare un [servizio collegato Archiviazione di Azure](connector-azure-blob-storage.md#linked-service-properties) che faccia riferimento all'account di archiviazione di Azure con l'archivio BLOB provvisorio. Quindi specificare le proprietà `enableStaging` e `stagingSettings` per l'attività di copia come illustrato nel codice seguente:

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

Le operazioni di caricamento di PolyBase sono limitate alle righe inferiori a 1 MB, che non possono essere caricate in VARCHR(MAX), NVARCHAR(MAX), or VARBINARY(MAX). Per altre informazioni, vedere [Limiti di capacità di SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-service-capacity-limits.md#loads).

Quando i dati di origine hanno righe di dimensioni superiori a 1 MB, è consigliabile suddividere verticalmente le tabelle di origine in tabelle più piccole. Assicurarsi che le dimensioni massime di ogni riga non superino il limite previsto. Le tabelle più piccole possono essere quindi caricate usando PolyBase e unite in Azure SQL Data Warehouse.

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

## <a name="data-type-mapping-for-azure-sql-data-warehouse"></a>Mapping dei tipi di dati per Azure SQL Data Warehouse

Quando si copiano i dati da o in Azure SQL Data Warehouse, vengono usati i mapping seguenti tra i tipi di dati di Azure SQL Data Warehouse e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di Azure SQL Data Warehouse | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| bigint | Int64 |
| binary | Byte[] |
| bit | boolean |
| char | String, Char[] |
| date | Datetime |
| DateTime | Datetime |
| datetime2 | Datetime |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| FILESTREAM attribute (varbinary(max)) | Byte[] |
| Float | Double |
| image | Byte[] |
| int | Int32 |
| money | Decimal |
| nchar | String, Char[] |
| ntext | String, Char[] |
| numeric | Decimal |
| nvarchar | String, Char[] |
| real | Single |
| rowversion | Byte[] |
| smalldatetime | Datetime |
| smallint | Int16 |
| smallmoney | Decimal |
| sql_variant | Object * |
| text | String, Char[] |
| time | Intervallo di tempo |
| timestamp | Byte[] |
| tinyint | Byte |
| uniqueidentifier | Guid |
| varbinary | Byte[] |
| varchar | String, Char[] |
| xml | xml |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati e formati supportati](copy-activity-overview.md##supported-data-stores-and-formats).
