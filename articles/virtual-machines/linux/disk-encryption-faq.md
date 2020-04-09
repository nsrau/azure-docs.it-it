---
title: Domande frequenti - Crittografia disco di Azure per macchine virtuali LinuxFAQ - Azure Disk Encryption for Linux VMs
description: Questo articolo fornisce le risposte alle domande frequenti su Crittografia disco di Microsoft Azure per macchine virtuali IaaS Linux.This article provides answers to frequently asked questions about Microsoft Azure Disk Encryption for Linux IaaS VMs.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 06/05/2019
ms.custom: seodec18
ms.openlocfilehash: ae3743530440c9df9094a0b9784922d2d6a3dfdf
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/09/2020
ms.locfileid: "80985406"
---
# <a name="azure-disk-encryption-for-linux-virtual-machines-faq"></a>Azure Disk Encryption for Linux virtual machines FAQ

Questo articolo fornisce le risposte alle domande frequenti (domande frequenti) su Crittografia disco di Azure per macchine virtuali (VM) di Azure. Per altre informazioni su questo servizio, vedere [Panoramica](disk-encryption-overview.md)di Crittografia disco di Azure.For more information about this service, see Azure Disk Encryption overview .

## <a name="what-is-azure-disk-encryption-for-linux-vms"></a>Che cos'è Crittografia disco di Azure per macchine virtuali Linux?

Crittografia disco di Azure per macchine virtuali Linux usa la funzionalità dm-crypt di Linux per fornire la crittografia completa del disco del sistema operativo e dei dischi dati. Inoltre, fornisce la crittografia del disco di risorse effimere quando si utilizza la [funzionalità EncryptFormatAll](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms). Il contenuto scorre crittografato dalla macchina virtuale al back-end di archiviazione. In questo modo, fornendo la crittografia end-to-end con una chiave gestita dal cliente.
 
Vedere [Macchine virtuali e sistemi operativi supportati](disk-encryption-overview.md#supported-vms-and-operating-systems).

## <a name="where-is-azure-disk-encryption-in-general-availability-ga"></a>Dove è presente Crittografia dischi di Azure con disponibilità generale (GA)?

Crittografia disco di Azure per macchine virtuali Linux è in disponibilità generale in tutte le aree pubbliche di Azure.Azure Disk Encryption for Linux VMs is in general availability in all Azure public regions.

## <a name="what-user-experiences-are-available-with-azure-disk-encryption"></a>Quali esperienze utente sono disponibili con Crittografia dischi di Azure?

La versione GA di Crittografia dischi di Azure supporta i modelli di Azure Resource Manager, Azure PowerShell e l'interfaccia della riga di comando di Azure. La presenza di diverse esperienze utente consente una maggiore flessibilità. Sono disponibili tre diverse opzioni per abilitare la crittografia del disco per le macchine virtuali. Per altre informazioni sull'esperienza utente e istruzioni dettagliate disponibili in Crittografia disco di Azure, vedere Scenari di Crittografia disco di Azure per Linux.For more information on the user experience and step-by-step guidance available in Azure Disk Encryption, see [Azure Disk Encryption scenarios for Linux](disk-encryption-linux.md).

## <a name="how-much-does-azure-disk-encryption-cost"></a>Quanto costa Crittografia dischi di Azure?

Non sono previsti costi aggiuntivi per la crittografia dei dischi delle macchine virtuali con Crittografia disco di Azure, ma sono previsti addebiti associati all'uso di Archiviazione chiavi di Azure.There's no charge for encrypting VM disks with Azure Disk Encryption, but there are charges associated with the use of Azure Key Vault. Per altre informazioni sui costi associati ad Azure Key Vault, vedere la pagina [Prezzi di Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="how-can-i-start-using-azure-disk-encryption"></a>Come si può iniziare a usare Crittografia dischi di Azure?

Per iniziare, leggere l'articolo [Azure Disk Encryption overview](disk-encryption-overview.md) (Panoramica di Crittografia dischi di Azure).

## <a name="what-vm-sizes-and-operating-systems-support-azure-disk-encryption"></a>Quali dimensioni delle macchine virtuali e sistemi operativi supportano Crittografia disco di Azure?

L'articolo Panoramica di [Crittografia dischi](disk-encryption-overview.md) di Azure elenca le dimensioni delle macchine virtuali e i sistemi operativi della macchina virtuale che supportano Crittografia disco di Azure.The Azure Disk Encryption overview article lists the [VM sizes](disk-encryption-overview.md#supported-vms) and VM [operating systems](disk-encryption-overview.md#supported-operating-systems) that support Azure Disk Encryption.

## <a name="can-i-encrypt-both-boot-and-data-volumes-with-azure-disk-encryption"></a>È possibile crittografare sia i volumi di avvio che i volumi di dati con Crittografia dischi di Azure?

Sì, è possibile crittografare sia i volumi di avvio che quelli di dati oppure è possibile crittografare il volume dei dati senza dover prima crittografare il volume del sistema operativo. 

Dopo aver crittografato il volume del sistema operativo, la disabilitazione della crittografia nel volume del sistema operativo non è supportata. Per le macchine virtuali Linux in un set di scalabilità, è possibile crittografare solo il volume di dati.

## <a name="can-i-encrypt-an-unmounted-volume-with-azure-disk-encryption"></a>È possibile crittografare un volume non montato con Crittografia disco di Azure?

No, Crittografia disco di Azure crittografa solo i volumi installati.

## <a name="what-is-storage-server-side-encryption"></a>Che cos'è la crittografia lato server di archiviazione?

La crittografia lato server di archiviazione crittografa i dischi gestiti di Azure in Archiviazione di Azure.Storage server-side encryption encrypts Azure managed disks in Azure Storage. I dischi gestiti vengono crittografati per impostazione predefinita con la crittografia lato server con una chiave gestita dalla piattaforma (al 10 giugno 2017). È possibile gestire la crittografia dei dischi gestiti con chiavi personalizzate specificando una chiave gestita dal cliente. Per altre informazioni, vedere: [Crittografia lato server dei dischi gestiti di Azure](disk-encryption.md).For more information see: Server-side encryption of Azure managed disks .
 
## <a name="how-is-azure-disk-encryption-different-from-storage-server-side-encryption-with-customer-managed-key-and-when-should-i-use-each-solution"></a>In che modo Crittografia disco di Azure è diversa dalla crittografia lato server di archiviazione con la chiave gestita dal cliente e quando usare ogni soluzione?

Crittografia disco di Azure offre la crittografia end-to-end per il disco del sistema operativo, i dischi dati e il disco di risorse effimero con una chiave gestita dal cliente.
- Se i requisiti includono la crittografia di tutti i dati precedenti e la crittografia end-to-end, usare Crittografia disco di Azure.If your requirements include encrypting all of the above and end-to-end encryption, use Azure Disk Encryption. 
- Se i requisiti includono la crittografia solo dei dati inattivi con la chiave gestita dal cliente, utilizzare la [crittografia lato server con chiavi gestite dal cliente](disk-encryption.md). Non è possibile crittografare un disco con crittografia lato server di Azure e crittografia lato server di archiviazione con chiavi gestite dal cliente. 
- Se la distribuzione di Linux non è elencata nei [sistemi operativi supportati per Crittografia disco](disk-encryption-overview.md#supported-operating-systems) di Azure o si usa uno scenario indicato negli scenari non supportati per [Windows](disk-encryption-linux.md#unsupported-scenarios), prendere in considerazione la [crittografia lato server con chiavi gestite dal cliente.](disk-encryption.md)
- Se i criteri dell'organizzazione consentono di crittografare il contenuto con una chiave gestita da Azure, non è necessaria alcuna azione: il contenuto viene crittografato per impostazione predefinita. Per i dischi gestiti, il contenuto all'interno dell'archiviazione viene crittografato per impostazione predefinita con la crittografia lato server con chiave gestita dalla piattaforma. La chiave è gestita dal servizio Archiviazione di Azure.The key is managed by the Azure Storage service. 



## <a name="how-do-i-rotate-secrets-or-encryption-keys"></a>Come si ruotano i segreti o le chiavi di crittografia?

Per ruotare i segreti, è sufficiente chiamare lo stesso comando utilizzato in origine per abilitare la crittografia del disco, specificando un insieme di credenziali delle chiavi diverso. Per ruotare la chiave di crittografia della chiave, chiamare lo stesso comando utilizzato in origine per abilitare la crittografia del disco, specificando la nuova crittografia della chiave. 

>[!WARNING]
> - Se in precedenza è stato usato Crittografia disco di [Azure con l'app Azure AD](disk-encryption-linux-aad.md) specificando le credenziali di Azure AD per crittografare questa macchina virtuale, è necessario continuare a usare questa opzione per crittografare la macchina virtuale. Non è possibile usare Crittografia disco di Azure in questa macchina virtuale crittografata perché non si tratta di uno scenario supportato, ovvero il passaggio dall'applicazione AAD per questa macchina virtuale crittografata non è ancora supportato.

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

Per usare Crittografia dischi di Azure, è necessario rispettare alcuni prerequisiti. Vedere [Crittografia del disco](disk-encryption-linux-aad.md) di Azure con contenuto di Azure AD per creare un'applicazione Azure Active Directory, creare un nuovo insieme di credenziali delle chiavi o configurare un insieme di credenziali delle chiavi esistente per l'accesso alla crittografia del disco per abilitare la crittografia e proteggere segreti e chiavi. Per altre informazioni sugli scenari di supporto delle chiavi di crittografia, vedere Creazione e configurazione di un insieme di credenziali delle chiavi per Crittografia disco di [Azure con Azure AD.](disk-encryption-key-vault-aad.md)

## <a name="is-azure-disk-encryption-using-an-azure-ad-app-previous-release-still-supported"></a>L'esecuzione di Crittografia dischi di Azure con un'app Azure AD (versione precedente) è ancora supportata?
Sì. L'esecuzione di Crittografia dischi con un'app Azure AD è ancora supportata. Tuttavia, quando si esegue la crittografia di nuove macchine virtuali, è consigliabile usare il nuovo metodo anziché un'app Azure AD. 

## <a name="can-i-migrate-vms-that-were-encrypted-with-an-azure-ad-app-to-encryption-without-an-azure-ad-app"></a>È possibile eseguire la migrazione di macchine virtuali crittografate con un'app Azure AD alla crittografia senza tale app?
  Non esiste attualmente un percorso di migrazione diretto per le macchine crittografate con un'app Azure AD alla crittografia senza tale app. Non esiste nemmeno un percorso diretto dalla crittografia senza un'app Azure AD alla crittografia con tale app. 

## <a name="what-version-of-azure-powershell-does-azure-disk-encryption-support"></a>Quale versione di Azure PowerShell è supportata da Crittografia dischi di Azure?

Usare la versione più recente di Azure PowerShell SDK per configurare Crittografia dischi di Azure. Scaricare la versione più recente di [Azure PowerShell](https://github.com/Azure/azure-powershell/releases). Crittografia dischi di Azure *non* è supportato da Azure SDK versione 1.1.0.

> [!NOTE]
> L'estensione di anteprima della crittografia del disco di Azure Linux "Microsoft.OSTCExtension.AzureDiskEncryptionForLinux" è deprecata. Questa estensione è stata pubblicata per la versione di anteprima della crittografia del disco di Azure.This extension was published for Azure disk encryption preview release. Non utilizzare la versione di anteprima dell'estensione nella distribuzione di test o di produzione.

> Per gli scenari di distribuzione come Azure Resource Manager (ARM), in cui è necessario distribuire l'estensione di crittografia del disco di Azure per la macchina virtuale Linux per abilitare la crittografia nella macchina virtuale Linux IaaS, è necessario usare l'estensione supportata di produzione della crittografia del disco di Azure "Microsoft.Azure.Security.AzureDiskEncryptionForLinux".

## <a name="can-i-apply-azure-disk-encryption-on-my-custom-linux-image"></a>È possibile applicare Crittografia dischi di Azure a un'immagine Linux personalizzata?

Non è possibile applicare Crittografia dischi di Azure a un'immagine Linux personalizzata. Sono consentite solo le immagini Linux per le distribuzioni supportate indicate in precedenza. Le immagini Linux personalizzate non sono attualmente supportate.

## <a name="can-i-apply-updates-to-a-linux-red-hat-vm-that-uses-the-yum-update"></a>È possibile applicare gli aggiornamenti a una VM Red Hat Linux che usa l'aggiornamento Yum?

Sì, è possibile eseguire un aggiornamento yum in una macchina virtuale Red Hat Linux.Yes, you can perform a yum update on a Red Hat Linux VM.  Per altre informazioni, vedere Crittografia disco di Azure in una [rete isolata.](disk-encryption-isolated-network.md)

## <a name="what-is-the-recommended-azure-disk-encryption-workflow-for-linux"></a>Qual è il flusso di lavoro di crittografia dischi di Azure consigliato per Linux?

Di seguito è riportato il flusso di lavoro consigliato per ottenere risultati ottimali in Linux:
* Iniziare dall'immagine della raccolta non modificata corrispondente alla distribuzione e alla versione del sistema operativo desiderate
* Eseguire il backup di tutte le unità montate che verranno crittografate.  In questo modo è possibile eseguire il ripristino in caso di errore, ad esempio se la macchina virtuale viene riavviata prima del completamento della crittografia.
* Eseguire la crittografia (può richiedere più ore o addirittura giorni a seconda delle caratteristiche della macchina virtuale e delle dimensioni di qualsiasi disco dati collegato)
* Personalizzare e aggiungere il software all'immagine in base alle esigenze.

Se questo flusso di lavoro non è possibile, l'uso della [crittografia del servizio di archiviazione](../../storage/common/storage-service-encryption.md) a livello di account di archiviazione della piattaforma può essere un'alternativa alla crittografia completa del disco tramite dm-crypt.

## <a name="what-is-the-disk-bek-volume-or-mntazure_bek_disk"></a>Che cos'è il disco "volume BEK" o "/mnt/azure_bek_disk"?

Il "volume Bek" è un volume di dati locale che archivia in modo sicuro le chiavi di crittografia per le macchine virtuali di Azure crittografate.
> [!NOTE]
> Non eliminare né modificare il contenuto del disco. Non smontare il disco, perché la presenza delle chiavi di crittografia è necessaria per qualsiasi operazione di crittografia sulla VM IaaS.


## <a name="what-encryption-method-does-azure-disk-encryption-use"></a>Qual è il metodo di crittografia usato da Crittografia dischi di Azure?

Crittografia disco di Azure usa il valore predefinito di decrittografia di aes-xts-plain64 con una chiave master del volume a 256 bit.

## <a name="if-i-use-encryptformatall-and-specify-all-volume-types-will-it-erase-the-data-on-the-data-drives-that-we-already-encrypted"></a>Se si utilizzano EncryptFormatAll e vengono specificati tutti i tipi di volume, verranno cancellati i dati sulle unità dati già crittografati?
No, i dati non verranno cancellati da unità dati che sono già crittografate usando Crittografia dischi di Azure. Analogamente al modo in cui EncryptFormatAll non crittografa nuovamente l'unità del sistema operativo, non riapplica la crittografia all'unità dati già crittografata. Per altre informazioni, vedere [Criteri EncryptFormatAll ](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).        

## <a name="is-xfs-filesystem-supported"></a>Il file system XFS è supportato?
I volumi XFS sono supportati per la crittografia del disco dati solo con EncryptFormatAll. Questo riformatto il volume, cancellando tutti i dati precedentemente lì. Per altre informazioni, vedere [Criteri EncryptFormatAll ](disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms).

## <a name="can-i-backup-and-restore-an-encrypted-vm"></a>È possibile eseguire il backup e il ripristino di una macchina virtuale crittografata? 

Backup di Azure offre un meccanismo per eseguire il backup e il ripristino delle macchine virtuali crittografate all'interno della stessa sottoscrizione e area.  Per istruzioni, vedere [Eseguire il backup e il ripristino di macchine virtuali crittografate con Backup di Azure.](https://docs.microsoft.com/azure/backup/backup-azure-vms-encryption)  Il ripristino di una macchina virtuale crittografata in un'area diversa non è attualmente supportato.  

## <a name="where-can-i-go-to-ask-questions-or-provide-feedback"></a>Quali risorse sono disponibili per porre domande o inviare commenti?

È possibile porre domande o inviare commenti e suggerimenti sul [forum di Crittografia dischi di Azure disponibile](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureDiskEncryption).

## <a name="next-steps"></a>Passaggi successivi
In questo documento sono state fornite le risposte alle domande più frequenti relative a Crittografia dischi di Azure. Per altre informazioni sul servizio, vedere gli articoli seguenti:

- [Panoramica di Crittografia dischi di AzureAzure Disk Encryption Overview](disk-encryption-overview.md)
- [Applicare la crittografia del disco nel Centro sicurezza Azure](https://docs.microsoft.com/azure/security-center/security-center-apply-disk-encryption)
- [Crittografia dei dati inattivi in Azure](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
