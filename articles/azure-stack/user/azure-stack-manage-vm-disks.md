---
title: Gestire i dischi delle macchine Virtuali in Azure Stack | Microsoft Docs
description: Effettuare il provisioning di dischi per macchine virtuali in Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/03/2018
ms.author: mabrigg
ms.reviewer: jiahan
ms.openlocfilehash: 473fb95de5da4a14c81d0fa3a5aafa33302d9ab2
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258680"
---
# <a name="provision-virtual-machine-disk-storage-in-azure-stack"></a>Effettuare il provisioning di archiviazione su disco di macchina virtuale in Azure Stack

*Si applica a: Azure Stack Development Kit e i sistemi integrati di Azure Stack*

Questo articolo descrive come eseguire il provisioning di archiviazione su disco di macchina virtuale usando il portale di Azure Stack o tramite PowerShell.

## <a name="overview"></a>Panoramica

A partire dalla versione 1808, Azure Stack supporta l'uso di managed disks e i dischi non gestiti nelle macchine virtuali, come un sistema operativo (SO) e un disco dati. Prima della versione 1808, sono supportati solo dischi non gestiti. 

**[I dischi gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#managed-disks)**  semplificare la gestione dei dischi per VM IaaS di Azure per gestire gli account di archiviazione associati ai dischi della macchina virtuale. È sufficiente specificare le dimensioni del disco necessario, e Azure Stack crea e gestisce automaticamente il disco.

**[Dischi non gestiti](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks)**, è necessario creare un [account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) per archiviare i dischi. I dischi creati vengono definiti dischi di macchine Virtuali e vengono archiviati in contenitori nell'account di archiviazione.

 

### <a name="best-practice-guidelines"></a>Procedure ottimali

Per migliorare le prestazioni e ridurre i costi complessivi, si consiglia di che posizionare ogni disco della macchina virtuale in un contenitore separato. Un contenitore deve contenere un disco del sistema operativo o un disco dati, ma non entrambi allo stesso tempo. (Tuttavia, vi è nulla a che non consentono l'inserimento di entrambi i tipi di disco nello stesso contenitore).

Se si aggiungono uno o più dischi dati a una macchina virtuale, usare i contenitori aggiuntivi come un percorso per archiviare questi dischi. Disco del sistema operativo per le macchine virtuali aggiuntive deve anche essere nei propri contenitori.

Quando si creano più VM, è possibile riutilizzare lo stesso account di archiviazione per ogni nuova macchina virtuale. Solo i contenitori creati devono essere univoci.

### <a name="adding-new-disks"></a>Aggiunta di nuovi dischi

La tabella seguente riepiloga come aggiungere i dischi tramite il portale e PowerShell.

| Metodo | Opzioni
|-|-|
|[Portale per gli utenti](#use-the-portal-to-add-additional-disks-to-a-vm)|-Aggiungere nuovi dischi dati a una VM esistente. Vengono creati nuovi dischi tramite Azure Stack. </br> </br>-Aggiungere un file del disco (VHD) a una VM con provisioning in precedenza. A tale scopo, è necessario preparare il file VHD e quindi caricare il file di Azure Stack. |
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

1.  Nel portale, scegliere **macchine virtuali**.    
    ![Esempio: Dashboard della macchina virtuale](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Selezionare una macchina virtuale che è stata fornita in precedenza.   
    ![Esempio: Selezionare una macchina virtuale nel dashboard](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  Per la macchina virtuale, selezionare **Disks** > **Collega nuovo**.       
    ![Esempio: Collegare un nuovo disco alla macchina virtuale](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  Nel **collega un nuovo disco** riquadro, selezionare **posizione**. Per impostazione predefinita, la posizione viene impostata allo stesso contenitore che contiene il disco del sistema operativo.      
    ![Esempio: Impostare il percorso del disco](media/azure-stack-manage-vm-disks/disk-location.png)

5.  Selezionare il **account di archiviazione** da usare. Selezionare quindi il **contenitore** in cui si desidera inserire il disco dati. Dal **contenitori** pagina, è possibile creare un nuovo contenitore se si desidera. È quindi possibile modificare il percorso per il nuovo disco per il proprio contenitore. Quando si usa un contenitore separato per ogni disco, si distribuisce il posizionamento del disco dati che consentono di migliorare le prestazioni. Scegli **seleziona** per salvare la selezione.     
    ![Esempio: Selezionare un contenitore](media/azure-stack-manage-vm-disks/select-container.png)

6.  Nel **collega un nuovo disco** pagina, aggiornare il **Name**, **tipo**, **dimensione**, e **ospitare la memorizzazione nella cache** impostazioni del disco. Quindi selezionare **OK** per salvare la nuova configurazione di disco della macchina virtuale.  
    ![Esempio: Collegamento di dischi completata](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Dopo che Azure Stack viene creato il disco e la collega alla macchina virtuale, il nuovo disco viene elencato nella sezione Impostazioni disco della macchina virtuale in **dischi dati**.   
    ![Esempio: Visualizzare i dischi](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>Collegare un disco dati esistente a una macchina virtuale

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
  ![Esempio: Selezionare un contenitore](media/azure-stack-manage-vm-disks/select-container2.png)

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
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>Fasi successive
Per altre informazioni sulle macchine virtuali di Azure Stack, vedere [considerazioni sulle macchine virtuali in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).
