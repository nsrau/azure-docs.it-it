---
title: Domande frequenti - Crittografia disco di Azure per macchine virtuali WindowsFAQ - Azure Disk Encryption for Windows VMs
description: Questo articolo fornisce le risposte alle domande frequenti su Crittografia disco di Microsoft Azure per le macchine virtuali IaaS di Windows.This article provides answers to frequently asked questions about Microsoft Azure Disk Encryption for Windows IaaS VMs.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 11/01/2019
ms.custom: seodec18
ms.openlocfilehash: 5cc6a3d8d736209f762959bca6f8ad379c14203f
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582720"
---
# <a name="azure-disk-encryption-for-windows-vms-faq"></a>Azure Disk Encryption for Windows VMs FAQ

Questo articolo fornisce le risposte alle domande frequenti (domande frequenti) su Crittografia disco di Azure per macchine virtuali Windows.This article provides answers to frequently asked questions (FAQ) about Azure Disk Encryption for Windows VMs. Per altre informazioni su questo servizio, vedere [Panoramica](disk-encryption-overview.md)di Crittografia disco di Azure.For more information about this service, see Azure Disk Encryption overview .

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Dove è presente Crittografia dischi di Azure con disponibilità generale (GA)?

Crittografia disco di Azure è in generale la disponibilità in tutte le aree pubbliche di Azure.Azure Disk Encryption is in general availability in all Azure public regions.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Quali esperienze utente sono disponibili con Crittografia dischi di Azure?

La versione GA di Crittografia dischi di Azure supporta i modelli di Azure Resource Manager, Azure PowerShell e l'interfaccia della riga di comando di Azure. La presenza di diverse esperienze utente consente una maggiore flessibilità. Sono disponibili tre diverse opzioni per abilitare la crittografia del disco per le macchine virtuali. Per altre informazioni sull'esperienza utente e istruzioni dettagliate disponibili in Crittografia disco di Azure, vedere Scenari di Crittografia disco di Azure per Windows.For more information on the user experience and step-by-step guidance available in Azure Disk Encryption, see [Azure Disk Encryption scenarios for Windows](disk-encryption-windows.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto costa Crittografia dischi di Azure?

Non sono previsti costi aggiuntivi per la crittografia dei dischi delle macchine virtuali con Crittografia disco di Azure, ma sono previsti addebiti associati all'uso di Archiviazione chiavi di Azure.There's no charge for encrypting VM disks with Azure Disk Encryption, but there are charges associated with the use of Azure Key Vault. Per altre informazioni sui costi associati ad Azure Key Vault, vedere la pagina [Prezzi di Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Come si può iniziare a usare Crittografia dischi di Azure?

Per iniziare, leggere l'articolo [Azure Disk Encryption overview](disk-encryption-overview.md) (Panoramica di Crittografia dischi di Azure).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Quali dimensioni delle macchine virtuali e sistemi operativi supportano Crittografia disco di Azure?

L'articolo Panoramica di [Crittografia dischi](disk-encryption-overview.md) di Azure elenca le dimensioni delle macchine virtuali e i sistemi operativi della macchina virtuale che supportano Crittografia disco di Azure.The Azure Disk Encryption overview article lists the [VM sizes](disk-encryption-overview.md#supported-vms) and VM [operating systems](disk-encryption-overview.md#supported-operating-systems) that support Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>È possibile crittografare sia i volumi di avvio che i volumi di dati con Crittografia dischi di Azure?

È possibile crittografare sia i volumi di avvio che i volumi di dati, ma non è possibile crittografare i dati senza prima crittografare il volume del sistema operativo.

Dopo aver crittografato il volume del sistema operativo, la disabilitazione della crittografia nel volume del sistema operativo non è supportata.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>È possibile crittografare un volume non montato con Crittografia disco di Azure?

No, Crittografia disco di Azure crittografa solo i volumi installati.

## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Come si ruotano i segreti o le chiavi di crittografia?

Per ruotare i segreti, è sufficiente chiamare lo stesso comando utilizzato in origine per abilitare la crittografia del disco, specificando un insieme di credenziali delle chiavi diverso. Per ruotare la chiave di crittografia della chiave, chiamare lo stesso comando utilizzato in origine per abilitare la crittografia del disco, specificando la nuova crittografia della chiave. 

>[!WARNING]
> - Se in precedenza è stato usato Crittografia disco di [Azure con l'app Azure AD](disk-encryption-windows-aad.md) specificando le credenziali di Azure AD per crittografare questa macchina virtuale, è necessario continuare a usare questa opzione per crittografare la macchina virtuale. Non è possibile usare Crittografia disco di Azure in questa macchina virtuale crittografata perché non si tratta di uno scenario supportato, ovvero il passaggio dall'applicazione AAD per questa macchina virtuale crittografata non è ancora supportato.

## <a name="how-do-i-add-or-remove-a-key-encryption-key-if-i-didnt-originally-use-one"></a>Come si aggiunge o si rimuove una chiave di crittografia della chiave se non ne è stata utilizzata originariamente?

Per aggiungere una chiave di crittografia della chiave, chiamare nuovamente il comando enable passando il parametro della chiave di crittografia della chiave. Per rimuovere una chiave di crittografia della chiave, chiamare nuovamente il comando enable senza il parametro key encryption.

## <a name="does-azure-disk-encryption-allow-you-to-bring-your-own-key-byok"></a>Con Crittografia dischi di Azure sono disponibili BYOK (Bring Your Own Key)?

Sì, è possibile fornire le proprie chiavi di crittografia della chiave. Queste chiavi sono protette in Azure Key Vault, l'archivio delle chiavi per Crittografia dischi di Azure. Per altre informazioni sugli scenari di supporto delle chiavi di crittografia, vedere Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di Azure.For more information on the key encryption keys support scenarios, see [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="can-i-use-an-azure-created-key-encryption-key"></a>È possibile usare una chiave di crittografia della chiave creata in Azure?

Sì, è possibile usare Azure Key Vault per generare una chiave di crittografia della chiave per l'uso con Crittografia dischi di Azure. Queste chiavi sono protette in Azure Key Vault, l'archivio delle chiavi per Crittografia dischi di Azure. Per altre informazioni sulla chiave di crittografia della chiave, vedere Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di Azure.For more information on the key encryption key, see [Creating and configuring](disk-encryption-key-vault.md)a key vault for Azure Disk Encryption .

## <a name="can-i-use-an-on-premises-key-management-service-or-hsm-to-safeguard-the-encryption-keys"></a>È possibile usare un servizio di gestione delle chiavi/modulo di protezione hardware locale per proteggere le chiavi di crittografia?

Non è possibile usare il servizio di gestione delle chiavi/modulo di protezione hardware locale per proteggere le chiavi di crittografia con Crittografia dischi di Azure. Per proteggere le chiavi di crittografia, è possibile usare solo il servizio Azure Key Vault. Per altre informazioni sugli scenari di supporto delle chiavi di crittografia, vedere Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di Azure.For more information on the key encryption key support scenarios, see [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption"></a>Quali sono i prerequisiti per la configurazione di Crittografia dischi di Azure?

Per usare Crittografia dischi di Azure, è necessario rispettare alcuni prerequisiti. Vedere l'articolo [Creazione e configurazione di un insieme](disk-encryption-key-vault.md) di credenziali delle chiavi per Crittografia disco di Azure per creare un nuovo insieme di credenziali delle chiavi o configurare un insieme di credenziali delle chiavi esistente per l'accesso alla crittografia del disco per abilitare la crittografia e proteggere segreti e chiavi. Per altre informazioni sugli scenari di supporto delle chiavi di crittografia, vedere Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di Azure.For more information on the key encryption key support scenarios, see [Creating and configuring a key vault for Azure Disk Encryption](disk-encryption-key-vault.md).

## <a name="what-are-the-prerequisites-to-configure-azure-disk-encryption-with-an-azure-ad-app-previous-release"></a>Quali sono i prerequisiti per la configurazione di Crittografia dischi di Azure con un'app Azure AD (versione precedente)?

Per usare Crittografia dischi di Azure, è necessario rispettare alcuni prerequisiti. Vedere [Crittografia del disco](disk-encryption-windows-aad.md) di Azure con contenuto di Azure AD per creare un'applicazione Azure Active Directory, creare un nuovo insieme di credenziali delle chiavi o configurare un insieme di credenziali delle chiavi esistente per l'accesso alla crittografia del disco per abilitare la crittografia e proteggere segreti e chiavi. Per altre informazioni sugli scenari di supporto delle chiavi di crittografia, vedere Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di [Azure con Azure AD.](disk-encryption-key-vault-aad.md)

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>L'esecuzione di Crittografia dischi di Azure con un'app Azure AD (versione precedente) è ancora supportata?
Sì. L'esecuzione di Crittografia dischi con un'app Azure AD è ancora supportata. Tuttavia, quando si esegue la crittografia di nuove macchine virtuali, è consigliabile usare il nuovo metodo anziché un'app Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>È possibile eseguire la migrazione di macchine virtuali crittografate con un'app Azure AD alla crittografia senza tale app?
  Non esiste attualmente un percorso di migrazione diretto per le macchine crittografate con un'app Azure AD alla crittografia senza tale app. Non esiste nemmeno un percorso diretto dalla crittografia senza un'app Azure AD alla crittografia con tale app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Quale versione di Azure PowerShell è supportata da Crittografia dischi di Azure?

Usare la versione più recente di Azure PowerShell SDK per configurare Crittografia dischi di Azure. Scaricare la versione più recente di [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Crittografia dischi di Azure *non* è supportato da Azure SDK versione 1.1.0.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Che cos'è il disco "volume BEK" o "/mnt/azure_bek_disk"?

Il "volume Bek" è un volume di dati locale che archivia in modo sicuro le chiavi di crittografia per le macchine virtuali di Azure crittografate.

> [!NOTE]
> Non eliminare né modificare il contenuto del disco. Non smontare il disco, perché la presenza delle chiavi di crittografia è necessaria per qualsiasi operazione di crittografia sulla VM IaaS.

## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Qual è il metodo di crittografia usato da Crittografia dischi di Azure?

Crittografia disco di Azure seleziona il metodo di crittografia in BitLocker in base alla versione di Windows come segue:

| Versioni di Windows                 | Versione | Metodo di crittografia        |
|----------------------------------|--------|--------------------------|
| Windows Server 2012, Windows 10 o versione successiva  | >1511 USD |XTS-AES 256 bit           |
| Windows Server 2012, Windows 8, 8.1, 10 | < 1511 |AES 256 bit              |
| Windows Server 2008R2            |        |AES 256 bit con diffusore |

\*AES 256 bit con Diffuser non è supportato in Windows 2012 e versioni successive.

Per determinare la versione del sistema operativo Windows, eseguire lo strumento 'winver' nella macchina virtuale.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Se si utilizzano EncryptFormatAll e vengono specificati tutti i tipi di volume, verranno cancellati i dati sulle unità dati già crittografati?
No, i dati non verranno cancellati da unità dati che sono già crittografate usando Crittografia dischi di Azure. Analogamente al modo in cui EncryptFormatAll non crittografa nuovamente l'unità del sistema operativo, non riapplica la crittografia all'unità dati già crittografata. 

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>È possibile eseguire il backup e il ripristino di una macchina virtuale crittografata? 

Backup di Azure offre un meccanismo per eseguire il backup e il ripristino delle macchine virtuali crittografate all'interno della stessa sottoscrizione e area.  Per istruzioni, vedere [Eseguire il backup e il ripristino di macchine virtuali crittografate con Backup di Azure.](../../backup/backup-azure-vms-encryption.md)  Il ripristino di una macchina virtuale crittografata in un'area diversa non è attualmente supportato.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Quali risorse sono disponibili per porre domande o inviare commenti?

È possibile porre domande o inviare commenti e suggerimenti sul [forum di Crittografia dischi di Azure disponibile](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Passaggi successivi
In questo documento sono state fornite le risposte alle domande più frequenti relative a Crittografia dischi di Azure. Per altre informazioni sul servizio, vedere gli articoli seguenti:

- [Panoramica di Crittografia dischi di AzureAzure Disk Encryption Overview](disk-encryption-overview.md)
- [Applicare la crittografia del disco nel Centro sicurezza Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Crittografia dei dati inattivi in Azure](../../security/fundamentals/encryption-atrest.md)
