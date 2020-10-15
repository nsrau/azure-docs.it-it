---
title: Domande frequenti - Crittografia dischi di Azure per macchine virtuali Windows
description: Questo articolo offre risposte alle domande frequenti su Crittografia dischi di Microsoft Azure per macchine virtuali IaaS Windows.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: 6fedefccf57364e690415542114030ba7ce12b49
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974092"
---
# <a name="azure-disk-encryption-for-windows-virtual-machines-faq"></a>Domande frequenti su Crittografia dischi di Azure per macchine virtuali Windows

Questo articolo offre risposte alle domande frequenti su Crittografia dischi di Azure per macchine virtuali Windows. Per altre informazioni su questo servizio, vedere la [panoramica di Crittografia dischi di Azure](disk-encryption-overview.md).

## <a name="what-is-azure-disk-encryption-for-windows-vms"></a>Che cos'è Crittografia dischi di Azure per macchine virtuali Windows?

Crittografia dischi di Azure per macchine virtuali Windows usa la funzionalità BitLocker di Windows per offrire la crittografia completa del disco del sistema operativo e dei dischi dati. Offre inoltre la crittografia del disco temporaneo quando il [parametro VolumeType è All](disk-encryption-windows.md#enable-encryption-on-a-newly-added-data-disk).  Il contenuto passa crittografato dalla macchina virtuale al back-end di archiviazione, offrendo quindi la crittografia end-to-end con una chiave gestita dal cliente.
 
Vedere [Macchine virtuali e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems).
 
## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Dove è presente Crittografia dischi di Azure con disponibilità generale (GA)?

Crittografia dischi di Azure è in disponibilità generale in tutte le aree pubbliche di Azure.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Quali esperienze utente sono disponibili con Crittografia dischi di Azure?

La versione GA di Crittografia dischi di Azure supporta i modelli di Azure Resource Manager, Azure PowerShell e l'interfaccia della riga di comando di Azure. La presenza di diverse esperienze utente consente una maggiore flessibilità. Per abilitare la crittografia dei dischi delle macchine virtuali sono disponibili tre diverse opzioni. Per altre informazioni sull'esperienza utente e sulle indicazioni dettagliate disponibili in Crittografia dischi di Azure, vedere [Scenari di Crittografia dischi di Azure per Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto costa Crittografia dischi di Azure?

Non è previsto alcun addebito per la crittografia dei dischi delle macchine virtuali con Crittografia dischi di Azure, ma sono previsti addebiti associati all'uso di Azure Key Vault. Per altre informazioni sui costi associati ad Azure Key Vault, vedere la pagina [Prezzi di Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Come si può iniziare a usare Crittografia dischi di Azure?

Per iniziare, leggere l'articolo [Azure Disk Encryption overview](disk-encryption-overview.md) (Panoramica di Crittografia dischi di Azure).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Quali sono le dimensioni e i sistemi operativi delle macchine virtuali che supportano Crittografia dischi di Azure?

L'articolo di [panoramica di Crittografia dischi di Azure](disk-encryption-overview.md) elenca le [dimensioni](disk-encryption-overview.md#supported-vms) e i [sistemi operativi delle macchine virtuali](disk-encryption-overview.md#supported-operating-systems) che supportano Crittografia dischi di Azure.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>È possibile crittografare sia i volumi di avvio che i volumi di dati con Crittografia dischi di Azure?

È possibile crittografare i volumi di avvio e di dati, ma non è possibile crittografare i dati senza aver prima crittografato il volume del sistema operativo.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>È possibile crittografare un volume non montato con Crittografia dischi di Azure?

No, Crittografia dischi di Azure crittografa solo i volumi montati.

## <a name="what-is-storage-server-side-encryption"></a>Che cos'è la crittografia di archiviazione lato server?

La crittografia di archiviazione lato server crittografa i dischi gestiti di Azure in Archiviazione di Azure. I dischi gestiti vengono crittografati per impostazione predefinita con la crittografia lato server con una chiave gestita dalla piattaforma (dal 10 giugno 2017). È possibile gestire la crittografia dei dischi gestiti con le proprie chiavi specificando una chiave gestita dal cliente. Per altre informazioni, vedere [Crittografia lato server dei dischi gestiti di Azure](disk-encryption.md).
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>In che modo Crittografia dischi di Azure si differenzia dalla crittografia di archiviazione lato server con chiave gestita dal cliente e quando è consigliabile usare una soluzione o l'altra?

Crittografia dischi di Azure offre crittografia end-to-end per il disco del sistema operativo, i dischi dati e il disco temporaneo con una chiave gestita dal cliente.

- Se i requisiti includono la crittografia di tutti gli elementi sopra citati e la crittografia end-to-end, usare Crittografia dischi di Azure. 
- Se i requisiti includono la crittografia dei soli dati inattivi con chiave gestita dal cliente, usare la [crittografia lato server con chiavi gestite dal cliente](disk-encryption.md). Non è possibile crittografare un disco con Crittografia dischi di Azure e la crittografia di archiviazione lato server con chiavi gestite dal cliente.
- Se si usa uno degli scenari indicati in [Scenari non supportati per Windows](disk-encryption-windows.md#unsupported-scenarios), considerare la possibilità di usare la [crittografia lato server con chiavi gestite dal cliente](disk-encryption.md). 
- Se i criteri dell'organizzazione consentono di crittografare il contenuto inattivo con una chiave gestita da Azure, non è necessaria alcuna azione: il contenuto viene crittografato per impostazione predefinita. Per i dischi gestiti, il contenuto all'interno dell'archiviazione viene crittografato per impostazione predefinita con la crittografia lato server con chiave gestita dalla piattaforma. La chiave è gestita dal servizio Archiviazione di Azure. 

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Come ruotare segreti o chiavi di crittografia?

Per ruotare i segreti, chiamare semplicemente lo stesso comando usato in origine per abilitare la crittografia del disco, specificando un insieme di credenziali delle chiavi diverso. Per ruotare la chiave di crittografia della chiave, chiamare lo stesso comando usato in origine per abilitare la crittografia del disco, specificando la crittografia della nuova chiave. 

>[!WARNING]
> - Se in precedenza è stato usato [Crittografia dischi di Azure con l'app Azure AD](disk-encryption-windows-aad.md) specificando le credenziali di Azure AD per crittografare la macchina virtuale, sarà necessario continuare a usare questa opzione per crittografarla. Non è possibile usare Crittografia dischi di Azure in questa macchina virtuale crittografata poiché non è uno scenario supportato. Il trasferimento dall'applicazione AAD per questa macchina virtuale crittografata non è infatti ancora supportato.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Come si può aggiungere o rimuovere una chiave di crittografia della chiave se in origine non ne è stata usata una?

Per aggiungere una chiave di crittografia della chiave, chiamare di nuovo il comando di abilitazione passando il parametro della chiave di crittografia della chiave. Per rimuovere una chiave di crittografia della chiave, chiamare di nuovo il comando di abilitazione senza il parametro della chiave di crittografia della chiave.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Con Crittografia dischi di Azure sono disponibili BYOK (Bring Your Own Key)?

Sì, è possibile fornire le proprie chiavi di crittografia della chiave. Queste chiavi sono protette in Azure Key Vault, l'archivio delle chiavi per Crittografia dischi di Azure. Per altre informazioni sugli scenari di supporto delle chiavi di crittografia della chiave, vedere [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>È possibile usare una chiave di crittografia della chiave creata in Azure?

Sì, è possibile usare Azure Key Vault per generare una chiave di crittografia della chiave per l'uso con Crittografia dischi di Azure. Queste chiavi sono protette in Azure Key Vault, l'archivio delle chiavi per Crittografia dischi di Azure. Per altre informazioni sulla chiave di crittografia della chiave, vedere [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md).

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>È possibile usare un servizio di gestione delle chiavi/modulo di protezione hardware locale per proteggere le chiavi di crittografia?

Non è possibile usare il servizio di gestione delle chiavi/modulo di protezione hardware locale per proteggere le chiavi di crittografia con Crittografia dischi di Azure. Per proteggere le chiavi di crittografia, è possibile usare solo il servizio Azure Key Vault. Per altre informazioni sugli scenari di supporto delle chiavi di crittografia della chiave, vedere [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quali sono i prerequisiti per la configurazione di Crittografia dischi di Azure?

Per usare Crittografia dischi di Azure, è necessario rispettare alcuni prerequisiti. Per creare un nuovo insieme di credenziali delle chiavi o configurarne uno esistente per l'accesso alla crittografia dei dischi al fine di abilitare la crittografia e per proteggere segreti e chiavi, vedere l'articolo [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md). Per altre informazioni sugli scenari di supporto delle chiavi di crittografia della chiave, vedere [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quali sono i prerequisiti per la configurazione di Crittografia dischi di Azure con un'app Azure AD (versione precedente)?

Per usare Crittografia dischi di Azure, è necessario rispettare alcuni prerequisiti. Per creare un'applicazione Azure Active Directory o un nuovo insieme di credenziali delle chiavi oppure configurarne uno esistente per l'accesso alla crittografia dischi al fine di abilitare la crittografia e per proteggere segreti e chiavi, vedere il contenuto relativo a [Crittografia dischi di Azure con Azure AD](disk-encryption-windows-aad.md). Per altre informazioni sugli scenari di supporto delle chiavi di crittografia della chiave, vedere [Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia dischi di Azure con Azure AD](disk-encryption-key-vault-aad.md).

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>L'esecuzione di Crittografia dischi di Azure con un'app Azure AD (versione precedente) è ancora supportata?
Sì. L'esecuzione di Crittografia dischi con un'app Azure AD è ancora supportata. Tuttavia, quando si esegue la crittografia di nuove macchine virtuali, è consigliabile usare il nuovo metodo anziché un'app Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>È possibile eseguire la migrazione di macchine virtuali crittografate con un'app Azure AD alla crittografia senza tale app?
  Non esiste attualmente un percorso di migrazione diretto per le macchine crittografate con un'app Azure AD alla crittografia senza tale app. Non esiste nemmeno un percorso diretto dalla crittografia senza un'app Azure AD alla crittografia con tale app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Quale versione di Azure PowerShell è supportata da Crittografia dischi di Azure?

Usare la versione più recente di Azure PowerShell SDK per configurare Crittografia dischi di Azure. Scaricare la versione più recente di [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Crittografia dischi di Azure *non* è supportato da Azure SDK versione 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Che cos'è il disco "volume BEK" o "/mnt/azure_bek_disk"?

Il "volume BEK" è un volume di dati locali in cui vengono archiviate in modo sicuro le chiavi di crittografia per le macchine virtuali di Azure crittografate.

> [!NOTE]
> Non eliminare né modificare il contenuto del disco. Non smontare il disco, perché la presenza delle chiavi di crittografia è necessaria per qualsiasi operazione di crittografia sulla VM IaaS.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Qual è il metodo di crittografia usato da Crittografia dischi di Azure?

Crittografia dischi di Azure seleziona il metodo di crittografia in BitLocker in base alla versione di Windows, come indicato di seguito:

| Versioni di Windows                 | Versione | Metodo di crittografia        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 o versione successiva  | >=1511 |XTS-AES a 256 bit           |
| Windows Server 2012, Windows 8, 8.1, 10 | < 1511 |AES a 256 bit *              |
| Windows Server 2008R2            |        |AES a 256 bit con diffusione |

\* AES a 256 bit con diffusione non è supportato in Windows 2012 e versioni successive.

Per determinare la versione del sistema operativo Windows, eseguire lo strumento "winver" nella macchina virtuale.

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>È possibile eseguire un backup e ripristino di una macchina virtuale crittografata? 

Backup di Azure offre un meccanismo di backup e ripristino di macchine virtuali crittografate all'interno della stessa sottoscrizione e area.  Per istruzioni, vedere [Eseguire il backup e ripristino di macchine virtuali crittografate con Backup di Azure](../../backup/backup-azure-vms-encryption.md).  Il ripristino di una macchina virtuale crittografata in un'area diversa non è attualmente supportato.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Quali risorse sono disponibili per porre domande o inviare commenti?

È possibile porre domande o inviare feedback usando la [pagina Domande e risposte Microsoft relativa a Crittografia dischi di Azure](/answers/topics/azure-disk-encryption.html).

## <a name="next-steps"></a>Passaggi successivi
In questo documento sono state fornite le risposte alle domande più frequenti relative a Crittografia dischi di Azure. Per altre informazioni sul servizio, vedere gli articoli seguenti:

- [Informazioni su Crittografia dischi di Azure](disk-encryption-overview.md)
- [Applicare la crittografia dei dischi nel Centro sicurezza di Azure](../../security-center/asset-inventory.md)
- [Crittografia dei dati inattivi in Azure](../../security/fundamentals/encryption-atrest.md)