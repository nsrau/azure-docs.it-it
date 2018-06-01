---
title: Uso di applicazioni in Azure Blockchain Workbench
description: Come usare i contratti dell'applicazione in Azure Blockchain Workbench.
services: azure-blockchain
keywords: ''
author: PatAltimore
ms.author: patricka
ms.date: 5/16/2018
ms.topic: article
ms.service: azure-blockchain
ms.reviewer: zeyadr
manager: femila
ms.openlocfilehash: b15d4980875b805b49f7dc1cdb941e607232f3cb
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/17/2018
ms.locfileid: "34258040"
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

Per creare un nuovo contratto, è necessario essere un membro specificato come **iniziatore** di contratti. Per informazioni sulla definizione dei ruoli applicazione e degli iniziatori del contratto, vedere i [flussi di lavoro nella panoramica della configurazione](blockchain-workbench-configuration-overview.md#workflows). Per informazioni sull'assegnazione di membri ai ruoli applicazione, vedere [Aggiungere un membro all'applicazione](blockchain-workbench-manage-users.md#add-member-to-application).

1. Nella sezione dell'applicazione Blockchain Workbench selezionare il riquadro dell'applicazione che contiene il contratto che si vuole creare. Viene visualizzato un elenco dei contratti attivi.

2. Per creare un nuovo contratto, selezionare **Nuovo contratto**.

    ![Pulsante Nuovo contratto](media/blockchain-workbench-use/contract-list.png)

3. Viene visualizzato il riquadro **Nuovo contratto**. Specificare i valori dei parametri iniziali. Selezionare **Create**.

    ![Riquadro Nuovo contratto](media/blockchain-workbench-use/new-contract.png)

    Il nuovo contratto creato viene visualizzato nell'elenco con gli altri contratti attivi.

    ![Elenco dei contratti attivi](media/blockchain-workbench-use/active-contracts.png)

## <a name="take-action-on-contract"></a>Eseguire operazioni sui contratti

A seconda dello stato in cui si trova il contratto, i membri possono eseguire azioni per la transizione allo stato successivo del contratto. Le azioni sono definite come [transizioni](blockchain-workbench-configuration-overview.md#transitions) all'interno di un [stato](blockchain-workbench-configuration-overview.md#states). I membri che appartengono a un ruolo applicazione o istanza consentito per la transizione possono eseguire l'azione. 

1. Nella sezione dell'applicazione Blockchain Workbench selezionare il riquadro dell'applicazione che contiene il contratto su cui si vuole eseguire l'azione.
2. Selezionare il contratto nell'elenco. I dettagli sul contratto vengono visualizzati in sezioni diverse. 

    ![Dettagli sul contratto](media/blockchain-workbench-use/contract-details.png)

    | Sezione  | DESCRIZIONE  |
    |---------|---------|
    | Status | Elenca lo stato di avanzamento corrente all'interno delle fasi del contratto |
    | Dettagli | Valori correnti del contratto |
    | Azione | Informazioni dettagliate sull'ultima azione |
    | Attività | Cronologia delle transazioni del contratto |
    
3. Nella sezione **Azione** selezionare **Intervieni**.

4. I dettagli sullo stato corrente del contratto vengono visualizzati in un riquadro. Scegliere l'azione che si vuole eseguire nell'elenco a discesa. 

    ![Scegliere un'azione](media/blockchain-workbench-use/choose-action.png)

5. Selezionare **Intervieni** per avviare l'azione.
6. Se sono necessari parametri per l'azione, specificare i valori per l'azione.

    ![Intervieni](media/blockchain-workbench-use/take-action.png)

7. Selezionare **Intervieni** per eseguire l'azione.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Come risolvere i problemi relativi ad Azure Blockchain Workbench](blockchain-workbench-troubleshooting.md)