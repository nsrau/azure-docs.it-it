---
title: Spostare il calcolo del mainframe nelle macchine virtuali di AzureMove mainframe compute to Azure Virtual Machines
description: Le risorse di calcolo di Azure vengono confrontate favorevolmente con la capacità del mainframe in modo da poter eseguire la migrazione e modernizzare le applicazioni IBM z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76288932"
---
# <a name="move-mainframe-compute-to-azure"></a>Spostare il calcolo del mainframe in AzureMove mainframe compute to Azure

I mainframe hanno una reputazione per l'elevata affidabilità e disponibilità e continuano ad essere la spina dorsale affidabile di molte aziende. Spesso si pensa che abbiano anche una scalabilità e una potenza di calcolo quasi illimitate. Tuttavia, alcune imprese hanno superato la capacità dei più grandi mainframe disponibili. Se questo suona come te, Azure offre agilità, copertura e risparmio di infrastruttura.

To run mainframe workloads on Microsoft Azure, you need to know how your mainframe's compute capabilities compare to Azure. Basato su un mainframe IBM z14 (il modello più recente a partire da questa scrittura), in questo articolo viene illustrato come ottenere risultati comparabili in Azure.Based on an IBM z14 mainframe (the current model as of this writing), this article tells you how to get comparable results on Azure.

Per iniziare, prendere in considerazione gli ambienti affiancati. Nella figura seguente viene confrontato un ambiente mainframe per l'esecuzione di applicazioni con un ambiente di hosting di Azure.The following figure compares a mainframe environment for running applications to an Azure hosting environment.

![I servizi di Azure e gli ambienti di emulazione offrono un supporto comparabile e semplificano la migrazione](media/mainframe-compute-azure.png)

La potenza dei mainframe viene spesso utilizzata per i sistemi di elaborazione delle transazioni online (OLTP) che gestiscono milioni di aggiornamenti per migliaia di utenti. Queste applicazioni spesso utilizzano software per l'elaborazione delle transazioni, la gestione dello schermo e l'immissione di form. Possono utilizzare un sistema di controllo delle informazioni del cliente (CICS), un sistema iMS (Information Management System) o un pacchetto di interfaccia di transazione (TIP).

Come illustrato nella figura, un emulatore TPM in Azure può gestire carichi di lavoro CICS e IMS. Un emulatore di sistema batch in Azure esegue il ruolo di Job Control Language (JCL). Mainframe data is migrated to Azure databases, such as Azure SQL Database. I servizi di Azure o altro software ospitato in Macchine virtuali di Azure possono essere usati per la gestione del sistema.

## <a name="mainframe-compute-at-a-glance"></a>Il calcolo del mainframe a colpo d'occhio

Nel mainframe z14, i processori sono disposti in un massimo di quattro *cassetti.* Un *cassetto* è semplicemente un cluster di processori e chipset. Ogni cassetto può avere sei chip di processore centrale (CP) attivi e ogni CP ha 10 chip di controller di sistema (SC). Nella terminologia Intel x86 sono presenti sei prese per cassetto, 10 core per socket e quattro cassetti. Questa architettura fornisce l'equivalente approssimativo di 24 socket e 240 core, massimo, per uno z14.

Lo z14 CP veloce ha una velocità di clock di 5,2 GHz. In genere, uno z14 viene consegnato con tutti i CP nella casella. Sono attivati in base alle esigenze. Un cliente viene comunemente addebitato per almeno quattro ore di tempo di calcolo al mese nonostante l'utilizzo effettivo.

Un processore mainframe può essere configurato come uno dei seguenti tipi:

- Processore a scopo generale (GP)
- Sistema z Processore di Informazioni integrato (zIIP)
- Integrata per il processore Linux (IFL)
- Processore di assistenza di sistema (SAP)
- Processore ICF (Integrated Coupling Facility)

## <a name="scaling-mainframe-compute-up-and-out"></a>Calcolo e uscita del mainframe di scalabilitàScaling mainframe compute up and out

I mainframe IBM offrono la possibilità di scalare fino a 240 core (l'attuale dimensione z14 per un singolo sistema). Inoltre, i mainframe IBM possono scalare orizzontalmente attraverso una funzione chiamata Coupling Facility (CF). La CF consente a più sistemi mainframe di accedere contemporaneamente agli stessi dati. Utilizzando la CF, la tecnologia Sysplex parallela mainframe raggruppa i processori mainframe in cluster. Quando è stata scritta questa guida, la funzionalità Sysplex parallela supportava 32 raggruppamenti di 64 processori ciascuno. È possibile raggruppare fino a 2.048 processori in questo modo per aumentare la capacità di calcolo.

Una CF consente ai cluster di calcolo di condividere i dati con accesso diretto. Viene utilizzato per il blocco delle informazioni, delle informazioni della cache e dell'elenco delle risorse di dati condivise. Un Sysplex parallelo che usa una o più CF può essere considerato come un cluster di calcolo con scalabilità orizzontale "condiviso tutto". Per ulteriori informazioni su queste funzionalità, vedere [Parallel Sysplex su IBM](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) sul sito Web IBM.

Le applicazioni possono usare queste funzionalità per fornire prestazioni di scalabilità orizzontale e disponibilità elevata. Per informazioni su come CICS può utilizzare Parallel Sysplex con CF, scaricare il REDbook [IBM CICS e Coupling Facility: Beyond the Basics.](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)

## <a name="azure-compute-at-a-glance"></a>Calcolo di Azure a colpo d'occhioAzure compute at a glance

Alcune persone pensano erroneamente che i server basati su Intel non siano potenti come mainframe. Tuttavia, i nuovi sistemi basati su Intel ad alta densità di nucleo hanno la stessa capacità di calcolo dei mainframe. Questa sezione descrive le opzioni di Azure infrastructure-as-a-service (IaaS) per l'elaborazione e l'archiviazione. Azure offre anche opzioni di platform-as-a-service (PaaS), ma questo articolo è incentrato sulle scelte di IaaS che forniscono una capacità mainframe paragonabile.

Le macchine virtuali di Azure forniscono potenza di calcolo in un intervallo di dimensioni e tipi. In Azure, una CPU virtuale (vCPU) equivale approssimativamente a un core in un mainframe.

Attualmente, l'intervallo di dimensioni delle macchine virtuali di Azure fornisce da 1 a 128 vCPU. I tipi di macchine virtuali (VM) sono ottimizzati per carichi di lavoro specifici. Ad esempio, l'elenco seguente mostra i tipi di macchina virtuale (correnti a partire da questa scrittura) e i relativi utilizzi consigliati:For example, the following list shows the VM types (current as of this writing) and their recommended uses:

| Dimensione     | Tipo e descrizione                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Serie D | Scopo generale con 64 vCPU e velocità di clock fino a 3,5 GHz                           |
| Serie E | Memoria ottimizzata con un massimo di 64 vCPU                                                 |
| Serie F | Calcolo ottimizzato con un massimo di 64 vCPU e velocità di clock a 3,7 GHz                       |
| Serie H | Ottimizzato per applicazioni HPC (High Performance Computing)                          |
| Serie L | Archiviazione ottimizzata per applicazioni ad alta velocità effettiva supportate da database come NoSQLStorage optimized for high-throughput applications backed by databases such as NoSQL |
| Serie M | Macchine virtuali ottimizzate per elaborazione e memoria di maggiore dimensioni con un massimo di 128 vCPU                        |

Per informazioni dettagliate sulle macchine virtuali disponibili, vedere [Serie di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Un mainframe z14 può avere fino a 240 core. Tuttavia, i mainframe z14 non usano quasi mai tutti i core per una singola applicazione o carico di lavoro. Al contrario, un mainframe separa i carichi di lavoro in partizioni logiche (LCR) e gli LCR dispongono di classificazioni, ovvero MIPS (Milioni di istruzioni al secondo) o MSU (Million Service Unit). Quando si determina la dimensione della macchina virtuale confrontabile necessaria per eseguire un carico di lavoro mainframe in Azure, tenere conto della classificazione MIPS (o MSU).

Di seguito sono riportate le stime generali:

-   150 MIPS per vCPU

-   1.000 MIPS per processore

Per determinare le dimensioni corrette della macchina virtuale per un determinato carico di lavoro in un LPAR, ottimizzare innanzitutto la macchina virtuale per il carico di lavoro. Quindi determinare il numero di vCPU necessarie. Una stima conservativa è di 150 MIPS per vCPU. Sulla base di questa stima, ad esempio, una macchina virtuale della serie F con 16 vCPU potrebbe facilmente supportare un carico di lavoro IBM Db2 proveniente da un LPAR con 2.400 MIPS.

## <a name="azure-compute-scale-up"></a>Scalabilità verticale di elaborazione di AzureAzure compute scale-up

Le macchine virtuali della serie M possono scalare fino a 128 vCCU (al momento della stesa di questo articolo). Utilizzando la stima conservativa di 150 MIPS per vCPU, la macchina virtuale della serie M equivale a circa 19.000 MIPS. La regola generale per la stima di MIPS per un mainframe è 1.000 MIPS per processore. Un mainframe z14 può avere fino a 24 processori e fornire circa 24.000 MIPS per un singolo sistema mainframe.

The largest single z14 mainframe has approximately 5,000 MIPS more than the largest VM available in Azure. Tuttavia, è importante confrontare la modalità di distribuzione dei carichi di lavoro. Se un sistema mainframe dispone sia di un'applicazione che di un database relazionale, vengono in genere distribuiti nello stesso mainframe fisico, ognuno nel proprio LPAR. La stessa soluzione in Azure viene spesso distribuita usando una macchina virtuale per l'applicazione e una macchina virtuale separata di dimensioni adeguate per il database.

Ad esempio, se un sistema VCPU M64 supporta l'applicazione e viene utilizzata una vCPU M96 per il database, sono necessari circa 150 vCPU o circa 24.000 MIPS come illustrato nella figura seguente.

![Confronto delle distribuzioni del carico di lavoro di 24.000 MIPS](media/mainframe-compute-mips.png)

L'approccio consiste nell'eseguire la migrazione degli LCR alle singole macchine virtuali. Azure passa facilmente alle dimensioni necessarie per la maggior parte delle applicazioni distribuite in un singolo sistema mainframe.

## <a name="azure-compute-scale-out"></a>Scalabilità orizzontale del calcolo di AzureAzure compute scale-out

Uno dei vantaggi di una soluzione basata su Azure è la possibilità di scalabilità orizzontale. La scalabilità rende disponibile a un'applicazione una capacità di calcolo quasi illimitata. Azure supporta più metodi per la scalabilità orizzontale della potenza di calcolo:Azure supports multiple methods to scale out compute power:

- **Bilanciamento del carico in un cluster.** In questo scenario, un'applicazione può usare un servizio di [bilanciamento del carico](/azure/load-balancer/load-balancer-overview) o un gestore delle risorse per distribuire il carico di lavoro tra più macchine virtuali in un cluster. Se è necessaria una maggiore capacità di calcolo, al cluster vengono aggiunte macchine virtuali aggiuntive.

- **Set di scalabilità di macchine virtuali.** In questo scenario di burst, un'applicazione può essere ridimensionata in risorse di [calcolo](/azure/virtual-machine-scale-sets/overview) aggiuntive in base all'utilizzo della macchina virtuale. Quando la domanda diminuisce, anche il numero di macchine virtuali in un set di scalabilità può scendere, garantendo un utilizzo efficiente della potenza di calcolo.

- **Scala PaaS.** Le offerte di Azure PaaS ridimensionano le risorse di calcolo. Ad esempio, [Azure Service Fabric](/azure/service-fabric/service-fabric-overview) alloca risorse di calcolo per soddisfare gli aumenti del volume di richieste.

- **Kubernetes clusters.** Le applicazioni in Azure possono usare [i cluster Kubernetes](/azure/aks/concepts-clusters-workloads) per i servizi di calcolo per le risorse specificate. Il servizio Azure Kubernetes (AKS) è un servizio gestito che orchestra nodi, pool e cluster Kubernetes in Azure.Azure Kubernetes Service (AKS) is a managed service that orchestra Kubernetes nodes, pools, and clusters on Azure.

Per scegliere il metodo corretto per la scalabilità orizzontale delle risorse di calcolo, è importante comprendere in che differenze tra Azure e mainframe. La chiave è come, o se i dati vengono condivisi dalle risorse di calcolo. In Azure i dati (per impostazione predefinita) non vengono in genere condivisi da più macchine virtuali. Se la condivisione dei dati è richiesta da più macchine virtuali in un cluster di calcolo con scalabilità orizzontale, i dati condivisi devono risiedere in una risorsa che supporta questa funzionalità. In Azure, la condivisione dei dati implica l'archiviazione come illustrato nella sezione seguente.

## <a name="azure-compute-optimization"></a>Ottimizzazione del calcolo di AzureAzure compute optimization

È possibile ottimizzare ogni livello di elaborazione in un'architettura di Azure.You can optimize each tier of processing in an Azure architecture. Usare il tipo di macchine virtuali e le funzionalità più adatte per ogni ambiente. La figura seguente mostra un modello potenziale per la distribuzione di macchine virtuali in Azure per supportare un'applicazione CICS che usa Db2.The following figure shows one potential pattern for deploying VMs in Azure to support a CICS application that uses Db2. Nel sito primario, le macchine virtuali per l'ambiente di produzione, pre-produzione e test sono distribuite con disponibilità elevata. Il sito secondario è destinato al backup e al ripristino di emergenza.

Ogni livello può anche fornire servizi di ripristino di emergenza appropriati. Ad esempio, le macchine virtuali di produzione e dei database possono richiedere il ripristino a caldo, mentre le macchine virtuali di test e sviluppo supportano il ripristino a freddo.

![Distribuzione a disponibilità elevata che supporta il ripristino di emergenza](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Passaggi successivi

- [Migrazione del mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Rehosting del frame principale nelle macchine virtuali di AzureMainframe rehosting on Azure Virtual Machines](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Spostare l'archiviazione mainframe in AzureMove mainframe storage to Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Risorse IBM

- [Sysplex parallelo su IBM](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e lo strumento di accoppiamento: oltre le basi](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Creating required users for a Db2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2) (Creazione degli utenti necessari per l'installazione della funzionalità Db2 pureScale)
- [Db2icrt - Create instance command](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html) (Db2icrt - Comando Crea istanza)
- [Db2 pureScale Soluzione per il database cluster](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Cloud di Microsoft Azure per enti pubblici per le applicazioni mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Altre risorse per la migrazione

- [Data center virtuale di Azure: guida al lift and shift](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) (iSCSI in GlusterFS)
