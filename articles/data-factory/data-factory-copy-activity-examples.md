<properties 
	pageTitle="Esempi relativi all'uso dell'attività di copia in Data factory di Azure" 
	description="Fornisce esempi relativi all'uso dell'attività di copia in Data factory di Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/03/2015" 
	ms.author="spelluru"/>

# Esempi relativi all'uso dell'attività di copia in Data factory di Azure
È possibile usare **l'attività di copia** in una pipeline per copiare i dati da un'origine a un sink (destinazione) in un batch. Questo argomento fornisce alcuni esempi relativi all'uso dell'attività di copia in una pipeline di Data factory.

## Copiare dati da un database di SQL Server locale in un BLOB di Azure
In questo esempio, vengono definite una tabella di input e una tabella di output, che vengono quindi usate in un'attività di copia in una pipeline che copia i dati da un database di SQL Server locale in un BLOB di Azure.

### Presupposti
Questo esempio presuppone che siano già disponibili gli elementi di Data factory di Azure seguenti:

* Gruppo di risorse denominato **ADF**.
* Una Data factory di Azure denominata **CopyFactory**.
* Un gateway di gestione dati denominato **mygateway** creato e presente online.  

### Creare un servizio collegato per il database di SQL Server locale di origine
In questo passaggio viene creato un servizio collegato denominato **MyOnPremisesSQLDB** che punta a un database di SQL Server locale.

	{
	  "name": "MyOnPremisesSQLDB",
	  "properties": {
	    "type": "OnPremisesSqlServer",
	    "typeProperties": {
	      "connectionString": "Data Source=<servername>;Initial Catalog=<database>;Integrated Security=False;User ID=<username>;Password=<password>;",
	      "gatewayName": "mygateway"
	    }
	  }
	}

Tenere presente quanto segue:

- L'oggetto **type** è impostato su **OnPremisesSqlServer**.
- L'oggetto **connectionString** è impostato sulla stringa di connessione per un database di SQL Server. 
- L'oggetto **gatewayName** è impostato sul nome del gateway di gestione dati installato nel computer locale e registrato nel portale del servizio Data factory di Azure. 

Per informazioni dettagliate sugli elementi JSON per la definizione di un servizio collegato SQL locale, vedere l'articolo relativo al [servizio collegato SQL locale](https://msdn.microsoft.com/library/dn893523.aspx).
 
### Creare un servizio collegato per il BLOB di Azure di destinazione
In questo passaggio viene creato un servizio collegato denominato **MyAzureStorage** che punta a una risorsa di archiviazione BLOB di Azure.

	{
	  "name": "MyAzureStorage",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Tenere presente quanto segue:

- L'oggetto **type** è impostato su **AzureStorage**.
- Per l'oggetto **connectionString** specificare un nome e una chiave dell'account per la risorsa di archiviazione di Azure.

Per informazioni dettagliate sugli elementi JSON per la definizione di un servizio collegato di Archiviazione di Azure, vedere l'articolo relativo al [servizio collegato di Archiviazione di Azure](https://msdn.microsoft.com/library/dn893522.aspx).

### Tabella di input JSON
Il seguente script JSON definisce una tabella di input che fa riferimento a una tabella di SQL: **MyTable** in un database di SQL Server locale definito dal servizio collegato **MyOnPremisesSQLDB**. Si noti che **name** è il nome della tabella di Data factory di Azure e **tableName** è il nome della tabella SQL in un database di SQL Server.

	         
	{
	  "name": "MyOnPremTable",
	  "properties": {
	    "type": "SqlServerTable",
	    "linkedServiceName": "MyOnPremisesSQLDB",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

Tenere presente quanto segue:

- **type** è impostato su **SqlServerTable**.
- L'oggetto **tableName** è impostato su **MyTable**, che include i dati di origine. 
- L'oggetto **linkedServiceName** è impostato su **MyOnPremisesSQLDB**, il servizio collegato creato per il database SQL locale.

Per informazioni dettagliate sugli elementi JSON per la definizione di una tabella di Data factory che fa riferimento a una tabella di SQL Server, vedere la sezione relativa alle [proprietà Location del database SQL locale](https://msdn.microsoft.com/library/mt185722.aspx#OnPremSQL).

### Tabella di output JSON
Lo script JSON seguente definisce una tabella di output, **MyAzureBlob**, che fa riferimento a un BLOB di Azure, **MyBlob** nella cartella BLOB **MySubFolder** nel contenitore BLOB **MyContainer**.
         
	{
	  "name": "MyAzureBlob",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "MyAzureStorage",
	    "typeProperties": {
	      "folderPath": "MyContainer/MySubFolder",
	      "fileName": "MyBlob",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "EscapeChar": "$",
	        "NullValue": "NaN"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

Tenere presente quanto segue:
 
- **type** è impostato su **AzureBlob**.
- L'oggetto **folderPath** è impostato su **MyContainer/MySubFolder**, che include il BLOB contenente i dati copiati. 
- L'oggetto **fileName** è impostato su **MyBlob**, il BLOB che conterrà i dati di output.
- L'oggetto **linkedServiceName** è impostato su **MyAzureStorge**, il servizio collegato creato per la risorsa di archiviazione di Azure.    

Per informazioni dettagliate sugli elementi JSON per la definizione di una tabella di Data factory che fa riferimento a un BLOB di Azure, vedere la sezione relativa alle [proprietà Location del BLOB di Azure](https://msdn.microsoft.com/library/mt185722.aspx#AzureBlob).

### Pipeline (con attività di copia) JSON
In questo esempio, una pipeline **CopyActivityPipeline** è definita con le proprietà seguenti:

- La proprietà **type** è impostata su **CopyActivity**.
- **MyOnPremTable** viene specificato come input (tag **inputs**).
- **MyAzureBlob** viene specificato come output (tag **outputs**).
- La sezione **Transformation** contiene due sezioni secondarie: **source** e **sink**. Il tipo per source è impostato su **SqlSource** e il tipo per sink è impostato su **BlobSink**. **sqlReaderQuery** definisce la trasformazione (proiezione) da eseguire sull'origine. Per informazioni dettagliate su tutte le proprietà, vedere [Riferimento agli script JSON](https://msdn.microsoft.com/library/dn835050.aspx).

         
		{
		  "name": "CopyActivityPipeline",
		  "properties": {
		    "description": "This is a sample pipeline to copy data from SQL Server to Azure Blob",
		    "activities": [
		      {
		        "name": "CopyActivity",
		        "description": "description",
		        "type": "Copy",
		        "inputs": [
		          {
		            "name": "MyOnPremTable"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "MyAzureBlob"
		          }
		        ],
		        "typeProperties": {
		          "source": {
		            "type": "SqlSource",
		            "sqlReaderQuery": "select * from MyTable"
		          },
		          "sink": {
		            "type": "BlobSink"
		          }
		        }
		      }
		    ]
		  }
		}

Per informazioni dettagliate sugli elementi JSON per la definizione di una pipeline di Data factory, vedere le [informazioni di riferimento sul formato JSON della pipeline](https://msdn.microsoft.com/library/dn834988.aspx). Per informazioni sulle proprietà di SqlSource (come **sqlReaderQuery** nell'esempio) e BlobSink, vedere [Origini e sink supportati](https://msdn.microsoft.com/library/dn894007.aspx).


## Copiare i dati da un file system locale a un BLOB di Azure
È possibile usare l'attività di copia per copiare i file da un file system locale (condivisioni di rete Windows/Linux network oppure host locale di Windows) a un BLOB di Azure. L'host può essere Windows o Linux con Samba configurato. Il gateway di gestione dati deve essere installato in un computer Windows in grado di connettersi all'host.

### Presupposti
Questo esempio presuppone quanto segue:

- **Host**: il nome del server che ospita il file system è **\\contoso**.
- **Cartella**: il nome della cartella contenente i file di input è **marketingcampaign\\regionaldata\\{slice}, dove i file sono partizionati in una cartella denominata {slice}, ad esempio 2014121112 (anno 2014, mese 12, giorno 11, ore 12).

### Creare un servizio collegato di tipo file system locale
Il codice JSON dell’esempio seguente può essere usato per creare un servizio collegato denominato **FolderDataStore** di tipo **OnPremisesFileServer**.

	{
	  "name": "FolderDataStore",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\\contoso",
	      "userId": "username",
	      "password": "password",
	      "gatewayName": "ContosoGateway"
	    }
	  }
	}

> [AZURE.NOTE]Ricordarsi di usare il carattere di escape '' per i nomi di host e cartelle nei file JSON. Per **\\Contoso** usare **\\\\Contoso**.

Per informazioni dettagliate sugli elementi JSON per la definizione di un servizio collegato di tipo file system locale, vedere l'argomento relativo al [servizio collegato di tipo file system locale](https://msdn.microsoft.com/library/dn930836.aspx).

### Creare un servizio collegato per il BLOB di Azure di destinazione
Il codice JSON di esempio seguente può essere usato per creare un servizio collegato denominato **MyAzureStorage** di tipo **AzureStorageLinkedService**.

	{
	  "name": "MyAzureStorage",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

Per informazioni dettagliate sugli elementi JSON per la definizione di un servizio collegato di Archiviazione di Azure, vedere l'articolo relativo al [servizio collegato di Archiviazione di Azure](https://msdn.microsoft.com/library/dn893522.aspx).

### Creare la tabella di input
Lo script JSON seguente definisce una tabella di input che fa riferimento a un servizio collegato di tipo file system locale creato precedentemente.

	{
	  "name": "OnPremFileSource",
	  "properties": {
	    "type": "FileShare",
	    "linkedServiceName": "FolderDataStore",
	    "typeProperties": {
	      "folderPath": "marketingcampaign\\regionaldata\\{Slice}",
	      "partitionedBy": [
	        {
	          "name": "Slice",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyyMMddHH"
	          }
	        }
	      ]
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 24
	    }
	  }
	}

Per informazioni dettagliate sugli elementi JSON per la definizione di una tabella di Data factory che fa riferimento a un file system locale, vedere la sezione relativa alle [proprietà Location del file system locale](https://msdn.microsoft.com/library/mt185722.aspx#OnPremFileSystem).

### Creare la tabella di output
Lo script JSON seguente definisce una tabella di output, **AzureBlobDest**, che fa riferimento a un BLOB di Azure, **MyBlob** nella cartella BLOB **MySubFolder** nel contenitore BLOB **MyContainer**.
         
	{
	  "name": "AzureBlobDest",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "MyAzureStorage",
	    "typeProperties": {
	      "folderPath": "MyContainer/MySubFolder",
	      "fileName": "MyBlob",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "EscapeChar": "$",
	        "NullValue": "NaN"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

Per informazioni dettagliate sugli elementi JSON per la definizione di una tabella di Data factory che fa riferimento a un BLOB di Azure, vedere la sezione relativa alle [proprietà Location del BLOB di Azure](https://msdn.microsoft.com/library/mt185722.aspx#AzureBlob).

### Creare la pipeline
Il formato JSON della pipeline seguente definisce una pipeline con un'attività di copia che copia dati dal file system locale al BLOB di Azure di destinazione.
 
	{
	  "name": "CopyFileToBlobPipeline",
	  "properties": {
	    "activities": [
	      {
	        "name": "Ingress",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "OnPremFileSource"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobDest"
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
	        "policy": {
	          "concurrency": 4,
	          "timeout": "00:05:00"
	        }
	      }
	    ]
	  }
	}

La pipeline in questo esempio copia il contenuto in formato binario, senza analizzare o eseguire alcuna trasformazione. Si noti che è possibile sfruttare la **concorrenza** per copiare sezioni di file in parallelo. Ciò risulta utile se si vogliono spostare sezioni già spostate in precedenza.

> [AZURE.NOTE]Le attività di copia concorrenti con lo stesso host tramite percorso UNC con account utente diversi potrebbero provocare errori che indicano che non sono consentite più connessioni a un server o a una risorsa condivisa da parte dello stesso utente usando più di un nome utente. Si tratta di una limitazione del sistema operativo per motivi di sicurezza. Pianificare le attività di copia con gateway diversi o installare il gateway nell'host e usare "localhost" o "local" invece del percorso UNC.

Per informazioni dettagliate sugli elementi JSON per la definizione di una pipeline di Data factory, vedere le [informazioni di riferimento sul formato JSON della pipeline](https://msdn.microsoft.com/library/dn834988.aspx). Per informazioni sulle proprietà di FileSystemSource e BlobSink, vedere [Origini e sink supportati](https://msdn.microsoft.com/library/dn894007.aspx).

### Scenari aggiuntivi per l'uso delle tabelle di file system

#### Copiare tutti i file di una cartella specifica
Si noti che nel codice JSON di esempio è specificato solo **folderPath**.

	"typeProperties": {
		"folderPath": "marketingcampaign\\regionaldata\\na",
	}
 
#### Copiare tutti i file CSV della cartella specifica
Si noti che l'oggetto **fileFilter** è impostato su ****.csv**.

    "typeProperties": {
        "folderPath": "marketingcampaign\\regionaldata\\na",
        "fileFilter": "*.csv",
    }

#### Copiare un file specifico
Si noti che l'oggetto **fileFiter** è impostato su un file specifico, **201501.csv**.

    "typeProperties": {
        "folderPath": "marketingcampaign\\regionaldata\\na",
        "fileFilter": "201501.csv",
    }

## Copiare i dati da un database Oracle locale a un BLOB di Azure
È possibile usare l'attività di copia per copiare file da un database Oracle locale a un BLOB di Azure.

### Creare un servizio collegato per un database Oracle locale
Il codice JSON seguente può essere usato per creare un servizio collegato che punta a un database Oracle locale. Si noti che l'oggetto **type** è impostato su **OnPremisesOracle**.

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracle",
			"typeProperties": {			
	        	"ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        	"gatewayName": "SomeGateway"	
			}
	    }
	}

Per informazioni dettagliate sugli elementi JSON per la definizione di un servizio collegato Oracle locale, vedere l'articolo relativo al [servizio collegato Oracle locale](https://msdn.microsoft.com/library/dn948537.aspx).

### Creare un servizio collegato per il BLOB di Azure di destinazione
Il codice JSON dell’esempio seguente può essere usato per creare un servizio collegato denominato **MyAzureStorage** di tipo **AzureStorage**.

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorage",
			"typeProperties": {
	        	"connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
			}
	    }
	}

Per informazioni dettagliate sugli elementi JSON per la definizione di un servizio collegato di Archiviazione di Azure, vedere l'articolo relativo al [servizio collegato di Archiviazione di Azure](https://msdn.microsoft.com/library/dn893522.aspx).

### Creare la tabella di input
Il codice JSON di esempio seguente può essere usato per creare una tabella di Data factory di Azure che fa riferimento a una tabella in un database Oracle locale. Si noti che il valore **type** è impostato su **OracleTable**.

	{
	  "name": "TableOracle",
	  "properties": {
	    "type": "OracleTable",
	    "linkedServiceName": "OnPremOracleSource",
	    "typeProperties": {
	      "tableName": "LOG"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": "1"
	    },
	    "policy": {}
	  }
	}

Per informazioni dettagliate sugli elementi JSON per la definizione di una tabella di Data factory che fa riferimento a una tabella di un database Oracle locale, vedere la sezione relativa alle [proprietà Location del database Oracle locale](https://msdn.microsoft.com/library/mt185722.aspx#Oracle).

### Creare la tabella di output
Lo script JSON seguente definisce una tabella di output, **MyAzureBlob**, che fa riferimento a un BLOB di Azure, **MyBlob** nella cartella BLOB **MySubFolder** nel contenitore BLOB **MyContainer**.
         
	{
	  "name": "MyAzureBlob",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "AzureBlobDest",
	    "typeProperties": {
	      "folderPath": "MyContainer/MySubFolder",
	      "fileName": "MyBlob",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "EscapeChar": "$",
	        "NullValue": "NaN"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

Per informazioni dettagliate sugli elementi JSON per la definizione di una tabella di Data factory che fa riferimento a un BLOB di Azure, vedere la sezione relativa alle [proprietà Location del BLOB di Azure](https://msdn.microsoft.com/library/mt185722.aspx#AzureBlob).

### Creare la pipeline
La pipeline di esempio seguente include un'attività di copia che copia dati da una tabella del database Oracle a un BLOB di Archiviazione di Azure.
	
	{
	  "name": "PipelineCopyOracleToBlob",
	  "properties": {
	    "activities": [
	      {
	        "name": "CopyActivity",
	        "description": "copy slices of oracle records to azure blob",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "TableOracle"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "TableAzureBlob"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from LOG where "Timestamp" >= to_date(\\'{0:yyyy-MM-dd}\\', \\'YYYY-MM-DD\\') AND "Timestamp" < to_date(\\'{1:yyyy-MM-dd}\\', \\'YYYY-MM-DD\\')', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	        "policy": {
	          "concurrency": 3,
	          "timeout": "00:05:00"
	        }
	      }
	    ],
	    "start": "2015-03-01T00:00:00Z",
	    "end": "2015-03-15T00:00:00Z",
	    "isPaused": false
	  }
	}

Per informazioni dettagliate sugli elementi JSON per la definizione di una pipeline di Data factory, vedere le [informazioni di riferimento sul formato JSON della pipeline](https://msdn.microsoft.com/library/dn834988.aspx). Per informazioni sulle proprietà di OracleSource e BlobSink, vedere [Origini e sink supportati](https://msdn.microsoft.com/library/dn894007.aspx).

## Vedere anche

- [Copiare i dati con Data factory di Azure][adf-copyactivity]
- [Attività di copia - Informazioni di riferimento sugli script JSON](https://msdn.microsoft.com/library/dn835035.aspx)
- [Video: Introduzione all'attività di copia con Data factory di Azure][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!---HONumber=August15_HO7-->