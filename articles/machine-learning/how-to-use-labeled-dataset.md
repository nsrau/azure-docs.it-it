---
title: Creare ed esplorare DataSet con etichette
titleSuffix: Azure Machine Learning
description: Informazioni su come esportare etichette dati dal Azure Machine Learning l'assegnazione di etichette ai progetti e usarle per le attività di machine learning.
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
# <a name="create-and-explore-azure-machine-learning-dataset-with-labels"></a>Creare ed esplorare Azure Machine Learning set di dati con etichette

In questo articolo si apprenderà come esportare le etichette dati da un progetto di data label Azure Machine Learning e come caricarli in formati comuni, ad esempio, un dataframe Pandas per l'esplorazione dei dati o un set di dati Torchvision per la trasformazione delle immagini. 

## <a name="what-are-datasets-with-labels"></a>Che cosa sono i set di dati con etichette 

Azure Machine Learning set di impostazioni con etichette sono [TabularDatasets](how-to-create-register-datasets.md#dataset-types) con una proprietà Label, verranno indicati come set di impostazioni. Questi tipi specifici di TabularDatasets vengono creati solo come output dei progetti di assegnazione di etichette dei dati Azure Machine Learning. Creare un progetto di assegnazione di etichette dei dati con [questi passaggi](how-to-create-labeling-projects.md). Machine Learning supporta i progetti di etichettatura dei dati per la classificazione delle immagini, con più etichette o con più classi e con le caselle delimitate.

## <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://aka.ms/AMLFree) prima di iniziare.
* [SDK Azure Machine Learning per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)o accesso a [Azure Machine Learning Studio](https://ml.azure.com/).
    * Installare il pacchetto [Azure-contrib-DataSet](https://docs.microsoft.com/python/api/azureml-contrib-dataset/?view=azure-ml-py)
* Un'area di lavoro di Machine Learning. Vedere [Creare un'area di lavoro di Azure Machine Learning](how-to-manage-workspace.md).
* Accesso a un progetto di data label Azure Machine Learning. Se non si dispone di un progetto di assegnazione di etichette, crearne uno con [questi passaggi](how-to-create-labeling-projects.md).

## <a name="export-data-labels"></a>Esporta etichette dati 

Quando si completa un progetto di assegnazione di etichette dei dati, è possibile esportare i dati delle etichette da un progetto di assegnazione di etichette. In questo modo, è possibile acquisire il riferimento ai dati e alle relative etichette ed esportarli in [formato Coco](http://cocodataset.org/#format-data) o come set di dati Azure Machine Learning. Usare il pulsante **Esporta** nella pagina **Dettagli del progetto** del progetto di etichettatura.

### <a name="coco"></a>COCO 

 Il file COCO viene creato nell'archivio BLOB predefinito dell'area di lavoro di Azure Machine Learning in una cartella all'interno di *export/coco*. 

### <a name="azure-machine-learning-dataset"></a>Set di dati di Azure Machine Learning

È possibile accedere al set di dati esportato Azure Machine Learning nella sezione **set** di dati di Azure Machine Learning Studio. La pagina dei dettagli del set di **dati** fornisce anche codice di esempio per accedere alle etichette da Python.

![Set di dati esportato](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="explore-labeled-datasets"></a>Esplorare i set di impostazioni con etichetta

Caricare i set di dati con etichetta in un set di dati Pandas o Torchvision per sfruttare le librerie open source più diffuse per l'esplorazione dei dati, nonché le librerie fornite da PyTorch per la trasformazione e la formazione di immagini.

### <a name="pandas-dataframe"></a>Dataframe Pandas

È possibile caricare i set di impostazioni con etichetta in un dataframe di [`to_pandas_dataframe()`](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py#to-pandas-dataframe-on-error--null---out-of-range-datetime--null--) Pandas con `azureml-contrib-dataset` il metodo della classe. Installare la classe con il comando della Shell seguente: 

```shell
pip install azureml-contrib-dataset
```

>[!NOTE]
>Lo spazio dei nomi azureml. contrib viene modificato di frequente, in quanto è possibile migliorare il servizio. Qualsiasi elemento in questo spazio dei nomi deve essere pertanto considerato come anteprima e non è completamente supportato da Microsoft.

Sono disponibili le opzioni di gestione dei file seguenti per i flussi di file durante la conversione in un dataframe Pandas.
* Download: scaricare i file di dati in un percorso locale.
* Montaggio: montare i file di dati in un punto di montaggio. Il montaggio funziona solo per le risorse di calcolo basate su Linux, tra cui Azure Machine Learning VM notebook e Azure Machine Learning calcolo.

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

### <a name="torchvision-datasets"></a>Set di impostazioni Torchvision

È possibile caricare set di dati con etichetta nel set di dati Torchvision con il metodo [to_torchvision ()](https://docs.microsoft.com/python/api/azureml-contrib-dataset/azureml.contrib.dataset.tabulardataset?view=azure-ml-py#to-torchvision--) anche dalla `azureml-contrib-dataset` classe. Per usare questo metodo, è necessario che [PyTorch](https://pytorch.org/) sia installato. 

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

* Vedere il [set di dati con le etichette notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/work-with-data/datasets-tutorial/labeled-datasets/labeled-datasets.ipynb) per l'esempio di training completo.
