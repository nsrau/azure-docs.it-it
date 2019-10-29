---
title: Livelli di coerenza in Azure Cosmos DB
description: Cosmos DB ha cinque livelli di coerenza per consentire di compensare scompensi di coerenza, disponibilità e latenza finale.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 395b7bc31377fd771549a399032bad9d951ec804
ms.sourcegitcommit: 04ec7b5fa7a92a4eb72fca6c6cb617be35d30d0c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/22/2019
ms.locfileid: "68384931"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Livelli di coerenza in Azure Cosmos DB

I database distribuiti che si basano sulla replica per garantire disponibilità elevata, bassa latenza o entrambe, applicano il compromesso fondamentale tra coerenza di lettura e disponibilità, latenza e velocità effettiva. La maggior parte dei database distribuiti disponibili in commercio chiede agli sviluppatori di scegliere tra i due modelli di coerenza *estremi: coerenza* assoluta e coerenza *finale* . La linearizzabilità o il modello di coerenza forte è lo standard Gold della programmabilità dei dati. Ma aggiunge un prezzo di latenza maggiore (in stato stabile) e una disponibilità ridotta (durante gli errori). D'altra parte, la coerenza finale offre una maggiore disponibilità e prestazioni migliori, ma rende difficile programmare le applicazioni. 

Azure Cosmos DB affronta la coerenza dei dati offrendo uno spettro di scelte che si collocano tra i due estremi. La coerenza assoluta e la coerenza finale si trovano alla fine dello spettro, ma sono disponibili molte opzioni di coerenza lungo lo spettro. Gli sviluppatori possono usare queste opzioni per eseguire scelte precise e compromessi granulari rispetto alla disponibilità e alle prestazioni elevate. 

Con Azure Cosmos DB, gli sviluppatori possono scegliere tra cinque modelli di coerenza ben definiti nello spettro della coerenza. Dal più forte al più rilassato, i modelli includono *forte*,decadimento ristretto, *sessione*, *prefisso coerente*e coerenza *finale* . I modelli sono ben definiti e intuitivi e possono essere usati per scenari reali specifici. Ogni modello fornisce compromessi a livello di [disponibilità e prestazioni](consistency-levels-tradeoffs.md) ed è supportato dai contratti di contratto. Nell'immagine seguente vengono illustrati i diversi livelli di coerenza come uno spettro.

![La coerenza come spettro](./media/consistency-levels/five-consistency-levels.png)

I livelli di coerenza sono indipendenti dall'area e sono garantiti per tutte le operazioni, indipendentemente dall'area da cui vengono gestite le operazioni di lettura e scrittura, dal numero di aree associate all'account Azure Cosmos o dal fatto che l'account sia configurato con una singola o più aree di scrittura.

## <a name="scope-of-the-read-consistency"></a>Ambito della coerenza di lettura

La coerenza di lettura si applica a una singola operazione di lettura il cui ambito si trova in un intervallo di chiavi di partizione o in una partizione logica. L'operazione di lettura può essere generata da un client remoto o da una stored procedure.

## <a name="configure-the-default-consistency-level"></a>Configurare il livello di coerenza predefinito

È possibile configurare il livello di coerenza predefinito nell'account Azure Cosmos in qualsiasi momento. Il livello di coerenza predefinito configurato per l'account si applica a tutti i database e contenitori di Azure Cosmos con tale account. Per impostazione predefinita, tutte le operazioni di lettura e le query eseguite su un contenitore o un database usano il livello di coerenza specificato. Per altre informazioni, vedere come [configurare il livello di coerenza predefinito](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garanzie associate ai livelli di coerenza

I contratti di servizio completi offerti da Azure Cosmos DB garantiscono che il 100% delle richieste di lettura soddisfano la garanzia di coerenza per qualsiasi livello di coerenza scelto. Una richiesta soddisfa il contratto di servizio per la coerenza se sono soddisfatte tutte le garanzie associate al livello di coerenza. Le definizioni esatte dei cinque livelli di coerenza in Azure Cosmos DB usando il linguaggio di specifica di TLA + sono disponibili nel repository [Azure-Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) github.

La semantica dei cinque livelli di coerenza è descritta qui:

- **Assoluta**: La coerenza assoluta offre una garanzia della linearità. Della linearità si riferisce alle richieste di servizio simultaneamente. È garantito che le letture restituiscano sempre la versione di un elemento di cui sia stato eseguito il commit più di recente. Un client non visualizza mai una scrittura parziale o di cui non sia stato eseguito il commit. Gli utenti possono sempre essere certi di leggere la scrittura più recente sottoposta a commit.

- **Decadimento ristretto**: è garantito che le operazioni di lettura rispettino la garanzia della coerenza del prefisso. È possibile che le letture ritardino dietro le Scritture al massimo *"K"* versioni (ad esempio, "aggiornamenti") di un elemento o di un intervallo di tempo *"T"* . In altre parole, quando si sceglie il decadimento ristretto, il "obsolescenza" può essere configurato in due modi: 

  * Numero di versioni (*K*) dell'elemento
  * Intervallo di tempo (*T*) in base al quale le letture potrebbero ritardare le Scritture 

  Il decadimento ristretto offre un ordine globale totale tranne all'interno della "finestra di decadimento". La garanzia di lettura monotona esiste in un'area sia all'interno che all'esterno della finestra di obsolescenza. La coerenza assoluta ha la stessa semantica di quella offerta dall'obsolescenza associata. La finestra di obsolescenza è uguale a zero. L'obsolescenza associata è anche definita linearizzabilità posticipata. Quando un client esegue le operazioni di lettura all'interno di un'area che accetta Scritture, le garanzie fornite dalla coerenza con decadimento ristretto sono identiche a quelle garantite dalla coerenza assoluta.

- **Sessione**:  All'interno di una singola sessione del client le letture sono garantite per rispettare il prefisso coerente (presupponendo una singola sessione di "writer"), letture monotone, scritture monotone, read-your-Scritture e scritture-follow-letture. I client al di fuori della sessione che esegue le Scritture visualizzeranno la coerenza finale.

- **Coerenza del prefisso**: gli aggiornamenti restituiti contengono un prefisso di tutti gli aggiornamenti, senza lacune. Il livello di coerenza del prefisso coerente garantisce che le letture non visualizzino mai le Scritture non ordinate.

- **Finale**: Non c'è garanzia di ordinamento per le letture. In assenza di altre scritture, le repliche finiscono per convergere.

## <a name="consistency-levels-explained-through-baseball"></a>I livelli di coerenza spiegati attraverso il baseball

Come esempio, ecco uno scenario tratto dal baseball. Si immagini una sequenza di operazioni di scrittura che rappresentano il punteggio di una partita di baseball. Il punteggio inning per inning è descritto nel documento [Replicated data consistency through baseball](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) (La coerenza dei dati replicati spiegata tramite il baseball). In questa ipotetica partita di baseball si sta svolgendo il settimo inning. È la dirittura del settimo inning. I visitatori sono dietro il punteggio da 2 a 5, come illustrato di seguito:

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Punti** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Ospiti** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Locali** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Un contenitore di Azure Cosmos include i totali di esecuzione per i visitatori e i team domestici. Nel corso della partita, garanzie di lettura diverse possono determinare la lettura di punteggi diversi da parte dei client. La tabella seguente elenca il set completo di punteggi che possono essere restituiti dalla lettura dei punteggi delle due squadre con ognuna delle cinque garanzie di coerenza. Il punteggio della squadra ospite viene elencato per primo. Possibili valori restituiti diversi sono separati da virgole.

| **Livello di coerenza** | **Punteggi (visitatori, Home)** |
| - | - |
| **Assoluto** | 2-5 |
| **Obsolescenza associata** | Punteggi non aggiornati di un inning al massimo: 2-3, 2-4, 2-5 |
| **Sessione** | <ul><li>Per il writer: 2-5</li><li> Per chiunque, eccetto il writer: 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5</li><li>Dopo la lettura 1-3: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Coerenza del prefisso** | 0-0, 0-1, 1-1, 1-2, 1-3, 2-3, 2-4, 2-5 |
| **Finale** | 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5 |

## <a name="additional-reading"></a>Altre informazioni

Per altre informazioni sui concetti di coerenza, vedere gli articoli seguenti:

- [High-level TLA+ specifications for the five consistency levels offered by Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla) (Specifiche TLA+ di alto livello per i cinque livelli di coerenza offerti da Azure Cosmo DB)
- [Replicated Data Consistency Explained Through Baseball (video)](https://www.youtube.com/watch?v=gluIh8zd26I) (La coerenza dei dati replicati illustrata attraverso il baseball) di Doug Terry
- [Replicated Data Consistency Explained through Baseball (white paper)](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf) (La coerenza dei dati replicati illustrata attraverso il baseball) di Doug Terry
- [Session Guarantees for weakly consistent replicated data](https://dl.acm.org/citation.cfm?id=383631) (Garanzie di sessione per i dati replicati con coerenza debole)
- [Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is Only Part of the Story](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k) (Compromessi sulla coerenza nella progettazione di sistemi di database distribuiti moderni: CAP è solo una parte della storia)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (Decadimento ristretto probabilistico per quorum parziali pratici)
- [Eventually Consistent - Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html) (Coerenza finale - Rivisitato)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui livelli di coerenza in Azure Cosmo DB, vedere gli articoli seguenti:

* [Scegliere il livello di coerenza ottimale per la propria applicazione](consistency-levels-choosing.md)
* [Livelli di coerenza nelle API di Azure Cosmos DB](consistency-levels-across-apis.md)
* [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
* [Configurare il livello di coerenza predefinito](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Sostituire il livello di coerenza predefinito](how-to-manage-consistency.md#override-the-default-consistency-level)

