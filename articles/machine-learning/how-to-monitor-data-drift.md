---
title: Rilevare la deriva dei dati nelle distribuzioni AKSDetect data drift on AKS deployments
titleSuffix: Azure Machine Learning
description: Rilevare la deriva dei dati (anteprima) nei modelli distribuiti del servizio Azure Kubernetes in Azure Machine Learning.Detect data drift (preview) on Azure Kubernetes Service deployed models in Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: cody-dkdc
ms.date: 11/04/2019
ms.openlocfilehash: d1da7309b296b57db0c28d5b52fe91efa86709c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75537006"
---
# <a name="detect-data-drift-preview-on-models-deployed-to-azure-kubernetes-service-aks"></a>Rilevare la deriva dei dati (anteprima) nei modelli distribuiti nel servizio Azure Kubernetes (AKS)Detect data drift (preview) on models deployed to Azure Kubernetes Service (AKS)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku.md)]

In questo articolo viene illustrato come monitorare la deriva dei dati tra il set di dati di training e i dati di inferenza di un modello distribuito. Nel contesto dell'apprendimento automatico, i modelli di apprendimento automatico addestrati possono verificarsi prestazioni di stima ridotte a causa della deriva. Con Azure Machine Learning è possibile monitorare la deriva dei dati e il servizio può inviare un avviso tramite posta elettronica quando viene rilevata una deriva.

## <a name="what-is-data-drift"></a>Che cos'è la deriva dei dati?

Nel contesto dell'apprendimento automatico, la deriva dei dati è la modifica dei dati di input del modello che porta alla riduzione delle prestazioni del modello. È uno dei motivi principali per cui l'accuratezza del modello si riduce nel tempo, quindi il monitoraggio della deriva dei dati aiuta a rilevare i problemi di prestazioni del modello. 

## <a name="what-can-i-monitor"></a>Cosa posso monitorare?

Con Azure Machine Learning è possibile monitorare gli input in un modello distribuito in AKS e confrontare questi dati con il set di dati di training per il modello. A intervalli regolari, i dati di inferenza vengono [snapshot e profilati](https://docs.microsoft.com/python/api/azureml-core/azureml.core.dataset.dataset?view=azure-ml-py), quindi calcolati in base al set di dati di base per produrre un'analisi di deriva dei dati che: 

+ Misura l'entità della deriva dei dati, chiamato coefficiente di deriva.
+ Misura il contributo alla deriva dei dati per feature, indicando quali caratteristiche hanno causato la deriva dei dati.
+ Misura le metriche a distanza. Attualmente vengono calcolate Wasserstein e Energy Distance.
+ Misura le distribuzioni delle funzioni. Stima della densità del kernel e istogrammi.
+ Inviare avvisi alla deriva dei dati via e-mail.

> [!Note]
> Questo servizio è in (anteprima) e limitato nelle opzioni di configurazione. Per informazioni dettagliate e aggiornamenti, consulta la documentazione e [le note sulla versione](azure-machine-learning-release-notes.md) dell'API. [API Documentation](https://docs.microsoft.com/python/api/azureml-datadrift/) 

### <a name="how-data-drift-is-monitored-in-azure-machine-learning"></a>Modalità di monitoraggio della deriva dei dati in Azure Machine LearningHow data drift is monitored in Azure Machine Learning

Con Azure Machine Learning, la deriva dei dati viene monitorata tramite set di dati o distribuzioni. Per monitorare la deriva dei dati, viene specificato un set di dati di base, in genere il set di dati di training per un modello. Un secondo set di dati, in genere modellare i dati di input raccolti da una distribuzione, viene testato rispetto al set di dati di base. Entrambi i set di dati sono profilati e vengono immessi nel servizio di monitoraggio della deriva dei dati. Un modello di apprendimento automatico viene sottoposto a training per rilevare le differenze tra i due set di dati. Le prestazioni del modello vengono convertite nel coefficiente di deriva, che misura l'entità della deriva tra i due set di dati. Utilizzando [l'interpretabilità](how-to-machine-learning-interpretability.md)del modello, vengono calcolate le feature che contribuiscono al coefficiente di deriva. Dal profilo del set di dati, vengono registrate le informazioni statistiche su ogni feature. 

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non ne hai uno, crea un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- Installazione di Azure Machine Learning SDK per Python. Usare le istruzioni riportate in [Azure Machine Learning SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) per eseguire queste operazioni:

    - Creare un ambiente Miniconda
    - Installare Azure Machine Learning SDK per Python

- Area di lavoro di [Azure Machine Learning](how-to-manage-workspace.md).

- Un [file di configurazione](how-to-configure-environment.md#workspace)dell'area di lavoro .

- Installare l'SDK di deriva dei dati utilizzando il comando seguente:

    ```shell
    pip install azureml-datadrift
    ```

- Creare un [set di dati](how-to-create-register-datasets.md) dai dati di training del modello.

- Specificare il set di dati di training durante la [registrazione](concept-model-management-and-deployment.md) del modello. Nell'esempio seguente `datasets` viene illustrato l'utilizzo del parametro per specificare il set di dati di training:The following example demonstrates using the parameter to specify the training dataset:

    ```python
    model = Model.register(model_path=model_file,
                        model_name=model_name,
                        workspace=ws,
                        datasets=datasets)

    print(model_name, image_name, service_name, model)
    ```

- [Abilitare](how-to-enable-data-collection.md) la raccolta dei dati del modello per raccogliere dati dalla `modeldata` distribuzione AKS del modello e verificare che i dati vengano raccolti nel contenitore BLOB.

## <a name="configure-data-drift"></a>Configurare la deriva dei dati
Per configurare la deriva dei dati per l'esperimento, importa le dipendenze come illustrato nell'esempio Python seguente. 

In questo esempio [`DataDriftDetector`](/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector) viene illustrata la configurazione dell'oggetto:

```python
# Import Azure ML packages
from azureml.core import Experiment, Run, RunDetails
from azureml.datadrift import DataDriftDetector, AlertConfiguration

# if email address is specified, setup AlertConfiguration
alert_config = AlertConfiguration('your_email@contoso.com')

# create a new DataDriftDetector object
datadrift = DataDriftDetector.create(ws, model.name, model.version, services, frequency="Day", alert_config=alert_config)
    
print('Details of Datadrift Object:\n{}'.format(datadrift))
```

## <a name="submit-a-datadriftdetector-run"></a>Inviare un'esecuzione di DataDriftDetectorSubmit a DataDriftDetector run

Con `DataDriftDetector` l'oggetto configurato, è possibile inviare una deriva dei [dati in](https://docs.microsoft.com/python/api/azureml-datadrift/azureml.datadrift.datadriftdetector.datadriftdetector#run-target-date--services-none--compute-target-none--create-compute-target-false--feature-list-none--drift-threshold-none-) una data specificata per il modello. Come parte dell'esecuzione, abilitare gli avvisi `drift_threshold` DataDriftDetector impostando il parametro. Se il [datadrift_coefficient](#visualize-drift-metrics) è `drift_threshold`al di sopra di quello specificato, viene inviato un messaggio di posta elettronica.

```python
# adhoc run today
target_date = datetime.today()

# create a new compute - creates datadrift-server
run = datadrift.run(target_date, services, feature_list=feature_list, create_compute_target=True)

# or specify existing compute cluster
run = datadrift.run(target_date, services, feature_list=feature_list, compute_target='cpu-cluster')

# show details of the data drift run
exp = Experiment(ws, datadrift._id)
dd_run = Run(experiment=exp, run_id=run.id)
RunDetails(dd_run).show()
```

## <a name="visualize-drift-metrics"></a>Visualizzare le metriche di deriva

<a name="metrics"></a>

Dopo aver inviato l'esecuzione di DataDriftDetector, è possibile visualizzare le metriche di deriva salvate in ogni iterazione di esecuzione per un'attività di deriva dei dati:After you submit your DataDriftDetector run, you are able to see the drift metrics that are saved in each run iteration for a data drift task:


|Metrica|Descrizione|
--|--|
wasserstein_distance|Distanza statistica definita per la distribuzione numerica unidimensionale.|
energy_distance|Distanza statistica definita per la distribuzione numerica unidimensionale.|
datadrift_coefficient|Calcolato in modo simile al coefficiente di correlazione di Matthew, ma questo output è un numero reale compreso tra 0 e 1. Nel contesto della deriva, 0 indica nessuna deriva e 1 indica la deriva massima.|
datadrift_contribution|Caratteristica importante delle caratteristiche che contribuiscono alla deriva.|

Esistono diversi modi per visualizzare le metriche di deriva:There are multiple ways to view drift metrics:

* Utilizzare `RunDetails`il [widget Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py).
* Utilizzare [`get_metrics()`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py#get-metrics-name-none--recursive-false--run-type-none--populate-false-) la funzione `datadrift` su qualsiasi oggetto run.
* Visualizzare le metriche dalla sezione **Modelli** dell'area di lavoro in [Azure Machine Learning Studio.](https://ml.azure.com)

Nell'esempio Python seguente viene illustrato come tracciare le metriche di deriva dei dati rilevanti. È possibile utilizzare le metriche restituite per creare visualizzazioni personalizzate:You can use the returned metrics to build custom visualizations:

```python
# start and end are datetime objects 
drift_metrics = datadrift.get_output(start_time=start, end_time=end)

# Show all data drift result figures, one per service.
# If setting with_details is False (by default), only the data drift magnitude will be shown; if it's True, all details will be shown.
drift_figures = datadrift.show(with_details=True)
```

![Visualizzare la deriva dei dati rilevata da Azure Machine LearningSee data drift detected by Azure Machine Learning](./media/how-to-monitor-data-drift/drift_show.png)


## <a name="schedule-data-drift-scans"></a>Pianificare le scansioni di deriva dei dati 

Quando si abilita il rilevamento della deriva dei dati, un DataDriftDetector viene eseguito alla frequenza pianificata specificata. Se l'datadrift_coefficient raggiunge `drift_threshold`l'oggetto specificato, viene inviato un messaggio di posta elettronica a ogni esecuzione pianificata. 

```python
datadrift.enable_schedule()
datadrift.disable_schedule()
```

La configurazione del rilevatore di deriva dati può essere visualizzata in **Modelli** nella scheda **Dettagli** dell'area di lavoro di Azure Machine [Learning Studio](https://ml.azure.com).

[![Azure Machine Learning studio Data Drift](./media/how-to-monitor-data-drift/drift-config.png)](media/how-to-monitor-data-drift/drift-config-expanded.png)

## <a name="view-results-in-your-azure-machine-learning-studio"></a>Visualizzare i risultati nello studio di Azure Machine LearningView results in your Azure Machine Learning studio

Per visualizzare i risultati nell'area di lavoro in [Azure Machine Learning Studio,](https://ml.azure.com)passare alla pagina del modello. Nella scheda dei dettagli del modello viene visualizzata la configurazione della deriva dei dati. È ora disponibile una scheda **Deriva dati** che consente di visualizzare le metriche di deriva dei dati. 

[![Azure Machine Learning studio Data Drift](./media/how-to-monitor-data-drift/drift-ui.png)](media/how-to-monitor-data-drift/drift-ui-expanded.png)

## <a name="receiving-drift-alerts"></a>Ricezione di avvisi di deriva

Impostando la soglia di avviso del coefficiente di deriva e fornendo un indirizzo di posta elettronica, viene inviato automaticamente un avviso di posta elettronica di [Monitoraggio di Azure](https://docs.microsoft.com/azure/azure-monitor/overview) ogni volta che il coefficiente di deriva è al di sopra della soglia. 

Per configurare avvisi e azioni personalizzati, tutte le metriche di deriva dei dati vengono archiviate nella risorsa Application Insights creata insieme all'area di lavoro di Azure Machine Learning.In order to you to set up custom alerts and actions, all data drift metrics are stored in the [Application Insights](how-to-enable-app-insights.md) resource that was created along with the Azure Machine Learning workspace. È possibile seguire il collegamento nell'avviso di posta elettronica alla query di Application Insights.You can follow the link in the email alert to the Application Insights query.

![Avviso e-mail di deriva dei dati](./media/how-to-monitor-data-drift/drift_email.png)

## <a name="retrain-your-model-after-drift"></a>Riqualificare il modello dopo la deriva

Quando i dati derivano negativamente sulle prestazioni del modello distribuito, è il momento di eseguire nuovamente il training del modello. A tale scopo, procedere come segue.

* Esaminare i dati raccolti e preparare i dati per eseguire il training del nuovo modello.
* Suddividerlo in dati di training/test.
* Eseguire nuovamente il training del modello usando i nuovi dati.
* Valutare le prestazioni del modello appena generato.
* Distribuire un nuovo modello se le prestazioni sono migliori rispetto al modello di produzione.

## <a name="next-steps"></a>Passaggi successivi

* Per un esempio completo di utilizzo della deriva dei dati, vedere il blocco appunti sulla deriva dei dati di [Azure ML.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/monitor-models/data-drift/drift-on-aks.ipynb) Questo blocco appunti Jupyter illustra l'uso di un set di dati aperto di [Azure](https://docs.microsoft.com/azure/open-datasets/overview-what-are-open-datasets) per eseguire il training di un modello per prevedere il meteo, distribuirlo in AKS e monitorare la deriva dei dati. 

* Rilevare la deriva dei dati con [i monitoraggi dei set di dati](how-to-monitor-datasets.md).

* Apprezziamo molto le vostre domande, commenti o suggerimenti mentre i dati si spostano verso la disponibilità generale. Utilizza il pulsante di feedback del prodotto qui sotto! 
