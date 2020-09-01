---
title: Rilevamento e distribuzione di modelli
titleSuffix: Azure Data Science Virtual Machine
description: Informazioni su come rilevare e registrare gli esperimenti dal Data Science Virtual Machine con Azure Machine Learning e/o MLFlow.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: samkemp
ms.author: samkemp
ms.topic: conceptual
ms.date: 07/17/2020
ms.openlocfilehash: 205aed1811c3d9d21a10be7bc4f01c73eb7295b7
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/01/2020
ms.locfileid: "89254801"
---
# <a name="track-experiments-and-deploy-models-in-azure-machine-learning"></a>Tenere traccia degli esperimenti e distribuire modelli in Azure Machine Learning

Migliorare il processo di creazione del modello tenendo traccia degli esperimenti e monitorando le metriche di esecuzione. Questo articolo illustra come aggiungere codice di registrazione allo script di training usando l'API [MLflow](https://mlflow.org/) e come tenere traccia dell'esperimento in Azure Machine Learning.

Il diagramma seguente illustra il rilevamento delle metriche di esecuzione di un esperimento e l'archiviazione degli elementi del modello nell'area di lavoro di Azure Machine Learning.

![tenere traccia degli esperimenti](./media/how-to-track-experiments/mlflow-diagram-track.png)

## <a name="prerequisites"></a>Prerequisiti

* È necessario effettuare il [provisioning di un area di lavoro di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/how-to-manage-workspace#create-a-workspace)

## <a name="create-a-new-notebook"></a>Creare un nuovo notebook

Il Azure Machine Learning e l'SDK di MLFlow sono preinstallati nel Data Science VM ed è possibile accedervi nell'ambiente **azureml_py36_ \* ** conda. In Jupyterlab fare clic sul pulsante di avvio e selezionare il seguente kernel:

![selezione kernel](./media/how-to-track-experiments/experiment-tracking-1.png)

## <a name="set-up-the-workspace"></a>Configurare l'area di lavoro

Passare alla [portale di Azure](https://portal.azure.com) e selezionare l'area di lavoro di cui è stato effettuato il provisioning come parte dei prerequisiti. Verrà visualizzato il __download config.js__ (vedere di seguito): scaricare la configurazione e assicurarsi che sia archiviata nella directory di lavoro in DSVM.

![Ottenere il file di configurazione](./media/how-to-track-experiments/experiment-tracking-2.png)

La configurazione contiene informazioni quali il nome dell'area di lavoro, la sottoscrizione e così via e significa che non è necessario codificare questi parametri.

## <a name="track-dsvm-runs"></a>Rileva esecuzioni DSVM

Aggiungere il codice seguente al notebook (o allo script) per impostare l'oggetto area di lavoro AzureML.

```Python
import mlflow
from azureml.core import Workspace

ws = Workspace.from_config()

mlflow.set_tracking_uri(ws.get_mlflow_tracking_uri())
```

>[!NOTE]
L'URI di rilevamento è valido fino a un massimo di un'ora. Se si riavvia lo script dopo un periodo di inattività, usare l'API get_mlflow_tracking_uri per ottenere un nuovo URI.

### <a name="load-the-data"></a>Caricare i dati

Questo esempio usa il set di dati Diabetes, un set di dati di piccole dimensioni provvisto di scikit-learn. Questa cella carica il set di dati e lo divide in set di training e test casuali.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
from sklearn.externals import joblib

X, y = load_diabetes(return_X_y = True)
columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=0)
data = {
    "train":{"X": X_train, "y": y_train},
    "test":{"X": X_test, "y": y_test}
}

print ("Data contains", len(data['train']['X']), "training samples and",len(data['test']['X']), "test samples")
```

### <a name="add-tracking"></a>Aggiungere il rilevamento

Aggiungere il rilevamento dell'esperimento usando Azure Machine Learning SDK e caricare un modello persistente nel record esecuzione dell'esperimento. Il codice seguente aggiunge log e carica un file di modello nell'esecuzione dell'esperimento. Il modello viene inoltre registrato nel registro di sistema del modello Azure Machine Learning.

```python
# Get an experiment object from Azure Machine Learning
from azureml.mlflow import register_model
experiment_name = 'experiment_with_mlflow'
mlflow.set_experiment(experiment_name)

with mlflow.start_run():
    # Log the algorithm parameter alpha to the run
    mlflow.log_param('alpha', 0.03)

    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])

    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    mlflow.log_metric('mse', mean_squared_error(data['test']['y'], preds))

    # Save the model
    model_file_name = 'model.pkl'
    joblib.dump(value = regression_model, filename = model_file_name)

    # upload the model file explicitly into artifacts
    mlflow.log_artifact(model_file_name)
    # register the model
    register_model(mlflow.active_run(), 'diabetes_model', 'model.pkl', model_framework="ScikitLearn")
```

### <a name="view-runs-in-azure-machine-learning"></a>Visualizza esecuzioni in Azure Machine Learning

È possibile visualizzare l'esperimento eseguito in [Azure Machine Learning Studio](https://ml.azure.com). Fare clic su __esperimenti__ nel menu a sinistra e selezionare "experiment_with_mlflow" (oppure se si è deciso di assegnare un nome diverso all'esperimento nel frammento precedente, fare clic sul nome usato):

![Seleziona esperimento](./media/how-to-track-experiments/mlflow-experiments.png)

Verrà visualizzato l'errore quadratico medio (MSE) registrato:

![MSE](./media/how-to-track-experiments/mlflow-experiments-2.png)

Se si fa clic sull'esecuzione, verranno visualizzati altri dettagli e anche il modello selezionato nei __log di output +__

## <a name="deploy-model-in-azure-machine-learning"></a>Distribuire il modello in Azure Machine Learning

In questa sezione viene descritto come distribuire i modelli sottoposti a training in un DSVM per Azure Machine Learning.

### <a name="step-1-create-inference-compute"></a>Passaggio 1: creare un calcolo inferenza

Nel menu a sinistra di [AzureML Studio](https://ml.azure.com) fare clic su __calcolo__ e quindi sulla scheda __cluster di inferenza__ . Fare quindi clic su __+ nuovo__ come illustrato di seguito:

![Crea calcolo inferenza](./media/how-to-track-experiments/mlflow-experiments-6.png)

Nel riquadro __nuovo cluster inferenza__ immettere i dettagli per:

* Nome calcolo
* Servizio Kubernetes-selezionare Crea nuovo
* Selezionare l'area
* Selezionare le dimensioni della VM (ai fini di questa esercitazione, il valore predefinito di Standard_D3_v2 è sufficiente)
* Scopo del cluster: selezionare __sviluppo e test__
* Il numero di nodi deve essere uguale a __1__
* Configurazione di rete-di base

Fare quindi clic su __Crea__.

![Dettagli calcolo](./media/how-to-track-experiments/mlflow-experiments-7.png)

### <a name="step-2-deploy-no-code-inference-service"></a>Passaggio 2: distribuire il servizio di inferenza senza codice

Quando il modello è stato registrato nel codice usando `register_model` , il Framework è stato specificato come sklearn. Azure Machine Learning non supporta le distribuzioni di codice per i Framework seguenti:

* scikit-learn
* Formato Tensorflow SaveModel
* Formato di modello ONNX

La distribuzione senza codice significa che è possibile distribuire direttamente dall'artefatto del modello senza dover specificare uno script di assegnazione dei punteggi specifico.

Per distribuire il modello di diabete, andare al menu a sinistra nel [Azure Machine Learning Studio](https://ml.azure.com) e selezionare __modelli__. Fare quindi clic sul diabetes_model registrato:

![Seleziona modello](./media/how-to-track-experiments/mlflow-experiments-3.png)

Fare quindi clic sul pulsante __Distribuisci__ nel riquadro Dettagli modello:

![Distribuire](./media/how-to-track-experiments/mlflow-experiments-4.png)

Il modello viene distribuito nel cluster di inferenza (servizio Azure Kubernetes) creato nel passaggio 1. Inserire i dettagli seguenti specificando un nome per il servizio e il nome del cluster di calcolo AKS (creato nel passaggio 1). Si consiglia inoltre di aumentare la __capacità di riserva della CPU__ a 1 (da 0,1) e la __capacità di riserva di memoria__ a 1 (a partire da 0,5). è possibile apportare questo aumento facendo clic su __Avanzate__ e inserendo i dettagli. Fare quindi clic su __Distribuisci__.

![Dettagli distribuzione](./media/how-to-track-experiments/mlflow-experiments-5.png)

### <a name="step-3-consume"></a>Passaggio 3: utilizzo

Quando il modello è stato distribuito correttamente, verrà visualizzato quanto segue (per visualizzare questa pagina, fare clic su endpoint nel menu a sinistra > fare clic sul nome del servizio distribuito):

![Usa modello](./media/how-to-track-experiments/mlflow-experiments-8.png)

Si noterà che lo stato di distribuzione passa dalla __transizione__ a __integro__. Questa sezione dei dettagli fornisce anche l'endpoint REST e gli URL di spavalderia che uno sviluppatore di applicazioni può usare per integrare il modello ML nelle app.

È possibile eseguire il test dell'endpoint usando il [post](https://www.postman.com/), oppure è possibile usare AzureML SDK:

```python
from azureml.core import Webservice
import json

# if you called your service differently then change the name below
service = Webservice(ws, name="diabetes-service")

input_payload = json.dumps({
    'data': X_test[0:2].tolist(),
    'method': 'predict'  # If you have a classification model, you can get probabilities by changing this to 'predict_proba'.
})

output = service.run(input_payload)

print(output)
```

### <a name="step-4-clean-up"></a>Passaggio 4: eseguire la pulizia

Eliminare il calcolo inferenza creato nel passaggio 1, in modo da non incorrere in costi di calcolo continui. Nel menu a sinistra nel Azure Machine Learning Studio fare clic su Compute > inferenza Clusters > selezionare il calcolo > Delete.

## <a name="next-steps"></a>Passaggi successivi

* Altre informazioni sulla [distribuzione di modelli in AzureML](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)
