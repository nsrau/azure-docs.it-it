---
title: Azure Data Factory - Informazioni di riferimento sugli script JSON | Documentazione Microsoft
description: "Questo articolo fornisce gli schemi JSON per le entità di Data Factory."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2017
ms.author: spelluru
ms.translationtype: Human Translation
ms.sourcegitcommit: 8f987d079b8658d591994ce678f4a09239270181
ms.openlocfilehash: 96c46b2c01272abfaf1dd2667a45e3818cbe49a0
ms.contentlocale: it-it
ms.lasthandoff: 05/18/2017


---
# <a name="azure-data-factory---json-scripting-reference"></a>Azure Data Factory - Informazioni di riferimento sugli script JSON
Questo articolo fornisce gli schemi JSON ed esempi per la definizione di entità di Azure Data Factory (pipeline, attività, set di dati e servizi collegati).  

## <a name="pipeline"></a>Pipeline 
La struttura di livello superiore per una definizione di pipeline è la seguente: 

```json
{
  "name": "SamplePipeline",
  "properties": {
    "description": "Describe what pipeline does",
    "activities": [
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
} 
```

Nella tabella seguente vengono descritte le proprietà all'interno della definizione JSON della pipeline:

| Proprietà | Descrizione | Obbligatorio
-------- | ----------- | --------
| name | Nome della pipeline. Specificare un nome che rappresenta l'azione che l'attività o la pipeline è configurata per eseguire<br/><ul><li>Numero massimo di caratteri: 260</li><li>Deve iniziare con una lettera, un numero o un carattere di sottolineatura (_)</li><li>Non sono ammessi i caratteri seguenti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li></ul> |Sì |
| Descrizione |Testo descrittivo per lo scopo dell'attività o della pipeline | No |
| attività | Contiene un elenco di attività. | Sì |
| start |Data e ora di inizio per la pipeline. Devono essere nel [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), ad esempio: 2014-10-14T16:32:41. <br/><br/>È possibile specificare un'ora locale, ad esempio in base al fuso orario EST. Di seguito è fornito l'esempio `2016-02-27T06:00:00**-05:00`, che indica le 6 EST.<br/><br/>Le proprietà start ed end insieme specificano il periodo attivo per la pipeline. Le sezioni di output vengono generate solo in questo periodo attivo. |No<br/><br/>Se si specifica un valore per la proprietà di fine, è necessario specificare un valore anche per la proprietà di avvio.<br/><br/>L'ora di inizio e l'ora di fine possono essere entrambe vuote per creare una pipeline. È necessario specificare entrambi i valori per impostare un periodo attivo per l'esecuzione della pipeline. Se non si specificano le ore di inizio e fine durante la creazione di una pipeline, è possibile impostare tali valori in un secondo momento usando il cmdlet Set-AzureRmDataFactoryPipelineActivePeriod. |
| end |Data e ora di fine per la pipeline. Se specificate, devono essere in formato ISO. Ad esempio: 2014-10-14T17:32:41 <br/><br/>È possibile specificare un'ora locale, ad esempio in base al fuso orario EST. Di seguito è fornito l'esempio `2016-02-27T06:00:00**-05:00`, che indica le 6 EST.<br/><br/>Per eseguire la pipeline illimitatamente, specificare 9999-09-09 come valore per la proprietà end. |No <br/><br/>Se si specifica un valore per la proprietà di avvio, è necessario specificare un valore anche per la proprietà di fine.<br/><br/>Vedere le note della proprietà **start** . |
| isPaused |Se impostato su true, la pipeline non viene eseguita. Valore predefinito = false. È possibile usare questa proprietà per abilitare o disabilitare. |No |
| pipelineMode |Metodo di pianificazione delle esecuzioni per la pipeline. I valori consentiti sono scheduled (predefinito) e onetime.<br/><br/>"Scheduled" indica che la pipeline viene eseguita a intervalli di tempo specificati in base al periodo di attività, ovvero all'ora di inizio e di fine. "Onetime" indica che la pipeline viene eseguita una sola volta. Al momento, dopo aver creato una pipeline monouso non è possibile modificarla o aggiornarla. Per informazioni dettagliate sull'impostazione onetime, vedere la sezione [Pipeline monouso](data-factory-create-pipelines.md#onetime-pipeline) . |No |
| expirationTime |Periodo di tempo dopo la creazione in cui la pipeline è valida e deve rimanere con provisioning eseguito. Se non ha esecuzioni attive, non riuscite o in sospeso, quando viene raggiunta la scadenza, la pipeline viene eliminata automaticamente. |No |


## <a name="activity"></a>Attività 
La struttura di livello superiore per un'attività all'interno di una definizione di pipeline (elemento attività) è la seguente:

```json
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
```

Nella tabella seguente vengono descritte le proprietà all'interno della definizione JSON dell'attività:

| Tag | Descrizione | Obbligatorio |
| --- | --- | --- |
| name |Nome dell'attività. Specificare un nome che rappresenta l'azione che l'attività è configurata per eseguire<br/><ul><li>Numero massimo di caratteri: 260</li><li>Deve iniziare con una lettera, un numero o un carattere di sottolineatura (_)</li><li>Non sono ammessi i caratteri seguenti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li></ul> |Sì |
| Descrizione |Testo descrittivo per lo scopo dell'attività. |Sì |
| type |Specifica il tipo di attività. Per informazioni sui diversi tipi di attività, vedere le sezioni [ARCHIVIAZIONE DEI DATI](#data-stores) e [ATTIVITÀ DI TRASFORMAZIONE DEI DATI](#data-transformation-activities). |Sì |
| inputs |Tabelle di input usate dall'attività<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Sì |
| outputs |Tabelle di output usate dall'attività.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": “outputtable1” } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": “outputtable1” }, { "name": “outputtable2” }  ],` |Sì |
| linkedServiceName |Nome del servizio collegato usato dall'attività. <br/><br/>Per un'attività può essere necessario specificare il servizio collegato che collega all'ambiente di calcolo richiesto. |Sì per attività di HDInsight, attività di Azure Machine Learning e attività delle stored procedure. <br/><br/>No per tutto il resto |
| typeProperties |Le proprietà nella sezione typeProperties dipendono dal tipo di attività. |No |
| policy |Criteri che influiscono sul comportamento di runtime dell'attività. Se vengono omessi, vengono usati i criteri predefiniti. |No |
| scheduler |La proprietà "scheduler" viene usata per definire la pianificazione per l'attività. Le relative proprietà secondarie sono quelle indicate nella sezione [Disponibilità dei set di dati](data-factory-create-datasets.md#dataset-availability). |No |

### <a name="policies"></a>Criteri
I criteri influiscono sul comportamento in fase di esecuzione di un'attività, in particolare quando viene elaborata la sezione di una tabella. La tabella seguente fornisce informazioni dettagliate.

| Proprietà | Valori consentiti | Valore predefinito | Descrizione |
| --- | --- | --- | --- |
| Concorrenza |Integer  <br/><br/>Valore massimo: 10 |1 |Numero di esecuzioni simultanee dell'attività.<br/><br/>Determina il numero di esecuzioni di attività parallele che possono verificarsi in sezioni diverse. Ad esempio, se un'attività deve passare attraverso grandi set di dati disponibili, con un valore di concorrenza maggiore che consente di velocizzare l'elaborazione dei dati. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Determina l'ordine delle sezioni di dati che vengono elaborate.<br/><br/>Ad esempio nel caso in cui si abbiano 2 sezioni, una alle 16.00 e l'altra alle 17.00, ed entrambe siano in attesa di esecuzione. Se si imposta executionPriorityOrder su NewestFirst, viene elaborata per prima la sezione delle 17:00. Allo stesso modo, se si imposta executionPriorityORder su OldestFIrst, verrà elaborata per prima la sezione delle 16:00. |
| retry |Integer <br/><br/>Valore massimo: 10 |0 |Numero di tentativi prima che l'elaborazione dei dati per la sezione sia contrassegnata come errore. L'esecuzione dell’attività per una sezione di dati viene ritentata fino al numero di tentativi specificato. Il tentativo viene eseguito appena possibile dopo l'errore. |
| timeout |TimeSpan |00:00:00 |Timeout per l'attività. Esempio: 00:10:00 (implica un timeout di 10 minuti)<br/><br/>Se un valore non è specificato o è 0, il timeout è infinito.<br/><br/>Se il tempo di elaborazione dei dati in una sezione supera il valore di timeout, viene annullato e il sistema prova a ripetere l'elaborazione. Il numero di tentativi dipende dalla proprietà retry. Quando si verifica il timeout, lo stato viene impostato su TimedOut. |
| delay |TimeSpan |00:00:00 |Specificare il ritardo prima che abbia inizio l'elaborazione dei dati della sezione.<br/><br/>L'esecuzione dell'attività per una sezione di dati viene avviata non appena il ritardo supera il tempo di esecuzione previsto.<br/><br/>Esempio: 00:10:00 (implica un ritardo di 10 minuti) |
| longRetry |Integer <br/><br/>Valore massimo: 10 |1 |Numero di tentativi estesi prima che l'esecuzione della sezione dia esito negativo.<br/><br/>I tentativi longRetry sono distanziati da longRetryInterval. Pertanto, se è necessario specificare un tempo tra i tentativi, utilizzare longRetry. Se si specificano sia Retry che longRetry, ogni tentativo longRetry include tentativi Retry e il numero massimo di tentativi corrisponde a Retry * longRetry.<br/><br/>Ad esempio, se si hanno le seguenti impostazioni nel criterio attività:<br/>Retry: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>si presume che la sezione da eseguire sia solo una, con stato Waiting, e che l'esecuzione dell'attività abbia ogni volta esito negativo. All’inizio vi saranno tre tentativi di esecuzione consecutivi. Dopo ogni tentativo, lo stato della sezione sarà Retry. Una volta terminati i 3 tentativi sulla sezione, lo stato sarà LongRetry.<br/><br/>Dopo un'ora (vale a dire il valore di longRetryInteval), verrà eseguita un'altra serie di 3 tentativi di esecuzione consecutivi. Al termine, lo stato della sezione sarà Failed e non verranno eseguiti altri tentativi. Quindi, sono stati eseguiti 6 tentativi.<br/><br/>Se un'esecuzione ha esito positivo, lo stato della sezione sarà Ready e non saranno ripetuti altri tentativi.<br/><br/>longRetry può essere usato nelle situazioni in cui i dati dipendenti arrivano in orari non deterministici o l'ambiente complessivo in cui si verifica l'elaborazione dei dati è debole. In tali casi, l'esecuzione di tentativi consecutivi potrebbe non essere utile, mentre l'applicazione di un intervallo consente di ottenere il risultato voluto.<br/><br/>Attenzione: non impostare valori elevati per longRetry o longRetryInterval. In genere, valori più elevati comportano altri problemi sistemici. |
| longRetryInterval |TimeSpan |00:00:00 |Il ritardo tra tentativi longRetry |

### <a name="typeproperties-section"></a>sezione typeProperties
La sezione typeProperties è diversa per ogni tipo di attività. Le attività di trasformazione dispongono delle sole proprietà del tipo. Vedere la sezione [Attività di trasformazione dei dati](#data-transformation-activities) in questo articolo per esempi JSON che definiscono le attività di trasformazione in una pipeline. 

L'**attività di copia** ha due sottosezioni nella sezione typeProperties: **origine** e **sink**. Vedere la sezione [Archivi dati](#data-stores) in questo articolo per esempi JSON che mostrano come usare un archivio dati come origine e/o sink. 

### <a name="sample-copy-pipeline"></a>Esempio di una pipeline di copia
In questa pipeline di esempio è presente un'attività di tipo **Copy** in the **attività** . In questo esempio, [Copia attività](data-factory-data-movement-activities.md) consente di copiare i dati da un archivio BLOB di Azure a un database SQL di Azure. 

```json
{
  "name": "CopyPipeline",
  "properties": {
    "description": "Copy data from a blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputDataset"
          }
        ],
        "outputs": [
          {
            "name": "OutputDataset"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink",
            "writeBatchSize": 10000,
            "writeBatchTimeout": "60:00:00"
          }
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00",
    "end": "2016-07-13T00:00:00"
  }
} 
```

Tenere presente quanto segue:

* Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **Copy**.
* L'input per l'attività è impostato su **InputDataset** e l'output è impostato su **OutputDataset**.
* Nella sezione **typeProperties** vengono specificati **BlobSource** come tipo di origine e **SqlSink** come tipo di sink.

Vedere la sezione [Archivi dati](#data-stores) in questo articolo per esempi JSON che mostrano come usare un archivio dati come origine e/o sink.    

Per la procedura completa di creazione di questa pipeline, vedere [Esercitazione: copiare i dati dall'archivio BLOB al database SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

### <a name="sample-transformation-pipeline"></a>Esempio di una pipeline di trasformazione
In questa pipeline di esempio è presente un'attività di tipo **HDInsightHive** in the **attività** . In questo esempio, l' [attività Hive di HDInsight](data-factory-hive-activity.md) trasforma i dati da un archivio BLOB di Azure tramite l'esecuzione di un file di script Hive in un cluster Hadoop di HDInsight. 

```json
{
    "name": "TransformPipeline",
    "properties": {
        "description": "My first Azure Data Factory pipeline",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "AzureStorageLinkedService",
                    "defines": {
                        "inputtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/inputdata",
                        "partitionedtable": "wasb://adfgetstarted@<storageaccountname>.blob.core.windows.net/partitioneddata"
                    }
                },
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
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Month",
                    "interval": 1
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00",
        "isPaused": false
    }
}
```

Tenere presente quanto segue: 

* Nella sezione attività esiste una sola attività con l'oggetto **type** impostato su **HDInsightHive**.
* Il file di script Hive, **partitionweblogs.hql**, è archiviato nell'account di archiviazione di Azure (specificato da scriptLinkedService, denominato **AzureStorageLinkedService**) e nella cartella **script** nel contenitore **adfgetstarted**.
* La sezione **defines** viene usata per specificare le impostazioni di runtime che vengono passate allo script Hive come valori di configurazione Hive, ad esempio `${hiveconf:inputtable}` e `${hiveconf:partitionedtable}`.

Vedere la sezione [Attività di trasformazione dei dati](#data-transformation-activities) in questo articolo per esempi JSON che definiscono le attività di trasformazione in una pipeline.

Per la procedura completa della creazione di questa pipeline, vedere [Esercitazione: creare la prima pipeline per elaborare i dati usando il cluster Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="linked-service"></a>Servizio collegato
La struttura di livello superiore per una definizione di servizio collegato è la seguente:

```json
{
    "name": "<name of the linked service>",
    "properties": {
        "type": "<type of the linked service>",
        "typeProperties": {
        }
    }
}
```

Nella tabella seguente vengono descritte le proprietà all'interno della definizione JSON dell'attività:

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- | 
| name | Nome del servizio collegato. | Sì | 
| proprietà - tipo | Tipo di servizio collegato. Ad esempio: archiviazione di Azure, database SQL di Azure. |
| typeProperties | La sezione typeProperties contiene elementi che sono diversi per ogni archivio dati o ambiente di calcolo. Vedere la sezione [Archivi dati](#datastores) per tutti i servizi collegati agli archivi dati e la sezione [Ambienti di calcolo](#compute-environments) per tutti i servizi collegati agli ambienti di calcolo. |   

## <a name="dataset"></a>Set di dati 
Un set di dati in Azure Data Factory viene definito come segue:

```json
{
    "name": "<name of dataset>",
    "properties": {
        "type": "<type of dataset: AzureBlob, AzureSql etc...>",
        "external": <boolean flag to indicate external data. only for input datasets>,
        "linkedServiceName": "<Name of the linked service that refers to a data store.>",
        "structure": [
            {
                "name": "<Name of the column>",
                "type": "<Name of the type>"
            }
        ],
        "typeProperties": {
            "<type specific property>": "<value>",
            "<type specific property 2>": "<value 2>",
        },
        "availability": {
            "frequency": "<Specifies the time unit for data slice production. Supported frequency: Minute, Hour, Day, Week, Month>",
            "interval": "<Specifies the interval within the defined frequency. For example, frequency set to 'Hour' and interval set to 1 indicates that new data slices should be produced hourly>"
        },
       "policy":
        {      
        }
    }
}
```

La tabella seguente descrive le proprietà nel codice JSON precedente:   

| Proprietà | Descrizione | Obbligatorio | Default |
| --- | --- | --- | --- |
| name | Nome del set di dati. Per le regole di denominazione, vedere [Azure Data Factory: regole di denominazione](data-factory-naming-rules.md) . |Sì |ND |
| type | Tipo del set di dati. Specificare uno dei tipi supportati da Azure Data Factory, ad esempio AzureBlob, AzureSqlTable. Vedere la sezione [Archivi dati](#data-stores) per tutti gli archivi dati e i tipi di set di dati supportati da Data Factory. | 
| structure | Schema del set di dati. Contiene le colonne, i tipi e così via. | No |ND |
| typeProperties | Proprietà che corrispondono al tipo selezionato. Vedere la sezione [Archivi dati](#data-stores) per i tipi supportati e le rispettive proprietà. |Sì |ND |
| external | Flag booleano per specificare se un set di dati è generato o meno in modo esplicito da una pipeline della data factory. |No |false |
| disponibilità | Definisce la finestra di elaborazione o il modello di sezionamento per la produzione di set di dati Per informazioni dettagliate sul modello di sezionamento dei set di dati, vedere l'articolo [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md). |Sì |ND |
| policy |Definisce i criteri o la condizione che devono soddisfare i sezionamenti di set di dati. <br/><br/>Per informazioni dettagliate, vedere la sezione [Criteri di set di dati](#Policy) . |No |ND |

Ogni colonna della sezione **struttura** contiene le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| name |Nome della colonna. |Sì |
| type |Tipo di dati della colonna.  |No |
| culture |Impostazioni cultura basate su .NET da usare quando il tipo è specificato ed un tipo .NET `Datetime` o `Datetimeoffset`. Il valore predefinito è `en-us`. |No |
| format |Stringa di formato da usare quando il tipo è specificato ed è un tipo .NET `Datetime` o `Datetimeoffset`. |No |

Nell'esempio seguente il set di dati ha tre colonne, ovvero `slicetimestamp`, `projectname` e `pageviews`, che sono rispettivamente di tipo String, String e Decimal.

```json
structure:  
[
    { "name": "slicetimestamp", "type": "String"},
    { "name": "projectname", "type": "String"},
    { "name": "pageviews", "type": "Decimal"}
]
```

La tabella seguente descrive le proprietà che è possibile usare nella sezione **availability**:

| Proprietà | Descrizione | Obbligatorio | Default |
| --- | --- | --- | --- |
| frequency |Specifica l'unità di tempo per la produzione di sezioni di set di dati.<br/><br/><b>Frequenza supportata</b>: minuto, ora, giorno, settimana, mese |Sì |ND |
| interval |Specifica un moltiplicatore per la frequenza.<br/><br/>"Intervallo di frequenza x" determina la frequenza con cui viene generata la sezione.<br/><br/>Se è necessario suddividere il set di dati su base oraria, impostare l'opzione <b>Frequenza</b> su <b>Ora</b> e <b>Intervallo</b> su <b>1</b>.<br/><br/><b>Nota</b> : se si specifica frequency come Minute, è consigliabile impostare interval su un valore non inferiore a 15 |Sì |ND |
| style |Specifica se la sezione deve essere generata all'inizio o alla fine dell'intervallo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se frequency è impostato su Month e style è impostato su EndOfInterval, la sezione viene generata l'ultimo giorno del mese. Se style è impostato su StartOfInterval, la sezione viene generata il primo giorno del mese.<br/><br/>Se l'opzione Frequnza è impostata su Mese e l'opzione Stile è impostata su EndOfInterval, la sezione viene generata l'ultima ora del giorno.<br/><br/>Se frequency è impostato su Hour e style è impostato su EndOfInterval, la sezione viene generata alla fine dell'ora. Ad esempio, una sezione per il periodo 13.00 - 14.00 viene generata alle 14.00. |No |EndOfInterval |
| anchorDateTime |Definisce la posizione assoluta nel tempo usata dall'utilità di pianificazione per calcolare i limiti della sezione del set di dati. <br/><br/><b>Nota:</b> se AnchorDateTime include parti della data più granulari rispetto alla frequenza, le parti più granulari vengono ignorate. <br/><br/>Ad esempio, se l'<b>intervallo</b> è <b>orario</b> (ovvero frequenza: ora e intervallo: 1) e <b>AnchorDateTime</b> contiene <b>minuti e secondi</b>, le parti <b>minuti e secondi</b> di AnchorDateTime vengono ignorate. |No |01/01/0001 |
| offset |Intervallo di tempo in base al quale l'inizio e la fine di tutte le sezioni dei set di dati vengono spostate. <br/><br/><b>Nota:</b> se vengono specificati sia anchorDateTime che offset, il risultato sarà lo spostamento combinato. |No |ND |

La sezione availability seguente specifica che il set di dati di output viene generato ogni ora oppure che il set di dati di input è disponibile ogni ora:

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 1    
}
```

La sezione **policy** nella definizione del set di dati stabilisce i criteri o la condizione che le sezioni del set di dati devono soddisfare.

| Nome criterio | Descrizione | Applicato a | Obbligatorio | Default |
| --- | --- | --- | --- | --- |
| minimumSizeMB |Verifica che i dati in un **BLOB di Azure** soddisfino i requisiti relativi alle dimensioni minime (in megabyte). |BLOB di Azure |No |ND |
| minimumRows |Verifica che i dati in un **database SQL di Azure** o in una **tabella di Azure** contengano il numero minimo di righe. |<ul><li>Database SQL di Azure</li><li>tabella di Azure</li></ul> |No |ND |

**Esempio:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

A meno che un set di dati non sia generato da Azure Data Factory, deve essere contrassegnato come **external**. Questa impostazione si applica in genere agli input della prima attività in una pipeline, a meno che non si usi il concatenamento di attività o di pipeline.

| name | Descrizione | Obbligatorio | Default Value |
| --- | --- | --- | --- |
| dataDelay |Tempo di attesa per il controllo sulla disponibilità dei dati esterni per il sezionamento specificato. Ad esempio, se i dati sono disponibili ogni ora, il controllo per vedere se i dati esterni sono disponibili e la sezione corrispondente è Ready può essere ritardato usando dataDelay.<br/><br/>Si applica solo all'ora corrente.  Ad esempio, se in questo momento sono le 13:00 e questo valore è di 10 minuti, la convalida inizia alle 13:10.<br/><br/>Questa impostazione non influisce sulle sezioni nel passato; le sezioni con i parametri Ora di fine sezione + dataDelay < Ora vengono elaborate senza alcun ritardo.<br/><br/>Valori superiori a 23:59 ore devono essere specificati nel formato `day.hours:minutes:seconds`. Per specificare 24 ore, ad esempio, non utilizzare 24:00:00; utilizzare invece 1.00:00:00. Il valore 24:00:00 viene considerato 24 giorni (24.00:00:00). Per 1 giorno e 4 ore, specificare 1:04:00:00. |No |0 |
| retryInterval |Il tempo di attesa tra un errore e il successivo tentativo. Se un tentativo non riesce, il tentativo successivo avviene dopo retryInterval. <br/><br/>Se in questo momento sono le 13:00, viene avviato il primo tentativo. Se la durata per completare il primo controllo di convalida è 1 minuto e l'operazione non è riuscita, il tentativo successivo è alle 13:00 + 1 min (durata) + 1 min (intervallo tentativi) = 13:02. <br/><br/>Per le sezioni passate, non si verifica alcun ritardo. La ripetizione avviene immediatamente. |No |00:01:00 (1 minute) |
| retryTimeout |Timeout per ogni nuovo tentativo.<br/><br/>Se questa proprietà è impostata su 10 minuti, la convalida deve essere completata entro 10 minuti. Se sono necessari più di 10 minuti per eseguire la convalida, il tentativo viene sospeso.<br/><br/>Se tutti i tentativi per la convalida scadono, la sezione viene contrassegnata come TimedOut. |No |00:10:00 (10 minutes) |
| maximumRetry |Numero di volte per controllare la disponibilità dei dati esterni. Il valore massimo consentito è 10. |No |3 |


## <a name="data-stores"></a>Archivi dati
La sezione [Servizi collegati](#linked-service) ha fornito le descrizioni degli elementi JSON comuni a tutti i tipi di servizi collegati. Questa sezione fornisce informazioni sugli elementi JSON specifici di ogni archivio dati.

La sezione [Set di dati ](#dataset) ha fornito le descrizioni degli elementi JSON comuni a tutti i tipi di set di dati. Questa sezione fornisce informazioni sugli elementi JSON specifici di ogni archivio dati.

La sezione [Attività](#activity) ha fornito le descrizioni degli elementi JSON comuni a tutti i tipi di attività. Questa sezione fornisce informazioni sugli elementi JSON specifici di ogni archivio dati quando è usato come origine/sink in un'attività di copia.  

Fare clic sul collegamento all'archivio a cui si è interessati per visualizzare gli schemi JSON per i servizi collegati, il set di dati e l'origine/sink dell'attività di copia.

| Categoria | Archivio dati 
|:--- |:--- |
| **Azure** |[Archivio BLOB di Azure](#azure-blob-storage) |
| &nbsp; |[Archivio Data Lake di Azure](#azure-datalake-store) |
| &nbsp; |[Azure Cosmos DB](#azure-cosmos-db) |
| &nbsp; |[Database SQL di Azure](#azure-sql-database) |
| &nbsp; |[Azure SQL Data Warehouse](#azure-sql-data-warehouse) |
| &nbsp; |[Ricerca di Azure](#azure-search) |
| &nbsp; |[Archivio tabelle di Azure](#azure-table-storage) |
| **Database** |[Amazon Redshift](#amazon-redshift) |
| &nbsp; |[IBM DB2](#ibm-db2) |
| &nbsp; |[MySQL](#mysql) |
| &nbsp; |[Oracle](#oracle) |
| &nbsp; |[PostgreSQL](#postgresql) |
| &nbsp; |[SAP Business Warehouse](#sap-business-warehouse) |
| &nbsp; |[SAP HANA](#sap-hana) |
| &nbsp; |[SQL Server](#sql-server) |
| &nbsp; |[Sybase](#sybase) |
| &nbsp; |[Teradata](#teradata) |
| **NoSQL** |[Cassandra](#cassandra) |
| &nbsp; |[MongoDB](#mongodb) |
| **File** |[Amazon S3](#amazon-s3) |
| &nbsp; |[File system](#file-system) |
| &nbsp; |[FTP](#ftp) |
| &nbsp; |[HDFS](#hdfs) |
| &nbsp; |[SFTP](#sftp) |
| **Altro** |[HTTP](#http) |
| &nbsp; |[OData](#odata) |
| &nbsp; |[ODBC](#odbc) |
| &nbsp; |[Salesforce](#salesforce) |
| &nbsp; |[Tabella Web](#web-table) |

## <a name="azure-blob-storage"></a>Archiviazione BLOB di Azure

### <a name="linked-service"></a>Servizio collegato
Esistono due tipi di servizi collegati: servizio collegato di Archiviazione di Azure e servizio collegato di firma di accesso condiviso Archiviazione di Azure.

#### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure
Per collegare un account di Archiviazione di Azure a una data factory tramite la **chiave dell'account**, creare un servizio collegato di Archiviazione di Azure. Per definire un servizio collegato di Archiviazione di Azure, impostare il **tipo** di servizio collegato su **AzureStorage**. Specificare quindi le seguenti proprietà nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| connectionString |Specificare le informazioni necessarie per connettersi all’archivio Azure per la proprietà connectionString. |Sì |

##### <a name="example"></a>Esempio  

```json
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

#### <a name="azure-storage-sas-linked-service"></a>Servizio collegato di firma di accesso condiviso Archiviazione di Azure
Il servizio collegato di firma di accesso condiviso Archiviazione di Azure consente di collegare un account di Archiviazione di Azure a Data factory di Azure tramite una firma di accesso condiviso. Offre a Data factory un accesso con restrizioni o limiti di tempo a tutte le risorse o a risorse specifiche (BLOB/contenitore) nella risorsa di archiviazione. Per collegare un account di Archiviazione di Azure a una data factory tramite la firma di accesso condiviso, creare un servizio collegato di firma di accesso condiviso Archiviazione di Azure. Per definire un Servizio collegato di firma di accesso condiviso Archiviazione di Azure, impostare il **tipo** del servizio collegato su **AzureStorageSas**. Specificare quindi le seguenti proprietà nella sezione **typeProperties**:   

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| sasUri |Specificare l'URI della firma di accesso condiviso per le risorse di Archiviazione di Azure come BLOB, contenitore o tabella. |Sì |

##### <a name="example"></a>Esempio

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

Per altre informazioni sui servizi collegati, vedere [Connettore Archiviazione BLOB di Azure](data-factory-azure-blob-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati BLOB di Azure, impostare il **tipo** del set di dati su **AzureBlob**. Specificare quindi le proprietà seguenti specifiche del BLOB di Azure nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| folderPath |Percorso del contenitore e della cartella nell'archivio BLOB. Esempio: myblobcontainer\myblobfolder\ |Sì |
| fileName |Nome del BLOB. fileName è facoltativo e non applica la distinzione tra maiuscole e minuscole.<br/><br/>Se si specifica un filename, l'attività, inclusa la copia, funziona sul BLOB specifico.<br/><br/>Quando fileName non è specificato, la copia include tutti i BLOB in folderPath per il set di dati di input.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il nome del file generato avrà il formato seguente: Data.<Guid>.txt, ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy è una proprietà facoltativa. Può essere utilizzata per specificare una proprietà folderPath dinamica e un nome file per i dati della serie temporale. Ad esempio, è possibile includere parametri per ogni ora di dati in folderPath. |No |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format), [JsonFormat](data-factory-supported-file-and-compression-formats.md#json-format), [AvroFormat](data-factory-supported-file-and-compression-formats.md#avro-format), [OrcFormat](data-factory-supported-file-and-compression-formats.md#orc-format) e [ParquetFormat](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. I livelli supportati sono **Ottimale** e **Più veloce**. Per altre informazioni, vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

#### <a name="example"></a>Esempio

```json
{
    "name": "AzureBlobInput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
 ```


Per altre informazioni, vedere [Connettore BLOB di Azure](data-factory-azure-blob-connector.md#dataset-properties).

### <a name="blobsource-in-copy-activity"></a>BlobSource in attività di copia
Se si copiano dati da Archiviazione BLOB di Azure, impostare il **tipo di origine** dell'attività di copia su **BlobSource**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| ricorsiva |Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. |True (valore predefinito), False |No |

#### <a name="example-blobsource"></a>Esempio: BlobSource**
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
### <a name="blobsink-in-copy-activity"></a>BlobSink in attività di copia
Se si copiano dati in Archiviazione BLOB di Azure, impostare il **tipo di sink** dell'attività di copia su **BlobSink**e specificare le proprietà seguenti nella sezione **sink**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| copyBehavior |Definisce il comportamento di copia quando l'origine è BlobSource o FileSystem. |<b>PreserveHierarchy:</b> mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><br/><b>FlattenHierarchy</b>: tutti i file della cartella di origine si trovano nel primo livello della cartella di destinazione. Il nome dei file di destinazione viene generato automaticamente. <br/><br/><b>MergeFiles (valore predefinito)</b>: unisce tutti i file della cartella di origine in un solo file. Se viene specificato il nome file/BLOB, il nome file unito sarà il nome specificato. In caso contrario, sarà il nome file generato automaticamente. |No |

#### <a name="example-blobsink"></a>Esempio: BlobSink

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Per altre informazioni, vedere [Connettore BLOB di Azure](data-factory-azure-blob-connector.md#copy-activity-properties). 

## <a name="azure-data-lake-store"></a>Archivio Azure Data Lake

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Azure Data Lake Store, impostare il tipo di servizio collegato su **AzureDataLakeStore** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type | La proprietà type deve essere impostata su: **AzureDataLakeStore** | Sì |
| dataLakeStoreUri | Specificare le informazioni sull'account Archivio Azure Data Lake. Ha il formato seguente: `https://[accountname].azuredatalakestore.net/webhdfs/v1` o `adl://[accountname].azuredatalakestore.net/`. | Sì |
| subscriptionId | ID sottoscrizione di Azure a cui il Data Lake Store appartiene. | Richiesto per il sink |
| resourceGroupName | Nome del gruppo di risorse di Azure a cui il Data Lake Store appartiene. | Richiesto per il sink |
| servicePrincipalId | Specificare l'ID client dell'applicazione. | Sì (per l'autenticazione di un'entità servizio) |
| servicePrincipalKey | Specificare la chiave dell'applicazione. | Sì (per l'autenticazione di un'entità servizio) |
| tenant | Specificare le informazioni sul tenant (nome di dominio o ID tenant) in cui si trova l'applicazione. È possibile recuperarlo passando il cursore del mouse sull'angolo superiore destro del portale di Azure. | Sì (per l'autenticazione di un'entità servizio) |
| autorizzazione | Fare clic sul pulsante **Autorizza** nell'**editor di Data Factory** e immettere le credenziali per assegnare l'URL di autorizzazione generato automaticamente a questa proprietà. | Sì (per l'autenticazione basata su credenziali utente)|
| sessionId | ID sessione OAuth dalla sessione di autorizzazione oauth. Ogni ID di sessione è univoco e può essere usato solo una volta. Questa impostazione viene generata automaticamente quando si usa l'editor di Data Factory. | Sì (per l'autenticazione basata su credenziali utente) |

#### <a name="example-using-service-principal-authentication"></a>Esempio: uso dell'autenticazione basata su entità servizio
```json
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "typeProperties": {
            "dataLakeStoreUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info. Example: microsoft.onmicrosoft.com>"
        }
    }
}
```

#### <a name="example-using-user-credential-authentication"></a>Esempio: uso dell'autenticazione basata su credenziali utente
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

Per altre informazioni, vedere [Connettore Azure Data Lake Store](data-factory-azure-datalake-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati Azure Data Lake Store, impostare il **tipo** del set di dati **AzureDataLakeStore**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| folderPath |Percorso del contenitore e della cartella nell'Archivio Azure Data Lake. |Sì |
| fileName |Il nome del file in fileName nell'archivio di Azure Data Lake è facoltativo e distingue tra maiuscole e minuscole. fileName è facoltativo e non applica la distinzione tra maiuscole e minuscole. <br/><br/>Se si specifica un filename, l'attività, inclusa la copia, funziona sul file specifico.<br/><br/>Quando fileName non è specificato, la copia include tutti i file in folderPath per il set di dati di input.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il nome del file generato avrà il formato seguente: Data<Guid>.txt, ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy è una proprietà facoltativa. Può essere utilizzata per specificare una proprietà folderPath dinamica e un nome file per i dati della serie temporale. Ad esempio, è possibile includere parametri per ogni ora di dati in folderPath. |No |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format), [JsonFormat](data-factory-supported-file-and-compression-formats.md#json-format), [AvroFormat](data-factory-supported-file-and-compression-formats.md#avro-format), [OrcFormat](data-factory-supported-file-and-compression-formats.md#orc-format) e [ParquetFormat](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. I livelli supportati sono **Ottimale** e **Più veloce**. Per altre informazioni, vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

#### <a name="example"></a>Esempio
```json
{
    "name": "AzureDataLakeStoreInput",
    "properties": {
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

Per altre informazioni, vedere [Connettore Azure Data Lake Store](data-factory-azure-datalake-connector.md#dataset-properties). 

### <a name="azure-data-lake-store-source-in-copy-activity"></a>Origine Azure Data Lake Store in attività di copia
Se si copiano dati da Azure Data Lake Store, impostare il **tipo di origine** dell'attività di copia su **AzureDataLakeStoreSource**e specificare le proprietà seguenti nella sezione **source**:

**AzureDataLakeStoreSource** supporta le proprietà seguenti della sezione **typeProperties**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| ricorsiva |Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. |True (valore predefinito), False |No |

#### <a name="example-azuredatalakestoresource"></a>Esempio: AzureDataLakeStoreSource

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureDakeLaketoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureDataLakeStoreInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureDataLakeStoreSource"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```

Per altre informazioni, vedere [Connettore Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties).

### <a name="azure-data-lake-store-sink-in-copy-activity"></a>Sink Azure Data Lake Store in attività di copia
Se si copiano dati in Azure Data Lake Store, impostare il **tipo di sink**  dell'attività di copia su **AzureDataLakeStoreSink**e specificare le proprietà seguenti nella sezione **sink**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| copyBehavior |Specifica il comportamento di copia. |<b>PreserveHierarchy:</b> mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><br/><b>FlattenHierarchy</b>: tutti i file della cartella di origine vengono creati nel primo livello della cartella di destinazione. Il nome dei file di destinazione viene generato automaticamente.<br/><br/><b>MergeFiles</b>: unisce tutti i file della cartella di origine in un solo file. Se viene specificato il nome file/BLOB, il nome file unito sarà il nome specificato. In caso contrario, sarà il nome file generato automaticamente. |No |

#### <a name="example-azuredatalakestoresink"></a>Esempio: AzureDataLakeStoreSink
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoDataLake",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureDataLakeStoreOutput"
            }],
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
        }]
    }
}
```

Per altre informazioni, vedere [Connettore Azure Data Lake Store](data-factory-azure-datalake-connector.md#copy-activity-properties). 

## <a name="azure-cosmos-db"></a>Azure Cosmos DB  

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Azure Cosmos DB, impostare il **tipo** di servizio collegato su **DocumentDb** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| **Proprietà** | **Descrizione** | **Obbligatorio** |
| --- | --- | --- |
| connectionString |Specificare le informazioni necessarie per connettersi al database di Azure Cosmos DB. |Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "CosmosDBLinkedService",
    "properties": {
        "type": "DocumentDb",
        "typeProperties": {
            "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
    }
}
```
Per altre informazioni, vedere l'articolo [Connettore Azure Cosmos DB](data-factory-azure-documentdb-connector.md#linked-service-properties).

### <a name="dataset"></a>Set di dati
Per definire un set di dati Azure Cosmos DB, impostare il **tipo** di set di dati su **DocumentDbCollection**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| **Proprietà** | **Descrizione** | **Obbligatorio** |
| --- | --- | --- |
| collectionName |Nome della raccolta di Azure Cosmos DB. |Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "PersonCosmosDBTable",
    "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "CosmosDBLinkedService",
        "typeProperties": {
            "collectionName": "Person"
        },
        "external": true,
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```
Per altre informazioni, vedere l'articolo [Connettore Azure Cosmos DB](data-factory-azure-documentdb-connector.md#dataset-properties).

### <a name="azure-cosmos-db-collection-source-in-copy-activity"></a>Origine della raccolta Azure Cosmos DB in attività di copia
Se si copiano dati da un archivio Azure Cosmos DB, impostare il **tipo di origine** dell'attività di copia su **DocumentDbCollectionSource** e specificare le proprietà seguenti nella sezione **source**:


| **Proprietà** | **Descrizione** | **Valori consentiti** | **Obbligatorio** |
| --- | --- | --- | --- |
| query |Specificare la query per leggere i dati. |Stringa di query supportata da Azure Cosmos DB. <br/><br/>Esempio: `SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` |No <br/><br/>Se non specificato, l'istruzione SQL eseguita: `select <columns defined in structure> from mycollection` |
| nestingSeparator |Carattere speciale per indicare che il documento è nidificato |Qualsiasi carattere. <br/><br/>Azure Cosmos DB è un archivio NoSQL per i documenti JSON, dove sono consentite strutture nidificate. Azure Data Factory consente di indicare una gerarchia tramite nestingSeparator, ovvero "." negli esempi precedenti. Con il separatore, l'attività copia genererà l'oggetto "Name" con tre elementi figlio First, Middle e Last, in base a "Name.First", "Name.Middle" e "Name.Last" nella definizione della tabella. |No |

#### <a name="example"></a>Esempio

```json
{
    "name": "DocDbToBlobPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "DocumentDbCollectionSource",
                    "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                    "nestingSeparator": "."
                },
                "sink": {
                    "type": "BlobSink",
                    "blobWriterAddHeader": true,
                    "writeBatchSize": 1000,
                    "writeBatchTimeout": "00:00:59"
                }
            },
            "inputs": [{
                "name": "PersonCosmosDBTable"
            }],
            "outputs": [{
                "name": "PersonBlobTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromCosmosDbToBlob"
        }],
        "start": "2016-04-01T00:00:00",
        "end": "2016-04-02T00:00:00"
    }
}
```

### <a name="azure-cosmos-db-collection-sink-in-copy-activity"></a>Sink della raccolta Azure Cosmos DB in attività di copia
Se si copiano dati in Azure Cosmos DB, impostare il **tipo di sink** dell'attività di copia su **DocumentDbCollectionSink** e specificare le proprietà seguenti nella sezione **sink**:

| **Proprietà** | **Descrizione** | **Valori consentiti** | **Obbligatorio** |
| --- | --- | --- | --- |
| nestingSeparator |È necessario un carattere speciale nel nome della colonna di origine per indicare tale documento nidificato. <br/><br/>Per l'esempio sopra: `Name.First` nella tabella di output produce la struttura JSON seguente nel documento di Cosmos DB:<br/><br/>"Name": {<br/>    "First": "John"<br/>}, |Carattere utilizzato per separare i livelli di nidificazione.<br/><br/>Il valore predefinito è `.` (punto). |Carattere utilizzato per separare i livelli di nidificazione. <br/><br/>Il valore predefinito è `.` (punto). |
| writeBatchSize |Numero di richieste in parallelo per il servizio Azure Cosmos DB per creare documenti.<br/><br/>È possibile ottimizzare le prestazioni quando si copiano dati da e verso Azure Cosmos DB usando questa proprietà. È possibile prevedere prestazioni migliori quando si aumenta writeBatchSize, poiché vengono inviate più richieste in parallelo a Azure Cosmos DB. Tuttavia è necessario evitare la limitazione che può generare il messaggio di errore: "La frequenza delle richieste è troppo elevata".<br/><br/>La limitazione è dovuta a diversi fattori, inclusi la dimensione dei documenti, il numero di termini nei documenti, i criteri di indicizzazione della raccolta di destinazione, ecc. Per le operazioni di copia, è possibile usare una raccolta migliore (ad esempio, S3) per poter disporre della maggiore velocità effettiva possibile (2.500 unità richieste al secondo). |Integer |No (valore predefinito: 5) |
| writeBatchTimeout |Tempo di attesa per il completamento dell’operazione prima del timeout. |Intervallo di tempo<br/><br/> Ad esempio: "00:30:00" (30 minuti). |No |

#### <a name="example"></a>Esempio

```json
{
    "name": "BlobToDocDbPipeline",
    "properties": {
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "DocumentDbCollectionSink",
                    "nestingSeparator": ".",
                    "writeBatchSize": 2,
                    "writeBatchTimeout": "00:00:00"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix"
                }
            },
            "inputs": [{
                "name": "PersonBlobTableIn"
            }],
            "outputs": [{
                "name": "PersonCosmosDbTableOut"
            }],
            "policy": {
                "concurrency": 1
            },
            "name": "CopyFromBlobToCosmosDb"
        }],
        "start": "2016-04-14T00:00:00",
        "end": "2016-04-15T00:00:00"
    }
}
```

Per altre informazioni, vedere l'articolo [Connettore Azure Cosmos DB](data-factory-azure-documentdb-connector.md#copy-activity-properties).

## <a name="azure-sql-database"></a>Database SQL di Azure

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Database SQL di Azure, impostare il **tipo** di servizio collegato su **AzureSqlDatabase**e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| connectionString |Specificare le informazioni necessarie per connettersi all'istanza di database SQL di Azure per la proprietà connectionString. |Sì |

#### <a name="example"></a>Esempio
```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Per altre informazioni, vedere [Connettore SQL di Azure](data-factory-azure-sql-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati del Database SQL di Azure, impostare il **tipo** di set di dati su **AzureSqlTable**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella o vista nell'istanza di database SQL di Azure a cui fa riferimento il servizio collegato. |Sì |

#### <a name="example"></a>Esempio

```json
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
```
Per altre informazioni, vedere [Connettore SQL di Azure](data-factory-azure-sql-connector.md#dataset-properties). 

### <a name="sql-source-in-copy-activity"></a>Origine SQL nell'attività di copia
Se si copiano dati da un database SQL di Azure, impostare il **tipo di origine** dell'attività di copia su **SqlSource**e specificare le proprietà seguenti nella sezione **source**:


| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| SqlReaderQuery |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Esempio: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Nome della stored procedure che legge i dati dalla tabella di origine. |Nome della stored procedure. |No |
| storedProcedureParameters |Parametri per la stored procedure. |Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No |

#### <a name="example"></a>Esempio

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
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
        }]
    }
}
```
Per altre informazioni, vedere [Connettore SQL di Azure](data-factory-azure-sql-connector.md#copy-activity-properties). 

### <a name="sql-sink-in-copy-activity"></a>Sink SQL nell'attività di copia
Se si copiano dati in un database SQL di Azure, impostare il **tipo di sink** dell'attività di copia su **SqlSink**e specificare le proprietà seguenti nella sezione **sink**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. |Intervallo di tempo<br/><br/> Ad esempio: "00:30:00" (30 minuti). |No |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize. |Numero intero (numero di righe) |No (valore predefinito: 10000) |
| sqlWriterCleanupScript |Specificare una query da eseguire nell'attività di copia per pulire i dati di una sezione specifica. |Istruzione di query. |No |
| sliceIdentifierColumnName |Specificare il nome di una colonna in cui inserire nell'attività di copia l'identificatore di sezione generato automaticamente che verrà usato per pulire i dati di una sezione specifica quando viene ripetuta l'esecuzione. |Nome di colonna di una colonna con tipo di dati binario (32). |No |
| sqlWriterStoredProcedureName |Nome della stored procedure che esegue l'upsert (aggiornamenti/inserimenti) nella tabella di destinazione. |Nome della stored procedure. |No |
| storedProcedureParameters |Parametri per la stored procedure. |Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No |
| sqlWriterTableType |Specificare il nome di un tipo di tabella da usare nella stored procedure. L'attività di copia rende i dati spostati disponibili in una tabella temporanea con questo tipo di tabella. Il codice della stored procedure può quindi unire i dati copiati con i dati esistenti. |Nome del tipo di tabella. |No |

#### <a name="example"></a>Esempio

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
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
        }]
    }
}
```

Per altre informazioni, vedere [Connettore SQL di Azure](data-factory-azure-sql-connector.md#copy-activity-properties). 

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Azure SQL Data Warehouse, impostare il **tipo** di servizio collegato su **AzureSqlDW**e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| connectionString |Specificare le informazioni necessarie per connettersi all'istanza di Azure SQL Data Warehouse per la proprietà connectionString. |Sì |



#### <a name="example"></a>Esempio

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Per altre informazioni, vedere [Connettore Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati di Azure SQL Data Warehouse, impostare il **tipo** di set di dati su **AzureSqlDWTable**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella o della visualizzazione nell'istanza del database SQL Data Warehouse di Azure a cui fa riferimento il servizio collegato. |Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "AzureSqlDWInput",
    "properties": {
    "type": "AzureSqlDWTable",
        "linkedServiceName": "AzureSqlDWLinkedService",
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
```

Per altre informazioni, vedere [Connettore Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#dataset-properties). 

### <a name="sql-dw-source-in-copy-activity"></a>Origine SQL DW nell'attività di copia
Se si copiano dati da un Azure SQL Data Warehouse, impostare il **tipo di origine** dell'attività di copia su **SqlDWSource**e specificare le proprietà seguenti nella sezione **source**:


| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| SqlReaderQuery |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: `select * from MyTable`. |No |
| sqlReaderStoredProcedureName |Nome della stored procedure che legge i dati dalla tabella di origine. |Nome della stored procedure. |No |
| storedProcedureParameters |Parametri per la stored procedure. |Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No |

#### <a name="example"></a>Esempio

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLDWtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSqlDWInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlDWSource",
                    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
        }]
    }
}
```

Per altre informazioni, vedere [Connettore Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties). 

### <a name="sql-dw-sink-in-copy-activity"></a>Sink SQL DW nell'attività di copia
Se si copiano dati in un Azure SQL Data Warehouse, impostare il **tipo di sink** dell'attività di copia su **SqlDWSink**e specificare le proprietà seguenti nella sezione **sink**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| sqlWriterCleanupScript |Specificare una query da eseguire nell'attività di copia per pulire i dati di una sezione specifica. |Istruzione di query. |No |
| allowPolyBase |Indica se usare PolyBase, quando applicabile, invece del meccanismo BULKINSERT. <br/><br/> **L'uso di PolyBase è il modo consigliato per caricare dati in SQL Data Warehouse.** |True  <br/>False (impostazione predefinita) |No |
| polyBaseSettings |Gruppo di proprietà che è possibile specificare quando la proprietà **allowPolybase** è impostata su **true**. |&nbsp; |No |
| rejectValue |Specifica il numero o la percentuale di righe che è possibile rifiutare prima che la query abbia esito negativo. <br/><br/>Per altre informazioni sulle opzioni di rifiuto di PolyBase, vedere la sezione **Arguments** (Argomenti) in [CREATE EXTERNAL TABLE (Transact-SQL)](https://msdn.microsoft.com/library/dn935021.aspx) . |0 (impostazione predefinita), 1, 2, … |No |
| rejectType |Indica se l'opzione rejectValue viene specificata come valore letterale o come percentuale. |Value (impostazione predefinita), Percentage |No |
| rejectSampleValue |Determina il numero di righe da recuperare prima che PolyBase ricalcoli la percentuale di righe rifiutate. |1, 2, … |Sì se **rejectType** è **percentage** |
| useTypeDefault |Specifica come gestire i valori mancanti nei file con testo delimitato quando PolyBase recupera dati dal file di testo.<br/><br/>Per altre informazioni su questa proprietà, vedere la sezione Arguments (Argomenti) in [CREATE EXTERNAL FILE FORMAT (Transact-SQL)](https://msdn.microsoft.com/library/dn935026.aspx). |True, False (valore predefinito) |No |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize. |Numero intero (numero di righe) |No (valore predefinito: 10000) |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. |Intervallo di tempo<br/><br/> Ad esempio: "00:30:00" (30 minuti). |No |

#### <a name="example"></a>Esempio

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQLDW",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureSqlDWOutput"
            }],
            "typeProperties": {
                "source": {
                "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlDWSink",
                    "allowPolyBase": true
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
        }]
    }
}
```

Per altre informazioni, vedere [Connettore Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties). 

## <a name="azure-search"></a>Ricerca di Azure

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Ricerca di Azure, impostare il **tipo** di servizio collegato su **AzureSearch** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| URL | URL del servizio Ricerca di Azure. | sì |
| key | Chiave amministratore del servizio Ricerca di Azure. | Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "AzureSearchLinkedService",
    "properties": {
        "type": "AzureSearch",
        "typeProperties": {
            "url": "https://<service>.search.windows.net",
            "key": "<AdminKey>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore Ricerca di Azure](data-factory-azure-search-connector.md#linked-service-properties).

### <a name="dataset"></a>Set di dati
Per definire un set di dati di Ricerca di Azure, impostare il **tipo** di set di dati su **AzureSearchIndex** e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| type | La proprietà type deve essere impostata su **AzureSearchIndex**.| Sì |
| indexName | Nome dell'indice di Ricerca di Azure. Il servizio Data Factory non crea l'indice. L'indice deve essere presente in Ricerca di Azure. | Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "AzureSearchIndexDataset",
    "properties": {
        "type": "AzureSearchIndex",
        "linkedServiceName": "AzureSearchLinkedService",
        "typeProperties": {
            "indexName": "products"
        },
        "availability": {
            "frequency": "Minute",
            "interval": 15
        }
    }
}
```

Per altre informazioni, vedere [Connettore Ricerca di Azure](data-factory-azure-search-connector.md#dataset-properties).

### <a name="azure-search-index-sink-in-copy-activity"></a>Sink Indice di Ricerca di Azure in attività di copia
Se si copiano dati in un indice di Ricerca di Azure, impostare il **tipo di sink**  dell'attività di copia su **AzureSearchIndexSink**e specificare le proprietà seguenti nella sezione **sink**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| -------- | ----------- | -------------- | -------- |
| WriteBehavior | Specifica se eseguire un'unione o una sostituzione quando nell'indice esiste già un documento. | Merge (impostazione predefinita)<br/>Carica| No |
| WriteBatchSize | Consente di caricare dati nell'indice di Ricerca di Azure quando le dimensioni del buffer raggiungono il valore indicato da writeBatchSize. | Da 1 a 1000. Il valore predefinito è 1000. | No |

#### <a name="example"></a>Esempio

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoAzureSearchIndex",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureSearchIndexDataset"
            }],
            "typeProperties": {
                "source": {
                    "type": "SqlSource",
                    "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "AzureSearchIndexSink"
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
        }]
    }
}
```

Per altre informazioni, vedere [Connettore Ricerca di Azure](data-factory-azure-search-connector.md#copy-activity-properties).

## <a name="azure-table-storage"></a>Archiviazione tabelle di Azure

### <a name="linked-service"></a>Servizio collegato
Esistono due tipi di servizi collegati: servizio collegato di Archiviazione di Azure e servizio collegato di firma di accesso condiviso Archiviazione di Azure.

#### <a name="azure-storage-linked-service"></a>Servizio collegato Archiviazione di Azure
Per collegare un account di Archiviazione di Azure a una data factory tramite la **chiave dell'account**, creare un servizio collegato di Archiviazione di Azure. Per definire un servizio collegato di Archiviazione di Azure, impostare il **tipo** di servizio collegato su **AzureStorage**. Specificare quindi le seguenti proprietà nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su: **AzureStorage** |Sì |
| connectionString |Specificare le informazioni necessarie per connettersi all’archivio Azure per la proprietà connectionString. |Sì |

**Esempio:**  

```json
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

#### <a name="azure-storage-sas-linked-service"></a>Servizio collegato di firma di accesso condiviso Archiviazione di Azure
Il servizio collegato di firma di accesso condiviso Archiviazione di Azure consente di collegare un account di Archiviazione di Azure a Data factory di Azure tramite una firma di accesso condiviso. Offre a Data factory un accesso con restrizioni o limiti di tempo a tutte le risorse o a risorse specifiche (BLOB/contenitore) nella risorsa di archiviazione. Per collegare un account di Archiviazione di Azure a una data factory tramite la firma di accesso condiviso, creare un servizio collegato di firma di accesso condiviso Archiviazione di Azure. Per definire un Servizio collegato di firma di accesso condiviso Archiviazione di Azure, impostare il **tipo** del servizio collegato su **AzureStorageSas**. Specificare quindi le seguenti proprietà nella sezione **typeProperties**:   

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type |La proprietà type deve essere impostata su: **AzureStorageSas** |Sì |
| sasUri |Specificare l'URI della firma di accesso condiviso per le risorse di Archiviazione di Azure come BLOB, contenitore o tabella. |Sì |

**Esempio:**

```json
{  
    "name": "StorageSasLinkedService",  
    "properties": {  
        "type": "AzureStorageSas",  
        "typeProperties": {  
            "sasUri": "<storageUri>?<sasToken>"   
        }  
    }  
}  
```

Per altre informazioni sui servizi collegati, vedere [Connettore Archiviazione tabelle di Azure](data-factory-azure-table-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati di Archiviazione tabelle di Azure, impostare il **tipo** di set di dati su **AzureTable**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella nell'istanza del database di tabelle di Azure a cui fa riferimento il servizio collegato. |Sì. Quando si specifica tableName senza azureTableSourceQuery, tutti i record della tabella vengono copiati nella destinazione. Se si specifica anche azureTableSourceQuery, i record della tabella che soddisfa la query vengono copiati nella destinazione. |

#### <a name="example"></a>Esempio

```json
{
    "name": "AzureTableInput",
    "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
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
```

Per altre informazioni sui servizi collegati, vedere [Connettore Archiviazione tabelle di Azure](data-factory-azure-table-connector.md#dataset-properties). 

### <a name="azure-table-source-in-copy-activity"></a>Origine Tabella di Azure nell'attività di copia
Se si copiano dati dall'archiviazione tabelle di Azure, impostare il **tipo di origine** dell'attività di copia su **AzureTableSource**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| AzureTableSourceQuery |Usare la query personalizzata per leggere i dati. |Stringa di query della tabella di Azure. Vedere gli esempi nella sezione successiva. |No. Quando si specifica tableName senza azureTableSourceQuery, tutti i record della tabella vengono copiati nella destinazione. Se si specifica anche azureTableSourceQuery, i record della tabella che soddisfa la query vengono copiati nella destinazione. |
| azureTableSourceIgnoreTableNotFound |Indica se ignorare l'eccezione di tabella inesistente. |TRUE<br/>FALSE |No |

#### <a name="example"></a>Esempio

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureTabletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "AzureTableSource",
                    "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
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
        }]
    }
}
```

Per altre informazioni sui servizi collegati, vedere [Connettore Archiviazione tabelle di Azure](data-factory-azure-table-connector.md#copy-activity-properties). 

### <a name="azure-table-sink-in-copy-activity"></a>Sink Tabella di Azure nell'attività di copia
Se si copiano dati in un'archiviazione tabelle di Azure, impostare il **tipo di sink** dell'attività di copia su **AzureTableSink**e specificare le proprietà seguenti nella sezione **sink**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| azureTableDefaultPartitionKeyValue |Valore predefinito della chiave di partizione che può essere usato dal sink. |Valore stringa. |No |
| azureTablePartitionKeyName |Specificare il nome della colonna i cui valori vengono usati come chiavi di partizione. Se non specificato, AzureTableDefaultPartitionKeyValue viene usato come chiave di partizione. |Nome colonna. |No |
| azureTableRowKeyName |Specificare il nome della colonna i cui valori vengono usati come chiavi di riga. Se non specificato, usare un GUID per ogni riga. |Nome colonna. |No |
| azureTableInsertType |Modalità di inserimento dei dati in una tabella di Azure.<br/><br/>Questa proprietà verifica se per le righe esistenti nella tabella di output con chiavi di partizione e di riga corrispondenti i valori vengono sostituiti o uniti. <br/><br/>Per scoprire come funzionano queste impostazioni (unione e sostituzione), vedere gli argomenti [Insert or Merge Entity](https://msdn.microsoft.com/library/azure/hh452241.aspx) (Inserire o unire un'entità) e [Insert or Replace Entity](https://msdn.microsoft.com/library/azure/hh452242.aspx) (Inserire o sostituire un'entità). <br/><br> Queste impostazioni vengono applicate a livello di riga, non a livello di tabella, e nessuna delle due opzioni elimina le righe della tabella di output che non esistono nell'input. |merge (impostazione predefinita)<br/>replace |No |
| writeBatchSize |Inserisce dati nella tabella di Azure quando viene raggiunto il writeBatchSize o writeBatchTimeout. |Numero intero (numero di righe) |No (valore predefinito: 10000) |
| writeBatchTimeout |Inserisce i dati nella tabella di Azure quando viene raggiunto writeBatchSize o writeBatchTimeout |Intervallo di tempo<br/><br/>Ad esempio: "00:20:00" (20 minuti) |No. (il valore predefinito è il timeout del client di archiviazione pari a 90 secondi) |

#### <a name="example"></a>Esempio

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "AzureTableOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "AzureTableSink",
                    "writeBatchSize": 100,
                    "writeBatchTimeout": "01:00:00"
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
        }]
    }
}
```
Per altre informazioni sui servizi collegati, vedere [Connettore Archiviazione tabelle di Azure](data-factory-azure-table-connector.md#copy-activity-properties). 

## <a name="amazon-redshift"></a>Amazon Redshift

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Amazon Redshift, impostare il **tipo** di servizio collegato su **AmazonRedshift** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| server |Indirizzo IP o nome host del server Amazon Redshift. |Sì |
| port |Il numero della porta TCP che il server Amazon Redshift usa per ascoltare le connessioni client. |No, valore predefinito: 5439 |
| database |Nome del database Amazon Redshift. |Sì |
| Nome utente |Nome dell'utente che ha accesso al database. |Sì |
| password |La password per l'account utente. |Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "AmazonRedshiftLinkedService",
    "properties": {
        "type": "AmazonRedshift",
        "typeProperties": {
            "server": "<Amazon Redshift host name or IP address>",
            "port": 5439,
            "database": "<database name>",
            "username": "user",
            "password": "password"
        }
    }
}
```

Per altre informazioni, vedere [Connettore Amazon Redshift](#data-factory-amazon-redshift-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati di AmazonRedshift, impostare il **tipo** di set di dati su **RelationalTable**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella nel database Amazon Redshift a cui fa riferimento il servizio collegato. |No (se la **query** di **RelationalSource** è specificata) |


#### <a name="example"></a>Esempio

```json
{
    "name": "AmazonRedshiftInputDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "AmazonRedshiftLinkedService",
        "typeProperties": {
            "tableName": "<Table name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Per altre informazioni, vedere [Connettore Amazon Redshift](#data-factory-amazon-redshift-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origine relazionale nell'attività di copia 
Se si copiano dati da Amazon Redshift, impostare il **tipo di origine** dell'attività di copia su **SqlRelationalSourceSource**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: `select * from MyTable`. |No (se **tableName** di **set di dati** è specificato) |

#### <a name="example"></a>Esempio

```json
{
    "name": "CopyAmazonRedshiftToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonRedshiftInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonRedshiftToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Per altre informazioni, vedere [Connettore Amazon Redshift](#data-factory-amazon-redshift-connector.md#copy-activity-properties).

## <a name="ibm-db2"></a>IBM DB2

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di IBM DB2, impostare il **tipo** di servizio collegato su **OnPremisesDB2** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| server |Nome del server DB2. |Sì |
| database |Nome del database DB2. |Sì |
| schema |Nome dello schema nel database. Il nome dello schema fa distinzione tra maiuscole e minuscole. |No |
| authenticationType |Tipo di autenticazione usato per connettersi al database DB2. I valori possibili sono: anonima, di base e Windows. |Sì |
| username |Specificare il nome utente se si usa l'autenticazione di base o Windows. |No |
| password |Specificare la password per l'account utente specificato per il nome utente. |No |
| gatewayName |Nome del gateway che il servizio Data factory deve usare per connettersi al database DB2 locale. |Sì |

#### <a name="example"></a>Esempio
```json
{
    "name": "OnPremDb2LinkedService",
    "properties": {
        "type": "OnPremisesDb2",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Per altre informazioni, vedere [Connettore IBM DB2](#data-factory-onprem-db2-connector.md#linked-service-properties).

### <a name="dataset"></a>Set di dati
Per definire un set di dati di DB2, impostare il **tipo** di set di dati su **RelationalTable**e specificare le proprietà seguenti nella sezione **typeProperties**:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella nell'istanza del database DB2 a cui fa riferimento il servizio collegato. La proprietà tableName fa distinzione tra maiuscole e minuscole. |No (se la **query** di **RelationalSource** è specificata) 

#### <a name="example"></a>Esempio
```json
{
    "name": "Db2DataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremDb2LinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

Per altre informazioni, vedere [Connettore IBM DB2](#data-factory-onprem-db2-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origine relazionale nell'attività di copia
Se si copiano dati da IBM DB2, impostare il **tipo di origine** dell'attività di copia su **RelationalSource**e specificare le proprietà seguenti nella sezione **source**:


| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: `"query": "select * from "MySchema"."MyTable""`. |No (se **tableName** di **set di dati** è specificato) |

#### <a name="example"></a>Esempio
```json
{
    "name": "CopyDb2ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"Orders\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "Db2DataSet"
            }],
            "outputs": [{
                "name": "AzureBlobDb2DataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "Db2ToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```
Per altre informazioni, vedere [Connettore IBM DB2](#data-factory-onprem-db2-connector.md#copy-activity-properties).

## <a name="mysql"></a>MySQL

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di MySQL, impostare il **tipo** di servizio collegato su **OnPremisesMySql** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| server |Nome del server MySQL. |Sì |
| database |Nome del database MySQL. |Sì |
| schema |Nome dello schema nel database. |No |
| authenticationType |Tipo di autenticazione usato per connettersi al database MySQL. I valori possibili sono:`Basic`. |Sì |
| username |Specificare nome utente per la connessione al database MySQL. |Sì |
| password |Specificare la password per l'account utente specificato. |Sì |
| gatewayName |Nome del gateway che il servizio Data factory deve usare per connettersi al database MySQL locale. |Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "OnPremMySqlLinkedService",
    "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
            "server": "<server name>",
            "database": "<database name>",
            "schema": "<schema name>",
            "authenticationType": "<authentication type>",
            "userName": "<user name>",
            "password": "<password>",
            "gatewayName": "<gateway>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore MySQL](data-factory-onprem-mysql-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati di MySQL, impostare il **tipo** di set di dati su **RelationalTable**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella nell'istanza del database MySQL a cui fa riferimento il servizio collegato. |No (se la **query** di **RelationalSource** è specificata) |

#### <a name="example"></a>Esempio

```json
{
    "name": "MySqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremMySqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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
Per altre informazioni, vedere [Connettore MySQL](data-factory-onprem-mysql-connector.md#dataset-properties). 

### <a name="relational-source-in-copy-activity"></a>Origine relazionale nell'attività di copia
Se si copiano dati da un database MySQL, impostare il **tipo di origine** dell'attività di copia su **RelationalSource**e specificare le proprietà seguenti nella sezione **source**:


| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: `select * from MyTable`. |No (se **tableName** di **set di dati** è specificato) |


#### <a name="example"></a>Esempio
```json
{
    "name": "CopyMySqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MySqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobMySqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MySqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Per altre informazioni, vedere [Connettore MySQL](data-factory-onprem-mysql-connector.md#copy-activity-properties). 

## <a name="oracle"></a>Oracle 

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Oracle, impostare il **tipo** di servizio collegato su **OnPremisesOracle** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| driverType | Specificare il driver da usare per copiare i dati da/verso il database Oracle. I valori consentiti sono **Microsoft** o **ODP** (impostazione predefinita). Per informazioni dettagliate sui driver, vedere la sezione [Versione e installazione supportate](#supported-versions-and-installation). | No |
| connectionString | Specificare le informazioni necessarie per connettersi all'istanza del database Oracle per la proprietà connectionString. | Sì |
| gatewayName | Nome del gateway usato per connettersi al server Oracle locale |Sì |

#### <a name="example"></a>Esempio
```json
{
    "name": "OnPremisesOracleLinkedService",
    "properties": {
        "type": "OnPremisesOracle",
        "typeProperties": {
            "driverType": "Microsoft",
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore Oracle](data-factory-onprem-oracle-connector.md#linked-service-properties).

### <a name="dataset"></a>Set di dati
Per definire un set di dati di Oracle, impostare il **tipo** di set di dati su **OracleTable**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella nell'istanza del database Oracle a cui fa riferimento il servizio collegato. |No, se **oracleReaderQuery** di **OracleSource** è specificato |

#### <a name="example"></a>Esempio

```json
{
    "name": "OracleInput",
    "properties": {
        "type": "OracleTable",
        "linkedServiceName": "OnPremisesOracleLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "external": true,
        "availability": {
            "offset": "01:00:00",
            "interval": "1",
            "anchorDateTime": "2016-02-27T12:00:00",
            "frequency": "Hour"
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
Per altre informazioni, vedere [Connettore Oracle](data-factory-onprem-oracle-connector.md#dataset-properties).

### <a name="oracle-source-in-copy-activity"></a>Origine Oracle nell'attività di copia
Se si copiano dati da un database Oracle, impostare il **tipo di origine** dell'attività di copia su **OracleSource**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| oracleReaderQuery |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: `select * from MyTable` <br/><br/>Se non specificato, l'istruzione SQL eseguita: `select * from MyTable` |No (se **tableName** di **set di dati** è specificato) |

#### <a name="example"></a>Esempio

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "OracletoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " OracleInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "OracleSource",
                    "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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
        }]
    }
}
```

Per altre informazioni, vedere [Connettore Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties).

### <a name="oracle-sink-in-copy-activity"></a>Sink Oracle nell'attività di copia
Se si copiano dati in un database Oracle, impostare il **tipo di sink** dell'attività di copia su **OracleSink**e specificare le proprietà seguenti nella sezione **sink**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. |Intervallo di tempo<br/><br/> Ad esempio: "00:30:00" (30 minuti). |No |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize. |Numero intero (numero di righe) |No (valore predefinito: 100) |
| sqlWriterCleanupScript |Specificare una query da eseguire nell'attività di copia per pulire i dati di una sezione specifica. |Istruzione di query. |No |
| sliceIdentifierColumnName |Specificare il nome della colonna per l'attività di copia da riempire con l'identificatore di sezione generato automaticamente, che viene usato per eliminare i dati di una sezione specifica quando viene nuovamente eseguita. |Nome di colonna di una colonna con tipo di dati binario (32). |No |

#### <a name="example"></a>Esempio
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-05T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoOracle",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": "OracleOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "OracleSink"
                }
            },
            "scheduler": {
                "frequency": "Day",
                "interval": 1
            },
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "OldestFirst",
                "retry": 0,
                "timeout": "01:00:00"
            }
        }]
    }
}
```
Per altre informazioni, vedere [Connettore Oracle](data-factory-onprem-oracle-connector.md#copy-activity-properties).

## <a name="postgresql"></a>PostgreSQL

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di PostgreSQL, impostare il **tipo** di servizio collegato su **OnPremisesPostgreSql** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| server |Nome del server PostgreSQL. |Sì |
| database |Nome del database PostgreSQL. |Sì |
| schema |Nome dello schema nel database. Il nome dello schema fa distinzione tra maiuscole e minuscole. |No |
| authenticationType |Tipo di autenticazione usato per connettersi al database PostgreSQL. I valori possibili sono: anonima, di base e Windows. |Sì |
| username |Specificare il nome utente se si usa l'autenticazione di base o Windows. |No |
| password |Specificare la password per l'account utente specificato per il nome utente. |No |
| gatewayName |Nome del gateway che il servizio Data factory deve usare per connettersi al database PostgreSQL locale. |Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "OnPremPostgreSqlLinkedService",
    "properties": {
        "type": "OnPremisesPostgreSql",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```
Per altre informazioni, vedere [Connettore PostgreSQL](data-factory-onprem-postgresql-connector.md#linked-service-properties).

### <a name="dataset"></a>Set di dati
Per definire un set di dati di PostgreSQL, impostare il **tipo** di set di dati su **RelationalTable**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella nell'istanza del database PostgreSQL a cui fa riferimento il servizio collegato. La proprietà tableName fa distinzione tra maiuscole e minuscole. |No (se la **query** di **RelationalSource** è specificata) |

#### <a name="example"></a>Esempio
```json
{
    "name": "PostgreSqlDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremPostgreSqlLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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
Per altre informazioni, vedere [Connettore PostgreSQL](data-factory-onprem-postgresql-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origine relazionale nell'attività di copia
Se si copiano dati da un database PostgreSQL, impostare il **tipo di origine** dell'attività di copia su **RelationalSource**e specificare le proprietà seguenti nella sezione **source**:


| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: "query": "select * from \"Schema\".\"Tabella\"". |No (se **tableName** di **set di dati** è specificato) |

#### <a name="example"></a>Esempio

```json
{
    "name": "CopyPostgreSqlToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from \"public\".\"usstates\""
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "PostgreSqlDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobPostgreSqlDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "PostgreSqlToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Per altre informazioni, vedere [Connettore PostgreSQL](data-factory-onprem-postgresql-connector.md#copy-activity-properties).

## <a name="sap-business-warehouse"></a>SAP Business Warehouse


### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di SAP Business Warehouse (BW), impostare il **tipo** di servizio collegato su **SapBw**e specificare le proprietà seguenti nella sezione **typeProperties**:  

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
server | Nome del server in cui si trova l'istanza di SAP BW. | string | Sì
systemNumber | Numero del sistema SAP BW. | Numero decimale a due cifre rappresentato come stringa. | Sì
clientId | ID del client nel sistema SAP BW. | Numero decimale a tre cifre rappresentato come stringa. | Sì
username | Nome dell'utente che ha accesso al server SAP | string | Sì
password | Password per l'utente. | string | Sì
gatewayName | Nome del gateway che il servizio Data factory deve usare per connettersi all'istanza di SAP BW locale. | string | Sì
encryptedCredential | Stringa di credenziali crittografata. | string | No

#### <a name="example"></a>Esempio

```json
{
    "name": "SapBwLinkedService",
    "properties": {
        "type": "SapBw",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati SAP BW, impostare il **tipo** del set di dati su **RelationalTable**. Il set di dati SAP BW di tipo **RelationalTable** non supporta alcuna proprietà specifica del tipo.  

#### <a name="example"></a>Esempio

```json
{
    "name": "SapBwDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapBwLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Per altre informazioni, vedere [Connettore SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#dataset-properties). 

### <a name="relational-source-in-copy-activity"></a>Origine relazionale nell'attività di copia
Se si copiano dati da SAP Business Warehouse, impostare il **tipo di origine** dell'attività di copia su **RelationalSource**e specificare le proprietà seguenti nella sezione **source**:


| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query | Specifica la query MDX che consente di leggere i dati dall'istanza di SAP BW. | Query MDX. | Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "CopySapBwToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<MDX query for SAP BW>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapBwDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapBwToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Per altre informazioni, vedere [Connettore SAP Business Warehouse](data-factory-sap-business-warehouse-connector.md#copy-activity-properties). 

## <a name="sap-hana"></a>SAP HANA

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di SAP HANA, impostare il **tipo** di servizio collegato su **SapHana** e specificare le proprietà seguenti nella sezione **typeProperties**:  

Proprietà | Descrizione | Valori consentiti | Obbligatoria
-------- | ----------- | -------------- | --------
server | Nome del server in cui si trova l'istanza di SAP HANA. Se il server usa una porta personalizzata, specificare `server:port`. | string | Sì
authenticationType | Tipo di autenticazione. | string. "Basic" o "Windows" | Sì 
username | Nome dell'utente che ha accesso al server SAP | string | Sì
password | Password per l'utente. | string | Sì
gatewayName | Nome del gateway che il servizio Data factory deve usare per connettersi all'istanza di SAP HANA locale. | string | Sì
encryptedCredential | Stringa di credenziali crittografata. | string | No

#### <a name="example"></a>Esempio

```json
{
    "name": "SapHanaLinkedService",
    "properties": {
        "type": "SapHana",
        "typeProperties": {
            "server": "<server name>",
            "authenticationType": "<Basic, or Windows>",
            "username": "<SAP user>",
            "password": "<Password for SAP user>",
            "gatewayName": "<gateway name>"
        }
    }
}

```
Per altre informazioni, vedere [Connettore SAP HANA](data-factory-sap-hana-connector.md#linked-service-properties).
 
### <a name="dataset"></a>Set di dati
Per definire un set di dati SAP HANA, impostare il **tipo** del set di dati su **RelationalTable**. Il set di dati SAP HANA di tipo **RelationalTable** non supporta alcuna proprietà specifica del tipo. 

#### <a name="example"></a>Esempio

```json
{
    "name": "SapHanaDataset",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "SapHanaLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
Per altre informazioni, vedere [Connettore SAP HANA](data-factory-sap-hana-connector.md#dataset-properties). 

### <a name="relational-source-in-copy-activity"></a>Origine relazionale nell'attività di copia
Se si copiano dati da un archivio dati SAP HANA, impostare il **tipo di origine** dell'attività di copia su **RelationalSource**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query | Specifica la query SQL che consente di leggere i dati dall'istanza di SAP HANA. | Query SQL. | Sì |


#### <a name="example"></a>Esempio


```json
{
    "name": "CopySapHanaToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "<SQL Query for HANA>"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "SapHanaDataset"
            }],
            "outputs": [{
                "name": "AzureBlobDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SapHanaToBlob"
        }],
        "start": "2017-03-01T18:00:00",
        "end": "2017-03-01T19:00:00"
    }
}
```

Per altre informazioni, vedere [Connettore SAP HANA](data-factory-sap-hana-connector.md#copy-activity-properties).


## <a name="sql-server"></a>SQL Server

### <a name="linked-service"></a>Servizio collegato
È stato creato un servizio collegato di tipo **OnPremisesSqlServer** per collegare un database di SQL Server locale a una data factory. La tabella seguente contiene le descrizioni degli elementi JSON specifici per il servizio collegato di SQL Server locale.

La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato SQL Server.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su **OnPremisesSqlServer**. |Sì |
| connectionString |Specificare le informazioni di connectionString necessarie per connettersi al database di SQL Server locale usando l'autenticazione di SQL o Windows. |Sì |
| gatewayName |Nome del gateway che il servizio Data factory deve usare per connettersi al database di SQL Server locale. |Sì |
| username |Specificare il nome utente se si usa l'autenticazione Windows. Esempio: **nomedominio\\nomeutente**. |No |
| password |Specificare la password per l'account utente specificato per il nome utente. |No |

È possibile crittografare le credenziali usando il cmdlet **New-AzureRmDataFactoryEncryptValue** e usarle nella stringa di connessione, come illustrato nell'esempio seguente (proprietà **EncryptedCredential**):  

```json
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Esempio: JSON per l'uso dell'autenticazione SQL

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Esempio: JSON per l'uso dell'autenticazione Windows

Se vengono specificati nome utente e password, il gateway li usa per rappresentare l'account utente specificato per la connessione al database di SQL Server locale. In caso contrario, il gateway si connette a SQL Server direttamente con il contesto di sicurezza del gateway (l'account di avvio).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore SQL Server](data-factory-sqlserver-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati di SQL Server, impostare il **tipo** di set di dati su **SqlServerTable**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella o vista nell'istanza del database di SQL Server a cui fa riferimento il servizio collegato. |Sì |

#### <a name="example"></a>Esempio
```json
{
    "name": "SqlServerInput",
    "properties": {
        "type": "SqlServerTable",
        "linkedServiceName": "SqlServerLinkedService",
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
```

Per altre informazioni, vedere [Connettore SQL Server](data-factory-sqlserver-connector.md#dataset-properties). 

### <a name="sql-source-in-copy-activity"></a>Origine SQL nell'attività di copia
Se si copiano dati da un database SQL Server, impostare il **tipo di origine** dell'attività di copia su **SqlSource**e specificare le proprietà seguenti nella sezione **source**:


| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| SqlReaderQuery |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: `select * from MyTable`. Può fare riferimento a più tabelle del database a cui fa riferimento il set di dati di input. Se non specificato, l'istruzione SQL eseguita: selezionare da MyTable. |No |
| sqlReaderStoredProcedureName |Nome della stored procedure che legge i dati dalla tabella di origine. |Nome della stored procedure. |No |
| storedProcedureParameters |Parametri per la stored procedure. |Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No |

Se la proprietà **sqlReaderQuery** è specificata per SqlSource, l'attività di copia esegue questa query nell'origine del database del server di SQL per ottenere i dati.

In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri).

Se non si specifica il parametro sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione della struttura vengono usate per compilare una query da eseguire nel database del server di SQL. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.

> [!NOTE]
> Quando si usa **sqlReaderStoredProcedureName** è necessario specificare un valore per la proprietà **tableName** nel set di dati JSON. Non sono disponibili convalide eseguite su questa tabella.


#### <a name="example"></a>Esempio
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "SqlServertoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": " SqlServerInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
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
        }]
    }
}
```

In questo esempio la proprietà **sqlReaderQuery** è specificata per SqlSource. L'attività di copia esegue questa query nell'origine del database del server SQL per ottenere i dati. In alternativa, è possibile specificare una stored procedure indicando i parametri **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se la stored procedure accetta parametri). La proprietà sqlReaderQuery può fare riferimento a più tabelle nel database a cui fa riferimento il set di dati di input. Non è limitato solo alla tabella impostata come typeProperty tableName del set di dati.

Se non si specifica il parametro sqlReaderQuery o sqlReaderStoredProcedureName, le colonne definite nella sezione della struttura vengono usate per compilare una query di selezione da eseguire nel database del server di SQL. Se la definizione del set di dati non dispone della struttura, vengono selezionate tutte le colonne della tabella.

Per altre informazioni, vedere [Connettore SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). 

### <a name="sql-sink-in-copy-activity"></a>Sink SQL nell'attività di copia
Se si copiano dati da un database SQL Server, impostare il **tipo di sink** dell'attività di copia su **SqlSink**e specificare le proprietà seguenti nella sezione **ink**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| writeBatchTimeout |Tempo di attesa per l'operazione di inserimento batch da completare prima del timeout. |Intervallo di tempo<br/><br/> Ad esempio: "00:30:00" (30 minuti). |No |
| writeBatchSize |Inserisce dati nella tabella SQL quando la dimensione del buffer raggiunge writeBatchSize. |Numero intero (numero di righe) |No (valore predefinito: 10000) |
| sqlWriterCleanupScript |Specificare la query per l'attività di copia da eseguire in modo che i dati di una sezione specifica vengano eliminati. Per altre informazioni, vedere la sezione relativa alla [ripetibilità](#repeatability-during-copy) . |Istruzione di query. |No |
| sliceIdentifierColumnName |Specificare il nome della colonna per l'attività di copia da riempire con l'identificatore di sezione generato automaticamente, che viene usato per eliminare i dati di una sezione specifica quando viene nuovamente eseguita. Per altre informazioni, vedere la sezione relativa alla [ripetibilità](#repeatability-during-copy) . |Nome di colonna di una colonna con tipo di dati binario (32). |No |
| sqlWriterStoredProcedureName |Nome della stored procedure che esegue l'upsert (aggiornamenti/inserimenti) nella tabella di destinazione. |Nome della stored procedure. |No |
| storedProcedureParameters |Parametri per la stored procedure. |Coppie nome/valore. I nomi e le maiuscole e minuscole dei parametri devono corrispondere ai nomi e alle maiuscole e minuscole dei parametri della stored procedure. |No |
| sqlWriterTableType |Specificare il tipo di tabella da usare nella stored procedure. L'attività di copia rende i dati spostati disponibili in una tabella temporanea con questo tipo di tabella. Il codice della stored procedure può quindi unire i dati copiati con i dati esistenti. |Nome del tipo di tabella. |No |

#### <a name="example"></a>Esempio
La pipeline contiene un'attività di copia configurata per usare i set di dati di input e output ed è programmata per essere eseguita ogni ora. Nella definizione JSON della pipeline, il tipo di **origine** è impostato su **BlobSource** e il tipo **sink** è impostato su **SqlSink**.

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureBlobInput"
            }],
            "outputs": [{
                "name": " SqlServerOutput "
            }],
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "blobColumnSeparators": ","
                },
                "sink": {
                    "type": "SqlSink"
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
        }]
    }
}
```

Per altre informazioni, vedere [Connettore SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). 

## <a name="sybase"></a>Sybase

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Sybase, impostare il **tipo** di servizio collegato su **OnPremisesSybase** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| server |Nome del server Sybase. |Sì |
| database |Nome del database Sybase. |Sì |
| schema |Nome dello schema nel database. |No |
| authenticationType |Tipo di autenticazione usato per connettersi al database Sybase. I valori possibili sono: anonima, di base e Windows. |Sì |
| username |Specificare il nome utente se si usa l'autenticazione di base o Windows. |No |
| password |Specificare la password per l'account utente specificato per il nome utente. |No |
| gatewayName |Nome del gateway che il servizio Data factory deve usare per connettersi al database Sybase locale. |Sì |

#### <a name="example"></a>Esempio
```json
{
    "name": "OnPremSybaseLinkedService",
    "properties": {
        "type": "OnPremisesSybase",
        "typeProperties": {
            "server": "<server>",
            "database": "<database>",
            "schema": "<schema>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore Sybase](data-factory-onprem-sybase-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati di Sybase, impostare il **tipo** di set di dati su **RelationalTable**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella nell'istanza del database Sybase a cui fa riferimento il servizio collegato. |No (se la **query** di **RelationalSource** è specificata) |

#### <a name="example"></a>Esempio

```json
{
    "name": "SybaseDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremSybaseLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

Per altre informazioni, vedere [Connettore Sybase](data-factory-onprem-sybase-connector.md#dataset-properties). 

### <a name="relational-source-in-copy-activity"></a>Origine relazionale nell'attività di copia
Se si copiano dati da un database Sybase, impostare il **tipo di origine** dell'attività di copia su **RelationalSource**e specificare le proprietà seguenti nella sezione **source**:


| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: `select * from MyTable`. |No (se **tableName** di **set di dati** è specificato) |

#### <a name="example"></a>Esempio

```json
{
    "name": "CopySybaseToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "select * from DBA.Orders"
                },
                "sink": {
                    "type": "BlobSink"
                }
            },
            "inputs": [{
                "name": "SybaseDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobSybaseDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "SybaseToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Per altre informazioni, vedere [Connettore Sybase](data-factory-onprem-sybase-connector.md#copy-activity-properties).

## <a name="teradata"></a>Teradata

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Teradata, impostare il **tipo** di servizio collegato su **OnPremisesTeradata** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| server |Nome del server Teradata. |Sì |
| authenticationType |Tipo di autenticazione usato per connettersi al database Teradata. I valori possibili sono: anonima, di base e Windows. |Sì |
| username |Specificare il nome utente se si usa l'autenticazione di base o Windows. |No |
| password |Specificare la password per l'account utente specificato per il nome utente. |No |
| gatewayName |Nome del gateway che il servizio Data factory deve usare per connettersi al database Teradata locale. |Sì |

#### <a name="example"></a>Esempio
```json
{
    "name": "OnPremTeradataLinkedService",
    "properties": {
        "type": "OnPremisesTeradata",
        "typeProperties": {
            "server": "<server>",
            "authenticationType": "<authentication type>",
            "username": "<username>",
            "password": "<password>",
            "gatewayName": "<gatewayName>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore Teradata](data-factory-onprem-teradata-connector.md#linked-service-properties).

### <a name="dataset"></a>Set di dati
Per definire un set di dati BLOB di Teradata, impostare il **tipo** del set di dati su **RelationalTable**. Attualmente non sono disponibili proprietà di tipo supportate per il set di dati Teradata. 

#### <a name="example"></a>Esempio
```json
{
    "name": "TeradataDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "OnPremTeradataLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

Per altre informazioni, vedere [Connettore Teradata](data-factory-onprem-teradata-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origine relazionale nell'attività di copia
Se si copiano dati da un database Teradata, impostare il **tipo di origine** dell'attività di copia su **RelationalSource**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: `select * from MyTable`. |Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "CopyTeradataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "TeradataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobTeradataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "TeradataToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "isPaused": false
    }
}
```

Per altre informazioni, vedere [Connettore Teradata](data-factory-onprem-teradata-connector.md#copy-activity-properties).

## <a name="cassandra"></a>Cassandra


### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Cassandra, impostare il **tipo** di servizio collegato su **OnPremisesCassandra** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| host |Uno o più indirizzi IP o nomi host di server Cassandra.<br/><br/>Specificare un elenco delimitato da virgole degli indirizzi IP o nomi host per la connessione a tutti i server contemporaneamente. |Sì |
| port |La porta TCP che il server Cassandra usa per ascoltare le connessioni client. |No, valore predefinito: 9042 |
| authenticationType |Di base o anonima |Sì |
| username |Specificare il nome utente per l'account utente. |Sì, se authenticationType è impostato su Basic. |
| password |Specifica la password per l'account utente. |Sì, se authenticationType è impostato su Basic. |
| gatewayName |Il nome del gateway che viene usato per connettersi al database Cassandra locale. |Sì |
| encryptedCredential |Credenziali crittografate dal gateway. |No |

#### <a name="example"></a>Esempio

```json
{
    "name": "CassandraLinkedService",
    "properties": {
        "type": "OnPremisesCassandra",
        "typeProperties": {
            "authenticationType": "Basic",
            "host": "<cassandra server name or IP address>",
            "port": 9042,
            "username": "user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore Cassandra](data-factory-onprem-cassandra-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati di Cassandra, impostare il **tipo** di set di dati su **Table**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| keyspace |Nome del keyspace o schema nel database Cassandra. |Sì, se **query** per **CassandraSource** non è definito. |
| tableName |Nome della tabella in un database Cassandra. |Sì, se **query** per **CassandraSource** non è definito. |

#### <a name="example"></a>Esempio

```json
{
    "name": "CassandraInput",
    "properties": {
        "linkedServiceName": "CassandraLinkedService",
        "type": "CassandraTable",
        "typeProperties": {
            "tableName": "mytable",
            "keySpace": "<key space>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

Per altre informazioni, vedere [Connettore Cassandra](data-factory-onprem-cassandra-connector.md#dataset-properties). 

### <a name="cassandra-source-in-copy-activity"></a>Origine Cassandra nell'attività di copia
Se si copiano dati da Cassandra, impostare il **tipo di origine** dell'attività di copia su **Source**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Query SQL-92 o query CQL. Vedere il [riferimento a CQL](https://docs.datastax.com/en/cql/3.1/cql/cql_reference/cqlReferenceTOC.html). <br/><br/>Quando si usa una query SQL, specificare **nome keyspace.nome tabella** per indicare la tabella su cui eseguire la query. |No (se tableName e keyspace sul set di dati sono definiti). |
| consistencyLevel |Il livello di coerenza specifica quante repliche devono rispondere a una richiesta di lettura prima della restituzione dei dati all'applicazione client. Cassandra controlla il numero di repliche specificato perché i dati soddisfino la richiesta di lettura. |ONE, TWO, THREE, QUORUM, ALL, LOCAL_QUORUM, EACH_QUORUM, LOCAL_ONE. Per informazioni dettagliate, vedere [Configuring data consistency](http://docs.datastax.com/en//cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) (Configurazione della coerenza dei dati). |No. Il valore predefinito è ONE. |

#### <a name="example"></a>Esempio
  
```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "CassandraToAzureBlob",
            "description": "Copy from Cassandra to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "CassandraInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "CassandraSource",
                    "query": "select id, firstname, lastname from mykeyspace.mytable"
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
        }]
    }
}
```

Per altre informazioni, vedere [Connettore Cassandra](data-factory-onprem-cassandra-connector.md#copy-activity-properties).

## <a name="mongodb"></a>MongoDB

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di MongoDB, impostare il **tipo** di servizio collegato su **OnPremisesMongoDB** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| server |Indirizzo IP o nome host del server MongoDB. |Sì |
| port |Porta TCP che il server MongoDB usa per ascoltare le connessioni client. |Facoltativo, valore predefinito: 27017 |
| authenticationType |Di base o anonima. |Sì |
| username |Account utente per accedere a MongoDB. |Sì (se si usa l'autenticazione di base). |
| password |Password per l'utente. |Sì (se si usa l'autenticazione di base). |
| authSource |Nome del database MongoDB che si vuole usare per controllare le credenziali di autenticazione. |Facoltativo (se si usa l'autenticazione di base). Predefinito: usa l'account di amministrazione e il database specificato usando la proprietà databaseName. |
| databaseName |Nome del database MongoDB a cui si vuole accedere. |Sì |
| gatewayName |Nome del gateway che accede all'archivio dati. |Sì |
| encryptedCredential |Credenziali crittografate in base al gateway. |Facoltativo |

#### <a name="example"></a>Esempio

```json
{
    "name": "OnPremisesMongoDbLinkedService",
    "properties": {
        "type": "OnPremisesMongoDb",
        "typeProperties": {
            "authenticationType": "<Basic or Anonymous>",
            "server": "< The IP address or host name of the MongoDB server >",
            "port": "<The number of the TCP port that the MongoDB server uses to listen for client connections.>",
            "username": "<username>",
            "password": "<password>",
            "authSource": "< The database that you want to use to check your credentials for authentication. >",
            "databaseName": "<database name>",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore MongoDB](data-factory-on-premises-mongodb-connector.md#linked-service-properties).

### <a name="dataset"></a>Set di dati
Per definire un set di dati MongoDB, impostare il **tipo** del set di dati su **DbCollection**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| collectionName |Nome della raccolta nel database MongoDB. |Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "MongoDbInputDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": "OnPremisesMongoDbLinkedService",
        "typeProperties": {
            "collectionName": "<Collection name>"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

Per altre informazioni, vedere [Connettore MongoDB](data-factory-on-premises-mongodb-connector.md#dataset-properties).

#### <a name="mongodb-source-in-copy-activity"></a>Origine MongoDB nell'attività di copia
Se si copiano dati da MongoDB, impostare il **tipo di origine** dell'attività di copia su **Source**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Stringa di query SQL-92. Ad esempio: `select * from MyTable`. |No, se **collectionName** di **set di dati** è specificato |

#### <a name="example"></a>Esempio

```json
{
    "name": "CopyMongoDBToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "MongoDbSource",
                    "query": "select * from MyTable"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "MongoDbInputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "MongoDBToAzureBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Per altre informazioni, vedere [Connettore MongoDB](data-factory-on-premises-mongodb-connector.md#copy-activity-properties).

## <a name="amazon-s3"></a>Amazon S3


### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Amazon S3, impostare il **tipo** di servizio collegato su **AwsAccessKey** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| accessKeyID |ID della chiave di accesso segreta. |string |Sì |
| secretAccessKey |La stessa chiave di accesso segreta. |La stringa segreta crittografata |Sì |

#### <a name="example"></a>Esempio
```json
{
    "name": "AmazonS3LinkedService",
    "properties": {
        "type": "AwsAccessKey",
        "typeProperties": {
            "accessKeyId": "<access key id>",
            "secretAccessKey": "<secret access key>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore Amazon S3](data-factory-amazon-simple-storage-service-connector.md#linked-service-properties).

### <a name="dataset"></a>Set di dati
Per definire un set di dati di Amazon S3, impostare il **tipo** di set di dati su **AmazonS3**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| bucketName |Il nome del bucket S3. |string |Sì |
| key |La chiave dell'oggetto S3. |string |No |
| prefix |Il prefisso per la chiave dell'oggetto S3. Vengono selezionati gli oggetti le cui chiavi iniziano con questo prefisso. Si applica solo quando la chiave è vuota. |string |No |
| version |La versione dell'oggetto S3 se è stato abilitato il controllo delle versioni S3. |string |No |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format), [JsonFormat](data-factory-supported-file-and-compression-formats.md#json-format), [AvroFormat](data-factory-supported-file-and-compression-formats.md#avro-format), [OrcFormat](data-factory-supported-file-and-compression-formats.md#orc-format) e [ParquetFormat](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No | |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. I livelli supportati sono **Ottimale** e **Più veloce**. Per altre informazioni, vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No | |


> [!NOTE]
> bucketName + chiave specifica la posizione dell'oggetto S3 in cui il bucket è il contenitore radice per gli oggetti S3 e la chiave rappresenta il percorso completo all'oggetto S3.

#### <a name="example-sample-dataset-with-prefix"></a>Esempio: set di dati di esempio con prefisso

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "prefix": "testFolder/test",
            "bucketName": "<S3 bucket name>",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```
#### <a name="example-sample-data-set-with-version"></a>Esempio: set di dati di esempio con versione

```json
{
    "name": "dataset-s3",
    "properties": {
        "type": "AmazonS3",
        "linkedServiceName": "link- testS3",
        "typeProperties": {
            "key": "testFolder/test.orc",
            "bucketName": "<S3 bucket name>",
            "version": "XXXXXXXXXczm0CJajYkHf0_k6LhBmkcL",
            "format": {
                "type": "OrcFormat"
            }
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true
    }
}
```

#### <a name="example-dynamic-paths-for-s3"></a>Esempio: percorsi dinamici per S3
Nell'esempio, vengono utilizzati dei valori fissi per le proprietà chiave e bucketName nel set di dati Amazon S3.

```json
"key": "testFolder/test.orc",
"bucketName": "<S3 bucket name>",
```

È possibile fare in modo che Data Factory calcoli la chiave e il bucketName dinamicamente in fase di runtime utilizzando le variabili di sistema, come SliceStart.

```json
"key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
"bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"
```

È possibile eseguire la stessa operazione per la proprietà prefix di un set di dati di Amazon S3. Per un elenco delle funzioni e delle variabili di sistema supportate da Data Factory, vedere l'articolo [Funzioni e variabili di sistema di Data Factory](data-factory-functions-variables.md) .

Per altre informazioni, vedere [Connettore Amazon S3](data-factory-amazon-simple-storage-service-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Origine File System nell'attività di copia
Se si copiano dati da Amazon S3, impostare il **tipo di origine** dell'attività di copia su **FileSystemSource**e specificare le proprietà seguenti nella sezione **source**:


| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| ricorsiva |Specifica se elencare in modo ricorsivo gli oggetti S3 sotto la directory. |true/false |No |


#### <a name="example"></a>Esempio


```json
{
    "name": "CopyAmazonS3ToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "FileSystemSource",
                    "recursive": true
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "AmazonS3InputDataset"
            }],
            "outputs": [{
                "name": "AzureBlobOutputDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "AmazonS3ToBlob"
        }],
        "start": "2016-08-08T18:00:00",
        "end": "2016-08-08T19:00:00"
    }
}
```

Per altre informazioni, vedere [Connettore Amazon S3](data-factory-amazon-simple-storage-service-connector.md#copy-activity-properties).

## <a name="file-system"></a>File system


### <a name="linked-service"></a>Servizio collegato
È possibile collegare un file system locale a una data factory di Azure con il servizio collegato del **file server locale**. La tabella seguente include le descrizioni degli elementi JSON specifici del servizio collegato del file server locale.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |Verificare che la proprietà type sia impostata su **OnPremisesFileServer**. |Sì |
| host |Specifica il percorso radice della cartella da copiare. Usare il carattere di escape '\' per i caratteri speciali nella stringa. Per ottenere alcuni esempi, vedere [Servizio collegato di esempio e definizioni del set di dati](#sample-linked-service-and-dataset-definitions) . |Sì |
| userid |Specificare l'ID dell'utente che ha accesso al server. |No (se si sceglie encryptedCredential) |
| password |Specificare la password per l'utente (userid). |No (se si sceglie encryptedCredential) |
| encryptedCredential |Specificare le credenziali crittografate che è possibile ottenere eseguendo il cmdlet New-AzureRmDataFactoryEncryptValue. |No (se si sceglie di specificare ID utente e password in testo normale) |
| gatewayName |Specifica il nome del gateway che Data Factory deve usare per connettersi al file server locale. |Sì |

#### <a name="sample-folder-path-definitions"></a>Definizioni del percorso della cartella di esempio 
| Scenario | Host nella definizione del servizio collegato | folderPath nella definizione del set di dati |
| --- | --- | --- |
| Cartella locale nel computer del gateway di gestione dati:  <br/><br/>Esempi: D:\\\* o D:\cartella\sottocartella\\* |D:\\\\ (per Gateway di gestione dati versione 2.0 e successive) <br/><br/> localhost (per le versioni precedenti alla versione 2.0 di Gateway di gestione dati) |.\\\\ o cartella\\\\sottocartella (per il Gateway di gestione dati versione 2.0 e successive) <br/><br/>D:\\\\ o D:\\\\cartella\\\\sottocartella (per la versione del gateway precedente a 2.0) |
| Cartella condivisa remota:  <br/><br/>Esempi: \\\\myserver\\share\\\* o \\\\myserver\\share\\cartella\\sottocartella\\* |\\\\\\\\myserver\\\\share |.\\\\ o cartella\\\\sottocartella |


#### <a name="example-using-username-and-password-in-plain-text"></a>Esempio: Uso di nome utente e password in testo normale

```json
{
    "Name": "OnPremisesFileServerLinkedService",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "\\\\Contosogame-Asia",
            "userid": "Admin",
            "password": "123456",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-encryptedcredential"></a>Esempio: Uso di encryptedcredential

```json
{
    "Name": " OnPremisesFileServerLinkedService ",
    "properties": {
        "type": "OnPremisesFileServer",
        "typeProperties": {
            "host": "D:\\",
            "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore File System](data-factory-onprem-file-system-connector.md#linked-service-properties).

### <a name="dataset"></a>Set di dati
Per definire un set di dati di File System, impostare il **tipo** di set di dati su **FileShare**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| folderPath |Specifica il percorso secondario della cartella. Usare il carattere di escape '\' per i caratteri speciali nella stringa. Per ottenere alcuni esempi, vedere [Servizio collegato di esempio e definizioni del set di dati](#sample-linked-service-and-dataset-definitions) .<br/><br/>È possibile combinare questa proprietà con **partitionBy** per ottenere percorsi di cartelle basati su data e ora di inizio/fine delle sezioni. |Sì |
| fileName |Specificare il nome del file in **folderPath** se si vuole che la tabella faccia riferimento a un file specifico nella cartella. Se non si specifica alcun valore per questa proprietà, la tabella punta a tutti i file nella cartella.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il formato del nome del file generato è il seguente: <br/><br/>`Data.<Guid>.txt` Esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| fileFilter |Specificare un filtro da usare per selezionare un sottoinsieme di file in folderPath anziché tutti i file. <br/><br/>I valori consentiti sono: `*` (più caratteri) e `?` (carattere singolo).<br/><br/>Esempio 1: "fileFilter": "*. log"<br/>Esempio 2: "fileFilter": 2016-1-?.txt"<br/><br/>Si noti che fileFilter è applicabile per un set di dati di input FileShare. |No |
| partitionedBy |È possibile usare partitionedBy per specificare un valore folderPath/fileName dinamico per i dati di una serie temporale. Un esempio è folderPath con parametri per ogni ora di dati. |No |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format), [JsonFormat](data-factory-supported-file-and-compression-formats.md#json-format), [AvroFormat](data-factory-supported-file-and-compression-formats.md#avro-format), [OrcFormat](data-factory-supported-file-and-compression-formats.md#orc-format) e [ParquetFormat](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**, mentre i livelli supportati sono **Optimal** (Ottimale) **Fastest** (Più veloce). vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> Non è possibile usare fileName e fileFilter contemporaneamente.

#### <a name="example"></a>Esempio

```json
{
    "name": "OnpremisesFileSystemInput",
    "properties": {
        "type": " FileShare",
        "linkedServiceName": " OnPremisesFileServerLinkedService ",
        "typeProperties": {
            "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
            "fileName": "{Hour}.csv",
            "partitionedBy": [{
                "name": "Year",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                        "format": "yyyy"
                }
            }, {
                "name": "Month",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "MM"
                }
            }, {
                "name": "Day",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "dd"
                }
            }, {
                "name": "Hour",
                "value": {
                    "type": "DateTime",
                    "date": "SliceStart",
                    "format": "HH"
                }
            }]
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

Per altre informazioni, vedere [Connettore File System](data-factory-onprem-file-system-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Origine File System nell'attività di copia
Se si copiano dati da File System, impostare il **tipo di origine** dell'attività di copia su **FileSystemSource**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| ricorsiva |Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. |True, False (valore predefinito) |No |

#### <a name="example"></a>Esempio

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T19:00:00",
        "description": "Pipeline for copy activity",
        "activities": [{
            "name": "OnpremisesFileSystemtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "OnpremisesFileSystemInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
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
        }]
    }
}
```
Per altre informazioni, vedere [Connettore File System](data-factory-onprem-file-system-connector.md#copy-activity-properties).

### <a name="file-system-sink-in-copy-activity"></a>Sink File System nell'attività di copia
Se si copiano dati in File System, impostare il **tipo di sink** dell'attività di copia su **FileSystemSink**e specificare le proprietà seguenti nella sezione **sink**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| copyBehavior |Definisce il comportamento di copia quando l'origine è BlobSource o FileSystem. |**PreserveHierarchy:** mantiene la gerarchia dei file nella cartella di destinazione. Il percorso relativo del file di origine nella cartella di origine è identico al percorso relativo del file di destinazione nella cartella di destinazione.<br/><br/>**FlattenHierarchy**: tutti i file della cartella di origine vengono creati nel primo livello della cartella di destinazione. Il nome dei file di destinazione viene generato automaticamente.<br/><br/>**MergeFiles**: unisce tutti i file della cartella di origine in un solo file. Se il nome file/BLOB viene specificato, il nome del file unito sarà il nome specificato. In caso contrario, verrà usato un nome file generato automaticamente. |No |
auto-

#### <a name="example"></a>Esempio

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2015-06-01T18:00:00",
        "end": "2015-06-01T20:00:00",
        "description": "pipeline for copy activity",
        "activities": [{
            "name": "AzureSQLtoOnPremisesFile",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [{
                "name": "AzureSQLInput"
            }],
            "outputs": [{
                "name": "OnpremisesFileSystemOutput"
            }],
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
        }]
    }
}
```

Per altre informazioni, vedere [Connettore File System](data-factory-onprem-file-system-connector.md#copy-activity-properties).

## <a name="ftp"></a>FTP

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di FTP, impostare il **tipo** di servizio collegato su **FtpServer** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio | Default |
| --- | --- | --- | --- |
| host |Nome o indirizzo IP del server FTP |Sì |&nbsp; |
| authenticationType |Specificare il tipo di autenticazione |Sì |Di base, anonimo |
| username |Utente che ha accesso al server FTP |No |&nbsp; |
| password |Password per l'utente (nome utente) |No |&nbsp; |
| encryptedCredential |Credenziali crittografate per accedere al server FTP |No |&nbsp; |
| gatewayName |Nome del gateway di Gateway di gestione dati per connettersi a un server FTP locale |No |&nbsp; |
| port |Porta su cui è in ascolto il server FTP |No |21 |
| enableSsl |Specificare se usare FTP su un canale SSL/TLS |No |true |
| enableServerCertificateValidation |Specificare se abilitare la convalida del certificato SSL del server quando si usa FTP sul canale SSL/TLS |No |true |

#### <a name="example-using-anonymous-authentication"></a>Esempio: uso dell'autenticazione anonima

```json
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
```

#### <a name="example-using-username-and-password-in-plain-text-for-basic-authentication"></a>Esempio: uso di nome utente e password in testo normale per l'autenticazione di base

```json
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
```

#### <a name="example-using-port-enablessl-enableservercertificatevalidation"></a>Esempio: uso di porta, enableSsl, enableServerCertificateValidation

```json
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
```

#### <a name="example-using-encryptedcredential-for-authentication-and-gateway"></a>Esempio: uso di encryptedCredential per autenticazione e gateway

```json
{
    "name": "FTPLinkedService",
    "properties": {
        "type": "FtpServer",
        "typeProperties": {
            "host": "myftpserver.com",
            "authenticationType": "Basic",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "gatewayName": "<onpremgateway>"
        }
      }
}
```

Per altre informazioni, vedere [Connettore FTP](data-factory-ftp-connector.md#linked-service-properties).

### <a name="dataset"></a>Set di dati
Per definire un set di dati di FTP, impostare il **tipo** di set di dati su **FileShare**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| folderPath |Sottopercorso alla cartella. Usare il carattere di escape "\" per i caratteri speciali nella stringa. Per ottenere alcuni esempi, vedere [Servizio collegato di esempio e definizioni del set di dati](#sample-linked-service-and-dataset-definitions) .<br/><br/>È possibile combinare questa proprietà con **partitionBy** per ottenere percorsi di cartelle basati su data e ora di inizio/fine delle sezioni. |Sì 
| fileName |Specificare il nome del file in **folderPath** se si vuole che la tabella faccia riferimento a un file specifico nella cartella. Se non si specifica alcun valore per questa proprietà, la tabella punta a tutti i file nella cartella.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il nome del file generato sarà nel formato seguente: <br/><br/>Data.<Guid>.txt, ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| fileFilter |Specificare un filtro da usare per selezionare un sottoinsieme di file in folderPath anziché tutti i file.<br/><br/>I valori consentiti sono: `*` (più caratteri) e `?` (carattere singolo).<br/><br/>Esempi 1: `"fileFilter": "*.log"`<br/>Esempio 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter è applicabile per un set di dati di input FileShare. Questa proprietà non è supportata con HDFS. |No |
| partitionedBy |partitionedBy può essere usato per specificare un valore folderPath dinamico e un nome file per i dati di una serie temporale. Ad esempio, folderPath con parametri per ogni ora di dati. |No |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format), [JsonFormat](data-factory-supported-file-and-compression-formats.md#json-format), [AvroFormat](data-factory-supported-file-and-compression-formats.md#avro-format), [OrcFormat](data-factory-supported-file-and-compression-formats.md#orc-format) e [ParquetFormat](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**, mentre i livelli supportati sono **Optimal** (Ottimale) **Fastest** (Più veloce). Per altre informazioni, vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useBinaryTransfer |Specificare se usare la modalità di trasferimento binario. True per la modalità binaria e false per ASCII. Valore predefinito: True. Questa proprietà può essere usata solo quando il tipo di servizio collegato associato è di tipo: FtpServer. |No |

> [!NOTE]
> filename e fileFilter non possono essere usati contemporaneamente.

#### <a name="example"></a>Esempio

```json
{
    "name": "FTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "FTPLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
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
```

Per altre informazioni, vedere [Connettore FTP](data-factory-ftp-connector.md#dataset-properties).

### <a name="file-system-source-in-copy-activity"></a>Origine File System nell'attività di copia
Se si copiano dati da un server FTP, impostare il **tipo di origine** dell'attività di copia su **FileSystemSource**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| ricorsiva |Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. |True, False (valore predefinito) |No |

#### <a name="example"></a>Esempio

```json
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
        "start": "2016-08-24T18:00:00",
        "end": "2016-08-24T19:00:00"
    }
}
```

Per altre informazioni, vedere [Connettore FTP](data-factory-ftp-connector.md#copy-activity-properties).


## <a name="hdfs"></a>HDFS

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di HDFS, impostare il **tipo** di servizio collegato su **Hdfs** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su: **Hdfs** |Sì |
| Url |URL di HDFS |Sì |
| authenticationType |Anonima o Windows. <br><br> Per usare l'**autenticazione Kerberos** per il connettore HDFS, fare riferimento a [questa sezione](#use-kerberos-authentication-for-hdfs-connector) per impostare correttamente l'ambiente locale. |Sì |
| userName |Nome utente per l'autenticazione di Windows |Sì (per l'autenticazione di Windows) |
| password |Password per l'autenticazione di Windows |Sì (per l'autenticazione di Windows) |
| gatewayName |Nome del gateway che il servizio Data Factory deve usare per connettersi a HDFS. |Sì |
| encryptedCredential |[New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) delle credenziali di accesso. |No |

#### <a name="example-using-anonymous-authentication"></a>Esempio: uso dell'autenticazione anonima

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "userName": "hadoop",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-windows-authentication"></a>Esempio: uso dell'autenticazione Windows

```json
{
    "name": "HDFSLinkedService",
    "properties": {
        "type": "Hdfs",
        "typeProperties": {
            "authenticationType": "Windows",
            "userName": "Administrator",
            "password": "password",
            "url": "http://<machine>:50070/webhdfs/v1/",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore HDFS](#data-factory-hdfs-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati di HDFS, impostare il **tipo** di set di dati su **FileShare**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| folderPath |Percorso della cartella. Esempio: `myfolder`<br/><br/>Usare il carattere di escape "\" per i caratteri speciali nella stringa. Ad esempio: per cartella\sottocartella specificare cartella\\\\sottocartella e per d:\cartellaesempio specificare l'unità d:\\\\cartellaesempio.<br/><br/>È possibile combinare questa proprietà con **partitionBy** per ottenere percorsi di cartelle basati su data e ora di inizio/fine delle sezioni. |Sì |
| fileName |Specificare il nome del file in **folderPath** se si vuole che la tabella faccia riferimento a un file specifico nella cartella. Se non si specifica alcun valore per questa proprietà, la tabella punta a tutti i file nella cartella.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il nome del file generato sarà nel formato seguente: <br/><br/>Data<Guid>.txt, ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| partitionedBy |partitionedBy può essere usato per specificare un valore folderPath dinamico e un nome file per i dati di una serie temporale. Ad esempio, folderPath con parametri per ogni ora di dati. |No |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format), [JsonFormat](data-factory-supported-file-and-compression-formats.md#json-format), [AvroFormat](data-factory-supported-file-and-compression-formats.md#avro-format), [OrcFormat](data-factory-supported-file-and-compression-formats.md#orc-format) e [ParquetFormat](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. I livelli supportati sono **Ottimale** e **Più veloce**. Per altre informazioni, vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

> [!NOTE]
> filename e fileFilter non possono essere usati contemporaneamente.

#### <a name="example"></a>Esempio

```json
{
    "name": "InputDataset",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "HDFSLinkedService",
        "typeProperties": {
            "folderPath": "DataTransfer/UnitTest/"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Per altre informazioni, vedere [Connettore HDFS](#data-factory-hdfs-connector.md#dataset-properties). 

### <a name="file-system-source-in-copy-activity"></a>Origine File System nell'attività di copia
Se si copiano dati da HDFS, impostare il **tipo di origine** dell'attività di copia su **FileSystemSource**e specificare le proprietà seguenti nella sezione **source**:

**FileSystemSource** supporta le proprietà seguenti:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| ricorsiva |Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. |True, False (valore predefinito) |No |

#### <a name="example"></a>Esempio

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "HdfsToBlobCopy",
            "inputs": [{
                "name": "InputDataset"
            }],
            "outputs": [{
                "name": "OutputDataset"
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
            "policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 1,
                "timeout": "00:05:00"
            }
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
```

Per altre informazioni, vedere [Connettore HDFS](#data-factory-hdfs-connector.md#copy-activity-properties).

## <a name="sftp"></a>SFTP


### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di SFTP, impostare il **tipo** di servizio collegato su **Sftp** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- | --- |
| host | Nome o indirizzo IP del server SFTP. |Sì |
| port |Porta su cui è in ascolto il server SFTP. Il valore predefinito è 21 |No |
| authenticationType |Specificare il tipo di autenticazione. Valori consentiti: **Base**, **SshPublicKey**. <br><br> Fare riferimento alle sezioni [Uso dell'autenticazione di base](#using-basic-authentication) e [Uso dell'autenticazione con chiave pubblica SSH](#using-ssh-public-key-authentication) rispettivamente per vedere altre proprietà ed esempi JSON. |Sì |
| skipHostKeyValidation | Specificare se si desidera ignorare la convalida tramite della chiave host. | No. Il valore predefinito è: falso |
| hostKeyFingerprint | Specificare le impronte digitali della chiave host. | Sì se `skipHostKeyValidation` è impostato su falso.  |
| gatewayName |Nome del gateway di gestione dati per connettersi a un server SFTP locale. | Sì se si copiano i dati da un server SFTP locale. |
| encryptedCredential | Credenziali crittografate per accedere al server SFTP. Generato automaticamente quando si specifica l'autenticazione di base (nome utente e password) o l'autenticazione SshPublicKey (nome utente e percorso della chiave privato o contenuto) nella copia guidata o nella finestra di dialogo popup ClickOnce. | No. Applicare solo se si copiano i dati da un server SFTP locale. |

#### <a name="example-using-basic-authentication"></a>Esempio: uso dell'autenticazione di base

Per usare l'autenticazione di base, impostare `authenticationType` come `Basic` e specificare le proprietà seguenti oltre a quelle generiche del connettore SFTP introdotte nell'ultima sezione:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- | --- |
| username | Utente che ha accesso al server SFTP. |Sì |
| password | Password per l'utente (nome utente). | Sì |

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<SFTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "password": "xxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-basic-authentication-with-encrypted-credential"></a>Esempio: autenticazione di base con credenziali crittografate**

```json
{
    "name": "SftpLinkedService",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "Basic",
            "username": "xxx",
            "encryptedCredential": "xxxxxxxxxxxxxxxxx",
            "skipHostKeyValidation": false,
            "hostKeyFingerPrint": "ssh-rsa 2048 xx:00:00:00:xx:00:x0:0x:0x:0x:0x:00:00:x0:x0:00",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="using-ssh-public-key-authentication"></a>Esempio: uso dell'autenticazione con chiave pubblica SSH:**

Per usare l'autenticazione di base, impostare `authenticationType` come `SshPublicKey` e specificare le proprietà seguenti oltre a quelle generiche del connettore SFTP introdotte nell'ultima sezione:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- | --- |
| username |Utente che ha accesso al server SFTP |Sì |
| privateKeyPath | Specificare un percorso assoluto al file di chiave privato a cui il gateway può accedere. | Specificare `privateKeyPath` o `privateKeyContent`. <br><br> Applicare solo se si copiano i dati da un server SFTP locale. |
| privateKeyContent | Una stringa serializzata del contenuto di chiave privata. La copia guidata può leggere il file di chiave privata ed estrarre automaticamente il contenuto della chiave privata. Se si usa un qualsiasi altro strumento/SDK, usare la proprietà privateKeyPath. | Specificare `privateKeyPath` o `privateKeyContent`. |
| passPhrase | Specificare la passphrase o la password per decrittografare la chiave privata se il file della chiave è protetto da una passphrase. | Sì se il file della chiave privata è protetto da una passphrase. |

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyPath",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "<FTP server name or IP address>",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyPath": "D:\\privatekey_openssh",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true,
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-sshpublickey-authentication-using-private-key-content"></a>Esempio: autenticazione SshPublicKey con contenuto della chiave privata**

```json
{
    "name": "SftpLinkedServiceWithPrivateKeyContent",
    "properties": {
        "type": "Sftp",
        "typeProperties": {
            "host": "mysftpserver.westus.cloudapp.azure.com",
            "port": 22,
            "authenticationType": "SshPublicKey",
            "username": "xxx",
            "privateKeyContent": "<base64 string of the private key content>",
            "passPhrase": "xxx",
            "skipHostKeyValidation": true
        }
    }
}
```

Per altre informazioni, vedere [Connettore SFTP](data-factory-sftp-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati di SFTP, impostare il **tipo** di set di dati su **FileShare**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| folderPath |Sottopercorso alla cartella. Usare il carattere di escape "\" per i caratteri speciali nella stringa. Per ottenere alcuni esempi, vedere [Servizio collegato di esempio e definizioni del set di dati](#sample-linked-service-and-dataset-definitions) .<br/><br/>È possibile combinare questa proprietà con **partitionBy** per ottenere percorsi di cartelle basati su data e ora di inizio/fine delle sezioni. |Sì |
| fileName |Specificare il nome del file in **folderPath** se si vuole che la tabella faccia riferimento a un file specifico nella cartella. Se non si specifica alcun valore per questa proprietà, la tabella punta a tutti i file nella cartella.<br/><br/>Quando fileName non viene specificato per un set di dati di output, il nome del file generato sarà nel formato seguente: <br/><br/>Data.<Guid>.txt, ad esempio: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt |No |
| fileFilter |Specificare un filtro da usare per selezionare un sottoinsieme di file in folderPath anziché tutti i file.<br/><br/>I valori consentiti sono: `*` (più caratteri) e `?` (carattere singolo).<br/><br/>Esempi 1: `"fileFilter": "*.log"`<br/>Esempio 2: `"fileFilter": 2016-1-?.txt"`<br/><br/> fileFilter è applicabile per un set di dati di input FileShare. Questa proprietà non è supportata con HDFS. |No |
| partitionedBy |partitionedBy può essere usato per specificare un valore folderPath dinamico e un nome file per i dati di una serie temporale. Ad esempio, folderPath con parametri per ogni ora di dati. |No |
| format | Sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Impostare la proprietà **type** nell'area format su uno di questi valori. Per altre informazioni, vedere le sezioni [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format), [JsonFormat](data-factory-supported-file-and-compression-formats.md#json-format), [AvroFormat](data-factory-supported-file-and-compression-formats.md#avro-format), [OrcFormat](data-factory-supported-file-and-compression-formats.md#orc-format) e [ParquetFormat](data-factory-supported-file-and-compression-formats.md#parquet-format). <br><br> Per **copiare i file così come sono** tra archivi basati su file (copia binaria), è possibile ignorare la sezione del formato nelle definizioni dei set di dati di input e di output. |No |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. I livelli supportati sono **Ottimale** e **Più veloce**. Per altre informazioni, vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |
| useBinaryTransfer |Specificare se usare la modalità di trasferimento binario. True per la modalità binaria e false per ASCII. Valore predefinito: True. Questa proprietà può essere usata solo quando il tipo di servizio collegato associato è di tipo: FtpServer. |No |

> [!NOTE]
> filename e fileFilter non possono essere usati contemporaneamente.

#### <a name="example"></a>Esempio

```json
{
    "name": "SFTPFileInput",
    "properties": {
        "type": "FileShare",
        "linkedServiceName": "SftpLinkedService",
        "typeProperties": {
            "folderPath": "<path to shared folder>",
            "fileName": "test.csv"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Per altre informazioni, vedere [Connettore SFTP](data-factory-sftp-connector.md#dataset-properties). 

### <a name="file-system-source-in-copy-activity"></a>Origine File System nell'attività di copia
Se si copiano dati da un'origine SFTP, impostare il **tipo di origine** dell'attività di copia su **FileSystemSource**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| ricorsiva |Indica se i dati vengono letti in modo ricorsivo dalle cartelle secondarie o solo dalla cartella specificata. |True, False (valore predefinito) |No |



#### <a name="example"></a>Esempio

```json
{
    "name": "pipeline",
    "properties": {
        "activities": [{
            "name": "SFTPToBlobCopy",
            "inputs": [{
                "name": "SFTPFileInput"
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
        "start": "2017-02-20T18:00:00",
        "end": "2017-02-20T19:00:00"
    }
}
```

Per altre informazioni, vedere [Connettore SFTP](data-factory-sftp-connector.md#copy-activity-properties).


## <a name="http"></a>HTTP

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di HTTP, impostare il **tipo** di servizio collegato su **Http** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| URL | URL di base al server Web | Sì |
| authenticationType | Specifica il tipo di autenticazione. I valori consentiti sono: **Anonymous**, **Basic**, **Digest**, **Windows** e **ClientCertificate**. <br><br> Fare riferimento alle sezioni sotto questa tabella per altre proprietà e altri esempi JSON per questi tipi di autenticazione. | Sì |
| enableServerCertificateValidation | Specificare se abilitare la convalida del certificato SSL del server se l'origine è un server Web HTTPS | No, il valore predefinito è true |
| gatewayName | Nome del gateway di gestione dati per connettersi a un'origine HTTP locale. | Sì se si copiano i dati da un'origine HTTP locale. |
| encryptedCredential | Credenziali crittografate per accedere all'endpoint HTTP. Generate automaticamente quando si configurano le informazioni di autenticazione nella procedura di copia guidata o nella finestra di dialogo popup ClickOnce. | No. Applicare solo se si copiano i dati da un server HTTP locale. |

#### <a name="example-using-basic-digest-or-windows-authentication"></a>Esempio: uso dell'autenticazione di base, Digest o Windows
Impostare `authenticationType` come `Basic`, `Digest`, o `Windows` e specificare le proprietà seguenti oltre a quelle generiche del connettore HTTP illustrate in precedenza:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| username | Nome utente per accedere all'endpoint HTTP. | Sì |
| password | Password per l'utente (nome utente). | Sì |

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "basic",
            "url": "https://en.wikipedia.org/wiki/",
            "userName": "user name",
            "password": "password"
        }
    }
}
```

#### <a name="example-using-clientcertificate-authentication"></a>Esempio: uso dell'autenticazione ClientCertificate

Per usare l'autenticazione di base, impostare `authenticationType` come `ClientCertificate` e specificare le proprietà seguenti oltre a quelle generiche del connettore HTTP introdotte in precedenza:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| embeddedCertData | Contenuto con codifica Base 64 dei dati binari del file di scambio di informazioni personali (PFX, Personal Information Exchange). | Specificare `embeddedCertData` o `certThumbprint`. |
| certThumbprint | L'identificazione personale del certificato installato nell'archivio certificati del computer gateway. Applicare solo se si copiano i dati da un'origine HTTP locale. | Specificare `embeddedCertData` o `certThumbprint`. |
| password | Password associata al certificato. | No |

Se si usa `certThumbprint` per l'autenticazione e il certificato è installato nell'archivio personale del computer locale, è necessario concedere l'autorizzazione di lettura per il servizio gateway:

1. Avviare Microsoft Management Console (MMC). Aggiungere lo snap-in **Certificati** con il **Computer locale** come destinazione.
2. Espandere **Certificati**, **Personali** e fare clic su **Certificati**.
3. Fare clic con il tasto destro del mouse sul certificato dall'archivio personale, quindi selezionare **Tutte le attività**->**Gestisci chiavi private...**
3. Nella scheda **Sicurezza** aggiungere l'account utente in cui è in esecuzione il Servizio host di Gateway di gestione dati con l'accesso in lettura al certificato.  

**Esempio: uso del certificato client:** questo servizio collegato collega la data factory a un server Web HTTP locale. Usa un file del certificato client installato nel computer che dispone del Gateway di gestione dati.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "certThumbprint": "thumbprint of certificate",
            "gatewayName": "gateway name"
        }
    }
}
```

#### <a name="example-using-client-certificate-in-a-file"></a>Esempio: utilizzo di un certificato client in un file
Questo servizio collegato collega la data factory a un server Web HTTP locale. Usa un file del certificato client nel computer con installato il Gateway di gestione dati.

```json
{
    "name": "HttpLinkedService",
    "properties": {
        "type": "Http",
        "typeProperties": {
            "authenticationType": "ClientCertificate",
            "url": "https://en.wikipedia.org/wiki/",
            "embeddedCertData": "base64 encoded cert data",
            "password": "password of cert"
        }
    }
}
```

Per altre informazioni, vedere [Connettore HTTP](data-factory-http-connector.md#linked-service-properties).

### <a name="dataset"></a>Set di dati
Per definire un set di dati di HTTP, impostare il **tipo** di set di dati su **Http**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| relativeUrl | URL relativo della risorsa che contiene i dati. Quando non è specificato alcun percorso, viene usato solo l'URL specificato nella definizione del servizio collegato. <br><br> Per creare un URL dinamico, è possibile usare le [unzioni e variabili di sistema di Data Factory](data-factory-functions-variables.md), ad esempio: `"relativeUrl": "$$Text.Format('/my/report?month={0:yyyy}-{0:MM}&fmt=csv', SliceStart)"`. | No |
| requestMethod | Metodo HTTP. I valori consentiti sono **GET** o **POST**. | No. Il valore predefinito è `GET`. |
| additionalHeaders | Intestazioni richiesta HTTP aggiuntive. | No |
| requestBody | Il corpo della richiesta HTTP. | No |
| format | Se si desidera semplicemente **recuperare i dati dall'endpoint HTTP così come sono** senza analizzarli, ignorare questa impostazione di formato. <br><br> Se si desidera analizzare i contenuti di risposta HTTP durante la copia, sono supportati i tipi di formato seguenti: **TextFormat**, **JsonFormat**, **AvroFormat**, **OrcFormat** e **ParquetFormat**. Per altre informazioni, vedere le sezioni [TextFormat](data-factory-supported-file-and-compression-formats.md#text-format), [JsonFormat](data-factory-supported-file-and-compression-formats.md#json-format), [AvroFormat](data-factory-supported-file-and-compression-formats.md#avro-format), [OrcFormat](data-factory-supported-file-and-compression-formats.md#orc-format) e [ParquetFormat](data-factory-supported-file-and-compression-formats.md#parquet-format). |No |
| compressione | Specificare il tipo e il livello di compressione dei dati. I tipi supportati sono **GZip**, **Deflate**, **BZip2** e **ZipDeflate**. I livelli supportati sono **Ottimale** e **Più veloce**. Per altre informazioni, vedere [File e formati di compressione in Azure Data Factory](data-factory-supported-file-and-compression-formats.md#compression-support). |No |

#### <a name="example-using-the-get-default-method"></a>Esempio: usando il metodo GET (predefinito)

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "XXX/test.xml",
            "additionalHeaders": "Connection: keep-alive\nUser-Agent: Mozilla/5.0\n"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

#### <a name="example-using-the-post-method"></a>Esempio: usando il metodo POST

```json
{
    "name": "HttpSourceDataInput",
    "properties": {
        "type": "Http",
        "linkedServiceName": "HttpLinkedService",
        "typeProperties": {
            "relativeUrl": "/XXX/test.xml",
            "requestMethod": "Post",
            "requestBody": "body for POST HTTP request"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```
Per altre informazioni, vedere [Connettore HTTP](data-factory-http-connector.md#dataset-properties).

### <a name="http-source-in-copy-activity"></a>Origine HTTP nell'attività di copia
Se si copiano dati da un'origine HTTP, impostare il **tipo di origine** dell'attività di copia su **HttpSource**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| httpRequestTimeout | Il timeout (TimeSpan) durante il quale la richiesta HTTP attende una risposta. Si tratta del timeout per ottenere una risposta, non per leggere i dati della risposta stessa. | No. Valore predefinito: 00:01:40 |


#### <a name="example"></a>Esempio

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "HttpSourceToAzureBlob",
            "description": "Copy from an HTTP source to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "HttpSourceDataInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "HttpSource"
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
        }]
    }
}
```

Per altre informazioni, vedere [Connettore HTTP](data-factory-http-connector.md#copy-activity-properties).

## <a name="odata"></a>OData

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di OData, impostare il **tipo** di servizio collegato su **OData** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| URL |URL del servizio OData. |Sì |
| authenticationType |Tipo di autenticazione usato per connettersi all'origine OData. <br/><br/> Per OData in cloud, i valori possibili sono Anonymous, Basic e OAuth. Si noti che Azure Data Factory attualmente supporta solo OAuth basato su Azure Active Directory). <br/><br/> Per OData locale, i valori possibili sono Anonima, Di base e Windows. |Sì |
| username |Specificare il nome utente se si usa l'autenticazione di base. |Sì (solo se si usa l'autenticazione di base) |
| password |Specificare la password per l'account utente specificato per il nome utente. |Sì (solo se si usa l'autenticazione di base) |
| authorizedCredential |Se si usa OAuth, fare clic sul pulsante **Autorizza** nella procedura guidata di copia di Data Factory o nell'Editor e immettere le credenziali. Il valore di questa proprietà viene quindi generato automaticamente. |Sì (solo se si usa l'autenticazione OAuth) |
| gatewayName |Nome del gateway che il servizio Data Factory deve usare per connettersi al servizio OData locale. Specificare solo se si copiano dati da un'origine OData locale. |No |

#### <a name="example---using-basic-authentication"></a>Esempio - Uso dell'autenticazione di base
```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Basic",
            "username": "username",
            "password": "password"
        }
    }
}
```

#### <a name="example---using-anonymous-authentication"></a>Esempio - Uso dell'autenticazione anonima

```json
{
    "name": "ODataLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "http://services.odata.org/OData/OData.svc",
            "authenticationType": "Anonymous"
        }
    }
}
```

#### <a name="example---using-windows-authentication-accessing-on-premises-odata-source"></a>Esempio - Uso dell'autenticazione Windows per accedere all'origine OData locale

```json
{
    "name": "inputLinkedService",
    "properties": {
        "type": "OData",
        "typeProperties": {
            "url": "<endpoint of on-premises OData source, for example, Dynamics CRM>",
            "authenticationType": "Windows",
            "username": "domain\\user",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example---using-oauth-authentication-accessing-cloud-odata-source"></a>Esempio - Uso dell'autenticazione OAuth per accedere all'origine OData cloud
```json
{
    "name": "inputLinkedService",
    "properties":
    {
        "type": "OData",
            "typeProperties":
        {
            "url": "<endpoint of cloud OData source, for example, https://<tenant>.crm.dynamics.com/XRMServices/2011/OrganizationData.svc>",
            "authenticationType": "OAuth",
            "authorizedCredential": "<auto generated by clicking the Authorize button on UI>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore OData](data-factory-odata-connector.md#linked-service-properties).

### <a name="dataset"></a>Set di dati
Per definire un set di dati di OData, impostare il **tipo** di set di dati su **ODataResource**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| path |Percorso della risorsa OData |No |

#### <a name="example"></a>Esempio

```json
{
    "name": "ODataDataset",
    "properties": {
        "type": "ODataResource",
        "typeProperties": {
            "path": "Products"
        },
        "linkedServiceName": "ODataLinkedService",
        "structure": [],
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
        }
    }
}
```

Per altre informazioni, vedere [Connettore OData](data-factory-odata-connector.md#dataset-properties).

### <a name="relational-source-in-copy-activity"></a>Origine relazionale nell'attività di copia
Se si copiano dati da un'origine OData, impostare il **tipo di origine** dell'attività di copia su **RelationalSource**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Esempio | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |"?$select=Name, Description&$top=5" |No |

#### <a name="example"></a>Esempio

```json
{
    "name": "CopyODataToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "?$select=Name, Description&$top=5"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "ODataDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobODataDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "ODataToBlob"
        }],
        "start": "2017-02-01T18:00:00",
        "end": "2017-02-03T19:00:00"
    }
}
```

Per altre informazioni, vedere [Connettore OData](data-factory-odata-connector.md#copy-activity-properties).


## <a name="odbc"></a>ODBC


### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di ODBC, impostare il **tipo** di servizio collegato su **OnPremisesOdbc** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| connectionString |La parte delle credenziali non di accesso della stringa di connessione e una credenziale crittografata facoltativa. Vedere gli esempi nelle sezioni seguenti. |Sì |
| credential |La parte delle credenziali di accesso della stringa di connessione specificata nel formato di valore della proprietà specifico del driver. Esempio: "Uid=<user ID>;Pwd=<password>;RefreshToken=<secret refresh token>;". |No |
| authenticationType |Tipo di autenticazione usato per connettersi all'archivio dati ODBC. I valori possibili sono: anonima e di base. |Sì |
| username |Specificare il nome utente se si usa l'autenticazione di base. |No |
| password |Specificare la password per l'account utente specificato per il nome utente. |No |
| gatewayName |Nome del gateway che il servizio Data Factory deve usare per connettersi all'archivio dati ODBC. |Sì |

#### <a name="example---using-basic-authentication"></a>Esempio - Uso dell'autenticazione di base

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=Server.database.windows.net; Database=TestDatabase;",
            "userName": "username",
            "password": "password",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```
#### <a name="example---using-basic-authentication-with-encrypted-credentials"></a>Esempio - Uso dell'autenticazione di base con credenziali crittografate
È possibile crittografare le credenziali usando il cmdlet [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx), in Azure PowerShell versione 1.0, oppure [New-AzureDataFactoryEncryptValue](https://msdn.microsoft.com/library/dn834940.aspx), in Azure PowerShell versione 0.9 o precedente.  

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Basic",
            "connectionString": "Driver={SQL Server};Server=myserver.database.windows.net; Database=TestDatabase;;EncryptedCredential=eyJDb25uZWN0...........................",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

#### <a name="example-using-anonymous-authentication"></a>Esempio: uso dell'autenticazione anonima

```json
{
    "name": "ODBCLinkedService",
    "properties": {
        "type": "OnPremisesOdbc",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "connectionString": "Driver={SQL Server};Server={servername}.database.windows.net; Database=TestDatabase;",
            "credential": "UID={uid};PWD={pwd}",
            "gatewayName": "<onpremgateway>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore ODBC](data-factory-odbc-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati di ODBC, impostare il **tipo** di set di dati su **RelationalTable**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella nell'archivio dati ODBC. |Sì |


#### <a name="example"></a>Esempio

```json
{
    "name": "ODBCDataSet",
    "properties": {
        "type": "RelationalTable",
        "linkedServiceName": "ODBCLinkedService",
        "typeProperties": {},
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

Per altre informazioni, vedere [Connettore ODBC](data-factory-odbc-connector.md#dataset-properties). 

### <a name="relational-source-in-copy-activity"></a>Origine relazionale nell'attività di copia
Se si copiano dati da un archivio dati ODBC, impostare il **tipo di origine** dell'attività di copia su **RelationalSource**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Stringa di query SQL. Ad esempio: `select * from MyTable`. |Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "CopyODBCToBlob",
    "properties": {
        "description": "pipeline for copy activity",
        "activities": [{
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                },
                "sink": {
                    "type": "BlobSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:00"
                }
            },
            "inputs": [{
                "name": "OdbcDataSet"
            }],
            "outputs": [{
                "name": "AzureBlobOdbcDataSet"
            }],
            "policy": {
                "timeout": "01:00:00",
                "concurrency": 1
            },
            "scheduler": {
                "frequency": "Hour",
                "interval": 1
            },
            "name": "OdbcToBlob"
        }],
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00"
    }
}
``` 

Per altre informazioni, vedere [Connettore ODBC](data-factory-odbc-connector.md#copy-activity-properties).

## <a name="salesforce"></a>Salesforce


### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Salesforce, impostare il **tipo** di servizio collegato su **Salesforce** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| environmentUrl | Specificare l'URL dell'istanza di Salesforce. <br><br> - Il valore predefinito è "https://login.salesforce.com". <br> - Per copiare i dati dalla sandbox, specificare "https://test.salesforce.com". <br> - Per copiare i dati dal dominio personalizzato, specificare ad esempio "https://[dominio].my.salesforce.com". |No |
| username |Specificare un nome utente per l'account utente. |Sì |
| password |Specificare la password per l'account utente. |Sì |
| securityToken |Specificare un token di sicurezza per l'account utente. Per istruzioni su come ottenere o reimpostare un token di sicurezza, vedere [Get security token](https://help.salesforce.com/apex/HTViewHelpDoc?id=user_security_token.htm) (Ottenere un token di sicurezza). Per informazioni generali sui token di sicurezza, vedere [Security and the API](https://developer.salesforce.com/docs/atlas.en-us.api.meta/api/sforce_api_concepts_security.htm)(Sicurezza e API). |Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "SalesforceLinkedService",
    "properties": {
        "type": "Salesforce",
        "typeProperties": {
            "username": "<user name>",
            "password": "<password>",
            "securityToken": "<security token>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore Salesforce](data-factory-salesforce-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati di Salesforce, impostare il **tipo** di set di dati su **RelationalTable**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| tableName |Nome della tabella in Salesforce. |No, se è specificata una **query** di **RelationalSource** |

#### <a name="example"></a>Esempio

```json
{
    "name": "SalesforceInput",
    "properties": {
        "linkedServiceName": "SalesforceLinkedService",
        "type": "RelationalTable",
        "typeProperties": {
            "tableName": "AllDataType__c"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
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

Per altre informazioni, vedere [Connettore Salesforce](data-factory-salesforce-connector.md#dataset-properties). 

### <a name="relational-source-in-copy-activity"></a>Origine relazionale nell'attività di copia
Se si copiano dati da Salesforce, impostare il **tipo di origine** dell'attività di copia su **RelationalSource**e specificare le proprietà seguenti nella sezione **source**:

| Proprietà | Descrizione | Valori consentiti | Obbligatorio |
| --- | --- | --- | --- |
| query |Usare la query personalizzata per leggere i dati. |Query SQL-92 o query [Salesforce Object Query Language (SOQL)](https://developer.salesforce.com/docs/atlas.en-us.soql_sosl.meta/soql_sosl/sforce_api_calls_soql.htm). Ad esempio: `select * from MyTable__c`. |No, se è specificato **tableName** per il **set di dati** |

#### <a name="example"></a>Esempio  



```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "SalesforceToAzureBlob",
            "description": "Copy from Salesforce to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "SalesforceInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "RelationalSource",
                    "query": "SELECT Id, Col_AutoNumber__c, Col_Checkbox__c, Col_Currency__c, Col_Date__c, Col_DateTime__c, Col_Email__c, Col_Number__c, Col_Percent__c, Col_Phone__c, Col_Picklist__c, Col_Picklist_MultiSelect__c, Col_Text__c, Col_Text_Area__c, Col_Text_AreaLong__c, Col_Text_AreaRich__c, Col_URL__c, Col_Text_Encrypt__c, Col_Lookup__c FROM AllDataType__c"
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
        }]
    }
}
```

> [!IMPORTANT]
> La parte "__c" del nome dell'API è necessaria per qualsiasi oggetto personalizzato.

Per altre informazioni, vedere [Connettore Salesforce](data-factory-salesforce-connector.md#copy-activity-properties). 

## <a name="web-data"></a>Dati Web 

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato Web, impostare il **tipo** di servizio collegato su **Web** e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| Url |URL dell'origine Web |Sì |
| authenticationType |Anonimo. |Sì |
 

#### <a name="example"></a>Esempio


```json
{
    "name": "web",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "authenticationType": "Anonymous",
            "url": "https://en.wikipedia.org/wiki/"
        }
    }
}
```

Per altre informazioni, vedere [Connettore Tabella Web](data-factory-web-table-connector.md#linked-service-properties). 

### <a name="dataset"></a>Set di dati
Per definire un set di dati Web, impostare il **tipo** di set di dati su **WebTable**e specificare le proprietà seguenti nella sezione **typeProperties**: 

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| type |Tipo del set di dati. Deve essere impostato su **WebTable** |Sì |
| path |URL relativo della risorsa che contiene la tabella. |No. Quando non è specificato alcun percorso, viene usato solo l'URL specificato nella definizione del servizio collegato. |
| index |Indice della tabella nella risorsa. Per i passaggi per ottenere l'indice di una tabella in una pagina HTML, vedere la sezione [Ottenere l'indice di una tabella in una pagina HTML](#get-index-of-a-table-in-an-html-page) . |Sì |

#### <a name="example"></a>Esempio

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Per altre informazioni, vedere [Connettore Tabella Web](data-factory-web-table-connector.md#dataset-properties). 

### <a name="web-source-in-copy-activity"></a>Origine Web nell'attività di copia
Se si copiano dati da una tabella Web, impostare il **tipo di origine** dell'attività di copia su **WebSource**. Quando l'origine nell'attività di copia è di tipo **WebSource**non sono attualmente supportate altre proprietà.

#### <a name="example"></a>Esempio

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-06-01T18:00:00",
        "end": "2016-06-01T19:00:00",
        "description": "pipeline with copy activity",
        "activities": [{
            "name": "WebTableToAzureBlob",
            "description": "Copy from a Web table to an Azure blob",
            "type": "Copy",
            "inputs": [{
                "name": "WebTableInput"
            }],
            "outputs": [{
                "name": "AzureBlobOutput"
            }],
            "typeProperties": {
                "source": {
                    "type": "WebSource"
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
        }]
    }
}
```

Per altre informazioni, vedere [Connettore Tabella Web](data-factory-web-table-connector.md#copy-activity-properties). 

## <a name="compute-environments"></a>Ambienti di calcolo
La tabella seguente elenca gli ambienti di calcolo supportati da Data Factory e le attività di trasformazione eseguibili in tali ambienti. Fare clic sul collegamento al calcolo al quale si è interessati per visualizzare gli schemi JSON per il servizio collegato per eseguirne il collegamento a una data factory. 

| Ambiente di calcolo | Attività |
| --- | --- |
| [Cluster HDInsight su richiesta](#on-demand-azure-hdinsight-cluster) o [il proprio cluster HDInsight](#existing-azure-hdinsight-cluster) |[Attività personalizzata .NET ](#net-custom-activity), [attività Hive](#hdinsight-hive-activity), [attività Pig](#hdinsight-pig-activity, [attività MapReduce](#hdinsight-mapreduce-activity), [attività di Hadoop Streaming](#hdinsight-streaming-activityd), [attività Spark](#hdinsight-spark-activity) |
| [Azure Batch](#azure-batch) |[Attività personalizzata .NET](#net-custom-activity) |
| [Azure Machine Learning](#azure-machine-learning) | [Attività di esecuzione batch di Machine Learning](#machine-learning-batch-execution-activity), [Attività della risorsa di aggiornamento di Machine Learning](#machine-learning-update-resource-activity) |
| [Azure Data Lake Analytics.](#azure-data-lake-analytics) |[Attività U-SQL di Data Lake Analytics](#data-lake-analytics-u-sql-activity) |
| [Azure SQL Database](#azure-sql-database-1), [Azure SQL Data Warehouse](#azure-sql-data-warehouse-1), [SQL Server](#sql-server-1) |[Stored procedure](#stored-procedure-activity) |

## <a name="on-demand-azure-hdinsight-cluster"></a>Cluster HDInsight di Azure su richiesta
Il servizio Data factory di Azure può creare automaticamente un cluster HDInsight su richiesta basato su Windows o Linux per elaborare i dati. La creazione del cluster avviene nella stessa area dell'account di archiviazione (proprietà linkedServiceName in JSON) associato al cluster. Su questo servizio collegato è possibile eseguire le attività di trasformazione seguenti: [attività personalizzata .NET](#net-custom-activity), [attività Hive](#hdinsight-hive-activity), [attività Pig] (#hdinsight-pig-activity, [attività MapReduce](#hdinsight-mapreduce-activity), [attività di Hadoop Streaming](#hdinsight-streaming-activityd), [attività Spark](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Servizio collegato 
La tabella seguente fornisce le descrizioni delle proprietà usate nella definizione JSON di Azure per un servizio collegato HDInsight su richiesta.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su **HDInsightOnDemand**. |Sì |
| clusterSize |Numero di nodi del ruolo di lavoro/nodi dati nel cluster. Il cluster HDInsight viene creato con 2 nodi head e il numero di nodi del ruolo di lavoro specificato per questa proprietà. I nodi sono di dimensione Standard_D3, con 4 core, quindi un cluster di 4 nodi del ruolo di lavoro ha 24 core, ossia 4\*4 = 16 core per i nodi del ruolo di lavoro + 2\*4 = 8 core per i nodi head. Vedere [Creare cluster Hadoop basati su Linux in HDInsight](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md) per i dettagli sul livello Standard_D3. |Sì |
| timeToLive |Il tempo di inattività consentito per il cluster HDInsight su richiesta. Specifica per quanto tempo il cluster HDInsight su richiesta rimane attivo dopo il completamento di un'attività eseguita se non sono presenti altri processi attivi del cluster.<br/><br/>Ad esempio, se un'esecuzione di attività accetta 6 minuti e timetolive è impostato su 5 minuti, il cluster rimane attivo per altri 5 minuti dopo i 6 minuti di elaborazione dell'attività. Se un'altra attività viene eseguita entro i 6 minuti consentiti, verrà elaborata dal cluster stesso.<br/><br/>Poiché la creazione di un cluster HDInsight su richiesta è un'operazione che usa un numero elevato di risorse e potrebbe richiedere alcuni minuti, usare questa impostazione a seconda delle necessità per migliorare le prestazioni di una data factory riutilizzando un cluster HDInsight su richiesta.<br/><br/>Se si imposta il valore della proprietà timetolive su 0, il cluster viene eliminato non appena l'attività in elaborazione termina. D'altra parte, se si imposta un valore elevato, il cluster può rimanere inattivo inutilmente causando costi elevati. È quindi importante impostare il valore appropriato in base alle esigenze.<br/><br/>Più pipeline possono condividere la stessa istanza del cluster HDInsight su richiesta se il valore della proprietà timetolive è impostato in modo appropriato. |Sì |
| version |Versione del cluster HDInsight Per informazioni dettagliate vedere le [versioni supportate di HDInsight in Azure Data Factory](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). |No |
| linkedServiceName |Servizio collegato Archiviazione di Azure che il cluster su richiesta deve usare per l'archiviazione e l'elaborazione dei dati. <p>Non è attualmente possibile creare un cluster HDInsight su richiesta che usa Azure Data Lake Store come risorsa di archiviazione. Per archiviare i dati dei risultati dell'elaborazione di HDInsight in un'istanza di Azure Data Lake Store, usare un'attività di copia per copiare i dati dall'archivio BLOB di Azure in Azure Data Lake Store.</p>  | Sì |
| additionalLinkedServiceNames |Specifica account di archiviazione aggiuntivi per il servizio collegato HDInsight in modo che il servizio Data Factory possa registrarli per conto dell'utente. |No |
| osType |Tipo di sistema operativo. I valori consentiti sono: Windows (impostazione predefinita) e Linux |No |
| hcatalogLinkedServiceName |Il nome del servizio collegato di Azure SQL che fa riferimento al database HCatalog. Viene creato il cluster HDInsight su richiesta usando il database SQL di Azure come metastore. |No |

### <a name="json-example"></a>Esempio di JSON
Il codice JSON seguente definisce un servizio collegato HDInsight su richiesta basato su Linux. Il servizio Data factory crea automaticamente un cluster HDInsight **basato su Linux** durante l'elaborazione di una sezione di dati. 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "clusterSize": 4,
            "timeToLive": "00:05:00",
            "osType": "linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

Per altre informazioni, vedere l'articolo relativo ai [servizi collegati di calcolo](data-factory-compute-linked-services.md). 

## <a name="existing-azure-hdinsight-cluster"></a>Cluster HDInsight di Azure esistente
È possibile creare un servizio collegato Azure HDInsight per registrare il proprio cluster HDInsight con Data Factory. Su questo servizio collegato è possibile eseguire le attività di trasformazione seguenti:[attività personalizzate .NET ](#net-custom-activity), [attività Hive](#hdinsight-hive-activity), [attività Pig](#hdinsight-pig-activity, [attività MapReduce](#hdinsight-mapreduce-activity), [attività di Hadoop Streaming](#hdinsight-streaming-activityd), [attività Spark](#hdinsight-spark-activity). 

### <a name="linked-service"></a>Servizio collegato
La tabella seguente fornisce le descrizioni delle proprietà usate nella definizione JSON di Azure di un servizio collegato HDInsight di Azure.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su **HDInsight**. |Sì |
| clusterUri |L'URI del cluster HDInsight. |Sì |
| username |Specifica il nome dell'utente da utilizzare per connettersi a un cluster HDInsight esistente. |Sì |
| password |Specifica la password per l'account utente. |Sì |
| linkedServiceName | Nome del servizio collegato all'archiviazione di Azure che fa riferimento all'archiviazione BLOB di Azure usata dal cluster HDInsight. <p>Attualmente non è possibile specificare un servizio collegato di Azure Data Lake Store per questa proprietà. È possibile accedere ai dati in Azure Data Lake Store da script Hive/Pig se il cluster HDInsight dispone di accesso a Data Lake Store. </p>  |Sì |

Per le versioni dei cluster di HDInsight, vedere le [versioni supportate di HDInsight](data-factory-compute-linked-services.md#supported-hdinsight-versions-in-azure-data-factory). 

#### <a name="json-example"></a>Esempio di JSON

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
            "userName": "admin",
            "password": "<password>",
            "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

## <a name="azure-batch"></a>Azure Batch
È possibile creare un servizio collegato di Azure Batch per registrare un pool di Batch di macchine virtuali in una data factory. È possibile eseguire le attività .NET personalizzate utilizzando Batch Azure o Azure HDInsight. Su questo servizio collegato è possibile eseguire un' [attività personalizzata .NET](#net-custom-activity). 

### <a name="linked-service"></a>Servizio collegato
La tabella seguente fornisce le descrizioni delle proprietà usate nella definizione JSON di Azure di un servizio collegato Azure Batch.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su **AzureBatch**. |Sì |
| accountName |Nome dell'account Azure Batch. |Sì |
| accessKey |Chiave di accesso per l'account Azure Batch. |Sì |
| poolName |Nome del pool di macchine virtuali. |Sì |
| linkedServiceName |Nome dello spazio di archiviazione del servizio collegato Azure associato al servizio collegato Azure Batch. Questo servizio collegato viene utilizzato per organizzare i file necessari per eseguire l'attività e archiviare i log di esecuzione dell’attività. |Sì |


#### <a name="json-example"></a>Esempio di JSON

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
        "type": "AzureBatch",
        "typeProperties": {
            "accountName": "<Azure Batch account name>",
            "accessKey": "<Azure Batch account key>",
            "poolName": "<Azure Batch pool name>",
            "linkedServiceName": "<Specify associated storage linked service reference here>"
        }
    }
}
```

## <a name="azure-machine-learning"></a>Azure Machine Learning
Creare un servizio collegato di Azure Machine Learning per registrare un endpoint di punteggio batch Machine Learning in una data factory. Su questo servizio collegato è possibile eseguire due attività di trasformazione dati: [Attività di esecuzione batch di Machine Learning](#machine-learning-batch-execution-activity), [Attività della risorsa di aggiornamento di Machine Learning](#machine-learning-update-resource-activity). 

### <a name="linked-service"></a>Servizio collegato
La tabella seguente fornisce le descrizioni delle proprietà usate nella definizione JSON di Azure di un servizio collegato Azure Machine Learning.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su **AzureML**. |Sì |
| mlEndpoint |L’URL del batch punteggio. |Sì |
| apiKey |Modello dell'area di lavoro pubblicato di API. |Sì |

#### <a name="json-example"></a>Esempio di JSON

```json
{
    "name": "AzureMLLinkedService",
    "properties": {
        "type": "AzureML",
        "typeProperties": {
            "mlEndpoint": "https://[batch scoring endpoint]/jobs",
            "apiKey": "<apikey>"
        }
    }
}
```

## <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics.
Creare un servizio collegato di **Azure Data Lake Analytics** per collegare un servizio di calcolo di Azure Data Lake Analytics a una Data factory di Azure prima di usare l' [attività U-SQL di Data Lake Analytics](data-factory-usql-activity.md) in una pipeline.

### <a name="linked-service"></a>Servizio collegato

La tabella seguente fornisce le descrizioni delle proprietà usate nella definizione JSON di un servizio collegato Azure Data Lake Analytics. 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| Tipo |La proprietà type deve essere impostata su **AzureDataLakeAnalytics**. |Sì |
| accountName |Nome dell'account di Azure Data Lake Analytics. |Sì |
| dataLakeAnalyticsUri |URI di Azure Data Lake Analytics. |No |
| autorizzazione |Il codice di autorizzazione viene recuperato automaticamente dopo aver fatto clic sul pulsante **Autorizza** nell'editor di Data factory e aver completato l'accesso OAuth. |Sì |
| subscriptionId |ID sottoscrizione di Azure |No (se non specificata, viene usata la sottoscrizione della Data factory). |
| resourceGroupName |Nome del gruppo di risorse di Azure |No (se non specificata, viene usato il gruppo di risorse di Data Factory). |
| sessionId |ID di sessione dalla sessione di autorizzazione OAuth. Ogni ID di sessione è univoco e può essere usato solo una volta. L'ID viene generato automaticamente nell'editor di Data Factory. |Sì |


#### <a name="json-example"></a>Esempio di JSON
Nell'esempio seguente viene fornita la definizione JSON per un servizio collegato di Azure Data Lake Analytics.

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "<account name>",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

## <a name="azure-sql-database"></a>Database SQL di Azure
Si crea un servizio collegato di Azure SQL e lo si utilizza con l’ [Attività di stored procedure](#stored-procedure-activity) per richiamare una procedura stored da una pipeline Data Factory. 

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Database SQL di Azure, impostare il **tipo** di servizio collegato su **AzureSqlDatabase**e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| connectionString |Specificare le informazioni necessarie per connettersi all'istanza di database SQL di Azure per la proprietà connectionString. |Sì |

#### <a name="json-example"></a>Esempio di JSON

```json
{
    "name": "AzureSqlLinkedService",
    "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Vedere l’articolo [Connettore di Azure SQL](data-factory-azure-sql-connector.md#linked-service-properties) per informazioni dettagliate su questo servizio collegato.

## <a name="azure-sql-data-warehouse"></a>Azure SQL Data Warehouse
Si crea un servizio collegato di Azure SQL Data Warehouse e lo si usa con l' [attività di stored procedure](data-factory-stored-proc-activity.md) per richiamare una stored procedure da una pipeline Data Factory. 

### <a name="linked-service"></a>Servizio collegato
Per definire un servizio collegato di Azure SQL Data Warehouse, impostare il **tipo** di servizio collegato su **AzureSqlDW**e specificare le proprietà seguenti nella sezione **typeProperties**:  

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| connectionString |Specificare le informazioni necessarie per connettersi all'istanza di Azure SQL Data Warehouse per la proprietà connectionString. |Sì |

#### <a name="json-example"></a>Esempio di JSON

```json
{
    "name": "AzureSqlDWLinkedService",
    "properties": {
        "type": "AzureSqlDW",
        "typeProperties": {
            "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
    }
}
```

Per altre informazioni, vedere [Connettore Azure SQL Data Warehouse](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties). 

## <a name="sql-server"></a>SQL Server 
Si crea un servizio collegato di SQL Server e lo si usa con l' [attività di stored procedure](data-factory-stored-proc-activity.md) per richiamare una stored procedure da una pipeline Data Factory. 

### <a name="linked-service"></a>Servizio collegato
È stato creato un servizio collegato di tipo **OnPremisesSqlServer** per collegare un database di SQL Server locale a una data factory. La tabella seguente contiene le descrizioni degli elementi JSON specifici per il servizio collegato di SQL Server locale.

La tabella seguente contiene le descrizioni degli elementi JSON specifici del servizio collegato SQL Server.

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| type |La proprietà type deve essere impostata su **OnPremisesSqlServer**. |Sì |
| connectionString |Specificare le informazioni di connectionString necessarie per connettersi al database di SQL Server locale usando l'autenticazione di SQL o Windows. |Sì |
| gatewayName |Nome del gateway che il servizio Data factory deve usare per connettersi al database di SQL Server locale. |Sì |
| username |Specificare il nome utente se si usa l'autenticazione Windows. Esempio: **nomedominio\\nomeutente**. |No |
| password |Specificare la password per l'account utente specificato per il nome utente. |No |

È possibile crittografare le credenziali usando il cmdlet **New-AzureRmDataFactoryEncryptValue** e usarle nella stringa di connessione, come illustrato nell'esempio seguente (proprietà **EncryptedCredential**):  

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```


#### <a name="example-json-for-using-sql-authentication"></a>Esempio: JSON per l'uso dell'autenticazione SQL

```json
{
    "name": "MyOnPremisesSQLDB",
    "properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "connectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=False;User ID=<username>;Password=<password>;",
            "gatewayName": "<gateway name>"
        }
    }
}
```
#### <a name="example-json-for-using-windows-authentication"></a>Esempio: JSON per l'uso dell'autenticazione Windows

Se vengono specificati nome utente e password, il gateway li usa per rappresentare l'account utente specificato per la connessione al database di SQL Server locale. In caso contrario, il gateway si connette a SQL Server direttamente con il contesto di sicurezza del gateway (l'account di avvio).

```json
{
    "Name": " MyOnPremisesSQLDB",
    "Properties": {
        "type": "OnPremisesSqlServer",
        "typeProperties": {
            "ConnectionString": "Data Source=<servername>;Initial Catalog=MarketingCampaigns;Integrated Security=True;",
            "username": "<domain\\username>",
            "password": "<password>",
            "gatewayName": "<gateway name>"
        }
    }
}
```

Per altre informazioni, vedere [Connettore SQL Server](data-factory-sqlserver-connector.md#linked-service-properties).

## <a name="data-transformation-activities"></a>ATTIVITÀ DI TRASFORMAZIONE DEI DATI

Attività | Descrizione
-------- | -----------
[Attività Hive di HDInsight](#hdinsight-hive-activity) | L'attività Hive di HDInsight in una pipeline di Data factory esegue query Hive sul proprio cluster HDInsight o sul cluster HDInsight su richiesta basato su Windows o Linux. 
[Attività Pig di HDInsight](#hdinsight-pig-activity) | L'attività Pig di HDInsight in una pipeline di Data factory esegue query Pig sul proprio cluster HDInsight o sul cluster HDInsight su richiesta basato su Windows o Linux.
[Attività MapReduce di HDInsight](#hdinsight-mapreduce-activity) | L'attività HDInsight MapReduce in una pipeline di Data Factory esegue i programmi di MapReduce nei cluster HDInsight personalizzati o su richiesta basati su Windows/Linux.
[Attività di streaming di HDInsight](#hdinsight-streaming-activity) | L'attività HDInsight Streaming Activity in una pipeline di Data Factory esegue i programmi di Hadoop Streaming nei cluster HDInsight personalizzati o su richiesta basati su Windows/Linux.
[Attività HDInsight Spark](#hdinsight-spark-activity) | L'attività Spark di HDInsight in una pipeline di Data Factory esegue programmi Spark nel cluster HDInsight personale. 
[Attività di esecuzione batch di Machine Learning](#machine-learning-batch-execution-activity) | Azure Data Factory consente di creare facilmente pipeline che usano un servizio Web pubblicato di Azure Machine Learning per l'analisi predittiva. Con Attività di esecuzione Batch in una pipeline di Azure Data Factory è possibile richiamare un servizio Web di Machine Learning per eseguire stime dei dati in batch. 
[Attività della risorsa di aggiornamento di Machine Learning](#machine-learning-update-resource-activity) | Nel corso del tempo è necessario ripetere il training dei modelli predittivi negli esperimenti di assegnazione dei punteggi di Machine Learning usando nuovi set di dati di input. Una volta ripetuto il training, aggiornare il servizio Web di assegnazione dei punteggi con il modello Machine Learning di cui è stato ripetuto il training. È possibile usare l'attività di aggiornamento risorse per aggiornare il servizio Web con il nuovo modello sottoposto a training.
[Attività stored procedure](#stored-procedure-activity) | È possibile usare l'attività stored procedure in una pipeline di Data Factory per richiamare una stored procedure in uno dei seguenti archivi dati: database SQL di Azure, Azure SQL Data Warehouse, database di SQL Server in azienda o in una macchina virtuale di Azure. 
[Attività U-SQL di Data Lake Analytics](#data-lake-analytics-u-sql-activity) | L'attività U-SQL di Data Lake Analytics esegue uno script U-SQL in un cluster di Azure Data Lake Analytics.  
[Attività personalizzata .NET](#net-custom-activity) | Se è necessario trasformare i dati in una modalità non supportata da Data Factory, è possibile creare un'attività personalizzata contenente la logica di elaborazione dei dati richiesta e usarla nella pipeline. È possibile configurare l'attività .NET personalizzata da eseguire usando il servizio Azure Batch o un cluster Azure HDInsight. 

     
## <a name="hdinsight-hive-activity"></a>Attività Hive di HDInsight
In una definizione JSON di attività Hive è possibile specificare le proprietà seguenti. Il tipo di proprietà dell'attività deve essere impostato su **HDInsightHive**. È necessario creare innanzitutto un servizio collegato di HDInsight e quindi specificarne il nome come valore della proprietà **linkedServiceName**. Quando si imposta il tipo di attività HDInsightHive vengono le proprietà seguenti sono supportate nella sezione **typeProperties**:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| script |Specificare lo script Hive inline |No |
| script path |Archiviare lo script Hive in un archivio BLOB di Azure e immettere il percorso del file. Usare la proprietà "script" o "scriptPath". Non è possibile usare entrambe le proprietà. Il nome del file distingue tra maiuscole e minuscole. |No |
| defines |Specificare i parametri come coppie chiave/valore per fare riferimento ad essi nello script Hive usando "hiveconf" |No |

Questi tipi di proprietà sono specifiche per l'attività Hive. Altre proprietà (esterne alla sezione typeProperties) sono supportate per tutte le attività.   

### <a name="json-example"></a>Esempio di JSON
Il codice JSON seguente definisce un'attività Hive di HDInsight in una pipeline.  

```json
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```

Per altre informazioni, vedere [Attività Hive](data-factory-hive-activity.md). 

## <a name="hdinsight-pig-activity"></a>Attività Pig di HDInsight
In una definizione JSON di attività Pig è possibile specificare le proprietà seguenti. Il tipo di proprietà dell'attività deve essere impostato su **HDInsightPig**. È necessario creare innanzitutto un servizio collegato di HDInsight e quindi specificarne il nome come valore della proprietà **linkedServiceName**. Quando il tipo di attività è impostato su HDInsightPig, nella sezione **typeProperties** sono supportate le proprietà seguenti: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| script |Specificare lo script Pig inline |No |
| script path |Archiviare lo script Pig in un archivio BLOB di Azure e immettere il percorso del file. Usare la proprietà "script" o "scriptPath". Non è possibile usare entrambe le proprietà. Il nome del file distingue tra maiuscole e minuscole. |No |
| defines |Specificare i parametri come coppie chiave/valore per fare riferimento ad essi nello script Pig |No |

Questi tipi di proprietà sono specifiche per l'attività Pig. Altre proprietà (esterne alla sezione typeProperties) sono supportate per tutte le attività.   

### <a name="json-example"></a>Esempio di JSON

```json
{
    "name": "HiveActivitySamplePipeline",
      "properties": {
    "activities": [
        {
            "name": "Pig Activity",
            "description": "description",
            "type": "HDInsightPig",
            "inputs": [
                  {
                    "name": "input tables"
                  }
            ],
            "outputs": [
                  {
                    "name": "output tables"
                  }
            ],
            "linkedServiceName": "MyHDInsightLinkedService",
            "typeProperties": {
                  "script": "Pig script",
                  "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                  "defines": {
                    "param1": "param1Value"
                  }
            },
               "scheduler": {
                  "frequency": "Day",
                  "interval": 1
            }
          }
    ]
  }
}
```

Per altre informazioni, vedere [Attività Pig](#data-factory-pig-activity.md). 

## <a name="hdinsight-mapreduce-activity"></a>Attività MapReduce di HDInsight
In una definizione JSON di attività MapReduce è possibile specificare le proprietà seguenti. Il tipo di proprietà dell'attività deve essere impostato su **HDInsightMapReduce**. È necessario creare innanzitutto un servizio collegato di HDInsight e quindi specificarne il nome come valore della proprietà **linkedServiceName**. Quando il tipo di attività è impostato su HDInsightMapReduce, nella sezione **typeProperties** sono supportate le proprietà seguenti: 

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| jarLinkedService | Nome del servizio collegato di Archiviazione di Azure che contiene il file JAR. | Sì |
| jarFilePath | Percorso del file JAR nell'archiviazione di Azure. | Sì | 
| className | Nome della classe principale nel file JAR. | Sì | 
| arguments | Un elenco di argomenti delimitati da virgole per il programma MapReduce. In fase di esecuzione, vengono visualizzati alcuni argomenti aggiuntivi (ad esempio: mapreduce.job.tags) dal framework di MapReduce. Per differenziare gli argomenti con gli argomenti di MapReduce, è consigliabile usare sia l'opzione che il valore come argomenti, come illustrato nell'esempio seguente (- s, --input - output e così via sono opzioni immediatamente seguite dai valori). | No | 

### <a name="json-example"></a>Esempio di JSON

```json
{
    "name": "MahoutMapReduceSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calculates an Item Similarity Matrix to determine the similarity between two items",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": ["-s", "SIMILARITY_LOGLIKELIHOOD", "--input", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input", "--output", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/", "--maxSimilaritiesPerItem", "500", "--tempDir", "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"]
                },
                "inputs": [
                    {
                        "name": "MahoutInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "MahoutOutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "MahoutActivity",
                "description": "Custom Map Reduce to generate Mahout result",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00",
        "end": "2017-01-04T00:00:00"
    }
}
```

Per altre informazioni, vedere [Attività MapReduce](data-factory-map-reduce.md). 

## <a name="hdinsight-streaming-activity"></a>Attività di streaming di HDInsight
In una definizione JSON di attività Hadoop Streaming è possibile specificare le proprietà seguenti. Il tipo di proprietà dell'attività deve essere impostato su **HDInsightStreaming**. È necessario creare innanzitutto un servizio collegato di HDInsight e quindi specificarne il nome come valore della proprietà **linkedServiceName**. Quando il tipo di attività è impostato su HDInsightStreaming, nella sezione **typeProperties** sono supportate le proprietà seguenti: 

| Proprietà | Descrizione | 
| --- | --- |
| mapper | Nome del mapper eseguibile. Nell'esempio cat.exe è l'eseguibile del mapper.| 
| reducer | Nome del reducer eseguibile. Nell'esempio wc.exe è l'eseguibile del mapper. | 
| input | File di input (incluso percorso) del mapper. Nell'esempio "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt" adfsample è il contenitore BLOB, example/data/Gutenberg è la cartella e davinci.txt è il BLOB. |
| output | File di output (incluso percorso) del reducer. L'output del processo di Hadoop Streaming viene scritto nel percorso specificato per questa proprietà. |
| filePaths | Percorsi dei file eseguibili del mapper e del reducer. Nell'esempio: "adfsample/example/apps/wc.exe", adfsample è il contenitore BLOB, example/apps è la cartella e wc.exe è l'eseguibile. | 
| fileLinkedService | Servizio collegato di Archiviazione di Azure che rappresenta l'archivio di Azure contenente i file specificati nella sezione filePaths. | 
| arguments | Un elenco di argomenti delimitati da virgole per il programma MapReduce. In fase di esecuzione, vengono visualizzati alcuni argomenti aggiuntivi (ad esempio: mapreduce.job.tags) dal framework di MapReduce. Per differenziare gli argomenti con gli argomenti di MapReduce, è consigliabile usare sia l'opzione che il valore come argomenti, come illustrato nell'esempio seguente (- s, --input - output e così via sono opzioni immediatamente seguite dai valori). | 
| getDebugInfo | Un elemento facoltativo. Quando viene impostata su Failure, i log vengono scaricati solo in caso di errore. Quando viene impostata su All, i log vengono sempre scaricati indipendentemente dallo stato dell'esecuzione. | 

> [!NOTE]
> È necessario specificare un set di dati di output per l'attività di Hadoop Streaming per la proprietà **output** . Questo set di dati fittizio è necessario per la pianificazione della pipeline (oraria, giornaliera e così via). Se l'attività non accetta un input, è possibile evitare di specificare il set di dati di input dell'attività per la proprietà **input**.  

## <a name="json-example"></a>Esempio di JSON

```json
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": ["<nameofthecluster>/example/apps/wc.exe","<nameofthecluster>/example/apps/cat.exe"],
                    "fileLinkedService": "StorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00",
        "end": "2014-01-05T00:00:00"
    }
}
```

Per altre informazioni, vedere l'argomento relativo all'[attività Hadoop Streaming](data-factory-hadoop-streaming-activity.md). 

## <a name="hdinsight-spark-activity"></a>Attività Spark di HDInsight
In una definizione JSON di attività Spark è possibile specificare le proprietà seguenti. Il tipo di proprietà dell'attività deve essere impostato su **HDInsightSpark**. È necessario creare innanzitutto un servizio collegato di HDInsight e quindi specificarne il nome come valore della proprietà **linkedServiceName**. Quando il tipo di attività è impostato su HDInsightSpark, nella sezione **typeProperties** sono supportate le proprietà seguenti: 

| Proprietà | Descrizione | Obbligatorio |
| -------- | ----------- | -------- |
| rootPath | Contenitore BLOB di Azure e cartella che contiene il file Spark. Il nome del file distingue tra maiuscole e minuscole. | Sì |
| entryFilePath | Percorso relativo alla cartella radice del pacchetto/codice Spark. | Sì |
| className | Classe principale Java/Spark dell'applicazione | No | 
| arguments | Elenco di argomenti della riga di comando del programma Spark. | No | 
| proxyUser | Account utente da rappresentare per eseguire il programma Spark | No | 
| sparkConfig | Proprietà di configurazione di Spark. | No | 
| getDebugInfo | Specifica quando i file di log di Spark vengono copiati nell'archiviazione di Azure usata dal cluster HDInsight (o) specificata da sparkJobLinkedService. Valori consentiti: None, Always o Failure. Valore predefinito: None. | No | 
| sparkJobLinkedService | Il servizio collegato di archiviazione di Azure che contiene il file di processo, le dipendenze e i log di Spark.  Se non si specifica un valore per questa proprietà, viene usato lo spazio di archiviazione associato al cluster HDInsight. | No |

### <a name="json-example"></a>Esempio di JSON

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-05T00:00:00",
        "end": "2017-02-06T00:00:00"
    }
}
```
Tenere presente quanto segue: 

- La proprietà **type** è impostata su **HDInsightSpark**.
- Il **rootPath** è impostato su **adfspark\\pyFiles**, dove adfspark è il contenitore BLOB di Azure e pyFiles è la cartella di file nel contenitore. In questo esempio, l'archivio BLOB di Azure è quello associato al cluster Spark. È possibile caricare il file in un archivio di Azure diverso. In tal caso, creare un servizio collegato Archiviazione di Azure per collegare l'account di archiviazione alla data factory. Quindi, specificare il nome del servizio collegato come valore per la proprietà **sparkJobLinkedService**. Vedere [Proprietà dell'attività Spark](#spark-activity-properties) per informazioni dettagliate su questa e altre proprietà supportate dall'attività Spark.
- La proprietà **entryFilePath** è impostata su **test.py**, ovvero il file python. 
- La proprietà **getDebugInfo** è impostata su **Sempre** e indica che i file di log vengono sempre generati (con esito positivo o negativo).    

    > [!IMPORTANT]
    > Non è consigliabile impostare questa proprietà su Sempre in un ambiente di produzione a meno che non si stia tentando di risolvere un problema. 
- La sezione **outputs** presenta un set di dati di output. Anche se il programma Spark non genera alcun output, è necessario specificare un set di dati di output. Il set di dati di output è ciò su cui si basa la pianificazione della pipeline (oraria, giornaliera e così via).

Per altre informazioni sull'attività, vedere l'argomento relativo all'[attività Spark](data-factory-spark.md).  

## <a name="machine-learning-batch-execution-activity"></a>Attività di esecuzione batch di Machine Learning
In una definizione JSON di attività di esecuzione batch di Machine Learning è possibile specificare le proprietà seguenti. Il tipo di proprietà dell'attività deve essere impostato su **AzureMLBatchExecution**. È necessario creare innanzitutto un servizio collegato di Azure Machine Learning e quindi specificare il nome come valore della proprietà **linkedServiceName**. Quando il tipo di attività è impostato su AzureMLBatchExecution, nella sezione **typeProperties** sono supportate le proprietà seguenti:

Proprietà | Descrizione | Obbligatorio 
-------- | ----------- | --------
webServiceInput | Il set di dati da passare come input del servizio Web di Azure Machine Learning. Questo set di dati deve essere incluso anche nella sezione inputs dell'attività. |Usare webServiceInput o webServiceInputs. | 
webServiceInputs | I set di dati specifici da passare come input del servizio Web di Azure Machine Learning. Se il servizio Web accetta più input, usare la proprietà webServiceInputs invece di webServiceInput. Includere anche i set di dati a cui **webServiceInputs** fa riferimento negli **input** dell'attività. | Usare webServiceInput o webServiceInputs. | 
webServiceOutputs | I set di dati assegnati come output del servizio Web Azure Machine Learning. Il servizio Web restituisce i dati di output in questo set di dati. | Sì | 
globalParameters | Specificare i valori dei parametri del servizio Web in questa sezione. | No | 

### <a name="json-example"></a>Esempio di JSON
In questo esempio, l'attività ha il set di dati **MLSqlInput** come input e **MLSqlOutput** come output. Il set di dati **MLSqlInput** viene passato come input al servizio Web usando la proprietà JSON **webServiceInput**. Il set di dati **MLSqlOutput** viene passato come output al servizio Web usando la proprietà JSON **webServiceOutputs**. 

```json
{
   "name": "MLWithSqlReaderSqlWriter",
   "properties": {
      "description": "Azure ML model with sql azure reader/writer",
      "activities": [{
         "name": "MLSqlReaderSqlWriterActivity",
         "type": "AzureMLBatchExecution",
         "description": "test",
         "inputs": [ { "name": "MLSqlInput" }],
         "outputs": [ { "name": "MLSqlOutput" } ],
         "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
         "typeProperties":
         {
            "webServiceInput": "MLSqlInput",
            "webServiceOutputs": {
               "output1": "MLSqlOutput"
            },
            "globalParameters": {
               "Database server name": "<myserver>.database.windows.net",
               "Database name": "<database>",
               "Server user account name": "<user name>",
               "Server user account password": "<password>"
            }              
         },
         "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
         }
      }],
      "start": "2016-02-13T00:00:00",
       "end": "2016-02-14T00:00:00"
   }
}
```

Nell'esempio JSON, il servizio Web di Azure Machine Learning distribuito usa un modulo Reader e un modulo Writer per leggere e scrivere i dati da e in un database SQL di Azure. Il servizio Web espone i quattro parametri seguenti: Database server name, Database name, Server user account name e Server user account password.

> [!NOTE]
> Possono essere passati come parametri per il servizio Web solo input e output dell'attività AzureMLBatchExecution. Nel precedente snippet JSON, ad esempio, MLSqlInput è un input per l'attività AzureMLBatchExecution e viene passato come input al servizio Web tramite il parametro webServiceInput.

## <a name="machine-learning-update-resource-activity"></a>Attività della risorsa di aggiornamento di Machine Learning
In una definizione JSON di attività della risorsa di aggiornamento di Machine Learning è possibile specificare le proprietà seguenti. Il tipo di proprietà dell'attività deve essere impostato su **AzureMLUpdateResource**. È necessario creare innanzitutto un servizio collegato di Azure Machine Learning e quindi specificare il nome come valore della proprietà **linkedServiceName**. Quando il tipo di attività è impostato su AzureMLUpdateResource, nella sezione **typeProperties** sono supportate le proprietà seguenti:

Proprietà | Descrizione | Obbligatorio 
-------- | ----------- | --------
trainedModelName | Nome del modello sottoposto nuovamente a training. | Sì |  
trainedModelDatasetName | Set di dati che punta al file iLearner restituito dall'operazione di ripetizione del training. | Sì | 

### <a name="json-example"></a>Esempio di JSON
La pipeline include due attività: **AzureMLBatchExecution** e **AzureMLUpdateResource**. Attività di esecuzione batch di Azure ML accetta i dati di training come input e genera il file con estensione iLearner come output. L'attività richiama il servizio Web di training, l'esperimento di training esposto come servizio Web, con i dati di training di input e riceve il file iLearner dal servizio Web. placeholderBlob è solo un set di dati di output fittizio richiesto dal servizio Data factory di Azure per eseguire la pipeline.


```json
{
    "name": "pipeline",
    "properties": {
        "activities": [
            {
                "name": "retraining",
                "type": "AzureMLBatchExecution",
                "inputs": [
                    {
                        "name": "trainingData"
                    }
                ],
                "outputs": [
                    {
                        "name": "trainedModelBlob"
                    }
                ],
                "typeProperties": {
                    "webServiceInput": "trainingData",
                    "webServiceOutputs": {
                        "output1": "trainedModelBlob"
                    }              
                 },
                "linkedServiceName": "trainingEndpoint",
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1,
                    "timeout": "02:00:00"
                }
            },
            {
                "type": "AzureMLUpdateResource",
                "typeProperties": {
                    "trainedModelName": "trained model",
                    "trainedModelDatasetName" :  "trainedModelBlob"
                },
                "inputs": [{ "name": "trainedModelBlob" }],
                "outputs": [{ "name": "placeholderBlob" }],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "AzureML Update Resource",
                "linkedServiceName": "updatableScoringEndpoint2"
            }
        ],
        "start": "2016-02-13T00:00:00",
        "end": "2016-02-14T00:00:00"
    }
}
```

## <a name="data-lake-analytics-u-sql-activity"></a>Attività U-SQL di Data Lake Analytics
In una definizione JSON di attività U-SQL è possibile specificare le proprietà seguenti. Il tipo di proprietà dell'attività deve essere impostato su **DataLakeAnalyticsU-SQL**. È necessario creare innanzitutto un servizio collegato di Azure Data Lake Analytics e quindi specificare il nome come valore della proprietà **linkedServiceName**. Quando il tipo di attività è impostato su DataLakeAnalyticsU-SQL, nella sezione **typeProperties** sono supportate le proprietà seguenti: 

| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| scriptPath |Percorso della cartella contenente lo script U-SQL. Il nome del file distingue tra maiuscole e minuscole. |No (se si usa uno script) |
| scriptLinkedService |Servizi collegati che collegano la risorsa di archiviazione contenente lo script alla Data factory |No (se si usa uno script) |
| script |Specificare lo script inline anziché scriptPath e scriptLinkedService. Ad esempio: "script": "CREATE DATABASE test". |No (se si usano le proprietà scriptPath e scriptLinkedService) |
| degreeOfParallelism |Il numero massimo di nodi usati contemporaneamente per eseguire il processo. |No |
| priority |Determina quali processi rispetto a tutti gli altri disponibili nella coda devono essere selezionati per essere eseguiti per primi. Più è basso il numero, maggiore sarà la priorità. |No |
| parameters |Parametri per lo script U-SQL |No |

### <a name="json-example"></a>Esempio di JSON

```json
{
    "name": "ComputeEventsByRegionPipeline",
    "properties": {
        "description": "This pipeline computes events for en-gb locale and date less than Feb 19, 2012.",
        "activities": 
        [
            {
                "type": "DataLakeAnalyticsU-SQL",
                "typeProperties": {
                    "scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
                    "scriptLinkedService": "StorageLinkedService",
                    "degreeOfParallelism": 3,
                    "priority": 100,
                    "parameters": {
                        "in": "/datalake/input/SearchLog.tsv",
                        "out": "/datalake/output/Result.tsv"
                    }
                },
                "inputs": [
                    {
                        "name": "DataLakeTable"
                    }
                ],
                "outputs": 
                [
                    {
                        "name": "EventsByRegionTable"
                    }
                ],
                "policy": {
                    "timeout": "06:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "EventsByRegion",
                "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
            }
        ],
        "start": "2015-08-08T00:00:00",
        "end": "2015-08-08T01:00:00",
        "isPaused": false
    }
}
```

Per altre informazioni, vedere [Attività Data Lake Analytics U-SQL](data-factory-usql-activity.md). 

## <a name="stored-procedure-activity"></a>Attività stored procedure
In una definizione JSON di attività stored procedure è possibile specificare le proprietà seguenti. Il tipo di proprietà dell'attività deve essere impostato su **SqlServerStoredProcedure**. È necessario creare uno dei seguenti servizi collegati e specificare il nome del servizio collegato come valore della proprietà **linkedServiceName**:

- SQL Server 
- Database SQL di Azure
- Azure SQL Data Warehouse

Quando il tipo di attività è impostato su SqlServerStoredProcedure, nella sezione **typeProperties** sono supportate le proprietà seguenti:

| Proprietà | Descrizione | Obbligatorio |
| --- | --- | --- |
| storedProcedureName |Specificare il nome della stored procedure nel database SQL di Azure o Azure SQL Data Warehouse rappresentato dal servizio collegato che usa la tabella di output. |Sì |
| storedProcedureParameters |Specificare i valori dei parametri della stored procedure. Se è necessario passare null per un parametro, usare la sintassi "param1": null (tutte lettere minuscole). Vedere l'esempio seguente per informazioni sull'uso di questa proprietà. |No |

Se si specifica un set di dati di input, questo dovrà essere disponibile (in stato 'Ready') per l'esecuzione dell'attività della stored procedure. Il set di dati di input non può essere usato nella stored procedure come parametro. Viene usato solo per verificare la dipendenza prima di iniziare l'attività della stored procedure. È necessario specificare un set di dati di output per un'attività della stored procedure. 

Il set di dati di output specifica la **pianificazione** per le attività della stored procedure (ogni ora, ogni settimana, ogni mese e così via). Il set di dati di output deve usare un **servizio collegato** che faccia riferimento a un database SQL di Azure, a un Azure SQL Data Warehouse o a un database SQL Server in cui si vuole che venga eseguita la stored procedure. Il set di dati di output può essere usato per passare il risultato della stored procedure per la successiva elaborazione da parte di un'altra attività ([concatenamento di attività](data-factory-scheduling-and-execution.md##multiple-activities-in-a-pipeline)) nella pipeline. Data Factory non scrive tuttavia automaticamente l'output di una stored procedure in questo set di dati. È la stored procedure a scrivere dati in una tabella SQL cui punta il set di dati di output. In alcuni casi, il set di dati di output può essere un **set di dati fittizio**che viene usato solo per specificare la pianificazione per l'esecuzione dell'attività della stored procedure.  

### <a name="json-example"></a>Esempio di JSON

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [{ "name": "sprocsampleout" }],
                "name": "SprocActivitySample"
            }
        ],
         "start": "2016-08-02T00:00:00",
         "end": "2016-08-02T05:00:00",
        "isPaused": false
    }
}
```

Per altre informazioni, vedere [Attività stored procedure](data-factory-stored-proc-activity.md). 

## <a name="net-custom-activity"></a>Attività personalizzata .NET
In una definizione JSON di attività personalizzata .NET è possibile specificare le proprietà seguenti. Il tipo di proprietà dell'attività deve essere impostato su **DotNetActivity**. È necessario creare un servizio collegato Azure HDInsight o Azure Batch e specificare il nome del servizio collegato come valore della proprietà **linkedServiceName**. Quando il tipo di attività è impostato su DotNetActivity, nella sezione **typeProperties** sono supportate le proprietà seguenti:
 
| Proprietà | Descrizione | Obbligatorio |
|:--- |:--- |:--- |
| AssemblyName | Nome dell'assembly. Nell'esempio è: **MyDotnetActivity.dll**. | Sì |
| EntryPoint |Nome della classe che implementa l'interfaccia IDotNetActivity. Nell'esempio è: **MyDotNetActivityNS.MyDotNetActivity** dove MyDotNetActivityNS è lo spazio dei nomi e MyDotNetActivity è la classe.  | Sì | 
| PackageLinkedService | None del servizio collegato di Archiviazione di Azure che punta all'archivio BLOB contenente il file con estensione zip dell'attività personalizzata. Nell'esempio è: **AzureStorageLinkedService**.| Sì |
| PackageFile | Nome del file con estensione zip. Nell'esempio è: **customactivitycontainer/MyDotNetActivity.zip**. | Sì |
| extendedProperties | Proprietà estese che è possibile definire e passare al codice .NET. In questo esempio, la variabile **SliceStart** è impostata su un valore basato sulla variabile di sistema SliceStart. | No | 

### <a name="json-example"></a>Esempio di JSON

```json
{
  "name": "ADFTutorialPipelineCustom",
  "properties": {
    "description": "Use custom activity",
    "activities": [
      {
        "Name": "MyDotNetActivity",
        "Type": "DotNetActivity",
        "Inputs": [
          {
            "Name": "InputDataset"
          }
        ],
        "Outputs": [
          {
            "Name": "OutputDataset"
          }
        ],
        "LinkedServiceName": "AzureBatchLinkedService",
        "typeProperties": {
          "AssemblyName": "MyDotNetActivity.dll",
          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
          "PackageLinkedService": "AzureStorageLinkedService",
          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
          "extendedProperties": {
            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
          }
        },
        "Policy": {
          "Concurrency": 2,
          "ExecutionPriorityOrder": "OldestFirst",
          "Retry": 3,
          "Timeout": "00:30:00",
          "Delay": "00:00:00"
        }
      }
    ],
    "start": "2016-11-16T00:00:00",
    "end": "2016-11-16T05:00:00",
    "isPaused": false
  }
}
```

Per altre informazioni, vedere l'articolo relativo all'[uso delle attività personalizzate in Data Factory](data-factory-use-custom-activities.md). 

## <a name="next-steps"></a>Passaggi successivi
Vedere le esercitazioni seguenti: 

- [Esercitazione: Creare una pipeline con un'attività di copia](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Esercitazione: Creare una pipeline con un'attività di Hive](data-factory-build-your-first-pipeline-using-editor.md)
