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
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 2ca091a1bbf56e2d2850a464d0109020b06483d0
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73576683"
---
# <a name="collect-data-for-models-in-production"></a>Raccogliere i dati per i modelli nell'ambiente di produzione
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Questo SDK verrà ritirato a breve. Questo SDK è ancora appropriato per gli sviluppatori che monitorano la tendenza dei dati nei modelli, ma la maggior parte degli sviluppatori deve usare il monitoraggio semplificato [dei dati con Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights). 

In questo articolo si apprenderà come raccogliere i dati del modello di input da Azure Machine Learning distribuiti in un cluster Azure Kubernetes (AKS) in un archivio BLOB di Azure. 

Una volta abilitata, la raccolta dei dati consente di:
* [Monitorare le deviazioni dei dati](how-to-monitor-data-drift.md) quando i dati di produzione entrano nel modello

* Prendere decisioni migliori sui casi in cui ripetere il training o ottimizzare il modello

* Ripetere il training del modello con i dati raccolti

## <a name="what-is-collected-and-where-does-it-go"></a>Quali informazioni vengono raccolte e dove sono trasferite?

Possono essere raccolti i dati seguenti:
* Dati di **input** del modello dai servizi Web distribuiti nel cluster Kubernetes di Azure (audio, immagini e video **non** vengono raccolti) 
  
* Previsioni del modello usando i dati di input di produzione.

> [!Note]
> La pre-aggregazione o il pre-calcolo su tali dati al momento non fanno parte del servizio.   

L'output viene salvato in un BLOB di Azure. Poiché i dati vengono aggiunti a un BLOB di Azure, è possibile scegliere lo strumento che si preferisce per eseguire l'analisi. 

La sintassi per il percorso dei dati di output nel BLOB è la seguente:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!Note]
> Nelle versioni di SDK precedenti a `0.1.0a16` l'argomento di `designation` era denominato `identifier`. Se il codice è stato sviluppato con una versione precedente, sarà necessario aggiornarlo di conseguenza.

## <a name="prerequisites"></a>Prerequisiti

- Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

- Un'area di lavoro di Azure Machine Learning, una directory locale contenente gli script e Azure Machine Learning SDK per Python installato. Informazioni su come ottenere questi prerequisiti usando il documento [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).

- Un modello di training di Machine Learning da distribuire nel servizio Azure Kubernetes. Se non si dispone di un modello, vedere l'esercitazione su come [eseguire il training del modello di classificazione delle immagini](tutorial-train-models-with-aml.md).

- Un cluster del servizio Azure Kubernetes. Per informazioni su come creare un cluster di questo tipo e distribuire un modello in tale cluster, vedere il documento [Come e dove distribuire modelli](how-to-deploy-and-where.md).

- [Configurare l'ambiente](how-to-configure-environment.md) e installare [Monitoring SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Abilitare la raccolta di dati
La raccolta dati può essere abilitata indipendentemente dal modello distribuito tramite Azure Machine Learning o altri strumenti. 

Per abilitarla, è necessario:

1. Aprire il file di assegnazione dei punteggi. 

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
    
    *Identifier* viene usato in un secondo momento per creare la struttura di cartelle del BLOB. Può essere usato per dividere i dati "raw" da quelli "elaborati".

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

5. Per creare una nuova immagine e distribuire il servizio, vedere il documento [Come e dove distribuire modelli](how-to-deploy-and-where.md).


Se si dispone già di un servizio con le dipendenze installate nel **file di ambiente** e nel **file di assegnazione dei punteggi**, abilitare la raccolta di dati nel modo seguente:

1. Passare a [Azure Machine Learning Studio](https://ml.azure.com).

1. Aprire l'area di lavoro.

1. Passare a **Distribuzioni** -> **Seleziona servizio** -> **Modifica**.

   ![Modificare il servizio](media/how-to-enable-data-collection/EditService.PNG)

1. In **Impostazioni avanzate**selezionare **Abilita raccolta dati modello**. 

    [![Deselezionare la raccolta dati](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   In questa finestra è anche possibile scegliere "Abilita diagnostica di AppInsights" per tenere traccia dell'integrità del servizio.  

1. Selezionare **Aggiorna** per applicare la modifica.


## <a name="disable-data-collection"></a>Disabilitare la raccolta dei dati
È possibile arrestare la raccolta dei dati in qualsiasi momento. Usare il codice Python o Azure Machine Learning Studio per disabilitare la raccolta dei dati.

+ Opzione 1: disabilitare in Azure Machine Learning Studio: 
  1. Accedere a [Azure Machine Learning Studio](https://ml.azure.com).

  1. Aprire l'area di lavoro.

  1. Passare a **Distribuzioni** -> **Seleziona servizio** -> **Modifica**.

     [![Opzione di modifica](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. In **Impostazioni avanzate** deselezionare **Abilita la raccolta dati del modello**. 

     [![Deselezionare la raccolta dati](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Selezionare **Aggiorna** per applicare la modifica.

  È anche possibile accedere a queste impostazioni nell'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com).

+ Opzione 2: uso di Python per disabilitare la raccolta dei dati:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Convalidare i dati e analizzarli
È possibile scegliere qualsiasi strumento per analizzare i dati raccolti nel BLOB di Azure.

Per accedere rapidamente ai dati dal BLOB:
1. Accedere a [Azure Machine Learning Studio](https://ml.azure.com).

1. Aprire l'area di lavoro.
1. Fare clic su **Archiviazione**.

    [![Archiviazione](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Seguire il percorso dei dati di output nel BLOB con questa sintassi:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analisi dei dati del modello tramite Power BI

1. Scaricare e aprire [Power bi desktop](https://www.powerbi.com)

1. Selezionare **Recupera dati** e fare clic su [**Archivio BLOB di Azure**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Configurazione di BLOB per Power BI](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Aggiungere il nome dell'account di archiviazione e immettere la chiave di archiviazione. È possibile trovare queste informazioni in **Impostazioni** >> Chiavi di accesso per il BLOB. 

1. Selezionare il contenitore **modeldata** e fare clic su **Modifica**. 

    [![Strumento di navigazione Power BI](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Nell'editor di query fare clic sotto la colonna "Nome" e aggiungere l'account di archiviazione 1. Modellare il percorso nel filtro. Nota: se si vogliono esaminare solo i file da un anno o un mese specifico, espandere semplicemente il percorso di filtro. Ad esempio, è sufficiente esaminare i dati di marzo:/modelData/SubscriptionId >/resourcegroupname >/workspacename >/WebServiceName >/ModelName >/modelversion > base/designazione >/anno >/3

1. Filtrare i dati di interesse in base a **Nome**. Se sono state archiviate **stime** e **input**, è necessario creare una query per ciascuna di esse.

1. Fare clic sulla freccia doppia a lato della colonna **Contenuto** per combinare i file. 

    [![Contenuto di Power BI](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Fare clic su OK. I dati verranno precaricati.

    [![Combinare file in Power BI](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. È ora possibile fare clic su **Chiudi e applica**.

1.  Se sono stati aggiunti input e stime, le tabelle verranno automaticamente correlate in base ai **RequestId**.

1. Iniziare a creare report personalizzati per i dati del modello.


### <a name="analyzing-model-data-using-databricks"></a>Analisi dei dati del modello con Databricks

1. Creare un'[area di lavoro di Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Passare all'area di lavoro di Databricks. 

1. Nell'area di lavoro databricks selezionare **Carica dati**.

    [![Caricamento dei dati](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Creare una nuova tabella e selezionare **Other Data Sources** (Altre origini dati) -> Azure Blob Storage (Archivio BLOB di Azure) -> Create Table in Notebook (Crea tabella in notebook).

    [![Tabella di database](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Aggiornare il percorso dei dati. Di seguito è fornito un esempio:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```
 
    [![Configurazione database](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Seguire i passaggi nel modello per visualizzare e analizzare i dati. 

## <a name="example-notebook"></a>Notebook di esempio

Il notebook [how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/deployment/enable-data-collection-for-models-in-aks/enable-data-collection-for-models-in-aks.ipynb) illustra i concetti descritti in questo articolo.  

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
