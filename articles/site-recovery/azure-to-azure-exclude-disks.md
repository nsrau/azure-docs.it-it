---
title: Escludere i dischi della macchina virtuale di Azure dalla replica con Azure Site Recovery e Azure PowerShellExclude Azure VM disks from replication with Azure Site Recovery and Azure PowerShell
description: Informazioni su come escludere i dischi delle macchine virtuali di Azure durante Azure Site Recovery tramite Azure PowerShell.Learn how to exclude disks of Azure virtual machines during Azure Site Recovery by using Azure PowerShell.
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 02/18/2019
ms.openlocfilehash: 7355233bb7241571e3f3820aafac6952af245654
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75973683"
---
# <a name="exclude-disks-from-powershell-replication-of-azure-vms"></a>Escludere i dischi dalla replica di PowerShell delle macchine virtuali di AzureExclude disks from PowerShell replication of Azure VMs Azure VMs the PowerShell replication of Azure V

Questo articolo descrive come escludere i dischi quando si replicano le macchine virtuali di Azure.This article describes how to exclude disks when you replicate Azure VMs. È possibile escludere i dischi per ottimizzare la larghezza di banda di replica utilizzata o le risorse sul lato di destinazione utilizzate da tali dischi. Attualmente, questa funzionalità è disponibile solo tramite Azure PowerShell.Currently, this capability is available only through Azure PowerShell.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

- Assicurarsi di aver compreso l'architettura e i componenti di ripristino di [emergenza.](azure-to-azure-architecture.md)
- Esaminare i [requisiti di supporto](azure-to-azure-support-matrix.md) per tutti i componenti.
- Assicurarsi di disporre del modulo AzureRm PowerShell "Az". Per installare o aggiornare PowerShell, vedere Installare il modulo di Azure PowerShell.To install or update PowerShell, see [Install the Azure PowerShell module.](https://docs.microsoft.com/powershell/azure/install-az-ps)
- Assicurarsi di aver creato un insieme di credenziali dei servizi di ripristino e di aver protetto le macchine virtuali almeno una volta. Se queste operazioni non sono state eseguite, seguire il processo in Configurare il ripristino di emergenza per le macchine virtuali di Azure usando Azure PowerShell.If you haven't done these things, follow the process at [Set up disaster recovery for Azure virtual machines using Azure PowerShell](azure-to-azure-powershell.md).
- Per informazioni sull'aggiunta di dischi a una macchina virtuale di Azure abilitata per la replica, [vedere questo articolo.](azure-to-azure-enable-replication-added-disk.md)

## <a name="why-exclude-disks-from-replication"></a>Perché escludere i dischi dalla replica
Potrebbe essere necessario escludere i dischi dalla replica perché:You might need to exclude disks from replication because:

- La macchina virtuale ha raggiunto i limiti di [Azure Site Recovery per replicare le velocità](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix)di modifica dei dati.

- I dati che sono sfornati sul disco escluso non sono importanti o non devono essere replicati.

- Si desidera salvare le risorse di archiviazione e di rete non replicando i dati.

## <a name="how-to-exclude-disks-from-replication"></a>Come escludere i dischi dalla replica

Nel nostro esempio, viene replicata una macchina virtuale con un sistema operativo e tre dischi dati che si esibiscazione negli Stati Uniti orientali nell'area Stati Uniti occidentali 2. Il nome della macchina virtuale è *AzureDemoVM*. Escludiamo il disco 1 e manteniamo i dischi 2 e 3.

## <a name="get-details-of-the-virtual-machines-to-replicate"></a>Ottenere i dettagli delle macchine virtuali da replicareGet details of the virtual machines to replicate

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

## <a name="replicate-an-azure-virtual-machine"></a>Replicare una macchina virtuale di AzureReplicate an Azure virtual machine

Per l'esempio seguente si presuppone che si disponga già di un account di archiviazione della cache, di criteri di replica e di mapping. Se non si dispone di questi elementi, seguire il processo in Configurare il ripristino di emergenza per le macchine virtuali di Azure usando Azure PowerShell.If you don't have these things, follow the process at [Set up disaster recovery for Azure virtual machines using Azure PowerShell](azure-to-azure-powershell.md).

Replicare una macchina virtuale di Azure con *dischi gestiti.*

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

Quando l'operazione di avvio e replica ha esito positivo, i dati della macchina virtuale vengono replicati nell'area di ripristino.

È possibile passare al portale di Azure e visualizzare le macchine virtuali replicate in "elementi replicati".

Il processo di replica inizia eseguendo il seeding di una copia dei dischi di replica della macchina virtuale nell'area di ripristino. Questa fase è denominata fase di replica iniziale.

Al termine della replica iniziale, la replica passa alla fase di sincronizzazione differenziale. A questo punto la macchina virtuale è protetta. Selezionare la macchina virtuale protetta per verificare se sono esclusi dischi.

## <a name="next-steps"></a>Passaggi successivi

Informazioni [sull'esecuzione di un failover](site-recovery-test-failover-to-azure.md)di test .
