---
title: Creare trigger di pianificazione in Azure Data Factory | Microsoft Docs
description: Informazioni su come creare un trigger in Azure Data Factory per l'esecuzione di una pipeline in base a una pianificazione.
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
ms.date: 01/23/2018
ms.author: shlo
ms.openlocfilehash: 51e2dddbe66ca372d89fc8efeb24bdab9fe6a442
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/24/2018
---
# <a name="create-a-trigger-that-runs-a-pipeline-on-a-schedule"></a>Creare un trigger per l'esecuzione di una pipeline in base a una pianificazione
Questo articolo fornisce informazioni sui trigger di pianificazione e sulla procedura per creare, avviare e monitorare un trigger di pianificazione. Per altri tipi di trigger, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md).

Quando si crea un trigger di pianificazione, si specifica una pianificazione (data di inizio, ricorrenza, data di fine e così via) per il trigger e si associa a una pipeline. Pipeline e trigger hanno una relazione molti-a-molti. Più trigger possono attivare una singola pipeline. Un singolo trigger può attivare più pipeline.

> [!NOTE]
> Questo articolo è applicabile ad Azure Data Factory versione 2, attualmente in versione di anteprima. Se si usa Azure Data Factory versione 1, disponibile a livello generale, vedere [Introduzione ad Azure Data Factory versione 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).

Le sezioni successive illustrano la procedura per creare un trigger di pianificazione in modi diversi. 

## <a name="data-factory-ui"></a>Interfaccia utente di Data Factory
È possibile creare un **trigger di pianificazione** per pianificare l'esecuzione periodica di una pipeline (ogni ora, ogni giorno e così via). 

> [!NOTE]
> Per una procedura dettagliata e completa per creare una pipeline e un trigger di pianificazione, associare il trigger alla pipeline ed eseguire e monitorare la pipeline, vedere [Avvio rapido: Creare una data factory tramite interfaccia utente di Data Factory](quickstart-create-data-factory-portal.md).

1. Passare alla scheda **Modifica**. 

    ![Passare alla scheda Modifica](./media/how-to-create-schedule-trigger/switch-edit-tab.png)
1. Scegliere **Trigger** dal menu e quindi fare clic su **Nuovo/Modifica**. 

    ![Menu Nuovo trigger](./media/how-to-create-schedule-trigger/new-trigger-menu.png)
2. Nella pagina **Add Triggers** (Aggiungi trigger) fare clic su **Choose trigger** (Scegli trigger) e quindi su **Nuovo**. 

    ![Add triggers (Aggiungi trigger) - Nuovo trigger](./media/how-to-create-schedule-trigger/add-trigger-new-button.png)
3. Nella pagina **Nuovo trigger** seguire questa procedura: 

    1. Verificare che sia selezionata l'opzione **Pianificazione** per **Tipo**. 
    2. Specificare il valore datetime di inizio del trigger per **Data di inizio (UTC)**. Per impostazione predefinita, questa opzione è impostata sul valore datetime corrente. 
    3. Specificare **Ricorrenza** per il trigger. Selezionare uno dei valori dall'elenco a discesa (Ogni minuto, Ogni ora, Ogni giorno, Ogni settimana e Ogni mese). Immettere il moltiplicatore nella casella di testo. Ad esempio, se si vuole impostare l'esecuzione del trigger una volta ogni 15 minuti, selezionare **Ogni minuto** e immettere **15** nella casella di testo. 
    4. Per il campo **Fine**, se non si vuole specificare un valore datetime di fine per il trigger, selezionare **No End** (Nessuna data di fine). Per specificare un valore datetime di fine, selezionare **On Date** (In data), specificare un valore datetime di fine e fare clic su **Applica**. Ogni esecuzione della pipeline è associata a un costo. Se si stanno eseguendo test, si consiglia di verificare che la pipeline sia attivata solo un paio di volte. Assicurarsi tuttavia che il tempo specificato tra l'ora di pubblicazione e l'ora di fine sia sufficiente per l'esecuzione della pipeline. Il trigger viene applicato solo dopo la pubblicazione della soluzione in Data Factory, non quando si salva il trigger nell'interfaccia utente.

        ![Impostazioni di trigger](./media/how-to-create-schedule-trigger/trigger-settings.png)
4. Nella finestra **Nuovo trigger** selezionare l'opzione **Attivato** e fare clic su **Avanti**. È possibile usare questa casella di controllo per disattivare il trigger in un secondo momento. 

    ![Impostazioni del trigger - Pulsante Avanti](./media/how-to-create-schedule-trigger/trigger-settings-next.png)
5. Nella pagina **Nuovo trigger** verificare il messaggio di avviso e quindi fare clic su **Fine**.

    ![Impostazioni del trigger - Pulsante Fine](./media/how-to-create-schedule-trigger/new-trigger-finish.png)
6. Fare clic su **Pubblica** per pubblicare le modifiche in Data Factory. Fino a quando si pubblicano le modifiche in Data Factory, il trigger non avvia le esecuzioni di pipeline. 

    ![Pulsante Publish](./media/how-to-create-schedule-trigger/publish-2.png)
8. Passare alla scheda **Monitoraggio** a sinistra. Fare clic su **Aggiorna** per aggiornare l'elenco. Verranno visualizzate le esecuzioni di pipeline attivate dal trigger pianificato. Notare i valori della colonna **Attivato da**. Se si usa l'opzione **Trigger Now** (Attiva adesso), verrà visualizzata l'esecuzione del trigger manuale nell'elenco. 

    ![Monitorare le esecuzioni attivate](./media/how-to-create-schedule-trigger/monitor-triggered-runs.png)
9. Fare clic sulla freccia GIÙ accanto a **Pipeline Runs** (Esecuzioni di pipeline) per passare alla visualizzazione **Trigger Runs** (Esecuzioni del trigger). 

    ![Monitorare le esecuzioni del trigger](./media/how-to-create-schedule-trigger/monitor-trigger-runs.png)

## <a name="azure-powershell"></a>Azure PowerShell
Questa sezione illustra come usare Azure PowerShell per creare, avviare e monitorare un trigger di pianificazione. Per vedere l'esempio in esecuzione, fare riferimento ad [Avvio rapido: Creare una data factory con Azure PowerShell](quickstart-create-data-factory-powershell.md). Quindi, aggiungere il codice seguente al metodo principale, che crea e avvia un trigger di pianificazione che viene eseguito ogni 15 minuti. Il trigger è associato a una pipeline denominata **Adfv2QuickStartPipeline**, creata come parte dell'Avvio rapido.

1. Creare un file JSON denominato **MyTrigger.json** nella cartella C:\ADFv2QuickStartPSH con il contenuto seguente:

    > [!IMPORTANT]
    > Prima di salvare il file JSON, impostare il valore dell'elemento **startTime** sull'ora UTC corrente. Impostare il valore dell'elemento **endTime** su un'ora dopo l'ora UTC corrente.

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
    - L'elemento **type** del trigger è impostato su "ScheduleTrigger".
    - L'elemento **frequency** è impostato su "Minute" e l'elemento **interval** è impostato su 15. Pertanto, il trigger esegue la pipeline ogni 15 minuti tra le ore di inizio e di fine.
    - L'elemento **endTime** è un'ora dopo il valore dell'elemento **startTime**. Pertanto, il trigger esegue la pipeline 15 minuti, 30 minuti e 45 minuti dopo l'ora di inizio. Non dimenticare di aggiornare l'ora di inizio impostandola sull'ora UTC corrente e l'ora di fine impostandola su un'ora dopo l'ora di inizio. 
    - Il trigger è associato alla pipeline **Adfv2QuickStartPipeline**. Per associare più pipeline a un trigger, aggiungere più sezioni **pipelineReference**.
    - La pipeline dell'Avvio rapido accetta due valori **parameters**: **inputPath** e **outputPath**. È pertanto possibile passare i valori per questi parametri dal trigger.

2. Creare un trigger usando il cmdlet **Start-AzureRmDataFactoryV2Trigger**:

    ```powershell
    Set-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger" -DefinitionFile "C:\ADFv2QuickStartPSH\MyTrigger.json"
    ```

3. Confermare che lo stato del trigger viene **interrotto** usando il cmdlet **Get-AzureRmDataFactoryV2Trigger**:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

4. Avviare il trigger usando il cmdlet **Start-AzureRmDataFactoryV2Trigger**:

    ```powershell
    Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

5. Confermare che lo stato del trigger viene **avviato** usando il cmdlet **Get-AzureRmDataFactoryV2Trigger**:

    ```powershell
    Get-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"
    ```

6.  Ottenere le esecuzioni di trigger in Azure PowerShell usando il cmdlet **Get AzureRmDataFactoryV2TriggerRun**. Per ottenere informazioni sulle esecuzioni di trigger, eseguire periodicamente il comando seguente. Aggiornare i valori di **TriggerRunStartedAfter** e **TriggerRunStartedBefore** in modo che corrispondano a quelli nella definizione del trigger:

    ```powershell
    Get-AzureRmDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-08T00:00:00" -TriggerRunStartedBefore "2017-12-08T01:00:00"
    ```
    
    Per monitorare le esecuzioni di trigger e di pipeline nel portale di Azure, vedere [Monitorare le esecuzioni di pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="net-sdk"></a>.NET SDK
Questa sezione illustra come usare .NET SDK per creare, avviare e monitorare un trigger. Per vedere l'esempio in esecuzione, fare riferimento ad [Avvio rapido: Creare una data factory con .NET SDK](quickstart-create-data-factory-dot-net.md). Quindi, aggiungere il codice seguente al metodo principale, che crea e avvia un trigger di pianificazione che viene eseguito ogni 15 minuti. Il trigger è associato a una pipeline denominata **Adfv2QuickStartPipeline**, creata come parte dell'Avvio rapido.

Per creare e avviare un trigger di pianificazione che viene eseguito ogni 15 minuti, aggiungere il codice seguente al metodo principale:

```csharp
            // Create the trigger
            Console.WriteLine("Creating the trigger");

            // Set the start time to the current UTC time
            DateTime startTime = DateTime.UtcNow;

            // Specify values for the inputPath and outputPath parameters
            Dictionary<string, object> pipelineParameters = new Dictionary<string, object>();
            pipelineParameters.Add("inputPath", "adftutorial/input");
            pipelineParameters.Add("outputPath", "adftutorial/output");

            // Create a schedule trigger
            string triggerName = "MyTrigger";
            ScheduleTrigger myTrigger = new ScheduleTrigger()
            {
                Pipelines = new List<TriggerPipelineReference>()
                {
                    // Associate the Adfv2QuickStartPipeline pipeline with the trigger
                    new TriggerPipelineReference()
                    {
                        PipelineReference = new PipelineReference(pipelineName),
                        Parameters = pipelineParameters,
                    }
                },
                Recurrence = new ScheduleTriggerRecurrence()
                {
                    // Set the start time to the current UTC time and the end time to one hour after the start time
                    StartTime = startTime,
                    TimeZone = "UTC",
                    EndTime = startTime.AddHours(1),
                    Frequency = RecurrenceFrequency.Minute,
                    Interval = 15,
                }
            };

            // Now, create the trigger by invoking the CreateOrUpdate method
            TriggerResource triggerResource = new TriggerResource()
            {
                Properties = myTrigger
            };
            client.Triggers.CreateOrUpdate(resourceGroup, dataFactoryName, triggerName, triggerResource);

            // Start the trigger
            Console.WriteLine("Starting the trigger");
            client.Triggers.Start(resourceGroup, dataFactoryName, triggerName);
```

Per monitorare l'esecuzione di un trigger, aggiungere il codice seguente prima dell'ultima istruzione `Console.WriteLine` nell'esempio:

```csharp
            // Check that the trigger runs every 15 minutes
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

Per monitorare le esecuzioni di trigger e di pipeline nel portale di Azure, vedere [Monitorare le esecuzioni di pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).


## <a name="python-sdk"></a>Python SDK
Questa sezione illustra come usare Python SDK per creare, avviare e monitorare un trigger. Per vedere l'esempio in esecuzione, fare riferimento ad [Avvio rapido: Creare una data factory con Python SDK](quickstart-create-data-factory-python.md). Aggiungere quindi il blocco di codice seguente dopo il blocco di codice di "monitoraggio dell'esecuzione della pipeline" nello script Python. Questo codice crea un trigger di pianificazione che viene eseguito ogni 15 minuti tra le ore di inizio e di fine specificate. Aggiornare la variabile **start_time** impostandola sull'ora UTC corrente e la variabile **end_time** impostandola su un'ora dopo l'ora UTC corrente.

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

    # Start the trigger
    adf_client.triggers.start(rg_name, df_name, tr_name)
```

Per monitorare le esecuzioni di trigger e di pipeline nel portale di Azure, vedere [Monitorare le esecuzioni di pipeline](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).

## <a name="azure-resource-manager-template"></a>Modello di Azure Resource Manager
È possibile usare un modello di Azure Resource Manager per creare un trigger. Per istruzioni dettagliate, vedere [Creare una data factory di Azure usando un modello di Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md).  

## <a name="pass-the-trigger-start-time-to-a-pipeline"></a>Passare l'ora di inizio del trigger a una pipeline
Azure Data Factory versione 1 supporta la lettura o la scrittura di dati partizionati usando le variabili di sistema **SliceStart**, **SliceEnd**, **WindowStart** e **WindowEnd**. In Azure Data Factory versione 2 è possibile ottenere questo comportamento usando un parametro della pipeline. L'ora di inizio e l'ora pianificata per il trigger vengono impostate come valore per il parametro della pipeline. Nell'esempio seguente l'ora pianificata per il trigger viene passata come valore al parametro **scheduledRunTime** della pipeline:

```json
"parameters": {
    "scheduledRunTime": "@trigger().scheduledTime"
}
```    

Per altre informazioni, vedere le istruzioni in [Come leggere o scrivere dati partizionati](how-to-read-write-partitioned-data.md).

## <a name="json-schema"></a>Schema JSON
La definizione JSON seguente illustra come creare un trigger di pianificazione con pianificazione e ricorrenza:

```json
{
  "properties": {
    "type": "ScheduleTrigger",
    "typeProperties": {
      "recurrence": {
        "frequency": <<Minute, Hour, Day, Week, Month>>,
        "interval": <<int>>,             // Optional, specifies how often to fire (default to 1)
        "startTime": <<datetime>>,
        "endTime": <<datetime - optional>>,
        "timeZone": "UTC"
        "schedule": {                    // Optional (advanced scheduling specifics)
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
>  La proprietà **parameters** è una proprietà obbligatoria dell'elemento **pipelines**. Se la pipeline non accetta parametri, è necessario includere una definizione JSON vuota per la proprietà **parameters**.


### <a name="schema-overview"></a>Panoramica dello schema
La tabella seguente fornisce una panoramica generale degli elementi dello schema principali correlati alla ricorrenza e alla pianificazione di un trigger:

| Proprietà JSON | DESCRIZIONE |
|:--- |:--- |
| **startTime** | Valore di data e ora. Per le pianificazioni semplici, il valore della proprietà **startTime** si applica alla prima occorrenza. Per le pianificazioni complesse, il trigger viene attivato non prima del valore **startTime** specificato. |
| **endTime** | Data e ora di fine per il trigger. Il trigger non viene eseguito dopo la data e l'ora di fine specificate. Il valore della proprietà non può essere nel passato. Questa proprietà è facoltativa. |
| **timeZone** | Fuso orario. È attualmente supportato solo il fuso orario UTC. |
| **recurrence** | Oggetto recurrence che specifica le regole di ricorrenza per il trigger. L'oggetto recurrence supporta gli elementi **frequency**, **interval**, **endTime**, **count** e **schedule**. Quando viene definito un oggetto recurrence, l'elemento **frequency** è obbligatorio. Gli altri elementi dell'oggetto recurrence sono facoltativi. |
| **frequency** | Unità di frequenza con cui il trigger si ripete. I valori supportati includono "minute", "hour", "day", "week" e "month". |
| **interval** | Numero intero positivo indicante l'intervallo per il valore **frequency**, che determina la frequenza con cui viene eseguito il trigger. Se, ad esempio, **interval** è 3 e **frequency** è "week", il trigger si ripete ogni 3 settimane. |
| **schedule** | Pianificazione della ricorrenza per il trigger. Un trigger con un valore **frequency** specificato modifica la ricorrenza in base a una pianificazione di ricorrenza. La proprietà **schedule** contiene modifiche per la ricorrenza basate su minuti, ore, giorni della settimana, giorni del mese e numero della settimana.


### <a name="schema-defaults-limits-and-examples"></a>Impostazioni predefinite dello schema, limiti ed esempi

| Proprietà JSON | type | Obbligatoria | Valore predefinito | Valori validi | Esempio |
|:--- |:--- |:--- |:--- |:--- |:--- |
| **startTime** | string | Sì | Nessuna | Date-Ore ISO-8601 | `"startTime" : "2013-01-09T09:30:00-08:00"` |
| **recurrence** | Oggetto | Sì | Nessuna | Oggetto ricorrenza | `"recurrence" : { "frequency" : "monthly", "interval" : 1 }` |
| **interval** | Number | No  | 1 | Da 1 a 1.000 | `"interval":10` |
| **endTime** | string | Sì | Nessuna | Valore di data e ora che fa riferimento a un momento nel futuro. | `"endTime" : "2013-02-09T09:30:00-08:00"` |
| **schedule** | Oggetto | No  | Nessuna | Oggetto pianificazione | `"schedule" : { "minute" : [30], "hour" : [8,17] }` |

### <a name="starttime-property"></a>Proprietà startTime
La tabella seguente illustra come la proprietà **startTime** controlla l'esecuzione di un trigger:

| Valore startTime | Ricorrenza senza pianificazione | Ricorrenza con pianificazione |
|:--- |:--- |:--- |
| Ora di inizio nel passato | Calcola l'ora della prima esecuzione futura dopo l'ora di inizio e avvia l'esecuzione in corrispondenza di tale ora.<br/><br/>Avvia le esecuzioni successive basandosi sul calcolo dall'ultima esecuzione.<br/><br/>Vedere l'esempio sotto a questa tabella. | Il trigger viene avviato _non prima_ dell'ora di inizio specificata. La prima occorrenza è basata sulla pianificazione calcolata dall'ora di inizio.<br/><br/>Avvia le esecuzioni successive in base alla pianificazione di ricorrenza. |
| Ora di inizio nel futuro o nel presente | Avvia l'esecuzione una sola volta all'ora di inizio specificata.<br/><br/>Avvia le esecuzioni successive basandosi sul calcolo dall'ultima esecuzione. | Il trigger viene avviato _non prima_ dell'ora di inizio specificata. La prima occorrenza è basata sulla pianificazione calcolata dall'ora di inizio.<br/><br/>Avvia le esecuzioni successive in base alla pianificazione di ricorrenza. |

È possibile esaminare uno scenario in cui l'ora di inizio è nel passato, con una ricorrenza, ma nessuna pianificazione. Si supponga che l'ora corrente sia `2017-04-08 13:00`, l'ora di inizio sia `2017-04-07 14:00` e la ricorrenza sia ogni due giorni. Il valore **recurrence** viene definito impostando la proprietà **frequency** su "day" e la proprietà **interval** su 2. Si noti che il valore **startTime** è nel passato e si verifica prima dell'ora corrente.

In queste condizioni, la prima esecuzione avviene alle `2017-04-09 at 14:00`. Il motore dell'utilità di pianificazione calcola le occorrenze dall'ora di inizio dell'esecuzione. Vengono eliminate tutte le istanze in passato. Il motore utilizza l'istanza successiva che si verifica in futuro. In questo scenario l'ora di inizio è `2017-04-07 at 2:00pm`, quindi l'istanza successiva viene eseguita due giorni dopo tale orario, ovvero il giorno `2017-04-09 at 2:00pm`.

Si noti che la prima esecuzione sarebbe la stessa anche se il valore **startTime** fosse `2017-04-05 14:00` o `2017-04-01 14:00`. Dopo la prima esecuzione, le esecuzioni successive vengono calcolate in base alla pianificazione. Le esecuzioni successive vengono quindi avviate il `2017-04-11 at 2:00pm`, poi il `2017-04-13 at 2:00pm`, quindi il `2017-04-15 at 2:00pm` e così via.

Quando infine le ore o i minuti non sono impostati nella pianificazione di un trigger, le ore o i minuti della prima esecuzione vengono usati come impostazioni predefinite.

### <a name="schedule-property"></a>Proprietà schedule
Da un lato, l'uso di una pianificazione può limitare il numero di esecuzioni di un trigger. Se ad esempio la pianificazione di un trigger con frequenza mensile prevede l'esecuzione solo il giorno 31, il trigger viene eseguito solo nei mesi che includono 31 giorni.

Un oggetto schedule può tuttavia anche aumentare il numero di esecuzioni di un trigger. Ad esempio, un trigger con una frequenza mensile la cui esecuzione è pianificata per i giorni 1 e 2 del mese viene eseguito il primo e il secondo giorno del mese, invece che una volta al mese.

Se vengono specificati più elementi **schedule**, l'ordine di valutazione è dall'impostazione della pianificazione più grande alla più piccola. La valutazione inizia con il numero della settimana, quindi prosegue con il giorno del mese, il giorno della settimana, l'ora e infine il minuto.

La tabella seguente illustra in modo dettagliato gli elementi **schedule**:


| Elemento JSON | DESCRIZIONE | Valori validi |
|:--- |:--- |:--- |
| **minutes** | Minuti dell'ora in cui verrà eseguito il trigger. | <ul><li>Integer</li><li>Matrice di numeri interi</li></ul>
| **hours** | Ore del giorno in cui verrà eseguito il trigger. | <ul><li>Integer</li><li>Matrice di numeri interi</li></ul> |
| **weekDays** | Giorni della settimana in cui verrà eseguito il trigger. Il valore può essere specificato solo con una frequenza settimanale. | <ul><li>I valori consentiti sono Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday</li><li>Matrice di valori in giorni (la dimensione massima della matrice è 7)</li><li>I valori dei giorni non fanno distinzione tra maiuscole e minuscole</li></ul> |
| **monthlyOccurrences** | Giorni del mese in cui verrà eseguito il trigger. Il valore può essere specificato solo con una frequenza mensile. | <ul><li>Matrice di oggetti **monthlyOccurence**: `{ "day": day,  "occurrence": occurence }`.</li><li>L'attributo **day** è il giorno della settimana in cui verrà eseguito il trigger. Ad esempio, una proprietà **monthlyOccurrences** con un valore **day** uguale a `{Sunday}` indica ogni domenica del mese. L'attributo **day** è obbligatorio.</li><li>L'attributo **occurrence** indica l'occorrenza dell'attributo **day** specificato durante il mese. Ad esempio, una proprietà **monthlyOccurrences** con i valori **day** e **occurrence** uguali a `{Sunday, -1}` indica l'ultima domenica del mese. L'attributo **occurrence** è facoltativo.</li></ul> |
| **monthDays** | Giorno del mese in cui verrà eseguito il trigger. Il valore può essere specificato solo con una frequenza mensile. | <ul><li>Qualsiasi valore <= -1 e >= -31</li><li>Qualsiasi valore >= 1 e <= 31</li><li>Matrice di valori</li></ul> |


## <a name="examples-of-trigger-recurrence-schedules"></a>Esempi di pianificazioni di ricorrenza del trigger
Questa sezione fornisce esempi di pianificazioni di ricorrenza con attenzione all'oggetto **schedule** e ai relativi elementi.

Gli esempi presumono che il valore **interval** sia 1 e che il valore **frequency** sia corretto in base alla definizione della pianificazione. Non è ad esempio possibile avere un valore **frequency** uguale a "day" e anche una modifica "monthDays" nell'oggetto **schedule**. Restrizioni come queste sono indicate nella tabella nella sezione precedente.

| Esempio | DESCRIZIONE |
|:--- |:--- |
| `{"hours":[5]}` | Viene eseguito alle 5:00 ogni giorno. |
| `{"minutes":[15], "hours":[5]}` | Viene eseguito alle 05:15 ogni giorno. |
| `{"minutes":[15], "hours":[5,17]}` | Viene eseguito alle 05:15 e alle 17:15 ogni giorno. |
| `{"minutes":[15,45], "hours":[5,17]}` | Viene eseguito alle 05:15, alle 05:45, alle 17:15 e alle 17:45 ogni giorno. |
| `{"minutes":[0,15,30,45]}` | Viene eseguito ogni 15 minuti. |
| `{hours":[0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23]}` | Viene eseguito ogni ora. Il trigger viene eseguito ogni ora. I minuti vengono controllati dal valore **startTime**, se specificato. Se non è specificato un valore, i minuti vengono controllati dall'ora di creazione. Se ad esempio l'ora di inizio o l'ora di creazione (qualunque delle due si applichi) è 12:25, il trigger viene eseguito alle 00:25, 01:25, 02:25, ..., 23:25.<br/><br/>Questa pianificazione equivale a un trigger con un valore **frequency** pari a "hour", un valore **interval** di 1 e nessun oggetto **schedule**.  Questa pianificazione può essere usata con valori diversi per **frequency** e **interval** per creare altri trigger. Quando ad esempio il valore **frequency** è "month", la pianificazione viene eseguita solo una volta al mese, invece che ogni giorno, quando il valore **frequency** è "day". |
| `{"minutes":[0]}` | Viene eseguito ogni ora all'inizio dell'ora. Questo trigger viene eseguito ogni ora con l'ora che inizia alle 00:00, 01:00, 02:00 e così via.<br/><br/>Questa pianificazione equivale a un trigger con un valore **frequency** pari a "hour" e un valore **startTime** di zero minuti oppure nessun oggetto **schedule**, ma un valore **frequency** pari a "day". Se il valore **frequency** è "week" o "month", la pianificazione viene eseguita rispettivamente solo una volta alla settimana o una volta al giorno. |
| `{"minutes":[15]}` | Viene eseguito 15 minuti dopo l'inizio di ogni ora. Questo trigger viene eseguito ogni ora, 15 minuti dopo l'inizio dell'ora a partire dalle 00:15, 01:15, 02:15 e così via e terminando alle 23:15. |
| `{"hours":[17], "weekDays":["saturday"]}` | Viene eseguito alle 17:00 di ogni sabato. |
| `{"hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Viene eseguito alle 17:00 di ogni lunedì, mercoledì e venerdì. |
| `{"minutes":[15,45], "hours":[17], "weekDays":["monday", "wednesday", "friday"]}` | Viene eseguito alle 17:15 e alle 17:45 di ogni lunedì, mercoledì e venerdì. |
| `{"minutes":[0,15,30,45], "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Viene eseguito ogni 15 minuti nei giorni feriali. |
| `{"minutes":[0,15,30,45], "hours": [9, 10, 11, 12, 13, 14, 15, 16] "weekDays":["monday", "tuesday", "wednesday", "thursday", "friday"]}` | Viene eseguito ogni 15 minuti nei giorni feriali tra le 09:00 e le 16:45. |
| `{"weekDays":["tuesday", "thursday"]}` | Viene eseguito ogni martedì e giovedì all'ora di inizio specificata. |
| `{"minutes":[0], "hours":[6], "monthDays":[28]}` | Viene eseguito alle 06:00 il ventottesimo giorno di ogni mese (presumendo che il valore di **frequency** sia "month"). |
| `{"minutes":[0], "hours":[6], "monthDays":[-1]}` | Viene eseguito alle 06:00 dell'ultimo giorno di ogni mese. Per eseguire un trigger l'ultimo giorno del mese, usare -1 invece del giorno 28, 29, 30 o 31. |
| `{"minutes":[0], "hours":[6], "monthDays":[1,-1]}` | Viene eseguito alle 06:00 il primo e l'ultimo giorno di ogni mese. |
| `{monthDays":[1,14]}` | Viene eseguito il primo e il quattordicesimo giorno di ogni mese all'ora di inizio specificata. |
| `{"minutes":[0], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Viene eseguito il primo venerdì di ogni mese alle 05:00. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1}]}` | Viene eseguito il primo venerdì di ogni mese all'ora di inizio specificata. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":-3}]}` | Viene eseguito il terzultimo venerdì di ogni mese, all'ora di inizio specificata. |
| `{"minutes":[15], "hours":[5], "monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Viene eseguito il primo e l'ultimo venerdì di ogni mese alle 05:15. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":1},{"day":"friday", "occurrence":-1}]}` | Viene eseguito il primo e l'ultimo venerdì di ogni mese all'ora di inizio specificata. |
| `{"monthlyOccurrences":[{"day":"friday", "occurrence":5}]}` | Viene eseguito il quinto venerdì di ogni mese all'ora di inizio specificata. Quando non ci sono cinque venerdì nel mese, la pipeline non viene eseguita, perché è pianificata per essere eseguita solo il quinto venerdì del mese. È consigliabile usare -1 invece di 5 per il valore **occurrence** per eseguire il trigger l'ultimo venerdì del mese. |
| `{"minutes":[0,15,30,45], "monthlyOccurrences":[{"day":"friday", "occurrence":-1}]}` | Viene eseguito ogni 15 minuti l'ultimo venerdì del mese. |
| `{"minutes":[15,45], "hours":[5,17], "monthlyOccurrences":[{"day":"wednesday", "occurrence":3}]}` | Viene eseguito alle 05:15, 05:45, 17:15 e 17:45 il terzo mercoledì di ogni mese. |


## <a name="next-steps"></a>Passaggi successivi
Per informazioni dettagliate sui trigger, vedere [Esecuzione e trigger di pipeline](concepts-pipeline-execution-triggers.md#triggers).
