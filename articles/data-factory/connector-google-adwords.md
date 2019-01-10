---
title: Copiare i dati da Google AdWords usando Azure Data Factory (Anteprima) | Microsoft Docs
description: Informazioni su come copiare dati da Google AdWords negli archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: jingwang
ms.openlocfilehash: b712b576e1dd47698de66889d4edf9dda026a16c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54017991"
---
# <a name="copy-data-from-google-adwords-using-azure-data-factory-preview"></a>Copiare i dati da Google AdWords usando Azure Data Factory (Anteprima)

Questo articolo illustra come usare l'attività di copia di Azure Data Factory per copiare dati da Google AdWords. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!IMPORTANT]
> Questo connettore è attualmente disponibile in anteprima. È possibile provarlo e inviare commenti e suggerimenti. Se si vuole accettare una dipendenza dai connettori in versione di anteprima nella propria soluzione, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Google AdWords a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità specifiche di Data Factory per il connettore Google AdWords.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato a Google AdWords sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **GoogleAdWords** | Yes |
| clientCustomerID | L'ID cliente Client dell'account AdWords di cui si desidera recuperare i dati del report.  | Yes |
| developerToken | Il token degli sviluppatori associato all'account di gestione che si usa per concedere l'accesso all'API AdWords.  È possibile scegliere di contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in ADF o archiviare la password in Azure Key Vault e consentire all'attività di copia di ADF di eseguire il pull da tale posizione durante l'esecuzione della copia dei dati. Per altre informazioni, vedere [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| authenticationType | Meccanismo di autenticazione OAuth 2.0 usato per l'autenticazione. È possibile usare ServiceAuthentication solo su runtime di integrazione self-hosted. <br/>I valori consentiti sono i seguenti: **ServiceAuthentication**, **UserAuthentication** | Yes |
| refreshToken | Il token di aggiornamento ottenuto da Google per autorizzare l'accesso a AdWords per UserAuthentication. È possibile scegliere di contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in ADF o archiviare la password in Azure Key Vault e consentire all'attività di copia di ADF di eseguire il pull da tale posizione durante l'esecuzione della copia dei dati. Per altre informazioni, vedere [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | No  |
| clientId | L'id client dell'applicazione google usato per acquisire il token di aggiornamento. È possibile scegliere di contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in ADF o archiviare la password in Azure Key Vault e consentire all'attività di copia di ADF di eseguire il pull da tale posizione durante l'esecuzione della copia dei dati. Per altre informazioni, vedere [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | No  |
| clientSecret | Il segreto client dell'applicazione google usata per acquisire il token di aggiornamento. È possibile scegliere di contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in ADF o archiviare la password in Azure Key Vault e consentire all'attività di copia di ADF di eseguire il pull da tale posizione durante l'esecuzione della copia dei dati. Per altre informazioni, vedere [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | No  |
| email | ID di posta elettronica dell'account del servizio usato per ServiceAuthentication. Può essere usato solo su runtime di integrazione self-hosted.  | No  |
| keyFilePath | Percorso completo per il file con estensione p12 usato per autenticare l'indirizzo di posta elettronica dell'account del servizio. Può essere usato solo su runtime di integrazione self-hosted.  | No  |
| trustedCertPath | Percorso completo del file PEM contenente i certificati CA attendibili per la verifica del server in caso di connessione tramite SSL. È possibile impostare questa proprietà solo quando si usa SSL nel runtime di integrazione self-hosted. Il valore predefinito è il file cacerts.pem installato con il runtime di integrazione.  | No  |
| useSystemTrustStore | Specifica se usare o meno un certificato della CA dall'archivio di scopi consentiti o da un file .pem specificato. Il valore predefinito è False.  | No  |

**Esempio:**

```json
{
    "name": "GoogleAdWordsLinkedService",
    "properties": {
        "type": "GoogleAdWords",
        "typeProperties": {
            "clientCustomerID" : "<clientCustomerID>",
            "developerToken": {
                 "type": "SecureString",
                 "value": "<developerToken>"
            },
            "authenticationType" : "ServiceAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refreshToken>"
            },
            "clientId": {
                 "type": "SecureString",
                 "value": "<clientId>"
            },
            "clientSecret": {
                 "type": "SecureString",
                 "value": "<clientSecret>"
            },
            "email" : "<email>",
            "keyFilePath" : "<keyFilePath>",
            "trustedCertPath" : "<trustedCertPath>",
            "useSystemTrustStore" : true,
        }
    }
}

```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati di Google AdWords.

Per copiare dati da Google AdWords, impostare la proprietà tipo del set di dati su **GoogleAdWordsObject**. Sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **GoogleAdWordsObject** | Yes |
| tableName | Nome della tabella. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "GoogleAdWordsDataset",
    "properties": {
        "type": "GoogleAdWordsObject",
        "linkedServiceName": {
            "referenceName": "<GoogleAdWords linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}

```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine di Google AdWords.

### <a name="google-adwords-as-source"></a>Google AdWords come origine

Per copiare dati da Google AdWords, impostare il tipo di origine nell'attività di copia su **GoogleAdWordsSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine di attività di copia deve essere impostata su: **GoogleAdWordsSource** | Yes |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromGoogleAdWords",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleAdWords input dataset name>",
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
                "type": "GoogleAdWordsSource",
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
