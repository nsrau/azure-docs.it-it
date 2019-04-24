---
title: Gestire un'area di lavoro di Machine Learning Studio
titleSuffix: Azure Machine Learning Studio
description: Gestire l'accesso alle aree di lavoro di Azure Machine Learning Studio e distribuire e gestire i servizi Web API Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 02/27/2017
ms.openlocfilehash: a947f9a94dd4ceed624e6b04a671b21b8926d25e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60322788"
---
# <a name="manage-an-azure-machine-learning-studio-workspace"></a>Gestire un'area di lavoro di Azure Machine Learning Studio

> [!NOTE]
> Per informazioni sulla gestione dei servizi Web nel portale dei servizi Web di Machine Learning, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md).
> 
> 

È possibile gestire le aree di lavoro di Machine Learning Studio nel portale di Azure.



## <a name="use-the-azure-portal"></a>Usare il portale di Azure

Per gestire un'area di lavoro di Studio nel portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com/) usando un account amministratore della sottoscrizione di Azure.
2. Nella casella di ricerca nella parte superiore della pagina immettere "aree di lavoro di Machine Learning Studio" e quindi selezionare **Aree di lavoro di Machine Learning Studio**.
3. Fare clic sull'area di lavoro da gestire.

Oltre alle informazioni e alle opzioni di gestione delle risorse standard disponibili, viene offerta la possibilità di effettuare le operazioni seguenti.

- Visualizzare **Proprietà**: questa pagina visualizza informazioni relative all'area di lavoro e alle risorse e consente di modificare la sottoscrizione e il gruppo di risorse a cui è connessa l'area di lavoro.
- **Risincronizzare le chiavi di archiviazione**: l'area di lavoro mantiene le chiavi per l'account di archiviazione. Se le chiavi vengono modificate nell'account di archiviazione, è possibile fare clic su **Risincronizza le chiavi** per sincronizzarle con l'area di lavoro.

Per gestire i servizi Web associati all'area di lavoro di Studio, usare il portale dei servizi Web di Machine Learning. Per informazioni complete, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md).

> [!NOTE]
> Per distribuire o gestire nuovi servizi Web è necessario che all'utente sia assegnato un ruolo di collaboratore o di amministratore nella sottoscrizione in cui viene distribuito il servizio Web. Se si invita un altro utente in un'area di lavoro di Machine Learning Studio, affinché possa distribuire o gestire i servizi Web è prima necessario assegnargli un ruolo di collaboratore o di amministratore nella sottoscrizione. 
> 
>Per altre informazioni sull'impostazione delle autorizzazioni di accesso, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../../role-based-access-control/role-assignments-portal.md).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sulla [distribuzione di Machine Learning con i modelli di Azure Resource Manager](deploy-with-resource-manager-template.md). 
