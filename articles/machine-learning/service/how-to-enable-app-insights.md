---
title: Abilitare Application Insights per il servizio Azure Machine Learning in un ambiente di produzione
description: Informazioni su come configurare Application Insights per un servizio Azure Machine Learning distribuito in un servizio Kubernetes di Azure
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 10/01/2018
ms.openlocfilehash: 45871ab515c7ffd9520b1d77d3fd1e77abcc29ef
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49114567"
---
# <a name="monitor-your-azure-machine-learning-models-in-production-with-application-insights"></a>Monitorare i modelli di Azure Machine Learning in un ambiente di produzione con Application Insights

In questo articolo viene illustrato come configurare **Application Insights** per il servizio **Azure Machine Learning**. Dopo essere stato abilitato, Application Insights offre la possibilità di monitorare:
* Frequenza delle richieste, tempi di risposta e percentuali di errore
* Tassi di dipendenza, tempi di risposta e percentuali di errore
* Eccezioni

Altre informazioni su Application Insights sono disponibili [qui](../../application-insights/app-insights-overview.md). 

## <a name="prerequisites"></a>Prerequisiti
* Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.
* Un'area di lavoro di Azure Machine Learning, una directory locale contenente gli script e Azure Machine Learning SDK per Python installato. Informazioni su come ottenere questi prerequisiti usando il documento [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).
* Un modello di training di Machine Learning da distribuire nel servizio Kubernetes di Azure (AKS). Se non si dispone di un modello, vedere l'esercitazione su come [eseguire il training del modello di classificazione delle immagini](tutorial-train-models-with-aml.md).
* Un [cluster AKS](how-to-deploy-to-aks.md).

## <a name="enable--disable-in-the-portal"></a>Abilitare e disabilitare nel portale

È possibile abilitare e disabilitare Application Insights nel portale di Azure.

### <a name="enable"></a>Abilitare

1. Aprire l'area di lavoro nel [portale di Azure](https://portal.azure.com).

1. Passare alle distribuzioni correnti e selezionare il servizio in cui si vuole abilitare Application Insights.

   [![Distribuzioni](media/how-to-enable-app-insights/Deployments.PNG)](./media/how-to-enable-app-insights/Deployments.PNG#lightbox)

3. Fare clic su **Modifica** e passare a **Impostazioni avanzate**.

   [![Modifica](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

4. In **Impostazioni avanzate** selezionare **Abilita diagnostica di AppInsights**.

   [![Modifica](media/how-to-enable-app-insights/AdvancedSettings.png)](./media/how-to-enable-app-insights/AdvancedSettings.png#lightbox)

1. Selezionare **Aggiorna** nella parte inferiore della schermata per applicare le modifiche. 

### <a name="disable"></a>Disabilitazione
Per disabilitare Application Insights nel portale di Azure, seguire questa procedura:

1. Accedere al portale di Azure all'indirizzo https://portal.azure.com.
1. Passa all'area di lavoro.
1. Scegliere **Distribuzioni**, quindi **Seleziona servizio** e infine **Modifica**.

   [![Modifica](media/how-to-enable-app-insights/Edit.PNG)](./media/how-to-enable-app-insights/Edit.PNG#lightbox)

1. In **Impostazioni avanzate** deselezionare l'opzione **Abilita diagnostica di AppInsights**. 

   [![Deseleziona](media/how-to-enable-app-insights/uncheck.png)](./media/how-to-enable-app-insights/uncheck.png#lightbox)

1. Selezionare **Aggiorna** nella parte inferiore della schermata per applicare le modifiche. 

## <a name="enable--disable-from-the-sdk"></a>Abilitare e disabilitare dall'SDK

### <a name="update-a-deployed-service"></a>Aggiornare un servizio distribuito
1. Identificare il servizio nell'area di lavoro (ws = nome dell'area di lavoro)

    ```python
    aks_service= Webservice(ws, "my-service-name")
    ```
2. Aggiornare il servizio e abilitare Application Insights. 

    ```python
    aks_service.update(enable_app_insights=True)
    ```

### <a name="log-custom-traces-in-your-service"></a>Registrare tracce personalizzate nel servizio
Se si vuole registrare tracce personalizzate, seguire il [processo di distribuzione standard per AKS](how-to-deploy-to-aks.md) per eseguire le operazioni seguenti:

1. Aggiornare il file di assegnazione dei punteggi tramite l'aggiunta di istruzioni di stampa.
    
    ```python
    print ("model initialized" + time.strftime("%H:%M:%S"))
    ```

2. Aggiornare la configurazione del servizio Kubernetes di Azure.
    
    ```python
    aks_config = AksWebservice.deploy_configuration(enable_app_insights=True)
    ```

3. [Compilare l'immagine e distribuirla.](how-to-deploy-to-aks.md)  

### <a name="disable-tracking-in-python"></a>Disabilitare il rilevamento in Python

Per disabilitare Application Insights, usare il codice seguente:

```python 
## replace <service_name> with the name of the web service
<service_name>.update(enable_app_insights=False)
```
    

## <a name="evaluate-data"></a>Valutare i dati
I dati del servizio vengono archiviati nell'account di Application Insights nello stesso gruppo di risorse in cui si trova l'area di lavoro del servizio Azure Machine Learning.
Per visualizzarli:
1. Accedere al gruppo di risorse nel [portale di Azure](https://portal.azure.com) e fare clic sulla risorsa di Application Insights. 
2. Nella scheda **Panoramica** viene visualizzato il set di base di metriche per il servizio.

   [![Panoramica](media/how-to-enable-app-insights/overview.png)](./media/how-to-enable-app-insights/overview.png#lightbox)

3. Per esaminare le tracce personalizzate, fare clic su **Analisi**.
4. Nella sezione dello schema fare clic su **tracce** e quindi **eseguire** la query. I dati vengono visualizzati in formato tabella e ne viene eseguito il mapping alle chiamate personalizzate nel file di assegnazione dei punteggi. 

   [![Tracce personalizzate](media/how-to-enable-app-insights/logs.png)](./media/how-to-enable-app-insights/logs.png#lightbox)

Fare clic [qui](../../application-insights/app-insights-overview.md) per altre informazioni su come usare Application Insights.
    

## <a name="example-notebook"></a>Notebook di esempio

Il notebook [00.Getting Started/13.enable-app-insights-in-production-service.ipynb](https://github.com/Azure/MachineLearningNotebooks/tree/master/01.getting-started/13.enable-app-insights) illustra i concetti descritti in questo articolo.  Per ottenere questo blocco appunti:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi
Nell'ambiente di produzione è possibile anche raccogliere i dati sui modelli. Leggere l'articolo [Raccogliere i dati per i modelli nell'ambiente di produzione](how-to-enable-data-collection.md) 
