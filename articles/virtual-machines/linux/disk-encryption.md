---
title: Crittografia lato server dei dischi gestiti di Azure - Interfaccia della riga di comando di AzureServer-side encryption of Azure Managed Disks - Azure CLI
description: Archiviazione di Azure protegge i dati crittografandoli inattivi prima di essere salvati in modo permanente nei cluster di archiviazione. È possibile fare affidamento sulle chiavi gestite da Microsoft per la crittografia dei dischi gestiti oppure utilizzare chiavi gestite dal cliente per gestire la crittografia con chiavi personalizzate.
author: roygara
ms.date: 04/02/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: f7eb63d0bbdce86f4a7195430dc15d6873e9f6e6
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754304"
---
# <a name="server-side-encryption-of-azure-managed-disks"></a>Crittografia lato server dei dischi gestiti di AzureServer-side encryption of Azure managed disks

I dischi gestiti di Azure crittografano automaticamente i dati per impostazione predefinita quando vengono resi persistenti nel cloud. La crittografia lato server protegge i dati e consente di soddisfare gli impegni di conformità e sicurezza dell'organizzazione. I dati nei dischi gestiti di Azure vengono crittografati in modo trasparente usando la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit, una delle più forti crittografie a blocchi disponibili e compatibile con FIPS 140-2.   

La crittografia non influisce sulle prestazioni dei dischi gestiti. Non sono previsti costi aggiuntivi per la crittografia.

Per altre informazioni sui moduli di crittografia sottostanti i dischi gestiti di Azure, vedere API di crittografia: nuova generazioneFor more information about the cryptographic modules underlying Azure managed disks, see [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

È possibile fare affidamento sulle chiavi gestite dalla piattaforma per la crittografia del disco gestito oppure è possibile gestire la crittografia utilizzando le proprie chiavi. Se si sceglie di gestire la crittografia con chiavi personalizzate, è possibile specificare una *chiave gestita dal cliente* da utilizzare per crittografare e decrittografare tutti i dati nei dischi gestiti. 

Le sezioni seguenti descrivono ognuna delle opzioni per la gestione delle chiavi in modo più dettagliato.

## <a name="platform-managed-keys"></a>Chiavi gestite dalla piattaforma

Per impostazione predefinita, i dischi gestiti utilizzano chiavi di crittografia gestite dalla piattaforma. A partire dal 10 giugno 2017, tutti i nuovi dischi gestiti, le istantanee, le immagini e i nuovi dati scritti nei dischi gestiti esistenti vengono automaticamente crittografati con le chiavi gestite dalla piattaforma.

## <a name="customer-managed-keys"></a>Chiavi gestite dal cliente

È possibile scegliere di gestire la crittografia a livello di ogni disco gestito, con le proprie chiavi. La crittografia lato server per i dischi gestiti con chiavi gestite dal cliente offre un'esperienza integrata con l'insieme di credenziali delle chiavi di Azure.Server-side encryption for managed disks with customer-managed keys offers an integrated experience with Azure Key Vault. È possibile importare le chiavi RSA nell'insieme di credenziali delle chiavi o generare nuove chiavi RSA in Azure Key Vault.You can either import [your RSA keys](../../key-vault/key-vault-hsm-protected-keys.md) to your Key Vault or generate new RSA keys in Azure Key Vault. 

I dischi gestiti di Azure gestiscono la crittografia e la decrittografia in modo completamente trasparente usando la [crittografia della busta.](../../storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique) Crittografa i dati utilizzando una chiave DEK (Data Encryption Key) basata su [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256, che a sua volta è protetta tramite le chiavi. Il servizio di archiviazione genera chiavi di crittografia dei dati e le crittografa con chiavi gestite dal cliente usando la crittografia RSA. La crittografia della busta consente di ruotare (modificare) le chiavi periodicamente in base ai criteri di conformità senza influire sulle macchine virtuali. Quando si ruotano le chiavi, il servizio di archiviazione crittografa nuovamente le chiavi di crittografia dei dati con le nuove chiavi gestite dal cliente. 

È necessario concedere l'accesso ai dischi gestiti nell'insieme di credenziali delle chiavi per utilizzare le chiavi per crittografare e decrittografare il file DEK. Ciò consente il controllo completo dei dati e delle chiavi. È possibile disabilitare le chiavi o revocare l'accesso ai dischi gestiti in qualsiasi momento. È anche possibile controllare l'utilizzo della chiave di crittografia con il monitoraggio dell'insieme di chiavi di Azure per assicurarsi che solo i dischi gestiti o altri servizi attendibili di Azure accedano alle chiavi.

Per gli SSD premium, gli SSD standard e gli HDD standard: quando disabiliti o elimini la chiave, tutte le macchine virtuali con dischi che usano tale chiave verranno arrestate automaticamente. In seguito, le macchine virtuali non saranno utilizzabili a meno che la chiave non sia nuovamente abilitata o non si assegni una nuova chiave.

Per i dischi ultra, quando si disabilita o si elimina una chiave, tutte le macchine virtuali con dischi ultra che usano la chiave non vengono arrestate automaticamente. Dopo aver deallocato e riavviato le macchine virtuali, i dischi smetteranno di usare la chiave e quindi le macchine virtuali non torneranno online. Per riportare le macchine virtuali online, è necessario assegnare una nuova chiave o abilitare la chiave esistente.

Il diagramma seguente mostra come i dischi gestiti usano Azure Active Directory e l'insieme di credenziali delle chiavi di Azure per effettuare richieste usando la chiave gestita dal cliente:The following diagram shows how managed disks use Azure Active Directory and Azure Key Vault to make requests using the customer-managed key:

![Flusso di lavoro del disco gestito e delle chiavi gestite dal cliente. Un amministratore crea un insieme di credenziali delle chiavi di Azure, quindi crea un set di crittografia del disco e configura il set di crittografia del disco. Il set è associato a una macchina virtuale, che consente al disco di usare Azure AD per l'autenticazione](media/disk-storage-encryption/customer-managed-keys-sse-managed-disks-workflow.png)


L'elenco seguente illustra il diagramma in modo ancora più dettagliato:

1. Un amministratore dell'insieme di credenziali delle chiavi di Azure crea le risorse dell'insieme di credenziali delle chiavi.
1. L'amministratore dell'insieme di credenziali delle chiavi importa le chiavi RSA in Key Vault o genera nuove chiavi RSA in Key Vault.
1. Tale amministratore crea un'istanza della risorsa Set di crittografia del disco, specificando un ID dell'insieme di credenziali delle chiavi di Azure e un URL della chiave. Set di crittografia del disco è una nuova risorsa introdotta per semplificare la gestione delle chiavi per i dischi gestiti. 
1. Quando viene creato un set di crittografia del disco, [un'identità gestita assegnata dal sistema](../../active-directory/managed-identities-azure-resources/overview.md) viene creata in Azure Active Directory (AD) e associata al set di crittografia del disco. 
1. L'amministratore dell'insieme di credenziali delle chiavi di Azure concede quindi all'identità gestita l'autorizzazione per eseguire operazioni nell'insieme di credenziali delle chiavi.
1. Un utente VM crea dischi associandoli al set di crittografia del disco. L'utente della macchina virtuale può anche abilitare la crittografia lato server con chiavi gestite dal cliente per le risorse esistenti associandole al set di crittografia del disco. 
1. Managed disks use the managed identity to send requests to the Azure Key Vault.
1. Per la lettura o la scrittura dei dati, i dischi gestiti inviano richieste all'insieme di credenziali delle chiavi di Azure per crittografare (eseguire il wrapping) e decrittografare (annullare il wrapping) la chiave di crittografia dei dati per eseguire la crittografia e la decrittografia dei dati. 

Per revocare l'accesso alle chiavi gestite dal cliente, vedere [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [l'interfaccia della riga di comando dell'insieme di credenziali delle chiavi](https://docs.microsoft.com/cli/azure/keyvault)di Azure . La revoca dell'accesso blocca in modo efficace l'accesso a tutti i dati nell'account di archiviazione, poiché la chiave di crittografia non è accessibile da Archiviazione di Azure.Revoking access effectively blocks access to all data in the storage account, as the encryption key is inaccessible by Azure Storage.

### <a name="supported-regions"></a>Aree supportate

[!INCLUDE [virtual-machines-disks-encryption-regions](../../../includes/virtual-machines-disks-encryption-regions.md)]

### <a name="restrictions"></a>Restrizioni

Per il momento, le chiavi gestite dal cliente hanno le restrizioni seguenti:For now, customer-managed keys have the following restrictions:

- Se questa funzionalità è abilitata per il disco, non è possibile disabilitarla.
    Se è necessario risolvere questo problema, è necessario [copiare tutti i dati](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) in un disco gestito completamente diverso che non utilizza chiavi gestite dal cliente.
- Sono supportate solo [chiavi RSA "soft" e "hard"](../../key-vault/about-keys-secrets-and-certificates.md#keys-and-key-types) di dimensioni 2048, senza altre chiavi o dimensioni.
- I dischi creati da immagini personalizzate crittografate utilizzando la crittografia lato server e le chiavi gestite dal cliente devono essere crittografati utilizzando le stesse chiavi gestite dal cliente e devono trovarsi nella stessa sottoscrizione.
- Gli snapshot creati da dischi crittografati con crittografia lato server e chiavi gestite dal cliente devono essere crittografati con le stesse chiavi gestite dal cliente.
- Le immagini personalizzate crittografate utilizzando la crittografia lato server e le chiavi gestite dal cliente non possono essere utilizzate nella raccolta immagini condivise.
- Tutte le risorse correlate alle chiavi gestite dal cliente (insiemi di credenziali delle chiavi di Azure, set di crittografia del disco, macchine virtuali, dischi e snapshot) devono trovarsi nella stessa sottoscrizione e nella stessa area.
- I dischi, gli snapshot e le immagini crittografati con chiavi gestite dal cliente non possono essere spostati in un'altra sottoscrizione.
- Se si usa il portale di Azure per creare il set di crittografia del disco, per il momento non è possibile usare gli snapshot.
- Anche i dischi gestiti crittografati usando chiavi gestite dal cliente non possono essere crittografati con Crittografia disco di Azure.Managed disks encrypted using customer-managed keys cannot also be encrypted with Azure Disk Encryption.

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Configurazione dell'insieme di credenziali delle chiavi di Azure e di DiskEncryptionSet

1. Verificare di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-az-cli2) e di avere eseguito la registrazione a un account di Azure con [az login](/cli/azure/reference-index).

1. Creare un'istanza di Archiviazione chiavi di Azure e una chiave di crittografia.

    Quando si crea l'istanza del Key Vault, è necessario abilitare la protezione soft delete ed purge. L'eliminazione temporanea garantisce che l'insieme di credenziali delle chiavi contenga una chiave eliminata per un determinato periodo di conservazione (impostazione predefinita di 90 giorni). La protezione dall'eliminazione garantisce che una chiave eliminata non possa essere eliminata definitivamente fino alla scadenza del periodo di conservazione. Queste impostazioni ti proteggono dalla perdita di dati a causa di cancellazioni accidentali. Queste impostazioni sono obbligatorie quando si utilizza un insieme di credenziali delle chiavi per la crittografia dei dischi gestiti.

    > [!IMPORTANT]
    > Non presentare la distinzione tra maiuscole e minuscole nell'area, in caso contrario potrebbero verificarsi problemi durante l'assegnazione di dischi aggiuntivi alla risorsa nel portale di Azure.Do not camel case the region, if you do so you may experience you may experience problems when assigning additional disks to the resource in the Azure portal.

    ```azurecli
    subscriptionId=yourSubscriptionID
    rgName=yourResourceGroupName
    location=westcentralus
    keyVaultName=yourKeyVaultName
    keyName=yourKeyName
    diskEncryptionSetName=yourDiskEncryptionSetName
    diskName=yourDiskName

    az account set --subscription $subscriptionId

    az keyvault create -n $keyVaultName -g $rgName -l $location --enable-purge-protection true --enable-soft-delete true

    az keyvault key create --vault-name $keyVaultName -n $keyName --protection software
    ```

1.    Creare un'istanza di un DiskEncryptionSet.Create an instance of a DiskEncryptionSet. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Concedere alla risorsa DiskEncryptionSet l'accesso all'insieme di credenziali delle chiavi. 

        > [!NOTE]
        > La creazione dell'identità di DiskEncryptionSet in Azure Active Directory potrebbe richiedere alcuni minuti. Se viene visualizzato un errore del tipo "Impossibile trovare l'oggetto Active Directory" durante l'esecuzione del comando seguente, attendere alcuni minuti e riprovare.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Creare una macchina virtuale usando un'immagine del Marketplace, crittografando il sistema operativo e i dischi dati con chiavi gestite dal clienteCreate a VM using a Marketplace image, encrypting the OS and data disks with customer-managed keys

```azurecli
rgName=yourResourceGroupName
vmName=yourVMName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az vm create -g $rgName -n $vmName -l $location --image $image --size $vmSize --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 128 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```


#### <a name="encrypt-existing-unattached-managed-disks"></a>Crittografare i dischi gestiti non collegati esistentiEncrypt existing unattached managed disks 

I dischi esistenti non devono essere collegati a una macchina virtuale in esecuzione per poterli crittografare usando lo script seguente:Your existing disks must not be attached to a running VM in order to you to encrypt them using the following script:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Creare un set di scalabilità di macchine virtuali usando un'immagine marketplace, crittografando il sistema operativo e i dischi dati con chiavi gestite dal clienteCreate a virtual machine scale set using a Marketplace image, encrypting the OS and data disks with customer-managed keys

```azurecli
rgName=yourResourceGroupName
vmssName=yourVMSSName
location=westcentralus
vmSize=Standard_DS3_V2
image=UbuntuLTS 
diskEncryptionSetName=yourDiskencryptionSetName

diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)
az vmss create -g $rgName -n $vmssName --image UbuntuLTS --upgrade-policy automatic --admin-username azureuser --generate-ssh-keys --os-disk-encryption-set $diskEncryptionSetId --data-disk-sizes-gb 64 128 --data-disk-encryption-sets $diskEncryptionSetId $diskEncryptionSetId
```

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Creare un disco vuoto crittografato usando la crittografia lato server con chiavi gestite dal cliente e collegarlo a una macchina virtualeCreate an empty disk encrypted using server-side encryption with customer-managed keys and attach it to a VM

```azurecli
vmName=yourVMName
rgName=yourResourceGroupName
diskName=yourDiskName
diskSkuName=Premium_LRS
diskSizeinGiB=30
location=westcentralus
diskLUN=2
diskEncryptionSetName=yourDiskEncryptionSetName


diskEncryptionSetId=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [id] -o tsv)

az disk create -n $diskName -g $rgName -l $location --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId --size-gb $diskSizeinGiB --sku $diskSkuName

diskId=$(az disk show -n $diskName -g $rgName --query [id] -o tsv)

az vm disk attach --vm-name $vmName --lun $diskLUN --ids $diskId 

```

#### <a name="change-the-key-of-a-diskencryptionset-to-rotate-the-key-for-all-the-resources-referencing-the-diskencryptionset"></a>Modificare la chiave di un DiskEncryptionSet per ruotare la chiave per tutte le risorse che fanno riferimento a DiskEncryptionSet

```azurecli

rgName=yourResourceGroupName
keyVaultName=yourKeyVaultName
keyName=yourKeyName
diskEncryptionSetName=yourDiskEncryptionSetName


keyVaultId=$(az keyvault show --name $keyVaultName--query [id] -o tsv)

keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)

az disk-encryption-set update -n keyrotationdes -g keyrotationtesting --key-url $keyVaultKeyUrl --source-vault $keyVaultId

```

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Trovare lo stato della crittografia lato server di un disco

```azurecli

az disk show -g yourResourceGroupName -n yourDiskName --query [encryption.type] -o tsv

```

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse sotto le coperte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory di Azure AD a un'altra, l'identità gestita associata ai dischi gestiti non viene trasferita al nuovo tenant, pertanto le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere Trasferimento di una sottoscrizione tra directory di [Azure AD.](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse sotto le coperte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory di Azure AD a un'altra, l'identità gestita associata ai dischi gestiti non viene trasferita al nuovo tenant, pertanto le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere Trasferimento di una sottoscrizione tra directory di [Azure AD.](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Crittografia lato server e crittografia del disco di AzureServer-side encryption versus Azure disk encryption

[Crittografia del disco](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) di Azure per macchine virtuali e set di scalabilità di macchine virtuali sfrutta la funzionalità [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) di Windows e la funzionalità [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per crittografare i dischi gestiti con chiavi gestite dal cliente all'interno della macchina virtuale guest.  La crittografia lato server con chiavi gestite dal cliente migliora in ADE consentendo di usare qualsiasi tipo di sistema operativo e immagini per le macchine virtuali crittografando i dati nel servizio di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare i modelli di Azure Resource Manager per la creazione di dischi crittografati con chiavi gestite dal clienteExplore the Azure Resource Manager templates for creating encrypted disks with customer-managed keys](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Informazioni sull'insieme di credenziali delle chiavi di Azure](../../key-vault/key-vault-overview.md)
- [Replicare computer con dischi abilitati per le chiavi gestite dal cliente](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurare il ripristino di emergenza di VM VMware in Azure con PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurare il ripristino di emergenza in Azure per le macchine virtuali Hyper-V tramite PowerShell e Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
