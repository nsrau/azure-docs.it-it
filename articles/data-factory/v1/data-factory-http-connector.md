---
title: Spostare i dati da un'origine HTTP-Azure
description: Informazioni su come spostare dati da un origine HTTP locale o cloud tramite Azure Data Factory.
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
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74924170"
---
# <a name="move-data-from-an-http-source-by-using-azure-data-factory"></a>Spostare dati da un'origine HTTP tramite Azure Data Factory

> [!div class="op_single_selector" title1="Selezionare uSelezionare la versione del servizio di Azure Data Factory in uso:"]
> * [Versione 1](data-factory-http-connector.md)
> * [Versione 2 (corrente)](../connector-http.md)

> [!NOTE]
> Le informazioni di questo articolo sono valide per la versione 1 di Data Factory. Se si usa la versione corrente del servizio Azure Data Factory, vedere [HTTP connector in V2](../connector-http.md) (Connettore HTTP nella versione 2).


Questo articolo descrive come usare l'attività di copia in Azure Data Factory per spostare dati da un'origine HTTP locale o cloud in un archivio dati sink supportato. È basato sull'articolo [Spostare i dati usando l'attività di copia](data-factory-data-movement-activities.md), che presenta una panoramica generale dello spostamento dei dati tramite l'attività di copia. L'articolo elenca anche gli archivi dati supportati dall'attività di copia come origini e sink.

Data Factory supporta attualmente solo lo spostamento di dati da un'origine HTTP ad altri archivi dati. Non supporta lo spostamento di dati da altri archivi dati a una destinazione HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Scenari supportati e tipi di autenticazione

È possibile usare questo connettore HTTP per recuperare dati da *endpoint HTTP/S cloud e locali* tramite il metodo HTTP **GET** o **POST**. Sono supportati i seguenti tipi di autenticazione: **Anonymous**, **Basic**, **Digest**, **Windows** e **ClientCertificate**. Notare la differenza tra questo connettore e il [connettore di tabella Web](data-factory-web-table-connector.md). Il connettore di tabella Web estrae il contenuto della tabella da una pagina Web HTML.

Nel copiare dati da un endpoint HTTP locale, è necessario installare Gateway di gestione dati nell'ambiente locale o in una macchina virtuale di Azure. Per informazioni su Gateway di gestione dati e per istruzioni dettagliate su come configurare il gateway, vedere [Moving data between on-premises locations and the cloud](data-factory-move-data-between-onprem-and-cloud.md) (Spostamento di dati tra posizioni locali e nel cloud).

## <a name="get-started"></a>Inizia oggi stesso

È possibile creare una pipeline con l'attività di copia per spostare i dati da un'origine HTTP usando diversi strumenti o API:

- Il modo più semplice per creare una pipeline è usare la procedura guidata di copia. Per una rapida procedura dettagliata di creazione di una pipeline mediante la procedura guidata di copia dei dati, vedere [Tutorial: Create a pipeline by using the Copy wizard](data-factory-copy-data-wizard-tutorial.md) (Esercitazione: Creare una pipeline tramite la procedura guidata di copia).

- È anche possibile usare gli strumenti seguenti per creare una pipeline: **Visual Studio**, **Azure PowerShell**, un **modello di Azure Resource Manager**, l' **API .NET**o l' **API REST**. Per istruzioni dettagliate su come creare una pipeline con un'attività di copia, vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). Per gli esempi JSON per copiare i dati da un'origine HTTP nell'archiviazione BLOB di Azure, vedere [Esempi JSON](#json-examples).

## <a name="linked-service-properties"></a>Proprietà del servizio collegato

La tabella seguente descrive gli elementi JSON specifici del servizio collegato HTTP:

| Proprietà | Description | Obbligatoria |
| --- | --- | --- |
| type | La proprietà **type** deve essere impostata su **Http**. | SÌ |
| url | URL di base del server Web. | SÌ |
| authenticationType | Specifica il tipo di autenticazione. I valori consentiti sono **Anonymous**, **Basic**, **Digest**, **Windows** e **ClientCertificate**. <br><br> Fare riferimento alle sezioni più avanti in questo articolo per altre proprietà e altri esempi JSON per questi tipi di autenticazione. | SÌ |
| enableServerCertificateValidation | Specifica se abilitare la convalida del certificato SSL del server se l'origine è un server Web HTTPS. Quando il server HTTPS usa un certificato autofirmato, impostare questa proprietà su **false**. | No<br /> (il valore predefinito è **true**) |
| gatewayName | Nome dell'istanza di Gateway di gestione dati da usare per la connessione a un'origine HTTP locale. | Sì, se si copiano dati da un'origine HTTP locale |
| encryptedCredential | Credenziale crittografata per l'accesso all'endpoint HTTP. Il valore viene generato automaticamente durante la configurazione delle informazioni di autenticazione nella procedura guidata di copia o tramite la finestra di dialogo **ClickOnce**. | No<br /> (si applica solo quando si copiano dati da un server HTTP locale) |

Per informazioni sull'impostazione di credenziali per un'origine dati connettore HTTP locale, vedere [Move data between on-premises sources and the cloud by using Data Management Gateway](data-factory-move-data-between-onprem-and-cloud.md) (Spostare dati tra origini locali e il cloud tramite Gateway di gestione dati).

### <a name="using-basic-digest-or-windows-authentication"></a>Usando l'autenticazione Basic, Digest o Windows

Impostare **authenticationType** su **Basic**, **Digest** o **Windows**. Oltre alle proprietà generiche del connettore HTTP descritte nelle sezioni precedenti, impostare le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
| --- | --- | --- |
| userName | Nome utente da usare per accedere all'endpoint HTTP. | SÌ |
| password | Password per l'utente (**username**). | SÌ |

**Esempio: Uso dell'autenticazione di base, digest o di Windows**

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

### <a name="using-clientcertificate-authentication"></a>Usando l'autenticazione ClientCertificate

Per usare l'autenticazione di base, impostare **authenticationType** su **ClientCertificate**. Oltre alle proprietà generiche del connettore HTTP descritte nelle sezioni precedenti, impostare le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
| --- | --- | --- |
| embeddedCertData | Contenuto dei dati binari del file PFX con codifica Base64. | Specificare **embeddedCertData** o **certThumbprint** |
| certThumbprint | L'identificazione personale del certificato installato nell'archivio certificati del computer gateway. Si applica solo quando si copiano dati da un server HTTP locale. | Specificare **embeddedCertData** o **certThumbprint** |
| password | Password associata al certificato. | No |

Se si usa **certThumbprint** per l'autenticazione e il certificato è installato nell'archivio personale del computer locale, è necessario concedere autorizzazioni di lettura per il servizio gateway:

1. Aprire Microsoft Management Console (MMC). Aggiungere lo snap-in **Certificati** con **Computer locale** come destinazione.
2. Espandere **Certificati** > **Personale** e quindi selezionare **Certificati**.
3. Fare clic con il tasto destro del mouse sul certificato dall'archivio personale e quindi scegliere **Tutte le attività** >**Gestisci chiavi private**.
3. Nella scheda **Sicurezza** aggiungere l'account utente in cui è in esecuzione il servizio host di Gateway di gestione dati con l'accesso in lettura al certificato.  

**Esempio: Uso di un certificato client**

Questo servizio collegato collega la data factory a un server Web HTTP locale. Usa un certificato client installato nel computer in cui è installato Gateway di gestione dati.

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

**Esempio: Uso di un certificato client in un file**

Questo servizio collegato collega la data factory a un server Web HTTP locale. Usa un file del certificato client nel computer in cui è installato Gateway di gestione dati.

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

## <a name="dataset-properties"></a>Proprietà del set di dati

Alcune sezioni di un file JSON del set di dati, tra cui struttura, disponibilità e criteri, sono simili per tutti i tipi di set di dati (database SQL di Azure, archiviazione BLOB di Azure e archiviazione tabelle di Azure).

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione di set di dati](data-factory-create-datasets.md).

La sezione **typeProperties** è diversa per ogni tipo di set di dati. La sezione **typeProperties** fornisce informazioni sulla posizione dei dati nell'archivio dati. La sezione **typeProperties** per un set di dati di tipo **Http** ha le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
|:--- |:--- |:--- |
| type | La proprietà **type** del set di dati deve essere impostata su **Http**. | SÌ |
| relativeUrl | URL relativo della risorsa che contiene i dati. Quando non è specificato alcun percorso, viene usato solo l'URL specificato nella definizione del servizio collegato. <br><br> Per creare un URL dinamico, è possibile usare le [funzioni e variabili di sistema di Data Factory](data-factory-functions-variables.md). Esempio: **relativeUrl**: **$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)** . | No |
| requestMethod | Metodo HTTP. I valori consentiti sono **GET** e **POST**. | No <br />Il valore predefinito è **GET** |
| additionalHeaders | Intestazioni richiesta HTTP aggiuntive. | No |
| requestBody | Corpo della richiesta HTTP. | No |
| format | Se si vuole *recuperare i dati da un endpoint HTTP così come sono*, senza analizzarli, ignorare l'impostazione di **format**. <br><br> Se si vuole analizzare il contenuto della risposta HTTP durante la copia, sono supportati questi tipi di formato: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Per altre informazioni, vedere le sezioni relative ai formati [testo](data-factory-supported-file-and-compression-formats.md#text-format), [JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Avro](data-factory-supported-file-and-compression-formats.md#avro-format), [Orc](data-factory-supported-file-and-compression-formats.md#orc-format) e [Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). |No |
| compressione | Specificare il tipo e il livello di compressione dei dati. Tipi supportati: **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. Livelli supportati: **Optimal** e **Fastest**. Per altre informazioni, vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

**Esempio: Uso del metodo GET (predefinito)**

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

**Esempio: Uso del metodo POST**

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

Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere [Creating pipelines](data-factory-create-pipelines.md) (Creazione di pipeline). 

Le proprietà disponibili nella sezione **typeProperties** dell'attività variano per ogni tipo di attività. Per l'attività di copia, le proprietà variano in base ai tipi di origine e sink.

Attualmente, quando l'origine nell'attività di copia è di tipo **HttpSource**, sono supportate le proprietà seguenti:

| Proprietà | Description | Obbligatoria |
| -------- | ----------- | -------- |
| httpRequestTimeout | Timeout (valore di **TimeSpan**) durante il quale la richiesta HTTP attende una risposta. Si tratta del timeout per ottenere una risposta, non per leggere i dati della risposta. | No<br />(valore predefinito: **00:01:40**) |

## <a name="supported-file-and-compression-formats"></a>Formati di file e di compressione supportati

Vedere [File and compression formats in Azure Data Factory](data-factory-supported-file-and-compression-formats.md) (Formati di file e compressione in Azure Data Factory).

## <a name="json-examples"></a>Esempi JSON

Gli esempi seguenti forniscono le definizioni JSON di esempio che è possibile usare per creare una pipeline usando [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Gli esempi mostrano come copiare dati da un'origine HTTP all'archiviazione BLOB di Azure. Tuttavia, i dati possono anche essere copiati *direttamente* da una delle origini in qualsiasi sink [supportato](data-factory-data-movement-activities.md#supported-data-stores-and-formats) tramite l'attività di copia in Azure Data Factory.

**Esempio: Copiare dati da un'origine HTTP all'archiviazione BLOB di Azure**

La soluzione di Data Factory per questo esempio contiene le entità Data Factory seguenti:

*   Un servizio collegato di tipo [HTTP](#linked-service-properties).
*   Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
*   Un [set di dati](data-factory-create-datasets.md) di input di tipo [HTTP](#dataset-properties).
*   Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
*   Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [HttpSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Nell'esempio i dati vengono copiati da un'origine HTTP a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi vengono descritte nelle sezioni che seguono gli esempi.

### <a name="http-linked-service"></a>Servizio collegato HTTP

Questo esempio usa il servizio collegato HTTP con l'autenticazione anonima. Per i diversi tipi di autenticazione che è possibile usare, vedere [Servizio collegato HTTP](#linked-service-properties).

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

### <a name="azure-storage-linked-service"></a>Servizio collegato di archiviazione di Azure

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

Impostando **external** su **true** si indica al servizio Data Factory che il set di dati è esterno alla data factory e non viene prodotto da un'attività nella data factory.

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

I dati vengono scritti in un nuovo BLOB ogni ora (**frequenza**: **oraria**, **intervallo**: **1**).

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

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e di output. L'attività di copia è pianificata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo **source** è impostato su **HttpSource** e il tipo **sink** è impostato su **BlobSink**.

Per l'elenco delle proprietà supportate da **HttpSource**, vedere [HttpSource](#copy-activity-properties).

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
> Per eseguire il mapping dal set di dati di origine alle colonne dal set di dati sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione

Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare le prestazioni, vedere [Copy Activity performance and tuning guide](data-factory-copy-activity-performance.md) (Guida alle prestazioni delle attività di copia e all'ottimizzazione).
