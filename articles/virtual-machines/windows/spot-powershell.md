---
title: Usare PowerShell per distribuire macchine virtuali di Azure SpotUse PowerShell to deploy Azure Spot VMs
description: Informazioni su come usare Azure PowerShell per distribuire macchine virtuali Spot per risparmiare sui costi.
services: virtual-machines-windows
author: cynthn
manager: gwallace
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: cynthn
ms.openlocfilehash: 17186d1d7b50ea872dc47eca8c2c4491787d2a38
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77158945"
---
# <a name="preview-deploy-spot-vms-using-azure-powershell"></a>Anteprima: Distribuire macchine virtuali spot tramite Azure PowerShellPreview: Deploy Spot VMs using Azure PowerShell


L'uso di [Spot VMs](spot-vms.md) consente di sfruttare la nostra capacità inutilizzata con un notevole risparmio sui costi. In qualsiasi momento in cui Azure richiede la capacità, l'infrastruttura di Azure rimuovere le macchine virtuali Spot.At any in time when Azure needs the capacity back, the Azure infrastructure will evict Spot VMs. Pertanto, le macchine virtuali Spot sono ideali per i carichi di lavoro in grado di gestire interruzioni come processi di elaborazione batch, ambienti di sviluppo/test, carichi di lavoro di elaborazione di grandi dimensioni e altro ancora.

I prezzi per le macchine virtuali Spot sono variabili, in base all'area e all'SKU. Per altre informazioni, vedere Prezzi delle macchine virtuali per [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows.](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) Per altre informazioni sull'impostazione del prezzo massimo, vedere [Spot VMs - Pricing](spot-vms.md#pricing).

È possibile impostare un prezzo massimo che si è disposti a pagare, all'ora, per la macchina virtuale. Il prezzo massimo per una macchina virtuale Spot può essere impostato in dollari USA (USD), usando fino a 5 cifre decimali. Ad esempio, `0.98765`il valore sarebbe un prezzo massimo di 0,98765 USD all'ora. Se si imposta il `-1`prezzo massimo su , la macchina virtuale non verrà rimossa in base al prezzo. Il prezzo per la macchina virtuale sarà il prezzo corrente per spot o il prezzo per una macchina virtuale standard, che è sempre inferiore, purché siano disponibili capacità e quote.

> [!IMPORTANT]
> Le istanze spot sono attualmente in anteprima pubblica.
> Questa versione di anteprima non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>



## <a name="create-the-vm"></a>Creare la VM

Creare una spotVM usando [New-AzVmConfig](/powershell/module/az.compute/new-azvmconfig) per creare la configurazione. Includi `-Priority Spot` e `-MaxPrice` imposta su:
- `-1`in modo che la macchina virtuale non venga rimossa in base al prezzo.
- un importo in dollari, fino a 5 cifre. Ad esempio, `-MaxPrice .98765` significa che la macchina virtuale verrà deallocata una volta che il prezzo di una spotVM è di circa 0,98765 USD all'ora.


Questo esempio crea una spotVM che non verrà deallocata in base ai prezzi (solo quando Azure ha bisogno di riavere la capacità).

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

È anche possibile creare una macchina virtuale Spot usando [l'interfaccia della riga di comando](../linux/spot-cli.md) di Azure o un [modello.](../linux/spot-template.md)

Se si verifica un errore, vedere [Codici di errore](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).