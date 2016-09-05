<properties 
	pageTitle="Pianificazione ed esecuzione con Data Factory" 
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
  
Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo di Azure Data Factory. L'articolo si basa sulle informazioni descritte negli articoli [Pipeline e attività in Azure Data Factory: creare e pianificare pipeline e concatenare attività](data-factory-create-pipelines.md) e [Set di dati in Azure Data Factory](data-factory-create-datasets.md) e presuppone la conoscenza dei concetti di base del modello applicativo di Data Factory: attività, pipeline, servizi collegati e set di dati.

## Pianificazione delle attività

La sezione **scheduler** del file JSON dell'attività consente di specificare una pianificazione ricorrente per l'attività. È ad esempio possibile pianificare che l'attività venga eseguita a cadenza oraria, come riportato di seguito:

	"scheduler": {
		"frequency": "Hour",
	    "interval": 1
	},  
    
![Esempio di utilità di pianificazione](./media/data-factory-scheduling-and-execution/scheduler-example.png)  

Come illustrato nel diagramma, se si specifica una pianificazione per l'attività vengono create finestre a cascata costituite da serie di intervalli temporali di dimensioni fisse, contigui e non sovrapposti. Queste finestre logiche a cascata per l'attività vengono denominate **finestre attività**.
 
Per l'esecuzione di finestre attività in corso, è possibile accedere all'intervallo di tempo associato alla finestra attività con le variabili di sistema [WindowStart](data-factory-functions-variables.md#data-factory-system-variables) e [WindowEnd](data-factory-functions-variables.md#data-factory-system-variables) nel file JSON dell'attività. Queste variabili possono essere usate per vari scopi nel file JSON dell'attività, ad esempio per selezionare dati da set di dati di input o output che rappresentano dati in serie temporale.

La proprietà **scheduler** supporta le stesse proprietà secondarie della proprietà **availability** in un set di dati. Per informazioni dettagliate, vedere [Disponibilità dei set di dati](data-factory-create-datasets.md#Availability). Esempi: pianificazione con uno specifico offset temporale o impostazione della modalità per allineare l'elaborazione all'inizio o alla fine della finestra attività.

L'indicazione delle proprietà dell'utilità di pianificazione per un'attività è facoltativa. Se si specificano le proprietà, queste devono corrispondere alla cadenza indicata nella definizione del set di dati di output. In questo momento la pianificazione è basata sul set di dati di output, quindi è necessario creare un set di dati di output anche se l'attività non genera alcun output. Se l'attività non richiede input, è possibile ignorare la creazione del set di dati di input.

## Set di dati in serie temporale e sezioni di dati

I dati in serie temporale sono costituiti da una sequenza continua di punti dati generalmente composta da misure successive effettuate in un intervallo di tempo. Esempi comuni di dati in serie temporale possono essere i dati di un sensore, i dati di telemetria di un'applicazione e così via.

Con Azure Data Factory è possibile elaborare i dati in serie temporale in modalità batch mediante esecuzioni di attività. In genere, vengono definite cadenze ricorrenti con cui vengono ricevuti i dati di input e devono essere prodotti i dati di output. La cadenza viene modellata specificando la sezione **availability** del set di dati, come indicato di seguito.

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

Ogni unità di dati usata e prodotta da un'esecuzione di attività prende il nome di **sezione** di dati. Il diagramma seguente illustra un esempio di attività con un set di dati di input e uno di output, ognuno con la disponibilità impostata su una frequenza oraria.

![Utilità di pianificazione della disponibilità](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Il diagramma illustra le sezioni di dati orarie per i set di dati di input e di output. Il diagramma mostra tre sezioni di input pronte per l'elaborazione e l'esecuzione di attività 10-11 AM in corso, che danno luogo alla sezione di output 10-11 AM.

È possibile accedere all'intervallo di tempo associato alla sezione in fase di produzione usando le variabili [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) e [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables) nel set di dati JSON.

La data factory richiede attualmente che la pianificazione specificata nell'attività corrisponda esattamente alla pianificazione specificata nella disponibilità del set di dati di output. Il mapping delle variabili WindowStart, WindowEnd, SliceStart e SliceEnd verrà quindi sempre eseguito allo stesso periodo di tempo e a un'unica sezione di output.

Per altre informazioni sulle diverse proprietà disponibili per la sezione di disponibilità, vedere l'articolo [Set di dati in Azure Data Factory](data-factory-create-datasets.md).

## Esempio: Attività di copia per spostare dati da Azure SQL al BLOB di Azure

Per questo esempio verrà creata una pipeline che copia dati da una tabella di Azure SQL al BLOB di Azure con cadenza oraria.

**Input: set di dati di Azure SQL**

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


Il parametro **frequency** è impostato su **Hour** e il parametro **interval** è impostato su **1** nella sezione **availability**.

**Output: set di dati del BLOB di Azure**
	
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


Il parametro **frequency** è impostato su **Hour** e il parametro **interval** è impostato su **1** nella sezione **availability**.



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


Nell'esempio, le sezioni relative alla pianificazione dell'attività e alla disponibilità del set di dati sono impostate su una frequenza oraria. L'esempio illustra come usare **WindowStart** e **WindowEnd** per selezionare i dati pertinenti per l'esecuzione di un'attività e copiarli in un BLOB con un elemento **folderPath** appropriato. L'oggetto folderPath è con parametri per avere una cartella separata per ogni ora.

Quando 3 delle sezioni nella fascia oraria 8 - 11 AM vengono eseguite e i dati in Azure SQL sono i seguenti:

![Input di esempio](./media/data-factory-scheduling-and-execution/sample-input-data.png)  

Distribuendo questa pipeline, l'archivio BLOB di Azure verrà popolato come segue:

1.	Il file mypath/2015/1/1/8/Data.&lt;Guid&gt;.txt con dati

			10002345,334,2,2015-01-01 08:24:00.3130000
			10002345,347,15,2015-01-01 08:24:00.6570000
			10991568,2,7,2015-01-01 08:56:34.5300000

	> [AZURE.NOTE] &lt;Guid&gt; verrà sostituito con un GUID effettivo. Nome file di esempio: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
2.	Il file mypath/2015/1/1/9/Data.&lt;Guid&gt;.txt con dati:

			10002345,334,1,2015-01-01 09:13:00.3900000
			24379245,569,23,2015-01-01 09:25:00.3130000
			16777799,21,115,2015-01-01 09:47:34.3130000
3.	Il file mypath/2015/1/1/10/Data.&lt;Guid&gt;.txt senza dati.


## Sezioni di dati, periodo attivo per la pipeline ed esecuzione contemporanea di più sezioni

L'articolo [Pipeline e attività in Azure Data Factory: creare e pianificare pipeline e concatenare attività](data-factory-create-pipelines.md) introduce il concetto di periodo attivo per una pipeline specificato impostando le proprietà **start** ed **end**.
 
È possibile impostare la data di inizio per il periodo attivo della pipeline nel passato. Data Factory calcolerà automaticamente (recuperando le informazioni) tutte le sezioni di dati nel passato e ne avvierà l'elaborazione.

Le sezioni di dati così recuperate potranno essere configurate per essere eseguite in parallelo. A questo scopo, è necessario impostare la proprietà **concurrency** nella sezione **policy** del file JSON dell'attività, come illustrato nell'articolo relativo alla [creazione di pipeline](data-factory-create-pipelines.md).

## Nuova esecuzione di sezioni di dati non riuscite e monitoraggio automatico delle dipendenze di dati

È possibile monitorare l'esecuzione delle sezioni in modalità grafica. Per informazioni dettagliate, vedere **Monitorare e gestire le pipeline** [di Azure Data Factory](data-factory-monitor-manage-pipelines.md) o [Monitorare e gestire le pipeline di Azure Data Factory con la nuova app di monitoraggio e gestione](data-factory-monitor-manage-app.md).

Si consideri l'esempio seguente che descrive due attività. Activity1 produce un set di dati in serie temporale il cui output è costituito da sezioni usate come input da Activity2 per la produzione del set di dati in serie temporale che rappresenta l'output finale.

![Sezione non riuscita](./media/data-factory-scheduling-and-execution/failed-slice.png)  

<br/>  

Il diagramma illustra che in una delle tre sezioni recenti si è verificato un errore durante la produzione della sezione 9-10 AM per **Dataset2**. La data factory monitorizza automaticamente le dipendenze per il set di dati in serie temporale e, di conseguenza, sospende l'avvio dell'esecuzione di attività per la sezione a valle 9-10 AM.


Gli strumenti di monitoraggio e gestione della data factory consentono inoltre di analizzare i log di diagnostica relativi alla sezione e individuare facilmente la causa principale del problema per permetterne la risoluzione. Dopo aver risolto il problema, è anche possibile avviare facilmente l'esecuzione di attività per generare la sezione non riuscita. Per altre informazioni su come riavviare le esecuzioni o per comprendere le transizioni di stato per le sezioni di dati, vedere **Monitorare e gestire le pipeline** [di Azure Data Factory](data-factory-monitor-manage-pipelines.md) o [Monitorare e gestire le pipeline di Azure Data Factory con la nuova app di monitoraggio e gestione](data-factory-monitor-manage-app.md).

Dopo che la sezione 9-10 AM di dataset2 è stata eseguita nuovamente ed è pronta, Data Factory avvierà l'esecuzione della sezione dipendente 9-10 AM nel set di dati finale, come illustrato nel diagramma seguente.

![Nuova esecuzione di una sezione non riuscita](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

Per un approfondimento sulla definizione e il monitoraggio delle dipendenze per una catena di attività, vedere le sezioni seguenti.

## Concatenamento di attività
È possibile concatenare due attività usando il set di dati di output di un'attività come set di dati di input di altre attività. Le attività possono essere nella stessa pipeline o in pipeline diverse. La seconda attività viene eseguita solo quando la prima viene completata correttamente.

Ad esempio, si consideri il caso seguente:
 
1.	La pipeline P1 include l'attività A1 che richiede il set di dati di input esterno D1 e produce il set di dati di **output** **D2**.
2.	La pipeline P2 include l'attività A2 che richiede l'**input** del set di dati **D2** e produce il set di dati di output D3.
 
In questo scenario, l'attività A1 viene eseguita quando i dati esterni sono disponibili e viene raggiunta la frequenza di disponibilità pianificata. L'attività A2 viene eseguita quando le sezioni pianificate di D2 diventano disponibili e viene raggiunta la frequenza di disponibilità pianificata. Se è presente un errore in una delle sezioni del set di dati D2, A2 non verrà eseguita per tale sezione fino a quando non diventa disponibile.

La visualizzazione sarebbe simile al diagramma seguente:

![Concatenamento di attività in due pipeline](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)  

La visualizzazione con entrambe le attività nella stessa pipeline sarebbe simile al diagramma seguente:

![Concatenamento di attività nella stessa pipeline](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)  

### Copia ordinata
È possibile eseguire più operazioni di copia l'una dopo l'altra in modo sequenziale o ordinato. Si supponga di avere due attività di copia in una pipeline: CopyActivity1 e CopyActivity con i set di dati di input e output seguenti.

CopyActivity1: Input: Dataset1 Output Dataset2

CopyActivity2: Inputs: Dataset2 Output: Dataset4

CopyActivity2 viene eseguita solo se l'esecuzione di CopyActivity1 è riuscita e Dataset2 è disponibile.

Nell'esempio, CopyActivity2 può avere un input diverso, ad esempio Dataset3, ma è necessario specificare Dataset2 anche come input per CopyActivity2, in modo che l'attività non venga eseguita fino a quando CopyActivity1 non è stata completata. ad esempio:

CopyActivity1: Input: Dataset1 Output Dataset2

CopyActivity2: Inputs: Dataset3, Dataset2 Output: Dataset4

Quando si specificano più input, solo il primo set di dati di input viene usato per la copia dei dati e gli altri set di dati vengono usati come dipendenze. L'esecuzione di CopyActivity2 si avvia solo quando le seguenti condizioni sono soddisfatte:

- L’esecuzione di CopyActivity1 è riuscita e Dataset2 è disponibile. Questo set di dati non viene usato per la copia dei dati in Dataset4. La sua funzione è semplicemente quella di pianificare la dipendenza per CopyActivity2.
- Dataset3 è disponibile. Questo set di dati rappresenta i dati che vengono copiati nella destinazione.



## Modellazione di set di dati con frequenze diverse

Negli esempi, la finestra di pianificazione dell'attività e le frequenze relative ai set di dati di input e di output erano identiche. Alcuni scenari richiedono tuttavia la possibilità di generare output a una frequenza diversa da quella degli input. La data factory supporta la modellazione di questi scenari.

### Esempio 1: Generazione di un report di output giornaliero per i dati di input, disponibile ogni ora

Si consideri uno scenario in cui i dati delle misurazioni di input dei sensori sono disponibili ogni ora nel Blob di Azure. Si intende generare un report aggregato giornaliero con statistiche, ad esempio media, max, min e così via, per il giorno con [attività Hive](data-factory-hive-activity.md) di Data Factory.

Ecco come modellare questo scenario con Data Factory:

**Set di dati dell'archivio BLOB di Azure di input:**

I file di input vengono rilasciati ogni ora nella cartella relativa al giorno specificato. Il valore di disponibilità per l'input è impostato su "ogni ora" (frequency: Hour, interval: 1).

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

**Set di dati di output del BLOB di Azure**

Un file di output viene creato ogni giorno nella cartella relativa al giorno corrispondente. Il valore di disponibilità per l'output è impostato su "ogni giorno" (frequency: Day e interval: 1).


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

Lo script Hive riceve le informazioni di data e ora appropriate sotto forma di parametri che usano la variabile **WindowStart**, come illustrato nel frammento seguente. Lo script Hive usa quindi questa variabile per caricare i dati dall'apposita cartella relativa al giorno ed eseguire l'aggregazione per generare l'output.

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

Per ogni giorno, la sezione di output dipende dalle 24 sezioni orarie ottenute dal set di dati di input. La data factory calcola automaticamente queste dipendenze prevedendo le sezioni di dati di input che rientrano nello stesso periodo di tempo della sezione di output da produrre. Se una delle 24 sezioni di input non è disponibile, Data Factory attenderà che la sezione di input sia pronta prima di avviare l'esecuzione dell'attività giornaliera.


### Esempio 2: Definizione di una dipendenza con espressioni e funzioni di data factory

Si consideri ora un altro scenario in cui si suppone che un'attività Hive elabori due set di dati di input e, mentre il primo ottiene nuovi dati giornalmente, l'altro riceve nuovi dati a cadenza settimanale. Si suppone inoltre di voler eseguire un join tra i due input e produrre un output giornalmente.
 
L'approccio semplice, in cui Data Factory determina automaticamente le sezioni di input da elaborare tramite l'allineamento con il periodo di tempo della sezione dei dati di output, non funziona.

È necessario definire un nuovo approccio per ogni esecuzione di attività in cui la data factory possa usare la sezione di dati dell'ultima settimana per il set di dati di input settimanale. A questo scopo è possibile usare le funzioni di Azure Data Factory, come illustrato nel frammento seguente.

**Input1: archivio BLOB di Azure**

Il primo input è l'archivio BLOB di Azure aggiornato **giornalmente**.
	
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

**Input2: archivio BLOB di Azure**

Il secondo input è l'archivio BLOB di Azure aggiornato **settimanalmente**.

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

**Output: archivio BLOB di Azure**

Un file di output viene creato ogni giorno nella cartella relativa al giorno corrispondente. Il valore di disponibilità per l'output è impostato su "ogni giorno" (frequency: Day, interval: 1).
	
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

Per un elenco di funzioni e variabili di sistema supportate da Azure Data Factory, vedere l'articolo [Azure Data Factory - Funzioni e variabili di sistema](data-factory-functions-variables.md).

## Approfondimento della dipendenza dei dati

Per generare una sezione di set di dati da un'esecuzione di attività, Data Factory usa il **modello di dipendenza** seguente per determinare le relazioni tra i set di dati usati e generati da un'attività.

L'intervallo di tempo dei set di dati di input necessario per generare la sezione di set di dati di output prende il nome di **periodo di dipendenza**.

Un'esecuzione di attività genera una sezione di set di dati solo dopo che sono disponibili le sezioni di dati nei set di dati di input compresi nel periodo di dipendenza. In questo modo, tutte le sezioni di input che rientrano nel periodo di dipendenza devono essere impostate sullo stato **Ready** affinché la sezione del set di dati di output possa essere generata da un'esecuzione di attività.

Per generare la sezione di set di dati [inizio, fine], è necessaria una funzione che esegua il mapping della sezione di set di dati al periodo di dipendenza. Questa funzione è costituita essenzialmente da una formula che converte l'inizio e la fine della sezione di set di dati nell'inizio e nella fine del periodo di dipendenza. Più formalmente,
	
	DatasetSlice = [start, end]
	DependecyPeriod = [f(start, end), g(start, end)]

in cui f e g sono funzioni di mapping che consentono di calcolare l'inizio e la fine del periodo di dipendenza per ogni input di attività.

Come illustrato negli esempi, il periodo di dipendenza corrisponde al periodo in cui viene prodotta la sezione di dati. In questi casi, Data Factory calcola automaticamente le sezioni di input che rientrano nel periodo di dipendenza.

Nell'esempio di aggregazione, in cui l'output viene prodotto giornalmente e i dati di input sono disponibili ogni ora, il periodo della sezione di dati è di 24 ore. La data factory identifica le sezioni di input orarie relative a questo periodo di tempo e rende la sezione di output dipendente dalla sezione di input.

È possibile specificare anche un mapping personalizzato per il periodo di dipendenza, come illustrato nell'esempio in cui uno degli input era settimanale, mentre la sezione di output veniva generata giornalmente.
   
## Convalida e dipendenza dei dati

Se necessario, per un set di dati è possibile definire anche un criterio di convalida che specifica in che modo i dati generati dall'esecuzione di una sezione possono essere convalidati prima che siano pronti per l'uso. Per informazioni dettagliate, vedere l'argomento relativo alla [creazione di set di dati](data-factory-create-datasets.md).

In questi casi, al termine del processo di esecuzione, lo stato della sezione di output viene impostato su **Waiting** con lo stato secondario impostato su **Validation**. Al termine della convalida, lo stato viene impostato invece su **Ready**.
   
Se una sezione di dati è stata correttamente generata ma non ha superato il processo di convalida, le esecuzioni di attività per le sezioni a valle dipendenti dalla sezione non riuscita non vengono elaborate.

In [Monitorare e gestire le pipeline di Data factory di Azure](data-factory-monitor-manage-pipelines.md) vengono descritti i vari stati disponibili per le sezioni di dati della data factory.

## Dati esterni

Se un set di dati viene contrassegnato come esterno, come illustrato nel frammento JSON seguente, significa che non è stato generato con Azure Data Factory. In questo caso, al criterio del set di dati è possibile applicare un set di parametri aggiuntivo che definisce i criteri di convalida e ripetizione per il set di dati. Per una descrizione di tutte le proprietà, vedere [Pipeline e attività in Azure Data Factory: creare e pianificare pipeline e concatenare attività](data-factory-create-pipelines.md).

Analogamente ai set di dati prodotti dalla data factory, è necessario che le sezioni di dati per i dati esterni siano pronte prima che le sezioni dipendenti possano essere elaborate.

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
È possibile creare e pianificare una pipeline da eseguire periodicamente, ad esempio ogni ora e ogni giorno, tra le ore di inizio e di fine specificate nella definizione della pipeline. Per informazioni dettagliate, vedere la sezione [Pianificazione delle attività](#scheduling-and-execution). È anche possibile creare una pipeline che viene eseguita una sola volta. A tale scopo, impostare la proprietà **pipelineMode** nella definizione della pipeline su **onetime**, come illustrato nell'esempio JSON seguente. Il valore predefinito per questa proprietà è **scheduled**.

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
- La **disponibilità** dei set di dati di input e output viene specificata (frequenza e intervallo) anche se i valori non vengono usati da Data Factory.
- Le pipeline monouso non vengono visualizzate nella vista Diagramma. Questo comportamento dipende dalla progettazione.
- Le pipeline monouso non possono essere aggiornate. È possibile clonare una pipeline monouso, rinominarla, aggiornarne le proprietà e distribuirla per crearne un'altra.

  




  









 
 












      

  

<!---HONumber=AcomDC_0824_2016-->