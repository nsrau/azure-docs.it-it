---
title: 'Provider di connettività e posizione: Azure ExpressRoute | Microsoft Docs'
description: Questo articolo fornisce una panoramica dettagliata delle località in cui vengono offerti i servizi e informazioni su come connettersi alle aree di Azure. Ordinati in base alla località.
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: ''
ms.assetid: feb67da3-5abc-4acb-bad4-f78e3c541ded
ms.service: expressroute
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/09/2019
ms.author: cherylmc
ms.openlocfilehash: bd0ed971b84baae3b85badfcc669bbad657d1551
ms.sourcegitcommit: b12a25fc93559820cd9c925f9d0766d6a8963703
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69018942"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partner e località di peering per ExpressRoute

> [!div class="op_single_selector"]
> * [Località per provider](expressroute-locations.md)
> * [Provider per località](expressroute-locations-providers.md)


Le tabelle riportate in questo articolo forniscono informazioni su località e copertura geografica ExpressRoute, provider di connettività ExpressRoute e ExpressRoute System Integrator (SIs).

> [!Note]
> Le aree di Azure e le località ExpressRoute sono due concetti distinti e diversi. la comprensione della differenza tra i due è fondamentale per l'esplorazione della connettività di rete ibrida di Azure. 
>
>

## <a name="azure-regions"></a>Aree di Azure
Le aree di Azure sono centri dati globali in cui si trovano risorse di calcolo, di rete e di archiviazione di Azure. Quando si crea una risorsa di Azure, un cliente deve selezionare un percorso di risorsa. Il percorso della risorsa determina quale data center di Azure (o zona di disponibilità) in cui viene creata la risorsa.

## <a name="expressroute-locations"></a>Località ExpressRoute
Le località ExpressRoute (talvolta denominate località di peering o Meet-me-locations) sono strutture di condivisione percorso in cui si trovano i dispositivi Microsoft Enterprise Edge (MSEE). Le località ExpressRoute rappresentano il punto di ingresso della rete Microsoft e vengono distribuite a livello globale, offrendo ai clienti la possibilità di connettersi alla rete di Microsoft in tutto il mondo. Questi percorsi rappresentano i casi in cui i partner ExpressRoute e i clienti di ExpressRoute Direct inviano connessioni incrociate alla rete Microsoft. In generale, non è necessario che la località ExpressRoute corrisponda all'area di Azure. Ad esempio, un cliente può creare un circuito ExpressRoute con la posizione della risorsa *Stati Uniti orientali*, nella località di peering di *Seattle* .

Si otterrà l'accesso al servizio di Azure in tutte le aree di un'area geopolitica se è stata effettuata la connessione ad almeno una località per ExpressRoute entro l'area geopolitica. 

## <a name="locations"></a>Aree di Azure per ExpressRoute le località all'interno di un'area geopolitica
La tabella seguente fornisce una mappa di aree di Azure e di località ExpressRoute in un'area geopolitica.

| **Area geopolitica** | **Zona** | **Aree di Azure** | **Località per ExpressRoute** |
| --- | --- | --- | --- |
| **Australia Government** | 1 | Australia centrale, Australia centrale 2 |Canberra, Canberra2 |
| **Europa** | 1 |Francia centrale, Francia meridionale, Europa settentrionale, Europa occidentale, Regno Unito occidentale, Regno Unito meridionale |Amsterdam, Amsterdam2, Copenaghen, Dublino, Francoforte, Londra, London2, Marsiglia, Newport (Galles), Parigi, Stoccolma, Zurigo |
| **America del Nord** | 1 |Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti orientali 2, Stati Uniti occidentali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Stati Uniti centro-occidentali, Canada centrale, Canada orientale |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Asia** | 2 |Asia orientale, Asia sudorientale |Hong Kong SAR, Kuala Lumpur, Singapore, Singapore2, Taipei |
| **India** | 2 |India occidentale, India centrale, India Meridionale |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Giappone** | 2 |Giappone occidentale, Giappone orientale |Osaka, Tokyo |
| **Oceania** | 2 |Australia sudorientale, Australia orientale |Auckland, Melbourne, Perth, Sydney | 
| **Corea del Sud** | 2 |Corea del Sud centrale, Corea del Sud meridionale |Busan, Seoul|
| **EMIRATI ARABI UNITI** | 3 | Emirati Arabi Uniti centrali, Emirati Arabi Uniti settentrionali | Dubai, Dubai2 |
| **Sud Africa** | 3 |Sudafrica occidentale, Sudafrica settentrionale |Città del Capo, Johannesburg |
| **America del Sud** | 3 |Brasile meridionale |San Paolo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Aree di Azure e confini geopolitici per cloud nazionali
Nella tabella seguente vengono fornite informazioni su aree e confini geopolitici per cloud nazionali.

| **Area geopolitica** | **Aree di Azure** | **Località per ExpressRoute** |
| --- | --- | --- |
| **Cloud del governo degli Stati Uniti** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD Central, US DoD East  |Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Cina orientale** |Cina orientale, Cina orientale 2 |Shanghai, Shanghai2 |
| **Cina settentrionale** |Cina settentrionale, Cina settentrionale 2 |Beijing (Pechino), Beijing2 |
| **Germania** |Germania centrale, Germania orientale |Berlino, Francoforte |

La connettività tra diverse aree geopolitiche non è supportata nello standard SKU EspressRoute. È necessario attivare il componente aggiuntivo premium ExpressRoute per supportare la connettività globale. La connettività per ambienti cloud nazionale non è supportata. È possibile utilizzare il provider di connettività in caso di necessità.

## <a name="partners"></a>Provider di connettività ExpressRoute

La tabella seguente mostra le località di connettività e i provider di servizi per ogni località. Per visualizzare i provider di servizi e le località per cui possono fornire il servizio, vedere la tabella delle [località per provider di servizi](expressroute-locations.md). 

Le **aree di Azure locali** sono quelle che possono accedere a [ExpressRoute locale](expressroute-faqs.md) a ogni località di peering. **n/a** indica che ExpressRoute local non è disponibile in tale posizione di peering.


### <a name="production-azure"></a>Produzione Azure
| **Location** | **Proprietario della località peer** | **Aree di Azure locali** | **Provider di servizi** |
| --- | --- | --- | --- |
| **Amsterdam** | Equinix | Europa occidentale | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, TeleCity Group, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | Interxion | Europa occidentale | CenturyLink Cloud Connect, DE-CIX, Interxion, Vodafone |
| **Atlanta** | Equinix | n/d | Equinix, Megaport |
| **Auckland** | Gruppo Vocus NZ | n/d | Devoli, Kordia, Megaport, Spark NZ, Vocus Group NZ |
| **Busan** |LG CNS | Corea del Sud meridionale | LG CNS |
| **Canberra** | CDC | Australia centrale | CDC |
| **Canberra2** | CDC | Australia centrale 2| CDC |
| **Città del Capo** | Teraco | Sudafrica occidentale | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | India meridionale | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | India meridionale | Airtel |
| **Chicago** | Equinix | Stati Uniti centro-settentrionali | Aryaka Networks, AT & T NetBond, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, intercloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Copenaghen** | Interxion | n/d | Interxion |
| **Dallas** | Equinix | n/d | Aryaka Networks, AT & T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutronia Networks, Telmex uninet, Telia Carrier, transtelco, Verizon, Zayo|
| **Denver** | CoreSite | Stati Uniti centro-occidentali | CoreSite, Megaport, Zayo |
| **Dubai** | Etisalat UAE | Emirati Arabi Uniti settentrionali | Etisalat UAE |
| **Dubai2** | du datamena | Emirati Arabi Uniti settentrionali | du datamena, Orixcom |
| **Dublino** | Equinix | Europa settentrionale | Colt, eir, Equinix, Interxion, Megaport |
| **Francoforte** | Interxion | n/d | DE-CIX, Interxion, arancione |
| **RAS di Hong Kong** | Equinix | Asia orientale | Reti Aryaka, British Telecom, CenturyLink Cloud Connect, Chief Telecom, Cina Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Johannesburg** | Teraco | Sudafrica settentrionale | British Telecom, soluzioni Internet-Cloud Connect, Liquid Telecom, teraco |
| **Kuala Lumpur** | TIME dotCom | n/d | TIME dotCom |
| **Las Vegas** | Opzione | n/d | CenturyLink Cloud Connect, Megaport |
| **Londra** | Equinix | Regno Unito meridionale | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | Telehouse | Regno Unito meridionale | IX REACH, Equinix |
| **Los Angeles** | CoreSite | n/d | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marsiglia** |Interxion | Francia meridionale | DE-CIX, Interxion, Jaguar Network |
| **Melbourne** | NextDC | Australia sud-orientale | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | Equinix | n/d | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | Cologix | n/d | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | India occidentale | Global CloudXchange (GCX), Reliance jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | India occidentale | Airtel, Sify, Vodafone Idea |
| **New York** | Equinix | n/d | CenturyLink Cloud Connect, Coresite, Equinix, intercloud, Megaport, Packet, Zayo |
| **Newport(Wales)** | Dati di nuova generazione | Regno Unito occidentale | British Telecom, Colt, livello 3 Communications, dati di nuova generazione |
| **Osaka** | Equinix | Giappone occidentale | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Parigi** | Interxion | Francia centrale | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | NextDC | n/d | Megaport, NextDC |
| **Quebec City** | 4Degrees | Canada orientale | Bell Canada, Megaport |
| **San Antonio** | CyrusOne | Stati Uniti centro-meridionali | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | Equinix | Brasile meridionale | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | Equinix | Stati Uniti occidentali 2 | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Seoul** | KINX | Corea del Sud centrale | KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** | Equinix | Stati Uniti occidentali | Aryaka Networks, AT & T NetBond, British Telecom, CenturyLink Cloud Connect, Comcast, Coresite, Equinix, intercloud, Internet2, IX REACH, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Valley2 Silicon** | Coresite | Stati Uniti occidentali | Coresite | 
| **Singapore** | Equinix | Asia sud-orientale | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | Global Switch | Asia sud-orientale | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Stoccolma** | Equinix | n/d | Gestore Telia |
| **Sydney** | Equinix | Australia orientale | AARNet, AT & T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Taipei** | Chief Telecom | n/d | Telecom principale, FarEasTone |
| **Tokyo** | Equinix | Giappone orientale | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | Cologix | Canada centrale | AL & T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX REACH Megaport, Telus, Verizon, Zayo |
| **Washington DC** | Equinix | Stati Uniti orientali, Stati Uniti orientali 2 | Aryaka Networks, AT & T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, Internet2, intercloud, Level 3 Communications, Megaport, Neutronia Networks, NTT Communications, Orange, PacketFabric, Sprint, tata Comunicazioni, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | Coresite | Stati Uniti orientali, Stati Uniti orientali 2 |Coresite | 
| **Zurigo** | Interxion | n/d | Intercloud, Interxion |

 **+** indica disponibile a breve

### <a name="national-cloud-environments"></a>Ambienti cloud nazionali

### <a name="us-government-cloud"></a>Cloud del governo degli Stati Uniti
| **Location** | **Provider di servizi** |
| --- | --- |
| **Chicago** |AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** |Equinix, Megaport, Verizon |
| **New York** |Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | ALL'& T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | Equinix, Level 3 Communications, Verizon |
| **Seattle** | Equinix, Megaport |
| **Washington DC** |ALLE & T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Cina
| **Location** | **Provider di servizi** |
| --- | --- |
| **Pechino** |China Telecom |
| **Beijing2** | Telecom per la Cina, GDS |
| **Shanghai** |China Telecom |
| **Shanghai2** | Telecom per la Cina, GDS |

Per altre informazioni, vedere [ExpressRoute in Cina](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Germania
| **Posizione** | **Provider di servizi** |
| --- | --- |
| **Berlino** |e-shelter, Megaport+, T-Systems |
| **Francoforte** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Connettività con provider di scambio
Se il provider di connettività non è incluso nelle sezioni precedenti, sarà comunque possibile creare una connessione.

* Contattare il provider di connettività per verificare se è connesso a uno qualsiasi degli scambi nella tabella precedente. Per altre informazioni sui servizi offerti dai provider di Exchange, è possibile esaminare i seguenti collegamenti. Alcuni provider di connettività sono già connessi agli scambi Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/)
  * [NEXTDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/packetcor/microsoft-azure/)
  
* Richiedere al provider di connettività di estendere la rete alla località di peering scelta.
  * Assicurarsi che il provider di connettività estenda la connettività con disponibilità elevata, in modo che non siano presenti singoli punti di errore.
* Ordinare a un circuito ExpressRoute con scambio come provider di connettività di connettersi a Microsoft.
  * Per configurare la connettività, eseguire la procedura illustrata in [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) .

## <a name="c1partners"></a>Connettività con provider di servizi aggiuntivi
| **Location** | **Exchange** | **Provider di connettività** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, livello 3 Communications, Telecity | BICS, CloudXpress, EuroFiber, Fastweb S. p. A, Gulf Bridge International, Kalaam Telecom Bahrain B. S. C, MainOne, Nianet, post, Immediateus, TDC erhverv, Telecom Italia Spark, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Castello corona
| **Città del Capo** | Teraco | MTN |
| **Chicago** | Equinix| Castello corona, Spectrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, data Foundry, Spectrum Enterprise, transtelco |
| **Francoforte** | Interxion, Telecity | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **RAS di Hong Kong** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londra** | BICS, Equinix, euNetworks, Telecity | Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, esponenziale E, HSO, NexGen Networks, Prossimaus, Tamars Telecom, Zain |
| **Los Angeles** | Equinix |Crown Castle, Spectrum Enterprise, transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Cogeco Peer 1, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice business, Crown Castle, Spectrum Enterprise, Webair |
| **Parigi** | Equinix | Proximus |
| **Quebec City** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Singapore** |Equinix |1CLOUDSTAR, BICS, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Transgate Technologies Inc., overanfield Metroconnect, Cogeco peer 1, IVedha Inc, Rogers, Thinktel, zirro|
| **Washington DC** |Equinix | Altice business, BICS, Cox Business, Crown Castle, GTT Communications Inc, Epsilon Telecommunications Limited, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Integratori di sistemi ExpressRoute
L'abilitazione della connettività privata per soddisfare le proprie esigenze può risultare complessa, in base alle dimensioni della rete. Per ottenere supporto per l'onboarding in ExpressRoute, è possibile collaborare con uno degli Integratori di sistemi elencati nella seguente tabella.

| **Continente** | **Integratori di sistemi** |
| --- | --- |
| **Asia** |Avanade Inc., OneAs1a |
| **Australia** | Ensyst, IT Consultancy, MOQdigital, Vigilant.IT |
| **Europa** |Avanade Inc., Altogee, Bright Skies GmbH, Inframon, MSG Services, New Signature, Nelite, Orange Networks, sol-tec |
| **America del Nord** |Avanade Inc., Equinix Professional Services, FlexManage, Lightstream, Perficient, Presidio |
| **America del Sud** |Avanade Inc., Venha Pra Nuvem |
## <a name="next-steps"></a>Passaggi successivi
* Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
* Verificare che vengano soddisfatti tutti i prerequisiti. Vedere [Prerequisiti per ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mappa delle località"
