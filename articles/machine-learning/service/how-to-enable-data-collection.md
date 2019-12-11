---
title: Raccogliere i dati per i modelli di produzione
titleSuffix: Azure Machine Learning
description: Informazioni su come raccogliere Azure Machine Learning i dati del modello di input nell'archivio BLOB di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 4ef1249a601334cc198662b90da95623247190e7
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978186"
---
# <a name="collect-data-for-models-in-production"></a>Raccogliere i dati per i modelli nell'ambiente di produzione

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> Il Azure Machine Learning SDK di monitoraggio verrà ritirato a breve. L'SDK è ancora appropriato per gli sviluppatori che attualmente usano l'SDK per monitorare la tendenza dei dati nei modelli. Per i nuovi clienti è tuttavia consigliabile usare il monitoraggio semplificato [dei dati con Application Insights](https://docs.microsoft.com/azure/machine-learning/service/how-to-enable-app-insights).

Questo articolo illustra come raccogliere i dati del modello di input da Azure Machine Learning. Viene anche illustrato come distribuire i dati di input in un cluster di Azure Kubernetes Service (AKS) e archiviare i dati di output nell'archivio BLOB di Azure.

Una volta abilitata la raccolta, i dati raccolti consentono di:

* [Monitorare le deviazioni dei dati](how-to-monitor-data-drift.md) quando i dati di produzione entrano nel modello.

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
> Nelle versioni di Azure Machine Learning SDK per Python precedenti alla versione 0.1.0 A16, l'argomento `designation` è denominato `identifier`. Se il codice è stato sviluppato con una versione precedente, è necessario aggiornarlo di conseguenza.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare.

- È necessario installare un'area di lavoro di AzureMachine Learning, una directory locale che contiene gli script e il Azure Machine Learning SDK per Python. Per informazioni su come installarli, vedere [come configurare un ambiente di sviluppo](how-to-configure-environment.md).

- È necessario un modello di apprendimento automatico con training da distribuire in AKS. Se non si dispone di un modello, vedere l'esercitazione [Train image classification Model](tutorial-train-models-with-aml.md) .

- È necessario un cluster AKS. Per informazioni su come crearne uno e distribuirlo, vedere [How to deploy and where](how-to-deploy-and-where.md).

- [Configurare l'ambiente](how-to-configure-environment.md) e installare l' [SDK di monitoraggio Azure Machine Learning](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Abilitare la raccolta di dati

È possibile abilitare la raccolta dei dati indipendentemente dal modello distribuito tramite Azure Machine Learning o altri strumenti.

Per abilitare la raccolta dei dati, è necessario:

1. Aprire il file di assegnazione dei punteggi.

1. Aggiungere il [codice seguente](https://aka.ms/aml-monitoring-sdk) all'inizio del file:

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

Se si dispone già di un servizio con le dipendenze installate nel file dell'ambiente e nel file di assegnazione dei punteggi, abilitare la raccolta dei dati attenendosi alla procedura seguente:

1. Passare a [Azure Machine Learning](https://ml.azure.com).

1. Aprire l'area di lavoro.

1. Selezionare **distribuzioni** > **selezionare servizio** > **modifica**.

   ![Modificare il servizio](media/how-to-enable-data-collection/EditService.PNG)

1. In **Impostazioni avanzate**selezionare **Abilita raccolta dati modello**.

    [![selezionare la raccolta dati](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   È anche possibile selezionare **Abilita diagnostica AppInsights** per tenere traccia dell'integrità del servizio.

1. Selezionare **Aggiorna** per applicare le modifiche.

## <a name="disable-data-collection"></a>Disabilitare la raccolta dei dati

È possibile interrompere la raccolta dati in qualsiasi momento. Usare il codice Python o Azure Machine Learning per disabilitare la raccolta dei dati.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>Opzione 1: disabilitare la raccolta dati in Azure Machine Learning

1. Accedere a [Azure Machine Learning](https://ml.azure.com).

1. Aprire l'area di lavoro.

1. Selezionare **distribuzioni** > **selezionare servizio** > **modifica**.

   [![selezionare l'opzione modifica](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. In **Impostazioni avanzate**deselezionare **Abilita raccolta dati modello**.

    [![deselezionare la casella di controllo raccolta dati](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

1. Selezionare **Aggiorna** per applicare la modifica.

È anche possibile accedere a queste impostazioni nell'area di lavoro in [Azure Machine Learning](https://ml.azure.com).

### <a name="option-2---use-python-to-disable-data-collection"></a>Opzione 2: usare Python per disabilitare la raccolta dei dati

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Convalidare e analizzare i dati

È possibile scegliere uno strumento di preferenza per analizzare i dati raccolti nell'archivio BLOB.

### <a name="quickly-access-your-blob-data"></a>Accedi rapidamente ai dati BLOB

1. Accedere a [Azure Machine Learning](https://ml.azure.com).

1. Aprire l'area di lavoro.

1. Selezionare **Archiviazione**.

    [![selezionare l'opzione di archiviazione](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Seguire il percorso dei dati di output del BLOB con questa sintassi:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Analizzare i dati del modello utilizzando Power BI

1. Scaricare e aprire [Power bi desktop](https://www.powerbi.com).

1. Selezionare **recuperare i dati** e selezionare [**archiviazione BLOB di Azure**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [impostazione ![Power BI BLOB](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Aggiungere il nome dell'account di archiviazione e immettere la chiave di archiviazione. È possibile trovare queste informazioni selezionando **impostazioni** > **chiavi di accesso** nel BLOB.

1. Selezionare il contenitore di **dati del modello** e scegliere **modifica**.

    [Strumento di navigazione Power BI ![](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Nell'editor di query fare clic nella colonna **nome** e aggiungere l'account di archiviazione.

1. Immettere il percorso del modello nel filtro. Se si desidera cercare solo i file di un anno o un mese specifico, è sufficiente espandere il percorso del filtro. Ad esempio, per cercare solo i dati di marzo, usare questo percorso filtro:

   /modelData/\<SubscriptionId >/\<resourcegroupname >/\<workspacename >/\<WebServiceName >/\<ModelName >/\<modelversion >/\<designazione >/\<anno >/3

1. Filtrare i dati rilevanti in base ai valori del **nome** . Se sono stati archiviati stime e input, è necessario creare una query per ciascuna di esse.

1. Selezionare le frecce doppie verso il basso accanto all'intestazione della colonna **contenuto** per combinare i file.

    [![Power BI contenuto](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Selezionare **OK**. Precaricamenti dei dati.

    [![Power BI combinare i file](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Selezionare **Chiudi e applica**.

1. Se sono stati aggiunti input e stime, le tabelle vengono ordinate automaticamente in base ai valori **RequestId** .

1. Iniziare a creare report personalizzati per i dati del modello.

### <a name="analyze-model-data-using-azure-databricks"></a>Analizzare i dati del modello utilizzando Azure Databricks

1. Creare un' [area di lavoro Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal).

1. Passare all'area di lavoro di Databricks.

1. Nell'area di lavoro databricks selezionare **Carica dati**.

    [![selezione dell'opzione di caricamento dei dati di databricks](media/how-to-enable-data-collection/dbupload.png)](./media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Selezionare **Crea nuova tabella** e selezionare **altre origini dati** > **archiviazione BLOB di Azure** > **Crea tabella nel notebook**.

    [creazione della tabella ![databricks](media/how-to-enable-data-collection/dbtable.PNG)](./media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Aggiornare il percorso dei dati. Di seguito è fornito un esempio:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [configurazione di ![databricks](media/how-to-enable-data-collection/dbsetup.png)](./media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Seguire i passaggi del modello per visualizzare e analizzare i dati.
