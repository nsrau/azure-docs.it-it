---
title: Introduzione a ExpressRoute | Documentazione Microsoft
description: Questa pagina fornisce una panoramica del servizio ExpressRoute, incluso il funzionamento di una connessione ExpressRoute.
documentationcenter: na
services: expressroute
author: cherylmc
manager: carmonm
editor: 
ms.assetid: fd95dcd5-df1d-41d6-85dd-e91d0091af05
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/10/2016
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 57b95d5018fa197096992f7cd255a54501875dea


---
# <a name="expressroute-technical-overview"></a>Panoramica tecnica relativa a ExpressRoute
Microsoft Azure ExpressRoute consente di estendere le reti locali nel cloud Microsoft tramite una connessione privata dedicata fornita da un provider di connettività. Con ExpressRoute è possibile stabilire connessioni ai servizi cloud Microsoft, come Microsoft Azure, Office 365 e CRM Online. La connettività può essere stabilita da una rete (IP VPN) any-to-any, da una rete Ethernet punto a punto o da una Cross Connection virtuale tramite un provider di connettività presso una struttura di condivisione del percorso. Le connessioni ExpressRoute non sfruttano la rete Internet pubblica. In questo modo possono offrire un livello di sicurezza superiore, maggiore affidabilità, velocità più elevate e minori latenze rispetto alle connessioni Internet tradizionali.

![](./media/expressroute-introduction/expressroute-basic.png)

**I vantaggi principali includono:**

* Connettività di livello 3 tra la rete locale e il cloud Microsoft tramite un provider di connettività. La connettività può essere stabilita da una rete (IPVPN) any-to-any, da una connessione Ethernet punto a punto o con una Cross Connection virtuale tramite scambio Ethernet.
* Connettività ai servizi cloud Microsoft in tutte le aree all'interno dell'area geopolitica.
* Connettività globale ai servizi Microsoft di tutte le aree con il componente aggiuntivo ExpressRoute Premium.
* Routing dinamico tra la rete e Microsoft con protocolli standard del settore (BGP).
* Ridondanza incorporata in ogni località di peering per una maggiore affidabilità.
* [Contratto di servizio](https://azure.microsoft.com/support/legal/sla/)per i tempi di attività delle connessioni.
* Qualità del servizio e supporto di più classi di servizio per applicazioni speciali, ad esempio Skype for Business.

Per altre informazioni, vedere le [Domande frequenti su ExpressRoute](expressroute-faqs.md) .

## <a name="a-namehowtoconnectahow-can-i-connect-my-network-to-microsoft-using-expressroute"></a><a name="howtoconnect"></a>Come connettere una rete a Microsoft tramite ExpressRoute
Per creare una connessione tra la rete locale e Microsoft Cloud è possibile procedere in tre modi diversi:

### <a name="co-located-at-a-cloud-exchange"></a>Percorso condiviso in una struttura Cloud Exchange
Nel caso di percorso condiviso in una struttura con scambio cloud, è possibile ordinare Cross Connection virtuali con il cloud Microsoft tramite lo scambio Ethernet del provider di condivisione del percorso. I provider di condivisione del percorso possono fornire Cross Connection di livello 2 oppure gestite di livello 3 tra l'infrastruttura nella struttura di condivisione percorso e il cloud Microsoft.

### <a name="point-to-point-ethernet-connections"></a>Connessioni Ethernet da punto a punto
È possibile connettere i data center o gli uffici locali al cloud Microsoft tramite collegamenti Ethernet punto a punto. I provider Ethernet punto a punto forniscono connessioni di livello 2 o connessioni gestite di livello 3 tra la sede dell'utente e il cloud Microsoft.

### <a name="any-to-any-ipvpn-networks"></a>Reti (IPVPN) any-to-any
È possibile integrare una rete WAN con il cloud Microsoft. I provider IPVPN (in genere VPN MPLS) forniscono connettività any-to-any tra le succursali e i data center. Il cloud Microsoft può essere interconnesso a una rete WAN in modo che abbia l'aspetto di qualsiasi altra succursale. I provider WAN offrono in genere connettività gestita di livello 3. Le caratteristiche e le funzionalità di ExpressRoute sono identiche in tutti i modelli di connettività sopra descritti. 

I provider di connettività possono fornire uno o più modelli di connettività. È possibile interagire con il provider di connettività per scegliere il modello più appropriato.

![](./media/expressroute-introduction/expressroute-connectivitymodels.png)

## <a name="expressroute-features"></a>Funzionalità di ExpressRoute
ExpressRoute supporta le funzionalità e le caratteristiche seguenti: 

### <a name="layer-3-connectivity"></a>Connettività di livello 3
Microsoft usa il protocollo di routing dinamico di standard del settore (BGP) per lo scambio di route tra la rete locale, le istanze in Azure e gli indirizzi pubblici Microsoft.  Vengono stabilite più sessioni BGP con la rete per profili di traffico diversi. Per altre informazioni, vedere l'articolo relativo [al circuito e ai domini di routing ExpressRoute](expressroute-circuit-peerings.md) .

### <a name="redundancy"></a>Ridondanza
Ogni circuito ExpressRoute è costituito da due connessioni a due router perimetrali Microsoft Enterprise (MSEE) dal provider di connettività o dal perimetro della rete. Microsoft richiederà una connessione BGP doppia dal provider di connettività/lato dell'utente, ognuna per ciascun MSEE. È possibile scegliere di non distribuire dispositivi ridondanti/circuiti Ethernet sul lato dell'utente. I provider di connettività tuttavia usano dispositivi ridondanti per garantire che le connessioni vengono passate a Microsoft in modo ridondante. Una connettività di livello 3 ridondante è un requisito per la validità del [contratto di servizio](https://azure.microsoft.com/support/legal/sla/) . 

### <a name="connectivity-to-microsoft-cloud-services"></a>Connettività ai servizi cloud Microsoft
[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

Le connessioni ExpressRoute abilitano l'accesso ai servizi seguenti:

* Servizi di Microsoft Azure
* Servizi di Microsoft Office 365
* Servizi di Microsoft CRM Online 

Per un elenco dettagliato dei servizi supportati per ExpressRoute, visitare la pagina [Domande frequenti su ExpressRoute](expressroute-faqs.md) .

### <a name="connectivity-to-all-regions-within-a-geopolitical-region"></a>Connettività a tutte le aree all'interno di un'area geopolitica
È possibile connettersi a Microsoft in una delle [località di peering](expressroute-locations.md) e accedere a tutte le aree all'interno dell'area geopolitica. 

Se ad esempio si esegue la connessione a Microsoft da Amsterdam tramite ExpressRoute, sarà possibile accedere a tutti i servizi cloud Microsoft ospitati nell'Europa settentrionale e occidentale. Per una panoramica delle aree geopolitiche, delle aree cloud Microsoft associate e delle località di peering ExpressRoute corrispondenti, vedere l'articolo [Partner e località di peering per Azure ExpressRoute](expressroute-locations.md) .

### <a name="global-connectivity-with-expressroute-premium-add-on"></a>Connettività globale con il componente aggiuntivo ExpressRoute Premium
È possibile abilitare la funzionalità componente aggiuntivo ExpressRoute Premium per estendere la connettività tra confini geopolitici. Se ad esempio si esegue la connessione a Microsoft da Amsterdam tramite ExpressRoute, sarà possibile accedere a tutti i servizi cloud Microsoft ospitati in tutte le aree del mondo (cloud nazionali esclusi). È possibile accedere ai servizi distribuiti nell'America del Sud o in Australia nello stesso modo in cui si accede alle aree dell'Europa settentrionale e occidentale.

### <a name="rich-connectivity-partner-ecosystem"></a>Ecosistema di partner di connettività avanzata
ExpressRoute presenta un ecosistema di partner di connettività e SI in continua crescita. Per le informazioni più aggiornate, vedere l'articolo [Partner e località di peering per Azure ExpressRoute](expressroute-locations.md) .

### <a name="connectivity-to-national-clouds"></a>Connettività a cloud nazionali
Microsoft gestisce ambienti cloud isolati per aree geopolitiche speciali e segmenti di clienti. Per un elenco dei cloud nazionali e dei provider, vedere l'articolo [Partner e località di peering per Azure ExpressRoute](expressroute-locations.md) .

### <a name="supported-bandwidth-options"></a>Opzioni di larghezze di banda supportate
È possibile acquistare circuiti ExpressRoute per un'ampia gamma di larghezze di banda. Di seguito è riportato l'elenco delle larghezze di banda supportate. Controllare presso il provider di connettività l'elenco delle larghezze di banda supportate fornite.

* 50 Mbps
* 100 Mbps
* 200 Mbps
* 500 Mbps
* 1 Gbps
* 2 Gbps
* 5 Gbps
* 10 Gbps

### <a name="dynamic-scaling-of-bandwidth"></a>Scalabilità dinamica della larghezza di banda
È possibile aumentare la larghezza di banda del circuito ExpressRoute (in base ad approssimazioni ottimali) senza dover chiudere le connessioni. 

### <a name="flexible-billing-models"></a>Modelli di fatturazione flessibili
È possibile selezionare il modello di fatturazione più adatto per le proprie esigenze. Scegliere tra i modelli di fatturazione elencati di seguito. Per altri dettagli, vedere la pagina [Domande frequenti su ExpressRoute](expressroute-faqs.md) . 

* **Dati illimitati**. Il circuito ExpressRoute viene addebitato in base a una tariffa mensile che include il trasferimento di tutti i dati in entrata e in uscita. 
* **Dati a consumo**. Il circuito ExpressRoute viene addebitato in base a una tariffa mensile. Nella tariffa è incluso il trasferimento di tutti i dati in entrata. Il trasferimento dei dati in uscita invece viene addebitato per GB di dati trasferiti. Le tariffe per il trasferimento dei dati variano in base all'area.
* **Componente aggiuntivo ExpressRoute Premium**. ExpressRoute Premium è un componente aggiuntivo per il circuito ExpressRoute. Offre le funzionalità seguenti: 
  * Estensione dei limiti di route per i peering pubblico e privato di Azure da 4.000 a 10.000 route.
  * Connettività globale per i servizi. Un circuito ExpressRoute creato in un'area qualsiasi (esclusi i cloud nazionali) potrà accedere alle risorse di tutte le altre aree del mondo. Ad esempio, una rete virtuale creata nell'Europa occidentale è accessibile attraverso un circuito ExpressRoute fornito nella Silicon Valley.
  * Aumento del numero di collegamenti di reti virtuali consentiti per circuito ExpressRoute da 10 a un numero superiore, a seconda della larghezza di banda del circuito.

## <a name="next-steps"></a>Passaggi successivi
* Informazioni sulle connessioni e i domini di routing ExpressRoute. Vedere [Circuiti e domini di routing ExpressRoute](expressroute-circuit-peerings.md).
* Trovare un provider di servizi. Vedere [Partner e località di peering per Azure ExpressRoute](expressroute-locations.md).
* Verificare che vengano soddisfatti tutti i prerequisiti. Vedere [Prerequisiti per ExpressRoute](expressroute-prerequisites.md).
* Fare riferimento ai requisiti per [routing](expressroute-routing.md), [NAT](expressroute-nat.md) e [QoS](expressroute-qos.md).
* Configurare la connessione ExpressRoute.
  * [Creare un circuito ExpressRoute](expressroute-howto-circuit-classic.md)
  * [Configurare il routing](expressroute-howto-routing-classic.md)
  * [Collegare una rete virtuale a un circuito ExpressRoute](expressroute-howto-linkvnet-classic.md)




<!--HONumber=Dec16_HO2-->


