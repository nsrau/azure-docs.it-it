---
title: Interpretazione dei modelli in Machine Learning automatizzato
titleSuffix: Azure Machine Learning
description: Informazioni su come ottenere spiegazioni sul modo in cui il modello di Machine Learning automatico determina l'importanza della funzionalità ed esegue stime quando si usa il Azure Machine Learning SDK.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: trbye
ms.date: 10/25/2019
ms.openlocfilehash: 5bde67913bf5e23345974f52dd6a9a22e2dd4865
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2019
ms.locfileid: "74158442"
---
# <a name="model-interpretability-in-automated-machine-learning"></a>Interpretazione dei modelli in Machine Learning automatizzato

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Questo articolo illustra come abilitare le funzionalità di interpretazione per Machine Learning automatiche (ML) nel servizio Azure Machine Learning. Machine Learning Machine Learning consente di comprendere l'importanza della funzionalità RAW and engineeringed. Per utilizzare l'interpretazione dei modelli, impostare `model_explainability=True` nell'oggetto `AutoMLConfig`.  

In questo articolo viene spiegato come:

- Eseguire l'interpretazione durante il training per il modello migliore o qualsiasi modello.
- Abilitare le visualizzazioni per visualizzare i modelli nei dati e nelle spiegazioni.
- Implementare l'interpretazione durante l'inferenza o il punteggio.

## <a name="prerequisites"></a>prerequisiti

- Funzionalità di interpretazione. Eseguire `pip install azureml-interpret azureml-contrib-interpret` per ottenere i pacchetti necessari.
- Conoscenza della creazione di esperimenti di Machine Learning automatizzati. Per altre informazioni su come usare l'SDK Azure Machine Learning, completare questa [esercitazione sul modello di regressione](tutorial-auto-train-models.md) o vedere come [configurare esperimenti](how-to-configure-auto-train.md)di Machine Learning automatici.

## <a name="interpretability-during-training-for-the-best-model"></a>Interpretazione durante il training per il modello migliore

Recuperare la spiegazione dalla `best_run`, che include spiegazioni per le funzionalità di progettazione e le funzionalità non elaborate.

### <a name="download-engineered-feature-importance-from-artifact-store"></a>Scarica l'importanza della funzionalità progettata dall'archivio elementi

È possibile usare `ExplanationClient` per scaricare le spiegazioni delle funzionalità progettate dall'archivio elementi della `best_run`. Per ottenere la spiegazione per le funzionalità non elaborate impostate `raw=True`.

```python
from azureml.contrib.interpret.explanation.explanation_client import ExplanationClient

client = ExplanationClient.from_run(best_run)
engineered_explanations = client.download_model_explanation(raw=False)
print(engineered_explanations.get_feature_importance_dict())
```

## <a name="interpretability-during-training-for-any-model"></a>Interpretazione durante il training di qualsiasi modello 

Quando si calcolano le spiegazioni dei modelli e le si visualizza, non si è limitati a una spiegazione del modello esistente per un modello di ML automatico. È anche possibile ottenere una spiegazione per il modello con dati di test diversi. I passaggi descritti in questa sezione illustrano come calcolare e visualizzare l'importanza della funzionalità progettata e l'importanza della funzionalità non elaborata in base ai dati di test.

### <a name="retrieve-any-other-automl-model-from-training"></a>Recuperare qualsiasi altro modello di AutoML da training

```python
automl_run, fitted_model = local_run.get_output(metric='r2_score')
```

### <a name="set-up-the-model-explanations"></a>Configurare le spiegazioni del modello

Usare `automl_setup_model_explanations` per ottenere spiegazioni sulle funzionalità non elaborate e progettate. Il `fitted_model` può generare gli elementi seguenti:

- Dati in primo piano da esempi di training o di test
- Elenchi di nomi di funzionalità non elaborati e progettati
- Classi trovabili nella colonna con etichetta negli scenari di classificazione

Il `automl_explainer_setup_obj` contiene tutte le strutture dall'elenco precedente.

```python
from azureml.train.automl.automl_explain_utilities import AutoMLExplainerSetupClass, automl_setup_model_explanations

automl_explainer_setup_obj = automl_setup_model_explanations(fitted_model, X=X_train, 
                                                             X_test=X_test, y=y_train, 
                                                             task='classification')
```

### <a name="initialize-the-mimic-explainer-for-feature-importance"></a>Inizializzare il Explainer MIME per l'importanza della funzionalità

Per generare una spiegazione per i modelli AutoML, usare la classe `MimicWrapper`. È possibile inizializzare MimicWrapper con questi parametri:

- Oggetto di installazione di Explainer
- Area di lavoro
- Modello LightGBM, che funge da surrogato per il modello di Machine Learning `fitted_model` automatizzato

Il MimicWrapper accetta anche l'oggetto `automl_run` in cui verranno caricate le spiegazioni non elaborate e progettate.

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

È possibile chiamare il metodo `explain()` in MimicWrapper con gli esempi di test trasformati per ottenere l'importanza della funzionalità per le funzionalità di progettazione generate. È anche possibile usare `ExplanationDashboard` per visualizzare la visualizzazione del dashboard dei valori di importanza delle funzionalità delle funzionalità di progettazione generate da Machine Learning featurizers.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard
engineered_explanations = explainer.explain(['local', 'global'],              
                                            eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(engineered_explanations.get_feature_importance_dict())
ExplanationDashboard(engineered_explanations, automl_explainer_setup_obj.automl_estimator, automl_explainer_setup_obj.X_test_transform)
```

### <a name="use-mimic-explainer-for-computing-and-visualizing-raw-feature-importance"></a>Usare il Visualizzatore di simulazioni per l'elaborazione e la visualizzazione dell'importanza della funzionalità RAW

È possibile chiamare di nuovo il metodo `explain()` in MimicWrapper con gli esempi di test trasformati e l'impostazione `get_raw=True` per ottenere l'importanza della funzionalità per le funzionalità non elaborate. È anche possibile usare `ExplanationDashboard` per visualizzare la visualizzazione del dashboard dei valori di importanza delle funzionalità delle funzionalità non elaborate.

```python
from azureml.contrib.interpret.visualize import ExplanationDashboard

raw_explanations = explainer.explain(['local', 'global'], get_raw=True, 
                                     raw_feature_names=automl_explainer_setup_obj.raw_feature_names,
                                     eval_dataset=automl_explainer_setup_obj.X_test_transform)

print(raw_explanations.get_feature_importance_dict())
ExplanationDashboard(raw_explanations, automl_explainer_setup_obj.automl_pipeline, automl_explainer_setup_obj.X_test_raw)
```

### <a name="interpretability-during-inference"></a>Interpretazione durante l'inferenza

Questa sezione descrive come rendere operativo un modello di Machine Learning automatizzato con lo Explainer usato per calcolare le spiegazioni nella sezione precedente.

### <a name="register-the-model-and-the-scoring-explainer"></a>Registrare il modello e il spiegazione del Punteggio

Usare il `TreeScoringExplainer` per creare il descrittore dei punteggi che calcolerà i valori di importanza delle funzionalità non elaborate e progettate in fase di inferenza. Si inizializza la descrizione del punteggio con la `feature_map` calcolata in precedenza. Il spiegatore dei punteggi usa il `feature_map` per restituire l'importanza della funzionalità non elaborata.

Salvare il Explainer di assegnazione dei punteggi, quindi registrare il modello e il Explainer di assegnazione dei punteggi con il servizio Gestione modelli. Eseguire il codice seguente:

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

Successivamente, creare le dipendenze di ambiente necessarie nel contenitore per il modello distribuito.

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

### <a name="inference-with-test-data"></a>Inferenza con dati di test

Inferenza con alcuni dati di test per visualizzare il valore stimato dal modello di ML automatico. Consente di visualizzare l'importanza della funzionalità progettata per il valore stimato e l'importanza della funzionalità non elaborata per il valore stimato.

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

### <a name="visualize-to-discover-patterns-in-data-and-explanations-at-training-time"></a>Visualizza per individuare i modelli nei dati e le spiegazioni in fase di training

È possibile visualizzare il grafico importanza funzionalità nell'area di lavoro in [Azure Machine Learning Studio](https://ml.azure.com). Al termine dell'esecuzione automatica di Machine Learning, selezionare **Visualizza dettagli modello** per visualizzare un'esecuzione specifica. Selezionare la scheda **spiegazioni** per visualizzare il dashboard di visualizzazione spiegazione.

[Architettura di interpretazione Machine Learning ![](./media/machine-learning-interpretability-explainability/automl-explainability.png)](./media/machine-learning-interpretability-explainability/automl-explainability.png#lightbox)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni su come abilitare le spiegazioni dei modelli e l'importanza delle funzionalità nelle aree di Azure Machine Learning SDK, ad eccezione dell'apprendimento automatico automatico, vedere l' [articolo relativo al concetto di interpretazione](how-to-machine-learning-interpretability.md).
