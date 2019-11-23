---
title: Raccogliere i dati per i modelli di produzione
titleSuffix: Azure Machine Learning
description: Informazioni su come raccogliere i dati del modello di input di Azure Machine Learning in un archivio BLOB di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 18b92fe090895c3aa08c3c931dfa8bd12db0f2d3
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406461"
---
# <a name="collect-data-for-models-in-production"></a>Raccogliere i dati per i modelli nell'ambiente di produzione
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> This SDK is retiring soon. This SDK is still appropriate for developers monitoring data drift in models but most developers should use the simplified [data monitoring with Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights). 

In this article, you can learn how to collect input model data from Azure Machine Learning you've deployed into Azure Kubernetes Cluster (AKS) into an Azure Blob storage. 

Una volta abilitata, la raccolta dei dati consente di:
* [Monitor data drifts](how-to-monitor-data-drift.md) as production data enters your model

* Prendere decisioni migliori sui casi in cui ripetere il training o ottimizzare il modello

* Ripetere il training del modello con i dati raccolti

## <a name="what-is-collected-and-where-does-it-go"></a>Quali informazioni vengono raccolte e dove sono trasferite?

Possono essere raccolti i dati seguenti:
* Dati di **input** del modello dai servizi Web distribuiti nel cluster Kubernetes di Azure (audio, immagini e video **non** vengono raccolti) 
  
* Model predictions using production input data

> [!Note]
> La pre-aggregazione o il pre-calcolo su tali dati al momento non fanno parte del servizio.   

L'output viene salvato in un BLOB di Azure. Poiché i dati vengono aggiunti a un BLOB di Azure, è possibile scegliere lo strumento che si preferisce per eseguire l'analisi. 

La sintassi per il percorso dei dati di output nel BLOB è la seguente:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> In versions of the SDK prior to `0.1.0a16` the `designation` argument was named `identifier`. If your code was developed with an earlier version, you will need to update accordingly.

## <a name="prerequisites"></a>Prerequisiti

- Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree) oggi stesso

- Un'area di lavoro di Azure Machine Learning, una directory locale contenente gli script e Azure Machine Learning SDK per Python installato. Learn how to get these prerequisites using the [How to configure a development environment](how-to-configure-environment.md) document

- Un modello di training di Machine Learning da distribuire nel servizio Azure Kubernetes. If you don't have one, see the [train image classification model](tutorial-train-models-with-aml.md) tutorial

- Un cluster del servizio Azure Kubernetes. For information on how to create and deploy to one, see the [How to deploy and where](how-to-deploy-and-where.md) document

- [Set up your environment](how-to-configure-environment.md) and install the [Monitoring SDK](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>Abilitare la raccolta di dati
Data collection can be enabled regardless of the model being deployed through Azure Machine Learning or other tools. 

Per abilitarla, è necessario:

1. Open the scoring file

1. Aggiungere il [codice seguente](https://aka.ms/aml-monitoring-sdk) all'inizio del file:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Dichiarare le variabili per la raccolta dei dati nella funzione `init()`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* è un parametro facoltativo. Non è necessario configurarlo se il modello non lo richiede. La presenza di un elemento correlationId semplifica il mapping con altri dati. Alcuni esempi sono: LoanNumber, CustomerId e così via.
    
    *Identifier* is later used for building the folder structure in your Blob, it can be used to divide “raw” data versus “processed”

3.  Aggiungere le righe di codice seguenti alla funzione `run(input_df)`:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

4. La raccolta dei dati **non** è automaticamente impostata su **true** quando si distribuisce un servizio in servizio Azure Kubernetes, pertanto è necessario aggiornare il file di configurazione, ad esempio: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    È anche possibile attivare AppInsights per il monitoraggio del servizio modificando questa configurazione:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. To create a new image and deploy the service, see the [How to deploy and where](how-to-deploy-and-where.md) document


Se si dispone già di un servizio con le dipendenze installate nel **file di ambiente** e nel **file di assegnazione dei punteggi**, abilitare la raccolta di dati nel modo seguente:

1. Go to [Azure Machine Learning studio](https://ml.azure.com)

1. Open your workspace

1. Go to **Deployments** -> **Select service** -> **Edit**

   ![Modificare il servizio](media/how-to-enable-data-collection/EditService.PNG)

1. In **Advanced Settings**, select **Enable Model data collection**

    [![Deselezionare la raccolta dati](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   In this window, you can also choose to "Enable Appinsights diagnostics" to track the health of your service

1. Select **Update** to apply the change


## <a name="disable-data-collection"></a>Disabilitare la raccolta dei dati
È possibile arrestare la raccolta dei dati in qualsiasi momento. Use Python code or Azure Machine Learning studio to disable data collection.

+ Option 1 - Disable in Azure Machine Learning studio: 
  1. Sign in to [Azure Machine Learning studio](https://ml.azure.com)

  1. Open your workspace

  1. Go to **Deployments** -> **Select service** -> **Edit**

     [![Opzione di modifica](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. In **Advanced Settings**, deselect **Enable Model data collection**

     [![Deselezionare la raccolta dati](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Select **Update** to apply the change

  You can also access these settings in your workspace in [Azure Machine Learning studio](https://ml.azure.com).

+ Opzione 2: uso di Python per disabilitare la raccolta dei dati:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Convalidare i dati e analizzarli
È possibile scegliere qualsiasi strumento per analizzare i dati raccolti nel BLOB di Azure.

Per accedere rapidamente ai dati dal BLOB:

1. Sign in to [Azure Machine Learning studio](https://ml.azure.com)

1. Open your workspace
1. Click on **Storage**

    [![Archiviazione](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Seguire il percorso dei dati di output nel BLOB con questa sintassi:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analisi dei dati del modello tramite Power BI

1. Download and Open [Power BI Desktop](https://www.powerbi.com)

1. Select **Get Data** and click on [**Azure Blob Storage**](https://docs.microsoft.com/power-bi/desktop-data-sources)

    [![Configurazione di BLOB per Power BI](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Aggiungere il nome dell'account di archiviazione e immettere la chiave di archiviazione. You can find this information in your blob's **Settings** >> Access keys

1. Select the container **modeldata** and click on **Edit**

    [![Strumento di navigazione Power BI](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Nell'editor di query fare clic sotto la colonna "Nome" e aggiungere l'account di archiviazione 1. Modellare il percorso nel filtro. Nota: se si vogliono esaminare solo i file da un anno o un mese specifico, espandere semplicemente il percorso di filtro. For example, just look into March data: /modeldata/subscriptionid>/resourcegroupname>/workspacename>/webservicename>/modelname>/modelversion>/designation>/year>/3

1. Filtrare i dati di interesse in base a **Nome**. If you stored **predictions** and **inputs**, you'll need to create a query for each

1. Click on the double arrow aside the **Content** column to combine the files

    [![Contenuto di Power BI](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Click OK and the data will preload

    [![Combinare file in Power BI](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. You can now click **Close and Apply**

1.  If you added inputs and predictions, your tables will automatically correlate by **RequestId**

1. Start building your custom reports on your model data


### <a name="analyzing-model-data-using-databricks"></a>Analisi dei dati del modello con Databricks

1. Create a [Databricks workspace](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

1. Go to your Databricks workspace

1. In your databricks workspace, select **Upload Data**

    [![Caricamento dei dati](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Create New Table and select **Other Data Sources** -> Azure Blob Storage -> Create Table in Notebook

    [![Tabella di database](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Aggiornare il percorso dei dati. Di seguito è fornito un esempio:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![Configurazione database](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Follow the steps on the template in order to view and analyze your data

## <a name="example-notebook"></a>Notebook di esempio

Il notebook [how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) illustra i concetti descritti in questo articolo.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
