---
title: Usare PowerShell per distribuire macchine virtuali di Azure spot
description: Informazioni su come usare Azure PowerShell per distribuire macchine virtuali spot per risparmiare sui costi.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 321983fbe99d17dc78198feb195eed8ea26de569
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82100618"
---
# <a name="deploy-spot-vms-using-azure-powershell"></a>Distribuire le VM spot usando Azure PowerShell


L'uso di [macchine virtuali con spot](spot-vms.md) consente di sfruttare la capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento, quando Azure necessita della capacità, l'infrastruttura di Azure eliminerà le VM spot. Quindi, le VM spot sono ottime per i carichi di lavoro in grado di gestire le interruzioni, ad esempio processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di calcolo di grandi dimensioni e altro ancora.

I prezzi per le VM spot sono variabili in base all'area e allo SKU. Per altre informazioni, vedere prezzi delle VM per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). Per altre informazioni sull'impostazione del prezzo massimo, vedere [spot VM-prezzi](spot-vms.md#pricing).

È possibile impostare un prezzo massimo che si è disposti a pagare, per ora, per la macchina virtuale. Il prezzo massimo per una VM spot può essere impostato in dollari USA (USD), usando un massimo di 5 cifre decimali. Ad esempio, il valore `0.98765`è un prezzo massimo di $0,98765 USD all'ora. Se si imposta il prezzo massimo `-1`, la macchina virtuale non verrà rimossa in base al prezzo. Il prezzo della macchina virtuale corrisponderà al prezzo corrente per spot o al prezzo di una macchina virtuale standard, che sempre è inferiore, purché siano disponibili capacità e quota.


## <a name="create-the-vm"></a>Creare la VM

Creare una spotVM usando [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) per creare la configurazione. Includere `-Priority Spot` e impostare `-MaxPrice` su:
- `-1`quindi, la macchina virtuale non viene rimossa in base al prezzo.
- importo in dollari, fino a 5 cifre. Ad esempio, `-MaxPrice .98765` indica che la macchina virtuale verrà deallocata quando il prezzo per un spotVM va a circa $98765 all'ora.


Questo esempio crea un spotVM che non verrà deallocato in base ai prezzi (solo quando Azure necessita della capacità).

```azurepowershell-interactive
$resourceGroup = "mySpotRG"
$location = "eastus"
$vmName = "mySpotVM"
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."
New-AzResourceGroup -Name $resourceGroup -Location $location
$subnetConfig = New-AzVirtualNetworkSubnetConfig `
   -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup `
   -Location $location -Name MYvNET -AddressPrefix 192.168.0.0/16 `
   -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration and set this to be a Spot VM

$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1 -Priority "Spot" -MaxPrice -1| `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2016-Datacenter -Version latest | `
Add-AzVMNetworkInterface -Id $nic.Id

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Dopo aver creato la macchina virtuale, è possibile eseguire una query per visualizzare il prezzo massimo per tutte le macchine virtuali nel gruppo di risorse.

```azurepowershell-interactive
Get-AzVM -ResourceGroupName $resourceGroup | `
   Select-Object Name,@{Name="maxPrice"; Expression={$_.BillingProfile.MaxPrice}}
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile creare una VM spot usando l' [interfaccia](../linux/spot-cli.md) della riga di comando di Azure o un [modello](../linux/spot-template.md).

Se si verifica un errore, vedere [codici di errore](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).