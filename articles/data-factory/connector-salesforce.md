---
title: Copiare dati da Salesforce usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come copiare dati da Salesforce in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory."
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
ms.date: 08/30/2017
ms.author: jingwang
ms.openlocfilehash: 7978e955bf5516a853443555ab10a69dcf22d63f
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2017
---
# <a name="copy-data-from-salesforce-using-azure-data-factory"></a>Copiare dati da Salesforce usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-salesforce-connector.md)
> * [Versione 2 - Anteprima](connector-salesforce.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database Salesforce. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le informazioni sul [connettore Salesforce nella versione 1](v1/data-factory-salesforce-connector.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un database Salesforce in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore Salesforce supporta le edizioni di Salesforce seguenti: **Developer Edition, Professional Edition, Enterprise Edition e Unlimited Edition**. Supporta anche la copia di dati dall'ambiente di **produzione, dalla sandbox e dal dominio personalizzato** di Salesforce.

## <a name="prerequisites"></a>Prerequisiti

* In Salesforce deve essere abilitata l'autorizzazione API. Vedere [How do I enable API access in Salesforce by permission set?](https://www.data2crm.com/migration/faqs/enable-api-access-salesforce-permission-set/)

## <a name="salesforce-request-limits"></a>Limiti delle richieste Salesforce

Salesforce presenta limiti per le richieste API totali e per le richieste API simultanee. Tenere presente quanto segue:

- Se il numero di richieste simultanee supera il limite, si verifica una limitazione e vengono visualizzati errori casuali.
- Se il numero totale di richieste supera il limite, l'account di Salesforce viene bloccato per 24 ore.

In entrambi gli scenari è anche possibile che venga visualizzato l'errore "REQUEST_LIMIT_EXCEEDED" ("LIMITE_RICHIESTE_SUPERATO"). Per i dettagli, vedere la sezione API Request Limits (Limiti delle richieste API) nell'articolo [Salesforce API Request Limits](http://resources.docs.salesforce.com/200/20/en-us/sfdc/pdf/salesforce_app_limits_cheatsheet.pdf) (Limiti delle richieste API di Salesforce).

## <a name="getting-started"></a>introduttiva
È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Salesforce.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Salesforce sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su **Salesforce**. |Sì |
| environmentUrl | Specificare l'URL dell'istanza di Salesforce. <br><br> - Il valore predefinito è `"https://login.salesforce.com"`. <br> - Per copiare dati dalla sandbox, specificare `"https://test.salesforce.com"`. <br> - Per copiare dati dal dominio personalizzato, specificare ad esempio `"https://[domain].my.salesforce.com"`. |No |
| username |Specificare un nome utente per l'account utente. |Sì |
| password |Specificare la password per l'account utente. |Sì |
| securityToken |Specificare un token di sicurezza per l'account utente. Per istruzioni su come ottenere o reimpostare un token di sicurezza, vedere [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Ottenere un token di sicurezza). Per informazioni generali sui token di sicurezza, vedere [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Sicurezza e API). |Sì |

**Esempio:**

```json
{
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
        }
    },
    "name": "SalesforceLinkedService"
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati Salesforce.

Per copiare dati da Salesforce, impostare la proprietà type del set di dati su **RelationalTable**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **RelationalTable** | Sì |
| tableName | Nome della tabella nel database Salesforce. | No (se nell'origine dell'attività è specificato "query") |

> [!IMPORTANT]
> La parte "__c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.

![Data Factory - connessione Salesforce - nome API](media/copy-data-from-salesforce/data-factory-salesforce-api-name.png)

**Esempio:**

```json
{
    "name": "SalesforceDataset",
    "properties":
    {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<Salesforce linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable__c"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Salesforce.

### <a name="salesforce-as-source"></a>Salesforce come origine

Per copiare dati da Salesforce, impostare il tipo di origine nell'attività di copia su **RelationalSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **RelationalSource** | Sì |
| query |Usare la query personalizzata per leggere i dati. È possibile usare una query SQL-92 o una query [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Ad esempio: `select * from MyTable__c`. | No (se nel set di dati è specificato "tableName") |

> [!IMPORTANT]
> La parte "__c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.

![Data Factory - connessione Salesforce - nome API](media/copy-data-from-salesforce/data-factory-salesforce-api-name-2.png)

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
                "type": "RelationalSource",
                "query": "SELECT Col_Currency__c, Col_Date__c, Col_Email__c FROM AllDataType__c"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="query-tips"></a>Suggerimenti di query

### <a name="retrieving-data-from-salesforce-report"></a>Recupero di dati dal report di Salesforce

Per recuperare dati dai report di Salesforce, è possibile specificare una query come `{call "<report name>"}. Example: `"query": "{call \"TestReport\"}".

### <a name="retrieving-deleted-records-from-salesforce-recycle-bin"></a>Recupero dei record eliminati dal Cestino di Salesforce

Per eseguire una query sui record eliminati temporaneamente dal Cestino di Salesforce, è possibile specificare **"IsDeleted = 1"** nella query. Ad esempio,

* Per eseguire una query solo sui record eliminati, specificare MyTable__c **where IsDeleted= 1**"
* Per eseguire query su tutti i record inclusi quelli esistenti ed eliminati, specificare "select * from MyTable__c **where IsDeleted = 0 or IsDeleted = 1**"

### <a name="retrieving-data-using-where-clause-on-datetime-column"></a>Recupero di dati tramite la clausola where nella colonna DateTime

Quando si specifica la query SQL o SOQL, prestare attenzione alla differenza di formato di DateTime. Ad esempio:

* **Esempio SOQL**: `$$Text.Format('SELECT Id, Name, BillingCity FROM Account WHERE LastModifiedDate >= {0:yyyy-MM-ddTHH:mm:ssZ} AND LastModifiedDate < {1:yyyy-MM-ddTHH:mm:ssZ}', <datetime parameter>, <datetime parameter>)`
* **Esempio SQL**: `$$Text.Format('SELECT * FROM Account WHERE LastModifiedDate >= {{ts\\'{0:yyyy-MM-dd HH:mm:ss}\\'}} AND LastModifiedDate < {{ts\\'{1:yyyy-MM-dd HH:mm:ss}\\'}}', <datetime parameter>, <datetime parameter>)`

## <a name="data-type-mapping-for-salesforce"></a>Mapping dei tipi di dati per Salesforce

Quando si copiano dati da Salesforce, vengono usati i mapping seguenti tra i tipi di dati di Salesforce e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di Salesforce | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
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


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md##supported-data-stores-and-formats).