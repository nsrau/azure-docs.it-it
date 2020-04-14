---
title: Spiegare i modelli e le previsioni di apprendimento automaticoExplain machine learning models and predictions
titleSuffix: Azure Machine Learning
description: Informazioni su come ottenere spiegazioni su come il modello di apprendimento automatico determina l'importanza delle funzionalità ed esegue stime quando si usa Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: Luis.Quintanilla
ms.date: 04/12/2020
ms.openlocfilehash: c1282ed16c9e3b92e7d5ec3f9969bee6fc3d917f
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257202"
---
# <a name="explain-machine-learning-models-and-predictions"></a>Spiegare i modelli e le previsioni di apprendimento automaticoExplain machine learning models and predictions

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questa guida alle procedure si apprenderà a usare il pacchetto di interpretabilità di Azure Machine Learning Python SDK per eseguire le attività seguenti:In this how-to guide, you learn to use the interpretability package of the Azure Machine Learning Python SDK to perform the following tasks:


* Spiegare localmente l'intero comportamento del modello o le stime individuali nel computer personale.

* Abilitare le tecniche di interpretabilità per le funzionalità progettate.

* Spiegare il comportamento per l'intero modello e le stime individuali in Azure.Explain the behavior for the entire model and individual predictions in Azure.

* Utilizzare un dashboard di visualizzazione per interagire con le spiegazioni del modello.

* Distribuire uno che esplica insieme al modello per osservare le spiegazioni durante l'inferenza.



Per altre informazioni sulle tecniche di interpretabilità supportate e sui modelli di apprendimento automatico, vedere [Model interpretability in Azure Machine Learning](how-to-machine-learning-interpretability.md) e notebook di [esempio.](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

## <a name="generate-feature-importance-value-on-your-personal-machine"></a>Genera il valore dell'importanza delle funzionalità sul tuo computer personale 
L'esempio seguente mostra come usare il pacchetto di interpretabilità nel computer personale senza contattare i servizi di Azure.The following example shows how to use the interpretability package on your personal machine without contacting Azure services.

1. Installazione `azureml-interpret` `azureml-interpret-contrib` e pacchetti.
    ```bash
    pip install azureml-interpret
    pip install azureml-interpret-contrib
    ```

2. Eseguire il training di un modello di esempio in un blocco appunti Jupyter locale.

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

3. Chiamare l'esplicatore in locale.
   * Per inizializzare un oggetto esplicativo, passare il modello e alcuni dati di training al costruttore dello esplicativo.
   * Per rendere le spiegazioni e le visualizzazioni più informative, è possibile scegliere di passare i nomi delle funzionalità e i nomi delle classi di output se si esegue la classificazione.

   Nei blocchi di codice seguenti viene illustrato `TabularExplainer` `MimicExplainer`come `PFIExplainer` creare un'istanza di un oggetto explainer con , e localmente.
   * `TabularExplainer`chiama uno dei tre esplicatori SHAP sotto (`TreeExplainer`, `DeepExplainer`, o `KernelExplainer`).
   * `TabularExplainer`seleziona automaticamente quello più appropriato per il tuo caso d'uso, ma puoi chiamare direttamente ciascuno dei suoi tre esplicatori sottostanti.

    ```python
    from interpret.ext.blackbox import TabularExplainer

    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    o

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

    o

    ```python
    from interpret.ext.blackbox import PFIExplainer

    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model,
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

### <a name="explain-the-entire-model-behavior-global-explanation"></a>Spiegare l'intero comportamento del modello (spiegazione globale) 

Fare riferimento all'esempio seguente per ottenere i valori di importanza della funzionalità di aggregazione (globale).

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

### <a name="explain-an-individual-prediction-local-explanation"></a>Spiegare una previsione individuale (spiegazione locale)
Ottenere i valori di importanza delle singole funzionalità di punti dati diversi chiamando spiegazioni per una singola istanza o un gruppo di istanze.
> [!NOTE]
> `PFIExplainer`non supporta le spiegazioni locali.

```python
# get explanation for the first data point in the test set
local_explanation = explainer.explain_local(x_test[0:5])

# sorted feature importance values and feature names
sorted_local_importance_names = local_explanation.get_ranked_local_names()
sorted_local_importance_values = local_explanation.get_ranked_local_values()
```

### <a name="raw-feature-transformations"></a>Trasformazioni di entità geografiche non elaborate

È possibile scegliere di ottenere spiegazioni in termini di funzionalità non elaborate e non trasformate anziché in termini di funzionalità ingegnerizzate. Per questa opzione, si passa la pipeline `train_explain.py`di trasformazione della funzionalità allo explainer in . In caso contrario, lo esplicatore fornisce spiegazioni in termini di funzionalità ingegnerizzate.

Il formato delle trasformazioni supportate è lo stesso descritto in [sklearn-pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). In generale, tutte le trasformazioni sono supportate purché operino su una singola colonna in modo che sia chiaro che sono uno-a-molti.

Ottenere una spiegazione per `sklearn.compose.ColumnTransformer` le funzionalità non elaborate utilizzando un o con un elenco di tuple trasformatore montato. Nell'esempio `sklearn.compose.ColumnTransformer`riportato di seguito viene utilizzato .

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

Nel caso in cui si desideri eseguire l'esempio con l'elenco delle tuple dei trasformatori adattate, utilizzare il codice seguente:

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

## <a name="generate-feature-importance-values-via-remote-runs"></a>Generare i valori di importanza delle funzionalità tramite esecuzioni remote

Nell'esempio seguente viene illustrato `ExplanationClient` come utilizzare la classe per abilitare l'interpretazione del modello per le esecuzioni remote. È concettualmente simile al processo locale, ad eccezione dell'utente:

* Utilizzare `ExplanationClient` l'esecuzione remota nell'esecuzione remota per caricare il contesto di interpretabilità.
* Scaricare il contesto in un secondo momento in un ambiente locale.

1. Installazione `azureml-interpret` `azureml-interpret-contrib` e pacchetti.
    ```bash
    pip install azureml-interpret
    pip install azureml-interpret-contrib
    ```
1. Creare uno script di training in un'istanza di Jupyter Notebook locale. Ad esempio: `train_explain.py`.

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

1. Configurare un calcolo di Azure Machine Learning come destinazione di calcolo e inviare l'esecuzione del training. Per istruzioni, vedere [Impostazione di destinazioni](how-to-set-up-training-targets.md#amlcompute) di calcolo per il training del modello. È anche possibile che [i blocchi appunti](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model/azure-integration/remote-explanation) di esempio sia utile.

1. Scaricare la spiegazione nel blocco appunti Jupyter locale.

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


## <a name="visualizations"></a>Visualizzazioni

Dopo aver scaricato le spiegazioni nel blocco appunti Jupyter locale, è possibile utilizzare il dashboard di visualizzazione per comprendere e interpretare il modello.

### <a name="understand-entire-model-behavior-global-explanation"></a>Comprendere l'intero comportamento del modello (spiegazione globale)Understand entire model behavior (global explanation) 

I grafici seguenti forniscono una visualizzazione generale del modello sottoposto a training insieme alle relative stime e spiegazioni.

|Trama|Descrizione|
|----|-----------|
|Esplorazione dei dati| Visualizza una panoramica del set di dati insieme ai valori di stima.|
|Importanza globale|Aggrega i valori di importanza delle caratteristiche dei singoli punti dati per mostrare le caratteristiche importanti principali K (configurabili) del modello. Aiuta a comprendere il comportamento generale del modello sottostante.|
|Spiegazione Esplorazione|Viene illustrato come una funzionalità influisce su una modifica nei valori di stima del modello o sulla probabilità di valori di stima. Mostra l'impatto dell'interazione tra le funzionalità.|
|Importanza sommaria|Utilizza i singoli valori di importanza delle entità geografiche in tutti i punti dati per mostrare la distribuzione dell'impatto di ogni feature sul valore di stima. Utilizzando questo diagramma, si esamina la direzione in cui i valori della funzionalità influiscono sui valori di stima.
|

[![Dashboard di visualizzazione globale](./media/how-to-machine-learning-interpretability-aml/global-charts.png)](./media/how-to-machine-learning-interpretability-aml/global-charts.png#lightbox)

### <a name="understand-individual-predictions-local-explanation"></a>Comprendere le previsioni individuali (spiegazione locale) 

È possibile caricare il grafico di importanza della singola feature per qualsiasi punto dati facendo clic su uno dei singoli punti dati in uno qualsiasi dei grafici complessivi.

|Trama|Descrizione|
|----|-----------|
|Importanza locale|Mostra le caratteristiche importanti principali di K (configurabile K) per una singola stima. Consente di illustrare il comportamento locale del modello sottostante in un punto dati specifico.|
|Esplorazione perturbazione (e cosa succede rebbe all'analisi)|Consente di modificare i valori delle entità geografiche del punto dati selezionato e di osservare le modifiche risultanti al valore di stima.|
|Aspettativa condizionale individuale (ICE)| Consente di modificare il valore della funzione da un valore minimo a un valore massimo. Consente di illustrare come cambia la stima del punto dati quando viene modificata una funzionalità.|

[![Importanza delle funzionalità locali del dashboard di visualizzazione](./media/how-to-machine-learning-interpretability-aml/local-charts.png)](./media/how-to-machine-learning-interpretability-aml/local-charts.png#lightbox)


[![Visualizzazione Dashboard Feature Perturbazione](./media/how-to-machine-learning-interpretability-aml/perturbation.gif)](./media/how-to-machine-learning-interpretability-aml/perturbation.gif#lightbox)


[![Dashboard di visualizzazione Grafici ICE](./media/how-to-machine-learning-interpretability-aml/ice-plot.png)](./media/how-to-machine-learning-interpretability-aml/ice-plot.png#lightbox)

> [!NOTE]
> Prima dell'avvio del kernel Jupyter, assicurarsi di abilitare le estensioni widget per il dashboard di visualizzazione.

* Jupyter Notebook

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.interpret.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.interpret.visualize
    ```

* JupyterLab

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```

Per caricare il dashboard di visualizzazione, utilizzare il codice seguente.

```python
from interpret_community.widget import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

### <a name="visualization-in-azure-machine-learning-studio"></a>Visualizzazione in Azure Machine Learning Studio

Se si completano i passaggi di [interpretabilità remota](how-to-machine-learning-interpretability-aml.md#generate-feature-importance-values-via-remote-runs) (caricamento della spiegazione generata nella cronologia di esecuzione di Azure Machine Learning), è possibile visualizzare il dashboard di visualizzazione in [Azure Machine Learning Studio.](https://ml.azure.com) Questo dashboard è una versione più semplice del dashboard di visualizzazione illustrato in precedenza (l'esplorazione della spiegazione e i grafici ICE sono disabilitati in quanto non esiste un calcolo attivo in studio in grado di eseguire i calcoli in tempo reale).

Se sono disponibili le spiegazioni del set di dati, globali e locali, i dati popolano tutte le schede (ad eccezione di Esplorazione perturbazione e ICE). Se è disponibile solo una spiegazione globale, la scheda Importanza di riepilogo e tutte le schede della spiegazione locale sono disabilitate.

Seguire uno di questi percorsi per accedere al dashboard di visualizzazione in Azure Machine Learning Studio:Follow one of these paths to access the visualization dashboard in Azure Machine Learning studio:

* **Riquadro Esperimenti (anteprima)Experiments** pane (Preview)
  1. Selezionare Esperimenti nel riquadro sinistro per visualizzare un elenco di esperimenti eseguiti in Azure Machine Learning.Select **Experiments** in the left pane to see a list of experiments that you've run on Azure Machine Learning.
  1. Selezionare un esperimento specifico per visualizzare tutte le esecuzioni dell'esperimento.
  1. Selezionare un'esecuzione, quindi la scheda **Spiegazioni** nel dashboard di visualizzazione della spiegazione.

   [![Importanza delle funzionalità locali del dashboard di visualizzazione](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png)](./media/how-to-machine-learning-interpretability-aml/amlstudio-experiments.png#lightbox)

* **Riquadro Modelli**
  1. Se è stato registrato il modello originale seguendo la procedura descritta in [Distribuire modelli con Azure Machine Learning,](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)è possibile selezionare **Modelli** nel riquadro sinistro per visualizzarlo.
  1. Selezionare un modello, quindi la scheda **Spiegazioni** per visualizzare il dashboard di visualizzazione della spiegazione.

## <a name="interpretability-at-inference-time"></a>Interpretabilità al momento dell'inferenza

È possibile distribuire l'esplicatore insieme al modello originale e utilizzarlo in fase di inferenza per fornire i singoli valori di importanza delle funzionalità (spiegazione locale) per qualsiasi nuovo punto dati. Offriamo anche espedienti di punteggio più leggeri per migliorare le prestazioni di interpretabilità al momento dell'inferenza. Il processo di distribuzione di un'elaborazione del punteggio più leggero è simile alla distribuzione di un modello e include i passaggi seguenti:The process of deploying a lighter-weight scoring explainer is similar to deploying a model and includes the following steps:

1. Creare un oggetto di spiegazione. Ad esempio, è `TabularExplainer`possibile utilizzare :

   ```python
    from interpret.ext.blackbox import TabularExplainer


   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Creare un'esplicazione del punteggio con l'oggetto spiegazione.

   ```python
   from azureml.interpret.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Configurare e registrare un'immagine che utilizza il modello di spiegazione del punteggio.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Come passaggio facoltativo, è possibile recuperare l'esplicatore del punteggio dal cloud e testare le spiegazioni.

   ```python
   from azureml.interpret.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Distribuire l'immagine in una destinazione di calcolo attenendosi alla procedura seguente:Deploy the image to a compute target, by following these steps:

   1. Se necessario, registrare il modello di stima originale seguendo i passaggi descritti in [Distribuire modelli con Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/how-to-deploy-and-where)

   1. Creare un file di punteggio.

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
   1. Definire la configurazione della distribuzione.

         Questa configurazione dipende dai requisiti del modello. Nell'esempio seguente viene definita una configurazione che utilizza un core della CPU e un GB di memoria.

         ```python
         from azureml.core.webservice import AciWebservice

          aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                    memory_gb=1,
                                                    tags={"data": "NAME_OF_THE_DATASET",
                                                          "method" : "local_explanation"},
                                                    description='Get local explanations for NAME_OF_THE_PROBLEM')
         ```

   1. Creare un file con dipendenze di ambiente.

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

   1. Creare un dockerfile personalizzato con il file di file di file di installazione.

         ```python
         %%writefile dockerfile
         RUN apt-get update && apt-get install -y g++
         ```

   1. Distribuire l'immagine creata.
   
         Questo processo richiede circa cinque minuti.

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

1. Testare la distribuzione.

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

1. Eseguire la pulizia.

   Per eliminare un servizio Web distribuito, usare `service.delete()`.

## <a name="next-steps"></a>Passaggi successivi

[Ulteriori informazioni sull'interpretabilità dei modelli](how-to-machine-learning-interpretability.md)

[Estrarre i blocchi appunti di esempio per l'interpretazione di Azure Machine LearningCheck Check out Azure Machine Learning Interpretability sample notebooks](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model)

