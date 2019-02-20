---
title: Creare una chiave di partizione sintetica in Azure Cosmos DB per distribuire uniformemente dati e carico di lavoro.
description: Informazioni su come usare le chiavi di partizione sintetica nei contenitori di Azure Cosmos
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: mjbrown
ms.openlocfilehash: 8becfe375f2e887348729cf1d76820fc41156d2a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/11/2019
ms.locfileid: "55997103"
---
# <a name="create-a-synthetic-partition-key"></a>Creare una chiave di partizione sintetica

È consigliabile disporre di una chiave di partizione con molti valori distinti, ad esempio centinaia o migliaia. L'obiettivo è quello di distribuire il carico di lavoro e i dati in modo uniforme tra gli elementi associati a tali valori di chiave. Se una proprietà di questo tipo non esiste nei dati, è possibile costruire una chiave di partizione sintetica. Le sezioni seguenti descrivono diverse tecniche di base per la generazione di una chiave di partizione sintetico per il contenitore in uso.

## <a name="concatenate-multiple-properties-of-an-item"></a>Concatenare più proprietà di un elemento

È possibile creare una chiave di partizione tramite la concatenazione di più valori di proprietà in un'unica proprietà `partitionKey` artificiale. Queste chiavi vengono indicate come chiavi sintetiche. Si consideri il documento di esempio seguente:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Per il documento precedente, una possibilità consiste nell'impostare /deviceId o /date come chiave di partizione. Usare questa opzione se si desidera partizionare il contenitore in base all'ID dispositivo o alla data. Un'altra opzione consiste nel concatenare i due valori in una proprietà `partitionKey` sintetica usata come chiave di partizione.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

Negli scenari in tempo reale, è possibile avere migliaia di documenti in un database. Anziché aggiungere manualmente la chiave sintetica, definire la logica lato client per concatenare i valori e inserire la chiave sintetica nei documenti.

## <a name="use-a-partition-key-with-a-random-suffix"></a>Usare una chiave di partizione con suffisso casuale

Un'altra strategia possibile per distribuire il carico di lavoro in modo più uniforme consiste nell'aggiungere un numero casuale alla fine del valore della chiave di partizione. Quando gli elementi vengono distribuiti secondo questa modalità, è possibile eseguire operazioni di scrittura parallele tra le partizioni.

Ad esempio se una chiave di partizione rappresenta una data. In questo caso è possibile scegliere un numero casuale compreso tra 1 e 400 e concatenarlo come suffisso alla data. Tale metodo restituisce valori della chiave di partizione come 2018-08-09.1, 2018-08-09.2 e così via, fino a 2018-08-09.400. Poiché per la chiave di partizione si usano valori casuali, le operazioni di scrittura giornaliere sul contenitore vengono distribuite uniformemente tra più partizioni. Questo metodo comporta un parallelismo e una velocità effettiva complessiva migliori.

## <a name="use-a-partition-key-with-precalculated-suffixes"></a>Usare una chiave di partizione con suffissi pre-calcolati 

La strategia di scelta casuale può migliorare notevolmente la velocità effettiva di scrittura, ma rende difficile la lettura di un elemento specifico. Non si conosce il valore del suffisso usato quando si scrive l'elemento; per semplificare la lettura di elementi singoli, usare la strategia di suffissi pre-calcolati. Questo metodo prevede l'uso di un numero calcolato in base agli elementi su cui eseguire la query anziché l'uso di un numero casuale per distribuire gli elementi tra le partizioni.

Si consideri l'esempio precedente, in cui un contenitore usa una data nella chiave di partizione. Si supponga che ogni elemento abbia un attributo per un numero di identificazione del veicolo (vin) accessibile. Si supponga anche l'esecuzione frequente di query per trovare gli elementi a partire numero di identificazione del veicolo, oltre alla data. Prima che l'applicazione scriva l'elemento nel contenitore, è possibile calcolare un suffisso hash in base al numero identificativo del veicolo e aggiungerlo alla data della chiave di partizione. Il calcolo potrebbe generare un numero compreso tra 1 e 400 che viene distribuito uniformemente. Questo risultato è simile ai risultati prodotti dal metodo di strategia casuale. Il valore chiave di partizione è la data concatenata con il risultato calcolato.

Con questa strategia le operazioni di scrittura vengono distribuite uniformemente tra i valori di chiave di partizione e tra tutte le partizioni. È possibile leggere facilmente un elemento specifico e la data poiché è possibile calcolare il valore di chiave di partizione per un determinato numero di identificazione del veicolo. Il vantaggio di questo metodo consiste nell'evitare di creare una singola chiave di partizione con accesso frequente. Una chiave di partizione con accesso frequente è la chiave di partizione che accetta tutti i carichi di lavoro. 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul concetto di partizionamento, vedere gli articoli seguenti:

* Altre informazioni sulle [partizioni logiche](partition-data.md).
* Altre informazioni su come [effettuare il provisioning della velocità effettiva per contenitori e database di Azure Cosmos](set-throughput.md).
* Informazioni su [come effettuare il provisioning della velocità effettiva in un contenitore di Azure Cosmos](how-to-provision-container-throughput.md).
* Informazioni su [come effettuare il provisioning della velocità effettiva in un database di Azure Cosmos](how-to-provision-database-throughput.md).
