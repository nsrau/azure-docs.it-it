---
title: Eseguire la migrazione delle macchine virtuali VMware in Azure con crittografia lato server (SSE) e chiavi gestite dal cliente (CMK) tramite migrazione server di migrazione di Azure
description: Informazioni su come eseguire la migrazione delle macchine virtuali VMware in Azure con crittografia lato server (SSE) e chiavi gestite dal cliente (CMK) con la migrazione del server di migrazione di Azure
author: bsiva
ms.service: azure-migrate
ms.manager: carmonm
ms.topic: article
ms.date: 03/12/2020
ms.author: raynew
ms.openlocfilehash: c6b791fda43a018a26204b2b43dc1e581ff3a945
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79269484"
---
# <a name="migrate-vmware-vms-to-azure-vms-enabled-with-server-side-encryption-and-customer-managed-keys"></a>Eseguire la migrazione delle macchine virtuali VMware alle macchine virtuali di Azure abilitate con la crittografia lato server e le chiavi gestite dal clienteMigrate VMware VMware VMs to Azure VMs enabled with server-side encryption and customer-managed keys

In questo articolo viene descritto come eseguire la migrazione di macchine virtuali VMware alle macchine virtuali di Azure con dischi crittografati usando la crittografia lato server (SSE) con chiavi gestite dal cliente (CMK), usando migrazione del server di Azure Migrate (replica senza agente).

L'esperienza del portale di migrazione del server di Azure Migrate consente di eseguire la migrazione delle [macchine virtuali VMware in Azure con la replica senza agente.](tutorial-migrate-vmware.md) The portal experience currently doesn't offer the ability to turn on SSE with CMK for your replicated disks in Azure. La possibilità di attivare SSE con CMK per i dischi replicati è attualmente disponibile solo tramite l'API REST. In questo articolo verrà illustrato come creare e distribuire un modello di [Azure Resource Manager](../azure-resource-manager/templates/overview.md) per replicare una macchina virtuale VMware e configurare i dischi replicati in Azure per l'uso di SSE con CMK.

Gli esempi in questo articolo usano Azure PowerShell per eseguire le attività necessarie per creare e distribuire il modello di Resource Manager.The examples in this article use [Azure PowerShell](/powershell/azure/new-azureps-module-az) to perform the tasks needed to create and deploy the Resource Manager template.

[Ulteriori informazioni](../virtual-machines/windows/disk-encryption.md) sulla crittografia lato server (SSE) con chiavi gestite dal cliente (CMK) per i dischi gestiti.

## <a name="prerequisites"></a>Prerequisiti

- [Esaminare l'esercitazione](tutorial-migrate-vmware.md) sulla migrazione delle macchine virtuali VMware in Azure con la replica senza agente per comprendere i requisiti degli strumenti.
- [Seguire queste istruzioni](how-to-add-tool-first-time.md) per creare un progetto di Azure Migrate e aggiungere lo strumento **Azure Migrate: Server Migration** al progetto.
- [Seguire queste istruzioni](how-to-set-up-appliance-vmware.md) per configurare l'appliance Azure Migrate per VMware nell'ambiente locale e completare l'individuazione.

## <a name="prepare-for-replication"></a>Preparare per la replica

Al termine dell'individuazione delle macchine virtuali, la riga Server individuati nel riquadro Migrazione server mostrerà un conteggio delle macchine virtuali VMware individuate dall'appliance.

Prima di poter avviare la replica delle macchine virtuali, è necessario preparare l'infrastruttura di replica.

1. Creare un'istanza del bus di servizio nell'area di destinazione. Il bus di servizio viene usato dall'appliance di Azure Migrate locale per comunicare con il servizio di migrazione del server per coordinare la replica e la migrazione.
2. Creare un account di archiviazione per il trasferimento dei log delle operazioni dalla replica.
3. Creare un account di archiviazione in cui l'appliance Dischino di Azure carica i dati di replica.
4. Creare un insieme di credenziali delle chiavi e configurare l'insieme di credenziali delle chiavi per gestire i token di firma di accesso condiviso per l'accesso ai BLOB negli account di archiviazione creati nei passaggi 3 e 4.Create a Key Vault and configure the Key Vault to manage shared access signature tokens for blob access on the storage accounts created in step 3 and 4.
5. Generare un token di firma di accesso condiviso per il bus di servizio creato nel passaggio 1 e creare un segreto per il token nell'insieme di credenziali delle chiavi creato nel passaggio precedente.
6. Creare criteri di accesso dell'insieme di credenziali delle chiavi per concedere all'appliance Azure Migrate locale (tramite l'app AAD dell'appliance) e al servizio di migrazione del server l'accesso all'insieme di credenziali delle chiavi.
7. Creare un criterio di replica e configurare il servizio Migrazione server con i dettagli dell'infrastruttura di replica creata nel passaggio precedente.

L'infrastruttura di replica deve essere creata nell'area di Azure di destinazione per la migrazione e nella sottoscrizione di Azure di destinazione in cui viene eseguita la migrazione delle macchine virtuali.

L'esperienza del portale di migrazione server semplifica la preparazione dell'infrastruttura di replica eseguendo automaticamente questa operazione quando si replica una macchina virtuale per la prima volta in un progetto. In questo articolo si presuppone che siano già state replicate una o più macchine virtuali usando l'esperienza del portale e che l'infrastruttura di replica sia già stata creata. Verrà illustrato come individuare i dettagli dell'infrastruttura di replica esistente e come utilizzarli come input per il modello di Resource Manager che verrà utilizzato per impostare la replica con CMK.

### <a name="identifying-replication-infrastructure-components"></a>Identificazione dei componenti dell'infrastruttura di replica

1. Nel portale di Azure passare alla pagina Gruppi di risorse e selezionare il gruppo di risorse in cui è stato creato il progetto Azure Migrate.On the Azure portal, go the resource groups page and select the resource group in which the Azure Migrate project was created.
2. Selezionare **Distribuzioni** dal menu a sinistra e cercare un nome di distribuzione che inizia con la stringa *"Microsoft.MigrateV2.VMwareV2EnableMigrate"*. Verrà visualizzato un elenco di modelli di Resource Manager creati dall'esperienza del portale per configurare la replica per le macchine virtuali in questo progetto. Scaricaremo un modello di questo tipo e lo useremo come base per preparare il modello per la replica con CMK.
3. Per scaricare il modello, selezionare qualsiasi distribuzione corrispondente al modello di stringa nel passaggio precedente > selezionare **Modello** dal menu a sinistra > fare clic su **Download** dal menu in alto. Salvare il file template.json in locale. Questo file modello verrà modificato nell'ultimo passaggio.

## <a name="create-a-disk-encryption-set"></a>Creazione di un set di crittografia del disco

Un oggetto set di crittografia del disco esegue il mapping dei dischi gestiti a un vault chiave contenente la chiave CMK da utilizzare per SSE. Per replicare le macchine virtuali con CMK, è necessario creare un set di crittografia del disco e passarlo come input all'operazione di replica.

Seguire l'esempio seguente per creare un set di crittografia del disco usando Azure PowerShell.Follow the example [here](../virtual-machines/windows/disk-encryption.md#powershell) to create a disk encryption set using Azure PowerShell. Verificare che il set di crittografia del disco venga creato nella sottoscrizione di destinazione in cui viene eseguita la migrazione delle macchine virtuali e nell'area di Azure di destinazione per la migrazione.

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

In questo passaggio si userà Azure PowerShell per ottenere i dettagli della macchina virtuale di cui è necessario eseguire la migrazione. Questi dettagli verranno utilizzati per costruire il modello di Resource Manager per la replica. In particolare, le due proprietà di interesse sono:

- ID risorsa macchina per le macchine virtuali individuate.
- Elenco di dischi per la macchina virtuale e i relativi identificatori di disco.

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

Copiare il valore della stringa SiteId corrispondente all'appliance Azure Migrate tramite cui viene individuata la macchina virtuale. Nell'esempio riportato in precedenza, SiteId è *"/subscriptions/509099b2-9d2c-4636-b43e-bd5cafb6be69/resourceGroups/ContosoVMwareCMK/providers/Microsoft.OffAzure/VMwareSites/VMwareApplianca8basite"*

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

Copiare i valori ResourceId, name e disk uuid per il computer di cui eseguire la migrazione.
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

## <a name="create-resource-manager-template-for-replication"></a>Creare un modello di Resource Manager per la replicaCreate Resource Manager template for replication

- Aprire il file modello di Resource Manager scaricato nel passaggio **Identificazione dei componenti dell'infrastruttura** di replica in un editor di propria scelta.
- Rimuovere tutte le definizioni di risorse dal modello ad eccezione delle risorse di tipo *"Microsoft.RecoveryServices/vaults/replicationFabrics/replicationProtectionContainers/replicationMigrationItems"*
- Se sono presenti più definizioni di risorse del tipo precedente, rimuovere tutte tranne una. Rimuovere tutte le definizioni di proprietà **dependsOn** dalla definizione di risorsa.
- Al termine di questo passaggio, si dovrebbe avere un file che assomiglia all'esempio seguente e ha lo stesso set di proprietà.

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

- Modificare la proprietà **name** nella definizione della risorsa. Sostituire la stringa che segue l'ultimo "/" nella proprietà name con il valore di *$machine. Nome*(dal passaggio precedente).
- Modificare il valore della proprietà **properties.providerSpecificDetails.vmwareMachineId** con il valore di *$machine. ResourceId*(dal passaggio precedente).
- Impostare i valori per **targetResourceGroupId**, **targetNetworkId**, **targetSubnetName** sull'ID del gruppo di risorse di destinazione, l'ID risorsa di rete virtuale di destinazione e il nome della subnet di destinazione rispettivamente.
- Impostare il valore di **licenseType** su "WindowsServer" per applicare il vantaggio Azure Hybrid per questa macchina virtuale. Se questa macchina virtuale non è idonea per il vantaggio Azure Hybrid, impostare il valore di licenseType su NoLicenseType.If this VM is not eligible for Azure Hybrid Benefit, set the value of **licenseType** to NoLicenseType.
- Modificare il valore della proprietà **targetVmName** con il nome della macchina virtuale di Azure desiderato per la macchina virtuale migrata.
- Facoltativamente, aggiungere una proprietà denominata **targetVmSize** sotto la proprietà targetVmName.Optionally add a property named targetVmSize below the **targetVmName** property. Impostare il valore della proprietà **targetVmSize** sulle dimensioni della macchina virtuale di Azure desiderate per la macchina virtuale migrata.
- La proprietà **disksToInclude** è un elenco di input del disco per la replica con ogni elemento dell'elenco che rappresenta un disco locale. Creare un numero di elementi di elenco pari al numero di dischi nella macchina virtuale locale. Sostituire la proprietà **diskId** nella voce di elenco con l'UId dei dischi identificati nel passaggio precedente. Impostare il valore **isOSDisk** su "true" per il disco del sistema operativo della macchina virtuale e su "false" per tutti gli altri dischi. Lasciare invariate le proprietà **logStorageAccountId** e **logStorageAccountSasSecretName.** Impostare il valore **diskType** sul tipo di disco gestito di Azure (*Standard_LRS, Premium_LRS StandardSSD_LRS*) da utilizzare per il disco. Per i dischi che devono essere crittografati con CMK, aggiungere una proprietà denominata **diskEncryptionSetId** e impostare il valore sull'ID risorsa del set di crittografia del disco**creato($des. Id**) nel passaggio Creare un set di *crittografia del disco*
- Salvare il file modello modificato. Per l'esempio precedente, il file modello modificato ha il seguente aspetto:

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

È ora possibile distribuire il modello di Resource Manager modificato nel gruppo di risorse di progetto per configurare la replica per la macchina virtuale. Informazioni su come distribuire le risorse con i modelli di [Azure Resource Manager e Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md)

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

[Monitorare](tutorial-migrate-vmware.md#track-and-monitor) lo stato della replica tramite l'esperienza del portale ed eseguire Test delle migrazioni e della migrazione.
