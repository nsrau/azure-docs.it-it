---
title: Uso di Azure ExpressRoute per supportare gli utenti remoti
description: Questa pagina descrive come sfruttare Azure ExpressRoute per abilitare il funzionamento in remoto a causa della pandemia COVID-19.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: article
ms.date: 03/22/2020
ms.author: ajitbhu
ms.openlocfilehash: 2fe990fd9c5922dd2e059dbb212cd8bb1cd3726e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80336658"
---
# <a name="using-azure-expressroute-to-create-hybrid-connectivity-to-support-remote-users"></a>Uso di Azure ExpressRoute per creare una connettività ibrida per supportare gli utenti remoti

Questo articolo descrive come sfruttare ExpressRoute, Azure, Microsoft Network e l'ecosistema di partner Azure per lavorare in modalità remota.

## <a name="connecting-to-azure-services-with-expressroute"></a>Connessione ai servizi di Azure con ExpressRoute

>[!NOTE]
>Questo articolo descrive come sfruttare ExpressRoute, Azure, Microsoft Network e l'ecosistema di partner di Azure per lavorare in modalità remota e mitigare i problemi di rete a causa della crisi COVID-19.
>

[ExpressRoute](expressroute-introduction.md) è un servizio di Azure che consente la connettività privata tra i Data Center Microsoft e l'infrastruttura locale o in una struttura di condivisione del percorso. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. Garantiscono connettività, affidabilità e velocità sicure, con latenze inferiori e coerenti rispetto alle connessioni Internet tipiche.

## <a name="useful-links"></a>Collegamenti utili

* [Come aumentare la larghezza di banda per il circuito ExpressRoute esistente](expressroute-howto-circuit-portal-resource-manager.md#modify)
* [Monitoraggio, metriche e avvisi di ExpressRoute](expressroute-monitoring-metrics-alerts.md#expressroute-gateway-connections-in-bitsseconds)
* [Ottimizzazione delle route su ExpressRoute](expressroute-optimize-routing.md)
* [Azure ExpressRoute per O365](https://docs.microsoft.com/office365/enterprise/azure-expressroute?redirectSourcePath=%252farticle%252f6d2534a2-c19c-4a99-be5e-33a0cee5d3bd)
* [Considerazioni sul routing asimmetrico](expressroute-asymmetric-routing.md)
* [Come aprire una richiesta di supporto nella portale di Azure](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

## <a name="troubleshoot"></a>Risolvere problemi

* [Verificare la connettività di ExpressRoute](expressroute-troubleshooting-expressroute-overview.md)
* Recupero della tabella ARP in [Gestione risorse](expressroute-troubleshooting-arp-resource-manager.md) e [classica](expressroute-troubleshooting-arp-classic.md)
* [Reimpostare un circuito non riuscito](reset-circuit.md)
* [Risolvere i problemi relativi alle prestazioni di rete](expressroute-troubleshooting-network-performance.md)

## <a name="next-steps"></a>Passaggi successivi

* [Informazioni sui modelli di connettività ExpressRoute](expressroute-connectivity-models.md)
* Informazioni sulle connessioni e i domini di routing ExpressRoute. Vedere [circuiti e domini di routing ExpressRoute](expressroute-circuit-peerings.md)
* Trovare un provider di servizi. Vedere [partner e località di peering di ExpressRoute](expressroute-locations.md)
* Verificare che vengano soddisfatti tutti i prerequisiti. Vedere [Prerequisiti per ExpressRoute](expressroute-prerequisites.md).
* [Creare e modificare un circuito ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md)
* [Creare e modificare i peering per un circuito ExpressRoute](expressroute-howto-routing-portal-resource-manager.md)
* [Connettere una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md)
