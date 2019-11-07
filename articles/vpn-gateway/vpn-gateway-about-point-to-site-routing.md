---
title: Informazioni sul routing VPN da punto a sito di Azure | Microsoft Docs
description: Questo articolo illustra il comportamento del routing VPN da punto a sito.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 10/08/2019
ms.author: anzaman
ms.openlocfilehash: ced1bc647f93beec73b8101a952944f31e497658
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73693220"
---
# <a name="about-point-to-site-vpn-routing"></a>Informazioni sul routing VPN da punto a sito

Questo articolo illustra il comportamento del routing VPN di Azure da punto a sito. Il comportamento del routing VPN da punto a sito dipende dal client del sistema operativo, dal protocollo usato per la connessione VPN e dal modo in cui le reti virtuali (Vnet) sono connesse tra loro.

Azure attualmente supporta due protocolli per l'accesso remoto: IKEv2 e SSTP. IKEv2 è supportato in molti sistemi operativi client, tra cui Windows, Linux, MacOS, Android e iOS. SSTP è supportato solo in Windows. Se si apporta una modifica alla topologia della rete e sono disponibili client VPN di Windows, il pacchetto client VPN per i client Windows deve essere scaricato e installato nuovamente affinché le modifiche vengano applicate al client.

> [!NOTE]
> Le informazioni contenute in questo articolo si applicano solo al protocollo IKEv2.
>

## <a name="diagrams"></a>Informazioni sui diagrammi

In questo articolo sono presenti numerosi diagrammi. Ogni sezione illustra una topologia o una configurazione diversa. Ai fini di questo articolo, le connessioni da sito a sito e le connessioni da rete virtuale a rete virtuale funzionano allo stesso modo, poiché sono entrambe tunnel IPsec. Tutti i gateway VPN descritti in questo articolo sono basati su route.

## <a name="isolatedvnet"></a>Una rete virtuale isolata

La connessione gateway VPN da punto a sito in questo esempio fa riferimento a una rete virtuale non connessa o che non ha eseguito il peering con un'altra rete virtuale (VNet1). In questo esempio, i client possono accedere a VNet1.

![routing VNet isolato](./media/vpn-gateway-about-point-to-site-routing/1.jpg "routing VNet isolato")

### <a name="address-space"></a>Spazio degli indirizzi

* VNet1: 10.1.0.0/16

### <a name="routes-added"></a>Route aggiunte

* Route aggiunte a client Windows: 10.1.0.0/16, 192.168.0.0/24

* Route aggiunte a client non Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accesso

* I client Windows possono accedere a VNet1

* I client non Windows possono accedere a VNet1

## <a name="multipeered"></a>Più reti virtuali con peering

In questo esempio, la connessione gateway VPN da punto a sito fa riferimento a VNet1. VNet1 ha eseguito il peering con VNet2. VNet2 ha eseguito il peering con VNet3. VNet1 ha eseguito il peering con VNet4. Non vi è alcun peering diretto tra VNet1 e VNet3. In VNet1 è abilitata l'opzione "Consenti transito gateway" e in VNet2 è abilitata l'opzione "Use remote gateways" (Usa gateway remoti).

I client che usano Windows possono accedere direttamente alle reti virtuali con peering, ma il client VPN deve essere scaricato nuovamente se vengono apportate modifiche al peering della rete virtuale o alla topologia di rete. I client non Windows possono accedere direttamente alle reti virtuali con peering. L'accesso non è transitivo e riguarda solo le reti virtuali con peering diretto.

![più reti virtuali con peering](./media/vpn-gateway-about-point-to-site-routing/2.jpg "più reti virtuali con peering")

### <a name="address-space"></a>Spazio degli indirizzi:

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* VNet4: 10.4.0.0/16

### <a name="routes-added"></a>Route aggiunte

* Route aggiunte a client Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

* Route aggiunte a client non Windows: 10.1.0.0/16, 10.2.0.0/16, 10.4.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accesso

* I client Windows possono accedere a VNet1, VNet2 e VNet4, ma il client VPN deve essere scaricato nuovamente affinché le modifiche apportate alla topologia risultino effettive

* I client non Windows possono accedere a VNet1, VNet2 e VNet4

## <a name="multis2s"></a>Più reti virtuali connesse tramite VPN da sito a sito

In questo esempio, la connessione gateway VPN da punto a sito fa riferimento a VNet1. VNet1 è connessa a VNet2 tramite una connessione VPN da sito a sito. VNet2 è connessa a VNet3 tramite una connessione VPN da sito a sito. Non è presente alcun peering diretto o connessione VPN da sito a sito tra VNet1 e VNet3. Nessuna connessione da sito a sito esegue il protocollo BGP per il routing.

I client che usano Windows, o un altro sistema operativo supportato, possono accedere solo a VNet1. Per accedere ad altre reti virtuali, è necessario usare il protocollo BGP.

![più reti virtuali e S2S](./media/vpn-gateway-about-point-to-site-routing/3.jpg "più reti virtuali e S2S")

### <a name="address-space"></a>Spazio degli indirizzi

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Route aggiunte

* Route aggiunte a client Windows: 10.1.0.0/16, 192.168.0.0/24

* Route aggiunte a client non Windows: 10.1.0.0/16, 10.2.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accesso

* I client Windows possono accedere solo a VNet1

* I client non Windows possono accedere solo a VNet1

## <a name="multis2sbgp"></a>Più reti virtuali connesse tramite VPN da sito a sito (BGP)

In questo esempio, la connessione gateway VPN da punto a sito fa riferimento a VNet1. VNet1 è connessa a VNet2 tramite una connessione VPN da sito a sito. VNet2 è connessa a VNet3 tramite una connessione VPN da sito a sito. Non è presente alcun peering diretto o connessione VPN da sito a sito tra VNet1 e VNet3. Tutte le connessioni da sito a sito eseguono il protocollo BGP per il routing.

I client che usano Windows, o un altro sistema operativo supportato, possono accedere a tutte le reti virtuali connesse tramite una connessione VPN da sito a sito, ma le route alle reti virtuali connesse devono essere aggiunte manualmente ai client Windows.

![più reti virtuali e S2S (BGP)](./media/vpn-gateway-about-point-to-site-routing/4.jpg "più reti virtuali e S2S BGP")

### <a name="address-space"></a>Spazio degli indirizzi

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

### <a name="routes-added"></a>Route aggiunte

* Route aggiunte a client Windows: 10.1.0.0/16

* Route aggiunte a client non Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accesso

* I client Windows possono accedere a VNet1, VNet2 e VNet3, ma le route a VNet2 e VNet3 devono essere aggiunte manualmente

* I client non Windows possono accedere a VNet1, VNet2 e VNet3

## <a name="vnetbranch"></a>Una rete virtuale e una succursale

In questo esempio, la connessione gateway VPN da punto a sito fa riferimento a VNet1. VNet1 non è connessa o non ha eseguito il peering con altre reti virtuali, ma è connessa a un sito locale tramite una connessione VPN da sito a sito che non esegue il protocollo BGP.

I client Windows e non Windows possono accedere solo a VNet1.

![routing con una VNet e una succursale](./media/vpn-gateway-about-point-to-site-routing/5.jpg "routing con una VNet e una succursale")

### <a name="address-space"></a>Spazio degli indirizzi

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Route aggiunte

* Route aggiunte a client Windows: 10.1.0.0/16, 192.168.0.0/24

* Route aggiunte a client non Windows: 10.1.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accesso

* I client Windows possono accedere solo a VNet1

* I client non Windows possono accedere solo a VNet1

## <a name="vnetbranchbgp"></a>Una rete virtuale e una succursale (BGP)

In questo esempio, la connessione gateway VPN da punto a sito fa riferimento a VNet1. VNet1 non è connessa o non ha eseguito il peering con altre reti virtuali, ma è connessa a un sito locale (Site1) tramite una connessione VPN da sito a sito che esegue il protocollo BGP.

I client Windows possono accedere alla rete virtuale e alla succursale (Site1), ma le route a Site1 devono essere aggiunte manualmente al client. I client non Windows possono accedere sia alla rete virtuale che alla succursale locale.

![una VNet e una succursale (BGP)](./media/vpn-gateway-about-point-to-site-routing/6.jpg "una VNet e una succursale")

### <a name="address-space"></a>Spazio degli indirizzi

* VNet1: 10.1.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Route aggiunte

* Route aggiunte a client Windows: 10.1.0.0/16, 192.168.0.0/24

* Route aggiunte a client non Windows: 10.1.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accesso

* I client Windows possono accedere a VNet1 e Site1, ma le route a Site1 devono essere aggiunte manualmente

* I client non Windows possono accedere a VNet1 e a Site1


## <a name="multivnets2sbranch"></a>Più reti virtuali connesse tramite una connessione da sito a sito e una succursale

In questo esempio, la connessione gateway VPN da punto a sito fa riferimento a VNet1. VNet1 è connessa a VNet2 tramite una connessione VPN da sito a sito. VNet2 è connessa a VNet3 tramite una connessione VPN da sito a sito. Non è presente alcun peering diretto o tunnel VPN da sito a sito tra le reti VNet1 e VNet3. VNet3 è connessa a una succursale (Site1) tramite una connessione VPN da sito a sito. Nessuna connessione VPN esegue il protocollo BGP.

Tutti i client possono accedere solo a VNet1.

![VNet S2S e succursale](./media/vpn-gateway-about-point-to-site-routing/7.jpg "VNet S2S e succursale")

### <a name="address-space"></a>Spazio degli indirizzi

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Route aggiunte

* Route aggiunte a client Windows: 10.1.0.0/16, 192.168.0.0/24

* Route aggiunte a client non Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accesso

* I client Windows possono accedere solo a VNet1

* I client non Windows possono accedere solo a VNet1

## <a name="multivnets2sbranchbgp"></a>Più reti virtuali connesse tramite una connessione da sito a sito e una succursale (BGP)

In questo esempio, la connessione gateway VPN da punto a sito fa riferimento a VNet1. VNet1 è connessa a VNet2 tramite una connessione VPN da sito a sito. VNet2 è connessa a VNet3 tramite una connessione VPN da sito a sito. Non è presente alcun peering diretto o tunnel VPN da sito a sito tra le reti VNet1 e VNet3. VNet3 è connessa a una succursale (Site1) tramite una connessione VPN da sito a sito. Tutte le connessioni VPN eseguono il protocollo BGP.

I client che usano Windows possono accedere alle reti virtuali e ai siti connessi tramite una connessione VPN da sito a sito, ma le route a VNet2, VNet3 e Site1 devono essere aggiunte manualmente al client. I client non Windows possono accedere alle reti virtuali e ai siti connessi tramite una connessione VPN da sito a sito senza alcun intervento manuale. L'accesso è transitivo e i client possono accedere alle risorse in tutte le reti virtuali e in tutti i siti (locali) connessi.

![VNet S2S e succursale](./media/vpn-gateway-about-point-to-site-routing/8.jpg "VNet S2S e succursale")

### <a name="address-space"></a>Spazio degli indirizzi

* VNet1: 10.1.0.0/16

* VNet2: 10.2.0.0/16

* VNet3: 10.3.0.0/16

* Site1: 10.101.0.0/16

### <a name="routes-added"></a>Route aggiunte

* Route aggiunte a client Windows: 10.1.0.0/16, 192.168.0.0/24

* Route aggiunte a client non Windows: 10.1.0.0/16, 10.2.0.0/16, 10.3.0.0/16, 10.101.0.0/16, 192.168.0.0/24

### <a name="access"></a>Accesso

* I client Windows possono accedere a VNet1, VNet2, VNet3 e Site1, ma le route a VNet2, VNet3 e Site1 devono essere aggiunte manualmente al client.

* I client non Windows possono accedere a VNet1, Vnet2, VNet3 e Site1.

## <a name="next-steps"></a>Passaggi successivi

Vedere [Creare una VPN da punto a sito tramite il portale di Azure](vpn-gateway-howto-point-to-site-resource-manager-portal.md) per iniziare a creare la propria VPN da punto a sito.
