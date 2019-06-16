---
title: Architettura di rete di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Architettura di rete della distribuzione di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8794a93cecb50774f30746c22931db31a9fa9194
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "66239612"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Architettura di rete di SAP HANA (istanze Large)

L'architettura dei servizi di rete di Azure è un componente fondamentale per la corretta distribuzione di applicazioni SAP in istanze Large di HANA. In genere, le distribuzioni di SAP HANA in Azure (istanze di grandi dimensioni) dispongono di un panorama applicativo di SAP più ampio con varie soluzioni SAP e varie dimensioni di database, utilizzo delle risorse della CPU e utilizzo della memoria. È probabile che non tutti i sistemi IT si trovano già in Azure. Il panorama applicativo SAP è spesso ibrido anche da un punto di DBMS e SAP application punto di vista usando una combinazione di NetWeaver e S/4 Hana e SAP HANA e altri DBMS. Azure offre diversi servizi che consentono di eseguire diversi sistemi DBMS, NetWeaver e S/4 Hana in Azure. Azure inoltre offre che la tecnologia per migliorare Azure aspetto la rete, ad esempio un data center virtuale per le distribuzioni di software locale

A meno che non sono ospitati i sistemi IT completati in Azure. Funzionalità di rete di Azure viene usata per connettersi al mondo locale con le risorse di Azure per rendere Azure simile a un Data Center virtuale di quelle in uso. La funzionalità di rete di Azure usata è: 

- Reti virtuali di Azure sono connessi per il [ExpressRoute](https://azure.microsoft.com/services/expressroute/) circuito che si connette a risorse di rete locale.
- Un circuito ExpressRoute che connette in locale ad Azure deve avere una larghezza di banda minima [1 Gbps o versione successiva](https://azure.microsoft.com/pricing/details/expressroute/). Questo minimo garantisce una larghezza di banda sufficiente per il trasferimento dei dati tra i sistemi locali e i sistemi in esecuzione nelle VM e per la connessione degli utenti locali ai sistemi di Azure.
- Tutti i sistemi SAP in Azure vengono configurati in reti virtuali di comunicare tra loro.
- Active Directory e DNS ospitati in locale sono estesi in Azure tramite ExpressRoute da locale o in esecuzione completa in Azure.

Nel caso specifico dell'integrazione di istanze Large di HANA nell'infrastruttura di rete di Azure data center, la tecnologia Azure ExpressRoute viene usata anche


> [!NOTE] 
> Una sola sottoscrizione di Azure può essere collegata a un solo tenant in un modulo per istanze Large di HANA in un'area di Azure specifica. Viceversa, un singolo tenant del modulo per istanze Large di HANA può essere collegato a una sola sottoscrizione di Azure. Questo requisito è coerente con quello di altri oggetti fatturabili in Azure.

Se SAP HANA in Azure (istanze Large) viene distribuito in diverse aree di Azure, viene distribuito un tenant separato nel modulo per istanze Large di HANA. È possibile eseguire entrambi nella stessa sottoscrizione di Azure purché queste istanze facciano parte dello stesso panorama applicativo SAP. 

> [!IMPORTANT] 
> Con SAP HANA in Azure (istanze Large) è supportato solo il metodo di distribuzione Azure Resource Manager.

 

## <a name="additional-virtual-network-information"></a>Informazioni aggiuntive sulle reti virtuali

Per connettere una rete virtuale al circuito ExpressRoute, è necessario creare un gateway ExpressRoute di Azure. Per altre informazioni, vedere [Expressroute sui gateway per ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Un gateway ExpressRoute di Azure viene usato con ExpressRoute a un'infrastruttura all'esterno di Azure o a un modulo per istanze Large di Azure. È possibile connettere il gateway ExpressRoute di Azure a un massimo di quattro circuiti ExpressRoute diversi, purché tali connessioni provengano da router di Microsoft enterprise edge diversi. Per altre informazioni, vedere [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> La massima velocità effettiva che è possibile ottenere con un gateway ExpressRoute è 10 Gbps con una connessione ExpressRoute. La copia di file tra una VM che si trova in una rete virtuale e un sistema locale (come singolo flusso di copia) non raggiunge la totale velocità effettiva dei diversi SKU del gateway. Per sfruttare l'intera larghezza di banda del gateway ExpressRoute, usare più flussi. In alternativa, è necessario copiare file diversi in flussi paralleli di un singolo file.


## <a name="networking-architecture-for-hana-large-instance"></a>Architettura di rete per le istanze Large di HANA
L'architettura di rete per le istanze Large di HANA può essere suddivisa in quattro parti:

- Rete locale e connessione ExpressRoute ad Azure. Questa parte è costituita dal dominio del cliente ed è connessa ad Azure tramite ExpressRoute. Il circuito Expressroute viene lasciato completamente dall'utente come un cliente. La larghezza di banda deve essere sufficientemente grande per gestire il traffico di rete tra le risorse a livello locale e l'area di Azure che in cui ci si connette. Nell'immagine seguente è riportata in basso a destra.
- Servizi di rete di Azure, come illustrati in precedenza, le reti virtuali che devono anche in questo caso i gateway ExpressRoute aggiunti. Questa parte è costituita da un'area che richiede una progettazione adatta ai propri requisiti in termini di applicazioni, sicurezza e conformità. L'eventuale uso di istanze Large di HANA è un altro aspetto da tenere in considerazione nella scelta del numero di reti virtuali e di SKU del gateway di Azure. Nell'immagine questa parte è riportata in alto a destra.
- Connettività delle istanze Large di HANA ad Azure tramite la tecnologia ExpressRoute. Questa parte è distribuita e gestita da Microsoft. Il cliente deve solo specificare alcuni intervalli di indirizzi IP e dopo la distribuzione degli asset nelle istanze Large di HANA connettere il circuito ExpressRoute alle reti virtuali. Per altre informazioni, vedere [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Non è senza alcun costo aggiuntivo per l'utente come un cliente per la connettività tra l'infrastruttura di rete di Azure data center e le unità di istanze Large di HANA.
- Funzionalità di rete all'interno di modulo per istanze Large di HANA, che è quasi completamente trasparente per l'utente.

![Rete virtuale connessa a SAP HANA in Azure (istanze Large) e in locale](./media/hana-overview-architecture/image1-architecture.png)

L'uso di istanze Large di HANA non influisce sul requisito della connessione degli asset locali ad Azure tramite ExpressRoute e nemmeno sul requisito di una o più reti virtuali su cui vengono eseguite le VM che ospitano il livello applicazione connesso alle istanze di HANA ospitate nelle unità di istanze Large di HANA. 

Le differenze rispetto alle distribuzioni SAP in Azure sono le seguenti:

- Le unità di istanze Large di HANA del tenant del cliente sono connesse alle reti virtuali tramite un altro circuito ExpressRoute. Per separare le condizioni di carico, on-premise a circuiti ExpressRoute di rete virtuale di Azure e i circuiti tra reti virtuali di Azure e le istanze Large di HANA non condividono gli stessi router.
- Il profilo di carico di lavoro tra il livello applicazione SAP e le istanze Large di HANA è di diversa natura e prevede un'elevata quantità di richieste e burst di piccole dimensioni come i trasferimenti di dati (set di risultati) da SAP HANA al livello applicazione.
- L'architettura delle applicazioni SAP è più sensibile alla latenza di rete rispetto agli scenari tipici con scambio dei dati tra ambiente locale e Azure.
- Il gateway ExpressRoute di Azure ha almeno due connessioni ExpressRoute. Un circuito che è connesso da on-premise e uno che è connesso da istanze Large di HANA. In tal modo spazio solo a un altro due circuiti aggiuntivi dal msee diversi a cui connettersi nel Gateway di ExpressRoute. Questa restrizione è indipendente dell'utilizzo del percorso veloce ExpressRoute. Tutti i circuiti connessi condividono la larghezza di banda massima per i dati in ingresso del gateway ExpressRoute.

La latenza di rete rilevata tra le VM e le unità di istanze Large di HANA può essere superiore alla latenza di andata e ritorno tipica di una rete da VM a VM. A seconda dell'area di Azure, i valori misurati possono superare la latenza di andata e ritorno di 0,7-ms classificata come inferiore alla media in [SAP Note #1100926 - domande frequenti: Prestazioni di rete](https://launchpad.support.sap.com/#/notes/1100926/E). A seconda dell'area di Azure e dello strumento usato per misurare la latenza di andata e ritorno di rete tra una macchina virtuale di Azure e l'unità di istanze Large di HANA, la latenza misurata può arrivare fino a 2 millisecondi. Nonostante ciò, i clienti distribuiscono applicazioni SAP di produzione basate su SAP HANA in istanze Large di SAP HANA con ottimi risultati. È importante testare accuratamente i processi aziendali nelle istanze Large di HANA in Azure. Una nuova funzionalità, denominata Path Fast ExpressRoute, è in grado di ridurre sostanzialmente la latenza di rete tra istanze Large di HANA e del livello di applicazione macchine virtuali in Azure (vedere sotto). 

Per garantire una latenza di rete deterministica tra le macchine virtuali e istanze Large di HANA, la scelta del gateway ExpressRoute è essenziale dello SKU. A differenza dei modelli di traffico tra l'ambiente locale e le VM, il modello di traffico tra le VM e le istanze Large di HANA può sviluppare burst di piccole dimensioni ma frequenti di richieste e volumi di dati da trasmettere. Per gestire in modo efficiente tali burst, è consigliabile usare lo SKU del gateway UltraPerformance. Per la classe di tipo II di SKU delle istanze Large HANA, l'uso dello SKU del gateway UltraPerformance come gateway Expressroute è obbligatorio.

> [!IMPORTANT] 
> Considerato il traffico di rete generale tra il livello database e il livello applicazione SAP, per la connessione a SAP HANA in Azure (istanze Large) sono supportati solo gli SKU del gateway HighPerformance o UltraPerformance per le reti virtuali. Per HANA tipo II SKU delle istanze Large, è supportato solo lo SKU ultraperformance come gateway ExpressRoute. Le eccezioni vengono applicate quando si usa ExpressRoute Fast percorso (vedere sotto)

### <a name="expressroute-fast-path"></a>Percorso di ExpressRoute Fast
Per ridurre la latenza, velocità percorso di ExpressRoute è stata introdotta e rilasciato in maggio 2019 per la connettività specifica delle istanze Large di HANA alle reti virtuali di Azure che ospitano macchine virtuali dell'applicazione SAP. La differenza principale alla soluzione il roll-out fino ad ora, è che i flussi di dati tra le macchine virtuali e istanze Large di HANA non vengono indirizzati attraverso il gateway ExpressRoute più. Macchine virtuali assegnate nella subnet della rete virtuale di Azure sono invece comunica direttamente con il router perimetrale aziendale dedicato. 

> [!IMPORTANT] 
> La funzionalità ExpressRoute Fast percorso richiede che la subnet che eseguono le macchine virtuali dell'applicazione SAP siano nella stessa rete virtuale di Azure che è stato connesso alle istanze Large di HANA. Macchine virtuali situate in reti virtuali di Azure con peering con la rete virtuale di Azure connessa direttamente alle unità di istanze Large di HANA non vengono beneficiano di Fast percorso di ExpressRoute. Di conseguenza tipico hub e spoke progettazioni di rete virtuale, in cui i circuiti ExpressRoute si connettono da una rete virtuale hub e recupero di peering delle reti virtuali che contiene il livello applicazione SAP (spoke), l'ottimizzazione da ExpressRoute Fast Percorso non funzionerà. Tenere presente inoltre percorso veloce ExpressRoute non supporta regole di routing definito dall'utente (UDR) oggi stesso. Per altre informazioni, vedere [ExpressRoute virtual network gateway e FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Per altre informazioni dettagliate su come configurare il percorso rapido ExpressRoute, vedere il documento [connettere una rete virtuale alle istanze large di HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> Un gateway UltraPerformance ExpressRoute è deve disporre funzionante Fast percorso di ExpressRoute


## <a name="single-sap-system"></a>Sistema SAP singolo

L'infrastruttura locale illustrata in precedenza è connessa ad Azure tramite ExpressRoute. Il circuito ExpressRoute si connette a un router Microsoft enterprise edge (MSEE). Per altre informazioni, vedere [Panoramica tecnica di ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Dopo aver stabilita la route, si connette al backbone di Azure.

> [!NOTE] 
> Per eseguire panorami applicativi SAP in Azure, connettersi al router Enterprise Edge più vicino all'area di Azure nel panorama applicativo SAP. Moduli per istanze Large di HANA sono connessi tramite dispositivi di router perimetrali enterprise dedicati per ridurre al minimo la latenza di rete tra VM in IaaS di Azure e moduli per istanze Large di HANA.

Il gateway di ExpressRoute per le macchine virtuali che ospitano le istanze dell'applicazione SAP è connesso a un circuito ExpressRoute che si connette a una locale. La stessa rete virtuale è connessa a un router Enterprise Edge per la connessione a moduli per istanze Large. Le macchine virtuali tramite ExpressRoute Fast percorso, il flusso di dati da istanze Large di HANA per il livello applicazione SAP non vengono indirizzate attraverso il gateway ExpressRoute con più e con cui ridurre la latenza media round trip della rete.

Questo sistema è un esempio semplice di un sistema SAP singolo. Il livello applicazione SAP è ospitato in Azure. Il database SAP HANA viene eseguito su SAP HANA in Azure (istanze Large). Il presupposto è che la larghezza di banda gateway ExpressRoute della velocità effettiva di 2 Gbps o 10 Gbps non rappresenti un collo di bottiglia.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Sistemi SAP multipli o sistemi SAP di grandi dimensioni

Se per la connessione a SAP HANA in Azure (istanze Large) vengono distribuiti i sistemi SAP multipli o sistemi SAP di grandi dimensioni, la velocità effettiva del gateway ExpressRoute potrebbe diventare un collo di bottiglia. O si vuole isolare l'ambiente di produzione e i sistemi non di produzione in diverse reti virtuali di Azure. In tal caso, dividere i livelli applicazione in più reti virtuali. È anche possibile creare una rete virtuale speciale che si connette alle istanze Large di HANA in casi come questi:

- Esecuzione di backup direttamente da istanze Large di HANA a una VM in Azure che ospita le condivisioni NFS.
- Copia di backup di grandi dimensioni o di altri file dalle unità di istanze di grandi dimensioni HANA su spazio del disco gestito in Azure.

Usare una rete virtuale distinta per ospitare le VM che gestiscono l'archiviazione di massa trasferimento dei dati tra istanze Large di HANA e Azure. In questo modo è possibile evitare gli effetti di trasferimento dei dati da istanze Large di HANA in Azure nel gateway ExpressRoute usato dalle VM che eseguono il livello applicazione SAP o file di grandi dimensioni. 

Per un'architettura di rete più scalabile:

- Utilizzare più reti virtuali per un livello applicazione SAP singolo e di dimensioni maggiori.
- Distribuire una rete virtuale separata per ogni sistema SAP distribuito anziché combinare questi sistemi SAP in subnet separate nella stessa rete virtuale.

  Un'architettura di rete più scalabile per SAP HANA in Azure (istanze di grandi dimensioni):

![Distribuire il livello applicazione SAP su più reti virtuali](./media/hana-overview-architecture/image4-networking-architecture.png)

A seconda le regole e restrizioni, che si desidera applicare tra le diverse reti virtuali che ospita le macchine virtuali di diversi sistemi SAP, si devono eseguire il peering di queste reti virtuali. Per altre informazioni sul peering delle reti virtuali, vedere [Peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Routing in Azure

Con la distribuzione predefinita, sono importanti per SAP HANA in Azure (istanze Large) tre considerazioni sul routing di rete:

* SAP HANA in Azure (istanze Large) è accessibile solo tramite macchine virtuali di Azure e la connessione ExpressRoute dedicata, non direttamente dall'istanza locale. L'accesso diretto dall'ambiente locale alle unità di istanze Large di HANA, come distribuite da Microsoft, non è consentito nell'immediato. Le restrizioni relative al routing temporaneo sono dovute all'attuale architettura di rete di Azure usata per le istanze Large di SAP HANA. Alcuni client di amministrazione e le applicazioni che richiedono l'accesso diretto, ad esempio SAP Solution Manager in esecuzione in locale, non possono connettersi al database SAP HANA. Per le eccezioni di controllare la sezione 'Routing diretto a istanze Large di HANA'.

* Se si dispone di unità di istanze Large di HANA distribuite nelle due diverse aree di Azure per il ripristino di emergenza, le stesse restrizioni di routine temporanee applicate in precedenza. In altre parole, gli indirizzi IP di un'unità di istanze Large di HANA in un'unica area (ad esempio, Stati Uniti occidentali) non sono stati indirizzati a un'unità di istanze Large di HANA distribuita in un'altra area (ad esempio, Stati Uniti orientali). Questa restrizione è indipendente dall'uso della rete di Azure, peering tra aree o del collegamento incrociato i circuiti ExpressRoute che connettono le unità di istanze Large di HANA alle reti virtuali. Per una rappresentazione grafica, vedere la figura nella sezione "Usare le unità di istanze Large di HANA in più aree". Questa restrizione, fornita con l'architettura distribuita, non consentito l'uso immediato della replica di sistema HANA come funzionalità di ripristino di emergenza. Per le modifiche recenti, cercare la sezione 'Unità di istanze Large di HANA di utilizzo in più aree'. 

* SAP HANA nelle unità Azure (istanze Large) hanno un indirizzo IP assegnato dal server del pool intervallo di indirizzi IP che è stato inviato quando si richiede la distribuzione di istanze Large di HANA. Per altre informazioni, vedere [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Questo indirizzo IP è accessibile tramite le sottoscrizioni di Azure e il circuito che si connette a reti virtuali di Azure per istanze Large di HANA. L'indirizzo IP assegnato al di fuori dell'intervallo di indirizzi del pool IP del server viene assegnato direttamente all'unità hardware. *Non* viene più assegnato tramite NAT, come avveniva nelle prime distribuzioni di questa soluzione. 

### <a name="direct-routing-to-hana-large-instances"></a>Indirizzare il Routing a istanze Large di HANA
Per impostazione predefinita il routing transitivo tra unità di istanze Large di HANA e in locale o di routing istanze Large di HANA distribuite in due aree diverse non funziona. Esistono diverse possibilità per abilitare tali un routing transitivo.

- Un proxy inverso per instradare i dati in modo bidirezionale. Ad esempio, F5 BIG-IP, NGINX con gestione traffico distribuita nella rete virtuale di Azure che si connette a istanze Large di HANA e in locale come una soluzione di routing del traffico o firewall virtuale.
- [Regole di IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) in una VM Linux per abilitare il routing tra le posizioni locali e le unità di istanze Large di HANA o fra tali unità in aree diverse. La macchina virtuale in esecuzione IPTables deve essere distribuito nella rete virtuale di Azure che si connette a istanze Large di HANA e in locale. La macchina virtuale deve essere ridimensionati secondo le esigenze, questa operazione, che la velocità effettiva di rete della macchina virtuale sia sufficiente per il traffico di rete previsto. Per informazioni dettagliate su macchine Virtuali della larghezza di banda di rete, vedere l'articolo [le dimensioni delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- [Firewall di Azure](https://azure.microsoft.com/services/azure-firewall/) sarebbe un'altra soluzione per consentire il traffico diretto tra locale e unità di istanze Large di HANA. 

Tutto il traffico di queste soluzioni verrà indirizzato tramite una rete virtuale di Azure e di conseguenza il traffico è stato possibile limitare ulteriormente tramite l'Appliance temporanea utilizzate o tramite Azure rete gruppi di sicurezza, comunque, determinati indirizzi IP o l'indirizzo IP compreso nell'intervallo da a livello locale è stato bloccato o consentito in modo esplicito l'accesso a istanze Large di HANA. 

> [!NOTE]  
> Tenere presente che Microsoft non fornisce l'implementazione e il supporto per soluzioni personalizzate che riguardano IPTables o appliance di rete di terze parti. Il supporto deve essere reso disponibile dal fornitore del componente usato o dall'integratore. 

#### <a name="express-route-global-reach"></a>Express Route portata globale
Microsoft ha introdotto una nuova funzionalità detta [raggiungere globale ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Copertura globale sono utilizzabili per istanze Large di HANA in due scenari:

- Abilitare l'accesso diretto da un'istanza locale per l'unità di istanze Large di HANA distribuite in aree diverse
- Abilitare la comunicazione diretta tra le unità di istanze Large di HANA distribuite in aree diverse


##### <a name="direct-access-from-on-premise"></a>Accesso diretto da on-premise
Nelle aree di Azure in cui viene offerto raggiungere globale, è possibile richiedere l'attivazione della funzionalità di raggiungere globale per il circuito ExpressRoute che connette la rete locale alla rete virtuale di Azure che si connette a anche le unità di istanze Large di HANA. Esistono alcune implicazioni di costi per il lato locale del circuito ExpressRoute. Per i prezzi, vedere i prezzi per [componente aggiuntivo di raggiungere globale](https://azure.microsoft.com/pricing/details/expressroute/). Non sono previsti costi aggiuntivi per l'utente correlato al circuito che connette l'unità di istanze Large di HANA in Azure. 

> [!IMPORTANT]  
> In caso di utilizzo raggiungono globale per l'accesso diretto tra le unità di istanze Large di HANA e le risorse a livello locale, è il flusso di dati e controllo di rete **non instradato attraverso reti virtuali di Azure**, ma direttamente tra Microsoft router perimetrali Enterprise exchange. Di conseguenza eventuali regole NSG o ASG o qualsiasi tipo di firewall, Appliance di rete virtuale o il proxy è stato distribuito in una rete virtuale di Azure vengono introduzione ignorate. **Se si usa ExpressRoute globale raggiungere per abilitare l'accesso diretto da locale a restrizioni dell'unità istanza Large di HANA e le autorizzazioni per accedere alle unità di istanze large di HANA è necessario definire nei firewall sul lato locale** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>La connessione di istanze Large di HANA in diverse aree di Azure
Nello stesso modo, come raggiungere globale ExpressRoute può essere usato per la connessione a livello locale alle unità di istanze Large di HANA, può essere utilizzato per connettersi due che dai tenant delle istanze Large di HANA distribuite automaticamente in due aree diverse. L'isolamento è i circuiti ExpressRoute che i tenant delle istanze Large di HANA usano per connettersi a entrambe le aree di Azure. Non sono previsti addebiti aggiuntivi per la connessione di due tenant di istanze Large di HANA distribuite in due aree diverse. 

> [!IMPORTANT]  
> Flusso di dati e il flusso di controllo del traffico di rete tra i diversi Large di HANA tenant istanza non verranno indirizzati tramite reti di azure. Di conseguenza è possibile utilizzare funzionalità di Azure o Appliance virtuali di rete per applicare restrizioni di comunicazione tra i due tenant di istanze Large di HANA. 

Per altre informazioni su come ottenere ExpressRoute raggiungere globali abilitati, leggere il documento [connettere una rete virtuale alle istanze large di HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Connettività Internet delle istanze Large di HANA
Le istanze Large di HANA *non* hanno una connettività Internet diretta. Questo limita ad esempio la possibilità di registrare l'immagine del sistema operativo direttamente con il relativo fornitore. Può quindi essere necessario usare il server SUSE Linux Enterprise Server Subscription Management Tool locale o Red Hat Enterprise Linux Subscription Manager.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Crittografia dei dati tra VM e istanze Large di HANA
I dati trasferiti tra le istanze Large di HANA e le VM non vengono crittografati. Tuttavia, esclusivamente per lo scambio tra il lato HANA DBMS e le applicazioni basate su JDBC/ODBC, è possibile abilitare la crittografia del traffico. Per altre informazioni, vedere [questa documentazione di SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Usare le unità di istanze Large di HANA in più aree

Per realizzare ups set di ripristino di emergenza, è necessario disporre di unità di istanze Large SHANA in più aree di Azure. Anche con l'uso di Azure [Peering reti virtuali], il routing transitivo per impostazione predefinita è non funziona tra i tenant delle istanze Large di HANA in due aree diverse. Tuttavia, raggiungere globale viene aperto il percorso di comunicazione tra le unità di istanze Large di HANA che è stato eseguito il provisioning in due aree diverse. Questo scenario di utilizzo di raggiungere globale ExpressRoute consente di:

 - Replica di sistema HANA senza altri proxy o firewall
 - Copia i backup tra le unità di istanze Large di HANA in due aree diverse per eseguire copie system o gli aggiornamenti di sistema


![Rete virtuale connessa ai moduli per istanze Large di Azure in diverse aree di Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

La figura illustra come le reti virtuali diverse in entrambe le aree siano connesse a due circuiti ExpressRoute diversi usati per la connessione a SAP HANA in Azure (istanze Large) in entrambe le aree di Azure (righe grigio). Motivo di questo due connessioni incrociate avviene per impedire un'interruzione di msee su entrambi i lati. Il flusso delle comunicazioni tra le due reti virtuali in due aree di Azure dovrà essere gestita tramite il [peering globale](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) delle due reti virtuali in due aree diverse (linea blu punteggiata). La linea rossa thick descrive la connessione ExpressRoute globale raggiungere, che consente le unità di istanze Large di HANA del tenant in due aree diverse di comunicare tra loro. 

> [!IMPORTANT] 
> Se vengono usati più circuiti ExpressRoute, è necessario definire le impostazioni AS Path Prepending (Percorso AS anteposto) e Local Preference BGP (BGP preferenza locale) per consentire il routing appropriato del traffico.

**Passaggi successivi**
- Fare riferimento all'articolo [Architettura di archiviazione di SAP HANA (istanze Large)](hana-storage-architecture.md)