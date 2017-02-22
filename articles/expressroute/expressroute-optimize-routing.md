---
title: Ottimizzare il routing in ExpressRoute | Documentazione Microsoft
description: "Questa pagina fornisce informazioni dettagliate su come ottimizzare il routing quando un cliente ha a disposizione più circuiti ExpressRoute per la connessione tra Microsoft e la rete aziendale del cliente."
documentationcenter: na
services: expressroute
author: charwen
manager: carmonm
editor: 
ms.assetid: fca53249-d9c3-4cff-8916-f8749386a4dd
ms.service: expressroute
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/27/2017
ms.author: charwen
translationtype: Human Translation
ms.sourcegitcommit: 1b26e82f862a3b2149024d863b907899e14e7d86
ms.openlocfilehash: 404929cf0def75d92d8bb6de8b41be3aecced458


---
# <a name="optimize-expressroute-routing"></a>Ottimizzare il routing in ExpressRoute
In presenza di più circuiti ExpressRoute sono disponibili più percorsi per connettersi a Microsoft. Il routing può quindi risultare non ottimale, ovvero è possibile che il traffico usi un percorso più lungo per raggiungere Microsoft e da Microsoft la rete del cliente. Più lungo è il percorso di rete, maggiore sarà la latenza che ha un impatto diretto sull'esperienza utente e sulle prestazioni dell'applicazione. Questo articolo descrive il problema e illustra come ottimizzare il routing con tecnologie di routing standard.

## <a name="suboptimal-routing-from-customer-to-microsoft"></a>Routing non ottimale dal cliente a Microsoft
Per esaminare il problema di routing si userà un esempio. Si supponga di avere due sedi negli Stati Uniti: una a Los Angeles e una a New York. Gli uffici sono connessi tramite una rete WAN (Wide Area Network), che può essere la propria rete backbone o la VPN IP del provider di servizi. Sono disponibili due circuiti ExpressRoute, uno negli Stati Uniti occidentali e uno negli Stati Uniti orientali, anch'essi connessi tramite la rete WAN. Naturalmente, per la connessione alla rete Microsoft esistono due percorsi. Si supponga ora di avere una distribuzione di Azure, ad esempio il servizio app di Azure, negli Stati Uniti occidentali e negli Stati Uniti orientali. Si vogliono connettere gli utenti di Los Angeles alla distribuzione di Azure negli Stati Uniti occidentali e gli utenti di New York alla distribuzione di Azure negli Stati Uniti orientali perché, secondo quanto annunciato dall'amministratore del servizio, per assicurare esperienze ottimali è consigliabile che gli utenti di ogni ufficio accedano ai servizi di Azure nelle vicinanze. Sfortunatamente, il piano funziona correttamente per gli utenti della costa orientale, ma non per quelli della costa occidentale. Di seguito è riportata la causa del problema. In ogni circuito ExpressRoute vengono pubblicati sia il prefisso di Azure negli Stati Uniti orientali (23.100.0.0/16) che il prefisso di Azure negli Stati Uniti occidentali (13.100.0.0/16). Se non si conosce l'area di provenienza di un prefisso, non si potrà differenziarne la gestione. Ritenendo che entrambi i prefissi siano più vicini agli Stati Uniti orientali rispetto agli Stati Uniti occidentali, la rete WAN potrebbe indirizzare gli utenti di entrambi gli uffici al circuito ExpressRoute negli Stati Uniti orientali. Molti utenti nell'ufficio di Los Angeles avranno di conseguenza un'esperienza insoddisfacente.

![](./media/expressroute-optimize-routing/expressroute-case1-problem.png)

### <a name="solution-use-bgp-communities"></a>Soluzione: usare BGP Community
Per ottimizzare il routing per gli utenti di entrambi gli uffici, è necessario sapere quale prefisso proviene da Azure negli Stati Uniti occidentali e quale da Azure negli Stati Uniti orientali. Queste informazioni vengono codificate con i [valori di BGP Community](expressroute-routing.md). È stato assegnato un valore di BGP Community univoco per ogni area di Azure, ad esempio "12076:51004" per gli Stati Uniti orientali, "12076:51006" per gli Stati Uniti occidentali. Dopo aver appreso da quale area di Azure proviene ogni prefisso, si può scegliere il circuito ExpressRoute da configurare come preferito. Poiché si usa BGP per lo scambio di informazioni di routing, è possibile usare il valore di BGP relativo alla preferenza locale per determinare il routing. In questo esempio è possibile assegnare un valore di preferenza locale 13.100.0.0/16 più alto negli Stati Uniti occidentali di quello negli Stati Uniti orientali e, in modo analogo, un valore di preferenza locale 23.100.0.0/16 più alto negli Stati Uniti orientali rispetto a quello negli Stati Uniti occidentali. Questa configurazione garantirà che, quando sono disponibili entrambi i percorsi per connettersi a Microsoft, gli utenti di Los Angeles useranno il circuito ExpressRoute negli Stati Uniti occidentali per connettersi ad Azure negli Stati Uniti occidentali, mentre gli utenti di New York useranno il circuito ExpressRoute negli Stati Uniti orientali per connettersi ad Azure negli Stati Uniti orientali. Il routing è ottimizzato su entrambi i lati. 

![](./media/expressroute-optimize-routing/expressroute-case1-solution.png)

## <a name="suboptimal-routing-from-microsoft-to-customer"></a>Routing non ottimale da Microsoft al cliente
Ecco un altro esempio in cui le connessioni da Microsoft usano un percorso più lungo per raggiungere la rete del cliente. In questo caso, si usano i server di Exchange in locale ed Exchange Online in un [ambiente ibrido](https://technet.microsoft.com/library/jj200581%28v=exchg.150%29.aspx). Gli uffici sono connessi a una rete WAN. Si annunciano a Microsoft i prefissi dei server locali in entrambi gli uffici tramite i due circuiti ExpressRoute. Nel caso, ad esempio, di migrazione delle cassette postali, Exchange Online avvierà le connessioni ai server locali. La connessione all'ufficio di Los Angeles viene purtroppo instradata al circuito ExpressRoute negli Stati Uniti orientali, attraversando l'intero continente prima di ritornare alla costa occidentale. La causa di questo problema è simile a quella del primo. Senza indicazioni la rete Microsoft non può stabilire quale prefisso del cliente è più vicino agli Stati Uniti orientali e quale agli Stati Uniti occidentali. Può quindi accadere che venga scelga il percorso errato per l'ufficio di Los Angeles.

![](./media/expressroute-optimize-routing/expressroute-case2-problem.png)

### <a name="solution-use-as-path-prepending"></a>Soluzione: anteporre AS PATH
Esistono due soluzioni al problema. La prima consiste semplicemente nell'annunciare il prefisso locale per l'ufficio di Los Angeles, 177.2.0.0/31, sul circuito ExpressRoute negli Stati Uniti occidentali e il prefisso locale per l'ufficio di New York, 177.2.0.2/31, sul circuito ExpressRoute negli Stati Uniti orientali. Di conseguenza, Microsoft avrà un solo percorso per connettersi agli uffici del cliente. Non esistono ambiguità e il routing risulta ottimizzato. Con questa progettazione è necessario considerare la strategia di failover. Nel caso di interruzione del percorso verso Microsoft tramite ExpressRoute, è necessario assicurarsi che Exchange Online possa comunque connettersi ai server locali. 

La seconda soluzione consiste nel continuare ad annunciare entrambi i prefissi in entrambi i circuiti ExpressRoute e, inoltre, indicare qual è il prefisso vicino a un determinato ufficio. Poiché è supportata l'anteposizione di AS PATH in BGP, si può configurare AS PATH nel prefisso per determinare il routing. In questo esempio si può estendere AS PATH per 172.2.0.0/31 negli Stati Uniti orientali, in modo che venga preferito il circuito ExpressRoute negli Stati Uniti occidentali per il traffico destinato a questo prefisso. La rete Microsoft considera infatti più breve il percorso per questo prefisso rispetto a quello negli Stati Uniti orientali. Allo stesso modo si può estendere AS PATH per 172.2.0.2/31 negli Stati Uniti occidentali, in modo che venga preferito il circuito ExpressRoute negli Stati Uniti orientali. Il routing è ottimizzato per entrambi gli uffici. Con questa progettazione, se un circuito ExpressRoute viene interrotto, Exchange Online può comunque raggiungere il cliente tramite un altro circuito ExpressRoute e la rete WAN. 

> [!IMPORTANT]
> I numeri AS privati in AS PATH per i prefissi ricevuti su peering Microsoft vengono rimossi. L'aggiunta di numeri AS pubblici in AS PATH è necessaria per determinare il routing per peering Microsoft.
> 
> 

![](./media/expressroute-optimize-routing/expressroute-case2-solution.png)

> [!IMPORTANT]
> Anche se gli esempi illustrati qui si riferiscono ai peer Microsoft e pubblici, le stesse funzionalità sono supportate per il peer privato. L'anteposizione di AS PATH funziona poi in un singolo circuito ExpressRoute, per determinare la selezione dei percorsi primari e secondari.
> 
> 




<!--HONumber=Jan17_HO4-->


