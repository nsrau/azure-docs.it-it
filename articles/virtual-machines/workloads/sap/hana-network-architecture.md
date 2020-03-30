---
title: Architettura di rete di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Architettura di rete della distribuzione di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 33684a6292d7e51c04f6bacc7c49ee5986dbec10
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502408"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Architettura di rete di SAP HANA (istanze Large)

L'architettura dei servizi di rete di Azure è un componente fondamentale per la corretta distribuzione di applicazioni SAP in istanze Large di HANA. In genere, le distribuzioni di SAP HANA in Azure (istanze di grandi dimensioni) dispongono di un panorama applicativo di SAP più ampio con varie soluzioni SAP e varie dimensioni di database, utilizzo delle risorse della CPU e utilizzo della memoria. È probabile che non tutti i sistemi IT si trovino già in Azure.It's likely that not all IT systems are already located in Azure already. Il panorama SAP è spesso ibrido anche dal punto di vista dell'applicazione DBMS e SAP utilizzando una combinazione di NetWeaver e S/4HANA e SAP HANA e altri DBMS. Azure offers different services that allow you to run the different DBMS, NetWeaver, and S/4HANA systems in Azure. Azure offre anche la tecnologia di rete per rendere Azure simile a un data center virtuale per le distribuzioni software locali

A meno che i sistemi IT completi non siano ospitati in Azure.Unless your complete IT systems are hosted in Azure. La funzionalità di rete di Azure viene usata per connettere il mondo locale con le risorse di Azure per rendere Azure simile a un data center virtuale. La funzionalità di rete di Azure usata è:The Azure network functionality used is: 

- Le reti virtuali di Azure sono connesse al circuito [ExpressRoute](https://azure.microsoft.com/services/expressroute/) che si connette agli asset di rete locali.
- Un circuito ExpressRoute che si connette in locale ad Azure deve avere una larghezza di banda minima di [1 Gbps o superiore.](https://azure.microsoft.com/pricing/details/expressroute/) Questo minimo garantisce una larghezza di banda sufficiente per il trasferimento dei dati tra i sistemi locali e i sistemi in esecuzione nelle VM e per la connessione degli utenti locali ai sistemi di Azure.
- Tutti i sistemi SAP in Azure vengono impostati nelle reti virtuali per comunicare tra loro.
- Active Directory and DNS hosted on-premises are extended into Azure through ExpressRoute from on-premises, or are running complete in Azure.

Per il caso specifico di integrazione di istanze DI grandi dimensioni HANA nell'infrastruttura di rete del data center di Azure, viene usata anche la tecnologia Azure ExpressRoute


> [!NOTE] 
> Solo una sottoscrizione di Azure può essere collegata a un solo tenant in un timbro istanza di grandi dimensioni HANA in un'area di Azure specifica. Al contrario, un singolo tenant timbro istanza di grandi dimensioni HANA può essere collegato a una sola sottoscrizione di Azure.Conversaly, a single HANA Large Instance stamp tenant can be linked to only One Azure subscription. Questo requisito è coerente con quello di altri oggetti fatturabili in Azure.

Se SAP HANA in Azure (istanze di grandi dimensioni) viene distribuito in più aree di Azure diverse, un tenant separato viene distribuito nel timbro Istanza di grandi dimensioni HANA. È possibile eseguire entrambi nella stessa sottoscrizione di Azure purché queste istanze facciano parte dello stesso panorama applicativo SAP. 

> [!IMPORTANT] 
> Solo il metodo di distribuzione di Azure Resource Manager è supportato con SAP HANA in Azure (istanze di grandi dimensioni).

 

## <a name="additional-virtual-network-information"></a>Informazioni aggiuntive sulle reti virtuali

Per connettere una rete virtuale a ExpressRoute, è necessario creare un gateway di Azure ExpressRoute.To connect a virtual network to ExpressRoute, an Azure ExpressRoute gateway must be created. Per altre informazioni, vedere [Informazioni sui gateway Expressroute per ExpressRoute.](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

Un gateway Di Azure ExpressRoute viene usato con ExpressRoute per un'infrastruttura esterna ad Azure o per un timbro Istanza di grandi dimensioni di Azure.An Azure ExpressRoute gateway is used with ExpressRoute to an infrastructure outside of Azure or to an Azure Large Instance stamp. È possibile connettere il gateway di Azure ExpressRoute a un massimo di quattro circuiti ExpressRoute diversi, purché tali connessioni provengano da diversi router perimetrali aziendali Microsoft.You can connect the Azure ExpressRoute gateway to a maximum of four different ExpressRoute circuits as long as those connections come from different Microsoft enterprise edge routers. Per altre informazioni, vedere [Infrastruttura e connettività SAP HANA (istanze di grandi dimensioni) in Azure.For more information, see SAP HANA (Large Instances) infrastructure and connectivity on Azure.](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

> [!NOTE] 
> La velocità effettiva massima che è possibile ottenere con un gateway ExpressRoute è 10 Gbps tramite una connessione ExpressRoute.The maximum throughput you can achieve with a ExpressRoute gateway is 10 Gbps by using an ExpressRoute connection. La copia di file tra una VM che si trova in una rete virtuale e un sistema locale (come singolo flusso di copia) non raggiunge la totale velocità effettiva dei diversi SKU del gateway. Per sfruttare l'intera larghezza di banda del gateway ExpressRoute, usare più flussi. In alternativa, è necessario copiare file diversi in flussi paralleli di un singolo file.


## <a name="networking-architecture-for-hana-large-instance"></a>Architettura di rete per le istanze Large di HANA
L'architettura di rete per le istanze Large di HANA può essere suddivisa in quattro parti:

- Rete locale e connessione ExpressRoute ad Azure. Questa parte è costituita dal dominio del cliente ed è connessa ad Azure tramite ExpressRoute. Questo circuito Expressroute è completamente pagato da te come cliente. La larghezza di banda deve essere sufficientemente grande da gestire il traffico di rete tra gli asset locali e l'area di Azure a cui ci si connette. Nell'immagine seguente è riportata in basso a destra.
- Servizi di rete di Azure, come illustrato in precedenza, con reti virtuali, che richiedono nuovamente l'aggiunta di gateway ExpressRoute.Azure network services, as previously discussed, with virtual networks, which again need ExpressRoute gateways added. Questa parte è costituita da un'area che richiede una progettazione adatta ai propri requisiti in termini di applicazioni, sicurezza e conformità. L'eventuale uso di istanze Large di HANA è un altro aspetto da tenere in considerazione nella scelta del numero di reti virtuali e di SKU del gateway di Azure. Nell'immagine questa parte è riportata in alto a destra.
- Connettività delle istanze Large di HANA ad Azure tramite la tecnologia ExpressRoute. Questa parte è distribuita e gestita da Microsoft. Il cliente deve solo specificare alcuni intervalli di indirizzi IP e dopo la distribuzione degli asset nelle istanze Large di HANA connettere il circuito ExpressRoute alle reti virtuali. Per altre informazioni, vedere [Infrastruttura e connettività SAP HANA (istanze di grandi dimensioni) in Azure.For more information, see SAP HANA (Large Instances) infrastructure and connectivity on Azure.](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Non sono previsti costi aggiuntivi per l'utente come cliente per la connettività tra l'infrastruttura di rete del data center di Azure e le unità di istanza di grandi dimensioni HANA.
- Rete all'interno del timbro HANA Large Instance, che è per lo più trasparente per voi.

![Rete virtuale connessa a SAP HANA in Azure (istanze Large) e in locale](./media/hana-overview-architecture/image1-architecture.png)

L'uso di istanze Large di HANA non influisce sul requisito della connessione degli asset locali ad Azure tramite ExpressRoute e nemmeno sul requisito di una o più reti virtuali su cui vengono eseguite le VM che ospitano il livello applicazione connesso alle istanze di HANA ospitate nelle unità di istanze Large di HANA. 

Le differenze rispetto alle distribuzioni SAP in Azure sono le seguenti:

- Le unità di istanze Large di HANA del tenant del cliente sono connesse alle reti virtuali tramite un altro circuito ExpressRoute. Per separare le condizioni di caricamento, i circuiti ExpressRoute da locale alla rete virtuale di Azure e i circuiti tra le reti virtuali di Azure e le istanze di grandi dimensioni HANA non condividono gli stessi router.
- Il profilo di carico di lavoro tra il livello applicazione SAP e le istanze Large di HANA è di diversa natura e prevede un'elevata quantità di richieste e burst di piccole dimensioni come i trasferimenti di dati (set di risultati) da SAP HANA al livello applicazione.
- L'architettura delle applicazioni SAP è più sensibile alla latenza di rete rispetto agli scenari tipici con scambio dei dati tra ambiente locale e Azure.
- Il gateway ExpressRoute di Azure ha almeno due connessioni ExpressRoute.The Azure ExpressRoute gateway has at least two ExpressRoute connections. Un circuito connesso da locale e uno connesso da istanze di grandi dimensioni HANA. In questo modo rimane spazio solo per altri due circuiti aggiuntivi da MSEE diversi a cui connettersi in Un gateway ExpressRoute.This leaves only room for another two additional circuits from different MSEEs to connect to on ExpressRoute Gateway. Questa restrizione è indipendente dall'utilizzo di ExpressRoute Fast Path.This restriction is independent of the usage of ExpressRoute Fast Path. Tutti i circuiti connessi condividono la larghezza di banda massima per i dati in ingresso del gateway ExpressRoute.All the connected circuits share the maximum bandwidth for incoming data of the ExpressRoute gateway.

Con la revisione 3 dei timbri di istanza di grandi dimensioni HANA, la latenza di rete sperimentata tra macchine virtuali e unità di istanza HANA di grandi dimensioni può essere superiore a una tipica latenza di andata e ritorno della rete VM-to-VM. A seconda dell'area di Azure, i valori misurati possono superare la latenza di andata e ritorno di 0,7 millisecondi classificata come inferiore alla media in [SAP Note #1100926 - FAQ: Network performance](https://launchpad.support.sap.com/#/notes/1100926/E) (Nota SAP 1100926 - Domande frequenti sulle prestazioni di rete). A seconda dell'area di Azure e dello strumento usato per misurare la latenza di andata e ritorno di rete tra una macchina virtuale di Azure e l'unità di istanze Large di HANA, la latenza misurata può arrivare fino a 2 millisecondi. Nonostante ciò, i clienti distribuiscono applicazioni SAP di produzione basate su SAP HANA in istanze Large di SAP HANA con ottimi risultati. È importante testare accuratamente i processi aziendali nelle istanze Large di HANA in Azure. Una nuova funzionalità, denominata percorso rapido ExpressRoute, è in grado di ridurre sostanzialmente la latenza di rete tra le istanze di grandi dimensioni HANA e le macchine virtuali a livello di applicazione in Azure (vedere di seguito). 

Con la revisione 4 degli indicatori di istanza di grandi dimensioni HANA, la latenza di rete tra macchine virtuali di Azure distribuite in prossimità del timbro Istanza di grandi dimensioni HANA viene considerata in grado di soddisfare la classificazione media o migliore della media, come documentato in [SAP Note #1100926 - Domande frequenti: Prestazioni](https://launchpad.support.sap.com/#/notes/1100926/E) di rete se è configurato Il percorso rapido di Azure ExpressRoute (vedere di seguito). Per distribuire macchine virtuali di Azure in prossimità delle unità di istanza DI grandi dimensioni HANA della revisione 4, è necessario sfruttare i gruppi di posizionamento di prossimità di Azure.In order to deploy Azure VMs in close proximity to HANA Large Instance units of Revision 4, you need to leverage [Azure Proximity Placement Groups](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Il modo in cui è possibile usare i gruppi di posizionamento di prossimità per individuare il livello dell'applicazione SAP nello stesso data center di Azure come unità di istanza di grandi dimensioni HANA ospitate dalla revisione 4 è descritto in Gruppi di posizionamento di prossimità di Azure per una latenza di [rete ottimale con le applicazioni SAP.](sap-proximity-placement-scenarios.md)

Per fornire latenza di rete deterministica tra macchine virtuali e istanza di grandi dimensioni HANA, la scelta dello SKU del gateway ExpressRoute è essenziale. A differenza dei modelli di traffico tra l'ambiente locale e le VM, il modello di traffico tra le VM e le istanze Large di HANA può sviluppare burst di piccole dimensioni ma frequenti di richieste e volumi di dati da trasmettere. Per gestire in modo efficiente tali burst, è consigliabile usare lo SKU del gateway UltraPerformance. Per la classe di tipo II degli SKU di istanza di grandi dimensioni HANA, l'uso dello SKU del gateway UltraPerformance come gateway ExpressRoute è obbligatorio.

> [!IMPORTANT] 
> Considerato il traffico di rete generale tra il livello database e il livello applicazione SAP, per la connessione a SAP HANA in Azure (istanze Large) sono supportati solo gli SKU del gateway HighPerformance o UltraPerformance per le reti virtuali. Per gli SKU HANA Large Instance Type II, solo lo SKU del gateway UltraPerformance è supportato come gateway ExpressRoute.For HANA Large Instance Type II SKUs, only the UltraPerformance gateway SKU is supported as a ExpressRoute gateway. Le eccezioni si applicano quando si usa ExpressRoute Fast Path (vedere di seguito)

### <a name="expressroute-fast-path"></a>Percorso rapido ExpressRoute
Per ridurre la latenza, ExpressRoute Fast Path è stato introdotto e rilasciato a maggio 2019 per la connettività specifica delle istanze di grandi dimensioni HANA alle reti virtuali di Azure che ospitano le macchine virtuali dell'applicazione SAP. La differenza principale rispetto alla soluzione implementata finora è che i flussi di dati tra macchine virtuali e istanze di grandi dimensioni HANA non vengono più instradati attraverso il gateway ExpressRoute.The major difference to the solution rolled out to far out to far, is, is, that the data flows between VMs and HANA Large Instances is nomore tind through the ExpressRoute gateway at. Le macchine virtuali assegnate nelle subnet della rete virtuale di Azure comunicano direttamente con il router perimetrale aziendale dedicato. 

> [!IMPORTANT] 
> La funzionalità Percorso rapido ExpressRoute richiede che le subnet che eseguono le macchine virtuali dell'applicazione SAP si trovino nella stessa rete virtuale di Azure connessa alle istanze di grandi dimensioni HANA. Le macchine virtuali situate nelle reti virtuali di Azure sottoposte a peering con la rete virtuale di Azure connesse direttamente alle unità di istanza di grandi dimensioni HANA non traggono vantaggio da ExpressRoute Fast Path. Di conseguenza, le progettazioni tipiche di reti virtuali hub e spoke, in cui i circuiti ExpressRoute si connettono a una rete virtuale hub e le reti virtuali contenenti il livello applicazione SAP (spokes) vengono sottoposte a peered, l'ottimizzazione da ExpressRoute Fast Il percorso non funzionerà. In aggiunta, ExpressRoute Fast Path non supporta oggi le regole di routing definite dall'utente. Per altre informazioni, vedere [Gateway di rete virtuale ExpressRoute e FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Per ulteriori informazioni su come configurare ExpressRoute Fast Path, leggere il documento [Connect a virtual network to HANA large instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> È necessario che un gateway UltraPerformance ExpressRoute funzioni con ExpressRoute


## <a name="single-sap-system"></a>Sistema SAP singolo

L'infrastruttura locale illustrata in precedenza è connessa ad Azure tramite ExpressRoute. Il circuito ExpressRoute si connette a un router Microsoft Enterprise Edge (MSEE). Per altre informazioni, vedere [Panoramica tecnica di ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Dopo aver stabilito la route, si connette alla backbone di Azure.After the route is established, it connects into the Azure backbone.

> [!NOTE] 
> Per eseguire panorami applicativi SAP in Azure, connettersi al router Enterprise Edge più vicino all'area di Azure nel panorama applicativo SAP. I timbri HANA Large Instance sono connessi tramite dispositivi perimetrali aziendali dedicati per ridurre al minimo la latenza di rete tra macchine virtuali in Azure IaaS e nei timbri di istanza di grandi dimensioni HANA.

Il gateway ExpressRoute per le macchine virtuali che ospitano istanze dell'applicazione SAP è connesso a un circuito ExpressRoute che si connette a un'ambiente locale. La stessa rete virtuale è connessa a un router Enterprise Edge per la connessione a moduli per istanze Large. Usando ExpressRoute Fast Path, il flusso di dati dalle istanze di grandi dimensioni HANA alle macchine virtuali del livello applicazione SAP non viene più instradato attraverso il gateway ExpressRoute e con ciò riduce la latenza di andata e ritorno della rete.

Questo sistema è un esempio semplice di un sistema SAP singolo. Il livello applicazione SAP è ospitato in Azure. Il database SAP HANA viene eseguito su SAP HANA in Azure (istanze Large). Il presupposto è che la larghezza di banda del gateway ExpressRoute di velocità effettiva di 2 Gbps o 10 Gbps non rappresenti un collo di bottiglia.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Sistemi SAP multipli o sistemi SAP di grandi dimensioni

Se vengono distribuiti più sistemi SAP o sistemi SAP di grandi dimensioni per connettersi a SAP HANA in Azure (istanze di grandi dimensioni), la velocità effettiva del gateway ExpressRoute potrebbe diventare un collo di bottiglia. In alternativa, si desidera isolare i sistemi di produzione e non di produzione in reti virtuali di Azure diverse. In tal caso, dividere i livelli applicazione in più reti virtuali. È anche possibile creare una rete virtuale speciale che si connette alle istanze Large di HANA in casi come questi:

- Esecuzione di backup direttamente da istanze Large di HANA a una VM in Azure che ospita le condivisioni NFS.
- Copia di backup di grandi dimensioni o di altri file dalle unità di istanze di grandi dimensioni HANA su spazio del disco gestito in Azure.

Usare una rete virtuale separata per ospitare macchine virtuali che gestiscono l'archiviazione per il trasferimento di massa di dati tra istanze di grandi dimensioni HANA e Azure.Use a separate virtual network to host VMs that manage storage for mass transfer of data between HANA Large Instances and Azure. Questa disposizione consente di evitare gli effetti del trasferimento di file o dati di grandi dimensioni da un'istanza di grandi dimensioni HANA ad Azure nel gateway ExpressRoute che serve le macchine virtuali che eseguono il livello applicazione SAP. 

Per un'architettura di rete più scalabile:

- Utilizzare più reti virtuali per un livello applicazione SAP singolo e di dimensioni maggiori.
- Distribuire una rete virtuale separata per ogni sistema SAP distribuito anziché combinare questi sistemi SAP in subnet separate nella stessa rete virtuale.

  Un'architettura di rete più scalabile per SAP HANA in Azure (istanze di grandi dimensioni):

![Distribuire il livello applicazione SAP su più reti virtuali](./media/hana-overview-architecture/image4-networking-architecture.png)

A seconda delle regole e delle restrizioni, si desidera applicare tra le diverse reti virtuali che ospitano macchine virtuali di sistemi SAP diversi, è consigliabile eseguire il peering di tali reti virtuali. Per altre informazioni sul peering delle reti virtuali, vedere [Peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Routing in Azure

Per impostazione predefinita, tre considerazioni sul routing di rete sono importanti per SAP HANA in Azure (istanze di grandi dimensioni):By default deployment, three network routing considerations are important for SAP HANA on Azure (Large Instances):

* È possibile accedere a SAP HANA in Azure (istanze di grandi dimensioni) solo tramite macchine virtuali di Azure e la connessione ExpressRoute dedicata, non direttamente dall'ambiente locale. L'accesso diretto dall'ambiente locale alle unità di istanze Large di HANA, come distribuite da Microsoft, non è consentito nell'immediato. Le restrizioni relative al routing temporaneo sono dovute all'attuale architettura di rete di Azure usata per le istanze Large di SAP HANA. Alcuni client di amministrazione e le applicazioni che richiedono l'accesso diretto, ad esempio SAP Solution Manager in esecuzione in locale, non possono connettersi al database SAP HANA. Per le eccezioni, controllare la sezione 'Routing diretto verso istanze di grandi dimensioni HANA'.

* Se si dispone di unità di istanza di grandi dimensioni HANA distribuite in due diverse aree di Azure per il ripristino di emergenza, le stesse restrizioni di routing temporanee applicate in passato. In altre parole, gli indirizzi IP di un'unità di istanza HANA Large In un'area (ad esempio, US West) non sono stati instradati a un'unità di istanza HANA Large Instance distribuita in un'altra area (ad esempio, Stati Uniti orientali). Questa restrizione era indipendente dall'uso del peering della rete di Azure tra aree o dalla connessione incrociata dei circuiti ExpressRoute che collegano unità di istanza di grandi dimensioni HANA alle reti virtuali. Per una rappresentazione grafica, vedere la figura nella sezione "Usare le unità di istanze Large di HANA in più aree". Questa restrizione, fornita con l'architettura distribuita, ha proibito l'uso immediato della replica di sistema HANA come funzionalità di ripristino di emergenza. Per le modifiche recenti, cercare la sezione "Usare unità di istanza di grandi dimensioni HANA in più aree". 

* Alle unità SAP HANA in Azure (istanze di grandi dimensioni) è assegnato un indirizzo IP dall'intervallo di indirizzi del pool IP del server inviato quando si richiede la distribuzione dell'istanza di grandi dimensioni HANA. Per altre informazioni, vedere [Infrastruttura e connettività SAP HANA (istanze di grandi dimensioni) in Azure.For more information, see SAP HANA (Large Instances) infrastructure and connectivity on Azure.](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) Questo indirizzo IP è accessibile tramite le sottoscrizioni e il circuito di Azure che connettono le reti virtuali di Azure alle istanze di grandi dimensioni HANA. L'indirizzo IP assegnato al di fuori dell'intervallo di indirizzi del pool IP del server viene assegnato direttamente all'unità hardware. *Non* viene più assegnato tramite NAT, come avveniva nelle prime distribuzioni di questa soluzione. 

### <a name="direct-routing-to-hana-large-instances"></a>Routing diretto a istanze di grandi dimensioni HANADirect Routing to HANA Large Instances

Per impostazione predefinita, il routing transitivo non funziona nei seguenti scenari:

* Tra unità di istanza di grandi dimensioni HANA e una distribuzione locale.

* Tra il routing delle istanze HANA di grandi dimensioni distribuito in due aree diverse.

Esistono tre modi per abilitare il routing transitivo in tali scenari:There are three ways to enable transitive routing in those scenarios:

- Un proxy inverso per instradare i dati in modo bidirezionale. Ad esempio, F5 BIG-IP, NGINX con Gestione traffico distribuito nella rete virtuale di Azure che si connette a istanze di grandi dimensioni HANA e in locale come soluzione di routing di firewall/traffico virtuale.
- [Regole di IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) in una VM Linux per abilitare il routing tra le posizioni locali e le unità di istanze Large di HANA o fra tali unità in aree diverse. La macchina virtuale che esegue IPTables deve essere distribuita nella rete virtuale di Azure che si connette a istanze di grandi dimensioni HANA e in locale. La macchina virtuale deve essere dimensionata di conseguenza, pertanto la velocità effettiva di rete della macchina virtuale è sufficiente per il traffico di rete previsto. Per informazioni dettagliate sulla larghezza di banda della rete VM, vedere l'articolo Dimensioni delle macchine virtuali Linux in Azure.For details on VM network bandwidth, check the article [Sizes of Linux virtual machines in Azure.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Firewall](https://azure.microsoft.com/services/azure-firewall/) di Azure sarebbe un'altra soluzione per consentire il traffico diretto tra unità di istanza di grandi dimensioni e locali. 

Tutto il traffico di queste soluzioni verrebbe instradato attraverso una rete virtuale di Azure e, di conseguenza, il traffico potrebbe essere ulteriormente limitato dalle appliance soft usate o dai gruppi di sicurezza di rete di Azure, in modo che determinati indirizzi IP o indirizzi IP locale potrebbe essere bloccato o consentito esplicitamente l'accesso a istanze di grandi dimensioni HANA. 

> [!NOTE]  
> Tenere presente che Microsoft non fornisce l'implementazione e il supporto per soluzioni personalizzate che riguardano IPTables o appliance di rete di terze parti. Il supporto deve essere reso disponibile dal fornitore del componente usato o dall'integratore. 

#### <a name="express-route-global-reach"></a>Copertura globale Express Route
Microsoft ha introdotto una nuova funzionalità denominata [Copertura globale ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). La copertura globale può essere utilizzata per le istanze di grandi dimensioni HANA in due scenari:Global Reach can be used for HANA Large Instances in two scenarios:

- Abilitare l'accesso diretto da unità di istanza di grandi dimensioni HANA distribuite in aree diverse
- Abilitare la comunicazione diretta tra le unità di istanza di grandi dimensioni HANA distribuite in aree diverseEnable direct communication between your HANA Large Instance units deployed in different regions


##### <a name="direct-access-from-on-premises"></a>Accesso diretto da locale
Nelle aree di Azure in cui viene offerta la copertura globale, è possibile richiedere l'abilitazione della funzionalità di copertura globale per il circuito ExpressRoute che connette la rete locale alla rete virtuale di Azure che si connette anche alle unità di istanza di grandi dimensioni HANA. Esistono alcune implicazioni sui costi per il lato locale del circuito ExpressRoute.There are some cost implications for the on-premises side of your ExpressRoute circuit. Per i prezzi, controlla i prezzi per [Global Reach Add-On](https://azure.microsoft.com/pricing/details/expressroute/). Non sono previsti costi aggiuntivi correlati al circuito che connette le unità di istanza HANA Large Instance ad Azure.There are no additional costs for you related to the circuit that connects the HANA Large Instance unit(s) to Azure. 

> [!IMPORTANT]  
> In caso di utilizzo di Global Reach per abilitare l'accesso diretto tra le unità HANA Large Instance e gli asset locali, i dati di rete e il flusso di controllo non vengono **instradati attraverso le reti virtuali**di Azure, ma direttamente tra i router di Scambio aziendali Microsoft. Di conseguenza, qualsiasi regola del gruppo di sicurezza di rete o ASG o qualsiasi tipo di firewall, nVA o proxy distribuito in una rete virtuale di Azure non viene toccato. **Se si usa la copertura globale ExpressRoute per consentire l'accesso diretto da locale ad HANA le restrizioni delle unità di istanza di grandi dimensioni e le autorizzazioni per accedere alle unità di istanza di grandi dimensioni HANA devono essere definite nei firewall sul lato locale** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Connessione di istanze HANA di grandi dimensioni in aree di Azure diverseConnecting HANA Large Instances in different Azure regions
Allo stesso modo, poiché ExpressRoute Global Reach può essere usato per la connessione locale a unità di istanza di grandi dimensioni HANA, può essere usata per connettere due tenant HANA Large Instance distribuiti in due aree diverse. L'isolamento è il percorso expressRoute che i tenant HANA Large Instance usano per connettersi ad Azure in entrambe le aree. Non sono previsti costi aggiuntivi per la connessione di due tenant HANA Large Instance distribuiti in due aree diverse. 

> [!IMPORTANT]  
> Il flusso di dati e il flusso di controllo del traffico di rete tra i diversi tenant di istanza di grandi dimensioni non verranno instradati attraverso reti di azure. Di conseguenza, non è possibile usare la funzionalità di Azure o le nVA per applicare restrizioni di comunicazione tra i due tenant DI istanze di grandi dimensioni HANA. 

Per ulteriori informazioni su come ottenere ExpressRoute Global Reach abilitato, leggere il documento [Connect a virtual network to HANA large instances](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Connettività Internet delle istanze Large di HANA
Le istanze Large di HANA *non* hanno una connettività Internet diretta. Questo limita ad esempio la possibilità di registrare l'immagine del sistema operativo direttamente con il relativo fornitore. Può quindi essere necessario usare il server SUSE Linux Enterprise Server Subscription Management Tool locale o Red Hat Enterprise Linux Subscription Manager.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Crittografia dei dati tra VM e istanze Large di HANA
I dati trasferiti tra le istanze Large di HANA e le VM non vengono crittografati. Tuttavia, esclusivamente per lo scambio tra il lato HANA DBMS e le applicazioni basate su JDBC/ODBC, è possibile abilitare la crittografia del traffico. Per altre informazioni, vedere [questa documentazione di SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Usare le unità di istanze Large di HANA in più aree

Per eseguire la realizzazione dei set di ripristino di emergenza, è necessario disporre di unità di istanza di grandi dimensioni SHANA in più aree di Azure.To realize disaster recovery set ups, you need to have SHANA Large Instance units in multiple Azure regions. Anche con l'uso di Azure [Global Vnet Peering], il routing transitivo per impostazione predefinita non funziona tra tenant HANA Large Instance in due aree diverse. Tuttavia, Copertura globale apre il percorso di comunicazione tra le unità di istanza di grandi dimensioni HANA di cui è stato eseguito il provisioning in due aree diverse. Questo scenario di utilizzo di Reach globale ExpressRoute consente:This usage scenario of ExpressRoute Global Reach enables:

 - Replica del sistema HANA senza proxy o firewall aggiuntivi
 - Copia di backup tra unità di istanza di grandi dimensioni HANA in due aree diverse per eseguire copie di sistema o aggiornamenti di sistema


![Rete virtuale connessa ai moduli per istanze Large di Azure in diverse aree di Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

La figura mostra come le diverse reti virtuali in entrambe le aree sono connesse a due circuiti ExpressRoute diversi usati per connettersi a SAP HANA in Azure (istanze di grandi dimensioni) in entrambe le aree di Azure (linee grigie). La ragione di queste due connessioni incrociate è proteggere da un'interruzione degli MSE su entrambi i lati. Il flusso di comunicazione tra le due reti virtuali nelle due aree di Azure dovrebbe essere gestito tramite il [peering globale](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) delle due reti virtuali nelle due aree diverse (linea tratteggiata blu). La linea rossa spessa descrive la connessione ExpressRoute Global Reach, che consente alle unità di istanza di grandi dimensioni HANA dei tenant in due aree diverse di comunicare tra loro. 

> [!IMPORTANT] 
> Se vengono usati più circuiti ExpressRoute, è necessario definire le impostazioni AS Path Prepending (Percorso AS anteposto) e Local Preference BGP (BGP preferenza locale) per consentire il routing appropriato del traffico.

**Passaggi successivi**
- Fare riferimento all'articolo [Architettura di archiviazione di SAP HANA (istanze Large)](hana-storage-architecture.md)
