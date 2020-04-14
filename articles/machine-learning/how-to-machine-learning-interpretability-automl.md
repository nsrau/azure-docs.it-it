---
title: Esplicabilità nell'apprendimento automatico automatizzato
titleSuffix: Azure Machine Learning
description: Informazioni su come ottenere spiegazioni su come il modello di Machine L automatizzato determina l'importanza delle funzionalità ed esegue stime quando si usa Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.date: 03/11/2020
ms.openlocfilehash: ecf7cde1a7f804ef1c43b21ac5c1d2a488660590
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/13/2020
ms.locfileid: "81257165"
---
# <a name="interpretability-model-explanations-in-automated-machine-learning"></a>Interpretazionità: spiegazioni dei modelli nell'apprendimento automatico

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In this article, you learn how to get explanations for automated machine learning (ML) in Azure Machine Learning. L'mL automatizzato consente di comprendere l'importanza delle funzionalità ingegnerizzate. 

Tutte le versioni SDK successive alla `model_explainability=True` 1.0.85 sono impostate per impostazione predefinita. Nella versione SDK 1.0.85 e versioni `model_explainability=True` precedenti `AutoMLConfig` gli utenti devono impostare nell'oggetto per utilizzare l'interpretazione del modello. 

In questo articolo vengono illustrate le operazioni seguenti:

- Eseguire l'interpretazione durante il training per il miglior modello o qualsiasi modello.
- Abilitare le visualizzazioni per facilitare la visualizzazione dei modelli nei dati e nelle spiegazioni.
- Implementare l'interpretabilità durante l'inferenza o il punteggio.

## <a name="prerequisites"></a>Prerequisiti

- Caratteristiche di interpretabilità. Eseguire `pip install azureml-interpret azureml-contrib-interpret` per ottenere i pacchetti necessari.
- Conoscenza della creazione di esperimenti automatizzati di ML. Per altre informazioni su come usare Azure Machine Learning SDK, completare questa [esercitazione](tutorial-auto-train-models.md) sul modello di regressione o vedere come [configurare esperimenti automatizzati](how-to-configure-auto-train.md)su Machine L.

## <a name="interpretability-during-training-for-the-best-model"></a>Interpretazionità durante il training per il modello migliore

Recuperare la spiegazione da `best_run`, che include spiegazioni per le funzionalità ingegnerizzate.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Scarica l'importanza delle funzionalità ingegnerizzate dall'archivio artefatti

È possibile `ExplanationClient` utilizzare per scaricare le spiegazioni delle funzionalità `best_run`ingegnerizzate dall'archivio artefatti del file . 

```python
from azureml.explain.model._internal.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretazionità durante il training per qualsiasi modello 

Quando si calcolano le spiegazioni del modello e le si visualizza, non si è limitati a una spiegazione del modello esistente per un modello di processo di gestione automatica. È inoltre possibile ottenere una spiegazione per il modello con dati di test diversi. I passaggi in questa sezione illustrano come calcolare e visualizzare l'importanza delle funzionalità progettate in base ai dati di test.

### <a name="retrieve-any-other-automl-model-from-training"></a>Recuperare qualsiasi altro modello AutoML dal trainingRetrieve any other AutoML model from training

```python
automl_run, fitted_model = local_run.get_output(metric='accuracy')
```

### <a name="set-up-the-model-explanations"></a>Impostare le spiegazioni del modello

Utilizzare `automl_setup_model_explanations` per ottenere le spiegazioni ingegnerizzate. Il `fitted_model` può generare i seguenti elementi:

- Dati in primo piano da campioni addestrati o di prova
- Elenchi di nomi di feature progettati
- Classi individuabili nella colonna con etichetta negli scenari di classificazioneFindable classes in your labeled column in classification scenarios

Il `automl_explainer_setup_obj` contiene tutte le strutture dall'elenco sopra.

```python
from azureml.train.automl.runtime.automl_explain_utilities import automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inizializzare lo spiegatore simulatore per l'importanza della funzionalità

Per generare una spiegazione per `MimicWrapper` i modelli AutoML, utilizzare la classe . È possibile inizializzare MimicWrapper con questi parametri:You can initialize the MimicWrapper with these parameters:

- L'oggetto di impostazione dell'esplicativo
- La tua area di lavoro
- Un modello LightGBM, che funge da `fitted_model` surrogato del modello mL automatizzato

Il MimicWrapper prende `automl_run` anche l'oggetto in cui verranno caricate le spiegazioni ingegnerizzate.

```python
from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.explain.model.mimic_wrapper import MimicWrapper

# Initialize the Mimic Explainer
explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Utilizzare MimicExplainer per calcolare e visualizzare l'importanza delle funzionalità ingegnerizzate

È possibile `explain()` chiamare il metodo in MimicWrapper con gli esempi di test trasformati per ottenere l'importanza della funzionalità per le funzionalità progettate generate. È inoltre `ExplanationDashboard` possibile visualizzare la visualizzazione del dashboard dei valori di importanza delle entità geografiche generate da parte di featurizzatori ML automatizzati.

```python
engineered_explanations = explainer.explain(['local', 'global'], eval_dataset=automl_explainer_setup_obj.X_test_transform)
print(engineered_explanations.get_feature_importance_dict())
```

### <a name="interpretability-during-inference"></a>Interpretazionità durante l'inferenza

In questa sezione viene illustrato come rendere operativo un modello di ML automatizzato con lo strumento di spiegazione utilizzato per calcolare le spiegazioni nella sezione precedente.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrare il modello e l'esplicativo del punteggio

Utilizzare `TreeScoringExplainer` l'oggetto per creare l'elaborazione del punteggio che calcolerà i valori di importanza delle funzionalità ingegnerizzate in fase di inferenza. Inizializzare lo explainer `feature_map` del punteggio con il che è stato calcolato in precedenza. 

Salvare l'esplicatore di punteggio e quindi registrare il modello e lo explainer del punteggio con il servizio di gestione modelli. Eseguire il codice seguente:

```python
from azureml.interpret.scoring.scoring_explainer import TreeScoringExplainer, save

# Initialize the ScoringExplainer
scoring_explainer = TreeScoringExplainer(explainer.explainer, feature_maps=[automl_explainer_setup_obj.feature_map])

# Pickle scoring explainer locally
save(scoring_explainer, exist_ok=True)

# Register trained automl model present in the 'outputs' folder in the artifacts
original_model = automl_run.register_model(model_name='automl_model', 
                                           model_path='outputs/model.pkl')

# Register scoring explainer
automl_run.upload_file('scoring_explainer.pkl', 'scoring_explainer.pkl')
scoring_explainer_model = automl_run.register_model(model_name='scoring_explainer', model_path='scoring_explainer.pkl')
```

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Creare le dipendenze conda per l'impostazione del servizioCreate the conda dependencies for setting up the service

Successivamente, creare le dipendenze di ambiente necessarie nel contenitore per il modello distribuito. Si noti che i valori predefiniti di azureml con la versione >1.0.45 devono essere elencati come dipendenza pip, perché contiene la funzionalità necessaria per ospitare il modello come servizio Web.

```python
from azureml.core.conda_dependencies import CondaDependencies

azureml_pip_packages = [
    'azureml-interpret', 'azureml-train-automl', 'azureml-defaults'
]

myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas', 'numpy', 'py-xgboost<=0.80'],
                                 pip_packages=azureml_pip_packages,
                                 pin_sdk_version=True)

with open("myenv.yml","w") as f:
    f.write(myenv.serialize_to_string())

with open("myenv.yml","r") as f:
    print(f.read())

```

### <a name="deploy-the-service"></a>Distribuire il servizio

Distribuire il servizio usando il file conda e il file di punteggio dei passaggi precedenti.

```python
from azureml.core.webservice import Webservice
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model, InferenceConfig
from azureml.core.environment import Environment

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                               memory_gb=1,
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"},
                                               description='Get local explanations for Bank marketing test data')
myenv = Environment.from_conda_specification(name="myenv", file_path="myenv.yml")
inference_config = InferenceConfig(entry_script="score_local_explain.py", environment=myenv)

# Use configs and models generated above
service = Model.deploy(ws,
                       'model-scoring',
                       [scoring_explainer_model, original_model],
                       inference_config,
                       aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-with-test-data"></a>Inferenza con i dati di test

Inferenza con alcuni dati di test per visualizzare il valore stimato dal modello ML automatizzato. Visualizzare l'importanza delle funzionalità progettate per il valore stimato.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
```

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualizza per scoprire modelli nei dati e spiegazioni in fase di allenamento

È possibile visualizzare il grafico di importanza delle caratteristiche nell'area di lavoro in [Azure Machine Learning Studio.](https://ml.azure.com) Al termine dell'esecuzione automatica di ML, selezionare **Visualizza dettagli modello** per visualizzare un'esecuzione specifica. Selezionare la scheda **Spiegazioni** per visualizzare il dashboard di visualizzazione della spiegazione.

[![Machine Learning Interpretability Architecture](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png)](./media/how-to-machine-learning-interpretability-automl/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come abilitare le spiegazioni del modello e l'importanza delle funzionalità in aree di Azure Machine Learning SDK diverse dall'apprendimento automatico automatizzato, vedere [l'articolo sul concetto sull'interpretabilità.](how-to-machine-learning-interpretability.md)
