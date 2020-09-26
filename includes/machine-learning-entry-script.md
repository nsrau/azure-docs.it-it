---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 4975bb2a8ad384b8abc28f1d1835c2c9e98b8c54
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91315431"
---
Lo script di avvio riceve i dati inviati a un servizio Web distribuito e li passa al modello. Riceve quindi la risposta restituita dal modello e la invia al client. *Lo script è specifico del modello*. Deve comprendere i dati previsti e restituiti dal modello.

Gli elementi necessari per eseguire lo script di immissione sono i seguenti:

1. Caricamento del modello (utilizzando una funzione denominata `init()` )
1. Esecuzione del modello sui dati di input (utilizzando una funzione denominata `run()` )

Di seguito vengono illustrati i passaggi dettagliati.

### <a name="writing-init"></a>Scrittura di init () 

#### <a name="loading-a-registered-model"></a>Caricamento di un modello registrato

I modelli registrati vengono archiviati in un percorso puntato da una variabile di ambiente denominata `AZUREML_MODEL_DIR` . Per altre informazioni sulla struttura di directory esatta, vedere [individuare i file di modello nello script di immissione](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models)

#### <a name="loading-a-local-model"></a>Caricamento di un modello locale

Se si è scelto di eseguire la registrazione del modello e di passare il modello come parte della directory di origine, è possibile leggerlo in come in locale, passando il percorso al modello relativo allo script di assegnazione dei punteggi. Ad esempio, se una directory è strutturata come segue:

```bash

- source_dir
    - score.py
    - models
        - model1.onnx

```

è possibile caricare i modelli con il codice Python seguente:

```python
import os

model = open(os.path.join('.', 'models', 'model1.onnx'))
```

#### <a name="writing-run"></a>Scrittura in corso ()

`run()` viene eseguito ogni volta che il modello riceve una richiesta di assegnazione dei punteggi e prevede che il corpo della richiesta sia un documento JSON con la struttura seguente:

```json
{
    "data": <model-specific-data-structure>
}

```

L'input per `run()` è una stringa di Python che contiene qualsiasi elemento che segue la chiave "data".

L'esempio seguente illustra come caricare un modello Scikit-learn registrato e assegnare un punteggio ai dati numpy:

```python
import json
import numpy as np
import os
from sklearn.externals import joblib


def init():
    global model
    model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'sklearn_mnist_model.pkl')
    model = joblib.load(model_path)

def run(data):
    try:
        data = np.array(json.loads(data))
        result = model.predict(data)
        # You can return any data type, as long as it is JSON serializable.
        return result.tolist()
    except Exception as e:
        error = str(e)
        return error
```

Per esempi più avanzati, tra cui la generazione di schemi spavalderia automatici e i dati binari (ad esempio, immagine), leggere [l'articolo sulla creazione di script di immissione avanzata](../articles/machine-learning/how-to-deploy-advanced-entry-script.md)