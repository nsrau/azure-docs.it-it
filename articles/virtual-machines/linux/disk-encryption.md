---
title: Crittografia lato server di Azure Managed Disks-interfaccia della riga di comando di Azure
description: Archiviazione di Azure protegge i dati mediante la crittografia dei dati inattivi prima di renderli permanente nei cluster di archiviazione. È possibile basarsi sulle chiavi gestite da Microsoft per la crittografia dei dischi gestiti oppure usare chiavi gestite dal cliente per gestire la crittografia con chiavi personalizzate.
author: roygara
ms.date: 04/21/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.openlocfilehash: 027efd268ee80fbaf921b42d09cc424c8e8483ba
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82136924"
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


L'elenco seguente illustra il diagramma in modo ancora più dettagliato:

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
    Se è necessario aggirare questo problema, è necessario [copiare tutti i dati](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) in un disco gestito completamente diverso che non usa chiavi gestite dal cliente.
- Sono supportate solo le [chiavi RSA "soft" e "hard"](../../key-vault/keys/about-keys.md) di dimensioni 2048, senza altre chiavi o dimensioni.
- I dischi creati da immagini personalizzate crittografate con la crittografia lato server e le chiavi gestite dal cliente devono essere crittografati con le stesse chiavi gestite dal cliente e devono trovarsi nella stessa sottoscrizione.
- Gli snapshot creati da dischi crittografati con la crittografia lato server e le chiavi gestite dal cliente devono essere crittografati con le stesse chiavi gestite dal cliente.
- Le immagini personalizzate crittografate usando la crittografia lato server e le chiavi gestite dal cliente non possono essere usate nella raccolta di immagini condivise.
- Tutte le risorse correlate alle chiavi gestite dal cliente (insiemi di credenziali delle chiavi di Azure, set di crittografia del disco, VM, dischi e snapshot) devono trovarsi nella stessa area e nella stessa sottoscrizione.
- I dischi, gli snapshot e le immagini crittografati con chiavi gestite dal cliente non possono passare a un'altra sottoscrizione.
- Se si usa il portale di Azure per creare il set di crittografia del disco, non è possibile usare gli snapshot per il momento.
- I dischi gestiti crittografati con chiavi gestite dal cliente non possono anche essere crittografati con crittografia dischi di Azure.

### <a name="cli"></a>CLI
#### <a name="setting-up-your-azure-key-vault-and-diskencryptionset"></a>Configurazione di Azure Key Vault e DiskEncryptionSet

1. Verificare di avere installato la versione più recente dell'[interfaccia della riga di comando di Azure](/cli/azure/install-az-cli2) e di avere eseguito la registrazione a un account di Azure con [az login](/cli/azure/reference-index).

1. Creare un'istanza di Azure Key Vault e la chiave di crittografia.

    Quando si crea l'istanza di Key Vault, è necessario abilitare la protezione eliminazione temporanea e ripulitura. L'eliminazione temporanea garantisce che la Key Vault contenga una chiave eliminata per un determinato periodo di conservazione (valore predefinito di 90 giorno). L'eliminazione della protezione garantisce che una chiave eliminata non possa essere eliminata definitivamente fino a quando non scade il periodo di conservazione. Queste impostazioni consentono di evitare la perdita di dati a causa dell'eliminazione accidentale. Queste impostazioni sono obbligatorie quando si usa un Key Vault per la crittografia dei dischi gestiti.

    > [!IMPORTANT]
    > In caso contrario, è possibile che si verifichino problemi durante l'assegnazione di dischi aggiuntivi alla risorsa nel portale di Azure.

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

1.    Creare un'istanza di un DiskEncryptionSet. 
    
        ```azurecli
        keyVaultId=$(az keyvault show --name $keyVaultName --query [id] -o tsv)
    
        keyVaultKeyUrl=$(az keyvault key show --vault-name $keyVaultName --name $keyName --query [key.kid] -o tsv)
    
        az disk-encryption-set create -n $diskEncryptionSetName -l $location -g $rgName --source-vault $keyVaultId --key-url $keyVaultKeyUrl
        ```

1.    Concedere all'insieme di credenziali delle chiavi l'accesso alla risorsa DiskEncryptionSet. 

        > [!NOTE]
        > Potrebbero essere necessari alcuni minuti prima che Azure crei l'identità del DiskEncryptionSet nel Azure Active Directory. Se viene rilevato un errore simile a "Impossibile trovare l'oggetto Active Directory" quando si esegue il comando seguente, attendere alcuni minuti e riprovare.

        ```azurecli
        desIdentity=$(az disk-encryption-set show -n $diskEncryptionSetName -g $rgName --query [identity.principalId] -o tsv)
    
        az keyvault set-policy -n $keyVaultName -g $rgName --object-id $desIdentity --key-permissions wrapkey unwrapkey get
    
        az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId
        ```

#### <a name="create-a-vm-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Creare una VM usando un'immagine del Marketplace, crittografando il sistema operativo e i dischi dati con chiavi gestite dal cliente

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


#### <a name="encrypt-existing-managed-disks"></a>Crittografare i dischi gestiti esistenti 

I dischi esistenti non devono essere collegati a una macchina virtuale in esecuzione per poterli crittografare usando lo script seguente:

```azurecli
rgName=yourResourceGroupName
diskName=yourDiskName
diskEncryptionSetName=yourDiskEncryptionSetName
 
az disk update -n $diskName -g $rgName --encryption-type EncryptionAtRestWithCustomerKey --disk-encryption-set $diskEncryptionSetId
```

#### <a name="create-a-virtual-machine-scale-set-using-a-marketplace-image-encrypting-the-os-and-data-disks-with-customer-managed-keys"></a>Creare un set di scalabilità di macchine virtuali usando un'immagine del Marketplace, crittografando il sistema operativo e i dischi dati con chiavi gestite dal cliente

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

#### <a name="create-an-empty-disk-encrypted-using-server-side-encryption-with-customer-managed-keys-and-attach-it-to-a-vm"></a>Creare un disco vuoto crittografato usando la crittografia lato server con chiavi gestite dal cliente e associarlo a una macchina virtuale

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

#### <a name="find-the-status-of-server-side-encryption-of-a-disk"></a>Individuare lo stato della crittografia lato server di un disco

```azurecli

az disk show -g yourResourceGroupName -n yourDiskName --query [encryption.type] -o tsv

```

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory Azure AD a un'altra, l'identità gestita associata a Managed Disks non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per ulteriori informazioni, vedere [trasferimento di una sottoscrizione tra Azure ad directory](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente, un'identità gestita viene assegnata automaticamente alle risorse dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o il disco gestito da una directory Azure AD a un'altra, l'identità gestita associata a Managed Disks non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per ulteriori informazioni, vedere [trasferimento di una sottoscrizione tra Azure ad directory](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Crittografia lato server rispetto a crittografia dischi di Azure

[Crittografia dischi di Azure per le macchine virtuali e i set di scalabilità](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) di macchine virtuali sfrutta la funzionalità [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) di Windows e la funzionalità [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) di Linux per crittografare i dischi gestiti con chiavi gestite dal cliente all'interno della VM guest.  La crittografia lato server con chiavi gestite dal cliente migliora l'ADE consentendo di usare qualsiasi tipo di sistema operativo e immagini per le macchine virtuali crittografando i dati nel servizio di archiviazione.

## <a name="next-steps"></a>Passaggi successivi

- [Esplorare i modelli di Azure Resource Manager per la creazione di dischi crittografati con chiavi gestite dal cliente](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Informazioni sull'insieme di credenziali delle chiavi di Azure](../../key-vault/general/overview.md)
- [Replicare i computer con i dischi abilitati per le chiavi gestite dal cliente](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [Configurare il ripristino di emergenza di VM VMware in Azure con PowerShell](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Configurare il ripristino di emergenza in Azure per le macchine virtuali Hyper-V tramite PowerShell e Azure Resource Manager](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
