---
title: Crittografia di archiviazione di Azure per dati inattivi
description: Archiviazione di Azure protegge i dati mediante la crittografia automatica prima di renderli permanente nel cloud. È possibile fare affidamento sulle chiavi gestite da Microsoft per la crittografia dell'account di archiviazione oppure è possibile gestire la crittografia con chiavi personalizzate.
services: storage
author: tamram
ms.service: storage
ms.date: 11/26/2019
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 63fa30b4cf4c5887e8fb44b357eb22e55fe230e7
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666138"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Crittografia di archiviazione di Azure per dati inattivi

Archiviazione di Azure crittografa automaticamente i dati quando vengono salvati in modo permanente nel cloud. La crittografia di archiviazione di Azure protegge i tuoi dati e ti aiuta a soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

## <a name="about-azure-storage-encryption"></a>Informazioni sulla crittografia di archiviazione di Azure

I dati in archiviazione di Azure vengono crittografati e decrittografati in modo trasparente usando la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2. La crittografia di archiviazione di Azure è simile alla crittografia BitLocker per Windows.

La crittografia di archiviazione di Azure è abilitata per tutti i nuovi account di archiviazione, inclusi Gestione risorse e gli account di archiviazione classici. La crittografia di archiviazione di Azure non può essere disabilitata. Poiché i dati sono protetti per impostazione predefinita, non è necessario modificare il codice o le applicazioni per sfruttare la crittografia di archiviazione di Azure.

Gli account di archiviazione vengono crittografati indipendentemente dal livello di prestazioni (standard o Premium) o dal modello di distribuzione (Azure Resource Manager o classica). Tutte le opzioni di ridondanza di archiviazione di Azure supportano la crittografia e tutte le copie di un account di archiviazione vengono crittografate. Tutte le risorse di archiviazione di Azure vengono crittografate, inclusi BLOB, dischi, file, code e tabelle. Vengono crittografati anche tutti i metadati degli oggetti.

La crittografia non influisce sulle prestazioni di archiviazione di Azure. Non sono previsti costi aggiuntivi per la crittografia di archiviazione di Azure.

Ogni BLOB in blocchi, BLOB di accodamento o BLOB di pagine che è stato scritto in archiviazione di Azure dopo il 20 ottobre 2017 è crittografato. I BLOB creati prima di questa data continuano a essere crittografati da un processo in background. Per forzare la crittografia di un blob creato prima del 20 ottobre 2017, è possibile riscrivere il BLOB. Per informazioni su come controllare lo stato di crittografia di un BLOB, vedere [controllare lo stato della crittografia di un BLOB](../blobs/storage-blob-encryption-status.md).

Per altre informazioni sui moduli di crittografia sottostanti la crittografia di archiviazione di Azure, vedere [Cryptography API: Next Generation](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

È possibile fare affidamento sulle chiavi gestite da Microsoft per la crittografia dell'account di archiviazione oppure è possibile gestire la crittografia con chiavi personalizzate. Se si sceglie di gestire la crittografia con le proprie chiavi, sono disponibili due opzioni:

- È possibile specificare una *chiave gestita dal cliente* con Azure Key Vault da usare per crittografare e decrittografare tutti i dati nell'account di archiviazione. Una chiave gestita dal cliente viene usata per crittografare tutti i dati in tutti i servizi dell'account di archiviazione.
- È possibile specificare una *chiave fornita dal cliente* per le operazioni di archiviazione BLOB. Un client che effettua una richiesta di lettura o scrittura sull'archiviazione BLOB può includere una chiave di crittografia sulla richiesta per un controllo granulare sulla modalità di crittografia e decrittografia dei dati BLOB.

La tabella seguente confronta le opzioni di gestione delle chiavi per la crittografia di archiviazione di Azure.

|                                        |    Chiavi gestite da Microsoft                             |    Chiavi gestite dal cliente                                                                                                                        |    Chiavi fornite dal cliente                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operazioni di crittografia/decrittografia    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Servizi di archiviazione di Azure supportati    |    Tutto                                                |    Archiviazione BLOB, File di Azure                                                                                                               |    Archiviazione BLOB                                                                  |
|    Archiviazione chiavi                         |    Archivio chiavi Microsoft    |    Azure Key Vault                                                                                                                              |    Azure Key Vault o qualsiasi altro archivio chiavi                                                                 |
|    Responsabilità della rotazione delle chiavi         |    Microsoft                                          |    Customer                                                                                                                                     |    Customer                                                                      |
|    Uso della chiave                           |    Microsoft                                          |    Portale di Azure, API REST provider di risorse di archiviazione, librerie di gestione archiviazione di Azure, PowerShell, interfaccia della riga di comando        |    API REST di archiviazione di Azure (archiviazione BLOB), librerie client di archiviazione di Azure    |
|    Accesso alle chiavi                          |    Solo Microsoft                                     |    Microsoft, cliente                                                                                                                    |    Solo cliente                                                                 |

Le sezioni seguenti descrivono in modo più dettagliato tutte le opzioni per la gestione delle chiavi.

## <a name="microsoft-managed-keys"></a>Chiavi gestite da Microsoft

Per impostazione predefinita, l'account di archiviazione usa le chiavi di crittografia gestite da Microsoft. È possibile visualizzare le impostazioni di crittografia per l'account di archiviazione nella sezione **crittografia** del [portale di Azure](https://portal.azure.com), come illustrato nella figura seguente.

![Visualizzare l'account crittografato con le chiavi gestite da Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chiavi gestite dal cliente con Azure Key Vault

È possibile gestire la crittografia di archiviazione di Azure a livello dell'account di archiviazione con le proprie chiavi. Quando si specifica una chiave gestita dal cliente al livello dell'account di archiviazione, questa chiave viene usata per crittografare e decrittografare tutti i dati nell'account di archiviazione, inclusi i dati BLOB, di Accodamento, di file e di tabella. Le chiavi gestite dal cliente offrono maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati.

È necessario utilizzare Azure Key Vault per archiviare le chiavi gestite dal cliente. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. L'account di archiviazione e l'insieme di credenziali chiave devono essere nella stessa area, ma possono appartenere a sottoscrizioni diverse. Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](../../key-vault/key-vault-overview.md).

Questo diagramma mostra il modo in cui archiviazione di Azure usa Azure Active Directory e Azure Key Vault per eseguire richieste usando la chiave gestita dal cliente:

![Diagramma che illustra il funzionamento delle chiavi gestite dal cliente in archiviazione di Azure](media/storage-service-encryption/encryption-customer-managed-keys-diagram.png)

Nell'elenco seguente vengono illustrati i passaggi numerati nel diagramma:

1. Un amministratore Azure Key Vault concede le autorizzazioni per le chiavi di crittografia all'identità gestita associata all'account di archiviazione.
2. Un amministratore di archiviazione di Azure configura la crittografia con una chiave gestita dal cliente per l'account di archiviazione.
3. Archiviazione di Azure usa l'identità gestita associata all'account di archiviazione per autenticare l'accesso ai Azure Key Vault tramite Azure Active Directory.
4. Archiviazione di Azure esegue il wrapping della chiave di crittografia dell'account con la chiave Customer in Azure Key Vault.
5. Per le operazioni di lettura/scrittura, archiviazione di Azure invia richieste a Azure Key Vault per eseguire il wrapping e annullare il wrapping della chiave di crittografia dell'account per eseguire operazioni di crittografia e decrittografia.

### <a name="enable-customer-managed-keys-for-a-storage-account"></a>Abilitare le chiavi gestite dal cliente per un account di archiviazione

Quando si Abilita la crittografia con chiavi gestite dal cliente per un account di archiviazione, archiviazione di Azure esegue il wrapping della chiave di crittografia dell'account con la chiave Customer nell'insieme di credenziali delle chiavi associato. L'abilitazione delle chiavi gestite dal cliente non influisca sulle prestazioni e l'account viene crittografato con la nuova chiave immediatamente, senza alcun ritardo di tempo.

Un nuovo account di archiviazione viene sempre crittografato con le chiavi gestite da Microsoft. Non è possibile abilitare le chiavi gestite dal cliente al momento della creazione dell'account. Le chiavi gestite dal cliente vengono archiviate in Azure Key Vault e l'insieme di credenziali delle chiavi deve essere sottoposto a provisioning con criteri di accesso che concedono autorizzazioni chiave all'identità gestita associata all'account di archiviazione. L'identità gestita è disponibile solo dopo la creazione dell'account di archiviazione.

Per informazioni su come usare le chiavi gestite dal cliente con Azure Key Vault per la crittografia di archiviazione di Azure, vedere uno di questi articoli:

- [Configurare chiavi gestite dal cliente con Key Vault per la crittografia di archiviazione di Azure dalla portale di Azure](storage-encryption-keys-portal.md)
- [Configurare chiavi gestite dal cliente con Key Vault per la crittografia di archiviazione di Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Configurare chiavi gestite dal cliente con Key Vault per la crittografia di archiviazione di Azure dall'interfaccia della riga di comando di Azure](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si configurano le chiavi gestite dal cliente nel portale di Azure, un'identità gestita viene automaticamente assegnata all'account di archiviazione dietro le quinte. Se successivamente si sposta la sottoscrizione, il gruppo di risorse o l'account di archiviazione da una directory Azure AD a un'altra, l'identità gestita associata all'account di archiviazione non viene trasferita al nuovo tenant, quindi le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere **trasferimento di una sottoscrizione tra Azure ad directory** nelle [domande frequenti e problemi noti relativi alle identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Archiviare chiavi gestite dal cliente in Azure Key Vault

Per abilitare le chiavi gestite dal cliente in un account di archiviazione, è necessario usare un Azure Key Vault per archiviare le chiavi. È necessario abilitare l' **eliminazione** temporanea e non **ripulire** le proprietà nell'insieme di credenziali delle chiavi.

L'insieme di credenziali delle chiavi deve trovarsi nella stessa sottoscrizione dell'account di archiviazione. Archiviazione di Azure Usa identità gestite per le risorse di Azure per l'autenticazione all'insieme di credenziali delle chiavi per le operazioni di crittografia e decrittografia. Le identità gestite attualmente non supportano gli scenari tra directory.

### <a name="rotate-customer-managed-keys"></a>Ruotare le chiavi gestite dal cliente

È possibile ruotare una chiave gestita dal cliente in Azure Key Vault in base ai criteri di conformità. Quando la chiave viene ruotata, è necessario aggiornare l'account di archiviazione per usare il nuovo URI della chiave. Per informazioni su come aggiornare l'account di archiviazione per usare una nuova versione della chiave nella portale di Azure, vedere la sezione intitolata **aggiornare la versione della chiave** in [configurare chiavi gestite dal cliente per archiviazione di Azure usando il portale di Azure](storage-encryption-keys-portal.md).

La rotazione della chiave non attiva la nuova crittografia dei dati nell'account di archiviazione. Non sono necessarie altre azioni da parte dell'utente.

### <a name="revoke-access-to-customer-managed-keys"></a>Revocare l'accesso alle chiavi gestite dal cliente

Per revocare l'accesso alle chiavi gestite dal cliente, usare PowerShell o l'interfaccia della riga di comando di Azure. Per altre informazioni, vedere [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) o l'interfaccia della riga di comando di [Azure Key Vault](/cli/azure/keyvault). La revoca dell'accesso blocca efficacemente l'accesso a tutti i dati nell'account di archiviazione, in quanto la chiave di crittografia non è accessibile da parte di archiviazione di Azure.

### <a name="customer-managed-keys-for-azure-managed-disks-preview"></a>Chiavi gestite dal cliente per Azure Managed Disks (anteprima)

Le chiavi gestite dal cliente sono disponibili anche per la gestione della crittografia di Azure Managed Disks (anteprima). Le chiavi gestite dal cliente hanno un comportamento diverso per i dischi gestiti rispetto alle risorse di archiviazione di Azure. Per altre informazioni, vedere [crittografia lato server di Azure Managed disks](../../virtual-machines/windows/disk-encryption.md) per Windows o la [crittografia lato server di Azure Managed disks](../../virtual-machines/linux/disk-encryption.md) per Linux.

## <a name="customer-provided-keys-preview"></a>Chiavi fornite dal cliente (anteprima)

I client che effettuano richieste nell'archivio BLOB di Azure hanno la possibilità di fornire una chiave di crittografia per una singola richiesta. L'inclusione della chiave di crittografia nella richiesta offre un controllo granulare sulle impostazioni di crittografia per le operazioni di archiviazione BLOB. Le chiavi fornite dal cliente (anteprima) possono essere archiviate in Azure Key Vault o in un altro archivio chiavi.

Per un esempio in cui viene illustrato come specificare una chiave fornita dal cliente per una richiesta all'archiviazione BLOB, vedere [specificare una chiave fornita dal cliente per una richiesta all'archivio BLOB con .NET](../blobs/storage-blob-customer-provided-key.md). 

### <a name="encrypting-read-and-write-operations"></a>Crittografia delle operazioni di lettura e scrittura

Quando un'applicazione client fornisce una chiave di crittografia per la richiesta, archiviazione di Azure esegue in modo trasparente la crittografia e la decrittografia durante la lettura e la scrittura dei dati BLOB. Archiviazione di Azure scrive un hash SHA-256 della chiave di crittografia insieme al contenuto del BLOB. L'hash viene usato per verificare che tutte le operazioni successive eseguite sul BLOB usino la stessa chiave di crittografia. 

Archiviazione di Azure non archivia o gestisce la chiave di crittografia che il client invia con la richiesta. La chiave viene eliminata in modo sicuro non appena il processo di crittografia o decrittografia è completo.

Quando un client crea o aggiorna un BLOB usando una chiave fornita dal cliente, anche le successive richieste di lettura e scrittura per tale BLOB devono fornire la chiave. Se la chiave non viene fornita su una richiesta di un BLOB che è già stata crittografata con una chiave fornita dal cliente, la richiesta ha esito negativo con codice di errore 409 (conflitto).

Se l'applicazione client invia una chiave di crittografia per la richiesta e l'account di archiviazione viene crittografato anche usando una chiave gestita da Microsoft o una chiave gestita dal cliente, archiviazione di Azure usa la chiave fornita nella richiesta per la crittografia e la decrittografia.

Per inviare la chiave di crittografia come parte della richiesta, un client deve stabilire una connessione sicura ad archiviazione di Azure tramite HTTPS.

Ogni snapshot BLOB può avere una propria chiave di crittografia.

### <a name="request-headers-for-specifying-customer-provided-keys"></a>Intestazioni della richiesta per specificare le chiavi fornite dal cliente

Per le chiamate REST, i client possono usare le intestazioni seguenti per passare in modo sicuro le informazioni sulla chiave di crittografia per una richiesta all'archiviazione BLOB:

|Intestazione di richiesta | Description |
|---------------|-------------|
|`x-ms-encryption-key` |Obbligatorio per le richieste di scrittura e di lettura. Valore della chiave di crittografia AES-256 con codifica Base64. |
|`x-ms-encryption-key-sha256`| Obbligatorio per le richieste di scrittura e di lettura. SHA256 con codifica Base64 della chiave di crittografia. |
|`x-ms-encryption-algorithm` | Obbligatorio per le richieste di scrittura, facoltativo per le richieste di lettura. Specifica l'algoritmo da utilizzare per la crittografia dei dati utilizzando la chiave specificata. Deve essere AES256. |

La specifica delle chiavi di crittografia nella richiesta è facoltativa. Tuttavia, se si specifica una delle intestazioni elencate in precedenza per un'operazione di scrittura, è necessario specificarle tutte.

### <a name="blob-storage-operations-supporting-customer-provided-keys"></a>Operazioni di archiviazione BLOB che supportano chiavi fornite dal cliente

Le operazioni di archiviazione BLOB seguenti supportano l'invio di chiavi di crittografia fornite dal cliente su una richiesta:

- [Put Blob](/rest/api/storageservices/put-blob)
- [Elenco Put Block](/rest/api/storageservices/put-block-list)
- [Put Block](/rest/api/storageservices/put-block)
- [Inserisci blocco da URL](/rest/api/storageservices/put-block-from-url)
- [Put Page](/rest/api/storageservices/put-page)
- [Inserisci pagina dall'URL](/rest/api/storageservices/put-page-from-url)
- [Append Block](/rest/api/storageservices/append-block)
- [Impostare le proprietà BLOB](/rest/api/storageservices/set-blob-properties)
- [Set Blob Metadata](/rest/api/storageservices/set-blob-metadata)
- [Ottenere un BLOB](/rest/api/storageservices/get-blob)
- [Ottenere le proprietà del BLOB](/rest/api/storageservices/get-blob-properties)
- [Ottenere i metadati del BLOB](/rest/api/storageservices/get-blob-metadata)
- [BLOB snapshot](/rest/api/storageservices/snapshot-blob)

### <a name="rotate-customer-provided-keys"></a>Ruotare le chiavi fornite dal cliente

Per ruotare una chiave di crittografia passata per la richiesta, scaricare il BLOB e caricarlo nuovamente con la nuova chiave di crittografia.

> [!IMPORTANT]
> Non è possibile usare la portale di Azure per leggere o scrivere in un contenitore o in un BLOB crittografato con una chiave fornita nella richiesta.
>
> Assicurarsi di proteggere la chiave di crittografia fornita su una richiesta di archiviazione BLOB in un archivio chiavi sicuro come Azure Key Vault. Se si tenta di eseguire un'operazione di scrittura su un contenitore o un BLOB senza la chiave di crittografia, l'operazione avrà esito negativo e si perderà l'accesso all'oggetto.

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Crittografia di archiviazione di Azure rispetto alla crittografia del disco

La crittografia di archiviazione di Azure crittografa i BLOB di pagine che eseguono il backup dei dischi delle macchine virtuali di Azure. Inoltre, tutti i dischi delle macchine virtuali di Azure, inclusi i dischi temporanei locali, possono essere facoltativamente crittografati con [crittografia dischi di Azure](../../security/azure-security-disk-encryption-overview.md). Crittografia dischi di Azure usa [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) standard del settore in Windows e [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) in Linux per fornire soluzioni di crittografia basate sul sistema operativo integrate con Azure Key Vault.

## <a name="next-steps"></a>Passaggi successivi

- [Cos'è l'insieme di credenziali chiave di Azure?](../../key-vault/key-vault-overview.md)
- [Configurare chiavi gestite dal cliente per la crittografia di Archiviazione di Azure dal portale di Azure](storage-encryption-keys-portal.md)
- [Configurare chiavi gestite dal cliente per la crittografia di Archiviazione di Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Configurare chiavi gestite dal cliente per la crittografia di Archiviazione di Azure dall'interfaccia della riga di comando di Azure](storage-encryption-keys-cli.md)
