---
title: Copiare dati da Xero usando Azure Data Factory (beta) | Microsoft Docs
description: "Informazioni su come copiare dati da Xero in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory."
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
ms.openlocfilehash: aa81f9d163da8d9236470c0b797f5430163ed39d
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="copy-data-from-xero-using-azure-data-factory-beta"></a>Copiare dati da Xero usando Azure Data Factory (beta)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da e in Xero. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Questo connettore è attualmente disponibile in versione beta. È possibile provarlo e inviare commenti e suggerimenti. Non usarlo in ambienti di produzione.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Xero a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Xero.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Xero sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **Xero** | Sì |
| host | Endpoint del server Xero (ovvero, api.xero.com).  | Sì |
| consumerKey | Chiave utente associata all'applicazione Xero. È possibile scegliere di contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory o archiviare la password in Azure Key Vault e consentire all'attività di copia di eseguire il pull da tale posizione durante l'esecuzione della copia dei dati. Per altre informazioni, vedere [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| privateKey | Chiave privata dal file con estensione pem generato per l'applicazione Xero privata. Includere tutto il testo dal file con estensione pem, incluse le terminazioni riga Unix (\n). È possibile scegliere di contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Data Factory o archiviare la password in Azure Key Vault e consentire all'attività di copia di eseguire il pull da tale posizione durante l'esecuzione della copia dei dati. Per altre informazioni, vedere [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |
| useEncryptedEndpoints | Specifica se gli endpoint dell'origine dati vengono crittografati tramite HTTPS. Il valore predefinito è true.  | No  |
| useHostVerification | Specifica se è necessario che il nome host nel certificato del server corrisponda al nome host del server per la connessione tramite SSL. Il valore predefinito è true.  | No  |
| usePeerVerification | Specifica se verificare l'identità del server durante la connessione tramite SSL. Il valore predefinito è true.  | No  |

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Xero.

Per copiare dati da Xero, impostare la proprietà type del set di dati su **XeroObject**. Non sono presenti proprietà aggiuntive specifiche del tipo in questo tipo di set di dati.

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Xero.

### <a name="xerosource-as-source"></a>XeroSource come origine

Per copiare dati da Xero, impostare il tipo di origine nell'attività di copia su **XeroSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **XeroSource** | Sì |
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
Per un elenco di archivi dati supportati dall'attività di copia, vedere [Archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
