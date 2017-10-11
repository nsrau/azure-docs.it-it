---
title: Creare una macchina virtuale da un disco specializzato in Azure | Documentazione Microsoft
description: Creare una nuova macchina virtuale collegando un disco non gestito specializzato nel modello di distribuzione Resource Manager.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 3b7d3cd5-e3d7-4041-a2a7-0290447458ea
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 05/23/2017
ms.author: cynthn
ms.openlocfilehash: 974d89aa96cba94fedfd1acbaf4f1d30ac8e6257
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/29/2017
---
# <a name="create-a-vm-from-a-specialized-vhd-in-a-storage-account"></a>Creare una VM da un disco rigido virtuale specializzato in un account di archiviazione

Creare una nuova macchina virtuale collegando un disco non gestito specializzato come disco del sistema operativo con Powershell. Un disco specializzato è una copia del disco rigido virtuale proveniente da una macchina virtuale esistente che gestisce gli account utente, le applicazioni e altri dati di stato dalla macchina virtuale originale. 

Sono disponibili due opzioni:
* [Caricare un VHD](create-vm-specialized.md#option-1-upload-a-specialized-vhd)
* [Copiare il disco rigido virtuale di una VM di Azure esistente](create-vm-specialized.md#option-2-copy-an-existing-azure-vm)

## <a name="before-you-begin"></a>Prima di iniziare
Se si usa PowerShell, verificare di avere la versione più recente del modulo di PowerShell AzureRM.Compute. Eseguire il comando seguente per installarlo.

```powershell
Install-Module AzureRM.Compute 
```
Per altre informazioni, vedere [Azure PowerShell Versioning](/powershell/azure/overview) (Controllo delle versioni di Azure PowerShell).


## <a name="option-1-upload-a-specialized-vhd"></a>Opzione 1: Caricare un disco rigido virtuale specializzato

È possibile caricare il disco rigido virtuale da una VM specializzata creata con uno strumento di virtualizzazione locale, ad esempio Hyper-V, o da una VM esportata da un altro cloud.

### <a name="prepare-the-vm"></a>Preparare la macchina virtuale
È possibile caricare un disco rigido virtuale specializzato creato usando una VM locale o un disco rigido virtuale esportato da un altro cloud. Un disco rigido virtuale specializzato gestisce gli account utente, le applicazioni e altri dati di stato dalla macchina virtuale originale. Se si intende usare il disco rigido virtuale così come è per creare una nuova macchina virtuale, assicurare il completamento delle operazioni seguenti. 
  
  * [Preparare un disco rigido virtuale (VHD) di Windows per il caricamento in Azure](prepare-for-upload-vhd-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). **Non** generalizzare la macchina Virtuale con Sysprep.
  * Rimuovere tutti gli strumenti di virtualizzazione guest e gli agenti installati nella macchina virtuale, ad esempio gli strumenti VMware.
  * Assicurarsi che la macchina virtuale sia configurata per eseguire il pull dell'indirizzo IP e delle impostazioni DNS tramite DHCP. In questo modo il server ottiene un indirizzo IP all'interno della rete virtuale all'avvio. 


### <a name="get-the-storage-account"></a>Ottenere l'account di archiviazione
Per archiviare l'immagine della VM caricata, è necessario un account di archiviazione di Azure. È possibile usare un account di archiviazione esistente o crearne uno nuovo. 

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

2. Creare un account di archiviazione denominato **mystorageaccount** in questo gruppo di risorse con il cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount).
   
    ```powershell
    New-AzureRmStorageAccount -ResourceGroupName myResourceGroup -Name mystorageaccount -Location "West US" `
        -SkuName "Standard_LRS" -Kind "Storage"
    ```
   
### <a name="upload-the-vhd-to-your-storage-account"></a>Caricare il disco rigido virtuale nell'account di archiviazione
Usare il cmdlet [Add-AzureRmVhd](/powershell/module/azurerm.compute/add-azurermvhd) per caricare l'immagine in un contenitore nell'account di archiviazione. In questo esempio, il file **myVHD.vhd** viene caricato da `"C:\Users\Public\Documents\Virtual hard disks\"` a un account di archiviazione denominato **mystorageaccount** nel gruppo di risorse **myResourceGroup**. Il file viene inserito nel contenitore denominato **mycontainer** e il nuovo nome del file sarà **myUploadedVHD.vhd**.

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


## <a name="option-2-copy-the-vhd-from-an-existing-azure-vm"></a>Opzione 2: Copiare il disco rigido virtuale da una VM di Azure esistente

È possibile copiare un disco rigido virtuale in un altro account di archiviazione da usare quando si crea una nuova VM duplicata.

### <a name="before-you-begin"></a>Prima di iniziare
Verificare quanto segue:

* Disporre delle informazioni sugli **account di archiviazione di origine e destinazione**. Per la VM di origine è necessario disporre dei nomi degli account di archiviazione e dei contenitori. In genere il nome del contenitore sarà **vhds**. È inoltre necessario disporre di un account di archiviazione di destinazione. Se non si dispone già di un account di archiviazione, è possibile crearne uno usando il portale (**Servizi** > Account di archiviazione > Aggiungi) oppure mediante il cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). 
* Avere scaricato e installato lo [strumento AzCopy](../../storage/common/storage-use-azcopy.md). 

### <a name="deallocate-the-vm"></a>Deallocare la VM
Deallocare la VM, operazione che consente di liberare il disco rigido virtuale da copiare. 

* **Portale**: fare clic su  **Macchine virtuali** > **myVM** > Stop (Termina)
* **PowerShell**: usare [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm) per arrestare (deallocare) la VM denominata **myVM** nel gruppo di risorse **myResourceGroup**.

```powershell
Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM
```

Nel portale di Azure lo **stato** della VM passa da **Arrestato** ad **Arrestato (deallocato)**.

### <a name="get-the-storage-account-urls"></a>Ottenere gli URL dell'account di archiviazione
Sono necessari gli URL degli account di archiviazione di origine e destinazione. Gli URL hanno l'aspetto seguente: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Se si conosce già il nome degli account di archiviazione e dei contenitori, per creare l'URL è sufficiente sostituire le informazioni tra parentesi. 

Per ottenere l'URL è possibile usare il portale di Azure o Azure PowerShell:

* **Portale**: fare clic su **>** per **Altri servizi** > **Account di archiviazione** > *account di archiviazione* > **BLOB**. Il file VHD di origine si trova probabilmente nel contenitore **vhds**. Fare clic su **Proprietà** per il contenitore e copiare il testo con l'etichetta **URL**. Sono necessari gli URL di entrambi i contenitori di origine e di destinazione. 
* **PowerShell**: usare [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) per ottenere le informazioni dalla VM denominata **myVM** nel gruppo di risorse **myResourceGroup**. Nei risultati esaminare la sezione **Storage profile** (Profilo archiviazione) per l'**URI VHD**. La prima parte dell'URI è l'URL del contenitore, mentre l'ultima parte è il nome del disco rigido virtuale del sistema operativo della VM.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"
``` 

## <a name="get-the-storage-access-keys"></a>Ottenere le chiavi di accesso alle risorse di archiviazione
Trovare le chiavi di accesso per gli account di archiviazione di origine e destinazione. Per altre informazioni sulle chiavi di accesso, vedere [Informazioni sugli account di archiviazione di Azure](../../storage/common/storage-create-storage-account.md).

* **Portale**: fare clic su **Altri servizi** > **Account di archiviazione** > *account di archiviazione* > **Chiavi di accesso**. Copiare la chiave denominata **key1**.
* **PowerShell**: usare [Get-AzureRmStorageAccountKey](/powershell/module/azurerm.storage/get-azurermstorageaccountkey) per ottenere la chiave di archiviazione per l'account di archiviazione **mystorageaccount** nel gruppo di risorse **myResourceGroup**. Copiare la chiave denominata **key1**.

```powershell
Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup
```

### <a name="copy-the-vhd"></a>Copiare il file VHD
È possibile copiare file da un account di archiviazione a un altro usando AzCopy. Se il container di destinazione specificato non esiste, tale contenitore verrà creato automaticamente. 

Per usare AzCopy, aprire un prompt dei comandi sul computer locale e passare alla cartella in cui è installato tale strumento. Il percorso sarà simile a *C:\Programmi (x86)\Microsoft SDKs\Azure\AzCopy*. 

Per copiare tutti i file all'interno di un contenitore, è possibile usare l'opzione **/S**. Può essere usata per copiare il disco rigido virtuale del sistema operativo e tutti i dischi dati che si trovano nello stesso contenitore. Questo esempio mostra come copiare tutti i file che si trovano nel contenitore **mysourcecontainer** dell'account di archiviazione **mysourcestorageaccount** nel contenitore **mydestinationcontainer** dell'account di archiviazione **mydestinationstorageaccount**. Sostituire i nomi degli account di archiviazione e dei contenitori con nomi a propria scelta. Sostituire `<sourceStorageAccountKey1>` e `<destinationStorageAccountKey1>` con chiavi a propria scelta.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Se si vuole copiare solo uno specifico file VHD in un contenitore con più file, è anche possibile specificare il nome del file usando l'opzione /Pattern. In questo esempio verrà copiato solo il file denominato **myFileName.vhd**.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Al termine verrà visualizzato un messaggio simile al seguente:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

### <a name="troubleshooting"></a>Risoluzione dei problemi
* Quando si usa AZCopy, se viene visualizzato l'errore "Il server non è stato in grado di autenticare la richiesta", assicurarsi che il valore dell'intestazione di autorizzazione sia formato correttamente, inclusa la firma. Se si sta usando la chiave 2 o la chiave di archiviazione secondaria, provare a usare la chiave 1 o la chiave di archiviazione primaria.

## <a name="create-the-new-vm"></a>Creare la nuova VM 

È necessario creare le risorse di rete e le altre risorse da usare nella nuova VM.

### <a name="create-the-subnet-and-vnet"></a>Creare la subNet e la vNet

Creare la rete virtuale e la subnet della [rete virtuale](../../virtual-network/virtual-networks-overview.md).

1. Creare la subnet. In questo esempio viene creata una subnet denominata **mySubNet** nel gruppo di risorse **myResourceGroup** e il prefisso dell'indirizzo della subnet viene impostato su **10.0.0.0/24**.
   
    ```powershell
    $rgName = "myResourceGroup"
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

### <a name="create-a-public-ip-address-and-nic"></a>Creare un indirizzo IP pubblico e NIC
Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, sono necessari un [indirizzo IP pubblico](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

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

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creare il gruppo di sicurezza di rete e una regola RDP
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

Per altre informazioni sugli endpoint e sulle regole NSG, vedere [Apertura di porte a una VM tramite PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

### <a name="set-the-vm-name-and-size"></a>Impostare il nome e le dimensioni della macchina virtuale

In questo esempio il nome della macchina virtuale viene impostato su "myVM" e le dimensioni su "Standard_A2".
```powershell
$vmName = "myVM"
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"
```

### <a name="add-the-nic"></a>Aggiungere la scheda di interfaccia di rete
    
```powershell
$vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id
```
    
    
### <a name="configure-the-os-disk"></a>Configurare il disco del sistema operativo

1. Impostare l'URI per il disco rigido virtuale caricato o copiato. In questo esempio, il file del disco rigido virtuale denominato **myOsDisk.vhd** viene mantenuto in un account di archiviazione denominato **myStorageAccount** all'interno di un contenitore denominato **myContainer**.

    ```powershell
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    ```
2. Aggiungere il disco del sistema operativo. In questo esempio, quando viene creato il disco del sistema operativo, il termine "osDisk" viene collegato al nome della macchina virtuale per creare il nome del disco del sistema operativo. Questo esempio specifica anche che il disco rigido virtuale basato su Windows deve essere collegato alla macchina virtuale come disco del sistema operativo.
    
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


### <a name="complete-the-vm"></a>Completare la VM 

Creare la macchina virtuale usando le configurazioni appena create.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se il comando ha esito positivo, viene visualizzato un output simile al seguente:

```powershell
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   

```

### <a name="verify-that-the-vm-was-created"></a>Verificare che la VM sia stata creata
La VM appena creata verrà visualizzata nel [portale di Azure](https://portal.azure.com) in **Sfoglia** > **Macchine virtuali** oppure usando i comandi di PowerShell seguenti:

```powershell
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Passaggi successivi
Per accedere alla nuova macchina virtuale, passare alla VM nel [portale](https://portal.azure.com), fare clic su **Connetti**e aprire il file RDP di Desktop remoto. Usare le credenziali dell'account della macchina virtuale originale per accedere alla nuova macchina virtuale. Per altre informazioni, vedere [Come connettersi e accedere a una macchina virtuale di Azure che esegue Windows](connect-logon.md).

