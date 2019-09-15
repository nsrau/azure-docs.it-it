---
title: Configurare applicazione Azure Insights per monitorare i modelli di ML
titleSuffix: Azure Machine Learning
description: Monitorare i servizi Web distribuiti con Azure Machine Learning usando applicazione Azure Insights
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 07/12/2019
ms.custom: seoapril2019
ms.openlocfilehash: 785507e9ae12d8da564a223c8cdf544a98b8de61
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002875"
---
# <a name="monitor-your-azure-machine-learning-models-with-application-insights"></a>Monitorare i modelli di Azure Machine Learning con Application Insights

Questo articolo illustra come configurare applicazione Azure Insights per la Azure Machine Learning. Application Insights offre la possibilità di monitorare:
* Frequenza delle richieste, tempi di risposta e percentuali di errore.
* Tassi di dipendenza, tempi di risposta e percentuali di errore.
* eccezioni,

Altre informazioni su [Application Insights](../../azure-monitor/app/app-insights-overview.md). 


## <a name="prerequisites"></a>Prerequisiti

* Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Prova subito la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree) .

* Un'area di lavoro di Azure Machine Learning, una directory locale contenente gli script e Azure Machine Learning SDK per Python installato. Per informazioni su come ottenere questi prerequisiti, vedere [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).
* Un modello di training di Machine Learning da distribuire nel servizio Azure Kubernetes o nel servizio Azure Container. Se non si dispone di un modello, vedere l'esercitazione su come [eseguire il training del modello di classificazione delle immagini](tutorial-train-models-with-aml.md).


## <a name="use-sdk-to-configure"></a>Usare SDK per configurare 

### <a name="update-a-deployed-service"></a>Aggiornare un servizio distribuito
1. Identificare il servizio nell'area di lavoro. Il valore per `ws` è il nome dell'area di lavoro.

    ```python
    from azureml.core.webservice import Webservice
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aggiornare il servizio e abilitare Application Insights. 

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

Per disabilitare Application Insights, usare il codice seguente:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    
## <a name="use-portal-to-configure"></a>Usare il portale per configurare

È possibile abilitare e disabilitare Application Insights nel portale di Azure.

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com).

1. Nella scheda **Distribuzioni** selezionare il servizio in cui si vuole abilitare Application Insights.

   [![Elenco dei servizi nella scheda Distribuzioni](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Selezionare **Modifica**.

   [![Pulsante Modifica](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. In **Impostazioni avanzate** selezionare la casella di controllo **Abilita diagnostica di AppInsights**.

   [![Casella di controllo selezionata per l'abilitazione della diagnostica](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Selezionare **Aggiorna** nella parte inferiore della schermata per applicare le modifiche. 

### <a name="disable"></a>Disattiva
1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com).
1. Selezionare **Distribuzioni**, il servizio e quindi **Modificare**.

   [![Usare il pulsante di modifica](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. In **Impostazioni avanzate** deselezionare la casella di controllo **Abilita diagnostica di AppInsights**. 

   [![Casella di controllo deselezionata per l'abilitazione della diagnostica](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Selezionare **Aggiorna** nella parte inferiore della schermata per applicare le modifiche. 
 

## <a name="evaluate-data"></a>Valutare i dati
I dati del servizio vengono archiviati nell'account di Application Insights, all'interno dello stesso gruppo di risorse Azure Machine Learning.
Per visualizzarli:
1. Passare all'area di lavoro del servizio Machine Learning nel [portale di Azure](https://portal.azure.com) e fare clic sul collegamento di Application Insights.

    [![AppInsightsLoc](media/how-to-enable-app-insights/AppInsightsLoc.png)](./media/how-to-enable-app-insights/AppInsightsLoc.png#lightbox)

1. Selezionare la scheda **Panoramica** per visualizzare il set di base di metriche per il servizio.

   [![Panoramica](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Per esaminare le tracce personalizzate, selezionare **Analisi**.
4. Nella sezione dello schema selezionare **Tracce**. Quindi selezionare **Esegui** per eseguire la query. I dati vengono visualizzati in formato tabella e ne viene eseguito il mapping alle chiamate personalizzate nel file di assegnazione dei punteggi. 

   [![Tracce personalizzate](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Per altre informazioni su Application Insights, vedere [Che cos'è Application Insights?](../../azure-monitor/app/app-insights-overview.md).


## <a name="example-notebook"></a>Notebook di esempio

Il notebook [Enable-App-Insights-in-Production-Service. ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-app-insights-in-production-service/enable-app-insights-in-production-service.ipynb) illustra i concetti descritti in questo articolo. 
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi
Nell'ambiente di produzione è possibile anche raccogliere i dati sui modelli. Leggere l'articolo [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md). 

Leggere anche [monitoraggio di Azure per i contenitori](https://docs.microsoft.com/azure/monitoring/monitoring-container-insights-overview?toc=%2fazure%2fmonitoring%2ftoc.json).
