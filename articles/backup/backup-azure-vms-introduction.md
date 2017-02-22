---
title: Pianificare l&quot;infrastruttura di backup delle VM in Azure | Documentazione Microsoft
description: Considerazioni importanti sulla pianificazione del backup di macchine virtuali in Azure
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
editor: 
keywords: backup di vm, backup di macchine virtuali
ms.assetid: 19d2cf82-1f60-43e1-b089-9238042887a9
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/08/2016
ms.author: markgal;trinadhk
translationtype: Human Translation
ms.sourcegitcommit: a4045fc0fc6e2c263da06ed31a590714e80fb4d4
ms.openlocfilehash: ac13b82c885720fa6d3d127b8e8dbbace5b09ef5


---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Pianificare l'infrastruttura di backup delle VM in Azure
Questo articolo fornisce suggerimenti relativi alle prestazioni e alle risorse per semplificare la pianificazione dell'infrastruttura di backup delle macchine virtuali. Definisce anche gli elementi fondamentali del servizio Backup. Questi aspetti possono essere essenziali per determinare l'architettura, la pianificazione della capacità e la pianificazione generale. Se è stato [preparato l'ambiente](backup-azure-vms-prepare.md), questo è il passaggio successivo prima di iniziare a [eseguire il backup delle VM](backup-azure-vms.md). Per altre informazioni sulle macchine virtuali di Azure, vedere [Macchine virtuali - Documentazione](https://azure.microsoft.com/documentation/services/virtual-machines/).

## <a name="how-does-azure-back-up-virtual-machines"></a>In che modo Azure esegue il backup delle macchine virtuali?
Quando il servizio Backup di Azure avvia un processo di backup all'ora pianificata, richiede all'estensione per il backup di acquisire uno snapshot temporizzato. Questo snapshot viene acquisito in combinazione con il Servizio Copia Shadow del volume (VSS) per ottenere uno snapshot coerente dei dischi nella macchina virtuale senza che sia necessario spegnerla.

Dopo l'acquisizione dello snapshot, il servizio Backup di Azure trasferisce i dati nell'insieme di credenziali per il backup. Per aumentare l'efficienza del processo di backup, il servizio identifica e trasferisce solo i blocchi di dati che sono stati modificati dall'ultimo backup.

![Architettura del backup delle macchine virtuali di Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino.

### <a name="data-consistency"></a>Coerenza dei dati
Le operazioni di backup e ripristino dei dati aziendali critici sono ulteriormente complicate dal fatto che il backup di tali dati deve essere eseguito mentre le applicazioni che generano i dati sono in esecuzione. Per risolvere il problema, il servizio Backup di Azure fornisce funzionalità di backup coerenti con l'applicazione per i carichi di lavoro Microsoft avvalendosi del Servizio Copia Shadow del volume per garantire che la scrittura dei dati nell'archiviazione venga effettuata correttamente.

> [!NOTE]
> Per le macchine virtuali Linux, sono disponibili solo backup coerenti con i file, perché Linux non ha una piattaforma equivalente al Servizio Copia Shadow del volume.
>
>

Backup di Azure esegue backup VSS completi nelle macchine virtuali di Windows (altre informazioni su [backup VSS completi](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Per abilitare i backup VSS di copia è necessario impostare la seguente chiave di registro nella macchina virtuale.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```


Questa tabella illustra i tipi di coerenza e le condizioni in cui si verificano durante le procedure di backup e ripristino delle macchine virtuali di Azure.

| Coerenza | Basato su VSS | Spiegazione e dettagli |
| --- | --- | --- |
| Coerenza con l'applicazione |Sì |Questo è il tipo di coerenza ideale per i carichi di lavoro Microsoft perché garantisce:<ol><li> L' *avvio*della macchina virtuale. <li>L' *assenza di qualsiasi danneggiamento*. <li>L'*assenza di perdite di dati*.<li> La coerenza dei dati con l'applicazione che li usa, grazie all'impiego dell'applicazione al momento del backup tramite il Servizio Copia Shadow del volume.</ol> La maggior parte dei carichi di lavoro di Microsoft ha writer VSS che eseguono azioni specifiche di un carico di lavoro correlate alla coerenza dei dati. Ad esempio, Microsoft SQL Server ha un writer VSS che garantisce l'esecuzione corretta delle operazioni di scrittura nel file di log delle transazioni e nel database.<br><br> Per i backup delle VM di Azure, il recupero di un punto di ripristino coerente con un'applicazione significa che l'estensione per il backup è riuscita a richiamare il flusso di lavoro del Servizio Copia Shadow del volume e a essere completata *correttamente* prima dell'esecuzione dello snapshot della VM. Naturalmente, questo significa che anche i writer VSS di tutte le applicazioni nella VM di Azure sono stati richiamati.<br><br>(apprendere le [nozioni di base di VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) e approfondire i dettagli di [funzionamento](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). |
| Coerenza del file system |Sì, per i computer basati su Windows |Esistono due scenari in cui il punto di ripristino può essere *coerente con il file system*:<ul><li>Backup delle macchine virtuali Linux in Azure, poiché Linux non dispone di una piattaforma equivalente al Servizio Copia Shadow del volume.<li>Errore VSS durante il backup di macchine virtuali di Windows in Azure.</li></ul> In entrambi i casi la soluzione ottimale è garantire: <ol><li> L' *avvio*della macchina virtuale. <li>L' *assenza di qualsiasi danneggiamento*.<li>L'*assenza di perdite di dati*.</ol> Le applicazioni devono implementare il proprio meccanismo di correzione sui dati ripristinati. |
| Coerenza dei dati |No |Questa situazione è equivalente a quella di una macchina virtuale che si è arrestata in modo anomalo a causa di un ripristino software o hardware. Ciò si verifica in genere quando la macchina virtuale di Azure viene arrestata durante il backup. Per i backup delle macchine virtuali di Azure, il recupero di un punto di ripristino coerente con l'arresto anomalo indica che Backup di Azure non offre alcuna garanzia di coerenza dei dati sul supporto di archiviazione, dal punto di vista del sistema operativo o dal punto di vista dell'applicazione. Solo i dati già esistente sul disco al momento del backup vengono acquisiti e sottoposti a backup. <br/> <br/> Anche se non ci sono garanzie, nella maggior parte dei casi il sistema operativo verrà avviato. Questa operazione è in genere seguita da una procedura di controllo come chkdsk per risolvere eventuali errori di danneggiamento del disco. I dati in memoria o le scritture che non sono state scaricate completamente sul disco andranno persi. In genere l'applicazione esegue il proprio meccanismo di verifica nel caso in cui sia necessario effettuare il rollback di dati. <br><br>Ad esempio, se il log delle transazioni contiene voci che non sono presenti nel database, il software del database esegue un rollback fino a quando i dati sono coerenti. Quando i dati sono distribuiti tra più dischi virtuali (ad esempio volumi con spanning), un punto di ripristino coerente con l'arresto anomalo del sistema non fornisce alcuna garanzia della correttezza dei dati. |

## <a name="performance-and-resource-utilization"></a>Prestazioni e utilizzo delle risorse
Analogamente al software distribuito in locale, è necessario pianificare le esigenze di utilizzo di capacità e risorse durante il backup delle macchine virtuali in Azure. I [limiti del servizio di archiviazione di Azure](../azure-subscription-service-limits.md#storage-limits) definiscono il modo in cui sono strutturate le distribuzioni delle macchine virtuali per ottenere prestazioni ottimali con il minimo impatto sui carichi di lavoro in esecuzione.

Prestare attenzione ai limiti seguenti dell'Archiviazione di Azure durante la pianificazione delle prestazioni del backup:

* Numero massimo in uscita per account di archiviazione
* Frequenza delle richieste totale per account di archiviazione

### <a name="storage-account-limits"></a>Limiti dell'account di archiviazione
Quando i dati di backup vengono copiati dall'account di archiviazione, vengono conteggiati ai fini delle metriche relative alle operazioni di input/output al secondo (IOPS) e ai dati in uscita (velocità effettiva) dell'account di archiviazione. Al contempo, le macchine virtuali sono in esecuzione e utilizzano operazioni di I/O al secondo (IOPS) e velocità effettiva. L'obiettivo è garantire che il traffico totale, del backup e delle macchine virtuali, non superi i limiti previsti per l'account di archiviazione.

### <a name="number-of-disks"></a>Numero di dischi
Il processo di backup prova a completare un processo di backup il più velocemente possibile, utilizzando quindi la quantità più elevata possibile di risorse. Tutte le operazioni di I/O sono tuttavia limitate dalla *velocità effettiva da raggiungere per BLOB singolo* che ha un limite di 60 MB al secondo. Nel tentativo di massimizzare la velocità, il processo di backup prova a eseguire il backup di ciascun disco della macchina virtuale *in parallelo*. Quindi, se una macchina virtuale contiene quattro dischi, il servizio Backup di Azure prova a eseguire il backup di tutti e quattro i dischi in parallelo. Di conseguenza, il fattore più importante che determina il traffico di backup in uscita da un account di archiviazione del cliente è il **numero di dischi** di cui viene eseguito il backup dall'account di archiviazione.

### <a name="backup-schedule"></a>Pianificazione dei backup
Un altro aspetto che influisce sulle prestazioni è la **pianificazione dei backup**. Se si configurano i criteri in modo che venga eseguito il backup di tutte le macchine virtuali contemporaneamente, è stato pianificato un blocco del traffico. Il processo di backup proverà a eseguire il backup di tutti i dischi in parallelo. Per ridurre il traffico di backup da un account di archiviazione è possibile pianificare il backup delle singole macchine virtuali a diverse ore del giorno, senza sovrapposizioni.

## <a name="capacity-planning"></a>Pianificazione della capacità
Tutti questi fattori indicano che è necessario pianificare in modo appropriato l'utilizzo dell'account di archiviazione. Scaricare il [foglio di Excel per la pianificazione della capacità di backup delle macchine virtuali](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) per valutare l'impatto delle scelte relative alla pianificazione dei backup e dei dischi.

### <a name="backup-throughput"></a>Velocità effettiva del backup
Per ogni disco di cui si esegue il backup, il servizio Backup di Azure legge i blocchi nel disco e archivia solo i dati modificati (backup incrementale). Questa tabella illustra i valori medi della velocità effettiva che è possibile aspettarsi dal Backup di Azure. In questo modo è possibile stimare la quantità di tempo necessaria per il backup di un disco con le dimensioni specificate.

| Operazione di backup | Velocità effettiva ottimale |
| --- | --- |
| Backup iniziale |160 Mbps |
| Backup incrementale (ripristino di emergenza) |640 Mbps  <br><br> Questo valore può diminuire in modo significativo in presenza di una varianza dislocata elevata sul disco di cui si deve eseguire il backup. |

## <a name="total-vm-backup-time"></a>Tempo totale di backup della macchina virtuale
Nonostante la maggior parte del tempo venga impiegata per la lettura e la copia dei dati, ci sono altre operazioni che influiscono sul tempo totale necessario per il backup di una macchina virtuale:

* Tempo necessario per l' [installazione o l'aggiornamento dell'estensione per il backup](backup-azure-vms.md).
* Tempo dello snapshot, ovvero il tempo impiegato per attivare uno snapshot. Gli snapshot vengono attivati vicino al momento del backup pianificato.
* Tempo di attesa di coda. Poiché il servizio di backup elabora i backup di più clienti, è possibile che la copia dei dati di backup dallo snapshot nell'insieme di credenziali di Backup o di Servizi di ripristino non venga avviata immediatamente. Nei periodi di massimo carico, l'attesa può durare al massimo 8 ore a causa del numero di backup in corso di elaborazione. Tuttavia, il tempo di backup totale della macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.

## <a name="total-restore-time"></a>Tempo totale di ripristino
Un'operazione di ripristino è costituita da due sottoattività principali: la copia dei dati dall'insieme di credenziali all'account di archiviazione scelto del cliente e la creazione della macchina virtuale. La copia dei dati dall'insieme di credenziali dipende dalla posizione in cui vengono archiviati i backup internamente in Azure e dalla posizione in cui è memorizzato l'account di archiviazione del cliente. Il tempo necessario alla copia dipende dalle seguenti attività:
* Tempo di attesa in coda: dal momento che il servizio esegue ripristini da più utenti contemporaneamente, le richieste di ripristino vengono messe in coda.
* Tempo di copia dei dati: i dati vengono copiati con un processo simile a quello di un primo backup dall'insieme di credenziali all'account di archiviazione del cliente. Se l'account di archiviazione del cliente in cui devono essere scritti i dati dall'insieme di credenziali viene caricato, il tempo di copia può aumentare. Pertanto per ottimizzare il tempo di copia, è necessario assicurarsi di selezionare un account di archiviazione che non venga caricato con altre letture e scritture di applicazione durante il ripristino.

## <a name="best-practices"></a>Procedure consigliate
È consigliabile seguire queste procedure durante la configurazione del backup per le macchine virtuali:

* Non pianificare il backup di più di 10 macchine virtuali classiche contemporaneamente dallo stesso servizio cloud. È consigliabile sfalsare di un'ora le ore di inizio dei backup se si vuole eseguire il backup di più macchine virtuali dallo stesso servizio cloud.
* Non pianificare il backup contemporaneo di più di 40 macchine virtuali.
* Pianificare i backup delle macchine virtuali durante orari non di picco, in modo che il servizio Backup usi IOPS per il trasferimento dei dati dall'account di archiviazione del cliente all'insieme di credenziali di Backup o di Servizi di ripristino.
* Assicurarsi che un criterio faccia riferimento a VM in più account di archiviazione. È consigliabile non proteggere con la stessa pianificazione di backup più di 20 dischi in totale da un singolo account di archiviazione. Se un account di archiviazione include più di 20 dischi, suddividere le VM tra più criteri per ottenere i valori di IOPS necessari durante la fase di trasferimento del processo di backup.
* Non ripristinare una macchina virtuale in esecuzione nell'Archiviazione Premium nello stesso account di archiviazione. Se il processo dell'operazione di ripristino coincide con l'operazione di backup, il valore di IOPS disponibile per il backup sarà ridotto.
* È consigliabile eseguire ogni macchina virtuale Premium VM in un account di archiviazione Premium distinto per assicurare prestazioni di backup ottimali.

## <a name="data-encryption"></a>Crittografia dei dati
Il Backup di Azure non crittografa i dati come parte del processo di backup. È tuttavia possibile crittografare i dati all'interno della macchina virtuale e ed eseguire il backup dei dati protetti facilmente (altre informazioni sul [backup dei dati crittografati](backup-azure-vms-encryption.md)).

## <a name="how-are-protected-instances-calculated"></a>Modalità di calcolo delle istanze protette
Le macchine virtuali di Azure di cui viene eseguito il backup mediante Backup di Azure sono soggette a [fatturazione sulla base dei prezzi di tale servizio](https://azure.microsoft.com/pricing/details/backup/). Il calcolo delle istanze protette si basa sulle dimensioni *effettive* della macchina virtuale, ovvero sul totale di tutti i dati presenti in quest'ultima con esclusione del “disco risorse”.

La fatturazione *non* sarà basata sulle dimensioni massime supportate per ogni disco dati collegato alla macchina virtuale, ma sui dati effettivi archiviati nel disco dati. Analogamente, la fattura relativa all'archiviazione dei backup è basata sulla quantità di dati archiviata con Backup di Azure, ovvero sul totale dei dati effettivi presenti in ogni punto di ripristino.

Si prenda ad esempio una macchina virtuale di dimensioni A2-Standard dotata di due dischi dati aggiuntivi con capacità massima di 1 TB ciascuno. La tabella seguente indica i dati effettivi archiviati in ciascuno di tali dischi:

| Tipo di disco | Dimensioni massime | Dati effettivi presenti |
| --- | --- | --- |
| Disco del sistema operativo |1023 GB |17 GB |
| Disco locale/Disco risorse |135 GB |5 GB (non incluso per il backup) |
| Disco dati 1 |1023 GB |30 GB |
| Disco dati 2 |1023 GB |0 GB |

In questo caso, le dimensioni *effettive* della macchina virtuale sono 17 GB + 30 GB + 0 GB = 47 GB. Queste dimensioni diventano le dimensioni dell'istanza protetta su cui è basata la fattura mensile. Con l'aumentare della quantità di dati presente nella macchina virtuale, le dimensioni dell'istanza protetta usata per la fatturazione cambiano di conseguenza.

La fatturazione inizia solo dopo il completamento del primo backup corretto. A questo punto inizierà la fatturazione sia per le istanze di archiviazione sia per quelle protette. La fatturazione continua fino a quando per la macchina virtuale sono presenti *dati di backup archiviati con Backup di Azure* . Se i dati di backup vengono mantenuti, l'esecuzione dell'operazione Arresta protezione non interrompe la fatturazione.

La fatturazione relativa a una macchina virtuale specificata verrà interrotta solo se la protezione viene arrestata *e* i dati di backup vengono eliminati. Quando non sono presenti processi di backup attivi (quando la protezione è stata interrotta), le dimensioni della macchina virtuale al momento dell'ultimo backup corretto diventano le dimensioni dell'istanza protetta su cui è basata la fattura mensile.

## <a name="questions"></a>Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](http://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire il backup di macchine virtuali](backup-azure-vms.md)
* [Gestire i backup delle macchine virtuali](backup-azure-manage-vms.md)
* [Ripristino di macchine virtuali](backup-azure-restore-vms.md)
* [Risolvere i problemi relativi al backup delle macchine virtuali di Azure](backup-azure-vms-troubleshoot.md)



<!--HONumber=Dec16_HO3-->


