---
title: Copiare dati da Presto tramite Azure Data Factory (anteprima) | Microsoft Docs
description: Informazioni su come copiare dati da Presto in archivi dati di sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: jingwang
ms.openlocfilehash: 666df827af9d6af38757b3f13c788d091a2c7c4a
ms.sourcegitcommit: a819209a7c293078ff5377dee266fa76fd20902c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/16/2019
ms.locfileid: "71010597"
---
# <a name="copy-data-from-presto-using-azure-data-factory-preview"></a>Copiare dati da Presto tramite Azure Data Factory (anteprima)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da Presto. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!IMPORTANT]
> Questo connettore è attualmente disponibile in anteprima. È possibile provarlo e inviare commenti e suggerimenti. Se si vuole accettare una dipendenza dai connettori in versione di anteprima nella propria soluzione, contattare il [supporto tecnico di Azure](https://azure.microsoft.com/support/).

## <a name="supported-capabilities"></a>Funzionalità supportate

Questo connettore presto è supportato per le attività seguenti:

- [Attività di copia](copy-activity-overview.md) con [matrice di origine supportata](copy-activity-overview.md)
- [Attività Lookup](control-flow-lookup-activity.md)

È possibile copiare dati da Presto a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory offre un driver predefinito per consentire la connettività, pertanto non è necessario installare manualmente alcun driver usando questo connettore.

## <a name="getting-started"></a>Introduzione

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di Data Factory specifiche per il connettore Presto.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato di Presto sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **Presto** | Yes |
| host | Indirizzo IP o nome host del server Presto, ovvero 192.168.222.160.  | Sì |
| serverVersion | Versione del server Presto, ovvero 0.148-t.  | Yes |
| catalogo | Il contesto del catalogo di tutte le richieste sul server.  | Sì |
| port | Porta TCP che il server Presto usa per l'ascolto delle connessioni client. Il valore predefinito è 8080.  | No |
| authenticationType | Meccanismo di autenticazione usato per la connessione al server Presto. <br/>I valori consentiti sono i seguenti: **Anonimo**, **LDAP** | Sì |
| userName | Nome utente usato per connettersi al server Presto.  | No |
| password | Password corrispondente al nome utente. Contrassegnare questo campo come SecureString per archiviarlo in modo sicuro in Azure Data Factory oppure [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No |
| enableSsl | Specifica se le connessioni al server sono crittografate tramite SSL. Il valore predefinito è False.  | No |
| trustedCertPath | Percorso completo del file PEM contenente i certificati CA attendibili per la verifica del server in caso di connessione tramite SSL. È possibile impostare questa proprietà solo quando si usa SSL nel runtime di integrazione self-hosted. Il valore predefinito è il file cacerts.pem installato con il runtime di integrazione.  | No |
| useSystemTrustStore | Specifica se usare o meno un certificato della CA dall'archivio di scopi consentiti o da un file .pem specificato. Il valore predefinito è False.  | No |
| allowHostNameCNMismatch | Specifica se è necessario che il nome del certificato SSL rilasciato dall'Autorità di certificazione corrisponda al nome host del server per la connessione tramite SSL. Il valore predefinito è False.  | No |
| allowSelfSignedServerCert | Specifica se consentire o meno i certificati autofirmati dal server. Il valore predefinito è False.  | No |
| timeZoneID | Fuso orario locale usato dalla connessione. I valori validi per questa opzione vengono specificati nel database del fuso orario IANA. Il valore predefinito è il fuso orario di sistema.  | No |

**Esempio:**

```json
{
    "name": "PrestoLinkedService",
    "properties": {
        "type": "Presto",
        "typeProperties": {
            "host" : "<host>",
            "serverVersion" : "0.148-t",
            "catalog" : "<catalog>",
            "port" : "<port>",
            "authenticationType" : "LDAP",
            "username" : "<username>",
            "password": {
                 "type": "SecureString",
                 "value": "<password>"
            },
            "timeZoneID" : "Europe/Berlin"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui [set di dati](concepts-datasets-linked-services.md). Questa sezione presenta un elenco delle proprietà supportate dal set di dati Presto.

Per copiare dati da Presto, impostare la proprietà type del set di dati su **PrestoObject**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su: **PrestoObject** | Sì |
| schema | Nome dello schema. |No (se nell'origine dell'attività è specificato "query")  |
| table | Nome della tabella. |No (se nell'origine dell'attività è specificato "query")  |
| tableName | Nome della tabella con schema. Questa proprietà è supportata per compatibilità con le versioni precedenti. Usare `schema` e`table` per il nuovo carico di lavoro. | No (se nell'origine dell'attività è specificato "query") |

**Esempio**

```json
{
    "name": "PrestoDataset",
    "properties": {
        "type": "PrestoObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Presto linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine Presto.

### <a name="presto-as-source"></a>Presto come origine

Per copiare dati da Presto, impostare il tipo di origine nell'attività di copia su **PrestoSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine di attività di copia deve essere impostata su: **PrestoSource** | Sì |
| query | Usare la query SQL personalizzata per leggere i dati. Ad esempio: `"SELECT * FROM MyTable"`. | No (se nel set di dati è specificato "tableName") |

**Esempio:**

```json
"activities":[
    {
        "name": "CopyFromPresto",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Presto input dataset name>",
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
                "type": "PrestoSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Proprietà attività di ricerca

Per informazioni dettagliate sulle proprietà, controllare l' [attività di ricerca](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Passaggi successivi
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
