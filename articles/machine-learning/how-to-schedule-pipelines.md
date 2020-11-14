---
title: Pianifica pipeline Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Le pipeline pianificate consentono di automatizzare le attività di routine, che richiedono molto tempo, ad esempio l'elaborazione, il training e il monitoraggio dei dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.topic: conceptual
ms.custom: how-to, devx-track-python
ms.openlocfilehash: 2c931aa6cd170c72654eb9ced6813d26e21ce5d0
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630312"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Pianificare le pipeline di Machine Learning con Azure Machine Learning SDK per Python

In questo articolo si apprenderà come pianificare una pipeline per l'esecuzione in Azure a livello di codice. È possibile scegliere di creare una pianificazione in base al tempo trascorso o alle modifiche del file System. È possibile usare pianificazioni basate sul tempo per gestire le attività di routine, ad esempio il monitoraggio della deriva dei dati. È possibile utilizzare pianificazioni basate sulle modifiche per rispondere a modifiche irregolari o imprevedibili, ad esempio il caricamento di nuovi dati o la modifica dei dati obsoleti. Dopo aver appreso come creare pianificazioni, si apprenderà come recuperarli e disattivarli.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree).

* Ambiente Python in cui è installato Azure Machine Learning SDK per Python. Per altre informazioni, vedere [creare e gestire ambienti riutilizzabili per il training e la distribuzione con Azure Machine Learning.](how-to-use-environments.md)

* Un'area di lavoro Machine Learning con una pipeline pubblicata. È possibile usare quello creato in [creare ed eseguire pipeline di Machine Learning con Azure Machine Learning SDK](how-to-create-your-first-pipeline.md).

## <a name="initialize-the-workspace--get-data"></a>Inizializzare l'area di lavoro & recuperare i dati

Per pianificare una pipeline, è necessario un riferimento all'area di lavoro, l'identificatore della pipeline pubblicata e il nome dell'esperimento in cui si vuole creare la pianificazione. È possibile ottenere questi valori con il codice seguente:

```Python
import azureml.core
from azureml.core import Workspace
from azureml.pipeline.core import Pipeline, PublishedPipeline
from azureml.core.experiment import Experiment

ws = Workspace.from_config()

experiments = Experiment.list(ws)
for experiment in experiments:
    print(experiment.name)

published_pipelines = PublishedPipeline.list(ws)
for published_pipeline in  published_pipelines:
    print(f"{published_pipeline.name},'{published_pipeline.id}'")

experiment_name = "MyExperiment" 
pipeline_id = "aaaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee" 
```

## <a name="create-a-schedule"></a>Creare una pianificazione

Per eseguire una pipeline in base a una pianificazione ricorrente, è necessario creare una pianificazione. Un oggetto `Schedule` associa una pipeline, un esperimento e un trigger. Il trigger può essere un oggetto `ScheduleRecurrence` che descrive l'attesa tra le esecuzioni o un percorso dell'archivio dati che specifica una directory da controllare per le modifiche. In entrambi i casi sarà necessario l'identificatore della pipeline e il nome dell'esperimento in cui creare la pianificazione.

Nella parte superiore del file Python importare le `Schedule` `ScheduleRecurrence` classi e:

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Creare una pianificazione basata sul tempo

Il `ScheduleRecurrence` costruttore dispone di un `frequency` argomento obbligatorio che deve essere una delle seguenti stringhe: "minute", "hour", "Day", "week" o "month". Richiede anche un argomento integer che `interval` specifica il numero di `frequency` unità che devono trascorrere tra l'inizio della pianificazione. Gli argomenti facoltativi consentono di essere più specifici dell'ora di inizio, come descritto in dettaglio nella [documentazione di SCHEDULERECURRENCE SDK](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?preserve-view=true&view=azure-ml-py).

Creare un `Schedule` che inizia un'esecuzione ogni 15 minuti:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Creare una pianificazione basata sulle modifiche

Le pipeline attivate dalle modifiche ai file possono essere più efficienti delle pianificazioni basate sul tempo. Ad esempio, è possibile eseguire un passaggio di pre-elaborazione quando un file viene modificato o quando viene aggiunto un nuovo file a una directory dei dati. È possibile monitorare le modifiche apportate a un archivio dati o alle modifiche all'interno di una directory specifica all'interno dell'archivio dati. Se si monitora una directory specifica, le modifiche apportate all'interno di sottodirectory della directory _non_ attiverà un'esecuzione.

Per creare un file reattivo `Schedule` , è necessario impostare il `datastore` parametro nella chiamata a [Schedule. Create](/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Per monitorare una cartella, impostare l' `path_on_datastore` argomento.

L' `polling_interval` argomento consente di specificare, in minuti, la frequenza con cui viene verificata la presenza di modifiche nell'archivio dati.

Se la pipeline è stata costruita con un [percorso di DataPath](/python/api/azureml-core/azureml.data.datapath.datapath?preserve-view=true&view=azure-ml-py) [PipelineParameter](/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?preserve-view=true&view=azure-ml-py), è possibile impostare tale variabile sul nome del file modificato impostando l' `data_path_parameter_name` argomento.

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on input file change.",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Argomenti facoltativi durante la creazione di una pianificazione

Oltre agli argomenti descritti in precedenza, è possibile impostare l' `status` argomento su `"Disabled"` per creare una pianificazione inattiva. Infine, `continue_on_step_failure` consente di passare un valore booleano che eseguirà l'override del comportamento di errore predefinito della pipeline.

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>Usare app per la logica di Azure per flussi di lavoro più complessi

App per la logica di Azure supporta flussi di lavoro più complessi ed è ampiamente integrato rispetto a Azure Machine Learning pipeline. Per altre informazioni, vedere [attivare un'esecuzione di una pipeline di machine learning da un'app per la logica](how-to-trigger-published-pipeline.md) .

## <a name="view-your-scheduled-pipelines"></a>Visualizzare le pipeline pianificate

Nella Web browser passare a Azure Machine Learning. Dalla sezione **endpoint** del pannello di navigazione scegliere **endpoint della pipeline**. Verrà quindi inviato un elenco delle pipeline pubblicate nell'area di lavoro.

![Pagina pipeline di AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

In questa pagina è possibile visualizzare informazioni di riepilogo su tutte le pipeline nell'area di lavoro: nomi, descrizioni, stato e così via. Eseguire il drill-through facendo clic sulla pipeline. Nella pagina risultante sono disponibili altri dettagli sulla pipeline ed è possibile eseguire il drill-down nelle singole esecuzioni.

## <a name="deactivate-the-pipeline"></a>Disattiva la pipeline

Se si dispone di un `Pipeline` pubblicato ma non pianificato, è possibile disabilitarlo con:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Se la pipeline è pianificata, è necessario annullare prima la pianificazione. Recuperare l'identificatore della pianificazione dal portale oppure eseguendo:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Quando si vuole `schedule_id` disabilitare, eseguire:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Se quindi si esegue `Schedule.list(ws)` nuovamente, si otterrà un elenco vuoto.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato usato l'SDK Azure Machine Learning per Python per pianificare una pipeline in due modi diversi. Una pianificazione si ripete in base al tempo di clock trascorso. L'altra pianificazione viene eseguita se un file viene modificato in un oggetto specificato `Datastore` o in una directory dell'archivio. Si è visto come usare il portale per esaminare la pipeline e le singole esecuzioni. Infine, si è appreso come disabilitare una pianificazione in modo che l'esecuzione della pipeline venga arrestata.

Per altre informazioni, vedere:

> [!div class="nextstepaction"]
> [Usare le pipeline di Azure Machine Learning per l'assegnazione del punteggio batch](tutorial-pipeline-batch-scoring-classification.md)

* Altre informazioni sulle [pipeline](concept-ml-pipelines.md)
* Scopri di più sull' [esplorazione Azure Machine Learning con Jupyter](samples-notebooks.md)