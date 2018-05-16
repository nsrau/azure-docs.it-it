---
title: Uso di applicazioni in Azure Blockchain Workbench
description: Come usare i contratti dell'applicazione in Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 4/26/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: 35a45947c2766ccc5e64f4c3523ce163a9680680
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/12/2018
---
# <a name="using-applications-in-azure-blockchain-workbench"></a>Uso di applicazioni in Azure Blockchain Workbench

È possibile usare Blockchain Workbench per creare ed eseguire operazioni su contratti. È anche possibile visualizzare i dettagli dei contratti, come lo stato e la cronologia delle transazioni.

## <a name="prerequisites"></a>prerequisiti

* Una distribuzione di Blockchain Workbench. Per altre informazioni sulla distribuzione, vedere [Distribuzione di Azure Blockchain Workbench](blockchain-workbench-deploy.md)
* Un'applicazione blockchain distribuita in Blockchain Workbench. Vedere [Creare un'applicazione blockchain in Azure Blockchain Workbench]()

[Aprire Blockchain Workbench](blockchain-workbench-deploy.md#blockchain-workbench-web-url) nel browser.

![Blockchain Workbench](media/blockchain-workbench-use/workbench.png)

È necessario accedere come membro di Blockchain Workbench. Se non è elencata alcuna applicazione, si è un membro di Blockchain Workbench, ma di nessuna delle applicazioni. L'amministratore di Blockchain Workbench può assegnare membri alle applicazioni.

## <a name="create-new-contract"></a>Creare un nuovo contratto 

Per creare un nuovo contratto, è necessario essere un membro del ruolo **AllowedInstanceRoles**. 

1. Nella sezione dell'applicazione Blockchain Workbench selezionare il riquadro dell'applicazione che contiene il contratto che si vuole creare. Viene visualizzato un elenco dei contratti attivi.

2. Per creare un nuovo contratto, selezionare **Nuovo contratto**.

    ![Pulsante Nuovo contratto](media/blockchain-workbench-use/contract-list.png)

3. Viene visualizzato il riquadro **Nuovo contratto**. Specificare i valori dei parametri iniziali. Selezionare **Create**.

    ![Riquadro Nuovo contratto](media/blockchain-workbench-use/new-contract.png)

    Il nuovo contratto creato viene visualizzato nell'elenco con gli altri contratti attivi.

    ![Elenco dei contratti attivi](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Eseguire operazioni sui contratti

1. Nella sezione dell'applicazione Blockchain Workbench selezionare il riquadro dell'applicazione che contiene il contratto su cui si vuole eseguire l'azione.

    ![Elenco delle applicazioni](media/blockchain-workbench-use/apps-list.png)

2. Selezionare il contratto nell'elenco.

    ![Elenco dei contratti](media/blockchain-workbench-use/select-contract.png)

    I dettagli sul contratto vengono visualizzati in sezioni diverse. 

    ![Dettagli sul contratto](media/blockchain-workbench-use/contract-details.png)

    | Sezione  | DESCRIZIONE  |
    |---------|---------|
    | Status | Elenca lo stato di avanzamento corrente all'interno delle fasi del contratto |
    | Dettagli | Valori correnti del contratto |
    | Azione | Informazioni dettagliate sull'ultima azione |
    | Attività | Cronologia delle transazioni del contratto |
    
3. Nella sezione **Azione** selezionare **Intervieni**.

4. I dettagli sullo stato corrente del contratto vengono visualizzati in un riquadro. Scegliere l'azione che si vuole eseguire nell'elenco a discesa. 

    ![Intervieni](media/blockchain-workbench-use/take-action.png)

5. Selezionare **Esegui** per eseguire l'azione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come risolvere i problemi relativi ad Azure Blockchain Workbench](blockchain-workbench-troubleshooting.md)