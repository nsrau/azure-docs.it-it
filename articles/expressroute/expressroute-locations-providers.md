<properties
   pageTitle="Percorsi di ExpressRoute | Microsoft Azure"
   description="Questo articolo fornisce una panoramica dettagliata delle località in cui vengono offerti i servizi e informazioni su come connettersi alle aree di Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/07/2016"
   ms.author="cherylmc" />

# Partner e località di peering per ExpressRoute

Le tabelle in questo articolo includono informazioni su provider di connettività ExpressRoute, copertura geografica di ExpressRoute, servizi cloud Microsoft supportati tramite ExpressRoute e integratori di sistemi ExpressRoute.

## <a name="partners"></a>Provider di connettività ExpressRoute

ExpressRoute è supportato in tutte le aree e le località di Azure. La mappa seguente fornisce un elenco di aree di Azure e località per ExpressRoute. Per località ExpressRoute si intendono le aree in cui è attivo il peering di Microsoft con alcuni provider di servizi.

![Mappa delle località][0]

Si otterrà l'accesso al servizio di Azure in tutte le aree di un'area geopolitica se è stata effettuata la connessione ad almeno una località per ExpressRoute entro l'area geopolitica. La tabella seguente fornisce una mappa di aree di Azure e di località ExpressRoute in un'area geopolitica.

|**Area geopolitica**|**Aree di Azure**|**Località per ExpressRoute**|
|---|---|---|
|**America del Nord**|Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Canada centrale, Canada orientale|Atlanta, Chicago, Dallas, Las Vegas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, Montreal+, Quebec City+, Toronto|
|**America del Sud**|Brasile meridionale|Sao Paulo|
|**Europa**|Europa settentrionale, Europa occidentale, Regno Unito occidentale, Regno Unito meridionale|Amsterdam, Dublino, Londra, Newport(Galles)+, Parigi|
|**Asia**|Asia orientale, Asia sudorientale|Hong Kong, Singapore|
|**Giappone**|Giappone occidentale, Giappone orientale|Osaka, Tokyo|
|**Australia**|Australia sudorientale, Australia orientale|Melbourne, Sydney|
|**India**|India occidentale, India centrale, India Meridionale|Chennai, Mumbai|



Nella tabella seguente vengono fornite informazioni su aree e confini geopolitici per cloud nazionali.

|**Area geopolitica**|**Aree di Azure**|**Località per ExpressRoute**|
|---|---|---|---|
|**Cloud del governo degli Stati Uniti**|Stati Uniti - Iowa, Virginia|Chicago, Dallas, New York, Washington DC|
|**Cina**|Cina meridionale, Cina orientale|Pechino, Shanghai|
|**Germania**|Germania centrale, Germania orientale|Berlino+, Francoforte|


La connettività tra diverse aree geopolitiche non è supportata nello standard SKU EspressRoute. È necessario attivare il componente aggiuntivo premium ExpressRoute per supportare la connettività globale. La connettività per ambienti cloud nazionale non è supportata. È possibile utilizzare il provider di connettività in caso di necessità.


## Posizioni del provider di connettività

> [AZURE.SELECTOR]
[Locations By Provider](expressroute-locations.md#connectivity-provider-locations)
[Providers By Location](expressroute-locations-providers.md#connectivity-provider-locations)

### Produzione Azure
| **Posizione** | **Provider di servizi** |
|---------------|-----------------------|
| **Amsterdam** | Aryaka Networks, AT&T NetBond, British Telecom, Colt, Equinix, euNetworks, GÉANT+, InterCloud, Internet Solutions - Cloud Connect, Interxion, Level 3 Communications, Orange, Tata Communications, TeleCity Group, Telenor, Verizon |
| **Atlanta** | Equinix |
| **Chennai** | Tata Communications |
| **Chicago** | AT&T NetBond, Comcast, Equinix, Level 3 Communications, Zayo Group |
| **Dallas** | AT&T NetBond, Equinix, Level 3 Communications, Megaport |
| **Dublino** | Colt, Telecity Group |
| **Hong Kong** | British Telecom, China Telecom Global, Equinix, Megaport, Orange, PCCW Global Limited, Tata Communications, Verizon |
| **Londra** | AT&T NetBond, British Telecom, Colt, Equinix, InterCloud, Internet Solutions - Cloud Connect, Interxion, Jisc+, Level 3 Communications, MTN, NTT Communications, Orange, Tata Communications, Telecity Group, Telenor, Verizon, Vodafone |
| **Las Vegas** | Level 3 Communications+, Megaport
| **Los Angeles** | CoreSite, Equinix, Megaport, NTT, Zayo Group |
| **Melbourne** | Equinix, Megaport, NEXTDC, Telstra Corporation |
| **New York** | Equinix, Megaport, Zayo Group |
| **Montreal** | Cologix+ |
| **Mumbai** | Tata Communications |
| **Osaka** | Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank |
| **Parigi** | Interxion |
| **Sao Paulo** | Equinix, Telefonica |
| **Seattle** | Equinix, Level 3 Communications, Megaport |
| **Silicon Valley** | Aryaka Networks, AT&T NetBond, British Telecom, CenturyLink+, Comcast, Equinix, Level 3 Communications, Orange, Tata Communications, Verizon, Zayo Group |
| **Singapore** | Aryaka Networks, AT&T NetBond, British Telecom, Equinix, InterCloud, Megaport, Orange, SingTel, Tata Communications, Verizon |
| **Sydney** | AT&T NetBond, British Telecom, Equinix, Megaport, NEXTDC, Telstra Corporation, Verizon |
| **Tokyo** | Aryaka Networks, British Telecom, Colt, Equinix, Internet Initiative Japan Inc. - IIJ, NTT Communications, Softbank, Verizon |
| **Toronto** | Cologix, Equinix, Zayo Group |
| **Washington DC** | Aryaka Networks, AT&T NetBond, British Telecom, Comcast, Equinix, InterCloud, Level 3 Communications, Megaport, Orange, Tata Communications, Verizon, Zayo Group |

 **+** indica disponibile a breve

### Ambienti cloud nazionali

#### Cloud del governo degli Stati Uniti

| **Posizione** |**Provider di servizi** |
|---------------|--------------------|
| **Chicago** | AT&T NetBond, Equinix, Level 3 Communications, Verizon |
| **Dallas** | Equinix, Verizon+ |
| **New York** | Equinix, Level 3 Communications+, Verizon |
| **Washington DC** | AT&T NetBond, Equinix, Level 3 Communications, Verizon |

#### Cina

| **Posizione** | **Provider di servizi** |
|---------------|-----------------------|
| **Pechino** | China Telecom |
| **Shanghai** | China Telecom |
Per altre informazioni, vedere [ExpressRoute in Cina](http://www.windowsazure.cn/home/features/expressroute/)

#### Germania

| **Posizione** | **Provider di servizi** |
|---------------|-----------------------|
| **Berlino** | Colt+, e-shelter+ |
| **Francoforte** | Colt, Equinix+, Interxion |

## <a name="nonpartners"></a>Connettività con provider di servizi non elencati

Se il provider di connettività non è incluso nelle sezioni precedenti, sarà comunque possibile creare una connessione.

- Contattare il provider di connettività per verificare se è connesso a uno qualsiasi degli scambi nella tabella precedente. Per altre informazioni sui servizi offerti dai provider di Exchange, è possibile esaminare i seguenti collegamenti. Alcuni provider di connettività sono già connessi agli scambi Ethernet.

	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
	- [Interxion](http://www.interxion.com/)
	- [NEXTDC](http://www.nextdc.com/)
	- [CoreSite](http://www.coresite.com/)
	- [Cologix](http://www.cologix.com/)
- Richiedere al provider di connettività di estendere la rete alla località di peering scelta.
	- Assicurarsi che il provider di connettività estenda la connettività con disponibilità elevata, in modo che non siano presenti singoli punti di errore.
- Ordinare a un circuito ExpressRoute con scambio come provider di connettività di connettersi a Microsoft.
	- Per configurare la connettività, eseguire la procedura illustrata in [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md).

|**Posizione**|**Exchange**|**Provider di connettività**|
|-------------|------------|-------------------------|
| **New York** | Equinix | Lightower |
| **Seattle** | Equinix | Alaska Communications |
| **Silicon Valley** | Equinix | XO Communications |
| **Singapore** | Equinix | 1CLOUDSTAR |
| **Washington DC** | Equinix | Lightower |

## Integratori di sistemi ExpressRoute

L'abilitazione della connettività privata per soddisfare le proprie esigenze può risultare complessa, in base alle dimensioni della rete. Per ottenere supporto per l'onboarding in ExpressRoute, è possibile collaborare con uno degli Integratori di sistemi elencati nella seguente tabella.

|**Continente**|**Integratori di sistemi**|
|-------------|---------------------|
| **Asia** | Avanade Inc., OneAs1a|
| **Europa** | Avanade Inc., Dotnet Solutions|
| **Stati Uniti** | Avanade Inc., Equinix Professional Services, Perficient, Project Leadership|

## Passaggi successivi

- Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
- Verificare che vengano soddisfatti tutti i prerequisiti. Vedere [Prerequisiti per ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mappa delle località"

<!---HONumber=AcomDC_0914_2016-->