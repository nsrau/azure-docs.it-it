---
title: Copiare dati da SAP ECC usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come copiare dati da SAP ECC in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory."
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
ms.date: 02/27/2018
ms.author: jingwang
ms.openlocfilehash: efca2c129a1c7b8aca6b879d6d1311c6be157be1
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2018
---
# <a name="copy-data-from-sap-ecc-using-azure-data-factory"></a>Copiare dati da SAP ECC usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da SAP ECC (SAP Enterprise Central Component). Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da SAP ECC a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore SAP ECC supporta:

- La copia di dati da SAP ECC su SAP NetWeaver versione 7.0 e versioni successive. 
- La copia di dati da oggetti esposti dai servizi OData di SAP ECC (ad esempio viste o tabelle SAP, BAPI, estrattori di dati e così via), o dati/IDOC inviati a SAP PI che possono essere ricevuti come OData tramite i relativi adattatori.
- La copia di dati usando l'autenticazione di base.

## <a name="prerequisites"></a>prerequisiti

In genere, SAP ECC espone entità tramite i servizi OData mediante il gateway SAP. Per usare questo connettore SAP ECC, è necessario:

- **Configurare il gateway SAP**. Per i server con SAP NetWeaver versione successiva alla 7.4, il gateway SAP è già installato. In caso contrario, è necessario installare il gateway incorporato o un hub di gateway prima di esporre i dati di SAP ECC tramite i servizi OData. Informazioni su come configurare il gateway SAP dalla [Guida all'installazione](https://help.sap.com/saphelp_gateway20sp12/helpdata/en/c3/424a2657aa4cf58df949578a56ba80/frameset.htm).

- **Attivare e configurare il servizio OData SAP**. È possibile attivare i servizi OData tramite TCODE SICF in pochi secondi. È inoltre possibile configurare gli oggetti da esporre. Di seguito è riportato un esempio di [istruzioni dettagliate](https://blogs.sap.com/2012/10/26/step-by-step-guide-to-build-an-odata-service-based-on-rfcs-part-1/).

## <a name="getting-started"></a>Introduzione

È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore SAP ECC.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di SAP ECC sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **SapEcc** | Sì |
| URL | URL del servizio SAP ECC OData. | Sì |
| username | Nome utente usato per la connessione a SAP ECC. | No  |
| password | Password di testo non crittografato usata per la connessione a SAP ECC. | No  |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione self-hosted o il runtime di integrazione di Azure (se l'archivio dati è accessibile pubblicamente). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

**Esempio:**

```json
{
    "name": "SapECCLinkedService",
    "properties": {
        "type": "SapEcc",
        "typeProperties": {
            "url": "<SAP ECC OData url e.g. http://eccsvrname:8000/sap/opu/odata/sap/zgw100_dd02l_so_srv/>",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        }
    },
    "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati SAP ECC.

Per copiare dati da SAP ECC, impostare la proprietà type del set di dati su **SapEccResource**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| path | Percorso dell'entità SAP ECC OData. | Sì |

**Esempio**

```json
{
    "name": "SapEccDataset",
    "properties": {
        "type": "SapEccResource",
        "typePoperties": {
            "path": "<entity path e.g. dd04tentitySet>"
        },
        "linkedServiceName": {
            "referenceName": "<SAP ECC linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine SAP ECC.

### <a name="sap-ecc-as-source"></a>SAP ECC come origine

Per copiare dati da SAP ECC, impostare il tipo di origine nell'attività di copia su **SapEccSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **SapEccSource** | Sì |
| query | Opzioni di query OData per filtrare i dati. Esempio: "$select=Name,Description&$top=10".<br/><br/>Il connettore SAP ECC copia i dati dall'URL combinato: (URL specificato nel servizio collegato)/(percorso specificato nel set di dati)?(query specificata nell'origine dell'attività di copia). Fare riferimento all'articolo sui [componenti URL di OData](http://www.odata.org/documentation/odata-version-3-0/url-conventions/). | Sì |

**Esempio:**

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

## <a name="data-type-mapping-for-sap-ecc"></a>Mapping dei tipi di dati per SAP ECC

Quando si copiano dati da SAP ECC, vengono usati i mapping seguenti tra i tipi di dati di OData per i dati di SAP ECC e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di OData | Tipo di dati provvisori di Data Factory |
|:--- |:--- |:--- |
| Edm.Binary | string |
| Edm.Boolean | Booleano |
| Edm.Byte | string |
| Edm.DateTime | Datetime |
| Edm.Decimal | Decimal |
| Edm.Double | Double |
| Edm.Single | Single |
| Edm.Guid | string |
| Edm.Int16 | Int16 |
| Edm.Int32 | Int32 |
| Edm.Int64 | Int64 |
| Edm.SByte | Int16 |
| Edm.String | string |
| Edm.Time | Intervallo di tempo |
| Edm.DateTimeOffset | DateTimeOffset |

> [!NOTE]
> I tipi di dati complessi non sono supportati.

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
