---
title: Informazioni sul gateway VPN | Microsoft Docs
description: Informazioni sulle connessione del gateway VPN per le reti virtuali di Azure.
services: vpn-gateway
documentationcenter: na
author: cherylmc
manager: carmonm
editor: 
tags: azure-resource-manager,azure-service-management
ms.assetid: 2358dd5a-cd76-42c3-baf3-2f35aadc64c8
ms.service: vpn-gateway
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/18/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 909320f7f898a10ff58c50d276bbe9b2b2a92b48


---
# <a name="about-vpn-gateway"></a>Informazioni sul gateway VPN
Un gateway di rete virtuale viene usato per inviare traffico di rete tra reti virtuali di Azure e percorsi locali e anche tra reti virtuali in Azure. Quando si configura un gateway VPN, è necessario creare e configurare un gateway di rete virtuale e una connessione del gateway di rete virtuale.

Nel modello di distribuzione Resource Manager, quando si crea una risorsa del gateway di rete virtuale, si specificano diverse impostazioni. Una delle impostazioni obbligatorie è '-GatewayType'. Esistono due tipi di gateway di rete virtuale: ExpressRoute e Vpn. 

Quando il traffico di rete viene inviato con una connessione privata dedicata, si usa il tipo di gateway 'ExpressRoute', detto appunto gateway ExpressRoute. Quando il traffico di rete viene inviato crittografato con una connessione pubblica, si usa il tipo di gateway 'Vpn', detto appunto gateway VPN. Le connessioni da sito a sito, da punto a sito e da rete virtuale a rete virtuale usano tutte un gateway VPN.

Ogni rete virtuale può avere un solo gateway di rete virtuale per tipo di gateway. Ad esempio, è possibile configurare un gateway di rete virtuale che usa -GatewayType ExpressRoute e uno che usa -GatewayType Vpn. Questo articolo è incentrato soprattutto sul gateway VPN. Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="pricing"></a>Prezzi
[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

## <a name="gateway-skus"></a>SKU del gateway
[!INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

Per informazioni sugli SKU del gateway VPN, vedere [SKU del gateway](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

### <a name="estimated-aggregate-throughput-by-sku"></a>Velocità effettiva aggregata stimata per SKU
[!INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="configuring-a-vpn-gateway"></a>Configurazione di un gateway VPN
Quando si configura un gateway VPN, le istruzioni da seguire dipendono dal modello di distribuzione usato per creare la rete virtuale. Ad esempio, se la rete virtuale è stata creata usando il modello di distribuzione classica, per creare e configurare le impostazioni del gateway VPN si usano le linee guida e le istruzioni per il modello di distribuzione classica. Per altre informazioni sui modelli di distribuzione, vedere [Confronto tra distribuzione di Azure Resource Manager e classica: comprensione dei modelli di implementazione e dello stato delle risorse](../resource-manager-deployment-model.md).

Una connessione gateway VPN si basa su più risorse configurate con impostazioni specifiche. La maggior parte delle risorse può essere configurata separatamente, anche se in alcuni casi è necessario configurarle seguendo un determinato ordine. È possibile iniziare a creare e configurare le risorse usando uno strumento di configurazione, ad esempio il portale di Azure, e successivamente decidere di passare a un altro strumento, ad esempio PowerShell, per configurare risorse aggiuntive o eventualmente modificare quelle esistenti. Attualmente, non è possibile configurare tutte le risorse e le relative impostazioni nel portale di Azure. Le istruzioni riportate negli articoli per ogni topologia di connessione indicano se è necessario usare uno strumento di configurazione specifico. Per informazioni sulle singole risorse e le impostazioni per il gateway VPN, vedere [Informazioni sulle impostazioni del gateway VPN](vpn-gateway-about-vpn-gateway-settings.md).

Le sezioni seguenti contengono tabelle che elencano:

* Modello di distribuzione disponibile
* Strumenti di configurazione disponibili
* Collegamenti che visualizzano direttamente un articolo, se disponibile

Usare i diagrammi e le descrizioni per selezionare la topologia di connessione più adatta alle esigenze. I diagrammi illustrano le principali topologie di base, ma è possibile creare configurazioni più complesse usando i diagrammi come riferimento.

## <a name="sitetosite-and-multisite"></a>Da sito a sito e multisito
### <a name="sitetosite"></a>Da sito a sito
Una connessione gateway VPN da sito a sito (S2S) avviene tramite un tunnel VPN IPsec/IKE (IKEv1 o IKEv2). Questo tipo di connessione richiede un dispositivo VPN che si trova in locale con un indirizzo IP pubblico assegnato e non dietro una NAT. Le connessioni S2S possono essere usate per le configurazioni cross-premise e ibride.   

![Connessione da sito a sito](./media/vpn-gateway-about-vpngateways/demos2s.png "site-to-site")

### <a name="multisite"></a>Multisito
È possibile creare e configurare una connessione gateway VPN tra la rete virtuale e più reti locali. Quando si usano più connessioni, è necessario usare una rete VPN di tipo RouteBased o un gateway dinamico per le reti virtuali classiche. Dato che una rete virtuale può avere un solo gateway VPN, tutte le connessioni che usano il gateway condividono la larghezza di banda disponibile. Questo tipo di connessione è spesso definito "multisito".

![Connessione multisito](./media/vpn-gateway-about-vpngateways/demomulti.png "multi-site")

### <a name="deployment-models-and-methods-for-sitetosite-and-multisite"></a>Metodi e modelli di distribuzione per connessioni da sito a sito e multisito
[!INCLUDE [vpn-gateway-table-site-to-site](../../includes/vpn-gateway-table-site-to-site-include.md)]

## <a name="vnettovnet"></a>Tra reti virtuali
La connessione di una rete virtuale a un'altra rete virtuale (da rete virtuale a rete virtuale) è simile alla connessione di una rete virtuale a un percorso di sito locale. Entrambi i tipi di connettività utilizzano un gateway VPN per fornire un tunnel sicuro tramite IPsec/IKE. È anche possibile combinare una comunicazione tra reti virtuali con configurazioni di connessioni multisito. Questo permette di definire topologie di rete che consentono di combinare la connettività cross-premise con la connettività tra reti virtuali.

Le reti virtuali connesse possono essere:

* Nella stessa area o in aree diverse
* Nella stessa sottoscrizione o in sottoscrizioni diverse 
* Nello stesso modello di distribuzione o in modelli diversi

![Connessione tra reti virtuali](./media/vpn-gateway-about-vpngateways/demov2v.png "vnet-to-vnet")

#### <a name="connections-between-deployment-models"></a>Connessioni tra modelli di distribuzione
Azure offre attualmente di due modelli di distribuzione: classica e Resource Manager. Se si usa Azure da qualche tempo, si dispone probabilmente di VM e istanze del ruolo di Azure in esecuzione su una rete virtuale classica. Le VM e le istanze del ruolo più recenti potrebbero invece essere in esecuzione su una rete virtuale creata in Resource Manager. Si crea una connessione tra le reti virtuali per consentire alle risorse di una rete virtuale di comunicare direttamente con le risorse di un'altra.

#### <a name="vnet-peering"></a>Peering reti virtuali
È possibile usare il peering reti virtuali per creare la connessione, purché la rete virtuale soddisfi determinati requisiti. Peering reti virtuali non usa un gateway di rete virtuale. Per altre informazioni, vedere [Peering reti virtuali](../virtual-network/virtual-network-peering-overview.md).

### <a name="deployment-models-and-methods-for-vnettovnet"></a>Metodi e modelli di distribuzione per connessioni da rete virtuale a rete virtuale
[!INCLUDE [vpn-gateway-table-vnet-to-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

## <a name="pointtosite"></a>Da punto a sito
Una connessione gateway VPN da punto a sito (P2S) consente di creare una connessione sicura alla rete virtuale da un singolo computer client. P2S è una connessione VPN tramite SSTP (Secure Sockets Tunneling Protocol). Le connessioni P2S non richiedono un dispositivo VPN o un indirizzo IP pubblico per funzionare. Per stabilire la connessione VPN, avviarla dal computer client. Questa è la soluzione ideale quando ci si vuole connettere alla rete virtuale da una posizione remota, ad esempio da casa o durante una riunione, oppure quando solo pochi client devono connettersi a una rete virtuale. Le connessioni da punto a sito possono essere usate in combinazione con le connessioni da sito a sito attraverso lo stesso gateway VPN, purché tutti i requisiti di configurazione per entrambe le connessioni siano compatibili.

![Connessione da punto a sito](./media/vpn-gateway-about-vpngateways/demop2s.png "point-to-site")

### <a name="deployment-models-and-methods-for-pointtosite"></a>Metodi e modelli di distribuzione per connessioni da punto a sito
[!INCLUDE [vpn-gateway-table-point-to-site](../../includes/vpn-gateway-table-point-to-site-include.md)]

## <a name="expressroute"></a>ExpressRoute
[!INCLUDE [expressroute-intro](../../includes/expressroute-intro-include.md)]

In una connessione ExpressRoute un gateway di rete virtuale viene configurato con il tipo di gateway 'ExpressRoute' anziché 'Vpn'. Per altre informazioni su ExpressRoute, vedere [Panoramica tecnica relativa a ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="sitetosite-and-expressroute-coexisting-connections"></a>Connessioni coesistenti da sito a sito ed ExpressRoute
ExpressRoute è una connessione dedicata diretta dalla rete WAN (non sulla rete Internet pubblica) a servizi Microsoft come Azure. Il traffico VPN da sito a sito viaggia crittografato sulla rete Internet pubblica. La possibilità di configurare connessioni VPN da sito a sito ed ExpressRoute per la stessa rete virtuale offre diversi vantaggi.

È possibile configurare una VPN da sito a sito come percorso di failover sicuro per ExpressRoute oppure usare VPN da sito a sito per connettersi a siti che non fanno parte della rete, ma che sono connessi tramite ExpressRoute. Si noti che questa configurazione richiede due gateway di rete virtuale per la stessa rete virtuale, uno che usa -GatewayType Vpn e l'altro che usa -GatewayType ExpressRoute.

![Connessioni coesistenti](./media/vpn-gateway-about-vpngateways/demoer.png "expressroute-site2site")

### <a name="deployment-models-and-methods-for-s2s-and-expressroute"></a>Metodi e modelli di distribuzione per le connessioni da sito a sito ed ExpressRoute
[!INCLUDE [vpn-gateway-table-coexist](../../includes/vpn-gateway-table-coexist-include.md)]

## <a name="next-steps"></a>Passaggi successivi
Pianificare la configurazione del gateway VPN. Vedere [Pianificazione e progettazione per il gateway VPN](vpn-gateway-plan-design.md) e [Connecting your on-premises network to Azure](../guidance/guidance-connecting-your-on-premises-network-to-azure.md) (Connessione della rete locale ad Azure).




<!--HONumber=Nov16_HO2-->


