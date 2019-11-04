---
title: Rilevare la tendenza dei dati (anteprima) nelle distribuzioni AKS
titleSuffix: Azure Machine Learning
description: Rilevare la tendenza dei dati nei modelli distribuiti del servizio Kubernetes di Azure in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: 536f3ab506dcbe2b8997f2c1870f25244b6c070f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489649"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Rilevare la tendenza dei dati (anteprima) nei modelli distribuiti in Azure Kubernetes Service (AKS)
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku.md)]

Questo articolo illustra come monitorare la tendenza dei dati tra il set di dati di training e i dati di inferenza di un modello distribuito. Nel contesto di Machine Learning, i modelli di apprendimento automatico sottoposti a training possono comportare una riduzione delle prestazioni di stima a causa della deviazione. Con Azure Machine Learning è possibile monitorare la tendenza dei dati e il servizio può inviare un avviso di posta elettronica quando viene rilevata una deriva.

## <a name="what-is-data-drift"></a>Che cos'è la deriva dei dati?

Nel contesto di Machine Learning, la deriva dei dati è la modifica dei dati di input del modello che comporta un peggioramento delle prestazioni del modello. Si tratta di uno dei motivi principali in cui l'accuratezza del modello diminuisce nel tempo, quindi il monitoraggio della deriva dei dati consente di rilevare i problemi di prestazioni del modello. 

## <a name="what-can-i-monitor"></a>Che cosa è possibile monitorare?

Con Azure Machine Learning, è possibile monitorare gli input per un modello distribuito in AKS e confrontare questi dati con il set di dati di training per il modello. A intervalli regolari, i dati di inferenza sono [snapshot e profilati](how-to-explore-prepare-data.md), quindi vengono calcolati in base al set di dati baseline per produrre un'analisi della deriva dei dati che: 

+ Misura la grandezza della deviazione dei dati, denominata coefficiente di tendenza.
+ Misura il contributo alla derivazione dei dati per funzionalità, che informa quali funzionalità hanno causato la deriva dei dati.
+ Misura le metriche della distanza. Attualmente Wasserstein e la distanza di energia vengono calcolate.
+ Misura le distribuzioni delle funzionalità. Stima della densità del kernel e istogrammi.
+ Invia avvisi alla deviazione dei dati tramite posta elettronica.

> [!Note]
> Questo servizio è in (anteprima) e limitato nelle opzioni di configurazione. Per informazioni dettagliate e aggiornamenti, vedere la [documentazione dell'API](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/?view=azure-ml-py) e le [Note sulla versione](azure-machine-learning-release-notes.md) . 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Modalità di monitoraggio della deviazione dei dati in Azure Machine Learning

Con Azure Machine Learning, la deriva dei dati viene monitorata tramite set di dati o distribuzioni. Per monitorare la deriva dei dati, viene specificato un set di dati di base, in genere il set di dati di training per un modello. Un secondo set di dati, in genere il modello di dati di input raccolti da una distribuzione, viene testato rispetto al set di dati di base. Entrambi i set di dati vengono profilati e vengono inseriti nel servizio di monitoraggio della deriva dati. Viene eseguito il training di un modello di apprendimento automatico per rilevare le differenze tra i due set di impostazioni. Le prestazioni del modello vengono convertite nel coefficiente di derivazione, che misura la grandezza della deviazione tra i due set di impostazioni. Grazie all' [interpretazione dei modelli](how-to-machine-learning-interpretability.md), vengono calcolate le funzionalità che contribuiscono al coefficiente di drifting. Dal profilo del set di dati vengono rilevate informazioni statistiche su ogni funzionalità. 

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha un account, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- Installazione di Azure Machine Learning SDK per Python. Usare le istruzioni riportate in [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) per eseguire queste operazioni:

    - Creare un ambiente Miniconda
    - Installare Azure Machine Learning SDK per Python

- [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md).

- Un [file di configurazione](how-to-configure-environment.md#workspace)dell'area di lavoro.

- Installare Data Drift SDK usando il comando seguente:

    ```shell
    pip install azureml-datadrift
    ```

- Creare un [set](how-to-create-register-datasets.md) di dati dai dati di training del modello.

- Specificare il set di dati di training durante la [registrazione](concept-model-management-and-deployment.md) del modello. Nell'esempio seguente viene illustrato l'utilizzo del parametro `datasets` per specificare il set di dati di training:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Abilitare la raccolta dei dati del modello](how-to-enable-data-collection.md) per raccogliere dati dalla distribuzione di AKS del modello e verificare che i dati vengano raccolti nel contenitore BLOB `modeldata`.

## <a name="configure-data-drift"></a>Configurare la tendenza dei dati
Per configurare la tendenza dei dati per l'esperimento, importare le dipendenze come illustrato nell'esempio Python seguente. 

In questo esempio viene illustrata la configurazione dell'oggetto [`DataDriftDetector`](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector.datadriftdetector?view=azure-ml-py) :

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DatadriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Inviare un'esecuzione DataDriftDetector

Con l'oggetto `DataDriftDetector` configurato, è possibile inviare un'operazione di [spostamento dei dati](https://docs.microsoft.com/python/api/azureml-contrib-datadrift/azureml.contrib.datadrift.datadriftdetector%28class%29?view=azure-ml-py#run-target-date--services--compute-target-name-none--create-compute-target-false--feature-list-none--drift-threshold-none-) in una data specificata per il modello. Come parte dell'esecuzione, abilitare gli avvisi DataDriftDetector impostando il parametro `drift_threshold`. Se il valore di [datadrift_coefficient](#metrics) è superiore al `drift_threshold`specificato, viene inviato un messaggio di posta elettronica.

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

## <a name="visualize-drift-metrics"></a>Visualizzare le metriche di Drift

<a name="metrics"></a>

Dopo aver inviato l'esecuzione di DataDriftDetector, è possibile visualizzare le metriche di spostamento salvate in ogni iterazione di esecuzione per un'attività di drifting dei dati:


|Metrica|Description|
--|--|
wasserstein_distance|Distanza statistica definita per la distribuzione numerica unidimensionale.|
energy_distance|Distanza statistica definita per la distribuzione numerica unidimensionale.|
datadrift_coefficient|Calcolato in modo analogo al coefficiente di correlazione di Matthew, ma questo output è un numero reale compreso tra 0 e 1. Nel contesto di Drift, 0 indica No Drift e 1 indica la deviazione massima.|
datadrift_contribution|Importanza delle funzionalità che contribuiscono alla deviazione.|

Sono disponibili diversi modi per visualizzare le metriche di Drift:

* Usare il [widget `RunDetails`Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Utilizzare la funzione [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) per qualsiasi oggetto di esecuzione `datadrift`.
* Visualizzare le metriche dalla sezione **modelli** dell'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).

Nell'esempio Python seguente viene illustrato come tracciare le metriche di spostamento dei dati rilevanti. È possibile usare le metriche restituite per creare visualizzazioni personalizzate:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per serivice.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Vedere la tendenza dei dati rilevata da Azure Machine Learning](media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Pianifica analisi della deviazione dati 

Quando si Abilita il rilevamento della tendenza dei dati, viene eseguito un DataDriftDetector alla frequenza pianificata specificata. Se il datadrift_coefficient raggiunge il `drift_threshold`specificato, viene inviato un messaggio di posta elettronica con ogni esecuzione pianificata. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

La configurazione del rilevamento della derivazione dei dati può essere visualizzata in **modelli** nella scheda **Dettagli** dell'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).

![Spostamento dei dati di Azure Machine Learning Studio](media/how-to-monitor-data-drift/drift-config.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Visualizzare i risultati in Azure Machine Learning Studio

Per visualizzare i risultati nell'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com), passare alla pagina del modello. Nella scheda Dettagli del modello viene visualizzata la configurazione della deviazione dati. È ora disponibile una scheda della barra di **spostamento dei** dati in cui vengono visualizzate le metriche di spostamento dei dati. 

[Spostamento dei dati ![Azure Machine Learning Studio](media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)


## <a name="receiving-drift-alerts"></a>Ricezione di avvisi di Drift

Impostando la soglia di avviso per il coefficiente di tendenza e fornendo un indirizzo di posta elettronica, viene automaticamente inviato un avviso di posta elettronica di [monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) ogni volta che il coefficiente di spostamento supera la soglia. 

Per configurare gli avvisi e le azioni personalizzati, tutte le metriche di spostamento dei dati vengono archiviate nella risorsa [Application Insights](how-to-enable-app-insights.md) creata insieme all'area di lavoro Azure Machine Learning. È possibile seguire il collegamento nell'avviso di posta elettronica per la query Application Insights.

![Avviso di posta elettronica Drift dei dati](media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Ripetere il training del modello dopo la deriva

Quando la tendenza dei dati influisce negativamente sulle prestazioni del modello distribuito, è il momento di ripetere il training del modello. A tale scopo, procedere con i passaggi seguenti.

* Esaminare i dati raccolti e preparare i dati per il training del nuovo modello.
* Suddividerla in dati di training/test.
* Eseguire di nuovo il training del modello utilizzando i nuovi dati.
* Valutare le prestazioni del modello appena generato.
* Distribuire un nuovo modello se le prestazioni sono migliori rispetto al modello di produzione.

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio completo dell'uso della dispersione dei dati, vedere il [notebook di spostamento dei dati di Azure ml](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/azure-ml-datadrift.ipynb). Questo Jupyter Notebook illustra l'uso di un [set di dati di Azure Open](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) per eseguire il training di un modello per stimare il tempo, distribuirlo in AKS e monitorare la tendenza dei dati. 

* Si apprezzeranno molto le domande, i commenti o i suggerimenti quando lo spostamento dei dati viene spostato verso la disponibilità generale. Usare il pulsante feedback del prodotto sotto. 
