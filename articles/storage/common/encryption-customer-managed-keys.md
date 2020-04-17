---
title: Usare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure per gestire la crittografia dell'accountUse customer-managed keys with Azure Key Vault to manage account encryption
titleSuffix: Azure Storage
description: È possibile usare la propria chiave di crittografia per proteggere i dati nell'account di archiviazione. Quando si specifica una chiave gestita dal cliente, tale chiave viene utilizzata per proteggere e controllare l'accesso alla chiave che crittografa i dati. Le chiavi gestite dal cliente offrono una maggiore flessibilità per gestire i controlli di accesso.
services: storage
author: tamram
ms.service: storage
ms.date: 03/12/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: b2755d5aa5dbaa669fa2fdd8b84596e040b5dd6b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81456822"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Usare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure per gestire la crittografia di Archiviazione di AzureUse customer-managed keys with Azure Key Vault to manage Azure Storage encryption

È possibile usare la propria chiave di crittografia per proteggere i dati nell'account di archiviazione. Quando si specifica una chiave gestita dal cliente, tale chiave viene utilizzata per proteggere e controllare l'accesso alla chiave che crittografa i dati. Le chiavi gestite dal cliente offrono una maggiore flessibilità per gestire i controlli di accesso.

È necessario usare l'insieme di credenziali delle chiavi di Azure per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API dell'insieme di credenziali delle chiavi di Azure per generare le chiavi. L'account di archiviazione e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse. Per altre informazioni su Archiviazione delle chiavi di Azure, vedere [Che cos'è l'insieme di](../../key-vault/general/overview.md)credenziali delle chiavi di Azure?.

## <a name="about-customer-managed-keys"></a>Informazioni sulle chiavi gestite dal cliente

Il diagramma seguente mostra come Archiviazione di Azure usa Azure Active Directory e l'insieme di credenziali delle chiavi di Azure per effettuare richieste usando la chiave gestita dal cliente:The following diagram shows how Azure Storage uses Azure Active Directory and Azure Key Vault to make requests using the customer-managed key:

![Diagramma che mostra il funzionamento delle chiavi gestite dal cliente in Archiviazione di Azure](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

Nell'elenco seguente vengono illustrati i passaggi numerati nel diagramma:

1. Un amministratore di Archiviazione chiavi di Azure concede le autorizzazioni per le chiavi di crittografia all'identità gestita associata all'account di archiviazione.
2. Un amministratore di Archiviazione di Azure configura la crittografia con una chiave gestita dal cliente per l'account di archiviazione.
3. Azure Storage uses the managed identity that's associated with the storage account to authenticate access to Azure Key Vault via Azure Active Directory.
4. Azure Storage wraps the account encryption key with the customer key in Azure Key Vault.
5. Per le operazioni di lettura/scrittura, Archiviazione di Azure invia richieste all'insieme di credenziali delle chiavi di Azure per annullare il wrapping della chiave di crittografia dell'account per eseguire operazioni di crittografia e decrittografia.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Creare un account che supporti le chiavi gestite dal cliente per code e tabelleCreate an account that supports customer-managed keys for queues and tables

I dati archiviati nei servizi di coda e tabelle non vengono protetti automaticamente da una chiave gestita dal cliente quando le chiavi gestite dal cliente sono abilitate per l'account di archiviazione. Facoltativamente, è possibile configurare questi servizi nel momento in cui si crea l'account di archiviazione da includere in questa protezione.

Per ulteriori informazioni su come creare un account di archiviazione che supporti le chiavi gestite dal cliente per code e tabelle, vedere [Creare un account che supporti le chiavi gestite dal cliente per tabelle e code.](account-encryption-key-create.md)

I dati nei servizi BLOB e file sono sempre protetti da chiavi gestite dal cliente quando le chiavi gestite dal cliente sono configurate per l'account di archiviazione.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Abilitare le chiavi gestite dal cliente per un account di archiviazioneEnable customer-managed keys for a storage account

Le chiavi gestite dal cliente possono essere abilitate solo per gli account di archiviazione esistenti. È necessario eseguire il provisioning dell'insieme di credenziali delle chiavi con criteri di accesso che concedono le autorizzazioni chiave all'identità gestita associata all'account di archiviazione. L'identità gestita è disponibile solo dopo la creazione dell'account di archiviazione.

Quando si configura una chiave gestita dal cliente, Archiviazione di Azure esegue il wrapping della chiave di crittografia dei dati radice per l'account con la chiave gestita dal cliente nell'insieme di credenziali delle chiavi associato. L'abilitazione delle chiavi gestite dal cliente non influisce sulle prestazioni e ha effetto immediato.

Quando si modifica la chiave usata per la crittografia di Archiviazione di Azure abilitando o disabilitando le chiavi gestite dal cliente, aggiornando la versione della chiave o specificando una chiave diversa, la crittografia della chiave radice cambia, ma non è necessario crittografare nuovamente i dati nell'account di archiviazione di Azure.When you modify the key being used for Azure Storage encryption by enabling or disabling customer-managed keys, updating the key version, or specifying a different key, then the encryption of the root key changes, but the data in your Azure Storage account does not need to be re-encrypted.

Quando si abilitano o disabilitano le chiavi gestite dal cliente o quando si modifica la chiave o la versione della chiave, la protezione della chiave di crittografia radice cambia, ma non è necessario crittografare nuovamente i dati nell'account di archiviazione di Azure.When you enable or disable customer managed keys, or when you modify the key or the key version, the protection of the root encryption key changes, but the data in your Azure Storage account s need to be re-encrypted.

Per informazioni su come usare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi di Azure per la crittografia di Archiviazione di Azure, vedere uno degli articoli seguenti:To learn how to use customer-managed keys with Azure Key Vault for Azure Storage encryption, see one of these articles:

- [Configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi per la crittografia di Archiviazione di Azure dal portale di AzureConfigure customer-managed keys with Key Vault for Azure Storage encryption from the Azure portal](storage-encryption-keys-portal.md)
- [Configurare le chiavi gestite dal cliente con Key Vault per la crittografia di Archiviazione di Azure da PowerShellConfigure customer-managed keys with Key Vault for Azure Storage encryption from PowerShell](storage-encryption-keys-powershell.md)
- [Configurare le chiavi gestite dal cliente con l'insieme delle chiavi per la crittografia di Archiviazione di Azure dall'interfaccia della riga di comando di AzureConfigure customer-managed keys with Key Vault for Azure Storage encryption from Azure CLI](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure AD. Le identità gestite attualmente non supportano gli scenari tra directory. Quando si configurano le chiavi gestite dal cliente nel portale di Azure, un'identità gestita viene assegnata automaticamente all'account di archiviazione sotto copertura. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o l'account di archiviazione da una directory di Azure AD a un'altra, l'identità gestita associata all'account di archiviazione non viene trasferita al nuovo tenant, pertanto le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere Trasferimento di una sottoscrizione tra directory di Azure AD nelle domande frequenti e problemi noti con le identità gestite per le risorse di Azure.For more information, see **Transferring** a subscription between Azure AD directories in [FAQs and known issues with managed identities for Azure resources.](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories)  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Archiviare le chiavi gestite dal cliente nell'insieme di credenziali delle chiavi di AzureStore customer-managed keys in Azure Key Vault

Per abilitare le chiavi gestite dal cliente in un account di archiviazione, è necessario usare un insieme di credenziali delle chiavi di Azure per archiviare le chiavi. È necessario attivare entrambe le proprietà **Soft Delete** e **Do Not Purge** nell'insieme di credenziali delle chiavi.

Solo le chiavi RSA e RSA-HSM a 2048 bit sono supportate con la crittografia di Archiviazione di Azure.Only 2048-bit RSA and RSA-HSM keys are supported with Azure Storage encryption. Per altre informazioni sulle chiavi, vedere **Chiavi dell'insieme di credenziali delle chiavi** in Informazioni su chiavi, segreti e certificati dell'insieme di credenziali delle chiavi di [Azure.](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys)

## <a name="rotate-customer-managed-keys"></a>Ruotare le chiavi gestite dal cliente

È possibile ruotare una chiave gestita dal cliente in Archiviazione delle chiavi di Azure in base ai criteri di conformità. Quando la chiave viene ruotata, è necessario aggiornare l'account di archiviazione per usare la nuova versione della chiave URI. Per informazioni su come aggiornare l'account di archiviazione per usare una nuova versione della chiave nel portale di Azure, vedere la sezione **relativa all'aggiornamento della versione della chiave** in [Configurare le chiavi gestite dal cliente per Archiviazione di Azure tramite il portale](storage-encryption-keys-portal.md)di Azure.

La rotazione della chiave non attiva la ricrittografia dei dati nell'account di archiviazione. Non è richiesta alcuna ulteriore azione da parte dell'utente.

## <a name="revoke-access-to-customer-managed-keys"></a>Revoca dell'accesso alle chiavi gestite dal cliente

È possibile revocare l'accesso dell'account di archiviazione alla chiave gestita dal cliente in qualsiasi momento. Dopo la revoca dell'accesso alle chiavi gestite dal cliente o dopo che la chiave è stata disabilitata o eliminata, i client non possono chiamare operazioni che leggono o scrivono in un BLOB o nei relativi metadati. I tentativi di chiamare una delle seguenti operazioni avranno esito negativo con codice di errore 403 (Accesso negato) per tutti gli utenti:

- [Elenca BLOB](/rest/api/storageservices/list-blobs), quando `include=metadata` viene chiamato con il parametro nell'URI della richiesta
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Snapshot Blob](/rest/api/storageservices/snapshot-blob), quando `x-ms-meta-name` viene chiamato con l'intestazione della richiesta
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Copia BLOB da URL](/rest/api/storageservices/copy-blob-from-url)
- [Impostare il livello di BLOB](/rest/api/storageservices/set-blob-tier)
- [Put Block](/rest/api/storageservices/put-block)
- [Inserisci blocco da URL](/rest/api/storageservices/put-block-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Aggiungi blocco da URL](/rest/api/storageservices/append-block-from-url)
- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Page](/rest/api/storageservices/put-page)
- [Inserisci pagina da URL](/rest/api/storageservices/put-page-from-url)
- [BLOB di copia incrementale](/rest/api/storageservices/incremental-copy-blob)

Per chiamare nuovamente queste operazioni, ripristinare l'accesso alla chiave gestita dal cliente.

Tutte le operazioni sui dati non elencate in questa sezione possono procedere dopo la revoca delle chiavi gestite dal cliente o la disabilitazione o l'eliminazione di una chiave.

Per revocare l'accesso alle chiavi gestite dal cliente, usare [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) o [l'interfaccia della riga di comando](storage-encryption-keys-cli.md#revoke-customer-managed-keys)di Azure.

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Chiavi gestite dal cliente per i dischi gestiti di AzureCustomer-managed keys for Azure managed disks

Le chiavi gestite dal cliente sono disponibili anche per la gestione della crittografia dei dischi gestiti di Azure.Customer-managed keys are also available for managing encryption of Azure managed disks. Le chiavi gestite dal cliente si comportano in modo diverso per i dischi gestiti rispetto alle risorse di Archiviazione di Azure.Customer-managed keys behave differently for managed disks than for Azure Storage resources. Per altre informazioni, vedere [Crittografia lato server dei dischi gestiti di Azure](../../virtual-machines/windows/disk-encryption.md) per la crittografia lato Windows o Crittografia [lato server dei dischi gestiti di Azure](../../virtual-machines/linux/disk-encryption.md) per Linux.For more information, see Server-side encryption of Azure managed disks for Windows or Server side encryption of Azure managed disks for Linux.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare le chiavi gestite dal cliente con l'insieme di credenziali delle chiavi per la crittografia di Archiviazione di Azure dal portale di AzureConfigure customer-managed keys with Key Vault for Azure Storage encryption from the Azure portal](storage-encryption-keys-portal.md)
- [Configurare le chiavi gestite dal cliente con Key Vault per la crittografia di Archiviazione di Azure da PowerShellConfigure customer-managed keys with Key Vault for Azure Storage encryption from PowerShell](storage-encryption-keys-powershell.md)
- [Configurare le chiavi gestite dal cliente con l'insieme delle chiavi per la crittografia di Archiviazione di Azure dall'interfaccia della riga di comando di AzureConfigure customer-managed keys with Key Vault for Azure Storage encryption from Azure CLI](storage-encryption-keys-cli.md)
- [Crittografia di Archiviazione di Azure per i dati inattiviAzure Storage encryption for data at rest](storage-service-encryption.md)
