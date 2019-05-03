---
title: 'Creare modelli di Python: Riferimento al modulo'
titleSuffix: Azure Machine Learning service
description: Informazioni su come usare il modello Crea modello di Python nel servizio Azure Machine Learning per creare modulo personalizzato di modellazione o l'elaborazione dei dati.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ea9b50cede3e2d264ca0476b6a987ca6896c3c79
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/02/2019
ms.locfileid: "65029746"
---
# <a name="create-python-model"></a>Creare modelli di Python

Questo articolo descrive come usare il **Create Model Python** modulo per creare un modello senza Training da uno script Python. 

È possibile basare il modello in qualsiasi strumento di apprendimento incluso in un pacchetto Python nell'ambiente di Azure Machine Learning. 

Dopo aver creato il modello, è possibile usare [Train Model](train-model.md) per il training del modello su un set di dati, come qualsiasi altro strumento di apprendimento in Azure Machine Learning. Il modello con training può essere passato a [Score Model](score-model.md) per utilizzare il modello per eseguire stime. Il modello con training può quindi essere salvato e il flusso di lavoro assegnazione dei punteggi può essere pubblicato come servizio web.

> [!WARNING]
> Non è attualmente possibile passare i risultati con punteggi di un modello Python per [Evaluate Model](evaluate-model.md). Se è necessario valutare un modello, è possibile scrivere Python personalizzato dello script ed eseguirlo usando il [Execute Python Script](execute-python-script.md) modulo.  


## <a name="how-to-configure-create-python-model"></a>Come configurare Create Model Python

Uso di questo modulo richiede una conoscenza intermedia o esperti di Python. Il modulo supporta l'uso di qualsiasi strumento di apprendimento è inclusa nei pacchetti Python già installati in Azure Machine Learning. Vedere l'elenco dei pacchetti Python pre-installato in [Execute Python Script](execute-python-script.md).
  

Questo articolo illustra come usare il **Create Model Python** con un semplice esperimento. Di seguito è riportato il grafico dell'esperimento.

![Create-python-model](./media/module/aml-create-python-model.png)

1.  Fare clic su **Create Model Python**, modificare lo script per implementare la modellazione o elaborare la gestione dei dati. È possibile basare il modello in qualsiasi strumento di apprendimento incluso in un pacchetto Python nell'ambiente di Azure Machine Learning.


    Di seguito è un codice di esempio del classificatore di Naive Bayes due classi usando il diffuso *sklearn* pacchetto.

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. Connettere il **Create Model Python** modulo appena creato per una **Train Model** e **Score Model**

3. Se è necessario valutare il modello, aggiungere un [Execute Python Script](execute-python-script.md) e modificare lo script Python per implementare la valutazione.

Di seguito è riportato il codice di valutazione di esempio.

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
