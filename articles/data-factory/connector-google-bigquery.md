---
title: Copiare dati da Google BigQuery usando Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da Google BigQuery in archivi dati di sink supportati usando un'attività di copia in una pipeline di data factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: jingwang
ms.openlocfilehash: 955de6cf4b17f1df1e545ccc196856c7c898edfe
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/12/2018
---
# <a name="copy-data-from-google-bigquery-by-using-azure-data-factory"></a>Copiare dati da Google BigQuery usando Azure Data Factory

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Google BigQuery. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta informazioni generali sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Google BigQuery a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

 Data Factory fornisce un driver predefinito per abilitare la connettività. Non è pertanto necessario installare manualmente un driver per usare questo connettore.

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà usate per definire entità di Data Factory specifiche per il connettore Google BigQuery.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato Google BigQuery sono supportate le proprietà seguenti.

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **GoogleBigQuery**. | Sì |
| project | ID di progetto del progetto BigQuery su cui eseguire query.  | Sì |
| additionalProjects | A elenco delimitato da virgole di ID di progetto dei progetti BigQuery a cui accedere.  | No  |
| requestGoogleDriveScope | Indica se richiedere l'accesso a Google Drive. L'abilitazione dell'accesso a Google Drive consente di abilitare il supporto per le tabelle federate che combinano dati di BigQuery con dati da Google Drive. Il valore predefinito è **false**.  | No  |
| authenticationType | Meccanismo di autenticazione OAuth 2.0 usato per l'autenticazione. È possibile usare ServiceAuthentication solo sul runtime di integrazione self-hosted. <br/>I valori consentiti sono **UserAuthentication** e **ServiceAuthentication**. Fare riferimento alle sezioni sotto questa tabella per altre proprietà e altri esempi JSON per questi tipi di autenticazione. | Sì |

### <a name="using-user-authentication"></a>Uso dell'autenticazione utente

Impostare la proprietà "authenticationType" su **UserAuthentication** e specificare le proprietà seguenti insieme alle proprietà generiche descritte nella precedente sezione:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| clientId | ID dell'applicazione usata per generare il token di aggiornamento. | No  |
| clientSecret | Segreto dell'applicazione usata per generare il token di aggiornamento. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No  |
| refreshToken | Token di aggiornamento ottenuto da Google e usato per autorizzare l'accesso a BigQuery. Informazioni su come ottenerne uno dal [i token di accesso OAuth 2.0](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens). Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No  |

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
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Uso dell'autenticazione del servizio

Impostare la proprietà "authenticationType" su **ServiceAuthentication** e specificare le proprietà seguenti insieme alle proprietà generiche descritte nella precedente sezione. È possibile usare questo tipo di autenticazione solo sul runtime di integrazione self-hosted.

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| email | ID di posta elettronica dell'account del servizio usato per ServiceAuthentication. Può essere usato solo sul runtime di integrazione self-hosted.  | No  |
| keyFilePath | Percorso completo per il file di chiave con estensione p12 usato per autenticare l'indirizzo di posta elettronica dell'account del servizio. | No  |
| trustedCertPath | Percorso completo del file PEM contenente i certificati CA attendibili usati per verificare il server in caso di connessione tramite SSL. Questa proprietà può essere impostata solo quando si usa SSL nel runtime di integrazione self-hosted. Il valore predefinito è il file cacerts.pem installato con il runtime di integrazione.  | No  |
| useSystemTrustStore | Specifica se usare o meno un certificato CA dall'archivio di attendibilità di sistema o da un file PEM specificato. Il valore predefinito è **false**.  | No  |

**Esempio:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
} 
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere l'articolo [Set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati di Google BigQuery.

Per copiare dati da Google BigQuery, impostare la proprietà type del set di dati su **GoogleBigQueryObject**. Non sono presenti proprietà aggiuntive specifiche del tipo in questo tipo di set di dati.

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dal tipo di origine Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource come tipo di origine

Per copiare dati da Google BigQuery, impostare il tipo di origine nell'attività di copia su **GoogleBigQuerySource**. Nella sezione **source** dell'attività di copia sono supportate le proprietà seguenti.

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **GoogleBigQuerySource**. | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Un esempio è `"SELECT * FROM MyTable"`. | Sì |

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
Per un elenco degli archivi dati supportati come origini e sink dall'attività di copia in Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
