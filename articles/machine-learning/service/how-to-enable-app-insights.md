---
title: Monitorare e raccogliere dati da endpoint di servizio Web Machine Learning
titleSuffix: Azure Machine Learning
description: Monitorare i servizi Web distribuiti con Azure Machine Learning usando applicazione Azure Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 19dba88bf04ee84459ebd9ef0279f125724d7522
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406447"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorare e raccogliere i dati dagli endpoint del servizio Web ML
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come raccogliere e monitorare i modelli distribuiti negli endpoint dei servizi Web in Azure Kubernetes Service (AKS) o nelle istanze di contenitore di Azure (ACI) abilitando applicazione Azure Insights. Oltre a raccogliere i dati di input e la risposta di un endpoint, è possibile monitorare:

* Frequenza delle richieste, tempi di risposta e percentuali di errore
* Tassi di dipendenza, tempi di risposta e percentuali di errore
* Eccezioni

[Altre informazioni su applicazione Azure Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>prerequisiti

* Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree) oggi stesso

* Un'area di lavoro di Azure Machine Learning, una directory locale contenente gli script e Azure Machine Learning SDK per Python installato. Per informazioni su come ottenere questi prerequisiti, vedere [come configurare un ambiente di sviluppo](how-to-configure-environment.md)
* Un modello di training di Machine Learning da distribuire nel servizio Azure Kubernetes o nel servizio Azure Container. Se non si dispone di un, vedere l'esercitazione [Train image classification Model](tutorial-train-models-with-aml.md)

## <a name="web-service-input-and-response-data"></a>Dati di input e risposta del servizio Web

L'input e la risposta al servizio, corrispondenti agli input al modello ML e alla relativa stima, vengono registrati nelle tracce di applicazione Azure Insights sotto il `"model_data_collection"`del messaggio. È possibile eseguire query direttamente in applicazione Azure Insights per accedere a questi dati oppure configurare un' [esportazione continua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) in un account di archiviazione per un periodo di conservazione più lungo o un'ulteriore elaborazione. I dati del modello possono quindi essere usati nel servizio Azure ML per impostare l'assegnazione di etichette, la ripetizione del training, la spiegazione, l'analisi dei dati o altro uso. 

## <a name="use-the-azure-portal-to-configure"></a>Usare il portale di Azure per configurare

È possibile abilitare e disabilitare applicazione Azure Insights nel portale di Azure. 

1. Nella [portale di Azure](https://portal.azure.com)aprire l'area di lavoro

1. Nella scheda **distribuzioni** selezionare il servizio in cui si vuole abilitare applicazione Azure Insights

   [![Elenco dei servizi nella scheda Distribuzioni](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Selezionare **modifica**

   [![Pulsante Modifica](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. In **Impostazioni avanzate**selezionare la casella di controllo **Abilita diagnostica AppInsights**

   [![Casella di controllo selezionata per l'abilitazione della diagnostica](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Per applicare le modifiche, selezionare **Aggiorna** nella parte inferiore della schermata

### <a name="disable"></a>Disabilitazione

1. Nella [portale di Azure](https://portal.azure.com)aprire l'area di lavoro
1. Selezionare **distribuzioni**, selezionare il servizio, quindi fare clic su **modifica** .

   [![Usare il pulsante di modifica](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. In **Impostazioni avanzate**deselezionare la casella di controllo **Abilita diagnostica AppInsights**

   [![Casella di controllo deselezionata per l'abilitazione della diagnostica](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Per applicare le modifiche, selezionare **Aggiorna** nella parte inferiore della schermata
 
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

3. Compilare un'immagine e distribuirla in [AKS](how-to-deploy-to-aks.md) o [ACI](how-to-deploy-to-aci.md)

### <a name="disable-tracking-in-python"></a>Disabilitare il rilevamento in Python

Per disabilitare applicazione Azure Insights, usare il codice seguente:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```

## <a name="evaluate-data"></a>Valutare i dati
I dati del servizio vengono archiviati nell'account di applicazione Azure Insights, all'interno dello stesso gruppo di risorse Azure Machine Learning.
Per visualizzarli:

1. Passare all'area di lavoro del servizio Machine Learning in [Azure Machine Learning Studio](https://ml.azure.com) e fare clic sul collegamento Application Insights

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Selezionare la scheda **Panoramica** per visualizzare un set di metriche di base per il servizio

   [![Panoramica](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Per esaminare i payload di input e risposta del servizio Web, selezionare **Analytics**
1. Nella sezione Schema selezionare **TRACES** e filtrare le tracce con il messaggio `"model_data_collection"`. Nelle dimensioni personalizzate è possibile visualizzare gli input, le stime e altri dettagli rilevanti

   [dati del modello di ![](media/how-to-enable-app-insights/model-data-trace.png)](./media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Per esaminare le tracce personalizzate, selezionare **Analytics**
4. Nella sezione dello schema selezionare **Tracce**. Quindi selezionare **Esegui** per eseguire la query. I dati devono essere visualizzati in formato tabella ed essere mappati alle chiamate personalizzate nel file di assegnazione dei punteggi

   [![Tracce personalizzate](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Per altre informazioni su come usare applicazione Azure Insights, vedere [che cos'è Application Insights?](../../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Esportare i dati per un'ulteriore elaborazione e una conservazione più lunga

È possibile usare l' [esportazione continua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) di applicazione Azure Insights per inviare messaggi a un account di archiviazione supportato, in cui è possibile impostare un periodo di conservazione più lungo. I messaggi di `"model_data_collection"` vengono archiviati in formato JSON e possono essere facilmente analizzati per estrarre i dati del modello. Azure Data Factory, le pipeline di Azure ML o altri strumenti di elaborazione dati possono essere usati per trasformare i dati in base alle esigenze. Dopo aver trasformato i dati, è possibile registrarli con l'area di lavoro Azure Machine Learning come set di dati. A tale scopo, vedere [How to create and register DataSets](how-to-create-register-datasets.md).

   [![esportazione continua](media/how-to-enable-app-insights/continuous-export-setup.png)](./media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Notebook di esempio

Il notebook [Enable-App-Insights-in-Production-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) illustra i concetti descritti in questo articolo. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* Vedere [come distribuire un modello in un cluster di servizi Kubernetes di Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-kubernetes-service) o [come distribuire un modello in istanze di contenitore di Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-container-instance) per distribuire i modelli negli endpoint del servizio Web e abilitare applicazione Azure Insights per sfruttare la raccolta dei dati e il monitoraggio degli endpoint
* Per altre informazioni sull'uso dei dati raccolti dai modelli nell'ambiente di produzione [, vedere MLOps: gestire, distribuire e monitorare i modelli con Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/concept-model-management-and-deployment) . Questi dati possono contribuire a migliorare continuamente il processo di Machine Learning
