---
title: Caricare un disco rigido virtuale specializzato in Azure da usare per creare una nuova macchina virtuale | Microsoft Docs
description: Caricare un disco rigido virtuale specializzato in Azure da usare per creare una nuova macchina virtuale.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 02/05/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 204fa369dd6db618ec5340317188681b0a2988e3
ms.openlocfilehash: fc8cb82e952a05b161f00ef9ebfbd4852d3987d4


---

# <a name="upload-a-specialized-vhd-to-azure-to-use-for-creating-a-new-vm"></a>Caricare un disco rigido virtuale specializzato in Azure da usare per creare una nuova macchina virtuale

Un disco rigido virtuale specializzato gestisce gli account utente, le applicazioni e altri dati di stato dalla macchina virtuale originale. È possibile caricare un disco rigido virtuale specializzato in Azure e usarlo per creare una macchina virtuale che usi Managed Disks o un account di archiviazione non gestita. Si consiglia di usare [Managed Disks](../storage/storage-managed-disks-overview.md) per sfruttare la gestione semplificata e le funzionalità aggiuntive offerte.


> [!IMPORTANT]
> Prima di caricare dischi rigidi virtuali in Azure, è necessario seguire la procedura in [Preparare un disco rigido virtuale Windows o VHDX prima del caricamento in Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
>
>


* Per informazioni sui prezzi delle varie dimensioni delle macchine virtuali, vedere [Prezzi di Macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows).
* Per informazioni sui prezzi di archiviazione, vedere [Prezzi di Archiviazione](https://azure.microsoft.com/pricing/details/storage/blobs/). 
* Per la disponibilità delle dimensioni di VM nelle aree di Azure, vedere [Prodotti disponibili in base all'area](https://azure.microsoft.com/regions/services/).
* Per trovare i limiti generali delle VM di Azure, vedere [Sottoscrizione di Azure e limiti, quote e vincoli dei servizi](../azure-subscription-service-limits.md).

## <a name="before-you-begin"></a>Prima di iniziare
Se si usa PowerShell, verificare di disporre della versione più recente del modulo di PowerShell AzureRM.Compute. Eseguire il comando seguente per installarlo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Per altre informazioni, vedere [Azure PowerShell Versioning](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning) (Controllo delle versioni di Azure PowerShell).


## <a name="prepare-the-vm"></a>Preparare la macchina virtuale
 
Se si intende usare il disco rigido virtuale specializzato così come è per creare una nuova macchina virtuale, assicurare il completamento delle operazioni seguenti. 
  * Se si intende usare Managed Disks, controllare [Pianificare la migrazione a Managed Disks](virtual-machines-windows-on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).
  * [Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Non** generalizzare la macchina Virtuale con Sysprep.
  * Rimuovere tutti gli strumenti di virtualizzazione guest e gli agenti installati nella macchina virtuale, ad esempio gli strumenti VMware.
  * Assicurarsi che la macchina virtuale sia configurata per eseguire il pull dell'indirizzo IP e delle impostazioni DNS tramite DHCP. In questo modo il server ottiene un indirizzo IP all'interno della rete virtuale all'avvio. 
  * Arrestare la macchina virtuale prima di procedere.

## <a name="log-in-to-azure"></a>Accedere ad Azure
Se PowerShell versione 1.4 o successiva non è già stato installato, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

1. Aprire Azure PowerShell e accedere al proprio account di Azure. Verrà visualizzata una finestra popup in cui immettere le credenziali dell'account Azure.
   
    ```powershell
    Login-AzureRmAccount
    ```
2. Ottenere l'ID di sottoscrizione per le sottoscrizioni disponibili.
   
    ```powershell
    Get-AzureRmSubscription
    ```
3. Impostare la sottoscrizione corretta utilizzandone l'ID. Sostituire `<subscriptionID>` con l'ID della sottoscrizione corretta.
   
    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="get-the-storage-account"></a>Ottenere l'account di archiviazione
Per archiviare l'immagine della VM caricata, è necessario un account di archiviazione di Azure. È possibile usare un account di archiviazione esistente o crearne uno nuovo. 

Se il disco rigido virtuale verrà usato per creare un disco gestito per una macchina virtuale, la posizione dell'account di archiviazione deve corrispondere al percorso in cui verrà creata la macchina virtuale.

Per mostrare gli account di archiviazione disponibili, digitare:

```powershell
Get-AzureRmStorageAccount
```

Se si vuole usare un account di archiviazione esistente, passare alla sezione [Caricare l'immagine della VM](#upload-the-vm-vhd-to-your-storage-account) .

Per creare un account di archiviazione, seguire questa procedura:

1. È necessario il nome del gruppo di risorse in cui deve essere creato l'account di archiviazione. Per trovare tutti i gruppi di risorse inclusi nella sottoscrizione digitare:
   
    ```powershell
    Get-AzureRmResourceGroup
    ```

    Per creare un gruppo di risorse denominato **MyResourceGroup** nell'area **Stati Uniti occidentali**, digitare:

    ```powershell
    New-AzureRmResourceGroup -Name myResourceGroup -Location "West US"
    ```

2. Creare un account di archiviazione denominato **mystorageaccount** in questo gruppo di risorse con il cmdlet [New-AzureRmStorageAccount](https://msdn.microsoft.com/library/mt607148.aspx).
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
    I valori validi -SkuName validi sono:
   
   * **Standard_LRS**: archiviazione con ridondanza locale. 
   * **Standard_ZRS**: archiviazione con ridondanza della zona.
   * **Standard_GRS**: archiviazione con ridondanza geografica. 
   * **Standard_RAGRS**: archiviazione con ridondanza geografica e accesso in lettura. 
   * **Premium_LRS**: archiviazione con ridondanza locale Premium. 

## <a name="upload-the-vhd-to-your-storage-account"></a>Caricare il disco rigido virtuale nell'account di archiviazione

Usare il cmdlet [Add-AzureRmVhd](https://msdn.microsoft.com/library/mt603554.aspx) per caricare il disco rigido virtuale in un contenitore nell'account di archiviazione. In questo esempio, il file **myVHD.vhd** viene caricato da `"C:\Users\Public\Documents\Virtual hard disks\"` a un account di archiviazione denominato **mystorageaccount** nel gruppo di risorse **myResourceGroup**. Il file viene inserito nel contenitore denominato **mycontainer** e il nuovo nome del file sarà **myUploadedVHD.vhd**.

```powershell
$rgName = "myResourceGroup"
$urlOfUploadedImageVhd = "https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd"
Add-AzureRmVhd -ResourceGroupName $rgName -Destination $urlOfUploadedImageVhd `
    -LocalFilePath "C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd"
```


Se l'operazione riesce, si ottiene una risposta simile alla seguente:

```powershell
MD5 hash is being calculated for the file C:\Users\Public\Documents\Virtual hard disks\myVHD.vhd.
MD5 hash calculation is completed.
Elapsed time for the operation: 00:03:35
Creating new page blob of size 53687091712...
Elapsed time for upload: 01:12:49

LocalFilePath           DestinationUri
-------------           --------------
C:\Users\Public\Doc...  https://mystorageaccount.blob.core.windows.net/mycontainer/myUploadedVHD.vhd
```

L'esecuzione del comando potrebbe richiedere del tempo, a seconda della connessione di rete e delle dimensioni del file VHD.

Salvare il percorso dell'**URI di destinazione** da usare in seguito se si desidera creare un disco gestito o una nuova macchina virtuale con il disco rigido virtuale caricato.

### <a name="other-options-for-uploading-a-vhd"></a>Altre opzioni per il caricamento di un disco rigido virtuale

È anche possibile caricare un disco rigido virtuale nell'account di archiviazione tramite uno dei seguenti modi:

-   [API Copy Blob di Archiviazione di Azure](https://msdn.microsoft.com/library/azure/dd894037.aspx)

-   [Caricamento di BLOB in Azure Storage Explorer](https://azurestorageexplorer.codeplex.com/)

-   [Materiale di riferimento dell'API REST del servizio di importazione/esportazione dell'archiviazione](https://msdn.microsoft.com/library/dn529096.aspx)

    È consigliabile usare il servizio di importazione/esportazione se il tempo di caricamento stimato è maggiore di 7 giorni. È possibile usare [DataTransferSpeedCalculator](https://github.com/Azure-Samples/storage-dotnet-import-export-job-management/blob/master/DataTransferSpeedCalculator.html) per stimare il tempo in base alla dimensione dei dati e all'unità di trasferimento. 

    Il servizio Importazione/esportazione può essere usato per eseguire la copia in un account di archiviazione standard. Sarà necessario copiare dall'archiviazione standard all’account di archiviazione premium mediante uno strumento come AzCopy.

    
## <a name="create-the-subnet-and-vnet"></a>Creare la subNet e la vNet

Creare la rete virtuale e la subnet della [rete virtuale](../virtual-network/virtual-networks-overview.md).

1. Creare la subnet. In questo esempio viene creata una subnet denominata **mySubNet** nel gruppo di risorse **myResourceGroup** e il prefisso dell'indirizzo della subnet viene impostato su **10.0.0.0/24**.
   
    ```powershell
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Creare la rete virtuale. In questo esempio il nome della rete virtuale è **myVnetName**, la posizione specificata è **Stati Uniti occidentali** e il prefisso dell'indirizzo per la rete virtuale è **10.0.0.0/16**. 
   
    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-nic"></a>Creare un indirizzo IP pubblico e NIC
Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, sono necessari un [indirizzo IP pubblico](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

1. Creare l'IP pubblico. In questo esempio, il nome dell'indirizzo IP pubblico è **myIP**.
   
    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Creare la scheda NIC. In questo esempio, il nome specificato della scheda NIC è **myNicName**.
   
    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
    -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creare il gruppo di sicurezza di rete e una regola RDP
Per essere in grado di accedere alla VM tramite RDP, è necessario disporre di una regola di sicurezza che consenta l'accesso RDP sulla porta 3389. Poiché il disco rigido virtuale per la nuova macchina virtuale è stato creato da una VM specializzata esistente, dopo l'avvenuta creazione della macchina virtuale è possibile usare un account esistente dalla VM di origine che aveva l'autorizzazione di accedere tramite RDP.

In questo esempio il nome NSG impostato è **myNsg**, mentre il nome della regola RDP è **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
    
```

Per altre informazioni sugli endpoint e sulle regole NSG, vedere [Apertura di porte a una VM tramite PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="set-the-vm-name-and-size"></a>Impostare il nome e le dimensioni della macchina virtuale

In questo esempio il nome della macchina virtuale viene impostato su "myVM" e le dimensioni su "Standard_A2".

```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

## <a name="add-the-nic"></a>Aggiungere la scheda di interfaccia di rete
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
## <a name="configure-the-os-disk"></a>Configurare il disco del sistema operativo

Il sistema operativo specializzato potrebbe essere un disco rigido virtuale [caricato in Azure](virtual-machines-windows-upload-image.md) o una [copia del disco rigido virtuale da una macchina virtuale di Azure esistente](virtual-machines-windows-vhd-copy.md). 

È possibile scegliere una delle due opzioni:
- **Opzione 1**: creare un disco gestito specializzato da un disco rigido virtuale specializzato in un account di archiviazione esistente da usare come disco del sistema operativo.

oppure 

- **Opzione 2**: usare un disco rigido virtuale specializzato archiviato nel proprio account di archiviazione (un disco non gestito). 

### <a name="option-1-create-a-managed-disk-from-an-unmanaged-specialized-disk"></a>Opzione 1: Creare un disco gestito da un disco specializzato non gestito

1. Creare un disco gestito dal disco rigido virtuale specializzato esistente nell'account di archiviazione. Questo esempio usa **myOSDisk1** come nome del disco, inserisce il disco nell'archiviazione **StandardLRS** usa **https://storageaccount.blob.core.windows.net/vhdcontainer/osdisk.vh.vhd** come URI per il disco rigido virtuale di origine.

    ```powershell
    $osDisk = New-AzureRmDisk -DiskName "myOSDisk1" -Disk (New-AzureRmDiskConfig `
    -AccountType StandardLRS  -Location $location -CreationDataCreateOption Import `
    -SourceUri $urlOfUploadedImageVhd -ResourceGroupName $rgName
    ```

2. Aggiungere il disco del sistema operativo alla configurazione. In questo esempio le dimensioni del disco vengono impostate su **128 GB** e viene collegato il disco gestito come disco del sistema operativo **Windows**.
    
    ```powershell
    $vm = Set-AzureRmVMOSDisk -VM $vm -ManagedDiskId $osDisk.Id -ManagedDiskStorageAccountType StandardLRS `
    -DiskSizeInGB 128 -CreateOption Attach -Windows
    ```

Facoltativo: collegare dischi gestiti aggiuntivi come dischi dati. Questa opzione presuppone che sia stato creato il disco dati gestito tramite [Create managed data disks](virtual-machines-windows-create-managed-disk-ps.md) (Creare dischi dati gestiti). 

```powershell
$vm = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 1
```


### <a name="option-2-attach-a-vhd-that-is-in-an-existing-storage-account"></a>Opzione 2: collegare un disco rigido virtuale che si trova in un account di archiviazione esistente

1. Impostare l'URI per il disco rigido virtuale che si desidera usare. In questo esempio, il file del disco rigido virtuale denominato **myOsDisk.vhd** viene mantenuto in un account di archiviazione denominato **myStorageAccount** all'interno di un contenitore denominato **myContainer**.

    ```powershell
    $osDiskUri = $urlOfUploadedImageVhd
    ```
2. Aggiungere il disco del sistema operativo usando l'URL del disco rigido virtuale del sistema operativo copiato. In questo esempio, quando viene creato il disco del sistema operativo, il termine "osDisk" viene collegato al nome della macchina virtuale per creare il nome del disco del sistema operativo. Questo esempio specifica anche che il disco rigido virtuale basato su Windows deve essere collegato alla macchina virtuale come disco del sistema operativo.
    
    ```powershell
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
    ```

Facoltativo: se si dispone di dischi dati da associare alla macchina virtuale, aggiungere i dischi dati tramite gli URL dei dischi rigidi virtuali di dati e il numero di unità logica (LUN) appropriato.

```powershell
$dataDiskName = $vmName + "dataDisk"
$vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 1 -CreateOption attach
```

Quando si usa un account di archiviazione, gli URL dei dischi dati e del sistema operativo sono simili al seguente: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Per trovarlo nel portale, passare al contenitore di archiviazione di destinazione, fare clic sul disco rigido virtuale del sistema operativo o dei dati copiato e quindi copiare il contenuto dell'URL.


## <a name="create-the-vm"></a>Creare la VM

Creare la macchina virtuale usando le configurazioni appena create.

```powershell
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se il comando ha esito positivo, viene visualizzato un output simile al seguente:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

## <a name="verify-that-the-vm-was-created"></a>Verificare che la VM sia stata creata
La VM appena creata verrà visualizzata nel [portale di Azure](https://portal.azure.com) in **Sfoglia** > **Macchine virtuali** oppure usando i comandi di PowerShell seguenti:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Passaggi successivi
Per accedere alla nuova macchina virtuale, passare alla VM nel [portale](https://portal.azure.com), fare clic su **Connetti**e aprire il file RDP di Desktop remoto. Usare le credenziali dell'account della macchina virtuale originale per accedere alla nuova macchina virtuale. Per altre informazioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



<!--HONumber=Feb17_HO2-->


