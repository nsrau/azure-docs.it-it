<properties 
   pageTitle="Topologie di connessione del gateway VPN | Microsoft Azure"
   description="Visualizzare topologie di connessione del gateway VPN e strumenti di configurazione e modelli di distribuzione disponibili."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/18/2016"
   ms.author="cherylmc" />

# Topologie di connessione del gateway VPN di Azure

Questo articolo illustra le topologie di connessione di base del gateway VPN. Le descrizioni e i grafici forniti permettono di selezionare la topologia di configurazione più adatta alle proprie esigenze. L'articolo descrive le principali topologie di base, ma è possibile creare topologie più complesse usando i diagrammi come riferimento.

Ogni topologia contiene una tabella che elenca il modello di distribuzione per cui la topologia è disponibile e gli strumenti di distribuzione che è possibile usare per configurarla. Contiene anche un collegamento diretto all'eventuale articolo disponibile. Le tabelle vengono aggiornate di frequente in base alla disponibilità di nuovi articoli e strumenti di distribuzione da usare.

Per altre informazioni sui gateway VPN, vedere [Informazioni sui gateway VPN](vpn-gateway-about-vpngateways.md).



## Da sito a sito e multisito

Una connessione da sito a sito avviene tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Per questo tipo di connessione è necessario un dispositivo VPN o Windows Server RRAS locale. Le connessioni da sito a sito possono essere usate per le configurazioni cross-premise e ibride.


**Diagramma da sito a sito**

![Connessione da sito a sito](./media/vpn-gateway-topology/site2site.png "Da sito a sito")

Se si usano VPN di Azure basate su route, è possibile creare e configurare più connessioni VPN da sito a sito alle reti locali. Questo tipo di configurazione è spesso definito connessione "multisito".
 

**Diagramma multisito**

![Connessione multisito](./media/vpn-gateway-topology/multisite.png "multisito")


**Metodi e modelli di distribuzione disponibili**

[AZURE.INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## Tra reti virtuali

La connessione di una rete virtuale a un'altra rete virtuale (da VNet a Vnet) è molto simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività usano un gateway VPN di Azure per fornire un tunnel sicuro tramite IPsec/IKE. Le reti virtuali a cui ci si connette possono trovarsi in aree geografiche o sottoscrizioni diverse. È anche possibile combinare una comunicazione tra reti virtuali con configurazioni multisito. Questo permette di definire topologie di rete che consentono di combinare la connettività cross-premise con la connettività tra reti virtuali.

Azure offre attualmente due modelli di distribuzione: gestione dei servizi di Azure, ovvero il modello di distribuzione classica, e Azure Resource Manager. Se si usa Azure già da tempo, le macchine virtuali di Azure e le istanze del ruolo sono probabilmente in esecuzione in una rete virtuale classica, mentre le macchine virtuali e le istanze del ruolo più recenti potrebbero essere in esecuzione in una rete virtuale creata in Azure Resource Manager. È possibile creare una connessione tra reti virtuali, anche se si trovano in modelli di distribuzione diversi.


**Diagramma tra reti virtuali**

![Connessione tra reti virtuali](./media/vpn-gateway-topology/vnet2vnet.png "tra reti virtuali")


**Metodi e modelli di distribuzione disponibili**

[AZURE.INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]



## Connessioni coesistenti da sito a sito ed ExpressRoute

ExpressRoute è una connessione dedicata diretta a servizi Microsoft come Azure dalla rete WAN, non sulla rete Internet pubblica. Il traffico VPN da sito a sito viaggia crittografato sulla rete Internet pubblica. La possibilità di configurare connessioni VPN da sito a sito ed ExpressRoute per la stessa rete virtuale offre diversi vantaggi. È possibile configurare una VPN da sito a sito come percorso di failover sicuro per ExpressRoute oppure usare VPN da sito a sito per connettersi a siti che non fanno parte della rete, ma che sono connessi tramite ExpressRoute.


**Diagramma per connessioni coesistenti**

![Connessioni coesistenti](./media/vpn-gateway-topology/expressroutes2s.png "expressroute-da sito a sito")


**Metodi e modelli di distribuzione disponibili**

[AZURE.INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]


## Da punto a sito

Una configurazione da punto a sito consente di creare una singola connessione sicura alla rete virtuale da un computer client. Una connessione VPN viene stabilita avviando la connessione dal computer client. È la soluzione ideale quando ci si vuole connettere alla rete virtuale da una posizione remota, ad esempio da casa o durante una riunione, oppure quando solo pochi client devono connettersi a una rete virtuale.

La connessione da punto a sito è una connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol). Le connessioni da punto a sito non richiedono un dispositivo VPN o un indirizzo IP pubblico per funzionare.

**Diagramma da punto a sito**

![Connessione da punto a sito](./media/vpn-gateway-topology/point2site.png "da punto a sito")

**Metodi e modelli di distribuzione disponibili**

[AZURE.INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## Passaggi successivi

Per una migliore comprensione dei gateway VPN è opportuno acquisire familiarità con quanto illustrato negli articoli [Informazioni sui gateway VPN](vpn-gateway-about-vpngateways.md) e [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md), prima di procedere con la pianificazione e progettazione della connessione.





 

<!---HONumber=AcomDC_0323_2016-->