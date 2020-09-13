---
title: Raccogliere i dati per i modelli di produzione
titleSuffix: Azure Machine Learning
description: Informazioni su come raccogliere dati da un modello di Azure Machine Learning distribuito
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: sgilley
ms.author: copeters
author: lostmygithubaccount
ms.date: 07/14/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 72706a67e1798662ea0f40fa7843c32d8267e0d0
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/10/2020
ms.locfileid: "89646075"
---
# <a name="collect-data-from-models-in-production"></a>Raccogliere dati dai modelli nell'ambiente di produzione

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come raccogliere dati da un modello di Azure Machine Learning distribuito in un cluster Azure Kubernetes Service (AKS). I dati raccolti vengono quindi archiviati nell'archivio BLOB di Azure.

Una volta abilitata la raccolta, i dati raccolti consentono di:

* [Monitorare le deviazioni dei dati](how-to-monitor-datasets.md) nei dati di produzione raccolti.

* Analizzare i dati raccolti usando [Power bi](#powerbi) o [Azure Databricks](#databricks)

* Prendere decisioni migliori su quando ripetere il training del modello o ottimizzarlo.

* Ripetere il training del modello con i dati raccolti.

## <a name="what-is-collected-and-where-it-goes"></a>Cosa viene raccolto e dove va

Possono essere raccolti i dati seguenti:

* Modellare i dati di input da servizi Web distribuiti in un cluster AKS. L'audio vocale, le immagini e i video *non* vengono raccolti.
  
* Previsioni del modello usando i dati di input di produzione.

>[!NOTE]
> La preaggregazione e i Precalcoli su questi dati non fanno attualmente parte del servizio di raccolta.

L'output viene salvato nell'archivio BLOB. Poiché i dati vengono aggiunti all'archiviazione BLOB, è possibile scegliere lo strumento preferito per eseguire l'analisi.

La sintassi per il percorso dei dati di output nel BLOB è la seguente:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> Nelle versioni di Azure Machine Learning SDK per Python precedenti alla versione 0.1.0 A16, l' `designation` argomento è denominato `identifier` . Se il codice è stato sviluppato con una versione precedente, è necessario aggiornarlo di conseguenza.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare.

- È necessario installare un'area di lavoro Azure Machine Learning, una directory locale che contiene gli script e il Azure Machine Learning SDK per Python. Per informazioni su come installarli, vedere [come configurare un ambiente di sviluppo](how-to-configure-environment.md).

- È necessario un modello di apprendimento automatico con training da distribuire in AKS. Se non si dispone di un modello, vedere l'esercitazione [Train image classification Model](tutorial-train-models-with-aml.md) .

- È necessario un cluster AKS. Per informazioni su come crearne uno e distribuirlo, vedere [How to deploy and where](how-to-deploy-and-where.md).

- [Configurare l'ambiente](how-to-configure-environment.md) e installare l' [SDK di monitoraggio Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py&preserve-view=true).

## <a name="enable-data-collection"></a>Abilitare la raccolta dati

È possibile abilitare la [raccolta dei dati](https://docs.microsoft.com/python/api/azureml-monitoring/azureml.monitoring.modeldatacollector.modeldatacollector?view=azure-ml-py&preserve-view=true) indipendentemente dal modello distribuito tramite Azure Machine Learning o altri strumenti.

Per abilitare la raccolta dei dati, è necessario:

1. Aprire il file di assegnazione dei punteggi.

1. Aggiungere il codice seguente all'inizio del file:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

1. Dichiarare le variabili per la raccolta dei dati nella funzione `init`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", designation="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", designation="predictions", feature_names=["prediction1", "prediction2"])
    ```

    *CorrelationId* è un parametro facoltativo. Non è necessario usarlo se il modello non lo richiede. L'uso di *CorrelationId* consente di eseguire facilmente il mapping con altri dati, ad esempio *LoanNumber* o *CustomerID*.
    
    Il parametro *Identifier* viene usato in un secondo momento per compilare la struttura di cartelle nel BLOB. È possibile usarlo per distinguere i dati non elaborati dai dati elaborati.

1. Aggiungere le righe di codice seguenti alla funzione `run(input_df)`:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. La raccolta dei dati *non* viene impostata automaticamente su **true** quando si distribuisce un servizio in AKS. Aggiornare il file di configurazione, come nell'esempio seguente:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    È anche possibile abilitare Application Insights per il monitoraggio del servizio modificando questa configurazione:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Per creare una nuova immagine e distribuire il modello di apprendimento automatico, vedere [How to deploy and where](how-to-deploy-and-where.md).


## <a name="disable-data-collection"></a>Disabilitare la raccolta dei dati

È possibile interrompere la raccolta dati in qualsiasi momento. Usare il codice Python per disabilitare la raccolta dei dati.

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Convalidare e analizzare i dati

È possibile scegliere uno strumento di preferenza per analizzare i dati raccolti nell'archivio BLOB.

### <a name="quickly-access-your-blob-data"></a>Accedi rapidamente ai dati BLOB

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Aprire l'area di lavoro.

1. Selezionare **Archiviazione**.

    [![Selezionare l'opzione di archiviazione](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Seguire il percorso dei dati di output del BLOB con questa sintassi:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a><a id="powerbi"></a> Analizzare i dati del modello utilizzando Power BI

1. Scaricare e aprire [Power bi desktop](https://www.powerbi.com).

1. Selezionare **recuperare i dati** e selezionare [**archiviazione BLOB di Azure**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Impostazione Power BI BLOB](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Aggiungere il nome dell'account di archiviazione e immettere la chiave di archiviazione. È possibile trovare queste informazioni selezionando **Impostazioni**  >  **chiavi di accesso** nel BLOB.

1. Selezionare il contenitore di **dati del modello** e scegliere **modifica**.

    [![Strumento di navigazione Power BI](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Nell'editor di query fare clic nella colonna **nome** e aggiungere l'account di archiviazione.

1. Immettere il percorso del modello nel filtro. Se si desidera cercare solo i file di un anno o un mese specifico, è sufficiente espandere il percorso del filtro. Ad esempio, per cercare solo i dati di marzo, usare questo percorso filtro:

   /modelData/ \<subscriptionid> / \<resourcegroupname> / \<workspacename> / \<webservicename> / \<modelname> / \<modelversion> / \<designation> / \<year> /3

1. Filtrare i dati rilevanti in base ai valori del **nome** . Se sono stati archiviati stime e input, è necessario creare una query per ciascuna di esse.

1. Selezionare le frecce doppie verso il basso accanto all'intestazione della colonna **contenuto** per combinare i file.

    [![Contenuto Power BI](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Selezionare **OK**. Precaricamenti dei dati.

    [![Power BI combinare i file](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Selezionare **Chiudi e applica**.

1. Se sono stati aggiunti input e stime, le tabelle vengono ordinate automaticamente in base ai valori **RequestId** .

1. Iniziare a creare report personalizzati per i dati del modello.

### <a name="analyze-model-data-using-azure-databricks"></a><a id="databricks"></a> Analizzare i dati del modello utilizzando Azure Databricks

1. Creare un' [area di lavoro Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

1. Passare all'area di lavoro di Databricks.

1. Nell'area di lavoro databricks selezionare **Carica dati**.

    [![Selezione dell'opzione di caricamento dei dati di databricks](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Selezionare **Crea nuova tabella** e selezionare **altre origini dati**  >  **archiviazione BLOB**  >  **di Azure crea tabella nel notebook**.

    [![Creazione della tabella databricks](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Aggiornare il percorso dei dati. Ecco un esempio:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Installazione di databricks](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Seguire i passaggi del modello per visualizzare e analizzare i dati.

## <a name="next-steps"></a>Passaggi successivi

[Rilevare la tendenza dei dati](how-to-monitor-datasets.md) nei dati raccolti.
