---
title: Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure | Microsoft Docs
description: Guida operativa per i sistemi SAP HANA distribuiti in macchine virtuali di Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/01/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7c4f3ec2727d06528eab788a2a24a6190fe26533
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81606136"
---
# <a name="sap-hana-infrastructure-configurations-and-operations-on-azure"></a>Configurazioni e operazioni dell'infrastruttura SAP HANA in Azure
Questa guida contiene le indicazioni necessarie per configurare l'infrastruttura di Azure e gestire i sistemi SAP HANA distribuiti in macchine virtuali native di Azure. Il documento include anche informazioni sulla configurazione per lo scale-out di SAP HANA per lo SKU di VM M128s. Questo documento non deve sostituire la documentazione standard di SAP, che include:

- [Guida all'amministrazione di SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/330e5550b09d4f0f8b6cceb14a64cd22.html)
- [Guide all'installazione di SAP](https://service.sap.com/instguides)
- [Note di SAP](https://service.sap.com/notes)

## <a name="prerequisites"></a>Prerequisiti
Per usare questa guida sono necessarie conoscenze di base dei componenti di Azure seguenti:

- [Macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm)
- [Rete e reti virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-virtual-network)
- [Archiviazione di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-disks)

Per altre informazioni su SAP NetWeaver e altri componenti SAP in Azure, vedere la sezione [SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) della [documentazione di Azure](https://docs.microsoft.com/azure/).

## <a name="basic-setup-considerations"></a>Considerazioni di base sulla configurazione
Le sezioni seguenti offrono considerazioni di base sulla configurazione per la distribuzione di sistemi SAP HANA in macchine virtuali di Azure.

### <a name="connect-into-azure-virtual-machines"></a>Connettersi nelle macchine virtuali di Azure
Come illustrato nella [Guida alla pianificazione di macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide), sono disponibili due metodi di base per la connessione nelle macchine virtuali di Azure:

- Connessione tramite Internet ed endpoint pubblici in una macchina virtuale di collegamento o in una macchina virtuale che esegue SAP HANA.
- Connessione tramite una rete [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal) o Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

Per gli scenari di produzione è necessaria la connettività da sito a sito tramite VPN o ExpressRoute. Questo tipo di connessione è necessario anche per gli scenari non di produzione che si inseriscono negli scenari di produzione in cui viene usato il software SAP. La figura seguente mostra un esempio di connettività intersito:

![Connettività intersito](media/virtual-machines-shared-sap-planning-guide/300-vpn-s2s.png)


### <a name="choose-azure-vm-types"></a>Scegliere i tipi di macchine virtuali di Azure
I tipi di macchine virtuali di Azure che possono essere usati per gli scenari di produzione sono elencati nella [documentazione SAP per IAAS](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html). Per gli scenari non di produzione è disponibile una gamma più ampia di macchine virtuali native di Azure.

>[!NOTE]
> Per gli scenari non di produzione, usare i tipi di macchine virtuali elencati nella [nota di SAP #1928533](https://launchpad.support.sap.com/#/notes/1928533). Per l'utilizzo di macchine virtuali di Azure per gli scenari di produzione, scegliere macchine virtuali con certificazione SAP HANA nell'[elenco di piattaforme IaaS certificate](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) pubblicato da SAP.

Per distribuire le macchine virtuali in Azure, usare:

- Il portale di Azure.
- I cmdlet di Azure PowerShell.
- L'interfaccia della riga di comando di Azure.

È anche possibile distribuire una piattaforma SAP HANA installata completa nei servizi di macchine virtuali di Azure tramite la [piattaforma SAP Cloud](https://cal.sap.com/). Per una descrizione del processo di installazione, vedere [Distribuire SAP S/4HANA o BW/4HANA in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) oppure usare lo script di automazione rilasciato [qui](https://github.com/AzureCAT-GSI/SAP-HANA-ARM).

>[!IMPORTANT]
> Per usare M208xx_v2 macchine virtuali, è necessario prestare attenzione alla selezione dell'immagine Linux dalla raccolta immagini della macchina virtuale di Azure. Per leggere i dettagli, vedere l'articolo dimensioni delle [macchine virtuali con ottimizzazione](../../mv2-series.md)per la memoria.
> 


### <a name="storage-configuration-for-sap-hana"></a>Configurazione dell'archiviazione per SAP HANA
Per le configurazioni di archiviazione e i tipi di archiviazione da usare con SAP HANA in Azure, leggere il documento [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](./hana-vm-operations-storage.md)


### <a name="set-up-azure-virtual-networks"></a>Configurare le reti virtuali di Azure
Dopo aver stabilito la connettività da sito a sito in Azure tramite VPN o ExpressRoute, è necessario avere almeno una rete virtuale di Azure che sia connessa tramite un gateway virtuale al circuito VPN o ExpressRoute. Nelle distribuzioni semplici il gateway virtuale può essere distribuito in una subnet della rete virtuale di Azure che ospita anche le istanze di SAP HANA. Per installare SAP HANA, si creano altre due subnet all'interno della rete virtuale di Azure. Una subnet ospita le macchine virtuali necessarie per eseguire le istanze di SAP HANA, mentre l'altra esegue macchine virtuali Jumpbox o di gestione per ospitare SAP HANA Studio, altro software di gestione o il software dell'applicazione.

> [!IMPORTANT]
> Se non per motivi di funzionalità, ma più importante se non per motivi di prestazioni, non è supportato configurare [appliance virtuali di rete di Azure](https://azure.microsoft.com/solutions/network-appliances/) nel percorso di comunicazione tra l'applicazione SAP e il livello DBMS di un sistema SAP basato su SAP NetWeaver, Hybris o S/4 HANA. La comunicazione tra il livello applicazione SAP e il livello DBMS deve essere diretta. La limitazione non include le [regole Azure ASG e NSG](https://docs.microsoft.com/azure/virtual-network/security-overview), a condizione che queste regole ASG e NSG consentano la comunicazione diretta. Altri scenari in cui non sono supportate appliance virtuali di rete sono nei percorsi di comunicazione tra macchine virtuali di Azure che rappresentano i nodi del cluster Pacemaker di Linux e i dispositivi SBD, come descritto in [Disponibilità elevata per SAP NetWeaver su macchine virtuali di Azure in SUSE Linux Enterprise Server for SAP applications](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse). Oppure nei percorsi di comunicazione tra macchine virtuali di Azure e Windows Server SOFS configurati come descritto in [Clustering di un'istanza ASCS/SCS di SAP in un cluster di failover Windows tramite una condivisione file in Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). Le appliance virtuali di rete nei percorsi di comunicazione possono facilmente raddoppiare la latenza di rete tra due partner di comunicazione e possono limitare la velocità effettiva nei percorsi critici tra il livello applicazione SAP e il livello DBMS. In alcuni scenari esaminati con i clienti, le appliance virtuali di rete possono causare errori dei cluster Linux Pacemaker nei casi in cui le comunicazioni tra i nodi del cluster Linux Pacemaker e il relativo dispositivo SBD avvengono tramite un'appliance virtuale di rete.  
> 

> [!IMPORTANT]
> Un'altra struttura **NON** supportata è la separazione del livello dell'applicazione SAP e del livello DBMS in reti virtuali Azure diverse non [peer](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). È consigliabile separare il livello dell'applicazione SAP e il livello DBMS usando subnet all'interno di una rete virtuale di Azure, anziché usare reti virtuali di Azure diverse. Se si decide di non seguire il consiglio e separare i due livelli in reti virtuali diverse, le due reti virtuali devono essere [peer](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). Tenere presente che il traffico di rete tra due reti virtuali [peer](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) di Azure è soggetto a costi di trasferimento. Se il livello dell'applicazione SAP e il livello DBMS vengono separati tra due reti virtuali Azure peer, i grandi volumi di dati (dell'ordine di molti terabyte) scambiati tra il livello dell'applicazione SAP e il livello DBMS possono originare costi importanti. 

Quando si installano le macchine virtuali per l'esecuzione di SAP HANA, queste macchine virtuali devono essere dotate di:

- Due schede di interfaccia di rete virtuali installate: una si connette alla subnet di gestione e l'altra viene usata per la connessione dalla rete locale o da altre reti all'istanza di SAP HANA nella macchina virtuale di Azure.
- Indirizzi IP privati statici distribuiti per entrambe le schede di interfaccia di rete virtuali.

> [!NOTE]
> È consigliabile assegnare indirizzi IP statici tramite Azure alle singole schede di interfaccia di rete virtuali. Non è opportuno assegnare indirizzi IP statici all'interno del sistema operativo guest a una scheda di interfaccia di rete virtuale. Alcuni servizi di Azure, come Backup di Azure, si basano sul fatto che almeno la scheda di interfaccia di rete virtuale primaria sia impostata su DHCP e non su indirizzi IP statici. Vedere anche il documento [Risolvere i problemi relativi al backup delle macchine virtuali di Azure](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Se è necessario assegnare più indirizzi IP statici a una macchina virtuale, occorre assegnare più schede di interfaccia di rete virtuali a una macchina virtuale.
>
>

Per le distribuzioni permanenti, è tuttavia necessario creare un'architettura di rete del data center virtuale in Azure. Questa architettura consiglia la separazione del gateway VNet di Azure che si connette al sito locale in una VNet di Azure separata. Questo VNet separato deve ospitare tutto il traffico che lascia in locale o in Internet. Questo approccio consente di distribuire il software per il controllo e la registrazione del traffico che entra nel data center virtuale in Azure in questa rete virtuale dell'hub separata. Si avrà quindi una rete virtuale che ospita tutto il software e le configurazioni, correlata al traffico in ingresso e in uscita nella distribuzione di Azure.

Gli articoli [Data center virtuale di Microsoft Azure: una prospettiva di rete](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter) e [Data center virtuale di Azure e piano di controllo aziendale](https://docs.microsoft.com/azure/architecture/vdc/) forniscono altre informazioni sull'approccio basato su data center virtuale e sulla struttura della rete virtuale di Azure correlata.


>[!NOTE]
>Il traffico trasmesso tra una rete virtuale dell'hub e la rete virtuale spoke con il [peering reti virtuali di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) è soggetto a [costi](https://azure.microsoft.com/pricing/details/virtual-network/) aggiuntivi. In base a tali costi, potrebbe essere necessario valutare l'opportunità di un compromesso tra l'esecuzione di una struttura di rete hub-spoke rigorosa e l'esecuzione di più [gateway di Azure ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways) da connettere agli "spoke" per ignorare il peering reti virtuali. Anche i gateway di Azure ExpressRoute comportano tuttavia [costi](https://azure.microsoft.com/pricing/details/vpn-gateway/) aggiuntivi. È anche possibile incorrere in costi aggiuntivi per il software di terze parti usato per la registrazione, il controllo e il monitoraggio del traffico di rete. A seconda dei costi per lo scambio di dati tramite il peering reti virtuali da un lato e dei costi creati dai gateway di Azure ExpressRoute aggiuntivi e dalle licenze software aggiuntive, è possibile decidere per la micro-segmentazione all'interno di una rete virtuale usando le subnet come unità di isolamento invece delle reti virtuali.


Per una panoramica dei diversi metodi per l'assegnazione di indirizzi IP, vedere [Tipi di indirizzi IP e metodi di allocazione in Azure](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). 

Per le macchine virtuali che eseguono SAP HANA, è consigliabile usare indirizzi IP statici assegnati, perché alcuni attributi di configurazione per HANA fanno riferimento a indirizzi IP.

I [gruppi di sicurezza di rete di Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) vengono usati per indirizzare il traffico instradato all'istanza di SAP HANA o al jumpbox. I gruppi di sicurezza di rete ed eventualmente i [gruppi di sicurezza delle applicazioni](https://docs.microsoft.com/azure/virtual-network/security-overview#application-security-groups) sono associati alla subnet di SAP HANA e alla subnet di gestione.

La figura seguente mostra una panoramica di uno schema di distribuzione approssimativo per SAP HANA che segue un'architettura di rete virtuale hub-spoke:

![Schema di distribuzione approssimativo per SAP HANA](media/hana-vm-operations/hana-simple-networking-dmz.png)

Per distribuire SAP HANA in Azure senza una connessione da sito a sito, è comunque necessario schermare l'istanza di SAP HANA da Internet pubblico e nasconderla dietro un proxy di inoltro. In questo scenario di base la distribuzione si basa su servizi DNS predefiniti di Azure per risolvere i nomi host. Questi servizi sono particolarmente importanti in distribuzioni più complesse in cui vengono usati indirizzi IP pubblici. Usare i gruppi di sicurezza di rete di Azure e le [appliance virtuali di rete di Azure](https://azure.microsoft.com/solutions/network-appliances/) per controllare e monitorare il routing da Internet all'architettura di rete virtuale di Azure in Azure. L'immagine seguente mostra uno schema approssimativo per la distribuzione di SAP HANA senza connessione da sito a sito in un'architettura di rete virtuale hub-spoke:
  
![Schema di distribuzione approssimativo per SAP HANA senza connessione da sito a sito](media/hana-vm-operations/hana-simple-networking-dmz.png)
 

Per un'altra descrizione di come usare le appliance virtuali di rete di Azure per controllare e monitorare l'accesso da Internet senza l'architettura di rete virtuale hub-spoke, vedere l'articolo [Distribuire appliance virtuali di rete con disponibilità elevata](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha).


## <a name="configuring-azure-infrastructure-for-sap-hana-scale-out"></a>Configurazione dell'infrastruttura di Azure per lo scale-out di SAP HANA
Per individuare i tipi di VM di Azure certificati per la scalabilità orizzontale OLAP o S/4HANA, controllare la [directory dell'hardware SAP Hana](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure). Un segno di spunta nella colonna ' clustering ' indica il supporto della scalabilità orizzontale. Tipo di applicazione indica se la scalabilità orizzontale OLAP o S/4HANA è supportata. Per informazioni dettagliate sui nodi certificati per la scalabilità orizzontale per ognuna delle macchine virtuali, vedere i dettagli delle voci nello SKU di VM specifico elencato nella directory dell'hardware SAP HANA.

Le versioni minime del sistema operativo per la distribuzione di configurazioni con scalabilità orizzontale in macchine virtuali di Azure, verificano i dettagli delle voci nello SKU di VM specifico elencato nella directory dell'hardware SAP HANA. Di una configurazione con scalabilità orizzontale di OLAP a n nodi, un nodo funziona come nodo master. Gli altri nodi fino al limite della certificazione agiscono da nodo del ruolo di lavoro. I nodi di standby aggiuntivi non sono conteggiati nel numero di nodi certificati

>[!NOTE]
> Le distribuzioni con scalabilità orizzontale di macchine virtuali di Azure di SAP HANA con il nodo standby sono possibili solo tramite l'archiviazione [Azure NetApp files](https://azure.microsoft.com/services/netapp/) . Nessun'altra SAP HANA archiviazione di Azure certificata consente la configurazione di SAP HANA nodi standby
>

Per/Hana/Shared, è anche consigliabile utilizzare [Azure NetApp files](https://azure.microsoft.com/services/netapp/). 

Una progettazione di base tipica per un singolo nodo in una configurazione con scalabilità orizzontale sarà simile alla seguente:

![Nozioni di base sullo scale-out di un singolo nodo](media/hana-vm-operations/scale-out-basics-anf-shared.PNG)

La configurazione di base di un nodo di macchina virtuale per lo scale-out di SAP HANA è simile alla seguente:

- Per **/Hana/Shared**, è possibile usare il servizio NFS nativo fornito tramite Azure NetApp files. 
- Tutti gli altri volumi del disco non sono condivisi tra i diversi nodi e non sono basati su NFS. Le configurazioni di installazione e i passaggi per le installazioni HANA con scalabilità orizzontale con **/Hana/data** e **/Hana/log** non condivisi sono disponibili più avanti in questo documento. Per l'archiviazione con certificazione HANA che è possibile usare, vedere l'articolo [SAP Hana configurazioni di archiviazione delle macchine virtuali di Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage).


Ridimensionare i volumi o i dischi, è necessario controllare il documento [SAP Hana requisiti di archiviazione TDI](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html), per le dimensioni richieste a seconda del numero di nodi del ruolo di lavoro. Il documento rilascia una formula che è necessario applicare per ottenere la capacità necessaria del volume

Gli altri criteri di progettazione visualizzati nella grafica della configurazione a nodo singolo per una macchina virtuale SAP HANA con scalabilità orizzontale sono VNet o meglio la configurazione della subnet. SAP consiglia di separare il traffico destinato ai client o alle applicazioni dalle comunicazioni tra i nodi HANA. Come illustrato nelle immagini, questo obiettivo viene raggiunto con due diverse schede di interfaccia di rete virtuale collegate alla VM. Le due schede di interfaccia di rete virtuale si trovano in subnet diverse e hanno due indirizzi IP diversi. È quindi possibile controllare il flusso del traffico con regole di routing che usano gruppi di sicurezza di rete o route definite dall'utente.

In particolare in Azure non esistono mezzi e metodi per applicare Qualità del servizio (QoS) e quote in schede di interfaccia di rete virtuale specifiche. Di conseguenza, la separazione delle comunicazioni tra i nodi e verso i client o le applicazioni non consente in alcun modo di stabilire la priorità di un flusso di traffico su un altro. La separazione rimane invece una misura di sicurezza per proteggere le comunicazioni tra i nodi delle configurazioni con scale-out.  

>[!NOTE]
>SAP consiglia di separare il traffico di rete verso il lato client/applicazione e il traffico intra-nodo come descritto in questo documento. Per questo motivo, è consigliabile applicare un'architettura, come illustrato nell'ultima grafica. Consultare anche il team di sicurezza e conformità per i requisiti che deviano dalla raccomandazione 
>

Per quanto riguarda la rete, l'architettura minima richiesta sarà simile alla seguente:

![Nozioni di base sullo scale-out di un singolo nodo](media/hana-vm-operations/overview-scale-out-networking.png)



### <a name="installing-sap-hana-scale-out-n-azure"></a>Installazione dello scale-out SAP HANA in Azure
Per installare una configurazione SAP con scale-out, è necessario eseguire i passaggi generali seguenti:

- Distribuzione di una nuova infrastruttura di Azure VNet esistente
- Distribuzione delle nuove macchine virtuali con archiviazione Premium gestita di Azure, volumi di dischi Ultra e/o volumi NFS basati su e
- - Adattare il routing di rete per assicurarsi che, ad esempio, la comunicazione tra nodi tra le macchine virtuali non venga instradata tramite un'[appliance virtuale di rete](https://azure.microsoft.com/solutions/network-appliances/). 
- Installare il nodo master SAP HANA.
- Adattare i parametri di configurazione del nodo master SAP HANA
- Continuare l'installazione dei nodi di lavoro SAP HANA

#### <a name="installation-of-sap-hana-in-scale-out-configuration"></a>Installazione di SAP HANA nella configurazione con scale-out
Quando viene distribuita l'infrastruttura di VM di Azure e vengono eseguite tutte le altre operazioni di preparazione, è necessario installare le configurazioni con scale-out di SAP HANA seguendo questi passaggi:

- Installare il nodo master SAP HANA in base alla documentazione di SAP
- Se si usa archiviazione Premium di Azure o l'archiviazione su disco Ultra con dischi non condivisi di/Hana/data e/Hana/log, è necessario modificare il file Global. ini e aggiungere il parametro ' basepath_shared = No ' al file Global. ini. Questo parametro consente di eseguire SAP HANA in scalabilità orizzontale senza volumi ' Shared ' **/Hana/data** e **/Hana/log** tra i nodi. Informazioni dettagliate sono fornite nella [nota SAP n. 2080991](https://launchpad.support.sap.com/#/notes/2080991). Se si usano volumi NFS basati su e per/Hana/data e/Hana/log, non è necessario apportare questa modifica
- Dopo l'eventuale modifica apportata al parametro Global. ini, riavviare l'istanza di SAP HANA
- Aggiungere altri nodi di lavoro. Vedere anche <https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.00/en-US/0d9fe701e2214e98ad4f8721f6558c34.html>. Specificare la rete interna per la comunicazione tra nodi di SAP HANA durante l'installazione o in seguito usando, ad esempio, hdblcm locale. Per una documentazione più dettagliata, vedere anche la [nota SAP n. 2183363](https://launchpad.support.sap.com/#/notes/2183363). 

Per informazioni dettagliate sulla configurazione di un sistema di SAP HANA con scalabilità orizzontale con un nodo standby in SUSE Linux, fare in modo dettagliato in [distribuire un SAP Hana sistema con scalabilità orizzontale con nodo standby nelle VM di Azure usando Azure NetApp files in SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse). La documentazione equivalente per Red Hat è disponibile nell'articolo [distribuire un SAP Hana sistema con scalabilità orizzontale con un nodo di standby in macchine virtuali di Azure usando Azure NetApp files in Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel). 


## <a name="sap-hana-dynamic-tiering-20-for-azure-virtual-machines"></a>SAP HANA Dynamic Tiering 2.0 per macchine virtuali di Azure

Oltre alle certificazioni di SAP HANA nelle macchine virtuali della serie M di Azure, anche SAP HANA Dynamic Tiering 2.0 è supportato in Microsoft Azure. Vedere più avanti i collegamenti alla documentazione di SAP HANA Dynamic Tiering. Anche se non esistono differenze nell'installazione o nel funzionamento del prodotto, ad esempio, tramite il pannello di controllo di SAP HANA in una macchina virtuale, alcuni elementi importanti sono obbligatori per il supporto ufficiale in Azure. Di seguito vengono descritti questi punti chiave. Nell'articolo verrà usata l'abbreviazione "DT 2,0" anziché il nome completo a livelli dinamici 2,0.

SAP HANA Dynamic Tiering 2.0 non è supportato da SAP BW o S4HANA. I casi d'uso principali sono attualmente le applicazioni HANA native.


### <a name="overview"></a>Panoramica

La figura seguente offre una panoramica del supporto di DT 2.0 in Microsoft Azure. Esiste un set di requisiti obbligatori, che deve essere seguito per garantire la conformità con la certificazione ufficiale:

- DT 2.0 deve essere installato in una VM di Azure dedicata. Non può essere eseguito nella stessa VM in cui viene eseguito SAP HANA
- Le VM SAP HANA e DT 2.0 devono essere distribuite nella stessa rete virtuale di Azure
- Le VM SAP HANA e DT 2.0 devono essere distribuite con la rete accelerata di Azure abilitata
- Il tipo di archiviazione per le VM DT 2.0 deve essere Archiviazione premium di Azure
- Alla VM DT 2.0 devono essere collegati più dischi di Azure
- È necessario creare un volume RAID/con striping software (tramite lvm o mdadm) usando lo striping tra i dischi di Azure

Ulteriori dettagli verranno illustrati nelle sezioni riportate di seguito.

![Panoramica dell'architettura di SAP HANA DT 2.0](media/hana-vm-operations/hana-data-tiering.png)



### <a name="dedicated-azure-vm-for-sap-hana-dt-20"></a>VM di Azure dedicata per SAP HANA DT 2.0

In IaaS di Azure DT 2.0 è supportato solo in una VM dedicata. Non è consentito eseguire DT 2.0 nella stessa VM di Azure in cui è in esecuzione l'istanza di HANA. Inizialmente si possono usare due tipi di macchina virtuale per eseguire SAP HANA DT 2.0:

- M64-32ms 
- E32sv3 

Per la descrizione dei tipi di VM, vedere [qui](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Considerata l'idea alla base di DT 2.0, ovvero l'offloading dei dati ad accesso frequente per risparmiare sui costi, la scelta migliore è usare dimensioni di VM corrispondenti. Non esistono tuttavia regole rigide sulle possibili combinazioni. Tutto dipende dal carico di lavoro del cliente specifico.

Le configurazioni consigliate sono:

| Tipo di VM SAP HANA | Tipo di VM DT 2.0 |
| --- | --- | 
| M128ms | M64-32ms |
| M128s | M64-32ms |
| M64ms | E32sv3 |
| M64s | E32sv3 |


Sono possibili tutte le combinazioni di macchine virtuali della serie M certificate per SAP HANA con macchine virtuali DT 2.0 supportate (M64-32ms e E32sv3).


### <a name="azure-networking-and-sap-hana-dt-20"></a>Rete di Azure e SAP HANA DT 2.0

Per installare DT 2.0 in una VM dedicata, è necessaria una velocità effettiva di rete tra la VM DT 2.0 e la VM SAP HANA di almeno 10 GB. È quindi obbligatorio inserire tutte le VM nella stessa rete virtuale di Azure e abilitare la rete accelerata di Azure.

Per altre informazioni sulla rete accelerata di Azure, vedere [qui](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)

### <a name="vm-storage-for-sap-hana-dt-20"></a>Risorse di archiviazione delle macchine virtuali per SAP HANA DT 2.0

In base alle indicazioni sulle procedure consigliate di DT 2.0, la velocità effettiva di I/O dei dischi deve essere almeno di 50 MB/sec per ogni core fisico. Osservare le specifiche per i due tipi di VM di Azure, supportati per DT 2,0, il limite massimo di velocità effettiva di i/o del disco per la macchina virtuale è simile al seguente:

- E32sv3: 768 MB/sec (senza memorizzazione nella cache), ovvero un rapporto di 48 MB/sec per ogni core fisico
- M64-32ms: 1000 MB/sec (senza memorizzazione nella cache), ovvero un rapporto di 62,5 MB/sec per ogni core fisico

È necessario collegare più dischi di Azure alla VM DT 2.0 e creare un volume RAID (striping) di software a livello di sistema operativo per raggiungere il limite massimo della velocità effettiva dei dischi per ogni VM. Un singolo disco di Azure non può fornire la velocità effettiva per raggiungere il limite massimo per le VM in questo ambito. Archiviazione Premium di Azure è obbligatorio per eseguire DT 2.0. 

- Per informazioni dettagliate sui tipi di dischi di Azure, vedere [qui](../../windows/disks-types.md)
- Per informazioni dettagliate sulla creazione del volume RAID del software tramite mdadm, vedere [qui](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
- Per informazioni dettagliate sulla configurazione di LVM per creare un volume con striping per la velocità effettiva massima, vedere [qui](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)

A seconda dei requisiti delle dimensioni, sono disponibili diverse opzioni per raggiungere la velocità effettiva massima di una VM. Di seguito sono elencate le possibili configurazioni dei dischi volume di dati per ogni tipo di VM DT 2.0 per raggiungere il limite massimo di velocità effettiva della VM. La VM E32sv3 deve essere considerata come base per i carichi di lavoro più piccoli. Se non dovesse risultare abbastanza veloce, potrebbe essere necessario ridimensionare la VM a M64-32ms.
Poiché la VM M64-32ms ha una quantità elevata di memoria, il carico di I/O potrebbe non raggiungere il limite soprattutto per i carichi di lavoro che eseguono un'intensa attività di lettura. Un numero inferiore di dischi nel set di striping potrebbe quindi essere sufficiente a seconda del carico di lavoro specifico del cliente, ma, per sicurezza, le configurazioni dei dischi seguenti sono state scelte per garantire la velocità effettiva massima:


| SKU di VM | Configurazione disco 1 | Configurazione disco 2 | Configurazione disco 3 | Configurazione disco 4 | Configurazione disco 5 | 
| ---- | ---- | ---- | ---- | ---- | ---- | 
| M64-32ms | 4 x P50 -> 16 TB | 4 x P40 -> 8 TB | 5 x P30 -> 5 TB | 7 x P20 -> 3,5 TB | 8 x P15 -> 2 TB | 
| E32sv3 | 3 x P50 -> 12 TB | 3 x P40 -> 6 TB | 4 x P30 -> 4 TB | 5 x P20 -> 2,5 TB | 6 x P15 -> 1,5 TB | 


Soprattutto in caso di intensa attività di lettura del carico di lavoro, per incrementare le prestazioni di I/O, è possibile attivare la cache host di Azure "di sola lettura" consigliata per i volumi di dati del software del database. Per il log delle transazioni invece, la cache del disco host di Azure deve essere "nessuna". 

Per quanto riguarda le dimensioni del volume di log, è consigliabile iniziare con un 15% euristico delle dimensioni dei dati. La creazione del volume di log può essere eseguita usando tipi diversi di dischi di Azure a seconda del costo e dei requisiti di velocità effettiva. Per il volume di log è necessaria una velocità effettiva di I/O elevata.  Se si usa il tipo di macchina virtuale M64-32ms standard è obbligatorio abilitare [acceleratore di scrittura](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator). L'acceleratore di scrittura di Azure offre la latenza di scrittura su disco ottimale per il log delle transazioni (disponibile solo per la serie M). Esistono tuttavia alcuni elementi da considerare, ad esempio, il numero massimo di dischi per tipo di VM. [Qui](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator) sono disponibili informazioni dettagliate sull'acceleratore di scrittura


Ecco alcuni esempi di dimensioni del volume di log:

| dimensioni volume dati e tipo di disco | configurazione volume di log e tipo di disco 1 | configurazione volume di log e tipo di disco 2 |
| --- | --- | --- |
| 4 x P50 -> 16 TB | 5 x P20 -> 2,5 TB | 3 x P30 -> 3 TB |
| 6 x P15 -> 1,5 TB | 4 x P6 -> 256 GB | 1 x P15 -> 256 GB |


Come per lo scale-out di SAP HANA, la directory /hana/shared deve essere condivisa tra la VM SAP HANA e la VM DT 2.0. È consigliabile la stessa architettura dello scale-out di SAP HANA con VM dedicate, che fungono da server NFS a disponibilità elevata. Per fornire un volume di backup condiviso, si può usare la medesima progettazione, ma sarà il cliente a decidere se è necessaria la disponibilità elevata o se è possibile usare solo una VM dedicata con capacità di archiviazione sufficiente per fungere da server di backup.



### <a name="links-to-dt-20-documentation"></a>Collegamenti alla documentazione di DT 2.0 

- [SAP HANA Dynamic Tiering installation and update guide](https://help.sap.com/viewer/88f82e0d010e4da1bc8963f18346f46e/2.0.03/en-US) (Guida all'installazione e all'aggiornamento di SAP HANA Dynamic Tiering)
- [SAP HANA Dynamic Tiering tutorials and resources](https://help.sap.com/viewer/fb9c3779f9d1412b8de6dd0788fa167b/2.0.03/en-US) (Esercitazioni e risorse di SAP HANA Dynamic Tiering)
- [SAP HANA Dynamic Tiering PoC](https://blogs.sap.com/2017/12/08/sap-hana-dynamic-tiering-delivering-on-low-tco-with-impressive-performance/) (PoC di SAP HANA Dynamic Tiering)
- [SAP HANA 2.0 SPS 02 dynamic tiering enhancements](https://blogs.sap.com/2017/07/31/sap-hana-2.0-sps-02-dynamic-tiering-enhancements/) (Miglioramenti di SAP HANA 2.0 SPS 02 Dynamic Tiering)




## <a name="operations-for-deploying-sap-hana-on-azure-vms"></a>Operazioni per la distribuzione di SAP HANA in macchine virtuali di Azure
Le sezioni seguenti descrivono alcune delle operazioni correlate alla distribuzione di sistemi SAP HANA in macchine virtuali di Azure.

### <a name="back-up-and-restore-operations-on-azure-vms"></a>Operazioni di backup e ripristino nelle macchine virtuali di Azure
I documenti seguenti descrivono come eseguire il backup e il ripristino della distribuzione di SAP HANA:

- [Panoramica di SAP HANA backup](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
- [Backup di SAP HANA a livello di file](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
- [Benchmark dello snapshot di archiviazione SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)


### <a name="start-and-restart-vms-that-contain-sap-hana"></a>Avviare e riavviare le macchine virtuali che contengono SAP HANA
Una delle principali caratteristiche del cloud pubblico di Azure è che i costi vengono addebitati solo in base ai minuti di calcolo. Ad esempio, quando si arresta una macchina virtuale che esegue SAP HANA, durante l'intervallo di calcolo vengono fatturati solo i costi di archiviazione. Un'altra caratteristica si presenta quando si specificano gli indirizzi IP statici per le macchine virtuali nella distribuzione iniziale. Quando si riavvia una macchina virtuale con SAP HANA, la macchina viene riavviata con l'indirizzo IP precedente. 


### <a name="use-saprouter-for-sap-remote-support"></a>Usare SAProuter per il supporto remoto SAP
Se esiste una connessione da sito a sito tra le sedi locali e Azure e si eseguono componenti SAP, è probabile che SAProuter sia già in esecuzione. In questo caso, completare le attività seguenti per il supporto remoto:

- Gestire l'indirizzo IP privato e statico della macchina virtuale che ospita SAP HANA nella configurazione di SAProuter.
- Configurare il gruppo di sicurezza di rete della subnet che ospita la macchina virtuale HANA per consentire il traffico attraverso la porta TCP/IP 3299.

Se ci si connette ad Azure tramite Internet senza avere a disposizione un router SAP per la macchina virtuale con SAP HANA, è necessario installare il componente. Installare SAProuter in una macchina virtuale separata nella subnet di gestione. L'immagine seguente mostra uno schema approssimativo per la distribuzione di SAP HANA senza connessione da sito a sito e con SAProuter:

![Schema di distribuzione approssimativo per SAP HANA senza connessione da sito a sito e con SAProuter](media/hana-vm-operations/hana-simple-networking-saprouter.png)

Installare SAProuter in una macchina virtuale separata e non nella macchina virtuale Jumpbox. La macchina virtuale separata deve avere un indirizzo IP statico. Per connettere la propria istanza di SAProuter all'istanza di SAProuter ospitata da SAP, contattare SAP per chiedere un indirizzo IP. Il SAProuter ospitato da SAP è la controparte dell'istanza di SAProuter installata nella macchina virtuale. Usare l'indirizzo IP di SAP per configurare l'istanza di SAProuter. Nelle impostazioni di configurazione l'unica porta necessaria è la porta TCP 3299.

Per altre informazioni su come configurare e gestire le connessioni di supporto remoto tramite SAProuter, vedere la [documentazione di SAP](https://support.sap.com/en/tools/connectivity-tools/remote-support.html).

### <a name="high-availability-with-sap-hana-on-azure-native-vms"></a>Disponibilità elevata con SAP HANA in macchine virtuali native di Azure
Se si sta eseguendo SUSE Linux Enterprise Server o Red Hat, è possibile stabilire un cluster Pacemaker con dispositivi STONITH. È possibile usare i dispositivi per impostare una configurazione di SAP HANA che usi la replica sincrona con la replica di sistema HANA e il failover automatico. Per ulteriori informazioni elencate nella sezione "passaggi successivi".

## <a name="next-steps"></a>Passaggi successivi
Acquisire familiarità con gli articoli elencati
- [Configurazioni dell'archiviazione di macchine virtuali di Azure in SAP HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)
- [Distribuire un sistema di SAP HANA con scalabilità orizzontale con un nodo standby in macchine virtuali di Azure usando Azure NetApp Files su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-suse)
- [Distribuire un sistema di SAP HANA con scalabilità orizzontale con un nodo standby in macchine virtuali di Azure usando Azure NetApp Files su Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel)
- [Disponibilità elevata di SAP HANA in macchine virtuali di Azure su SUSE Linux Enterprise Server](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)
- [Disponibilità elevata di SAP HANA in macchine virtuali di Azure su Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel)

 

