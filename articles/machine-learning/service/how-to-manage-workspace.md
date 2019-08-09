---
title: Creare e gestire aree di lavoro
titleSuffix: Azure Machine Learning service
description: Informazioni su come creare, visualizzare ed eliminare aree di lavoro del servizio Azure Machine Learning nel portale di Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: f93b74c57d45ddfc7b47beff7852d183a93df00e
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/08/2019
ms.locfileid: "68856158"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Creare e gestire aree di lavoro del servizio Azure Machine Learning

In questo articolo si procederà alla creazione, alla visualizzazione e all'eliminazione di [**aree di lavoro del servizio Azure Machine Learning**](concept-workspace.md) nel portale di Azure per il [servizio Azure Machine Learning](overview-what-is-azure-ml.md).  Il portale è il modo più semplice per iniziare a usare le aree di lavoro, ma in base alle esigenze modificate o ai requisiti per l'aumento dell'automazione è anche possibile creare ed eliminare aree di lavoro [tramite l'interfaccia della](reference-azure-machine-learning-cli.md)riga di comando, [con codice Python](https://aka.ms/aml-sdk) o [tramite l'estensione vs code](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="create-a-workspace"></a>Creare un'area di lavoro

Per creare un'area di lavoro, è necessaria una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view"></a>Visualizzare un'area di lavoro

1. Nell'angolo superiore sinistro del portale selezionare **Tutti i servizi**.

1. Nel campo filtro **tutti i servizi** Digitare servizio di **Machine Learning**.  

1. Selezionare **Machine Learning aree di lavoro del servizio**.

   ![Cerca area di lavoro del servizio Azure Machine Learning](media/how-to-manage-workspace/all-services.png)

1. Esaminare l'elenco delle aree di lavoro trovate. È possibile filtrarle in base alla sottoscrizione, ai gruppi di risorse e alle posizioni.  

1. Selezionare un'area di lavoro per visualizzarne le proprietà.
   ![Proprietà area di lavoro](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Eliminazione di un'area di lavoro

Usare il pulsante Elimina nella parte superiore dell'area di lavoro da eliminare.

  ![Pulsante Elimina](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Pulire le risorse

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

Seguire l'esercitazione completa per apprendere come usare un'area di lavoro per compilare e distribuire modelli, nonché per eseguirne il training, con il servizio Azure Machine Learning.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
