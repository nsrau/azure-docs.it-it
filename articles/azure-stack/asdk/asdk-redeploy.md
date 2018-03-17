---
title: Ridistribuire il Kit di sviluppo di Azure Stack (ASDK) | Documenti Microsoft
description: In questa esercitazione imparare a reinstallare il ASDK.
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 579414f79da29a443ddaf8e167bf3889a647f33d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-redeploy-the-asdk"></a>Esercitazione: ridistribuire il ASDK
In questa esercitazione, è illustrato come ridistribuire Azure Stack Development Kit (ASDK) in un ambiente non di produzione. Perché non è supportato l'aggiornamento di ASDK, è necessario ridistribuirla completamente per spostare in una versione più recente. È anche possibile ridistribuire il ASDK ogni volta che si desidera iniziare da zero.

> [!IMPORTANT]
> Non è supportato l'aggiornamento di ASDK a una nuova versione. È necessario ridistribuire il ASDK nel computer host kit sviluppo ogni volta che si desidera valutare una versione più recente dello Stack di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Rimuovere la registrazione di Azure 
> * Ridistribuire la ASDK

## <a name="remove-azure-registration"></a>Rimuovere la registrazione di Azure 
Se sono state registrate in precedenza l'installazione ASDK con Azure, è consigliabile rimuovere la risorsa di registrazione prima di ridistribuire il ASDK. Registrare nuovamente il ASDK per consentire la diffusione marketplace quando si ridistribuisce il ASDK. Se non è stato registrato in precedenza il ASDK con la sottoscrizione di Azure, è possibile ignorare questa sezione.

Per rimuovere la risorsa di registrazione, usare il **Remove-AzsRegistration** cmdlet per annullare la registrazione dello Stack di Azure. Quindi, usare il **Remove-AzureRMRsourceGroup** per eliminare il gruppo di risorse di Azure Stack dalla sottoscrizione di Azure:

1. Aprire la console PowerShell come amministratore in un computer dotato di accesso all'endpoint con privilegi. Per ASDK, ovvero il computer host kit di sviluppo.

2. Eseguire i comandi di PowerShell seguenti per annullare la registrazione dell'installazione ASDK ed eliminare le **azurestack** gruppo di risorse dalla sottoscrizione di Azure:

  ```Powershell    
  #Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Provide Azure subscription admin credentials
  Login-AzureRmAccount

  # Provide ASDK admin credentials
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

  # Unregister Azure Stack
  Remove-AzsRegistration `
      -CloudAdminCredential $YourCloudAdminCredential `
      -PrivilegedEndpoint AzS-ERCS01

  # Remove the Azure Stack resource group
  Remove-AzureRmResourceGroup -Name azurestack -Force
  ```

3. Richiesto per l'accesso alla sottoscrizione di Azure sia l'installazione locale di ASDK quando l'esecuzione dello script.
4. Al completamento dello script, si dovrebbero essere visualizzati messaggi simili ai seguenti esempi:

    ` De-Activating Azure Stack (this may take up to 10 minutes to complete).` ` Your environment is now unable to syndicate items and is no longer reporting usage data.` ` Remove registration resource from Azure...` ` "Deleting the resource..." on target "/subscriptions/<subscription information>"` ` ********** End Log: Remove-AzsRegistration ********* `



Stack di Azure dovrebbe ora essere correttamente annullata la registrazione dalla sottoscrizione di Azure. Inoltre, il gruppo di risorse azurestack, creato quando il ASDK è registrato in Azure, anche deve essere eliminato.

## <a name="redeploy-the-asdk"></a>Ridistribuire la ASDK
Per ridistribuire dello Stack di Azure, è necessario ricominciare da zero come descritto di seguito. I passaggi sono diversi a seconda del fatto usato lo script di installazione (asdk-installer.ps1) dello Stack di Azure per installare il ASDK.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Ridistribuire ASDK usando lo script di installazione
1. Nel computer ASDK, aprire una console di PowerShell con privilegi elevata e passare allo script asdk installer.ps1 nel **AzureStack_Installer** directory situata in un'unità non di sistema. Eseguire lo script e fare clic su **riavviare**.

   ![Eseguire lo script asdk installer.ps1](media/asdk-redeploy/1.png)

2. Selezionare il sistema operativo di base (non **Azure Stack**) e fare clic su **Avanti**.

   ![Riavviare il sistema operativo host](media/asdk-redeploy/2.png)

3. Dopo che l'host di kit sviluppo riavvio nel sistema operativo di base, accedere come amministratore locale. Individuare ed eliminare le **C:\CloudBuilder.vhdx** file che è stato utilizzato come parte della distribuzione precedente. 

4. Ripetere gli stessi passaggi eseguiti al primo [distribuire l'ASDK](asdk-deploy.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Ridistribuire la ASDK senza utilizzare il programma di installazione
Se è stato utilizzato lo script asdk installer.ps1 non installare il ASDK, è necessario riconfigurare manualmente il computer host kit sviluppo prima di ridistribuire il ASDK.

1. Avviare l'utilità di configurazione di sistema eseguendo **msconfig.exe** nel computer ASDK. Nel **avvio** scheda, selezionare il sistema operativo computer host (non Azure Stack), fare clic su **imposta come predefinito**, quindi fare clic su **OK**. Fare clic su **riavviare** quando richiesto.

      ![Impostare la configurazione di avvio](media/asdk-redeploy/4.png)

2. Dopo che l'host di kit sviluppo riavvio nel sistema operativo di base, accedere come amministratore locale per il computer host kit di sviluppo. Individuare ed eliminare le **C:\CloudBuilder.vhdx** file che è stato utilizzato come parte della distribuzione precedente. 

3. Ripetere gli stessi passaggi eseguiti al primo [distribuire ASDK tramite PowerShell](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Passaggi successivi
Questa esercitazione illustra come:

> [!div class="checklist"]
> * Rimuovere la registrazione di Azure 
> * Ridistribuire la ASDK

Passare alla prossima esercitazione per informazioni su come aggiungere un elemento del marketplace dello Stack di Azure.

> [!div class="nextstepaction"]
> [Aggiungere un elemento del marketplace Azure Stack](asdk-marketplace-item.md)




