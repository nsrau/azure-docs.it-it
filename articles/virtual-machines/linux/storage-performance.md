---
title: Ottimizzare le prestazioni nelle macchine virtuali della serie Lsv2 di Azure-archiviazione | Microsoft Docs
description: Informazioni su come ottimizzare le prestazioni per la soluzione nelle macchine virtuali della serie Lsv2.
services: virtual-machines-linux
author: laurenhughes
manager: gwallace
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/05/2019
ms.author: joelpell
ms.openlocfilehash: a06ae79181c70f1cb8519f703cb42a3d699bebf3
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/06/2019
ms.locfileid: "68828390"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Ottimizzare le prestazioni nelle macchine virtuali della serie Lsv2

Le macchine virtuali serie Lsv2 supportano un'ampia gamma di carichi di lavoro che necessitano di un elevato livello di I/O e velocità effettiva nell'archiviazione locale in un'ampia gamma di applicazioni e settori.  La serie Lsv2 è ideale per Big data, database SQL, NoSQL, data warehousing e database transazionali di grandi dimensioni, tra cui Cassandra, MongoDB, Cloudera e Redis.

La progettazione delle macchine virtuali (VM) della serie Lsv2 ottimizza il processore AMD EPYC™ 7551 per fornire le prestazioni migliori tra il processore, la memoria, i dispositivi NVMe e le macchine virtuali. Per lavorare con i partner in Linux, sono disponibili diverse build di Azure Marketplace ottimizzate per le prestazioni della serie Lsv2, che attualmente includono:

- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 8,0
- Debian 9
- Debian 10

Questo articolo fornisce suggerimenti e suggerimenti per garantire che i carichi di lavoro e le applicazioni raggiungano le prestazioni massime progettate nelle VM. Le informazioni contenute in questa pagina verranno aggiornate continuamente quando si aggiungono altre immagini ottimizzate per Lsv2 ad Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Architettura del chipset AMD EYPC™

Le macchine virtuali della serie Lsv2 usano processori AMD EYPC™ server basati sulla microarchitettura Zen. AMD ha sviluppato un'infrastruttura Infinity (IF) per EYPC™ come interconnessione scalabile per il modello NUMA, che può essere usato per le comunicazioni in tempo, su pacchetto e su più pacchetti. Rispetto a QPI (Quick-Path Interconnection) e UPI (Interconnection ultra-path) usati nei processori di Intel monolitici a matrice moderna, l'architettura di Small-die di AMD many-NUMA può comportare vantaggi a livello di prestazioni e problemi. L'effetto effettivo dei vincoli di larghezza di banda e latenza della memoria può variare a seconda del tipo di carico di lavoro in esecuzione.

## <a name="tips-to-maximize-performance"></a>Suggerimenti per ottimizzare le prestazioni

* Se si sta caricando un guest di Linux personalizzato per il carico di lavoro, si noti che la rete accelerata sarà disattivata per impostazione predefinita. Se si intende abilitare la rete accelerata, abilitarla al momento della creazione della macchina virtuale per ottenere prestazioni ottimali.

* L'hardware che alimenta le macchine virtuali della serie Lsv2 usa dispositivi NVMe con otto coppie di coda di I/O (QP) s. Ogni coda di I/O dei dispositivi NVMe è effettivamente una coppia: una coda di invio e una coda di completamento. Il driver NVMe è configurato per ottimizzare l'utilizzo di queste otto query al secondo di I/O distribuendo I/O in una pianificazione round robin. Per ottenere prestazioni massime, eseguire otto processi per dispositivo per la corrispondenza.

* Evitare di combinare I comandi di amministratore di NVMe (ad esempio, la query SMART info di NVMe e così via) con I comandi di I/O di NVMe durante I carichi di lavoro attivi. I dispositivi Lsv2 NVMe sono supportati dalla tecnologia Hyper-V NVMe Direct, che passa alla "modalità lenta" ogni volta che sono in sospeso tutti i comandi di amministratore di NVMe. Se ciò accade, gli utenti di Lsv2 potrebbero vedere un calo significativo delle prestazioni nelle prestazioni di I/O di NVMe.

* Gli utenti di Lsv2 non devono basarsi sulle informazioni NUMA sul dispositivo (tutte 0) segnalate dall'interno della macchina virtuale per le unità dati per stabilire l'affinità NUMA per le proprie app. Il modo consigliato per ottenere prestazioni migliori consiste nel distribuire i carichi di lavoro tra le CPU, se possibile.

* La profondità massima della coda supportata per ogni coppia di coda I/O per il dispositivo NVMe della macchina virtuale Lsv2 è 1024 (rispetto a Limite 32 per Amazon i3. Gli utenti di Lsv2 devono limitare i carichi di lavoro di benchmarking (sintetici) alla profondità della coda 1024 o inferiore per evitare di attivare le condizioni di coda completa, che possono ridurre le prestazioni.

## <a name="utilizing-local-nvme-storage"></a>Utilizzo dell'archiviazione NVMe locale

L'archiviazione locale nel disco NVMe da 1,92 TB in tutte le VM Lsv2 è temporanea. Durante un riavvio standard della macchina virtuale, i dati sul disco NVMe locale rimarranno in modo permanente. I dati non verranno salvati in NVMe se la macchina virtuale viene ridistribuita, deallocata o eliminata. I dati non verranno mantenuti se un altro problema causa la mancata integrità della macchina virtuale o dell'hardware su cui è in esecuzione. In tal caso, tutti i dati nell'host precedente vengono cancellati in modo sicuro.

Si verificano anche casi in cui la macchina virtuale deve essere spostata in un altro computer host, ad esempio durante un'operazione di manutenzione pianificata. Le operazioni di manutenzione pianificata e alcuni errori hardware possono essere previsti con [eventi pianificati](scheduled-events.md). È necessario utilizzare Eventi pianificati per rimanere aggiornati su tutte le operazioni di manutenzione e ripristino previste.

Nel caso in cui un evento di manutenzione pianificata richieda la ricreazione della macchina virtuale in un nuovo host con dischi locali vuoti, è necessario risincronizzare i dati (anche in questo caso, i dati dell'host precedente verranno cancellati in modo protetto). Questo problema si verifica perché le macchine virtuali della serie Lsv2 attualmente non supportano la migrazione in tempo reale nel disco NVMe locale.

Sono disponibili due modalità per la manutenzione pianificata.

### <a name="standard-vm-customer-controlled-maintenance"></a>Manutenzione standard della VM controllata dal cliente

- La macchina virtuale viene spostata in un host aggiornato durante una finestra di 30 giorni.
- I dati di archiviazione locale di Lsv2 potrebbero andare perduti, quindi è consigliabile eseguire il backup dei dati prima dell'evento.

### <a name="automatic-maintenance"></a>Manutenzione automatica

- Si verifica se il cliente non esegue la manutenzione controllata dal cliente o in caso di procedure di emergenza, ad esempio un evento di sicurezza zero-day.
- Progettato per mantenere i dati dei clienti, ma esiste un piccolo rischio di blocco o riavvio di una macchina virtuale.
- I dati di archiviazione locale di Lsv2 potrebbero andare perduti, quindi è consigliabile eseguire il backup dei dati prima dell'evento.

Per tutti gli eventi del servizio imminenti, usare il processo di manutenzione controllato per selezionare un'ora più comoda per l'aggiornamento. Prima dell'evento, è possibile eseguire il backup dei dati in archiviazione Premium. Al termine dell'evento di manutenzione, è possibile restituire i dati alla risorsa di archiviazione di NVMe locale aggiornata delle VM Lsv2.

Gli scenari in cui vengono gestiti i dati nei dischi NVMe locali includono:

- La macchina virtuale è in esecuzione e integra.
- La macchina virtuale viene riavviata sul posto (dall'utente o da Azure).
- La macchina virtuale è sospesa (arrestata senza deallocazione).
- La maggior parte delle operazioni di manutenzione pianificata.

Gli scenari che cancellano in modo sicuro i dati per proteggere il cliente includono:

- La macchina virtuale viene ridistribuita, arrestata (deallocata) o eliminata (dall'utente).
- La VM diventa non integra ed è necessario che il servizio venga riparato in un altro nodo a causa di un problema hardware.
- Un numero ridotto di operazioni di manutenzione pianificate che richiedono la riallocazione della macchina virtuale a un altro host per la manutenzione.

Per altre informazioni sulle opzioni per il backup dei dati nella risorsa di archiviazione locale, vedere [backup e ripristino di emergenza per dischi IaaS di Azure](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Domande frequenti

* **Ricerca per categorie iniziare a distribuire macchine virtuali serie Lsv2?**  
   Analogamente a qualsiasi altra macchina virtuale, usare il [portale](quick-create-portal.md), l'interfaccia della riga di comando di [Azure](quick-create-cli.md)o [PowerShell](quick-create-powershell.md) per creare una macchina virtuale.

* **Si verificherà un errore di un singolo disco NVMe perché tutte le macchine virtuali nell'host avranno esito negativo?**  
   Se viene rilevato un errore del disco sul nodo hardware, l'hardware si trova in stato di errore. Quando si verifica questa situazione, tutte le macchine virtuali nel nodo vengono automaticamente deallocate e spostate in un nodo integro. Per le macchine virtuali della serie Lsv2, questo significa che anche i dati del cliente nel nodo in errore vengono cancellati in modo sicuro e dovranno essere ricreati dal cliente nel nuovo nodo. Come indicato, prima della disponibilità della migrazione in tempo reale in Lsv2, i dati nel nodo in errore verranno spostati in modo proattivo con le VM mentre vengono trasferiti a un altro nodo.

* **È necessario apportare modifiche a rq_affinity per le prestazioni?**  
   Quando si usano le operazioni di input/output massime al secondo (IOPS), l'impostazione rq_affinity è una regolazione secondaria. Quando tutto il resto funziona correttamente, provare a impostare rq_affinity su 0 per verificare se fa una differenza.

* **È necessario modificare le impostazioni di blk_mq?**  
   RHEL/CentOS 7. x usa automaticamente BLK-mq per i dispositivi NVMe. Non sono necessarie modifiche o impostazioni di configurazione. L'impostazione scsi_mod. use _blk_mq è solo per SCSI ed è stata usata durante l'anteprima di Lsv2 perché i dispositivi NVMe erano visibili nelle macchine virtuali guest come dispositivi SCSI. Attualmente, i dispositivi NVMe sono visibili come dispositivi NVMe, quindi l'impostazione SCSI BLK-mq è irrilevante.

* **È necessario modificare "fio"?**  
   Per ottenere il numero massimo di IOPS con uno strumento di misurazione delle prestazioni come ' Fio ' nelle dimensioni delle macchine virtuali L64v2 e L80v2, impostare "rq_affinity" su 0 in ogni dispositivo NVMe.  Questa riga di comando, ad esempio, imposterà "rq_affinity" su zero per tutti i 10 dispositivi NVMe in una macchina virtuale L80v2:

   ```console
   for i in `seq 0 9`; do echo 0 >/sys/block/nvme${i}n1/queue/rq_affinity; done
   ```

   Si noti inoltre che le prestazioni migliori si ottengono quando l'I/O viene eseguito direttamente in ogni dispositivo NVMe non elaborato senza partizionamento, nessun file System, nessuna configurazione RAID 0 e così via. Prima di avviare una sessione di test, assicurarsi che `blkdiscard` la configurazione sia in uno stato di aggiornamento/pulizia noto eseguendo in ogni dispositivo NVMe.
   
## <a name="next-steps"></a>Passaggi successivi

* Vedere le specifiche per tutte le [macchine virtuali ottimizzate per le prestazioni di archiviazione](sizes-storage.md) in Azure
