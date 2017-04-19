---
title: Creare una macchina virtuale da un&quot;immagine di macchina virtuale gestita in Azure | Documentazione Microsoft
description: Creare una macchina virtuale Windows da un&quot;immagine di macchina virtuale gestita generalizzata usando Azure PowerShell nel modello di distribuzione Resource Manager.
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
ms.date: 02/7/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: a43ccbdd539caa66840e5f7206cb8163665614a6
ms.lasthandoff: 03/31/2017


---
# <a name="create-a-vm-from-a-generalized-managed-vm-image"></a>Creare una VM da un'immagine di VM gestita generalizzata

È possibile creare più macchine virtuali da un'immagine di macchina virtuale gestita in Azure. Un'immagine di macchina virtuale gestita contiene le informazioni necessarie per creare una macchina virtuale, inclusi i dischi dati e del sistema operativo. I dischi rigidi virtuali che costituiscono l'immagine, inclusi i dischi del sistema operativo e qualsiasi disco dati, vengono archiviati come dischi gestiti. 

Tutte le informazioni sull'account personale sono state rimosse da un'immagine di macchina virtuale generalizzata tramite [Sysprep](generalize-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). È possibile creare una macchina virtuale generalizzata eseguendo Sysprep in una VM locale e quindi [caricando il disco rigido virtuale in Azure](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). In alternativa, è possibile eseguire Sysprep in una VM Azure esistente e quindi [creare un'immagine della macchina virtuale](capture-image-resource.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).



## <a name="prerequisites"></a>Prerequisiti

È necessario aver già [creato un'immagine di macchina virtuale gestita](capture-image-resource.md) da usare per creare la nuova macchina virtuale. 

Verificare di avere la versione più recente del modulo di PowerShell AzureRM.Compute. Eseguire il comando seguente per installarlo.

```powershell
Install-Module AzureRM.Compute -RequiredVersion 2.6.0
```
Per altre informazioni, vedere [Controllo delle versioni di Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/#azure-powershell-versioning).



## <a name="collect-information-about-the-image"></a>Raccogliere informazioni relative all'immagine

Innanzitutto, è necessario raccogliere le informazioni di base dell'immagine e creare una variabile per l'immagine. Questo esempio usa un'immagine di macchina virtuale gestita denominata **myImage** nel gruppo di risorse **myResourceGroup** nell'area **Stati Uniti centro-occidentali**. 

```powershell
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

## <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare la rete virtuale e la subnet della [rete virtuale](../../virtual-network/virtual-networks-overview.md) stessa.

1. Creare la subnet. Questo esempio crea una subnet denominata **mySubnet** con un prefisso di indirizzo di **10.0.0.0/24**.  
   
    ```powershell
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
2. Creare la rete virtuale. Questo esempio crea una rete virtuale denominata **myVnet** con un prefisso di indirizzo di **10.0.0.0/16**.  
   
    ```powershell
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
        -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    

## <a name="create-a-public-ip-address-and-network-interface"></a>Creare un indirizzo IP pubblico e un'interfaccia di rete

Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, sono necessari un [indirizzo IP pubblico](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

1. Creare un indirizzo IP pubblico. In questo esempio viene creato un indirizzo IP pubblico denominato **myPip**. 
   
    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
        -AllocationMethod Dynamic
    ```       
2. Creare la scheda NIC. In questo esempio viene creata una scheda NIC denominata **myNic**. 
   
    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location `
        -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creare il gruppo di sicurezza di rete e una regola RDP

Per essere in grado di accedere alla VM tramite RDP, è necessario disporre di una regola di sicurezza della rete (NSG) che consenta l'accesso RDP sulla porta 3389. 

In questo esempio viene creato un gruppo di sicurezza di rete denominato **myNsg** contenente una regola denominata **myRdpRule** che consente il traffico RDP sulla porta 3389. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Apertura di porte a una VM tramite PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```powershell
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Creare una variabile per la rete virtuale

Creare una variabile per la rete virtuale realizzata. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

## <a name="get-the-credentials-for-the-vm"></a>Ottenere le credenziali per la macchina virtuale

Il cmdlet seguente apre una finestra in cui verrà immesso un nuovo nome utente e una nuova password da usare come account dell'amministratore locale per accedere in da remoto alla macchina virtuale. 

```powershell
$cred = Get-Credential
```

## <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>Impostare le variabili per il nome della macchina virtuale, per il nome del computer e per le dimensioni della macchina virtuale

1. Creare variabili per il nome della macchina virtuale e per il nome del computer. In questo il nome della macchina virtuale viene impostato su **myVM** e il nome del computer viene impostato su **myComputer**.

    ```powershell
    $vmName = "myVM"
    $computerName = "myComputer"
    ```
2. Impostare le dimensioni della macchina virtuale. Questo esempio crea una macchina virtuale con dimensione **Standard_DS1_v2**. Per altre informazioni, vedere la documentazione [Dimensioni per le macchine virtuali Windows in Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/).

    ```powershell
    $vmSize = "Standard_DS1_v2"
    ```

3. Aggiungere il nome della macchina virtuale e le dimensioni per la configurazione della macchina virtuale.

```powershell
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

## <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Impostare l'immagine della macchina virtuale come immagine di origine per la nuova macchina virtuale

Impostare l'immagine di origine usando l'ID dell'immagine di macchina virtuale gestita.

```powershell
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

## <a name="set-the-os-configuration-and-add-the-nic"></a>Impostare la configurazione del sistema operativo e aggiungere la scheda di interfaccia di rete.

Immettere il tipo di archiviazione (PremiumLRS o StandardLRS) e le dimensioni del disco del sistema operativo. In questo esempio il tipo di account viene impostato su **PremiumLRS**, le dimensioni del disco su **128 GB** e il caching del disco su **ReadWrite**.

```powershell
$vm = Set-AzureRmVMOSDisk -VM $vm  -StorageAccountType PremiumLRS -DiskSizeInGB 128 `
-CreateOption FromImage -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

## <a name="create-the-vm"></a>Creare la VM

Creare la nuova macchina virtuale usando la configurazione creata e archiviata nella variabile **$vm**.

```powershell
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

## <a name="verify-that-the-vm-was-created"></a>Verificare che la VM sia stata creata
Al termine, la VM appena creata dovrebbe essere visualizzata nel [portale di Azure](https://portal.azure.com) in **Browse** (Sfoglia)  > **Macchine virtuali**. In alternativa, è possibile usare i comandi PowerShell seguenti:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Passaggi successivi
Per gestire la nuova macchina virtuale con Azure PowerShell, vedere [Gestire macchine virtuali di Azure con Azure Resource Manager e PowerShell](ps-manage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


