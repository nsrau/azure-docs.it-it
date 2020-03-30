---
title: Criteri di peering Microsoft
titleSuffix: Azure
description: Criteri di peering Microsoft
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: conceptual
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: a683ad71f5e80c91728262dc7bbabf36e9d68deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775225"
---
# <a name="peering-policy"></a>Criteri di peering
I requisiti generali di Microsoft forniti dalla rete sono illustrati nelle sezioni seguenti. Questi sono applicabili sia al peering diretto che alle richieste di peering di Exchange.

## <a name="technical-requirements"></a>Requisiti tecnici

* Una rete completamente ridondante con capacità sufficiente per scambiare traffico senza congestione.
* Peer avrà un numero di sistema autonomo instradabile pubblicamente (ASN).
* Sono supportati sia IPv4 che IPv6 e Microsoft prevede di stabilire sessioni di entrambi i tipi in ogni posizione di peering.
* MD5 non è supportato.
* **Dettagli ASN:**
    * Microsoft gestisce AS8075 con i seguenti ASN: AS8068, AS8069, AS12076. Per un elenco completo di ASN con peering AS8075, fare riferimento a AS-SET MICROSOFT.
    * Tutte le parti che osservano il peering con Microsoft accettano in alcun caso di non accettare route da AS12076 (Express Route) e devono filtrare AS12076 in tutti i peer.
* **Criteri di routing:**
    * Peer avrà almeno un instradabile pubblicamente /24.
    * Microsoft sovrascriverà i discriminatori multi-uscita (MED) ricevuti.
    * Microsoft preferisce ricevere i tag della community BGP dai peer per indicare l'origine della route.
    * I peer sono tenuti a registrare le proprie route in un database pubblico di Internet Routing Registry (IRR), ai fini del filtraggio, e si impegneranno in buona fede per mantenere aggiornate queste informazioni.
    * È consigliabile che i peer impostino un prefisso massimo di 1000 (IPv4) e 100 route (IPv6) nelle sessioni di peering con Microsoft.
    * A meno che non siano specificamente concordati in precedenza, i peer devono annunciare route coerenti in tutte le località in cui sono associati con Microsoft.
    * In generale, le sessioni di peering con AS8075 annuncieranno tutte le route AS-MICROSOFT. Le interconnessioni AS8075 in Africa e Asia possono essere limitate alle rotte rilevanti per la rispettiva regione.
    * Nessuna delle due parti stabilirà una route statica, una route di ultima risorsa o invierà in altro modo il traffico all'altra parte per una route non annunciata tramite BGP.
    * I peer devono rispettare gli standard del settore [MANRS](https://www.manrs.org/) per la sicurezza delle route.

## <a name="operational-requirements"></a>Requisiti operativi
* Un Network Operations Center (NOC) 24x7, completamente dotato di personale, in grado di assistere nella risoluzione di tutti i problemi tecnici e di prestazioni, le violazioni della sicurezza, gli attacchi Denial of Service o qualsiasi altro abuso proveniente dal peer o dai loro clienti.
* I peer devono avere un profilo completo e aggiornato su [PeeringDB,](https://www.peeringdb.com) incluso un indirizzo di posta elettronica NOC 24x7 proveniente dal dominio aziendale e dal numero di telefono. Utilizziamo queste informazioni per convalidare i dettagli del peer, come le informazioni NOC, le informazioni di contatto tecniche e la loro presenza presso le strutture di peering, ecc. Gli account Personali Yahoo, Gmail e hotmail non sono accettati.

## <a name="physical-connection-requirements"></a>Requisiti di connessione fisica
* Le posizioni in cui è possibile connettersi a Microsoft per il peering diretto o il peering di Exchange sono elencate in [PeeringDB](https://www.peeringdb.com/net/694)
* **Peering di interscambio:**
    * L'interconnessione deve essere su fibra monomodalità utilizzando l'ottica 10 Gbps appropriata.
    * I peer devono aggiornare le proprie porte quando l'utilizzo di picco supera il 50%.
* **Peering diretto:**
    * L'interconnessione deve essere su fibra a modalità singola utilizzando l'ottica appropriata 10Gbps o 100Gbps.
    * Microsoft stabilirà solo il peering diretto con provider di servizi di rete o ISP.
    * I peer devono aggiornare i propri porti quando l'utilizzo di picco supera il 50% e mantenere una capacità diversificata in ogni metropolitana, all'interno di una singola posizione o in diverse località in una metropolitana.
    * Ogni peering diretto è costituito da due connessioni a due router perimetrali Microsoft dai router del peer situati nel perimetro del peer. Microsoft richiede sessioni BGP doppio su queste connessioni. Il peer può scegliere di non distribuire i dispositivi ridondanti.

## <a name="traffic-requirements"></a>Requisiti del traffico
* I peer sul peering di Exchange devono avere almeno 200 MB di traffico e meno di 2 Gb.  Per il traffico superiore a 2 Gb il peering diretto deve essere esaminato.
* Per il peering diretto, il traffico dalla rete a Microsoft deve soddisfare requisiti minimi inferiori.

    | Area geografica                      | Traffico minimo verso Microsoft   |
    | :----------------------- |:-------------------------------|
    | Africa                   | 500 Mbps                       |
    | APAC (tranne l'India)      |   2 Gbps                       |
    | APAC (solo India)        | 500 Mbps                       |
    | Europa                   |   2 Gbps                       |
    | Latam                    |   2 Gbps                       |
    | Medio Oriente              | 500 Mbps                       |
    | ND                       |   2 Gbps                       |

* **Diversità:**
    * In NA, Europa, APAC e LATAM, interconnettere in almeno tre località geograficamente diverse, se possibile, e mantenere una capacità diversificata per consentire il failover del traffico all'interno di ogni metropolitana.
    * In Africa, Medio Oriente e India, interconnettersi in tutte le diverse località possibili. Deve mantenere una capacità diversificata sufficiente per garantire che il traffico rimanga nella regione.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui passaggi per configurare il peering diretto con Microsoft, segui la procedura dettagliata di [peering diretto.](walkthrough-direct-all.md)
* Per informazioni sui passaggi per configurare il peering di Exchange con Microsoft, seguire la procedura dettagliata di [peering di Exchange](walkthrough-exchange-all.md).