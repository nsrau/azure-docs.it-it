---
title: Configurare la convalida incrociata e le divisioni di dati negli esperimenti di Machine Learning automatici
titleSuffix: Azure Machine Learning
description: Informazioni su come configurare la convalida incrociata e le divisioni dei set di dati per esperimenti automatici di Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: ed11a1b772acb31268f3d0a61fba10301ad62e18
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93320474"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Configurare la suddivisione dei dati e la convalida trasversale in Machine Learning automatizzato

Questo articolo illustra le diverse opzioni per la configurazione di suddivisione dei dati di Training/convalida e la convalida incrociata per gli esperimenti automatici di Machine Learning, AutoML.

In Azure Machine Learning, quando si usa AutoML per compilare più modelli ML, ogni esecuzione figlio deve convalidare il modello correlato calcolando la metrica di qualità per tale modello, ad esempio l'accuratezza o l'AUC ponderata. Queste metriche vengono calcolate confrontando le stime effettuate con ogni modello con etichette reali dalle osservazioni precedenti nei dati di convalida. 

Gli esperimenti AutoML eseguono automaticamente la convalida del modello. Le sezioni seguenti descrivono come è possibile personalizzare ulteriormente le impostazioni di convalida con [Azure Machine Learning Python SDK](/python/api/overview/azure/ml/?preserve-view=true&view=azure-ml-py). 

Per un'esperienza di basso livello o senza codice, vedere [creare esperimenti automatici di Machine Learning in Azure Machine Learning Studio](how-to-use-automated-ml-for-ml-models.md). 

> [!NOTE]
> Lo Studio supporta attualmente la suddivisione dei dati di Training/convalida e le opzioni di convalida incrociata, ma non supporta la specifica di singoli file di dati per il set di convalida. 

## <a name="prerequisites"></a>Prerequisiti

Per questo articolo è necessario,

* Un'area di lavoro di Azure Machine Learning. Per creare l'area di lavoro, vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).

* Familiarità con la configurazione di un esperimento di Machine Learning automatizzato con il Azure Machine Learning SDK. Seguire l' [esercitazione](tutorial-auto-train-models.md) o le [procedure](how-to-configure-auto-train.md) per visualizzare i modelli di progettazione dell'esperimento automatizzato di machine learning.

* Una comprensione dei dati di convalida incrociata e di Training/convalida si suddivide come concetti ML. Per una spiegazione di alto livello,

    * [Informazioni sui set di training, convalida e test in Machine Learning](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Informazioni sulla convalida incrociata](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd)

## <a name="default--data-splits-and-cross-validation"></a>Suddivisione dei dati predefinita e convalida incrociata

Usare l'oggetto [AutoMLConfig](/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?preserve-view=true&view=azure-ml-py) per definire le impostazioni dell'esperimento e del training. Nel frammento di codice seguente si noti che sono definiti solo i parametri obbligatori, ovvero i parametri `n_cross_validation` per `validation_ data` o **non** sono inclusi.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Se non si specifica in modo esplicito un `validation_data` `n_cross_validation` parametro o, AutoML applica le tecniche predefinite in base al numero di righe nel set di dati singolo `training_data` fornito:

|&nbsp;Dimensioni dati di training &nbsp;| Tecnica di convalida |
|---|-----|
|**Maggiore &nbsp; di &nbsp; 20.000 &nbsp; righe**| Viene applicata la suddivisione dei dati di Training/convalida. Il valore predefinito prevede il 10% del set di dati di training iniziale come set di convalida. A sua volta, il set di convalida viene usato per il calcolo delle metriche.
|**Inferiore &nbsp; a &nbsp; 20.000 &nbsp; righe**| Viene applicato l'approccio per la convalida incrociata. Il numero predefinito di riduzioni dipende dal numero di righe. <br> **Se il set di dati è inferiore a 1.000 righe** , vengono utilizzate 10 riduzioni. <br> **Se le righe sono comprese tra 1.000 e 20.000** , vengono usate tre riduzioni.

## <a name="provide-validation-data"></a>Fornire dati di convalida

In questo caso, è possibile iniziare con un singolo file di dati e suddividerlo in set di training e di convalida oppure è possibile fornire un file di dati separato per il set di convalida. In entrambi i casi, il `validation_data` parametro nell' `AutoMLConfig` oggetto assegna i dati da utilizzare come set di convalida. Questo parametro accetta solo i set di dati sotto forma di un set di dati [Azure Machine Learning](how-to-create-register-datasets.md) o di un dataframe Pandas.   

Nell'esempio di codice seguente viene definita in modo esplicito la parte dei dati forniti in `dataset` da utilizzare per il training e la convalida.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Specificare le dimensioni del set di convalida

In questo caso, per l'esperimento viene fornito solo un singolo set di dati. Ovvero il `validation_data` parametro **non** viene specificato e il set di dati fornito viene assegnato al  `training_data` parametro.  Nell' `AutoMLConfig` oggetto è possibile impostare il parametro in `validation_size` modo da mantenere una parte dei dati di training per la convalida. Ciò significa che il set di convalida verrà suddiviso da AutoML dall'oggetto `training_data` fornito inizialmente. Questo valore deve essere compreso tra 0,0 e 1,0 non inclusivo (ad esempio, 0,2 significa che il 20% dei dati viene mantenuto per i dati di convalida).

Vedere l'esempio di codice seguente:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="set-the-number-of-cross-validations"></a>Impostare il numero di convalide incrociate

Per eseguire la convalida incrociata, includere il `n_cross_validations` parametro e impostarlo su un valore. Questo parametro consente di impostare il numero di convalide incrociate da eseguire, in base allo stesso numero di riduzioni.

Nel codice seguente sono definite cinque riduzioni per la convalida incrociata. Di conseguenza, cinque corsi di formazione diversi, ogni training che usa 4/5 dei dati e ogni convalida che usa 1/5 dei dati con una piegatura diversa per ogni volta.

Di conseguenza, le metriche vengono calcolate con la media delle 5 metriche di convalida.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Specifica riduzioni dati personalizzate per la convalida incrociata

È anche possibile fornire riduzioni di dati di convalida incrociata (CV). Questo scenario è considerato uno scenario più avanzato perché si specificano le colonne da dividere e utilizzare per la convalida.  Includere colonne personalizzate con suddivisione CV nei dati di training e specificare le colonne popolando i nomi di colonna nel `cv_split_column_names` parametro. Ogni colonna rappresenta una suddivisione della convalida incrociata e viene riempita con i valori integer 1 o 0, dove 1 indica che la riga deve essere utilizzata per il training e 0 indica che la riga deve essere utilizzata per la convalida.

Il frammento di codice seguente contiene dati di marketing Bank con due colonne di divisione CV ' CV1' è CV2'.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Per usare `cv_split_column_names` con `training_data` e `label_column_name` , aggiornare il Azure Machine Learning Python SDK versione 1.6.0 o successiva. Per le versioni precedenti dell'SDK, fare riferimento a utilizzando `cv_splits_indices` , ma si noti che viene utilizzato solo con l' `X` input del `y` set di dati e. 

## <a name="next-steps"></a>Passaggi successivi

* [Prevenire i dati sbilanciati e l'overfitting](concept-manage-ml-pitfalls.md).
* [Esercitazione: usare Machine Learning automatico per stimare le tariffe taxi-sezione dati suddivisi](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* Come eseguire [il training automatico di un modello di previsione delle serie temporali](how-to-auto-train-forecast.md).