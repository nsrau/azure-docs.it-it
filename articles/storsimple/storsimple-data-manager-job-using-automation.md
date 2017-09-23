---
title: Attivare un processo tramite Automazione di Azure | Microsoft Docs
description: Informazioni su come usare Automazione di Azure per attivare i processi di Gestore dati StorSimple (anteprima privata)
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 9691408bcd80afb6eba534f26749b76dd3bfe315
ms.contentlocale: it-it
ms.lasthandoff: 03/21/2017

---

# <a name="use-azure-automation-to-trigger-a-job-private-preview"></a>Attivare un processo tramite Automazione di Azure (anteprima privata)

In questo articolo viene descritto come usare Automazione di Azure per attivare un processo di Gestore dati StorSimple.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di disporre di:

*   Azure Powershell installato. [Scaricare Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Impostazioni di configurazione per l'inizializzazione del processo di trasformazione dei dati (le istruzioni per ottenere queste impostazioni sono incluse qui).
*   Una definizione del processo configurata correttamente in una risorsa dati ibrida all'interno di un gruppo di risorse.
*   Scaricare il file [zip](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) `DataTransformationApp.zip` dal repository GitHub.
*   Scaricare lo [script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Get-ConfigurationParams.ps1) `Get-ConfigurationParams.ps1` dal repository GitHub.
*   Scaricare lo [script](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) `Trigger-DataTransformation-Job.ps1` dal repository GitHub.

## <a name="step-by-step"></a>Procedura dettagliata

### <a name="get-azure-active-directory-permissions-for-the-automation-job-to-run-the-job-definition"></a>Ottenere le autorizzazioni di Azure Active Directory per il processo di automazione in modo da eseguire la definizione del processo

1. Per recuperare i parametri di configurazione di Active Directory, eseguire i passaggi seguenti:

    1. Aprire Windows PowerShell nel computer locale. Assicurarsi che [Azure PowerShell](https://azure.microsoft.com/downloads/) sia installato.
    1. Eseguire lo script `Get-ConfigurationParams.ps1` (nella cartella scaricata in precedenza). Digitare il comando seguente nella finestra di PowerShell:

        ```
        .\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```

        ActiveDirectoryKey è una password da usare successivamente. Immettere una password a propria scelta. AppName può essere una stringa qualsiasi.

2. Questo script restituisce come output i valori seguenti che devono essere usati durante l'attivazione del runbook dell'automazione. Prendere nota dei valori seguenti.

    - ID Client
    - ID tenant
    - Chiave di Active Directory (uguale a quella immessa in precedenza)
    - ID sottoscrizione

### <a name="set-up-the-automation-account"></a>Configurare l'account di automazione

1. Accedere ad Azure e aprire l'account di automazione.
2. Fare clic sul riquadro **Asset** per aprire l'elenco degli asset.
3. Fare clic nel riquadro **Moduli** per aprire l'elenco dei moduli.
4. Fare clic sul pulsante **+ Aggiungi un modulo** per avviare il pannello Aggiungi modulo.

    ![Impostazioni dell'account di automazione](./media/storsimple-data-manager-job-using-automation/add-module1m.png)

5. Dopo aver selezionato il file `DataTransformationApp.zip` dal computer locale, fare clic su **OK** per importare il modulo.

   Quando Automazione di Azure esegue l'importazione del modulo nell'account, estrae anche i metadati relativi al modulo. Questa operazione potrebbe richiedere alcuni minuti.

   ![Impostazioni dell'account di automazione](./media/storsimple-data-manager-job-using-automation/add-module2m.png)

   

6. Si riceverà una notifica per indicare che è in corso la distribuzione del modulo e un'altra notifica al termine dell'operazione.  È inoltre possibile controllare lo stato nel riquadro **Moduli**.

### <a name="to-import-the-runbook-that-triggers-the-job-definition"></a>Per importare il runbook che attiva la definizione del processo

1. Nel portale di Azure aprire l'account di automazione.
2. Fare clic nel riquadro **Runbook** per aprire l'elenco dei runbook.
3. Fare clic su **+ Aggiungi runbook** e poi **Importa un runbook esistente**.

   ![Importare un runbook esistente](./media/storsimple-data-manager-job-using-automation/import-a-runbook.png)

4. Fare clic su **File di runbook** e selezionare il file da importare `Trigger-DataTransformation-Job.ps1`.
5. Per importare il runbook fare clic su **Crea**. Il nuovo runbook verrà visualizzato nell'elenco dei runbook dell'account di automazione.
7. Fare clic sul runbook **Trigger-DataTransformation-Job** quindi su **Modifica**.
8. Fare clic su **Pubblica** quindi su **Sì** quando viene richiesta la conferma.


### <a name="to-run-the-runbook"></a>Per eseguire il runbook:
1. Nel portale di Azure aprire l'account di automazione.
2. Fare clic nel pannello **Runbook** per aprire l'elenco dei runbook.
3. Fare clic su **Trigger-DataTransformation-Job**.
4. Fare clic su **Avvia** per avviare il runbook.

   ![Avvia runbook](./media/storsimple-data-manager-job-using-automation/run-runbook1m.png)

5. Nel pannello **Avvia runbook** immettere tutti i parametri. Fare clic su **OK** per inviare il processo di trasformazione dati.

   ![Avvia runbook](./media/storsimple-data-manager-job-using-automation/run-runbook2m.png)


## <a name="next-steps"></a>Passaggi successivi

[Usare l'interfaccia utente di StorSimple Data Manager per la trasformazione dei dati](storsimple-data-manager-ui.md).
