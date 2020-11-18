---
title: Eseguire la migrazione di VM VMware ad Azure (senza agente) - PowerShell
description: Informazioni su come eseguire una migrazione senza agente di VM VMware con Azure Migrate tramite PowerShell.
services: ''
author: rahugup
manager: bsiva
ms.topic: tutorial
ms.date: 10/1/2020
ms.author: rahugup
ms.openlocfilehash: 185979fcc0eeaebbe1c3b09d74050e05899737af
ms.sourcegitcommit: 0d171fe7fc0893dcc5f6202e73038a91be58da03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2020
ms.locfileid: "93376800"
---
# <a name="migrate-vmware-vms-to-azure-agentless---powershell"></a>Eseguire la migrazione di VM VMware ad Azure (senza agente) - PowerShell

Questo articolo illustra come eseguire la migrazione di macchine virtuali VMware individuate con il metodo senza agente usando Azure PowerShell per [Azure Migrate: Migrazione server](migrate-services-overview.md#azure-migrate-server-migration-tool). 

Si apprenderà come:

> [!div class="checklist"]
> * Recuperare le VM VMware individuate in un progetto di Azure Migrate.
> * Avviare la replica delle VM.
> * Aggiornare le proprietà per la replica delle VM.
> * Monitorare la replica.
> * Eseguire una migrazione di test per verificare che tutti gli elementi funzionino come previsto.
> * Eseguire una migrazione completa delle VM.

> [!NOTE]
> Le esercitazioni illustrano il percorso di distribuzione più semplice per uno scenario, per consentire di configurare rapidamente un modello di verifica. Quando possibile vengono usate le opzioni predefinite e non sono riportati tutti i percorsi e le impostazioni possibili.

Se non si ha una sottoscrizione di Azure, creare un [account gratuito](https://azure.microsoft.com/pricing/free-trial/) prima di iniziare.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare questa esercitazione, è necessario:

1. [Completare l'esercitazione sull'individuazione](tutorial-discover-vmware.md) per preparare Azure e VMware per la migrazione.
2. È consigliabile completare la seconda esercitazione per [valutare le macchine virtuali VMware](tutorial-assess-vmware.md) prima di eseguirne la migrazione ad Azure.
3. Avere a disposizione il modulo `Az` di Azure PowerShell. Se è necessario installare o aggiornare Azure PowerShell, vedere questa [guida all'installazione e alla configurazione di Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="install-azure-migrate-powershell-module"></a>Installare il modulo Azure Migrate di PowerShell

Il modulo Azure Migrate di PowerShell è disponibile in anteprima. È necessario installare il modulo di PowerShell usando il comando seguente. 

```azurepowershell
Install-Module -Name Az.Migrate 
```

## <a name="sign-in-to-your-microsoft-azure-subscription"></a>Accedere alla sottoscrizione di Microsoft Azure

Accedere alla sottoscrizione di Azure con il cmdlet `Connect-AzAccount`.

```azurepowershell
Connect-AzAccount
```

Selezionare la sottoscrizione di Azure. Usare il cmdlet `Get-AzSubscription` per ottenere l'elenco delle sottoscrizioni di Azure a cui si ha accesso. Selezionare la sottoscrizione di Azure con il progetto Azure Migrate da usare eseguendo il cmdlet `Set-AzContext`.

```azurepowershell
Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
```

## <a name="retrieve-the-azure-migrate-project"></a>Recuperare il progetto Azure Migrate

Il progetto Azure Migrate viene usato per archiviare i metadati di individuazione, valutazione e migrazione raccolti dall'ambiente da valutare o trasferire.
In un progetto è possibile tenere traccia degli asset individuati, orchestrare le valutazioni ed eseguire le migrazioni.

Come parte dei prerequisiti, sarà già stato creato un progetto Azure Migrate. Usare il cmdlet `Get-AzMigrateProject` per recuperare i dettagli di un progetto Azure Migrate. È necessario specificare il nome del progetto Azure Migrate (`Name`) e il nome del relativo gruppo di risorse (`ResourceGroupName`).

```azurepowershell
# Get resource group of the Azure Migrate project
$ResourceGroup = Get-AzResourceGroup -Name "MyResourceGroup"

# Get details of the Azure Migrate project
$MigrateProject = Get-AzMigrateProject -Name "MyMigrateProject" -ResourceGroupName $ResourceGroup.ResourceGroupName

# View Azure Migrate project details
$MigrateProject | ConvertTo-JSON
```

## <a name="retrieve-discovered-vms-in-an-azure-migrate-project"></a>Recuperare le VM individuate in un progetto Azure Migrate

Azure Migrate si basa su un'[appliance leggera di Azure Migrate](migrate-appliance-architecture.md). Come parte dei prerequisiti, l'appliance di Azure Migrate sarà già stata distribuita come VM VMware.

Per recuperare una VM VMware specifica in un progetto Azure Migrate, specificare il nome del progetto Azure Migrate (`ProjectName`), il relativo gruppo di risorse (`ResourceGroupName`) e il nome della macchina virtuale (`DisplayName`). 

> [!NOTE]
> **Il valore del parametro della macchina virtuale (`DisplayName`) distingue tra maiuscole e minuscole**.

```azurepowershell
# Get a specific VMware VM in an Azure Migrate project
$DiscoveredServer = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -DisplayName "MyTestVM"

# View discovered server details
$DiscoveredServer | ConvertTo-JSON
```
In questa esercitazione verrà eseguita la migrazione di questa VM.

È anche possibile recuperare tutte le VM VMware in un progetto Azure Migrate usando i parametri `ProjectName` e `ResourceGroupName`.

```azurepowershell
# Get all VMware VMs in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName 
```
Se un progetto Azure Migrate include più appliance, è possibile usare i parametri `ProjectName`, `ResourceGroupName`e `ApplianceName` per recuperare tutte le macchine virtuali individuate con una specifica appliance. 

```azurepowershell
# Get all VMware VMs discovered by an Azure Migrate Appliance in an Azure Migrate project
$DiscoveredServers = Get-AzMigrateDiscoveredServer -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName -ApplianceName "MyMigrateAppliance" |Format-Table DisplayName, Name, Type

```

## <a name="initialize-replication-infrastructure"></a>Inizializzare l'infrastruttura di replica

[Azure Migrate: Migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool) sfrutta più risorse di Azure per la migrazione delle macchine virtuali. Migrazione del server effettua il provisioning delle risorse seguenti nello stesso gruppo di risorse del progetto.

- **Bus di servizio**: Migrazione del server usa il bus di servizio per inviare i messaggi di orchestrazione della replica all'appliance.
- **Account di archiviazione del gateway**: lo strumento Migrazione server usa l'account di archiviazione del gateway per archiviare le informazioni sullo stato delle VM da replicare.
- **Account di archiviazione di log**: l'appliance di Azure Migrate carica i log di replica per le VM in un account di archiviazione di log. Azure Migrate applica le informazioni di replica ai dischi gestiti dalla replica.
- **Insieme di credenziali delle chiavi**: l'appliance di Azure Migrate usa l'insieme di credenziali delle chiavi per gestire le stringhe di connessione per il bus di servizio e le chiavi di accesso per gli account di archiviazione usati nella replica.

Prima di replicare la prima VM nel progetto Azure Migrate, eseguire lo script seguente per effettuare il provisioning dell'infrastruttura di replica. Questo script effettua il provisioning delle suddette risorse e le configura in modo che sia possibile avviare la migrazione delle VM VMware.

> [!NOTE]
> Un progetto Azure Migrate supporta le migrazioni solo in un'unica area di Azure. Una volta eseguito questo script, non è possibile cambiare l'area di destinazione della migrazione delle VM VMware.
> Se si configura una nuova appliance nel progetto Azure Migrate, sarà necessario eseguire lo script `Initialize-AzMigrateReplicationInfrastructure`. 

Nell'articolo verrà inizializzata l'infrastruttura di replica in modo che sia possibile eseguire la migrazione delle VM nell'area `Central US`. È possibile [scaricare il file](https://github.com/Azure/azure-docs-powershell-samples/tree/master/azure-migrate/migrate-at-scale-vmware-agentles) dal repository GitHub o eseguirlo usando il frammento di codice seguente. 

```azurepowershell
# Download the script from Azure Migrate GitHub repository 
Invoke-WebRequest https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-migrate/migrate-at-scale-vmware-agentles/Initialize-AzMigrateReplicationInfrastructure.ps1 -OutFile .\AzMigrateReplicationinfrastructure.ps1

# Run the script for initializing replication infrastructure for the current Migrate project
.\AzMigrateReplicationInfrastructure.ps1 -ResourceGroupName $ResourceGroup.ResourceGroupName -ProjectName $MigrateProject.Name -Scenario agentlessVMware -TargetRegion "CentralUS" 
```


## <a name="replicate-vms"></a>Replicare le VM

Dopo aver completato l'individuazione e l'inizializzazione dell'infrastruttura di replica, è possibile avviare la replica di macchine virtuali VMware ad Azure. È possibile eseguire fino a 300 repliche contemporaneamente.

È possibile specificare le proprietà di replica come indicato di seguito.

- **Sottoscrizione e gruppo di risorse di destinazione**: specificare la sottoscrizione e il gruppo di risorse in cui eseguire la migrazione della VM fornendo l'ID gruppo di risorse con il parametro `TargetResourceGroupId`. 
- **Rete virtuale e subnet di destinazione**: specificare l'ID della rete virtuale di Azure e il nome della subnet in cui eseguire la migrazione della VM usando rispettivamente i parametri `TargetNetworkId` e `TargetSubnetName`. 
- **Nome della macchina virtuale di destinazione**: specificare il nome della macchina virtuale di Azure da creare usando il parametro `TargetVMName`.
- **Dimensioni della macchina virtuale di destinazione**: specificare le dimensioni della macchina virtuale di Azure da usare per la macchina virtuale di replica usando il parametro `TargetVMSize`. Ad esempio, per eseguire la migrazione di una VM a una VM D2_v2 in Azure, specificare il valore "Standard_D2_v2" per `TargetVMSize`.  
- **Licenza**: per usare Vantaggio Azure Hybrid per i computer Windows Server coperti da sottoscrizioni attive di Software Assurance o Windows Server, specificare il valore "WindowsServer" per il parametro `LicenseType`. In caso contrario, specificare il valore "NoLicenseType" per il parametro `LicenseType`.
- **Disco del sistema operativo**: specificare l'identificatore univoco del disco che contiene il bootloader e il programma di installazione del sistema operativo. L'ID disco da usare è la proprietà UUID (identificatore univoco) per il disco recuperato tramite il cmdlet `Get-AzMigrateServer`.
- **Tipo di disco**: specificare il valore per il parametro `DiskType` come indicato di seguito.
    - Per usare i dischi gestiti Premium, specificare "Premium_LRS" come valore per il parametro `DiskType`. 
    - Per usare dischi SSD Standard, specificare "StandardSSD_LRS" come valore per il parametro `DiskType`. 
    - Per usare dischi HHD Standard, specificare "Standard_LRS" come valore per il parametro `DiskType`. 
- **Ridondanza dell'infrastruttura**: specificare l'opzione di ridondanza dell'infrastruttura come indicato di seguito. 
    - Zona di disponibilità per aggiungere la macchina migrata a una zona di disponibilità specifica nell'area. Usare questa opzione per distribuire i server che formano un livello applicazione a più nodi tra zone di disponibilità. Questa opzione è disponibile solo se l'area di destinazione selezionata per la migrazione supporta le zone di disponibilità. Per usare le zone di disponibilità, specificare il relativo valore per il parametro `TargetAvailabilityZone`.
    - Set di disponibilità per inserire la macchina migrata in un set di disponibilità. Per usare questa opzione, il gruppo di risorse di destinazione selezionato deve avere uno o più set di disponibilità. Per usare il set di disponibilità, specificare il relativo ID per il parametro `TargetAvailabilitySet`. 

### <a name="replicate-vms-with-all-disks"></a>Replicare le VM con tutti i dischi
In questa esercitazione verranno replicati tutti i dischi della macchina virtuale individuata e verrà specificato un nuovo nome per la macchina virtuale in Azure. Il primo disco del server individuato viene specificato come disco del sistema operativo e viene eseguita la migrazione di tutti i dischi come HDD Standard. È il disco che contiene il bootloader e il programma di installazione del sistema operativo. Il cmdlet restituisce un processo di cui è possibile tenere traccia per monitorare lo stato dell'operazione. 

```azurepowershell
# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for a discovered VM in an Azure Migrate project
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -OSDiskID $DiscoveredServer.Disk[0].Uuid -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -DiskType "Standard_LRS" -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

### <a name="replicate-vms-with-select-disks"></a>Replicare VM con dischi selezionati
È anche possibile replicare in modo selettivo i dischi della macchina virtuale individuata usando il cmdlet `New-AzMigrateDiskMapping` e fornendo il disco come input del parametro `DiskToInclude` nel cmdlet `New-AzMigrateServerReplication`. È anche possibile usare il cmdlet `New-AzMigrateDiskMapping` per specificare tipi di dischi di destinazione diversi per ogni singolo disco da replicare. 

Specificare i valori per i parametri seguenti del cmdlet `New-AzMigrateDiskMapping`.

- **DiskId**: specificare l'identificatore univoco del disco di cui eseguire la migrazione. L'ID disco da usare è la proprietà UUID (identificatore univoco) per il disco recuperato tramite il cmdlet `Get-AzMigrateServer`.  
- **IsOSDisk**: specificare "true" se il disco di cui eseguire la migrazione è il disco del sistema operativo della VM; in caso contrario, "false".
- **DiskType**: specificare il tipo di disco da usare in Azure. 

Nell'esempio seguente verranno replicati solo due dischi della macchina virtuale individuata. Si specificherà il disco del sistema operativo e si useranno tipi di dischi diversi per ogni disco da replicare. Il cmdlet restituisce un processo di cui è possibile tenere traccia per monitorare lo stato dell'operazione. 

```azurepowershell
# View disk details of the discovered server
$DiscoveredServer.Disk | ConvertTo-JSON

# Create a new disk mapping for the disks to be replicated
$DisksToReplicate = @()
$OSDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[0].Uuid -DiskType "StandardSSD_LRS" -IsOSDisk "true"
$DataDisk = New-AzMigrateDiskMapping -DiskID $DiscoveredServer.Disk[1].Uuid -DiskType "Premium_LRS" -IsOSDisk "false"

$DisksToReplicate += $OSDisk
$DisksToReplicate += $DataDisk 

# Retrieve the resource group that you want to migrate to
$TargetResourceGroup = Get-AzResourceGroup -Name "MyTargetResourceGroup"

# Retrieve the Azure virtual network and subnet that you want to migrate to
$TargetVirtualNetwork = Get-AzVirtualNetwork -Name "MyVirtualNetwork"

# Start replication for the VM
$MigrateJob =  New-AzMigrateServerReplication -InputObject $DiscoveredServer -TargetResourceGroupId $TargetResourceGroup.ResourceId -TargetNetworkId $TargetVirtualNetwork.Id -LicenseType "NoLicenseType" -DiskToInclude $DisksToReplicate -TargetSubnetName $TargetVirtualNetwork.Subnets[0].Name -TargetVMName "MyMigratedTestVM" -TargetVMSize "Standard_DS2_v2"

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="monitor-replication"></a>Monitorare la replica 

La replica avviene nel modo indicato di seguito:

- Al termine del processo, viene avviata la replica iniziale delle macchine virtuali in Azure.
- Durante la replica iniziale viene creato uno snapshot della VM. I dati dei dischi dello snapshot vengono replicati nei dischi gestiti di replica in Azure.
- Al termine della replica iniziale, viene avviata la replica differenziale. Le modifiche incrementali ai dischi locali vengono replicate periodicamente nei dischi di replica in Azure.

Per tenere traccia dello stato della replica, usare il cmdlet `Get-AzMigrateServerReplication`. 

> [!NOTE]
> L'ID della VM individuata e l'ID della VM di replica sono due identificatori univoci diversi. Entrambi gli identificatori possono essere usati per recuperare i dettagli di un server di replica.  

### <a name="monitor-replication-using-discovered-vm-identifier"></a>Monitorare la replica usando l'identificatore della VM individuata
```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID
```

### <a name="monitor-replication-using-replicating-vm-identifier"></a>Monitorare la replica usando l'identificatore della VM di replica

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```

È possibile tenere traccia delle proprietà "MigrationState" e "MigrationStateDescription" nell'output. 
- Per la replica iniziale, i valori per tali proprietà saranno rispettivamente "InitialSeedingInProgress" e "Initial replication". 
- Durante la replica differenziale, i valori per tali proprietà saranno rispettivamente "Replicating" e "Ready to migrate".
- Una volta completata la migrazione, i valori per tali proprietà saranno rispettivamente "Migration succeeded" e "Migrated".

```Output
AllowedOperation            : {DisableMigration, TestMigrate, Migrate}
CurrentJobId                : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationJobs/None
CurrentJobName              : None
CurrentJobStartTime         : 1/1/1753 1:01:01 AM
EventCorrelationId          : 9d435c55-4660-41a5-a8ed-dd74213d85fa
Health                      : Normal
HealthError                 : {}
Id                          : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationFabrics/xxx/replicationProtectionContainers/xxx/
                              replicationMigrationItems/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-
                              39b2-1e14-f90584522703
LastTestMigrationStatus     :
LastTestMigrationTime       :
Location                    :
MachineName                 : MyTestVM
MigrationState              : InitialSeedingInProgress
MigrationStateDescription   : Initial replication
Name                        : 10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_5009e941-3e40-39b2-1e14-f90584522703
PolicyFriendlyName          : xxx
PolicyId                    : /Subscriptions/xxx/resourceGroups/xxx/providers/Micr
                              osoft.RecoveryServices/vaults/xxx/replicationPolicies/xxx
ProviderSpecificDetail      : Microsoft.Azure.PowerShell.Cmdlets.Migrate.Models.Api20180110.VMwareCbtMigrationDetails
TestMigrateState            : None
TestMigrateStateDescription : None
Type                        : Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems
```

Per informazioni dettagliate sullo stato di avanzamento della replica, eseguire il cmdlet seguente.

```azurepowershell
$replicatingserver.ProviderSpecificDetail | convertto-json
```
Per tenere traccia dello stato di avanzamento della replica iniziale, è possibile usare le proprietà "InitialSeedingProgressPercentage" nell'output.

```output
    "DataMoverRunAsAccountId": "/subscriptions/xxx/resourceGroups/xxx/providers/Microsoft.OffAzure/VMwareSites/xxx/runasaccounts/xxx",
    "FirmwareType":  "BIOS",
    "InitialSeedingProgressPercentage": 20,
    "InstanceType":  "VMwareCbt",
    "LastRecoveryPointReceived":  "\/Date(1601733591427)\/",
    "LicenseType":  "NoLicenseType",
    "MigrationProgressPercentage":  null,
    "MigrationRecoveryPointId":  null,
    "OSType":  "Windows",
    "PerformAutoResync":  "true",
```

La replica avviene nel modo indicato di seguito:

- Al termine del processo, viene avviata la replica iniziale delle macchine virtuali in Azure.
- Durante la replica iniziale viene creato uno snapshot della VM. I dati dei dischi dello snapshot vengono replicati nei dischi gestiti di replica in Azure.
- Al termine della replica iniziale, viene avviata la replica differenziale. Le modifiche incrementali ai dischi locali vengono replicate periodicamente nei dischi di replica in Azure.

## <a name="retrieve-the-status-of-a-job"></a>Recuperare lo stato di un processo

È possibile monitorare stato di un processo usando il cmdlet `Get-AzMigrateJob`. 

```azurepowershell
# Retrieve the updated status for a job
$job = Get-AzMigrateJob -InputObject $job
```

## <a name="update-properties-of-a-replicating-vm"></a>Aggiornare le proprietà di una VM di replica

[Azure Migrate: Migrazione del server](migrate-services-overview.md#azure-migrate-server-migration-tool) consente di cambiare le proprietà di destinazione, ad esempio nome, dimensioni, gruppo di risorse, configurazione della scheda di interfaccia di retee così via, per una macchina virtuale di replica. Il cmdlet restituisce un processo di cui è possibile tenere traccia per monitorare lo stato dell'operazione. 

```azurepowershell
# Retrieve the replicating VM details by using the discovered VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -DiscoveredMachineId $DiscoveredServer.ID

# View NIC details of the replicating server
Write-Output $ReplicatingServer.ProviderSpecificDetail.VMNic 
```
Per una VM è possibile aggiornare le proprietà seguenti.

- **Nome della macchina virtuale**: specificare il nome della macchina virtuale di Azure da creare usando il parametro `TargetVMName`.
- **Dimensioni della macchina virtuale**: specificare le dimensioni della macchina virtuale di Azure da usare per la macchina virtuale di replica usando il parametro `TargetVMSize`. Ad esempio, per eseguire la migrazione di una VM a una VM D2_v2 in Azure, specificare il valore "Standard_D2_v2" per `TargetVMSize`.  
- **Rete virtuale**: specificare l'ID della rete virtuale di Azure in cui eseguire la migrazione della VM usando il parametro `TargetNetworkId`. 
- **Gruppo di risorse**: specificare l'ID del gruppo di risorse in cui eseguire la migrazione della VM usando il parametro `TargetResourceGroupId`.
- **Interfaccia di rete**: è possibile specificare la configurazione della scheda di interfaccia di rete usando il cmdlet `New-AzMigrateNicMapping`. L'oggetto viene quindi passato come input del parametro `NicToUpdate` nel cmdlet `Set-AzMigrateServerReplication`. 

    - **Cambiare l'allocazione di indirizzi IP**: per specificare un indirizzo IP statico per una scheda di interfaccia di rete, specificare un indirizzo IPv4 da usare come IP statico per la VM usando il parametro `TargetNicIP`. Per assegnare dinamicamente un indirizzo IP per una scheda di interfaccia di rete, specificare "auto" come valore del parametro `TargetNicIP`.
    - Usare i valori "Primary", "Secondary" o "DoNotCreate" per il parametro `TargetNicSelectionType` per specificare se la scheda di interfaccia di rete deve essere primaria o secondaria oppure se non deve essere creata nella macchina virtuale di cui eseguire la migrazione. Per la VM è possibile specificare un'unica scheda di interfaccia di rete come primaria. 
    - Per impostare la scheda di interfaccia di rete come primaria, è anche necessario specificare altre schede che dovranno essere impostate come secondarie o che non dovranno essere create nella VM di cui eseguire la migrazione.  
    - Per cambiare la subnet della scheda di interfaccia di rete, specificare il relativo nome usando il parametro `TargetNicSubnet`.

 - **Zona di disponibilità**: per usare le zone di disponibilità, specificare il relativo valore per il parametro `TargetAvailabilityZone`.
 - **Set di disponibilità**: per usare le zone di disponibilità, specificare il relativo ID per il parametro `TargetAvailabilitySet`.

Nell'esempio seguente la configurazione della scheda di interfaccia di rete verrà aggiornata impostando la prima come primaria e assegnandole un indirizzo IP statico. La seconda scheda di interfaccia di rete verrà rimossa per la migrazione e verranno aggiornati il nome e le dimensioni della VM. 

```azurepowershell
# Specify the NIC properties to be updated for a replicating VM. 
$NicMapping = @()
$NicMapping1 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[0].NicId -TargetNicIP "xxx.xxx.xxx.xxx" -TargetNicSelectionType "Primary"
$NicMapping2 = New-AzMigrateNicMapping -NicId $ReplicatingServer.ProviderSpecificDetail.VMNic[1].NicId -TargetNicSelectionType "DoNotCreate"

$NicMapping += $NicMapping1
$NicMapping += $NicMapping2

# Update the name, size and NIC configuration of a replicating server
$UpdateJob = Set-AzMigrateServerReplication -InputObject $ReplicatingServer -TargetVMSize "Standard_DS13_v2" -TargetVMName "MyMigratedVM" -NicToUpdate $NicMapping

# Track job status to check for completion
while (($UpdateJob.State -eq "InProgress") -or ($UpdateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $UpdateJob = Get-AzMigrateJob -InputObject $UpdateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $UpdateJob.State
```

È anche possibile elencare tutti i server di replica in un progetto Azure Migrate e quindi usare l'identificatore della VM di replica per aggiornare le proprietà della VM.

```azurepowershell
# List all replicating VMs in an Azure Migrate project and filter the result for selecting the replication VM. This cmdlet will not return all properties of the replicating VM.
$ReplicatingServer = Get-AzMigrateServerReplication -ProjectName $MigrateProject.Name -ResourceGroupName $ResourceGroup.ResourceGroupName | where MachineName -eq $DiscoveredServer.DisplayName

# Retrieve replicating VM details using replicating VM identifier
$ReplicatingServer = Get-AzMigrateServerReplication -TargetObjectID $ReplicatingServer.Id 
```


## <a name="run-a-test-migration"></a>Eseguire una migrazione di test

All'avvio della replica differenziale, è possibile eseguire una migrazione di test per le VM prima di eseguire una migrazione completa ad Azure. È consigliabile farlo almeno una volta per ogni macchina virtuale, prima di eseguirne la migrazione. Il cmdlet restituisce un processo di cui è possibile tenere traccia per monitorare lo stato dell'operazione. 

- L'esecuzione di una migrazione di test verifica che tutto funzioni come previsto. La migrazione di test non ha alcun effetto sul computer locale, che rimane operativo e continua a eseguire la replica. 
- Il test simula la migrazione creando una VM di Azure con dati replicati, in genere eseguendo la migrazione a un rete virtuale non di produzione nella sottoscrizione di Azure.
- È possibile usare la VM di Azure di test replicata per convalidare la migrazione, eseguire test delle app e risolvere eventuali problemi prima della migrazione completa.

Selezionare la rete virtuale di Azure da usare per il test specificando il relativo ID con il parametro `TestNetworkID`.

```azurepowershell
# Retrieve the Azure virtual network created for testing 
$TestVirtualNetwork = Get-AzVirtualNetwork -Name MyTestVirtualNetwork

# Start test migration for a replicating server
$TestMigrationJob = Start-AzMigrateTestMigration -InputObject $ReplicatingServer -TestNetworkID $TestVirtualNetwork.Id

# Track job status to check for completion
while (($TestMigrationJob.State -eq "InProgress") -or ($TestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $TestMigrationJob = Get-AzMigrateJob -InputObject $TestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $TestMigrationJob.State
```

Al termine del test, pulire la migrazione di test usando il cmdlet `Start-AzMigrateTestMigrationCleanup`. Il cmdlet restituisce un processo di cui è possibile tenere traccia per monitorare lo stato dell'operazione. 

```azurepowershell
# Clean-up test migration for a replicating server
$CleanupTestMigrationJob = Start-AzMigrateTestMigrationCleanup -InputObject $ReplicatingServer

# Track job status to check for completion
while (($CleanupTestMigrationJob.State -eq "InProgress") -or ($CleanupTestMigrationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $CleanupTestMigrationJob = Get-AzMigrateJob -InputObject $CleanupTestMigrationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $CleanupTestMigrationJob.State
```

## <a name="migrate-vms"></a>Eseguire la migrazione di VM

Dopo aver verificato che la migrazione di test funzioni nel modo previsto, è possibile procedere con la migrazione del server di replica usando il cmdlet seguente. Il cmdlet restituisce un processo di cui è possibile tenere traccia per monitorare lo stato dell'operazione. 

Se non si vuole disattivare il server di origine, non usare il parametro `TurnOffSourceServer`.

```azurepowershell
# Start migration for a replicating server and turn off source server as part of migration
$MigrateJob = Start-AzMigrateServerMigration -InputObject $ReplicatingServer -TurnOffSourceServer 

# Track job status to check for completion
while (($MigrateJob.State -eq "InProgress") -or ($MigrateJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $MigrateJob = Get-AzMigrateJob -InputObject $MigrateJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $MigrateJob.State
```

## <a name="complete-the-migration"></a>Completare la migrazione

1. Al termine della migrazione, arrestare la replica per il computer locale e pulire le informazioni sullo stato di replica per la macchina virtuale usando il cmdlet seguente. Il cmdlet restituisce un processo di cui è possibile tenere traccia per monitorare lo stato dell'operazione. 

```azurepowershell
# Stop replication for a migrated server
$StopReplicationJob = Remove-AzMigrateServerReplication -InputObject $ReplicatingServer 

# Track job status to check for completion
while (($StopReplicationJob.State -eq "InProgress") -or ($StopReplicationJob.State -eq "NotStarted")){
        #If the job hasn't completed, sleep for 10 seconds before checking the job status again
        sleep 10;
        $StopReplicationJob = Get-AzMigrateJob -InputObject $StopReplicationJob
}
#Check if the Job completed successfully. The updated job state of a successfully completed job should be "Succeeded"
Write-Output $StopReplicationJob.State
```

2. Installare l'agente [Windows](../virtual-machines/extensions/agent-windows.md) o [Linux](../virtual-machines/extensions/agent-linux.md) per le VM di Azure sulle macchine virtuali di cui è stata eseguita la migrazione.
3. Apportare nell'app le eventuali modifiche post-migrazione necessarie, come l'aggiornamento delle stringhe di connessione del database e delle configurazioni dei server Web.
4. Eseguire i test di accettazione della migrazione e dell'applicazione finale sull'applicazione migrata ora in esecuzione in Azure.
5. Trasferire il traffico all'istanza della VM di Azure di cui è stata eseguita la migrazione.
6. Rimuovere le macchine virtuali locali dall'inventario delle macchine virtuali locale.
7. Rimuovere le macchine virtuali locali dai processi di backup locali.
8. Aggiornare la documentazione interna con la nuova posizione e il nuovo indirizzo IP delle macchine virtuali di Azure. 

## <a name="post-migration-best-practices"></a>Procedure consigliate dopo la migrazione

- Per una maggiore resilienza:
    - Proteggere i dati eseguendo il backup delle macchine virtuali di Azure con il servizio Backup di Azure. [Altre informazioni](../backup/quick-backup-vm-portal.md)
    - Mantenere i carichi di lavoro in esecuzione e sempre disponibili eseguendo la replica delle macchine virtuali di Azure in un'area secondaria con Site Recovery. [Altre informazioni](../site-recovery/azure-to-azure-tutorial-enable-replication.md)
- Per una maggiore sicurezza:
    - Bloccare e limitare l'accesso del traffico in ingresso con la funzionalità di [amministrazione JIT del Centro sicurezza di Azure](../security-center/security-center-just-in-time.md).
    - Limitare il traffico di rete verso gli endpoint di gestione con la funzionalità [Gruppi di sicurezza di rete](../virtual-network/security-overview.md).
    - Distribuire [Crittografia dischi di Azure](../security/fundamentals/azure-disk-encryption-vms-vmss.md) per garantire la sicurezza dei dischi e proteggere i dati da furti e accessi non autorizzati.
    - Per altre informazioni sulla [protezione delle risorse IaaS](https://azure.microsoft.com/services/virtual-machines/secure-well-managed-iaas/), visitare il [Centro sicurezza di Azure](https://azure.microsoft.com/services/security-center/).
- Per il monitoraggio e la gestione:
-  È consigliabile distribuire [Gestione costi di Azure](../cost-management-billing/cloudyn/overview.md) per monitorare l'utilizzo delle risorse e le spese.



