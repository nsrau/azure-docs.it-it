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
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: b68d2a72dc18f683f2203429908a536db1b5124a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063993"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Modellare l'interpretabilità in Azure Machine LearningModel interpretability in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Panoramica dell'interpretabilità del modello

L'interpretabilità è fondamentale sia per i data scientist che per i responsabili delle decisioni aziendali per garantire la conformità alle politiche aziendali, agli standard del settore e alle normative governative:
+ Gli scienziati dei dati hanno bisogno della capacità di spiegare i loro modelli ai dirigenti e alle parti interessate, in modo che possano comprendere il valore e l'accuratezza delle loro scoperte 
+ I responsabili delle decisioni imprenditoriali hanno bisogno di una tranquillità della capacità di fornire trasparenza agli utenti finali per ottenere e mantenere la loro fiducia

L'abilitazione della capacità di spiegare un modello di apprendimento automatico è importante durante due fasi principali dello sviluppo del modello:Enabling the capability of explaining a machine learning model is important during two main phases of model development:
+ Durante la fase di training del ciclo di sviluppo del modello di apprendimento automatico. I progettisti di modelli e gli valutatori possono utilizzare l'output di interpretabilità di un modello per verificare ipotesi e creare fiducia con le parti interessate. Usano inoltre le informazioni dettagliate nel modello per il debug, la convalida del comportamento del modello corrisponde ai relativi obiettivi e per verificare la presenza di distorsioni o funzionalità non significative.
+ Durante la fase di inferenza, avere trasparenza sui modelli distribuiti consente ai dirigenti di comprendere come funziona il modello e come le sue decisioni trattano e influenzano le persone nella vita reale. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretazionità con Azure Machine Learning

In questo articolo viene illustrato come vengono implementati i concetti di interpretabilità dei modelli nell'SDK.

Utilizzando le classi e i metodi nell'SDK, è possibile ottenere:
+ Valori di importanza delle funzionalità per le funzionalità non elaborate e non progettate
+ Interpretabilità su set di dati reali su larga scala, durante il training e l'inferenza.
+ Visualizzazioni interattive per facilitare l'individuazione di modelli nei dati e nelle spiegazioni in fase di formazione


Nell'apprendimento automatico, **le funzionalità** sono i campi dati usati per prevedere un punto dati di destinazione. Ad esempio, per prevedere il rischio di credito, è possibile utilizzare i campi dati per età, dimensioni dell'account e età dell'account. In questo caso, l'età, la dimensione dell'account e l'età dell'account sono **caratteristiche.** L'importanza della funzionalità indica in che modo ogni campo dati influisce sulle stime del modello. Ad esempio, l'età può essere molto utilizzata nella stima, mentre le dimensioni e l'età dell'account non influiscono in modo significativo sull'accuratezza della stima. Questo processo consente ai data scientist di spiegare le previsioni risultanti, in modo che le parti interessate abbiano visibilità su quali punti dati sono più importanti nel modello.

Utilizzando questi strumenti, è possibile spiegare i modelli di apprendimento automatico **a livello globale su tutti i dati**o **localmente su un punto dati specifico** utilizzando le tecnologie all'avanguardia in modo facile da usare e scalabile.

Le classi di interpretabilità vengono rese disponibili tramite più pacchetti SDK. Informazioni su come [installare pacchetti SDK per Azure Machine Learning.](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)

* `azureml.interpret`, il pacchetto principale, contenente le funzionalità supportate da Microsoft.

* `azureml.contrib.interpret`, l'anteprima e le funzionalità sperimentali che puoi provare.

* `azureml.train.automl.automlexplainer`pacchetto per l'interpretazione di modelli di apprendimento automatico automatizzati.

> [!IMPORTANT]
> Il contenuto `contrib` nello spazio dei nomi non è completamente supportato. Man mano che le funzionalità sperimentali diventano mature, verranno gradualmente spostate nello spazio dei nomi principale.

## <a name="how-to-interpret-your-model"></a>Come interpretare il modello

È possibile applicare le classi e i metodi di interpretabilità per comprendere il comportamento globale del modello o le stime specifiche. Il primo è chiamato spiegazione globale e il secondo è chiamato spiegazione locale.

I metodi possono anche essere classificati in base al fatto che il metodo sia indipendente dal modello o specifico del modello. Alcuni metodi sono destinati a determinati tipi di modelli. Ad esempio, l'espediente della struttura ad albero di SHAP si applica solo ai modelli basati su albero. Alcuni metodi trattano il modello come una scatola nera, ad esempio l'especossore imitatore o lo esplicatore del kernel di SHAP. Il `interpret` pacchetto sfrutta questi diversi approcci in base a set di dati, tipi di modello e casi d'uso.

L'output è un set di informazioni su come un determinato modello esegue la stima, ad esempio:The output is a set of information on how a given model makes its prediction, such as:
* Importanza delle caratteristiche relative/locali/globali
* Relazione di previsione e funzionalità globale/locale

### <a name="explainers"></a>Spiegazioni

Questo pacchetto utilizza le tecniche di interpretabilità sviluppate in [Interpret-Community](https://github.com/interpretml/interpret-community/), un pacchetto pitone open source per la formazione di modelli interpretabili e per aiutare a spiegare i sistemi di ia-ai blackbox. [Interpret-Community](https://github.com/interpretml/interpret-community/) funge da host per gli esplicatori supportati da questo SDK e attualmente supporta le seguenti tecniche di interpretabilità:

* **SHAP Tree Explainer**: [SHAP](https://github.com/slundberg/shap)'s tree explainer, che si concentra sul tempo polinomiale veloce algoritmo di stima del valore SHAP specifico per alberi ed insieme di alberi.
* **SHAP Deep Explainer**: Sulla base della spiegazione di [SHAP](https://github.com/slundberg/shap), Deep Explainer "è un algoritmo di approssimazione ad alta velocità per i valori SHAP nei modelli di deep learning che si basa su una connessione con DeepLIFT descritto nel [documento NIPS SHAP](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Sono supportati i modelli TensorFlow e Keras che utilizzano il back-end TensorFlow (c'è anche il supporto preliminare per PyTorch)".
* **SHAP Linear Explainer**: Lo esplicatore lineare di [SHAP](https://github.com/slundberg/shap)calcola i valori SHAP per un modello lineare, tenendo facoltativamente conto delle correlazioni tra le funzionalità.

* **SHAP Kernel Explainer**: L'esplicatore del kernel [di SHAP](https://github.com/slundberg/shap)utilizza una regressione lineare locale appositamente ponderata per stimare i valori SHAP per qualsiasi modello.
* **Mimic Explainer**: Mimic explainer si basa sull'idea di formare [modelli surrogati globali](https://christophm.github.io/interpretable-ml-book/global.html) per imitare i modelli di scatola nera. Un modello surrogato globale è un modello intrinsecamente interpretabile che viene sottoposto a training per approssimare le stime di un modello di scatola nera nel modo più accurato possibile. Data scientist può interpretare il modello surrogato per trarre conclusioni sul modello di scatola nera. È possibile utilizzare uno dei seguenti modelli interpretabili come modello surrogato: LightGBM (LGBMExplainableModel), Linear Regression (LinearExplainableModel), Stochastic Gradient Descent explainable model (SGDExplainableModel) e Decision Tree ( DecisionTreeExplainableModel).


* **Permutation Feature Importance Explainer**: Permutation Feature Importance è una tecnica utilizzata per spiegare i modelli di classificazione e regressione ispirata alla carta Random Forests di [Breiman](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (vedere la sezione 10). A livello generale, il modo in cui funziona è mescolando casualmente i dati una feature alla volta per l'intero set di dati e calcolando quanto cambia la metrica delle prestazioni degli interessi. Maggiore è la modifica, maggiore è l'importanza della funzionalità.

* **Lime Explainer** (`contrib`): Sulla base di [LIME](https://github.com/marcotcr/lime), LIME Explainer utilizza l'algoritmo lime (Local Interpretable Model-Agnostic Explanations) all'avanguardia per creare modelli surrogati locali. A differenza dei modelli surrogati globali, LIME si concentra sulla formazione di modelli surrogati locali per spiegare le stime individuali.
* **HAN Text Explainer** (`contrib`): HAN Text Explainer utilizza una rete di attenzione gerarchica per ottenere spiegazioni del modello dai dati di testo per un determinato modello di testo a casella nera. Viene eseguito il training del modello surrogato HAN sulle uscite previste del modello di scatola nera specificato. Dopo la formazione globale attraverso il corpo di testo, aggiunge un passo di perfezionamento per un documento specifico al fine di migliorare l'accuratezza delle spiegazioni. HAN utilizza un RNN bidirezionale con due livelli di attenzione, per l'attenzione di frasi e parole. Una volta che il DNN viene addestrato sul modello scatola nera e ottimizzato su un documento specifico, l'utente può estrarre l'importanza della parola dai livelli di attenzione. HAN è dimostrato essere più preciso di LIME o SHAP per i dati di testo, ma più costoso in termini di tempo di formazione pure. Sono stati apportati miglioramenti per dare all'utente la possibilità di inizializzare la rete con incorporamenti di parole GloVe per ridurre il tempo di formazione. Il tempo di formazione può essere migliorato in modo significativo eseguendo HAN in una macchina virtuale GPU di Azure remota. L'implementazione di HAN è descritta in ["Hierarchical Attention Networks for Document Classification (Yang et al., 2016)"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


* **Esplicatore tabulare:** `TabularExplainer` utilizza la logica seguente per richiamare gli esplicativi [Direct SHAP:](https://github.com/slundberg/shap)

    1. Se si tratta di un modello ad `TreeExplainer`albero, applicare SHAP ,
    2. Se si tratta di un modello `DeepExplainer`DNN, applicare SHAP ,
    3. Se si tratta di un modello `LinearExplainer`lineare, applicare SHAP ,
    3. Trattalo come un modello black-box e applica SHAP`KernelExplainer`


`TabularExplainer`ha inoltre apportato miglioramenti significativi alle funzionalità e alle prestazioni rispetto agli spiegatori SHAP diretti:

* **Riepilogo del set di dati di inizializzazione.** Nei casi in cui la velocità di spiegazione è più importante, riepiloghiamo il set di dati di inizializzazione e generiamo un piccolo set di campioni rappresentativi, che velocizza sia la spiegazione globale che quella locale.
* **Campionamento del set di dati**di valutazione . Se l'utente passa un ampio set di campioni di valutazione ma non ne ha effettivamente bisogno, il parametro di campionamento può essere impostato su true per velocizzare la spiegazione globale.

Il diagramma seguente mostra la struttura attuale degli esplicatori diretti e meta.

[![Machine Learning Interpretability Architecture](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Modelli supportati

Tutti i modelli sottoposti a `numpy.array` `pandas.DataFrame`training `iml.datatypes.DenseData`su `scipy.sparse.csr_matrix` set di dati `explain` in Python , , o format sono supportati dal pacchetto di interpretabilità dell'SDK.

Le funzioni di spiegazione accettano sia modelli che pipeline come input. Se viene fornito un modello, il `predict` modello `predict_proba` deve implementare la funzione di stima o conforme alla convenzione Scikit. Se viene fornita una pipeline (nome dello script della pipeline), la funzione di spiegazione presuppone che lo script della pipeline in esecuzione restituisca una stima. Supportiamo modelli addestrati tramite i framework di deep learning PyTorch, TensorFlow e Keras.

### <a name="local-and-remote-compute-target"></a>Destinazione di calcolo locale e remota

Il `explain` pacchetto è progettato per funzionare con destinazioni di elaborazione locali e remote. Se eseguito in locale, le funzioni SDK non contatteranno alcun servizio di Azure.If run locally, The SDK functions will not contact any Azure services. È possibile eseguire la spiegazione in remoto in Azure Machine Learning Compute e registrare le informazioni di spiegazione in Servizi di cronologia esecuzione di Azure Machine Learning.You can run explanation remotely on Azure Machine Learning Compute and log the explanation info into Azure Machine Learning Run History Services. Una volta registrate queste informazioni, i report e le visualizzazioni della spiegazione sono facilmente disponibili nell'area di lavoro di Azure Machine Learning per l'analisi degli utenti.


## <a name="next-steps"></a>Passaggi successivi

Vedere le procedure per abilitare l'interpretazione per i modelli che si allenano in locale e in risorse di calcolo remoto di Azure Machine Learning.See the [how-to](how-to-machine-learning-interpretability-aml.md) for enabling interpretability for models training both locally and on Azure Machine Learning remote compute resources. Vedere [i blocchi appunti di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) per scenari aggiuntivi.
