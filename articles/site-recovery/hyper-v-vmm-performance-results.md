---
title: Eseguire test dei risultati della replica delle macchine virtuali Hyper-V in cloud VMM in un sito secondario con Azure Site Recovery | Microsoft Docs
description: Questo articolo fornisce informazioni sui test delle prestazioni per la replica di macchine virtuali Hyper-V in cloud VMM in un sito secondario tramite Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi
ms.openlocfilehash: a7413b2dcb24a42092eb2af9816b1d29a8306e19
ms.sourcegitcommit: b49431b29a53efaa5b82f9be0f8a714f668c38ab
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68377219"
---
# <a name="test-results-for-hyper-v-replication-to-a-secondary-site"></a>Eseguire test dei risultati della replica Hyper-V in un sito secondario


Questo articolo fornisce i risultati dei test delle prestazioni durante la replica di macchine virtuali Hyper-V in cloud System Center Virtual Machine Manager (VMM) in un data center secondario.

## <a name="test-goals"></a>Obiettivi di test

L'obiettivo del test è analizzare le prestazioni di Site Recovery durante la replica in uno stato stazionario.

- La replica dello stato stazionario si verifica quando le macchine virtuali hanno completato la replica iniziale e sincronizzano le modifiche differenziali.
- È importante misurare le prestazioni usando lo stato stazionario perché è lo stato in cui resta la maggior parte delle macchine virtuali a meno che non si verifichino interruzioni impreviste.
- La distribuzione di test è costituita da due siti locali con un server VMM in ciascuno di essi. Questa distribuzione di test è tipica di una distribuzione sede centrale/filiale, con la sede centrale che funge da sito primario e la filiale che funge da sito secondario o di ripristino.

## <a name="what-we-did"></a>Passaggi eseguiti

Passaggi necessari per il superamento del test:

1. Creare macchine virtuali tramite i modelli VMM.
2. Avviare le macchine virtuali e acquisire le metriche delle prestazioni di acquisizione dei dati di base per 12 ore.
3. Creare i cloud nei server VMM primario e di ripristino.
4. Configurare la replica in Site Recovery, incluso il mapping tra cloud di origine e di ripristino.
5. Abilitare la protezione per le macchine virtuali e consentire il completamento della replica iniziale.
6. Attendere un paio d'ore per la stabilizzazione del sistema.
7. Acquisire le metriche delle prestazioni per 12 ore. Le macchine virtuali sono rimaste in uno stato di replica previsto per queste 12 ore.
8. Misurare il differenziale tra le metriche delle prestazioni di base e le metriche delle prestazioni di replica.


## <a name="primary-server-performance"></a>Prestazioni del server primario

* Replica Hyper-V, usata da Site Recovery, tiene traccia in modo asincrono delle modifiche apportate a un file di log con sovraccarico di archiviazione minimo nel server primario.
* Replica Hyper-V usa la cache in memoria autonoma per ridurre il sovraccarico di IOPS per la traccia. Archivia scritture nel disco VHDX in memoria e le scarica nel file di log prima dell'invio del log al sito di ripristino del log. Lo scaricamento del disco avviene anche se le scritture raggiungono un limite predeterminato.
* Il grafico seguente mostra il sovraccarico di IOPS nello stato stazionario per la replica. Si può notare che il sovraccarico di operazioni di I/O al secondo dovuto alla replica è pari al 5% circa, quindi abbastanza basso.

  ![Risultati sito primario](./media/hyper-v-vmm-performance-results/IC744913.png)

Replica Hyper-V usa la memoria nel server primario per ottimizzare le prestazioni del disco. Come illustrato nel grafico seguente, il sovraccarico della memoria in tutti i server del cluster primario è marginale. Il sovraccarico della memoria mostrato è la percentuale di memoria usata dalla replica rispetto alla memoria totale installata sul server Hyper-V.

![Risultati sito primario](./media/hyper-v-vmm-performance-results/IC744914.png)

Replica Hyper-V ha un sovraccarico di CPU minimo. Come illustrato nel grafico, il sovraccarico della replica è tra il 2 e il 3%.

![Risultati sito primario](./media/hyper-v-vmm-performance-results/IC744915.png)

## <a name="secondary-server-performance"></a>Prestazioni del server secondario

Replica Hyper-V usa una piccola quantità di memoria nel server di ripristino per ottimizzare il numero di operazioni di archiviazione. Il grafico riepiloga l'utilizzo della memoria nel server di ripristino. Il sovraccarico della memoria mostrato è la percentuale di memoria usata dalla replica rispetto alla memoria totale installata sul server Hyper-V.

![Risultati sito secondario](./media/hyper-v-vmm-performance-results/IC744916.png)

La quantità di operazioni di I/O nel sito di ripristino è una funzione del numero di operazioni di scrittura nel sito primario. Ora verranno esaminate le operazioni di I/O totali sul sito di ripristino rispetto alle operazioni di I/O totali e le operazioni di scrittura nel sito primario. I grafici mostrano che il totale di IOPS nel sito di ripristino è

* Circa 1,5 volte la scrittura di IOPS sul database primario.
* Circa il 37% del totale di IOPS nel sito primario.

![Risultati sito secondario](./media/hyper-v-vmm-performance-results/IC744917.png)

![Risultati sito secondario](./media/hyper-v-vmm-performance-results/IC744918.png)

## <a name="effect-on-network-utilization"></a>Risultati dell'uso di rete

Una media di 275 MB al secondo di larghezza di banda è stata usata tra i nodi primario e di ripristino (con compressione abilitata) su una larghezza di banda esistente di 5 GB al secondo.

![Risultati utilizzo rete](./media/hyper-v-vmm-performance-results/IC744919.png)

## <a name="effect-on-vm-performance"></a>Effetto sulle prestazioni della macchina virtuale

Una considerazione importante è l'impatto della replica sui carichi di lavoro di produzione in esecuzione nelle macchine virtuali. Se viene eseguito il provisioning adeguato del sito primario per la replica, l’impatto sui carichi di lavoro dovrebbe essere nullo. Il meccanismo di traccia a basso utilizzo di risorse di Replica Hyper-V assicura che i carichi di lavoro in esecuzione nelle macchine virtuali non subiscano ripercussioni durante la replica nello stato stazionario. Tale condizione è illustrata nei grafici seguenti.

Questo grafico mostra le operazioni di I/O al secondo eseguite dalle macchine virtuali con carichi di lavoro diversi prima e dopo l'abilitazione della replica. È possibile osservare che non esiste alcuna differenza tra i due.

![Risultati effetto replica](./media/hyper-v-vmm-performance-results/IC744920.png)

Il grafico seguente mostra la velocità effettiva della macchine virtuali con carichi di lavoro diversi prima e dopo l'abilitazione della replica. È possibile osservare che la replica non ha alcun impatto significativo.

![Risultati effetti replica](./media/hyper-v-vmm-performance-results/IC744921.png)

## <a name="conclusion"></a>Conclusione

I risultati mostrano chiaramente che Site Recovery, grazie a Replica Hyper-V, si adatta bene con un sovraccarico minimo per un cluster di grandi dimensioni. Site Recovery consente di eseguire in modo semplice distribuzione, replica, gestione e monitoraggio. Replica Hyper-V fornisce l'infrastruttura necessaria per la corretta scalabilità della replica. 

## <a name="test-environment-details"></a>Ambiente di test nel dettaglio

### <a name="primary-site"></a>Sito primario

* Il sito primario include un cluster contenente cinque server Hyper-V con 470 macchine virtuali.
* Le macchine virtuali eseguono diversi carichi di lavoro e per tutte la protezione di Site Recovery è abilitata.
* L’archiviazione per il nodo del cluster è fornita da una rete SAN iSCSI. Modello – Hitachi HUS130.
* Ogni server del cluster ha quattro schede di rete (NIC) di 1 Gbps ciascuna.
* Due delle schede di rete sono connesse a una rete privata iSCSI e due sono connesse a una rete aziendale esterna. Una delle reti esterne è riservata alle comunicazioni del cluster.

![Requisiti hardware principali](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Modello | Processore | Numero di processori | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Server Hyper-V nel cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 ha 256 |Dell ™ PowerEdge ™ R820 |CPU Intel(R) Xeon(R) E5-4620 0 \@ 2,20 GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + ruolo Hyper-V |
| Server VMM |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="secondary-site"></a>Sito secondario

* Il sito secondario ha un cluster di failover a sei nodi
* L’archiviazione per il nodo del cluster è fornita da una rete SAN iSCSI. Modello – Hitachi HUS130.

![Specifiche hardware principali](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Modello | Processore | Numero di processori | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Server Hyper-V nel cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |CPU Intel(R) Xeon(R) E5-2630 0 \@ 2,30 GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + ruolo Hyper-V |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |CPU Intel(R) Xeon(R) E5-4620 0 \@ 2,20 GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + ruolo Hyper-V |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |CPU Intel(R) Xeon(R) E5-4620 0 \@ 2,20 GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + ruolo Hyper-V |
| Server VMM |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### <a name="server-workloads"></a>Carichi di lavoro server

* A scopo di test sono stati scelti i carichi di lavoro comunemente utilizzati negli scenari aziendali dei clienti.
* Si utilizza [IOMeter](http://www.iometer.org) con le caratteristiche del carico di lavoro riepilogate in tabella per la simulazione.
* Tutti i profili IOMeter sono impostati per scrivere byte casuali per simulare modelli  di scrittura dei casi peggiori per i carichi di lavoro.

| Carico di lavoro | Dimensioni I/O (KB) | % accesso | % lettura | I/O in sospeso | Modello I/O |
| --- | --- | --- | --- | --- | --- |
| File server |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |Tutti 100% casuale |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% casuale<br />100% sequenziale |
| Exchange |32 |100% |67% |8 |100% casuale |
| Workstation/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Entrambi 100% casuale |
| File Server Web |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |Tutti 75% casuale |

### <a name="vm-configuration"></a>Configurazione della macchina virtuale

* 470 macchine virtuali nel cluster primario.
* Tutte le macchine virtuali con disco VHDX.
* Macchine virtuali con carichi di lavoro in esecuzione riepilogati nella tabella. Tutti sono stati creati con i modelli VMM.

| Carico di lavoro | N. di macchine virtuali | RAM minima (GB) | RAM massima (GB) | Dimensioni disco logico (GB) per macchina virtuale | Numero massimo di IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| File Server |50 |1 |2 |552 |22 |
| VDI |149 |0,5 |1 |80 |6 |
| Server Web |149 |0,5 |1 |80 |6 |
| TOTALE |470 | | |96,83 TB |4108 |

### <a name="site-recovery-settings"></a>Impostazioni di Site Recovery

* Site Recovery è stato configurato per la protezione da locale a locale
* Il server VMM ha quattro cloud configurati contenenti i server del cluster Hyper-V e le macchine virtuali.

| Cloud VMM primario | Macchine virtuali protette | Frequenza di replica | Punti di ripristino aggiuntivi |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 min |Nessuna |
| PrimaryCloudRpo30s |47 |30 secondi |Nessuna |
| PrimaryCloudRpo30sArp1 |47 |30 secondi |1 |
| PrimaryCloudRpo5m |235 |5 min |Nessuna |

### <a name="performance-metrics"></a>Metriche delle prestazioni

Nella tabella vengono riepilogate le metriche delle prestazioni e i contatori misurati nella distribuzione.

| Metrica | Contatore |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Available memory |\Memory\Available MBytes |
| IOPS |\PhysicalDisk(_Total)\Disk Transfers/sec |
| VM read (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Read Operations/Sec |
| VM write (IOPS) operations/sec |\Hyper-V Virtual Storage Device(\<VHD>)\Write Operations/S |
| VM read throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Read Bytes/sec |
| VM write throughput |\Hyper-V Virtual Storage Device(\<VHD>)\Write Bytes/sec |

## Next steps

[Set up replication](hyper-v-vmm-disaster-recovery.md)\Processor(_Total)\` Processor Timeation of Hyper-V VMs in VMM clouds to a secondary site with Azure Site Recovery | Microsoft Docs
description: This article provides information about performance testing for replication of Hyper-V VMs in VMM clouds to a secondary site using Azure Site Recovery.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: sutalasi

---
# Test results for Hyper-V replication to a secondary site


This article provides the results of performance testing when replicating Hyper-V VMs in System Center Virtual Machine Manager (VMM) clouds, to a secondary datacenter.

## Test goals

The goal of testing was to examine how Site Recovery performs during steady state replication.

- Steady state replication occurs when VMs have completed initial replication, and are synchronizing delta changes.
- It’s important to measure performance using steady state, because it’s the state in which most VMs remain, unless unexpected outages occur.
- The test deployment consisted of two on-premises sites, with a VMM server in each site. This test deployment is typical of a head office/branch office deployment, with head office acting as the primary site, and the branch office as the secondary or recovery site.

## What we did

Here's what we did in the test pass:

1. Created VMs using VMM templates.
2. Started VMs, and captured baseline performance metrics over 12 hours.
3. Created clouds on the primary and recovery VMM servers.
4. Configured replication in Site Recovery, including mapping between source and recovery clouds.
5. Enabled protection for VMs, and allowed them to complete initial replication.
6. Waited a couple of hours for system stabilization.
7. Captured performance metrics over 12 hours, where all VMs remained in an expected replication state for those 12 hours.
8. Measured the delta between the baseline performance metrics, and the replication performance metrics.


## Primary server performance

* Hyper-V Replica (used by Site Recovery) asynchronously tracks changes to a log file, with minimum storage overhead on the primary server.
* Hyper-V Replica utilizes self-maintained memory cache to minimize IOPS overhead for tracking. It stores writes to the VHDX in memory, and flushes them into the log file before the time that the log is sent to the recovery site. A disk flush also happens if the writes hit a predetermined limit.
* The graph below shows the steady state IOPS overhead for replication. We can see that the IOPS overhead due to replication is around 5%, which is quite low.

  ![Primary results](./media/hyper-v-vmm-performance-results/IC744913.png)

Hyper-V Replica uses memory on the primary server, to optimize disk performance. As shown in the following graph, memory overhead on all servers in the primary cluster is marginal. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Primary results](./media/hyper-v-vmm-performance-results/IC744914.png)

Hyper-V Replica has minimum CPU overhead. As shown in the graph, replication overhead is in the range of 2-3%.

![Primary results](./media/hyper-v-vmm-performance-results/IC744915.png)

## Secondary server performance

Hyper-V Replica uses a small amount of memory on the recovery server, to optimize the number of storage operations. The graph summarizes the memory usage on the recovery server. The memory overhead shown is the percentage of memory used by replication, compared to the total installed memory on the Hyper-V server.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744916.png)

The amount of I/O operations on the recovery site is a function of the number of write operations on the primary site. Let’s look at the total I/O operations on the recovery site in comparison with the total I/O operations and write operations on the primary site. The graphs show that the total IOPS on the recovery site is

* Around 1.5 times the write IOPS on the primary.
* Around 37% of the total IOPS on the primary site.

![Secondary results](./media/hyper-v-vmm-performance-results/IC744917.png)

![Secondary results](./media/hyper-v-vmm-performance-results/IC744918.png)

## Effect on network utilization

An average of 275 Mb per second of network bandwidth was used between the primary and recovery nodes (with compression enabled), against an existing bandwidth of 5 Gb per second.

![Results network utilization](./media/hyper-v-vmm-performance-results/IC744919.png)

## Effect on VM performance

An important consideration is the impact of replication on production workloads running on the virtual machines. If the primary site is adequately provisioned for replication, there shouldn’t be any impact on the workloads. Hyper-V Replica’s lightweight tracking mechanism ensures that workloads running in the virtual machines are not impacted during steady-state replication. This is illustrated in the following graphs.

This graph shows IOPS performed by virtual machines running different workloads, before and after replication was enabled. You can observe that there is no difference between the two.

![Replica effect results](./media/hyper-v-vmm-performance-results/IC744920.png)

The following graph shows the throughput of virtual machines running different workloads, before and after replication was enabled. You can observe that replication has no significant impact.

![Results replica effects](./media/hyper-v-vmm-performance-results/IC744921.png)

## Conclusion

The results clearly show that Site Recovery, coupled with Hyper-V Replica, scales well with minimum overhead for a large cluster. Site Recovery provides simple deployment, replication, management and monitoring. Hyper-V Replica provides the necessary infrastructure for successful replication scaling. 

## Test environment details

### Primary site

* The primary site has a cluster containing five Hyper-V servers, running 470 virtual machines.
* The VMs run different workloads, and all have Site Recovery protection enabled.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.
* Each cluster server has four network cards (NICs) of one Gbps each.
* Two of the network cards are connected to an iSCSI private network, and two are connected to an external enterprise network. One of the external networks is reserved for cluster communications only.

![Primary hardware requirements](./media/hyper-v-vmm-performance-results/IC744922.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST11<br />ESTLAB-HOST12<br />ESTLAB-HOST13<br />ESTLAB-HOST14<br />ESTLAB-HOST25 |128<br />ESTLAB-HOST25 has 256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Secondary site

* The secondary site has a six-node failover cluster.
* Storage for the cluster node is provided by an iSCSI SAN. Model – Hitachi HUS130.

![Primary hardware specification](./media/hyper-v-vmm-performance-results/IC744923.png)

| Server | RAM | Model | Processor | Number of processors | NIC | Software |
| --- | --- | --- | --- | --- | --- | --- |
| Hyper-V servers in cluster: <br />ESTLAB-HOST07<br />ESTLAB-HOST08<br />ESTLAB-HOST09<br />ESTLAB-HOST10 |96 |Dell ™ PowerEdge ™ R720 |Intel(R) Xeon(R) CPU E5-2630 0 \@ 2.30GHz |2 |I Gbps x 4 |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST17 |128 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |4 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| ESTLAB-HOST24 |256 |Dell ™ PowerEdge ™ R820 |Intel(R) Xeon(R) CPU E5-4620 0 \@ 2.20GHz |2 | |Windows Server Datacenter 2012 R2 (x64) + Hyper-V role |
| VMM Server |2 | | |2 |1 Gbps |Windows Server Database 2012 R2 (x64) + VMM 2012 R2 |

### Server workloads

* For test purposes we picked workloads commonly used in enterprise customer scenarios.
* We use [IOMeter](http://www.iometer.org) with the workload characteristic summarized in the table for simulation.
* All IOMeter profiles are set to write random bytes to simulate worst-case write patterns for workloads.

| Workload | I/O size (KB) | % Access | %Read | Outstanding I/Os | I/O pattern |
| --- | --- | --- | --- | --- | --- |
| File Server |4<br />8<br />16<br />32<br />64 |60%<br />20%<br />5%<br />5%<br />10% |80%<br />80%<br />80%<br />80%<br />80% |8<br />8<br />8<br />8<br />8 |All 100% random |
| SQL Server (volume 1)<br />SQL Server (volume 2) |8<br />64 |100%<br />100% |70%<br />0% |8<br />8 |100% random<br />100% sequential |
| Exchange |32 |100% |67% |8 |100% random |
| Workstation/VDI |4<br />64 |66%<br />34% |70%<br />95% |1<br />1 |Both 100% random |
| Web File Server |4<br />8<br />64 |33%<br />34%<br />33% |95%<br />95%<br />95% |8<br />8<br />8 |All 75% random |

### VM configuration

* 470 VMs on the primary cluster.
* All VMs with VHDX disk.
* VMs running workloads summarized in the table. All were created with VMM templates.

| Workload | # VMs | Minimum RAM (GB) | Maximum RAM (GB) | Logical disk size (GB) per VM | Maximum IOPS |
| --- | --- | --- | --- | --- | --- |
| SQL Server |51 |1 |4 |167 |10 |
| Exchange Server |71 |1 |4 |552 |10 |
| File Server |50 |1 |2 |552 |22 |
| VDI |149 |.5 |1 |80 |6 |
| Web server |149 |.5 |1 |80 |6 |
| TOTAL |470 | | |96.83 TB |4108 |

### Site Recovery settings

* Site Recovery was configured for on-premises to on-premises protection
* The VMM server has four clouds configured, containing the Hyper-V cluster servers and their VMs.

| Primary VMM cloud | Protected VMs | Replication frequency | Additional recovery points |
| --- | --- | --- | --- |
| PrimaryCloudRpo15m |142 |15 mins |None |
| PrimaryCloudRpo30s |47 |30 secs |None |
| PrimaryCloudRpo30sArp1 |47 |30 secs |1 |
| PrimaryCloudRpo5m |235 |5 mins |None |

### Performance metrics

The table summarizes the performance metrics and counters that were measured in the deployment.

| Metric | Counter |
| --- | --- |
| CPU |\Processor(_Total)\% Processor Time |
| Memoria disponibile |\Memoria\MByte disponibili |
| Input/output al secondo |\DiscoFisico(_Totale)\Trasferimenti disco/secondo |
| Operazioni di lettura VM (IOPS) al secondo |\Dispositivo dispositivo di archiviazione virtuale\<(VHD >) \Byte letti/sec |
| Operazioni di scrittura VM (IOPS) al secondo |Dispositivo di archiviazione virtuale \dispositivo\<(VHD >) \Byte scritti operazioni/sec |
| Velocità effettiva lettura VM |Dispositivo di archiviazione virtuale \dispositivo\<(> VHD) \Byte letti/sec |
| Velocità effettiva di scrittura VM |Dispositivo di archiviazione virtuale \dispositivo\<(> VHD) \Byte scritti/sec |

## <a name="next-steps"></a>Passaggi successivi

[Configurare la replica](hyper-v-vmm-disaster-recovery.md)
