<properties
	pageTitle="Che cos'è Azure Backup? | Microsoft Azure"
	description="Con Azure Backup e i servizi di ripristino è possibile eseguire il backup e il ripristino di dati e applicazioni da server Windows, computer client Windows, server System Center DPM e macchine virtuali di Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"
	keywords="backup e ripristino; servizi di ripristino; soluzioni di backup"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="trinadhk;jimpark"/>

# Informazioni su Backup di Azure
Backup di Azure è il servizio usato per eseguire il backup e il ripristino dei dati in Microsoft Cloud. Sostituisce la soluzione di backup locale o esterna esistente con una soluzione basata sul cloud affidabile, sicura e conveniente. Consente anche di proteggere gli asset eseguiti nel cloud. Backup di Azure fornisce servizi di ripristino basati su un'infrastruttura di altissimo livello che garantisce scalabilità, durabilità e disponibilità elevata.

[Panoramica video di Backup di Azure](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## Perché usare Backup di Azure
Le soluzioni di backup tradizionali si sono evolute e considerano ora il cloud come un endpoint analogo a un disco o un nastro. Questo approccio, anche se semplice, è limitato e non consente di sfruttare appieno la piattaforma cloud sottostante. Ne risulta una soluzione poco efficiente e costosa. Backup di Azure, invece, offre tutti i vantaggi di una soluzione di backup nel cloud efficace e conveniente. Ecco alcuni dei vantaggi principali offerti da Backup di Azure:

| Funzionalità | Vantaggi |
| ------- | ------- |
| Gestione automatica dell'archiviazione | Nessuna spesa di investimento necessaria per dispositivi di archiviazione locale. Backup di Azure alloca e gestisce automaticamente le risorse di archiviazione di backup e usa un modello di utilizzo con pagamento in base al consumo. |
| Scalabilità illimitata | Consente di sfruttare le garanzie di disponibilità elevata senza costi di manutenzione e monitoraggio. Backup di Azure usa la potenza e la scalabilità sottostanti del cloud di Azure, con funzionalità di scalabilità automatica non intrusive. |
| Più opzioni di archiviazione | È possibile scegliere l'archivio di backup più adatto alle esigenze: <li>un BLOB in blocchi di archiviazione con ridondanza locale è la soluzione ideale per i clienti attenti ai costi, senza rinunciare alla protezione dei dati da errori hardware locali. <li>Un BLOB in blocchi di archiviazione con replica geografica offre tre copie aggiuntive in un data center abbinato. Questo assicura la disponibilità elevata dei dati di backup anche nel caso di un'emergenza a livello di sito di Azure. |
| Trasferimento dati illimitato | Non è previsto alcun addebito per il trasferimento dati in uscita durante un'operazione di ripristino dall'insieme di credenziali di Backup. Non sono previsti addebiti neanche per i dati in ingresso in Azure. |
| Gestione centralizzata | Il portale di Azure offre semplicità e familiarità. Con l'evoluzione del servizio, funzionalità come la gestione centrale consentiranno di gestire l'infrastruttura di backup da un'unica posizione. |
| Crittografia dei dati | Consente la trasmissione e l'archiviazione protette dei dati del cliente nel cloud pubblico. La passphrase di crittografia viene archiviata nell'origine e non viene mai trasmessa o archiviata in Azure. La chiave di crittografia è necessaria per ripristinare i dati e solo per il cliente è disponibile l'accesso completo ai dati nel servizio. |  
| Backup coerente con l'applicazione | I backup coerenti con l'applicazione in Windows eliminano la necessità di correzioni al momento del ripristino. Questo riduce l'obiettivo del tempo di ripristino (RTO) e consente ai clienti di ripristinare più rapidamente uno stato di esecuzione. |
| Conservazione a lungo termine | Invece di pagare per soluzioni di backup esterne su nastro, i clienti possono eseguire il backup in Azure, che offre una soluzione interessante con semantica simile al nastro a costi molto ridotti. |

## Componenti di Backup di Azure
Essendo una soluzione di backup ibrida, Backup di Azure è costituita da più componenti che interagiscono per abilitare flussi di lavoro di backup e ripristino end-to-end.

![Componenti di Backup di Azure](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

## Scenari di distribuzione

| Componente | Può essere distribuito in Azure? | Può essere distribuito in locale? | Archiviazione di destinazione supportata|
| --- | --- | --- | --- |
| Agente di Backup di Azure | <p>**Sì**</p> <p>Azure Backup Agent può essere distribuito in qualsiasi macchina virtuale di Windows Server eseguita in Azure.</p> | <p>**Sì**</p> <p>Backup Agent può essere distribuito in qualsiasi computer fisico o macchina virtuale di Windows Server.</p> | <p>Insieme di credenziali di Backup di Azure</p> |
| System Center Data Protection Manager (DPM) | <p>**Sì**</p> <p>Altre informazioni su [come proteggere i carichi di lavoro in Azure con System Center DPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx).</p> | <p>**Sì**</p> <p>Altre informazioni su [come proteggere i carichi di lavoro e le macchine virtuali nel data center](https://technet.microsoft.com/library/hh758173.aspx).</p> | <p>Disco collegato al computer locale,</p> <p> insieme di credenziali di Backup di Azure,</p> <p> nastro (solo in locale)</p> |
| Server di backup di Azure | <p>**Sì**</p> <p>Altre informazioni su [come proteggere i carichi di lavoro in Azure con il server di Backup di Azure](backup-azure-microsoft-azure-backup.md).</p> | <p>**Sì**</p> <p>Altre informazioni su [come proteggere i carichi di lavoro in Azure con il server di Backup di Azure](backup-azure-microsoft-azure-backup.md).</p> | <p>Insieme di credenziali di Backup di Azure</p> |
| Backup di Azure (estensione VM) | <p>Sì</p> <p>Specializzato per il [backup di macchine virtuali di infrastruttura distribuita come servizio (IaaS) di Azure](backup-azure-vms-introduction.md).</p> | <p>**No**</p> <p>Usare System Center DPM per il backup delle macchine virtuali nel data center.</p> | <p>Insieme di credenziali di Backup di Azure</p> |

## Applicazioni e carichi di lavoro di cui è possibile eseguire il backup

| Carico di lavoro | Computer di origine | Soluzione di Backup di Azure |
| --- | --- |---|
| File e cartelle | Windows Server | <p>[Azure Backup Agent](backup-configure-vault.md)</p> <p>[System Center DPM](backup-azure-dpm-introduction.md)</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
| File e cartelle | Client Windows | <p>[Azure Backup Agent](backup-configure-vault.md)</p> <p>[System Center DPM](backup-azure-dpm-introduction.md)</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Macchina virtuale Hyper-V (Windows) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Macchina virtuale Hyper-V (Linux) | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md)</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md)</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md)</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md),</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Macchine virtuali IaaS di Azure (Windows)| - | [Backup di Azure (estensione VM)](backup-azure-vms-introduction.md) |
| Macchine virtuali IaaS di Azure (Linux) | - | [Backup di Azure (estensione VM)](backup-azure-vms-introduction.md) |

## Funzionalità
Le cinque tabelle seguenti forniscono un riepilogo di come viene gestita la funzionalità di Backup in ogni componente:

### Archiviazione

| Funzionalità | Agente di Backup di Azure | System Center DPM | Server di backup di Azure | Backup di Azure (estensione VM) |
| ------- | --- | --- | --- | ---- |
| Insieme di credenziali di Backup di Azure | ![Sì][green] | ![Sì][green] | ![Sì][green] | ![Sì][green] |
| Archiviazione su disco | | ![Sì][green] | ![Sì][green] | |
| Archiviazione su nastro | | ![Sì][green] | | |
| Compressione (nell'insieme di credenziali per il backup) | ![Sì][green] | ![Sì][green]| ![Sì][green] | |
| Backup incrementale | ![Sì][green] | ![Sì][green] | ![Sì][green] | ![Sì][green] |
| Deduplicazione dei dischi | | ![Parzialmente][yellow] | ![Parzialmente][yellow]| | |

**Chiave** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Sì][green]= Supportata &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ![Parzialmente][yellow]= Parzialmente supportata &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *&lt;vuoto&gt;* = Non supportata

L'insieme di credenziali di Backup è la destinazione di archiviazione preferita in tutti i componenti. System Center DPM e il server di Backup di Azure consentono anche di avere anche una copia del disco locale, ma solo System Center DPM offre la possibilità di scrivere dati su un dispositivo di archiviazione su nastro.

#### Backup incrementale
Indipendentemente dalla risorsa di archiviazione di destinazione, che sia un disco, un nastro o un insieme di credenziali di Backup, ogni componente supporta il backup incrementale. Questo garantisce velocità ed efficienza nell'archiviazione dei backup grazie all'uso delle sole modifiche incrementali dall'ultimo backup, che vengono quindi trasferite nell'archivio di destinazione. I backup vengono anche compressi per ridurre lo spazio di archiviazione.

L'estensione della macchina virtuale è il componente che non esegue alcuna compressione. Tutti i dati di backup vengono copiati dall'account di archiviazione del cliente all'insieme di credenziali di Backup nella stessa area, senza compressione. L'archiviazione dei dati senza compressione comporta un leggero aumento dello spazio di archiviazione utilizzato, ma consente di abbreviare i tempi di ripristino.

#### Deduplicazione
La deduplicazione è supportata per System Center DPM e per il server di Backup quando sono [distribuiti in una macchina virtuale Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). La deduplicazione viene eseguita a livello di host sfruttando la funzionalità di deduplicazione di Windows Server nei dischi rigidi virtuali (VHD) collegati alla macchina virtuale come risorsa di archiviazione di backup.

>[AZURE.WARNING] La deduplicazione non è disponibile in Azure per i componenti di Backup. Quando System Center DPM e il server di Backup vengono distribuiti in Azure, i dischi di archiviazione collegati alla macchina virtuale non possono essere deduplicati.

### Sicurezza

| Funzionalità | Agente di Backup di Azure | System Center DPM | Server di backup di Azure | Backup di Azure (estensione VM) |
| ------- | --- | --- | --- | ---- |
| Sicurezza di rete (in Azure) | ![Sì][green] |![Sì][green] | ![Sì][green] | ![Parzialmente][yellow]|
| Sicurezza dei dati (in Azure) | ![Sì][green] |![Sì][green] | ![Sì][green] | ![Parzialmente][yellow]|

**Chiave** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Sì][green]= Supportata &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; ![Parzialmente][yellow]= Parzialmente supportata &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *&lt;vuoto&gt;* = Non supportata

Tutto il traffico di backup dai server all'insieme di credenziali di Backup viene crittografato con Advanced Encryption Standard 256. I dati vengono inviati tramite un collegamento HTTPS protetto. I dati di backup vengono archiviati anche nell'insieme di credenziali di Backup in formato crittografato. Solo il cliente ha la passphrase per sbloccare i dati. Microsoft non può decrittografare i dati di backup

>[AZURE.WARNING] La chiave usata per crittografare i dati di backup è unicamente a disposizione del cliente. Microsoft non ne conserva una copia in Azure e non dispone dell'accesso alla chiave. Se la chiave viene smarrita, Microsoft non può recuperare i dati di backup.

Per il backup di macchine virtuali di Azure è necessario impostare in modo esplicito la crittografia *all'interno* della macchina virtuale. Usare BitLocker nelle macchine virtuali di Windows e **dm-crypt** nelle macchine virtuali di Linux. Backup di Azure non esegue automaticamente la crittografia dei dati di backup provenienti da questo percorso.

### Carichi di lavoro supportati

| Funzionalità | Agente di Backup di Azure | System Center DPM | Server di backup di Azure | Backup di Azure (estensione VM) |
| ------- | --- | --- | --- | ---- |
| Computer Windows Server: file e cartelle | ![Sì][green] | ![Sì][green] | ![Sì][green] | |
| Computer client di Windows: file e cartelle | ![Sì][green] | ![Sì][green] | ![Sì][green] | |
| Macchina virtuale Hyper-V (Windows) | | ![Sì][green] | ![Sì][green] | |
| Macchina virtuale Hyper-V (Linux) | | ![Sì][green] | ![Sì][green] | |
| Microsoft SQL Server | | ![Sì][green] | ![Sì][green] | |
| Microsoft SharePoint | | ![Sì][green] | ![Sì][green] | |
| Microsoft Exchange | | ![Sì][green] | ![Sì][green] | |
| Macchina virtuale di Azure (Windows) | | | | ![Sì][green] |
| Macchina virtuale di Azure (Linux) | | | | ![Sì][green] |

**Chiave** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Sì][green]= Supportata &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *&lt;vuoto&gt;* = Non supportata

### Rete

| Funzionalità | Agente di Backup di Azure | System Center DPM | Server di backup di Azure | Backup di Azure (estensione VM) |
| ------- | --- | --- | --- | ---- |
| Compressione di rete (al server di Backup) | | ![Sì][green] | ![Sì][green] | |
| Compressione di rete (all'insieme di credenziali di Backup) | ![Sì][green] | ![Sì][green] | ![Sì][green] | |
| Protocollo di rete (al server di Backup) | | TCP | TCP | |
| Protocollo di rete (all'insieme di credenziali di Backup) | HTTPS | HTTPS | HTTPS | HTTPS |

**Chiave** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Sì][green]= Supportata &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; *&lt;vuoto&gt;* = Non supportata

Poiché l'estensione della macchina virtuale legge i dati direttamente dall'account di archiviazione di Azure attraverso la rete di archiviazione, non è necessario ottimizzare il traffico. Il traffico attraversa la rete di archiviazione locale nel data center di Azure, quindi considerata la larghezza di banda non c'è una particolare esigenza di compressione.

Per i clienti che proteggono i dati in un server di backup, come System Center DPM o il server di Backup, è possibile comprimere il traffico dal server primario al server di backup per ridurre l'uso della larghezza di banda.

### Backup e conservazione

| | Agente di Backup di Azure | System Center DPM e server di Backup di Azure | Backup di Azure (estensione VM) |
| --- | --- | --- | --- |
| Frequenza di backup (all'insieme di credenziali di Backup) | 3 backup al giorno | 2 backup al giorno | 1 backup al giorno |
| Frequenza di backup (su disco) | Non applicabile | <p>Ogni 15 minuti per SQL Server</p> <p>Ogni ora per altri carichi di lavoro</p> | Non applicabile |
| Opzioni di conservazione | Giornaliera, settimanale, mensile, annuale | Giornaliera, settimanale, mensile, annuale | Giornaliera, settimanale, mensile, annuale |
| Periodo di conservazione | Fino a 99 anni | Fino a 99 anni | Fino a 99 anni |
| Punti di ripristino nell'insieme di credenziali di Backup | Senza limiti | Senza limiti | Senza limiti |
| Punti di ripristino nel disco locale | Non applicabile | Non applicabile | Non applicabile |
| Punti di ripristino su nastro | Non applicabile | Non applicabile | Non applicabile |

## Informazioni sul file delle credenziali di insieme

Il file delle credenziali di insieme è un certificato generato dal portale per ogni insieme di credenziali per il backup. Il portale carica quindi la chiave pubblica nel Servizio di controllo di accesso (o ACS). La chiave privata del certificato viene resa disponibile per l'utente come parte del flusso di lavoro indicato come input nel flusso di lavoro di registrazione del computer. In questo modo viene eseguita l'autenticazione del computer per l'invio dei dati di backup a un insieme di credenziali identificato nel servizio Backup di Azure.

Il file delle credenziali di insieme viene usato solo durante il flusso di lavoro di registrazione. È responsabilità dell'utente garantire che il file delle credenziali di insieme non venga danneggiato. Nelle mani di un utente non autorizzato il file delle credenziali di insieme può essere usato per registrare altri computer nello stesso insieme di credenziali. Poiché i dati di backup sono crittografati tramite una passphrase appartenente al cliente, i dati di backup esistenti non possono tuttavia essere compromessi. Per attenuare questo problema, le credenziali dell'insieme di credenziali sono impostate per scadere in 48hrs. È possibile scaricare le credenziali di insieme di un insieme di credenziali per il backup un numero illimitato di volte, ma durante la registrazione del flusso di lavoro è applicabile solo il file delle credenziali di insieme più recente.

## Differenze tra Backup e Azure Site Recovery
Molti clienti fanno confusione tra ripristino del backup e ripristino di emergenza. Entrambi acquisiscono dati e forniscono una semantica di ripristino, ma la proposta di valore alla base è diversa.

Backup di Azure esegue il backup dei dati in locale o nel cloud. Azure Site Recovery coordina la replica, il failover e il failback di macchine virtuali e server fisici. Sono necessari entrambi per una soluzione di ripristino di emergenza completa. La strategia di ripristino di emergenza deve fare in modo che i dati siano protetti e ripristinabili (Backup) *e* deve mantenere i carichi di lavoro disponibili e accessibili (Site Recovery) quando si verificano interruzioni.

Per prendere decisioni relative al backup e al ripristino di emergenza, è necessario considerare i concetti importanti seguenti:

| Concetto | Dettagli | Backup | Ripristino di emergenza |
| ------- | ------- | ------ | ----------------- |
| Obiettivo del punto di ripristino (RPO) | Quantità di dati persi accettabile nel caso in cui si renda necessario un ripristino. | Le soluzioni di backup presentano un'ampia variabilità a livello di RPO accettabile. I backup delle macchine virtuali hanno in genere un RPO di un giorno, mentre i backup dei database hanno RPO fino a un minimo di 15 minuti. | Le soluzioni di ripristino di emergenza hanno RPO estremamente bassi. La copia di ripristino di emergenza può risalire a pochi minuti o pochi secondi prima. |
| Obiettivo del tempo di ripristino (RTO) | Quantità di tempo necessario per completare un ripristino o un recupero. | Dato l'RPO più alto, la quantità di dati che deve essere elaborata dalla soluzione di backup è in genere molto più grande e questo comporta un RTO maggiore. Ad esempio, il ripristino dei dati dai nastri può richiedere giorni, a seconda del tempo richiesto per il trasporto del nastro da una posizione esterna. | Le soluzioni di ripristino di emergenza hanno RTO più bassi, perché il livello di sincronizzazione con l'origine è maggiore e il numero di modifiche da elaborare è ridotto. |
| Conservazione | Periodo di tempo per il quale i dati devono essere archiviati. | <p>Per scenari che richiedono il ripristino operativo, per danneggiamento dei dati, eliminazione accidentale dei file o errori del sistema operativo, i dati di backup sono in genere conservati per un massimo di 30 giorni.</p> <p>Dal punto di vista della conformità, potrebbe essere necessario conservare i dati per mesi o anche anni. In questi casi, i dati di backup sono particolarmente adatti per l'archiviazione.</p> | Il ripristino di emergenza richiede solo dati di ripristino operativo. Questo richiede in genere poche ore o fino a un giorno. A causa degli intervalli di acquisizione dei dati così ridotti nelle soluzioni di ripristino di emergenza, l'uso di questi dati per la conservazione a lungo termine non è consigliabile. |


## Passaggi successivi

- [Valutazione di Backup di Azure](backup-try-azure-backup-in-10-mins.md)
- [Servizio di backup di Azure - Domande frequenti](backup-azure-backup-faq.md)
- Visitare il [forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).


[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!---HONumber=AcomDC_0211_2016-->