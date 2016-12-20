---
title: Spostare dati da un server FTP | Documentazione Microsoft
description: Informazioni su come spostare dati da un server FTP usando Azure Data Factory.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: eea3bab0-a6e4-4045-ad44-9ce06229c718
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/20/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: f27d978eab3aba52ee9a51a5f284b592e3edd6af


---
# <a name="move-data-from-an-ftp-server-using-azure-data-factory"></a>Spostare dati da un server FTP usando Azure Data Factory
Questo articolo illustra come usare l'attività di copia in Azure Data Factory per spostare dati da un server FTP a un archivio dati sink supportato. Questo articolo si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md), che offre una panoramica generale dello spostamento dei dati con attività di copia e l'elenco degli archivi dati supportati come origini/sink.

Data Factory supporta attualmente solo lo spostamento di dati da un server FTP ad altri archivi dati, non da altri archivi dati a un server FTP. Supporta i server FTP locali e cloud.

Se si spostano dati da un server FTP **locale** a un archivio dati cloud, ad esempio l'archivio BLOB di Azure, installare e usare Gateway di gestione dati. Gateway di gestione dati è un agente client installato nel computer locale, che consente ai servizi cloud di connettersi alla risorsa locale. Per informazioni dettagliate sul gateway, vedere [Gateway di gestione dati](data-factory-data-management-gateway.md). Vedere l'articolo sullo [spostamento di dati tra sedi locali e cloud](data-factory-move-data-between-onprem-and-cloud.md) per istruzioni dettagliate sulla configurazione e sull'uso del gateway. Il gateway consente di connettersi a un server FTP anche se il server si trova in una macchina virtuale (VM) IaaS di Azure.

È possibile installare il gateway nello stesso computer locale o nella VM IaaS di Azure come server FTP. È tuttavia consigliabile installare il gateway in un computer separato o in una VM IaaS di Azure distinta, per evitare conflitti tra le risorse e ottenere prestazioni migliori. Quando si installa il gateway in un computer separato, questo deve poter accedere al server FTP.

## <a name="copy-data-wizard"></a>Copia di dati guidata
Il modo più semplice per creare una pipeline che copia i dati da un server FTP consiste nell'usare la procedura Copia di dati guidata. Per una procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati, vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md).

Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md).

## <a name="sample-copy-data-from-ftp-server-to-azure-blob"></a>Esempio: copiare dati dal server FTP al BLOB di Azure
Questo esempio illustra come copiare dati dal server FTP a un archivio BLOB di Azure. I dati possono tuttavia essere copiati **direttamente** in qualsiasi sink dichiarato [qui](data-factory-data-movement-activities.md#supported-data-stores-and-formats) usando l'attività di copia in Azure Data Factory.  

L'esempio include le entità di Data Factory seguenti:

* Un servizio collegato di tipo [FtpServer](#ftp-linked-service-properties).
* Un servizio collegato di tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service).
* Un [set di dati](data-factory-create-datasets.md) di input di tipo [FileShare](#fileshare-dataset-type-properties).
* Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
* Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [FileSystemSource](#ftp-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

Nell'esempio i dati vengono copiati da un server FTP a un BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato FTP**: questo esempio usa l'autenticazione di base con il nome utente e la password in testo normale. È possibile anche usare uno dei tre metodi seguenti:

* Autenticazione anonima
* Autenticazione di base con credenziali crittografate
* FTP su SSL/TLS (FTPS)

Per i diversi tipi di autenticazione disponibili, vedere la sezione relativa al [servizio collegato FTP](#ftp-linked-service-properties).

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

**Servizio collegato Archiviazione di Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Set di dati di input FTP**: questo set di dati fa riferimento alla cartella FTP `mysharedfolder` e al file `test.csv`. La pipeline copia il file nella destinazione.

Impostando "external": "true" si comunica al servizio Data Factory che il set di dati è esterno alla data factory e non è prodotto da un'attività al suo interno.

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


**Set di dati di output del BLOB di Azure**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

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



**Pipeline con attività di copia**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **FileSystemSource** e il tipo di **sink** è impostato su **BlobSink**.

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

## <a name="ftp-linked-service-properties"></a>Proprietà del servizio collegato FTP
La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato FTP.

| Proprietà | Descrizione | Obbligatorio | Predefinito |
| --- | --- | --- | --- |
| type |La proprietà type deve essere impostata su FtpServer |Sì |&nbsp; |
| host |Nome o indirizzo IP del server FTP |Sì |&nbsp; |
| authenticationType |Specificare il tipo di autenticazione |Sì |Di base, anonimo |
| username |Utente che ha accesso al server FTP |No |&nbsp; |
| password |Password per l'utente (nome utente) |No |&nbsp; |
| encryptedCredential |Credenziali crittografate per accedere al server FTP |No |&nbsp; |
| gatewayName |Nome del gateway di Gateway di gestione dati per connettersi a un server FTP locale |No |&nbsp; |
| port |Porta su cui è in ascolto il server FTP |No |21 |
| enableSsl |Specificare se usare FTP su un canale SSL/TLS |No |true |
| enableServerCertificateValidation |Specificare se abilitare la convalida del certificato SSL del server quando si usa FTP sul canale SSL/TLS |No |true |

### <a name="using-anonymous-authentication"></a>Uso dell'autenticazione anonima
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

### <a name="using-username-and-password-in-plain-text-for-basic-authentication"></a>Uso di nome utente e password in testo normale per l'autenticazione di base
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


### <a name="using-port-enablessl-enableservercertificatevalidation"></a>Uso di porta, enableSsl, enableServerCertificateValidation
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

### <a name="using-encryptedcredential-for-authentication-and-gateway"></a>Uso di encryptedCredential per autenticazione e gateway
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

Vedere [Spostare dati tra origini locali e il cloud con Gateway di gestione dati](data-factory-move-data-between-onprem-and-cloud.md) per informazioni dettagliate sull'impostazione delle credenziali per un'origine dati FTP locale.

[!INCLUDE [data-factory-file-share-dataset](../../includes/data-factory-file-share-dataset.md)]

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="ftp-copy-activity-type-properties"></a>Proprietà del tipo di attività di copia FTP
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia, le proprietà del tipo variano in base ai tipi di origine e sink.

[!INCLUDE [data-factory-file-system-source](../../includes/data-factory-file-system-source.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni
Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

## <a name="next-steps"></a>Passaggi successivi
Vedere gli articoli seguenti:

* [Esercitazione dell'attività di copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) per le istruzioni dettagliate della creazione di una pipeline con un'attività di copia.



<!--HONumber=Nov16_HO3-->


