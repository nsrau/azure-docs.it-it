---
title: Convertire il codice dell'esperimento di Machine Learning in codice di produzione
titleSuffix: Azure Machine Learning
description: Informazioni su come convertire il codice sperimentale di Machine Learning in codice di produzione usando il modello di codice MLOpsPython.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: b929dd8979ecf587872092af2fa44e5ae03626d1
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472486"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Esercitazione: Convertire il codice sperimentale di ML in codice di produzione

Per un progetto di Machine Learning, è necessaria la sperimentazione, in cui le ipotesi vengono verificate con strumenti agili come Jupyter Notebook usando set di dati reali. Quando il modello è pronto per la produzione, il codice del modello deve essere inserito in un repository di codice di produzione. In alcuni casi, il codice del modello deve essere convertito in script Python da inserire nel repository del codice di produzione. Questa esercitazione illustra un approccio consigliato per esportare il codice di sperimentazione negli script Python.  

In questa esercitazione verranno illustrate le procedure per:

> [!div class="checklist"]
> * Pulire il codice non essenziale
> * Effettuare il refactoring del codice di Jupyter Notebook in funzioni
> * Creare script Python per attività correlate
> * Creare unit test

## <a name="prerequisites"></a>Prerequisites

- Generare il [modello MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) e usare i notebook `experimentation/Diabetes Ridge Regression Training.ipynb` e `experimentation/Diabetes Ridge Regression Scoring.ipynb`. Questi notebook vengono usati come esempio di conversione dalla sperimentazione alla produzione.
- Installare nbconvert. Seguire solo le istruzioni di installazione nella sezione __Installazione di nbconvert__ nella pagina [Installazione](https://nbconvert.readthedocs.io/en/latest/install.html).

## <a name="remove-all-nonessential-code"></a>Rimuovere tutto il codice non essenziale

Parte del codice scritto durante la sperimentazione è destinata solo a scopi esplorativi. Pertanto, il primo passaggio per convertire il codice sperimentale in codice di produzione consiste nel rimuovere questo codice non essenziale. La rimozione del codice non essenziale renderà anche il codice più gestibile. In questa sezione verrà rimosso il codice dal notebook `experimentation/Diabetes Ridge Regression Training.ipynb`. Le istruzioni che definiscono la forma di `X` e `y` e la cella che chiama `features.describe` servono solo per l'esplorazione dei dati e possono essere rimosse. Dopo aver rimosso il codice non essenziale, `experimentation/Diabetes Ridge Regression Training.ipynb` dovrà essere simile al codice seguente senza markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
 
X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Effettuare il refactoring del codice in funzioni

In secondo luogo, è necessario effettuare il refactoring del codice di Jupyter in funzioni. Il refactoring del codice in funzioni semplifica gli unit test e rende il codice più gestibile. In questa sezione si effettuerà il refactoring di quanto segue:
- Il notebook Diabetes Ridge Regression Training (`experimentation/Diabetes Ridge Regression Training.ipynb`)
- Il notebook Diabetes Ridge Regression Scoring (`experimentation/Diabetes Ridge Regression Scoring.ipynb`)

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Effettuare il refactoring del notebook Diabetes Ridge Regression Training in funzioni
In `experimentation/Diabetes Ridge Regression Training.ipynb` completare i passaggi seguenti:

1. Creare una funzione denominata `train_model`, che accetta i parametri `data` e `alpha` e restituisce un modello. 
1. Copiare il codice sotto le intestazioni "Train Model on Training Set" e "Validate Model on Validation Set" nella funzione `train_model`.

La funzione `train_model` dovrà essere simile al codice seguente:

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

Una volta creata la funzione `train_model`, sostituire il codice sotto le intestazioni "Train Model on Training Set" e "Validate Model on Validation Set" con l'istruzione seguente:

```python
reg = train_model(data, alpha)
```

L'istruzione precedente chiama la funzione `train_model` passando i parametri `data` e `alpha` e restituisce il modello.

In `experimentation/Diabetes Ridge Regression Training.ipynb` completare i passaggi seguenti:

1. Creare una funzione denominata `main`, che non accetta parametri e non restituisce nulla.
1. Copiare il codice sotto le intestazioni "Load Data", "Split Data into Training and Validation Sets" e "Save Model" nella funzione `main`.
1. Copiare la chiamata appena creata a `train_model` nella funzione `main`.

La funzione `main` dovrà essere simile al codice seguente:

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

Una volta creata la funzione `main`, sostituire tutto il codice sotto le intestazioni "Load data", "Split Data in Training and Validation Sets" e "Save Model" insieme alla chiamata appena creata a `train_model` con l'istruzione seguente:

```python
main()
```

Dopo il refactoring, `experimentation/Diabetes Ridge Regression Training.ipynb` dovrà essere simile al codice seguente senza il markdown:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

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
Convertire il notebook in uno script eseguibile eseguendo l'istruzione seguente in un prompt dei comandi, che usa il pacchetto nbconvert e il percorso `experimentation/Diabetes Ridge Regression Training.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

Dopo aver convertito il notebook in `train.py`, rimuovere tutti i commenti. Il file `train.py` dovrà essere simile al codice seguente:

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5
    
    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

Il file `train.py` disponibile nella directory `diabetes_regression/training` nel repository MLOpsPython supporta gli argomenti della riga di comando (in particolare `build_id`, `model_name` e `alpha`). Il supporto per gli argomenti della riga di comando può essere aggiunto al file `train.py` per supportare i nomi di modelli dinamici e i valori `alpha`, ma non è necessario per la corretta esecuzione del codice.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Creare il file Python per il notebook Diabetes Ridge Regression Scoring
Convertire il notebook in uno script eseguibile eseguendo l'istruzione seguente in un prompt dei comandi, che usa il pacchetto nbconvert e il percorso `experimentation/Diabetes Ridge Regression Scoring.ipynb`:

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

Dopo aver convertito il notebook in `score.py`, rimuovere tutti i commenti. Il file `score.py` dovrà essere simile al codice seguente:

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

È necessario apportare modifiche alla funzione `train_model` per creare un'istanza di un modello di variabile globale in modo che sia visibile in tutto lo script. Aggiungere l'istruzione seguente all'inizio della funzione `init`:

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
Il quarto passaggio consiste nel creare unit test per ogni file Python, rendendo il codice più affidabile e più gestibile. In questa sezione verrà creato un unit test per una delle funzioni di `train.py`.

`train.py` contiene due funzioni, `train_model` e `main`. Ogni funzione necessita di un unit test, ma in questa esercitazione ne verrà creato uno solo per la funzione `train_model` usando il framework Pytest.  Pytest non è l'unico framework disponibile per unit test Python, ma è uno dei più usati. Per altre informazioni, visitare [Pytest](https://pytest.org).

Un unit test in genere contiene tre azioni principali:
- Arrange: creare e configurare gli oggetti necessari
- Act: agire su un oggetto
- Assert: dichiarare cosa è previsto

Una condizione comune per `train_model` è quando vengono passati `data` e un valore `alpha`. Il risultato previsto è che devono essere chiamate le funzioni `Ridge.train` e `Ridge.predict`. Poiché i metodi di training di Machine Learning non sono in genere veloci da eseguire, la chiamata a `Ridge.train` verrà simulata. Considerando che il valore restituito da `Ridge.train` è un oggetto simulato, verrà simulato anche `Ridge.predict`. L'unit test per `train_model`, che verifica il passaggio di `data` e di un valore `alpha` con il risultato previsto delle funzioni `Ridge.train` e `Ridge.predict` chiamate tramite simulazione e il framework Pytest, dovrà essere simile al codice seguente:

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch(Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Usare un modello personalizzato con il modello di codice MLOpsPython
Se sono stati seguiti i passaggi descritti in questa guida, si avrà un set di script correlati agli script di training/assegnazione di punteggi/test disponibili nel repository MLOpsPython.  In base alla struttura indicata sopra, di seguito vengono illustrati i passaggi necessari per usare questi file per un progetto personalizzato di Machine Learning:  

1.  Seguire la Guida introduttiva
2.  Sostituire il codice di training
3.  Sostituire il codice di assegnazione di punteggi
4.  Aggiornare il codice di valutazione

### <a name="follow-the-getting-started-guide"></a>Seguire la Guida introduttiva
È necessario seguire la Guida introduttiva per avere l'infrastruttura e le pipeline di supporto per l'esecuzione di MLOpsPython.  È consigliabile distribuire il codice MLOpsPython così com'è prima di inserire il codice personalizzato per assicurarsi che la struttura e la pipeline funzionino correttamente.  È inoltre utile acquisire familiarità con la struttura del codice del repository.

### <a name="replace-training-code"></a>Sostituire il codice di training
La sostituzione del codice usato per eseguire il training del modello e la rimozione o la sostituzione degli unit test corrispondenti sono operazioni necessarie per il funzionamento della soluzione con codice personalizzato.  Seguire in particolare questi passaggi:

1. Sostituire `diabetes_regression\training\train.py`. Questo script esegue il training del modello in locale o nella risorsa di calcolo di Azure ML.
1. Rimuovere o sostituire gli unit test di training disponibili in `tests/unit/code_test.py`

### <a name="replace-score-code"></a>Sostituire il codice di assegnazione di punteggi
Affinché il modello fornisca funzionalità di inferenza in tempo reale, è necessario sostituire il codice di assegnazione di punteggi. Il modello MLOpsPython usa questo codice per distribuire il modello in modo da assegnare punteggi in tempo reale in Istanze di Azure Container, nel servizio Azure Kubernetes o nelle app Web.  Se si vuole mantenere l'assegnazione di punteggi, sostituire `diabetes_regression/scoring/score.py`.

### <a name="update-evaluation-code"></a>Aggiornare il codice di valutazione
Il modello MLOpsPython usa lo script evaluate_model per confrontare le prestazioni del modello appena sottoposto a training con il modello di produzione corrente in base a un errore quadratico medio. Se le prestazioni del modello appena sottoposto a training sono migliori rispetto a quelle del modello di produzione corrente, le pipeline continuano. In caso contrario, le pipeline vengono arrestate. Per mantenere la valutazione, sostituire tutte le istanze di `mse` in `diabetes_regression/evaluate/evaluate_model.py` con la metrica desiderata. 

Per eliminare la valutazione, impostare la variabile della pipeline DevOps `RUN_EVALUATION` in `.pipelines\diabetes_regression-variables` su `false`.

## <a name="next-steps"></a>Passaggi successivi

Ora che si è appreso come eseguire la conversione da un esperimento al codice di produzione, usare i collegamenti seguenti per informazioni su come monitorare le esecuzioni degli esperimenti e i modelli distribuiti come servizi Web:

> [!div class="nextstepaction"]
> [Monitorare le esecuzioni e le metriche di esperimenti di Azure ML](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [Monitorare e raccogliere i dati dagli endpoint del servizio Web ML](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
