---
title: Spostare dati da e in un file system | Microsoft Docs
description: Informazioni su come spostare i dati da e in un file system locale usando Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: jhubbard
editor: monicar

ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/01/2016
ms.author: jingwang

---
# <a name="move-data-to-and-from-an-on-premises-file-system-by-using-azure-data-factory"></a>Spostare dati da e in un file system locale usando Azure Data Factory
Questo articolo illustra come usare l'attività di copia di Azure Data Factory per spostare dati da e in un file system locale. Per un elenco di archivi dati che possono essere usati come origini o sink con il file system locale, vedere [Archivi dati supportati](data-factory-data-movement-activities.md#supported-data-stores) . Questo articolo si basa sull'articolo [Spostamento di dati e attività di copia](data-factory-data-movement-activities.md) , che offre una panoramica generale dello spostamento dei dati con attività di copia e delle combinazioni di archivi dati supportati.

Data Factory supporta la connessione da e verso il file system locale tramite il Gateway di gestione dati. Per informazioni sul Gateway di gestione dati e per istruzioni dettagliate sulla configurazione del gateway vedere l'articolo [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md).

> [!NOTE]
> Non è necessario installare altri file binari per la comunicazione da e verso il file system locale, tranne il Gateway di gestione dati.
> 
> Per suggerimenti sulla risoluzione di problemi correlati alla connessione o al gateway, vedere [Risoluzione dei problemi del gateway](data-factory-data-management-gateway.md#troubleshoot-gateway-issues).
> 
> 

## <a name="linux-file-share"></a>Condivisione file Linux
Eseguire i due passaggi seguenti per usare una condivisione di file Linux con il servizio collegato del file server:

* Installare [Samba](https://www.samba.org/) sul server Linux.
* Installare e configurare il Gateway di gestione dati su un server Windows. L'installazione di un Gateway di gestione dati su un server Linux non è supportata.

## <a name="copy-wizard"></a>Copia guidata
Il modo più semplice per creare una pipeline che copia i dati da e nel file system locale consiste nell'usare la Copia guidata. Per una procedura dettagliata, vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md).

Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Questi esempi mostrano come copiare dati da e nel file system locale e in Archiviazione BLOB di Azure. È tuttavia possibile copiare dati *direttamente* da una qualsiasi delle origini in uno qualsiasi dei sink elencati in [Sink e origini supportate](data-factory-data-movement-activities.md#supported-data-stores) tramite l'attività di copia in Azure Data Factory.

## <a name="sample:-copy-data-from-an-on-premises-file-system-to-azure-blob-storage"></a>Esempio: Copiare i dati da un file system locale in Archiviazione BLOB di Azure
Questo esempio illustra come copiare dati da un file system locale in Archiviazione BLOB di Azure.

L'esempio include le entità della data factory seguenti:

* Un servizio collegato di tipo [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties).
* Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties).
* Un [set di dati](data-factory-create-datasets.md) di input di tipo [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties).
* Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
* La [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

L'esempio seguente copia i dati appartenenti a una serie temporale da un file system locale in Archiviazione BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

Come primo passaggio configurare il Gateway di gestione dati secondo le istruzioni in [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md).

**Servizio collegato del file server locale:**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

Si consiglia di usare la proprietà **encryptedCredential** anziché le proprietà **userid** e **password**. Per informazioni dettagliate su questo servizio collegato, vedere l'articolo relativo al [servizio collegato del file server](#onpremisesfileserver-linked-service-properties).

**Servizio collegato Archiviazione di Azure:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Set di dati di input del file system locale:**

I dati vengono prelevati da un nuovo file ogni ora. Le proprietà folderPath e fileName vengono determinate in base all'ora di inizio della sezione.  

L'impostazione di `"external": "true"` comunica a Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

    {
      "name": "OnpremisesFileSystemInput",
      "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
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
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Set di dati di ouput di Archiviazione BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **FileSystemSource** e il tipo di **sink** è impostato su **BlobSink**.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T19:00:00",
        "description":"Pipeline for copy activity",
        "activities":[  
          {
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "OnpremisesFileSystemInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
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
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

## <a name="sample:-copy-data-from-azure-sql-database-to-an-on-premises-file-system"></a>Esempio: Copiare i dati dal database SQL di Azure in un file system locale
L'esempio seguente mostra:

* Un servizio collegato di tipo AzureSqlDatabase.
* Un servizio collegato di tipo OnPremisesFileServer.
* Un set di dati di input di tipo AzureSqlTable.
* Un set di dati di output di tipo FileShare.
* La pipeline con attività di copia che usa SqlSource e FileSystemSink.

L'esempio copia i dati di una serie temporale da una tabella di Azure SQL a un sistema di file locale ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato SQL di Azure:**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

**Servizio collegato del file server locale:**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia.<region>.corp.<company>.com",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

Si consiglia di usare la proprietà **encryptedCredential** anziché le proprietà **userid** e **password**. Per informazioni dettagliate su questo servizio collegato, vedere l'articolo relativo al [servizio collegato del file system](#onpremisesfileserver-linked-service-properties).

**Set di dati di input SQL Azure:**

L'esempio presuppone che sia stata creata una tabella "MyTable" in SQL Azure e che contenga una colonna denominata "timestampcolumn" per i dati di una serie temporale.

L'impostazione di ``“external”: ”true”`` comunica a Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Set di dati di output del file system locale:**

I dati vengono copiati in un nuovo file ogni ora. folderPath e fileName per il BLOB vengono determinati in base all'ora di inizio della sezione.

    {
      "name": "OnpremisesFileSystemOutput",
      "properties": {
        "type": "FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
          "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
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
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Pipeline con un'attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo **source** è impostato su **SqlSource** e il tipo **sink** è impostato su **FileSystemSink**. La query SQL specificata per la proprietà **SqlReaderQuery** consente di selezionare i dati da copiare nell'ultima ora.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2015-06-01T18:00:00",
        "end":"2015-06-01T20:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSQLInput"
              }
            ],
            "outputs": [
              {
                "name": "OnpremisesFileSystemOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
              },
              "sink": {
                "type": "FileSystemSink"
              }
            },
           "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 3,
              "timeout": "01:00:00"
            }
          }
         ]
       }
    }

## <a name="on-premises-file-server-linked-service-properties"></a>Proprietà del servizio collegato del file server locale
È possibile collegare un file system locale a una data factory di Azure con il servizio collegato del file server locale. La tabella seguente include le descrizioni degli elementi JSON specifici del servizio collegato del file server locale.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |Verificare che la proprietà type sia impostata su **OnPremisesFileServer**. |Sì |
| host |Specifica il percorso radice della cartella da copiare. Usare il carattere di escape '\' per i caratteri speciali nella stringa. Per ottenere alcuni esempi, vedere [Servizio collegato di esempio e definizioni del set di dati](#sample-linked-service-and-dataset-definitions) . |Sì |
| userid |Specificare l'ID dell'utente che ha accesso al server. |No (se si sceglie encryptedCredential) |
| password |Specificare la password per l'utente (userid). |No (se si sceglie encryptedCredential) |
| encryptedCredential |Specificare le credenziali crittografate che è possibile ottenere eseguendo il cmdlet New-AzureRmDataFactoryEncryptValue. |No (se si sceglie di specificare ID utente e password in testo normale) |
| gatewayName |Specifica il nome del gateway che Data Factory deve usare per connettersi al file server locale. |Sì |

Per informazioni dettagliate sull'impostazione delle credenziali per un'origine dati file system locale, vedere [Impostazione delle credenziali e della sicurezza](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

### <a name="sample-linked-service-and-dataset-definitions"></a>Servizio collegato di esempio e definizioni del set di dati
| Scenario | Host nella definizione del servizio collegato | folderPath nella definizione del set di dati |
| --- | --- | --- |
| Cartella locale nel computer del gateway di gestione dati:  <br/><br/>Esempi: D:\\\* o D:\cartella\sottocartella\\* |D:\\\\ (per Gateway di gestione dati versione 2.0 e successive) <br/><br/> localhost (per le versioni precedenti alla versione 2.0 di Gateway di gestione dati) |.\\\\ o cartella\\\\sottocartella (per il Gateway di gestione dati versione 2.0 e successive) <br/><br/>D:\\\\ o D:\\\\cartella\\\\sottocartella (per la versione del gateway precedente a 2.0) |
| Cartella condivisa remota:  <br/><br/>Esempi: \\\\myserver\\share\\\* o \\\\myserver\\share\\cartella\\sottocartella\\* |\\\\\\\\myserver\\\\share |.\\\\ o cartella\\\\sottocartella |

**Per determinare la versione del gateway:**

1. Avviare [Gestione configurazione del gateway di gestione dati](data-factory-data-management-gateway.md#data-management-gateway-configuration-manager) sul computer.
2. Passare alla scheda **Guida** .

> [!NOTE]
> È consigliabile [eseguire l'aggiornamento a Gateway di gestione dati versione 2.0 o successive](data-factory-data-management-gateway.md#update-data-management-gateway) per sfruttare le funzionalità e le correzioni più recenti.
> 
> 

**Esempio: Uso di nome utente e password in testo normale**

    {
      "Name": "OnPremisesFileServerLinkedService",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "\\\\Contosogame-Asia",
          "userid": "Admin",
          "password": "123456",
          "gatewayName": "mygateway"
        }
      }
    }

**Esempio: Uso di encryptedcredential**

    {
      "Name": " OnPremisesFileServerLinkedService ",
      "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
          "host": "D:\\",
          "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
          "gatewayName": "mygateway"
        }
      }
    }

## <a name="on-premises-file-system-dataset-type-properties"></a>Proprietà del tipo del set di dati del file system locale
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione di set di dati, vedere l'articolo [Creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati.

La sezione typeProperties è diversa per ogni tipo di set di dati e fornisce informazioni come la posizione e il formato dei dati nell'archivio dati. La sezione typeProperties per il set di dati di tipo **FileShare** presenta le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| folderPath |Specifica il percorso secondario della cartella. Usare il carattere di escape '\' per i caratteri speciali nella stringa. Per ottenere alcuni esempi, vedere [Servizio collegato di esempio e definizioni del set di dati](#sample-linked-service-and-dataset-definitions) .<br/><br/>È possibile combinare questa proprietà con **partitionBy** per ottenere percorsi di cartelle basati su data e ora di inizio/fine delle sezioni. |Sì |
| fileName |Specificare il nome del file in **folderPath** se si vuole che la tabella faccia riferimento a un file specifico nella cartella. Se non si specifica alcun valore per questa proprietà, la tabella punta a tutti i file nella cartella.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il formato del nome del file generato è il seguente: <br/><br/>`Data.<Guid>.txt` Esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |È possibile usare partitionedBy per specificare un valore folderPath/fileName dinamico per i dati di una serie temporale. Un esempio è folderPath con parametri per ogni ora di dati. |No |
| Format |Sono supportati i tipi di formato seguenti: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per informazioni dettagliate, vedere le sezioni [Specifica TextFormat](#specifying-textformat), [Specifica AvroFormat](#specifying-avroformat), [Specifica JsonFormat](#specifying-jsonformat), [Specifica OrcFormat](#specifying-orcformat) e [Specifica ParquetFormat](#specifying-parquetformat). Se si desidera copiare i file così come sono tra archivi basati su file (copia binaria), è possibile saltare la sezione formato sia nella definizione del set di dati di output che in quella di input. |No |
| fileFilter |Specificare un filtro da usare per selezionare un sottoinsieme di file in folderPath anziché tutti i file. <br/><br/>I valori consentiti sono: `*` (più caratteri) e `?` (carattere singolo).<br/><br/>Esempio 1: "fileFilter": "*. log"<br/>Esempio 2: "fileFilter": 2014 - 1-?. txt"<br/><br/>Si noti che fileFilter è applicabile per un set di dati di input FileShare. |No |
| compressione |Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate** e **BZip2**. I livelli supportati sono **Ottimale** e **Più veloce**. Le impostazioni di compressione non sono attualmente supportate per i dati **AvroFormat** oppure **OrcFormat**. Per altre informazioni, vedere la sezione [Supporto della compressione](#compression-support). |No |

> [!NOTE]
> Non è possibile usare fileName e fileFilter contemporaneamente.
> 
> 

### <a name="using-partitionedby-property"></a>Uso della proprietà partitionedBy
Come indicato nella sezione precedente, partitionedBy può essere usato per specificare valori fileName e folderPath dinamici per i dati di una serie temporale. È possibile eseguire questa operazione con le macro della data factory e le variabili di sistema SliceStart e SliceEnd, che indicano il periodo di tempo logico per una sezione di dati specificata.

Per altri dettagli sui set di dati delle serie temporali, sulla pianificazione e sulle sezioni, vedere gli articoli [Creazione di set di dati](data-factory-create-datasets.md), [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md) e [Creazione di pipeline](data-factory-create-pipelines.md).

#### <a name="sample-1:"></a>Esempio 1.
    "folderPath": "wikidatagateway/wikisampledataout/{Slice}",
    "partitionedBy":
    [
        { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
    ],

In questo esempio {Slice} viene sostituito con il valore della variabile di sistema SliceStart di Data Factory nel formato (AAAAMMGGHH). SliceStart fa riferimento all'ora di inizio della sezione. La proprietà folderPath è diversa per ogni sezione. Ad esempio: wikisampledataout/wikidatagateway/2014100103 o wikisampledataout/wikidatagateway/2014100104.

#### <a name="sample-2:"></a>Esempio 2:
    "folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
    "fileName": "{Hour}.csv",
    "partitionedBy":
     [
        { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
        { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
        { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
        { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
    ],

In questo esempio l'anno, il mese, il giorno e l'ora di SliceStart vengono estratti in variabili separate usate dalle proprietà folderPath e fileName.

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="file-share-copy-activity-type-properties"></a>Proprietà del tipo di attività di copia di una condivisione file
**FileSystemSource** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| ricorsiva |Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. |True, False (valore predefinito) |No |

**FileSystemSink** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| copyBehavior |Definisce il comportamento di copia quando l'origine è BlobSource o FileSystem. |**PreserveHierarchy:** mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><br/>**FlattenHierarchy**: tutti i file della cartella di origine vengono creati nel primo livello della cartella di destinazione. Il nome dei file di destinazione viene generato automaticamente.<br/><br/>**MergeFiles**: unisce tutti i file della cartella di origine in un solo file. Se il nome file/BLOB viene specificato, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome file generato automaticamente. |No |

### <a name="recursive-and-copybehavior-examples"></a>esempi ricorsivi e copyBehavior
Questa sezione descrive il comportamento derivante dell'operazione di copia per diverse combinazioni di valori ricorsivi e delle proprietà copyBehavior.

| valore ricorsivo | valore di copyBehavior | Comportamento risultante |
| --- | --- | --- |
| true |preserveHierarchy |Per una cartella di origine Cartella1 con la struttura seguente,<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la cartella di destinazione Cartella1 viene creata con la stessa struttura dell'origine:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5 |
| true |flattenHierarchy |Per una cartella di origine Cartella1 con la struttura seguente,<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File5 |
| true |mergeFiles |Per una cartella di origine Cartella1 con la struttura seguente,<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente: <br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 + File3 + File4 + File 5 viene unito in un file con nome generato automaticamente. |
| false |preserveHierarchy |Per una cartella di origine Cartella1 con la struttura seguente,<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |flattenHierarchy |Per una cartella di origine Cartella1 con la struttura seguente,<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;nome generato automaticamente per File2<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |
| false |mergeFiles |Per una cartella di origine Cartella1 con la struttura seguente,<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File1<br/>&nbsp;&nbsp;&nbsp;&nbsp;File2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Sottocartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;File5<br/><br/>la Cartella1 di destinazione viene creata con la struttura seguente:<br/><br/>Cartella1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Il contenuto di File1 + File2 viene unito in un file con un nome file generato automaticamente.<br/>&nbsp;&nbsp;&nbsp;&nbsp;Nome generato automaticamente per File1<br/><br/>La sottocartella1 con File3, File4 e File5 non viene considerata. |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Prestazioni e ottimizzazione
 Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

<!--HONumber=Oct16_HO2-->


