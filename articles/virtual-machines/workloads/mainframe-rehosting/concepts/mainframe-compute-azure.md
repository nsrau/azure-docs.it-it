---
title: Spostare il calcolo di mainframe per le macchine virtuali di Azure
description: Calcolo di Azure confronta le risorse presenta dei vantaggi rispetto alla capacità di mainframe in modo che è possibile eseguire la migrazione e modernizzare le applicazioni z14 IBM.
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: multiple
ms.openlocfilehash: 8aea4a74ba84855f011dada70ea75ec0d5fb64fe
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/03/2019
ms.locfileid: "58896380"
---
# <a name="move-mainframe-compute-to-azure"></a>Spostare il calcolo di mainframe in Azure

Mainframe abbiamo una reputazione per la disponibilità e affidabilità elevata e continuano a essere il backbone di molte organizzazioni attendibile. È spesso ritenevano di avere la scalabilità quasi illimitata per soddisfare e potenza di calcolo anche. Tuttavia, alcune organizzazioni hanno superato le la capacità di mainframe più grande disponibile. Se tutto ciò suona come te, Azure offre risparmi in termini di agilità, portata e infrastruttura.

Per eseguire i carichi di lavoro di mainframe in Microsoft Azure, è necessario conoscere la modalità di calcolo di confronto con la funzionalità Azure del mainframe. Basato su un mainframe z14 IBM (il modello più recente al momento della stesura di questo documento), questo articolo descrive come ottenere risultati simili in Azure.

Per iniziare, prendere in considerazione gli ambienti di fianco a fianco. La figura seguente viene confrontato un ambiente mainframe per l'esecuzione di applicazioni in un ambiente di hosting di Azure.

![Servizi di Azure e offrono gli ambienti di emulazione confrontabili supportano e semplifica la migrazione](media/mainframe-compute-azure.png)

La potenza del mainframe viene spesso usata per i sistemi (OLTP) di gestire milioni di aggiornamenti per migliaia di utenti di elaborazione delle transazioni online. Queste applicazioni usano spesso software per l'elaborazione delle transazioni, la gestione dello schermo e voce di modulo. Si possono utilizzare un cliente informazioni Control System (CICS), informazioni Management System (IMS) o interfaccia pacchetto TIP (Transaction).

Come illustrato nella figura seguente, un emulatore del TPM in Azure può gestire carichi di lavoro CICS e IMS. Un emulatore sistema batch in Azure esegue il ruolo di Job Control Language (JCL). Migrazione dei dati di mainframe per database di Azure, ad esempio Database SQL di Azure. Servizi di Azure o altri software ospitato nelle macchine virtuali di Azure può essere utilizzato per la gestione di system.

## <a name="mainframe-compute-at-a-glance"></a>Calcolo di mainframe in modo immediato

Nel mainframe, z14 processori vengono disposte in massimo quattro *cassetti*. Oggetto *cassetto* è semplicemente un cluster di processori e per PC portatili. Ogni pannello può avere sei chips processore centrale active (CP) e ogni CP ha 10 chips controller (SC) di sistema. Nella terminologia di Intel x86, sono disponibili sei socket per ogni pannello 10 core per socket e quattro pannelli. Questa architettura offre l'equivalente di 24 socket e 240 core, massimi, per un z14.

La rapida z14 CP con velocità di clock 5.2 GHz. In genere, un z14 viene recapitato con tutto l'istruzione CPs nella casella. Sono attivati in base alle esigenze. Un cliente comune viene addebitato almeno quattro ore dall'ora di calcolo al mese nonostante l'utilizzo effettivo.

Un processore di mainframe può essere configurato come uno dei tipi seguenti:

- Processore (GP) scopo generale
- Sistema z integrata informazioni processore (zIIP)
- Funzionalità integrata per il processore di Linux (IFL)
- Processore del sistema Assist (SAP)
- Processore accoppiamento impianto (ICF) integrato

## <a name="scaling-mainframe-compute-up-and-out"></a>Calcolo di mainframe della scalabilità verticale e orizzontale

Mainframe IBM e offrono la possibilità di scalare fino a 240 core (la dimensione z14 corrente per un singolo sistema). Mainframe IBM, inoltre, possono scalare orizzontalmente mediante una funzionalità denominata l'accoppiamento tra funzionalità (CF). Il cloud Foundry consente a più sistemi di mainframe per accedono contemporaneamente agli stessi dati. Uso di cloud Foundry, i processori mainframe Sysplex parallele tecnologia gruppi mainframe in cluster. Quando è stato scritto in questa Guida, la funzionalità parallele Sysplex supportata 32 raggruppamenti di 64 processori. Fino a 2.048 processori può essere raggruppata in questo modo per aumentare le capacità di calcolo.

Un cloud Foundry consente a cluster di elaborazione condividere dati con accesso diretto. Viene usato per il blocco di informazioni, informazioni della cache e l'elenco delle risorse di dati condiviso. Un Sysplex parallela usando uno o più CFs può essere considerato come un "condivisi tutti gli elementi" cluster di calcolo di tipo scale-out. Per altre informazioni su queste funzionalità, vedere [Sysplex parallele in IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources) sul sito Web IBM.

Le applicazioni possono usare queste funzionalità per fornire le prestazioni di scalabilità orizzontale e disponibilità elevata. Per informazioni su come CICS usare Sysplex parallele con cloud Foundry, scaricare il [IBM CICS e la possibilità di connettere: Oltre le nozioni di base](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf) redbook.

## <a name="azure-compute-at-a-glance"></a>Calcolo di Azure in modo immediato

Alcuni utenti ritengono erroneamente che i server basati su Intel non sono efficienti quanto mainframe. Tuttavia, i nuovi sistemi basati su Intel ad alta densità di core hanno come calcolo molto capacità come mainframe. Questa sezione descrive le opzioni di Azure infrastructure-as-a-service (IaaS) per l'elaborazione e archiviazione. Azure offre anche opzioni di platform-as-a-service (PaaS), ma questo articolo è incentrato sulle scelte IaaS offrono capacità di mainframe confrontabili.

Macchine virtuali di Azure forniscono potenza di calcolo in un intervallo di dimensioni e tipi. In Azure, una CPU virtuale (vCPU) equivale approssimativamente a un core in un mainframe.

Attualmente, le dimensioni intervallo della macchina virtuale Azure fornisce da 1 a 128 Vcpu. Tipi di macchine virtuali (VM) sono ottimizzati per carichi di lavoro specifici. Ad esempio, nell'elenco seguente mostra i tipi di VM (attuali rispetto alla stesura di questo articolo) e il relativo utilizzo consigliato:

| Dimensione     | Tipo e descrizione                                                                 |
|----------|--------------------------------------------------------------------------------------|
| Serie D | Generico con 64 vCPU e un massimo di velocità di clock 3,5 GHz                           |
| Serie E | Memoria ottimizzata con un massimo di 64 Vcpu                                                 |
| Serie F | Calcolo ottimizzato con un massimo di 64 Vcpu e velocità di clock 3..7 GHz                       |
| Serie H | Ottimizzato per le applicazioni high performance computing (HPC)                          |
| Serie L | Archiviazione ottimizzata per le applicazioni a elevata velocità effettiva supportate da database, ad esempio NoSQL |
| Serie M | Memoria e calcolo più grande con ottimizzazione per la VM con fino a 128 Vcpu                        |

Per informazioni dettagliate su macchine virtuali disponibili, vedere [serie di macchine virtuali](https://azure.microsoft.com/pricing/details/virtual-machines/series/).

Un mainframe z14 può avere fino a 240 Core. Z14 mainframe, tuttavia, usano quasi mai tutte le memorie centrali per una singola applicazione o carico di lavoro. Al contrario, un mainframe segrega i carichi di lavoro in partizioni logiche (LPARs) e il LPARs hanno classificazioni, MIPS (milioni di istruzioni al secondo) o MSU (milioni per unità del servizio). Quando si determina le dimensioni VM confrontabili necessari per eseguire un carico di lavoro di mainframe in Azure, classificazione di fattore in MIPS (o MSU).

Di seguito sono stime generali:

-   150 MIPS per ogni vCPU

-   1.000 MIPS per processore

Per determinare le dimensioni VM corretta per un carico di lavoro in un LPAR, ottimizzare la macchina virtuale per il carico di lavoro. Quindi determinare il numero di Vcpu necessita. Stima conservativa è 150 MIPS per ogni vCPU. In base a questa stima, ad esempio, una VM serie F con 16 Vcpu può supportare facilmente un carico di lavoro di IBM Db2 provenienti da un LPAR con MIPS 2.400.

## <a name="azure-compute-scale-up"></a>Scalabilità verticale di calcolo di Azure

Le macchine virtuali serie M è possono scalare fino a 128 Vcpu (al momento che è stato scritto questo articolo). Utilizzo di stima conservativa di 150 MIPS per ogni vCPU, la macchina virtuale serie M corrisponde a circa 19.000 MIPS. La regola generale per la stima MIPS per un mainframe è MIPS 1.000 per ogni processore. Un mainframe z14 può avere fino a 24 processori e fornire circa 24.000 MIPS per un sistema mainframe singolo.

Il mainframe z14 singola più grande è circa 5.000 MIPS più la più grande VM disponibili in Azure. Ancora è importante confrontare la distribuzione di carichi di lavoro. Se il sistema mainframe dispone di un'applicazione e un database relazionale, è in genere distribuiti sul mainframe fisico stesso, ognuno nella propria LPAR. La stessa soluzione in Azure viene spesso distribuita usando una macchina virtuale per l'applicazione e una macchina virtuale separata e dimensione adeguata per il database.

Ad esempio, se un sistema di vCPU M64 supporta l'applicazione e per il database viene utilizzata una vCPU M96, sono necessari circa 150 Vcpu, o circa 24.000 MIPS come mostrato nella figura seguente.

![Confronto tra le distribuzioni del carico di lavoro di 24.000 MIPS](media/mainframe-compute-mips.png)

L'approccio consiste nell'eseguire la migrazione LPARs a singole macchine virtuali. Quindi Azure facilmente contestualmente le prestazioni per le dimensioni necessarie per la maggior parte delle applicazioni distribuite in un sistema mainframe singolo.

## <a name="azure-compute-scale-out"></a>Calcolo di Azure. scale-out

Uno dei vantaggi di una soluzione basata su Azure è la possibilità di scalare in orizzontale. Rende scalabilità quasi illimitata per soddisfare la capacità disponibile per un'applicazione di calcolo. Azure supporta più metodi per la scalabilità orizzontale potenza di calcolo:

- **Il bilanciamento del carico interno di un cluster.** In questo scenario, un'applicazione può usare una [bilanciamento del carico](/azure/load-balancer/load-balancer-overview) o di resource manager per distribuire il carico di lavoro tra più macchine virtuali in un cluster. Se più di calcolo serve una capacità, le macchine virtuali aggiuntive vengono aggiunti al cluster.

- **Set di scalabilità di macchine virtuali.** In questo scenario di burst, è possibile scalare un'applicazione a ulteriori [risorse di calcolo](/azure/virtual-machine-scale-sets/overview) basata sull'utilizzo della macchina virtuale. Quando la richiesta rientra, il numero di macchine virtuali in un set di scalabilità può anche scendere, assicurando un uso efficiente della potenza di calcolo.

- **PaaS ridimensionamento.** La scalabilità offerte PaaS di Azure le risorse di calcolo. Ad esempio, [Azure Service Fabric](/azure/service-fabric/service-fabric-overview) alloca le risorse di calcolo per soddisfare l'aumento del volume di richieste.

- **Cluster Kubernetes.** Le applicazioni in Azure possono usare [i cluster Kubernetes](/azure/aks/concepts-clusters-workloads) per i servizi di calcolo per le risorse specificate. Azure Kubernetes Service (AKS) è un servizio gestito che Orchestra i nodi Kubernetes, pool e i cluster in Azure.

Per scegliere il metodo corretto per la scalabilità orizzontale delle risorse di calcolo, è importante comprendere differiscano tra Azure e mainframe. La chiave è come, o se, ovvero i dati vengono condivisi dalle risorse di calcolo. In Azure, i dati (per impostazione predefinita) non sono in genere condiviso da più macchine virtuali. Se la condivisione di dati richiesto dal cluster di calcolo più macchine virtuali di tipo scale-out, i dati condivisi devono risiedere in una risorsa che supporta questa funzionalità. In Azure, la condivisione dei dati comporta archiviazione come nella sezione seguente vengono illustrate.

## <a name="azure-compute-optimization"></a>Ottimizzazione di calcolo di Azure

È possibile ottimizzare ogni livello di elaborazione in un'architettura di Azure. Usare il tipo più adatto di macchine virtuali e le funzionalità per ogni ambiente. La figura seguente mostra un potenziale criterio per la distribuzione di macchine virtuali in Azure per supportare un'applicazione CICS che usa Db2. Nel sito primario, le macchine virtuali per l'ambiente di produzione, pre-produzione e test sono distribuite con disponibilità elevata. Il sito secondario è destinato al backup e al ripristino di emergenza.

Ogni livello può anche fornire servizi di ripristino di emergenza appropriato. Ad esempio, le macchine virtuali di produzione e dei database possono richiedere il ripristino a caldo, mentre le macchine virtuali di test e sviluppo supportano il ripristino a freddo.

![Distribuzione a disponibilità elevata che supporta il ripristino di emergenza](media/mainframe-compute-dr.png)

## <a name="next-steps"></a>Passaggi successivi

- [Migrazione dei mainframe](/azure/architecture/cloud-adoption/infrastructure/mainframe-migration/overview)
- [Mainframe rehosting in macchine virtuali di Azure](/azure/virtual-machines/workloads/mainframe-rehosting/overview)
- [Spostare risorse di archiviazione di mainframe in Azure](mainframe-storage-Azure.md)

### <a name="ibm-resources"></a>Risorse di IBM

- [Sysplex parallele in IBM Z](https://www.ibm.com/it-infrastructure/z/technologies/parallel-sysplex-resources)
- [IBM CICS e la funzionalità di controllo libero: Oltre le nozioni di base](http://www.redbooks.ibm.com/redbooks/pdfs/sg248420.pdf)
- [Creazione di utenti necessari per un'installazione della funzionalità di pureScale Db2](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/t0055374.html?pos=2)
- [Db2icrt - istanza comando Create](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.admin.cmd.doc/doc/r0002057.html)
- [DB2 pureScale Clustered soluzione di Database](http://www.ibmbigdatahub.com/blog/db2-purescale-clustered-database-solution-part-1)
- [IBM Data Studio](https://www.ibm.com/developerworks/downloads/im/data/index.html/)

### <a name="azure-government"></a>Azure Government

- [Cloud di Microsoft Azure per enti pubblici per le applicazioni di mainframe](https://azure.microsoft.com/resources/microsoft-azure-government-cloud-for-mainframe-applications/)
- [Microsoft e FedRAMP](https://www.microsoft.com/TrustCenter/Compliance/FedRAMP)

### <a name="more-migration-resources"></a>Altre risorse per la migrazione

- [Alliance modernizzazione di piattaforma: IBM Db2 in Azure](https://www.platformmodernization.org/pages/ibmdb2azure.aspx)
- [Azure Data Center virtuale Lift- and -Shift Guida](https://azure.microsoft.com/resources/azure-virtual-datacenter-lift-and-shift-guide/)
- [GlusterFS iSCSI](https://docs.gluster.org/en/latest/Administrator%20Guide/GlusterFS%20iSCSI/)
