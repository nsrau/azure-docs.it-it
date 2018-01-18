---
title: Aggiungere un'immagine di macchina virtuale allo Stack di Azure | Documenti Microsoft
description: Aggiungere Windows o Linux VM immagine personalizzata dell'organizzazione per i tenant da utilizzare.
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/17/2018
ms.author: mabrigg
ms.openlocfilehash: 3b228452d416bbb2c54243b95292f7e1198af14f
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/18/2018
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>Creare un'immagine di macchina virtuale personalizzata disponibile nello Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Nello Stack di Azure, gli operatori possono rendere le immagini di macchina virtuale personalizzata disponibile agli utenti. Queste immagini possono fare riferimento i modelli di gestione risorse di Azure oppure è possibile aggiungerli all'interfaccia utente Azure Marketplace come un elemento del Marketplace.

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>Aggiungere un'immagine di macchina virtuale al Marketplace tramite PowerShell

Eseguire i seguenti prerequisiti, tramite il [kit di sviluppo](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) o da un basati su Windows client esterno, se si è [connessi tramite VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md).  

2. Scaricare il [gli strumenti necessari per lavorare con Azure Stack](azure-stack-powershell-download.md).  

3. Preparare un'immagine di disco rigido virtuale del sistema operativo Windows o Linux nel formato VHD (non usare il formato VHDX).

   * Per le immagini di Windows, per istruzioni sulla preparazione dell'immagine, vedere [caricare un'immagine di macchina virtuale Windows Azure per le distribuzioni di gestione risorse](../virtual-machines/windows/upload-generalized-managed.md).

   * Per le immagini Linux, vedere [macchine virtuali Linux di distribuire in Azure Stack](azure-stack-linux.md). Completare i passaggi per preparare l'immagine o utilizzare un'immagine Linux Stack Azure esistente, come descritto nell'articolo.    

   Stack di Azure supporta il formato di disco rigido virtuale del disco fisso. Il formato fisso strutture del disco logico in modo lineare all'interno del file, in modo tale offset del disco X venga archiviato all'offset del blob X. Un piccolo piè di pagina alla fine del blob vengono descritte le proprietà del file VHD. Per verificare se il disco è fisso, utilizzare il [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) comando di PowerShell.  

   > [!IMPORTANT]
   >  Stack di Azure non supporta dischi rigidi virtuali disco dinamico. Ridimensionamento di un disco dinamico è associato a una macchina virtuale verrà lasciare la macchina virtuale in uno stato di errore. Per evitare questo problema, eliminare la macchina virtuale senza eliminare disco della macchina virtuale, un blob del disco rigido virtuale in un account di archiviazione. Converti il disco rigido virtuale da un disco dinamico a un disco fisso e ricreare la macchina virtuale.

Per aggiungere l'immagine a Azure Marketplace dello Stack, completare i passaggi seguenti:

1. Importare i moduli Connect e ComputeAdmin:

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
          -EnvironmentName AzureStackAdmin

        Login-AzureRmAccount `
          -EnvironmentName "AzureStackAdmin" `
          -TenantId $TenantID
        ```

3. Aggiungere l'immagine di macchina virtuale tramite la chiamata di `Add-AzsVMImage` cmdlet. Nel `Add-AzsVMImage` cmdlet, specificare `osType` come Windows o Linux. Includere il server di pubblicazione, offerta, SKU e versione dell'immagine di macchina virtuale. Per informazioni sui parametri consentiti, vedere [parametri](#parameters). I parametri vengono utilizzati dai modelli di gestione risorse di Azure per fare riferimento all'immagine di macchina virtuale. L'esempio seguente richiama lo script:

  ```powershell
  Add-AzsVMImage `
    -publisher "Canonical" `
    -offer "UbuntuServer" `
    -sku "14.04.3-LTS" `
    -version "1.0.0" `
    -osType Linux `
    -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
  ```

Il comando esegue le operazioni seguenti:

* Esegue l'autenticazione per l'ambiente dello Stack di Azure.
* Carica il disco rigido virtuale locale in un account di archiviazione temporanea appena creata.
* Aggiunge l'immagine di macchina virtuale per il repository di immagini di macchina virtuale.
* Crea un elemento del Marketplace.

Per verificare che il comando eseguito correttamente, nel portale, andare al Marketplace. Verificare che sia disponibile nell'immagine di macchina virtuale di **macchine virtuali** categoria.

![Immagine di macchina virtuale aggiunto correttamente](./media/azure-stack-add-vm-image/image5.PNG)

## <a name="remove-a-vm-image-by-using-powershell"></a>Rimuove un'immagine di macchina virtuale mediante PowerShell

Quando non è più necessario l'immagine di macchina virtuale che è stato caricato, è possibile eliminare il Marketplace tramite il cmdlet seguente:

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>Parametri

| Parametro | DESCRIZIONE |
| --- | --- |
| **publisher** |Il server di pubblicazione segmento corrispondente al nome dell'immagine di macchina virtuale da parte degli utenti durante la distribuzione dell'immagine. Un esempio è **Microsoft**. Non includere uno spazio o altri caratteri speciali in questo campo. |
| **offer** |L'offerta segmento corrispondente al nome dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine di macchina virtuale. Un esempio è **WindowsServer**. Non includere uno spazio o altri caratteri speciali in questo campo. |
| **sku** |Segmento del nome SKU dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine di macchina virtuale. Un esempio è **Datacenter2016**. Non includere uno spazio o altri caratteri speciali in questo campo. |
| **version** |Versione dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine di macchina virtuale. Questa versione è nel formato *\#.\#.\#*. Un esempio è **1.0.0**. Non includere uno spazio o altri caratteri speciali in questo campo. |
| **osType** |OsType dell'immagine deve essere **Windows** o **Linux**. |
| **osDiskLocalPath** |Il percorso locale per il disco del sistema operativo VHD che si sta caricando come immagine di macchina virtuale allo Stack di Azure. |
| **dataDiskLocalPaths** |Matrice facoltativa dei percorsi locali per i dischi dati che possono essere caricati come parte dell'immagine di macchina virtuale. |
| **CreateGalleryItem** |Flag booleano che determina se creare un elemento nel Marketplace. Per impostazione predefinita, viene impostata su **true**. |
| **title** |Il nome visualizzato dell'elemento del Marketplace. Per impostazione predefinita, viene impostata sul `Publisher-Offer-Sku` valore dell'immagine di macchina virtuale. |
| **description** |La descrizione dell'elemento del Marketplace. |
| **location** |Il percorso in cui pubblicare l'immagine di macchina virtuale. Per impostazione predefinita, questo valore è impostato su **locale**.|
| **osDiskBlobURI** |(Facoltativo) Questo script accetta anche un URI di archiviazione Blob per `osDisk`. |
| **dataDiskBlobURIs** |(Facoltativo) Questo script accetta inoltre una matrice di URI di archiviazione Blob per l'aggiunta di dischi dati dell'immagine. |

## <a name="add-a-vm-image-through-the-portal"></a>Aggiungere un'immagine di macchina virtuale tramite il portale

> [!NOTE]
> Con questo metodo, è necessario creare l'elemento del Marketplace separatamente.

Le immagini devono essere in grado di fare riferimento a un URI di archiviazione Blob. Preparare un'immagine del sistema operativo Windows o Linux nel formato VHD (non VHDX) e quindi caricare l'immagine a un account di archiviazione in Azure o Azure Stack. Se l'immagine è già stata caricata nell'archiviazione Blob in Azure o Azure Stack, è possibile ignorare il passaggio 1.

1. [Caricare un'immagine di macchina virtuale Windows Azure per le distribuzioni di gestione risorse](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) oppure, per un'immagine Linux, seguire le istruzioni descritte in [macchine virtuali Linux di distribuire in Azure Stack](azure-stack-linux.md). Prima di caricare l'immagine, è importante prendere in considerazione i fattori seguenti:

   * Stack di Azure supporta il formato di disco rigido virtuale del disco fisso. Il formato fisso strutture del disco logico in modo lineare all'interno del file, in modo tale offset del disco X venga archiviato all'offset del blob X. Un piccolo piè di pagina alla fine del blob vengono descritte le proprietà del file VHD. Per verificare se il disco è fisso, utilizzare il [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) comando di PowerShell.  

    > [!IMPORTANT]
   >  Stack di Azure non supporta dischi rigidi virtuali disco dinamico. Ridimensionamento di un disco dinamico è associato a una macchina virtuale verrà lasciare la macchina virtuale in uno stato di errore. Per evitare questo problema, eliminare la macchina virtuale senza eliminare disco della macchina virtuale, un blob del disco rigido virtuale in un account di archiviazione. Converti il disco rigido virtuale da un disco dinamico a un disco fisso e ricreare la macchina virtuale.

   * È più efficiente per caricare un'immagine nell'archiviazione Blob di Azure dello Stack di archiviazione Blob di Azure perché richiede meno tempo per effettuare il push dell'immagine nel repository immagini di Azure Stack.

   * Quando si carica il [immagine di macchina virtuale Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), assicurarsi di sostituire il **account di accesso da Azure** passaggio con il [configurare l'ambiente di PowerShell dell'operatore Azure Stack](azure-stack-powershell-configure-admin.md) passaggio.  

   * Prendere nota di archiviazione Blob di URI in cui caricare l'immagine. L'URI di archiviazione Blob ha il seguente formato:  *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* con estensione vhd.

   * Per rendere il blob è accessibile in modo anonimo, passare al contenitore di blob account di archiviazione in cui è stato caricato l'immagine di macchina virtuale, disco rigido virtuale. Selezionare **Blob**, quindi selezionare **criteri di accesso**. Facoltativamente, è possibile invece generare una firma di accesso condiviso per il contenitore e includerlo come parte dell'URI del blob.

   ![Passare al BLOB di account di archiviazione](./media/azure-stack-add-vm-image/image1.png)

   ![Impostare l'accesso su public blob](./media/azure-stack-add-vm-image/image2.png)

2. Accedi allo Stack di Azure come operatore. Nel menu, selezionare **più servizi** > **i provider di risorse**. Selezionare quindi **calcolo** > **immagini di macchina virtuale** > **Aggiungi**.

3. In **aggiungere un'immagine di macchina virtuale**, immettere il server di pubblicazione, l'offerta, SKU e versione dell'immagine della macchina virtuale. Questi segmenti dei nomi di fare riferimento all'immagine di macchina virtuale in modelli di gestione risorse. Assicurarsi di selezionare il **osType** valore correttamente. Per **URI Blob del disco del sistema operativo**, immettere l'URI del Blob in cui l'immagine è stata caricata. Selezionare quindi **crea** per iniziare a creare l'immagine di macchina virtuale.

   ![Iniziare a creare l'immagine](./media/azure-stack-add-vm-image/image4.png)

   Quando l'immagine viene creata correttamente, lo stato di immagine di macchina virtuale viene modificato in **Succeeded**.

4. Per rendere l'immagine di macchina virtuale più facilmente disponibile per l'utilizzo di utente nell'interfaccia utente, è buona norma [creare un elemento del Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Passaggi successivi

[Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)
