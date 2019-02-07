---
title: "Passaggio 1: Creare un'area di lavoro"
titleSuffix: Azure Machine Learning Studio
description: 'Passaggio 1 della procedura dettagliata sullo sviluppo di una soluzione predittiva: informazioni su come configurare una nuova area di lavoro di Azure Machine Learning Studio.'
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: article
author: garyericson
ms.author: garye
ms.custom: seodec18
ms.date: 03/23/2017
ms.openlocfilehash: c018988eb36e395372b3d927732a1acbcb5c2fa9
ms.sourcegitcommit: fea5a47f2fee25f35612ddd583e955c3e8430a95
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55509509"
---
# <a name="walkthrough-step-1-create-an-azure-machine-learning-studio-workspace"></a>Passaggio 1 della procedura dettagliata: Creare un'area di lavoro di Azure Machine Learning Studio
Questo è il primo passaggio della procedura dettagliata per [sviluppare una soluzione di analisi predittiva con Azure Machine Learning](walkthrough-develop-predictive-solution.md).

1. **Creare un'area di lavoro di Machine Learning**
2. [Caricare i dati esistenti](walkthrough-2-upload-data.md)
3. [Creare un nuovo esperimento](walkthrough-3-create-new-experiment.md)
4. [Eseguire il training e valutare i modelli](walkthrough-4-train-and-evaluate-models.md)
5. [Distribuire il servizio Web](walkthrough-5-publish-web-service.md)
6. [Accedere al servizio Web](walkthrough-6-access-web-service.md)

- - -
<!-- This needs to be updated to refer to the new way of creating workspaces in the Ibiza portal -->

Per usare Machine Learning Studio, è necessario disporre di un'area di lavoro di Microsoft Azure Machine Learning. Quest'area di lavoro contiene tutti gli strumenti necessari per la creazione, la gestione e la pubblicazione di esperimenti.  

L'amministratore della sottoscrizione di Azure deve creare l'area di lavoro e quindi aggiungere l'utente come proprietario o collaboratore. Per informazioni, vedere [Creare un'area di lavoro di Azure Machine Learning](create-workspace.md).

Dopo aver creato l'area di lavoro, aprire Machine Learning Studio ([https://studio.azureml.net/Home](https://studio.azureml.net/Home)). Se sono disponibili più aree di lavoro, è possibile selezionare l'area di lavoro nella barra degli strumenti nell'angolo superiore destro della finestra.

![Selezionare l'area di lavoro in Studio][2]

> [!TIP]
> Se si è proprietari dell'area di lavoro, è possibile condividere gli esperimenti su cui si sta lavorando invitando altri utenti nell'area di lavoro. Questa operazione può essere eseguita nella pagina **SETTINGS** di Machine Learning Studio. È sufficiente conoscere l'account Microsoft o l'account aziendale di ogni utente.
> 
> Nella pagina **SETTINGS** fare clic su **USERS** e quindi su **INVITE MORE USERS** nella parte inferiore della finestra.
> 
> 

- - -
**Passaggio successivo: [Caricare i dati esistenti](walkthrough-2-upload-data.md)**

[1]: ./media/walkthrough-1-create-ml-workspace/create1.png
[2]: ./media/walkthrough-1-create-ml-workspace/open-workspace.png
