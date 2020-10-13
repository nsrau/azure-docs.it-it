---
title: Usare Studio per distribuire i modelli con training nella finestra di progettazione
titleSuffix: Azure Machine Learning
description: Usare Azure Machine Learning Studio per distribuire i modelli sottoposti a training nella finestra di progettazione.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/12/2020
ms.topic: conceptual
ms.custom: how-to, deploy, studio
ms.openlocfilehash: e2f3e0b596847000af62aa6e23da5b137ee9de33
ms.sourcegitcommit: 090ea6e8811663941827d1104b4593e29774fa19
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91999002"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>Usare Studio per distribuire i modelli con training nella finestra di progettazione

In questo articolo si apprenderà come distribuire un modello sottoposto a training dalla finestra di progettazione come endpoint in tempo reale in Azure Machine Learning Studio.

La distribuzione in studio prevede i passaggi seguenti:

1. Registrare il modello sottoposto a training.
1. Scaricare lo script di immissione e il file delle dipendenze conda per il modello.
1. Opzionale Configurare lo script di immissione.
1. Distribuire il modello in una destinazione di calcolo.

È anche possibile distribuire i modelli direttamente nella finestra di progettazione per ignorare i passaggi di registrazione del modello e di download dei file. Questa operazione può essere utile per la distribuzione rapida. Per altre informazioni, vedere [distribuire un modello con la finestra di progettazione](tutorial-designer-automobile-price-deploy.md).

I modelli sottoposti a training nella finestra di progettazione possono essere distribuiti anche tramite l'SDK o l'interfaccia della riga di comando (CLI). Per ulteriori informazioni, vedere la pagina relativa alla [distribuzione del modello esistente con Azure Machine Learning](how-to-deploy-existing-model.md).

## <a name="prerequisites"></a>Prerequisiti

* [Area di lavoro Azure Machine Learning](how-to-manage-workspace.md)

* Una pipeline di training completata contenente uno dei moduli seguenti:
    - [Modulo Train Model](./algorithm-module-reference/train-model.md)
    - [Modulo Train Anomaly Detection Model](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [Modulo Train clustering Model](./algorithm-module-reference/train-clustering-model.md)
    - [Modulo Train Pytorch Model](./algorithm-module-reference/train-pytorch-model.md)
    - [Modulo Train SVD Recommender](./algorithm-module-reference/train-svd-recommender.md)
    - [Modulo Train Vowpal Wabbit Model](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [Modulo Train Wide & Deep Model](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>Registrare il modello

Al termine della pipeline di training, registrare il modello sottoposto a training nell'area di lavoro di Azure Machine Learning per accedere al modello in altri progetti.

1. Selezionare il [modulo Train Model](./algorithm-module-reference/train-model.md).
1. Selezionare la scheda **output + log** nel riquadro di destra.
1. Selezionare la schermata **registra** icona ![ del modello dell'icona a forma di ingranaggio ](./media/how-to-deploy-model-designer/register-model-icon.png) .

    ![Screenshot del riquadro destro del modulo Train Model](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. Immettere un nome per il modello e quindi selezionare **Salva**.

Dopo aver registrato il modello, è possibile trovarlo nella pagina dei **modelli** asset in studio.
    
![Screenshot del modello registrato nella pagina asset dei modelli](./media/how-to-deploy-model-designer/models-asset-page.png)


## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>Scaricare il file script di immissione e il file delle dipendenze conda

Per distribuire un modello in Azure Machine Learning Studio sono necessari i file seguenti:

- **Entry file script** : carica il modello sottoposto a training, elabora i dati di input dalle richieste, esegue le inferenze in tempo reale e restituisce il risultato. La finestra di progettazione genera automaticamente un `score.py` file di script di immissione quando il modulo **Train Model** viene completato.

- **Conda dipendenze file** : specifica i pacchetti PIP e conda da cui dipende il servizio Web. La finestra di progettazione crea automaticamente un `conda_env.yaml` file al termine del modulo **Train Model** .

È possibile scaricare questi due file nel riquadro destro del modulo **Train Model** :

1. Selezionare il modulo **Train Model**.
1. Nella scheda **output + logs** selezionare la cartella `trained_model_outputs` .
1. Scaricare il `conda_env.yaml` file e il `score.py` file.

    ![Screenshot dei file di download per la distribuzione nel riquadro destro](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

In alternativa, è possibile scaricare i file dalla pagina asset dei **modelli** dopo la registrazione del modello:

1. Passare alla pagina dei **modelli** asset.
1. Selezionare il modello che si desidera distribuire.
1. Selezionare la scheda **artefatti** .
1. Selezionare la cartella `trained_model_outputs` .
1. Scaricare il `conda_env.yaml` file e il `score.py` file.  

    ![Screenshot dei file di download per la distribuzione nella pagina dei dettagli del modello](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> Il `score.py` file fornisce quasi le stesse funzionalità dei moduli **Score Model** . Tuttavia, alcuni moduli come [Score SVD Recommender](./algorithm-module-reference/score-svd-recommender.md), [Score Wide e Deep Recommender](./algorithm-module-reference/score-wide-and-deep-recommender.md)e [Score Vowpal Wabbit Model](./algorithm-module-reference/score-vowpal-wabbit-model.md) hanno parametri per diverse modalità di assegnazione dei punteggi. È anche possibile modificare tali parametri nello script di immissione.
>
>Per ulteriori informazioni sull'impostazione dei parametri nel `score.py` file, vedere la sezione [configurare lo script di immissione](#configure-the-entry-script).

## <a name="deploy-the-model"></a>Distribuire il modello

Dopo aver scaricato i file necessari, si è pronti per distribuire il modello.

1. Nella pagina **modello Asset selezionare** il modello registrato.
1. Selezionare il pulsante **Distribuisci** .
1. Nel menu configurazione immettere le informazioni seguenti:

    - Immettere un nome per l'endpoint.
    - Selezionare per distribuire il modello nel [servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md) o nell' [istanza di contenitore di Azure](how-to-deploy-azure-container-instance.md).
    - Caricare `score.py` per il **file di script di immissione**.
    - Caricare `conda_env.yml` per il **file delle dipendenze conda**. 

    >[!TIP]
    > In impostazione **avanzata** è possibile impostare la capacità della CPU/memoria e altri parametri per la distribuzione. Queste impostazioni sono importanti per determinati modelli, ad esempio i modelli PyTorch, che consumano una notevole quantità di memi (circa 4 GB).

1. Selezionare **Distribuisci** per distribuire il modello come endpoint in tempo reale.

    ![Screenshot della pagina Distribuisci modello nella pagina asset del modello](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>Utilizzare l'endpoint in tempo reale

Una volta completata la distribuzione, è possibile trovare l'endpoint in tempo reale nella pagina asset **endpoint** . Al termine, sarà disponibile un endpoint REST, che i client possono usare per inviare richieste all'endpoint in tempo reale. 

> [!NOTE]
> La finestra di progettazione genera anche un file JSON di dati di esempio per il test, che è possibile scaricare `_samples.json` nella cartella **trained_model_outputs** .

Utilizzare l'esempio di codice seguente per utilizzare un endpoint in tempo reale.

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>Utilizzare endpoint in tempo reale correlati alla visione artificiale

Quando si utilizzano gli endpoint in tempo reale correlati alla visione artificiale, è necessario convertire le immagini in byte, poiché il servizio Web accetta solo stringa come input. Di seguito è riportato il codice di esempio:

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>Configurare lo script di immissione

Alcuni moduli della finestra di progettazione come [Score SVD Recommender](./algorithm-module-reference/score-svd-recommender.md), [Score Wide e Deep Recommender](./algorithm-module-reference/score-wide-and-deep-recommender.md)e [Score Vowpal Wabbit Model](./algorithm-module-reference/score-vowpal-wabbit-model.md) hanno parametri per diverse modalità di assegnazione dei punteggi. 

In questa sezione viene illustrato come aggiornare anche questi parametri nel file di script di immissione.

Nell'esempio seguente viene aggiornato il comportamento predefinito per un modello con training **& Deep Recommender** . Per impostazione predefinita, il `score.py` file indica al servizio Web di stimare le classificazioni tra utenti ed elementi. 

È possibile modificare il file di script di immissione per apportare raccomandazioni sugli elementi e restituire gli elementi consigliati modificando il `recommender_prediction_kind` parametro.


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

Per i modelli **Wide & Recommender** e **Vowpal Wabbit** , è possibile configurare il parametro della modalità di assegnazione dei punteggi usando i metodi seguenti:

- I nomi dei parametri sono le combinazioni di caratteri minuscoli e di sottolineatura dei nomi dei parametri per il [Punteggio Vowpal Wabbit Model](./algorithm-module-reference/score-vowpal-wabbit-model.md) e il [Punteggio Wide e Deep Recommender](./algorithm-module-reference/score-wide-and-deep-recommender.md);
- I valori dei parametri di tipo mode sono stringhe dei nomi di opzioni corrispondenti. Utilizzare il **tipo di stima del raccomandazione** nei codici precedenti come esempio, il valore può essere `'Rating Prediction'` o `'Item Recommendation'` . Altri valori non sono consentiti.

Per il modello con training di **SVD Recommender** , i nomi e i valori dei parametri potrebbero essere meno evidenti ed è possibile cercare le tabelle seguenti per decidere come impostare i parametri.

| Nome parametro in [Score SVD Recommender](./algorithm-module-reference/score-svd-recommender.md)                           | Nome del parametro nel file di script di immissione |
| ------------------------------------------------------------ | --------------------------------------- |
| Recommender prediction kind                                  | prediction_kind                         |
| Recommended item selection                                   | recommended_item_selection              |
| Minimum size of the recommendation pool for a single user    | min_recommendation_pool_size            |
| Maximum number of items to recommend to a user               | max_recommended_item_count              |
| Indica se restituire le classificazioni stimate degli elementi insieme alle etichette | return_ratings                          |

Il codice seguente illustra come impostare i parametri per un SVD Recommender, che usa tutti i sei parametri per consigliare gli elementi classificati con le classificazioni stimate associate.

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>Passaggi successivi

* [Eseguire il training di un modello nella finestra di progettazione](tutorial-designer-automobile-price-train-score.md)
* [Risolvere i problemi relativi a una distribuzione non riuscita](how-to-troubleshoot-deployment.md)
* [Distribuire nel servizio Azure Kubernetes](how-to-deploy-azure-kubernetes-service.md)
* [Creazione di applicazioni client per l'utilizzo di servizi Web](how-to-consume-web-service.md)
* [Aggiornare un servizio Web](how-to-deploy-update-web-service.md)
