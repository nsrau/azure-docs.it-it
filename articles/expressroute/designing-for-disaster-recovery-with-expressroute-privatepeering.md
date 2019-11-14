---
title: 'Azure ExpressRoute: progettazione per il ripristino di emergenza'
description: Questa pagina fornisce consigli sull'architettura per il ripristino di emergenza durante l'uso di Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74076694"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Progettazione per il ripristino di emergenza con peering privato ExpressRoute

ExpressRoute è progettato per garantire un'elevata disponibilità per la connettività di rete privata di livello vettore alle risorse Microsoft. In altre parole, non esiste un singolo punto di errore nel percorso ExpressRoute all'interno della rete Microsoft. Per considerazioni sulla progettazione per ottimizzare la disponibilità di un circuito ExpressRoute, vedere [progettazione per la disponibilità elevata con ExpressRoute][HA].

Tuttavia, prendendo in considerazione l'Adagio più comune di*Murphy, in*questo articolo, in questo articolo ci concentreremo sulle soluzioni che vanno oltre gli errori che possono essere risolti usando un unico circuito ExpressRoute. In altre parole, in questo articolo vengono esaminate le considerazioni sull'architettura di rete per la creazione di una connettività di rete back-end affidabile per il ripristino di emergenza usando circuiti ExpressRoute con ridondanza geografica.

## <a name="need-for-redundant-connectivity-solution"></a>Necessità di una soluzione di connettività ridondante

Sono disponibili possibilità e istanze in cui un intero servizio a livello di area (ovvero Microsoft, provider di servizi di rete, cliente o altri provider di servizi cloud) viene ridotto. La causa principale di questo effetto sul servizio a livello di area è la calamità naturale. Per la continuità aziendale e per le applicazioni mission-critical è quindi importante pianificare il ripristino di emergenza.   

Indipendentemente dal fatto che si eseguano applicazioni mission-critical in un'area di Azure o in locale o in qualsiasi altra posizione, è possibile usare un'altra area di Azure come sito di failover. Gli articoli seguenti illustrano il ripristino di emergenza dalle applicazioni e dalle prospettive di accesso front-end:

- [Ripristino di emergenza su scala aziendale][Enterprise DR]
- [Ripristino di emergenza SMB con Azure Site Recovery][SMB DR]

Se si fa affidamento sulla connettività ExpressRoute tra la rete locale e Microsoft per le operazioni cruciali, il piano di ripristino di emergenza deve includere anche la connettività di rete con ridondanza geografica. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Problemi relativi all'uso di più circuiti ExpressRoute

Quando si esegue l'interconnessione dello stesso set di reti utilizzando più di una connessione, si introducono percorsi paralleli tra le reti. I percorsi paralleli, quando non progettati correttamente, possono causare il routing asimmetrico. Se sono presenti entità con stato (ad esempio, NAT, firewall) nel percorso, il routing asimmetrico potrebbe bloccare il flusso del traffico.  In genere, tramite il percorso di peering privato di ExpressRoute non si arriveranno entità con stato, ad esempio NAT o firewall. Pertanto, il routing asimmetrico sul peering privato ExpressRoute non blocca necessariamente il flusso del traffico.
 
Tuttavia, se si esegue il bilanciamento del carico del traffico tra percorsi paralleli con ridondanza geografica, indipendentemente dal fatto che si disponga o meno di entità con stato, si verificheranno prestazioni di rete incoerenti. Questo articolo illustra come risolvere questi problemi.

## <a name="small-to-medium-on-premises-network-considerations"></a>Considerazioni sulla rete locale da piccole a medie

Si prenda in considerazione la rete di esempio illustrata nel diagramma seguente. Nell'esempio viene stabilita la connettività ExpressRoute con ridondanza geografica tra una posizione locale di Contoso e VNet di Contoso in un'area di Azure. Nel diagramma la linea verde continua indica il percorso preferito (tramite ExpressRoute 1) e l'oggetto punteggiato rappresenta il percorso di standby (tramite ExpressRoute 2).

[![1]][1]

Quando si progetta la connettività ExpressRoute per il ripristino di emergenza, è necessario prendere in considerazione quanto segue:

- uso di circuiti ExpressRoute con ridondanza geografica
- uso di diverse reti di provider di servizi per diversi circuiti ExpressRoute
- progettazione di ognuno dei circuiti ExpressRoute per la [disponibilità elevata][HA]
- terminazione del circuito ExpressRoute diverso in una posizione diversa nella rete del cliente

Per impostazione predefinita, se si annunciano le route in modo identico su tutti i percorsi di ExpressRoute, Azure bilancia il carico del traffico associato locale in tutti i percorsi di ExpressRoute usando il routing a più percorsi (ECMP) a costo uguale.

Tuttavia, con i circuiti ExpressRoute con ridondanza geografica è necessario prendere in considerazione le diverse prestazioni di rete con percorsi di rete diversi, in particolare per la latenza di rete. Per ottenere prestazioni di rete più coerenti durante il normale funzionamento, è consigliabile preferire il circuito ExpressRoute che offre la latenza minima.

È possibile influenzare Azure per preferire un circuito ExpressRoute a un altro usando una delle tecniche seguenti, elencate in ordine di efficienza:

- inoltro più specifico della pubblicità sul circuito ExpressRoute preferito rispetto ad altri circuiti ExpressRoute
- configurazione di un peso di connessione superiore per la connessione che collega la rete virtuale al circuito ExpressRoute preferito
- annuncio delle route su un circuito ExpressRoute meno preferibile con un percorso più lungo (come percorso anteposto)

### <a name="more-specific-route"></a>Route più specifiche

Il diagramma seguente illustra la selezione del percorso ExpressRoute usando un annuncio di route più specifico. Nell'esempio illustrato, l'intervallo IP di Contoso locale/24 viene annunciato come due intervalli di indirizzi tramite il percorso preferito (ExpressRoute 1) e As/24 tramite il percorso autonomo (ExpressRoute 2).

[![2]][2]

Poiché/25 è più specifico, rispetto a/24, Azure invia il traffico destinato a 10.1.11.0/24 tramite ExpressRoute 1 nello stato normale. Se entrambe le connessioni di ExpressRoute 1 si arrestano, il VNet visualizzerà l'annuncio route 10.1.11.0/24 solo tramite ExpressRoute 2; e pertanto il circuito standby viene usato in questo stato di errore.

### <a name="connection-weight"></a>Peso della connessione

Lo screenshot seguente illustra la configurazione del peso di una connessione ExpressRoute tramite portale di Azure.

[![3]][3]

Il diagramma seguente illustra la selezione del percorso ExpressRoute con il peso della connessione. Il peso predefinito della connessione è 0. Nell'esempio seguente, il peso della connessione per ExpressRoute 1 è configurato come 100. Quando un VNet riceve un prefisso di route annunciato tramite più di un circuito ExpressRoute, VNet preferisce la connessione con il peso più alto.

[![4]][4]

Se entrambe le connessioni di ExpressRoute 1 si arrestano, il VNet visualizzerà l'annuncio route 10.1.11.0/24 solo tramite ExpressRoute 2; e pertanto il circuito standby viene usato in questo stato di errore.

### <a name="as-path-prepend"></a>COME percorso anteposto

Nel diagramma seguente viene illustrata la selezione del percorso ExpressRoute utilizzando come percorso anteposto. Nel diagramma l'annuncio della route su ExpressRoute 1 indica il comportamento predefinito di eBGP. Nell'annuncio della route su ExpressRoute 2, il numero ASN della rete locale viene anteposto anche al percorso AS della route. Quando la stessa route viene ricevuta attraverso più circuiti ExpressRoute, in base al processo di selezione della route eBGP, VNet preferisce la route con il percorso più breve. 

[![5]][5]

Se entrambe le connessioni di ExpressRoute 1 si arrestano, il VNet visualizzerà l'annuncio route 10.1.11.0/24 solo tramite ExpressRoute 2. In modo consequenziale, il percorso più lungo diventa irrilevante. Pertanto, il circuito standby verrà usato in questo stato di errore.

Usando una qualsiasi delle tecniche, se si influisce su Azure per preferire uno dei ExpressRoute rispetto ad altri, è anche necessario assicurarsi che la rete locale preferisca anche lo stesso percorso ExpressRoute per il traffico associato di Azure per evitare flussi asimmetrici. In genere, il valore della preferenza locale viene usato per influenzare la rete locale per preferire un circuito ExpressRoute rispetto ad altri. Preferenza locale è una metrica interna BGP (iBGP). È preferibile la route BGP con il valore di preferenza locale più elevato.

> [!IMPORTANT]
> Quando si usano determinati circuiti ExpressRoute come stand-by, è necessario gestirli attivamente e testare periodicamente l'operazione di failover. 
> 

## <a name="large-distributed-enterprise-network"></a>Rete aziendale distribuita di grandi dimensioni

Quando si dispone di una rete aziendale distribuita di grandi dimensioni, è probabile che siano presenti più circuiti ExpressRoute. In questa sezione viene illustrato come progettare il ripristino di emergenza usando i circuiti ExpressRoute Active-Active, senza che siano necessari circuiti stand-by aggiuntivi. 

Si prenda in considerazione l'esempio illustrato nella figura seguente. Nell'esempio Contoso dispone di due percorsi locali connessi a due distribuzioni di Contoso IaaS in due diverse aree di Azure tramite circuiti ExpressRoute in due diverse località di peering. 

[![6]][6]

Il modo in cui si progetta il ripristino di emergenza influisca sulla modalità di indirizzamento del traffico tra le aree geografiche tra le località (regione1/region2 e Location2/location1). Consideriamo due diverse architetture di emergenza che indirizzano in modo diverso il traffico tra le aree geografiche.

### <a name="scenario-1"></a>Scenario 1

Nel primo scenario viene progettato il ripristino di emergenza, in modo che tutto il traffico tra un'area di Azure e la rete locale scorra attraverso il circuito ExpressRoute locale nello stato stazionario. Se il circuito ExpressRoute locale ha esito negativo, il circuito ExpressRoute remoto viene usato per tutti i flussi di traffico tra Azure e la rete locale.

Il diagramma seguente illustra lo scenario 1. Nel diagramma le linee verdi indicano i percorsi per il flusso del traffico tra VNet1 e le reti locali. Le linee blu indicano i percorsi per il flusso del traffico tra VNet2 e le reti locali. Le linee continue indicano il percorso desiderato nello stato stazionario e le linee tratteggiate indicano il percorso del traffico nell'errore del circuito ExpressRoute corrispondente che trasporta il flusso di traffico a stato stazionario. 

[![7]][7]

È possibile progettare lo scenario usando il peso della connessione per influenzare reti virtuali per preferire la connessione al percorso di peering locale ExpressRoute per il traffico associato alla rete locale. Per completare la soluzione, è necessario garantire il flusso del traffico inverso simmetrico. È possibile usare la preferenza locale nella sessione iBGP tra i router BGP (in cui i circuiti ExpressRoute vengono interrotti sul lato locale) per preferire un circuito ExpressRoute. La soluzione è illustrata nella figura seguente. 

[![8]][8]

### <a name="scenario-2"></a>Scenario 2

Lo scenario 2 è illustrato nella figura seguente. Nel diagramma le linee verdi indicano i percorsi per il flusso del traffico tra VNet1 e le reti locali. Le linee blu indicano i percorsi per il flusso del traffico tra VNet2 e le reti locali. Nello stato stazionario (linee continue nel diagramma), tutto il traffico tra reti virtuali e i percorsi locali viene trasmesso tramite backbone Microsoft per la maggior parte e attraversa l'interconnessione tra percorsi locali solo nello stato di errore (linee tratteggiate in diagramma) di un ExpressRoute.

[![9]][9]

La soluzione è illustrata nella figura seguente. Come illustrato, è possibile progettare lo scenario usando una route più specifica (opzione 1) o il percorso come anteposto (opzione 2) per influenzare la selezione del percorso VNet. Per influenzare la selezione della route di rete locale per il traffico associato di Azure, è necessario configurare l'interconnessione tra la posizione locale come meno preferibile. Howe è possibile configurare il collegamento di interconnessione come preferibile a seconda del protocollo di routing usato nella rete locale. È possibile usare la preferenza locale con iBGP o metrica con IGP (OSPF o IS-IS).

[![10]][10]


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come progettare per il ripristino di emergenza di una connettività di peering privato del circuito ExpressRoute. Gli articoli seguenti illustrano il ripristino di emergenza dalle applicazioni e dalle prospettive di accesso front-end:

- [Ripristino di emergenza su scala aziendale][Enterprise DR]
- [Ripristino di emergenza SMB con Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "considerazioni sulla rete locale di dimensioni ridotte o medie"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "influire sulla selezione del percorso utilizzando route più specifiche"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "configurazione del peso della connessione tramite portale di Azure"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "influire sulla selezione del percorso utilizzando il peso della connessione"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "influire sulla selezione del percorso utilizzando come percorso anteposto"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "considerazioni sulla rete locale distribuita di grandi dimensioni"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "scenario 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "circuito ExpressRoute attivo-attivo soluzione 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "scenario 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "circuiti ExpressRoute Active-Active circuiti soluzione 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





