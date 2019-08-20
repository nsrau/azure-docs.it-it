---
title: Distribuire host dedicati di Azure usando il Azure PowerShell | Microsoft Docs
description: Distribuire le macchine virtuali in host dedicati usando Azure PowerShell.
services: virtual-machines-windows
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/01/2019
ms.author: cynthn
ms.openlocfilehash: 2482fb4ab74a3c1e032a32890c3dc2c3920b5e6b
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68725620"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-azure-powershell"></a>Anteprima: Distribuire macchine virtuali in host dedicati usando il Azure PowerShell

Questo articolo illustra come creare un [host dedicato](dedicated-hosts.md) di Azure per ospitare le macchine virtuali (VM). 

Assicurarsi di aver installato Azure PowerShell versione 2.4.2 o successiva ed è stato effettuato l'accesso a un account Azure in con `Connect-AzAccount`. Per installare la versione 2.4.2, aprire un prompt di PowerShell e digitare:

```powershell
Install-Module -Name Az.Compute -Repository PSGallery -RequiredVersion 2.4.2-preview -AllowPrelease
```

Per abilitare la funzionalità di anteprima del modulo in PowerShell, è necessaria almeno la versione 1.6.0 del modulo PowerShellGet. Le versioni più recenti di PowerShell core sono state integrate automaticamente, ma per le versioni precedenti di PowerShell è possibile eseguire il comando seguente per eseguire l'aggiornamento alla versione più recente:

```powershell
Install-Module -Name PowerShellGet -Repository PSGallery -Force
```


> [!IMPORTANT]
> Gli host dedicati di Azure sono attualmente disponibili in anteprima pubblica.
> Questa versione di anteprima viene messa a disposizione senza contratto di servizio e non è consigliata per i carichi di lavoro di produzione. Alcune funzionalità potrebbero non essere supportate o potrebbero presentare funzionalità limitate. Per altre informazioni, vedere [Condizioni supplementari per l'utilizzo delle anteprime di Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>
> **Limitazioni di anteprima note**
> - I set di scalabilità di macchine virtuali non sono attualmente supportati negli host dedicati.
> - La versione iniziale di anteprima supporta la serie di macchine virtuali seguente: DSv3 e ESv3. 



## <a name="create-a-host-group"></a>Creazione di un gruppo host

Un **gruppo host** è una risorsa che rappresenta una raccolta di host dedicati. È possibile creare un gruppo host in un'area e una zona di disponibilità e aggiungervi host. Quando si pianifica la disponibilità elevata, sono disponibili opzioni aggiuntive. Con gli host dedicati è possibile usare una o entrambe le opzioni seguenti: 
- Span tra più zone di disponibilità. In questo caso, è necessario disporre di un gruppo host in ognuna delle zone che si desidera utilizzare.
- Si estende su più domini di errore di cui è stato eseguito il mapping ai rack fisici. 
 
In entrambi i casi, è necessario specificare il numero di domini di errore per il gruppo host. Se non si desidera estendere i domini di errore nel gruppo, utilizzare un numero di domini di errore pari a 1. 

È anche possibile decidere di usare le zone di disponibilità e i domini di errore. In questo esempio viene creato un gruppo host nella zona 1, con 2 domini di errore. 


```powershell
$rgName = "myDHResourceGroup"
$location = "East US"

New-AzResourceGroup -Location $location -Name $rgName
$hostGroup = New-AzHostGroup `
   -Location $location `
   -Name myHostGroup `
   -PlatformFaultDomain 2 `
   -ResourceGroupName $rgName `
   -Zone 1
```

## <a name="create-a-host"></a>Creazione di un host

A questo punto è possibile creare un host dedicato nel gruppo host. Oltre a un nome per l'host, è necessario fornire lo SKU per l'host. Lo SKU host acquisisce la serie di macchine virtuali supportate e la generazione dell'hardware per l'host dedicato.  Durante l'anteprima, sono supportati i valori SKU host seguenti: DSv3_Type1 e ESv3_Type1.


Per altre informazioni sugli SKU e i prezzi degli host, vedere [prezzi di host dedicati di Azure](https://aka.ms/ADHPricing).

Se si imposta un numero di domini di errore per il gruppo host, verrà richiesto di specificare il dominio di errore per l'host. In questo esempio il dominio di errore per l'host viene impostato su 1.


```powershell
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


```powershell
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

## <a name="check-the-status-of-the-host"></a>Verificare lo stato dell'host

È possibile controllare lo stato di integrità dell'host e il numero di macchine virtuali che è ancora possibile distribuire nell'host usando [GetAzHost](/powershell/module/az.compute/get-azhost) con il parametro `-InstanceView`.

```
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

## <a name="clean-up"></a>Eseguire la pulizia

Viene addebitato il costo per gli host dedicati anche quando nessuna macchina virtuale viene distribuita. È necessario eliminare tutti gli host attualmente non utilizzati per ridurre i costi.  

È possibile eliminare un host solo quando non sono più presenti macchine virtuali che lo utilizzano. Eliminare le macchine virtuali usando [Remove-AzVM](/powershell/module/az.compute/remove-azvm).

```powershell
Remove-AzVM -ResourceGroupName $rgName -Name myVM
```

Dopo l'eliminazione delle macchine virtuali, è possibile eliminare l'host usando [Remove-AzHost](/powershell/module/az.compute/remove-azhost).

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

Una volta eliminati tutti gli host, è possibile eliminare il gruppo host usando [Remove-AzHostGroup](/powershell/module/az.compute/remove-azhostgroup). 

```powershell
Remove-AzHost -ResourceGroupName $rgName -Name myHost
```

È anche possibile eliminare l'intero gruppo di risorse in un unico comando usando [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup). Questa operazione eliminerà tutte le risorse create nel gruppo, incluse tutte le macchine virtuali, gli host e i gruppi host.
 
```azurepowershell-interactive
Remove-AzResourceGroup -Name $rgName
```


## <a name="next-steps"></a>Passaggi successivi

- È [disponibile un](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)modello di esempio, che usa sia le zone che i domini di errore per la resilienza massima in un'area.

- È anche possibile distribuire host dedicati usando il [portale di Azure](dedicated-hosts-portal.md).
