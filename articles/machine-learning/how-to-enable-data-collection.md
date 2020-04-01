---
title: Raccogliere i dati per i modelli di produzione
titleSuffix: Azure Machine Learning
description: Informazioni su come raccogliere i dati del modello di input di Azure Machine Learning nell'archiviazione BLOB di Azure.Learn how to collect Azure Machine Learning input model data in Azure Blob storage.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: laobri
ms.author: copeters
author: lostmygithubaccount
ms.date: 11/12/2019
ms.custom: seodec18
ms.openlocfilehash: 44acc81df9eb6dc6a6af28b5b0f4730aa93adffc
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475438"
---
# <a name="collect-data-for-models-in-production"></a>Raccogliere i dati per i modelli nell'ambiente di produzione

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

>[!IMPORTANT]
> L'SDK di monitoraggio di Azure Machine Learning verrà presto ritirato. L'SDK è ancora appropriato per gli sviluppatori che attualmente utilizzano l'SDK per monitorare la deriva dei dati nei modelli. Tuttavia, per i nuovi clienti, è consigliabile utilizzare il monitoraggio dei dati semplificato [con Application Insights](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights).

Questo articolo illustra come raccogliere dati del modello di input da Azure Machine Learning.This article shows how to collect input model data from Azure Machine Learning. Viene inoltre illustrato come distribuire i dati di input in un cluster di azure Kubernetes Service (AKS) e archiviare i dati di output nell'archiviazione BLOB di Azure.It also shows how to deploy the input data into an Azure Kubernetes Service (AKS) cluster and store the output data in Azure Blob storage.

Una volta abilitata la raccolta, i dati raccolti consentono di:

* [Monitorare le derivarie dei dati](how-to-monitor-data-drift.md) man mano che i dati di produzione entrano nel modello.

* Prendi decisioni migliori su quando riqualificare o ottimizzare il tuo modello.

* Riqualificare il modello con i dati raccolti.

## <a name="what-is-collected-and-where-it-goes"></a>Cosa viene raccolto e dove va

Possono essere raccolti i dati seguenti:

* Modellare i dati di input dai servizi Web distribuiti in un cluster AKS. L'audio, le immagini e i video vocali *non* vengono raccolti.
  
* Previsioni del modello usando i dati di input di produzione.

>[!NOTE]
> Preaggregazione e precalcoli su questi dati non fanno attualmente parte del servizio di raccolta.

L'output viene salvato nell'archivio BLOB. Poiché i dati vengono aggiunti all'archiviazione BLOB, è possibile scegliere lo strumento preferito per eseguire l'analisi.

La sintassi per il percorso dei dati di output nel BLOB è la seguente:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```

>[!NOTE]
> Nelle versioni di Azure Machine Learning SDK per Python precedenti alla versione `designation` 0.1.0a16, l'argomento è denominato `identifier`. Se il codice è stato sviluppato con una versione precedente, è necessario aggiornarlo di conseguenza.

## <a name="prerequisites"></a>Prerequisiti

- Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare.

- È necessario installare un'area di lavoro di AzureMachine Learning, una directory locale contenente gli script e Azure Machine Learning SDK per Python.A AzureMachine Learning workspace, a local directory containing your scripts, and the Azure Machine Learning SDK for Python must be installed. Per informazioni su come installarli, vedere [Come configurare un ambiente](how-to-configure-environment.md)di sviluppo .

- È necessario un modello di apprendimento automatico sottoposto a training da distribuire in AKS. Se non si dispone di un modello, vedere l'esercitazione Eseguire il modello di [classificazione delle immagini](tutorial-train-models-with-aml.md) del treno.

- È necessario un cluster AKS. Per informazioni su come crearne uno e distribuirlo, vedere [Come distribuire e dove](how-to-deploy-and-where.md).

- [Configurare l'ambiente](how-to-configure-environment.md) e installare [Azure Machine Learning Monitoring SDK.](https://aka.ms/aml-monitoring-sdk)

## <a name="enable-data-collection"></a>Abilitare la raccolta di dati

È possibile abilitare la raccolta dati indipendentemente dal modello distribuito tramite Azure Machine Learning o altri strumenti.

Per abilitare la raccolta dei dati, è necessario:To enable data collection, you need to:

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

    *CorrelationId* è un parametro facoltativo. Non è necessario utilizzarlo se il modello non lo richiede. L'uso di *CorrelationId* consente di eseguire più facilmente il mapping con altri dati, ad esempio *LoanNumber* o *CustomerId*.
    
    Il parametro *Identifier* viene usato successivamente per compilare la struttura di cartelle nel BLOB. È possibile utilizzarlo per differenziare i dati non elaborati dai dati elaborati.

1. Aggiungere le righe di codice seguenti alla funzione `run(input_df)`:

    ```python
    data = np.array(data)
    result = model.predict(data)
    inputs_dc.collect(data) #this call is saving our input data into Azure Blob
    prediction_dc.collect(result) #this call is saving our input data into Azure Blob
    ```

1. La raccolta dei dati *non* viene impostata automaticamente su true quando si distribuisce un servizio in AKS.Data collection is not automatically set to **true** when you deploy a service in AKS. Aggiornare il file di configurazione, come nell'esempio seguente:Update your configuration file, as in the following example:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```

    È anche possibile abilitare Application Insights per il monitoraggio del servizio modificando questa configurazione:You can also enable Application Insights for service monitoring by changing this configuration:

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ```

1. Per creare una nuova immagine e distribuire il modello di apprendimento automatico, vedere [Come distribuire e dove](how-to-deploy-and-where.md).

Se si dispone già di un servizio con le dipendenze installate nel file di ambiente e nel file di punteggio, abilitare la raccolta dei dati attenendosi alla procedura seguente:If you already have a service with the dependencies installed in your environment file and scoring file, enable data collection by following these steps:

1. Passare ad [Azure Machine Learning](https://ml.azure.com).

1. Aprire l'area di lavoro.

1. Selezionare **Distribuzioni** > **Selezionare il servizio** > **Modifica**.

   ![Modificare il servizio](././media/how-to-enable-data-collection/EditService.PNG)

1. In **Impostazioni avanzate**selezionare Abilita la diagnostica e la raccolta dati di Application **Insights**.

1. Selezionare **Aggiorna** per applicare le modifiche.

## <a name="disable-data-collection"></a>Disabilitare la raccolta dei dati

Puoi interrompere la raccolta dei dati in qualsiasi momento. Usare il codice Python o Azure Machine Learning per disabilitare la raccolta dei dati.

### <a name="option-1---disable-data-collection-in-azure-machine-learning"></a>Opzione 1 - Disabilitare la raccolta dati in Azure Machine LearningOption 1 - Disable data collection in Azure Machine Learning

1. Accedere ad [Azure Machine Learning](https://ml.azure.com).

1. Aprire l'area di lavoro.

1. Selezionare **Distribuzioni** > **Selezionare il servizio** > **Modifica**.

   [![Selezionare l'opzione Modifica](././media/how-to-enable-data-collection/EditService.PNG)](./././media/how-to-enable-data-collection/EditService.PNG#lightbox)

1. In **Impostazioni avanzate** **deselezionare Abilita diagnostica e raccolta dati**di Application Insights .

1. Selezionare **Aggiorna** per applicare la modifica.

È anche possibile accedere a queste impostazioni nell'area di lavoro in [Azure Machine Learning.](https://ml.azure.com)

### <a name="option-2---use-python-to-disable-data-collection"></a>Opzione 2 - Utilizzare Python per disabilitare la raccolta dei datiOption 2 - Use Python to disable data collection

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-and-analyze-your-data"></a>Convalidare e analizzare i dati

È possibile scegliere uno strumento delle preferenze per analizzare i dati raccolti nell'archivio BLOB.

### <a name="quickly-access-your-blob-data"></a>Accedere rapidamente ai dati BLOB

1. Accedere ad [Azure Machine Learning](https://ml.azure.com).

1. Aprire l'area di lavoro.

1. Selezionare **Archiviazione**.

    [![Selezionare l'opzione Archiviazione](./media/how-to-enable-data-collection/StorageLocation.png)](././media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Seguire il percorso dei dati di output del BLOB con questa sintassi:Follow the path to the blob's output data with this syntax:

   ```
   /modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<designation>/<year>/<month>/<day>/data.csv
   # example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
   ```

### <a name="analyze-model-data-using-power-bi"></a>Analizzare i dati del modello usando Power BIAnalyze model data using Power BI

1. Scaricare e aprire [Power BI Desktop](https://www.powerbi.com).

1. Selezionare **Ottieni dati** e quindi [**Archiviazione BLOB di Azure**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Configurazione del BLOB di Power BI](./media/how-to-enable-data-collection/PBIBlob.png)](././media/how-to-enable-data-collection/PBIBlob.png#lightbox)

1. Aggiungere il nome dell'account di archiviazione e immettere la chiave di archiviazione. È possibile trovare queste informazioni selezionando **Impostazioni** > **Chiavi di accesso** nel BLOB.

1. Selezionare il contenitore **di dati** del modello e **scegliere Modifica**.

    [![Navigatore di Power BI](./media/how-to-enable-data-collection/pbiNavigator.png)](././media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Nell'editor di query fare clic nella colonna **Nome** e aggiungere l'account di archiviazione.

1. Immettere il percorso del modello nel filtro. Se si desidera esaminare solo i file di un anno o di un mese specifico, è sufficiente espandere il percorso del filtro. Ad esempio, per esaminare solo i dati di marzo, utilizzare questo percorso di filtro:For example, to look only into March data, use this filter path:

   /modeldata/\<>\<subscriptionid/>\<resourcegroupname/\<workspacename>/ webservicename>/\<modelname>/\<modelversion>/\<> di designazione/\<anno>/3

1. Filtrare i dati rilevanti in base ai valori **Nome.** Se sono stati archiviati stime e input, è necessario creare una query per ognuno di essi.

1. Selezionare le doppie frecce verso il basso accanto all'intestazione di colonna **Contenuto** per combinare i file.

    [![Contenuto di Power BI](./media/how-to-enable-data-collection/pbiContent.png)](././media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Selezionare **OK**. I dati vengono precaricati.

    [![Combinazione di file di Power BI](./media/how-to-enable-data-collection/pbiCombine.png)](././media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. Selezionare **Chiudi e applica**.

1. Se sono stati aggiunti input e stime, le tabelle vengono ordinate automaticamente in base ai valori **RequestId.If** you added inputs and predictions, your tables are automatically ordered by RequestId values.

1. Iniziare a creare report personalizzati per i dati del modello.

### <a name="analyze-model-data-using-azure-databricks"></a>Analizzare i dati del modello usando Azure DatabricksAnalyze model data using Azure Databricks

1. Creare un'area di lavoro di [Azure Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal)

1. Passare all'area di lavoro di Databricks.

1. Nell'area di lavoro Databricks selezionare **Carica dati**.

    [![Selezione dell'opzione Datimatti per il caricamento dei dati](./media/how-to-enable-data-collection/dbupload.png)](././media/how-to-enable-data-collection/dbupload.png#lightbox)

1. Selezionare **Crea nuova tabella** e selezionare Altre origini > dati**Archiviazione** > BLOB di **Azure****in Blocco appunti.**

    [![Creazione tabella Databricks](./media/how-to-enable-data-collection/dbtable.PNG)](././media/how-to-enable-data-collection/dbtable.PNG#lightbox)

1. Aggiornare la posizione dei dati. Esempio:

    ```
    file_location = "wasbs://mycontainer@storageaccountname.blob.core.windows.net/modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/*/*/data.csv" 
    file_type = "csv"
    ```

    [![Impostazione databricks](./media/how-to-enable-data-collection/dbsetup.png)](././media/how-to-enable-data-collection/dbsetup.png#lightbox)

1. Seguire i passaggi sul modello per visualizzare e analizzare i dati.
