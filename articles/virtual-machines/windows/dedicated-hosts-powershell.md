---
title: Distribuire host dedicati di Azure tramite Azure PowerShellDeploy Azure dedicated hosts using the Azure PowerShell
description: Distribuire macchine virtuali in host dedicati tramite Azure PowerShell.Deploy VMs to dedicated hosts using Azure PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: b90189c6ba5e51a24d0c248b5aa08e9a5e4bbd9b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2020
ms.locfileid: "82082850"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Distribuire macchine virtuali ad host dedicati usando Azure PowerShellDeploy VMs to dedicated hosts using the Azure PowerShell

Questo articolo illustra come creare un [host dedicato di](dedicated-hosts.md) Azure per ospitare le macchine virtuali (VM). 

Assicurarsi di aver installato Azure PowerShell versione 2.8.0 o successiva e di `Connect-AzAccount`aver eseguito l'accesso a un account azure in con . 

## <a name="limitations"></a>Limitazioni

- I set di scalabilità delle macchine virtuali non sono attualmente supportati in host dedicati.
- Le dimensioni e i tipi di hardware disponibili per gli host dedicati variano in base all'area geografica. Per ulteriori informazioni, consulta la [pagina dei prezzi](https://aka.ms/ADHPricing) host.

## <a name="create-a-host-group"></a>Creare un gruppo host

Un **gruppo host** è una risorsa che rappresenta una raccolta di host dedicati. Creare un gruppo host in un'area e una zona di disponibilità e aggiungervi host. Quando si pianifica la disponibilità elevata, sono disponibili opzioni aggiuntive. È possibile utilizzare una o entrambe le seguenti opzioni con gli host dedicati: 
- Estendere più zone di disponibilità. In questo caso, è necessario disporre di un gruppo host in ciascuna delle zone che si desidera utilizzare.
- Estendetevi su più domini di errore mappati a rack fisici. 
 
In entrambi i casi, è necessario fornire il conteggio dei domini di errore per il gruppo host. Se non si desidera estendere i domini di errore nel gruppo, utilizzare un conteggio dei domini di errore pari a 1. 

È inoltre possibile decidere di utilizzare sia le zone di disponibilità che i domini di errore. In questo esempio viene creato un gruppo host nella zona 1, con 2 domini di errore. 


```azurepowershell-interactive
$rgName = "myDHResourceGroup"
$location = "EastUS"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Creare un hostCreate a host

Ora creiamo un host dedicato nel gruppo host. Oltre a un nome per l'host, è necessario fornire lo SKU per l'host. Lo SKU host acquisisce la serie di macchine virtuali supportate e la generazione dell'hardware per l'host dedicato.

Per altre informazioni sugli SKU e sui prezzi dell'host, vedere [Prezzi dell'host dedicato di Azure.For more](https://aka.ms/ADHPricing)information about the host SKUs and pricing, see Azure Dedicated Host pricing .

Se imposti un numero di domini di errore per il tuo gruppo host, ti verrà chiesto di specificare il dominio di errore per il tuo host. In questo esempio, impostiamo il dominio di errore per l'host su 1.In this example, we set the fault domain for the host to 1.


```azurepowershell-interactive
$dHost = New-AzHost `
   -HostGroupName $hostGroup.Name `
   -Location $location -Name myHost `
   -ResourceGroupName $rgName `
   -Sku DSv3-Type1 `
   -AutoReplaceOnFailure 1 `
   -PlatformFaultDomain 1
```

## <a name="create-a-vm"></a>Creare una macchina virtuale

Creare una macchina virtuale nell'host dedicato. 

Se è stata specificata una zona di disponibilità durante la creazione del gruppo host, è necessario utilizzare la stessa zona durante la creazione della macchina virtuale. Per questo esempio, poiché il gruppo host si trova nella zona 1, è necessario creare la macchina virtuale nella zona 1.For this example, because our host group is in zone 1, we need to create the VM in zone 1.  


```azurepowershell-interactive
$cred = Get-Credential
New-AzVM `
   -Credential $cred `
   -ResourceGroupName $rgName `
   -Location $location `
   -Name myVM `
   -HostId $dhost.Id `
   -Image Win2016Datacenter `
   -Zone 1 `
   -Size Standard_D4s_v3
```

> [!WARNING]
> Se si crea una macchina virtuale in un host che non dispone di risorse sufficienti, la macchina virtuale verrà creata in uno stato non riuscito. 

## <a name="check-the-status-of-the-host"></a>Controllare lo stato dell'host

È possibile controllare lo stato di integrità dell'host e il numero di `-InstanceView` macchine virtuali che è comunque possibile distribuire all'host usando [GetAzHost](/powershell/module/az.compute/get-azhost) con il parametro .

```azurepowershell-interactive
Get-AzHost `
   -ResourceGroupName $rgName `
   -Name myHost `
   -HostGroupName $hostGroup.Name `
   -InstanceView
```

L'output sarà simile al seguente:

```
ResourceGroupName      : myDHResourceGroup
PlatformFaultDomain    : 1
AutoReplaceOnFailure   : True
HostId                 : 12345678-1234-1234-abcd-abc123456789
ProvisioningTime       : 7/28/2019 5:31:01 PM
ProvisioningState      : Succeeded
InstanceView           : 
  AssetId              : abc45678-abcd-1234-abcd-123456789abc
  AvailableCapacity    : 
    AllocatableVMs[0]  : 
      VmSize           : Standard_D2s_v3
      Count            : 32
    AllocatableVMs[1]  : 
      VmSize           : Standard_D4s_v3
      Count            : 16
    AllocatableVMs[2]  : 
      VmSize           : Standard_D8s_v3
      Count            : 8
    AllocatableVMs[3]  : 
      VmSize           : Standard_D16s_v3
      Count            : 4
    AllocatableVMs[4]  : 
      VmSize           : Standard_D32-8s_v3
      Count            : 2
    AllocatableVMs[5]  : 
      VmSize           : Standard_D32-16s_v3
      Count            : 2
    AllocatableVMs[6]  : 
      VmSize           : Standard_D32s_v3
      Count            : 2
    AllocatableVMs[7]  : 
      VmSize           : Standard_D64-16s_v3
      Count            : 1
    AllocatableVMs[8]  : 
      VmSize           : Standard_D64-32s_v3
      Count            : 1
    AllocatableVMs[9]  : 
      VmSize           : Standard_D64s_v3
      Count            : 1
  Statuses[0]          : 
    Code               : ProvisioningState/succeeded
    Level              : Info
    DisplayStatus      : Provisioning succeeded
    Time               : 7/28/2019 5:31:01 PM
  Statuses[1]          : 
    Code               : HealthState/available
    Level              : Info
    DisplayStatus      : Host available
Sku                    : 
  Name                 : DSv3-Type1
Id                     : /subscriptions/10101010-1010-1010-1010-101010101010/re
sourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts
/myHost
Name                   : myHost
Location               : eastus
Tags                   : {}
```

## <a name="add-an-existing-vm"></a>Aggiungere una macchina virtuale esistenteAdd an existing VM 

È possibile aggiungere una macchina virtuale esistente a un host dedicato, ma la macchina virtuale deve prima essere Arrestato- Dealallocato. Prima di spostare una macchina virtuale in un host dedicato, assicurarsi che la configurazione della macchina virtuale sia supportata:Before you move a VM to a dedicated host, make sure that the VM configuration is supported:

- La dimensione della macchina virtuale deve essere nella stessa famiglia di dimensioni dell'host dedicato. Ad esempio, se l'host dedicato è DSv3, la dimensione della macchina virtuale potrebbe essere Standard_D4s_v3, ma non potrebbe essere un Standard_A4_v2. 
- La macchina virtuale deve trovarsi nella stessa area dell'host dedicato.
- La macchina virtuale non può far parte di un gruppo di posizionamento di prossimità. Rimuovere la macchina virtuale dal gruppo di posizionamento di prossimità prima di spostarla in un host dedicato. Per altre informazioni, vedere [Spostare una macchina virtuale da un gruppo di posizionamento di prossimitàFor](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups#move-an-existing-vm-out-of-a-proximity-placement-group) more information, see Move a VM out of a proximity placement group
- La macchina virtuale non può essere in un set di disponibilità.
- Se la macchina virtuale si trova in una zona di disponibilità, deve essere la stessa zona di disponibilità del gruppo host. Le impostazioni della zona di disponibilità per la macchina virtuale e il gruppo host devono corrispondere.

Sostituire i valori delle variabili con le proprie informazioni.

```azurepowershell-interactive
$vmRGName = "movetohost"
$vmName = "myVMtoHost"
$dhRGName = "myDHResourceGroup"
$dhGroupName = "myHostGroup"
$dhName = "myHost"

$myDH = Get-AzHost `
   -HostGroupName $dhGroupName `
   -ResourceGroupName $dhRGName `
   -Name $dhName
   
$myVM = Get-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
   
$myVM.Host = New-Object Microsoft.Azure.Management.Compute.Models.SubResource

$myVM.Host.Id = "$myDH.Id"

Stop-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName -Force
   
Update-AzVM `
   -ResourceGroupName $vmRGName `
   -VM $myVM -Debug
   
Start-AzVM `
   -ResourceGroupName $vmRGName `
   -Name $vmName
```


## <a name="clean-up"></a>Eseguire la pulizia

Vengono addebitati gli host dedicati anche quando non sono distribuite macchine virtuali. È necessario eliminare tutti gli host attualmente non in uso per risparmiare sui costi.  

È possibile eliminare un host solo quando non sono presenti più macchine virtuali che lo utilizzano. Eliminare le macchine virtuali utilizzando [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Dopo aver eliminato le macchine virtuali, è possibile eliminare l'host utilizzando [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Dopo aver eliminato tutti gli host, è possibile eliminare il gruppo host utilizzando [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

È inoltre possibile eliminare l'intero gruppo di risorse in un singolo comando utilizzando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Verranno eliminate tutte le risorse create nel gruppo, incluse tutte le macchine virtuali, gli host e i gruppi host.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Passaggi successivi

- È disponibile un modello di esempio, disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), che utilizza sia zone che domini di errore per la massima resilienza in un'area.

- È anche possibile distribuire host dedicati usando il portale di [Azure.](dedicated-hosts-portal.md)
