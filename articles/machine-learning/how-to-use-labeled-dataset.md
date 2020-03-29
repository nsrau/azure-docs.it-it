---
title: Creare ed esplorare set di dati con etichette
titleSuffix: Azure Machine Learning
description: Informazioni su come esportare le etichette dati dai progetti di etichettatura di Azure Machine Learning e usarle per le attività di apprendimento automatico.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.topic: how-to
ms.date: 01/21/2020
ms.openlocfilehash: 5138109de3f80d405ce95b605714b511480563f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76549489"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Creare ed esplorare il set di dati di Azure Machine Learning con le etichetteCreate and explore Azure Machine Learning dataset with labels

In questo articolo imparerai a esportare le etichette dati da un progetto di etichettatura dei dati di Azure Machine Learning e a caricarle in formati comuni, ad esempio un frame di dati panda per l'esplorazione dei dati o un set di dati Torchvision per la trasformazione delle immagini. 

## <a name="what-are-datasets-with-labels"></a>Che cosa sono i set di dati con le etichette 

I set di dati di Azure Machine Learning con etichette sono [TabularDatasets](how-to-create-register-datasets.md#dataset-types) con una proprietà label, come set di dati con etichetta. Questi tipi specifici di TabularDatasets vengono creati solo come output dei progetti di etichettatura dei dati di Azure Machine Learning.These specific types of TabularDatasets are only created as an output of Azure Machine Learning data labeling projects. Creare un progetto di etichettatura dei dati con [questi passaggi.](how-to-create-labeling-projects.md) Machine Learning supporta progetti di etichettatura dei dati per la classificazione delle immagini, con più etichette o multiclasse, e l'identificazione degli oggetti insieme a caselle delimitate.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare.
* [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o accesso allo studio di Azure Machine [Learning.](https://ml.azure.com/)
    * Installare il pacchetto [azure-contrib-datasetInstall the azure-contrib-dataset](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py) package
* Un'area di lavoro di Machine Learning. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).
* Accesso a un progetto di etichettatura dei dati di Azure Machine Learning.Access to an Azure Machine Learning data labeling project. Se non si dispone di un progetto di etichettatura, crearne uno con [questi passaggi.](how-to-create-labeling-projects.md)

## <a name="export-data-labels"></a>Esportare le etichette dati 

Quando si completa un progetto di etichettatura dei dati, è possibile esportare i dati dell'etichetta da un progetto di etichetta. In questo modo, consente di acquisire sia il riferimento ai dati che le relative etichette ed esportarli in formato COCO o come set di dati di Azure Machine Learning.Doing so, allows you to capture both the reference to the data and its labels, and export them in [COCO format](http://cocodataset.org/#format-data) or as an Azure Machine Learning dataset. Usare il pulsante **Esporta** nella pagina **Dettagli del progetto** del progetto di etichettatura.

### <a name="coco"></a>Coco 

 Il file COCO viene creato nell'archivio BLOB predefinito dell'area di lavoro di Azure Machine Learning in una cartella all'interno di *export/coco*. 

### <a name="azure-machine-learning-dataset"></a>Set di dati di Azure Machine Learning

È possibile accedere al set di dati di Azure Machine Learning esportato nella sezione Set di dati di Azure Machine Learning Studio.You can access the exported Azure Machine Learning dataset in the **Datasets** section of your Azure Machine Learning studio. La pagina Dettagli set di dati fornisce anche codice di esempio per accedere alle etichette da Python.The dataset **Details** page also provides sample code to access your labels from Python.

![Set di dati esportato](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Esplorare i set di dati con etichettaExplore labeled datasets

Caricare i set di dati etichettati in un frame di dati pandas o in un set di dati Torchvision per sfruttare le librerie open source più diffusi per l'esplorazione dei dati, nonché PyTorch ha fornito librerie per la trasformazione e il training delle immagini.

### <a name="pandas-dataframe"></a>Dataframe Pandas

È possibile caricare i set di dati con [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) etichetta in `azureml-contrib-dataset` un frame di dati pandas con il metodo della classe . Installare la classe con il seguente comando della shell: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Lo spazio dei nomi azureml.contrib cambia frequentemente, mentre si lavora per migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere pertanto considerato come anteprima e non è completamente supportato da Microsoft.

Offriamo le seguenti opzioni di gestione dei file per i flussi di file durante la conversione in un frame dati pandas.
* Download: scaricare i file di dati in un percorso locale.
* Montaggio: montare i file di dati su un punto di montaggio. Mount funziona solo per il calcolo basato su Linux, tra cui la macchina virtuale del notebook di Azure Machine Learning e Azure Machine Learning Compute.

```Python
import azureml.contrib.dataset
from azureml.contrib.dataset import FileHandlingOption
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Set di dati Torchvision

È possibile caricare set di dati etichettati nel set `azureml-contrib-dataset` di dati Torchvision con il metodo [to_torchvision()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) anche dalla classe. Per utilizzare questo metodo, è necessario disporre di [PyTorch](https://pytorch.org/) installato. 

```python
from torchvision.transforms import functional as F

# load animal_labels dataset into torchvision dataset
pytorch_dataset = animal_labels.to_torchvision()
img = pytorch_dataset[0][0]
print(type(img))

# use methods from torchvision to transform the img into grayscale
pil_image = F.to_pil_image(img)
gray_image = F.to_grayscale(pil_image, num_output_channels=3)

imgplot = plt.imshow(gray_image)
```

## <a name="next-steps"></a>Passaggi successivi

* Vedere il [set di dati con etichette notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) per un esempio di formazione completo.
