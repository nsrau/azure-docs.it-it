---
title: 'Azure ExpressRoute: prerequisiti'
description: Questa pagina fornisce un elenco di requisiti da soddisfare prima di potere ordinare un circuito Azure ExpressRoute. Include un elenco di controllo.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: duau
ms.openlocfilehash: 87c8a7523222afd3ae4daeb8fb5824025e693cd1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89569629"
---
# <a name="expressroute-prerequisites--checklist"></a>Prerequisiti di ExpressRoute ed elenco di controllo
Per connettersi ai servizi di Microsoft Cloud usando ExpressRoute sarà necessario verificare che siano stati soddisfatti i requisiti elencati nelle sezioni seguenti.

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

## <a name="azure-account"></a>Account Azure
* Un account Microsoft Azure valido e attivo. Questo account è necessario per configurare il circuito ExpressRoute. I circuiti ExpressRoute sono risorse all'interno di sottoscrizioni di Azure. Una sottoscrizione di Azure è un requisito, anche se la connettività è limitata a servizi cloud Microsoft non Azure, ad esempio Microsoft 365.
* Una sottoscrizione Active Microsoft 365 (se si utilizza Microsoft 365 Services). Per ulteriori informazioni, vedere la sezione requisiti specifici Microsoft 365 di questo articolo.

## <a name="connectivity-provider"></a>Provider di connettività

* È possibile collaborare con un [partner per la connettività ExpressRoute](expressroute-locations.md#partners) per connettersi a Microsoft Cloud. È possibile configurare una connessione tra la rete locale e Microsoft in [tre modi](expressroute-introduction.md)diversi.
* Se il provider non è un partner per la connettività ExpressRoute, è comunque possibile connettersi a Microsoft Cloud tramite un [provider Cloud Exchange](expressroute-locations.md#connectivity-through-exchange-providers).

## <a name="network-requirements"></a>Requisiti di rete
* **Ridondanza a ogni località di peering**: Microsoft richiede che le sessioni BGP ridondanti siano configurate tra i router Microsoft e i router di peering in ogni circuito ExpressRoute (anche quando si dispone di una sola [connessione fisica a uno scambio cloud](expressroute-faqs.md#onep2plink)).
* **Ridondanza per il ripristino di emergenza**: Microsoft consiglia di impostare almeno due circuiti ExpressRoute in posizioni di peering diverse per evitare un singolo punto di errore.
* **Routing**: a seconda di come ci si connette a Microsoft Cloud, è necessario configurare e gestire le sessioni BGP per i [domini di routing](expressroute-circuit-peerings.md) o chiedere al provider di farlo. Alcuni provider di connettività Ethernet o provider Cloud Exchange possono offrire la gestione BGP come servizio a valore aggiunto.
* **NAT**: Microsoft accetta solo indirizzi IP pubblici tramite peer Microsoft. Se si usano indirizzi IP privati nella rete locale, è necessario convertirli in indirizzi IP pubblici (o chiedere al provider di farlo) [con il processo NAT](expressroute-nat.md).
* **QoS**: Skype for Business include diversi servizi, ad esempio voce, video o testo, che richiedono una gestione QoS differenziata. È necessario seguire i [requisiti QoS](expressroute-qos.md).
* **Sicurezza di rete**: quando ci si connette a Microsoft Cloud tramite ExpressRoute, tenere conto della [sicurezza di rete](../best-practices-network-security.md) .

## <a name="microsoft-365"></a>Microsoft 365
Se si prevede di abilitare Microsoft 365 in ExpressRoute, esaminare i documenti seguenti per ulteriori informazioni sui requisiti di Microsoft 365.

* [Azure ExpressRoute per Microsoft 365](/microsoft-365/enterprise/azure-expressroute)
* [Routing con ExpressRoute per Microsoft 365](/microsoft-365/enterprise/routing-with-expressroute)
* [Disponibilità elevata e failover con ExpressRoute](https://aka.ms/erhighavailability)
* [URL e intervalli di indirizzi IP Microsoft 365](/microsoft-365/enterprise/urls-and-ip-address-ranges)
* [Pianificazione della rete e ottimizzazione delle prestazioni per Microsoft 365](/microsoft-365/enterprise/network-planning-and-performance)
* [Pianificazione della rete e della migrazione per Microsoft 365](/microsoft-365/enterprise/network-and-migration-planning)
* [Microsoft 365 l'integrazione con gli ambienti locali](/microsoft-365/enterprise/microsoft-365-integration)
* [Video di training avanzato su ExpressRoute in Office 365](https://channel9.msdn.com/series/aer/)

## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
* Individuare un partner per la connettività ad ExpressRoute. Vedere [Partner e località di peering per Azure ExpressRoute](expressroute-locations.md).
* Vedere i requisiti per [routing](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurare la connessione ExpressRoute.
  * [Creare un circuito ExpressRoute](expressroute-howto-circuit-arm.md)
  * [Configurare il routing](expressroute-howto-routing-arm.md)
  * [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)
