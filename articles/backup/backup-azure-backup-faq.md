
---
title: Domande frequenti su Backup di Azure | Microsoft Docs
description: Risposte alle domande frequenti sul servizio Backup, l&quot;agente di Backup, backup e conservazione, ripristino, sicurezza e altre domande frequenti sul backup e sul ripristino di emergenza.
services: backup
documentationcenter: 
author: markgalioto
manager: jwhit
editor: 
keywords: backup e ripristino di emergenza; servizio Backup
ms.assetid: 1011bdd6-7a64-434f-abd7-2783436668d7
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 11/16/2016
ms.author: trinadhk; giridham; arunak; markgal; jimpark;
translationtype: Human Translation
ms.sourcegitcommit: 1b2eeab756309148db5890ccc0d93853f3e69372
ms.openlocfilehash: 4b7a8a88d21b11deb377c2fef4e2d8f9fbcf9036


---
# <a name="azure-backup-service--faq"></a>Servizio Backup di Azure: Domande frequenti
Questo articolo contiene un elenco di domande comuni (e le relative risposte) sul servizio Backup di Azure. La community risponde rapidamente e, se una domanda viene fatta spesso, viene aggiunta a questo articolo. Le risposte alle domande contengono in genere informazioni di riferimento o di supporto. È possibile formulare le domande su Backup di Azure nella sezione Disqus di questo articolo o di un articolo correlato. È anche possibile inserire le domande sul servizio Backup di Azure nel [forum di discussione](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazureonlinebackup).

## <a name="what-is-the-list-of-supported-operating-systems-from-which-i-can-back-up-to-azure-using-azure-backup-br"></a>Qual è l'elenco dei sistemi operativi supportati da cui è possibile eseguire il backup in Azure con Backup di Azure? <br/>
Backup di Azure supporta i sistemi operativi elencati di seguito per il backup: file, cartelle e applicazioni di carico di lavoro protetti tramite server di Backup di Azure e SCDPM.

| Sistema operativo | Piattaforma | SKU |
|:--- | --- |:--- |
| Windows 8 e versioni più recenti di SP |64 bit |Enterprise, Pro |
| Windows 7 e versioni più recenti di SP |64 bit |Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| Windows 8.1 e versioni più recenti di SP |64 bit |Enterprise, Pro |
| Windows 10 |64 bit |Enterprise, Pro, Home |
| Windows Server 2016 |64 bit |Standard, Datacenter, Essentials |
| Windows Server 2012 R2 e più recenti SPs |64 bit |Standard, Datacenter, Foundation |
| Windows Server 2012 e versioni più recenti di SP |64 bit |Datacenter, Foundation, Standard |
| Windows Storage Server 2012 R2 e versioni più recenti di SP |64 bit |Standard, Workgroup |
| Windows Storage Server 2012 e versioni più recenti di SP |64 bit |Standard, Workgroup |
| Windows Server 2012 R2 e più recenti SPs |64 bit |Essential |
| Windows Server 2008 R2 SP1, |64 bit |Standard, Enterprise, Datacenter, Foundation |
| Windows Server 2008 SP2 |64 bit |Standard, Enterprise, Datacenter, Foundation |

Per il backup di VM di Azure,

* **Linux**: Backup di Azure supporta [un elenco di distribuzioni approvate da Azure](../virtual-machines/virtual-machines-linux-endorsed-distros.md) , ad eccezione di CoreOS Linux.  È possibile usare altre distribuzioni Bring Your Own Linux, a condizione che l'agente di macchine virtuali sia disponibile nella macchina virtuale e sia configurato il supporto per Python.
* **Windows Server**: le versioni precedenti a Windows Server 2008 R2 non sono supportate.

## <a name="where-can-i-download-the-latest-azure-backup-agent-br"></a>Dov'è possibile scaricare l'agente di Backup di Azure più recente? <br/>
È possibile scaricare l'agente più recente per il backup di Windows Server, System Center DPM o dei client Windows da [qui](http://aka.ms/azurebackup_agent). Per eseguire il backup di una macchina virtuale usare l'agente di macchine virtuali, che installa automaticamente l'estensione appropriata. L'agente di VM è già presente nelle macchine virtuali create dalla raccolta di Azure.

## <a name="which-version-of-scdpm-server-is-supported-br"></a>Quale versione del server SCDPM è supportata? <br/>
È consigliabile installare la versione [più recente](http://aka.ms/azurebackup_agent) dell'agente di Backup di Azure nell'aggiornamento cumulativo più recente di SCDPM (UR11 ad agosto 2016)

## <a name="when-configuring-the-azure-backup-agent-i-am-prompted-to-enter-the-vault-credentials-do-vault-credentials-expire"></a>Quando si configura l'agente di Backup di Azure, viene richiesto di immettere le credenziali dell'insieme di credenziali. Le credenziali dell'insieme di credenziali scadono?
Sì, le credenziali dell'insieme di credenziali scadono dopo 48 ore. Se il file scade, accedere al portale di Azure e scaricare i file delle credenziali dell'insieme di credenziali dall'insieme di credenziali.

## <a name="is-there-any-limit-on-the-number-of-vaults-that-can-be-created-in-each-azure-subscription-br"></a>Esistono limiti al numero degli insiemi di credenziali che è possibile creare in ogni sottoscrizione di Azure? <br/>
Sì. A partire da settembre 2016 è possibile creare 25 insiemi di credenziali di backup per ogni sottoscrizione. È possibile creare fino a 25 insiemi di credenziali di Servizi di ripristino per ogni area supportata di Backup di Azure per ogni sottoscrizione. Se sono necessari più insiemi di credenziali, creare una nuova sottoscrizione.

## <a name="are-there-any-limits-on-the-number-of-serversmachines-that-can-be-registered-against-each-vault-br"></a>Esistono limiti al numero di server/computer che possono essere registrati in ogni insieme di credenziali? <br/>
Sì, è possibile registrare fino a 50 computer per ogni insieme di credenziali. Per le macchine virtuali IaaS di Azure, il limite è di 200 macchine virtuali per ogni insieme di credenziali. Se è necessario registrare più computer, creare un altro insieme di credenziali.

## <a name="how-do-i-register-my-server-to-another-datacenterbr"></a>Come si registra un server in un altro data center?<br/>
I dati di backup vengono inviati al data center dell'insieme di credenziali in cui sono registrati. Il modo più semplice per modificare il data center consiste nel disinstallare e reinstallare l'agente e registrarlo in un nuovo insieme di credenziali appartenente al data center desiderato.

## <a name="what-happens-if-i-rename-a-windows-server-that-is-backing-up-data-to-azurebr"></a>Cosa accade se si rinomina un server Windows che esegue il backup dei dati in Azure?<br/>
Quando si rinomina un server, tutti i backup attualmente configurati vengono arrestati.
Registrare il nome nuovo del server con l'insieme di credenziali di Backup. Quando si registra il nome nuovo con l'insieme di credenziali, il primo backup sarà *completo*. Se è necessario recuperare dati di cui in precedenza è stato eseguito il backup nell'insieme di credenziali con il nome server precedente, è possibile recuperarli usando l'opzione [**Un altro server**](backup-azure-restore-windows-server.md#recover-to-an-alternate-machine) della procedura guidata **Ripristina dati**.

## <a name="what-types-of-drives-can-i-backup-files-and-folders-from-br"></a>Da quali tipi di unità è possibile eseguire il backup di file e cartelle? <br/>
Non è possibile eseguire il backup del set di unità/volumi seguente:

* Supporti rimovibili: l'unità deve essere segnalata come fissa per poter essere usata come origine di backup.
* Volumi di sola lettura: il volume deve essere accessibile in scrittura per garantire il funzionamento del servizio Copia Shadow del volume (VSS).
* Volumi offline: il volume deve essere online per garantire il funzionamento del servizio VSS.
* Condivisione di rete: il volume deve essere in locale nel server per poter essere sottoposto a backup online.
* Volumi protetti da BitLocker: il volume deve essere sbloccato prima di poter eseguire il backup.
* Identificazione del sistema del file: NTFS è l'unico file system supportato per questa versione del servizio di backup online.

## <a name="what-file-and-folder-types-can-i-back-up-from-my-serverbr"></a>Di quali tipi di file e cartelle è possibile eseguire il backup dal server?<br/>
Sono supportati i tipi seguenti:

* Crittografato
* Compresso
* Sparse
* Compresso + Sparse
* Collegamenti reali: non supportato, ignorato
* Punto di analisi: non supportato, ignorato
* Crittografato + compresso: non supportato, ignorato
* Crittografato + Sparse: non supportato, ignorato
* Flusso compresso: non supportato, ignorato
* Flusso di tipo sparse: non supportato, ignorato

## <a name="whats-the-minimum-size-requirement-for-the-cache-folder-br"></a>Qual è il requisito di dimensioni minime per la cartella della cache? <br/>
La dimensione della cartella della cache determina la quantità di dati sottoposti a backup. La cartella della cache deve essere circa il 5% dello spazio necessario per l'archivio dati.

## <a name="if-my-organization-has-one-vault-how-can-i-isolate-one-servers-data-from-another-server-when-restoring-databr"></a>Se l'organizzazione ha un insieme di credenziali, come è possibile isolare i dati di un server da un altro server durante il ripristino dei dati?<br/>
Tutti i server che vengono registrati nello stesso insieme di credenziali possono ripristinare i dati dei quali è stato eseguito il backup da altri server *che usano la stessa passphrase*. Se sono presenti server i cui dati di backup devono essere isolati dagli altri server dell'organizzazione, usare una passphrase designata per tali server. Ad esempio, per i server del reparto risorse umane può essere usata una passphrase, per quelli dell'ufficio contabilità un'altra e per quelli di archiviazione un'altra ancora.

## <a name="can-i-migrate-my-backup-data-or-vault-between-subscriptions-br"></a>È possibile eseguire la migrazione dei dati o dell'insieme di credenziali per il backup tra sottoscrizioni? <br/>
No. L'insieme di credenziali viene creato a livello di sottoscrizione e non può essere riassegnato a un'altra sottoscrizione.

## <a name="does-the-azure-backup-agent-work-on-a-server-that-uses-windows-server-2012-deduplication-br"></a>L'agente di Backup di Azure funziona in un server che usa la deduplicazione di Windows Server 2012? <br/>
Sì. Il servizio agente converte i dati deduplicati in dati normali quando si prepara l'operazione di backup. Quindi ottimizza i dati per il backup, crittografa i dati e infine invia i dati crittografati al servizio di backup online.

## <a name="if-i-cancel-a-backup-job-once-it-has-started-is-the-transferred-backup-data-deleted-br"></a>Se si annulla un processo di backup una volta avviato, i dati di backup trasferiti vengono eliminati? <br/>
No. Tutti i dati trasferiti nell'insieme di credenziali, prima del punto di annullamento, rimangono nell'insieme di credenziali. Backup di Azure usa un meccanismo di checkpoint per aggiungere occasionalmente checkpoint ai dati di backup durante il backup. Dato che sono presenti checkpoint nei dati di backup, il processo di backup successivo può convalidare l'integrità dei file. Il processo di backup successivo sarà incrementale nei backup di dati eseguiti in precedenza. I backup incrementali trasferiscono solo dati nuovi o modificati, che equivalgono a un migliore utilizzo della larghezza di banda.

Se si annulla un processo di backup per una macchina virtuale di Azure, tutti i dati trasferiti vengono ignorati. Il processo di backup successivo trasferisce i dati incrementali dall'ultimo processo di backup riuscito.

## <a name="why-am-i-seeing-the-warning-azure-backups-have-not-been-configured-for-this-server-even-though-i-had-scheduled-regular-backups-previously-br"></a>Perché viene visualizzato l'avviso "I backup di Azure non sono stati configurati per questo server", anche se in precedenza sono stati pianificati backup regolari? <br/>
Questo avviso viene generato quando le impostazioni di pianificazione di backup archiviate nel server locale non sono identiche alle impostazioni archiviate nell'insieme di credenziali di backup. Quando il server o le impostazioni sono state ripristinate a uno stato noto soddisfacente, le pianificazioni di backup possono perdere la sincronizzazione. Se viene visualizzato questo avviso, [riconfigurare i criteri di backup](backup-azure-manage-windows-server.md) e quindi **eseguire subito il backup** per risincronizzare il server locale con Azure.

## <a name="what-firewall-rules-should-be-configured-for-azure-backup-br"></a>Quali regole del firewall devono essere configurate per Backup di Azure? <br/>
Per proteggere facilmente i dati da locale ad Azure e da carico di lavoro ad Azure, è consigliabile consentire al firewall di comunicare con gli URL seguenti:

* www.msftncsi.com
* \*.Microsoft.com
* \*.WindowsAzure.com
* \*.microsoftonline.com
* \*.windows.net

## <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-already-backed-by-the-azure-backup-service-using-the-vm-extension-br"></a>È possibile installare l'agente di Backup di Azure in una VM di Azure già supportata dal servizio Backup di Azure usando l'estensione per le VM? <br/>
Certo. Backup di Azure consente il backup a livello di VM per le VM di Azure usando l'estensione per le VM. È possibile installare Azure Backup Agent in un sistema operativo guest Windows per proteggere file e cartelle nel sistema operativo guest.

## <a name="can-i-install-the-azure-backup-agent-on-an-azure-vm-to-back-up-files-and-folders-present-on-temporary-storage-provided-by-the-azure-vm-br"></a>È possibile installare l'agente di Backup di Azure in una VM di Azure per eseguire il backup di file e cartelle presenti nell'archivio temporaneo fornito dalla VM di Azure? <br/>
Sì. Installare Azure Backup Agent nel sistema operativo guest Windows ed eseguire il backup di file e cartelle in un'archiviazione temporanea. Si noti tuttavia che l'esecuzione dei backup non riesce se i dati di archiviazione temporanea vengono cancellati. Se poi i dati nell'archivio temporaneo sono stati eliminati, è possibile eseguire il ripristino solo in un archivio non temporaneo.

## <a name="i-have-installed-azure-backup-agent-to-protect-my-files-and-folders-can-i-now-install-scdpm-to-work-with-azure-backup-agent-to-protect-on-premises-applicationvm-workloads-to-azure-br"></a>È stato installato l'agente di Backup di Azure per proteggere i file e le cartelle. Ora è possibile installare SCDPM per usare l'agente di Backup di Azure per proteggere i carichi di lavoro di applicazioni/VM locali in Azure? <br/>
Per usare Azure Backup con System Center Data Protection Manager (DPM), installare DPM prima di installare Azure Backup Agent. L'installazione dei componenti di Backup di Azure in questo ordine assicura che Azure Backup Agent funzionerà correttamente con DPM. L'installazione di Azure Backup Agent prima di installare DPM non è consigliabile o supportata.

## <a name="what-is-the-length-of-file-path-that-can-be-specified-as-part-of-azure-backup-policy-using-azure-backup-agent-br"></a>Qual è la lunghezza del percorso file che può essere specificata come parte dei criteri di Backup di Azure usando l'agente di Backup di Azure? <br/>
L'agente di Backup di Azure si basa su NTFS. La[ lunghezza del percorso del file è limitata dall'API Windows](https://msdn.microsoft.com/library/aa365247.aspx#fully_qualified_vs._relative_paths). Se si vuole eseguire il backup di file il cui percorso supera la lunghezza specificata dall'API Windows, è possibile scegliere di eseguire il backup della cartella superiore o dell'unità disco dei file di backup.  

## <a name="what-characters-are-allowed-in-file-path-of-azure-backup-policy-using-azure-backup-agent-br"></a>Quali caratteri sono consentiti nel percorso file dei criteri di Backup di Azure che usano l'agente di Backup di Azure? <br>
 L'agente di Backup di Azure si basa su NTFS. Consente i [caratteri supportati da NTFS](https://msdn.microsoft.com/library/aa365247.aspx#naming_conventions) come parte della specifica file.  

## <a name="can-i-use-azure-backup-server-to-create-a-bare-metal-recovery-bmr-backup-for-a-physical-server-br"></a>È possibile usare il server di Backup di Azure per creare un backup di ripristino bare metal per un server fisico? <br/>
Sì.

## <a name="can-i-configure-the-backup-service-to-send-mail-if-a-backup-job-fails-br"></a>È possibile configurare il servizio Backup per inviare un messaggio se un processo di backup non riesce? <br/>
Sì, il servizio Backup ha diversi avvisi basati su eventi che possono essere usati con uno script di PowerShell. Per una descrizione completa, vedere come [configurare le notifiche](backup-azure-monitor-vms.md#configure-notifications)

## <a name="is-there-a-limit-on-the-size-of-each-data-source-being-backed-up-br"></a>Esiste un limite alle dimensioni di ogni origine dati sottoposta a backup? <br/>
Non c'è nessun limite alla quantità di dati di cui è possibile eseguire il backup in un insieme di credenziali. Backup di Azure limita le dimensioni massime per l'origine dati, che sono tuttavia molto grandi. A partire da agosto 2015, le dimensioni massime di un'origine dati per i sistemi operativi supportati sono:

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

## <a name="are-there-limits-on-the-number-of-times-a-backup-job-can-be-scheduled-per-daybr"></a>Esistono limiti al numero di processi di backup giornalieri che è possibile pianificare?<br/>
Sì, è possibile eseguire processi di backup in Windows Server o nei client Windows fino a tre volte al giorno. È possibile eseguire processi di backup in System Center DPM fino a due volte al giorno. È possibile eseguire un processo di backup per le VM IaaS una volta al giorno.

## <a name="is-there-a-difference-between-the-scheduling-policy-for-dpm-and-windows-server-ie-on-windows-server-without-dpm-br"></a>Esistono differenze tra i criteri di pianificazione per DPM e Windows Server (ad esempio, Windows Server senza DPM)? <br/>
Sì. Usando DPM, è possibile specificare pianificazioni giornaliere, settimanali, mensili e annuali. Windows Server (senza DPM) consente di specificare solo pianificazioni giornaliere e settimanali.

## <a name="is-there-a-difference-between-the-retention-policy-for-dpm-and-windows-serverclient-ie-on-windows-server-without-dpmbr"></a>Esistono differenze tra i criteri di conservazione per DPM e Windows Server/client Windows, ad esempio in Windows Server senza DPM?<br/>
No, sia DPM che Windows Server/client prevedono criteri di conservazione giornalieri, settimanali, mensili e annuali.

## <a name="can-i-configure-my-retention-policies-selectively--ie-configure-weekly-and-daily-but-not-yearly-and-monthlybr"></a>È possibile configurare i criteri di conservazione in modo selettivo, ad esempio con frequenza settimanale e giornaliera, ma non annuale e mensile?<br/>
Sì, la struttura di memorizzazione del Backup di Azure consente di disporre della massima flessibilità nella definizione dei criteri di conservazione in base alle esigenze.

## <a name="can-i-schedule-a-backup-at-6pm-and-specify-retention-policies-at-a-different-timebr"></a>È possibile "pianificare un backup" alle 18:00 e specificare i "criteri di conservazione" a un orario diverso?<br/>
No. I criteri di conservazione possono essere applicati solo ai punti di backup. Nell'immagine seguente viene specificato il criterio di conservazione per i backup eseguiti a mezzanotte e alle 18. <br/>

![Pianificare backup e conservazione](./media/backup-azure-backup-faq/Schedule.png)
<br/>

## <a name="is-an-incremental-copy-transferred-for-the-retention-policies-scheduled-br"></a>Viene trasferita una copia incrementale per i criteri di conservazione pianificati? <br/>
No, la copia incrementale viene inviata in base all'ora indicata nella pagina di pianificazione del backup. I punti che possono essere conservati vengono determinati in base ai criteri di conservazione.

## <a name="if-a-backup-is-retained-for-a-long-duration-does-it-take-more-time-to-recover-an-older-data-point-br"></a>Se una copia di backup viene conservata a lungo, è necessario più tempo per ripristinare un punto dati meno recente? <br/>
 No, il tempo necessario per ripristinare il punto meno recente o il più recente è lo stesso. Ogni punto di ripristino si comporta come un punto completo.

## <a name="if-each-recovery-point-is-like-a-full-point-does-it-impact-the-total-billable-backup-storagebr"></a>Se ogni punto di ripristino si comporta come un punto completo, questo influisce sul totale dell'archiviazione di backup fatturabile?<br/>
I punti di conservazione tipici a lungo termine archiviano i dati di backup come punti completi. I punti completi sono *inefficienti* dal punto di vista dell'archiviazione, ma consentono un ripristino più facile e veloce. Le copie incrementali sono *efficienti* dal punto di vista dell'archiviazione, ma richiedono il ripristino di una catena di dati, che influisce sui tempi di ripristino. L'architettura di archiviazione di Backup di Azure offre il meglio dei due mondi, garantendo un'archiviazione dei dati ottimale per ripristini veloci e costi di archiviazione ridotti. Questo approccio all'archiviazione dati assicura che la larghezza di banda in ingresso e in uscita venga usata in modo efficiente. Sia la quantità dell'archivio dati che il tempo necessario per ripristinare i dati vengono mantenuti a un livello minimo. Altre informazioni sui risparmi consentiti dai [backup incrementali](https://azure.microsoft.com/blog/microsoft-azure-backup-save-on-long-term-storage/) .

## <a name="is-there-a-limit-on-the-number-of-recovery-points-that-can-be-createdbr"></a>Esiste un limite al numero di punti di ripristino che è possibile creare?<br/>
No. Sono stati eliminati i limiti nei punti di ripristino. È possibile creare tutti i punti di ripristino che si desidera.

## <a name="why-is-the-amount-of-data-transferred-in-backup-not-equal-to-the-amount-of-data-i-backed-upbr"></a>Perché la quantità di dati trasferiti nel backup non equivale alla quantità di dati sottoposti a backup?<br/>
 Tutti i dati di cui viene eseguito il backup dall'agente di Backup di Azure, da SCDPM o dal server di Backup di Azure vengono compressi e crittografati prima di essere trasferiti. Una volta applicate la compressione e la crittografia, i dati nell'insieme di credenziali per il backup sono ridotti del 30-40%.

## <a name="is-there-a-way-to-adjust-the-amount-of-bandwidth-used-by-the-backup-servicebr"></a>È possibile modificare la quantità di larghezza di banda usata dal servizio Backup?<br/>
 Sì, usare l'opzione **Modifica proprietà** nell'agente di Backup per modificare la larghezza di banda. È possibile modificare la quantità di larghezza di banda e gli orari in cui si usa tale larghezza di banda. Per informazioni dettagliate, vedere **[Abilitare la limitazione della larghezza di banda ](backup-configure-vault.md#enable-network-throttling)** nell'articolo [Eseguire il backup di un client o server Windows in Azure usando il modello di distribuzione di Resource Manager.

## <a name="my-internet-bandwidth-is-limited-for-the-amount-of-data-i-need-to-back-up-is-there-a-way-i-can-move-data-to-a-certain-location-with-a-large-network-pipe-and-push-that-data-into-azure-br"></a>La larghezza di banda Internet è limitata per la quantità di dati di cui è necessario eseguire il backup. È possibile spostare i dati in una determinata posizione con una pipe di rete di grandi dimensioni ed effettuare il push di tali dati in Azure? <br/>
È possibile eseguire il backup dei dati in Azure con il processo di backup online standard oppure è possibile usare il servizio Importazione/Esportazione di Azure per trasferire i dati nell'archivio BLOB in Azure. Non c'è altro modo per eseguire il backup nell'archiviazione di Azure. Per informazioni su come usare il servizio Importazione/Esportazione di Azure con Backup di Azure, vedere l'articolo [Flusso di lavoro di Backup offline](backup-azure-backup-import-export.md) .

## <a name="how-many-recoveries-can-i-perform-on-the-data-that-is-backed-up-to-azurebr"></a>Quanti ripristini è possibile eseguire sui dati sottoposti a backup in Azure?<br/>
Non esistono limiti al numero di ripristini da Backup di Azure.

## <a name="do-i-have-to-pay-for-the-egress-traffic-from-azure-data-center-during-recoveriesbr"></a>È necessario pagare per il traffico in uscita dal data center di Azure durante i ripristini?<br/>
 No. I ripristini sono gratuiti e non viene addebitato alcun costo per il traffico in uscita.

## <a name="is-the-data-sent-to-azure-encrypted-br"></a>I dati inviati in Azure sono crittografati? <br/>
Sì. I dati vengono crittografati nel computer server/client/SCDPM locale mediante AES256 e i dati vengono inviati tramite un collegamento HTTPS sicuro.

## <a name="is-the-backup-data-on-azure-encrypted-as-wellbr"></a>Anche i dati di backup in Azure sono crittografati?<br/>
 Sì. I dati inviati ad Azure rimangono crittografati (inattivi). Microsoft non decrittografa mai i dati di backup. Quando si esegue il backup di una macchina virtuale di Azure, Backup di Azure si basa sulla crittografia della macchina virtuale. Ad esempio, se la macchina virtuale è crittografata con Crittografia dischi di Azure , Backup di Azure usa tale crittografia per proteggere i dati.

## <a name="what-is-the-minimum-length-of-encryption-key-used-to-encrypt-backup-data-br"></a>Qual è la lunghezza minima della chiave di crittografia usata per crittografare i dati di backup? <br/>
 La chiave di crittografia deve contenere almeno 16 caratteri.

## <a name="what-happens-if-i-misplace-the-encryption-key-can-i-recover-the-data-or-can-microsoft-recover-the-data-br"></a>Cosa accade se si smarrisce la chiave di crittografia? È possibile ripristinare i dati? Microsoft può recuperarli? <br/>
La chiave usata per crittografare i dati di backup è disponibile solo nelle risorse del cliente. Microsoft non ne conserva una copia in Azure e non dispone dell'accesso alla chiave. Se il cliente smarrisce la chiave, Microsoft non può recuperare i dati di backup.

## <a name="how-do-i-change-the-cache-location-specified-for-the-azure-backup-agentbr"></a>Come si modifica il percorso della cache specificato per l'agente di Backup di Azure?<br/>
 Scorrere in sequenza l'elenco puntato seguente per modificare il percorso della cache.

* Arrestare il motore Backup eseguendo il comando seguente in un prompt dei comandi con privilegi elevati:

  ```PS C:\> Net stop obengine```
* Non spostare i file. Copiare invece la cartella dello spazio della cache in un'altra unità con spazio sufficiente. Lo spazio della cache originale può essere rimosso dopo avere verificato che i backup usino il nuovo spazio della cache.
* Aggiornare le voci del Registro di sistema seguenti con il percorso della nuova cartella dello spazio della cache.<br/>

| Percorso del Registro | Chiave del Registro | Valore |
| --- | --- | --- |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config` |ScratchLocation |*Nuovo percorso della cartella della cache* |
| `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider` |ScratchLocation |*Nuovo percorso della cartella della cache* |

* Riavviare il motore Backup eseguendo il comando seguente in un prompt dei comandi con privilegi elevati:

  ```PS C:\> Net start obengine```

  Una volta completata la creazione del backup nel nuovo percorso della cache, è possibile rimuovere la cartella della cache originale.

## <a name="where-can-i-put-the-cache-folder-for-the-azure-backup-agent-to-work-as-expectedbr"></a>Dove è possibile salvare la cartella della cache perché l'agente di Backup di Azure funzioni come previsto?<br/>
I percorsi seguenti per la cartella della cache non sono consigliati:

* Condivisione di rete o supporti rimovibili: la cartella della cache deve essere locale nel server di cui eseguire il backup con il backup online. I percorsi di rete o i supporti rimovibili, ad esempio le unità USB, non sono supportati.
* Volumi offline: la cartella della cache deve essere online per il backup previsto con l'agente di Backup di Azure.

## <a name="are-there-any-attributes-of-the-cache-folder-that-are-not-supportedbr"></a>Esistono attributi della cartella della cache non supportati?<br/>
 Gli attributi seguenti o le loro combinazioni non sono supportate per la cartella della cache:

* Crittografato
* De-duplicated
* Compresso
* Sparse
* Reparse-Point

La cartella cache e i metadati del disco rigido virtuale non hanno gli attributi necessari per Azure Backup Agent.

## <a name="recovery-services-vaults-are-resource-manager-based-are-backup-vaults-classic-mode-still-supported-br"></a>Gli insiemi di credenziali di Servizi di ripristino sono basati su Resource Manager. Gli insiemi di credenziali di Backup (modalità classica) sono ancora supportati? <br/>
Sì, gli insiemi di credenziali di Backup sono ancora supportati. Creare gli insiemi di credenziali per il backup nel [portale classico](https://manage.windowsazure.com). Creare insiemi di credenziali di Servizi di ripristino nel [portale di Azure](https://portal.azure.com). Tuttavia, è consigliabile creare un insieme di credenziali di Servizi di ripristino perché tutti gli miglioramenti futuri saranno disponibili solo nell'insieme di credenziali di Servizi di ripristino.

## <a name="can-i-migrate-a-backup-vault-to-a-recovery-services-vault-br"></a>È possibile eseguire la migrazione di un insieme di credenziali per il backup in un insieme di credenziali di Servizi di ripristino? <br/>
Purtroppo no, al momento non è possibile eseguire la migrazione del contenuto di un insieme di credenziali di Backup in un insieme di credenziali di Servizi di ripristino. Questa funzionalità verrà presto aggiunta, ma non è attualmente disponibile.

## <a name="do-recovery-services-vaults-support-classic-vms-or-resource-manager-based-vms-br"></a>Gli insiemi di credenziali di Servizi di ripristino supportano le macchine virtuali in modalità classica o quelle basate su Resource Manager? <br/>
Gli insiemi di credenziali di Servizi di ripristino supportano entrambi modelli.  È possibile eseguire il backup di una macchina virtuale creata nel portale classico o di una macchina virtuale di Resource Manager creata nel portale di Azure nell'insieme di credenziali di Servizi di ripristino.

## <a name="i-have-backed-up-my-classic-vms-in-a-backup-vault-can-i-migrate-my-vms-from-classic-mode-to-resource-manager-mode-and-protect-them-in-a-recovery-services-vault"></a>È stato eseguito il backup di macchine virtuali classiche nell'insieme di credenziali per il backup. È possibile eseguire la migrazione delle macchine virtuali dalla modalità classica alla modalità di Resource Manager e proteggerli in un insieme di credenziali dei Servizi di ripristino?
I punti di ripristino delle macchine virtuali classiche in un insieme di credenziali per il backup non eseguono automaticamente la migrazione a un insieme di credenziali dei Servizi di ripristino quando si sposta la macchina virtuale dalla modalità classica alla modalità di Resource Manager. Seguire questa procedura per trasferire i backup di macchine virtuali:

1. Nell'insieme di credenziali per il backup passare alla scheda **Elementi protetti** e selezionare la macchina virtuale. Fare clic su [Arresta protezione](backup-azure-manage-vms-classic.md#stop-protecting-virtual-machines). Lasciare *deselezionata* l'opzione **Elimina i dati di backup associati**.
2. Eseguire la migrazione della macchina virtuale dalla modalità classica alla modalità Resource Manager. Assicurarsi di eseguire la migrazione alla modalità Resource Manager anche della risorsa di archiviazione e della rete corrispondenti alla macchina virtuale.
3. Creare un insieme di credenziali dei Servizi di ripristino e configurare il backup della macchina virtuale migrata usando l'azione **Backup** nella parte superiore del dashboard dell'insieme di credenziali. Per informazioni dettagliate sul backup di una macchina virtuale in un insieme di credenziali dei Servizi di ripristino, vedere l'articolo [Proteggere le VM di Azure con un insieme di credenziali dei servizi di ripristino](backup-azure-vms-first-look-arm.md).



<!--HONumber=Jan17_HO1-->


