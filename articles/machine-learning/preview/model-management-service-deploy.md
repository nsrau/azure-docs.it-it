---
title: Distribuzione dei servizi Web di Gestione modelli di Azure Machine Learning | Microsoft Docs
description: Questo documento descrive i passaggi necessari per distribuire un modello di Machine Learning usando la Gestione modelli di Azure Machine Learning.
services: machine-learning
author: raymondl
ms.author: raymondl, aashishb
manager: neerajkh
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.topic: article
ms.date: 01/03/2018
ms.openlocfilehash: 965e33f3c7d050dca8f6c4e92d75cb7c7a8fa60d
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/03/2018
---
# <a name="deploying-a-machine-learning-model-as-a-web-service"></a>Distribuzione di un modello di Machine Learning come un servizio Web

Gestione modelli di Azure Machine Learning fornisce interfacce per distribuire i modelli come servizi Web basati su Docker in contenitori. È possibile distribuire i modelli creati tramite framework come Spark, Microsoft Cognitive Toolkit (CNTK), Keras, Tensorflow e Python. 

Questo documento illustra i passaggi per distribuire i modelli come servizi Web tramite l'interfaccia della riga di comando (CLI) di Gestione modelli di Azure Machine Learning.

## <a name="deploying-web-services"></a>Distribuzione dei servizi Web
Usando le interfacce della riga di comando è possibile distribuire servizi Web per l'esecuzione nel computer locale o in un cluster.

È consigliabile iniziare con una distribuzione locale. Per prima cosa confermare che il modello e il codice funzionino e quindi eseguire la distribuzione del servizio Web in un cluster per l'uso a livello di produzione. Per altre informazioni sulla configurazione dell'ambiente per la distribuzione di cluster, vedere [Model Management configuration](deployment-setup-configuration.md) (Configurazione della Gestione modelli). 

Di seguito sono indicati i passaggi di distribuzione:
1. Usare il modello di Machine Learning salvato e sottoposto a training
2. Creare uno schema per i dati di input e di output del servizio Web
3. Creare un'immagine contenitore basato su Docker
4. Creare e distribuire il servizio Web

### <a name="1-save-your-model"></a>1. Salvare il modello
Iniziare con il file di modello salvato sottoposto a training, ad esempio mymodel.pkl. L'estensione del file varia in base alla piattaforma usata per sottoporre a training e salvare il modello. 

È possibile usare ad esempio la libreria Pickle di Python per salvare in un file un modello sottoposto a training. Di seguito è illustrato un [esempio](http://scikit-learn.org/stable/modules/model_persistence.html) con il set di dati Iris:

```python
import pickle
from sklearn import datasets
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf = linear_model.LogisticRegression()
clf.fit(X, y)  
saved_model = pickle.dumps(clf)
```

### <a name="2-create-a-schemajson-file"></a>2. Creare un file schema.json
Questo passaggio è facoltativo. 

Creare uno schema per convalidare automaticamente l'input e output del servizio Web. Le interfacce della riga di comando usano lo schema anche per generare un documento Swagger per il servizio Web.

Per creare lo schema importare le librerie seguenti:

```python
from azureml.api.schema.dataTypes import DataTypes
from azureml.api.schema.sampleDefinition import SampleDefinition
from azureml.api.realtime.services import generate_schema
```
Successivamente, definire le variabili di input come un frame di dati di Spark, Pandas o una matrice NumPy. L'esempio seguente usa una matrice Numpy:

```python
inputs = {"input_array": SampleDefinition(DataTypes.NUMPY, yourinputarray)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```
L'esempio seguente usa un frame di dati Spark:

```python
inputs = {"input_df": SampleDefinition(DataTypes.SPARK, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```

L'esempio seguente usa un frame di dati PANDAS:

```python
inputs = {"input_df": SampleDefinition(DataTypes.PANDAS, yourinputdataframe)}
generate_schema(run_func=run, inputs=inputs, filepath='service_schema.json')
```

### <a name="3-create-a-scorepy-file"></a>3. Creare un file score.py
Fornire un file score.py, che carica il modello e restituisce il risultato delle stime usando il modello.

Il file deve includere due funzioni: init e esecuzione.

Aggiungere il codice seguente all'inizio del file score.py per abilitare la funzionalità di raccolta dati che consente di raccogliere dati di input e di stima del modello

```python
from azureml.datacollector import ModelDataCollector
```

Vedere la sezione [raccolta dei dati del modello](how-to-use-model-data-collection.md) per altre informazioni su come usare questa funzionalità.

#### <a name="init-function"></a>Funzione init
Usare la funzione init per caricare il modello salvato.

Esempio di una funzione init semplice per il caricamento del modello:

```python
def init():  
    from sklearn.externals import joblib
    global model, inputs_dc, prediction_dc
    model = joblib.load('model.pkl')

    inputs_dc = ModelDataCollector('model.pkl',identifier="inputs")
    prediction_dc = ModelDataCollector('model.pkl', identifier="prediction")
```

#### <a name="run-function"></a>Funzione di esecuzione
La funzione di esecuzione usa il modello e i dati di input per restituire una stima.

Esempio di una semplice funzione di esecuzione per l'elaborazione dell'input e la restituzione del risultato della stima:

```python
def run(input_df):
    global clf2, inputs_dc, prediction_dc
    try:
        prediction = model.predict(input_df)
        inputs_dc.collect(input_df)
        prediction_dc.collect(prediction)
        return prediction
    except Exception as e:
        return (str(e))
```

### <a name="4-register-a-model"></a>4. Registrare un modello
Il comando seguente viene usato per registrare un modello creato nel passaggio 1 precedente,

```
az ml model register --model [path to model file] --name [model name]
```

### <a name="5-create-manifest"></a>5. Creare il manifesto
Il comando seguente consente di creare un manifesto per il modello,

```
az ml manifest create --manifest-name [your new manifest name] -f [path to code file] -r [runtime for the image, e.g. spark-py]
```
È possibile aggiungere un modello registrato in precedenza al manifesto con l'argomento `--model-id` o `-i` nel comando illustrato in precedenza. È possibile specificare più modelli con altri argomenti -i.

### <a name="6-create-an-image"></a>6. Creare un'immagine 
È possibile creare un'immagine di cui prima è stato creato il relativo manifesto. 

```
az ml image create -n [image name] -manifest-id [the manifest ID]
```

Oppure è possibile creare il manifesto e l'immagine con un unico comando. 

```
az ml image create -n [image name] --model-file [model file or folder path] -f [code file, e.g. the score.py file] -r [the runtime eg.g. spark-py which is the Docker container image base]
```

>[!NOTE]
>Per altre informazioni sui parametri di comando digitare -h alla fine del comando, ad esempio az ml image create -h.


### <a name="7-create-and-deploy-the-web-service"></a>7. Creare e distribuire il servizio Web
Distribuire il servizio usando il comando seguente:

```
az ml service create realtime --image-id <image id> -n <service name>
```

>[!NOTE] 
>È anche possibile usare un singolo comando per eseguire i quattro passaggi precedenti. Usare -h con il comando di creazione del servizio per altri dettagli.

### <a name="8-test-the-service"></a>8. Testare il servizio
Usare il comando seguente per ottenere informazioni su come chiamare il servizio:

```
az ml service usage realtime -i <service id>
```

Chiamare il servizio usando la funzione di esecuzione dal prompt dei comandi:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [INPUT DATA]}"
```

L'esempio seguente chiama un servizio Web Iris di esempio:

```
az ml service run realtime -i <service id> -d "{\"input_df\": [{\"sepal length\": 3.0, \"sepal width\": 3.6, \"petal width\": 1.3, \"petal length\":0.25}]}"
```

## <a name="next-steps"></a>Passaggi successivi
Ora che il servizio Web è stato testato per l'esecuzione in locale è possibile distribuirlo a un cluster per l'uso su larga scala. Per informazioni dettagliate sull'impostazione di un cluster per la distribuzione dei servizi Web, vedere [Model Management Configuration](deployment-setup-configuration.md) (Configurazione della Gestione modelli). 
