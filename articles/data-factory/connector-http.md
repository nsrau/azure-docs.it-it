---
title: Copiare dati da un'origine HTTP tramite Azure Data Factory | Microsoft Docs
description: Informazioni su come copiare dati da un'origine HTTP locale o cloud in archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 87505081f16008dff7da1f567c1265c695f3f0ab
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60653773"
---
# <a name="copy-data-from-an-http-endpoint-by-using-azure-data-factory"></a>Copiare dati da un endpoint HTTP tramite Azure Data Factory

> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1](v1/data-factory-http-connector.md)
> * [Versione corrente](connector-http.md)

Questo articolo descrive come usare l'attività di copia in Azure Data Factory per copiare dati da un endpoint HTTP. L'articolo è basato su [Attività di copia in Azure Data Factory](copy-activity-overview.md), dove viene presentata una panoramica generale dell'attività di copia.

La differenza tra questo connettore HTTP, il [connettore REST](connector-rest.md) e il [connettore Tabella Web](connector-web-table.md) è la seguente:

- Il **connettore REST** supporta in modo specifico la copia dei dati dalle API RESTful. 
- Il **connettore HTTP** è un connettore generico per recuperare i dati da qualsiasi endpoint HTTP, ad esempio per scaricare file. Prima che il connettore REST diventi disponibile, può capitare di usare il connettore HTTP per copiare dati dall'API RESTful, che è supportata ma meno funzionale rispetto al connettore REST.
- Il **connettore Tabella Web** estrae il contenuto della tabella da una pagina Web HTML.

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un'origine HTTP in qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati dall'attività di copia come origini e sink, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

È possibile usare questo Connettore HTTP per:

- Recuperare dati da un endpoint HTTP/S tramite il metodo HTTP **GET** o **POST**.
- Recuperare dati usando una delle autenticazioni seguenti: **Anonymous**, **Basic**, **Digest**, **Windows** o **ClientCertificate**.
- Copiare la risposta HTTP così com'è o analizzarla usando i [formati di file e i codec di compressione supportati](supported-file-formats-and-compression-codecs.md).

> [!TIP]
> Per testare una richiesta HTTP per il recupero dei dati prima di configurare il connettore HTTP in Data Factory, fare riferimento alla specifica dell'API per i requisiti relativi a intestazione e corpo. È possibile usare strumenti come Postman o un Web browser per la convalida.

## <a name="get-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti presentano informazioni dettagliate sulle proprietà che è possibile usare per definire entità di Data Factory specifiche per il connettore HTTP.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato HTTP sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** deve essere impostata su **HttpServer**. | Sì |
| url | URL di base del server Web. | Sì |
| enableServerCertificateValidation | Specificare se abilitare la convalida del certificato SSL del server quando ci si connette a un endpoint HTTP. Se il server HTTPS usa un certificato autofirmato, impostare questa proprietà su **false**. | No <br /> (il valore predefinito è **true**) |
| authenticationType | Specifica il tipo di autenticazione. I valori consentiti sono **Anonymous**, **Basic**, **Digest**, **Windows** e **ClientCertificate**. <br><br> Vedere le sezioni seguenti per altre proprietà e altri esempi JSON per questi tipi di autenticazione. | Sì |
| connectVia | [Runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare Azure Integration Runtime o un runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non è specificata, questa proprietà usa il tipo Azure Integration Runtime predefinito. |No  |

### <a name="using-basic-digest-or-windows-authentication"></a>Usando l'autenticazione Basic, Digest o Windows

Impostare la proprietà **authenticationType** su **Basic**, **Digest** o **Windows**. Oltre alle proprietà generiche descritte nella sezione precedente, specificare le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| userName | Nome utente da usare per accedere all'endpoint HTTP. | Sì |
| password | Password per l'utente (valore di **userName**). Contrassegnare questo campo come tipo **SecureString** per archiviare la password in modo sicuro in Data Factory. È anche possibile [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | Sì |

**Esempio**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<user name>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Usando l'autenticazione ClientCertificate

Per usare l'autenticazione ClientCertificate, impostare la proprietà **authenticationType** su **ClientCertificate**. Oltre alle proprietà generiche descritte nella sezione precedente, specificare le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| embeddedCertData | Dati del certificato con codifica Base64. | Specificare **embeddedCertData** o **certThumbprint**. |
| certThumbprint | Identificazione personale del certificato installato nell'archivio certificati del computer per il runtime di integrazione self-hosted. Si applica solo quando nella proprietà **connectVia** è specificato il tipo self-hosted del runtime di integrazione. | Specificare **embeddedCertData** o **certThumbprint**. |
| password | Password associata al certificato. Contrassegnare questo campo come tipo **SecureString** per archiviare la password in modo sicuro in Data Factory. È anche possibile [fare riferimento a un segreto archiviato in Azure Key Vault](store-credentials-in-key-vault.md). | No  |

Se si usa **certThumbprint** per l'autenticazione e il certificato è installato nell'archivio personale del computer locale, concedere l'autorizzazione di lettura al runtime di integrazione self-hosted:

1. Aprire Microsoft Management Console (MMC). Aggiungere lo snap-in **Certificati** con **Computer locale** come destinazione.
2. Espandere **Certificati** > **Personale** e quindi selezionare **Certificati**.
3. Fare clic con il tasto destro del mouse sul certificato dall'archivio personale e quindi scegliere **Tutte le attività** > **Gestisci chiavi private**.
3. Nella scheda **Sicurezza** aggiungere l'account utente in cui è in esecuzione il servizio host di Integration Runtime (DIAHostService), con l'accesso in lettura al certificato.

**Esempio 1: Uso di certThumbprint**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "certThumbprint": "<thumbprint of certificate>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Esempio 2: Uso di embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<Base64-encoded cert data>",
            "password": {
                "type": "SecureString",
                "value": "password of cert"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà del set di dati

Questa sezione presenta un elenco delle proprietà supportate dal set di dati HTTP. 

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di dati, vedere [Set di dati e servizi collegati](concepts-datasets-linked-services.md). 

Per copiare dati da HTTP, impostare la proprietà **type** del set di dati su **HttpFile**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del set di dati deve essere impostata su **HttpFile**. | Sì |
| relativeUrl | URL relativo della risorsa che contiene i dati. Quando questa proprietà non è specificata, viene usato solo l'URL indicato nella definizione del servizio collegato. | No  |
| requestMethod | Metodo HTTP. I valori consentiti sono **Get** (predefinito) e **Post**. | No  |
| additionalHeaders | Intestazioni richiesta HTTP aggiuntive. | No  |
| requestBody | Corpo della richiesta HTTP. | No  |
| format | Se si vuole recuperare dati dall'endpoint HTTP così come sono, senza analizzarli, e quindi copiarli in un archivio basato su file, ignorare la sezione **format** nelle definizioni del set di dati di input e di output.<br/><br/>Se si vuole analizzare il contenuto della risposta HTTP durante la copia, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. In **format** impostare la proprietà **type** su uno di questi valori. Per altre informazioni, vedere le sezioni relative ai formati [JSON](supported-file-formats-and-compression-codecs.md#json-format), [testo](supported-file-formats-and-compression-codecs.md#text-format), [Avro](supported-file-formats-and-compression-codecs.md#avro-format), [Orc](supported-file-formats-and-compression-codecs.md#orc-format) e [Parquet](supported-file-formats-and-compression-codecs.md#parquet-format). |No  |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md#compression-support).<br/><br/>Tipi supportati: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>Livelli supportati:  **Optimal** (Ottimale) e **Fastest** (Più veloce). |No  |

> [!NOTE]
> Le dimensioni del payload della richiesta HTTP supportate sono circa 500 KB. Se le dimensioni del payload da passare all'endpoint Web sono maggiori di 500 KB, provare a inviare in batch il payload in blocchi più piccoli.

**Esempio 1: Uso del metodo Get (predefinito)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        }
    }
}
```

**Esempio 2: Uso del metodo Post**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "HttpFile",
        "linkedServiceName": {
            "referenceName": "<HTTP linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "relativeUrl": "<relative url>",
            "requestMethod": "Post",
            "requestBody": "<body for POST HTTP request>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Questa sezione presenta un elenco delle proprietà supportate dall'origine HTTP.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Pipeline](concepts-pipelines-activities.md). 

### <a name="http-as-source"></a>HTTP come origine

Per copiare dati da HTTP, impostare il tipo **source** nell'attività di copia su **HttpSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** dell'origine dell'attività di copia deve essere impostata su **HttpSource**. | Sì |
| httpRequestTimeout | Timeout (valore di **TimeSpan**) durante il quale la richiesta HTTP attende una risposta. Si tratta del timeout per ottenere una risposta, non per leggere i dati della risposta. Il valore predefinito è **00:01:40**.  | No  |

**Esempio**

```json
"activities":[
    {
        "name": "CopyFromHTTP",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<HTTP input dataset name>",
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
                "type": "HttpSource",
                "httpRequestTimeout": "00:01:00"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```


## <a name="next-steps"></a>Passaggi successivi

Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere [Archivi dati e formati supportati](copy-activity-overview.md#supported-data-stores-and-formats).
