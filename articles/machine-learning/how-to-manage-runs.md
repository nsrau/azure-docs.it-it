---
title: Avviare, monitorare e annullare le esecuzioni di training in Python
titleSuffix: Azure Machine Learning
description: Informazioni su come avviare, stato e gestire l'esperimento di Machine Learning con il Azure Machine Learning Python SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: roastala
author: rastala
manager: cgronlun
ms.reviewer: nibaccam
ms.date: 01/09/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 921c88f4771fedb910dc41983d559987a8cdfb0c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349334"
---
# <a name="start-monitor-and-cancel-training-runs-in-python"></a>Avviare, monitorare e annullare le esecuzioni di training in Python

Il [Azure Machine Learning SDK per Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py), l'interfaccia della riga di comando di [Machine Learning](reference-azure-machine-learning-cli.md)e [Azure Machine Learning Studio](https://ml.azure.com) offre diversi metodi per monitorare, organizzare e gestire le esecuzioni per il training e la sperimentazione.

Questo articolo illustra alcuni esempi delle attività seguenti:

* Monitorare le prestazioni di esecuzione.
* Esecuzioni annullate o non riuscite.
* Crea esecuzioni figlio.
* Esecuzioni di tag e di ricerca.

> [!TIP]
> Per informazioni sul monitoraggio del servizio Azure Machine Learning e dei servizi di Azure associati, vedere [How to monitor Azure Machine Learning](monitor-azure-machine-learning.md).
> Per informazioni sui modelli di monitoraggio distribuiti come servizi Web o moduli di IoT Edge, vedere [raccogliere dati del modello](how-to-enable-data-collection.md) e [monitorarli con Application Insights](how-to-enable-app-insights.md).

## <a name="prerequisites"></a>Prerequisiti

Sono necessari gli elementi seguenti:

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

* SDK Azure Machine Learning per Python (versione 1.0.21 o successiva). Per eseguire l'installazione o l'aggiornamento alla versione più recente dell'SDK, vedere [installare o aggiornare l'SDK](/python/api/overview/azure/ml/install?preserve-view=true&view=azure-ml-py).

    Per verificare la versione di Azure Machine Learning SDK, usare il codice seguente:

    ```python
    print(azureml.core.VERSION)
    ```

* L' [interfaccia](/cli/azure/?preserve-view=true&view=azure-cli-latest) della riga di comando di Azure e l' [estensione CLI per Azure Machine Learning](reference-azure-machine-learning-cli.md).

## <a name="monitor-run-performance"></a>Monitorare le prestazioni di esecuzione

* Avviare un'esecuzione e il processo di registrazione

    # <a name="python"></a>[Python](#tab/python)
    
    1. Configurare l'esperimento importando le [classi area di lavoro](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py), [esperimento](/python/api/azureml-core/azureml.core.experiment.experiment?preserve-view=true&view=azure-ml-py), [esecuzione](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py)e [ScriptRunConfig](/python/api/azureml-core/azureml.core.scriptrunconfig?preserve-view=true&view=azure-ml-py) dal pacchetto [azureml. Core](/python/api/azureml-core/azureml.core?preserve-view=true&view=azure-ml-py) .
    
        ```python
        import azureml.core
        from azureml.core import Workspace, Experiment, Run
        from azureml.core import ScriptRunConfig
        
        ws = Workspace.from_config()
        exp = Experiment(workspace=ws, name="explore-runs")
        ```
    
    1. Avviare un'esecuzione e il processo di registrazione con il [`start_logging()`](/python/api/azureml-core/azureml.core.experiment%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truestart-logging--args----kwargs-) metodo.
    
        ```python
        notebook_run = exp.start_logging()
        notebook_run.log(name="message", value="Hello from run!")
        ```
        
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    Per avviare un'esecuzione dell'esperimento, seguire questa procedura:
    
    1. Da una shell o da un prompt dei comandi, usare l'interfaccia della riga di comando di Azure per eseguire l'autenticazione alla sottoscrizione di Azure:
    
        ```azurecli-interactive
        az login
        ```
        
        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 
    
    1. Alleghi una configurazione dell'area di lavoro alla cartella che contiene lo script di training. Sostituire `myworkspace` con l'area di lavoro Azure Machine Learning. Sostituire `myresourcegroup` con il gruppo di risorse di Azure che contiene l'area di lavoro:
    
        ```azurecli-interactive
        az ml folder attach -w myworkspace -g myresourcegroup
        ```
    
        Questo comando crea una `.azureml` sottodirectory che contiene i file di ambiente runconfig e conda di esempio. Contiene anche un file `config.json` usato per comunicare con l'area di lavoro di Azure Machine Learning.
    
        Per altre informazioni, vedere [az ml folder attach](/cli/azure/ext/azure-cli-ml/ml/folder?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).
    
    2. Per avviare l'esecuzione, usare il comando seguente. Quando si usa questo comando, specificare il nome del file runconfig (il testo prima di \*.runconfig se si sta esaminando il file system) rispetto al parametro -c.
    
        ```azurecli-interactive
        az ml run submit-script -c sklearn -e testexperiment train.py
        ```
    
        > [!TIP]
        > Il `az ml folder attach` comando ha creato una `.azureml` sottodirectory, che contiene due file runconfig di esempio.
        >
        > Se si dispone di uno script Python che crea un oggetto di configurazione di esecuzione a livello di codice, è possibile usare [RunConfig.save()](/python/api/azureml-core/azureml.core.runconfiguration?preserve-view=true&view=azure-ml-py#&preserve-view=truesave-path-none--name-none--separate-environment-yaml-false-) per salvarlo come file runconfig.
        >
        > Per ulteriori esempi di file runconfig, vedere [https://github.com/MicrosoftDocs/pipelines-azureml/](https://github.com/MicrosoftDocs/pipelines-azureml/) .
    
        Per altre informazioni, vedere [az ml run submit-script](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Per avviare un invio di un'esecuzione della pipeline nella finestra di progettazione, attenersi alla procedura seguente:
    
    1. Impostare una destinazione di calcolo predefinita per la pipeline.
    
    1. Selezionare **Esegui** nella parte superiore dell'area di disegno della pipeline.
    
    1. Selezionare un esperimento per raggruppare le esecuzioni della pipeline.
    
    ---

* Monitorare lo stato di un'esecuzione

    # <a name="python"></a>[Python](#tab/python)
    
    * Ottenere lo stato di un'esecuzione con il [`get_status()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-status--) metodo.
    
        ```python
        print(notebook_run.get_status())
        ```
    
    * Per ottenere l'ID esecuzione, il tempo di esecuzione e altri dettagli sull'esecuzione, usare il [`get_details()`](/python/api/azureml-core/azureml.core.workspace.workspace?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-details--) metodo.
    
        ```python
        print(notebook_run.get_details())
        ```
    
    * Al termine dell'esecuzione, utilizzare il [`complete()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecomplete--set-status-true-) metodo per contrassegnarlo come completato.
    
        ```python
        notebook_run.complete()
        print(notebook_run.get_status())
        ```
    
    * Se si usa lo `with...as` schema progettuale di Python, l'esecuzione verrà contrassegnata automaticamente come completata quando l'esecuzione non rientra nell'ambito. Non è necessario contrassegnare manualmente l'esecuzione come completata.
        
        ```python
        with exp.start_logging() as notebook_run:
            notebook_run.log(name="message", value="Hello from run!")
            print(notebook_run.get_status())
        
        print(notebook_run.get_status())
        ```
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    * Per visualizzare un elenco di esecuzioni per l'esperimento, usare il comando seguente. Sostituire `experiment` con il nome dell'esperimento:
    
        ```azurecli-interactive
        az ml run list --experiment-name experiment
        ```
    
        Questo comando restituisce un documento JSON che elenca le informazioni sulle esecuzioni per questo esperimento.
    
        Per altre informazioni, vedere [az ml experiment list](/cli/azure/ext/azure-cli-ml/ml/experiment?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).
    
    * Per visualizzare le informazioni su un'esecuzione specifica, usare il comando seguente. Sostituire `runid` con l'ID dell'esecuzione:
    
        ```azurecli-interactive
        az ml run show -r runid
        ```
    
        Questo comando restituisce un documento JSON che elenca le informazioni sull'esecuzione.
    
        Per ulteriori informazioni, vedere [AZ ml Run Show](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-show).
    
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    Per visualizzare il numero di esecuzioni attive per l'esperimento in studio.
    
    1. Passare alla sezione **esperimenti** .
    
    1. Selezionare un esperimento.
    
        Nella pagina dell'esperimento è possibile visualizzare il numero di destinazioni di calcolo attive e la durata di ogni esecuzione. 
    
    1. Apportare personalizzazioni all'esperimento selezionando esecuzioni da confrontare, aggiungendo grafici o applicando filtri. Queste modifiche possono essere salvate come una **visualizzazione personalizzata** , in modo da poter tornare facilmente al lavoro. Gli utenti con autorizzazioni per l'area di lavoro possono modificare o visualizzare la visualizzazione personalizzata. Inoltre, condividere la visualizzazione personalizzata con altri copiando e incollando l'URL nel browser.  
    
        :::image type="content" source="media/how-to-manage-runs/custom-views.gif" alt-text="Screenshot: creare una visualizzazione personalizzata":::
    
    1. Selezionare un numero di esecuzione specifico.
    
    1. Nella scheda **logs** è possibile trovare i log di diagnostica e degli errori per l'esecuzione della pipeline.
    
    ---
    
## <a name="cancel-or-fail-runs"></a>Esecuzioni annullate o non riuscite

Se si nota un errore o se il completamento dell'esecuzione richiede troppo tempo, è possibile annullare l'esecuzione.

# <a name="python"></a>[Python](#tab/python)

Per annullare un'esecuzione utilizzando l'SDK, utilizzare il [`cancel()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truecancel--) Metodo:

```python
src = ScriptRunConfig(source_directory='.', script='hello_with_delay.py')
local_run = exp.submit(src)
print(local_run.get_status())

local_run.cancel()
print(local_run.get_status())
```

Se l'esecuzione termina, ma contiene un errore (ad esempio, è stato usato lo script di training errato), è possibile usare il [`fail()`](/python/api/azureml-core/azureml.core.run%28class%29#fail-error-details-none--error-code-none---set-status-true-) metodo per contrassegnarlo come non riuscito.

```python
local_run = exp.submit(src)
local_run.fail()
print(local_run.get_status())
```

# <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)

Per annullare un'esecuzione usando l'interfaccia della riga di comando, usare il comando seguente. Sostituire `runid` con l'ID dell'esecuzione

```azurecli-interactive
az ml run cancel -r runid -w workspace_name -e experiment_name
```

Per ulteriori informazioni, vedere [AZ ml Run Cancel](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-cancel).

# <a name="studio"></a>[Studio](#tab/azure-studio)

Per annullare un'esecuzione in studio, seguire questa procedura:

1. Passare alla pipeline in esecuzione nella sezione **esperimenti** o **pipeline** . 

1. Selezionare il numero di esecuzione della pipeline che si desidera annullare.

1. Sulla barra degli strumenti selezionare **Annulla**

---

## <a name="create-child-runs"></a>Crea esecuzioni figlio

Crea esecuzioni figlio per raggruppare le esecuzioni correlate, ad esempio per diverse iterazioni di ottimizzazione iperparametri.

> [!NOTE]
> Le esecuzioni figlio possono essere create solo usando l'SDK.

In questo esempio di codice viene usato lo `hello_with_children.py` script per creare un batch di cinque esecuzioni figlio da un'esecuzione inviata tramite il [`child_run()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truechild-run-name-none--run-id-none--outputs-none-) Metodo:

```python
!more hello_with_children.py
src = ScriptRunConfig(source_directory='.', script='hello_with_children.py')

local_run = exp.submit(src)
local_run.wait_for_completion(show_output=True)
print(local_run.get_status())

with exp.start_logging() as parent_run:
    for c,count in enumerate(range(5)):
        with parent_run.child_run() as child:
            child.log(name="Hello from child run", value=c)
```

> [!NOTE]
> Quando si spostano all'esterno dell'ambito, le esecuzioni figlio vengono automaticamente contrassegnate come completate.

Per creare molte esecuzioni figlio in modo efficiente, utilizzare il [`create_children()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-) metodo. Poiché ogni creazione genera una chiamata di rete, la creazione di un batch di esecuzioni è più efficiente della loro creazione una alla volta.

### <a name="submit-child-runs"></a>Invia esecuzioni figlio

Le esecuzioni figlio possono essere inviate anche da un'esecuzione padre. In questo modo è possibile creare gerarchie di esecuzioni padre e figlio. Non è possibile creare un'esecuzione figlio senza padre: anche se l'esecuzione padre non esegue alcuna operazione ma avvia le esecuzioni figlio, è comunque necessario creare la gerarchia. Lo stato di tutte le esecuzioni è indipendente: un elemento padre può trovarsi nello `"Completed"` stato di esito positivo anche se una o più esecuzioni figlio sono state annullate o non riuscite.  

È possibile che l'esecuzione del figlio usi una configurazione di esecuzione diversa da quella dell'esecuzione padre. Ad esempio, è possibile usare una configurazione meno potente basata sulla CPU per l'elemento padre, usando le configurazioni basate su GPU per gli elementi figlio. Un altro desiderio comune è passare ogni elemento figlio e dati diversi. Per personalizzare un'esecuzione figlio, creare un `ScriptRunConfig` oggetto per l'esecuzione figlio. Il codice seguente esegue le operazioni seguenti:

- Recuperare una risorsa di calcolo denominata `"gpu-cluster"` dall'area di lavoro `ws`
- Scorre i diversi valori di argomento da passare agli `ScriptRunConfig` oggetti figlio
- Crea e invia una nuova esecuzione figlio, usando la risorsa di calcolo personalizzata e l'argomento
- Blocca fino al completamento di tutte le esecuzioni figlio

```python
# parent.py
# This script controls the launching of child scripts
from azureml.core import Run, ScriptRunConfig

compute_target = ws.compute_targets["gpu-cluster"]

run = Run.get_context()

child_args = ['Apple', 'Banana', 'Orange']
for arg in child_args: 
    run.log('Status', f'Launching {arg}')
    child_config = ScriptRunConfig(source_directory=".", script='child.py', arguments=['--fruit', arg], compute_target=compute_target)
    # Starts the run asynchronously
    run.submit_child(child_config)

# Experiment will "complete" successfully at this point. 
# Instead of returning immediately, block until child runs complete

for child in run.get_children():
    child.wait_for_completion()
```

Per creare molte esecuzioni figlio con configurazioni, argomenti e input identici, usare il [`create_children()`](/python/api/azureml-core/azureml.core.run.run?preserve-view=true&view=azure-ml-py#&preserve-view=truecreate-children-count-none--tag-key-none--tag-values-none-) metodo. Poiché ogni creazione genera una chiamata di rete, la creazione di un batch di esecuzioni è più efficiente della loro creazione una alla volta.

All'interno di un'esecuzione figlio, è possibile visualizzare l'ID esecuzione padre:

```python
## In child run script
child_run = Run.get_context()
child_run.parent.id
```

### <a name="query-child-runs"></a>Eseguire query su esecuzioni figlio

Per eseguire una query sulle esecuzioni figlio di un elemento padre specifico, usare il [`get_children()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueget-children-recursive-false--tags-none--properties-none--type-none--status-none---rehydrate-runs-true-) metodo. L' ``recursive = True`` argomento consente di eseguire una query su un albero annidato di elementi figlio e nipoti.

```python
print(parent_run.get_children())
```

### <a name="log-to-parent-or-root-run"></a>Registra nell'elemento padre o nell'esecuzione radice

È possibile usare il `Run.parent` campo per accedere all'esecuzione che ha avviato l'esecuzione figlio corrente. Un caso d'uso comune è quando si desidera consolidare i risultati dei log in un'unica posizione. Si noti che le esecuzioni figlio vengono eseguite in modo asincrono e non esiste alcuna garanzia di ordinamento o sincronizzazione oltre la possibilità che l'elemento padre attenda il completamento delle esecuzioni figlio.

```python
# in child (or even grandchild) run

def root_run(self : Run) -> Run :
    if self.parent is None : 
        return self
    return root_run(self.parent)

current_child_run = Run.get_context()
root_run(current_child_run).log("MyMetric", f"Data from child run {current_child_run.id}")

```


## <a name="tag-and-find-runs"></a>Esegui tag e trova

In Azure Machine Learning, è possibile usare proprietà e tag per organizzare ed eseguire query sulle esecuzioni per ottenere informazioni importanti.

* Aggiungere proprietà e tag

    # <a name="python"></a>[Python](#tab/python)
    
    Per aggiungere metadati ricercabili alle esecuzioni, usare il [`add_properties()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=trueadd-properties-properties-) metodo. Il codice seguente, ad esempio, aggiunge la `"author"` proprietà all'esecuzione:
    
    ```Python
    local_run.add_properties({"author":"azureml-user"})
    print(local_run.get_properties())
    ```
    
    Le proprietà non sono modificabili, quindi creano un record permanente a scopo di controllo. L'esempio di codice seguente genera un errore perché è già stato aggiunto `"azureml-user"` come `"author"` valore della proprietà nel codice precedente:
    
    ```Python
    try:
        local_run.add_properties({"author":"different-user"})
    except Exception as e:
        print(e)
    ```
    
    Diversamente dalle proprietà, i tag sono modificabili. Per aggiungere informazioni significative e ricercabili per i consumer dell'esperimento, usare il [`tag()`](/python/api/azureml-core/azureml.core.run%28class%29?preserve-view=true&view=azure-ml-py#&preserve-view=truetag-key--value-none-) metodo.
    
    ```Python
    local_run.tag("quality", "great run")
    print(local_run.get_tags())
    
    local_run.tag("quality", "fantastic run")
    print(local_run.get_tags())
    ```
    
    È anche possibile aggiungere tag stringa semplici. Quando questi tag vengono visualizzati nel dizionario dei tag come chiavi, hanno un valore pari a `None` .
    
    ```Python
    local_run.tag("worth another look")
    print(local_run.get_tags())
    ```
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    > [!NOTE]
    > Usando l'interfaccia della riga di comando, è possibile aggiungere o aggiornare solo i tag.
    
    Per aggiungere o aggiornare un tag, usare il comando seguente:
    
    ```azurecli-interactive
    az ml run update -r runid --add-tag quality='fantastic run'
    ```
    
    Per ulteriori informazioni, vedere [AZ ml Run Update](/cli/azure/ext/azure-cli-ml/ml/run?preserve-view=true&view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-update).
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    È possibile visualizzare le proprietà e i tag in studio, ma non modificarli.
    
    ---

* Proprietà e tag della query

    È possibile eseguire query sulle esecuzioni all'interno di un esperimento per restituire un elenco di esecuzioni corrispondenti a proprietà e tag specifici.

    # <a name="python"></a>[Python](#tab/python)
    
    ```Python
    list(exp.get_runs(properties={"author":"azureml-user"},tags={"quality":"fantastic run"}))
    list(exp.get_runs(properties={"author":"azureml-user"},tags="worth another look"))
    ```
    
    # <a name="azure-cli"></a>[Interfaccia della riga di comando di Azure](#tab/azure-cli)
    
    L'interfaccia della riga di comando di Azure supporta le query [JMESPath](http://jmespath.org) , che possono essere usate per filtrare le esecuzioni in base a proprietà e tag. Per usare una query JMESPath con l'interfaccia della riga di comando di Azure, specificarla con il `--query` parametro. Gli esempi seguenti illustrano alcune query che usano proprietà e Tag:
    
    ```azurecli-interactive
    # list runs where the author property = 'azureml-user'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user']
    # list runs where the tag contains a key that starts with 'worth another look'
    az ml run list --experiment-name experiment [?tags.keys(@)[?starts_with(@, 'worth another look')]]
    # list runs where the author property = 'azureml-user' and the 'quality' tag starts with 'fantastic run'
    az ml run list --experiment-name experiment [?properties.author=='azureml-user' && tags.quality=='fantastic run']
    ```
    
    Per altre informazioni sull'esecuzione di query sui risultati dell'interfaccia della riga di comando di Azure, vedere [eseguire query sull'output del comando CLI](/cli/azure/query-azure-cli?preserve-view=true&view=azure-cli-latest)
    
    # <a name="studio"></a>[Studio](#tab/azure-studio)
    
    1. Passare alla sezione **pipeline** .
    
    1. Usare la barra di ricerca per filtrare le pipeline usando i tag, le descrizioni, i nomi degli esperimenti e il nome del mittente.
    
    ---

## <a name="example-notebooks"></a>Notebook di esempio

I notebook seguenti illustrano i concetti in questo articolo:

* Per altre informazioni sulle API di registrazione, vedere il [notebook dell'API di registrazione](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api/logging-api.ipynb).

* Per ulteriori informazioni sulla gestione delle esecuzioni con l'SDK Azure Machine Learning, vedere [gestire il notebook di esecuzione](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/manage-runs/manage-runs.ipynb).

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come registrare le metriche per gli esperimenti, vedere [metriche di log durante le esecuzioni di training](how-to-track-experiments.md).
* Per informazioni su come monitorare le risorse e i log da Azure Machine Learning, vedere [Azure Machine Learning di monitoraggio](monitor-azure-machine-learning.md).