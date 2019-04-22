---
title: Azure Site Recovery - esclusione dei dischi durante la replica di macchine virtuali di Azure usando Azure PowerShell | Microsoft Docs
description: Informazioni su come escludere i dischi di macchine virtuali di Azure durante il ripristino del sito di Azure usando Azure PowerShell.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: 54a32d7f7aa4bcab73f5828da3e7eba9d25276be
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678276"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Escludere dischi dalla replica PowerShell delle macchine virtuali di Azure

Questo articolo descrive come escludere dischi quando si esegue la replica di macchine virtuali di Azure. È possibile escludere i dischi per ottimizzare la larghezza di banda di replica usata o le risorse lato destinazione che usano questi dischi. Questa funzionalità è attualmente disponibile solo tramite Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

- Assicurarsi di aver compreso le [componenti e architettura di ripristino di emergenza](azure-to-azure-architecture.md).
- Verificare i [requisiti di supporto](azure-to-azure-support-matrix.md) per tutti i componenti.
- Assicurarsi di aver AzureRm di PowerShell "Az" modulo. Per installare o aggiornare PowerShell, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Assicurarsi di aver creato un insieme di credenziali di servizi di ripristino e macchine virtuali protette almeno una volta. Se già stato queste operazioni, seguire la procedura al [configurare il ripristino di emergenza per macchine virtuali di Azure usando Azure PowerShell](azure-to-azure-powershell.md).

## <a name="why-exclude-disks-from-replication"></a>Perché escludere dischi dalla replica
Potrebbe essere necessario escludere dischi dalla replica, perché:

- La macchina virtuale ha raggiunto [frequenza di modifica ai limiti di Azure Site Recovery per replicare i dati](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- I dati di varianza nel disco escluso non sono importanti o non devono essere replicati.

- Si desidera salvare le risorse di archiviazione e di rete non eseguendo la replica dei dati.

## <a name="how-to-exclude-disks-from-replication"></a>Come escludere dischi dalla replica

In questo esempio è replicare una macchina virtuale che dispone di un sistema operativo e tre dischi dati che si trovano l'area Stati Uniti orientali nell'area Stati Uniti occidentali 2. Il nome della macchina virtuale viene *AzureDemoVM*. Abbiamo escludere il disco 1 e mantenere dischi 2 e 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Ottenere i dettagli delle macchine virtuali da replicare

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM     
```

```
ResourceGroupName  : A2AdemoRG
Id                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/A2AdemoRG/providers/Microsoft.Compute/virtualMachines/AzureDemoVM
VmId               : 1b864902-c7ea-499a-ad0f-65da2930b81b
Name               : AzureDemoVM
Type               : Microsoft.Compute/virtualMachines
Location           : eastus
Tags               : {}
DiagnosticsProfile : {BootDiagnostics}
HardwareProfile    : {VmSize}
NetworkProfile     : {NetworkInterfaces}
OSProfile          : {ComputerName, AdminUsername, WindowsConfiguration, Secrets}
ProvisioningState  : Succeeded
StorageProfile     : {ImageReference, OsDisk, DataDisks}
```

Ottenere informazioni dettagliate sui dischi della macchina virtuale. Queste informazioni verranno usate in un secondo momento quando si avvia la replica della macchina virtuale.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replicare una macchina virtuale di Azure

Nell'esempio seguente, si presuppone di avere già un account di archiviazione della cache, criteri di replica e i mapping. Se non si dispone di queste operazioni, seguire la procedura al [configurare il ripristino di emergenza per macchine virtuali di Azure usando Azure PowerShell](azure-to-azure-powershell.md).

Replicare una macchina virtuale di Azure con *dischi gestiti*.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration).

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded, so we will provide it during the time of replication. 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating a replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Quando l'operazione di replica iniziale ha esito positivo, i dati della macchina virtuale vengono replicati nell'area di ripristino.

È possibile accedere al portale di Azure e visualizzare le macchine virtuali replicate in "elementi replicati".

Il processo di replica inizia con il seeding di una copia di dischi di replica della macchina virtuale nell'area di ripristino. Questa fase viene chiamata la fase di replica iniziale.

Al termine della replica iniziale, la replica passa alla fase di sincronizzazione differenziale. A questo punto la macchina virtuale è protetta. Selezionare la macchina virtuale protetta per vedere se i dischi vengono esclusi.

## <a name="next-steps"></a>Passaggi successivi

Scopri [che esegue un failover di test](site-recovery-test-failover-to-azure.md).
