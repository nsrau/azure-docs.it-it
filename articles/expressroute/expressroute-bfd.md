---
title: Configurare il rilevamento dell'inoltro bidirezionale su ExpressRoute | Microsoft Docs
description: Questo documento contiene istruzioni su come configurare il rilevamento dell'inoltro bidirezionale sul peering privato di un circuito ExpressRoute.
documentationcenter: na
services: expressroute
author: rambk
manager: tracsman
editor: ''
ms.assetid: ''
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 8/17/2018
ms.author: rambala
ms.openlocfilehash: 6d941bf810a45e8808f83c4df701a856f664c7ef
ms.sourcegitcommit: 0b7fc82f23f0aa105afb1c5fadb74aecf9a7015b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2018
ms.locfileid: "51631660"
---
# <a name="configure-bfd-over-expressroute"></a>Configurare il rilevamento dell'inoltro bidirezionale su ExpressRoute

ExpressRoute supporta il rilevamento dell'inoltro bidirezionale su peering privato. Abilitando questa funzionalità su ExpressRoute, è possibile accelerare il rilevamento degli errori di collegamento tra i dispositivi Microsoft Enterprise Edge (MSEE) e i router su cui termina il circuito ExpressRoute (PE). È possibile terminare ExpressRoute sui dispositivi di routing Edge di clienti o partner (se si è usato il servizio di connessione gestita di livello 3). Questo documento illustra i vantaggi offerti dal rilevamento dell'inoltro bidirezionale e mostra come abilitare questa funzionalità su ExpressRoute.

## <a name="need-for-bfd"></a>Vantaggi del rilevamento dell'inoltro bidirezionale

Il diagramma seguente illustra i vantaggi offerti dall'abilitazione del rilevamento dell'inoltro bidirezionale sul circuito ExpressRoute: [![1]][1]

È possibile abilitare un circuito ExpressRoute tramite connessioni di livello 2 o connessioni gestite di livello 3. In entrambi i casi, se è presente almeno un dispositivo di livello 2 nel percorso di connessione ExpressRoute, la responsabilità di rilevare eventuali errori di collegamento nel percorso spetta al protocollo BGP (Border Gateway Protocol) soprastante.

Nei dispositivi MSEE, per i tempi di keep-alive e attesa della sessione BGP sono in genere configurati rispettivamente 60 e 180 secondi. Pertanto, in seguito a un errore di collegamento, possono essere necessari fino a tre minuti per rilevare eventuali errori di collegamento e passare il traffico a una connessione alternativa.

È possibile controllare i timer BGP configurando tempi di keep-alive e attesa della sessione BGP inferiori nel dispositivo di peering perimetrale del cliente. Se tra i due dispositivi di peering i timer BGP non corrispondono, la sessione BGP tra i peer userà il valore di timer inferiore. Come tempo di keep-alive BGP può essere impostato un minimo di tre secondi e il tempo di attesa può essere dell'ordine di decine di secondi. Tuttavia, un'impostazione aggressiva dei timer BGP è meno preferibile perché il protocollo comporta un uso intensivo della memoria.

In questo scenario, il rilevamento dell'inoltro bidirezionale può essere di aiuto poiché consente di rilevare gli errori di collegamento con un sovraccarico ridotto in un intervallo di frazioni di secondo. 


## <a name="enabling-bfd"></a>Abilitazione del rilevamento dell'inoltro bidirezionale

Il rilevamento dell'inoltro bidirezionale è configurato per impostazione predefinita in tutte le nuove interfacce di peering privato ExpressRoute create sui dispositivi MSEE. Pertanto, per abilitare il rilevamento dell'inoltro bidirezionale, è sufficiente configurarlo nei router PE. Il processo di configurazione del rilevamento dell'inoltro bidirezionale prevede due passaggi: è necessario eseguire la configurazione nell'interfaccia e quindi creare un collegamento alla sessione BGP.

Di seguito è riportato un esempio di configurazione di router PE in cui è usato Cisco IOS XE. 

    interface TenGigabitEthernet2/0/0.150
      description private peering to Azure
      encapsulation dot1Q 15 second-dot1q 150
      ip vrf forwarding 15
      ip address 192.168.15.17 255.255.255.252
      bfd interval 300 min_rx 300 multiplier 3


    router bgp 65020
      address-family ipv4 vrf 15
        network 10.1.15.0 mask 255.255.255.128
        neighbor 192.168.15.18 remote-as 12076
        neighbor 192.168.15.18 fall-over bfd
        neighbor 192.168.15.18 activate
        neighbor 192.168.15.18 soft-reconfiguration inbound
      exit-address-family

>[!NOTE]
>Per abilitare il rilevamento dell'inoltro bidirezionale per un peering privato già esistente è necessario reimpostare il peering. Vedere [Reimpostare i peering ExpressRoute][ResetPeering].
>

## <a name="bfd-timer-negotiation"></a>Negoziazione dei timer di rilevamento dell'inoltro bidirezionale

Tra i due peer di rilevamento dell'inoltro bidirezionale, quello più lento determina la velocità di trasmissione. Gli intervalli di trasmissione/ricezione del rilevamento dell'inoltro bidirezionale su dispositivi MSEE sono impostati su 300 millisecondi. In alcuni scenari, l'intervallo può essere impostato su un valore più elevato di 750 millisecondi. Configurando valori più alti, è possibile imporre intervalli più lunghi, ma non più brevi.

>[!NOTE]
>Se si sono configurati circuiti di peering privato ExpressRoute con ridondanza geografica o si usa la connettività VPN IPSec da sito a sito come backup per il peering privato ExpressRoute, l'abilitazione del rilevamento dell'inoltro bidirezionale sul peering privato può consentire un failover più veloce nel caso si verifichi un errore di connettività di ExpressRoute. 
>

## <a name="next-steps"></a>Passaggi successivi

Per maggiori informazioni o assistenza, consultare i collegamenti seguenti:

- [Creare e modificare un circuito ExpressRoute][CreateCircuit]
- [Creare e modificare il routing per un circuito ExpressRoute][CreatePeering]

<!--Image References-->
[1]: ./media/expressroute-bfd/BFD_Need.png "Il rilevamento dell'inoltro bidirezionale riduce il tempo di individuazione degli errori di collegamento"

<!--Link References-->
[CreateCircuit]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-circuit-portal-resource-manager 
[CreatePeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-routing-portal-resource-manager
[ResetPeering]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-reset-peering






