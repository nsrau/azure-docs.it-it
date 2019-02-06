---
title: Creare una chiave di partizione sintetica in Azure Cosmos DB per distribuire uniformemente dati e carico di lavoro.
description: Informazioni su come usare le chiavi di partizione sintetica nei contenitori di Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/30/2018
ms.author: mjbrown
ms.openlocfilehash: 987bfe023e7355a2780af57dc5e5cac3129a5ca1
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454149"
---
# <a name="create-a-synthetic-partition-key"></a>Creare una chiave di partizione sintetica

È consigliabile disporre di una chiave di partizione con molti valori distinti, ad esempio centinaia o migliaia. L'obiettivo è quello di distribuire il carico di lavoro e i dati in modo uniforme tra gli elementi associati a tali valori di chiave. Se una proprietà di questo tipo non esiste nei dati, è possibile costruire una chiave di partizione sintetica. Le sezioni seguenti descrivono diverse tecniche di base per la generazione di una chiave di partizione sintetico per il contenitore in uso.

## <a name="concatenating-multiple-properties-of-an-item"></a>Concatenazione di più proprietà di un elemento

È possibile creare una chiave di partizione tramite la concatenazione di più valori di proprietà in un'unica proprietà `partitionKey` artificiale. Queste chiavi vengono indicate come chiavi sintetiche. Si consideri il documento di esempio seguente:

```JavaScript
{
"deviceId": "abc-123",
"date": 2018
}
```

Per il documento precedente, una possibilità consiste nell'impostare come chiave di partizione /deviceId o /date, a seconda che si voglia partizionare il contenitore in base all'ID dispositivo o alla data. Un'altra opzione consiste nel concatenare i due valori in una proprietà `partitionKey` sintetica usata come chiave di partizione.

```JavaScript
{
"deviceId": "abc-123",
"date": 2018,
"partitionKey": "abc-123-2018"
}
```

In scenari in tempo reale è possibile che in un database siano presenti migliaia di documenti e di conseguenza, anziché aggiungere manualmente la chiave sintetica, è necessario definire la logica lato client per concatenare i valori e inserire la chiave sintetica nei documenti.

## <a name="using-a-partition-key-with-random-suffix"></a>Uso di una chiave di partizione con suffisso casuale

Un'altra strategia possibile per distribuire il carico di lavoro in modo più uniforme consiste nell'aggiungere un numero casuale alla fine del valore della chiave di partizione. La distribuzione degli elementi secondo questa modalità consente di eseguire operazioni di scrittura parallele tra le partizioni.

Se ad esempio una chiave di partizione rappresenta una data, è possibile scegliere un numero casuale compreso tra 1 e 400 e concatenarlo come suffisso alla data. Tale metodo restituisce valori della chiave di partizione come 2018-08-09.1, 2018-08-09.2 e così via, fino a 2018-08-09.400. Se si per la chiave di partizione si usano valori casuali, le operazioni di scrittura giornaliere sul contenitore vengono distribuite uniformemente tra più partizioni. Questo metodo comporta un parallelismo e una velocità effettiva complessiva migliori.

## <a name="using-a-partition-key-with-pre-calculated-suffixes"></a>Uso di una chiave di partizione con suffissi pre-calcolati 

Sebbene la strategia di usare numeri casuali per la chiave di partizione possa migliorare notevolmente la velocità effettiva di scrittura, è difficile leggere un elemento specifico, perché non si conosce il valore del suffisso usato quando si scrive l'elemento. Per semplificare la lettura di elementi singoli, è possibile usare la strategia di suffissi pre-calcolati. Questo metodo prevede l'uso di un numero calcolato in base agli elementi su cui eseguire la query anziché l'uso di un numero casuale per distribuire gli elementi tra le partizioni.

Si consideri l'esempio precedente, in cui un contenitore usa una data nella chiave di partizione. Si supponga ora che a ogni elemento sia associato un numero identificativo del veicolo come attributo accessibile e che si eseguano spesso query per trovare gli elementi in base a tale numero, oltre alla data. Prima che l'applicazione scriva l'elemento nel contenitore, è possibile calcolare un suffisso hash in base al numero identificativo del veicolo e aggiungerlo alla data della chiave di partizione. Il calcolo potrebbe generare un numero compreso tra 1 e 400 distribuito in modo uniforme, analogamente ai risultati ottenuti con il metodo di strategia casuale. Il valore di chiave di partizione è data concatenata con il risultato calcolato.

Con questa strategia le operazioni di scrittura vengono distribuite uniformemente tra i valori di chiave di partizione e tra tutte le partizioni. È possibile leggere facilmente un elemento specifico e la data, poiché è possibile calcolare il valore di chiave di partizione per un determinato numero di identificazione del veicolo. Il vantaggio di questo metodo consiste nell'evitare di creare una singola chiave di partizione con accesso frequente (ovvero la chiave di partizione che accetta tutti i carichi di lavoro). 

## <a name="next-steps"></a>Passaggi successivi

Per altre informazioni sul concetto di partizionamento, vedere gli articoli seguenti:

* Informazioni sulle [partizioni logiche](partition-data.md)
* Informazioni su come [effettuare il provisioning della velocità effettiva in contenitori e database Cosmos](set-throughput.md)
* Informazioni su [come effettuare il provisioning della velocità effettiva in un contenitore Cosmos](how-to-provision-container-throughput.md)
* Informazioni su [come effettuare il provisioning della velocità effettiva in un database Cosmos](how-to-provision-database-throughput.md)
