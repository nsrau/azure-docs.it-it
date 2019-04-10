---
title: Avviare, monitorare e annullare le esecuzioni di training in Python
titleSuffix: Azure Machine Learning service
description: Informazioni su come iniziare a stato, tag, nonché organizzare esperimenti di machine learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 4/5/2019
ms.openlocfilehash: 726273024a2da0cea5207c86140f3c31263a208f
ms.sourcegitcommit: ef20235daa0eb98a468576899b590c0bc1a38394
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2019
ms.locfileid: "59426707"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Avviare, monitorare e annullare le esecuzioni di training in Python

Il [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) fornisce diversi metodi per monitorare, organizzare e gestire le esecuzioni per il training e sperimentazione.

Questo argomento vengono illustrati esempi delle attività seguenti:

* [Monitoraggio delle prestazioni di esecuzione](#monitor)
* [Annullare o interrompere esecuzioni](#cancel)
* [Creare figlio esecuzioni](#children)
* [Tag e trovare le esecuzioni](#tag)

## <a name="prerequisites"></a>Prerequisiti

È necessario quanto segue:

* Una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

* Un'area di lavoro del servizio Azure Machine Learning. Visualizzare [creare un'area di lavoro del servizio di Azure Machine Learning](setup-create-workspace.md).

* Azure Machine Learning SDK per Python installata (versione 1.0.21 o versione successiva). Per installare o aggiornare la versione più recente del SDK, vedere la [installare/aggiornare il SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) pagina.

    Per controllare la versione del SDK di Azure Machine Learning, usare il codice seguente.

    ```Python
    print(azureml.core.VERSION)
    ```

<a name="monitor"></a>

## <a name="start-and-status-a-run"></a>Inizio e stato di un'esecuzione

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

Ottenere lo stato dell'esecuzione con [ `get_status()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--).

```Python
print(notebook_run.get_status())
```

Per ottenere informazioni aggiuntive sull'utilizzo fase [ `get_details()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--).

```Python
notebook_run.get_details()
```

Quando l'esecuzione viene completata correttamente, usare il [ `complete()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) metodo per contrassegnarlo come completato.

```Python
notebook_run.complete()
print(notebook_run.get_status())
```

È anche possibile usare Python `with...as` pattern. In questo contesto, l'esecuzione contrassegnerà automaticamente se stesso come completo quando l'esecuzione non rientra nell'ambito. In questo modo non è necessario contrassegnare manualmente l'esecuzione come completato.

```Python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print("Is it still running?",notebook_run.get_status())

print("Has it completed?",notebook_run.get_status())
```

<a name="cancel"></a>

## <a name="cancel-or-fail-runs"></a>Annullare o interrompere esecuzioni

 Se si nota un errore o l'esecuzione sembra essere impiega tempo per il completamento, usare il [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metodo per interrompere l'esecuzione prima che venga completato e contrassegnarlo come annullata.

```Python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')

local_script_run = exp.submit(run_config)
print("Did the run start?",local_script_run.get_status())

local_script_run.cancel()
print("Did the run cancel?",local_script_run.get_status())
```

Se l'esecuzione viene completata, ma contiene un errore, ad esempio, è stato usato lo script di training non corretto, è possibile usare la [ `fail()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#fail-error-details-none---set-status-true-) metodo per contrassegnarlo come non riuscita.

```Python
local_script_run = exp.submit(run_config)
local_script_run.fail()

print(local_script_run.get_status())
```

<a name="children"></a>

## <a name="create-child-runs"></a>Creare figlio esecuzioni

Creare figlio viene eseguito per raggruppare le esecuzioni correlate, ad esempio per diversi iterazioni di ottimizzazione degli iperparametri.

Questo esempio di codice Usa lo script hello_with_children.py per creare un batch di cinque esecuzioni figlio all'interno di un'esecuzione inviato utilizzando il [ `child_run()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) (metodo).

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
> Figlio viene eseguito completata automaticamente quando vengono spostati fuori ambito.

È anche possibile avviare esecuzioni figlio uno alla volta, ma poiché ogni creazione comporta una chiamata di rete, è meno efficiente rispetto all'invio di un batch di esecuzioni.

Usare la [ `get_children()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) esecuzione del metodo per eseguire una query il figlio di un'entità padre specifica.

```Python
list(parent_run.get_children())
```

<a name="tag"></a>

## <a name="tag-and-find-runs"></a>Tag e trovare le esecuzioni

Nel servizio di Azure Machine Learning, è possibile utilizzare le proprietà e i tag per organizzare e le esecuzioni per importanti informazioni di query.

### <a name="add-properties-and-tags"></a>Aggiungere tag e proprietà

Usare la [ `add_properties()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) per aggiungere metadati disponibili per la ricerca per le esecuzioni. Ad esempio, il codice seguente aggiunge la proprietà "author" per l'esecuzione.

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Proprietà non sono modificabili, che si rivela utile come un record permanente a fini di controllo. Esempio di codice seguente genererà un errore, poiché sono già stati aggiunti "azureml-user", come la proprietà "author" nel codice precedente.

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

I tag sono tuttavia può essere modificato. Uso [ `tag()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) per aggiungere le informazioni disponibili per la ricerca e significative per i consumer dell'esperimento.

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

È anche possibile aggiungere un tag di stringa semplice. Viene visualizzato nel dizionario di tag con il valore di `None`.

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

### <a name="query-properties-and-tags"></a>Eseguire query sui tag e proprietà

È possibile eseguire una query viene eseguito all'interno di un esperimento che corrispondono alle proprietà specifiche e i tag.

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

## <a name="example-notebooks"></a>Notebook di esempio

I notebook seguenti illustrano i concetti descritti in questo articolo:

* Per altre informazioni sull'API di registrazione, vedere la [notebook di registrazione API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Per altre informazioni sulla gestione viene eseguito con il SDK di Azure Machine Learning, vedere la [gestire esecuzioni notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come registrare le metriche per gli esperimenti, vedere la [registrare metriche durante le esecuzioni di training](https://docs.microsoft.com/azure/machine-learning/service/how-to-track-experiment) articolo.