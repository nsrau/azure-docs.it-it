---
title: Crittografia di archiviazione di Azure per i dati inattivi | Microsoft Docs
description: Archiviazione di Azure protegge i dati mediante la crittografia automatica prima di renderli permanente nel cloud. Tutti i dati in un'archiviazione di Azure vengono crittografati e decrittografati in modo trasparente con la crittografia AES a 256 bit e sono conformi a FIPS 140-2.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 2abaa994bf507c3ffb65199af9ac609ece138ea4
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71673054"
---
# <a name="azure-storage-encryption-for-data-at-rest"></a>Crittografia di archiviazione di Azure per dati inattivi

Archiviazione di Azure crittografa automaticamente i dati in modo permanente nel cloud. La crittografia protegge i dati e ti aiuta a soddisfare gli impegni di sicurezza e conformità dell'organizzazione. I dati in archiviazione di Azure vengono crittografati e decrittografati in modo trasparente usando la [crittografia AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard)a 256 bit, una delle crittografie a blocchi più solide disponibili ed è conforme a FIPS 140-2. La crittografia di archiviazione di Azure è simile alla crittografia BitLocker per Windows.

La crittografia di archiviazione di Azure è abilitata per tutti gli account di archiviazione nuovi ed esistenti e non può essere disabilitata. Poiché i dati sono protetti per impostazione predefinita, non è necessario modificare il codice o le applicazioni per sfruttare la crittografia di archiviazione di Azure. 

Gli account di archiviazione vengono crittografati indipendentemente dal livello di prestazioni (standard o Premium) o dal modello di distribuzione (Azure Resource Manager o classica). Tutte le opzioni di ridondanza di archiviazione di Azure supportano la crittografia e tutte le copie di un account di archiviazione vengono crittografate. Tutte le risorse di archiviazione di Azure vengono crittografate, inclusi BLOB, dischi, file, code e tabelle. Vengono crittografati anche tutti i metadati degli oggetti.

La crittografia non influisce sulle prestazioni di archiviazione di Azure. Non sono previsti costi aggiuntivi per la crittografia di archiviazione di Azure.

Per altre informazioni sui moduli di crittografia sottostanti la crittografia di archiviazione di Azure, vedere [Cryptography API: generazione successiva](https://docs.microsoft.com/windows/desktop/seccng/cng-portal).

## <a name="key-management"></a>Gestione della chiave

È possibile utilizzare chiavi gestite da Microsoft per la crittografia dell'account di archiviazione oppure è possibile gestire la crittografia con chiavi personalizzate, insieme a Azure Key Vault.

### <a name="microsoft-managed-keys"></a>Chiavi gestite da Microsoft

Per impostazione predefinita, l'account di archiviazione usa le chiavi di crittografia gestite da Microsoft. È possibile visualizzare le impostazioni di crittografia per l'account di archiviazione nella sezione **crittografia** del [portale di Azure](https://portal.azure.com), come illustrato nella figura seguente.

![Visualizzare l'account crittografato con le chiavi gestite da Microsoft](media/storage-service-encryption/encryption-microsoft-managed-keys.png)

### <a name="customer-managed-keys"></a>Chiavi gestite dal cliente

È possibile gestire la crittografia di archiviazione di Azure con chiavi gestite dal cliente. Le chiavi gestite dal cliente offrono maggiore flessibilità per creare, ruotare, disabilitare e revocare i controlli di accesso. È anche possibile controllare le chiavi di crittografia usate per proteggere i dati. 

Usare Azure Key Vault per gestire le chiavi e controllare l'utilizzo della chiave. È possibile creare chiavi personalizzate e archiviarle in un insieme di credenziali delle chiavi oppure usare le API Azure Key Vault per generare chiavi. L'account di archiviazione e l'insieme di credenziali chiave devono essere nella stessa area, ma possono appartenere a sottoscrizioni diverse. Per ulteriori informazioni su Azure Key Vault, vedere [che cos'è Azure Key Vault?](../../key-vault/key-vault-overview.md).

Per revocare l'accesso alle chiavi gestite dal cliente, vedere [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) e [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). La revoca dell'accesso blocca efficacemente l'accesso a tutti i dati nell'account di archiviazione, in quanto la chiave di crittografia non è accessibile da parte di archiviazione di Azure.

Per informazioni su come usare le chiavi gestite dal cliente con archiviazione di Azure, vedere uno di questi articoli:

- [Configurare chiavi gestite dal cliente per la crittografia di Archiviazione di Azure dal portale di Azure](storage-encryption-keys-portal.md)
- [Configurare chiavi gestite dal cliente per la crittografia di Archiviazione di Azure da PowerShell](storage-encryption-keys-powershell.md)
- [Usare chiavi gestite dal cliente con crittografia di archiviazione di Azure dall'interfaccia della riga di comando di Azure](storage-encryption-keys-cli.md)

> [!IMPORTANT]
> Le chiavi gestite dal cliente si basano sulle identità gestite per le risorse di Azure, una funzionalità di Azure Active Directory (Azure AD). Quando si trasferisce una sottoscrizione da una directory Azure AD a un'altra, le identità gestite non vengono aggiornate e le chiavi gestite dal cliente potrebbero non funzionare più. Per altre informazioni, vedere **trasferimento di una sottoscrizione tra Azure ad directory** nelle [domande frequenti e problemi noti relativi alle identità gestite per le risorse di Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

> [!NOTE]  
> Le chiavi gestite dal cliente non sono supportate per [Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="azure-storage-encryption-versus-disk-encryption"></a>Crittografia di archiviazione di Azure rispetto alla crittografia del disco

Con la crittografia di archiviazione di Azure, tutti gli account di archiviazione di Azure e le risorse che contengono sono crittografati, inclusi i BLOB di pagine che eseguono i dischi delle macchine virtuali di Azure. Inoltre, i dischi delle macchine virtuali di Azure possono essere crittografati con [crittografia dischi di Azure](../../security/azure-security-disk-encryption-overview.md). Crittografia dischi di Azure usa [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) standard del settore in Windows e [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) in Linux per fornire soluzioni di crittografia basate sul sistema operativo integrate con Azure Key Vault.

## <a name="next-steps"></a>Passaggi successivi

- [Cos'è l'insieme di credenziali chiave di Azure?](../../key-vault/key-vault-overview.md)
