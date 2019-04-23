---
title: Ottimizzare le prestazioni nelle macchine virtuali di Azure serie Lsv2 - archiviazione | Microsoft Docs
description: Informazioni su come ottimizzare le prestazioni per la soluzione nelle macchine virtuali serie Lsv2.
services: virtual-machines-windows
author: laurenhughes
manager: jeconnoc
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: f84e81a5a9e9c9cf6f477adefa0869d776f7dd71
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014274"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Ottimizzare le prestazioni nelle macchine virtuali serie Lsv2

Le macchine virtuali serie Lsv2 supporta un'ampia gamma di carichi di lavoro che richiedono i/o elevato e la velocità effettiva nell'archiviazione locale in un'ampia gamma di settori e applicazioni.  La serie Lsv2 è ideale per Big Data, SQL, NoSQL database, il data warehousing e grandi database transazionali, inclusi Cassandra, MongoDB, Cloudera e Redis.

La progettazione delle macchine virtuali serie Lsv2 (VM) ottimizza il processore AMD EPYC™ 7551 per offrire le migliori prestazioni tra il processore, memoria, i dispositivi NVMe e le macchine virtuali. Oltre a ottimizzare le prestazioni dell'hardware, le macchine virtuali serie Lsv2 sono progettate per funzionare con le esigenze dei sistemi operativi Windows e Linux per ottenere prestazioni migliori con l'hardware e software.

Ottimizzazione di software e hardware ha comportato la versione ottimizzata di [Windows Server Datacenter 2019](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.WindowsServer?tab=Overview), rilasciata a dicembre dall'inizio del 2018 per Azure Marketplace, che supporta le massime prestazioni nei dispositivi NVMe Lsv2 serie Macchine virtuali.

Questo articolo fornisce suggerimenti e i suggerimenti per assicurarsi che le applicazioni e carichi di lavoro di ottengono le massime prestazioni progettata in macchine virtuali. Le informazioni in questa pagina verranno aggiornate continuamente con l'aggiunta di altre immagini Lsv2 ottimizzato per Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Architettura di chipset AMD EYPC™

Macchine virtuali della serie Lsv2 usano processori server AMD EYPC™ basati la microarchitettura Zen. AMD sviluppato Fabric Infinity (se) per EYPC™ come scalabile per il modello NUMA che può essere usato per le comunicazioni su dado, sul pacchetto e multi-pacchetto di interconnessione. Rispetto a QPI (interconnessione rapida-Path) e per oggetto non pagato (interconnessione Ultra-Path) usato nei processori monolitica dado moderne Intel, architettura piccole tirate AMD molti NUMA potrebbe bring entrambe i vantaggi delle prestazioni, nonché le sfide. L'impatto effettivo di vincoli di larghezza di banda e latenza di memoria può variare a seconda del tipo di carichi di lavoro in esecuzione.

## <a name="tips-for-maximizing-performance"></a>Suggerimenti per ottimizzare le prestazioni

* L'hardware che supporta le macchine virtuali serie Lsv2 Usa dispositivi NVMe con otto Query Processor coppie coda i/o (ha) s. Ogni coda di dispositivo i/o NVMe è effettivamente una coppia: una coda di invio e una coda dei completamenti. Il driver NVMe è configurato per ottimizzare l'utilizzo di questi otto QPs dei / o distribuendo I / O in un round robin pianificazione. Per ottenere prestazioni massime, eseguire otto processi per ogni dispositivo in modo che corrispondano.

* Evitare di combinare NVMe i comandi di amministratore (ad esempio, NVMe SMART query info, e così via) con i comandi dei / o NVMe durante i carichi di lavoro attivi. I dispositivi NVMe Lsv2 sono supportati da Hyper-V NVMe diretto tecnologia, che passa in "modalità lenta" ogni volta che tutti i comandi di amministratore NVMe in sospeso. Gli utenti di Lsv2 potrebbero visualizzare un sostanziale delle prestazioni drop delle prestazioni dei / o NVMe in tal caso.

* Lsv2 consigliabile non basarsi su NUMA le informazioni sul dispositivo (solo 0) segnalati dall'interno della macchina virtuale per le unità dati per stabilire l'affinità NUMA per le proprie app. È consigliabile per migliorare le prestazioni per distribuire i carichi di lavoro tra CPU, se possibile. 

* La profondità massima supportata per ogni coppia di coda i/o per dispositivi NVMe VM Lsv2 è 1024 (Visual Studio. Amazon i3 32 Profondità coda per un limite). Gli utenti Lsv2 consigliabile limitare i carichi di lavoro benchmarking (sintetici) alla profondità della coda 1024 o inferiore per evitare la generazione di condizioni completo della coda, che possono ridurre le prestazioni.

## <a name="utilizing-local-nvme-storage"></a>Utilizzo di archiviazione locale NVMe

L'archiviazione locale sul disco in tutte le VM Lsv2 NVMe $1,92 TB è temporaneo. Durante un riavvio standard ha esito positivo della macchina virtuale, i dati sul disco locale NVMe verranno mantenuti. I dati non verranno mantenute nel NVMe se la macchina virtuale viene ridistribuita, deallocata o eliminata. I dati non verranno conservato se un altro problema fa sì che la macchina virtuale o l'hardware in che cui viene eseguito, per diventare non integro. In questo caso, vengono cancellati in modo sicuro tutti i dati sull'host precedente.

È anche possibile che quando la macchina virtuale deve essere spostato in un computer host diverso, ad esempio, durante un'operazione di manutenzione pianificata. Operazioni di manutenzione pianificata e alcuni errori hardware possono essere anticipati con [gli eventi pianificati](scheduled-events.md). Usare gli eventi pianificati per essere sempre aggiornati su qualsiasi manutenzione prevista e operazioni di ripristino.

Nel caso in cui un evento di manutenzione pianificata richiede alla macchina virtuale di essere ricreato in un nuovo host con dischi locali vuoti, i dati dovrà essere risincronizzata (anche in questo caso, con tutti i dati sull'host precedente che viene cancellato in modo sicuro). Ciò si verifica perché le VM serie Lsv2 attualmente non supportano la migrazione in tempo reale sul disco locale NVMe.

Sono disponibili due modalità per la manutenzione pianificata.

### <a name="standard-vm-customer-controlled-maintenance"></a>Manutenzione controllati dai clienti standard delle macchine Virtuali

- La macchina virtuale viene spostata in un host aggiornato durante un intervallo di 30 giorni.
- I dati di archiviazione locale Lsv2 potrebbero essere persi, pertanto, sono consigliabile effettuare un backup dati prima dell'evento.

### <a name="automatic-maintenance"></a>Manutenzione automatica

- Si verifica se il cliente non viene eseguita manutenzione controllati dal cliente o in caso le procedure di emergenza, ad esempio un evento di tipo zero-day di sicurezza.
- Consente di conservare i dati del cliente, ma manca un rischio minimo di un blocco di macchina virtuale o un riavvio.
- I dati di archiviazione locale Lsv2 potrebbero essere persi, pertanto, sono consigliabile effettuare un backup dati prima dell'evento.

Per qualsiasi evento imminente del servizio, utilizzare il processo di manutenzione controllata per selezionare un'ora più semplice per l'utente per l'aggiornamento. Prima dell'evento, potrebbe eseguire il backup dei dati in archiviazione premium. Dopo aver completato l'evento di manutenzione, è possibile restituire i dati nell'archiviazione NVMe locale le macchine virtuali Lsv2 aggiornato.

Gli scenari che gestiscono i dati nei dischi locali NVMe includono:

- La macchina virtuale è in esecuzione e integro.
- La macchina virtuale viene riavviata posto (dall'utente o da Azure).
- La macchina virtuale sospesa (arrestato senza deallocazione).
- La maggior parte delle operazioni di manutenzione pianificate operazioni di manutenzione.

Scenari di cancellare in modo sicuro i dati per proteggere i clienti includono:

- La macchina virtuale viene ridistribuita, arrestata (deallocata), o eliminata (da parte dell'utente).
- La macchina virtuale diventa non integra e ha al servizio di correzione a un altro nodo a causa di un problema hardware.
- Un numero ridotto di operazioni di manutenzione alla manutenzione pianificata che richiede la macchina virtuale essere riassegnato a un altro host per la manutenzione.

Per altre informazioni sulle opzioni di backup dei dati nell'archiviazione locale, vedere [Backup e ripristino di emergenza per dischi IaaS di Azure](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Domande frequenti

* **Come si inizia a distribuire le VM di serie Lsv2?**  
   Molto, come qualsiasi altra macchina virtuale, usare il [Portal](quick-create-portal.md), [CLI di Azure](quick-create-cli.md), o [PowerShell](quick-create-powershell.md) per creare una macchina virtuale.

* **Errore di un singolo disco NVMe causerà tutte le macchine virtuali nell'host di esito negativo?**  
   Se viene rilevato un errore del disco nel nodo hardware, l'hardware è in stato di errore. In questo caso, tutte le macchine virtuali nel nodo vengono automaticamente DEALLOCATE e spostate in un nodo integro. Per le macchine virtuali serie Lsv2, ciò significa che i dati dei clienti su tale nodo vengono cancellati in modo sicuro e dovranno essere ricreate dal cliente nel nuovo nodo. Come accennato, prima che diventi disponibile in Lsv2 migrazione in tempo reale, i dati nel nodo di failover verranno spostati in modo proattivo con le macchine virtuali durante il trasferimento a un altro nodo.

* **È necessario apportare modifiche di polling in Windows in Windows Server 2012 o Windows Server 2016?**  
   Polling NVMe è disponibile solo in Windows Server 2019 in Azure.  

* **È possibile passare nuovamente interrupt tradizionali routine (ISR) modello di servizio?**  
   Macchine virtuali della serie Lsv2 sono ottimizzate per NVMe polling. Gli aggiornamenti vengono forniti in modo continuativo per migliorare le prestazioni di polling.

* **È possibile modificare le impostazioni di polling in Windows Server 2019?**  
   Le impostazioni di polling non sono utente modificabile.
   
## <a name="next-steps"></a>Passaggi successivi

* Vedere le specifiche per tutte le [macchine virtuali ottimizzate per prestazioni di archiviazione](sizes-storage.md) in Azure
