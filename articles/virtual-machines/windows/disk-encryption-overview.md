---
title: Abilitare crittografia dischi di Azure per macchine virtuali Windows
description: Questo articolo fornisce istruzioni sull'abilitazione della crittografia del disco Microsoft Azure per le macchine virtuali Windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: 5648dc1a915f2d38dc7811e6fe23ec76d0aa6f23
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: it-IT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82857273"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Crittografia dischi di Azure per macchine virtuali Windows 

Crittografia dischi di Azure consente di proteggere e salvaguardare i dati per soddisfare gli obblighi di sicurezza e conformità dell'organizzazione. Usa la funzionalità [BitLocker](https://en.wikipedia.org/wiki/BitLocker) di Windows per fornire la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali (VM) di Azure ed è integrato con [Azure Key Vault](../../key-vault/index.yml) per facilitare il controllo e la gestione delle chiavi e dei segreti di crittografia del disco. 

Se si usa [Centro sicurezza di Azure](../../security-center/index.yml), si viene avvisati se sono presenti macchine virtuali non crittografate. Gli avvisi vengono visualizzati con un livello di gravità elevato e la raccomandazione di crittografare tali macchine virtuali.

![Avviso crittografia dischi in Centro sicurezza di Azure](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se in precedenza è stata usata la funzionalità Crittografia dischi di Azure con Azure AD per crittografare una macchina virtuale, sarà necessario continuare a usare questa opzione per crittografarla. Per altre informazioni, vedere [Crittografia dischi di Azure con Azure AD (versione precedente)](disk-encryption-overview-aad.md). 
> - Alcune indicazioni possono comportare un maggior utilizzo delle risorse di calcolo, rete o dati con un conseguente aumento dei costi di licenza o sottoscrizione. Per creare le risorse in Azure nella aree geografiche supportate, è necessario avere una sottoscrizione di Azure attiva e valida.

È possibile apprendere le nozioni di base di crittografia dischi di Azure per Windows in pochi minuti con la Guida [introduttiva creare e crittografare una macchina virtuale Windows con l'interfaccia](disk-encryption-cli-quickstart.md) della riga di comando di Azure o [creare e crittografare una VM Windows con Azure PowerShell](disk-encryption-powershell-quickstart.md).

## <a name="supported-vms-and-operating-systems"></a>Macchine virtuali e sistemi operativi supportati

### <a name="supported-vms"></a>VM supportate

Le macchine virtuali Windows sono disponibili in un [intervallo di dimensioni](sizes-general.md). Crittografia dischi di Azure non è disponibile nelle [VM Basic, serie A](https://azure.microsoft.com/pricing/details/virtual-machines/series/)o nelle macchine virtuali con meno di 2 GB di memoria.

Crittografia dischi di Azure è disponibile anche per le macchine virtuali con Archiviazione Premium.

Crittografia dischi di Azure non è disponibile nelle macchine virtuali di [generazione 2](generation-2.md#generation-1-vs-generation-2-capabilities) e [VM della serie Lsv2](../lsv2-series.md). Per altre eccezioni, vedere [Crittografia dischi di Azure: Scenari non supportati](disk-encryption-windows.md#unsupported-scenarios).

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

- Client Windows: Windows 8 e versioni successive.
- Windows Server: Windows Server 2008 R2 e versioni successive.  
 
> [!NOTE]
> Windows Server 2008 R2 richiede l'installazione di .NET Framework 4,5 per la crittografia. installarlo da Windows Update con l'aggiornamento facoltativo Microsoft .NET Framework 4.5.2 per sistemi basati su Windows Server 2008 R2 x64 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 Core e Windows Server 2016 Core richiedono l'installazione del componente BdeHdCfg nella macchina virtuale per la crittografia.


## <a name="networking-requirements"></a>Requisiti di rete
Per abilitare crittografia dischi di Azure, le macchine virtuali devono soddisfare i requisiti di configurazione degli endpoint di rete seguenti:
  - Per ottenere un token per la connessione all'insieme di credenziali delle chiavi, la VM Windows deve essere in grado di connettersi a un endpoint Azure Active Directory, \[ login.microsoftonline.com \] .
  - Per scrivere le chiavi di crittografia nell'insieme di credenziali delle chiavi, la macchina virtuale Windows deve essere in grado di connettersi all'endpoint dell'insieme di credenziali delle chiavi.
  - La VM Windows deve essere in grado di connettersi a un endpoint di archiviazione di Azure che ospita il repository di estensioni di Azure e un account di archiviazione di Azure che ospita i file VHD.
  -  Se i criteri di sicurezza limitano l'accesso dalle macchine virtuali di Azure a Internet, è possibile risolvere l'URI precedente e configurare una regola specifica per consentire la connettività in uscita agli indirizzi IP. Per altre informazioni, vedere [Azure Key Vault protetto da firewall](../../key-vault/general/access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Requisiti di Criteri di gruppo

Crittografia dischi di Azure usa la protezione con chiave esterna BitLocker per le macchine virtuali Windows. Per le macchine virtuali aggiunte a un dominio, non eseguire il push di criteri di gruppo che applichino protezioni TPM. Per informazioni sui criteri di gruppo per "Consenti BitLocker senza un TPM compatibile", vedere informazioni di [riferimento su bitlocker criteri di gruppo](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

I criteri di BitLocker nelle macchine virtuali appartenenti a un dominio con criteri di gruppo personalizzati devono includere l'impostazione seguente: [Configura archiviazione utente delle informazioni di ripristino di BitLocker-> Consenti chiave di ripristino a 256 bit](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Crittografia dischi di Azure avrà esito negativo quando le impostazioni di Criteri di gruppo personalizzate per BitLocker sono incompatibili. Sulle macchine sprovviste delle corrette impostazioni di criteri, applicare i nuovi criteri, forzare l'aggiornamento dei criteri (gpupdate.exe /force) e, dopodiché, potrebbe essere necessario riavviare.

Crittografia dischi di Azure non riuscirà se i criteri di gruppo a livello di dominio bloccano l'algoritmo AES-CBC, usato da BitLocker.

## <a name="encryption-key-storage-requirements"></a>Requisiti di archiviazione delle chiavi di crittografia  

Crittografia dischi di Azure richiede Azure Key Vault per controllare e gestire segreti e chiavi di crittografia dei dischi. L'insieme di credenziali delle chiavi e le macchine virtuali devono risiedere nella stessa area e sottoscrizione di Azure.

Per altre informazioni, vedere [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md).

## <a name="terminology"></a>Terminologia
La tabella seguente definisce alcuni dei termini comuni usati nella documentazione di Crittografia dischi di Azure:

| Terminologia | Definizione |
| --- | --- |
| Insieme di credenziali chiave di Azure | Key Vault è un servizio di crittografia e gestione delle chiavi basato su moduli di sicurezza hardware convalidati dagli standard FIPS (Federal Information Processing Standards). Questi standard consentono di proteggere le chiavi crittografiche e i segreti sensibili. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md). |
| Interfaccia della riga di comando di Azure | [L'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) è ottimizzata per la gestione e l'amministrazione delle risorse di Azure dalla riga di comando.|
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) è una tecnologia di crittografia del volume di Windows riconosciuta nel settore, usata per abilitare la crittografia dei dischi nelle macchine virtuali Windows. |
| Chiave di crittografia della chiave (KEK) | La chiave asimmetrica (RSA 2048) che è possibile usare per proteggere il segreto o eseguirne il wrapping. È possibile fornire una chiave protetta tramite modulo di protezione hardware o una chiave protetta tramite software. Per altre informazioni, vedere la documentazione di [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) e [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md). |
| Cmdlet PowerShell | Per altre informazioni, vedere [Cmdlet di Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva: creare e crittografare una VM Windows con l'interfaccia della riga di comando](disk-encryption-cli-quickstart.md)
- [Guida introduttiva: creare e crittografare una VM Windows con Azure PowerShell](disk-encryption-powershell-quickstart.md)
- [Scenari di crittografia dischi di Azure per macchine virtuali Windows](disk-encryption-windows.md)
- [Script dell'interfaccia della riga di comando dei prerequisiti di Crittografia dischi di Azure](https://github.com/ejarvi/ade-cli-getting-started)
- [Script di PowerShell dei prerequisiti di Crittografia dischi di Azure](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per la crittografia dischi di Azure](disk-encryption-key-vault.md)


