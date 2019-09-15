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
ms.date: 09/12/2019
ms.author: cherylmc
ms.openlocfilehash: 17d1a8e2e98f1b2967b4f2e33e14ee187a765190
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/14/2019
ms.locfileid: "70995729"
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
| **Location** | **Proprietario e indirizzo** | **Aree di Azure locali** | **Provider di servizi** |
| --- | --- | --- | --- |
| **Amsterdam** | [AM5 Equinix](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | Europa occidentale | Aryaka Networks, AT & T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, intercloud, Interxion, KPN, IX REACH, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Telefónica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [AMS8 Interxion](https://www.interxion.com/Locations/amsterdam/schiphol/) | Europa occidentale | CenturyLink Cloud Connect, DE-CIX, Colt, Interxion, Vodafone |
| **Atlanta** | [AT2 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | n/d | Equinix, Megaport |
| **Auckland** | [Gruppo Vocus NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | n/d | Devoli, Kordia, Megaport, Spark NZ, Vocus Group NZ |
| **Busan** | [LG CNS](https://datacenter.lgcns.com/Contents/En/Menu_1/Locations_1.aspx) | Corea del Sud meridionale | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | Australia centrale | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | Australia centrale 2| CDC |
| **Città del Capo** | [CT1 teraco](https://www.teraco.co.za/data-centre-locations/cape-town/) | Sudafrica occidentale | Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | India meridionale | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | India meridionale | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | Stati Uniti centro-settentrionali | Aryaka Networks, AT & T NetBond, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, intercloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Copenaghen** | [CPH1 Interxion](https://www.interxion.com/Locations/copenhagen/) | n/d | Interxion |
| **Dallas** | [DA3 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/d | Aryaka Networks, AT & T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutronia Networks, Telmex uninet, Telia Carrier, transtelco, Verizon, Zayo|
| **Denver** | [DE1 CoreSite](https://www.coresite.com/data-centers/locations/denver/de1) | Stati Uniti centro-occidentali | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | Emirati Arabi Uniti settentrionali | Etisalat UAE |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | Emirati Arabi Uniti settentrionali | du datamena, Orixcom |
| **Dublino** | [DB3 Equinix](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | Europa settentrionale | Colt, eir, Equinix, Interxion, Megaport |
| **Francoforte** | [FRA11 Interxion](https://www.interxion.com/Locations/frankfurt/) | n/d | DE-CIX, Interxion, arancione |
| **RAS di Hong Kong** | [HK1 Equinix](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | Asia orientale | Reti Aryaka, British Telecom, CenturyLink Cloud Connect, Chief Telecom, Cina Telecom Global, Equinix, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | Sudafrica settentrionale | British Telecom, soluzioni Internet-Cloud Connect, Liquid Telecom, Orange, teraco |
| **Kuala Lumpur** | [TEMPO per la dotCom](https://www.aims.com.my/co-location/points-of-presence.html) | n/d | TIME dotCom |
| **Las Vegas** | [Switch LV](https://www.switch.com/las-vegas) | n/d | CenturyLink Cloud Connect, Megaport |
| **Londra** | [LD5 Equinix](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | Regno Unito meridionale | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Telecamera North Two](https://www.telehouse.com/global-data-centers/emea/london-data-centers/telehouse-north-two/) | Regno Unito meridionale | IX REACH, Equinix |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | n/d | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Marsiglia** |[MRS1 Interxion](https://www.interxion.com/Locations/marseille/) | Francia meridionale | DE-CIX, Interxion, Jaguar Network |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | Australia sud-orientale | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [MI1 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | n/d | C3ntro+, Equinix, Megaport, Neutrona Networks |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | n/d | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | India occidentale | Global CloudXchange (GCX), Reliance jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | India occidentale | Airtel, Sify, Vodafone Idea |
| **New York** | [NY9 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | n/d | CenturyLink Cloud Connect, Coresite, Equinix, intercloud, Megaport, Packet, Zayo |
| **Newport(Wales)** | [Dati di nuova generazione](https://www.nextgenerationdata.co.uk) | Regno Unito occidentale | British Telecom, Colt, livello 3 Communications, dati di nuova generazione |
| **Osaka** | [OS1 Equinix](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | Giappone occidentale | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT SmartConnect, Softbank |
| **Parigi** | [PAR5 Interxion](https://www.interxion.com/Locations/paris/) | Francia centrale | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | n/d | Megaport, NextDC |
| **Quebec City** | [Privilegiati](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | Canada orientale | Bell Canada, Megaport |
| **San Antonio** | [SA1 CyrusOne](https://cyrusone.com/locations/texas/san-antonio-texas/) | Stati Uniti centro-meridionali | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | Brasile meridionale | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [SE2 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | Stati Uniti occidentali 2 | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Seoul** | [IDC KINX Gasan](https://www.kinx.net/support/location/?lang=en) | Corea del Sud centrale | KINX, LG CNS, Sejong Telecom |
| **Silicon Valley** | [SV1 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | Stati Uniti occidentali | Aryaka Networks, AT & T NetBond, British Telecom, CenturyLink Cloud Connect, Comcast, Coresite, Equinix, intercloud, Internet2, IX REACH, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Valley2 Silicon** | [SV7 Coresite](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | Stati Uniti occidentali | Coresite | 
| **Singapore** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | Asia sud-orientale | Aryaka Networks, AT&T NetBond, British Telecom, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Switch globale Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | Asia sud-orientale | Colt, Epsilon Global Communications, Megaport, SingTel |
| **Stoccolma** | [SK1 Equinix](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | n/d | Gestore Telia |
| **Sydney** | [SY2 Equinix](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | Australia orientale | AARNet, AT & T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Taipei** | Chief Telecom | n/d | Telecom principale, FarEasTone |
| **Tokyo** | [TY4 Equinix](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | Giappone orientale | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | Canada centrale | AL & T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX REACH Megaport, Telus, Verizon, Zayo |
| **Washington DC** | [DC2 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | Stati Uniti orientali, Stati Uniti orientali 2 | Aryaka Networks, AT & T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Comcast, Coresite, Equinix, Internet2, intercloud, Level 3 Communications, Megaport, Neutronia Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, tata Comunicazioni, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | Stati Uniti orientali, Stati Uniti orientali 2 |Coresite, Viasat, Zayo | 
| **Zurigo** | [ZUR2 Interxion](https://www.interxion.com/Locations/zurich/) | n/d | Intercloud, Interxion |

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
| **Location** | **Provider di servizi** |
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
| **Location** | **Exchange** | **Provider di connettività** |
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
| **Montreal** | Cologix, Equinix | Airgate Technologies, Inc. Tecnologie aptum, Rogers, zirro |
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
