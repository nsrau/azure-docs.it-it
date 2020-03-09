---
title: Spostare i dati da un'origine HTTP-Azure
description: Informazioni su come spostare i dati da un'origine HTTP locale o cloud usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: e668f44bbc3d2e381edeb80c568a41355584a4ee
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2020
ms.locfileid: "78387442"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Spostare i dati da un'origine HTTP usando Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare la versione del servizio Data Factory in uso:"]
> * [Versione 1](data-factory-http-connector.md)
> * [Versione 2 (versione corrente)](../connector-http.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del Data Factory. Se si usa la versione corrente del servizio Azure Data Factory, vedere [connettore HTTP nella versione V2](../connector-http.md).


Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da un endpoint HTTP locale o cloud a un archivio dati sink supportato. Questo articolo si basa sullo [spostamento dei dati tramite l'attività di copia](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati tramite l'attività di copia. L'articolo elenca anche gli archivi dati supportati dall'attività di copia come origini e sink.

Data Factory attualmente supporta solo lo stato di trasferimento dei dati da un'origine HTTP ad altri archivi dati. Non supporta lo stato di trasferimento dei dati da altri archivi dati a una destinazione HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Scenari supportati e tipi di autenticazione

È possibile usare questo connettore HTTP per recuperare dati da *un endpoint HTTP/S cloud e locale* usando i metodi HTTP **Get** o **post** . Sono supportati i seguenti tipi di autenticazione: **Anonymous**, **Basic**, **digest**, **Windows**e **ClientCertificate**. Si noti la differenza tra questo connettore e il [connettore tabella Web](data-factory-web-table-connector.md). Il connettore tabella Web estrae il contenuto della tabella da una pagina Web HTML.

Quando si copiano dati da un endpoint HTTP locale, è necessario installare Gestione dati gateway nell'ambiente locale o in una VM di Azure. Per informazioni su Gestione dati gateway e per istruzioni dettagliate su come configurare il gateway, vedere [lo spostamento dei dati tra percorsi locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md).

## <a name="get-started"></a>Inizia subito

È possibile creare una pipeline con un'attività di copia per spostare i dati da un'origine HTTP usando diversi strumenti o API:

- Il modo più semplice per creare una pipeline consiste nell'usare la procedura guidata Copia dati. Per una rapida procedura dettagliata sulla creazione di una pipeline usando la procedura guidata Copia dati, vedere [esercitazione: creare una pipeline usando la copia guidata](data-factory-copy-data-wizard-tutorial.md).

- È anche possibile usare gli strumenti seguenti per creare una pipeline: **Visual Studio**, **Azure PowerShell**, un **modello di Azure Resource Manager**, l' **API .NET**o l' **API REST**. Per istruzioni dettagliate su come creare una pipeline con un'attività di copia, vedere l' [esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Per esempi JSON che consentono di copiare dati da un'origine HTTP in un archivio BLOB di Azure, vedere [esempi JSON](#json-examples).

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

La tabella seguente descrive gli elementi JSON specifici del servizio collegato HTTP:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| Tipo | La proprietà **Type** deve essere impostata su **http**. | Sì |
| URL | URL di base del server Web. | Sì |
| authenticationType | Specifica il tipo di autenticazione. I valori consentiti sono **Anonymous**, **Basic**, **digest**, **Windows**e **ClientCertificate**. <br><br> Per altre proprietà ed esempi JSON per questi tipi di autenticazione, vedere le sezioni successive di questo articolo. | Sì |
| enableServerCertificateValidation | Specifica se abilitare la convalida del certificato SSL del server se l'origine è un server Web HTTPS. Quando il server HTTPS utilizza un certificato autofirmato, impostarlo su **false**. | No<br /> (il valore predefinito è **true**) |
| gatewayName | Nome dell'istanza del gateway Gestione dati da usare per connettersi a un'origine HTTP locale. | Sì, se si copiano dati da un'origine HTTP locale |
| encryptedCredential | Credenziali crittografate per accedere all'endpoint HTTP. Il valore viene generato automaticamente quando si configurano le informazioni di autenticazione nella copia guidata o tramite la finestra di dialogo **ClickOnce** . | No<br /> (applicare solo quando si copiano dati da un server HTTP locale) |

Per informazioni dettagliate sull'impostazione delle credenziali per un'origine dati del connettore HTTP locale, vedere [spostare dati tra origini locali e il cloud usando Gestione dati Gateway](data-factory-move-data-between-onprem-and-cloud.md).

### <a name="using-basic-digest-or-windows-authentication"></a>Utilizzo dell'autenticazione di base, digest o Windows

Impostare **AuthenticationType** su di **base**, **digest**o **Windows**. Oltre alle proprietà del connettore HTTP generico descritte nelle sezioni precedenti, impostare le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| Nome utente | Nome utente da utilizzare per accedere all'endpoint HTTP. | Sì |
| Password | Password per l'utente (**username**). | Sì |

**Esempio: uso dell'autenticazione di base, digest o Windows**

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "basic",
            "url" : "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

### <a name="using-clientcertificate-authentication"></a>Uso dell'autenticazione ClientCertificate

Per utilizzare l'autenticazione di base, impostare **AuthenticationType** su **ClientCertificate**. Oltre alle proprietà del connettore HTTP generico descritte nelle sezioni precedenti, impostare le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| embeddedCertData | Contenuto con codifica Base64 dei dati binari del file PFX. | Specificare **embeddedCertData** o **certThumbprint** |
| certThumbprint | Identificazione personale del certificato installato nell'archivio certificati del computer gateway. Applicare solo quando si copiano dati da un'origine HTTP locale. | Specificare **embeddedCertData** o **certThumbprint** |
| Password | Password associata al certificato. | No |

Se si usa **certThumbprint** per l'autenticazione e il certificato è installato nell'archivio personale del computer locale, concedere le autorizzazioni di lettura al servizio gateway:

1. Aprire Microsoft Management Console (MMC). Aggiungere lo snap-in **certificati** destinato al **computer locale**.
2. Espandere **certificati** > **personale**, quindi selezionare **certificati**.
3. Fare clic con il pulsante destro del mouse sul certificato nell'archivio personale, quindi selezionare **tutte le attività** >**Gestisci chiavi private**.
3. Nella scheda **sicurezza** aggiungere l'account utente con cui è in esecuzione il servizio host del gateway gestione dati, con accesso in lettura al certificato.  

**Esempio: utilizzo di un certificato client**

Questo servizio collegato collega l'data factory a un server Web HTTP locale. Usa un certificato client installato nel computer in cui è installato Gestione dati Gateway.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"

        }
    }
}
```

**Esempio: utilizzo di un certificato client in un file**

Questo servizio collegato collega l'data factory a un server Web HTTP locale. Usa un file del certificato client nel computer in cui è installato Gestione dati Gateway.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "Base64-encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà set di dati

Alcune sezioni di un file JSON del set di dati, ad esempio struttura, disponibilità e criteri, sono simili per tutti i tipi di set di dati (database SQL di Azure, archiviazione BLOB di Azure, archiviazione tabelle di Azure).

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione dei set di impostazioni, vedere [creazione di set](data-factory-create-datasets.md)di elementi.

La sezione **typeProperties** è diversa per ogni tipo di set di dati. La sezione **typeProperties** fornisce informazioni sul percorso dei dati nell'archivio dati. La sezione **typeProperties** per un set di dati del tipo **http** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| Tipo | Il **tipo** del set di dati deve essere impostato su **http**. | Sì |
| relativeUrl | URL relativo della risorsa che contiene i dati. Quando il percorso non è specificato, viene usato solo l'URL specificato nella definizione del servizio collegato. <br><br> Per costruire un URL dinamico, è possibile usare [Data Factory funzioni e variabili di sistema](data-factory-functions-variables.md). Esempio: **relativeUrl**: **$ $Text. Format ('/My/Report? month = {0: aaaa}-{0: mm} & fmt = CSV ', SliceStart)** . | No |
| requestMethod | Metodo HTTP. I valori consentiti sono **Get** e **post**. | No <br />(il valore predefinito è **Get**) |
| additionalHeaders | Intestazioni di richiesta HTTP aggiuntive. | No |
| requestBody | Corpo della richiesta HTTP. | No |
| formato | Se si desidera *recuperare i dati da un endpoint HTTP così come sono* senza analizzarli, ignorare l'impostazione del **formato** . <br><br> Se si desidera analizzare il contenuto della risposta HTTP durante la copia, sono supportati i tipi di formato **seguenti:** TextFormat, **JsonFormat**, **AvroFormat**, **OrcFormat**e **ParquetFormat**. Per altre informazioni, vedere [formato testo](data-factory-supported-file-and-compression-formats.md#text-format), [formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [formato Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [formato ORC](data-factory-supported-file-and-compression-formats.md#orc-format)e [formato parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |No |
| compressione | Specificare il tipo e il livello di compressione per i dati. Tipi supportati: **gzip**, **deflate**, **bzip2**e **ZipDeflate**. Livelli supportati: **ottimale** e più **veloce**. Per ulteriori informazioni, vedere [formati di compressione e file in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

**Esempio: uso del metodo GET (default)**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

**Esempio: utilizzo del metodo POST**

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
           "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia

Sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri per tutti i tipi di attività.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [creazione di pipeline](data-factory-create-pipelines.md). 

Le proprietà disponibili nella sezione **typeProperties** dell'attività variano a seconda del tipo di attività. Per un'attività di copia, le proprietà variano a seconda dei tipi di origine e sink.

Attualmente, quando l'origine nell'attività di copia è del tipo **HttpSource** , sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| httpRequestTimeout | Timeout (valore **TimeSpan** ) per la richiesta HTTP per ottenere una risposta. È il timeout per ottenere una risposta e non il timeout per leggere i dati della risposta. | No<br />(valore predefinito: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Formati di file e di compressione supportati

Per ulteriori informazioni, vedere [formati di compressione e file in Azure Data Factory](data-factory-supported-file-and-compression-formats.md) .

## <a name="json-examples"></a>Esempi JSON

Gli esempi seguenti forniscono le definizioni JSON di esempio che è possibile usare per creare una pipeline usando [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Gli esempi illustrano come copiare dati da un'origine HTTP in un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati *direttamente* da qualsiasi origine a qualsiasi sink [supportato](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.

**Esempio: copiare dati da un'origine HTTP in un archivio BLOB di Azure**

La soluzione Data Factory per questo esempio contiene le entità Data Factory seguenti:

*   Un servizio collegato di tipo [http](#linked-service-properties).
*   Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Un [set di dati](data-factory-create-datasets.md) di input di tipo [http](#dataset-properties).
*   Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   [Pipeline](data-factory-create-pipelines.md) con un'attività di copia che usa [HttpSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

L'esempio copia i dati da un'origine HTTP a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni che seguono gli esempi.

### <a name="http-linked-service"></a>Servizio collegato HTTP

Questo esempio usa il servizio collegato HTTP con l'autenticazione anonima. Vedere [servizio collegato http](#linked-service-properties) per diversi tipi di autenticazione che è possibile usare.

```json
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

### <a name="azure-storage-linked-service"></a>Servizio collegato archiviazione di Azure

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
    }
  }
}
```

### <a name="http-input-dataset"></a>Set di dati di input HTTP

Impostando **External** su **true** si comunica al servizio Data Factory che il set di dati è esterno al data factory e non è prodotto da un'attività nel data factory.

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}

```

### <a name="azure-blob-output-dataset"></a>Set di dati di output del BLOB di Azure

I dati vengono scritti in un nuovo BLOB ogni ora (**frequenza**: **ora**, **intervallo**: **1**).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

### <a name="pipeline-that-uses-a-copy-activity"></a>Pipeline che usa un'attività di copia

La pipeline contiene un'attività di copia configurata per l'uso dei set di dati di input e di output. L'attività di copia è pianificata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **HttpSource** e il tipo di **sink** è impostato su **BlobSink**.

Per l'elenco delle proprietà supportate da **HttpSource** , vedere [HttpSource](#copy-activity-properties).

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with a copy activity",
    "activities":[  
      {
        "name": "HttpSourceToAzureBlob",
        "description": "Copy from an HTTP source to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "HttpSourceDataInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "HttpSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

> [!NOTE]
> Per eseguire il mapping delle colonne da un set di dati di origine a colonne da un set di dati sink, vedere [mapping delle colonne di DataSet in Azure Data Factory](data-factory-map-columns.md)

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione

Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati (attività di copia) in Azure Data Factory e su vari modi per ottimizzarlo, vedere la [Guida alle prestazioni dell'attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).
