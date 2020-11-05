---
title: Creare ed esplorare set di dati con etichette
titleSuffix: Azure Machine Learning
description: Informazioni su come esportare le etichette dati dai progetti di etichettatura di Azure Machine Learning e usarle per le attività di apprendimento automatico.
author: nibaccam
ms.author: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to, data4ml
ms.date: 05/14/2020
ms.openlocfilehash: 05535f47b212c3f72256dd8b2b8e3923c309a08c
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93356366"
---
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Creare ed esplorare il set di dati di Azure Machine Learning con etichette

In questo articolo si apprenderà come esportare le etichette dati da un progetto di etichettatura dei data di Azure Machine Learning e come caricarle in formati comuni come un dataframe Pandas per l'esplorazione dei dati o un set di dati di Torchvision per la trasformazione delle immagini. 

## <a name="what-are-datasets-with-labels"></a>Informazioni sui set di dati con etichette 

Si fa riferimento a Azure Machine Learning set di impostazioni con etichette come DataSet con etichetta. Questi tipi di set di dati specifici di set di dati con etichetta vengono creati solo come output dei progetti di assegnazione di etichette di dati Azure Machine Learning. Creare un progetto di etichettatura dei dati seguendo [questa procedura](how-to-create-labeling-projects.md). Machine Learning supporta i progetti di etichettatura dei dati per la classificazione di immagini, multi-etichetta o multi-classe, e l'identificazione di oggetti con i riquadri di selezione.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare.
* [Azure Machine Learning SDK per Python](/python/api/overview/azure/ml/intro?preserve-view=true&view=azure-ml-py) o accesso ad [Azure Machine Learning Studio](https://ml.azure.com/).
    * Installare il pacchetto [azure-contrib-dataset](/python/api/azureml-contrib-dataset/?preserve-view=true&view=azure-ml-py)
* Un'area di lavoro di Machine Learning. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).
* Accedere a un progetto di etichettatura dei dati di Azure Machine Learning. Se non è disponibile un progetto di etichettatura, crearne uno seguendo [questi passaggi](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Esportare le etichette dati 

Quando si completa un progetto di etichettatura dei dati, è possibile esportare i dati delle etichette da un progetto di etichettatura. Ciò consente di acquisire sia il riferimento ai dati che le relative etichette e di esportarli in [formato COCO](http://cocodataset.org/#format-data) o come set di dati di Azure Machine Learning. Usare il pulsante **Esporta** nella pagina **Dettagli del progetto** del progetto di etichettatura.

### <a name="coco"></a>COCO 

 Il file COCO viene creato nell'archivio BLOB predefinito dell'area di lavoro di Azure Machine Learning in una cartella all'interno di *export/coco*. 

### <a name="azure-machine-learning-dataset"></a>Set di dati di Azure Machine Learning

È possibile accedere al set di dati di Azure Machine Learning esportato nella sezione **Set di dati** di Azure Machine Learning Studio. La pagina **Dettagli** del set di dati contiene anche il codice di esempio per accedere alle etichette da Python.

![Set di dati esportato](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Esplorare i set di dati con etichetta

Caricare i set di dati con etichetta in un dataframe Pandas o un set di dati di Torchvision per sfruttare le librerie open source più diffuse per l'esplorazione dei dati, nonché le librerie di PyTorch per la trasformazione e il training delle immagini.

### <a name="pandas-dataframe"></a>Dataframe Pandas

È possibile caricare i set di dati con etichetta in un dataframe Pandas con il metodo [`to_pandas_dataframe()`](/python/api/azureml-core/azureml.data.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueto-pandas-dataframe-on-error--null---out-of-range-datetime--null--) della classe `azureml-contrib-dataset`. Installare la classe con il seguente comando della shell: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Lo spazio dei nomi azureml.contrib cambia di frequente perché Microsoft è attualmente impegnata a migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere pertanto considerato come anteprima e non è completamente supportato da Microsoft.

Azure Machine Learning offre le seguenti opzioni di gestione dei file per i flussi di file durante la conversione in un dataframe Pandas.
* Download: Scaricare i file di dati in un percorso locale.
* Montaggio: Montare i file di dati in un punto di montaggio. Il montaggio funziona solo per un ambiente di calcolo basato su Linux, ad esempio una macchina virtuale con notebook di Azure Machine Learning e l'ambiente di calcolo di Azure Machine Learning.

Nel codice seguente, il `animal_labels` set di dati è l'output di un progetto di assegnazione di etichette precedentemente salvato nell'area di lavoro.

```Python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')
animal_pd = animal_labels.to_pandas_dataframe(file_handling_option=FileHandlingOption.DOWNLOAD, target_path='./download/', overwrite_download=True)

import matplotlib.pyplot as plt
import matplotlib.image as mpimg

#read images from downloaded path
img = mpimg.imread(animal_pd.loc[0,'image_url'])
imgplot = plt.imshow(img)
```

### <a name="torchvision-datasets"></a>Set di dati di Torchvision

È possibile caricare set di dati con etichetta nel set di dati di Torchvision con il metodo [to_torchvision()](/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?preserve-view=true&view=azure-ml-py#&preserve-view=trueto-torchvision--) anche dalla classe `azureml-contrib-dataset`. Per usare questo metodo, è necessario aver installato [PyTorch](https://pytorch.org/). 

Nel codice seguente, il `animal_labels` set di dati è l'output di un progetto di assegnazione di etichette precedentemente salvato nell'area di lavoro.

```python
import azureml.core
import azureml.contrib.dataset
from azureml.core import Dataset, Workspace
from azureml.contrib.dataset import FileHandlingOption

from torchvision.transforms import functional as F

# get animal_labels dataset from the workspace
animal_labels = Dataset.get_by_name(workspace, 'animal_labels')

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

* Vedere il [notebook dei set di dati con etichetta](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) per un esempio completo di training.