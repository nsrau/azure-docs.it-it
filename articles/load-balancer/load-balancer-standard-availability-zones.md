---
title: Azure Load Balancer Standard e zone di disponibilità
titleSuffix: Azure Load Balancer
description: Con questo percorso di apprendimento, inizia a usare Load Balancer Standard e zone di disponibilità di Azure.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/30/2020
ms.author: allensu
ms.openlocfilehash: 5ecfbc610bfa62f723e0a02b8cdeb52cd33fb5cd
ms.sourcegitcommit: c535228f0b77eb7592697556b23c4e436ec29f96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82853450"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer Standard e zone di disponibilità

Azure Load Balancer Standard supporta scenari di [zone di disponibilità](../availability-zones/az-overview.md) . È possibile usare Load Balancer Standard per ottimizzare la disponibilità nello scenario end-to-end allineando le risorse alle zone e distribuendo le risorse tra le zone.  Esaminare le [zone di disponibilità](../availability-zones/az-overview.md) per indicazioni sulle zone di disponibilità, quali aree attualmente supportano le zone di disponibilità e altri concetti e prodotti correlati. Le zone di disponibilità in combinazione con Load Balancer Standard sono un set di funzionalità espansivo e flessibile che consente di creare molti scenari diversi.  Leggere questo documento per comprendere questi [concetti](#concepts) e le [linee guida di progettazione](#design) degli scenari fondamentali.

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a> Concetti relativi alle zone di disponibilità applicati a Load Balancer

Una risorsa Load Balancer stessa eredita la configurazione della zona dai componenti: front-end, regola e definizione del pool back-end.
Nel contesto delle zone di disponibilità, il comportamento e le proprietà di una regola di Load Balancer vengono descritti come con ridondanza della zona o di zona.  Nel contesto di Load Balancer, il ridondanza della zona significa sempre *più zone* e la zona è l'isolamento del servizio in una *singola zona*.
Entrambi i tipi (Public, Internal) Load Balancer supportano scenari con ridondanza della zona e di zona ed entrambi possono indirizzare il traffico tra le zone in base alle esigenze.

## <a name="frontend"></a>Front-end

Un Front-End Load Balancer è una configurazione IP front-end che fa riferimento a una risorsa indirizzo IP pubblico o a un indirizzo IP privato all'interno della subnet di una risorsa di rete virtuale.  Costituisce l'endpoint con carico bilanciato in cui viene esposto il servizio.
Una risorsa Load Balancer può contenere contemporaneamente regole con front-end con ridondanza della zona e di zona. Quando una risorsa IP pubblico o un indirizzo IP privato è stata garantita a una zona, la zonalità (o la sua mancanza) non è modificabile.  Se si vuole modificare o omettere il zonalità di un indirizzo IP pubblico o un front-end di indirizzo IP privato, è necessario ricreare l'IP pubblico nell'area appropriata.  Le zone di disponibilità non modificano i vincoli per più front-end, esaminare più Front- [end per Load Balancer](load-balancer-multivip-overview.md) per informazioni dettagliate su questa funzionalità.

### <a name="zone-redundant"></a>Con ridondanza della zona 

In un'area con zone di disponibilità, un front-end di Load Balancer Standard può essere con ridondanza della zona.  Con ridondanza della zona si intende che tutti i flussi in ingresso o in uscita sono serviti da più zone di disponibilità in un'area contemporaneamente usando un unico indirizzo IP. Gli schemi di ridondanza DNS non sono necessari. Un singolo indirizzo IP front-end può sopravvivere a un errore di zona e può essere usato per raggiungere tutti i membri del pool back-end (non interessati) indipendentemente dalla zona. Una o più zone di disponibilità possono avere esito negativo e il percorso dei dati rimane integro fino a quando una zona dell'area rimane integra. Il singolo indirizzo IP del front-end viene servito simultaneamente da più distribuzioni di infrastrutture indipendenti in più zone di disponibilità.  Questo non significa il percorso dei dati di hitless, ma qualsiasi tentativo o ristabilimento avrà esito positivo in altre zone non interessate dall'errore della zona.   

### <a name="zonal"></a>Zona

È possibile scegliere di avere un front-end garantito per una singola zona, noto come *front-end di zona*.  Ciò significa che qualsiasi flusso in ingresso o in uscita viene gestito da una singola zona in un'area.  La durata del front-end è legata all'integrità della zona.  Il percorso dati non è interessato dagli errori in zone diverse da quelle in cui è stato garantito. È possibile usare front-end di zona per esporre un indirizzo IP per zona di disponibilità.  

Inoltre, è possibile utilizzare i front-end di zona direttamente per gli endpoint con bilanciamento del carico all'interno di ogni zona. È anche possibile ricorrere a questo metodo per esporre endpoint con carico bilanciato per zona, in modo da monitorare singolarmente ogni zona.  In alternativa, per gli endpoint pubblici è possibile integrarli con un prodotto di bilanciamento del carico DNS come [Gestione traffico](../traffic-manager/traffic-manager-overview.md) e usare un singolo nome DNS. Il client verrà quindi risolto in questo nome DNS in più indirizzi IP di zona.  

Per un approfondimento di questi concetti (con ridondanza della zona e di zona per lo stesso back-end), consultare [Più front-end per Azure Load Balancer](load-balancer-multivip-overview.md).

Per un Front-End Load Balancer pubblico, aggiungere un parametro *Zones* alla risorsa IP pubblica a cui fa riferimento la configurazione IP front-end usata dalla rispettiva regola.

Per un front-end di Load Balancer interno, è necessario aggiungere un parametro *zones* alla configurazione IP front-end dell'istanza di Load Balancer interna. Il front-end di zona fa sì che Load Balancer garantisca un indirizzo IP in una subnet per una zona specifica.

## <a name="backend"></a>Back-end

Load Balancer funziona con le istanze di macchine virtuali.  Possono essere autonomi, set di disponibilità o set di scalabilità di macchine virtuali.  Tutte le istanze di macchine virtuali in una singola rete virtuale possono far parte del pool back-end indipendentemente dal fatto che sia garantita o meno a una zona o a quale zona è stata garantita.

Se si vuole allineare e garantire il front-end e il back-end con una singola zona, posizionare solo le macchine virtuali all'interno della stessa zona nel rispettivo pool back-end.

Se si vuole indirizzare le macchine virtuali in più zone, è sufficiente collocare le macchine virtuali da più zone nello stesso pool back-end.  Quando si usano set di scalabilità di macchine virtuali, è possibile inserire uno o più set di scalabilità di macchine virtuali nello stesso pool back-end.  Ognuno di questi set di scalabilità di macchine virtuali può essere inserito in una o più zone.

## <a name="outbound-connections"></a>Connessioni in uscita

Le stesse proprietà con ridondanza della zona e di zona si applicano alle [connessioni in uscita](load-balancer-outbound-connections.md).  Un indirizzo IP pubblico con ridondanza della zona usato per le connessioni in uscita è servito da tutte le zone. Un indirizzo IP pubblico di zona viene servito solo dalla zona in cui è garantita.  Le allocazioni delle porte SNAT della connessione in uscita sopravvivono agli errori di zona e lo scenario continuerà a fornire connettività SNAT in uscita se non è influenzato da un errore della zona.  Questo può richiedere trasmissioni o per ristabilire le connessioni per scenari con ridondanza della zona se un flusso è stato gestito da una zona interessata.  I flussi in zone diverse dalle zone interessate non sono interessati.

L'algoritmo di preallocazione della porta SNAT è lo stesso con o senza zone di disponibilità.

## <a name="health-probes"></a>Probe di integrità

Le definizioni dei probe di integrità esistenti rimangono invariate senza zone di disponibilità.  Tuttavia, il modello di integrità è stato espanso a livello di infrastruttura. 

Quando si usano i front-end con ridondanza della zona, Load Balancer espande il modello di integrità interno per verificare in modo indipendente la raggiungibilità di una macchina virtuale da ogni zona di disponibilità e arrestare i percorsi tra le zone che potrebbero avere avuto esito negativo senza l'intervento del cliente.  Se un percorso specificato non è disponibile dall'infrastruttura Load Balancer di una zona a una macchina virtuale in un'altra area, Load Balancer possibile rilevare ed evitare questo errore. Altre zone che sono in grado di raggiungere questa macchina virtuale possono continuare a rendere disponibile la macchina virtuale dai rispettivi front-end.  Di conseguenza, è possibile che, durante gli eventi di errore, ogni zona possa avere distribuzioni leggermente diverse di nuovi flussi proteggendo allo stesso tempo l'integrità complessiva del servizio end-to-end.

## <a name="design-considerations"></a><a name="design"></a>Considerazioni sulla progettazione

Load Balancer è appositamente flessibile nel contesto delle zone di disponibilità. È possibile scegliere di allinearsi alle zone oppure è possibile scegliere di essere con ridondanza della zona per ogni regola.  A una maggiore disponibilità può corrispondere un aumento della complessità, pertanto per prestazioni ottimali è richiesta una progettazione orientata alla disponibilità.  Verranno ora prese in esame alcune importanti considerazioni sulla progettazione.

### <a name="automatic-zone-redundancy"></a>Ridondanza automatica della zona

Con Load Balancer è più semplice avere un singolo IP come front-end con ridondanza della zona. Un indirizzo IP con ridondanza della zona può rendere disponibile in modo sicuro una risorsa di zona in qualsiasi zona e può non risentire di uno o più errori a livello di zona fintanto che una zona rimane integra all'interno dell'area. Al contrario, un front-end di zona è una riduzione del servizio a una singola zona e la sua durata è legata alla rispettiva zona.

La ridondanza della zona non implica un percorso dati privo di passaggi o un piano di controllo; si tratta espressamente di un piano dati. I flussi con ridondanza della zona possono usare qualsiasi zona e i flussi di un cliente useranno tutte le zone integre in un'area. In caso di errore di una zona, i flussi di traffico che usano le zone integre in quel dato momento non sono interessati.  I flussi di traffico che usano una zona nel momento in cui si verifica un errore di zona possono essere interessati, ma le applicazioni possono essere ripristinate. Questi flussi possono continuare nelle zone integre rimanenti all'interno dell'area durante la ritrasmissione o il ristabilimento, una volta che Azure è convergente in base all'errore della zona.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a> Limiti tra zone

È importante comprendere che ogni volta che un servizio end-to-end attraversa le zone, la sua durata è legata non solo a una zona ma potenzialmente a più zone.  Di conseguenza, il servizio end-to-end potrebbe non garantire maggiore disponibilità rispetto alle distribuzioni non di zona.

Evitare di introdurre dipendenze tra zone indesiderate, in modo da annullare i guadagni di disponibilità quando si usano le zone di disponibilità.  Quando l'applicazione è costituita da più componenti e si vuole ottenere la resilienza agli errori a livello di zona, è necessario prestare attenzione a garantire che un numero sufficiente di componenti critici non risenta degli errori in caso di problemi con una zona.  Ad esempio, un singolo componente critico per l'applicazione può influire sull'intera applicazione se esiste solo in una zona diversa da quelle che non risentono degli errori.  Inoltre, prendere in considerazione anche il ripristino della zona e come l'applicazione eseguirà la convergenza. È necessario comprendere il modo in cui i motivi dell'applicazione sono relativi a errori di parti del sistema. Verranno presi in esame alcuni punti chiave che saranno usati come riferimento per eventuali domande relative a scenari specifici.

- Se l'applicazione ha due componenti come un indirizzo IP e una macchina virtuale con disco gestito, che sono garantiti nell'area 1 e nella zona 2, quando la zona 1 ha esito negativo, il servizio end-to-end non sopravviverà quando l'area 1 ha esito negativo.  Non attraversare le zone con scenari di zona, a meno che non si sia consapevoli di aver creato una modalità di errore potenzialmente pericolosa.  Questo scenario può offrire flessibilità.

- Se l'applicazione è costituita da due componenti come un indirizzo IP e una macchina virtuale con disco gestito ed è garantita la ridondanza della zona e la zona 1 rispettivamente, il servizio end-to-end sopravviverà a un errore di zona dell'area 2, zona 3 o a entrambi, a meno che non si sia verificato un errore nell'area 1.  Tuttavia, se ci si limita a prestare attenzione solo alla raggiungibilità del front-end si perde di vista l'integrità del servizio.  Prendere in considerazione l'idea di sviluppare un modello di integrità e di capacità più esteso.  È possibile usare in combinazione i concetti di ridondanza della zona e di zona per aumentare il dettaglio delle informazioni e la gestibilità.

- Se l'applicazione include due componenti come un front-end di Load Balancer con ridondanza della zona e un set di scalabilità di macchine virtuali tra zone in tre zone, le risorse nelle zone non interessate dall'errore saranno disponibili, ma la capacità del servizio end-to-end potrebbe risultare degradata durante l'errore della zona. Dal punto di vista dell'infrastruttura, la distribuzione può non risentire di uno o più errori a livello di zona e ciò genera le domande seguenti:
  - Si comprende in che modo l'applicazione valuta questi errori e la capacità degradata?
  - È necessario disporre di misure di sicurezza nel servizio per forzare il failover a una coppia di aree se necessario?
  - Come verrà monitorato, rilevato e mitigato un simile scenario? Potrebbe essere possibile usare la diagnostica di Load Balancer Standard per aumentare il monitoraggio delle prestazioni del servizio end-to-end. Tenere in considerazione quanto è disponibile e ciò che potrebbe essere necessario aumentare per un quadro completo.

- Le zone possono rendere gli errori più facilmente comprensibili e contenuti.  Tuttavia, gli errori a livello di zona non sono diversi dagli altri errori quando si tratta di concetti quali timeout, nuovi tentativi e algoritmi di backoff. Anche se Azure Load Balancer fornisce percorsi con ridondanza della zona e tenta di recuperare rapidamente, a livello di pacchetti in tempo reale, possono verificarsi ritrasmissioni o ripristini all'insorgere di un errore ed è importante comprendere in che modo l'applicazione gestisce gli errori. Lo schema di bilanciamento del carico non subirà le ripercussioni degli errori, ma è necessario pianificare gli aspetti seguenti:
  - Quando in una zona si verifica un errore, il servizio end-to-end lo comprende e se lo stato viene perso in che modo si procederà al ripristino?
  - Quando una zona viene ripristinata, l'applicazione comprende come eseguire la convergenza in modo sicuro?

Esaminare i [modelli di progettazione cloud di Azure](https://docs.microsoft.com/azure/architecture/patterns/) per migliorare la resilienza dell'applicazione in scenari di errore.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [zone di disponibilità](../availability-zones/az-overview.md)
- Altre informazioni su [Load Balancer standard](load-balancer-standard-overview.md)
- Altre informazioni su come [eseguire il bilanciamento del carico delle macchine virtuali in una zona usando il servizio Load Balancer Standard con un front-end di zona](load-balancer-standard-public-zonal-cli.md)
- Altre informazioni su come [eseguire il bilanciamento del carico delle macchine virtuali in più zone usando il servizio Load Balancer Standard con un front-end con ridondanza della zona](load-balancer-standard-public-zone-redundant-cli.md)
- Informazioni sui [modelli di progettazione cloud di Azure](https://docs.microsoft.com/azure/architecture/patterns/) per migliorare la resilienza dell'applicazione in scenari di errore.
