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
ms.date: 09/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 724a91b6ba0be030a2281bce366e4378892df59b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58011574"
---
# <a name="sap-hana-large-instances-network-architecture"></a>Architettura di rete di SAP HANA (istanze Large)

L'architettura dei servizi di rete di Azure è un componente fondamentale per la corretta distribuzione di applicazioni SAP in istanze Large di HANA. In genere, le distribuzioni di SAP HANA in Azure (istanze di grandi dimensioni) dispongono di un panorama applicativo di SAP più ampio con varie soluzioni SAP e varie dimensioni di database, utilizzo delle risorse della CPU e utilizzo della memoria. È probabile che non tutti i sistemi SAP siano basati su SAP HANA e che il panorama applicativo SAP sia un ibrido con i componenti seguenti:

- Sistemi SAP distribuiti in locale. A causa delle dimensioni, attualmente questi sistemi non possono essere ospitati in Azure. Un esempio può essere un sistema SAP ERP di produzione che viene eseguito su SQL Server (come database) e richiede risorse di memoria o CPU superiori a quelle che possono essere disponibili nelle VM.
- Sistemi SAP basati su SAP HANA distribuiti in locale.
- Sistemi SAP distribuiti nelle VM. Questi sistemi possono essere istanze di sviluppo, test, sandbox o produzione per qualsiasi applicazione basata su SAP NetWeaver distribuibile in VM di Azure in base alle richieste di memoria e all'utilizzo delle risorse. Questi sistemi possono essere basati su database come SQL Server. Per altre informazioni, vedere [supporto nota SAP #1928533-applicazioni SAP in Azure: Prodotti e tipi di macchine Virtuali di Azure supportati](https://launchpad.support.sap.com/#/notes/1928533/E). Possono inoltre essere basati su database come SAP HANA. Per altre informazioni, vedere [SAP HANA certified IaaS platforms](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html) (Piattaforme IaaS certificate per SAP HANA).
- Server applicazioni SAP distribuiti in Azure (nelle VM) che utilizzano SAP HANA in Azure (istanze Large) in moduli per istanze Large in Azure.

Il caso tipico consiste in un panorama applicativo SAP ibrido con quattro o più diversi scenari di distribuzione. In molti casi, tuttavia, i clienti hanno un panorama applicativo SAP completo in esecuzione in Azure. Grazie alla potenza sempre maggiore delle VM, il numero di clienti che sposta tutte le soluzioni SAP in Azure è in aumento.

Le reti di Azure nel contesto dei sistemi SAP distribuiti in Azure non sono complicate e si basano sui principi seguenti:

- Le reti virtuali di Azure devono essere connesse al circuito di ExpressRoute che si connette a una rete locale.
- Un circuito ExpressRoute che connette l'ambiente locale ad Azure deve avere in genere una larghezza di banda di almeno 1 Gbps. Questo minimo garantisce una larghezza di banda sufficiente per il trasferimento dei dati tra i sistemi locali e i sistemi in esecuzione nelle VM e per la connessione degli utenti locali ai sistemi di Azure.
- Per comunicare tra loro, tutti i sistemi SAP in Azure devono essere configurati in reti virtuali.
- Active Directory e DNS ospitati in locale vengono estesi dall'ambiente locale ad Azure tramite ExpressRoute.


> [!NOTE] 
> Dal punto di vista della fatturazione, una singola sottoscrizione di Azure può essere collegata a un solo tenant in un modulo per istanze Large in un'area specifica di Azure. Viceversa, un singolo tenant del modulo per istanze Large può essere collegato a una sola sottoscrizione di Azure. Questo requisito è coerente con quello di altri oggetti fatturabili in Azure.

Se si distribuisce SAP HANA in Azure (istanze Large) in diverse aree di Azure, viene distribuito un tenant separato nel modulo per istanze Large. È possibile eseguire entrambi nella stessa sottoscrizione di Azure purché queste istanze facciano parte dello stesso panorama applicativo SAP. 

> [!IMPORTANT] 
> Con SAP HANA in Azure (istanze Large) è supportata solo la distribuzione di Azure Resource Manager.

 

## <a name="additional-virtual-network-information"></a>Informazioni aggiuntive sulle reti virtuali

Per connettere una rete virtuale a ExpressRoute, è necessario creare un gateway di Azure. Per altre informazioni, vedere [Informazioni sui gateway di rete virtuale per ExpressRoute](../../../expressroute/expressroute-about-virtual-network-gateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

Un gateway di Azure può essere usato con ExpressRoute per la connessione a un'infrastruttura all'esterno di Azure o a un modulo per istanze Large in Azure. Può inoltre essere usato anche per la connessione tra reti virtuali. Per altre informazioni, vedere [Configurare una connessione tra reti per Resource Manager usando PowerShell](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). È possibile connettere il gateway di Azure a un massimo di quattro diverse connessioni ExpressRoute a condizione che tali connessioni provengano da router Microsoft Enterprise Edge diversi. Per altre informazioni, vedere [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

> [!NOTE] 
> La velocità effettiva fornita da un gateway di Azure è diversa per i due casi d'uso. Per altre informazioni, vedere [Informazioni sul gateway VPN](../../../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). La velocità effettiva massima che è possibile ottenere con un gateway di rete virtuale, usando una connessione ExpressRoute, è 10 Gbps. La copia di file tra una VM che si trova in una rete virtuale e un sistema locale (come singolo flusso di copia) non raggiunge la totale velocità effettiva dei diversi SKU del gateway. Per sfruttare l'intera larghezza di banda del gateway di rete virtuale, usare più flussi. In alternativa, è necessario copiare file diversi in flussi paralleli di un singolo file.


## <a name="networking-architecture-for-hana-large-instance"></a>Architettura di rete per le istanze Large di HANA
L'architettura di rete per le istanze Large di HANA può essere suddivisa in quattro parti:

- Rete locale e connessione ExpressRoute ad Azure. Questa parte è costituita dal dominio del cliente ed è connessa ad Azure tramite ExpressRoute. Nell'immagine seguente è riportata in basso a destra.
- Servizi di rete di Azure, come illustrati in precedenza, con reti virtuali, anche in questo caso con gateway. Questa parte è costituita da un'area che richiede una progettazione adatta ai propri requisiti in termini di applicazioni, sicurezza e conformità. L'eventuale uso di istanze Large di HANA è un altro aspetto da tenere in considerazione nella scelta del numero di reti virtuali e di SKU del gateway di Azure. Nell'immagine questa parte è riportata in alto a destra.
- Connettività delle istanze Large di HANA ad Azure tramite la tecnologia ExpressRoute. Questa parte è distribuita e gestita da Microsoft. Il cliente deve solo specificare alcuni intervalli di indirizzi IP e dopo la distribuzione degli asset nelle istanze Large di HANA connettere il circuito ExpressRoute alle reti virtuali. Per altre informazioni, vedere [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 
- Rete nelle istanze Large di HANA, in gran parte trasparente per il cliente.

![Rete virtuale connessa a SAP HANA in Azure (istanze Large) e in locale](./media/hana-overview-architecture/image3-on-premises-infrastructure.png)

L'uso di istanze Large di HANA non influisce sul requisito della connessione degli asset locali ad Azure tramite ExpressRoute e nemmeno sul requisito di una o più reti virtuali su cui vengono eseguite le VM che ospitano il livello applicazione connesso alle istanze di HANA ospitate nelle unità di istanze Large di HANA. 

Le differenze rispetto alle distribuzioni SAP in Azure sono le seguenti:

- Le unità di istanze Large di HANA del tenant del cliente sono connesse alle reti virtuali tramite un altro circuito ExpressRoute. Per separare le condizioni di carico, i collegamenti ExpressRoute dall'ambiente locale alle reti virtuali e i collegamenti tra le reti virtuali di Azure e le istanze Large di HANA non condividono gli stessi router.
- Il profilo di carico di lavoro tra il livello applicazione SAP e le istanze Large di HANA è di diversa natura e prevede un'elevata quantità di richieste e burst di piccole dimensioni come i trasferimenti di dati (set di risultati) da SAP HANA al livello applicazione.
- L'architettura delle applicazioni SAP è più sensibile alla latenza di rete rispetto agli scenari tipici con scambio dei dati tra ambiente locale e Azure.
- Il gateway di rete virtuale ha almeno due connessioni ExpressRoute. Entrambe condividono la larghezza di banda massima per i dati in ingresso del gateway di rete virtuale.

La latenza di rete rilevata tra le VM e le unità di istanze Large di HANA può essere superiore alla latenza di andata e ritorno tipica di una rete da VM a VM. A seconda dell'area di Azure, i valori misurati possono superare la latenza di andata e ritorno di 0,7-ms classificata come inferiore alla media in [SAP Note #1100926 - domande frequenti: Prestazioni di rete](https://launchpad.support.sap.com/#/notes/1100926/E). A seconda dell'area di Azure e dello strumento usato per misurare la latenza di andata e ritorno di rete tra una macchina virtuale di Azure e l'unità di istanze Large di HANA, la latenza misurata può arrivare fino a 2 millisecondi. Nonostante ciò, i clienti distribuiscono applicazioni SAP di produzione basate su SAP HANA in istanze Large di SAP HANA con ottimi risultati. È importante testare accuratamente i processi aziendali nelle istanze Large di HANA in Azure.
 
Per garantire una latenza di rete deterministica tra le VM e le istanze Large di HANA, la scelta dello SKU del gateway di rete virtuale è essenziale. A differenza dei modelli di traffico tra l'ambiente locale e le VM, il modello di traffico tra le VM e le istanze Large di HANA può sviluppare burst di piccole dimensioni ma frequenti di richieste e volumi di dati da trasmettere. Per gestire in modo efficiente tali burst, è consigliabile usare lo SKU del gateway UltraPerformance. Per gli SKU della classe di tipo II delle istanze Large di HANA, l'uso dello SKU UltraPerformance come gateway di rete virtuale è obbligatorio.

> [!IMPORTANT] 
> Considerato il traffico di rete generale tra il livello database e il livello applicazione SAP, per la connessione a SAP HANA in Azure (istanze Large) sono supportati solo gli SKU del gateway HighPerformance o UltraPerformance per le reti virtuali. Per gli SKU della classe di tipo II delle istanze Large di HANA, come gateway di rete virtuale è supportato solo lo SKU UltraPerformance.



## <a name="single-sap-system"></a>Sistema SAP singolo

L'infrastruttura locale illustrata in precedenza è connessa ad Azure tramite ExpressRoute. Il circuito ExpressRoute è connesso a sua volta a un router Enterprise Edge. Per altre informazioni, vedere [Panoramica tecnica di ExpressRoute](../../../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Una volta stabilita la route, questa si connette al backbone di Azure e tutte le aree di Azure risultano accessibili.

> [!NOTE] 
> Per eseguire panorami applicativi SAP in Azure, connettersi al router Enterprise Edge più vicino all'area di Azure nel panorama applicativo SAP. I moduli per istanze Large in Azure sono connessi tramite dispositivi router Enterprise Edge dedicati per ridurre al minimo la latenza di rete tra VM in IaaS di Azure e moduli per istanze Large.

Il gateway di rete virtuale per le VM che ospitano le istanze dell'applicazione SAP è connesso al circuito ExpressRoute. La stessa rete virtuale è connessa a un router Enterprise Edge per la connessione a moduli per istanze Large.

Questo sistema è un esempio semplice di un sistema SAP singolo. Il livello applicazione SAP è ospitato in Azure. Il database SAP HANA viene eseguito su SAP HANA in Azure (istanze Large). Si presuppone che la larghezza di banda del gateway di rete virtuale con velocità effettiva di 2 Gbps o 10 Gbps non rappresenti un collo di bottiglia.

## <a name="multiple-sap-systems-or-large-sap-systems"></a>Sistemi SAP multipli o sistemi SAP di grandi dimensioni

Se vengono distribuiti più sistemi SAP o sistemi SAP di grandi dimensioni per connettersi a SAP HANA in Azure (istanze Large), la velocità effettiva del gateway di rete virtuale potrebbe diventare un collo di bottiglia. In tal caso, dividere i livelli applicazione in più reti virtuali. È anche possibile creare una rete virtuale speciale che si connette alle istanze Large di HANA in casi come questi:

- Esecuzione di backup direttamente da istanze Large di HANA a una VM in Azure che ospita le condivisioni NFS.
- Copia di backup di grandi dimensioni o di altri file dalle unità di istanze di grandi dimensioni HANA su spazio del disco gestito in Azure.

Usare una rete virtuale separata per ospitare le VM che gestiscono l'archiviazione. In questo modo è possibile evitare gli effetti del trasferimento di dati o file di grandi dimensioni dalle istanze Large di HANA ad Azure sul gateway di rete virtuale usato dalle VM che eseguono il livello applicazione SAP. 

Per un'architettura di rete più scalabile:

- Utilizzare più reti virtuali per un livello applicazione SAP singolo e di dimensioni maggiori.
- Distribuire una rete virtuale separata per ogni sistema SAP distribuito anziché combinare questi sistemi SAP in subnet separate nella stessa rete virtuale.

  Un'architettura di rete più scalabile per SAP HANA in Azure (istanze di grandi dimensioni):

![Distribuire il livello applicazione SAP su più reti virtuali](./media/hana-overview-architecture/image4-networking-architecture.png)

La figura mostra la distribuzione del livello applicazione SAP, o dei componenti, su più reti virtuali di Azure. Questa configurazione ha comportato un inevitabile sovraccarico di latenza che si è verificato durante la comunicazione tra le applicazioni ospitate nelle reti virtuali. Per impostazione predefinita, in questa configurazione il traffico di rete tra VM in reti virtuali diverse viene instradato attraverso i router Enterprise Edge. Per ottimizzare e ridurre la latenza di comunicazione tra due reti virtuali è necessario eseguire il peering delle reti virtuali nella stessa area. Questo metodo funziona anche se le reti virtuali si trovano in sottoscrizioni diverse. Con il peering delle rete virtuali, la comunicazione diretta tra VM in due diverse reti virtuali di Azure può avvenire tramite il backbone della rete di Azure. La latenza risulta simile a quella generata da VM presenti nella stessa rete virtuale. Il traffico indirizzato agli intervalli di indirizzi IP connessi tramite il gateway di rete virtuale di Azure viene instradato attraverso il singolo gateway della rete virtuale. 

Per altre informazioni sul peering delle reti virtuali, vedere [Peering di rete virtuale](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview).


## <a name="routing-in-azure"></a>Routing in Azure

Per SAP HANA in Azure (istanze Large) sono importanti tre considerazioni sul routing di rete:

* SAP HANA in Azure (istanze Large) è accessibile solo tramite le VM e la connessione ExpressRoute dedicata, non direttamente dall'ambiente locale. L'accesso diretto dall'ambiente locale alle unità di istanze Large di HANA, come distribuite da Microsoft, non è consentito nell'immediato. Le restrizioni relative al routing temporaneo sono dovute all'attuale architettura di rete di Azure usata per le istanze Large di SAP HANA. Alcuni client di amministrazione e le applicazioni che richiedono l'accesso diretto, ad esempio SAP Solution Manager in esecuzione in locale, non possono connettersi al database SAP HANA.

* Se si hanno unità di istanze Large di HANA distribuite in due diverse aree di Azure allo scopo di ripristino di emergenza, vengono applicate le stesse restrizioni relative al routing temporaneo. In altre parole, gli indirizzi IP di un'unità di istanze Large di HANA in un'area, ad esempio Stati Uniti occidentali, non vengono instradati a un'unità di istanze Large di HANA distribuita in un'altra area, ad esempio Stati Uniti orientali. Questa restrizione è indipendente dall'uso del peering di rete di Azure tra le aree o del collegamento incrociato dei circuiti ExpressRoute che connettono le unità di istanze Large di HANA alle reti virtuali. Per una rappresentazione grafica, vedere la figura nella sezione "Usare le unità di istanze Large di HANA in più aree". Questa restrizione, integrata nell'architettura distribuita, impedisce l'uso immediato della replica del sistema HANA come funzionalità di ripristino di emergenza.

* Alle unità SAP HANA in Azure (istanze Large) viene assegnato un indirizzo IP dall'intervallo di indirizzi del pool IP del server che è stato inviato. Per altre informazioni, vedere [Infrastruttura e connettività a SAP HANA (istanze Large) in Azure](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Questo indirizzo IP è accessibile tramite le sottoscrizioni di Azure ed ExpressRoute che connette le reti virtuali di Azure ad HANA in Azure (istanze Large). L'indirizzo IP assegnato al di fuori dell'intervallo di indirizzi del pool IP del server viene assegnato direttamente all'unità hardware. *Non* viene più assegnato tramite NAT, come avveniva nelle prime distribuzioni di questa soluzione. 

> [!NOTE]
> Per superare la restrizione relativa al routing temporaneo, come illustrato nelle prime due voci dell'elenco precedente, usare componenti aggiuntivi per il routing. I componenti che consentono di superare la restrizione possono essere:
> 
> * Un proxy inverso per instradare i dati in modo bidirezionale. Ad esempio, F5 BIG-IP, NGINX con Gestione traffico distribuita in Azure come soluzione di routing del traffico o del firewall virtuale.
> * [Regole di IPTables](http://www.linuxhomenetworking.com/wiki/index.php/Quick_HOWTO_%3a_Ch14_%3a_Linux_Firewalls_Using_iptables#.Wkv6tI3rtaQ) in una VM Linux per abilitare il routing tra le posizioni locali e le unità di istanze Large di HANA o fra tali unità in aree diverse.
> 
> Tenere presente che Microsoft non fornisce l'implementazione e il supporto per soluzioni personalizzate che riguardano IPTables o appliance di rete di terze parti. Il supporto deve essere reso disponibile dal fornitore del componente usato o dall'integratore. 

## <a name="internet-connectivity-of-hana-large-instance"></a>Connettività Internet delle istanze Large di HANA
Le istanze Large di HANA *non* hanno una connettività Internet diretta. Questo limita ad esempio la possibilità di registrare l'immagine del sistema operativo direttamente con il relativo fornitore. Può quindi essere necessario usare il server SUSE Linux Enterprise Server Subscription Management Tool locale o Red Hat Enterprise Linux Subscription Manager.

## <a name="data-encryption-between-vms-and-hana-large-instance"></a>Crittografia dei dati tra VM e istanze Large di HANA
I dati trasferiti tra le istanze Large di HANA e le VM non vengono crittografati. Tuttavia, esclusivamente per lo scambio tra il lato HANA DBMS e le applicazioni basate su JDBC/ODBC, è possibile abilitare la crittografia del traffico. Per altre informazioni, vedere [questa documentazione di SAP](http://help-legacy.sap.com/saphelp_hanaplatform/helpdata/en/db/d3d887bb571014bf05ca887f897b99/content.htm?frameset=/en/dd/a2ae94bb571014a48fc3b22f8e919e/frameset.htm&current_toc=/en/de/ec02ebbb57101483bdf3194c301d2e/plain.htm&node_id=20&show_children=false).

## <a name="use-hana-large-instance-units-in-multiple-regions"></a>Usare le unità di istanze Large di HANA in più aree

È possibile distribuire SAP HANA in Azure (istanze Large) in più aree di Azure per motivi diversi dal ripristino di emergenza. Ad esempio, se si vuole accedere a istanze Large di HANA da ognuna delle VM distribuite nelle diverse reti virtuali nelle aree. Gli indirizzi IP assegnati alle diverse unità di istanze Large di HANA non vengono propagati oltre le reti virtuali di Azure, che sono direttamente connesse alle istanze tramite il gateway. Si ha pertanto una lieve variazione del progetto di rete virtuale. Un gateway di rete virtuale può gestire quattro circuiti ExpressRoute diversi da vari router Enterprise Edge. Ogni rete virtuale connessa a uno dei moduli per istanze Large può essere connessa a tale modulo in un'altra area di Azure.

![Rete virtuale connessa ai moduli per istanze Large di Azure in diverse aree di Azure](./media/hana-overview-architecture/image8-multiple-regions.png)

La figura mostra come le diverse reti virtuali nelle due aree siano connesse a due circuiti ExpressRoute diversi usati per la connessione a SAP HANA in Azure (istanze Large) in entrambe le aree di Azure. Le connessioni appena introdotte sono indicate dalle linee rosse con profilo rettangolare. Con queste connessioni dalle reti virtuali, le VM in esecuzione in una di tali reti possono accedere a ognuna delle diverse unità di istanze Large di HANA distribuite nelle due aree. Come mostrato nella figura, si presuppone che siano configurate due connessioni ExpressRoute dall'ambiente locale alle due aree di Azure. Questa configurazione è consigliata per il ripristino di emergenza.

> [!IMPORTANT] 
> Se vengono usati più circuiti ExpressRoute, è necessario definire le impostazioni AS Path Prepending (Percorso AS anteposto) e Local Preference BGP (BGP preferenza locale) per consentire il routing appropriato del traffico.

**Passaggi successivi**
- Fare riferimento all'articolo [Architettura di archiviazione di SAP HANA (istanze Large)](hana-storage-architecture.md)