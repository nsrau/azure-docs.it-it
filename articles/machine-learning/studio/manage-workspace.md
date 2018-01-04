---
title: Gestire un'area di lavoro di Machine Learning | Microsoft Docs
description: Gestione dell'accesso alle aree di lavoro di Azure Machine Learning e distribuzione e gestione dei servizi Web API ML
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: daf3d413-7a77-4beb-9a7a-6b4bdf717719
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: garye
ms.openlocfilehash: 2e4b2869b6eac9670853832d58bc37f1cb0ed001
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="manage-an-azure-machine-learning-workspace"></a>Gestire un'area di lavoro di Azure Machine Learning

> [!NOTE]
> Per informazioni sulla gestione dei servizi Web nel portale dei servizi Web di Machine Learning, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md).
> 
> 

È possibile gestire le aree di lavoro di Machine Learning nel portale di Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="use-the-azure-portal"></a>Usare il portale di Azure

Per gestire un'area di lavoro nel portale di Azure:

1. Accedere al [portale di Azure](https://portal.azure.com/) usando un account amministratore della sottoscrizione di Azure.
2. Nella casella di ricerca nella parte superiore della pagina immettere "aree di lavoro di Machine Learning" e quindi selezionare **Aree di lavoro di Machine Learning**.
3. Fare clic sull'area di lavoro da gestire.

Oltre alle informazioni e alle opzioni di gestione delle risorse standard disponibili, viene offerta la possibilità di effettuare le operazioni seguenti.

- Visualizzare **Proprietà**: questa pagina visualizza informazioni relative all'area di lavoro e alle risorse e consente di modificare la sottoscrizione e il gruppo di risorse a cui è connessa l'area di lavoro.
- **Risincronizzare le chiavi di archiviazione**: l'area di lavoro mantiene le chiavi per l'account di archiviazione. Se le chiavi vengono modificate nell'account di archiviazione, è possibile fare clic su **Risincronizza le chiavi** per sincronizzarle con l'area di lavoro.

Per gestire i servizi Web associati all'area di lavoro, usare il portale dei servizi Web di Machine Learning. Per informazioni complete, vedere [Gestire un servizio Web usando il portale dei servizi Web di Azure Machine Learning](manage-new-webservice.md).

> [!NOTE]
> Per distribuire o gestire nuovi servizi Web è necessario che all'utente sia assegnato un ruolo di collaboratore o di amministratore nella sottoscrizione in cui viene distribuito il servizio Web. Se si invita un altro utente in un'area di lavoro di Machine Learning, affinché possa distribuire o gestire servizi Web è prima necessario assegnargli un ruolo di collaboratore o di amministratore nella sottoscrizione. 
> 
>Per ulteriori informazioni sull'impostazione delle autorizzazioni di accesso, vedere [visualizzare le assegnazioni di accesso per utenti e gruppi nel portale di Azure](../../active-directory/role-based-access-control-manage-assignments.md).

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni, vedere [distribuire Machine Learning con modelli di gestione risorse di Azure](deploy-with-resource-manager-template.md). 