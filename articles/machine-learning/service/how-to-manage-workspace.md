---
title: Creare e gestire le aree di lavoro di Azure Machine Learning
description: Informazioni su come creare, visualizzare ed eliminare aree di lavoro di Azure Machine Learning nel portale di Azure.
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.openlocfilehash: 7d01a2e3ebd46315966c82a43a17ffc5b329b829
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "46954348"
---
# <a name="create-and-manage-azure-machine-learning-workspaces"></a>Creare e gestire le aree di lavoro di Azure Machine Learning

In questo articolo si procederà alla creazione, alla visualizzazione e all'eliminazione di [**aree di lavoro di Azure Machine Learning**](concept-azure-machine-learning-architecture.md#workspace) nel portale di Azure per il [servizio Azure Machine Learning](overview-what-is-azure-ml.md).  È anche possibile creare ed eliminare aree di lavoro [tramite l'interfaccia della riga di comando](reference-azure-machine-learning-cli.md) oppure [tramite codice Python](http://aka.ms/aml-sdk).

Per creare un'area di lavoro, è necessaria una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="create-a-workspace"></a>Creare un'area di lavoro 

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>Visualizzare un'area di lavoro

1. Nell'angolo superiore sinistro del portale selezionare **Tutti i servizi**. 

1. Nel campo di filtro **Tutti i servizi** digitare **Machine Learning Workspace** (Area di lavoro di Machine Learning).  

   ![Ricerca di un'area di lavoro di Azure Machine Learning](media/how-to-manage-workspace/allservices-search1.png)

1. Nei risultati del filtro selezionare **Machine Learning Workspace** (Area di lavoro di Machine Learning) per visualizzare un elenco delle aree di lavoro. 

   ![Ricerca di un'area di lavoro di Azure Machine Learning](media/how-to-manage-workspace/allservices-search.PNG)

1. Esaminare l'elenco delle aree di lavoro trovate. È possibile filtrarle in base alla sottoscrizione, ai gruppi di risorse e alle posizioni.  

   ![Elenco delle aree di lavoro di Azure Machine Learning](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. Selezionare l'area di lavoro appena creata per visualizzarne le proprietà.

   ![png](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Eliminazione di un'area di lavoro

Usare il pulsante Elimina nella parte superiore dell'area di lavoro da eliminare.

  ![png](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Pulire le risorse 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Passaggi successivi

Seguire l'esercitazione completa per apprendere come usare un'area di lavoro per compilare e distribuire modelli, nonché per eseguirne il training, con il servizio Azure Machine Learning.

> [!div class="nextstepaction"]
> [Esercitazione: Eseguire il training dei modelli](tutorial-train-models-with-aml.md)
