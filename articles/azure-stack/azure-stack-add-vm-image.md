---
title: Aggiungere e rimuovere un'immagine di macchina virtuale in Azure Stack | Microsoft Docs
description: Aggiungere o rimuovere Windows o Linux VM immagine personalizzata della propria organizzazione per i tenant da utilizzare.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: deee5dc1f744025d3f01953a17ee914336100f9f
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57340695"
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>Adottare un'immagine di macchina virtuale disponibili in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

In Azure Stack, è possibile rendere disponibili immagini di macchine virtuali agli utenti. Queste immagini possono essere utilizzate dai modelli di Azure Resource Manager. È anche possibile aggiungerli all'interfaccia utente di Marketplace di Azure come un elemento del Marketplace. Usare un form dell'immagine di globale di Azure Marketplace o un'immagine personalizzata. L'immagine può essere aggiunta tramite il portale o Windows PowerShell.

## <a name="add-a-vm-image-through-the-portal"></a>Aggiungere un'immagine di macchina virtuale tramite il portale

> [!NOTE]  
> Con questo metodo, è necessario creare separatamente l'elemento del Marketplace.

Le immagini devono essere in grado di farvi riferimento da un URI di archiviazione blob. Preparare un'immagine del sistema operativo Windows o Linux in formato VHD (non VHDX) e quindi caricare l'immagine in un account di archiviazione in Azure o Azure Stack. Se l'immagine è già stato caricato nell'archiviazione blob di Azure o Azure Stack, è possibile ignorare il passaggio 1.

1. [Caricare un'immagine di VM Windows in Azure per distribuzioni di Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) o, per un'immagine Linux, seguire le istruzioni descritte [distribuire macchine virtuali in Azure Stack](azure-stack-linux.md). Prima di caricare l'immagine, è importante prendere in considerazione i fattori seguenti:

   - Azure Stack solo supporta la generazione di uno (1) della macchina virtuale nel disco fisso VHD formato. Il formato fisso strutture del disco logico in modo lineare all'interno del file, in modo che l'offset del disco X venga archiviato in corrispondenza dell'offset di blob X. Un piccolo piè di pagina alla fine del blob vengono descritte le proprietà del file VHD. Per verificare se il disco è fissa, usare il [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) comando di PowerShell.  

    > [!IMPORTANT]  
    >  Azure Stack non supporta i VHD dei dischi dinamici. Ridimensionamento di un disco dinamico collegato a una macchina virtuale lascia la macchina virtuale in stato di errore. Per attenuare questo problema, eliminare la macchina virtuale senza eliminare il disco della VM, un blob di disco rigido virtuale in un account di archiviazione. Converti il disco rigido virtuale da un disco dinamico a un disco a dimensione fissa e a ricreare la macchina virtuale.

   - È più efficiente per caricare un immagine su archiviazione blob di Azure Stack rispetto ad Azure nell'archivio blob perché richiede meno tempo per effettuare il push dell'immagine nel repository di immagini di Azure Stack.

   - Quando si carica il [immagine di macchina virtuale Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), assicurarsi di sostituire il **Login to Azure** passaggio con il [configurare l'ambiente PowerShell dell'operatore Azure Stack](azure-stack-powershell-configure-admin.md) passaggio.  

   - Prendere nota di archiviazione blob di URI in cui caricare l'immagine. L'URI dell'archiviazione blob ha il formato seguente: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* file con estensione vhd.

   - Per rendere accessibile in modo anonimo di blob, passare al contenitore blob dell'account di archiviazione in cui è stato caricato il VHD dell'immagine della macchina virtuale. Selezionare **Blob**, quindi selezionare **criterio di accesso**. Facoltativamente, è possibile generare una firma di accesso condiviso per il contenitore e includerlo come parte dell'URI del blob. Questo passaggio garantisce che il blob è disponibile per essere utilizzato per l'aggiunta di ciò come un'immagine. Se il blob non è accessibile in modo anonimo, l'immagine di macchina virtuale verrà creato a in stato di errore.

    ![Passare al BLOB dell'account di archiviazione](./media/azure-stack-add-vm-image/image1.png)

    ![Accesso al blob insieme al ruolo public](./media/azure-stack-add-vm-image/image2.png)

2. Accedere ad Azure Stack come operatore. Nel menu, selezionare **tutti i servizi** > **immagini** sotto **calcolo** > **Aggiungi**.

3. Sotto **Crea immagine**, immettere il percorso, nome, sottoscrizione, gruppo di risorse del disco del sistema operativo, tipo di sistema operativo, URI del blob di archiviazione, il tipo di Account e ospitare la memorizzazione nella cache. Quindi, selezionare **Create** per iniziare a creare l'immagine di macchina virtuale.

   ![Iniziare a creare l'immagine](./media/azure-stack-add-vm-image/image4.png)

   Quando l'immagine viene creata correttamente, lo stato di immagine di macchina virtuale diventa **Succeeded**.

4. Per rendere l'immagine di macchina virtuale più facilmente disponibili per l'utilizzo di utente nell'interfaccia utente, è buona norma [creare un elemento del Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Rimuovere un'immagine di macchina virtuale tramite il portale

1. Aprire il portale di amministrazione all'indirizzo [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

2. Selezionare **gestione Marketplace**e quindi selezionare la macchina virtuale da eliminare.

3. Fare clic su **Elimina**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Aggiungere un'immagine di VM in Marketplace usando PowerShell

> [!Note]  
> Quando si aggiunge un'immagine sarà disponibile solo per Gestione risorse di Azure basato su modelli e le distribuzioni di PowerShell. Per rendere disponibili per un'immagine un gli utenti come un elemento del marketplace, pubblicare l'elemento del marketplace seguendo i passaggi descritti nell'articolo, [creare e pubblicare un elemento del Marketplace](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-and-publish-marketplace-item)

1. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md).  

2. Accedere ad Azure Stack come operatore. Per istruzioni, vedere [accedi ad Azure Stack come operatore](azure-stack-powershell-configure-admin.md).

3. Aprire PowerShell con privilegi elevati ed eseguire:

  ```PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
  ```

  Il **Add-AzsPlatformimage** cmdlet consente di specificare i valori usati dai modelli di Azure Resource Manager per fare riferimento all'immagine di macchina virtuale. I valori includono:
  - **publisher**  
    Ad esempio: `Canonical`  
    Il segmento nome editore dell'immagine di macchina virtuale che gli utenti usano quando si distribuisce l'immagine. Ad esempio **Microsoft**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **offer**  
    Ad esempio: `UbuntuServer`  
    Il segmento nome offerta dell'immagine di macchina virtuale utilizzato dagli utenti quando distribuiscono l'immagine di macchina virtuale. Ad esempio **WindowsServer**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **sku**  
    Ad esempio: `14.04.3-LTS`  
    Il segmento nome SKU dell'immagine di macchina virtuale utilizzato dagli utenti quando distribuiscono l'immagine di macchina virtuale. Ad esempio **Datacenter2016**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **version**  
    Ad esempio: `1.0.0`  
    La versione dell'immagine di macchina virtuale utilizzato dagli utenti quando distribuiscono l'immagine di macchina virtuale. Questa versione è nel formato *\#.\#.\#*. Ad esempio **1.0.0**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **osType**  
    Ad esempio: `Linux`  
    OsType dell'immagine deve essere **Windows** oppure **Linux**.  
  - **OSUri**  
    Ad esempio: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    È possibile specificare un URI di archiviazione blob per un `osDisk`.  

    Per altre informazioni, vedere il riferimento di PowerShell per il [Add-AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet e il [New-DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) cmdlet.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Aggiungere un'immagine di macchina virtuale personalizzata al Marketplace tramite PowerShell
 
1. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md).

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

2. Se si usa **Active Directory Federation Services**, usare il cmdlet seguente:

  ```PowerShell
  # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAudience endpoint for your environment>"

  # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. Accedere ad Azure Stack come operatore. Per istruzioni, vedere [accedi ad Azure Stack come operatore](azure-stack-powershell-configure-admin.md).

4. Creare un account di archiviazione in Azure globale o Azure Stack per archiviare l'immagine di macchina virtuale personalizzata. Per istruzioni, vedere [Guida introduttiva: Caricare, scaricare ed elencare BLOB con il portale di Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Preparare un'immagine del sistema operativo Windows o Linux in formato VHD (non VHDX), caricare l'immagine nell'account di archiviazione e ottenere l'URI in cui l'immagine di macchina virtuale può essere recuperato tramite PowerShell.  

  ```PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ```

6. (Facoltativo) È possibile caricare un array di dischi di dati come parte dell'immagine di macchina virtuale. Creare i dischi di dati usando il cmdlet New-DataDiskObject. Aprire PowerShell da un prompt dei comandi con privilegi elevati ed eseguire:

  ```PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
  ```

7. Aprire PowerShell con privilegi elevati ed eseguire:

  ```PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
  ```

    Per altre informazioni sul cmdlet Add-AzsPlatformimage e New-DataDiskObject cmdlet, vedere Microsoft PowerShell [documentazione di Azure Stack operatore modulo](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Rimuovere un'immagine di macchina virtuale tramite PowerShell

Quando non è più necessario l'immagine di macchina virtuale che è stato caricato, è possibile eliminarlo dal Marketplace usando il cmdlet seguente:

1. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md).

2. Accedere ad Azure Stack come operatore.

3. Aprire PowerShell con privilegi elevati ed eseguire:

  ```PowerShell  
  Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
  ```
  Il **Remove-AzsPlatformImage** cmdlet consente di specificare i valori usati dai modelli di Azure Resource Manager per fare riferimento all'immagine di macchina virtuale. I valori includono:
  - **publisher**  
    Ad esempio: `Canonical`  
    Il segmento nome editore dell'immagine di macchina virtuale che gli utenti usano quando si distribuisce l'immagine. Ad esempio **Microsoft**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **offer**  
    Ad esempio: `UbuntuServer`  
    Il segmento nome offerta dell'immagine di macchina virtuale utilizzato dagli utenti quando distribuiscono l'immagine di macchina virtuale. Ad esempio **WindowsServer**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **sku**  
    Ad esempio: `14.04.3-LTS`  
    Il segmento nome SKU dell'immagine di macchina virtuale utilizzato dagli utenti quando distribuiscono l'immagine di macchina virtuale. Ad esempio **Datacenter2016**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **version**  
    Ad esempio: `1.0.0`  
    La versione dell'immagine di macchina virtuale utilizzato dagli utenti quando distribuiscono l'immagine di macchina virtuale. Questa versione è nel formato *\#.\#.\#*. Ad esempio **1.0.0**. Non includere uno spazio o altri caratteri speciali in questo campo.  
    
    Per altre informazioni sul cmdlet Remove-AzsPlatformImage, vedere Microsoft PowerShell [documentazione di Azure Stack operatore modulo](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>Passaggi successivi

[Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)
