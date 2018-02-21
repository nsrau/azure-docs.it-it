---
title: Creare una macchina virtuale da un'immagine gestita in Azure | Microsoft Docs
description: Creare una macchina virtuale Windows da un'immagine gestita generalizzata usando Azure PowerShell o il portale Azure nel modello di distribuzione Gestione risorse.
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 12/01/2017
ms.author: cynthn
ms.openlocfilehash: d8986c71fd0300af385750af898d620e6d3e1f24
ms.sourcegitcommit: cc03e42cffdec775515f489fa8e02edd35fd83dc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/07/2017
---
# <a name="create-a-vm-from-a-managed-image"></a>Creare una macchina virtuale da un'immagine gestita

È possibile creare più macchine virtuali da un'immagine gestita tramite PowerShell o il portale Azure. Un'immagine di macchina virtuale gestita contiene le informazioni necessarie per creare una macchina virtuale, inclusi i dischi dati e del sistema operativo. I dischi rigidi virtuali che costituiscono l'immagine, inclusi i dischi del sistema operativo e qualsiasi disco dati, vengono archiviati come dischi gestiti. 

È necessario aver già [creato un'immagine di macchina virtuale gestita](capture-image-resource.md) da usare per creare la nuova macchina virtuale. 

## <a name="use-the-portal"></a>Usare il portale

1. Aprire il [portale di Azure](https://portal.azure.com).
2. Nel menu a sinistra selezionare **Tutte le risorse**. È possibile ordinare le risorse per **tipo** per individuare facilmente le immagini.
3. Selezionare l'immagine che si intende usare dall'elenco. Si apre la pagina **Panoramica** delle immagini.
4. Fare clic su **+ Crea VM** dal menu.
5. Immettere le informazioni relative alla macchina virtuale. Il nome utente e la password immessi in questo modulo verranno usati per accedere alla macchina virtuale. Al termine fare clic su **OK**. È possibile creare la nuova macchina virtuale in un gruppo di risorse esistente o scegliere **Crea nuovo** per creare un nuovo gruppo di risorse per archiviare la macchina virtuale.
6. Selezionare una dimensione per la VM. Per visualizzare altre dimensioni, selezionare **Visualizza tutto** o modificare il filtro **Supported disk type** (Tipo di disco supportato). 
7. In **impostazioni**, apportare le modifiche in base alle esigenze e fare clic su **OK**. 
8. Nella pagina del riepilogo dovrebbe essere visualizzato il nome dell'immagine nell'elenco **Immagine privata**. Fare clic su **OK** per avviare la distribuzione della macchina virtuale.


## <a name="use-powershell"></a>Usare PowerShell

### <a name="prerequisites"></a>prerequisiti

Verificare di disporre della versione più recente dei moduli di PowerShell AzureRM.Compute e AzureRM.Network. Aprire un prompt dei comandi di PowerShell come Amministratore ed eseguire il comando seguente per installarli.

```azurepowershell-interactive
Install-Module AzureRM.Compute,AzureRM.Network
```
Per altre informazioni, vedere [Controllo delle versioni di Azure PowerShell](/powershell/azure/overview).



### <a name="collect-information-about-the-image"></a>Raccogliere informazioni relative all'immagine

Innanzitutto, è necessario raccogliere le informazioni di base dell'immagine e creare una variabile per l'immagine. Questo esempio usa un'immagine di macchina virtuale gestita denominata **myImage** nel gruppo di risorse **myResourceGroup** nell'area **Stati Uniti centro-occidentali**. 

```azurepowershell-interactive
$rgName = "myResourceGroup"
$location = "West Central US"
$imageName = "myImage"
$image = Get-AzureRMImage -ImageName $imageName -ResourceGroupName $rgName
```

### <a name="create-a-virtual-network"></a>Crea rete virtuale
Creare la rete virtuale e la subnet della [rete virtuale](../../virtual-network/virtual-networks-overview.md) stessa.

Creare la subnet. Questo esempio crea una subnet denominata **mySubnet** con un prefisso di indirizzo di **10.0.0.0/24**.  
   
```azurepowershell-interactive
$subnetName = "mySubnet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig `
    -Name $subnetName -AddressPrefix 10.0.0.0/24
```

Creare la rete virtuale. Questo esempio crea una rete virtuale denominata **myVnet** con un prefisso di indirizzo di **10.0.0.0/16**.  
   
```azurepowershell-interactive
$vnetName = "myVnet"
$vnet = New-AzureRmVirtualNetwork `
    -Name $vnetName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AddressPrefix 10.0.0.0/16 `
    -Subnet $singleSubnet
```    

### <a name="create-a-public-ip-address-and-network-interface"></a>Creare un indirizzo IP pubblico e un'interfaccia di rete

Per abilitare la comunicazione con la macchina virtuale nella rete virtuale, sono necessari un [indirizzo IP pubblico](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) e un'interfaccia di rete.

Creare un indirizzo IP pubblico. In questo esempio viene creato un indirizzo IP pubblico denominato **myPip**. 
   
```azurepowershell-interactive
$ipName = "myPip"
$pip = New-AzureRmPublicIpAddress `
    -Name $ipName `
    -ResourceGroupName $rgName `
    -Location $location `
    -AllocationMethod Dynamic
```
       
Creare la scheda NIC. In questo esempio viene creata una scheda NIC denominata **myNic**. 
   
```azurepowershell-interactive
$nicName = "myNic"
$nic = New-AzureRmNetworkInterface `
    -Name $nicName `
    -ResourceGroupName $rgName `
    -Location $location `
    -SubnetId $vnet.Subnets[0].Id `
    -PublicIpAddressId $pip.Id
```

### <a name="create-the-network-security-group-and-an-rdp-rule"></a>Creare il gruppo di sicurezza di rete e una regola RDP

Per essere in grado di accedere alla VM tramite RDP, è necessario disporre di una regola di sicurezza della rete (NSG) che consenta l'accesso RDP sulla porta 3389. 

In questo esempio viene creato un gruppo di sicurezza di rete denominato **myNsg** contenente una regola denominata **myRdpRule** che consente il traffico RDP sulla porta 3389. Per altre informazioni sui gruppi di sicurezza di rete, vedere [Apertura di porte a una VM tramite PowerShell](nsg-quickstart-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

```azurepowershell-interactive
$nsgName = "myNsg"
$ruleName = "myRdpRule"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name $ruleName -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


### <a name="create-a-variable-for-the-virtual-network"></a>Creare una variabile per la rete virtuale

Creare una variabile per la rete virtuale realizzata. 

```azurepowershell-interactive
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName

```

### <a name="get-the-credentials-for-the-vm"></a>Ottenere le credenziali per la macchina virtuale

Il cmdlet seguente apre una finestra in cui verrà immesso un nuovo nome utente e una nuova password da usare come account dell'amministratore locale per accedere in da remoto alla macchina virtuale. 

```azurepowershell-interactive
$cred = Get-Credential
```

### <a name="set-variables-for-the-vm-name-computer-name-and-the-size-of-the-vm"></a>Impostare le variabili per il nome della macchina virtuale, per il nome del computer e per le dimensioni della macchina virtuale

Creare variabili per il nome della macchina virtuale e per il nome del computer. In questo il nome della macchina virtuale viene impostato su **myVM** e il nome del computer viene impostato su **myComputer**.

```azurepowershell-interactive
$vmName = "myVM"
$computerName = "myComputer"
```

Impostare le dimensioni della macchina virtuale. Questo esempio crea una macchina virtuale con dimensione **Standard_DS1_v2**. Per altre informazioni, vedere la documentazione [Dimensioni per le macchine virtuali Windows in Azure](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/).

```azurepowershell-interactive
$vmSize = "Standard_DS1_v2"
```

Aggiungere il nome della macchina virtuale e le dimensioni per la configurazione della macchina virtuale.

```azurepowershell-interactive
$vm = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
```

### <a name="set-the-vm-image-as-source-image-for-the-new-vm"></a>Impostare l'immagine della macchina virtuale come immagine di origine per la nuova macchina virtuale

Impostare l'immagine di origine usando l'ID dell'immagine di macchina virtuale gestita.

```azurepowershell-interactive
$vm = Set-AzureRmVMSourceImage -VM $vm -Id $image.Id
```

### <a name="set-the-os-configuration-and-add-the-nic"></a>Impostare la configurazione del sistema operativo e aggiungere la scheda di interfaccia di rete.

Immettere il tipo di archiviazione (PremiumLRS o StandardLRS) e le dimensioni del disco del sistema operativo. In questo esempio il tipo di account viene impostato su **PremiumLRS**, le dimensioni del disco su **128 GB** e il caching del disco su **ReadWrite**.

```azurepowershell-interactive
$vm = Set-AzureRmVMOSDisk -VM $vm  `
    -StorageAccountType PremiumLRS `
    -DiskSizeInGB 128 `
    -CreateOption FromImage `
    -Caching ReadWrite

$vm = Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $computerName `
-Credential $cred -ProvisionVMAgent -EnableAutoUpdate

$vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
```

### <a name="create-the-vm"></a>Creare la VM

Creare la nuova macchina virtuale usando la configurazione creata e archiviata nella variabile **$vm**.

```azurepowershell-interactive
New-AzureRmVM -VM $vm -ResourceGroupName $rgName -Location $location
```

### <a name="verify-that-the-vm-was-created"></a>Verificare che la VM sia stata creata
Al termine, la VM appena creata dovrebbe essere visualizzata nel [portale di Azure](https://portal.azure.com) in **Browse** (Sfoglia)  > **Macchine virtuali**. In alternativa, è possibile usare i comandi PowerShell seguenti:

```azurepowershell-interactive
$vmList = Get-AzureRmVM -ResourceGroupName $rgName
$vmList.Name
```

## <a name="next-steps"></a>Passaggi successivi
Per gestire la nuova macchina virtuale con Azure PowerShell, vedere [Creare e gestire VM di Windows con il modulo Azure PowerShell](tutorial-manage-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

