---
title: Informazioni su Machine Learning automatizzato
titleSuffix: Azure Machine Learning
description: Informazioni su come Azure Machine Learning è in grado di scegliere automaticamente un algoritmo e, a partire da questo, generare un modello per consentire all'utente di risparmiare tempo. Tale scelta viene effettuata usando i parametri e i criteri specificati per selezionare l'algoritmo migliore per il modello.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
author: cartacioS
ms.author: sacartac
ms.date: 04/22/2020
ms.openlocfilehash: 4908f66dbc699a449b7b94febac8133bacc9f669
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/06/2020
ms.locfileid: "91760970"
---
# <a name="what-is-automated-machine-learning-automl"></a>Informazioni su Machine Learning automatizzato

Machine Learning automatizzato, o ML automatizzato, è il processo che consente di automatizzare le attività iterative per lo sviluppo di modelli di Machine Learning che richiedono molto tempo. Consente a data scientist, analisti e sviluppatori di creare modelli di Machine Learning con scalabilità, efficienza e produttività elevate, garantendo al tempo stesso la qualità del modello. Machine Learning automatizzato si basa su un'analisi approfondita da parte della [divisione Microsoft Research](https://www.microsoft.com/research/project/automl/).

Lo sviluppo tradizionale di modelli di Machine Learning richiede un utilizzo elevato delle risorse, una conoscenza approfondita del dominio e un dispendio considerevole di tempo per produrre e confrontare numerosi modelli. Grazie a Machine Learning automatizzato, sarà possibile accelerare il tempo necessario per ottenere modelli di Machine Learning pronti per la produzione con grande facilità ed efficienza.

## <a name="when-to-use-automl-classify-regression--forecast"></a>Quando usare Machine Learning automatizzato: classificazione, regressione e previsione

Applicare Machine Learning automatizzato in modo che Azure Machine Learning esegua il training e l'ottimizzazione di un modello usando la metrica di destinazione specificata. Machine Learning automatizzato democratizza il processo di sviluppo di modelli di Machine Learning e consente agli utenti, indipendentemente dalla loro esperienza di data science, di identificare una pipeline di Machine Learning end-to-end per eventuali problemi.

I data scientist, gli analisti e gli sviluppatori di tutti i settori possono usare Machine Learning automatizzato per:
+ Implementare soluzioni di Machine Learning senza una conoscenza approfondita della programmazione
+ Risparmiare tempo e risorse
+ Utilizzare procedure consigliate di data science
+ Fornire efficaci soluzioni ai problemi

### <a name="classification"></a>Classificazione

La classificazione è un'attività di Machine Learning comune. La classificazione è un tipo di apprendimento supervisionato in cui i modelli imparano a usare i dati di training e applicano tali informazioni ai nuovi dati. Azure Machine Learning offre funzionalità specifiche per queste attività, ad esempio la definizione delle funzionalità del testo per la rete neurale profonda per la classificazione. Altre informazioni sulle [opzioni di definizione delle funzionalità](how-to-configure-auto-features.md#featurization). 

L'obiettivo principale dei modelli di classificazione è quello di stimare le categorie in cui rientreranno i nuovi dati in base alle informazioni sui dati di training. Esempi di classificazione comuni includono rilevamento delle frodi, riconoscimento della grafia e rilevamento degli oggetti. Per altre informazioni, vedere l'articolo relativo alla [creazione di un modello di classificazione con ml automatico](tutorial-first-experiment-automated-ml.md).

Vedere esempi di attività di classificazione e sull'uso di Machine Learning automatizzato in questi notebook Python: [Fraud Detection](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) (Rilevamento delle frodi), [Marketing Prediction](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb) (Stima del marketing) e [Newsgroup Data Classification](https://towardsdatascience.com/automated-text-classification-using-machine-learning-3df4f4f9570b) (Classificazione dei dati di newsgroup)

### <a name="regression"></a>Regressione

Analogamente alla classificazione, anche quelle di regressione sono attività di apprendimento supervisionato comuni. Azure Machine Learning offre [funzionalità specifiche per queste attività](how-to-configure-auto-features.md#featurization).

Diversamente dalla classificazione, dove i valori di output stimati sono categorici, i modelli di regressione stimano i valori di output numerici in base a predittori indipendenti. Nella regressione l'obiettivo è di stabilire la relazione tra le variabili indipendenti del predittore stimando il modo in cui una variabile influisce sulle altre. Ad esempio, il prezzo dell'automobile in base a caratteristiche come, chilometraggio dei gas, classificazione di sicurezza e così via. Per altre informazioni, vedere un esempio dell'attività di [regressione con Machine Learning automatizzato](tutorial-auto-train-models.md).

Vedere esempi di attività di regressione e sull'uso di Machine Learning automatizzato per le stime in questi notebook Python: [CPU Performance Prediction](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb) (Stima delle prestazioni della CPU) 

### <a name="time-series-forecasting"></a>Previsione della serie temporale

La compilazione di previsioni è parte integrante di qualsiasi azienda, sia che si tratti di ricavi, scorte, vendite o richieste dei clienti. È possibile usare Machine Learning automatizzato per combinare tecniche e approcci e ottenere una previsione della serie temporale consigliata e di alta qualità. Altre informazioni sono disponibili in questa guida pratica: [Machine Learning automatizzato per la previsione di una serie temporale](how-to-auto-train-forecast.md). 

Un esperimento di serie temporale automatizzato viene considerato un problema di regressione multivariata. I valori della serie temporale precedente vengono "trasformati tramite Pivot" per diventare dimensioni aggiuntive per il regressore insieme ad altri predittori. Questo approccio, a differenza dei metodi classici della serie temporale, ha il vantaggio di incorporare naturalmente più variabili contestuali e la relazione tra loro durante il training. Machine Learning automatizzato apprende un modello singolo, ma spesso con rami interni, per tutti gli elementi del set di dati e degli orizzonti di stima. Maggiori sono quindi i dati disponibili per la stima dei parametri del modello e più probabile diventa la generalizzazione per la serie non visibile.

La configurazione avanzata della previsione include:
* Rilevamento festività e definizione delle funzionalità
* Strumenti di apprendimento della serie temporale e della rete neurale profonda (Auto-ARIMA, Prophet, ForecastTCN)
* Supporto di numerosi modelli tramite raggruppamento
* Convalida incrociata dell'origine in sequenza
* Ritardi configurabili
* Funzionalità di aggregazione della finestra mobile


Vedere esempi di attività di regressione e sull'uso di Machine Learning automatizzato per le stime in questi notebook Python: [Sales Forecasting](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-orange-juice-sales/auto-ml-forecasting-orange-juice-sales.ipynb) (Previsioni di vendita), [Demand Forecasting](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-energy-demand/auto-ml-forecasting-energy-demand.ipynb) (Previsioni della domanda) e [Beverage Production Forecast](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/forecasting-beer-remote/auto-ml-forecasting-beer-remote.ipynb) (Previsioni della produzione di bevande).

## <a name="how-automl-works"></a>Funzionamento di Machine Learning automatizzato

Durante il training, Azure Machine Learning crea numerose pipeline in parallelo che tentano di usare diversi algoritmi e parametri per conto dell'utente. Il servizio esegue l'iterazione tramite gli algoritmi di Machine Learning abbinati alle selezioni di funzionalità, in cui ogni iterazione produce un modello con un punteggio di training. Maggiore è il punteggio, più il modello viene considerato "adatto" ai dati.  Il servizio si arresterà quando raggiunge i criteri di uscita definiti nell'esperimento. 

Con **Azure Machine Learning**, è possibile progettare ed eseguire gli esperimenti di training di Machine Learning automatizzato con i passaggi seguenti:

1. **Identificare il problema di Machine Learning** da risolvere: classificazione, previsione o regressione

1. **Scegliere se si vuole usare Python SDK o l'esperienza Web di Studio**: Informazioni sull'equivalenza tra [Python SDK e l'esperienza Web di Studio](#parity).

   * Per un'esperienza limitata o senza codice, provare l'esperienza Web di Azure Machine Learning Studio all'indirizzo [https://ml.azure.com](https://ml.azure.com/)  
   * Per gli sviluppatori Python, vedere [Azure Machine Learning Python SDK](how-to-configure-auto-train.md) 
    
1. **Specificare l'origine e il formato dei dati di training con etichetta**: Matrici Numpy o dataframe Pandas

1. **Configurare la destinazione di calcolo per il training del modello**, ad esempio [computer locale, ambiente di calcolo di Azure Machine Learning, macchine virtuali remote o Azure Databricks](how-to-set-up-training-targets.md).  Altre informazioni sul training automatico [su una risorsa remota](how-to-auto-train-remote.md).

1. **Configurare i parametri di Machine Learning automatizzato** che determinano il numero di iterazioni su modelli diversi, le impostazioni degli iperparametri, la definizione delle funzionalità/pre-elaborazione avanzata e le metriche da esaminare quando si determina il modello migliore.  
1. **Inviare l'esecuzione del training**

1. **Esaminare i risultati** 

La figura seguente illustra questo processo. 
![Machine Learning automatizzato](./media/concept-automated-ml/automl-concept-diagram2.png)


È possibile esaminare anche le informazioni registrate sull'esecuzione, che [contengono le metriche](how-to-understand-automated-ml.md) raccolte durante l'esecuzione. L'esecuzione del training genera un oggetto Python serializzato (file `.pkl`) che contiene il modello e la pre-elaborazione dei dati.

Mentre la creazione di modelli è automatica, è anche possibile [ottenere informazioni sull'importanza delle funzionalità](how-to-configure-auto-train.md#explain) per i modelli generati.

Informazioni su come usare una [destinazione di calcolo remota](how-to-auto-train-remote.md).



> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE2Xc9t]


## <a name="feature-engineering"></a>Progettazione delle funzioni

La progettazione di funzioni è il processo di utilizzo della conoscenza del dominio dei dati per creare funzionalità che consentono agli algoritmi di Machine Learning di ottenere risultati migliori. In Azure Machine Learning vengono applicate le tecniche di ridimensionamento e normalizzazione per semplificare la progettazione delle funzionalità. Insieme, queste tecniche e progettazione di funzionalità sono denominate conteggi.

Per gli esperimenti automatici di Machine Learning, conteggi viene applicato automaticamente, ma può anche essere personalizzato in base ai dati. [Altre informazioni su ciò che è incluso nella definizione delle funzionalità](how-to-configure-auto-features.md#featurization).  

> [!NOTE]
> I passaggi di definizione delle funzionalità di Machine Learning automatizzato (normalizzazione delle funzionalità, gestione dei dati mancanti, conversione dei valori di testo in formato numerico e così via) diventano parte del modello sottostante. Quando si usa il modello per le previsioni, gli stessi passaggi di definizione delle funzionalità applicati durante il training vengono automaticamente applicati ai dati di input.

### <a name="automatic-featurization-standard"></a>Automatico conteggi (standard)

In ogni esperimento di Machine Learning automatizzato, i dati vengono ridimensionati o normalizzati automaticamente per consentire il corretto funzionamento degli algoritmi. Durante il training del modello, viene applicata una delle tecniche di ridimensionamento o normalizzazione seguenti a ogni modello. Informazioni sul modo in cui AutoML consente di [prevenire i dati in eccesso e sbilanciati](concept-manage-ml-pitfalls.md) nei modelli.

|Ridimensionamento e normalizzazione| Descrizione |
| ------------- | ------------- |
| [StandardScaleWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.StandardScaler.html)  | Consente di standardizzare le funzionalità rimuovendo la media e il ridimensionando alla varianza unitaria  |
| [MinMaxScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MinMaxScaler.html)  | Consente di trasformare le funzionalità ridimensionando ogni funzionalità in base al valore minimo e massimo della colonna  |
| [MaxAbsScaler](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.MaxAbsScaler.html#sklearn.preprocessing.MaxAbsScaler) |Consente di ridimensionare ogni funzionalità in base al valore massimo assoluto |
| [RobustScalar](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.RobustScaler.html) |Consente il ridimensionamento delle funzionalità in base al rispettivo intervallo quantile |
| [PCA](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.PCA.html) |Consente di ridurre la dimensionalità lineare mediante scomposizione di valori singolari dei dati per proiettarla in uno spazio di dimensioni inferiori |
| [TruncatedSVDWrapper](https://scikit-learn.org/stable/modules/generated/sklearn.decomposition.TruncatedSVD.html) |Consente di eseguire la riduzione della dimensionalità lineare per mezzo della scomposizione del valore singolare troncato (SVD). Contrariamente a PCA, questo estimatore non centra i dati prima di calcolare la scomposizione del valore singolare, può quindi funzionare con le matrici scipy.sparse in modo efficiente |
| [SparseNormalizer](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.Normalizer.html) | Ogni esempio, ovvero ogni riga della matrice di dati, con almeno un componente diverso da zero, viene ridimensionato indipendentemente dagli altri esempi, in modo che la relativa norma (l1 o l2) sia uguale a uno |

### <a name="customize-featurization"></a>Personalizzare conteggi

Sono disponibili anche tecniche aggiuntive per la progettazione di funzioni quali, codifica e trasformazioni. 

Abilitare questa impostazione con:

+ Azure Machine Learning Studio: abilitare **Automatic featurization** (Definizione delle funzionalità automatica) nella sezione **View additional configuration** (Visualizza configurazione aggiuntiva) [con questi passaggi](how-to-use-automated-ml-for-ml-models.md#customize-featurization).

+ Python SDK: specificare `"feauturization": 'auto' / 'off' / 'FeaturizationConfig'` nell'oggetto [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig) . Altre informazioni sull' [Abilitazione di conteggi](how-to-configure-auto-features.md). 

## <a name="ensemble-models"></a><a name="ensemble"></a> Modelli Ensemble

Machine Learning automatizzato supporta i modelli Ensemble, che sono abilitati per impostazione predefinita. L'apprendimento dell'insieme migliora i risultati di Machine Learning e le prestazioni predittive combinando più modelli rispetto all'uso di modelli singoli. Le iterazioni dell'insieme vengono visualizzate come iterazioni finali dell'esecuzione. Machine Learning automatizzato usa i metodi Ensemble di voto e sovrapposizione per combinare i modelli:

* **Voto**: stima in base alla media ponderata delle probabilità della classe stimata (per le attività di classificazione) o alle destinazioni di regressione stimate (per le attività di regressione).
* **Sovrapposizione**: combina modelli eterogenei ed esegue il training di un metamodello in base all'output dei singoli modelli. I metamodelli predefiniti correnti sono LogisticRegression per le attività di classificazione ed ElasticNet per le attività di regressione/previsione.

L'[algoritmo di selezione dell'insieme Caruana](http://www.niculescu-mizil.org/papers/shotgun.icml04.revised.rev2.pdf) con l'inizializzazione dell'insieme ordinato viene usato per decidere quali modelli usare nell'insieme. A un livello elevato, questo algoritmo inizializza l'insieme con un massimo di cinque modelli con i punteggi individuali migliori e verifica che questi modelli siano entro il 5% di soglia del punteggio migliore per evitare un insieme iniziale scadente. Per ogni iterazione dell'insieme, viene aggiunto un nuovo modello all'insieme esistente e viene calcolato il punteggio risultante. Se un nuovo modello migliora il punteggio dell'insieme esistente, l'insieme viene aggiornato in modo da includere il nuovo modello.

Per la modifica delle impostazioni dell'insieme predefinite in Machine Learning automatizzato, vedere la [guida pratica](how-to-configure-auto-train.md#ensemble).

## <a name="guidance-on-local-vs-remote-managed-ml-compute-targets"></a><a name="local-remote"></a>Linee guida per le destinazioni di calcolo locali e remote gestite di Machine Learning

L'interfaccia Web per Machine Learning automatizzato usa sempre una [destinazione di calcolo](concept-compute-target.md) remota.  Tuttavia, quando si usa Python SDK, scegliere una destinazione di calcolo locale o remota per il training di Machine Learning automatizzato.

* **Calcolo locale**: il training viene eseguito sul computer portatile locale o sul calcolo della macchina virtuale. 
* **Calcolo remoto**: il training viene eseguito nei cluster di calcolo di Machine Learning.  

### <a name="choose-compute-target"></a>Scegliere una destinazione di calcolo
Quando si sceglie la destinazione di calcolo, tenere in considerazione i fattori seguenti:

 * **Scegliere un computer locale**: se lo scenario riguarda le esplorazioni iniziali o le demo che usano dati di piccole dimensioni e training di breve durata, ovvero secondi o pochi minuti per ogni esecuzione figlio, il training sul computer locale potrebbe essere la scelta migliore.  Non sono presenti tempi di installazione, le risorse dell'infrastruttura (PC o macchina virtuale) sono direttamente disponibili.
 * **Scegliere un cluster di elaborazione di Machine Learning remoto**: se si esegue il training con set di dati di dimensioni maggiori, ad esempio nel training di produzione che crea modelli che richiedono training di maggiore durata, il calcolo remoto fornirà prestazioni ottimali end-to-end in termini di tempo, in quanto `AutoML` parallelizza i training nei nodi del cluster. In un calcolo remoto, il tempo di avvio per l'infrastruttura interna aggiungerà circa 1,5 minuti per ogni esecuzione figlio, oltre a minuti aggiuntivi per l'infrastruttura cluster se le macchine virtuali non sono ancora attive e in esecuzione.

### <a name="pros-and-cons"></a>Vantaggi e svantaggi
Nella scelta tra calcolo locale o remoto, tenere presenti i vantaggi e gli svantaggi seguenti.

|  | Vantaggi  |Svantaggi  |
|---------|---------|---------|---------|
|**Destinazione di calcolo locale** |  <li> Nessun tempo di avvio dell'ambiente   | <li>  Subset di funzionalità<li>  Esecuzioni non parallelizzabili <li> Non ottimale per dati di grandi dimensioni <li>Nessun flusso di dati durante il training <li>  Nessuna definizione delle funzionalità basata sulla rete neurale profonda <li> Solo Python SDK |
|**Cluster di elaborazione di Machine Learning remoti**|  <li> Set completo di funzionalità <li> Esecuzioni figlio parallelizzate <li>   Supporto per dati di grandi dimensioni<li>  Definizione delle funzionalità basata sulla rete neurale profonda <li>  Scalabilità dinamica del cluster di elaborazione su richiesta <li> Esperienza senza codice (interfaccia utente Web)  |  <li> Tempo di avvio per i nodi del cluster <li> Tempo di avvio per ogni esecuzione figlio    |

### <a name="feature-availability"></a>Disponibilità delle funzionalità 

 Quando si usa il calcolo remoto, sono disponibili altre funzionalità, come illustrato nella tabella seguente. 

| Funzionalità                                                    | Remote | Local | 
|------------------------------------------------------------|--------|-------|
| Streaming dei dati (supporto per dati di grandi dimensioni, fino a 100 GB)          | ✓      |       | 
| Training e definizione delle funzionalità di testo basate su rete neurale profonda e modello BERT             | ✓      |       |
| Supporto GPU predefinito (training e inferenza)        | ✓      |       |
| Supporto per la classificazione delle immagini e l'assegnazione di etichette                  | ✓      |       |
| Modelli di previsione Auto-ARIMA, Prophet e ForecastTCN | ✓      |       | 
| Più esecuzioni/iterazioni in parallelo                       | ✓      |       |
| Creare modelli con l'interpretabilità nell'interfaccia utente dell'esperienza Web di Machine Learning automatizzato      | ✓      |       |
| Personalizzazione della progettazione delle funzionalità nell'interfaccia utente dell'esperienza Web di Studio| ✓      |       |
| Ottimizzazione degli iperparametri di Machine Learning automatizzato                             | ✓      |       |
| Supporto del flusso di lavoro della pipeline di Azure Machine Learning                         | ✓      |       |
| Continuare un'esecuzione                                             | ✓      |       |
| Previsione                                                | ✓      | ✓     |
| Creare ed eseguire esperimenti nei notebook                    | ✓      | ✓     |
| Registrare e visualizzare le informazioni e le metriche dell'esperimento nell'interfaccia utente | ✓      | ✓     |
| Protezione dati                                            | ✓      | ✓     |

## <a name="many-models"></a>Molti modelli 

L'[acceleratore di soluzione molti modelli](https://aka.ms/many-models) (anteprima) si basa su Azure Machine Learning e consente di usare Machine Learning automatizzato per eseguire il training, utilizzare e gestire centinaia o addirittura migliaia di modelli di Machine Learning.

Ad esempio, la creazione di un modello __per ogni istanza o unico__ negli scenari seguenti può produrre risultati migliori:

* Stima delle vendite per ogni singolo negozio
* Manutenzione predittiva per centinaia di pozzi di petrolio
* Adattamento di un'esperienza ai singoli utenti

## <a name="automl-in-azure-machine-learning"></a>Machine Learning automatizzato in Azure Machine Learning

Azure Machine Learning offre due esperienze per l'uso di ML automatizzato:

* Per i clienti con esperienza di codice, [Azure Machine Learning Python SDK](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py&preserve-view=true) 

* Per i clienti con esperienza limitata o che non dispongono di codice, Azure Machine Learning Studio all'indirizzo [https://ml.azure.com](https://ml.azure.com/)  

<a name="parity"></a>

### <a name="experiment-settings"></a>Impostazioni dell'esperimento 

Le impostazioni seguenti consentono di configurare l'esperimento di Machine Learning automatizzato. 

| |Python SDK|Esperienza Web di Studio|
----|:----:|:----:
|**Suddivide i dati in set di training/convalida**| ✓|✓
|**Supporta le attività di Machine Learning: classificazione, regressione e previsione**| ✓| ✓
|**Ottimizza in base alla metrica primaria**| ✓| ✓
|**Supporta l'ambiente di calcolo di Azure Machine Learning come destinazione di calcolo** | ✓|✓
|**Configurare l'orizzonte di previsione, i ritardi di destinazione & finestra in sequenza**|✓|✓
|**Imposta criteri uscita** |✓|✓ 
|**Imposta il numero massimo di iterazioni simultanee**| ✓|✓
|**Elimina colonne**| ✓|✓
|**Algoritmi di blocco**|✓|✓
|**Convalida incrociata** |✓|✓
|**Supporta il training su cluster di Azure Databricks**| ✓|
|**Visualizza i nomi delle funzionalità progettate**|✓|
|**Riepilogo delle funzionalità**| ✓|
|**Definizione delle funzionalità per le festività**|✓|
|**Livelli di dettaglio file di log**| ✓|

### <a name="model-settings"></a>Impostazioni del modello

Queste impostazioni possono essere applicate al modello migliore in seguito all'esperimento di Machine Learning automatizzato.

| |Python SDK|Esperienza Web di Studio|
|----|:----:|:----:|
|**Registrazione, distribuzione e spiegazione migliore del modello**| ✓|✓|
|**Abilitare i modelli di ensemble di voto & stack Ensemble**| ✓|✓|
|**Mostra il modello migliore in base a una metrica non primaria**|✓||
|**Abilita/Disabilita la compatibilità del modello ONNX**|✓||
|**Testare un modello** | ✓| |

### <a name="run-control-settings"></a>Impostazioni di controllo dell'esecuzione

Queste impostazioni consentono di esaminare e controllare le esecuzioni dell'esperimento e le esecuzioni figlio. 

| |Python SDK|Esperienza Web di Studio|
|----|:----:|:----:|
|**Esegue la tabella di riepilogo**| ✓|✓|
|**Annulla esecuzioni & esecuzioni figlio**| ✓|✓|
|**Ottiene protezioni**| ✓|✓|
|**Sospendere & riprendere le esecuzioni**| ✓| |

<a name="use-with-onnx"></a>

## <a name="automl--onnx"></a>Machine Learning automatizzato e ONNX

Con Azure Machine Learning, è possibile usare Machine Learning automatizzato per compilare un modello Python e convertirlo nel formato ONNX. Quando i modelli sono nel formato ONNX, possono essere eseguiti in un'ampia gamma di piattaforme e dispositivi. Altre informazioni sull'[accelerazione dei modelli di Machine Learning con ONNX](concept-onnx.md).

Vedere come eseguire la conversione nel formato ONNX [in questo esempio di notebook Jupyter](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-bank-marketing-all-features/auto-ml-classification-bank-marketing-all-features.ipynb). Informazioni sugli [algoritmi supportati in ONNX](how-to-configure-auto-train.md#select-your-experiment-type).

Il runtime di ONNX supporta anche C#, quindi è possibile usare il modello creato automaticamente nelle app C# senza la necessità di ricodificare o di una delle latenze di rete introdotte dagli endpoint REST. Altre informazioni sull'[inferenza dei modelli ONNX con l'API C# di runtime ONNX](https://github.com/Microsoft/onnxruntime/blob/master/docs/CSharp_API.md). 

## <a name="next-steps"></a>Passaggi successivi

Sono disponibili più risorse per iniziare a usare AutoML. 

### <a name="tutorials-how-tos"></a>Esercitazioni/procedure
Le esercitazioni sono esempi introduttivi end-to-end di scenari AutoML.
+ **Per un'esperienza di Code First**, seguire l' [esercitazione: eseguire automaticamente il training di un modello di regressione con Azure Machine Learning Python SDK](tutorial-auto-train-models.md).

 + **Per un'esperienza di basso o senza codice**, vedere l' [esercitazione: creare modelli di classificazione Machine Learning automatizzati con Azure Machine Learning Studio](tutorial-first-experiment-automated-ml.md).

Gli articoli illustrano in dettaglio le funzionalità offerte da AutoML. Ad esempio, 

+ Configurare le impostazioni per gli esperimenti di training automatici
    + In Azure Machine Learning Studio, [usare questi passaggi](how-to-use-automated-ml-for-ml-models.md). 
    + Con Python SDK, [usare questi passaggi](how-to-configure-auto-train.md).

+  [Questa procedura](how-to-auto-train-forecast.md)illustra come eseguire il training automatico usando i dati delle serie temporali.

### <a name="jupyter-notebook-samples"></a>Esempi di Jupyter notebook 

Esaminare gli esempi di codice e i casi di utilizzo dettagliati nel [repository dei notebook di GitHub per gli esempi automatici di Machine Learning](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/).

### <a name="python-sdk-reference"></a>Informazioni di riferimento su Python SDK

Approfondire la conoscenza dei modelli di progettazione e delle specifiche di classe dell'SDK con la [documentazione di riferimento della classe AutoML](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py&preserve-view=true). 

> [!Note]
> Le funzionalità automatiche di Machine Learning sono disponibili anche in altre soluzioni Microsoft, ad esempio [ml.NET](https://docs.microsoft.com/dotnet/machine-learning/automl-overview), [HDInsight](../hdinsight/spark/apache-spark-run-machine-learning-automl.md), [Power bi](https://docs.microsoft.com/power-bi/service-machine-learning-automated) e [SQL Server](https://cloudblogs.microsoft.com/sqlserver/2019/01/09/how-to-automate-machine-learning-on-sql-server-2019-big-data-clusters/)

