---
title: Convertire il codice del notebook in script Python
titleSuffix: Azure Machine Learning
description: Trasformare i notebook sperimentali di Machine Learning in codice pronto per la produzione usando il modello di codice MLOpsPython. È quindi possibile testare, distribuire e automatizzare il codice.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 04/30/2020
ms.openlocfilehash: a0b66f233de9e1bfdc6d011b65489884a1049a12
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82559669"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Esercitazione: Convertire gli esperimenti di ML in codice Python di produzione

In questa esercitazione viene illustrato come convertire Jupyter Notebook in script Python per rendere intuitivi il test e l'automazione usando il modello di codice MLOpsPython e Azure Machine Learning. Questo processo viene in genere usato per acquisire il codice di sperimentazione/training da Juptyer Notebook e convertirlo in script Python. Tali script possono quindi essere usati per il testing e l'automazione CI/CD nell'ambiente di produzione. 

Per un progetto di Machine Learning, è necessaria la sperimentazione, in cui le ipotesi vengono verificate con strumenti agili come Jupyter Notebook usando set di dati reali. Quando il modello è pronto per la produzione, il codice del modello deve essere inserito in un repository di codice di produzione. In alcuni casi, il codice del modello deve essere convertito in script Python da inserire nel repository del codice di produzione. Questa esercitazione illustra un approccio consigliato per esportare il codice di sperimentazione negli script Python.

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Pulire il codice non essenziale
> * Effettuare il refactoring del codice di Jupyter Notebook in funzioni
> * Creare script Python per attività correlate
> * Creare unit test

## <a name="prerequisites"></a>Prerequisiti

- Generare il [modello MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) e usare i notebook `experimentation/Diabetes Ridge Regression Training.ipynb` e `experimentation/Diabetes Ridge Regression Scoring.ipynb`. Questi notebook vengono usati come esempio di conversione dalla sperimentazione alla produzione. Questi notebook sono disponibili all'indirizzo [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation).
- Installare `nbconvert`. Seguire solo le istruzioni di installazione nella sezione __Installazione di nbconvert__ nella pagina [Installazione](https://nbconvert.readthedocs.io/en/latest/install.html).

## <a name="remove-all-nonessential-code"></a>Rimuovere tutto il codice non essenziale

Parte del codice scritto durante la sperimentazione è destinata solo a scopi esplorativi. Pertanto, il primo passaggio per convertire il codice sperimentale in codice di produzione consiste nel rimuovere questo codice non essenziale. La rimozione del codice non essenziale renderà anche il codice più gestibile. In questa sezione verrà rimosso il codice dal notebook `experimentation/Diabetes Ridge Regression Training.ipynb`. Le istruzioni che definiscono la forma di `X` e `y` e la cella che chiama `features.describe` servono solo per l'esplorazione dei dati e possono essere rimosse. Dopo aver rimosso il codice non essenziale, `experimentation/Diabetes Ridge Regression Training.ipynb` dovrà essere simile al codice seguente senza markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Effettuare il refactoring del codice in funzioni

In secondo luogo, è necessario effettuare il refactoring del codice di Jupyter in funzioni. Il refactoring del codice in funzioni semplifica gli unit test e rende il codice più gestibile. In questa sezione si effettuerà il refactoring di quanto segue:

- Il notebook Diabetes Ridge Regression Training (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Il notebook Diabetes Ridge Regression Scoring (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Effettuare il refactoring del notebook Diabetes Ridge Regression Training in funzioni

In `experimentation/Diabetes Ridge Regression Training.ipynb` completare i passaggi seguenti:

1. Creare una funzione denominata `split_data` per dividere il frame di dati in dati di test e di training. La funzione dovrà accettare il frame di dati `df` come parametro e restituire un dizionario contenente le chiavi `train` e `test`.

    Spostare il codice sotto l'intestazione *Split Data into Training and Validation Sets* nella funzione `split_data` e modificarlo affinché restituisca l'oggetto `data`.

1. Creare una funzione denominata `train_model` che accetta i parametri `data` e `args` e restituisce un modello con training.

    Spostare il codice sotto l'intestazione *Train Model on Training Set* nella funzione `train_model` e modificarlo affinché restituisca l'oggetto `reg_model`. Rimuovere il dizionario `args`. I valori proverranno dal parametro `args`.

1. Creare una funzione denominata `get_model_metrics` che accetta i parametri `reg_model` e `data`, valuta il modello e quindi restituisce un dizionario di metriche per il modello con training.

    Spostare il codice sotto l'intestazione *Validate Model on Validation Set* nella funzione `get_model_metrics` e modificarlo affinché restituisca l'oggetto `metrics`.

Le tre funzioni si presenteranno come segue:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

Sempre in `experimentation/Diabetes Ridge Regression Training.ipynb` seguire questa procedura:

1. Creare una funzione denominata `main`, che non accetta parametri e non restituisce nulla.
1. Spostare il codice sotto l'intestazione "Load Data" nella funzione `main`.
1. Aggiungere le chiamate per le funzioni appena scritte nella funzione `main`:
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Spostare il codice sotto l'intestazione "Save Model" nella funzione `main`.

La funzione `main` dovrà essere simile al codice seguente:

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

A questo punto, nel notebook non dovrebbe essere rimasto codice non incluso in una funzione ad eccezione delle istruzioni import nella prima cella.

Aggiungere un'istruzione che chiama la funzione `main`.

```python
main()
```

Dopo il refactoring, `experimentation/Diabetes Ridge Regression Training.ipynb` dovrà essere simile al codice seguente senza il markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Effettuare il refactoring del notebook Diabetes Ridge Regression Scoring in funzioni

In `experimentation/Diabetes Ridge Regression Scoring.ipynb` completare i passaggi seguenti:

1. Creare una funzione denominata `init`, che non accetta parametri e non restituisce nulla.
1. Copiare il codice sotto l'intestazione "Load Model" nella funzione `init`.

La funzione `init` dovrà essere simile al codice seguente:

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

Una volta creata la funzione `init`, sostituire tutto il codice sotto l'intestazione "Load Model" con una singola chiamata a `init`, come indicato di seguito:

```python
init()
```

In `experimentation/Diabetes Ridge Regression Scoring.ipynb` completare i passaggi seguenti:

1. Creare una nuova funzione denominata `run`, che accetta `raw_data` e `request_headers` come parametri e restituisce un dizionario dei risultati come segue:

    ```python
    {"result": result.tolist()}
    ```

1. Copiare il codice sotto le intestazioni "Prepare Data" e "Score Data" nella funzione `run`.

    La funzione `run` dovrà essere simile al codice seguente (ricordarsi di rimuovere le istruzioni che impostano le variabili `raw_data` e `request_headers`, che verranno usate più avanti quando verrà chiamata la funzione `run`):

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Una volta creata la funzione `run`, sostituire tutto il codice sotto le intestazioni "Prepare Data" e "Score Data" con il codice seguente:

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Il codice precedente imposta le variabili `raw_data` e `request_header`, chiama la funzione `run` con `raw_data` e `request_header` e stampa le previsioni.

Dopo il refactoring, `experimentation/Diabetes Ridge Regression Scoring.ipynb` dovrà essere simile al codice seguente senza il markdown:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Combinare funzioni correlate nei file Python

Il terzo passaggio consiste nell'unire le funzioni correlate in file Python per semplificare il riutilizzo del codice. In questa sezione verranno creati i file Python per i notebook seguenti:

- Il notebook Diabetes Ridge Regression Training (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Il notebook Diabetes Ridge Regression Scoring (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Creare il file Python per il notebook Diabetes Ridge Regression Training

Convertire il notebook in uno script eseguibile eseguendo questa istruzione al prompt dei comandi, che usa il pacchetto `nbconvert` e il percorso `experimentation/Diabetes Ridge Regression Training.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Dopo la conversione del notebook in `train.py`, rimuovere i commenti indesiderati. Sostituire la chiamata a `main()` alla fine del file con una chiamata condizionale come il codice seguente:

```python
if __name__ == '__main__':
    main()
```

Il file `train.py` dovrà essere simile al codice seguente:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

È ora possibile richiamare `train.py` da un terminale eseguendo `python train.py`.
Le funzioni di `train.py` possono essere chiamate anche da altri file.

Il file `train_aml.py` che si trova nella directory `diabetes_regression/training` nel repository MLOpsPython chiama le funzioni definite in `train.py` nel contesto dell'esecuzione di un esperimento di Azure Machine Learning. Le funzioni possono essere chiamate anche in unit test, come illustrato più avanti in questa guida.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Creare il file Python per il notebook Diabetes Ridge Regression Scoring

Convertire il notebook in uno script eseguibile eseguendo questa istruzione al prompt dei comandi, che usa il pacchetto `nbconvert` e il percorso `experimentation/Diabetes Ridge Regression Scoring.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Dopo la conversione del notebook in `score.py`, rimuovere i commenti indesiderati. Il file `score.py` dovrà essere simile al codice seguente:

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

La variabile `model` deve essere globale per essere visibile in tutto lo script. Aggiungere l'istruzione seguente all'inizio della funzione `init`:

```python
global model
```

Dopo aver aggiunto l'istruzione precedente, la funzione `init` dovrà essere simile al codice seguente:

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Creare unit test per ogni file Python

Come quarto passaggio, creare unit test per le funzioni Python. Gli unit test proteggono il codice da regressioni funzionali e ne semplificano la manutenzione. In questa sezione si creeranno unit test per le funzioni in `train.py`.

`train.py` contiene più funzioni, ma in questa esercitazione si creerà un solo unit test per la funzione `train_model` usando il framework Pytest. Pytest non è l'unico framework disponibile per unit test Python, ma è uno dei più usati. Per altre informazioni, visitare [Pytest](https://pytest.org).

Un unit test in genere contiene tre azioni principali:

- Arrange: creare e configurare gli oggetti necessari
- Act: agire su un oggetto
- Assert: dichiarare cosa è previsto

Lo unit test chiamerà `train_model` con alcuni argomenti e dati hardcoded e verificherà che `train_model` abbia funzionato come previsto usando il modello con training risultante per eseguire una previsione e confrontarla con un valore previsto.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come eseguire la conversione da un esperimento al codice di produzione, vedere i collegamenti seguenti per altre informazioni e i passaggi successivi:

+ [MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md): creare una pipeline CI/CD per eseguire il training, la valutazione e la distribuzione di un modello usando Azure Pipelines e Azure Machine Learning
+ [Monitorare le esecuzioni e le metriche degli esperimenti di Azure ML](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
+ [Monitorare e raccogliere dati da endpoint servizio Web di ML](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
