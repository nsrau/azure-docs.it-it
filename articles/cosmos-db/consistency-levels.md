---
title: Livelli di coerenza in Azure Cosmos DB
description: Cosmos DB ha cinque livelli di coerenza per consentire di compensare scompensi di coerenza, disponibilità e latenza finale.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: e5966f142ece32f148c56edb5b0ef5dfd88603aa
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/14/2020
ms.locfileid: "81380069"
---
# <a name="consistency-levels-in-azure-cosmos-db"></a>Livelli di coerenza in Azure Cosmos DB

I database distribuiti che si basano sulla replica per garantire disponibilità elevata, bassa latenza o entrambe, applicano il compromesso fondamentale tra coerenza di lettura e disponibilità, latenza e velocità effettiva. La maggior parte dei database distribuiti disponibili in commercio chiede agli sviluppatori di scegliere tra i due modelli di coerenza estrema: *forte* coerenza ed *eventuale* coerenza. La linearità del modello di forte coerenza è il gold standard della programmabilità dei dati. Ma aggiunge un prezzo di maggiore latenza di scrittura (in stato stabile) e disponibilità ridotta (durante gli errori). D'altra parte, la coerenza finale offre una maggiore disponibilità e prestazioni migliori, ma rende difficile programmare le applicazioni.

Azure Cosmos DB affronta la coerenza dei dati offrendo uno spettro di scelte che si collocano tra i due estremi. Gli sviluppatori possono usare queste opzioni per effettuare scelte precise e compromessi granulari per quanto riguarda la disponibilità elevata e le prestazioni.

Con Azure Cosmos DB, gli sviluppatori possono scegliere tra cinque livelli di coerenza ben definiti nello spettro di coerenza. Questi livelli includono una *stantiezza* *forte,* delimitata, *una sessione,* *un prefisso coerente*e *un'eventuale* coerenza. I livelli sono ben definiti e intuitivi e possono essere utilizzati per scenari reali specifici. Ogni livello offre compromessi di [disponibilità e prestazioni](consistency-levels-tradeoffs.md) e sono supportati da contratti di servizio. L'immagine seguente mostra i diversi livelli di coerenza come spettro.

![La coerenza come spettro](./media/consistency-levels/five-consistency-levels.png)

I livelli di coerenza sono indipendenti dall'area e sono garantiti per tutte le operazioni indipendentemente dall'area da cui vengono servite le operazioni di lettura e scrittura, il numero di aree associate all'account Cosmos di Azure o se l'account è configurato con una o più aree di scrittura.

## <a name="scope-of-the-read-consistency"></a>Ambito della coerenza di lettura

La coerenza di lettura si applica a una singola operazione di lettura con ambito all'interno di una partizione logica. L'operazione di lettura può essere generata da un client remoto o da una stored procedure.

## <a name="configure-the-default-consistency-level"></a>Configurare il livello di coerenza predefinito

È possibile configurare il livello di coerenza predefinito nell'account Azure Cosmos in qualsiasi momento. Il livello di coerenza predefinito configurato nell'account si applica a tutti i database e i contenitori di Azure Cosmos con tale account. Per impostazione predefinita, tutte le operazioni di lettura e le query eseguite su un contenitore o un database usano il livello di coerenza specificato. Per altre informazioni, vedere come [configurare il livello di coerenza predefinito](how-to-manage-consistency.md#configure-the-default-consistency-level).

## <a name="guarantees-associated-with-consistency-levels"></a>Garanzie associate ai livelli di coerenza

I contratti di servizio completi offerti da Azure Cosmos DB garantiscono che il 100% delle richieste di lettura soddisfano la garanzia di coerenza per qualsiasi livello di coerenza scelto. Una richiesta soddisfa il contratto di servizio per la coerenza se sono soddisfatte tutte le garanzie associate al livello di coerenza. Le definizioni precise dei cinque livelli di coerenza in Azure Cosmos DB usando il linguaggio di specifica TLA sono fornite nel repository [azure-cosmos-tla](https://github.com/Azure/azure-cosmos-tla) GitHub.

La semantica dei cinque livelli di coerenza è descritta qui:

- **Forte**: Forte consistenza offre una garanzia di linearità. Linearizzabilità si riferisce alla gestione simultanea delle richieste. È garantito che le letture restituiscano sempre la versione di un elemento di cui sia stato eseguito il commit più di recente. Un client non visualizza mai una scrittura parziale o di cui non sia stato eseguito il commit. Gli utenti possono sempre essere certi di leggere la scrittura più recente sottoposta a commit.

  Il grafico seguente illustra la forte coerenza con le note musicali. Dopo che i dati vengono scritti nell'area "Stati Uniti occidentali 2", quando si leggono i dati da altre aree, si ottiene il valore più recente:

  ![video](media/consistency-levels/strong-consistency.gif)

- **Bounded staleness**: Le letture sono garantite per rispettare la garanzia coerente-prefisso. Le letture potrebbero essere in ritardo rispetto alle scritture al massimo delle versioni *"K"* (ovvero "aggiornamenti") di un elemento o per l'intervallo di tempo *"T".* In altre parole, quando si sceglie lo stantio delimitato, la "stantità" può essere configurata in due modi:

- Il numero di versioni (*K*) dell'elemento
- L'intervallo di tempo (*T*) in base al quale le letture potrebbero rimanere in ritardo rispetto alle scritture

La stantiezza delimitata offre un ordine globale totale al di fuori della "finestra di stantio". Quando un client esegue operazioni di lettura all'interno di un'area che accetta scritture, le garanzie fornite dalla coerenza di stantio delimitata sono identiche a tali garanzie in base alla coerenza forte.

All'interno della finestra di stantio, Bounded Staleness fornisce le seguenti garanzie di coerenza:

- Coerenza per i client nella stessa area per un singolo account master:
- Coerenza per i client in aree diverse per un singolo account master : prefisso coerente
- Coerenza per i client che scrivono in una singola area per un account multimaster: prefisso coerente
- Coerenza per i client che scrivono in aree diverse per un account multi-master - Eventuale

  La stantiezza delimitata viene spesso scelta da applicazioni distribuite a livello globale che prevedono latenze di scrittura basse ma richiedono una garanzia di ordine globale totale. Lo stantio delimitato è ottimo per le applicazioni con la collaborazione e la condivisione di gruppo, stock ticker, publish-subscribe/queueing ecc. Il grafico seguente illustra la coerenza dello stantio delimitato con le note musicali. Dopo che i dati vengono scritti nella regione "Stati Uniti occidentali 2", le regioni "East US 2" e "Australia orientale" leggono il valore scritto in base al tempo di ritardo massimo configurato o alle operazioni massime:

  ![video](media/consistency-levels/bounded-staleness-consistency.gif)

- **Sessione**: All'interno di una singola sessione client le letture della sessione sono garantite per rispettare il prefisso coerente, le letture monotoniche, le scritture monotoniche, le garanzie di lettura-scrittura e write-follows-reads. Si presuppone che una singola sessione "writer" o la condivisione del token di sessione per più writer.

I client al di fuori della sessione che eseguono le scritture vedranno le seguenti garanzie:

- Coerenza per i client nella stessa area per un singolo account master: prefisso coerente
- Coerenza per i client in aree diverse per un singolo account master : prefisso coerente
- Coerenza per i client che scrivono in una singola area per un account multimaster: prefisso coerente
- Coerenza per i client che scrivono in più aree geografiche per un account multimaster - Eventuale

  La coerenza della sessione è il livello di coerenza ampiamente utilizzato sia per le applicazioni in un'area singola che per le applicazioni distribuite a livello globale. Fornisce latenze di scrittura, disponibilità e velocità effettiva di lettura paragonabili a quelle di eventuale coerenza, ma fornisce anche le garanzie di coerenza che soddisfano le esigenze delle applicazioni scritte per operare nel contesto di un utente. Il grafico seguente illustra la coerenza della sessione con le note musicali. Il "Autore di West US 2" e il "lettore West US 2" utilizzano la stessa sessione (Sessione A) in modo che entrambi leggono gli stessi dati allo stesso tempo. Mentre l'area "Australia est" utilizza "Sessione B", quindi riceve i dati in un secondo momento, ma nello stesso ordine delle scritture.

  ![video](media/consistency-levels/session-consistency.gif)

- **Prefisso coerente**: Gli aggiornamenti restituiti contengono alcuni prefissi di tutti gli aggiornamenti, senza spazi vuoti. Il livello di coerenza del prefisso coerente garantisce che la lettura non visualizzi mai scritture non in ordine.

Se queste sono state eseguite nell'ordine `A, B, C`, il client vede `A`, `A,B` o `A,B,C`, ma mai il fuori sequenza ad esempio `A,C` o `B,A,C`. Prefisso coerente fornisce latenze di scrittura, disponibilità e velocità effettiva di lettura paragonabili a quelle di coerenza finale, ma fornisce anche le garanzie di ordine che si adattano alle esigenze degli scenari in cui l'ordine è importante. 

Di seguito sono riportate le garanzie di coerenza per il prefisso coerente:

- Coerenza per i client nella stessa area per un singolo account master: prefisso coerente
- Coerenza per i client in aree diverse per un singolo account master : prefisso coerente
- Coerenza per i client che scrivono in una singola area per un account multimaster: prefisso coerente
- Coerenza per i client che scrivono in più aree geografiche per un account multimaster - Eventuale

Il grafico seguente illustra la coerenza del prefisso di coerenza con le note musicali. In tutte le regioni, le letture non vedono mai le scritture in ordine:

  ![video](media/consistency-levels/consistent-prefix.gif)

- **Eventuale**: Non c'è alcuna garanzia di ordinazione per le letture. In assenza di altre scritture, le repliche finiscono per convergere.  
La coerenza finale è la forma più debole di coerenza perché un client può leggere i valori che sono più vecchi di quelli che aveva letto prima. La coerenza finale è ideale quando l'applicazione non richiede alcuna garanzia di ordinazione. Gli esempi includono il conteggio di Retweet, Mi piace o commenti non a thread. Il grafico seguente illustra l'eventuale coerenza con le note musicali.

  ![video](media/consistency-levels/eventual-consistency.gif)

## <a name="additional-reading"></a>Informazioni aggiuntive

Per altre informazioni sui concetti di coerenza, vedere gli articoli seguenti:

- [High-level TLA+ specifications for the five consistency levels offered by Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla) (Specifiche TLA+ di alto livello per i cinque livelli di coerenza offerti da Azure Cosmo DB)
- [Coerenza dei dati replicati spiegata attraverso il baseball (video) di Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Coerenza dei dati replicati spiegata attraverso il baseball (whitepaper) di Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
- [Session Guarantees for weakly consistent replicated data](https://dl.acm.org/citation.cfm?id=383631) (Garanzie di sessione per i dati replicati con coerenza debole)
- [Consistency Tradeoffs in Modern Distributed Database Systems Design: CAP is Only Part of the Story](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k) (Compromessi sulla coerenza nella progettazione di sistemi di database distribuiti moderni: CAP è solo una parte della storia)
- [Probabilistic Bounded Staleness (PBS) for Practical Partial Quorums](https://vldb.org/pvldb/vol5/p776_peterbailis_vldb2012.pdf) (Decadimento ristretto probabilistico per quorum parziali pratici)
- [Eventually Consistent - Revisited](https://www.allthingsdistributed.com/2008/12/eventually_consistent.html) (Coerenza finale - Rivisitato)

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sui livelli di coerenza in Azure Cosmo DB, vedere gli articoli seguenti:

- [Scegliere il livello di coerenza ottimale per la propria applicazione](consistency-levels-choosing.md)
- [Livelli di coerenza nelle API di Azure Cosmos DB](consistency-levels-across-apis.md)
- [Compromessi nella disponibilità e nelle prestazioni per vari livelli di coerenza](consistency-levels-tradeoffs.md)
- [Configurare il livello di coerenza predefinito](how-to-manage-consistency.md#configure-the-default-consistency-level)
- [Sostituire il livello di coerenza predefinito](how-to-manage-consistency.md#override-the-default-consistency-level)
