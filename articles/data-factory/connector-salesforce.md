---
title: Copiare dati da e in Salesforce usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come copiare dati da Salesforce in archivi dati di sink supportati o da archivi dati di origine supportati in Salesforce usando un'attività di copia in una pipeline di data factory."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2018
ms.author: jingwang
ms.openlocfilehash: 4b2561aa338707567b44237e668e9d6d1a01bfea
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/09/2018
---
# <a name="copy-data-from-and-to-salesforce-by-using-azure-data-factory"></a>Copiare dati da e in Salesforce usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibile a livello generale](v1/data-factory-salesforce-connector.md)
> * [Versione 2 - Anteprima](connector-salesforce.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Salesforce. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta informazioni generali sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 di Data Factory, disponibile a livello generale, vedere [Connettore di Salesforce nella versione 1](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Salesforce in qualsiasi archivio dati di sink supportato. È anche possibile copiare dati da qualsiasi archivio di dati di origine supportato in Salesforce. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Salesforce supporta:

- Le edizioni Developer, Professional, Enterprise o Unlimited di Salesforce.
- La copia di dati da e nell'ambiente di produzione, nella sandbox e nel dominio personalizzato di Salesforce.

## <a name="prerequisites"></a>prerequisiti

In Salesforce deve essere abilitata l'autorizzazione API. Per altre informazioni, vedere [Enable API access in Salesforce by permission set](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/) (Abilitare l'accesso al'API in Salesforce in base al set di autorizzazioni).

## <a name="salesforce-request-limits"></a>Limiti delle richieste Salesforce

Salesforce presenta limiti per le richieste API totali e per le richieste API simultanee. Tenere presente quanto segue:

- Se il numero di richieste simultanee supera il limite, si verifica una limitazione e vengono visualizzati errori casuali.
- Se il numero totale di richieste supera il limite, l'account di Salesforce viene bloccato per 24 ore.

In entrambi gli scenari è anche possibile che venga visualizzato il messaggio di errore "REQUEST_LIMIT_EXCEEDED" ("LIMITE_RICHIESTE_SUPERATO"). Per altre informazioni, vedere la sezione "API Request Limits" (Limiti delle richieste API) in [Salesforce developer limits](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Limiti per sviluppatori di Salesforce).

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per il connettore Salesforce.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Salesforce sono supportate le proprietà seguenti.

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su **Salesforce**. |Sì |
| environmentUrl | Specificare l'URL dell'istanza di Salesforce. <br> - Il valore predefinito è `"https://login.salesforce.com"`. <br> - Per copiare dati dalla sandbox, specificare `"https://test.salesforce.com"`. <br> - Per copiare dati dal dominio personalizzato, specificare ad esempio `"https://[domain].my.salesforce.com"`. |No  |
| username |Specificare un nome utente per l'account utente. |Sì |
| password |Specificare la password per l'account utente.<br/><br/>Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| securityToken |Specificare un token di sicurezza per l'account utente. Per istruzioni su come reimpostare o ottenere un token di sicurezza, vedere [Get a security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Ottenere un token di sicurezza). Per informazioni generali sui token di sicurezza, vedere [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Sicurezza e API).<br/><br/>Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). |Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Se non specificato, viene usato il runtime di integrazione di Azure predefinito. | No per l'origine, Sì per il sink se il servizio collegato all'origine non dispone di un runtime di integrazione |

>[!IMPORTANT]
>Quando si copiano dati in Salesforce, non è possibile usare il runtime di integrazione di Azure predefinito per eseguire l'attività di copia. In altre parole, se per il servizio collegato all'origine non viene specificato un runtime di integrazione, [creare un Azure Integration Runtime](create-azure-integration-runtime.md#create-azure-ir) in modo esplicito con una posizione vicina all'istanza di Salesforce. Associare il servizio collegato di Salesforce come nell'esempio seguente.

**Esempio: Archiviare le credenziali in Data Factory**

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
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

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Salesforce.

Per copiare dati da e in Salesforce, impostare la proprietà type del set di dati su **SalesforceObject**. Sono supportate le proprietà seguenti.

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **SalesforceObject**.  | Sì |
| objectApiName | Nome dell'oggetto di Salesforce da cui recuperare i dati. | No per l'origine, Sì per il sink |

> [!IMPORTANT]
> La parte "__c" del**nome dell'API** è necessaria per qualsiasi oggetto personalizzato.

![Nome API della connessione Salesforce di Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Esempio:**

```json
{
    "name": "SalesforceDataset",
    "properties": {
        "type": "SalesforceObject",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "objectApiName": "MyTable__c"
        }
    }
}
```

>[!NOTE]
>Per compatibilità con le versioni precedenti, quando si copiano dati da Salesforce, l'uso del set di dati di tipo "RelationalTable" precedente continua a funzionare, anche se viene consigliato di passare all'uso del nuovo tipo "SalesforceObject".

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **RelationalTable**. | Sì |
| tableName | Nome della tabella in Salesforce. | No (se nell'origine dell'attività è specificato "query") |

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine e dal sink Salesforce.

### <a name="salesforce-as-a-source-type"></a>Salesforce come tipo di origine

Per copiare dati da Salesforce, impostare il tipo di origine nell'attività di copia su **SalesforceSource**. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **SalesforceSource**. | Sì |
| query |Usare la query personalizzata per leggere i dati. È possibile usare una query SQL-92 o una query [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Un esempio è `select * from MyTable__c`. | No (se nel set di dati è specificato "tableName") |

> [!IMPORTANT]
> La parte "__c" del**nome dell'API** è necessaria per qualsiasi oggetto personalizzato.

![Elenco dei nomi API della connessione Salesforce di Data Factory](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Salesforce input dataset name>",
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
                "type": "SalesforceSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

>[!NOTE]
>Per compatibilità con le versioni precedenti, quando si copiano dati da Salesforce, se si usa il tipo "RelationalSource" precedente, l'origine continua a funzionare anche se viene consigliato di passare all'uso del nuovo tipo "SalesforceSource".

### <a name="salesforce-as-a-sink-type"></a>Salesforce come tipo di sink

Per copiare dati in Salesforce, impostare il tipo di sink nell'attività di copia su **SalesforceSink**. Nella sezione **sink** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del sink dell'attività di copia deve essere impostata su **SalesforceSink**. | Sì |
| writebehavior | Comportamento dell'azione di scrittura per l'operazione.<br/>I valori consentiti sono: **Insert** e **Upsert**. | No (il valore predefinito è Insert) |
| externalIdFieldName | Nome del campo ID esterno per l'operazione upsert. Il campo specificato deve essere definito come "External Id Field" (Campo ID esterno) nell'oggetto di Salesforce. Non può includere valori NULL nei dati di input corrispondenti. | Sì per "Upsert" |
| writeBatchSize | Conteggio delle righe di dati scritti da Salesforce in ogni batch. | No (il valore predefinito è 5.000) |
| ignoreNullValues | Indica se ignorare i valori NULL dai dati di input durante un'operazione di scrittura.<br/>I valori consentiti sono **true** e **false**.<br>- **True**: i dati nell'oggetto di destinazione rimangono invariati quando si esegue un'operazione di upsert o aggiornamento. Inserire un valore predefinito definito quando si esegue un'operazione di inserimento.<br/>- **False**: i dati nell'oggetto di destinazione vengono aggiornati a NULL quando si esegue un'operazione di upsert o aggiornamento. Inserire un valore NULL quando si esegue un'operazione di inserimento. | No (il valore predefinito è false) |

**Esempio: Sink Salesforce in un'attività di copia**

```json
"activities":[
    {
        "name": "CopyToSalesforce",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Salesforce output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "SalesforceSink",
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

### <a name="retrieve-data-from-a-salesforce-report"></a>Recuperare dati da un report di Salesforce

È possibile recuperare dati dai report di Salesforce specificando una query come `{call "<report name>"}`. Un esempio è `"query": "{call \"TestReport\"}"`.

### <a name="retrieve-deleted-records-from-the-salesforce-recycle-bin"></a>Recuperare i record eliminati dal Cestino di Salesforce

Per recuperare i record eliminati temporaneamente dal Cestino di Salesforce, è possibile specificare **"IsDeleted = 1"** nella query. Ad esempio: 

* Per recuperare solo i record eliminati, specificare "select * from MyTable__c **where IsDeleted= 1**".
* Per recuperare tutti i record inclusi quelli esistenti ed eliminati, specificare "select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**."

### <a name="retrieve-data-by-using-a-where-clause-on-the-datetime-column"></a>Recuperare i dati usando una clausola where nella colonna DateTime

Quando si specifica la query SOQL o SQL, prestare attenzione alla differenza di formato di DateTime. Ad esempio: 

* **Esempio SOQL**: `SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= @{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-ddTHH:mm:ssZ')} AND LastModifiedDate < @{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-ddTHH:mm:ssZ')}`
* **Esempio SQL**: `SELECT * FROM Account WHERE LastModifiedDate >= {ts'@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}'} AND LastModifiedDate < {ts'@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'}"`

## <a name="data-type-mapping-for-salesforce"></a>Mapping dei tipi di dati per Salesforce

Quando si copiano dati da Salesforce, vengono usati i mapping seguenti tra i tipi di dati di Salesforce e i tipi di dati provvisori di Data Factory. Per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink, vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md).

| Tipo di dati di Salesforce | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| Numero automatico |string |
| Casella di controllo |boolean |
| Valuta |Double |
| Data |Datetime |
| Data/ora |Datetime |
| Email |string |
| ID |string |
| Relazione di ricerca |string |
| Elenco a discesa seleziona multipla |string |
| Number |Double |
| Percentuale |Double |
| Telefono |string |
| Elenco a discesa |string |
| Text |string |
| Area di testo |string |
| Area di testo (Long) |string |
| Area di testo (Rich) |string |
| Testo (Crittografato) |string |
| URL |string |

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).