---
title: Spostare dati da un'origine HTTP - Azure | Microsoft Docs
description: Informazioni su come spostare dati da un'origine HTTP locale o cloud mediante Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2017
ms.author: jingwang
ms.translationtype: Human Translation
ms.sourcegitcommit: 757d6f778774e4439f2c290ef78cbffd2c5cf35e
ms.openlocfilehash: 4870d2a0bbe35f3980864d8b4f3d011a189b650e
ms.contentlocale: it-it
ms.lasthandoff: 04/10/2017


---
# <a name="move-data-from-an-http-source-using-azure-data-factory"></a>Spostare i dati da un'origine HTTP usando Azure Data Factory
Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare dati da un'origine HTTP locale o cloud a un archivio dati sink supportato. Questo articolo si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e l'elenco degli archivi dati supportati come origini/sink.

Data Factory supporta attualmente solo lo spostamento di dati da un'origine HTTP ad altri archivi dati, non da altri archivi dati a un'origine HTTP.

## <a name="supported-scenarios-and-authentication-types"></a>Scenari supportati e tipi di autenticazione
È possibile usare questo connettor eHTTP per recuperare dati da **endpoint HTTP/s cloud e locali** usando il metodo HTTP **GET** o **POST**. Sono supportati i seguenti tipi di autenticazione: **Anonymous**, **Basic**, **Digest**, **Windows** e **ClientCertificate**. Si noti la differenza tra questo connettore e [il connettore tabella Web](data-factory-web-table-connector.md): quest'ultimo viene usato per estrarre il contenuto di una tabella da una pagina Web HTML.

Quando si copiano dati da un endpoint HTTP locale, è necessario installare un gateway di gestione dati nella macchina virtuale di Azure o nell'ambiente locale. Vedere l'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per informazioni sul Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway.

## <a name="getting-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da un'origine HTTP usando diversi strumenti/API.

- Il modo più semplice per creare una pipeline è usare la **Copia guidata**. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

- È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **Azure PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia. Per gli esempi JSON per copiare i dati dall'origine HTTP in archiviazione BLOB di Azure, vedere la sezione degli [esempi JSON](#json-examples) in questo articolo.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato HTTP.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type | La proprietà type deve essere impostata su: `Http`. | Sì |
| URL | URL di base al server Web | Sì |
| authenticationType | Specifica il tipo di autenticazione. I valori consentiti sono: **Anonymous**, **Basic**, **Digest**, **Windows** e **ClientCertificate**. <br><br> Fare riferimento alle sezioni sotto questa tabella per altre proprietà e altri esempi JSON per questi tipi di autenticazione. | Sì |
| enableServerCertificateValidation | Specificare se abilitare la convalida del certificato SSL del server se l'origine è un server Web HTTPS | No, il valore predefinito è true |
| gatewayName | Nome del gateway di gestione dati per connettersi a un'origine HTTP locale. | Sì se si copiano i dati da un'origine HTTP locale. |
| encryptedCredential | Credenziali crittografate per accedere all'endpoint HTTP. Generate automaticamente quando si configurano le informazioni di autenticazione nella procedura di copia guidata o nella finestra di dialogo popup ClickOnce. | No. Applicare solo se si copiano i dati da un server HTTP locale. |

Vedere [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) per informazioni dettagliate sull'impostazione delle credenziali per un'origine dati connettore HTTP locale.

### <a name="using-basic-digest-or-windows-authentication"></a>Usando l'autenticazione Basic, Digest o Windows

Impostare `authenticationType` come `Basic`, `Digest`, o `Windows` e specificare le proprietà seguenti oltre a quelle generiche del connettore HTTP illustrate in precedenza:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| username | Nome utente per accedere all'endpoint HTTP. | Sì |
| password | Password per l'utente (nome utente). | Sì |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Esempio: usando l'autenticazione Basic, Digest or Windows

```JSON
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

Per usare l'autenticazione di base, impostare `authenticationType` come `ClientCertificate` e specificare le proprietà seguenti oltre a quelle generiche del connettore HTTP introdotte in precedenza:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| embeddedCertData | Contenuto con codifica Base 64 dei dati binari del file di scambio di informazioni personali (PFX, Personal Information Exchange). | Specificare `embeddedCertData` o `certThumbprint`. |
| certThumbprint | L'identificazione personale del certificato installato nell'archivio certificati del computer gateway. Applicare solo se si copiano i dati da un'origine HTTP locale. | Specificare `embeddedCertData` o `certThumbprint`. |
| password | Password associata al certificato. | No |

Se si usa `certThumbprint` per l'autenticazione e il certificato è installato nell'archivio personale del computer locale, è necessario concedere l'autorizzazione di lettura per il servizio gateway:

1. Avviare Microsoft Management Console (MMC). Aggiungere lo snap-in **Certificati** con il **Computer locale** come destinazione.
2. Espandere **Certificati**, **Personali** e fare clic su **Certificati**.
3. Fare clic con il tasto destro del mouse sul certificato dall'archivio personale, quindi selezionare **Tutte le attività**->**Gestisci chiavi private...**
3. Nella scheda **Sicurezza** aggiungere l'account utente in cui è in esecuzione il Servizio host di Gateway di gestione dati con l'accesso in lettura al certificato.  

#### <a name="example-using-client-certificate"></a>Esempio: utilizzo di un certificato client
Questo servizio collegato collega la data factory a un server Web HTTP locale. Usa un file del certificato client installato nel computer che dispone del Gateway di gestione dati.

```JSON
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

#### <a name="example-using-client-certificate-in-a-file"></a>Esempio: utilizzo di un certificato client in un file
Questo servizio collegato collega la data factory a un server Web HTTP locale. Usa un file del certificato client nel computer con installato il Gateway di gestione dati.

```JSON
{
    "name": "HttpLinkedService",
    "properties":
    {
        "type": "Http",
        "typeProperties":
        {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **Http** presenta le proprietà seguenti

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | Specifica il tipo del set di dati. deve essere impostato su `Http`. | Sì |
| relativeUrl | URL relativo della risorsa che contiene i dati. Quando non è specificato alcun percorso, viene usato solo l'URL specificato nella definizione del servizio collegato. <br><br> Per creare un URL dinamico, è possibile usare [funzioni di Data Factory e variabili di sistema](data-factory-functions-variables.md), ad esempio "relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)". | No |
| requestMethod | Metodo HTTP. I valori consentiti sono **GET** o **POST**. | No. Il valore predefinito è `GET`. |
| additionalHeaders | Intestazioni richiesta HTTP aggiuntive. | No |
| requestBody | Il corpo della richiesta HTTP. | No |
| format | Se si desidera semplicemente **recuperare i dati dall'endpoint HTTP così come sono** senza analizzarli, ignorare questa impostazione di formato. <br><br> Se si desidera analizzare i contenuti di risposta HTTP durante la copia, sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Per altre informazioni, vedere le sezioni [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format), [JsonFormat](data-factory-supported-file-and-compression-formats.md#json-format), [AvroFormat](data-factory-supported-file-and-compression-formats.md#avro-format), [OrcFormat](data-factory-supported-file-and-compression-formats.md#orc-format) e [ParquetFormat](data-factory-supported-file-and-compression-formats.md#parquet-format). |No |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. I livelli supportati sono **Ottimale** e **Più veloce**. Per altre informazioni, vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

### <a name="example-using-the-get-default-method"></a>Esempio: usando il metodo GET (predefinito)

```JSON
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

### <a name="example-using-the-post-method"></a>Esempio: usando il metodo POST

```JSON
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
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

D'altra parte, le proprietà disponibili nella sezione **typeProperties** dell'attività variano in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

Quando l'origine nell'attività di copia è di tipo **HttpSource**, sono supportate le proprietà seguenti.

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| httpRequestTimeout | Il timeout (TimeSpan) durante il quale la richiesta HTTP attende una risposta. Si tratta del timeout per ottenere una risposta, non per leggere i dati della risposta stessa. | No. Valore predefinito: 00:01:40 |

## <a name="supported-file-and-compression-formats"></a>Formati di file e di compressione supportati
Per i dettagli, vedere l'articolo relativo ai [file e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

## <a name="json-examples"></a>Esempi JSON
L'esempio seguente fornisce le definizioni JSON campione da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Illustrano come copiare dati da un'origine HTTP in un archivio BLOB di Azure. Tuttavia, i dati possono essere copiati **direttamente** da una delle origini in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Data factory di Azure.

### <a name="example-copy-data-from-http-source-to-azure-blob-storage"></a>Esempio: copiare dati da un'origine HTTP in un archivio BLOB di Azure
La soluzione di Data Factory per questo esempio contiene le entità Data Factory seguenti:

1. Un servizio collegato di tipo [HTTP](#linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [HTTP](#dataset-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [HttpSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

Nell'esempio i dati vengono copiati da un'origine HTTP a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

### <a name="http-linked-service"></a>Servizio collegato HTTP
Questo esempio usa il servizio collegato HTTP con l'autenticazione anonima. Per i diversi tipi di autenticazione disponibili, vedere la sezione relativa al [servizio collegato HTTP](#linked-service-properties).

```JSON
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

### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure

```JSON
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

### <a name="http-input-dataset"></a>Set di dati di input HTTP
Impostando **external** su **true** si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

```JSON
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

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1).

```JSON
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

### <a name="pipeline-with-copy-activity"></a>Pipeline con attività di copia

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo **source** è impostato su **HttpSource** e il tipo **sink** è impostato su **BlobSink**.

Vedere [HttpSource](#httpsource-in-copy-activity) per l'elenco delle proprietà supportate da HttpSource.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
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
> Per eseguire il mapping dal set di dati di origine alle colonne del set di dati sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

