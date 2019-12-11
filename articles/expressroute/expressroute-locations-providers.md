---
title: 'Località e provider di connettività: Azure ExpressRoute | Documentazione Microsoft'
description: Questo articolo fornisce una panoramica dettagliata delle località in cui vengono offerti i servizi e informazioni su come connettersi alle aree di Azure. Ordinati in base alla località.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: cherylmc
ms.openlocfilehash: 71a0d18669ea2c5fc9e0548e195c3df0145f6cb0
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/10/2019
ms.locfileid: "74967797"
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

| **Area geopolitica** | **Aree di Azure** | **Località per ExpressRoute** |
| --- | --- | --- |
| **Australia Government** | Australia centrale, Australia centrale 2 |Canberra, Canberra2 |
| **Europa** | Francia centrale, Francia meridionale, Europa settentrionale, Europa occidentale, Regno Unito occidentale, Regno Unito meridionale |Amsterdam, Amsterdam2, Copenaghen, Dublino, Francoforte, Ginevra, Londra, London2, Marsiglia, Milano, Monaco, Newport (Galles), Oslo, Parigi, Stavanger, Stoccolma, Zurigo, Monaco |
| **America del Nord** | Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti orientali 2, Stati Uniti occidentali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Stati Uniti centro-occidentali, Canada centrale, Canada orientale |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto |
| **Asia** | Asia orientale, Asia sudorientale |RAS di Hong Kong, Giacarta, Kuala Lumpur, Singapore, Singapore2, Taipei |
| **India** | India occidentale, India centrale, India Meridionale |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Giappone** | Giappone occidentale, Giappone orientale |Osaka, Tokyo |
| **Oceania** | Australia sudorientale, Australia orientale |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Corea del Sud** | Corea del Sud centrale, Corea del Sud meridionale |Busan, Seoul|
| **Emirati Arabi Uniti** | Emirati Arabi Uniti centrali, Emirati Arabi Uniti settentrionali | Dubai, Dubai2 |
| **Sud Africa** | Sudafrica occidentale, Sudafrica settentrionale |Città del Capo, Johannesburg |
| **America del Sud** | Brasile meridionale |Sao Paulo |

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

* Le **aree di Azure locali** sono quelle che possono accedere a [ExpressRoute locale](expressroute-faqs.md) a ogni località di peering. **n/a** indica che ExpressRoute local non è disponibile in tale posizione di peering.

* La **zona** si riferisce ai [prezzi](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Azure commerciale globale
| **Località** | **Indirizzo** | **Zona** | **Aree di Azure locali** | **ER diretto** | **Provider di servizi** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [AM5 Equinix](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Europa occidentale | 10G, 100G | Aryaka Networks, AT & T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, intercloud, Interxion, KPN, IX REACH, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Telefónica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [AMS8 Interxion](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Europa occidentale | 10G, 100G | CenturyLink Cloud Connect, Colt, DE-CIX, euNetworks, Interxion, Orange, Vodafone |
| **Atlanta** | [AT2 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | N/D | N/D | Equinix, Megaport |
| **Auckland** | [Gruppo Vocus NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | N/D | 10G | Devoli, Kordia, Megaport, Spark NZ, Vocus Group NZ |
| **Busan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Corea meridionale | N/D | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australia centrale | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australia centrale 2| 10G, 100G | CDC |
| **Città del Capo** | [CT1 teraco](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Sudafrica occidentale | 10G | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | India meridionale | 10G | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | India meridionale | N/D | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | Stati Uniti centro-settentrionali | 10G, 100G | Aryaka Networks, AT & T NetBond, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, intercloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Copenaghen** | [CPH1 Interxion](https://www.interxion.com/Locations/copenhagen/) | 1 | N/D | 10G | Interxion |
| **Dallas** | [DA3 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | N/D | 10G, 100G | Aryaka Networks, AT & T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutronia Networks, Telmex uninet, Telia Carrier, transtelco, Verizon, Zayo|
| **Denver** | [DE1 CoreSite](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | Stati Uniti centro-occidentali | N/D | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Emirati Arabi Uniti settentrionali | N/D | Etisalat UAE |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Emirati Arabi Uniti settentrionali | N/D | du datamena, Megaport, Orixcom |
| **Dublino** | [DB3 Equinix](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Europa settentrionale | 10G, 100G | Colt, eir, Equinix, Interxion, Megaport |
| **Francoforte** | [FRA11 Interxion](https://www.interxion.com/Locations/frankfurt/) | 1 | Germania centro-occidentale | 10G, 100G | Colt, DE-CIX, GEANT, Interxion, Orange, Telia Carrier |
| **Geneva** | [GV2 Equinix](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Svizzera occidentale | 10G, 100G | |
| **RAS di Hong Kong** | [HK1 Equinix](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Asia orientale | N/D | Reti Aryaka, British Telecom, CenturyLink Cloud Connect, Chief Telecom, Cina Telecom Global, Equinix, intercloud, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Giacarta** | Telkom Indonesia | 4 | N/D | 10G | |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Sudafrica settentrionale | N/D | British Telecom, soluzioni Internet-Cloud Connect, Liquid Telecom, Orange, teraco |
| **Kuala Lumpur** | [TEMPO per la dotCom](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | N/D | N/D | TIME dotCom |
| **Las Vegas** | [Switch LV](https://www.switch.com/las-vegas) | 1 | N/D | N/D | CenturyLink Cloud Connect, Megaport |
| **Londra** | [LD5 Equinix](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Regno Unito meridionale | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Telecamera North Two](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | 1 | Regno Unito meridionale | 10G, 100G | Colt, IX REACH, Equinix, Megaport, Telehouse-KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | N/D | N/D | CoreSite, Equinix, Megaport, Neutronia Networks, NTT, transtelco, Zayo |
| **Marsiglia** |[MRS1 Interxion](https://www.interxion.com/Locations/marseille/) | 1 | Francia meridionale | N/D | DE-CIX, GEANT, Interxion, Jaguar Network |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Australia sud-orientale | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [MI1 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | N/D | 10G | C3ntro, Equinix, Megaport, reti neutre |
| **Milano** | [IRIDEos](https://irideos.it/en/data-centers/) | 1 | N/D | 10G | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | N/D | N/D | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | India occidentale | N/D | Global CloudXchange (GCX), Reliance jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | India occidentale | N/D | Airtel, Sify, Vodafone Idea |
| **Monaco** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/) | 1 | N/D | 10G, 100G | |
| **New York** | [NY9 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | N/D | N/D | CenturyLink Cloud Connect, Colt, Coresite, Equinix, intercloud, Megaport, Packet, Zayo |
| **Newport(Wales)** | [Dati di nuova generazione](https://www.nextgenerationdata.co.uk) | 1 | Regno Unito occidentale | N/D | British Telecom, Colt, livello 3 Communications, dati di nuova generazione |
| **Osaka** | [OS1 Equinix](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Giappone occidentale | N/D | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [Ulven DigiPlex](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Norvegia orientale | 10G, 100G | Telenor |
| **Parigi** | [PAR5 Interxion](https://www.interxion.com/Locations/paris/) | 1 | Francia centrale | N/D | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | N/D | 10G | Megaport, NextDC |
| **Quebec City** | [Privilegiati](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Canada orientale | N/D | Bell Canada, Megaport |
| **San Antonio** | [SA1 CyrusOne](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | Stati Uniti centro-meridionali | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brasile meridionale | N/D | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [SE2 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | Stati Uniti occidentali 2 | 10G, 100G | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Seoul** | [IDC KINX Gasan](https://www.kinx.net/support/location/?lang=en) | 2 | Corea centrale | 10G, 100G | KINX, KT, LG snc, Sejong Telecom |
| **Silicon Valley** | [SV1 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | Stati Uniti occidentali | 10G, 100G | Aryaka Networks, AT & T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, intercloud, Internet2, IX REACH, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Valley2 Silicon** | [SV7 Coresite](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | Stati Uniti occidentali | 10G, 100G | Colt, Coresite | 
| **Singapore** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Asia sud-orientale | 10G, 100G | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Switch globale Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Asia sud-orientale | 10G, 100G | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Stavanger** | [Green Mountain DC1](https://greenmountain.no/dc1-stavanger/) | 1 | N/D | 10G, 100G | |
| **Stoccolma** | [SK1 Equinix](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | N/D | 10G | Equinix, Telia Carrier |
| **Sydney** | [SY2 Equinix](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Australia orientale | 10G, 100G | AARNet, AT & T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Australia orientale | 10G, 100G | NextDC |
| **Taipei** | Chief Telecom | 2 | N/D | 10G | Telecom principale, FarEasTone |
| **Tokyo** | [TY4 Equinix](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Giappone orientale | 10G, 100G | Aryaka Networks, AT & T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Giappone Inc.-IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Canada centrale | 10G, 100G | AL & T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX REACH Megaport, Telus, Verizon, Zayo |
| **Washington DC** | [DC2 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Stati Uniti orientali, Stati Uniti orientali 2 | 10G, 100G | Aryaka Networks, AT & T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, Internet2, intercloud, Level 3 Communications, Megaport, Neutronia Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, tata Comunicazioni, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Stati Uniti orientali, Stati Uniti orientali 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Viasat, Zayo | 
| **Zurigo** | [ZUR2 Interxion](https://www.interxion.com/Locations/zurich/) | 1 | N/D | N/D | Intercloud, Interxion, Megaport, Swisscom |

 **+** indica disponibile a breve

### <a name="national-cloud-environments"></a>Ambienti cloud nazionali

I cloud nazionali di Azure sono isolati tra loro e da Azure commerciale globale. ExpressRoute per un cloud di Azure non possono connettersi alle aree di Azure negli altri.

### <a name="us-government-cloud"></a>Cloud del governo degli Stati Uniti
| **Località** | **Indirizzo** | **Aree di Azure locali**| **ER diretto** | **Provider di servizi** |
| --- | --- | --- | --- | --- |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | N/D | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [DA3 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | N/D | 10G, 100G | Equinix, Megaport, Verizon |
| **New York** | [NY5 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | N/D | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov Arizona | N/D | ALL'& T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [SA2 CyrusOne](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov Texas | N/D | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [SV4 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | N/D | 10G, 100G | Equinix, Level 3 Communications, Verizon |
| **Seattle** | [SE2 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | N/D | N/D | Equinix, Megaport |
| **Washington DC** | [DC2 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD (area orientale), US Gov Virginia | 10G, 100G | ALLE & T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Cina
| **Località** | **Provider di servizi** |
| --- | --- |
| **Pechino** |China Telecom |
| **Beijing2** | Telecom per la Cina, GDS |
| **Shanghai** |China Telecom |
| **Shanghai2** | Telecom per la Cina, GDS |

Per altre informazioni, vedere [ExpressRoute in Cina](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Germania
| **Località** | **Provider di servizi** |
| --- | --- |
| **Berlino** |e-shelter, Megaport+, T-Systems |
| **Francoforte** |Colt, Equinix, Interxion |

## <a name="c1partners"></a>Connettività tramite provider di Exchange
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

## <a name="connectivity-through-satellite-operators"></a>Connettività tramite operatori satellite
Se si è in modalità remota e non si dispone di connettività Fiber o si desidera esplorare altre opzioni di connettività, è possibile controllare gli operatori satellite seguenti. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="c1partners"></a>Connettività tramite provider di servizi aggiuntivi
| **Località** | **Exchange** | **Provider di connettività** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, livello 3 comunicazioni | BICS, CloudXpress, EuroFiber, Fastweb S. p. A, Gulf Bridge International, Kalaam Telecom Bahrain B. S. C, MainOne, Nianet, POST Telecom Luxembourg, vicinal, TDC erhverv, Telecom Italia Spark, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Castello corona
| **Città del Capo** | Teraco | MTN |
| **Chicago** | Equinix| Castello corona, Spectrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, data Foundry, Spectrum Enterprise, transtelco |
| **Francoforte** | Interxion | BICS, Cinia, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **RAS di Hong Kong** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londra** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, esponenziale E, HSO, NexGen Networks, Prossimaus, Tamars Telecom, Zain |
| **Los Angeles** | Equinix |Crown Castle, Spectrum Enterprise, transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix, Equinix | Tecnologie di controllo delle tecnologie, Inc. aptum Technologies, Rogers, zirro |
| **New York** |Equinix, Megaport | Altice business, Crown Castle, Spectrum Enterprise, Webair |
| **Parigi** | Equinix | Proximus |
| **Quebec City** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Singapore** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Transgate Technologies Inc., overanfield Metroconnect, aptum Technologies, IVedha Inc, Rogers, Thinktel, zirro|
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
