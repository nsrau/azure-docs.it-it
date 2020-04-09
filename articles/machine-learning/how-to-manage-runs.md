---
title: Avviare, monitorare e annullare le esecuzioni degli allenamenti in PythonStart, monitor, and cancel training runs in Python
titleSuffix: Azure Machine Learning
description: Scopri come avviare, impostare lo stato di, taggare e organizzare gli esperimenti di apprendimento automatico.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.openlocfilehash: c1b70aaef49cc2b993c873509dc935d71069efa2
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985916"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Avviare, monitorare e annullare le esecuzioni degli allenamenti in PythonStart, monitor, and cancel training runs in Python
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

[Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py), [l'interfaccia della riga](reference-azure-machine-learning-cli.md)di comando di Machine Learning e Azure Machine Learning [Studio](https://ml.azure.com) offrono vari metodi per monitorare, organizzare e gestire le esecuzioni per formazione e sperimentazione.

In questo articolo vengono illustrati esempi delle attività seguenti:This article shows examples of the following tasks:

* Monitorare le prestazioni di esecuzione.
* Annullamento o esito negativo.
* Creare esecuzioni figlio.
* Tagga e trova corse.

## <a name="prerequisites"></a>Prerequisiti

Avrai bisogno dei seguenti elementi:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Area di lavoro di [Azure Machine Learning](how-to-manage-workspace.md).

* Azure Machine Learning SDK per Python (versione 1.0.21 o successiva). Per installare o aggiornare alla versione più recente dell'SDK, vedere [Installare o aggiornare l'SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

    Per verificare la versione di Azure Machine Learning SDK, usare il codice seguente:

    ```python
    print(azureml.core.VERSION)
    ```

* L'interfaccia [della riga di comando](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) di Azure e [l'estensione dell'interfaccia della riga di comando per Azure Machine Learning.](reference-azure-machine-learning-cli.md)

## <a name="start-a-run-and-its-logging-process"></a>Avviare un'esecuzione e il relativo processo di registrazioneStart a run and its logging process

### <a name="using-the-sdk"></a>Uso dell'SDK

Configurare l'esperimento importando le classi [Workspace](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py), [Experiment](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment.experiment?view=azure-ml-py), [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py)e [ScriptRunConfig](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) dal pacchetto [azureml.core](https://docs.microsoft.com/python/api/azureml-core/azureml.core?view=azure-ml-py) .

```python
import azureml.core
from azureml.core import Workspace, Experiment, Run
from azureml.core import ScriptRunConfig

ws = Workspace.from_config()
exp = Experiment(workspace=ws, name="explore-runs")
```

Avviare un'esecuzione e [`start_logging()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) il relativo processo di registrazione con il metodo .

```python
notebook_run = exp.start_logging()
notebook_run.log(name="message", value="Hello from run!")
```

### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Per avviare un'esecuzione dell'esperimento, procedi nel seguente modo:

1. Da una shell o da un prompt dei comandi usare l'interfaccia della riga di comando di Azure per eseguire l'autenticazione nella sottoscrizione di Azure:From a shell or command prompt, use the Azure CLI to authenticate to your Azure subscription:

    ```azurecli-interactive
    az login
    ```
    
    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

1. Collegare una configurazione dell'area di lavoro alla cartella che contiene lo script di training. Sostituire `myworkspace` con l'area di lavoro di Azure Machine Learning.Replace with your Azure Machine Learning workspace. Sostituire `myresourcegroup` con il gruppo di risorse di Azure che contiene l'area di lavoro:Replace with the Azure resource group that contains your workspace:

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Questo comando `.azureml` crea una sottodirectory che contiene i file di ambiente runconfig e conda di esempio. Contiene inoltre `config.json` un file usato per comunicare con l'area di lavoro di Azure Machine Learning.It also contains a file that is used to communicate with your Azure Machine Learning workspace.

    Per ulteriori informazioni, vedere [az ml folder attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

2. Per avviare l'esecuzione, utilizzare il comando seguente. Quando si utilizza questo comando, specificare il nome \*del file runconfig (il testo prima di .runconfig se si sta esaminando il file system) rispetto al parametro -c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Il `az ml folder attach` comando `.azureml` ha creato una sottodirectory, che contiene due file runconfig di esempio.
    >
    > Se si dispone di uno script Python che crea un oggetto di configurazione di esecuzione a livello di codice, è possibile utilizzare [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) per salvarlo come file runconfig.
    >
    > Per altri file runconfig [https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml)di esempio, vedere .

    Per ulteriori informazioni, vedere [az ml run submit-script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

### <a name="using-azure-machine-learning-studio"></a>Uso di Azure Machine Learning Studio

Per avviare un'esecuzione di una pipeline di invio nella finestra di progettazione (anteprima), eseguire la procedura seguente:To start a submit a pipeline run in the designer (preview), use the following steps:

1. Impostare una destinazione di calcolo predefinita per la pipeline.

1. Selezionare **Esegui** nella parte superiore del canvas della pipeline.

1. Selezionare un esperimento per raggruppare le esecuzioni della pipeline.

## <a name="monitor-the-status-of-a-run"></a>Monitorare lo stato di un'esecuzione

### <a name="using-the-sdk"></a>Uso dell'SDK

Ottenere lo stato di [`get_status()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-status--) un'esecuzione con il metodo .

```python
print(notebook_run.get_status())
```

Per ottenere l'ID di esecuzione, il tempo di [`get_details()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py#get-details--) esecuzione e ulteriori dettagli sull'esecuzione, utilizzare il metodo .

```python
print(notebook_run.get_details())
```

Al termine della corsa, utilizzare [`complete()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) il metodo per contrassegnarla come completata.

```python
notebook_run.complete()
print(notebook_run.get_status())
```

Se si utilizza `with...as` il modello di progettazione di Python, l'esecuzione contrassegnerà automaticamente se stessa come completata quando l'esecuzione è fuori dall'ambito. Non è necessario contrassegnare manualmente l'esecuzione come completata.

```python
with exp.start_logging() as notebook_run:
    notebook_run.log(name="message", value="Hello from run!")
    print(notebook_run.get_status())

print(notebook_run.get_status())
```

### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

1. Per visualizzare un elenco di esecuzioni per l'esperimento, usa il comando seguente. Sostituire `experiment` con il nome dell'esperimento:

    ```azurecli-interactive
    az ml run list --experiment-name experiment
    ```

    Questo comando restituisce un documento JSON che elenca le informazioni sulle esecuzioni per questo esperimento.

    Per ulteriori informazioni, consultate [Az ml experiment list](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

2. Per visualizzare le informazioni su un'esecuzione specifica, utilizzare il comando seguente. Sostituire `runid` con l'ID della corsa:

    ```azurecli-interactive
    az ml run show -r runid
    ```

    Questo comando restituisce un documento JSON che elenca le informazioni sull'esecuzione.

    Per ulteriori informazioni, vedere [az ml run show show](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).


### <a name="using-azure-machine-learning-studio"></a>Uso di Azure Machine Learning Studio

Per visualizzare il numero di esecuzioni attive per l'esperimento in studio.

1. Passare alla sezione **Esperimenti..** 

1. Selezionare un esperimento.

    Nella pagina dell'esperimento è possibile visualizzare il numero di destinazioni di calcolo attive e la durata di ogni esecuzione. 

1. Selezionare un numero di corsa specifico.

1. Nella scheda **Log** è possibile trovare i log di diagnostica e degli errori per l'esecuzione della pipeline.


## <a name="cancel-or-fail-runs"></a>Annullamento o esito negativo delle esecuzioni

Se si nota un errore o se la corsa sta impiegando troppo tempo per finire, è possibile annullare la corsa.

### <a name="using-the-sdk"></a>Uso dell'SDK

Per annullare un'esecuzione utilizzando [`cancel()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#cancel--) l'SDK, utilizzare il metodo :

```python
run_config = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_script_run = exp.submit(run_config)
print(local_script_run.get_status())

local_script_run.cancel()
print(local_script_run.get_status())
```

Se la corsa termina, ma contiene un errore (ad esempio, è stato [`fail()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)#fail-error-details-none--error-code-none---set-status-true-) utilizzato lo script di training non corretto), è possibile utilizzare il metodo per contrassegnarlo come non riuscito.

```python
local_script_run = exp.submit(run_config)
local_script_run.fail()
print(local_script_run.get_status())
```

### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

Per annullare un'esecuzione utilizzando l'interfaccia della riga di comando, utilizzare il comando seguente. Sostituire `runid` con l'ID della corsa

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Per ulteriori informazioni, vedere [az ml run cancel](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

### <a name="using-azure-machine-learning-studio"></a>Uso di Azure Machine Learning Studio

Per annullare una conduzione in studio, attenersi alla seguente procedura:

1. Passare alla pipeline in esecuzione nella sezione Esperimenti o Pipeline.Go to the running pipeline in either the **Experiments** or **Pipelines** section. 

1. Selezionare il numero di esecuzione della pipeline che si desidera annullare.

1. Nella barra degli strumenti, selezionare **Annulla**


## <a name="create-child-runs"></a>Creare esecuzioni figlio

Creare esecuzioni figlio per raggruppare le esecuzioni correlate, ad esempio per diverse iterazioni di ottimizzazione degli iperparametri.

> [!NOTE]
> Le esecuzioni figlio possono essere create solo utilizzando l'SDK.

Questo esempio di `hello_with_children.py` codice usa lo script per creare un batch [`child_run()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#child-run-name-none--run-id-none--outputs-none-) di cinque esecuzioni figlio dall'interno di un'esecuzione inviata utilizzando il metodo :

```python
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
> Quando si spostano all'esterno dell'ambito, le esecuzioni figlio vengono contrassegnate automaticamente come completate.

Per creare molte esecuzioni [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) figlio in modo efficiente, utilizzare il metodo . Poiché ogni creazione comporta una chiamata di rete, la creazione di un batch di esecuzioni è più efficiente rispetto alla creazione di una alla volta.

### <a name="submit-child-runs"></a>Inviare esecuzioni figlio

Le esecuzioni figlio possono anche essere inviate da un'esecuzione padre. In questo modo è possibile creare gerarchie di esecuzioni padre e figlio. 

È possibile che si desideri che l'esecuzione dell'elemento figlio utilizzi una configurazione di esecuzione diversa da quella dell'esecuzione padre. Ad esempio, è possibile utilizzare una configurazione meno potente basata sulla CPU per l'elemento padre, mentre si utilizzano configurazioni basate su GPU per i figli. Un altro desiderio comune è quello di passare ogni figlio diversi argomenti e dati. Per personalizzare un'esecuzione `RunConfiguration` figlio, passare un `ScriptRunConfig` oggetto al costruttore dell'elemento figlio. Questo esempio di codice, che `ScriptRunConfig` fa parte dello script dell'oggetto padre:This code example, which would be part of the parent object's script:

- Crea `RunConfiguration` un recupero di una risorsa di calcolo denominata`"gpu-compute"`
- Scorre i diversi valori di argomento `ScriptRunConfig` da passare agli oggetti figlio
- Crea e invia una nuova esecuzione figlio, usando la risorsa di calcolo personalizzata e l'argomento
- Blocchi fino a quando tutti i bambini vengono completati

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig, RunConfiguration

run_config_for_aml_compute = RunConfiguration()
run_config_for_aml_compute.target = "gpu-compute"
run_config_for_aml_compute.environment.docker.enabled = True 

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], run_config = run_config_for_aml_compute)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Per creare molte esecuzioni figlio con configurazioni, argomenti [`create_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run.run?view=azure-ml-py#create-children-count-none--tag-key-none--tag-values-none-) e input identici in modo efficiente, utilizzare il metodo . Poiché ogni creazione comporta una chiamata di rete, la creazione di un batch di esecuzioni è più efficiente rispetto alla creazione di una alla volta.

Within a child run, you can view the parent run ID:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Esecuzione figlio di queryQuery child runs

Per eseguire una query sulle esecuzioni [`get_children()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#get-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) figlio di un elemento padre specifico, utilizzare il metodo . L'argomento ``recursive = True`` consente di eseguire una query su un albero annidato di figli e nipoti.

```python
print(parent_run.get_children())
```

## <a name="tag-and-find-runs"></a>Contrassegnare e trovare le sequenze

In Azure Machine Learning è possibile usare proprietà e tag per organizzare ed eseguire query sulle esecuzioni per ottenere informazioni importanti.

### <a name="add-properties-and-tags"></a>Aggiungere proprietà e tag

#### <a name="using-the-sdk"></a>Uso dell'SDK

Per aggiungere metadati ricercabili alle [`add_properties()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#add-properties-properties-) esecuzioni, utilizzare il metodo . Ad esempio, il codice `"author"` seguente aggiunge la proprietà all'esecuzione:

```Python
local_script_run.add_properties({"author":"azureml-user"})
print(local_script_run.get_properties())
```

Le proprietà non sono modificabili, pertanto creano un record permanente a scopo di controllo. L'esempio di codice seguente genera un `"azureml-user"` errore, perché è già stato aggiunto come valore della proprietà nel codice precedente:The following code example results in an error, because we already added as the `"author"` property value in the preceding code:

```Python
try:
    local_script_run.add_properties({"author":"different-user"})
except Exception as e:
    print(e)
```

A differenza delle proprietà, i tag sono modificabili. Per aggiungere informazioni ricercabili e significative per [`tag()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#tag-key--value-none-) gli utenti dell'esperimento, utilizzare il metodo .

```Python
local_script_run.tag("quality", "great run")
print(local_script_run.get_tags())

local_script_run.tag("quality", "fantastic run")
print(local_script_run.get_tags())
```

È inoltre possibile aggiungere semplici tag di stringa. Quando questi tag vengono visualizzati nel dizionario `None`dei tag come chiavi, hanno un valore di .

```Python
local_script_run.tag("worth another look")
print(local_script_run.get_tags())
```

#### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

> [!NOTE]
> Utilizzando l'interfaccia della riga di comando, è possibile solo aggiungere o aggiornare i tag.

Per aggiungere o aggiornare un tag, utilizzare il comando seguente:

```azurecli-interactive
az ml run update -r runid --add-tag quality='fantastic run'
```

Per ulteriori informazioni, vedere [az ml run update](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).

### <a name="query-properties-and-tags"></a>Proprietà e tag delle query

È possibile eseguire query all'interno di un esperimento per restituire un elenco di esecuzioni che corrispondono a proprietà e tag specifici.

#### <a name="using-the-sdk"></a>Uso dell'SDK

```Python
list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
```

#### <a name="using-the-cli"></a>Uso dell'interfaccia della riga di comando

L'interfaccia della riga di comando di Azure supporta le query [JMESPath,](http://jmespath.org) che possono essere usate per filtrare le esecuzioni in base a proprietà e tag. Per usare una query JMESPath con l'interfaccia della riga di comando di Azure, specificarla con il `--query` parametro . Negli esempi seguenti vengono illustrate query di base che utilizzano proprietà e tag:

```azurecli-interactive
# list runs where the author property = 'azureml-user'
az ml run list --experiment-name experiment [?properties.author=='azureml-user']
# list runs where the tag contains a key that starts with 'worth another look'
az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
# list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
```

Per altre informazioni sull'esecuzione di query sui risultati dell'interfaccia della riga di comando di Azure, vedere [Eseguire query sull'output del comando dell'interfaccia della riga](https://docs.microsoft.com/cli/azure/query-azure-cli?view=azure-cli-latest)di comando di Azure.For more information on querying Azure CLI results, see Query Azure CLI command

### <a name="using-azure-machine-learning-studio"></a>Uso di Azure Machine Learning Studio

1. Passare alla sezione **Pipeline.Navigate** to the Pipelines section.

1. Utilizzare la barra di ricerca per filtrare le pipeline usando tag, descrizioni, nomi di esperimenti e nome dell'utente mittente.

## <a name="example-notebooks"></a>Notebook di esempio

I blocchi appunti seguenti illustrano i concetti illustrati in questo articolo:

* Per altre informazioni sulle API di registrazione, vedere il [blocco appunti dell'API di registrazione.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb)

* Per altre informazioni sulla gestione delle esecuzioni con Azure Machine Learning SDK, vedere il [blocco appunti Gestisci esecuzione](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come registrare le metriche per gli esperimenti, vedere [Registrare le metriche durante le esecuzioni](how-to-track-experiments.md)di training.
* Per informazioni su come monitorare risorse e log da Azure Machine Learning, vedere Monitoraggio di [Azure Machine Learning.](monitor-azure-machine-learning.md)