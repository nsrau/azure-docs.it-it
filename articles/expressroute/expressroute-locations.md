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
   ms.date="07/28/2016"
   ms.author="cherylmc" />

# Partner e località di peering per ExpressRoute

Le tabelle in questo articolo includono informazioni su provider di connettività ExpressRoute, copertura geografica di ExpressRoute, servizi cloud Microsoft supportati tramite ExpressRoute e integratori di sistemi ExpressRoute.

## <a name="partners"></a>Provider di connettività ExpressRoute

ExpressRoute è supportato in tutte le aree e le località di Azure. La mappa seguente fornisce un elenco di aree di Azure e località per ExpressRoute. Per località ExpressRoute si intendono le aree in cui è attivo il peering di Microsoft con alcuni provider di servizi.

![Mappa delle località][0]

Si otterrà l'accesso al servizio di Azure in tutte le aree di un'area geopolitica se è stata effettuata la connessione ad almeno una località per ExpressRoute entro l'area geopolitica. La tabella seguente fornisce una mappa di aree di Azure e di località ExpressRoute in un'area geopolitica.

|**Area geopolitica**|**Aree di Azure**|**Località per ExpressRoute**|
|---|---|---|
|**America del Nord**|Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Canada centrale, Canada orientale|Atlanta, Chicago, Dallas, Las Vegas+, Los Angeles, New York, Seattle, Silicon Valley, Washington DC, Montreal+, Quebec City+, Toronto|
|**America del Sud**|Brasile meridionale|Sao Paulo|
|**Europa**|Europa settentrionale, Europa occidentale|Amsterdam, Dublino, Londra, Newport(Galles)+, Parigi|
|**Asia**|Asia orientale, Asia sudorientale|Hong Kong, Singapore|
|**Giappone**|Giappone occidentale, Giappone orientale|Osaka, Tokyo|
|**Australia**|Australia sudorientale, Australia orientale|Melbourne, Sydney|
|**India**|India occidentale, India centrale, India Meridionale|Chennai, Mumbai|



Nella tabella seguente vengono fornite informazioni su aree e confini geopolitici per cloud nazionali.

|**Area geopolitica**|**Aree di Azure**|**Località per ExpressRoute**|
|---|---|---|---|
|**Cloud del governo degli Stati Uniti**|Stati Uniti - Iowa, Virginia|Chicago, Dallas+, New York, Washington DC|
|**Cina**|Cina meridionale, Cina orientale|Pechino, Shanghai|
|**Germania**|Germania centrale, Germania orientale|Berlino, Francoforte|


La connettività tra diverse aree geopolitiche non è supportata nello standard SKU EspressRoute. È necessario attivare il componente aggiuntivo premium ExpressRoute per supportare la connettività globale. La connettività per ambienti cloud nazionale non è supportata. È possibile utilizzare il provider di connettività in caso di necessità.


## Posizioni del provider di connettività

> [AZURE.SELECTOR]
[Locations By Provider](expressroute-locations.md#connectivity-provider-locations)
[Providers By Location](expressroute-locations-providers.md#connectivity-provider-locations)

### Produzione Azure

| **Provider di servizi** |**Microsoft Azure** | **Office 365 e CRM Online** | **Località** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | Supportato | Supportato | Amsterdam, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Supportato | Supportato | Amsterdam, Chicago, Dallas, Londra, Silicon Valley, Singapore, Sydney, Washington DC |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Supportato | Supportato | Amsterdam, Hong Kong, Londra, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
|**CenturyLink** | Presto disponibile | Presto disponibile| Silicon Valley |
|**China Telecom Global** | Supportato | Non supportato | Hong Kong |
|**[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** | Supportato | Presto disponibile | Montreal+, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Supportato | Supportato | Amsterdam, Dublino, Londra, Tokyo |
| **Comcast** | Supportato | Supportato | Chicago, Silicon Valley, Washington DC |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** | Supportato | Supportato | Los Angeles | 
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Supportato | Supportato | Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londra, Los Angeles, Melbourne, New York, Osaka, San Paolo, Seattle, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **euNetworks** | Supportato | Supportato | Amsterdam |
| **GÉANT** | Presto disponibile | Presto disponibile | Amsterdam+ |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** | Supportato | Supportato | Osaka, Tokyo |
| **[InterCloud](https://www.intercloud.com/)** | Supportato | Supportato | Amsterdam, Londra, Singapore, Washington DC |
| **Internet Solutions - Cloud Connect** | Supportato | Supportato | Amsterdam, Londra |
| **Interxion** | Supportato | Supportato | Amsterdam, Londra, Parigi |
| **Jisc** | Presto disponibile | Presto disponibile | Londra+ | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Supportato | Supportato | Amsterdam, Chicago, Dallas, Las Vegas+, Londra, Seattle, Silicon Valley, Washington DC |
| **Megaport** | Supportato | Supportato | Dallas, Hong Kong, Las Vegas, Los Angeles, Melbourne, New York, Seattle, Singapore, Sydney, Washington DC |
| **MTN** | Supportato | Supportato | Londra |
| **NEXTDC** | Supportato | Supportato | Melbourne, Sydney |
| **NTT Communications** | Supportato | Supportato | Londra, Los Angeles, Osaka, Tokyo |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** | Supportato | Supportato | Amsterdam, Hong Kong, Londra, Silicon Valley, Singapore, Washington DC |
| **PCCW Global Limited** | Supportato | Supportato | Hong Kong |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | Supportato | Supportato | Singapore |
| **Softbank** | Supportato | Supportato | Osaka, Tokyo | 
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Supportato | Supportato | Amsterdam, Chennai, Hong Kong, Londra, Mumbai, Silicon Valley, Singapore, Washington DC |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Supportato | Supportato | Amsterdam, Londra |
| **Telefonica** | Presto disponibile | Presto disponibile | Sao Paulo+ |
| **Telenor** | Supportato | Supportato | Amsterdam, Londra |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Supportato | Imminente | Melbourne, Sydney |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** | Supportato | Supportato | Amsterdam, Hong Kong, Londra, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **Vodafone** | Supportato | Non supportato | Londra | 
| **[Zayo Group](http://www.zayo.com/solutions/industries/connect-to-cloud-data-centers/cloud-connectivity/microsoft-expressroute/)** | Supportato | Supportato | Chicago, Los Angeles, New York, Silicon Valley, Toronto, Washington DC |

 **+** indica disponibile a breve

### Ambienti cloud nazionali

#### Cloud del governo degli Stati Uniti

| **Provider di servizi** |**Microsoft Azure** | **Office 365** | **Località** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Supportato | Supportato | Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Supportato | Supportato | Chicago, Dallas+, New York, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Supportato | Supportato | Chicago, New York +, Washington DC |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Supportato | Supportato | Chicago, Dallas+, New York, Washington DC |

#### Cina

| **Provider di servizi** |**Microsoft Azure** | **Office 365** | **Località** |
|-----------------------|--------------------|----------------|---------------|
| **China Telecom** | Supportato | Non supportato | Pechino, Shanghai|
Per altre informazioni, vedere [ExpressRoute in Cina](http://www.windowsazure.cn/home/features/expressroute/).

#### Germania

| **Provider di servizi** |**Microsoft Azure** | **Office 365** | **Località** |
|-----------------------|--------------------|----------------|---------------|
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Supportato | Non supportato | Berlino+, Francoforte|
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Presto disponibile | Non supportato | Francoforte+|
| **e-shelter** | Presto disponibile | Non supportato | Berlino+|
| **Interxion** | Supportato | Non supportato | Francoforte|

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

|**Provider di connettività**|**Exchange**|**Località**|
|---|---|---|
|**[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)**|Equinix|Singapore|
|**Alaska Communications**|Equinix|Seattle|
|**[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)**|Equinix|New York, Washington DC|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|


## Integratori di sistemi ExpressRoute

L'abilitazione della connettività privata per soddisfare le proprie esigenze può risultare complessa, in base alle dimensioni della rete. Per ottenere supporto per l'onboarding in ExpressRoute, è possibile collaborare con uno degli Integratori di sistemi elencati nella seguente tabella.

|**Integratore di sistemi**|**Continente**|
|---|---|
|**[Avanade Inc.](http://www.avanade.com/)**| Asia, Europa, Stati Uniti |
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**| Europa |
|**[Equinix Professional Services](http://www.equinix.com/services/consulting/)**|Stati Uniti|
|**[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Asia |
|**[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | Stati Uniti |
|**[Project Leadership](http://www.projectleadership.net/azure)** | Stati Uniti |

## Passaggi successivi

- Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
- Verificare che vengano soddisfatti tutti i prerequisiti. Vedere [Prerequisiti per ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mappa delle località"

<!---HONumber=AcomDC_0817_2016-->