---
title: Spostare dati da un server FTP usando Azure Data Factory | Microsoft Docs
description: Informazioni su come spostare dati da un server FTP usando Azure Data Factory.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/15/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0452610e56294a19bab302d6df73dff2a70a2eeb
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/03/2017
---
# <a name="move-data-from-an-ftp-server-by-using-azure-data-factory"></a>Spostare dati da un server FTP usando Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-ftp-connector.md)
> * [Versione 2 - Anteprima](../connector-ftp.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 di Data Factory, che è disponibile a livello generale. Se si usa la versione 2 del servizio Data Factory, disponibile in anteprima, vedere [Connettore FTP nella versione 2](../connector-ftp.md).

Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare i dati da un server FTP. Si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con l'attività di copia.

È possibile copiare dati da un server FTP a qualsiasi archivio dati di sink supportato. Per un elenco degli archivi dati supportati come sink dall'attività di copia, vedere la tabella relativa agli [archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats). Data Factory supporta attualmente solo lo spostamento di dati da un server FTP ad altri archivi dati, non da altri archivi dati a un server FTP. Supporta i server FTP locali e cloud.

> [!NOTE]
> L'attività di copia non elimina il file di origine dopo che è stato correttamente copiato nella destinazione. Se è necessario eliminare il file di origine dopo una copia con esito positivo, creare un'attività personalizzata per eliminare il file e usare l'attività nella pipeline. 

## <a name="enable-connectivity"></a>Abilitare la connettività
Se si spostano dati da un server FTP **locale** in un archivio dati cloud, ad esempio in Archiviazione BLOB di Azure, installare e usare Gateway di gestione dati. Gateway di gestione dati è un agente client installato nel computer locale, che consente ai servizi cloud di connettersi alla risorsa locale. Per informazioni dettagliate, vedere [Gateway di gestione dati](data-factory-data-management-gateway.md). Per istruzioni dettagliate sulla configurazione e sull'uso del gateway, vedere [Spostare dati tra origini locali e il cloud](data-factory-move-data-between-onprem-and-cloud.md). Il gateway consente di connettersi a un server FTP anche se il server si trova in una macchina virtuale (VM) di infrastruttura distribuita come servizio (IaaS) di Azure.

È possibile installare il gateway nello stesso computer locale o nella VM IaaS come server FTP. È tuttavia consigliabile installare il gateway in un computer separato o in una macchina virtuale IaaS distinta per evitare conflitti tra le risorse e ottenere prestazioni migliori. Quando si installa il gateway in un computer separato, questo deve poter accedere al server FTP.

## <a name="get-started"></a>Introduzione
È possibile creare una pipeline con l'attività di copia che sposta i dati da un'origine FTP usando diversi strumenti o API.

Il modo più semplice per creare una pipeline è usare la **Copia guidata di Data Factory**. Per una procedura dettagliata, vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md).

È possibile anche usare gli strumenti seguenti per creare una pipeline: **portale di Azure**, **Visual Studio**, **PowerShell**, **modello di Azure Resource Manager**, **API .NET** e **API REST**. Vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia.

Se si usano gli strumenti o le API, eseguire la procedura seguente per creare una pipeline che sposta i dati da un archivio dati di origine a un archivio dati sink:

1. Creare i **servizi collegati** per collegare gli archivi di dati di input e output alla data factory.
2. Creare i **set di dati** per rappresentare i dati di input e di output per le operazioni di copia.
3. Creare una **pipeline** con un'attività di copia che accetti un set di dati come input e un set di dati come output.

Quando si usa la procedura guidata, le definizioni JSON per queste entità di data factory (servizi, set di dati e pipeline collegati) vengono create automaticamente. Quando si usano gli strumenti o le API, ad eccezione delle API .NET, usare il formato JSON per definire le entità di Data Factory. Per un esempio con definizioni JSON per entità di Data Factory usate per copiare dati da un archivio dati FTP, vedere la sezione [Esempio JSON: copiare dati da un server FTP al BLOB di Azure](#json-example-copy-data-from-ftp-server-to-azure-blob) di questo articolo.

> [!NOTE]
> Per altre informazioni sui formati di compressione e i file e supportati da usare, vedere [Informazioni sui formati di compressione e sui file supportati da Azure Data Factory](data-factory-supported-file-and-compression-formats.md).

Le sezioni seguenti riportano informazioni dettagliate sulle proprietà JSON che vengono usate per definire entità di Data Factory specifiche di FTP.

## <a name="linked-service-properties"></a>Proprietà del servizio collegato
La tabella seguente descrive gli elementi JSON specifici di un servizio collegato FTP.

| Proprietà | Descrizione | Obbligatorio | Predefinito |
| --- | --- | --- | --- |
| type |Impostare su FtpServer. |Sì |&nbsp; |
| host |Specificare il nome o indirizzo IP del server FTP. |Sì |&nbsp; |
| authenticationType |Specificare il tipo di autenticazione. |Sì |Di base, anonimo |
| username |Specificare l'utente che ha accesso al server FTP. |No |&nbsp; |
| password |Specificare la password per l'utente (nome utente). |No |&nbsp; |
| encryptedCredential |Specificare le credenziali crittografate per accedere al server FTP. |No |&nbsp; |
| gatewayName |Specificare il nome del gateway in Gateway di gestione dati per connettersi a un server FTP locale. |No |&nbsp; |
| port |Specificare la porta su cui è in ascolto il server FTP. |No |21 |
| enableSsl |Specificare se usare FTP su un canale SSL/TLS. |No |true |
| enableServerCertificateValidation |Specificare se abilitare la convalida del certificato SSL del server quando si usa FTP sul canale SSL/TLS. |No |true |

### <a name="use-anonymous-authentication"></a>Usare l'autenticazione anonima

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {        
            "authenticationType": "Anonymous",
              "host": "myftpserver.com"
        }
    }
}
```

### <a name="use-username-and-password-in-plain-text-for-basic-authentication"></a>Usare nome utente e password in testo normale per l'autenticazione di base

```JSON
{
    "name": "FTPLinkedService",
      "properties": {
    "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "username": "Admin",
            "password": "123456"
        }
      }
}
```

### <a name="use-port-enablessl-enableservercertificatevalidation"></a>Usare port, enableSsl, enableServerCertificateValidation

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",    
            "username": "Admin",
            "password": "123456",
            "port": "21",
            "enableSsl": true,
            "enableServerCertificateValidation": true
        }
    }
}
```

### <a name="use-encryptedcredential-for-authentication-and-gateway"></a>Usare encryptedCredential per autenticazione e gateway

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "mygateway"
        }
      }
}
```

## <a name="dataset-properties"></a>Proprietà dei set di dati
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati.

La sezione **typeProperties** è diversa per ogni tipo di set di dati. Fornisce informazioni specifiche del tipo di set di dati. La sezione **typeProperties** per un set di dati di tipo **FileShare** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| folderPath |Sottopercorso alla cartella. Usare il carattere di escape "\" per i caratteri speciali nella stringa. Per ottenere alcuni esempi, vedere [Servizio collegato di esempio e definizioni del set di dati](#sample-linked-service-and-dataset-definitions) .<br/><br/>È possibile combinare questa proprietà con **partitionBy** per ottenere percorsi di cartelle basati su data e ora di inizio e fine delle sezioni. |Sì |
| fileName |Specificare il nome del file in **folderPath** se si vuole che la tabella faccia riferimento a un file specifico nella cartella. Se non si specifica alcun valore per questa proprietà, la tabella punta a tutti i file nella cartella.<br/><br/>Quando **fileName** non viene specificato per un set di dati di output, il formato del nome del file generato è il seguente: <br/><br/>Data<Guid>.txt, ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| fileFilter |Specificare un filtro da usare per selezionare un sottoinsieme di file in **folderPath** anziché tutti i file.<br/><br/>I valori consentiti sono: `*` (più caratteri) e `?` (carattere singolo).<br/><br/>Esempio 1: `"fileFilter": "*.log"`<br/>Esempio 2: `"fileFilter": 2014-1-?.txt"`<br/><br/> **fileFilter** è applicabile per un set di dati di input FileShare. Questa proprietà non è supportata con Hadoop Distributed File System (HDFS). |No |
| partitionedBy |Usata per specificare una proprietà **folderPath** e **fileName** dinamica per i dati della serie temporale. È ad esempio possibile specificare un **folderPath** contenente i parametri per ogni ora di dati. |No |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [Formato testo](data-factory-supported-file-and-compression-formats.md#text-format), [Formato JSON](data-factory-supported-file-and-compression-formats.md#json-format), [Formato AVRO](data-factory-supported-file-and-compression-formats.md#avro-format), [Formato OCR](data-factory-supported-file-and-compression-formats.md#orc-format) e [Formato Parquet](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Per copiare i file così come sono tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**, mentre i livelli supportati sono **Ottimale** e **Più veloce**. Per altre informazioni, vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useBinaryTransfer |Specificare se usare la modalità di trasferimento binario. I valori sono true per la modalità binaria (valore predefinito) e false per ASCII. Questa proprietà può essere usata solo quando il tipo di servizio collegato associato è di tipo: FtpServer. |No |

> [!NOTE]
> **fileName** e **fileFilter** non possono essere usati contemporaneamente.

### <a name="use-the-partionedby-property"></a>Usare la proprietà partionedBy
Come indicato nella sezione precedente, è possibile specificare un valore **folderPath** e **fileName** dinamico per i dati delle serie temporali con la proprietà **partitionedBy**.

Per informazioni sui set di dati delle serie temporali, sulla pianificazione e sulle sezioni, vedere gli articoli relativi a [creazione di set di dati](data-factory-create-datasets.md), [pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) e [creazione di pipeline](data-factory-create-pipelines.md).

#### <a name="sample-1"></a>Esempio 1

```json
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
In questo esempio {Slice} viene sostituito con il valore della variabile di sistema SliceStart di Data Factory nel formato specificato (AAAAMMGGHH). SliceStart fa riferimento all'ora di inizio della sezione. Il percorso cartella è diverso per ogni sezione. Ad esempio: wikidatagateway/wikisampledataout/2014100103 o wikidatagateway/wikisampledataout/2014100104.

#### <a name="sample-2"></a>Esempio 2

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```
In questo esempio l'anno, il mese, il giorno e l'ora di SliceStart vengono estratti in variabili separate che vengono usate dalle proprietà **folderPath** e **fileName**.

## <a name="copy-activity-properties"></a>Proprietà dell'attività di copia
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

D'altra parte, le proprietà disponibili nella sezione **typeProperties** dell'attività variano in base al tipo di attività. Per l'attività di copia, le proprietà del tipo variano in base ai tipi di origine e sink.

Nell'attività di copia con origine di tipo **FileSystemSource**, nella sezione **typeProperties** sono disponibili le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| ricorsiva |Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. |True, False (valore predefinito) |No |

## <a name="json-example-copy-data-from-ftp-server-to-azure-blob"></a>Esempio JSON: copiare i dati dal server FTP in BLOB di Azure
Questo esempio illustra come copiare dati dal server FTP in Archiviazione BLOB di Azure. I dati possono tuttavia essere copiati direttamente in uno qualsiasi dei sink indicati nella sezione [Archivi dati e formati supportati](data-factory-data-movement-activities.md#supported-data-stores-and-formats), usando l'attività di copia in Data Factory.  

Gli esempi seguenti specificano le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md):

* Un servizio collegato di tipo [FtpServer](#linked-service-properties)
* Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
* Un [set di dati](data-factory-create-datasets.md) di input di tipo [FileShare](#dataset-properties)
* Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties)
* Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [FileSystemSource](#copy-activity-properties) e [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties)

Nell'esempio i dati vengono copiati da un server FTP a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

### <a name="ftp-linked-service"></a>Servizio collegato FTP

Questo esempio usa l'autenticazione di base con il nome utente e la password in testo normale. È possibile anche usare uno dei tre metodi seguenti:

* Autenticazione anonima
* Autenticazione di base con credenziali crittografate
* FTP su SSL/TLS (FTPS)

Per i diversi tipi di autenticazione disponibili, vedere la sezione relativa al [servizio collegato FTP](#linked-service-properties).

```JSON
{
    "name": "FTPLinkedService",
    "properties": {
    "type": "FtpServer",
    "typeProperties": {
        "host": "myftpserver.com",           
        "authenticationType": "Basic",
        "username": "Admin",
        "password": "123456"
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
### <a name="ftp-input-dataset"></a>Set di dati di input FTP

Questo set di dati fa riferimento alla cartella FTP `mysharedfolder` e al file `test.csv`. La pipeline copia il file nella destinazione.

Impostando **external** su **true** si comunica al servizio data factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

```JSON
{
  "name": "FTPFileInput",
  "properties": {
    "type": "FileShare",
    "linkedServiceName": "FTPLinkedService",
    "typeProperties": {
      "folderPath": "mysharedfolder",
      "fileName": "test.csv",
      "useBinaryTransfer": true
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```

### <a name="azure-blob-output-dataset"></a>Set di dati di output del BLOB di Azure

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```JSON
{
    "name": "AzureBlobOutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "mycontainer/ftp/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            },
            "partitionedBy": [
                {
                    "name": "Year",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "yyyy"
                    }
                },
                {
                    "name": "Month",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "MM"
                    }
                },
                {
                    "name": "Day",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "dd"
                    }
                },
                {
                    "name": "Hour",
                    "value": {
                        "type": "DateTime",
                        "date": "SliceStart",
                        "format": "HH"
                    }
                }
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```


### <a name="a-copy-activity-in-a-pipeline-with-file-system-source-and-blob-sink"></a>Un'attività di copia in una pipeline con un'origine su file system e un sink BLOB

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **FileSystemSource** e il tipo di **sink** è impostato su **BlobSink**.

```JSON
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "FTPToBlobCopy",
            "inputs": [{
                "name": "FtpFileInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource"
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
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-08-24T18:00:00Z",
        "end": "2016-08-24T19:00:00Z"
    }
}
```
> [!NOTE]
> Per eseguire il mapping dal set di dati di origine alle colonne del set di dati sink, vedere [Mapping delle colonne del set di dati in Azure Data Factory](data-factory-map-columns.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

* Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md) .

* Per le istruzioni dettagliate sulla creazione di una pipeline con un'attività di copia, vedere l'[esercitazione sull'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
