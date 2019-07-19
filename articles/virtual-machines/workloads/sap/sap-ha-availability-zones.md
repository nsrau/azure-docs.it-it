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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ead1dfdce4bf3a803eee46a536dc7062626640d9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234245"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>Configurazioni del carico di lavoro SAP con le zone di disponibilità di Azure
[Zone di disponibilità di Azure](https://docs.microsoft.com/azure/availability-zones/az-overview) è una delle funzionalità a disponibilità elevata offerte da Azure. L'uso delle zone di disponibilità migliora la disponibilità generale dei carichi di lavoro SAP in Azure. Questa funzionalità è già disponibile in alcune [aree di Azure](https://azure.microsoft.com/global-infrastructure/regions/). In futuro sarà disponibile in un numero maggiore di aree.

Questo elemento grafico illustra l'architettura di base della disponibilità elevata di SAP:

![Configurazione con disponibilità elevata standard](./media/sap-ha-availability-zones/standard-ha-config.png)

Il livello dell'applicazione SAP viene distribuito in un [set di disponibilità](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) di Azure. Per la disponibilità elevata di SAP Central Services è possibile distribuire due macchine virtuali in un set di disponibilità separato. Usare Windows Server Failover Clustering o Pacemaker (Linux) come framework a disponibilità elevata con failover automatico in caso di problemi dell'infrastruttura o del software. Per altre informazioni su queste distribuzioni, vedere:

- [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Clustering di un'istanza ASCS/SCS di SAP in un cluster di failover Windows tramite una condivisione file](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Disponibilità elevata per SAP NetWeaver su macchine virtuali di Azure in SUSE Linux Enterprise Server for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Disponibilità elevata delle macchine virtuali di Azure per SAP NetWeaver in Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Un'architettura analoga è applicabile al l livello DBMS di sistemi SAP NetWeaver, S/4HANA o Hybris. Il livello DBMS viene distribuito in modalità attivo/passivo con una soluzione cluster di failover per la protezione da errori a livello di infrastruttura o di software. La soluzione cluster di failover può essere un framework di failover specifico di DBMS, Windows Server Failover Clustering o Pacemaker.

Per distribuire la stessa architettura usando le zone di disponibilità di Azure, è necessario apportare alcune modifiche all'architettura illustrata in precedenza. Le modifiche sono descritte in questo articolo.

## <a name="considerations-for-deploying-across-availability-zones"></a>Considerazioni sulla distribuzione in più zone di disponibilità


Quando si usano le zone di disponibilità, tenere presente quanto segue:

- Non esistono garanzie relative alle distanze tra diverse zone di disponibilità all'interno di un'area di Azure.
- Le zone di disponibilità non sono una soluzione di ripristino di emergenza ideale. In caso di calamità naturali gravi che causano danni molto estesi in alcune aree del mondo, inclusi danni notevoli alla rete elettrica, la distanza tra diverse zone potrebbe non essere sufficientemente estesa da costituire una soluzione di ripristino di emergenza adeguata.
- La latenza di rete tra le zone di disponibilità non è la stessa in tutte le aree di Azure. In alcuni casi, è possibile distribuire ed eseguire il livello dell'applicazione SAP su zone diverse, perché la latenza di rete da una zona alla macchina virtuale DBMS attiva è accettabile. In alcune aree di Azure, tuttavia, la latenza tra la macchina virtuale DBMS attiva e l'istanza dell'applicazione SAP, distribuite in zone diverse, può non essere sufficiente per i processi aziendali SAP. In questi casi, l'architettura di distribuzione deve essere diversa con un'architettura attiva/attiva per l'applicazione o attiva/passiva se la latenza di rete tra zone è troppo elevata.
- Nel decidere dove usare le zone di disponibilità, tenere conto della latenza di rete tra le zone. La latenza di rete svolge un ruolo importante in due aree:
    - Latenza tra le due istanze di DBMS per le quali è necessario configurare la replica sincrona. Maggiore sarà la latenza di rete, maggiore sarà l'impatto sulla scalabilità del carico di lavoro.
    - La differenza nella latenza di rete tra una macchina virtuale che esegue un'istanza di dialogo SAP nella stessa zona dell'istanza del DBMS attiva e una macchina virtuale simile in un'altra zona. Maggiore sarà questa differenza, più alto sarà l'impatto sul tempo di esecuzione dei processi aziendali e dei processi batch, a seconda che siano eseguiti nella stessa zona del sistema DBMS o in una zona diversa.

Quando si distribuiscono macchine virtuali di Azure in più zone di disponibilità e si implementano soluzioni di failover nella stessa area di Azure, si applicano alcune restrizioni:

- È necessario usare [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) quando si distribuisce nelle zone di disponibilità di Azure. 
- Il mapping delle enumerazioni di zona con le zone fisiche è fisso per ogni sottoscrizione di Azure. Se si usano sottoscrizioni diverse per distribuire i sistemi SAP, è necessario definire le zone ideali per ogni sottoscrizione.
- Non è possibile distribuire i set di disponibilità di Azure all'interno di una zona di disponibilità di Azure, a meno che non si usi il [gruppo di posizionamento](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) Il modo in cui è possibile distribuire il livello DBMS SAP e i servizi centrali tra le zone e allo stesso tempo distribuire il livello dell'applicazione SAP usando i set di disponibilità e ottenere comunque una prossimità più vicina delle VM è documentato nell'articolo [posizionamento di prossimità di Azure Gruppi per la latenza di rete ottimale con le applicazioni SAP](sap-proximity-placement-scenarios.md). Se non si usano i gruppi di posizionamento di prossimità di Azure, è necessario scegliere uno o l'altro come Framework di distribuzione per le macchine virtuali.
- Non è possibile usare un'istanza di [Azure Load Balancer Basic](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) per creare soluzioni cluster di failover basate su Windows Server Failover Clustering o Linux Pacemaker. È invece necessario usare lo [SKU di Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).



## <a name="the-ideal-availability-zones-combination"></a>Combinazione ideale delle zone di disponibilità
Prima di decidere come usare le zone di disponibilità, è necessario determinare:

- La latenza di rete tra le tre zone di un'area di Azure, in modo da poter scegliere le zone con la latenza di rete minore nel traffico di rete tra le zone.
- La differenza tra la latenza tra le macchine virtuali all'interno di una delle zone scelte e la latenza di rete tra le due zone scelte.
- Se i tipi di macchina virtuale che è necessario distribuire sono disponibili nelle due zone scelte. Con alcune macchine virtuali, in particolare le macchine virtuali serie M, possono verificarsi situazioni in cui alcuni SKU sono disponibili solo in due delle tre zone.

## <a name="network-latency-between-and-within-zones"></a>Latenza di rete tra e nelle zone
Per determinare qual è la latenza tra le varie zone, è necessario:

- Distribuire lo SKU di macchina virtuale che si vuole usare per l'istanza del DBMS in tutte e tre le zone. Assicurarsi che [Rete accelerata di Azure](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) sia abilitata durante l'esecuzione di questa misurazione.
- Dopo aver individuato le due zone con la minore latenza di rete, distribuire altre tre macchine virtuali dello SKU che si vuole usare come macchina virtuale del livello dell'applicazione nelle tre zone di disponibilità. Misurare la latenza di rete per le due macchine virtuali di DBMS nelle due zone DBMS selezionate. 
- Come strumento per la misurazione usare **niping**, lo strumento di SAP descritto nelle note di supporto SAP [#500235](https://launchpad.support.sap.com/#/notes/500235) e [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Prestare attenzione ai comandi documentati per le misurazioni della latenza. Poiché **ping** non funziona nei percorsi del codice di Rete accelerata di Azure, non è consigliabile usarlo.

In base alle misurazioni e alla disponibilità degli SKU di macchine virtuali nelle zone di disponibilità, è necessario prendere alcune decisioni:

- Definire le zone ideali per il livello DBMS.
- Stabilire se si vuole distribuire il livello dell'applicazione SAP attivo su una, due o tutte e tre le zone, in base alle differenze della latenza di rete all'interno di una zona o tra più zone.
- Stabilire se si vuole distribuire una configurazione attiva/passiva o una configurazione attiva/attiva dal punto di vista dell'applicazione. Queste configurazioni sono illustrate più avanti in questo articolo.

Nel prendere queste decisioni, tenere presenti anche le raccomandazioni per la latenza di rete di SAP documentate nella nota SAP [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> Le misurazioni e le decisioni sono valide per la sottoscrizione di Azure in uso quando si eseguono le misurazioni. Se si usa un'altra sottoscrizione di Azure, è necessario ripetere le misurazioni. Il mapping delle zone enumerate può essere diverso per un'altra sottoscrizione di Azure.


> [!IMPORTANT]
> È previsto che le misurazioni indicate in precedenza restituiscano risultati diversi in ogni area di Azure che supporta le [zone di disponibilità](https://docs.microsoft.com/azure/availability-zones/az-overview). Anche se i requisiti di latenza di rete non cambiano, può essere necessario adottare strategie di distribuzione diverse in diverse aree di Azure, perché la latenza di rete tra le zone può essere diversa. In alcune aree di Azure la latenza di rete fra le tre zone può essere notevolmente diversa. In altre aree la latenza di rete fra le tre zone può essere più uniforme. Non è corretto affermare che è sempre presente una latenza di rete compresa tra 1 e 2 millisecondi. La latenza di rete tra le zone di disponibilità nelle aree di Azure non può essere generalizzata.

## <a name="activeactive-deployment"></a>Distribuzione attiva/attiva
Questa architettura di distribuzione è denominata Active/Active perché i server applicazioni SAP attivi vengono distribuiti in due o tre zone. L'istanza di SAP Central Services che usa la replica di accodamento verrà distribuita tra due zone. Lo stesso vale per il livello DBMS, che verrà distribuito tra le stesse zone di SAP Central Services.

Quando si considera questa configurazione, è necessario trovare le due zone di disponibilità nell'area di appartenenza che offrono una latenza di rete tra zone accettabile per il carico di lavoro e per la replica DBMS sincrona. È anche opportuno assicurarsi che la differenza tra la latenza di rete all'interno delle zone selezionate e tra le zone non sia eccessiva. Questo perché occorre evitare variazioni troppo estese, a seconda che un processo venga eseguito all'interno della stessa zona del server DBMS o in zone diverse, nei tempi di esecuzione dei processi aziendali o dei processi batch. Alcune variazioni sono accettabili, ma non differenze sostanziali.

Uno schema semplificato di una distribuzione attiva/attiva su due zone può essere simile al seguente:

![Distribuzione attiva/attiva in una zona](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

A questa configurazione si applicano le considerazioni seguenti:

- Non usando il [gruppo di posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location), si considera il zone di disponibilità di Azure come domini di errore e di aggiornamento per tutte le VM perché i set di disponibilità non possono essere distribuiti in zone di disponibilità di Azure.
- Se si vuole combinare le distribuzioni di zona per il livello DBMS e i servizi centrali, ma si vogliono usare i set di disponibilità di Azure per il livello dell'applicazione, è necessario usare i gruppi di prossimità di Azure come descritto nell'articolo [gruppi di posizionamento di prossimità di Azure per Optimal latenza di rete con le applicazioni SAP](sap-proximity-placement-scenarios.md).
- Per i servizi di bilanciamento del carico per i cluster di failover di SAP Central Services e per il livello DBMS, è necessario usare lo [SKU di Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Load Balancer Basic non funziona tra zone.
- La rete virtuale di Azure distribuita per ospitare il sistema SAP, unita alle relative subnet, è estesa tra le zone. Non è necessario separare le reti virtuali per ogni zona.
- Per tutte le macchine virtuali distribuite, è necessario usare [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). I dischi non gestiti non sono supportati per le distribuzioni nelle zone.
- L'archiviazione Premium di Azure e l'[archiviazione Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) non supportano alcun tipo di replica dell'archiviazione tra le zone. L'applicazione (DBMS o SAP Central Services) deve replicare i dati importanti.
- Lo stesso vale per la directory sapmnt condivisa, ovvero un disco condiviso (Windows), una condivisione CIFS (Windows) o una condivisione NFS (Linux). È necessario usare una tecnologia che esegue la replica di tali dischi condivisi o condivisioni tra le zone. Sono supportate le tecnologie seguenti:
  - Per Windows, una soluzione cluster che usa SIOS DataKeeper, come documentato in [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
  - Per SUSE Linux, una condivisione NFS configurata come documentato in [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
    Attualmente la soluzione che usa File server di scalabilità orizzontale Microsoft, come documentato in [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), non è supportata tra le zone.
- La terza zona viene usata per ospitare il dispositivo SBD nel caso si creino un [cluster Pacemaker di SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) o altre istanze dell'applicazione.
- Per ottenere la coerenza del tempo di esecuzione per i processi aziendali critici, è possibile provare a indirizzare determinati utenti e processi batch alle istanze dell'applicazione che si trovano in zona con l'istanza di DBMS attiva usando i gruppi di server di batch SAP, i gruppi di accesso SAP o i gruppi RFC. In caso di failover di zona, è tuttavia necessario spostare manualmente questi gruppi in istanze in esecuzione su macchine virtuali nella stessa zona della macchina virtuale del database attivo.  
- Può essere necessario distribuire le istanze di dialogo inattive in ognuna delle zone. In questo modo, viene attivata una restituzione immediata alla capacità di risorse precedente, se una zona usata da parte delle istanze dell'applicazione è fuori servizio.


## <a name="activepassive-deployment"></a>Distribuzione attiva/passiva
Se non si riesce a trovare una differenza accettabile tra la latenza di rete all'interno del traffico di rete di una zona e tra zone, è possibile distribuire un'architettura attiva/passiva dal punto di vista del livello dell'applicazione SAP. Occorre definire una zona *attiva*, che è la zona in cui si distribuisce il livello dell'applicazione completo e in cui si tenta di eseguire sia l'istanza DBMS attiva che l'istanza di SAP Central Services. Con questa configurazione, è necessario assicurarsi di non avere variazioni estreme a livello di tempi di esecuzione, a seconda che un processo venga eseguito o meno nella stessa zona dell'istanza DBMS attiva, per le transazioni aziendali e i processi batch.

Il layout di base di questa architettura è simile al seguente:

![Distribuzione attiva/passiva in una zona](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

A questa configurazione si applicano le considerazioni seguenti:

- I set di disponibilità non possono essere distribuiti nelle zone di disponibilità di Azure. Per compensare tale situazione, è possibile usare i gruppi di posizionamento di prossimità di Azure, come documentato nell'articolo [gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP](sap-proximity-placement-scenarios.md).
- Quando si usa questa architettura, è necessario monitorare attentamente lo stato e provare a mantenere le istanze di DBMS e di SAP Central Services attive nella stessa zona del livello dell'applicazione distribuita. In caso di failover dell'istanza di SAP Central Services o DBMS, è opportuno assicurarsi che sia possibile eseguire manualmente il failback nella zona con il livello dell'applicazione SAP distribuita non appena possibile.
- Per i servizi di bilanciamento del carico per i cluster di failover di SAP Central Services e per il livello DBMS, è necessario usare lo [SKU di Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Load Balancer Basic non funziona tra zone.
- La rete virtuale di Azure distribuita per ospitare il sistema SAP, unita alle relative subnet, è estesa tra le zone. Non è necessario separare le reti virtuali per ogni zona.
- Per tutte le macchine virtuali distribuite, è necessario usare [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). I dischi non gestiti non sono supportati per le distribuzioni nelle zone.
- L'archiviazione Premium di Azure e l'[archiviazione Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) non supportano alcun tipo di replica dell'archiviazione tra le zone. L'applicazione (DBMS o SAP Central Services) deve replicare i dati importanti.
- Lo stesso vale per la directory sapmnt condivisa, ovvero un disco condiviso (Windows), una condivisione CIFS (Windows) o una condivisione NFS (Linux). È necessario usare una tecnologia che esegue la replica di tali dischi condivisi o condivisioni tra le zone. Sono supportate le tecnologie seguenti:
    - Per Windows, una soluzione cluster che usa SIOS DataKeeper, come documentato in [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Per SUSE Linux, una condivisione NFS configurata come documentato in [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
  Attualmente la soluzione che usa File server di scalabilità orizzontale Microsoft, come documentato in [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), non è supportata tra le zone.
- La terza zona viene usata per ospitare il dispositivo SBD nel caso si creino un [cluster Pacemaker di SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) o altre istanze dell'applicazione.
- È necessario distribuire le macchine virtuali inattive nella zona passiva (dal punto di vista del sistema DBMS) per poter avviare le risorse dell'applicazione in caso di errore della zona.
    - [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) non è attualmente in grado di replicare le macchine virtuali attive nelle macchine virtuali inattive tra zone. 
- È necessario investire in una soluzione di automazione che consenta, in caso di errore di una zona, di avviare automaticamente il livello dell'applicazione SAP nella seconda zona.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Configurazione combinata per disponibilità elevata e ripristino di emergenza
Microsoft non condivide alcuna informazione sulla distanza geografica fra le strutture che ospitano diverse zone di disponibilità in un'area di Azure. Nonostante ciò, alcuni clienti usano le zone per una configurazione di disponibilità elevata e ripristino di emergenza combinata che promette un obiettivo del punto di ripristino (RPO, Recovery Point Objective) pari a zero. Questo significa che non si dovrebbe perdere alcuna transazione di database di cui è stato eseguito il commit, anche in caso di ripristino di emergenza. 

> [!NOTE]
> È consigliabile usare una configurazione simile alla seguente solo in determinate circostanze. È ad esempio possibile usarla quando i dati non possono essere spostati fuori dall'area di Azure per motivi di sicurezza o conformità. 

Ecco un esempio di una configurazione di questo tipo:

![Disponibilità elevata e ripristino di emergenza combinati nelle zone](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

A questa configurazione si applicano le considerazioni seguenti:

- Si presuppone che esista una distanza significativa tra le strutture che ospitano una zona di disponibilità, in caso contrario è obbligatorio rimanere all'interno di una determinata area di Azure. I set di disponibilità non possono essere distribuiti nelle zone di disponibilità di Azure. Per compensare tale situazione, è possibile usare i gruppi di posizionamento di prossimità di Azure, come documentato nell'articolo [gruppi di posizionamento di prossimità di Azure per la latenza di rete ottimale con le applicazioni SAP](sap-proximity-placement-scenarios.md).
- Quando si usa questa architettura, è necessario monitorare attentamente lo stato e provare a mantenere le istanze di DBMS e di SAP Central Services attive nella stessa zona del livello dell'applicazione distribuita. In caso di failover dell'istanza di SAP Central Services o DBMS, è opportuno assicurarsi che sia possibile eseguire manualmente il failback nella zona con il livello dell'applicazione SAP distribuita non appena possibile.
- Esistono istanze dell'applicazione di produzione pre-installate nelle macchine virtuali che eseguono istanze dell'applicazione di controllo qualità attive.
- In caso di errore di una zona, si arrestano le istanze dell'applicazione di controllo qualità e si avviano invece le istanze di produzione. Tenere presente che è necessario usare nomi virtuali per le istanze dell'applicazione per far funzionare questo meccanismo.
- Per i servizi di bilanciamento del carico per i cluster di failover di SAP Central Services e per il livello DBMS, è necessario usare lo [SKU di Azure Load Balancer Standard](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Load Balancer Basic non funziona tra zone.
- La rete virtuale di Azure distribuita per ospitare il sistema SAP, unita alle relative subnet, è estesa tra le zone. Non è necessario separare le reti virtuali per ogni zona.
- Per tutte le macchine virtuali distribuite, è necessario usare [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). I dischi non gestiti non sono supportati per le distribuzioni nelle zone.
- L'archiviazione Premium di Azure e l'[archiviazione Ultra SSD](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) non supportano alcun tipo di replica dell'archiviazione tra le zone. L'applicazione (DBMS o SAP Central Services) deve replicare i dati importanti.
- Lo stesso vale per la directory sapmnt condivisa, ovvero un disco condiviso (Windows), una condivisione CIFS (Windows) o una condivisione NFS (Linux). È necessario usare una tecnologia che esegue la replica di tali dischi condivisi o condivisioni tra le zone. Sono supportate le tecnologie seguenti:
    - Per Windows, una soluzione cluster che usa SIOS DataKeeper, come documentato in [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - Per SUSE Linux, una condivisione NFS configurata come documentato in [Disponibilità elevata per NFS in macchine virtuali di Azure su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).

  Attualmente la soluzione che usa File server di scalabilità orizzontale Microsoft, come documentato in [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), non è supportata tra le zone.
- La terza zona viene usata per ospitare il dispositivo SBD nel caso si creino un [cluster Pacemaker di SUSE Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) o altre istanze dell'applicazione.





## <a name="next-steps"></a>Passaggi successivi
Ecco alcuni passaggi da eseguire successivamente per la distribuzione in più zone di disponibilità di Azure:

- [Clustering di un'istanza SAP ASCS/SCS in un cluster di failover Windows tramite un disco condiviso del cluster in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Preparare l'infrastruttura di Azure per la disponibilità elevata di SAP con un cluster di failover Windows e la condivisione di file per le istanze di SAP ASCS/SCS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






