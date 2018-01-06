---
title: Come creare trigger di pianificazione in Data Factory di Azure | Documenti Microsoft
description: Informazioni su come creare un trigger in Azure Data Factory in cui viene eseguita una pipeline in una pianificazione.
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: shlo
ms.openlocfilehash: 6b92e8402d372e29e264dc70b128124973d66bb9
ms.sourcegitcommit: 1d423a8954731b0f318240f2fa0262934ff04bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/05/2018
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Come creare un trigger che esegue una pipeline in una pianificazione
Questo articolo fornisce informazioni sul trigger di pianificazione e le procedure per creare, avviare e monitorare un trigger. Per altri tipi di trigger, vedere [della Pipeline di esecuzione e i trigger](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere l'[introduzione a Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

### <a name="schedule-trigger-json-definition"></a>Trigger di pianificazione definizione JSON
Quando si crea un trigger di pianificazione, è possibile specificare la pianificazione e ricorrenza usando JSON, come illustrato nell'esempio di questa sezione.

Per avviare una pipeline di eseguire il trigger di pianificazione, includere un riferimento di pipeline della pipeline particolare nella definizione del trigger. Pipeline e trigger hanno una relazione molti-a-molti. Più trigger possono attivare una singola pipeline. Un singolo trigger può attivare più pipeline.

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // optional, how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // optional (advanced scheduling specifics)
          "hours": [<<0-23>>],
          "weekDays": : [<<Monday-Sunday>>],
          "minutes": [<<0-59>>],
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


### <a name="overview-schedule-trigger-schema"></a>Cenni preliminari: Schema di trigger di pianificazione
La tabella seguente fornisce una panoramica di alto livello degli elementi principali correlati alla ricorrenza e pianificazione in un trigger:

Proprietà JSON |     DESCRIZIONE
------------- | -------------
startTime | startTime è in formato Data-Ora. Per le pianificazioni semplici startTime indica la prima occorrenza. Per le pianificazioni complesse il trigger viene attivato non prima del valore startTime.
endTime | Specifica la data e l'ora di fine per il trigger. Il trigger non viene eseguito dopo questo periodo di tempo. Non è possibile impostare un endTime nel passato. Si tratta di una proprietà facoltativa.
timeZone | Attualmente è supportato solo UTC.
ricorrenza | L'oggetto recurrence specifica le regole di ricorrenza per il trigger. L'oggetto recurrence supporta gli elementi: frequency, interval, endTime, count e schedule. Se viene definito l'oggetto recurrence, è necessario definire anche frequency. Gli altri elementi di recurrence sono facoltativi.
frequency | Rappresenta l'unità di frequenza con cui il trigger si ripete. I valori supportati sono: `minute`, `hour`, `day`, `week` o `month`.
interval | L'intervallo è un numero intero positivo. Indica l'intervallo di frequenza che determina la frequenza con cui viene eseguito il trigger. Se, ad esempio, interval è 3 e frequency è "week", il trigger si ripete ogni 3 settimane.
schedule | Un trigger con una frequenza specificata modifica la sua ricorrenza in base a una pianificazione di ricorrenza. Un oggetto schedule contiene modifiche in base a minuti, ore, giorni della settimana, giorni del mese e numero settimana.


### <a name="overview-schedule-trigger-schema-defaults-limits-and-examples"></a>Panoramica: Pianificare i valori predefiniti dello schema di trigger, i limiti ed esempi

Nome JSON | Tipo di valore | Obbligatorio? | Valore predefinito | Valori validi | Esempio
--------- | ---------- | --------- | ------------- | ------------ | -------
startTime | string | Sì | Nessuna | Date-Ore ISO-8601 | ```"startTime" : "2013-01-09T09:30:00-08:00"```
ricorrenza | Oggetto | Sì | Nessuna | Oggetto ricorrenza | ```"recurrence" : { "frequency" : "monthly", "interval" : 1 }```
interval | Number | No  | 1 | Da 1 a 1000. | ```"interval":10```
endTime | string | Sì | Nessuna | Il valore Data-Ora fa riferimento a un momento nel futuro | `"endTime" : "2013-02-09T09:30:00-08:00"`
schedule | Oggetto | No  | Nessuna | Oggetto pianificazione | `"schedule" : { "minute" : [30], "hour" : [8,17] }`

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


Nome JSON | DESCRIZIONE | Valori validi
--------- | ----------- | ------------
minutes | Minuti dell'ora in cui verrà eseguito il trigger. | <ul><li>Integer</li><li>Matrice di numeri interi</li></ul>
hours | Ore del giorno in cui verrà eseguito il trigger. | <ul><li>Integer</li><li>Matrice di numeri interi</li></ul>
weekDays | Giorni della settimana in cui verrà eseguito il trigger. Può essere specificato solo con una frequenza settimanale. | <ul><li>I valori consentiti sono Monday, Tuesday, Wednesday, Thursday, Friday, Saturday o Sunday</li><li>Matrice dei valori (dimensione massima della matrice: 7)</li></p>Non viene applicata la distinzione tra maiuscole e minuscole</p>
monthlyOccurrences | Determina in quali giorni del mese verrà eseguito il trigger. Può essere specificato solo con una frequenza mensile. | Matrice di oggetti monthlyOccurence: `{ "day": day,  "occurrence": occurence }`. <p> Il giorno della settimana in cui verrà eseguito il trigger, ad esempio `{Sunday}` corrisponde a ogni domenica del mese. Richiesto.<p>occurrence è l'occorrenza del giorno durante il mese, ad esempio `{Sunday, -1}` corrisponde all'ultima domenica del mese. facoltativo.
monthDays | Giorno del mese in cui verrà eseguito il trigger. Può essere specificato solo con una frequenza mensile. | <ul><li>Qualsiasi valore <= -1 e >= -31</li><li>Qualsiasi valore >= 1 e <= 31</li><li>Una matrice di valori</li>


## <a name="examples-recurrence-schedules"></a>Esempi: pianificazioni di ricorrenza
Questa sezione fornisce esempi di pianificazioni di ricorrenza con attenzione all'oggetto schedule e ai relativi elementi secondari.

Le pianificazioni di esempio presuppongono che l'intervallo sia impostato su 1. Inoltre, presupporre che la frequenza sia corretta in base al contenuto di schedule, ad esempio, non si può usare la frequenza "day" e modificare "monthDays" nella pianificazione. Queste restrizioni sono indicate nella tabella nella sezione precedente.

Esempio | DESCRIZIONE
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


## <a name="use-azure-powershell"></a>Usare Azure PowerShell
In questa sezione viene illustrato come usare Azure PowerShell per creare, avviare e monitorare un trigger di utilità di pianificazione. Se si desidera vedere questo esempio di utilizzo, vengono prima sottoposti il [Guida introduttiva: creazione di una data factory con Azure PowerShell](quickstart-create-data-factory-powershell.md). Quindi, aggiungere il codice seguente al metodo principale, che crea e avvia un trigger di pianificazione che viene eseguito ogni 15 minuti. Il trigger è associato a una pipeline (**Adfv2QuickStartPipeline**) creati come parte della Guida rapida.

1. Creare un file JSON denominato MyTrigger.json nella cartella C:\ADFv2QuickStartPSH\ con il seguente contenuto:

    > [!IMPORTANT]
    > Impostare **startTime** all'ora UTC corrente e **endTime** a un'ora dopo l'ora UTC corrente ora prima di salvare il file JSON.

    ```json   
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Minute",
                    "interval": 15,
                    "startTime": "2017-12-08T00:00:00",
                    "endTime": "2017-12-08T01:00:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "Adfv2QuickStartPipeline"
                    },
                    "parameters": {
                        "inputPath": "adftutorial/input",
                        "outputPath": "adftutorial/output"
                    }
                }
            ]
        }
    }
    ```

    Nel frammento di codice JSON:
    - Il **tipo** del trigger è impostato su **ScheduleTrigger**.
    - Il **frequenza** è impostato su **minuto** e **intervallo** è impostato su **15**. Pertanto, il trigger viene eseguita la pipeline di ogni 15 minuti tra l'ora di inizio e fine.
    - Il **endTime** è un'ora dopo la **startTime**, in modo che la pipeline viene eseguito il trigger 15 minuti, 30 minuti e 45 minuti dopo l'ora di inizio. Non dimenticare di aggiornare la proprietà startTime sulla endTime a un'ora dopo l'ora di inizio e l'ora UTC correnti.  
    - Il trigger è associato il **Adfv2QuickStartPipeline** pipeline. Per associare più pipeline con un trigger, aggiungere più **pipelineReference** sezioni.
    - La pipeline della Guida introduttiva accetta due **parametri**. È pertanto possibile passare i valori per i parametri dal trigger.
2. Creare un trigger utilizzando il **Set AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```
3. Verificare che lo stato del trigger sia **arrestato** utilizzando il **Get AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
4. Avviare il trigger utilizzando il **inizio AzureRmDataFactoryV2Trigger** cmdlet:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
5. Verificare che lo stato del trigger sia **Started** utilizzando il **Get AzureRmDataFactoryV2Trigger** cmdlet.

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```
6.  Ottenere trigger esegue con PowerShell tramite il **Get AzureRmDataFactoryV2TriggerRun** cmdlet. Per ottenere le informazioni sull'esecuzione di trigger, eseguire periodicamente il comando seguente: aggiornamento **TriggerRunStartedAfter** e **TriggerRunStartedBefore** valori corrispondono ai valori della definizione del trigger .

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```

    Per monitorare l'esecuzione di trigger/pipeline viene eseguito nel portale di Azure, vedere [esecuzione della pipeline di monitoraggio](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-net-sdk"></a>Usare .NET SDK
In questa sezione viene illustrato come utilizzare .NET SDK per creare, avviare e monitorare un trigger. Se si desidera visualizzare il codice di utilizzo, vengono prima sottoposti il [avvio rapido per la creazione di una data factory mediante .NET SDK](quickstart-create-data-factory-dot-net.md). Quindi, aggiungere il codice seguente al metodo principale, che crea e avvia un trigger di pianificazione che viene eseguito ogni 15 minuti. Il trigger è associato a una pipeline (**Adfv2QuickStartPipeline**) creati come parte della Guida rapida.

```csharp
            //create the trigger
            Console.WriteLine("Creating the trigger");

            // set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // set the start time to current UTC time and the end time to be one hour after.
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // now, create the trigger by invoking CreateOrUpdate method.
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            //start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);

```

Per monitorare un trigger di esecuzione, aggiungere il codice seguente prima dell'ultimo `Console.WriteLine` istruzione:

```csharp
            // Check the trigger runs every 15 minutes
            Console.WriteLine("Trigger runs. You see the output every 15 minutes");

            for (int i = 0; i < 3; i++)
            {
                System.Threading.Thread.Sleep(TimeSpan.FromMinutes(15));
                List<TriggerRun> triggerRuns = client.Triggers.ListRuns(resourceGroup, dataFactoryName, triggerName, DateTime.UtcNow.AddMinutes(-15 * (i + 1)), DateTime.UtcNow.AddMinutes(2)).ToList();
                Console.WriteLine("{0} trigger runs found", triggerRuns.Count);

                foreach (TriggerRun run in triggerRuns)
                {
                    foreach (KeyValuePair<string, string> triggeredPipeline in run.TriggeredPipelines)
                    {
                        PipelineRun triggeredPipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, triggeredPipeline.Value);
                        Console.WriteLine("Pipeline run ID: {0}, Status: {1}", triggeredPipelineRun.RunId, triggeredPipelineRun.Status);
                        List<ActivityRun> runs = client.ActivityRuns.ListByPipelineRun(resourceGroup, dataFactoryName, triggeredPipelineRun.RunId, run.TriggerRunTimestamp.Value, run.TriggerRunTimestamp.Value.AddMinutes(20)).ToList();
                    }
                }
            }
```

Per monitorare l'esecuzione di trigger/pipeline viene eseguito nel portale di Azure, vedere [esecuzione della pipeline di monitoraggio](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-python-sdk"></a>Utilizzare il SDK per Python
In questa sezione viene illustrato come utilizzare il SDK di Python per creare, avviare e monitorare un trigger. Se si desidera visualizzare il codice di utilizzo, vengono prima sottoposti il [avvio rapido per la creazione di una data factory con Python SDK](quickstart-create-data-factory-python.md). Aggiungere quindi il blocco di codice seguente dopo il blocco di codice "monitora la pipeline di esecuzione" nello script python. Questo codice crea un trigger di pianificazione che viene eseguito ogni 15 minuti tra l'inizio specificato e l'ora di fine. Aggiornare il start_time per l'ora UTC corrente e il end_time a un'ora successiva all'ora UTC corrente.

```python
    # Create a trigger
    tr_name = 'mytrigger'
    scheduler_recurrence = ScheduleTriggerRecurrence(frequency='Minute', interval='15',start_time='2017-12-12T04:00:00', end_time='2017-12-12T05:00:00', time_zone='UTC')
    pipeline_parameters = {'inputPath':'adftutorial/input', 'outputPath':'adftutorial/output'}
    pipelines_to_run = []
    pipeline_reference = PipelineReference('copyPipeline')
    pipelines_to_run.append(TriggerPipelineReference(pipeline_reference, pipeline_parameters))
    tr_properties = ScheduleTrigger(description='My scheduler trigger', pipelines = pipelines_to_run, recurrence=scheduler_recurrence)    
    adf_client.triggers.create_or_update(rg_name, df_name, tr_name, tr_properties)

    # start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```
Per monitorare l'esecuzione di trigger/pipeline viene eseguito nel portale di Azure, vedere [esecuzione della pipeline di monitoraggio](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline)

## <a name="use-resource-manager-template"></a>Usare i modelli di Resource Manager
È possibile utilizzare un modello di gestione risorse di Azure per creare un trigger. Per istruzioni dettagliate, vedere [creare una data factory di Azure utilizzando il modello di gestione risorse](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Passare l'ora di inizio del trigger a una pipeline
Nella versione 1, Azure Data Factory supportava la lettura o la scrittura di dati partizionati usando le variabili di sistema SliceStart/SliceEnd/WindowStart/WindowEnd. Nella versione 2, è possibile ottenere questo comportamento usando un parametro della pipeline e l'ora di inizio/ora prevista per il trigger come valore per il parametro. Nell'esempio seguente, ora pianificata del trigger viene passato come un valore per il scheduledRunTime parametro pipeline.

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    
Per ulteriori informazioni, vedere [come leggere o scrivere dati partizionati](how-to-read-write-partitioned-data.md).

## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sui trigger, vedere [della Pipeline di esecuzione e i trigger](concepts-pipeline-execution-triggers.md#triggers).
