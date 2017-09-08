---
title: Convertire una macchina virtuale Windows da dischi non gestiti a dischi gestiti - Azure Managed Disks | Microsoft Docs
description: Come convertire i dischi non gestiti di una VM Windows in dischi gestiti usando PowerShell nel modello di distribuzione Resource Manager
services: virtual-machines-windows
documentationcenter: 
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
ms.date: 06/23/2017
ms.author: cynthn
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 54afcf1e37f696979bfe270a473c72aedf20dc43
ms.contentlocale: it-it
ms.lasthandoff: 08/21/2017

---

# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Convertire i dischi non gestiti di una VM Windows in dischi gestiti

Se si hanno macchine virtuali (VM) Windows che usano dischi non gestiti, è possibile convertire le VM all'uso di dischi gestiti mediante il servizio [Azure Managed Disks](managed-disks-overview.md). Questo processo consente di convertire sia il disco del sistema operativo che eventuali dischi dati collegati.

Questo articolo illustra come convertire le VM con Azure PowerShell. Se è necessario eseguirne l'installazione o l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps.md).

## <a name="before-you-begin"></a>Prima di iniziare


* Vedere [Pianificare la migrazione a Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Convertire VM a istanza singola
Questa sezione descrive come convertire i dischi delle macchine virtuali di Azure a istanza singola da non gestiti a gestiti. Se le VM sono in un set di disponibilità, vedere la sezione successiva. 

1. Deallocare la VM mediante il cmdlet [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). L'esempio seguente dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`: 

  ```powershell
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Convertire i dischi della VM in dischi gestiti mediante il cmdlet [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk). Il processo seguente converte la macchina virtuale precedente, incluso il disco del sistema operativo ed eventuali dischi dati:

  ```powershell
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```

3. Avviare la VM dopo la conversione ai dischi gestiti mediante [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm). L'esempio seguente riavvia la VM precedente:

  ```powershell
  Start-AzureRmVM -ResourceGroupName $rgName -Name $vmName
  ```


## <a name="convert-vms-in-an-availability-set"></a>Convertire VM in un set di disponibilità

Se le macchine virtuali che si desidera convertire in dischi gestiti si trovano in un set di disponibilità, è innanzitutto necessario convertire il set di disponibilità in un set di disponibilità gestito.

1. Convertire il set di disponibilità mediante il cmdlet [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset). L'esempio seguente aggiorna il set di disponibilità denominato `myAvailabilitySet` nel gruppo di risorse `myResourceGroup`:

  ```powershell
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Se l'area in cui si trova il set di disponibilità ha solo 2 domini di errore gestiti, ma il numero di domini di errore non gestiti è 3, questo comando visualizza un errore di questo tipo: "Il conteggio del dominio di errore specificato 3 deve essere compreso nell'intervallo 1-2". Per correggere l'errore, impostare il dominio di errore su 2 e impostare `Sku` su `Aligned` come segue:

  ```powershell
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Deallocare e convertire le VM nel set di disponibilità. Lo script seguente dealloca ogni VM mediante il cmdlet [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm), la converte mediante [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) e la riavvia mediante [Start-AzureRmVM](/powershell/module/azurerm.compute/start-azurermvm):

  ```powershell
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
     Start-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name
  }
  ```


## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore durante la conversione o se una VM si trova in uno stato di errore a causa di problemi in una conversione precedente, eseguire di nuovo il cmdlet `ConvertTo-AzureRmVMManagedDisk`. In genere è sufficiente riprovare per sbloccare la situazione.


## <a name="next-steps"></a>Passaggi successivi

[Convertire i dischi gestiti da Standard a Premium](convert-disk-storage.md)

Eseguire una copia di sola lettura di una VM usando [snapshot](snapshot-copy-managed-disk.md).


