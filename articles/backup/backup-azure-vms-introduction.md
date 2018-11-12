---
title: Pianificazione dell'infrastruttura di backup delle macchine virtuali in Azure
description: Considerazioni importanti sulla pianificazione del backup di macchine virtuali in Azure
services: backup
author: markgalioto
manager: carmonm
keywords: backup di vm, backup di macchine virtuali
ms.service: backup
ms.topic: conceptual
ms.date: 8/29/2018
ms.author: markgal
ms.openlocfilehash: ae02a1bcbf00a022cfd884b02141ce084f1fffa8
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/07/2018
ms.locfileid: "51232461"
---
# <a name="plan-your-vm-backup-infrastructure-in-azure"></a>Pianificare l'infrastruttura di backup delle VM in Azure
Questo articolo fornisce suggerimenti relativi alle prestazioni e alle risorse per semplificare la pianificazione dell'infrastruttura di backup delle macchine virtuali. Definisce anche gli elementi fondamentali del servizio Backup. Questi aspetti possono essere essenziali per determinare l'architettura, la pianificazione della capacità e la pianificazione generale. Se è stato [preparato l'ambiente](backup-azure-arm-vms-prepare.md), la pianificazione è il passaggio successivo prima di iniziare a [eseguire il backup delle VM](backup-azure-arm-vms.md). Per altre informazioni sulle macchine virtuali di Azure, vedere [Macchine virtuali - Documentazione](https://azure.microsoft.com/documentation/services/virtual-machines/). 

> [!NOTE]
> Questo articolo riguarda l'uso di dischi gestiti e non gestiti. Se si usano dischi non gestiti, vengono suggeriti gli account di archiviazione. Se si usa [Azure Managed Disks](../virtual-machines/windows/managed-disks-overview.md), non è necessario preoccuparsi dei problemi relativi alle prestazioni e all'uso delle risorse. Azure ottimizza l'uso dello spazio di archiviazione per l'utente.
>

## <a name="how-does-azure-back-up-virtual-machines"></a>In che modo Azure esegue il backup delle macchine virtuali?
Quando il servizio Backup di Azure avvia un processo di backup all'ora pianificata, richiede all'estensione di backup di acquisire uno snapshot temporizzato. Il servizio Backup di Azure usa l'estensione _VMSnapshot_ in Windows e l'estensione _VMSnapshotLinux_ in Linux. L'estensione viene installata all primo backup delle macchine virtuali. Per installare l'estensione, è necessario che la macchina virtuale sia in esecuzione. Se la macchina virtuale non è in esecuzione, il servizio Backup crea uno snapshot dell'archivio sottostante (poiché non si verifica alcuna scrittura di applicazione durante l'arresto della macchina virtuale).

Quando crea uno snapshot delle macchine virtuali di Windows, il servizio Backup si coordina con il servizio Copia Shadow del volume (VSS) per ottenere uno snapshot coerente dei dischi delle macchine virtuali. Se si esegue il backup di macchine virtuali Linux, è possibile scrivere i propri script personalizzati per assicurare la coerenza quando si crea uno snapshot delle macchine virtuali. Dettagli su come richiamare gli script sono forniti più avanti in questo articolo.

Dopo che il servizio Backup di Azure crea lo snapshot, i data vengono trasferiti nell'insieme di credenziali. Per offrire la massima efficienza, il servizio identifica e trasferisce solo i blocchi di dati che sono stati modificati dall'ultimo backup.

![Architettura del backup delle macchine virtuali di Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino.

> [!NOTE]
> 1. Durante il processo di backup, il servizio Backup di Azure non include il disco temporaneo collegato alla macchina virtuale. Per altre informazioni, vedere il blog sull'[archiviazione temporanea](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).
> 2. Backup di Azure crea uno snapshot a livello di archiviazione e lo trasferisce nell'insieme di credenziali. Non modificare le chiavi dell'account di archiviazione fino al termine del processo di backup.
>

### <a name="data-consistency"></a>Coerenza dei dati
Le operazioni di backup e ripristino dei dati aziendali critici sono ulteriormente complicate dal fatto che il backup di tali dati deve essere eseguito mentre le applicazioni che generano i dati sono in esecuzione. Per risolvere questo problema, Backup di Azure supporta i backup coerenti con l'applicazione per le macchine virtuali di Windows e di Linux
#### <a name="windows-vm"></a>Macchina virtuale Windows
Backup di Azure esegue backup VSS completi nelle macchine virtuali di Windows (altre informazioni su [backup VSS completi](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)). Per abilitare i backup VSS di copia è necessario impostare la chiave di registro seguente nella macchina virtuale.

```
[HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
"USEVSSCOPYBACKUP"="TRUE"
```

#### <a name="linux-vms"></a>Macchine virtuali di Linux

Backup di Azure offre un framework di scripting per controllare il flusso di lavoro di backup e l'ambiente. Per garantire backup delle macchine virtuali Linux con coerenza delle applicazioni, usare il framework di script per creare script pre-backup e post-backup personalizzati. Chiamare lo script pre-backup prima di creare lo snapshot della macchina virtuale poi chiamare lo script post-backup al termine del processo di snapshot. Per altre informazioni, vedere l'articolo dedicato ai [backup delle macchine virtuali Linux con coerenza delle applicazioni](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent).

> [!NOTE]
> Backup di Azure richiama solo script di pre-backup e di post-backup scritti dal cliente. Se script di pre-backup e di post-backup vengono eseguiti correttamente, Azure Backup contrassegna il punto di ripristino come coerente con l'applicazione. Tuttavia, il cliente è responsabile della coerenza con l'applicazione quando usa script personalizzati.
>

La tabella seguente riporta i tipi di coerenza e le condizioni in cui si verificano.

| Consistency | Basato su VSS | Spiegazione e dettagli |
| --- | --- | --- |
| Coerenza con l'applicazione |Sì per Windows|La coerenza dell'applicazione è ideale per i carichi di lavoro perché garantisce:<ol><li> L' *avvio*della macchina virtuale. <li>L' *assenza di qualsiasi danneggiamento*. <li>L'*assenza di perdite di dati*.<li> La coerenza dei dati con l'applicazione che li usa, grazie all'impiego dell'applicazione al momento del backup tramite il Servizio Copia shadow del volume o lo script di pre-/post-backup.</ol> <li>*Macchine virtuali Windows*: la maggior parte dei carichi di lavoro di Microsoft ha writer VSS che eseguono azioni specifiche del carico di lavoro correlate alla coerenza dei dati. Ad esempio, il writer VSS di SQL Server garantisce l'esecuzione corretta delle operazioni di scrittura nel file di log delle transazioni e nel database. Per i backup delle macchine virtuali IaaS, per creare un punto di ripristino concoerenza delle applicazioni, l'estensione del backup deve richiamare il flusso di lavoro del VSS e completarlo prima dell'acquisizione dello snapshot della macchina virtuale. Affinché lo snapshot della macchina virtuale di Azure sia preciso, è necessario completare anche i writer VSS di tutte le applicazioni delle VM di Azure. (apprendere le [nozioni di base di VSS](http://blogs.technet.com/b/josebda/archive/2007/10/10/the-basics-of-the-volume-shadow-copy-service-vss.aspx) e approfondire i dettagli di [funzionamento](https://technet.microsoft.com/library/cc785914%28v=ws.10%29.aspx)). </li> <li> *Macchine virtuali Linux*: i clienti possono eseguire [script di pre-backup e post-backup personalizzati per garantire coerenza con l'applicazione](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). </li> |
| Coerenza del file system |Sì, per i computer basati su Windows |Esistono due scenari in cui il punto di ripristino può essere *coerente con il file system*:<ul><li>Backup di macchine virtuali Linux in Azure, senza script di pre-/post-backup o se lo script di pre-/post-backup non è stato eseguito correttamente. <li>Errore VSS durante il backup di macchine virtuali di Windows in Azure.</li></ul> In entrambi i casi la soluzione ottimale è garantire: <ol><li> L' *avvio*della macchina virtuale. <li>L' *assenza di qualsiasi danneggiamento*.<li>L'*assenza di perdite di dati*.</ol> Le applicazioni devono implementare il proprio meccanismo di correzione sui dati ripristinati. |
| Coerenza dei dati |No  |Questa situazione è equivalente a quella di una macchina virtuale che si è arrestata in modo anomalo a causa di un ripristino software o hardware. La coerenza per arresto anomalo del sistema si verifica in genere quando la macchina virtuale di Azure viene arrestata durante il backup. Un punto di ripristino coerente con l'arresto anomalo non offre alcuna garanzia di coerenza dei dati sul supporto di archiviazione, dal punto di vista del sistema operativo o dal punto di vista dell'applicazione. Solo i dati già esistenti sul disco al momento del backup vengono acquisiti e sottoposti a backup. <br/> <br/> Anche se non vi sono garanzie, nella maggior parte dei casi il sistema operativo si avvia. Questa operazione è in genere seguita da una procedura di controllo del disco come chkdsk per correggere eventuali errori di danneggiamento del disco. I dati in memoria o le scritture che non sono state trasferite sul disco andranno persi. In genere l'applicazione esegue il proprio meccanismo di verifica nel caso in cui sia necessario effettuare il rollback di dati. <br><br>Ad esempio, se il log delle transazioni contiene voci non presenti nel database, il software del database esegue un rollback fino a quando i dati non sono coerenti. Quando i dati sono distribuiti tra più dischi virtuali (ad esempio volumi con spanning), un punto di ripristino coerente con l'arresto anomalo del sistema non fornisce alcuna garanzia della correttezza dei dati. |

## <a name="performance-and-resource-utilization"></a>Prestazioni e utilizzo delle risorse
Analogamente al software distribuito in locale, è necessario pianificare le esigenze di utilizzo di capacità e risorse durante il backup delle macchine virtuali in Azure. I [limiti del servizio di archiviazione di Azure](../azure-subscription-service-limits.md#storage-limits) definiscono il modo in cui sono strutturate le distribuzioni delle macchine virtuali per ottenere prestazioni ottimali con il minimo impatto sui carichi di lavoro in esecuzione.

### <a name="number-of-disks"></a>Numero di dischi
Il processo di backup prova a completare un processo di backup il più velocemente possibile, utilizzando quindi la quantità più elevata possibile di risorse. Tutte le operazioni di I/O sono tuttavia limitate dalla *velocità effettiva da raggiungere per BLOB singolo* che ha un limite di 60 MB al secondo. Nel tentativo di massimizzare la velocità, il processo di backup prova a eseguire il backup di ciascun disco della macchina virtuale *in parallelo*. Se una macchina virtuale contiene quattro dischi, il servizio prova a eseguire il backup di tutti e quattro i dischi in parallelo. Il fattore più importante che determina il traffico di backup di un account di archiviazione è il **numero di dischi** di cui viene eseguito il backup.

### <a name="backup-schedule"></a>Pianificazione dei backup
Un altro aspetto che influisce sulle prestazioni è la **pianificazione dei backup**. Se si configurano i criteri in modo che venga eseguito il backup di tutte le macchine virtuali contemporaneamente, è stato pianificato un blocco del traffico. Il processo di backup tenta di eseguire il backup di tutti i dischi in parallelo. Per ridurre il traffico di backup, eseguire il backup delle singole macchine virtuali in diverse ore del giorno, senza sovrapposizioni.

## <a name="capacity-planning"></a>Pianificazione della capacità
Scaricare il [foglio di Excel per la pianificazione della capacità di backup delle macchine virtuali](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) per valutare l'impatto delle scelte relative alla pianificazione dei backup e dei dischi.

### <a name="backup-throughput"></a>Velocità effettiva del backup
Per ogni disco di cui si esegue il backup, il servizio Backup di Azure legge i blocchi nel disco e archivia solo i dati modificati (backup incrementale). Nella tabella seguente vengono illustrati i valori di velocità effettiva del servizio Backup. Usando i dati seguenti è possibile stimare la quantità di tempo necessaria per il backup di un disco con le dimensioni specificate.

| Operazione di backup | Velocità effettiva ottimale |
| --- | --- |
| Backup iniziale |160 Mbps |
| Backup incrementale (ripristino di emergenza) |640 Mbps  <br><br> La velocità effettiva si riduce in modo significativo se i dati modificati (per cui è necessario eseguire il backup) sono dispersi sul disco rigido.|

## <a name="total-vm-backup-time"></a>Tempo totale di backup della macchina virtuale
Nonostante la maggior parte del tempo di backup venga impiegata per la lettura e la copia dei dati, altre operazioni influiscono sul tempo totale necessario per il backup di una macchina virtuale:

* Tempo necessario per l' [installazione o l'aggiornamento dell'estensione per il backup](backup-azure-arm-vms.md).
* Tempo dello snapshot, ovvero il tempo impiegato per attivare uno snapshot. Gli snapshot vengono attivati vicino al momento del backup pianificato.
* Tempo di attesa di coda. Poiché il servizio Backup elabora processi per più clienti contemporaneamente, i dati dello snapshot potrebbero non essere copiati immediatamente nell'insieme di credenziali di Servizi di ripristino. Nei momenti di picco del carico, potrebbero essere necessarie fino a otto ore prima che i backup vengano elaborati. Tuttavia, il tempo di backup totale della macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.
Il tempo totale di backup inferiore alle 24 ore è valido per i backup incrementali, ma potrebbe non esserlo per il primo backup. Il tempo del primo backup è proporzionale e può essere superiore a 24 ore in base alle dimensioni dei dati e all'ora in cui si esegue il backup.
* L'ora del trasferimento dati, necessaria al servizio di backup per calcolare le modifiche incrementali da backup e trasferimento precedenti, cambia nell'archiviazione dell'insieme di credenziali.

### <a name="why-are-backup-times-longer-than-12-hours"></a>Perché i tempi di backup superano le 12 ore?

Il backup è costituito da due fasi: l'acquisizione di snapshot e il trasferimento degli snapshot nell'insieme di credenziali. Il servizio di Backup consente di ottimizzare l'archiviazione. Quando si trasferiscono i dati dello snapshot all'insieme di credenziali, il servizio trasferisce solo le modifiche incrementali dello snapshot precedente.  Per determinare le modifiche incrementali, il servizio calcola il checksum dei blocchi. Se viene modificato un blocco, il blocco viene identificato come blocco da inviare all'insieme di credenziali. Il servizio penetra ulteriormente in ognuno dei blocchi identificati, alla ricerca di opportunità per ridurre al minimo i dati da trasferire. Dopo aver valutato tutti i blocchi modificati, il servizio unisce le modifiche e le invia all'insieme di credenziali. In alcune applicazioni legacy, le scritture frammentate e di piccole dimensioni non sono ottimali per l'archiviazione. Se lo snapshot contiene molte delle operazioni di scrittura frammentate e di piccole dimensioni, il servizio impiega più tempo nell'elaborazione dei dati scritti dalle applicazioni. Il blocco di scrittura dell'applicazione minimo consigliato per le applicazioni in esecuzione su una macchina virtuale è pari a 8 kB. Se l'applicazione usa un blocco più piccolo degli 8 KB, le prestazioni del backup ne risentono. Per assistenza sull'ottimizzazione dell'applicazione per migliorare le prestazioni di backup, vedere l'articolo sull'[ottimizzazione delle applicazioni per prestazioni ottimali con Archiviazione di Azure](../virtual-machines/windows/premium-storage-performance.md). Sebbene l'articolo sulle prestazioni di backup usi esempi di archiviazione Premium, le linee guida sono applicabili ai dischi di archiviazione Standard.

## <a name="total-restore-time"></a>Tempo totale di ripristino

Un'operazione di ripristino è costituita da due attività principali: la copia dei dati dall'insieme di credenziali all'account di archiviazione scelto del cliente e la creazione della macchina virtuale. Il tempo necessario per copiare i dati dall'insieme di credenziali dipende dalla posizione in cui vengono archiviati i backup in Azure e dalla posizione dell'account di archiviazione del cliente. Il tempo necessario alla copia dipende dalle seguenti attività:
* Tempo di attesa in coda: dal momento che il servizio esegue ripristini da più clienti contemporaneamente, le richieste di ripristino vengono messe in coda.
* Tempo di copia dei dati: i dati vengono copiati dall'insieme di credenziali nell'account di archiviazione del cliente. I tempi di ripristino dipendono dalle IOPS e dalla velocità effettiva dl servizio Backup di Azure sull'account di archiviazione del cliente selezionato. Per ridurre il tempo di copia durante il processo di ripristino, selezionare un account di archiviazione non caricato con altre letture e scritture di applicazione.

## <a name="best-practices"></a>Procedure consigliate

È consigliabile seguire queste procedure durante la configurazione del backup per tutte le macchine virtuali:

* Non pianificare il backup di più di 10 macchine virtuali classiche contemporaneamente dallo stesso servizio cloud. Se si vuole eseguire il backup di più macchine virtuali dallo stesso servizio cloud, sfalsare di un'ora le ore di inizio dei backup.
* Non pianificare il backup di più di 100 macchine virtuali per uno stesso insieme di credenziali contemporaneamente.
* Pianificare i backup di macchine Virtuali durante le ore non di punta. In questo modo il servizio Backup usa le IOPS per il trasferimento dei dati dall'account di archiviazione del cliente all'insieme di credenziali.
* Verificare che le macchine virtuali Linux abilitate per il backup abbiano la versione 2.7 di Python o superiore.

### <a name="best-practices-for-vms-with-unmanaged-disks"></a>Procedure consigliate per le macchine virtuali con dischi non gestiti

Le indicazioni seguenti si applicano solo alle macchine virtuali con dischi non gestiti. Se le macchine virtuali usano dischi gestiti, il servizio Backup controlla tutte le attività di gestione dell'archiviazione.

* Assicurarsi di applicare un criterio di backup per macchine virtuali distribuite tra più account di archiviazione. È consigliabile non proteggere con la stessa pianificazione di backup più di 20 dischi in totale da un singolo account di archiviazione. Se un account di archiviazione include più di 20 dischi, suddividere le VM tra più criteri per ottenere i valori di IOPS necessari durante la fase di trasferimento del processo di backup.
* Non ripristinare una macchina virtuale in esecuzione nell'Archiviazione Premium nello stesso account di archiviazione. Se il processo dell'operazione di ripristino coincide con l'operazione di backup, il valore di IOPS disponibile per il backup sarà ridotto.
* Per il backup di macchine virtuali Premium nello stack di backup V1, è consigliabile allocare solo il 50% dello spazio totale dell'account di archiviazione, in modo che il servizio Backup possa copiare lo snapshot nell'account di archiviazione e trasferire i dati dall'account di archiviazione all'insieme di credenziali.


## <a name="data-encryption"></a>Crittografia dei dati
Il Backup di Azure non crittografa i dati come parte del processo di backup. È tuttavia possibile crittografare i dati all'interno della macchina virtuale e ed eseguire il backup dei dati protetti facilmente (altre informazioni sul [backup dei dati crittografati](backup-azure-vms-encryption.md)).

## <a name="calculating-the-cost-of-protected-instances"></a>Calcolo del costo delle istanze protette
Le macchine virtuali di Azure di cui viene eseguito il backup mediante Backup di Azure sono soggette a [fatturazione sulla base dei prezzi di tale servizio](https://azure.microsoft.com/pricing/details/backup/). Il calcolo delle istanze protette si basa sulle dimensioni *effettive* della macchina virtuale, ovvero sul totale di tutti i dati presenti nella macchina virtuale con esclusione dell'archivio temporaneo.

I prezzi per il backup delle macchine virtuali non si basano sulla dimensione massima supportata di ogni disco dati collegato alla macchina virtuale. Si basano viceversa sui dati effettivi archiviati nel disco dati. Analogamente, la fattura relativa all'archiviazione dei backup è basata sulla quantità di dati archiviata in Backup di Azure, ovvero sul totale dei dati effettivi presenti in ogni punto di ripristino.

Si prenda ad esempio una macchina virtuale di dimensioni A2-Standard dotata di due dischi dati aggiuntivi con capacità massima di 4 TB ciascuno. La tabella seguente indica i dati effettivi archiviati in ciascuno di tali dischi:

| Tipo di disco | Dimensioni massime | Dati effettivi presenti |
| --------- | -------- | ----------- |
| Disco del sistema operativo |4095 GB |17 GB |
| Disco locale/disco temporaneo |135 GB |5 GB (non incluso per il backup) |
| Disco dati 1 |4095 GB |30 GB |
| Disco dati 2 |4095 GB |0 GB |

In questo caso, le dimensioni effettive della macchina virtuale sono 17 GB + 30 GB + 0 GB = 47 GB. La dimensione dell'istanza protetta (47 GB) diventa la base della fattura mensile. Con l'aumentare della quantità di dati presente nella macchina virtuale, le dimensioni dell'istanza protetta usata per la fatturazione cambiano di conseguenza.

La fatturazione inizia solo dopo il completamento corretto del primo backup. A questo punto inizierà la fatturazione sia per le istanze di archiviazione sia per quelle protette. La fatturazione continua fino a quando per la macchina virtuale sono presenti dati di backup archiviati in un insieme di credenziali. Se si interrompe la protezione sulla macchina virtuale ma in un insieme di credenziali continuano a essere presenti dati di backup di tale macchina virtuale, la fatturazione continua.

La fatturazione relativa a una macchina virtuale specifica viene interrotta solo se viene interrotta la protezione e vengono eliminati i dati di backup. Quando si interrompe la protezione e non vi sono processi di backup attivi, la dimensione dell'ultimo backup della macchina virtuale completato correttamente diventa la dimensione dell'Istanza protetta utilizzata per la fatturazione mensile.

## <a name="questions"></a>Domande?
In caso di domande o se si vuole che venga inclusa una funzionalità, è possibile [inviare commenti e suggerimenti](https://aka.ms/azurebackup_feedback).

## <a name="next-steps"></a>Passaggi successivi
* [Eseguire il backup di macchine virtuali](backup-azure-arm-vms.md)
* [Gestire i backup delle macchine virtuali](backup-azure-manage-vms.md)
* [Ripristino di macchine virtuali](backup-azure-arm-restore-vms.md)
* [Risolvere i problemi relativi al backup delle macchine virtuali di Azure](backup-azure-vms-troubleshoot.md)
