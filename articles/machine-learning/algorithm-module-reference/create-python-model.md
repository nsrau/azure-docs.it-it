---
title: 'Creare un modello Python: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Crea modello Python in Azure Machine Learning per creare un modulo di modellazione o elaborazione dati personalizzato.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 929938bba9c9512ecfd663a540cf4a7ebbf68e2b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371818"
---
# <a name="create-python-model-module"></a>Creare il modulo del modello Python

Questo articolo descrive un modulo in Azure Machine Learning Designer (anteprima).

Informazioni su come usare il modulo Crea modello Python per creare un modello non sottoposto a training da uno script Python. È possibile basare il modello su qualsiasi strumento di apprendimento incluso in un pacchetto python nell'ambiente Azure Machine Learning Designer. 

Dopo aver creato il modello, è possibile utilizzare [Train Model](train-model.md) per eseguire il training del modello su un set di dati, come qualsiasi altro discente in Azure Machine Learning. Il modello sottoposto a training può essere passato al [modello di Punteggio](score-model.md) per eseguire stime. È quindi possibile salvare il modello sottoposto a training e pubblicare il flusso di lavoro di assegnazione dei punteggi come servizio Web.

> [!WARNING]
> Attualmente, non è possibile passare i risultati con punteggio di un modello Python per valutare il [modello](evaluate-model.md). Se è necessario valutare un modello, è possibile scrivere uno script Python personalizzato ed eseguirlo usando il modulo [Execute Python script](execute-python-script.md) .  


## <a name="configure-the-module"></a>Configurare il modulo

L'uso di questo modulo richiede una conoscenza intermedia o esperta di Python. Il modulo supporta l'uso di qualsiasi discente incluso nei pacchetti Python già installati in Azure Machine Learning. Vedere l'elenco di pacchetti Python preinstallati in [Execute Python script](execute-python-script.md).
  

Questo articolo illustra come usare la **creazione di un modello Python** con una semplice pipeline. Ecco un diagramma della pipeline:

![Diagramma della creazione di un modello Python](./media/module/create-python-model.png)

1. Selezionare **Crea modello Python**e modificare lo script per implementare la modellazione o il processo di gestione dei dati. È possibile basare il modello su qualsiasi discente incluso in un pacchetto python nell'ambiente Azure Machine Learning.

   Il codice di esempio seguente del classificatore Naive Bayes a due classi usa il popolare pacchetto *sklearn* :

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

1. Connettere il modulo **Crea modello Python** appena creato per eseguire il **Training** del modello e assegnare un **punteggio al modello**.

1. Se è necessario valutare il modello, aggiungere un modulo [Execute Python script (Esegui script Python](execute-python-script.md) ) e modificare lo script Python.

   Lo script seguente è un esempio di codice di valutazione:

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

## <a name="next-steps"></a>Passaggi successivi

Vedere il [set di moduli disponibili](module-reference.md) per Azure Machine Learning. 