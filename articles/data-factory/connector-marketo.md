---
title: Copiare i dati da Marketo usando Azure Data Factory (Beta) | Documenti Microsoft
description: "Informazioni su come copiare i dati da Marketo agli archivi dati sink supportati utilizzando un'attività di copia in una pipeline di Data Factory di Azure."
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
ms.date: 12/15/2017
ms.author: jingwang
ms.openlocfilehash: 80c377938005ba699c8632003b215bf2c52b6c33
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/19/2017
---
# <a name="copy-data-from-marketo-using-azure-data-factory-beta"></a>Copiare i dati da Marketo usando Azure Data Factory (Beta)

In questo articolo viene descritto come utilizzare l'attività di copia in Azure Data Factory per copiare dati da Marketo. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Questo connettore è attualmente in versione Beta. È possibile provarlo e fornire commenti e suggerimenti. Non utilizzarlo in ambienti di produzione.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Marketo per qualsiasi archivio dati sink supportati. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti forniscono dettagli sulle proprietà che consentono di definire entità Data Factory specifica al connettore di Marketo.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Le proprietà seguenti sono supportate per il servizio collegato di Marketo:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà di tipo deve essere impostata su: **Marketo** | Sì |
| endpoint | L'endpoint del server di Marketo. (ad esempio 123-ABC-321.mktorest.com)  | Sì |
| clientId | L'Id del servizio Marketo del client.  | Sì |
| clientSecret | Segreto del client del servizio di Marketo. È possibile scegliere di contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in ADF o archiviare la password in Azure Key Vault e consentire all'attività di copia di ADF di eseguire il pull da tale posizione durante l'esecuzione della copia dei dati. Per altre informazioni, vedere [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| useEncryptedEndpoints | Specifica se gli endpoint di origine dati vengono crittografati tramite HTTPS. Il valore predefinito è true.  | No  |
| useHostVerification | Specifica se richiedere il nome host nel certificato del server in modo che corrisponda il nome host del server quando ci si connette tramite SSL. Il valore predefinito è true.  | No  |
| usePeerVerification | Specifica se verificare l'identità del server quando ci si connette tramite SSL. Il valore predefinito è true.  | No  |

**Esempio:**

```json
{
    "name": "MarketoLinkedService",
    "properties": {
        "type": "Marketo",
        "typeProperties": {
            "endpoint" : "123-ABC-321.mktorest.com",
            "clientId" : "<clientId>",
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). In questa sezione fornisce un elenco delle proprietà supportate dal set di dati Marketo.

Per copiare dati da Marketo, impostare la proprietà del tipo di set di dati da **MarketoObject**. Non vi è alcuna proprietà aggiuntive specifiche del tipo in questo tipo di set di dati.

**Esempio**

```json
{
    "name": "MarketoDataset",
    "properties": {
        "type": "MarketoObject",
        "linkedServiceName": {
            "referenceName": "<Marketo linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione fornisce un elenco di proprietà supportati dall'origine di Marketo.

### <a name="marketosource-as-source"></a>MarketoSource come origine

Per copiare dati da Marketo, impostare il tipo di origine in attività di copia per **MarketoSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | Impostare la proprietà del tipo di origine dell'attività di copia: **MarketoSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM Activitiy_Types"`. | Sì |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromMarketo",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Marketo input dataset name>",
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
                "type": "MarketoSource",
                "query": "SELECT top 1000 * FROM Activitiy_Types"
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
