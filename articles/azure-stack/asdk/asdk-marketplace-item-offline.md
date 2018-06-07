---
title: Aggiungere un elemento del marketplace dello Stack di Azure da un'origine locale | Documenti Microsoft
description: Viene descritto come aggiungere un'immagine del sistema operativo locale a Azure Marketplace dello Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/20/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: a093e60718881b2fe9ca70df7596e8963dc55d9f
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808044"
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>Esercitazione: aggiungere un elemento del marketplace dello Stack di Azure da un'origine locale

Un operatore di Stack di Azure, la prima operazione da eseguire per consentire agli utenti di distribuire una macchina virtuale (VM) consiste nell'aggiungere un'immagine di macchina virtuale nel Marketplace dello Stack di Azure. Per impostazione predefinita, non viene pubblicato nel Marketplace dello Stack di Azure, ma è possibile caricare le immagini ISO di VM per renderli disponibili agli utenti. Utilizzare questa opzione se è stato distribuito Azure Stack in uno scenario disconnesso o in scenari con una connettività limitata.

In questa esercitazione è scaricare un'immagine di macchina virtuale di Windows Server 2016 da di Windows Server valutazioni pagina e caricarlo nel Marketplace dello Stack di Azure.

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Aggiungere un'immagine di macchina virtuale di Windows Server 2016
> * Verificare che l'immagine di macchina virtuale è disponibile 
> * Immagine di macchina virtuale di test

## <a name="prerequisites"></a>Prerequisiti

Per completare questa esercitazione:

- Installare il [i moduli di Azure Stack-compatibile con Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell)
- Scaricare il [gli strumenti di Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools)
- Scaricare il [immagine ISO di macchina virtuale Windows Server 2106](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016) dalla pagina di Windows Server valutazioni

## <a name="add-a-windows-server-vm-image"></a>Aggiungere un'immagine di macchina virtuale Windows Server
È possibile pubblicare l'immagine di Windows Server 2016 nel Marketplace dello Stack di Azure mediante l'aggiunta di un'immagine ISO scaricata in precedenza tramite PowerShell. 

Utilizzare questa opzione se è stato distribuito Azure Stack in uno scenario disconnesso o in scenari con una connettività limitata.

1. [Installare PowerShell per Azure Stack](../azure-stack-powershell-install.md).

  ```PowerShell  
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ```

2. Se si utilizza **Active Directory Federation Services**, usare il cmdlet seguente:

  ```PowerShell
  # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. Accedi allo Stack di Azure come operatore. Per istruzioni, vedere [Accedi allo Stack di Azure come operatore](../azure-stack-powershell-configure-admin.md).

   ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

4. Aggiungere l'immagine di Windows Server 2016 nel Marketplace dello Stack di Azure.

    Il **Add-AzsPlatformimage** cmdlet usato per aggiungere l'immagine specifica i valori usati dai modelli di gestione risorse di Azure per fare riferimento all'immagine di macchina virtuale.
    
    I valori includono:
    
  - **publisher**  
    Ad esempio: `Microsoft`  
    Il server di pubblicazione segmento corrispondente al nome dell'immagine di macchina virtuale da parte degli utenti durante la distribuzione dell'immagine. Un esempio è **Microsoft**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **offer**  
    Ad esempio: `WindowsServer`  
    L'offerta segmento corrispondente al nome dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine di macchina virtuale. Un esempio è **WindowsServer**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **sku**  
    Ad esempio: `Datacenter2016`  
    Segmento del nome SKU dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine di macchina virtuale. Un esempio è **Datacenter2016**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **version**  
    Ad esempio: `1.0.0`  
    Versione dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine di macchina virtuale. Questa versione è nel formato *\#.\#.\#*. Un esempio è **1.0.0**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **osType**  
    Ad esempio: `Windows`  
    OsType dell'immagine deve essere **Windows** o **Linux**. Sostituire *fully_qualified_path_to_ISO* con il percorso di Windows Server 2016 ISO che è stato scaricato. 
  - **OSUri**  
    Ad esempio: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    È possibile specificare un URI di archiviazione blob per un `osDisk`. In tal caso, è necessario specificare la posizione in cui è archiviata l'immagine che è stato scaricato.

    Per altre informazioni, vedere la Guida di riferimento di PowerShell per la [Add-AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet.

    Aprire PowerShell con un prompt dei comandi con privilegi elevati ed eseguire:

      ````PowerShell  
        Add-AzsPlatformimage -publisher "Microsoft" -offer "WindowsServer" -sku "Datacenter2016" -version "1.0.0” -OSType "Windows" -OSUri "<fully_qualified_path_to_ISO>"
      ````  

## <a name="verify-the-marketplace-item-is-available"></a>Verificare che sia disponibile l'elemento del marketplace
Utilizzare questi passaggi per verificare che la nuova immagine di macchina virtuale è disponibile in marketplace dello Stack di Azure.

1. Accedi ai [portale di amministrazione di ASDK](https://adminportal.local.azurestack.external).

2. Selezionare **altri servizi** > **Marketplace gestione**. 

3. Verificare che l'immagine di macchina virtuale di Windows Server 2016 Data Center è stato aggiunto correttamente.

   ![Immagine scaricata da Azure](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>Immagine di macchina virtuale di test
Un operatore di Stack di Azure, è possibile usare il [portale dell'amministratore](https://adminportal.local.azurestack.external) per creare un test di convalidare l'immagine della macchina virtuale è stata resa disponibile correttamente. 

1. Accedi ai [portale di amministrazione di ASDK](https://adminportal.local.azurestack.external).

2. Fare clic su **nuove** > **calcolo** > **Data Center di Windows Server 2016** > **creare**.  
 ![Creare una macchina virtuale dall'immagine del marketplace](media/asdk-marketplace-item/new-compute.png)

3. Nel **nozioni di base** blade, immettere le informazioni seguenti e quindi fare clic su **OK**:
  - **Nome**: test-vm-1
  - **Nome utente**: AdminTestUser
  - **Password**: AzS TestVM01
  - **Sottoscrizione**: accettare la sottoscrizione di Provider predefinito
  - **Gruppo di risorse**: test-vm-rg
  - **Percorso**: locale

4. Nel **scegliere una dimensione** pannello, fare clic su **A1 Standard**, quindi fare clic su **selezionare**.  

5. Nel **impostazioni** pannello, accettare le impostazioni predefinite e fare clic su **OK**

6. Nel **riepilogo** pannello, fare clic su **OK** per creare la macchina virtuale.  
> [!NOTE]
> Distribuzione della macchina virtuale richiede alcuni minuti.

7. Per visualizzare per la nuova macchina virtuale, fare clic su **macchine virtuali**, quindi cercare **test-vm-1** e fare clic sul relativo nome.
    ![Immagine di macchina virtuale di test prima visualizzata](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Pulire le risorse
Dopo aver correttamente eseguito l'accesso alla macchina virtuale e verificato il corretto funzionamento dell'immagine di test, è necessario eliminare il gruppo di risorse di test. Ciò consente di liberare risorse limitate disponibili per le installazioni ASDK a nodo singolo.

Quando non è più necessario, eliminare il gruppo di risorse, macchina virtuale e tutte le risorse correlate procedendo come segue:

1. Accedi ai [portale di amministrazione di ASDK](https://adminportal.local.azurestack.external).
2. Fare clic su **gruppi di risorse** > **test-vm-rg** > **Elimina gruppo di risorse**.
3. Tipo di **test-vm-rg** come nome del gruppo di risorse e quindi fare clic su **eliminare**.

## <a name="next-steps"></a>Passaggi successivi

Questa esercitazione illustra come:

> [!div class="checklist"]
> * Aggiungere un'immagine di macchina virtuale di Windows Server 2016
> * Verificare che l'immagine di macchina virtuale è disponibile 
> * Immagine di macchina virtuale di test

Passare alla prossima esercitazione per imparare a creare un'offerta di Azure Stack e un piano.

> [!div class="nextstepaction"]
> [Offerta di servizi IaaS di Azure Stack](asdk-offer-services.md)
