---
title: Usare Azure Machine Learning Services in Azure Notebooks
description: Panoramica dei notebook di esempio per Azure Machine Learning Services che è possibile usare con Azure Notebooks.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: 0dc4fc31-ae1c-422c-ac34-7b025e6651b4
ms.service: notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: b6d3cbd56182b8dba3738f38798efa378c6b49d0
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/11/2018
ms.locfileid: "53254827"
---
# <a name="use-azure-machine-learning-services-in-a-notebook"></a>Usare Azure Machine Learning Services in un notebook

Azure Notebooks è preconfigurato con l'ambiente necessario per interagire con [Azure Machine Learning Services](/azure/machine-learning/service/). È possibile clonare facilmente un progetto di esempio nell'account di Notebooks per esplorare un'ampia gamma di scenari di Machine Learning.

## <a name="clone-the-sample-into-your-account"></a>Clonare l'esempio nell'account personale

1. Accedere ad [Azure Notebooks](https://notebooks.azure.com/).
1. Selezionare **My Projects** (Progetti personali) per passare al dashboard dei progetti.
1. Selezionare il pulsante **Upload GitHub Repo** (Carica repository GitHub) (freccia verso l'alto) per aprire la finestra popup **Upload GitHub Repository** (Carica repository GitHub).
1. Nella finestra popup immettere `Azure/MachineLearningNotebooks` in **GitHub repository** (Repository di GitHub), specificare un nome per il progetto in **Project Name** (Nome progetto), ad esempio "Azure Machine Learning Services", specificare un identificatore in **Project ID** (ID progetto), deselezionare la casella **Public** (Pubblico), se lo si desidera, e quindi selezionare **Import** (Importa).

    ![Importare un esempio di Azure Machine Learning Notebook nell'account di Notebooks](media/azureml-import-project.png)

1. Dopo un minuto o due, Azure Notebooks aprirà automaticamente il dashboard del nuovo progetto.

## <a name="run-a-sample-notebook"></a>Eseguire un notebook di esempio

1. Selezionare **00 - configuration.ipynb** per avviare la sezione di configurazione del notebook e seguire le istruzioni per creare un'area di lavoro di Azure Machine Learning.

    - Poiché Azure Notebooks contiene già i pacchetti Python necessari, è sufficiente eseguire il frammento di codice disponibile nel passaggio 2 dei prerequisiti per verificare la versione dell'SDK di Azure Machine Learning.

1. Al termine della configurazione, selezionare **01.getting-started** per passare a una cartella contenente tredici diversi esempi di notebook, ognuno dei quali facilmente comprensibile.

## <a name="next-steps"></a>Passaggi successivi

Nella documentazione di Azure Machine Learning Services sono disponibili molte altre risorse che illustrano l'utilizzo di Machine Learning Service con i notebook:

- [Avvio rapido: Iniziare a usare Azure Machine Learning con Python](https://docs.microsoft.com/azure/machine-learning/service/quickstart-create-workspace-with-python)
- [Esercitazione n. 1: Eseguire il training di un modello di classificazione delle immagini con il servizio Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-train-models-with-aml)
- [Esercitazione n. 2: Distribuire un modello di classificazione di immagini nell'istanza di contenitore di Azure](https://docs.microsoft.com/azure/machine-learning/service/tutorial-deploy-models-with-aml)
- [Esercitazione: Eseguire il training di un modello di classificazione con apprendimento automatico nel servizio Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/service/tutorial-auto-train-models)

Vedere anche la documentazione relativa ad [Azure Machine Learning SDK per Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py).
