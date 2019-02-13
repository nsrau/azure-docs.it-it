---
title: Configurazioni del carico di lavoro SAP con le zone di disponibilità di Azure | Microsoft Docs
description: Architettura e scenari di disponibilità elevata per SAP NetWeaver con le zone di disponibilità di Azure
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 409a304296d3fdff897a203177e2c150162755c6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746218"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurazioni del carico di lavoro SAP con le zone di disponibilità di Azure

Le [zone di disponibilità di Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) sono una delle funzionalità per la disponibilità elevata offerte da Azure per migliorare la disponibilità complessiva del carico di lavoro SAP in Azure. Le zone di disponibilità sono già disponibili in varie [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/) e seguiranno altre aree. 

L'architettura di base per la disponibilità elevata di SAP può essere descritta come illustrato nel grafico seguente:

![Configurazione standard della disponibilità elevata](./media/sap-ha-availability-zones/standard-ha-config.png)

Il livello dell'applicazione SAP viene distribuito in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) di Azure. Per la disponibilità elevata di SAP Central Services, vengono distribuite due macchine virtuali in un set di disponibilità separato e si usano i servizi cluster di failover di Windows o Pacemaker (Linux) per distribuire un framework di disponibilità elevata che consente il failover automatico in caso di problemi a un'infrastruttura o di software. Nei documenti seguenti sono illustrate in dettaglio queste distribuzioni:

- [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Clustering di un'istanza ASCS/SCS di SAP in un cluster di failover Windows tramite una condivisione file](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Disponibilità elevata per SAP NetWeaver su macchine virtuali di Azure in SUSE Linux Enterprise Server for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Disponibilità elevata delle macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Un'architettura analoga è applicabile al l livello DBMS di sistemi SAP NetWeaver, S/4HANA o Hybris. Il livello DBMS viene distribuito in modalità attiva/passiva con una soluzione di cluster di failover, che usa framework di failover specifici per sistemi DBMS, i servizi cluster di failover di Windows o Pacemaker per avviare le attività di failover in caso di errore software o dell'infrastruttura. 

Per distribuire la stessa architettura usando le zone di disponibilità di Azure, è necessario apportare alcune modifiche all'architettura descritta. Queste modifiche sono descritte nelle diverse parti di questo documento.

## <a name="considerations-deploying-across-availability-zones"></a>Considerazioni sulla distribuzione in più zone di disponibilità

Quando si usano le zone di disponibilità occorre tenere conto di alcuni aspetti. Ad esempio:

- Le zone di disponibilità di Azure non offrono alcuna garanzia di distanza certa tra le varie zone all'interno di un'area di Azure.
- Le zone di disponibilità di Azure non sono una soluzione di ripristino di emergenza ideale. In caso di calamità naturali gravi che causano danni molto estesi in alcune aree del mondo, inclusi danni notevoli alla rete elettrica, la distanza tra le diverse zone potrebbe non essere sufficiente perché questa soluzione possa essere considerata appropriata per il ripristino di emergenza.
- La latenza di rete tra le diverse zone di disponibilità di Azure nelle diverse aree di Azure è diversa per ogni area di Azure. In alcuni casi, si può riuscire a eseguire il livello dell'applicazione SAP distribuito su zone diverse, perché la latenza di rete da una zona alla macchina virtuale DBMS attiva è comunque accettabile dal punto di vista dell'impatto sui processi aziendali. In altri scenari in alcune aree di Azure, la latenza tra la macchina virtuale DBMS attiva in una zona e l'istanza di un'applicazione SAP in un'altra zona può essere eccessiva e non accettabile per i processi aziendali SAP. Di conseguenza, l'architettura di distribuzione deve essere diversa con un'architettura attiva/attiva per l'applicazione o attiva/passiva se la latenza tra zone è troppo elevata.
- Decidere la configurazione per la quale si potrebbero usare le zone di disponibilità in base alla latenza di rete misurata tra le diverse zone. La latenza di rete svolge un ruolo importante in due aree differenti:
    - Latenza tra le due istanze del DBMS per le quali è necessario configurare la replica sincrona. Maggiore sarà la latenza di rete, maggiore sarà la probabilità di effetti sulla scalabilità del carico di lavoro.
    - La differenza nella latenza di rete tra una macchina virtuale che esegue un'istanza di dialogo SAP nella stessa zona dell'istanza del DBMS attiva e una macchina virtuale simile in un'altra area. Maggiore sarà questa differenza, più alto sarà l'impatto sul tempo di esecuzione dei processi aziendali e dei processi batch, a seconda che siano eseguiti nella stessa zona del sistema DBMS o in una zona diversa.


Per quanto riguarda l'utilizzo delle funzionalità di Azure, esistono alcune restrizioni delle funzionalità che possono essere usate durante la distribuzione di macchine virtuali di Azure tra aree e l'implementazione di soluzioni di failover tra zone di disponibilità diverse all'interno della stessa area di Azure. Queste restrizioni sono le seguenti:

- L'uso di [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) è obbligatorio per la distribuzione in zone di disponibilità di Azure. 
- Il mapping delle enumerazioni di zona con le zone fisiche è fisso per ogni sottoscrizione di Azure. Se si usano sottoscrizioni diverse per distribuire i sistemi SAP, è necessario definire le zone ideali per ogni sottoscrizione separatamente.
- Non è possibile distribuire set di disponibilità di Azure all'interno di una zona di disponibilità di Azure. Scegliere una zona di disponibilità di Azure o un set di disponibilità di Azure impostato come framework di distribuzione per le macchine virtuali.
- Non è possibile usare [Azure Load Balancer Basic](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) per creare soluzioni cluster di failover basate sui servizi cluster di failover di Windows o su Linux Pacemaker. È invece necessario usare lo [SKU Load Balancer Standard di Azure](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="ideal-zone-combination"></a>Combinazione ideale di zone
Per decidere in che modo è possibile sfruttare le zone di disponibilità, è necessario eseguire indagini per ottenere i dati seguenti:

- La latenza di rete tra le tre diverse zone di un'area di Azure, in modo da poter scegliere le zone con la latenza di rete minore nel traffico di rete tra le zone.
- La differenza tra latenza tra le macchine virtuali all'interno di una delle zone scelte e la latenza di rete tra le due zone scelte.
- Stabilire se i tipi di macchina virtuale che è necessario distribuire tra le zone di disponibilità prescelte sono disponibili nelle due zone di propria scelta. In particolare con le macchine virtuali serie M, in alcune situazioni i diversi SKU di macchina virtuale serie M sono disponibili solo in due delle tre zone.

### <a name="network-latency-between-zones-and-within-zone"></a>Latenza di rete tra le zone e all'interno della zona
Per scoprire qual è la latenza tra le varie zone, è necessario:

- Distribuire lo SKU di macchina virtuale che si vuole usare per l'istanza del DBMS in tutte e tre le zone. Assicurarsi che l'opzione [rete accelerata di Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) sia abilitata durante l'esecuzione di questa misurazione.
- Dopo aver individuato le due zone con la minore latenza di rete, distribuire altre tre macchine virtuali dello SKU che si vuole usare come macchina virtuale del livello dell'applicazione nelle tre zone di disponibilità. Misurare la latenza di rete per le due macchine virtuali del DBMS nelle due diverse zone DBMS prescelte. 
- Come strumento per la misurazione, usare **niping**, ovvero uno strumento di SAP, che funziona come descritto nelle note di supporto SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Concentrarsi sui comandi documentati da SAP per le misurazioni della latenza. L'uso di **ping** non è consigliato perché **ping** non funziona su percorsi di codice di rete accelerata di Azure.

In base alle misurazioni e alla disponibilità degli SKU di macchine virtuali nelle diverse zone, è necessario prendere le decisioni seguenti:

- Definire le zone ideali per il livello DBMS
- Stabilire se, in base alle differenze della latenza di rete all'interno di una zona o tra più zone, è possibile distribuire il livello dell'applicazione SAP attivo su una, due o tutte e tre le zone diverse
- Stabilire se si vuole distribuire una configurazione attiva/passiva o una configurazione attiva/attiva dal punto di vista dell'applicazione (vedere più avanti)

Per prendere queste decisioni, tenere presenti le raccomandazioni per la latenza di rete di SAP documentate nella nota SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

### <a name="be-aware-of"></a>Note importanti

> [!IMPORTANT]
> Le misurazioni e le decisioni sono valide per la sottoscrizione di Azure usata per effettuare tali misurazioni. Se si usa un'altra sottoscrizione di Azure, è necessario ripetere le misurazioni perché il mapping dell'enumerazione delle zone dipendente dalla sottoscrizione può cambiare per una sottoscrizione diversa.


> [!IMPORTANT]
> È previsto che le misurazioni indicate in precedenza producano risultati diversi in ogni area di Azure che supporta le [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Anche se i requisiti di latenza di rete non cambiano, potrebbe essere necessario adottare strategie di distribuzione diverse in diverse aree di Azure, perché la latenza di rete tra le zone può essere diversa. È previsto che in alcune aree di Azure la latenza di rete tra le tre zone diverse possa essere notevolmente diversa. In altre aree, invece, la latenza di rete tra le tre diverse zone è più uniforme. L'attestazione della presenza di una latenza di rete tra le zone **sempre** compresa tra 1 e 2 millisecondi è **errata**. La latenza di rete tra le zone di disponibilità nelle aree di Azure non può essere generalizzata.


## <a name="activeactive-deployment"></a>Distribuzione attiva/attiva
Questa architettura di distribuzione viene chiamata attiva/attiva, perché le istanze di dialogo SAP attive vengono distribuite tra due o tre zone. I servizi SAP Central Services che usano la replica di accodamento verranno distribuiti tra due zone. Lo stesso vale per il livello DBMS, che verrà distribuite tra le stesse zone di SAP Central Services.

Per prendere in considerazione questa configurazione, è necessario trovare due zone di disponibilità nell'area di appartenenza, in grado di offrire una latenza di rete tra zone accettabile per il carico di lavoro e per la replica DBMS sincrona. Inoltre, è opportuno che il differenziale tra la latenza di rete all'interno delle zone selezionate e tra le zone non sia troppo grande. Il motivo di quest'ultimo requisito è che occorre evitare variazioni troppo estese nei tempi di esecuzione dei processi aziendali o dei processi batch, a seconda che un processo venga eseguito all'interno della stessa zona del server DBMS o su zone diverse. Alcune variazioni sono accettabili, ma non differenze sostanziali.

Uno schema semplificato di una distribuzione attiva/attiva su due zone potrebbe essere simile al seguente:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

A questa configurazione si applicano le considerazioni seguenti:

- Le zone di disponibilità di Azure vengono considerate in errore e si aggiornano i domini per tutte le macchine virtuali dato che i set di disponibilità non possono essere distribuiti nelle zone di disponibilità di Azure
- I servizi di bilanciamento del carico di Azure usati per i cluster di failover di SAP Central Services, nonché per il livello DBMS, devono corrispondere allo [SKU Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Load Balancer Basic non funziona tra zone.
- La rete virtuale di Azure e le relative subnet distribuite per ospitare il sistema SAP sono estese tra le zone. **Non è necessario** separare le reti virtuali per ogni zona.
- Per tutte le macchine virtuali distribuite, usare [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). I dischi non gestiti non sono supportati per le distribuzioni nelle zone.
- L'archiviazione Premium di Azure oppure l'[archiviazione Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) non supporta alcun tipo di replica dell'archiviazione tra le zone. Di conseguenza è responsabilità dell'applicazione (DBMS o SAP Central Services) replicare i dati importanti.
- Lo stesso vale per la directory sapmnt condivisa, ovvero un disco condiviso (Windows), una condivisione CIFS (Windows) o una condivisione NFS (Linux). È necessario usare una tecnologia che esegue la replica di tale disco condiviso o condivisione tra le zone. Al momento sono supportate le tecnologie seguenti:
    - Per Windows è supportata tra le zone una soluzione cluster che usa SIOS Datakeeper, come documentato nel [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Per SUSE Linux è supportata una condivisione NFS configurata come documentato in [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    - Al momento, la soluzione che usa i servizi SOFS (Scale-Out File Services) di Windows, come documentato in [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **non è supportata tra le zone**.
- La terza zona viene usata per ospitare il dispositivo SBD nel caso si creino un [cluster Pacemaker di SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) o altre istanze dell'applicazione.
- Per ottenere la coerenza in fase di esecuzione per transazioni e/o processi aziendali critici. È possibile provare a indirizzare determinati processi batch e utenti direttamente a istanze specifiche dell'applicazione nella zona con l'istanza DBMS attiva usando gruppi di server batch, gruppi di accesso o gruppi RFC SAP. Tuttavia, in caso di failover di zona, è necessario spostare manualmente questi gruppi nelle istanze di dialogo nelle zone rimanenti. 
- Decidere se si vogliono distribuire alcune istanze di dialogo inattive in ognuna delle zone per essere in grado di tornare immediatamente alla capacità di risorse precedente in caso di non disponibilità di una zona in cui è stata distribuita una parte delle istanze dell'applicazione.


## <a name="activepassive-deployment"></a>Distribuzione attiva/passiva
Nei casi in cui non si riesce a trovare un delta accettabile tra la latenza di rete all'interno di una zona e il traffico di rete tra le zone, l'architettura che è possibile distribuire è di tipo attivo/passivo dal punto di vista del livello dell'applicazione SAP. È possibile definire una zona 'attiva', che è la zona in cui si distribuisce il livello dell'applicazione completo e in cui si tenta di eseguire l'istanza DBMS attiva e l'istanza di SAP Central Services attiva. Con questa configurazione, ci si assicura di non avere una differenza estrema a livello di tempi di esecuzione per le transazioni aziendali e i processi batch, a seconda che un processo venga eseguito o meno nella stessa zona dell'istanza DBMS attiva.

Il layout di base di questo tipo di architettura è simile al seguente:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

A questa configurazione si applicano le considerazioni seguenti:

- I set di disponibilità non possono essere distribuiti nelle zone di disponibilità di Azure. Di conseguenza, in questo caso, si ottiene un dominio di aggiornamento e un dominio di errore per il livello dell'applicazione. Il motivo è che viene distribuito solo in una zona. Questa configurazione comporta alcuni leggeri svantaggi rispetto all'architettura di riferimento che prevede la distribuzione del livello dell'applicazione in un set di disponibilità di Azure.
- Per la gestione operativa di tale architettura, è necessario prevedere un monitoraggio attento e provare a mantenere le istanze di DBMS e di SAP Central Services attive nella stessa zona del livello dell'applicazione distribuita. In caso di failover dell'istanza di SAP Central Services o DBMS, è opportuno assicurarsi che sia possibile eseguire manualmente il failback nella zona con il livello dell'applicazione SAP distribuita non appena possibile.
- I servizi di bilanciamento del carico di Azure usati per i cluster di failover di SAP Central Services, nonché per il livello DBMS, devono corrispondere allo [SKU Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Load Balancer Basic non funziona tra zone.
- La rete virtuale di Azure e le relative subnet distribuite per ospitare il sistema SAP sono estese tra le zone. **Non è necessario** separare le reti virtuali per ogni zona.
- Per tutte le macchine virtuali distribuite, è necessario usare [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). I dischi non gestiti non sono supportati per le distribuzioni nelle zone.
- L'archiviazione Premium di Azure oppure l'[archiviazione Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) non supporta alcun tipo di replica dell'archiviazione tra le zone. Di conseguenza è responsabilità dell'applicazione (DBMS o SAP Central Services) replicare i dati importanti.
- Lo stesso vale per la directory sapmnt condivisa, ovvero un disco condiviso (Windows), una condivisione CIFS (Windows) o una condivisione NFS (Linux). È necessario usare una tecnologia che esegue la replica di tale disco condiviso o condivisione tra le zone. Al momento sono supportate le tecnologie seguenti:
    - Per Windows è supportata tra le zone una soluzione cluster che usa SIOS Datakeeper, come documentato nel [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Per SUSE Linux è supportata una condivisione NFS configurata come documentato in [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    - Al momento, la soluzione che usa i servizi SOFS (Scale-Out File Services) di Windows, come documentato in [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **non è supportata tra le zone**.
- La terza zona viene usata per ospitare il dispositivo SBD nel caso si creino un [cluster Pacemaker di SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) o altre istanze dell'applicazione.
- Distribuire le macchine virtuali inattive nella zona passiva (dal punto di vista del sistema DBMS) per poter avviare le risorse dell'applicazione in caso di errore della zona.
    - Non è possibile usare [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) per replicare le macchine virtuali attive nelle macchine virtuali inattive tra zone. Al momento, Azure Site Recovery non è in grado di offrire questa funzionalità.
- Investire in una soluzione di automazione che consenta, in caso di errore di una zona, di avviare automaticamente il livello dell'applicazione SAP nella seconda zona.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Configurazione combinata per disponibilità elevata e ripristino di emergenza
Nonostante il fatto che Microsoft non fornisca informazioni sulla distanza geografica tra le strutture che ospitano le diverse zone di disponibilità di Azure in una determinata area di Azure, alcuni clienti stanno sfruttando le zone per una configurazione combinata per disponibilità elevata e ripristino di emergenza in grado di garantire un obiettivo del punto di ripristino (RPO, **R**ecovery **P**oint **O**bjective) pari a zero. Questo significa non perdere alcuna transazione di database di cui è stato eseguito il commit, anche in caso di ripristino di emergenza. 

> [!NOTE]
> Una configurazione di questo tipo è consigliabile solo in circostanze specifiche. Ad esempio, i casi in cui i dati non possono lasciare l'area di Azure per motivi di sicurezza e conformità. 

In questo grafico è illustrato un esempio di questa configurazione:

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

A questa configurazione si applicano le considerazioni seguenti:

- Si presuppone che esista una distanza significativa tra le strutture che ospitano una zona di disponibilità oppure è obbligatorio rimanere in una determinata area di Azure. I set di disponibilità non possono essere distribuiti nelle zone di disponibilità di Azure. Di conseguenza, in questo caso, si ottiene un dominio di aggiornamento e un dominio di errore per il livello dell'applicazione. Il motivo è che viene distribuito solo in una zona. Questa configurazione rappresenta un passo indietro rispetto all'architettura di riferimento che prevede la distribuzione del livello dell'applicazione in un set di disponibilità di Azure.
- Per la gestione operativa di tale architettura, è necessario prevedere un monitoraggio attento e provare a mantenere le istanze di DBMS e di SAP Central Services attive nella stessa zona del livello dell'applicazione distribuita. In caso di failover dell'istanza di SAP Central Services o DBMS, è opportuno assicurarsi che sia possibile eseguire manualmente il failback nella zona con il livello dell'applicazione SAP distribuita non appena possibile.
- Esistono istanze dell'applicazione di produzione pre-installate nelle macchine virtuali che eseguono istanze dell'applicazione di controllo qualità attive.
- In caso di errore di una zona, si arrestano le istanze dell'applicazione di controllo qualità e si avviano invece le istanze di produzione. Tenere presente che è necessario intervenire sui nomi virtuali per le istanze dell'applicazione per far funzionare questo meccanismo.
- I servizi di bilanciamento del carico di Azure usati per i cluster di failover di SAP Central Services, nonché per il livello DBMS, devono corrispondere allo [SKU Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Load Balancer Basic non funziona tra zone.
- La rete virtuale di Azure e le relative subnet distribuite per ospitare il sistema SAP sono estese tra le zone. **Non è necessario** separare le reti virtuali per ogni zona.
- Per tutte le macchine virtuali distribuite, è necessario usare [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). I dischi non gestiti non sono supportati per le distribuzioni nelle zone.
- L'archiviazione Premium di Azure oppure l'[archiviazione Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) non supporta alcun tipo di replica dell'archiviazione tra le zone. Di conseguenza è responsabilità dell'applicazione (DBMS o SAP Central Services) replicare i dati importanti.
- Lo stesso vale per la directory sapmnt condivisa, ovvero un disco condiviso (Windows), una condivisione CIFS (Windows) o una condivisione NFS (Linux). È necessario usare una tecnologia che esegue la replica di tale disco condiviso o condivisione tra le zone. Al momento sono supportate le tecnologie seguenti:
    - Per Windows è supportata tra le zone una soluzione cluster che usa SIOS Datakeeper, come documentato nel [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Per SUSE Linux è supportata una condivisione NFS configurata come documentato in [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    - Al momento, la soluzione che usa i servizi SOFS (Scale-Out File Services) di Windows, come documentato in [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **non è supportata tra le zone**.
- La terza zona viene usata per ospitare il dispositivo SBD nel caso si creino un [cluster Pacemaker di SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) o altre istanze dell'applicazione.





## <a name="next-steps"></a>Passaggi successivi
Vedere le informazioni seguenti per la distribuzione tra zone di disponibilità di Azure:

- [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






