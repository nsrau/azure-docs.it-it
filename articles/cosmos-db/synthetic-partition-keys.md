---
title: Creare una chiave di partizione sintetica in Azure Cosmos DB per distribuire uniformemente dati e carico di lavoro.
description: Informazioni su come usare le chiavi di partizione sintetica nei contenitori di Azure Cosmos
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
author: markjbrown
ms.author: mjbrown
ms.openlocfilehash: 8b4e2b8abac39f3268e0da7838acd566f40fdccc
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754808"
---
# <a name="create-a-synthetic-partition-key"></a>Creare una chiave di partizione sintetica

È consigliabile avere una chiave di partizione con molti valori distinti, ad esempio centinaia o migliaia. L'obiettivo è quello di distribuire il carico di lavoro e i dati in modo uniforme tra gli elementi associati a tali valori di chiave. Se tale proprietà non esiste nei dati, è possibile costruire una chiave di *partizione sintetica*. Questo documento descrive diverse tecniche di base per la generazione di una chiave di partizione sintetica per il contenitore Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Concatenare più proprietà di un elemento

È possibile creare una chiave di partizione tramite la concatenazione di più valori di proprietà in un'unica proprietà `partitionKey` artificiale. Queste chiavi vengono indicate come chiavi sintetiche. Si consideri il documento di esempio seguente:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Per il documento precedente, una possibilità consiste nell'impostare /deviceId o /date come chiave di partizione. Usare questa opzione se si vuole partizionare il contenitore in base all'ID o alla data del dispositivo. Un'altra opzione consiste nel concatenare i due valori in una proprietà `partitionKey` sintetica usata come chiave di partizione.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Negli scenari in tempo reale è possibile disporre di migliaia di elementi in un database. Anziché aggiungere manualmente la chiave sintetica, definire la logica lato client per concatenare i valori e inserire la chiave sintetica negli elementi nei contenitori Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Usare una chiave di partizione con suffisso casuale

Un'altra strategia possibile per distribuire il carico di lavoro in modo più uniforme consiste nell'aggiungere un numero casuale alla fine del valore della chiave di partizione. Quando gli elementi vengono distribuiti secondo questa modalità, è possibile eseguire operazioni di scrittura parallele tra le partizioni.

Ad esempio se una chiave di partizione rappresenta una data. È possibile scegliere un numero casuale compreso tra 1 e 400 e concatenarlo come suffisso alla data. Questo metodo genera valori di chiave di partizione come  `2018-08-09.1`, `2018-08-09.2` e così via, tramite  `2018-08-09.400`. Poiché per la chiave di partizione si usano valori casuali, le operazioni di scrittura giornaliere sul contenitore vengono distribuite uniformemente tra più partizioni. Questo metodo comporta un parallelismo e una velocità effettiva complessiva migliori.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Usare una chiave di partizione con suffissi pre-calcolati 

La strategia di suffisso casuale può migliorare significativamente la velocità effettiva di scrittura, ma è difficile leggere un elemento specifico. Non si conosce il valore del suffisso usato quando è stato scritto l'elemento. Per semplificare la lettura di singoli elementi, utilizzare la strategia suffissi pre-calcolati. Anziché utilizzare un numero casuale per distribuire gli elementi tra le partizioni, utilizzare un numero calcolato in base a un elemento che si desidera eseguire una query.

Si consideri l'esempio precedente, in cui un contenitore utilizza una data come chiave di partizione. Si supponga ora che ogni elemento disponga di un attributo  `Vehicle-Identification-Number` (`VIN`) a cui si vuole accedere. Inoltre, si supponga di eseguire spesso query per trovare gli elementi in base alla `VIN`, oltre alla data. Prima che l'applicazione scriva l'elemento nel contenitore, è possibile calcolare un suffisso hash in base al numero identificativo del veicolo e aggiungerlo alla data della chiave di partizione. Il calcolo potrebbe generare un numero compreso tra 1 e 400 distribuito in modo uniforme. Questo risultato è simile ai risultati prodotti dal metodo di strategia dei suffissi casuali. Il valore chiave di partizione è la data concatenata con il risultato calcolato.

Con questa strategia le operazioni di scrittura vengono distribuite uniformemente tra i valori di chiave di partizione e tra tutte le partizioni. È possibile leggere facilmente un elemento e una data particolari, perché è possibile calcolare il valore della chiave di partizione per un `Vehicle-Identification-Number` specifico. Il vantaggio di questo metodo è che è possibile evitare di creare una singola chiave di partizione a caldo, ovvero una chiave di partizione che accetta tutto il carico di lavoro. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul concetto di partizionamento, vedere gli articoli seguenti:

* [Partizionamento e scalabilità orizzontale in Azure Cosmos DB](partition-data.md)
* Altre informazioni su come [effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos](set-throughput.md).
* [Effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md)
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
