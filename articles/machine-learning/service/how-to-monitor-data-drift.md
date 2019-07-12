---
title: Rilevare lo sfasamento di dati (anteprima) in distribuzioni con configurazione servizio contenitore di AZURE
titleSuffix: Azure Machine Learning service
description: Rilevare lo sfasamento di dati in Azure Kubernetes Service distribuito nel servizio Azure Machine Learning i modelli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 07/08/2019
ms.openlocfilehash: 3b8152bde8b7e44dde1b0b9c82216333778f83da
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/11/2019
ms.locfileid: "67806013"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Rilevare lo sfasamento di dati (anteprima) per i modelli distribuiti a Azure Kubernetes Service (AKS)

In questo articolo descrive come monitorare la deviazione di dati tra il set di dati di training e inferenza dei dati di un modello distribuito. Nel contesto di machine learning, sottoposto a training modelli di machine learning verifichi le prestazioni della stima con funzionalità ridotte a causa di deviazione. Con il servizio di Azure Machine Learning, è possibile monitorare lo sfasamento di dati e il servizio può inviare un messaggio di avviso all'utente quando viene rilevato uno sfasamento.

## <a name="what-is-data-drift"></a>Che cos'è lo sfasamento di dati?

Lo sfasamento di dati si verifica quando i dati serviti da un modello nell'ambiente di produzione sono diversi da quelli usati per il training del modello. È uno dei motivi principali in cui accuratezza del modello diminuisca nel tempo, in questo modo i dati di monitoraggio sfasamento consente di rilevare i problemi di prestazioni del modello. 

## <a name="what-can-i-monitor"></a>Ciò che è possibile monitorare?

Con il servizio di Azure Machine Learning, è possibile monitorare gli input per un modello distribuito nel servizio contenitore di AZURE e confrontare questi dati per il set di dati di training per il modello. A intervalli regolari, siano i dati di inferenza [snapshot e il profiling](how-to-explore-prepare-data.md), quindi calcolato rispetto ai set di dati della linea di base per produrre un'analisi di sfasamento di dati che: 

+ Calcola la grandezza di sfasamento di dati, denominato il coefficiente di deviazione.
+ Le misure i dati dello sfasamento contributo dalla funzionalità, per informare le funzionalità che ha causato lo sfasamento di dati.
+ Le misure a metriche di distanza. Attualmente vengono calcolate Wasserstein e distanza di energia.
+ Distribuzioni di misure delle funzionalità. Attualmente la stima di densità del kernel e gli istogrammi.
+ Inviare gli avvisi per i dati dello sfasamento tramite posta elettronica.

> [!Note]
> Questo servizio è in (anteprima) e limitate nelle opzioni di configurazione. Vedere la [documentazione dell'API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) e [note sulla versione](azure-machine-learning-release-notes.md) per informazioni dettagliate e gli aggiornamenti. 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning-service"></a>Modo in cui viene monitorato lo sfasamento di dati nel servizio Azure Machine Learning

Usa il servizio di Azure Machine Learning, lo sfasamento di dati viene monitorato tramite i set di dati o le distribuzioni. Per il monitoraggio sfasamento di dati, viene specificato un set di dati della linea di base - generalmente il training set di dati per un modello. Un secondo set di dati - in genere i dati di input modello raccolti da una distribuzione - è testata con il set di dati della linea di base. Entrambi i set di dati [profilate](how-to-explore-prepare-data.md#explore-with-summary-statistics) e servizio di monitoraggio dello sfasamento di input per i dati. Viene eseguito il training di un modello di machine learning per rilevare le differenze tra due set di dati. Le prestazioni del modello viene convertita nel coefficiente di deviazione, che misura la grandezza di deviazione tra due set di dati. Usando [interpretazione del modello](machine-learning-interpretability-explainability.md), vengono calcolate le funzionalità che contribuiscono al coefficiente di deviazione. Dal profilo del set di dati, vengano tenuta traccia informazioni statistiche su ognuna delle funzionalità. 

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non hai uno, creare un account gratuito, prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

- Un'area di lavoro del servizio di Azure Machine Learning e Azure Machine Learning SDK per Python installato. Usare le istruzioni riportate in [Creare un'area di lavoro del servizio Azure Machine Learning](setup-create-workspace.md#sdk) per eseguire queste operazioni:

    - Creare un ambiente Miniconda
    - Installare Azure Machine Learning SDK per Python
    - Creare un'area di lavoro
    - Scrivere un file di configurazione dell'area di lavoro (aml_config/config.json).

- Installare lo sfasamento di dati SDK usando il comando seguente:

    ```shell
    pip install azureml-contrib-datadrift
    ```

- Creare un [set di dati](how-to-create-register-datasets.md) dai dati di training del modello.

- Specificare il set di dati di training durante [registrazione](concept-model-management-and-deployment.md) il modello. Nell'esempio seguente viene illustrato l'utilizzo di `datasets` parametro per specificare il set di dati di training:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Abilitare la raccolta di dati del modello](how-to-enable-data-collection.md) per raccogliere i dati dalla distribuzione del modello di servizio contenitore di AZURE e verificare che i dati vengono raccolti nel `modeldata` contenitore blob.

## <a name="configure-data-drift"></a>Configurare lo sfasamento di dati
Per configurare lo sfasamento di dati per l'esperimento, importare le dipendenze, come illustrato nell'esempio seguente di Python. 

In questo esempio viene illustrata la configurazione di [ `DataDriftDetector` ](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) oggetto:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Invia un'esecuzione DataDriftDetector

Con il `DataDriftDetector` oggetto configurato, è possibile inviare un [sfasamento di dati eseguito](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) in una determinata data per il modello. Come parte dell'esecuzione, abilitare avvisi DataDriftDetector impostando il `drift_threshold` parametro. Se il [datadrift_coefficient](#metrics) supera il determinato `drift_threshold`, viene inviato un messaggio di posta elettronica.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Visualizzare le metriche di deviazione

<a name="metrics"></a>

Dopo aver inviato il DataDriftDetector eseguire, si è in grado di visualizzare le metriche di sfasamento che vengono salvate in ogni iterazione di esecuzione per un'attività di sfasamento di dati:


|Metrica|Descrizione|
--|--|
wasserstein_distance|Distanza statistica definita per la distribuzione numerica unidimensionale.|
energy_distance|Distanza statistica definita per la distribuzione numerica unidimensionale.|
datadrift_coefficient|Calcolato come il coefficiente di correlazione di Matthew allo stesso modo, ma questo output è un numero reale compreso tra 0 e 1. Nel contesto di deviazione, 0 non indica nessun orientamenti e 1 indica che lo sfasamento massimo.|
datadrift_contribution|Importanza delle caratteristiche delle funzionalità contributo dello sfasamento.|

Esistono diversi modi per visualizzare le metriche di deviazione:

* Usare il widget di Jupyter.
* Usare la [ `get_metrics()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) funzione in qualsiasi `datadrift` oggetto di esecuzione.
* Visualizzare le metriche nel portale di Azure nel modello

Python di esempio seguente viene illustrato come tracciare le metriche di sfasamento di dati rilevanti. Per creare visualizzazioni personalizzate, è possibile usare le metriche restituite:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Vedere lo sfasamento di dati rilevato da Azure Machine Learning](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Lo sfasamento le analisi dei dati di pianificazione 

Quando si abilita il rilevamento di deviazione di dati, un DataDriftDetector viene eseguito in base alla frequenza specificata, pianificata. Se raggiunge la datadrift_coefficient il determinato `drift_threshold`, un messaggio di posta elettronica viene inviato con ogni esecuzione pianificata. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

La configurazione del Rilevatore di sfasamento di dati può essere visualizzata nella pagina dei dettagli del modello nel portale di Azure.

![Portale di Azure Data deviazione della configurazione](media/how-to-monitor-data-drift/drift_config.png)

## <a name="view-results-in-azure-ml-workspace-ui"></a>Visualizzare i risultati nell'interfaccia utente dell'area di lavoro di Machine Learning di Azure

Per visualizzare i risultati nell'interfaccia utente dell'area di lavoro di Machine Learning di Azure, passare alla pagina del modello. Nella scheda dettagli del modello, verrà visualizzata la configurazione di sfasamento di dati. Una scheda 'Sfasamento di dati (anteprima)' è ora disponibile che mostrano metriche lo sfasamento di dati. 

![Portale di Azure lo sfasamento di dati](media/how-to-monitor-data-drift/drift_ui.png)

## <a name="receiving-drift-alerts"></a>Ricevere avvisi di deviazione

Impostando il coefficiente di deviazione della soglia di avviso e fornendo un indirizzo di posta elettronica, un' [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) avviso tramite posta elettronica viene inviato automaticamente ogni volta che il coefficiente lo sfasamento è superiore alla soglia. 

Perché è possibile configurare avvisi personalizzati e le azioni, tutte le metriche di sfasamento di dati vengono archiviate nel [Application Insights](how-to-enable-app-insights.md) risorsa creata con l'area di lavoro del servizio di Azure Machine Learning. È possibile seguire il collegamento nell'avviso di posta elettronica per la query di Application Insights.

![Avviso di posta elettronica di sfasamento di dati](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Ripetere il training del modello dopo gli orientamenti

Quando lo sfasamento di dati influisce negativamente sulle prestazioni del modello distribuito, è possibile ripetere il training di modelli. Quanto segue [ `diff()` ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py#diff-rhs-dataset--compute-target-none--columns-none-
) metodo offre un'idea iniziale di le modifiche apportate tra i vecchi e nuovi set di dati. 

```python
from azureml.core import Dataset

old_training_dataset.diff(new_training_dataset)
```

Basata sull'output del codice precedente, è possibile ripetere il training del modello. A tale scopo, procedere con la procedura seguente.

* Analizzare i dati raccolti e preparare i dati per il training del nuovo modello.
* Suddividerlo in dati di training/test.
* Il training del modello usando i nuovi dati.
* Valutare le prestazioni del modello appena generato.
* Se le prestazioni sono migliori rispetto al modello di produzione, distribuire nuovo modello.

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio completo dell'uso di sfasamento di dati, vedere la [dati di Azure Machine Learning deviano notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). Questo Notebook di Jupyter illustra l'uso di un' [set di dati aperto Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) per addestrare un modello per stimare le condizioni meteo, distribuirla in AKS ed esegue il monitoraggio sfasamento di dati. 

* È possibile lasciare le domande, commenti o suggerimenti notevolmente quando lo sfasamento di dati viene spostato verso generale la disponibilità. Usare il pulsante commenti e suggerimenti di prodotto seguente. 
