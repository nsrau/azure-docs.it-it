---
title: Copiare dati da SAP ECC
description: Informazioni su come copiare dati da SAP ECC in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/03/2020
ms.openlocfilehash: 9088b36acead9f47e94949ee102d66a8aff2d226
ms.sourcegitcommit: 3d56d25d9cf9d3d42600db3e9364a5730e80fa4a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/03/2020
ms.locfileid: "87529603"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Copiare dati da SAP ECC usando Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da SAP Enterprise Central Component (ECC). Per altre informazioni, vedere [Panoramica dell'attività di copia](copy-activity-overview.md).

>[!TIP]
>Per informazioni sul supporto generale di ADF sullo scenario di integrazione dei dati SAP, vedere l'articolo relativo all' [integrazione dei dati SAP con Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) con un'introduzione dettagliata su ogni connettore SAP, comparsing e linee guida.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore SAP ECC è supportato per le attività seguenti:

- [Attività Copy](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da SAP ECC a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore SAP ECC supporta:

- La copia di dati da SAP ECC su SAP NetWeaver versione 7.0 e successive.
- La copia di dati da oggetti esposti dai servizi OData di SAP ECC, ad esempio:

  - Viste o tabelle SAP.
  - Oggetti Business Application Programming Interface [BAPI].
  - Estrattori di dati.
  - Dati o Intermediate Document (IDOC) inviati all'integrazione del processo (PI) SAP che possono essere ricevuti come OData tramite i relativi adattatori.

- La copia di dati usando l'autenticazione di base.

>[!TIP]
>Per copiare dati da SAP ECC tramite una tabella o una vista SAP, usare il connettore [tabella SAP](connector-sap-table.md), che è più veloce e più scalabile.

## <a name="prerequisites"></a>Prerequisiti

Per usare questo connettore SAP ECC, è necessario esporre le entità ECC SAP tramite i servizi OData tramite il gateway SAP. Più in particolare:

- **Configurare il gateway SAP**. Per i server con SAP NetWeaver versioni successive alla 7.4, il gateway SAP è già installato. Per le versioni precedenti, è necessario installare il gateway SAP incorporato o il sistema per l'hub del gateway SAP prima di esporre i dati SAP ECC tramite i servizi OData. Per configurare il gateway SAP, vedere la [guida all'installazione](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Attivare e configurare il servizio OData di SAP**. È possibile attivare il servizio OData tramite TCODE SICF in pochi secondi. È anche possibile configurare gli oggetti da esporre. Per altre informazioni, vedere la [guida dettagliata](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore SAP ECC.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di SAP ECC sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| `type` | La proprietà `type` deve essere impostata su `SapEcc`. | Sì |
| `url` | URL del servizio OData di SAP ECC. | Sì |
| `username` | Nome utente usato per la connessione a SAP ECC. | No |
| `password` | Password di testo non crittografato usata per la connessione a SAP ECC. | No |
| `connectVia` | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Per altre informazioni, vedere la sezione [Prerequisiti](#prerequisites). Se non viene specificato un runtime, come valore predefinito viene usato Azure Integration Runtime. | No |

### <a name="example"></a>Esempio

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData URL, e.g., http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of integration runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati](concepts-datasets-linked-services.md). La sezione seguente presenta un elenco delle proprietà supportate dal set di dati SAP ECC.

Per copiare dati da SAP ECC, impostare la proprietà `type` del set di dati su `SapEccResource`.

Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| `path` | Percorso dell'entità SAP ECC OData. | Sì |

### <a name="example"></a>Esempio

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typeProperties": {
            "path": "<entity path, e.g., dd04tentitySet>"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipeline](concepts-pipelines-activities.md). La sezione seguente presenta un elenco delle proprietà supportate dall'origine SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC come origine

Per copiare dati da SAP ECC, impostare la proprietà `type` nella sezione `source` dell'attività di copia su `SapEccSource`.

Nella sezione `source` dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| `type` | La proprietà `type` della sezione `source` dell'attività di copia deve essere impostata su `SapEccSource`. | Sì |
| `query` | Opzioni di query OData per filtrare i dati. Ad esempio:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>il connettore SAP ECC copia dati dall'URL combinato:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Per altre informazioni, vedere [OData URL components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/) (Componenti dell'URL di OData). | No |
| `sapDataColumnDelimiter` | Singolo carattere utilizzato come delimitatore passato a SAP RFC per suddividere i dati di output. | No |
| `httpRequestTimeout` | Timeout (valore di **TimeSpan**) durante il quale la richiesta HTTP attende una risposta. Si tratta del timeout per ottenere una risposta, non per leggere i dati della risposta. Se non è specificato, il valore predefinito è **00:30:00** (30 minuti). | No |

### <a name="example"></a>Esempio

```json
"activities":[
    {
        "name": "CopyFromSAPECC",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP ECC input dataset name>",
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
                "type": "SapEccSource",
                "query": "$top=10"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mappings-for-sap-ecc"></a>Mapping dei tipi di dati per SAP ECC

Quando si copiano dati da SAP ECC, vengono usati i mapping seguenti tra i tipi di dati di OData per i dati di SAP ECC e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di OData | Tipo di dati provvisorio di Data Factory |
|:--- |:--- |
| `Edm.Binary` | `String` |
| `Edm.Boolean` | `Bool` |
| `Edm.Byte` | `String` |
| `Edm.DateTime` | `DateTime` |
| `Edm.Decimal` | `Decimal` |
| `Edm.Double` | `Double` |
| `Edm.Single` | `Single` |
| `Edm.Guid` | `String` |
| `Edm.Int16` | `Int16` |
| `Edm.Int32` | `Int32` |
| `Edm.Int64` | `Int64` |
| `Edm.SByte` | `Int16` |
| `Edm.String` | `String` |
| `Edm.Time` | `TimeSpan` |
| `Edm.DateTimeOffset` | `DateTimeOffset` |

> [!NOTE]
> I tipi di dati complessi non sono attualmente supportati.

## <a name="lookup-activity-properties"></a>Proprietà dell'attività Lookup

Per altre informazioni sulle proprietà, vedere [Attività Lookup](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
