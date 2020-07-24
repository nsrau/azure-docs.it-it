---
title: Eseguire il training automatico di un modello di previsione di una serie temporale
titleSuffix: Azure Machine Learning
description: Informazioni su come usare Azure Machine Learning per eseguire il training di un modello di regressione di previsione di una serie temporale usando Machine Learning automatizzato.
services: machine-learning
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 03/09/2020
ms.openlocfilehash: ec2ef52978a24619103d9a0a2b7fb8ed1099c40b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87031593"
---
# <a name="auto-train-a-time-series-forecast-model"></a>Eseguire il training automatico di un modello di previsione di una serie temporale
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo descrive come configurare ed eseguire il training di un modello di regressione di previsione di una serie temporale usando Machine Learning automatizzato in [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py). 

Per un'esperienza di codice ridotta, vedere [Esercitazione: Prevedere la domanda con Machine Learning automatizzato](tutorial-automated-ml-forecast.md) per un esempio di previsione di una serie temporale usando Machine Learning automatizzato in [Azure Machine Learning Studio](https://ml.azure.com/).

La configurazione di un modello di previsione è simile alla configurazione di un modello di regressione standard tramite Machine Learning automatizzato, ma sono disponibili alcune opzioni di configurazione e passaggi di pre-elaborazione per l'utilizzo dei dati della serie temporale. 

Ad esempio, è possibile [configurare](#config) fino a quale momento deve estendersi la previsione (orizzonte di previsione), oltre a ritardi e altro ancora. Machine Learning automatizzato apprende un modello singolo, ma spesso con rami interni, per tutti gli elementi del set di dati e degli orizzonti di stima. Maggiori sono quindi i dati disponibili per la stima dei parametri del modello e più probabile diventa la generalizzazione per la serie non visibile.

Gli esempi seguenti mostrano come:

* Preparare i dati per la modellazione della serie temporale
* Configurare parametri specifici della serie temporale in un oggetto [`AutoMLConfig`](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig)
* Eseguire stime con i dati della serie temporale

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2X1GW]

A differenza dei metodi della serie temporale classici, in Machine Learning automatizzato i valori della serie temporale precedente vengono "trasformati tramite Pivot" per diventare dimensioni aggiuntive per il regressore insieme ad altri predittori. Questo approccio incorpora più variabili contestuali e la relazione reciproca durante il training. Poiché più fattori possono influenzare una previsione, questo metodo si allinea perfettamente con scenari di previsione reali. Ad esempio, quando si prevedono le vendite, le interazioni delle tendenze cronologiche, il tasso di cambio e il prezzo influenzano congiuntamente il risultato delle vendite. 

Le funzionalità estratte dai dati di training svolgono un ruolo fondamentale. Inoltre, Machine Learning automatizzato esegue passaggi di pre-elaborazione standard e genera funzionalità aggiuntive della serie temporale per acquisire gli effetti stagionali e ottimizzare la precisione predittiva.

## <a name="time-series-and-deep-learning-models"></a>Modelli di serie temporale e Deep Learning

Il Deep Learning di Machine Learning automatizzato consente di prevedere i dati univariati e multivariati della serie temporale.

I modelli di Deep Learning hanno tre funzionalità intrinseche:
1. Possono imparare da mapping arbitrari da input a output
1. Supportano più input e output
1. Possono estrarre automaticamente i modelli nei dati di input che si estendono su lunghe sequenze

Con dati di maggiori dimensioni, i modelli di Deep Learning, ad esempio ForecastTCN di Microsoft, possono migliorare i punteggi del modello risultante. Informazioni su come [configurare l'esperimento per Deep Learning](#configure-a-dnn-enable-forecasting-experiment).

Machine Learning automatizzato fornisce agli utenti sia i modelli nativi della serie temporale, che i modelli di Deep Learning come parte del sistema di raccomandazione. 

Modelli| Descrizione | Vantaggi
----|----|---
Prophet (anteprima)|Il modello Prophet funziona meglio con le serie temporali con effetti stagionali forti e diverse stagioni di dati cronologici. Per utilizzare questo modello, installarlo localmente utilizzando `pip install fbprophet` . | Accuratezza, rapidità, affidabilità per outlier, dati mancanti e modifiche radicali nella serie temporale.
Auto-ARIMA (anteprima)|AutoRegressive Integrated Moving Average (ARIMA) garantisce prestazioni ottimali quando i dati sono stazionari. Ciò significa che le proprietà statistiche, come la media e la varianza, sono costanti per l'intero set. Se, ad esempio, si lancia una moneta, la probabilità di ottenere testa è pari al 50%, indipendentemente dal fatto che venga lanciata oggi, domani o il prossimo anno.| Ideale per le serie univariate, perché i valori precedenti vengono usati per stimare i valori futuri.
ForecastTCN (anteprima)| ForecastTCN è un modello di rete neurale progettato per affrontare le attività di previsione più complesse, acquisendo le tendenze locali e globali non lineari nei dati, nonché le relazioni tra le serie temporali.|Capacità di sfruttare le tendenze complesse nei dati e di ridimensionare immediatamente nel set di dati di dimensioni maggiori.

## <a name="prerequisites"></a>Prerequisiti

* Un'area di lavoro di Azure Machine Learning. Per creare l'area di lavoro, vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).
* Questo articolo presuppone una conoscenza di base della configurazione di un esperimento di Machine Learning automatizzato. Seguire l'[esercitazione](tutorial-auto-train-models.md) o la [procedura](how-to-configure-auto-train.md) per visualizzare i modelli di progettazione dell'esperimento di Machine Learning automatizzato di base.

## <a name="preparing-data"></a>Preparazione dei dati

La differenza più importante tra un tipo di attività di regressione di previsione e un tipo di attività di regressione all'interno di Machine Learning automatizzato consiste nell'includere una funzionalità nei dati che rappresenti una serie temporale valida. Una serie temporale regolare presenta una frequenza ben definita e coerente e un valore in ogni punto di esempio in un intervallo di tempo continuo. Si consideri lo snapshot seguente di un file `sample.csv`.

```output
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
```

Questo set di dati è un semplice esempio di dati di vendita giornalieri per una società con due punti vendita diversi, A e B. Esiste inoltre una funzionalità per `week_of_year` che consentirà al modello di rilevare la stagionalità settimanale. Il campo `day_datetime` rappresenta una serie temporale pulita con frequenza giornaliera e il campo `sales_quantity` è la colonna di destinazione per l'esecuzione delle stime. Leggere i dati in un frame di dati Pandas e quindi usare la funzione `to_datetime` per assicurarsi che la serie temporale sia di tipo `datetime`.

```python
import pandas as pd
data = pd.read_csv("sample.csv")
data["day_datetime"] = pd.to_datetime(data["day_datetime"])
```

In questo caso, i dati sono già ordinati in ordine crescente in base al campo dell'ora `day_datetime`. Tuttavia, quando si configura un esperimento, verificare che la colonna temporale desiderata venga ordinata in ordine crescente per compilare una serie temporale valida. Si supponga che i dati contengano 1000 record e creino una suddivisione deterministica nei dati per creare set di dati di training e di test. Identificare il nome colonna dell'etichetta e impostarlo su etichetta. In questo esempio, l'etichetta sarà `sales_quantity`. Quindi separare il campo etichetta da `test_data` per formare il set `test_target`.

```python
train_data = data.iloc[:950]
test_data = data.iloc[-50:]

label =  "sales_quantity"
 
test_labels = test_data.pop(label).values
```

> [!NOTE]
> Quando si esegue il training di un modello per la previsione di valori futuri, assicurarsi che tutte le funzionalità usate nel training possano essere usate quando si eseguono stime per l'orizzonte previsto. Ad esempio, durante la creazione di una previsione della richiesta, includere una funzionalità per il prezzo dei titoli azionari corrente potrebbe aumentare notevolmente l'accuratezza del training. Se, tuttavia, si vogliono effettuare previsioni con un orizzonte lungo, potrebbe non essere possibile stimare in modo accurato i valori dei titoli azionari futuri corrispondenti ai punti futuri della serie temporale e l'accuratezza del modello potrebbe risentirne.

<a name="config"></a>

## <a name="train-and-validation-data"></a>Dati di training e convalida
È possibile specificare set di training e convalida separati direttamente nel costruttore `AutoMLConfig`.

### <a name="rolling-origin-cross-validation"></a>Convalida incrociata dell'origine in sequenza
Per la previsione della serie temporale, la convalida incrociata dell'origine in sequenza viene usata per suddividere la serie temporale in modo coerente in termini di tempo. La convalida incrociata dell'origine in sequenza divide la serie in dati di training e convalida usando un punto di origine. Scorrere l'origine nel tempo comporta la generazione di riduzioni della convalida incrociata.  

![testo alternativo](./media/how-to-auto-train-forecast/ROCV.svg)

Questa strategia consente di mantenere l'integrità dei dati della serie temporale ed elimina il rischio di perdita dei dati. La convalida incrociata dell'origine in sequenza viene usata automaticamente per le attività di previsione passando i dati di training e convalida insieme e impostando il numero di riduzioni della convalida incrociata usando `n_cross_validations`. Altre informazioni sul modo in cui Machine Learning automatizzato applica la convalida incrociata per [impedire l'overfitting dei modelli](concept-manage-ml-pitfalls.md#prevent-over-fitting).

```python
automl_config = AutoMLConfig(task='forecasting',
                             n_cross_validations=3,
                             ...
                             **time_series_settings)
```
Altre informazioni sull'oggetto [AutoMLConfig](#configure-and-run-experiment).

## <a name="configure-and-run-experiment"></a>Configurare ed eseguire l'esperimento

Per le attività di previsione, Machine Learning automatizzato usa operazioni di pre-elaborazione e di stima specifiche per i dati della serie temporale. Verranno eseguiti i passaggi di pre-elaborazione seguenti:

* Rilevare la frequenza di campionamento della serie temporale (ad esempio, ogni ora, giorno, settimana) e creare nuovi record per i momenti mancanti per rendere la serie continua
* Imputare i valori mancanti nell'oggetto di destinazione (tramite il caricamento in diretta) e nelle colonne di funzioni (usando i valori della colonna mediana)
* Creare funzionalità basate sulla granularità per consentire effetti fissi su serie diverse
* Creare funzionalità basate sul tempo per facilitare l'apprendimento di modelli stagionali
* Codificare variabili categoriche su quantità numeriche

L'oggetto [`AutoMLConfig`](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py) definisce le impostazioni e i dati necessari per un'attività di Machine Learning automatizzato. Analogamente a un problema di regressione, si definiscono parametri di training standard, come il tipo di attività, il numero di iterazioni, i dati di training e il numero di convalide incrociate. Per le attività di previsione, è necessario impostare parametri aggiuntivi che interessano l'esperimento. La tabella seguente descrive ciascun parametro e il relativo utilizzo.

| Nome&nbsp;parametro | Descrizione | Obbligatoria |
|-------|-------|-------|
|`time_column_name`|Usato per specificare la colonna datetime nei dati di input usati per compilare la serie temporale e dedurne la frequenza.|✓|
|`grain_column_names`|Nomi che definiscono i singoli gruppi di serie nei dati di input. Se la granularità non è definita, si presuppone che il set di dati sia una serie temporale.||
|`max_horizon`|Definisce l'orizzonte di previsione massimo desiderato in unità di frequenza di serie temporali. Le unità si basano sull'intervallo temporale dei dati di training, ad esempio mensile o settimanale, che il modulo di previsione deve prevedere.|✓|
|`target_lags`|Numero di righe riferite al ritardo dei valori di destinazione in base alla frequenza dei dati. Il ritardo è rappresentato come un elenco o un singolo Integer. È consigliabile usare il ritardo quando la relazione tra le variabili indipendenti e la variabile dipendente non corrisponde o non è correlata per impostazione predefinita. Ad esempio, quando si tenta di prevedere la richiesta di un prodotto, la richiesta per un mese specifico può dipendere dal prezzo di determinati prodotti nei tre mesi precedenti. In questo esempio, è possibile che si voglia ritardare la destinazione (richiesta) negativamente di tre mesi, in modo che il modello sia in grado di eseguire il training sulla relazione corretta.||
|`target_rolling_window_size`|*n* periodi cronologici da usare per generare valori previsti, < = dimensioni del set di training. Se omesso, *n* è la dimensione massima del set di training. Specificare questo parametro quando si vuole considerare solo una certa quantità di dati cronologici durante il training del modello.||
|`enable_dnn`|Abilitare le reti neurali profonde di previsione.||

Per altre informazioni, vedere la [documentazione di riferimento](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig).

Creare le impostazioni della serie temporale come oggetto Dictionary. Impostare `time_column_name` sul campo `day_datetime` nel set di dati. Definire il parametro `grain_column_names`, per assicurarsi che vengano creati **due gruppi di serie temporali distinti** per i dati: uno per il negozio A e uno per il negozio B. Infine, impostare `max_horizon` su 50 per stimare l'intero set di test. Impostare una finestra di previsione su 10 periodi con `target_rolling_window_size` e specificare un singolo ritardo sui valori di destinazione per due periodi successivi con il parametro `target_lags`. Si consiglia di impostare `max_horizon`, `target_rolling_window_size` e `target_lags` su "automatico", che rileverà automaticamente questi valori. Nell'esempio seguente sono state usate le impostazioni "automatiche" per questi parametri. 

```python
time_series_settings = {
    "time_column_name": "day_datetime",
    "grain_column_names": ["store"],
    "max_horizon": "auto",
    "target_lags": "auto",
    "target_rolling_window_size": "auto",
    "preprocess": True,
}
```

> [!NOTE]
> I passaggi di pre-elaborazione di Machine Learning automatizzati (normalizzazione delle funzionalità, gestione dei dati mancanti, conversione di valori di testo nel formato numerico e così via) diventano parte del modello sottostante. Quando si usa il modello per le previsioni, gli stessi passaggi di pre-elaborazione applicati durante il training vengono automaticamente applicati ai dati di input.

Definendo `grain_column_names` nel frammento di codice precedente, Machine Learning automatizzato creerà due gruppi di serie temporali distinti, noti anche come serie temporali multiple. Se non viene definita alcuna granularità, Machine Learning automatizzato presuppone che il set di dati sia una singola serie temporale. Per altre informazioni sulle singole serie temporali, vedere [energy_demand_notebook](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand).

A questo punto, creare un oggetto `AutoMLConfig` standard, specificando il tipo di attività `forecasting` e inviare l'esperimento. Al termine del modello, recuperare l'iterazione di esecuzione migliore.

```python
from azureml.core.workspace import Workspace
from azureml.core.experiment import Experiment
from azureml.train.automl import AutoMLConfig
import logging

automl_config = AutoMLConfig(task='forecasting',
                             primary_metric='normalized_root_mean_squared_error',
                             experiment_timeout_minutes=15,
                             enable_early_stopping=True,
                             training_data=train_data,
                             label_column_name=label,
                             n_cross_validations=5,
                             enable_ensembling=False,
                             verbosity=logging.INFO,
                             **time_series_settings)

ws = Workspace.from_config()
experiment = Experiment(ws, "forecasting_example")
local_run = experiment.submit(automl_config, show_output=True)
best_run, fitted_model = local_run.get_output()
```

Per esempi di codice dettagliati sulla configurazione di previsione avanzata, vedere i [notebook di esempio della previsione](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning), tra cui:

* [Rilevamento festività e definizione delle funzionalità](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [Convalida incrociata dell'origine in sequenza](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Ritardi configurabili](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-bike-share/auto-ml-forecasting-bike-share.ipynb)
* [Funzionalità di aggregazione della finestra mobile](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)
* [Rete neurale profonda](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb)

### <a name="configure-a-dnn-enable-forecasting-experiment"></a>Configurare un esperimento di previsione per l'abilitazione della rete neurale profonda

> [!NOTE]
> Il supporto della rete neurale profonda per la previsione in Machine Learning automatizzato è in anteprima e non è supportato per le esecuzioni locali.

Per sfruttare le reti neurali profonde per la previsione, è necessario impostare il parametro `enable_dnn` in AutoMLConfig su true. 

```python
automl_config = AutoMLConfig(task='forecasting',
                             enable_dnn=True,
                             ...
                             **time_series_settings)
```
Altre informazioni sull'oggetto [AutoMLConfig](#configure-and-run-experiment).

In alternativa, è possibile selezionare l'opzione `Enable deep learning` in Studio.
![testo alternativo](./media/how-to-auto-train-forecast/enable_dnn.png)

È consigliabile usare un cluster di elaborazione di Azure Machine Learning con SKU della GPU e almeno due nodi come destinazione di calcolo. Per consentire un tempo sufficiente per il completamento del training della rete neurale profonda, è consigliabile impostare il timeout dell'esperimento su almeno due ore.
Per altre informazioni sull'ambiente di calcolo di Azure Machine Learning e sulle dimensioni delle macchine virtuali che includono la GPU, vedere la documentazione relativa all'[ambiente di calcolo di Machine Learning](how-to-set-up-training-targets.md#amlcompute) e alle [dimensioni delle macchine virtuali ottimizzate per la GPU](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu).

Per un esempio di codice dettagliato sull'uso delle reti neurali profonde, vedere il [notebook relativo alle previsioni della produzione di bevande](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

### <a name="target-rolling-window-aggregation"></a>Aggregazioni della finestra mobile di destinazione
Spesso le informazioni migliori a disposizione di un modulo di previsione sono date dal valore recente della destinazione. La creazione di statistiche cumulative della destinazione può aumentare l'accuratezza delle stime. Le aggregazioni della finestra mobile di destinazione consentono di aggiungere un'aggregazione in sequenza di valori di dati come funzionalità. Per abilitare le finestre mobili di destinazione, impostare `target_rolling_window_size` sulle dimensioni della finestra Integer desiderate. 

Un esempio può essere visualizzato quando si stima la domanda di energia. È possibile aggiungere una funzionalità della finestra mobile di tre giorni per tenere conto delle modifiche termiche degli spazi riscaldati. Nell'esempio seguente è stata creata questa finestra di dimensione tre impostando `target_rolling_window_size=3` nel costruttore `AutoMLConfig`. La tabella mostra la definizione delle funzionalità che si verifica quando viene applicata l'aggregazione della finestra. Le colonne per i valori minimo, massimo e somma vengono generate in una finestra temporale scorrevole di tre valori in base alle impostazioni definite. Ogni riga ha una nuova funzionalità calcolata, nel caso del timestamp per l'8 settembre 2017 alle ore 4:00, i valori massimo, minimo e somma vengono calcolati usando i valori della richiesta per l'8 settembre 2017 dalle ore 1:00 alle ore 3:00. Questa finestra su base tre scorre in avanti per popolare i dati per le righe rimanenti.

![testo alternativo](./media/how-to-auto-train-forecast/target-roll.svg)

La generazione e l'uso di queste funzionalità aggiuntive come ulteriori dati contestuali facilitano l'accuratezza del modello di training.

Visualizzare un esempio di codice Python sfruttando la [funzionalità di aggregazione della finestra mobile di destinazione](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb).

### <a name="view-feature-engineering-summary"></a>Riepilogo della progettazione delle caratteristiche di visualizzazione

Per i tipi di attività della serie temporale in Machine Learning automatizzato, è possibile visualizzare i dettagli del processo di progettazione delle caratteristiche. Il codice seguente mostra le funzionalità non elaborate con gli attributi seguenti:

* Nome della funzionalità non elaborata
* Numero di caratteristiche progettate in base alla funzionalità non elaborata
* Tipo rilevato
* Indicazione dell'eliminazione della caratteristica
* Elenco delle trasformazioni della caratteristica in base alla funzionalità non elaborata

```python
fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
```

## <a name="forecasting-with-best-model"></a>Eseguire previsioni con il modello migliore

Usare l'iterazione del modello migliore per prevedere i valori per il set di dati di test.

È consigliabile usare la funzione `forecast()` anziché `predict()`, in modo da poter specificare quando devono essere avviate le stime. Nell'esempio seguente vengono innanzitutto sostituiti tutti i valori di `y_pred` con `NaN`. In questo caso, l'origine della previsione sarà alla fine dei dati di training, come avviene in genere quando si usa `predict()`. Se, tuttavia, si sostituisce solo la seconda metà di `y_pred` con `NaN`, la funzione lascia i valori numerici nella prima metà non modificati, ma prevede i valori `NaN` nella seconda metà. La funzione restituisce sia i valori previsti, che le funzionalità allineate.

È anche possibile usare il parametro `forecast_destination` nella funzione `forecast()` per prevedere i valori fino a una data specificata.

```python
label_query = test_labels.copy().astype(np.float)
label_query.fill(np.nan)
label_fcst, data_trans = fitted_pipeline.forecast(
    test_data, label_query, forecast_destination=pd.Timestamp(2019, 1, 8))
```

Calcolare i valori della radice dell'errore quadratico medio tra i valori effettivi di `actual_labels` e i valori previsti in `predict_labels`.

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(actual_labels, predict_labels))
rmse
```

Ora che è stata determinata l'accuratezza complessiva del modello, il passaggio successivo più realistico consiste nell'uso del modello per prevedere valori futuri sconosciuti. Fornire un set di dati nello stesso formato del set di test `test_data`, ma con datetime futuri e il set di stime risultante, corrisponde ai valori previsti per ogni passaggio della serie temporale. Si supponga che gli ultimi record della serie temporale nel set di dati si riferiscano al giorno 31/12/2018. Per prevedere la richiesta del giorno successivo (o il numero di periodi che è necessario prevedere, < = `max_horizon`), creare un singolo record della serie temporale per ogni negozio per il giorno 01/01/2019.

```output
day_datetime,store,week_of_year
01/01/2019,A,1
01/01/2019,A,1
```

Ripetere i passaggi necessari per caricare i dati futuri in un dataframe e quindi eseguire `best_run.predict(test_data)` per stimare i valori futuri.

> [!NOTE]
> Non è possibile stimare i valori per un numero di periodi maggiore di `max_horizon`. È necessario rieseguire il training del modello con un orizzonte più ampio per stimare i valori futuri oltre l'orizzonte corrente.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni su come creare esperimenti con Machine Learning automatizzato, seguire l'[esercitazione](tutorial-auto-train-models.md).
* Vedere la documentazione di riferimento di [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
