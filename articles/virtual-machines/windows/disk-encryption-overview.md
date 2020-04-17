---
title: Abilitare Crittografia disco di Azure per le macchine virtuali WindowsEnable Azure Disk Encryption for Windows VMs
description: In questo articolo vengono fornite istruzioni sull'abilitazione di Crittografia disco di Microsoft Azure per le macchine virtuali Windows.This article provides instructions on enabling Microsoft Azure Disk Encryption for Windows VMs.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/05/2019
ms.custom: seodec18
ms.openlocfilehash: b2fe7e4fbe4830567533053580880315ceface5d
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458880"
---
# <a name="azure-disk-encryption-for-windows-vms"></a>Azure Disk Encryption for Windows VMs 

Crittografia dischi di Azure consente di proteggere i dati per soddisfare gli obblighi di sicurezza e conformità dell'organizzazione. Usa la funzionalità [Bitlocker](https://en.wikipedia.org/wiki/BitLocker) di Windows per fornire la crittografia del volume per il sistema operativo e i dischi dati delle macchine virtuali (VM) di Azure ed è integrato con [l'insieme](../../key-vault/index.yml) di credenziali delle chiavi di Azure per controllare e gestire le chiavi e i segreti di crittografia del disco. 

Se si usa [il Centro sicurezza](../../security-center/index.yml)di Azure , si viene avvisati se sono disponibili macchine virtuali non crittografate. Gli avvisi vengono visualizzati con un livello di gravità elevato e la raccomandazione di crittografare tali macchine virtuali.

![Avviso crittografia dischi in Centro sicurezza di Azure](../media/disk-encryption/security-center-disk-encryption-fig1.png)

> [!WARNING]
> - Se in precedenza è stato usato Crittografia disco di Azure con Azure AD per crittografare una macchina virtuale, è necessario continuare a usare questa opzione per crittografare la macchina virtuale. Per informazioni dettagliate, vedere Crittografia del disco di [Azure con Azure AD (versione precedente).](disk-encryption-overview-aad.md) 
> - Alcune indicazioni possono comportare un maggior utilizzo delle risorse di calcolo, rete o dati con un conseguente aumento dei costi di licenza o sottoscrizione. Per creare le risorse in Azure nella aree geografiche supportate, è necessario avere una sottoscrizione di Azure attiva e valida.

È possibile apprendere le nozioni di base di Crittografia disco di Azure per Windows in pochi minuti con la guida introduttiva [Creare e crittografare una macchina virtuale Windows con l'interfaccia della](disk-encryption-cli-quickstart.md) riga di comando di Azure o Creare e [crittografare una macchina virtuale Windows con la guida introduttiva](disk-encryption-powershell-quickstart.md)di Azure Powershell .

## <a name="supported-vms-and-operating-systems"></a>Macchine virtuali e sistemi operativi supportati

### <a name="supported-vms"></a>VM supportate

Le macchine virtuali Windows sono disponibili in un [intervallo di dimensioni.](sizes-general.md) Crittografia disco di Azure non è disponibile nelle [macchine virtuali di base serie A](https://azure.microsoft.com/pricing/details/virtual-machines/series/)o in macchine virtuali con meno di 2 GB di memoria.

Crittografia disco di Azure è disponibile anche per le macchine virtuali con archiviazione Premium.Azure Disk Encryption is also available for VMs with premium storage.

Crittografia disco di Azure non è disponibile nelle [macchine virtuali di generazione 2](generation-2.md#generation-1-vs-generation-2-capabilities)e nelle macchine virtuali serie [Lsv2](../lsv2-series.md). Per altre eccezioni, vedere Crittografia disco di [Azure: scenari non supportati.](disk-encryption-windows.md#unsupported-scenarios)

### <a name="supported-operating-systems"></a>Sistemi operativi supportati

- Client Windows: Windows 8 e versioni successive.
- Windows Server: Windows Server 2008 R2 e versioni successive.  
 
> [!NOTE]
> Windows Server 2008 R2 richiede l'installazione di .NET Framework 4.5 per la crittografia; installarlo da Windows Update con l'aggiornamento facoltativo di Microsoft.NET Framework 4.5.2 per sistemi basati su Windows Server 2008 R2 x64 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 Core e Windows Server 2016 Core richiedono l'installazione del componente bdehdcfg nella macchina virtuale per la crittografia.


## <a name="networking-requirements"></a>Requisiti di rete
Per abilitare Crittografia disco di Azure, le macchine virtuali devono soddisfare i requisiti di configurazione dell'endpoint di rete seguenti:To enable Azure Disk Encryption, the VMs must meet the following network endpoint configuration requirements:
  - Per ottenere un token per la connessione all'insieme di credenziali delle chiavi, \[\]la macchina virtuale Windows deve essere in grado di connettersi a un endpoint di Azure Active Directory, login.microsoftonline.com .
  - Per scrivere le chiavi di crittografia nell'insieme di credenziali delle chiavi, la macchina virtuale Windows deve essere in grado di connettersi all'endpoint dell'insieme di credenziali delle chiavi.
  - La macchina virtuale Windows deve essere in grado di connettersi a un endpoint di archiviazione di Azure che ospita il repository delle estensioni di Azure e un account di archiviazione di Azure che ospita i file del disco rigido virtuale.
  -  Se i criteri di sicurezza limitano l'accesso dalle macchine virtuali di Azure a Internet, è possibile risolvere l'URI precedente e configurare una regola specifica per consentire la connettività in uscita agli indirizzi IP. Per altre informazioni, vedere [Azure Key Vault protetto da firewall](../../key-vault/general/access-behind-firewall.md).    


## <a name="group-policy-requirements"></a>Requisiti di Criteri di gruppo

Azure Disk Encryption uses the BitLocker external key protector for Windows VMs. Per le macchine virtuali aggiunte a un dominio, non eseguire il push di criteri di gruppo che applichino protezioni TPM. Per informazioni sui criteri di gruppo per "Consenti BitLocker senza un TPM compatibile", vedere Informazioni di riferimento sui criteri di gruppo di [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

I criteri BitLocker nelle macchine virtuali aggiunte al dominio con criteri di gruppo personalizzati devono includere l'impostazione seguente: Configurare l'archiviazione utente delle informazioni di [ripristino di BitLocker -> Consenti chiave](/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings)di ripristino a 256 bit . Crittografia dischi di Azure avrà esito negativo quando le impostazioni di Criteri di gruppo personalizzate per BitLocker sono incompatibili. Sulle macchine sprovviste delle corrette impostazioni di criteri, applicare i nuovi criteri, forzare l'aggiornamento dei criteri (gpupdate.exe /force) e, dopodiché, potrebbe essere necessario riavviare.

Azure Disk Encryption will fail if domain level group policy blocks the AES-CBC algorithm, which is used by BitLocker.

## <a name="encryption-key-storage-requirements"></a>Requisiti di archiviazione delle chiavi di crittografiaEncryption key storage requirements  

Crittografia disco di Azure richiede un insieme di credenziali delle chiavi di Azure per controllare e gestire le chiavi e i segreti di crittografia del disco. L'insieme di credenziali delle chiavi e le macchine virtuali devono risiedere nella stessa area e sottoscrizione di Azure.Your key vault and VMs must reside in the same Azure region and subscription.

Per informazioni dettagliate, vedere Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di [Azure.](disk-encryption-key-vault.md)

## <a name="terminology"></a>Terminologia
La tabella seguente definisce alcuni dei termini comuni usati nella documentazione relativa alla crittografia del disco di Azure:The following table defines some of the common terms used in Azure disk encryption documentation:

| Terminologia | Definizione |
| --- | --- |
| Insieme di credenziali chiave di Azure | Key Vault è un servizio di crittografia e gestione delle chiavi basato su moduli di sicurezza hardware convalidati dagli standard FIPS (Federal Information Processing Standards). Questi standard consentono di proteggere le chiavi crittografiche e i segreti sensibili. Per altre informazioni, vedere la documentazione dell'insieme di credenziali delle chiavi di Azure e Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di Azure.For more information, see the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) documentation and Creating and [configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md). |
| Interfaccia della riga di comando di Azure | [L'interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli) è ottimizzata per la gestione e l'amministrazione delle risorse di Azure dalla riga di comando.|
| BitLocker |[BitLocker](https://technet.microsoft.com/library/hh831713.aspx) è una tecnologia di crittografia del volume di Windows riconosciuta dal settore che viene utilizzata per abilitare la crittografia del disco nelle macchine virtuali Windows. |
| Chiave di crittografia chiave (KEK) | Chiave asimmetrica (RSA 2048) che è possibile utilizzare per proteggere o racchiudere il segreto. È possibile fornire una chiave protetta tramite modulo di protezione hardware o una chiave protetta tramite software. Per altre informazioni, vedere la documentazione dell'insieme di credenziali delle chiavi di Azure e Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di Azure.For more information, see the [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) documentation and Creating and [configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md). |
| Cmdlet PowerShell | Per altre informazioni, vedere [Cmdlet di Azure PowerShell](/powershell/azure/overview). |


## <a name="next-steps"></a>Passaggi successivi

- [Guida introduttiva - Creare e crittografare una macchina virtuale Windows con l'interfaccia della riga di comando di AzureQuickstart - Create and encrypt a Windows VM with Azure CLI](disk-encryption-cli-quickstart.md)
- [Guida introduttiva - Creare e crittografare una macchina virtuale Windows con Azure PowershellQuickstart - Create and encrypt a Windows VM with Azure Powershell](disk-encryption-powershell-quickstart.md)
- [Scenari di crittografia dischi di Azure per macchine virtuali Windows](disk-encryption-windows.md)
- [Script dell'interfaccia della riga di comando dei prerequisiti di Crittografia disco di AzureAzure Disk Encryption prerequisites](https://github.com/ejarvi/ade-cli-getting-started)
- [Script di PowerShell per i prerequisiti di Crittografia disco di AzureAzure Disk Encryption prerequisites PowerShell script](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts)
- [Creazione e configurazione di un insieme di credenziali delle chiavi per la crittografia dischi di Azure](disk-encryption-key-vault.md)


