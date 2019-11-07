---
title: Definire un'attività di machine learning per un'esecuzione automatica di Machine Learning
titleSuffix: Azure Machine Learning
description: Informazioni su come definire un'attività di machine learning per un'esecuzione automatica di Machine Learning
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: c34a8e999267a77fa4aabbd9dc3a73b2c4f5a0ef
ms.sourcegitcommit: 359930a9387dd3d15d39abd97ad2b8cb69b8c18b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73647205"
---
# <a name="how-to-define-a-machine-learning-task"></a>Come definire un'attività di Machine Learning 
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo vengono illustrate le attività di machine learning supportate e viene illustrato come definirle per un esperimento di Machine Learning automatizzato (ML).


## <a name="What is a machine learning task?"></a>Che cos'è un'attività di Machine Learning?

Un'attività di Machine Learning rappresenta il tipo di problema da risolvere creando un modello predittivo. Automatizzato ML supporta tre tipi diversi di attività, tra cui la classificazione, la regressione e la previsione delle serie temporali.

Tipo di attività| Descrizione| Esempio
----|----|----
Classificazione | Attività per la stima della categoria di una determinata riga in un set di dati. | Rilevamento di frodi su una carta di credito. La colonna di destinazione verrà **rilevata illecitamente** con le categorie *true* o *false*. In questo caso, ogni riga dei dati verrà classificata come true o false.
regressione | Attività per la stima di un output di quantità continua. | Costo dell'automobile in base alle funzionalità, la colonna di destinazione è **Price**.
Previsione |Attività per l'esecuzione di stime informate per determinare la direzione delle tendenze future.| Previsione della domanda di energia per i prossimi 48 ore. La colonna di destinazione è **richiesta** e i valori stimati verrebbero usati per mostrare i modelli nella domanda di energia.

L'apprendimento automatico supporta i seguenti algoritmi durante l'automazione e il processo di ottimizzazione. Come utente, non è necessario specificare l'algoritmo.

Classificazione | regressione | Previsione delle serie temporali
-- |-- |--
[Regressione logistica](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)|[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)
[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC lineare](https://scikit-learn.org/stable/modules/svm.html#classification)|[Lasso LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Lasso LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificazione a vettori di supporto C (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificatore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regressore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificatore lineare DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Bayesiano naif](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Imposta il tipo di attività
È possibile impostare il tipo di attività per gli esperimenti di Machine Learning automatici con SDK o Azure Machine Learning Studio.

Usare il parametro `task` nel costruttore `AutoMLConfig` per specificare il tipo di esperimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

È possibile impostare l'attività nell'ambito della creazione automatica dell'esperimento di Machine Learning in Azure Machine Learning Studio. 

[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Selezione del tipo di attività](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Passaggi successivi

+ Scopri [di più su Machine](concept-automated-ml.md) learning in Azure Machine Learning.
+ Altre informazioni sulla [formazione automatica di un modello di previsione delle serie temporali](how-to-auto-train-forecast.md) in Azure Machine Learning
+ Provare l'esercitazione sulla [classificazione automatica Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .
+ Provare il notebook di esempio di [regressione automatizzata Machine Learning](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning/model-explanation) .

