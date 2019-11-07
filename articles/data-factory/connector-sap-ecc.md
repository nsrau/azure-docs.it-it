---
title: Copiare dati da SAP ECC usando Azure Data Factory
description: Informazioni su come copiare dati da SAP ECC in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/02/2019
ms.author: jingwang
ms.openlocfilehash: 526f85ca4b8854a36232c75a55847a73a8d372cc
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680305"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Copiare dati da SAP ECC usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da SAP Enterprise Central Component (ECC). Per altre informazioni, vedere [Cenni preliminari sull'attività di copia](copy-activity-overview.md).

>[!TIP]
>Per informazioni sul supporto generale di ADF sullo scenario di integrazione dei dati SAP, vedere l'articolo relativo all' [integrazione dei dati SAP con Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) con informazioni dettagliate introduttive, comparsing e linee guida.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore SAP ECC è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da SAP ECC a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore SAP ECC supporta:

- Copia di dati da SAP ECC in SAP NetWeaver versione 7,0 e successive.
- Copia di dati da qualsiasi oggetto esposto da SAP ECC OData Services, ad esempio:

  - Tabelle o viste SAP.
  - Oggetti business Application Programming Interface [Bova].
  - Estrattori di dati.
  - Dati o documenti intermedi (IDOCs) inviati all'integrazione del processo SAP (PI) che può essere ricevuta come OData tramite adattatori relativi.

- Copia dei dati tramite l'autenticazione di base.

>[!TIP]
>Per copiare dati da SAP ECC tramite una tabella o una vista SAP, usare [SAP Table](connector-sap-table.md) Connector, che è più veloce e più scalabile.

## <a name="prerequisites"></a>Prerequisiti

In genere, SAP ECC espone entità tramite i servizi OData mediante il gateway SAP. Per usare questo connettore SAP ECC, è necessario:

- **Configurare il gateway SAP**. Per i server con versioni di SAP NetWeaver successive alla 7,4, il gateway SAP è già installato. Per le versioni precedenti, è necessario installare il gateway SAP incorporato o il sistema Hub gateway SAP prima di esporre i dati SAP ECC tramite i servizi OData. Per configurare il gateway SAP, vedere la [Guida all'installazione](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Attivare e configurare il servizio SAP OData**. È possibile attivare il servizio OData tramite TCODE SICF in pochi secondi. È anche possibile configurare quali oggetti devono essere esposti. Per ulteriori informazioni, vedere le [istruzioni dettagliate](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="prerequisites"></a>Prerequisiti

[!INCLUDE [data-factory-v2-integration-runtime-requirements](../../includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire le entità Data Factory specifiche del connettore SAP ECC.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato SAP ECC sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| `type` | È necessario impostare la proprietà `type` su `SapEcc`. | Sì |
| `url` | URL del servizio SAP ECC OData. | Sì |
| `username` | Nome utente usato per la connessione a SAP ECC. | No |
| `password` | Password in testo non crittografato utilizzata per la connessione a SAP ECC. | No |
| `connectVia` | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. Ulteriori informazioni sono disponibili nella sezione [prerequisiti](#prerequisites) . Se non si specifica un runtime, viene usato il runtime di integrazione di Azure predefinito. | No |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere [Datasets](concepts-datasets-linked-services.md). Nella sezione seguente viene fornito un elenco delle proprietà supportate dal set di dati SAP ECC.

Per copiare dati da SAP ECC, impostare la proprietà `type` del set di dati su `SapEccResource`.

Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [pipeline](concepts-pipelines-activities.md). Nella sezione seguente viene fornito un elenco delle proprietà supportate dall'origine SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC come origine

Per copiare dati da SAP ECC, impostare la proprietà `type` nella sezione `source` dell'attività di copia su `SapEccSource`.

Nella sezione `source` dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| `type` | La proprietà `type` della sezione `source` dell'attività di copia deve essere impostata su `SapEccSource`. | Sì |
| `query` | Opzioni di query OData per filtrare i dati. Ad esempio:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Il connettore SAP ECC copia i dati dall'URL combinato:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Per altre informazioni, vedere [OData URL components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/) (Componenti dell'URL di OData). | No |

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

Quando si copiano dati da SAP ECC, i mapping seguenti vengono usati dai tipi di dati OData per i dati SAP ECC per Azure Data Factory tipi di dati provvisori. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

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

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
