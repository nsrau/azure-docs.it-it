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
ms.date: 09/25/2019
ms.custom: seoapril2019
ms.openlocfilehash: 08c891ce2702455cac9618152061e992fc81420b
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2019
ms.locfileid: "72274037"
---
# <a name="monitor-and-collect-data-from-ml-web-service-endpoints"></a>Monitorare e raccogliere i dati dagli endpoint del servizio Web ML

Questo articolo illustra come raccogliere e monitorare i modelli distribuiti negli endpoint dei servizi Web in Azure Kubernetes Service (AKS) o nelle istanze di contenitore di Azure (ACI) abilitando applicazione Azure Insights. Oltre a raccogliere i dati di input e la risposta di un endpoint, è possibile monitorare:
* Frequenza delle richieste, tempi di risposta e percentuali di errore.
* Tassi di dipendenza, tempi di risposta e percentuali di errore.
* eccezioni,

[Altre informazioni su applicazione Azure Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Prerequisiti

* Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* Un'area di lavoro di Azure Machine Learning, una directory locale contenente gli script e Azure Machine Learning SDK per Python installato. Per informazioni su come ottenere questi prerequisiti, vedere [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).
* Un modello di training di Machine Learning da distribuire nel servizio Azure Kubernetes o nel servizio Azure Container. Se non si dispone di un modello, vedere l'esercitazione su come [eseguire il training del modello di classificazione delle immagini](tutorial-train-models-with-aml.md).

## <a name="web-service-input-and-response-data"></a>Dati di input e risposta del servizio Web

L'input e la risposta al servizio, corrispondenti agli input al modello ML e alla relativa stima, vengono registrati nelle tracce di applicazione Azure Insights nel messaggio `"model_data_collection"`. È possibile eseguire query direttamente in applicazione Azure Insights per accedere a questi dati oppure configurare un' [esportazione continua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) in un account di archiviazione per un periodo di conservazione più lungo o un'ulteriore elaborazione. I dati del modello possono quindi essere usati nel servizio Azure ML per impostare l'assegnazione di etichette, la ripetizione del training, la spiegazione, l'analisi dei dati o altro uso. 

## <a name="use-the-azure-portal-to-configure"></a>Usare il portale di Azure per configurare

È possibile abilitare e disabilitare applicazione Azure Insights nel portale di Azure. 

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com).

1. Nella scheda **distribuzioni** selezionare il servizio in cui si vuole abilitare applicazione Azure Insights.

   [![Elenco dei servizi nella scheda Distribuzioni](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Selezionare **Modifica**.

   [![Pulsante Modifica](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. In **Impostazioni avanzate** selezionare la casella di controllo **Abilita diagnostica di AppInsights**.

   [![Casella di controllo selezionata per l'abilitazione della diagnostica](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Selezionare **Aggiorna** nella parte inferiore della schermata per applicare le modifiche. 

### <a name="disable"></a>Disabilitazione
1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com).
1. Selezionare **distribuzioni**, selezionare il servizio, quindi fare clic su **modifica**.

   [![Usare il pulsante di modifica](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. In **Impostazioni avanzate** deselezionare la casella di controllo **Abilita diagnostica di AppInsights**. 

   [![Casella di controllo deselezionata per l'abilitazione della diagnostica](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Selezionare **Aggiorna** nella parte inferiore della schermata per applicare le modifiche. 
 
## <a name="use-python-sdk-to-configure"></a>Usare Python SDK per configurare 

### <a name="update-a-deployed-service"></a>Aggiornare un servizio distribuito
1. Identificare il servizio nell'area di lavoro. Il valore per `ws` è il nome dell'area di lavoro.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aggiornare il servizio e abilitare applicazione Azure Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Registrare tracce personalizzate nel servizio
Per registrare tracce personalizzate, seguire il processo di distribuzione standard per il servizio Azure Kubernetes o il servizio Istanze di Azure Container, descritto nel documento [Distribuire modelli con il servizio di Azure Machine Learning](how-to-deploy-and-where.md). Seguire quindi questa procedura:

1. Aggiornare il file di assegnazione dei punteggi tramite l'aggiunta di istruzioni di stampa.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aggiornare la configurazione del servizio.
    
    ```python
    config = Webservice.deploy_configuration(enable_app_insights=True)
    ```

3. Compilare un'immagine e distribuirla in [Azure Kubernetes](how-to-deploy-to-aks.md) o [Azure Container](how-to-deploy-to-aci.md).  

### <a name="disable-tracking-in-python"></a>Disabilitare il rilevamento in Python

Per disabilitare applicazione Azure Insights, usare il codice seguente:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="evaluate-data"></a>Valutare i dati
I dati del servizio vengono archiviati nell'account di applicazione Azure Insights, all'interno dello stesso gruppo di risorse Azure Machine Learning.
Per visualizzarli:
1. Passare all'area di lavoro del servizio Machine Learning nel [portale di Azure](https://portal.azure.com). Fare clic sul collegamento applicazione Azure Insights.

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Selezionare la scheda **Panoramica** per visualizzare il set di base di metriche per il servizio.

   [![Panoramica](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

1. Per esaminare i payload di input e risposta del servizio Web, selezionare **Analytics**
1. Nella sezione Schema selezionare **tracce** e filtrare le tracce con il messaggio `"model_data_collection"`. Nelle dimensioni personalizzate è possibile visualizzare gli input, le stime e altri dettagli pertinenti.

   [dati @no__t 1Model](media/how-to-enable-app-insights/model-data-trace.png)](./media/how-to-enable-app-insights/model-data-trace.png#lightbox)


3. Per esaminare le tracce personalizzate, selezionare **Analisi**.
4. Nella sezione dello schema selezionare **Tracce**. Quindi selezionare **Esegui** per eseguire la query. I dati vengono visualizzati in formato tabella e ne viene eseguito il mapping alle chiamate personalizzate nel file di assegnazione dei punteggi. 

   [![Tracce personalizzate](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Per altre informazioni su come usare applicazione Azure Insights, vedere [che cos'è Application Insights?](../../azure-monitor/app/app-insights-overview.md).

## <a name="export-data-for-further-processing-and-longer-retention"></a>Esportare i dati per un'ulteriore elaborazione e una conservazione più lunga

È possibile usare l' [esportazione continua](https://docs.microsoft.com/azure/azure-monitor/app/export-telemetry) di applicazione Azure Insights per inviare messaggi a un account di archiviazione supportato, in cui è possibile impostare un periodo di conservazione più lungo. I messaggi `"model_data_collection"` vengono archiviati in formato JSON e possono essere facilmente analizzati per estrarre i dati del modello. Azure Data Factory, le pipeline di Azure ML o altri strumenti di elaborazione dati possono essere usati per trasformare i dati in base alle esigenze. Dopo aver trasformato i dati, è possibile registrarli con l'area di lavoro del servizio Azure Machine Learning come set di dati.

   [Esportazione ![Continuous](media/how-to-enable-app-insights/continuous-export-setup.png)](./media/how-to-enable-app-insights/continuous-export-setup.png)


## <a name="example-notebook"></a>Notebook di esempio

Il notebook [Enable-App-Insights-in-Production-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) illustra i concetti descritti in questo articolo. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi

* Vedere [come distribuire un modello in un cluster di servizi Kubernetes di Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-kubernetes-service) o [come distribuire un modello in istanze di contenitore di Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-azure-container-instance) per distribuire i modelli negli endpoint del servizio Web e abilitare applicazione Azure Insights per sfruttare la raccolta dei dati e l'endpoint monitoraggio.
* Vedere [MLOps: È possibile gestire, distribuire e monitorare i modelli con Azure Machine Learning @ no__t-0 per altre informazioni sull'uso dei dati raccolti dai modelli nell'ambiente di produzione. Tali dati possono contribuire a migliorare continuamente il processo di machine learning. 
