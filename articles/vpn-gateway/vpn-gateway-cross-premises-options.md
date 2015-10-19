<properties 
   pageTitle="Informazioni sulla connettività sicura cross-premises per le reti virtuali | Microsoft Azure"
   description="Informazioni sui tipi di connessioni sicure cross-premises per reti virtuali, incluse le connessioni ExpressRoute, point-to-site e site-to-site."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/05/2015"
   ms.author="cherylmc" />

# Informazioni sulla connettività sicura cross-premises per le reti virtuali

Se si desidera connettere i siti locali in modo sicuro a una rete virtuale, sono disponibili tre opzioni: [Site-to-site](#site-to-site-connections), [Point-to-site](#point-to-site-connections), e [ExpressRoute](#expressroute-connections).

La scelta dell'opzione può dipendere da diversi fattori, ad esempio:


- Che tipo di velocità effettiva richiede la soluzione?
- Si desidera comunicare sulla rete Internet pubblica tramite VPN sicura o tramite una connessione privata?
- Si ha un indirizzo IP pubblico disponibile per l'utilizzo?
- Si prevede di utilizzare un dispositivo VPN? In tal caso, è compatibile?
- Si connette un numero limitato di computer o si desidera una connessione permanente per il sito?
- Che tipo di gateway VPN è necessario per la soluzione che si desidera creare?

La tabella seguente può aiutare nella scelta della migliore opzione di connettività per la soluzione.

| - | **Point-to-site** | **Site-to-Site** | **ExpressRoute - EXP** | **ExpressRoute - NSP** | |------------------------------|---------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------| | **Servizi supportati di Azure** | Macchine virtuali dei servizi cloud | Macchine virtuali dei servizi cloud | [Elenco servizi](../expressroute/expressroute-faqs.md#supported-azure-services) | [Elenco servizi](../expressroute/expressroute-faqs.md#supported-azure-services) | | **Larghezze di banda tipiche** | In genere < 100 Mbps aggregate | In genere < 100 Mbps aggregate | 200 Mbps, 500 Mbps, 1 Gbps e 10 Gbps | 10 Mbps, 50 Mbps, 100 Mbps, 500 Mbps, 1 Gbps | | **Protocolli supportati** | Secure Socket Tunneling Protocol (SSTP) | [IPsec](http://go.microsoft.com/fwlink/p/?LinkId=618592) | Connessione diretta su VLAN | Tecnologie VPN del NSP (MPLS, VPLS,...) | | **Routing** | Statico | Statico - sono supportati routing basati sui criteri (routing statico) e basati sul route basato (VPN con routing dinamico) | BGP | BGP | | **Resilienza delle connessioni** | attivo-passivo | attivo-passivo | attivo-attivo | attivo-attivo | | **Caso di utilizzo tipico** | Creazione di prototipi, sviluppo / test / gli scenari di laboratorio per i servizi cloud e macchine virtuali | Scenari di sviluppo / test lab e piccoli carichi di lavoro su piccola scala per i servizi cloud e le macchine virtuali | Accesso a tutti i servizi di Azure (elenco convalidato), aziendali e carichi di lavoro critici, Backup, Big Data, Azure come sito di ripristino di emergenza | Accesso a tutti i servizi di Azure (elenco convalidato), aziendali e carichi di lavoro critici, Backup, Big Data, Azure come sito di ripristino di emergenza | | **SLA** | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.microsoft.com/support/legal/sla/) | [SLA](https://azure.Microsoft.com/support/legal/sla/) | | **Prezzi** | [Prezzi](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [Prezzi](http://azure.microsoft.com/pricing/details/vpn-gateway/) | [Prezzi](http://azure.microsoft.com/pricing/details/expressroute/) | [Prezzi](http://azure.microsoft.com/pricing/details/expressroute/) | | **Documentazione tecnica** | [Documentazione del Gateway VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [Documentazione del Gateway VPN](https://azure.microsoft.com/documentation/services/vpn-gateway/) | [Documentazione di ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) | [Documentazione di ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) | | **Domande frequenti** | [Domande frequenti sul Gateway VPN](vpn-gateway-vpn-faq.md) | [Domande frequenti su Gateway VPN](vpn-gateway-vpn-faq.md) | [Domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md) | [Domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md) |
                                                                                 



## Connessioni Site-to-site

Una VPN da sito a sito consente di creare una connessione protetta tra il sito locale e la rete virtuale. Per creare una connessione da sito a sito, un dispositivo VPN che si trova nella rete locale viene configurato per creare una connessione protetta con il Gateway VPN di Azure. Una volta creata la connessione, le risorse nella rete locale e le risorse che si trovano nella rete virtuale possono comunicare direttamente e in modo sicuro. Per le connessioni da sito a sito non è necessario stabilire una connessione separata per ogni computer client nella rete locale per accedere alle risorse nella rete virtuale.

**Utilizzare una connessione site-to-site quando:**

- Si desidera creare una soluzione ibrida.
- Per creare una connessione tra la sede locale e la rete virtuale senza configurazioni lato client.
- Per creare una connessione permanente. 

**Requisiti**

- Il dispositivo VPN locale deve disporre di un indirizzo IP IPv4 a Internet. Non può essere dietro un NAT.
- È necessario il dispositivo VPN compatibile. Vedere [Informazioni sui dispositivi VPN](http://go.microsoft.com/fwlink/p/?LinkID=615099). 
- Il dispositivo VPN che si utilizza deve essere compatibile con il tipo di gateway necessario per la soluzione. Vedere [Informazioni sui gateway VPN](vpn-gateway-about-vpngateways.md).
- La SKU del gateway influirà anche sulla velocità effettiva aggregata. Per altre informazioni, vedere [SKU del gateway](vpn-gateway-about-vpngateways.md#gateway-skus). 

Se si vuole configurare una connessione gateway VPN da sito a sito, vedere[Configurare una rete virtuale con una connessione di gateway VPN da sito a sito](vpn-gateway-site-to-site-create.md).

Se si vuole configurare una connessione gateway VPN da sito a sito con RRAS, vedere [Configurare una VPN da sito a sito mediante RRAS (Routing and Remote Access Service, servizio Routing e accesso remoto) di Windows Server 2012](https://msdn.microsoft.com/library/dn636917.aspx)


## Connessioni Point-to-Site

Una VPN point-to-site consente inoltre di creare una connessione protetta alla rete virtuale. In una configurazione point-to-site, la connessione viene configurata singolarmente in ogni computer client che si desidera connettere alla rete virtuale. Le connessioni Point-to-site non richiedono un dispositivo VPN. Questo tipo di connessione utilizza un client VPN installato in ogni computer client. La VPN viene stabilita avviando manualmente la connessione dal computer client locale.

Possono esistere contemporaneamente configurazioni point-to-site e site-to-site, ma a differenza delle connessioni da sito a sito, non è possibile configurare connessioni point-to-site contemporaneamente con una connessione ExpressRoute alla stessa rete virtuale.

**Utilizzare una connessione point-to-site quando:**

- Si desidera configurare solo alcuni client per la connessione a una rete virtuale.

- Si desidera connettersi alla rete virtuale da una postazione remota. Ad esempio, la connessione da un bar o da una conferenza.

- È disponibile una connessione da sito a sito, ma alcuni client devono connettersi da una postazione remota.

- Non si ha accesso a un dispositivo VPN che soddisfi i requisiti minimi per una connessione da sito a sito.

- Il dispositivo VPN locale non dispone di un indirizzo IP IPv4 a Internet.

Se si vuole configurare una connessione point-to-site, vedere[Configurare una rete virtuale con una connessione VPN point-to-site](vpn-gateway-point-to-site-create.md).

## Connessioni ExpressRoute

ExpressRoute di Azure permette di creare connessioni private tra i data center di Azure e l'infrastruttura disponibile localmente o in un ambiente con percorso condiviso. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica e offrono un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.

In alcuni casi, l'uso di connessioni ExpressRoute per trasferire dati tra dispositivi locali e Azure può produrre vantaggi significativi in termini di costi. Con ExpressRoute è possibile stabilire connessioni ad Azure in un’ubicazione ExpressRoute (struttura Exchange Provider), oppure connettersi direttamente ad Azure dalla rete WAN esistente, ad esempio una VPN MPLS fornita da un provider di servizi di rete.

Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).


## Passaggi successivi

Vedere [Domande frequenti su ExpressRoute](../expressroute/expressroute-faqs.md) e [Domande frequenti su Gateway VPN](vpn-gateway-vpn-faq.md) per ulteriori informazioni.

<!---HONumber=Oct15_HO2-->