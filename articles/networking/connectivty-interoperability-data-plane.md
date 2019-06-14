---
title: 'Interoperabilità nelle funzionalità di connettività back-end di Azure: Analisi del piano dati | Microsoft Docs'
description: Questo articolo illustra l'analisi del piano dati dell'installazione test che è possibile usare per analizzare l'interoperabilità tra ExpressRoute, una VPN da sito a sito e il peering reti virtuali in Azure.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: f4d94536a8c1b509e0ce435a764e69984b5d415e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60425543"
---
# <a name="interoperability-in-azure-back-end-connectivity-features-data-plane-analysis"></a>Interoperabilità nelle funzionalità di connettività back-end di Azure: Analisi del piano dati

Questo articolo illustra l'analisi del piano dati dell'[installazione test][Setup]. È anche possibile esaminare la [configurazione dell'installazione test][Configuration] e l'[analisi del piano di controllo][Control-Analysis] dell'installazione test.

L'analisi del piano dati esamina il percorso seguito dai pacchetti che passano da una rete locale (LAN o rete virtuale) a un'altra all'interno di una topologia. Il percorso dei dati tra due reti locali non è necessariamente simmetrico. In questo articolo viene pertanto analizzato un percorso di inoltro da una rete locale a un'altra distinta dal percorso inverso.

## <a name="data-path-from-the-hub-vnet"></a>Percorso dei dati dalla rete virtuale dell'hub

### <a name="path-to-the-spoke-vnet"></a>Percorso della rete virtuale spoke

Il peering reti virtuali emula la funzionalità di bridge di rete tra le due reti virtuali con peering. Di seguito è indicato l'output traceroute da una rete virtuale dell'hub a una macchina virtuale nella rete virtuale spoke:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

L'immagine seguente è la rappresentazione grafica della connessione tra la rete virtuale dell'hub e la rete virtuale spoke dal punto di vista di Azure Network Watcher:


[![1]][1]

### <a name="path-to-the-branch-vnet"></a>Percorso della rete virtuale del ramo

Di seguito è indicato l'output traceroute da una rete virtuale dell'hub a una macchina virtuale nella rete virtuale del ramo:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

In questo traceroute il primo hop è il gateway VPN in Gateway VPN di Azure della rete virtuale dell'hub. Il secondo hop è il gateway VPN della rete virtuale del ramo. L'indirizzo IP del gateway VPN della rete virtuale del ramo non è annunciato nella rete virtuale dell'hub. Il terzo hop è la macchina virtuale nella rete virtuale del ramo.

L'immagine seguente è la rappresentazione grafica della connessione tra la rete virtuale dell'hub e la rete virtuale del ramo dal punto di vista di Network Watcher:

[![2]][2]

Per la stessa connessione, la figura seguente mostra la visualizzazione griglia in Network Watcher:

[![3]][3]

### <a name="path-to-on-premises-location-1"></a>Percorso della rete locale Location 1

Di seguito è indicato l'output traceroute da una rete virtuale dell'hub a una macchina virtuale nella rete locale Location 1:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

In questo traceroute il primo hop è l'endpoint del tunnel del gateway Azure ExpressRoute a Microsoft Enterprise Edge Router (MSEE). Il secondo e il terzo hop sono gli IP del router perimetrale del cliente e della rete LAN locale Location 1. Questi indirizzi IP non sono annunciati nella rete virtuale dell'hub. Il quarto hop è la macchina virtuale nella rete locale Location 1.


### <a name="path-to-on-premises-location-2"></a>Percorso della rete locale Location 2

Di seguito è indicato l'output traceroute da una rete virtuale dell'hub a una macchina virtuale nella rete locale Location 2:

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

In questo traceroute il primo hop è l'endpoint del tunnel del gateway ExpressRoute a MSEE. Il secondo e il terzo hop sono gli IP del router perimetrale del cliente e della rete LAN locale Location 2. Questi indirizzi IP non sono annunciati nella rete virtuale dell'hub. Il quarto hop è la macchina virtuale nella rete locale Location 2.

### <a name="path-to-the-remote-vnet"></a>Percorso della rete virtuale remota

Di seguito è indicato l'output traceroute da una rete virtuale dell'hub a una macchina virtuale nella rete virtuale remota:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

In questo traceroute il primo hop è l'endpoint del tunnel del gateway ExpressRoute a MSEE. Il secondo hop è l'indirizzo IP del gateway della rete virtuale remota. L'intervallo IP del secondo hop non è annunciato all'interno della rete virtuale dell'hub. Il terzo hop è la macchina virtuale nella rete virtuale remota.

## <a name="data-path-from-the-spoke-vnet"></a>Percorso dei dati dalla rete virtuale spoke

La rete virtuale spoke condivide la visualizzazione di rete della rete virtuale dell'hub. Con il peering reti virtuali, la rete virtuale spoke usa la connettività dei gateway remoti della rete virtuale dell'hub come se questi fossero connessi direttamente alla rete virtuale spoke.

### <a name="path-to-the-hub-vnet"></a>Percorso della rete virtuale dell'hub

Di seguito è indicato l'output traceroute dalla rete virtuale spoke a una macchina virtuale nella rete virtuale dell'hub:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet"></a>Percorso della rete virtuale del ramo

Di seguito è indicato l'output traceroute dalla rete virtuale spoke a una macchina virtuale nella rete virtuale del ramo:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

In questo traceroute il primo hop è il gateway VPN della rete virtuale dell'hub. Il secondo hop è il gateway VPN della rete virtuale del ramo. L'indirizzo IP del gateway VPN della rete virtuale del ramo non è annunciato nella rete virtuale dell'hub/spoke. Il terzo hop è la macchina virtuale nella rete virtuale del ramo.

### <a name="path-to-on-premises-location-1"></a>Percorso della rete locale Location 1

Di seguito è indicato l'output traceroute dalla rete virtuale spoke a una macchina virtuale nella rete locale Location 1:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

In questo traceroute il primo hop è l'endpoint del tunnel del gateway ExpressRoute della rete virtuale dell'hub a MSEE. Il secondo e il terzo hop sono gli IP del router perimetrale del cliente e della rete LAN locale Location 1. Questi indirizzi IP non sono annunciati nella rete virtuale dell'hub/spoke. Il quarto hop è la macchina virtuale nella rete locale Location 1.

### <a name="path-to-on-premises-location-2"></a>Percorso della rete locale Location 2

Di seguito è indicato l'output traceroute dalla rete virtuale spoke a una macchina virtuale nella rete locale Location 2:


    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

In questo traceroute il primo hop è l'endpoint del tunnel del gateway ExpressRoute della rete virtuale dell'hub a MSEE. Il secondo e il terzo hop sono gli IP del router perimetrale del cliente e della rete LAN locale Location 2. Questi indirizzi IP non sono annunciati nelle reti virtuali dell'hub/spoke. Il quarto hop è la macchina virtuale nella rete locale Location 2.

### <a name="path-to-the-remote-vnet"></a>Percorso della rete virtuale remota

Di seguito è indicato l'output traceroute dalla rete virtuale spoke a una macchina virtuale nella rete virtuale remota:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

In questo traceroute il primo hop è l'endpoint del tunnel del gateway ExpressRoute della rete virtuale dell'hub a MSEE. Il secondo hop è l'indirizzo IP del gateway della rete virtuale remota. L'intervallo IP del secondo hop non è annunciato all'interno della rete virtuale dell'hub/spoke. Il terzo hop è la macchina virtuale nella rete virtuale remota.

## <a name="data-path-from-the-branch-vnet"></a>Percorso dei dati dalla rete virtuale del ramo

### <a name="path-to-the-hub-vnet"></a>Percorso della rete virtuale dell'hub

Di seguito è indicato l'output traceroute dalla rete virtuale del ramo a una macchina virtuale nella rete virtuale dell'hub:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

In questo traceroute il primo hop è il gateway VPN della rete virtuale del ramo. Il secondo hop è il gateway VPN della rete virtuale dell'hub. L'indirizzo IP del gateway VPN della rete virtuale dell'hub non è annunciato nella rete virtuale remota. Il terzo hop è la macchina virtuale nella rete virtuale dell'hub.

### <a name="path-to-the-spoke-vnet"></a>Percorso della rete virtuale spoke

Di seguito è indicato l'output traceroute dalla rete virtuale del ramo a una macchina virtuale nella rete virtuale spoke:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

In questo traceroute il primo hop è il gateway VPN della rete virtuale del ramo. Il secondo hop è il gateway VPN della rete virtuale dell'hub. L'indirizzo IP del gateway VPN della rete virtuale dell'hub non è annunciato nella rete virtuale remota. Il terzo hop è la macchina virtuale nella rete virtuale spoke.

### <a name="path-to-on-premises-location-1"></a>Percorso della rete locale Location 1

Di seguito è indicato l'output traceroute dalla rete virtuale del ramo a una macchina virtuale nella rete locale Location 1:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

In questo traceroute il primo hop è il gateway VPN della rete virtuale del ramo. Il secondo hop è il gateway VPN della rete virtuale dell'hub. L'indirizzo IP del gateway VPN della rete virtuale dell'hub non è annunciato nella rete virtuale remota. Il terzo hop è il punto di terminazione del tunnel VPN sul router CE primario. Il quarto hop è un indirizzo IP interno della rete locale Location 1. Questo indirizzo IP della rete LAN non è annunciato all'esterno del router perimetrale del cliente. Il quinto hop è la macchina virtuale di destinazione nella rete locale Location 1.

### <a name="path-to-on-premises-location-2-and-the-remote-vnet"></a>Percorso della rete locale Location 2 e alla rete virtuale remota

Come descritto nell'analisi del piano di controllo, la rete virtuale del ramo non ha visibilità sulla rete locale Location 2 o sulla rete virtuale remota in base alla configurazione di rete. I risultati del ping seguenti ne sono una conferma: 

    C:\Users\rb>ping 10.1.31.10

    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

    C:\Users\rb>ping 10.17.30.4

    Pinging 10.17.30.4 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.17.30.4:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

## <a name="data-path-from-on-premises-location-1"></a>Percorso dei dati dalla rete locale Location 1

### <a name="path-to-the-hub-vnet"></a>Percorso della rete virtuale dell'hub

Di seguito è indicato l'output traceroute dalla rete locale Location 1 a una macchina virtuale nella rete virtuale dell'hub:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

In questo traceroute i primi due hop fanno parte della rete locale. Il terzo hop è l'interfaccia MSEE primaria per il router perimetrale del cliente. Il quarto hop è il gateway ExpressRoute della rete virtuale dell'hub. L'intervallo IP del gateway ExpressRoute della rete virtuale dell'hub non è annunciato nella rete locale. Il quinto hop è la macchina virtuale di destinazione.

Network Watcher offre solo una visualizzazione basata su Azure. Per una prospettiva locale usare Monitoraggio prestazioni rete di Azure. Monitoraggio prestazioni rete offre agenti che è possibile installare nei server nelle reti all'esterno di Azure per analisi del percorso dati.

L'immagine seguente è la visualizzazione della topologia della connettività dalla macchina virtuale della rete locale Location 1 alla macchina virtuale nella rete virtuale dell'hub via ExpressRoute:

[![4]][4]

Come indicato in precedenza, l'installazione test usa una VPN da sito a sito come connettività di backup per ExpressRoute tra la rete locale Location 1 e la rete virtuale dell'hub. Per testare il percorso dati di backup, indurre un errore di collegamento di ExpressRoute tra il router perimetrale del cliente primario della rete locale Location 1 e il router MSEE corrispondente, arrestando l'interfaccia perimetrale del cliente rivolta verso il router MSEE:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

L'immagine seguente è la visualizzazione della topologia della connettività dalla macchina virtuale della rete locale Location 1 alla macchina virtuale nella rete virtuale dell'hub attraverso la connettività VPN da sito a sito quando la connettività ExpressRoute non è attiva:

[![5]][5]

### <a name="path-to-the-spoke-vnet"></a>Percorso della rete virtuale spoke

Di seguito è indicato l'output traceroute dalla rete locale Location 1 a una macchina virtuale nella rete virtuale spoke:

Si prenda di nuovo in considerazione la connettività ExpressRoute primaria per eseguire l'analisi del percorso dati verso la rete virtuale spoke:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

La connettività ExpressRoute 1 primaria verrà usata per il resto dell'analisi del percorso dati.

### <a name="path-to-the-branch-vnet"></a>Percorso della rete virtuale del ramo

Di seguito è indicato l'output traceroute dalla rete locale Location 1 a una macchina virtuale nella rete virtuale del ramo:

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

### <a name="path-to-on-premises-location-2"></a>Percorso della rete locale Location 2

Come descritto nell'[analisi del piano di controllo][Control-Analysis], la rete locale Location 1 non ha visibilità sulla rete locale Location 2 in base alla configurazione di rete. I risultati del ping seguenti ne sono una conferma: 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

### <a name="path-to-the-remote-vnet"></a>Percorso della rete virtuale remota

Di seguito è indicato l'output traceroute dalla rete locale Location 1 a una macchina virtuale nella rete virtuale remota:

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

## <a name="data-path-from-on-premises-location-2"></a>Percorso dei dati dalla rete locale Location 2

### <a name="path-to-the-hub-vnet"></a>Percorso della rete virtuale dell'hub

Di seguito è indicato l'output traceroute dalla rete locale Location 2 a una macchina virtuale nella rete virtuale dell'hub:

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Percorso della rete virtuale spoke

Di seguito è indicato l'output traceroute dalla rete locale Location 2 a una macchina virtuale nella rete virtuale spoke:

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-on-premises-location-1-and-the-remote-vnet"></a>Percorso della rete virtuale del ramo, della rete locale Location 1 e della rete virtuale remota

Come descritto nell'[analisi del piano di controllo][Control-Analysis], la rete locale Location 1 non ha visibilità sulla rete virtuale del ramo, sulla rete locale Location 1 o sulla rete virtuale remota in base alla configurazione di rete. 

## <a name="data-path-from-the-remote-vnet"></a>Percorso dei dati dalla rete virtuale remota

### <a name="path-to-the-hub-vnet"></a>Percorso della rete virtuale dell'hub

Di seguito è indicato l'output traceroute dalla rete virtuale remota a una macchina virtuale nella rete virtuale dell'hub:

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

### <a name="path-to-the-spoke-vnet"></a>Percorso della rete virtuale spoke

Di seguito è indicato l'output traceroute dalla rete virtuale remota a una macchina virtuale nella rete virtuale spoke:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-the-branch-vnet-and-on-premises-location-2"></a>Percorso della rete virtuale del ramo e della rete locale Location 2

Come descritto nell'[analisi del piano di controllo][Control-Analysis], la rete virtuale remota non ha visibilità sulla rete virtuale del ramo o sulla rete locale Location 2 in base alla configurazione di rete. 

### <a name="path-to-on-premises-location-1"></a>Percorso della rete locale Location 1

Di seguito è indicato l'output traceroute dalla rete virtuale remota a una macchina virtuale nella rete locale Location 1:

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>ExpressRoute e connettività VPN da sito a sito in parallelo

###  <a name="site-to-site-vpn-over-expressroute"></a>VPN da sito a sito con ExpressRoute

È possibile configurare una VPN da sito a sito con il peering Microsoft ExpressRoute per scambiare privatamente i dati tra la rete locale e le reti virtuali di Azure. Con questa configurazione, i dati possono essere scambiati garantendone riservatezza, autenticità e integrità. Lo scambio di dati è anche impossibile da riprodurre. Per altre informazioni su come configurare una VPN IPSec da sito a sito in modalità tunnel con il peering Microsoft ExpressRoute, vedere [Configurare una VPN da sito a sito tramite peering ExpressRoute Microsoft][S2S-Over-ExR]. 

La limitazione principale per la configurazione di una VPN da sito a sito che usa il peering Microsoft è rappresentata dalla velocità effettiva. La velocità effettiva nel tunnel IPSec è limitata dalla capacità del gateway VPN. La velocità effettiva del gateway VPN è inferiore alla velocità effettiva di ExpressRoute. In questo scenario, usando il tunnel IPSec per il traffico a sicurezza elevata e il peering privato per tutti gli altri tipi di traffico è possibile ottimizzare l'uso della larghezza di banda di ExpressRoute.

### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN da sito a sito come percorso di failover sicuro per ExpressRoute

ExpressRoute funge da coppia di circuiti ridondanti per garantire la disponibilità elevata. È possibile configurare la connettività ExpressRoute con ridondanza geografica in diverse aree di Azure. Come dimostrato nell'installazione test, in un'area di Azure è anche possibile usare una VPN da sito a sito per creare un percorso di failover per la connettività di ExpressRoute. Quando gli stessi prefissi vengono annunciati sia in ExpressRoute che in una VPN da sito a sito, Azure dà la priorità a ExpressRoute. Per evitare il routing asimmetrico tra ExpressRoute e la VPN da sito a sito, la configurazione di rete locale deve fare lo stesso, preferendo la connettività di ExpressRoute rispetto alla connettività VPN da sito a sito.

Per altre informazioni su come configurare connessioni coesistenti per ExpressRoute e VPN da sito a sito, vedere [Configurare connessioni coesistenti da sito a sito ed ExpressRoute usando PowerShell][ExR-S2S-CoEx].

## <a name="extend-back-end-connectivity-to-spoke-vnets-and-branch-locations"></a>Estendere la connettività back-end a reti virtuali spoke e del ramo

### <a name="spoke-vnet-connectivity-by-using-vnet-peering"></a>Connettività della rete virtuale spoke tramite il peering reti virtuali

L'architettura di rete virtuale dell'hub e spoke è molto diffusa. L'hub è una rete virtuale in Azure che funge da punto centrale di connettività tra le reti virtuali spoke e la rete locale. Gli spoke sono reti virtuali che eseguono il peering con l'hub e che è possibile usare per isolare i carichi di lavoro. Il traffico scorre tra il data center locale e l'hub attraverso una connessione VPN o ExpressRoute. Per altre informazioni sull'architettura, vedere [Implementare una topologia di rete hub-spoke in Azure][Hub-n-Spoke].

Nel peering reti virtuali all'interno di un'area, le reti virtuali spoke possono usare i gateway di rete virtuale dell'hub (gateway ExpressRoute e VPN) per comunicare con le reti remote.

### <a name="branch-vnet-connectivity-by-using-site-to-site-vpn"></a>Connettività delle reti virtuali del ramo mediante VPN da sito a sito

È possibile far comunicare tra loro le reti virtuali del ramo in aree diverse e le reti virtuali tramite una rete virtuale dell'hub. La soluzione di Azure nativa per questa configurazione è la connettività VPN da sito a sito tramite una rete VPN. In alternativa, è possibile usare un'appliance virtuale di rete per il routing nell'hub.

Per altre informazioni, vedere [Che cos'è un Gateway VPN?][VPN] e [Distribuire appliance virtuali di rete con disponibilità elevata][Deploy-NVA].


## <a name="next-steps"></a>Passaggi successivi

Vedere le [domande frequenti su ExpressRoute][ExR-FAQ] per:
-   Informazioni sul numero di circuiti ExpressRoute che è possibile connettere a un gateway ExpressRoute.
-   Informazioni sul numero di gateway ExpressRoute che è possibile connettere a un circuito ExpressRoute.
-   Informazioni su altri limiti di scalabilità di ExpressRoute.


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Visualizzazione di Network Watcher della connettività da una rete virtuale dell'hub a una rete virtuale spoke"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Visualizzazione di Network Watcher della connettività da una rete virtuale dell'hub a una rete virtuale del ramo"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Visualizzazione griglia di Network Watcher della connettività da una rete virtuale dell'hub a una rete virtuale del ramo"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Visualizzazione di Monitoraggio prestazioni rete della connettività dalla macchina virtuale della rete Location 1 alla rete virtuale dell'hub tramite ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Visualizzazione di Monitoraggio prestazioni rete della connettività dalla macchina virtuale della rete Location 1 alla rete virtuale dell'hub tramite VPN da sito a sito"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[ExpressRoute]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[VPN]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways
[VNet]: https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-configuration
[Control-Analysis]:https://docs.microsoft.com/azure/networking/connectivty-interoperability-control-plane
[Data-Analysis]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-data-plane
[ExR-FAQ]: https://docs.microsoft.com/azure/expressroute/expressroute-faqs
[S2S-Over-ExR]: https://docs.microsoft.com/azure/expressroute/site-to-site-vpn-over-microsoft-peering
[ExR-S2S-CoEx]: https://docs.microsoft.com/azure/expressroute/expressroute-howto-coexist-resource-manager
[Hub-n-Spoke]: https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke
[Deploy-NVA]: https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha
[VNet-Config]: https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-peering


