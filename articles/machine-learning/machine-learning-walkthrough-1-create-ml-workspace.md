---
title: 'Passaggio 1: Creare un''area di lavoro di Machine Learning | Documentazione Microsoft'
description: 'Passaggio 1 della procedura dettagliata Sviluppare una soluzione predittiva: informazioni su come configurare una nuova area di lavoro di Azure Machine Learning Studio.'
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: b3c97e3d-16ba-4e42-9657-2562854a1e04
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: garye
ms.translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 8ca42ef8f5314866301f5c9e93caa90dc837a66e
ms.contentlocale: it-it
ms.lasthandoff: 03/25/2017

---
# <a name="walkthrough-step-1-create-a-machine-learning-workspace"></a>Passaggio 1 della procedura dettagliata: Creare un'area di lavoro di Machine Learning
Questo è il primo passaggio della procedura dettagliata per [sviluppare una soluzione di analisi predittiva con Azure Machine Learning](machine-learning-walkthrough-develop-predictive-solution.md).

1. **Creare un'area di lavoro di Machine Learning**
2. [Caricare i dati esistenti](machine-learning-walkthrough-2-upload-data.md)
3. [Creare un nuovo esperimento](machine-learning-walkthrough-3-create-new-experiment.md)
4. [Eseguire il training e valutare i modelli](machine-learning-walkthrough-4-train-and-evaluate-models.md)
5. [Distribuire il servizio Web](machine-learning-walkthrough-5-publish-web-service.md)
6. [Accedere al servizio Web](machine-learning-walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Per usare Machine Learning Studio, è necessario disporre di un'area di lavoro di Microsoft Azure Machine Learning. Quest'area di lavoro contiene tutti gli strumenti necessari per la creazione, la gestione e la pubblicazione di esperimenti.  

<!--
## To create a workspace
1. Sign in to the [Azure classic portal](https://manage.windowsazure.com).
2. In the  Azure services panel, click **MACHINE LEARNING**.  
   ![Create workspace][1]
3. Click **CREATE AN ML WORKSPACE**.
4. On the **QUICK CREATE** page, enter your workspace information and then click **CREATE AN ML WORKSPACE**.
-->

L'amministratore della sottoscrizione di Azure deve creare l'area di lavoro e quindi aggiungere l'utente come proprietario o collaboratore. Per informazioni, vedere [Creare un'area di lavoro di Azure Machine Learning](machine-learning-create-workspace.md).

Dopo aver creato l'area di lavoro, aprire Machine Learning Studio ([https://studio.azureml.net](https://studio.azureml.net/Home)). Se sono disponibili più aree di lavoro, è possibile selezionare l'area di lavoro nella barra degli strumenti nell'angolo superiore destro della finestra.

![Selezionare l'area di lavoro in Studio][2]

> [!TIP]
> Se si è proprietari dell'area di lavoro, è possibile condividere gli esperimenti su cui si sta lavorando invitando altri utenti nell'area di lavoro. Questa operazione può essere eseguita nella pagina **SETTINGS** di Machine Learning Studio. È sufficiente conoscere l'account Microsoft o l'account aziendale di ogni utente.
> 
> Nella pagina **SETTINGS** fare clic su **USERS** e quindi su **INVITE MORE USERS** nella parte inferiore della finestra.
> 
> 

- - -
**Passaggio successivo: [Caricare i dati esistenti](machine-learning-walkthrough-2-upload-data.md)**

[1]: ./media/machine-learning-walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/machine-learning-walkthrough-1-create-ml-workspace/open-workspace.png

