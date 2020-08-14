---
title: 'Provider di connettività e posizione: Azure ExpressRoute | Microsoft Docs'
description: Questo articolo fornisce una panoramica dettagliata delle località in cui vengono offerti i servizi e informazioni su come connettersi alle aree di Azure. Ordinati in base alla località.
services: expressroute
author: cherylmc
ms.service: expressroute
ms.topic: conceptual
ms.date: 08/13/2020
ms.author: cherylmc
ms.openlocfilehash: 73a22efdc43911869c27a15c3f6918be4e35bc38
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/14/2020
ms.locfileid: "88225564"
---
# <a name="expressroute-partners-and-peering-locations"></a>Partner e località di peering per ExpressRoute

> [!div class="op_single_selector"]
> * [Località per provider](expressroute-locations.md)
> * [Provider per località](expressroute-locations-providers.md)


Le tabelle in questo articolo includono informazioni su copertura geografica e posizioni di ExpressRoute, provider di connettività di ExpressRoute e integratori di sistemi ExpressRoute.

> [!Note]
> Le aree di Azure e le posizioni di ExpressRoute sono due concetti distinti e diversi. Comprendere la differenza tra le due è fondamentale per l'esplorazione della connettività di rete ibrida di Azure. 
>
>

## <a name="azure-regions"></a>Aree di Azure
Le aree di Azure sono data center globali in cui si trovano risorse di calcolo, rete e archiviazione di Azure. Quando si crea una risorsa di Azure, un cliente deve selezionare una posizione della risorsa. La posizione della risorsa determina in quale data center o zona di disponibilità di Azure viene creata la risorsa.

## <a name="expressroute-locations"></a>Posizioni di ExpressRoute
Le posizioni di ExpressRoute (talvolta denominate posizioni di peering o Meet-me-location) sono strutture di condivisione percorso in cui si trovano i dispositivi Microsoft Enterprise Edge (MSEE). Le posizioni ExpressRoute rappresentano il punto di ingresso della rete Microsoft e vengono distribuite a livello globale, offrendo ai clienti la possibilità di connettersi alla rete di Microsoft in tutto il mondo. Queste posizioni rappresentano i casi in cui i partner ExpressRoute e i clienti di ExpressRoute Direct inviano connessioni incrociate alla rete Microsoft. In generale, non è necessario che la posizione di ExpressRoute corrisponda all'area di Azure. Ad esempio, un cliente può creare un circuito ExpressRoute con la posizione della risorsa *Stati Uniti orientali* nella posizione di peering *Seattle*.

Si otterrà l'accesso al servizio di Azure in tutte le aree di un'area geopolitica se è stata effettuata la connessione ad almeno una località per ExpressRoute entro l'area geopolitica. 

## <a name="azure-regions-to-expressroute-locations-within-a-geopolitical-region"></a><a name="locations"></a>Aree di Azure e posizioni ExpressRoute in un'area geopolitica
La tabella seguente fornisce una mappa di aree di Azure e di località ExpressRoute in un'area geopolitica.

| **Area geopolitica** | **Aree di Azure** | **Località per ExpressRoute** |
| --- | --- | --- |
| **Australia Government** | Australia centrale, Australia centrale 2 |Canberra, Canberra2 |
| **Europa** | Francia centrale, Francia meridionale, Germania settentrionale, Germania centro-occidentale, Europa settentrionale, Norvegia orientale, Norvegia occidentale, Svizzera settentrionale, Svizzera occidentale, Regno Unito occidentale, Regno Unito meridionale, Europa occidentale |Amsterdam, Amsterdam2, Berlino, Copenaghen, Dublino, Francoforte, Ginevra, Londra, Londra2, Marsiglia, Milano, Monaco di Baviera, Newport (Galles), Oslo, Parigi, Stavanger, Stoccolma, Zurigo |
| **America del Nord** | Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti orientali 2, Stati Uniti occidentali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Stati Uniti centro-occidentali, Canada centrale, Canada orientale |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Los Angeles2, Miami, Minneapolis, New York, Queretaro (Messico), San Antonio, Seattle, Silicon Valley, Silicon Valley2, Washington DC, Washington DC2, Montreal, Quebec City, Toronto, Vancouver |
| **Asia** | Asia orientale, Asia sudorientale | Bangkok, Hong Kong, Hong Kong2, Jakarta, Kuala Lumpur, Singapore, Singapore2, Taipei |
| **India** | India occidentale, India centrale, India Meridionale |Chennai, Chennai2, Mumbai, Mumbai2 |
| **Giappone** | Giappone occidentale, Giappone orientale |Osaka, Tokyo, Tokyo2 |
| **Oceania** | Australia sud-orientale, Australia orientale |Auckland, Melbourne, Perth, Sydney, Sydney2 | 
| **Corea del Sud** | Corea centrale, Corea meridionale |Busan, Seoul|
| **Emirati Arabi Uniti** | Emirati Arabi centrali, Emirati Arabi settentrionali | Dubai, Dubai2 |
| **Sud Africa** | Sudafrica occidentale, Sudafrica settentrionale |Città del Capo, Johannesburg |
| **America del Sud** | Brasile meridionale |Sao Paulo |

## <a name="azure-regions-and-geopolitical-boundaries-for-national-clouds"></a>Aree di Azure e confini geopolitici per cloud nazionali
Nella tabella seguente vengono fornite informazioni su aree e confini geopolitici per cloud nazionali.

| **Area geopolitica** | **Aree di Azure** | **Località per ExpressRoute** |
| --- | --- | --- |
| **Cloud del US Gov** |US Gov Arizona, US Gov Iowa, US Gov Texas, US Gov Virginia, US DoD (area centrale), US DoD (area orientale)  |Atlanta, Chicago, Dallas, New York, Phoenix, San Antonio, Seattle, Silicon Valley, Washington DC |
| **Cina orientale** |Cina orientale, Cina orientale 2 |Shanghai, Shanghai2 |
| **Cina settentrionale** |Cina settentrionale, Cina settentrionale 2 |Beijing (Pechino), Beijing2 |
| **Germania** |Germania centrale, Germania orientale |Berlino, Francoforte |

La connettività tra diverse aree geopolitiche non è supportata nello standard SKU EspressRoute. È necessario attivare il componente aggiuntivo premium ExpressRoute per supportare la connettività globale. La connettività per ambienti cloud nazionale non è supportata. È possibile utilizzare il provider di connettività in caso di necessità.

## <a name="expressroute-connectivity-providers"></a><a name="partners"></a>Provider di connettività ExpressRoute

La tabella seguente mostra le località di connettività e i provider di servizi per ogni località. Per visualizzare i provider di servizi e le località per cui possono fornire il servizio, vedere la tabella delle [località per provider di servizi](expressroute-locations.md).

* **Le aree di Azure locali** sono quelle alle quali può accedere [ExpressRoute Local](expressroute-faqs.md) in ogni posizione di peering. **n/a** indica che ExpressRoute local non è disponibile in quella posizione di peering.

* **Zona** si riferisce ai [prezzi](https://azure.microsoft.com/pricing/details/expressroute/).


### <a name="global-commercial-azure"></a>Azure commerciale globale
| **Posizione** | **Indirizzo** | **Zona** | **Aree di Azure locali** | **ER Direct** | **Provider di servizi** |
| --- | --- | --- | --- | --- | --- |
| **Amsterdam** | [Equinix AM5](https://www.equinix.com/locations/europe-colocation/netherlands-colocation/amsterdam-data-centers/am5/) | 1 | Europa occidentale | 10 G, 100 G | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT, InterCloud, Interxion, KPN, IX Reach, Level 3 Communications, Megaport, NTT Communications, Orange, Tata Communications, Telefonica, Telenor, Telia Carrier, Verizon, Zayo |
| **Amsterdam2** | [Interxion AMS8](https://www.interxion.com/Locations/amsterdam/schiphol/) | 1 | Europa occidentale | 10 G, 100 G | CenturyLink Cloud Connect, Colt, DE-CIX, euNetworks, GÉANT, Interxion, NOS, Orange, Vodafone |
| **Atlanta** | [Equinix AT2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at2/) | 1 | n/d | 10 G, 100 G | Equinix, Megaport |
| **Auckland** | [Vocus Group NZ Albany](https://www.vocus.co.nz/business/cloud-data-centres) | 2 | n/d | 10 G | Devoli, Kordia, Megaport, Spark NZ, Vocus Group NZ |
| **Bangkok** | [AIS](https://business.ais.co.th/solution/en/azure-expressroute.html) | 2 | n/d | 10 G | AIS, UIH |
| **Berlino** | [NTT GDC](https://www.e-shelter.de/en/location/berlin-1-data-center) | 1 | Germania settentrionale | 10 G | NTT Global DataCenters EMEA|
| **Busan** | [LG CNS](https://www.lgcns.com/En/Service/DataCenter) | 2 | Corea meridionale | n/d | LG CNS |
| **Canberra** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australia centrale | 10 G, 100 G | CDC |
| **Canberra2** | [CDC](https://cdcdatacentres.com.au/content/about-cdc) | 1 | Australia centrale 2| 10 G, 100 G | CDC |
| **Città del Capo** | [Teraco CT1](https://www.teraco.co.za/data-centre-locations/cape-town/) | 3 | Sudafrica occidentale | 10 G | BCX, Internet Solutions - Cloud Connect, Liquid Telecom, Teraco |
| **Chennai** | Tata Communications | 2 | India meridionale | 10 G | Global CloudXchange (GCX), SIFY, Tata Communications |
| **Chennai2** | Airtel | 2 | India meridionale | 10 G | Airtel |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | 1 | Stati Uniti centro-settentrionali | 10 G, 100 G | Aryaka Networks, AT&T NetBond, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, Level 3 Communications, Megaport, PacketFabric, PCCW Global Limited, Sprint, Telia Carrier, Verizon, Zayo |
| **Copenhagen** | [Interxion CPH1](https://www.interxion.com/Locations/copenhagen/) | 1 | n/d | 10 G | Interxion |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | 1 | n/d | 10 G, 100 G | Aryaka Networks, AT&T NetBond, Cologix, Equinix, Internet2, Level 3 Communications, Megaport, Neutrona Networks, Telmex Uninet, Telia Carrier, Transtelco, Verizon, Zayo|
| **Denver** | [CoreSite DE1](https://www.coresite.com/data-centers/locations/denver/de1) | 1 | Stati Uniti centro-occidentali | n/d | CoreSite, Megaport, Zayo |
| **Dubai** | [PCCS](https://www.pacificcontrols.net/cloudservices/index.html) | 3 | Emirati Arabi Uniti settentrionali | n/d | Etisalat UAE |
| **Dubai2** | [du datamena](http://datamena.com/solutions/data-centre) | 3 | Emirati Arabi Uniti settentrionali | n/d | DE-CIX, du datamena, Megaport, Orange, Orixcom |
| **Dublino** | [Equinix DB3](https://www.equinix.com/locations/europe-colocation/ireland-colocation/dublin-data-centers/db3/) | 1 | Europa settentrionale | 10 G, 100 G | Colt, EIR, Equinix, GEANT, euNetworks, Interxion, Megaport |
| **Francoforte** | [Interxion FRA11](https://www.interxion.com/Locations/frankfurt/) | 1 | Germania centro-occidentale | 10 G, 100 G | IN&T NetBond, CenturyLink Cloud Connect, Colt, DE-CIX, Equinix, euNetworks, Geant, Interxion, Megaport, Orange, Telia Carrier |
| **Ginevra** | [Equinix GV2](https://www.equinix.com/locations/europe-colocation/switzerland-colocation/geneva-data-centers/gv2/) | 1 | Svizzera occidentale | 10 G, 100 G | Equinix, Megaport |
| **RAS di Hong Kong** | [Equinix HK1](https://www.equinix.com/locations/asia-colocation/hong-kong-colocation/hong-kong-data-center/hk1/) | 2 | Asia orientale | 10 G | Aryaka Networks, British Telecom, CenturyLink Cloud Connect, Chief Telecom, China Telecom Global, Equinix, InterCloud, Megaport, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telia Carrier, Verizon |
| **Hong Kong2** | [MEGA-i](https://www.iadvantage.net/index.php/locations/mega-i) | 2 | Asia orientale | 10 G | China Mobile International, China Telecom globale, PCCW Global Limited, SingTel |
| **Giacarta** | Telkom Indonesia | 4 | n/d | 10 G | |
| **Johannesburg** | [Teraco JB1](https://www.teraco.co.za/data-centre-locations/johannesburg/#jb1) | 3 | Sudafrica settentrionale | 10 G | BCX, British Telecom, Internet Solutions - Cloud Connect, Liquid Telecom, Orange, Teraco |
| **Kuala Lumpur** | [TIME dotCom Menara AIMS](https://www.time.com.my/enterprise/connectivity/direct-cloud) | 2 | n/d | n/d | TIME dotCom |
| **Las Vegas** | [Switch LV](https://www.switch.com/las-vegas) | 1 | n/d | n/d | CenturyLink Cloud Connect, Megaport |
| **Londra** | [Equinix LD5](https://www.equinix.com/locations/europe-colocation/united-kingdom-colocation/london-data-centers/ld5/) | 1 | Regno Unito meridionale | 10 G, 100 G | AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc, Level 3 Communications, Megaport, MTN, NTT Communications, Orange, PCCW Global Limited, Tata Communications, Telehouse - KDDI, Telenor, Telia Carrier, Verizon, Vodafone, Zayo |
| **London2** | [Telehouse North Two](https://www.telehouse.net/data-centres/emea/uk-data-centres/london-data-centres/north-two) | 1 | Regno Unito meridionale | 10 G, 100 G | CenturyLink Cloud Connect, Colt, GTT, IX REACH, Equinix, Megaport, Telehouse-KDDI |
| **Los Angeles** | [CoreSite LA1](https://www.coresite.com/data-centers/locations/los-angeles/one-wilshire) | 1 | n/d | 10 G, 100 G | CoreSite, Equinix, Megaport, Neutrona Networks, NTT, Zayo |
| **Los Angeles2** | [LA1 Equinix](https://www.equinix.com/locations/americas-colocation/united-states-colocation/los-angeles-data-centers/la1/) | 1 | n/d | 10 G, 100 G | Equinix |
| **Marsiglia** |[Interxion MRS1](https://www.interxion.com/Locations/marseille/) | 1 | Francia meridionale | n/d | DE-CIX, GEANT, Interxion, Jaguar Network, Ooredoo Cloud Connect |
| **Melbourne** | [NextDC M1](https://www.nextdc.com/data-centres/m1-melbourne-data-centre) | 2 | Australia sud-orientale | 10 G, 100 G | AARNet, Devoli, Equinix, Megaport, NEXTDC, Optus, Telstra Corporation, TPG Telecom |
| **Miami** | [Equinix MI1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/miami-data-centers/mi1/) | 1 | n/d | 10 G, 100 G | Claro, C3ntro, Equinix, Megaport, Neutrona Networks |
| **Milano** | [IRIDEOS](https://irideos.it/en/data-centers/) | 1 | n/d | 10 G | Equinix, Retelit |
| **Minneapolis** | [MIN1 Cologix](https://www.cologix.com/data-centers/minneapolis/min1/) | 1 | n/d | 10 G, 100 G | Cologix |
| **Montreal** | [Cologix MTL3](https://www.cologix.com/data-centers/montreal/mtl3/) | 1 | n/d | 10 G, 100 G | Bell Canada, Cologix, Megaport, Telus, Zayo |
| **Mumbai** | Tata Communications | 2 | India occidentale | 10 G | DE-CIX, Global CloudXchange (GCX), Reliance jio, Sify, Tata Communications, Verizon |
| **Mumbai2** | Airtel | 2 | India occidentale | 10 G | Airtel, Sify, Vodafone Idea |
| **Monaco** | [EdgeConneX](https://www.edgeconnex.com/locations/europe/munich/) | 1 | n/d | 10 G | DE-CIX |
| **New York** | [Equinix NY9](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny9/) | 1 | n/d | 10 G, 100 G | CenturyLink Cloud Connect, Colt, Coresite, DE-CIX, Equinix, intercloud, Megaport, Packet, Zayo |
| **Newport(Wales)** | [Dati di nuova generazione](https://www.nextgenerationdata.co.uk) | 1 | Regno Unito occidentale | n/d | British Telecom, Colt, Level 3 Communications, Next Generation Data |
| **Osaka** | [Equinix OS1](https://www.equinix.com/locations/asia-colocation/japan-colocation/osaka-data-centers/os1/) | 2 | Giappone occidentale | 10 G, 100 G | A TOKYO, Colt, Equinix, Internet Initiative Giappone Inc.-IIJ, Megaport, NTT Communications, NTT SmartConnect, Softbank |
| **Oslo** | [DigiPlex Ulven](https://www.digiplex.com/locations/oslo-datacentre) | 1 | Norvegia orientale | 10 G, 100 G | Global Connect, Megaport, Telenor, Telia Carrier |
| **Parigi** | [Interxion PAR5](https://www.interxion.com/Locations/paris/) | 1 | Francia centrale | 10 G, 100 G | CenturyLink Cloud Connect, Colt, Equinix, Intercloud, Interxion, Orange, Telia Carrier, Zayo |
| **Perth** | [NextDC P1](https://www.nextdc.com/data-centres/p1-perth-data-centre) | 2 | n/d | 10 G | Megaport, NextDC |
| **Phoenix** | [EdgeConneX PHX01](https://www.edgeconnex.com/locations/north-america/phoenix-az/) | 1 | n/d | 10 G | |
| **Quebec City** | [Vantage](https://vantage-dc.com/data_centers/quebec-city-data-center-campus/) | 1 | Canada orientale | n/d | Bell Canada, Megaport |
| **Queretaro (Messico)** | [Reti KIO QR01](https://www.kionetworks.com/es-mx/) | 4 | n/d | 10 G | Transtelco|
| **Quincy** | [Sabey datacenter-creazione di un](https://sabeydatacenters.com/data-center-locations/central-washington-data-centers/quincy-data-center) | 1 | Stati Uniti occidentali 2 | 10 G, 100 G | | 
| **San Antonio** | [CyrusOne SA1](https://cyrusone.com/locations/texas/san-antonio-texas/) | 1 | Stati Uniti centro-meridionali | 10 G, 100 G | CenturyLink Cloud Connect, Megaport |
| **Sao Paulo** | [Equinix SP2](https://www.equinix.com/locations/americas-colocation/brazil-colocation/sao-paulo-data-centers/sp2/) | 3 | Brasile meridionale | n/d | Aryaka Networks, Ascenty Data Centers, British Telecom, Equinix, Level 3 Communications, Neutrona Networks, Orange, Tata Communications, Telefonica, UOLDIVEO |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | 1 | Stati Uniti occidentali 2 | 10 G, 100 G | Aryaka Networks, Equinix, Level 3 Communications, Megaport, Telus, Zayo |
| **Seoul** | [KINX Gasan IDC](https://www.kinx.net/?lang=en) | 2 | Corea centrale | 10 G, 100 G | KINX, KT, LG CNS, Sejong Telecom |
| **Silicon Valley** | [Equinix SV1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv1/) | 1 | Stati Uniti occidentali | 10 G, 100 G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Colt, Comcast, Coresite, Equinix, InterCloud, Internet2, IX Reach, Packet, PacketFabric, Level 3 Communications, Megaport, Orange, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Silicon Valley2** | [Coresite SV7](https://www.coresite.com/data-centers/locations/silicon-valley/sv7) | 1 | Stati Uniti occidentali | 10 G, 100 G | Colt, Coresite | 
| **Singapore** | [Equinix SG1](https://www.equinix.com/locations/asia-colocation/singapore-colocation/singapore-data-center/sg1/) | 2 | Asia sud-orientale | 10 G, 100 G | Aryaka Networks, AT&T NetBond, British Telecom, China Mobile International, Epsilon Global Communications, Equinix, InterCloud, Level 3 Communications, Megaport, NTT Communications, Orange, SingTel, Tata Communications, Telstra Corporation, Verizon, Vodafone |
| **Singapore2** | [Global Switch Tai Seng](https://www.globalswitch.com/locations/singapore-data-centres/) | 2 | Asia sud-orientale | 10 G, 100 G | China Unicom Global, Colt, Epsilon Global Communications, Megaport, PCCW Global Limited, SingTel |
| **Stavanger** | [Green Mountain DC1](https://greenmountain.no/dc1-stavanger/) | 1 | Norvegia occidentale | 10 G, 100 G |Connessione globale, Megaport |
| **Stoccolma** | [Equinix SK1](https://www.equinix.com/locations/europe-colocation/sweden-colocation/stockholm-data-centers/sk1/) | 1 | n/d | 10 G | Equinix, Telia Carrier |
| **Sydney** | [Equinix SY2](https://www.equinix.com/locations/asia-colocation/australia-colocation/sydney-data-centers/sy2/) | 2 | Australia orientale | 10 G, 100 G | AARNet, AT&T NetBond, British Telecom, Devoli, Equinix, Kordia, Megaport, NEXTDC, NTT Communications, Optus, Orange, Spark NZ, Telstra Corporation, TPG Telecom, Verizon, Vocus Group NZ |
| **Sydney2** | [NextDC S1](https://www.nextdc.com/data-centres/s1-sydney-data-centre) | 2 | Australia orientale | 10 G, 100 G | Megaport, NextDC |
| **Taipei** | Chief Telecom | 2 | n/d | 10 G | Chief Telecom, Chunghwa Telecom, FarEasTone |
| **Tokyo** | [Equinix TY4](https://www.equinix.com/locations/asia-colocation/japan-colocation/tokyo-data-centers/ty4/) | 2 | Giappone orientale | 10 G, 100 G | Aryaka Networks, AT&T NetBond, BBIX, British Telecom, CenturyLink Cloud Connect, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, Megaport, NTT Communications, NTT EAST, Orange, Softbank, Verizon |
| **Tokyo2** | [AT TOKYO](https://www.attokyo.com/) | 2 | Giappone orientale | 10 G, 100 G | AT TOKYO |
| **Toronto** | [Cologix TOR1](https://www.cologix.com/data-centers/toronto/tor1/) | 1 | Canada centrale | 10 G, 100 G | AT&T NetBond, Bell Canada, CenturyLink Cloud Connect, Cologix, Equinix, IX Reach Megaport, Telus, Verizon, Zayo |
| **Vancouver** | [Cologix VAN1](https://www.cologix.com/data-centers/vancouver/van1/) | 1 | n/d | 10 G | Cologix |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | 1 | Stati Uniti orientali, Stati Uniti orientali 2 | 10 G, 100 G | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink Cloud Connect, Cologix, Colt, Comcast, Coresite, Equinix, Internet2, InterCloud, IX Reach, Level 3 Communications, Megaport, Neutrona Networks, NTT Communications, Orange, PacketFabric, SES, Sprint, Tata Communications, Telia Carrier, Verizon, Zayo |
| **Washington DC2** | [Coresite Reston](https://www.coresite.com/data-centers/locations/northern-virginia-washington-dc/reston-campus) | 1 | Stati Uniti orientali, Stati Uniti orientali 2 | 10 G, 100 G | CenturyLink Cloud Connect, Coresite, Intelsat, Megaport, Viasat, Zayo | 
| **Zurigo** | [Interxion ZUR2](https://www.interxion.com/Locations/zurich/) | 1 | Svizzera settentrionale | 10 G, 100 G | Intercloud, Interxion, Megaport, Swisscom |

 **+** indica disponibile a breve

### <a name="national-cloud-environments"></a>Ambienti cloud nazionali

I cloud nazionali di Azure sono isolati tra loro e da Azure commerciale globale. ExpressRoute di un cloud di Azure non può connettersi alle aree di Azure di altri.

### <a name="us-government-cloud"></a>Cloud del US Gov
| **Posizione** | **Indirizzo** | **Aree di Azure locali**| **ER Direct** | **Provider di servizi** |
| --- | --- | --- | --- | --- |
| **Atlanta** | [Equinix AT1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/atlanta-data-centers/at1/) | n/d | 10 G, 100 G | Equinix |
| **Chicago** | [Equinix CH1](https://www.equinix.com/locations/americas-colocation/united-states-colocation/chicago-data-centers/ch1/) | n/d | 10 G, 100 G | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | [Equinix DA3](https://www.equinix.com/locations/americas-colocation/united-states-colocation/dallas-data-centers/da3/) | n/d | 10 G, 100 G | Equinix, Megaport, Verizon |
| **New York** | [Equinix NY5](https://www.equinix.com/locations/americas-colocation/united-states-colocation/new-york-data-centers/ny5/) | n/d | 10 G, 100 G | Equinix, CenturyLink Cloud Connect, Verizon |
| **Phoenix** | [CyrusOne Chandler](https://cyrusone.com/locations/arizona/phoenix-arizona-chandler/) | US Gov Arizona | n/d | AT&T NetBond, CenturyLink Cloud Connect, Megaport |
| **San Antonio** | [CyrusOne SA2](https://cyrusone.com/locations/texas/san-antonio-texas-ii/) | US Gov Texas | n/d | CenturyLink Cloud Connect, Megaport |
| **Silicon Valley** | [Equinix SV4](https://www.equinix.com/locations/americas-colocation/united-states-colocation/silicon-valley-data-centers/sv4/) | n/d | 10 G, 100 G | AT&T, Equinix, Level 3 Communications, Verizon |
| **Seattle** | [Equinix SE2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/seattle-data-centers/se2/) | n/d | n/d | Equinix, Megaport |
| **Washington DC** | [Equinix DC2](https://www.equinix.com/locations/americas-colocation/united-states-colocation/washington-dc-data-centers/dc2/) | US DoD (area orientale), US Gov Virginia | 10 G, 100 G | AT&T NetBond, CenturyLink Cloud Connect, Equinix, Level 3 Communications, Megaport, Verizon |

### <a name="china"></a>Cina
| **Posizione** | **Provider di servizi** |
| --- | --- |
| **Pechino** |China Telecom |
| **Beijing2** | China Telecom, China Unicom, GDS |
| **Shanghai** |China Telecom |
| **Shanghai2** | China Telecom, China Unicom, GDS |

Per altre informazioni, vedere [ExpressRoute in Cina](http://www.windowsazure.cn/home/features/expressroute/)

### <a name="germany"></a>Germania
| **Posizione** | **Provider di servizi** |
| --- | --- |
| **Berlino** |e-shelter, Megaport+, T-Systems |
| **Francoforte** |Colt, Equinix, Interxion |

## <a name="connectivity-through-exchange-providers"></a><a name="c1partners"></a>Connettività con provider di scambio
Se il provider di connettività non è incluso nelle sezioni precedenti, sarà comunque possibile creare una connessione.

* Contattare il provider di connettività per verificare se è connesso a uno qualsiasi degli scambi nella tabella precedente. Per altre informazioni sui servizi offerti dai provider di Exchange, è possibile esaminare i seguenti collegamenti. Alcuni provider di connettività sono già connessi agli scambi Ethernet.
  * [Cologix](https://www.cologix.com/)
  * [CoreSite](https://www.coresite.com/)
  * [DE-CIX](https://www.de-cix.net/en/de-cix-service-world/cloud-exchange)
  * [Equinix Cloud Exchange](https://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](https://www.interxion.com/)
  * [NEXTDC](https://www.nextdc.com/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [PacketFabric](https://www.packetfabric.com/cloud-connectivity/microsoft-azure)
  * [Teraco](https://www.teraco.co.za/platform-teraco/africa-cloud-exchange/)
  
* Richiedere al provider di connettività di estendere la rete alla località di peering scelta.
  * Assicurarsi che il provider di connettività estenda la connettività con disponibilità elevata, in modo che non siano presenti singoli punti di errore.
* Ordinare a un circuito ExpressRoute con scambio come provider di connettività di connettersi a Microsoft.
  * Per configurare la connettività, eseguire la procedura illustrata in [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) .

## <a name="connectivity-through-satellite-operators"></a>Connettività tramite operatori satellite
Se si è in modalità remota e non si dispone di connettività in fibra o si desidera valutare altre opzioni di connettività, sono disponibili gli operatori satellite seguenti. 

* Intelsat
* [SES](https://www.ses.com/networks/signature-solutions/signature-cloud/ses-and-azure-expressroute)
* [Viasat](http://www.directcloud.viasatbusiness.com/)

## <a name="connectivity-through-additional-service-providers"></a><a name="c1partners"></a>Connettività con provider di servizi aggiuntivi
| **Posizione** | **Exchange** | **Provider di connettività** |
| --- | --- | --- |
| **Amsterdam** | Equinix, Interxion, Level 3 Communications | BICS, CloudXpress, Eurofiber, Fastweb S.p.A, Gulf Bridge International, Kalaam Telecom Bahrain B.S.C, MainOne, Nianet, POST Telecom Luxembourg, Proximus, RETN, TDC Erhverv, Telecom Italia Sparkle, Telekom Deutschland GmbH, Telia |
| **Atlanta** | Equinix| Crown Castle
| **Città del Capo** | Teraco | MTN |
| **Chicago** | Equinix| Crown Castle, Spectrum Enterprise, Windstream |
| **Dallas** | Equinix, Megaport | Axtel, C3ntro Telecom, Cox Business, Crown Castle, Data Foundry, Spectrum Enterprise, Transtelco |
| **Francoforte** | Interxion | BICS, Cinia, Equinix, Nianet, QSC AG, Telekom Deutschland GmbH |
| **Hamburg** | Equinix | Cinia |
| **RAS di Hong Kong** | Equinix | Chief, Macroview Telecom |
| **Johannesburg** | Teraco | MTN |
| **Londra** | BICS, Equinix, euNetworks| Bezeq International Ltd., CoreAzure, Epsilon Telecommunications Limited, Exponential E, HSO, NexGen Networks, Proximus, Tamares Telecom, Zain |
| **Los Angeles** | Equinix |Crown Castle, Spectrum Enterprise, Transtelco |
| **Madrid** | Level3 | Zertia |
| **Montreal** | Cologix| Airgate Technologies, Inc. Aptum Technologies, Rogers, Zirro |
| **New York** |Equinix, Megaport | Altice Business, Crown Castle, Spectrum Enterprise, Webair |
| **Parigi** | Equinix | Proximus |
| **Quebec City** | Megaport | Fibrenoire |
| **Sao Paulo** | Equinix | Venha Pra Nuvem |
| **Seattle** |Equinix | Alaska Communications |
| **Silicon Valley** |Coresite, Equinix | Cox Business, Spectrum Enterprise, Windstream, X2nsat Inc. |
| **Singapore** |Equinix |1CLOUDSTAR, BICS, CMC Telecom, Epsilon Telecommunications Limited, LGA Telecom, United Information Highway (UIH) |
| **Slough** | Equinix | HSO|
| **Sydney** | Megaport | Macquarie Telecom Group|
| **Tokyo** | Equinix | ARTERIA Networks Corporation, BroadBand Tower, Inc. |
| **Toronto** | Equinix, Megaport | Airgate Technologies Inc., Beanfield Metroconnect, Aptum Technologies, IVedha Inc, Rogers, Thinktel, Zirro|
| **Washington DC** |Equinix | Altice Business, BICS, Cox Business, Crown Castle, Gtt Communications Inc, Epsilon Telecommunications Limited, Masergy, Windstream |

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
