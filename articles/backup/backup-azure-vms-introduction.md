---
title: Informazioni sul backup di macchine virtuali di Azure
description: Informazioni sul backup di macchine virtuali di Azure e su alcune procedure consigliate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 12/11/2018
ms.author: raynew
ms.openlocfilehash: 9a80671a72f059e24a8cebc5de803af9261ad829
ms.sourcegitcommit: 21466e845ceab74aff3ebfd541e020e0313e43d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53743953"
---
# <a name="about-azure-vm-backup"></a>Informazioni sul backup di macchine virtuali di Azure

Questo articolo descrive come il [servizio Backup di Azure](backup-introduction-to-azure-backup.md) esegue il backup di macchine virtuali di Azure.

## <a name="backup-process"></a>Processo di backup

Ecco come il servizio Backup di Azure esegue un backup per le macchine virtuali di Azure.

1. Per le macchine virtuali di Azure selezionate per il backup, il servizio Backup di Azure avvia un processo di backup in base alla pianificazione specificata.
2. Il servizio attiva l'estensione per il backup.
    - Le macchine virtuali Windows usano l'estensione _VMSnapshot_.
    - Le macchine virtuali Linux usano l'estensione _VMSnapshotLinux_.
    - L'estensione viene installata al primo backup delle macchine virtuali.
    - Per installare l'estensione, è necessario che la macchina virtuale sia in esecuzione.
    - Se la macchina virtuale non è in esecuzione, il servizio Backup crea uno snapshot dell'archivio sottostante (poiché non si verifica alcuna scrittura di applicazione durante l'arresto della macchina virtuale).
4. L'estensione per il backup crea uno snapshot coerente con le app a livello di archiviazione.
5. Dopo la creazione dello snapshot, i dati vengono trasferiti nell'insieme di credenziali. Per offrire la massima efficienza, il servizio identifica e trasferisce solo i blocchi di dati che sono stati modificati dall'ultimo backup, ovvero i dati differenziali.
5. Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino.

![Architettura del backup delle macchine virtuali di Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="data-encryption"></a>Crittografia dei dati

Il servizio Backup di Azure non esegue la crittografia dei dati come parte del processo di backup. Backup di Azure supporta il backup di macchine virtuali di Azure crittografate tramite Crittografia dischi di Azure.

- Il backup di macchine virtuali crittografate solo con BEK (Bitlocker Encryption Key) e con BEK e KEK (Key Encryption Key) è supportato per le macchine virtuali di Azure gestite e non gestite.
- I dati BEK (segreti) e KEK (chiavi) sottoposti a backup vengono crittografati in modo da poter essere letti e usati solo quando ripristinati nell'insieme di credenziali delle chiavi dagli utenti autorizzati.
- Poiché anche i dati BEK vengono sottoposti a backup, nei casi in cui i questi vengono persi, gli utenti autorizzati possono ripristinarli nell'insieme di credenziali delle chiavi e recuperare la macchina virtuale crittografata. Le chiavi e i segreti delle macchine virtuali crittografate vengono sottoposti a backup in formato crittografato. In questo modo, né Azure né gli utenti non autorizzati possono leggere o usare le chiavi e i segreti sottoposti a backup. Solo gli utenti con il livello di autorizzazioni corretto possono eseguire il backup e il ripristino di macchine virtuali crittografate e anche di chiavi e segreti.

## <a name="snapshot-consistency"></a>Coerenza degli snapshot

Per acquisire snapshot mentre le app sono in esecuzione, Backup di Azure crea snapshot coerenti con le app.

- **Macchine virtuali Windows**: per le macchine virtuali Windows, il servizio Backup si coordina con il Servizio Copia Shadow del volume (VSS) per ottenere uno snapshot coerente dei dischi delle macchine virtuali.
    - Per impostazione predefinita, Backup di Azure esegue backup VSS completi. [Altre informazioni](http://blogs.technet.com/b/filecab/archive/2008/05/21/what-is-the-difference-between-vss-full-backup-and-vss-copy-backup-in-windows-server-2008.aspx)
    - Se si vuole modificare l'impostazione in modo che Backup di Azure esegua backup di copia VSS, impostare la chiave del Registro di sistema seguente:
        ```
        [HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\BCDRAGENT]
        ""USEVSSCOPYBACKUP"="TRUE"
        ```
- **Macchine virtuali Linux**: per assicurarsi che le macchine virtuali Linux siano coerenti con le app quando Backup di Azure crea uno snapshot, è possibile usare il framework di script di pre-backup e post-backup Linux. Per garantire la coerenza quando si crea uno snapshot delle macchine virtuali è possibile scrivere script personalizzati.
    -  Backup di Azure richiama solo script di pre-backup e post-backup scritti dal cliente.
    - Se gli script di pre-backup e post-backup vengono eseguiti correttamente, Backup di Azure contrassegna il punto di ripristino come coerente con le applicazioni. È tuttavia il cliente a essere responsabile della coerenza con le applicazioni quando usa script personalizzati.
    - [Altre informazioni](backup-azure-linux-app-consistent.md) sulla configurazione degli script.


#### <a name="consistency-types"></a>Tipi di coerenza

La tabella seguente illustra i vari tipi di coerenza.

**Snapshot** | **Basato su VSS** | **Dettagli** | **Ripristino**
--- | --- | --- | ---
**Coerenza con le applicazioni** | Sì (solo Windows) | Il backup coerenti con le app acquisiscono contenuto in memoria e operazioni di I/O in sospeso. Gli snapshot coerenti con le app usano writer VSS (o script di pre/post-backup per Linux) che assicurano la coerenza dei dati delle app prima dell'esecuzione di un backup. | Quando si esegue il ripristino con uno snapshot coerente con le app, la macchina virtuale viene avviata. Non si verificano problemi di perdita o danneggiamento dei dati. Le app vengono avviate in uno stato coerente.
**Coerenza con il file system** | Sì (solo Windows) |  I backup coerenti con i file forniscono backup coerenti dei file su disco creando uno snapshot di tutti i file nello stesso momento.<br/><br/> I punti di ripristino di Backup di Azure sono coerenti con i file per:<br/><br/> - Backup di macchine virtuali Linux che non hanno script di pre/post-backup o in cui lo script non è riuscito.<br/><br/> - Backup di macchine virtuali Windows in cui VSS non è riuscito. | Quando si esegue il ripristino con uno snapshot coerente con i file, la macchina virtuale viene avviata. Non si verificano problemi di perdita o danneggiamento dei dati. Le app devono implementare uno specifico meccanismo di correzione per assicurarsi che i dati ripristinati siano coerenti.
**Coerenza con l'arresto anomalo del sistema** | No  | Un backup coerente con l'arresto anomalo del sistema si verifica spesso quando una macchina virtuale di Azure si arresta in fase di backup.  Solo i dati già esistenti sul disco al momento del backup vengono acquisiti e sottoposti a backup.<br/><br/> Un punto di ripristino coerente con l'arresto anomalo del sistema non garantisce la coerenza dei dati per il sistema operativo o l'app. | Anche se non vi sono garanzie, la macchina virtuale viene avviata e viene eseguito un controllo del disco per correggere eventuali errori di danneggiamento. Le operazioni di scrittura o i dati in memoria che non sono stati trasferiti nel disco vengono persi. Le app implementano una propria procedura di verifica dei dati. Per un'app di database, ad esempio, se un log delle transazioni contiene voci non presenti nel database, il software del database esegue un rollback finché i dati non sono coerenti.


## <a name="service-and-subscription-limits"></a>Limiti del servizio e della sottoscrizione

Backup di Azure presenta alcuni limiti per le sottoscrizioni e gli insiemi di credenziali.

[!INCLUDE [azure-backup-limits](../../includes/azure-backup-limits.md)]


## <a name="backup-performance"></a>Prestazioni del backup

### <a name="disk-considerations"></a>Considerazioni sui dischi

Il processo di backup prova a completare il backup al più presto, utilizzando il maggior numero possibile di risorse.

- Nel tentativo di raggiungere la massima velocità, il processo di backup prova a eseguire il backup di ciascun disco della macchina virtuale in parallelo.
- Se ad esempio una macchina virtuale ha quattro dischi, il servizio prova a eseguire il backup di tutti e quattro i dischi in parallelo.
- Il numero di dischi di cui viene eseguito il backup è il fattore più importante che determina il traffico di backup di un account di archiviazione.
- Tutte le operazioni di I/O sono tuttavia limitate dalla *velocità effettiva da raggiungere per BLOB singolo* che ha un limite di 60 MB al secondo.
- Per ogni disco di cui si esegue il backup, il servizio Backup di Azure legge i blocchi nel disco e archivia solo i dati modificati (backup incrementale). È possibile usare i valori medi di velocità effettiva elencati di seguito per stimare la quantità di tempo necessaria per il backup di un disco con le dimensioni specificate.

    **Operazione** | **Velocità effettiva ottimale**
    --- | ---
    Backup iniziale | 160 Mbps |
    Backup incrementale | 640 Mbps  <br><br> La velocità effettiva si riduce notevolmente se i dati differenziali si trovano in varie aree del disco.|



### <a name="scheduling-considerations"></a>Considerazioni sulla pianificazione

La pianificazione del backup influisce sulle prestazioni.

- Se si configurano criteri in modo da eseguire il backup di tutte le macchine virtuali allo stesso tempo, si pianifica in realtà un blocco del traffico, poiché il processo di backup prova a eseguire il backup di tutti i dischi in parallelo.
- Per ridurre il traffico di backup, eseguire il backup delle singole macchine virtuali in diverse ore del giorno, senza sovrapposizioni.


### <a name="time-considerations"></a>Considerazioni sul tempo di backup

Anche se la maggior parte del tempo di backup viene impiegata per la lettura e la copia dei dati, altre operazioni influiscono sul tempo totale necessario per il backup di una macchina virtuale:

- **Installazione dell'estensione per il backup**: tempo necessario per installare o aggiornare l'estensione per il backup.
- **Attivazione dello snapshot**: tempo impiegato per attivare uno snapshot. Gli snapshot vengono attivati vicino al momento del backup pianificato.
- **Tempo di attesa in coda**: poiché il servizio Backup elabora processi per più account di archiviazione di clienti allo stesso tempo, i dati dello snapshot potrebbero non essere copiati immediatamente nell'insieme di credenziali di Servizi di ripristino. Nei momenti di picco del carico, potrebbero essere necessarie fino a otto ore prima che i backup vengano elaborati. Tuttavia, il tempo di backup totale della macchina virtuale sarà inferiore a 24 ore per i criteri di backup giornalieri.
- **Backup iniziale**: anche se il tempo totale di backup inferiore a 24 ore è un valore valido per i backup incrementali, potrebbe non esserlo per il primo backup. Il tempo necessario dipende dalla dimensione dei dati e dal momento in cui viene eseguito il backup.
- **Tempo di trasferimento dei dati**: tempo necessario al servizio di backup per calcolare le modifiche incrementali rispetto al backup precedente e trasferire tali modifiche nella risorsa di archiviazione dell'insieme di credenziali.

### <a name="factors-affecting-backup-time"></a>Fattori che influiscono sul tempo di backup

Il backup è costituito da due fasi, la creazione di snapshot e il trasferimento degli snapshot nell'insieme di credenziali. Il servizio di Backup consente di ottimizzare l'archiviazione.

- Quando si trasferiscono i dati dello snapshot all'insieme di credenziali, il servizio trasferisce solo le modifiche incrementali dello snapshot precedente.
- Per determinare le modifiche incrementali, il servizio calcola il checksum dei blocchi.
    - Se viene modificato un blocco, il blocco viene identificato come blocco da inviare all'insieme di credenziali.
    - Il servizio penetra più a fondo in ognuno dei blocchi identificati, alla ricerca di opportunità per ridurre al minimo i dati da trasferire.
    - Dopo aver valutato tutti i blocchi modificati, il servizio unisce le modifiche e le invia all'insieme di credenziali.

Le situazioni che possono influire sul tempo di backup includono:


- **Backup iniziale per un nuovo disco aggiunto in una macchina virtuale già protetta**: se una macchina virtuale è sottoposta a backup incrementale, quando viene aggiunto un nuovo disco il backup potrebbe perdere il contratto di servizio di un giorno, a seconda della dimensione del nuovo disco.
- **App frammentata**: se un'app non è configurata correttamente potrebbe non essere in uno stato ottimale per l'archiviazione:
    - Se lo snapshot contiene molte delle operazioni di scrittura frammentate e di piccole dimensioni, il servizio impiega più tempo nell'elaborazione dei dati scritti dalle applicazioni.
    - Il blocco di scrittura dell'applicazione minimo consigliato per le applicazioni in esecuzione su una macchina virtuale è pari a 8 kB. Se l'applicazione usa un blocco più piccolo degli 8 KB, le prestazioni del backup ne risentono.
- **Account di archiviazione sovraccaricato**: è possibile che sia stato pianificato un backup quando l'app è in esecuzione nell'ambiente di produzione o se più di cinque/dieci dischi sono ospitati dallo stesso account di archiviazione.
- **Modalità Controllo coerenza (CC)**: per i dischi di dimensione maggiore di 1 TB, il backup potrebbe essere eseguito in modalità CC per due motivi:
    - Il disco gestito si sposta come parte del riavvio della macchina virtuale.
    - Alza di livello lo snapshot nel BLOB di base.


## <a name="restore-considerations"></a>Considerazioni sul ripristino

Un'operazione di ripristino comprende due attività principali: la copia dei dati dall'insieme di credenziali all'account di archiviazione selezionato e la creazione della macchina virtuale. Il tempo necessario per copiare i dati dall'insieme di credenziali dipende dalla posizione in cui sono archiviati i backup in Azure e dalla posizione dell'account di archiviazione. Il tempo necessario alla copia dipende dai seguenti fattori:

- **Tempo di attesa in coda**: poiché il servizio elabora processi di ripristino da più account di archiviazione allo stesso tempo, le richieste di ripristino vengono messe in coda.
- **Tempo di copia dei dati**: i dati vengono copiati dall'insieme di credenziali all'account di archiviazione. Il tempo di ripristino dipende dalle operazioni di I/O al secondo e dalla velocità effettiva dell'account di archiviazione selezionato che usa il servizio Backup di Azure. Per ridurre il tempo di copia durante il processo di ripristino, selezionare un account di archiviazione non caricato con altre letture e scritture di applicazione.

## <a name="best-practices"></a>Procedure consigliate

Quando si configurano i backup per le macchine virtuali, è consigliabile seguire queste procedure:

- Non pianificare il backup di più di 100 macchine virtuali per uno stesso insieme di credenziali contemporaneamente.
- Pianificare i backup di macchine Virtuali durante le ore non di punta. In questo modo il servizio Backup usa operazioni di I/O al secondo per il trasferimento dei dati dall'account di archiviazione del cliente all'insieme di credenziali.
- Se si esegue il backup di dischi gestiti, il servizio Backup di Azure esegue la gestione delle risorse di archiviazione. Se si esegue il backup di dischi non gestiti:
    - Assicurarsi di applicare un criterio di backup per macchine virtuali distribuite tra più account di archiviazione.
    - È consigliabile non proteggere con la stessa pianificazione di backup più di 20 dischi di un singolo account di archiviazione.
    - Se un account di archiviazione include più di 20 dischi, suddividere le VM tra più criteri per ottenere i valori di IOPS necessari durante la fase di trasferimento del processo di backup.
    - Non ripristinare una macchina virtuale in esecuzione in una risorsa di archiviazione Premium nello stesso account di archiviazione. Se il processo dell'operazione di ripristino coincide con l'operazione di backup, il valore di IOPS disponibile per il backup sarà ridotto.
    - Per il backup di macchine virtuali Premium nello stack di backup V1, è consigliabile allocare solo il 50% dello spazio totale dell'account di archiviazione, in modo che il servizio Backup possa copiare lo snapshot nell'account di archiviazione e trasferire i dati dall'account di archiviazione all'insieme di credenziali.
- È consigliabile usare account di archiviazione diversi anziché gli stessi account per ripristinare le macchine virtuali da un unico insieme di credenziali. Ciò consente di evitare la limitazione della larghezza di banda della rete e ha come risultato un ripristino completo con buone prestazioni.
- I ripristini dal livello di archiviazione 1 verranno completati in pochi minuti rispetto a quelli dal livello di archiviazione 2 che richiedono alcune ore. Per ripristini più rapidi è consigliabile usare la [funzionalità Instant RP](backup-upgrade-to-vm-backup-stack-v2.md). Questa funzionalità è applicabile solo alle macchine virtuali di Azure gestite.


## <a name="backup-costs"></a>Costi di backup

Per il backup di macchine virtuali di Azure con Backup di Azure vengono applicati i [prezzi di Backup di Azure](https://azure.microsoft.com/pricing/details/backup/).

- La fatturazione inizia solo dopo il completamento corretto del primo backup. A questo punto inizierà la fatturazione sia per le istanze di archiviazione sia per quelle protette.
- La fatturazione continua fino a quando per la macchina virtuale sono presenti dati di backup archiviati in un insieme di credenziali. Se si interrompe la protezione sulla macchina virtuale ma in un insieme di credenziali continuano a essere presenti dati di backup di tale macchina virtuale, la fatturazione continua.
- La fatturazione relativa a una macchina virtuale specifica viene interrotta solo se viene arrestata la protezione e vengono eliminati i dati di backup.
- Quando si interrompe la protezione e non vi sono processi di backup attivi, la dimensione dell'ultimo backup della macchina virtuale completato correttamente diventa la dimensione dell'Istanza protetta utilizzata per la fatturazione mensile.
- Il calcolo delle istanze protette si basa sulla dimensione *effettiva* della macchina virtuale, ovvero sul totale dei dati presenti nella macchina virtuale con esclusione delle risorse di archiviazione temporanee.
- Si basano viceversa sui dati effettivi archiviati nel disco dati.
- I prezzi per il backup delle macchine virtuali non si basano sulla dimensione massima supportata di ogni disco dati collegato alla macchina virtuale.
- Analogamente, la fattura relativa all'archiviazione dei backup è basata sulla quantità di dati archiviata in Backup di Azure, ovvero sul totale dei dati effettivi presenti in ogni punto di ripristino.

Si prenda ad esempio una macchina virtuale di dimensioni A2-Standard dotata di due dischi dati aggiuntivi con capacità massima di 4 TB ciascuno. La tabella seguente indica i dati effettivi archiviati in ciascuno di tali dischi:

| Tipo di disco | Dimensioni massime | Dati effettivi presenti |
| --------- | -------- | ----------- |
| Disco del sistema operativo |4095 GB |17 GB |
| Disco locale/disco temporaneo |135 GB |5 GB (non incluso per il backup) |
| Disco dati 1 |4095 GB |30 GB |
| Disco dati 2 |4095 GB |0 GB |

- In questo caso, la dimensione effettiva della macchina virtuale è 17 GB + 30 GB + 0 GB = 47 GB.
- La dimensione dell'istanza protetta (47 GB) diventa la base della fattura mensile.
- Con l'aumentare della quantità di dati presente nella macchina virtuale, le dimensioni dell'istanza protetta usata per la fatturazione cambiano di conseguenza.


## <a name="next-steps"></a>Passaggi successivi

Dopo aver esaminato il processo di backup e le considerazioni relative alle prestazioni, eseguire le operazioni seguenti:

- Scaricare il [foglio di calcolo di Excel per la pianificazione della capacità](https://gallery.technet.microsoft.com/Azure-Backup-Storage-a46d7e33) per provare i valori della pianificazione per dischi e backup.
- [Informazioni su](../virtual-machines/windows/premium-storage-performance.md) ottimizzare le app per ottenere prestazioni ideali con Archiviazione di Azure. Questo articolo riguarda principalmente l'archiviazione Premium, ma è applicabile anche ai dischi di archiviazione standard.
- [Iniziare a usare](backup-azure-arm-vms-prepare.md) il servizio di backup esaminando le limitazioni e il supporto delle macchine virtuali, creando un insieme di credenziali e preparando le macchine virtuali per il backup.
