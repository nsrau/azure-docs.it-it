---
title: Crittografia dischi di Azure con Azure AD (versione precedente)
description: Questo articolo illustra i prerequisiti per l'uso di Crittografia dischi di Azure per le macchine virtuali IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 33b257e9d344fc31df072509f105d2e8fd1bd29b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/10/2019
ms.locfileid: "72245175"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Crittografia dischi di Azure con Azure AD (versione precedente)

**La nuova versione di Crittografia dischi di Azure elimina la necessità di specificare un parametro dell'applicazione Azure AD per abilitare la crittografia dei dischi per le macchine virtuali. Con la nuova versione, non è più necessario specificare le credenziali di Azure AD durante il passaggio di abilitazione della crittografia. Tutte le nuove macchine virtuali devono essere crittografate senza i parametri dell'applicazione Azure AD. Per visualizzare le istruzioni per abilitare la crittografia del disco della macchina virtuale con la nuova versione, vedere [crittografia dischi di Azure per macchine virtuali Windows](disk-encryption-overview.md). Le macchine virtuali che sono già state crittografate con i parametri dell'applicazione Azure AD sono ancora supportate e dovrebbero continuare a essere gestite con la sintassi di AAD.**

Questo articolo integra [crittografia dischi di Azure per le macchine virtuali Windows](disk-encryption-overview.md) con requisiti e prerequisiti aggiuntivi per crittografia dischi di azure con Azure ad (versione precedente). La sezione [macchine virtuali e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems) rimane invariata.

## <a name="networking-and-group-policy"></a>Rete e Criteri di gruppo

**Per abilitare la funzionalità Crittografia dischi di Azure usando la sintassi precedente dei parametri AAD, le macchine virtuali IaaS devono soddisfare i requisiti di configurazione degli endpoint di rete seguenti:** 
  - Per ottenere un token per la connessione all'insieme di credenziali delle chiavi, è necessario che la macchina virtuale IaaS possa connettersi a un endpoint di Azure Active Directory, \[login.microsoftonline.com\].
  - Per scrivere le chiavi di crittografia nell'insieme di credenziali delle chiavi, è necessario che la macchina virtuale IaaS possa connettersi all'endpoint dell'insieme di credenziali delle chiavi.
  - La VM IaaS deve potersi connettere a un endpoint di archiviazione di Azure che ospita il repository delle estensioni di Azure e a un account di archiviazione di Azure che ospita i file del disco rigido virtuale.
  -  Se i criteri di sicurezza limitano l'accesso dalle macchine virtuali di Azure a Internet, è possibile risolvere l'URI precedente e configurare una regola specifica per consentire la connettività in uscita agli indirizzi IP. Per altre informazioni, vedere [Azure Key Vault protetto da firewall](../../key-vault/key-vault-access-behind-firewall.md).
  - In Windows, se TLS 1.0 è stato disabilitato esplicitamente e .NET non è stato aggiornato almeno alla versione 4.6, la seguente modifica del Registro di sistema consentirà a ADE di selezionare una versione TLS più recente:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

**Criteri di gruppo:**
 - La soluzione Crittografia dischi di Azure usa la protezione con chiave esterna BitLocker per macchine virtuali IaaS Windows. Per le macchine virtuali aggiunte a un dominio, non eseguire il push di criteri di gruppo che applichino protezioni TPM. Per informazioni sui Criteri di gruppo per consentire BitLocker senza un TPM compatibile, vedere [BitLocker Group Policy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1) (Informazioni di riferimento sui Criteri di gruppo BitLocker).

-  I criteri di BitLocker nelle macchine virtuali aggiunte a un dominio con criteri di gruppo personalizzati devono includere l'impostazione seguente: [Configurare l'archiviazione utente delle informazioni di ripristino di BitLocker: > consentire la chiave di ripristino a 256 bit](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Crittografia dischi di Azure avrà esito negativo quando le impostazioni di Criteri di gruppo personalizzate per BitLocker sono incompatibili. Sulle macchine sprovviste delle corrette impostazioni di criteri, applicare i nuovi criteri, forzare l'aggiornamento dei criteri (gpupdate.exe /force) e, dopodiché, potrebbe essere necessario riavviare.  

## <a name="encryption-key-storage-requirements"></a>Requisiti di archiviazione delle chiavi di crittografia  

Crittografia dischi di Azure richiede un Azure Key Vault per controllare e gestire le chiavi e i segreti di crittografia del disco. L'insieme di credenziali delle chiavi e le macchine virtuali devono trovarsi nella stessa area e nella stessa sottoscrizione di Azure.

Per informazioni dettagliate, vedere [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure con Azure ad (versione precedente)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Passaggi successivi

- [Creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure con Azure AD (versione precedente)](disk-encryption-key-vault-aad.md)
- [Abilitare crittografia dischi di Azure con Azure AD nelle VM Windows (versione precedente)](disk-encryption-windows-aad.md)
- [Script dell'interfaccia della riga di comando dei prerequisiti di crittografia dischi di Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Script di PowerShell per prerequisiti di crittografia dischi di Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)