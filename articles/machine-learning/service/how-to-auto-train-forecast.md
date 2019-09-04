---
title: Eseguire il training automatico di un modello di previsione delle serie temporali
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il servizio Azure Machine Learning per eseguire il training di un modello di regressione di previsione di serie temporali usando Machine Learning automatizzato.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.service: machine-learning
ms.subservice: core
ms.reviewer: trbye
ms.topic: conceptual
ms.date: 06/20/2019
ms.openlocfilehash: 2a037a495a1e1ed211bd9a535891ccf75fdb140b
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70278188"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Eseguire il training automatico di un modello di previsione delle serie temporali

Questo articolo illustra come eseguire il training di un modello di regressione delle previsioni di serie temporali usando Machine Learning automatizzato nel servizio Azure Machine Learning. La configurazione di un modello di previsione è simile alla configurazione di un modello di regressione standard usando Machine Learning automatizzato, ma sono disponibili alcune opzioni di configurazione e passaggi di pre-elaborazione per l'uso di dati di serie temporali. Gli esempi seguenti illustrano come:

* Preparare i dati per la modellazione delle serie temporali
* Configurare specifici parametri della serie temporale in un [`AutoMLConfig`](/python/api/azureml-train-automl/azureml.train.automl.automlconfig) oggetto
* Eseguire stime con dati di serie temporali

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

È possibile utilizzare Machine Learning Machine Learning per combinare tecniche e approcci e ottenere una previsione della serie temporale consigliata e di alta qualità. Un esperimento di serie temporali automatizzato viene considerato un problema di regressione MultiVariante. I valori delle serie temporali precedenti sono "trasformati tramite pivot" per diventare dimensioni aggiuntive per il regressore insieme ad altri predittori.

Questo approccio, a differenza dei metodi classici della serie temporale, ha il vantaggio di incorporare naturalmente più variabili contestuali e la relazione tra loro durante il training. Nelle applicazioni di previsione reali, più fattori possono influenzare una previsione. Ad esempio, quando si prevedono le vendite, le interazioni delle tendenze cronologiche, il tasso di cambio e il prezzo comportano congiuntamente il risultato delle vendite. Un ulteriore vantaggio è che tutte le innovazioni recenti nei modelli di regressione si applicano immediatamente alla previsione.

È possibile [configurare](#config) il modo in cui la previsione dovrebbe estendersi (orizzonte di previsione), nonché i ritardi e altro ancora. Machine Learning Machine Learning apprende un singolo modello, ma spesso con rami internamente, per tutti gli elementi del set di dati e gli orizzonti di stima. Sono pertanto disponibili più dati per stimare i parametri del modello e la generalizzazione per la serie non visibile diventa possibile.

Le funzionalità estratte dai dati di training svolgono un ruolo fondamentale. Inoltre, Automated Machine Learning esegue i passaggi di pre-elaborazione standard e genera ulteriori funzionalità della serie temporale per acquisire gli effetti stagionali e ottimizzare la precisione predittiva.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro del servizio Azure Machine Learning. Per creare l'area di lavoro, vedere [creare un'area di lavoro del servizio Azure Machine Learning](how-to-manage-workspace.md).
* Questo articolo presuppone una conoscenza di base della configurazione di un esperimento di Machine Learning automatizzato. Seguire l' [esercitazione](tutorial-auto-train-models.md) o le [procedure](how-to-configure-auto-train.md) per visualizzare i modelli di progettazione degli esperimenti automatici di base di machine learning.

## <a name="preparing-data"></a>Preparazione dei dati

La differenza più importante tra un tipo di attività di regressione previsione e un tipo di attività di regressione all'interno di Machine Learning automatizzato consiste nell'includere una funzionalità nei dati che rappresenta una serie temporale valida. Una serie temporale regolare presenta una frequenza ben definita e coerente e presenta un valore in ogni punto di esempio in un intervallo di tempo continuo. Si consideri lo snapshot seguente `sample.csv`di un file.

    day_datetime,store,sales_quantity,week_of_year
    9/3/2018,A,2000,36
    9/3/2018,B,600,36
    9/4/2018,A,2300,36
    9/4/2018,B,550,36
    9/5/2018,A,2100,36
    9/5/2018,B,650,36
    9/6/2018,A,2400,36
    9/6/2018,B,700,36
    9/7/2018,A,2450,36
    9/7/2018,B,650,36

Questo set di dati è un semplice esempio di dati di vendita giornalieri per una società che dispone di due archivi diversi, a e B. Inoltre, esiste `week_of_year` una funzionalità per che consentirà al modello di rilevare la stagionalità settimanale. Il campo `day_datetime` rappresenta una serie temporale pulita con frequenza giornaliera e il campo `sales_quantity` è la colonna di destinazione per l'esecuzione delle stime. Leggere i dati in un frame di dati Pandas, quindi `to_datetime` usare la funzione per assicurarsi che la serie `datetime` temporale sia un tipo.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

In questo caso i dati sono già ordinati in ordine crescente in base al `day_datetime`campo orario. Tuttavia, quando si configura un esperimento, verificare che la colonna di tempo desiderata venga ordinata in ordine crescente per compilare una serie temporale valida. Si supponga che i dati contengano 1.000 record e creino una suddivisione deterministica nei dati per creare set di dati di training e di test. Quindi separare il campo `sales_quantity` di destinazione per creare il training e i set di test.

```python
X_train = data.iloc[:950]
X_test = data.iloc[-50:]

y_train = X_train.pop("sales_quantity").values
y_test = X_test.pop("sales_quantity").values
```

> [!NOTE]
> Quando si esegue il training di un modello per la previsione dei valori futuri, assicurarsi che tutte le funzionalità usate nel training possano essere usate quando si eseguono stime per l'orizzonte previsto. Ad esempio, durante la creazione di una previsione della domanda, inclusa una funzionalità per il prezzo azionario corrente, potrebbe aumentare notevolmente la precisione di training. Tuttavia, se si intende prevedere con un orizzonte lungo, potrebbe non essere possibile stimare in modo accurato i valori azionari futuri corrispondenti ai punti di serie temporali futuri e l'accuratezza del modello potrebbe soffrire.

<a name="config"></a>
## <a name="configure-and-run-experiment"></a>Configurare ed eseguire l'esperimento

Per le attività di previsione, Machine Learning automatizzato USA operazioni di pre-elaborazione e di stima specifiche per i dati delle serie temporali. Verranno eseguiti i seguenti passaggi di pre-elaborazione:

* Rilevare la frequenza di campionamento della serie temporale (ad esempio, ogni ora, ogni giorno, ogni settimana) e creare nuovi record per i punti di tempo mancanti per rendere la serie continua.
* Imputare i valori mancanti nell'oggetto di destinazione (tramite il caricamento in diretta) e nelle colonne della funzionalità (usando i valori della colonna mediana)
* Creazione di funzionalità basate su granularità per consentire effetti fissi su diverse serie
* Creazione di funzionalità basate sul tempo per facilitare l'apprendimento di modelli stagionali
* Codificare variabili categoriche in quantità numeriche

L' `AutoMLConfig` oggetto definisce le impostazioni e i dati necessari per un'attività automatica di machine learning. Analogamente a un problema di regressione, si definiscono parametri di training standard come il tipo di attività, il numero di iterazioni, i dati di training e il numero di convalide incrociate. Per le attività di previsione, è necessario impostare parametri aggiuntivi che interessano l'esperimento. La tabella seguente illustra ogni parametro e il relativo utilizzo.

| Param | Descrizione | Obbligatoria |
|-------|-------|-------|
|`time_column_name`|Utilizzato per specificare la colonna DateTime nei dati di input utilizzati per compilare la serie temporale e dedurre la relativa frequenza.|✓|
|`grain_column_names`|Nome/i che definisce i singoli gruppi di serie nei dati di input. Se la granularità non è definita, si presuppone che il set di dati sia una serie temporale.||
|`max_horizon`|Definisce l'orizzonte di previsione massimo desiderato in unità di frequenza di serie temporali. Le unità sono basate sull'intervallo di tempo dei dati di training, ad esempio ogni mese, settimanalmente che il Forecaster deve prevedere.|✓|
|`target_lags`|*n* punti per i valori di destinazione del ritardo di avanzamento prima del training del modello.||
|`target_rolling_window_size`|*n* periodi cronologici da usare per generare valori previsti, < = dimensioni del set di training. Se omesso, *n* è la dimensione massima del set di training.||

Creare le impostazioni della serie temporale come oggetto Dictionary. Impostare sul `time_column_name` `day_datetime` campo nel set di dati. Definire il `grain_column_names` parametro per assicurarsi che vengano creati **due gruppi di serie temporali distinti** per i dati, uno per i negozi a e B. Infine, impostare `max_horizon` su 50 per stimare l'intero set di test. Impostare una finestra previsioni su 10 punti con `target_rolling_window_size`e ritardare i valori di destinazione 2 punti con il `target_lags` parametro.

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": 50,
    "target_lags": 2,
    "target_rolling_window_size": 10,
    "preprocess": True,
}
```

> [!NOTE]
> I passaggi di pre-elaborazione di Machine Learning automatizzati (normalizzazione delle funzionalità, gestione dei dati mancanti, conversione di valori di testo nel formato numerico e così via) diventano parte del modello sottostante. Quando si usa il modello per le previsioni, gli stessi passaggi di pre-elaborazione applicati durante il training vengono automaticamente applicati ai dati di input.

A questo punto, `AutoMLConfig` creare un oggetto standard `forecasting` , specificando il tipo di attività e inviare l'esperimento. Al termine del modello, recuperare l'iterazione di esecuzione migliore.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             iterations=10,
                             X=X_train,
                             y=y_train,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

> [!NOTE]
> Per la procedura di convalida incrociata (CV), i dati delle serie temporali possono violare i presupposti statistici di base della strategia di convalida incrociata della K-fold canonica, in modo che Machine Learning automatico implementi una procedura di convalida dell'origine in sequenza per creare riduzioni di convalida incrociata per i dati di serie temporali. Per utilizzare questa procedura, specificare il `n_cross_validations` parametro `AutoMLConfig` nell'oggetto. È possibile ignorare la convalida e usare i set di convalida personalizzati `X_valid` con `y_valid` i parametri e.

### <a name="view-feature-engineering-summary"></a>Visualizza riepilogo Progettazione funzionalità

Per i tipi di attività delle serie temporali in automazione automatica, è possibile visualizzare i dettagli del processo di progettazione delle funzionalità. Il codice seguente mostra ogni funzionalità non elaborata con gli attributi seguenti:

* Nome della funzionalità non elaborata
* Numero di funzionalità progettate da questa funzionalità non elaborata
* Tipo rilevato
* Indica se la funzionalità è stata eliminata
* Elenco di trasformazioni di funzionalità per la funzionalità RAW

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Previsione con il modello migliore

Utilizzare l'iterazione del modello migliore per prevedere i valori per il set di dati di test.

```python
y_predict = fitted_model.predict(X_test)
y_actual = y_test.flatten()
```

In alternativa, è possibile utilizzare la `forecast()` funzione `predict()`anziché, che consentirà le specifiche di quando le stime devono essere avviate. Nell'esempio seguente vengono innanzitutto sostituiti tutti i valori in `y_pred` con `NaN`. In questo caso, l'origine della previsione sarà alla fine dei dati di training, come in genere quando si usa `predict()`. Tuttavia, se si sostituisce solo la seconda metà di `y_pred` con `NaN`, la funzione lascia i valori numerici nella prima metà non modificati, ma prevede i `NaN` valori nella seconda metà. La funzione restituisce i valori previsti e le funzionalità allineate.

È anche possibile usare il `forecast_destination` parametro `forecast()` nella funzione per prevedere i valori fino a una data specificata.

```python
y_query = y_test.copy().astype(np.float)
y_query.fill(np.nan)
y_fcst, X_trans = fitted_pipeline.forecast(
    X_test, y_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcolare valori RMSE (radice errore quadratico medio) tra i `y_test` valori effettivi e i valori previsti in. `y_pred`

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

Ora che è stata determinata l'accuratezza del modello complessiva, il passaggio successivo più realistico consiste nell'usare il modello per prevedere valori futuri sconosciuti. È sufficiente fornire un set di dati nello stesso formato del set `X_test` di test ma con DateTime futuri e il set di stime risultante corrisponde ai valori previsti per ogni passaggio della serie temporale. Si supponga che gli ultimi record di serie temporali nel set di dati siano per 12/31/2018. Per prevedere la domanda del giorno successivo (o il numero di periodi in cui è necessario prevedere, < `max_horizon`=), creare un singolo record della serie temporale per ogni negozio per 01/01/2019.

    day_datetime,store,week_of_year
    01/01/2019,A,1
    01/01/2019,A,1

Ripetere i passaggi necessari per caricare i dati futuri in un dataframe, quindi eseguire `best_run.predict(X_test)` per stimare i valori futuri.

> [!NOTE]
> Non è possibile stimare i valori per il `max_horizon`numero di periodi maggiore di. È necessario rieseguire il training del modello con un orizzonte più ampio per stimare i valori futuri oltre l'orizzonte corrente.

## <a name="next-steps"></a>Passaggi successivi

* Seguire l' [esercitazione](tutorial-auto-train-models.md) per imparare a creare esperimenti con Machine Learning automatizzato.
* Visualizzare la documentazione [di riferimento di Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) .
