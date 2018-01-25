---
title: Copiare dati da HBase usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come copiare dati da HBase in archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory."
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
ms.openlocfilehash: ea2258b953925116f759655583d9601c5a55db7c
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/19/2018
---
# <a name="copy-data-from-hbase-using-azure-data-factory"></a>Copiare dati da HBase usando Azure Data Factory 

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da HBase. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da HBase a qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started-2](../../includes/data-factory-v2-connector-get-started-2.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore HBase.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato HBase sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **HBase** | Sì |
| host | Indirizzo IP o nome host del server HBase, ovvero 192.168.222.160.  | Sì |
| port | Porta TCP che l'istanza di HBase usa per l'ascolto delle connessioni client. Il valore predefinito è 9090.  | No  |
| httpPath | URL parziale corrispondente al server HBase. (/gateway/sandbox/hbase/version)  | No  |
| authenticationType | Meccanismo di autenticazione da usare per la connessione al server HBase. <br/>I valori consentiti sono: **Anonima**, **Di base** | Sì |
| username | Nome utente usato per connettersi all'istanza di HBase.  | No  |
| password | Password corrispondente al nome utente. È possibile scegliere di contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory o archiviare la password in Azure Key Vault e consentire all'attività di copia di eseguire il pull da tale posizione durante l'esecuzione della copia dei dati. Per altre informazioni, vedere [Archiviare le credenziali in Azure Key Vault](store-credentials-in-key-vault.md). | No  |
| enableSsl | Specifica se le connessioni al server sono crittografate tramite SSL. Il valore predefinito è False.  | No  |
| trustedCertPath | Percorso completo del file PEM contenente i certificati CA attendibili per la verifica del server in caso di connessione tramite SSL. È possibile impostare questa proprietà solo quando si usa SSL nel runtime di integrazione self-hosted. Il valore predefinito è il file cacerts.pem installato con il runtime di integrazione.  | No  |
| allowHostNameCNMismatch | Specifica se è necessario che il nome del certificato SSL rilasciato dall'Autorità di certificazione corrisponda al nome host del server per la connessione tramite SSL. Il valore predefinito è False.  | No  |
| allowSelfSignedServerCert | Specifica se consentire o meno i certificati autofirmati dal server. Il valore predefinito è False.  | No  |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione self-hosted o il runtime di integrazione di Azure (se l'archivio dati è accessibile pubblicamente). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

**Esempio:**

```json
{
    "name": "HBaseLinkedService",
    "properties": {
        "type": "HBase",
        "typeProperties": {
            "host" : "<host>",
            "port" : "<port>",
            "httpPath" : "/gateway/sandbox/hbase/version",
            "authenticationType" : "Basic",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "enableSsl" : true,
            "trustedCertPath" : "<trustedCertPath>",
            "allowHostNameCNMismatch" : true,
            "allowSelfSignedServerCert" : true
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati HBase.

Per copiare dati da HBase, impostare la proprietà type del set di dati su **HBaseObject**. Non sono presenti proprietà aggiuntive specifiche del tipo in questo tipo di set di dati.

**Esempio**

```json
{
    "name": "HBaseDataset",
    "properties": {
        "type": "HBaseObject",
        "linkedServiceName": {
            "referenceName": "<HBase linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine HBase.

### <a name="hbasesource-as-source"></a>HBaseSource come origine

Per copiare dati da HBase, impostare il tipo di origine nell'attività di copia su **HBaseSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **HBaseSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | Sì |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromHBase",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HBase input dataset name>",
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
                "type": "HBaseSource",
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
