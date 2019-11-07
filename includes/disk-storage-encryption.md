---
title: File di inclusione
description: File di inclusione
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/24/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0659e57f5a5b223c199becf492b27c7a70cbdc63
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73612093"
---
Azure Managed disks crittografa automaticamente i dati per impostazione predefinita quando vengono salvati nel cloud. La crittografia lato server consente di proteggere i dati e di soddisfare gli impegni di sicurezza e conformità dell'organizzazione. I dati nei dischi gestiti di Azure vengono crittografati in modo trasparente usando la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2.   

La crittografia non influisca sulle prestazioni dei dischi gestiti. Non sono previsti costi aggiuntivi per la crittografia.

Per altre informazioni sui moduli di crittografia sottostanti di Azure Managed disks, vedere [API di crittografia: prossima generazione](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

È possibile basarsi sulle chiavi gestite dalla piattaforma per la crittografia del disco gestito oppure è possibile gestire la crittografia usando chiavi personalizzate (anteprima pubblica). Se si sceglie di gestire la crittografia con le proprie chiavi, è possibile specificare una *chiave gestita dal cliente* da usare per crittografare e decrittografare tutti i dati nei dischi gestiti. 

Le sezioni seguenti descrivono in modo più dettagliato tutte le opzioni per la gestione delle chiavi.

## <a name="platform-managed-keys"></a>Chiavi gestite dalla piattaforma

Per impostazione predefinita, Managed disks USA chiavi di crittografia gestite dalla piattaforma. A partire dal 10 giugno 2017, tutti i nuovi dischi gestiti, gli snapshot, le immagini e i nuovi dati scritti nei dischi gestiti esistenti vengono crittografati automaticamente con chiavi gestite dalla piattaforma. 

## <a name="customer-managed-keys-public-preview"></a>Chiavi gestite dal cliente (anteprima pubblica)

È possibile scegliere di gestire la crittografia a livello di ogni disco gestito, con le proprie chiavi. La crittografia lato server per Managed disks con chiavi gestite dal cliente offre un'esperienza integrata con Azure Key Vault. È possibile importare [le chiavi RSA](../articles/key-vault/key-vault-hsm-protected-keys.md) nella Key Vault o generare nuove chiavi rsa in Azure Key Vault. Azure Managed disks gestisce la crittografia e la decrittografia in modo completamente trasparente usando la [crittografia envelope](../articles/storage/common/storage-client-side-encryption.md#encryption-via-the-envelope-technique). Consente di crittografare i dati usando una chiave di crittografia dei dati basata su [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256, che a sua volta è protetta usando le chiavi. È necessario concedere l'accesso a Managed disks nel Key Vault per usare le chiavi per la crittografia e la decrittografia della chiave di crittografia. In questo modo è possibile controllare completamente i dati e le chiavi. È possibile disabilitare le chiavi o revocare l'accesso ai dischi gestiti in qualsiasi momento. È anche possibile controllare l'utilizzo della chiave di crittografia con Azure Key Vault monitoraggio per garantire che solo i dischi gestiti o altri servizi di Azure attendibili accedano alle chiavi.

Il diagramma seguente illustra come i dischi gestiti usano Azure Active Directory e Azure Key Vault per eseguire richieste usando la chiave gestita dal cliente:

![Flusso di lavoro chiavi gestite dal cliente di dischi gestiti](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


Nell'elenco seguente vengono illustrati i passaggi numerati nel diagramma:

1. Un amministratore Azure Key Vault crea le risorse di Key Vault.
1. L'amministratore dell'insieme di credenziali delle chiavi importa le chiavi RSA per Key Vault o genera nuove chiavi RSA nel Key Vault.
1. Tale amministratore crea un'istanza della risorsa del set di crittografia del disco, specificando un ID Azure Key Vault e un URL della chiave. Il set di crittografia del disco è una nuova risorsa introdotta per semplificare la gestione delle chiavi per Managed Disks. 
1. Quando viene creato un set di crittografia del disco, viene creata un' [identità gestita assegnata dal sistema](../articles/active-directory/managed-identities-azure-resources/overview.md) in Azure Active Directory (ad) e associata al set di crittografia del disco. 
1. L'amministratore di Azure Key Vault concede quindi l'autorizzazione di identità gestita per eseguire operazioni nell'insieme di credenziali delle chiavi.
1. Un utente di VM crea dischi associando i dischi al set di crittografia del disco. L'utente della macchina virtuale può inoltre abilitare la crittografia lato server con chiavi gestite dal cliente per le risorse esistenti associando tali chiavi al set di crittografia del disco. 
1. Managed disks usa l'identità gestita per inviare richieste al Azure Key Vault.
1. Per la lettura o la scrittura di dati, Managed disks invia richieste a Azure Key Vault per crittografare (eseguire il wrapping) e decrittografare (annullare il wrapping) la chiave di crittografia dei dati per eseguire la crittografia e la decrittografia dei dati. 

Per revocare l'accesso alle chiavi gestite dal cliente, vedere [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). La revoca dell'accesso blocca efficacemente l'accesso a tutti i dati nell'account di archiviazione, in quanto la chiave di crittografia non è accessibile da parte di archiviazione di Azure.

### <a name="supported-scenarios-and-restrictions"></a>Scenari e restrizioni supportati

Durante l'anteprima, sono supportati solo gli scenari seguenti:

- Creare una macchina virtuale (VM) da un'immagine di Azure Marketplace e crittografare il disco del sistema operativo con la crittografia lato server usando chiavi gestite dal cliente.
- Creare un'immagine personalizzata crittografata con la crittografia lato server e le chiavi gestite dal cliente.
- Creare una macchina virtuale da un'immagine personalizzata e crittografare il disco del sistema operativo usando la crittografia lato server e le chiavi gestite dal cliente.
- Creare dischi dati crittografati usando la crittografia lato server e le chiavi gestite dal cliente.
- Creare snapshot crittografati tramite la crittografia lato server e le chiavi gestite dal cliente.
- Creare set di scalabilità di macchine virtuali crittografati con la crittografia lato server e le chiavi gestite dal cliente.

L'anteprima presenta anche le restrizioni seguenti:

- Disponibile solo negli Stati Uniti centro-occidentali.
- I dischi creati da immagini personalizzate crittografate con la crittografia lato server e le chiavi gestite dal cliente devono essere crittografati con le stesse chiavi gestite dal cliente e devono trovarsi nella stessa sottoscrizione.
- Gli snapshot creati da dischi crittografati con la crittografia lato server e le chiavi gestite dal cliente devono essere crittografati con le stesse chiavi gestite dal cliente.
- Le immagini personalizzate crittografate usando la crittografia lato server e le chiavi gestite dal cliente non possono essere usate nella raccolta di immagini condivise.
- Il Key Vault deve trovarsi nella stessa sottoscrizione e nella stessa area delle chiavi gestite dal cliente.
- I dischi, gli snapshot e le immagini crittografati con chiavi gestite dal cliente non possono passare a un'altra sottoscrizione.

### <a name="setting-up-your-azure-key-vault"></a>Impostazione della Azure Key Vault

1.  Creare un'istanza di Azure Key Vault e la chiave di crittografia.

    Quando si crea l'istanza di Key Vault, è necessario abilitare la protezione eliminazione temporanea e ripulitura. L'eliminazione temporanea garantisce che la Key Vault contenga una chiave eliminata per un determinato periodo di conservazione (valore predefinito di 90 giorno). L'eliminazione della protezione garantisce che una chiave eliminata non possa essere eliminata definitivamente fino a quando non scade il periodo di conservazione. Queste impostazioni consentono di evitare la perdita di dati a causa dell'eliminazione accidentale. Queste impostazioni sono obbligatorie quando si usa un Key Vault per la crittografia dei dischi gestiti.

    ```powershell
    $keyVault = New-AzKeyVault -Name myKeyVaultName ` 
    -ResourceGroupName myRGName ` 
    -Location westcentralus ` 
    -EnableSoftDelete ` 
    -EnablePurgeProtection 
     
    $key = Add-AzKeyVaultKey -VaultName $keyVault.VaultName ` 
    -Name myKeyName ` 
    -Destination Software `  
    ```

1.  Creare un'istanza di un DiskEncryptionSet. 
    
    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName myRGName ` 
      -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateDiskEncryptionSet.json" ` 
      -diskEncryptionSetName "myDiskEncryptionSet1" ` 
      -keyVaultId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.KeyVault/vaults/myKeyVaultName" ` 
      -keyVaultKeyUrl "https://myKeyVaultName.vault.azure.net/keys/myKeyName/403445136dee4a57af7068cab08f7d42" ` 
      -region "WestCentralUS"
    ```

1.  Concedere all'insieme di credenziali delle chiavi l'accesso alla risorsa DiskEncryptionSet.

    ```powershell
    $identity = Get-AzADServicePrincipal -DisplayName myDiskEncryptionSet1  
     
    Set-AzKeyVaultAccessPolicy ` 
        -VaultName $keyVault.VaultName ` 
        -ObjectId $identity.Id ` 
        -PermissionsToKeys wrapkey,unwrapkey,get 
     
    New-AzRoleAssignment ` 
        -ObjectId $identity.Id ` 
        -RoleDefinitionName "Reader" ` 
        -ResourceName $keyVault.VaultName ` 
        -ResourceType "Microsoft.KeyVault/vaults" ` 
        -ResourceGroupName myRGName `  
    ```

### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys-via-a-resource-manager-template"></a>Creare una VM usando un'immagine del Marketplace, crittografando il sistema operativo e i dischi dati con chiavi gestite dal cliente tramite un modello di Gestione risorse

```
$password=ConvertTo-SecureString -String "myVMPassword" `
  -AsPlainText -Force
New-AzResourceGroupDeployment -ResourceGroupName CMKTesting `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateVMWithDisksEncryptedWithCMK.json" `
  -virtualMachineName "myVMName" `
  -adminPassword $password `
  -vmSize "Standard_DS3_V2" `
  -diskEncryptionSetId "/subscriptions/mySubscriptionId/resourceGroups/myRGName/providers/Microsoft.Compute/diskEncryptionSets/myDiskEncryptionSet1" `
  -region "westcentralus" 
```

### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Creare un disco vuoto crittografato usando la crittografia lato server con chiavi gestite dal cliente e associarlo a una macchina virtuale

```PowerShell
$vmName = "yourVMName"
$rgName = "yourRGName"
$diskName = "yourDiskName"
$diskSKU = "Premium_LRS"
$diskSizeinGiB = "30"
$diskEncryptionSetId = "/subscriptions/<subscriptionID>/resourceGroups/yourRGName/providers/Microsoft.Compute/diskEncryptionSets/<yourDiskEncryptionSetName>"
$region = "westcentralus"
$diskLUN = 1

New-AzResourceGroupDeployment -ResourceGroupName $rgName `
  -TemplateUri "https://raw.githubusercontent.com/ramankumarlive/manageddiskscmkpreview/master/CreateEmptyDataDiskEncryptedWithSSECMK.json" `
  -diskName $diskName `
  -diskSkuName $diskSKU `
  -dataDiskSizeInGb $diskSizeinGiB `
  -diskEncryptionSetId $diskEncryptionSetId `
  -region $region 

$vm = Get-AzVM -Name $vmName -ResourceGroupName $rgName 
$disk = Get-AzDisk -DiskName $diskName -ResourceGroupName $rgName
$vm = Add-AzVMDataDisk -VM $vm -Name $diskName -CreateOption Attach -ManagedDiskId $disk.Id -Lun 1
Update-AzVM -ResourceGroupName $rgName -VM $vm
```


> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory Azure AD a un'altra, l'identità gestita associata a Managed Disks non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per ulteriori informazioni, vedere [trasferimento di una sottoscrizione tra Azure ad directory](../articles/active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Crittografia lato server rispetto a crittografia dischi di Azure

[Crittografia dischi di Azure](../articles/security/fundamentals/azure-disk-encryption-vms-vmss.md) sfrutta la funzionalità [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) di Windows e la funzionalità [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per crittografare i dischi gestiti con chiavi gestite dal cliente all'interno della VM guest.  La crittografia lato server con chiavi gestite dal cliente migliora l'ADE consentendo di usare qualsiasi tipo di sistema operativo e immagini per le macchine virtuali crittografando i dati nel servizio di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- [Cos'è l'insieme di credenziali chiave di Azure?](../articles/key-vault/key-vault-overview.md)
