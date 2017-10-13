---
title: "Creare e pianificare pipeline e concatenare attività in Data Factory | Documentazione Microsoft"
description: Informazioni su come creare una pipeline di dati in Azure Data Factory per spostare e trasformare i dati. Creare un flusso di lavoro basato sui dati per produrre informazioni pronte per l'uso.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 13b137c7-1033-406f-aea7-b66f25b313c0
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2017
ms.author: shlo
robots: noindex
ms.openlocfilehash: 94c820b23a91f3493a0fbc8e1fd38c86d371e985
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="pipelines-and-activities-in-azure-data-factory"></a>Pipeline e attività in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](data-factory-create-pipelines.md)
> * [Versione 2 - Anteprima](../concepts-pipelines-activities.md)

> [!NOTE]
> Questo articolo si applica alla versione 1 del servizio Data Factory, disponibile a livello generale (GA). Se si usa la versione 2 del servizio Data Factory, che è in anteprima, vedere le informazioni sulle [pipeline nella versione 2](../concepts-pipelines-activities.md).

Questo articolo fornisce informazioni sulle pipeline e sulle attività in Azure Data Factory e su come usarle per creare flussi di lavoro completi basati sui dati per gli scenari di elaborazione e trasferimento dei dati.  

> [!NOTE]
> Questo articolo è da leggersi dopo aver consultato l' [Introduzione a Data factory di Azure](data-factory-introduction.md). Se non si ha esperienza diretta nella creazione di data factory, l'[esercitazione sulla trasformazione dei dati](data-factory-build-your-first-pipeline.md) e/o [quella sullo spostamento dei dati](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) può essere utile per comprendere meglio questo articolo.  

## <a name="overview"></a>Panoramica
Una data factory può comprendere una o più pipeline. Una pipeline è un raggruppamento logico di attività che insieme eseguono un compito. Le attività in una pipeline definiscono le azioni da eseguire sui dati. Ad esempio, è possibile usare un'attività di copia per copiare i dati da un Server SQL locale a un'archiviazione BLOB di Azure. Quindi, usare un'attività Hive che esegue uno script Hive in un cluster HDInsight di Azure per elaborare o trasformare i dati dall'archivio BLOB per produrre dati di output. Infine, usare una seconda attività di copia per copiare i dati di output in un Azure SQL Data Warehouse in cui vengono compilate le soluzioni di report di business intelligence, BI. 

Un'attività può non avere alcun [set di dati](data-factory-create-datasets.md) di input o può averne più di uno e generare uno o più [set di dati di output](data-factory-create-datasets.md). Nel diagramma seguente viene illustrata la relazione tra attività, set di dati e pipeline in Data Factory: 

![Relazione tra pipeline, attività e set di dati](media/data-factory-create-pipelines/relationship-pipeline-activity-dataset.png)

Una pipeline consente di gestire le attività come un set anziché singolarmente. Ad esempio, è possibile distribuire, pianificare, sospendere e riprendere una pipeline, anziché gestire le attività della pipeline singolarmente.

Data Factory supporta due tipi di attività: attività di spostamento dei dati e attività di trasformazione dei dati. Ogni attività può non avere alcun [set di dati](data-factory-create-datasets.md) di input o può averne più di uno e generare uno o più set di dati di output.

Un set di dati di input rappresenta l'input per un'attività nella pipeline, un set di dati di output rappresenta l'output dell'attività. I set di dati identificano i dati all'interno dei diversi archivi dati, come tabelle, file, cartelle e documenti. Dopo aver creato un set di dati, è possibile usarlo con le attività in una pipeline. Ad esempio, un set di dati può essere configurato come set di dati di input o di output di un'attività di copia o un'attività HDInsightHive. Per altre informazioni sui set di dati, vedere l'articolo [Set di dati in Azure Data Factory](data-factory-create-datasets.md).

### <a name="data-movement-activities"></a>Attività di spostamento dei dati
L'attività di copia in Data Factory esegue la copia dei dati da un archivio dati di origine a un archivio dati sink. Data Factory supporta gli archivi dati seguenti. I dati da qualsiasi origine possono essere scritti in qualsiasi sink. Fare clic su un archivio dati per informazioni su come copiare dati da e verso tale archivio.

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Gli archivi dati contrassegnati da un asterisco (*) possono essere locali o in IaaS di Azure e richiederanno l'installazione del [Gateway di gestione dati](data-factory-data-management-gateway.md) in un computer IaaS locale o in Azure.

Per altre informazioni, vedere l'articolo [Attività di spostamento dei dati](data-factory-data-movement-activities.md).

### <a name="data-transformation-activities"></a>Attività di trasformazione dei dati
[!INCLUDE [data-factory-transformation-activities](../../../includes/data-factory-transformation-activities.md)]

Per altre informazioni, vedere l'articolo [Attività di trasformazione dei dati](data-factory-data-transformation-activities.md).

### <a name="custom-net-activities"></a>Attività .NET personalizzate 
Per spostare i dati da e verso un archivio dati che non è supportato dall'attività di copia o per trasformare i dati usando la propria logica, creare un'**attività .NET personalizzata**. Per i dettagli sulla creazione e l'uso di un'attività personalizzata, vedere l'articolo [Usare attività personalizzate in una pipeline di Azure Data Factory](data-factory-use-custom-activities.md).

## <a name="schedule-pipelines"></a>Pianificare le pipeline
Una pipeline è attiva solo tra l'ora di **inizio** e l'ora di **fine**. Non viene eseguita prima dell'ora di inizio o dopo l'ora di fine. Se la pipeline viene sospesa, non viene eseguita indipendentemente dall'ora di inizio e di fine. Per essere eseguita, una pipeline non deve essere in pausa. Vedere [Pianificazione ed esecuzione con Data factory](data-factory-scheduling-and-execution.md) per comprendere il funzionamento della pianificazione e dell'esecuzione in Data factory di Azure.

## <a name="pipeline-json"></a>Pipeline JSON
Osserviamo più da vicino come viene definita una pipeline nel formato JSON. La struttura generica di una pipeline è simile a quella indicata di seguito:

```json
{
    "name": "PipelineName",
    "properties": 
    {
        "description" : "pipeline description",
        "activities":
        [

        ],
        "start": "<start date-time>",
        "end": "<end date-time>",
        "isPaused": true/false,
        "pipelineMode": "scheduled/onetime",
        "expirationTime": "15.00:00:00",
        "datasets": 
        [
        ]
    }
}
```

| Tag | Descrizione | Obbligatorio |
| --- | --- | --- |
| name |Nome della pipeline. Specificare un nome che rappresenti l'azione eseguita dalla pipeline. <br/><ul><li>Numero massimo di caratteri: 260</li><li>Deve iniziare con una lettera, un numero o un carattere di sottolineatura (_)</li><li>Non sono ammessi i caratteri seguenti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li></ul> |Sì |
| Descrizione | Specificare il testo descrittivo che illustra lo scopo della pipeline. |Sì |
| attività | Nella sezione delle **attività** possono essere definite una o più attività. Vedere la sezione successiva per informazioni dettagliate sulle attività dell'elemento JSON. | Sì |  
| start | Data e ora di inizio per la pipeline. Devono essere nel [formato ISO](http://en.wikipedia.org/wiki/ISO_8601), Ad esempio: `2016-10-14T16:32:41Z`. <br/><br/>È possibile specificare un'ora locale, ad esempio in base al fuso orario EST. Di seguito viene riportato un esempio: `2016-02-27T06:00:00-05:00`, che indica le 06:00 EST.<br/><br/>Le proprietà start ed end insieme specificano il periodo attivo per la pipeline. Le sezioni di output vengono generate solo in questo periodo attivo. |No<br/><br/>Se si specifica un valore per la proprietà di fine, è necessario specificare un valore anche per la proprietà di avvio.<br/><br/>L'ora di inizio e l'ora di fine possono essere entrambe vuote per creare una pipeline. È necessario specificare entrambi i valori per impostare un periodo attivo per l'esecuzione della pipeline. Se non si specificano le ore di inizio e fine durante la creazione di una pipeline, è possibile impostare tali valori in un secondo momento usando il cmdlet Set-AzureRmDataFactoryPipelineActivePeriod. |
| end | Data e ora di fine per la pipeline. Se specificate, devono essere in formato ISO. Ad esempio: `2016-10-14T17:32:41Z` <br/><br/>È possibile specificare un'ora locale, ad esempio in base al fuso orario EST. Di seguito è fornito l'esempio `2016-02-27T06:00:00-05:00`, che indica le 6 EST.<br/><br/>Per eseguire la pipeline illimitatamente, specificare 9999-09-09 come valore per la proprietà end. <br/><br/> Una pipeline è attiva solo tra l'ora di inizio e l’ora di fine. Non viene eseguita prima dell'ora di inizio o dopo l'ora di fine. Se la pipeline viene sospesa, non viene eseguita indipendentemente dall'ora di inizio e di fine. Per essere eseguita, una pipeline non deve essere in pausa. Vedere [Pianificazione ed esecuzione con Data factory](data-factory-scheduling-and-execution.md) per comprendere il funzionamento della pianificazione e dell'esecuzione in Data factory di Azure. |No <br/><br/>Se si specifica un valore per la proprietà di avvio, è necessario specificare un valore anche per la proprietà di fine.<br/><br/>Vedere le note della proprietà **start** . |
| isPaused | Se impostata su true, la pipeline non viene eseguita. È in stato di sospensione. Valore predefinito = false. È possibile usare questa proprietà per abilitare o disabilitare una pipeline. |No |
| pipelineMode | Metodo di pianificazione delle esecuzioni per la pipeline. I valori consentiti sono scheduled (predefinito) e onetime.<br/><br/>"Scheduled" indica che la pipeline viene eseguita a intervalli di tempo specificati in base al periodo di attività, ovvero all'ora di inizio e di fine. "Onetime" indica che la pipeline viene eseguita una sola volta. Al momento, dopo aver creato una pipeline monouso non è possibile modificarla o aggiornarla. Per informazioni dettagliate sull'impostazione onetime, vedere la sezione [Pipeline monouso](#onetime-pipeline) . |No |
| expirationTime | Periodo di tempo dopo la creazione in cui la [pipeline monouso](#onetime-pipeline) è valida e deve rimanere con provisioning eseguito. Se non ha esecuzioni attive, non riuscite o in sospeso, quando viene raggiunta la scadenza, la pipeline viene eliminata automaticamente. Il valore predefinito: `"expirationTime": "3.00:00:00"`|No |
| set di dati |Elenco dei set di dati usati dalle attività definite nella pipeline. Questa proprietà può essere usata per definire i set di dati specifici di questa pipeline e non definiti all'interno della data factory. I set di dati definiti all'interno di questa pipeline possono essere usati solo da questa pipeline e non possono essere condivisi. Per informazioni dettagliate, vedere la sezione [Set di dati con ambito](data-factory-create-datasets.md#scoped-datasets) . |No |

## <a name="activity-json"></a>Attività JSON
Nella sezione delle **attività** possono essere definite una o più attività. Ogni attività presenta la seguente struttura di livello superiore:

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
    },
    "scheduler":
    {
    }
}
```

La tabella seguente descrive le proprietà all'interno della definizione JSON dell'attività:

| Tag | Descrizione | Obbligatorio |
| --- | --- | --- |
| name | Nome dell'attività. Specificare un nome che rappresenti l'azione eseguita dall'attività. <br/><ul><li>Numero massimo di caratteri: 260</li><li>Deve iniziare con una lettera, un numero o un carattere di sottolineatura (_)</li><li>Non sono ammessi i caratteri seguenti: ".", "+", "?", "/", "<", ">", "*", "%", "&", ":", "\\"</li></ul> |Sì |
| Descrizione | Testo descrittivo per il tipo o lo scopo dell'attività |Sì |
| type | Tipo di attività. Per informazioni sui diversi tipi di attività, vedere le sezioni [Attività di spostamento dei dati](#data-movement-activities) e [Attività di trasformazione dei dati](#data-transformation-activities). |Sì |
| inputs |Tabelle di input usate dall'attività<br/><br/>`// one input table`<br/>`"inputs":  [ { "name": "inputtable1"  } ],`<br/><br/>`// two input tables` <br/>`"inputs":  [ { "name": "inputtable1"  }, { "name": "inputtable2"  } ],` |Sì |
| outputs |Tabelle di output usate dall'attività.<br/><br/>`// one output table`<br/>`"outputs":  [ { "name": "outputtable1" } ],`<br/><br/>`//two output tables`<br/>`"outputs":  [ { "name": "outputtable1" }, { "name": "outputtable2" }  ],` |Sì |
| linkedServiceName |Nome del servizio collegato usato dall'attività. <br/><br/>Per un'attività può essere necessario specificare il servizio collegato che collega all'ambiente di calcolo richiesto. |Sì per Attività di HDInsight e Attività di assegnazione punteggio batch di Azure Machine Learning  <br/><br/>No per tutto il resto |
| typeProperties |Le proprietà nella sezione **typeProperties** dipendono dal tipo di attività. Per visualizzare le proprietà del tipo per un'attività, fare clic sui collegamenti all'attività nella sezione precedente. | No |
| policy |Criteri che influiscono sul comportamento di runtime dell'attività. Se vengono omessi, vengono usati i criteri predefiniti. |No |
| scheduler | La proprietà "scheduler" viene usata per definire la pianificazione per l'attività. Le relative proprietà secondarie sono quelle indicate nella sezione [Disponibilità dei set di dati](data-factory-create-datasets.md#dataset-availability). |No |


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

## <a name="sample-copy-pipeline"></a>Esempio di una pipeline di copia
In questa pipeline di esempio è presente un'attività di tipo **Copy** in the **attività** . In questo esempio, l'[attività di copia](data-factory-data-movement-activities.md) consente di copiare i dati da un'archiviazione BLOB di Azure a un database SQL di Azure. 

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
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
} 
```

Tenere presente quanto segue:

* Nella sezione delle attività esiste una sola attività con l'oggetto **type** impostato su **Copy**.
* L'input per l'attività è impostato su **InputDataset** e l'output è impostato su **OutputDataset**. Vedere l'articolo [Set di dati](data-factory-create-datasets.md) per la definizione di set di dati in JSON. 
* Nella sezione **typeProperties** vengono specificati **BlobSource** come tipo di origine e **SqlSink** come tipo di sink. Nella sezione [Attività di spostamento dati](#data-movement-activities), scegliere l'archivio dati che si desidera usare come origine o un sink per avere altre informazioni sullo spostamento dei dati da e verso tale archivio dati. 

Per la procedura completa di creazione di questa pipeline, vedere [Esercitazione: copiare i dati dall'archivio BLOB al database SQL](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md). 

## <a name="sample-transformation-pipeline"></a>Esempio di una pipeline di trasformazione
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
        "start": "2016-04-01T00:00:00Z",
        "end": "2016-04-02T00:00:00Z",
        "isPaused": false
    }
}
```

Tenere presente quanto segue: 

* Nella sezione attività esiste una sola attività con l'oggetto **type** impostato su **HDInsightHive**.
* Il file di script Hive, **partitionweblogs.hql**, è archiviato nell'account di archiviazione di Azure (specificato da scriptLinkedService, denominato **AzureStorageLinkedService**) e nella cartella **script** nel contenitore **adfgetstarted**.
* La sezione `defines` viene usata per specificare le impostazioni di runtime che vengono passate allo script Hive come valori di configurazione Hive, ad esempio `${hiveconf:inputtable}` e `${hiveconf:partitionedtable}`.

La sezione **typeProperties** è diversa per ogni attività di trasformazione. Per altre informazioni sulle proprietà del tipo supportate per un'attività di trasformazione, fare clic sull'attività di trasformazione nella tabella [Data transformation activities](#data-transformation-activities) (Attività di trasformazione dati). 

Per la procedura completa della creazione di questa pipeline, vedere [Esercitazione: creare la prima pipeline per elaborare i dati usando il cluster Hadoop](data-factory-build-your-first-pipeline.md). 

## <a name="multiple-activities-in-a-pipeline"></a>Attività multiple in una pipeline
Le due pipeline di due esempio precedenti contengono una sola attività. È possibile avere più di un'attività in una pipeline.  

Se si hanno più attività in una pipeline e l'output di un'attività non è l'input di un'altra attività, le attività possono essere eseguite in parallelo se le sezioni di dati di input per le attività sono pronte. 

È possibile concatenare due attività usando il set di dati di output di un'attività come set di dati di input di altre attività. La seconda attività viene eseguita solo quando la prima viene completata correttamente.

![Concatenamento di attività nella stessa pipeline](./media/data-factory-create-pipelines/chaining-one-pipeline.png)

In questo esempio, la pipeline ha due attività: Activity1 e Activity2. Activity1 accetta Dataset1 come input e produce un output Dataset2. Activity2 accetta Dataset2 come input e produce un output Dataset3. Poiché l'output di Activity1 (Dataset2) è l'input di Activity2, Activity2 viene eseguita solo in seguito al corretto completamento dell'attività precedente e genera la sezione Dataset2. Se il completamento di Activity1 per qualche motivo non riesce e non produce la sezione Dataset2, Activity2 non viene eseguita per tale sezione, ad esempio dalle 09:00 alle 10:00. 

È anche possibile concatenare le attività che si trovano in pipeline diverse.

![Concatenamento di attività in due pipeline](./media/data-factory-create-pipelines/chaining-two-pipelines.png)

In questo esempio, Pipeline1 ha una sola attività che accetta come input Dataset1 e genera come output Dataset2. Pipeline2 ha una sola attività che accetta come input Dataset2 e genera come output Dataset3. 

Per altre informazioni, vedere [Pianificazione ed esecuzione](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline). 

## <a name="create-and-monitor-pipelines"></a>Creare e monitorare le pipeline
È possibile creare pipeline usando uno di questi strumenti o SDK. 

- Copia guidata. 
- Portale di Azure
- Visual Studio
- Azure PowerShell
- Modello di Azure Resource Manager
- API REST
- API .NET

Vedere le esercitazioni seguenti per istruzioni dettagliate sulla creazione di pipeline tramite uno di questi strumenti o SDK.
 
- [Creare una pipeline con un'attività di trasformazione dati](data-factory-build-your-first-pipeline.md)
- [Creare una pipeline con un'attività di spostamento dati](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)

Dopo aver creato o distribuito una pipeline, è possibile gestire e monitorare le pipeline usando i pannelli del portale di Azure o con un'app di gestione e monitoraggio. Vedere l'argomento successivo per le istruzioni dettagliate. 

- [Monitorare e gestire le pipeline con i pannelli del portale di Azure](data-factory-monitor-manage-pipelines.md).
- [Monitorare e gestire le pipeline con l'app di monitoraggio e gestione](data-factory-monitor-manage-app.md)


## <a name="onetime-pipeline"></a>Pipeline monouso
È possibile creare e pianificare una pipeline da eseguire periodicamente, ad esempio ogni ora o ogni giorno, tra le ore di inizio e di fine specificate nella definizione della pipeline. Per informazioni dettagliate, vedere la sezione [Pianificazione delle attività](#scheduling-and-execution) . È anche possibile creare una pipeline che viene eseguita una sola volta. A tale scopo, impostare la proprietà **pipelineMode** nella definizione della pipeline su **onetime**, come illustrato nell'esempio JSON seguente. Il valore predefinito per questa proprietà è **scheduled**.

```json
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
```

Tenere presente quanto segue:

* Le ore di **inizio** e **fine** della pipeline non vengono specificate.
* La **disponibilità** dei set di dati di input e output viene specificata (**frequenza** e **intervallo**) anche se i valori non vengono usati da Data Factory.  
* Le pipeline monouso non vengono visualizzate nella vista Diagramma. Questo comportamento dipende dalla progettazione.
* Le pipeline monouso non possono essere aggiornate. È possibile clonare una pipeline monouso, rinominarla, aggiornarne le proprietà e distribuirla per crearne un'altra.


## <a name="next-steps"></a>Passaggi successivi
- Per altre informazioni sui set di dati, vedere l'articolo [Creare i set di dati](data-factory-create-datasets.md). 
- Per altre informazioni sulle modalità di pianificazione ed esecuzione delle pipeline, vedere l'articolo [Pianificazione ed esecuzione in Azure Data Factory](data-factory-scheduling-and-execution.md). 
  

