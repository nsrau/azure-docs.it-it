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
ms.date: 09/24/2018
ms.openlocfilehash: 70c023fc8fe996060d3eff3d5a700b5f910097b4
ms.sourcegitcommit: 4eddd89f8f2406f9605d1a46796caf188c458f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2018
ms.locfileid: "49113632"
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

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non se ne ha una, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

- Un'area di lavoro del servizio Azure Machine Learning, una directory locale contenente gli script e Azure Machine Learning SDK per Python installato. Informazioni su come ottenere questi prerequisiti usando il documento [Come configurare un ambiente di sviluppo](how-to-configure-environment.md).

- Un modello di training di Machine Learning da distribuire nel servizio Kubernetes di Azure (AKS). Se non si dispone di un modello, vedere l'esercitazione su come [eseguire il training del modello di classificazione delle immagini](tutorial-train-models-with-aml.md).

- Un [cluster AKS](how-to-deploy-to-aks.md).

- Le dipendenze e il modulo seguenti installati [nel proprio ambiente](how-to-configure-environment.md):
  + In Linux:
    ```shell
    sudo apt-get install libxml++2.6-2v5
    pip install azureml-monitoring
    ```

  + In Windows:
    ```shell
    pip install azureml-monitoring
    ```

## <a name="enable-data-collection"></a>Abilitare la raccolta di dati
È possibile abilitare la raccolta dei dati indipendentemente dal fatto che il modello venga distribuito tramite il servizio Azure Machine Learning o con altri strumenti. 

Per abilitarla, è necessario:

1. Aprire il file di assegnazione dei punteggi. 

1. Aggiungere il codice seguente all'inizio del file:

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

   ![Modificare il servizio](media/how-to-enable-data-collection/EditService.png)

1. In **Impostazioni avanzate** deselezionare **Abilita la raccolta dati del modello**. 

   ![Deselezionare la raccolta dei dati](media/how-to-enable-data-collection/CheckDataCollection.png)

   In questa finestra è anche possibile scegliere "Abilita diagnostica di AppInsights" per tenere traccia dell'integrità del servizio.  

1. Selezionare **Aggiorna** per applicare la modifica.


## <a name="disable-data-collection"></a>Disabilitare la raccolta dei dati
È possibile arrestare la raccolta dei dati in qualsiasi momento. Per disabilitare la raccolta dei dati, usare il codice Python o il portale di Azure.

+ Opzione 1: disabilitazione nel portale di Azure: 
  1. Accedere al [portale di Azure](https://portal.azure.com).

  1. Aprire l'area di lavoro.

  1. Passare a **Distribuzioni** -> **Seleziona servizio** -> **Modifica**.

     ![Modificare il servizio](media/how-to-enable-data-collection/EditService.png)

  1. In **Impostazioni avanzate** deselezionare **Abilita la raccolta dati del modello**. 

     ![Deselezionare la raccolta dei dati](media/how-to-enable-data-collection/UncheckDataCollection.png) 

  1. Selezionare **Aggiorna** per applicare la modifica.

* Opzione 2: uso di Python per disabilitare la raccolta dei dati:

  ```python 
  ## replace <service_name> with the name of the web service
  <service_name>.update(collect_model_data=False)
  ```

## <a name="example-notebook"></a>Notebook di esempio

Il notebook [00.Getting Started/12.enable-data-collection-for-models-in-aks.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/01.getting-started/12.enable-data-collection-for-models-in-aks) illustra i concetti descritti in questo articolo.  

Per ottenere questo blocco appunti:
 
[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]