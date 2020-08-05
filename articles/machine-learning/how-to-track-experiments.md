---
title: Registrare gli esperimenti e le metriche di ML
titleSuffix: Azure Machine Learning
description: Monitorare gli esperimenti di Azure ML e le metriche di esecuzione per migliorare il processo di creazione del modello. Aggiungere la registrazione allo script di training usando Run. log, Run. start_logging o ScriptRunConfig.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: d0c6488f9a75bbf9ba6775138edeed9c4a397abf
ms.sourcegitcommit: 1b2d1755b2bf85f97b27e8fbec2ffc2fcd345120
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/04/2020
ms.locfileid: "87552221"
---
# <a name="enable-logging-in-azure-ml-training-runs"></a>Abilitare la registrazione nelle esecuzioni di training di Azure ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Il Azure Machine Learning Python SDK consente di registrare le informazioni in tempo reale usando sia il pacchetto di registrazione Python predefinito che la funzionalità specifica dell'SDK. È possibile accedere in locale e inviare i log all'area di lavoro nel portale.

I log consentono di diagnosticare gli errori e gli avvisi o di rilevare le metriche delle prestazioni, ad esempio i parametri e le prestazioni del modello. Questo articolo illustra come abilitare la registrazione negli scenari seguenti:

> [!div class="checklist"]
> * Sessioni di training interattive
> * Invio di processi di training mediante ScriptRunConfig
> * Impostazioni native di Python `logging`
> * Registrazione da origini aggiuntive


> [!TIP]
> Questo articolo illustra come monitorare il processo di training del modello. Se si è interessati al monitoraggio dell'utilizzo delle risorse e degli eventi di Azure Machine Learning, ad esempio le quote, le esecuzioni di training completate o le distribuzioni di modelli completati, vedere [monitoraggio Azure Machine Learning](monitor-azure-machine-learning.md).

## <a name="data-types"></a>Tipi di dati

È possibile registrare più tipi di dati, tra cui valori scalari, elenchi, tabelle, immagini, directory e altro ancora. Per ulteriori informazioni e per esempi di codice Python per tipi di dati diversi, vedere la [pagina di riferimento della classe Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

## <a name="interactive-logging-session"></a>Sessione di registrazione interattiva

Le sessioni di registrazione interattiva vengono in genere usate negli ambienti notebook. Il metodo [Experiment. start_logging ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment(class)?view=azure-ml-py#start-logging--args----kwargs-) avvia una sessione di registrazione interattiva. Tutte le metriche registrate durante la sessione vengono aggiunte al record di esecuzione nell'esperimento. Il metodo [Run. complete ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#complete--set-status-true-) termina le sessioni e contrassegna l'esecuzione come completata.

Il frammento di codice seguente usa una sessione di registrazione interattiva per registrare i parametri di training e le metriche delle prestazioni con il metodo [Run. log ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----) . Carica inoltre il modello sottoposto a training in un percorso di output specificato.

[!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb?name=create_experiment)]

Per un notebook di esempio completo che usa la registrazione interattiva, vedere eseguire il [training di un modello in un notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook/train-within-notebook.ipynb).

## <a name="scriptrunconfig-logs"></a>Log ScriptRunConfig

Questa sezione illustra come aggiungere codice di registrazione all'interno delle esecuzioni di ScriptConfig. È possibile usare la classe [**ScriptRunConfig**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) per incapsulare gli script e gli ambienti per le esecuzioni ripetibili. È anche possibile usare questa opzione per visualizzare un widget di Visual Jupyter Notebooks per il monitoraggio.

In questo esempio viene eseguito uno sweep di parametri su valori alfa e vengono acquisiti i risultati utilizzando il metodo [Run. log ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py#log-name--value--description----) .

1. Creare uno script di training che includa la logica di registrazione `train.py` .

   [!code-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train.py)]


1. Inviare lo ```train.py``` script per l'esecuzione in un ambiente gestito dall'utente. L'intera cartella di script viene inviata per il training.

   [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=src)] [!notebook-python[] (~/MachineLearningNotebooks/how-to-use-azureml/training/train-on-local/train-on-local.ipynb?name=run)]

    Il `show_output` parametro attiva la registrazione dettagliata, che consente di visualizzare i dettagli del processo di training, nonché le informazioni sulle risorse remote o sulle destinazioni di calcolo. Usare il codice seguente per attivare la registrazione dettagliata quando si invia l'esperimento.

```python
run = exp.submit(src, show_output=True)
```

È anche possibile usare lo stesso parametro nella funzione `wait_for_completion` all'esecuzione risultante.

```python
run.wait_for_completion(show_output=True)
```

Per un notebook di esempio completo che usa i registri ScriptRunConfigs, vedere eseguire [il training di un modello localmente](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local/train-on-local.ipynb).

## <a name="native-python-logging"></a>Registrazione Python nativa

Alcuni log nell'SDK possono contenere un errore che indica di impostare il livello di registrazione su DEBUG. Per impostare il livello di registrazione, aggiungere il codice seguente allo script.

```python
import logging
logging.basicConfig(level=logging.DEBUG)
```

## <a name="additional-logging-sources"></a>Origini di registrazione aggiuntive

Azure Machine Learning possono anche registrare le informazioni di altre origini durante il training, ad esempio le esecuzioni automatiche di Machine Learning o i contenitori Docker che eseguono i processi. Questi log non sono documentati, ma se si riscontrano problemi e si contatta il supporto tecnico Microsoft, potrebbero essere in grado di usare questi log durante la risoluzione dei problemi.

Per informazioni sulla registrazione delle metriche in Azure Machine Learning Designer (anteprima), vedere [come registrare le metriche nella finestra di progettazione (anteprima)](how-to-track-designer-experiments.md) .

## <a name="example-notebooks"></a>Notebook di esempio
I notebook seguenti illustrano i concetti descritti in questo articolo:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/track-and-monitor-experiments/logging-api](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/track-and-monitor-experiments/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come usare Azure Machine Learning, vedere questi articoli:

* Informazioni su come [registrare le metriche in Azure Machine Learning Designer (anteprima)](how-to-track-designer-experiments.md).

* Vedere un esempio di come registrare il modello migliore e distribuirlo nell'esercitazione [Eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](tutorial-train-models-with-aml.md).
