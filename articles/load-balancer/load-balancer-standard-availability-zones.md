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
ms.date: 05/07/2020
ms.author: allensu
ms.openlocfilehash: 55a86eeee4f819955e3f8adfcc0f55f24d58bed0
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87420312"
---
# <a name="standard-load-balancer-and-availability-zones"></a>Load Balancer Standard e zone di disponibilità

Azure Load Balancer Standard supporta scenari di zone di disponibilità. È possibile usare Load Balancer standard per aumentare la disponibilità in tutto lo scenario allineando le risorse con e distribuendo tra le zone. Le zone di disponibilità in combinazione con Load Balancer standard sono un set di funzionalità flessibile e flessibile che consente di creare molti scenari diversi.  Leggere questo documento per comprendere questi [concetti](#concepts) e le [linee guida di progettazione](#design) degli scenari fondamentali.

## <a name="availability-zones-concepts-applied-to-load-balancer"></a><a name="concepts"></a> Concetti relativi alle zone di disponibilità applicati a Load Balancer

Un servizio di bilanciamento del carico eredita la configurazione della zona dai relativi componenti: 

* Front-end
* Regole
* Definizione del pool back-end

Nel contesto delle zone di disponibilità, il comportamento e le proprietà di una regola di bilanciamento del carico vengono descritti come con ridondanza della zona o di zona.  Nel contesto del servizio di bilanciamento del carico, con ridondanza della zona si intende sempre **più zone** e la zona è l'isolamento del servizio in una **singola zona**. Un Azure Load Balancer dispone di due tipi, Public e Internal. Entrambi i tipi di bilanciamento del carico supportano la ridondanza della zona e la distribuzione di zona.  Entrambi i tipi di servizio di bilanciamento del carico possono indirizzare il traffico tra le zone secondo necessità.

## <a name="frontend"></a>Front-end

Un front-end di bilanciamento del carico è una configurazione IP front-end che fa riferimento a un indirizzo IP pubblico o a un indirizzo IP privato all'interno della subnet di una rete virtuale. Costituisce l'endpoint con carico bilanciato in cui viene esposto il servizio.

Una risorsa di bilanciamento del carico può includere contemporaneamente regole con front-end con ridondanza della zona e di zona. Quando un indirizzo IP pubblico o privato è garantito per una zona, la zonalità (o la sua mancanza) non è modificabile. Per modificare o omettere il zonalità di un front-end di indirizzo IP pubblico o privato, ricreare l'IP nell'area appropriata. 

Le zone di disponibilità non modificano i vincoli per più front-end. Per informazioni dettagliate su questa funzionalità, vedere più Front- [end per Load Balancer](load-balancer-multivip-overview.md) .

### <a name="zone-redundant"></a>Con ridondanza della zona 

In un'area con zone di disponibilità, un front-end di bilanciamento del carico standard può essere con ridondanza della zona. Più zone possono essere utilizzate in ingresso o in uscita in un'area. Questo traffico viene servito da un singolo indirizzo IP. Gli schemi di ridondanza DNS non sono necessari. 

Un singolo indirizzo IP front-end sopravviverà in caso di errore della zona. L'IP front-end può essere usato per raggiungere tutti i membri del pool back-end (non interessati), a prescindere dalla zona. Una o più zone di disponibilità possono avere esito negativo e il percorso dei dati rimane integro fino a quando una zona dell'area rimane integra. 

L'indirizzo IP del front-end viene servito simultaneamente da più distribuzioni di infrastrutture indipendenti in più zone di disponibilità. Eventuali tentativi o ristabilimenti riusciranno in altre zone non interessate dall'errore della zona. 

<p align="center">
  <img src="./media/az-zonal/zone-redundant-lb-1.svg" alt="Figure depicts a zone-redundant standard load balancer directing traffic in three different zones to three different subnets in a zone redundant configuration." width="512" title="NAT di rete virtuale">
</p>

*Figura: bilanciamento del carico con ridondanza della zona*

### <a name="zonal"></a>Zona

È possibile scegliere di avere un front-end garantito per una singola zona, noto come *front-end di zona*.  Questo scenario indica che qualsiasi flusso in ingresso o in uscita viene servito da una singola zona in un'area.  La durata del front-end è legata all'integrità della zona.  Il percorso dati non è interessato dagli errori in zone diverse da quelle in cui è stato garantito. È possibile usare front-end di zona per esporre un indirizzo IP per zona di disponibilità.  

Inoltre, è supportato l'uso diretto dei front-end di zona per gli endpoint con carico bilanciato all'interno di ogni zona. È possibile usare questa configurazione per esporre gli endpoint con carico bilanciato per zona per monitorare singolarmente ogni zona. Per gli endpoint pubblici, è possibile integrarli con un prodotto di bilanciamento del carico DNS come [Gestione traffico](../traffic-manager/traffic-manager-overview.md) e usare un singolo nome DNS.


<p align="center">
  <img src="./media/az-zonal/zonal-lb-1.svg" alt="Figure depicts three zonal standard load balancers each directing traffic in a zone to three different subnets in a zonal configuration." width="512" title="NAT di rete virtuale">
</p>

*Figura: bilanciamento del carico con ridondanza della zona*

Per un approfondimento di questi concetti (con ridondanza della zona e di zona per lo stesso back-end), consultare [Più front-end per Azure Load Balancer](load-balancer-multivip-overview.md).

Per un front-end di Load Balancer pubblico, aggiungere un parametro **Zones** all'indirizzo IP pubblico. Alla configurazione IP front-end usata dalla rispettiva regola viene fatto riferimento a questo indirizzo IP pubblico.

Per un front-end di bilanciamento del carico interno, aggiungere un parametro **Zones** alla configurazione IP del servizio di bilanciamento del carico interno. Un front-end di zona garantisce un indirizzo IP in una subnet a una zona specifica.

## <a name="backend"></a>Back-end

Azure Load Balancer funziona con le istanze di macchine virtuali. Queste istanze possono essere autonome, set di disponibilità o set di scalabilità di macchine virtuali. Tutte le macchine virtuali in una singola rete virtuale possono far parte del pool back-end, indipendentemente dalla zona a cui è garantita la macchina virtuale.

Per allineare e garantire il front-end e il back-end con una singola zona, posizionare solo le macchine virtuali all'interno della stessa zona nel pool back-end corrispondente.

Per indirizzare le macchine virtuali in più zone, inserire le macchine virtuali da più zone nello stesso pool back-end. 

Quando si usano i set di scalabilità di macchine virtuali, inserire uno o più set di scalabilità di macchine virtuali nello stesso pool back-end. I set di scalabilità possono trovarsi in una o più zone.

## <a name="outbound-connections"></a>Connessioni in uscita

Le connessioni con ridondanza della zona e di zona si applicano alle [connessioni in uscita](load-balancer-outbound-connections.md). Un indirizzo IP pubblico con ridondanza della zona usato per le connessioni in uscita è servito da tutte le zone. Un indirizzo IP pubblico di zona viene servito solo dalla zona in cui è garantita. 

Le allocazioni delle porte SNAT della connessione in uscita sopravvivono agli errori di zona e lo scenario continuerà a fornire connettività SNAT in uscita se non è interessato da un errore della zona. Gli errori di zona possono richiedere che le connessioni vengano ristabilite per gli scenari con ridondanza della zona se il traffico è stato servito da una zona interessata. I flussi in zone diverse dalle zone interessate non sono interessati.

L'algoritmo di preallocazione della porta SNAT è lo stesso con o senza zone di disponibilità.

## <a name="health-probes"></a>Probe di integrità

Le definizioni dei probe di integrità esistenti rimangono invariate senza zone di disponibilità. Tuttavia, il modello di integrità è stato espanso a livello di infrastruttura. 

Quando si usano i front-end con ridondanza della zona, il servizio di bilanciamento del carico espande il controllo dell'integrità interno. Il servizio di bilanciamento del carico esegue la verifica indipendente della disponibilità di una macchina virtuale da ogni zona e arresta i percorsi tra le zone che hanno avuto esito negativo senza intervento.  

Altre zone che sono in grado di raggiungere questa macchina virtuale possono continuare a rendere disponibile la macchina virtuale dai rispettivi front-end. Durante gli eventi di errore, ogni zona può avere distribuzioni diverse di nuovi flussi proteggendo allo stesso tempo l'integrità complessiva del servizio.

## <a name="design-considerations"></a><a name="design"></a>Considerazioni sulla progettazione

Il servizio di bilanciamento del carico è flessibile nel contesto delle zone di disponibilità. È possibile scegliere di allinearsi alle zone o essere con ridondanza della zona per ogni regola. Una maggiore disponibilità può essere il prezzo di una maggiore complessità. Progettazione per la disponibilità per ottenere prestazioni ottimali.

### <a name="zone-redundancy"></a>Ridondanza della zona

Con Load Balancer è più semplice avere un singolo IP come front-end con ridondanza della zona. Un indirizzo IP con ridondanza della zona può gestire una risorsa di zona in qualsiasi area.  L'IP può sopravvivere a uno o più errori di zona, purché una zona rimanga integra all'interno dell'area.  Un front-end di zona è invece una riduzione del servizio in una singola zona e condivide il destino con la rispettiva zona.

La ridondanza della zona non implica il percorso di hitless o il piano di controllo; si tratta del piano dati. I flussi con ridondanza della zona possono usare qualsiasi zona e i flussi di un cliente useranno tutte le zone integre in un'area. In un errore di zona non sono interessati flussi di traffico che usano zone integre. 

I flussi di traffico che usano una zona al momento dell'errore della zona possono essere interessati, ma le applicazioni possono essere ripristinate. Il traffico continua nelle zone integre all'interno dell'area durante la ritrasmissione quando Azure è convergente intorno all'errore della zona.

### <a name="cross-zone-boundaries"></a><a name="xzonedesign"></a> Limiti tra zone

È importante comprendere che ogni volta che un servizio attraversa le zone, si condivide il destino con una zona, ma potenzialmente più zone. Di conseguenza, il servizio potrebbe non avere acquisito alcuna disponibilità rispetto alle distribuzioni non di zona.

Evitare di introdurre dipendenze tra zone indesiderate, in modo da annullare i guadagni di disponibilità quando si usano le zone di disponibilità. Se l'applicazione contiene più componenti critici, verificare la sopravvivenza in caso di errore di una zona.

Un singolo componente critico per l'applicazione può avere un effetto sull'intera applicazione se esiste solo in una zona diversa dalle zone superstiti. Si consideri il ripristino della zona e il modo in cui l'applicazione convergerà. Comprendere il modo in cui l'applicazione risponde a errori di parti di esso. Esaminare i punti chiave e usarli per le domande quando si pensa a uno scenario specifico.

- Se l'applicazione dispone di due componenti:

    * Indirizzo IP
    * Macchina virtuale con disco gestito

Sono configurati nella zona 1 e nella zona 2. Quando la zona 1 ha esito negativo, il servizio non sopravvive. Non attraversare le zone con scenari di zona, a meno che non si sia consapevoli che si sta creando una modalità di errore potenzialmente pericolosa. Questo scenario può offrire flessibilità.

- Se l'applicazione dispone di due componenti:

    * Indirizzo IP
    * Macchina virtuale con disco gestito

Sono configurati per essere con ridondanza della zona e zona 1. Il servizio sopravviverà in caso di errore della zona di zona 2, zona 3 o a entrambi, a meno che l'area 1 non sia riuscita. Tuttavia, si perde la possibilità di ragionare sull'integrità del servizio, se tutto ciò che si sta osservando è la raggiungibilità del front-end.  Prendere in considerazione l'idea di sviluppare un modello di integrità e di capacità più esteso.  È possibile usare in combinazione i concetti di ridondanza della zona e di zona per aumentare il dettaglio delle informazioni e la gestibilità.

- Se l'applicazione dispone di due componenti:

    * Front-end di bilanciamento del carico con ridondanza della zona
    * Set di scalabilità di macchine virtuali tra zone in tre zone

Le risorse in zone non interessate dall'errore saranno disponibili. È possibile che la capacità del servizio sia diminuita durante l'errore. Dal punto di vista dell'infrastruttura, la distribuzione può sopravvivere a uno o più errori di zona. Questo scenario genera le domande seguenti:

  - Si comprende il modo in cui l'applicazione risponde a errori e capacità ridotta?
  - È necessario disporre di misure di sicurezza nel servizio per forzare il failover a una coppia di aree se necessario?
  - Come verrà monitorato, rilevato e mitigato un simile scenario? È possibile usare la diagnostica di Load Balancer standard per aumentare il monitoraggio delle prestazioni del servizio. Prendere in considerazione gli elementi disponibili e gli elementi che potrebbero richiedere un aumento.

- Le zone possono rendere gli errori più facilmente comprensibili e contenuti. L'errore della zona non è diverso dagli altri errori quando si tratta di timeout, tentativi e algoritmi backoff. Azure Load Balancer fornisce percorsi con ridondanza della zona. Il servizio di bilanciamento del carico tenta di eseguire rapidamente il ripristino a livello di pacchetto in tempo reale. Le ritrasmissioni o i ristabilimenti possono verificarsi durante l'inizio di un errore. È importante comprendere il modo in cui l'applicazione gestisce gli errori. Lo schema di bilanciamento del carico sopravviverà, ma è necessario pianificare le domande seguenti:

  - Quando una zona ha esito negativo, il servizio riconosce questo errore e se lo stato viene perso, come verrà ripristinato?
  - Quando una zona viene ripristinata, l'applicazione comprende come eseguire la convergenza in modo sicuro?

Esaminare i [modelli di progettazione cloud di Azure](https://docs.microsoft.com/azure/architecture/patterns/) per migliorare la resilienza dell'applicazione in scenari di errore.

## <a name="next-steps"></a>Passaggi successivi
- Altre informazioni su [zone di disponibilità](../availability-zones/az-overview.md)
- Altre informazioni su [Load Balancer standard](load-balancer-standard-overview.md)
- Altre informazioni su come [eseguire il bilanciamento del carico delle macchine virtuali in una zona usando il servizio Load Balancer Standard con un front-end di zona](load-balancer-standard-public-zonal-cli.md)
- Altre informazioni su come [eseguire il bilanciamento del carico delle macchine virtuali in più zone usando il servizio Load Balancer Standard con un front-end con ridondanza della zona](load-balancer-standard-public-zone-redundant-cli.md)
- Informazioni sui [modelli di progettazione cloud di Azure](https://docs.microsoft.com/azure/architecture/patterns/) per migliorare la resilienza dell'applicazione in scenari di errore.
