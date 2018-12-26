---
title: Crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault | Microsoft Docs
description: La funzionalità Crittografia del servizio di archiviazione di Azure consente di crittografare Archiviazione BLOB di Azure e File di Azure sul lato del servizio durante l'archiviazione dei dati e di decrittografarlo durante il recupero dei dati usando chiavi gestite dal cliente.
services: storage
author: lakasa
ms.service: storage
ms.topic: article
ms.date: 10/11/2018
ms.author: lakasa
ms.component: common
ms.openlocfilehash: 5ef9c15d4edf62ef63b16765f16971a9be5ca58b
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/06/2018
ms.locfileid: "52970706"
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Crittografia del servizio di archiviazione di Azure con chiavi gestite dal cliente in Azure Key Vault

In Microsoft Azure la protezione e la salvaguardia dei dati sono essenziali per soddisfare i criteri di sicurezza e conformità dell'organizzazione. Uno dei metodi usati dalla piattaforma di archiviazione di Azure per proteggere i dati è la crittografia del servizio di archiviazione, che crittografa i dati durante la scrittura nella risorsa di archiviazione e li decrittografa durante il recupero. Le operazioni di crittografia e decrittografia sono automatiche e trasparenti e usano la [crittografia AES](https://wikipedia.org/wiki/Advanced_Encryption_Standard) a 256 bit, uno dei più efficaci algoritmi di crittografia a blocchi.

È possibile usare con SSE le chiavi di crittografia gestite da Microsoft oppure con le proprie chiavi di crittografia. Questo articolo descrive come usare le chiavi di crittografia personali. Per altre informazioni sull'uso di chiavi gestite da Microsoft o sulla crittografia del servizio di archiviazione in generale, vedere [Crittografia del servizio di archiviazione per dati inattivi](storage-service-encryption.md).

La crittografia del servizio di archiviazione per Archiviazione BLOB di Azure e File di Azure è integrata con Azure Key Vault per consentire la gestione delle chiavi di crittografia con un insieme di credenziali delle chiavi. È possibile creare chiavi di crittografia personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API di Azure Key Vault per generare chiavi di crittografia. Con Azure Key Vault è possibile gestire e controllare le chiavi e anche controllarne l'utilizzo.

> [!Note]  
> La crittografia del servizio di archiviazione con le chiavi gestite dal cliente non è disponibile per [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). [Crittografia dischi di Azure](../../security/azure-security-disk-encryption-overview.md) usa gli standard di settore [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) in Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) in Linux per offrire una soluzione di crittografia integrata con Key Vault.

Le chiavi personalizzate sono utili perché offrono maggiore flessibilità, consentendo di creare, ruotare, disabilitare e definire i controlli di accesso. Con queste chiavi è anche possibile controllare le chiavi di crittografia usate per proteggere i dati.

## <a name="get-started-with-customer-managed-keys"></a>Introduzione alle chiavi gestite dal cliente

Per usare chiavi gestite dal cliente con la crittografia SSE è possibile creare un nuovo insieme di credenziali delle chiavi e una nuova chiave oppure usare un insieme di credenziali delle chiavi e una chiave già esistenti. L'account di archiviazione e l'insieme di credenziali chiave devono essere nella stessa area, ma possono appartenere a sottoscrizioni diverse.

### <a name="step-1-create-a-storage-account"></a>Passaggio 1: Creare un account di archiviazione

Creare prima di tutto un account di archiviazione, se non ne è già disponibile uno. Per altre informazioni, vedere [Creare un account di archiviazione](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>Passaggio 2: abilitare la crittografia del servizio di archiviazione per l'archiviazione BLOB e file

Per abilitare la crittografia SSE usando chiavi gestite dal cliente, è necessario abilitare in Azure Key Vault anche due funzionalità di sicurezza delle chiavi, per l'eliminazione temporanea e la protezione dall'eliminazione. Ciò consente di evitare che le chiavi vengano eliminate intenzionalmente o accidentalmente. Il periodo massimo di conservazione delle chiavi è impostato su 90 giorni, in modo da proteggere gli utenti da azioni di malintenzionati o attacchi ransomware.

Se si vogliono abilitare a livello di codice le chiavi gestite dal cliente per la crittografia SSE, è possibile usare l'[API REST del provider di risorse di Archiviazione di Azure](https://docs.microsoft.com/rest/api/storagerp), la [libreria client dei provider delle risorse di archiviazione per .NET](https://docs.microsoft.com/dotnet/api), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) o l'[interfaccia della riga di comando di Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Per usare chiavi gestite dal cliente con la crittografia SSE, è necessario assegnare un'identità all'account di archiviazione. È possibile impostare l'identità eseguendo il seguente comando di PowerShell o dell'interfaccia della riga di comando di Azure:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

```azurecli-interactive
az storage account \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --assign-identity
```

È possibile abilitare l'eliminazione temporanea e la protezione dall'eliminazione eseguendo i seguenti comandi di PowerShell o dell'interfaccia della riga di comando di Azure:

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

```azurecli-interactive
az resource update \
    --id $(az keyvault show --name <vault_name> -o tsv | awk '{print $1}') \
    --set properties.enableSoftDelete=true

az resource update \
    --id $(az keyvault show --name <vault_name> -o tsv | awk '{print $1}') \
    --set properties.enablePurgeProtection=true
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>Passaggio 3: abilitare la crittografia con chiavi gestite dal cliente

Per impostazione predefinita, la crittografia SSE usa chiavi gestite da Microsoft. È possibile abilitare la crittografia SSE con chiavi gestite dal cliente per l'account di archiviazione usando il [portale di Azure](https://portal.azure.com/). Nel pannello **Impostazioni** relativo all'account di archiviazione fare clic su **Crittografia**. Selezionare l'opzione **Usare una chiave personalizzata**, come illustrato nella figura seguente.

![Schermata del portale che mostra l'opzione Crittografia](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>Passaggio 4: selezionare la chiave

È possibile impostare la chiave specificando un URI oppure selezionandola da un insieme di credenziali delle chiavi.

#### <a name="specify-a-key-as-a-uri"></a>Specificare una chiave da un URI

Per specificare la chiave da un URI, seguire questi passaggi:

1. Scegliere l'opzione **Immettere l'URI della chiave**.
2. Nel campo **URI della chiave** specificare l'URI.

   ![Screenshot del portale che visualizza l'opzione di crittografia Immettere l'URI della chiave](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

#### <a name="specify-a-key-from-a-key-vault"></a>Specificare una chiave da un insieme di credenziali delle chiavi

Per specificare la chiave da un insieme di credenziali delle chiavi, seguire questi passaggi:

1. Scegliere l'opzione **Selezionare la chiave dall'insieme di credenziali delle chiavi**.
2. Scegliere l'insieme di credenziali delle chiavi contenente la chiave da usare.
3. Scegliere la chiave dall'insieme di credenziali delle chiavi.

   ![Screenshot del portale che visualizza l'opzione di crittografia Usare una chiave personalizzata](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Se l'account di archiviazione non ha accesso all'insieme di credenziali delle chiavi, è possibile eseguire il comando di Azure PowerShell illustrato nell'immagine seguente per concedere l'accesso.

![Screenshot del portale che visualizza l'accesso negato all'insieme di credenziali delle chiavi](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

È anche possibile concedere l'accesso tramite il portale di Azure passando ad Azure Key Vault nel portale e attivando l'accesso all'account di archiviazione.

È possibile associare la chiave precedente con un account di archiviazione esistente usando i comandi di PowerShell seguenti:

```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzureRmKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzureRmStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```

### <a name="step-5-copy-data-to-storage-account"></a>Passaggio 5: copiare i dati nell'account di archiviazione

Trasferire i dati nel nuovo account di archiviazione in modo che sia crittografato. Per altre informazioni, vedere [Domande frequenti su Crittografia del servizio di archiviazione](storage-service-encryption.md#faq-for-storage-service-encryption).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>Passaggio 6: eseguire query relative allo stato dei dati crittografati

Eseguire query relative allo stato dei dati crittografati.

## <a name="faq-for-sse-with-customer-managed-keys"></a>Domande frequenti sulla crittografia SSE con chiavi gestite dal cliente

**È possibile usare chiavi gestite dal cliente con la crittografia SSE se si usa l'archiviazione Premium?**  
Sì, la crittografia del servizio di archiviazione con chiavi gestite da Microsoft e chiavi gestite dal cliente è supportata sia nell'archiviazione Standard sia nell'archiviazione Premium.

**È possibile creare nuovi account di archiviazione con la crittografia del servizio di archiviazione con chiavi gestite dal cliente abilitate usando Azure PowerShell e l'interfaccia della riga di comando di Azure?**  
Sì.

**Qual è il costo aggiuntivo del servizio Archiviazione di Azure se si usano chiavi gestite dal cliente con la crittografia del servizio di archiviazione?**  
Per l'uso di Azure Key Vault è previsto un costo. Per altre informazioni, visitare la [pagina relativa ai prezzi di Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). Non sono previsti costi aggiuntivi per la crittografia SSE, che è abilitata per tutti gli account di archiviazione.

**La crittografia del servizio di archiviazione è disponibile per Azure Managed Disks?**  
La crittografia del servizio di archiviazione è disponibile per Azure Managed Disks con chiavi gestite da Microsoft, ma non con chiavi gestite dal cliente. Invece di Managed Disks che supporta la crittografia del servizio di archiviazione con le chiavi gestite dal cliente, è consigliabile usare [Crittografia dischi di Azure](../../security/azure-security-disk-encryption-overview.md), che usa gli standard di settore [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) in Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) in Linux per offrire crittografia integrata con Key Vault.

**Quali sono le differenze tra la crittografia del servizio di archiviazione e Crittografia dischi di Azure?**  
Crittografia dischi di Azure offre l'integrazione tra soluzioni basate sul sistema operativo, ad esempio BitLocker e DM-Crypt, e Azure Key Vault. La crittografia del servizio di archiviazione offre la crittografia in modo nativo a livello piattaforma di archiviazione di Azure, al di sotto della macchina virtuale.

**È possibile revocare l'accesso alle chiavi di crittografia?**
Sì, è possibile revocare l'accesso in qualsiasi momento. Esistono diversi modi per revocare l'accesso alle chiavi. Per altre informazioni, vedere [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) (Modulo PowerShell di Azure Key Vault) e [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault) (Interfaccia della riga di comando di Azure Key Vault). La revoca dell'accesso blocca di fatto l'accesso a tutti i BLOB dell'account di archiviazione poiché il servizio Archiviazione di Azure non può accedere alla chiave di crittografia dell'account.

**È possibile creare un account di archiviazione e una chiave in aree diverse?**  
No, l'account di archiviazione, Azure Key Vault e la chiave devono trovarsi nella stessa area.

**È possibile abilitare chiavi gestite dal cliente per la crittografia del servizio di archiviazione mentre si crea l'account di archiviazione?**  
 No. Quando si crea l'account di archiviazione, per la crittografia SSE sono disponibili solo chiavi gestite da Microsoft. Per usare chiavi gestite dal cliente, è necessario aggiornare le proprietà dell'account di archiviazione. È possibile usare REST o una delle librerie client di archiviazione per aggiornare l'account di archiviazione a livello di codice oppure aggiornare le proprietà dell'account di archiviazione tramite il portale di Azure dopo la creazione dell'account.

**È possibile disabilitare la crittografia mentre si usano chiavi gestite dal cliente con la crittografia del servizio di archiviazione?**  
No, non è possibile disabilitarla. Per Archiviazione Blob di Azure, File di Azure, Coda di Azure e Archiviazione tabelle di Azure la crittografia è abilitata per impostazione predefinita. È tuttavia possibile passare dall'uso di chiavi gestite da Microsoft all'uso di chiavi gestite dal cliente e viceversa.

**La crittografia del servizio di archiviazione è abilitata quando si crea un nuovo account di archiviazione?**  
La crittografia del servizio di archiviazione è abilitata per tutti gli account di archiviazione e per Archiviazione BLOB di Azure, File di Azure, Archiviazione code di Azure e Archiviazione tabelle di Azure.

**La crittografia del servizio di archiviazione con chiavi gestite dal cliente non viene abilitata per l'account di archiviazione. Per quale motivo?**  
Accertarsi di usare un account di archiviazione di Azure Resource Manager. Gli account di archiviazione di tipo classico non sono supportati con le chiavi gestite dal cliente. La crittografia SSE con chiavi gestite dal cliente può essere abilitata solo sugli account di archiviazione di Resource Manager.

**A cosa servono le impostazioni per l'eliminazione temporanea e la protezione dall'eliminazione? È necessario abilitarle per usare la crittografia SSE con chiavi gestite dal cliente?**  
Per usare la crittografia del servizio di archiviazione con chiavi gestite dal cliente, è necessario abilitare le impostazioni per l'eliminazione temporanea e la protezione dall'eliminazione. Ciò consente di assicurarsi che le chiavi non vengano eliminate intenzionalmente o accidentalmente. Il periodo massimo di conservazione delle chiavi è impostato su 90 giorni, in modo da proteggere gli utenti da azioni di malintenzionati o attacchi ransomware. Questa impostazione non può essere disabilitata.

**La crittografia del servizio di archiviazione con chiavi gestite dal cliente è disponibile solo in aree specifiche?**  
La crittografia del servizio di archiviazione con chiavi gestite dal cliente è disponibile in tutte le aree per Archiviazione BLOB di Azure e per File di Azure.

**Chi si può contattare per ottenere assistenza in caso di problemi o per inviare commenti e suggerimenti?**  
Per qualsiasi problema relativo alla crittografia del servizio di archiviazione, contattare l'indirizzo [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com).

## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sul set completo di funzionalità di sicurezza che consentono agli sviluppatori di creare applicazioni protette, vedere [Guida alla sicurezza di Archiviazione di Azure](storage-security-guide.md).
- Per informazioni generali su Azure Key Vault , vedere [Cos'è l'insieme di credenziali chiave di Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)
- Per la guida introduttiva di Azure Key Vault, vedere [Introduzione all'insieme di credenziali delle chiavi di Azure](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
