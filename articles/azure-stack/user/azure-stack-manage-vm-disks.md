---
title: Gestire i dischi delle macchine Virtuali in Azure Stack | Microsoft Docs
description: Creare dischi per macchine virtuali in Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/18/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 5719d5c49d3061acd167f51f74aac109dc22ec49
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/08/2019
ms.locfileid: "55961398"
---
# <a name="create-virtual-machine-disk-storage-in-azure-stack"></a>Creare i dischi di macchina virtuale in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Questo articolo descrive come creare macchine virtuali di archiviazione su disco tramite il portale di Azure Stack o tramite PowerShell.

## <a name="overview"></a>Informazioni generali

A partire dalla versione 1808, Azure Stack supporta l'uso di managed disks e i dischi non gestiti nelle macchine virtuali, come un sistema operativo (SO) e un disco dati. Prima della versione 1808, sono supportati solo dischi non gestiti. 

**[I dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#managed-disks)**  semplificare la gestione dei dischi per VM IaaS di Azure per gestire gli account di archiviazione associati ai dischi della macchina virtuale. È sufficiente specificare le dimensioni del disco necessario, e Azure Stack crea e gestisce automaticamente il disco.

**[Dischi non gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks)**, è necessario creare un [account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) per archiviare i dischi. I dischi creati vengono definiti dischi di macchine Virtuali e vengono archiviati in contenitori nell'account di archiviazione.

### <a name="best-practice-guidelines"></a>Procedure ottimali

Per migliorare le prestazioni e ridurre i costi complessivi, si consiglia di che posizionare ogni disco della macchina virtuale in un contenitore separato. Un contenitore deve contenere un disco del sistema operativo o un disco dati, ma non entrambi allo stesso tempo. (Tuttavia, vi è nulla a che non consentono l'inserimento di entrambi i tipi di disco nello stesso contenitore).

Se si aggiungono uno o più dischi dati a una macchina virtuale, usare i contenitori aggiuntivi come un percorso per archiviare questi dischi. Disco del sistema operativo per le macchine virtuali aggiuntive deve anche essere nei propri contenitori.

Quando si creano le macchine virtuali, è possibile riutilizzare lo stesso account di archiviazione per ogni nuova macchina virtuale. Solo i contenitori creati devono essere univoci.

### <a name="adding-new-disks"></a>Aggiunta di nuovi dischi

La tabella seguente riepiloga come aggiungere i dischi tramite il portale e PowerShell.

| Metodo | Opzioni
|-|-|
|Portale utenti|-Aggiungere nuovi dischi dati a una VM esistente. Vengono creati nuovi dischi tramite Azure Stack. </br> </br>-Aggiungere un file di disco (VHD) esistente a una macchina virtuale creata in precedenza. A tale scopo, è necessario preparare il file VHD e quindi caricare il file di Azure Stack. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | -Creare una nuova macchina virtuale con un disco del sistema operativo e allo stesso tempo aggiungere uno o più dischi dati a tale macchina virtuale. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Usare il portale per aggiungere dischi a una macchina virtuale

Per impostazione predefinita, quando si usa il portale per creare una macchina virtuale per la maggior parte degli elementi del marketplace, viene creato solo il disco del sistema operativo.

Dopo aver creato una macchina virtuale, è possibile usare il portale per:
* Creare un nuovo disco dati e collegarlo alla macchina virtuale.
* Caricare un disco dati esistente e collegarlo alla macchina virtuale.

Ogni disco non gestito che è aggiungere deve essere inserito in un contenitore separato.

>[!NOTE]  
>I dischi creati e gestiti da Azure vengono chiamati [dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Usare il portale per creare e collegare un nuovo disco dati

1.  Nel portale, scegliere **tutti i servizi** > **macchine virtuali**.    
    ![Esempio: Dashboard della macchina virtuale](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Selezionare una macchina virtuale che è stata creata in precedenza.   
    ![Esempio: Selezionare una macchina virtuale nel dashboard](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  Per la macchina virtuale, selezionare **Disks** > **Aggiungi disco dati**.       
    ![Esempio: Collegare un nuovo disco alla macchina virtuale](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  Per il disco dati:
    -  Immettere il **LUN**. Il LUN deve essere un numero valido.
    -  Selezionare **creare un disco**.
    ![Esempio: Collegare un nuovo disco alla macchina virtuale](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5.  Nella creazione gestito pannello disco:
    -  Immettere il **nome** del disco.
    -  Selezionarne uno già esistente o crearne una nuova **gruppo di risorse**.
    -  Selezionare il **posizione**. Per impostazione predefinita, la posizione viene impostata allo stesso contenitore che contiene il disco del sistema operativo.
    -  Selezionare il **tipo di Account**. 
        ![Esempio: Collegare un nuovo disco alla macchina virtuale](media/azure-stack-manage-vm-disks/create-manage-disk.png)

        **Unità SSD Premium**  
        I dischi Premium (SSD) sono supportati da unità SSD e offrono prestazioni coerenti e bassa latenza. Forniscono il miglior rapporto tra prezzo e prestazioni e sono ideali per carichi di lavoro di produzione e le applicazioni dei / O intensivo.
       
        **Unità disco rigido standard**  
        I dischi standard (HDD) sono supportati da unità magnetiche e sono preferibili per applicazioni in cui i dati si accede raramente. Dischi con ridondanza della zona sono supportati da archiviazione con ridondanza (della zona ZRS) che i dati vengono replicati in più zone e sono disponibili anche se una singola zona è inattivo. 

    -  Selezionare il **tipo di origine**.

       Creare un disco da uno snapshot di un altro disco, un blob in un account di archiviazione o creare un disco vuoto.

        **Snapshot**  
        Selezionare uno snapshot, se disponibile. Lo snapshot deve essere disponibile nella sottoscrizione e posizione della macchina virtuale.

        **Blob di archiviazione**  
        - Aggiungere l'URI del blob di archiviazione che contiene l'immagine del disco.  
        - Selezionare **esplorare** per aprire il pannello account di archiviazione. Per istruzioni, vedi [aggiungere un disco dati da un account di archiviazione](#add-a-data-disk-from-a-storage-account).
        - Selezionare il tipo del sistema operativo dell'immagine, ovvero **Windows**, **Linux**, o **Nessuno (disco dati)**.

        **Nessuno (disco vuoto)**

    -  Selezionare il **dimensioni (GiB)**.

       Disco standard i costi aumentano in base alle dimensioni del disco. Premium disco i costi e le prestazioni aumentano in base alla dimensione del disco. Per altre informazioni, vedere [prezzi di Managed Disks](https://go.microsoft.com/fwlink/?linkid=843142).

    -  Selezionare **Create**. Stack di Azure crea e convalida il disco gestito.

5.  Dopo che Azure Stack viene creato il disco e la collega alla macchina virtuale, il nuovo disco viene elencato nella sezione Impostazioni disco della macchina virtuale in **dischi dati**.   

    ![Esempio: Visualizzare i dischi](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>Aggiungere un disco dati da un account di archiviazione

Per altre informazioni sull'uso di account di archiviazione in Azure Stack, vedere [Introduzione ad archiviazione di Azure Stack](azure-stack-storage-overview.md).

1. Selezionare il **account di archiviazione** da usare.
2. Selezionare il **contenitore** in cui si desidera inserire il disco dati. Dal **contenitori** pannello, è possibile creare un nuovo contenitore se si desidera. È quindi possibile modificare il percorso per il nuovo disco per il proprio contenitore. Quando si usa un contenitore separato per ogni disco, si distribuisce il posizionamento del disco dati che consentono di migliorare le prestazioni.
3. Scegli **seleziona** per salvare la selezione.

    ![Esempio: Consente di selezionare un contenitore](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Collegare un disco dati esistente a una macchina virtuale

1.  [Preparare un file con estensione vhd](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) per l'uso come disco dati per una macchina virtuale. Caricare il file con estensione vhd in un account di archiviazione che usa con la macchina virtuale che si desidera allegare il file con estensione vhd.

    Prevede di usare un contenitore diverso per conservare il file con estensione vhd del contenitore che contiene il disco del sistema operativo.   
    ![Esempio: Caricare un file VHD](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  Dopo aver caricato il file con estensione vhd, si è pronti per collegare il disco rigido virtuale a una macchina virtuale. Nel menu a sinistra, selezionare **macchine virtuali**.  
 ![Esempio: Selezionare una macchina virtuale nel dashboard](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Scegliere la macchina virtuale dall'elenco.

    ![Esempio: Selezionare una macchina virtuale nel dashboard](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  Nella pagina per la macchina virtuale, selezionare **Disks** > **collega esistente**.   

    ![Esempio: Collegare un disco esistente](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  Nel **collegare un disco esistente** pagina, selezionare **File di disco rigido virtuale**. Il **gli account di archiviazione** verrà visualizzata la pagina.    

    ![Esempio: Selezionare un file di disco rigido virtuale](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  Sotto **gli account di archiviazione**, selezionare l'account da usare e quindi scegliere un contenitore che include il file con estensione vhd caricato in precedenza. Selezionare il file con estensione vhd e quindi scegliere **seleziona** per salvare la selezione.    

    ![Esempio: Consente di selezionare un contenitore](media/azure-stack-manage-vm-disks/select-container2.png)

7.  Sotto **collegare un disco esistente**, il file selezionato è elencato in **File di disco rigido virtuale**. Aggiornamento di **ospitare la memorizzazione nella cache** configurazione del disco e quindi selezionare **OK** per salvare la nuova configurazione di disco della macchina virtuale.    

    ![Esempio: Allegare il file di disco rigido virtuale](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Dopo che Azure Stack viene creato il disco e la collega alla macchina virtuale, il nuovo disco viene elencato nella sezione Impostazioni disco della macchina virtuale in **dischi dati**.   

    ![Esempio: Collegare il disco completo](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Usare PowerShell per aggiungere più dischi non gestiti a una macchina virtuale

È possibile usare PowerShell per effettuare il provisioning di una macchina virtuale e aggiungere un nuovo disco dati o collegare un preesistenti **VHD** file come un disco dati.

Il **Add-AzureRmVMDataDisk** cmdlet aggiunge un disco dati a una macchina virtuale. Quando si crea una macchina virtuale, oppure è possibile aggiungere un disco dati a una macchina virtuale esistente, è possibile aggiungere un disco dati. Specificare il **VhdUri** parametro per distribuire i dischi a contenitori diversi.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Aggiungere dischi dati a una nuova macchina virtuale
Gli esempi seguenti usano i comandi di PowerShell per creare una macchina virtuale con tre dischi dati, ciascuno inserito in un contenitore diverso.

Il primo comando crea un oggetto macchina virtuale e quindi lo archivia nella *$VirtualMachine* variabile. Il comando Assegna un nome e le dimensioni della macchina virtuale.

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                    -VMSize "Standard_A2"
```

I tre comandi successivi assegnano percorsi di tre dischi dati per il *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, e *$DataDiskVhdUri03* variabili. Definire un nome di percorso diverso nell'URL per distribuire i dischi a contenitori diversi.

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

I tre comandi finali aggiungono dischi dati alla macchina virtuale archiviata in *$VirtualMachine*. Ogni comando specifica il nome, posizione e le proprietà aggiuntive del disco. L'URI di ciascun disco viene archiviato in *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, e *$DataDiskVhdUri03*.

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                -VhdUri $DataDiskVhdUri01 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                -VhdUri $DataDiskVhdUri02 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                -VhdUri $DataDiskVhdUri03 -CreateOption Empty
```

Usare i comandi PowerShell seguenti per aggiungere la configurazione di rete e dei dischi del sistema operativo alla macchina virtuale e quindi avviare la nuova macchina virtuale.

```powershell
#set variables
$rgName = "myResourceGroup"
$location = "local"
#Set OS Disk
$osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
$osDiskName = "osDisk"

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
    -CreateOption FromImage -Windows

# Create a subnet configuration
$subnetName = "mySubNet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

# Create a vnet configuration
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

# Create a public IP
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
    -AllocationMethod Dynamic

# Create a network security group configuration
$nsgName = "myNsg"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule

# Create a NIC configuration
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
-Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

#Create the new VM
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
```

### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Aggiungere dischi dati a una macchina virtuale esistente

Gli esempi seguenti usano i comandi di PowerShell per aggiungere tre dischi dati a una VM esistente.
Il primo comando recupera la macchina virtuale denominata VirtualMachine usando il **Get-AzureRmVM** cmdlet. Il comando archivia la macchina virtuale nella variabile *$VirtualMachine* .

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```
I tre comandi successivi assegnano percorsi di tre dischi dati per le variabili $DataDiskVhdUri01 DataDiskVhdUri02 $ e $DataDiskVhdUri03.  I nomi dei percorsi diversi nel vhduri indicano diversi contenitori per il posizionamento del disco.
```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```
```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```
```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```


I tre comandi successivi aggiungono dischi dati alla macchina virtuale archiviata nel *$VirtualMachine* variabile. Ogni comando specifica il nome, posizione e le proprietà aggiuntive del disco. L'URI di ciascun disco viene archiviato in *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, e *$DataDiskVhdUri03*.

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                      -VhdUri $DataDiskVhdUri01 -LUN 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                      -VhdUri $DataDiskVhdUri02 -LUN 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                      -VhdUri $DataDiskVhdUri03 -LUN 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

L'ultimo comando Aggiorna lo stato della macchina virtuale archiviato in *$VirtualMachine* - in*ResourceGroupName*.

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sulle macchine virtuali di Azure Stack, vedere [considerazioni sulle macchine virtuali in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).
