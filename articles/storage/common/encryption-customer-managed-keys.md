---
title: Usare chiavi gestite dal cliente con Azure Key Vault per gestire la crittografia dell'account
titleSuffix: Azure Storage
description: È possibile usare la propria chiave di crittografia per proteggere i dati nell'account di archiviazione. Quando si specifica una chiave gestita dal cliente, questa chiave viene usata per proteggere e controllare l'accesso alla chiave che crittografa i dati. Le chiavi gestite dal cliente offrono una maggiore flessibilità nella gestione dei controlli di accesso.
services: storage
author: tamram
ms.service: storage
ms.date: 07/20/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: d53818c91d32bc7435d1328c2ae73a8eb3172cd4
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87029791"
---
# <a name="use-customer-managed-keys-with-azure-key-vault-to-manage-azure-storage-encryption"></a>Usare chiavi gestite dal cliente con Azure Key Vault per gestire la crittografia di archiviazione di Azure

È possibile usare la propria chiave di crittografia per proteggere i dati nell'account di archiviazione. Quando si specifica una chiave gestita dal cliente, questa chiave viene usata per proteggere e controllare l'accesso alla chiave che crittografa i dati. Le chiavi gestite dal cliente offrono una maggiore flessibilità nella gestione dei controlli di accesso.

È necessario utilizzare Azure Key Vault per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. L'account di archiviazione e l'insieme di credenziali delle chiavi devono trovarsi nella stessa area e nello stesso tenant di Azure Active Directory (Azure AD), ma possono trovarsi in sottoscrizioni diverse. Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](../../key-vault/general/overview.md).

## <a name="about-customer-managed-keys"></a>Informazioni sulle chiavi gestite dal cliente

Il diagramma seguente mostra in che modo archiviazione di Azure usa Azure Active Directory e Azure Key Vault per eseguire richieste usando la chiave gestita dal cliente:

![Diagramma che illustra il funzionamento delle chiavi gestite dal cliente in archiviazione di Azure](media/encryption-customer-managed-keys/encryption-customer-managed-keys-diagram.png)

Nell'elenco seguente vengono illustrati i passaggi numerati nel diagramma:

1. Un amministratore Azure Key Vault concede le autorizzazioni per le chiavi di crittografia all'identità gestita associata all'account di archiviazione.
2. Un amministratore di archiviazione di Azure configura la crittografia con una chiave gestita dal cliente per l'account di archiviazione.
3. Archiviazione di Azure usa l'identità gestita associata all'account di archiviazione per autenticare l'accesso ai Azure Key Vault tramite Azure Active Directory.
4. Archiviazione di Azure esegue il wrapping della chiave di crittografia dell'account con la chiave Customer in Azure Key Vault.
5. Per le operazioni di lettura/scrittura, archiviazione di Azure invia richieste a Azure Key Vault per annullare il wrapping della chiave di crittografia dell'account per eseguire operazioni di crittografia e decrittografia.

## <a name="create-an-account-that-supports-customer-managed-keys-for-queues-and-tables"></a>Creare un account che supporti le chiavi gestite dal cliente per le code e le tabelle

I dati archiviati nei servizi Queue e Table non vengono protetti automaticamente da una chiave gestita dal cliente quando le chiavi gestite dal cliente sono abilitate per l'account di archiviazione. Facoltativamente, è possibile configurare questi servizi quando si crea l'account di archiviazione da includere in questa protezione.

Per altre informazioni su come creare un account di archiviazione che supporti le chiavi gestite dal cliente per le code e le tabelle, vedere [creare un account che supporta chiavi gestite dal cliente per tabelle e code](account-encryption-key-create.md).

I dati nei servizi BLOB e file sono sempre protetti dalle chiavi gestite dal cliente quando le chiavi gestite dal cliente sono configurate per l'account di archiviazione.

## <a name="enable-customer-managed-keys-for-a-storage-account"></a>Abilitare le chiavi gestite dal cliente per un account di archiviazione

Quando si configura una chiave gestita dal cliente, archiviazione di Azure esegue il wrapping della chiave di crittografia dei dati radice per l'account con la chiave gestita dal cliente nell'insieme di credenziali delle chiavi associato. L'abilitazione delle chiavi gestite dal cliente non influisce sulle prestazioni e ha effetto immediato.

Quando si abilitano o disabilitano le chiavi gestite dal cliente o quando si modifica la chiave o la versione della chiave, la protezione della chiave di crittografia radice viene modificata, ma non è necessario crittografare nuovamente i dati nell'account di archiviazione di Azure.

Le chiavi gestite dal cliente possono essere abilitate solo sugli account di archiviazione esistenti. L'insieme di credenziali delle chiavi deve essere configurato con criteri di accesso che concedono le autorizzazioni per l'identità gestita associata all'account di archiviazione. L'identità gestita è disponibile solo dopo la creazione dell'account di archiviazione.

È possibile passare in qualsiasi momento tra chiavi gestite dal cliente e chiavi gestite da Microsoft. Per ulteriori informazioni sulle chiavi gestite da Microsoft, vedere [informazioni sulla gestione delle chiavi di crittografia](storage-service-encryption.md#about-encryption-key-management).

Per informazioni su come usare le chiavi gestite dal cliente con Azure Key Vault per la crittografia di archiviazione di Azure, vedere uno di questi articoli:

- [Configurare chiavi gestite dal cliente con Key Vault per la crittografia di archiviazione di Azure dalla portale di Azure](storage-encryption-keys-portal.md)
- [Configurare chiavi gestite dal cliente con Key Vault per la crittografia di archiviazione di Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Configurare chiavi gestite dal cliente con Key Vault per la crittografia di archiviazione di Azure dall'interfaccia della riga di comando di Azure](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure AD. Le identità gestite attualmente non supportano gli scenari tra directory. Quando si configurano le chiavi gestite dal cliente nel portale di Azure, un'identità gestita viene automaticamente assegnata all'account di archiviazione dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o l'account di archiviazione da una directory Azure AD a un'altra, l'identità gestita associata all'account di archiviazione non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere **trasferimento di una sottoscrizione tra Azure ad directory** nelle [domande frequenti e problemi noti relativi alle identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="store-customer-managed-keys-in-azure-key-vault"></a>Archiviare chiavi gestite dal cliente in Azure Key Vault

Per abilitare le chiavi gestite dal cliente in un account di archiviazione, è necessario usare un insieme di credenziali delle chiavi di Azure per archiviare le chiavi. È necessario abilitare l' **eliminazione** temporanea e non **ripulire** le proprietà nell'insieme di credenziali delle chiavi.

La crittografia di archiviazione di Azure supporta chiavi RSA e RSA-HSM di dimensioni 2048, 3072 e 4096. Per ulteriori informazioni sulle chiavi, vedere **Key Vault chiavi** in [informazioni su Azure Key Vault chiavi, segreti e certificati](../../key-vault/about-keys-secrets-and-certificates.md#key-vault-keys).

L'uso di Azure Key Vault dispone di costi associati. Per ulteriori informazioni, vedere [Key Vault prezzi](/pricing/details/key-vault/).

## <a name="rotate-customer-managed-keys"></a>Ruotare le chiavi gestite dal cliente

È possibile ruotare una chiave gestita dal cliente in Azure Key Vault in base ai criteri di conformità. Per la rotazione di una chiave gestita dal cliente sono disponibili due opzioni:

- **Rotazione automatica:** Per configurare la rotazione automatica delle chiavi gestite dal cliente, omettere la versione della chiave quando si Abilita la crittografia con chiavi gestite dal cliente per l'account di archiviazione. Se la versione della chiave viene omessa, Archiviazione di Azure verifica ogni giorno su Azure Key Vault la disponibilità di una nuova versione di una chiave gestita dal cliente. Se è disponibile una nuova versione della chiave, Archiviazione di Azure usa automaticamente la versione più recente della chiave.
- **Rotazione manuale:** Per usare una particolare versione della chiave per la crittografia di archiviazione di Azure, specificare la versione della chiave quando si Abilita la crittografia con chiavi gestite dal cliente per l'account di archiviazione. Se si specifica la versione della chiave, archiviazione di Azure usa tale versione per la crittografia fino a quando non si aggiorna manualmente la versione della chiave.

    Quando la chiave viene ruotata manualmente, è necessario aggiornare l'account di archiviazione per usare il nuovo URI della versione della chiave. Per informazioni su come aggiornare l'account di archiviazione per usare una nuova versione della chiave nella portale di Azure, vedere [aggiornare manualmente la versione della chiave](storage-encryption-keys-portal.md#manually-update-the-key-version).

La rotazione di una chiave gestita dal cliente non attiva la nuova crittografia dei dati nell'account di archiviazione. Non sono necessarie altre azioni da parte dell'utente.

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

Per revocare l'accesso alle chiavi gestite dal cliente, usare [PowerShell](storage-encryption-keys-powershell.md#revoke-customer-managed-keys) o l'interfaccia della riga di comando di [Azure](storage-encryption-keys-cli.md#revoke-customer-managed-keys).

## <a name="customer-managed-keys-for-azure-managed-disks"></a>Chiavi gestite dal cliente per Azure Managed Disks

Le chiavi gestite dal cliente sono disponibili anche per la gestione della crittografia di Azure Managed Disks. Le chiavi gestite dal cliente hanno un comportamento diverso per i dischi gestiti rispetto alle risorse di archiviazione di Azure. Per altre informazioni, vedere la pagina relativa alla [crittografia lato server di Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) per Windows o la [crittografia lato server di Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) per Linux.

## <a name="next-steps"></a>Passaggi successivi

- [Configurare chiavi gestite dal cliente con Key Vault per la crittografia di archiviazione di Azure dalla portale di Azure](storage-encryption-keys-portal.md)
- [Configurare chiavi gestite dal cliente con Key Vault per la crittografia di archiviazione di Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Configurare chiavi gestite dal cliente con Key Vault per la crittografia di archiviazione di Azure dall'interfaccia della riga di comando di Azure](storage-encryption-keys-cli.md)
- [Crittografia del servizio di archiviazione di Azure per dati inattivi](storage-service-encryption.md)
