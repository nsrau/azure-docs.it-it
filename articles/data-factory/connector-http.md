---
title: Copiare dati da origini HTTP usando Azure Data Factory | Microsoft Docs
description: "Informazioni su come copiare dati da un'origine HTTP locale o cloud in archivi dati sink supportati usando un'attività di copia in una pipeline di Azure Data Factory."
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
ms.date: 10/12/2017
ms.author: jingwang
ms.openlocfilehash: cdf4e808045bb649b3a2406e8f7c1ef30e34fe7b
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/11/2018
---
# <a name="copy-data-from-http-endpoint-using-azure-data-factory"></a>Copiare dati da un endpoint HTTP usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-http-connector.md)
> * [Versione 2 - Anteprima](connector-http.md)

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per copiare dati da un endpoint HTTP. Si basa sull'articolo di [panoramica dell'attività di copia](copy-activity-overview.md) che presenta una panoramica generale sull'attività di copia.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere [Connettore HTTP in V1](v1/data-factory-http-connector.md).

## <a name="supported-capabilities"></a>Funzionalità supportate

È possibile copiare dati da un'origine HTTP in qualsiasi archivio dati sink supportato. Per un elenco degli archivi dati supportati come origini/sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).

In particolare, il connettore HTTP supporta:

- Il recupero dei dati dall'endpoint HTTP/s tramite il metodo **GET** o **POST** di HTTP.
- Il recupero dei dati tramite le autenticazioni seguenti: **Anonima**, **Base**, **Digest**, **Windows** e **ClientCertificate**.
- La copia della risposta HTTP così com'è o l'analisi con i [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md).

La differenza tra questo connettore e [il connettore della tabella Web](connector-web-table.md) è che quest'ultimo viene usato per estrarre il contenuto di una tabella da una pagina Web HTML.

## <a name="getting-started"></a>Attività iniziali

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà che vengono usate per definire entità di data factory specifiche per il connettore HTTP.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

Per il servizio collegato HTTP sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su **HttpServer**. | Sì |
| url | URL di base al server Web | Sì |
| enableServerCertificateValidation | Specificare se abilitare la convalida del certificato SSL del server quando si esegue la connessione all'endpoint HTTP. | No, il valore predefinito è true |
| authenticationType | Specifica il tipo di autenticazione. I valori consentiti sono: **Anonymous**, **Basic**, **Digest**, **Windows** e **ClientCertificate**. <br><br> Fare riferimento alle sezioni sotto questa tabella per altre proprietà e altri esempi JSON per questi tipi di autenticazione. | Sì |
| connectVia | Il [runtime di integrazione](concepts-integration-runtime.md) da usare per la connessione all'archivio dati. È possibile usare il runtime di integrazione di Azure o il runtime di integrazione self-hosted (se l'archivio dati si trova in una rete privata). Se non specificato, viene usato il runtime di integrazione di Azure predefinito. |No |

### <a name="using-basic-digest-or-windows-authentication"></a>Usando l'autenticazione Basic, Digest o Windows

Impostare la proprietà "authenticationType" su **Base**, **Digest** o **Windows** e specificare le proprietà seguenti insieme alle proprietà generiche descritte nella precedente sezione:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| userName | Nome utente per accedere all'endpoint HTTP. | Sì |
| password | Password per l'utente (userName). Contrassegnare questo campo come SecureString. | Sì |

**Esempio**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "Basic",
            "url" : "<HTTP endpoint>",
            "userName": "<username>",
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

Per usare l'autenticazione ClientCertificate impostare la proprietà "authenticationType" su **ClientCertificate** e specificare le proprietà seguenti insieme alle proprietà generiche descritte nella precedente sezione:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| embeddedCertData | Dati del certificato con codifica Base64. | Specificare `embeddedCertData` o `certThumbprint`. |
| certThumbprint | Identificazione personale del certificato installato nell'archivio certificati del computer per il runtime di integrazione self-hosted. Si applica solo quando in connectVia viene specificato il tipo di runtime di integrazione self-hosted. | Specificare `embeddedCertData` o `certThumbprint`. |
| password | Password associata al certificato. Contrassegnare questo campo come SecureString. | No |

Se si usa "certThumbprint" per l'autenticazione e il certificato è installato nell'archivio personale del computer locale, è necessario concedere l'autorizzazione di lettura al runtime di integrazione self-hosted:

1. Avviare Microsoft Management Console (MMC). Aggiungere lo snap-in **Certificati** con il **Computer locale** come destinazione.
2. Espandere **Certificati**, **Personali** e fare clic su **Certificati**.
3. Fare clic con il tasto destro del mouse sul certificato dall'archivio personale, quindi selezionare **Tutte le attività** -> **Gestisci chiavi private...**
3. Nella scheda **Sicurezza** aggiungere l'account utente in cui è in esecuzione il servizio host di Runtime di integrazione, ovvero DIAHostService, con l'accesso in lettura al certificato.

**Esempio 1: uso di certThumbprint**

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

**Esempio 2: uso di embeddedCertData**

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "HttpServer",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "<HTTP endpoint>",
            "embeddedCertData": "<base64 encoded cert data>",
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

## <a name="dataset-properties"></a>Proprietà dei set di dati

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sui set di dati. Questa sezione presenta un elenco delle proprietà supportate dal set di dati HTTP.

Per copiare dati da HTTP, impostare la proprietà type del set di dati su **HttpFile**. Sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type del set di dati deve essere impostata su **HttpFile** | Sì |
| relativeUrl | URL relativo della risorsa che contiene i dati. Quando questa proprietà non è specificata, viene usato solo l'URL specificato nella definizione del servizio collegato. | No |
| requestMethod | Metodo HTTP.<br/>I valori consentiti sono **Get**, come valore predefinito, o **Post**. | No |
| additionalHeaders | Intestazioni richiesta HTTP aggiuntive. | No |
| requestBody | Il corpo della richiesta HTTP. | No |
| formato | Se si desidera **recuperare dati dall'endpoint HTTP così com'è**, senza analizzarli e copiarli in un archivio basato su file, ignorare la sezione del formato sia per le definizioni di input che di output.<br/><br/>Se si desidera analizzare i contenuti della risposta HTTP durante la copia, sono supportati i tipi di formato file seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [Json Format](supported-file-formats-and-compression-codecs.md#json-format) (Formato JSON), [Text Format](supported-file-formats-and-compression-codecs.md#text-format) (Formato testo), [Avro Format](supported-file-formats-and-compression-codecs.md#avro-format) (Formato Avro), [Orc Format](supported-file-formats-and-compression-codecs.md#orc-format) (Formato Orc) e [Parquet Format](supported-file-formats-and-compression-codecs.md#parquet-format) (Formato Parquet). |No |
| compressione | Specificare il tipo e il livello di compressione dei dati. Per altre informazioni, vedere l'articolo sui [formati di file supportati e i codec di compressione](supported-file-formats-and-compression-codecs.md#compression-support).<br/>I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**.<br/>I livelli supportati sono **Ottimale** e **Più veloce**. |No |

**Esempio 1: uso del metodo Get, per impostazione predefinita**

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

**Esempio 2: uso del metodo Post**

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

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulle [pipeline](concepts-pipelines-activities.md). Questa sezione presenta un elenco delle proprietà supportate dall'origine HTTP.

### <a name="http-as-source"></a>HTTP come origine

Per copiare dati da un database HTTP, impostare il tipo di origine nell'attività di copia su **HttpSource**. Nella sezione **origine** dell'attività di copia sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type dell'origine dell'attività di copia deve essere impostata su **HttpSource** | Sì |
| httpRequestTimeout | Il timeout (TimeSpan) durante il quale la richiesta HTTP attende una risposta. Si tratta del timeout per ottenere una risposta, non per leggere i dati della risposta stessa.<br/> Il valore predefinito è 00:01:40  | No |

**Esempio:**

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
Per un elenco degli archivi dati supportati come origini o sink dall'attività di copia in Azure Data Factory, vedere gli [archivi dati supportati](copy-activity-overview.md#supported-data-stores-and-formats).