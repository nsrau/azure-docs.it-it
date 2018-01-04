---
title: Come creare trigger in Data Factory di Azure | Documenti Microsoft
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
ms.openlocfilehash: eed286c01604ab0e9ac2113d56cbce268503668d
ms.sourcegitcommit: aaba209b9cea87cb983e6f498e7a820616a77471
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2017
---
# <a name="how-to-create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Come creare un trigger che esegue una pipeline in una pianificazione
In questo articolo viene descritta la procedura per creare, avviare e monitorare un trigger. Per informazioni sui trigger, vedere [della Pipeline di esecuzione e i trigger](concepts-pipeline-execution-triggers.md).

> [!NOTE]
> Questo articolo si applica alla versione 2 del servizio Data Factory, attualmente in versione di anteprima. Se si usa la versione 1 del servizio Data Factory, disponibile a livello generale, vedere l'[introduzione a Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).


## <a name="use-azure-powershell"></a>Usare Azure PowerShell
In questa sezione viene illustrato come usare Azure PowerShell per creare, avviare e monitorare un trigger. Se si desidera vedere questo esempio di utilizzo, vengono prima sottoposti il [Guida introduttiva: creazione di una data factory con Azure PowerShell](quickstart-create-data-factory-powershell.md). Quindi, aggiungere il codice seguente al metodo principale, che crea e avvia un trigger di pianificazione che viene eseguito ogni 15 minuti. Il trigger è associato a una pipeline (**Adfv2QuickStartPipeline**) creati come parte della Guida rapida.

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