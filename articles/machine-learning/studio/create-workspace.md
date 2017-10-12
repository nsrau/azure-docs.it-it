---
title: Creare un'area di lavoro di Machine Learning | Microsoft Docs
description: Come creare un'area di lavoro per Azure Machine Learning Studio.
services: machine-learning
documentationcenter: 
author: garyericson
manager: jhubbard
editor: cgronlun
ms.assetid: aa96b784-ac6c-44bc-a28a-85d49fbe90a2
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: garye;bradsev;ahgyger
ms.openlocfilehash: 4e1fa0a9abd4721d15a94923263ff2f521bceee8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="create-and-share-an-azure-machine-learning-workspace"></a>Creare e condividere un'area di lavoro di Azure Machine Learning
Questo menu si collega ad argomenti che descrivono come configurare i diversi ambienti di scienza dei dati usati da Cortana Analytics Process (CAP).

[!INCLUDE [data-science-environment-setup](../../../includes/cap-setup-environments.md)]

Per usare Azure Machine Learning Studio, è necessario disporre di un'area di lavoro di Machine Learning. Quest'area di lavoro contiene tutti gli strumenti necessari per la creazione, la gestione e la pubblicazione di esperimenti.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

### <a name="to-create-a-workspace"></a>Per creare un'area di lavoro
1. Accedere al [portale di Azure](https://portal.azure.com/)

    > [!NOTE]
    > Per eseguire l'accesso e creare un'area di lavoro è necessario essere amministratore di una sottoscrizione di Azure. 
    >
    > 

2. Fare clic su **+Nuovo**

3. Selezionare **Intelligence e analisi**, fare clic su **Area di lavoro di Machine Learning** e quindi su **Crea**

4. Immettere le informazioni sull'area di lavoro

    - Il *nome dell'area di lavoro* può contenere al massimo 260 caratteri e non può terminare con uno spazio. Il nome non può includere questi caratteri: `< > * % & : \ ? + /`
    - Se si distribuiscono i servizi Web da quest'area di lavoro, vengono usati il *piano di servizio Web* che si sceglie (o si crea) e il *piano tariffario* selezionato.

    ![Creazione di una nuova area di lavoro](./media/create-workspace/create-new-workspace.png)

5. Fare clic su **Crea**

Dopo aver distribuito l'area di lavoro è possibile aprirla in Machine Learning Studio.

1. Passare a Machine Learning Studio all'indirizzo [https://studio.azureml.net/](https://studio.azureml.net/).

2. Selezionare l'area di lavoro nell'angolo in alto a destra.

    ![Selezionare l'area di lavoro](./media/create-workspace/open-workspace.png)

3. Fare clic su **esperimenti personali**.

    ![Aprire gli esperimenti](./media/create-workspace/my-experiments.png)

Per informazioni sulla gestione dell'area di lavoro, vedere [Gestire un'area di lavoro di Azure Machine Learning](manage-workspace.md).
Se si verifica un problema durante la creazione dell'area di lavoro, vedere [Guida per la risoluzione dei problemi: Creare un'area di lavoro di Machine Learning e connettersi a essa](troubleshooting-creating-ml-workspace.md).


## <a name="sharing-an-azure-machine-learning-workspace"></a>Condivisione di un'area di lavoro di Azure Machine Learning
Dopo la creazione di un'area di lavoro di Machine Learning, è possibile invitare utenti all'area di lavoro per condividere l'accesso all'area di lavoro e a tutti i rispettivi esperimenti, dataset, notebook e così via. È possibile aggiungere gli utenti in due ruoli:

* **Utente**: un utente dell'area di lavoro può creare, aprire, modificare ed eliminare esperimenti, dataset e così via nell'area di lavoro.
* **Proprietario**: un proprietario può invitare e rimuovere gli utenti dell'area di lavoro in aggiunta alle operazioni consentite a un utente.

> [!NOTE]
> L'account amministratore che crea l'area di lavoro viene aggiunto automaticamente all'area di lavoro come proprietario. Agli altri amministratori o utenti della sottoscrizione non viene invece concesso automaticamente l'accesso all'area di lavoro, ma è necessario inviare loro un invito in modo esplicito.
> 
> 

### <a name="to-share-a-workspace"></a>Per condividere un'area di lavoro

1. Accedere a Machine Learning Studio all'indirizzo [https://studio.azureml.net/Home](https://studio.azureml.net/Home)

2. Nel pannello sinistro fare clic su **IMPOSTAZIONI**

3. Fare clic sulla scheda **UTENTI**

4. Fare clic su **INVITE MORE USERS (INVITA ALTRI UTENTI)** in fondo alla pagina

    ![Impostazioni di Studio](./media/create-workspace/settings.png)

5. Immettere uno o più indirizzi di posta elettronica. L'utente ha bisogno di un account Microsoft o di un account aziendale valido (da Azure Active Directory).

6. Scegliere se si desidera aggiungere l'utente come proprietario o utente.

7. Fare clic sul segno di spunta **OK**.

Ogni utente aggiunto riceverà un messaggio di posta elettronica con istruzioni per l'accesso all'area di lavoro condivisa.

> [!NOTE]
> Per poter distribuire o gestire i servizi Web nell'area di lavoro, l'utente deve essere un collaboratore o un amministratore nella sottoscrizione di Azure. 



