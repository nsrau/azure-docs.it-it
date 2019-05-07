---
title: Crittografia di archiviazione di Azure per dati inattivi | Microsoft Docs
description: Archiviazione di Azure protegge i dati mediante crittografia automaticamente la prima persistenza nel cloud. Tutti i dati in archiviazione di Azure vengono crittografati e decrittografati utilizzando in modo trasparente la crittografia AES a 256 bit e sia conforme a FIPS 140-2.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 6eb7de7810ce23aed4031cca9f038da7149a6f9c
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153079"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Crittografia di archiviazione di Azure per dati inattivi

Archiviazione di Azure crittografa automaticamente i dati quando si rende persistente nel cloud. Protegge i dati e per aiutare a soddisfare gli impegni di sicurezza e conformità aziendali. I dati in archiviazione di Azure vengono crittografati e decrittografati in modo trasparente con 256 bit [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), algoritmi di crittografia a uno dei blocchi più efficaci e sia conforme a FIPS 140-2. Crittografia di archiviazione di Azure è simile alla crittografia di BitLocker in Windows.

Crittografia di archiviazione di Azure è abilitata per tutti gli account di archiviazione nuovi ed esistenti e non può essere disabilitata. Poiché i dati sono protetti per impostazione predefinita, non devi modificare il codice o applicazioni per sfruttare i vantaggi della crittografia di archiviazione di Azure. 

Gli account di archiviazione vengono crittografati indipendentemente dal livello di prestazioni (standard o premium) o il modello di distribuzione (classica o Azure Resource Manager). Tutte le opzioni di ridondanza di archiviazione di Azure supportano la crittografia, e tutte le copie di un account di archiviazione vengono crittografate. Vengono crittografate tutte le risorse di archiviazione di Azure, tra cui BLOB, dischi, file, code e tabelle. Tutti i metadati dell'oggetto vengono anche crittografati.

Crittografia non influenza le prestazioni di archiviazione di Azure. Non sono previsti costi aggiuntivi per la crittografia di archiviazione di Azure.

Per altre informazioni sui moduli crittografici sottostante la crittografia di archiviazione di Azure, vedere [Cryptography API: generazione successiva](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="key-management"></a>Gestione della chiave

È possibile usare chiavi gestite da Microsoft per la crittografia dell'account di archiviazione oppure è possibile gestire la crittografia con le proprie chiavi, insieme a Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Chiavi gestite da Microsoft

Per impostazione predefinita, l'account di archiviazione Usa le chiavi di crittografia gestite da Microsoft. È possibile visualizzare le impostazioni di crittografia per l'account di archiviazione nel **crittografia** sezione il [portale di Azure](https://portal.azure.com), come illustrato nell'immagine seguente.

![Visualizza un account crittografato con chiavi gestite da Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Chiavi gestite dal cliente

È possibile gestire la crittografia di archiviazione di Azure con chiavi gestite dal cliente. Le chiavi gestite dal cliente consentono una maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati. 

Usare Azure Key Vault per gestire le chiavi e controllarne l'uso. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure è possibile usare le API di Azure Key Vault per generare le chiavi. L'account di archiviazione e l'insieme di credenziali chiave devono essere nella stessa area, ma possono appartenere a sottoscrizioni diverse. Per altre informazioni su Azure Key Vault, vedere [cos'è Azure Key Vault?](../../key-vault/key-vault-overview.md).

Per revocare l'accesso alle chiavi gestite dal cliente, vedere [insieme di credenziali delle chiavi di Azure PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [CLI di Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault). Per revocare l'accesso in modo efficace blocca l'accesso a tutti i dati nell'account di archiviazione, come la chiave di crittografia non è accessibile da archiviazione di Azure.

Per informazioni su come usare chiavi gestite dal cliente con archiviazione di Azure, vedere uno di questi articoli:

- [Configurare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure dal portale di Azure](storage-encryption-keys-portal.md)
- [Configurare le chiavi gestite dal cliente per la crittografia di archiviazione di Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Usare chiavi gestite dal cliente con crittografia di archiviazione di Azure della riga di comando di Azure](storage-encryption-keys-cli.md)

> [!NOTE]  
> Le chiavi gestite dal cliente non sono supportate per [Azure managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Crittografia di archiviazione di Azure e crittografia del disco

Grazie alla crittografia di archiviazione di Azure, tutti gli account di archiviazione di Azure e le risorse contenute vengono crittografate, compresi i BLOB di pagine che supportano i dischi di macchina virtuale di Azure. Inoltre, i dischi di macchina virtuale di Azure possono essere crittografati con [crittografia dischi di Azure](../../security/azure-security-disk-encryption-overview.md). Crittografia dischi di Azure Usa standard di settore [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) in Windows e [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) in Linux per fornire soluzioni di crittografia in base al sistema operativo sono integrate con Azure Key Vault.

## <a name="next-steps"></a>Passaggi successivi

- [Cos'è l'insieme di credenziali chiave di Azure?](../../key-vault/key-vault-overview.md)
