---
title: Crittografia di Archiviazione di Azure per i dati inattiviAzure Storage encryption for data at rest
description: Archiviazione di Azure protegge i dati crittografandoli automaticamente prima di salvarlo nel cloud. È possibile fare affidamento sulle chiavi gestite da Microsoft per la crittografia dei dati nell'account di archiviazione oppure gestire la crittografia con le proprie chiavi.
services: storage
author: tamram
ms.service: storage
ms.date: 04/10/2020
ms.topic: conceptual
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: f112a4523bc5af9ecae57e93dfb90795d3fe9c50
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81113268"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Crittografia di Archiviazione di Azure per i dati inattiviAzure Storage encryption for data at rest

Archiviazione di Azure crittografa automaticamente i dati quando vengono resi persistenti nel cloud. La crittografia di Archiviazione di Azure protegge i dati e consente di soddisfare gli impegni di sicurezza e conformità dell'organizzazione.

## <a name="about-azure-storage-encryption"></a>Informazioni sulla crittografia di Archiviazione di AzureAbout Azure Storage encryption

I dati in Archiviazione di Azure vengono crittografati e decrittografati in modo trasparente utilizzando la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit, una delle più forti crittografie a blocchi disponibili ed è compatibile con FIPS 140-2. Azure Storage encryption is similar to BitLocker encryption on Windows.

La crittografia di Archiviazione di Azure è abilitata per tutti gli account di archiviazione, inclusi Resource Manager e gli account di archiviazione classici. La crittografia di Archiviazione di Azure non può essere disabilitata. Poiché i dati sono protetti per impostazione predefinita, non è necessario modificare il codice o le applicazioni per sfruttare i vantaggi della crittografia di Archiviazione di Azure.Because your data is secured by default, you don't need to modify your code or applications to take advantage of Azure Storage encryption.

I dati in un account di archiviazione vengono crittografati indipendentemente dal livello di prestazioni (standard o premium), dal livello di accesso (a caldo o a freddo) o dal modello di distribuzione (Azure Resource Manager o versione classica). Vengono crittografati anche tutti i BLOB nel livello di archiviazione. Tutte le opzioni di ridondanza di Archiviazione di Azure supportano la crittografia e tutti i dati nelle aree primaria e secondaria vengono crittografati quando è abilitata la replica geografica. Tutte le risorse di Archiviazione di Azure vengono crittografate, inclusi BLOB, dischi, file, code e tabelle. Anche tutti i metadati dell'oggetto vengono crittografati. Non sono previsti costi aggiuntivi per la crittografia di Archiviazione di Azure.There is no additional cost for Azure Storage encryption.

Ogni BLOB in blocchi, BLOB di accodamento o BLOB di pagine scritto in Archiviazione di Azure dopo il 20 ottobre 2017 viene crittografato. I BLOB creati prima di questa data continuano a essere crittografati da un processo in background. Per forzare la crittografia di un BLOB creato prima del 20 ottobre 2017, è possibile riscrivere il BLOB. Per informazioni su come controllare lo stato di crittografia di un BLOB, vedere [Controllare lo stato di crittografia di un BLOB.](../blobs/storage-blob-encryption-status.md)

Per altre informazioni sui moduli di crittografia sottostanti la crittografia di Archiviazione di Azure, vedere API di [crittografia: nuova generazione.](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

## <a name="about-encryption-key-management"></a>Informazioni sulla gestione delle chiavi di crittografia

I dati in un nuovo account di archiviazione vengono crittografati con chiavi gestite da Microsoft.Data in a new storage account is encrypted with Microsoft-managed keys. È possibile fare affidamento sulle chiavi gestite da Microsoft per la crittografia dei dati oppure è possibile gestire la crittografia con chiavi personalizzate. Se si sceglie di gestire la crittografia con le proprie chiavi, sono disponibili due opzioni:

- È possibile specificare una chiave gestita dal cliente con l'insieme di credenziali delle chiavi di Azure da usare per crittografare e decrittografare i dati nell'archiviazione BLOB e nei file di Azure.You can specify a *customer-managed key* with Azure Key Vault to use for encrypting and decrypting data in Blob storage and in Azure Files. <sup>1,2</sup> Per altre informazioni sulle chiavi gestite dal cliente, vedere Usare chiavi gestite dal cliente con L'insieme di credenziali delle chiavi di Azure per gestire la crittografia di Archiviazione di Azure.1,2 For more information about customer-managed keys, see [Use customer-managed keys with Azure Key Vault to manage Azure Storage encryption](encryption-customer-managed-keys.md).
- È possibile specificare una *chiave fornita dal cliente* nelle operazioni di archiviazione BLOB. Un client che effettua una richiesta di lettura o scrittura sull'archiviazione BLOB può includere una chiave di crittografia nella richiesta di controllo granulare sulla crittografia e decrittografia dei dati BLOB. Per altre informazioni sulle chiavi fornite dal cliente, vedere Fornire una chiave di [crittografia in una richiesta di archiviazione BLOB (anteprima).](encryption-customer-provided-keys.md)

La tabella seguente confronta le opzioni di gestione delle chiavi per la crittografia di Archiviazione di Azure.The following table compares key management options for Azure Storage encryption.

|                                        |    Chiavi gestite da Microsoft                             |    Chiavi gestite dal cliente                                                                                                                        |    Chiavi fornite dal cliente                                                          |
|----------------------------------------|-------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------|
|    Operazioni di crittografia/decrittografia    |    Azure                                              |    Azure                                                                                                                                        |    Azure                                                                         |
|    Servizi di Archiviazione di Azure supportatiAzure Storage services supported    |    Tutti                                                |    Archiviazione BLOB, File di Azure<sup>1,2BLOB</sup> storage, Azure Files 1,2                                                                                                               |    Archiviazione BLOB                                                                  |
|    Memorizzazione delle chiavi                         |    Archivio chiavi Microsoft    |    Insieme di credenziali chiave di Azure                                                                                                                              |    Archivio chiavi di Azure o qualsiasi altro archivio chiaviAzure Key Vault or any other key store                                                                 |
|    Responsabilità di rotazione chiave         |    Microsoft                                          |    Customer                                                                                                                                     |    Customer                                                                      |
|    Controllo dei tasti                          |    Microsoft                                     |    Customer                                                                                                                    |    Customer                                                                 |

<sup>1</sup> Per informazioni sulla creazione di un account che supporta l'utilizzo di chiavi gestite dal cliente con l'archiviazione delle code, vedere [Creare un account che supporti le chiavi gestite dal cliente per le code](account-encryption-key-create.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).<br />
<sup>2</sup> Per informazioni sulla creazione di un account che supporti l'utilizzo di chiavi gestite dal cliente con archiviazione tabelle, vedere [Creare un account che supporti le chiavi gestite dal cliente per](account-encryption-key-create.md?toc=%2fazure%2fstorage%2ftables%2ftoc.json)le tabelle.

## <a name="next-steps"></a>Passaggi successivi

- [Informazioni sull'insieme di credenziali delle chiavi di Azure](../../key-vault/key-vault-overview.md)
- [Configurare chiavi gestite dal cliente per la crittografia di Archiviazione di Azure dal portale di Azure](storage-encryption-keys-portal.md)
- [Configurare chiavi gestite dal cliente per la crittografia di Archiviazione di Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Configurare chiavi gestite dal cliente per la crittografia di Archiviazione di Azure dall'interfaccia della riga di comando di Azure](storage-encryption-keys-cli.md)
