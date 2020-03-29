---
title: Crittografia del disco di Azure con prerequisiti dell'app Azure AD (versione precedente)Azure Disk Encryption with Azure AD app prerequisites (previous release)
description: Questo articolo illustra i prerequisiti per l'uso di Crittografia dischi di Azure per le macchine virtuali IaaS.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: f38fd7c8e14f58052912f68a277f194fd3866f2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970570"
---
# <a name="azure-disk-encryption-with-azure-ad-previous-release"></a>Crittografia del disco di Azure con Azure AD (versione precedente)Azure Disk Encryption with Azure AD (previous release)

La nuova versione di Crittografia disco di Azure elimina il requisito per fornire un parametro dell'applicazione Azure Active Directory (Azure AD) per abilitare la crittografia del disco della macchina virtuale. Con la nuova versione, non è più necessario specificare le credenziali di Azure AD durante il passaggio di abilitazione della crittografia. Tutte le nuove macchine virtuali devono essere crittografate senza i parametri dell'applicazione Azure AD usando la nuova versione. Per istruzioni su come abilitare la crittografia del disco della macchina virtuale usando la nuova versione, vedere Crittografia disco di Azure per macchine virtuali Linux.For instructions on how to enable VM disk encryption by using the new release, see [Azure Disk Encryption for Linux VMs](disk-encryption-overview.md). Le macchine virtuali che sono già state crittografate con i parametri dell'applicazione Azure AD sono ancora supportate e dovrebbero continuare a essere gestite con la sintassi di AAD.

Questo articolo fornisce supplementi a [Crittografia dischi](disk-encryption-overview.md) di Azure per macchine virtuali Linux con requisiti e prerequisiti aggiuntivi per Crittografia disco di Azure con Azure AD (versione precedente).

Le informazioni contenute in queste sezioni rimangono le stesse:

- [Macchine virtuali e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Requisiti aggiuntivi per le macchine virtuali](disk-encryption-overview.md#additional-vm-requirements)


 

## <a name="networking-and-group-policy"></a> Rete e Criteri di gruppo

Per abilitare la funzionalità di crittografia del disco di Azure usando la sintassi dei parametri AAD meno recenti, le macchine virtuali dell'infrastruttura come servizio (IaaS) devono soddisfare i requisiti di configurazione dell'endpoint di rete seguenti:To enable the Azure Disk Encryption feature by using the older AAD parameter syntax, the infrastructure as a service (IaaS) VMs must meet the following network endpoint configuration requirements: 
  - Per ottenere un token per la connessione all'insieme di credenziali delle chiavi, la \[\]macchina virtuale IaaS deve essere in grado di connettersi a un endpoint di Azure AD login.microsoftonline.com .
  - Per scrivere le chiavi di crittografia nell'insieme di credenziali delle chiavi, è necessario che la macchina virtuale IaaS possa connettersi all'endpoint dell'insieme di credenziali delle chiavi.
  - La VM IaaS deve potersi connettere a un endpoint di archiviazione di Azure che ospita il repository delle estensioni di Azure e a un account di archiviazione di Azure che ospita i file del disco rigido virtuale.
  -  Se i criteri di sicurezza limitano l'accesso dalle macchine virtuali di Azure a Internet, è possibile risolvere l'URI precedente e configurare una regola specifica per consentire la connettività in uscita agli indirizzi IP. Per altre informazioni, vedere [Azure Key Vault protetto da firewall](../../key-vault/key-vault-access-behind-firewall.md).
  - In Windows, se TLS 1.0 è disabilitato in modo esplicito e la versione .NET non viene aggiornata a 4.6 o versione successiva, la seguente modifica del Registro di sistema consente a Crittografia disco di Azure di selezionare la versione TLS più recente:On Windows, if TLS 1.0 is explicitly disabled and the .NET version isn't updated to 4.6 or higher, the following registry change enables Azure Disk Encryption to select the more recent TLS version:
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001
    
            [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
            "SystemDefaultTlsVersions"=dword:00000001
            "SchUseStrongCrypto"=dword:00000001` 
         
    
### <a name="group-policy"></a>Criteri di gruppo
 - La soluzione Crittografia dischi di Azure usa la protezione con chiave esterna BitLocker per macchine virtuali IaaS Windows. Per le macchine virtuali aggiunte al dominio, non eseguire il push dei criteri di gruppo che applicano le protezioni TPM. Per informazioni sui Criteri di gruppo per l'opzione **Consenti BitLocker senza un TPM compatibile,** vedere Informazioni di riferimento su [Criteri di gruppo di BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

- I criteri BitLocker nelle macchine virtuali aggiunte al dominio con criteri di gruppo personalizzati devono includere l'impostazione seguente: [Configurare l'archiviazione utente delle informazioni di ripristino di BitLocker, > Consenti chiave](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)di ripristino a 256 bit . Crittografia disco di Azure non riesce quando le impostazioni personalizzate di Criteri di gruppo per BitLocker sono incompatibili. Nei computer che non dispongono dell'impostazione dei criteri corretta, applicare il nuovo criterio, forzare l'aggiornamento del nuovo criterio (gpupdate.exe /force), quindi riavviare se necessario. 

## <a name="encryption-key-storage-requirements"></a>Requisiti di archiviazione delle chiavi di crittografiaEncryption key storage requirements 

Crittografia disco di Azure richiede Azure Key Vault per controllare e gestire le chiavi e i segreti di crittografia del disco. L'insieme di credenziali delle chiavi e le macchine virtuali devono risiedere nella stessa area e sottoscrizione di Azure.Your key vault and VMs must reside in the same Azure region and subscription.

Per altre informazioni, vedere Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di [Azure con Azure AD (versione precedente).](disk-encryption-key-vault-aad.md)
 
## <a name="next-steps"></a>Passaggi successivi

- [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di Azure con Azure AD (versione precedente)Creating and configuring a key vault for Azure Disk Encryption with Azure AD (previous release)](disk-encryption-key-vault-aad.md)
- [Abilitare Crittografia disco di Azure con Azure AD in macchine virtuali Linux (versione precedente)Enable Azure Disk Encryption with Azure AD on Linux VMs (previous release)](disk-encryption-linux-aad.md)
- [Script dell'interfaccia della riga di comando dei prerequisiti di Crittografia disco di AzureAzure Disk Encryption prerequisites](https://github.com/ejarvi/ade-cli-getting-started)
- [Script di PowerShell per i prerequisiti di Crittografia disco di AzureAzure Disk Encryption prerequisites PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)