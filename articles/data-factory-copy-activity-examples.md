<properties 
	pageTitle="Esempi relativi all'utilizzo di attività di copia nella Factory di dati di Azure" 
	description="Vengono forniti esempi per da utilizzare un'attività di copia in una factory di dati di Azure." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Esempi relativi all'utilizzo di attività di copia nella Factory di dati di Azure
È possibile usare **l'attività di copia** in una pipeline per copiare i dati da un'origine a un sink (destinazione) in un batch. In questo argomento fornisce alcuni esempi di utilizzo dell'attività di copia in una pipeline di dati Factory. Per una panoramica dettagliata dell'attività di copia e degli scenari principali supportati, vedere [Copiare dati con Data factory di Azure][adf-copyactivity].

## Copia dei dati da un database di SQL Server locale in un blob di Azure
In questo esempio, vengono definite una tabella di input e una tabella di output, che vengono quindi usate in un'attività di copia in una pipeline che copia i dati da un database di SQL Server locale in un BLOB di Azure.

### Presupposti
Questo esempio si presuppone che si disponga già i seguenti elementi di Factory di dati di Azure:

* Gruppo di risorse denominato **ADF**.
* Una Data factory di Azure denominata **CopyFactory**.
* Un gateway di gestione di dati denominato **mygateway** viene creato ed è in linea.  

### Creare un servizio per il database di SQL Server di origine locale collegato
In questo passaggio, si crea un servizio collegato denominato **MyOnPremisesSQLDB** che punta a un database di SQL Server locale.

	{
	    "name": "MyOnPremisesSQLDB",
	    "properties":
	    {
	        "type": "OnPremisesSqlLinkedService",
	        "connectionString": "Data Source=<servername>;Initial Catalog=<database>;Integrated Security=False;User ID=<username>;Password=<password>;",
	        "gatewayName": "mygateway"
	    }
	}

Tenere presente quanto segue:

- **tipo** è impostato su **OnPremisesSqlLinkedService**.
- **connectionString** è impostato su una stringa di connessione per un database SQL Server. 
- **gatewayName** è impostata sul nome del Gateway di gestione dati una volta installato nel computer locale e registrata con il portale dei servizi Azure dati Factory. 

Vedere [servizio collegato SQL locale](https://msdn.microsoft.com/library/dn893523.aspx) per informazioni dettagliate su JSON elementi per la definizione SQL locale collegato servizio.
 
### Creare un servizio collegato per la destinazione blob di Azure
In questo passaggio, si crea un servizio collegato denominato **MyAzureStorage** che fa riferimento a una risorsa di archiviazione blob di Azure.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Tenere presente quanto segue:

- **tipo** è impostato su **AzureStorageLinkedService**.
- **connectionString** : specificare il nome dell'account e la chiave per l'archiviazione di Azure account.

Vedere [servizio collegato di archiviazione Azure](https://msdn.microsoft.com/library/dn893522.aspx) per informazioni dettagliate sugli elementi JSON per definire un servizio di archiviazione Azure collegate servizio.

### Tabella di input JSON
Il seguente script JSON definisce una tabella di input che fa riferimento a una tabella di SQL: **MyTable** in un database di SQL Server locale definito dal servizio collegato **MyOnPremisesSQLDB**. Si noti che **name** è il nome della tabella di Data factory di Azure e **tableName** è il nome della tabella SQL in un database di SQL Server.

         
	{
		"name": "MyOnPremTable",
    	"properties":
   		{
			"location":
    		{
    			"type": "OnPremisesSqlServerTableLocation",
    			"tableName": "MyTable",
    			"linkedServiceName": "MyOnPremisesSQLDB"
    		},
    		"availability":
   			{
    			"frequency": "Hour",
    			"interval": 1
   			}
 		}
	}

Tenere presente quanto segue:

- **tipo** è impostato su **OnPremisesSqlServerTableLocation**.
- **tableName** è impostato su **MyTable**, che contiene i dati di origine. 
- **linkedServiceName** è impostato su **MyOnPremisesSQLDB**, collegate servizio creato per il database SQL locale.

Vedere [Proprietà indirizzo di On-Premise SQL](https://msdn.microsoft.com/library/dn894089.aspx#OnPremSQL) per informazioni dettagliate sugli elementi JSON per definire una tabella dati Factory che fa riferimento a una tabella di SQL Server.

### Tabella di output JSON
Il seguente script JSON definisce una tabella di output: **MyAzureBlob**, che fa riferimento a un blob di Azure: **MyBlob** nella cartella blob: **MySubFolder** nel contenitore di blob: **MyContainer**.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Tenere presente quanto segue:
 
- **tipo** è impostato su **AzureBlobLocation**.
- **folderPath** è impostato su **MyContainer/MySubFolder**, che contiene il blob che contiene i dati copiati. 
- **fileName** è impostato su **MyBlob**, il blob contenenti i dati di output.
- **linkedServiceName** è impostato su **MyAzureStorge**, collegate servizio creato per l'archiviazione di Azure.    

Vedere [Proprietà indirizzo di blob di Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) per informazioni dettagliate sugli elementi JSON per definire una tabella dati Factory che fa riferimento a un blob di Azure.

### Pipeline (con attività di copia) JSON
In questo esempio, una pipeline **CopyActivityPipeline** è definita con le proprietà seguenti:

- La proprietà **type** è impostata su **CopyActivity**.
- **MyOnPremTable** viene specificato come input (tag **inputs**).
- **MyAzureBlob** viene specificato come output (tag **outputs**).
- La sezione **Transformation** contiene due sezioni secondarie: **source** e **sink**. Il tipo per source è impostato su **SqlSource** e il tipo per sink è impostato su **BlobSink**. **sqlReaderQuery** definisce la trasformazione (proiezione) da eseguire sull'origine. Per informazioni dettagliate su tutte le proprietà, vedere [Riferimento agli script JSON][json-script-reference].

         
		{
		    "name": "CopyActivityPipeline",
    		"properties":
    		{
				"description" : "This is a sample pipeline to copy data from SQL Server to Azure Blob",
        		"activities":
        		[
      				{
						"name": "CopyActivity",
						"description": "description", 
						"type": "CopyActivity",
						"inputs":  [ { "name": "MyOnPremTable"  } ],
						"outputs":  [ { "name": "MyAzureBlob" } ],
						"transformation":
	    				{
							"source":
							{
								"type": "SqlSource",
                    			"sqlReaderQuery": "select * from MyTable"
							},
							"sink":
							{
                        		"type": "BlobSink"
							}
	    				}
      				}
        		]
    		}
		}

Vedere [riferimento JSON Pipeline](https://msdn.microsoft.com/library/dn834988.aspx) per informazioni dettagliate sugli elementi JSON per definire una pipeline di dati Factory e [supportate origini e sink](https://msdn.microsoft.com/library/dn894007.aspx) per le proprietà di SqlSource (ad esempio: **sqlReaderQuery **nell'esempio) e BlobSink. 


## Copiare i dati da un file system locale in un blob di Azure
È possibile utilizzare l'attività di copia per copiare i file da un file system locale (condivisioni di rete di Windows/Linux o host locale di Windows) in un Blob di Azure. L'host può essere Windows o Linux con Samba configurato. Gateway di gestione dati deve essere installato in un computer Windows che può connettersi all'host.

### Presupposti
Questo esempio si presuppone quanto segue:

- **Host** -nome del server che ospita il file system è: **\contoso**.
- **Cartella** -nome della cartella che contiene i file di input è: **marketingcampaign\regionaldata\ {slice}, dove i file vengono partizionati in una cartella denominata {slice}, ad esempio 2014121112 (2014 anno, mese 12, il giorno 11, ore 12). 
### Creare un servizio di sistema collegato file locali
Nell'esempio seguente viene JSON può essere utilizzato per creare un servizio collegato denominato **FolderDataStore** di tipo **OnPremisesFileSystemLinkedService**.

	{
	    "name": "FolderDataStore",
	    "properties": {
	        "type": "OnPremisesFileSystemLinkedService",
	        "host": "\contoso",
	        "userId": "username",
	        "password": "password",
	        "gatewayName": "ContosoGateway"
	    }
	}

> [AZURE.NOTE]Ricordarsi di utilizzare il carattere di escape ' per i nomi degli host e le cartelle nel file JSON. Per **\Contoso**, utilizzare **\Contoso**.

Vedere [servizio collegato per il sistema File locale](https://msdn.microsoft.com/library/dn930836.aspx) per informazioni dettagliate sugli elementi JSON per definire un file system locale collegato servizio.

### Creare un servizio collegato per la destinazione blob di Azure
Nell'esempio seguente viene JSON può essere utilizzato per creare un servizio collegato denominato **MyAzureStorage** di tipo **AzureStorageLinkedSerivce**.

	{
	    "name": "MyAzureStorage",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Vedere [servizio collegato di archiviazione Azure](https://msdn.microsoft.com/library/dn893522.aspx) per informazioni dettagliate sugli elementi JSON per definire un servizio di archiviazione Azure collegate servizio.

### Creare la tabella di input
Il seguente script JSON definisce una tabella di input che fa riferimento a un servizio di sistema collegato file locale creato in precedenza.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\{Slice}",
	            "partitionedBy": [
	                { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }
	            ],
	            "linkedServiceName": "FolderDataStore"
	        },
	        "availability": {
	            "waitOnExternal": { },
	            "frequency": "Hour",
	            "interval": 24
	        }
	    }
	}

Vedere [Proprietà indirizzo di File System locale](https://msdn.microsoft.com/library/dn894089.aspx#OnPremFileSystem) per informazioni dettagliate sugli elementi JSON per definire una tabella dati Factory che fa riferimento a un file system locale.

### Creare l'output in grado di
Il seguente script JSON definisce una tabella di output: **AzureBlobDest**, che fa riferimento a un blob di Azure: **MyBlob** nella cartella blob: **MySubFolder** nel contenitore di blob: **MyContainer**.
         
	{
   		"name": "AzureBlobDest",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "MyAzureStorage",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Vedere [Proprietà indirizzo di blob di Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) per informazioni dettagliate sugli elementi JSON per definire una tabella dati Factory che fa riferimento a un blob di Azure.

### Creare la pipeline
La pipeline seguente JSON definisce una pipeline con un'attività di copia che copia i dati dal file system locale per la destinazione blob di Azure.
 
	{
	    "name": "CopyFileToBlobPipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "Ingress",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "OnPremFileSource" } ],
	                "outputs": [ { "name": "AzureBlobDest" } ],
	                "transformation": {
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

La pipeline in questo esempio copia il contenuto in formato binario, senza l'analisi o eseguire alcuna trasformazione. Si noti che è possibile sfruttare **concorrenza** per copiare le sezioni dei file in parallelo. Ciò è utile quando si desidera spostare che le sezioni sono già verificati in precedenza.

> [AZURE.NOTE]Attività Copia simultanea con lo stesso host tramite un percorso UNC con account utente diversi possono causare errori come "non sono consentite più connessioni a un server o una risorsa condivisa dall'utente stesso, utilizzando più di un nome utente,". Questa è la limitazione del sistema operativo per motivi di sicurezza. A pianificare le attività di copia con gateway diversi, o installare il gateway all'interno dell'host e utilizzare "localhost" o "locale" anziché un percorso UNC.

Vedere [riferimento JSON Pipeline](https://msdn.microsoft.com/library/dn834988.aspx) per informazioni dettagliate sugli elementi JSON per definire una pipeline di dati Factory e [supportate origini e sink](https://msdn.microsoft.com/library/dn894007.aspx) per le proprietà di FileSystemSource e BlobSink.

### Scenari aggiuntivi per l'utilizzo di tabelle di sistema di file

#### Copiare tutti i file in una cartella specifica
Si noti che solo **folderPath** specificato nell'esempio di JSON.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}
 
#### Copiare tutti i file CSV nella cartella specifica
Si noti che il **fileFilter** è impostato su ***CSV**.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "*.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

#### Copiare un file specifico
Si noti che il **fileFiter** è impostata su un file specifico: **201501.csv**.

	{
	    "name": "OnPremFileSource",
	    "properties": {
	        "location": {
	            "type": "OnPremisesFileSystemLocation",
	            "folderPath": "marketingcampaign\regionaldata\na",
	            "fileFilter": "201501.csv",
	            "linkedServiceName": "FolderDataStore"
	        },
	        ...
	    }
	}

## Copia dei dati da un database Oracle in locale in un blob di Azure
È possibile utilizzare l'attività di copia per copiare i file da un database di Oracle in locale in locale in un Blob di Azure.

### creare un servizio collegato per un database Oracle in locale
JSON seguente consente di creare un servizio collegato che punta a un database Oracle in locale. Si noti che il **tipo** è impostato su **OnPremisesOracleLinkedService**.

	{
	    "name": "OnPremOracleSource",
	    "properties": {
	        "type": "OnPremisesOracleLinkedService",
	        "ConnectionString": "data source=ds;User Id=uid;Password=pwd;",
	        "gatewayName": "SomeGateway"
	    }
	}

Vedere [servizio collegato Oracle locale](https://msdn.microsoft.com/library/dn948537.aspx) per informazioni dettagliate sugli elementi JSON per definire un Oracle locale collegato servizio.

### Creare un servizio collegato per la destinazione blob di Azure
Nell'esempio seguente viene JSON può essere utilizzato per creare un servizio collegato denominato **MyAzureStorage** di tipo **AzureStorageLinkedSerivce**.

	{
	    "name": "AzureBlobDest",
	    "properties":
	    {
	        "type": "AzureStorageLinkedService",
	        "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>" "
	    }
	}

Vedere [servizio collegato di archiviazione Azure](https://msdn.microsoft.com/library/dn893522.aspx) per informazioni dettagliate sugli elementi JSON per definire un servizio di archiviazione Azure collegate servizio.

### Creare la tabella di input
Il seguente esempio JSON può essere utilizzato per creare una tabella di Azure dati Factory che fa riferimento a una tabella in un database Oracle in locale. Si noti che il **tipo di percorso** è impostato su **OnPremisesOracleTableLocation**.

	{
	    "name": "TableOracle",
	    "properties": {
	        "location": {
	            "type": "OnPremisesOracleTableLocation",
	            "tableName": "LOG",
	            "linkedServiceName": "OnPremOracleSource"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": "1",
	            "waitOnExternal": {}
	        },
	        "policy": {}
	    }
	} 

Vedere [proprietà percorso Oracle locale](https://msdn.microsoft.com/library/dn894089.aspx#Oracle) per informazioni dettagliate sugli elementi JSON per definire una tabella dati Factory che fa riferimento a una tabella in un database Oracle in locale.

### Creare la tabella di output
Il seguente script JSON definisce una tabella di output: **MyAzureBlob**, che fa riferimento a un blob di Azure: **MyBlob** nella cartella blob: **MySubFolder** nel contenitore di blob: **MyContainer**.
         
	{
   		"name": "MyAzureBlob",
	    "properties":
    	{
    		"location":
    		{
        		"type": "AzureBlobLocation",
        		"folderPath": "MyContainer/MySubFolder",
        		"fileName": "MyBlob",
        		"linkedServiceName": "AzureBlobDest",
        		"format":
        		{
            		"type": "TextFormat",
            		"columnDelimiter": ",",
            		"rowDelimiter": ";",
             		"EscapeChar": "$",
             		"NullValue": "NaN"
        		}
    		},
        	"availability":
      		{
       			"frequency": "Hour",
       			"interval": 1
      		}
   		}
	}

Vedere [Proprietà indirizzo di blob di Azure](https://msdn.microsoft.com/library/dn894089.aspx#AzureBlob) per informazioni dettagliate sugli elementi JSON per definire una tabella dati Factory che fa riferimento a un blob di Azure.

### Creare la pipeline
La pipeline di esempio riportato di seguito dispone di un'attività di copia che copia dati da una tabella di database Oracle in un blob di archiviazione di Azure.

	{
	    "name": "PipelineCopyOracleToBlob",
	    "properties": {
	        "activities": [
	            {
	                "name": "CopyActivity",
	                "description": "copy slices of oracle records to azure blob",
	                "type": "CopyActivity",
	                "inputs": [ { "name": "TableOracle" } ],
	                "outputs": [ { "name": "TableAzureBlob" } ],
	                "transformation": {
	                    "source": {
	                        "type": "OracleSource",
	                        "oracleReaderQuery": "$$Text.Format('select * from LOG where \"Timestamp\" >= to_date('{0:yyyy-MM-dd}', 'YYYY-MM-DD') AND \"Timestamp\" < to_date('{1:yyyy-MM-dd}', 'YYYY-MM-DD')', SliceStart, SliceEnd)"
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

Vedere [riferimento JSON Pipeline](https://msdn.microsoft.com/library/dn834988.aspx) per informazioni dettagliate sugli elementi JSON per definire una pipeline di dati Factory e [supportate origini e sink](https://msdn.microsoft.com/library/dn894007.aspx) per le proprietà di OracleSource e BlobSink.

## Vedere anche

- [Copiare i dati con Data factory di Azure][adf-copyactivity]
- [Attività di copia - Informazioni di riferimento sugli script JSON](https://msdn.microsoft.com/library/dn835035.aspx)
- [Video: Introduzione all'attività di copia con Data factory di Azure][copy-activity-video]


[adf-copyactivity]: data-factory-copy-activity.md
[copy-activity-video]: http://azure.microsoft.com/documentation/videos/introducing-azure-data-factory-copy-activity/

<!---HONumber=GIT-SubDir-->