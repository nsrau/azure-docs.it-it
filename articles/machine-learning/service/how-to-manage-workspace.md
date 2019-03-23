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
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: e65f739a9641181381205c7255d0472325e8055c
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58359887"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Creare e gestire aree di lavoro del servizio Azure Machine Learning

In questo articolo si procederà alla creazione, alla visualizzazione e all'eliminazione di [**aree di lavoro del servizio Azure Machine Learning**](concept-azure-machine-learning-architecture.md#workspace) nel portale di Azure per il [servizio Azure Machine Learning](overview-what-is-azure-ml.md).  È anche possibile creare ed eliminare aree di lavoro [tramite l'interfaccia della riga di comando](reference-azure-machine-learning-cli.md) oppure [tramite codice Python](https://aka.ms/aml-sdk).

## <a name="create-a-workspace"></a>Creare un'area di lavoro 

Per creare un'area di lavoro, è necessaria una sottoscrizione di Azure. Se non è disponibile una sottoscrizione di Azure, creare un account gratuito prima di iniziare. Provare subito la [versione gratuita o a pagamento del servizio Azure Machine Learning](https://aka.ms/AMLFree).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view"></a>Visualizzare un'area di lavoro

1. Nell'angolo superiore sinistro del portale selezionare **Tutti i servizi**. 

1. Nel **tutti i servizi** campo del filtro, digitare **di machine learning servizio**.  

1. Selezionare **aree di lavoro di Machine Learning servizio**.

   ![Cerca per area di lavoro del servizio Azure Machine Learning](media/how-to-manage-workspace/all-services.png)

1. Esaminare l'elenco delle aree di lavoro trovate. È possibile filtrarle in base alla sottoscrizione, ai gruppi di risorse e alle posizioni.  

1. Selezionare un'area di lavoro per visualizzarne le proprietà.
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
