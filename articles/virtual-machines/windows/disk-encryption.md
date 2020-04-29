---
title: Crittografia lato server di Azure Managed Disks-PowerShell
description: Archiviazione di Azure protegge i dati mediante la crittografia dei dati inattivi prima di renderli permanente nei cluster di archiviazione. È possibile basarsi sulle chiavi gestite da Microsoft per la crittografia dei dischi gestiti oppure usare chiavi gestite dal cliente per gestire la crittografia con chiavi personalizzate.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-windows
ms.subservice: disks
ms.openlocfilehash: b25d35ce467786c402c56c8a7d19a305381db94f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82137791"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Crittografia lato server di Azure Managed Disks

Azure Managed disks crittografa automaticamente i dati per impostazione predefinita quando vengono salvati nel cloud. La crittografia lato server consente di proteggere i dati e di soddisfare gli impegni di sicurezza e conformità dell'organizzazione. I dati nei dischi gestiti di Azure vengono crittografati in modo trasparente usando la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2.

La crittografia non influisca sulle prestazioni dei dischi gestiti. Non sono previsti costi aggiuntivi per la crittografia.

Per altre informazioni sui moduli di crittografia sottostanti di Azure Managed disks, vedere [API di crittografia: prossima generazione](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

È possibile basarsi sulle chiavi gestite dalla piattaforma per la crittografia del disco gestito oppure è possibile gestire la crittografia usando le proprie chiavi. Se si sceglie di gestire la crittografia con le proprie chiavi, è possibile specificare una *chiave gestita dal cliente* da usare per crittografare e decrittografare tutti i dati nei dischi gestiti. 

Le sezioni seguenti descrivono in modo più dettagliato tutte le opzioni per la gestione delle chiavi.

## <a name="platform-managed-keys"></a>Chiavi gestite dalla piattaforma

Per impostazione predefinita, Managed disks USA chiavi di crittografia gestite dalla piattaforma. A partire dal 10 giugno 2017, tutti i nuovi dischi gestiti, gli snapshot, le immagini e i nuovi dati scritti nei dischi gestiti esistenti vengono crittografati automaticamente con chiavi gestite dalla piattaforma.

## <a name="customer-managed-keys"></a>Chiavi gestite dal cliente

È possibile scegliere di gestire la crittografia a livello di ogni disco gestito, con le proprie chiavi. La crittografia lato server per Managed disks con chiavi gestite dal cliente offre un'esperienza integrata con Azure Key Vault. È possibile importare [le chiavi RSA](../../key-vault/keys/hsm-protected-keys.md) nella Key Vault o generare nuove chiavi rsa in Azure Key Vault. 

Azure Managed disks gestisce la crittografia e la decrittografia in modo completamente trasparente usando la [crittografia envelope](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Consente di crittografare i dati usando una chiave di crittografia dei dati basata su [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256, che a sua volta è protetta usando le chiavi. Il servizio di archiviazione genera chiavi di crittografia dei dati e le crittografa con chiavi gestite dal cliente usando la crittografia RSA. La crittografia envelope consente di ruotare (modificare) periodicamente le chiavi in base ai criteri di conformità senza influisca sulle VM. Quando si ruotano le chiavi, il servizio di archiviazione esegue nuovamente la crittografia delle chiavi di crittografia dei dati con le nuove chiavi gestite dal cliente. 

È necessario concedere l'accesso a Managed disks nel Key Vault per usare le chiavi per la crittografia e la decrittografia della chiave di crittografia. In questo modo è possibile controllare completamente i dati e le chiavi. È possibile disabilitare le chiavi o revocare l'accesso ai dischi gestiti in qualsiasi momento. È anche possibile controllare l'utilizzo della chiave di crittografia con Azure Key Vault monitoraggio per garantire che solo i dischi gestiti o altri servizi di Azure attendibili accedano alle chiavi.

Per SSD Premium, SSD standard e HDD standard: quando si disabilita o si elimina la chiave, tutte le VM con dischi che usano tale chiave verranno arrestate automaticamente. In seguito, le macchine virtuali non saranno utilizzabili, a meno che la chiave non sia abilitata di nuovo o si assegni una nuova chiave.

Per i dischi Ultra, quando si disabilita o si elimina una chiave, le macchine virtuali con dischi Ultra con la chiave non vengono arrestate automaticamente. Dopo aver deallocato e riavviato le macchine virtuali, i dischi smetteranno di usare la chiave, quindi le macchine virtuali non torneranno in linea. Per riportare online le VM, è necessario assegnare una nuova chiave o abilitare la chiave esistente.

Il diagramma seguente illustra come i dischi gestiti usano Azure Active Directory e Azure Key Vault per eseguire richieste usando la chiave gestita dal cliente:

![Flusso di lavoro del disco gestito e delle chiavi gestite dal cliente. Un amministratore crea una Azure Key Vault, quindi crea un set di crittografia del disco e imposta il set di crittografia del disco. Il set è associato a una VM, che consente al disco di usare Azure AD per l'autenticazione](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


L'elenco seguente illustra il diagramma in modo più dettagliato:

1. Un amministratore Azure Key Vault crea le risorse di Key Vault.
1. L'amministratore dell'insieme di credenziali delle chiavi importa le chiavi RSA per Key Vault o genera nuove chiavi RSA nel Key Vault.
1. Tale amministratore crea un'istanza della risorsa del set di crittografia del disco, specificando un ID Azure Key Vault e un URL della chiave. Il set di crittografia del disco è una nuova risorsa introdotta per semplificare la gestione delle chiavi per Managed Disks. 
1. Quando viene creato un set di crittografia del disco, viene creata un' [identità gestita assegnata dal sistema](../../active-directory/managed-identities-azure-resources/overview.md) in Azure Active Directory (ad) e associata al set di crittografia del disco. 
1. L'amministratore di Azure Key Vault concede quindi l'autorizzazione di identità gestita per eseguire operazioni nell'insieme di credenziali delle chiavi.
1. Un utente di VM crea dischi associando i dischi al set di crittografia del disco. L'utente della macchina virtuale può inoltre abilitare la crittografia lato server con chiavi gestite dal cliente per le risorse esistenti associando tali chiavi al set di crittografia del disco. 
1. Managed disks usa l'identità gestita per inviare richieste al Azure Key Vault.
1. Per la lettura o la scrittura di dati, Managed disks invia richieste a Azure Key Vault per crittografare (eseguire il wrapping) e decrittografare (annullare il wrapping) la chiave di crittografia dei dati per eseguire la crittografia e la decrittografia dei dati. 

Per revocare l'accesso alle chiavi gestite dal cliente, vedere [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). La revoca dell'accesso blocca efficacemente l'accesso a tutti i dati nell'account di archiviazione, in quanto la chiave di crittografia non è accessibile da parte di archiviazione di Azure.

### <a name="supported-regions"></a>Aree supportate

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Restrizioni

Per il momento, le chiavi gestite dal cliente presentano le restrizioni seguenti:

- Se questa funzionalità è abilitata per il disco, non è possibile disabilitarla.
    Se è necessario aggirare questo problema, è necessario [copiare tutti i dati](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) in un disco gestito completamente diverso che non usa chiavi gestite dal cliente.
- Sono supportate solo le [chiavi RSA "soft" e "hard"](../../key-vault/keys/about-keys.md) di dimensioni 2080, senza altre chiavi o dimensioni.
- I dischi creati da immagini personalizzate crittografate con la crittografia lato server e le chiavi gestite dal cliente devono essere crittografati con le stesse chiavi gestite dal cliente e devono trovarsi nella stessa sottoscrizione.
- Gli snapshot creati da dischi crittografati con la crittografia lato server e le chiavi gestite dal cliente devono essere crittografati con le stesse chiavi gestite dal cliente.
- Le immagini personalizzate crittografate usando la crittografia lato server e le chiavi gestite dal cliente non possono essere usate nella raccolta di immagini condivise.
- Tutte le risorse correlate alle chiavi gestite dal cliente (insiemi di credenziali delle chiavi di Azure, set di crittografia del disco, VM, dischi e snapshot) devono trovarsi nella stessa area e nella stessa sottoscrizione.
- I dischi, gli snapshot e le immagini crittografati con chiavi gestite dal cliente non possono passare a un'altra sottoscrizione.
- Se si usa il portale di Azure per creare il set di crittografia del disco, non è possibile usare gli snapshot per il momento.
- I dischi gestiti crittografati con chiavi gestite dal cliente non possono anche essere crittografati con crittografia dischi di Azure.

### <a name="powershell"></a>PowerShell

#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Configurazione di Azure Key Vault e DiskEncryptionSet

1. Assicurarsi di aver installato la versione più recente di [Azure PowerShell](/powershell/azure/install-az-ps)e di avere eseguito l'accesso a un account Azure con Connect-AzAccount

1. Creare un'istanza di Azure Key Vault e la chiave di crittografia.

    Quando si crea l'istanza di Key Vault, è necessario abilitare la protezione eliminazione temporanea e ripulitura. L'eliminazione temporanea garantisce che la Key Vault contenga una chiave eliminata per un determinato periodo di conservazione (valore predefinito di 90 giorno). L'eliminazione della protezione garantisce che una chiave eliminata non possa essere eliminata definitivamente fino a quando non scade il periodo di conservazione. Queste impostazioni consentono di evitare la perdita di dati a causa dell'eliminazione accidentale. Queste impostazioni sono obbligatorie quando si usa un Key Vault per la crittografia dei dischi gestiti.

    > [!IMPORTANT]
    > In caso contrario, è possibile che si verifichino problemi durante l'assegnazione di dischi aggiuntivi alla risorsa nel portale di Azure.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName -ResourceGroupName $ResourceGroupName -Location $LocationName -EnableSoftDelete -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName -Destination $keyDestination  
    ```

1.    Creare un'istanza di un DiskEncryptionSet. 
    
        ```powershell
        $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName -SourceVaultId $keyVault.ResourceId -KeyUrl $key.Key.Kid -IdentityType SystemAssigned
        
        $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -InputObject $desConfig 
        ```

1.    Concedere all'insieme di credenziali delle chiavi l'accesso alla risorsa DiskEncryptionSet.

        > [!NOTE]
        > Potrebbero essere necessari alcuni minuti prima che Azure crei l'identità del DiskEncryptionSet nel Azure Active Directory. Se viene rilevato un errore simile a "Impossibile trovare l'oggetto Active Directory" quando si esegue il comando seguente, attendere alcuni minuti e riprovare.
        
        ```powershell
        $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
         
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
         
        New-AzRoleAssignment -ResourceName $keyVaultName -ResourceGroupName $ResourceGroupName -ResourceType "Microsoft.KeyVault/vaults" -ObjectId $des.Identity.PrincipalId -RoleDefinitionName "Reader" 
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Creare una VM usando un'immagine del Marketplace, crittografando il sistema operativo e i dischi dati con chiavi gestite dal cliente

```powershell
$VMLocalAdminUser = "yourVMLocalAdminUserName"
$VMLocalAdminSecurePassword = ConvertTo-SecureString <password> -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerName = "yourComputerName"
$VMName = "yourVMName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourdiskEncryptionSetName"
    
$NetworkName = "yourNetworkName"
$NICName = "yourNICName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix
$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet
$NIC = New-AzNetworkInterface -Name $NICName -ResourceGroupName $ResourceGroupName -Location $LocationName -SubnetId $Vnet.Subnets[0].Id
    
$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);
    
$VirtualMachine = New-AzVMConfig -VMName $VMName -VMSize $VMSize
$VirtualMachine = Set-AzVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName $ComputerName -Credential $Credential -ProvisionVMAgent -EnableAutoUpdate
$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $NIC.Id
$VirtualMachine = Set-AzVMSourceImage -VM $VirtualMachine -PublisherName 'MicrosoftWindowsServer' -Offer 'WindowsServer' -Skus '2012-R2-Datacenter' -Version latest

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name $($VMName +"_OSDisk") -DiskEncryptionSetId $diskEncryptionSet.Id -CreateOption FromImage

$VirtualMachine = Add-AzVMDataDisk -VM $VirtualMachine -Name $($VMName +"DataDisk1") -DiskSizeInGB 128 -StorageAccountType Premium_LRS -CreateOption Empty -Lun 0 -DiskEncryptionSetId $diskEncryptionSet.Id 
    
New-AzVM -ResourceGroupName $ResourceGroupName -Location $LocationName -VM $VirtualMachine -Verbose
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Creare un disco vuoto crittografato usando la crittografia lato server con chiavi gestite dal cliente e associarlo a una macchina virtuale

```PowerShell
$vmName = "yourVMName"
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = 30
$diskLUN = 1
$diskEncryptionSetName="yourDiskEncryptionSetName"


$vm = Get-AzVM -Name $vmName -ResourceGroupName $ResourceGroupName 

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Empty -DiskSizeInGB $diskSizeinGiB -StorageAccountType $diskSKU -Lun $diskLUN -DiskEncryptionSetId $diskEncryptionSet.Id 

Update-AzVM -ResourceGroupName $ResourceGroupName -VM $vm

```

#### <a name="encrypt-existing-managed-disks"></a>Crittografare i dischi gestiti esistenti 

I dischi esistenti non devono essere collegati a una macchina virtuale in esecuzione per poterli crittografare usando lo script seguente:

```PowerShell
$rgName = "yourResourceGroupName"
$diskName = "yourDiskName"
$diskEncryptionSetName = "yourDiskEncryptionSetName"
 
$diskEncryptionSet = Get-AzDiskEncryptionSet -ResourceGroupName $rgName -Name $diskEncryptionSetName
 
New-AzDiskUpdateConfig -EncryptionType "EncryptionAtRestWithCustomerKey" -DiskEncryptionSetId $diskEncryptionSet.Id | Update-AzDisk -ResourceGroupName $rgName -DiskName $diskName
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Creare un set di scalabilità di macchine virtuali usando un'immagine del Marketplace, crittografando il sistema operativo e i dischi dati con chiavi gestite dal cliente

```PowerShell
$VMLocalAdminUser = "yourLocalAdminUser"
$VMLocalAdminSecurePassword = ConvertTo-SecureString Password@123 -AsPlainText -Force
$LocationName = "westcentralus"
$ResourceGroupName = "yourResourceGroupName"
$ComputerNamePrefix = "yourComputerNamePrefix"
$VMScaleSetName = "yourVMSSName"
$VMSize = "Standard_DS3_v2"
$diskEncryptionSetName="yourDiskEncryptionSetName"
    
$NetworkName = "yourVNETName"
$SubnetName = "yourSubnetName"
$SubnetAddressPrefix = "10.0.0.0/24"
$VnetAddressPrefix = "10.0.0.0/16"
    
$SingleSubnet = New-AzVirtualNetworkSubnetConfig -Name $SubnetName -AddressPrefix $SubnetAddressPrefix

$Vnet = New-AzVirtualNetwork -Name $NetworkName -ResourceGroupName $ResourceGroupName -Location $LocationName -AddressPrefix $VnetAddressPrefix -Subnet $SingleSubnet

$ipConfig = New-AzVmssIpConfig -Name "myIPConfig" -SubnetId $Vnet.Subnets[0].Id 

$VMSS = New-AzVmssConfig -Location $LocationName -SkuCapacity 2 -SkuName $VMSize -UpgradePolicyMode 'Automatic'

$VMSS = Add-AzVmssNetworkInterfaceConfiguration -Name "myVMSSNetworkConfig" -VirtualMachineScaleSet $VMSS -Primary $true -IpConfiguration $ipConfig

$diskEncryptionSet=Get-AzDiskEncryptionSet -ResourceGroupName $ResourceGroupName -Name $diskEncryptionSetName

# Enable encryption at rest with customer managed keys for OS disk by setting DiskEncryptionSetId property 

$VMSS = Set-AzVmssStorageProfile $VMSS -OsDiskCreateOption "FromImage" -DiskEncryptionSetId $diskEncryptionSet.Id -ImageReferenceOffer 'WindowsServer' -ImageReferenceSku '2012-R2-Datacenter' -ImageReferenceVersion latest -ImageReferencePublisher 'MicrosoftWindowsServer'

$VMSS = Set-AzVmssOsProfile $VMSS -ComputerNamePrefix $ComputerNamePrefix -AdminUsername $VMLocalAdminUser -AdminPassword $VMLocalAdminSecurePassword

# Add a data disk encrypted at rest with customer managed keys by setting DiskEncryptionSetId property 

$VMSS = Add-AzVmssDataDisk -VirtualMachineScaleSet $VMSS -CreateOption Empty -Lun 1 -DiskSizeGB 128 -StorageAccountType Premium_LRS -DiskEncryptionSetId $diskEncryptionSet.Id

$Credential = New-Object System.Management.Automation.PSCredential ($VMLocalAdminUser, $VMLocalAdminSecurePassword);

New-AzVmss -VirtualMachineScaleSet $VMSS -ResourceGroupName $ResourceGroupName -VMScaleSetName $VMScaleSetName
```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Modificare la chiave di un DiskEncryptionSet per ruotare la chiave per tutte le risorse che fanno riferimento a DiskEncryptionSet

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$keyVaultName="yourKeyVaultName"
$keyName="yourKeyName"
$diskEncryptionSetName="yourDiskEncryptionSetName"

$keyVault = Get-AzKeyVault -VaultName $keyVaultName -ResourceGroupName $ResourceGroupName

$keyVaultKey = Get-AzKeyVaultKey -VaultName $keyVaultName -Name $keyName

Update-AzDiskEncryptionSet -Name $diskEncryptionSetName -ResourceGroupName $ResourceGroupName -SourceVaultId $keyVault.ResourceId -KeyUrl $keyVaultKey.Id
```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Individuare lo stato della crittografia lato server di un disco

```PowerShell
$ResourceGroupName="yourResourceGroupName"
$DiskName="yourDiskName"

$disk=Get-AzDisk -ResourceGroupName $ResourceGroupName -DiskName $DiskName
$disk.Encryption.Type

```

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory Azure AD a un'altra, l'identità gestita associata a Managed Disks non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per ulteriori informazioni, vedere [trasferimento di una sottoscrizione tra Azure ad directory](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory Azure AD a un'altra, l'identità gestita associata a Managed Disks non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per ulteriori informazioni, vedere [trasferimento di una sottoscrizione tra Azure ad directory](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Crittografia lato server rispetto a crittografia dischi di Azure

[Crittografia dischi di Azure](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) sfrutta la funzionalità [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) di Windows e la funzionalità [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per crittografare i dischi gestiti con chiavi gestite dal cliente all'interno della VM guest.  La crittografia lato server con chiavi gestite dal cliente migliora l'ADE consentendo di usare qualsiasi tipo di sistema operativo e immagini per le macchine virtuali crittografando i dati nel servizio di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare i modelli di Azure Resource Manager per la creazione di dischi crittografati con chiavi gestite dal cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Informazioni sull'insieme di credenziali delle chiavi di Azure](../../key-vault/general/overview.md)
- [Replicare i computer con i dischi abilitati per le chiavi gestite dal cliente](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurare il ripristino di emergenza di VM VMware in Azure con PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurare il ripristino di emergenza in Azure per le macchine virtuali Hyper-V tramite PowerShell e Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
