---
title: Progettazione per il ripristino di emergenza con Azure ExpressRoute | Microsoft Docs
description: Questa pagina offre indicazioni architetturali per il ripristino di emergenza quando si usa Azure ExpressRoute.
documentationcenter: na
services: networking
author: rambk
manager: tracsman
ms.service: expressroute
ms.topic: article
ms.workload: infrastructure-services
ms.date: 05/25/2019
ms.author: rambala
ms.openlocfilehash: cf2b4e385de901254fde3c3d3e807feda98d5b41
ms.sourcegitcommit: c63e5031aed4992d5adf45639addcef07c166224
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67466077"
---
# <a name="designing-for-disaster-recovery-with-expressroute-private-peering"></a>Progettazione per il ripristino di emergenza con peering privato di ExpressRoute

ExpressRoute è progettato per la disponibilità elevata fornire la connettività di rete privata di livello per le risorse di Microsoft gestore telefonico. In altre parole, non è presente alcun singolo punto di guasto nel percorso di ExpressRoute nella rete di Microsoft. Per considerazioni sulla progettazione ottimizzare la disponibilità di un circuito ExpressRoute, vedere [la progettazione della disponibilità elevata con ExpressRoute][HA].

Tuttavia, richiede popolari informo - di Murphy*se qualsiasi elemento può andare storto, verrà*, in considerazione, in questo articolo ci consentirà di concentrarci sulle soluzioni che superano gli errori che possono essere indirizzati con un singolo circuito ExpressRoute. In altre parole, in questo articolo verrà ora esaminato in considerazioni sull'architettura di rete per la creazione di connettività di rete back-end affidabile per il ripristino di emergenza con circuiti ExpressRoute con ridondanza geografica.

## <a name="need-for-redundant-connectivity-solution"></a>È necessario per la soluzione di connettività ridondante

Sono presenti istanze in cui ottiene danneggiato un intero servizio a livello di area, sia per quello di Microsoft, altri provider di servizi cloud, cliente o i provider di servizi di rete, e le possibilità. La causa principale per tale impatto a livello di area ampia servizio includono calamità naturale. Pertanto, per le applicazioni critiche di continuità aziendale e mission aziendali è importante pianificare il ripristino di emergenza.   

Indipendentemente dal fatto che si esegue le applicazioni critiche in un'area di Azure o in locale o in qualunque altra parte, è possibile usare un'altra area di Azure come sito di failover. I seguenti articoli indirizzi ripristino di emergenza da applicazioni e le prospettive accesso front-end:

- [Ripristino di emergenza su scala aziendale][Enterprise DR]
- [Ripristino di emergenza SMB con Azure Site Recovery][SMB DR]

Se si fa affidamento sulla connettività di ExpressRoute tra la rete locale e Microsoft per operazioni mission-critical, il piano di ripristino di emergenza deve includere anche la connettività di rete con ridondanza geografica. 

## <a name="challenges-of-using-multiple-expressroute-circuits"></a>Difficoltà di uso più circuiti ExpressRoute

Quando si interconnessione lo stesso set di reti che utilizzano più di una connessione, si introducono percorsi paralleli tra le reti. I percorsi, quando non è correttamente progettato, potrebbe causare il routing asimmetrico in parallelo. Se sono presenti entità con stata (ad esempio NAT, firewall) nel percorso, il routing asimmetrico è stato possibile bloccare il flusso del traffico.  In genere, tramite il percorso di peering privato ExpressRoute non servono tra le entità con state, ad esempio NAT o firewall. Pertanto, il routing asimmetrico tramite peering privato di ExpressRoute non blocca necessariamente il flusso del traffico.
 
Tuttavia, se si bilanciare il carico tra percorsi paralleli con ridondanza geografica, indipendentemente dal fatto che si dispone di entità con stata o No, si verifica una situazione delle prestazioni di rete non coerente. In questo articolo, esaminiamo come risolvere queste problematiche.

## <a name="small-to-medium-on-premises-network-considerations"></a>Considerazioni sulla rete locale medio-piccole

Si consideri la rete di esempio illustrata nel diagramma seguente. Nell'esempio, la connettività di ExpressRoute con ridondanza geografica viene stabilita tra posizione locale di Contoso e la rete virtuale di Contoso in un'area di Azure. Nel diagramma, la linea verde continua indica il percorso preferito (tramite ExpressRoute 1) e quella punteggiato rappresenta stand-by percorso (tramite ExpressRoute 2).

[![1]][1]

Quando si progetta la connettività di ExpressRoute per il ripristino di emergenza, è necessario prendere in considerazione:

- usando i circuiti ExpressRoute con ridondanza geografica
- con le reti di provider di servizio diversi per diversi circuito di ExpressRoute
- ogni circuito ExpressRoute per la progettazione [la disponibilità elevata][HA]
- interruzione del circuito ExpressRoute diversi in un percorso diverso dalla rete dei clienti

Per impostazione predefinita, se si pubblicano route in modo identico in tutti i percorsi di ExpressRoute, Azure eseguirà il bilanciamento del carico associato il traffico locale tra tutti i percorsi di ExpressRoute Usa il routing (ECMP) di percorsi multipli Equal-cost.

Tuttavia, con i circuiti ExpressRoute con ridondanza geografica è necessario considerare le prestazioni di rete diversa considerazione con diversi percorsi di rete (in particolare per la latenza di rete). Per ottenere prestazioni più coerenti di rete durante il normale funzionamento, è possibile preferito il circuito ExpressRoute che offre la latenza minima.

È possibile influenzare Azure per preferire un circuito ExpressRoute rispetto a un'altra usando una delle tecniche seguenti (elencate in ordine l'efficacia):

- Annuncio route più specifica sul circuito ExpressRoute preferito rispetto alle altre circuiti di ExpressRoute
- configurazione di maggiore peso della connessione per la connessione che collega la rete virtuale al circuito ExpressRoute preferito
- annunciare le route meno preferito circuito di ExpressRoute con percorso più lungo di AS (come percorso anteposizioni)

### <a name="more-specific-route"></a>Route più specifica

Il diagramma seguente illustra che influenzano la selezione di percorso ExpressRoute tramite più specifiche degli annunci di route. Nell'esempio illustrato, Contoso locale/24 intervallo di indirizzi IP viene annunciata come gli intervalli di indirizzi di due /25 tramite il percorso preferito (1 di ExpressRoute) e da/24 attraverso il percorso in standby (2 di ExpressRoute).

[![2]][2]

Poiché /25 è più specifico, rispetto a da/24, Azure invia il traffico destinato a 10.1.11.0/24 tramite ExpressRoute 1 nello stato normale. Se entrambe le connessioni ExpressRoute 1 si disattivano, quindi la rete virtuale vedrebbe annuncio route 10.1.11.0/24 solo via ExpressRoute 2. e pertanto il circuito standby viene utilizzato in questo stato di errore.

### <a name="connection-weight"></a>Peso della connessione

Nella schermata seguente viene illustrato come configurare il peso di una connessione ExpressRoute tramite il portale di Azure.

[![3]][3]

Il diagramma seguente illustra che influenza Selezione percorso di ExpressRoute con peso della connessione. Il peso della connessione predefinita è 0. Nell'esempio seguente, il peso della connessione per 1 ExpressRoute è configurato come 100. Quando una rete virtuale riceve un prefisso di route annunciato tramite più di un circuito ExpressRoute, la rete virtuale preferisce la connessione con il peso più alto.

[![4]][4]

Se entrambe le connessioni ExpressRoute 1 si disattivano, quindi la rete virtuale vedrebbe annuncio route 10.1.11.0/24 solo via ExpressRoute 2. e pertanto il circuito standby viene utilizzato in questo stato di errore.

### <a name="as-path-prepend"></a>COME percorso anteporre

Il diagramma seguente illustra che influenzano la selezione di percorso ExpressRoute mediante come percorso anteporre. Nel diagramma, l'annuncio delle route tramite ExpressRoute 1 indica il comportamento predefinito di eBGP. Nell'annuncio delle route tramite ExpressRoute 2, ASN della rete locale viene anteposto inoltre della route come percorso. Quando la stessa route viene ricevuta tramite più circuiti ExpressRoute, per il processo di selezione delle route di eBGP, rete virtuale si preferisce la route con i più brevi come percorso. 

[![5]][5]

Se entrambe le connessioni ExpressRoute 1 si disattivano, quindi la rete virtuale verrà visualizzati l'annuncio route 10.1.11.0/24 solo via ExpressRoute 2. Consequentially, più a lungo come percorso diventa irrilevante. Pertanto, il circuito standby viene utilizzato in questo stato di errore.

Usare una delle tecniche, se è influenzare Azure preferita in uno dei ExpressRoute rispetto ad altri, occorre anche assicurarsi che la rete locale inoltre essere preferibile lo stesso percorso di ExpressRoute per il traffico per evitare i flussi asimmetrici associato ad Azure. In genere, il valore di preferenza locale viene utilizzato per influire sulla rete locale per preferire un circuito ExpressRoute rispetto ad altri. Preferenza locale è una metrica per il protocollo BGP (iBGP) interna. È preferibile alla route BGP con il massimo valore di preferenza locale.

> [!IMPORTANT]
> Quando si usano determinati circuiti di ExpressRoute come standby, è necessario gestirle attivamente e provare periodicamente a eseguire l'operazione di failover. 
> 

## <a name="large-distributed-enterprise-network"></a>Rete aziendale distribuiti di grandi dimensioni

Quando si dispone di una rete aziendale distribuiti di grandi dimensioni, probabilmente sarà preferibile avere più circuiti ExpressRoute. In questa sezione, vediamo come progettare il ripristino di emergenza utilizzando i circuiti ExpressRoute attivo-attivo, senza la necessità di circuiti aggiuntivi in stand-by. 

Si consideri l'esempio illustrato nel diagramma seguente. Nell'esempio, Contoso ha due sedi locali connessi a due di distribuzione IaaS di Contoso in due diverse aree di Azure tramite i circuiti ExpressRoute in due posizioni di peering diverse. 

[![6]][6]

Come definiamo l'architettura di ripristino di emergenza influisce sul modo tra aree attraversare viene indirizzato il traffico del percorso (Area1/regione2 a location2/location1). Si consideri due architetture diverse emergenza che instrada il traffico cross-percorso dell'area in modo diverso.

### <a name="scenario-1"></a>Scenario 1

Nel primo scenario, è possibile progettare il ripristino di emergenza in modo che tutto il traffico tra una rete Azure di area e in locale flusso attraverso il circuito ExpressRoute locale nello stato stazionario. Se il circuito ExpressRoute locale ha esito negativo, quindi sul circuito ExpressRoute remoto viene usato per tutti i flussi di traffico tra Azure e rete locale.

Scenario 1 è illustrato nel diagramma seguente. Nel diagramma, linee verdi indicano i percorsi per il flusso del traffico tra le reti VNet1 e in locale. Le linee blue indicano i percorsi per il flusso del traffico tra reti VNet2 e in locale. Linee continue indicano il percorso desiderato lo stato stabile e linee tratteggiate indicano il percorso del traffico l'errore del circuito ExpressRoute corrispondente che contiene il flusso del traffico nello stato stazionario. 

[![7]][7]

È possibile progettare lo scenario con peso della connessione per influenzare le reti virtuali per preferire il traffico associato ad connessione al percorso locale in peering ExpressRoute nella rete locale. Per completare la soluzione, è necessario assicurarsi che il flusso del traffico inverso simmetrico. È possibile utilizzare preferenza locale nella sessione iBGP tra i router BGP (in cui i circuiti ExpressRoute vengono terminati sul lato di on-premises) per preferire un circuito ExpressRoute. La soluzione è illustrata nel diagramma seguente. 

[![8]][8]

### <a name="scenario-2"></a>Scenario 2

Scenario 2 è illustrata nel diagramma seguente. Nel diagramma, linee verdi indicano i percorsi per il flusso del traffico tra le reti VNet1 e in locale. Le linee blue indicano i percorsi per il flusso del traffico tra reti VNet2 e in locale. In allo stato stabile (linee continue nel diagramma), tutto il traffico tra i percorsi di reti virtuali e locali passano attraverso il backbone di Microsoft per la maggior parte e passano attraverso l'interconnessione tra percorsi locali solo nello stato di errore (linee tratteggiate nella il diagramma) di un circuito ExpressRoute.

[![9]][9]

La soluzione è illustrata nel diagramma seguente. Come illustrato, è possibile progettare lo scenario con più specifico i route (opzione 1) o AS path anteporre (opzione 2) per poter influire sulla selezione del percorso della rete virtuale. Per poter influire sulla selezione di route di rete locale per il traffico di Azure associato, è necessario configurare l'interconnessione tra il percorso locale come meno preferibile. Howe si configura il collegamento di interconnessione come preferibile dipende dal protocollo di routing usato all'interno della rete locale. È possibile usare preferenza locale con iBGP o metrica con il protocollo IGP (OSPF o IS-IS).

[![10]][10]


## <a name="next-steps"></a>Passaggi successivi

In questo articolo è stato illustrato come progettare per il ripristino di emergenza di connettività di peering privata di un circuito di ExpressRoute. I seguenti articoli indirizzi ripristino di emergenza da applicazioni e le prospettive accesso front-end:

- [Ripristino di emergenza su scala aziendale][Enterprise DR]
- [Ripristino di emergenza SMB con Azure Site Recovery][SMB DR]

<!--Image References-->
[1]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/one-region.png "piccolo per considerazioni sulla rete locale di medie dimensioni"
[2]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/specificroute.png "che influiscono sulla selezione del percorso usando i route più specifiche"
[3]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/configure-weight.png "configurazione peso della connessione tramite il portale di Azure"
[4]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/connectionweight.png "che influiscono sulla selezione del percorso con peso della connessione"
[5]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/aspath.png "anteporre influenzando l'uso come percorso di selezione del percorso"
[6]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region.png "considerazioni relative alla rete di grandi dimensioni distribuite in locale"
[7]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch1.png "scenario 1"
[8]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol1.png "soluzione 1 di circuiti ExpressRoute attivo-attivo"
[9]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-arch2.png "scenario 2"
[10]: ./media/designing-for-disaster-recovery-with-expressroute-pvt/multi-region-sol2.png "soluzione 2 di circuiti ExpressRoute attivo-attivo"

<!--Link References-->
[HA]: https://docs.microsoft.com/azure/expressroute/designing-for-high-availability-with-expressroute
[Enterprise DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-enterprise-scale-dr/
[SMB DR]: https://azure.microsoft.com/solutions/architecture/disaster-recovery-smb-azure-site-recovery/
[con wgt]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-assign-a-high-weight-to-local-connection
[AS Path Pre]: https://docs.microsoft.com/azure/expressroute/expressroute-optimize-routing#solution-use-as-path-prepending





