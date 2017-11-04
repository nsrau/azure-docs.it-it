---
title: Aggiungere l'immagine di macchina virtuale predefinita a Azure Marketplace Stack | Documenti Microsoft
description: Aggiungere l'immagine di macchina virtuale di Windows Server 2016 predefinita Stack Azure Marketplace.
services: azure-stack
documentationcenter: 
author: SnehaGunda
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/10/2017
ms.author: sngun
ms.openlocfilehash: e5e7ab55800eccea98cbd71ae32bdc611ee3e961
ms.sourcegitcommit: 6acb46cfc07f8fade42aff1e3f1c578aa9150c73
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/18/2017
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>Aggiungere l'immagine di macchina virtuale di Windows Server 2016 a Stack Azure Marketplace

Per impostazione predefinita, nessuna immagine di macchina virtuale sono disponibile in Azure Marketplace dello Stack. Un operatore di Stack di Azure è necessario aggiungere un'immagine nel Marketplace agli utenti di accedere. È possibile aggiungere l'immagine di Windows Server 2016 a Azure Marketplace Stack utilizzando uno dei metodi seguenti:

* [Scaricare l'immagine da Azure Marketplace](#add-the-image-by-downloading-it-from-the-azure-marketplace). Utilizzare questa opzione se si opera in uno scenario connesso e registrato in Azure l'istanza dello Stack di Azure.

* [Aggiungere l'immagine con PowerShell](#add-the-image-by-using-powershell). Utilizzare questa opzione se è stato distribuito Azure Stack in uno scenario disconnesso, o in scenari con una connettività limitata.

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>Aggiungere l'immagine, scaricarlo da Azure Marketplace

1. Distribuire Azure Stack e quindi accedere al Kit di sviluppo dello Stack di Azure.

2. Selezionare **più servizi** > **gestione Marketplace** > **Aggiungi da Azure**. 

3. Trovare o cercare il **Datacenter di Windows Server 2016-Eval** immagine e quindi selezionare **scaricare**.

   ![Scaricare l'immagine da Azure](media/azure-stack-add-default-image/download-image.png)

Al termine dell'operazione di download, l'immagine è disponibile in **gestione Marketplace**. L'immagine è anche disponibile sotto **macchine virtuali**.

## <a name="add-the-image-by-using-powershell"></a>Aggiungere l'immagine con PowerShell

### <a name="prerequisites"></a>Prerequisiti 

Eseguire i seguenti prerequisiti, tramite il [kit di sviluppo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) o da un basati su Windows client esterno, se si è [connessi tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. Installare [moduli di PowerShell Azure compatibile Stack Azure](azure-stack-powershell-install.md).  

2. Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).  

3. Nella pagina Windows Server valutazioni [scaricare la versione di valutazione di Windows Server 2016](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016). Quando richiesto, selezionare la versione ISO del download. Registrare il percorso per il download, che viene utilizzato in un secondo momento nella procedura descritta in questo articolo. Questo passaggio richiede la connettività internet.  

### <a name="add-the-image-to-the-azure-stack-marketplace"></a>Aggiungere l'immagine a Stack Azure Marketplace
   
1. Importare i moduli di Azure Connect dello Stack e ComputeAdmin utilizzando i comandi seguenti:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Accedere all'ambiente dello Stack di Azure. Eseguire uno degli script di seguito, a seconda se è stato distribuito l'ambiente dello Stack di Azure tramite Azure Active Directory (Azure AD) o Active Directory Federation Services (ADFS). (Sostituire Azure AD `tenantName`, `GraphAudience` endpoint, e `ArmEndpoint` valori in base alla configurazione dell'ambiente.)  

   * **Azure Active Directory**. Usare il cmdlet seguente:

    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"
    
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

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```

   * **Active Directory Federation Services**. Usare il cmdlet seguente:
    
    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"

    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience `
      -EnableAdfsAuthentication:$true

   $TenantID = Get-AzsDirectoryTenantId `
     -ADFS `
     -EnvironmentName "AzureStackAdmin" 

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```
   
3. Aggiungere l'immagine di Windows Server 2016 a Stack Azure Marketplace. (Sostituire *fully_qualified_path_to_ISO* con il percorso di Windows Server 2016 ISO che è stato scaricato.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

Per assicurarsi che l'immagine di macchina virtuale di Windows Server 2016 include l'aggiornamento cumulativo più recente, includere il `IncludeLatestCU` parametro quando si esegue il `New-AzsServer2016VMImage` cmdlet. Per informazioni sui parametri consentiti per il `New-AzsServer2016VMImage` cmdlet, vedere [parametri](#parameters). Sono necessari circa un'ora per pubblicare l'immagine in Azure Marketplace dello Stack. 

## <a name="parameters"></a>Parametri

|Parametri di nuovo AzsServer2016VMImage|Obbligatorio?|Descrizione|
|-----|-----|------|
|ISOPath|Sì|Il percorso completo per Windows Server 2016 ISO scaricata.|
|net35|No|Installa il runtime di .NET 3.5 dell'immagine di Windows Server 2016. Per impostazione predefinita, questo valore è impostato su **true**.|
|Versione|No|Specifica **Core**, **completo**, o **entrambi** immagini di Windows Server 2016. Per impostazione predefinita, questo valore è impostato su **completo**.|
|VHDSizeInMB|No|Imposta le dimensioni (in MB) dell'immagine del disco rigido virtuale da aggiungere all'ambiente dello Stack di Azure. Per impostazione predefinita, questo valore è impostato su 40,960 MB.|
|CreateGalleryItem|No|Specifica se è necessario creare un elemento del Marketplace per l'immagine di Windows Server 2016. Per impostazione predefinita, questo valore è impostato su **true**.|
|location |No |Specifica il percorso in cui deve essere pubblicata l'immagine di Windows Server 2016.|
|IncludeLatestCU|No|Si applica l'aggiornamento cumulativo più recente di Windows Server 2016 per il nuovo disco rigido virtuale.|
|CUUri |No |Aggiornamento cumulativo di Windows Server 2016 set per l'esecuzione da un URI specifico. |
|CUPath |No |Aggiornamento cumulativo di Windows Server 2016 set per l'esecuzione da un percorso locale. Questa opzione è utile se è stata distribuita l'istanza dello Stack di Azure in un ambiente disconnesso.|

## <a name="next-steps"></a>Passaggi successivi

[Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)
