---
title: Monitorare e raccogliere dati da endpoint di servizio Web Machine Learning
titleSuffix: Azure Machine Learning
description: Monitorare i servizi Web distribuiti con Azure Machine Learning usando applicazione Azure Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: larryfr
author: blackmist
ms.date: 03/12/2020
ms.openlocfilehash: 464ec1fcf0986dc04bd92bbe9e31b5675e5822d4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79136194"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorare e raccogliere dati da endpoint servizio Web di ML
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come raccogliere e monitorare i modelli distribuiti negli endpoint dei servizi Web in Azure Kubernetes Service (AKS) o nelle istanze di contenitore di Azure (ACI) abilitando applicazione Azure Insights tramite 
* [SDK Azure Machine Learning Python](#python)
* [Azure Machine Learning Studio](#studio) allehttps://ml.azure.com

Oltre a raccogliere i dati di output e la risposta di un endpoint, è possibile monitorare:

* Frequenza delle richieste, tempi di risposta e percentuali di errore
* Tassi di dipendenza, tempi di risposta e percentuali di errore
* Eccezioni

[Altre informazioni su applicazione Azure Insights](../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Prerequisiti

* Se non si ha una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Prova subito la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree)

* Un'area di lavoro di Azure Machine Learning, una directory locale contenente gli script e Azure Machine Learning SDK per Python installato. Per informazioni su come ottenere questi prerequisiti, vedere [come configurare un ambiente di sviluppo](how-to-configure-environment.md)

* Un modello di training di Machine Learning da distribuire nel servizio Azure Kubernetes o nel servizio Azure Container. Se non si dispone di un, vedere l'esercitazione [Train image classification Model](tutorial-train-models-with-aml.md)

## <a name="web-service-metadata-and-response-data"></a>Metadati del servizio Web e dati di risposta

>[!Important]
> Applicazione Azure Insights registra solo i payload fino a 64KB. Se viene raggiunto questo limite, vengono registrati solo gli output più recenti del modello. 

I metadati e la risposta al servizio, corrispondenti ai metadati del servizio Web e alle stime del modello, vengono registrati nelle tracce di applicazione Azure Insights sotto il messaggio `"model_data_collection"`. È possibile eseguire query direttamente in applicazione Azure Insights per accedere a questi dati oppure configurare un' [esportazione continua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) in un account di archiviazione per un periodo di conservazione più lungo o un'ulteriore elaborazione. È quindi possibile usare i dati del modello nella Azure Machine Learning per configurare l'assegnazione di etichette, la ripetizione del training, la spiegazione, l'analisi dei dati o altro uso. 

<a name="python"></a>

## <a name="use-python-sdk-to-configure"></a>Usare Python SDK per configurare 

### <a name="update-a-deployed-service"></a>Aggiornare un servizio distribuito

1. Identificare il servizio nell'area di lavoro. Il valore per `ws` è il nome dell'area di lavoro

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aggiornare il servizio e abilitare applicazione Azure Insights

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Registrare tracce personalizzate nel servizio

Per registrare tracce personalizzate, seguire il processo di distribuzione standard per il servizio Azure Kubernetes o il servizio Istanze di Azure Container, descritto nel documento [Distribuire modelli con il servizio di Azure Machine Learning](how-to-deploy-and-where.md). Seguire quindi questa procedura:

1. Aggiornare il file di assegnazione dei punteggi aggiungendo istruzioni Print
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aggiornare la configurazione del servizio
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Compilare un'immagine e distribuirla in [Azure Kubernetes o Azure Container](how-to-deploy-and-where.md).

### <a name="disable-tracking-in-python"></a>Disabilitare il rilevamento in Python

Per disabilitare applicazione Azure Insights, usare il codice seguente:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

<a name="studio"></a>

## <a name="use-azure-machine-learning-studio-to-configure"></a>Usare Azure Machine Learning Studio per la configurazione

È anche possibile abilitare applicazione Azure Insights da Azure Machine Learning Studio quando si è pronti per distribuire il modello con questa procedura.

1. Accedere all'area di lavoro all'indirizzohttps://ml.azure.com/
1. Passare a **modelli** e selezionare il modello che si desidera distribuire
1. Selezionare **+ Distribuisci**
1. Popolare il modulo **Distribuisci modello**
1. Espandere il menu **Avanzate**

    ![Modulo di distribuzione](./media/how-to-enable-app-insights/deploy-form.png)
1. Selezionare **Enable Application Insights Diagnostics and Data Collection**

    ![Abilita App Insights](./media/how-to-enable-app-insights/enable-app-insights.png)
## <a name="evaluate-data"></a>Valutare i dati
I dati del servizio vengono archiviati nell'account di applicazione Azure Insights, all'interno dello stesso gruppo di risorse Azure Machine Learning.
Per visualizzarli:

1. Passare all'area di lavoro Azure Machine Learning nel [portale di Azure](https://ms.portal.azure.com/) e fare clic sul collegamento Application Insights

    [![AppInsightsLoc](./media/how-to-enable-app-insights/AppInsightsLoc.png)](././media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Selezionare la scheda **Panoramica** per visualizzare un set di metriche di base per il servizio

   [![Panoramica](./media/how-to-enable-app-insights/overview.png)](././media/how-to-enable-app-insights/overview.png#lightbox)

1. Per esaminare i metadati e la risposta della richiesta del servizio Web, selezionare la tabella **requests (richieste** ) nella sezione **logs (Analytics)** e selezionare **Run (Esegui** ) per visualizzare le richieste

   [![Modellare i dati](./media/how-to-enable-app-insights/model-data-trace.png)](././media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Per esaminare le tracce personalizzate, selezionare **Analytics**
4. Nella sezione dello schema selezionare **Tracce**. Quindi selezionare **Esegui** per eseguire la query. I dati devono essere visualizzati in formato tabella ed essere mappati alle chiamate personalizzate nel file di assegnazione dei punteggi

   [![Tracce personalizzate](./media/how-to-enable-app-insights/logs.png)](././media/how-to-enable-app-insights/logs.png#lightbox)

Per altre informazioni su come usare applicazione Azure Insights, vedere [che cos'è Application Insights?](../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Esportare i dati per un'ulteriore elaborazione e una conservazione più lunga

>[!Important]
> Applicazione Azure Insights supporta solo le esportazioni nell'archivio BLOB. I limiti aggiuntivi di questa funzionalità di esportazione sono elencati in esportare i dati di [telemetria da Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry#continuous-export-advanced-storage-configuration).

È possibile usare l' [esportazione continua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) di applicazione Azure Insights per inviare messaggi a un account di archiviazione supportato, in cui è possibile impostare un periodo di conservazione più lungo. I `"model_data_collection"` messaggi vengono archiviati in formato JSON e possono essere facilmente analizzati per estrarre i dati del modello. 

Azure Data Factory, le pipeline di Azure ML o altri strumenti di elaborazione dati possono essere usati per trasformare i dati in base alle esigenze. Dopo aver trasformato i dati, è possibile registrarli con l'area di lavoro Azure Machine Learning come set di dati. A tale scopo, vedere [How to create and register DataSets](how-to-create-register-datasets.md).

   [![Esportazione continua](./media/how-to-enable-app-insights/continuous-export-setup.png)](././media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Notebook di esempio

Il notebook [Enable-App-Insights-in-Production-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) illustra i concetti descritti in questo articolo. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* Vedere [come distribuire un modello in un cluster di servizi Kubernetes di Azure](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-kubernetes-service) o [come distribuire un modello in istanze di contenitore di Azure](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-azure-container-instance) per distribuire i modelli negli endpoint del servizio Web e abilitare applicazione Azure Insights per sfruttare la raccolta dei dati e il monitoraggio degli endpoint
* Per altre informazioni sull'uso dei dati raccolti dai modelli nell'ambiente di produzione [, vedere MLOps: gestire, distribuire e monitorare i modelli con Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-model-management-and-deployment) . Questi dati possono contribuire a migliorare continuamente il processo di Machine Learning
