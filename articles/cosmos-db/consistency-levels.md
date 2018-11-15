---
title: Livelli di coerenza in Azure Cosmos DB | Microsoft Docs
description: Cosmos DB ha cinque livelli di coerenza per consentire di compensare scompensi di coerenza, disponibilità e latenza finale.
keywords: coerenza finale, azure cosmos db, azure, Microsoft azure
services: cosmos-db
author: aliuy
manager: kfile
ms.service: cosmos-db
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2018
ms.author: andrl
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2611c25764503551c4da918d06bcaabe315cbf7c
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/02/2018
ms.locfileid: "50963082"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Livelli di coerenza in Azure Cosmos DB

I database distribuiti che si basano sulla replica per gestire la disponibilità elevata, la bassa latenza o entrambe, applicano il compromesso fondamentale tra coerenza di lettura e disponibilità, latenza e velocità effettiva. La maggior parte dei database distribuiti disponibili in commercio richiede che gli sviluppatori scelgano tra due modelli di coerenza estremi: la coerenza assoluta e la coerenza finale. Anche se la  [linearizzabilità](http://cs.brown.edu/~mph/HerlihyW90/p463-herlihy.pdf) o il modello di coerenza assoluta è lo standard più alto di programmabilità dei dati, presenta un notevole svantaggio in termini di maggiore latenza (in stato stazionario) e disponibilità ridotta (in caso di errori). La coerenza finale, d'altra parte, offre maggiore disponibilità e migliori prestazioni, ma è difficile da programmare.

Cosmos DB affronta la coerenza dei dati offrendo uno spettro di scelte che si collocano tra i due estremi. La coerenza assoluta e la coerenza finale sono le due estremità dello spettro, nel mezzo del quale sono disponibili molte altre opzioni. Queste opzioni di coerenza consentono agli sviluppatori di effettuare scelte precise e compromessi di granularità in relazione alla disponibilità o alle prestazioni. Cosmos DB offre agli sviluppatori la possibilità di scegliere tra cinque modelli di coerenza ben definiti nello spettro della coerenza (dal più sicuro al più debole): **assoluto**, **decadimento ristretto**, **sessione**, **coerenza del prefisso** e **finale**. Ognuno di questi modelli di coerenza è ben definito, intuitivo e può essere usato per scenari reali specifici. Ognuno dei cinque modelli prevede [compromessi in termini di disponibilità e prestazioni](consistency-levels-tradeoffs.md) ed è supportato da un contratto di servizio completo.

![La coerenza come spettro](./media/consistency-levels/five-consistency-levels.png)

I livelli di coerenza sono indipendenti dall'area. Il livello di coerenza dell'account Cosmos DB è garantito per tutte le operazioni di lettura a prescindere dalle proprietà seguenti:

- L'area da cui vengono gestite le letture e le scritture
- Il numero di aree associate all'account Cosmos DB
- La presenza di una o più aree di scrittura nella configurazione dell'account

## <a name="scope-of-the-read-consistency"></a>L'ambito della coerenza di lettura

La coerenza di lettura si applica a una singola operazione di lettura il cui ambito si trova nell'intervallo delle chiavi di partizione, ovvero una partizione logica. L'operazione di lettura può essere generata da un client remoto o da una stored procedure.

## <a name="configuring-the-default-consistency-level"></a>Configurazione del livello di coerenza predefinito

È possibile configurare il **livello di coerenza predefinito** nell'account Cosmos DB in qualsiasi momento. Il livello di coerenza predefinito configurato per l'account si applica a tutti i database Cosmos (e i contenitori) in tale account. Per impostazione predefinita, tutte le operazioni di lettura e le query eseguite su un contenitore o un database usano il livello di coerenza specificato. Per altre informazioni, vedere l'articolo su come [configurare il livello di coerenza predefinito](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garanzie associate ai livelli di coerenza

I contratti di servizio completi offerti da Azure Cosmos DB garantiscono che il 100% delle richieste di lettura soddisfino la garanzia di coerenza per qualsiasi livello di coerenza scelto. Si considera che una richiesta abbia soddisfatto il contratto di servizio per la coerenza se sono state soddisfatte tutte le garanzie associate al livello di coerenza. Le definizioni precise dei cinque livelli di coerenza in Cosmos DB che usano il [linguaggio di specifica TLA+](http://lamport.azurewebsites.net/tla/tla.html) sono disponibili nel repository GitHub [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla). La semantica dei cinque livelli di coerenza è descritta di seguito:

- **Livello di coerenza = "assoluto"**: questo livello offre una garanzia di [linearizzabilità](https://aphyr.com/posts/313-strong-consistency-models), assicurando che le operazioni di lettura restituiscano la versione di un elemento più recente sottoposta a commit. Un client non visualizzerà mai una scrittura parziale o non sottoposta a commit. Gli utenti possono sempre essere certi di leggere la scrittura più recente sottoposta a commit.

- **Livello di coerenza = "decadimento ristretto"**: questo livello assicura che le operazioni di lettura rispettino la garanzia della coerenza del prefisso. Le operazioni di lettura possono essere in ritardo rispetto alle operazioni di scrittura per al massimo K versioni (ovvero gli aggiornamenti) di un elemento o per un intervallo temporale "t". Quando si sceglie il decadimento ristretto, il decadimento può essere configurato in due modi: 

  * Numero di versioni (K) dell'elemento oppure
  * Intervallo di tempo (t) con cui le operazioni di lettura possono accumulare ritardo rispetto alle operazioni di scrittura. 

  Il decadimento ristretto offre un ordine globale totale tranne all'interno della "finestra di decadimento". La garanzia di lettura monotona esiste in un'area sia all'interno che all'esterno della "finestra di decadimento". La coerenza assoluta è basata sulla stessa semantica della coerenza con decadimento ristretto, fatta eccezione per la "finestra di decadimento", che è pari a zero. Il decadimento ristretto è anche definito **linearizzabilità posticipata**. Quando un client esegue operazioni di lettura all'interno di un'area che accetta operazioni di scrittura, le garanzie offerte dal livello di coerenza con decadimento ristretto sono identiche a quelle della coerenza assoluta.

- **Livello di coerenza = "sessione"**: questo livello assicura che le operazioni di lettura rispettino le garanzie della coerenza del prefisso, delle letture monotoniche, delle scritture monotoniche, della lettura delle proprie scritture, delle letture che seguono la scrittura. L'ambito di una coerenza di sessione è una sessione client.

- **Livello di coerenza = "coerenza del prefisso"**: gli aggiornamenti restituiti contengono un prefisso di tutti gli aggiornamenti, senza interruzioni. La coerenza del prefisso garantisce che le operazioni di lettura non vedano mai il fuori sequenza delle operazioni di scrittura.

- **Livello di coerenza = "finale"**: non vi è garanzia di ordinamento per le operazioni di lettura. In assenza di altre scritture, le repliche finiscono per convergere.

## <a name="consistency-levels-explained-through-baseball"></a>I livelli di coerenza spiegati attraverso il baseball

Si prenda come esempio una partita di baseball e si immagini una sequenza di operazioni di scrittura che rappresenta il punteggio della partita con i punti inning per inning, come illustrato nel white paper [Replicated Data Consistency Through Baseball](https://www.microsoft.com/en-us/research/wp-content/uploads/2011/10/ConsistencyAndBaseballReport.pdf) (La coerenza dei dati replicati illustrata attraverso il baseball). Questa ipotetica partita di baseball si trova al momento nel mezzo del settimo inning e la squadra locale sta vincendo 2-5.

| | **1** | **2** | **3** | **4** | **5** | **6** | **7** | **8** | **9** | **Punti** |
| - | - | - | - | - | - | - | - | - | - | - |
| **Ospiti** | 0 | 0 | 1 | 0 | 1 | 0 | 0 |  |  | 2 |
| **Locali** | 1 | 0 | 1 | 1 | 0 | 2 |  |  |  | 5 |

Un contenitore Cosmos DB include i punti totali della squadra ospite e della squadra locale. Nel corso della partita, garanzie di lettura diverse possono determinare la lettura di punteggi diversi da parte dei client. La tabella seguente elenca il set completo di punteggi che potrebbero essere restituiti leggendo i punteggi delle due squadre con ognuna delle cinque garanzie di coerenza. Il punteggio della squadra ospite viene elencato per primo e i diversi valori restituiti possibili sono separati da virgole.

| **Livello di coerenza** | **Punteggio** |
| - | - |
| **Assoluto** | 2-5 |
| **Decadimento ristretto** | punteggio precedente al massimo di un inning" 2-3, 2-4, 2-5 |
| **Sessione** | <ul><li>per il writer" 2-5</li><li> per chiunque, eccetto il writer: 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5</li><li>dopo la lettura 1-3: 1-3, 1-4, 1-5, 2-3, 2-4, 2-5</li> |
| **Coerenza del prefisso** | 0-0, 0-1, 1-1, 1-2, 1-3, 2-3, 2-4, 2-5 |
| **Finale** | 0-0, 0-1, 0-2, 0-3, 0-4, 0-5, 1-0, 1-1, 1-2, 1-3, 1-4, 1-5, 2-0, 2-1, 2-2, 2-3, 2-4, 2-5 |

## <a name="additional-reading"></a>Informazioni aggiuntive

Per altre informazioni sui concetti di coerenza, vedere gli articoli seguenti:

- [High-level TLA+ specifications for the five consistency levels offered by Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla) (Specifiche TLA+ di alto livello per i cinque livelli di coerenza offerti da Azure Cosmo DB)
- [Video: Replicated Data Consistency Explained through Baseball di Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I) (La coerenza dei dati replicati illustrata attraverso il baseball)
- [White paper: Replicated Data Consistency Explained through Baseball di Doug Terry](https://www.microsoft.com/en-us/research/publication/replicated-data-consistency-explained-through-baseball/?from=http%3A%2F%2Fresearch.microsoft.com%2Fpubs%2F157411%2Fconsistencyandbaseballreport.pdf) (La coerenza dei dati replicati illustrata attraverso il baseball)
- [Session Guarantees for Weakly Consistent Replicated Data](https://dl.acm.org/citation.cfm?id=383631) (Garanzie di sessione per i dati replicati con coerenza debole)
- [Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is Only Part of the Story](https://www.computer.org/web/csdl/index/-/csdl/mags/co/2012/02/mco2012020037-abs.html) (Compromessi sulla coerenza nella progettazione di sistemi di database distribuiti moderni: CAP è solo una parte della storia)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](http://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (Decadimento ristretto probabilistico per quorum parziali pratici)
- [Eventually Consistent - Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html) (Coerenza finale - Rivisitato)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui livelli di coerenza in Cosmo DB, vedere gli articoli seguenti:

* [Choosing the right consistency level for your application](consistency-levels-choosing.md) (Scelta del livello di coerenza ottimale per l'applicazione)
* [Consistency levels across Cosmos DB APIs](consistency-levels-across-apis.md) (Livelli di coerenza nelle API Cosmo DB)
* [Availability and performance tradeoffs for various consistency levels](consistency-levels-tradeoffs.md) (Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza)
* [Come configurare il livello di coerenza predefinito](how-to-manage-consistency.md#configure-the-default-consistency-level)
* [Come ignorare il livello di coerenza predefinito](how-to-manage-consistency.md#override-the-default-consistency-level)

