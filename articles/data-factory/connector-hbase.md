---
title: Copiare i dati da Data Factory di Azure tramite | Documenti Microsoft
description: "Informazioni su come copiare i dati da HBase agli archivi dati sink supportati utilizzando un'attività di copia in una pipeline di Data Factory di Azure."
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
ms.openlocfilehash: 72a4a82ab16ddc290a911428f95ddbf851595721
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-hbase-using-azure-data-factory"></a>Copiare i dati tramite Data Factory di Azure 

In questo articolo viene descritto come utilizzare l'attività di copia in Azure Data Factory per copiare i dati da HBase. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da HBase per qualsiasi archivio dati sink supportati. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti forniscono dettagli sulle proprietà che consentono di definire entità Data Factory specifica al connettore di HBase.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Le proprietà seguenti sono supportate per il servizio collegato di HBase:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà di tipo deve essere impostata su: **HBase** | Sì |
| host | L'indirizzo IP o host nome del server di HBase. (ad esempio 192.168.222.160)  | Sì |
| port | La porta TCP utilizzata dall'istanza di HBase in ascolto per le connessioni client. Il valore predefinito è 9090.  | No  |
| httpPath | URL parziale corrispondente al server di HBase. (ad esempio /gateway/sandbox/hbase/version)  | No  |
| authenticationType | Il meccanismo di autenticazione da utilizzare per connettersi al server di HBase. <br/>I valori consentiti sono: **anonimo**, **base** | Sì |
| username | Il nome utente utilizzato per connettersi all'istanza di HBase.  | No  |
| password | La password corrispondente al nome utente. È possibile scegliere contrassegnare questo campo come SecureString per archiviare in modo sicuro in ADF o archiviare le password nell'insieme di credenziali chiave di Azure e consentire l'attività di copia pull da tale posizione quando si esegue una copia dei dati - ulteriori da [archiviare le credenziali nell'insieme di credenziali chiave](store-credentials-in-key-vault.md). | No  |
| enableSsl | Specifica se le connessioni al server vengono crittografate tramite SSL. Il valore predefinito è False.  | No  |
| trustedCertPath | Il percorso completo del file con estensione PEM contenente i certificati CA attendibili per la verifica del server quando ci si connette tramite SSL. Questa proprietà può essere impostata solo quando si utilizza SSL su infrarossi self-hosted. Il valore predefinito è il file cacerts.pem installato con l'infrarossi.  | No  |
| allowHostNameCNMismatch | Specifica se è richiesto un nome certificato SSL rilasciato dalla CA in modo che corrisponda il nome host del server quando ci si connette tramite SSL. Il valore predefinito è False.  | No  |
| allowSelfSignedServerCert | Specifica se consentire i certificati autofirmati dal server. Il valore predefinito è False.  | No  |
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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). In questa sezione fornisce un elenco delle proprietà supportate dal set di dati di HBase.

Per copiare dati da HBase, impostare la proprietà del tipo di set di dati da **HBaseObject**. Non vi è alcuna proprietà aggiuntive specifiche del tipo in questo tipo di set di dati.

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione fornisce un elenco di proprietà supportati dall'origine di HBase.

### <a name="hbasesource-as-source"></a>HBaseSource come origine

Per copiare dati da HBase, impostare il tipo di origine in attività di copia per **HBaseSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | Impostare la proprietà del tipo di origine dell'attività di copia: **HBaseSource** | Sì |
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
