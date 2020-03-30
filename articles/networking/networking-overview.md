---
title: Panoramica dei servizi di rete di AzureAzure networking services overview
description: 'Informazioni sui servizi di rete in Azure e sulle relative funzionalità: servizi di connettività, servizi di protezione delle applicazioni, servizi di distribuzione delle applicazioni e monitoraggio della rete.'
services: networking
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: kumud
ms.openlocfilehash: 90eddea839d2f6ae5235ac6a391b40dd667ab401
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80257836"
---
# <a name="azure-networking-services-overview"></a>Panoramica dei servizi di rete di AzureAzure networking services overview

I servizi di rete in Azure offrono un'ampia gamma di funzionalità di rete che possono essere usate insieme o separatamente. Per altre informazioni, fare clic su una qualsiasi delle funzionalità chiave seguenti:
- Servizi di [**integrazione:**](#connect)connettere le risorse di Azure e le risorse locali usando uno o una combinazione di questi servizi di rete in Azure: Rete virtuale (VNet), WAN virtuale, ExpressRoute, gateway VPN, gateway NAT di rete virtuale, DNS di Azure, servizio peering e Azure Bastion.Connectivity services : Connect Azure resources and on-premises resources using any or a combination of these networking services in Azure - Virtual Network (VNet), Virtual WAN, ExpressRoute, VPN Gateway, Virtual network NAT Gateway, Azure DNS, Peering service, and Azure Bastion.
- [**Servizi di protezione delle**](#protect) applicazioni Proteggere le applicazioni usando uno o una combinazione di questi servizi di rete in Azure: private link, protezione DDoS, firewall, gruppi di sicurezza di rete, Firewall applicazione Web ed endpoint di rete virtuale.
- [**Servizi di distribuzione delle applicazioni**](#deliver) Distribuire le applicazioni nella rete di Azure usando uno o una combinazione di questi servizi di rete in Azure: rete per la distribuzione di contenuti (CDN), servizio frontale di Azure, Gestione traffico, Gateway applicazione, Internet Analyzer e Bilanciamento del carico.
- [**Monitoraggio della rete:**](#monitor) monitorare le risorse di rete usando uno o una combinazione di questi servizi di rete in Azure: Network Watcher, ExpressRoute Monitor, Azure Monitor o VNet Terminal Access Point (TAP).

## <a name="connectivity-services"></a><a name="connect"></a>Servizi di connettività
 
Questa sezione descrive i servizi che forniscono la connettività tra le risorse di Azure, la connettività da una rete locale alle risorse di Azure e la connettività da un ramo a una succursale in Azure: Rete virtuale (VNet), WAN virtuale, ExpressRoute, Gateway VPN, Gateway NAT di rete virtuale, DNS di Azure, servizio di peering di Azure e Bastione di Azure.Virtual network NAT Gateway, Azure DNS, Azure Peering service, and Azure Bastion.

|Service|Perché usare?|Scenari|
|---|---|---|
|[Rete virtuale](#vnet)|Consente alle risorse di Azure di comunicare in modo sicuro tra loro, con Internet e con le reti locali.| <p>[Filtrare il traffico di rete](../virtual-network/tutorial-filter-network-traffic.md)</p> <p>[Indirizzare il traffico di rete](../virtual-network/tutorial-create-route-table-portal.md)</p> <p>[Limitare l'accesso di rete alle risorse](../virtual-network/tutorial-restrict-network-access-to-resources.md)</p> <p>[Connettere reti virtuali](../virtual-network/tutorial-connect-virtual-networks-portal.md)</p>|
|[ExpressRoute](#expressroute)|Estende le reti locali nel cloud Microsoft tramite una connessione privata facilitata da un provider di connettività.|<p>[Creare e modificare un circuito ExpressRoute](../expressroute/expressroute-howto-circuit-portal-resource-manager.md)</p> <p>[Creare e modificare i peering per un circuito ExpressRoute](../expressroute/expressroute-howto-routing-portal-resource-manager.md)</p> <p>[Collegare una rete virtuale a un circuito ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md)</p> <p>[Configurare e gestire i filtri di route per i circuiti ExpressRouteConfigure and manage route filters for ExpressRoute circuits](../expressroute/how-to-routefilter-portal.md)</p>|
|[VPN Gateway](#vpngateway)|Invia il traffico crittografato tra una rete virtuale di Azure e una posizione locale tramite Internet pubblico.|<p>[Connessioni da sito a sito](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)</p> <p>[Connessioni da VNet a VNet](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)</p> <p>[Connessioni da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md)</p>|
|[Rete WAN virtuale](#virtualwan)|Ottimizza e automatizza la connettività delle succursali in Azure e tramite Azure.Optimizes and automates branch connectivity to, and through, Azure. Le aree di Azure fungono da hub a cui è possibile scegliere di connettere i rami.|<p>[Connessioni da sito a sito](../virtual-wan/virtual-wan-site-to-site-portal.md), connessioni [ExpressRoute](../virtual-wan/virtual-wan-expressroute-portal.md)</p>|
|[Azure DNS](#dns)|Ospita domini DNS che forniscono la risoluzione dei nomi tramite l'infrastruttura di Microsoft Azure.|<p>[Ospitare il dominio in DNS di AzureHost your domain in Azure DNS](../dns/dns-delegate-domain-azure-dns.md)</p><p>[Creare record DNS per un'app Web](../dns/dns-web-sites-custom-domain.md)</p> <p>[Creare un record alias per Gestione trafficoCreate an alias record for Traffic Manager](../dns/tutorial-alias-tm.md)</p> <p>[Creare un record alias per l'indirizzo IP pubblico](../dns/tutorial-alias-pip.md)</p> <p>[Creare un record alias per il record di risorse di zona](../dns/tutorial-alias-rr.md)</p>|
|[Azure Bastion](#bastion)|Configurare una connettività RDP/SSH sicura e ininterrotta con le macchine virtuali direttamente nel portale di Azure su SSL. Quando ci si connette tramite Il bastione di Azure, le macchine virtuali non necessitano di un indirizzo IP pubblicoWhen you connect via Azure Bastion, your virtual machines do not need a public IP address|<p>[Creare un host del Bastione di AzureCreate an Azure Bastion host](../bastion/bastion-create-host-portal.md)</p><p>[Connettersi usando SSH a una macchina virtuale LinuxConnect using SSH to a Linux VM](../bastion/bastion-connect-vm-ssh.md)</p><p>[Connettersi tramite RDP a una macchina virtuale WindowsConnect using RDP to a Windows VM](../bastion/bastion-connect-vm-rdp.md)</p>|
|[Gateway NAT di rete virtuale](#nat)|Creare un gateway NAT per fornire la connettività in uscita per una macchina virtuale.|<p>[Creare un gateway NATCreate a NAT Gateway](../virtual-network/quickstart-create-nat-gateway-portal.md)</p>|
|[Servizio peering di Azure (anteprima)Azure Peering Service (Preview)](#azurepeeringservice)|Collabora con i provider di servizi per un routing ottimale e affidabile al cloud Microsoft tramite la rete pubblica.|<p>[Registrare il servizio peering di AzureRegister Azure Peering Service](../peering-service/azure-portal.md)</p>|
||||


### <a name="virtual-network"></a><a name="vnet"></a>Rete virtuale

Rete virtuale di Azure (VNet) è il blocco predefinito fondamentale per la rete privata in Azure. È possibile utilizzare una rete virtuale per:
- Comunicare tra le risorse di **Azure:** è possibile distribuire macchine virtuali e diversi altri tipi di risorse di Azure in una rete virtuale, ad esempio ambienti del servizio app di Azure, il servizio Azure Kubernetes (AKS) e i set di scalabilità delle macchine virtuali di Azure.Communicate between Azure resources : You can deploy VMs, and several other types of Azure resources to a virtual network, such as Azure App Service Environments, the Azure Kubernetes Service (AKS) and Azure Virtual Machine Scale Sets. Per visualizzare un elenco completo delle risorse di Azure che è possibile distribuire in una rete virtuale, vedere [Integrazione del servizio di rete virtuale](../virtual-network/virtual-network-for-azure-services.md).
- **Comunicare tra loro:** è possibile connettere tra loro reti virtuali, consentendo alle risorse in entrambe le reti virtuali di comunicare tra loro, usando il peering della rete virtuale. Le reti virtuali connesse possono essere in aree di Azure uguali o diversi. Per altre informazioni, vedere [Peering di rete virtuale](../virtual-network/virtual-network-peering-overview.md).
- **Comunicare a Internet**: Tutte le risorse in una rete virtuale possono comunicare in uscita verso Internet, per impostazione predefinita. Per la comunicazione in ingresso con una risorsa, è possibile assegnarle un indirizzo IP pubblico o un servizio di bilanciamento del carico pubblico. È inoltre possibile utilizzare [gli indirizzi IP pubblici](../virtual-network/virtual-network-public-ip-address.md) o Load [Balancer](../load-balancer/load-balancer-overview.md) pubblici per gestire le connessioni in uscita.
- **Comunicare con reti locali:** è possibile connettere i computer e le reti locali a una rete virtuale tramite [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) o [ExpressRoute](../expressroute/expressroute-introduction.md).

Per altre informazioni, vedere [Che cos'è](../virtual-network/virtual-networks-overview.md)la rete virtuale di Azure? .

### <a name="expressroute"></a><a name="expressroute"></a>ExpressRoute
ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata facilitata da un provider di connettività. La connessione è privata. Il traffico non passa da Internet. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Office 365 e Dynamics 365.  Per altre informazioni, vedere [Che cos'è ExpressRoute?](../expressroute/expressroute-introduction.md).

![Azure ExpressRoute](./media/networking-overview/expressroute-connection-overview.png)

### <a name="vpn-gateway"></a><a name="vpngateway"></a>VPN Gateway
Gateway VPN consente di creare connessioni cross-premise crittografate alla rete virtuale da percorsi locali o di creare connessioni crittografate tra reti virtuali. Sono disponibili diverse configurazioni per le connessioni gateway VPN, ad esempio da sito a sito, da punto a sito o da vNet a VNet.
Il diagramma seguente illustra più connessioni VPN da sito a sito alla stessa rete virtuale.

![Connessioni del gateway VPN di Azure da sito a sito](./media/networking-overview/vpngateway-multisite-connection-diagram.png)

Per ulteriori informazioni sui diversi tipi di connessioni VPN, vedere [Gateway VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

### <a name="virtual-wan"></a><a name="virtualwan"></a>Rete WAN virtuale
La rete WAN virtuale di Azure è un servizio di rete che offre connettività tra rami ottimizzata e automatizzata verso e tramite Azure. Le aree di Azure fungono da hub a cui è possibile scegliere di connettere i rami. È possibile sfruttare il backbone di Azure per anche connettere i rami e sfruttare i vantaggi della connettività da ramo a VNet. La rete WAN virtuale di Azure riunisce molti servizi di connettività cloud di Azure, ad esempio VPN da sito a sito, ExpressRoute, VPN utente da punto a sito in un'unica interfaccia operativa. La connettività con le reti virtuali di Azure viene stabilita tramite connessioni di rete virtuali. Per altre informazioni, vedere [Che cos'è](../virtual-wan/virtual-wan-about.md)la rete WAN virtuale di Azure? .

![Diagramma della rete WAN virtuale](./media/networking-overview/virtualwan1.png)

### <a name="azure-dns"></a><a name="dns"></a>Azure DNS
DNS di Azure è un servizio di hosting per i domini DNS che offre la risoluzione dei nomi usando l'infrastruttura di Microsoft Azure. L'hosting dei domini in Azure consente di gestire i record DNS usando gli stessi strumenti, credenziali, API e fatturazione usati per altri servizi Azure. Per altre informazioni, vedere [Che cos'è il DNS](../dns/dns-overview.md)di Azure? .

### <a name="azure-bastion"></a><a name="bastion"></a>Azure Bastion
Azure Bastion è un nuovo servizio PaaS completamente gestito dalla piattaforma di cui è possibile eseguire il provisioning all'interno della rete virtuale. Offre connettività RDP/SSH sicura e ininterrotta con le macchine virtuali direttamente nel portale di Azure tramite SSL. Quando ci si connette tramite Azure Bastion, per le macchine virtuali non è necessario un indirizzo IP pubblico. Per altre informazioni, vedere [Che cos'è Azure Bastion?](../bastion/bastion-overview.md).

![Architettura di Azure BastionAzure Bastion architecture](./media/networking-overview/architecture.png)

### <a name="virtual-network-nat-gateway"></a><a name="nat"></a>Gateway NAT di rete virtuale
Virtual Network NAT (network address translation) semplifica la connettività Internet solo in uscita per le reti virtuali. Se configurata in una subnet, tutta la connettività in uscita usa gli indirizzi IP pubblici statici specificati. La connettività in uscita è possibile senza bilanciamento del carico o indirizzi IP pubblici collegati direttamente alle macchine virtuali. Per altre informazioni, vedere [Che cos'è il gateway NAT](../virtual-network/nat-overview.md) della rete virtuale? 

![Gateway NAT di rete virtuale](./media/networking-overview/flow-map.png)

### <a name="azure-peering-service"></a><a name="azurepeeringservice"></a>Servizio peering di AzureAzure Peering Service
Il servizio di peering di Azure migliora la connettività dei clienti ai servizi cloud Microsoft, ad esempio Office 365, Dynamics 365, i servizi SaaS (Software as a Service), Azure o qualsiasi servizio Microsoft accessibile tramite Internet pubblico. Per altre informazioni, vedere [Che cos'è il servizio](../peering-service/about.md)di peering di Azure? .

## <a name="application-protection-services"></a><a name="protect"></a>Servizi di protezione delle applicazioni

Questa sezione descrive i servizi di rete in Azure che proteggono le risorse di rete - Proteggere le applicazioni usando uno o una combinazione di questi servizi di rete in Azure: Private Link, protezione DDoS, Firewall, gruppi di sicurezza di rete, WebThis section describes networking services in Azure that help protect your network resources - Protect your applications using any or a combination of these networking services in Azure - Private Link, DDoS protection, Firewall, Network Security Groups, Web Application Firewall ed endpoint di rete virtuale.

|Service|Perché usare?|Scenario|
|---|---|---|
|[Protezione DDoS](#ddosprotection) |Disponibilità elevata per le applicazioni con protezione da costi di traffico IP in eccesso|[Gestire la protezione DDoS di AzureManage Azure DDoS Protection](../virtual-network/manage-ddos-protection.md)|
|[Firewall applicazione Web](#waf)|<p>[Azure WAF con gateway applicazione](../web-application-firewall/ag/ag-overview.md) offre protezione regionale alle entità nello spazio di indirizzi pubblico e privatoAzure WAF with Application Gateway provides regional protection to entities in public and private address space</p><p>[Azure WAF con porte anteriore](../web-application-firewall/afds/afds-overview.md) offre protezione dal perimetro della rete agli endpoint pubblici.</p>|<p>[Configurare le regole di protezione del bot](../frontdoor/waf-front-door-policy-configure-bot-protection.md)</p> <p>[Configurare il codice di risposta personalizzato](../frontdoor/waf-front-door-configure-custom-response-code.md)</p> <p>[Configurare le regole di restrizione IP](../frontdoor/waf-front-door-configure-ip-restriction.md)</p> <p>[Configurare la regola di limite di frequenza](../frontdoor/waf-front-door-rate-limit-powershell.md)</p> |
|[Firewall di Azure](#firewall)|Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. È un firewall con stato completo distribuito come servizio con disponibilità elevata e scalabilità cloud senza limiti.|<p>[Distribuire un firewall di Azure in una rete virtualeDeploy an Azure Firewall in a Vnet](../firewall/tutorial-firewall-deploy-portal.md)</p> <p>[- Distribuire un firewall di Azure in una rete ibrida-Deploy an Azure Firewall in a hybrid network](../firewall/tutorial-hybrid-ps.md)</p> <p>[Filtrare il traffico in ingresso con Azure Firewall DNAT](../firewall/tutorial-firewall-dnat.md)</p>|
|[Gruppi di sicurezza di rete](#nsg)|Controllo completo granulare dei nodi finali distribuiti in VM/subnet per tutti i flussi di traffico di reteFull granular distributed end node control at VM/subnet for all network traffic flows|[Filtrare il traffico di rete con i gruppi di sicurezza di rete](../virtual-network/tutorial-filter-network-traffic.md)|
|[Endpoint del servizio di rete virtualeVirtual network service endpoints](#serviceendpoints)|Consente di limitare l'accesso di rete ad alcune risorse del servizio di Azure a una subnet di rete virtuale|[Limitare l'accesso alla rete alle risorse PaaS](../virtual-network/tutorial-restrict-network-access-to-resources-powershell.md)|
[Collegamento privato](#privatelink)|Consente di accedere ai servizi di Azure PaaS (ad esempio, Archiviazione di Azure e Database SQL) e Azure ospita i servizi di proprietà del cliente/partner ospitati da Un endpoint privato nella rete virtuale.|<p>[Creare un endpoint privatoCreate a private endpoint](../private-link/create-private-endpoint-portal.md)</p><p>[Creare un servizio Private Link](../private-link/create-private-link-service-portal.md)</p>|
|||
### <a name="ddos-protection"></a><a name="ddosprotection"></a>Protezione DDoS 
[Azure DDoS Protection](../virtual-network/manage-ddos-protection.md) fornisce contromisure contro le minacce DDoS più sofisticate. Il servizio fornisce funzionalità di attenuazione DDoS avanzate per l'applicazione e le risorse distribuite nelle reti virtuali. Inoltre, i clienti che utilizzano Azure DDoS Protection hanno accesso al supporto DDoS Rapid Response per coinvolgere gli esperti DDoS durante un attacco attivo.

![Protezione DDoS](./media/networking-overview/ddos-protection.png)

### <a name="web-application-firewall"></a><a name="waf"></a>Firewall applicazione Web

[Firewall applicazione Web di Azure](../web-application-firewall/overview.md) (WAF) offre protezione alle applicazioni Web da exploit Web comuni e vulnerabilità quali L'inserimento SQL e lo scripting tra siti. Azure WAF offre una protezione out-of-box dalle 10 principali vulnerabilità di OWASP tramite regole gestite. Inoltre, i clienti possono anche configurare regole personalizzate, ovvero regole gestite dai clienti per fornire una protezione aggiuntiva in base all'intervallo IP di origine, e richiedere attributi quali intestazioni, cookie, campi dati del modulo o parametri della stringa di query.

I clienti possono scegliere di distribuire [Azure WAF con](../application-gateway/waf-overview.md) il gateway applicazione che fornisce protezione regionale alle entità nello spazio di indirizzi pubblico e privato. I clienti possono anche scegliere di distribuire [Azure WAF con Front Door](../frontdoor/waf-overview.md) che fornisce protezione dal perimetro della rete agli endpoint pubblici.

![Web application firewall](./media/networking-overview/waf-overview.png)


### <a name="azure-firewall"></a><a name="firewall"></a>Firewall di Azure
Firewall di Azure è un servizio di sicurezza di rete gestito basato sul cloud che consente di proteggere le risorse della rete virtuale di Azure. Usando Firewall di Azure, è possibile creare, applicare e registrare i criteri di connettività di rete e delle applicazioni in modo centralizzato tra sottoscrizioni e reti virtuali. Firewall di Azure usa un indirizzo IP pubblico statico per le risorse della rete virtuale consentendo ai firewall esterni di identificare il traffico proveniente dalla rete virtuale. 

Per altre informazioni su Firewall di Azure, vedere la [documentazione](../firewall/overview.md)di Firewall di Azure.For more information about Azure Firewall, see the Azure Firewall documentation .

![Panoramica del firewall](./media/networking-overview/firewall-threat.png)

### <a name="network-security-groups"></a><a name="nsg"></a>Gruppi di sicurezza di rete
È possibile filtrare il traffico di rete da e verso le risorse di Azure in una rete virtuale di Azure con un gruppo di sicurezza di rete. Per altre informazioni, vedere [Panoramica della sicurezza](../virtual-network/security-overview.md).

### <a name="service-endpoints"></a><a name="serviceendpoints"></a>Endpoint di servizioService endpoints
Gli endpoint del servizio Rete virtuale estendono lo spazio di indirizzi privato della rete virtuale e l'identità della rete virtuale ai servizi di Azure tramite una connessione diretta. Gli endpoint consentono di associare le risorse critiche dei servizi di Azure solo alle proprie reti virtuali. Il traffico che transita dalla rete virtuale al servizio di Azure rimane sempre nella rete backbone di Microsoft Azure. Per altre informazioni, vedere [Endpoint servizio di rete virtuale](../virtual-network/virtual-network-service-endpoints-overview.md).

![Endpoint servizio di rete virtuale](./media/networking-overview/vnet-service-endpoints-overview.png)

### <a name="azure-private-link"></a><a name="privatelink"></a>Collegamento privato di Azure
[Azure Private Link](../private-link/private-link-overview.md) consente di accedere ai servizi di Azure PaaS (ad esempio, Archiviazione di Azure e Database SQL) e ai servizi di proprietà del cliente/partner ospitati dal cliente ospitato da Azure tramite un endpoint privato nella rete virtuale.
Il traffico tra la rete virtuale e il servizio viaggia nella rete backbone Microsoft. Non è più necessario esporre il servizio a Internet pubblico. È possibile creare il proprio servizio di collegamento privato nella rete virtuale e consegnarlo ai clienti.

![Panoramica dell'endpoint privato](./media/networking-overview/private-endpoint.png)


## <a name="application-delivery-services"></a><a name="deliver"></a>Servizi di distribuzione delle applicazioni

Questa sezione descrive i servizi di rete in Azure che consentono di distribuire applicazioni: Network Watcher, ExpressRoute Monitor, Azure Monitor o VNet Terminal Access Point (TAP).

|Service|Perché usare?|Scenario|
|---|---|---|
|[Rete per la distribuzione di contenuti](#cdn)|Fornisce contenuti ad alta larghezza di banda agli utenti. Le reti CDN archiviano il contenuto memorizzato nella cache nei server perimetrali in percorsi POP (POINT-of-Presence) vicini agli utenti finali, per ridurre al minimo la latenza|<p>[Aggiungere una rete CDN a un'app Web](../cdn/cdn-add-to-web-app.md)</p> <p>[- Accedere ai BLOB di archiviazione usando un dominio personalizzato della rete CDN di Azure tramite HTTPS- Access storage blobs using an Azure CDN custom domain over HTTPS](..//cdn/cdn-storage-custom-domain-https.md)</p> <p>[Aggiungere un dominio personalizzato all'endpoint della rete CDN di AzureAdd a custom domain to your Azure CDN endpoint](../cdn/cdn-map-content-to-custom-domain.md)</p> <p>[Configurare HTTPS in un dominio personalizzato della rete CDN di AzureConfigure HTTPS on an Azure CDN custom domain](../cdn/cdn-custom-ssl.md?tabs=option-1-default-enable-https-with-a-cdn-managed-certificate)</p>|
|[Servizio Frontdoor di Azure](#frontdoor)|Consente di definire, gestire e monitorare il routing globale per il traffico Web ottimizzando le prestazioni migliori e il failover globale immediato per la disponibilità elevata.|<p>[Aggiungere un dominio personalizzato al servizio Front Door di AzureAdd a custom domain to your Azure Front Door Service](../frontdoor/front-door-custom-domain.md)</p> <p>[Configurare HTTPS per un dominio personalizzato di Frontdoor](../frontdoor/front-door-custom-domain-https.md)</p><p>[Impostare i criteri firewall applicazione Web con filtro geografico](../frontdoor/front-door-tutorial-geo-filtering.md)|
|[Gestione traffico](#trafficmanager)|Distribuisce il traffico in base al DNS ai servizi nelle aree di Azure globali, fornendo al contempo disponibilità elevata e velocità di risposta|<p> [Indirizzare il traffico per una bassa latenza](../traffic-manager/tutorial-traffic-manager-improve-website-response.md)</p><p>[Indirizzare il traffico a un endpoint prioritario](../traffic-manager/traffic-manager-configure-priority-routing-method.md)</p><p> [Controllare il traffico con endpoint ponderati](../traffic-manager/tutorial-traffic-manager-weighted-endpoint-routing.md)</p><p>[Instradare il traffico in base alla posizione geografica dell'endpoint](../traffic-manager/traffic-manager-configure-geographic-routing-method.md)</p> <p> [Indirizzare il traffico in base alla subnet dell'utente](../traffic-manager/tutorial-traffic-manager-subnet-routing.md)</p>|
|[Bilanciamento del carico](#loadbalancer)|Fornisce il bilanciamento del carico regionale instradando il traffico tra le zone di disponibilità e nelle reti virtuali. Fornisce il bilanciamento del carico interno instradando il traffico tra e tra le risorse per compilare l'applicazione regionale.|<p> [Bilanciare il carico del traffico Internet verso le macchine virtuali](../load-balancer/tutorial-load-balancer-standard-manage-portal.md)</p> <p>[Bilanciamento del carico del traffico tra macchine virtuali all'interno di una rete virtualeLoad-balance traffic across VMs inside a virtual network](../load-balancer/tutorial-load-balancer-basic-internal-portal.md)<p>[Trasferire il traffico di inoltro a una porta specifica in macchine virtuali specifiche](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)</p><p> [Configurare il bilanciamento del carico e le regole in uscita](../load-balancer/configure-load-balancer-outbound-cli.md)</p>|
|[Gateway applicazione](#applicationgateway)|Il gateway applicazione di Azure è un servizio di bilanciamento del carico del traffico Web che consente di gestire il traffico verso le applicazioni Web.|<p>[Indirizzare il traffico Web con un gateway applicazione Azure](../application-gateway/quick-create-portal.md)</p><p>[Configurare un gateway applicazione con la terminazione SSL](../application-gateway/create-ssl-portal.md)</p><p>[Creare un gateway applicazione con reindirizzamento basato su percorsi URL](../application-gateway/create-url-route-portal.md) </p>|
|

### <a name="content-delivery-network"></a><a name="cdn"></a>Rete per la distribuzione di contenuti
La rete per la distribuzione di contenuti (rete CDN) offre agli sviluppatori una soluzione globale per distribuire rapidamente contenuto con esigenze di larghezza di banda elevata tramite la memorizzazione di tale contenuto nella cache in nodi fisici strategici ubicati in tutto il mondo. Per altre informazioni sulla rete CDN di Azure, vedere Rete per la distribuzione di contenuti di AzureFor more information about Azure CDN, see [Azure Content Delivery Network](../cdn/cdn-overview.md)

![Rete CDN di Azure](./media/networking-overview/cdn-overview.png)

### <a name="azure-front-door-service"></a><a name="frontdoor"></a>Servizio porta anteriore di Azure
Il servizio Frontdoor di Azure consente di definire, gestire e monitorare il routing globale del traffico Web, rendendo le prestazioni ottimali e il failover globale immediato per una disponibilità elevata. Con Frontdoor è possibile trasformare il consumer globale (multi-area) e le applicazioni aziendali in applicazioni moderne, solide, personalizzate e dalle alte prestazioni, in API, con contenuti che raggiungono un audience globale grazie ad Azure. Per altre informazioni, vedere [Porta anteriore di Azure](../frontdoor/front-door-overview.md).For more information, see Azure Front Door .


### <a name="traffic-manager"></a><a name="trafficmanager"></a>Gestione traffico

Gestione traffico di Azure è un servizio di bilanciamento del carico basato su DNS che consente di distribuire il traffico in modo ottimale ai servizi nelle aree globali di Azure, offrendo al tempo stesso disponibilità e velocità di risposta elevate. Gestione traffico offre una gamma di metodi di routing del traffico per distribuire il traffico, ad esempio priorità, ponderazione, prestazioni, geografiche, multivalore o subnet. Per ulteriori informazioni sui metodi di routing del traffico, vedere Metodi di routing di [Gestione traffico](../traffic-manager/traffic-manager-routing-methods.md).

Il diagramma seguente mostra il routing basato sulla priorità degli endpoint con Gestione traffico:The following diagram shows endpoint priority-based routing with Traffic Manager:

![Metodo di routing del traffico "Priorità" di Gestione traffico di Azure](./media/networking-overview/priority.png)

Per altre informazioni su Gestione traffico, vedere [Che cos'è Gestione traffico](../traffic-manager/traffic-manager-overview.md) di Azure?

### <a name="load-balancer"></a><a name="loadbalancer"></a>Bilanciamento del carico
Azure Load Balancer offre servizi di bilanciamento del carico di livello 4 a elevate prestazioni e bassa latenza per tutti i protocolli UDP e TCP. Gestisce le connessioni in ingresso e in uscita. È possibile configurare endpoint con carico bilanciato pubblici e interni e definire regole per il mapping delle connessioni in ingresso a destinazioni del pool back-end, usando opzioni di probe dell'integrità TCP e HTTP per gestire la disponibilità del servizio. Per altre informazioni su Azure Load Balancer, vedere l'articolo [Panoramica del servizio di bilanciamento del carico](../load-balancer/load-balancer-overview.md).

La figura seguente mostra un'applicazione multilivello con connessione Internet che usa servizi di bilanciamento del carico sia interni che esterni:

![Esempio di Azure Load BalancerAzure Load Balancer example](./media/networking-overview/load-balancer.png)


### <a name="application-gateway"></a><a name="applicationgateway"></a>Gateway applicazione
Il gateway applicazione di Azure è un servizio di bilanciamento del carico del traffico Web che consente di gestire il traffico verso le applicazioni Web. Si tratta di un Application Delivery Controller (ADC) come servizio, che offre varie funzionalità di bilanciamento del carico di livello 7 per le applicazioni. Per altre informazioni, vedere [Che cos'è il gateway applicazione](../application-gateway/overview.md)di Azure? .

Nel diagramma seguente viene illustrato il routing basato sul percorso URL con il gateway applicazione.

![Esempio di gateway applicazione](./media/networking-overview/figure1-720.png)

## <a name="network-monitoring-services"></a><a name="monitor"></a>Servizi di monitoraggio della rete
Questa sezione descrive i servizi di rete in Azure che consentono di monitorare le risorse di rete: Network Watcher, ExpressRoute Monitor, Monitoraggio di Azure e TAP della rete virtuale.

|Service|Perché usare?|Scenario|
|---|---|---|
|[Network Watcher](#networkwatcher)|Aiuta a monitorare e risolvere i problemi di connettività, aiuta a diagnosticare problemi di VPN, gruppo di sicurezza di rete e problemi di routing, acquisire pacchetti nella macchina virtuale, automatizzare l'attivazione degli strumenti di diagnostica usando Funzioni di Azure e app per la logica|<p>[Diagnosticare un problema di filtro del traffico delle macchine virtuali](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md)</p><p>[Diagnosticare un problema di routing delle macchine virtuali](../network-watcher/diagnose-vm-network-routing-problem.md)</p><p>[Monitorare le comunicazioni tra macchine virtualiMonitor communications between VMs](../network-watcher/connection-monitor.md)</p><p>[Diagnosticare problemi di comunicazione tra le reti](../network-watcher/diagnose-communication-problem-between-networks.md)</p><p>[Registrare il traffico di rete da e verso una macchina virtuale](../network-watcher/network-watcher-nsg-flow-logging-portal.md)</p>|
|[Monitoraggio di ExpressRoute](#expressroutemonitor)|Fornisce il monitoraggio in tempo reale delle prestazioni, della disponibilità e dell'utilizzo della rete, aiuta con l'individuazione automatica della topologia di rete, fornisce un isolamento degli errori più rapido, rileva problemi di rete temporanei, consente di analizzare le prestazioni cronologiche della rete caratteristiche, supporta l'abbonamento multi-abbonamento|<p>[Configurare Monitoraggio prestazioni rete per ExpressRoute](../expressroute/how-to-npm.md)</p><p>[Monitoraggio, metriche e avvisi di ExpressRoute](../expressroute/expressroute-monitoring-metrics-alerts.md)</p>|
|[Monitoraggio di Azure](#azuremonitor)|Consente di comprendere le prestazioni delle applicazioni e di identificare in modo proattivo i problemi che li interessano e le risorse da cui dipendono.|<p>[Metriche e avvisi di Gestione traffico](../traffic-manager/traffic-manager-metrics-alerts.md)</p><p>[Azure monitor diagnostics for Standard Load Balancer](../load-balancer/load-balancer-standard-diagnostics.md)</p><p>[Monitorare i log e le metriche di Firewall di Azure](../firewall/tutorial-diagnostics.md)</p><p>[Monitoraggio e registrazione di Azure Web application firewall](../frontdoor/waf-front-door-monitor.md)</p>|
|[TAP di rete virtuale](#vnettap)|Fornisce lo streaming continuo del traffico di rete delle macchine virtuali all'agente di raccolta pacchetti, consente soluzioni di gestione delle prestazioni di rete e delle applicazioni e strumenti di analisi della sicurezza|[Creare una risorsa TAP della rete virtualeCreate a VNet TAP resource](../virtual-network/tutorial-tap-virtual-network-cli.md)|
|

### <a name="network-watcher"></a><a name="networkwatcher"></a>Network Watcher
Azure Network Watcher fornisce gli strumenti per il monitoraggio, la diagnostica, la visualizzazione delle metriche e l'abilitazione o la disabilitazione dei log per le risorse in una rete virtuale di Azure. Per ulteriori informazioni, vedere [Che cos'è Network Watcher?](../network-watcher/network-watcher-monitoring-overview.md?toc=%2fazure%2fnetworking%2ftoc.json).
### <a name="expressroute-monitor"></a><a name="expressroutemonitor"></a>Monitoraggio di ExpressRoute
Per informazioni su come visualizzare le metriche del circuito ExpressRoute, i log di diagnostica e gli avvisi, vedere Monitoraggio, metriche e avvisi di ExpressRoute.To learn about how view ExpressRoute circuit metrics, diagnostic logs and alerts, see [ExpressRoute monitoring, metrics, and alerts.](../expressroute/expressroute-monitoring-metrics-alerts.md?toc=%2fazure%2fnetworking%2ftoc.json)
### <a name="azure-monitor"></a><a name="azuremonitor"></a>Monitoraggio di Azure
Monitoraggio di Azure ottimizza la disponibilità e le prestazioni delle applicazioni in uso offrendo una soluzione completa per raccogliere e analizzare la telemetria e intervenire di conseguenza dal cloud e dagli ambienti locali. È utile per ottenere informazioni sulle prestazioni delle applicazioni e identificare in modo proattivo i problemi delle applicazioni e delle risorse da cui dipendono. Per altre informazioni, vedere Panoramica di Monitoraggio di Azure.For more [information,](../azure-monitor/overview.md?toc=%2fazure%2fnetworking%2ftoc.json)see Azure Monitor Overview .
### <a name="virtual-network-tap"></a><a name="vnettap"></a>TAP di rete virtuale
Un TAP (Terminal Access Point) di rete virtuale di Azure consente di trasmettere il traffico di rete della macchina virtuale come flusso continuo a un agente di raccolta di pacchetti di rete o a uno strumento di analisi. L'agente di raccolta o lo strumento di analisi viene fornito da un partner [dell'appliance virtuale](https://azure.microsoft.com/solutions/network-appliances/) di rete. 

L'immagine seguente illustra come funziona un TAP di rete virtuale. 

![Come funziona il TAP di rete virtuale](./media/networking-overview/virtual-network-tap-architecture.png)

Per ulteriori informazioni, vedere [Che cos'è la rete virtuale TAP](../virtual-network/virtual-network-tap-overview.md).

## <a name="next-steps"></a>Passaggi successivi

- Creare la prima rete virtuale e connettervi alcune VM seguendo la procedura descritta nell'articolo [Creare la prima rete virtuale](../virtual-network/quick-create-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Connettere il computer a una rete virtuale completando la procedura descritta [nell'articolo Configurare una connessione da punto a sito](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
- Bilanciare il carico del traffico Internet verso server pubblici seguendo la procedura descritta nell'articolo [Creazione del servizio di bilanciamento del carico Internet attraverso il portale di Azure](../load-balancer/load-balancer-get-started-internet-portal.md?toc=%2fazure%2fnetworking%2ftoc.json).
 
 
   
