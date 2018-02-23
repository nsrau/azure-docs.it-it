---
title: Domande frequenti su Backup di Azure | Microsoft Docs
description: "Risposte alle domande frequenti su: funzionalità di Backup di Azure, inclusi insieme di credenziali di Servizi di ripristino, elementi di cui è possibile eseguire il backup, funzionamento, crittografia e limiti. "
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: backup e ripristino di emergenza; servizio Backup
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/21/2017
ms.author: markgal;arunak;trinadhk;sogup;
ms.openlocfilehash: d6ee96b17c6bc85a2278bbe98867a579ff9c550a
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/01/2018
---
# <a name="questions-about-the-azure-backup-service"></a>Domande sul servizio Backup di Azure
Questo articolo risponde alle domande comuni sui componenti di Backup di Azure. Alcune risposte includono collegamenti ad articoli con informazioni complete. Per porre domande su Backup di Azure, fare clic su **Commenti** a destra. I commenti vengono visualizzati alla fine di questo articolo. Per inserire commenti, è necessario un account Livefyre. È anche possibile inserire le domande sul servizio Backup di Azure nel [forum di discussione](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

Per analizzare rapidamente le sezioni di questo articolo, usare i collegamenti riportati a destra sotto **In questo articolo**.


## <a name="recovery-services-vault"></a>Insieme di credenziali dei servizi di ripristino

### <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>Esistono limiti al numero degli insiemi di credenziali che è possibile creare in ogni sottoscrizione di Azure? <br/>
Sì. A partire da settembre 2016 è possibile creare 25 insiemi di credenziali di Servizi di ripristino per ogni sottoscrizione. Si possono creare fino a 25 insiemi di credenziali dei servizi di ripristino per area di Backup di Azure supportata per ogni sottoscrizione. Se sono necessari più insiemi di credenziali, creare una sottoscrizione aggiuntiva.

### <a name="are-there-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>Esistono limiti al numero di server/computer che possono essere registrati in ogni insieme di credenziali? <br/>
È possibile registrare fino a 200 macchine virtuali di Azure per insieme di credenziali. Se si usano agenti MAB, è possibile registrare fino a 50 agenti MAB per insieme di credenziali. È possibile registrare 50 server MAB/server DPM in un insieme di credenziali.

### <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>Se l'organizzazione ha un insieme di credenziali, come è possibile isolare i dati di un server da un altro server durante il ripristino dei dati?<br/>
Tutti i server che vengono registrati nello stesso insieme di credenziali possono ripristinare i dati dei quali è stato eseguito il backup da altri server *che usano la stessa passphrase*. Se sono presenti server i cui dati di backup devono essere isolati dagli altri server dell'organizzazione, usare una passphrase designata per tali server. Ad esempio, per i server del reparto risorse umane può essere usata una passphrase, per quelli dell'ufficio contabilità un'altra e per quelli di archiviazione un'altra ancora.

### <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>È possibile eseguire la migrazione dei dati o dell'insieme di credenziali per il backup tra sottoscrizioni? <br/>
di serie L'insieme di credenziali viene creato a livello di sottoscrizione e non può essere riassegnato a un'altra sottoscrizione.

### <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-still-supported-br"></a>Gli insiemi di credenziali di Servizi di ripristino sono basati su Resource Manager. Gli insiemi di credenziali di backup sono ancora supportati? <br/>
Gli insiemi di credenziali di backup sono stati convertiti in insiemi di credenziali di Servizi di ripristino. Se non è stata eseguita la conversione dell'insieme di credenziali di backup in un insieme di credenziali di Servizi di ripristino, tale conversione è stata eseguita automaticamente. 

### <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>È possibile eseguire la migrazione di un insieme di credenziali per il backup in un insieme di credenziali di Servizi di ripristino? <br/>
Tutti gli insiemi di credenziali di backup sono stati convertiti in insiemi di credenziali di Servizi di ripristino. Se non è stata eseguita la conversione dell'insieme di credenziali di backup in un insieme di credenziali di Servizi di ripristino, tale conversione è stata eseguita automaticamente.

## <a name="azure-backup-agent"></a>Agente di Backup di Azure
Per un elenco dettagliato delle domande, vedere le [domande frequenti sul backup di file-cartelle di Azure](backup-azure-file-folder-backup-faq.md)

## <a name="azure-vm-backup"></a>Backup di macchine virtuali di Azure
Per un elenco dettagliato delle domande, vedere le [domande frequenti sul backup di macchine virtuali di Azure](backup-azure-vm-backup-faq.md)

## <a name="back-up-vmware-servers"></a>Eseguire il backup dei server VMware

### <a name="can-i-back-up-vmware-vcenter-servers-to-azure"></a>È possibile eseguire il backup dei server VMware vCenter in Azure?

Sì. È possibile usare il server di Backup di Azure per eseguire il backup di VMware vCenter ed ESXi in Azure. Per informazioni sulla versione di VMware supportata, vedere l'articolo [Matrice di protezione del server di Backup di Azure](backup-mabs-protection-matrix.md). Per istruzioni dettagliate, vedere [Usare il server di Backup di Azure per eseguire il backup di un server VMware](backup-azure-backup-server-vmware.md).


## <a name="azure-backup-server-and-system-center-data-protection-manager"></a>Server di Backup di Azure e System Center Data Protection Manager
### <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>È possibile usare il server di Backup di Azure per creare un backup di ripristino bare metal per un server fisico? <br/>
Sì.

### <a name="can-i-register-my-dpm-server-to-multiple-vaults-br"></a>È possibile registrare un server Data Protection Manager in più insiemi di credenziali? <br/>
di serie Un server di Backup di Microsoft Azure o un server Data Protection Manager può essere registrato in un solo insieme di credenziali.

### <a name="which-version-of-system-center-data-protection-manager-is-supported-br"></a>Quale versione di System Center Data Protection Manager è supportata? <br/>
È consigliabile installare l'agente di Backup di Azure [più recente](http://aka.ms/azurebackup_agent) nell'ultimo aggiornamento cumulativo di System Center Data Protection Manager (DPM). Ad agosto 2016, il più recente è l'aggiornamento cumulativo 11.

### <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-system-center-dpm-to-work-with-azure-backup-agent-to-protect-on-premises-applicationvm-workloads-to-azure-br"></a>È stato installato l'agente di Backup di Azure per proteggere i file e le cartelle. È ora possibile installare System Center DPM per usare l'agente di Backup di Azure per proteggere i carichi di lavoro di applicazioni/VM locali in Azure? <br/>
Per usare Azure Backup con System Center Data Protection Manager (DPM), installare DPM prima di installare Azure Backup Agent. L'installazione dei componenti di Backup di Azure in questo ordine assicura che l'agente di Backup di Azure funziona correttamente con DPM. L'installazione di Azure Backup Agent prima di installare DPM non è consigliabile o supportata.


## <a name="how-azure-backup-works"></a>Funzionamento di Backup di Azure
### <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>Se si annulla un processo di backup una volta avviato, i dati di backup trasferiti vengono eliminati? <br/>
di serie Tutti i dati trasferiti nell'insieme di credenziali prima dell'annullamento del processo di backup rimangono nell'insieme di credenziali. Backup di Azure usa un meccanismo di checkpoint per aggiungere occasionalmente checkpoint ai dati di backup durante il backup. Dato che sono presenti checkpoint nei dati di backup, il processo di backup successivo può convalidare l'integrità dei file. Il processo di backup successivo sarà incrementale nei backup di dati eseguiti in precedenza. I backup incrementali trasferiscono solo dati nuovi o modificati, il che equivale a un migliore utilizzo della larghezza di banda.

Se si annulla un processo di backup per una macchina virtuale di Azure, tutti i dati trasferiti vengono ignorati. Il processo di backup successivo trasferisce i dati incrementali dall'ultimo processo di backup riuscito.

### <a name="are-there-limits-on-when-or-how-many-times-a-backup-job-can-be-scheduledbr"></a>Esistono limiti in relazione a quando o quante volte può essere pianificato un processo di backup?<br/>
Sì. È possibile eseguire processi di backup in Windows Server o nelle workstation Windows fino a tre volte al giorno. È possibile eseguire processi di backup in System Center DPM fino a due volte al giorno. È possibile eseguire un processo di backup per le VM IaaS una volta al giorno. È possibile usare i criteri di pianificazione per Windows Server o la workstation Windows per specificare pianificazioni giornaliere o settimanali. Con System Center DPM, è possibile specificare pianificazioni giornaliere, settimanali, mensili e annuali.

### <a name="why-is-the-size-of-the-data-transferred-to-the-recovery-services-vault-smaller-than-the-data-i-backed-upbr"></a>Perché le dimensioni dei dati trasferiti nell'insieme di credenziali dei servizi di ripristino sono inferiori a quelle dei dati sottoposti a backup?<br/>
 Tutti i dati di cui viene eseguito il backup dall'agente di Backup di Azure, da SCDPM o dal server di Backup di Azure vengono compressi e crittografati prima di essere trasferiti. Dopo l'applicazione della compressione e della crittografia, i dati nell'insieme di credenziali di Servizi di ripristino sono ridotti del 30-40%.

## <a name="what-can-i-back-up"></a>Backup consentiti
### <a name="which-operating-systems-do-azure-backup-support-br"></a>Quali sistemi operativi sono supportati da Backup di Azure? <br/>
Backup di Azure supporta i sistemi operativi elencati di seguito per il backup di file, cartelle e applicazioni del carico di lavoro protetti con il server di Backup di Azure e System Center Data Protection Manager (DPM).

| Sistema operativo | Piattaforma | SKU |
|:--- | --- |:--- |
| Windows 8 e versioni più recenti di SP |64 bit |Enterprise, Pro |
| Windows 7 e versioni più recenti di SP |64 bit |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 e versioni più recenti di SP |64 bit |Enterprise, Pro |
| Windows 10 |64 bit |Enterprise, Pro, Home |
| Windows Server 2016 |64 bit |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 e più recenti SPs |64 bit |Standard, Datacenter, Foundation |
| Windows Server 2012 e versioni più recenti di SP |64 bit |Datacenter, Foundation, Standard |
| Windows Storage Server 2016 e versioni più recenti di SP |64 bit |Standard, Workgroup | 
| Windows Storage Server 2012 R2 e versioni più recenti di SP |64 bit |Standard, Workgroup |
| Windows Storage Server 2012 e versioni più recenti di SP |64 bit |Standard, Workgroup |
| Windows Server 2012 R2 e più recenti SPs |64 bit |Essential |
| Windows Server 2008 R2 SP1 |64 bit |Standard, Enterprise, Datacenter, Foundation |

**Per il backup di VM di Azure**

* **Linux**: Backup di Azure supporta [un elenco di distribuzioni approvate da Azure](../virtual-machines/linux/endorsed-distros.md) , ad eccezione di CoreOS Linux.  È possibile usare altre distribuzioni Bring Your Own Linux, a condizione che l'agente di macchine virtuali sia disponibile nella macchina virtuale e sia configurato il supporto per Python.
* **Windows Server**: le versioni precedenti a Windows Server 2008 R2 non sono supportate.


### <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>Esiste un limite alle dimensioni di ogni origine dati sottoposta a backup? <br/>
Non c'è nessun limite alla quantità di dati di cui è possibile eseguire il backup in un insieme di credenziali. Backup di Azure limita le dimensioni massime per l'origine dati, che sono tuttavia grandi. A partire da agosto 2015, le dimensioni massime di un'origine dati per i sistemi operativi supportati sono:

| Numero S. | Sistema operativo | Dimensione massima origine dati |
|:---:|:--- |:--- |
| 1 |Windows Server 2012 o versioni successive |54400 GB |
| 2 |Windows 8 o versione successiva |54400 GB |
| 3 |Windows Server 2008, Windows Server 2008 R2 |1700 GB |
| 4 |Windows 7 |1700 GB |

La tabella seguente illustra come vengono determinate le dimensioni di ogni origine dati.

| Origine dati | Dettagli |
|:---:|:--- |
| Volume |Quantità di dati sottoposti a backup dal volume singolo di un computer server o client. |
| Macchina virtuale Hyper-V |Somma dei dati di tutti i dischi rigidi virtuali della macchina virtuale di cui viene eseguito il backup |
| Database di Microsoft SQL Server |Dimensioni di un singolo database SQL di cui viene eseguito il backup |
| Microsoft SharePoint |Somma dei database di contenuto e di configurazione in una farm di SharePoint di cui viene eseguito il backup |
| Microsoft Exchange |Somma di tutti i database di Exchange in un server di Exchange di cui viene eseguito il backup |
| Stato del sistema/ripristino bare metal |Ogni copia del ripristino bare metal o dello stato del sistema del computer di cui viene eseguito il backup |

Per il backup di macchine virtuali di Azure, ogni macchina virtuale può avere fino a 16 dischi di dati, ognuno dei quali di dimensioni pari o inferiori a 1023 GB. 

## <a name="retention-policy-and-recovery-points"></a>Criteri di conservazione e punti di ripristino
### <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-that-is-on-windows-server-without-dpmbr"></a>Esistono differenze tra i criteri di conservazione per DPM e Windows Server/client Windows (ossia in Windows Server senza DPM)?<br/>
No, sia DPM che Windows Server/client prevedono criteri di conservazione giornalieri, settimanali, mensili e annuali.

### <a name="can-i-configure-my-retention-policies-selectively--ie-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>È possibile configurare i criteri di conservazione in modo selettivo, ad esempio con frequenza settimanale e giornaliera, ma non annuale e mensile?<br/>
Sì, la struttura di memorizzazione del Backup di Azure consente di disporre della massima flessibilità nella definizione dei criteri di conservazione in base alle esigenze.

### <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>È possibile "pianificare un backup" alle 18.00 e specificare criteri di conservazione con un orario diverso?<br/>
di serie I criteri di conservazione possono essere applicati solo ai punti di backup. Nell'immagine seguente viene specificato il criterio di conservazione per i backup eseguiti a mezzanotte e alle 18. <br/>

![Pianificare backup e conservazione](./media/backup-azure-backup-faq/Schedule.png)
<br/>

### <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Se una copia di backup viene conservata a lungo, è necessario più tempo per ripristinare un punto dati meno recente? <br/>
No, il tempo necessario per ripristinare il punto meno recente o il più recente è lo stesso. Ogni punto di ripristino si comporta come un punto completo.

### <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>Se ogni punto di ripristino si comporta come un punto completo, questo influisce sul totale dell'archiviazione di backup fatturabile?<br/>
I punti di conservazione tipici a lungo termine archiviano i dati di backup come punti completi. I punti completi sono *inefficienti* dal punto di vista dell'archiviazione, ma consentono un ripristino più facile e veloce. Le copie incrementali sono *efficienti* dal punto di vista dell'archiviazione, ma richiedono il ripristino di una catena di dati, che influisce sui tempi di ripristino. L'architettura di archiviazione di Backup di Azure offre il meglio dei due mondi, garantendo un'archiviazione dei dati ottimale per ripristini veloci e costi di archiviazione ridotti. Questo approccio all'archiviazione dati assicura che la larghezza di banda in ingresso e in uscita venga usata in modo efficiente. Sia la quantità dell'archivio dati che il tempo necessario per ripristinare i dati vengono mantenuti a un livello minimo. Sono disponibili altre informazioni sull'efficienza dei [backup incrementali](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/).

### <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>Esiste un limite al numero di punti di ripristino che è possibile creare?<br/>
Per ogni istanza protetta, è possibile creare fino a 9999 punti di ripristino. Un'istanza protetta è un computer, un server (fisico o virtuale) o un carico di lavoro configurato per eseguire il backup dei dati in Azure. Per altre informazioni, vedere le spiegazioni disponibili in [Backup e conservazione](./backup-introduction-to-azure-backup.md#backup-and-retention) e [Che cos'è un'istanza protetta?](./backup-introduction-to-azure-backup.md#what-is-a-protected-instance).

### <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Quanti ripristini è possibile eseguire sui dati sottoposti a backup in Azure?<br/>
Non esistono limiti al numero di ripristini da Backup di Azure.

### <a name="when-restoring-data-do-i-pay-for-the-egress-traffic-from-azure-br"></a>Quando si ripristinano i dati, vengono addebitati costi per il traffico in uscita da Azure? <br/>
di serie I ripristini sono gratuiti e non viene addebitato alcun costo per il traffico in uscita.

### <a name="what-happens-when-i-change-my-backup-policy"></a>Che cosa succede quando si modificano i criteri di backup?
Quando vengono applicati nuovi criteri, vengono seguite la pianificazione e la conservazione stabilite dai nuovi criteri. Se il periodo di conservazione viene esteso, i punti di ripristino esistenti vengono contrassegnati in modo che vengano mantenuti in base ai nuovi criteri. Se il periodo di conservazione viene ridotto, vengono contrassegnati per l'eliminazione ed eliminati nel successivo processo di pulizia.

## <a name="azure-backup-encryption"></a>Crittografia in Backup di Azure
### <a name="is-the-data-sent-to-azure-encrypted-br"></a>I dati inviati in Azure sono crittografati? <br/>
Sì. I dati vengono crittografati nel computer server/client/SCDPM locale mediante AES256 e i dati vengono inviati tramite un collegamento HTTPS sicuro.

### <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Anche i dati di backup in Azure sono crittografati?<br/>
Sì. I dati inviati ad Azure rimangono crittografati (inattivi). Microsoft non decrittografa mai i dati di backup. Quando si esegue il backup di una macchina virtuale di Azure, Backup di Azure si basa sulla crittografia della macchina virtuale. Ad esempio, se la macchina virtuale è crittografata con Crittografia dischi di Azure o un'altra tecnologia di crittografia , Backup di Azure usa tale crittografia per proteggere i dati.

### <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>Qual è la lunghezza minima della chiave di crittografia usata per crittografare i dati di backup? <br/>
Quando si usa l'agente di Backup di Azure, la chiave di crittografia deve contenere almeno 16 caratteri. Per le macchine virtuali di Azure non c'è un limite di lunghezza delle chiavi usate da Azure Key Vault. 

### <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>Cosa accade se si smarrisce la chiave di crittografia? È possibile ripristinare i dati? Microsoft può recuperarli? <br/>
La chiave usata per crittografare i dati di backup è disponibile solo nelle risorse del cliente. Microsoft non ne conserva una copia in Azure e non dispone dell'accesso alla chiave. Se il cliente smarrisce la chiave, Microsoft non può recuperare i dati di backup.
