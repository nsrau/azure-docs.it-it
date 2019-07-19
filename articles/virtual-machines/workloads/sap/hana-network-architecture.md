---
title: Architettura di rete di SAP HANA in Azure (istanze Large) | Microsoft Docs
description: Architettura di rete della distribuzione di SAP HANA in Azure (istanze Large).
services: virtual-machines-linux
documentationcenter: ''
author: RicksterCDN
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 01001336e166d5eb2c7dff845b80da2174225a25
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234420"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Architettura di rete di SAP HANA (istanze Large)

L'architettura dei servizi di rete di Azure è un componente fondamentale per la corretta distribuzione di applicazioni SAP in istanze Large di HANA. In genere, le distribuzioni di SAP HANA in Azure (istanze di grandi dimensioni) dispongono di un panorama applicativo di SAP più ampio con varie soluzioni SAP e varie dimensioni di database, utilizzo delle risorse della CPU e utilizzo della memoria. È probabile che non tutti i sistemi IT si trovino già in Azure. Il panorama applicativo SAP è spesso ibrido anche da un punto di DBMS e da un punto di vista dell'applicazione SAP usando una combinazione di NetWeaver e S/4HANA e SAP HANA e di altri sistemi DBMS. Azure offre diversi servizi che consentono di eseguire i diversi sistemi DBMS, NetWeaver e S/4HANA in Azure. Azure ti offre anche la tecnologia di rete per rendere Azure simile a una data center virtuale alle distribuzioni software locali

A meno che i sistemi IT completi non siano ospitati in Azure. La funzionalità di rete di Azure viene usata per connettere il mondo locale con le risorse di Azure per rendere Azure simile a un data center virtuale. La funzionalità di rete di Azure usata è: 

- Le reti virtuali di Azure sono connesse al circuito [ExpressRoute](https://azure.microsoft.com/services/expressroute/) che si connette alle risorse di rete locali.
- Un circuito ExpressRoute che si connette da locale ad Azure deve avere una larghezza di banda minima di [1 Gbps o superiore](https://azure.microsoft.com/pricing/details/expressroute/). Questo minimo garantisce una larghezza di banda sufficiente per il trasferimento dei dati tra i sistemi locali e i sistemi in esecuzione nelle VM e per la connessione degli utenti locali ai sistemi di Azure.
- Tutti i sistemi SAP in Azure sono configurati in reti virtuali per comunicare tra loro.
- Active Directory e DNS ospitati in locale sono estesi in Azure tramite ExpressRoute da locale o sono in esecuzione completi in Azure.

Per il caso specifico di integrazione di istanze large di HANA nell'infrastruttura di rete data center di Azure, viene usata anche la tecnologia Azure ExpressRoute


> [!NOTE] 
> Solo una sottoscrizione di Azure può essere collegata a un solo tenant in un timbro di istanze large di HANA in un'area di Azure specifica. Viceversa, un singolo tenant del timbro di istanze large di HANA può essere collegato a una sola sottoscrizione di Azure. Questo requisito è coerente con quello di altri oggetti fatturabili in Azure.

Se SAP HANA in Azure (istanze large) viene distribuita in più aree di Azure diverse, viene distribuito un tenant separato nel modulo per istanze large di HANA. È possibile eseguire entrambi nella stessa sottoscrizione di Azure purché queste istanze facciano parte dello stesso panorama applicativo SAP. 

> [!IMPORTANT] 
> Solo il metodo di distribuzione Azure Resource Manager è supportato con SAP HANA in Azure (istanze large).

 

## <a name="additional-virtual-network-information"></a>Informazioni aggiuntive sulle reti virtuali

Per connettere una rete virtuale a ExpressRoute, è necessario creare un gateway ExpressRoute di Azure. Per ulteriori informazioni, vedere [informazioni sui gateway Expressroute per Expressroute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Un gateway Azure ExpressRoute viene usato con ExpressRoute a un'infrastruttura esterna ad Azure o a un indicatore di istanze large di Azure. È possibile connettere il gateway Azure ExpressRoute a un massimo di quattro circuiti ExpressRoute diversi, purché tali connessioni provengano da router perimetrali Microsoft Enterprise differenti. Per altre informazioni, vedere [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> La velocità effettiva massima che è possibile ottenere con un gateway ExpressRoute è 10 Gbps usando una connessione ExpressRoute. La copia di file tra una VM che si trova in una rete virtuale e un sistema locale (come singolo flusso di copia) non raggiunge la totale velocità effettiva dei diversi SKU del gateway. Per sfruttare l'intera larghezza di banda del gateway ExpressRoute, usare più flussi. In alternativa, è necessario copiare file diversi in flussi paralleli di un singolo file.


## <a name="networking-architecture-for-hana-large-instance"></a>Architettura di rete per le istanze Large di HANA
L'architettura di rete per le istanze Large di HANA può essere suddivisa in quattro parti:

- Rete locale e connessione ExpressRoute ad Azure. Questa parte è costituita dal dominio del cliente ed è connessa ad Azure tramite ExpressRoute. Questo circuito Expressroute viene completamente pagato dal cliente. La larghezza di banda deve essere sufficientemente grande da poter gestire il traffico di rete tra le risorse locali e l'area di Azure in cui si esegue la connessione. Nell'immagine seguente è riportata in basso a destra.
- Servizi di rete di Azure, come descritto in precedenza, con le reti virtuali, che richiedono di nuovo l'aggiunta di gateway ExpressRoute. Questa parte è costituita da un'area che richiede una progettazione adatta ai propri requisiti in termini di applicazioni, sicurezza e conformità. L'eventuale uso di istanze Large di HANA è un altro aspetto da tenere in considerazione nella scelta del numero di reti virtuali e di SKU del gateway di Azure. Nell'immagine questa parte è riportata in alto a destra.
- Connettività delle istanze Large di HANA ad Azure tramite la tecnologia ExpressRoute. Questa parte è distribuita e gestita da Microsoft. Il cliente deve solo specificare alcuni intervalli di indirizzi IP e dopo la distribuzione degli asset nelle istanze Large di HANA connettere il circuito ExpressRoute alle reti virtuali. Per altre informazioni, vedere [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Non sono previsti costi aggiuntivi come cliente per la connettività tra l'infrastruttura di rete data center di Azure e le unità di istanze large di HANA.
- Funzionalità di rete all'interno del timbro di istanze large di HANA, che è per lo più trasparente.

![Rete virtuale connessa a SAP HANA in Azure (istanze Large) e in locale](./media/hana-overview-architecture/image1-architecture.png)

L'uso di istanze Large di HANA non influisce sul requisito della connessione degli asset locali ad Azure tramite ExpressRoute e nemmeno sul requisito di una o più reti virtuali su cui vengono eseguite le VM che ospitano il livello applicazione connesso alle istanze di HANA ospitate nelle unità di istanze Large di HANA. 

Le differenze rispetto alle distribuzioni SAP in Azure sono le seguenti:

- Le unità di istanze Large di HANA del tenant del cliente sono connesse alle reti virtuali tramite un altro circuito ExpressRoute. Per separare le condizioni di carico, i circuiti ExpressRoute da locale a rete virtuale di Azure e i circuiti tra le reti virtuali di Azure e le istanze large di HANA non condividono gli stessi router.
- Il profilo di carico di lavoro tra il livello applicazione SAP e le istanze Large di HANA è di diversa natura e prevede un'elevata quantità di richieste e burst di piccole dimensioni come i trasferimenti di dati (set di risultati) da SAP HANA al livello applicazione.
- L'architettura delle applicazioni SAP è più sensibile alla latenza di rete rispetto agli scenari tipici con scambio dei dati tra ambiente locale e Azure.
- Il gateway Azure ExpressRoute ha almeno due connessioni ExpressRoute. Un circuito connesso da locale e uno connesso dalle istanze large di HANA. Questa operazione lascia solo spazio per altri due circuiti aggiuntivi da MSEE diversi per la connessione al gateway ExpressRoute. Questa restrizione è indipendente dall'utilizzo del percorso rapido ExpressRoute. Tutti i circuiti connessi condividono la larghezza di banda massima per i dati in ingresso del gateway ExpressRoute.

Con la revisione 3 degli indicatori di istanze large di HANA, la latenza di rete riscontrata tra le macchine virtuali e le unità di istanze large di HANA può essere superiore rispetto a una tipica latenza di round trip della rete da macchina virtuale a macchina virtuale. A seconda dell'area di Azure, i valori misurati possono superare la latenza di round trip 0,7-ms classificata [come inferiore alla media nella nota SAP #1100926-domande frequenti: Prestazioni](https://launchpad.support.sap.com/#/notes/1100926/E)di rete. A seconda dell'area di Azure e dello strumento usato per misurare la latenza di andata e ritorno di rete tra una macchina virtuale di Azure e l'unità di istanze Large di HANA, la latenza misurata può arrivare fino a 2 millisecondi. Nonostante ciò, i clienti distribuiscono applicazioni SAP di produzione basate su SAP HANA in istanze Large di SAP HANA con ottimi risultati. È importante testare accuratamente i processi aziendali nelle istanze Large di HANA in Azure. Una nuova funzionalità, denominata percorso rapido ExpressRoute, è in grado di ridurre la latenza di rete tra le istanze large di HANA e le macchine virtuali a livello di applicazione in Azure in modo sostanziale (vedere di seguito). 

Con la revisione 4 degli indicatori di istanze large di Hana, la latenza di rete tra le macchine virtuali di Azure distribuite in prossimità del timbro di istanze large di Hana è stata rilevata per [soddisfare la media o meglio della classificazione media, come documentato nella nota SAP #1100926-DOMANDE FREQUENTI: Prestazioni](https://launchpad.support.sap.com/#/notes/1100926/E) di rete se è configurato il percorso rapido di Azure ExpressRoute (vedere di seguito). Per distribuire le macchine virtuali di Azure in prossimità di unità di istanze large di HANA 4, è necessario sfruttare i [gruppi di posizionamento di prossimità di Azure](https://docs.microsoft.com/azure/virtual-machines/linux/co-location). Il modo in cui è possibile usare i gruppi di posizionamento di prossimità per individuare il livello dell'applicazione SAP nello stesso data center di Azure in cui sono ospitate le unità di istanze large per la revisione 4 ospitate in [gruppi di posizionamento di Azure per la latenza di rete ottimale con le applicazioni SAP ](sap-proximity-placement-scenarios.md).

Per garantire una latenza di rete deterministica tra le VM e le istanze large di HANA, la scelta dello SKU del gateway ExpressRoute è essenziale. A differenza dei modelli di traffico tra l'ambiente locale e le VM, il modello di traffico tra le VM e le istanze Large di HANA può sviluppare burst di piccole dimensioni ma frequenti di richieste e volumi di dati da trasmettere. Per gestire in modo efficiente tali burst, è consigliabile usare lo SKU del gateway UltraPerformance. Per la classe di tipo II di SKU di istanze large di HANA, è obbligatorio usare lo SKU del gateway UltraPerformance come gateway Expressroute.

> [!IMPORTANT] 
> Considerato il traffico di rete generale tra il livello database e il livello applicazione SAP, per la connessione a SAP HANA in Azure (istanze Large) sono supportati solo gli SKU del gateway HighPerformance o UltraPerformance per le reti virtuali. Per gli SKU di tipo II di istanze large di HANA, solo lo SKU del gateway UltraPerformance è supportato come gateway ExpressRoute. Si applicano eccezioni quando si usa il percorso rapido ExpressRoute (vedere di seguito)

### <a name="expressroute-fast-path"></a>Percorso rapido ExpressRoute
Per ridurre la latenza, il percorso rapido di ExpressRoute è stato introdotto e rilasciato nel 2019 maggio per la connettività specifica di istanze large di HANA alle reti virtuali di Azure che ospitano le macchine virtuali dell'applicazione SAP. La differenza principale rispetto alla soluzione è stata implementata finora, consiste nel fatto che i flussi di dati tra le macchine virtuali e le istanze large di HANA non vengono più indirizzati attraverso il gateway ExpressRoute. Le macchine virtuali assegnate nelle subnet della rete virtuale di Azure, invece, comunicano direttamente con il router perimetrale aziendale dedicato. 

> [!IMPORTANT] 
> Per la funzionalità di percorso rapido di ExpressRoute è necessario che le subnet che eseguono le macchine virtuali dell'applicazione SAP si trovino nella stessa rete virtuale di Azure che è stata connessa alle istanze large di HANA. Le macchine virtuali che si trovano in reti virtuali di Azure con peering con la rete virtuale di Azure connessa direttamente alle unità di istanze large di HANA non traggono vantaggio dal percorso rapido ExpressRoute. Di conseguenza, le tipiche progettazioni di reti virtuali Hub e spoke, in cui i circuiti ExpressRoute si connettono a una rete virtuale dell'hub e le reti virtuali che contengono il livello dell'applicazione SAP (spoke) ricevono il peering, l'ottimizzazione di ExpressRoute veloce Il percorso non funzionerà. In aggiunta il percorso ExpressRoute Fast non supporta attualmente le regole di routing definite dall'utente (UDR). Per altre informazioni, vedere [gateway di rete virtuale ExpressRoute e FastPath](https://docs.microsoft.com/azure/expressroute/expressroute-about-virtual-network-gateways). 


Per altre informazioni su come configurare il percorso rapido di ExpressRoute, vedere il documento [connettere una rete virtuale a istanze large di Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).    

> [!NOTE]
> È necessario un gateway ExpressRoute di UltraPerformance per il corretto funzionamento di ExpressRoute percorso rapido


## <a name="single-sap-system"></a>Sistema SAP singolo

L'infrastruttura locale illustrata in precedenza è connessa ad Azure tramite ExpressRoute. Il circuito ExpressRoute si connette a un router Microsoft Enterprise Edge (MSEE). Per altre informazioni, vedere [Panoramica tecnica di ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Una volta stabilita la route, si connette al backbone di Azure.

> [!NOTE] 
> Per eseguire panorami applicativi SAP in Azure, connettersi al router Enterprise Edge più vicino all'area di Azure nel panorama applicativo SAP. I timbri delle istanze large di HANA sono connessi tramite dispositivi router perimetrali aziendali dedicati per ridurre al minimo la latenza di rete tra le macchine virtuali in IaaS di Azure e gli indicatori di istanze large

Il gateway ExpressRoute per le macchine virtuali che ospitano le istanze dell'applicazione SAP è connesso a un circuito ExpressRoute che si connette a un'istanza locale. La stessa rete virtuale è connessa a un router Enterprise Edge per la connessione a moduli per istanze Large. Usando il percorso veloce ExpressRoute, il flusso di dati dalle istanze large di HANA alle VM a livello di applicazione SAP non viene più indirizzato attraverso il gateway ExpressRoute e con tale riduzione della latenza di round trip di rete.

Questo sistema è un esempio semplice di un sistema SAP singolo. Il livello applicazione SAP è ospitato in Azure. Il database SAP HANA viene eseguito su SAP HANA in Azure (istanze Large). Si presuppone che la larghezza di banda del gateway ExpressRoute della velocità effettiva di 2 Gbps o 10 Gbps non rappresenti un collo di bottiglia.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Sistemi SAP multipli o sistemi SAP di grandi dimensioni

Se vengono distribuiti più sistemi SAP o sistemi SAP di grandi dimensioni per connettersi a SAP HANA in Azure (istanze large), la velocità effettiva del gateway ExpressRoute potrebbe diventare un collo di bottiglia. Oppure si vuole isolare i sistemi di produzione e non di produzione in reti virtuali di Azure diverse. In tal caso, dividere i livelli applicazione in più reti virtuali. È anche possibile creare una rete virtuale speciale che si connette alle istanze Large di HANA in casi come questi:

- Esecuzione di backup direttamente da istanze Large di HANA a una VM in Azure che ospita le condivisioni NFS.
- Copia di backup di grandi dimensioni o di altri file dalle unità di istanze di grandi dimensioni HANA su spazio del disco gestito in Azure.

Usare una rete virtuale separata per ospitare le VM che gestiscono l'archiviazione per il trasferimento di massa dei dati tra le istanze large di HANA e Azure. Questa soluzione evita gli effetti del trasferimento di file o dati di grandi dimensioni dalle istanze large di HANA ad Azure sul gateway ExpressRoute che serve le macchine virtuali che eseguono il livello dell'applicazione SAP. 

Per un'architettura di rete più scalabile:

- Utilizzare più reti virtuali per un livello applicazione SAP singolo e di dimensioni maggiori.
- Distribuire una rete virtuale separata per ogni sistema SAP distribuito anziché combinare questi sistemi SAP in subnet separate nella stessa rete virtuale.

  Un'architettura di rete più scalabile per SAP HANA in Azure (istanze di grandi dimensioni):

![Distribuire il livello applicazione SAP su più reti virtuali](./media/hana-overview-architecture/image4-networking-architecture.png)

A seconda delle regole e delle restrizioni, si desidera applicare tra le diverse reti virtuali che ospitano macchine virtuali di sistemi SAP diversi, è necessario eseguire il peering delle reti virtuali. Per altre informazioni sul peering delle reti virtuali, vedere [Peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Routing in Azure

Per impostazione predefinita, sono importanti tre considerazioni sul routing di rete per SAP HANA in Azure (istanze large):

* SAP HANA in Azure (istanze large) sono accessibili solo tramite macchine virtuali di Azure e la connessione ExpressRoute dedicata, non direttamente dall'ambiente locale. L'accesso diretto dall'ambiente locale alle unità di istanze Large di HANA, come distribuite da Microsoft, non è consentito nell'immediato. Le restrizioni relative al routing temporaneo sono dovute all'attuale architettura di rete di Azure usata per le istanze Large di SAP HANA. Alcuni client di amministrazione e le applicazioni che richiedono l'accesso diretto, ad esempio SAP Solution Manager in esecuzione in locale, non possono connettersi al database SAP HANA. Per le eccezioni, controllare la sezione "routing diretto a istanze large di HANA".

* Se si dispone di unità di istanze large di HANA distribuite in due aree di Azure diverse per il ripristino di emergenza, vengono applicate in passato le stesse restrizioni di routing temporaneo. In altre parole, gli indirizzi IP di un'unità di istanze large di HANA in un'area, ad esempio Stati Uniti occidentali, non sono stati instradati a un'unità di istanze large di HANA distribuita in un'altra area (ad esempio, Stati Uniti orientali). Questa restrizione era indipendente dall'uso del peering di rete di Azure tra le aree o dalla connessione incrociata dei circuiti ExpressRoute che connettono le unità di istanze large di HANA alle reti virtuali. Per una rappresentazione grafica, vedere la figura nella sezione "Usare le unità di istanze Large di HANA in più aree". Questa restrizione, fornita con l'architettura distribuita, non ha consentito l'uso immediato della replica di sistema HANA come funzionalità di ripristino di emergenza. Per le modifiche recenti, cercare la sezione "usare unità di istanze large di HANA in più aree". 

* SAP HANA nelle unità di Azure (istanze large) hanno un indirizzo IP assegnato dall'intervallo di indirizzi del pool IP del server inviato quando si richiede la distribuzione di istanze large di HANA. Per altre informazioni, vedere [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Questo indirizzo IP è accessibile tramite le sottoscrizioni di Azure e il circuito che connette le reti virtuali di Azure alle istanze large di HANA. L'indirizzo IP assegnato al di fuori dell'intervallo di indirizzi del pool IP del server viene assegnato direttamente all'unità hardware. *Non* viene più assegnato tramite NAT, come avveniva nelle prime distribuzioni di questa soluzione. 

### <a name="direct-routing-to-hana-large-instances"></a>Routing diretto a istanze large di HANA
Per impostazione predefinita, il routing transitivo tra le unità di istanze large di HANA e il routing locale o tra le istanze large di HANA distribuite in due aree diverse non funziona. Sono disponibili diverse possibilità per abilitare questo routing transitivo.

- Un proxy inverso per instradare i dati in modo bidirezionale. Ad esempio, F5 BIG-IP, NGINX con Traffic Manager distribuito nella rete virtuale di Azure che si connette alle istanze large di HANA e alla soluzione locale come firewall virtuale/routing del traffico.
- [Regole di IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) in una VM Linux per abilitare il routing tra le posizioni locali e le unità di istanze Large di HANA o fra tali unità in aree diverse. La VM che esegue IPTables deve essere distribuita nella rete virtuale di Azure che si connette alle istanze large di HANA e in locale. La macchina virtuale deve essere dimensionata di conseguenza, quindi la velocità effettiva della rete della macchina virtuale è sufficiente per il traffico di rete previsto. Per informazioni dettagliate sulla larghezza di banda della rete VM, vedere le [dimensioni degli articoli delle macchine virtuali Linux in Azure](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Il [firewall di Azure](https://azure.microsoft.com/services/azure-firewall/) costituisce un'altra soluzione per consentire il traffico diretto tra unità locali e istanze large di Hana. 

Tutto il traffico di queste soluzioni verrebbe indirizzato attraverso una rete virtuale di Azure e, di conseguenza, il traffico potrebbe essere ulteriormente limitato dalle appliance Soft usate o dai gruppi di sicurezza di rete di Azure, quindi, che determinati indirizzi IP o intervalli di indirizzi IP da in locale può essere bloccato o consentito in modo esplicito l'accesso alle istanze large di HANA. 

> [!NOTE]  
> Tenere presente che Microsoft non fornisce l'implementazione e il supporto per soluzioni personalizzate che riguardano IPTables o appliance di rete di terze parti. Il supporto deve essere reso disponibile dal fornitore del componente usato o dall'integratore. 

#### <a name="express-route-global-reach"></a>Copertura globale Express Route
Microsoft ha introdotto una nuova funzionalità denominata [ExpressRoute copertura globale](https://docs.microsoft.com/azure/expressroute/expressroute-global-reach). Copertura globale può essere usato per le istanze large di HANA in due scenari:

- Abilitare l'accesso diretto dall'ambiente locale alle unità di istanze large di HANA distribuite in aree diverse
- Abilitare la comunicazione diretta tra le unità di istanze large di HANA distribuite in aree diverse


##### <a name="direct-access-from-on-premise"></a>Accesso diretto da locale
Nelle aree di Azure in cui viene offerto Copertura globale è possibile richiedere l'abilitazione della funzionalità Copertura globale per il circuito ExpressRoute che connette la rete locale alla rete virtuale di Azure che si connette anche alle unità di istanze large di HANA. Esistono alcune implicazioni sui costi per il lato locale del circuito ExpressRoute. Per i prezzi, verificare i prezzi per [copertura globale componente](https://azure.microsoft.com/pricing/details/expressroute/)aggiuntivo. Non sono previsti costi aggiuntivi correlati al circuito che connette le unità di istanze large di HANA ad Azure. 

> [!IMPORTANT]  
> Se si usa Copertura globale per abilitare l'accesso diretto tra le unità di istanze large di HANA e le risorse locali, i dati di rete e il flusso di controllo **non vengono indirizzati attraverso le reti virtuali di Azure**, ma direttamente tra Microsoft Enterprise Exchange router. Di conseguenza, le regole NSG o ASG, o qualsiasi tipo di firewall, appliance virtuale di rete o proxy distribuito in una rete virtuale di Azure, non vengono rimosse. **Se si usa ExpressRoute Copertura globale per abilitare l'accesso diretto da un'unità locale a un'istanza large di HANA, le restrizioni e le autorizzazioni per l'accesso alle unità di istanze large di HANA devono essere definite nei firewall sul lato locale** 

##### <a name="connecting-hana-large-instances-in-different-azure-regions"></a>Connessione di istanze large di HANA in diverse aree di Azure
Allo stesso modo, poiché ExpressRoute Copertura globale può essere usato per la connessione da locale a unità di istanze large di HANA, può essere usato per connettere i tenant di istanze large di HANA distribuiti in due aree diverse. L'isolamento è il circuito ExpressRoute che i tenant di istanze large di HANA usano per connettersi ad Azure in entrambe le aree. Non sono previsti addebiti aggiuntivi per la connessione di due tenant di istanze large di HANA distribuiti in due aree diverse. 

> [!IMPORTANT]  
> Il flusso di dati e il flusso di controllo del traffico di rete tra i diversi tenant di istanze large di HANA non verranno instradati attraverso le reti di Azure. Di conseguenza, non è possibile usare la funzionalità di Azure o appliance virtuali per applicare le restrizioni di comunicazione tra i due tenant di istanze large di HANA. 

Per altri dettagli su come ottenere ExpressRoute Copertura globale abilitata, leggere il documento [connettere una rete virtuale a istanze large di Hana](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-connect-vnet-express-route).


## <a name="internet-connectivity-of-hana-large-instance"></a>Connettività Internet delle istanze Large di HANA
Le istanze Large di HANA *non* hanno una connettività Internet diretta. Questo limita ad esempio la possibilità di registrare l'immagine del sistema operativo direttamente con il relativo fornitore. Può quindi essere necessario usare il server SUSE Linux Enterprise Server Subscription Management Tool locale o Red Hat Enterprise Linux Subscription Manager.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Crittografia dei dati tra VM e istanze Large di HANA
I dati trasferiti tra le istanze Large di HANA e le VM non vengono crittografati. Tuttavia, esclusivamente per lo scambio tra il lato HANA DBMS e le applicazioni basate su JDBC/ODBC, è possibile abilitare la crittografia del traffico. Per altre informazioni, vedere [questa documentazione di SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Usare le unità di istanze Large di HANA in più aree

Per realizzare set di ripristino di emergenza, è necessario avere unità di istanze large di SHANA in più aree di Azure. Anche con l'uso di Azure [peering VNET globale], per impostazione predefinita il routing transitivo non funziona tra i tenant di istanze large di HANA in due aree diverse. Tuttavia, Copertura globale apre il percorso di comunicazione tra le unità di istanze large di HANA di cui è stato effettuato il provisioning in due aree diverse. Questo scenario di utilizzo di ExpressRoute Copertura globale consente di:

 - Replica di sistema HANA senza proxy o firewall aggiuntivi
 - Copia di backup tra unità di istanze large di HANA in due aree diverse per eseguire copie di sistema o aggiornamenti del sistema


![Rete virtuale connessa ai moduli per istanze Large di Azure in diverse aree di Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

La figura mostra come le diverse reti virtuali in entrambe le aree siano connesse a due circuiti ExpressRoute diversi usati per connettersi a SAP HANA in Azure (istanze di grandi dimensioni) in entrambe le aree di Azure (linee grigie). Il motivo di queste due connessioni incrociate è la protezione da un'interruzione del MSEE su entrambi i lati. Il flusso di comunicazione tra le due reti virtuali nelle due aree di Azure deve essere gestito sul peering [globale](https://blogs.msdn.microsoft.com/azureedu/2018/04/24/how-to-setup-global-vnet-peering-in-azure/) delle due reti virtuali nelle due aree diverse (linea blu tratteggiata). La linea rossa spessa descrive la connessione Copertura globale ExpressRoute, che consente alle unità di istanze large di HANA dei tenant in due aree diverse di comunicare tra loro. 

> [!IMPORTANT] 
> Se vengono usati più circuiti ExpressRoute, è necessario definire le impostazioni AS Path Prepending (Percorso AS anteposto) e Local Preference BGP (BGP preferenza locale) per consentire il routing appropriato del traffico.

**Passaggi successivi**
- Fare riferimento all'articolo [Architettura di archiviazione di SAP HANA (istanze Large)](hana-storage-architecture.md)