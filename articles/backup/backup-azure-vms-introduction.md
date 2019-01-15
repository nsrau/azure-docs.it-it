---
title: Informazioni sul backup di macchine virtuali di Azure
description: Informazioni sul backup di macchine virtuali di Azure e su alcune procedure consigliate.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 01/08/2019
ms.author: raynew
ms.openlocfilehash: cac219414418277ace09ba3a0b442f3bf74e6025
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/08/2019
ms.locfileid: "54107430"
---
# <a name="about-azure-vm-backup"></a>Informazioni sul backup di macchine virtuali di Azure

Questo articolo descrive come il [servizio Backup di Azure](backup-introduction-to-azure-backup.md) esegue il backup di macchine virtuali di Azure.

## <a name="backup-process"></a>Processo di backup

Ecco come il servizio Backup di Azure esegue un backup per le macchine virtuali di Azure.

1. Per le macchine virtuali di Azure selezionate per il backup, il servizio Backup di Azure avvia un processo di backup in base alla pianificazione specificata.
2. Il servizio attiva l'estensione per il backup.
    - Le macchine virtuali Windows usano l'estensione _VMSnapshot_.
    - Le macchine virtuali Linux usano l'estensione _VMSnapshotLinux_.
    - L'estensione viene installata all primo backup delle macchine virtuali.
    - Per installare l'estensione, è necessario che la macchina virtuale sia in esecuzione.
    - Se la macchina virtuale non è in esecuzione, il servizio Backup crea uno snapshot dell'archivio sottostante (poiché non si verifica alcuna scrittura di applicazione durante l'arresto della macchina virtuale).
4. L'estensione per il backup crea uno snapshot coerente con i file e l'arresto anomalo del sistema a livello di archiviazione.
5. Dopo la creazione dello snapshot, i dati vengono trasferiti nell'insieme di credenziali. Per offrire la massima efficienza, il servizio identifica e trasferisce solo i blocchi di dati che sono stati modificati dall'ultimo backup, ovvero i dati differenziali.
5. Quando il trasferimento dei dati è completato, lo snapshot viene rimosso e viene creato un punto di ripristino.

![Architettura del backup delle macchine virtuali di Azure](./media/backup-azure-vms-introduction/vmbackup-architecture.png)

## <a name="data-encryption"></a>Crittografia dei dati

Il servizio Backup di Azure non esegue la crittografia dei dati come parte del processo di backup. Backup di Azure supporta il backup di macchine virtuali di Azure crittografate tramite Crittografia dischi di Azure.

- Il backup di macchine virtuali crittografate solo con BEK (Bitlocker Encryption Key) e con BEK e KEK (Key Encryption Key) è supportato per le macchine virtuali di Azure gestite e non gestite.
- I dati BEK (segreti) e KEK (chiavi) sottoposti a backup vengono crittografati in modo da poter essere letti e usati solo quando ripristinati nell'insieme di credenziali delle chiavi dagli utenti autorizzati.
- Poiché anche i dati BEK vengono sottoposti a backup, nei casi in cui i questi vengono persi, gli utenti autorizzati possono ripristinarli nell'insieme di credenziali delle chiavi e recuperare la macchina virtuale crittografata. Le chiavi e i segreti delle macchine virtuali crittografate vengono sottoposti a backup in formato crittografato. In questo modo, né Azure né gli utenti non autorizzati possono leggere o usare le chiavi e i segreti sottoposti a backup. Solo gli utenti con il livello di autorizzazioni corretto possono eseguire il backup e il ripristino di macchine virtuali crittografate, nonché di chiavi e segreti.

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

Il servizio consente di ottimizzare l'operazione di backup eseguendo il backup in parallelo di ogni disco della macchina virtuale. Se ad esempio una macchina virtuale ha quattro dischi, il servizio prova a eseguire il backup di tutti e quattro i dischi in parallelo. Per ogni disco di cui si esegue il backup, il servizio Backup di Azure legge i blocchi nel disco e archivia solo i dati modificati (backup incrementale).


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

- **Backup iniziale per un nuovo disco aggiunto in una macchina virtuale già protetta**: Se una macchina virtuale è in fase di backup incrementale e un nuovo disco viene aggiunto a questa macchina virtuale, la durata del backup può superare le 24 ore dato che il disco appena aggiunto deve essere sottoposto a replica iniziale insieme alla replica differenziale dei dischi esistenti.
- **Frammentazione**: Il prodotto di backup esegue un'analisi alla ricerca di modifiche incrementali tra due operazioni di backup. Le operazioni di backup sono più veloci quando le modifiche sul disco vengono collocate rispetto a quando le modifiche sono distribuite nel disco. 
- **Varianza**: La varianza giornaliera (per la replica incrementale) di ogni disco superiore a 200 GB può richiedere più di 8 ore per il completamento dell'operazione. Se la macchina virtuale ha più di un disco e uno di questi dischi sta impiegando più tempo per il backup, ciò può compromettere l'operazione complessiva di backup (o può provocare errori). 
- **Modalità di confronto checksum (CC)**: La modalità CC è relativamente più lenta rispetto alla modalità ottimizzata usata da Instant RP. Se Instant RP è già in uso e gli snapshot di livello 1 sono stati eliminati, il backup passa alla modalità CC portando l'operazione di Backup a superare le 24 ore (o ad avere esito negativo).

## <a name="restore-considerations"></a>Considerazioni sul ripristino

Un'operazione di ripristino comprende due attività principali: la copia dei dati dall'insieme di credenziali all'account di archiviazione selezionato e la creazione della macchina virtuale. Il tempo necessario per copiare i dati dall'insieme di credenziali dipende dalla posizione in cui sono archiviati i backup in Azure e dalla posizione dell'account di archiviazione. Il tempo necessario alla copia dipende dalle seguenti attività:

- **Tempo di attesa in coda**: poiché il servizio elabora processi di ripristino da più account di archiviazione allo stesso tempo, le richieste di ripristino vengono messe in coda.
- **Tempo di copia dei dati**: i dati vengono copiati dall'insieme di credenziali all'account di archiviazione. Il tempo di ripristino dipende dalle operazioni di I/O al secondo e dalla velocità effettiva dell'account di archiviazione selezionato che viene usato dal servizio Backup di Azure. Per ridurre il tempo di copia durante il processo di ripristino, selezionare un account di archiviazione non caricato con altre letture e scritture di applicazione.

## <a name="best-practices"></a>Procedure consigliate

Quando si configurano i backup per le macchine virtuali, è consigliabile seguire queste procedure:

- Aggiornare gli insiemi di credenziali a Instant RP. Esaminare questi [vantaggi](backup-upgrade-to-vm-backup-stack-v2.md) e queste [considerazioni](backup-upgrade-to-vm-backup-stack-v2.md#considerations-before-upgrade) e quindi procedere all'aggiornamento seguendo queste [istruzioni](backup-upgrade-to-vm-backup-stack-v2.md#upgrade).  
- È possibile modificare l'orario dei criteri fornito come impostazione predefinita per la creazione degli snapshot al fine di garantire un uso ottimale delle risorse (ad esempio se l'orario predefinito dei criteri è impostato alle 12:00 prendere in considerazione un incremento di alcuni minuti).
- Per il backup di una macchina virtuale Premium senza la funzionalità Instant RP, viene allocato circa il 50% dello spazio totale dell'account di archiviazione. Il servizio Backup richiede questo spazio per copiare lo snapshot nello stesso account di archiviazione e trasferirlo nell'insieme di credenziali.
- Per il ripristino di macchine virtuali da un unico insieme di credenziali è consigliabile usare diversi [account di archiviazione v2](https://docs.microsoft.com/azure/storage/common/storage-account-upgrade) per assicurarsi che non vengano applicate limitazioni all'account di archiviazione di destinazione. Ad esempio ogni macchina virtuale deve avere un account di archiviazione diverso (per il ripristino di 10 macchine virtuali è consigliabile usare 10 account di archiviazione diversi).
- I ripristini dal livello di archiviazione 1 (snapshot) verranno completati in pochi minuti (dato che si tratta dello stesso account di archiviazione) rispetto a quelli dal livello di archiviazione 2 (insieme di credenziali) che possono richiedere alcune ore. È consigliabile usare la funzionalità [Instant RP](backup-upgrade-to-vm-backup-stack-v2.md) per ripristini più rapidi in caso i dati siano disponibili nel livello 1 (se i dati devono essere ripristinati dall'insieme di credenziali, sarà necessario del tempo).
- Il limite sul numero di dischi per account di archiviazione dipende dalla modalità in cui le applicazioni in esecuzione nella macchina virtuale IaaS accedono ai dischi. Verificare se più dischi sono ospitati in un singolo account di archiviazione. Come regola generale, se sono presenti da 5 a 10 o più dischi in un solo account di archiviazione, bilanciare il carico spostando alcuni dischi in account di archiviazione separati.

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

- [Informazioni su](../virtual-machines/windows/premium-storage-performance.md) ottimizzare le app per ottenere prestazioni ideali con Archiviazione di Azure. Questo articolo riguarda principalmente l'archiviazione Premium, ma è applicabile anche ai dischi di archiviazione standard.
- [Iniziare a usare](backup-azure-arm-vms-prepare.md) il servizio di backup esaminando le limitazioni e il supporto delle macchine virtuali, creando un insieme di credenziali e preparando le macchine virtuali per il backup.
