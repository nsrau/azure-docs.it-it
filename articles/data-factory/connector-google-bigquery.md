---
title: Copiare dati da Google BigQuery usando Azure Data Factory (beta) | Microsoft Docs
description: "Informazioni su come copiare dati da Google BigQuery in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory."
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
ms.date: 01/05/2018
ms.author: jingwang
ms.openlocfilehash: 10e7687448f4401864e06545447ed4c46e5552be
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-google-bigquery-using-azure-data-factory-beta"></a>Copiare dati da Google BigQuery usando Azure Data Factory (beta)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Google BigQuery. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Questo connettore è attualmente disponibile in versione Beta. È possibile provarlo e inviare commenti e suggerimenti. Non usarlo in ambienti di produzione.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Google BigQuery a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Google BigQuery connector.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Google BigQuery sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **GoogleBigQuery** | Sì |
| project | ID di progetto del progetto BigQuery su cui eseguire query.  | Sì |
| additionalProjects | A elenco delimitato da virgole di ID di progetto dei progetti BigQuery a cui accedere.  | No |
| requestGoogleDriveScope | Indica se richiedere l'accesso a Google Drive. L'abilitazione dell'accesso a Google Drive consente di abilitare il supporto per le tabelle federate che combinano dati di BigQuery con dati da Google Drive. Il valore predefinito è False.  | No |
| authenticationType | Meccanismo di autenticazione OAuth 2.0 usato per l'autenticazione. È possibile usare ServiceAuthentication solo su runtime di integrazione self-hosted. <br/>I valori consentiti sono **ServiceAuthentication**, **UserAuthentication** | Sì |
| refreshToken | Token di aggiornamento ottenuto da Google per autorizzare l'accesso a BigQuery per UserAuthentication. È possibile scegliere di contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory o archiviare la password in Azure Key Vault e consentire all'attività di copia di eseguire il pull da tale posizione durante l'esecuzione della copia dei dati. Per altre informazioni, vedere [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| posta elettronica | ID di posta elettronica dell'account del servizio usato per ServiceAuthentication. Può essere usato solo su runtime di integrazione self-hosted.  | No |
| keyFilePath | Percorso completo per il file con estensione p12 usato per autenticare l'indirizzo di posta elettronica dell'account del servizio. Può essere usato solo su runtime di integrazione self-hosted.  | No |
| trustedCertPath | Percorso completo del file PEM contenente i certificati CA attendibili per la verifica del server in caso di connessione tramite SSL. È possibile impostare questa proprietà solo quando si usa SSL nel runtime di integrazione self-hosted. Il valore predefinito è il file cacerts.pem installato con il runtime di integrazione.  | No |
| useSystemTrustStore | Specifica se usare o meno un certificato CA da un archivio di scopi consentiti o da un file PEM specificato. Il valore predefinito è False.  | No |

**Esempio:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati di Google BigQuery.

Per copiare dati da Google BigQuery, impostare la proprietà type del set di dati su **GoogleBigQueryObject**. Non sono presenti proprietà aggiuntive specifiche per il tipo in questo tipo di set di dati.

**Esempio**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Google BigQuery.

### <a name="googlebigquerysource-as-source"></a>GoogleBigQuerySource come origine

Per copiare dati da Google BigQuery, impostare il tipo di origine nell'attività di copia su **GoogleBigQuerySource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **GoogleBigQuerySource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | Sì |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
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
