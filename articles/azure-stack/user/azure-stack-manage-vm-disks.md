---
title: Gestire i dischi di macchina virtuale nello Stack di Azure | Documenti Microsoft
description: Il provisioning di dischi per le macchine virtuali per lo Stack di Azure.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/14/2017
ms.author: brenduns
ms.reviewer: jiahan
ms.openlocfilehash: 0c36e2eaaf2d266842b2b7de0b0c8dc0ed1e0145
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/15/2017
---
# <a name="virtual-machine-disk-storage-for-azure-stack"></a>Archiviazione su disco di macchina virtuale per lo Stack di Azure

*Si applica a: Azure Stack integrate di sistemi Azure Stack Development Kit*

Stack di Azure supporta l'utilizzo di [non gestita dischi](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds#unmanaged-disks) in una macchina virtuale come disco del sistema operativo (sistema operativo) e un disco dati. Per utilizzare i dischi non gestiti, creare un [account di archiviazione](https://docs.microsoft.com/azure/storage/common/storage-create-storage-account) e quindi archiviare i dischi come BLOB di pagine in contenitori nell'account di archiviazione. Questi dischi sono definiti come dischi di macchina virtuale.

Per migliorare le prestazioni e ridurre i costi di gestione del sistema Azure Stack, si consiglia di che posizionare ogni disco della macchina virtuale in un contenitore separato. Un contenitore può contenere un disco del sistema operativo o un disco dati, ma non entrambi contemporaneamente. Tuttavia, non vi è alcuna restrizione che impedisce l'inserimento nello stesso contenitore.

Se si aggiungono uno o più dischi dati a una macchina virtuale, prevede di utilizzare altri contenitori come un percorso per contenere questi dischi. Come i dischi dati, il disco del sistema operativo per le macchine virtuali aggiuntive deve essere anche i propri contenitori separati.

Quando si creano più macchine virtuali, è possibile riutilizzare lo stesso account di archiviazione per ogni nuova macchina virtuale. Solo i contenitori creati devono essere univoci.  

Per aggiungere i dischi a una macchina virtuale, utilizzare il portale per gli utenti o PowerShell.

| Metodo | Opzioni
|-|-|
|[Portale per gli utenti](#use-the-portal-to-add-additional-disks-to-a-vm)|-Aggiungere nuovi dischi dati a una macchina virtuale che è stato eseguito il provisioning in precedenza. Nuovi dischi vengono creati dallo Stack di Azure. </br> </br>-Aggiungere un file VHD esistente come un disco a una macchina virtuale che è stato eseguito il provisioning in precedenza. A tale scopo è innanzitutto necessario preparare e caricare il file con estensione vhd in Azure Stack. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | -Creare una nuova macchina virtuale con un disco del sistema operativo e allo stesso tempo, aggiungere uno o più dischi dati a tale macchina virtuale. |


## <a name="use-the-portal-to-add-additional-disks-to-a-vm"></a>Utilizzare il portale per aggiungere ulteriori dischi a una macchina virtuale
Per impostazione predefinita, quando si usa il portale per creare una macchina virtuale per la maggior parte degli elementi del marketplace, viene creato solo un disco del sistema operativo. I dischi creati da Azure sono denominati dischi gestiti.

Dopo avere stabilito una macchina virtuale, è possibile utilizzare il portale per aggiungere un nuovo disco dati o un disco dati esistente a tale macchina virtuale. Ogni disco aggiuntivo debba essere inserito in un contenitore separato. I dischi che si aggiunge a una macchina virtuale vengono chiamati i dischi non gestiti.

### <a name="use-the-portal-to-attach-a-new-data-disk-to-a-vm"></a>Utilizzare il portale per collegare un nuovo disco dati a una macchina virtuale

1.  Nel portale, fare clic su **macchine virtuali**.    
    ![Esempio: Dashboard macchina virtuale](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2.  Selezionare una macchina virtuale che in precedenza è stato eseguito il provisioning.   
    ![Esempio: Selezionare una macchina virtuale nel dashboard](media/azure-stack-manage-vm-disks/select-a-vm.png)

3.  Per la macchina virtuale, fare clic su **dischi** > **nuovo collegamento**.       
    ![Esempio: Collega un nuovo disco alla macchina virtuale](media/azure-stack-manage-vm-disks/Attach-disks.png)    

4.  Nel **Collega nuovo disco** riquadro, fare clic su **percorso**. Per impostazione predefinita, il percorso viene impostato allo stesso contenitore che contiene il disco del sistema operativo.      
    ![Esempio: Impostare il percorso del disco](media/azure-stack-manage-vm-disks/disk-location.png)

5.  Selezionare il **account di archiviazione** da utilizzare. Selezionare quindi il **contenitore** in cui si desidera inserire il disco dati. Dal **contenitori** pagina, è possibile creare un nuovo contenitore se si desidera. È quindi possibile modificare il percorso per il nuovo disco al contenitore. Quando si utilizza un contenitore separato per ogni disco, si distribuisce la posizione del disco dati che può migliorare le prestazioni. Fare clic su **selezionare** per salvare la selezione.     
    ![Esempio: Selezionare un contenitore](media/azure-stack-manage-vm-disks/select-container.png)

6.  Nel **Collega nuovo disco** pagina, aggiornare il **nome**, **tipo**, **dimensioni**, e **ospitare la memorizzazione nella cache** impostazioni del disco. Quindi fare clic su **OK** per salvare la nuova configurazione del disco per la macchina virtuale.  
    ![Esempio: Allegato completo del disco](media/azure-stack-manage-vm-disks/complete-disk-attach.png)  

7.  Dopo il dello Stack di Azure viene creato il disco che viene associato alla macchina virtuale, il nuovo disco viene elencato per le impostazioni del disco della macchina virtuale in **dischi dati**.   
    ![Esempio: Disco di vista](media/azure-stack-manage-vm-disks/view-data-disk.png)


### <a name="attach-an-existing-data-disk-to-a-vm"></a>Collegare un disco dati esistente a una macchina virtuale
1.  [Preparare un file con estensione vhd](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) da usare come disco dati per una macchina virtuale. Caricare il file con estensione vhd in un account di archiviazione utilizzati con la macchina virtuale che si desidera collegare il file con estensione vhd.

  Prevede di utilizzare un diverso contenitore deve per contenere il file con estensione vhd di contenitore che contiene il disco del sistema operativo.   
  ![Esempio: Caricare un file VHD](media/azure-stack-manage-vm-disks/upload-vhd.png)

2.  Dopo aver caricato il file con estensione vhd, si è pronti per collegare il disco rigido virtuale a una macchina virtuale. Nel menu a sinistra, fare clic su **macchine virtuali**.  
 ![Esempio: Selezionare una macchina virtuale nel dashboard](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3.  Selezionare la macchina virtuale dall'elenco.    
  ![Esempio: Selezionare una macchina virtuale nel dashboard](media/azure-stack-manage-vm-disks/select-a-vm.png)

4.  Nella pagina per la macchina virtuale, fare clic su **dischi** > **collegamento esistente**.   
  ![Esempio: Collegare un disco esistente](media/azure-stack-manage-vm-disks/attach-disks2.png)

5.  Nel **collegare un disco esistente** pagina, fare clic su **File VHD**. Il **gli account di archiviazione** verrà visualizzata la pagina.    
  ![Esempio: Selezionare un file di disco rigido virtuale](media/azure-stack-manage-vm-disks/select-vhd.png)

6.  In **gli account di archiviazione**, selezionare l'account da utilizzare e quindi selezionare un contenitore che include il file con estensione vhd caricato in precedenza. Selezionare il file con estensione vhd e quindi fare clic su **selezionare** per salvare la selezione.    
  ![Esempio: Selezionare un contenitore](media/azure-stack-manage-vm-disks/select-container2.png)

7.  In **collegare un disco esistente**, il file selezionato è elencato in **File VHD**. Aggiornamento di **ospitare la memorizzazione nella cache** l'impostazione del disco e quindi fare clic su **OK** per salvare la nuova configurazione del disco per la macchina virtuale.    
  ![Esempio: Collegare il file di disco rigido virtuale](media/azure-stack-manage-vm-disks/attach-vhd.png)

8.  Dopo il dello Stack di Azure viene creato il disco che viene associato alla macchina virtuale, il nuovo disco viene elencato per le impostazioni del disco della macchina virtuale in **dischi dati**.   
  ![Esempio: Completare il disco collegare](media/azure-stack-manage-vm-disks/complete-disk-attach.png)


## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Utilizzare PowerShell per aggiungere più dischi non gestiti a una macchina virtuale
È possibile utilizzare PowerShell per eseguire il provisioning di una macchina virtuale e aggiungere un nuovo disco dati o allegare un preesistenti **VHD** file come un disco dati.

Il **Aggiungi AzureRmVMDataDisk** cmdlet aggiunge un disco dati a una macchina virtuale. Quando si crea una macchina virtuale, oppure è possibile aggiungere un disco dati a una macchina virtuale esistente, è possibile aggiungere un disco dati. Specificare il **VhdUri** parametro per distribuire i dischi in contenitori diversi.

### <a name="add-data-disks-to-a-new-virtual-machine"></a>Aggiungere i dischi dati a una nuova macchina virtuale
L'esempio seguente usa i comandi di PowerShell per creare una macchina virtuale con tre dischi dati, ognuno in un contenitore diverso.

Il primo comando crea un oggetto macchina virtuale e quindi lo archivia nel *$VirtualMachine* variabile. Il comando Assegna un nome e le dimensioni per la macchina virtuale.
  ```
  $VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
  ```

I tre comandi successivi assegnano i percorsi di tre dischi dati per il *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, e *$DataDiskVhdUri03* variabili. Definire un nome di un percorso diverso nell'URL per distribuire i dischi in contenitori diversi.     
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```

  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```

  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```

I tre comandi finali aggiungono dischi dati alla macchina virtuale archiviata in *$VirtualMachine*. Ogni comando specifica il nome, percorso e le proprietà aggiuntive del disco. L'URI di ogni disco viene archiviato in *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, e *$DataDiskVhdUri03*.
  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                  -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                  -VhdUri $DataDiskVhdUri01 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                 -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                 -VhdUri $DataDiskVhdUri02 -CreateOption Empty
  ```

  ```
  $VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                  -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                  -VhdUri $DataDiskVhdUri03 -CreateOption Empty
  ```

Utilizzare i comandi di PowerShell seguenti per aggiungere la configurazione di rete e disco del sistema operativo nella macchina virtuale e quindi avviare la nuova macchina virtuale.
  ```
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

  # Create a network security group cnfiguration
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



### <a name="add-data-disks-to-an-existing-virtual-machine"></a>Aggiungere i dischi dati a una macchina virtuale esistente
L'esempio seguente usa i comandi di PowerShell per aggiungere tre dischi dati a una macchina virtuale esistente.
Il primo comando Ottiene la macchina virtuale denominata VirtualMachine utilizzando il **Get AzureRmVM** cmdlet. Il comando archivia la macchina virtuale nella variabile *$VirtualMachine* .
  ```
  $VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                  -Name "VirtualMachine"
  ```
I tre comandi successivi assegnano percorsi di tre dischi dati per le variabili $DataDiskVhdUri01, DataDiskVhdUri02 $ e $DataDiskVhdUri03.  I nomi di percorso diversi nel vhduri indicano contenitori diversi per il posizionamento del disco.
  ```
  $DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
  ```
  ```
  $DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
  ```
  ```
  $DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
  ```


  I tre comandi successivi aggiungono dischi di dati per la macchina virtuale archiviata nella *$VirtualMachine* variabile. Ogni comando specifica il nome, percorso e le proprietà aggiuntive del disco. L'URI di ogni disco viene archiviato in *$DataDiskVhdUri01*, *$DataDiskVhdUri02*, e *$DataDiskVhdUri03*.
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                        -VhdUri $DataDiskVhdUri01 -LUN 0 `
                        -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                        -VhdUri $DataDiskVhdUri02 -LUN 1 `
                        -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
  ```
  ```
  Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                        -VhdUri $DataDiskVhdUri03 -LUN 2 `
                        -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
  ```


  L'ultimo comando Aggiorna lo stato della macchina virtuale archiviato in *$VirtualMachine* in -*ResourceGroupName*.
  ```
  Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
  ```
<!-- Pending scripts  

## Distribute the data disks of an existing VM
If you have a VM with more than one disk in the same container, the service operator of the Azure Stack deployment might ask you to redistribute the disks into individual containers.

To do so, use the scripts from the following location in GitHub. These scripts can be used to move the data disks to different containers.
-->

## <a name="next-steps"></a>Passaggi successivi
Per ulteriori informazioni sulle macchine virtuali di Azure Stack, vedere [considerazioni per le macchine virtuali in Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-vm-considerations).
