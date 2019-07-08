---
title: Rilevare lo sfasamento di dati (anteprima) in distribuzioni con configurazione servizio contenitore di AZURE
titleSuffix: Azure Machine Learning service
description: Informazioni su come rilevare lo sfasamento di dati in Azure Kubernetes Service distribuito nel servizio Azure Machine Learning i modelli.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 06/20/2019
ms.openlocfilehash: c446c8236ca64948f0bb6a8354a83579cc6ff24c
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443952"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service"></a>Rilevare lo sfasamento di dati (anteprima) per i modelli distribuiti in Azure Kubernetes Service
In questo articolo descrive come monitorare la deviazione di dati tra il set di dati di training e inferenza dei dati di un modello distribuito. 

## <a name="what-is-data-drift"></a>Che cos'è lo sfasamento di dati?

Lo sfasamento di dati, noto anche come sfasamento del concetto, è uno dei motivi principali in cui accuratezza del modello comporta una riduzione nel tempo. Si verifica quando i dati forniti a un modello nell'ambiente di produzione sono diversi dai dati utilizzati per il training del modello. Il servizio di Azure Machine Learning è possibile monitorare lo sfasamento di dati e, quando viene rilevato uno sfasamento, il servizio può inviare un messaggio di avviso all'utente.  

> [!Note]
> Questo servizio è in (anteprima) e limitate nelle opzioni di configurazione. Vedere la [documentazione dell'API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) e [note sulla versione](azure-machine-learning-release-notes.md) per informazioni dettagliate e gli aggiornamenti. 

## <a name="what-can-i-monitor"></a>Ciò che è possibile monitorare?
Con il servizio di Azure Machine Learning, è possibile monitorare gli input per un modello distribuito nel servizio contenitore di AZURE e confrontare questi dati per il set di dati di training per il modello. A intervalli regolari, siano i dati di inferenza [snapshot e il profiling](how-to-explore-prepare-data.md), quindi calcolato rispetto ai set di dati della linea di base per produrre un'analisi di sfasamento di dati che: 

+ Calcola la grandezza di sfasamento di dati, denominato il coefficiente di deviazione.
+ Le misure i dati dello sfasamento contributo dalla funzionalità, per informare le funzionalità che ha causato lo sfasamento di dati.
+ Le misure a metriche di distanza. Attualmente vengono calcolate Wasserstein e distanza di energia.
+ Distribuzioni di misure delle funzionalità. Attualmente la stima di densità del kernel e gli istogrammi.
+ Inviare gli avvisi per i dati dello sfasamento tramite posta elettronica.

Per informazioni dettagliate sul modo in cui vengono calcolate queste metriche, vedere la [concetto di sfasamento di dati](concept-data-drift.md) articolo.

## <a name="prerequisites"></a>Prerequisiti

- Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

- Un'area di lavoro del servizio di Azure Machine Learning e Azure Machine Learning SDK per Python installato. Informazioni su come ottenere questi prerequisiti usando il documento [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).

- [Configurare l'ambiente](how-to-configure-environment.md)e quindi installare lo sfasamento di dati SDK usando il comando seguente:

    ```
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

## <a name="import-dependencies"></a>Importare le dipendenze 
Importare le dipendenze usate in questa guida:

```python
# Azure ML service packages 
from azureml.core import Experiment, Run, RunDetails
from azureml.contrib.datadrift import DataDriftDetector, AlertConfiguration
``` 

## <a name="configure-data-drift"></a>Configurare lo sfasamento di dati 

Python di esempio seguente viene illustrata la configurazione di `DataDriftDetector` oggetto:

```python
# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

Per altre informazioni, vedere il `[DataDrift](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py)` documentazione di riferimento per la classe.

## <a name="submit-a-datadriftdetector-run"></a>Invia un'esecuzione DataDriftDetector

Con il `DataDriftDetector` oggetto configurato, è possibile inviare un [sfasamento di dati eseguito](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) in una determinata data per il modello. 

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

Python di esempio seguente viene illustrato come tracciare le metriche di sfasamento di dati rilevanti. Per creare visualizzazioni personalizzate, è possibile usare le metriche restituite:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Vedere lo sfasamento di dati rilevato da Azure Machine Learning](media/how-to-monitor-data-drift/drift_show.png)

Per informazioni dettagliate sulle metriche calcolate, vedere la [concetto di sfasamento di dati](concept-data-drift.md) articolo.

## <a name="schedule-data-drift-scans"></a>Lo sfasamento le analisi dei dati di pianificazione 

Quando si abilita il rilevamento di deviazione di dati, un DataDriftDetector viene eseguito in base alla frequenza specificata, pianificata. Se il coefficiente lo sfasamento è superiore alla soglia specificata, viene inviato un messaggio di posta elettronica. 

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

Impostando il coefficiente di deviazione della soglia di avviso e fornendo un indirizzo di posta elettronica, un' [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) avviso tramite posta elettronica viene inviato automaticamente ogni volta che il coefficiente lo sfasamento è superiore alla soglia. Perché è possibile configurare le azioni e gli avvisi personalizzati, tutte le metriche di sfasamento di dati vengono archiviate nella risorsa di Application Insights è stato creato con l'area di lavoro del servizio di Azure Machine Learning. È possibile seguire il collegamento nell'avviso di posta elettronica per la query di Application Insights.

![Avviso di posta elettronica di sfasamento di dati](media/how-to-monitor-data-drift/drift_email.png)

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio completo dell'uso di sfasamento di dati, vedere la [dati di Azure Machine Learning deviano notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/data-drift/azure-ml-datadrift.ipynb). Questo Notebook di Jupyter illustra l'uso di un' [set di dati aperto Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) per addestrare un modello per stimare le condizioni meteo, distribuirla in AKS ed esegue il monitoraggio sfasamento di dati. 

* È possibile lasciare le domande, commenti o suggerimenti notevolmente quando lo sfasamento di dati viene spostato verso generale la disponibilità. Usare il pulsante commenti e suggerimenti di prodotto seguente. 
