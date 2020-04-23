---
title: Che cos'è ML / AutoML automatizzato
titleSuffix: Azure Machine Learning
description: Informazioni su come Azure Machine Learning può scegliere automaticamente un algoritmo e generare un modello da esso per risparmiare tempo usando i parametri e i criteri specificati per selezionare l'algoritmo migliore per il modello.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: f592a7f5a4af38988bcf433f0adc89d9be7579cb
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082010"
---
# <a name="what-is-automated-machine-learning-automl"></a>Che cos'è l'apprendimento automatico automatico (AutoML)?

L'apprendimento automatico, noto anche come ML automatizzato o AutoML, è il processo di automazione delle attività iterative e dispendiose in termini di tempo dello sviluppo di modelli di apprendimento automatico. Consente a data scientist, analisti e sviluppatori di creare modelli di Machine Learning con scalabilità elevata, efficienza e produttività, mantenendo al contempo la qualità dei modelli. Automated ML si basa su una svolta dalla nostra [divisione Microsoft Research](https://arxiv.org/abs/1705.05355).

Lo sviluppo di modelli di apprendimento automatico tradizionale richiede un utilizzo intensivo delle risorse e richiede una conoscenza e un tempo di dominio significativi per produrre e confrontare decine di modelli. Con l'apprendimento automatico, accelererai il tempo necessario per ottenere modelli ML pronti per la produzione con grande facilità ed efficienza.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Quando usare AutoML: classificare, regressione & previsioneWhen to use AutoML: classify, regression, & forecast

Applicare il processo di gestione automatica delle macchine virtuali quando si vuole che Azure Machine Learning esegga e sintonizza un modello usando la metrica di destinazione specificata. Automated ML democratizza il processo di sviluppo del modello di apprendimento automatico e consente ai suoi utenti, indipendentemente dalle loro competenze di data science, di identificare una pipeline di apprendimento automatico end-to-end per qualsiasi problema.

Gli scienziati dei dati, gli analisti e gli sviluppatori di tutti i settori possono utilizzare l'unità di gestione automatica per:
+ Implementare soluzioni ML senza un'ampia conoscenza della programmazione
+ Risparmia tempo e risorse
+ Sfruttare le best practice di analisi scientifica dei dati
+ Fornire una risoluzione dei problemi agile

### <a name="classification"></a>Classificazione

La classificazione è un'attività di apprendimento automatico comune. La classificazione è un tipo di apprendimento supervisionato in cui i modelli apprendono usando i dati di training e applicano tali apprendimenti anuovi dati. Azure Machine Learning offre funzionalità specifiche per queste attività, ad esempio i featurizzatori di testo di rete neurale profondo per la classificazione. Ulteriori informazioni sulle [opzioni di featurizzazione](how-to-use-automated-ml-for-ml-models.md#featurization). 

L'obiettivo principale dei modelli di classificazione è prevedere in quali categorie rientrano i nuovi dati in base agli apprendimenti dai dati di training. Esempi di classificazione comuni includono il rilevamento delle frodi, il riconoscimento della grafia e il rilevamento di oggetti.  Scopri di più e vedi un esempio di [classificazione con l'apprendimento automatico automatizzato.](tutorial-train-models-with-aml.md)

Vedere esempi di classificazione e apprendimento automatico automatizzato in questi blocchi appunti Python: [Rilevamento delle frodi](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb), [previsione di marketing](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb)e [classificazione dei dati](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-text-dnn/auto-ml-classification-text-dnn.ipynb) dei newsgroup

### <a name="regression"></a>Regressione
Analogamente alla classificazione, le attività di regressione sono anche un'attività di apprendimento supervisionato comune. Azure Machine Learning offre [funzionalità specifiche per queste attività.](how-to-use-automated-ml-for-ml-models.md#featurization)

Diversi dalla classificazione in cui i valori di output previsti sono categorici, i modelli di regressione prevedono valori di output numerici basati su predittori indipendenti. Nella regressione, l'obiettivo è quello di aiutare a stabilire la relazione tra quelle variabili predittive indipendenti stimando l'impatto di una variabile sulle altre. Ad esempio, il prezzo dell'automobile basato su caratteristiche come, chilometraggio del gas, valutazione di sicurezza, ecc. Scopri di più e vedi un esempio di [regressione con l'apprendimento automatico automatico](tutorial-auto-train-models.md).

Vedere esempi di regressione e apprendimento automatico automatizzato per le stime in questi notebook Python: [previsione delle prestazioni della CPU](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb), 

### <a name="time-series-forecasting"></a>Previsione di serie temporali

Le previsioni di costruzione sono parte integrante di qualsiasi azienda, che si tratti di ricavi, scorte, vendite o domanda dei clienti. È possibile utilizzare ML automatizzato per combinare tecniche e approcci e ottenere una previsione di serie temporali consigliata e di alta qualità. Scopri di più con questa procedura: [apprendimento automatico automatizzato per la previsione di serie temporali.](how-to-auto-train-forecast.md) 

Un esperimento automatizzato di serie temporali viene considerato come un problema di regressione multivariato. I valori delle serie temporali passati sono "pivoted" per diventare dimensioni aggiuntive per il regressore insieme ad altri predittori. Questo approccio, a differenza dei metodi classici di serie temporali, ha il vantaggio di incorporare naturalmente più variabili contestuali e la loro relazione tra loro durante il training. L'infrastruttura ML automatizzata apprende un singolo modello ramificato internamente per tutti gli elementi nel set di dati e negli orizzonti di stima. Diventa quindi possibile un numero maggiore di dati per stimare i parametri del modello e la generalizzazione in serie invisibili.

La configurazione avanzata delle previsioni include:Advanced forecasting configuration includes:
* rilevamento e featurizzazione delle vacanze
* dischi di serie temporali e DNN (Auto-ARIMA, Profeta, ForecastTCN)
* molti modelli supportano il raggruppamento
* convalida incrociata dell'origine continua
* ritardi configurabili
* funzionalità di aggregazione per finestre in sequenza


Vedere esempi di regressione e apprendimento automatico automatico per le stime in questi blocchi appunti Python: [Sales Forecasting](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb), [Demand Forecasting](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb)e [Beverage Production Forecast](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb).

## <a name="how-automl-works"></a>Funzionamento di AutoML

Durante il training, Azure Machine Learning crea una serie di pipeline in parallelo che provano algoritmi e parametri diversi per l'utente. Il servizio scorre gli algoritmi di ML associati alle selezioni di funzionalità, in cui ogni iterazione produce un modello con un punteggio di training. Maggiore è il punteggio, migliore è il modello è considerato "adattare" i dati.  Si fermerà una volta che raggiunge i criteri di uscita definiti nell'esperimento. 

Usando Azure Machine Learning è possibile progettare ed eseguire gli esperimenti di formazione sulla mL automatizzati con questi passaggi:Using **Azure Machine Learning**, you can design and run your automated ML training experiments with these steps:

1. **Identificare il problema ML** da risolvere: classificazione, previsione o regressione

1. **Scegli se vuoi usare Python SDK o l'esperienza web**di studio: scopri la parità tra Python SDK e l'esperienza [web di studio.](#parity)

   * Per un'esperienza di codice limitata o assenza, provare l'esperienza Web di Azure Machine Learning studio all'indirizzo[https://ml.azure.com](https://ml.azure.com/)  
   * Per gli sviluppatori Python, consulta [Azure Machine Learning Python SDK](how-to-configure-auto-train.md) 

    [!INCLUDE [aml-applies-to-enterprise-sku](../../includes/aml-applies-to-enterprise-sku-inline.md)]  
    
1. **Specificare l'origine e il formato dei dati di training etichettati:** matrici Numpy o frame di dati Pandas

1. **Configurare la destinazione di calcolo per**il training del modello, ad esempio il computer locale, i calcoli di Azure Machine Learning, le [macchine virtuali remote o Azure Databrick.](how-to-set-up-training-targets.md)  Informazioni sulla formazione automatizzata [su una risorsa remota](how-to-auto-train-remote.md).

1. **Configurare i parametri** di apprendimento automatico che determinano il numero di iterazioni su modelli diversi, le impostazioni dei parametri iperparametrici, la pre-elaborazione/featurizzazione avanzata e le metriche da esaminare quando si determina il modello migliore.  
1. **Inviare l'esecuzione della formazione.**

1. **Esaminare i risultati** 

Nella seguente figura viene illustrato questo processo. 
![Apprendimento automatico](./media/concept-automated-ml/automl-concept-diagram2.png)


È inoltre possibile esaminare le informazioni di esecuzione registrate, che [contiene le metriche](how-to-understand-automated-ml.md) raccolte durante l'esecuzione. L'esecuzione di training produce`.pkl` un oggetto serializzato Python (file) che contiene il modello e la pre-elaborazione dei dati.

Mentre la creazione di modelli è automatizzata, è anche possibile [scoprire quanto siano importanti o rilevanti le feature](how-to-configure-auto-train.md#explain) per i modelli generati.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]

<a name="preprocess"></a>

## <a name="preprocessing"></a>Pre-elaborazione

In ogni esperimento di apprendimento automatico, i dati vengono pre-elaborati utilizzando i metodi predefiniti e, facoltativamente, tramite la pre-elaborazione avanzata.

> [!NOTE]
> I passaggi di pre-elaborazione di Machine Learning automatizzati (normalizzazione delle funzionalità, gestione dei dati mancanti, conversione di valori di testo nel formato numerico e così via) diventano parte del modello sottostante. Quando si usa il modello per le previsioni, gli stessi passaggi di pre-elaborazione applicati durante il training vengono automaticamente applicati ai dati di input.

### <a name="automatic-preprocessing-standard"></a>Pre-elaborazione automatica (standard)

In ogni esperimento di apprendimento automatico, i dati vengono ridimensionati o normalizzati automaticamente per consentire agli algoritmi di ottenere prestazioni soddisfacimenti.  Durante il training del modello, a ogni modello verrà applicata una delle tecniche di ridimensionamento o normalizzazione seguenti.

|Normalizzazione del ridimensionamento&nbsp;&&nbsp;| Descrizione |
| ------------- | ------------- |
| [StandardScaleWrapper (informazioni in questo standard)](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Standardizzare le funzionalità rimuovendo la media e il ridimensionamento in varianza unitaria  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Trasforma le feature ridimensionando ogni feature in base al valore minimo e massimo della colonna  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Scala ogni feature in base al suo valore assoluto massimo |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Questo Scaler è caratterizzato dalla loro gamma di quantitativi |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Riduzione della dimensionalità lineare mediante la scomposizione del valore singolare dei dati per proiettarlo in uno spazio dimensionale inferiore |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Questo trasformatore esegue la riduzione della dimensionalità lineare mediante la decomposizione del valore singolare troncato (SVD). Contrariamente a PCA, questo stimatore non centra i dati prima di calcolare la decomposizione del valore singolare, il che significa che può lavorare con matrici scipy.sparse in modo efficiente |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Ogni campione (ovvero ogni riga della matrice di dati) con almeno un componente diverso da zero viene ridimensionato indipendentemente dagli altri campioni in modo che la sua norma (l1 o l2) sia uguale a uno |

### <a name="advanced-preprocessing--featurization"></a>La & la fattibilità avanzata della pre-elaborazione

Sono inoltre disponibili ulteriori pre-elaborazione e featurizzazione avanzata, come guardrail di dati, codifica e trasformazioni. [Scopri di più su quale fattibilizzazione è inclusa](how-to-use-automated-ml-for-ml-models.md#featurization). Abilitare questa impostazione con:

+ Azure Machine Learning Studio: abilitare la **funzionalità di distribuzione automatica** nella sezione Visualizzare la configurazione **aggiuntiva** [con questi passaggi.](how-to-use-automated-ml-for-ml-models.md#create-and-run-experiment)

+ Python SDK: `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` specifica per la [ `AutoMLConfig` classe](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig). 

<a name="parity"></a>

## <a name="the-studio-vs-sdk"></a>Lo studio vs SDK

Scopri la parità e le differenze tra le funzionalità di Machine L automatizzate di alto livello disponibili tramite Python SDK e lo studio in Azure Machine Learning. 

### <a name="experiment-settings"></a>Impostazioni dell'esperimento 

Le impostazioni seguenti consentono di configurare l'esperimento mL automatizzato. 

| |Python SDK|L'esperienza web in studio|
----|:----:|:----:
Suddividere i dati in set di training/convalida| ✓|✓
Supporta le attività di ML: classificazione, regressione e previsione| ✓| ✓
Ottimizza in base alla metrica primaria| ✓| ✓
Supporta il calcolo AML come destinazione di calcoloSupports AML compute as compute target | ✓|✓
Configurare l'orizzonte di previsione, i ritardi di destinazione & la finestra di rotolamento|✓|✓
Impostare i criteri di uscita |✓|✓ 
Impostare iterazioni simultanee| ✓|✓
Colonne di rilascio| ✓|✓
Algoritmi di blocco|✓|✓
Convalida incrociata |✓|✓
Supporta il training nei cluster di Azure DatabricksSupports training on Azure Databricks clusters| ✓|
Visualizzare i nomi delle funzioni ingegnerizzate|✓|
Riepilogo della featurizzazione| ✓|
Fedeltà per le vacanze|✓|
Livelli di dettaglio del file di registro| ✓|

### <a name="model-settings"></a>Impostazioni del modello

Queste impostazioni possono essere applicate al modello migliore in seguito all'esperimento mL automatizzato.

| |Python SDK|L'esperienza web in studio|
|----|:----:|:----:|
|Migliore registrazione del modello, distribuzione, spiegabilità| ✓|✓|
|Abilitare i modelli di ensemble di voto & stack ensemble| ✓|✓|
|Mostra modello migliore in base a una metrica non primaria|✓||
|Abilitare/disabilitare la compatibilità del modello ONNX|✓||
|Testare un modello | ✓| |

### <a name="run-control-settings"></a>Eseguire le impostazioni di controllo

Queste impostazioni consentono di esaminare e controllare le esecuzioni dell'esperimento e le esecuzioni figlio. 

| |Python SDK|L'esperienza web in studio|
|----|:----:|:----:|
|Tabella di riepilogo di esecuzione| ✓|✓|
|Annulla eseguito & esecuzioni figlio| ✓|✓|
|Scarica i guardrail| ✓|✓|
|Sospendere l'esecuzione di & ripresa| ✓| |

## <a name="ensemble-models"></a><a name="ensemble"></a>Modelli Ensemble

L'apprendimento automatico supporta i modelli di ensemble, che sono abilitati per impostazione predefinita. L'apprendimento all'insieme migliora i risultati dell'apprendimento automatico e le prestazioni predittive combinando più modelli anziché utilizzando singoli modelli. Le iterazioni dell'insieme vengono visualizzate come le iterazioni finali dell'esecuzione. L'apprendimento automatico usa i metodi dell'ensemble di voto e impilamento per combinare i modelli:Automated machine learning uses both voting and stacking ensemble methods for combining models:

* **Votazione**: prevede in base alla media ponderata delle probabilità di classe previste (per le attività di classificazione) o agli obiettivi di regressione previsti (per le attività di regressione).
* **Impilamento**: impilamento combina modelli eterogenei ed essuate un meta-modello basato sull'output dei singoli modelli. I meta-modelli predefiniti correnti sono LogisticRegression per le attività di classificazione ed ElasticNet per le attività di regressione/previsione.

L'algoritmo di [selezione dell'ensemble Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) con l'inizializzazione dell'ensemble ordinato viene utilizzato per decidere quali modelli utilizzare all'interno dell'ensemble. A livello generale, questo algoritmo inizializza l'insieme con un massimo di cinque modelli con le migliori partiture individuali e verifica che questi modelli siano entro la soglia del 5% del punteggio migliore per evitare un insieme iniziale scadente. Quindi, per ogni iterazione dell'insieme, viene aggiunto un nuovo modello all'insieme esistente e viene calcolata la partitura risultante. Se un nuovo modello ha migliorato la partitura dell'insieme esistente, l'insieme viene aggiornato per includere il nuovo modello.

Scopri le [procedure](how-to-configure-auto-train.md#ensemble) per la modifica delle impostazioni predefinite dell'insieme nell'apprendimento automatico automatico.

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>AutoML & ONNX

Con Azure Machine Learning è possibile usare la mL automatizzata per creare un modello Python e convertirlo nel formato ONNX. Una volta che i modelli sono in formato ONNX, possono essere eseguiti su una varietà di piattaforme e dispositivi. Ulteriori informazioni [sull'accelerazione dei modelli ML con ONNX](concept-onnx.md).

Scopri come convertire in formato ONNX in questo esempio di [notebook Jupyter.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) Scopri quali [algoritmi sono supportati in ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

Il runtime di ONNX supporta anche C , in modo da poter usare il modello compilato automaticamente nelle app di C, senza alcuna necessità di ricodifica o di qualsiasi latenza di rete introdotta dagli endpoint REST. Ulteriori informazioni [sull'inferenza dei modelli ONNX con l'API di runtime ONNX di C.](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md) 



## <a name="next-steps"></a>Passaggi successivi

Vedere esempi e informazioni su come creare modelli usando l'apprendimento automatico:See examples and learn how to build models using automated machine learning:

+ Seguire [l'esercitazione: eseguire automaticamente](tutorial-auto-train-models.md) il training di un modello di regressione con Azure Machine LearningFollow the Tutorial: Automatically train a regression model with Azure Machine Learning

+ Configurare le impostazioni per l'esperimento di allenamento automatico:
  + In Azure Machine Learning Studio [usare questi passaggi.](how-to-use-automated-ml-for-ml-models.md)
  + Con Python SDK, [utilizzare questi passaggi](how-to-configure-auto-train.md).

+ Scopri come eseguire il training automatico usando i dati di serie temporali, [attenersi alla seguente procedura.](how-to-auto-train-forecast.md)

+ Prova gli esempi di [Jupyter Notebook per l'apprendimento automatico](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/)

* L'unità ML automatizzata è disponibile anche in altre soluzioni Microsoft, ad esempio, [ML.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power BI](https://docs.microsoft.com/power-bi/service-machine-learning-automated) e SQL [Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)
