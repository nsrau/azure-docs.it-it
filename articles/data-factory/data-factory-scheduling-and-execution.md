<properties
	pageTitle="Pianificazione ed esecuzione con Data Factory | Microsoft Azure"
	description="Informazioni sugli aspetti di pianificazione ed esecuzione del modello applicativo di Azure Data Factory."
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
	ms.date="08/22/2016"
	ms.author="spelluru"/>

# Pianificazione ed esecuzione con Data Factory

Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo Azure Data Factory. Questo articolo si basa su [Creazione di pipeline](data-factory-create-pipelines.md) e [Creazione di set di dati](data-factory-create-datasets.md) e presuppone la conoscenza dei concetti di base del modello applicativo di Data Factory: attività, pipeline, servizi collegati e set di dati.

## Pianificare un'attività

La sezione scheduler del file JSON dell'attività consente di specificare una pianificazione ricorrente per l'attività. Ad esempio, è possibile pianificare che l'attività venga eseguita a cadenza oraria, come riportato di seguito:

	"scheduler": {
		"frequency": "Hour",
	    "interval": 1
	},  

![Esempio di utilità di pianificazione](./media/data-factory-scheduling-and-execution/scheduler-example.png)

Come illustrato nel diagramma, se si specifica una pianificazione per l'attività vengono create finestre a cascata costituite da una serie di intervalli temporali di dimensioni fisse, contigui e non sovrapposti. Queste finestre logiche a cascata per l'attività vengono denominate *finestre attività*.

Per l'esecuzione di finestre attività in corso, è possibile accedere all'intervallo di tempo associato alla finestra attività con le variabili di sistema [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) e [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) nel file JSON dell'attività. È possibile usare queste variabili per scopi diversi nel file JSON dell'attività. Ad esempio, è possibile usarle per selezionare i dati dai set di dati di input e output che rappresentano i dati della serie temporale.

La proprietà **scheduler** supporta le stesse proprietà secondarie della proprietà **availability** in un set di dati. Per informazioni dettagliate, vedere [Disponibilità dei set di dati](data-factory-create-datasets.md#Availability). Esempi: pianificazione con uno specifico offset temporale o impostazione della modalità per allineare l'elaborazione all'inizio o alla fine della finestra attività.

Se si desidera, è possibile specificare le proprietà dell'utilità di pianificazione per un'attività. Se si specifica una proprietà, questa deve corrispondere alla cadenza indicata nella definizione del set di dati di output. In questo momento la pianificazione è basata sul set di dati di output, quindi è necessario creare un set di dati di output anche se l'attività non genera alcun output. Se l'attività non richiede input, è possibile ignorare la creazione del set di dati di input.

## Set di dati in serie temporale e sezioni di dati

I dati in serie temporale sono costituiti da una sequenza continua di punti dati generalmente composta da misure successive effettuate in un intervallo di tempo. Esempi comuni di dati in serie temporale possono essere i dati di un sensore e i dati di telemetria di un'applicazione.

Con Data Factory è possibile elaborare i dati in serie temporale in modalità batch mediante esecuzioni di attività. In genere, viene definita una cadenza ricorrente con cui vengono ricevuti i dati di input e devono essere prodotti i dati di output. La cadenza viene modellata specificando il parametro **availability** del set di dati, come indicato di seguito.

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

Ogni unità di dati usata e prodotta da un'esecuzione di attività prende il nome di sezione di dati. Nel diagramma seguente viene illustrato un esempio di un'attività con un set di dati di input e un set di dati di output. Il parametro **availability** per questi set di dati è impostato su frequenza oraria.

![Utilità di pianificazione della disponibilità](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Il diagramma precedente illustra le sezioni di dati orarie per i set di dati di input e di output. Il diagramma mostra tre sezioni di input che sono pronte per l'elaborazione. L'attività 10-11 AM è in corso e produce la sezione di output 10-11 AM.

È possibile accedere all'intervallo di tempo associato alla sezione in fase di produzione nel set di dati JSON usando le variabili [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) e [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables).

Attualmente Data Factory richiede che la pianificazione specificata nell'attività corrisponda esattamente alla pianificazione specificata nella sezione **availability** del set di dati di output. Il mapping delle variabili **WindowStart**, **WindowEnd**, **SliceStart** e **SliceEnd** viene quindi sempre eseguito allo stesso periodo di tempo e a un'unica sezione di output.

Per altre informazioni sulle diverse proprietà disponibili per la sezione availability, vedere l'articolo [Creazione di set di dati](data-factory-create-datasets.md).

## Spostare i dati dal database SQL di Azure all'archiviazione BLOB di Azure con attività di copia

Per questo esempio verrà creata una pipeline che copia dati da una tabella del database SQL di Azure all'archiviazione BLOB di Azure con cadenza oraria.

**Input: set di dati del database SQL di Azure**

	{
	    "name": "AzureSqlInput",
	    "properties": {
	        "published": false,
	        "type": "AzureSqlTable",
	        "linkedServiceName": "AzureSqlLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        },
	        "external": true,
	        "policy": {}
	    }
	}


Il parametro **frequency** è impostato su **Hour** e il parametro **interval** è impostato su **1** nella sezione availability.

**Output: set di dati dell'archiviazione BLOB di Azure**

	{
	    "name": "AzureBlobOutput",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
	            "format": {
	                "type": "TextFormat"
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
	                        "format": "%M"
	                    }
	                },
	                {
	                    "name": "Day",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%d"
	                    }
	                },
	                {
	                    "name": "Hour",
	                    "value": {
	                        "type": "DateTime",
	                        "date": "SliceStart",
	                        "format": "%H"
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


Il parametro **frequency** è impostato su **Hour** e il parametro **interval** è impostato su **1** nella sezione availability.



**Attività: attività di copia**

	{
	    "name": "SamplePipeline",
	    "properties": {
	        "description": "copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "name": "AzureSQLtoBlob",
	                "description": "copy activity",
	                "typeProperties": {
	                    "source": {
	                        "type": "SqlSource",
	                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 100000,
	                        "writeBatchTimeout": "00:05:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "AzureSQLInput"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobOutput"
	                    }
	                ],
	       			"scheduler": {
	          			"frequency": "Hour",
	          			"interval": 1
	        		}
	            }
	        ],
	        "start": "2015-01-01T08:00:00Z",
	        "end": "2015-01-01T11:00:00Z"
	    }
	}


Nell'esempio, le sezioni relative alla pianificazione dell'attività e alla disponibilità del set di dati sono impostate su una frequenza oraria. L'esempio illustra come usare **WindowStart** e **WindowEnd** per selezionare i dati pertinenti per l'esecuzione di un'attività e copiarli in un BLOB con un elemento **folderPath** appropriato. All'oggetto **folderPath** sono applicati parametri per avere una cartella separata per ogni ora.

Quando vengono eseguite tre delle sezioni nella fascia oraria 8-11 AM, i dati nel database SQL di Azure sono i seguenti:

![Input di esempio](./media/data-factory-scheduling-and-execution/sample-input-data.png)

Dopo la distribuzione della pipeline, l'archiviazione BLOB di Azure viene popolata come segue:

-	Il file mypath/2015/1/1/8/Data.&lt;Guid&gt;.txt con dati

			10002345,334,2,2015-01-01 08:24:00.3130000
			10002345,347,15,2015-01-01 08:24:00.6570000
			10991568,2,7,2015-01-01 08:56:34.5300000

	> [AZURE.NOTE] &lt;Guid&gt; verrà sostituito con un GUID effettivo. Nome file di esempio: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
-	Il file mypath/2015/1/1/9/Data.&lt;Guid&gt;.txt con dati:

			10002345,334,1,2015-01-01 09:13:00.3900000
			24379245,569,23,2015-01-01 09:25:00.3130000
			16777799,21,115,2015-01-01 09:47:34.3130000
-	Il file mypath/2015/1/1/10/Data.&lt;Guid&gt;.txt senza dati.


## Creare una sezione di dati, impostare il periodo attivo per una pipeline ed eseguire le sezioni contemporaneamente

L'articolo [Creazione di pipeline](data-factory-create-pipelines.md) ha introdotto il concetto di periodo attivo per una pipeline specificato impostando le proprietà **start** e **end**.

È possibile impostare la data di inizio per il periodo attivo della pipeline nel passato. Data Factory calcola automaticamente (recuperando le informazioni) tutte le sezioni di dati nel passato e ne avvia l'elaborazione.

È possibile configurare le sezioni di dati recuperati in modo che vengano eseguite in parallelo. A questo scopo, è necessario impostare la proprietà **concurrency** nella sezione policy del file JSON dell'attività, come illustrato nell'articolo [Creazione delle pipeline](data-factory-create-pipelines.md).

## Ripetere l'esecuzione della sezione di dati con esito negativo e tenere traccia automaticamente della dipendenza

È possibile monitorare l'esecuzione delle sezioni in una ricca modalità visiva. Per informazioni dettagliate, vedere [Monitorare e gestire le pipeline con i pannelli del portale di Azure](data-factory-monitor-manage-pipelines.md) o [App di monitoraggio e gestione](data-factory-monitor-manage-app.md).

Si consideri l'esempio seguente che descrive due attività. Activity1 produce un set di dati in serie temporale il cui output è costituito da sezioni usate come input da Activity2 per la produzione del set di dati in serie temporale che rappresenta l'output finale.

![Sezione non riuscita](./media/data-factory-scheduling-and-execution/failed-slice.png)

<br/>

Il diagramma illustra che in una delle tre sezioni recenti si è verificato un errore durante la produzione della sezione 9-10 AM per Dataset2. Data Factory tiene automaticamente traccia della dipendenza per il set di dati della serie temporale. Di conseguenza, non viene avviata l'esecuzione dell'attività per la sezione di downstream 9-10 AM.


Gli strumenti di monitoraggio e gestione di Data Factory consentono inoltre di analizzare i log di diagnostica relativi alla sezione con esito negativo e individuare facilmente la causa principale del problema per permetterne la risoluzione. Dopo aver risolto il problema, è possibile avviare facilmente l'esecuzione di attività per generare la sezione non riuscita. Per altre informazioni su come riavviare le esecuzioni o per comprendere le transizioni di stato per le sezioni di dati, vedere [Monitorare e gestire le pipeline con i pannelli del portale di Azure](data-factory-monitor-manage-pipelines.md) o [App di monitoraggio e gestione](data-factory-monitor-manage-app.md).

Dopo che la sezione 9-10 AM di **Dataset2** è stata eseguita nuovamente, Data Factory avvia l'esecuzione della sezione dipendente 9-10 AM nel set di dati finale.

![Nuova esecuzione di una sezione non riuscita](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## Eseguire attività in sequenza
È possibile concatenare due attività, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input di altre attività. Le attività possono essere nella stessa pipeline o in pipeline diverse. La seconda attività viene eseguita solo quando la prima viene completata correttamente.

Ad esempio, si consideri il caso seguente:

1.	La pipeline P1 include l'attività A1 che richiede il set di dati di input esterno D1 e produce il set di dati di output D2.
2.	La pipeline P2 include l'attività A2 che richiede l'input del set di dati D2 e produce il set di dati di output D3.

In questo scenario, le attività A1 e A2 si trovano in pipeline differenti. L'attività A1 viene eseguita quando i dati esterni sono disponibili e viene raggiunta la frequenza di disponibilità pianificata. L'attività A2 viene eseguita quando le sezioni pianificate di D2 diventano disponibili e viene raggiunta la frequenza di disponibilità pianificata. Se è presente un errore in una delle sezioni del set di dati D2, A2 non verrà eseguita per tale sezione fino a quando non diventa disponibile.

La visualizzazione diagramma sarebbe simile al diagramma seguente:

![Concatenamento di attività in due pipeline](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Come anticipato, le attività possono essere nella stessa pipeline. La visualizzazione diagramma con entrambe le attività nella stessa pipeline sarebbe simile al diagramma seguente:

![Concatenamento di attività nella stessa pipeline](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

### Copiare in sequenza
È possibile eseguire più operazioni di copia l'una dopo l'altra in modo sequenziale o ordinato. Ad esempio, si supponga di avere due attività di copia in una pipeline (CopyActivity1 e CopyActivity2) con i set di dati di input e output seguenti.

CopyActivity1

Input: Dataset. Output: Dataset2.

CopyActivity2

Input: Dataset2. Output: Dataset3.

CopyActivity2 viene eseguita solo se l'esecuzione di CopyActivity1 è riuscita e Dataset2 è disponibile.

Ecco la pipeline JSON di esempio:

	{
		"name": "ChainActivities",
	    "properties": {
			"description": "Run activities in sequence",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "copyBehavior": "PreserveHierarchy",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Dataset1"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "Dataset2"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00"
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "CopyFromBlob1ToBlob2",
	                "description": "Copy data from a blob to another"
	            },
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Dataset2"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "Dataset3"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00"
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "CopyFromBlob2ToBlob3",
	                "description": "Copy data from a blob to another"
	            }
	        ],
	        "start": "2016-08-25T01:00:00Z",
	        "end": "2016-08-25T01:00:00Z",
	        "isPaused": false
	    }
	}

Si noti che nell'esempio, il set di dati di output della prima attività di copia (Dataset2) è specificato come input per la seconda attività. La seconda attività viene quindi eseguita solo quando è pronto il set di dati di output dalla prima attività.

Nell'esempio, CopyActivity2 può avere un input diverso, ad esempio Dataset3, ma è necessario specificare Dataset2 anche come input per CopyActivity2, in modo che l'attività non venga eseguita fino a quando CopyActivity1 non è stata completata. Ad esempio:

CopyActivity1

Input: Dataset1. Output: Dataset2.

CopyActivity2

Input: Dataset3, Dataset2. Output: Dataset4.

	{
		"name": "ChainActivities",
	    "properties": {
			"description": "Run activities in sequence",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "copyBehavior": "PreserveHierarchy",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Dataset1"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "Dataset2"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00"
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "CopyFromBlobToBlob",
	                "description": "Copy data from a blob to another"
	            },
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource"
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "Dataset3"
	                    },
	                    {
	                        "name": "Dataset2"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "Dataset4"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00"
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "CopyFromBlob3ToBlob4",
	                "description": "Copy data from a blob to another"
	            }
	        ],
	        "start": "2017-04-25T01:00:00Z",
	        "end": "2017-04-25T01:00:00Z",
	        "isPaused": false
	    }
	}


Si noti che nell'esempio vengono specificati due set di dati di input per la seconda attività di copia. Quando si specificano più input, solo il primo set di dati di input viene usato per la copia dei dati e gli altri set di dati vengono usati come dipendenze. L'esecuzione di CopyActivity2 si avvia solo dopo che le seguenti condizioni sono soddisfatte:

- L’esecuzione di CopyActivity1 è riuscita e Dataset2 è disponibile. Questo set di dati non viene usato per la copia dei dati in Dataset4. La sua funzione è semplicemente quella di pianificare la dipendenza per CopyActivity2.
- Dataset3 è disponibile. Questo set di dati rappresenta i dati che vengono copiati nella destinazione.



## Modellare i set di dati con frequenze diverse

Negli esempi, la finestra di pianificazione dell'attività e le frequenze relative ai set di dati di input e di output erano identiche. Alcuni scenari richiedono tuttavia la possibilità di generare output a una frequenza diversa da quella degli input. Data Factory supporta la modellazione di questi scenari.

### Esempio 1: Generazione di un report di output giornaliero per i dati di input, disponibile ogni ora

Si consideri uno scenario in cui i dati delle misurazioni di input dei sensori sono disponibili ogni ora nell'archiviazione BLOB di Azure. Si intende generare un report aggregato giornaliero con statistiche, ad esempio media, max e min, per il giorno con [attività Hive di Data Factory](data-factory-hive-activity.md).

Ecco come modellare questo scenario con Data Factory:

**Set di dati di input**

I file di input vengono rilasciati ogni ora nella cartella relativa al giorno specificato. Il valore di disponibilità per l'input è impostato su **Hour** (frequency: Hour, interval: 1).

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Set di dati di output**

Un file di output viene creato ogni giorno nella cartella relativa al giorno corrispondente. Il valore di disponibilità per l'output è impostato su **Day** (frequency: Day, interval: 1).


	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Attività: attività Hive in una pipeline**

Lo script Hive riceve le informazioni *DateTime* appropriate sotto forma di parametri che usano la variabile **WindowStart**, come illustrato nel frammento seguente. Lo script Hive usa quindi questa variabile per caricare i dati dall'apposita cartella relativa al giorno ed eseguire l'aggregazione per generare l'output.

		{  
		    "name":"SamplePipeline",
		    "properties":{  
		    "start":"2015-01-01T08:00:00",
		    "end":"2015-01-01T11:00:00",
		    "description":"hive activity",
		    "activities": [
		        {
		            "name": "SampleHiveActivity",
		            "inputs": [
		                {
		                    "name": "AzureBlobInput"
		                }
		            ],
		            "outputs": [
		                {
		                    "name": "AzureBlobOutput"
		                }
		            ],
		            "linkedServiceName": "HDInsightLinkedService",
		            "type": "HDInsightHive",
		            "typeProperties": {
		                "scriptPath": "adftutorial\\hivequery.hql",
		                "scriptLinkedService": "StorageLinkedService",
		                "defines": {
		                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
		                    "Month": "$$Text.Format('{0:%M}',WindowStart)",
		                    "Day": "$$Text.Format('{0:%d}',WindowStart)"
		                }
		            },
		            "scheduler": {
		                "frequency": "Day",
		                "interval": 1
		            },			
		            "policy": {
		                "concurrency": 1,
		                "executionPriorityOrder": "OldestFirst",
		                "retry": 2,
		                "timeout": "01:00:00"
		            }
	             }
		     ]
		   }
		}

Il diagramma seguente illustra lo scenario dal punto di vista della dipendenza dei dati.

![Dipendenza dei dati](./media/data-factory-scheduling-and-execution/data-dependency.png)

Per ogni giorno, la sezione di output dipende dalle 24 sezioni orarie ottenute dal set di dati di input. Data Factory calcola automaticamente queste dipendenze prevedendo le sezioni di dati di input che rientrano nello stesso periodo di tempo della sezione di output da produrre. Se una delle 24 sezioni di input non è disponibile, Data Factory attenderà che la sezione di input sia pronta prima di avviare l'esecuzione dell'attività giornaliera.


### Esempio 2: Definizione di una dipendenza con espressioni e funzioni di Data Factory

Si consideri ora un altro scenario Si supponga di avere un'attività Hive che elabora i due set di dati di input. Uno di questi dispone di nuovi dati ogni giorno, mentre l'altro li riceve ogni settimana. Si supponga inoltre di voler eseguire un join tra i due input e produrre un output ogni giorno.

L'approccio semplice, in cui Data Factory determina automaticamente le sezioni di input da elaborare tramite l'allineamento con il periodo di tempo della sezione dei dati di output, non funziona.

È necessario definire un nuovo approccio per ogni esecuzione di attività in cui Data Factory possa usare la sezione di dati dell'ultima settimana per il set di dati di input settimanale. A questo scopo è possibile usare le funzioni di Azure Data Factory, come illustrato nel frammento seguente.

**Input1: BLOB di Azure**

Il primo input è il BLOB di Azure aggiornato ogni giorno.

	{
	  "name": "AzureBlobInputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Input2: BLOB di Azure**

Input2 è il BLOB di Azure aggiornato ogni settimana.

	{
	  "name": "AzureBlobInputWeekly",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
		"external": true,
	    "availability": {
	      "frequency": "Day",
	      "interval": 7
	    }
	  }
	}

**Output: BLOB di Azure**

Un file di output viene creato ogni giorno nella cartella relativa al giorno corrispondente. Il valore di disponibilità per l'output è impostato su **day** (frequency: Day, interval: 1).

	{
	  "name": "AzureBlobOutputDaily",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
	      "partitionedBy": [
	        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
	        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "%M"}},
	        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "%d"}}
	      ],
	      "format": {
	        "type": "TextFormat"
	      }
	    },
	    "availability": {
	      "frequency": "Day",
	      "interval": 1
	    }
	  }
	}

**Attività: attività Hive in una pipeline**

L'attività Hive usa i due input e genera una sezione di output giornaliera. È possibile specificare che la sezione di output giornaliera dipenda dalla sezione di input della settimana precedente per l'input settimanale procedendo come indicato di seguito.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"hive activity",
	    "activities": [
	      {
	        "name": "SampleHiveActivity",
	        "inputs": [
	          {
	            "name": "AzureBlobInputDaily"
	          },
	          {
	            "name": "AzureBlobInputWeekly",
	            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
	            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutputDaily"
	          }
	        ],
	        "linkedServiceName": "HDInsightLinkedService",
	        "type": "HDInsightHive",
	        "typeProperties": {
	          "scriptPath": "adftutorial\\hivequery.hql",
	          "scriptLinkedService": "StorageLinkedService",
	          "defines": {
	            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
	            "Month": "$$Text.Format('{0:%M}',WindowStart)",
	            "Day": "$$Text.Format('{0:%d}',WindowStart)"
	          }
	        },
	        "scheduler": {
	          "frequency": "Day",
	          "interval": 1
	        },			
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 2,  
	          "timeout": "01:00:00"
	        }
		   }
	     ]
	   }
	}


## Funzioni e variabili di sistema di Data Factory   

Per un elenco di funzioni e variabili di sistema supportate da Data Factory, vedere l'articolo [Funzioni e variabili di sistema di Data Factory](data-factory-functions-variables.md).

## Approfondimento della dipendenza dei dati

Per generare una sezione di set di dati da un'esecuzione di attività, Data Factory usa il *modello di dipendenza* seguente per determinare le relazioni tra i set di dati usati e generati da un'attività.

L'intervallo di tempo dei set di dati di input necessario per generare la sezione di set di dati di output prende il nome di *periodo di dipendenza*.

Un'esecuzione di attività genera una sezione di set di dati solo dopo che sono disponibili le sezioni di dati nei set di dati di input compresi nel periodo di dipendenza. Significa che tutte le sezioni di input che rientrano nel periodo di dipendenza devono avere lo stato **Pronto** affinché l'attività venga eseguita per generare una sezione del set di dati di output.

Per generare la sezione del set di dati [**start**, **end**], una funzione deve eseguire il mapping della sezione del set di dati al periodo di dipendenza. Questa funzione è costituita essenzialmente da una formula che converte l'inizio e la fine della sezione di set di dati nell'inizio e nella fine del periodo di dipendenza. Più formalmente,

	DatasetSlice = [start, end]
	DependecyPeriod = [f(start, end), g(start, end)]

**F** e **g** sono funzioni di mapping che consentono di calcolare l'inizio e la fine del periodo di dipendenza per ogni input di attività.

Come illustrato negli esempi, il periodo di dipendenza corrisponde al periodo in cui verrà prodotta la sezione di dati. In questi casi, Data Factory calcola automaticamente le sezioni di input che rientrano nel periodo di dipendenza.

Ad esempio, nell'esempio di aggregazione in cui l'output viene prodotto giornalmente e i dati di input sono disponibili ogni ora, il periodo della sezione di dati è di 24 ore. Data Factory identifica le sezioni di input orarie relative a questo periodo di tempo e rende la sezione di output dipendente dalla sezione di input.

È possibile specificare anche un mapping personalizzato per il periodo di dipendenza, come illustrato nell'esempio in cui uno degli input era settimanale, mentre la sezione di output veniva generata giornalmente.

## Convalida e dipendenza dei dati

Per un set di dati è possibile definire anche un criterio di convalida che specifica in che modo i dati generati dall'esecuzione di una sezione possono essere convalidati prima che siano pronti per l'uso. Per informazioni dettagliate, vedere [Creazione di set di dati](data-factory-create-datasets.md).

In questi casi, al termine del processo di esecuzione, lo stato della sezione di output viene impostato su **In attesa** con lo stato secondario impostato su **Convalida**. Al termine della convalida, lo stato viene impostato invece su **Pronto**.

Se una sezione di dati è stata correttamente generata ma non ha superato il processo di convalida, le esecuzioni di attività per le sezioni a valle dipendenti dalla sezione non riuscita non vengono elaborate.

In [Monitorare e gestire le pipeline](data-factory-monitor-manage-pipelines.md) vengono descritti i vari stati disponibili per le sezioni di dati di Data Factory.

## Dati esterni

Se un set di dati viene contrassegnato come esterno, come illustrato nel frammento JSON seguente, significa che non è stato generato con Data Factory. In questo caso, al criterio del set di dati è possibile applicare un set di parametri aggiuntivo che definisce i criteri di convalida e ripetizione per il set di dati. Per una descrizione di tutte le proprietà, vedere [Creazione di pipeline](data-factory-create-pipelines.md).

Analogamente ai set di dati prodotti da Data Factory, è necessario che le sezioni di dati per i dati esterni siano pronte prima che le sezioni dipendenti possano essere elaborate.

	{
		"name": "AzureSqlInput",
		"properties":
		{
			"type": "AzureSqlTable",
			"linkedServiceName": "AzureSqlLinkedService",
			"typeProperties":
			{
				"tableName": "MyTable"
			},
			"availability":
			{
				"frequency": "Hour",
				"interval": 1     
			},
			"external": true,
			"policy":
			{
				"externalData":
				{
					"retryInterval": "00:01:00",
					"retryTimeout": "00:10:00",
					"maximumRetry": 3
				}
			}  
		}
	}


## Pipeline monouso
È possibile creare e pianificare una pipeline da eseguire periodicamente, ad esempio ogni ora o ogni giorno, tra le ore di inizio e di fine specificate nella definizione della pipeline. Per informazioni dettagliate, vedere la sezione [Pianificazione delle attività](#scheduling-and-execution). È anche possibile creare una pipeline che viene eseguita una sola volta. A tale scopo, impostare la proprietà **pipelineMode** nella definizione della pipeline su **onetime**, come illustrato nell'esempio JSON seguente. Il valore predefinito per questa proprietà è **scheduled**.

	{
	    "name": "CopyPipeline",
	    "properties": {
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "BlobSource",
	                        "recursive": false
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "InputDataset"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "OutputDataset"
	                    }
	                ]
	                "name": "CopyActivity-0"
	            }
	        ]
	        "pipelineMode": "OneTime"
	    }
	}

Tenere presente quanto segue:

- Le ore di **inizio** e **fine** della pipeline non vengono specificate.
- La **disponibilità** dei set di dati di input e output viene specificata (**frequenza** e **intervallo**) anche se i valori non vengono usati da Data Factory.
- Le pipeline monouso non vengono visualizzate nella vista Diagramma. Questo comportamento dipende dalla progettazione.
- Le pipeline monouso non possono essere aggiornate. È possibile clonare una pipeline monouso, rinominarla, aggiornarne le proprietà e distribuirla per crearne un'altra.

<!---HONumber=AcomDC_0907_2016-->