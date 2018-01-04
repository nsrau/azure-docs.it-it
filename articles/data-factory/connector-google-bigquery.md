---
title: Copiare i dati da Google BigQuery usando Azure Data Factory (Beta) | Documenti Microsoft
description: "Informazioni su come copiare i dati da Google BigQuery agli archivi dati sink supportati utilizzando un'attività di copia in una pipeline di Data Factory di Azure."
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
ms.openlocfilehash: 00962b1bb32ff096712d36c07620505e72667380
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-google-bigquery-using-azure-data-factory-beta"></a>Copiare i dati da Google BigQuery usando Azure Data Factory (Beta)

In questo articolo viene descritto come utilizzare l'attività di copia in Azure Data Factory per copiare i dati da Google BigQuery. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

> [!IMPORTANT]
> Questo connettore è attualmente in versione Beta. È possibile provarlo e fornire commenti e suggerimenti. Non utilizzarlo in ambienti di produzione.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da Google BigQuery per qualsiasi archivio dati sink supportati. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti forniscono dettagli sulle proprietà che consentono di definire entità Data Factory specifica al connettore di Google BigQuery.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Le proprietà seguenti sono supportate per Google BigQuery servizio collegato:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà di tipo deve essere impostata su: **GoogleBigQuery** | Sì |
| Progetto | Il progetto BigQuery predefinito per eseguire query.  | Sì |
| additionalProjects | Un elenco delimitato da virgole di pubblico BigQuery progetti per l'accesso.  | No  |
| requestGoogleDriveScope | Se si desidera richiedere l'accesso all'unità di Google. Consentire l'accesso di Google Drive Abilita il supporto per le tabelle federate che combinano dati BigQuery con i dati dall'unità di Google. Il valore predefinito è False.  | No  |
| authenticationType | Il meccanismo di autenticazione OAuth 2.0 utilizzato per l'autenticazione. ServiceAuthentication può essere utilizzato solo su infrarossi self-hosted. <br/>I valori consentiti sono: **ServiceAuthentication**, **UserAuthentication** | Sì |
| RefreshToken | Il token di aggiornamento ottenuto da Google per autorizzare l'accesso a BigQuery per UserAuthentication. È possibile scegliere contrassegnare questo campo come SecureString per archiviare in modo sicuro in ADF o archiviare le password nell'insieme di credenziali chiave di Azure e consentire l'attività di copia pull da tale posizione quando si esegue una copia dei dati - ulteriori da [archiviare le credenziali nell'insieme di credenziali chiave](store-credentials-in-key-vault.md). | No  |
| email | L'ID di posta elettronica account di servizio utilizzato per ServiceAuthentication e può essere utilizzato solo in IR self-hosted  | No  |
| keyFilePath | Il percorso completo al file di chiave. p12 che viene usato per autenticare l'indirizzo di posta elettronica di account del servizio e può essere utilizzato solo in modo indipendente infrarossi.  | No  |
| trustedCertPath | Il percorso completo del file con estensione PEM contenente i certificati CA attendibili per la verifica del server quando ci si connette tramite SSL. Questa proprietà può essere impostata solo quando si utilizza SSL su infrarossi self-hosted. Il valore predefinito è il file cacerts.pem installato con l'infrarossi.  | No  |
| useSystemTrustStore | Specifica se utilizzare un certificato dall'archivio di attendibilità del sistema o da un file con estensione PEM specificato. Il valore predefinito è False.  | No  |

**Esempio:**

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project>",
            "additionalProjects" : "<additionalProjects>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "refreshToken": {
                 "type": "SecureString",
                 "value": "<refreshToken>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). In questa sezione fornisce un elenco delle proprietà supportate dal set di dati di Google BigQuery.

Per copiare dati da Google BigQuery, impostare la proprietà del tipo di set di dati da **GoogleBigQueryObject**. Non vi è alcuna proprietà aggiuntive specifiche del tipo in questo tipo di set di dati.

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione fornisce un elenco di proprietà supportati dall'origine Google BigQuery.

### <a name="googlebigquerysource-as-source"></a>GoogleBigQuerySource come origine

Per copiare dati da Google BigQuery, impostare il tipo di origine in attività di copia per **GoogleBigQuerySource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | Impostare la proprietà del tipo di origine dell'attività di copia: **GoogleBigQuerySource** | Sì |
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
