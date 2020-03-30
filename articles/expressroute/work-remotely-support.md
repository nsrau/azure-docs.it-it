---
title: Uso di Azure ExpressRoute per supportare gli utenti remotiUsing Azure ExpressRoute to support remote users
description: In questa pagina viene descritto come è possibile sfruttare Azure ExpressRoute per consentire di lavorare in remoto a causa della pandemia COVID-19.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336658"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Uso di Azure ExpressRoute per creare connettività ibrida per supportare gli utenti remotiUsing Azure ExpressRoute to create hybrid connectivity to support remote users

Questo articolo descrive come è possibile sfruttare ExpressRoute, Azure, la rete Microsoft e l'ecosistema dei partner di Azure per lavorare in remoto.

## <a name="connecting-to-azure-services-with-expressroute"></a>Connessione ai servizi di Azure con ExpressRouteConnecting to Azure services with ExpressRoute

>[!NOTE]
>Questo articolo descrive come è possibile sfruttare ExpressRoute, Azure, la rete Microsoft e l'ecosistema dei partner di Azure per lavorare in remoto e ridurre i problemi di rete che si trovano ad affrontare a causa di una crisi COVID-19.
>

[ExpressRoute](expressroute-introduction.md) è un servizio di Azure che consente la connettività privata tra i data center Microsoft e l'infrastruttura locale o in una struttura di colocation. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. Offrono connettività, affidabilità e velocità sicure, con latenze inferiori e coerenti rispetto alle connessioni tipiche su Internet.

## <a name="useful-links"></a>Collegamenti utili

* [Come aumentare la larghezza di banda per il circuito ExpressRoute esistenteHow to increase the bandwidth for existing ExpressRoute circuit](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [Monitoraggio, metriche e avvisi di ExpressRouteExpressRoute monitoring, metrics and alerts](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Ottimizzazione del percorso su ExpressRoute](expressroute-optimize-routing.md)
* [Azure ExpressRoute per O365](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Considerazioni sul routing asimmetrico](expressroute-asymmetric-routing.md)
* [Come aprire una richiesta di supporto nel portale di AzureHow to open a support request in the Azure portal](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Risolvere i problemi

* [Verificare la connettività ExpressRouteVerify ExpressRoute Connectivity](expressroute-troubleshooting-expressroute-overview.md)
* Ottenere la tabella ARP in Resource Manager e [ClassicGetting](expressroute-troubleshooting-arp-classic.md) ARP table in [Resource Manager](expressroute-troubleshooting-arp-resource-manager.md) and Classic
* [Reimpostare un circuito non riuscito](reset-circuit.md)
* [Risolvere i problemi relativi alle prestazioni di reteTroubleshoot Network Performance](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sui modelli di connettività ExpressRouteLearn about ExpressRoute connectivity models](expressroute-connectivity-models.md)
* Informazioni sulle connessioni e i domini di routing ExpressRoute. Vedere [Circuiti ExpressRoute e domini](expressroute-circuit-peerings.md) di routing
* Trovare un provider di servizi. Vedere [Partner ExpressRoute e posizioni di peeringSee ExpressRoute partners and peering locations](expressroute-locations.md)
* Verificare che vengano soddisfatti tutti i prerequisiti. Vedere [Prerequisiti per ExpressRoute](expressroute-prerequisites.md).
* [Creare e modificare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
* [Creare e modificare i peering per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Connettere una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
