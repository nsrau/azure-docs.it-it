---
title: "Esercitazione sui set di disponibilità per macchine virtuali Windows in Azure | Microsoft Docs"
description: "Informazioni sui set di disponibilità per macchine virtuali Windows in Azure."
documentationcenter: 
services: virtual-machines-windows
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
ms.date: 04/26/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 831c55939ad3673aa8e44f165e18e826f64b54cc
ms.contentlocale: it-it
ms.lasthandoff: 05/03/2017


---

# <a name="how-to-use-availability-sets"></a>Come usare i set di disponibilità

In questa esercitazione sarà descritto come aumentare la disponibilità delle macchine virtuali (VM), inserendole in un raggruppamento logico denominato set di disponibilità. Quando si creano macchine virtuali in un set di disponibilità, la piattaforma Azure ne distribuirà il posizionamento nell'infrastruttura sottostante. Se si verifica un errore dell'hardware o viene effettuata la manutenzione pianificata della piattaforma, l'uso dei set di disponibilità garantisce che almeno una macchina virtuale rimanga in esecuzione.

La procedura descritta in questa esercitazione può essere completata usando il modulo [Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/) più recente.

## <a name="availability-set-overview"></a>Informazioni generali sui set di disponibilità

È possibile creare macchine virtuali in raggruppamenti logici di hardware nel data center di Azure sottostante. Quando si creano due o più macchine virtuali, le risorse di calcolo e di archiviazione vengono distribuite sull'hardware, cioè tra server, commutatori di rete e risorse di archiviazione. Questa distribuzione assicura la disponibilità dell'app in caso di manutenzione di un componente hardware. I set di disponibilità consentono di definire questo raggruppamento logico.

I set di disponibilità garantiscono un'elevata disponibilità per le macchine virtuali. Verificare anche che le applicazioni siano progettate per tollerare interruzioni o eventi di manutenzione.

## <a name="create-an-availability-set"></a>Creare un set di disponibilità

È possibile creare un set di disponibilità con il comando [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). In questo esempio, sia il numero di domini di aggiornamento che quello di domini di errore vengono impostati su *2* per il set di disponibilità denominato *myAvailabilitySet* nel gruppo di risorse *myResourceGroupAvailability*.


```powershell
New-AzureRmAvailabilitySet `
   -Location EastUS `
   -Name myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability `
   -Managed `
   -PlatformFaultDomainCount 2 `
   -PlatformUpdateDomainCount 2
```

## <a name="create-vms-inside-an-availability-set"></a>Creare macchine virtuali in un set di disponibilità

Per garantire la corretta distribuzione delle macchine virtuali in tutto l'hardware, le VM devono essere create all'interno del set di disponibilità. Non è possibile aggiungere una macchina virtuale esistente a un set di disponibilità dopo la sua creazione. 

L'hardware in un percorso è suddiviso in più domini di aggiornamento e domini di errore. I **domini di aggiornamento** sono gruppi di macchine virtuali con relativo hardware fisico sottostante che è possibile riavviare nello stesso momento. Le macchine virtuali nello stesso **dominio di errore** condividono risorse di archiviazione comuni, nonché un alimentatore e un commutatore di rete comune. 

Quando si crea la configurazione di una macchina virtuale usando il comando [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig), si specifica il set di disponibilità usando il parametro `-AvailabilitySetId` per specificare l'ID del set di disponibilità.

Creare 2 macchine virtuali con il comando [New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm) nel set di disponibilità.

```powershell
$availabilitySet = Get-AzureRmAvailabilitySet `
    -ResourceGroupName myResourceGroupAvailability `
    -Name myAvailabilitySet

$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name mySubnet `
    -AddressPrefix 192.168.1.0/24
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName myResourceGroupAvailability `
    -Location EastUS `
    -Name MYvNET `
    -AddressPrefix 192.168.0.0/16 `
    -Subnet $subnetConfig

for ($i=1; $i -le 2; $i++)
{
   $pip = New-AzureRmPublicIpAddress `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name "mypublicdns$(Get-Random)" `
        -AllocationMethod Static `
        -IdleTimeoutInMinutes 4

   $nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
        -Name myNetworkSecurityGroupRuleRDP$i `
        -Protocol Tcp `
        -Direction Inbound `
        -Priority 1000 `
        -SourceAddressPrefix * `
        -SourcePortRange * `
        -DestinationAddressPrefix * `
        -DestinationPortRange 3389 `
        -Access Allow

   $nsg = New-AzureRmNetworkSecurityGroup `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -Name myNetworkSecurityGroup$i `
        -SecurityRules $nsgRuleRDP

   $nic = New-AzureRmNetworkInterface `
        -Name myNic$i `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -SubnetId $vnet.Subnets[0].Id `
        -PublicIpAddressId $pip.Id `
        -NetworkSecurityGroupId $nsg.Id

   # Here is where we specify the availability set
   $vm = New-AzureRmVMConfig `
        -VMName myVM$i `
        -VMSize Standard_D1 `
        -AvailabilitySetId $availabilitySet.Id

   $vm = Set-AzureRmVMOperatingSystem `
        -VM $vm `
        -Windows -ComputerName myVM$i `   
        -Credential $cred `
        -ProvisionVMAgent `
        -EnableAutoUpdate
   $vm = Set-AzureRmVMSourceImage `
        -VM $vm `
        -PublisherName MicrosoftWindowsServer `
        -Offer WindowsServer `
        -Skus 2016-Datacenter `
        -Version latest
   $vm = Set-AzureRmVMOSDisk `
        -VM $vm `
        -Name myOsDisk$i `
        -DiskSizeInGB 128 `
        -CreateOption FromImage `
        -Caching ReadWrite
   $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
   New-AzureRmVM `
        -ResourceGroupName myResourceGroupAvailability `
        -Location EastUS `
        -VM $vm
}

```

La creazione e la configurazione di entrambe le macchine virtuali richiedono alcuni minuti. Al termine, si avranno 2 macchine virtuali distribuite nell'hardware sottostante. 

## <a name="check-for-available-vm-sizes"></a>Controllare le dimensioni delle macchine virtuali disponibili 

È possibile aggiungere più macchine virtuali al set di disponibilità in un secondo momento, ma è necessario sapere quali dimensioni di macchina virtuale sono disponibili nell'hardware. Usare il comando [Get-AzureRMVMSize](/powershell/module/azurerm.compute/get-azurermvmsize) per elencare tutte le dimensioni disponibili nel cluster hardware per il set di disponibilità.

```powershell
Get-AzureRmVMSize `
   -AvailabilitySetName myAvailabilitySet `
   -ResourceGroupName myResourceGroupAvailability  
```

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione è stato descritto come creare un set di scalabilità di macchine virtuali. Passare all'esercitazione successiva per informazioni sui set di scalabilità di macchine virtuali.

[Creare un set di scalabilità di macchine virtuali](tutorial-create-vmss.md)



