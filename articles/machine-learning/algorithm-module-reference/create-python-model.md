---
title: 'Creare un modello Python: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modello di creazione modello Python in Azure Machine Learning per creare un modulo di modellazione o elaborazione dati personalizzato.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 11/19/2019
ms.openlocfilehash: 0c1a4f33da7e1f39951d641ed1d563c46fb664ca
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232657"
---
# <a name="create-python-model"></a>Creare il modello Python

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Informazioni su come usare il modulo **Crea modello Python** per creare un modello non sottoposto a training da uno script Python. È possibile basare il modello su qualsiasi strumento di apprendimento incluso in un pacchetto python nell'ambiente Azure Machine Learning Designer. 

Dopo aver creato il modello, è possibile utilizzare [Train Model](train-model.md) per eseguire il training del modello su un set di dati, come qualsiasi altro discente in Azure Machine Learning. Il modello sottoposto a training può essere passato al [modello di Punteggio](score-model.md) per utilizzare il modello per eseguire stime. Il modello con Training può quindi essere salvato e il flusso di lavoro di assegnazione dei punteggi può essere pubblicato come servizio Web.

> [!WARNING]
> Attualmente non è possibile passare i risultati con punteggio di un modello Python per valutare il [modello](evaluate-model.md). Se è necessario valutare un modello, è possibile scrivere uno script Python personalizzato ed eseguirlo usando il modulo [Execute Python script](execute-python-script.md) .  


## <a name="how-to-configure-create-python-model"></a>Come configurare la creazione di un modello Python

L'uso di questo modulo richiede una conoscenza intermedia o esperta di Python. Il modulo supporta l'uso di qualsiasi discente incluso nei pacchetti Python già installati in Azure Machine Learning. Vedere l'elenco di pacchetti Python preinstallato in [Esegui script Python](execute-python-script.md).
  

Questo articolo illustra come usare il modello di **creazione di Python** con una semplice pipeline. Di seguito è riportato il grafico della pipeline.

![creare-Python-Model](./media/module/aml-create-python-model.png)

1.  Fare clic su **Crea modello Python**, modificare lo script per implementare la modellazione o il processo di gestione dei dati. È possibile basare il modello su qualsiasi discente incluso in un pacchetto python nell'ambiente Azure Machine Learning.


    Di seguito è riportato un codice di esempio di classificatore Naive Bayes a due classi usando il popolare pacchetto *sklearn* .

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


2. Connettere il modulo di **creazione del modello Python** appena creato a un modello **Train** e un **modello di Punteggio**

3. Se è necessario valutare il modello, aggiungere uno [script Execute Python](execute-python-script.md) e modificare lo script Python per implementare la valutazione.

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
