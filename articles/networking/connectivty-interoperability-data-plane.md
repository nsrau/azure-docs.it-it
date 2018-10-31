---
title: 'Interoperabilità di ExpressRoute, VPN da sito a sito e peering reti virtuali - analisi del piano dati: interoperabilità delle funzionalità per la connettività di back-end di Azure | Microsoft Docs'
description: Questa pagina contiene i dettagli dell'analisi del piano dati dell'installazione test creata per analizzare l'interoperabilità delle funzionalità ExpressRoute, VPN da sito a sito e peering reti virtuali.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute,vpn-gateway,virtual-network
ms.topic: article
ms.workload: infrastructure-services
ms.date: 10/18/2018
ms.author: rambala
ms.openlocfilehash: c9f3824b1e0f44338696ba3c2e434d60eee3af8b
ms.sourcegitcommit: 9e179a577533ab3b2c0c7a4899ae13a7a0d5252b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49947103"
---
# <a name="interoperability-of-expressroute-site-to-site-vpn-and-vnet-peering---data-plane-analysis"></a>Interoperabilità di ExpressRoute, VPN da sito a sito e peering reti virtuali: analisi del piano dati

Questo articolo illustra l'analisi del piano dati dell'installazione test. Per esaminare l'installazione test, vedere la relativa sezione in [questo articolo][Setup]. Per esaminare i dettagli di configurazione dell'installazione test, vedere [Configurazione dell'installazione test][Configuration]. Per esaminare l'analisi del piano di controllo dell'installazione test, vedere [Analisi del piano di controllo][Control-Analysis].

L'analisi del piano dati esamina il percorso seguito dai pacchetti che passano da una rete locale (LAN/rete virtuale) a un'altra all'interno di una topologia. Il percorso dei dati tra due reti locali può non essere necessariamente simmetrico. Di conseguenza, in questo articolo viene analizzato il percorso di inoltro da una rete locale a un'altra separatamente dal percorso inverso.

##<a name="data-path-from-hub-vnet"></a>Percorso dei dati dalla rete virtuale dell'hub

###<a name="path-to-spoke-vnet"></a>Percorso alla rete virtuale spoke

Il peering reti virtuali emula la funzionalità di bridge di rete tra le due reti virtuali con peering. Di seguito è indicato l'output traceroute da una rete virtuale dell'hub a una macchina virtuale nella rete virtuale spoke:

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.11.30.4

    Trace complete.

L'immagine seguente è la rappresentazione grafica della connessione tra la rete virtuale dell'hub e la rete virtuale spoke presentata da Azure Network Watcher:


[![1]][1]

###<a name="path-to-branch-vnet"></a>Percorso alla rete virtuale di diramazione

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms     1 ms     1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     2 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Nel traceroute precedente, il primo hop è il gateway VPN della rete virtuale dell'hub. Il secondo hop è il gateway VPN della rete virtuale di diramazione, il cui indirizzo IP non è annunciato all'interno della rete virtuale dell'hub. Il terzo hop è la macchina virtuale nella rete virtuale di diramazione.

L'immagine seguente è la rappresentazione grafica della connessione tra la rete virtuale dell'hub e la rete virtuale di diramazione presentata da Azure Network Watcher:

[![2]][2]

Per la stessa connessione, l'immagine seguente rappresenta la visualizzazione della griglia presentata da Azure Network Watcher:

[![3]][3]

###<a name="path-to-on-premises-location-1"></a>Percorso alla rete locale Location-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     2 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Nel traceroute precedente il primo hop è l'endpoint del tunnel del gateway ExpressRoute a MSEE. Il secondo e il terzo hop sono rispettivamente il router CE e la rete LAN locale Location 1, i cui indirizzi IP non vengono annunciati all'interno della rete virtuale dell'hub. Il quarto hop è la macchina virtuale nella rete locale Location-1.


###<a name="path-to-on-premises-location-2"></a>Percorso alla rete locale Location-2

    C:\Users\rb>tracert 10.1.31.10

    Tracing route to 10.1.31.10 over a maximum of 30 hops

      1    76 ms    75 ms    75 ms  10.10.30.134
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    75 ms    75 ms    75 ms  10.1.31.10

    Trace complete.

Nel traceroute precedente il primo hop è l'endpoint del tunnel del gateway ExpressRoute a MSEE. Il secondo e il terzo hop sono rispettivamente il router CE e la rete LAN locale Location 2, i cui indirizzi IP non vengono annunciati all'interno della rete virtuale dell'hub. Il quarto hop è la macchina virtuale nella rete locale Location-2.

###<a name="path-to-remote-vnet"></a>Percorso alla rete virtuale remota

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     2 ms     2 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    69 ms  10.17.30.4

    Trace complete.

Nel traceroute precedente il primo hop è l'endpoint del tunnel del gateway ExpressRoute a MSEE. Il secondo hop è l'indirizzo IP del gateway della rete virtuale remota. L'intervallo IP del secondo hop non viene annunciato all'interno della rete virtuale dell'hub. Il terzo hop è la macchina virtuale nella rete virtuale remota.

##<a name="data-path-from-spoke-vnet"></a>Percorso dei dati dalla rete virtuale spoke

Tenere presente che la rete virtuale spoke condivide la visualizzazione di rete della rete virtuale dell'hub. Con il peering reti virtuali, la rete virtuale spoke usa la connettività dei gateway remoti della rete virtuale dell'hub come se questi fossero connessi direttamente alla rete virtuale spoke.

###<a name="path-to-hub-vnet"></a>Percorso alla rete virtuale dell'hub

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.10.30.4

    Trace complete.

###<a name="path-to-branch-vnet"></a>Percorso alla rete virtuale di diramazione

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.10.30.142
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

Nel traceroute precedente il primo hop è il gateway VPN della rete virtuale dell'hub. Il secondo hop è il gateway VPN della rete virtuale di diramazione, il cui indirizzo IP non è annunciato all'interno della rete virtuale spoke o dell'hub. Il terzo hop è la macchina virtuale nella rete virtuale di diramazione.

###<a name="path-to-on-premises-location-1"></a>Percorso alla rete locale Location-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Nel traceroute precedente il primo hop è l'endpoint del tunnel del gateway ExpressRoute della rete virtuale dell'hub a MSEE. Il secondo e il terzo hop sono rispettivamente il router CE e la rete LAN locale Location 1, i cui indirizzi IP non vengono annunciati all'interno della rete virtuale spoke o dell'hub. Il quarto hop è la macchina virtuale nella rete locale Location-1.

###<a name="path-to-on-premises-location-2"></a>Percorso alla rete locale Location-2

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    24 ms     2 ms     3 ms  10.10.30.132
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4     3 ms     2 ms     2 ms  10.2.30.10

    Trace complete.

Nel traceroute precedente il primo hop è l'endpoint del tunnel del gateway ExpressRoute della rete virtuale dell'hub a MSEE. Il secondo e il terzo hop sono rispettivamente il router CE e la rete LAN locale Location 2, i cui indirizzi IP non vengono annunciati all'interno della rete virtuale spoke o dell'hub. Il quarto hop è la macchina virtuale nella rete locale Location-2.

###<a name="path-to-remote-vnet"></a>Percorso alla rete virtuale remota

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1     2 ms     1 ms     1 ms  10.10.30.133
      2     *        *        *     Request timed out.
      3    71 ms    70 ms    70 ms  10.17.30.4

    Trace complete.

Nel traceroute precedente il primo hop è l'endpoint del tunnel del gateway ExpressRoute della rete virtuale dell'hub a MSEE. Il secondo hop è l'indirizzo IP del gateway della rete virtuale remota. L'intervallo IP del secondo hop non viene annunciato all'interno della rete virtuale spoke o dell'hub. Il terzo hop è la macchina virtuale nella rete virtuale remota.

##<a name="data-path-from-branch-vnet"></a>Percorso dei dati dalla rete virtuale di diramazione

###<a name="path-to-hub-vnet"></a>Percorso alla rete virtuale dell'hub

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     3 ms  10.10.30.4

    Trace complete.

Nel traceroute precedente il primo hop è il gateway VPN della rete virtuale di diramazione. Il secondo hop è il gateway VPN della rete virtuale dell'hub, il cui indirizzo IP non è annunciato all'interno della rete virtuale remota. Il terzo hop è la macchina virtuale nella rete virtuale dell'hub.

###<a name="path-to-spoke-vnet"></a>Percorso alla rete virtuale spoke

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1     1 ms    <1 ms     1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     4 ms     3 ms     2 ms  10.11.30.4

    Trace complete.

Nel traceroute precedente il primo hop è il gateway VPN della rete virtuale di diramazione. Il secondo hop è il gateway VPN della rete virtuale dell'hub, il cui indirizzo IP non è annunciato all'interno della rete virtuale remota e il terzo hop è la macchina virtuale nella rete virtuale spoke.

###<a name="path-to-on-premises-location-1"></a>Percorso alla rete locale Location-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1     1 ms    <1 ms    <1 ms  10.11.30.100
      2     *        *        *     Request timed out.
      3     3 ms     2 ms     2 ms  10.2.30.125
      4     *        *        *     Request timed out.
      5     3 ms     3 ms     3 ms  10.2.30.10

    Trace complete.

Nel traceroute precedente il primo hop è il gateway VPN della rete virtuale di diramazione. Il secondo hop è il gateway VPN della rete virtuale dell'hub, il cui indirizzo IP non è annunciato all'interno della rete virtuale remota. Il terzo hop è il punto di terminazione del tunnel VPN sul router CE primario. Il quarto hop è un indirizzo IP interno della rete LAN locale Location-1 che non è annunciato fuori dal router CE. Il quinto hop è la macchina virtuale di destinazione nella rete locale Location-1.

###<a name="path-to-on-premises-location-2-and-remote-vnet"></a>Percorso alla rete locale Location-2 e alla rete virtuale remota

Come descritto prima nell'analisi del piano di controllo, la rete virtuale di diramazione non ha visibilità per la rete locale Location-2 o per la rete virtuale remota in base alla configurazione di rete. I seguenti risultati del ping ne sono una conferma. 

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

##<a name="data-path-from-on-premises-location-1"></a>Percorso dei dati dalla rete locale Location-1

###<a name="path-to-hub-vnet"></a>Percorso alla rete virtuale dell'hub

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     2 ms     2 ms     2 ms  10.10.30.4

    Trace complete.

In traceroute precedente i primi due hop fanno parte della rete locale. Il terzo hop è l'interfaccia MSEE primario per il router CE. Il quarto hop è il gateway ExpressRoute della rete virtuale dell'hub, il cui intervallo IP non è annunciato alla rete locale. Il quinto hop è la macchina virtuale di destinazione.

Azure Network Watcher offre solo una visualizzazione basata su Azure. Di conseguenza, per la visualizzazione basata sulle reti locali è stato usato Monitoraggio prestazioni rete di Azure. Monitoraggio prestazioni rete rende disponibili agenti che possono essere server installati in una rete esterna ad Azure ed eseguono analisi del percorso dati.

L'immagine seguente è la visualizzazione della topologia della connettività tra la macchina virtuale della rete locale Location-1 alla macchina virtuale nella rete virtuale dell'hub via ExpressRoute.

[![4]][4]

Ricordare che l'installazione test usa la VPN da sito a sito come connettività di backup per ExpressRoute tra la rete locale Location-1 e la rete virtuale dell'hub. Per testare di nuovo il percorso dati, indurre un errore di collegamento di ExpressRoute tra il router CE primario della rete locale Location-1 e il router MSEE corrispondente arrestando l'interfaccia CE rivolta verso il router MSEE.

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     3 ms  10.10.30.4

    Trace complete.

L'immagine seguente è la visualizzazione della topologia della connettività tra la macchina virtuale della rete locale Location-1 alla macchina virtuale nella rete virtuale dell'hub attraverso la connettività VPN da sito a sito quando la connettività ExpressRoute non è attiva.

[![5]][5]

###<a name="path-to-spoke-vnet"></a>Percorso alla rete virtuale spoke

Si prenda di nuovo in considerazione la connettività ExpressRoute primaria per eseguire l'analisi del percorso dati verso la rete virtuale spoke.

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5     3 ms     2 ms     2 ms  10.11.30.4

    Trace complete.

La connettività ExpressRoute-1 primaria verrà usata per il resto dell'analisi del percorso dati.

###<a name="path-to-branch-vnet"></a>Percorso alla rete virtuale di diramazione

    C:\Users\rb>tracert 10.11.30.68

    Tracing route to 10.11.30.68 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2    <1 ms    <1 ms    <1 ms  192.168.30.0
      3     3 ms     2 ms     2 ms  10.11.30.68

    Trace complete.

###<a name="path-to-on-premises-location-2"></a>Percorso alla rete locale Location-2

Come descritto prima nell'analisi del piano di controllo, la rete locale Location-1 non ha visibilità per la rete locale Location-2 in base alla configurazione di rete. I seguenti risultati del ping ne sono una conferma. 

    C:\Users\rb>ping 10.1.31.10
    
    Pinging 10.1.31.10 with 32 bytes of data:

    Request timed out.
    ...
    Request timed out.

    Ping statistics for 10.1.31.10:
        Packets: Sent = 4, Received = 0, Lost = 4 (100% loss),

###<a name="path-to-remote-vnet"></a>Percorso alla rete virtuale remota

    C:\Users\rb>tracert 10.17.30.4

    Tracing route to 10.17.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.2.30.3
      2     2 ms     5 ms     7 ms  192.168.30.0
      3    <1 ms    <1 ms    <1 ms  192.168.30.18
      4     *        *        *     Request timed out.
      5    69 ms    70 ms    69 ms  10.17.30.4

    Trace complete.

##<a name="data-path-from-on-premises-location-2"></a>Percorso dei dati dalla rete locale Location-2

###<a name="path-to-hub-vnet"></a>Percorso alla rete virtuale dell'hub

    C:\Windows\system32>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    <1 ms    <1 ms    <1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.4
      3    <1 ms    <1 ms    <1 ms  192.168.31.22
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Percorso alla rete virtuale spoke

    C:\Windows\system32>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops
      1    <1 ms    <1 ms     1 ms  10.1.31.3
      2    <1 ms    <1 ms    <1 ms  192.168.31.0
      3    <1 ms    <1 ms    <1 ms  192.168.31.18
      4     *        *        *     Request timed out.
      5    75 ms    74 ms    74 ms  10.11.30.4

    Trace complete.

###<a name="path-to-branch-vnet-on-premises-location-1-and-remote-vnet"></a>Percorso a rete virtuale di diramazione, rete locale Location-1 e rete virtuale remota

Come descritto prima nell'analisi del piano di controllo, la rete locale Location-1 non ha visibilità per la rete virtuale di diramazione, la rete locale Location-1 e la rete virtuale remota in base alla configurazione di rete. 

##<a name="data-path-from-remote-vnet"></a>Percorso dei dati dalla rete virtuale remota

###<a name="path-to-hub-vnet"></a>Percorso alla rete virtuale dell'hub

    C:\Users\rb>tracert 10.10.30.4

    Tracing route to 10.10.30.4 over a maximum of 30 hops

      1    65 ms    65 ms    65 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    69 ms    68 ms    68 ms  10.10.30.4

    Trace complete.

###<a name="path-to-spoke-vnet"></a>Percorso alla rete virtuale spoke

    C:\Users\rb>tracert 10.11.30.4

    Tracing route to 10.11.30.4 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3    71 ms    69 ms    69 ms  10.11.30.4

    Trace complete.

### <a name="path-to-branch-vnet-and-on-premises-location-2"></a>Percorso alla rete virtuale di diramazione e alla rete locale Location-2

Come descritto prima nell'analisi del piano di controllo, la rete virtuale remota non ha visibilità per la rete virtuale di diramazione e la rete locale Location-2 in base alla configurazione di rete. 


### <a name="path-to-on-premises-location-1"></a>Percorso alla rete locale Location-1

    C:\Users\rb>tracert 10.2.30.10

    Tracing route to 10.2.30.10 over a maximum of 30 hops

      1    67 ms    67 ms    67 ms  10.17.30.36
      2     *        *        *     Request timed out.
      3     *        *        *     Request timed out.
      4    69 ms    69 ms    69 ms  10.2.30.10

    Trace complete.


## <a name="further-reading"></a>Altre informazioni

### <a name="using-expressroute-and-site-to-site-vpn-connectivity-in-tandem"></a>Uso di ExpressRoute e della connettività VPN da sito a sito in parallelo

####<a name="site-to-site-vpn-over-expressroute"></a>VPN da sito a sito con ExpressRoute

La connettività VPN da sito a sito può essere configurata con il peering ExpressRoute di Microsoft per scambiare privatamente i dati tra la rete locale e le reti virtuali di Azure con riservatezza, impossibilità di riproduzione, autenticità e integrità. Per altre informazioni su come configurare la VPN IPSec da sito a sito in modalità tunnel con il peering ExpressRoute di Microsoft, vedere [Configurare una VPN da sito a sito tramite peering ExpressRoute Microsoft][S2S-Over-ExR]. 

La limitazione principale della configurazione VPN da sito a sito con peering Microsoft è la velocità effettiva. La velocità effettiva nel tunnel IPSec è limitata dalla capacità del gateway VPN. La velocità effettiva del gateway VPN è inferiore a quella di ExpressRoute. In questi scenari l'uso del tunnel IPSec per il traffico ad alta protezione e del peering privato per tutti gli altri tipi di traffico consente di ottimizzare l'uso della larghezza di banda di ExpressRoute.

#### <a name="site-to-site-vpn-as-a-secure-failover-path-for-expressroute"></a>VPN da sito a sito come percorso di failover protetto per ExpressRoute
ExpressRoute viene offerto come coppia di circuiti ridondanti per garantire la disponibilità elevata. È possibile configurare la connettività ExpressRoute con ridondanza geografica in diverse aree di Azure. Inoltre, come si è visto nell'installazione test, se all'interno di una determinata area di Azure si vuole usare un percorso di failover per la connettività ExpressRoute, è possibile usare la VPN da sito a sito. Quando gli stessi prefissi vengono annunciati sia su ExpressRoute che sulla VPN da sito a sito, Azure preferisce ExpressRoute alla VPN da sito a sito. Per evitare il routing asimmetrico tra ExpressRoute e la VPN da sito a sito, la configurazione di rete locale deve anche ricambiare preferendo ExpressRoute piuttosto che la connettività VPN da sito a sito.

Per altre informazioni su come configurare le connessioni ExpressRoute e VPN da sito a sito coesistenti, vedere [Configurare connessioni coesistenti da sito a sito ed ExpressRoute usando PowerShell][ExR-S2S-CoEx].

### <a name="extending-backend-connectivity-to-spoke-vnets-and-branch-locations"></a>Estensione della connettività di back-end a reti virtuali spoke e diramazioni

#### <a name="spoke-vnet-connectivity-using-vnet-peering"></a>Connettività della rete virtuale spoke con il peering reti virtuali

L'architettura di rete virtuale hub-spoke è ampiamente usata. L'hub è una rete virtuale di Azure che funge da punto centrale di connettività tra le reti virtuali spoke e la rete locale. Gli spoke sono reti virtuali che eseguono il peering con l'hub e possono essere usati per isolare i carichi di lavoro. Il traffico scorre tra il data center locale e l'hub attraverso una connessione VPN o ExpressRoute. Per maggiori dettagli sull'architettura, vedere [Implementare una topologia di rete hub-spoke in Azure][Hub-n-Spoke]

Il peering reti virtuali all'interno di un'area consente alle reti virtuali spoke di usare i gateway di rete virtuale dell'hub (gateway ExpressRoute e VPN) per comunicare con le reti remote.

#### <a name="branch-vnet-connectivity-using-site-to-site-vpn"></a>Creare un ramo della connettività di rete virtuale usando la VPN da sito a sito

Se si vuole creare un ramo di reti virtuali (in aree diverse) e le reti locali comunicano tra loro attraverso una rete virtuale dell'hub, la soluzione nativa di Azure è la connettività VPN da sito a sito che usa la VPN. Un'opzione alternativa è usare un'appliance virtuale di rete per il routing nell'hub.

Per configurare i gateway VPN, vedere [Configurazione del gateway VPN][VPN]. Per la distribuzione di appliance di rete virtuale con disponibilità elevata, vedere [Distribuire appliance virtuali di rete con disponibilità elevata][Deploy-NVA].

## <a name="next-steps"></a>Passaggi successivi

Per informazioni sul numero di circuiti ExpressRoute che possono essere connessi a un gateway ExpressRoute, sul numero di gateway ExpressRoute che si possono connettere a un circuito ExpressRoute o per informazioni su altri limiti di scalabilità di ExpressRoute, vedere le [domande frequenti su ExpressRoute][ExR-FAQ]


<!--Image References-->
[1]: ./media/backend-interoperability/HubVM-SpkVM.jpg "Visualizzazione di Network Watcher della connettività dalla rete virtuale dell'hub alla rete virtuale spoke"
[2]: ./media/backend-interoperability/HubVM-BranchVM.jpg "Visualizzazione di Network Watcher della connettività dalla rete virtuale dell'hub alla rete virtuale di diramazione"
[3]: ./media/backend-interoperability/HubVM-BranchVM-Grid.jpg "Visualizzazione griglia di Network Watcher della connettività dalla rete virtuale dell'hub alla rete virtuale di diramazione"
[4]: ./media/backend-interoperability/Loc1-HubVM.jpg "Visualizzazione di Monitoraggio prestazioni rete della connettività dalla macchina virtuale di Location-1 alla rete virtuale dell'hub via ExpressRoute 1"
[5]: ./media/backend-interoperability/Loc1-HubVM-S2S.jpg "Visualizzazione di Monitoraggio prestazioni rete della connettività dalla macchina virtuale di Location-1 alla rete virtuale dell'hub via VPN da sito a sito"

<!--Link References-->
[Setup]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-preface
[Configuration]: https://docs.microsoft.com/azure/networking/connectivty-interoperability-config
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




