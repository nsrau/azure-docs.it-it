---
title: Interpretabilità dei modelli
titleSuffix: Azure Machine Learning service
description: Informazioni su come spiegare il motivo per cui il modello esegue stime usando il SDK di Azure Machine Learning. Può essere utilizzato durante il training e inferenza per comprendere come il modello esegue le stime.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/29/2019
ms.openlocfilehash: 4261e869fe17283886d7d8ea8101e03110d6dad4
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2019
ms.locfileid: "65851985"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Interpretazione di modello con il servizio di Azure Machine Learning

In questo articolo si apprenderà come spiegare il motivo per cui il modello creato le stime quanto avveniva con il pacchetto di interpretazione di Azure Machine Learning Python SDK.

Uso dei metodi e classi in questo pacchetto, è possibile ottenere:
+ Interpretazione nel mondo reale i set di dati su larga scala, durante il training e inferenza. 
+ Visualizzazioni interattive per facilitano l'individuazione di modelli nei dati e le spiegazioni in fase di training
+ I valori di priorità delle funzionalità: funzionalità di gestione delle eccezioni non elaborate e ingegneria

Durante la fase di training del ciclo di sviluppo, gli analizzatori e i progettisti di modelli possono utilizzare per spiegare l'output di un modello agli stakeholder per fiducia va costruita.  Sono inoltre utilizzare le informazioni dettagliate nel modello per il debug, convalida il comportamento del modello una corrispondenza per i loro obiettivi e per verificare la presenza di distorsione.

Inferenza o modello di punteggio, è la fase in cui viene usato il modello distribuito per la stima, in genere sui dati di produzione. Durante questa fase, i data Scientist possono spiegare le stime per le persone che usano il modello risultante. Ad esempio, il motivo per cui è stato il modello di negare un mutuo o stimare che un portfolio di investimento comporta un rischio più elevato?

Utilizzando queste offerta, si possono spiegare i modelli di machine learning **a livello globale in tutti i dati**, o **localmente su un punto dati specifico** usando le tecnologie di avanzato in modo scalabile e facile da usare.

Le classi di interpretazione vengono resi disponibili tramite due pacchetti di Python. Informazioni su come [installare i pacchetti SDK per Azure Machine Learning](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), il pacchetto principale, che contiene le funzionalità supportate da Microsoft. 

* `azureml.contrib.explain.model`, anteprima e le funzionalità sperimentali che è possibile provare.

> [!IMPORTANT]
> Le cose in contrib non sono completamente supportate. Man mano che diventano le funzionalità sperimentali mature, verranno spostati gradualmente per il pacchetto principale.

## <a name="how-to-interpret-your-model"></a>Come interpretare il modello

È possibile applicare le classi di interpretazione e metodi per comprendere il comportamento globali del modello o stime specifiche. Il primo è denominato spiegazione globale e quest'ultimo viene chiamato spiegazione locale.

I metodi possono essere suddivise anche a seconda che il metodo sia indipendente dal modello o un modello specifico. Alcuni metodi come destinazione un determinato tipo di modelli. Ad esempio, spiegazione di struttura ad albero di & proprietà si applica solo ai modelli basati su albero. Alcuni metodi di considerano il modello come una casella nera, ad esempio che riproducono spiegazione o spiegazione del & proprietà kernel. Il `explain` pacchetto sfrutta questi diversi approcci basati su set di dati, tipi di modello e casi d'uso. 

L'output è un set di informazioni su come un modello specificato rende la stima, ad esempio:
* Importanza delle caratteristiche relative globale o locale

* Relazione tra funzionalità e stima di globale o locale

### <a name="explainers"></a>Explainers

Esistono due set di explainers: Explainers diretto ed Explainers Meta nel SDK.

__Indirizzare explainers__ provengono dalle librerie integrate. il SDK esegue il wrapping di tutte le explainers in modo da esporre un'API comune e un formato di output. Se si è più a proprio agio usando direttamente questi explainers, è possibile richiamare direttamente li invece di usare le API comuni e il formato di output. Di seguito sono un elenco del explainers diretta disponibili nel SDK:

* **Albero spiegazione**: Spiegazione di struttura ad albero di & proprietà, che è incentrato sul tempo polinomiale rapido & proprietà valore stima specifico dell'algoritmo alberi e insiemi di alberi delle.
* **Spiegazione approfondita**: Base la spiegazione di & proprietà, spiegazione approfondita "è un algoritmo di approssimazione ad alta velocità per i valori di & proprietà nei modelli di apprendimento profondo si basa su una connessione con DeepLIFT descritto nel documento NIPS & proprietà. Sono supportati i modelli di TensorFlow e modelli Keras con TensorFlow back-end (è inoltre disponibile supporto preliminare per PyTorch) ".
* **Spiegazione: informazioni su kernel**: Spiegazione: informazioni su Kernel di & proprietà Usa una regressione lineare locale ponderata in modo speciale per stimare i valori di & proprietà per qualsiasi modello.
* **Simulare spiegazione**: Spiegazione che riproducono si basa sul concetto di modelli globali surrogato. Un modello di surrogato globale è un modello può essere interpretato in modo intrinseco che viene eseguito il training per approssimare il modo più accurato possibile le stime di un modello di casella nera. Esperto di dati può interpretare il modello di surrogato per trarre conclusioni sul modello di casella nera.
* **Spiegazione: informazioni su verde LIMONE** (`contrib`): Basato su verde LIMONE, spiegazione LIME utilizza l'algoritmo d'avanguardia locale può essere interpretato indipendente dal modello spiegazioni (verde) per creare modelli di surrogato locale. A differenza dei modelli di surrogato globale, verde LIMONE concentra training dei modelli di surrogato locale per spiegare le stime singole.
* **Spiegazione: informazioni su testo HAN** (`contrib`): Spiegazione: informazioni su testo HAN Usa un Hierarchical Attention Network per ottenere una spiegazione del modello dai dati di testo per un modello di testo determinato casella nera. È il training del modello di surrogati di HAN negli output stimato del modello specifico docente. Dopo il training a livello globale nel corpo di testo, è stato aggiunto un passaggio di ottimizzazione per un documento specifico per migliorare l'accuratezza delle descrizioni. HAN Usa un bidirezionale RNN con due livelli di attenzione, attenzione parole e frasi. Una volta che la rete neurale profonda viene eseguito il training del modello per docenti e ottimizzare in un documento specifico, è possibile estrarre importances la parola dei livelli di attenzione. Sono state trovate HAN sia più accurati rispetto a verde LIMONE o & proprietà per i dati di testo, ma più costose in termini di nonché tempi di training. Tuttavia, sono stati apportati miglioramenti per la durata del training, assegnando all'utente la possibilità di inizializzare la rete con rappresentazioni distribuite delle parole GloVe, anche se è ancora lento. Il tempo di training può essere migliorato in modo significativo tramite l'esecuzione HAN in una VM GPU di Azure remota. L'implementazione di HAN è descritta in 'Reti di attenzione gerarchici per la classificazione di documento (Yang e così via, 2016)' ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

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

Tutti i modelli che vengono sottoposti a training sul set di dati in Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, o `scipy.sparse.csr_matrix` formato sono supportati per l'interpretazione di `explain` pacchetto del SDK.

Le funzioni di spiegazione accettano entrambi i modelli e le pipeline come input. Se viene fornito un modello, il modello deve implementare la funzione di stima `predict` o `predict_proba` conforme alla convenzione di Scikit. Se viene fornita una pipeline (nome dello script di pipeline), la funzione di spiegazione presuppone che l'esecuzione della pipeline di uno script restituisce una stima.

### <a name="local-and-remote-compute-target"></a>Destinazione di calcolo locali e remoti

Il `explain` pacchetto è progettato per funzionare con entrambe le destinazioni di calcolo locali e remoti. Se eseguito in locale, le funzioni del SDK non contatterà tutti i servizi Azure. È possibile eseguire in remoto una spiegazione nel calcolo di Azure Machine Learning e registrare le informazioni nella spiegazione in servizi di Azure Machine Learning eseguire cronologia. Una volta che tale informazione viene registrata, report e visualizzazioni dalla spiegazione sono immediatamente disponibili nel portale dell'area di lavoro di Azure Machine Learning per analisi degli utenti.

## <a name="interpretability-in-training"></a>Interpretazione di training

### <a name="train-and-explain-locally"></a>Eseguire il training e spiegare in locale

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

2. Chiamare la spiegazione: Per inizializzare un oggetto di spiegazione, è necessario passare al costruttore della spiegazione del modello e i dati di training. È possibile passare facoltativamente anche i nomi delle funzionalità e i nomi delle classi output (se questa classificazione) che verranno usati per rendere più informativi spiegazioni e visualizzazioni. Di seguito viene illustrato come creare un'istanza di un oggetto di spiegazione tramite [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py) e [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py) in locale. `TabularExplainer` viene chiamato uno dei tre explainers sotto (`TreeExplainer`, `DeepExplainer`, o `KernelExplainer`) e viene automaticamente selezionando quello più appropriato per il caso d'uso. È tuttavia possibile chiamare direttamente ognuno dei relativi tre explainers sottostante.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    o
    
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel

    # "features" and "classes" fields are optional
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Ottenere la funzionalità globale i valori di priorità.

    ```python
    # You can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. I valori di priorità delle funzionalità locali: usare chiamate di funzione seguenti per descrivere una singola istanza o un gruppo di istanze.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    o
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Eseguire il training e spiegare in modalità remota

Anche se è possibile eseguire il training sulle varie destinazioni di calcolo supportate dal servizio di Azure Machine Learning, l'esempio in questa sezione viene illustrato come eseguire questa operazione utilizzando una destinazione di calcolo di Azure Machine Learning.

1. Creare uno script di training in un notebook Jupyter locale (ad esempio, run_explainer.py).

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine   
    # "features" and "classes" fields are optional 
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0])
    # upload global and local explanation objects to Run History
    client.upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Seguire le istruzioni sullo [configurare le destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md#amlcompute) per informazioni su come configurare un Azure calcolo di Machine Learning come destinazione di calcolo e invia l'esecuzione di training.

3. Scaricare la spiegazione in locale Jupyter notebook. 


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

## <a name="visualizations"></a>Visualizzazioni

Usare il dashboard di visualizzazione per comprendere e interpretare il modello:

### <a name="global-visualizations"></a>Visualizzazioni globale

I tracciati seguenti forniscono una visualizzazione globale del modello con Training con le stime e spiegazioni.

|Tracciato|Descrizione|
|----|-----------|
|Esplorazione dei dati| Panoramica del set di dati con valori di stima.|
|Importanza globale|Mostra le prime funzioni importanti K (configurabile K) a livello globale. Questo grafico è utile per comprendere il comportamento globale del modello sottostante.|
|Esplorazione di spiegazione|Viene illustrato come una funzionalità è responsabile di garantire una modifica in valori di stima del modello (o probabilità di valori di stima). |
|Riepilogo| Usa un valori di importanza firmato funzionalità locali in tutti i punti dati per mostrare la distribuzione dell'impatto che ogni funzionalità presenta il valore di stima.|

[![Visualization Dashboard Global](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Visualizzazioni locali
È possibile fare clic su qualsiasi singolo punto dati in qualsiasi momento il tracciato precedente per caricare il tracciato di importanza funzionalità locali per il punto dati specificato.

|Tracciato|Descrizione|
|----|-----------|
|Importanza locale|Mostra le prime funzioni importanti K (configurabile K) a livello globale. Questo grafico è utile per comprendere il comportamento locale del modello sottostante in un punto dati specifico.|

[![Visualizzazione del Dashboard locale](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)

Per caricare il dashboard di visualizzazione, usare il codice seguente:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
``` 

## <a name="raw-feature-transformations"></a>Trasformazioni di funzioni non elaborate

Facoltativamente, è possibile passare la pipeline di trasformazione di funzionalità per la spiegazione di ricevere le spiegazioni in termini di funzioni non elaborate prima di trasformazione (anziché funzioni progettate). Se si ignora questo, la spiegazione fornisce spiegazioni in termini di funzioni progettate. 

Il formato delle trasformazioni supportate è uguale a quello descritto nel [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). In generale, tutte le trasformazioni sono supportate fino a quando vengono applicate a una singola colonna e sono pertanto chiaramente uno a molti.

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# Assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# Append model to preprocessing pipeline.
# Now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                    ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# Pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1], initialization_examples=x_train, features=dataset_feature_names, classes=dataset_classes, transformations=transformations)
```

## <a name="interpretability-in-inference"></a>Interpretazione di inferenza

La spiegazione può essere distribuiti insieme al modello originale e utilizzabile in fase di assegnazione dei punteggi per fornire le informazioni di spiegazione locale. Il processo di distribuzione di una spiegazione di assegnazione dei punteggi è simile alla distribuzione di un modello e i passaggi seguenti:

1. Creare un oggetto di spiegazione:
   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, x_test)
   ``` 

1. Creare una spiegazione di assegnazione dei punteggi usando l'oggetto spiegazione:
   ```python
   scoring_explainer = explainer.create_scoring_explainer(x_test)

   # Pickle scoring explainer
   scoring_explainer_path = scoring_explainer.save('scoring_explainer_deploy')
   ``` 

1. Configurare e registrare un'immagine che utilizza il modello di spiegazione di assegnazione dei punteggi.
   ```python
   # Register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   run.upload_file('breast_cancer_scoring_explainer.pkl', scoring_explainer_path)
   model = run.register_model(model_name='breast_cancer_scoring_explainer', model_path='breast_cancer_scoring_explainer.pkl')
   print(model.name, model.id, model.version, sep = '\t')
   ``` 

1. [Facoltativo] Recuperare la spiegazione di assegnazione dei punteggi dal cloud e le spiegazioni di test
   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import ScoringExplainer

   # Retreive the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'breast_cancer_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # Load scoring explainer from disk
   scoring_explainer = ScoringExplainer.load(scoring_explainer_model_path)

   # Test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Distribuire l'immagine in una destinazione di calcolo:

   1. Creare un file di assegnazione dei punteggi (prima di questo passaggio, seguire i passaggi descritti in [distribuire modelli con il servizio di Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) per registrare il modello di stima originale)
        ```python
        %%writefile score.py
        import json
        import numpy as np
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # Retrieve the path to the model file using the model name
            # Assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('breast_cancer_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # Get predictions and explanations for each data point
            data = np.array(json.loads(raw_data)['data'])
            # Make prediction
            predictions = original_model.predict(data)
            # Retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # You can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ``` 
    1. Definire la configurazione della distribuzione (questa configurazione dipende dai requisiti del modello. L'esempio seguente definisce una configurazione che usa un core CPU e 1 GB di memoria)
        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                                       memory_gb=1, 
                                                       tags={"data": "breastcancer",  
                                                             "method" : "local_explanation"}, 
                                                       description='Get local explanations for breast cancer data')
        ``` 

    1. Creare un file con dipendenze di ambiente

        ```python
        from azureml.core.conda_dependencies import CondaDependencies 

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)


        myenv = CondaDependencies.create(pip_packages=["azureml-defaults", "azureml-explain-model", "azureml-contrib-explain-model"], 
                                        conda_packages=["scikit-learn"])

        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())
            
        with open("myenv.yml","r") as f:
            print(f.read())
        ``` 
    1. Creare un dockerfile personalizzato con installato g + +

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++  
        ``` 
    1. Distribuire l'immagine creata (tempo stimato: 5 minuti)
        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # Use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile", 
                                                        runtime="python", 
                                                        conda_file="myenv.yml")

        # Use configs and models generated above
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

    # Create data to test service with
    x_list = x_test.tolist()
    examples = x_list[:4]
    input_data = "{\"data\": " + str(examples) + "}"

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ``` 

1. Pulizia: Per eliminare un servizio Web distribuito, usare `service.delete()`.

## <a name="next-steps"></a>Passaggi successivi

Per una raccolta di notebook di Jupyter che illustrano le istruzioni riportate sopra, vedere la [notebook di esempio Azure Machine Learning interpretazione](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
