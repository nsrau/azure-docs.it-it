---
title: Monitorare e raccogliere dati dagli endpoint del servizio Web di Machine LearningMonitor and collect data from Machine Learning web service endpoints
titleSuffix: Azure Machine Learning
description: Monitorare i servizi Web distribuiti con Azure Machine Learning usando Azure Application InsightsMonitor web services deployed with Azure Machine Learning using Azure Application Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 03/12/2020
ms.openlocfilehash: 464ec1fcf0986dc04bd92bbe9e31b5675e5822d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79136194"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorare e raccogliere dati dagli endpoint del servizio Web MLMonitor and collect data from ML web service endpoints
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo viene illustrato come raccogliere dati da e monitorare i modelli distribuiti agli endpoint del servizio Web in Azure Kubernetes Service (AKS) o Azure Container Instances (ACI) abilitando Azure Application Insights tramite 
* [Azure Machine Learning Python SDK](#python)
* [Azure Machine Learning studio](#studio) pressohttps://ml.azure.com

Oltre a raccogliere i dati di output e la risposta di un endpoint, è possibile monitorare:In addition to collecting an endpoint's output data and response, you can monitor:

* Frequenza delle richieste, tempi di risposta e percentuali di errore
* Tassi di dipendenza, tempi di risposta e percentuali di errore
* Eccezioni

[Altre informazioni su Azure Application Insights .Learn more about Azure Application Insights](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Prova subito la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree)

* Un'area di lavoro di Azure Machine Learning, una directory locale contenente gli script e Azure Machine Learning SDK per Python installato. Per informazioni su come ottenere questi prerequisiti, vedere [Come configurare un ambiente](how-to-configure-environment.md) di sviluppoTo learn how to get these prerequisites, see How to configure a development environment

* Un modello di training di Machine Learning da distribuire nel servizio Azure Kubernetes o nel servizio Azure Container. Se non ne hai uno, vedi l'esercitazione Sul modello di [classificazione delle immagini](tutorial-train-models-with-aml.md) del treno

## <a name="web-service-metadata-and-response-data"></a>Metadati del servizio Web e dati di risposta

>[!Important]
> Azure Application Insights registra solo payload fino a 64 kb. Se viene raggiunto questo limite, vengono registrati solo gli output più recenti del modello. 

I metadati e la risposta al servizio, corrispondenti ai metadati del servizio Web e alle `"model_data_collection"`stime del modello, vengono registrati nelle tracce di Azure Application Insights nel messaggio. È possibile eseguire query direttamente su Azure Application Insights per accedere a questi dati o configurare [un'esportazione continua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) in un account di archiviazione per una conservazione più lunga o un'ulteriore elaborazione. I dati del modello possono quindi essere usati in Azure Machine Learning per configurare l'etichettatura, la riqualificazione, la spiegazione, l'analisi dei dati o altri utilizzi. 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Utilizzare Python SDK per configurare 

### <a name="update-a-deployed-service"></a>Aggiornare un servizio distribuito

1. Identificare il servizio nell'area di lavoro. Il valore `ws` per è il nome dell'area di lavoro

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aggiornare il servizio e abilitare Azure Application InsightsUpdate your service and enable Azure Application Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Registrare tracce personalizzate nel servizio

Per registrare tracce personalizzate, seguire il processo di distribuzione standard per il servizio Azure Kubernetes o il servizio Istanze di Azure Container, descritto nel documento [Distribuire modelli con il servizio di Azure Machine Learning](how-to-deploy-and-where.md). Seguire quindi questa procedura:

1. Aggiornare il file di punteggio aggiungendo istruzioni di stampa
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aggiornare la configurazione del servizioUpdate the service configuration
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Compilare un'immagine e distribuirla in [Azure Kubernetes o Azure Container](how-to-deploy-and-where.md).

### <a name="disable-tracking-in-python"></a>Disabilitare il rilevamento in Python

Per disabilitare Azure Application Insights, usare il codice seguente:To disable Azure Application Insights, use the following code:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Usare Azure Machine Learning Studio per configurare

È anche possibile abilitare Azure Application Insights da Azure Machine Learning Studio quando si è pronti a distribuire il modello con questi passaggi.

1. Accedere all'area di lavoro all'ora di lavoro all'orahttps://ml.azure.com/
1. Vai a **Modelli** e seleziona il modello da distribuire
1. Selezionare **Distribuisci**
1. Popolare il modulo **Distribuisci modello**
1. Espandere il menu **Avanzate**

    ![Distribuisci modulo](./media/how-to-enable-app-insights/deploy-form.png)
1. Selezionare **Abilita la diagnostica e** la raccolta dati di Application Insights

    ![Abilitare i dati statistici dell'appEnable App](./media/how-to-enable-app-insights/enable-app-insights.png)
## <a name="evaluate-data"></a>Valutare i dati
Your service's data is stored in your Azure Application Insights account, within the same resource group as Azure Machine Learning.
Per visualizzarli:

1. Passare all'area di lavoro di Azure Machine Learning nel portale di [Azure](https://ms.portal.azure.com/) e fare clic sul collegamento Application Insights

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Selezionare la scheda **Panoramica** per visualizzare un set di metriche di base per il servizio

   [![Panoramica](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. Per esaminare i metadati e la risposta delle richieste del servizio Web, selezionare la tabella **delle richieste** nella sezione **Registri (analisi)** e selezionare **Esegui** per visualizzare le richieste

   [![Modellare i dati](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Per esaminare le tracce personalizzate, seleziona **Analytics**
4. Nella sezione dello schema selezionare **Tracce**. Quindi selezionare **Esegui** per eseguire la query. I dati devono essere visualizzati in formato tabella e mappati alle chiamate personalizzate nel file di punteggio

   [![Tracce personalizzate](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Per altre informazioni su come usare Azure Application Insights, vedere [Che cos'è Application Insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Esportare i dati per un'ulteriore elaborazione e una conservazione più lunga

>[!Important]
> Azure Application Insights supporta solo le esportazioni nell'archiviazione BLOB. Limiti aggiuntivi di questa funzionalità di esportazione sono elencati in [Esportare dati di telemetria da App Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

È possibile usare [l'esportazione continua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) di Azure Application Insights per inviare messaggi a un account di archiviazione supportato, in cui è possibile impostare una conservazione più lunga. I `"model_data_collection"` messaggi vengono archiviati in formato JSON e possono essere facilmente analizzati per estrarre i dati del modello. 

Azure Data Factory, le pipeline di Azure ML o altri strumenti di elaborazione dati possono essere usati per trasformare i dati in base alle esigenze. Dopo aver trasformato i dati, è possibile registrarli nell'area di lavoro di Azure Machine Learning come set di dati. A tale scopo, vedere [Come creare e registrare i dataset](how-to-create-register-datasets.md).

   [![Esportazione continua](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Notebook di esempio

Il blocco appunti [enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) illustra i concetti illustrati in questo articolo. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come distribuire un modello in un cluster del [servizio Azure Kubernetes](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) o [su come distribuire un modello in istanze del contenitore](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) di Azure per distribuire i modelli agli endpoint del servizio Web e abilitare Azure Application Insights per sfruttare la raccolta dei dati e il monitoraggio degli endpoint
* Per altre informazioni su altre informazioni sulla convalida dei dati raccolti dai modelli nell'ambiente di produzione, vedere [MLOps: Manage, deploy, and monitor models with Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) . Tali dati possono contribuire a migliorare continuamente il processo di apprendimento automatico
