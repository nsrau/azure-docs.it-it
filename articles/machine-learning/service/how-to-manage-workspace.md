---
title: Creare aree di lavoro di Azure ML nel portale
titleSuffix: Azure Machine Learning
description: Informazioni su come creare, visualizzare ed eliminare aree di lavoro Azure Machine Learning nel portale di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 511c737e160c0f0753e570314c9b29346972cb04
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/21/2019
ms.locfileid: "71269259"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Creare e gestire aree di lavoro Azure Machine Learning nel portale di Azure

Questo articolo illustra come creare, visualizzare ed eliminare [**Azure Machine Learning aree di lavoro**](concept-workspace.md) nel portale di Azure per [Azure Machine Learning](overview-what-is-azure-ml.md).  Il portale è il modo più semplice per iniziare a usare le aree di lavoro, ma in base alle esigenze modificate o ai requisiti per l'aumento dell'automazione è anche possibile creare ed eliminare aree di lavoro [tramite l'interfaccia della](reference-azure-machine-learning-cli.md)riga di comando, [con codice Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) o [tramite l'estensione vs code](how-to-vscode-tools.md#get-started-with-azure-machine-learning-for-visual-studio-code).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Per creare un'area di lavoro, è necessaria una sottoscrizione di Azure. Se non si dispone di una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare la [versione gratuita o a pagamento di Azure Machine Learning](https://aka.ms/AMLFree).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>Scaricare un file di configurazione

1. Se si vuole creare una [VM notebook](tutorial-1st-experiment-sdk-setup.md#azure), ignorare questo passaggio.

1. Se si prevede di usare codice nell'ambiente locale che fa riferimento a questa area di lavoro, selezionare **Scarica config json** dalla sezione **Panoramica** dell'area di lavoro.  

   ![Scarica config.json](./media/how-to-manage-workspace/configure.png)
   
   Inserire il file nella struttura di directory che contiene gli script Python o i notebook di Jupyter. Può trattarsi della stessa directory, in una sottodirectory denominata *.azureml* o in una directory padre. Quando si crea una macchina virtuale notebook, questo file viene aggiunto alla directory corretta nella macchina virtuale.


## <a name="view"></a>Visualizzare un'area di lavoro

1. Nell'angolo superiore sinistro del portale selezionare **Tutti i servizi**.

1. Nel campo filtro **tutti i servizi** Digitare servizio di **Machine Learning**.  

1. Selezionare **Machine Learning aree di lavoro del servizio**.

   ![Cerca area di lavoro Azure Machine Learning](media/how-to-manage-workspace/all-services.png)

1. Esaminare l'elenco delle aree di lavoro trovate. È possibile filtrarle in base alla sottoscrizione, ai gruppi di risorse e alle posizioni.  

1. Selezionare un'area di lavoro per visualizzarne le proprietà.
   Proprietà ![Workspace ](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Eliminazione di un'area di lavoro

Usare il pulsante Elimina nella parte superiore dell'area di lavoro da eliminare.

  ![Pulsante Elimina](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

Seguire l'esercitazione completa per apprendere come usare un'area di lavoro per creare, eseguire il training e distribuire modelli con Azure Machine Learning.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
