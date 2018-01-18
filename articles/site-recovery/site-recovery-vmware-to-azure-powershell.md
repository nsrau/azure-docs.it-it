---
title: Replicare ed eseguire il failover di macchine virtuali VMware in Azure con PowerShell per Azure Site Recovery | Microsoft Docs
description: Replicare ed eseguire il failover di macchine virtuali VMware in Azure con PowerShell per Azure Site Recovery
services: site-recovery
documentationcenter: 
author: bsiva
manager: abhemraj
editor: raynew
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/02/2018
ms.author: bsiva
ms.openlocfilehash: ee4847a61392a8eacde82ea62c3812d601b489f3
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/13/2018
---
# <a name="replicate-and-failover-vmware-virtual-machines-to-azure-using-azure-site-recovery-powershell"></a>Replicare ed eseguire il failover di macchine virtuali VMware in Azure con PowerShell per Azure Site Recovery

Questo articolo illustra come eseguire la replica e il failover di macchine virtuali VMware in Azure tramite Azure PowerShell. 

Si apprenderà come:

> [!div class="checklist"]
> - Creare un insieme di credenziali dei servizi di ripristino.
> - Impostare il contesto dell'insieme di credenziali.
> - Verificare che il server di configurazione e i server di elaborazione con scalabilità orizzontale siano registrati nell'insieme di credenziali.
> - Creare i criteri di replica ed eseguirne il mapping per poterli usare con il server di configurazione.
> - Aggiungere un server vCenter e individuare le macchine virtuali VMware.
> - Creare gli account di archiviazione in cui replicare le macchine virtuali.
> - Replicare le macchine virtuali VMware negli account di archiviazione di Azure.
> - Configurare le impostazioni di failover per la replica delle macchine virtuali.
> - Eseguire un failover di test, convalidarlo ed eliminarlo.
> - Eseguire il failover in Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:
- Assicurarsi di aver compreso i [componenti e l'architettura dello scenario](concepts-vmware-to-azure-architecture.md).
- Verificare i [requisiti di supporto](site-recovery-support-matrix-to-azure.md) per tutti i componenti.
- Assicurarsi di disporre della versione 5.0.1 o versioni successive del modulo AzureRm PowerShell. Se è necessario installare o aggiornare Azure PowerShell, vedere [Come installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Accedere alla sottoscrizione di Microsoft Azure

Accedere alla sottoscrizione di Azure usando il cmdlet Login-AzureRmAccount

```azurepowershell
Login-AzureRmAccount
```
Selezionare la sottoscrizione di Azure in cui si vuole replicare le macchine virtuali VMware. Usare il cmdlet Get-AzureRmSubscription per ottenere l'elenco delle sottoscrizioni di Azure a cui si ha accesso. Selezionare la sottoscrizione di Azure che si intende usare tramite il cmdlet Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

* Creare il gruppo di risorse in cui creare l'insieme di credenziali di Servizi di ripristino. Nell'esempio seguente, il gruppo di risorse è denominato VMwareDRtoAzurePS ed è stato creato nell'area Asia orientale.

```azurepowershell
New-AzureRmResourceGroup -Name "VMwareDRtoAzurePS" -Location "East Asia"
```
```
ResourceGroupName : VMwareDRtoAzurePS
Location          : eastasia
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRtoAzurePS
```
   
* Creare un insieme di credenziali di Servizi di ripristino. Nell'esempio seguente, l'insieme di credenziali di Servizi di ripristino è denominato VMwareDRToAzurePs ed è stato creato nell'area Asia orientale, nel gruppo di risorse creato nel passaggio precedente.

```azurepowershell
New-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePs" -Location "East Asia" -ResourceGroupName "VMwareDRToAzurePs"
```
```
Name              : VMwareDRToAzurePs
ID                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs
Type              : Microsoft.RecoveryServices/vaults
Location          : eastasia
ResourceGroupName : VMwareDRToAzurePs
SubscriptionId    : xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
Properties        : Microsoft.Azure.Commands.RecoveryServices.ARSVaultProperties
``` 

* Scaricare la chiave di registrazione dell'insieme di credenziali, che consente di registrare il server di configurazione locale nell'insieme di credenziali. La registrazione è parte integrante del processo di installazione del software del server di configurazione.

```azurepowershell
#Get the vault object by name and resource group and save it to the $vault PowerShell variable 
$vault = Get-AzureRmRecoveryServicesVault -Name "VMwareDRToAzurePS" -ResourceGroupName "VMwareDRToAzurePS"

#Download vault registration key to the path C:\Work
Get-AzureRmRecoveryServicesVaultSettingsFile -SiteRecovery -Vault $Vault -Path "C:\Work\"
```
```
FilePath
--------
C:\Work\VMwareDRToAzurePs_2017-11-23T19-52-34.VaultCredentials
```

Usare la chiave di registrazione dell'insieme di credenziali scaricata e seguire le procedure descritte negli articoli elencati di seguito per completare l'installazione e la registrazione del server di configurazione.
* [Scegliere gli obiettivi della protezione](site-recovery-set-up-vmware-to-azure.md#choose-your-protection-goals)
* [Configurare l'ambiente di origine](site-recovery-set-up-vmware-to-azure.md#set-up-the-source-environment) 

## <a name="set-the-vault-context"></a>Impostare il contesto dell'insieme di credenziali

> [!TIP]
> Il modulo PowerShell per Azure Site Recovery (modulo AzureRm.RecoveryServices.SiteRecovery) viene fornito con alias di facile utilizzo per la maggior parte dei cmdlet. I cmdlet disponibili nel modulo assumono il formato *\<Operazione>-**AzureRmRecoveryServicesAsr**\<Oggetto>* e hanno alias equivalenti che assumono il formato *\<Operazione>-**ASR**\<Oggetto>*. In questo articolo vengono usati gli alias dei cmdlet per una maggiore semplicità di lettura.

Impostare il contesto dell'insieme di credenziali usando il cmdlet Set-ASRVaultContext. In questo modo, le successive operazioni di Azure Site Recovery nella sessione di PowerShell verranno eseguite nel contesto dell'insieme di credenziali selezionato. Nell'esempio seguente, vengono usati i dettagli dell'insieme di credenziali ottenuti dalla variabile $vault per specificare il contesto dell'insieme di credenziali per la sessione di PowerShell.
 ```azurepowershell
Set-ASRVaultContext -Vault $vault
```
```
ResourceName      ResourceGroupName ResourceNamespace          ResouceType
------------      ----------------- -----------------          -----------
VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
```

Nelle sezioni successive di questo articolo si presuppone che il contesto dell'insieme di credenziali per le operazioni di Azure Site Recovery sia stato impostato.

## <a name="validate-that-your-configuration-server-and-scale-out-process-servers-are-registered-to-the-vault"></a>Verificare che il server di configurazione e i server di elaborazione con scalabilità orizzontale siano registrati nell'insieme di credenziali

 Si presuppone che:
- Un server di configurazione denominato *ConfigurationServer* sia stato registrato in questo insieme di credenziali
- Un server di elaborazione aggiuntivo denominato *ScaleOut-ProcessServer* sia stato registrato in *ConfigurationServer*
- Account denominati *vCenter_account*, *WindowsAccount* e *LinuxAccount* siano stati impostati nel server di configurazione. Questi account consentono di aggiungere il server vCenter per individuare le macchine virtuali ed eseguire l'installazione push del software del servizio Mobility nei server Windows e Linux che devono essere replicati.

I server di configurazione registrati sono rappresentati in Azure Site Recovery da un oggetto di infrastruttura. In questo passaggio si ottiene l'elenco degli oggetti di infrastruttura presenti nell'insieme di credenziali e si identifica il server di configurazione.

```azurepowershell
# Verify that the Configuration server is successfully registered to the vault
$ASRFabrics = Get-ASRFabric
$ASRFabrics.count
```
```
1
```
```azurepowershell
#Print details of the Configuration Server
$ASRFabrics[0]
```
```
Name                  : 2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
FriendlyName          : ConfigurationServer
ID                    : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationFabrics
                        /2c33d710a5ee6af753413e97f01e314fc75938ea4e9ac7bafbf4a31f6804460d
Type                  : Microsoft.RecoveryServices/vaults/replicationFabrics
FabricType            : VMware
SiteIdentifier        : ef7a1580-f356-4a00-aa30-7bf80f952510
FabricSpecificDetails : Microsoft.Azure.Commands.RecoveryServices.SiteRecovery.ASRVMWareSpecificDetails
```

* Identificare i server di elaborazione che è possibile usare per la replica delle macchine.

```azurepowershell
$ProcessServers = $ASRFabrics[0].FabricSpecificDetails.ProcessServers
for($i=0; $i -lt $ProcessServers.count; $i++) {
 "{0,-5} {1}" -f $i, $ProcessServers[$i].FriendlyName
}
```
```
0     ScaleOut-ProcessServer
1     ConfigurationServer
```

In base all'output precedente, ***$ProcessServers[0]*** corrisponde a *ScaleOut-ProcessServer* e ***$ProcessServers[1]*** corrisponde al ruolo del server di elaborazione in *ConfigurationServer*

* Identificare gli account configurati nel server di configurazione.

```azurepowershell
$AccountHandles = $ASRFabrics[0].FabricSpecificDetails.RunAsAccounts
#Print the account details
$AccountHandles
```
```
AccountId AccountName
--------- -----------
1         vCenter_account
2         WindowsAccount
3         LinuxAccount
```

In base all'output precedente, ***$AccountHandles[0]*** corrisponde all'account *vCenter_account*, ***$AccountHandles[1]*** all'account *WindowsAccount* e ***$AccountHandles[2]*** all'account *LinuxAccount*

## <a name="create-a-replication-policy-and-map-it-for-use-with-the-configuration-server"></a>Creare criteri di replica ed eseguirne il mapping per poterli usare con il server di configurazione

In questo passaggio vengono creati due gruppi di criteri di replica, i primi per replicare le macchine virtuali VMware in Azure, i secondi per replicare di nuovo nel sito VMware locale le macchine virtuali sottoposte a failover in esecuzione in Azure.

> [!NOTE]
> La maggior parte delle operazioni di Azure Site Recovery viene eseguita in modo asincrono. Quando si avvia un'operazione, viene inviato un processo di Azure Site Recovery e viene restituito un oggetto di monitoraggio del processo, che consente di monitorare lo stato dell'operazione.

* Creare criteri di replica denominati *ReplicationPolicy* per replicare in Azure le macchine virtuali VMware con le proprietà specificate.

```azurepowershell
$Job_PolicyCreate = New-ASRPolicy -VMwareToAzure -Name "ReplicationPolicy" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60

# Track Job status to check for completion
while (($Job_PolicyCreate.State -eq "InProgress") -or ($Job_PolicyCreate.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_PolicyCreate = Get-ASRJob -Job $Job_PolicyCreate
}

#Display job status
$Job_PolicyCreate
```
```
Name             : 8d18e2d9-479f-430d-b76b-6bc7eb2d0b3e
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/8d18e2d
                   9-479f-430d-b76b-6bc7eb2d0b3e
Type             :
JobType          : AddProtectionProfile
DisplayName      : Create replication policy
ClientRequestId  : a162b233-55d7-4852-abac-3d595a1faac2 ActivityId: 9895234a-90ea-4c1a-83b5-1f2c6586252a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:49:24 AM
EndTime          : 11/24/2017 2:49:23 AM
TargetObjectId   : ab31026e-4866-5440-969a-8ebcb13a372f
TargetObjectType : ProtectionProfile
TargetObjectName : ReplicationPolicy
AllowedActions   :
Tasks            : {Prerequisites check for creating the replication policy, Creating the replication policy}
Errors           : {}
```

* Creare i criteri di replica da usare per il failback da Azure al sito VMware locale.

```azurepowershell
$Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
```

Usare i dettagli del processo presenti in *$Job_FailbackPolicyCreate* per tenere traccia dell'operazione fino al completamento.

* Creare un mapping del contenitore di protezione per eseguire il mapping dei criteri di replica con il server di configurazione.

```azurepowershell
#Get the protection container corresponding to the Configuration Server
$ProtectionContainer = Get-ASRProtectionContainer -Fabric $ASRFabrics[0]

#Get the replication policies to map by name.
$ReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy"
$FailbackReplicationPolicy = Get-ASRPolicy -Name "ReplicationPolicy-Failback"

# Associate the replication policies to the protection container corresponding to the Configuration Server. 

$Job_AssociatePolicy = New-ASRProtectionContainerMapping -Name "PolicyAssociation1" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociatePolicy.State -eq "InProgress") -or ($Job_AssociatePolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociatePolicy = Get-ASRJob -Job $Job_AssociatePolicy
}
$Job_AssociatePolicy.State

$Job_AssociateFailbackPolicy = New-ASRProtectionContainerMapping -Name "FailbackPolicyAssociation" -PrimaryProtectionContainer $ProtectionContainer -Policy $Policy[0]

# Check the job status
while (($Job_AssociateFailbackPolicy.State -eq "InProgress") -or ($Job_AssociateFailbackPolicy.State -eq "NotStarted")){ 
        sleep 10; 
        $Job_AssociateFailbackPolicy = Get-ASRJob -Job $Job_AssociateFailbackPolicy
}
$Job_AssociateFailbackPolicy.State

```

## <a name="add-a-vcenter-server-and-discover-vmware-virtual-machines"></a>Aggiungere un server vCenter e individuare le macchine virtuali VMware

Aggiungere un server vCenter in base all'indirizzo IP o al nome host. Il parametro **-port** specifica la porta del server vCenter a cui connettersi, il parametro **-Name** specifica un nome descrittivo da usare per il server vCenter e il parametro **-Account** specifica l'handle di account del server di configurazione da usare per individuare le macchine virtuali gestite dal server vCenter.

```azurepowershell
# The $AccountHandles[0] variable holds details of vCenter_account

$Job_AddvCenterServer = New-ASRvCenter -Fabric $ASRFabrics[0] -Name "MyvCenterServer" -IpOrHostName "10.150.24.63" -Account $AccountHandles[0] -Port 443

#Wait for the job to complete and ensure it completed successfully

while (($Job_AddvCenterServer.State -eq "InProgress") -or ($Job_AddvCenterServer.State -eq "NotStarted")) { 
        sleep 30; 
        $Job_AddvCenterServer = Get-ASRJob -Job $Job_AddvCenterServer
}
$Job_AddvCenterServer
```
```
Name             : 0f76f937-f9cf-4e0e-bf27-10c9d1c252a4
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/0f76f93
                   7-f9cf-4e0e-bf27-10c9d1c252a4
Type             :
JobType          : DiscoverVCenter
DisplayName      : Add vCenter server
ClientRequestId  : a2af8892-5686-4d64-a528-10445bc2f698 ActivityId: 7ec05aad-002e-4da0-991f-95d0de7a9f3a
State            : Succeeded
StateDescription : Completed
StartTime        : 11/24/2017 2:41:47 AM
EndTime          : 11/24/2017 2:44:37 AM
TargetObjectId   : 10.150.24.63
TargetObjectType : VCenter
TargetObjectName : MyvCenterServer
AllowedActions   :
Tasks            : {Adding vCenter server}
Errors           : {}
```

## <a name="create-storage-accounts"></a>Creare gli account di archiviazione

In questo passaggio vengono creati gli account di archiviazione da usare per la replica. Questi account di archiviazione verranno usati in un secondo momento per replicare le macchine virtuali. Verificare che gli account di archiviazione vengano creati nella stessa area di Azure in cui è stato creato l'insieme di credenziali. È possibile ignorare questo passaggio se si prevede di usare un account di archiviazione esistente per la replica.

> [!NOTE]
> Durante la replica di macchine virtuali locali in un account di archiviazione Premium, è necessario specificare un account di archiviazione standard aggiuntivo (account di archiviazione log). L'account di archiviazione log svolge la funzione di archivio intermedio in cui vengono conservati i log di replica fino a quando non possono essere applicati nella destinazione di archiviazione Premium.
>

```azurepowershell

$PremiumStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-virtual-machines"></a>Replicare le macchine virtuali VMware

Per individuare le macchine virtuali, il server vCenter impiega un intervallo di circa 15-20 minuti, al termine del quale, per ogni macchina virtuale individuata, viene creato in Azure Site Recovery un oggetto di elemento da proteggere. In questo passaggio, tre delle macchine virtuali individuate vengono replicate negli account di archiviazione di Azure creati nel passaggio precedente.

Per proteggere una macchina virtuale individuata sono necessari i dettagli seguenti:
* L'elemento da proteggere che deve essere replicato.
* L'account di archiviazione in cui replicare la macchina virtuale. È necessario anche un archivio di log per la protezione delle macchine virtuali destinate a un account di archiviazione Premium.
* Il server di elaborazione usato per la replica. L'elenco dei server di elaborazione disponibili è stato recuperato e salvato nelle variabili ***$ProcessServers[0]*** *(ScaleOut-ProcessServer)* e ***$ProcessServers[1]*** *(ConfigurationServer)*.
* L'account da usare per eseguire nei computer l'installazione push del software del servizio Mobility. L'elenco degli account disponibili è stato recuperato e archiviato nella variabile ***$AccountHandles***.
* Il mapping del contenitore di protezione per i criteri di replica da usare per la replica.
* Il gruppo di risorse in cui devono essere create le macchine virtuali in caso di failover.
* Facoltativamente, la rete virtuale e la subnet di Azure a cui devono essere connesse le macchine virtuali sottoposte a failover.

Replicare ora le macchine virtuali seguenti usando le impostazioni specificate in questa tabella


|Macchina virtuale  |Server di elaborazione        |Account di archiviazione              |Account di archiviazione log  |Criterio           |Account per l'installazione del servizio Mobility|Gruppo di risorse di destinazione  | Rete virtuale di destinazione  |Subnet di destinazione  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |ScaleOut-ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| N/D                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| N/D                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |ASR-vnet                 |Subnet-1       |   

 
```azurepowershell

#Get the target resource group to be used
$ResourceGroup = Get-AzureRmResourceGroup -Name "VMwareToAzureDrPs"

#Get the target virtual network to be used
$RecoveryVnet = Get-AzureRmVirtualNetwork -Name "ASR-vnet" -ResourceGroupName "asrrg" 

#Get the protection container mapping for replication policy named ReplicationPolicy
$PolicyMap  = Get-ASRProtectionContainerMapping -ProtectionContainer $ProtectionContainer | where PolicyFriendlyName -eq "ReplicationPolicy"

#Get the protectable item corresponding to the virtual machine Win2K12VM1
$VM1 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "Win2K12VM1"

# Enable replication for virtual machine Win2K12VM1
# The name specified for the replicated item needs to be unique within the protection container. Using a random GUID to ensure uniqueness
$Job_EnableRepication1 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM1 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $PremiumStorageAccount.Id -LogStorageAccountId $LogStorageAccount.Id -ProcessServer $ProcessServers[0] -Account $AccountHandles[1] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

#Get the protectable item corresponding to the virtual machine CentOSVM1
$VM2 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM1"

# Enable replication for virtual machine CentOSVM1
$Job_EnableRepication2 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM2 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1"

#Get the protectable item corresponding to the virtual machine CentOSVM2
$VM3 = Get-ASRProtectableItem -ProtectionContainer $ProtectionContainer -FriendlyName "CentOSVM2"

# Enable replication for virtual machine CentOSVM2
$Job_EnableRepication3 = New-ASRReplicationProtectedItem -VMwareToAzure -ProtectableItem $VM3 -Name (New-Guid).Guid -ProtectionContainerMapping $PolicyMap -RecoveryAzureStorageAccountId $ReplicationStdStorageAccount.Id  -ProcessServer $ProcessServers[1] -Account $AccountHandles[2] -RecoveryResourceGroupId $ResourceGroup.ResourceId -RecoveryAzureNetworkId $RecoveryVnet.Id -RecoveryAzureSubnetName "Subnet-1" 

```

Al termine del processo di abilitazione della replica, viene avviata la replica iniziale per le macchine virtuali. È possibile che la replica iniziale richieda qualche minuto, in base alla quantità di dati da replicare e alla larghezza di banda disponibile per la replica. Al termine della replica iniziale, la macchina virtuale viene impostata su uno stato protetto. A questo punto, è possibile eseguire un failover di test per la macchina virtuale, aggiungerlo ai piani di ripristino e così via.

Per controllare lo stato di replica e l'integrità della replica della macchina virtuale, è possibile usare il cmdlet Get-ASRReplicationProtectedItem.

```azurepowershell
Get-ASRReplicationProtectedItem -ProtectionContainer $ProtectionContainer | Select FriendlyName, ProtectionState, ReplicationHealth
```
```
FriendlyName ProtectionState                 ReplicationHealth
------------ ---------------                 -----------------
CentOSVM1    Protected                       Normal
CentOSVM2    InitialReplicationInProgress    Normal
Win2K12VM1   Protected                       Normal
```

## <a name="configure-failover-settings-for-replicating-virtual-machines"></a>Configurare le impostazioni di failover per la replica di macchine virtuali

Le impostazioni di failover per i computer protetti possono essere aggiornate usando il cmdlet Set-ASRReplicationProtectedItem. Di seguito sono elencate alcune delle impostazioni che è possibile aggiornare con questo cdmlet:
* Nome della macchina virtuale da creare in caso di failover
* Dimensione della macchina virtuale da creare in caso di failover
* Rete virtuale e subnet di Azure a cui devono connettersi le schede di interfaccia di rete della macchina virtuale in caso di failover
* Failover su Managed Disks
* Applicazione del vantaggio Azure Hybrid Use
* Assegnazione di un indirizzo IP statico dalla rete virtuale di destinazione alla macchina virtuale in caso di failover.

In questo esempio viene aggiornata la dimensione della macchina virtuale da creare in caso di failover della macchina virtuale *Win2K12VM1* e si specifica che la macchina virtuale deve usare Managed Disks in caso di failover.

```azurepowershell
$ReplicatedVM1 = Get-ASRReplicationProtectedItem -FriendlyName "Win2K12VM1" -ProtectionContainer $ProtectionContainer

Set-ASRReplicationProtectedItem -InputObject $ReplicatedVM1 -Size "Standard_DS11" -UseManagedDisk True
```
```
Name             : cafa459c-44a7-45b0-9de9-3d925b0e7db9
ID               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/VMwareDRToAzurePs/providers/Microsoft.RecoveryServices/vaults/VMwareDRToAzurePs/replicationJobs/cafa459
                   c-44a7-45b0-9de9-3d925b0e7db9
Type             :
JobType          : UpdateVmProperties
DisplayName      : Update the virtual machine
ClientRequestId  : b0b51b2a-f151-4e9a-a98e-064a5b5131f3 ActivityId: ac2ba316-be7b-4c94-a053-5363f683d38f
State            : InProgress
StateDescription : InProgress
StartTime        : 11/24/2017 2:04:26 PM
EndTime          :
TargetObjectId   : 88bc391e-d091-11e7-9484-000c2955bb50
TargetObjectType : ProtectionEntity
TargetObjectName : Win2K12VM1
AllowedActions   :
Tasks            : {Update the virtual machine properties}
Errors           : {}
```

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Eseguire un failover di test, convalidarlo ed eliminarlo

```azurepowershell
#Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

#Get details of the test failover virtual network to be used
TestFailovervnet = Get-AzureRmVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

#Start the test failover operation
$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
```
Al termine del processo di failover di test, si noterà che in Azure è stata creata una macchina virtuale con suffisso *"-Test"* (in questo caso, Win2K12VM1-Test). 

È ora possibile connettersi alla macchina virtuale sottoposta al failover di test e convalidare il failover di test.

Eliminare il failover di test usando il cmdlet Start-ASRTestFailoverCleanupJob. Questa operazione elimina la macchina virtuale creata nell'ambito dell'operazione di failover di test.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
```

## <a name="failover-to-azure"></a>Failover in Azure

In questo passaggio viene eseguito il failover della macchina virtuale Win2K12VM1 in un punto di ripristino specifico.

```azurepowershell
# Get the list of available recovery points for Win2K12VM1
$RecoveryPoints = Get-ASRRecoveryPoint -ReplicationProtectedItem $ReplicatedVM1
"{0} {1}" -f $RecoveryPoints[0].RecoveryPointType, $RecoveryPoints[0].RecoveryPointTime
```
```
CrashConsistent 11/24/2017 5:28:25 PM
```
```azurepowershell

#Start the failover job
$Job_Failover = Start-ASRUnplannedFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -Direction PrimaryToRecovery -RecoveryPoint $RecoveryPoints[0]
do {
        $Job_Failover = Get-ASRJob -Job $Job_Failover;
        sleep 60;
} while (($Job_Failover.State -eq "InProgress") -or ($JobFailover.State -eq "NotStarted"))
$Job_Failover.State
```
```
Succeeded
```

Al termine del failover, è possibile eseguire il commit dell'operazione di failover e configurare la replica inversa da Azure al sito VMware locale.

## <a name="next-steps"></a>Passaggi successivi
Vedere l'articolo di [riferimento di PowerShell per Azure Site Recovery ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) per informazioni su come poter eseguire altre attività, ad esempio la creazione di piani di ripristino e il failover di test di piani di ripristino tramite PowerShell.
