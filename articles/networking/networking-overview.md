---
title: Rete di Azure | Microsoft Docs
description: "Informazioni sulle funzionalità e i servizi di rete di Azure."
services: networking
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/19/2017
ms.author: jdial
ms.openlocfilehash: 7ed018c8c9759bc497c5fea129257486f6128531
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="azure-networking"></a>Rete di Azure

Azure offre un'ampia gamma di funzionalità di rete che possono essere usate insieme o separatamente. Per altre informazioni, fare clic su una qualsiasi delle funzionalità chiave seguenti:
- [Connettività tra risorse di Azure](#connectivity): permette di connettere tra loro le risorse di Azure in una rete virtuale privata protetta nel cloud.
- [Connettività Internet](#internet-connectivity): consente la comunicazione in ingresso e in uscita dalle risorse di Azure tramite Internet.
- [Connettività locale](#on-premises-connectivity): permette di connettere una rete locale alle risorse di Azure tramite una rete privata virtuale (VPN) tramite Internet o tramite una connessione ad Azure dedicata.
- [Bilanciamento del carico e indirizzamento del traffico](#load-balancing): permettono di bilanciare il carico del traffico ai server nella stessa località e di indirizzare il traffico ai server in località diverse.
- [Sicurezza](#security): permette di filtrare il traffico di rete tra le subnet della rete o le singole macchine virtuali (VM).
- [Routing](#routing): consente l'uso del routing predefinito o il controllo completo del routing tra le risorse di Azure e quelle locali.
- [Gestibilità](#manageability): permette di monitorare e gestire le risorse di rete di Azure.
- [Strumenti di distribuzione e configurazione](#tools): permettono di usare un portale basato sul Web o strumenti da riga di comando multipiattaforma per distribuire e configurare le risorse di rete.

## <a name="Connectivity"></a>Connettività tra risorse di Azure

Le risorse di Azure, quali ad esempio Macchine virtuali, Servizi cloud, i set di scalabilità di macchine virtuali e gli ambienti del servizio app di Azure possono comunicare tra loro in privato tramite una rete virtuale di Azure. Una rete virtuale è un isolamento logico del cloud di Azure dedicato alla [sottoscrizione](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fnetworking%2ftoc.json). È possibile implementare più reti virtuali in ogni sottoscrizione e [area](https://azure.microsoft.com/regions) di Azure. Ogni rete virtuale è isolata dalle altre. Per ogni rete virtuale è possibile:

- Specificare uno spazio indirizzi IP privato personalizzato con indirizzi pubblici e privati (RFC 1918). Azure assegna alle risorse connesse alla rete virtuale un indirizzo IP privato dello spazio indirizzi specificato.
- Segmentare la rete virtuale in una o più subnet e allocare una parte dello spazio indirizzi della rete virtuale a ogni subnet.
- Usare la risoluzione dei nomi fornita da Azure oppure specificare un server DNS personalizzato che verrà usato dalle risorse connesse a una rete virtuale.

Per altre informazioni sul servizio Rete virtuale di Azure, vedere l'articolo [Rete virtuale di Azure](../virtual-network/virtual-networks-overview.md?toc=%2fazure%2fnetworking%2ftoc.json). È possibile connettere tra loro le reti virtuali in modo che le risorse connesse a una di esse possano comunicare tra loro attraverso le reti virtuali. Per connettere tra loro le reti virtuali è possibile usare una o entrambe le opzioni seguenti.

- **Peering:** consente alle risorse connesse a diverse reti virtuali di Azure nella stessa area di Azure di comunicare tra loro. La larghezza di banda e la latenza tra le reti virtuali sono uguali a quelle che si riscontrerebbero se le risorse fossero connesse alla stessa rete virtuale. Per altre informazioni sul peering, vedere l'articolo [Peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Gateway VPN:** consente alle risorse connesse a diverse reti virtuali di Azure in aree di Azure diverse di comunicare tra loro. Il traffico tra le reti virtuali passa attraverso un gateway VPN di Azure. Larghezza di banda tra le reti virtuali dipende dalla larghezza di banda del gateway. Per altre informazioni sulla connessione di reti virtuali con un gateway VPN, vedere l'articolo [Configurare una connessione da rete virtuale a rete virtuale tra le aree](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="internet-connectivity"></a>Connettività Internet

Per impostazione predefinita, tutte le risorse di Azure connesse a una rete virtuale hanno la connettività in uscita a Internet. L'indirizzo IP privato della risorsa viene convertito (con Source Network Address Translation, SNAT) in un indirizzo IP pubblico dall'infrastruttura di Azure. Per altre informazioni sulla connettività Internet in uscita, vedere l'articolo [Informazioni sulle connessioni in uscita in Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fnetworking%2ftoc.json).

Per la comunicazione in ingresso verso le risorse di Azure da Internet o la comunicazione in uscita verso Internet senza SNAT, è necessario che a una risorsa sia assegnato un indirizzo IP pubblico. Per altre informazioni sugli indirizzi IP pubblici, vedere l'articolo [Indirizzi IP pubblici](../virtual-network/virtual-network-public-ip-address.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="on-premises-connectivity"></a>Connettività locale

È possibile accedere in modo sicuro alle risorse in una rete virtuale tramite una connessione VPN o una connessione diretta privata. Per inviare il traffico di rete tra la rete virtuale di Azure e la rete locale, è necessario creare un gateway di rete virtuale. Configurare le impostazioni del gateway per creare il tipo di connessione da usare, VPN o ExpressRoute.

È possibile connettere la rete locale a una rete virtuale usando qualsiasi combinazione delle opzioni seguenti.

**Da punto a sito (VPN su SSTP)**

La figura seguente mostra connessioni da punto a sito separate tra più computer e una rete virtuale:

![Da punto a sito](./media/networking-overview/point-to-site.png)

Questa connessione viene stabilita tra un singolo computer e una rete virtuale. Questo tipo di connessione è ideale se si sta appena iniziando a usare Azure o per gli sviluppatori, perché richiede modifiche minime o nessuna modifica alla rete esistente. Risulta utile anche quando ci si connette da una posizione remota, ad esempio da casa o durante una conferenza. Le connessioni da punto a sito sono spesso associate a una connessione da sito a sito tramite lo stesso gateway di rete virtuale. La connessione usa il protocollo SSTP per garantire la comunicazione crittografata tramite Internet tra il computer e la rete virtuale. La latenza per una VPN da punto a sito è imprevedibile, dal momento che il traffico attraversa Internet.

**Da sito a sito (tunnel VPN IPsec/IKE)**

![Da sito a sito](./media/networking-overview/site-to-site.png)

Questa connessione viene stabilita tra il dispositivo VPN locale e un gateway VPN di Azure. Questo tipo di connessione consente a qualsiasi risorsa locale autorizzata dall'utente di accedere alla rete virtuale. La connessione è una VPN IPSec/IKE che fornisce la comunicazione crittografata su Internet tra il dispositivo locale e il gateway VPN di Azure. È possibile connettere più siti locali allo stesso gateway VPN. Il dispositivo VPN locale di ogni sito deve avere un indirizzo IP pubblico esterno che non si trovi dietro un NAT. La latenza per una connessione da sito a sito è imprevedibile, perché il traffico attraversa Internet.

**ExpressRoute (connessione privata dedicata)**

![ExpressRoute](./media/networking-overview/expressroute.png)

Questo tipo di connessione viene stabilito tra la rete e Azure tramite un partner ExpressRoute. La connessione è privata. Il traffico non attraversa Internet. La latenza per una connessione ExpressRoute è prevedibile, perché il traffico non attraversa Internet. È possibile combinare ExpressRoute con una connessione da sito a sito.

Per altre informazioni su tutte le opzioni di connessione riportate sopra, vedere [Diagrammi delle topologie di connessione](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="load-balancing"></a>Bilanciamento del carico e indirizzamento del traffico

Microsoft Azure offre numerosi servizi che consentono di gestire la distribuzione del traffico e il bilanciamento del carico. È possibile usare le funzionalità seguenti insieme o separatamente:

**Bilanciamento del carico DNS**

Il servizio Gestione traffico di Azure offre il bilanciamento del carico DNS globale. Gestione traffico risponde ai client con l'indirizzo IP di un endpoint integro, in base a uno dei metodi di routing seguenti:
- **Geografico:** i client vengono indirizzati a endpoint specifici di Azure, esterni o annidati, in base alla località geografica da cui provengono le query DNS. Questo metodo consente l'uso di scenari in cui è importante conoscere l'area geografica di un client e indirizzarlo in base a tale informazione. Ne sono esempi la conformità a requisiti di sovranità dei dati, la localizzazione del contenuto e dell'esperienza utente e la misurazione del traffico da aree diverse.
- **Prestazioni:** l'indirizzo IP restituito al client è il "più vicino" al client stesso. L'endpoint più vicino non è necessariamente il più vicino in termini di distanza geografica. Questo metodo determina invece l'endpoint più vicino misurando la latenza di rete. Gestione traffico gestisce una tabella della latenza di Internet per tenere traccia del tempo di round trip tra gli intervalli di indirizzi IP e ogni data center di Azure.
- **Priorità:** il traffico viene indirizzato all'endpoint primario, vale a dire quello con priorità più alta. Se l'endpoint primario non è disponibile, Gestione traffico indirizza il traffico verso il secondo endpoint. Se l'endpoint primario e secondario non sono disponibili, il traffico viene indirizzato al terzo e così via. La disponibilità dell'endpoint si basa sullo stato configurato (abilitato o disabilitato) e sul monitoraggio degli endpoint in corso.
- **Round robin ponderato:** per ogni richiesta, Gestione traffico sceglie in modo casuale un endpoint disponibile, con una probabilità di scelta basata sul peso assegnato a tutti gli endpoint disponibili. L'uso dello stesso peso in tutti gli endpoint comporta una distribuzione uniforme del traffico. Se vengono usati pesi superiori o inferiori in specifici endpoint, questi verranno restituiti più o meno frequentemente nelle risposte DNS.

La figura seguente mostra una richiesta per un'applicazione Web indirizzata a un endpoint dell'app Web. Gli endpoint possono anche essere altri servizi di Azure, ad esempio Macchine virtuali o Servizi cloud.

![Gestione traffico](./media/networking-overview/traffic-manager.png)

Il client si connette direttamente a tale endpoint. Gestione traffico di Azure rileva gli endpoint non integri e reindirizza i client a un diverso endpoint integro. Per altre informazioni su Gestione traffico, vedere l'articolo [Panoramica di Gestione traffico](../traffic-manager/traffic-manager-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

**Bilanciamento del carico dell'applicazione**

Il servizio gateway applicazione di Azure fornisce il controller per la distribuzione di applicazioni come servizio. Il gateway applicazione offre diverse funzionalità di bilanciamento del carico di livello 7 (HTTP/HTTPS) per le applicazioni, tra cui un Web application firewall che protegge le applicazioni Web da vulnerabilità ed exploit. Il gateway applicazione consente anche di ottimizzare la produttività delle Web farm eseguendo l'offload al gateway applicazione della terminazione SSL con utilizzo elevato di CPU. 

Altre funzionalità di routing di livello 7 includono la distribuzione round robin del traffico in ingresso, l'affinità di sessione basata su cookie, il routing basato su percorso URL e la possibilità di ospitare più siti Web dietro un unico gateway applicazione. Il gateway applicazione può essere configurato come gateway con connessione Internet, come gateway solo interno o come una combinazione di queste due opzioni. È completamente gestito in Azure e offre scalabilità e disponibilità elevata, oltre a un set completo di funzionalità di registrazione e diagnostica che ne migliorano la gestibilità. Per altre informazioni sul gateway applicazione, vedere l'articolo [Panoramica del gateway applicazione](../application-gateway/application-gateway-introduction.md?toc=%2fazure%2fnetworking%2ftoc.json).

La figura seguente mostra il routing basato sul percorso dell'URL con un gateway applicazione:

![gateway applicazione](./media/networking-overview/application-gateway.png)

**Bilanciamento carico di rete**

Azure Load Balancer offre servizi di bilanciamento del carico di livello 4 a elevate prestazioni e bassa latenza per tutti i protocolli UDP e TCP. Gestisce le connessioni in ingresso e in uscita. È possibile configurare endpoint con carico bilanciato pubblici e interni e definire regole per il mapping delle connessioni in ingresso a destinazioni del pool back-end, usando opzioni di probe dell'integrità TCP e HTTP per gestire la disponibilità del servizio. Per altre informazioni su Azure Load Balancer, vedere l'articolo [Panoramica del servizio di bilanciamento del carico](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

La figura seguente mostra un'applicazione multilivello con connessione Internet che usa servizi di bilanciamento del carico sia interni che esterni:

![Bilanciamento del carico](./media/networking-overview/load-balancer.png)

## <a name="security"></a>Sicurezza

È possibile filtrare il traffico in ingresso e in uscita dalle risorse di Azure usando le opzioni seguenti:

- **Rete:** è possibile implementare gruppi di sicurezza di rete di Azure per filtrare il traffico in ingresso e in uscita dalle risorse di Azure. Ogni gruppo di sicurezza di rete contiene una o più regole in ingresso e in uscita. Ogni regola specifica di indirizzi IP di origine, gli indirizzi IP di destinazione, la porta e il protocollo in base al quale viene filtrato il traffico. I gruppi di sicurezza di rete si possono applicare a singole subnet e macchine virtuali. Per altre informazioni sui gruppi di sicurezza di rete, vedere la [panoramica dei gruppi di sicurezza di rete](../virtual-network/virtual-networks-nsg.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Applicazione:** usando un gateway applicazione con un Web application firewall è possibile proteggere le applicazioni Web da vulnerabilità ed exploit, ad esempio attacchi SQL injection, cross-site scripting e intestazioni non valide. Il gateway applicazione filtra questo tipo di traffico e gli impedisce di raggiungere i server Web. Le regole da abilitare sono configurabili. Viene offerta la possibilità di configurare i criteri di negoziazione SSL per consentire la disabilitazione di determinati criteri. Per altre informazioni in proposito, vedere l'articolo relativo al [Web application firewall](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

Per fare uso di funzionalità di rete non offerte da Azure o di applicazioni di rete usate in locale, è possibile implementare i prodotti in macchine virtuali e connetterle alla rete virtuale. [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) include varie macchine virtuali di diverso tipo preconfigurate con applicazioni di rete che attualmente potrebbero essere in uso. Le macchine virtuali preconfigurate sono dette in genere appliance virtuali di rete e sono disponibili con applicazioni quali firewall e ottimizzazione WAN.

## <a name="routing"></a>Routing

Azure crea tabelle di route predefinite che consentono alle risorse connesse a qualsiasi subnet di qualsiasi rete virtuale di comunicare tra loro. Per sostituire le route predefinite create da Azure, è possibile implementare uno o entrambi i tipi di route seguenti.
- **Route definite dall'utente:** è possibile creare tabelle di route personalizzate con route che controllano l'instradamento del traffico per ogni subnet. Per altre informazioni sulle route definite dall'utente, vedere l'articolo [Route definite dall'utente](../virtual-network/virtual-networks-udr-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Route BGP (Border Gateway Protocol):** se si connette la rete virtuale alla rete locale con un gateway VPN di Azure o una connessione ExpressRoute, è possibile propagare le route BGP alle reti virtuali. BGP è il protocollo di routing standard comunemente usato in Internet per lo scambio di informazioni di routing e raggiungibilità tra due o più reti. Quando viene usato nel contesto di reti virtuali di Azure, BGP consente ai gateway VPN di Azure e ai dispositivi VPN locali, detti peer BGP o router adiacenti, lo scambio di "route" che indicano a entrambi i gateway la disponibilità e la raggiungibilità di tali prefissi per il passaggio attraverso i gateway o i router coinvolti. BGP può anche abilitare il routing di transito tra più reti propagando a tutti gli altri peer BGP le route che un gateway BGP apprende da un peer BGP. Per altre informazioni su BGP, vedere l'articolo [Panoramica di BGP con i gateway VPN di Azure](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="manageability"></a>Gestibilità

Azure offre gli strumenti seguenti per il monitoraggio e la gestione di rete:
- **Log attività:** tutte le risorse di Azure hanno log attività che contengono informazioni sulle operazioni eseguite, lo stato delle operazioni e chi le ha avviate. Per altre informazioni sui log attività, vedere l'articolo [Panoramica del log attività di Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Log di diagnostica:** le risorse di rete creano eventi periodici e spontanei, che vengono registrati negli account di archiviazione di Azure e inviati a un hub eventi di Azure oppure ad Azure Log Analytics. I log di diagnostica contengono informazioni dettagliate sull'integrità delle singole risorse. Sono disponibili log di diagnostica per Azure Load Balancer, gruppi di sicurezza di rete, route e gateway applicazione. Per altre informazioni in proposito, vedere la [panoramica dei log di diagnostica](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Metriche:** le metriche sono costituite da contatori e misurazioni delle prestazioni raccolti in un determinato periodo di tempo sulle risorse. Le metriche possono essere usate per attivare avvisi in base a soglie. Attualmente le metriche sono disponibili nel gateway applicazione. Per altre informazioni sulle metriche, vedere l'articolo [Panoramica delle metriche in Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Risoluzione dei problemi:** le informazioni sulla risoluzione dei problemi sono accessibile direttamente nel portale di Azure. Tali informazioni permettono di diagnosticare i problemi comuni relativi a ExpressRoute, gateway VPN, gateway applicazione, log di sicurezza di rete, route, DNS, Azure Load Balancer e Gestione traffico.
- **Controllo degli accessi in base al ruolo:** il controllo degli accessi in base al ruolo permette di controllare chi può creare e gestire le risorse di rete. Per altre informazioni in proposito, vedere l'articolo [Introduzione al controllo degli accessi in base al ruolo nel portale di Azure](../active-directory/role-based-access-control-what-is.md?toc=%2fazure%2fnetworking%2ftoc.json). 
- **Acquisizione pacchetti:** il servizio Azure Network Watcher offre la possibilità di eseguire un'acquisizione pacchetti in una macchina virtuale tramite un'estensione nella macchina virtuale. Questa funzionalità è disponibile per macchine virtuali Windows e Linux. Per altre informazioni sull'acquisizione pacchetti, vedere la [panoramica dell'acquisizione pacchetti](../network-watcher/network-watcher-packet-capture-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Verifica dei flussi IP:** Network Watcher consente di verificare i flussi IP tra una macchina virtuale di Azure e una risorsa remota per determinare se i pacchetti sono consentiti o negati. Questa funzionalità offre agli amministratori la possibilità di diagnosticare rapidamente i problemi di connettività. Per altre informazioni su come verificare i flussi IP, vedere la [panoramica della verifica del flusso IP](../network-watcher/network-watcher-ip-flow-verify-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Risoluzione dei problemi di connettività VPN:** la funzionalità di risoluzione dei problemi VPN di Network Watcher offre la possibilità di eseguire query su una connessione o un gateway e verificare l'integrità delle risorse. Per altre informazioni sulla risoluzione dei problemi delle connessioni VPN, vedere la [panoramica della risoluzione dei problemi di connettività VPN](../network-watcher/network-watcher-troubleshoot-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
- **Topologia di rete:** Network Watcher permette di visualizzare una rappresentazione grafica delle risorse di rete in una rete virtuale. Per altre informazioni sulla visualizzazione della topologia di rete, vedere la [panoramica della topologia](../network-watcher/network-watcher-topology-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).

## <a name="tools"></a>Strumenti di distribuzione e configurazione

È possibile distribuire e configurare le risorse di rete di Azure con uno qualsiasi degli strumenti seguenti:

- **Portale di Azure:** interfaccia utente grafica che viene eseguita in un browser. Aprire il [portale di Azure](http://portal.azure.com).
- **Azure PowerShell:** strumenti da riga di comando per la gestione di Azure da computer Windows. Per altre informazioni su Azure PowerShell, vedere l'articolo [Panoramica di Azure PowerShell](/powershell/azure/overview?view=azurermps-3.8.0?toc=%2fazure%2fnetworking%2ftoc.json).
- **Interfaccia della riga di comando di Azure:** strumenti da riga di comando per la gestione di Azure da computer Linux, macOS o Windows. Per altre informazioni sull'interfaccia della riga di comando di Azure, vedere la [panoramica dell'interfaccia della riga di comando di Azure](/cli/azure/get-started-with-azure-cli?toc=%2fazure%2fnetworking%2ftoc.json).
- **Modelli di Azure Resource Manager:** file in formato JSON che definiscono l'infrastruttura e la configurazione delle soluzioni Azure. Usando il modello è possibile distribuire ripetutamente la soluzione nel corso del ciclo di vita garantendo al contempo che le risorse vengano distribuite in uno stato coerente. Per altre informazioni sulla creazione di modelli, vedere l'articolo [Procedure consigliate per la creazione di modelli di Azure Resource Manager](../azure-resource-manager/resource-manager-template-best-practices.md?toc=%2fazure%2fnetworking%2ftoc.json). Per distribuire i modelli è possibile usare il portale di Azure, l'interfaccia della riga di comando o PowerShell. Per iniziare da subito a usare i modelli, è possibile distribuire uno dei numerosi modelli preconfigurati disponibili nella libreria [Modelli di avvio rapido di Azure](https://azure.microsoft.com/resources/templates/?term=network). 

## <a name="pricing"></a>Prezzi

Alcuni dei servizi di rete di Azure comportano un addebito, mentre altri sono gratuiti. Per altre informazioni, vedere le pagine relative ai prezzi di [rete virtuale](https://azure.microsoft.com/pricing/details/virtual-network), [gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway), [gateway applicazione](https://azure.microsoft.com/en-us/pricing/details/application-gateway/), [bilanciamento del carico](https://azure.microsoft.com/pricing/details/load-balancer), [Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher), [DNS](https://azure.microsoft.com/pricing/details/dns), [Gestione traffico](https://azure.microsoft.com/pricing/details/traffic-manager) ed [ExpressRoute](https://azure.microsoft.com/pricing/details/expressroute).

## <a name="next-steps"></a>Passaggi successivi

- Creare la prima rete virtuale e connettervi alcune VM seguendo la procedura descritta nell'articolo [Creare la prima rete virtuale](../virtual-network/virtual-network-get-started-vnet-subnet.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Connettere il computer a una rete virtuale seguendo la procedura descritta nell'articolo [Configurare una connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Bilanciare il carico del traffico Internet verso server pubblici seguendo la procedura descritta nell'articolo [Creazione del servizio di bilanciamento del carico Internet attraverso il portale di Azure](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
