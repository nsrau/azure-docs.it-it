---
title: Interpretabilità dei modelli
titleSuffix: Azure Machine Learning service
description: Informazioni su come spiegare il motivo per cui il modello esegue stime usando il Azure Machine Learning SDK. Può essere utilizzato durante il training e l'inferenza per comprendere il modo in cui il modello esegue stime.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: 2e8eb79c4baebebb1974a977394215545ef944db
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69872395"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Interpretazione dei modelli con il servizio Azure Machine Learning

In questo articolo si apprenderà come spiegare il motivo per cui il modello ha eseguito le stime con i diversi pacchetti di interpretazione del Azure Machine Learning Python SDK.

Utilizzando le classi e i metodi nell'SDK, è possibile ottenere:
+ Valori di importanza delle funzionalità per le funzionalità RAW e di progettazione
+ Interpretazione dei set di impostazioni del mondo reale su larga scala, durante il training e l'inferenza.
+ Visualizzazioni interattive per facilitare l'individuazione di modelli nei dati e spiegazioni in fase di training

Durante la fase di training del ciclo di sviluppo, i progettisti e gli analizzatori di modelli possono utilizzare l'output di interpretazione di un modello per verificare le ipotesi e il trust di compilazione con le parti interessate.  Usano anche le informazioni dettagliate sul modello per il debug, la convalida del comportamento del modello corrisponde ai rispettivi obiettivi e la verifica della distorsione.

In Machine Learning le **funzionalità** sono i campi dati usati per stimare un punto dati di destinazione. Ad esempio, per stimare il rischio di credito, è possibile che vengano usati campi dati per età, dimensioni dell'account e validità dell'account. In questo caso, l'età, le dimensioni dell'account e la validità dell'account sono **funzionalità**. L'importanza della funzionalità indica il modo in cui ogni campo dati ha interessato le stime del modello. Ad esempio, l'età può essere utilizzata molto spesso nella stima, mentre le dimensioni dell'account e l'età non influiscono significativamente sull'accuratezza della stima. Questo processo consente ai data scientist di spiegare le stime risultanti, in modo che gli stakeholder abbiano visibilità sui punti dati più importanti nel modello.

Grazie a questi strumenti, è possibile spiegare i modelli **di machine learning a livello globale su tutti i dati**o **localmente in un punto dati specifico** usando le tecnologie all'avanguardia in modo facile da usare e scalabile.

Le classi di interpretazione vengono rese disponibili tramite più pacchetti SDK. Informazioni su come [installare i pacchetti SDK per Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), il pacchetto principale, che contiene le funzionalità supportate da Microsoft.

* `azureml.contrib.explain.model`, l'anteprima e le funzionalità sperimentali che è possibile provare.

* `azureml.train.automl.automlexplainer`pacchetto per interpretare i modelli automatici di machine learning.

> [!IMPORTANT]
> Il `contrib` contenuto dello spazio dei nomi non è completamente supportato. Man mano che le funzionalità sperimentali diventano mature, verranno spostate gradualmente nello spazio dei nomi principale.

## <a name="how-to-interpret-your-model"></a>Come interpretare il modello

È possibile applicare le classi e i metodi di interpretazione per comprendere il comportamento globale del modello o stime specifiche. Il primo è denominato spiegazione globale e quest'ultimo è denominato spiegazione locale.

I metodi possono anche essere suddivisi in categorie a seconda che il metodo sia indipendente dal modello o specifico del modello. Alcuni metodi hanno come destinazione determinati tipi di modelli. SHAP, ad esempio, è applicabile solo ai modelli basati su albero. Alcuni metodi considerano il modello come un black box, ad esempio Mimit Explainer o SHAP ' s kernel Explainer. Il `explain` pacchetto utilizza questi approcci diversi in base ai set di dati, ai tipi di modello e ai casi di utilizzo.

L'output è un set di informazioni sul modo in cui un determinato modello esegue la stima, ad esempio:
* Importanza della funzionalità relativa globale/locale

* Funzionalità globale/locale e relazione di stima

### <a name="explainers"></a>Explainers

Sono disponibili due set di spiegatori: I Explainer diretti e i meta Explainer nell'SDK.

I __Explainer diretti__ provengono da librerie integrate. L'SDK esegue il wrapping di tutti i Explainer in modo che espongano un'API comune e un formato di output. Se si ha più dimestichezza con questi spiegatori, è possibile richiamarli direttamente invece di usare l'API comune e il formato di output. Di seguito è riportato un elenco dei Explainer diretti disponibili nell'SDK:

* **Spiegazione dell'albero Shap**: SHAP ' s Tree Explainer, che è incentrato sull'algoritmo di stima del valore SHAP del tempo polinomiale specifico per alberi ed è costituito da alberi.
* **Spiegazione approfondita di Shap**: In base alla spiegazione fornita da SHAP, Deep Explainer "è un algoritmo di approssimazione ad alta velocità per i valori SHAP nei modelli di apprendimento avanzato che si basa su una connessione a DeepLIFT descritta nel documento SHAP pin. Sono supportati i modelli TensorFlow e i modelli keras usando il back-end TensorFlow (è disponibile anche il supporto preliminare per PyTorch) ".
* **Spiegazione del kernel Shap**: Il spiegatore kernel di SHAP usa una regressione lineare locale ponderata in modo specifico per stimare i valori di SHAP per qualsiasi modello.
* **Spiegatore Mimic**: Il Explainer MIME si basa sul concetto di modelli surrogati globali. Un modello di surrogato globale è un modello interpretabile in modo intrinseco che viene sottoposto a training per approssimare le stime di un modello di black box nel modo più accurato possibile. I data scientist possono interpretare il modello surrogato per trarre conclusioni sul modello di black box. È possibile usare uno dei modelli interpretabili seguenti come modello surrogato: LightGBM (LinearExplainableModel), regressione lineare (LinearExplainableModel), modello a discesa di sfumatura stocastica (SGDExplainableModel) e albero delle decisioni (DecisionTreeExplainableModel).


* **Spiegazione dell'importanza della funzionalità**di permutazione: L'importanza della funzionalità di permutazione è una tecnica usata per spiegare i modelli di classificazione e regressione ispirati dalla [carta delle foreste casuali di Breiman](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (vedere la sezione 10). A un livello elevato, il modo in cui funziona è rimescolando in modo casuale i dati una funzionalità alla volta per l'intero set di dati e calcolando il grado di riduzione della metrica delle prestazioni di interesse. Maggiore è la modifica, più importante è la funzionalità.

* **Spiegazione di lime** (`contrib`): In base a LIME, il Delucidatore LIME usa l'algoritmo di CALCe per la creazione di modelli di surrogati locali all'avanguardia. A differenza dei modelli surrogati globali, LIME è incentrato sul training di modelli surrogati locali per spiegare singole stime.
* **Spiegazione del testo Han** (`contrib`): Il Explainer del testo HAN usa una rete gerarchica di attenzione per ottenere spiegazioni del modello dai dati di testo per un determinato modello di testo black box. Il modello di surrogato HAN viene sottoposto a training per gli output previsti di un determinato modello di insegnante. Dopo aver eseguito il training a livello globale nel corpus di testo, è stato aggiunto un passaggio preciso per un documento specifico, in modo da migliorare l'accuratezza delle spiegazioni. HAN usa un RNN bidirezionale con due livelli di attenzione, per la frase e la parola attenzione. Dopo aver eseguito il training del DNN sul modello Teacher e aver ottimizzato in base a un documento specifico, è possibile estrarre le parole importanti dai livelli di attenzione. È stato rilevato che HAN è più accurato di LIME o SHAP per i dati di testo ma anche più costoso in termini di tempo di training. Tuttavia, sono stati apportati miglioramenti al tempo di training offrendo all'utente la possibilità di inizializzare la rete con gli incorporamenti di Word GloVe, sebbene sia ancora lenta. Il tempo di training può essere migliorato significativamente eseguendo HAN in una VM GPU di Azure remota. L'implementazione di HAN è descritta in ["reti gerarchiche di attenzione per la classificazione dei documenti (Yang et al., 2016)"](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


I __meta Explainers__ selezionano automaticamente un Explainer diretto appropriato e generano le informazioni di spiegazione migliore in base al modello e ai set di dati specificati. I meta Explainer sfruttano tutte le librerie (SHAP, LIME, Mimic e così via) integrate o sviluppate. Di seguito sono riportati i meta Explainer disponibili nell'SDK:

* **Spiegatore tabulare**: Utilizzato con i set di impostazioni di tabella.
* **Spiegazione del testo**: Utilizzato con i set di impostazioni di testo.
* **Spiegazione dell'immagine**: Utilizzato con i set di impostazioni di immagine.

Oltre al metaselezione dei Explainer diretti, i meta Explainer sviluppano funzionalità aggiuntive sulle librerie sottostanti e migliorano la velocità e la scalabilità rispetto ai Explainer diretti.

Attualmente `TabularExplainer` usa la logica seguente per richiamare i Explainer Shap diretti:

1. Se si tratta di un modello basato su albero, applicare `TreeExplainer`Shap, else
2. Se si tratta di un modello DNN, applicare `DeepExplainer`Shap, else
3. Considerarlo come un modello nero e applicare SHAP`KernelExplainer`

L'Intelligence incorporata `TabularExplainer` in diventerà più sofisticata Man mano che un numero maggiore di librerie è integrato nell'SDK e vengono fornite informazioni su vantaggi e svantaggi di ogni Explainer.

`TabularExplainer`ha inoltre apportato miglioramenti significativi alle funzionalità e alle prestazioni rispetto ai Explainer diretti:

* **Riepilogo del set di dati di inizializzazione**. Nei casi in cui la velocità di spiegazione è più importante, viene riepilogato il set di dati di inizializzazione e viene generato un piccolo set di campioni rappresentativi, che consente di velocizzare la spiegazione globale e locale.
* **Campionamento del set di dati di valutazione**. Se l'utente passa un set di esempi di valutazione di grandi dimensioni ma non ne è necessario la valutazione, è possibile impostare il parametro di campionamento su true per velocizzare la spiegazione globale.

Nel diagramma seguente viene illustrata la struttura corrente dei metadati diretti e metaexplainer.

[![Architettura di interpretazione Machine Learning](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Modelli supportati

Qualsiasi modello di cui è stato eseguito il training su `numpy.array`set `pandas.DataFrame`di `iml.datatypes.DenseData`impostazioni in `scipy.sparse.csr_matrix` Python,, `explain` o format è supportato dal pacchetto di interpretazione dell'SDK.

Le funzioni di spiegazione accettano sia i modelli sia le pipeline come input. Se viene fornito un modello, il modello deve implementare la funzione `predict` di stima o `predict_proba` conforme alla convenzione Scikit. Se viene fornita una pipeline (nome dello script della pipeline), la funzione di spiegazione presuppone che lo script della pipeline in esecuzione restituisca una stima. Sono supportati i modelli sottoposti a training tramite i Framework PyTorch, TensorFlow e keras Deep Learning.

### <a name="local-and-remote-compute-target"></a>Destinazione di calcolo locale e remota

Il `explain` pacchetto è progettato per funzionare sia con le destinazioni di calcolo locali che remote. Se l'esecuzione viene eseguita localmente, le funzioni SDK non contatteranno i servizi di Azure. È possibile eseguire la spiegazione in modalità remota Azure Machine Learning calcolo e registrare le informazioni di spiegazione in Azure Machine Learning servizi di cronologia di esecuzione. Una volta registrate queste informazioni, i report e le visualizzazioni dalla spiegazione sono immediatamente disponibili nel portale di area di lavoro di Azure Machine Learning per l'analisi degli utenti.

## <a name="interpretability-in-training"></a>Interpretazione nel training

### <a name="train-and-explain-locally"></a>Esegui il training e spiega in locale

1. Eseguire il training del modello in un notebook Jupyter locale.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Chiama lo spiegatore: Per inizializzare un oggetto Explainer, è necessario passare il modello e alcuni dati di training al costruttore del sistema di spiegazione. Facoltativamente, è anche possibile passare i nomi delle funzionalità e i nomi delle classi di output (se si esegue la classificazione), che verranno usati per rendere più informative le spiegazioni e le visualizzazioni. Di seguito viene illustrato come creare un'istanza di un oggetto Explainer usando [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)e [PFIExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) in locale. `TabularExplainer`chiama uno dei tre Explainer Shap sotto (`TreeExplainer`, `DeepExplainer`o `KernelExplainer`) e seleziona automaticamente quello più appropriato per il caso d'uso. È tuttavia possibile chiamare direttamente ciascuno dei tre Explainer sottostanti.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    oppure

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel
    from azureml.explain.model.mimic.models.linear_model import SGDExplainableModel
    from azureml.explain.model.mimic.models.tree_model import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns. 
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```
   oppure

    ```python
    from azureml.explain.model.permutation.permutation_importance import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

3. Ottenere i valori di importanza della funzionalità globale.

    ```python
    # you can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    
    # if you used the PFIExplainer in the previous step, use the next line of code instead
    # global_explanation = explainer.explain_global(x_train, true_labels=y_test)

    # sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))

    # alternatively, you can print out a dictionary that holds the top K feature names and values
    global_explanation.get_feature_importance_dict()
    ```

4. Ottenere i valori di importanza della funzionalità locale: usare le chiamate di funzione seguenti per illustrare una singola istanza o un gruppo di istanze. Si noti che PFIExplainer non supporta le spiegazioni locali.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    oppure

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Eseguire il training e spiegare in remoto

Sebbene sia possibile eseguire il training sulle varie destinazioni di calcolo supportate dal servizio Azure Machine Learning, l'esempio riportato in questa sezione illustra come eseguire questa operazione usando una destinazione di calcolo Azure Machine Learning.

1. Creare uno script di training in un notebook Jupyter locale, ad esempio run_explainer. py.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                x_train, 
                                features=feature_names, 
                                classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Seguire le istruzioni riportate in [configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md#amlcompute) per informazioni su come configurare un Azure Machine Learning calcolo come destinazione di calcolo e inviare l'esecuzione del training.

3. Scaricare la spiegazione nel notebook di Jupyter locale.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>Visualizzazioni

Usare il dashboard di visualizzazione per comprendere e interpretare il modello:

### <a name="global-visualizations"></a>Visualizzazioni globali

I tracciati seguenti forniscono una visualizzazione globale del modello sottoposto a training insieme alle relative stime e spiegazioni.

|Tracciato|DESCRIZIONE|
|----|-----------|
|Esplorazione dei dati| Panoramica del set di dati insieme ai valori di stima.|
|Importanza globale|Mostra le principali funzionalità importanti della K (configurabile K) a livello globale. Questo grafico è utile per comprendere il comportamento globale del modello sottostante.|
|Esplorazione spiegazione|Viene illustrato come una funzionalità è responsabile di apportare una modifica ai valori di stima del modello (o probabilità dei valori di stima). |
|Riepilogo| Utilizza i valori di importanza della funzionalità locale con segno in tutti i punti dati per mostrare la distribuzione dell'effetto di ogni funzionalità sul valore di stima.|

[![Dashboard di visualizzazione globale](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Visualizzazioni locali

È possibile fare clic su un singolo punto dati in qualsiasi momento dei tracciati precedenti per caricare il tracciato di importanza della funzionalità locale per il punto dati specificato.

|Tracciato|Descrizione|
|----|-----------|
|Importanza locale|Mostra le principali funzionalità importanti della K (configurabile K) a livello globale. Questo grafico è utile per comprendere il comportamento locale del modello sottostante in un punto dati specifico.|
|Esplorazione della perturbazione|Consente di modificare i valori delle funzionalità del punto dati selezionato e di osservare il modo in cui tali modifiche influiranno sul valore di stima.|
|Singola previsione condizionale (ICE)| Consente di modificare un valore della funzionalità da un valore minimo a un valore massimo per vedere come viene modificata la stima del punto dati quando una funzionalità viene modificata.|

[![Importanza della funzionalità locale del dashboard di visualizzazione](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![Perturbazione delle funzionalità del dashboard di visualizzazione](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![Tracciati di ghiaccio del dashboard di visualizzazione](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Si noti che è necessario che le estensioni widget del dashboard di visualizzazione siano abilitate prima dell'avvio del kernel Jupyter.

* Jupyter Notebook

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
Per caricare il dashboard di visualizzazione, usare il codice seguente:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Trasformazioni di funzionalità non elaborate

Facoltativamente, è possibile passare la pipeline di trasformazione delle funzionalità al Explainer per ricevere spiegazioni in termini di funzionalità non elaborate prima della trasformazione, anziché delle funzionalità di progettazione. Se si ignora questo, il Explainer fornisce spiegazioni in termini di funzionalità progettate.

Il formato delle trasformazioni supportate è uguale a quello descritto in [sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). In generale, tutte le trasformazioni sono supportate finché operano su una singola colonna e sono quindi chiaramente da uno a molti. 

È possibile illustrare le funzionalità non elaborate `sklearn.compose.ColumnTransformer` usando un o un elenco di Tuple Transformer adattate. La cella seguente utilizza `sklearn.compose.ColumnTransformer`. 

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Se si vuole eseguire l'esempio con l'elenco delle tuple di Transformer adattate, usare il codice seguente: 
```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="interpretability-at-inferencing-time"></a>Interpretazione al momento dell'inferenza

Il Explainer può essere distribuito insieme al modello originale e può essere utilizzato in fase di assegnazione dei punteggi per fornire le informazioni sulla spiegazione locale. Sono inoltre disponibili spiegatori per l'assegnazione di punteggi più leggeri per rendere l'interpretazione più efficiente in termini di tempo di inferenza. Il processo di distribuzione di un Explainer con punteggio più chiaro è simile alla distribuzione di un modello e include i passaggi seguenti:




1. Creare un oggetto spiegazione, ad esempio usando TabularExplainer:

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Creare una descrizione del punteggio utilizzando l'oggetto spiegazione:

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Configurare e registrare un'immagine che usa il modello di spiegazione dei punteggi.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Opzionale Recupera la descrizione del punteggio dal cloud e testa le spiegazioni

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Distribuire l'immagine in una destinazione di calcolo:

   1. Creare un file di assegnazione dei punteggi (prima di questo passaggio, seguire la procedura descritta in [distribuire modelli con il servizio Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) per registrare il modello di stima originale)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import pandas as pd
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Definire la configurazione della distribuzione. questa configurazione dipende dai requisiti del modello. Nell'esempio seguente viene definita una configurazione che usa un core CPU e 1 GB di memoria)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Creare un file con dipendenze di ambiente

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-explain-model', 'azureml-core', 'azureml-telemetry', 'azureml-explain-model']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Creare un dockerfile personalizzato con g + + installato

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Distribuire l'immagine creata (stima tempo: 5 minuti)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. Test della distribuzione

    ```python
    import requests

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Pulisci: Per eliminare un servizio Web distribuito, usare `service.delete()`.




## <a name="next-steps"></a>Passaggi successivi

Per visualizzare una raccolta di notebook di Jupyter che illustrano le istruzioni riportate sopra, vedere il [Azure Machine Learning notebook di esempio di interpretazione](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
