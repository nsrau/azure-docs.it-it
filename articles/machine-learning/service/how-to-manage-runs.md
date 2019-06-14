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
ms.openlocfilehash: a93492b8ea97500fe3c761f3ac0c49f8c1342d09
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074964"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Avviare, monitorare e annullare le esecuzioni di training in Python

Il [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) e [interfaccia della riga di comando di Machine Learning](reference-azure-machine-learning-cli.md) offrono vari metodi per monitorare, organizzare e gestire le esecuzioni per il training e sperimentazione.

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

* Il [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) e [estensione dell'interfaccia della riga per il servizio di Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="start-a-run-and-its-logging-process"></a>Avviare un'esecuzione e il processo di registrazione

### <a name="using-the-sdk"></a>Uso dell'SDK

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

### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Per avviare un'esecuzione dell'esperimento, procedere come segue:

1. Da una shell o il prompt dei comandi, usare il comando di Azure per eseguire l'autenticazione alla sottoscrizione di Azure:

    ```azurecli-interactive
    az login
    ```

1. Collegare una configurazione dell'area di lavoro per la cartella che contiene lo script di training. Sostituire `myworkspace` con l'area di lavoro del servizio di Azure Machine Learning. Sostituire `myresourcegroup` con il gruppo di risorse di Azure che contiene l'area di lavoro:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Questo comando crea un `.azureml` sottodirectory che contiene file dell'ambiente conda e runconfig di esempio. Contiene inoltre un `config.json` file utilizzato per comunicare con l'area di lavoro di Azure Machine Learning.

    Per altre informazioni, vedere [cartella di az ml collegare](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Per avviare l'esecuzione, usare il comando seguente. Quando si utilizza questo comando, specificare il nome del file runconfig (il testo prima \*. runconfig se si sta esaminando il file system) con il parametro - c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Il `az ml folder attach` comando creato un `.azureml` sottodirectory, che contiene due file di esempio runconfig. 
    >
    > Se si dispone di uno script Python che crea un oggetto di configurazione di esecuzione a livello di codice, è possibile usare [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) salvarlo come file runconfig.
    >
    > Per più file runconfig di esempio, vedere [ https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml ](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    Per altre informazioni, vedere [az ml eseguire script di inviare](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

## <a name="monitor-the-status-of-a-run"></a>Monitorare lo stato di un'esecuzione

### <a name="using-the-sdk"></a>Uso dell'SDK

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

### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

1. Per visualizzare un elenco di esecuzioni dell'esperimento, usare il comando seguente. Sostituire `experiment` con il nome dell'esperimento:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Questo comando restituisce un documento JSON che elenca le informazioni sulle esecuzioni per questo esperimento.

    Per altre informazioni, vedere [esperimento di ml az elenco](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Per visualizzare informazioni su un'esecuzione specifica, usare il comando seguente. Sostituire `runid` con l'ID dell'esecuzione:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Questo comando restituisce un documento JSON che elenca le informazioni sull'esecuzione.

    Per altre informazioni, vedere [az ml eseguire show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).

## <a name="cancel-or-fail-runs"></a>Annullare o interrompere esecuzioni

Se si nota un errore o se richiede troppo tempo per completare l'esecuzione, è possibile annullare l'esecuzione.

### <a name="using-the-sdk"></a>Uso dell'SDK

Per annullare un'esecuzione con il SDK, usare il [ `cancel()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) metodo:

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

### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Per annullare un'esecuzione usando l'interfaccia della riga di comando, usare il comando seguente. Sostituire `runid` con l'ID dell'esecuzione

```azurecli-interactive
az ml run cancel -r runid
```

Per altre informazioni, vedere [az ml Esegui Annulla](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

## <a name="create-child-runs"></a>Creare figlio esecuzioni

Creare figlio viene eseguito per raggruppare esecuzioni correlate, ad esempio per diverse iterazioni di ottimizzazione degli iperparametri.

> [!NOTE]
> Esecuzioni figlio possono essere creati solo usando il SDK.

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

#### <a name="using-the-sdk"></a>Uso dell'SDK

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

#### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

> [!NOTE]
> Usando l'interfaccia della riga di comando, è possibile solo aggiungere o aggiornare i tag.

Per aggiungere o aggiornare un tag, usare il comando seguente:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Per altre informazioni, vedere [az ml eseguire update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Eseguire query sui tag e proprietà

È possibile eseguire una query viene eseguito all'interno di un esperimento per restituire un elenco di esecuzioni che corrispondono a tag e proprietà specifiche.

#### <a name="using-the-sdk"></a>Uso dell'SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Il comando di Azure supporta [JMESPath](http://jmespath.org) query che possono essere usate per filtrare esecuzioni basate sui tag e proprietà. Per usare una query JMESPath con l'interfaccia CLI di Azure, specificare con il `--query` parametro. Gli esempi seguenti illustrano le query di base usando tag e proprietà:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Per altre informazioni sull'esecuzione di query i risultati della riga di comando di Azure, vedere [output del comando Query Azure CLI](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest).

## <a name="example-notebooks"></a>Notebook di esempio

Il notebook seguenti illustrano i concetti presentati in questo articolo:

* Per altre informazioni sull'API di registrazione, vedere la [notebook di registrazione API](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api/logging-api.ipynb).

* Per altre informazioni sulla gestione viene eseguito con il SDK di Azure Machine Learning, vedere la [gestire esecuzioni notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training/manage-runs).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come registrare le metriche per gli esperimenti, vedere [registrare metriche durante le esecuzioni di training](how-to-track-experiments.md).
