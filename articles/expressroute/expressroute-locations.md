<properties
   pageTitle="Località per ExpressRoute"
   description="Questa pagina fornisce una panoramica dettagliata delle località in cui vengono offerti i servizi e informazioni su come connettersi alle aree di Azure."
   services="expressroute"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor="tysonn" />
<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="cherylmc" />

# Partner e località di peering per ExpressRoute
Le tabelle in questa pagina includono informazioni su provider di connettività ExpressRoute (di Exchange e di servizi di rete), copertura geografica di ExpressRoute, servizi cloud Microsoft supportati tramite ExpressRoute e integratori di sistemi ExpressRoute.

## Provider di connettività ExpressRoute
ExpressRoute è supportato in tutte le aree e le località di Azure. La mappa seguente fornisce un elenco di aree di Azure e località per ExpressRoute. Per località ExpressRoute si intendono le aree in cui è attivo il peering di Microsoft con alcuni provider di servizi.
 
![](./media/expressroute-locations/expressroute-locations-map.png)

Si otterrà l'accesso al servizio di Azure in tutte le aree di un'area geopolitica se è stata effettuata la connessione ad almeno una località per ExpressRoute entro l'area geopolitica. La tabella seguente fornisce una mappa di aree di Azure e di località ExpressRoute in un'area geopolitica.

|**Area geopolitica**|**Aree di Azure**|**Località per ExpressRoute**|
|---|---|---|
|**Stati Uniti**|Tutte le aree degli Stati Uniti: Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti orientali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali|Atlanta, Chicago, Dallas, Los Angeles, New York, Seattle, Silicon Valley, Washington DC|
|**Agenzie governative statunitensi**|Iowa, Virginia|Ashburn, Chicago|
|**Sud America**|Brasile meridionale|Sao Paulo|
|**Europa**|Europa settentrionale, Europa occidentale|Amsterdam, Londra|
|**Asia**|Asia orientale, Asia sudorientale|Hong Kong, Singapore|
|**Giappone**|Giappone occidentale, Giappone orientale|Tokyo|
|**Australia**|Australia sudorientale, Australia orientale|Melbourne, Sydney|
|**India**|India occidentale, India centrale, India Meridionale|Chennai, Mumbai|

La connettività tra diverse aree geopolitiche non è supportata. È possibile collaborare con il provider di connettività per estendere la connettività in diverse aree geopolitiche usando la rete del provider.


## Località per provider di Exchange

| **Provider di servizi** |**Microsoft Azure** | **Office 365** | **Località** |
|-----------------------|--------------------|----------------|---------------|
| **[Aryaka Networks](http://www.aryaka.com/)** | Supportato | Non supportato | Silicon Valley, Singapore, Washington DC |
| **[Colt Ethernet](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Supportato | Non supportato | Amsterdam, Londra |
| **Comcast** | Supportato | Non supportato | Silicon Valley, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** | Supportato | Imminente | Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londra, Los Angeles, Melbourne, New York, San Paolo, Seattle, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[InterCloud](https://www.intercloud.com/)** | Supportato | Non supportato | Amsterdam, Londra, Singapore, Washington DC |
| **Internet Solutions - Cloud Connect** | Supportato | Non supportato | Amsterdam, Londra |
| **Interxion** | Supportato | Non supportato | Amsterdam |
| **[Level 3 Communications - Exchange](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Supportato | Non supportato | Chicago, Dallas, Londra, Seattle, Silicon Valley, Washington DC |
| **NEXTDC** | Supportato | Non supportato | Melbourne, Sydney+ |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** | Supportato | Imminente | Amsterdam, Londra |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Supportato | Non supportato | Melbourne+, Sydney |
| **[Zayo Group](http://www.zayo.com/)** | Supportato | Non supportato | Washington DC |

 **+** indica disponibile a breve

Per i passaggi di configurazione della connessione, vedere [Configurare una connessione ExpressRoute mediante un provider di Exchange](expressroute-configuring-exps.md).

## Località per i provider di servizi di rete


| **Provider di servizi** |**Microsoft Azure** | **Office 365** | **Località** |
|-----------------------|--------------------|----------------|---------------|
| **[AT&T](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** | Supportato | Imminente | Amsterdam+, Londra+, Dallas, Silicon Valley, Washington DC |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** | Supportato | Imminente | Amsterdam, Londra, Silicon Valley+, Washington DC |
|**China Telecom Global** | Imminente | Non supportato | Hong Kong+ |
| **[Colt IPVPN](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** | Supportato | Non supportato | Amsterdam, Londra |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2013/pdf/Azure_E.pdf)** | Supportato | Non supportato | Tokyo |
| **[Level 3 Communications - IPVPN](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** | Supportato | Non supportato | Chicago, Dallas, Londra, Seattle, Silicon Valley, Washington DC |
| **NTT Communications** | Imminente | Non supportato | Tokyo+ | 
| **[Orange](http://www.orange-business.com/)** | Supportato | Non supportato | Amsterdam, Londra, Silicon Valley, Washington DC |
| **PCCW Global Limited** | Supportato | Non supportato | Hong Kong |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** | Supportato | Non supportato | Singapore |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** | Supportato | Imminente | Amsterdam, Chennai+, Hong Kong, Londra, Mumbai+, Singapore |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** | Supportato | Non supportato | Melbourne+, Sydney |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** | Supportato | Non supportato | Londra, Hong Kong, Silicon Valley, Washington DC |

 **+** indica disponibile a breve

Per i passaggi di configurazione della connessione, vedere [Configurare una connessione ExpressRoute tramite un provider di servizi di rete](expressroute-configuring-nsps.md).

## Connettività tramite provider di servizi non elencati 

Se il provider di connettività usato non è incluso negli elenchi precedenti, sarà comunque possibile creare una connessione.

- Contattare il provider di connettività per verificare se è connesso a provider di Exchange nelle località elencate per EXP. Per altre informazioni sui servizi offerti dai provider di Exchange, vedere i collegamenti seguenti. Alcuni provider di connettività sono già connessi agli scambi Ethernet dei provider di Exchange.
	- [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/) 
	- [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
- Richiedere al provider di connettività di estendere la rete alla località di Exchange scelta.
	- Assicurarsi che il provider di connettività estenda la connettività con disponibilità elevata, in modo che non siano presenti singoli punti di errore.
	- È possibile che i provider di connettività (in particolare i provider Ethernet) richiedano all'utente di ottenere un paio di circuiti per gli scambi Ethernet, in modo da assicurare la disponibilità elevata. 
- Ordinare un circuito ExpressRoute tramite il provider di Exchange per connettersi ad Azure.
	- Per configurare la connettività, eseguire la procedura illustrata in [Configurare la connessione EXP](expressroute-configuring-exps.md).

|**Provider di connettività**|**Provider di Exchange**|**Località di peering**|
|---|---|---|
|**[XO Communications](http://www.xo.com/)**|Equinix|Silicon Valley|

## Integratori di sistemi ExpressRoute
L'abilitazione della connettività privata per soddisfare le proprie esigenze può risultare complessa, in base alle dimensioni della rete. Per ottenere supporto per l'onboarding in ExpressRoute, è possibile collaborare con uno degli Integratori di sistemi elencati nella tabella seguente.


|**Integratore di sistemi**|**Continente**|
|---|---|
|**[Nimbo](http://www.nimbo.com/)**|Stati Uniti||
|**[Dotnet Solutions](http://www.dotnetsolutions.co.uk/)**|EMEA|

## Passaggi successivi
- Assicurarsi che siano soddisfatti i [prerequisiti per ExpressRoute](expressroute-prerequisites.md).
- Per altre informazioni, vedere la pagina relativa alle [domande frequenti](expressroute-faqs.md).
- Per informazioni sulla configurazione di una connessione ExpressRoute, vedere [Configurare una connessione ExpressRoute mediante un provider di Exchange](expressroute-configuring-exps.md) o [Configurare una connessione ExpressRoute tramite un provider di servizi di rete](expressroute-configuring-nsps.md).
- Per configurare sia una connessione da sito a sito VPN sia una connessione ExpressRoute sulla stessa rete virtuale, vedere [Configurare connessioni ExpressRoute e VPN da sito a sito coesistenti](expressroute-coexist.md).
 

<!---HONumber=August15_HO7-->