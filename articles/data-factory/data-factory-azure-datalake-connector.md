---
title: Spostare dati da/in Azure Data Lake Store | Documentazione Microsoft
description: Informazioni su come spostare i dati da e in Archivio Azure Data Lake con Azure Data Factory
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.assetid: 25b1ff3c-b2fd-48e5-b759-bb2112122e30
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: jingwang
translationtype: Human Translation
ms.sourcegitcommit: b2d1a740782a20a7c6b7b8cec8335a41f16231f5
ms.openlocfilehash: 5a6a14e5fc8f6915b34f9667c4294a46c8591633
ms.lasthandoff: 02/09/2017


---
# <a name="move-data-to-and-from-azure-data-lake-store-using-azure-data-factory"></a>Spostare dati da e in Archivio Azure Data Lake con Azure Data Factory
Questo articolo illustra come usare l'attività di copia in una data factory di Azure per spostare dati da Azure Data Lake Store in un altro archivio dati e viceversa. Questo articolo si basa sull'articolo relativo alle [attività di spostamento dei dati](data-factory-data-movement-activities.md) , che offre una panoramica generale dello spostamento dei dati con l'attività di copia e delle combinazioni di archivio dati supportate.

> [!NOTE]
> Prima di creare una pipeline con un'attività di copia per spostare i dati da e in Azure Data Lake Store, creare un account Azure Data Lake Store. Per informazioni su Azure Data Lake Store, vedere [Introduzione ad Azure Data Lake Store con il portale di Azure](../data-lake-store/data-lake-store-get-started-portal.md).
>

## <a name="supported-authentication-types"></a>Tipi di autenticazione supportati
Il connettore di Azure Data Lake Store supporta l'autenticazione basata su **entità servizio** e l'autenticazione basata su **credenziali utente**. È consigliabile usare il primo tipo di autenticazione, in particolare per la copia pianificata di dati, per evitare il comportamento di scadenza dei token, che può verificarsi con il secondo. Per informazioni dettagliate sulla configurazione, vedere la sezione [Proprietà del servizio collegato Azure Data Lake Store](#azure-data-lake-store-linked-service-properties).

## <a name="copy-data-wizard"></a>Copia di dati guidata
Il modo più semplice per creare una pipeline che copia i dati in o da Azure Data Lake Store consiste nell'usare la Copia dati guidata. Vedere [Esercitazione: Creare una pipeline usando la Copia guidata](data-factory-copy-data-wizard-tutorial.md) per la procedura dettagliata sulla creazione di una pipeline attenendosi alla procedura guidata per copiare i dati.

Gli esempi seguenti forniscono le definizioni JSON di esempio da usare per creare una pipeline con il [portale di Azure](data-factory-copy-activity-tutorial-using-azure-portal.md), [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) o [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Tali esempi mostrano come copiare dati da/in Azure Data Lake Store e Archiviazione BLOB di Azure. I dati possono tuttavia essere copiati **direttamente** da qualsiasi origine a qualsiasi sink supportato. Per altre informazioni, vedere la sezione "Archivi dati e formati supportati" in [Spostare dati con l'attività di copia](data-factory-data-movement-activities.md).  

## <a name="sample-copy-data-from-azure-blob-to-azure-data-lake-store"></a>Esempio: Copiare dati dal BLOB di Azure in Archivio Azure Data Lake
L'esempio seguente mostra:

1. Un servizio collegato di tipo [AzureStorage](#azure-storage-linked-service-properties).
2. Un servizio collegato di tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureBlob](#azure-blob-dataset-type-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [BlobSource](#azure-blob-copy-activity-type-properties) e [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties).

L'esempio copia i dati di una serie temporale da un archivio BLOB di Azure in Azure Data Lake Store ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato Archiviazione di Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```

**Servizio collegato di Azure Data Lake:**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

> [!NOTE]
> Per informazioni dettagliate sulla configurazione, vedere la procedura nella sezione [Proprietà del servizio collegato Azure Data Lake Store](#azure-data-lake-store-linked-service-properties).
>

**Set di dati di input del BLOB di Azure:**

I dati vengono prelevati da un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella e il nome del file per il BLOB vengono valutati dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, e giorno dell'ora di inizio e il nome del file usa la parte dell'ora di inizio relativa all'ora. L'impostazione di "external" su "true" comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

```JSON
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
```

**Set di dati di output di Azure Data Lake:**

L'esempio copia i dati in un Archivio Azure Data Lake. Nuovi dati vengono copiati in Archivio Azure Data Lake ogni ora.

```JSON
{
    "name": "AzureDataLakeStoreOutput",
      "properties": {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/output/"
        },
        "availability": {
              "frequency": "Hour",
              "interval": 1
        }
      }
}
```


**Pipeline con un’attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **BlobSource** e il tipo **sink** è impostato su **AzureDataLakeStoreSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":
    {  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":
        [  
              {
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureBlobInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureDataLakeStoreOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                      },
                      "sink": {
                        "type": "AzureDataLakeStoreSink"
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

## <a name="sample-copy-data-from-azure-data-lake-store-to-azure-blob"></a>Esempio: Copiare dati da Archivio Azure Data Lake nel BLOB di Azure
L'esempio seguente mostra:

1. Un servizio collegato di tipo [AzureDataLakeStore](#azure-data-lake-linked-service-properties).
2. Un servizio collegato di tipo [AzureStorage](#azure-storage-linked-service-properties).
3. Un [set di dati](data-factory-create-datasets.md) di input di tipo [AzureDataLakeStore](#azure-data-lake-dataset-type-properties).
4. Un [set di dati](data-factory-create-datasets.md) di output di tipo [AzureBlob](#azure-blob-dataset-type-properties).
5. Una [pipeline](data-factory-create-pipelines.md) con attività di copia che usa [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) e [BlobSink](#azure-blob-copy-activity-type-properties).

L'esempio copia i dati di una serie temporale da Azure Data Lake Store in un archivio BLOB di Azure ogni ora. Le proprietà JSON usate in questi esempi sono descritte nelle sezioni riportate dopo gli esempi.

**Servizio collegato dell'Archivio Azure Data Lake:**

```JSON
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>"
        }
    }
}
```

> [!NOTE]
> Per informazioni dettagliate sulla configurazione, vedere la procedura nella sezione [Proprietà del servizio collegato Azure Data Lake Store](#azure-data-lake-store-linked-service-properties).
>

**Servizio collegato Archiviazione di Azure:**

```JSON
{
  "name": "StorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
    }
  }
}
```
**Set di dati di input di Azure Data Lake:**

Impostando **"external" su "true"** si comunica al servizio Data Factory che la tabella è esterna alla data factory e non è prodotta da un'attività al suo interno.

```JSON
{
    "name": "AzureDataLakeStoreInput",
      "properties":
    {
        "type": "AzureDataLakeStore",
        "linkedServiceName": "AzureDataLakeStoreLinkedService",
        "typeProperties": {
            "folderPath": "datalake/input/",
            "fileName": "SearchLog.tsv",
            "format": {
                "type": "TextFormat",
                "rowDelimiter": "\n",
                "columnDelimiter": "\t"
            }
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
```
**Set di dati di output del BLOB di Azure:**

I dati vengono scritti in un nuovo BLOB ogni ora (frequenza: ora, intervallo: 1). Il percorso della cartella per il BLOB viene valutato dinamicamente in base all'ora di inizio della sezione in fase di elaborazione. Il percorso della cartella usa le parti anno, mese, giorno e ora dell'ora di inizio.

```JSON
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
```

**Pipeline con l’attività di copia:**

La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline il tipo **source** è impostato su **AzureDataLakeStoreSource** e il tipo **sink** è impostato su **BlobSink**.

```JSON
{  
    "name":"SamplePipeline",
    "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
              {
                "name": "AzureDakeLaketoBlob",
                "description": "copy activity",
                "type": "Copy",
                "inputs": [
                  {
                    "name": "AzureDataLakeStoreInput"
                  }
                ],
                "outputs": [
                  {
                    "name": "AzureBlobOutput"
                  }
                ],
                "typeProperties": {
                    "source": {
                        "type": "AzureDataLakeStoreSource",
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

## <a name="azure-data-lake-store-linked-service-properties"></a>Proprietà del servizio collegato dell'Archivio Azure Data Lake
La tabella seguente fornisce una descrizione degli elementi JSON specifici per il servizio collegato di Azure Data Lake Store ed è possibile scegliere tra l'autenticazione basata su **entità servizio** e l'autenticazione basata su **credenziali utente**.

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **AzureDataLakeStore** | Sì |
| dataLakeStoreUri | Specificare le informazioni sull'account Archivio Azure Data Lake. È nel formato seguente: **https://[accountname].azuredatalakestore.net/webhdfs/v1** o **adl://[accountname].azuredatalakestore.net/**. | Sì |
| subscriptionId | ID sottoscrizione di Azure a cui il Data Lake Store appartiene. | Richiesto per il sink |
| resourceGroupName | Nome del gruppo di risorse di Azure a cui il Data Lake Store appartiene. | Richiesto per il sink |

### <a name="using-service-principal-authentication-recommended"></a>Uso dell'autenticazione basata su entità servizio (opzione consigliata)
Per usare l'autenticazione basata su entità servizio, è prima di tutto necessario registrare un'entità applicazione in Azure Active Directory (AAD) e concedere a tale entità l'accesso a Data Lake Store. È quindi possibile specificare le proprietà seguenti in Azure Data Factory con le informazioni corrispondenti per ID applicazione, chiave applicazione e tenant per la copia di dati da/verso Data Lake Store. Per informazioni su come configurare l'autenticazione e recuperare le informazioni necessarie, vedere [Autenticazione da servizio a servizio](../data-lake-store/data-lake-store-authenticate-using-active-directory.md).

> [!IMPORTANT]
> Quando si utilizza Copia guidata, per passare da una cartella a un'altra assicurarsi di concedere all'entità servizio almeno l'autorizzazione di lettura per la radice ADLS ("/") o il ruolo Lettore per l'account ADLS. In caso contrario, potrebbe essere visualizzato l'errore "Le credenziali fornite non sono valide".
>
> Se si crea un'entità servizio completamente nuova, o se ne aggiorna una, da AAD, potrebbero essere necessari alcuni minuti prima che l'entità sia operativa. Controllare innanzitutto l'entità servizio e la configurazione ACL ADLS. Se viene ancora visualizzato l'errore "Le credenziali fornite non sono valide", attendere qualche minuto e riprovare.
>

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì |
| servicePrincipalKey | Specificare la chiave dell'applicazione. | Sì |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì |

**Esempio: uso dell'autenticazione basata su entità servizio**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

### <a name="using-user-credential-authentication"></a>Uso dell'autenticazione basata su credenziali utente
In alternativa, è possibile usare l'autenticazione basata su credenziali utente per copiare dati da/verso Data Lake Store specificando le proprietà seguenti.

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| autorizzazione | Fare clic sul pulsante **Autorizza** nell'**editor di Data Factory** e immettere le credenziali per assegnare l'URL di autorizzazione generato automaticamente a questa proprietà. | Sì |
| sessionId | ID sessione OAuth dalla sessione di autorizzazione oauth. Ogni ID di sessione è univoco e può essere usato solo una volta. Questa impostazione viene generata automaticamente quando si usa l'editor di Data Factory. | Sì |

**Esempio: uso dell'autenticazione basata su credenziali utente**
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<session ID>",
            "authorization": "<authorization URL>",
            "subscriptionId": "<subscription of ADLS>",
            "resourceGroupName": "<resource group of ADLS>"
        }
    }
}
```

#### <a name="token-expiration"></a>Scadenza del token
Il codice di autorizzazione generato con il pulsante **Autorizza** ha una scadenza. Per le scadenze dei diversi tipi di account utente, vedere la tabella seguente. Alla **scadenza del token** di autenticazione potrebbe essere visualizzato il messaggio di errore seguente: Errore dell'operazione relativa alle credenziali: invalid_grant - AADSTS70002: Errore di convalida delle credenziali. AADSTS70008: La concessione dell'accesso specificata è scaduta o è stata revocata. ID traccia: d18629e8-af88-43c5-88e3-d8419eb1fca1 ID correlazione: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Timestamp: 2015-12-15 21:09:31Z".

| Tipo di utente | Scade dopo |
|:--- |:--- |
| Account utente NON gestiti da Azure Active Directory ((@hotmail.com,, @live.com, e così via). |12 ore |
| Account utente gestiti da Azure Active Directory (AAD) |14 giorni dopo l'esecuzione dell'ultima sezione. <br/><br/>90 giorni, se viene eseguita una sezione basata sul servizio collegato OAuth almeno una volta ogni 14 giorni. |

Se si modifica la password prima dell'ora di scadenza del token, il token scade immediatamente e viene visualizzato l'errore visto in questa sezione.

Per evitare/risolvere questo problema, alla **scadenza del token** ripetere l'autorizzazione usando il pulsante **Autorizza** e ridistribuire il servizio collegato. È anche possibile generare valori per le proprietà **sessionId** e **authorization** a livello di codice usando il codice riportato nella sezione seguente:

#### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>Per generare valori sessionId e authorization a livello di codice

```csharp
if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```
Per informazioni dettagliate sulle classi di Data Factory usate nel codice, vedere gli argomenti [AzureDataLakeStoreLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) e [AuthorizationSessionGetResponse Class](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Aggiungere un riferimento alla versione **2.9.10826.1824** di **Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll** per la classe WindowsFormsWebAuthenticationDialog usata nel codice.

## <a name="azure-data-lake-dataset-type-properties"></a>Proprietà del tipo di set di dati di Azure Data Lake
Per un elenco completo delle proprietà e delle sezioni JSON disponibili per la definizione dei set di dati, vedere l'articolo sulla [creazione di set di dati](data-factory-create-datasets.md). Le sezioni come struttura, disponibilità e criteri di un set di dati JSON sono simili per tutti i tipi di set di dati, ad esempio Azure SQL, BLOB di Azure, tabelle di Azure e così via.

La sezione **typeProperties** è diversa per ogni tipo di set di dati e contiene informazioni sulla posizione, il formato dei dati e così via nell'archivio dati. La sezione typeProperties per il set di dati di tipo **AzureDataLakeStore** include le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| folderPath |Percorso del contenitore e della cartella nell'Archivio Azure Data Lake. |Sì |
| fileName |Il nome del file in fileName nell'archivio di Azure Data Lake è facoltativo e distingue tra maiuscole e minuscole. fileName è facoltativo e non applica la distinzione tra maiuscole e minuscole. <br/><br/>Se si specifica un filename, l'attività, inclusa la copia, funziona sul file specifico.<br/><br/>Quando fileName non è specificato, la copia include tutti i file in folderPath per il set di dati di input.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il nome del file generato avrà il formato seguente: Data.<Guid>.txt, ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy è una proprietà facoltativa. Può essere utilizzata per specificare una proprietà folderPath dinamica e un nome file per i dati della serie temporale. Ad esempio, è possibile includere parametri per ogni ora di dati in folderPath. Per informazioni dettagliate ed esempi, vedere la sezione [Uso della proprietà partitionedBy](#using-partitionedby-property) . |No |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](#specifying-textformat), [JsonFormat](#specifying-jsonformat), [AvroFormat](#specifying-avroformat), [OrcFormat](#specifying-orcformat) e [ParquetFormat](#specifying-parquetformat). <br><br> Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**, mentre i livelli supportati sono **Optimal** (Ottimale) **Fastest** (Più veloce). Per altre informazioni, vedere la sezione [Specificare la compressione](#specifying-compression). |No |

### <a name="using-partitionedby-property"></a>Uso della proprietà partitionedBy
È possibile specificare una proprietà folderPath dinamica e il nome file per i dati di una serie temporale con la sezione **partitionedBy** , macro Data Factory e le variabili di sistema SliceStart e SliceEnd, che indicano l'ora di inizio e fine per una sezione di dati specificata.

Per altre informazioni sui set di dati delle serie temporali, sulla pianificazione e sulle sezioni, vedere gli articoli [Set di dati in Azure Data Factory](data-factory-create-datasets.md) e [Pianificazione ed esecuzione con Data Factory](data-factory-scheduling-and-execution.md).

#### <a name="sample-1"></a>Esempio 1

```JSON
"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
"partitionedBy":
[
    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
],
```
In questo esempio {Slice} viene sostituito con il valore della variabile di sistema SliceStart di Data Factory nel formato (AAAAMMGGHH) specificato. SliceStart fa riferimento all'ora di inizio della sezione. La proprietà folderPath è diversa per ogni sezione. For example: wikidatagateway/wikisampledataout/2014100103 or wikidatagateway/wikisampledataout/2014100104

#### <a name="sample-2"></a>Esempio 2
```JSON
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
In questo esempio l'anno, il mese, il giorno e l'ora di SliceStart vengono estratti in variabili separate che vengono usate dalle proprietà folderPath e fileName.

[!INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]

[!INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## <a name="azure-data-lake-copy-activity-type-properties"></a>Proprietà del tipo di attività di copia di tabelle di Azure Data Lake
Per un elenco completo delle sezioni e delle proprietà disponibili per la definizione delle attività, fare riferimento all'articolo [Creazione di pipeline](data-factory-create-pipelines.md). Per tutti i tipi di attività sono disponibili proprietà come nome, descrizione, tabelle di input e output e criteri.

Le proprietà disponibili nella sezione typeProperties dell'attività variano invece in base al tipo di attività. Per l'attività di copia variano in base ai tipi di origine e sink.

**AzureDataLakeStoreSource** supporta le proprietà seguenti della sezione **typeProperties**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| ricorsiva |Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. |True (valore predefinito), False |No |

**AzureDataLakeStoreSink** supporta le proprietà seguenti della sezione **typeProperties**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| copyBehavior |Specifica il comportamento di copia. |**PreserveHierarchy:** mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><br/>**FlattenHierarchy**: tutti i file della cartella di origine vengono creati nel primo livello della cartella di destinazione. Il nome dei file di destinazione viene generato automaticamente.<br/><br/>**MergeFiles**: unisce tutti i file della cartella di origine in un solo file. Se viene specificato il nome file/BLOB, il nome file unito sarà il nome specificato. In caso contrario, sarà il nome file generato automaticamente. |No |

[!INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[!INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[!INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Ottimizzazione delle prestazioni

A seconda del fatto che lo spostamento dei dati iniziale sia pianificato con un volume elevato di dati cronologici o con un carico di dati di produzione incrementale, Azure Data Factory include opzioni per migliorare le prestazioni di tali attività. Il parametro concurrency fa parte dell'**attività di copia** e definisce il numero di finestre attività diverse che verranno elaborate in parallelo. Il parametro **parallelCopies** definisce il parallelismo per l'esecuzione di una singola attività. È importante considerare la possibilità di usare questi parametri quando si progettano pipeline per lo spostamento dei dati con Azure Data Factory per raggiungere la massima velocità effettiva.

Per informazioni sui fattori chiave che influiscono sulle prestazioni dello spostamento dei dati, ovvero dell'attività di copia, in Azure Data Factory e sui vari modi per ottimizzare tali prestazioni, vedere la [Guida alle prestazioni delle attività di copia e all'ottimizzazione](data-factory-copy-activity-performance.md).

