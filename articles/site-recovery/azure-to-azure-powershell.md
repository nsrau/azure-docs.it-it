---
title: Ripristino di emergenza per macchine virtuali di Azure con Azure PowerShell e Azure Site Recovery
description: Informazioni su come configurare il ripristino di emergenza per le macchine virtuali di Azure con Azure Site Recovery usando Azure PowerShell.
services: site-recovery
author: sujayt
manager: rochakm
ms.topic: article
ms.date: 3/29/2019
ms.author: sutalasi
ms.openlocfilehash: 583511194fb100add1d5fc4ea9c06a869cf652b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77212289"
---
# <a name="set-up-disaster-recovery-for-azure-virtual-machines-using-azure-powershell"></a>Configurare il ripristino di emergenza per le macchine virtuali di Azure usando Azure PowerShell

In this article, you see how to set up and test disaster recovery for Azure virtual machines using Azure PowerShell.

Si apprenderà come:

> [!div class="checklist"]
> - Creare un insieme di credenziali dei servizi di ripristino.
> - Impostare il contesto dell'insieme di credenziali per la sessione di PowerShell.
> - Preparare l'insieme di credenziali per avviare la replica delle macchine virtuali di Azure.
> - Creare i mapping di rete.
> - Creare gli account di archiviazione in cui replicare le macchine virtuali.
> - Replicare le macchine virtuali di Azure in un'area di ripristino per il ripristino di emergenza.
> - Eseguire un failover di test, convalidarlo ed eliminarlo.
> - Eseguire il failover nell'area di ripristino.

> [!NOTE]
> Non tutte le funzionalità dello scenario disponibili tramite il portale sono disponibili tramite Azure PowerShell. Di seguito sono riportate alcune delle funzionalità dello scenario che attualmente non sono supportate da Azure PowerShell:
> - La possibilità di specificare che tutti i dischi in una macchina virtuale devono essere replicati senza dover specificare esplicitamente ciascun disco della macchina virtuale.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:
- Assicurarsi di aver compreso i [componenti e l'architettura dello scenario](azure-to-azure-architecture.md).
- Esaminare i [requisiti di supporto](azure-to-azure-support-matrix.md) per tutti i componenti.
- Si dispone del `Az` modulo di Azure PowerShell.You have the Azure PowerShell module. Se è necessario installare o aggiornare Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azure/install-az-ps).

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Accedere alla sottoscrizione di Microsoft AzureSign in to your Microsoft Azure subscription

Accedere alla sottoscrizione di `Connect-AzAccount` Azure con il cmdlet.

```azurepowershell
Connect-AzAccount
```

Selezionare la sottoscrizione di Azure. Usare `Get-AzSubscription` il cmdlet per ottenere l'elenco delle sottoscrizioni di Azure a cui si ha accesso. Selezionare la sottoscrizione di `Set-AzContext` Azure con cui usare il cmdlet.

```azurepowershell
Set-AzContext -SubscriptionId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
```

## <a name="get-details-of-the-virtual-machine-to-be-replicated"></a>Ottenere i dettagli della macchina virtuale da replicareGet details of the virtual machine to be replicated

In questo articolo, una macchina virtuale nell'area Stati Uniti orientali viene replicata e ripristinata nell'area Stati Uniti occidentali 2. La macchina virtuale da replicare dispone di un disco del sistema operativo e di un singolo disco dati. Il nome della macchina virtuale utilizzata `AzureDemoVM`nell'esempio è .

```azurepowershell
# Get details of the virtual machine
$VM = Get-AzVM -ResourceGroupName "A2AdemoRG" -Name "AzureDemoVM"

Write-Output $VM
```

```Output
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

## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

Creare il gruppo di risorse in cui creare l'insieme di credenziali di Servizi di ripristino.

> [!IMPORTANT]
> * L'insieme di credenziali di Servizi di ripristino deve trovarsi in una località di Azure diversa rispetto alle macchine virtuali da proteggere.
> * Il gruppo di risorse dell'insieme di credenziali di Servizi di ripristino deve trovarsi in una località di Azure diversa rispetto alle macchine virtuali da proteggere.
> * L'insieme di credenziali di Servizi di ripristino e il gruppo di risorse a cui appartiene possono trovarsi nella stessa località di Azure.

Nell'esempio descritto in questo articolo, la macchina virtuale da proteggere si trova nell'area Stati Uniti orientali. L'area di ripristino selezionata per il ripristino di emergenza si trova nell'area Stati Uniti occidentali 2. L'insieme di credenziali dei servizi di ripristino e il gruppo di risorse dell'insieme di credenziali si trovano entrambi nell'area di ripristino, Stati Uniti occidentali 2.

```azurepowershell
#Create a resource group for the recovery services vault in the recovery Azure region
New-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"
```

```Output
ResourceGroupName : a2ademorecoveryrg
Location          : westus2
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg
```

Creare un insieme di credenziali di Servizi di ripristino. In questo esempio, un `a2aDemoRecoveryVault` insieme di credenziali di Servizi di ripristino denominato viene creato nell'area Stati Uniti occidentali 2.

```azurepowershell
#Create a new Recovery services vault in the recovery region
$vault = New-AzRecoveryServicesVault -Name "a2aDemoRecoveryVault" -ResourceGroupName "a2ademorecoveryrg" -Location "West US 2"

Write-Output $vault
```

```Output
Name              : a2aDemoRecoveryVault
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoRecoveryVault
Type              : Microsoft.RecoveryServices/vaults
Location          : westus2
ResourceGroupName : a2ademorecoveryrg
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
```

## <a name="set-the-vault-context"></a>Impostare il contesto dell'insieme di credenziali

Impostare il contesto dell'insieme di credenziali da usare nella sessione di PowerShell. Dopo aver impostato il contesto dell'insieme di credenziali, le operazioni di Azure Site Recovery nella sessione di PowerShell vengono eseguite nel contesto dell'insieme di credenziali selezionato.

```azurepowershell
#Setting the vault context.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

```Output
ResourceName         ResourceGroupName ResourceNamespace          ResourceType
------------         ----------------- -----------------          -----------
a2aDemoRecoveryVault a2ademorecoveryrg Microsoft.RecoveryServices Vaults
```

```azurepowershell
#Delete the downloaded vault settings file
Remove-Item -Path $Vaultsettingsfile.FilePath
```

Per una migrazione da Azure ad Azure, è possibile impostare il contesto dell'insieme di credenziali sull'insieme di credenziali appena creato:For an Azure-to-Azure migration, you can set the vault context to the newly created vault:

```azurepowershell
#Set the vault context for the PowerShell session.
Set-AzRecoveryServicesAsrVaultContext -Vault $vault
```

## <a name="prepare-the-vault-to-start-replicating-azure-virtual-machines"></a>Preparare l'insieme di credenziali per avviare la replica delle macchine virtuali di Azure

### <a name="create-a-site-recovery-fabric-object-to-represent-the-primary-source-region"></a>Creare un oggetto di infrastruttura di Site Recovery per rappresentare l'area primaria (di origine)

L'oggetto di infrastruttura nell'insieme di credenziali rappresenta un'area di Azure. L'oggetto di infrastruttura primario viene creato per rappresentare l'area di Azure a cui appartengono le macchine virtuali protette per l'insieme di credenziali. Nell'esempio descritto in questo articolo, la macchina virtuale da proteggere si trova nell'area Stati Uniti orientali.

- È possibile creare un solo oggetto di infrastruttura per ogni area.
- Se è già stata abilitata la replica di Site Recovery per una VM nel portale di Azure, Site Recovery crea automaticamente un oggetto di infrastruttura. Se per un'area esiste un oggetto di infrastruttura, non è possibile crearne un altro.

Prima di iniziare, tenere in mente che le operazioni di Site Recovery vengono eseguite in modo asincrono. Quando si avvia un'operazione, viene inviato un processo di Azure Site Recovery e viene restituito un oggetto di monitoraggio del processo, Utilizzare l'oggetto di rilevamento dei processi`Get-AzRecoveryServicesAsrJob`per ottenere lo stato più recente per il processo ( ) e per monitorare lo stato dell'operazione.

```azurepowershell
#Create Primary ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'East US'  -Name "A2Ademo-EastUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$PrimaryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-EastUS"
```

Se le macchine virtuali di più aree di Azure vengono protette per lo stesso insieme di credenziali, creare un oggetto di infrastruttura per ogni area di Azure di origine.

### <a name="create-a-site-recovery-fabric-object-to-represent-the-recovery-region"></a>Creare un oggetto di infrastruttura di Site Recovery per rappresentare l'area di ripristino

L'oggetto di infrastruttura di ripristino rappresenta l'area di Azure di ripristino. In caso di failover, le macchine virtuali vengono replicate e ripristinate nell'area di ripristino rappresentata dall'infrastruttura di ripristino. L'area di Azure di ripristino usata in questo esempio è Stati Uniti occidentali 2.

```azurepowershell
#Create Recovery ASR fabric
$TempASRJob = New-AzRecoveryServicesAsrFabric -Azure -Location 'West US 2'  -Name "A2Ademo-WestUS"

# Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$RecoveryFabric = Get-AzRecoveryServicesAsrFabric -Name "A2Ademo-WestUS"
```

### <a name="create-a-site-recovery-protection-container-in-the-primary-fabric"></a>Creare un contenitore di protezione di Site Recovery nell'infrastruttura primaria

Il contenitore di protezione è un contenitore che viene usato per raggruppare gli elementi replicati all'interno di un'infrastruttura.

```azurepowershell
#Create a Protection container in the primary Azure region (within the Primary fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $PrimaryFabric -Name "A2AEastUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

Write-Output $TempASRJob.State

$PrimaryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $PrimaryFabric -Name "A2AEastUSProtectionContainer"
```

### <a name="create-a-site-recovery-protection-container-in-the-recovery-fabric"></a>Creare un contenitore di protezione di Site Recovery nell'infrastruttura di ripristino

```azurepowershell
#Create a Protection container in the recovery Azure region (within the Recovery fabric)
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainer -InputObject $RecoveryFabric -Name "A2AWestUSProtectionContainer"

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"

Write-Output $TempASRJob.State

$RecoveryProtContainer = Get-AzRecoveryServicesAsrProtectionContainer -Fabric $RecoveryFabric -Name "A2AWestUSProtectionContainer"
```

### <a name="create-a-replication-policy"></a>Creare un criterio di replica

```azurepowershell
#Create replication policy
$TempASRJob = New-AzRecoveryServicesAsrPolicy -AzureToAzure -Name "A2APolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$ReplicationPolicy = Get-AzRecoveryServicesAsrPolicy -Name "A2APolicy"
```

### <a name="create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container"></a>Creare un mapping del contenitore di protezione tra i contenitori di protezione principale e di ripristino

Un mapping del contenitore di protezione esegue il mapping del contenitore di protezione principale con un contenitore di protezione di ripristino e un criterio di replica. Creare un mapping per ogni criterio di replica che verrà usato per replicare le macchine virtuali tra una coppia di contenitori di protezione.

```azurepowershell
#Create Protection container mapping between the Primary and Recovery Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2APrimaryToRecovery" -Policy $ReplicationPolicy -PrimaryProtectionContainer $PrimaryProtContainer -RecoveryProtectionContainer $RecoveryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$EusToWusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $PrimaryProtContainer -Name "A2APrimaryToRecovery"
```

### <a name="create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover"></a>Creare un mapping del contenitore di protezione per il failback (replica inversa dopo un failover)

Dopo un failover, quando si è pronti a riportare la macchina virtuale di cui è stato eseguito il failover nell'area originale di Azure, si esegue un failback. Per eseguire il failback, la macchina virtuale di cui è stato eseguito il failover viene replicata in modo inverso dall'area di failover all'area originale. Per la replica inversa, vengono invertiti i ruoli dell'area di origine e dell'area di ripristino. L'area di origine diventa la nuova area di ripristino e quella che in origine era l'area di ripristino diventa l'area primaria. Il mapping del contenitore di protezione per la replica inversa rappresenta i ruoli invertiti delle aree di origine e di ripristino.

```azurepowershell
#Create Protection container mapping (for fail back) between the Recovery and Primary Protection Containers with the Replication policy
$TempASRJob = New-AzRecoveryServicesAsrProtectionContainerMapping -Name "A2ARecoveryToPrimary" -Policy $ReplicationPolicy -PrimaryProtectionContainer $RecoveryProtContainer -RecoveryProtectionContainer $PrimaryProtContainer

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State

$WusToEusPCMapping = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $RecoveryProtContainer -Name "A2ARecoveryToPrimary"
```

## <a name="create-cache-storage-account-and-target-storage-account"></a>Creare l'account di archiviazione della cache e l'account di archiviazione di destinazioneCreate cache storage account and target storage account

Un account di archiviazione della cache è un account di archiviazione standard nella stessa area di Azure della macchina virtuale replicata. L'account di archiviazione della cache viene usato per conservare temporaneamente le modifiche della replica prima che vengano spostate nell'area di Azure di ripristino. È possibile scegliere di, ma non è necessario, specificare account di archiviazione della cache diversi per i diversi dischi di una macchina virtuale.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$EastUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestorage" -ResourceGroupName "A2AdemoRG" -Location 'East US' -SkuName Standard_LRS -Kind Storage
```

Per le macchine virtuali **che non usano dischi gestiti,** l'account di archiviazione di destinazione è l'account di archiviazione nell'area di ripristino in cui vengono replicati i dischi della macchina virtuale. L'account di archiviazione di destinazione può essere un account di archiviazione standard o un account di archiviazione Premium. Selezionare il tipo di account di archiviazione richiesto in base alla frequenza di scrittura I/O per i dischi e ai limiti di varianza supportati da Azure Site Recovery per il tipo di archiviazione.

```azurepowershell
#Create Target storage account in the recovery region. In this case a Standard Storage account
$WestUSTargetStorageAccount = New-AzStorageAccount -Name "a2atargetstorage" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -SkuName Standard_LRS -Kind Storage
```

## <a name="create-network-mappings"></a>Creare i mapping di rete

Un mapping di rete esegue il mapping delle reti virtuali nell'area primaria alle reti virtuali nell'area di ripristino. Il mapping di rete specifica la rete virtuale di Azure nell'area di ripristino in cui deve eseguire il failover di una macchina virtuale nella rete virtuale primaria. Una rete virtuale di Azure può essere mappata solo a un'unica rete virtuale di Azure in un'area di ripristino.

- Creare una rete virtuale di Azure nell'area di ripristino per eseguire il failover a:Create an Azure virtual network in the recovery region to fail over to:

   ```azurepowershell
    #Create a Recovery Network in the recovery region
    $WestUSRecoveryVnet = New-AzVirtualNetwork -Name "a2arecoveryvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.0.0.0/16"

    Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $WestUSRecoveryVnet -AddressPrefix "10.0.0.0/20" | Set-AzVirtualNetwork

    $WestUSRecoveryNetwork = $WestUSRecoveryVnet.Id
   ```

- Recuperare la rete virtuale primaria. Rete virtuale a cui è connessa la macchina virtuale:

   ```azurepowershell
    #Retrieve the virtual network that the virtual machine is connected to

    #Get first network interface card(nic) of the virtual machine
    $SplitNicArmId = $VM.NetworkProfile.NetworkInterfaces[0].Id.split("/")

    #Extract resource group name from the ResourceId of the nic
    $NICRG = $SplitNicArmId[4]

    #Extract resource name from the ResourceId of the nic
    $NICname = $SplitNicArmId[-1]

    #Get network interface details using the extracted resource group name and resource name
    $NIC = Get-AzNetworkInterface -ResourceGroupName $NICRG -Name $NICname

    #Get the subnet ID of the subnet that the nic is connected to
    $PrimarySubnet = $NIC.IpConfigurations[0].Subnet

    # Extract the resource ID of the Azure virtual network the nic is connected to from the subnet ID
    $EastUSPrimaryNetwork = (Split-Path(Split-Path($PrimarySubnet.Id))).Replace("\","/")
   ```

- Creare il mapping di rete tra la rete virtuale primaria e la rete virtuale di ripristino:Create network mapping between the primary virtual network and the recovery virtual network:

   ```azurepowershell
    #Create an ASR network mapping between the primary Azure virtual network and the recovery Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AEusToWusNWMapping" -PrimaryFabric $PrimaryFabric -PrimaryAzureNetworkId $EastUSPrimaryNetwork -RecoveryFabric $RecoveryFabric -RecoveryAzureNetworkId $WestUSRecoveryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
   ```

- Creare il mapping di rete per la direzione inversa (fail back):

    ```azurepowershell
    #Create an ASR network mapping for fail back between the recovery Azure virtual network and the primary Azure virtual network
    $TempASRJob = New-AzRecoveryServicesAsrNetworkMapping -AzureToAzure -Name "A2AWusToEusNWMapping" -PrimaryFabric $RecoveryFabric -PrimaryAzureNetworkId $WestUSRecoveryNetwork -RecoveryFabric $PrimaryFabric -RecoveryAzureNetworkId $EastUSPrimaryNetwork

    #Track Job status to check for completion
    while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
            sleep 10;
            $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
    }

    #Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
    Write-Output $TempASRJob.State
    ```

## <a name="replicate-azure-virtual-machine"></a>Replicare la macchina virtuale di Azure

Replicare la macchina virtuale di Azure con **dischi gestiti**.

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id `
         -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id `
         -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType `
         -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId
```

Replicare la macchina virtuale di Azure con **dischi non gestiti**.

```azurepowershell
#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#Disk replication configuration for the OS disk
$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $OSDiskVhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Disk replication configuration for data disk
$DataDisk1ReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -VhdUri $DataDisk1VhdURI.Uri -LogStorageAccountId $EastUSCacheStorageAccount.Id -RecoveryAzureStorageAccountId $WestUSTargetStorageAccount.Id

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.
$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.
$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId

#Track Job status to check for completion
while (($TempASRJob.State -eq "InProgress") -or ($TempASRJob.State -eq "NotStarted")){
        sleep 10;
        $TempASRJob = Get-AzRecoveryServicesAsrJob -Job $TempASRJob
}

#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TempASRJob.State
```

Quando l'operazione di avvio della replica ha esito positivo, i dati della macchina virtuale vengono replicati nell'area di ripristino.

Il processo di replica inizia eseguendo il seeding di una copia dei dischi replicati della macchina virtuale nell'area di ripristino. Questa fase viene denominata fase di replica iniziale.

La replica iniziale di AFter viene completata, la replica passa alla fase di sincronizzazione differenziale. A questo punto la macchina virtuale è protetta ed è possibile eseguire un'operazione di failover di test. Lo stato di replica dell'elemento replicato che rappresenta la macchina virtuale passa allo stato **Protetto** al termine della replica iniziale.

Per monitorare lo stato e l'integrità della replica della macchina virtuale, ottenere i dettagli dell'elemento della macchina virtuale protetto dalla replica.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

```
FriendlyName ProtectionState ReplicationHealth
------------ --------------- -----------------
AzureDemoVM  Protected       Normal
```

## <a name="do-a-test-failover-validate-and-cleanup-test-failover"></a>Eseguire un failover di test, convalidare e pulire il failover di testDo a test failover, validate, and cleanup test failover

Dopo che la replica per la macchina virtuale ha raggiunto uno stato protetto, è possibile eseguire un'operazione di failover di test nella macchina virtuale (nell'elemento protetto dalla replica della macchina virtuale).

```azurepowershell
#Create a separate network for test failover (not connected to my DR network)
$TFOVnet = New-AzVirtualNetwork -Name "a2aTFOvnet" -ResourceGroupName "a2ademorecoveryrg" -Location 'West US 2' -AddressPrefix "10.3.0.0/16"

Add-AzVirtualNetworkSubnetConfig -Name "default" -VirtualNetwork $TFOVnet -AddressPrefix "10.3.0.0/20" | Set-AzVirtualNetwork

$TFONetwork= $TFOVnet.Id
```

Eseguire un failover di test.

```azurepowershell
$ReplicationProtectedItem = Get-AzRecoveryServicesAsrReplicationProtectedItem -FriendlyName "AzureDemoVM" -ProtectionContainer $PrimaryProtContainer

$TFOJob = Start-AzRecoveryServicesAsrTestFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -AzureVMNetworkId $TFONetwork -Direction PrimaryToRecovery
```

Attendere il completamento dell'operazione di failover di test.

```azurepowershell
Get-AzRecoveryServicesAsrJob -Job $TFOJob
```

```Output
Name             : 3dcb043e-3c6d-4e0e-a42e-8d4245668547
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/3dcb043e-3c6d-4e0e-a42e-8d4245668547
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : TestFailover
DisplayName      : Test failover
ClientRequestId  : 1ef8515b-b130-4452-a44d-91aaf071931c ActivityId: 907bb2bc-ebe6-4732-8b66-77d0546eaba8
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:29:43 AM
EndTime          : 4/25/2018 4:33:06 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisites check for test failover, Create test virtual machine, Preparing the virtual machine, Start the virtual machine}
Errors           : {}
```

Al termine del processo di failover di test, è possibile connettersi al test ha esito negativo sulla macchina virtuale e convalidare il failover di test.

Al termine del test sulla macchina virtuale sottoposta al failover di test, eseguire la pulizia della copia di test avviando l'operazione di pulizia del failover di test. Questa operazione elimina la copia di test della macchina virtuale creata nell'ambito del failover di test.

```azurepowershell
$Job_TFOCleanup = Start-AzRecoveryServicesAsrTestFailoverCleanupJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $Job_TFOCleanup | Select State
```

```Output
State
-----
Succeeded
```

## <a name="fail-over-to-azure"></a>Failover in Azure

Eseguire il failover della macchina virtuale in un punto di ripristino specifico.

```azurepowershell
$RecoveryPoints = Get-AzRecoveryServicesAsrRecoveryPoint -ReplicationProtectedItem $ReplicationProtectedItem

#The list of recovery points returned may not be sorted chronologically and will need to be sorted first, in order to be able to find the oldest or the latest recovery points for the virtual machine.
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[-1].RecoveryPointTime
```

```Output
CrashConsistent 4/24/2018 11:10:25 PM
```

```azurepowershell
#Start the fail over job
$Job_Failover = Start-AzRecoveryServicesAsrUnplannedFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[-1]

do {
        $Job_Failover = Get-AzRecoveryServicesAsrJob -Job $Job_Failover;
        sleep 30;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))

$Job_Failover.State
```

```Output
Succeeded
```

Quando il processo di failover ha esito positivo, è possibile eseguire il commit dell'operazione di failover.

```azurepowershell
$CommitFailoverJOb = Start-AzRecoveryServicesAsrCommitFailoverJob -ReplicationProtectedItem $ReplicationProtectedItem

Get-AzRecoveryServicesAsrJob -Job $CommitFailoverJOb
```

```Output
Name             : 58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/a2ademorecoveryrg/providers/Microsoft.RecoveryServices/vaults/a2aDemoR
                   ecoveryVault/replicationJobs/58afc2b7-5cfe-4da9-83b2-6df358c6e4ff
Type             : Microsoft.RecoveryServices/vaults/replicationJobs
JobType          : CommitFailover
DisplayName      : Commit
ClientRequestId  : 10a95d6c-359e-4603-b7d9-b7ee3317ce94 ActivityId: 8751ada4-fc42-4238-8de6-a82618408fcf
State            : Succeeded
StateDescription : Completed
StartTime        : 4/25/2018 4:50:58 AM
EndTime          : 4/25/2018 4:51:01 AM
TargetObjectId   : ce86206c-bd78-53b4-b004-39b722c1ac3a
TargetObjectType : ProtectionEntity
TargetObjectName : azuredemovm
AllowedActions   :
Tasks            : {Prerequisite check, Commit}
Errors           : {}
```

## <a name="reprotect-and-fail-back-to-the-source-region"></a>Riproteggere ed eseguire il failback nell'area di origineReprotect and fail back to the source region

Dopo un failover, quando si è pronti per tornare all'area originale, `Update-AzRecoveryServicesAsrProtectionDirection` avviare la replica inversa per l'elemento protetto dalla replica utilizzando il cmdlet.

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage
```

```azurepowershell
#Use the recovery protection container, new cache storage account in West US and the source region VM resource group
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure
-ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId
```

Al termine della riprotezione, è possibile eseguire il failover nella direzione inversa, dagli Stati Uniti occidentali agli Stati Uniti orientali e il failback nell'area di origine.

## <a name="disable-replication"></a>Disabilitare la replica

È possibile disabilitare `Remove-AzRecoveryServicesAsrReplicationProtectedItem` la replica con il cmdlet.

```azurepowershell
Remove-AzRecoveryServicesAsrReplicationProtectedItem -ReplicationProtectedItem $ReplicatedItem
```

## <a name="next-steps"></a>Passaggi successivi

Visualizzare il riferimento PowerShell di Azure Site Recovery per informazioni su come eseguire altre attività, ad esempio la creazione di piani di ripristino e il test del failover dei piani di ripristino con PowerShell.View the [Azure Site Recovery PowerShell reference](/powershell/module/az.RecoveryServices) to learn how you can do other tasks such as creating recovery plans and testing failover of recovery plans with PowerShell.
