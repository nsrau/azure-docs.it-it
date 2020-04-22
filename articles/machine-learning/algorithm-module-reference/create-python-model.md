---
title: 'Crea modello Python: riferimento al modulo'
titleSuffix: Azure Machine Learning
description: Informazioni su come usare il modulo Crea modello Python in Azure Machine Learning per creare un modulo di modellazione o di elaborazione dati personalizzato.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: c8be0882452dc120f538394a5481769e26e3fa15
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682816"
---
# <a name="create-python-model-module"></a>Crea modulo modello Python

Questo articolo descrive un modulo nella finestra di progettazione di Azure Machine Learning (anteprima).

Informazioni su come usare il modulo Crea modello Python per creare un modello non sottoposto a training da uno script Python. È possibile basare il modello su qualsiasi allievo incluso in un pacchetto Python nell'ambiente di progettazione di Azure Machine Learning.You can base the model on any learner that's included in a Python package in the Azure Machine Learning designer environment. 

Dopo aver creato il modello, è possibile usare il modello di training per eseguire il training del modello in un set di dati, ad esempio qualsiasi altro allievo in Azure Machine Learning.After you create the model, you can use [Train Model](train-model.md) to train the model on a dataset, like any other learner in Azure Machine Learning. Il modello sottoposto a training può essere passato al modello di [punteggio](score-model.md) per eseguire stime. È quindi possibile salvare il modello sottoposto a training e pubblicare il flusso di lavoro di assegnazione del punteggio come servizio Web.You can then save the trained model and publish the scoring workflow as a web service.

> [!WARNING]
> Attualmente, non è possibile passare i risultati con punteggio di un modello Python per valutare il [modello](evaluate-model.md). Se è necessario valutare un modello, è possibile scrivere uno script Python personalizzato ed eseguirlo utilizzando il modulo [Esegui script Python.](execute-python-script.md)  


## <a name="configure-the-module"></a>Configurare il modulo

L'uso di questo modulo richiede una conoscenza intermedia o esperta di Python. The module supports use of any learner that's included in the Python packages already installed in Azure Machine Learning. Vedere l'elenco dei pacchetti Python preinstallato in [Esegui Python Script](execute-python-script.md).

> [!NOTE]
> Prestare molta attenzione quando si scrive lo script e si assicura che non vi sia alcun errore di sintassi, ad esempio utilizzando un oggetto non dichiarato o un modulo non importato.

> [!NOTE]
Prestare anche ulteriori attenzioni all'elenco dei moduli preinstallati in [Execute Python Script](execute-python-script.md). Importare solo moduli preinstallati. Si prega di non installare pacchetti aggiuntivi come "pip install xgboost" in questo script, altrimenti verranno generati errori durante la lettura di modelli in moduli down-stream.
  
Questo articolo illustra come usare **Create Python Model** con una pipeline semplice. Ecco un diagramma della pipeline:Here's a diagram of the pipeline:

![Diagramma della creazione di un modello Python](./media/module/create-python-model.png)

1. Selezionare **Crea modello Python**e modificare lo script per implementare il processo di modellazione o gestione dei dati. È possibile basare il modello su qualsiasi allievo incluso in un pacchetto Python nell'ambiente Azure Machine Learning.You can base the model on any learner that's included in a Python package in the Azure Machine Learning environment.

> [!NOTE]
> Prestare particolare attenzione ai commenti nel codice di esempio dello script e assicurarsi che lo script segua rigorosamente il requisito, inclusi il nome della classe, i metodi e la firma del metodo. La violazione porterà a eccezioni. 

   Il codice di esempio seguente del classificatore Naive Bayes a due classi utilizza il pacchetto *sklearn* più diffuso:

   ```Python

   # The script MUST define a class named AzureMLModel.
   # This class MUST at least define the following three methods:
       # __init__: in which self.model must be assigned,
       # train: which trains self.model, the two input arguments must be pandas DataFrame,
       # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
   # The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.

   # Please do not install extra packages such as "pip install xgboost" in this script,
   # otherwise errors will be raised when reading models in down-stream modules.
   
   import pandas as pd
   from sklearn.naive_bayes import GaussianNB


   class AzureMLModel:
       def __init__(self):
           self.model = GaussianNB()
           self.feature_column_names = list()

       def train(self, df_train, df_label):
           # self.feature_column_names records the column names used for training.
           # It is recommended to set this attribute before training so that the
           # feature columns used in predict and train methods have the same names.
           self.feature_column_names = df_train.columns.tolist()
           self.model.fit(df_train, df_label)

       def predict(self, df):
           # The feature columns used for prediction MUST have the same names as the ones for training.
           # The name of score column ("Scored Labels" in this case) MUST be different from any other columns in input data.
           return pd.DataFrame(
               {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
                'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
           )


   ```

1. Collegare il modulo **Crea modello Python** appena creato per eseguire il training del **modello** e il modello **di partitura**.

1. Se è necessario valutare il modello, aggiungere un modulo [Execute Python Script](execute-python-script.md) e modificare lo script Python.

   Lo script seguente è codice di valutazione di esempio:The following script is sample evaluation code:

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

Vedere il set di moduli disponibili per Azure Machine Learning.See the [set of modules available](module-reference.md) to Azure Machine Learning. 