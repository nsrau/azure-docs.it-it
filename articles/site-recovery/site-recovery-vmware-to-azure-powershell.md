---
title: Eseguire la replica e failover le macchine virtuali VMware in Azure con Azure Site Recovery PowerShell | Documenti Microsoft
description: Eseguire la replica e failover le macchine virtuali VMware in Azure tramite Azure PowerShell di ripristino del sito
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
ms.date: 14/12/2017
ms.author: bsiva
ms.openlocfilehash: 3cf2478eb810961604e1218731f5303abd0f611a
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/18/2017
---
# <a name="replicate-and-failover-vmware-virtual-machines-to-azure-using-azure-site-recovery-powershell"></a>Eseguire la replica e failover le macchine virtuali VMware in Azure tramite Azure PowerShell di ripristino del sito

In questo articolo, vengono visualizzati la modalità di replica e failover delle macchine virtuali VMware in Azure tramite Azure PowerShell. 

Si apprenderà come:

> [!div class="checklist"]
> - Creare un insieme di credenziali dei servizi di ripristino.
> - Impostare il contesto dell'insieme di credenziali.
> - Verificare che il Server di configurazione e i server di scalabilità orizzontale sono registrati nell'insieme di credenziali.
> - Creare un criterio di replica ed eseguirne il mapping per l'utilizzo con il Server di configurazione.
> - Aggiungere un server vCenter e individuare le macchine virtuali VMware.
> - Creare gli account di archiviazione per la replica delle macchine virtuali.
> - Replicare le macchine virtuali VMware agli account di archiviazione di Azure.
> - Configurare le impostazioni di failover per la replica delle macchine virtuali.
> - Eseguire un failover di test, convalida e il failover di test di pulizia.
> - Failover in Azure.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:
- Assicurarsi di aver compreso i [componenti e l'architettura dello scenario](concepts-vmware-to-azure-architecture.md).
- Verificare i [requisiti di supporto](site-recovery-support-matrix-to-azure.md) per tutti i componenti.
- Si dispone di versione 5.0.1 o maggiore del modulo AzureRm PowerShell. Se è necessario installare o aggiornare Azure PowerShell, seguire questo [Guida per installare e configurare Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="log-in-to-your-microsoft-azure-subscription"></a>Accedere alla sottoscrizione di Microsoft Azure

Accedere alla sottoscrizione di Azure tramite il cmdlet di accesso AzureRmAccount

```azurepowershell
Login-AzureRmAccount
```
Selezionare la sottoscrizione di Azure che si desidera replicare macchine virtuali VMware. Usare il cmdlet Get-AzureRmSubscription per ottenere l'elenco delle sottoscrizioni di Azure che si dispone di accesso a. Selezionare la sottoscrizione di Azure con il cmdlet Select-AzureRmSubscription.

```azurepowershell
Select-AzureRmSubscription -SubscriptionName "ASR Test Subscription"
```
## <a name="create-a-recovery-services-vault"></a>Creare un insieme di credenziali di Servizi di ripristino

* Creare un gruppo di risorse in cui creare l'insieme di credenziali di servizi di ripristino. Nell'esempio seguente, il gruppo di risorse denominato VMwareDRtoAzurePS e viene creato nell'area Asia orientale.

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
   
* Creare un insieme di credenziali di servizi di ripristino. Nell'esempio seguente, l'insieme di credenziali di servizi di ripristino è denominato VMwareDRToAzurePs e viene creato nell'area Asia orientale e nel gruppo di risorse creato nel passaggio precedente.

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

* Scaricare la chiave di registrazione dell'insieme di credenziali per l'insieme di credenziali. La chiave di registrazione dell'insieme di credenziali viene utilizzata per registrare il Server di configurazione locale per questo insieme di credenziali. La registrazione è parte del processo di installazione del software Server di configurazione.

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

Utilizzare la chiave di registrazione dell'insieme di credenziali scaricato e seguire i passaggi negli articoli forniti di seguito per completare l'installazione e la registrazione del Server di configurazione.
* [Scegliere gli obiettivi di protezione](site-recovery-set-up-vmware-to-azure.md#choose-your-protection-goals)
* [Configurare l'ambiente di origine](site-recovery-set-up-vmware-to-azure.md#set-up-the-source-environment) 

## <a name="set-the-vault-context"></a>Impostare il contesto dell'insieme di credenziali

> [!TIP]
> Il modulo PowerShell di Azure Site Recovery (modulo AzureRm.RecoveryServices.SiteRecovery) viene fornito con alias facile da usare per la maggior parte dei cmdlet. I cmdlet nel modulo assumono la forma  *\<operazione >-**AzureRmRecoveryServicesAsr**\<oggetto >* e dispongono di equivalenti gli alias che assumono la forma  *\<operazione >-**ASR**\<oggetto >*. In questo articolo utilizza gli alias di cmdlet per facilitare la lettura.

Impostare il contesto dell'insieme di credenziali usando il cmdlet Set-ASRVaultContext. Una volta impostato, vengono eseguite le operazioni successive di Azure Site Recovery nella sessione di PowerShell nel contesto dell'insieme di credenziali selezionato. Nell'esempio seguente, i dettagli dell'insieme di credenziali di $vault variabile viene utilizzata per specificare il contesto dell'insieme di credenziali per la sessione di PowerShell.
 ```azurepowershell
Set-ASRVaultContext -Vault $vault
```
```
ResourceName      ResourceGroupName ResourceNamespace          ResouceType
------------      ----------------- -----------------          -----------
VMwareDRToAzurePs VMwareDRToAzurePs Microsoft.RecoveryServices vaults
```

Nelle sezioni successive di questo articolo si presuppongono che il contesto dell'insieme di credenziali per le operazioni di Azure Site Recovery è stato impostato.

## <a name="validate-that-your-configuration-server-and-scale-out-process-servers-are-registered-to-the-vault"></a>Verificare che il Server di configurazione e i server di scalabilità orizzontale sono registrati nell'insieme di credenziali

 Si supponga di:
- Un server di configurazione denominato *ConfigurationServer* è stato registrato per questo insieme di credenziali
- Un Server di elaborazione aggiuntive denominato *ProcessServer di scalabilità orizzontale* è stato registrato per *ConfigurationServer*
- Account denominato *vCenter_account*, *WindowsAccount*, e *LinuxAccount* sono stati impostati nel server di configurazione. Questi account vengono utilizzati per aggiungere il server vCenter per individuare le macchine virtuali e per il software del servizio di mobilità nel server di Windows e Linux devono essere replicate l'installazione push.

Server di configurazione registrati sono rappresentati da un oggetto di infrastruttura in Azure Site Recovery. In questo passaggio, ottenere l'elenco di infrastruttura di oggetti nell'insieme di credenziali e identificare il Server di configurazione.

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

* Identificare i server di elaborazione che può essere usato per replicare le macchine.

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

Nell'output precedente ***$ProcessServers [0]*** corrisponde a *ProcessServer di scalabilità orizzontale* e ***$ProcessServers [1]*** corrisponde al ruolo del Server di elaborazione su *ConfigurationServer*

* Identificare gli account configurati nel Server di configurazione.

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

Nell'output precedente ***$AccountHandles [0]*** corrisponde all'account di *vCenter_account*, ***$AccountHandles [1]*** account *WindowsAccount*, e ***$AccountHandles [2]*** account *LinuxAccount*

## <a name="create-a-replication-policy-and-map-it-for-use-with-the-configuration-server"></a>Creare un criterio di replica ed eseguirne il mapping per l'utilizzo con il Server di configurazione

In questo passaggio vengono creati due criteri di replica. Failover di un criterio per replicare le macchine virtuali VMware in Azure e l'altro per replicare le macchine virtuali in esecuzione in Azure nuovo nel sito di VMware locale.

> [!NOTE]
> La maggior parte delle operazioni di Azure Site Recovery vengono eseguite in modo asincrono. Quando si avvia un'operazione, viene inviato un processo di Azure Site Recovery e viene restituito un processo di rilevamento di oggetto. Questo processo rilevamento oggetto può essere utilizzato per monitorare lo stato dell'operazione.

* Creare un criterio di replica denominato *ReplicationPolicy* per replicare le macchine virtuali VMware in Azure con le proprietà specificate.

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

* Creare un criterio di replica da utilizzare per il failback da Azure al sito locale VMware.

```azurepowershell
$Job_FailbackPolicyCreate = New-ASRPolicy -AzureToVMware -Name "ReplicationPolicy-Failback" -RecoveryPointRetentionInHours 24 -ApplicationConsistentSnapshotFrequencyInHours 4 -RPOWarningThresholdInMinutes 60
```

Usare i dettagli del processo in *Job_FailbackPolicyCreate $* per tenere traccia dell'operazione di completamento.

* Creare un mapping di contenitore di protezione per eseguire il mapping dei criteri di replica con il Server di configurazione.

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

Aggiungere un Server vCenter tramite indirizzo IP o nome host. Il **-porta** parametro specifica la porta del server vCenter a cui connettersi, **-nome** parametro specifica un nome descrittivo da utilizzare per il server vCenter e **-Account** parametro specifica l'handle di account nel server di configurazione da utilizzare per individuare le macchine virtuali gestite dal server vCenter.

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

In questo passaggio vengono creati gli account di archiviazione da utilizzare per la replica. Questi account di archiviazione vengono utilizzati in un secondo momento per replicare le macchine virtuali. Verificare che gli account di archiviazione vengono creati nella stessa area di Azure dell'insieme di credenziali. È possibile ignorare questo passaggio se si prevede di utilizzare un account di archiviazione esistente per la replica.

> [!NOTE]
> Durante la replica di macchine virtuali locali in un account di archiviazione premium, è necessario specificare un account di archiviazione standard aggiuntivi (account di archiviazione di log). L'account di archiviazione del log contiene i log di replica come archivio intermedio fino a quando il log può essere applicato nella destinazione di archiviazione premium.
>

```azurepowershell

$PremiumStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "premiumstorageaccount1" -Location "East Asia" -SkuName Premium_LRS

$LogStorageAccount = New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "logstorageaccount1" -Location "East Asia" -SkuName Standard_LRS

$ReplicationStdStorageAccount= New-AzureRmStorageAccount -ResourceGroupName "VMwareDRToAzurePs" -Name "replicationstdstorageaccount1" -Location "East Asia" -SkuName Standard_LRS
```

## <a name="replicate-vmware-virtual-machines"></a>Replica delle macchine virtuali VMware

Sono necessari circa 15-20 minuti per le macchine virtuali vengano rilevati dal server vCenter. Una volta individuato, viene creato un oggetto di un elemento da proteggere in Azure Site Recovery per ogni macchina virtuale individuato. In questo passaggio, tre delle macchine virtuali individuate vengono replicati per gli account di archiviazione di Azure creati nel passaggio precedente.

È necessario che i dettagli seguenti per proteggere una macchina virtuale individuata:
* Elemento da proteggere da replicare.
* L'account di archiviazione per replicare la macchina virtuale. Inoltre, un archivio di log è necessaria per proteggere le macchine virtuali in un account di archiviazione premium.
* Il Server di elaborazione da utilizzare per la replica. L'elenco di server di elaborazione disponibili recuperato e salvato nel ***$ProcessServers [0]****(scalabilità orizzontale ProcessServer)* e ***$ProcessServers [1]*** *(ConfigurationServer)* variabili.  
* L'account da utilizzare per il software del servizio di mobilità nel computer di installazione push. L'elenco di account disponibili recuperato e archiviato nel ***$AccountHandles*** variabile.
* Il mapping di contenitore di protezione per il criterio di replica da utilizzare per la replica.
* Il gruppo di risorse in cui è necessario creare macchine virtuali in caso di failover.
* Facoltativamente, la rete virtuale di Azure e la subnet a cui tale macchina virtuale con failover deve essere connesso.

Ora di replicare le macchine virtuali seguenti utilizzando le impostazioni specificate in questa tabella


|Macchina virtuale  |Server di elaborazione        |Account di archiviazione              |Account di archiviazione di log  |Criterio           |Account per l'installazione del servizio di mobilità|Gruppo di risorse di destinazione  | Rete virtuale di destinazione  |Subnet di destinazione  |
|-----------------|----------------------|-----------------------------|---------------------|-----------------|-----------------------------------------|-----------------------|-------------------------|---------------|
|Win2K12VM1       |Scalabilità orizzontale ProcessServer|premiumstorageaccount1       |logstorageaccount1   |ReplicationPolicy|WindowsAccount                           |VMwareDRToAzurePs      |Rete virtuale di ripristino automatico di sistema                 |Subnet-1       |
|CentOSVM1       |ConfigurationServer   |replicationstdstorageaccount1| N/D                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |Rete virtuale di ripristino automatico di sistema                 |Subnet-1       |   
|CentOSVM2       |ConfigurationServer   |replicationstdstorageaccount1| N/D                 |ReplicationPolicy|LinuxAccount                             |VMwareDRToAzurePs      |Rete virtuale di ripristino automatico di sistema                 |Subnet-1       |   

 
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

Una volta che il processo di abilitazione della replica viene completato correttamente, la replica iniziale viene avviata per le macchine virtuali. La replica iniziale potrebbe richiedere qualche minuto a seconda della quantità di dati da replicare e la larghezza di banda disponibile per la replica. Dopo aver completato la replica iniziale, la macchina virtuale passa a uno stato protetto. Una volta che la macchina virtuale raggiunge uno stato protetto, che è possibile eseguire un failover di test per la macchina virtuale, aggiungerlo ai piani di ripristino e così via.

È possibile controllare lo stato di replica e l'integrità di replica della macchina virtuale con il cmdlet Get-ASRReplicationProtectedItem.

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

## <a name="configure-failover-settings-for-replicating-virtual-machines"></a>Configurare le impostazioni di failover per la replica delle macchine virtuali

Le impostazioni di failover per i computer protetti possono essere aggiornate usando il cmdlet Set-ASRReplicationProtectedItem. Alcune impostazioni che possono essere aggiornate tramite questo cmdlet sono:
* Nome della macchina virtuale da creare in caso di failover
* Dimensioni della macchina virtuale da creare sul failover delle macchine Virtuali
* Rete virtuale di Azure e una subnet che le schede di interfaccia della macchina virtuale deve essere connessa a failover
* Failover su dischi gestiti
* Applicare il vantaggio di utilizzare ibridi di Azure
* Assegnare un indirizzo IP statico dalla rete virtuale di destinazione per poter essere assegnati alla macchina virtuale in caso di failover.

In questo esempio, si aggiorna la dimensione di macchina virtuale della macchina virtuale da creare in caso di failover per la macchina virtuale *Win2K12VM1* e specificare che la macchina virtuale, utilizzare Gestione dischi in caso di failover.

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

## <a name="perform-a-test-failover-validate-and-cleanup-test-failover"></a>Eseguire un failover di test, convalida e il failover di test di pulizia

```azurepowershell
#Test failover of Win2K12VM1 to the test virtual network "V2TestNetwork"

#Get details of the test failover virtual network to be used
TestFailovervnet = Get-AzureRmVirtualNetwork -Name "V2TestNetwork" -ResourceGroupName "asrrg" 

#Start the test failover operation
$TFOJob = Start-ASRTestFailoverJob -ReplicationProtectedItem $ReplicatedVM1 -AzureVMNetworkId $TestFailovervnet.Id -Direction PrimaryToRecovery
```
Una volta completata del processo di failover di test, si noterà che una macchina virtuale con suffisso *"-Test"* (Win2K12VM1-Test in questo caso) al relativo nome viene creato in Azure. 

È ora possibile connettersi al test di failover macchina virtuale e convalidare il failover di test.

Failover di test di pulizia utilizzando il cmdlet Start-ASRTestFailoverCleanupJob. Questa operazione Elimina la macchina virtuale creata come parte dell'operazione di failover di test.

```azurepowershell
$Job_TFOCleanup = Start-ASRTestFailoverCleanupJob -ReplicationProtectedItem $ReplicatedVM1
```

## <a name="failover-to-azure"></a>Failover in Azure

In questo passaggio è failover la macchina virtuale Win2K12VM1 a un punto di ripristino specifico.

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

Una volta eseguito correttamente il failover, è possibile eseguire il commit del failover operazione e configurazione della replica inversa da Azure nuovo nel sito VMware locale.

## <a name="next-steps"></a>Passaggi successivi
Visualizzazione di [riferimento di PowerShell di Azure Site Recovery ](https://docs.microsoft.com/powershell/module/AzureRM.RecoveryServices.SiteRecovery) per informazioni su come è possibile eseguire altre attività, ad esempio la creazione di piani di ripristino e il test failover dei piani di ripristino tramite PowerShell.
