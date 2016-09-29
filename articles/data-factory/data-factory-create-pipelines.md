<properties 
	pageTitle="Creare e pianificare pipeline e concatenare attività in Azure Data Factory | Microsoft Azure" 
	description="Informazioni su come creare una pipeline di dati in Azure Data Factory per spostare e trasformare i dati. Creare un flusso di lavoro basato sui dati per produrre informazioni pronte per l'uso." 
    keywords="pipeline di dati, flusso di lavoro basato sui dati"
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
	ms.date="09/12/2016" 
	ms.author="spelluru"/>

# Pipeline e attività in Azure Data Factory: creare e pianificare pipeline e concatenare attività
Questo articolo fornisce informazioni sulle pipeline e sulle attività in Azure Data Factory e su come usarle per costruire flussi di lavoro end-to-end basati sui dati per lo scenario.

> [AZURE.NOTE] Questo articolo presuppone che siano già stati letti gli articoli [Introduzione al servizio Azure Data Factory, un servizio di integrazione dati nel cloud](data-factory-introduction.md) e [Set di dati in Azure Data Factory](data-factory-create-datasets.md). Se non si ha esperienza diretta nella creazione di data factory, l'[Esercitazione: Creare la prima data factory (panoramica)](data-factory-build-your-first-pipeline.md) può essere utile per comprendere meglio questo articolo.

## Che cos'è una pipeline di dati?
**La pipeline è un raggruppamento logico di attività**. Tali attività vengono usate per raggruppare in un'unità attività che eseguono un'operazione. Per comprendere meglio le pipeline, è necessario comprendere innanzitutto un'attività.

## Che cos'è un'attività?
Le attività definiscono le azioni da eseguire sui dati. Ogni attività accetta zero o più [set di dati](data-factory-create-datasets.md) come input e produce uno o più set di dati come output. **Un'attività è un'unità di orchestrazione in Data factory di Azure.**

Ad esempio, è possibile usare un'attività Copia per orchestrare la copia dei dati da un set di dati a un altro. Allo stesso modo, è possibile usare un'attività HDInsight per eseguire una query Hive su un cluster di Azure HDInsight e trasformare o analizzare i dati. Data factory di Azure offre un’ampia gamma di [attività di trasformazione, analisi ](data-factory-data-transformation-activities.md) e [spostamento dei dati](data-factory-data-movement-activities.md). È inoltre possibile scegliere di creare un'attività personalizzata .NET per eseguire il proprio codice.

Prendere in considerazione i due set di dati indicati di seguito:

**Set di dati di SQL Azure**

La tabella "MyTable" contiene una colonna "timestampcolumn", che consente di specificare il valore datetime relativo al momento in cui i dati sono stati inseriti nel database.

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

I dati vengono copiati in un nuovo BLOB ogni ora. Il percorso del BLOB riflette data e ora specifiche con la granularità basata sull'ora.

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


L'attività di copia nella pipeline seguente copia i dati da Azure SQL all'archivio BLOB di Azure. Accetta una tabella di SQL Azure come set di dati di input con frequenza oraria e scrive i dati nell’archivio BLOB di Azure rappresentato dal set di dati 'AzureBlobOutput'. Anche il set di dati di output ha una frequenza oraria. Per comprendere in che modo i dati vengono copiati nell'unità di tempo, vedere la sezione [Pianificazione ed esecuzione](#scheduling-and-execution). Questa pipeline ha un periodo attivo di tre ore da "2015-01-01T08:00:00" a "2015-01-01T11:00:00".

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

Dopo avere brevemente illustrato cosa sia un'attività, si esaminerà nuovamente la pipeline.
 
La pipeline è un raggruppamento logico di attività. Tali attività vengono usate per raggruppare in un'unità attività che eseguono un'operazione. **Una pipeline è anche l'unità di distribuzione e gestione per le attività.** Ad esempio, è possibile che si desideri raggruppare attività correlate logicamente come una pipeline, in modo che possano essere nello stato attivo o sospeso insieme.

Un set di dati di output di un'attività in una pipeline può essere il set di dati di input per un’altra attività nella stessa pipeline o in un’altra pipeline, mediante la definizione delle dipendenze tra attività. Per altre informazioni, vedere la sezione [Pianificazione ed esecuzione](#chaining-activities).

I passaggi tipici durante la creazione di una pipeline in Data factory di Azure sono:

1.	Creare una data factory (se non è stata creata).
2.	Creare un servizio collegato per ciascun archivio dati o calcolo.
3.	Creare set di dati di input e di output.
4.	Creare una pipeline con le attività che operano sui set di dati.

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
name | Nome dell'attività o della pipeline. Specificare un nome che rappresenta l'azione che l'attività o la pipeline è configurata per eseguire<br/><ul><li>Numero massimo di caratteri: 260</li><li>Deve iniziare con una lettera, un numero o un carattere di sottolineatura (\_)</li><li>I caratteri seguenti non sono consentiti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\"</li></ul> | Sì
Descrizione | Testo descrittivo per lo scopo dell'attività o della pipeline | Sì
type | Specifica il tipo di attività. Per informazioni sui diversi tipi di attività, vedere gli articoli [Attività di spostamento dei dati](data-factory-data-movement-activities.md) e [Trasformazione e analisi tramite Azure Data Factory](data-factory-data-transformation-activities.md). | Sì
inputs | Tabelle di input usate dall'attività<br/><br/>// una tabella di input<br/>"inputs": [ { "name": "inputtable1" } ],<br/><br/>// due tabelle di input <br/>"inputs": [ { "name": "inputtable1" }, { "name": "inputtable2" } ], | Sì
outputs | Tabelle di output usate dall'attività// una tabella di output<br/>"outputs": [ { "name": “outputtable1” } ],<br/><br/>//due tabelle di output<br/>"outputs": [ { "name": “outputtable1” }, { "name": “outputtable2” } ], | Sì
linkedServiceName | Nome del servizio collegato usato dall'attività. <br/><br/>Per un'attività può essere necessario specificare il servizio collegato che collega all'ambiente di calcolo richiesto. | Sì per Attività di HDInsight e Attività di assegnazione punteggio batch di Azure Machine Learning <br/><br/>No per tutto il resto
typeProperties | Le proprietà nella sezione typeProperties dipendono dal tipo di attività. | No
policy | Criteri che influiscono sul comportamento di runtime dell'attività. Se vengono omessi, vengono usati i criteri predefiniti. | No
start | Data e ora di inizio per la pipeline. Devono essere nel [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio: 2014-10-14T16:32:41Z. <br/><br/>È possibile specificare un'ora locale, ad esempio in base al fuso orario EST. Ecco un esempio: "2016-02-27T06:00:00**-05:00**", ossia le 6.00 EST.<br/><br/>Le proprietà start ed end insieme specificano il periodo attivo per la pipeline. Le sezioni di output vengono generate solo in questo periodo attivo. | No<br/><br/>Se si specifica un valore per la proprietà end, è necessario specificare il valore per la proprietà start.<br/><br/>L'ora di inizio e l'ora di fine possono essere entrambe vuote per creare una pipeline. Entrambe devono avere un valore per impostare un periodo attivo per l'esecuzione della pipeline. Se non si specificano le ore di inizio e fine durante la creazione di una pipeline, è possibile impostare tali valori in un secondo momento usando il cmdlet Set-AzureRmDataFactoryPipelineActivePeriod.
end | Data e ora di fine per la pipeline. Se specificate, devono essere in formato ISO. Ad esempio: 2014-10-14T17:32:41Z <br/><br/>È possibile specificare un'ora locale, ad esempio in base al fuso orario EST. Ecco un esempio: "2016-02-27T06:00:00**-05:00**", ossia le 6.00 EST.<br/><br/>Per eseguire la pipeline illimitatamente, specificare 9999-09-09 come valore per la proprietà end. | No <br/><br/>Se si specifica un valore per la proprietà start, è necessario specificare il valore per la proprietà end.<br/><br/>Vedere le note della proprietà **start**.
isPaused | Se impostato su true, la pipeline non viene eseguita. Valore predefinito = false. È possibile usare questa proprietà per abilitare o disabilitare. | No 
scheduler | La proprietà "scheduler" viene usata per definire la pianificazione per l'attività. Le relative proprietà secondarie sono quelle indicate nella sezione [Disponibilità dei set di dati](data-factory-create-datasets.md#Availability). | No |   
| pipelineMode | Metodo di pianificazione delle esecuzioni per la pipeline. I valori consentiti sono scheduled (predefinito) e onetime.<br/><br/>"Scheduled" indica che la pipeline viene eseguita a intervalli di tempo specificati in base al periodo di attività, ovvero all'ora di inizio e di fine. "Onetime" indica che la pipeline viene eseguita una sola volta. Al momento, dopo aver creato una pipeline monouso non è possibile modificarla o aggiornarla. Per informazioni dettagliate sull'impostazione onetime, vedere la sezione [Pipeline monouso](data-factory-scheduling-and-execution.md#onetime-pipeline). | No | 
| expirationTime | Periodo di tempo dopo la creazione in cui la pipeline è valida e deve rimanere con provisioning eseguito. Se non ha esecuzioni attive, non riuscite o in sospeso, quando viene raggiunta la scadenza, la pipeline viene eliminata automaticamente. | No | 
| datasets | Elenco dei set di dati usati dalle attività definite nella pipeline. Questa proprietà può essere usata per definire i set di dati specifici di questa pipeline e non definiti all'interno della data factory. I set di dati definiti all'interno di questa pipeline possono essere usati solo da questa pipeline e non possono essere condivisi. Per informazioni dettagliate, vedere la sezione [Set di dati con ambito](data-factory-create-datasets.md#scoped-datasets).| No |  
 

## Tipi di attività per lo spostamento e la trasformazione dei dati
Data factory di Azure fornisce una vasta gamma di attività di [spostamento dei dati](data-factory-data-movement-activities.md) e [trasformazione dei dati](data-factory-data-transformation-activities.md).

### Criteri
I criteri influiscono sul comportamento in fase di esecuzione di un'attività, in particolare quando viene elaborata la sezione di una tabella. La tabella seguente fornisce informazioni dettagliate.

Proprietà | Valori consentiti | Valore predefinito | Descrizione
-------- | ----------- | -------------- | ---------------
Concorrenza | Integer <br/><br/>Valore massimo: 10 | 1 | Numero di esecuzioni simultanee dell'attività.<br/><br/>Determina il numero di esecuzioni di attività parallele che possono verificarsi in sezioni diverse. Ad esempio, se un'attività deve passare attraverso grandi set di dati disponibili, con un valore di concorrenza maggiore che consente di velocizzare l'elaborazione dei dati. 
executionPriorityOrder | NewestFirst<br/><br/>OldestFirst | OldestFirst | Determina l'ordine delle sezioni di dati che vengono elaborate.<br/><br/>Ad esempio nel caso in cui si abbiano 2 sezioni, una alle 16.00 e l'altra alle 17.00, ed entrambe siano in attesa di esecuzione. Se si imposta executionPriorityOrder su NewestFirst, viene elaborata per prima la sezione delle 17:00. Allo stesso modo, se si imposta executionPriorityORder su OldestFIrst, verrà elaborata per prima la sezione delle 16:00. 
retry | Integer<br/><br/>Valore massimo: 10 | 3 | Numero di tentativi prima che l'elaborazione dei dati per la sezione sia contrassegnata come errore. L'esecuzione dell’attività per una sezione di dati viene ritentata fino al numero di tentativi specificato. Il tentativo viene eseguito appena possibile dopo l'errore.
timeout | TimeSpan | 00:00:00 | Timeout per l'attività. Esempio: 00:10:00 (implica un timeout di 10 minuti)<br/><br/>Se un valore viene omesso oppure è 0, il timeout è infinito.<br/><br/>Se il tempo di elaborazione dei dati in una sezione supera il valore di timeout, viene annullato e il sistema prova a ripetere l'elaborazione. Il numero di tentativi dipende dalla proprietà retry. Quando si verifica il timeout, lo stato viene impostato su TimedOut.
delay | TimeSpan | 00:00:00 | Specificare il ritardo prima che abbia inizio l'elaborazione dei dati della sezione.<br/><br/>L'esecuzione dell'attività per una sezione di dati viene avviata non appena il ritardo supera il tempo di esecuzione previsto.<br/><br/>Esempio: 00:10:00 (implica un ritardo di 10 minuti)
longRetry | Integer<br/><br/>Valore massimo: 10 | 1 | Numero di tentativi estesi prima che l'esecuzione della sezione dia esito negativo.<br/><br/>I tentativi longRetry sono distanziati da longRetryInterval. Pertanto, se è necessario specificare un tempo tra i tentativi, utilizzare longRetry. Se si specificano sia Retry che longRetry, ogni tentativo longRetry include tentativi Retry e il numero massimo di tentativi corrisponde a Retry * longRetry.<br/><br/>Ad esempio, se nei criteri attività è presente quanto segue:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>si presume che la sezione da eseguire sia solo una, con stato Waiting, e che l'esecuzione dell'attività abbia ogni volta esito negativo. All’inizio vi saranno tre tentativi di esecuzione consecutivi. Dopo ogni tentativo, lo stato della sezione sarà Retry. Una volta terminati i 3 tentativi sulla sezione, lo stato sarà LongRetry.<br/><br/>Dopo un'ora (vale a dire il valore di longRetryInteval), verrà eseguita un'altra serie di 3 tentativi di esecuzione consecutivi. Al termine, lo stato della sezione sarà Failed e non verranno eseguiti altri tentativi. Quindi, sono stati eseguiti 6 tentativi.<br/><br/>Se l'esecuzione ha esito positivo, lo stato della sezione sarà Ready e non vengono effettuati altri tentativi.<br/><br/>longRetry può essere usato nelle situazioni in cui i dati dipendenti arrivano in orari non deterministici o l'ambiente complessivo in cui si verifica l'elaborazione dei dati è debole. In tali casi, l'esecuzione di tentativi consecutivi potrebbe non essere utile, mentre l'applicazione di un intervallo consente di ottenere il risultato voluto.<br/><br/>Attenzione: non impostare valori elevati per longRetry o longRetryInterval. In genere, valori più elevati comportano altri problemi sistemici. 
longRetryInterval | TimeSpan | 00:00:00 | Il ritardo tra tentativi longRetry 

## Concatenamento di attività
Se si hanno più attività in una pipeline e l'output di un'attività non è l'input di un'altra attività, le attività possono essere eseguite in parallelo se le sezioni di dati di input per le attività sono pronte.

È possibile concatenare due attività usando il set di dati di output di un'attività come set di dati di input di altre attività. Le attività possono essere nella stessa pipeline o in pipeline diverse. La seconda attività viene eseguita solo quando la prima viene completata correttamente.

Ad esempio, si consideri il caso seguente:
 
1.	La pipeline P1 include l'attività A1 che richiede il set di dati di input esterno D1 e produce il set di dati di **output** **D2**.
2.	La pipeline P2 include l'attività A2 che richiede l'**input** del set di dati **D2** e produce il set di dati di output D3.
 
In questo scenario, l'attività A1 viene eseguita quando i dati esterni sono disponibili e viene raggiunta la frequenza di disponibilità pianificata. L'attività A2 viene eseguita quando le sezioni pianificate di D2 diventano disponibili e viene raggiunta la frequenza di disponibilità pianificata. Se è presente un errore in una delle sezioni del set di dati D2, A2 non verrà eseguita per tale sezione fino a quando non diventa disponibile.

Vista diagramma:

![Concatenamento di attività in due pipeline](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

Vista diagramma con entrambe le attività nella stessa pipeline:

![Concatenamento di attività nella stessa pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

## Pianificazione ed esecuzione
Finora si è appreso che cosa si intende per pipeline e attività. È anche stato esaminato il modo in cui vengono definite ed è stata fornita una panoramica generale delle attività in Azure Data Factory. Ora si osserverà come vengono eseguite.

Una pipeline è attiva solo tra l'ora di inizio e l’ora di fine. Non viene eseguita prima dell'ora di inizio o dopo l'ora di fine. Se la pipeline viene sospesa, non viene eseguita indipendentemente dall'ora di inizio e di fine. Per essere eseguita, una pipeline non deve essere in pausa. In realtà, non è la pipeline a essere eseguita, ma le attività nella pipeline. Tuttavia, l’esecuzione avviene nel contesto generale della pipeline.

Vedere [Pianificazione ed esecuzione con Data factory](data-factory-scheduling-and-execution.md)per comprendere il funzionamento della pianificazione e dell'esecuzione in Data factory di Azure.

### Elaborazione parallela delle sezioni
Impostare **concurrency** nella definizione JSON dell'attività su un valore maggiore di 1, in modo che vengano elaborate più sezioni in parallelo da più istanze dell'attività in fase di esecuzione. Questa funzionalità è particolarmente utile durante l'elaborazione di sezioni che vengono specificate a posteriori.


## Creazione e gestione di una pipeline
Data factory di Azure fornisce vari meccanismi per creare e distribuire le pipeline (che a loro volta contengono all’interno una o più attività).

### Uso del portale di Azure

1. Accedere al [portale di Azure](https://portal.azure.com/).
2. Passare all'istanza di Data factory di Azure in cui si desidera creare una pipeline
3. Fare clic sul riquadro **Crea e distribuisci**nella sezione **Riepilogo**.
 
	![Riquadro Creare e distribuire](./media/data-factory-create-pipelines/author-deploy-tile.png)

4. Fare clic su **Nuova pipeline** sulla barra dei comandi.

	![Pulsante Nuova pipeline](./media/data-factory-create-pipelines/new-pipeline-button.png)

5. Si noterà la finestra dell'editor con il modello JSON della pipeline.

	![Editor pipeline](./media/data-factory-create-pipelines/pipeline-in-editor.png)

6. Dopo avere completato la creazione della pipeline, fare clic su **Distribuisci** sulla barra dei comandi per distribuire la pipeline.

	> [AZURE.NOTE] Durante la distribuzione, il servizio Azure Data Factory esegue alcuni controlli di convalida per risolvere alcuni problemi comuni. Se si verifica un errore, vengono visualizzate le informazioni corrispondenti. Intraprendere azioni correttive e quindi distribuire nuovamente la pipeline creata. È possibile utilizzare l'editor per aggiornare ed eliminare una pipeline.

Per una procedura dettagliata per la creazione di una data factory con una pipeline, vedere [Introduzione ad Azure Data Factory (editor di Data Factory)](data-factory-build-your-first-pipeline-using-editor.md).

### Utilizzo del plug-in Visual Studio
È possibile utilizzare Visual Studio per creare e distribuire pipeline a Data factory di Azure. Per una procedura dettagliata per la creazione di una data factory con una pipeline, vedere [Introduzione ad Azure Data Factory (Visual Studio)](data-factory-build-your-first-pipeline-using-vs.md).


### Uso di Azure PowerShell
È possibile utilizzare Azure PowerShell per creare pipeline nella Data factory di Azure. Ad esempio, è stata definita la pipeline di JSON in un file nella posizione c:\\DPWikisample.json. È possibile caricarla nell'istanza Data factory di Azure, come illustrato nell'esempio seguente.

	New-AzureRmDataFactoryPipeline -ResourceGroupName ADF -Name DPWikisample -DataFactoryName wikiADF -File c:\DPWikisample.json

Per una procedura dettagliata per la creazione di una data factory con una pipeline, vedere [Introduzione ad Azure Data Factory (Azure PowerShell)](data-factory-build-your-first-pipeline-using-powershell.md).

### Uso di .NET SDK
È possibile creare e distribuire la pipeline anche tramite .NET SDK. Questo meccanismo può essere usato per creare pipeline a livello di programmazione. Per altre informazioni, vedere [Creare, gestire e monitorare istanze di Azure Data Factory a livello di codice](data-factory-create-data-factories-programmatically.md).


### Uso del modello di Azure Resource Manager
È possibile creare e distribuire pipeline usando un modello di Azure Resource Manager. Per altre informazioni, vedere [Introduzione ad Azure Data Factory (Azure Resource Manager)](data-factory-build-your-first-pipeline-using-arm.md).

### Uso dell'API REST
È possibile creare e distribuire la pipeline anche tramite le API REST. Questo meccanismo può essere usato per creare pipeline a livello di programmazione. Per altre informazioni, vedere [Creare o aggiornare una pipeline](https://msdn.microsoft.com/library/azure/dn906741.aspx).


## Gestione e monitoraggio  
Dopo avere distribuito una pipeline, è possibile gestire e monitorare le pipeline, le sezioni e le esecuzioni. Per ulteriori informazioni, vedere la sezione: [Monitoraggio e gestione delle pipeline](data-factory-monitor-manage-pipelines.md).

## Passaggi successivi

- Informazioni sulla [pianificazione e l'esecuzione in Data factory di Azure](data-factory-scheduling-and-execution.md).
- Per altre informazioni sulle funzionalità di [spostamento dei dati](data-factory-data-movement-activities.md) e [trasformazione dei dati](data-factory-data-transformation-activities.md), vedere Data factory di Azure
- Informazioni sulla [gestione e il monitoraggio in Data factory di Azure](data-factory-monitor-manage-pipelines.md).
- [Creare e distribuire la prima pipeline](data-factory-build-your-first-pipeline.md).

<!---HONumber=AcomDC_0914_2016-->