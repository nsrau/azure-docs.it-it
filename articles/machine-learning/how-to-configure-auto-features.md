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
ms.topic: how-to
ms.date: 05/28/2020
ms.custom: seodec18
ms.openlocfilehash: 11bb692027d8a2e5033c7bdaf8eb2c565d1562b0
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86205702"
---
# <a name="featurization-in-automated-machine-learning"></a>Conteggi in Machine Learning automatizzato

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questa guida si apprenderà come:

- Quali impostazioni di conteggi Azure Machine Learning offerte.
- Come personalizzare queste funzionalità per gli [esperimenti di Machine Learning automatizzati](concept-automated-ml.md).

La *progettazione di funzioni* è il processo di utilizzo della conoscenza del dominio dei dati per creare funzionalità che consentono agli algoritmi di Machine Learning (ml) di acquisire maggiore familiarità. In Azure Machine Learning, le tecniche di ridimensionamento e scalabilità dei dati vengono applicate per semplificare la progettazione delle funzionalità. Insieme, queste tecniche e questa funzionalità di progettazione sono denominate *conteggi* in esperimenti automatici di Machine Learning, o *AutoML*.

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
|`"featurization": 'auto'`| Specifica che, come parte della pre-elaborazione, [i passaggi per i dati Guardrails e conteggi](#featurization) devono essere eseguiti automaticamente. È l'impostazione predefinita.|
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
|**Genera funzionalità aggiuntive*** |Per le caratteristiche di tipo DateTime: anno, mese, giorno, giorno della settimana, giorno dell'anno, trimestre, settimana dell'anno, ora, minuti, secondi.<br/><br/>Per le funzionalità di testo: frequenza dei termini basata su unigrammi, bigrammi e trigrammi.|
|**Trasformare e codificare***|Trasforma le funzionalità numeriche con pochi valori univoci nelle funzionalità categoriche.<br/><br/>La codifica One-Hot viene utilizzata per le funzionalità categoriche con cardinalità bassa. Per le funzionalità categoriche con cardinalità elevata, viene usata la codifica One-Hot-hash.|
|**Incorporamenti di parole**|Un featurizer di testo converte i vettori di token di testo in vettori di frase usando un modello con training. Il vettore di incorporamento di ogni parola in un documento viene aggregato con il resto per produrre un vettore di funzionalità del documento.|
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
**Rilevamento bilanciamento classi** |Passed <br><br><br><br><br> Avvisato | Gli input sono stati analizzati e tutte le classi sono bilanciate nei dati di training. Un set di dati è considerato bilanciato se ogni classe presenta una rappresentazione corretta del set di dati, misurata in base al numero e al rapporto degli esempi. <br><br><br> Sono state rilevate classi non bilanciate negli input. Per correggere la distorsione del modello, risolvere il problema di bilanciamento del carico. Altre informazioni sui [dati sbilanciati](https://docs.microsoft.com/azure/machine-learning/concept-manage-ml-pitfalls#identify-models-with-imbalanced-data).
**Rilevamento dei problemi di memoria** |Passed <br><br><br><br> Fine |<br> Sono stati analizzati i valori selezionati (orizzonte, lag, finestra in sequenza) e non sono stati rilevati potenziali problemi di memoria insufficiente. Altre informazioni sulle [configurazioni di previsione](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#configure-and-run-experiment)delle serie temporali. <br><br><br>I valori selezionati (orizzonte, lag, finestra in sequenza) sono stati analizzati e potrebbero causare un esaurimento della memoria da parte dell'esperimento. Le configurazioni di ritardo o della finestra in sequenza sono state disattivate.
**Rilevamento della frequenza** |Passed <br><br><br><br> Fine |<br> La serie temporale è stata analizzata e tutti i punti dati sono allineati con la frequenza rilevata. <br> <br> La serie temporale è stata analizzata e sono stati rilevati punti dati che non si allineano alla frequenza rilevata. Questi punti dati sono stati rimossi dal set di dati. Altre informazioni sulla [preparazione dei dati per la previsione delle serie temporali](https://docs.microsoft.com/azure/machine-learning/how-to-auto-train-forecast#preparing-data).

## <a name="customize-featurization"></a>Personalizzare conteggi

È possibile personalizzare le impostazioni di conteggi per assicurarsi che i dati e le funzionalità usati per il training del modello ML provochino stime pertinenti.

Per personalizzare featurizations, specificare  `"featurization": FeaturizationConfig` nell' `AutoMLConfig` oggetto. Se si usa Azure Machine Learning Studio per l'esperimento, vedere l'articolo sulle [procedure](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

Le personalizzazioni supportate includono:

|Personalizzazione|Definizione|
|--|--|
|**Aggiornamento dello scopo della colonna**|Consente di eseguire l'override del tipo di funzionalità per la colonna specificata.|
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

## <a name="next-steps"></a>Passaggi successivi

* Informazioni su come configurare gli esperimenti di Machine Learning automatici:

    * Per un'esperienza di codice per la prima volta, [configurare gli esperimenti di Machine Learning automatici usando l'SDK Azure Machine Learning](how-to-configure-auto-train.md).
    * Per un'esperienza low-code o senza codice: [creare esperimenti di Machine Learning automatici in Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md).

* Altre informazioni su [come e dove distribuire un modello](how-to-deploy-and-where.md).

* Altre informazioni su [come eseguire il training di un modello di regressione usando Machine Learning automatizzato](tutorial-auto-train-models.md) o [come eseguire il training usando Machine Learning automatizzato in una risorsa remota](how-to-auto-train-remote.md).
