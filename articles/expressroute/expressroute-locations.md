---
title: "Località e provider di connettività: Azure ExpressRoute | Documentazione Microsoft"
description: "Questo articolo fornisce una panoramica dettagliata delle località in cui vengono offerti i servizi e informazioni su come connettersi alle aree di Azure. Ordinamento per provider di connettività."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
ms.assetid: c878513a-d594-42ad-8b0e-403efd0c4b25
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/30/2017
ms.author: kaanan
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 6286e059da7df95303bca00b5910632302bd19b7
ms.contentlocale: it-it
ms.lasthandoff: 07/04/2017


---
# Partner e località di peering per ExpressRoute
<a id="expressroute-partners-and-peering-locations" class="xliff"></a>

> [!div class="op_single_selector"]
> * [Località per provider](expressroute-locations.md)
> * [Provider per località](expressroute-locations-providers.md)


Le tabelle in questo articolo includono informazioni su provider di connettività ExpressRoute, copertura geografica di ExpressRoute, servizi cloud Microsoft supportati tramite ExpressRoute e integratori di sistemi ExpressRoute.

## <a name="partners"></a>Provider di connettività ExpressRoute
ExpressRoute è supportato in tutte le aree e le località di Azure. La mappa seguente fornisce un elenco di aree di Azure e località per ExpressRoute. Per località ExpressRoute si intendono le aree in cui è attivo il peering di Microsoft con alcuni provider di servizi.

![Mappa delle località][0]

Si otterrà l'accesso al servizio di Azure in tutte le aree di un'area geopolitica se è stata effettuata la connessione ad almeno una località per ExpressRoute entro l'area geopolitica.

### Aree di Azure e località ExpressRoute in un'area geopolitica
<a id="azure-regions-to-expressroute-locations-within-a-geopolitical-region" class="xliff"></a>
La tabella seguente fornisce una mappa di aree di Azure e di località ExpressRoute in un'area geopolitica.

| **Area geopolitica** | **Aree di Azure** | **Località per ExpressRoute** |
| --- | --- | --- |
| **America del Nord** |Stati Uniti orientali, Stati Uniti occidentali, Stati Uniti orientali 2, Stati Uniti occidentali 2, Stati Uniti centrali, Stati Uniti centro-meridionali, Stati Uniti centro-settentrionali, Stati Uniti centro-occidentali, Canada centrale, Canada orientale |Atlanta, Chicago, Dallas, Denver, Las Vegas, Los Angeles, Miami, New York, San Antonio, Seattle, Silicon Valley, Washington DC, Montreal, Quebec City, Toronto |
| **America del Sud** |Brasile meridionale |Sao Paulo |
| **Europa** |Europa settentrionale, Europa occidentale, Regno Unito occidentale, Regno Unito meridionale |Amsterdam, Dublino, Londra, Newport (Galles), Parigi |
| **Asia** |Asia orientale, Asia sudorientale |Hong Kong, Singapore |
| **Giappone** |Giappone occidentale, Giappone orientale |Osaka, Tokyo |
| **Australia** |Australia sudorientale, Australia orientale |Melbourne, Sydney |
| **India** |India occidentale, India centrale, India Meridionale |Chennai, Mumbai |
| **Corea del Sud** |Corea centrale, Corea meridionale |Busan, Seoul |

### Aree e confini geopolitici per cloud nazionali
<a id="regions-and-geopolitical-boundaries-for-national-clouds" class="xliff"></a>
Nella tabella seguente vengono fornite informazioni su aree e confini geopolitici per cloud nazionali.

| **Area geopolitica** | **Aree di Azure** | **Località per ExpressRoute** |
| --- | --- | --- |
| **Cloud del governo degli Stati Uniti** |US Gov Iowa, US Gov Virginia, US DoD Central, US DoD East  |Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **Cina** |Cina meridionale, Cina orientale |Pechino, Shanghai |
| **Germania** |Germania centrale, Germania orientale |Berlino, Francoforte |

La connettività tra diverse aree geopolitiche non è supportata nello standard SKU EspressRoute. È necessario attivare il componente aggiuntivo premium ExpressRoute per supportare la connettività globale. La connettività per ambienti cloud nazionale non è supportata. È possibile utilizzare il provider di connettività in caso di necessità.

## <a name="locations"></a>Località dei provider di connettività

La tabella seguente mostra le località per provider di servizi. Per visualizzare i provider disponibili in base alla località, vedere la tabella dei [provider di servizi per località](expressroute-locations-providers.md#locations).


### Produzione Azure
<a id="production-azure" class="xliff"></a>
| **Provider di servizi** | **Microsoft Azure** | **Office 365 e Dynamics 365** | **Località** |
| --- | --- | --- | --- |
| **[AARNet](https://www.aarnet.edu.au/network-and-services/cloud-services-applications/azure-expressroute/)** |Supportato |Supportato |Melbourne, Sydney |
| **[Airtel](http://www.airtel.in/business/connexion)** | Supportato | Supportato | Chennai, Mumbai |
| **[Aryaka Networks](http://www.aryaka.com/)** |Supportato |Supportato |Amsterdam, Dallas, Hong Kong, Silicon Valley, Singapore, Tokyo, Washington DC |
| **[Ascenty Data Centers](https://ascenty.com/solucoes/conectividade-e-interconexoes/Microsoft-express-route/)** |Presto disponibile |Presto disponibile |Sao Paulo |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Supportato |Supportato |Amsterdam, Chicago, Dallas, Londra, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **[Bell Canada](https://business.bell.ca/shop/enterprise/cloud-connect-access-to-cloud-partner-services)** |Supportato |Supportato |Montreal, Toronto |
| **[British Telecom](http://www.globalservices.bt.com/uk/en/news/bt_to_provide_connectivity_to_microsoft_azure)** |Supportato |Supportato |Amsterdam, Hong Kong, Londra, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[CenturyLink](http://www.centurylink.com/business/enterprise/services/data-network/mpls-vpn.html)** |Presto disponibile |Presto disponibile |Silicon Valley |
| **China Telecom Global** |Supportato |Non supportato |Hong Kong |
| **[Cologix](http://www.cologix.com/solutions/cloud-connect/public-clouds/microsoft-cloud/)** |Supportato |Supportato |Dallas, Montreal, Toronto |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Supportato |Supportato |Amsterdam, Dublino, Londra, Parigi, Tokyo |
| **Comcast** |Supportato |Supportato |Chicago, Silicon Valley, Washington DC |
| **[Console](https://www.consoleconnect.com/partners/cloudsaas/)**| Supportato | Supportato |Silicon Valley, Toronto |
| **[CoreSite](http://www.coresite.com/solutions/cloud-services/public-cloud-providers/microsoft-azure-expressroute)** |Supportato |Supportato |Denver, Los Angeles, New York |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Supportato |Supportato |Amsterdam, Atlanta, Chicago, Dallas, Hong Kong, Londra, Los Angeles, Melbourne, New York, Osaka, Parigi, San Paolo, Seattle, Silicon Valley, Singapore, Sydney, Tokyo, Toronto, Washington DC |
| **euNetworks** |Supportato |Supportato |Amsterdam |
| **GÉANT** |Supportato |Supportato |Amsterdam |
| **[Global Cloud Xchange (GCX)] (http://globalcloudxchange.com/cloud-platform/cloud-x-fusion/cloud-x-fusion-for-azure/)** | Supportato| Supportato | Chennai |
| **[InterCloud](https://www.intercloud.com/)** |Supportato |Supportato |Amsterdam, Londra, Singapore, Washington DC |
| **[Internet Initiative Japan Inc. - IIJ](http://www.iij.ad.jp/en/news/pressrelease/2015/1216-2.html)** |Supportato |Supportato |Osaka, Tokyo |
| **Internet Solutions - Cloud Connect** |Supportato |Supportato |Amsterdam, Londra |
| **[Interxion](http://www.interxion.com/why-interxion/colocate-with-the-clouds/Microsoft-Azure/)** |Supportato |Supportato |Amsterdam, Londra, Parigi |
| **Jisc** |Supportato |Supportato |Londra |
| **KINX** |Supportato |Supportato |Seul |
| **[KPN](http://www.kpn.com/cloudconnect)** | Supportato | Supportato | Amsterdam | 
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Supportato |Supportato |Amsterdam, Chicago, Dallas, Las Vegas+, Londra, San Paolo, Seattle, Silicon Valley, Singapore, Washington DC |
| **LG CNS** |Supportato |Supportato |Busan |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Supportato |Supportato |Amsterdam, Chicago, Dallas, Hong Kong, Las Vegas, Londra, Los Angeles, Melbourne, Miami, New York, Quebec City, San Antonio, Seattle, Silicon Valley, Singapore, Sydney, Toronto, Washington DC |
| **MTN** |Supportato |Supportato |Londra |
| **[Dati di nuova generazione](http://www.nextgenerationdata.co.uk/ngd-cloud-gateway/)** |Supportato |Supportato |Newport(Wales) |
| **NEXTDC** |Supportato |Supportato |Melbourne, Sydney |
| **[NTT Communications](http://www.ntt.com/en/services/network/virtual-private-network.html)** |Supportato |Supportato |Londra, Los Angeles, Osaka, Singapore, Tokyo, Washington DC |
| **NTT SmartConnect** |Presto disponibile |Presto disponibile |Osaka |
| **[Orange](http://www.orange-business.com/en/products/business-vpn-galerie)** |Supportato |Supportato |Amsterdam, Hong Kong, Londra, Parigi+, Silicon Valley, Singapore, Sydney, Washington DC |
| **PCCW Global Limited** |Supportato |Supportato |Hong Kong - R.A.S. |
| **Sejong Telecom** |Supportato |Supportato |Seul |
| **[SIFY](http://telecom.sify.com/azure-expressroute.html)** |Supportato |Supportato |Chennai |
| **[SingTel](http://info.singtel.com/about-us/news-releases/singtel-provide-secure-private-access-microsoft-azure-public-cloud)** |Supportato |Supportato |Singapore |
| **[Softbank](http://www.softbank.jp/biz/cloud/cloud_access/direct_access_for_az/)** |Supportato |Supportato |Osaka, Tokyo |
| **[Tata Communications](http://www.tatacommunications.com/lp/izo/azure/azure_index.html)** |Supportato |Supportato |Amsterdam, Chennai, Hong Kong, Londra, Mumbai, Silicon Valley, Singapore, Washington DC |
| **[TeleCity Group](http://www.telecitygroup.com/investor-centre/news_details.htm?locid=03100500400b00d&xml)** |Supportato |Supportato |Amsterdam, Dublino, Londra |
| **[Telefonica](https://www.business-solutions.telefonica.com/es/enterprise/solutions/efficient-infrastructure/managed-voice-data-connectivity/)** |Supportato |Supportato |Amsterdam, San Paolo |
| **[Telehouse - KDDI](http://www.telehouse.net/solutions/cloud-services/cloud-link)** |Supportato |Supportato |Londra |
| **Telenor** |Supportato |Supportato |Amsterdam, Londra |
| **[Telstra Corporation](http://www.telstra.com.au/business-enterprise/network-services/networks/cloud-direct-connect/)** |Supportato |Supportato |Melbourne, Sydney |
| **[Telus](http://www.telus.com)** |Supportato |Supportato |Toronto |
| **[UOLDIVEO](http://www.uoldiveo.com.br/solucoes/cloud.html#rmcl)** |Presto disponibile |Presto disponibile |San Paolo+ |
| **[Verizon](http://www.verizonenterprise.com/products/networking/secure-cloud-interconnect/)** |Supportato |Supportato |Amsterdam, Chicago, Dallas, Hong Kong, Londra, Silicon Valley, Singapore, Sydney, Tokyo, Washington DC |
| **[Vodafone](http://www.vodafone.com/business/global-enterprise/global-connectivity/vodafone-ip-vpn-cloud-connect)** |Supportato |Non supportato |Londra |
| **[Zayo Group](http://www.zayo.com/solutions/industries/cloud-connectivity/microsoft-expressroute)** |Supportato |Supportato |Amsterdam, Chicago, Dallas+, Londra+, Los Angeles, New York, Silicon Valley, Toronto, Washington DC |

 **+** indica disponibile a breve

### Ambiente cloud nazionale
<a id="national-cloud-environment" class="xliff"></a>

### Cloud del governo degli Stati Uniti
<a id="us-government-cloud" class="xliff"></a>
| **Provider di servizi** | **Microsoft Azure** | **Office 365** | **Località** |
| --- | --- | --- | --- |
| **[AT&T NetBond](https://www.synaptic.att.com/clouduser/html/productdetail/ATT_NetBond.htm)** |Supportato |Supportato |Chicago, Washington DC |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Supportato |Supportato |Chicago, Dallas, New York, Seattle, Silicon Valley, Washington DC |
| **[Level 3 Communications](http://your.level3.com/LP=882?WT.tsrc=02192014LP882AzureVanityAzureText)** |Supportato |Supportato |Chicago, New York+, Silicon Valley, Washington DC |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Supportato | Supportato | Chicago, Dallas |
| **[Verizon](http://news.verizonenterprise.com/2014/04/secure-cloud-interconnect-solutions-enterprise/)** |Supportato |Supportato |Chicago, Dallas, New York, Washington DC |

### Cina
<a id="china" class="xliff"></a>
| **Provider di servizi** | **Microsoft Azure** | **Office 365** | **Località** |
| --- | --- | --- | --- |
| **China Telecom** |Supportato |Non supportato |Pechino, Shanghai |

Per altre informazioni, vedere [ExpressRoute in Cina](http://www.windowsazure.cn/home/features/expressroute/).

### Germania
<a id="germany" class="xliff"></a>
| **Provider di servizi** | **Microsoft Azure** | **Office 365** | **Località** |
| --- | --- | --- | --- |
| **[Colt](http://www.colt.net/uk/en/news/colt-announces-dedicated-cloud-access-for-microsoft-azure-services-en.htm)** |Supportato |Non supportato |Berlino+, Francoforte |
| **[Equinix](http://www.equinix.com/partners/microsoft-azure/)** |Supportato |Non supportato |Francoforte |
| **[e-shelter](https://www.e-shelter.de/en/microsoft-expressroutetm)** |Supportato |Non supportato |Berlino |
| **Interxion** |Supportato |Non supportato |Francoforte |
| **[Megaport](https://www.megaport.com/services/microsoft-expressroute/)** |Supportato  | Non supportato | Berlino |

## Connettività con provider di scambio
<a id="connectivity-through-exchange-providers" class="xliff"></a>

Se il provider di connettività non è incluso nelle sezioni precedenti, sarà comunque possibile creare una connessione.

* Contattare il provider di connettività per verificare se è connesso a uno qualsiasi degli scambi nella tabella precedente. Per altre informazioni sui servizi offerti dai provider di Exchange, è possibile esaminare i seguenti collegamenti. Alcuni provider di connettività sono già connessi agli scambi Ethernet.
  * [Cologix](http://www.cologix.com/)
  * [Console](https://www.consoleconnect.com/partners/cloudsaas/)
  * [CoreSite](http://www.coresite.com/)
  * [Equinix Cloud Exchange](http://www.equinix.com/services/interconnection-connectivity/cloud-exchange/)
  * [Interxion](http://www.interxion.com/products/interconnection/cloud-connect/)
  * [Megaport](https://www.megaport.com/services/microsoft-expressroute/)
  * [NEXTDC](http://www.nextdc.com/)
  * [TeleCity CloudIX](http://www.telecitygroup.com/colocation-services/cloud-ix.htm)
* Richiedere al provider di connettività di estendere la rete alla località di peering scelta.
  * Assicurarsi che il provider di connettività estenda la connettività con disponibilità elevata, in modo che non siano presenti singoli punti di errore.
* Ordinare a un circuito ExpressRoute con scambio come provider di connettività di connettersi a Microsoft.
  * Per configurare la connettività, eseguire la procedura illustrata in [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md) .

## Connettività con provider di servizi aggiuntivi
<a id="connectivity-through-additional-service-providers" class="xliff"></a>

| **Provider di connettività** | **Exchange** | **Località** |
| --- | --- | --- |
| **[1CLOUDSTAR](http://www.1cloudstar.com/service/cloudconnect-azure-expressroute/)** |Equinix |Singapore |
| **[Airgate Technologies, Inc.](http://airgate.ca/cloud-express/)** | Equinix, Cologix | Toronto, Montreal |
| **[Alaska Communications](http://www.alaskacommunications.com/For-Your-Business/Direct-Cloud-Service)** |Equinix |Seattle |
| **[Altice Business](https://golightpath.com/transport)** |Equinix |New York, Washington DC |
| **[Arteria Networks Corporation](https://arteria-net.com/business/service/cloud_access/sca/)** |Equinix |Tokyo |
| **[Bezeq International Ltd.](https://www.bezeqint.net/english)** | euNetworks | Londra |
| **[C3ntro Telecom](http://www.c3ntro.com/data/cloud-conectivity/)** | Equinix, Megaport | Dallas |
| **[Cogeco Peer 1](https://www.cogecopeer1.com/en/)**| Equinix | Montreal, Toronto |
| **[Cox Business](https://www.cox.com/business/networking/cloud-connectivity.html)** | Equinix | Dallas, Silicon Valley, Washington DC | 
| **[Data Foundry](https://www.datafoundry.com/services/cloud-connect)** | Megaport | Dallas |
| **[Epsilon Telecommunications Limited](http://www.epsilontel.com/data-connectivity/cloud-access/)** | Equinix | Londra, Singapore, Washington DC |
| **[Eurofiber](https://eurofiber.nl/microsoft-azure/)** | Equinix | Amsterdam |
| **[Esponenziale E](http://www.exponential-e.com/services/connectivity-services/cloud-connect-exchange)** | Equinix | Londra |
| **[Fastweb S.p.A](http://www.fastweb.it/grandi-aziende/connessione-voce-e-wifi/scheda-prodotto/rete-privata-virtuale/)** | Equinix | Amsterdam |
| **[Gtt Communications Inc](https://www.gtt.net/wp-content/uploads/2017/04/EtherCloud-Data-Sheet.pdf)** |Equinix | Washington DC |
| **[HSO](http://www.hso.co.uk/products/cloud-direct)** |Equinix | Londra, Slough |
| **LGA Telecom** |Equinix |Singapore|
| **[Lightower](http://www.lightower.com/network-solutions/cloud-connect/#microsoft-azure)** |Equinix | Chicago, New York, Washington DC |
| **[Macroview Telecom](http://www.macroview.com/en/scripts/catitem.php?catid=solution&sectionid=expressroute)** |Equinix |Hong Kong - R.A.S. |
| **[Macquarie Telecom Group](https://macquariegovernment.com/secure-cloud/secure-cloud-exchange/)** | Megaport | Sydney |
| **[Masergy](https://www.masergy.com/solutions/hybrid-networking/cloud-marketplace/microsoft-azure)** | Equinix | Washington DC |
| **[NexGen Networks](http://www.nexgen-net.com/nexgen-networks-direct-connect-microsoft-azure-expressroute.html)** | Interxion | Londra |
| **[Nianet](https://nianet.dk/produkter/internet/microsoft-expressroute)** |Telecity | Amsterdam, Francoforte |  
| **[QSC AG](https://www.qsc.de/de/produkte-loesungen/cloud-services-und-it-outsourcing/pure-enterprise-cloud/multi-cloud-management/azure-expressroute/)** |Interxion | Francoforte |  
| **Rogers** | Cologix, Equinix | Montreal, Toronto |
| **[Tamares Telecom](http://www.tamarestelecom.com/our-services/#Connectivity)** | Telecity | Londra | 
| **[ThinkTel](http://www.thinktel.ca/services/agile-ix-data/expressroute/)** | Equinix | Toronto | 
| **[Transtelco](http://www.transtelco.net/tcloud/microsoft)** |Equinix | Dallas, Los Angeles |  
| **[United Information Highway (UIH)](https://www.uih.co.th/en/internet-solution/cloud-direct/uih-cloud-direct-for-microsoft-azure-expressroute)**| Equinix | Singapore |
| **[Webair](https://www.webair.com/microsoft-express-route-partnership/)**| Megaport | New York |
| **[Windstream](http://www.windstreambusiness.com/solutions/cloud-services/cloud-and-managed-hosting-services)**| Equinix | Chicago, Silicon Valley, Washington DC |
| **Zain** |Equinix |Londra|
| **[Zertia](http://www.zertia.es/index.php/novedades)**| Level 3 | Madrid |
| **[Zirro](https://zirro.com/services/)**| Equinix | Toronto, Montreal |

## Connettività con provider di data center
<a id="connectivity-through-datacenter-providers" class="xliff"></a>
| **Provider** | **Exchange** |
| --- | --- |
| **[Cyrus One](https://cyrusone.com/enterprise-data-center-services/connectivity-and-interconnection/)** | Megaport |
| **[Digital Realty](https://www.digitalrealty.com/services/interconnection/service-exchange/)** | Megaport |
| **[EdgeConnex](http://www.edgeconnex.com/services/edge-data-centers-proximity-matters/)** | Megaport |
| **[RagingWire Data Centers](http://www.ragingwire.com/wholesale/wholesale-data-centers-worldwide-nexcenters)** | Console |
| **[T5 Datacenters](http://t5datacenters.com/network-cloud-connect/)** | Console |

## Connettività con le reti nazionali per la ricerca e l'istruzione
<a id="connectivity-through-national-research-and-education-networks-nren" class="xliff"></a>

| **Provider**|
| --- |
| **AARNET**| 
| **DeIC, tramite GÉANT**|
| **GARR, tramite GÉANT**|
| **GÉANT**|
| **HEAnet, tramite GÉANT**|
| **JISC**|
| **RedIRIS, tramite GÉANT**|
| **SINET**|
| **Surfnet, tramite GÉANT**|

* Se il provider di connettività non è elencato qui, verificare se è connesso a uno dei partner di scambio di ExpressRoute indicati sopra.

## Integratori di sistemi ExpressRoute
<a id="expressroute-system-integrators" class="xliff"></a>
L'abilitazione della connettività privata per soddisfare le proprie esigenze può risultare complessa, in base alle dimensioni della rete. Per ottenere supporto per l'onboarding in ExpressRoute, è possibile collaborare con uno degli Integratori di sistemi elencati nella seguente tabella.

| **Integratore di sistemi** | **Continente** |
| --- | --- |
| **[Altogee](http://www.altogee.be/expressroute)** | Europa |
| **[Avanade Inc.](http://www.avanade.com/)** | Asia, Europa, America del Nord, America del Sud |
| **[Bright Skies GmbH](http://bskies.io/expressroute)** | Europa
| **[Ensyst](http://www.ensyst.com.au)** | Asia
| **[Equinix Professional Services](http://www.equinix.com/services/consulting/)** | America del Nord |
| **[FlexManage](http://www.flexmanage.com/cloud)** | America del Nord |
| **[Inframon](http://www.inframon.com/partner/microsoft/)** | Europa |
| **[The IT Consultancy Group](http://itconsult.com.au/microsoft-expressroute)** | Australia |
| **[MOQdigital](http://www.moqdigital.com.au/insights/technical/network-connectivity-options-for-azure)** | Australia |
| **[MSG Services](https://www.msg-services.de/it-services/managed-services/cloud-outsourcing/)** | Europa (Germania) |
| **[Nelite](http://nelite.com/)** | Europa |
| **[New Signature](https://www.newsignature.co.uk/)** | Europa |
| **[OneAs1a](http://www.oneas1a.com/express-connect-any-cloud-ecac)** | Asia |
| **[Orange Networks](https://orange-networks.com/blog/88-azureexpressroute)** | Europa |
| **[Perficient](http://www.perficient.com/Partners/Microsoft/Cloud/Azure-ExpressRoute)** | America del Nord |
| **[Presidio](http://info.presidio.com/microsoft-azure-expressroute)** | America del Nord |
| **[sol-tec](http://www.sol-tec.com/Technologies)** | Europa |
| **[Vigilant.IT](https://vigilant.it/expressroute)** | Australia |


## Passaggi successivi
<a id="next-steps" class="xliff"></a>
* Per altre informazioni su ExpressRoute, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md).
* Verificare che vengano soddisfatti tutti i prerequisiti. Vedere [Prerequisiti per ExpressRoute](expressroute-prerequisites.md).

<!--Image References-->
[0]: ./media/expressroute-locations/expressroute-locations-map.png "Mappa delle località"

