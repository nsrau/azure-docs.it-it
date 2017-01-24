---
title: "Creare una VM Windows con più schede di interfaccia di rete | Microsoft Docs"
description: "Informazioni su come creare una VM Windows con più schede di interfaccia di rete collegate usando i modelli di Azure PowerShell o Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: 9bff5b6d-79ac-476b-a68f-6f8754768413
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 10/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 7167048a287bee7c26cfc08775dcb84f9e7c2eed
ms.openlocfilehash: 46156a3331585b47761432c13462dffeb0b7eeb5


---
# <a name="creating-a-windows-vm-with-multiple-nics"></a>Creazione di una VM Windows con più schede di interfaccia di rete
È possibile creare una macchina virtuale (VM) in Azure con più interfacce di rete virtuale (NIC) collegate. Uno scenario comune è quello di avere subnet diverse per la connettività front-end e back-end, oppure disporre di una rete dedicata a una soluzione di monitoraggio o di backup. In questo articolo vengono presentati i comandi rapidi per creare una macchina virtuale con più schede di rete collegate. Per informazioni dettagliate, incluse quelle sulla creazione di più schede di rete all'interno degli script di PowerShell, consultare la sezione dedicata alla [distribuzione di macchine virtuali con più schede di rete](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Le differenti [dimensioni della macchina virtuale](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) supportano un numero variabile di schede di rete, pertanto scegliere le dimensioni della macchina virtuale di conseguenza.

> [!WARNING]
> È necessario collegare più schede di rete quando si crea una VM, poiché non è possibile aggiungere le schede di rete a una macchina virtuale esistente. È possibile [creare una nuova VM basata sui dischi virtuali originali](virtual-machines-windows-vhd-copy.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e creare più schede di interfaccia di rete mentre si distribuisce la VM.
> 
> 

## <a name="create-core-resources"></a>Creare le risorse principali
Verificare di aver prima [installato e configurato la versione più recente di Azure PowerShell](/powershell/azureps-cmdlets-docs). Accedere all'account di Azure:

```powershell
Login-AzureRmAccount
```

Nell'esempio seguente sostituire i nomi dei parametri di esempio con i valori desiderati. I nomi dei parametri di esempio includono `myResourceGroup`, `mystorageaccount` e `myVM`.

Creare prima un gruppo di risorse. Nell'esempio seguente viene creato un gruppo di risorse denominato `myResourceGroup` nella località `WestUs`:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

Creare un account di archiviazione in cui salvare le VM. Nell'esempio seguente viene creato un nuovo account di archiviazione denominato `mystorageaccount`:

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>Creare la rete virtuale e le subnet
Definire due subnet per la rete virtuale: una per il traffico front-end e l'altra per il traffico di back-end. Nell'esempio seguente vengono definite due subnet chiamate `mySubnetFrontEnd` e `mySubnetBackEnd`:

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

Creare una rete virtuale e le subnet. Nell'esempio seguente viene creata una rete virtuale chiamata `myVnet`:

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>Creare più schede di rete
Creare due schede di interfaccia di rete, quindi collegarne una alla subnet di front-end e l'altra alla subnet di back-end. Nell'esempio seguente vengono create due schede di interfaccia di rete chiamate `myNic1` e `myNic2`:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

In genere è necessario creare anche un [gruppo di sicurezza di rete](../virtual-network/virtual-networks-nsg.md) o un [servizio di bilanciamento del carico](../load-balancer/load-balancer-overview.md) per gestire e distribuire il traffico tra le VM. L'articolo dettagliato sulla [macchina virtuale con più schede di rete](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) offre una guida alla creazione di un gruppo di sicurezza di rete e all'assegnazione delle schede di rete.

## <a name="create-the-virtual-machine"></a>Creare la macchina virtuale
Ora è possibile iniziare con la configurazione della macchina virtuale. Ad ogni dimensione della macchina virtuale corrisponde un limite del numero totale di schede di rete che è possibile aggiungere. Ulteriori informazioni sulle [dimensioni delle macchine Virtuali di Windows](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Per prima cosa, impostare le credenziali della VM sulla variabile `$cred` come indicato di seguito:

```powershell
$cred = Get-Credential
```

Nell'esempio seguente viene definita una VM chiamata `myVM` e viene usata una dimensione della VM che supporta fino a due schede di interfaccia di rete (`Standard_DS2_v2`):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

Creare il resto del file config della VM. Nell'esempio seguente viene creata una VM di Windows Server 2012 R2:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName "myVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

Collegare le due schede di rete create in precedenza:

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

Configurare l'archiviazione e il disco virtuale per la nuova macchina virtuale:

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

Infine, creare una macchina virtuale:

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Creazione di più schede di rete utilizzando i modelli di Resource Manager
I modelli di Azure Resource Manager utilizzano i file JSON dichiarativi per definire l'ambiente. È possibile consultare una [panoramica di Azure Resource Manager](../azure-resource-manager/resource-group-overview.md). I modelli di Resource Manager offrono un modo di creare più istanze di una risorsa durante la distribuzione, come ad esempio la creazione di più schede di rete. Utilizzare *Copia* per specificare il numero di istanze da creare:

```json
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Ulteriori informazioni sulla [creazione di più istanze utilizzando *Copia*](../azure-resource-manager/resource-group-create-multiple.md). 

È inoltre possibile utilizzare un `copyIndex()` per poi aggiungere un numero al nome di una risorsa, che consente di creare `myNic1`, `MyNic2`, e così via. Di seguito viene riportato un esempio di aggiunta del valore di indice:

```json
"name": "[concat('myNic', copyIndex())]", 
```

È possibile consultare un esempio completo di [creazione di più schede di rete utilizzando i modelli di Resource Manager](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Passaggi successivi
Assicurarsi di consultare [Dimensioni delle macchine virtuali di Windows](virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) durante il tentativo di creazione di una macchina virtuale con più schede di rete. Prestare attenzione al numero massimo di schede di rete supportato per ogni dimensione della macchina virtuale. 

Tenere presente che non è possibile aggiungere altre schede di rete a una macchina virtuale esistente. È necessario creare tutte le schede di rete quando si distribuisce la macchina virtuale. Prestare attenzione quando si pianificano le distribuzioni per assicurarsi di avere la connettività di rete necessaria fin dall'inizio.




<!--HONumber=Jan17_HO1-->


