---
title: Conteggi negli esperimenti AutoML
titleSuffix: Azure Machine Learning
description: Informazioni sulle impostazioni di conteggi Azure Machine Learning offerte e sul modo in cui la progettazione delle funzionalità è supportata negli esperimenti di Machine Learning automatici.
author: nibaccam
ms.author: nibaccam
ms.reviewer: nibaccam
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.date: 05/28/2020
ms.openlocfilehash: a5eb24b5420431a43afa2ffd006ac821f0e907c9
ms.sourcegitcommit: faeabfc2fffc33be7de6e1e93271ae214099517f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88185758"
---
# <a name="featurization-in-automated-machine-learning"></a>Conteggi in Machine Learning automatizzato

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questa guida si apprenderà:

- Quali impostazioni di conteggi Azure Machine Learning offerte.
- Come personalizzare queste funzionalità per gli [esperimenti di Machine Learning automatizzati](concept-automated-ml.md).

La *progettazione di funzioni* è il processo di utilizzo della conoscenza del dominio dei dati per creare funzionalità che consentono agli algoritmi di Machine Learning (ml) di acquisire maggiore familiarità. In Azure Machine Learning, le tecniche di ridimensionamento e scalabilità dei dati vengono applicate per semplificare la progettazione delle funzionalità. Insieme, queste tecniche e questa funzionalità di progettazione sono denominate *conteggi* in esperimenti automatici di Machine Learning, o *AutoML*.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si conosca già come configurare un esperimento di AutoML. Per informazioni sulla configurazione, vedere gli articoli seguenti:

- Per un'esperienza di codice per la prima volta, [configurare gli esperimenti di Machine Learning automatici usando il Azure Machine Learning SDK per Python](how-to-configure-auto-train.md).
- Per un'esperienza low-code o senza codice: [creare, rivedere e distribuire modelli di Machine Learning automatici usando il Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

## <a name="configure-featurization"></a>Configurare conteggi

In ogni esperimento di Machine Learning automatizzato, le [tecniche di ridimensionamento automatico e di normalizzazione](#featurization) vengono applicate ai dati per impostazione predefinita. Queste tecniche sono tipi di conteggi che aiutano *determinati* algoritmi sensibili alle funzionalità su scale diverse. Tuttavia, è anche possibile abilitare conteggi aggiuntivi, ad esempio l'imputazione, la *codifica*e le *trasformazioni* *dei valori mancanti*.

> [!NOTE]
> I passaggi per automatizzare conteggi di Machine Learning, ad esempio la normalizzazione delle funzioni, la gestione dei dati mancanti o la conversione di testo in numerici, diventano parte del modello sottostante. Quando si usa il modello per le stime, gli stessi passaggi di conteggi applicati durante il training vengono applicati automaticamente ai dati di input.

Per gli esperimenti configurati con Python SDK, è possibile abilitare o disabilitare l'impostazione conteggi e specificare ulteriormente i passaggi conteggi da usare per l'esperimento. Se si usa Azure Machine Learning Studio, vedere la [procedura per abilitare conteggi](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

Nella tabella seguente vengono illustrate le impostazioni accettate per `featurization` nella [classe AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig):

|Configurazione di conteggi | Descrizione|
------------- | ------------- |
|`"featurization": 'auto'`| Specifica che, come parte della pre-elaborazione, [i passaggi per i dati Guardrails e conteggi](#featurization) devono essere eseguiti automaticamente. Questa è l'impostazione predefinita.|
|`"featurization": 'off'`| Specifica che i passaggi di conteggi non devono essere eseguiti automaticamente.|
|`"featurization":`&nbsp;`'FeaturizationConfig'`| Specifica che devono essere usati i passaggi di conteggi personalizzati. [Informazioni su come personalizzare la definizione delle funzionalità](#customize-featurization).|

<a name="featurization"></a>

## <a name="automatic-featurization"></a>Automatic featurization (Definizione automatica funzionalità)

Nella tabella seguente sono riepilogate le tecniche applicate automaticamente ai dati. Queste tecniche vengono applicate per gli esperimenti configurati con l'SDK o con Studio. Per disabilitare questo comportamento, impostare `"featurization": 'off'` nell' `AutoMLConfig` oggetto.

> [!NOTE]
> Se si prevede di esportare i modelli creati da AutoML in un [modello ONNX](concept-onnx.md), nel formato ONNX sono supportate solo le opzioni conteggi indicate con un asterisco ("*"). Altre informazioni sulla [conversione di modelli in ONNX](concept-automated-ml.md#use-with-onnx).

|&nbsp;Procedura conteggi| Descrizione |
| ------------- | ------------- |
|**Elimina la cardinalità elevata o nessuna funzionalità di varianza*** |Eliminare queste funzionalità dai set di training e di convalida. Si applica alle funzionalità con tutti i valori mancanti, con lo stesso valore in tutte le righe o con cardinalità elevata (ad esempio, hash, ID o GUID).|
|**Imputare i valori mancanti*** |Per le funzionalità numeriche, imputare alla media dei valori nella colonna.<br/><br/>Per le funzionalità categoriche, imputare il valore più frequente.|
|**Genera funzionalità aggiuntive*** |Per le caratteristiche di tipo DateTime: anno, mese, giorno, giorno della settimana, giorno dell'anno, trimestre, settimana dell'anno, ora, minuti, secondi.<br><br> *Per le attività di previsione,* vengono create queste funzionalità di data/ora aggiuntive: ISO anno, semestre semestrale, calendario mese come stringa, settimana, giorno della settimana come stringa, giorno del trimestre, giorno dell'anno, AM/PM (0 se l'ora è precedente a mezzogiorno (12.00), 1 in caso contrario), AM/PM come stringa, ora del giorno (12 ore)<br/><br/>Per le funzionalità di testo: frequenza dei termini basata su unigrammi, bigrammi e trigrammi. Altre informazioni su [come eseguire questa operazione con Bert.](#bert-integration)|
|**Trasformare e codificare***|Trasforma le funzionalità numeriche con pochi valori univoci nelle funzionalità categoriche.<br/><br/>La codifica One-Hot viene utilizzata per le funzionalità categoriche con cardinalità bassa. Per le funzionalità categoriche con cardinalità elevata, viene usata la codifica One-Hot-hash.|
|**Incorporamenti di parole**|Un featurizer di testo converte i vettori di token di testo in vettori di frase usando un modello con training preliminare. Il vettore di incorporamento di ogni parola in un documento viene aggregato con il resto per produrre un vettore di funzionalità del documento.|
|**Codifiche di destinazione**|Per le funzionalità categoriche, questo passaggio esegue il mapping di ogni categoria con un valore di destinazione medio per i problemi di regressione e la probabilità della classe per ogni classe per i problemi di classificazione. Viene applicata la ponderazione basata sulla frequenza e la convalida incrociata k-fold per ridurre l'overfitting del mapping e del rumore causato dalle categorie di dati di tipo sparse.|
|**Codifica di destinazione del testo**|Per l'input di testo, viene usato un modello lineare in stack con elenco di parole per generare la probabilità di ogni classe.|
|**Peso dell’evidenza**|Calcola il peso dell’evidenza come misura della correlazione delle colonne di categoria con la colonna di destinazione. Guai viene calcolato come log del rapporto tra le probabilità out-of-Class e le probabilità out-of-Class. Questo passaggio produce una colonna della funzionalità numerica per classe e rimuove la necessità di imputare in modo esplicito i valori mancanti e il trattamento degli outlier.|
|**Distanza cluster**|Addestra un modello di clustering k-means su tutte le colonne numeriche. Produce *k* nuove funzionalità (una nuova funzionalità numerica per ogni cluster) che contengono la distanza di ogni campione al centro di ogni cluster.|

## <a name="data-guardrails"></a>Protezione dati

*I dati Guardrails* consentono di identificare i potenziali problemi relativi ai dati, ad esempio valori mancanti o [squilibrio della classe](concept-manage-ml-pitfalls.md#identify-models-with-imbalanced-data). Consentono inoltre di eseguire azioni correttive per ottenere risultati migliori.

Guardrails dati applicati:

- **Per gli esperimenti SDK**: quando `"featurization": 'auto'` vengono specificati i parametri o `validation=auto` nell' `AutoMLConfig` oggetto.
- **Per gli esperimenti in studio**: quando è abilitata la conteggi automatica.

È possibile esaminare i dati Guardrails per l'esperimento:

- Impostando `show_output=True` quando si invia un esperimento utilizzando l'SDK.

- In studio, nella scheda **Guardrails dati** dell'esecuzione automatica di ml.

### <a name="data-guardrail-states"></a>Stati di barriera dati

Guardrails dati visualizzano uno dei tre stati seguenti:

|State| Descrizione |
|----|---- |
|**Riuscito**| Non sono stati rilevati problemi relativi ai dati e non è richiesta alcuna azione da te. |
|**Completato**| Le modifiche sono state applicate ai dati. Si consiglia di esaminare le azioni correttive eseguite da AutoML, per garantire che le modifiche siano allineate con i risultati previsti. |
|**Avvisato**| È stato rilevato un problema relativo ai dati ma non è stato possibile rimediare. Si consiglia di esaminare e risolvere il problema.|

### <a name="supported-data-guardrails"></a>Guardrails dati supportati

La tabella seguente descrive i Guardrails di dati attualmente supportati e gli Stati associati che potrebbero essere visualizzati quando si invia l'esperimento:

Protezione|Stato|Condizione&nbsp;per il&nbsp;trigger
---|---|---
**Imputazione di valori di funzionalità mancanti** |Passed <br><br><br> Fine| Nei dati di training non sono stati rilevati valori di funzionalità mancanti. Altre informazioni sull' [imputazione di valori mancanti.](https://docs.microsoft.com/azure/machine-learning/how-to-use-automated-ml-for-ml-models#advanced-featurization-options) <br><br> Sono stati rilevati valori di funzionalità mancanti nei dati di training e sono stati imputati.
**Gestione di funzionalità ad alta cardinalità** |Passed <br><br><br> Fine| Gli input sono stati analizzati e non sono state rilevate funzionalità di cardinalità elevata. <br><br> Sono state rilevate funzionalità di cardinalità elevata negli input ed è stata gestita.
**Gestione della suddivisione della convalida** |Fine| La configurazione della convalida è stata impostata su `'auto'` e i dati di training contengono *meno di 20.000 righe*. <br> Ogni iterazione del modello sottoposto a training è stata convalidata tramite la convalida incrociata. Altre informazioni sui [dati di convalida](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train#train-and-validation-data). <br><br> La configurazione della convalida è stata impostata su `'auto'` e i dati di training contenevano *più di 20.000 righe*. <br> I dati di input sono stati suddivisi in un set di dati di training e un set di dati di convalida per la convalida del modello.
**Rilevamento bilanciamento classi** |Passed <br><br>Avvisato <br><br>Fine | Gli input sono stati analizzati e tutte le classi sono bilanciate nei dati di training. Un set di dati è considerato bilanciato se ogni classe presenta una rappresentazione corretta del set di dati, misurata in base al numero e al rapporto degli esempi. <br><br><br> Sono state rilevate classi non bilanciate negli input. Per correggere la distorsione del modello, risolvere il problema di bilanciamento del carico. Altre informazioni sui [dati sbilanciati](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data).<br><br><br> Sono state rilevate classi sbilanciate negli input e la logica di sweep ha determinato l'applicazione del bilanciamento del carico.
**Rilevamento dei problemi di memoria** |Passed <br><br><br><br> Fine |<br> Sono stati analizzati i valori selezionati (orizzonte, lag, finestra in sequenza) e non sono stati rilevati potenziali problemi di memoria insufficiente. Altre informazioni sulle [configurazioni di previsione](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)delle serie temporali. <br><br><br>I valori selezionati (orizzonte, lag, finestra in sequenza) sono stati analizzati e potrebbero causare un esaurimento della memoria da parte dell'esperimento. Le configurazioni di ritardo o della finestra in sequenza sono state disattivate.
**Rilevamento della frequenza** |Passed <br><br><br><br> Fine |<br> La serie temporale è stata analizzata e tutti i punti dati sono allineati con la frequenza rilevata. <br> <br> La serie temporale è stata analizzata e sono stati rilevati punti dati che non si allineano alla frequenza rilevata. Questi punti dati sono stati rimossi dal set di dati. Altre informazioni sulla [preparazione dei dati per la previsione delle serie temporali](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data).

## <a name="customize-featurization"></a>Personalizzare conteggi

È possibile personalizzare le impostazioni di conteggi per assicurarsi che i dati e le funzionalità usati per il training del modello ML provochino stime pertinenti.

Per personalizzare featurizations, specificare  `"featurization": FeaturizationConfig` nell' `AutoMLConfig` oggetto. Se si usa Azure Machine Learning Studio per l'esperimento, vedere l'articolo sulle [procedure](how-to-use-automated-ml-for-ml-models.md#customize-featurization). Per personalizzare conteggi per i tipi di attività di previsione, fare riferimento alle procedure per la [previsione](how-to-auto-train-forecast.md#customize-featurization).

Le personalizzazioni supportate includono:

|Personalizzazione|Definizione|
|--|--|
|**Aggiornamento dello scopo della colonna**|Esegue l'override del tipo di funzionalità rilevato automaticamente per la colonna specificata.|
|**Aggiornamento dei parametri del trasformatore** |Aggiornare i parametri per il trasformatore specificato. Attualmente supporta l' *imputatore* (medio, più frequente e mediano) e *HashOneHotEncoder*.|
|**Eliminazione delle colonne** |Specifica le colonne da eliminare dall'trasformato.|
|**Blocco dei trasformatori**| Specifica i trasformatori di blocco da usare nel processo conteggi.|

Creare l' `FeaturizationConfig` oggetto usando le chiamate API:

```python
featurization_config = FeaturizationConfig()
featurization_config.blocked_transformers = ['LabelEncoder']
featurization_config.drop_columns = ['aspiration', 'stroke']
featurization_config.add_column_purpose('engine-size', 'Numeric')
featurization_config.add_column_purpose('body-style', 'CategoricalHash')
#default strategy mean, add transformer param for for 3 columns
featurization_config.add_transformer_params('Imputer', ['engine-size'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['city-mpg'], {"strategy": "median"})
featurization_config.add_transformer_params('Imputer', ['bore'], {"strategy": "most_frequent"})
featurization_config.add_transformer_params('HashOneHotEncoder', [], {"number_of_bits": 3})
```

## <a name="featurization-transparency"></a>Trasparenza conteggi

Ogni modello AutoML ha conteggi applicato automaticamente.  Conteggi include la progettazione automatica delle funzioni (quando `"featurization": 'auto'` ) e la scalabilità e la normalizzazione, che influiscano quindi sull'algoritmo selezionato e sui relativi valori iperparametri. AutoML supporta metodi diversi per garantire la visibilità degli elementi applicati al modello.

Prendere in considerazione questo esempio di previsione:

+ Sono disponibili quattro funzionalità di input: A (numerico), B (numerico), C (numerico), D (DateTime).
+ La funzione numerica C viene eliminata perché è una colonna ID con tutti i valori univoci.
+ Le funzionalità numeriche A e B presentano valori mancanti e pertanto sono imputate dal valore medio.
+ La funzionalità DateTime D è trasformato in 11 diverse funzionalità progettate.

Per ottenere queste informazioni, usare l' `fitted_model` output dell'esecuzione dell'esperimento Machine Learning automatizzato.

```python
automl_config = AutoMLConfig(…)
automl_run = experiment.submit(automl_config …)
best_run, fitted_model = automl_run.get_output()
```
### <a name="automated-feature-engineering"></a>Definizione delle funzionalità automatizzata 
`get_engineered_feature_names()`Restituisce un elenco di nomi di funzionalità progettati.

  >[!Note]
  >Usare 'timeseriestransformer' per task='forecasting' oppure usare 'datatransformer' per l'attività 'regression' o 'classification'.

  ```python
  fitted_model.named_steps['timeseriestransformer']. get_engineered_feature_names ()
  ```

Questo elenco include tutti i nomi delle funzionalità definite. 

  ```
  ['A', 'B', 'A_WASNULL', 'B_WASNULL', 'year', 'half', 'quarter', 'month', 'day', 'hour', 'am_pm', 'hour12', 'wday', 'qday', 'week']
  ```

`get_featurization_summary()`Ottiene un riepilogo conteggi di tutte le funzionalità di input.

  ```python
  fitted_model.named_steps['timeseriestransformer'].get_featurization_summary()
  ```

Output

  ```
  [{'RawFeatureName': 'A',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'B',
    'TypeDetected': 'Numeric',
    'Dropped': 'No',
    'EngineeredFeatureCount': 2,
    'Tranformations': ['MeanImputer', 'ImputationMarker']},
   {'RawFeatureName': 'C',
    'TypeDetected': 'Numeric',
    'Dropped': 'Yes',
    'EngineeredFeatureCount': 0,
    'Tranformations': []},
   {'RawFeatureName': 'D',
    'TypeDetected': 'DateTime',
    'Dropped': 'No',
    'EngineeredFeatureCount': 11,
    'Tranformations': ['DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime','DateTime']}]
  ```

   |Output|Definizione|
   |----|--------|
   |RawFeatureName|Nome della funzionalità o della colonna di input del set di dati specificato.|
   |TypeDetected|Tipo di dati rilevato della funzionalità di input.|
   |Dropped|Indica se la funzionalità di input è stata eliminata o usata.|
   |EngineeringFeatureCount|Numero di funzionalità generate tramite trasformazioni della definizione delle funzionalità automatizzata.|
   |Trasformazioni|Elenco delle trasformazioni applicate alle funzionalità di input per generare le funzionalità definite.|

### <a name="scaling-and-normalization"></a>Ridimensionamento e normalizzazione

Per comprendere il ridimensionamento o la normalizzazione e l'algoritmo selezionato con i relativi valori di iperparametri, usare `fitted_model.steps` . 

L'output di esempio seguente viene eseguito `fitted_model.steps` per un'esecuzione scelta:

```
[('RobustScaler', 
  RobustScaler(copy=True, 
  quantile_range=[10, 90], 
  with_centering=True, 
  with_scaling=True)), 

  ('LogisticRegression', 
  LogisticRegression(C=0.18420699693267145, class_weight='balanced', 
  dual=False, 
  fit_intercept=True, 
  intercept_scaling=1, 
  max_iter=100, 
  multi_class='multinomial', 
  n_jobs=1, penalty='l2', 
  random_state=None, 
  solver='newton-cg', 
  tol=0.0001, 
  verbose=0, 
  warm_start=False))
```

Per ottenere altri dettagli, usare la funzione helper seguente: 

```python
from pprint import pprint

def print_model(model, prefix=""):
    for step in model.steps:
        print(prefix + step[0])
        if hasattr(step[1], 'estimators') and hasattr(step[1], 'weights'):
            pprint({'estimators': list(
                e[0] for e in step[1].estimators), 'weights': step[1].weights})
            print()
            for estimator in step[1].estimators:
                print_model(estimator[1], estimator[0] + ' - ')
        else:
            pprint(step[1].get_params())
            print()

print_model(model)
```

Questa funzione helper restituisce l'output seguente per una particolare esecuzione utilizzando `LogisticRegression with RobustScalar` come algoritmo specifico.

```
RobustScaler
{'copy': True,
'quantile_range': [10, 90],
'with_centering': True,
'with_scaling': True}

LogisticRegression
{'C': 0.18420699693267145,
'class_weight': 'balanced',
'dual': False,
'fit_intercept': True,
'intercept_scaling': 1,
'max_iter': 100,
'multi_class': 'multinomial',
'n_jobs': 1,
'penalty': 'l2',
'random_state': None,
'solver': 'newton-cg',
'tol': 0.0001,
'verbose': 0,
'warm_start': False}
```

### <a name="predict-class-probability"></a>Stimare la probabilità della classe

I modelli prodotti con il Machine Learning automatizzato hanno tutti oggetti wrapper che rispecchiano la funzionalità della classe di origine open source. La maggior parte degli oggetti wrapper del modello di classificazione restituiti dal Machine Learning automatizzato implementa la funzione `predict_proba()` che accetta dati di esempio di tipo matrice o matrice sparsa delle funzionalità (valori X) e restituisce una matrice n-dimensionale di ogni esempio e la probabilità della rispettiva classe.

Si supponga di avere recuperato l'esecuzione migliore e il modello adattato usando le stesse chiamate precedenti, è possibile chiamare `predict_proba()` direttamente dal modello adattato specificando un `X_test` di esempio nel formato appropriato, a seconda del tipo di modello.

```python
best_run, fitted_model = automl_run.get_output()
class_prob = fitted_model.predict_proba(X_test)
```

Se il modello sottostante non supporta la funzione `predict_proba()` o il formato non è corretto, verrà generata un'eccezione specifica della classe del modello. Per esempi relativi all'implementazione di questa funzione per i diversi tipi di modello, vedere la documentazione di riferimento di [RandomForestClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.RandomForestClassifier.html#sklearn.ensemble.RandomForestClassifier.predict_proba) e [XGBoost](https://xgboost.readthedocs.io/en/latest/python/python_api.html).

## <a name="bert-integration"></a>Integrazione con BERT

[Bert](https://techcommunity.microsoft.com/t5/azure-ai/how-bert-is-integrated-into-azure-automated-machine-learning/ba-p/1194657) viene usato nel livello conteggi di AutoML. In questo livello, se una colonna contiene testo libero o altri tipi di dati come timestamp o numeri semplici, viene applicato conteggi di conseguenza.

Per BERT, il modello viene ottimizzato e sottoposto a training usando le etichette fornite dall'utente. Da qui, gli incorporamenti dei documenti vengono restituiti come funzionalità insieme ad altri, come le funzionalità basate su timestamp, giorno della settimana. 


### <a name="bert-steps"></a>Passaggi BERT

Per richiamare BERT, è necessario impostare  `enable_dnn: True` nel automl_settings e usare un calcolo GPU (ad esempio `vm_size = "STANDARD_NC6"` , o una GPU più elevata). Se viene usato un calcolo della CPU, anziché BERT, AutoML Abilita BiLSTM DNN featurizer.

AutoML esegue i passaggi seguenti per BERT. 

1. **Pre-elaborazione e suddivisione in token di tutte le colonne di testo**. Ad esempio, il trasformatore "StringCast" si trova nel riepilogo conteggi del modello finale. Un esempio di come produrre il riepilogo conteggi del modello è disponibile in [questo notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb).

2. **Concatena tutte le colonne di testo in una singola colonna di testo**, `StringConcatTransformer` di conseguenza nel modello finale. 

    La nostra implementazione di BERT limita la lunghezza totale del testo di un campione di training a 128 token. Ciò significa che tutte le colonne di testo concatenate devono avere una lunghezza massima di 128 token. Se sono presenti più colonne, ogni colonna deve essere eliminata, in modo che questa condizione venga soddisfatta. In caso contrario, per le colonne concatenate di lunghezza >128 token il livello tokenizer di BERT tronca questo input a 128 token.

3. **Come parte della funzionalità di sweep, AutoML confronta BERT con le funzionalità di base (contenitore di parole) in un campione di dati.** Questo confronto determina se BERT darebbe un miglioramento dell'accuratezza. Se BERT è migliore della linea di base, AutoML USA BERT per il testo conteggi per l'intero dato. In tal caso, si vedrà `PretrainedTextDNNTransformer` nel modello finale.

BERT viene in genere eseguito più a lungo rispetto ad altri featurizers. Per ottenere prestazioni ottimali, è consigliabile usare "STANDARD_NC24r" o "STANDARD_NC24rs_V3" per le funzionalità RDMA. 

AutoML distribuirà il training BERT tra più nodi, se disponibili, fino a un massimo di otto nodi. Questa operazione può essere eseguita nell' `AutoMLConfig` oggetto impostando il `max_concurrent_iterations` parametro su un valore maggiore di 1. 
### <a name="supported-languages"></a>Lingue supportate

AutoML supporta attualmente circa 100 lingue e, a seconda della lingua del set di dati, AutoML sceglie il modello BERT appropriato. Per i dati in tedesco, viene usato il modello di BERT tedesco. Per la lingua inglese, viene usato il modello di BERT inglese. Per tutti gli altri linguaggi, viene usato il modello di BERT multilingue.

Nel codice riportato di seguito viene attivato il modello tedesco BERT, dal momento che la lingua del set di dati è specificata `deu` , il codice della lingua di tre lettere per il tedesco in base alla [classificazione ISO](https://iso639-3.sil.org/code/deu):

```python
from azureml.automl.core.featurization import FeaturizationConfig

featurization_config = FeaturizationConfig(dataset_language='deu')

automl_settings = {
    "experiment_timeout_minutes": 120,
    "primary_metric": 'accuracy', 
# All other settings you want to use 
    "featurization": featurization_config,
    
  "enable_dnn": True, # This enables BERT DNN featurizer
    "enable_voting_ensemble": False,
    "enable_stack_ensemble": False
}
```

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come configurare gli esperimenti di Machine Learning automatici:

    * Per un'esperienza di codice per la prima volta, [configurare gli esperimenti di Machine Learning automatici usando l'SDK Azure Machine Learning](how-to-configure-auto-train.md).
    * Per un'esperienza low-code o senza codice: [creare esperimenti di Machine Learning automatici in Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

* Altre informazioni su [come e dove distribuire un modello](how-to-deploy-and-where.md).

* Altre informazioni su [come eseguire il training di un modello di regressione usando Machine Learning automatizzato](tutorial-auto-train-models.md) o [come eseguire il training usando Machine Learning automatizzato in una risorsa remota](how-to-auto-train-remote.md).
