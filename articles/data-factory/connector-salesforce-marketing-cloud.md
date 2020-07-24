---
title: Copiare dati da Salesforce Marketing Cloud
description: Informazioni su come copiare dati da Salesforce Marketing Cloud in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/17/2020
ms.openlocfilehash: 1f0fb1ee8580c0c7f6eb30228b65e0a3780ef0a8
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076799"
---
# <a name="copy-data-from-salesforce-marketing-cloud-using-azure-data-factory"></a>Copiare dati da Salesforce Marketing Cloud usando Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Salesforce Marketing Cloud. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore di Salesforce Marketing Cloud è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Salesforce Marketing Cloud in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Salesforce Marketing Cloud Connector supporta l'autenticazione OAuth 2 e supporta sia i tipi di pacchetto legacy che quelli avanzati. Il connettore è basato sull' [API REST di Salesforce Marketing Cloud](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/index-api.htm).

>[!NOTE]
>Questo connettore non supporta il recupero di oggetti personalizzati o le estensioni per dati personalizzati.

## <a name="getting-started"></a>Guida introduttiva

È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Per istruzioni dettagliate su come creare una pipeline con un'attività di copia, vedere l' [esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) .

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Salesforce Marketing Cloud.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Salesforce Marketing Cloud sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **SalesforceMarketingCloud** | Sì |
| connectionProperties | Gruppo di proprietà che definisce come connettersi a Salesforce Marketing Cloud. | Sì |
| ***In `connectionProperties` :*** | | |
| authenticationType | specifica il metodo di autenticazione da usare. I valori consentiti sono `Enhanced sts OAuth 2.0` o `OAuth_2.0` .<br><br>Il pacchetto Legacy Marketing Cloud di Salesforce supporta solo `OAuth_2.0` , mentre le esigenze del pacchetto sono migliorate `Enhanced sts OAuth 2.0` . <br>Dal 1 ° agosto 2019, Salesforce Marketing Cloud ha rimosso la possibilità di creare pacchetti legacy. Tutti i nuovi pacchetti sono pacchetti avanzati. | Sì |
| host | Per il pacchetto migliorato, l'host deve essere il [sottodominio](https://developer.salesforce.com/docs/atlas.en-us.mc-apis.meta/mc-apis/your-subdomain-tenant-specific-endpoints.htm) rappresentato da una stringa di 28 caratteri che inizia con le lettere "MC", ad esempio `mc563885gzs27c5t9-63k636ttgm` . <br>Per il pacchetto legacy, specificare `www.exacttargetapis.com` . | Sì |
| clientId | L'ID client associato all'applicazione Salesforce Marketing Cloud.  | Sì |
| clientSecret | Il segreto client associato all'applicazione Salesforce Marketing Cloud. È possibile scegliere di contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in ADF o archiviare il segreto in Azure Key Vault e consentire all'attività di copia di ADF di eseguire il pull da tale posizione durante l'esecuzione della copia dei dati. per altre informazioni, vedere [archiviare le credenziali in Key Vault](store-credentials-in-key-vault.md). | Sì |
| useEncryptedEndpoints | Specifica se gli endpoint dell'origine dati vengono crittografati tramite HTTPS. Il valore predefinito è true.  | No |
| useHostVerification | Specifica se è necessario che il nome host nel certificato del server corrisponda al nome host del server durante la connessione tramite TLS. Il valore predefinito è true.  | No |
| usePeerVerification | Specifica se verificare l'identità del server durante la connessione tramite TLS. Il valore predefinito è true.  | No |

**Esempio: uso dell'autenticazione STS OAuth 2 avanzata per il pacchetto migliorato** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "<subdomain e.g. mc563885gzs27c5t9-63k636ttgm>",
                "authenticationType": "Enhanced sts OAuth 2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

**Esempio: uso dell'autenticazione OAuth 2 per il pacchetto legacy** 

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "connectionProperties": {
                "host": "www.exacttargetapis.com",
                "authenticationType": "OAuth_2.0",
                "clientId": "<clientId>",
                "clientSecret": {
                     "type": "SecureString",
                     "value": "<clientSecret>"
                },
                "useEncryptedEndpoints": true,
                "useHostVerification": true,
                "usePeerVerification": true
            }
        }
    }
}

```

Se si usa il servizio collegato cloud marketing di Salesforce con il payload seguente, è ancora supportato così com'è, mentre si consiglia di usare quello nuovo in futuro, che aggiunge il supporto migliorato per i pacchetti.

```json
{
    "name": "SalesforceMarketingCloudLinkedService",
    "properties": {
        "type": "SalesforceMarketingCloud",
        "typeProperties": {
            "clientId": "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "useEncryptedEndpoints": true,
            "useHostVerification": true,
            "usePeerVerification": true
        }
    }
}

```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Salesforce Marketing Cloud.

Per copiare dati da Salesforce Marketing Cloud, impostare la proprietà type del set di dati su **SalesforceMarketingCloudObject**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà Type del set di dati deve essere impostata su: **SalesforceMarketingCloudObject** | Sì |
| tableName | Nome della tabella. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "SalesforceMarketingCloudDataset",
    "properties": {
        "type": "SalesforceMarketingCloudObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SalesforceMarketingCloud linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Salesforce Marketing Cloud.

### <a name="salesforce-marketing-cloud-as-source"></a>Salesforce Marketing Cloud come origine

Per copiare dati da Salesforce Marketing Cloud, impostare il tipo di origine nell'attività di copia su **SalesforceMarketingCloudSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **SalesforceMarketingCloudSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSalesforceMarketingCloud",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SalesforceMarketingCloud input dataset name>",
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
                "type": "SalesforceMarketingCloudSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
