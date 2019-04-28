---
title: Interpretabilità dei modelli
titleSuffix: Azure Machine Learning service
description: Informazioni su come spiegare il motivo per cui il modello esegue stime usando il SDK di interpretazione di Azure Machine Learning. Può essere utilizzato durante il training e inferenza per comprendere come il modello effettua stime.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/09/2019
ms.openlocfilehash: 668551603dfa2a9c42f4538fd9a66ee646e1feb7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60817262"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Interpretazione di modello con il servizio di Azure Machine Learning

In questo articolo si apprenderà come spiegare il motivo per cui il modello creato le stime viene eseguita tramite l'interpretazione di SDK di Azure Machine Learning. La possibilità di spiegare il modello è importante per i motivi seguenti:

* I clienti e stakeholder desiderano **se si possono considerare attendibili le stime del modello di appartenenza**.
* Come un data scientist, si desidera comprendere **saper eseguire query sul modello per trovare approfondimenti**. È necessario anche strumenti per prendere decisioni informate sul **come migliorare il modello**.
* Un'azienda, è necessario comprendere **il comportamento del modello con diversi input distribuzioni** e **il modello di comportamento mentre si analizza l'input specifico**.

Interpretazione di Machine learning è importante in due fasi del ciclo di sviluppo di apprendimento automatico: 

* Durante **training**: Gli analizzatori e i progettisti di modelli richiedono strumenti interpretazione per spiegare l'output di un modello agli stakeholder per fiducia va costruita. Devono anche insights nel modello in modo che possano eseguire il debug di modello e prendere decisioni sul fatto che il comportamento corrisponde a loro obiettivi. Infine, è necessario garantire che il modello non è influenzato.

* Durante **inferenza**: Le stime devono essere giustificati per le persone che usano il modello. Ad esempio, il motivo per cui è stato il modello di negare un mutuo o stimare che un portfolio di investimento comporta un rischio più elevato?

L'interpretazione di SDK di Azure Machine Learning include tecnologie sviluppate da Microsoft e collaudata le librerie di terze parti (ad esempio, & proprietà e verde LIMONE). il SDK consente di creare un'API comune tra le librerie integrate e si integra servizi di Azure Machine Learning. Con questo SDK, è possibile spiegare modelli di machine learning **a livello globale in tutti i dati**, o **localmente su un punto dati specifico** usando le tecnologie di avanzato in modo scalabile e facile da usare.

## <a name="how-does-it-work"></a>Come funziona?

Azure Machine Learning interpretazione possono essere applicati per comprendere il comportamento globale o specifiche stime del modello. Il primo è denominato spiegazione globale e quest'ultimo viene chiamato spiegazione locale.

Metodi di Machine Learning interpretazione Azure possono essere classificati anche a seconda che il metodo sia indipendente dal modello o un modello specifico. Alcuni metodi come destinazione un determinato tipo di modelli. Ad esempio, spiegazione di struttura ad albero di & proprietà si applica solo ai modelli basati su albero. Alcuni metodi di considerano il modello come una casella nera, ad esempio che riproducono spiegazione o spiegazione del & proprietà kernel. SDK di Azure Machine Learning interpretazione sfrutta questi diversi approcci basati su set di dati, tipi di modello e casi d'uso.

Azure Machine Learning interpretazione restituisce un set di informazioni sul modo in cui un modello esegue la stima. Le informazioni includono gli elementi, ad esempio:

* Importanza delle caratteristiche relative globale o locale
* Relazione tra funzionalità e stima di globale o locale

## <a name="architecture"></a>Architettura

SDK di Azure Machine Learning interpretazione è strutturato in due pacchetti di Python:

* [azureml.Explain.Model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py) -il pacchetto principale, che contiene le funzionalità supportate da Microsoft.
* `azureml.contrib.explain.model` -Visualizzare in anteprima e le funzionalità sperimentali che è possibile provare.

    > [!IMPORTANT]
    > Le cose in contrib non sono completamente supportate. Man mano che diventano le funzionalità sperimentali mature, verranno spostati gradualmente per il pacchetto principale.

### <a name="explainers"></a>Explainers

L'interpretazione di SDK di Azure Machine Learning introduce due set di explainers: Explainers diretto ed Explainers Meta.

__Indirizzare explainers__ provengono dalle librerie integrate. il SDK esegue il wrapping di tutte le explainers in modo da esporre un'API comune e un formato di output. Di seguito sono un elenco del explainers diretta disponibili nel SDK:

> [!TIP]
> Se si è più a proprio agio usando direttamente questi explainers, è possibile richiamare direttamente li invece di usare le API comuni e il formato di output.

* **Albero spiegazione**: Spiegazione di struttura ad albero di & proprietà, che è incentrato sul tempo polinomiale rapido & proprietà valore stima specifico dell'algoritmo alberi e insiemi di alberi delle.
* **Spiegazione approfondita**: Base la spiegazione di & proprietà, spiegazione approfondita "è un algoritmo di approssimazione ad alta velocità per i valori di & proprietà nei modelli di apprendimento profondo si basa su una connessione con DeepLIFT descritto nel documento NIPS & proprietà. Sono supportati i modelli di TensorFlow e modelli Keras con TensorFlow back-end (è inoltre disponibile supporto preliminare per PyTorch) ".
* **Spiegazione: informazioni su kernel**: Spiegazione: informazioni su Kernel di & proprietà Usa una regressione lineare locale ponderata in modo speciale per stimare i valori di & proprietà per qualsiasi modello.
* **Simulare spiegazione**: Spiegazione che riproducono si basa sul concetto di modelli globali surrogato. Un modello di surrogato globale è un modello può essere interpretato in modo intrinseco che viene eseguito il training per approssimare il modo più accurato possibile le stime di un modello di casella nera. Esperto di dati può interpretare il modello di surrogato per trarre conclusioni sul modello di casella nera.
* **Spiegazione: informazioni su verde LIMONE**: Basato su verde LIMONE, spiegazione LIME utilizza l'algoritmo d'avanguardia locale può essere interpretato indipendente dal modello spiegazioni (verde) per creare modelli di surrogato locale. A differenza dei modelli di surrogato globale, verde LIMONE concentra training dei modelli di surrogato locale per spiegare le stime singole.
* **Spiegazione: informazioni su testo HAN**: Spiegazione: informazioni su testo HAN Usa un Hierarchical Attention Network per ottenere una spiegazione del modello dai dati di testo per un modello di testo determinato casella nera. È il training del modello di surrogati di HAN negli output stimato del modello specifico docente. Dopo il training a livello globale nel corpo di testo, è stato aggiunto un passaggio di ottimizzazione per un documento specifico per migliorare l'accuratezza delle descrizioni. HAN Usa un bidirezionale RNN con due livelli di attenzione, attenzione parole e frasi. Una volta che la rete neurale profonda viene eseguito il training del modello per docenti e ottimizzare in un documento specifico, è possibile estrarre importances la parola dei livelli di attenzione. Sono state trovate HAN sia più accurati rispetto a verde LIMONE o & proprietà per i dati di testo, ma più costose in termini di nonché tempi di training. Tuttavia, sono stati apportati miglioramenti per la durata del training, assegnando all'utente la possibilità di inizializzare la rete con rappresentazioni distribuite delle parole GloVe, anche se è ancora lento. Il tempo di training può essere migliorato in modo significativo tramite l'esecuzione HAN in una VM GPU di Azure remota. L'implementazione di HAN è descritta in 'Reti di attenzione gerarchici per la classificazione di documento (Yang e così via, 2016)' ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Explainers Meta__ automaticamente selezionare una spiegazione: informazioni su direct appropriato e generare le migliori informazioni spiegazione in base al modello specificato e un set di dati. Il explainers meta sfruttare tutte le librerie di (& proprietà, verde, sono simili e così via) che è stato integrato o sviluppate. Di seguito sono le explainers meta disponibili nel SDK:

* **Spiegazione in formato tabulare**: Usato con set di dati tabulari.
* **Spiegazione: informazioni su testo**: Utilizzato con i set di dati di testo.

Inoltre a metamodelli selezione della explainers diretto, explainers meta sviluppare funzionalità aggiuntive all'inizio le librerie sottostanti e migliorare la velocità e scalabilità tramite il explainers diretto.

Attualmente `TabularExplainer` impiega la logica seguente per richiamare il Explainers diretto:

1. Se è un modello di struttura ad albero, applicare `TreeExplainer`, else
2. Se si tratta di un modello di rete neurale profonda, applicare `DeepExplainer`, else
3. Considerarlo come un modello di casella nera e applicare `KernelExplainer`

L'intelligence incorporata `TabularExplainer` diventerà più sofisticate come altre librerie sono integrate nel SDK e contiene informazioni su vantaggi e svantaggi di ogni spiegazione.

`TabularExplainer` anche ha apportato miglioramenti significativi delle prestazioni e funzionalità tramite i Explainers diretto:

* **Riepilogo del set di dati di inizializzazione**. Nei casi in cui la velocità di spiegazione è più importante, si riepilogano il set di dati di inizializzazione e genera un set ridotto di campioni rappresentativi, che consente di velocizzare spiegazione globale e locale.
* **Il set di dati di valutazione di campionamento**. Se l'utente passa in un ampio set di esempi di valutazione e non necessariamente effettivamente tutti gli elementi da valutare, il parametro di campionamento può essere impostato su true per velocizzare la spiegazione globale.

Il diagramma seguente mostra la relazione tra i due set di direct ed explainers meta.

[![Architettura di interpretazione di Machine Learning](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Modelli supportati

Tutti i modelli che vengono sottoposti a training sul set di dati in Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, o `scipy.sparse.csr_matrix` formato sono supportati da Machine Learning interpretazione di SDK.

Le funzioni di spiegazione accettano entrambi i modelli e le pipeline come input. Se viene fornito un modello, il modello deve implementare la funzione di stima `predict` o `predict_proba` conforme alla convenzione di Scikit. Se viene fornita una pipeline (nome dello script di pipeline), la funzione di spiegazione presuppone che l'esecuzione della pipeline di uno script restituisce una stima.

### <a name="local-and-remote-compute-target"></a>Destinazione di calcolo locali e remoti

il SDK di interpretazione di Machine Learning è progettato per funzionare con entrambe le destinazioni di calcolo locali e remoti. Se eseguito in locale, le funzioni del SDK non contatterà tutti i servizi Azure. È possibile eseguire in remoto una spiegazione nel calcolo di Azure Machine Learning e registrare le informazioni nella spiegazione in servizi di Azure Machine Learning eseguire cronologia. Una volta che tale informazione viene registrata, report e visualizzazioni dalla spiegazione sono immediatamente disponibili nel portale di Azure Machine Learning dell'area di lavoro per analisi degli utenti.

## <a name="train-and-explain-locally"></a>Eseguire il training e spiegare in locale

1. Eseguire il training del modello in un notebook Jupyter locale. 

    ``` python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Chiamare la spiegazione: Per avviare un oggetto di spiegazione, è necessario passare il modello, i dati di training, la funzionalità di interesse (facoltativo) e i nomi delle classi di output (se classificazione) per la spiegazione. Di seguito viene illustrato come creare un'istanza di un oggetto di spiegazione tramite [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py), e `LimeExplainer` in locale. `TabularExplainer` viene chiamato uno dei tre explainers sotto (`TreeExplainer`, `DeepExplainer`, o `KernelExplainer`) e viene automaticamente selezionando quello più appropriato per il caso d'uso. È tuttavia possibile chiamare direttamente ognuno dei relativi tre explainers sottostante.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    oppure
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Ottenere la funzionalità globale i valori di priorità.

    ```python
    # You can use the training data or the test data here. 
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. I valori di priorità delle funzionalità locali: usare chiamate di funzione seguenti per descrivere una singola istanza o un gruppo di istanze.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    oppure
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

## <a name="train-and-explain-remotely"></a>Eseguire il training e spiegare in modalità remota

Anche se è possibile eseguire il training sulle varie destinazioni di calcolo supportate dal servizio di Azure Machine Learning, l'esempio in questa sezione viene illustrato come eseguire questa operazione usando AMLCompute.

1. Creare uno script di training in un notebook Jupyter locale (ad esempio, run_explainer.py).

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine    
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0,:])
    # upload global and local explanation objects to Run History
    upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Seguire le istruzioni sullo [configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md#amlcompute) per informazioni su come configurare un Azure calcolo di Machine Learning come destinazione di calcolo e invia l'esecuzione di training.

3. Scaricare la spiegazione in locale Jupyter notebook. 
    > [!IMPORTANT]
    > Le cose in contrib non sono completamente supportate. Man mano che diventano le funzionalità sperimentali mature, verranno spostati gradualmente per il pacchetto principale.

    ``` python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="next-steps"></a>Fasi successive

Per una raccolta di notebook di Jupyter che illustrano le istruzioni riportate sopra, vedere la [notebook di esempio Azure Machine Learning interpretazione](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
