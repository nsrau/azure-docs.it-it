---
title: Copiare i dati da Xero usando Azure Data Factory (Beta) | Documenti Microsoft
description: "Informazioni su come copiare i dati da Xero agli archivi dati sink supportati utilizzando un'attività di copia in una pipeline di Data Factory di Azure."
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
ms.openlocfilehash: b7ef7f350c8d1f89f76bc524cf348c8ec3e4b6ff
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-xero-using-azure-data-factory-beta"></a>Copiare i dati da Xero usando Azure Data Factory (Beta)

In questo articolo viene descritto come utilizzare l'attività di copia in Azure Data Factory per copiare i dati da Xero. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Questo connettore è attualmente in versione Beta. È possibile provarlo e fornire commenti e suggerimenti. Non utilizzarlo in ambienti di produzione.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Xero a qualsiasi archivio dati sink supportati. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti forniscono dettagli sulle proprietà che consentono di definire entità Data Factory specifica al connettore di Xero.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Le proprietà seguenti sono supportate per il servizio di Xero collegato:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà di tipo deve essere impostata su: **Xero** | Sì |
| host | L'endpoint del server di Xero. (vale a dire api.xero.com)  | Sì |
| consumerKey | Il codice del cliente associato all'applicazione di Xero. È possibile scegliere contrassegnare questo campo come SecureString per archiviare in modo sicuro in Data Factory o archiviare le password nell'insieme di credenziali chiave di Azure e consentire l'attività di copia pull da tale posizione quando si esegue una copia dei dati - ulteriori da [archiviare le credenziali nell'insieme di credenziali chiave](store-credentials-in-key-vault.md). | Sì |
| privateKey | La chiave privata dal file con estensione PEM che è stato generato per l'applicazione di Xero privata. Includere tutto il testo dal file con estensione PEM, incluso il endings(\n) riga Unix. È possibile scegliere contrassegnare questo campo come SecureString per archiviare in modo sicuro in Data Factory o archiviare le password nell'insieme di credenziali chiave di Azure e consentire l'attività di copia pull da tale posizione quando si esegue una copia dei dati - ulteriori da [archiviare le credenziali nell'insieme di credenziali chiave](store-credentials-in-key-vault.md). | Sì |
| useEncryptedEndpoints | Specifica se gli endpoint di origine dati vengono crittografati tramite HTTPS. Il valore predefinito è true.  | No  |
| useHostVerification | Specifica se il nome host è necessaria nel certificato del server in modo che corrisponda il nome host del server quando ci si connette tramite SSL. Il valore predefinito è true.  | No  |
| usePeerVerification | Specifica se verificare l'identità del server quando ci si connette tramite SSL. Il valore predefinito è true.  | No  |

**Esempio:**

```json
{
    "name": "XeroLinkedService",
    "properties": {
        "type": "Xero",
        "typeProperties": {
            "host" : "api.xero.com",
            "consumerKey": {
                 "type": "SecureString",
                 "value": "<consumerKey>"
            },
            "privateKey": {
                 "type": "SecureString",
                 "value": "<privateKey>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). In questa sezione fornisce un elenco delle proprietà supportate dal set di dati di Xero.

Per copiare dati da Xero, impostare la proprietà del tipo di set di dati da **XeroObject**. Non vi è alcuna proprietà aggiuntive specifiche del tipo in questo tipo di set di dati.

**Esempio**

```json
{
    "name": "XeroDataset",
    "properties": {
        "type": "XeroObject",
        "linkedServiceName": {
            "referenceName": "<Xero linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione fornisce un elenco di proprietà supportati dall'origine di Xero.

### <a name="xerosource-as-source"></a>XeroSource come origine

Per copiare dati da Xero, impostare il tipo di origine in attività di copia per **XeroSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | Impostare la proprietà del tipo di origine dell'attività di copia: **XeroSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM Contacts"`. | Sì |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromXero",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Xero input dataset name>",
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
                "type": "XeroSource",
                "query": "SELECT * FROM Contacts"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi di dati supportati dall'attività di copia, vedere [supportati archivi dati](copy-activity-overview.md#supported-data-stores-and-formats).
