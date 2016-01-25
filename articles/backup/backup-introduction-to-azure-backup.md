<properties
	pageTitle="Che cos&#39;è Azure Backup? | Microsoft Azure"
	description="Con Azure Backup e i servizi di ripristino è possibile eseguire il backup e il ripristino di dati e applicazioni da server Windows, computer client Windows, server SCDPM o macchine virtuali di Azure."
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor="tysonn"
	keywords="backup e ripristino; servizi di ripristino"/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016"
	ms.author="trinadhk;jimpark"/>

# Informazioni su Backup di Azure
Backup di Azure è il servizio che consente di eseguire il backup e il ripristino dei dati in Microsoft Cloud. Sostituisce la soluzione di backup locale o esterno esistente con una soluzione basata sul cloud affidabile, sicura e conveniente. Consente anche di proteggere gli asset in esecuzione nel cloud. Backup di Azure fornisce servizi di ripristino basati su un'infrastruttura di altissimo livello che garantisce scalabilità, durabilità e disponibilità elevata.

[Panoramica video di Backup di Azure](https://azure.microsoft.com/documentation/videos/what-is-azure-backup/)

## Perché usare Backup di Azure
Le soluzioni di backup tradizionali si sono evolute e considerano ora un cloud come un endpoint analogo a un disco o a un nastro. Questo approccio, anche se semplice, è limitato e non permette di sfruttare appieno la piattaforma cloud sottostante. Ne risulta una soluzione poco efficiente e costosa. Backup di Azure, invece, offre tutti i vantaggi di una soluzione di backup su cloud efficace e conveniente. Ecco alcuni dei vantaggi principali offerti da Backup di Azure:

| FUNZIONALITÀ | VANTAGGI |
| ------- | ------- |
| Gestione automatica dell'archiviazione | Nessuna spesa di investimento necessaria per dispositivi di archiviazione locale. Backup di Azure alloca e gestisce automaticamente le risorse di archiviazione di backup, con un modello di utilizzo con pagamento in base al consumo. |
| Scalabilità illimitata | Garanzia di disponibilità elevata senza costi di manutenzione e monitoraggio. Backup di Azure sfrutta la potenza e la scalabilità del cloud di Azure sottostante, con funzionalità di scalabilità automatica non intrusive. |
| Più opzioni di archiviazione | È possibile scegliere l'archivio di backup più adatto alle esigenze: <li>Il BLOB in blocchi con archiviazione con ridondanza locale è la soluzione ideale per i clienti attenti ai costi che devono proteggere i dati da errori hardware locali. <li>Il BLOB in blocchi con archiviazione con ridondanza geografica fornisce 3 copie aggiuntive in un data center abbinato, garantendo così l'elevata disponibilità dei dati di backup anche in caso di emergenza a livello di sito Azure. |
| Trasferimento dati illimitato | Nessun addebito per il trasferimento dati in uscita durante le operazioni di ripristino dall'insieme di credenziali di Backup di Azure. Non sono previsti addebiti neanche per i dati in ingresso in Azure. |
| Gestione centralizzata | Semplicità e familiarità del portale di Azure. Con l'evoluzione del servizio, funzionalità come la gestione centrale consentiranno di gestire l'infrastruttura di backup da un'unica posizione. |
| Crittografia dei dati | Trasmissione e archiviazione protette dei dati del cliente nel cloud pubblico. La passphrase di crittografia viene archiviata nell'origine e non viene mai trasmessa o archiviata in Azure. La chiave di crittografia è necessaria per ripristinare i dati e solo il cliente dispone dell'accesso completo ai dati nel servizio. |  
| Integrazione con VSS | I backup coerenti con l'applicazione in Windows eliminano la necessità di correzioni al momento del ripristino. Questo riduce l'RTO e consente ai clienti di raggiungere lo stato di esecuzione più rapidamente. |
| Conservazione a lungo termine | Anziché pagare per soluzioni di backup esterne su nastro, i clienti possono eseguire il backup in Azure, che offre una soluzione interessante con semantica simile a nastro a un costo molto ridotto. |

## Componenti di Backup di Azure
Backup di Azure è una soluzione di backup ibrida, costituita da più componenti che interagiscono per consentire flussi di lavoro end-to-end di backup e ripristino.

![Componenti di Backup di Azure](./media/backup-introduction-to-azure-backup/azure-backup-overview.png)

## Scenari di distribuzione

| Componente | Può essere distribuito in Azure? | Può essere distribuito in locale? | Archiviazione di destinazione supportata|
| --- | --- | --- | --- |
| Azure Backup Agent | <p>\*\*Sì\*\*</p> <p>Azure Backup Agent può essere distribuito in qualsiasi macchina virtuale Windows Server in esecuzione in Azure.</p> | <p>\*\*Sì\*\*</p> <p>Azure Backup Agent può essere distribuito in qualsiasi computer fisico o macchina virtuale Windows Server.</p> | Insieme di credenziali di Backup di Azure |
| System Center Data Protection Manager \(SCDPM\) | <p>\*\*Sì\*\*</p> <p>Altre informazioni sulla [protezione dei carichi di lavoro in Azure con SCDPM](http://blogs.technet.com/b/dpm/archive/2014/09/02/azure-iaas-workload-protection-using-data-protection-manager.aspx).</p> | <p>\*\*Sì\*\*</p> <p>Altre informazioni sulla [protezione dei carichi di lavoro e delle macchine virtuali nel data center](https://technet.microsoft.com/library/hh758173.aspx). | Disco collegato al computer locale</p> <p>Insieme di credenziali di Backup di Azure</p> <p>Nastro \(solo in locale\)</p> |
| Server di backup di Azure | <p>\*\*Sì\*\*</p> <p>Altre informazioni sulla [protezione dei carichi di lavoro in Azure con il server di Backup di Azure](backup-azure-microsoft-azure-backup.md).</p> | <p>\*\*Sì\*\*</p> <p>Altre informazioni sulla [protezione dei carichi di lavoro in Azure con il server di Backup di Azure](backup-azure-microsoft-azure-backup.md).</p> | Insieme di credenziali di Backup di Azure |
| Backup di Azure \(estensione VM\) | <p>Sì</p> <p>Specializzato per il [backup di macchine virtuali IaaS di Azure](backup-azure-vms-introduction.md).</p> | <p>\*\*No\*\*</p> <p>Usare SCDPM per il backup delle macchine virtuali nel data center. | Insieme di credenziali di Backup di Azure</p> |

## Applicazioni e carichi di lavoro

| Carico di lavoro | Computer di origine | Soluzione di Backup di Azure |
| --- | --- |---|
| File e cartelle | Windows Server | <p>[Azure Backup Agent](backup-configure-vault.md)</p> <p>[System Center DPM](backup-azure-dpm-introduction.md)</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
| File e cartelle | Client Windows | <p>[Azure Backup Agent](backup-configure-vault.md)</p> <p>[System Center DPM](backup-azure-dpm-introduction.md)</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Macchina virtuale Hyper-V \(Windows\) | Windows Server | <p>System Center DPM</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Macchina virtuale Hyper-V \(Linux\) | Windows Server | <p>System Center DPM</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SQL Server | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md)</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft SharePoint | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md)</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
| Microsoft Exchange | Windows Server | <p>[System Center DPM](backup-azure-backup-sql.md)</p> <p>[Server di Backup di Azure](backup-azure-microsoft-azure-backup.md)</p> |
\| Macchine virtuali IaaS di Azure \(Windows\)\| - \| [Backup di Azure \(estensione VM\)](backup-azure-vms-introduction.md) \| \| Macchine virtuali IaaS di Azure \(Linux\) \| - \| [Backup di Azure \(estensione VM\)](backup-azure-vms-introduction.md) \|

## Funzionalità
Le tabelle riportate di seguito forniscono un riepilogo di come vengono gestite le funzionalità di Backup di Azure in ogni componente:

### Archiviazione

| Funzionalità | Agente di Backup di Azure | SCDPM | Server di backup di Azure | Backup di Azure \(estensione VM\) |
| ------- | --- | --- | --- | ---- |
| Insieme di credenziali di Backup di Azure | ![Sì][green] | ![Sì][green] | ![Sì][green] | ![Sì][green] |
| Archiviazione su disco | | ![Sì][green] | ![Sì][green] | |
| Archiviazione su nastro | | ![Sì][green] | | |
| Compressione \(nell'insieme di credenziali per il backup\) | ![Sì][green] | ![Sì][green]| ![Sì][green] | |
| Backup incrementale | ![Sì][green] | ![Sì][green] | ![Sì][green] | ![Sì][green] |
| Deduplicazione dei dischi | | ![Parzialmente][yellow] | ![Parzialmente][yellow]| | |

L'insieme di credenziali di Backup di Azure è la destinazione di archiviazione preferita in tutti i componenti. Il server di Backup di Azure e SCDPM permettono di avere anche una copia del disco locale, ma solo SCDPM offre la possibilità di scrivere dati in un dispositivo di archiviazione su nastro.

#### Backup incrementale
Indipendentemente dalla risorsa di archiviazione di destinazione, che sia un disco, un nastro o un insieme di credenziali di backup, ogni componente supporta i backup incrementali. Questo garantisce velocità ed efficienza nell'archiviazione dei backup grazie all'uso delle sole modifiche incrementali dall'ultimo backup, che vengono trasferite nell'archivio di destinazione. I backup vengono anche compressi per ridurre il volume di archiviazione.

>[AZURE.NOTE]L'estensione della macchina virtuale è il componente che non esegue alcuna compressione. Tutti i dati di backup vengono copiati dall'account di archiviazione del cliente all'insieme di credenziali di backup nella stessa area, senza compressione. L'archiviazione dei dati senza compressione comporta un leggero aumento dello spazio di archiviazione utilizzato, ma consente di abbreviare i tempi di ripristino.

#### Deduplicazione
La deduplicazione è supportata per SCDPM e per il server di Backup di Azure quando sono [distribuiti all'interno di una macchina virtuale Hyper-V](http://blogs.technet.com/b/dpm/archive/2015/01/06/deduplication-of-dpm-storage-reduce-dpm-storage-consumption.aspx). La deduplicazione viene eseguita a livello di host sfruttando la funzionalità di deduplicazione di Windows Server, nei dischi rigidi virtuali collegati come risorsa di archiviazione di backup alla macchina virtuale.

>[AZURE.WARNING]La deduplicazione non è disponibile in Azure per i componenti di Backup di Azure. Quando SCDPM e il server di Backup di Azure vengono distribuiti in Azure, i dischi di archiviazione collegati alla macchina virtuale non possono essere deduplicati.

### Sicurezza

| Funzionalità | Agente di Backup di Azure | SCDPM | Server di backup di Azure | Backup di Azure \(estensione VM\) |
| ------- | --- | --- | --- | ---- |
| Sicurezza di rete \(in Azure\) | ![Sì][green] |![Sì][green] | ![Sì][green] | ![Parzialmente][yellow]|
| Sicurezza dei dati \(in Azure\) | ![Sì][green] |![Sì][green] | ![Sì][green] | ![Parzialmente][yellow]|

Tutto il traffico di backup dai server all'insieme di credenziali di Backup di Azure viene crittografato con AES256 e i dati vengono inviati tramite una connessione HTTPS protetta. I dati di backup vengono archiviati nell'insieme di credenziali di backup anche in formato crittografato. Solo il cliente ha la passphrase per sbloccare i dati. Microsoft non può decrittografare i dati di backup.

>[AZURE.WARNING]La chiave usata per crittografare i dati di backup è unicamente a disposizione del cliente. Microsoft non ne conserva una copia in Azure e non dispone dell'accesso alla chiave. Se la chiave viene smarrita, Microsoft non può recuperare i dati di backup.

Per il backup di macchine virtuali di Azure, è necessario impostare in modo esplicito la crittografia *all'interno* della macchina virtuale. Usare BitLocker nelle macchine virtuali Windows e dm-crypt nelle macchine virtuali Linux. Backup di Azure non esegue automaticamente la crittografia dei dati di backup provenienti da questo percorso.

### Carichi di lavoro supportati

| Funzionalità | Agente di Backup di Azure | SCDPM | Server di backup di Azure | Backup di Azure \(estensione VM\) |
| ------- | --- | --- | --- | ---- |
| Computer Windows Server \(file/cartelle\) | ![Sì][green] | ![Sì][green] | ![Sì][green] | |
| Computer client Windows \(file/cartelle\) | ![Sì][green] | ![Sì][green] | ![Sì][green] | |
| Macchina virtuale Hyper-V \(Windows\) | | ![Sì][green] | ![Sì][green] | |
| Macchina virtuale Hyper-V \(Linux\) | | ![Sì][green] | ![Sì][green] | |
| Microsoft SQL Server | | ![Sì][green] | ![Sì][green] | |
| Microsoft SharePoint | | ![Sì][green] | ![Sì][green] | |
| Microsoft Exchange | | ![Sì][green] | ![Sì][green] | |
| Macchina virtuale di Azure \(Windows\) | | | | ![Sì][green] |
| Macchina virtuale di Azure \(Linux\) | | | | ![Sì][green] |

### Rete

| Funzionalità | Agente di Backup di Azure | SCDPM | Server di backup di Azure | Backup di Azure \(estensione VM\) |
| ------- | --- | --- | --- | ---- |
| Compressione di rete \(al server di backup\) | | ![Sì][green] | ![Sì][green] | |
| Compressione di rete \(all'insieme di credenziali di backup\) | ![Sì][green] | ![Sì][green] | ![Sì][green] | |
| Protocollo di rete \(al server di backup\) | | TCP | TCP | |
| Protocollo di rete \(all'insieme di credenziali di backup\) | HTTPS | HTTPS | HTTPS | HTTPS |

Poiché l'estensione della macchina virtuale legge i dati direttamente dall'account di archiviazione di Azure attraverso la rete di archiviazione, ottimizzare il traffico non è necessario. Il traffico attraversa la rete di archiviazione locale nel data center di Azure e la larghezza di banda non richiede una particolare compressione.

Per i clienti che proteggono i dati in un server di backup \(SCDPM o server di Backup di Azure\), è possibile comprimere il traffico dal server primario al server di backup per ridurre l'utilizzo della larghezza di banda.

### Backup e conservazione

| Frequenza di backup \(nell'insieme di credenziali di Azure\) | 3 backup al giorno | 2 backup al giorno | 1 backup al giorno |
| --- | --- | --- | --- |
| Frequenza di backup \(su disco\) | Non applicabile | <p>Ogni 15 minuti per SQL Server</p> <p>Ogni 1 ora per altri carichi di lavoro</p> | Non applicabile |
| Opzioni di conservazione | Giornaliera, settimanale, mensile, annuale | Giornaliera, settimanale, mensile, annuale | Giornaliera, settimanale, mensile, annuale |
| Periodo di conservazione | Fino a 99 anni | Fino a 99 anni | Fino a 99 anni |
| Punti di ripristino nell'insieme di credenziali di Azure | senza limiti | senza limiti | senza limiti |
| Punti di ripristino nel disco locale | Non applicabile | Non applicabile | Non applicabile |
| Punti di ripristino su nastro | Non applicabile | Non applicabile | Non applicabile |

## Differenze tra Backup di Azure e Azure Site Recovery
Molti clienti fanno confusione tra backup e ripristino di emergenza. Entrambi acquisiscono dati e forniscono una semantica di ripristino, ma la proposta di valore alla base dei due servizi è diversa.

Backup di Azure esegue il backup dei dati in locale o nel cloud. Azure Site Recovery coordina la replica, il failover e il failback di macchine virtuali e server fisici. Entrambi i servizi sono necessari per una soluzione di ripristino di emergenza completa, perché la strategia di ripristino di emergenza deve fare in modo che i dati siano protetti e ripristinabili \(Backup di Azure\) E deve mantenere i carichi di lavoro disponibili e accessibili \(Azure Site Recovery\) quando si verificano interruzioni del servizio.

Per prendere decisioni relative al backup e al ripristino di emergenza, è necessario avere presenti alcuni concetti importanti:

| CONCETTO | DETTAGLI | BACKUP | RIPRISTINO DI EMERGENZA |
| ------- | ------- | ------ | ----------------- |
| Obiettivo del punto di ripristino \(RPO\) | Quantità di dati persi accettabile nel caso in cui si renda necessario un ripristino. | Le soluzioni di backup variano notevolmente in termini di RPO accettabile. I backup di macchine virtuali hanno in genere un RPO di 1 giorno, mentre i backup di database hanno RPO minimi fino a 15 minuti. | Le soluzioni di ripristino di emergenza hanno RPO estremamente bassi. La copia di ripristino di emergenza può risalire a pochi minuti o pochi secondi prima. |
| Obiettivo del tempo di ripristino \(RTO\) | Quantità di tempo necessario per completare un ripristino. | Dato l'RPO più alto, la quantità di dati che deve essere elaborata dalla soluzione di backup è in genere molto più grande e questo comporta un RTO maggiore. Ad esempio, il ripristino dei dati da nastri può richiedere giorni, a seconda del tempo impiegato per trasportare il nastro da una posizione esterna. | Le soluzioni di ripristino di emergenza hanno RTO più bassi, perché il livello di sincronizzazione con l'origine è maggiore e il numero di modifiche da elaborare è ridotto. |
| Conservazione | Periodo di tempo per il quale i dati devono essere archiviati. | <p>Per scenari che richiedono il ripristino operativo \(danneggiamento dei dati, eliminazione accidentale dei file, errori del sistema operativo\), i dati di backup sono in genere conservati per un massimo di 30 giorni.</p> <p>Dal punto di vista della conformità, potrebbe essere necessario conservare i dati per mesi o anche anni. Per questo tipo di esigenza i dati di backup sono particolarmente adatti.</p> | Il ripristino di emergenza richiede soltanto dati di ripristino operativo. I tempi sono in genere poche ore o fino a un giorno. A causa degli intervalli di acquisizione dei dati così ridotti nelle soluzioni di ripristino di emergenza, la conservazione a lungo termine non è consigliabile. |

## Passaggi successivi

- [Valutazione di Backup di Azure](backup-try-azure-backup-in-10-mins.md)
- Le domande frequenti sul servizio di Backup di Azure sono disponibili [in questa pagina](backup-azure-backup-faq.md).
- Visitare il [Forum su Backup di Azure](http://go.microsoft.com/fwlink/p/?LinkId=290933).


[green]: ./media/backup-introduction-to-azure-backup/green.png
[yellow]: ./media/backup-introduction-to-azure-backup/yellow.png
[red]: ./media/backup-introduction-to-azure-backup/red.png

<!---HONumber=AcomDC_0114_2016-->