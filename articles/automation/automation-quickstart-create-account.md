---
title: Guida introduttiva di Azure - Creare un account di Automazione di Azure | Microsoft Docs
description: Questo articolo illustra come iniziare a creare un account di Automazione di Azure ed eseguire un runbook.
services: automation
ms.date: 04/04/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: a07c6ac524aa213519ace1ae204ac2d76db802aa
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/25/2020
ms.locfileid: "83836703"
---
# <a name="create-an-azure-automation-account"></a>Creare un account di Automazione di Azure

È possibile creare un account di Automazione di Azure tramite Azure, usando il portale di Azure, un'interfaccia utente basata su browser che consente l'accesso a una serie di risorse. Un account di Automazione può gestire le risorse in tutte le aree e le sottoscrizioni di uno specifico tenant. 

Questa guida di avvio rapido illustra la creazione di un account di Automazione e l'esecuzione di un runbook al suo interno. Se non si ha una sottoscrizione di Azure, creare un [account Azure gratuito](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) prima di iniziare.

## <a name="sign-in-to-azure"></a>Accedere ad Azure

[Accedere ad Azure](https://portal.azure.com).

## <a name="create-automation-account"></a>Creare l'account di Automazione

1. Scegliere un nome per l'account Azure. I nomi degli account di Automazione devono essere univoci in ogni area e gruppo di risorse. I nomi degli account di Automazione che sono stati eliminati potrebbero non essere subito disponibili.

    > [!NOTE]
    > Non è possibile cambiare il nome dell'account dopo che è stato immesso nell'interfaccia utente. 

2. Fare clic sul pulsante **Crea una risorsa** nell'angolo in alto a sinistra del portale di Azure.

3. Selezionare **IT e strumenti di gestione** e quindi selezionare **Automazione**.

4. Immettere le informazioni sull'account, incluso il nome dell'account selezionato. Per **Crea un account RunAs di Azure** scegliere **Sì** per abilitare automaticamente gli elementi che consentono di semplificare l'autenticazione in Azure. Al termine, fare clic su **Crea**, per avviare la distribuzione dell'account di Automazione.

    ![Immettere le informazioni sull'account di Automazione nella pagina](./media/automation-quickstart-create-account/create-automation-account-portal-blade.png)  

    > [!NOTE]
    > Per un elenco aggiornato delle località in cui è possibile distribuire un account di Automazione, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all).

5. Al termine della distribuzione, fare clic su **Tutti i servizi**.

6. Selezionare **Account di automazione** quindi scegliere l'account di Automazione creato.

    ![Panoramica dell'account di Automazione](./media/automation-quickstart-create-account/automation-account-overview.png)

## <a name="run-a-runbook"></a>Eseguire un runbook

Eseguire uno dei runbook di esercitazione.

1. Fare clic su **Runbook** in **Automazione processi**. Viene visualizzato l'elenco di runbook. Per impostazione predefinita, nell'account sono abilitati diversi runbook di esercitazione.

    ![Elenco dei runbook dell'account di Automazione](./media/automation-quickstart-create-account/automation-runbooks-overview.png)

1. Selezionare il runbook **AzureAutomationTutorialScript**. Questa azione apre la pagina di panoramica del runbook.

    ![Panoramica del runbook](./media/automation-quickstart-create-account/automation-tutorial-script-runbook-overview.png)

1. Fare clic su **Avvia**, quindi nella pagina Avvia runbook fare clic su **OK** per avviare il runbook.

    ![Pagina del processo del runbook](./media/automation-quickstart-create-account/automation-tutorial-script-job.png)

1. Quando lo stato del processo diventa `Running`, fare clic su **Output** o su **Tutti i log** per visualizzare l'output del processo del runbook. Per questo runbook di esercitazione, l'output è un elenco delle risorse di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questa guida introduttiva è stato distribuito un account di Automazione, è stato avviato il processo di un runbook e sono stati visualizzati i risultati del processo. Per altre informazioni su Automazione di Azure, passare alla guida introduttiva sulla creazione del primo runbook.

> [!div class="nextstepaction"]
> [Avvio rapido di Automazione: creare un runbook di Automazione di Azure](./automation-quickstart-create-runbook.md)

