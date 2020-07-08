---
title: Interpretazione del modello in Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come spiegare il motivo per cui il modello esegue stime usando il Azure Machine Learning SDK. Può essere utilizzato durante il training e l'inferenza per comprendere il modo in cui il modello esegue stime.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 06/30/2020
ms.openlocfilehash: 97401b2bdbcc2dc1379505f8dade443a4f1eb318
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85601684"
---
# <a name="model-interpretability-in-azure-machine-learning"></a>Interpretazione del modello in Azure Machine Learning
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

## <a name="overview-of-model-interpretability"></a>Panoramica dell'interpretazione dei modelli

L'interpretazione è essenziale per i data scientist, i revisori e i responsabili decisionali aziendali per garantire la conformità ai criteri aziendali, agli standard del settore e alle normative governative:

+ I data scientist devono poter spiegare i propri modelli a dirigenti e stakeholder, in modo che possano comprendere il valore e l'accuratezza delle proprie scoperte. Richiedono anche l'interpretazione per eseguire il debug dei modelli e prendere decisioni informate su come migliorarle. 

+ Per i revisori legali sono necessari strumenti per convalidare i modelli rispetto alla conformità alle normative e monitorare il modo in cui le decisioni dei modelli incidono sugli utenti. 

+ I decisori aziendali devono avere la possibilità di garantire la trasparenza per gli utenti finali. In questo modo è possibile ottenere e mantenere l'attendibilità.


L'abilitazione della funzionalità di spiegazione di un modello di apprendimento automatico è importante durante due fasi principali dello sviluppo di modelli:
+ Durante la fase di training, i progettisti e gli analizzatori di modelli possono utilizzare l'output di interpretazione di un modello per verificare le ipotesi e il trust di compilazione con le parti interessate. Usano anche le informazioni dettagliate sul modello per il debug, la convalida del comportamento del modello corrisponde ai rispettivi obiettivi e la verifica della correttezza del modello o delle funzionalità non significative.

+ Durante la fase di inferenza, la trasparenza dei modelli distribuiti consente ai dirigenti di comprendere in che modo il modello funziona e in che modo le sue decisioni trattano e influiscano sulle persone nella vita reale. 

## <a name="interpretability-with-azure-machine-learning"></a>Interpretabilità con Azure Machine Learning

Le classi di interpretazione vengono rese disponibili tramite più pacchetti SDK: (informazioni su come [installare i pacchetti SDK per Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py))

* `azureml.interpret`, il pacchetto principale, che contiene le funzionalità supportate da Microsoft.

* `azureml.contrib.interpret`, l'anteprima e le funzionalità sperimentali che è possibile provare.

Usare `pip install azureml-interpret` e `pip install azureml-interpret-contrib` per uso generale e `pip install azureml-contrib-interpret` per AutoML usare per ottenere i pacchetti di interpretazione.


> [!IMPORTANT]
> Il contenuto dello `contrib` spazio dei nomi non è completamente supportato. Man mano che le funzionalità sperimentali diventano mature, verranno spostate gradualmente nello spazio dei nomi principale.
.



## <a name="how-to-interpret-your-model"></a>Come interpretare il modello

Utilizzando le classi e i metodi nell'SDK, è possibile:
+ Spiegare la stima del modello generando valori di importanza della funzionalità per l'intero modello e/o i singoli punti di contatto. 
+ Ottieni l'interpretazione dei modelli sui set di risultati reali su larga scala, durante il training e l'inferenza.
+ Usare un dashboard di visualizzazione interattiva per individuare i modelli nei dati e le spiegazioni in fase di training


In Machine Learning le **funzionalità** sono i campi dati usati per stimare un punto dati di destinazione. Ad esempio, per stimare il rischio di credito, è possibile che vengano usati campi dati per età, dimensioni dell'account e validità dell'account. In questo caso, l'età, le dimensioni dell'account e la validità dell'account sono **funzionalità**. L'importanza della funzionalità indica il modo in cui ogni campo dati ha interessato le stime del modello. Ad esempio, l'età può essere utilizzata molto spesso nella stima, mentre le dimensioni dell'account e l'età non influiscono significativamente sui valori di stima. Questo processo consente ai data scientist di spiegare le stime risultanti, in modo che le parti interessate abbiano visibilità sulle funzionalità più importanti del modello.

Per informazioni sulle tecniche di interpretazione supportate, sui modelli di apprendimento automatico supportati e sugli ambienti di esecuzione supportati, vedere qui.


## <a name="supported-interpretability-techniques"></a>Tecniche di interpretazione supportate

 `azureml-interpret`Usa le tecniche di interpretazione sviluppate in [interpreta-community](https://github.com/interpretml/interpret-community/), un pacchetto python open source per il training dei modelli interpretabili e la spiegazione dei sistemi blackbox ai. [Interpret-community](https://github.com/interpretml/interpret-community/) funge da host per i Explainer supportati da SDK e attualmente supporta le tecniche di interpretazione seguenti:

|Tecnica di interpretazione|Descrizione|Type|
|--|--|--------------------|
|Spiegazione dell'albero SHAP| [Shap](https://github.com/slundberg/shap)' s Tree Explainer, che è incentrato sull'algoritmo di stima del valore Shap del tempo polinomiale specifico per **alberi ed è costituito da alberi**.|Specifico del modello|
|SHAP spiegazione approfondita| In base alla spiegazione fornita da SHAP, Deep Explainer "è un algoritmo di approssimazione ad alta velocità per i valori SHAP nei modelli di apprendimento avanzato che si basa su una connessione a DeepLIFT descritta nel [documento Shap pin](https://papers.nips.cc/paper/7062-a-unified-approach-to-interpreting-model-predictions). Sono supportati i modelli **TensorFlow** e i modelli **keras** usando il back-end TensorFlow (è disponibile anche il supporto preliminare per PyTorch) ".|Specifico del modello|
|SHAP Linear Explainer| Il Explainer lineare di SHAP calcola i valori SHAP per un **modello lineare**, eventualmente tenendo conto delle correlazioni tra funzionalità.|Specifico del modello|
|Spiegazione del kernel SHAP| Il spiegatore kernel di SHAP usa una regressione lineare locale ponderata in modo specifico per stimare i valori di SHAP per **qualsiasi modello**.|Indipendente dal modello|
|MIME Explainer (surrogato globale)| Mimic Explainer si basa sul concetto di training dei [modelli surrogati globali](https://christophm.github.io/interpretable-ml-book/global.html) per simulare i modelli blackbox. Un modello di surrogato globale è un modello interpretabile in modo intrinseco che viene sottoposto a training per approssimare le stime di **qualsiasi modello di Black Box** nel modo più accurato possibile. I data scientist possono interpretare il modello surrogato per trarre conclusioni sul modello di black box. È possibile usare uno dei modelli interpretabili seguenti come modello di surrogato: LightGBM (LGBMExplainableModel), regressione lineare (LinearExplainableModel), modello a discesa stocastica a gradiente (SGDExplainableModel) e albero delle decisioni (DecisionTreeExplainableModel).|Indipendente dal modello|
|Spiegazione dell'importanza della funzionalità di permutazione (PFI)| L'importanza della funzionalità di permutazione è una tecnica usata per spiegare i modelli di classificazione e regressione ispirati dalla [carta delle foreste casuali di Breiman](https://www.stat.berkeley.edu/~breiman/randomforest2001.pdf) (vedere la sezione 10). A un livello elevato, il modo in cui funziona è rimescolando in modo casuale i dati una funzionalità alla volta per l'intero set di dati e calcolando la metrica di prestazioni modificata. Maggiore è la modifica, maggiore è l'importanza della funzionalità. PFI può spiegare il comportamento complessivo di **qualsiasi modello sottostante** , ma non spiega le singole stime. |Indipendente dal modello|




Oltre alle tecniche di interpretazione descritte in precedenza, è supportato un altro Explainer basato su SHAP, denominato `TabularExplainer` . A seconda del modello, `TabularExplainer` Usa uno dei Shap Explainer supportati:

* TreeExplainer per tutti i modelli basati su albero
* DeepExplainer per modelli DNN
* LinearExplainer per modelli lineari
* KernelExplainer per tutti gli altri modelli

`TabularExplainer`ha inoltre apportato miglioramenti significativi a funzionalità e prestazioni rispetto ai SHAP Explainer diretti:

* **Riepilogo del set di dati di inizializzazione**. Nei casi in cui la velocità di spiegazione è la più importante, viene riepilogato il set di dati di inizializzazione e viene generato un piccolo set di campioni rappresentativi, che accelera la generazione di valori di importanza complessiva della funzionalità.
* **Campionamento del set di dati di valutazione**. Se l'utente passa un set di esempi di valutazione di grandi dimensioni, ma non ne è effettivamente necessario la valutazione, è possibile impostare il parametro di campionamento su true per velocizzare il calcolo delle spiegazioni complessive del modello.

Il diagramma seguente mostra la struttura corrente dei Explainer supportati.

[![Architettura di interpretazione Machine Learning](./media/how-to-machine-learning-interpretability/interpretability-architecture.png)](./media/how-to-machine-learning-interpretability/interpretability-architecture.png#lightbox)


## <a name="supported-machine-learning-models"></a>Modelli di apprendimento automatico supportati

Il `azureml.interpret` pacchetto dell'SDK supporta i modelli sottoposti a training con i formati di set di dati seguenti:
- `numpy.array`
- `pandas.DataFrame`
- `iml.datatypes.DenseData`
- `scipy.sparse.csr_matrix`

Le funzioni di spiegazione accettano sia i modelli sia le pipeline come input. Se viene fornito un modello, il modello deve implementare la funzione di stima `predict` o `predict_proba` conforme alla convenzione Scikit. Se il modello non supporta questa operazione, è possibile eseguire il wrapping del modello in una funzione che genera lo stesso risultato di `predict` o `predict_proba` in Scikit e utilizzare tale funzione wrapper con il Explainer selezionato. Se viene fornita una pipeline, la funzione di spiegazione presuppone che lo script della pipeline in esecuzione restituisca una stima. Con questa tecnica di wrapping, può supportare i modelli sottoposti a `azureml.interpret` training tramite i Framework di apprendimento avanzato PyTorch, TensorFlow e keras, oltre ai modelli di apprendimento automatico classici.

## <a name="local-and-remote-compute-target"></a>Destinazione di calcolo locale e remota

Il `azureml.interpret` pacchetto è progettato per funzionare sia con le destinazioni di calcolo locali che remote. Se l'esecuzione viene eseguita localmente, le funzioni SDK non contatteranno i servizi di Azure. 

È possibile eseguire la spiegazione in modalità remota Azure Machine Learning calcolo e registrare le informazioni sulla spiegazione nel servizio di cronologia di esecuzione Azure Machine Learning. Una volta registrate queste informazioni, i report e le visualizzazioni dalla spiegazione sono immediatamente disponibili in Azure Machine Learning Studio per l'analisi degli utenti.


## <a name="next-steps"></a>Passaggi successivi

- Vedere le [procedure](how-to-machine-learning-interpretability-aml.md) per abilitare l'interpretazione per i modelli di training sia localmente che su Azure Machine Learning risorse di calcolo remote. 
- Per altri scenari, vedere i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model) . 
- Se si è interessati all'interpretazione per gli scenari di testo, vedere [interpretare il testo](https://github.com/interpretml/interpret-text), un repository Open Source correlato per [interpretare la community](https://github.com/interpretml/interpret-community/), per le tecniche di interpretazione per la PNL. `azureml.interpret`il pacchetto attualmente non supporta queste tecniche, ma è possibile iniziare con un [notebook di esempio sulla classificazione del testo](https://github.com/interpretml/interpret-text/blob/master/notebooks/text_classification/text_classification_classical_text_explainer.ipynb).
