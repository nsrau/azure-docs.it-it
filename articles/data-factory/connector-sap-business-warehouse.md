---
title: Copiare dati da SAP BW usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come copiare dati da SAP Business Warehouse in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory."
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
ms.date: 01/10/2018
ms.author: jingwang
ms.openlocfilehash: 7f494cff1e8dc57a41467cd722fdf224e10c9dec
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2018
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Copiare dati da SAP Business Warehouse usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-sap-business-warehouse-connector.md)
> * [Versione 2 - Anteprima](connector-sap-business-warehouse.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database SAP Business Warehouse (BW). Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere le informazioni sul [connettore SAP BW nella versione 1](v1/data-factory-sap-business-warehouse-connector.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da SAP Business Warehouse in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore SAP Business Warehouse supporta:

- SAP Business Warehouse **versione 7.x**.
- La copia di dati da **InfoCube e QueryCube** (incluse query BEx) tramite query MDX.
- La copia di dati usando l'autenticazione di base.

## <a name="prerequisites"></a>prerequisiti

Per usare il connettore SAP Business Warehouse, è necessario:

- Configurare un runtime di integrazione self-hosted. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).
- Installare la **libreria SAP NetWeaver** nel computer del runtime di integrazione. È possibile ottenere la libreria SAP Netweaver dal proprio amministratore SAP o direttamente dall'[area per il download di software SAP](https://support.sap.com/swdc). Per ottenere il percorso di download della versione più recente, cercare **SAP Note #1025361**. Assicurarsi di selezionare la libreria SAP NetWeaver a **64 bit**, che corrisponde all'installazione del runtime di integrazione in uso. Installare quindi tutti i file inclusi in SAP NetWeaver RFC SDK in base alla nota SAP. La libreria SAP NetWeaver è inclusa anche nell'installazione degli strumenti client SAP.

> [!TIP]
> Inserire le DLL estratte dall'SDK di NetWeaver RFC nella cartella system32.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore SAP Business Warehouse.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di SAP Business Warehouse (BW) sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **SapBw** | Sì |
| server | Nome del server in cui si trova l'istanza di SAP BW. | Sì |
| systemNumber | Numero del sistema SAP BW.<br/>Valore consentito: numero decimale a due cifre rappresentato come stringa. | Sì |
| clientId | ID del client nel sistema SAP BW.<br/>Valore consentito: numero decimale a tre cifre rappresentato come stringa. | Sì |
| userName | Nome dell'utente che ha accesso al server SAP. | Sì |
| password | Password per l'utente. Contrassegnare questo campo come SecureString. | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È necessario un runtime di integrazione self-hosted come indicato in [Prerequisiti](#prerequisites). |Sì |

**Esempio:**

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati SAP BW.

Per copiare dati da SAP BW, impostare la proprietà type del set di dati su **RelationalTable**. Il set di dati SAP BW di tipo RelationalTable non supporta alcuna proprietà specifica del tipo.

**Esempio:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW come origine

Per copiare dati da SAP BW, impostare il tipo di origine nell'attività di copia su **RelationalSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su: **RelationalSource** | Sì |
| query | Specifica la query MDX che consente di leggere i dati dall'istanza di SAP BW. | Sì |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromSAPBW",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW input dataset name>",
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
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw"></a>Mapping dei tipi di dati per SAP BW

Quando si copiano dati da SAP BW, vengono usati i mapping seguenti tra i tipi di dati di SAP BW e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di SAP BW | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| ACCP | int |
| CHAR | string |
| CLNT | string |
| CURR | Decimale |
| CUKY | string |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | int |
| LANG | string |
| LCHR | string |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | string |
| UNITÀ | string |
| DATS | string |
| NUMC | string |
| TIMS | string |


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).