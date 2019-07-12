---
title: Copiare dati da SAP ECC usando Azure Data Factory | Microsoft Docs
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
ms.date: 07/02/2019
ms.author: jingwang
ms.openlocfilehash: 7ccd2e7a804c6495f6caf5e264b1f7c2a36cb02e
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827778"
---
# <a name="copy-data-from-sap-ecc-by-using-azure-data-factory"></a>Copiare dati da SAP ECC usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da SAP Enterprise Central Component (ECC). Per altre informazioni, vedere [panoramica dell'attività di copia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da SAP ECC a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore SAP ECC supporta:

- Copia dei dati da SAP ECC su SAP NetWeaver versione 7.0 e versioni successive.
- Copia dei dati da tutti gli oggetti esposti dai servizi di SAP ECC OData, ad esempio:

  - SAP tabelle o viste.
  - Oggetti di Business Application Programming Interface [BAPI].
  - Estrattori di dati.
  - I dati o documenti intermedi (IDOC) inviati a SAP processo di integrazione (PI) che può essere ricevuto come OData tramite gli adapter relativi.

- Copia dei dati usando l'autenticazione di base.

>[!TIP]
>Per copiare dati da SAP ECC tramite una tabella SAP o una vista, usare il [tabella SAP](connector-sap-table.md) connettore, che è più veloce e più scalabile.

## <a name="prerequisites"></a>Prerequisiti

In genere, SAP ECC espone entità tramite i servizi OData mediante il gateway SAP. Per usare questo connettore SAP ECC, è necessario:

- **Configurare il gateway SAP**. Per i server con le versioni di SAP NetWeaver versione successiva alla 7.4, Gateway SAP è già installato. Per le versioni precedenti, è necessario installare il Gateway SAP incorporato o dal sistema dell'hub di Gateway SAP prima di esporre i dati di SAP ECC tramite i servizi OData. Per impostare il Gateway SAP, vedere la [Guida all'installazione](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Attivare e configurare il servizio OData SAP**. È possibile attivare il servizio OData tramite TCODE SICF in pochi secondi. È anche possibile configurare gli oggetti che devono essere esposti. Per altre informazioni, vedere la [istruzioni dettagliate](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore SAP ECC.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di SAP ECC sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| `type` | Il `type` proprietà deve essere impostata su `SapEcc`. | Sì |
| `url` | L'URL del servizio SAP ECC OData. | Yes |
| `username` | Il nome utente usato per connettersi a SAP ECC. | No |
| `password` | La password non crittografata utilizzata per connettersi a SAP ECC. | No |
| `connectVia` | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare un runtime di integrazione self-hosted o il runtime di integrazione di Azure (se l'archivio dati accessibile pubblicamente). Se non si specifica un runtime, `connectVia` Usa il runtime di integrazione di Azure predefinito. | No |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere [set di dati](concepts-datasets-linked-services.md). La sezione seguente fornisce un elenco delle proprietà supportate dal set di dati SAP ECC.

Per copiare dati da SAP ECC, impostare il `type` proprietà del set di dati `SapEccResource`.

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
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di attività, vedere [pipeline](concepts-pipelines-activities.md). La sezione seguente fornisce un elenco delle proprietà supportate dall'origine SAP ECC.

### <a name="sap-ecc-as-a-source"></a>SAP ECC come origine

Per copiare dati da SAP ECC, impostare il `type` proprietà di `source` sezione dell'attività di copia su `SapEccSource`.

Nell'attività di copia sono supportate le proprietà seguenti `source` sezione:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| `type` | Il `type` proprietà dell'attività di copia `source` sezione deve essere impostata su `SapEccSource`. | Sì |
| `query` | Le opzioni di query OData per filtrare i dati. Ad esempio:<br/><br/>`"$select=Name,Description&$top=10"`<br/><br/>Il connettore SAP ECC copia i dati dall'URL combinato:<br/><br/>`<URL specified in the linked service>/<path specified in the dataset>?<query specified in the copy activity's source section>`<br/><br/>Per altre informazioni, vedere [OData URL components](https://www.odata.org/documentation/odata-version-3-0/url-conventions/) (Componenti dell'URL di OData). | No |

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

Quando si copiano dati da SAP ECC, seguenti vengono usati i mapping dai tipi di dati di OData per i dati di SAP ECC a tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

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
> Tipi di dati complessi non sono attualmente supportati.

## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Azure Data Factory, vedere [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
