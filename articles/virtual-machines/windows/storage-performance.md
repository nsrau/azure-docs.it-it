---
title: Ottimizzare le prestazioni nelle macchine virtuali della serie Lsv2 di Azure - ArchiviazioneOptimize performance on Azure Lsv2-series virtual machines - Storage
description: Informazioni su come ottimizzare le prestazioni per la soluzione nelle macchine virtuali della serie Lsv2.
services: virtual-machines-windows
author: sasha-melamed
manager: gwallace
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/17/2019
ms.author: joelpell
ms.openlocfilehash: 57b248908a02327f2521be05920259681a26817a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77920230"
---
# <a name="optimize-performance-on-the-lsv2-series-virtual-machines"></a>Ottimizzare le prestazioni nelle macchine virtuali della serie Lsv2Optimize performance on the Lsv2-series virtual machines

Le macchine virtuali della serie Lsv2 supportano un'ampia gamma di carichi di lavoro che richiedono un'elevata velocità di i/o e velocità effettiva nell'archiviazione locale in un'ampia gamma di applicazioni e settori.  La serie Lsv2 è ideale per I database Big Data, SQL, NoSQL, data warehousing e database transazionali di grandi dimensioni, tra cui Cassandra, MongoDB, Cloudera e Redis.

La progettazione delle macchine virtuali (VM) della serie Lsv2 massimizza il processore AMD EPYC™ 7551 per offrire le migliori prestazioni tra il processore, la memoria, i dispositivi NVMe e le macchine virtuali. Oltre a massimizzare le prestazioni hardware, le macchine virtuali serie Lsv2 sono progettate per funzionare con le esigenze dei sistemi operativi Windows e Linux per migliorare le prestazioni con l'hardware e il software.

L'ottimizzazione del software e dell'hardware ha portato alla versione ottimizzata di [Windows Server 2019 Datacenter,](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)rilasciata all'inizio di dicembre 2018 in Azure Marketplace, che supporta le massime prestazioni sui dispositivi NVMe nelle macchine virtuali serie Lsv2.

Questo articolo fornisce suggerimenti e suggerimenti per garantire che i carichi di lavoro e le applicazioni raggiungano le massime prestazioni progettate nelle macchine virtuali. Le informazioni in questa pagina verranno continuamente aggiornate man mano che vengono aggiunte più immagini ottimizzate lsv2 ad Azure Marketplace.

## <a name="amd-eypc-chipset-architecture"></a>Architettura del chipset ™ AMD EYPC

Le macchine virtuali della serie Lsv2 utilizzano processori server AMD EYPC™ basati sulla microarchitettura zen. AMD ha sviluppato Infinity Fabric (IF) per EYPC™ come interconnessione scalabile per il suo modello NUMA che potrebbe essere utilizzato per le comunicazioni on-die, on-package e multi-package. Rispetto a QPI (Quick-Path Interconnect) e UPI (Ultra-Path Interconnect) utilizzati su processori intel monolitici-die, l'architettura a molti-NUMA small-die di AMD può portare sia vantaggi in termini di prestazioni che sfide. L'impatto effettivo dei vincoli di larghezza di banda e latenza della memoria può variare a seconda del tipo di carichi di lavoro in esecuzione.

## <a name="tips-for-maximizing-performance"></a>Suggerimenti per ottimizzare le prestazioni

* L'hardware che alimenta le macchine virtuali della serie Lsv2 utilizza dispositivi NVMe con otto QP (I/O Queue Pair). Ogni coda di I/O del dispositivo NVMe è in realtà una coppia: una coda di invio e una coda di completamento. Il driver NVMe è configurato per ottimizzare l'utilizzo di questi otto QP I/O distribuendo gli I/O in base a un programma round robin. Per ottenere prestazioni massime, eseguire otto processi per dispositivo per la corrispondenza.

* Evitare di combinare i comandi di amministrazione NVMe (ad esempio, query di informazioni NVMe SMART e così via) con i comandi NVMe I/O durante i carichi di lavoro attivi. I dispositivi Lsv2 NVMe sono supportati dalla tecnologia VVMe Direct hyper-V, che passa alla "modalità lenta" ogni volta che sono in sospeso comandi di amministrazione NVMe. Gli utenti di Lsv2 potrebbero vedere un drastico calo delle prestazioni di NVMe I/O se ciò accade.

* Gli utenti di Lsv2 non devono basarsi sulle informazioni NUMA del dispositivo (tutte 0) segnalate dall'interno della macchina virtuale per le unità dati per decidere l'affinità NUMA per le proprie app. Il modo consigliato per migliorare le prestazioni consiste nel distribuire i carichi di lavoro tra le CPU, se possibile. 

* La profondità massima supportata dalla coda per coppia di i/o per il dispositivo NVMe VM Lsv2 è 1024 (rispetto al limite di Amazon i3 QD 32). Gli utenti di Lsv2 devono limitare i carichi di lavoro di benchmarking (sintetico) alla profondità 1024 o inferiore per evitare di attivare le condizioni complete della coda, riducendo le prestazioni.

## <a name="utilizing-local-nvme-storage"></a>Utilizzo dello storage NVMe locale

L'archiviazione locale sul disco NVMe da 1,92 TB in tutte le macchine virtuali Lsv2 è effimera. Durante un riavvio standard della macchina virtuale, i dati sul disco NVMe locale verranno mantenuti. I dati non verranno mantenuti in NVMe se la macchina virtuale viene ridistribuita, deallocata o eliminata. I dati non verranno mantenuti se un altro problema causa la malintegrità della macchina virtuale o dell'hardware in cui è in esecuzione. In questo caso, tutti i dati nell'host precedente vengono cancellati in modo sicuro.

In alcuni casi, ad esempio, durante un'operazione di manutenzione pianificata, la macchina virtuale deve essere spostata in un computer host diverso. Le operazioni di manutenzione pianificate e alcuni errori hardware possono essere anticipati con [Eventi pianificati](scheduled-events.md). Gli eventi pianificati devono essere utilizzati per rimanere aggiornati su eventuali operazioni di manutenzione e ripristino previste.

Nel caso in cui un evento di manutenzione pianificata richieda che la macchina virtuale venga ricreata in un nuovo host con dischi locali vuoti, i dati dovranno essere risincronizzati (ancora una volta, con tutti i dati nell'host precedente cancellati in modo sicuro). Ciò si verifica perché le macchine virtuali serie Lsv2 non supportano attualmente la migrazione in tempo reale sul disco NVMe locale.

Ci sono due modalità per la manutenzione pianificata.

### <a name="standard-vm-customer-controlled-maintenance"></a>Manutenzione standard della macchina virtuale controllata dal cliente

- La macchina virtuale viene spostata in un host aggiornato durante una finestra di 30 giorni.
- I dati di archiviazione locale DiSv2 potrebbero andare persi, pertanto è consigliabile eseguire il backup dei dati prima dell'evento.

### <a name="automatic-maintenance"></a>Manutenzione automatica

- Si verifica se il cliente non esegue la manutenzione controllata dal cliente o in caso di procedure di emergenza, ad esempio un evento di sicurezza zero-day.
- Destinato a conservare i dati dei clienti, ma c'è un piccolo rischio di blocco o riavvio di una macchina virtuale.
- I dati di archiviazione locale DiSv2 potrebbero andare persi, pertanto è consigliabile eseguire il backup dei dati prima dell'evento.

Per eventuali eventi di servizio imminenti, utilizzare il processo di manutenzione controllata per selezionare un tempo più conveniente per l'aggiornamento. Prima dell'evento, è possibile eseguire il backup dei dati in uno spazio di archiviazione premium. Al termine dell'evento di manutenzione, è possibile restituire i dati all'archiviazione NVMe locale delle macchine virtuali Lsv2 aggiornata.

Gli scenari che gestiscono i dati sui dischi NVMe locali includono:Scenarios that maintain data on local NVMe disks include:

- La macchina virtuale è in esecuzione e integro.
- La macchina virtuale viene riavviata sul posto (dall'utente o da Azure).
- La macchina virtuale viene sospesa (arrestata senza deallocazione).
- La maggior parte delle operazioni di manutenzione pianificate.

Gli scenari che cancellano in modo sicuro i dati per proteggere il cliente includono:Scenarios that secure ly era il data to protect the customer include:

- La macchina virtuale viene ridistribuita, arrestata (deallocata) o eliminata (dall'utente).
- La macchina virtuale diventa non integra e deve essere gestita la manutenzione per la manutenzione a un altro nodo a causa di un problema hardware.
- Un numero ridotto di operazioni di manutenzione pianificate che richiede il riallocamento della macchina virtuale a un altro host per la manutenzione.

Per altre informazioni sulle opzioni per il backup dei dati nell'archiviazione locale, vedere Backup e ripristino di emergenza per i [dischi di Azure IaaS](backup-and-disaster-recovery-for-azure-iaas-disks.md).

## <a name="frequently-asked-questions"></a>Domande frequenti

* **Come si inizia a distribuire le macchine virtuali della serie Lsv2?**  
   Analogamente a qualsiasi altra macchina virtuale, usare [il portale,](quick-create-portal.md) [l'interfaccia della riga di comando](quick-create-cli.md)di Azure o [PowerShell](quick-create-powershell.md) per creare una macchina virtuale.

* **Un singolo errore del disco NVMe causerà un errore di tutte le macchine virtuali nell'host?**  
   Se viene rilevato un errore del disco nel nodo hardware, l'hardware è in stato di errore. In questo caso, tutte le macchine virtuali nel nodo vengono automaticamente deallocate e spostate in un nodo integro. Per le macchine virtuali della serie Lsv2, ciò significa che anche i dati del cliente nel nodo in errore vengono cancellati in modo sicuro e dovranno essere ricreati dal cliente nel nuovo nodo. Come indicato, prima che la migrazione in tempo reale diventi disponibile in Lsv2, i dati nel nodo in cui si è verificato l'errore verranno spostati in modo proattivo con le macchine virtuali quando vengono trasferite in un altro nodo.

* **È necessario apportare modifiche di polling in Windows in Windows Server 2012 o Windows Server 2016?**  
   NVMe polling is only available on Windows Server 2019 on Azure.  

* **È possibile tornare a un modello ISR (Interrupt Service Routine) tradizionale?**  
   I VM della serie Lsv2 sono ottimizzati per il polling NVMe. Gli aggiornamenti vengono forniti continuamente per migliorare le prestazioni di polling.

* **È possibile regolare le impostazioni di polling in Windows Server 2019?**  
   Le impostazioni di polling non sono regolabili dall'utente.
   
## <a name="next-steps"></a>Passaggi successivi

* Vedere le specifiche per tutte le macchine virtuali ottimizzate per le prestazioni di archiviazione in AzureSee specifications for all [VMs optimized for storage performance](sizes-storage.md) on Azure
