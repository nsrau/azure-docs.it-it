---
title: Copiare dati da e in Salesforce Service Cloud
description: Informazioni su come copiare i dati da Salesforce Service Cloud agli archivi dati sink supportati o dagli archivi dati di origine supportati in Salesforce Service Cloud usando un'attività di copia in una pipeline di data factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/24/2020
ms.openlocfilehash: 4540b27a9241a14b3d1a153d11bf43900e8ae0ec
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153854"
---
# <a name="copy-data-from-and-to-salesforce-service-cloud-by-using-azure-data-factory"></a>Copiare dati da e in Salesforce Service Cloud usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Salesforce Service Cloud.This article outlines how to use Copy Activity in Azure Data Factory to copy data from and to Salesforce Service Cloud. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta informazioni generali sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore Salesforce Service Cloud è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività di ricerca](control-flow-lookup-activity.md)

È possibile copiare i dati da Salesforce Service Cloud in qualsiasi archivio dati sink supportato. È inoltre possibile copiare i dati da qualsiasi archivio dati di origine supportato in Salesforce Service Cloud. Per un elenco degli archivi dati supportati come origini o sink dall'attività Copia, vedere la tabella [Archivi dati supportati.](copy-activity-overview.md#supported-data-stores-and-formats)

In particolare, questo connettore Salesforce Service Cloud supporta:

- Le edizioni Developer, Professional, Enterprise o Unlimited di Salesforce.
- La copia di dati da e nell'ambiente di produzione, nella sandbox e nel dominio personalizzato di Salesforce.

Il connettore Salesforce si basa sull'API REST/Bulk di Salesforce. Per impostazione predefinita, il connettore utilizza [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) per copiare i dati da Salesforce e [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) per copiare i dati in Salesforce. È anche possibile impostare in modo esplicito [ `apiVersion` ](#linked-service-properties) la versione dell'API usata per leggere/scrivere i dati tramite la proprietà nel servizio collegato.

## <a name="prerequisites"></a>Prerequisiti

In Salesforce deve essere abilitata l'autorizzazione API. Per altre informazioni, vedere [Enable API access in Salesforce by permission set](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (Abilitare l'accesso al'API in Salesforce in base al set di autorizzazioni).

## <a name="salesforce-request-limits"></a>Limiti delle richieste Salesforce

Salesforce presenta limiti per le richieste API totali e per le richieste API simultanee. Tenere presente quanto segue:

- Se il numero di richieste simultanee supera il limite, si verifica una limitazione e vengono visualizzati errori casuali.
- Se il numero totale di richieste supera il limite, l'account di Salesforce viene bloccato per 24 ore.

In entrambi gli scenari è anche possibile che venga visualizzato il messaggio di errore "REQUEST_LIMIT_EXCEEDED" ("LIMITE_RICHIESTE_SUPERATO"). Per altre informazioni, vedere la sezione "API Request Limits" (Limiti delle richieste API) in [Salesforce developer limits](https://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Limiti per sviluppatori di Salesforce).

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Nelle sezioni seguenti vengono fornite informazioni dettagliate sulle proprietà utilizzate per definire le entità di Data Factory specifiche del connettore Salesforce Service Cloud.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Salesforce sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su **SalesforceServiceCloud**. |Sì |
| environmentUrl | Specificare l'URL dell'istanza di Salesforce Service Cloud. <br> - Il valore predefinito è `"https://login.salesforce.com"`. <br> - Per copiare dati dalla sandbox, specificare `"https://test.salesforce.com"`. <br> - Per copiare dati dal dominio personalizzato, specificare ad esempio `"https://[domain].my.salesforce.com"`. |No |
| username |Specificare un nome utente per l'account utente. |Sì |
| password |Specificare la password per l'account utente.<br/><br/>Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| securityToken |Specificare un token di sicurezza per l'account utente. <br/><br/>Per informazioni generali sui token di sicurezza, vedere [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Sicurezza e API). Il token di sicurezza può essere ignorato solo se si aggiunge l'indirizzo IP del runtime di integrazione all'elenco di [indirizzi IP attendibili](https://developer.salesforce.com/docs/atlas.en-us.securityImplGuide.meta/securityImplGuide/security_networkaccess.htm) in Salesforce. Quando si usa il runtime di integrazione di Azure, vedere Indirizzi IP del runtime di integrazione di [Azure.](azure-integration-runtime-ip-addresses.md)<br/><br/>Per istruzioni su come ottenere e reimpostare un token di sicurezza, vedere Ottenere un token di [sicurezza.](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |No |
| apiVersion | Specificare la versione dell'API REST/Bulk di `48.0`Salesforce da utilizzare, ad esempio . Per impostazione predefinita, il connettore utilizza [v45](https://developer.salesforce.com/docs/atlas.en-us.218.0.api_rest.meta/api_rest/dome_versions.htm) per copiare i dati da Salesforce e [v40](https://developer.salesforce.com/docs/atlas.en-us.208.0.api_asynch.meta/api_asynch/asynch_api_intro.htm) per copiare i dati in Salesforce. | No |
| connectVia | Runtime [di integrazione](concepts-integration-runtime.md) da utilizzare per la connessione all'archivio dati. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No per l'origine, Sì per il sink se il servizio collegato all'origine non dispone di un runtime di integrazione |

>[!IMPORTANT]
>Quando si copiano dati in Salesforce Service Cloud, il runtime di integrazione di Azure predefinito non può essere usato per eseguire la copia. In altre parole, se il servizio collegato di origine non dispone di un runtime di integrazione specificato, creare in modo esplicito un runtime di integrazione di Azure con un percorso vicino all'istanza di Salesforce Service Cloud.In other words, if your source linked service doesn't have a specified integration [runtime, explicitly create an Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) with a location near your Salesforce Service Cloud instance. Associare il servizio collegato Salesforce Service Cloud come nell'esempio seguente.

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di](concepts-datasets-linked-services.md) dati. In questa sezione viene fornito un elenco delle proprietà supportate dal set di dati Salesforce Service Cloud.

Per copiare i dati da e in Salesforce Service Cloud, sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **SalesforceServiceCloudObject**.  | Sì |
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

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **RelationalTable**. | Sì |
| tableName | Nome della tabella in Salesforce Service Cloud. | No (se nell'origine dell'attività è specificato "query") |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione viene fornito un elenco delle proprietà supportate dall'origine e dal sink di Salesforce Service Cloud.

### <a name="salesforce-service-cloud-as-a-source-type"></a>Salesforce Service Cloud come tipo di origine

Per copiare i dati da Salesforce Service Cloud, nella sezione **dell'origine** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **SalesforceServiceCloudSource**. | Sì |
| query |Usare la query personalizzata per leggere i dati. È possibile usare una query SQL-92 o una query [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Vedere altri suggerimenti nella sezione [Suggerimenti di query](#query-tips). Se query non è specificata, verranno recuperati tutti i dati dell'oggetto Salesforce Service Cloud specificato in "objectApiName" nel set di dati. | No (se "objectApiName" è specificato nel set di dati) |
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

### <a name="salesforce-service-cloud-as-a-sink-type"></a>Salesforce Service Cloud come tipo di sink

Per copiare i dati in Salesforce Service Cloud, nella sezione del **sink** attività di copia sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **SalesforceServiceCloudSink**. | Sì |
| writeBehavior | Comportamento dell'azione di scrittura per l'operazione.<br/>I valori consentiti sono: **Insert** e **Upsert**. | No (il valore predefinito è Insert) |
| externalIdFieldName | Nome del campo ID esterno per l'operazione upsert. Il campo specificato deve essere definito come "Campo ID esterno" nell'oggetto Salesforce Service Cloud. Non può includere valori NULL nei dati di input corrispondenti. | Sì per "Upsert" |
| writeBatchSize | Il numero di righe di dati scritti in Salesforce Service Cloud in ogni batch. | No (il valore predefinito è 5.000) |
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

### <a name="retrieve-data-from-a-salesforce-service-cloud-report"></a>Recuperare dati da un report Salesforce Service Cloud

È possibile recuperare dati dai report Salesforce Service `{call "<report name>"}`Cloud specificando una query come . Un esempio è `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-service-cloud-recycle-bin"></a>Recuperare i record eliminati dal Cestino cloud di Salesforce Service

Per eseguire una query sui record eliminati temporaneamente dal `readBehavior` `queryAll`Cestino cloud di Salesforce Service, è possibile specificare come . 

### <a name="difference-between-soql-and-sql-query-syntax"></a>Differenza tra la sintassi di query SOQL e SQL

Quando si copiano dati da Salesforce Service Cloud, è possibile utilizzare una query SOQL o una query SQL. Si noti che queste due query hanno una sintassi e un supporto di funzionalità diversi. Non mischiarli. Si consiglia di utilizzare la query SOQL supportata in modo nativo da Salesforce Service Cloud. Nella tabella seguente sono elencate le differenze principali:

| Sintassi | Modalità SOQL | Modalità SQL |
|:--- |:--- |:--- |
| Selezione di colonne | È necessario enumerare i campi da copiare nella query, ad esempio`SELECT field1, filed2 FROM objectname` | `SELECT *` è supportata oltre alla selezione della colonna. |
| Virgolette | I nomi di campo/oggetto non possono essere racchiusi tra virgolette. | I nomi di campo/oggetto non possono essere racchiusi tra virgolette, ad es. `SELECT "id" FROM "Account"` |
| Formato Datetime |  Fare riferimento a informazioni dettagliate [qui](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql_select_dateformats.htm) ed esempi nella sezione successiva. | Fare riferimento a informazioni dettagliate [qui](https://docs.microsoft.com/sql/odbc/reference/develop-app/date-time-and-timestamp-literals?view=sql-server-2017) ed esempi nella sezione successiva. |
| Valori booleani | Rappresentati come `False` e `True`, ad esempio `SELECT … WHERE IsDeleted=True`. | Rappresentati come 0 o 1, ad esempio `SELECT … WHERE IsDeleted=1`. |
| Ridenominazione delle colonne | Non supportato. | Supportata, ad es. `SELECT a AS b FROM …`. |
| Relazione | Supportata, ad es. `Account_vod__r.nvs_Country__c`. | Non supportato. |

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Recuperare i dati usando una clausola where nella colonna DateTime

Quando si specifica la query SOQL o SQL, prestare attenzione alla differenza di formato di DateTime. Ad esempio:

* **Campione SOQL**:`SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Esempio SQL**:`SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}`

### <a name="error-of-malformed_querytruncated"></a>Errore di MALFORMED_QUERY:Troncato

Se si colpisce l'errore "MALFORMED_QUERY: troncato", in genere è dovuto alla data di cui è presente una colonna di tipo JunctionIdList nei dati e Salesforce ha limitazioni sul supporto di tali dati con un numero elevato di righe. Per attenuare, provare a escludere la colonna JunctionIdList o limitare il numero di righe da copiare (è possibile eseguire più attività di copia).

## <a name="data-type-mapping-for-salesforce-service-cloud"></a>Mapping dei tipi di dati per Salesforce Service Cloud

Quando si copiano dati da Salesforce Service Cloud, i mapping seguenti vengono utilizzati dai tipi di dati Salesforce Service Cloud ai tipi di dati provvisori di Data Factory. Per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

| Tipo di dati Salesforce Service Cloud | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| Numero automatico |string |
| Casella di controllo |Boolean |
| Valuta |Decimal |
| Data |Datetime |
| Data/ora |Datetime |
| Email |string |
| ID |string |
| Relazione di ricerca |string |
| Elenco a discesa seleziona multipla |string |
| Number |Decimal |
| Percentuale |Decimal |
| Telefono |string |
| Elenco a discesa |string |
| Testo |string |
| Area di testo |string |
| Area di testo (Long) |string |
| Area di testo (Rich) |string |
| Testo (Crittografato) |string |
| URL |string |

## <a name="lookup-activity-properties"></a>Proprietà dell'attività di ricerca

Per informazioni dettagliate sulle proprietà, selezionare [Attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
