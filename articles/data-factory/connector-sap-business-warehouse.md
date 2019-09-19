---
title: Copiare dati da SAP BW usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da SAP Business Warehouse in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: e53cb822d1100b3d13a96c9f86aee6db420e4bb1
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71089600"
---
# <a name="copy-data-from-sap-business-warehouse-using-azure-data-factory"></a>Copiare dati da SAP Business Warehouse usando Azure Data Factory
> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](v1/data-factory-sap-business-warehouse-connector.md)
> * [Versione corrente](connector-sap-business-warehouse.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un database SAP Business Warehouse (BW). Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

>[!TIP]
>Per informazioni sul supporto generale di ADF sullo scenario di integrazione dei dati SAP, vedere l'articolo relativo all' [integrazione dei dati SAP con Azure Data Factory whitepaper](https://github.com/Azure/Azure-DataFactory/blob/master/whitepaper/SAP%20Data%20Integration%20using%20Azure%20Data%20Factory.pdf) con informazioni dettagliate introduttive, comparsing e linee guida.

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore SAP Business Warehouse è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine/sink supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da SAP Business Warehouse in qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore SAP Business Warehouse supporta:

- SAP Business Warehouse **versione 7.x**.
- La copia di dati da **InfoCube e QueryCube** (incluse query BEx) tramite query MDX.
- La copia di dati usando l'autenticazione di base.

## <a name="prerequisites"></a>Prerequisiti

Per usare il connettore SAP Business Warehouse, è necessario:

- Configurare un runtime di integrazione self-hosted. Per i dettagli, vedere l'articolo [Runtime di integrazione self-hosted](create-self-hosted-integration-runtime.md).
- Installare la **libreria SAP NetWeaver** nel computer del runtime di integrazione. È possibile ottenere la libreria SAP Netweaver dal proprio amministratore SAP o direttamente dall'[area per il download di software SAP](https://support.sap.com/swdc). Per ottenere il percorso di download della versione più recente, cercare **SAP Note #1025361**. Assicurarsi di selezionare la libreria SAP NetWeaver a **64 bit**, che corrisponde all'installazione del runtime di integrazione in uso. Installare quindi tutti i file inclusi in SAP NetWeaver RFC SDK in base alla nota SAP. La libreria SAP NetWeaver è inclusa anche nell'installazione degli strumenti client SAP.

>[!TIP]
>Per risolvere un problema di connettività a SAP BW, assicurarsi che:
>- Tutte le librerie di dipendenza estratte da NetWeaver RFC SDK siano disponibili nella cartella %windir%\system32. In genere la cartella contiene icudt34.dll, icuin34.dll, icuuc34.dll, libicudecnumber.dll, librfc32.dll, libsapucum.dll, sapcrypto.dll, sapcryto_old.dll, sapnwrfc.dll.
>- Le porte necessarie per la connessione al server SAP siano abilitate nel computer del runtime di integrazione self-hosted. In genere sono le porte 3300 e 3201.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore SAP Business Warehouse.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di SAP Business Warehouse (BW) sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **SapBw** | Sì |
| server | Nome del server in cui si trova l'istanza di SAP BW. | Sì |
| systemNumber | Numero del sistema SAP BW.<br/>Valore consentito: numero decimale a due cifre rappresentato come stringa. | Sì |
| clientId | ID del client nel sistema SAP BW.<br/>Valore consentito: numero decimale a tre cifre rappresentato come stringa. | Sì |
| userName | Nome dell'utente che ha accesso al server SAP. | Sì |
| password | Password per l'utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
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

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati SAP BW.

Per copiare dati da SAP BW, impostare la proprietà Type del set di dati su **SapBwCube**. Il set di dati SAP BW di tipo RelationalTable non supporta alcuna proprietà specifica del tipo.

**Esempio:**

```json
{
    "name": "SAPBWDataset",
    "properties": {
        "type": "SapBwCube",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

Se si usa `RelationalTable` un set di dati tipizzato, è ancora supportato così com'è, mentre si consiglia di usare quello nuovo in futuro.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine SAP BW.

### <a name="sap-bw-as-source"></a>SAP BW come origine

Per copiare dati da SAP BW, nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine di attività di copia deve essere impostata su: **SapBwSource** | Yes |
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
                "type": "SapBwSource",
                "query": "<MDX query for SAP BW>"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Se si usa `RelationalSource` l'origine tipizzata, questo è ancora supportato così com'è, mentre si consiglia di usare quello nuovo in futuro.

## <a name="data-type-mapping-for-sap-bw"></a>Mapping dei tipi di dati per SAP BW

Quando si copiano dati da SAP BW, vengono usati i mapping seguenti tra i tipi di dati di SAP BW e i tipi di dati provvisori di Azure Data Factory. Vedere [Mapping dello schema e del tipo di dati](copy-activity-schema-and-type-mapping.md) per informazioni su come l'attività di copia esegue il mapping dello schema di origine e del tipo di dati al sink.

| Tipo di dati di SAP BW | Tipo di dati provvisori di Data Factory |
|:--- |:--- |
| ACCP | Int |
| CHAR | String |
| CLNT | String |
| CURR | Decimal |
| CUKY | String |
| DEC | Decimal |
| FLTP | Double |
| INT1 | Byte |
| INT2 | Int16 |
| INT4 | Int |
| LANG | String |
| LCHR | String |
| LRAW | Byte[] |
| PREC | Int16 |
| QUAN | Decimal |
| RAW | Byte[] |
| RAWSTRING | Byte[] |
| STRING | String |
| UNIT | String |
| DATS | String |
| NUMC | String |
| TIMS | String |


## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
