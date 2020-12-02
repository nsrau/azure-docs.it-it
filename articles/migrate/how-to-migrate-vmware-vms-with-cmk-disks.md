---
title: Eseguire la migrazione di macchine virtuali VMware in Azure con la crittografia lato server (SSE) e le chiavi gestite dal cliente (CMK) usando la migrazione di Azure Migrate server
description: Informazioni su come eseguire la migrazione di macchine virtuali VMware in Azure con la crittografia lato server (SSE) e le chiavi gestite dal cliente (CMK) con Azure Migrate migrazione del server
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: 89b795011460ee2803d07d14b9c2abe9c8ffca55
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483352"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Eseguire la migrazione di macchine virtuali VMware alle macchine virtuali di Azure abilitate con la crittografia lato server e le chiavi gestite dal cliente

Questo articolo descrive come eseguire la migrazione di VM VMware in macchine virtuali di Azure con dischi crittografati usando la crittografia lato server (SSE) con chiavi gestite dal cliente (CMK), usando Azure Migrate migrazione del server (replica senza agenti).

L'esperienza del portale per la migrazione di Azure Migrate Server consente [di eseguire la migrazione di macchine virtuali VMware in Azure con la replica senza agente.](tutorial-migrate-vmware.md) L'esperienza del portale attualmente non offre la possibilità di attivare SSE con CMK per i dischi replicati in Azure. La possibilità di attivare SSE con CMK per i dischi replicati è attualmente disponibile solo tramite l'API REST. Questo articolo illustra come creare e distribuire un [modello di Azure Resource Manager](../azure-resource-manager/templates/overview.md) per replicare una macchina virtuale VMware e configurare i dischi replicati in Azure per usare SSE con CMK.

Gli esempi in questo articolo usano [Azure PowerShell](/powershell/azure/new-azureps-module-az) per eseguire le attività necessarie per creare e distribuire il modello di gestione risorse.

[Altre](../virtual-machines/disk-encryption.md) informazioni sulla crittografia lato server (SSE) con chiavi gestite dal cliente (CMK) per Managed Disks.

## <a name="prerequisites"></a>Prerequisiti

- Per informazioni sui requisiti dello strumento, [vedere l'esercitazione](tutorial-migrate-vmware.md) sulla migrazione di macchine virtuali VMware in Azure con replica senza agente.
- [Seguire queste istruzioni](./create-manage-projects.md) per creare un progetto Azure migrate e aggiungere lo strumento di **migrazione Azure migrate: server** al progetto.
- [Seguire queste istruzioni](how-to-set-up-appliance-vmware.md) per configurare l'appliance Azure migrate per VMware nell'ambiente locale e completare l'individuazione.

## <a name="prepare-for-replication"></a>Preparare per la replica

Al termine dell'individuazione della macchina virtuale, la riga server individuati nel riquadro migrazione server visualizzerà il numero di macchine virtuali VMware individuate dall'appliance.

Prima di poter avviare la replica di macchine virtuali, è necessario preparare l'infrastruttura di replica.

1. Creare un'istanza del bus di servizio nell'area di destinazione. Il bus di servizio viene usato dal dispositivo Azure Migrate locale per comunicare con il servizio migrazione server per coordinare la replica e la migrazione.
2. Creare un account di archiviazione per il trasferimento dei log delle operazioni dalla replica.
3. Creare un account di archiviazione in cui il dispositivo Azure Migrate caricherà i dati di replica.
4. Creare una Key Vault e configurare la Key Vault per gestire i token di firma di accesso condiviso per l'accesso BLOB negli account di archiviazione creati nel passaggio 3 e 4.
5. Generare un token di firma di accesso condiviso per il bus di servizio creato nel passaggio 1 e creare un segreto per il token nel Key Vault creato nel passaggio precedente.
6. Creare un criterio di accesso Key Vault per concedere al dispositivo Azure Migrate locale (usando l'app AAD Appliance) e al servizio migrazione server l'accesso al Key Vault.
7. Creare un criterio di replica e configurare il servizio migrazione server con i dettagli dell'infrastruttura di replica creata nel passaggio precedente.

L'infrastruttura di replica deve essere creata nell'area di Azure di destinazione per la migrazione e nella sottoscrizione di Azure di destinazione in cui viene eseguita la migrazione delle macchine virtuali.

L'esperienza del portale per la migrazione dei server semplifica la preparazione dell'infrastruttura di replica eseguendo automaticamente questa operazione quando si esegue la replica di una macchina virtuale per la prima volta in un progetto. In questo articolo si presuppone che sia già stata eseguita la replica di una o più macchine virtuali con l'esperienza del portale e che l'infrastruttura di replica sia già stata creata. Si esaminerà come individuare i dettagli dell'infrastruttura di replica esistente e come usare questi dettagli come input per il modello di Gestione risorse che verrà usato per configurare la replica con CMK.

### <a name="identifying-replication-infrastructure-components"></a>Identificazione dei componenti dell'infrastruttura di replica

1. Nella portale di Azure, passare alla pagina gruppi di risorse e selezionare il gruppo di risorse in cui è stato creato il progetto Azure Migrate.
2. Selezionare **distribuzioni** dal menu a sinistra e cercare un nome di distribuzione che inizia con la stringa *"Microsoft. MigrateV2. VMwareV2EnableMigrate"*. Verrà visualizzato un elenco di modelli di Gestione risorse creati dall'esperienza del portale per configurare la replica per le macchine virtuali in questo progetto. Un modello di questo tipo verrà scaricato e usato come base per preparare il modello per la replica con CMK.
3. Per scaricare il modello, selezionare una distribuzione corrispondente al modello di stringa nel passaggio precedente > scegliere **modello** dal menu a sinistra > fare clic su **Scarica** nel menu in alto. Salvare il template.jsnel file localmente. Il file di modello verrà modificato nell'ultimo passaggio.

## <a name="create-a-disk-encryption-set"></a>Creare un set di crittografia del disco

Un oggetto del set di crittografia del disco viene mappato Managed Disks a un Key Vault che contiene il CMK da usare per la crittografia SSE. Per replicare le macchine virtuali con CMK, è necessario creare un set di crittografia del disco e passarlo come input per l'operazione di replica.

Seguire [l'esempio seguente](../virtual-machines/windows/disks-enable-customer-managed-keys-powershell.md) per creare un set di crittografia del disco usando Azure PowerShell. Verificare che il set di crittografia del disco venga creato nella sottoscrizione di destinazione in cui viene eseguita la migrazione delle macchine virtuali e nell'area di Azure di destinazione per la migrazione.

Il set di crittografia del disco può essere configurato per crittografare i dischi gestiti con una chiave gestita dal cliente o per la crittografia doppia con una chiave gestita dal cliente e una chiave della piattaforma. Per usare l'opzione Double Encryption at rest, configurare il set di crittografia del disco come descritto [qui](../virtual-machines/windows/disks-enable-double-encryption-at-rest-powershell.md).

Nell'esempio riportato di seguito il set di crittografia del disco è configurato per l'uso di una chiave gestita dal cliente.

```azurepowershell
$Location = "southcentralus"                           #Target Azure region for migration 
$TargetResourceGroupName = "ContosoMigrationTarget"
$KeyVaultName = "ContosoCMKKV"
$KeyName = "ContosoCMKKey"
$KeyDestination = "Software"
$DiskEncryptionSetName = "ContosoCMKDES"

$KeyVault = New-AzKeyVault -Name $KeyVaultName -ResourceGroupName $TargetResourceGroupName -Location $Location -EnableSoftDelete -EnablePurgeProtection

$Key = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KeyName -Destination $KeyDestination

$desConfig = New-AzDiskEncryptionSetConfig -Location $Location -SourceVaultId $KeyVault.ResourceId -KeyUrl $Key.Key.Kid -IdentityType SystemAssigned

$des = New-AzDiskEncryptionSet -Name $DiskEncryptionSetName -ResourceGroupName $TargetResourceGroupName -InputObject $desConfig

Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get

New-AzRoleAssignment -ResourceName $KeyVaultName -ResourceGroupName $TargetResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader"
```

## <a name="get-details-of-the-vmware-vm-to-migrate"></a>Ottenere i dettagli della macchina virtuale VMware di cui eseguire la migrazione

In questo passaggio si userà Azure PowerShell per ottenere i dettagli della macchina virtuale di cui deve essere eseguita la migrazione. Questi dettagli verranno usati per costruire il modello di Gestione risorse per la replica. In particolare, le due proprietà di interesse sono:

- ID risorsa del computer per le macchine virtuali individuate.
- L'elenco di dischi per la macchina virtuale e i relativi identificatori del disco.

```azurepowershell

$ProjectResourceGroup = "ContosoVMwareCMK"   #Resource group that the Azure Migrate Project is created in
$ProjectName = "ContosoVMwareCMK"            #Name of the Azure Migrate Project

$solution = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.Migrate/MigrateProjects/solutions -ExpandProperties -ResourceName $ProjectName | where Name -eq "Servers-Discovery-ServerDis
covery"

# Displays one entry for each appliance in the project mapping the appliance to the VMware sites discovered through the appliance.
$solution.Properties.details.extendedDetails.applianceNameToSiteIdMapV2 | ConvertFrom-Json | select ApplianceName, SiteId
```
```Output
ApplianceName  SiteId
-------------  ------
VMwareApplianc /subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite
```

Copiare il valore della stringa SiteId corrispondente all'appliance Azure Migrate in cui viene individuata la macchina virtuale. Nell'esempio illustrato in precedenza, SiteId è *"/subscriptions/509099b2-9d2c-4636-B43E-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/Providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

```azurepowershell

#Replace value with SiteId from the previous step
$SiteId = "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"
$SiteName = Get-AzResource -ResourceId $SiteId -ExpandProperties | Select-Object -ExpandProperty Name
$DiscoveredMachines = Get-AzResource -ResourceGroupName $ProjectResourceGroup -ResourceType Microsoft.OffAzure/VMwareSites/machines  -ExpandProperties -ResourceName $SiteName

#Get machine details
PS /home/bharathram> $MachineName = "FPL-W19-09"     #Replace string with VMware VM name of the machine to migrate
PS /home/bharathram> $machine = $Discoveredmachines | where {$_.Properties.displayName -eq $MachineName}
PS /home/bharathram> $machine.count   #Validate that only 1 VM was found matching this name.
```

Copiare i valori di UUID ResourceId, Name e disk per la macchina da migrare.
```Output
PS > $machine.Name
10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210
PS > $machine.ResourceId
/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210

PS > $machine.Properties.disks | select uuid, label, name, maxSizeInBytes

uuid                                 label       name    maxSizeInBytes
----                                 -----       ----    --------------
6000C291-5106-2aac-7a74-4f33c3ddb78c Hard disk 1 scsi0:0    42949672960
6000C293-39a1-bd70-7b24-735f0eeb79c4 Hard disk 2 scsi0:1    53687091200
6000C29e-cbee-4d79-39c7-d00dd0208aa9 Hard disk 3 scsi0:2    53687091200

```

## <a name="create-resource-manager-template-for-replication"></a>Creare Gestione risorse modello per la replica

- Aprire il file di modello di Gestione risorse scaricato nel passaggio **Identificazione componenti dell'infrastruttura di replica** in un editor di propria scelta.
- Rimuovere tutte le definizioni di risorse dal modello ad eccezione delle risorse di tipo *"Microsoft. RecoveryServices/Vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"*
- Se sono presenti più definizioni di risorse del tipo precedente, rimuovere tutte le definizioni tranne una. Rimuovere tutte le definizioni di proprietà **dependsOn** dalla definizione di risorsa.
- Al termine di questo passaggio, è necessario disporre di un file simile a quello riportato nell'esempio seguente con lo stesso set di proprietà.

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoMigration7371rsvault/VMware104e4replicationfabric/VMware104e4replicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
            "properties": {
                "policyId": "/Subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.RecoveryServices/vaults/ContosoMigration7371rsvault/replicationPolicies/migrateVMware104e4sitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_500937f3-805e-9414-11b1-f22923456e08",
                    "targetResourceGroupId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG",
                    "targetNetworkId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/PayrollRG/providers/Microsoft.Network/virtualNetworks/PayrollNW",
                    "targetSubnetName": "PayrollSubnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C295-dafe-a0eb-906e-d47cb5b05a1d",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                            "logStorageAccountSasSecretName": "migratelsa1432469187-cacheSas",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.OffAzure/VMwareSites/VMware104e4site/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/6785ea1f-ac40-4244-a9ce-94b12fd832ca/resourceGroups/ContosoMigration/providers/Microsoft.Storage/storageAccounts/migratelsa1432469187",
                    "targetVmName": "PayrollWeb04"
                }
            }
        }
    ]
}
```

- Modificare la proprietà **Name** nella definizione di risorsa. Sostituire la stringa che segue l'ultimo "/" nella proprietà Name con il valore *$Machine. Nome*(dal passaggio precedente).
- Modificare il valore della proprietà **Properties. providerSpecificDetails. vmwareMachineId** con il valore *$Machine. ResourceId*(dal passaggio precedente).
- Impostare i valori per **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** rispettivamente sull'ID del gruppo di risorse di destinazione, sull'ID di risorsa della rete virtuale di destinazione e sul nome della subnet di destinazione.
- Impostare il valore di **licenseType** su "WindowsServer" per applicare vantaggio Azure Hybrid per questa macchina virtuale. Se questa macchina virtuale non è idonea per Vantaggio Azure Hybrid, impostare il valore di **licenseType** su NoLicenseType.
- Modificare il valore della proprietà **targetVmName** con il nome della macchina virtuale di Azure desiderato per la VM di cui è stata eseguita la migrazione.
- Facoltativamente, aggiungere una proprietà denominata **targetVmSize** sotto la proprietà **targetVmName** . Impostare il valore della proprietà **targetVmSize** sulle dimensioni di macchina virtuale di Azure desiderate per la macchina virtuale di cui è stata eseguita la migrazione.
- La proprietà **disksToInclude** è un elenco di input del disco per la replica con ogni elemento dell'elenco che rappresenta un disco locale. Creare tutti gli elementi di elenco come numero di dischi nella macchina virtuale locale. Sostituire la proprietà **DiskID** nell'elemento dell'elenco con l'UUID dei dischi identificati nel passaggio precedente. Impostare il valore **isOSDisk** su "true" per il disco del sistema operativo della macchina virtuale e "false" per tutti gli altri dischi. Lasciare invariate le proprietà **logStorageAccountId** e **logStorageAccountSasSecretName** . Impostare il valore **diskType** sul tipo di disco gestito di Azure (*Standard_LRS, Premium_LRS, StandardSSD_LRS*) da usare per il disco. Per i dischi che devono essere crittografati con CMK, aggiungere una proprietà denominata **diskEncryptionSetId** e impostare il valore sull'ID risorsa del set di crittografia del disco creato (**$des. ID**) nel passaggio *creare un set di crittografia del disco*
- Salvare il file di modello modificato. Per l'esempio precedente, il file modello modificato è simile al seguente:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems",
            "apiVersion": "2018-01-10",
            "name": "ContosoVMwareCMK00ddrsvault/VMwareApplianca8bareplicationfabric/VMwareApplianca8bareplicationcontainer/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
            "properties": {
                "policyId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.RecoveryServices/vaults/ContosoVMwareCMK00ddrsvault/replicationPolicies/migrateVMwareApplianca8basitepolicy",
                "providerSpecificDetails": {
                    "instanceType": "VMwareCbt",
                    "vmwareMachineId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/machines/10-150-8-52-b090bef3-b733-5e34-bc8f-eb6f2701432a_50098f99-f949-22ca-642b-724ec6595210",
                    "targetResourceGroupId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoMigrationTarget",
                    "targetNetworkId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/cmkRTest/providers/Microsoft.Network/virtualNetworks/cmkvm1_vnet",
                    "targetSubnetName": "cmkvm1_subnet",
                    "licenseType": "NoLicenseType",
                    "disksToInclude": [
                        {
                            "diskId": "6000C291-5106-2aac-7a74-4f33c3ddb78c",
                            "isOSDisk": "true",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C293-39a1-bd70-7b24-735f0eeb79c4",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        },
                        {
                            "diskId": "6000C29e-cbee-4d79-39c7-d00dd0208aa9",
                            "isOSDisk": "false",
                            "logStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                            "logStorageAccountSasSecretName": "migratelsa1671875959-cacheSas",
                            "diskEncryptionSetId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/CONTOSOMIGRATIONTARGET/providers/Microsoft.Compute/diskEncryptionSets/ContosoCMKDES",
                            "diskType": "Standard_LRS"
                        }
                    ],
                    "dataMoverRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "snapshotRunAsAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite/runasaccounts/b090bef3-b733-5e34-bc8f-eb6f2701432a",
                    "targetBootDiagnosticsStorageAccountId": "/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.Storage/storageAccounts/migratelsa1671875959",
                    "performAutoResync": "true",
                    "targetVmName": "FPL-W19-09"
                }
            }
        }
    ]
}
```

## <a name="set-up-replication"></a>Configurare la replica

È ora possibile distribuire il modello di Gestione risorse modificato al gruppo di risorse del progetto per configurare la replica per la macchina virtuale. Informazioni su come [distribuire risorse con modelli di Azure Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName $ProjectResourceGroup -TemplateFile "C:\Users\Administrator\Downloads\template.json"
```

```Output
DeploymentName          : template
ResourceGroupName       : ContosoVMwareCMK
ProvisioningState       : Succeeded
Timestamp               : 3/11/2020 8:52:00 PM
Mode                    : Incremental
TemplateLink            :
Parameters              :
Outputs                 :
DeploymentDebugLogLevel :

```

## <a name="next-steps"></a>Passaggi successivi

[Monitorare](tutorial-migrate-vmware.md#track-and-monitor) lo stato della replica tramite l'esperienza del portale ed eseguire migrazioni di test e migrazione.