---
title: Crittografia dischi di Azure con Azure AD (versione precedente)
description: Questo articolo illustra i prerequisiti per l'uso di Crittografia dischi di Azure per le macchine virtuali IaaS.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 025d02ccdf38e72682cf67cc07a8b2edd549e599
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82081575"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Crittografia dischi di Azure con Azure AD (versione precedente)

**La nuova versione di crittografia dischi di Azure Elimina la necessità di fornire un Azure AD parametro dell'applicazione per abilitare la crittografia del disco della macchina virtuale. Con la nuova versione non è più necessario fornire le credenziali di Azure AD durante il passaggio abilitare la crittografia. Tutte le nuove macchine virtuali devono essere crittografate senza i parametri dell'applicazione Azure AD usando la nuova versione. Per visualizzare le istruzioni per abilitare la crittografia del disco della macchina virtuale con la nuova versione, vedere [crittografia dischi di Azure per macchine virtuali Windows](disk-encryption-overview.md). Le macchine virtuali già crittografate con Azure AD parametri dell'applicazione sono ancora supportate e continueranno a essere gestite con la sintassi di AAD.**

Questo articolo integra [crittografia dischi di Azure per le macchine virtuali Windows](disk-encryption-overview.md) con requisiti e prerequisiti aggiuntivi per crittografia dischi di azure con Azure ad (versione precedente). La sezione [macchine virtuali e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems) rimane invariata.

## <a name="networking-and-group-policy"></a> Rete e Criteri di gruppo

**Per abilitare la funzionalità Crittografia dischi di Azure usando la sintassi precedente dei parametri AAD, le macchine virtuali IaaS devono soddisfare i requisiti di configurazione degli endpoint di rete seguenti:** 
  - Per ottenere un token per la connessione all'insieme di credenziali delle chiavi, è necessario che la macchina virtuale IaaS possa connettersi a un endpoint di Azure Active Directory, \[login.microsoftonline.com\].
  - Per scrivere le chiavi di crittografia nell'insieme di credenziali delle chiavi, è necessario che la macchina virtuale IaaS possa connettersi all'endpoint dell'insieme di credenziali delle chiavi.
  - La VM IaaS deve potersi connettere a un endpoint di archiviazione di Azure che ospita il repository delle estensioni di Azure e a un account di archiviazione di Azure che ospita i file del disco rigido virtuale.
  -  Se i criteri di sicurezza limitano l'accesso dalle macchine virtuali di Azure a Internet, è possibile risolvere l'URI precedente e configurare una regola specifica per consentire la connettività in uscita agli indirizzi IP. Per altre informazioni, vedere [Azure Key Vault protetto da firewall](../../key-vault/key-vault-access-behind-firewall.md).
  - La macchina virtuale da crittografare deve essere configurata in modo da usare TLS 1,2 come protocollo predefinito. Se TLS 1,0 è stato disabilitato in modo esplicito e la versione di .NET non è stata aggiornata a 4,6 o versione successiva, la modifica del registro di sistema seguente consentirà a ADE di selezionare la versione più recente di TLS:

```console
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001

[HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
"SystemDefaultTlsVersions"=dword:00000001
"SchUseStrongCrypto"=dword:00000001` 
```

**Criteri di gruppo:**
 - La soluzione Crittografia dischi di Azure usa la protezione con chiave esterna BitLocker per macchine virtuali IaaS Windows. Per le macchine virtuali aggiunte a un dominio, non eseguire il push di criteri di gruppo che applichino protezioni TPM. Per informazioni sui Criteri di gruppo per consentire BitLocker senza un TPM compatibile, vedere [BitLocker Group Policy Reference](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1) (Informazioni di riferimento sui Criteri di gruppo BitLocker).

-  I criteri di BitLocker nelle macchine virtuali appartenenti a un dominio con criteri di gruppo personalizzati devono includere l'impostazione seguente: [Configura archiviazione utente delle informazioni di ripristino di BitLocker-> Consenti chiave di ripristino a 256 bit](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Crittografia dischi di Azure avrà esito negativo quando le impostazioni di Criteri di gruppo personalizzate per BitLocker sono incompatibili. Sulle macchine sprovviste delle corrette impostazioni di criteri, applicare i nuovi criteri, forzare l'aggiornamento dei criteri (gpupdate.exe /force) e, dopodiché, potrebbe essere necessario riavviare.  

## <a name="encryption-key-storage-requirements"></a>Requisiti di archiviazione delle chiavi di crittografia  

Crittografia dischi di Azure richiede Azure Key Vault per controllare e gestire segreti e chiavi di crittografia dei dischi. L'insieme di credenziali delle chiavi e le macchine virtuali devono risiedere nella stessa area e sottoscrizione di Azure.

Per informazioni dettagliate, vedere [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure con Azure ad (versione precedente)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Passaggi successivi

- [Creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure con Azure AD (versione precedente)](disk-encryption-key-vault-aad.md)
- [Abilitare crittografia dischi di Azure con Azure AD nelle VM Windows (versione precedente)](disk-encryption-windows-aad.md)
- [Script dell'interfaccia della riga di comando dei prerequisiti di Crittografia dischi di Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Script di PowerShell dei prerequisiti di Crittografia dischi di Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
