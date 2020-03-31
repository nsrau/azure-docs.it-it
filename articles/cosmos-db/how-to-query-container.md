---
title: Eseguire query sui contenitori in Azure Cosmos DB
description: Informazioni su come eseguire query sui contenitori in Azure Cosmos DB usando query in-partition e tra partizioni
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: 299980b67caaea85fbfb40cb1a30ee50fa32d0f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131391"
---
# <a name="query-an-azure-cosmos-container"></a>Eseguire una query su un contenitore in Azure Cosmos

Questo articolo illustra come eseguire una query su un contenitore (raccolta, grafo o tabella) in Azure Cosmos DB. In particolare, viene illustrato il funzionamento delle query in partizione e tra partizioni in Azure Cosmos DB.

## <a name="in-partition-query"></a>Query in partizioni

Quando si esegue una query sui dati dai contenitori, se alla query è specificato un filtro chiave di partizione, Azure Cosmos DB ottimizza automaticamente la query. Indirizza la query alle [partizioni fisiche corrispondenti ai](partition-data.md#physical-partitions) valori della chiave di partizione specificati nel filtro.

Si consideri ad esempio la query `DeviceId`seguente con un filtro di uguaglianza su . Se si esegue questa query su `DeviceId`un contenitore partizionato in , la query verrà filtrata in base a una singola partizione fisica.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Come nell'esempio precedente, questa query verrà filtrata anche in base a una singola partizione. L'aggiunta del `Location` filtro aggiuntivo non modifica questa impostazione:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Ecco una query che ha un filtro di intervallo sulla chiave di partizione e non verrà limitato a una singola partizione fisica. Per essere una query all'in-partition, la query deve avere un filtro di uguaglianza che include la chiave di partizione:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Query tra partizioni

La query seguente non dispone di un`DeviceId`filtro sulla chiave di partizione ( ). Pertanto, deve fan-out a tutte le partizioni fisiche in cui viene eseguito sull'indice di ogni partizione:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Ogni partizione fisica ha il proprio indice. Pertanto, quando si esegue una query tra partizioni su un contenitore, si esegue effettivamente una query *per* partizione fisica. Azure Cosmos DB aggregherà automaticamente i risultati tra diverse partizioni fisiche.

Gli indici in partizioni fisiche diverse sono indipendenti l'uno dall'altro. Non esiste alcun indice globale in Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Query tra partizioni in parallelo

Le versioni degli SDK di Azure Cosmos DB 1.9.0 e successive supportano le opzioni di esecuzione di query in parallelo. Le query tra partizioni in parallelo consentono di eseguire query tra partizioni a bassa latenza.

È possibile gestire l'esecuzione di query in parallelo, ottimizzando i parametri seguenti:

- **MaxConcurrency**: Imposta il numero massimo di connessioni di rete simultanee alle partizioni del contenitore. Se si imposta `-1`questa proprietà su , l'SDK gestisce il grado di parallelismo. Se `MaxConcurrency` l'opzione è impostata su `0`, è disponibile una singola connessione di rete alle partizioni del contenitore.

- **MaxBufferedItemCount**: bilancia la latenza delle query rispetto all'utilizzo della memoria sul lato client. Se questa opzione viene omessa o è impostata su -1, il numero di elementi memorizzati nel buffer durante l'esecuzione di query in parallelo viene gestito dall'SDK.

A causa della capacità di Azure Cosmos DB di parallelizzare le query tra partizioni, la latenza delle query viene in genere ridimensionata e il sistema aggiunge [partizioni fisiche.](partition-data.md#physical-partitions) Tuttavia, la carica RU aumenterà in modo significativo con l'aumentare del numero totale di partizioni fisiche.

Quando si esegue una query tra partizioni, si esegue essenzialmente una query separata per ogni singola partizione fisica. Mentre le query tra partizioni utilizzeranno l'indice, se disponibile, non sono ancora efficienti quanto le query all'in-partition.

## <a name="useful-example"></a>Esempio utile

Di seguito è riportata un'analogia per comprendere meglio le query tra partizioni:Here's an analogy to better understand cross-partition queries:

Immaginiamo di essere un autista di consegna che deve consegnare i pacchi a diversi complessi di appartamenti. Ogni complesso di appartamenti ha una lista sul posto che ha tutti i numeri di unità del residente. Possiamo confrontare ogni complesso di apartment con una partizione fisica e ogni elenco con l'indice della partizione fisica.

Possiamo confrontare le query all'in-partition e cross-partition usando questo esempio:

### <a name="in-partition-query"></a>Query in partizioni

Se il conducente di consegna conosce il complesso di appartamenti corretto (partizione fisica), allora possono guidare immediatamente verso l'edificio corretto. Il conducente può controllare l'elenco dei numeri unitari del residente (l'indice) e consegnare rapidamente i pacchi appropriati. In questo caso, il conducente non perde tempo o fatica guidando verso un complesso di appartamenti per verificare e vedere se ci vivono dei destinatari del pacco.

### <a name="cross-partition-query-fan-out"></a>Query tra partizioni (fan-out)

Se il conducente di consegna non conosce il complesso di appartamenti corretto (partizione fisica), dovrà guidare per ogni singolo condominio e controllare l'elenco con tutti i numeri di unità del residente (l'indice). Una volta arrivati in ogni complesso di appartamenti, saranno ancora in grado di utilizzare l'elenco degli indirizzi di ogni residente. Tuttavia, dovranno controllare l'elenco di ogni complesso di appartamenti, se i destinatari del pacchetto vivono lì o no. Ecco come funzionano le query tra partizioni. Mentre possono utilizzare l'indice (non è necessario bussare a ogni singola porta), devono controllare separatamente l'indice per ogni partizione fisica.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Query tra partizioni (con ambito solo a poche partizioni fisiche)Cross-partition query (scoped to only a few physical partitions)

Se il conducente di consegna sa che tutti i destinatari del pacchetto vivono all'interno di alcuni complessi di appartamenti, non avranno bisogno di guidare a ogni singolo. Durante la guida verso alcuni complessi di appartamenti richiederà ancora più lavoro rispetto a visitare un solo edificio, il conducente di consegna consente comunque di risparmiare tempo e fatica significativi. Se una query ha la chiave `IN` di partizione nel filtro con la parola chiave, controllerà solo gli indici della partizione fisica pertinente per i dati.

## <a name="avoiding-cross-partition-queries"></a>Evitare le query tra partizioniAvoiding cross-partition queries

Per la maggior parte dei contenitori, è inevitabile disporre di alcune query tra partizioni. Avere alcune query tra partizioni è ok! Quasi tutte le operazioni di query sono supportate tra le partizioni (chiavi di partizione logiche e partizioni fisiche). Azure Cosmos DB include inoltre molte ottimizzazioni nel motore di query e negli SDK client per parallelizzare l'esecuzione delle query tra partizioni fisiche.

Per la maggior parte degli scenari con un numero elevato di letture, è consigliabile selezionare semplicemente la proprietà più comune nei filtri di query. È inoltre necessario assicurarsi che la chiave di partizione sia conforme ad altre procedure consigliate per la selezione della [chiave di partizione.](partitioning-overview.md#choose-partitionkey)

Evitare query tra partizioni in genere è importante solo con contenitori di grandi dimensioni. Viene addebitato un minimo di circa 2,5 RU ogni volta che si controlla l'indice di una partizione fisica per i risultati, anche se nessun elemento nella partizione fisica corrisponde al filtro della query. Di conseguenza, se si dispone di una sola (o solo poche) partizioni fisiche, le query tra partizioni non utilizzeranno un numero significativamente maggiore di RU rispetto alle query in-partition.

Il numero di partizioni fisiche è legato alla quantità di RU di cui è stato eseguito il provisioning. Ogni partizione fisica consente fino a 10.000 RU di cui è stato eseguito il provisioning e può archiviare fino a 50 GB di dati. Azure Cosmos DB gestirà automaticamente le partizioni fisiche. Il numero di partizioni fisiche nel contenitore dipende dalla velocità effettiva di cui è stato eseguito il provisioning e dall'archiviazione utilizzata.

È consigliabile cercare di evitare query tra partizioni se il carico di lavoro soddisfa i criteri seguenti:You should try to avoid cross-partition queries if your workload meets the criteria below:
- Si prevede di avere più di 30.000 RU di provisioning
- Si prevede di archiviare oltre 100 GB di dati

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni sul partizionamento in Azure Cosmos DB:

- [Partizionamento in Azure Cosmos DB](partitioning-overview.md)
- [Chiavi di partizione sintetiche in Azure Cosmos DB](synthetic-partition-keys.md)
