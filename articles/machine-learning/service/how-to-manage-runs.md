---
title: Avviare, monitorare e annullare le esecuzioni di training in Python
titleSuffix: Azure Machine Learning service
description: Informazioni su come iniziare, impostare lo stato del, tag e organizzare gli esperimenti di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 04/05/2019
ms.openlocfilehash: c0c1c1353b12944fa913dfb0789192917b99f234
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819212"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Avviare, monitorare e annullare le esecuzioni di training in Python

Il [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) fornisce diversi metodi per monitorare, organizzare e gestire le esecuzioni per il training e sperimentazione.

Questo articolo illustra alcuni esempi delle attività seguenti:

* Monitoraggio delle prestazioni di esecuzione.
* Annullare o interrompere esecuzioni.
* Creare figlio viene eseguito.
* Tag e trovare le esecuzioni.

## <a name="prerequisites"></a>Prerequisiti

È necessario quanto segue:

* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* Un' [dell'area di lavoro di Azure Machine Learning servizio](setup-create-workspace.md).

* Azure Machine Learning SDK per Python (versione 1.0.21 o versione successiva). Per installare o aggiornare la versione più recente del SDK, vedere [installare o aggiornare il SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Per controllare la versione del SDK di Azure Machine Learning, usare il codice seguente:

    ```Python
    print(azureml.core.VERSION)
    ```

## <a name="start-a-run-and-its-logging-process"></a>Avviare un'esecuzione e il processo di registrazione

Configurare l'esperimento importando il [dell'area di lavoro](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [sperimentare](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [eseguire](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py), e [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) classi dal [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) pacchetto.

```Python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Avviare un'esecuzione e il processo di registrazione con il [ `start_logging()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) (metodo).

```Python
notebook_run = exp.start_logging()

notebook_run.log(name="message", value="Hello from run!")
```

## <a name="monitor-the-status-of-a-run"></a>Monitorare lo stato di un'esecuzione

Ottenere lo stato di un'esecuzione con il [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) (metodo).

```Python
print(notebook_run.get_status())
```

Per ottenere altri dettagli sull'esecuzione, usare il [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) (metodo).

```Python
notebook_run.get_details()
```

Quando l'esecuzione viene completata correttamente, usare il [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metodo per contrassegnarlo come completato.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

Se si usano Python `with...as` modello, l'esecuzione contrassegnerà automaticamente se stesso come completata quando l'esecuzione non rientra nell'ambito. Non è necessario contrassegnare manualmente l'esecuzione come completato.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

## <a name="cancel-or-fail-runs"></a>Annullare o interrompere esecuzioni

 Se si nota un errore o se l'esecuzione sta richiedendo troppo tempo alla fine, usare il [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metodo per interrompere l'esecuzione prima che venga completato e contrassegnarlo come annullata.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Se l'esecuzione viene completata, ma contiene un errore (ad esempio, lo script di training non corretto è stato usato), è possibile usare la [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) metodo per contrassegnarlo come non riuscita.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

## <a name="create-child-runs"></a>Creare figlio esecuzioni

Creare figlio viene eseguito per raggruppare esecuzioni correlate, ad esempio per diverse iterazioni di ottimizzazione degli iperparametri.

In questo esempio di codice Usa il `hello_with_children.py` script per creare un batch di cinque esecuzioni figlio all'interno di un'esecuzione inviata tramite il [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) metodo:

```Python
!more hello_with_children.py
run_config = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_script_run = exp.submit(run_config)
local_script_run.wait_for_completion(show_output=True)
print(local_script_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Durante lo spostamento dall'ambito, viene eseguito figlio contrassegnati automaticamente come completata.

È anche possibile avviare esecuzioni figlio uno alla volta, ma poiché ogni creazione comporta una chiamata di rete, è meno efficiente rispetto all'invio di un batch di esecuzioni.

Per eseguire una query viene eseguito il figlio di un elemento padre specifico, usare il [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) (metodo).

```Python
list(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Tag e trovare le esecuzioni

Nel servizio di Azure Machine Learning, è possibile utilizzare le proprietà e i tag per organizzare e le esecuzioni per importanti informazioni di query.

### <a name="add-properties-and-tags"></a>Aggiungere tag e proprietà

Per aggiungere metadati ricercabili per l'esecuzione, usare il [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) (metodo). Ad esempio, il codice seguente aggiunge il `"author"` proprietà per l'esecuzione:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Proprietà non sono modificabili, quindi crea un record permanente a fini di controllo. Nell'esempio di codice risultati degli esempi di un errore, perché sono già stati aggiunti `"azureml-user"` come il `"author"` valore della proprietà nel codice precedente:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

A differenza delle proprietà, i tag sono modificabili. Per aggiungere le informazioni disponibili per la ricerca e significative per i consumer dell'esperimento, usare il [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) (metodo).

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

È anche possibile aggiungere i tag di stringa semplice. Quando questi tag vengono visualizzati nel dizionario di tag, hanno valore `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Eseguire query sui tag e proprietà

È possibile eseguire una query viene eseguito all'interno di un esperimento per restituire un elenco di esecuzioni che corrispondono a tag e proprietà specifiche.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Notebook di esempio

Il notebook seguenti illustrano i concetti presentati in questo articolo:

* Per altre informazioni sull'API di registrazione, vedere la [notebook di registrazione API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Per altre informazioni sulla gestione viene eseguito con il SDK di Azure Machine Learning, vedere la [gestire esecuzioni notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come registrare le metriche per gli esperimenti, vedere [registrare metriche durante le esecuzioni di training](how-to-track-experiments.md).