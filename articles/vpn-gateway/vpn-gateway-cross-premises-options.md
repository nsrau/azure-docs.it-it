<properties 
   pageTitle="Informazioni sulla connettività sicura cross-premises per le reti virtuali | Microsoft Azure"
   description="Informazioni sui tipi di connessioni sicure cross-premise per reti virtuali, incluse le connessioni ExpressRoute, da punto a sito e da sito a sito."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/16/2016"
   ms.author="cherylmc" />

# Informazioni sulla connettività sicura cross-premises per le reti virtuali

Questo articolo illustra le diverse modalità di connessione del sito locale a una rete virtuale di Azure. L'articolo si applica a entrambi i modelli di distribuzione classica e di Gestione risorse. Per diagrammi di connessione del gateway VPN, vedere [Topologie di connessione del gateway VPN di Azure](vpn-gateway-topology.md).

Sono disponibili tre opzioni di connessione: da sito a sito, da punto a sito ed ExpressRoute. La scelta dell'opzione può dipendere da diversi fattori, ad esempio:


- Che tipo di velocità effettiva richiede la soluzione?
- Si desidera comunicare sulla rete Internet pubblica tramite VPN sicura o tramite una connessione privata?
- Si ha un indirizzo IP pubblico disponibile per l'utilizzo?
- Si prevede di utilizzare un dispositivo VPN? In tal caso, è compatibile?
- Si connette un numero limitato di computer o si desidera una connessione permanente per il sito?
- Che tipo di gateway VPN è necessario per la soluzione che si desidera creare?

La tabella seguente può aiutare nella scelta della migliore opzione di connettività per la soluzione.

[AZURE.INCLUDE [vpn-gateway-cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

## Connessioni Site-to-site

Una VPN da sito a sito consente di creare una connessione sicura tra il sito locale e la rete virtuale. Per creare una connessione da sito a sito, un dispositivo VPN che si trova nella rete locale viene configurato per creare una connessione protetta con il Gateway VPN di Azure. Una volta creata la connessione, le risorse nella rete locale e le risorse che si trovano nella rete virtuale possono comunicare direttamente e in modo sicuro. Per le connessioni da sito a sito non è necessario stabilire una connessione separata per ogni computer client nella rete locale per accedere alle risorse nella rete virtuale.

**Usare una connessione da sito a sito quando:**

- Si desidera creare una soluzione ibrida.
- Per creare una connessione tra la sede locale e la rete virtuale senza configurazioni lato client.
- Per creare una connessione permanente. 

**Requisiti**

- Il dispositivo VPN locale deve disporre di un indirizzo IP IPv4 a Internet. Non può essere dietro un NAT.
- È necessario il dispositivo VPN compatibile. Vedere [Informazioni sui dispositivi VPN](vpn-gateway-about-vpn-devices.md). 
- Il dispositivo VPN che si utilizza deve essere compatibile con il tipo di gateway necessario per la soluzione. Vedere [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md).
- La SKU del gateway influirà anche sulla velocità effettiva aggregata. Per altre informazioni, vedere [SKU del gateway](vpn-gateway-about-vpngateways.md#gwsku). 

**Metodi e modelli di distribuzione disponibili per la connessione da sito a sito**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]


## Connessioni Point-to-Site

Una VPN da punto a sito consente anche di creare una connessione protetta alla rete virtuale. In una configurazione da punto a sito, la connessione viene configurata singolarmente in ogni computer client che si vuole connettere alla rete virtuale. Le connessioni da punto a sito non richiedono un dispositivo VPN. Questo tipo di connessione utilizza un client VPN installato in ogni computer client. La VPN viene stabilita avviando manualmente la connessione dal computer client locale.

Possono esistere contemporaneamente configurazioni da punto a sito e da sito a sito, ma a differenza delle connessioni da sito a sito, non è possibile configurare connessioni da punto a sito contemporaneamente con una connessione ExpressRoute alla stessa rete virtuale.

**Usare una connessione da punto a sito quando:**

- Si desidera configurare solo alcuni client per la connessione a una rete virtuale.

- Si desidera connettersi alla rete virtuale da una postazione remota. Ad esempio, la connessione da un bar o da una conferenza.

- È disponibile una connessione da sito a sito, ma alcuni client devono connettersi da una postazione remota.

- Non si ha accesso a un dispositivo VPN che soddisfi i requisiti minimi per una connessione da sito a sito.

- Il dispositivo VPN locale non dispone di un indirizzo IP IPv4 a Internet.

**Metodi e modelli di distribuzione disponibili per la connessione da punto a sito**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## Connessioni ExpressRoute

ExpressRoute di Azure permette di creare connessioni private tra i data center di Azure e l'infrastruttura disponibile localmente o in un ambiente con percorso condiviso. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica e offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.

In alcuni casi, l'uso di connessioni ExpressRoute per trasferire dati tra dispositivi locali e Azure può produrre vantaggi significativi in termini di costi. Con ExpressRoute è possibile stabilire connessioni ad Azure in un’ubicazione ExpressRoute (struttura Exchange Provider), oppure connettersi direttamente ad Azure dalla rete WAN esistente, ad esempio una VPN MPLS fornita da un provider di servizi di rete.

Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).


## Passaggi successivi

- Per altre informazioni sul gateway VPN, vedere gli articoli [Informazioni sul gateway VPN](vpn-gateway-about-vpngateways.md), [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md) e [Pianificazione e progettazione per il gateway VPN](vpn-gateway-plan-design.md).

- Per altre informazioni su ExpressRoute, vedere gli articoli [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md), [Domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md) e [Flussi di lavoro ExpressRoute](../expressroute/expressroute-workflows.md).

<!---HONumber=AcomDC_0518_2016-->