---
title: 'Azure ExpressRoute: Progettazione per il ripristino di emergenzaAzure ExpressRoute: Designing for disaster recovery'
description: Questa pagina fornisce consigli sull'architettura per il ripristino di emergenza durante l'uso di Azure ExpressRoute.This page provides architectural recommendations for disaster recovery while using Azure ExpressRoute.
services: expressroute
author: rambk
ms.service: expressroute
ms.topic: article
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: 726a014983c0da959d72b7976fef2ebb2c6e9b9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74076694"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Progettazione per il ripristino di emergenza con il peering privato ExpressRouteDesigning for disaster recovery with ExpressRoute private peering

ExpressRoute è progettato per la disponibilità elevata per fornire connettività di rete privata di livello carrier alle risorse Microsoft.ExpressRoute is designed for high availability to provide carrier grade private network connectivity to Microsoft resources. In altre parole, non esiste un singolo punto di errore nel percorso ExpressRoute all'interno della rete Microsoft.In other words, there is no single point of failure in the ExpressRoute path within Microsoft network. Per considerazioni sulla progettazione per ottimizzare la disponibilità di un circuito ExpressRoute, vedere [Progettazione per la disponibilità elevata con ExpressRoute.][HA]

Tuttavia, prendendo l'adagio popolare di Murphy-- se qualcosa può andare storto, sarà --in considerazione, in questo articolo ci concentriamo su soluzioni che vanno oltre gli errori che possono essere risolti utilizzando un singolo circuito ExpressRoute.However, taking Murphy's popular adage--*if anything can go wrong, it will*--in consideration, in this article let us focus on solutions that go beyond failures that can be addressed using a single ExpressRoute circuit. In altre parole, in questo articolo esaminiamo le considerazioni sull'architettura di rete per la creazione di connettività di rete back-end affidabile per il ripristino di emergenza usando circuiti ExpressRoute con ridondanza geografica.

## <a name="need-for-redundant-connectivity-solution"></a>Necessità di soluzione di connettività ridondante

Ci sono possibilità e casi in cui un intero servizio regionale (sia quello di Microsoft, fornitori di servizi di rete, cliente o altri fornitori di servizi cloud) viene degradato. La causa principale di tale impatto a livello regionale del servizio includono la calamità naturale. Pertanto, per la continuità aziendale e le applicazioni mission-critical è importante pianificare il ripristino di emergenza.   

Indipendentemente dal fatto che si esecano le applicazioni mission-critical in un'area di Azure o in locale o in qualsiasi altro luogo, è possibile usare un'altra area di Azure come sito di failover. Gli articoli seguenti riguardano il ripristino di emergenza dalle prospettive di accesso front-end e delle applicazioni front-end:The following articles addresses disaster recovery from applications and frontend access perspectives:

- [Ripristino di emergenza di livello aziendale][Enterprise DR]
- [Ripristino di emergenza per piccole e medie imprese con Azure Site Recovery][SMB DR]

Se ci si basa sulla connettività ExpressRoute tra la rete locale e Microsoft per le operazioni mission-critical, il piano di ripristino di emergenza deve includere anche la connettività di rete con ridondanza geografica. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Problemi relativi all'uso di più circuiti ExpressRoute

Quando si interconnettere lo stesso set di reti utilizzando più di una connessione, si introducono percorsi paralleli tra le reti. I percorsi paralleli, se non adeguatamente progettati, potrebbero portare a un routing asimmetrico. Se nel percorso sono presenti entità con stato (ad esempio NAT, firewall), il routing asimmetrico potrebbe bloccare il flusso del traffico.  In genere, nel percorso di peering privato ExpressRoute non si incontrano entità con stato, ad esempio NAT o firewall. Pertanto, il routing asimmetrico tramite il peering privato ExpressRoute non blocca necessariamente il flusso di traffico.
 
Tuttavia, se si bilancia il carico del traffico tra percorsi paralleli con ridondanza geografica, indipendentemente dal fatto che si disponga o meno di entità con stato, si verificherebbero prestazioni di rete incoerenti. In questo articolo viene illustrato come affrontare queste sfide.

## <a name="small-to-medium-on-premises-network-considerations"></a>Considerazioni sulla rete locale di piccole e medie dimensioni

Consideriamo la rete di esempio illustrata nel diagramma seguente. Nell'esempio, la connettività ExpressRoute con ridondanza geografica viene stabilita tra un percorso locale di Contoso e la rete virtuale di Contoso in un'area di Azure.In the example, geo-redundant ExpressRoute connectivity is established between a Contoso's on-premises location and Contoso's VNet in an Azure region. Nel diagramma, la linea verde continua indica il percorso preferito (tramite ExpressRoute 1) e quello punteggiato rappresenta il percorso stand-by (tramite ExpressRoute 2).

[![1]][1]

Quando si progetta la connettività ExpressRoute per il ripristino di emergenza, è necessario considerare quanto segue:When you are designing ExpressRoute connectivity for disaster recovery, you need to consider:

- uso di circuiti ExpressRoute con ridondanza geograficaUsing geo-redundant ExpressRoute circuits
- utilizzo di diverse reti di provider di servizi per diversi circuiti ExpressRoute
- progettazione di ciascuno del circuito ExpressRoute per la [disponibilità elevataDesigning][HA] each of the ExpressRoute circuit for high availability
- terminando il diverso circuito ExpressRoute in una posizione diversa sulla rete del cliente

Per impostazione predefinita, se si annunciano route identiche su tutti i percorsi ExpressRoute, Azure bilancia il carico del traffico associato locale in tutti i percorsi ExpressRoute usando il routing ECMP (Equal-cost multi-path).

Tuttavia, con i circuiti ExpressRoute con ridondanza geografica è necessario prendere in considerazione diverse prestazioni di rete con percorsi di rete diversi (in particolare per la latenza di rete). Per ottenere prestazioni di rete più coerenti durante il normale funzionamento, è possibile preferire il circuito ExpressRoute che offre la latenza minima.

È possibile influenzare Azure per preferire un circuito ExpressRoute rispetto a un altro utilizzando una delle tecniche seguenti (elencate nell'ordine di efficacia):You can influence Azure to prefer one ExpressRoute circuit over another one using one of the following techniques (listed in the order of effectiveness):

- annuncio di route più specifiche sul circuito ExpressRoute preferito rispetto ad altri circuiti ExpressRoute
- configurazione di un peso di connessione più elevato sulla connessione che collega la rete virtuale al circuito ExpressRoute preferito
- annuncio delle route su un circuito ExpressRoute meno preferito con percorso AS più lungo (anteposizione percorso AS)

### <a name="more-specific-route"></a>Percorso più specifico

Il diagramma seguente illustra l'influenza della selezione del percorso ExpressRoute usando un annuncio di route più specifico. Nell'esempio illustrato, l'intervallo IP /24 locale di Contoso viene annunciato come due intervalli di indirizzi /25 tramite il percorso preferito (ExpressRoute 1) e come /24 tramite il percorso di stand-by (ExpressRoute 2).

[![2]][2]

Poiché /25 è più specifico rispetto a /24, Azure invierebbe il traffico destinato a 10.1.11.0/24 tramite ExpressRoute 1 nello stato normale. Se entrambe le connessioni di ExpressRoute 1 si arrestano, la rete virtuale visualizzerebbe l'annuncio route 10.1.11.0/24 solo tramite ExpressRoute 2; e pertanto il circuito standby viene utilizzato in questo stato di errore.

### <a name="connection-weight"></a>Peso della connessione

La schermata seguente illustra la configurazione del peso di una connessione ExpressRoute tramite il portale di Azure.The following screenshot illustrates configuring the weight of an ExpressRoute connection via Azure portal.

[![3]][3]

Il diagramma seguente illustra l'influenza della selezione del percorso ExpressRoute usando il peso della connessione. Il peso di connessione predefinito è 0.The default connection weight is 0. Nell'esempio seguente, il peso della connessione per ExpressRoute 1 è configurato come 100.In the example below, the weight of the connection for ExpressRoute 1 is configured as 100. Quando una rete virtuale riceve un prefisso di route annunciato tramite più di un circuito ExpressRoute, la rete virtuale preferirà la connessione con il peso più elevato.

[![4]][4]

Se entrambe le connessioni di ExpressRoute 1 si arrestano, la rete virtuale visualizzerebbe l'annuncio route 10.1.11.0/24 solo tramite ExpressRoute 2; e pertanto il circuito standby viene utilizzato in questo stato di errore.

### <a name="as-path-prepend"></a>Percorso AS anteporre

Il diagramma seguente illustra l'influenza della selezione del percorso ExpressRoute usando la preparazione del percorso AS.The following diagram illustrates influencing ExpressRoute path selection using AS path prepend. Nel diagramma, l'annuncio di route su ExpressRoute 1 indica il comportamento predefinito di eBGP. Nell'annuncio di route su ExpressRoute 2, l'ASN della rete locale viene anteposto anche nel percorso AS della route. Quando la stessa route viene ricevuta tramite più circuiti ExpressRoute, per il processo di selezione della route eBGP, VNet preferirebbe la route con il percorso AS più breve. 

[![5]][5]

Se entrambe le connessioni di ExpressRoute 1 si arrestano, la rete virtuale visualizzerebbe l'annuncio route 10.1.11.0/24 solo tramite ExpressRoute 2.If both connections of ExpressRoute 1 go down, then the VNet would see the 10.1.11.0/24 route advertisement only via ExpressRoute 2. Di conseguenza, il percorso AS più lungo diventerebbe irrilevante. Pertanto, il circuito di standby verrebbe utilizzato in questo stato di errore.

Usando una delle tecniche, se si influenza Azure a preferire uno degli ExpressRoute rispetto ad altri, è inoltre necessario assicurarsi che la rete locale preferisca anche lo stesso percorso ExpressRoute per il traffico associato ad Azure per evitare flussi asimmetrici. In genere, il valore delle preferenze locali viene usato per influenzare la rete locale per preferire un circuito ExpressRoute rispetto ad altri. La preferenza locale è una metrica Interna BGP (iBGP). La route BGP con il valore di preferenza locale più alto è preferibile.

> [!IMPORTANT]
> Quando si usano determinati circuiti ExpressRoute come stand-by, è necessario gestirli attivamente e testare periodicamente l'operazione di failover. 
> 

## <a name="large-distributed-enterprise-network"></a>Rete aziendale distribuita di grandi dimensioni

Quando si dispone di una rete aziendale distribuita di grandi dimensioni, è probabile che si disponga di più circuiti ExpressRoute.When you have a large distributed enterprise network, you're likely to have multiple ExpressRoute circuits. In questa sezione viene illustrato come progettare il ripristino di emergenza usando i circuiti ExpressRoute attivi-attivi, senza la necessità di circuiti stand-by aggiuntivi. 

Consideriamo l'esempio illustrato nel diagramma seguente. Nell'esempio, Contoso ha due posizioni locali connesse a due distribuzioni IaaS Contoso in due aree di Azure diverse tramite circuiti ExpressRoute in due posizioni di peering diverse. 

[![6]][6]

Il modo in cui progettiamo il ripristino di emergenza influisce sul modo in cui il traffico da attraversare regionale (regione1/regione2 a posizione2/posizione1) viene instradato. Consideriamo due diverse architetture di emergenza che indirizzano il traffico di posizione in area in modo diverso.

### <a name="scenario-1"></a>Scenario 1

Nel primo scenario, è possibile progettare il ripristino di emergenza in modo che tutto il traffico tra un'area di Azure e la rete locale scorra attraverso il circuito ExpressRoute locale nello stato stabile. Se si verifica un errore nel circuito ExpressRoute locale, il circuito ExpressRoute remoto viene usato per tutti i flussi di traffico tra Azure e la rete locale.

Lo scenario 1 è illustrato nel diagramma seguente. Nel diagramma, le linee verdi indicano i percorsi per il flusso di traffico tra VNet1 e le reti locali. Le linee blu indicano i percorsi per il flusso di traffico tra VNet2 e le reti locali. Le linee continue indicano il percorso desiderato nello stato costante e le linee tratteggiate indicano il percorso del traffico nell'errore del circuito ExpressRoute corrispondente che trasporta il flusso del traffico a stato costante. 

[![7]][7]

È possibile progettare lo scenario usando il peso della connessione per influenzare le reti virtuali per preferire la connessione al percorso di peering locale ExpressRoute per il traffico associato alla rete locale. Per completare la soluzione, è necessario garantire un flusso di traffico inverso simmetrico. È possibile usare le preferenze locali nella sessione iBGP tra i router BGP (su cui i circuiti ExpressRoute vengono terminati sul lato locale) per preferire un circuito ExpressRoute.You can use local preference on the iBGP session between your BGP routers (on which ExpressRoute circuits are terminated on on-premises side) to prefer a ExpressRoute circuits. La soluzione è illustrata nel diagramma seguente. 

[![8]][8]

### <a name="scenario-2"></a>Scenario 2

Lo scenario 2 è illustrato nel diagramma seguente. Nel diagramma, le linee verdi indicano i percorsi per il flusso di traffico tra VNet1 e le reti locali. Le linee blu indicano i percorsi per il flusso di traffico tra VNet2 e le reti locali. Nello stato stazionario (linee solide nel diagramma), tutto il traffico tra reti virtuali e ubicazioni locali scorre tramite la spina dorsale Microsoft per la maggior parte e scorre attraverso l'interconnessione tra le posizioni locali solo nello stato di errore (linee tratteggiate in il diagramma) di un ExpressRoute.

[![9]][9]

La soluzione è illustrata nel diagramma seguente. Come illustrato, è possibile progettare lo scenario utilizzando route più specifica (opzione 1) o percorso AS anteporre (opzione 2) per influenzare la selezione del percorso della rete virtuale. Per influenzare la selezione della route di rete locale per il traffico associato ad Azure, è necessario configurare l'interconnessione tra la posizione locale come meno preferibile. Il modo in cui si configura il collegamento di interconnessione in base alle funzionalità preferibili dipende dal protocollo di routing utilizzato all'interno della rete locale. È possibile utilizzare le preferenze locali con iBGP o metrica con IGP (OSPF o IS-IS).

[![10]][10]


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come progettare il ripristino di emergenza di una connettività di peering privato di un circuito ExpressRoute.In this article, we discussed how to design for disaster recovery of an ExpressRoute circuit private peering connectivity. Gli articoli seguenti riguardano il ripristino di emergenza dalle prospettive di accesso front-end e delle applicazioni front-end:The following articles addresses disaster recovery from applications and frontend access perspectives:

- [Ripristino di emergenza di livello aziendale][Enterprise DR]
- [Ripristino di emergenza per piccole e medie imprese con Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "considerazioni sulla rete locale di piccole e medie dimensioni"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.pngche influenzano la "selezione dei percorsi utilizzando percorsi più specifici"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.pngconfigurazione del peso della connessione tramite il portale di "Azure3 configuring connection weight via Azure portal"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "che influenzano la selezione" del percorso utilizzando il peso della connessione
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.pnginfluenzare la selezione del "percorso utilizzando la prepend del percorso AS"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "considerazioni sulla rete locale distribuita di grandi dimensioni"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "scenario 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "circuiti ExpressRoute attivi-attivi 1"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "scenario 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "circuiti ExpressRoute attivi-attivi 2"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





