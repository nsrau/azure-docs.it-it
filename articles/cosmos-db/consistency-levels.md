---
title: Livelli di coerenza in Azure Cosmos DB
description: Cosmos DB ha cinque livelli di coerenza per consentire di compensare scompensi di coerenza, disponibilità e latenza finale.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/06/2020
ms.openlocfilehash: 8f482c4fe6817c75079ceb98e981c846c395ad13
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396026"
---
# <a name="what-are-consistency-levels-in-azure-cosmos-db"></a>Che cosa sono i livelli di coerenza in Azure Cosmos DB?

I database distribuiti che si basano sulla replica per garantire disponibilità elevata, bassa latenza o entrambe, applicano il compromesso fondamentale tra coerenza di lettura e disponibilità, latenza e velocità effettiva. La maggior parte dei database distribuiti disponibili in commercio chiede agli sviluppatori di scegliere tra i due modelli di coerenza *estremi: coerenza assoluta e coerenza* *finale* . Il della linearità del modello di coerenza forte è lo standard Gold di programmabilità dei dati. Ma aggiunge un prezzo di latenza di scrittura superiore (in stato stabile) e una disponibilità ridotta (durante gli errori). D'altra parte, la coerenza finale offre una maggiore disponibilità e prestazioni migliori, ma rende difficile programmare le applicazioni.

Azure Cosmos DB affronta la coerenza dei dati offrendo uno spettro di scelte che si collocano tra i due estremi. Gli sviluppatori possono usare queste opzioni per eseguire scelte precise e compromessi granulari rispetto alla disponibilità e alle prestazioni elevate.

Con Azure Cosmos DB, gli sviluppatori possono scegliere tra cinque livelli di coerenza ben definiti nello spettro di coerenza. Questi livelli includono forte, *bounded staleness*decadimento *ristretto*, *sessione*, *prefisso coerente*e coerenza *finale* . I livelli sono ben definiti e intuitivi e possono essere usati per scenari reali specifici. Ogni livello offre [compromessi in merito a disponibilità e prestazioni](consistency-levels-tradeoffs.md) e supportato da contratti di contratto. Nell'immagine seguente vengono illustrati i diversi livelli di coerenza come uno spettro.

:::image type="content" source="./media/consistency-levels/five-consistency-levels.png" alt-text="La coerenza come spettro" border="false" :::

I livelli di coerenza sono indipendenti dall'area e sono garantiti per tutte le operazioni, indipendentemente dall'area da cui vengono gestite le operazioni di lettura e scrittura, dal numero di aree associate all'account Azure Cosmos o dal fatto che l'account sia configurato con una o più aree di scrittura.

## <a name="scope-of-the-read-consistency"></a>Ambito della coerenza di lettura

La coerenza di lettura si applica a una singola operazione di lettura con ambito all'interno di una partizione logica. L'operazione di lettura può essere generata da un client remoto o da una stored procedure.

## <a name="configure-the-default-consistency-level"></a>Configurare il livello di coerenza predefinito

È possibile configurare il livello di coerenza predefinito nell'account Azure Cosmos in qualsiasi momento. Il livello di coerenza predefinito configurato per l'account si applica a tutti i database e contenitori di Azure Cosmos con tale account. Per impostazione predefinita, tutte le operazioni di lettura e le query eseguite su un contenitore o un database usano il livello di coerenza specificato. Per altre informazioni, vedere come [configurare il livello di coerenza predefinito](how-to-manage-consistency.md#configure-the-default-consistency-level). È anche possibile eseguire l'override del livello di coerenza predefinito per una richiesta specifica. per ulteriori informazioni, vedere come [sostituire l'articolo del livello di coerenza predefinito](how-to-manage-consistency.md?#override-the-default-consistency-level) .

## <a name="guarantees-associated-with-consistency-levels"></a>Garanzie associate ai livelli di coerenza

I contratti di servizio completi offerti da Azure Cosmos DB garantiscono che il 100% delle richieste di lettura soddisfano la garanzia di coerenza per qualsiasi livello di coerenza scelto. Una richiesta soddisfa il contratto di servizio per la coerenza se sono soddisfatte tutte le garanzie associate al livello di coerenza. Le definizioni esatte dei cinque livelli di coerenza in Azure Cosmos DB usando il linguaggio di specifica di TLA + sono disponibili nel repository [Azure-Cosmos-TLA](https://github.com/Azure/azure-cosmos-tla) github.

La semantica dei cinque livelli di coerenza è descritta qui:

- **Forte**: la coerenza assoluta offre una garanzia della linearità. Della linearità si riferisce alle richieste di servizio simultaneamente. È garantito che le letture restituiscano sempre la versione di un elemento di cui sia stato eseguito il commit più di recente. Un client non visualizza mai una scrittura parziale o di cui non sia stato eseguito il commit. Gli utenti possono sempre essere certi di leggere la scrittura più recente sottoposta a commit.

  Il grafico seguente illustra la coerenza assoluta con le note musicali. Dopo che i dati sono stati scritti nell'area "Stati Uniti occidentali 2", quando si leggono i dati da altre aree, si ottiene il valore più recente:

  :::image type="content" source="media/consistency-levels/strong-consistency.gif" alt-text="video":::

- Decadimento **delimitato**: le letture sono garantite per rispettare la garanzia di prefisso coerente. È possibile che le letture ritardino alla base delle Scritture al massimo da *"K"* versioni (ovvero "aggiornamenti") di un elemento o dall'intervallo di tempo *"T"* , a seconda del valore raggiunto per primo. In altre parole, quando si sceglie il decadimento ristretto, il "obsolescenza" può essere configurato in due modi:

- Numero di versioni (*K*) dell'elemento
- Intervallo di tempo (*T*) in base al quale le letture potrebbero ritardare le Scritture

L'obsolescenza associata offre un ordine globale totale esterno alla "finestra di obsolescenza". Quando un client esegue le operazioni di lettura all'interno di un'area che accetta Scritture, le garanzie fornite dalla coerenza con decadimento ristretto sono identiche a quelle garantite dalla coerenza assoluta.

All'interno della finestra di obsolescenza, il decadimento associato fornisce le garanzie di coerenza seguenti:

- Coerenza per i client nella stessa area per un account master singolo = Strong
- Coerenza per i client in aree diverse per un account master singolo = prefisso coerente
- Coerenza dei client che scrivono in una singola area per un account multimaster = prefisso coerente
- Coerenza dei client che scrivono in aree diverse per un account multimaster = eventuale

  Il decadimento ristretto viene spesso scelto da applicazioni distribuite a livello globale che prevedono latenze di scrittura ridotte, ma che richiedono la garanzia totale degli ordini globali Il decadimento ristretto è ideale per le applicazioni che includono la collaborazione e la condivisione di gruppi, il titolo di borsa, la pubblicazione-sottoscrizione/Accodamento e così via. Il grafico seguente illustra la coerenza con decadimento ristretto con le note musicali. Dopo che i dati sono stati scritti nell'area "Stati Uniti occidentali 2", le aree "Stati Uniti orientali 2" e "Australia orientale" leggono il valore scritto in base al tempo massimo di ritardo configurato o al numero massimo di operazioni:

  :::image type="content" source="media/consistency-levels/bounded-staleness-consistency.gif" alt-text="video":::

- **Sessione**: all'interno di una singola sessione client le letture sono garantite per rispettare il prefisso coerente, le letture monotone, le Scritture monotone, la lettura delle Scritture e le garanzie Write-follows. Si presuppone una singola sessione "writer" o si condivide il token di sessione per più writer.

I client al di fuori della sessione che esegue le Scritture vedranno le garanzie seguenti:

- Coerenza per i client nella stessa area per un account master singolo = prefisso coerente
- Coerenza per i client in aree diverse per un account master singolo = prefisso coerente
- Coerenza dei client che scrivono in una singola area per un account multimaster = prefisso coerente
- Coerenza per la scrittura dei client in più aree per un account multi-master = eventuale

  La coerenza di sessione è il livello di coerenza più diffuso per l'area singola e per le applicazioni distribuite a livello globale. Fornisce latenze di scrittura, disponibilità e velocità effettiva di lettura paragonabili a quelle della coerenza finale, ma fornisce anche le garanzie di coerenza in base alle esigenze delle applicazioni scritte per funzionare nel contesto di un utente. Il grafico seguente illustra la coerenza della sessione con le note musicali. "West US 2 Writer" e "West US 2 Reader" usano la stessa sessione (Session A) in modo che entrambi leggano gli stessi dati nello stesso momento. Mentre l'area "Australia orientale" utilizza "sessione B", riceve i dati in un secondo momento, ma nello stesso ordine delle Scritture.

  :::image type="content" source="media/consistency-levels/session-consistency.gif" alt-text="video":::

- **Prefisso coerente**: gli aggiornamenti restituiti contengono un prefisso di tutti gli aggiornamenti, senza gap. Il livello di coerenza del prefisso coerente garantisce che le letture non visualizzino mai le Scritture non ordinate.

Se le Scritture sono state eseguite nell'ordine `A, B, C` , un client può vedere `A` , `A,B` o `A,B,C` , ma non le permutazioni non ordinate come `A,C` o `B,A,C` . Il prefisso coerente fornisce latenze di scrittura, disponibilità e velocità effettiva di lettura paragonabili a quelle della coerenza finale, ma fornisce anche le garanzie di ordine che soddisfano le esigenze degli scenari in cui l'ordine è importante. 

Di seguito sono riportate le garanzie di coerenza per il prefisso coerente:

- Coerenza per i client nella stessa area per un account master singolo = prefisso coerente
- Coerenza per i client in aree diverse per un account master singolo = prefisso coerente
- Coerenza dei client che scrivono in una singola area per un account multimaster = prefisso coerente
- Coerenza per la scrittura dei client in più aree per un account multi-master = eventuale

Il grafico seguente illustra la coerenza del prefisso di coerenza con le note musicali. In tutte le aree, le letture non visualizzano mai le Scritture non in ordine:

  :::image type="content" source="media/consistency-levels/consistent-prefix.gif" alt-text="video":::

- **Eventuale**: non esiste alcuna garanzia di ordinamento per le letture. In assenza di ulteriori operazioni di scrittura, le repliche alla fine convergeranno.  
La coerenza finale è la forma di coerenza più debole, perché un client può leggere i valori più vecchi di quelli precedentemente letti. La coerenza finale è ideale in cui l'applicazione non richiede alcuna garanzia di ordinamento. Gli esempi includono il numero di Retweet, mi piace o commenti non thread. Il grafico seguente illustra la coerenza finale con le note musicali.

  :::image type="content" source="media/consistency-levels/eventual-consistency.gif" alt-text="video":::

## <a name="additional-reading"></a>Altre letture

Per altre informazioni sui concetti di coerenza, vedere gli articoli seguenti:

- [High-level TLA+ specifications for the five consistency levels offered by Azure Cosmos DB](https://github.com/Azure/azure-cosmos-tla) (Specifiche TLA+ di alto livello per i cinque livelli di coerenza offerti da Azure Cosmo DB)
- [Coerenza dei dati replicati illustrata attraverso il baseball (video) di Doug Terry](https://www.youtube.com/watch?v=gluIh8zd26I)
- [Coerenza dei dati replicati illustrata attraverso il baseball (white paper) di Doug Terry](https://www.microsoft.com/research/publication/replicated-data-consistency-explained-through-baseball/)
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
