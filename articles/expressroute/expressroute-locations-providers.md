---
title: 'Località e provider di connettività: Azure ExpressRoute | Documentazione Microsoft'
description: Questo articolo fornisce una panoramica dettagliata delle località in cui vengono offerti i servizi e informazioni su come connettersi alle aree di Azure. Ordinati in base alla località.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: cherylmc
ms.openlocfilehash: 0133322eab9a554cf28c0a90fbadf9afe6fbcd64
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81314275"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partner e località di peering per ExpressRoute

> [!div class="op_single_selector"]
> * [Posizioni per provider](expressroute-locations.md)
> * [Provider per posizione](expressroute-locations-providers.md)


Le tabelle in questo articolo forniscono informazioni sulla copertura geografica e le posizioni di ExpressRoute, sui provider di connettività ExpressRoute e sui provider di connettività ExpressRoute System Integrator (SIs).

> [!Note]
> Le aree di Azure e le posizioni ExpressRoute hanno due concetti distinti e diversi, la comprensione della differenza tra i due è fondamentale per esplorare la connettività di rete ibrida di Azure.Azure regions and ExpressRoute locations are two distinct and different concepts, understanding the difference between the two is critical to exploring Azure hybrid networking connectivity. 
>
>

## <a name="azure-regions"></a>Aree di Azure
Le aree di Azure sono data center globali in cui si trovano le risorse di elaborazione, rete e archiviazione di Azure.Azure regions are global datacenters where Azure compute, networking and storage resources are located. Quando si crea una risorsa di Azure, un cliente deve selezionare una posizione della risorsa. La posizione della risorsa determina in quale data center di Azure (o zona di disponibilità) viene creata la risorsa.

## <a name="expressroute-locations"></a>Posizioni ExpressRouteExpressRoute locations
Le posizioni ExpressRoute (talvolta definite posizioni di peering o meet-me-location) sono strutture di co-location in cui si trovano i dispositivi Microsoft Enterprise Edge (MSEE). Le posizioni ExpressRoute sono il punto di ingresso alla rete Microsoft e sono distribuite a livello globale, offrendo ai clienti l'opportunità di connettersi alla rete Microsoft in tutto il mondo. Questi percorsi sono dove i partner ExpressRoute e i clienti diretti ExpressRoute emettono connessioni incrociate alla rete microsoft. In generale, non è necessario che il percorso ExpressRoute corrisponda all'area di Azure.In general, the ExpressRoute location does not need to match the Azure region. Ad esempio, un cliente può creare un circuito ExpressRoute con il percorso della risorsa *Stati Uniti orientali,* nella posizione di Seattle Peering.For example, a customer can create an ExpressRoute circuit with the resource location East US , in the *Seattle* Peering location.

Si otterrà l'accesso al servizio di Azure in tutte le aree di un'area geopolitica se è stata effettuata la connessione ad almeno una località per ExpressRoute entro l'area geopolitica. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Aree di Azure e località ExpressRoute in un'area geopolitica
La tabella seguente fornisce una mappa di aree di Azure e di località ExpressRoute in un'area geopolitica.

| **Area geopolitica** | **Aree di AzureAzure regions** | **Posizioni ExpressRouteExpressRoute locations** |
| --- | --- | --- |
| **Australia Government** | Australia centrale, Australia centrale 2 |Canberra, Canberra2 |
| **Europa** | Francia Centrale, Francia Meridionale, Germania Nord, Germania Centro Ovest, Europa Settentrionale, Norvegia Est, Norvegia Ovest, Svizzera Nord, Svizzera Ovest, Regno Unito Ovest, Regno Unito Sud, Europa occidentale |Amsterdam, Amsterdam2, Copenaghen, Dublino, Francoforte, Ginevra, Londra, Londra2, Marsiglia, Milano, Monaco, Newport (Galles), Oslo, Parigi, Stavanger, Stoccolma, |
| **America del Nord** | Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti orientali 2, Stati Uniti occidentali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Stati Uniti centro-occidentali, Canada centrale, Canada orientale |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto, Vancouver |
| **Asia** | Asia orientale, Asia sudorientale | Bangkok, Hong Kong, Hong Kong2, Giacarta, Kuala Lumpur, Singapore2, Taipei |
| **India** | India occidentale, India centrale, India Meridionale |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Giappone** | Giappone occidentale, Giappone orientale |Osaka, Tokyo, Tokyo2 |
| **Oceania** | Australia sud-orientale, Australia orientale |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Corea del Sud** | Corea centrale, Corea meridionale |Busan, Seoul|
| **Emirati Arabi Uniti** | Emirati Arabi Uniti Centrale, Emirati Arabi Uniti Nord | Dubai, Dubai2 |
| **Sud Africa** | Sud Africa Occidentale, Sud Africa Nord |Città del Capo, Johannesburg |
| **America del Sud** | Brasile meridionale |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Aree di Azure e limiti geopolitici per i cloud nazionaliAzure regions and geopolitical boundaries for national clouds
Nella tabella seguente vengono fornite informazioni su aree e confini geopolitici per cloud nazionali.

| **Area geopolitica** | **Aree di AzureAzure regions** | **Posizioni ExpressRouteExpressRoute locations** |
| --- | --- | --- |
| **Cloud del US Gov** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD (area centrale), US DoD (area orientale)  |Atlanta,Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Cina orientale** |Cina orientale, Cina orientale 2 |Shanghai, Shanghai2 |
| **Cina settentrionale** |Cina settentrionale, Cina settentrionale 2 |Beijing (Pechino), Beijing2 |
| **Germania** |Germania centrale, Germania orientale |Berlino, Francoforte |

La connettività tra diverse aree geopolitiche non è supportata nello standard SKU EspressRoute. È necessario attivare il componente aggiuntivo premium ExpressRoute per supportare la connettività globale. La connettività per ambienti cloud nazionale non è supportata. È possibile utilizzare il provider di connettività in caso di necessità.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Provider di connettività ExpressRoute

La tabella seguente mostra le località di connettività e i provider di servizi per ogni località. Per visualizzare i provider di servizi e le località per cui possono fornire il servizio, vedere la tabella delle [località per provider di servizi](expressroute-locations.md).

* **Le aree di Azure locali** sono quelle a cui può accedere [ExpressRoute Local](expressroute-faqs.md) in ogni posizione di peering. **n/a** indica che ExpressRoute Local non è disponibile nel percorso di peering.

* **La zona** si riferisce [ai prezzi](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Azure commerciale globale
| **Posizione** | **Indirizzo** | **Zona** | **Aree di Azure localiLocal Azure regions** | **ER Diretto** | **Fornitori** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Europa occidentale | 10G, 100G | Reti di Aryaka, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GàANT, InterCloud, Interxion, KPN, IX Reach, Livello 3 Comunicazioni, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, |
| **Amsterdam2** | [Interxione AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Europa occidentale | 10G, 100G | CenturyLink Cloud Connect, Colt, DE-CIX, euNetworks, Interxion, Orange, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n/d | n/d | Equinix, Megaport |
| **Auckland** | [Vocus Group N. Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n/d | 10G (in questo stato del maligno | Devoli, Kordia, Megaport, Spark N.N., Vocus Group N. |
| **Bangkok** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | n/d | 10G (in questo stato del maligno | AIS |
| **Busan** | [LG CNS](https://www.lgcns.com/datacenter) | 2 | Corea meridionale | n/d | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australia centrale | 10G, 100G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australia centrale 2| 10G, 100G | CDC |
| **Città del Capo** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Sudafrica Ovest | 10G (in questo stato del maligno | BCX, Soluzioni Internet - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | India meridionale | 10G (in questo stato del maligno | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | India meridionale | n/d | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | Stati Uniti centro-settentrionali | 10G, 100G | Reti Aryaka, AT&T NetBond, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, Comunicazione di livello 3, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, |
| **Copenhagen** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n/d | 10G (in questo stato del maligno | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n/d | 10G, 100G | Reti Aryaka, AT&T NetBond, Cologix, Equinix, Internet2, Livello 3 Comunicazioni, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon,|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | Stati Uniti centro-occidentali | n/d | CoreSite, Megaport |
| **Dubai** | [PCC](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Emirati Arabi Uniti settentrionali | n/d | Etisalat Emirati Arabi Uniti |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Emirati Arabi Uniti settentrionali | n/d | du datamena, Megaport, Orange, Orixcom |
| **Dublino** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Europa settentrionale | 10G, 100G | Colt, eir, Equinix, euNetworks, Interxion, Megaport |
| **Francoforte** | [Interxione FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Germania Centro Ovest | 10G, 100G | CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, GEANT, Interxion, Megaport, Orange, Telia Carrier |
| **Ginevra** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Svizzera Ovest | 10G, 100G | Equinix, Megaport |
| **RAS di Hong Kong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Asia orientale | n/d | Reti Aryaka, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, Equinix, InterCloud, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Hong Kong2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | n/d | 10G (in questo stato del maligno | |
| **Giacarta** | Telkom Indonesia | 4 | n/d | 10G (in questo stato del maligno | |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Sudafrica settentrionale | 10G (in questo stato del maligno | British Telecom, Soluzioni Internet - Cloud Connect, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | [TEMPO dotCom Menara AIMS](https://www.aims.com.my/co-location/points-of-presence.html) | 2 | n/d | n/d | TIME dotCom |
| **Las Vegas** | [Interruttore LV](https://www.switch.com/las-vegas) | 1 | n/d | n/d | CenturyLink Cloud Connect, Megaport |
| **Londra** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Regno Unito meridionale | 10G, 100G | AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Telehouse Nord Due](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Regno Unito meridionale | 10G, 100G | CenturyLink Cloud Connect, Colt, IX Reach, Equinix, Megaport, Telehouse - KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n/d | n/d | CoreSite, Equinix, Megaport, Reti Neutrona, NTT, Transtelco |
| **Marsiglia** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Francia meridionale | n/d | DE-CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud Connect |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Australia sud-orientale | 10G, 100G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n/d | 10G (in questo stato del maligno | Reti C3ntro, Equinix, Megaport, Neutrona |
| **Milan** | [IRIDERIDEOS (in stato di IRIDE)](https://irideos.it/en/data-centers/) | 1 | n/d | 10G (in questo stato del maligno | |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n/d | 10G, 100G | Bell Canada, Cologix, Megaport, Telus |
| **Mumbai** | Tata Communications | 2 | India occidentale | n/d | Global CloudXchange (GCX), Reliance Jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | India occidentale | n/d | Airtel, Sify, Vodafone Idea |
| **Monaco** | [EdgeConneX (informazioni in cui](https://www.edgeconnex.com/locations/europe/) | 1 | n/d | 10G, 100G | |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n/d | n/d | CenturyLink Cloud Connect, Colt, Coresite, Equinix, InterCloud, Megaport, Pacchetto, |
| **Newport(Galles)** | [Dati di nuova generazione](https://www.nextgenerationdata.co.uk) | 1 | Regno Unito occidentale | n/d | British Telecom, Colt, Comunicazioni di livello 3, Dati di nuova generazione |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Giappone occidentale | 10G, 100G | Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Norvegia orientale | 10G, 100G | Megaport, Telenor, Telia Carrier |
| **Parigi** | [Interxione PAR5](https://www.interxion.com/Locations/paris/) | 1 | Francia centrale | 10G, 100G | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n/d | 10G (in questo stato del maligno | Megaport |
| **Quebec City** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Canada orientale | n/d | Bell Canada, Megaport |
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | Stati Uniti centro-meridionali | 10G, 100G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brasile meridionale | n/d | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | Stati Uniti occidentali 2 | 10G, 100G | Reti Aryaka, Equinix, Comunicazioni di livello 3, Megaport, Telus |
| **Seoul** | [KINX Gasan IDC](https://www.kinx.net/support/location/?lang=en) | 2 | Corea centrale | 10G, 100G | KINX, KT, LG CNS, Sejong Telecom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | Stati Uniti occidentali | 10G, 100G | Reti Aryaka, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, IX Reach, Packet, PacketFabric, Livello 3 Comunicazioni, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, |
| **Silicon Valley2** | [Sito centrale SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | Stati Uniti occidentali | 10G, 100G | Colt | 
| **Singapore** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Asia sud-orientale | 10G, 100G | Reti Aryaka, AT&T NetBond, British Telecom, China Mobile International, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Interruttore globale Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Asia sud-orientale | 10G, 100G | Cina Unicom Globale, Colt, Epsilon Global Communications, Megaport, SingTel |
| **Stavanger** | [Montagna verde DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Norvegia Ovest | 10G, 100G | |
| **Stoccolma** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n/d | 10G (in questo stato del maligno | Equinix, Telia Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Australia orientale | 10G, 100G | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, COMUNICAZIONI NTT, Optus, Orange, Spark N'N, Telstra Corporation, TPG Telecom, Verizon, Vocus Group N.N. |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Australia orientale | 10G, 100G | Megaport |
| **Taipei** | Chief Telecom | 2 | n/d | 10G (in questo stato del maligno | Capo Telecom, FarEasTone |
| **Tokyo** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Giappone orientale | 10G, 100G | Aryaka Networks, AT&T NetBond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Tokyo2** | [A TOKYO](https://www.attokyo.com/) | 2 | Giappone orientale | 10G, 100G | A TOKYO |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Canada centrale | 10G, 100G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | Cologix | 10G, 100G | |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Stati Uniti Orientali, Stati Uniti Orientali 2 | 10G, 100G | Reti di Aryaka, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, InterCloud, IX Reach, Livello 3 Comunicazioni, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, Tata Communications, Telia Carrier, Verizon, |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Stati Uniti Orientali, Stati Uniti Orientali 2 | 10G, 100G | CenturyLink Cloud Connect, Coresite, Intelsat, Viasat, Zayo | 
| **Zurigo** | [Interxion :UR2](https://www.interxion.com/Locations/zurich/) | 1 | Svizzera Nord | 10G, 100G | Intercloud, Interxion, Megaport, Swisscom |

 **+** denota in arrivo

### <a name="national-cloud-environments"></a>Ambienti cloud nazionali

I cloud nazionali di Azure sono isolati l'uno dall'altro e da Azure commerciale globale. ExpressRoute per un cloud di Azure non può connettersi alle aree di Azure nelle altre.

### <a name="us-government-cloud"></a>Cloud del US Gov
| **Posizione** | **Indirizzo** | **Aree di Azure localiLocal Azure regions**| **ER Diretto** | **Fornitori** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | n/d | 10G, 100G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n/d | 10G, 100G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/d | 10G, 100G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n/d | 10G, 100G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov Arizona | n/d | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov Texas | n/d | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n/d | 10G, 100G | AT&T, Equinix, Livello 3 Comunicazioni, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n/d | n/d | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD East, US Gov Virginia | 10G, 100G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Cina
| **Posizione** | **Fornitori** |
| --- | --- |
| **Pechino** |China Telecom |
| **Beijing2** | China Telecom, China Unicom, GDS |
| **Shanghai** |China Telecom |
| **Shanghai2** | China Telecom, GDS |

Per altre informazioni, vedere [ExpressRoute in CinaTo](http://www.windowsazure.cn/home/features/expressroute/) learn more, see ExpressRoute in China

### <a name="germany"></a>Germania
| **Posizione** | **Fornitori** |
| --- | --- |
| **Berlino** |e-shelter, Megaport+, T-Systems |
| **Francoforte** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Connettività tramite provider di Exchange
Se il provider di connettività non è incluso nelle sezioni precedenti, sarà comunque possibile creare una connessione.

* Contattare il provider di connettività per verificare se è connesso a uno qualsiasi degli scambi nella tabella precedente. Per altre informazioni sui servizi offerti dai provider di Exchange, è possibile esaminare i seguenti collegamenti. Alcuni provider di connettività sono già connessi agli scambi Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [InterXion](https://www.interxion.com/)
  * [NextDC (Paesi in stato di Post](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Richiedere al provider di connettività di estendere la rete alla località di peering scelta.
  * Assicurarsi che il provider di connettività estenda la connettività con disponibilità elevata, in modo che non siano presenti singoli punti di errore.
* Ordinare a un circuito ExpressRoute con scambio come provider di connettività di connettersi a Microsoft.
  * Per configurare la connettività, eseguire la procedura illustrata in [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) .

## <a name="connectivity-through-satellite-operators"></a>Connettività attraverso operatori satellitari
Se sei remoto e non hai connettività in fibra ottica o vuoi esplorare altre opzioni di connettività, puoi controllare i seguenti operatori satellitari. 

* Intelsat
* [Ses](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Connettività tramite provider di servizi aggiuntivi
| **Posizione** | **Exchange** | **Provider di connettività** |
| --- | --- | --- |
| **Amsterdam** | Comunicazioni Equinix, Interxion, Livello 3 | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, Kalaam Telecom Bahrain B.S.C, MainOne, Nianet, POST Telecom Lussemburgo, Proximus, RETN, TDC Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia Deutschland GmbH, Telia Deutschland GmbH, Telia Deutschland GmbH, Telia Deelia |
| **Atlanta** | Equinix| Castello della Corona
| **Città del Capo** | Teraco | MTN |
| **Chicago** | Equinix| Crown Castle, Spectrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, Data Foundry, Spectrum Enterprise, Transtelco |
| **Francoforte** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **RAS di Hong Kong** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londra** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Esponenziale E, HSO, Reti NexGen, Proximus, Tamares Telecom |
| **Los Angeles** | Equinix |Castello della Corona, Impresa dello Spettro, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix| Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Castello della Corona, Spettro Enterprise, Webair |
| **Parigi** | Equinix | Proximus |
| **Quebec City** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Singapore** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, Gtt Communications Inc, Epsilon Telecommunications Limited, Masergy, Windstream |

## <a name="expressroute-system-integrators"></a>Integratori di sistemi ExpressRoute
L'abilitazione della connettività privata per soddisfare le proprie esigenze può risultare complessa, in base alle dimensioni della rete. Per ottenere supporto per l'onboarding in ExpressRoute, è possibile collaborare con uno degli Integratori di sistemi elencati nella seguente tabella.

| **Continent** | **Integratori di sistemi** |
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
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mappa delle posizioni"
