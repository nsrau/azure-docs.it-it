---
title: Eseguire query sui contenitori in Azure Cosmos DB
description: Informazioni su come eseguire query sui contenitori in Azure Cosmos DB usando query in-Partition e tra partizioni
author: markjbrown
ms.service: cosmos-db
ms.topic: how-to
ms.date: 3/18/2019
ms.author: mjbrown
ms.openlocfilehash: e948031d3d1d03890bfcfccd65424a15e6e314cd
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/20/2020
ms.locfileid: "92276109"
---
# <a name="query-an-azure-cosmos-container"></a>Eseguire una query su un contenitore in Azure Cosmos

Questo articolo illustra come eseguire una query su un contenitore (raccolta, grafo o tabella) in Azure Cosmos DB. In particolare, vengono illustrate le modalità di lavoro delle query in partizione e tra partizioni in Azure Cosmos DB.

## <a name="in-partition-query"></a>Query in partizioni

Quando si esegue una query sui dati dei contenitori, se per la query è stato specificato un filtro della chiave di partizione, Azure Cosmos DB ottimizza automaticamente la query. Instrada la query alle [partizioni fisiche](partitioning-overview.md#physical-partitions) corrispondenti ai valori della chiave di partizione specificati nel filtro.

Si consideri, ad esempio, la query seguente con un filtro di uguaglianza su `DeviceId` . Se si esegue questa query su un contenitore partizionato in `DeviceId` , questa query verrà filtrata in un'unica partizione fisica.

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001'
```

Come per l'esempio precedente, la query verrà filtrata anche in una singola partizione. L'aggiunta del filtro aggiuntivo in `Location` non cambia:

```sql
    SELECT * FROM c WHERE c.DeviceId = 'XMS-0001' AND c.Location = 'Seattle'
```

Di seguito è illustrata una query con un filtro di intervallo sulla chiave di partizione e non verrà definito come ambito di una singola partizione fisica. Per essere una query nel partizionamento, è necessario che la query disponga di un filtro di uguaglianza che includa la chiave di partizione:

```sql
    SELECT * FROM c WHERE c.DeviceId > 'XMS-0001'
```

## <a name="cross-partition-query"></a>Query tra partizioni

La query seguente non dispone di un filtro per la chiave di partizione ( `DeviceId` ). Pertanto, è necessario che la ventola venga eseguita su tutte le partizioni fisiche in cui viene eseguita sull'indice di ogni partizione:

```sql
    SELECT * FROM c WHERE c.Location = 'Seattle`
```

Ogni partizione fisica dispone di un proprio indice. Pertanto, quando si esegue una query tra partizioni in un contenitore, si esegue effettivamente una query *per* partizione fisica. Azure Cosmos DB aggrega automaticamente i risultati in partizioni fisiche diverse.

Gli indici di partizioni fisiche diverse sono indipendenti l'uno dall'altro. Nessun indice globale in Azure Cosmos DB.

## <a name="parallel-cross-partition-query"></a>Query tra partizioni in parallelo

Le versioni degli SDK di Azure Cosmos DB 1.9.0 e successive supportano le opzioni di esecuzione di query in parallelo. Le query tra partizioni in parallelo consentono di eseguire query tra partizioni a bassa latenza.

È possibile gestire l'esecuzione di query in parallelo, ottimizzando i parametri seguenti:

- **MaxConcurrency**: imposta il numero massimo di connessioni di rete simultanee alle partizioni del contenitore. Se si imposta questa proprietà su `-1` , l'SDK gestisce il grado di parallelismo. Se  `MaxConcurrency` impostato su `0` , esiste una singola connessione di rete alle partizioni del contenitore.

- **MaxBufferedItemCount**: bilancia la latenza delle query rispetto all'utilizzo della memoria sul lato client. Se questa opzione viene omessa o è impostata su -1, il numero di elementi memorizzati nel buffer durante l'esecuzione di query in parallelo viene gestito dall'SDK.

Grazie alla capacità Azure Cosmos DB di parallelizzare le query tra partizioni, la latenza delle query viene in genere ridimensionata in modo da consentire al sistema di aggiungere [partizioni fisiche](partitioning-overview.md#physical-partitions). Tuttavia, l'addebito delle UR aumenterà in modo significativo man mano che aumenta il numero totale di partizioni fisiche.

Quando si esegue una query tra partizioni, si esegue essenzialmente una query separata per ogni singola partizione fisica. Sebbene le query tra partizioni utilizzino l'indice, se disponibile, non sono ancora molto efficienti come le query nel partizionamento.

## <a name="useful-example"></a>Esempio utile

Ecco un'analogia per comprendere meglio le query tra partizioni:

Si supponga di essere un driver di recapito che deve recapitare i pacchetti a complessi di Apartment diversi. Ogni complesso di Apartment dispone di un elenco locale con tutti i numeri di unità del residente. È possibile confrontare ogni Apartment complesso con una partizione fisica e ogni elenco nell'indice della partizione fisica.

È possibile confrontare le query in partizione e tra partizioni usando questo esempio:

### <a name="in-partition-query"></a>Query in partizioni

Se il driver di recapito è a conoscenza del complesso di Apartment corretto (partizione fisica), potrà immediatamente procedere alla compilazione corretta. Il driver può controllare l'elenco dei numeri di unità del residente (indice) del complesso di Apartment e fornire rapidamente i pacchetti appropriati. In questo caso, il driver non spreca tempo o fatica a guidare un complesso di Apartment per controllare e verificare se sono presenti destinatari di pacchetti.

### <a name="cross-partition-query-fan-out"></a>Query tra partizioni (fan-out)

Se il driver di recapito non conosce la struttura di Apartment corretta (partizione fisica), sarà necessario eseguire un'unità per ogni singolo Apartment e controllare l'elenco con tutti i numeri di unità del residente (l'indice). Una volta che arrivano a ogni complesso di Apartment, saranno comunque in grado di usare l'elenco degli indirizzi di ogni residente. Tuttavia, dovranno controllare l'elenco di ogni complesso di Apartment, indipendentemente dal fatto che i destinatari dei pacchetti siano presenti o meno. Questo è il funzionamento delle query tra partizioni. Sebbene possano usare l'indice (non è necessario bussare a ogni sportello singolo), devono controllare separatamente l'indice per ogni partizione fisica.

### <a name="cross-partition-query-scoped-to-only-a-few-physical-partitions"></a>Query tra partizioni (limitate a poche partizioni fisiche)

Se il driver di recapito è consapevole che tutti i destinatari dei pacchetti risiedono in un certo numero di complessi di Apartment, non dovranno eseguire un'unità a ogni singolo. Sebbene il passaggio a un numero ridotto di complessi Apartment richieda ancora più lavoro rispetto alla visita di un unico edificio, il driver di recapito continua a risparmiare tempo e fatica. Se una query ha la chiave di partizione nel filtro con la `IN` parola chiave, verificherà solo gli indici della partizione fisica pertinente per i dati.

## <a name="avoiding-cross-partition-queries"></a>Evitare query tra partizioni

Per la maggior parte dei contenitori, è inevitabile che ci siano alcune query tra partizioni. La presenza di alcune query tra partizioni è accettabile. Quasi tutte le operazioni di query sono supportate tra le partizioni (chiavi di partizione logiche e partizioni fisiche). Azure Cosmos DB dispone anche di numerose ottimizzazioni nel motore di query e negli SDK client per parallelizzare l'esecuzione di query tra partizioni fisiche.

Per la maggior parte degli scenari di lettura intensivo, è consigliabile selezionare semplicemente la proprietà più comune nei filtri di query. È anche necessario assicurarsi che la chiave di partizione rispetti le altre [procedure consigliate](partitioning-overview.md#choose-partitionkey)per la selezione della chiave di partizione.

Evitando in genere le query tra partizioni sono importanti solo con contenitori di grandi dimensioni. Viene addebitato un minimo di circa 2,5 UR ogni volta che si controlla l'indice di una partizione fisica per i risultati, anche se nessun elemento della partizione fisica corrisponde al filtro della query. Di conseguenza, se si dispone solo di una o più partizioni fisiche, le query tra partizioni non utilizzeranno molto più ur rispetto alle query in partizione.

Il numero di partizioni fisiche è associato alla quantità di UR di cui è stato effettuato il provisioning. Ogni partizione fisica consente fino a 10.000 UR di cui è stato effettuato il provisioning e può archiviare fino a 50 GB di dati. Azure Cosmos DB gestirà automaticamente le partizioni fisiche. Il numero di partizioni fisiche nel contenitore dipende dalla velocità effettiva con provisioning e dall'archiviazione utilizzata.

Se il carico di lavoro soddisfa i criteri indicati di seguito, provare a evitare le query tra partizioni:
- Si prevede di avere più di 30.000 UR di cui è stato effettuato il provisioning
- Si prevede di archiviare oltre 100 GB di dati

## <a name="next-steps"></a>Passaggi successivi

Vedere gli articoli seguenti per altre informazioni sul partizionamento in Azure Cosmos DB:

- [Partitioning in Azure Cosmos DB](partitioning-overview.md) (Partizionamento in Azure Cosmos DB)
- [Chiavi di partizione sintetiche in Azure Cosmos DB](synthetic-partition-keys.md)
