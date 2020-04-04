---
title: Modellare l'interpretabilità in Azure Machine LearningModel interpretability in Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come spiegare perché il modello esegue stime usando Azure Machine Learning SDK. Può essere usato durante il training e l'inferenza per comprendere come il modello esegue le stime.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 04/02/2020
ms.openlocfilehash: fcb837af85a54102e8c9eafc33249af9dba6b5ce
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631403"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Modellare l'interpretabilità in Azure Machine LearningModel interpretability in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Panoramica dell'interpretabilità del modello

L'interpretabilità è fondamentale per data scientist, revisori e responsabili delle decisioni aziendali per garantire la conformità alle politiche aziendali, agli standard di settore e alle normative governative:

+ Gli scienziati dei dati hanno bisogno della capacità di spiegare i loro modelli ai dirigenti e alle parti interessate, in modo che possano comprendere il valore e l'accuratezza delle loro scoperte. Richiedono inoltre l'interpretabilità per eseguire il debug dei modelli e prendere decisioni informate su come migliorarli. 

+ I revisori legali richiedono strumenti per convalidare i modelli in relazione alla conformità normativa e monitorare l'impatto delle decisioni dei modelli sugli esseri umani. 

+ I responsabili delle decisioni aziendali hanno bisogno di una pace nel suo tempo, avendo la capacità di fornire trasparenza agli utenti finali. Questo permette loro di guadagnare e mantenere la fiducia.


L'abilitazione della capacità di spiegare un modello di apprendimento automatico è importante durante due fasi principali dello sviluppo del modello:Enabling the capability of explaining a machine learning model is important during two main phases of model development:
+ Durante la fase di training, poiché i progettisti e gli analizzatori del modello possono utilizzare l'output di interpretabilità di un modello per verificare ipotesi e creare fiducia con le parti interessate. Usano inoltre le informazioni dettagliate nel modello per il debug, la convalida del comportamento del modello corrisponde ai loro obiettivi e per verificare l'ingiustizia del modello o le funzionalità non significative.

+ Durante la fase di inferenza, avere trasparenza sui modelli distribuiti consente ai dirigenti di comprendere come funziona il modello e come le sue decisioni trattano e influenzano le persone nella vita reale. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretazionità con Azure Machine Learning

Le classi di interpretabilità vengono rese disponibili tramite più pacchetti SDK: (Informazioni su come [installare pacchetti SDK per Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py))

* `azureml.interpret`, il pacchetto principale, contenente le funzionalità supportate da Microsoft.

* `azureml.contrib.interpret`, l'anteprima e le funzionalità sperimentali che puoi provare.

* `azureml.train.automl.automlexplainer`pacchetto per l'interpretazione di modelli di apprendimento automatico automatizzati.

Utilizzare `pip install azureml-interpret` `pip install azureml-interpret-contrib` e per uso `pip install azureml-interpret-contrib` generale, e per AutoML utilizzare per ottenere i pacchetti di interpretabilità.


> [!IMPORTANT]
> Il contenuto `contrib` nello spazio dei nomi non è completamente supportato. Man mano che le funzionalità sperimentali diventano mature, verranno gradualmente spostate nello spazio dei nomi principale.
.



## <a name="how-to-interpret-your-model"></a>Come interpretare il modello

Utilizzando le classi e i metodi nell'SDK, è possibile:
+ Spiegare la stima del modello generando i valori di importanza delle funzionalità per l'intero modello e/o singoli punti dati. 
+ Ottieni l'interpretabilità del modello su set di dati reali su larga scala, durante il training e l'inferenza.
+ Usare un dashboard di visualizzazione interattivo per individuare modelli nei dati e nelle spiegazioni in fase di formazioneUse an interactive visualization dashboard to discover patterns in data and explanations at training time


Nell'apprendimento automatico, **le funzionalità** sono i campi dati usati per prevedere un punto dati di destinazione. Ad esempio, per prevedere il rischio di credito, è possibile utilizzare i campi dati per età, dimensioni dell'account e età dell'account. In questo caso, l'età, la dimensione dell'account e l'età dell'account sono **caratteristiche.** L'importanza della funzionalità indica in che modo ogni campo dati influisce sulle stime del modello. Ad esempio, l'età può essere molto utilizzata nella stima, mentre le dimensioni e l'età dell'account non influiscono in modo significativo sui valori di stima. Questo processo consente ai data scientist di spiegare le previsioni risultanti, in modo che le parti interessate abbiano visibilità sulle funzionalità più importanti nel modello.

Scopri le tecniche di interpretabilità supportate, i modelli di apprendimento automatico supportati e gli ambienti di esecuzione supportati qui.


## <a name="supported-interpretability-techniques"></a>Tecniche di interpretabilità supportate

 `azureml-interpret`utilizza le tecniche di interpretabilità sviluppate in [Interpret-Community,](https://github.com/interpretml/interpret-community/)un pacchetto pitone open source per la formazione di modelli interpretabili e per spiegare i sistemi di iaridi blackbox. [Interpret-Community](https://github.com/interpretml/interpret-community/) funge da host per gli esplicatori supportati da questo SDK e attualmente supporta le seguenti tecniche di interpretabilità:

|Tecnica di interpretabilità|Descrizione|Type|
|--|--|--------------------|
|1. Spiegazione albero SHAP| L'esplicatore di alberi di [SHAP,](https://github.com/slundberg/shap)che si concentra sull'algoritmo di stima del valore SHAP veloce tempo polinomio specifico per **alberi ed insieme di alberi**.|Specifico del modello|
|2. Spiegazione profonda SHAP| Sulla base della spiegazione di [SHAP](https://github.com/slundberg/shap), Deep Explainer "è un algoritmo di approssimazione ad alta velocità per i valori SHAP nei modelli di deep learning che si basa su una connessione con DeepLIFT descritto nella [carta SHAP NIPS](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Sono supportati i modelli **TensorFlow** e **Keras** che utilizzano il back-end TensorFlow (c'è anche il supporto preliminare per PyTorch)".|Specifico del modello|
|3. Spiegazione lineare SHAP| Lo esplicativo lineare di [SHAP](https://github.com/slundberg/shap)calcola i valori SHAP per un **modello lineare,** tenendo facoltativamente conto delle correlazioni tra funzionalità.|Specifico del modello|
|4. Spiegazione del kernel SHAP| L'esplicatore del kernel [SHAP](https://github.com/slundberg/shap)utilizza una regressione lineare locale particolarmente ponderata per stimare i valori SHAP per **qualsiasi modello.**|Indipendente dal modello|
|5. Mimic Explainer (Global Surrogate)| Mimic explainer si basa sull'idea di formare [modelli surrogati globali](https://christophm.github.io/interpretable-ml-book/global.html) per imitare i modelli di scatola nera. Un modello surrogato globale è un modello intrinsecamente interpretabile che viene sottoposto a training per approssimare le stime di **qualsiasi modello di scatola nera** nel modo più accurato possibile. Gli scienziati dei dati possono interpretare il modello surrogato per trarre conclusioni sul modello di scatola nera. È possibile utilizzare uno dei seguenti modelli interpretabili come modello surrogato: LightGBM (LGBMExplainableModel), Linear Regression (LinearExplainableModel), Stochastic Gradient Descent explainable model (SGDExplainableModel) e Decision Tree (DecisionTreeExplainableModel).|Indipendente dal modello|
|6. Spiegazione dell'importanza delle funzionalità di permutazione (PFI)| Permutation Feature Importance è una tecnica utilizzata per spiegare i modelli di classificazione e regressione che si ispira alla carta Random Forests di [Breiman](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (vedi sezione 10). A livello generale, il modo in cui funziona è mescolando casualmente i dati una feature alla volta per l'intero set di dati e calcolando quanto cambia la metrica delle prestazioni degli interessi. Maggiore è la modifica, maggiore è l'importanza della funzionalità. PFI può spiegare il comportamento complessivo di **qualsiasi modello sottostante,** ma non spiega le stime individuali. |Indipendente dal modello|




Oltre alle tecniche di interpretabilità descritte in precedenza, supportiamo un altro [esplicatore basato su SHAP,](https://github.com/slundberg/shap)denominato `TabularExplainer`. A seconda del `TabularExplainer` modello, utilizza uno degli esplicatori SHAP supportati:

* TreeExplainer per tutti i modelli basati su albero
* DeepExplainer per i modelli DNN
* LinearExplainer per modelli lineari
* KernelExplainer per tutti gli altri modelli

`TabularExplainer`ha inoltre apportato miglioramenti significativi alle funzionalità e alle prestazioni rispetto agli spiegatori SHAP diretti:

* **Riepilogo del set di dati di inizializzazione.** Nei casi in cui la velocità di spiegazione è più importante, si riepiloga il set di dati di inizializzazione e si genera un piccolo set di campioni rappresentativi, che velocizza la generazione di valori di importanza delle funzionalità complessive e individuali.
* **Campionamento del set di dati**di valutazione . Se l'utente passa un ampio set di campioni di valutazione ma non ne ha effettivamente bisogno, il parametro di campionamento può essere impostato su true per velocizzare il calcolo delle spiegazioni generali del modello.

Il diagramma seguente mostra la struttura corrente degli esplicatori supportati.

[![Machine Learning Interpretability Architecture](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Modelli di apprendimento automatico supportati

Il `azureml.interpret` pacchetto dell'SDK supporta i modelli sottoposti a training con i formati di set di dati seguenti:The package of the SDK supports models trained with the following dataset formats:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

Le funzioni di spiegazione accettano sia modelli che pipeline come input. Se viene fornito un modello, il `predict` modello `predict_proba` deve implementare la funzione di stima o conforme alla convenzione Scikit. Se il modello non supporta questa funzionalità, è possibile eseguire il `predict` `predict_proba` wrapping del modello in una funzione che genera lo stesso risultato o in Scikit e utilizzare tale funzione wrapper con l'esplicativo selezionato. Se viene fornita una pipeline, la funzione di spiegazione presuppone che lo script della pipeline in esecuzione restituisca una stima. Utilizzando questa tecnica `azureml.interpret` di wrapping, è possibile supportare i modelli sottoposti a training tramite i framework di deep learning PyTorch, TensorFlow e Keras, nonché i modelli di apprendimento automatico classici.

## <a name="local-and-remote-compute-target"></a>Destinazione di calcolo locale e remota

Il `azureml.interpret` pacchetto è progettato per funzionare con destinazioni di elaborazione locali e remote. Se eseguito in locale, le funzioni SDK non contatteranno alcun servizio di Azure.If run locally, The SDK functions will not contact any Azure services. 

È possibile eseguire una spiegazione in remoto in Azure Machine Learning Compute e registrare le informazioni di spiegazione nel servizio di cronologia di esecuzione di Azure Machine Learning.You can run explanation remotely on Azure Machine Learning Compute and log the explanation info into the Azure Machine Learning Run History Service. Una volta registrate queste informazioni, i report e le visualizzazioni della spiegazione sono facilmente disponibili in Azure Machine Learning Studio per l'analisi degli utenti.


## <a name="next-steps"></a>Passaggi successivi

Vedere le procedure per abilitare l'interpretazione per i modelli che si allenano in locale e in risorse di calcolo remoto di Azure Machine Learning.See the [how-to](how-to-machine-learning-interpretability-aml.md) for enabling interpretability for models training both locally and on Azure Machine Learning remote compute resources. Vedere [i blocchi appunti di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) per scenari aggiuntivi.
