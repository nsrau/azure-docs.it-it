---
title: Informazioni sulla progettazione del gateway VPN di Azure
description: Informazioni sui modi in cui è possibile progettare una topologia del gateway VPN per la connessione alle reti virtuali di Azure.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/03/2020
ms.author: cherylmc
ms.openlocfilehash: 61732f66aef58f5a9edcb9e095782e19e8aaffdd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91397216"
---
# <a name="vpn-gateway-design"></a>Progettazione del gateway VPN

È importante tenere presente che sono disponibili configurazioni diverse per le connessioni del gateway VPN. È necessario determinare la configurazione più adatta alle proprie esigenze. Nelle sezioni seguenti è possibile visualizzare le informazioni di progettazione e i diagrammi della topologia sulle seguenti connessioni del gateway VPN. Usare i diagrammi e le descrizioni per selezionare la topologia di connessione più adatta alle esigenze. I diagrammi mostrano le topologie di base principali, ma è possibile creare configurazioni più complesse usando i diagrammi come linee guida.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a><a name="s2smulti"></a>Da sito a sito e multisito (tunnel VPN IPsec/IKE)

### <a name="site-to-site"></a><a name="S2S"></a>Da sito a sito

Una connessione gateway VPN da sito a sito (S2S) avviene tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Le connessioni S2S possono essere usate per le configurazioni cross-premise e ibride. Una connessione da sito a sito richiede la presenza in locale di un dispositivo VPN a cui è stato assegnato un indirizzo IP pubblico. Per informazioni sulla selezione di un dispositivo VPN, vedere [Domande frequenti sul gateway VPN - Dispositivi VPN](vpn-gateway-vpn-faq.md#s2s).

![Esempio di connessione gateway VPN di Azure da sito a sito](./media/design/vpngateway-site-to-site-connection-diagram.png)

Il gateway VPN può essere configurato in modalità attivo-standby usando un indirizzo IP pubblico o in modalità attivo-attivo usando due indirizzi IP pubblici. In modalità attivo-standby, un tunnel IPsec è attivo e l'altro è in standby. In questa configurazione, il traffico passa attraverso il tunnel attivo e, se si verifica un problema con questo tunnel, il traffico passa al tunnel di standby. È *consigliabile* configurare il gateway VPN in modalità attivo-attivo, in cui entrambi i tunnel IPSec sono contemporaneamente attivi, con il flusso di dati in entrambi i tunnel contemporaneamente. Un ulteriore vantaggio della modalità attivo-attivo è che i clienti riscontrano velocità effettiva più elevate.

### <a name="multi-site"></a><a name="Multi"></a>Multisito

Questo tipo di connessione è una variante della connessione da sito a sito. È possibile creare più di una connessione VPN dal gateway di rete virtuale, che in genere connette a più siti locali. Quando si usano più connessioni, è necessario usare una rete VPN di tipo RouteBased (nota come gateway dinamico nell'ambito delle reti virtuali classiche). Poiché ogni rete virtuale può avere un solo gateway VPN, tutte le connessioni che usano il gateway condividono la larghezza di banda disponibile. Questo tipo di connessione è spesso definito connessione "multisito".

![Esempio di connessione gateway VPN di Azure multisito](./media/design/vpngateway-multisite-connection-diagram.png)

### <a name="deployment-models-and-methods-for-site-to-site-and-multi-site"></a>Metodi e modelli di distribuzione per connessioni da sito a sito e multisito

[!INCLUDE [site-to-site and multi-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="point-to-site-vpn"></a><a name="P2S"></a>VPN da punto a sito

Una connessione gateway VPN da punto a sito (P2S) consente di creare una connessione sicura alla rete virtuale da un singolo computer client. Una connessione da punto a sito viene stabilita avviandola dal computer client. Questa soluzione è utile per i telelavoratori che intendono connettersi alle reti virtuali di Azure da una posizione remota, ad esempio da casa o durante una riunione. Una VPN da punto a sito è anche una soluzione utile da usare al posto di una VPN da sito a sito quando solo pochi client devono connettersi a una rete virtuale.

A differenza delle connessioni da sito a sito, le connessioni da punto a sito non necessitano di un indirizzo IP pubblico locale o di un dispositivo VPN. Le connessioni da punto a sito possono essere usate con le connessioni da sito a sito attraverso lo stesso gateway VPN, purché tutti i requisiti di configurazione per entrambe le connessioni siano compatibili. Per altre informazioni sulle connessioni da punto a sito, vedere [About Point-to-Site VPN](point-to-site-about.md) (Informazioni sulla VPN da punto a sito).

![Esempio di connessione gateway VPN di Azure da punto a sito](./media/design/point-to-site.png)

### <a name="deployment-models-and-methods-for-p2s"></a>Metodi e modelli di distribuzione per la connessione da punto a sito

[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="vnet-to-vnet-connections-ipsecike-vpn-tunnel"></a><a name="V2V"></a>Connessioni da rete virtuale a rete virtuale (tunnel VPN IPsec/IKE)

La connessione di una rete virtuale a un'altra rete virtuale (da rete virtuale a rete virtuale) è simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE. È anche possibile combinare una comunicazione tra reti virtuali con configurazioni di connessioni multisito. Questo permette di definire topologie di rete che consentono di combinare la connettività cross-premise con la connettività tra reti virtuali.

Le reti virtuali connesse possono essere:

* Nella stessa area o in aree diverse
* Nella stessa sottoscrizione o in sottoscrizioni diverse 
* Nello stesso modello di distribuzione o in modelli diversi

![Esempio di connessione gateway VPN di Azure da rete virtuale a rete virtuale](./media/design/vpngateway-vnet-to-vnet-connection-diagram.png)

### <a name="connections-between-deployment-models"></a>Connessioni tra modelli di distribuzione

Azure offre attualmente di due modelli di distribuzione: classica e Resource Manager. Se si usa Azure da qualche tempo, si dispone probabilmente di VM e istanze del ruolo di Azure in esecuzione su una rete virtuale classica. Le VM e le istanze del ruolo più recenti potrebbero invece essere in esecuzione su una rete virtuale creata in Resource Manager. Si crea una connessione tra le reti virtuali per consentire alle risorse di una rete virtuale di comunicare direttamente con le risorse di un'altra.

### <a name="vnet-peering"></a>Peering reti virtuali

È possibile usare il peering reti virtuali per creare la connessione, purché la rete virtuale soddisfi determinati requisiti. Peering reti virtuali non usa un gateway di rete virtuale. Per altre informazioni, vedere [Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnet-to-vnet"></a>Metodi e modelli di distribuzione per connessioni da rete virtuale a rete virtuale

[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="expressroute-private-connection"></a><a name="ExpressRoute"></a>ExpressRoute (connessione privata)

ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, ad esempio Microsoft Azure, Microsoft 365 e CRM online. La connettività può provenire da una rete any-to-any (VPN IP), una rete Ethernet da punto a punto o una cross-Connection virtuale tramite un provider di connettività in una struttura di condivisione percorso.

Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. In questo modo possono offrire un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.

Una connessione ExpressRoute usa un gateway di rete virtuale come parte della configurazione obbligatoria. In una connessione ExpressRoute il gateway di rete virtuale viene configurato con il tipo di gateway "ExpressRoute" invece che "Vpn". Anche se, per impostazione predefinita, il traffico che passa in un circuito ExpressRoute non è crittografato, è possibile creare una soluzione che consente di inviare traffico crittografato su un circuito ExpressRoute. Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="site-to-site-and-expressroute-coexisting-connections"></a><a name="coexisting"></a>Connessioni coesistenti da sito a sito ed ExpressRoute

ExpressRoute è una connessione privata diretta dalla rete WAN (non sulla rete Internet pubblica) a servizi Microsoft come Azure. Il traffico VPN da sito a sito viaggia crittografato sulla rete Internet pubblica. La possibilità di configurare connessioni VPN da sito a sito ed ExpressRoute per la stessa rete virtuale offre diversi vantaggi.

È possibile configurare una VPN da sito a sito come percorso di failover sicuro per ExpressRoute oppure usare VPN da sito a sito per connettersi a siti che non fanno parte della rete, ma che sono connessi tramite ExpressRoute. Si noti che questa configurazione richiede due gateway di rete virtuale per la stessa rete virtuale, uno che usa il tipo di gateway "Vpn" e l'altro che usa il tipo di gateway "ExpressRoute".

![Esempio di connessioni coesistenti ExpressRoute e gateway VPN](./media/design/expressroute-vpngateway-coexisting-connections-diagram.png)

### <a name="deployment-models-and-methods-for-s2s-and-expressroute-coexist"></a>Metodi e modelli di distribuzione per la coesistenza di connessioni da sito a sito ed ExpressRoute

[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="highly-available-connections"></a><a name="highly-available"></a>Connessioni a disponibilità elevata

Per la pianificazione e la progettazione per le connessioni a disponibilità elevata, vedere [connessioni a disponibilità elevata](vpn-gateway-highlyavailable.md).

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni, vedere [Domande frequenti sul gateway VPN](vpn-gateway-vpn-faq.md).

* Altre informazioni sulle [impostazioni di configurazione del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).

* Per le considerazioni sul protocollo BGP del gateway VPN, vedere [informazioni su BGP](vpn-gateway-bgp-overview.md).

* Vedere [Sottoscrizione e limiti del servizio](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

* Informazioni su alcune altre [funzionalità di rete](../networking/networking-overview.md) chiave di Azure.
