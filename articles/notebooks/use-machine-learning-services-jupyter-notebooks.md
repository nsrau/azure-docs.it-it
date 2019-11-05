---
title: Usare Azure Machine Learning in Azure Notebooks
description: Panoramica dei notebook di esempio per Azure Machine Learning che è possibile usare con Azure Notebooks.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 6eac5d77404c85d5481ded7e58b0cd9fab0de083
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73496649"
---
# <a name="use-azure-machine-learning-in-a-notebook"></a>Usare Azure Machine Learning in un notebook

Azure Notebooks viene preconfigurato con l'ambiente necessario per l'uso di [Azure Machine Learning](/azure/machine-learning/service/). È possibile clonare facilmente un progetto di esempio nell'account di Notebooks per esplorare un'ampia gamma di scenari di Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Clonare l'esempio nell'account personale

1. Accedere ad [Azure Notebooks](https://notebooks.azure.com/).
1. Selezionare **progetti personali** per passare al dashboard progetti.
1. Selezionare il pulsante **carica repository GitHub** (freccia su) per aprire il popup **caricare il repository GitHub** .
1. Nella finestra popup immettere `Azure/MachineLearningNotebooks` nel **repository GitHub**, specificare un nome per il progetto in **nome progetto** , ad esempio "Azure Machine Learning", specificare un identificatore in **ID progetto**, deselezionare **public** se si desidera, quindi selezionare **Importa**.

    ![Importare un esempio di Azure Machine Learning Notebook nell'account di Notebooks](media/azureml-import-project.png)

1. Dopo un minuto o due, Azure Notebooks aprirà automaticamente il dashboard del nuovo progetto.

## <a name="run-a-sample-notebook"></a>Eseguire un notebook di esempio

1. Selezionare **00 - configuration.ipynb** per avviare la sezione di configurazione del notebook e seguire le istruzioni per creare un'area di lavoro di Azure Machine Learning.

    - Poiché Azure Notebooks contiene già i pacchetti Python necessari, è sufficiente eseguire il frammento di codice disponibile nel passaggio 2 dei prerequisiti per verificare la versione dell'SDK di Azure Machine Learning.

1. Al termine della configurazione, selezionare **01. Getting-Started** per aprire la cartella contenente tredici diversi notebook di esempio, ciascuno dei quali è di facile comprensione.

## <a name="next-steps"></a>Passaggi successivi

La documentazione di Azure Machine Learning contiene un'ampia gamma di altre risorse che guidano l'utente nell'uso di Machine Learning all'interno dei notebook:

- [Guida introduttiva: usare Python per iniziare a usare Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Esercitazione #1: eseguire il training di un modello di classificazione delle immagini con Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Esercitazione #2: distribuire un modello di classificazione delle immagini nell'istanza di contenitore di Azure (ACI)](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Esercitazione: eseguire il training di un modello di classificazione con Machine Learning automatizzato in Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Vedere anche la documentazione relativa ad [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
