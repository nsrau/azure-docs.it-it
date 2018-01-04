---
title: Copiare i dati dall'Hive con Data Factory di Azure | Documenti Microsoft
description: "Informazioni su come copiare i dati dall'Hive agli archivi dati sink supportati utilizzando un'attività di copia in una pipeline di Data Factory di Azure."
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
ms.openlocfilehash: af9050d41502f55b0426b858654b8af6985b93ca
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="copy-data-from-hive-using-azure-data-factory"></a>Copiare i dati dall'Hive con Data Factory di Azure 

In questo articolo viene descritto come utilizzare l'attività di copia in Azure Data Factory per copiare i dati dall'Hive. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Attività di copia nella versione 1](v1/data-factory-data-movement-activities.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare i dati dall'Hive per qualsiasi archivio dati sink supportati. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

È possibile creare una pipeline con l'attività di copia usando .NET SDK, Python SDK, Azure PowerShell, l'API REST o il modello Azure Resource Manager. Vedere l'[esercitazione sull'attività di copia](quickstart-create-data-factory-dot-net.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Le sezioni seguenti forniscono dettagli sulle proprietà che consentono di definire entità Data Factory specifica al connettore Hive.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Le proprietà seguenti sono supportate per il servizio collegato Hive:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà di tipo deve essere impostata su: **Hive** | Sì |
| host | Indirizzo IP o host nome del server di Hive, separati da ';' per più host (solo quando serviceDiscoveryMode è attiva).  | Sì |
| port | La porta TCP utilizzato dal server di Hive per l'ascolto per le connessioni client.  | No  |
| serverType | Il tipo di server Hive. <br/>I valori consentiti sono: **HiveServer1**, **HiveServer2**, **HiveThriftServer** | No  |
| thriftTransportProtocol | Il protocollo di trasporto da usare nel livello Thrift. <br/>I valori consentiti sono: **binario**, **SASL**, * * HTTP * * | No  |
| authenticationType | Il metodo di autenticazione utilizzato per accedere al server Hive. <br/>I valori consentiti sono: **anonimo**, **Username**, **UsernameAndPassword**, **WindowsAzureHDInsightService** | Sì |
| serviceDiscoveryMode | true per indicare l'utilizzo del servizio ZooKeeper, false non.  | No  |
| zooKeeperNameSpace | Lo spazio dei nomi in ZooKeeper in quale Server Hive 2 vengono aggiunti i nodi.  | No  |
| useNativeQuery | Specifica se il driver utilizza le query HiveQL native o li converte in un formato equivalente in HiveQL.  | No  |
| username | Il nome utente utilizzato per accedere a Server Hive.  | No  |
| password | La password corrispondente al nome utente fornito dall'utente nel campo nome utente è possibile scegliere di contrassegnare questo campo come SecureString per archiviarlo in modo protetto nel file ADF, o archiviare password nell'insieme di credenziali chiave di Azure e consentire il pull di attività di copia da qui quando si esegue la copia dei dati - lea altre RN [archiviare le credenziali nell'insieme di credenziali chiave](store-credentials-in-key-vault.md). | No  |
| httpPath | URL parziale corrispondente al server Hive.  | No  |
| enableSsl | Specifica se le connessioni al server vengono crittografate tramite SSL. Il valore predefinito è False.  | No  |
| trustedCertPath | Il percorso completo del file con estensione PEM contenente i certificati CA attendibili per la verifica del server quando ci si connette tramite SSL. Questa proprietà può essere impostata solo quando si utilizza SSL su infrarossi self-hosted. Il valore predefinito è il file cacerts.pem installato con l'infrarossi.  | No  |
| useSystemTrustStore | Specifica se utilizzare un certificato dall'archivio di attendibilità del sistema o da un file con estensione PEM specificato. Il valore predefinito è False.  | No  |
| allowHostNameCNMismatch | Specifica se è richiesto un nome certificato SSL rilasciato dalla CA in modo che corrisponda il nome host del server quando ci si connette tramite SSL. Il valore predefinito è False.  | No  |
| allowSelfSignedServerCert | Specifica se consentire i certificati autofirmati dal server. Il valore predefinito è False.  | No  |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione self-hosted o il runtime di integrazione di Azure (se l'archivio dati è accessibile pubblicamente). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No  |

**Esempio:**

```json
{
    "name": "HiveLinkedService",
    "properties": {
        "type": "Hive",
        "typeProperties": {
            "host" : "<cluster>.azurehdinsight.net",
            "port" : "<port>",
            "authenticationType" : "WindowsAzureHDInsightService",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "httpPath" : "gateway/sandbox/spark"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). In questa sezione fornisce un elenco delle proprietà supportate dal set di dati Hive.

Per copiare i dati dall'Hive, impostare la proprietà del tipo di set di dati da **HiveObject**. Non vi è alcuna proprietà aggiuntive specifiche del tipo in questo tipo di set di dati.

**Esempio**

```json
{
    "name": "HiveDataset",
    "properties": {
        "type": "HiveObject",
        "linkedServiceName": {
            "referenceName": "<Hive linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). In questa sezione fornisce un elenco di proprietà supportati dall'origine Hive.

### <a name="hivesource-as-source"></a>HiveSource come origine

Per copiare i dati dall'Hive, impostare il tipo di origine in attività di copia per **HiveSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | DESCRIZIONE | Obbligatoria |
|:--- |:--- |:--- |
| type | Impostare la proprietà del tipo di origine dell'attività di copia: **HiveSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | Sì |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromHive",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Hive input dataset name>",
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
                "type": "HiveSource",
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
