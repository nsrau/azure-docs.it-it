<properties 
	pageTitle="Pianificazione ed esecuzione con Data Factory" 
	description="Informazioni sugli aspetti di pianificazione ed esecuzione del modello applicativo di Data factory di Azure." 
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
	ms.date="07/28/2015" 
	ms.author="spelluru"/>

# Pianificazione ed esecuzione con Data factory
  
Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo di Data factory di Azure. L'articolo si basa sulle informazioni fornite negli articoli relativi alla [creazione di pipeline](data-factory-create-pipelines.md) e alla [creazione di set di dati](data-factory-create-datasets.md) e presuppone la conoscenza dei concetti di base dei modello applicativo di Data factory: attività, pipeline, servizi collegati e set di dati.

## Pianificazione delle attività

La sezione **scheduler** del file JSON dell'attività consente di specificare una pianificazione ricorrente per l'attività. È possibile pianificare, ad esempio, che l'attività venga eseguita a cadenza oraria, come riportato di seguito:

	"scheduler": {
		"frequency": "Hour",
	    "interval": 1
	},  
    
![Esempio di utilità di pianificazione](./media/data-factory-scheduling-and-execution/scheduler-example.png)

Come sopra illustrato, se si specifica una pianificazione oraria si creano esecuzioni di attività corrispondenti a una serie di finestre a cascata, costituite da serie di intervalli temporali di dimensioni fisse, contigue e non sovrapposte.
 
Per l'esecuzione di attività in corso, è possibile accedere all'intervallo di tempo con le variabili di sistema **WindowStart** e **WindowEnd** del file JSON dell'attività. Queste variabili, in realtà, possono essere usate per vari scopi nel file JSON dell'attività e negli script associati all'attività, ad esempio per selezionare dati da set di dati di input o output che rappresentano dati in serie temporale.

Per altre informazioni sulle diverse proprietà disponibili per l'utilità di pianificazione, ad esempio la pianificazione con uno specifico offset temporale o l'impostazione della modalità per allineare l'elaborazione all'inizio o alla fine dell'intervallo di tempo, fare riferimento all'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md).

## Set di dati in serie temporale e sezioni di dati

I dati in serie temporale sono costituiti da una sequenza continua di punti dati generalmente composta da misure successive effettuate in un intervallo di tempo. Esempi comuni di dati in serie temporale possono essere i dati di un sensore, i dati di telemetria di un'applicazione e così via.

Con Data factory di Azure è possibile elaborare i dati in serie temporale in modalità batch mediante esecuzioni di attività. In genere, vengono definite cadenze ricorrenti con cui vengono ricevuti i dati di input e devono essere prodotti i dati di output. La cadenza viene modellata specificando la sezione **availability** del set di dati, come indicato di seguito.

    "availability": {
      "frequency": "Hour",
      "interval": 1
    },

Ogni unità di dati usata e prodotta da un'esecuzione di attività prende il nome di **sezione** di dati. Il diagramma seguente illustra un esempio di un'attività con un set di dati in serie temporale di input e uno di output, ciascuno con la disponibilità impostata su una frequenza oraria.

![Utilità di pianificazione della disponibilità](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Il diagramma sopra riportato illustra le sezioni di dati orarie per i set di dati di input e di output. Il diagramma, in particolare, mostra tre sezioni di input pronte per l'elaborazione e l'esecuzione di attività 10-11 AM in corso, che danno luogo alla sezione di output 10-11 AM.

È possibile accedere all'intervallo di tempo associato alla sezione in fase di produzione usando le variabili **SliceStart** e **SliceEnd** nel set di dati JSON.

Per altre informazioni sulle diverse proprietà disponibili per la sezione di disponibilità, fare riferimento all'articolo sulla [creazione di set di dati](data-factory-create-datasets.md).

## Esempio: Attività di copia per spostare dati da Azure SQL al BLOB di Azure

Per questo esempio si prenderà come riferimento l'attività di copia illustrata nell'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md), in cui a cadenza oraria vengono copiati i dati da una tabella di Azure SQL al BLOB di Azure.

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


Osservare come nella sezione **availability** il parametro **frequency** sia impostato su **Hour** e il parametro **interval** sia impostato su **1**.

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


Osservare come nella sezione **availability** il parametro **frequency** sia impostato su **Hour** e il parametro **interval** sia impostato su **1**.



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


Nell'esempio precedente, le sezioni relative alla pianificazione dell'attività e alla disponibilità del set di dati sono impostate su una frequenza oraria. Questo esempio mostra anche come usare le variabili **WindowStart** e **WindowEnd** per selezionare i dati rilevanti per l'esecuzione di attività specificata e inviarli a un archivio BLOB con i parametri della proprietà **folderPath** dinamica impostati in modo da aggiornare la cartella ogni ora.

Quando sono in esecuzione tre delle sezioni comprese tra 8-11 AM, questo è il risultato che si ottiene con un archivio BLOB e una tabella di Azure di esempio.

Si supponga che in SQL Azure siano presenti i dati seguenti:

![Input di esempio](./media/data-factory-scheduling-and-execution/sample-input-data.png)

Distribuendo questa pipeline, l'archivio BLOB di Azure verrà popolato come segue:

1.	Il file mypath/2015/1/1/8/Data.<Guid>.txt con i dati seguenti: 

		10002345,334,2,2015-01-01 08:24:00.3130000
		10002345,347,15,2015-01-01 08:24:00.6570000
		10991568,2,7,2015-01-01 08:56:34.5300000

	**Nota:** <Guid> verrà sostituito con un guid effettivo. Nome file di esempio: Data.bcde1348-7620-4f93-bb89-0eed3455890b.txt
2.	Il file mypath/2015/1/1/9/Data.<Guid>.txt con i dati seguenti:

		10002345,334,1,2015-01-01 09:13:00.3900000
		24379245,569,23,2015-01-01 09:25:00.3130000
		16777799,21,115,2015-01-01 09:47:34.3130000
3.	Il file mypath/2015/1/1/10/Data.<Guid>.txt senza dati.


## Sezioni di dati, periodo attivo per la pipeline ed esecuzione contemporanea di più sezioni

L'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md) ha introdotto il concetto di periodo attivo per una pipeline specificata impostandone le proprietà **start** e **end**.
 
Per il periodo attivo della pipeline è possibile impostare una data di inizio nel passato e la data factory calcolerà automaticamente (recuperando le informazioni) tutte le sezioni di dati nel passato e ne inizierà l'elaborazione.

Le sezioni di dati così recuperate potranno essere configurate per essere eseguite in parallelo. A questo scopo, è necessario impostare la proprietà della concorrenza nella sezione **policy** del file JSON dell'attività, come illustrato nell'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md).

## Nuova esecuzione di sezioni di dati non riuscite e monitoraggio automatico delle dipendenze di dati

È possibile monitorare l'esecuzione delle sezioni in modalità grafica. Per informazioni dettagliate, vedere l'argomento relativo al [monitoraggio e alla gestione delle pipeline](data-factory-monitor-manage-pipelines.md).

Si consideri l'esempio seguente che mostra due attività. Activity1 produce un set di dati in serie temporale il cui output è costituito da sezioni usate come input da Activity2 per la produzione del set di dati in serie temporale che rappresenta l'output finale.

![Sezione non riuscita](./media/data-factory-scheduling-and-execution/failed-slice.png)

<br/>

Il diagramma precedente mostra che in una delle tre sezioni recenti si è verificato un errore durante la produzione della sezione 9-10 AM per **Dataset2**. La data factory monitorizza automaticamente le dipendenze per il set di dati in serie temporale e, di conseguenza, sospende l'avvio dell'esecuzione di attività per la sezione a valle 9-10 AM.


Gli strumenti di monitoraggio e gestione della data factory consentono inoltre di analizzare i log di diagnostica relativi alla sezione e individuare facilmente la causa principale del problema per permetterne la risoluzione. Dopo aver risolto il problema, è possibile anche avviare facilmente l'esecuzione di attività per generare la sezione non riuscita. Per altre informazioni su come avviare nuove esecuzioni o comprendere le transizioni di stato per le sezioni di dati, vedere l'articolo sul [monitoraggio e la gestione](data-factory-monitor-manage-pipelines.md).

Quando, dopo aver avviato la nuova esecuzione, la sezione 9-10AM del dataset2 è pronta, la data factory avvierà l'esecuzione della sezione dipendente 9-10 AM nel set di dati finale, come illustrato nel diagramma seguente.

![Nuova esecuzione di una sezione non riuscita](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

Per un approfondimento sulla definizione e il monitoraggio delle dipendenze per catene complesse di attività e set di dati, fare riferimento alle sezioni seguenti.

## Modellazione di set di dati con frequenze diverse

Negli esempi precedenti, la finestra di pianificazione dell'attività e le frequenze relative ai set di dati di input e di output erano identiche. Alcuni scenari richiedono tuttavia la possibilità di generare output a una frequenza diversa da quella degli input. La data factory supporta la modellazione di questi scenari.

### Esempio 1: Generazione di un report di output giornaliero per i dati di input, disponibile ogni ora

Si consideri uno scenario in cui i dati di input sono costituiti dalle misurazioni di sensori, resi disponibili ogni ora nell'archivio BLOB di Azure, e si desidera generare un report aggregato giornaliero con statistiche quali valore medio, minimo e massimo del giorno mediante [l'attività Hive](data-factory-hive-activity.md) della data factory.

Ecco come modellare questo scenario con la data factory:

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

Un file di output verrà rilasciato ogni giorno nella cartella relativa al giorno corrispondente. Il valore di disponibilità per l'output è impostato su "ogni giorno" (frequency: Day e interval: 1).


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

Lo script Hive riceve le informazioni di data e ora appropriate sotto forma di parametri mediante la variabile **WindowStart**, come illustrato di seguito. Lo script Hive usa quindi questa variabile per caricare i dati dall'apposita cartella relativa al giorno ed eseguire l'aggregazione per generare l'output.

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
		                    "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
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

Ecco il risultato dal punto di vista della dipendenza dei dati.

![Dipendenza dei dati](./media/data-factory-scheduling-and-execution/data-dependency.png)

Per ogni giorno, la sezione di output dipende dalle 24 sezioni orarie ottenute dal set di dati di input. La data factory calcola automaticamente queste dipendenze prevedendo le sezioni di dati di input che rientrano nello stesso periodo di tempo della sezione di output da produrre. Se una delle 24 sezioni di input non è disponibile (perché, ad esempio, nell'attività a monte che produce la sezione è in corso un processo di elaborazione), la data factory attenderà che la sezione di input sia pronta prima di avviare l'esecuzione di attività giornaliera.


### Esempio 2: Definizione di una dipendenza con espressioni e funzioni di data factory

Si consideri ora un altro scenario in cui si suppone che un'attività Hive elabori due set di dati di input e, mentre il primo ottiene nuovi dati giornalmente, l'altro riceve nuovi dati a cadenza settimanale. Si suppone inoltre di voler eseguire un join tra i due input e produrre un output giornalmente.
 
L'approccio semplice applicato fino a ora, in cui la data factory determina automaticamente le sezioni di input da elaborare identificando le sezioni di dati di input allineate al periodo di tempo delle sezioni di dati di output, non funziona più.

È necessario definire un nuovo approccio per ogni esecuzione di attività in cui la data factory possa usare la sezione di dati dell'ultima settimana per il set di dati di input settimanale. A questo scopo, è possibile avvalersi delle funzioni di Data factory di Azure, come illustrato di seguito.

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

Un file di output verrà rilasciato ogni giorno nella cartella relativa al giorno corrispondente. Il valore di disponibilità per l'output è impostato su "ogni giorno" (frequency: Day, interval: 1).
	
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
	            "startTime": "Date.AddDays(SliceStart,  -7 - Date.DayOfWeek(SliceStart))",
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
	            "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
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



## Variabili di sistema della data factory

Nome variabile | Descrizione | Ambito dell'oggetto | Ambito JSON e casi d'uso
------------- | ----------- | ------------ | ------------------------
WindowStart | Inizio dell'intervallo di tempo relativo alla finestra di esecuzione dell'attività | attività | <ol><li>Definizione delle query di selezione dei dati. Vedere gli articoli connettore a cui fa riferimento l'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md).</li><li>Passaggio dei parametri a uno script Hive (esempio illustrato in precedenza).</li>
WindowEnd | Fine dell'intervallo di tempo relativo alla finestra di esecuzione dell'attività | attività | come sopra
SliceStart | Inizio dell'intervallo di tempo relativo alla sezione di dati in fase di produzione | set di dati<br/>attività | <ol><li>Definizione di nomi di file e percorsi di cartelle dinamici durante l'utilizzo dell'[archivio BLOB di Azure](data-factory-azure-blob-connector.md) e dei [set di dati del File System](data-factory-onprem-file-system-connector.md).</li><li>Definizione delle dipendenze di input con le funzioni della data factory nella raccolta di input dell'attività.</li></ol>
SliceEnd | Fine dell'intervallo di tempo relativo alla sezione di dati in fase di produzione | set di dati<br/>attività | come sopra. 

> [AZURE.NOTE]La data factory richiede attualmente che la pianificazione specificata nell'attività corrisponda esattamente alla pianificazione specificata nella disponibilità del set di dati di output. In questo modo, il mapping delle variabili WindowStart, WindowEnd, SliceStart e SliceEnd verrà sempre eseguito allo stesso periodo di tempo e a un'unica sezione di output.
 
## Riferimenti alle funzioni della data factory

È possibile combinare le funzioni della data factory con le variabili di sistema sopra illustrate per gli scopi seguenti:

1.	Definizione delle query di selezione dei dati (vedere gli articoli connettore a cui fa riferimento l'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md).

	La sintassi per richiamare una funzione della data factory è **$$<function>** per le query di selezione dei dati e altre proprietà dell'attività e del set di dati.  
2. Definizione delle dipendenze di input con le funzioni della data factory nella raccolta di input dell'attività (vedere l'esempio precedente).

	La sintassi $$ non è necessaria per definire le espressioni delle dipendenze di input.

Nell'esempio seguente la proprietà **sqlReaderQuery** di un file JSON è assegnata a un valore restituito dalla funzione **Text.Format**. Questo esempio usa anche una variabile di sistema denominata **WindowStart**, che rappresenta l'ora di inizio della finestra di esecuzione dell'attività.
	
	{
	    "Type": "SqlSource",
	    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
	}

### Funzioni

Le tabelle seguenti elencano tutte le funzioni di Data factory di Azure.

Categoria | Funzione | Parametri | Descrizione
-------- | -------- | ---------- | ----------- 
Time | AddHours(X,Y) | X: DateTime <p>Y: int</p> | Aggiunge Y ore all'ora X specificata. <p>Esempio: 05/09/2013 12:00:00 + 2 ore = 05/09/2013 14:00:00</p>
Time | AddMinutes(X,Y) | X: DateTime <p>Y: int</p> | Aggiunge Y minuti a X.<p>Esempio: 15/09/2013 12:00:00 + 15 minuti = 15/09/2013 12:15:00</p>
Time | StartOfHour(X) | X: DateTime | Ottiene l'ora di inizio dell'ora rappresentata dal componente ora di X.<p>Esempio: StartOfHour di 15/09/2013 17:10:23 è 15/09/2013 17:00:00</p>
Date | AddDays(X,Y) | X: DateTime<p>Y: int</p> | Aggiunge Y giorni a X.<p>Esempio: 15/09/2013 12:00:00 + 2 giorni = 17/09/2013 12:00:00</p>
Date | AddMonths(X,Y) | X: DateTime<p>Y: int</p> | Aggiunge Y mesi a X.<p>Esempio: 15/09/2013 12:00:00 + 1 mese = 15/10/2013 12:00:00</p> 
Date | AddQuarters(X,Y) | X: DateTime <p>Y: int</p> | Aggiunge Y * 3 mesi a X.<p>Esempio: 15/09/2013 12:00:00 + 1 trimestre = 15/12/2013 12:00:00</p>
Date | AddWeeks(X,Y) | X: DateTime<p>Y: int</p> | Aggiunge Y * 7 giorni a X.<p>Esempio: 15/09/2013 12:00:00 + 1 settimana = 22/09/2013 12:00:00</p>
Date | AddYears(X,Y) | X: DateTime<p>Y: int</p> | Aggiunge Y anni a X.<p>Esempio: 15/09/2013 12:00:00 + 1 anno = 15/09/2014 12:00:00</p>
Date | Day(X) | X: DateTime | Ottiene il componente giorno di X.<p>Esempio: Day di 15/09/2013 12:00:00 è 9.</p>
Date | DayOfWeek(X) | X: DateTime | Ottiene il giorno del componente settimana di X.<p>Esempio: DayOfWeek di 15/09/2013 12:00:00 è domenica.</p>
Date | DayOfYear(X) | X: DateTime | Ottiene il giorno dell'anno rappresentato dal componente anno di X.<p>Esempi:<br/>01/12/2015: giorno 335 di 2015<br/>31/12/2015: giorno 365 di 2015<br/>31/12/2016: giorno 366 di 2016 (anno bisestile)</p>
Date | DaysInMonth(X) | X: DateTime | Ottiene i giorni del mese rappresentati dal componente mese del parametro X.<p>Esempio: DaysInMonth di 15/09/2013 corrisponde a 30 poiché sono presenti 30 giorni nel mese di settembre.</p>
Date | EndOfDay(X) | X: DateTime | Ottiene la data e ora che rappresenta la fine del giorno (componente giorno) X.<p>Esempio: EndOfDay di 15/09/2013 17:10:23 è 15/09/2013 23:59:59.</p>
Date | EndOfMonth(X) | X: DateTime | Ottiene la fine del mese rappresentato dal componente mese del parametro X.<p>Esempio: EndOfMonth di 15/09/2013 17:10:23 è 30/09/2013 23:59:59 (data e ora che rappresentano la fine del mese di settembre)</p>
Date | StartOfDay(X) | X: DateTime | Ottiene l'inizio del giorno rappresentato dal componente giorno del parametro X.<p>Esempio: StartOfDay di 15/09/2013 17:10:23 è 15/09/2013 00:00:00.</p>
DateTime | From(X) | X: String | Analizza la stringa X fino a una data/ora.
DateTime | Ticks(X) | X: DateTime | Ottiene la proprietà dei tick del parametro X. Un tick equivale a 100 nanosecondi. Il valore di questa proprietà rappresenta il numero di tick trascorsi dalla mezzanotte 00.00.00 del 1° gennaio 0001. 
Text | Format(X) | X: variabile stringa | Formatta il testo.

#### Esempio di Text.Format

	"defines": { 
	    "Year" : "$$Text.Format('{0:yyyy}',WindowStart)",
	    "Month" : "$$Text.Format('{0:MM}',WindowStart)",
	    "Day" : "$$Text.Format('{0:dd}',WindowStart)",
	    "Hour" : "$$Text.Format('{0:hh}',WindowStart)"
	}

> [AZURE.NOTE]Quando si usa una funzione all'interno di un'altra funzione, non è necessario usare il prefisso **$$** per la funzione interna. Ad esempio: $$Text.Format('PartitionKey eq \\'my\_pkey\_filter\_value\\' and RowKey ge \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). In questo esempio, il prefisso **$$** non viene usato per la funzione **Time.AddHours**.
  

## Approfondimento della dipendenza dei dati

Per generare una sezione di set di dati da un'esecuzione di attività, la data factory usa il **modello di dipendenza** seguente per determinare le relazioni tra i sei di dati usati da un'attività e quelli generati dall'attività.

L'intervallo di tempo dei set di dati di input necessario per generare la sezione di set di dati di output prende il nome di **periodo di dipendenza**.

Un'esecuzione di attività genera una sezione di set di dati solo dopo che sono disponibili le sezioni di dati nei set di dati di input compresi nel periodo di dipendenza. In questo modo, tutte le sezioni di input che rientrano nel periodo di dipendenza devono essere impostate sullo stato **Ready** affinché la sezione del set di dati di output possa essere generata da un'esecuzione di attività.

Per generare la sezione di set di dati [inizio, fine], è necessaria una funzione che esegua il mapping della sezione di set di dati al periodo di dipendenza. Questa funzione è costituita essenzialmente da una formula che converte l'inizio e la fine della sezione di set di dati nell'inizio e nella fine del periodo di dipendenza. Più formalmente,
	
	DatasetSlice = [start, end]
	DependecyPeriod = [f(start, end), g(start, end)]

in cui f e g sono funzioni di mapping che consentono di calcolare l'inizio e la fine del periodo di dipendenza per ogni input di attività.

Come dimostrato dagli esempi precedenti, nella maggior parte dei casi il periodo di dipendenza corrisponde al periodo in cui viene prodotta la sezione di dati. In questi casi, la data factory calcola automaticamente le sezioni di input che rientrano nel periodo di dipendenza.

Nell'esempio di aggregazione precedente, in cui l'output viene prodotto giornalmente e i dati di input sono disponibili ogni ora, il periodo della sezione di dati è di 24 ore. La data factory identifica le sezioni di input orarie relative a questo periodo di tempo e rende la sezione di output dipendente dalla sezione di input.

È possibile fornire anche un mapping personalizzato per il periodo di dipendenza, come illustrato nell'esempio precedente in cui uno degli input era settimanale, mentre la sezione di output veniva generata giornalmente.
   
## Convalida e dipendenza dei dati

Se necessario, per un set di dati è possibile definire anche un criterio di convalida che specifica in che modo i dati generati dall'esecuzione di una sezione possono essere convalidati prima che siano pronti per l'uso. Per informazioni dettagliate, vedere l'argomento relativo alla [creazione di set di dati](data-factory-create-datasets.md).

In questi casi, al termine del processo di esecuzione, lo stato della sezione di output viene impostato su **Waiting** con lo stato secondario impostato su **Validation**. Al termine della convalida, lo stato viene impostato invece su **Ready**.
   
Se una sezione di dati è stata correttamente generata ma non ha superato il processo di convalida, le esecuzioni di attività per le sezioni a valle dipendenti dalla sezione non riuscita non vengono elaborate.

In [Monitorare e gestire le pipeline di Data factory di Azure](data-factory-monitor-manage-pipelines.md) vengono descritti i vari stati disponibili per le sezioni di dati della data factory.

## Dati esterni

Se un set di dati viene contrassegnato come esterno (come illustrato nel file JSON seguente), significa che non è stato generato con Data factory di Azure. In questo caso, al criterio del set di dati è possibile applicare un set di parametri aggiuntivo che definisce i criteri di convalida e ripetizione per il set di dati. Per una descrizione di tutte le proprietà, vedere l'articolo sulla [creazione di pipeline](data-factory-create-pipelines.md).

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






  









 
 












      

  

<!---HONumber=August15_HO7-->