---
title: Azure Load Balancer Standard e zone di disponibilità
titleSuffix: Azure Load Balancer
description: With this learning path, get started with Azure Standard Load Balancer and Availability Zones.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5a65982c5c13eb4e4273efcfd8d14910b0f35572
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197148"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer Standard e zone di disponibilità

Azure Standard Load Balancer supporta gli scenari delle zone di [disponibilità.](../availability-zones/az-overview.md) È possibile utilizzare Load Balancer Standard per ottimizzare la disponibilità nello scenario end-to-end allineando le risorse alle zone e distribuendole tra le zone.  Esaminare le zone di [disponibilità](../availability-zones/az-overview.md) per istruzioni sulle zone di disponibilità, sulle aree attualmente supportate e su altri concetti e prodotti correlati. Le zone di disponibilità in combinazione con Load Balancer Standard sono un set di funzionalità espansivo e flessibile in grado di creare molti scenari diversi.  Leggere questo documento per comprendere questi [concetti](#concepts) e le [linee guida di progettazione](#design) degli scenari fondamentali.

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a> Concetti relativi alle zone di disponibilità applicati a Load Balancer

Una risorsa di Load Balancer è di per sé regionale e mai di zona. La granularità di ciò che è possibile configurare è vincolata da ogni configurazione di definizione del front-end, della regola e del pool back-end.
Nel contesto delle zone di disponibilità, il comportamento e le proprietà di una regola di bilanciamento del carico sono descritti come zone ridondanti o zonali.  Queste due espressioni descrivono la zonalità di una proprietà.  Nel contesto di Load Balancer, la ridondanza di zona significa sempre *più zone* e zone significa isolare il servizio in una *singola zona.*
Le istanze di Load Balancer pubbliche e interne supportano entrambe scenari con ridondanza della zona e di zona e possono entrambe indirizzare il traffico tra le zone in base alle esigenze (*bilanciamento del carico tra zone*). 

### <a name="frontend"></a>Front-end

Un front-end di Load Balancer è una configurazione IP front-end che fa riferimento a una risorsa indirizzo IP pubblico o a un indirizzo IP privato all'interno della subnet di una risorsa di rete virtuale.  Costituisce l'endpoint con carico bilanciato in cui viene esposto il servizio.
Una risorsa di bilanciamento del carico può contenere regole con front-end di zona e ridondanza di zona contemporaneamente. Quando una risorsa IP pubblica o un indirizzo IP privato è stato garantito a una zona, la zonalità (o la sua mancanza) non è modificabile.  Se si desidera modificare o o omettere la zona di un indirizzo IP pubblico o di un indirizzo IP privato frontend, è necessario ricreare l'indirizzo IP pubblico nella zona appropriata.  Le zone di disponibilità non modificano i vincoli per più front-end, esaminare [più front-end per Load Balancer](load-balancer-multivip-overview.md) per i dettagli per questa funzionalità.

#### <a name="zone-redundant"></a>Con ridondanza della zona 

In un'area con zone di disponibilità, un front-end di Load Balancer Standard può essere ridondante per la zona.  Ridondante di zona significa che tutti i flussi in ingresso o in uscita vengono serviti da più zone di disponibilità in un'area contemporaneamente utilizzando un singolo indirizzo IP. Gli schemi di ridondanza DNS non sono necessari. Un singolo indirizzo IP front-end può sopravvivere a un errore di zona e può essere utilizzato per raggiungere tutti i membri del pool back-end (non interessati) indipendentemente dalla zona. Una o più zone di disponibilità possono avere esito negativo e il percorso dati sopravvive finché una zona nell'area rimane integro. Il singolo indirizzo IP del frontend viene servito contemporaneamente da più distribuzioni di infrastrutture indipendenti in più zone di disponibilità.  Questo non significa che il percorso dei dati senza hit, ma qualsiasi tentativo o ripristino avrà esito positivo in altre zone non interessate dall'errore di zona.   

#### <a name="optional-zone-isolation"></a>Isolamento della zona opzionale

È possibile scegliere di avere un front-end garantito per una singola zona, noto come *front-end di zona*.  Ciò significa che qualsiasi flusso in ingresso o in uscita viene gestito da una singola zona in un'area.  La durata del front-end è legata all'integrità della zona.  Il percorso dati non è interessato dagli errori in zone diverse da quelle in cui è stato garantito. È possibile usare front-end di zona per esporre un indirizzo IP per zona di disponibilità.  

Inoltre, è possibile utilizzare direttamente i front-end delle zone per gli endpoint con bilanciamento del carico all'interno di ogni zona. È anche possibile ricorrere a questo metodo per esporre endpoint con carico bilanciato per zona, in modo da monitorare singolarmente ogni zona.  In alternativa, per gli endpoint pubblici è possibile integrarli con un prodotto di bilanciamento del carico DNS come [Gestione traffico](../traffic-manager/traffic-manager-overview.md) e usare un singolo nome DNS. Il client verrà quindi risolto in questo nome DNS in più indirizzi IP di zona.  

Per un approfondimento di questi concetti (con ridondanza della zona e di zona per lo stesso back-end), consultare [Più front-end per Azure Load Balancer](load-balancer-multivip-overview.md).

Per un front-end di Load Balancer pubblico, aggiungere un parametro *zones* alla risorsa IP pubblica a cui fa riferimento la configurazione IP front-end utilizzata dalla rispettiva regola.

Per un front-end di Load Balancer interno, è necessario aggiungere un parametro *zones* alla configurazione IP front-end dell'istanza di Load Balancer interna. Il front-end di zona fa sì che Load Balancer garantisca un indirizzo IP in una subnet per una zona specifica.

### <a name="cross-zone-load-balancing"></a>Bilanciamento del carico tra zone

Il bilanciamento del carico tra zone è la possibilità di Load Balancer di raggiungere un endpoint di back-end in qualsiasi zona ed è indipendente dal front-end e dalla relativa zonalità.  Qualsiasi regola di bilanciamento del carico può indirizzare l'istanza back-end in qualsiasi zona di disponibilità o istanze regionali.

È necessario prestare attenzione per costruire lo scenario in modo da esprimere una nozione di zone di disponibilità. Ad esempio, è necessario garantire la distribuzione della macchina virtuale all'interno di una singola zona o più zone e allineare le risorse front-end e back-end zonali alla stessa zona.  Se si attraversano zone di disponibilità con solo risorse zonali, lo scenario funzionerà ma potrebbe non avere una modalità di errore chiara rispetto alle zone di disponibilità. 

### <a name="backend"></a>Back-end

Load Balancer funziona con le istanze delle macchine virtuali.  Possono essere autonomi, set di disponibilità o set di scalabilità di macchine virtuali.  Qualsiasi istanza di macchina virtuale in una singola rete virtuale può far parte del pool back-end indipendentemente dal fatto che sia garantita o meno a una zona o a quale zona è stata garantita.

Se si desidera allineare e garantire il front-end e il back-end con una singola zona, inserire solo le macchine virtuali all'interno della stessa zona nel rispettivo pool back-end.

Se si desidera indirizzare le macchine virtuali in più aree, è sufficiente posizionare le macchine virtuali da più aree nello stesso pool back-end.  Quando si usano set di scalabilità di macchine virtuali, è possibile inserire uno o più set di scalabilità di macchine virtuali nello stesso pool back-end.  Ognuno di questi set di scalabilità di macchine virtuali può essere inserito in una o più zone.

### <a name="outbound-connections"></a>Connessioni in uscita

Le stesse proprietà di zona ridondante e zona si applicano alle [connessioni in uscita](load-balancer-outbound-connections.md).  Un indirizzo IP pubblico ridondante di zona utilizzato per le connessioni in uscita viene servito da tutte le zone. Un indirizzo IP pubblico zonale è servito solo dalla zona in cui è garantito.  Le allocazioni di porte SNAT della connessione in uscita sopravvivono agli errori di zona e lo scenario continuerà a fornire la connettività SNAT in uscita se non viene influenzata da un errore di zona.  Ciò può richiedere trasmissioni o per le connessioni da ristabilire per scenari con ridondanza di zona se un flusso è stato servito da una zona interessata.  I flussi in zone diverse dalle zone interessate non sono interessati.

L'algoritmo di preallocazione delle porte SNAT è lo stesso con o senza zone di disponibilità.

### <a name="health-probes"></a>Probe di integrità

Le definizioni di probe di integrità esistenti rimangono invariate senza zone di disponibilità.  Tuttavia, il modello di integrità è stato ampliato a livello di infrastruttura. 

Quando si usano front-end con ridondanza di zona, Load Balancer espande il proprio modello di integrità interno per sondare in modo indipendente la raggiungibilità di una macchina virtuale da ogni zona di disponibilità e arrestare i percorsi tra le zone che potrebbero non essere riusciti senza l'intervento del cliente.  Se un determinato percorso non è disponibile dall'infrastruttura di Load Balancer di una zona a una macchina virtuale in un'altra zona, Load Balancer è in grado di rilevare ed evitare questo errore. Altre zone che sono in grado di raggiungere questa macchina virtuale possono continuare a rendere disponibile la macchina virtuale dai rispettivi front-end.  Di conseguenza, è possibile che durante gli eventi di errore, ogni zona possa avere distribuzioni leggermente diverse di nuovi flussi proteggendo al contempo l'integrità complessiva del servizio end-to-end.

## <a name="design-considerations"></a><a name="design"></a>Considerazioni di progettazione

Load Balancer è intenzionalmente flessibile nel contesto delle zone di disponibilità. È possibile scegliere di allineare alle zone oppure ridondare la zona per ogni regola.  A una maggiore disponibilità può corrispondere un aumento della complessità, pertanto per prestazioni ottimali è richiesta una progettazione orientata alla disponibilità.  Verranno ora prese in esame alcune importanti considerazioni sulla progettazione.

### <a name="automatic-zone-redundancy"></a>Ridondanza automatica della zona

Con Load Balancer è più semplice avere un singolo IP come front-end con ridondanza della zona. Un indirizzo IP con ridondanza della zona può rendere disponibile in modo sicuro una risorsa di zona in qualsiasi zona e può non risentire di uno o più errori a livello di zona fintanto che una zona rimane integra all'interno dell'area. Al contrario, un front-end di zona è una riduzione del servizio a una singola zona e la sua durata è legata alla rispettiva zona.

La ridondanza della zona non implica un percorso dati privo di passaggi o un piano di controllo; si tratta espressamente di un piano dati. I flussi con ridondanza della zona possono usare qualsiasi zona e i flussi di un cliente useranno tutte le zone integre in un'area. In caso di errore di una zona, i flussi di traffico che usano le zone integre in quel dato momento non sono interessati.  I flussi di traffico che utilizzano una zona all'ora dell'errore di zona possono essere interessati, ma le applicazioni possono essere ripristinate. Questi flussi possono continuare nelle zone integre rimanenti all'interno dell'area dopo la ritrasmissione o il ripristino, una volta che Azure è convergente intorno all'errore della zona.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a> Limiti tra zone

È importante comprendere che ogni volta che un servizio end-to-end attraversa le zone, la sua durata è legata non solo a una zona ma potenzialmente a più zone.  Di conseguenza, il servizio end-to-end potrebbe non garantire maggiore disponibilità rispetto alle distribuzioni non di zona.

Evitare di introdurre dipendenze tra zone non intenzionali, che annulleranno i vantaggi di disponibilità quando si usano le zone di disponibilità.  Quando l'applicazione è costituita da più componenti e si vuole ottenere la resilienza agli errori a livello di zona, è necessario prestare attenzione a garantire che un numero sufficiente di componenti critici non risenta degli errori in caso di problemi con una zona.  Ad esempio, un singolo componente critico per l'applicazione può influire sull'intera applicazione se esiste solo in una zona diversa da quelle che non risentono degli errori.  Inoltre, prendere in considerazione anche il ripristino della zona e come l'applicazione eseguirà la convergenza. È necessario comprendere in che modo l'applicazione causa rispetto agli errori di parti di esso. Verranno presi in esame alcuni punti chiave che saranno usati come riferimento per eventuali domande relative a scenari specifici.

- Se l'applicazione dispone di due componenti, ad esempio un indirizzo IP e una macchina virtuale con disco gestito e sono garantiti nella zona 1 e nella zona 2, quando la zona 1 non supera il servizio end-to-end non sopravviverà quando si verifica un errore della zona 1.  Non incrociare le zone con scenari di zona a meno che non si comprenda appieno che si sta creando una modalità di errore potenzialmente pericolosa.  Questo scenario è consentito fornire flessibilità.

- Se l'applicazione dispone di due componenti, ad esempio un indirizzo IP e una macchina virtuale con disco gestito e sono garantiti, rispettivamente ridondanti per zona e zona 1, il servizio end-to-end sopravviverà all'errore di zona della zona 2, della zona 3 o di entrambi, a meno che la zona 1 non si sia guasto.  Tuttavia, se ci si limita a prestare attenzione solo alla raggiungibilità del front-end si perde di vista l'integrità del servizio.  Prendere in considerazione l'idea di sviluppare un modello di integrità e di capacità più esteso.  È possibile usare in combinazione i concetti di ridondanza della zona e di zona per aumentare il dettaglio delle informazioni e la gestibilità.

- Se l'applicazione include due componenti come un front-end di Load Balancer con ridondanza della zona e un set di scalabilità di macchine virtuali tra zone in tre zone, le risorse nelle zone non interessate dall'errore saranno disponibili, ma la capacità del servizio end-to-end potrebbe risultare degradata durante l'errore della zona. Dal punto di vista dell'infrastruttura, la distribuzione può non risentire di uno o più errori a livello di zona e ciò genera le domande seguenti:
  - Si comprende in che modo l'applicazione valuta questi errori e la capacità degradata?
  - È necessario disporre di misure di sicurezza nel servizio per forzare il failover a una coppia di aree se necessario?
  - Come verrà monitorato, rilevato e mitigato un simile scenario? Potrebbe essere possibile usare la diagnostica di Load Balancer Standard per aumentare il monitoraggio delle prestazioni del servizio end-to-end. Tenere in considerazione quanto è disponibile e ciò che potrebbe essere necessario aumentare per un quadro completo.

- Le zone possono rendere gli errori più facilmente comprensibili e contenuti.  Tuttavia, gli errori a livello di zona non sono diversi dagli altri errori quando si tratta di concetti quali timeout, nuovi tentativi e algoritmi di backoff. Anche se Azure Load Balancer fornisce percorsi con ridondanza della zona e tenta di recuperare rapidamente, a livello di pacchetti in tempo reale, possono verificarsi ritrasmissioni o ripristini all'insorgere di un errore ed è importante comprendere in che modo l'applicazione gestisce gli errori. Lo schema di bilanciamento del carico non subirà le ripercussioni degli errori, ma è necessario pianificare gli aspetti seguenti:
  - Quando in una zona si verifica un errore, il servizio end-to-end lo comprende e se lo stato viene perso in che modo si procederà al ripristino?
  - Quando una zona viene ripristinata, l'applicazione comprende come eseguire la convergenza in modo sicuro?

Esaminare i modelli di [progettazione cloud](https://docs.microsoft.com/azure/architecture/patterns/) di Azure per migliorare la resilienza dell'applicazione in scenari di errore.

## <a name="next-steps"></a>Passaggi successivi
- Ulteriori informazioni sulle [zone di disponibilità](../availability-zones/az-overview.md)
- Altre informazioni su [Load Balancer StandardLearn more](load-balancer-standard-overview.md) about Standard Load Balancer
- Altre informazioni su come [eseguire il bilanciamento del carico delle macchine virtuali in una zona usando il servizio Load Balancer Standard con un front-end di zona](load-balancer-standard-public-zonal-cli.md)
- Altre informazioni su come [eseguire il bilanciamento del carico delle macchine virtuali in più zone usando il servizio Load Balancer Standard con un front-end con ridondanza della zona](load-balancer-standard-public-zone-redundant-cli.md)
- Informazioni sui modelli di [progettazione cloud](https://docs.microsoft.com/azure/architecture/patterns/) di Azure per migliorare la resilienza dell'applicazione in scenari di errore.
