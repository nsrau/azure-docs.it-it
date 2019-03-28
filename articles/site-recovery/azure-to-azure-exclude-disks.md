---
title: Azure Site Recovery - Escludere dischi durante la replica di macchine virtuali di Azure con Azure PowerShell | Microsoft Docs
description: Informazioni su come escludere dischi per le macchine virtuali di Azure con Azure Site Recovery usando Azure PowerShell.
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2019
ms.author: asgang
ms.openlocfilehash: b378f77874b1ebef243836c101fa71a53f4775d1
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58517744"
---
# <a name="exclude-disks-from-replication-of-azure-vms-to-azure-using-azure-powershell"></a>Escludere dischi dalla replica di macchine virtuali di Azure in Azure con Azure PowerShell

Questo articolo descrive come escludere dischi durante la replica di macchine virtuali di Azure, in modo da ottimizzare la larghezza di banda di replica usata o le risorse lato destinazione usate dai dischi. Attualmente questa funzionalità è esposta solo tramite Azure PowerShell.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

- Assicurarsi di aver compreso i [componenti e l'architettura dello scenario](azure-to-azure-architecture.md).
- Verificare i [requisiti di supporto](azure-to-azure-support-matrix.md) per tutti i componenti.
- Assicurarsi di disporre della versione 5.7.0 o versioni successive del modulo AzureRM PowerShell. Se è necessario installare o aggiornare Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).
- L'insieme di credenziali dei servizi di ripristino deve essere già stato creato e la protezione delle macchine virtuali deve essere stata eseguita almeno una volta. In caso contrario, procedere usando la documentazione indicata [qui](azure-to-azure-powershell.md). 

## <a name="why-exclude-disks-from-replication"></a>Perché escludere dischi dalla replica
Spesso è necessario escludere dischi dalla replica per i motivi riportati di seguito:

- La macchina virtuale ha raggiunto i [limiti di Azure Site Recovery per la replica delle frequenze di modifica dei dati](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)

- I dati di varianza nel disco escluso non sono importanti o non devono essere necessariamente replicati.

- Si preferisce salvare le risorse di archiviazione e di rete non eseguendo la replica di questa varianza.


## <a name="how-to-exclude-disks-from-replication"></a>Come escludere dischi dalla replica

Nell'esempio descritto in questo articolo, una macchina virtuale con 1 sistema operativo e 3 dischi dati nell'area Stati Uniti orientali verrà replicata nell'area Stati Uniti occidentali 2. Il nome della macchina virtuale usata nell'esempio è AzureDemoVM. Si escluderà il disco 1 e si manterranno i dischi 2 e 3

## <a name="get-details-of-the-virtual-machines-to-be-replicated"></a>Ottenere i dettagli delle macchine virtuali da replicare

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzureRmVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

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


Ottenere i dettagli del disco per i dischi della macchina virtuale. I dettagli del disco verranno usati in seguito durante l'avvio della replica della macchina virtuale.

```azurepowershell
$OSDiskVhdURI = $VM.StorageProfile.OsDisk.Vhd
$DataDisk1VhdURI = $VM.StorageProfile.DataDisks[0].Vhd
```

## <a name="replicate-azure-virtual-machine"></a>Replicare la macchina virtuale di Azure

Nell'esempio seguente si presuppone che siano già stati creati un account di archiviazione della cache, criteri di replica e mapping. In caso contrario, procedere usando la documentazione indicata [qui](azure-to-azure-powershell.md). 


Replicare la macchina virtuale di Azure con **dischi gestiti**.

```azurepowershell

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzureRmResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId =  $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data Disk 1 i.e StorageProfile.DataDisks[0] is excluded so we will provide it during the time of replication 

# Data disk 2
$datadiskId2  = $vm.StorageProfile.DataDisks[1].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[1]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[1]. StorageAccountType

$DataDisk2ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId2 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

# Data Disk 3

$datadiskId3  = $vm.StorageProfile.DataDisks[2].ManagedDisk.id
$RecoveryReplicaDiskAccountType =  $vm.StorageProfile.DataDisks[2]. StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[2]. StorageAccountType

$DataDisk3ReplicationConfig  = New-AzureRmRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId3 -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk2ReplicationConfig, $DataDisk3ReplicationConfig


#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-ASRReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Quando l'operazione di avvio della replica ha esito positivo, i dati della macchina virtuale vengono replicati nell'area di ripristino.

Nella sezione Elementi replicati del portale di Azure è possibile visualizzare le macchine virtuali sottoposte a replica.
Il processo di replica inizia eseguendo il seeding di una copia dei dischi replicati della macchina virtuale nell'area di ripristino. Questa fase viene denominata fase di replica iniziale.

Al termine della replica iniziale, la replica passa alla fase di sincronizzazione differenziale. A questo punto la macchina virtuale è protetta. Fare clic sulla macchina virtuale protetta e selezionare Dischi per controllare se il disco è escluso o meno.

## <a name="next-steps"></a>Passaggi successivi

[Altre informazioni](site-recovery-test-failover-to-azure.md) sull'esecuzione di un failover di test.
