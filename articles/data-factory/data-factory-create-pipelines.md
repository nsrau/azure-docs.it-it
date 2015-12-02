<properties 
	pageTitle="Creazione di pipeline" 
	description="Informazioni sulle pipeline di Data factory di Azure e su come crearle per spostare e trasformare i dati al fine di produrre informazioni che possano essere utilizzate per una maggiore conoscenza" 
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
	ms.topic="article" y
	ms.date="10/20/2015" 
	ms.author="spelluru"/>

# Informazioni su pipeline e attività
Questo articolo consentirà di conoscere le pipeline e le attività in Data factory di Azure e su come sfruttarle per costruire flussi di lavoro end-to-end basati sui dati per lo scenario o l'azienda. In questo articolo si presuppone di avere verificato gli articoli sulla [panoramica](data-factory-introduction.md) e sulla [ creazione di set di dati](data-factory-create-datasets.md).

## Che cos'è una pipeline?
**La pipeline è un raggruppamento logico di attività**. Tali attività vengono usate per raggruppare in un'unità attività che eseguono un'operazione. Per comprendere meglio le pipeline, è necessario comprendere innanzitutto un'attività.

### Che cos'è un'attività?
Le attività definiscono le azioni da eseguire sui dati. Ogni attività accetta zero o più [set di dati](data-factory-create-datasets.md) come input e produce uno o più set di dati come output. **Un'attività è un'unità di orchestrazione in Data factory di Azure.**

Ad esempio, è possibile usare un'attività Copia per orchestrare la copia dei dati da un set di dati a un altro. Allo stesso modo, è possibile usare un'attività Hive che esegue una query Hive su un cluster di Azure HDInsight per trasformare o analizzare i dati. Data factory di Azure offre un’ampia gamma di [attività di trasformazione, analisi ](data-factory-data-transformation-activities.md) e [spostamento dei dati](data-factory-data-movement-activities.md). È inoltre possibile scegliere di creare un'attività personalizzata .NET per eseguire il proprio codice.

Considerare i due set di dati indicati di seguito:

**Set di dati di SQL Azure**

La tabella "MyTable" contiene una colonna 'timestampcolumn' consente di specificare il valore datetime relativo al momento in cui i dati sono stati inseriti nel database.

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

**Set di dati dell'archivio BLOB di Azure**

I dati vengono copiati in un nuovo BLOB ogni ora e il percorso del BLOB riflette la data e l'ora specifiche con granularità oraria.

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


L'attività di copia nella pipeline seguente copia i dati da SQL Azure all'Archivio BLOB di Azure. Accetta una tabella di SQL Azure come set di dati di input con frequenza oraria e scrive i dati nell’archivio BLOB di Azure rappresentato dal set di dati 'AzureBlobOutput'. Anche il set di dati di output ha una frequenza oraria. Vedere la sezione relativa alla pianificazione e all’esecuzione per comprendere come i dati vengono copiati nell’unità di tempo. Questa pipeline ha un periodo attivo di 3 ore da "2015-01-01T08:00:00" a "2015-01-01T11:00:00".

**Pipeline:**
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-01-01T08:00:00",
	    "end":"2015-01-01T11:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
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
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

Ora che abbiamo una breve conoscenza di cosa sia un'attività, passiamo a vedere nuovamente cos’è la pipeline.
 
La pipeline è un raggruppamento logico di attività. Tali attività vengono usate per raggruppare in un'unità attività che eseguono un'operazione. **Una pipeline è anche l'unità di distribuzione e gestione per le attività.** Ad esempio, è possibile che si desideri raggruppare attività correlate logicamente come una pipeline, in modo che possano essere nello stato attivo o sospeso insieme.

Un set di dati di output di un'attività in una pipeline può essere il set di dati di input per un’altra attività nella stessa pipeline o in un’altra pipeline, mediante la definizione delle dipendenze tra attività. Tutto questo è illustrato dettagliatamente nella sezione relativa alla [pianificazione e all’esecuzione](#scheduling-and-execution).

I passaggi tipici durante la creazione di una pipeline in Data factory di Azure sono:

1.	Creare una data factory (se non è stata creata).
2.	Creare un servizio collegato per ciascun archivio dati o calcolo.
3.	Creare set di dati di input e di output.
4.	Creare una pipeline con le attività che operano sui set di dati definiti in precedenza.

![Entità di Data factory](./media/data-factory-create-pipelines/entities.png)

Osserviamo più da vicino come viene definita una pipeline.

## Anatomia di una pipeline  

La struttura generica di una pipeline è simile a quella indicata di seguito:

	{
	    "name": "PipelineName",
	    "properties": 
	    {
	        "description" : "pipeline description",
	        "activities":
	        [
	
	        ],
			"start": "<start date-time>",
			"end": "<end date-time>"
	    }
	}

Nella sezione delle **attività** possono essere definite una o più attività. Ogni attività presenta la seguente struttura di livello superiore:

	{
	    "name": "ActivityName",
	    "description": "description", 
	    "type": "<ActivityType>",
	    "inputs":  "[]",
	    "outputs":  "[]",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties":
	    {
	
	    },
	    "policy":
	    {
	    }
	    "scheduler":
	    {
	    }
	}

Nella tabella seguente vengono descritte le proprietà all'interno delle definizioni e le definizioni JSON della pipeline:

Tag | Descrizione | Obbligatorio
--- | ----------- | --------
name | Nome dell'attività o della pipeline. Specificare un nome che rappresenta l'azione che l'attività o la pipeline è configurata per eseguire<br/><ul><li>Numero massimo di caratteri: 260</li><li>Deve iniziare con una lettera, un numero o un carattere di sottolineatura (_)</li><li>I caratteri seguenti non sono consentiti: “.”, “+”, “?”, “/”, “<”,”>”,”*”,”%”,”&”,”:”,”\\”</li></ul> | Sì
description | Testo che descrive l’uso previsto per l'attività o la pipeline | Sì
type | Specifica il tipo dell'attività. Vedere gli articoli sulle [attività di sposatmento dei dati](data-factory-data-movement-activities.md) e [di trasformazione dei dati](data-factory-data-transformation-activities.md) per i diversi tipi di attività. | Sì
input | Le tabelle di input utilizzate dall’attività<p>// una tabella di input<br/>"inputs": [ { "name": "inputtable1" } ],</p><p>// due tabelle di input <br/>"inputs": [ { "name": "inputtable1" }, { "name": "inputtable2" } ],</p> | Sì
outputs | Le tabelle di output utilizzate dall’attività.<p>// una tabella di output<br/>"outputs": [ { "name": “outputtable1” } ],</p><p>//due tabelle di output<br/>"outputs": [ { "name": “outputtable1” }, { "name": “outputtable2” } ],</p> | Sì
linkedServiceName | Nome del servizio collegato utilizzato dall’attività. <p>Un'attività può richiedere di specificare il servizio collegato che si collega all'ambiente di calcolo richiesto.</p>| Sì per l’attività HDInsight e per l’attività di assegnazione dei punteggi di batch di Azure Machine Learning<p>No per tutte le altre</p>
typeProperties | Le proprietà della sezione typeProperties dipendono dal tipo di attività. Consultare l'articolo su ogni singola attività per ulteriori informazioni sull’attività | No
policy | Criteri che influiscono sul comportamento in fase di esecuzione dell'attività. Se vengono omessi, verranno usati i criteri predefiniti. Scorrere di seguito per informazioni dettagliate | No
start | Data e ora di inizio della pipeline. Devono essere nel [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio 2014-10-14T16:32:41Z. <p>Le proprietà start ed end insieme specificano il periodo attivo per la pipeline. Le sezioni di output vengono generate solo in questo periodo attivo.</p> | No<p>Se si specifica un valore per la proprietà end, è necessario specificare il valore per la proprietà start.</p><p>L'ora di inizio e l'ora di fine possono essere entrambe vuote per creare una pipeline, ma entrambe devono avere un valore per impostare un periodo attivo per l'esecuzione della pipeline. Se non si specificano le ore di inizio e fine durante la creazione di una pipeline, è possibile impostare tali valori in un secondo momento mediante il cmdlet Set-AzureDataFactoryPipelineActivePeriod.</p>
end | Data-ora di fine per la pipeline. Se specificate, devono essere in formato ISO. Ad esempio: 2014-10-14T17:32:41Z <p>Per eseguire la pipeline per un tempo illimitato, specificare 9999-09-09 come valore della proprietà end.</p>| No <p>Se si specifica un valore per la proprietà start, è necessario specificare il valore per la proprietà end.</p><p>Vedere le note della proprietà **start**.</p>
isPaused | Se impostata su true, la pipeline non viene eseguita. Valore predefinito = false. È possibile utilizzare questa proprietà per abilitare o disabilitare. | No
scheduler | La proprietà “scheduler” viene utilizzata per definire la pianificazione per l’attività. Le relative proprietà secondarie sono quelle indicate nella [proprietà availability in un set di dati](data-factory-create-datasets.md#Availability). | No |   

### Tipi di attività
Data factory di Azure fornisce una vasta gamma di attività di [spostamento dei dati](data-factory-data-movement-activities.md) e [trasformazione dei dati](data-factory-data-transformation-activities.md).

### Criteri
I criteri influiscono sul comportamento in fase di esecuzione di un'attività, in particolare quando viene elaborata la sezione di una tabella. La tabella seguente fornisce informazioni dettagliate.

Proprietà | Valori consentiti | Valore predefinito | Descrizione
-------- | ----------- | -------------- | ---------------
Concorrenza | Integer <p>Valore massimo: 10</p> | 1 | Numero di esecuzioni simultanee dell'attività.<p>Determina il numero di esecuzioni di attività parallele che possono verificarsi in sezioni diverse. Ad esempio, se un'attività deve passare attraverso grandi set di dati disponibili, con una concorrenza maggiore che consente di velocizzare l'elaborazione dei dati.</p> 
executionPriorityOrder | NewestFirst<p>OldestFirst</p> | OldestFirst | Determina l'ordine delle sezioni di dati che vengono elaborate.<p>Ad esempio, se si dispone di 2 sezioni (una alle 16.00 e l'altra alle 17.00) ed entrambe sono in attesa di esecuzione. Se si imposta executionPriorityOrder su NewestFirst, verrà elaborata per prima la sezione delle 17.00. Allo stesso modo, se si imposta executionPriorityOrder su OldestFirst, verrà elaborata per prima la sezione alle 16.00.</p> 
retry | Integer<p>Il valore massimo può essere 10</p> | 3 | Numero di tentativi prima che l'elaborazione dei dati per la sezione sia contrassegnata come errore. L'esecuzione dell’attività per una sezione di dati viene ritentata fino al numero di tentativi specificato. Il tentativo viene eseguito appena possibile dopo l'errore.
timeout | TimeSpan | 00:00:00 | Timeout per l'attività. Esempio: 00:10:00 (implica un timeout di 10 minuti)<p>Se un valore viene omesso oppure è 0, il timeout è infinito.</p><p>Se il tempo di elaborazione dei dati in una sezione supera il valore di timeout, viene annullato e il sistema tenta di ripetere l'elaborazione. Il numero di tentativi dipende dalla proprietà retry. Quando si verifica il timeout, lo stato sarà TimedOut.</p>
delay | TimeSpan | 00:00:00 | Specificare il ritardo prima che l'elaborazione dei dati della sezione abbia inizio.<p>L'esecuzione dell'attività per una sezione di dati viene avviata una volta che la proprietà delay ha superato il tempo di esecuzione previsto.</p><p>Esempio: 00:10:00 (implica un ritardo di 10 minuti)</p>
longRetry | Integer<p>Valore massimo: 10</p> | 1 | Il numero di tentativi longRetry dell'esecuzione della sezione esecuzione non è riuscito.<p>I tentativi longRetry sono distanziati in base a longRetryInterval. Pertanto, se è necessario specificare un tempo tra i tentativi, utilizzare longRetry. Se viene specificato sia Retry che longRetry, ogni tentativo longRetry includerà dei tentativi Retry e il numero massimo di tentativi sarà Retry * longRetry.</p><p>Ad esempio, se nel criterio di attività è presente quanto segue:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/></p><p>è presumibile che la sezione da eseguire sia una soltanto (lo stato è PendingExecution) e l'esecuzione dell'attività ha ogni volta esito negativo. All’inizio vi saranno tre tentativi di esecuzione consecutivi. Dopo ogni tentativo, lo stato della sezione sarà Retry. Una volta terminati i tre tentativi sulla sezione, lo stato sarà LongRetry.</p><p>Dopo un'ora (vale a dire il valore di longRetryInteval), verrà eseguita un'altra serie di tre tentativi di esecuzione consecutivi. Al termine, lo stato della sezione sarà Failed e non verranno eseguiti altri tentativi. Pertanto, sono stati eseguiti sei tentativi.</p><p>Nota: se l'esecuzione ha esito positivo, lo stato della sezione sarà Ready e non verranno effettuati altri tentativi.</p><p>longRetry può essere usato nelle situazioni in cui i dati dipendenti arrivano in orari non deterministici o l'ambiente complessivo in cui si verifica l'elaborazione dei dati è abbastanza debole. In tali casi, l'esecuzione di tentativi consecutivi potrebbe non essere utile, mentre l'applicazione di un intervallo consente di ottenere il risultato desiderato.</p><p>Attenzione: non impostare valori elevati per longRetry o longRetryInterval. In genere, valori più elevati implicano altri problemi sistemici che vengono eliminati con questo sistema</p> 
longRetryInterval | TimeSpan | 00:00:00 | Il ritardo tra tentativi longRetry 

## Creazione e gestione di una pipeline
Data factory di Azure fornisce vari meccanismi per creare e distribuire le pipeline (che a loro volta contengono all’interno una o più attività).

### Utilizzo del Portale di anteprima di Azure

1. Eseguire l'accesso al [portale di anteprima di Azure](https://portal.azure.com/).
2. Passare all'istanza di Data factory di Azure in cui si desidera creare una pipeline
3. Fare clic sul riquadro **Crea e distribuisci** nella sezione **Riepilogo**. 
 
	![Riquadro Creare e distribuire](./media/data-factory-create-pipelines/author-deploy-tile.png)

4. Fare clic su **Nuova pipeline** sulla barra dei comandi.

	![Pulsante Nuova pipeline](./media/data-factory-create-pipelines/new-pipeline-button.png)

5. Si noterà la finestra dell'editor con il modello JSON della pipeline.

	![Editor pipeline](./media/data-factory-create-pipelines/pipeline-in-editor.png)

6. Dopo aver completato la creazione della pipeline, fare clic su **Distribuisci** sulla barra dei comandi per distribuire la pipeline.

	**Nota:** durante la distribuzione, il servizio Data factory di Azure esegue alcuni controlli di convalida per risolvere alcuni problemi comuni. Nel caso in cui si verifica un errore, verranno visualizzate le informazioni corrispondenti. Intraprendere azioni correttive e quindi distribuire nuovamente la pipeline creata. È possibile utilizzare l'editor per aggiornare ed eliminare una pipeline.

### Utilizzo del plug-in Visual Studio
È possibile utilizzare Visual Studio per creare e distribuire pipeline a Data factory di Azure. Per altre informazioni, fare riferimento all'esercitazione relativa alla [copia dei dati dall'archivio di Azure ad Azure SQL (Visual Studio)](data-factory-get-started-using-vs.md).

### Uso di Azure PowerShell
È possibile utilizzare Azure PowerShell per creare pipeline nella Data factory di Azure. Ad esempio, è stata definita la pipeline di JSON in un file nella posizione c:\\DPWikisample.json. È possibile caricarla nell'istanza Data factory di Azure, come illustrato nell'esempio seguente.

	New-AzureDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Per altre informazioni su questo cmdlet, vedere il cmdlet [New-AzureDataFactoryPipeline](https://msdn.microsoft.com/library/dn820227.aspx).

### Utilizzo dell'API REST
È possibile creare e distribuire la pipeline anche tramite le API REST. Questo meccanismo può essere utilizzato per creare pipeline a livello di programmazione. Per altre informazioni su questo argomento, vedere [Creare o aggiornare una pipeline](https://msdn.microsoft.com/library/azure/dn906741.aspx).

### Utilizzo di .NET SDK
È possibile creare e distribuire la pipeline anche tramite .NET SDK. Questo meccanismo può essere utilizzato per creare pipeline a livello di programmazione. Per altre informazioni su questo argomento, vedere [Creazione, monitoraggio e gestione delle istanze di Data factory di Azure mediante Data Factory .NET SDK](data-factory-create-data-factories-programmatically.md).


## Pianificazione ed esecuzione
Finora si è appreso che cosa si intende per pipeline e attività. Inoltre è stato dato uno sguardo al modo in cui vengono definite e a una visualizzazione di alto livello delle attività in Data factory di Azure. Ora osserviamo in che modo vengono eseguite.

Una pipeline è attiva solo tra l'ora di inizio e l’ora di fine. Non viene eseguita prima dell'ora di inizio o dopo l'ora di fine. Se la pipeline viene sospesa, non viene eseguita indipendentemente dall'ora di inizio e di fine. Per essere eseguita, una pipeline non deve essere in pausa.

In realtà, non è la pipeline a essere eseguita, bensì le attività all’interno della pipeline. Tuttavia, l’esecuzione avviene nel contesto generale della pipeline. Vedere [Pianificazione ed esecuzione con Data factory](data-factory-scheduling-and-execution.md)per comprendere il funzionamento della pianificazione e dell'esecuzione in Data factory di Azure.

## Gestione e monitoraggio  
Una volta distribuita una pipeline, è possibile gestire e monitorare le pipeline, le sezioni e le esecuzioni. Per altre informazioni, vedere: [Monitorare e gestire le pipeline](data-factory-monitor-manage-pipelines.md).

## Passaggi successivi

- Informazioni sulla [pianificazione e l'esecuzione in Data factory di Azure](data-factory-scheduling-and-execution.md).  
- Per altre informazioni sulle funzionalità di [spostamento dei dati](data-factory-data-movement-activities.md) e [trasformazione dei dati](data-factory-data-transformation-activities.md), vedere Data factory di Azure
- Informazioni sulla [gestione e il monitoraggio in Data factory di Azure](data-factory-monitor-manage-pipelines.md).
- [Creare e distribuire la prima pipeline](data-factory-build-your-first-pipeline.md). 


 

   













 
 


 

 

<!---HONumber=Nov15_HO3-->