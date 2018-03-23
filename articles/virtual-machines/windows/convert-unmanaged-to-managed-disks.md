---
title: Convertire una macchina virtuale Windows da dischi non gestiti a dischi gestiti - Azure Managed Disks | Microsoft Docs
description: Come convertire i dischi non gestiti di una VM Windows in dischi gestiti usando PowerShell nel modello di distribuzione Resource Manager
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/03/2018
ms.author: cynthn
ms.openlocfilehash: 92168ba5605e119d42ba40ee694cebb3ad116041
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 03/08/2018
---
# <a name="convert-a-windows-virtual-machine-from-unmanaged-disks-to-managed-disks"></a>Convertire i dischi non gestiti di una VM Windows in dischi gestiti

Se si hanno macchine virtuali (VM) Windows che usano dischi non gestiti, è possibile convertire le VM all'uso di dischi gestiti mediante il servizio [Azure Managed Disks](managed-disks-overview.md). Questo processo consente di convertire sia il disco del sistema operativo che eventuali dischi dati collegati.

Questo articolo illustra come convertire le VM con Azure PowerShell. Se è necessario eseguirne l'installazione o l'aggiornamento, vedere [Installare e configurare Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="before-you-begin"></a>Prima di iniziare


* Vedere [Pianificare la migrazione a Managed Disks](on-prem-to-azure.md#plan-for-the-migration-to-managed-disks).

* Vedere le [domande frequenti sulla migrazione a Managed Disks](faq-for-disks.md#migrate-to-managed-disks).

[!INCLUDE [virtual-machines-common-convert-disks-considerations](../../../includes/virtual-machines-common-convert-disks-considerations.md)]




## <a name="convert-single-instance-vms"></a>Convertire VM a istanza singola
Questa sezione descrive come convertire i dischi delle macchine virtuali di Azure a istanza singola da non gestiti a gestiti. Se le VM sono in un set di disponibilità, vedere la sezione successiva. 

1. Deallocare la VM mediante il cmdlet [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm). L'esempio seguente dealloca la macchina virtuale denominata `myVM` nel gruppo di risorse `myResourceGroup`: 

  ```azurepowershell-interactive
  $rgName = "myResourceGroup"
  $vmName = "myVM"
  Stop-AzureRmVM -ResourceGroupName $rgName -Name $vmName -Force
  ```

2. Convertire i dischi della VM in dischi gestiti mediante il cmdlet [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk). Il processo seguente converte la macchina virtuale precedente, incluso il disco del sistema operativo e i dischi dati, e quindi avvia la macchina virtuale:

  ```azurepowershell-interactive
  ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vmName
  ```



## <a name="convert-vms-in-an-availability-set"></a>Convertire VM in un set di disponibilità

Se le macchine virtuali che si desidera convertire in dischi gestiti si trovano in un set di disponibilità, è innanzitutto necessario convertire il set di disponibilità in un set di disponibilità gestito.

1. Convertire il set di disponibilità mediante il cmdlet [Update-AzureRmAvailabilitySet](/powershell/module/azurerm.compute/update-azurermavailabilityset). L'esempio seguente aggiorna il set di disponibilità denominato `myAvailabilitySet` nel gruppo di risorse `myResourceGroup`:

  ```azurepowershell-interactive
  $rgName = 'myResourceGroup'
  $avSetName = 'myAvailabilitySet'

  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned 
  ```

  Se l'area in cui si trova il set di disponibilità ha solo 2 domini di errore gestiti, ma il numero di domini di errore non gestiti è 3, questo comando visualizza un errore di questo tipo: "Il conteggio del dominio di errore specificato 3 deve essere compreso nell'intervallo 1-2". Per correggere l'errore, impostare il dominio di errore su 2 e impostare `Sku` su `Aligned` come segue:

  ```azurepowershell-interactive
  $avSet.PlatformFaultDomainCount = 2
  Update-AzureRmAvailabilitySet -AvailabilitySet $avSet -Sku Aligned
  ```

2. Deallocare e convertire le VM nel set di disponibilità. Lo script seguente dealloca ogni macchina virtuale tramite il cmdlet [Stop-AzureRmVM](/powershell/module/azurerm.compute/stop-azurermvm), la converte con [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) e la riavvia automaticamente con un processo separato da quello di conversione:

  ```azurepowershell-interactive
  $avSet = Get-AzureRmAvailabilitySet -ResourceGroupName $rgName -Name $avSetName

  foreach($vmInfo in $avSet.VirtualMachinesReferences)
  {
     $vm = Get-AzureRmVM -ResourceGroupName $rgName | Where-Object {$_.Id -eq $vmInfo.id}
     Stop-AzureRmVM -ResourceGroupName $rgName -Name $vm.Name -Force
     ConvertTo-AzureRmVMManagedDisk -ResourceGroupName $rgName -VMName $vm.Name
  }
  ```


## <a name="troubleshooting"></a>Risoluzione dei problemi

Se si verifica un errore durante la conversione o se una VM si trova in uno stato di errore a causa di problemi in una conversione precedente, eseguire di nuovo il cmdlet `ConvertTo-AzureRmVMManagedDisk`. In genere è sufficiente riprovare per sbloccare la situazione.
Prima di eseguire la conversione, assicurarsi che lo stato di tutte le estensioni di macchina virtuale corrisponda a 'Provisioning completato'. In caso contrario, la conversione avrà esito negativo con codice di errore 409.


## <a name="next-steps"></a>Passaggi successivi

[Convertire i dischi gestiti da Standard a Premium](convert-disk-storage.md)

Eseguire una copia di sola lettura di una VM usando [snapshot](snapshot-copy-managed-disk.md).

