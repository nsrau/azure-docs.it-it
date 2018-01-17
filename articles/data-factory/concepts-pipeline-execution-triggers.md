---
title: Esecuzione e trigger di pipeline in Azure Data Factory | Microsoft Docs
description: "Questo articolo contiene informazioni sulla modalità di esecuzione di una pipeline in Azure Data Factory su richiesta o mediante la creazione di un trigger."
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2018
ms.author: shlo
ms.openlocfilehash: 88ae5dfbf6246ecf92d6528ad3d9a8e5fb57e4b0
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/06/2018
---
# <a name="pipeline-execution-and-triggers-in-azure-data-factory"></a>Esecuzione e trigger di pipeline in Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versione 1 - Disponibilità generale](v1/data-factory-scheduling-and-execution.md)
> * [Versione 2 - Anteprima](concepts-pipeline-execution-triggers.md)

**Esecuzione di pipeline** in Azure Data Factory versione 2 definisce un'istanza dell'esecuzione di una pipeline. Ad esempio si dispone di una pipeline che viene eseguita alle ore 8, alle 9 e alle 10. Non vi saranno tre esecuzioni separate della pipeline (esecuzioni di pipeline) in questo caso. Ogni esecuzione di pipeline dispone di un ID di esecuzione della pipeline univoco, ovvero un GUID che definisce in modo univoco questa esecuzione di pipeline specifica. Le istanze delle esecuzioni di pipeline in genere vengono create mediante il passaggio di argomenti ai parametri definiti nelle pipeline. Esistono due modi per eseguire una pipeline: **manualmente** o tramite un **trigger**. Questo articolo fornisce informazioni dettagliate su entrambe le modalità di esecuzione di una pipeline.

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale (GA), vedere le informazioni sulla [pianificazione ed esecuzione in Data Factory versione 1](v1/data-factory-scheduling-and-execution.md).

## <a name="run-pipeline-on-demand"></a>Esecuzione della pipeline su richiesta
In questo metodo la pipeline viene eseguita manualmente. Viene considerata anche come esecuzione su richiesta di una pipeline.

Ad esempio si dispone di una pipeline denominata **copyPipeline** che si vuole eseguire. La pipeline è una pipeline semplice con una singola attività che esegue una copia da una cartella di origine in Archiviazione BLOB di Azure in una cartella di destinazione nella stessa risorsa di archiviazione. Di seguito è riportata la definizione della pipeline di esempio:

```json
{
  "name": "copyPipeline",
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "name": "CopyBlobtoBlob",
        "inputs": [
          {
            "referenceName": "sourceBlobDataset",
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "sinkBlobDataset",
            "type": "DatasetReference"
          }
        ]
      }
    ],
    "parameters": {
      "sourceBlobContainer": {
        "type": "String"
      },
      "sinkBlobContainer": {
        "type": "String"
      }
    }
  }
}

```
La pipeline accetta due parametri: sourceBlobContainer e sinkBlobContainer, come illustrato nella definizione JSON. Passare valori a questi parametri in fase di esecuzione.

Per eseguire manualmente la pipeline, è possibile usare uno dei modi seguenti: .NET, PowerShell, REST e Python.

### <a name="rest-api"></a>API REST
Di seguito è riportato un comando REST di esempio:  

```
POST
https://management.azure.com/subscriptions/mySubId/resourceGroups/myResourceGroup/providers/Microsoft.DataFactory/factories/myDataFactory/pipelines/copyPipeline/createRun?api-version=2017-03-01-preview
```
Per un esempio completo, vedere [Avvio rapido: Creare una data factory usando l'API REST](quickstart-create-data-factory-rest-api.md).

### <a name="powershell"></a>PowerShell
Di seguito è riportato un comando PowerShell di esempio:

```powershell
Invoke-AzureRmDataFactoryV2Pipeline -DataFactory $df -PipelineName "Adfv2QuickStartPipeline" -ParameterFile .\PipelineParameters.json
```

I parametri vengono passati nel corpo nel payload della richiesta. In .NET, PowerShell e Python passare i valori in un dizionario passato come argomento alla chiamata.

```json
{
  “sourceBlobContainer”: “MySourceFolder”,
  “sinkBlobCountainer”: “MySinkFolder”
}
```

Il payload di risposta è un ID univoco dell'esecuzione della pipeline:

```json
{
  "runId": "0448d45a-a0bd-23f3-90a5-bfeea9264aed"
}
```


Per un esempio completo, vedere [Avvio rapido: Creare una data factory usando PowerShell](quickstart-create-data-factory-powershell.md).

### <a name="net"></a>.NET
Di seguito è riportata una chiamata .NET di esempio:

```csharp
client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters)
```

Per un esempio completo, vedere [Avvio rapido: Creare una data factory usando .NET](quickstart-create-data-factory-dot-net.md).

> [!NOTE]
> È possibile usare l'API .NET per richiamare le pipeline di Data Factory da Funzioni di Azure, dai servizi web personalizzati e così via.

## <a name="triggers"></a>Trigger
I trigger sono il secondo modo per avviare l'esecuzione di una pipeline. I trigger rappresentano un'unità di elaborazione che determina quando deve essere avviata l'esecuzione di una pipeline. Data Factory supporta attualmente due tipi di trigger: 1)**trigger dell'Utilità di pianificazione**, ovvero un trigger che richiama una pipeline in base a una pianificazione concreta 2)**trigger di finestra a cascata**, per i trigger che operano su un intervallo periodico, mantenendo al tempo stesso lo stato. Attualmente il servizio Data Factory non supporta i trigger basati su eventi, ad esempio il trigger di un'esecuzione di pipeline in caso di ricezione di un file.

Pipeline e trigger hanno una relazione molti-a-molti. Più trigger possono avviare una singola pipeline e un singolo trigger può avviare più pipeline. Nella seguente definizione JSON di un trigger, la proprietà **pipelines** referenzia un elenco di pipeline attivate dal trigger specifico e ai valori per i parametri della pipeline.

### <a name="basic-trigger-definition"></a>Definizione del trigger di base:
```json
    "properties": {
        "name": "MyTrigger",
        "type": "<type of trigger>",
        "typeProperties": {
            …
        },
        "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                          "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
            }
        ]
    }
```

## <a name="schedule-trigger"></a>Trigger di pianificazione
Il trigger di pianificazione esegue le pipeline con una pianificazione basata sul tempo reale. Questo trigger supporta opzioni di calendario avanzate e periodiche, ad esempio settimanale, il lunedì alle 17:00 e il giovedì alle 21:00. È flessibile poiché il criterio del set di dati è indipendente dai dati di serie temporali e dai dati di serie non temporali.

Per informazioni più specifiche ed esempi sui trigger di pianificazione, vedere [Procedura: Creare un trigger di pianificazione](how-to-create-schedule-trigger.md)

## <a name="tumbling-window-trigger"></a>Creare un trigger di finestra a cascata
I trigger di finestra a cascata vengono attivati in base a un intervallo di tempo periodico a partire da un'ora di inizio specificata, mantenendo al tempo stesso lo stato. Le finestre a cascata sono costituite da una serie di intervalli temporali di dimensioni fisse, contigui e non sovrapposti.
Per informazioni più specifiche ed esempi sui trigger di finestra a cascata, vedere [Procedura: Creare un trigger di finestra a cascata](how-to-create-tumbling-window-trigger.md)

### <a name="scheduler-trigger-json-definition"></a>Definizione JSON del trigger dell'utilità di pianificazione
Quando si crea un trigger dell'utilità di pianificazione, è possibile specificare la pianificazione e la ricorrenza usando JSON, come illustrato nell'esempio in questa sezione. 

Per fare in modo che il trigger dell'utilità di pianificazione attivi l'esecuzione di una pipeline, includere un riferimento di pipeline della pipeline specifica nella definizione del trigger. Pipeline e trigger hanno una relazione molti-a-molti. Più trigger possono attivare una singola pipeline. Un singolo trigger può attivare più pipeline.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Year>>,
        "interval": <<int>>,             // how often to fire
        "startTime": <<datetime>>,
        "endTime": <<datetime>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-24>>],
          "weekDays": ": [<<Monday-Sunday>>],
          "minutes": [<<0-60>>],
          "monthDays": [<<1-31>>],
          "monthlyOccurences": [
               {
                    "day": <<Monday-Sunday>>,
                    "occurrence": <<1-5>>
               }
           ] 
        }
      }
    },
   "pipelines": [
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "<Name of your pipeline>"
                },
                "parameters": {
                    "<parameter 1 Name>": {
                        "type": "Expression",
                        "value": "<parameter 1 Value>"
                    },
                    "<parameter 2 Name>" : "<parameter 2 Value>"
                }
           }
      ]
  }
}
```

> [!IMPORTANT]
>  La proprietà **parameters** è obbligatoria in **pipelines**. Anche se la pipeline non accetta parametri, includere un oggetto JSON vuoto per i parametri, dato che la proprietà deve esistere.


### <a name="overview-scheduler-trigger-schema"></a>Panoramica: schema del trigger dell'utilità di pianificazione
La tabella seguente fornisce una panoramica di alto livello degli elementi principali correlati alla ricorrenza e pianificazione in un trigger:

Proprietà JSON |     Descrizione
------------- | -------------
startTime | startTime è in formato Data-Ora. Per le pianificazioni semplici startTime indica la prima occorrenza. Per le pianificazioni complesse il trigger viene attivato non prima del valore startTime.
endTime | Specifica la data e l'ora di fine per il trigger. Il trigger non viene eseguito dopo questo periodo di tempo. Non è possibile impostare un endTime nel passato.
timeZone | Attualmente è supportato solo UTC. 
ricorrenza | L'oggetto recurrence specifica le regole di ricorrenza per il trigger. L'oggetto recurrence supporta gli elementi: frequency, interval, endTime, count e schedule. Se viene definito l'oggetto recurrence, è necessario definire anche frequency. Gli altri elementi di recurrence sono facoltativi.
frequency | Rappresenta l'unità di frequenza con cui il trigger si ripete. I valori supportati sono: `minute`, `hour`, `day`, `week` o `month`.
interval | L'intervallo è un numero intero positivo. Indica l'intervallo di frequenza che determina la frequenza con cui viene eseguito il trigger. Se, ad esempio, interval è 3 e frequency è "week", il trigger si ripete ogni 3 settimane.
schedule | Un trigger con una frequenza specificata modifica la sua ricorrenza in base a una pianificazione di ricorrenza. Un oggetto schedule contiene modifiche in base a minuti, ore, giorni della settimana, giorni del mese e numero settimana.


## <a name="tumbling-window-trigger-vs-schedule-trigger"></a>Confronto tra un trigger di finestra a cascata e un trigger di pianificazione
Il trigger di finestra a cascata e il trigger di pianificazione funzionano entrambi in base agli heartbeat temporali, ma presentano alcune differenze.
Per il trigger di finestra a cascata:
* **Scenari di recupero delle informazioni**: i trigger di finestra a cascata supportano scenari di recupero delle informazioni, poiché sono in grado di pianificare esecuzioni per finestre nel passato. Il trigger di pianificazione può essere eseguito solo su periodi di tempo a partire dall'ora attuale.
* **Affidabilità**: i trigger di finestra a cascata pianificheranno esecuzioni delle pipeline per tutte le finestre a partire da una data di inizio, senza interruzioni e con un'affidabilità del 100%.
* **Ripetizione dei tentativi**: i trigger di finestra a cascata offrono funzionalità di ripetizione dei tentativi. Le esecuzioni non riuscite delle pipeline hanno un criterio di ripetizione dei tentativi predefinito pari a 0 oppure a un valore specificato dall'utente come parte della definizione del trigger. I tentativi verranno inoltre ripetuti automaticamente sulle istanze con errori di esecuzione a causa di limiti di concorrenza/server/limitazione, ad esempio con errori con codice di stato 400 (errore dell'utente), 429 (numero eccessivo di richieste), 500 (errore interno del server).
* **Concorrenza**: i trigger di finestra a cascata consentono agli utenti di impostare esplicitamente limiti di concorrenza per il trigger (valore massimo compreso tra 1 e 50 esecuzioni della pipeline attivate simultanee).
* **Variabili di inizio e di fine finestra**: per i trigger di finestra a cascata gli utenti possono accedere a triggerOutputs().windowStartTime e triggerOutputs().windowEndTime come variabili di sistema dei trigger nella definizione dei trigger. Queste variabili corrisponderanno rispettivamente all'ora di inizio e di fine della finestra. Se ad esempio è presente un trigger di finestra a cascata che viene eseguito ogni ora, per la finestra compresa tra le ore 1:00 e 2:00 i valori saranno triggerOutputs().WindowStartTime = 2017-09-01T01:00:00Z e triggerOutputs().WindowEndTime = 2017-09-01T02:00:00Z.
* **Relazione tra pipeline e trigger**: i trigger di pianificazione hanno una relazione di tipo n:m con le pipeline. Un trigger di pianificazione può attivare più pipeline. I trigger di finestra a cascata hanno una relazione di tipo 1:1 con le pipeline. Un trigger di finestra a cascata può attivare solo una pipeline.

### <a name="schedule-trigger-example"></a>Esempio di trigger di pianificazione

```json
{
    "properties": {
        "name": "MyTrigger",
        "type": "ScheduleTrigger",
        "typeProperties": {
            "recurrence": {
                "frequency": "Hour",
                "interval": 1,
                "startTime": "2017-11-01T09:00:00-08:00",
                "endTime": "2017-11-02T22:00:00-08:00"
            }
        },
        "pipelines": [{
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToBlobPipeline"
                },
                "parameters": {}
            },
            {
                "pipelineReference": {
                    "type": "PipelineReference",
                    "referenceName": "SQLServerToAzureSQLPipeline"
                },
                "parameters": {}
            }
        ]
    }
}
```

### <a name="overview-scheduler-trigger-schema-defaults-limits-and-examples"></a>Panoramica: impostazioni predefinite dello schema del trigger dell'utilità di pianificazione, limiti ed esempi

Nome JSON | Tipo di valore | Obbligatorio? | Valore predefinito | Valori validi | Esempio
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | Stringa | Sì | Nessuno | Date-Ore ISO-8601 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
ricorrenza | Oggetto | Sì | Nessuno | Oggetto ricorrenza | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Numero | Sì | Nessuno | Da 1 a 1000. | ```"interval":10```
endTime | Stringa | Sì | Nessuno | Il valore Data-Ora fa riferimento a un momento nel futuro | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Oggetto | No | Nessuno | Oggetto pianificazione | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

### <a name="deep-dive-starttime"></a>Approfondimenti: startTime
La tabella seguente illustra come startTime controlla la modalità di esecuzione di un trigger:

Valore startTime | Ricorrenza senza pianificazione | Ricorrenza con pianificazione
--------------- | --------------------------- | ------------------------
Ora di inizio nel passato | Calcola l'ora della prima esecuzione futura dopo l'ora di inizio e avvia l'esecuzione in corrispondenza di tale ora.<p>Avvia le esecuzioni successive basandosi sul calcolo dall'ultima esecuzione.</p><p>Vedere l'esempio sotto a questa tabella.</p> | Il trigger viene avviato _non prima_ dell'ora di inizio specificata. La prima occorrenza è basata sulla pianificazione calcolata dall'ora di inizio. <p>Avviare le esecuzioni successive in base alla pianificazione di ricorrenza</p>
Ora di inizio nel futuro o nel presente | Avvia l'esecuzione una sola volta all'ora di inizio specificata. <p>Avviare le esecuzioni successive basandosi sul calcolo dall'ultima esecuzione.</p> | Il trigger viene avviato _non prima_ dell'ora di inizio specificata. La prima occorrenza è basata sulla pianificazione calcolata dall'ora di inizio.<p>Avviare le esecuzioni successive in base alla pianificazione di ricorrenza.</p>

È possibile esaminare lo scenario che prevede un valore per startTime nel passato, un oggetto recurrence, ma nessun oggetto schedule. Si supponga che l'ora corrente sia `2017-04-08 13:00`, startTime sia `2017-04-07 14:00` e recurrence sia pari a ogni due giorni, in base a frequency: day e interval: 2. Si noti che il valore di startTime è nel passato e si verifica prima dell'ora corrente.

In queste condizioni, la prima esecuzione avviene alle `2017-04-09 at 14:00`. Il motore dell'utilità di pianificazione calcola le occorrenze dall'ora di inizio dell'esecuzione. Vengono eliminate tutte le istanze in passato. Il motore utilizza l'istanza successiva che si verifica in futuro. In questo caso, quindi, il valore di startTime è `2017-04-07 at 2:00pm`, quindi l'istanza successiva viene eseguita due giorni dopo tale orario, ovvero il giorno `2017-04-09 at 2:00pm`.

Si noti che la prima esecuzione sarebbe la stessa anche se startTime fosse `2017-04-05 14:00` o `2017-04-01 14:00`. Dopo la prima esecuzione, le esecuzioni successive vengono calcolate in base alla pianificazione. Pertanto sono `2017-04-11 at 2:00pm`, quindi `2017-04-13 at 2:00pm`, quindi `2017-04-15 at 2:00pm` e così via.

Infine, quando un trigger ha una pianificazione, se non sono impostate ore e/o minuti nella pianificazione, per impostazione predefinita vengono usate le ore e/o minuti della prima esecuzione.

### <a name="deep-dive-schedule"></a>Approfondimenti: schedule
Da un lato, un oggetto schedule può limitare il numero di esecuzioni di un trigger. Se ad esempio l'oggetto schedule di un trigger con frequenza "month" prevede l'esecuzione solo il giorno 31, il trigger viene eseguito solo nei mesi che includono 31 giorni.

Un oggetto schedule può tuttavia anche aumentare il numero di esecuzioni di un trigger. Se, ad esempio l'oggetto schedule di un trigger con frequenza "month" prevede l'esecuzione nei giorni 1 e 2 del mese, il trigger viene eseguito il 1° e il 2° giorno del mese, invece che una volta al mese.

Se vengono specificati più elementi dell'oggetto schedule, l'ordine di valutazione è dal più grande al più piccolo: numero della settimana, giorno del mese, giorno della settimana, ora e minuto.

La tabella seguente illustra in modo dettagliato gli elementi dell'oggetto schedule.


Nome JSON | Descrizione | Valori validi
--------- | ----------- | ------------
minuti | Minuti dell'ora in cui verrà eseguito il trigger. | <ul><li>Matrice di numeri interi</li></ul>
ore | Ore del giorno in cui verrà eseguito il trigger. | <ul><li>Matrice di numeri interi</li></ul>
weekDays | Giorni della settimana in cui verrà eseguito il trigger. Può essere specificato solo con una frequenza settimanale. | <ul><li>Matrice dei valori seguenti (dimensione massima della matrice: 7)<ul><li>lunedì</li><li>martedì</li><li>mercoledì</li><li>giovedì</li><li>venerdì</li><li>sabato</li><li>domenica</li></ul></li></p>Non viene applicata la distinzione tra maiuscole e minuscole</p>
monthlyOccurrences | Determina in quali giorni del mese verrà eseguito il trigger. Può essere specificato solo con una frequenza mensile. | Matrice di oggetti monthlyOccurence: `{ "day": day,  "occurrence": occurence }`. <p> Il giorno della settimana in cui verrà eseguito il trigger, ad esempio `{Sunday}` corrisponde a ogni domenica del mese. Richiesto.<p>occurrence è l'occorrenza del giorno durante il mese, ad esempio `{Sunday, -1}` corrisponde all'ultima domenica del mese. facoltativo.
monthDays | Giorno del mese in cui verrà eseguito il trigger. Può essere specificato solo con una frequenza mensile. | <ul><li>Matrice dei valori seguenti</li><ul><li>Qualsiasi valore <= -1 e >= -31</li><li>Qualsiasi valore >= 1 e <= 31</li></ul></ul> |


## <a name="examples-recurrence-schedules"></a>Esempi: pianificazioni di ricorrenza
Questa sezione fornisce esempi di pianificazioni di ricorrenza con attenzione all'oggetto schedule e ai relativi elementi secondari.

Le pianificazioni di esempio presuppongono che l'intervallo sia impostato su 1. Inoltre, presupporre che la frequenza sia corretta in base al contenuto di schedule, ad esempio, non si può usare la frequenza "day" e modificare "monthDays" nella pianificazione. Queste restrizioni sono indicate nella tabella nella sezione precedente. 

Esempio | Descrizione
------- | -----------
`{"hours":[5]}` | Viene eseguito alle 5 di mattina ogni giorno.
`{"minutes":[15], "hours":[5]}` | Viene eseguito alle 5:15 ogni giorno.
`{"minutes":[15], "hours":[5,17]}` | Viene eseguito alle 5:15 e alle 17:15 ogni giorno.
`{"minutes":[15,45], "hours":[5,17]}` | Viene eseguito alle 5:15, 5:45, 17:15 e 17:45 ogni giorno.
`{"minutes":[0,15,30,45]}` | Viene eseguito ogni 15 minuti.
`{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Viene eseguito ogni ora. Il trigger viene eseguito ogni ora. Il valore per i minuti viene controllato dall'oggetto startTime, se specificato, oppure dall'ora di creazione, se tale oggetto non è specificato. Ad esempio, se l'ora di inizio o l'ora di creazione (qualunque delle due si applichi) è 12:25, il trigger viene eseguito alle 00:25, 01:25, 02:25, ..., 23:25. La pianificazione equivale a un processo con frequency pari a "hour", interval di 1 e nessun oggetto schedule. Questa pianificazione, tuttavia, può essere usata con valori diversi per frequency e interval, per creare anche altri processi. Se, ad esempio, il valore per frequency fosse "month", la pianificazione verrebbe eseguita solo una volta al mese invece di ogni giorno, come accadrebbe se il valore di frequency fosse "day".
`{"minutes":[0]}` | Viene eseguito ogni ora all'inizio dell'ora. Anche questo trigger viene eseguito ogni ora, ma al suo inizio, ad esempio 12:00, 13:00, 14:00, etc. Questa impostazione equivale a un trigger con frequenza di "hour", un valore startTime con zero minuti e nessuna pianificazione se la frequenza è "day", ma se la frequenza è "week" o "month", la pianificazione verrebbe eseguita rispettivamente un solo giorno alla settimana o al mese.
`{"minutes":[15]}` | Viene eseguito 15 minuti dopo l'inizio di ogni ora. Viene eseguito ogni ora, a partire da 00:15, poi 01:15, 02:15, e così via fino a 22:15 e 23:15.
`{"hours":[17], "weekDays":["saturday"]}` | Viene eseguito alle 17.00 di ogni sabato.
`{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Viene eseguito alle 17 di ogni lunedì, mercoledì e venerdì.
`{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Viene eseguito alle 17:15 e alle 17:45 di ogni lunedì, mercoledì e venerdì.
`{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Eseguire ogni 15 minuti nei giorni feriali
`{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Viene eseguito ogni 15 minuti nei giorni feriali tra le 09:00 e le 16:45.
`{"weekDays":["tuesday", "thursday"]}` | Viene eseguito ogni martedì e giovedì all'ora di inizio specificata.
`{"minutes":[0], "hours":[6], "monthDays":[28]}` | Viene eseguito alle 06:00 il ventottesimo giorno di ogni mese, presumendo che la frequenza sia mensile.
`{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Viene eseguito alle 06:00 dell'ultimo giorno del mese. Se si vuole eseguire un trigger l'ultimo giorno del mese, usare -1 anziché il giorno 28, 29, 30 o 31.
`{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Viene eseguito alle 06:00 il primo e l'ultimo giorno di ogni mese.
`{monthDays":[1,14]}` | Viene eseguito il primo e il quattordicesimo giorno di ogni mese all'ora di inizio.
`{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Viene eseguito il primo venerdì di ogni mese alle ore 05:00.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Viene eseguito il primo venerdì di ogni mese all'ora di inizio specificata.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Viene eseguito il terzo venerdì dalla fine del mese, ogni mese, all'ora di inizio.
`{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Viene eseguito nel primo e ultimo venerdì di ogni mese alle 5:15.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Viene eseguito il primo e l'ultimo venerdì di ogni mese all'ora di inizio specificata.
`{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Viene eseguito il quinto venerdì di ogni mese all'ora di inizio. Se non ci sono cinque venerdì nel mese, la pipeline non viene eseguita, dato che è pianificata per essere lanciato solamente al quinto venerdì del mese.  È consigliabile usare -1 anziché 5 per l'occorrenza se si vuole eseguire il trigger l'ultimo venerdì del mese.
`{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Viene eseguito ogni 15 minuti, l'ultimo venerdì del mese.
`{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Viene eseguito alle ore 5:15, 5:45, 17:15 e 17:45 il terzo mercoledì di ogni mese.




## <a name="next-steps"></a>Passaggi successivi
Vedere le esercitazioni seguenti:

- [Quickstart: create a data factory using .NET](quickstart-create-data-factory-dot-net.md) (Avvio rapido: Creare una data factory tramite .NET)
- [Procedura: Creare un trigger di pianificazione](how-to-create-schedule-trigger.md)
- [Procedura: Creare un trigger di finestra a cascata](how-to-create-tumbling-window-trigger.md)
