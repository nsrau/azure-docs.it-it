---
title: Interpretazione dei modelli in ML automatizzato
titleSuffix: Azure Machine Learning
description: Informazioni su come spiegare il motivo per cui il modello di Machine Learning automatizzato esegue stime usando il Azure Machine Learning SDK. Può essere utilizzato durante il training e l'inferenza per comprendere in che modo il modello determina l'importanza della funzionalità ed esegue stime.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 2c9df55eb319dd45281eca4684c79d83dc6ef933
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73515330"
---
# <a name="model-interpretability-for-automated-ml-models"></a>Interpretazione dei modelli per i modelli di Machine Learning automatici

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come abilitare la funzionalità di interpretazione nell'apprendimento automatico automatico usando il servizio Azure Machine Learning. Automated Machine Learning consente di comprendere l'importanza della funzionalità RAW and engineeringed. Per utilizzare l'interpretazione dei modelli, impostare `model_explainability=True` nell'oggetto `AutoMLConfig`.  

In questo articolo vengono illustrate le attività seguenti:

* Interpretazione durante il training per il modello migliore o qualsiasi modello
* Abilitazione delle visualizzazioni per facilitare l'individuazione di modelli nei dati e nelle spiegazioni
* Interpretazione durante l'inferenza o il Punteggio

## <a name="prerequisites"></a>Prerequisiti

* Eseguire `pip install azureml-interpret azureml-contrib-interpret` per ottenere i pacchetti necessari per le funzionalità di interpretazione.
* Questo articolo presuppone la conoscenza della creazione di esperimenti di Machine Learning automatizzati. Per informazioni sull'uso di Machine Learning automatiche in SDK, vedere l' [esercitazione](tutorial-auto-train-models.md) o [procedura](how-to-configure-auto-train.md) .
 
## <a name="interpretability-during-training-for-the-best-model"></a>Interpretazione durante il training per il modello migliore 

Recuperare la spiegazione dalla `best_run`, che include spiegazioni per le funzionalità di progettazione e le funzionalità non elaborate. 

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Scarica l'importanza della funzionalità progettata dall'archivio elementi

È possibile usare `ExplanationClient` per scaricare le spiegazioni delle funzionalità progettate dall'archivio elementi del best_run. Per ottenere la spiegazione per le funzionalità non elaborate impostate `raw=True`. 

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretazione durante il training di qualsiasi modello 

In questa sezione viene illustrato come calcolare le spiegazioni dei modelli e visualizzare le spiegazioni. Oltre a recuperare una spiegazione del modello esistente per un modello di Machine Learning automatico, è anche possibile illustrare il modello con dati di test diversi. La procedura seguente consente di calcolare e visualizzare l'importanza della funzionalità progettata e l'importanza della funzionalità RAW in base ai dati di test.

### <a name="retrieve-any-other-automl-model-from-training"></a>Recuperare qualsiasi altro modello di AutoML da training

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="setup-the-model-explanations"></a>Configurare le spiegazioni del modello

Fitted_model può generare gli elementi seguenti, che verranno usati per ottenere le spiegazioni delle funzionalità non elaborate e progettate usando automl_setup_model_explanations:

* Trasformato di dati da esempi di training/esempi di test
* Raccolta di elenchi di nomi di funzionalità non elaborati e progettati
* Trovare le classi nella colonna con etichetta negli scenari di classificazione

Il automl_explainer_setup_obj contiene tutte le strutture dall'elenco precedente.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```
### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inizializzare il Explainer MIME per l'importanza della funzionalità

Per spiegare i modelli AutoML, usare la classe `MimicWrapper`. MimicWrapper può essere inizializzato con i parametri per l'oggetto di installazione di Explainer, l'area di lavoro e un modello LightGBM che funge da modello surrogato per spiegare il modello di Machine Learning automatico (fitted_model qui). Il MimicWrapper accetta anche l'oggetto automl_run in cui verranno caricate le spiegazioni non elaborate e progettate.

```python
from azureml.interpret.mimic.models.lightgbm_model import LGBMExplainableModel
from azureml.interpret.mimic_wrapper import MimicWrapper

explainer = MimicWrapper(ws, automl_explainer_setup_obj.automl_estimator, LGBMExplainableModel, 
                         init_dataset=automl_explainer_setup_obj.X_transform, run=automl_run,
                         features=automl_explainer_setup_obj.engineered_feature_names, 
                         feature_maps=[automl_explainer_setup_obj.feature_map],
                         classes=automl_explainer_setup_obj.classes)
```

### <a name="use-mimicexplainer-for-computing-and-visualizing-engineered-feature-importance"></a>Usare MimicExplainer per l'elaborazione e la visualizzazione dell'importanza della funzionalità progettata

Il metodo explain () in MimicWrapper può essere chiamato con gli esempi di test trasformati per ottenere l'importanza della funzionalità per le funzionalità di progettazione generate. È anche possibile usare ExplanationDashboard per visualizzare la visualizzazione della lavagna del tratteggio dei valori di importanza della funzionalità delle funzionalità generate da Machine Learning automatiche di ML featurizers.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```
### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Usare il Visualizzatore di simulazioni per l'elaborazione e la visualizzazione dell'importanza della funzionalità RAW

Il metodo explain () in MimicWrapper può essere chiamato di nuovo con gli esempi di test trasformati e impostando `get_raw` su true per ottenere l'importanza della funzionalità per le funzionalità non elaborate. È anche possibile usare ExplanationDashboard per visualizzare la visualizzazione del dashboard dei valori di importanza della funzionalità delle funzionalità non elaborate.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Interpretazione durante l'inferenza

Questa sezione descrive come rendere operativo un modello di Machine Learning automatico con il Explainer, usato per calcolare le spiegazioni nella sezione precedente.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrare il modello e il spiegazione del Punteggio

Usare il `TreeScoringExplainer` per creare il Explainer di assegnazione dei punteggi, che verrà usato per calcolare i valori di importanza delle funzionalità non elaborate e progettate in fase di inferenza. Si noti che si inizializza il Explainer di assegnazione dei punteggi con il feature_map calcolato in precedenza. Il feature_map verrà usato dal Explainer di assegnazione dei punteggi per restituire l'importanza della funzionalità non elaborata.

Nel codice riportato di seguito si salva il Explainer di assegnazione dei punteggi e si registra il modello e la descrizione del punteggio con il servizio Gestione modelli.

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

### <a name="create-the-conda-dependencies-for-setting-up-the-service"></a>Creare le dipendenze conda per la configurazione del servizio

Successivamente, è possibile creare le dipendenze di ambiente necessarie nel contenitore per il modello distribuito.

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

Distribuire il servizio usando il file conda e il file di assegnazione dei punteggi dei passaggi precedenti.

```python
from azureml.core.webservice import Webservice
from azureml.core.model import InferenceConfig
from azureml.core.webservice import AciWebservice
from azureml.core.model import Model

aciconfig = AciWebservice.deploy_configuration(cpu_cores=1, 
                                               memory_gb=1, 
                                               tags={"data": "Bank Marketing",  
                                                     "method" : "local_explanation"}, 
                                               description='Get local explanations for Bank marketing test data')

inference_config = InferenceConfig(runtime= "python", 
                                   entry_script="score_local_explain.py",
                                   conda_file="myenv.yml")

# Use configs and models generated above
service = Model.deploy(ws, 'model-scoring', [scoring_explainer_model, original_model], inference_config, aciconfig)
service.wait_for_deployment(show_output=True)
```

### <a name="inference-using-test-data"></a>Inferenza con dati di test

Inferenza usando alcuni dati di test per visualizzare il valore stimato dal modello di Machine Learning automatico e visualizzare l'importanza della funzionalità progettata per il valore stimato e l'importanza della funzionalità non elaborata per il valore stimato.

```python
if service.state == 'Healthy':
    # Serialize the first row of the test data into json
    X_test_json = X_test[:1].to_json(orient='records')
    print(X_test_json)
    # Call the service to get the predictions and the engineered and raw explanations
    output = service.run(X_test_json)
    # Print the predicted value
    print(output['predictions'])
    # Print the engineered feature importances for the predicted value
    print(output['engineered_local_importance_values'])
    # Print the raw feature importances for the predicted value
    print(output['raw_local_importance_values'])
```

### <a name="visualizations-to-aid-you-in-the-discovery-of-patterns-in-data-and-explanations-at-training-time"></a>Visualizzazioni che facilitano l'individuazione di modelli nei dati e spiegazioni in fase di training

È anche possibile visualizzare il grafico importanza funzionalità nell'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com). Una volta completata l'esecuzione automatica di Machine Learning, è necessario fare clic su "Visualizza dettagli modello", che consente di passare a un'esecuzione specifica. Da qui è possibile fare clic sulla scheda "spiegazione" per visualizzare il dashboard di visualizzazione spiegazione. 

[Architettura di interpretazione Machine Learning ![](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come è possibile abilitare le spiegazioni dei modelli e l'importanza delle funzionalità in altre aree dell'SDK al di fuori dell'apprendimento automatico automatico, vedere l'articolo relativo al [concetto](how-to-machine-learning-interpretability.md) di interpretazione.