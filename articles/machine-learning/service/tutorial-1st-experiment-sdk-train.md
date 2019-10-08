---
title: 'Esercitazione: Eseguire il training del primo modello di Machine Learning'
titleSuffix: Azure Machine Learning
description: In questa esercitazione si apprenderanno gli schemi progettuali di base in Azure Machine Learning e si eseguirà il training di un semplice modello scikit-learn basato sul set di dati diabetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.date: 09/03/2019
ms.openlocfilehash: c775b16eaa15ccd7115f4770bf197545a9de2500
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828024"
---
# <a name="tutorial-train-your-first-ml-model"></a>Esercitazione: Eseguire il training del primo modello di Machine Learning

Questa esercitazione è la **seconda di una serie in due parti**. Nell'esercitazione precedente [si è creata un'area di lavoro e si è scelto un ambiente di sviluppo](tutorial-1st-experiment-sdk-setup.md). In questa esercitazione si apprenderanno gli schemi progettuali di base in Azure Machine Learning e si eseguirà il training di un semplice modello scikit-learn basato sul set di dati diabetes. Al termine dell'esercitazione, si avrà la conoscenza pratica dell'SDK necessaria per passare allo sviluppo di esperimenti e flussi di lavoro più complessi.

In questa esercitazione si apprenderanno informazioni sulle attività seguenti:

> [!div class="checklist"]
> * Connettere l'area di lavoro e creare un esperimento
> * Caricare i dati ed eseguire il training di modelli scikit-learn
> * Visualizzare i risultati del training nel portale
> * Recuperare il modello migliore

## <a name="prerequisites"></a>Prerequisiti

L'unico prerequisito consiste nell'eseguire la prima parte di questa esercitazione, [Configurare l'ambiente e l'area di lavoro](tutorial-1st-experiment-sdk-setup.md).

In questa parte dell'esercitazione si eseguirà il codice nel notebook di Jupyter `tutorials/tutorial-1st-experiment-sdk-train.ipynb` di esempio aperto alla fine della prima parte. Questo articolo illustra lo stesso codice presente nel notebook.

## <a name="open-the-notebook"></a>Aprire il notebook

1. Effettuare l'accesso alla [pagina di destinazione dell'area di lavoro](https://ml.azure.com/).

1. Aprire il file **tutorial-1st-experiment-sdk-train.ipynb** nella cartella, come illustrato nella [prima parte](tutorial-1st-experiment-sdk-setup.md#open).


> [!Warning]
> **Non** creare un *nuovo* notebook nell'interfaccia di Jupyter. Il notebook `tutorials/tutorial-1st-experiment-sdk-train.ipynb` include **tutto il codice e i dati necessari** per questa esercitazione.

## <a name="connect-workspace-and-create-experiment"></a>Connettere l'area di lavoro e creare un esperimento

> [!Important]
> Il resto di questo articolo contiene lo stesso contenuto visualizzato nel notebook.  
>
> Passare ora al notebook di Jupyter se si desidera leggere durante l'esecuzione del codice. 
> Per eseguire una singola cella di codice in un notebook, fare clic sulla cella di codice e premere **MAIUSC + INVIO**. In alternativa, eseguire l'intero notebook scegliendo **Esegui tutto** dalla barra degli strumenti superiore.

Importare la classe `Workspace` e caricare le informazioni sulla sottoscrizione dal file `config.json` con la funzione `from_config().`. Per impostazione predefinita, questa funzione cerca il file JSON nella directory corrente, ma è possibile anche specificare un parametro del percorso che punti al file usando `from_config(path="your/file/path")`. In un server notebook cloud, il file si trova automaticamente nella directory radice.

Se il codice seguente richiede un'autenticazione aggiuntiva, incollare semplicemente il collegamento in un browser e immettere il token di autenticazione.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Creare quindi un esperimento nell'area di lavoro. Un esperimento è un'altra risorsa cloud fondamentale che rappresenta una raccolta di prove, ossia singole esecuzioni del modello. In questa esercitazione si userà l'esperimento per creare le esecuzioni e tenere traccia del training del modello nel portale di Azure. I parametri includono il riferimento all'area di lavoro e la stringa del nome dell'esperimento.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Caricare i dati e preparare il training

Per questa esercitazione si userà il set di dati diabetes, un set di dati pre-normalizzato incluso in scikit-learn. Questo set di dati usa caratteristiche come età, sesso e BMI per prevedere lo stato di avanzamento del diabete. Caricare i dati dalla funzione statica `load_diabetes()` e dividerli in set di training e di test con `train_test_split()`. Questa funzione separa i dati affinché il modello contenga dati non visibili da usare per i test dopo il training.


```python
from sklearn.datasets import load_diabetes
from sklearn.model_selection import train_test_split

X, y = load_diabetes(return_X_y = True)
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Eseguire il training di un modello

Il training di un modello scikit-learn può essere facilmente eseguito in locale se di scala ridotta, ma in caso di training di molte iterazioni con decine di permutazioni di caratteristiche e impostazioni di iperparametri diversi è facile perdere traccia dei modelli sottoposti a training e di come il training è stato eseguito. Lo schema progettuale seguente illustra come sfruttare l'SDK per tenere facilmente traccia del training nel cloud.

Creare uno script che esegue il training di modelli ridge in un ciclo con diversi valori alfa di iperparametri.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)

    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)

    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name

    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

Il codice precedente effettua le operazioni seguenti:

1. Per ogni valore alfa di iperparametro nella matrice `alphas` viene creata una nuova esecuzione all'interno dell'esperimento. Il valore alfa viene registrato per distinguere ogni singola esecuzione.
1. In ogni esecuzione viene creata un'istanza di un modello ridge che viene sottoposto a training e usato per eseguire previsioni. Viene calcolata la radice dell'errore quadratico medio per i valori effettivi rispetto a quelli previsti, che viene quindi registrata nell'esecuzione. A questo punto, all'esecuzione sono associati metadati sia per il valore alfa sia per l'accuratezza della radice dell'errore quadratico medio.
1. Il modello per ogni esecuzione viene quindi serializzato e caricato nell'esecuzione. In questo modo è possibile scaricare il file del modello dall'esecuzione nel portale.
1. Alla fine di ogni iterazione, l'esecuzione viene completata chiamando `run.complete()`.

Al termine del training, chiamare la variabile `experiment` per recuperare un collegamento all'esperimento nel portale.

```python
experiment
```

<table style="width:100%"><tr><th>NOME</th><th>Area di lavoro</th><th>Pagina report</th><th>Pagina documentazione</th></tr><tr><td>diabetes-experiment</td><td>nome-area-lavoro</td><td>Collegamento al portale di Azure</td><td>Collegamento alla documentazione</td></tr></table>

## <a name="view-training-results-in-portal"></a>Visualizzare i risultati del training nel portale

Seguendo il **collegamento al portale di Azure** si passa alla pagina principale dell'esperimento, in cui vengono visualizzate tutte le singole esecuzioni dell'esperimento. Tutti i valori per cui è stata personalizzata la registrazione (in questo caso, `alpha_value` e `rmse`) diventano campi per ogni esecuzione e sono disponibili anche per i grafici e i riquadri nella parte superiore della pagina dell'esperimento. Per aggiungere una metrica registrata a un grafico o un riquadro, passare il puntatore del mouse su di esso, fare clic sul pulsante di modifica e trovare la metrica per cui è stata personalizzata la registrazione.

In caso di training di modelli su vasta scala, con centinaia e migliaia di esecuzioni separate, questa pagina semplifica la visualizzazione di ogni modello sottoposto a training, in particolare di come è stato eseguito il training e di come le metriche univoche usate sono cambiate nel tempo.

![Pagina principale dell'esperimento nel portale](./media/tutorial-quickstart/experiment-main.png)

Facendo clic sul collegamento con il numero di un'esecuzione nella colonna `RUN NUMBER` si passa alla pagina relativa a ogni singola esecuzione. La scheda **Dettagli** predefinita mostra informazioni più dettagliate su ogni esecuzione. Passare alla scheda **Output** per visualizzare il file con estensione `.pkl` per il modello che è stato caricato nell'esecuzione durante ogni iterazione del training. In questa scheda è possibile scaricare il file del modello, per non doverne ripetere manualmente il training.

![Pagina dei dettagli dell'esecuzione nel portale](./media/tutorial-quickstart/model-download.png)

## <a name="get-the-best-model"></a>Recuperare il modello migliore

Oltre che dall'esperimento nel portale, è possibile scaricare i file di modello anche a livello di codice. Il codice seguente scorre ogni esecuzione nell'esperimento e accede sia alle metriche registrate che ai dettagli (contenenti run_id) dell'esecuzione. Viene così tenuta traccia dell'esecuzione migliore, in questo caso l'esecuzione con la radice dell'errore quadratico medio più bassa.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]

    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))
```

    Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
    Best run_id rmse: 57.234760283951765

Usare l'ID dell'esecuzione migliore per recuperare la singola esecuzione con il costruttore `Run` e l'oggetto esperimento. Chiamare quindi `get_file_names()` per visualizzare tutti i file disponibili per il download da questa esecuzione. In questo caso, durante il training è stato caricato un solo file per ogni esecuzione.

```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

    ['model_alpha_0.1.pkl']

Chiamare `download()` sull'oggetto esecuzione, specificando il nome del file del modello da scaricare. Per impostazione predefinita, questa funzione esegue il download nella directory corrente.

```python
best_run.download_file(name="model_alpha_0.1.pkl")
```

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende eseguire altre esercitazioni su Azure Machine Learning, non completare questa sezione.

### <a name="stop-the-notebook-vm"></a>Arrestare la macchina virtuale per notebook

Se si è usato un server notebook cloud, per ridurre i costi arrestare la macchina virtuale per notebook quando non viene usata.

1. Nell'area di lavoro selezionare **Macchine virtuali per notebook**.

   ![Arrestare il server di macchine virtuali](./media/tutorial-1st-experiment-sdk-setup/stop-server.png)

1. Selezionare la macchina virtuale dall'elenco.

1. Selezionare **Arresta**.

1. Quando si è pronti a usare di nuovo il server, selezionare **Avvia**.

### <a name="delete-everything"></a>Eliminare tutto

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

È anche possibile mantenere il gruppo di risorse ma eliminare una singola area di lavoro. Visualizzare le proprietà dell'area di lavoro e selezionare **Elimina**.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione sono state completate le attività seguenti:

> [!div class="checklist"]
> * Connessione dell'area di lavoro e creazione di un esperimento
> * Caricamento dei dati e training di modelli scikit-learn
> * Visualizzazione dei risultati del training nel portale e recupero dei modelli

[Distribuire il modello](tutorial-deploy-models-with-aml.md) con Azure Machine Learning.
Scoprire come sviluppare esperimenti di [Machine Learning automatizzato](tutorial-auto-train-models.md).
