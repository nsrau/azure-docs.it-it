---
title: Distribuire host dedicati di Azure usando il Azure PowerShell
description: Distribuire le macchine virtuali in host dedicati usando Azure PowerShell.
author: cynthn
ms.service: virtual-machines-windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.reviewer: zivr
ms.openlocfilehash: 599d13daac2e062c8f71f5f7d7133646a1447123
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87266589"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Distribuire macchine virtuali in host dedicati usando il Azure PowerShell

Questo articolo illustra come creare un [host dedicato](dedicated-hosts.md) di Azure per ospitare le macchine virtuali (VM). 

Assicurarsi di aver installato Azure PowerShell versione 2.8.0 o successiva ed è stato effettuato l'accesso a un account Azure in con `Connect-AzAccount` . 

## <a name="limitations"></a>Limitazioni

- I set di scalabilità di macchine virtuali non sono attualmente supportati negli host dedicati.
- Le dimensioni e i tipi di hardware disponibili per gli host dedicati variano in base all'area. Per ulteriori informazioni, fare riferimento alla [pagina dei prezzi](https://aka.ms/ADHPricing) dell'host.

## <a name="create-a-host-group"></a>Creare un gruppo host

Un **gruppo host** è una risorsa che rappresenta una raccolta di host dedicati. È necessario creare un gruppo host in un'area e in una zona di disponibilità e aggiungervi gli host. Quando si pianifica la disponibilità elevata, sono disponibili opzioni aggiuntive. Con gli host dedicati è possibile usare una o entrambe le opzioni seguenti: 
- Estendersi tra più zone di disponibilità. In questo caso, è necessario disporre di un gruppo host in ognuna delle zone che si desidera usare.
- Estendersi tra più domini di errore mappati a rack fisici. 
 
In entrambi i casi, è necessario specificare il numero di domini di errore per il gruppo host. Se non si desidera estendere i domini di errore nel gruppo, usare un numero di domini di errore pari a 1. 

È anche possibile decidere di usare le zone di disponibilità e i domini di errore. In questo esempio viene creato un gruppo host nella zona 1, con 2 domini di errore. 


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

## <a name="create-a-host"></a>Creare un host

A questo punto è possibile creare un host dedicato nel gruppo host. Oltre a un nome per l'host, è necessario fornire lo SKU per l'host. Lo SKU dell'host acquisisce la serie di macchine virtuali supportate e la generazione dell'hardware per l'host dedicato.

Per altre informazioni sugli SKU e i prezzi degli host, vedere [Prezzi degli host dedicati di Azure](https://aka.ms/ADHPricing).

Se si imposta un numero di domini di errore per il gruppo host, verrà richiesto di specificare il dominio di errore per l'host. In questo esempio il dominio di errore per l'host viene impostato su 1.


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

Se durante la creazione del gruppo host è stata specificata una zona di disponibilità, è necessario usare la stessa area durante la creazione della macchina virtuale. Per questo esempio, poiché il gruppo host si trova nella zona 1, è necessario creare la macchina virtuale nella zona 1.  


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
> Se si crea una macchina virtuale in un host che non dispone di risorse sufficienti, la macchina virtuale verrà creata in uno stato di errore. 

## <a name="check-the-status-of-the-host"></a>Controllare lo stato dell'host

È possibile controllare lo stato di integrità dell'host e il numero di macchine virtuali che è ancora possibile distribuire nell'host usando [GetAzHost](/powershell/module/az.compute/get-azhost) con il `-InstanceView` parametro.

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

## <a name="add-an-existing-vm"></a>Aggiungere una macchina virtuale esistente 

È possibile aggiungere una macchina virtuale esistente a un host dedicato, ma la VM deve essere prima di tutto Stop\Deallocated. Prima di spostare una macchina virtuale in un host dedicato, verificare che la configurazione della macchina virtuale sia supportata:

- Le dimensioni della macchina virtuale devono essere nella stessa famiglia di dimensioni dell'host dedicato. Se, ad esempio, l'host dedicato è DSv3, è possibile che le dimensioni della macchina virtuale siano Standard_D4s_v3, ma non una Standard_A4_v2. 
- La macchina virtuale deve trovarsi nella stessa area dell'host dedicato.
- La macchina virtuale non può far parte di un gruppo di posizionamento di prossimità. Rimuovere la macchina virtuale dal gruppo di posizionamento vicino prima di trasferirla in un host dedicato. Per altre informazioni, vedere [spostare una macchina virtuale da un gruppo di posizionamento vicino](./proximity-placement-groups.md#move-an-existing-vm-out-of-a-proximity-placement-group)
- La macchina virtuale non può trovarsi in un set di disponibilità.
- Se la macchina virtuale si trova in una zona di disponibilità, deve corrispondere alla stessa zona di disponibilità del gruppo host. Le impostazioni della zona di disponibilità per la macchina virtuale e il gruppo host devono corrispondere.

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

Viene addebitato il costo per gli host dedicati anche quando non viene distribuita nessuna macchina virtuale. È necessario eliminare tutti gli host attualmente non usati per ridurre i costi.  

È possibile eliminare un host solo quando non sono più presenti macchine virtuali che lo usano. Eliminare le macchine virtuali usando [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```azurepowershell-interactive
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Dopo l'eliminazione delle macchine virtuali, è possibile eliminare l'host usando [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Una volta eliminati tutti gli host, è possibile eliminare il gruppo host usando [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```azurepowershell-interactive
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

È anche possibile eliminare l'intero gruppo di risorse in un unico comando usando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Questa operazione eliminerà tutte le risorse create nel gruppo, incluse tutte le macchine virtuali, gli host e i gruppi host.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Passaggi successivi

- È disponibile [qui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md) un modello di esempio che usa sia le zone che i domini di errore per la resilienza massima in un'area.

- È anche possibile distribuire host dedicati usando il [portale di Azure](dedicated-hosts-portal.md).
