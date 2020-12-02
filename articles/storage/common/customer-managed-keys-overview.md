---
title: Chiavi gestite dal cliente per la crittografia dell'account
titleSuffix: Azure Storage
description: È possibile usare la propria chiave di crittografia per proteggere i dati nell'account di archiviazione. Quando si specifica una chiave gestita dal cliente, tale chiave viene usata per proteggere e controllare l'accesso alla chiave che crittografa i dati. Le chiavi gestite dal cliente offrono maggiore flessibilità per gestire i controlli di accesso.
services: storage
author: tamram
ms.service: storage
ms.date: 09/15/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: 8eaadc031039b22e209db1023c65da39e1e096b1
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483284"
---
# <a name="customer-managed-keys-for-azure-storage-encryption"></a>Chiavi gestite dal cliente per la crittografia di archiviazione di Azure

È possibile usare la propria chiave di crittografia per proteggere i dati nell'account di archiviazione. Quando si specifica una chiave gestita dal cliente, tale chiave viene usata per proteggere e controllare l'accesso alla chiave che crittografa i dati. Le chiavi gestite dal cliente offrono maggiore flessibilità per gestire i controlli di accesso.

Per archiviare le chiavi gestite dal cliente, è necessario usare Azure Key Vault o Azure Key Vault modello di protezione hardware (HSM) gestito (anteprima). È possibile creare chiavi personalizzate e archiviarle nell'insieme di credenziali delle chiavi o nel modulo di protezione hardware gestito oppure è possibile usare le API Azure Key Vault per generare chiavi. L'account di archiviazione e l'insieme di credenziali delle chiavi o il modulo di protezione hardware gestito devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse.

Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](../../key-vault/general/overview.md).

> [!NOTE]
> Azure Key Vault e Azure Key Vault HSM gestito supportano le stesse API e le stesse interfacce di gestione per la configurazione.

## <a name="about-customer-managed-keys"></a>Informazioni sulle chiavi gestite dal cliente

Il diagramma seguente illustra in che modo archiviazione di Azure usa Azure Active Directory e un insieme di credenziali delle chiavi o un modulo di protezione hardware gestito per eseguire richieste usando la chiave gestita dal cliente:

![Diagramma che illustra il funzionamento delle chiavi gestite dal cliente in archiviazione di Azure](media/customer-managed-keys-overview/encryption-customer-managed-keys-diagram.png)

Nell'elenco seguente vengono illustrati i passaggi numerati nel diagramma:

1. Un amministratore Azure Key Vault concede le autorizzazioni per le chiavi di crittografia all'identità gestita associata all'account di archiviazione.
2. Un amministratore di archiviazione di Azure configura la crittografia con una chiave gestita dal cliente per l'account di archiviazione.
3. Archiviazione di Azure usa l'identità gestita associata all'account di archiviazione per autenticare l'accesso ai Azure Key Vault tramite Azure Active Directory.
4. Archiviazione di Azure esegue il wrapping della chiave di crittografia dell'account con la chiave Customer in Azure Key Vault.
5. Per le operazioni di lettura/scrittura, archiviazione di Azure invia richieste a Azure Key Vault per annullare il wrapping della chiave di crittografia dell'account per eseguire operazioni di crittografia e decrittografia.

## <a name="customer-managed-keys-for-queues-and-tables"></a>Chiavi gestite dal cliente per code e tabelle

I dati archiviati nella coda e nell'archiviazione tabelle non vengono protetti automaticamente da una chiave gestita dal cliente quando le chiavi gestite dal cliente sono abilitate per l'account di archiviazione. Facoltativamente, è possibile configurare questi servizi da includere in questa protezione al momento della creazione dell'account di archiviazione.

Per altre informazioni su come creare un account di archiviazione che supporti le chiavi gestite dal cliente per le code e le tabelle, vedere [creare un account che supporta chiavi gestite dal cliente per tabelle e code](account-encryption-key-create.md).

I dati nell'archiviazione BLOB e nei File di Azure sono sempre protetti dalle chiavi gestite dal cliente quando le chiavi gestite dal cliente sono configurate per l'account di archiviazione.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Abilitare le chiavi gestite dal cliente per un account di archiviazione

Quando si configura una chiave gestita dal cliente, archiviazione di Azure esegue il wrapping della chiave di crittografia dei dati radice per l'account con la chiave gestita dal cliente nell'insieme di credenziali delle chiavi associato o nel modulo di protezione hardware gestito. L'abilitazione delle chiavi gestite dal cliente non influisce sulle prestazioni e ha effetto immediato.

Quando si abilitano o disabilitano le chiavi gestite dal cliente o quando si modifica la chiave o la versione della chiave, la protezione della chiave di crittografia radice viene modificata, ma non è necessario crittografare nuovamente i dati nell'account di archiviazione di Azure.

Le chiavi gestite dal cliente possono essere abilitate solo sugli account di archiviazione esistenti. L'insieme di credenziali delle chiavi o il modulo di protezione hardware gestito deve essere configurato per concedere le autorizzazioni all'identità gestita associata all'account di archiviazione. L'identità gestita è disponibile solo dopo la creazione dell'account di archiviazione.

È possibile passare in qualsiasi momento tra chiavi gestite dal cliente e chiavi gestite da Microsoft. Per ulteriori informazioni sulle chiavi gestite da Microsoft, vedere [informazioni sulla gestione delle chiavi di crittografia](storage-service-encryption.md#about-encryption-key-management).

Per informazioni su come configurare la crittografia di archiviazione di Azure con chiavi gestite dal cliente in un insieme di credenziali delle chiavi, vedere [configurare la crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault](customer-managed-keys-configure-key-vault.md). Per configurare le chiavi gestite dal cliente in un modulo di protezione hardware gestito, vedere [configurare la crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault HSM gestito (anteprima)](customer-managed-keys-configure-key-vault-hsm.md).

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure AD. Le identità gestite attualmente non supportano gli scenari tra directory. Quando si configurano le chiavi gestite dal cliente nel portale di Azure, un'identità gestita viene automaticamente assegnata all'account di archiviazione dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o l'account di archiviazione da una directory Azure AD a un'altra, l'identità gestita associata all'account di archiviazione non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere **trasferimento di una sottoscrizione tra Azure ad directory** nelle [domande frequenti e problemi noti relativi alle identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

La crittografia di archiviazione di Azure supporta chiavi RSA e RSA-HSM di dimensioni 2048, 3072 e 4096. Per ulteriori informazioni sulle chiavi, vedere [informazioni sulle chiavi](../../key-vault/keys/about-keys.md).

L'uso di un insieme di credenziali delle chiavi o di un modulo HSM gestito ha costi associati Per ulteriori informazioni, vedere [Key Vault prezzi](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="update-the-key-version"></a>Aggiornare la versione della chiave

Quando si configura la crittografia con chiavi gestite dal cliente, sono disponibili due opzioni per l'aggiornamento della versione della chiave:

- **Aggiorna automaticamente la versione della chiave:** Per aggiornare automaticamente una chiave gestita dal cliente quando è disponibile una nuova versione, omettere la versione della chiave quando si Abilita la crittografia con chiavi gestite dal cliente per l'account di archiviazione. Se la versione della chiave viene omessa, archiviazione di Azure controlla ogni giorno l'insieme di credenziali delle chiavi o il modulo di protezione hardware gestito per una nuova versione di una chiave gestita dal cliente. Archiviazione di Azure usa automaticamente la versione più recente della chiave.
- **Aggiornare manualmente la versione della chiave:** Per usare una versione specifica di una chiave per la crittografia di archiviazione di Azure, specificare la versione della chiave quando si Abilita la crittografia con chiavi gestite dal cliente per l'account di archiviazione. Se si specifica la versione della chiave, archiviazione di Azure usa tale versione per la crittografia fino a quando non si aggiorna manualmente la versione della chiave.

    Quando la versione della chiave viene specificata in modo esplicito, è necessario aggiornare manualmente l'account di archiviazione per usare il nuovo URI della versione della chiave quando viene creata una nuova versione. Per informazioni su come aggiornare l'account di archiviazione per usare una nuova versione della chiave, vedere [configurare la crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault](customer-managed-keys-configure-key-vault.md) o [configurare la crittografia con chiavi gestite dal cliente archiviate in Azure Key Vault HSM gestito (anteprima)](customer-managed-keys-configure-key-vault-hsm.md).

Se si aggiorna la versione della chiave per una chiave gestita dal cliente, la nuova crittografia dei dati nell'account di archiviazione non viene attivata. Non sono necessarie altre azioni da parte dell'utente.

> [!NOTE]
> Per ruotare una chiave, creare una nuova versione della chiave nell'insieme di credenziali delle chiavi o nel modulo di protezione hardware gestito, in base ai criteri di conformità. È possibile ruotare la chiave manualmente o creare una funzione per ruotarla in base a una pianificazione.

## <a name="revoke-access-to-customer-managed-keys"></a>Revocare l'accesso alle chiavi gestite dal cliente

È possibile revocare l'accesso dell'account di archiviazione alla chiave gestita dal cliente in qualsiasi momento. Dopo che l'accesso alle chiavi gestite dal cliente è stato revocato o dopo che la chiave è stata disabilitata o eliminata, i client non possono chiamare operazioni che leggono o scrivono in un BLOB o i relativi metadati. I tentativi di chiamare una delle operazioni seguenti avranno esito negativo con codice di errore 403 (accesso negato) per tutti gli utenti:

- [Elencare i BLOB](/rest/api/storageservices/list-blobs), quando viene chiamato con il `include=metadata` parametro nell'URI della richiesta
- [Get Blob](/rest/api/storageservices/get-blob)
- [Get Blob Properties](/rest/api/storageservices/get-blob-properties)
- [Get Blob Metadata](/rest/api/storageservices/get-blob-metadata)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [BLOB snapshot](/rest/api/storageservices/snapshot-blob), quando viene chiamato con l' `x-ms-meta-name` intestazione della richiesta
- [Copy Blob](/rest/api/storageservices/copy-blob)
- [Copia BLOB da URL](/rest/api/storageservices/copy-blob-from-url)
- [Set Blob Tier](/rest/api/storageservices/set-blob-tier)
- [Put Block](/rest/api/storageservices/put-block)
- [Inserisci blocco da URL](/rest/api/storageservices/put-block-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Accoda blocco da URL](/rest/api/storageservices/append-block-from-url)
- [Put Blob](/rest/api/storageservices/put-blob)
- [Put Page](/rest/api/storageservices/put-page)
- [Inserisci pagina dall'URL](/rest/api/storageservices/put-page-from-url)
- [Incremental Copy Blob](/rest/api/storageservices/incremental-copy-blob)

Per chiamare nuovamente queste operazioni, ripristinare l'accesso alla chiave gestita dal cliente.

Tutte le operazioni sui dati non elencate in questa sezione possono continuare dopo la revoca delle chiavi gestite dal cliente o la disabilitazione o l'eliminazione di una chiave.

Per revocare l'accesso alle chiavi gestite dal cliente, usare [PowerShell](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys) o l'interfaccia della riga di comando di [Azure](./customer-managed-keys-configure-key-vault.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Chiavi gestite dal cliente per Azure Managed Disks

Le chiavi gestite dal cliente sono disponibili anche per la gestione della crittografia di Azure Managed Disks. Le chiavi gestite dal cliente hanno un comportamento diverso per i dischi gestiti rispetto alle risorse di archiviazione di Azure. Per altre informazioni, vedere la pagina relativa alla [crittografia lato server di Azure Managed disks](../../virtual-machines/disk-encryption.md) per Windows o la [crittografia lato server di Azure Managed disks](../../virtual-machines/disk-encryption.md) per Linux.

## <a name="next-steps"></a>Passaggi successivi

- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
- [Configurare la crittografia con le chiavi gestite dal cliente archiviate in Azure Key Vault](customer-managed-keys-configure-key-vault.md)
- [Configurare la crittografia con chiavi gestite dal cliente archiviate nel modulo di protezione hardware Azure Key Vault gestito (anteprima)](customer-managed-keys-configure-key-vault-hsm.md)