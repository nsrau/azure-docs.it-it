---
title: Spostare il calcolo mainframe in macchine virtuali di Azure
description: Le risorse di calcolo di Azure si confrontano a seconda della capacità del mainframe per poter eseguire la migrazione e modernizzare le applicazioni IBM Z14.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 97f354d0a313d58c671366dd0e5f485504823e13
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76288932"
---
# <a name="move-mainframe-compute-to-azure"></a>Spostare il calcolo mainframe in Azure

I mainframe hanno una reputazione per affidabilità e disponibilità elevate e continuano a essere il backbone attendibile di molte aziende. Spesso si ritiene che la scalabilità e la potenza di elaborazione siano quasi illimitate. Tuttavia, alcune aziende hanno una capacità superiore a quella dei più grandi mainframe disponibili. In questo modo, Azure offre flessibilità, copertura e risparmio sull'infrastruttura.

Per eseguire carichi di lavoro mainframe in Microsoft Azure, è necessario capire come vengono confrontate le funzionalità di calcolo del mainframe con Azure. In base a un mainframe IBM Z14 (il modello più recente al momento della stesura di questo documento), questo articolo spiega come ottenere risultati confrontabili in Azure.

Per iniziare, considerare gli ambienti side-by-side. Nella figura seguente viene confrontato un ambiente mainframe per l'esecuzione di applicazioni in un ambiente di hosting di Azure.

![I servizi di Azure e gli ambienti di emulazione offrono supporto analogo e semplificano la migrazione](media/mainframe-compute-azure.png)

La potenza dei mainframe è spesso utilizzata per sistemi OLTP (Online Transaction Processing) che gestiscono milioni di aggiornamenti per migliaia di utenti. Queste applicazioni utilizzano spesso il software per l'elaborazione delle transazioni, la gestione dello schermo e la voce del modulo. Possono usare un sistema di controllo delle informazioni sul cliente (CICS), un sistema di gestione delle informazioni (IMS) o un pacchetto dell'interfaccia di transazione (TIP).

Come illustrato nella figura, un emulatore TPM in Azure è in grado di gestire i carichi di lavoro CICS e IMS. Un emulatore di sistema batch in Azure esegue il ruolo di JCL (Job Control Language). Viene eseguita la migrazione dei dati mainframe ai database di Azure, ad esempio il database SQL di Azure. I servizi di Azure o altri software ospitati in macchine virtuali di Azure possono essere usati per la gestione del sistema.

## <a name="mainframe-compute-at-a-glance"></a>Calcolo immediato del mainframe

Nel mainframe Z14, i processori sono disposti in un massimo di quattro *cassetti*. Un *cassetto* è semplicemente un cluster di processori e chipset. Ogni cassetto può avere sei chip del processore centrale attivo (CP) e ogni CP ha 10 chip del controller di sistema (SC). Nella terminologia di Intel x86 sono presenti sei socket per cassetto, 10 core per socket e quattro cassetti. Questa architettura fornisce l'equivalente approssimativa di 24 socket e 240 core, Maximum, per un Z14.

Il CP Z14 veloce ha una velocità di clock di 5,2 GHz. In genere, un Z14 viene recapitato con tutti i CPs nella casella. Vengono attivati in base alle esigenze. Un cliente viene generalmente addebitato per almeno quattro ore di tempo di calcolo al mese nonostante l'utilizzo effettivo.

Un processore mainframe può essere configurato come uno dei seguenti tipi:

- Processore per utilizzo generico (GP)
- System z Integrated Information Processor (zIIP)
- Processore IFL (Integrated Facility for Linux)
- System Assist Processor (SAP)
- Processore di funzione di accoppiamento integrato (ICF)

## <a name="scaling-mainframe-compute-up-and-out"></a>Scalabilità orizzontale e orizzontale del calcolo del mainframe

I mainframe IBM offrono la possibilità di scalare fino a 240 core, ovvero le dimensioni Z14 correnti per un singolo sistema. Inoltre, i mainframe IBM possono aumentare la scalabilità orizzontale tramite una funzionalità denominata funzione di accoppiamento (CF). La CF consente a più sistemi mainframe di accedere contemporaneamente agli stessi dati. Usando la CF, il mainframe Parallel Sysplex Technology raggruppa i processori mainframe nei cluster. Quando questa guida è stata scritta, la funzionalità Sysplex parallela supporta 32 raggruppamenti di processori 64 ciascuno. In questo modo è possibile raggruppare fino a 2.048 processori per aumentare la capacità di calcolo.

Una CF consente ai cluster di calcolo di condividere i dati con accesso diretto. Viene usato per bloccare le informazioni, le informazioni sulla cache e l'elenco delle risorse di dati condivise. Un Sysplex parallelo che usa uno o più CFs può essere considerato come un cluster di calcolo con scalabilità orizzontale "Shared Everything". Per ulteriori informazioni su queste funzionalità, vedere [Parallel Sysplex on IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) sul sito Web IBM.

Le applicazioni possono usare queste funzionalità per offrire prestazioni di scalabilità orizzontale e disponibilità elevata. Per informazioni sul modo in cui CICS può usare Sysplex parallele con CF, scaricare [IBM CICS e la funzionalità di accoppiamento: oltre al](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) Redbook di base.

## <a name="azure-compute-at-a-glance"></a>Calcolo rapido di Azure

Alcune persone pensano erroneamente che i server basati su Intel non siano potenti come i mainframe. Tuttavia, i nuovi sistemi basati su Intel a densità Core hanno una capacità di calcolo maggiore di quella dei mainframe. Questa sezione descrive le opzioni IaaS (Infrastructure-as-a-Service) di Azure per l'elaborazione e l'archiviazione. Azure fornisce anche opzioni di piattaforma distribuita come servizio (PaaS), ma questo articolo è incentrato sulle opzioni di IaaS che forniscono capacità di mainframe analoghe.

Le macchine virtuali di Azure forniscono la potenza di calcolo in una gamma di dimensioni e tipi. In Azure una CPU virtuale (vCPU) equivale approssimativamente a un core in un mainframe.

Attualmente, l'intervallo di dimensioni delle macchine virtuali di Azure offre da 1 a 128 vCPU. I tipi di macchina virtuale (VM) sono ottimizzati per carichi di lavoro specifici. Ad esempio, l'elenco seguente mostra i tipi di VM (correnti al momento della stesura di questo articolo) e i relativi usi consigliati:

| Dimensione     | Tipo e descrizione                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Serie D | Utilizzo generico con 64 vCPU e velocità di clock fino a 3,5 GHz                           |
| Serie E | Con ottimizzazione per la memoria fino a 64 vCPU                                                 |
| Serie F | Calcolo ottimizzato con una velocità di clock fino a 64 vCPU e 3.7 GHz                       |
| Serie H | Ottimizzato per applicazioni HPC (High Performance Computing)                          |
| Serie L | Archiviazione ottimizzata per applicazioni a velocità effettiva elevata supportate da database come NoSQL |
| Serie M | Macchine virtuali di calcolo e con ottimizzazione per la memoria più grandi con fino a 128 vCPU                        |

Per informazioni dettagliate sulle macchine virtuali disponibili, vedere [serie di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Un mainframe Z14 può avere fino a 240 core. Tuttavia, i mainframe Z14 quasi mai utilizzano tutti i core per una singola applicazione o carico di lavoro. Al contrario, un mainframe separa i carichi di lavoro in partizioni logiche (LPAR) e le LPAR hanno classificazioni, ovvero MIPS (milioni di istruzioni al secondo) o MSU (milioni di unità di servizio). Quando si determinano le dimensioni della macchina virtuale paragonabili necessarie per eseguire un carico di lavoro mainframe in Azure, calcolare la classificazione MIPS (o MSU).

Di seguito sono riportate le stime generali:

-   150 MIPS per vCPU

-   1.000 MIPS per processore

Per determinare le dimensioni corrette della macchina virtuale per un determinato carico di lavoro in un LPAR, prima di tutto ottimizzare la macchina virtuale per il carico di lavoro. Determinare quindi il numero di vCPU necessari. Una stima conservativa è 150 MIPS per ogni vCPU. In base a questa stima, ad esempio, una VM serie F con 16 vCPU può supportare facilmente un carico di lavoro IBM DB2 proveniente da un LPAR con 2.400 MIPS.

## <a name="azure-compute-scale-up"></a>Scalabilità verticale di calcolo di Azure

Le macchine virtuali della serie M possono essere scalate fino a 128 vCPU, al momento della scrittura di questo articolo. Utilizzando la stima conservativa di 150 MIPS per vCPU, la VM serie M equivale a circa 19.000 MIPS. La regola generale per la stima di MIPS per un mainframe è 1.000 MIPS per processore. Un mainframe Z14 può avere fino a 24 processori e fornire circa 24.000 MIPS per un singolo sistema mainframe.

Il più grande mainframe Z14 singolo ha approssimativamente 5.000 MIPS più della VM più grande disponibile in Azure. Tuttavia è importante confrontare il modo in cui vengono distribuiti i carichi di lavoro. Se un sistema mainframe dispone sia di un'applicazione sia di un database relazionale, vengono in genere distribuiti nello stesso mainframe fisico, ciascuno con la propria LPAR. La stessa soluzione in Azure viene spesso distribuita usando una macchina virtuale per l'applicazione e una macchina virtuale di dimensioni separate e adattabile per il database.

Se, ad esempio, un sistema M64 vCPU supporta l'applicazione e viene usato un vCPU M96 per il database, sono necessari circa 150 vCPU o circa 24.000 MIPS come illustrato nella figura seguente.

![Confronto delle distribuzioni dei carichi di lavoro di 24.000 MIPS](media/mainframe-compute-mips.png)

L'approccio consiste nel migrare LPAR a singole macchine virtuali. Azure consente quindi di ridimensionare facilmente le dimensioni necessarie per la maggior parte delle applicazioni distribuite in un singolo sistema mainframe.

## <a name="azure-compute-scale-out"></a>Scalabilità orizzontale di calcolo di Azure

Uno dei vantaggi di una soluzione basata su Azure è la possibilità di scalare in orizzontale. Il ridimensionamento rende disponibile una capacità di calcolo quasi illimitata per un'applicazione. Azure supporta più metodi per la scalabilità orizzontale della potenza di calcolo:

- **Bilanciamento del carico in un cluster.** In questo scenario, un'applicazione può usare un servizio di [bilanciamento del carico](/azure/load-balancer/load-balancer-overview) o Resource Manager per suddividere il carico di lavoro tra più macchine virtuali in un cluster. Se è necessaria una maggiore capacità di calcolo, vengono aggiunte altre VM al cluster.

- **Set di scalabilità di macchine virtuali.** In questo scenario di espansione, un'applicazione può essere ridimensionata a [risorse di calcolo](/azure/virtual-machine-scale-sets/overview) aggiuntive in base all'utilizzo della macchina virtuale. Quando la richiesta cade, il numero di macchine virtuali in un set di scalabilità può anche andare inattivo, garantendo un uso efficiente della potenza di calcolo.

- **Ridimensionamento PaaS.** Azure PaaS offre scalabilità delle risorse di calcolo. [Azure Service Fabric](/azure/service-fabric/service-fabric-overview) , ad esempio, alloca le risorse di calcolo per soddisfare gli aumenti del volume delle richieste.

- **Cluster Kubernetes.** Le applicazioni in Azure possono usare [cluster Kubernetes](/azure/aks/concepts-clusters-workloads) per i servizi di calcolo per le risorse specificate. Azure Kubernetes Service (AKS) è un servizio gestito che orchestra nodi, pool e cluster Kubernetes in Azure.

Per scegliere il metodo appropriato per la scalabilità orizzontale delle risorse di calcolo, è importante comprendere il modo in cui Azure e i mainframe sono diversi. La chiave è come, o se, i dati vengono condivisi dalle risorse di calcolo. In Azure i dati (per impostazione predefinita) non sono in genere condivisi da più macchine virtuali. Se la condivisione dei dati è richiesta da più macchine virtuali in un cluster di calcolo con scalabilità orizzontale, i dati condivisi devono trovarsi in una risorsa che supporta questa funzionalità. In Azure, la condivisione dei dati riguarda l'archiviazione come illustrato nella sezione seguente.

## <a name="azure-compute-optimization"></a>Ottimizzazione calcolo di Azure

È possibile ottimizzare ogni livello di elaborazione in un'architettura di Azure. Usare il tipo più adatto di macchine virtuali e funzionalità per ogni ambiente. La figura seguente illustra un modello potenziale per la distribuzione di macchine virtuali in Azure per supportare un'applicazione CICS che usa DB2. Nel sito primario, le macchine virtuali per l'ambiente di produzione, pre-produzione e test sono distribuite con disponibilità elevata. Il sito secondario è destinato al backup e al ripristino di emergenza.

Ogni livello può inoltre fornire servizi di ripristino di emergenza appropriati. Ad esempio, le macchine virtuali di produzione e dei database possono richiedere il ripristino a caldo, mentre le macchine virtuali di test e sviluppo supportano il ripristino a freddo.

![Distribuzione a disponibilità elevata che supporta il ripristino di emergenza](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Passaggi successivi

- [Migrazione del mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Riallocazione del mainframe in macchine virtuali di Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Spostare l'archiviazione mainframe in Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Risorse IBM

- [Sysplex parallelo in IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [CICS IBM e la funzionalità di accoppiamento: oltre le nozioni di base](https://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Creating required users for a Db2 pureScale Feature installation](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2) (Creazione degli utenti necessari per l'installazione della funzionalità Db2 pureScale)
- [Db2icrt - Create instance command](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html) (Db2icrt - Comando Crea istanza)
- [Soluzione di database del cluster DB2 pureScale](https://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Microsoft Azure per enti pubblici cloud per applicazioni mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Altre risorse di migrazione

- [Data center virtuale di Azure: guida al lift and shift](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/) (iSCSI in GlusterFS)
