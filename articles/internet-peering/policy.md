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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775225"
---
# <a name="peering-policy"></a>Criteri di peering
I requisiti generali di Microsoft della rete sono illustrati nelle sezioni riportate di seguito. Applicabili sia al peering diretto che alle richieste di peering di Exchange.

## <a name="technical-requirements"></a>Requisiti tecnici

* Una rete completamente ridondante con capacità sufficiente per lo scambio di traffico senza congestione.
* Il peer avrà un numero di sistema autonomo (ASN) instradabile pubblicamente.
* Sono supportati sia IPv4 che IPv6 e Microsoft prevede di stabilire sessioni di entrambi i tipi in ogni località di peering.
* MD5 non è supportato.
* **Dettagli ASN:**
    * Microsoft gestisce AS8075 insieme ai seguenti ASN: AS8068, AS8069, AS12076. Per un elenco completo di ASN con peering AS8075, fare riferimento a SET MICROSOFT.
    * Il peering di tutte le parti con Microsoft non accetta le route da AS12076 (Express Route) in qualsiasi circostanza e deve filtrare AS12076 in tutti i peer.
* **Criteri di routing:**
    * Il peer avrà almeno un instradabile pubblicamente/24.
    * Microsoft sovrascriverà i discriminatori a più uscite ricevuti (MED).
    * Microsoft preferisce ricevere i tag della community BGP dai peer per indicare l'origine della route.
    * Si prevede che i peer registrino le route in un database di Internet routing Registry (IRR) pubblico, allo scopo di filtrare, e conferiscono un impegno di fiducia per la conservazione delle informazioni.
    * Si consiglia ai peer di impostare un prefisso max di 1000 (IPv4) e 100 (IPv6) route nelle sessioni di peering con Microsoft.
    * A meno che non sia specificato in anticipo, i peer dovrebbero annunciare Route coerenti in tutte le posizioni in cui sono peer con Microsoft.
    * In generale, le sessioni di peering con AS8075 annuncieranno tutte le route AS-MICROSOFT. Le interconnessioni AS8075 in Africa e Asia possono essere limitate alle route rilevanti per la rispettiva area.
    * Nessuna delle due parti stabilirà una route statica, una route dell'ultima risorsa o altrimenti invierà traffico all'altra parte per una route non annunciata tramite BGP.
    * Si prevede che i peer siano conformi agli standard del settore [MANRS](https://www.manrs.org/) per la sicurezza delle route.

## <a name="operational-requirements"></a>Requisiti operativi
* Un servizio di rete Operations Center (NOC) completamente personale, in grado di supportare la risoluzione di tutti i problemi tecnici e di prestazioni, le violazioni della sicurezza, gli attacchi Denial of Service o qualsiasi altro abuso originato all'interno del peer o dei clienti.
* Si prevede che i peer abbiano un profilo completo e aggiornato in [PeeringDB](https://www.peeringdb.com) , incluso un messaggio di posta elettronica NOC 24x7 dal dominio aziendale e dal numero di telefono. Queste informazioni vengono usate per convalidare i dettagli del peer, ad esempio le informazioni relative ai NOC, le informazioni di contatto tecnico e la loro presenza presso le strutture di peering e così via. Gli account personali Yahoo, Gmail e Hotmail non sono accettati.

## <a name="physical-connection-requirements"></a>Requisiti di connessione fisica
* Le località in cui è possibile connettersi con Microsoft per il peering diretto o il peering di Exchange sono elencate in [PeeringDB](https://www.peeringdb.com/net/694)
* **Peering di Exchange:**
    * L'interconnessione deve essere basata su Fiber in modalità singola usando l'ottica 10Gbps appropriata.
    * Si prevede che i peer aggiornino le porte quando il picco di utilizzo supera il 50%.
* **Peering diretto:**
    * L'interconnessione deve essere basata su Fiber in modalità singola usando l'ottica 10Gbps o 100Gbps appropriata.
    * Microsoft stabilirà il peering diretto solo con provider di servizi di rete o ISP.
    * Si prevede che i peer aggiornino le porte quando il picco di utilizzo supera il 50% e mantengono diverse capacità in ogni metro, in un'unica posizione o in diverse posizioni in una metropolitana.
    * Ogni peering diretto è costituito da due connessioni a due router Microsoft Edge dai router del peer che si trovano in Edge del peer. Microsoft richiede sessioni BGP doppie tra queste connessioni. Il peer può scegliere di non distribuire dispositivi ridondanti alla fine.

## <a name="traffic-requirements"></a>Requisiti di traffico
* Il peering sul peering di Exchange deve avere un minimo di 200 MB di traffico e minore di 2 GB.  Per il traffico che supera i 2 GB, è necessario rivedere il peering diretto.
* Per il peering diretto, il traffico dalla rete a Microsoft deve soddisfare i requisiti minimi.

    | Zona geografica                      | Traffico minimo a Microsoft   |
    | :----------------------- |:-------------------------------|
    | Africa                   | 500 Mbps                       |
    | APAC (eccetto India)      |   2 Gbps                       |
    | APAC (solo India)        | 500 Mbps                       |
    | Europa                   |   2 Gbps                       |
    | LATAM                    |   2 Gbps                       |
    | Medio Oriente              | 500 Mbps                       |
    | ND                       |   2 Gbps                       |

* **Diversità**
    * In NA, Europe, APAC e LATAt, interconnettersi in almeno tre posizioni geograficamente diverse, se possibile, e mantenere diverse capacità per consentire il failover del traffico all'interno di ogni metro.
    * In Africa, Medio Oriente e India, è possibile interconnettere il maggior numero possibile di posizioni diverse. Deve mantenere una capacità diversificata sufficiente per garantire che il traffico rimanga nell'area.

## <a name="next-steps"></a>Passaggi successivi

* Per informazioni sui passaggi per configurare il peering diretto con Microsoft, seguire la [procedura dettagliata relativa al peering diretto](walkthrough-direct-all.md).
* Per informazioni sui passaggi per configurare il peering di Exchange con Microsoft, seguire la [procedura dettagliata del peering di Exchange](walkthrough-exchange-all.md).