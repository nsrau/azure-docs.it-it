---
title: Abilitare la raccolta di dati per i modelli nell'ambiente di produzione - Azure Machine Learning
description: Informazioni su come raccogliere i dati del modello di input di Azure Machine Learning in un archivio BLOB di Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: marthalc
author: marthalc
ms.date: 11/08/2018
ms.openlocfilehash: f4340d1ef30bb4317e658c9a9a936f009054e784
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/15/2018
ms.locfileid: "51710631"
---
# <a name="collect-data-for-models-in-production"></a>Raccogliere i dati per i modelli nell'ambiente di produzione

In questo articolo viene descritto come raccogliere i dati del modello di input dai servizi Azure Machine Learning che sono stati distribuiti nel cluster Kubernetes di Azure (AKS) in un archivio BLOB di Azure. 

Una volta abilitata, la raccolta dei dati consente di:
* Monitorare le deviazioni mentre i dati di produzione attraversano il modello

* Prendere decisioni migliori sui casi in cui ripetere il training o ottimizzare il modello

* Ripetere il training del modello con i dati raccolti

## <a name="what-is-collected-and-where-does-it-go"></a>Quali informazioni vengono raccolte e dove sono trasferite?

Possono essere raccolti i dati seguenti:
* Dati di **input** del modello dai servizi Web distribuiti nel cluster Kubernetes di Azure (AKS) (audio, immagini e video **non** vengono raccolti) 
  
* Previsioni del modello usando i dati di input di produzione.

> [!Note]
> La pre-aggregazione o il pre-calcolo su tali dati al momento non fanno parte del servizio.   

L'output viene salvato in un BLOB di Azure. Poiché i dati vengono aggiunti a un BLOB di Azure, è possibile scegliere lo strumento che si preferisce per eseguire l'analisi. 

La sintassi per il percorso dei dati di output nel BLOB è la seguente:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```
>[!NOTE]
> Il codice in questo articolo è stato testato con Azure Machine Learning SDK versione 0.1.74

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://aka.ms/AMLfree) prima di iniziare.

- Un'area di lavoro del servizio Azure Machine Learning, una directory locale contenente gli script e Azure Machine Learning SDK per Python installato. Informazioni su come ottenere questi prerequisiti usando il documento [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).

- Un modello di training di Machine Learning da distribuire nel servizio Kubernetes di Azure (AKS). Se non si dispone di un modello, vedere l'esercitazione su come [eseguire il training del modello di classificazione delle immagini](tutorial-train-models-with-aml.md).

- Un [cluster AKS](how-to-deploy-to-aks.md).

- [Configurare l'ambiente](how-to-configure-environment.md) e installare [Monitoring SDK](https://aka.ms/aml-monitoring-sdk).

## <a name="enable-data-collection"></a>Abilitare la raccolta di dati
È possibile abilitare la raccolta dei dati indipendentemente dal fatto che il modello venga distribuito tramite il servizio Azure Machine Learning o con altri strumenti. 

Per abilitarla, è necessario:

1. Aprire il file di assegnazione dei punteggi. 

1. Aggiungere il [codice seguente](https://aka.ms/aml-monitoring-sdk) all'inizio del file:

   ```python 
   from azureml.monitoring import ModelDataCollector
   ```

2. Dichiarare le variabili per la raccolta dei dati nella funzione `init()`:

    ```python
    global inputs_dc, prediction_dc
    inputs_dc = ModelDataCollector("best_model", identifier="inputs", feature_names=["feat1", "feat2", "feat3". "feat4", "feat5", "feat6"])
    prediction_dc = ModelDataCollector("best_model", identifier="predictions", feature_names=["prediction1", "prediction2"])
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

4. La raccolta dei dati **non** è automaticamente impostata su **true** quando si distribuisce un servizio in AKS, pertanto è necessario aggiornare il file di configurazione, ad esempio: 

    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True)
    ```
    È anche possibile attivare AppInsights per il monitoraggio del servizio modificando questa configurazione:
    ```python
    aks_config = AksWebservice.deploy_configuration(collect_model_data=True, enable_app_insights=True)
    ``` 

5. [Creare una nuova immagine e distribuire il servizio.](how-to-deploy-to-aks.md) 


Se si dispone già di un servizio con le dipendenze installate nel **file di ambiente** e nel **file di assegnazione dei punteggi**, abilitare la raccolta di dati nel modo seguente:

1. Accedere al [portale di Azure](https://portal.azure.com).

1. Aprire l'area di lavoro.

1. Passare a **Distribuzioni** -> **Seleziona servizio** -> **Modifica**.

   ![Modificare il servizio](media/how-to-enable-data-collection/EditService.PNG)

1. In **Impostazioni avanzate** deselezionare **Abilita la raccolta dati del modello**. 

    [![Deselezionare la raccolta dati](media/how-to-enable-data-collection/CheckDataCollection.png)](./media/how-to-enable-data-collection/CheckDataCollection.png#lightbox)

   In questa finestra è anche possibile scegliere "Abilita diagnostica di AppInsights" per tenere traccia dell'integrità del servizio.  

1. Selezionare **Aggiorna** per applicare la modifica.


## <a name="disable-data-collection"></a>Disabilitare la raccolta dei dati
È possibile arrestare la raccolta dei dati in qualsiasi momento. Per disabilitare la raccolta dei dati, usare il codice Python o il portale di Azure.

+ Opzione 1: disabilitazione nel portale di Azure: 
  1. Accedere al [portale di Azure](https://portal.azure.com).

  1. Aprire l'area di lavoro.

  1. Passare a **Distribuzioni** -> **Seleziona servizio** -> **Modifica**.

    [![Modificare il servizio](media/how-to-enable-data-collection/EditService.PNG)](./media/how-to-enable-data-collection/EditService.PNG#lightbox)

  1. In **Impostazioni avanzate** deselezionare **Abilita la raccolta dati del modello**. 

    [![Deselezionare la raccolta dati](media/how-to-enable-data-collection/UncheckDataCollection.png)](./media/how-to-enable-data-collection/UncheckDataCollection.png#lightbox)

  1. Selezionare **Aggiorna** per applicare la modifica.

* Opzione 2: uso di Python per disabilitare la raccolta dei dati:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="validate-your-data-and-analyze-it"></a>Convalidare i dati e analizzarli
È possibile scegliere qualsiasi strumento per analizzare i dati raccolti nel BLOB di Azure. 

Per accedere rapidamente ai dati dal BLOB:
1. Accedere al [portale di Azure](https://portal.azure.com).

1. Aprire l'area di lavoro.
1. Fare clic su **Archiviazione**.

    [![Archiviazione](media/how-to-enable-data-collection/StorageLocation.png)](./media/how-to-enable-data-collection/StorageLocation.png#lightbox)

1. Seguire il percorso dei dati di output nel BLOB con questa sintassi:

```
/modeldata/<subscriptionid>/<resourcegroup>/<workspace>/<webservice>/<model>/<version>/<identifier>/<year>/<month>/<day>/data.csv
# example: /modeldata/1a2b3c4d-5e6f-7g8h-9i10-j11k12l13m14/myresourcegrp/myWorkspace/aks-w-collv9/best_model/10/inputs/2018/12/31/data.csv
```


### <a name="analyzing-model-data-through-power-bi"></a>Analisi dei dati del modello tramite Power BI

1. Scaricare e aprire [Power BI Desktop](http://www.powerbi.com)

1. Selezionare **Recupera dati** e fare clic su [**Archivio BLOB di Azure**](https://docs.microsoft.com/power-bi/desktop-data-sources).

    [![Configurazione di BLOB per Power BI](media/how-to-enable-data-collection/PBIBlob.png)](./media/how-to-enable-data-collection/PBIBlob.png#lightbox)


1. Aggiungere il nome dell'account di archiviazione e immettere la chiave di archiviazione. È possibile trovare queste informazioni in **Impostazioni** >> Chiavi di accesso per il BLOB. 

1. Selezionare il contenitore **modeldata** e fare clic su **Modifica**. 

    [![Strumento di navigazione Power BI](media/how-to-enable-data-collection/pbiNavigator.png)](./media/how-to-enable-data-collection/pbiNavigator.png#lightbox)

1. Nell'editor di query fare clic sotto la colonna "Nome" e aggiungere l'account di archiviazione 1. Modellare il percorso nel filtro. Nota: se si vogliono esaminare solo i file da un anno o un mese specifico, espandere semplicemente il percorso di filtro. Ad esempio, esaminare semplicemente i dati di marzo: /modeldata/subscriptionid>/resourcegroupname>/workspacename>/webservicename>/modelname>/modelversion>/identifier>/year>/3

1. Filtrare i dati di interesse in base a **Nome**. Se sono stati archiviati **stime** e **input**, sarà necessario creare una query per ognuno.

1. Fare clic sulla freccia doppia a lato della colonna **Contenuto** per combinare i file. 

    [![Contenuto di Power BI](media/how-to-enable-data-collection/pbiContent.png)](./media/how-to-enable-data-collection/pbiContent.png#lightbox)

1. Fare clic su OK. I dati verranno precaricati.

    [![Combinare file in Power BI](media/how-to-enable-data-collection/pbiCombine.png)](./media/how-to-enable-data-collection/pbiCombine.png#lightbox)

1. È ora possibile fare clic su **Chiudi e applica**.

1.  Se sono stati aggiunti input e stime, le tabelle verranno automaticamente correlate in base a **RequestId**.

1. Iniziare a creare report personalizzati per i dati del modello.


### <a name="analyzing-model-data-using-databricks"></a>Analisi dei dati del modello con Databricks

1. Creare un'[area di lavoro di Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). 

1. Passare all'area di lavoro di Databricks. 

1. Nell'area di lavoro di Databricks selezionare **Upload Data** (Carica dati).

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

Il notebook [00.Getting Started/12.enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/12.enable-data-collection-for-models-in-aks) illustra i concetti descritti in questo articolo.  

Per ottenere questo blocco appunti:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]
