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
ms.date: 05/08/2017
ms.author: cynthn
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 022396a8bf0478414be179b9f7341a459ed2bc60
ms.contentlocale: it-it
ms.lasthandoff: 05/09/2017


---

# <a name="how-to-use-availability-sets"></a>Come usare i set di disponibilità

In questa esercitazione si apprenderà come aumentare la disponibilità e l'affidabilità delle soluzioni delle proprie macchine virtuali in Azure tramite una funzionalità denominata set di disponibilità. I set di disponibilità assicurano che le macchine virtuali distribuite in Azure vengano distribuite tra più cluster hardware isolati. Questa operazione assicura che, se si verifica un errore hardware o software all'interno di Azure, solo un sottoinsieme delle macchine virtuali verrà interessato e la soluzione complessiva rimarrà disponibile e operativa dal punto di vista dei clienti che la usano. 

In questa esercitazione si apprenderà come:

> [!div class="checklist"]
> * Creare un set di disponibilità
> * Creare una macchina virtuale in un set di disponibilità
> * Controllare le dimensioni delle macchine virtuali disponibili

Questa esercitazione richiede il modulo Azure PowerShell 3.6 o versioni successive. Eseguire ` Get-Module -ListAvailable AzureRM` per trovare la versione. Se è necessario eseguire l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="availability-set-overview"></a>Informazioni generali sui set di disponibilità

Un set di disponibilità è una funzionalità di raggruppamento logico che è possibile usare in Azure per garantire che le risorse delle macchine virtuali inserite dall'utente siano isolate tra loro quando vengono distribuite all'interno di un data center di Azure. Azure garantisce che le macchine virtuali inserite all'interno di un set di disponibilità vengano eseguite tra più server fisici, rack di calcolo, unità di archiviazione e commutatori di rete. Ciò garantisce che in caso di errore hardware o software in Azure, verrà interessato solo un sottoinsieme delle macchine virtuali e l'applicazione complessiva si manterrà aggiornata e continuerà a essere disponibile per i clienti. L'uso dei set di disponibilità è una funzionalità essenziale da sfruttare quando si desidera creare soluzioni di cloud affidabili.

Si consideri una soluzione tipica basata su macchine virtuali, in cui si dispone di 4 server Web front-end e vengono usate 2 macchine virtuali di back-end che ospitano un database. Con Azure è possibile definire due set di disponibilità prima di distribuire le macchine virtuali: un set di disponibilità per il livello "Web" e un set di disponibilità per il livello "database". Quando si crea una nuova macchina virtuale, è quindi possibile specificare il set di disponibilità come parametro per il comando az vm create. Azure automaticamente garantirà che le macchine virtuali create all'interno del set di disponibilità vengano isolate su più risorse hardware fisiche. Ciò significa che, se l'hardware fisico su cui è in esecuzione una delle macchine virtuali dei server di database o dei server Web presenta un problema, le altre istanze delle macchine virtuali dei server Web e di database rimarranno in esecuzione correttamente perché si trovano su un hardware diverso.

Usare sempre i set di disponibilità quando si vogliono distribuire soluzioni affidabili basate su macchine virtuali all'interno di Azure.

## <a name="create-an-availability-set"></a>Creare un set di disponibilità

È possibile creare un set di disponibilità con il comando [New-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/new-azurermavailabilityset). In questo esempio, sia il numero di domini di aggiornamento che quello di domini di errore vengono impostati su *2* per il set di disponibilità denominato *myAvailabilitySet* nel gruppo di risorse *myResourceGroupAvailability*.

Creare un gruppo di risorse.

```powershell
New-AzureRmResourceGroup -Name myResourceGroupAvailability -Location EastUS
```


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

In questa esercitazione si è appreso come:

> [!div class="checklist"]
> * Creare un set di disponibilità
> * Creare una macchina virtuale in un set di disponibilità
> * Controllare le dimensioni delle macchine virtuali disponibili

Passare all'esercitazione successiva per informazioni sui set di scalabilità di macchine virtuali.

> [!div class="nextstepaction"]
> [Creare un set di scalabilità di macchine virtuali](tutorial-create-vmss.md)



