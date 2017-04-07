---
title: Spostare i dati da Salesforce usando Data Factory | Documentazione Microsoft
description: Informazioni su come spostare dati da Salesforce usando Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: dbe3bfd6-fa6a-491a-9638-3a9a10d396d1
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b4802009a8512cb4dcb49602545c7a31969e0a25
ms.openlocfilehash: 18a3a47cac6036923f3a72db70c9250252dcd361
ms.lasthandoff: 03/29/2017


---
# <a name="move-data-from-salesforce-by-using-azure-data-factory"></a>Spostare dati da Salesforce usando Azure Data Factory
Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Salesforce in qualsiasi archivio dati elencato nella colonna Sink della tabella relativa a [origini e sink supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats) . Questo articolo si basa sull'articolo [Spostamento di dati e attività di copia](data-factory-data-movement-activities.md) , che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivi dati supportati.

Attualmente Azure Data Factory supporta solo lo spostamento di dati da Salesforce verso gli [archivi dati sink supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats), ma non supporta lo spostamento di dati da altri archivi dati a Salesforce.

## <a name="supported-versions"></a>Versioni supportate
Questo connettore supporta le edizioni di Salesforce seguenti: Developer Edition, Professional Edition, Enterprise Edition o Unlimited Edition. Supporta anche la copia dall'ambiente di produzione, dalla sandbox e dal dominio personalizzato di Salesforce.

## <a name="prerequisites"></a>Prerequisiti
* È necessario abilitare l'autorizzazione API. Vedere [How do I enable API access in Salesforce by permission set?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)
* Per copiare dati da Salesforce in archivi dati locali, è necessario che nell'ambiente locale sia installato almeno Gateway di gestione dati 2.0.

## <a name="salesforce-request-limits"></a>Limiti delle richieste Salesforce
Salesforce presenta limiti per le richieste API totali e per le richieste API simultanee. Tenere presente quanto segue:

- Se il numero di richieste simultanee supera il limite, si verifica una limitazione e vengono visualizzati errori casuali.
- Se il numero totale di richieste supera il limite, l'account di Salesforce verrà bloccato per 24 ore.

In entrambi gli scenari è anche possibile che venga visualizzato l'errore "REQUEST_LIMIT_EXCEEDED" ("LIMITE_RICHIESTE_SUPERATO"). Per i dettagli, vedere la sezione API Request Limits (Limiti delle richieste API) nell'articolo [Salesforce API Request Limits](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Limiti delle richieste API di Salesforce).

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da Salesforce usando diversi strumenti/API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia. 

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink: 

1. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory.
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia. 
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output. 

Quando si usa la procedura guidata, le definizioni JSON per queste entità di Data Factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di Data Factory.  Per un esempio con le definizioni JSON per le entità di Data Factory usate per copiare dati da Salesforce, vedere la sezione [Esempio JSON: Copiare dati da Salesforce a BLOB di Azure](#json-example-copy-data-from-salesforce-to-azure-blob) di questo articolo. 

Nelle sezioni seguenti sono disponibili le informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità della Data Factory specifiche di Salesforce: 

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
La tabella seguente include le descrizioni degli elementi JSON specifici del servizio collegato Salesforce.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su **Salesforce**. |Sì |
| environmentUrl | Specificare l'URL dell'istanza di Salesforce. <br><br> - Il valore predefinito è "https://login.salesforce.com". <br> - Per copiare i dati dalla sandbox, specificare "https://test.salesforce.com". <br> - Per copiare i dati dal dominio personalizzato, specificare ad esempio "https://[dominio].my.salesforce.com". |No |
| username |Specificare un nome utente per l'account utente. |Sì |
| password |Specificare la password per l'account utente. |Sì |
| securityToken |Specificare un token di sicurezza per l'account utente. Per istruzioni su come ottenere o reimpostare un token di sicurezza, vedere [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Ottenere un token di sicurezza). Per informazioni generali sui token di sicurezza, vedere [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Sicurezza e API). |Sì |

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo [Set di dati in Azure Data Factory](data-factory-create-datasets.md) . Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per un set di dati di tipo **RelationalTable** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella in Salesforce. |No, se è specificata una **query** di **RelationalSource** |

> [!IMPORTANT]
> La parte "__c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.

![Data Factory - connessione Salesforce - nome API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo relativo alla [creazione di pipeline](data-factory-create-pipelines.md) . Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e diversi criteri.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Nell'attività di copia, quando l'origine è di tipo **RelationalSource** (che include Salesforce), nella sezione typeProperties sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Query SQL-92 o query [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Ad esempio: `select * from MyTable__c`. |No, se è specificato **tableName** per il **set di dati** |

> [!IMPORTANT]
> La parte "__c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.

![Data Factory - connessione Salesforce - nome API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)

## <a name="query-tips"></a>Suggerimenti di query
### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Recupero di dati tramite la clausola where nella colonna DateTime
Quando si specifica la query SQL o SOQL, prestare attenzione alla differenza di formato di DateTime. Ad esempio:

* **Esempio SOQL**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', WindowStart, WindowEnd)`
* **Esempio SQL**:
    * **Uso della procedura di copia guidata per specificare la query:** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\'{0:yyyy-MM-dd HH:mm:ss}\'}} AND LastModifiedDate < {{ts\'{1:yyyy-MM-dd HH:mm:ss}\'}}', WindowStart, WindowEnd)`
    * **Uso della modifica JSON per specificare la query (usare correttamente il carattere di escape):** `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', WindowStart, WindowEnd)`

### <a name="retrieving-data-from-salesforce-report"></a>Recupero di dati dal report di Salesforce
È possibile recuperare i dati dai report di Salesforce specificando una query come `{call "<report name>"}`, ad esempio. `"query": "{call \"TestReport\"}"`.

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Recupero dei record eliminati dal Cestino di Salesforce
Per eseguire una query sui record eliminati temporaneamente dal Cestino di Salesforce, è possibile specificare **"IsDeleted = 1"** nella query. Ad esempio,

* Per eseguire una query solo sui record eliminati, specificare MyTable__c **where IsDeleted= 1**"
* Per eseguire query su tutti i record inclusi quelli esistenti ed eliminati, specificare "select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**"

## <a name="json-example-copy-data-from-salesforce-to-azure-blob"></a>Esempio JSON: Copiare dati da Salesforce a BLOB di Azure
L'esempio seguente fornisce le definizioni JSON si esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Illustrano come copiare dati da Salesforce in un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.   

Ecco gli elementi di Data Factory che è necessario creare per implementare lo scenario. Le sezioni che seguono l'elenco forniscono informazioni dettagliate su questi passaggi.

* Un servizio collegato di tipo [Salesforce](#linked-service-properties)
* Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Un [set di dati](data-factory-create-datasets.md) di input di tipo [RelationalTable](#dataset-properties)
* Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [RelationalSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

**Servizio collegato Salesforce**

Questo esempio usa il servizio collegato **Salesforce** . Per informazioni sulle proprietà supportate da questo servizio collegato, vedere la sezione [Proprietà del servizio collegato Salesforce](#linked-service-properties) .  Per istruzioni su come ottenere o reimpostare un token di sicurezza, vedere [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Ottenere un token di sicurezza).

```json
{
    "name": "SalesforceLinkedService",
    "properties":
    {
        "type": "Salesforce",
        "typeProperties":
        {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```
**Servizio collegato Archiviazione di Azure**

```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
    "type": "AzureStorage",
    "typeProperties": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
    }
}
```
**Set di dati di input Salesforce**

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"  
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "externalData": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3
            }
        }
    }
}
```

Impostando **external** su **true** si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

> [!IMPORTANT]
> La parte "__c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.

![Data Factory - connessione Salesforce - nome API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name.png)

**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/alltypes_c"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Pipeline con attività di copia**

La pipeline contiene un'attività di copia configurata per l'uso dei set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline il tipo di **origine** è impostato su **RelationalSource** e il tipo di **sink** è impostato su **BlobSink**.

Per l'elenco delle proprietà supportate da RelationalSource, vedere [Proprietà del tipo RelationalSource](#copy-activity-properties) .

```json
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2016-06-01T18:00:00",
        "end":"2016-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
        {
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [
            {
                "name": "SalesforceInput"
            }
            ],
            "outputs": [
            {
                "name": "AzureBlobOutput"
            }
            ],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"                
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }
        ]
    }
}
```
> [!IMPORTANT]
> La parte "__c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.

![Data Factory - connessione Salesforce - nome API](media/data-factory-salesforce-connector/data-factory-salesforce-api-name-2.png)


### <a name="type-mapping-for-salesforce"></a>Mapping dei tipi per Salesforce
| Tipo Salesforce | Tipo basato su .NET |
| --- | --- |
| Numero automatico |String |
| Casella di controllo |Boolean |
| Valuta |Double |
| Data |DateTime |
| Data/ora |DateTime |
| Email |String |
| ID |String |
| Relazione di ricerca |String |
| Elenco a discesa seleziona multipla |String |
| Number |Double |
| Percentuale |Double |
| Telefono |String |
| Elenco a discesa |String |
| Text |String |
| Area di testo |String |
| Area di testo (Long) |String |
| Area di testo (Rich) |String |
| Testo (Crittografato) |String |
| URL |String |

> [!NOTE]
> Per eseguire il mapping dal set di dati di origine alle colonne del set di dati sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md) .

