---
title: Aggiungere e rimuovere un'immagine di macchina virtuale in Azure Stack | Documenti Microsoft
description: Aggiungere o rimuovere Windows o Linux VM immagine personalizzata dell'organizzazione per i tenant da utilizzare.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 06/27/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.openlocfilehash: 8dd77dd3431f1be2b8edd8b51929c21b1d5bcd88
ms.sourcegitcommit: d1eefa436e434a541e02d938d9cb9fcef4e62604
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2018
ms.locfileid: "37081351"
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>Rendere disponibile nello Stack di Azure un'immagine di macchina virtuale

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Nello Stack di Azure, si possono rendere le immagini di macchina virtuale disponibili agli utenti. Queste immagini possono fare riferimento i modelli di Azure Resource Manager oppure è possibile aggiungerli all'interfaccia utente Azure Marketplace come un elemento del Marketplace. È possibile utilizzare un formato immagine globale Azure Marketplace o aggiungere un'immagine personalizzata. È possibile aggiungere una macchina virtuale tramite il portale o Windows PowerShell.

## <a name="add-a-vm-image-through-the-portal"></a>Aggiungere un'immagine di macchina virtuale tramite il portale

> [!NOTE]
> Con questo metodo, è necessario creare l'elemento del Marketplace separatamente.

Le immagini devono essere in grado di farvi riferimento da un URI di archiviazione blob. Preparare un'immagine del sistema operativo Windows o Linux nel formato VHD (non VHDX) e quindi caricare l'immagine a un account di archiviazione in Azure o Azure Stack. Se l'immagine è già stata caricata nell'archiviazione blob in Azure o Azure Stack, è possibile ignorare il passaggio 1.

1. [Caricare un'immagine di macchina virtuale Windows Azure per le distribuzioni di gestione risorse](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) oppure, per un'immagine Linux, seguire le istruzioni descritte nel [macchine virtuali Linux di distribuire in Azure Stack](azure-stack-linux.md). Prima di caricare l'immagine, è importante tenere presente quanto segue:

   - Stack di Azure supporta il formato di disco rigido virtuale di disco fisso. Il formato fisso strutture del disco logico in modo lineare all'interno del file, in modo tale offset del disco X venga archiviato in corrispondenza dell'offset blob X. Un piccolo piè di pagina alla fine del blob vengono descritte le proprietà del file VHD. Per verificare se il disco è fisso, utilizzare il [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) comando di PowerShell.  

    > [!IMPORTANT]
    >  Stack di Azure non supporta dischi rigidi virtuali disco dinamico. Ridimensionamento di un disco dinamico è associato a una macchina virtuale lascerà la macchina virtuale in uno stato di errore. Per evitare questo problema, eliminare la macchina virtuale senza eliminare disco della macchina virtuale, un blob di disco rigido virtuale in un account di archiviazione. Converti il disco rigido virtuale da un disco dinamico a un disco fisso e ricreare la macchina virtuale.

   * È più efficiente per caricare un'immagine nell'archiviazione blob Azure Stack rispetto a Azure nell'archiviazione blob perché richiede meno tempo per effettuare il push dell'immagine nel repository di immagini dello Stack di Azure.

   * Quando si carica il [immagine di macchina virtuale di Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), assicurarsi di sostituire il **account di accesso da Azure** spostarsi con il [configurare l'ambiente di PowerShell dell'operatore Azure Stack](azure-stack-powershell-configure-admin.md) passaggio.  

   * Prendere nota di archiviazione blob di URI in cui caricare l'immagine. URI di archiviazione blob ha il formato seguente: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* file con estensione vhd.

   * Per rendere il blob in modo anonimo accessibile, passare al contenitore blob di account di archiviazione in cui è stato caricato immagine disco rigido virtuale della macchina virtuale. Selezionare **Blob**, quindi selezionare **criteri di accesso**. Facoltativamente, è possibile invece generare una firma di accesso condiviso per il contenitore e includerlo come parte dell'URI del blob.

   ![Vai agli oggetti BLOB di account di archiviazione](./media/azure-stack-add-vm-image/image1.png)

   ![Set blob accesso al ruolo public](./media/azure-stack-add-vm-image/image2.png)

2. Accedi allo Stack di Azure come operatore. Nel menu di scelta, selezionare **più servizi**. Quindi, selezionare **calcolo** > **immagini di macchina virtuale** > **Aggiungi**.

3. Sotto **aggiungere un'immagine di macchina virtuale**, immettere il server di pubblicazione, offerta, SKU e versione dell'immagine della macchina virtuale. Questi segmenti nome fare riferimento all'immagine di macchina virtuale nei modelli di gestione risorse. Assicurarsi di selezionare il **osType** valore correttamente. Per **URI Blob del disco del sistema operativo**, immettere l'URI del Blob in cui l'immagine è stata caricata. Quindi, selezionare **crea** per iniziare a creare l'immagine di macchina virtuale.

   ![Iniziare a creare l'immagine](./media/azure-stack-add-vm-image/image4.png)

   Quando l'immagine viene creata correttamente, lo stato di immagine di macchina virtuale diventa **Succeeded**.

4. Per rendere l'immagine di macchina virtuale più facilmente disponibile per l'utilizzo di utente nell'interfaccia utente, è buona norma [creare un elemento del Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Rimuove un'immagine di macchina virtuale tramite il portale

1. Aprire il portale di amministrazione all'indirizzo [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

2. Selezionare **gestione Marketplace**e quindi selezionare la macchina virtuale si desidera eliminare.

3. Fare clic su **Elimina**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Aggiungere un'immagine di macchina virtuale nel marketplace tramite PowerShell

1. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md).  

2. Accedi allo Stack di Azure come operatore. Per istruzioni, vedere [Accedi allo Stack di Azure come operatore](azure-stack-powershell-configure-admin.md).

3. Aprire PowerShell con un prompt dei comandi con privilegi elevati ed eseguire:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
  ````

  Il **Add-AzsPlatformimage** cmdlet specifica i valori usati dai modelli di gestione risorse di Azure per fare riferimento all'immagine di macchina virtuale. I valori includono:
  - **publisher**  
    Ad esempio: `Canonical`  
    Il server di pubblicazione segmento corrispondente al nome dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine. Ad esempio **Microsoft**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **offer**  
    Ad esempio: `UbuntuServer`  
    L'offerta segmento corrispondente al nome dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine di macchina virtuale. Ad esempio **WindowsServer**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **sku**  
    Ad esempio: `14.04.3-LTS`  
    Il segmento di nome SKU dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine di macchina virtuale. Ad esempio **Datacenter2016**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **version**  
    Ad esempio: `1.0.0`  
    Versione dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine di macchina virtuale. Questa versione è nel formato *\#.\#.\#*. Ad esempio **1.0.0**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **osType**  
    Ad esempio: `Linux`  
    OsType dell'immagine deve essere **Windows** oppure **Linux**.  
  - **OSUri**  
    Ad esempio: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    È possibile specificare un URI di archiviazione blob per un `osDisk`.  

    Per altre informazioni, vedere la Guida di riferimento di PowerShell per la [Add-AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet e il [New-DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) cmdlet.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Aggiungere un'immagine di macchina virtuale personalizzata nel marketplace tramite PowerShell

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

3. Accedi allo Stack di Azure come operatore. Per istruzioni, vedere [Accedi allo Stack di Azure come operatore](azure-stack-powershell-configure-admin.md).

4. Creare un account di archiviazione in Azure globale o dello Stack di Azure per archiviare l'immagine di macchina virtuale personalizzata. Per istruzioni, vedi [Guida introduttiva: elencare i BLOB tramite il portale di Azure, caricamento e download](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Preparare un'immagine del sistema operativo Windows o Linux nel formato VHD (non VHDX), caricare l'immagine all'account di archiviazione e ottenere l'URI in cui l'immagine di macchina virtuale può essere recuperato tramite PowerShell.  

  ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

6. (Facoltativo) È possibile caricare un array di dischi di dati come parte dell'immagine di macchina virtuale. Creare il disco dati utilizzando il cmdlet New-DataDiskObject. Aprire PowerShell da un prompt dei comandi con privilegi elevati ed eseguire:

  ````PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
  ````

7. Aprire PowerShell con un prompt dei comandi con privilegi elevati ed eseguire:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
  ````

    Per ulteriori informazioni sui cmdlet Add-AzsPlatformimage e il cmdlet New-DataDiskObject, vedere Microsoft PowerShell [documentazione di Azure Stack operatore modulo](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Rimuove un'immagine di macchina virtuale mediante PowerShell

Quando non è più necessario l'immagine di macchina virtuale che è stato caricato, è possibile eliminare il Marketplace tramite il cmdlet seguente:

1. [Installare PowerShell per Azure Stack](azure-stack-powershell-install.md).

2. Accedi allo Stack di Azure come operatore.

3. Aprire PowerShell con un prompt dei comandi con privilegi elevati ed eseguire:

  ````PowerShell  
  Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
  ````
  Il **Remove-AzsPlatformImage** cmdlet specifica i valori usati dai modelli di gestione risorse di Azure per fare riferimento all'immagine di macchina virtuale. I valori includono:
  - **publisher**  
    Ad esempio: `Canonical`  
    Il server di pubblicazione segmento corrispondente al nome dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine. Ad esempio **Microsoft**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **offer**  
    Ad esempio: `UbuntuServer`  
    L'offerta segmento corrispondente al nome dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine di macchina virtuale. Ad esempio **WindowsServer**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **sku**  
    Ad esempio: `14.04.3-LTS`  
    Il segmento di nome SKU dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine di macchina virtuale. Ad esempio **Datacenter2016**. Non includere uno spazio o altri caratteri speciali in questo campo.  
  - **version**  
    Ad esempio: `1.0.0`  
    Versione dell'immagine di macchina virtuale da parte degli utenti quando si distribuisce l'immagine di macchina virtuale. Questa versione è nel formato *\#.\#.\#*. Ad esempio **1.0.0**. Non includere uno spazio o altri caratteri speciali in questo campo.  
    
    Per ulteriori informazioni sul cmdlet Remove-AzsPlatformImage, vedere Microsoft PowerShell [documentazione di Azure Stack operatore modulo](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>Passaggi successivi

[Effettuare il provisioning di una macchina virtuale](azure-stack-provision-vm.md)
