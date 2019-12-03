---
title: Interpretazione dei modelli nel servizio Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come spiegare il motivo per cui il modello esegue stime usando il Azure Machine Learning SDK. Può essere utilizzato durante il training e l'inferenza per comprendere il modo in cui il modello esegue stime.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 14118098e5d476dc07b21462180673b942a6224d
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74672561"
---
# <a name="model-interpretability-in-azure-machine-learning-service"></a>Interpretazione dei modelli nel servizio Azure Machine Learning
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Panoramica dell'interpretazione dei modelli

L'interpretazione è essenziale per i data scientist e i responsabili decisionali aziendali per garantire la conformità ai criteri aziendali, agli standard del settore e alle normative governative:
+ I data scientist devono poter spiegare i propri modelli a dirigenti e stakeholder, in modo che possano comprendere il valore e l'accuratezza dei risultati 
+ I responsabili delle decisioni aziendali devono avere la possibilità di fornire trasparenza agli utenti finali per ottenere e gestire la propria attendibilità

L'abilitazione della funzionalità di spiegazione di un modello di apprendimento automatico è importante durante due fasi principali dello sviluppo di modelli:
+ Durante la fase di training del ciclo di sviluppo del modello di machine learning. I progettisti e gli analizzatori di modelli possono utilizzare l'output di interpretazione di un modello per verificare le ipotesi e il trust di compilazione con le parti interessate. Usano anche le informazioni dettagliate sul modello per il debug, la convalida del comportamento del modello corrisponde ai rispettivi obiettivi e per verificare la presenza di distorsioni o funzionalità non significative.
+ Durante la fase di inferenza, la trasparenza dei modelli distribuiti consente ai dirigenti di comprendere in che modo il modello funziona e in che modo le sue decisioni trattano e influiscano sulle persone nella vita reale. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretabilità con Azure Machine Learning

Questo articolo illustra come vengono implementati i concetti relativi all'interpretazione dei modelli nell'SDK.

Utilizzando le classi e i metodi nell'SDK, è possibile ottenere:
+ Valori di importanza delle funzionalità per le funzionalità RAW e di progettazione
+ Interpretazione dei set di impostazioni del mondo reale su larga scala, durante il training e l'inferenza.
+ Visualizzazioni interattive per facilitare l'individuazione di modelli nei dati e spiegazioni in fase di training


In Machine Learning le **funzionalità** sono i campi dati usati per stimare un punto dati di destinazione. Ad esempio, per stimare il rischio di credito, è possibile che vengano usati campi dati per età, dimensioni dell'account e validità dell'account. In questo caso, l'età, le dimensioni dell'account e la validità dell'account sono **funzionalità**. L'importanza della funzionalità indica il modo in cui ogni campo dati ha interessato le stime del modello. Ad esempio, l'età può essere utilizzata molto spesso nella stima, mentre le dimensioni dell'account e l'età non influiscono significativamente sull'accuratezza della stima. Questo processo consente ai data scientist di spiegare le stime risultanti, in modo che gli stakeholder abbiano visibilità sui punti dati più importanti nel modello.

Grazie a questi strumenti, è possibile spiegare i modelli **di machine learning a livello globale su tutti i dati**o **localmente in un punto dati specifico** usando le tecnologie all'avanguardia in modo facile da usare e scalabile.

Le classi di interpretazione vengono rese disponibili tramite più pacchetti SDK. Informazioni su come [installare i pacchetti SDK per Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* `azureml.interpret`, il pacchetto principale, che contiene le funzionalità supportate da Microsoft.

* `azureml.contrib.interpret`, l'anteprima e le funzionalità sperimentali che è possibile provare.

* `azureml.train.automl.automlexplainer` pacchetto per interpretare i modelli automatici di machine learning.

> [!IMPORTANT]
> Il contenuto nello spazio dei nomi `contrib` non è completamente supportato. Man mano che le funzionalità sperimentali diventano mature, verranno spostate gradualmente nello spazio dei nomi principale.

## <a name="how-to-interpret-your-model"></a>Come interpretare il modello

È possibile applicare le classi e i metodi di interpretazione per comprendere il comportamento globale del modello o stime specifiche. Il primo è denominato spiegazione globale e quest'ultimo è denominato spiegazione locale.

I metodi possono anche essere suddivisi in categorie a seconda che il metodo sia indipendente dal modello o specifico del modello. Alcuni metodi hanno come destinazione determinati tipi di modelli. SHAP, ad esempio, è applicabile solo ai modelli basati su albero. Alcuni metodi considerano il modello come un black box, ad esempio Mimit Explainer o SHAP ' s kernel Explainer. Il pacchetto di `interpret` utilizza questi approcci diversi in base ai set di dati, ai tipi di modello e ai casi di utilizzo.

L'output è un set di informazioni sul modo in cui un determinato modello esegue la stima, ad esempio:
* Importanza della funzionalità relativa globale/locale
* Funzionalità globale/locale e relazione di stima

### <a name="explainers"></a>Explainers

Questo pacchetto usa le tecniche di interpretazione sviluppate in [interpreta-community](https://github.com/interpretml/interpret-community/), un pacchetto python open source per il training dei modelli interpretabili e la spiegazione dei sistemi blackbox ai. [Interpret-community](https://github.com/interpretml/interpret-community/) funge da host per i Explainer supportati da SDK e attualmente supporta le tecniche di interpretazione seguenti:

* **Explainer Tree Shap**: [Shap](https://github.com/slundberg/shap)' s Tree Explainer, che è incentrato sull'algoritmo di stima del valore Shap tempo polinomiale specifico degli alberi e sugli alberi.
* **Shap Deep Explainer**: basata sulla spiegazione di [Shap](https://github.com/slundberg/shap), Deep Explainer "è un algoritmo di approssimazione ad alta velocità per i valori Shap in modelli di apprendimento avanzato che si basa su una connessione a DeepLIFT descritta nel [documento Shap pin](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Sono supportati i modelli TensorFlow e i modelli keras usando il back-end TensorFlow (è disponibile anche il supporto preliminare per PyTorch) ".
* **Shap Linear Explainer**: il Explainer lineare di [Shap](https://github.com/slundberg/shap)calcola i valori Shap per un modello lineare, eventualmente tenendo conto delle correlazioni tra le funzionalità.

* **Shap kernel Explainer**: il kernel Explainer di [Shap](https://github.com/slundberg/shap)usa una regressione lineare locale ponderata per stimare i valori Shap per qualsiasi modello.
* **Mimatore mimico**: lo spiegatore MIME si basa sul concetto di training dei [modelli surrogati globali](https://christophm.github.io/interpretable-ml-book/global.html) per simulare i modelli blackbox. Un modello di surrogato globale è un modello interpretabile in modo intrinseco che viene sottoposto a training per approssimare le stime di un modello di black box nel modo più accurato possibile. I data scientist possono interpretare il modello surrogato per trarre conclusioni sul modello di black box. È possibile usare uno dei modelli interpretabili seguenti come modello di surrogato: LightGBM (LGBMExplainableModel), regressione lineare (LinearExplainableModel), modello a discesa a gradienti stocastici (SGDExplainableModel) e albero delle decisioni ( DecisionTreeExplainableModel).


* **Spiegazione dell'importanza della funzionalità di permutazione**: l'importanza della funzionalità di permutazione è una tecnica usata per spiegare i modelli di classificazione e regressione ispirati dalla [carta delle foreste casuali di Breiman](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (vedere la sezione 10). A un livello elevato, il modo in cui funziona è rimescolando in modo casuale i dati una funzionalità alla volta per l'intero set di dati e calcolando la metrica di prestazioni modificata. Maggiore è la modifica, più importante è la funzionalità.

* **Lime Explainer** (`contrib`): basato su [lime](https://github.com/marcotcr/lime), lime Explainer usa l'algoritmo di calce (limetta) interpretabile locale all'avanguardia per creare modelli surrogati locali. A differenza dei modelli surrogati globali, LIME è incentrato sul training di modelli surrogati locali per spiegare singole stime.
* **Spiegatore del testo Han** (`contrib`): il Explainer del testo Han usa una rete gerarchica di attenzione per ottenere spiegazioni del modello dai dati di testo per un determinato modello di Black Box testo. Il modello di surrogato HAN viene sottoposto a training su uno specifico output stimato del modello black box. Dopo il training a livello globale nel corpus di testo, viene aggiunto un passaggio preciso per un documento specifico, in modo da migliorare l'accuratezza delle spiegazioni. HAN usa un RNN bidirezionale con due livelli di attenzione, per la frase e la parola attenzione. Dopo aver eseguito il training del DNN sul modello di black box e aver completato l'ottimizzazione in un documento specifico, l'utente può estrarre le parole importanti dai livelli di attenzione. HAN viene mostrato come più accurato di LIME o SHAP per i dati di testo, ma anche in termini di tempo di training. Sono stati apportati miglioramenti per offrire agli utenti la possibilità di inizializzare la rete con gli incorporamenti di Word GloVe per ridurre i tempi di training. Il tempo di training può essere migliorato significativamente eseguendo HAN in una VM GPU di Azure remota. L'implementazione di HAN è descritta in ["reti gerarchiche di attenzione per la classificazione dei documenti (Yang et al., 2016)"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


* **Spiegatore tabulare**: `TabularExplainer` usa la logica seguente per richiamare i Explainer [Shap](https://github.com/slundberg/shap) diretti:

    1. Se si tratta di un modello basato su albero, applicare SHAP `TreeExplainer`, in caso contrario
    2. Se si tratta di un modello DNN, applicare SHAP `DeepExplainer`else
    3. Se è un modello lineare, applicare SHAP `LinearExplainer`, else
    3. Considerarlo come un modello nero e applicare SHAP `KernelExplainer`


in `TabularExplainer` sono stati inoltre apportati miglioramenti significativi alle funzionalità e alle prestazioni rispetto ai SHAP Explainer diretti:

* **Riepilogo del set di dati di inizializzazione**. Nei casi in cui la velocità di spiegazione è più importante, viene riepilogato il set di dati di inizializzazione e viene generato un piccolo set di campioni rappresentativi, che consente di velocizzare la spiegazione globale e locale.
* **Campionamento del set di dati di valutazione**. Se l'utente passa un set di esempi di valutazione di grandi dimensioni ma non ne è necessario la valutazione, è possibile impostare il parametro di campionamento su true per velocizzare la spiegazione globale.

Nel diagramma seguente viene illustrata la struttura corrente dei metadati diretti e metaexplainer.

[Architettura di interpretazione Machine Learning ![](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Modelli supportati

Tutti i modelli di cui è stato eseguito il training sui set di impostazioni in Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`o `scipy.sparse.csr_matrix` formato sono supportati dal pacchetto di interpretazione `explain` dell'SDK.

Le funzioni di spiegazione accettano sia i modelli sia le pipeline come input. Se viene fornito un modello, il modello deve implementare la funzione di stima `predict` o `predict_proba` conforme alla convenzione Scikit. Se viene fornita una pipeline (nome dello script della pipeline), la funzione di spiegazione presuppone che lo script della pipeline in esecuzione restituisca una stima. Sono supportati i modelli sottoposti a training tramite i Framework PyTorch, TensorFlow e keras Deep Learning.

### <a name="local-and-remote-compute-target"></a>Destinazione di calcolo locale e remota

Il pacchetto `explain` è progettato per funzionare sia con le destinazioni di calcolo locali che remote. Se l'esecuzione viene eseguita localmente, le funzioni SDK non contatteranno i servizi di Azure. È possibile eseguire la spiegazione in modalità remota Azure Machine Learning calcolo e registrare le informazioni di spiegazione in Azure Machine Learning servizi di cronologia di esecuzione. Una volta registrate queste informazioni, i report e le visualizzazioni dalla spiegazione sono immediatamente disponibili nel portale di area di lavoro di Azure Machine Learning per l'analisi degli utenti.


## <a name="next-steps"></a>Passaggi successivi

Vedere le [procedure](how-to-machine-learning-interpretability-aml.md) per abilitare l'interpretazione per i modelli di training sia localmente che su Azure Machine Learning risorse di calcolo remote. Per altri scenari, vedere i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) .
