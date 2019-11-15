---
title: Escludere i dischi delle macchine virtuali di Azure dalla replica con Azure Site Recovery e Azure PowerShell
description: Informazioni su come escludere dischi di macchine virtuali di Azure durante Azure Site Recovery usando Azure PowerShell.
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: beb4ee6fcf6413634d7d728e3f070294e8ce3787
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084981"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Escludere dischi dalla replica di PowerShell di macchine virtuali di Azure

Questo articolo descrive come escludere dischi quando si esegue la replica di VM di Azure. È possibile escludere i dischi per ottimizzare la larghezza di banda di replica utilizzata o le risorse sul lato di destinazione utilizzate da tali dischi. Questa funzionalità è attualmente disponibile solo tramite Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>prerequisiti

Prima di iniziare:

- Assicurarsi di aver compreso i [componenti e l'architettura di ripristino di emergenza](azure-to-azure-architecture.md).
- Esaminare i [requisiti di supporto](azure-to-azure-support-matrix.md) per tutti i componenti.
- Assicurarsi di avere il modulo AzureRm di PowerShell "AZ". Per installare o aggiornare PowerShell, vedere [installare il modulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).
- Assicurarsi di aver creato almeno una volta un insieme di credenziali di servizi di ripristino e le macchine virtuali protette. Se non sono state eseguite queste operazioni, seguire la procedura in [configurare il ripristino di emergenza per macchine virtuali di Azure con Azure PowerShell](azure-to-azure-powershell.md).
- Per informazioni sull'aggiunta di dischi a una macchina virtuale di Azure abilitata per la replica, [vedere questo articolo](azure-to-azure-enable-replication-added-disk.md).

## <a name="why-exclude-disks-from-replication"></a>Perché escludere i dischi dalla replica
Potrebbe essere necessario escludere i dischi dalla replica perché:

- La macchina virtuale ha raggiunto [Azure Site Recovery limiti per replicare la frequenza di modifica dei dati](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix).

- I dati che vengono rilevati nel disco escluso non sono importanti o non devono essere replicati.

- Si vuole salvare le risorse di archiviazione e di rete non eseguendo la replica dei dati.

## <a name="how-to-exclude-disks-from-replication"></a>Come escludere dischi dalla replica

In questo esempio viene replicata una macchina virtuale con un sistema operativo e tre dischi dati che si trova nell'area Stati Uniti orientali nell'area Stati Uniti occidentali 2. Il nome della macchina virtuale è *AzureDemoVM*. Si esclude il disco 1 e si mantengono i dischi 2 e 3.

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

Ottenere i dettagli sui dischi della macchina virtuale. Queste informazioni verranno usate in un secondo momento quando si avvia la replica della macchina virtuale.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-an-azure-virtual-machine"></a>Replicare una macchina virtuale di Azure

Per l'esempio seguente si presuppone che si disponga già di un account di archiviazione della cache, dei criteri di replica e dei mapping. Se non si hanno queste informazioni, seguire la procedura in [configurare il ripristino di emergenza per macchine virtuali di Azure con Azure PowerShell](azure-to-azure-powershell.md).

Replicare una macchina virtuale di Azure con *Managed disks*.

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

Quando l'operazione di avvio della replica ha esito positivo, i dati della VM vengono replicati nell'area di ripristino.

È possibile passare alla portale di Azure e visualizzare le VM replicate in "elementi replicati".

Il processo di replica inizia con il seeding di una copia dei dischi di replica della macchina virtuale nell'area di ripristino. Questa fase è detta fase di replica iniziale.

Al termine della replica iniziale, la replica passa alla fase di sincronizzazione differenziale. A questo punto la macchina virtuale è protetta. Selezionare la macchina virtuale protetta per verificare se sono stati esclusi dischi.

## <a name="next-steps"></a>Passaggi successivi

Informazioni sull' [esecuzione di un failover di test](site-recovery-test-failover-to-azure.md).
