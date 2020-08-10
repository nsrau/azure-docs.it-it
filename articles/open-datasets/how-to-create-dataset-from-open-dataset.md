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
ms.openlocfilehash: db2e493f04d74308b21e8a63222f26b1e2af8546
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/10/2020
ms.locfileid: "88038238"
---
# <a name="create-azure-machine-learning-datasets-from-azure-open-datasets"></a>Creare set di impostazioni di Azure Machine Learning da Azure Open DataSet
[!INCLUDE [aml-applies-to-basic-enterprise-sku](../../includes/aml-applies-to-basic-enterprise-sku.md)]

In questo articolo si apprenderà come creare un set di dati Azure Machine Learning dai set di dati [Open di Azure](https://azure.microsoft.com/services/open-datasets/) per accedere ai dati per gli esperimenti locali o remoti. 

Creando un set di dati [Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md), viene creato un riferimento al percorso dell'origine dati, insieme a una copia dei relativi metadati. Poiché i dati rimangono nella posizione esistente, non viene addebitato alcun costo aggiuntivo per l'archiviazione e non si rischia di modificare involontariamente le origini dati originali. Inoltre, i set di impostazioni vengono valutati in modo differito, che favoriscono le velocità delle prestazioni del flusso
 
Per informazioni sul modo in cui i set di dati si adattano al flusso di lavoro di accesso ai dati complessivo di Azure Machine Learning, vedere l'articolo relativo ai [dati](../machine-learning/concept-data.md#data-workflow) di


I set di impostazioni aperti si trovano nel cloud in Microsoft Azure e sono inclusi sia nell' [SDK Azure Machine Learning Python](#create-datasets-with-the-sdk) che in [Azure Machine Learning Studio](#create-datasets-with-the-studio).

## <a name="why-use-azure-open-datasets"></a>Perché usare i set di impostazioni di Azure aperti? 

Azure Open Datasets include set di dati pubblici curati che è possibile usare per aggiungere caratteristiche specifiche dello scenario alle soluzioni di Machine Learning e realizzare modelli più accurati. I set di dati includono dati di pubblico dominio relativi a meteo, censimento, festività, sicurezza pubblica e posizione, che consentono di eseguire il training di modelli di Machine Learning e arricchire le soluzioni predittive. 

Per altre informazioni, vedere [che cosa sono i set di dati aperti?](overview-what-are-open-datasets.md)

## <a name="prerequisites"></a>Prerequisiti

Per creare e usare i set di impostazioni, è necessario:

* Una sottoscrizione di Azure. Se non se ne ha una, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

* [Area di lavoro Azure Machine Learning](../machine-learning/how-to-manage-workspace.md).

* [SDK Azure Machine Learning per Python installato](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py), che include il pacchetto azureml-DataSets.

    * Creare un' [istanza di calcolo di Azure Machine Learning](../machine-learning/concept-compute-instance.md#managing-a-compute-instance), ovvero un ambiente di sviluppo completamente configurato e gestito che includa notebook integrati e SDK già installato.

    **OR**

    * Usare il notebook di Jupyter e installare l'SDK manualmente con [queste istruzioni](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

> [!NOTE]
> Alcune classi del set di dati presentano dipendenze dal pacchetto [azureml-dataprep](https://docs.microsoft.com/python/api/azureml-dataprep/?view=azure-ml-py) , che è compatibile solo con Python a 64 bit. Per gli utenti Linux queste classi sono supportate solo nelle distribuzioni seguenti: Red Hat Enterprise Linux (7, 8), Ubuntu (14,04, 16,04, 18,04), Fedora (27, 28), Debian (8, 9) e CentOS (7).

## <a name="create-datasets-with-the-sdk"></a>Creare set di impostazioni con l'SDK

Per creare set di impostazioni tramite le classi Open DataSets in Azure Machine Learning Python SDK, verificare che il pacchetto sia stato installato con `pip install azureml-opendatasets` . Ogni set di dati discreto è rappresentato dalla propria classe nell'SDK e alcune classi sono disponibili come `TabularDataset` , `FileDataset` o. Per un elenco completo delle classi, vedere la [documentazione di riferimento](https://docs.microsoft.com/python/api/azureml-opendatasets/azureml.opendatasets?view=azure-ml-py) .

È possibile recuperare determinate classi come `TabularDataset` o `FileDataset` , che consente di modificare e/o scaricare direttamente i file. Altre classi possono ottenere un set di dati **solo** usando una `get_tabular_dataset()` delle `get_file_dataset()` funzioni o. Nell'esempio di codice riportato di seguito vengono illustrati alcuni esempi di questi tipi di classi.

```python
from azureml.opendatasets import MNIST

# MNIST class can return either TabularDataset or FileDataset
tabular_dataset = MNIST.get_tabular_dataset()
file_dataset = MNIST.get_file_dataset()

from azureml.opendatasets import Diabetes

# Diabetes class can return ONLY TabularDataset and must be called from the static function
diabetes_tabular = Diabetes.get_tabular_dataset()
```

Quando si registra un set di dati creato da set di dati aperti, non vengono scaricati immediatamente dati, ma i dati saranno accessibili in un secondo momento, quando richiesto (durante il training, ad esempio) da una posizione di archiviazione centrale.

## <a name="create-datasets-with-the-studio"></a>Creare set di impostazioni con studio

È anche possibile creare set di impostazioni da set di set di impostazioni aperti con il [Azure Machine Learning Studio](https://ml.azure.com). Nell'area di lavoro selezionare la scheda **set** di **elementi in asset**. Nel menu a discesa **Crea set di dati** selezionare **da set di dati aperti**.

![Aprire il set di dati con l'interfaccia utente](./media/how-to-create-dataset-from-open-dataset/open-datasets-1.png)

Selezionare un set di dati selezionando il relativo riquadro. È possibile filtrare usando la barra di ricerca. Selezionare **Avanti**.

![Scegliere il set di dati](./media/how-to-create-dataset-from-open-dataset/open-datasets-2.png)

Scegliere un nome con cui registrare il set di dati e, facoltativamente, filtrare i dati usando i filtri disponibili. In questo caso, per il set di dati Public Holidays è possibile filtrare il periodo di tempo a un anno e il codice paese solo negli Stati Uniti. Selezionare **Crea**.

![Imposta parametri set di dati e crea set di dati](./media/how-to-create-dataset-from-open-dataset/open-datasets-3.png)

Il set di dati è ora disponibile nell'area di lavoro in **set**di dati. È possibile usarlo nello stesso modo degli altri set di impostazioni creati.


## <a name="access-datasets-for-your-experiments"></a>Accedere ai set di impostazioni per gli esperimenti

Usare i set di impostazioni negli esperimenti di machine learning per il training dei modelli ML. [Vedere altre informazioni su come eseguire il training con i set di dati](../machine-learning/how-to-train-with-datasets.md).

## <a name="example-notebooks"></a>Notebook di esempio

Per esempi e dimostrazioni della funzionalità Open DataSets, vedere questi [notebook](https://github.com/Azure/OpenDatasetsNotebooks/tree/master/tutorials).

## <a name="next-steps"></a>Passaggi successivi

* Eseguire [il training di un modello con set di impostazioni](../machine-learning/how-to-train-with-datasets.md).

* [Creare un set di dati di Azure Machine Learning](../machine-learning/how-to-create-register-datasets.md).



