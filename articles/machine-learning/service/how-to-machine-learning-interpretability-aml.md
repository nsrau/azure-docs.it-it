---
title: Interpretazione dei modelli per le esecuzioni locali e remote
titleSuffix: Azure Machine Learning
description: Informazioni su come spiegare il motivo per cui il modello esegue stime usando il Azure Machine Learning SDK. Può essere utilizzato durante il training e l'inferenza per comprendere in che modo il modello determina l'importanza della funzionalità ed esegue stime.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: a2b71a10606b7cd20f06b2497515b758426833a9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515304"
---
# <a name="model-interpretability-for-local-and-remote-runs"></a>Interpretazione dei modelli per le esecuzioni locali e remote

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo si apprenderà come spiegare il motivo per cui il modello ha eseguito le stime con il pacchetto di interpretazione del Azure Machine Learning Python SDK. Si apprenderanno le seguenti attività:

* Interpretare i modelli di Machine Learning sottoposti a training sia localmente che su risorse di calcolo Remote
* Archivia le spiegazioni locali e globali sulla cronologia di esecuzione di Azure
* Visualizzazione delle visualizzazioni di interpretazione in [Azure Machine Learning Studio](https://ml.azure.com)
* Distribuire una descrizione del punteggio con il modello

Per ulteriori informazioni sull'interpretazione dei modelli, vedere l' [articolo](how-to-machine-learning-interpretability.md)relativo al concetto.

## <a name="local-interpretability"></a>Interpretariato locale

L'esempio seguente illustra come usare il pacchetto di interpretazione localmente senza contattare i servizi di Azure. Eseguire `pip install azureml-interpret` per ottenere il pacchetto di interpretazione.

1. Eseguire il training di un modello di esempio in un notebook Jupyter locale.

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

2. Chiamare il Explainer in locale: per inizializzare un oggetto Explainer, è necessario passare il modello e alcuni dati di training al costruttore del Explainer. Facoltativamente, è anche possibile passare i nomi delle funzionalità e i nomi delle classi di output (se si esegue la classificazione), che verranno usati per rendere più informative le spiegazioni e le visualizzazioni. Di seguito viene illustrato come creare un'istanza di un oggetto Explainer usando `TabularExplainer`, `MimicExplainer`e `PFIExplainer` localmente. `TabularExplainer` chiama uno dei tre Explainer SHAP sotto (`TreeExplainer`, `DeepExplainer`o `KernelExplainer`) e seleziona automaticamente quello più appropriato per il caso d'uso. È tuttavia possibile chiamare direttamente ciascuno dei tre Explainer sottostanti.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    oppure

    ```python

    from interpret.ext.blackbox import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    
    from interpret.ext.glassbox import LGBMExplainableModel
    from interpret.ext.glassbox import LinearExplainableModel
    from interpret.ext.glassbox import SGDExplainableModel
    from interpret.ext.glassbox import DecisionTreeExplainableModel

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
    from interpret.ext.blackbox import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="overall-global-feature-importance-values"></a>Valori di importanza complessiva della funzionalità (globale)

Ottenere i valori di importanza della funzionalità globale.
    
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

### <a name="instance-level-local-feature-importance-values"></a>Valori di importanza della funzionalità a livello di istanza (locale)

Ottenere i valori di importanza della funzionalità locale: usare le chiamate di funzione seguenti per illustrare una singola istanza o un gruppo di istanze. Si noti che PFIExplainer non supporta le spiegazioni locali.

```python
# explain the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

## <a name="interpretability-for-remote-runs"></a>Interpretazione per le esecuzioni remote

Questo esempio illustra come usare la classe `ExplanationClient` per abilitare l'interpretazione del modello per le esecuzioni remote. Il concetto è simile a quello della sezione precedente, ma si usa il `ExplanationClient` nell'esecuzione remota per caricare il contesto di interpretazione, quindi è possibile scaricare il contesto in un secondo momento in un ambiente locale. Usare `pip install azureml-contrib-interpret` per ottenere il pacchetto necessario.

1. Creare uno script di training in un notebook Jupyter locale, ad esempio train_explain. py.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run
    from interpret.ext.blackbox import TabularExplainer

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

1. Seguire le istruzioni per configurare le [destinazioni di calcolo per il training del modello](how-to-set-up-training-targets.md#amlcompute) per informazioni su come configurare un Azure Machine Learning calcolo come destinazione di calcolo e inviare l'esecuzione del training. È anche possibile visualizzare i [notebook di esempio](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation).

1. Scaricare la spiegazione nel notebook di Jupyter locale.

    ```python
    from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient
    
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

## <a name="raw-feature-transformations"></a>Trasformazioni di funzionalità non elaborate

Facoltativamente, è possibile passare la pipeline di trasformazione delle funzionalità al Explainer (in train_explain. py) per ricevere spiegazioni in termini di funzionalità non elaborate prima della trasformazione, anziché delle funzionalità di progettazione. Se si ignora questo, il Explainer fornisce spiegazioni in termini di funzionalità progettate.

Il formato delle trasformazioni supportate è uguale a quello descritto in [sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). In generale, tutte le trasformazioni sono supportate finché operano su una singola colonna e sono quindi chiaramente da uno a molti. 

Illustrare le funzionalità non elaborate usando un `sklearn.compose.ColumnTransformer` o un elenco di tuple del trasformatore adattate. Il codice seguente usa `sklearn.compose.ColumnTransformer`. 


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


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Se si vuole eseguire l'esempio con l'elenco delle tuple di Transformer adattate, usare il codice seguente.

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

## <a name="visualizations"></a>Visualizzazioni

Una volta scaricate le spiegazioni nel notebook di Jupyter locale, è possibile usare il dashboard di visualizzazione per comprendere e interpretare il modello.

### <a name="global-visualizations"></a>Visualizzazioni globali

I tracciati seguenti forniscono una visualizzazione globale del modello sottoposto a training insieme alle relative stime e spiegazioni.

|Tracciato|Descrizione|
|----|-----------|
|Esplorazione dei dati| Panoramica del set di dati insieme ai valori di stima.|
|Importanza globale|Mostra le principali funzionalità importanti della K (configurabile K) a livello globale. Questo grafico è utile per comprendere il comportamento globale del modello sottostante.|
|Esplorazione spiegazione|Viene illustrato come una funzionalità è responsabile di apportare una modifica ai valori di stima del modello (o probabilità dei valori di stima). Viene inoltre illustrato il modo in cui due funzionalità interagiscono per influenzare le stime.|
|Importanza Riepilogo| Utilizza i valori di importanza della funzionalità locale con segno in tutti i punti dati per mostrare la distribuzione dell'effetto di ogni funzionalità sul valore di stima.|

[Dashboard di visualizzazione ![globale](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Visualizzazioni locali

Fare clic su un singolo punto dati in qualsiasi momento nei tracciati precedenti per caricare il tracciato di importanza della funzionalità locale per il punto dati specificato.

|Tracciato|Descrizione|
|----|-----------|
|Importanza locale|Mostra le principali funzionalità importanti della K (configurabile K) a livello globale. Questo grafico è utile per comprendere il comportamento locale del modello sottostante in un punto dati specifico.|
|Esplorazione della perturbazione|Consente di modificare i valori delle funzionalità del punto dati selezionato e di osservare il modo in cui tali modifiche influiranno sul valore di stima.|
|Singola previsione condizionale (ICE)| Consente di modificare un valore della funzionalità da un valore minimo a un valore massimo per vedere come viene modificata la stima del punto dati quando una funzionalità viene modificata.|

[Importanza della funzionalità locale del dashboard di visualizzazione ![](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![perturbazione delle funzionalità del dashboard di visualizzazione](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[Tracciati di ghiaccio del dashboard di visualizzazione ![](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Si noti che è necessario che le estensioni widget del dashboard di visualizzazione siano abilitate prima dell'avvio del kernel Jupyter.

* Jupyter Notebook

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Per caricare il dashboard di visualizzazione, usare il codice seguente.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualizzazione in Azure Machine Learning Studio

Completando la procedura descritta nella sezione relativa all' [interpretazione remota](how-to-machine-learning-interpretability-aml.md#interpretability-for-remote-runs) , è possibile controllare il dashboard di visualizzazione in [Azure Machine Learning Studio](https://ml.azure.com). Il dashboard illustrato in Azure Machine Learning Studio è una versione più semplice del dashboard di visualizzazione descritta sopra e supporta solo le due schede seguenti.

|Tracciato|Descrizione|
|----|-----------|
|Importanza globale|Mostra le principali funzionalità importanti della K (configurabile K) a livello globale. Questo grafico è utile per comprendere il comportamento globale del modello sottostante.|
|Importanza Riepilogo| Utilizza i valori di importanza della funzionalità locale con segno in tutti i punti dati per mostrare la distribuzione dell'effetto di ogni funzionalità sul valore di stima.|

Se sono disponibili sia la spiegazione globale che quella locale, entrambe le schede verranno popolate con i dati. Se è disponibile solo una spiegazione globale, la seconda scheda verrà disabilitata.

Per accedere al dashboard di visualizzazione in Azure Machine Learning Studio, è possibile usare uno dei percorsi seguenti:

1. Scheda esperimenti (anteprima): facendo clic sulla scheda "esperimenti", viene visualizzato un elenco di esperimenti eseguiti sul servizio Azure Machine Learning. Da tale elenco è possibile selezionare un determinato esperimento da reindirizzare a una pagina con tutte le esecuzioni nel nome dell'esperimento selezionato. Facendo clic su ogni esecuzione e sulla scheda "spiegazione", viene visualizzato il dashboard di visualizzazione spiegazione.


[Importanza della funzionalità locale del dashboard di visualizzazione ![](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png)](./media/machine-learning-interpretability-explainability/amlstudio-experiments.png#lightbox)


2. Scheda modelli: nel caso in cui sia stato registrato il modello originale usando la procedura descritta in [distribuire modelli con Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where), il modello viene visualizzato nell'elenco della scheda "modelli". Facendo clic su ogni modello e sulla scheda "spiegazione", viene visualizzato il dashboard di visualizzazione spiegazione.

## <a name="interpretability-at-inference-time"></a>Interpretazione in fase di inferenza

Il Explainer può essere distribuito insieme al modello originale e può essere utilizzato in fase di inferenza per fornire le informazioni sulla spiegazione locale. Sono inoltre disponibili spiegatori per il punteggio più leggeri per migliorare le prestazioni di interpretazione in fase di inferenza. Il processo di distribuzione di un Explainer con punteggio più chiaro è simile alla distribuzione di un modello e include i passaggi seguenti:


1. Creare un oggetto spiegazione, ad esempio usando TabularExplainer:

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Creare una descrizione del punteggio utilizzando l'oggetto spiegazione:

   ```python
   from azureml.contrib.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

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
   from azureml.contrib.interpret.scoring.scoring_explainer import load

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

   1. Creare un file di assegnazione dei punteggi (prima di questo passaggio, seguire la procedura descritta in [distribuire modelli con Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) per registrare il modello di stima originale)

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

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-interpret', 'azureml-core', 'azureml-telemetry', 'azureml-interpret']
 

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

   1. Distribuisci l'immagine creata (stima tempo: 5 minuti)

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

1. Pulizia: per eliminare un servizio Web distribuito, utilizzare `service.delete()`.

## <a name="next-steps"></a>Passaggi successivi

Per ulteriori informazioni sull'interpretazione dei modelli, vedere l' [articolo concettuale](how-to-machine-learning-interpretability.md).
