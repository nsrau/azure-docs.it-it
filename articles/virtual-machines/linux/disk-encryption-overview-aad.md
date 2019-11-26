---
title: Crittografia dischi di Azure con Azure AD prerequisiti per le app (versione precedente)
description: Questo articolo illustra i prerequisiti per l'uso di Crittografia dischi di Azure per le macchine virtuali IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: e1b9df750886af050163a85e2c6a3539bd63c733
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457190"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Crittografia dischi di Azure con Azure AD (versione precedente)

La nuova versione di crittografia dischi di Azure Elimina la necessità di fornire un parametro dell'applicazione Azure Active Directory (Azure AD) per abilitare la crittografia del disco della macchina virtuale. Con la nuova versione, non è più necessario specificare le credenziali di Azure AD durante il passaggio di abilitazione della crittografia. Tutte le nuove macchine virtuali devono essere crittografate senza i parametri dell'applicazione Azure AD usando la nuova versione. Per istruzioni su come abilitare la crittografia del disco della macchina virtuale usando la nuova versione, vedere [crittografia dischi di Azure per macchine virtuali Linux](disk-encryption-overview.md). Le macchine virtuali che sono già state crittografate con i parametri dell'applicazione Azure AD sono ancora supportate e dovrebbero continuare a essere gestite con la sintassi di AAD.

Questo articolo fornisce informazioni aggiuntive su [crittografia dischi di Azure per macchine virtuali Linux](disk-encryption-overview.md) con requisiti e prerequisiti aggiuntivi per crittografia dischi di azure con Azure ad (versione precedente).

Le informazioni contenute in queste sezioni rimangono invariate:

- [Macchine virtuali e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisiti aggiuntivi per le macchine virtuali](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a>Rete e Criteri di gruppo

Per abilitare la funzionalità Crittografia dischi di Azure usando la sintassi dei parametri AAD precedente, le macchine virtuali IaaS (Infrastructure as a Service) devono soddisfare i requisiti di configurazione degli endpoint di rete seguenti: 
  - Per ottenere un token per la connessione all'insieme di credenziali delle chiavi, la VM IaaS deve essere in grado di connettersi a un endpoint Azure AD, \[\]login.microsoftonline.com.
  - Per scrivere le chiavi di crittografia nell'insieme di credenziali delle chiavi, è necessario che la macchina virtuale IaaS possa connettersi all'endpoint dell'insieme di credenziali delle chiavi.
  - La VM IaaS deve potersi connettere a un endpoint di archiviazione di Azure che ospita il repository delle estensioni di Azure e a un account di archiviazione di Azure che ospita i file del disco rigido virtuale.
  -  Se i criteri di sicurezza limitano l'accesso da macchine virtuali di Azure a Internet, è possibile risolvere l'URI precedente e configurare una regola specifica per consentire la connettività in uscita agli indirizzi IP. Per altre informazioni, vedere [Azure Key Vault protetto da firewall](../../key-vault/key-vault-access-behind-firewall.md).
  - In Windows, se TLS 1,0 è disabilitato in modo esplicito e la versione di .NET non viene aggiornata a 4,6 o versione successiva, la modifica del registro di sistema seguente Abilita crittografia dischi di Azure per selezionare la versione più recente di TLS:
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>Criteri di gruppo
 - La soluzione Crittografia dischi di Azure usa la protezione con chiave esterna BitLocker per macchine virtuali IaaS Windows. Per le macchine virtuali appartenenti a un dominio, non eseguire il push di criteri di gruppo che applicano protezioni TPM. Per informazioni sui Criteri di gruppo per l'opzione **Consenti BitLocker senza un TPM compatibile**, vedere la pagina relativa al [riferimento criteri di gruppo BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- I criteri di BitLocker nelle macchine virtuali appartenenti a un dominio con un Criteri di gruppo personalizzato devono includere l'impostazione seguente: [Configura archiviazione utente delle informazioni di ripristino di BitLocker-> consente la chiave di ripristino a 256 bit](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Crittografia dischi di Azure non riesce se le impostazioni Criteri di gruppo personalizzate per BitLocker sono incompatibili. Nei computer che non hanno l'impostazione dei criteri corretta, applicare il nuovo criterio, forzare l'aggiornamento del nuovo criterio (GPUpdate. exe/Force), quindi riavviare se necessario. 

## <a name="encryption-key-storage-requirements"></a>Requisiti di archiviazione delle chiavi di crittografia 

Crittografia dischi di Azure richiede Azure Key Vault per controllare e gestire le chiavi e i segreti di crittografia del disco. L'insieme di credenziali delle chiavi e le macchine virtuali devono trovarsi nella stessa area e nella stessa sottoscrizione di Azure.

Per altre informazioni, vedere [creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure con Azure ad (versione precedente)](disk-encryption-key-vault-aad.md).
 
## <a name="next-steps"></a>Passaggi successivi

- [Creazione e configurazione di un insieme di credenziali delle chiavi per crittografia dischi di Azure con Azure AD (versione precedente)](disk-encryption-key-vault-aad.md)
- [Abilitare crittografia dischi di Azure con Azure AD nelle macchine virtuali Linux (versione precedente)](disk-encryption-linux-aad.md)
- [Script dell'interfaccia della riga di comando dei prerequisiti di crittografia dischi di Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Script di PowerShell per prerequisiti di crittografia dischi di Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)