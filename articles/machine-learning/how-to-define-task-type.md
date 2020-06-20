---
title: Definire un'attività di Machine Learning per un'esecuzione di Machine Learning automatizzato
titleSuffix: Azure Machine Learning
description: Informazioni su come definire un'attività di Machine Learning per un'esecuzione di Machine Learning automatizzato
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: cb2fbcda7f30db24b876a66df22071f14df74814
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653243"
---
# <a name="how-to-define-a-machine-learning-task"></a>Come definire un'attività di Machine Learning 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo vengono illustrate le attività di Machine Learning supportate e viene spiegato come definirle per l'esecuzione di un esperimento di Machine Learning automatizzato.


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a> Che cos'è un'attività di Machine Learning?

Un'attività di Machine Learning rappresenta il tipo di problema da risolvere creando un modello predittivo. Il processo di Machine Learning automatizzato supporta tre tipi diversi di attività, tra cui la classificazione, la regressione e la previsione delle serie temporali.

Tipo di attività| Descrizione| Esempio
----|----|----
Classificazione | Attività per la stima della categoria di una determinata riga in un set di dati. | Rilevamento di frodi su una carta di credito. La colonna di destinazione potrebbe essere **Frode rilevata** con le categorie *True* e *False*. In questo caso, ogni riga del set di dati viene classificata come True o False.
Regressione | Attività per la stima di un output di quantità continuo. | Costo di un'automobile in base alle sue caratteristiche. La colonna di destinazione potrebbe essere **prezzo**.
Previsione |Attività per l'esecuzione di stime informate per determinare la direzione delle tendenze future.| Previsione della domanda di energia per le prossime 48 ore. La colonna di destinazione potrebbe essere **domanda** e i valori stimati verrebbero usati per rivelare i modelli nella domanda di energia.

L'apprendimento automatico supporta i seguenti algoritmi durante l'automazione e il processo di ottimizzazione. Come utente, non è necessario specificare l'algoritmo.

Classificazione | Regressione | Previsione di serie temporali
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
[DNN Classifier](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [DNN Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[DNN Linear Classifier](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Linear Regressor](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Bayesiano naif](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Impostare il tipo di attività
È possibile impostare il tipo di attività per gli esperimenti di Machine Learning automatizzato con l'SDK o Azure Machine Learning Studio.

Usare il parametro `task` nel costruttore `AutoMLConfig` per specificare il tipo di esperimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

È possibile impostare l'attività durante la creazione dell'esecuzione dell'esperimento di Machine Learning automatizzato in Azure Machine Learning Studio. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Selezione del tipo di attività](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Passaggi successivi

+ Altre informazioni sulle funzionalità di [Machine Learning automatizzato](concept-automated-ml.md) in Azure Machine Learning.
+ Altre informazioni sul [training automatico di un modello di previsione di serie temporali](how-to-auto-train-forecast.md) in Azure Machine Learning.
+ Provare l'esercitazione sulla [classificazione con il processo di Machine Learning automatizzato](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb).
+ Provare il notebook di esempio sulla [regressione con Machine Learning automatizzato](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-explanation-featurization/auto-ml-regression-explanation-featurization.ipynb).

