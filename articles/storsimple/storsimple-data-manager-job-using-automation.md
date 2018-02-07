---
title: Usare Automazione di Azure per avviare un processo in StorSimple Data Manager | Microsoft Docs
description: Informazioni su come usare Automazione di Azure per attivare i processi di StorSimple Data Manager
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 1e5fcbee664271058ac1c7fa80bb285e09b8579a
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/17/2018
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Usare Automazione di Azure per attivare un processo

Questo articolo illustra come usare la funzione di trasformazione dei dati all'interno del servizio StorSimple Data Manager per trasformare i dati del dispositivo StorSimple. È possibile avviare un processo di trasformazione dei dati in due modi: 

 - Usare .NET SDK
 - Usare un runbook di Automazione di Azure
 
Questo articolo illustra come creare un runbook di Automazione di Azure e usarlo per avviare un processo di trasformazione dei dati. Per altre informazioni su come avviare la trasformazione dei dati tramite .NET SDK, passare a [Usare .NET SDK per attivare processi di trasformazione dei dati](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di disporre di:

*   Azure PowerShell installato nel computer client. [Scaricare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).
*   Una definizione di processo configurata correttamente nel servizio StorSimple Data Manager all'interno di un gruppo di risorse.
*   File [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) scaricato dal repository GitHub. 
*   Script [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) scaricato dal repository GitHub.

## <a name="step-by-step-procedure"></a>Procedura dettagliata

### <a name="set-up-the-automation-account"></a>Configurare l'account di Automazione

1. Creare un account di automazione RunAs di Azure nel portale di Azure. A tale scopo, passare ad **Azure Marketplace > Tutto** e quindi cercare **Automazione**. Selezionare **Account di automazione**.

    ![Creare un account di Automazione RunAs](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Per aggiungere un nuovo account di automazione, fare clic su **+ Aggiungi**.

    ![Creare un account di Automazione RunAs](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. In **Aggiungi account di Automazione**:

    1. Specificare il **nome** dell'account di automazione.
    2. Selezionare la **sottoscrizione** collegata al servizio StorSimple Data Manager.
    3. Creare un nuovo gruppo di risorse o selezionarne uno esistente.
    4. Selezionare un **percorso**.
    5. Lasciare selezionata l'opzione predefinita **Crea account RunAs**.
    6. Per ottenere un collegamento per l'accesso rapido nel dashboard, selezionare **Aggiungi al dashboard**. Fare clic su **Crea**.

    ![Creare un account di Automazione RunAs](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
    Quando la creazione dell'account di automazione è completata, si riceve una notifica.
    
    ![Notifica relativa alla distribuzione dell'account di Automazione](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

    Per altre informazioni, vedere [Creare un account RunAs](../automation/automation-create-runas-account.md).

3. Nell'account appena creato passare a **Risorse condivise > Moduli** e fare clic su **+ Aggiungi modulo**.

    ![Importare il modulo 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Passare al percorso del file `DataTransformationApp.zip` dal computer locale, quindi selezionare e aprire il modulo. Fare clic su **OK** per importare il modulo.

    ![Importare il modulo 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   Quando Automazione di Azure esegue l'importazione del modulo nell'account, estrae anche i metadati relativi al modulo. Questa operazione potrebbe richiedere alcuni minuti.

   ![Importare il modulo 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Si riceverà una notifica per indicare che è in corso la distribuzione del modulo e un'altra notifica al termine dell'operazione.  Lo stato in **Moduli** passerà a **Disponibile**.

    ![Importare il modulo 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importare, pubblicare ed eseguire il runbook di automazione

Seguire questa procedura per importare, pubblicare ed eseguire il runbook per attivare la definizione del processo.

1. Nel portale di Azure aprire l'account di automazione. Passare ad **Automazione processi > Runbook** e fare clic su **+ Aggiungi runbook**.

    ![Aggiungere un runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. In **Aggiungi runbook** fare clic su **Importa un runbook esistente**.

3. Selezionare il file di script di Azure PowerShell `Trigger-DataTransformation-Job.ps1` in **Runbook file**. Il tipo di runbook viene selezionato automaticamente. Specificare un nome e una descrizione facoltativa per il runbook. Fare clic su **Crea**.

    ![Aggiungere un runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Il nuovo runbook verrà visualizzato nell'elenco dei runbook dell'account di automazione. Selezionare e fare clic su questo runbook.

    ![Aggiungere un runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Modificare il runbook e fare clic su **Riquadro di test**.

    ![Aggiungere un runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Specificare i parametri, ad esempio il nome del servizio StorSimple Data Manager, il gruppo di risorse associato e il nome della definizione del processo. **Avviare** il test. Il report viene generato al termine dell'esecuzione. Per altre informazioni, vedere [Testare il runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Aggiungere un runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Esaminare l'output dal runbook nel riquadro di test. Se soddisfatti, chiudere il riquadro. Fare clic su **Pubblica** e, quando viene richiesta la conferma, confermare e pubblicare il runbook.

    ![Aggiungere un runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Tornare a **Runbook** e selezionare il runbook appena creato.

    ![Aggiungere un runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Avviare** il runbook. In **Avvia runbook** immettere tutti i parametri. Fare clic su **OK** per inviare e avviare il processo di trasformazione dei dati.

10. Per monitorare l'avanzamento del processo nel portale di Azure, passare a **Processi** nell'area personale del servizio StorSimple Data Manager. Selezionare e fare clic sul processo per visualizzarne i dettagli.

    ![Aggiungere un runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Passaggi successivi

[Usare l'interfaccia utente di StorSimple Data Manager per la trasformazione dei dati](storsimple-data-manager-ui.md).