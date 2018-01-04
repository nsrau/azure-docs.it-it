---
title: Copiare i dati da Couchbase usando Azure Data Factory (Beta) | Documenti Microsoft
description: "Informazioni su come copiare i dati da Couchbase agli archivi dati sink supportati utilizzando un'attività di copia in una pipeline di Data Factory di Azure."
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
ms.date: 11/30/2017
ms.author: jingwang
ms.openlocfilehash: d13314f7d4165442224ff09aee1042f3fdffee85
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-couchbase-using-azure-data-factory-beta"></a>Copiare i dati da Couchbase usando Azure Data Factory (Beta)

In questo articolo viene descritto come utilizzare l'attività di copia in Azure Data Factory per copiare i dati da Couchbase. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Questo connettore è attualmente in versione Beta. È possibile provarlo e fornire commenti e suggerimenti. Non utilizzarlo in ambienti di produzione.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Couchbase per qualsiasi archivio dati sink supportati. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti forniscono dettagli sulle proprietà che consentono di definire entità Data Factory specifica al connettore Couchbase.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Le proprietà seguenti sono supportate per il servizio Couchbase collegato:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà di tipo deve essere impostata su: **Couchbase** | Sì |
| connectionString | Una stringa di connessione ODBC per connettersi a Couchbase. È possibile scegliere contrassegnare questo campo come SecureString per archiviare in modo sicuro in ADF o archiviare le password nell'insieme di credenziali chiave di Azure e consentire l'attività di copia pull da tale posizione quando si esegue una copia dei dati - ulteriori da [archiviare le credenziali nell'insieme di credenziali chiave](store-credentials-in-key-vault.md). | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione self-hosted o il runtime di integrazione di Azure (se l'archivio dati è accessibile pubblicamente). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

**Esempio:**

```json
{
    "name": "CouchbaseLinkedService",
    "properties": {
        "type": "Couchbase",
        "typeProperties": {
            "connectionString": {
                 "type": "SecureString",
                 "value": "Server=<server>; Port=<port>;AuthMech=1;CredString=[{\"user\": \"JSmith\", \"pass\":\"access123\"}, {\"user\": \"Admin\", \"pass\":\"simba123\"}];"
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). In questa sezione fornisce un elenco delle proprietà supportate dal set di dati Couchbase.

Per copiare dati da Couchbase, impostare la proprietà del tipo di set di dati da **CouchbaseTable**. Non vi è alcuna proprietà aggiuntive specifiche del tipo in questo tipo di set di dati.

**Esempio**

```json
{
    "name": "CouchbaseDataset",
    "properties": {
        "type": "CouchbaseTable",
        "linkedServiceName": {
            "referenceName": "<Couchbase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione fornisce un elenco di proprietà supportati dall'origine Couchbase.

### <a name="couchbasesource-as-source"></a>CouchbaseSource come origine

Per copiare dati da Couchbase, impostare il tipo di origine in attività di copia per **CouchbaseSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | Impostare la proprietà del tipo di origine dell'attività di copia: **CouchbaseSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | Sì |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromCouchbase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Couchbase input dataset name>",
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
                "type": "CouchbaseSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
