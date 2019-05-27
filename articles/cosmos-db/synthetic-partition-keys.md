---
title: Creare una chiave di partizione sintetica in Azure Cosmos DB per distribuire uniformemente dati e carico di lavoro.
description: Informazioni su come usare le chiavi di partizione sintetica nei contenitori di Azure Cosmos
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.openlocfilehash: 1fd436746dcd2e93a1699ac5c68965213c74580e
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/21/2019
ms.locfileid: "65978872"
---
# <a name="create-a-synthetic-partition-key"></a>Creare una chiave di partizione sintetica

È la procedura consigliata per avere una chiave di partizione con molti valori distinti, ad esempio centinaia o migliaia. L'obiettivo è quello di distribuire il carico di lavoro e i dati in modo uniforme tra gli elementi associati a tali valori di chiave. Se tale proprietà non esiste nei dati, è possibile costruire una *chiave di partizione sintetiche*. Questo documento illustra diverse tecniche di base per la generazione di una chiave di partizione sintetico per il contenitore Cosmos.

## <a name="concatenate-multiple-properties-of-an-item"></a>Concatenare più proprietà di un elemento

È possibile creare una chiave di partizione tramite la concatenazione di più valori di proprietà in un'unica proprietà `partitionKey` artificiale. Queste chiavi vengono indicate come chiavi sintetiche. Si consideri il documento di esempio seguente:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Per il documento precedente, una possibilità consiste nell'impostare /deviceId o /date come chiave di partizione. Usare questa opzione, se si desidera partizionare il contenitore in base all'ID dispositivo o Data. Un'altra opzione consiste nel concatenare i due valori in una proprietà `partitionKey` sintetica usata come chiave di partizione.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

In scenari in tempo reale, è possibile avere migliaia di elementi in un database. Anziché aggiungere manualmente la chiave sintetica, definire la logica lato client per concatenare i valori e inserire la chiave sintetica negli elementi dei contenitori Cosmos.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Usare una chiave di partizione con suffisso casuale

Un'altra strategia possibile per distribuire il carico di lavoro in modo più uniforme consiste nell'aggiungere un numero casuale alla fine del valore della chiave di partizione. Quando gli elementi vengono distribuiti secondo questa modalità, è possibile eseguire operazioni di scrittura parallele tra le partizioni.

Ad esempio se una chiave di partizione rappresenta una data. È possibile scegliere un numero casuale compreso tra 1 e 400 e concatenarlo come suffisso per la data. Questo metodo restituisce valori di chiave di partizione, ad esempio `2018-08-09.1`,`2018-08-09.2`e così via, tramite `2018-08-09.400`. Poiché per la chiave di partizione si usano valori casuali, le operazioni di scrittura giornaliere sul contenitore vengono distribuite uniformemente tra più partizioni. Questo metodo comporta un parallelismo e una velocità effettiva complessiva migliori.

## <a name="use-a-partition-key-with-pre-calculated-suffixes"></a>Usare una chiave di partizione con i suffissi pre-calcolati 

La strategia di suffisso casuale può migliorare notevolmente la velocità effettiva di scrittura, ma è difficile da leggere un elemento specifico. Non si conosce il valore del suffisso usato quando è stato scritto l'elemento. Per renderlo più facile da leggere elementi singoli, usare la strategia di suffissi pre-calcolate. Invece di usare un numero casuale per distribuire gli elementi tra le partizioni, usare un numero che viene calcolato basato su un elemento che si desidera eseguire una query.

Si consideri l'esempio precedente, in cui un contenitore Usa una data come chiave di partizione. Si supponga ora che ogni elemento ha un `Vehicle-Identification-Number` (`VIN`) attributo che si desidera accedere. Inoltre, si supponga che si eseguono spesso query per trovare gli elementi dal `VIN`, oltre alla data. Prima che l'applicazione scriva l'elemento nel contenitore, è possibile calcolare un suffisso hash in base al numero identificativo del veicolo e aggiungerlo alla data della chiave di partizione. Il calcolo potrebbe generare un numero compreso tra 1 e 400 che è distribuito uniformemente. Questo risultato è simile per i risultati prodotti dal metodo strategia suffisso casuale. Il valore chiave di partizione è la data concatenata con il risultato calcolato.

Con questa strategia le operazioni di scrittura vengono distribuite uniformemente tra i valori di chiave di partizione e tra tutte le partizioni. Consentire la lettura un determinato elemento e data, poiché è possibile calcolare il valore di chiave di partizione per uno specifico `Vehicle-Identification-Number`. Il vantaggio di questo metodo è possibile evitare di creare una chiave di partizione singola a caldo, ad esempio, una chiave di partizione che accetta tutti i carichi di lavoro. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul concetto di partizionamento, vedere gli articoli seguenti:

* Altre informazioni sulle [partizioni logiche](partition-data.md).
* Altre informazioni su come [effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos](set-throughput.md).
* Informazioni su [come effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md).
* [Effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md)
