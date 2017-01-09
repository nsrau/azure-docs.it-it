---
title: Requisiti di routing per ExpressRoute | Documentazione Microsoft
description: Questa pagina illustra i requisiti dettagliati per la configurazione e la gestione del routing per i circuiti ExpressRoute.
documentationcenter: na
services: expressroute
author: osamazia
manager: ganesr
editor: 
ms.assetid: 5b382e79-fa3f-495a-a764-c5ff86af66a2
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/12/2016
ms.author: osamam
translationtype: Human Translation
ms.sourcegitcommit: 6adb1dd25c24b18b834dd921c2586ef29d56dc81
ms.openlocfilehash: e04763fa711eed4c699f2bc3f20834849dfe52e0


---
# <a name="expressroute-routing-requirements"></a>Requisiti per il routing di ExpressRoute
Per connettersi ai servizi cloud Microsoft con ExpressRoute, è necessario configurare e gestire il routing. Alcuni provider di connettività offrono la configurazione e la gestione del routing come servizio gestito. Rivolgersi al proprio provider di connettività per verificare se viene offerto questo servizio. Se non è offerto, è necessario rispettare i requisiti seguenti. 

Vedere l'articolo [Circuiti e domini di routing ExpressRoute](expressroute-circuit-peerings.md) per una descrizione delle sessioni di routing da configurare per facilitare la connettività.

> [!NOTE]
> Microsoft non supporta alcun protocollo di ridondanza router (ad esempio, HSRP e VRRP) per le configurazioni con disponibilità elevata. Per la disponibilità elevata, Microsoft si avvale invece di una coppia ridondante di sessioni BGP per peering.
> 
> 

## <a name="ip-addresses-used-for-peerings"></a>Indirizzi IP usati per i peering
Per configurare il routing tra la propria rete e i router perimetrali Enterprise di Microsoft (MSEE, Microsoft Enterprise Edge), sarà necessario riservare alcuni blocchi di indirizzi IP. Questa sezione fornisce un elenco di requisiti e descrive le regole che indicano la modalità di acquisizione e di utilizzo di questi indirizzi IP.

### <a name="ip-addresses-used-for-azure-private-peering"></a>Indirizzi IP usati per il peering privato di Azure
Per configurare i peering, è possibile usare sia indirizzi IP privati sia indirizzi IP pubblici. L'intervallo di indirizzi usato per configurare le route non deve sovrapporsi agli intervalli di indirizzi usati per creare le reti virtuali in Azure. 

* È necessario riservare una subnet /29 o due subnet /30 per le interfacce di routing.
* Le subnet usate per il routing possono essere costituite sia da indirizzi IP privati sia da indirizzi IP pubblici.
* Le subnet non devono essere in conflitto con l'intervallo riservato dal cliente per l'uso in Microsoft Cloud.
* Se viene usata una subnet /29, questa verrà divisa in due subnet /30. 
  * La prima subnet /30 verrà usata per il collegamento primario e la seconda per il collegamento secondario.
  * Per ogni subnet /30, è necessario usare il primo indirizzo IP della subnet /30 nel router. Microsoft userà il secondo indirizzo IP della subnet /30 per configurare una sessione BGP.
  * È necessario configurare entrambe le sessioni BGP affinché il [contratto di servizio per la disponibilità](https://azure.microsoft.com/support/legal/sla/) sia valido.  

#### <a name="example-for-private-peering"></a>Esempio per il peering privato
Se si sceglie di usare la subnet a.b.c.d/29 per configurare il peering, verrà divisa in due subnet /30. L'esempio seguente mostra come viene usata la subnet a.b.c.d/29. 

La subnet a.b.c.d/29 verrà divisa in a.b.c.d/30 e a.b.c.d+4/30 e passata a Microsoft tramite le API di provisioning. Si userà l'indirizzo a.b.c.d+1 come indirizzo IP VRF per il router PE (Provider Edge) primario e Microsoft userà l'indirizzo a.b.c.d+2 come indirizzo IP VRF per il router MSEE primario. Si userà l'indirizzo a.b.c.d+5 come indirizzo IP VRF per il router PE (Provider Edge) secondario e Microsoft userà l'indirizzo a.b.c.d+6 come indirizzo IP VRF per il router MSEE secondario.

Si consideri un caso in cui si seleziona l'indirizzo 192.168.100.128/29 per configurare il peering privato. L'intervallo 192.168.100.128/29 include gli indirizzi IP da 192.168.100.128 a 192.168.100.135, tra cui:

* 192.168.100.128/30 che verrà assegnato a link1, con il provider che usa 192.168.100.129 e Microsoft che usa 192.168.100.130.
* 192.168.100.132/30 che verrà assegnato a link2, con il provider che usa 192.168.100.133 e Microsoft che usa 192.168.100.134.

### <a name="ip-addresses-used-for-azure-public-and-microsoft-peering"></a>Indirizzi IP usati per il peering Microsoft e il peering pubblico di Azure
Per configurare le sessioni BGP è necessario usare indirizzi IP pubblici di proprietà. Microsoft deve essere in grado di verificare la proprietà degli indirizzi IP tramite il Routing Internet Registry e l'Internet Routing Registry. 

* È necessario usare una subnet /29 univoca o due subnet /30 per configurare il peering BGP per ogni peering per circuito ExpressRoute (se è presente più di un circuito). 
* Se viene usata una subnet /29, questa verrà divisa in due subnet /30. 
  * La prima subnet /30 verrà usata per il collegamento primario e la seconda per il collegamento secondario.
  * Per ogni subnet /30, è necessario usare il primo indirizzo IP della subnet /30 nel router. Microsoft userà il secondo indirizzo IP della subnet /30 per configurare una sessione BGP.
  * È necessario configurare entrambe le sessioni BGP affinché il [contratto di servizio per la disponibilità](https://azure.microsoft.com/support/legal/sla/) sia valido.

## <a name="public-ip-address-requirement"></a>Requisito dell'indirizzo IP pubblico
### <a name="private-peering"></a>Peering privato
È possibile scegliere di usare gli indirizzi IPv4 pubblici o privati per il peering privato. Offriamo un isolamento end-to-end del traffico di modo che la sovrapposizione degli indirizzi con altri clienti non si verifica in caso di peering privato. Questi indirizzi non vengono annunciati su Internet. 

### <a name="public-peering"></a>Peering pubblico
Il percorso di peering pubblico di Azure consente di connettersi a tutti i servizi ospitati in Azure tramite i relativi indirizzi IP pubblici. Sono inclusi i servizi elencati nell'articolo [Domande frequenti su ExpressRoute](expressroute-faqs.md) e tutti i servizi ospitati da ISV in Microsoft Azure. La connettività ai servizi di Microsoft Azure nel peering pubblico viene sempre avviata dalla propria rete nella rete Microsoft. È necessario usare gli indirizzi IP pubblici per il traffico destinato alla rete Microsoft.

### <a name="microsoft-peering"></a>Peering Microsoft
Il percorso di peering Microsoft consente di connettersi a servizi cloud Microsoft che non sono supportati tramite il percorso di peering pubblico di Azure. L'elenco dei servizi include servizi Office 365 quali Exchange Online, SharePoint Online, Skype for Business e CRM Online. Microsoft supporta la connettività bidirezionale nel peering Microsoft. Il traffico destinato ai servizi cloud Microsoft nel peering pubblico deve usare indirizzi IPv4 pubblici validi per poter accedere alla rete Microsoft.

Assicurarsi che l'indirizzo IP e il numero AS siano registrati all'utente in uno dei registri elencati di seguito.

* [ARIN](https://www.arin.net/)
* [APNIC](https://www.apnic.net/)
* [AFRINIC](https://www.afrinic.net/)
* [LACNIC](http://www.lacnic.net/)
* [RIPENCC](https://www.ripe.net/)
* [RADB](http://www.radb.net/)
* [ALTDB](http://altdb.net/)

> [!IMPORTANT]
> Gli indirizzi IP pubblici annunciati su Microsoft tramite ExpressRoute non devono essere annunciati su Internet, altrimenti potrebbe interrompersi la connettività con altri servizi Microsoft. Tuttavia, gli indirizzi IP pubblici usati dai server nella rete che comunica con gli endpoint di Office 365 nell'ambiente Microsoft possono essere annunciati tramite ExpressRoute. 
> 
> 

## <a name="dynamic-route-exchange"></a>Scambio di route dinamico
Lo scambio di routing avviene tramite il protocollo eBGP. Vengono stabilite sessioni eBGP tramite i router MSEE e i propri router. L'autenticazione delle sessioni eBGP non è un requisito. Se necessario, è possibile configurare un hash MD5. Vedere il [Configura routing](expressroute-howto-routing-classic.md) e il [Circuito flussi di lavoro di provisioning e circuito stati](expressroute-workflows.md) per informazioni sulla configurazione delle sessioni BGP.

## <a name="autonomous-system-numbers"></a>Numeri AS (Autonomous System)
Microsoft userà il numero AS 12076 per il peering pubblico di Azure, il peering privato di Azure e il peering Microsoft. Gli ASN da 65515 a 65520 sono riservati per uso interno. Sono supportati sia i numeri AS a 16 bit che a 32 bit.

Non sono previsti requisiti per la simmetria del trasferimento dei dati. I percorsi di andata e ritorno possono transitare attraverso coppie di router diverse. Le route identiche devono essere annunciate da entrambi i lati in più coppie di circuiti appartenenti all'utente. Non è necessario che le metriche delle route siano identiche.

## <a name="route-aggregation-and-prefix-limits"></a>Limiti per i prefissi e l'aggregazione di route
Sono supportati fino a 4000 prefissi annunciati a Microsoft tramite il peering privato di Azure. Questo numero può essere aumentato fino a 10.000 prefissi se è abilitato il componente aggiuntivo ExpressRoute Premium. Per il peering pubblico di Azure e il peering Microsoft sono accettati fino a 200 prefissi per sessione BGP. 

La sessione BGP verrà rimossa se il numero di prefissi supera il limite previsto. Verranno accettate route predefinite solo sul collegamento del peering privato. Il provider deve filtrare le route predefinite e gli indirizzi IP privati (RFC 1918) dai percorsi per il peering pubblico di Azure e il peering Microsoft. 

## <a name="transit-routing-and-cross-region-routing"></a>Routing di transito e routing tra aree
Non è possibile configurare ExpressRoute come router di transito. Per i servizi di routing di transito è necessario rivolgersi al provider di connettività.

## <a name="advertising-default-routes"></a>Annuncio delle route predefinite
Le route predefinite sono consentite solo nelle sessioni di peering privato di Azure. In questo caso, tutto il traffico dalle reti virtuali associate verrà instradato alla rete dell'utente. L'annuncio delle route predefinite nel peering privato comporta il blocco del percorso Internet da Azure. In questo caso, per indirizzare il traffico da e verso Internet per i servizi ospitati in Azure, sarà necessario usare il proprio router CE (Corporate Edge). 

 Per abilitare la connettività ad altri servizi di Azure e ai servizi di infrastruttura, è necessario verificare che:

* Il peering pubblico di Azure sia abilitato per il routing del traffico a endpoint pubblici
* Si usi il routing definito dall'utente per consentire la connettività Internet per ogni subnet che la richiede.

> [!NOTE]
> L'annuncio delle route predefinite interromperà l'attivazione della licenza di Windows e di altre macchine virtuali. Per risolvere questo problema, seguire le istruzioni fornite [qui](http://blogs.msdn.com/b/mast/archive/2015/05/20/use-azure-custom-routes-to-enable-kms-activation-with-forced-tunneling.aspx) .
> 
> 

## <a name="support-for-bgp-communities"></a>Supporto per le community BGP
Questa sezione fornisce una panoramica che illustra come vengono usate le community BGP con ExpressRoute. Microsoft annuncerà le route nei percorsi per il peering pubblico e il peering Microsoft con route contrassegnate con i valori della community appropriati. I motivi per questa operazione e i dettagli sui valori della community sono descritti di seguito. Microsoft, tuttavia, non rispetterà eventuali valori della community contrassegnati in base a route annunciate a Microsoft.

Se si effettua la connessione a Microsoft tramite ExpressRoute presso una qualsiasi località di peering all'interno di un'area geopolitica, sarà possibile accedere a tutti i servizi cloud Microsoft in tutte le aree all'interno dell'area geopolitica. 

Ad esempio, se ci si connette a Microsoft tramite ExpressRoute ad Amsterdam, sarà possibile accedere a tutti i servizi cloud Microsoft ospitati nell'Europa settentrionale e nell'Europa occidentale. 

Per un elenco dettagliato delle aree geopolitiche, delle aree di Azure associate e delle località di peering ExpressRoute corrispondenti, vedere la pagina [Partner e località i peering per ExpressRoute](expressroute-locations.md) .

È possibile acquistare più di un circuito ExpressRoute per area geopolitica. Un maggior numero di connessioni offre vantaggi significativi in termini di disponibilità elevata, grazie alla ridondanza geografica. Se si hanno più circuiti ExpressRoute, si riceverà lo stesso set di prefissi annunciati da Microsoft nei percorsi per il peering pubblico e per il peering Microsoft. Questo significa che saranno disponibili più percorsi dalla propria rete a Microsoft. In questo caso, all'interno della rete potrebbero essere prese decisioni di routing non ottimali, che possono, a loro volta, determinare esperienze di connettività non ottimali a vari servizi. 

Microsoft contrassegnerà i prefissi annunciati tramite il peering pubblico e il peering Microsoft con i valori della community BGP appropriati indicanti l'area in cui sono ospitati i prefissi. Per prendere le decisioni di routing appropriate e offrire un [servizio di routing ottimale ai clienti](expressroute-optimize-routing.md), è possibile usare i valori di Community.

| **Area di Microsoft Azure** | **Valore della community BGP** |
| --- | --- |
| **America del Nord** | |
| Stati Uniti orientali |12076:51004 |
| Stati Uniti orientali 2 |12076:51005 |
| Stati Uniti occidentali |12076:51006 |
| Stati Uniti occidentali 2 |12076:51026 |
| Stati Uniti centro-occidentali |12076:51027 |
| Stati Uniti centro-settentrionali |12076:51007 |
| Stati Uniti centro-meridionali |12076:51008 |
| Stati Uniti centrali |12076:51009 |
| Canada centrale |12076:51020 |
| Canada orientale |12076:51021 |
| **America del Sud** | |
| Brasile meridionale |12076:51014 |
| **Europa** | |
| Europa settentrionale |12076:51003 |
| Europa occidentale |12076:51002 |
| Regno Unito meridionale | 12076:51024 |
| Regno Unito occidentale | 12076:51025 |
| **Asia Pacifico** | |
| Asia orientale |12076:51010 |
| Asia sudorientale |12076:51011 |
| **Giappone** | |
| Giappone orientale |12076:51012 |
| Giappone occidentale |12076:51013 |
| **Australia** | |
| Australia orientale |12076:51015 |
| Australia sudorientale |12076:51016 |
| **India** | |
| India meridionale |12076:51019 |
| India occidentale |12076:51018 |
| India centrale |12076:51017 |

Tutte le route annunciate da Microsoft verranno contrassegnate con il valore della community appropriato. 

> [!IMPORTANT]
> I prefissi globali verranno contrassegnati con un valore della community appropriato e saranno annunciati solo quando è abilitato il componente aggiuntivo ExpressRoute Premium.
> 
> 

Microsoft contrassegnerà anche i prefissi in base al servizio di appartenenza. Questo si applica solo al peering Microsoft. La tabella seguente fornisce il mapping del servizio al valore della community BGP.

| **Servizio** | **Valore della community BGP** |
| --- | --- |
| Exchange Online |12076:5010 |
| SharePoint Online |12076:5020 |
| Skype for Business Online |12076:5030 |
| CRM Online |12076:5040 |
| Altri servizi online di Office 365 |12076:5100 |

> [!NOTE]
> Microsoft non riconosce eventuali valori di BGP Community impostati sulle route pubblicate su Microsoft.
> 
> 

### <a name="bgp-community-support-in-national-clouds-preview"></a>Supporto della Community BGP nei cloud nazionali (anteprima)

| **Area di Azure per cloud nazionali**| **Valore della community BGP** |
| --- | --- |
| **US Government** |  |
| US Gov Iowa | 12076:51109 |
| US Gov Virginia | 12076:51105 |


| **Servizio dei cloud nazionali** | **Valore della community BGP** |
| --- | --- |
| **US Government** |  |
| Exchange Online |12076:5110 |
| SharePoint Online |12076:5120 |
| Skype for Business Online |12076:5130 |
| CRM Online |12076:5140 |
| Altri servizi online di Office 365 |12076:5200 |

## <a name="next-steps"></a>Passaggi successivi
* Configurare la connessione ExpressRoute.
  
  * [Creare un circuito ExpressRoute per il modello di distribuzione classica](expressroute-howto-circuit-classic.md) o [Creare e modificare un circuito ExpressRoute usando Azure Resource Manager](expressroute-howto-circuit-arm.md)
  * [Configurare il routing per il modello di distribuzione classica](expressroute-howto-routing-classic.md) o [Configurare il routing per il modello di distribuzione Resource Manager](expressroute-howto-routing-arm.md)
  * [Collegare una rete virtuale classica a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md) o [Collegare una rete virtuale di Azure Resource Manager a un circuito ExpressRoute](expressroute-howto-linkvnet-arm.md)




<!--HONumber=Dec16_HO2-->


