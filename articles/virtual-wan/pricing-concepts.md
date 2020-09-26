---
title: Informazioni sui prezzi della rete WAN virtuale
titleSuffix: Azure Virtual WAN
description: Questo articolo descrive le domande sui prezzi comuni della rete WAN virtuale
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: b4025990a1a62351d3971d788558dea8ecb390ba
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91327958"
---
# <a name="about-virtual-wan-pricing"></a>Informazioni sui prezzi della rete WAN virtuale

La rete WAN virtuale di Azure offre più servizi di rete e sicurezza in un framework unificato. Si basa su un'architettura Hub e spoke, in cui gli hub sono gestiti da Microsoft con diversi servizi disponibili all'interno dell'hub, ad esempio VPN, ExpressRoute, VPN utente (da punto a sito), firewall, routing e così via.

Il prezzo di ogni servizio nella rete WAN virtuale è. Pertanto, il suggerimento di un singolo prezzo non è applicabile alla rete WAN virtuale. Il [calcolatore dei prezzi di Azure](https://azure.microsoft.com/pricing/calculator/) fornisce un meccanismo per derivare il costo, basato sui servizi di cui è stato effettuato il provisioning in una rete WAN virtuale. Questo articolo descrive le domande frequenti sui prezzi della rete WAN virtuale.

>[!NOTE]
>Per informazioni aggiornate sui prezzi, vedere [prezzi della rete WAN virtuale](https://azure.microsoft.com/pricing/details/virtual-wan/).
>

## <a name="common-pricing-questions"></a><a name="questions"></a>Domande comuni sui prezzi

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>Che cos'è un'unità di scala?

Un' **unità di scala** fornisce l'unità per la capacità aggregata di da sito a sito (S2S), da punto a sito (P2S) e EXPRESSROUTE (ER) in un hub virtuale. Ad esempio:

* **1 l'unità di scala VPN S2S** implica una capacità totale del gateway vpn a 500 Mbps (le istanze duali vengono distribuite per la resilienza) in un hub virtuale che costa $0.361 all'ora.
* **1 unità di scala er** comporta un totale di 2 Gbps del gateway er nell'hub virtuale che costa $0.42/HR.
* **5 unità di scala er** potrebbero implicare un totale di 10 Gbps per il gateway er all'interno di un hub virtuale VNet a prezzo di $0.42 * 5/hr. ER incrementa $0,25/hr dall'unità di scala da 6 a 10.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>Che cos'è un'unità di connessione?

Un' **unità di connessione** si applica a qualsiasi endpoint locale o non Microsoft che si connette ai gateway di Azure. Per la VPN da sito a sito, questo implica i rami. Per la VPN utente (da punto a sito), ciò implica che gli utenti remoti. Per ExpressRoute, questo implica le connessioni del circuito ExpressRoute.<br>Ad esempio:

* Una connessione di Branch connessa alla VPN di Azure in un hub virtuale costa $0,05/HR. Di conseguenza, 100 connessioni ramo a un hub virtuale di Azure costeranno $0,05 * 100/hr.

* Due connessioni del circuito ExpressRoute che si connettono a un hub virtuale costano $0,05 * 2/hr.

* Tre connessioni utente remote che si connettono al gateway P2S dell'hub virtuale di Azure avranno un costo di $0.03 * 3/hr.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>In che modo vengono calcolati i prezzi di trasferimento dati?

* Il traffico che entra in Azure non viene addebitato. Il traffico in uscita da Azure (tramite VPN, ExpressRoute o connessioni VPN da punto a sito) è soggetto agli [addebiti per il trasferimento dati di Azure](https://azure.microsoft.com/pricing/details/bandwidth/)standard.

* Per gli addebiti per il trasferimento dei dati tra un hub WAN virtuale e un hub WAN virtuale remoto o VNet in un'area diversa da quella dell'hub di origine, vengono applicati addebiti per il trasferimento dei dati per il traffico che lascia un hub. Esempio: il traffico in uscita da un hub degli Stati Uniti orientali verrà addebitato da $0,02/GB a un hub degli Stati Uniti occidentali. Non sono previsti addebiti per il traffico che entra nell'hub Stati Uniti occidentali. Le tabelle seguenti illustrano gli addebiti.

Le tabelle seguenti usano le abbreviazioni seguenti: {NAM: America del Nord}, {EU: Europe}, {MEA: Middle East Africa}, {OC: Oceania (Australia centrale e Australia Central 2)}, {LATAt: America Latina} 

**Prezzi intra-continente (*)**

| Intra-continente| Prezzo ($/GB)|
|---|---|
| Da NAM a NAM|$0,02 |
| Da UE a UE |$0,02 |
| ASIA-ASIA (esclusa la Cina)|$0,10 |
| Da MEA a MEA|$0,16 |
| LATA-LATA |$0,16 |
| OC-OC|$0,12 |

**Prezzi tra i continentali (*)**

| Inter-Continental| Prezzo ($/GB)|
|---|---|
| DA NAM a UE o da UE a NAM |$ 0,07 |
| DA LATAt a qualsiasi posizione |$0,17 |
| DA MEA a Anywhere |$0,17 |
| DA OCEANIA a ovunque |$0,12 |
| DALL'ASIA (ad eccezione della Cina) a qualsiasi posizione |$0,12 |

(*) Potrebbero essere applicati addebiti a partire dal 1 ° agosto 2020.

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Qual è la differenza tra una tariffa dell'hub standard e una tariffa di elaborazione dell'hub standard?

La rete WAN virtuale è in due tipi:

* Una **rete WAN virtuale di base**, in cui gli utenti possono distribuire più hub e usufruire della connettività VPN da sito a sito. Una rete WAN virtuale di base non ha funzionalità avanzate, ad esempio hub completamente mesh, connettività ExpressRoute, connettività VPN da punto a sito, VPN da punto a sito, connettività VNet-VNet, connettività di transito VPN e ExpressRoute, firewall di Azure e così via. Non è prevista una tariffa di base o una tariffa per l'elaborazione dei dati per gli hub in una rete WAN virtuale di base.

* Una **rete WAN virtuale standard** offre funzionalità avanzate, ad esempio hub completamente mesh, connettività ExpressRoute, connettività VPN da punto a sito, VPN da punto a sito, connettività VNet a VNet, connettività di transito VPN e ExpressRoute, firewall di Azure e così via. Tutto il routing dell'hub virtuale viene fornito da un router che Abilita più servizi in un hub virtuale. È prevista una tariffa di base per l'hub, il cui prezzo è di $0,25/hr. È anche previsto un addebito per l'elaborazione dei dati nel router dell'hub virtuale per la connettività di transito da VNet a VNet. Vedere la figura seguente.

 Nella figura di **esempio** seguente sono disponibili due reti virtuali, VNET 1 e VNET 2. Si supponga che siano presenti 1 Gbps di dati inviati da una macchina virtuale in VNET 1 a un'altra VM in VNET 2. Si applicano gli addebiti seguenti:

* Tariffa di base dell'hub virtuale $0,25/hr

* Tariffa per l'elaborazione dei dati dell'hub virtuale di $0.02/GB per 1 Gbps

**Esempio**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="Esempio":::

## <a name="next-steps"></a>Passaggi successivi

* Per altre informazioni sulla rete WAN virtuale, vedere le [domande frequenti](virtual-wan-faq.md).

* Per i prezzi correnti, vedere [prezzi della rete WAN virtuale](https://azure.microsoft.com/pricing/details/virtual-wan/).
