---
title: Definire un'attività di apprendimento automatico per un'esecuzione automatica dell'apprendimento automaticoDefine a machine learning task for an automated machine learning run
titleSuffix: Azure Machine Learning
description: Informazioni su come definire un'attività di apprendimento automatico per un'esecuzione automatica dell'apprendimento automatico
services: machine-learning
author: RachelKellam
ms.author: rakellam
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.date: 11/04/2019
ms.openlocfilehash: fef3f0347d963b8a7d5fe2f1ca771293e9c3d2a9
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80475494"
---
# <a name="how-to-define-a-machine-learning-task"></a>Come definire un'attività di apprendimento automaticoHow to define a machine learning task 
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo vengono apprese le attività di apprendimento automatico supportate e viene illustrato come definirle per l'esecuzione di un esperimento di Machine Learning automatizzato (ML automatizzato).


## <a name="what-is-a-machine-learning-task"></a><a name="What is a machine learning task?"></a>Che cos'è un'attività di apprendimento automatico?

Un'attività di apprendimento automatico rappresenta il tipo di problema da risolvere creando un modello predittivo. L'area di configurazione automatica degli stati uniti supporta tre diversi tipi di attività, tra cui classificazione, regressione e previsione di serie temporali.

Tipo di attività| Descrizione| Esempio
----|----|----
Classificazione | Attività per la stima della categoria di una determinata riga in un set di dati. | Rilevamento di frodi su carta di credito. La colonna di destinazione sarebbe **Frode rilevata** con categorie *True* o *False*. In questo caso, stiamo classificando ogni riga nei dati come true o false.
Regressione | Attività per la previsione di un output di quantità continua. | Costo dell'automobile in base alle sue caratteristiche, la colonna di destinazione sarebbe **prezzo**.
Previsione |Compito di effettuare stime informate nel determinare la direzione delle tendenze future.| Prevedere la domanda di energia per loro prossime 48 ore. La colonna di destinazione sarebbe **la domanda** e i valori previsti verrebbero utilizzati per mostrare i modelli nella domanda di energia.

L'apprendimento automatico supporta i seguenti algoritmi durante l'automazione e il processo di ottimizzazione. Come utente, non è necessario specificare l'algoritmo.

Classificazione | Regressione | Previsione di una serie temporale
-- |-- |--
[Logistic Regression](https://scikit-learn.org/stable/modules/linear_model.html#logistic-regression)| [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)| [Rete elastica](https://scikit-learn.org/stable/modules/linear_model.html#elastic-net)
[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)|[Light GBM](https://lightgbm.readthedocs.io/en/latest/index.html)
[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#classification)|[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)|[Gradient boosting](https://scikit-learn.org/stable/modules/ensemble.html#regression)
[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#decision-trees)|[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)|[Albero delle decisioni](https://scikit-learn.org/stable/modules/tree.html#regression)
[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)|[K vicini più prossimi](https://scikit-learn.org/stable/modules/neighbors.html#nearest-neighbors-regression)
[SVC lineare](https://scikit-learn.org/stable/modules/svm.html#classification)|[Lasso LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)|[Lasso LARS](https://scikit-learn.org/stable/modules/linear_model.html#lars-lasso)
[Classificazione a vettori di supporto C (SVC)](https://scikit-learn.org/stable/modules/svm.html#classification)|[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)|[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#regression)
[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)|[Foresta casuale](https://scikit-learn.org/stable/modules/ensemble.html#random-forests)
[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)|[Alberi estremamente casuali](https://scikit-learn.org/stable/modules/ensemble.html#extremely-randomized-trees)
[Xgboost (Xgboost)](https://xgboost.readthedocs.io/en/latest/parameter.html)|[Xgboost (Xgboost)](https://xgboost.readthedocs.io/en/latest/parameter.html)| [Xgboost (Xgboost)](https://xgboost.readthedocs.io/en/latest/parameter.html)
[Classificatore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNClassifier)|[Regressore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor) | [Regressore DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/DNNRegressor)|
[Classificatore lineare DNN](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearClassifier)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)|[Regressore lineare](https://www.tensorflow.org/api_docs/python/tf/estimator/LinearRegressor)
[Bayesiano naif](https://scikit-learn.org/stable/modules/naive_bayes.html#bernoulli-naive-bayes)||
[Discesa stocastica del gradiente (SGD)](https://scikit-learn.org/stable/modules/sgd.html#sgd)||


### <a name="set-the-task-type"></a>Impostare il tipo di attività
È possibile impostare il tipo di attività per gli esperimenti di Machine Learning automatizzati con l'SDK o lo studio di Azure Machine Learning.You can set the task type for your automated ML experiments with the SDK or the Azure Machine Learning studio.

Utilizzare `task` il parametro nel `AutoMLConfig` costruttore per specificare il tipo di esperimento.

```python
from azureml.train.automl import AutoMLConfig

# task can be one of classification, regression, forecasting
automl_config = AutoMLConfig(task="classification")
```

È possibile impostare l'attività come parte della creazione dell'esecuzione automatica dell'esperimento ML in Azure Machine Learning Studio.You can set the task as part of your automated ML experiment run creation in the Azure Machine Learning studio. 

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-enterprise-sku-inline.md)]

![Selezione del tipo di attività](./media/how-to-define-task-type/task-type.png)


## <a name="next-steps"></a>Passaggi successivi

+ Altre informazioni su ml automatizzato in Azure Machine Learning.Learn more about [automated ml](concept-automated-ml.md) in Azure Machine Learning.
+ Altre informazioni sul [training automatico di un modello di previsione di serie temporali](how-to-auto-train-forecast.md) in Azure Machine LearningLearn more about auto-training a time-series forecast model in Azure Machine Learning
+ Provare [l'esercitazione sulla classificazione automatica](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/classification-credit-card-fraud/auto-ml-classification-credit-card-fraud.ipynb) di Machine Learning.
+ Provare il blocco appunti di esempio [di regressione automatica dell'apprendimento automatico.](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/regression-hardware-performance-explanation-and-featurization/auto-ml-regression-hardware-performance-explanation-and-featurization.ipynb)

