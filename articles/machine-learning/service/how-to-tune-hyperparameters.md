---
title: Ottimizzare gli iperparametri per il modello
titleSuffix: Azure Machine Learning
description: Ottimizza in modo efficiente gli iperparametri per il modello di apprendimento avanzato/apprendimento automatico usando Azure Machine Learning. Si apprenderà come definire lo spazio di ricerca dei parametri, come specificare una metrica primaria da ottimizzare e come terminare le esecuzioni con prestazioni scarse.
ms.author: swatig
author: swatig007
ms.reviewer: sgilley
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: seodec18
ms.openlocfilehash: a7b0276ca41e1b9342b3602a67dea0517c60f66a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489351"
---
# <a name="tune-hyperparameters-for-your-model-with-azure-machine-learning"></a>Ottimizzazione degli iperparametri per il modello con Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Ottimizza in modo efficiente gli iperparametri per il modello usando Azure Machine Learning.  L'ottimizzazione degli iperparametri include i passaggi seguenti:

* Definire lo spazio di ricerca dei parametri
* Specificare una metrica primaria da ottimizzare  
* Specificare criteri di terminazione anticipata per esecuzioni con scarse prestazioni
* Allocare le risorse per l'ottimizzazione degli iperparametri
* Avviare un esperimento con la configurazione precedente
* Visualizzare le esecuzioni di training
* Selezionare la configurazione con le migliori prestazioni per il modello

## <a name="what-are-hyperparameters"></a>Che cosa sono gli iperparametri?

Gli iperparametri sono parametri regolabili scelti per il training di un modello, che regolano il processo di training stesso. Per eseguire il training di una rete neurale profonda, decidere ad esempio il numero di livelli nascosti nella rete e il numero di nodi in ogni livello. Questi valori in genere rimangono costanti durante il processo di training.

Negli scenari di Machine Learning/apprendimento avanzato, le prestazioni del modello dipendono in larga misura dai valori degli iperparametri selezionati. L'obiettivo dell'esplorazione degli iperparametri è eseguire ricerche nelle varie configurazioni degli iperparametri per individuare una configurazione con le prestazioni migliori. In genere, il processo di esplorazione degli iperparametri viene eseguito manualmente, dato che lo spazio di ricerca è vasto e la valutazione di ogni configurazione può essere costosa.

Azure Machine Learning consente di automatizzare l'esplorazione degli iperparametri in modo efficiente, assicurando un risparmio di tempo e risorse. Specificare l'intervallo di valori degli iperparametri e il numero massimo di esecuzioni di training. Il sistema avvia automaticamente più esecuzioni simultanee con diverse configurazioni dei parametri e individua la configurazione con le migliori prestazioni, misurate in base alla metrica scelta. Le esecuzioni di training con scarse prestazioni vengono terminate automaticamente in modo anticipato, riducendo gli sprechi di risorse di calcolo. Queste risorse vengono invece usate per esplorare altre configurazioni degli iperparametri.


## <a name="define-search-space"></a>Definire lo spazio di ricerca

Ottimizzare automaticamente gli iperparametri esplorando l'intervallo di valori definiti per ogni iperparametro.

### <a name="types-of-hyperparameters"></a>Tipi di iperparametri

Ogni iperparametro può essere discreto o continuo e presenta una distribuzione dei valori descritti da un' [espressione di parametro](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.parameter_expressions?view=azure-ml-py).

#### <a name="discrete-hyperparameters"></a>Iperparametri discreti 

Gli iperparametri discreti vengono specificati come una scelta (`choice`) tra valori discreti. `choice` può essere:

* Uno o più valori delimitati da virgole
* Un oggetto `range`
* Un oggetto `list` arbitrario


```Python
    {
        "batch_size": choice(16, 32, 64, 128)
        "number_of_hidden_layers": choice(range(1,5))
    }
```

In questo caso, `batch_size` accetta uno dei valori [16, 32, 64, 128] e `number_of_hidden_layers` accetta uno dei valori [1, 2, 3, 4].

È inoltre possibile specificare iperparametri discreti avanzati usando una distribuzione. Sono supportate le distribuzioni seguenti:

* `quniform(low, high, q)`: restituisce un valore come round(uniform(low, high) / q) * q
* `qloguniform(low, high, q)`: restituisce un valore come round(exp(uniform(low, high)) / q) * q
* `qnormal(mu, sigma, q)`: restituisce un valore come round(normal(mu, sigma) / q) * q
* `qlognormal(mu, sigma, q)`: restituisce un valore come round(exp(normal(mu, sigma)) / q) * q

#### <a name="continuous-hyperparameters"></a>Iperparametri continui 

Gli iperparametri continui vengono specificati come una distribuzione su un intervallo di valori continuo. Le distribuzioni supportate includono:

* `uniform(low, high)` : restituisce un valore distribuito in modo uniforme tra low e high
* `loguniform(low, high)` : restituisce un valore ricavato in base a exp(uniform(low, high)), in modo che il logaritmo del valore restituito sia distribuito uniformemente
* `normal(mu, sigma)` : restituisce un valore reale distribuito in modo normale con la media mu e la deviazione standard sigma
* `lognormal(mu, sigma)` : restituisce un valore ricavato in base a exp(normal(mu, sigma)), in modo che il logaritmo del valore restituito sia distribuito in modo normale

Esempio di definizione per lo spazio dei parametri:

```Python
    {    
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1)
    }
```

Questo codice definisce uno spazio di ricerca con due parametri: `learning_rate` e `keep_probability`. `learning_rate` ha una distribuzione normale con un valore medio di 10 e una deviazione standard pari a 3. `keep_probability` ha una distribuzione uniforme con un valore minimo di 0,05 e un valore massimo di 0,1.

### <a name="sampling-the-hyperparameter-space"></a>Campionamento dello spazio degli iperparametri

È anche possibile specificare il metodo di campionamento dei parametri da usare per la definizione dello spazio degli iperparametri. Azure Machine Learning supporta il campionamento casuale, il campionamento della griglia e il campionamento Bayes.

#### <a name="picking-a-sampling-method"></a>Selezione di un metodo di campionamento

* Il campionamento della griglia può essere usato se lo spazio degli iperparametri può essere definito come scelta tra valori discreti e se si dispone di un budget sufficiente per eseguire una ricerca completa su tutti i valori nello spazio di ricerca definito. Inoltre, è possibile utilizzare la terminazione anticipata automatica delle esecuzioni con prestazioni scarse, riducendo così gli sprechi delle risorse.
* Il campionamento casuale consente allo spazio degli iperparametri di includere sia gli iperparametri discreti che quelli continui. In pratica, la maggior parte delle volte produce risultati ottimali e consente anche l'uso di una terminazione automatica anticipata delle esecuzioni con prestazioni ridotte. Alcuni utenti eseguono una ricerca iniziale usando il campionamento casuale e quindi rifiniscono in modo iterativo lo spazio di ricerca per migliorare i risultati.
* Il campionamento bayesiano sfrutta le conoscenze degli esempi precedenti quando si scelgono i valori degli iperparametri, tentando in modo efficace di migliorare la metrica primaria segnalata. Il campionamento Bayes è consigliato quando si dispone di un budget sufficiente per esplorare lo spazio degli iperparametri: per ottenere risultati ottimali con il campionamento Bayes è consigliabile usare un numero massimo di esecuzioni maggiore o uguale a 20 volte il numero di iperparametri da ottimizzare. Si noti che il campionamento Bayes non supporta attualmente i criteri di terminazione precedenti.

#### <a name="random-sampling"></a>Campionamento casuale

Nel campionamento casuale i valori degli iperparametri vengono selezionati in modo casuale dallo spazio di ricerca definito. Il [campionamento casuale](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.randomparametersampling?view=azure-ml-py) consente allo spazio di ricerca di includere sia gli iperparametri discreti che quelli continui.

```Python
from azureml.train.hyperdrive import RandomParameterSampling
param_sampling = RandomParameterSampling( {
        "learning_rate": normal(10, 3),
        "keep_probability": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

#### <a name="grid-sampling"></a>Campionamento a griglia

Il [campionamento Grid](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.gridparametersampling?view=azure-ml-py) esegue una semplice ricerca sulla griglia su tutti i valori possibili nello spazio di ricerca definito. Può essere usato solo con gli iperparametri specificati tramite `choice`. Lo spazio seguente ha, ad esempio, un totale di sei campioni:

```Python
from azureml.train.hyperdrive import GridParameterSampling
param_sampling = GridParameterSampling( {
        "num_hidden_layers": choice(1, 2, 3),
        "batch_size": choice(16, 32)
    }
)
```

#### <a name="bayesian-sampling"></a>Campionamento bayesiano

Il [campionamento bayesiano](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.bayesianparametersampling?view=azure-ml-py) è basato sull'algoritmo di ottimizzazione Bayes e consente di scegliere in modo intelligente i valori di iperparametri per l'esempio successivo. Il campione viene scelto in base alle prestazioni del campione precedente, in modo che il nuovo campione migliori la metrica primaria segnalata.

Quando si usa il campionamento bayesiano, il numero di esecuzioni simultanee influisce sull'efficacia del processo di ottimizzazione. In genere, un numero minore di esecuzioni simultanee può migliorare la convergenza di campionamento, poiché il minor grado di parallelismo aumenta il numero di esecuzioni che traggono vantaggio dalle esecuzioni precedentemente completate.

Il campionamento bayesiano supporta solo le distribuzioni di `choice`, `uniform`e `quniform` nello spazio di ricerca.

```Python
from azureml.train.hyperdrive import BayesianParameterSampling
param_sampling = BayesianParameterSampling( {
        "learning_rate": uniform(0.05, 0.1),
        "batch_size": choice(16, 32, 64, 128)
    }
)
```

> [!NOTE]
> Il campionamento bayesiano non supporta i criteri per la terminazione anticipata (vedere [Specificare i criteri per la terminazione anticipata](#specify-early-termination-policy)). Quando si usa il campionamento bayesiano dei parametri, impostare `early_termination_policy = None` o lasciare disattivato il parametro `early_termination_policy`.

<a name='specify-primary-metric-to-optimize'/>

## <a name="specify-primary-metric"></a>Specificare la metrica primaria

Specificare la [metrica primaria](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.primarymetricgoal?view=azure-ml-py) da ottimizzare per l'esperimento di ottimizzazione degli iperparametri. Ogni esecuzione di training viene valutata in base a questa metrica primaria e le esecuzioni con scarse prestazioni (in cui la metrica primaria non soddisfa i criteri di terminazione anticipata) verranno terminate. Oltre al nome della metrica primaria, specificare anche l'obiettivo dell'ottimizzazione: se aumentare o ridurre la metrica primaria.

* `primary_metric_name`: nome della metrica primaria da ottimizzare. Il nome della metrica primaria deve corrispondere esattamente al nome della metrica registrato dallo script di training. Vedere [Registrare le metriche per l'ottimizzazione degli iperparametri](#log-metrics-for-hyperparameter-tuning).
* `primary_metric_goal`: può essere `PrimaryMetricGoal.MAXIMIZE` o `PrimaryMetricGoal.MINIMIZE` e determina se la metrica primaria verrà aumentata o ridotta durante la valutazione dell'esecuzione. 

```Python
primary_metric_name="accuracy",
primary_metric_goal=PrimaryMetricGoal.MAXIMIZE
```

Ottimizzare le esecuzioni per migliorare il livello di precisione.  Assicurarsi di registrare questo valore nello script di training.

<a name='log-metrics-for-hyperparameter-tuning'/>

### <a name="log-metrics-for-hyperparameter-tuning"></a>Registrare le metriche per l'ottimizzazione degli iperparametri

Lo script di training per il modello deve registrare le metriche pertinenti durante il training del modello. Quando si configura l'ottimizzazione degli iperparametri, specificare la metrica primaria da usare per la valutazione delle prestazioni di esecuzione. Vedere [specificare una metrica primaria da ottimizzare](#specify-primary-metric-to-optimize).  Nello script di training è necessario registrare questa metrica in modo che sia disponibile per il processo di ottimizzazione degli iperparametri.

Registrare la metrica nello script di training con il seguente frammento di esempio:

```Python
from azureml.core.run import Run
run_logger = Run.get_context()
run_logger.log("accuracy", float(val_accuracy))
```

Lo script di training calcola `val_accuracy` e lo registra come precisione, che viene usata come metrica primaria. Ogni volta che viene registrata, la metrica viene ricevuta dal servizio di ottimizzazione degli iperparametri. È compito dello sviluppatore del modello determinare la frequenza con cui segnalare questa metrica.

<a name='specify-early-termination-policy'/>

## <a name="specify-early-termination-policy"></a>Specificare i criteri per la terminazione anticipata

Terminare automaticamente le esecuzioni con scarse prestazioni con criteri di terminazione anticipata. Questa terminazione riduce gli sprechi di risorse, impiegandole per l'esplorazione di altre configurazioni dei parametri.

Quando si usano criteri di terminazione anticipata, è possibile configurare i parametri seguenti che consentono di controllare quando vengono applicati i criteri:

* `evaluation_interval`: frequenza per l'applicazione del criterio. Ogni volta che lo script di training registra la metrica primaria viene conteggiata come un intervallo. Di conseguenza, un valore `evaluation_interval` di 1 applicherà i criteri ogni volta che lo script di training segnala la metrica primaria. Un valore `evaluation_interval` di 2 applicherà i criteri una volta ogni due. Se non specificato, `evaluation_interval` è impostato su 1 per impostazione predefinita.
* `delay_evaluation`: ritarda la prima valutazione dei criteri per un numero di intervalli specificato. Si tratta di un parametro facoltativo che consente l'esecuzione di tutte le configurazioni per un numero minimo iniziale di intervalli, evitando la terminazione anticipata delle esecuzioni di training. Se specificato, i criteri vengono applicati ogni multiplo di evaluation_interval che è maggiore o uguale a delay_evaluation.

Azure Machine Learning supporta i seguenti criteri di terminazione anticipata.

### <a name="bandit-policy"></a>Criteri Bandit

[Bandit](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.banditpolicy?view=azure-ml-py#definition) è un criterio di terminazione basato sulla quantità di Slack/slack e sull'intervallo di valutazione. Questi criteri terminano in modo anticipato qualsiasi esecuzione in cui la metrica primaria non rientra nel fattore/valore per il margine di flessibilità rispetto all'esecuzione di training con le migliori prestazioni. I parametri di configurazione accettati sono i seguenti:

* `slack_factor` o `slack_amount`: margine di flessibilità consentito rispetto all'esecuzione di training con le migliori prestazioni. `slack_factor` specifica il margine di flessibilità consentito come rapporto. `slack_amount` specifica il margine di flessibilità consentito come valore assoluto, anziché come rapporto.

    Si consideri ad esempio un criterio Bandit applicato all'intervallo 10. Si supponga che l'esecuzione con le migliori prestazioni nell'intervallo 10 abbia segnalato una metrica primaria di 0,8 con l'obiettivo di aumentare la metrica primaria. Se i criteri sono stati specificati con un valore `slack_factor` di 0,2, verrà terminata qualsiasi esecuzione di training la cui metrica migliore nell'intervallo 10 è minore di 0,66 (0,8/(1+`slack_factor`)). Se invece i criteri sono stati specificati con un valore `slack_amount` di 0,2, verrà terminata qualsiasi esecuzione di training la cui metrica migliore nell'intervallo 10 è minore di 0,6 (0,8 - `slack_amount`).
* `evaluation_interval`: frequenza per l'applicazione del criterio (parametro facoltativo).
* `delay_evaluation`: ritarda la prima valutazione dei criteri per un numero di intervalli specificato (parametro facoltativo).


```Python
from azureml.train.hyperdrive import BanditPolicy
early_termination_policy = BanditPolicy(slack_factor = 0.1, evaluation_interval=1, delay_evaluation=5)
```

In questo esempio, i criteri di terminazione anticipata vengono applicati a ogni intervallo in cui vengono segnalate le metriche, a partire dall'intervallo di valutazione 5. Verrà terminata qualsiasi esecuzione la cui metrica migliore è minore di (1/(1+0,1) o del 91% rispetto all'esecuzione con le migliori prestazioni.

### <a name="median-stopping-policy"></a>Criteri di arresto con valore mediano

L' [arresto mediano](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.medianstoppingpolicy?view=azure-ml-py) è un criterio di terminazione anticipato basato su medie in esecuzione di metriche primarie segnalate dalle esecuzioni. Questi criteri calcolano le medie mobili per tutte le esecuzioni di training e terminano le esecuzioni con prestazioni inferiori al valore mediano delle medie mobili. I parametri di configurazione accettati da questi criteri sono i seguenti:
* `evaluation_interval`: frequenza per l'applicazione del criterio (parametro facoltativo).
* `delay_evaluation`: ritarda la prima valutazione dei criteri per un numero di intervalli specificato (parametro facoltativo).


```Python
from azureml.train.hyperdrive import MedianStoppingPolicy
early_termination_policy = MedianStoppingPolicy(evaluation_interval=1, delay_evaluation=5)
```

In questo esempio, i criteri di terminazione anticipata vengono applicati a ogni intervallo, a partire dall'intervallo di valutazione 5. Un'esecuzione verrà terminata nell'intervallo 5 se la metrica primaria migliore è inferiore al valore mediano delle medie mobili sugli intervalli 1:5 di tutte le esecuzioni di training.

### <a name="truncation-selection-policy"></a>Criteri di selezione con troncamento

La [selezione dei troncamenti](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.truncationselectionpolicy?view=azure-ml-py) Annulla una determinata percentuale di esecuzioni più basse a ogni intervallo di valutazione. Le esecuzioni vengono confrontate in base alle relative prestazioni rispetto alla metrica primaria e la percentuale X di quelle più basse viene terminata. I parametri di configurazione accettati sono i seguenti:

* `truncation_percentage`: percentuale delle esecuzioni con le prestazioni più basse da terminare in ogni intervallo di valutazione. Immettere un valore intero compreso tra 1 e 99.
* `evaluation_interval`: frequenza per l'applicazione del criterio (parametro facoltativo).
* `delay_evaluation`: ritarda la prima valutazione dei criteri per un numero di intervalli specificato (parametro facoltativo).


```Python
from azureml.train.hyperdrive import TruncationSelectionPolicy
early_termination_policy = TruncationSelectionPolicy(evaluation_interval=1, truncation_percentage=20, delay_evaluation=5)
```

In questo esempio, i criteri di terminazione anticipata vengono applicati a ogni intervallo, a partire dall'intervallo di valutazione 5. Un'esecuzione verrà terminata in corrispondenza dell'intervallo 5 se le prestazioni con intervallo 5 sono nel 20% delle prestazioni più basso di tutte le esecuzioni all'intervallo 5.

### <a name="no-termination-policy"></a>Criteri senza terminazione

Per eseguire tutte le esecuzioni di training fino al completamento, impostare il criterio su None. In tal modo, non verrà applicato alcun criterio di terminazione anticipata.

```Python
policy=None
```

### <a name="default-policy"></a>Criteri predefiniti

Se non viene specificato alcun criterio, il servizio di ottimizzazione iperparametri consente di eseguire tutte le esecuzioni di training fino al completamento.

### <a name="picking-an-early-termination-policy"></a>Selezione di un criterio di chiusura anticipato

* Se si sta cercando un criterio conservativo che consenta un risparmio senza terminare i processi promettenti, è possibile usare un criterio di arresto con valore mediano con `evaluation_interval` 1 e `delay_evaluation` 5. Queste sono impostazioni conservative, che possono garantire circa il 25-35% di risparmio senza alcuna perdita sulla metrica primaria (in base ai dati di valutazione).
* Se si sta cercando un risparmio più aggressivo dalla terminazione anticipata, è possibile usare criteri Bandit con criteri di selezione Slack o troncamento più restrittivi (più piccoli) con una percentuale di troncamento maggiore.

## <a name="allocate-resources"></a>Allocare le risorse

Controllare il budget di risorse per l'esperimento di ottimizzazione degli iperparametri specificando il numero massimo totale di esecuzioni di training.  Specificare, facoltativamente, la durata massima per l'esperimento di ottimizzazione degli iperparametri.

* `max_total_runs`: numero massimo totale di esecuzioni di training che verranno create. Limite superiore: il numero di esecuzioni potrebbe essere inferiore, ad esempio se lo spazio degli iperparametri è finito e ha un minor numero di campioni. Deve essere un numero compreso tra 1 e 1000.
* `max_duration_minutes`: durata massima in minuti dell'esperimento di ottimizzazione degli iperparametri. Parametro facoltativo. Se presente, vengono annullate automaticamente tutte le esecuzioni in esecuzione dopo questo periodo di tempo.

>[!NOTE] 
>Se si specificano sia `max_total_runs` che `max_duration_minutes`, l'esperimento di ottimizzazione degli iperparametri viene terminato quando viene raggiunta la prima di queste due soglie.

Specificare inoltre il numero massimo di esecuzioni di training da eseguire simultaneamente durante la ricerca per l'ottimizzazione degli iperparametri.

* `max_concurrent_runs`: numero massimo di esecuzioni da eseguire simultaneamente in qualsiasi momento. Se non si specifica alcun valore, verranno avviate in parallelo tutte le esecuzioni specificate da `max_total_runs`. Se specificato, il valore deve essere un numero compreso tra 1 e 100.

>[!NOTE] 
>Il numero di esecuzioni simultanee è limitato dalle risorse disponibili nella destinazione di calcolo specificata. È quindi necessario verificare che la destinazione di calcolo disponga di risorse sufficienti per la concorrenza desiderata.

Allocare risorse per l'ottimizzazione degli iperparametri:

```Python
max_total_runs=20,
max_concurrent_runs=4
```

Questo codice configura l'esperimento di ottimizzazione degli iperparametri in modo da usare un massimo di 20 esecuzioni totali, eseguendo quattro configurazioni alla volta.

## <a name="configure-experiment"></a>Configurare l'esperimento

Configurare l'esperimento di [ottimizzazione iperparametri](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverunconfig?view=azure-ml-py) usando lo spazio di ricerca di iperparametri definito, i criteri di terminazione anticipati, la metrica primaria e l'allocazione delle risorse dalle sezioni precedenti. Specificare anche un oggetto `estimator`, che verrà chiamato con gli iperparametri campionati. L'oggetto `estimator` descrive lo script di training da eseguire, le risorse per ogni processo (singola GPU o più GPU) e la destinazione di calcolo da usare. Poiché la concorrenza per l'esperimento di ottimizzazione degli iperparametri è limitata dalle risorse disponibili, verificare che la destinazione di calcolo specificata in `estimator` disponga di risorse sufficienti per la concorrenza desiderata. Per altre informazioni sugli oggetti estimator, vedere [Come eseguire il training di modelli](how-to-train-ml-models.md).

Configurare l'esperimento di ottimizzazione degli iperparametri:

```Python
from azureml.train.hyperdrive import HyperDriveConfig
hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="submit-experiment"></a>Inviare l'esperimento

Dopo aver definito la configurazione dell'ottimizzazione iperparametri, [inviare un esperimento](https://docs.microsoft.com/python/api/azureml-core/azureml.core.experiment%28class%29?view=azure-ml-py#submit-config--tags-none----kwargs-):

```Python
from azureml.core.experiment import Experiment
experiment = Experiment(workspace, experiment_name)
hyperdrive_run = experiment.submit(hyperdrive_run_config)
```

`experiment_name` è il nome assegnato all'esperimento di ottimizzazione degli iperparametri e `workspace` è l'area di lavoro in cui si desidera creare l'esperimento. per ulteriori informazioni sugli esperimenti, vedere [come funziona Azure Machine Learning?](concept-azure-machine-learning-architecture.md)

## <a name="warm-start-your-hyperparameter-tuning-experiment-optional"></a>Avvio a caldo dell'esperimento di ottimizzazione iperparametri (facoltativo)

Spesso, la ricerca dei migliori valori di iperparametri per il modello può essere un processo iterativo, che necessita di più esecuzioni di ottimizzazione che apprendere dalle esecuzioni precedenti dell'ottimizzazione degli iperparametri. Il riutilizzo delle informazioni da queste esecuzioni precedenti accelererà il processo di ottimizzazione degli iperparametri, riducendo in tal modo il costo di ottimizzazione del modello e potenzialmente migliorerà la metrica primaria del modello risultante. Quando si avvia un esperimento di ottimizzazione di iperparametri con il campionamento Bayes, le versioni di valutazione dell'esecuzione precedente verranno usate come conoscenze precedenti per scegliere in modo intelligente i nuovi esempi, per migliorare la metrica primaria. Inoltre, quando si usa il campionamento casuale o della griglia, tutte le decisioni di terminazione anticipata utilizzeranno le metriche delle esecuzioni precedenti per determinare le esecuzioni di training con scarso rendimento. 

Azure Machine Learning consente di avviare l'operazione di ottimizzazione dell'iperparametri usando le informazioni di un massimo di 5 esecuzioni del padre di ottimizzazione degli iperparametri completati o annullati in precedenza. È possibile specificare l'elenco di esecuzioni padre a cui iniziare a caldo usando il frammento di codice seguente:

```Python
from azureml.train.hyperdrive import HyperDriveRun

warmstart_parent_1 = HyperDriveRun(experiment, "warmstart_parent_run_ID_1")
warmstart_parent_2 = HyperDriveRun(experiment, "warmstart_parent_run_ID_2")
warmstart_parents_to_resume_from = [warmstart_parent_1, warmstart_parent_2]
```

In alcuni casi, inoltre, è possibile che le singole esecuzioni di training di un esperimento di ottimizzazione iperparametri vengano annullate a causa di vincoli di budget o non riuscite per altri motivi. A questo punto è possibile riprendere l'esecuzione di questo training individuale dall'ultimo checkpoint (presupponendo che lo script di training gestisca i checkpoint). La ripresa di una singola esecuzione di training utilizzerà la stessa configurazione di iperparametri e installerà la cartella degli output utilizzata per l'esecuzione. Lo script di training deve accettare l'argomento `resume-from`, che contiene i file del checkpoint o del modello da cui riprendere l'esecuzione del training. È possibile riprendere le esecuzioni di training individuali usando il frammento di codice seguente:

```Python
from azureml.core.run import Run

resume_child_run_1 = Run(experiment, "resume_child_run_ID_1")
resume_child_run_2 = Run(experiment, "resume_child_run_ID_2")
child_runs_to_resume = [resume_child_run_1, resume_child_run_2]
```

È possibile configurare l'esperimento di ottimizzazione iperparametri per iniziare a caldo da un esperimento precedente o riprendere le esecuzioni di training individuali usando i parametri facoltativi `resume_from` e `resume_child_runs` nella configurazione:

```Python
from azureml.train.hyperdrive import HyperDriveConfig

hyperdrive_run_config = HyperDriveConfig(estimator=estimator,
                          hyperparameter_sampling=param_sampling, 
                          policy=early_termination_policy,
                          resume_from=warmstart_parents_to_resume_from, 
                          resume_child_runs=child_runs_to_resume,
                          primary_metric_name="accuracy", 
                          primary_metric_goal=PrimaryMetricGoal.MAXIMIZE,
                          max_total_runs=100,
                          max_concurrent_runs=4)
```

## <a name="visualize-experiment"></a>Visualizzare l'esperimento

Il Azure Machine Learning SDK fornisce un [widget del notebook](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets.rundetails?view=azure-ml-py) che visualizza lo stato di avanzamento delle esecuzioni di training. Il frammento seguente mostra tutte le esecuzioni di ottimizzazione degli iperparametri in un Jupyter Notebook:

```Python
from azureml.widgets import RunDetails
RunDetails(hyperdrive_run).show()
```

Il codice mostra una tabella con informazioni dettagliate sulle esecuzioni di training per ciascuna configurazione degli iperparametri.

![tabella di ottimizzazione degli iperparametri](media/how-to-tune-hyperparameters/HyperparameterTuningTable.png)

È anche possibile visualizzare le prestazioni di ognuna delle esecuzioni nel corso del training. 

![tracciato di ottimizzazione degli iperparametri](media/how-to-tune-hyperparameters/HyperparameterTuningPlot.png)

È infine possibile identificare visivamente la correlazione tra le prestazioni e i valori dei singoli iperparametri usando un tracciato a coordinate parallele. 

[![le coordinate parallele di ottimizzazione degli iperparametri](media/how-to-tune-hyperparameters/HyperparameterTuningParallelCoordinates.png)](media/how-to-tune-hyperparameters/hyperparameter-tuning-parallel-coordinates-expanded.png)

È possibile visualizzare tutte le esecuzioni di ottimizzazione degli iperparametri nel portale Web di Azure. Per altre informazioni su come visualizzare un esperimento nel portale Web, vedere la sezione relativa a [come tracciare gli esperimenti](how-to-track-experiments.md#view-the-experiment-in-the-web-portal).

## <a name="find-the-best-model"></a>Individuare il modello migliore

Una volta completate tutte le operazioni di ottimizzazione degli iperparametri, [identificare la configurazione con le prestazioni migliori](https://docs.microsoft.com/python/api/azureml-train-core/azureml.train.hyperdrive.hyperdriverun?view=azure-ml-py#get-best-run-by-primary-metric-include-failed-true--include-canceled-true-) e i valori di iperparametri corrispondenti:

```Python
best_run = hyperdrive_run.get_best_run_by_primary_metric()
best_run_metrics = best_run.get_metrics()
parameter_values = best_run.get_details()['runDefinition']['Arguments']

print('Best Run Id: ', best_run.id)
print('\n Accuracy:', best_run_metrics['accuracy'])
print('\n learning rate:',parameter_values[3])
print('\n keep probability:',parameter_values[5])
print('\n batch size:',parameter_values[7])
```

## <a name="sample-notebook"></a>Notebook di esempio
Vedere i notebook Train-iperparameter-* in questa cartella:
* [how-to-use-azureml/training-with-deep-learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/training-with-deep-learning)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Passaggi successivi
* [Tenere traccia di un esperimento](how-to-track-experiments.md)
* [Distribuire un modello con training](how-to-deploy-and-where.md)
