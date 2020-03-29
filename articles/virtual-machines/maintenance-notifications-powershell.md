---
title: Ricevere notifiche di manutenzione per le macchine virtuali di Azure tramite PowerShellGet maintenance notifications for Azure VMs using PowerShell
description: Visualizzare le notifiche di manutenzione per le macchine virtuali in esecuzione in Azure e avviare la manutenzione self-service tramite PowerShell.View maintenance notifications for virtual machines running in Azure and start self-service maintenance using PowerShell.
author: shants123
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/19/2019
ms.author: shants
ms.openlocfilehash: b23c210d7c8a9f1d42e6e1b46e0f7f81bda857b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77916083"
---
# <a name="handling-planned-maintenance-using-powershell"></a>Gestione della manutenzione pianificata tramite PowerShellHandling planned maintenance using PowerShell

**Questo articolo si applica alle macchine virtuali che eseguono sia Linux che Windows.This article applies to virtual machines running both Linux and Windows.**

È possibile usare Azure PowerShell per vedere quando le macchine virtuali sono pianificate per [la manutenzione.](maintenance-notifications.md) Le informazioni sulla manutenzione pianificata sono disponibili con il cmdlet [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm), usando il parametro `-status`.
  
Le informazioni vengono restituite solo se è presente una manutenzione pianificata. Se non è presente alcuna manutenzione pianificata per la macchina virtuale, il cmdlet non restituisce informazioni. 


```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Name myVM -Status
```

MaintenanceRedeployStatus restituisce le proprietà seguenti: 

| valore | Descrizione   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | Indica se in questo momento è possibile avviare la manutenzione per la macchina virtuale |
| PreMaintenanceWindowStartTime         | Inizio della finestra di manutenzione self-service, che segnala la possibilità di avviare la manutenzione della VM |
| PreMaintenanceWindowEndTime           | Termine della finestra di manutenzione self-service, che segnala la possibilità di avviare la manutenzione della VM |
| MaintenanceWindowStartTime            | Inizio della manutenzione pianificata in cui Azure avvia la manutenzione della VM |
| MaintenanceWindowEndTime              | Fine dell'intervallo pianificato di manutenzione in cui Azure avvia la manutenzione della macchina virtuale |
| LastOperationResultCode               | Risultato dell'ultimo tentativo di avviare la manutenzione della macchina virtuale |



È anche possibile ottenere lo stato di manutenzione per tutte le macchine virtuali in un gruppo di risorse tramite [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm), senza specificare una macchina virtuale.
 
```powershell
Get-AzVM -ResourceGroupName myResourceGroup -Status
```

L'esempio di PowerShell seguente accetta l'ID sottoscrizione e restituisce un elenco di macchine virtuali pianificate per la manutenzione.

```powershell

function MaintenanceIterator
{
    Select-AzSubscription -SubscriptionId $args[0]

    $rgList= Get-AzResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]        
    $vmList = Get-AzVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>Avviare la manutenzione della macchina virtuale usando PowerShell

Usando le informazioni derivate dalla funzione nella sezione precedente, il codice seguente avvia la manutenzione in una macchina virtuale se **IsCustomerInitiatedMaintenanceAllowed** è impostato su true.

```powershell
Restart-AzVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```

## <a name="classic-deployments"></a>Distribuzioni classiche

[!INCLUDE [classic-vm-deprecation](../../includes/classic-vm-deprecation.md)]

Se si dispone ancora di macchine virtuali legacy distribuite tramite il modello di distribuzione classico, è possibile usare PowerShell per eseguire query per le macchine virtuali e avviare la manutenzione.

Per ottenere lo stato di manutenzione di una macchina virtuale, digitare:

```
Get-AzureVM -ServiceName <Service name> -Name <VM name>
```

Per avviare la manutenzione di una VM classica, digitare:

```
Restart-AzureVM -InitiateMaintenance -ServiceName <service name> -Name <VM name>
```

## <a name="next-steps"></a>Passaggi successivi

È anche possibile gestire la manutenzione pianificata usando [l'interfaccia della riga di comando](maintenance-notifications-cli.md) o il portale di Azure.You can also handle planned maintenance using the Azure CLI or [portal](maintenance-notifications-portal.md).
