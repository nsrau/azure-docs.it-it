---
title: Pianificazione ed esecuzione con Data Factory | Documentazione Microsoft
description: Informazioni sugli aspetti di pianificazione ed esecuzione del modello applicativo di Azure Data Factory.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 088a83df-4d1b-4ac1-afb3-0787a9bd1ca5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/24/2017
ms.author: spelluru
ms.translationtype: Human Translation
ms.sourcegitcommit: a3ca1527eee068e952f81f6629d7160803b3f45a
ms.openlocfilehash: 861fcd7160fcab025909b60086f1a5a8a68f33fb
ms.contentlocale: it-it
ms.lasthandoff: 04/27/2017


---
# <a name="data-factory-scheduling-and-execution"></a>Pianificazione ed esecuzione con Data Factory
Questo articolo descrive gli aspetti di pianificazione ed esecuzione del modello applicativo Azure Data Factory. L'articolo presuppone la conoscenza dei concetti di base del modello applicativo di Data Factory: attività, pipeline, servizi collegati e set di dati. Per i concetti di base di Data Factory di Azure, vedere gli articoli seguenti:

* [Introduzione al servizio Data factory](data-factory-introduction.md)
* [Pipeline](data-factory-create-pipelines.md)
* [Set di dati](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Ora di inizio e ora di fine della pipeline
Una pipeline è attiva solo tra l'ora di **inizio** e l'ora di **fine**. Non viene eseguita prima dell'ora di inizio o dopo l'ora di fine. Se messa in pausa, la pipeline non viene eseguita, indipendentemente dall'ora di inizio e di fine. Per essere eseguita, una pipeline non deve essere in pausa. Queste impostazioni (inizio, fine, in pausa) sono disponibili nella definizione di pipeline: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Per altre informazioni su queste proprietà, vedere l'articolo [Creare pipeline](data-factory-create-pipelines.md). 


## <a name="specify-schedule-for-an-activity"></a>Pianificare l'esecuzione di un'attività
Non è la pipeline a essere eseguita, ma sono le attività della pipeline a essere eseguite nel contesto generale della pipeline. La sezione **Utilità di pianificazione** del file JSON dell'attività consente di specificare una pianificazione ricorrente per l'attività. Ad esempio, è possibile pianificare l'attività perché venga eseguita a cadenza oraria, come riportato di seguito:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Come illustrato nel diagramma seguente, pianificando un'attività si crea una serie di finestre a cascata con l'ora di inizio e di fine della pipeline. Le finestre a cascata sono costituite da una serie di intervalli temporali di dimensioni fisse, contigui e non sovrapposti. Queste finestre logiche a cascata per un'attività vengono denominate **finestre attività**.

![Esempio di utilità di pianificazione delle attività](media/data-factory-scheduling-and-execution/scheduler-example.png)

La proprietà **scheduler** per un'attività è facoltativa. Se specificata, questa proprietà deve corrispondere alla cadenza indicata nella definizione del set di dati di output per l'attività. Attualmente, è il set di dati di output a determinare la pianificazione. Pertanto, anche se l'attività non genera alcun output, è necessario specificare un set di dati di output. 

## <a name="specify-schedule-for-a-dataset"></a>Specificare una pianificazione per un set di dati
Un'attività in una pipeline di Data Factory può non avere alcun **set di dati** di input o può averne più di uno e generare uno o più set di dati di output. Per un'attività è possibile specificare la cadenza con cui sono resi disponibili i dati di input o sono generati i dati di output usando la sezione **availability** nelle definizioni dei set di dati. 

La proprietà **frequency** nella sezione **availability** specifica l'unità di tempo. I valori consentiti per la frequenza sono: Minute, Hour, Day, Week, e Month (minuto, ora, giorno, settimana e mese). La proprietà **interval** nella sezione availability consente di specificare un moltiplicatore di frequenza. Ad esempio: se la frequenza è impostata su Day e interval è impostato su 1 per un set di dati di output, questi saranno prodotti con cadenza giornaliera. Se si specifica frequency come Minute, è consigliabile impostare interval su un valore non inferiore a 15. 

Nell'esempio seguente, i dati di input sono disponibili ogni ora e i dati di output sono generati con cadenza oraria (`"frequency": "Hour", "interval": 1`). 

**Set di dati di input:** 

```json
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
```


**Set di dati di output**

```json
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
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Attualmente, **è il set di dati di output a determinare la pianificazione**. In altre parole, la pianificazione specificata per il set di dati di output viene usata per eseguire un'attività in fase di runtime. Pertanto, anche se l'attività non genera alcun output, è necessario specificare un set di dati di output. Se l'attività non richiede input, è possibile ignorare la creazione del set di dati di input. 

Nella definizione di pipeline seguente, la proprietà **scheduler** viene usata per specificare una pianificazione per l'attività. Questa proprietà è facoltativa. Attualmente, la pianificazione dell'attività deve corrispondere alla pianificazione specificata per il set di dati di output.
 
```json
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
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

In questo esempio, l'attività viene eseguita ogni ora tra l'ora di inizio e quella di fine della pipeline. I dati di output sono prodotti con cadenza oraria per finestre di tre ore (08:00 - 09:00, 09:00 - 10:00 e 10:00 - 11:00). 

Ogni unità di dati usata o prodotta da un'esecuzione di attività prende il nome di **sezione di dati**. Nel diagramma seguente viene illustrato un esempio di attività con un set di dati di input e un set di dati di output: 

![Utilità di pianificazione della disponibilità](./media/data-factory-scheduling-and-execution/availability-scheduler.png)

Il diagramma illustra le sezioni di dati orarie per i set di dati di input e di output. Il diagramma mostra tre sezioni di input che sono pronte per l'elaborazione. L'attività 10-11 AM è in corso e produce la sezione di output 10-11 AM. 

È possibile accedere all'intervallo di tempo associato alla sezione corrente nel set di dati JSON usando le variabili [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) e [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). Allo stesso modo, è possibile accedere all'intervallo di tempo associato a una finestra di attività usando WindowStart e WindowEnd. La pianificazione di un'attività deve corrispondere alla pianificazione del set di dati di output per l'attività stessa. Di conseguenza, i valori SliceStart e SliceEnd sono rispettivamente uguali ai valori WindowStart e WindowEnd. Per altre informazioni su queste variabili, vedere gli articoli [Funzioni e variabili di sistema di Data Factory](data-factory-functions-variables.md#data-factory-system-variables).  

È possibile usare queste variabili per scopi diversi nel file JSON dell'attività. Ad esempio, è possibile usarle per selezionare i dati dai set di dati di input e output che rappresentano i dati della serie temporale (ad esempio, dalle 08:00 alle 09:00). L'esempio usa anche **WindowStart** e **WindowEnd** per selezionare i dati pertinenti per l'esecuzione di un'attività e copiarli in un BLOB con un elemento **folderPath** appropriato. All'oggetto **folderPath** sono applicati parametri per avere una cartella separata per ogni ora.  

Nell'esempio precedente, la pianificazione specificata per i set di dati di input e di output è la stessa (cadenza oraria). Se il set di dati di input per l'attività è disponibile con una frequenza diversa, ad esempio ogni 15 minuti, l'attività che genera il set di dati di output viene eseguita ancora una volta con cadenza oraria, poiché è il set di dati di output a impostare la pianificazione dell'attività. Per altre informazioni, vedere [Modellare i set di dati con frequenze diverse](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Disponibilità e criteri dei set di dati
È stato illustrato l'uso delle proprietà frequency e interval nella sezione availability della definizione di set di dati. Esistono alcune altre proprietà che influiscono sulla pianificazione e l'esecuzione di un'attività. 

### <a name="dataset-availability"></a>Disponibilità dei set di dati 
La tabella seguente descrive le proprietà che è possibile usare nella sezione **availability**:

| Proprietà | Descrizione | Obbligatorio | Default |
| --- | --- | --- | --- |
| frequency |Specifica l'unità di tempo per la produzione di sezioni di set di dati.<br/><br/><b>Frequenza supportata</b>: minuto, ora, giorno, settimana, mese |Sì |ND |
| interval |Specifica un moltiplicatore per la frequenza.<br/><br/>"Intervallo di frequenza x" determina la frequenza con cui viene generata la sezione.<br/><br/>Se è necessario suddividere il set di dati su base oraria, impostare l'opzione <b>Frequenza</b> su <b>Ora</b> e <b>Intervallo</b> su <b>1</b>.<br/><br/><b>Nota</b> : se si specifica frequency come Minute, è consigliabile impostare interval su un valore non inferiore a 15 |Sì |ND |
| style |Specifica se la sezione deve essere generata all'inizio o alla fine dell'intervallo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Se frequency è impostato su Month e style è impostato su EndOfInterval, la sezione viene generata l'ultimo giorno del mese. Se style è impostato su StartOfInterval, la sezione viene generata il primo giorno del mese.<br/><br/>Se l'opzione Frequnza è impostata su Mese e l'opzione Stile è impostata su EndOfInterval, la sezione viene generata l'ultima ora del giorno.<br/><br/>Se frequency è impostato su Hour e style è impostato su EndOfInterval, la sezione viene generata alla fine dell'ora. Ad esempio, una sezione per il periodo 13.00 - 14.00 viene generata alle 14.00. |No |EndOfInterval |
| anchorDateTime |Definisce la posizione assoluta nel tempo usata dall'utilità di pianificazione per calcolare i limiti della sezione del set di dati. <br/><br/><b>Nota:</b> se AnchorDateTime include parti della data più granulari rispetto alla frequenza, le parti più granulari vengono ignorate. <br/><br/>Ad esempio, se l'<b>intervallo</b> è <b>orario</b> (ovvero frequenza: ora e intervallo: 1) e <b>AnchorDateTime</b> contiene <b>minuti e secondi</b>, le parti <b>minuti e secondi</b> di AnchorDateTime vengono ignorate. |No |01/01/0001 |
| offset |Intervallo di tempo in base al quale l'inizio e la fine di tutte le sezioni dei set di dati vengono spostate. <br/><br/><b>Nota:</b> se vengono specificati sia anchorDateTime che offset, il risultato sarà lo spostamento combinato. |No |ND |

### <a name="offset-example"></a>Esempio di offset
Per impostazione predefinita, le sezioni giornaliere (`"frequency": "Day", "interval": 1`) iniziano alle 00:00, mezzanotte, ora UTC. Se, invece, si desidera impostare l'ora di inizio alle 06:00 UTC, impostare l'offset come illustrato nel frammento riportato di seguito: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Esempio di anchorDateTime
Nell'esempio seguente, il set di dati viene prodotto ogni 23 ore. La prima sezione inizia all'ora specificata da anchorDateTime, che è impostato su `2017-04-19T08:00:00` (ora UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Esempio di offset/style
Il seguente set di dati è un set di dati mensile e viene generato il 3 di ogni mese alle ore 08:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00",    
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Criteri di set di dati
Per un set di dati è possibile definire anche un criterio di convalida che specifica in che modo i dati generati dall'esecuzione di una sezione possono essere convalidati prima che siano pronti per l'uso. In questi casi, al termine del processo di esecuzione, lo stato della sezione di output viene impostato su **In attesa** con lo stato secondario impostato su **Convalida**. Al termine della convalida, lo stato viene impostato invece su **Pronto**. Se una sezione di dati è stata correttamente generata ma non ha superato il processo di convalida, le esecuzioni di attività per le sezioni a valle dipendenti da questa sezione non vengono elaborate. [Monitorare e gestire le pipeline](data-factory-monitor-manage-pipelines.md) vengono descritti i vari stati disponibili per le sezioni di dati di Data Factory.

La sezione **policy** nella definizione del set di dati stabilisce i criteri o la condizione che le sezioni del set di dati devono soddisfare. La tabella seguente descrive le proprietà che è possibile usare nella sezione **policy**:

| Nome criterio | Descrizione | Applicato a | Obbligatorio | Default |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Verifica che i dati in un **BLOB di Azure** soddisfino i requisiti relativi alle dimensioni minime (in megabyte). |BLOB di Azure |No |ND |
| minimumRows | Verifica che i dati in un **database SQL di Azure** o in una **tabella di Azure** contengano il numero minimo di righe. |<ul><li>Database SQL di Azure</li><li>tabella di Azure</li></ul> |No |ND |

#### <a name="examples"></a>esempi
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Per altre informazioni sulle proprietà e gli esempi precedenti, vedere l'articolo [Creare i set di dati](data-factory-create-datasets.md). 

## <a name="activity-policies"></a>Criteri di attività
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

Per altre informazioni, vedere l'articolo [Pipeline](data-factory-create-pipelines.md). 

## <a name="parallel-processing-of-data-slices"></a>Elaborazione parallela delle sezioni di dati
È possibile impostare la data di inizio della pipeline nel passato. Così facendo, Data Factory calcolerà automaticamente tutte le sezioni di dati nel passato recuperando le informazioni e ne avvierà l'elaborazione. Ad esempio: si crea una pipeline con data di inizio 01.04.2017 e la data corrente è 10.04.2017. Se il set di dati di output ha cadenza giornaliera, Data Factory inizierà immediatamente l'elaborazione di tutte le sezioni dal 01.04.2017 al 09.04.2017 perché la data di inizio è nel passato. La sezione dal 10.04.2017 non verrà ancora elaborata perché il valore della proprietà di stile nella sezione availability è EndOfInterval per impostazione predefinita. La sezione meno recente verrà elaborata per prima perché il valore predefinito di executionPriorityOrder è OldestFirst. Per una descrizione della proprietà style, vedere la sezione [disponibilità dei set di dati](#dataset-availability). Per una descrizione della sezione executionPriorityOrder, vedere la sezione [criteri di attività](#activity-policies). 

È possibile configurare le sezioni di dati recuperati perché siano eseguite in parallelo, impostando la proprietà **concurrency** nella sezione **policy** dell'attività JSON. Questa proprietà determina il numero di esecuzioni di attività parallele che possono verificarsi in sezioni diverse. Il valore predefinito per questa proprietà è 1. Pertanto, per impostazione predefinita viene elaborata una sola sezione alla volta. Il valore massimo è 10. Se una pipeline deve passare attraverso grandi set di dati disponibili, con un valore di concorrenza maggiore che consente di velocizzare l'elaborazione dei dati. 

## <a name="rerun-a-failed-data-slice"></a>Nuova esecuzione di una sezione di dati non riuscita
Quando si verifica un errore durante l'elaborazione di una sezione di dati, è possibile scoprire perché l'elaborazione di una sezione non è riuscita tramite i pannelli del portale di Azure o l'App Monitoraggio e gestione. Per informazioni dettagliate, vedere [Monitorare e gestire le pipeline con i pannelli del portale di Azure](data-factory-monitor-manage-pipelines.md) o [App di monitoraggio e gestione](data-factory-monitor-manage-app.md).

Si consideri l'esempio seguente che descrive due attività. Activity1 e Activity 2. Activity1 usa una sezione di Dataset1 e genera una sezione di Dataset2, che viene usata come input da Activity2 per produrre una sezione del set di dati finale.

![Sezione non riuscita](./media/data-factory-scheduling-and-execution/failed-slice.png)

Il diagramma illustra che in una delle tre sezioni recenti si è verificato un errore durante la produzione della sezione 9-10 AM per Dataset2. Data Factory tiene automaticamente traccia della dipendenza per il set di dati della serie temporale. Di conseguenza, non viene avviata l'esecuzione dell'attività per la sezione di downstream 9-10 AM.

Gli strumenti di monitoraggio e gestione di Data Factory consentono inoltre di analizzare i log di diagnostica relativi alla sezione con esito negativo e individuare facilmente la causa principale del problema per permetterne la risoluzione. Dopo aver risolto il problema, è possibile avviare facilmente l'esecuzione di attività per generare la sezione non riuscita. Per altre informazioni su come riavviare le esecuzioni o per comprendere le transizioni di stato per le sezioni di dati, vedere [Monitorare e gestire le pipeline con i pannelli del portale di Azure](data-factory-monitor-manage-pipelines.md) o [App di monitoraggio e gestione](data-factory-monitor-manage-app.md).

Dopo che la sezione 9-10 AM di **Dataset2**è stata eseguita nuovamente, Data Factory avvia l'esecuzione della sezione dipendente 9-10 AM nel set di dati finale.

![Nuova esecuzione di una sezione non riuscita](./media/data-factory-scheduling-and-execution/rerun-failed-slice.png)

## <a name="multiple-activities-in-a-pipeline"></a>Attività multiple in una pipeline
È possibile avere più di un'attività in una pipeline. Se si hanno più attività in una pipeline e l'output di un'attività non è l'input di un'altra attività, le attività possono essere eseguite in parallelo se le sezioni di dati di input per le attività sono pronte.

È possibile concatenare due attività, ovvero eseguire un'attività dopo l'altra, impostando il set di dati di output di un'attività come set di dati di input di altre attività. Le attività possono essere nella stessa pipeline o in pipeline diverse. La seconda attività viene eseguita solo quando la prima viene completata correttamente.

Ad esempio, si consideri il caso seguente in cui una pipeline ha due attività:

1. L'attività A1, che richiede il set di dati di input esterno D1 e produce il set di dati di output D2.
2. L'attività A2, che richiede l'input del set di dati D2 e produce il set di dati di output D3.

In questo scenario, le attività A1 e A2 si trovano nella stessa pipeline. L'attività A1 viene eseguita quando i dati esterni sono disponibili e viene raggiunta la frequenza di disponibilità pianificata. L'attività A2 viene eseguita quando le sezioni pianificate di D2 diventano disponibili e viene raggiunta la frequenza di disponibilità pianificata. Se è presente un errore in una delle sezioni del set di dati D2, A2 non verrà eseguita per tale sezione fino a quando non diventa disponibile.

La visualizzazione diagramma con entrambe le attività nella stessa pipeline sarebbe simile al diagramma seguente:

![Concatenamento di attività nella stessa pipeline](./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png)

Come accennato in precedenza, le attività possono trovarsi in pipeline diverse. In questo caso, la visualizzazione diagramma sarebbe simile al diagramma seguente:

![Concatenamento di attività in due pipeline](./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png)

Per un esempio, vedere la sezione [copiare in sequenza](#copy-sequentially) nell'appendice.

## <a name="model-datasets-with-different-frequencies"></a>Modellare i set di dati con frequenze diverse
Negli esempi, la finestra di pianificazione dell'attività e le frequenze relative ai set di dati di input e di output erano identiche. Alcuni scenari richiedono tuttavia la possibilità di generare output a una frequenza diversa da quella degli input. Data Factory supporta la modellazione di questi scenari.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Esempio 1: Generazione di un report di output giornaliero per i dati di input, disponibile ogni ora
Si consideri uno scenario in cui i dati delle misurazioni di input dei sensori sono disponibili ogni ora nell'archiviazione BLOB di Azure. Si intende generare un report aggregato giornaliero con statistiche, ad esempio media, max e min, per il giorno con [attività Hive di Data Factory](data-factory-hive-activity.md).

Ecco come modellare questo scenario con Data Factory:

**Set di dati di input**

I file di input vengono rilasciati ogni ora nella cartella relativa al giorno specificato. Il valore di disponibilità per l'input è impostato su **Hour** (frequency: Hour, interval: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
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
```
**Set di dati di output**

Un file di output viene creato ogni giorno nella cartella relativa al giorno corrispondente. Il valore di disponibilità per l'output è impostato su **Day** (frequency: Day, interval: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
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
```

**Attività: attività Hive in una pipeline**

Lo script Hive riceve le informazioni *DateTime* appropriate sotto forma di parametri che usano la variabile **WindowStart** , come illustrato nel frammento seguente. Lo script Hive usa quindi questa variabile per caricare i dati dall'apposita cartella relativa al giorno ed eseguire l'aggregazione per generare l'output.

```json
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
                    "Month": "$$Text.Format('{0:MM}',WindowStart)",
                    "Day": "$$Text.Format('{0:dd}',WindowStart)"
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
```

Il diagramma seguente illustra lo scenario dal punto di vista della dipendenza dei dati.

![Dipendenza dei dati](./media/data-factory-scheduling-and-execution/data-dependency.png)

Per ogni giorno, la sezione di output dipende dalle 24 sezioni orarie ottenute dal set di dati di input. Data Factory calcola automaticamente queste dipendenze prevedendo le sezioni di dati di input che rientrano nello stesso periodo di tempo della sezione di output da produrre. Se una delle 24 sezioni di input non è disponibile, Data Factory attenderà che la sezione di input sia pronta prima di avviare l'esecuzione dell'attività giornaliera.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Esempio 2: Definizione di una dipendenza con espressioni e funzioni di Data Factory
Si consideri ora un altro scenario Si supponga di avere un'attività Hive che elabora i due set di dati di input. Uno di questi dispone di nuovi dati ogni giorno, mentre l'altro li riceve ogni settimana. Si supponga inoltre di voler eseguire un join tra i due input e produrre un output ogni giorno.

L'approccio semplice, in cui Data Factory determina automaticamente le sezioni di input da elaborare tramite l'allineamento con il periodo di tempo della sezione dei dati di output, non funziona.

È necessario definire un nuovo approccio per ogni esecuzione di attività in cui Data Factory possa usare la sezione di dati dell'ultima settimana per il set di dati di input settimanale. Per implementare questo comportamento, è possibile usare le funzioni di Azure Data Factory, come illustrato nel frammento seguente.

**Input1: BLOB di Azure**

Il primo input è il BLOB di Azure aggiornato ogni giorno.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
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
```

**Input2: BLOB di Azure**

Input2 è il BLOB di Azure aggiornato ogni settimana.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
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
```

**Output: BLOB di Azure**

Un file di output viene creato ogni giorno nella cartella relativa al giorno corrispondente. Il valore di disponibilità per l'output è impostato su **day** (frequency: Day, interval: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
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
```

**Attività: attività Hive in una pipeline**

L'attività Hive usa i due input e genera una sezione di output giornaliera. È possibile specificare che la sezione di output giornaliera dipenda dalla sezione di input della settimana precedente per l'input settimanale procedendo come indicato di seguito.

```json
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
            "Month": "$$Text.Format('{0:MM}',WindowStart)",
            "Day": "$$Text.Format('{0:dd}',WindowStart)"
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
```

Per un elenco di funzioni e variabili di sistema supportate da Data Factory, vedere l'articolo [Funzioni e variabili di sistema di Data Factory](data-factory-functions-variables.md) .

## <a name="appendix"></a>Appendice

### <a name="example-copy-sequentially"></a>Esempio: copiare in sequenza
È possibile eseguire più operazioni di copia l'una dopo l'altra in modo sequenziale o ordinato. Ad esempio, si supponga di avere due attività di copia in una pipeline (CopyActivity1 e CopyActivity2) con i set di dati di input e output seguenti:   

CopyActivity1

Input: Dataset. Output: Dataset2.

CopyActivity2

Input: Dataset2.  Output: Dataset3.

CopyActivity2 viene eseguita solo se l'esecuzione di CopyActivity1 è riuscita e Dataset2 è disponibile.

Ecco la pipeline JSON di esempio:

```json
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
```

Si noti che nell'esempio, il set di dati di output della prima attività di copia (Dataset2) è specificato come input per la seconda attività. La seconda attività viene quindi eseguita solo quando è pronto il set di dati di output dalla prima attività.  

Nell'esempio, CopyActivity2 può avere un input diverso, ad esempio Dataset3, ma è necessario specificare Dataset2 anche come input per CopyActivity2, in modo che l'attività non venga eseguita fino a quando CopyActivity1 non è stata completata. Ad esempio:

CopyActivity1

Input: Dataset1. Output: Dataset2.

CopyActivity2

Input: Dataset3, Dataset2. Output: Dataset4.

```json
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
```

Si noti che nell'esempio vengono specificati due set di dati di input per la seconda attività di copia. Quando si specificano più input, solo il primo set di dati di input viene usato per la copia dei dati e gli altri set di dati vengono usati come dipendenze. L'esecuzione di CopyActivity2 si avvia solo dopo che le seguenti condizioni sono soddisfatte:

* L’esecuzione di CopyActivity1 è riuscita e Dataset2 è disponibile. Questo set di dati non viene usato per la copia dei dati in Dataset4. La sua funzione è semplicemente quella di pianificare la dipendenza per CopyActivity2.   
* Dataset3 è disponibile. Questo set di dati rappresenta i dati che vengono copiati nella destinazione. 
