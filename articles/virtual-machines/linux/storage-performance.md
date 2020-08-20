---
title: Ottimizzare le prestazioni nelle macchine virtuali della serie Lsv2 in Azure - Archiviazione
description: Di seguito viene descritto come ottimizzare le prestazioni per la propria soluzione nelle macchine virtuali della serie Lsv2.
services: virtual-machines-linux
author: laurenhughes
ms.service: virtual-machines-linux
ms-subservice: sizes
ms.topic: conceptual
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: cd9e539e01e8e11d866186552ab3b8dde7e03f91
ms.sourcegitcommit: 271601d3eeeb9422e36353d32d57bd6e331f4d7b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88654770"
---
# <a name="optimize-performance-on-the-lsv2-series-linux-virtual-machines"></a>Ottimizzare le prestazioni nelle macchine virtuali Linux della serie Lsv2

Le macchine virtuali della serie Lsv2 supportano un'ampia gamma di carichi di lavoro che necessitano di un elevato livello di I/O e velocità effettiva nell'archiviazione locale in un'ampia gamma di applicazioni e settori.  La serie Lsv2 è ideale per i database Big Data, SQL, NoSQL, data warehousing e database transazionali di grandi dimensioni, tra cui Cassandra, MongoDB, Cloudera e Redis.

La progettazione delle macchine virtuali (VM) della serie Lsv2 ottimizza il processore AMD EPYC™ 7551 per offrire le prestazioni migliori tra il processore, la memoria, i dispositivi NVMe e le macchine virtuali. Per lavorare con i partner in Linux, sono disponibili diverse build di Azure Marketplace ottimizzate per le prestazioni della serie Lsv2, che attualmente includono:

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8,0
- Debian 9
- Debian 10

Questo articolo offre consigli e suggerimenti per garantire che i carichi di lavoro e le applicazioni raggiungano le prestazioni massime progettate nelle macchine virtuali. Le informazioni contenute in questa pagina verranno aggiornate continuamente quando si aggiungono altre immagini ottimizzate per Lsv2 ad Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Architettura del chipset AMD EYPC™

Le macchine virtuali della serie Lsv2 usano processori server AMD EYPC™ basati sulla microarchitettura Zen. AMD ha sviluppato Infinity Fabric (IF) per EYPC™ come interconnessione scalabile per il modello NUMA, che può essere usato per le comunicazioni su die, su pacchetto e su più pacchetti. Rispetto a QPI (Quick-Path Interconnection) e UPI (Ultra-Path Interconnect) usati nei moderni processori Intel con die monolitico, l'architettura con piccoli die e molti NUMA di AMD può comportare sia vantaggi in termini di prestazioni che sfide. L'impatto effettivo dei vincoli di larghezza di banda della memoria e latenza può variare a seconda del tipo di carico di lavoro in esecuzione.

## <a name="tips-to-maximize-performance"></a>Suggerimenti per ottimizzare le prestazioni

* Se si sta caricando un guest di Linux personalizzato per il carico di lavoro, si noti che la rete accelerata sarà **disattivata** per impostazione predefinita. Se si intende abilitare la rete accelerata, abilitarla al momento della creazione della macchina virtuale per ottenere prestazioni ottimali.

* L'hardware che alimenta le macchine virtuali della serie Lsv2 usa dispositivi NVMe con otto coppie di code (QP) di I/O. Ogni coda di I/O del dispositivo NVMe è effettivamente una coppia: una coda di invio e una coda di completamento. Il driver NVMe è configurato per ottimizzare l'uso di queste otto coppie di code di I/O distribuendo I/O in una pianificazione round robin. Per ottenere prestazioni massime, eseguire otto processi per dispositivo per la corrispondenza.

* Evitare di combinare i comandi di amministratore di NVMe (ad esempio, query di informazioni di NVMe SMART e così via) con i comandi di I/O di NVMe durante I carichi di lavoro attivi. I dispositivi NVMe Lsv2 sono supportati dalla tecnologia Hyper-V NVMe Direct, che passa alla "modalità lenta" ogni volta che sono in sospeso tutti i comandi di amministratore di NVMe. Se ciò accade, gli utenti di Lsv2 potrebbero assistere a un calo significativo delle prestazioni I/O di NVMe.

* Gli utenti di Lsv2 non devono basarsi sulle informazioni NUMA del dispositivo (pari a 0) segnalate dall'interno della macchina virtuale per le unità dati per stabilire l'affinità NUMA per le proprie app. Per ottenere prestazioni migliori è consigliabile distribuire i carichi di lavoro tra le CPU, se possibile.

* La profondità massima della coda supportata per ogni coppia di coda di I/O per il dispositivo NVMe della macchina virtuale Lsv2 è 1024 (rispetto a un limite di 32 per Amazon i3). Gli utenti di Lsv2 devono limitare i carichi di lavoro di benchmarking (sintetico) a una profondità della coda di 1024 o inferiore per evitare di attivare le condizioni complete della coda, che possono ridurre le prestazioni.

## <a name="utilizing-local-nvme-storage"></a>Uso dell'archiviazione NVMe locale

L'archiviazione locale nel disco NVMe da 1,92 TB in tutte le macchine virtuali Lsv2 è temporanea. Durante un riavvio standard della macchina virtuale con esito positivo, i dati sul disco NVMe locale vengono mantenuti. I dati non verranno mantenuti nei dispositivi NVMe se la macchina virtuale viene ridistribuita, deallocata o eliminata. I dati non verranno mantenuti se la macchina virtuale o l'hardware su cui è in esecuzione risulteranno non integri a causa di altri problemi. In tal caso, tutti i dati nell'host precedente vengono cancellati in modo sicuro.

In alcuni casi, ad esempio durante un'operazione di manutenzione pianificata, la macchina virtuale deve essere spostata in un computer host diverso. Le operazioni di manutenzione pianificata e alcuni errori hardware possono essere previsti con [Eventi pianificati](scheduled-events.md). È necessario usare gli eventi pianificati per rimanere aggiornati su eventuali operazioni di manutenzione e ripristino previste.

Nel caso in cui un evento di manutenzione pianificata richieda la ricreazione della macchina virtuale in un nuovo host con dischi locali vuoti, è necessario risincronizzare i dati (anche in questo caso, i dati dell'host precedente verranno cancellati in modo sicuro). Ciò si verifica perché le macchine virtuali della serie Lsv2 non supportano attualmente la migrazione in tempo reale sul disco NVMe locale.

Sono disponibili due modalità per la manutenzione pianificata.

### <a name="standard-vm-customer-controlled-maintenance"></a>Manutenzione standard controllata dal cliente della macchina virtuale

- La macchina virtuale viene spostata in un host aggiornato durante un periodo di 30 giorni.
- I dati di archiviazione locale di Lsv2 potrebbero andare perduti, quindi è consigliabile eseguire il backup dei dati prima dell'evento.

### <a name="automatic-maintenance"></a>Manutenzione automatica

- Si verifica se il cliente non esegue la manutenzione controllata dal cliente o in caso di procedure di emergenza, ad esempio un evento di sicurezza zero-day.
- Progettata per mantenere i dati dei clienti, presenta tuttavia un piccolo rischio di blocco o riavvio di una macchina virtuale.
- I dati di archiviazione locale di Lsv2 potrebbero andare perduti, quindi è consigliabile eseguire il backup dei dati prima dell'evento.

Per eventuali eventi del servizio imminenti, usare il processo di manutenzione controllata per selezionare un orario più comodo per l'aggiornamento. Prima dell'evento, è possibile eseguire il backup dei dati in uno spazio di archiviazione Premium. Al termine dell'evento di manutenzione, è possibile restituire i dati alla risorsa di archiviazione NVMe locale aggiornata delle macchine virtuali Lsv2.

Ecco alcuni scenari in cui i dati sui dischi NVMe locali vengono mantenuti:

- La macchina virtuale è integra e in esecuzione.
- La macchina virtuale viene riavviata sul posto (dall'utente o da Azure).
- La macchina virtuale viene sospesa (arrestata senza deallocazione).
- La maggior parte delle operazioni di manutenzione pianificate.

Gli scenari che cancellano in modo sicuro i dati per proteggere il cliente includono:

- La macchina virtuale viene ridistribuita, arrestata (deallocata) o eliminata (dall'utente).
- La macchina virtuale diventa non integra ed è necessario correggere il servizio in un altro nodo a causa di un problema hardware.
- Un numero ridotto di operazioni di manutenzione pianificate che richiedono la riallocazione della macchina virtuale a un altro host per la manutenzione.

Per altre informazioni sulle opzioni di backup dei dati nell'archiviazione locale, vedere [Backup e ripristino di emergenza per dischi IaaS di Azure](../backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Domande frequenti

* **Come iniziare a distribuire le macchine virtuali della serie Lsv2?**  
   Come qualsiasi altra macchina virtuale, usare il [Portale](quick-create-portal.md), l'[interfaccia della riga di comando di Azure](quick-create-cli.md) o [PowerShell](quick-create-powershell.md) per creare una macchina virtuale.

* **Un singolo errore del disco NVMe causerà eventuali errori in tutte le macchine virtuali nell'host?**  
   Se viene rilevato un errore del disco sul nodo hardware, quest'ultimo risulterà in stato di errore. In questo caso, tutte le macchine virtuali nel nodo vengono automaticamente deallocate e spostate in un nodo integro. Per le macchine virtuali della serie Lsv2, ciò significa che anche i dati del cliente nel nodo in cui si è verificato l'errore vengono cancellati in modo sicuro e dovranno essere ricreati dal cliente nel nuovo nodo. Come già indicato, prima che la migrazione in tempo reale diventi disponibile in Lsv2, i dati nel nodo in cui si è verificato l'errore verranno spostati in modo proattivo con le macchine virtuali quando vengono trasferite in un altro nodo.

* **È necessario apportare modifiche a rq_affinity per le prestazioni?**  
   L'impostazione rq_affinity è una lieve regolazione quando si usa il numero massimo assoluto di operazioni di input/output al secondo (IOPS). Quando tutto il resto funziona correttamente, provare a impostare rq_affinity su 0 per verificare se fa una differenza.

* **È necessario modificare le impostazioni di blk_mq?**  
   RHEL/CentOS 7. x usa automaticamente BLK-mq per i dispositivi NVMe. Non sono necessarie modifiche o impostazioni di configurazione. L'impostazione scsi_mod. use_blk_mq è solo per SCSI ed è stata usata durante l'anteprima di Lsv2 perché i dispositivi NVMe sono visibili nelle macchine virtuali guest come dispositivi SCSI. Attualmente, i dispositivi NVMe sono visibili come dispositivi NVMe, quindi l'impostazione SCSI BLK-mq è irrilevante.

* **È necessario modificare "fio"?**  
   Per ottenere il numero massimo di IOPS con uno strumento di misurazione delle prestazioni come ' Fio ' nelle dimensioni delle macchine virtuali L64v2 e L80v2, impostare "rq_affinity" su 0 in ogni dispositivo NVMe.  Questa riga di comando, ad esempio, imposterà "rq_affinity" su zero per tutti i 10 dispositivi NVMe in una macchina virtuale L80v2:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Si noti inoltre che le prestazioni migliori si ottengono quando l'I/O viene eseguito direttamente in ogni dispositivo NVMe non elaborato senza partizionamento, nessun file System, nessuna configurazione RAID 0 e così via. Prima di avviare una sessione di test, assicurarsi che la configurazione sia in uno stato di aggiornamento/pulizia noto eseguendo `blkdiscard` in ogni dispositivo NVMe.
   
## <a name="next-steps"></a>Passaggi successivi

* Vedere le specifiche per tutte le [macchine virtuali ottimizzate per le prestazioni di archiviazione](../sizes-storage.md) in Azure
