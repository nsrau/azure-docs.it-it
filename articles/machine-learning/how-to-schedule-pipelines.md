---
title: Pianificare le pipeline di Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Pianificare pipeline di Azure Machine Learning usando Azure Machine Learning SDK per Python.Schedule Azure Machine Learning pipelines using the Azure Machine Learning SDK for Python. Le pipeline pianificate consentono di automatizzare attività di routine che richiedono molto tempo, ad esempio l'elaborazione dei dati, il training e il monitoraggio.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: laobri
author: lobrien
ms.date: 11/12/2019
ms.openlocfilehash: d9e1bff3d25a978b5159d8e6ab8ab2453df77ca3
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2020
ms.locfileid: "81640504"
---
# <a name="schedule-machine-learning-pipelines-with-azure-machine-learning-sdk-for-python"></a>Pianificare pipeline di Machine Learning con Azure Machine Learning SDK per Python

In this article, you'll learn how to programmatically schedule a pipeline to run on Azure. È possibile scegliere di creare una pianificazione in base al tempo trascorso o alle modifiche apportate al file system. Le pianificazioni basate sul tempo possono essere utilizzate per gestire le attività di routine, ad esempio il monitoraggio della deriva dei dati. Le pianificazioni basate sulle modifiche possono essere utilizzate per reagire a modifiche irregolari o imprevedibili, ad esempio nuovi dati caricati o vecchi dati in fase di modifica. Dopo aver appreso come creare le pianificazioni, imparerai a recuperarle e disattivarle.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si dispone di una sottoscrizione di Azure, creare un [account gratuito.](https://aka.ms/AMLFree)

* Ambiente Python in cui è installato Azure Machine Learning SDK per Python. Per altre informazioni, vedere [Creare e gestire ambienti riutilizzabili per la formazione e la distribuzione con Azure Machine Learning.For more information, see Create and manage reusable environments for training and deployment with Azure Machine Learning.](how-to-use-environments.md)

* Area di lavoro di Machine Learning con una pipeline pubblicata. È possibile usare quella compilata in Creare ed eseguire pipeline di [Machine Learning con Azure Machine Learning SDK.](how-to-create-your-first-pipeline.md)

## <a name="initialize-the-workspace--get-data"></a>Inizializzare l'area di lavoro & ottenere i dati

Per pianificare una pipeline, è necessario un riferimento all'area di lavoro, l'identificatore della pipeline pubblicata e il nome dell'esperimento in cui si vuole creare la pianificazione. È possibile ottenere questi valori con il codice seguente:You can get these values with the following code:

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

Per eseguire una pipeline su base ricorrente, è necessario creare una pianificazione. Un `Schedule` oggetto associa una pipeline, un esperimento e un trigger. Il trigger può`ScheduleRecurrence` essere un oggetto che descrive l'attesa tra le esecuzioni o un percorso di Datastore che specifica una directory per controllare le modifiche. In entrambi i casi, sono necessari l'identificatore della pipeline e il nome dell'esperimento in cui creare la pianificazione.

Nella parte superiore del file `Schedule` python, importa le classi e: `ScheduleRecurrence`

```python

from azureml.pipeline.core.schedule import ScheduleRecurrence, Schedule
```

### <a name="create-a-time-based-schedule"></a>Creare una pianificazione basata sul tempo

Il `ScheduleRecurrence` costruttore `frequency` ha un argomento obbligatorio che deve essere una delle seguenti stringhe: "Minute", "Hour", "Day", "Week" o "Month". Richiede inoltre un `interval` argomento intero che `frequency` specifica quante unità devono trascorrere tra gli inizi della pianificazione. Gli argomenti facoltativi consentono di essere più specifici sugli orari di inizio, come descritto nei documenti di [ScheduleRecurrence SDK](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedulerecurrence?view=azure-ml-py).

Creare `Schedule` un che inizia una corsa ogni 15 minuti:Create a that begins a run every 15 minutes:

```python
recurrence = ScheduleRecurrence(frequency="Minute", interval=15)
recurring_schedule = Schedule.create(ws, name="MyRecurringSchedule", 
                            description="Based on time",
                            pipeline_id=pipeline_id, 
                            experiment_name=experiment_name, 
                            recurrence=recurrence)
```

### <a name="create-a-change-based-schedule"></a>Creare una pianificazione basata sulle modifiche

Le pipeline attivate dalle modifiche dei file possono essere più efficienti rispetto alle pianificazioni basate sul tempo. Ad esempio, è possibile eseguire un passaggio di pre-elaborazione quando un file viene modificato o quando un nuovo file viene aggiunto a una directory di dati. È possibile monitorare qualsiasi modifica apportata a un archivio dati o all'interno di una directory specifica all'interno dell'archivio dati. Se si monitora una directory specifica, le modifiche all'interno delle sottodirectory di tale directory _non_ attiveranno un'esecuzione.

Per creare una riattiva `Schedule`su file `datastore` , è necessario impostare il parametro nella chiamata su [Schedule.create](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.schedule.schedule?view=azure-ml-py#create-workspace--name--pipeline-id--experiment-name--recurrence-none--description-none--pipeline-parameters-none--wait-for-provisioning-false--wait-timeout-3600--datastore-none--polling-interval-5--data-path-parameter-name-none--continue-on-step-failure-none--path-on-datastore-none---workflow-provider-none---service-endpoint-none-). Per monitorare una `path_on_datastore` cartella, impostare l'argomento.

L'argomento `polling_interval` consente di specificare, in minuti, la frequenza con cui l'archivio dati viene controllato per le modifiche.

Se la pipeline è stata costruita con un [DataPath](https://docs.microsoft.com/python/api/azureml-core/azureml.data.datapath.datapath?view=azure-ml-py) [PipelineParameter](https://docs.microsoft.com/python/api/azureml-pipeline-core/azureml.pipeline.core.pipelineparameter?view=azure-ml-py), è `data_path_parameter_name` possibile impostare tale variabile sul nome del file modificato impostando l'argomento .

```python
datastore = Datastore(workspace=ws, name="workspaceblobstore")

reactive_schedule = Schedule.create(ws, name="MyReactiveSchedule", description="Based on time",
                            pipeline_id=pipeline_id, experiment_name=experiment_name, datastore=datastore, data_path_parameter_name="input_data")
```

### <a name="optional-arguments-when-creating-a-schedule"></a>Argomenti facoltativi durante la creazione di una pianificazioneOptional arguments when creating a schedule

Oltre agli argomenti descritti in precedenza, è possibile impostare l'argomento `status` `"Disabled"` su per creare una pianificazione inattiva. Infine, `continue_on_step_failure` consente di passare un valore booleano che eseguirà l'override del comportamento di errore predefinito della pipeline.

### <a name="use-azure-logic-apps-for-more-complex-workflows"></a>Usare le app per la logica di Azure per flussi di lavoro più complessiUse Azure Logic Apps for more complex workflows

App per la logica di Azure supporta flussi di lavoro più complessi ed è molto più ampiamente integrato rispetto alle pipeline di Azure Machine Learning.Azure Logic Apps supports more complex workflows and is molto more broadly integrated than Azure Machine Learning pipelines. Per altre informazioni, vedere [Attivare un'esecuzione di una pipeline di Machine Learning da un'app per la logica.](how-to-trigger-published-pipeline.md)

## <a name="view-your-scheduled-pipelines"></a>Visualizzare le pipeline pianificate

Nel Web browser passare ad Azure Machine Learning.In your Web browser, navigate to Azure Machine Learning. Nella sezione **Endpoint** del pannello di navigazione scegliere **Endpoint pipeline.** Verrà visualizzato un elenco delle pipeline pubblicate nell'area di lavoro.

![Pagina Pipeline di AML](./media/how-to-schedule-pipelines/scheduled-pipelines.png)

In questa pagina è possibile visualizzare informazioni di riepilogo su tutte le pipeline nell'area di lavoro: nomi, descrizioni, stato e così via. Eseguire il drill-in facendo clic nella pipeline. Nella pagina risultante sono disponibili ulteriori dettagli sulla pipeline ed è possibile eseguire il drill-down nelle singole esecuzioni.

## <a name="deactivate-the-pipeline"></a>Disattivare la pipeline

Se si `Pipeline` dispone di un che viene pubblicato, ma non pianificato, è possibile disabilitarlo con:

```python
pipeline = PublishedPipeline.get(ws, id=pipeline_id)
pipeline.disable()
```

Se la pipeline è pianificata, è necessario prima annullarla. Recuperare l'identificatore della pianificazione dal portale o eseguendo:

```python
ss = Schedule.list(ws)
for s in ss:
    print(s)
```

Una volta `schedule_id` che si desidera disattivare, eseguire:

```python
def stop_by_schedule_id(ws, schedule_id):
    s = next(s for s in Schedule.list(ws) if s.id == schedule_id)
    s.disable()
    return s

stop_by_schedule_id(ws, schedule_id)
```

Se poi `Schedule.list(ws)` si esegue di nuovo, si dovrebbe ottenere un elenco vuoto.

## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato usato Azure Machine Learning SDK per Python per pianificare una pipeline in due modi diversi. Una pianificazione si ripete in base all'ora dell'orologio trascorso. L'altra pianificazione viene eseguita se `Datastore` un file viene modificato in una directory specificata o all'interno di una directory in tale archivio. Si è visto come utilizzare il portale per esaminare la pipeline e le singole esecuzioni. Infine, si è appreso come disabilitare una pianificazione in modo che la pipeline smette di funzionare.

Per altre informazioni, vedere:

> [!div class="nextstepaction"]
> [Usare le pipeline di Azure Machine Learning per l'assegnazione del punteggio batch](tutorial-pipeline-batch-scoring-classification.md)

* Ulteriori informazioni sulle [pipeline](concept-ml-pipelines.md)
* Altre informazioni [sull'esplorazione di Azure Machine Learning con Jupyter](samples-notebooks.md)

