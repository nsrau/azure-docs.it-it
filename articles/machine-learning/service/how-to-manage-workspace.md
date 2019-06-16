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
ms.openlocfilehash: 7f0806a1d68cd2cede1ae51f0a50a8125c1e7c8b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66016537"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Creare e gestire aree di lavoro del servizio Azure Machine Learning

In questo articolo si procederà alla creazione, alla visualizzazione e all'eliminazione di [**aree di lavoro del servizio Azure Machine Learning**](concept-workspace.md) nel portale di Azure per il [servizio Azure Machine Learning](overview-what-is-azure-ml.md).  È anche possibile creare ed eliminare aree di lavoro [tramite la CLI](reference-azure-machine-learning-cli.md), [con il codice Python](https://aka.ms/aml-sdk) oppure [tramite l'estensione di Visual Studio Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

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
