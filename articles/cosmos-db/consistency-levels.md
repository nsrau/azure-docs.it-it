---
title: Livelli di coerenza in Azure Cosmos DB
description: Cosmos DB ha cinque livelli di coerenza per consentire di compensare scompensi di coerenza, disponibilità e latenza finale.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/27/2018
ms.openlocfilehash: 914933e4e0489d68640edb58ceb91dc73a963eb3
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/04/2019
ms.locfileid: "54034965"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Livelli di coerenza in Azure Cosmos DB

I database distribuiti che si basano sulla replica per garantire disponibilità elevata, bassa latenza o entrambe, applicano il compromesso fondamentale tra coerenza di lettura e disponibilità, latenza e velocità effettiva. La maggior parte dei database distribuiti disponibili in commercio richiede che gli sviluppatori scelgano tra due modelli di coerenza estremi: la coerenza assoluta e la coerenza finale. La  [linearizzabilità](https://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) o il modello di coerenza assoluta rappresenta lo standard assoluto di programmabilità dei dati, ma aggiunge il caro prezzo di una latenza elevata (in uno stato stabile) e di una disponibilità ridotta (in caso di errori). La coerenza finale, d'altra parte, offre maggiore disponibilità e migliori prestazioni, ma è difficile da programmare. 

Azure Cosmos DB affronta la coerenza dei dati offrendo uno spettro di scelte che si collocano tra i due estremi. La coerenza assoluta e la coerenza finale rappresentano le due estremità, ma all'interno dello spettro sono disponibili molte altre opzioni. Gli sviluppatori possono usare queste opzioni per effettuare scelte precise e compromessi di granularità in relazione alla disponibilità elevata o alle prestazioni. 

Con Azure Cosmos DB, gli sviluppatori possono scegliere tra cinque modelli di coerenza ben definiti nello spettro della coerenza. Dal più forte al più debole, un modello può essere assoluto, con obsolescenza associata, sessione, coerenza del prefisso ed eventuale. I modelli sono ben definiti e intuitivi e possono essere usati per scenari reali specifici. Ogni modello prevede [compromessi di disponibilità e prestazioni ](consistency-levels-tradeoffs.md) ed è supportato da un contratto di servizio completo. L'immagine seguente illustra diversi livelli di coerenza sotto forma di spettro.

![La coerenza come spettro](./media/consistency-levels/five-consistency-levels.png)

I livelli di coerenza sono indipendenti dall'area. Il livello di coerenza dell'account Azure Cosmos è garantito per tutte le operazioni di lettura, indipendentemente dall'area da cui vengono gestite le operazioni di lettura e scrittura, dal numero di aree associate all'account Azure Cosmos o dal fatto che l'account sia configurato con una o più aree di scrittura.

## <a name="scope-of-the-read-consistency"></a>Ambito della coerenza di lettura

La coerenza di lettura si applica a una singola operazione di lettura il cui ambito si trova in un intervallo di chiavi di partizione o in una partizione logica. L'operazione di lettura può essere generata da un client remoto o da una stored procedure.

## <a name="configure-the-default-consistency-level"></a>Configurare il livello di coerenza predefinito

È possibile configurare il livello di coerenza predefinito nell'account Azure Cosmos in qualsiasi momento. Il livello di coerenza predefinito configurato per l'account si applica a tutti i database di Azure Cosmos DB e ai contenitori in tale account. Per impostazione predefinita, tutte le operazioni di lettura e le query eseguite su un contenitore o un database usano il livello di coerenza specificato. Per altre informazioni, vedere come [configurare il livello di coerenza predefinito](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garanzie associate ai livelli di coerenza

I contratti di servizio completi offerti da Azure Cosmos DB garantiscono che il 100% delle richieste di lettura soddisfano la garanzia di coerenza per qualsiasi livello di coerenza scelto. Una richiesta soddisfa il contratto di servizio per la coerenza se sono soddisfatte tutte le garanzie associate al livello di coerenza. Le definizioni precise dei cinque livelli di coerenza in Azure Cosmos DB che usano il [linguaggio di specifica TLA+](https://lamport.azurewebsites.net/tla/tla.html) sono disponibili nel repository GitHub [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla). 

La semantica dei cinque livelli di coerenza è descritta qui:

- **Assoluta**: La coerenza assoluta offre una garanzia di [linearizzabilità](https://aphyr.com/posts/313-strong-consistency-models). È garantito che le letture restituiscano sempre la versione di un elemento di cui sia stato eseguito il commit più di recente. Un client non visualizza mai una scrittura parziale o di cui non sia stato eseguito il commit. Gli utenti possono sempre essere certi di leggere la scrittura più recente sottoposta a commit.

- **Decadimento ristretto**: è garantito che le operazioni di lettura rispettino la garanzia della coerenza del prefisso. Le operazioni di lettura possono essere in ritardo rispetto alle operazioni di scrittura al massimo per "K" versioni (ovvero "aggiornamenti") di un elemento o per un intervallo di tempo "t". Quando si sceglie l'obsolescenza associata, è possibile configurare l'obsolescenza in due modi: 

  * Numero di versioni (K) dell'elemento
  * Intervallo di tempo (t) in cui le operazioni di lettura possono essere in ritardo rispetto alle operazioni di scrittura 

  Il decadimento ristretto offre un ordine globale totale tranne all'interno della "finestra di decadimento". La garanzia di lettura monotona esiste in un'area sia all'interno che all'esterno della finestra di obsolescenza. La coerenza assoluta è basata sulla stessa semantica della coerenza con obsolescenza associata. La finestra di obsolescenza è uguale a zero. L'obsolescenza associata è anche definita linearizzabilità posticipata. Quando un client esegue operazioni di lettura all'interno di un'area che accetta operazioni di scrittura, le garanzie offerte dal livello di coerenza con obsolescenza associata sono identiche a quelle della coerenza assoluta.

- **Sessione**: questo livello assicura che le operazioni di lettura rispettino le garanzie della coerenza del prefisso (presupponendo una singola sessione di scrittura), delle letture monotoniche, delle scritture monotoniche, della lettura delle proprie scritture e delle letture che seguono la scrittura. L'ambito di una coerenza di sessione è una sessione client.

- **Coerenza del prefisso**: gli aggiornamenti restituiti contengono un prefisso di tutti gli aggiornamenti, senza lacune. La coerenza del prefisso garantisce che le operazioni di lettura non riscontrino mai scritture non in ordine.

- **Finale**: Non c'è garanzia di ordinamento per le letture. In assenza di altre scritture, le repliche finiscono per convergere.

## <a name="consistency-levels-explained-through-baseball"></a>I livelli di coerenza spiegati attraverso il baseball

Come esempio, ecco uno scenario tratto dal baseball. Si immagini una sequenza di operazioni di scrittura che rappresentano il punteggio di una partita di baseball. Il punteggio inning per inning è descritto nel documento [Replicated data consistency through baseball](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) (La coerenza dei dati replicati spiegata tramite il baseball). In questa ipotetica partita di baseball si sta svolgendo il settimo inning. È la dirittura del settimo inning. La squadra ospite perde 2 a 5.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Punti** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Ospiti** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Locali** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

I punti totali della squadra ospite e della squadra locale si trovano in un contenitore Azure Cosmos DB. Nel corso della partita, garanzie di lettura diverse possono determinare la lettura di punteggi diversi da parte dei client. La tabella seguente elenca il set completo di punteggi che possono essere restituiti dalla lettura dei punteggi delle due squadre con ognuna delle cinque garanzie di coerenza. Il punteggio della squadra ospite viene elencato per primo. Possibili valori restituiti diversi sono separati da virgole.

| **Livello di coerenza** | **Punteggio** |
| - | - |
| **Assoluto** | 2-5 |
| **Obsolescenza associata** | Punteggi non aggiornati di un inning al massimo: 2-3, 2-4, 2-5 |
| **Sessione** | <ul><li>Per il writer: 2-5</li><li> Per chiunque, eccetto il writer: 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5</li><li>Dopo la lettura 1-3: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Coerenza del prefisso** | 0-0, 0-1, 1-1, 1-2, 1-3, 2-3, 2-4, 2-5 |
| **Finale** | 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5 |

## <a name="additional-reading"></a>Informazioni aggiuntive

Per altre informazioni sui concetti di coerenza, vedere gli articoli seguenti:

- [High-level TLA+ specifications for the five consistency levels offered by Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla) (Specifiche TLA+ di alto livello per i cinque livelli di coerenza offerti da Azure Cosmo DB)
- [Replicated Data Consistency Explained Through Baseball (video)](https://www.youtube.com/watch?v=gluIh8zd26I) (La coerenza dei dati replicati illustrata attraverso il baseball) di Doug Terry
- [Replicated Data Consistency Explained through Baseball (white paper)](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf) (La coerenza dei dati replicati illustrata attraverso il baseball) di Doug Terry
- [Session Guarantees for weakly consistent replicated data](https://dl.acm.org/citation.cfm?id=383631) (Garanzie di sessione per i dati replicati con coerenza debole)
- [Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is Only Part of the Story](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html) (Compromessi sulla coerenza nella progettazione di sistemi di database distribuiti moderni: CAP è solo una parte della storia)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (Decadimento ristretto probabilistico per quorum parziali pratici)
- [Eventually Consistent - Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html) (Coerenza finale - Rivisitato)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui livelli di coerenza in Azure Cosmo DB, vedere gli articoli seguenti:

* [Scegliere il livello di coerenza ottimale per la propria applicazione](consistency-levels-choosing.md)
* [Livelli di coerenza nelle API di Azure Cosmos DB](consistency-levels-across-apis.md)
* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Configurare il livello di coerenza predefinito](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Sostituire il livello di coerenza predefinito](how-to-manage-consistency.md#override-the-default-consistency-level)

