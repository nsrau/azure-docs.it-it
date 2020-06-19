---
title: Replicare le macchine virtuali di Azure in esecuzione nei gruppi di posizionamento di prossimità
description: Informazioni su come replicare le macchine virtuali di Azure in esecuzione nei gruppi di posizionamento di prossimità usando Azure Site Recovery.
author: Sharmistha-Rai
manager: gaggupta
ms.topic: how-to
ms.date: 05/25/2020
ms.openlocfilehash: 204ac3be46ac7ba0e1ea96e50379ca417b1299ce
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/26/2020
ms.locfileid: "83847634"
---
# <a name="replicate-azure-virtual-machines-running-in-proximity-placement-groups-to-another-region"></a>Replicare le macchine virtuali di Azure in esecuzione nei gruppi di posizionamento di prossimità in un'altra area

Questo articolo descrive come eseguire la replica, il failover e il failback di macchine virtuali in esecuzione in un gruppo di posizionamento di prossimità in un'area secondaria.

[Gruppi di posizionamento di prossimità](https://docs.microsoft.com/azure/virtual-machines/windows/proximity-placement-groups-portal) è una funzionalità di raggruppamento logico di macchine virtuali di Azure che è possibile usare per ridurre la latenza di rete tra le macchine virtuali associata alle applicazioni. Quando le macchine virtuali vengono distribuite nello stesso gruppo di posizionamento di prossimità, sono posizionate fisicamente il più vicino possibile l'una all'altra. I gruppi di posizionamento di prossimità sono particolarmente utili per soddisfare i requisiti dei carichi di lavoro sensibili alla latenza.

## <a name="disaster-recovery-with-proximity-placement-groups"></a>Ripristino di emergenza con i gruppi di posizionamento di prossimità

In uno scenario tipico, è possibile che le macchine virtuali siano in esecuzione in un gruppo di posizionamento di prossimità per evitare la latenza di rete tra i vari livelli dell'applicazione. Anche se questo fornisce all'applicazione una latenza di rete ottimale, si vogliono proteggere le applicazioni usando Site Recovery per qualsiasi errore a livello di area. Site Recovery replica i dati da un'area di Azure a un'altra e inserisce le macchine nell'area di ripristino di emergenza in caso di failover.

## <a name="considerations"></a>Considerazioni

- L'approccio migliore consiste nell'eseguire il failover/failback delle macchine virtuali in un gruppo di posizionamento di prossimità. Tuttavia, se la macchina virtuale non può essere inserita all'interno del posizionamento di prossimità durante il failover/failback, il failover/failback continuerà a verificarsi e le macchine virtuali verranno create all'esterno di un gruppo di posizionamento di prossimità.
-  Se un set di disponibilità viene aggiunto a un gruppo di posizionamento di prossimità e durante il failover/failback le macchine virtuali nel set di disponibilità hanno un vincolo di allocazione, le macchine virtuali verranno create al di fuori del set di disponibilità e del gruppo di posizionamento di prossimità.
-  Site Recovery per i gruppi di posizionamento di prossimità non è supportato per i dischi non gestiti.

## <a name="prerequisites"></a>Prerequisiti

1. Verificare che sia installato il modulo Az di Azure PowerShell. Se è necessario installare o aggiornare Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

## <a name="set-up-site-recovery-for-virtual-machines-in-proximity-placement-group"></a>Configurare Site Recovery per le macchine virtuali nel gruppo di posizionamento di prossimità

### <a name="azure-to-azure"></a>Da Azure ad Azure

1. [Accedere](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#sign-in-to-your-microsoft-azure-subscription) al proprio account e impostare la sottoscrizione.
2. Ottenere i dettagli della macchina virtuale che si intende replicare come indicato [qui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#get-details-of-the-virtual-machine-to-be-replicated).
3. [Creare](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-recovery-services-vault) un insieme di credenziali di Servizi di ripristino e [impostare](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#set-the-vault-context) il contesto dell'insieme di credenziali.
4. Preparare l'insieme di credenziali per avviare la replica della macchina virtuale. Ciò comporta la creazione di un [oggetto Service Fabric](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-fabric-object-to-represent-the-primary-source-region) sia per l'area primaria che per l'area di ripristino.
5. [Creare](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-site-recovery-protection-container-in-the-primary-fabric) un contenitore di protezione di Site Recovery sia per l'infrastruttura primaria che per l'infrastruttura di ripristino.
6. [Creare](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-replication-policy) i criteri di replica.
7. Creare un mapping del contenitore di protezione tra il contenitore di protezione primario e quello di ripristino usando [questi](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-between-the-primary-and-recovery-protection-container) passaggi e un mapping del contenitore di protezione per il failback come indicato [qui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-a-protection-container-mapping-for-failback-reverse-replication-after-a-failover).
8. Creare un account di archiviazione della cache seguendo [questi](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-cache-storage-account-and-target-storage-account) passaggi.
9. Creare i mapping di rete necessari come indicato [qui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#create-network-mappings).
10. Per replicare una macchina virtuale di Azure con dischi gestiti, usare il cmdlet di PowerShell seguente: 

```azurepowershell
#Get the resource group that the virtual machine must be created in when failed over.
$RecoveryRG = Get-AzResourceGroup -Name "a2ademorecoveryrg" -Location "West US 2"

#Specify replication properties for each disk of the VM that is to be replicated (create disk replication configuration)

#OsDisk
$OSdiskId = $vm.StorageProfile.OsDisk.ManagedDisk.Id
$RecoveryOSDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.OsDisk.ManagedDisk.StorageAccountType

$OSDiskReplicationConfig = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $EastUSCacheStorageAccount.Id ` -DiskId $OSdiskId -RecoveryResourceGroupId  $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType  $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryOSDiskAccountType -RecoveryProximityPlacementGroupId $recPpg.Id

# Data disk
$datadiskId1 = $vm.StorageProfile.DataDisks[0].ManagedDisk.Id
$RecoveryReplicaDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType
$RecoveryTargetDiskAccountType = $vm.StorageProfile.DataDisks[0].ManagedDisk.StorageAccountType

$DataDisk1ReplicationConfig  = New-AzRecoveryServicesAsrAzureToAzureDiskReplicationConfig -ManagedDisk -LogStorageAccountId $CacheStorageAccount.Id ` -DiskId $datadiskId1 -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryReplicaDiskAccountType $RecoveryReplicaDiskAccountType ` -RecoveryTargetDiskAccountType $RecoveryTargetDiskAccountType

#Create a list of disk replication configuration objects for the disks of the virtual machine that are to be replicated.

$diskconfigs = @()
$diskconfigs += $OSDiskReplicationConfig, $DataDisk1ReplicationConfig

#Start replication by creating replication protected item. Using a GUID for the name of the replication protected item to ensure uniqueness of name.

$TempASRJob = New-AzRecoveryServicesAsrReplicationProtectedItem -AzureToAzure -AzureVmId $VM.Id -Name (New-Guid).Guid -ProtectionContainerMapping $EusToWusPCMapping -AzureToAzureDiskReplicationConfiguration $diskconfigs -RecoveryResourceGroupId $RecoveryRG.ResourceId -RecoveryProximityPlacementGroupId $recPpg.Id
```
Quando l'operazione di avvio della replica ha esito positivo, i dati della macchina virtuale vengono replicati nell'area di ripristino.

Il processo di replica inizia eseguendo il seeding di una copia dei dischi replicati della macchina virtuale nell'area di ripristino. Questa fase viene denominata fase di replica iniziale.

Al termine della replica iniziale, la replica passa alla fase di sincronizzazione differenziale. A questo punto la macchina virtuale è protetta ed è possibile eseguire un'operazione di failover di test. Al termine della replica iniziale, lo stato della replica dell'elemento replicato che rappresenta la macchina virtuale passa allo stato Protetto.

Per monitorare lo stato e l'integrità della replica della macchina virtuale, ottenere i dettagli dell'elemento della macchina virtuale protetto dalla replica. 

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $PrimaryProtContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```

11. Per eseguire un failover di test, convalidarlo ed eliminarlo, seguire [questi](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#do-a-test-failover-validate-and-cleanup-test-failover) passaggi.
12. Per eseguire il failover, seguire i passaggi indicati [qui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#fail-over-to-azure).
13. Per eseguire la riprotezione e il failback nell'area di origine, usare il cmdlet di PowerShell seguente:

```azurepowershell
#Create Cache storage account for replication logs in the primary region
$WestUSCacheStorageAccount = New-AzStorageAccount -Name "a2acachestoragewestus" -ResourceGroupName "A2AdemoRG" -Location 'West US' -SkuName Standard_LRS -Kind Storage


#Use the recovery protection container, new cache storage account in West US and the source region VM resource group 
Update-AzRecoveryServicesAsrProtectionDirection -ReplicationProtectedItem $ReplicationProtectedItem -AzureToAzure -ProtectionContainerMapping $WusToEusPCMapping -LogStorageAccountId $WestUSCacheStorageAccount.Id -RecoveryResourceGroupID $sourceVMResourcegroup.ResourceId -RecoveryProximityPlacementGroupId $vm.ProximityPlacementGroup.Id
```
14. Per disabilitare la replica, seguire i passaggi indicati [qui](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#disable-replication).

### <a name="vmware-to-azure"></a>Da VMware ad Azure

1. Assicurarsi di [preparare i server VMware locali](https://docs.microsoft.com/azure/site-recovery/vmware-azure-tutorial-prepare-on-premises) per il ripristino di emergenza in Azure.
2. Accedere al proprio account e impostare la sottoscrizione come specificato [qui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#log-into-azure).
3. [Configurare](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-up-a-recovery-services-vault) un insieme di credenziali di Servizi di ripristino e [impostare il contesto dell'insieme di credenziali](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#set-the-vault-context).
4. [Convalidare](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#validate-vault-registration) la registrazione dell'insieme di credenziali.
5. [Creare](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-a-replication-policy) i criteri di replica.
6. [Aggiungere](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#add-a-vcenter-server-and-discover-vms) un server vCenter e individuare le macchine virtuali e [creare](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#create-storage-accounts-for-replication) gli account di archiviazione per la replica.
7. Per replicare le macchine virtuali VMware, vedere qui i dettagli e usare il cmdlet di PowerShell seguente:

```azurepowershell
#Get the target resource group to be used
$ResourceGroup = Get-AzResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap = Get-AzRecoveryServicesAsrProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM1 = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1 using the Az.RecoveryServices module 2.0.0 onwards to replicate to managed disks
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableReplication1 = New-AzRecoveryServicesAsrReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -logStorageAccountId $LogStorageAccount.Id -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" -RecoveryProximityPlacementGroupId $recPpg.Id
```
8. Per controllare lo stato di replica e l'integrità della replica della macchina virtuale, è possibile usare il cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
9. Configurare le impostazioni di failover seguendo i passaggi indicati [qui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#configure-failover-settings).
10. [Eseguire](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#run-a-test-failover) un failover di test. 
11. Eseguire il failover in Azure usando [questi](https://docs.microsoft.com/azure/site-recovery/vmware-azure-disaster-recovery-powershell#fail-over-to-azure) passaggi.

### <a name="hyper-v-to-azure"></a>Da Hyper-V ad Azure

1. Assicurarsi di [preparare i server Hyper-V locali](https://docs.microsoft.com/azure/site-recovery/hyper-v-prepare-on-premises-tutorial) per il ripristino di emergenza in Azure.
2. [Accedere](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-1-sign-in-to-your-azure-account) ad Azure.
3. [Configurare](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-2-set-up-the-vault) un insieme di credenziali e [impostare](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-3-set-the-recovery-services-vault-context) il contesto dell'insieme di credenziali di Servizi di ripristino.
4. [Creare](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-4-create-a-hyper-v-site) un sito Hyper-V.
5. [Installare](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-5-install-the-provider-and-agent) il provider e l'agente.
6. [Creare](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-6-create-a-replication-policy) i criteri di replica.
7. Abilitare la replica usando i passaggi seguenti: 
    
    a. Recuperare l'elemento di protezione corrispondente alla macchina virtuale da proteggere, come segue:

    ```azurepowershell
    $VMFriendlyName = "Fabrikam-app"          #Name of the VM
    $ProtectableItem = Get-AzRecoveryServicesAsrProtectableItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName
    ```
    b. Proteggere la macchina virtuale. Se alla VM protetta è collegato più di un disco, specificare il disco del sistema operativo usando il parametro OSDiskName.
    
    ```azurepowershell
    $OSType = "Windows"          # "Windows" or "Linux"
    $DRjob = New-AzRecoveryServicesAsrReplicationProtectedItem -ProtectableItem $VM -Name $VM.Name -ProtectionContainerMapping $ProtectionContainerMapping -RecoveryAzureStorageAccountId   $StorageAccountID -OSDiskName $OSDiskNameList[$i] -OS $OSType -RecoveryResourceGroupId $ResourceGroupID -RecoveryProximityPlacementGroupId $recPpg.Id
    ```
    c. Attendere che le macchine virtuali raggiungano uno stato protetto dopo la replica iniziale. L’operazione può richiedere un certo tempo a seconda di fattori quali la quantità di dati da replicare e la larghezza di banda upstream disponibile in Azure. Quando viene raggiunto lo stato protetto, i valori State e StateDescription del processo vengono aggiornati come segue: 
    
    ```azurepowershell
    $DRjob = Get-AzRecoveryServicesAsrJob -Job $DRjob
    $DRjob | Select-Object -ExpandProperty State

    $DRjob | Select-Object -ExpandProperty StateDescription
    ```
    d. Aggiornare le proprietà di ripristino, ad esempio le dimensioni del ruolo macchina virtuale, e la rete di Azure a cui associare la scheda di interfaccia di rete della macchina virtuale dopo il failover.

    ```azurepowershell
    $nw1 = Get-AzVirtualNetwork -Name "FailoverNw" -ResourceGroupName "MyRG"

    $VMFriendlyName = "Fabrikam-App"

    $rpi = Get-AzRecoveryServicesAsrReplicationProtectedItem -ProtectionContainer $protectionContainer -FriendlyName $VMFriendlyName

    $UpdateJob = Set-AzRecoveryServicesAsrReplicationProtectedItem -InputObject $rpi -PrimaryNic $VM.NicDetailsList[0].NicId -RecoveryNetworkId $nw1.Id -RecoveryNicSubnetName $nw1.Subnets[0].Name

    $UpdateJob = Get-AzRecoveryServicesAsrJob -Job $UpdateJob

    $UpdateJob | Select-Object -ExpandProperty state

    Get-AzRecoveryServicesAsrJob -Job $job | Select-Object -ExpandProperty state
    ```
8. Eseguire un [failover](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-powershell-resource-manager#step-8-run-a-test-failover) di test.


## <a name="next-steps"></a>Passaggi successivi

Per eseguire la riprotezione e il failback per VMware in Azure, seguire i passaggi descritti [qui](https://docs.microsoft.com/azure/site-recovery/vmware-azure-prepare-failback).

Per eseguire il failover per Hyper-V in Azure, seguire i passaggi descritti [qui](https://docs.microsoft.com/azure/site-recovery/site-recovery-failover) e per eseguire il failback, seguire i passaggi illustrati [qui](https://docs.microsoft.com/azure/site-recovery/hyper-v-azure-failback).

Per altre informazioni, vedere [Failover in Site Recovery](site-recovery-failover.md).
