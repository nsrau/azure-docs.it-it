---
title: Ottimizzare le prestazioni nelle macchine virtuali della serie Lsv2 di Azure-archiviazione | Microsoft Docs
description: Informazioni su come ottimizzare le prestazioni per la soluzione nelle macchine virtuali della serie Lsv2.
services: virtual-machines-windows
author: laurenhughes
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 5728afe8195a8f25e5aafcb815b0c61558b32547
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101795"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Ottimizzare le prestazioni nelle macchine virtuali della serie Lsv2

Le macchine virtuali serie Lsv2 supportano un'ampia gamma di carichi di lavoro che necessitano di un elevato livello di I/O e velocità effettiva nell'archiviazione locale in un'ampia gamma di applicazioni e settori.  La serie Lsv2 è ideale per Big data, database SQL, NoSQL, data warehousing e database transazionali di grandi dimensioni, tra cui Cassandra, MongoDB, Cloudera e Redis.

La progettazione delle macchine virtuali (VM) della serie Lsv2 ottimizza il processore AMD EPYC™ 7551 per fornire le prestazioni migliori tra il processore, la memoria, i dispositivi NVMe e le macchine virtuali. Oltre a ottimizzare le prestazioni dell'hardware, le macchine virtuali della serie Lsv2 sono progettate per funzionare con le esigenze dei sistemi operativi Windows e Linux per ottenere prestazioni migliori con l'hardware e il software.

L'ottimizzazione del software e dell'hardware ha comportato la versione ottimizzata di [Windows Server 2019 datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview), rilasciata nel primo dicembre 2018 ad Azure Marketplace, che supporta le prestazioni massime sui dispositivi NVMe nelle VM serie Lsv2.

Questo articolo fornisce suggerimenti e suggerimenti per garantire che i carichi di lavoro e le applicazioni raggiungano le prestazioni massime progettate nelle VM. Le informazioni contenute in questa pagina verranno aggiornate continuamente quando si aggiungono altre immagini ottimizzate per Lsv2 ad Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Architettura del chipset AMD EYPC™

Le macchine virtuali della serie Lsv2 usano processori AMD EYPC™ server basati sulla microarchitettura Zen. AMD ha sviluppato un'infrastruttura Infinity (IF) per EYPC™ come interconnessione scalabile per il modello NUMA, che può essere usato per le comunicazioni in tempo, su pacchetto e su più pacchetti. Rispetto a QPI (Quick-Path Interconnection) e UPI (Interconnection ultra-path) usati nei processori di Intel monolitici a matrice moderna, l'architettura di Small-die di AMD many-NUMA può comportare vantaggi a livello di prestazioni e problemi. L'effetto effettivo dei vincoli di larghezza di banda e latenza della memoria può variare a seconda del tipo di carico di lavoro in esecuzione.

## <a name="tips-for-maximizing-performance"></a>Suggerimenti per ottimizzare le prestazioni

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

* **È necessario apportare modifiche di polling in Windows in Windows Server 2012 o Windows Server 2016?**  
   Il polling di NVMe è disponibile solo in Windows Server 2019 in Azure.  

* **È possibile tornare a un modello di routine del servizio di interrupt tradizionale (ISR)?**  
   Le macchine virtuali della serie Lsv2 sono ottimizzate per il polling NVMe. Gli aggiornamenti vengono forniti continuamente per migliorare le prestazioni di polling.

* **È possibile modificare le impostazioni di polling in Windows Server 2019?**  
   Le impostazioni di polling non sono regolabili dall'utente.
   
## <a name="next-steps"></a>Passaggi successivi

* Vedere le specifiche per tutte le [macchine virtuali ottimizzate per le prestazioni di archiviazione](sizes-storage.md) in Azure
