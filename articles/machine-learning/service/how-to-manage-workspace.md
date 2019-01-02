---
title: Creare e gestire aree di lavoro
titleSuffix: Azure Machine Learning service
description: Informazioni su come creare, visualizzare ed eliminare aree di lavoro del servizio Azure Machine Learning nel portale di Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: 73a47929fa26ae0729943e17d0f6c9a054f4f6bb
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/12/2018
ms.locfileid: "53276707"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Creare e gestire aree di lavoro del servizio Azure Machine Learning

In questo articolo si procederà alla creazione, alla visualizzazione e all'eliminazione di [**aree di lavoro del servizio Azure Machine Learning**](concept-azure-machine-learning-architecture.md#workspace) nel portale di Azure per il [servizio Azure Machine Learning](overview-what-is-azure-ml.md).  È anche possibile creare ed eliminare aree di lavoro [tramite l'interfaccia della riga di comando](reference-azure-machine-learning-cli.md) oppure [tramite codice Python](https://aka.ms/aml-sdk).

## <a name="create-a-workspace"></a>Creare un'area di lavoro 

Per creare un'area di lavoro, è necessaria una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](http://aka.ms/AMLFree).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>Visualizzare un'area di lavoro

1. Nell'angolo superiore sinistro del portale selezionare **Tutti i servizi**. 

1. Nel campo di filtro **Tutti i servizi** digitare **area di lavoro del servizio Machine Learning**.  

   ![Cercare l'area di lavoro del servizio Azure Machine Learning](media/how-to-manage-workspace/allservices-search1.png)

1. Nei risultati del filtro selezionare **Machine Learning service workspace** (Area di lavoro di Machine Learning) per visualizzare un elenco delle aree di lavoro. 

   ![Elencare le aree di lavoro del servizio Azure Machine Learning](media/how-to-manage-workspace/allservices-search.PNG)

1. Esaminare l'elenco delle aree di lavoro trovate. È possibile filtrarle in base alla sottoscrizione, ai gruppi di risorse e alle posizioni.  

   ![Visualizzare le aree di lavoro](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. Selezionare l'area di lavoro appena creata per visualizzarne le proprietà.

   ![Proprietà dell'area di lavoro](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Eliminazione di un'area di lavoro

Usare il pulsante Elimina nella parte superiore dell'area di lavoro da eliminare.

  ![Pulsante Elimina](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Pulire le risorse 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

Seguire l'esercitazione completa per apprendere come usare un'area di lavoro per compilare e distribuire modelli, nonché per eseguirne il training, con il servizio Azure Machine Learning.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
