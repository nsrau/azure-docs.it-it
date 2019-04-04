---
title: Ridistribuire Azure Stack Development Kit (ASDK) | Microsoft Docs
description: In questo articolo descrive come reinstallare il ASDK.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: ''
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 284e1ce3c3b9a63f3c25e85891b1d2688726183e
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58879982"
---
# <a name="redeploy-the-asdk"></a>Ridistribuire il ASDK
In questo articolo descrive come ridistribuire Azure Stack Development Kit (ASDK) in un ambiente non di produzione. Poiché non è supportato l'aggiornamento di ASDK, è necessario ridistribuirla completamente per spostare in una versione più recente. È anche possibile ridistribuire il ASDK ogni volta che si desidera solo ricominciare da zero.

> [!IMPORTANT]
> Non è supportato l'aggiornamento di ASDK a una nuova versione. È necessario ridistribuire il ASDK nel computer host kit di sviluppo ogni volta che si vuole valutare una versione più recente di Azure Stack.

## <a name="remove-azure-registration"></a>Rimuovere la registrazione di Azure 
Se è stato registrato in precedenza l'installazione ASDK con Azure, è necessario rimuovere la risorsa di registrazione prima di ridistribuire il ASDK. Registrare nuovamente il ASDK per abilitare la disponibilità degli elementi del Marketplace quando si ridistribuisce il ASDK. Se non è stato registrato in precedenza il ASDK nella sottoscrizione di Azure, è possibile ignorare questa sezione.

Per rimuovere la risorsa di registrazione, usare il **Remove-AzsRegistration** cmdlet per annullare la registrazione di Azure Stack. Quindi, usare il **Remove-AzureRMResourceGroup** cmdlet per eliminare il gruppo di risorse di Azure Stack dalla sottoscrizione di Azure:

1. Aprire una console PowerShell come amministratore in un computer dotato di accesso all'endpoint con privilegi. Per ASDK, ovvero il computer host kit di sviluppo.

2. Eseguire i comandi di PowerShell seguenti per annullare la registrazione dell'installazione ASDK ed eliminare le **azurestack** gruppo di risorse dalla sottoscrizione di Azure:

   ```Powershell    
   #Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Provide Azure subscription admin credentials
   Add-AzureRmAccount

   # Provide ASDK admin credentials
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

   # Unregister Azure Stack
   Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

   # Remove the Azure Stack resource group
   Remove-AzureRmResourceGroup -Name azurestack -Force
   ```

3. Richiesto per accedere alla sottoscrizione di Azure sia l'installazione locale di ASDK durante l'esecuzione dello script.
4. Al termine dell'esecuzione dello script, si dovrebbero vedere messaggi simili ai seguenti esempi:

    `De-Activating Azure Stack (this may take up to 10 minutes to complete).`
    `Your environment is now unable to syndicate items and is no longer reporting usage data.`
    `Remove registration resource from Azure...`
    `"Deleting the resource..." on target "/subscriptions/<subscription information>"`
    `********** End Log: Remove-AzsRegistration *********`



Azure Stack dovrebbe ora essere è stata annullata la registrazione dalla sottoscrizione di Azure. Inoltre, il gruppo di risorse azurestack creato durante la registrazione di ASDK con Azure, anche deve essere eliminato.

## <a name="deploy-the-asdk"></a>Distribuire il ASDK
Per ridistribuire Azure Stack, è necessario ricominciare da zero come descritto di seguito. I passaggi sono diversi a seconda se usato lo script di installazione (asdk installer.ps1) Azure Stack per l'installazione di ASDK.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Ridistribuire il ASDK lo script di installazione
1. Nel computer ASDK, aprire una console di PowerShell con privilegi elevata e passare allo script asdk installer.ps1 nel **AzureStack_Installer** directory situata in un'unità non di sistema. Eseguire lo script e fare clic su **riavvio**.

   ![Eseguire lo script asdk installer.ps1](media/asdk-redeploy/1.png)

2. Selezionare il sistema operativo di base (non **Azure Stack**) e fare clic su **successivo**.

   ![Riavviare il sistema operativo host](media/asdk-redeploy/2.png)

3. Dopo il riavvio nel sistema operativo di base di host kit di sviluppo, accedere come amministratore locale. Individuare ed eliminare le **C:\CloudBuilder.vhdx** file che è stato usato come parte della distribuzione precedente. 

4. Ripetere gli stessi passaggi che è stata eseguita a primo [distribuire il ASDK](asdk-install.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Ridistribuire il ASDK senza usare il programma di installazione
Se non si è stata utilizzata asdk installer.ps1 script per installare il ASDK, è necessario riconfigurare manualmente il computer host kit di sviluppo prima di ridistribuire il ASDK.

1. Avviare l'utilità Configurazione di sistema eseguendo **msconfig.exe** nel computer ASDK. Nel **Boot** scheda, selezionare sistema operativo del computer host (non Azure Stack), fare clic su **imposta come predefinito**e quindi fare clic su **OK**. Fare clic su **riavviare** quando richiesto.

      ![Impostare la configurazione di avvio](media/asdk-redeploy/4.png)

2. Dopo il riavvio nel sistema operativo di base di host kit di sviluppo, accedere come amministratore locale per il computer host kit di sviluppo. Individuare ed eliminare le **C:\CloudBuilder.vhdx** file che è stato usato come parte della distribuzione precedente. 

3. Ripetere gli stessi passaggi che è stata eseguita a primo [distribuire il ASDK usando PowerShell](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Passaggi successivi
[Registra le attività di distribuzione ASDK](asdk-post-deploy.md)




