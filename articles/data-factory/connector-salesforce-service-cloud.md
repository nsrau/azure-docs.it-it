---
title: Copiare dati da e in Salesforce Service Cloud
description: Informazioni su come copiare dati dal cloud del servizio Salesforce in archivi dati di sink supportati o da archivi dati di origine supportati a Salesforce Service Cloud usando un'attività di copia in una pipeline data factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: jingwang
ms.openlocfilehash: 3d49ec7260017421a60268e159c8152359455871
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895624"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Copiare dati da e in Salesforce Service Cloud usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Salesforce Service Cloud. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta informazioni generali sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Cloud del servizio Salesforce è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati dal cloud del servizio Salesforce in qualsiasi archivio dati di sink supportato. È anche possibile copiare dati da qualsiasi archivio dati di origine supportato a Salesforce Service Cloud. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Cloud del servizio salesforce supporta:

- Le edizioni Developer, Professional, Enterprise o Unlimited di Salesforce.
- La copia di dati da e nell'ambiente di produzione, nella sandbox e nel dominio personalizzato di Salesforce.

Il connettore Cloud del servizio Salesforce è basato sull'API REST/bulk di Salesforce, con [V45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) per la copia dei dati da e [V40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) per la copia dei dati in.

## <a name="prerequisites"></a>Prerequisiti

In Salesforce deve essere abilitata l'autorizzazione API. Per altre informazioni, vedere [Enable API access in Salesforce by permission set](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (Abilitare l'accesso al'API in Salesforce in base al set di autorizzazioni).

## <a name="salesforce-request-limits"></a>Limiti delle richieste Salesforce

Salesforce presenta limiti per le richieste API totali e per le richieste API simultanee. Tenere presente quanto segue:

- Se il numero di richieste simultanee supera il limite, si verifica una limitazione e vengono visualizzati errori casuali.
- Se il numero totale di richieste supera il limite, l'account di Salesforce viene bloccato per 24 ore.

In entrambi gli scenari è anche possibile che venga visualizzato il messaggio di errore "REQUEST_LIMIT_EXCEEDED" ("LIMITE_RICHIESTE_SUPERATO"). Per altre informazioni, vedere la sezione "API Request Limits" (Limiti delle richieste API) in [Salesforce developer limits](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Limiti per sviluppatori di Salesforce).

## <a name="get-started"></a>Inizia oggi stesso

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire Data Factory entità specifiche del connettore del servizio cloud di Salesforce.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Salesforce sono supportate le proprietà seguenti.

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type |La proprietà Type deve essere impostata su **SalesforceServiceCloud**. |SÌ |
| environmentUrl | Specificare l'URL dell'istanza di Salesforce Service Cloud. <br> - Il valore predefinito è `"https://login.salesforce.com"`. <br> - Per copiare dati dalla sandbox, specificare `"https://test.salesforce.com"`. <br> - Per copiare dati dal dominio personalizzato, specificare ad esempio `"https://[domain].my.salesforce.com"`. |No |
| Nome utente |Specificare un nome utente per l'account utente. |SÌ |
| password |Specificare la password per l'account utente.<br/><br/>Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |SÌ |
| securityToken |Specificare un token di sicurezza per l'account utente. Per istruzioni su come reimpostare o ottenere un token di sicurezza, vedere [Get a security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Ottenere un token di sicurezza). Per informazioni generali sui token di sicurezza, vedere [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Sicurezza e API).<br/><br/>Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |SÌ |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se non diversamente specificato, viene usato il runtime di integrazione di Azure predefinito. | No per l'origine, Sì per il sink se il servizio collegato all'origine non dispone di un runtime di integrazione |

>[!IMPORTANT]
>Quando si copiano dati in Salesforce Service Cloud, non è possibile usare il Azure Integration Runtime predefinito per eseguire la copia. In altre parole, se il servizio collegato all'origine non dispone di un runtime di integrazione specifico, creare in modo esplicito [un Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) con una posizione vicina all'istanza del cloud del servizio salesforce. Associare il servizio collegato del cloud del servizio Salesforce come nell'esempio seguente.

**Esempio: Archiviare le credenziali in Data Factory**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            },
            "securityToken": {
                "type": "SecureString",
                "value": "<security token>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio: Archiviare le credenziali in Key Vault**

```json
{
    "name": "SalesforceServiceCloudLinkedService",
    "properties": {
        "type": "SalesforceServiceCloud",
        "typeProperties": {
            "username": "<username>",
            "password": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of password in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
            },
            "securityToken": {
                "type": "AzureKeyVaultSecret",
                "secretName": "<secret name of security token in AKV>",
                "store":{
                    "referenceName": "<Azure Key Vault linked service>",
                    "type": "LinkedServiceReference"
                }
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati del servizio Salesforce Service Cloud.

Per copiare dati da e in Salesforce Service Cloud, sono supportate le proprietà seguenti.

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type deve essere impostata su **SalesforceServiceCloudObject**.  | SÌ |
| objectApiName | Nome dell'oggetto di Salesforce da cui recuperare i dati. | No per l'origine, Sì per il sink |

> [!IMPORTANT]
> La parte "__c" del**nome dell'API** è necessaria per qualsiasi oggetto personalizzato.

![Nome API della connessione Salesforce di Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Esempio:**

```json
{
    "name": "SalesforceServiceCloudDataset",
    "properties": {
        "type": "SalesforceServiceCloudObject",
        "typeProperties": {
            "objectApiName": "MyTable__c"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Salesforce Service Cloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **RelationalTable**. | SÌ |
| tableName | Nome della tabella in Salesforce Service Cloud. | No (se nell'origine dell'attività è specificato "query") |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink del servizio salesforce.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Cloud del servizio Salesforce come tipo di origine

Per copiare dati da Salesforce Service Cloud, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type dell'origine dell'attività di copia deve essere impostata su **SalesforceServiceCloudSource**. | SÌ |
| query |Usare la query personalizzata per leggere i dati. È possibile usare una query SQL-92 o una query [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Vedere altri suggerimenti nella sezione [Suggerimenti di query](#query-tips). Se la query non è specificata, verranno recuperati tutti i dati dell'oggetto Cloud del servizio salesforce specificato in "objectApiName" nel set di dati. | No (se "objectApiName" è specificato nel set di dati) |
| readBehavior | Indica se eseguire query sui record esistenti o su tutti i record inclusi quelli eliminati. Se non specificato, il comportamento predefinito è quello indicato per primo. <br>Valori consentiti: **query** (predefinito), **queryAll**.  | No |

> [!IMPORTANT]
> La parte "__c" del**nome dell'API** è necessaria per qualsiasi oggetto personalizzato.

![Elenco dei nomi API della connessione Salesforce di Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce Service Cloud input dataset name>",
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
                "type": "SalesforceServiceCloudSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Cloud del servizio Salesforce come tipo di sink

Per copiare dati in Salesforce Service Cloud, nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del sink dell'attività di copia deve essere impostata su **SalesforceServiceCloudSink**. | SÌ |
| writeBehavior | Comportamento dell'azione di scrittura per l'operazione.<br/>I valori consentiti sono: **Insert** e **Upsert**. | No (il valore predefinito è Insert) |
| externalIdFieldName | Nome del campo ID esterno per l'operazione upsert. Il campo specificato deve essere definito come "campo ID esterno" nell'oggetto Cloud del servizio salesforce. Non può includere valori NULL nei dati di input corrispondenti. | Sì per "Upsert" |
| writeBatchSize | Conteggio delle righe dei dati scritti in Salesforce Service Cloud in ogni batch. | No (il valore predefinito è 5.000) |
| ignoreNullValues | Indica se ignorare i valori NULL dai dati di input durante un'operazione di scrittura.<br/>I valori consentiti sono **true** e **false**.<br>- **True**: i dati nell'oggetto di destinazione rimangono invariati quando si esegue un'operazione di upsert o aggiornamento. Inserire un valore predefinito definito quando si esegue un'operazione di inserimento.<br/>- **False**: i dati nell'oggetto di destinazione vengono aggiornati a NULL quando si esegue un'operazione di upsert o aggiornamento. Inserire un valore NULL quando si esegue un'operazione di inserimento. | No (il valore predefinito è false) |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyToSalesforceServiceCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce Service Cloud output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceServiceCloudSink",
                "writeBehavior": "Upsert",
                "externalIdFieldName": "CustomerId__c",
                "writeBatchSize": 10000,
                "ignoreNullValues": true
            }
        }
    }
]
```

## <a name="query-tips"></a>Suggerimenti di query

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Recuperare dati da un report cloud di servizio salesforce

È possibile recuperare dati dai report cloud del servizio salesforce specificando una query come `{call "<report name>"}`. Un esempio è `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Recuperare i record eliminati dal cestino del servizio salesforce

Per eseguire una query sui record eliminati temporaneamente dal cestino del servizio Salesforce, è possibile specificare `readBehavior` come `queryAll`. 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Differenza tra la sintassi di query SOQL e SQL

Quando si copiano dati da Salesforce Service Cloud, è possibile usare query SOQL o query SQL. Si noti che queste due query hanno una sintassi e un supporto di funzionalità diversi. Non mischiarli. Si consiglia di usare la query SOQL, supportata in modo nativo da Salesforce Service Cloud. Nella tabella seguente sono elencate le differenze principali:

| Sintassi | Modalità SOQL | Modalità SQL |
|:--- |:--- |:--- |
| Selezione di colonne | È necessario enumerare i campi da copiare nella query, ad esempio `SELECT field1, filed2 FROM objectname` | `SELECT *` è supportata oltre alla selezione della colonna. |
| Virgolette | I nomi di campo/oggetto non possono essere racchiusi tra virgolette. | I nomi di campo/oggetto non possono essere racchiusi tra virgolette, ad es. `SELECT "id" FROM "Account"` |
| Formato Datetime |  Fare riferimento a informazioni dettagliate [qui](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) ed esempi nella sezione successiva. | Fare riferimento a informazioni dettagliate [qui](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) ed esempi nella sezione successiva. |
| Valori booleani | Rappresentati come `False` e `True`, ad esempio `SELECT … WHERE IsDeleted=True`. | Rappresentati come 0 o 1, ad esempio `SELECT … WHERE IsDeleted=1`. |
| Ridenominazione delle colonne | Non supportati. | Supportata, ad es. `SELECT a AS b FROM …`. |
| Relazione | Supportata, ad es. `Account_vod__r.nvs_Country__c`. | Non supportati. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Recuperare i dati usando una clausola where nella colonna DateTime

Quando si specifica la query SOQL o SQL, prestare attenzione alla differenza di formato di DateTime. ad esempio:

* **Esempio SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Esempio SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Errore di MALFORMED_QUERY: troncato

Se si è verificato un errore di "MALFORMED_QUERY: troncato", in genere è dovuto a una colonna di tipo JunctionIdList nei dati e a Salesforce è possibile limitare il supporto di tali dati con un numero elevato di righe. Per attenuare, provare a escludere la colonna JunctionIdList o a limitare il numero di righe da copiare (è possibile partizionare in più esecuzioni di attività di copia).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Mapping dei tipi di dati per Salesforce Service Cloud

Quando si copiano dati da Salesforce Service Cloud, vengono usati i mapping seguenti dai tipi di dati del servizio Salesforce per Data Factory tipi di dati provvisori. Per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

| Tipo di dati cloud del servizio salesforce | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| Numero automatico |Stringa |
| Casella di controllo |boolean |
| Valuta |DECIMAL |
| Data |Data e ora |
| Data/ora |Data e ora |
| Indirizzo di posta elettronica |Stringa |
| ID |Stringa |
| Relazione di ricerca |Stringa |
| Elenco a discesa seleziona multipla |Stringa |
| Numero |DECIMAL |
| Percentuale |DECIMAL |
| Telefono |Stringa |
| Elenco a discesa |Stringa |
| Testo |Stringa |
| Area di testo |Stringa |
| Area di testo (Long) |Stringa |
| Area di testo (Rich) |Stringa |
| Testo (Crittografato) |Stringa |
| URL |Stringa |

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
