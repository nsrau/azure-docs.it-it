<properties
	pageTitle="Pianificare l'infrastruttura di backup delle macchine virtuali in Azure | Microsoft Azure"
	description="Considerazioni importanti per la pianificazione dell'infrastruttura di backup delle macchine virtuali in Azure"
	services="backup"
	documentationCenter=""
	authors="Jim-Parker"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="backup"
	ms.workload="storage-backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/29/2015"
	ms.author="trinadhk; aashishr; jimpark; markgal"/>

# Pianificare l'infrastruttura di backup delle macchine virtuali in Azure
Questo articolo illustra le considerazioni principali da tenere in considerazione durante la pianificazione dell'infrastruttura di backup delle macchine virtuali. Se è stato [preparato l'ambiente](backup-azure-vms-prepare.md), questo è il passaggio successivo prima di iniziare a [eseguire il backup delle macchine virtuali](backup-azure-vms.md). Per altre informazioni sulle macchine virtuali di Azure, vedere [Macchine virtuali - Documentazione](https://azure.microsoft.com/documentation/services/virtual-machines/).

## In che modo Azure esegue il backup delle macchine virtuali?
Quando il servizio Backup di Azure avvia un processo di backup all'ora pianificata, richiede all'estensione per il backup di acquisire uno snapshot temporizzato. Questo snapshot viene acquisito in combinazione con il Servizio Copia Shadow del volume per ottenere uno snapshot coerente dei dischi nella macchina virtuale senza che sia necessario spegnerla.

Dopo l'acquisizione dello snapshot, il servizio Backup di Azure trasferisce i dati nell'insieme di credenziali per il backup. Per aumentare l'efficienza del processo di backup, il servizio identifica e trasferisce solo i blocchi di dati che sono stati modificati dall'ultimo backup.

![Architettura del backup delle macchine virtuali di Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino.

### Coerenza dei dati
Le operazioni di backup e ripristino dei dati aziendali critici sono ulteriormente complicate dal fatto che è necessario eseguire il backup mentre le applicazioni che generano i dati sono in esecuzione. Per risolvere il problema, il servizio Backup di Azure fornisce funzionalità di backup coerenti con l'applicazione per i carichi di lavoro Microsoft avvalendosi del Servizio Copia Shadow del volume per garantire che la scrittura dei dati nell'archiviazione venga effettuata correttamente.

>[AZURE.NOTE]Per le macchine virtuali Linux, è disponibile solo il backup coerente con i file, perché Linux non ha una piattaforma equivalente al Servizio Copia Shadow del volume.

Il backup di Azure richiede il backup VSS completo sulle macchine virtuali di Windows. Altre informazioni [backup VSS completo](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx). Per abilitare i backup VSS di copia è necessario impostare la seguente chiave di registro nella macchina virtuale.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```


Questa tabella illustra i tipi di coerenza e le condizioni in cui si verificano durante le procedure di backup e ripristino delle macchine virtuali di Azure.

| Coerenza | Basato su VSS | Spiegazione e dettagli |
|-------------|-----------|---------|
| Coerenza con l'applicazione | Sì | Questo è il tipo di coerenza ideale per i carichi di lavoro Microsoft perché garantisce:<ol><li> L'*avvio* della macchina virtuale. <li>L'*assenza di qualsiasi danneggiamento*. <li>L'*assenza di perdite di dati*.<li> La coerenza dei dati con l'applicazione che li usa, grazie all'impiego dell'applicazione al momento del backup tramite il Servizio Copia Shadow del volume.</ol> La maggior parte dei carichi di lavoro di Microsoft ha writer del Servizio snapshot del volume che eseguono azioni specifiche di un carico di lavoro correlate alla coerenza dei dati. Ad esempio, Microsoft SQL Server ha un writer del Servizio snapshot del volume che garantisce l'esecuzione corretta delle operazioni di scrittura nel file di log delle transazioni e nel database.<br><br> Per il backup delle VM di Azure, il recupero di un punto di ripristino coerente con un'applicazione significa che l'estensione per il backup è riuscita a richiamare il flusso di lavoro del Servizio snapshot del volume e a essere completata *correttamente* prima dell'esecuzione dello snapshot della VM. Naturalmente, questo significa che anche i writer del Servizio snapshot del volume di tutte le applicazioni nella VM di Azure sono stati richiamati.<br><br>Leggere le [nozioni di base del Servizio snapshot del volume](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) per approfondimenti sui dettagli di [funzionamento](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx). |
| Coerenza con i file system | Sì, per i computer Windows | Esistono due scenari in cui il punto di ripristino può essere *coerente con i file system*:<ul><li>Backup di macchine virtuali Linux in Azure, poiché Linux non ha una piattaforma equivalente al Servizio Copia Shadow del volume.<li>Errore del Servizio Copia Shadow del volume durante il backup per macchine virtuali Windows in Azure.</li></ul> In entrambi i casi la soluzione ottimale è garantire: <ol><li>L'*avvio* della macchina virtuale. <li>L'*assenza di qualsiasi danneggiamento*.<li>L'*assenza di perdite di dati*.</ol> Le applicazioni devono implementare il proprio meccanismo di correzione sui dati ripristinati.|
| Coerenza dei dati | No | Questa situazione è equivalente a quella di un computer che si sia arrestato in modo anomalo (a causa di un ripristino software o hardware). Ciò si verifica in genere quando la macchina virtuale di Azure viene arrestata durante il backup. Per il backup della macchina virtuale di Azure, ottenere un punto di ripristino coerente con l'arresto anomalo significa che il Backup di Azure non fornisce alcuna garanzia in merito alla coerenza dei dati nel supporto di archiviazione, sia dal punto di vista del sistema operativo che dal punto di vista dell'applicazione. Solo i dati già esistente sul disco al momento del backup vengono acquisiti e sottoposti a backup. <br/> <br/> Anche se non ci sono garanzie, nella maggior parte dei casi il sistema operativo verrà avviato. Questa operazione è in genere seguita da una procedura di controllo come chkdsk per risolvere eventuali errori di danneggiamento del disco. I dati in memoria o le scritture che non sono state scaricate completamente sul disco andranno persi. In genere l'applicazione esegue il proprio meccanismo di verifica nel caso in cui sia necessario effettuare il rollback di dati. Per il backup della macchina virtuale di Azure, ottenere un punto di ripristino coerente con l'arresto anomalo significa che il Backup di Azure non fornisce alcuna garanzia in merito alla coerenza dei dati nella risorsa di archiviazione, sia dal punto di vista del sistema operativo che dal punto di vista dell'applicazione. Questo accade solitamente quando la VM di Azure è spenta al momento del backup.<br><br>Ad esempio, se il log delle transazioni contiene voci che non sono presenti nel database, il software del database esegue un rollback fino a quando i dati non sono coerenti. Quando si usano dati distribuiti tra più dischi virtuali (ad esempio volumi con spanning), un punto di ripristino coerente con l'arresto anomalo del sistema non fornisce alcuna garanzia della correttezza dei dati.|


## Prestazioni e utilizzo delle risorse
Come il software di backup distribuito in locale, anche il backup delle macchine virtuali in Azure deve essere pianificato in termini di capacità e utilizzo delle risorse. I [limiti del servizio di archiviazione di Azure](azure-subscription-service-limits.md#storage-limits) definiscono il modo in cui sono strutturate le distribuzioni delle macchine virtuali per ottenere prestazioni ottimali con il minimo impatto sui carichi di lavoro in esecuzione.

Di seguito sono riportati i due limiti principali del servizio di archiviazione di Azure che influiscono sulle prestazioni del backup:

- Numero massimo in uscita per account di archiviazione
- Frequenza delle richieste totale per account di archiviazione

### Limiti dell'account di archiviazione
Quando i dati di backup vengono copiati dall'account di archiviazione del cliente, vengono conteggiati ai fini delle metriche relative alle operazioni di I/O al secondo (IOPS) e ai dati in uscita (velocità effettiva) dell'account di archiviazione. Al contempo, le macchine virtuali sono in esecuzione e utilizzano operazioni di I/O al secondo (IOPS) e velocità effettiva. L'obiettivo è garantire che il traffico totale, del backup e delle macchine virtuali, non superi i limiti previsti per l'account di archiviazione.

### Numero di dischi
Il processo di backup tenta di consumare il maggior numero di risorse possibile per completare il backup più rapidamente. Tutte le operazioni di I/O sono tuttavia limitate dalla *velocità effettiva da raggiungere per BLOB singolo* che ha un limite di *60 MB al secondo*. Per accelerare il processo di backup, si prova a eseguire *in parallelo* il backup di ogni disco della macchina virtuale. Quindi, se una macchina virtuale contiene 4 dischi, il servizio Backup di Azure proverà a eseguire il backup di tutti e 4 i dischi in parallelo. Di conseguenza, il fattore più importante che determina il traffico di backup in uscita da un account di archiviazione del cliente è il **numero di dischi** di cui viene eseguito il backup dall’account di archiviazione.

### Pianificazione dei backup
Un ulteriore aspetto che influisce sulle prestazioni è la **pianificazione dei backup**. Se tutte le macchine virtuali sono configurate per l'esecuzione del backup alla stessa ora, aumenta il numero di dischi di cui viene eseguito il backup *in parallelo*, perché il servizio Backup di Azure prova a eseguire il backup del maggior numero possibile di dischi. Per ridurre il traffico di backup da un account di archiviazione è possibile pianificare il backup delle singole macchine virtuali a diverse ore del giorno, senza sovrapposizioni.

## Pianificazione della capacità
Tutti questi fattori indicano che è necessario pianificare in modo appropriato l'utilizzo dell'account di archiviazione. Scaricare il [foglio di Excel per la pianificazione della capacità di backup delle macchine virtuali](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) per valutare l'impatto delle scelte relative alla pianificazione dei backup e dei dischi.

### Velocità effettiva del backup
Per ogni disco di cui si esegue il backup, il servizio Backup di Azure legge i blocchi nel disco e archivia solo i dati modificati (backup incrementale). Questa tabella illustra i valori medi della velocità effettiva che è possibile aspettarsi dal Backup di Azure. In questo modo è possibile stimare la quantità di tempo necessaria per il backup di un disco con le dimensioni specificate.

| Operazione di backup | Velocità effettiva ottimale |
| ---------------- | ---------- |
| Backup iniziale | 160 Mbps |
| Backup incrementale (ripristino di emergenza) | 640 Mbps <br><br> Questo valore può abbassarsi in modo significativo in presenza di una varianza dislocata elevata sul disco di cui si deve eseguire il backup |

### Tempo totale di backup della macchina virtuale
Nonostante maggior parte del tempo venga impiegata per la lettura e la copia dei dati, altre operazioni che influiscono sul tempo totale necessario per il backup di una macchina virtuale:

- Tempo necessario per l'[installazione o l'aggiornamento dell'estensione per il backup](backup-azure-vms.md#offline-vms)
- Tempo dello snapshot: tempo impiegato per attivare uno snapshot. Gli snapshot vengono attivati vicino al momento del backup pianificato.
- Tempo di attesa nella coda: poiché il servizio di backup elabora i backup di più clienti, la copia dei dati di backup dallo snapshot nell'insieme di credenziali di Backup di Azure potrebbe non essere avviata immediatamente. Nei periodi di massimo carico, sia possono estendere al massimo di 8 ore i tempi di attesa a causa del numero di backup in corso di elaborazione. Tuttavia, il tempo di backup totale della macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.

## Crittografia dei dati

Il Backup di Azure non crittografa i dati come parte del processo di backup. È tuttavia possibile crittografare i dati all'interno della macchina virtuale e ed eseguire il backup dei dati protetti facilmente. Ulteriori informazioni su [backup dei dati crittografati](backup-azure-vms-encryption.md).


## Modalità di calcolo delle istanze protette
Le macchine virtuali di Azure di cui viene eseguito il backup mediante Backup di Azure sono soggette a [fatturazione sulla base dei prezzi di tale servizio](http://azure.microsoft.com/pricing/details/backup/). Il calcolo delle istanze protette si basa sulle dimensioni *effettive* della macchina virtuale, ovvero sul totale di tutti i dati presenti in quest'ultima con esclusione del “disco risorse”. La fatturazione *non* sarà basata sulle dimensioni massime supportate per ogni disco dati collegato alla macchina virtuale, ma sui dati effettivi archiviati nel disco dati. Analogamente, la fattura relativa all'archiviazione dei backup è basata sulla quantità di dati archiviata con Backup di Azure, ovvero sul totale dei dati effettivi presenti in ogni punto di ripristino.

Si prenda ad esempio una macchina virtuale di dimensioni A2-Standard dotata di due dischi dati aggiuntivi con capacità massima di 1 TB ciascuno. La tabella seguente indica i dati effettivi archiviati in ciascuno di tali dischi:

|Tipo di disco|Dimensioni massime|Dati effettivi presenti|
|---------|--------|------|
| Disco del sistema operativo | 1023 GB | 17 GB |
| Disco locale/Disco risorse | 135 GB | 5 GB (non incluso per il backup) |
| Disco dati 1 |	1023 GB | 30 GB |
| Disco dati 2 | 1023 GB | 0 GB |

In questo caso, le dimensioni *effettive* della macchina virtuale sono 17 GB + 30 GB + 0 GB = 47 GB. Queste dimensioni diventano le dimensioni dell'istanza protetta su cui è basata la fattura mensile. Con l'aumentare della quantità di dati presente nella macchina virtuale, le dimensioni dell'istanza protetta usata per la fatturazione cambiano di conseguenza.

La fatturazione inizia solo dopo il completamento del primo backup corretto. A questo punto inizierà la fatturazione sia per le istanze di archiviazione sia per quelle protette. La fatturazione continua fino a quando per la macchina virtuale sono presenti *dati di backup archiviati con Backup di Azure*. Se i dati di backup vengono mantenuti, l'esecuzione dell'operazione Arresta protezione non interrompe la fatturazione. La fatturazione relativa a una macchina virtuale specificata verrà interrotta solo se la protezione viene arrestata *e* i dati di backup vengono eliminati. Quando non sono presenti processi di backup attivi (quando la protezione è stata interrotta), le dimensioni della macchina virtuale al momento dell'ultimo backup corretto diventano le dimensioni dell'istanza protetta su cui è basata la fattura mensile.

## Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## Passaggi successivi

- [Backup delle macchine virtuali](backup-azure-vms.md)
- [Gestire i backup delle macchine virtuali](backup-azure-manage-vms.md)
- [Ripristino di macchine virtuali](backup-azure-restore-vms.md)
- [Risolvere i problemi relativi al backup delle macchine virtuali di Azure](backup-azure-vms-troubleshoot.md)

<!---HONumber=AcomDC_1203_2015-->