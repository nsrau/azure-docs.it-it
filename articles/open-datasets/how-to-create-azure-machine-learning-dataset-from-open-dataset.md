---
title: Creare set di impostazioni con i set di impostazioni di Azure Open
titleSuffix: Azure Open Datasets
description: Informazioni su come creare un set di dati Azure Machine Learning dai set di dati aperti di Azure.
ms.service: open-datasets
ms.topic: conceptual
ms.author: nibaccam
author: nibaccam
ms.date: 08/05/2020
ms.custom: how-to, tracking-python
ms.openlocfilehash: c90d11ba630dbb1e37054715855ae5547a8a034b
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902719"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Creare set di impostazioni di Azure Machine Learning da Azure Open DataSet

Questo articolo illustra come inserire dati di arricchimento curati negli esperimenti di Machine Learning locali o remoti con set di dati [Azure Machine Learning](../machine-learning/overview-what-is-azure-ml.md) e set di dati [aperti di Azure](https://docs.microsoft.com/azure/open-datasets/). 

Creando un set di dati [Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md), viene creato un riferimento al percorso dell'origine dati, insieme a una copia dei relativi metadati. Poiché i set di dati vengono valutati in modo differito e i dati rimangono nella posizione esistente,
* Non sostenere costi di archiviazione aggiuntivi.
* Non è rischioso modificare involontariamente le origini dati originali. 
* Migliorare le velocità di prestazioni del flusso di lavoro ML.

Per informazioni sul modo in cui i set di dati si adattano al flusso di lavoro di accesso ai dati complessivo di Azure Machine Learning, vedere l'articolo relativo ai [dati](../machine-learning/concept-data.md#data-workflow) di

I set di impostazioni di Azure aprono i set di informazioni pubblici che è possibile usare per aggiungere funzionalità specifiche dello scenario per arricchire le soluzioni predittive e migliorarne la precisione. Vedere il [Catalogo dei set](https://azure.microsoft.com/en-in/services/open-datasets/catalog/) di dati aperti per i dati di dominio pubblico che consentono di eseguire il training di modelli di Machine Learning, ad esempio:

* [Meteo](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/)
* [Census](https://azure.microsoft.com/services/open-datasets/catalog/us-decennial-census-zip/)
* [Vacanze](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/)
* [sicurezza pubblica](https://azure.microsoft.com/services/open-datasets/catalog/chicago-safety-data/)
* posizione

I set di impostazioni aperti si trovano nel cloud in Microsoft Azure e sono inclusi sia nell' [SDK Azure Machine Learning Python](#create-datasets-with-the-sdk) che in [Azure Machine Learning Studio](#create-datasets-with-the-studio).


## <a name="prerequisites"></a>Prerequisiti

Per questo articolo sono necessari:

* Una sottoscrizione di Azure. Se non se ne ha una, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* [Area di lavoro Azure Machine Learning](../machine-learning/how-to-manage-workspace.md).

* [SDK Azure Machine Learning per Python installato](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), che include il `azureml-datasets` pacchetto.

    * Creare un' [istanza di calcolo di Azure Machine Learning](../machine-learning/concept-compute-instance.md#managing-a-compute-instance), ovvero un ambiente di sviluppo completamente configurato e gestito che includa notebook integrati e SDK già installato.

    **OR**

    * Lavorare sul proprio ambiente Python e installare l'SDK manualmente con [queste istruzioni](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Alcune classi del set di dati presentano dipendenze dal pacchetto [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) , che è compatibile solo con Python a 64 bit. Per gli utenti Linux queste classi sono supportate solo nelle distribuzioni seguenti: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) e CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Creare set di impostazioni con l'SDK

Per creare Azure Machine Learning set di DataSet tramite le classi di Azure Open DataSets in Python SDK, verificare di aver installato il pacchetto con `pip install azureml-opendatasets` . Ogni set di dati discreto è rappresentato dalla propria classe nell'SDK e alcune classi sono disponibili come Azure Machine Learning [ `TabularDataset` , `FileDataset` ](../machine-learning/how-to-create-register-datasets.md#dataset-types)o entrambi. Per un elenco completo delle classi, vedere la [documentazione di riferimento](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) `opendatasets` .

È possibile recuperare determinate `opendatasets` classi come `TabularDataset` o `FileDataset` , che consente di modificare e/o scaricare direttamente i file. Altre classi possono ottenere un set di dati **solo** usando le `get_tabular_dataset()` funzioni o della `get_file_dataset()` `Dataset` classe in Python SDK.

Il codice seguente mostra che la `opendatasets` classe MNIST può restituire un oggetto `TabularDataset` o `FileDataset` . 


```python
from azureml.core import Dataset
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()
```

In questo esempio, la `opendatasets` classe diabete è disponibile solo come `TabularDataset` , di conseguenza l'uso di `get_tabular_dataset()` .

```python

from azureml.opendatasets import Diabetes
from azureml.core import Dataset

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```
## <a name="register-datasets"></a>Registrare i set di impostazioni

Registrare un set di dati di Azure Machine Learning con l'area di lavoro, in modo che sia possibile condividerli con altri utenti e riutilizzarli tra gli esperimenti nell'area di lavoro. Quando si registra un set di dati Azure Machine Learning creato da set di dati aperti, non vengono scaricati immediatamente dati, ma i dati saranno accessibili in un secondo momento, quando richiesto (durante il training, ad esempio) da una posizione di archiviazione centrale.

Per registrare i set di impostazioni con un'area di lavoro, usare il [`register()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.abstract_dataset.abstractdataset?view=azure-ml-py#register-workspace--name--description-none--tags-none--create-new-version-false-) metodo. 
```Python
titanic_ds = titanic_ds.register(workspace=workspace,
                                 name='titanic_ds',
                                 description='titanic training data')
```

## <a name="create-datasets-with-the-studio"></a>Creare set di impostazioni con studio

È anche possibile creare set di impostazioni di Azure Machine Learning da Azure Open DataSets con [Azure Machine Learning Studio](https://ml.azure.com), un'interfaccia Web consolidata che include gli strumenti di machine learning per eseguire Data Science scenari per Data Science professionisti di tutti i livelli di competenza.

> [!Note]
> I set di impostazioni creati tramite Azure Machine Learning Studio vengono registrati automaticamente nell'area di lavoro.

1. Nell'area di lavoro selezionare la scheda **set** di **elementi in asset**. Nel menu a discesa **Crea set di dati** selezionare **da set di dati aperti**.

    ![Aprire il set di dati con l'interfaccia utente](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

1. Selezionare un set di dati selezionando il relativo riquadro. È possibile filtrare usando la barra di ricerca. Selezionare **Avanti**.

    ![Scegliere il set di dati](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

1. Scegliere un nome con cui registrare il set di dati e, facoltativamente, filtrare i dati usando i filtri disponibili. In questo caso, per il set di dati **Public Holidays** è possibile filtrare il periodo di tempo a un anno e il codice paese solo negli Stati Uniti. Vedere il [Catalogo dei set](https://azure.microsoft.com/services/open-datasets/catalog) di dati aperti di Azure per informazioni dettagliate sui dati, ad esempio le descrizioni dei campi e gli intervalli di date. Selezionare **Crea**.

    ![Imposta parametri set di dati e crea set di dati](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

    Il set di dati è ora disponibile nell'area di lavoro in **set**di dati. È possibile usarlo nello stesso modo degli altri set di impostazioni creati.


## <a name="access-datasets-for-your-experiments"></a>Accedere ai set di impostazioni per gli esperimenti

Usare i set di impostazioni negli esperimenti di machine learning per il training dei modelli ML. [Vedere altre informazioni su come eseguire il training con i set di dati](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Notebook di esempio

Per esempi e dimostrazioni della funzionalità Open DataSets, vedere questi [notebook di esempio](samples.md).

## <a name="next-steps"></a>Passaggi successivi

* Eseguire [il training del primo modello ml](../machine-learning/tutorial-1st-experiment-sdk-train.md).

* Eseguire il [training con i set di impostazioni](../machine-learning/how-to-train-with-datasets.md).

* [Creare un set di dati di Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md).



