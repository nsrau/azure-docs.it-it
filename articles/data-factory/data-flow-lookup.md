---
title: Trasformazione ricerca flusso di dati mapping Azure Data Factory
description: Trasformazione ricerca flusso di dati mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/03/2019
ms.openlocfilehash: 25d8588f8e2c968dc2516938263aaa7d6ddcff13
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387864"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Trasformazione ricerca flusso di dati mapping Azure Data Factory

Usare la trasformazione Ricerca per aggiungere dati di riferimento da un'altra origine al flusso di dati. La trasformazione Ricerca richiede un'origine definita che punta alla tabella di riferimento e corrisponde ai campi chiave.

![Trasformazione Ricerca](media/data-flow/lookup1.png "Ricerca")

Selezionare i campi chiave per individuare la corrispondenza tra i campi del flusso in ingresso e i campi dall'origine di riferimento. È necessario avere già creato una nuova origine nell'area di progettazione del flusso di dati da usare come lato destro per la ricerca.

Quando vengono trovate corrispondenze, le righe e le colonne risultanti dall'origine di riferimento verranno aggiunte al flusso di dati. È possibile scegliere i campi di interesse da includere nel sink alla fine del flusso di dati. In alternativa, usare una trasformazione seleziona dopo la ricerca per eliminare l'elenco dei campi in modo da mantenere solo i campi da entrambi i flussi che si vuole mantenere.

La trasformazione Ricerca esegue l'equivalente di un left outer join. Quindi, verranno visualizzate tutte le righe dell'origine sinistra combinate con le corrispondenze dal lato destro. Se si dispone di più valori corrispondenti nella ricerca o se si desidera personalizzare l'espressione di ricerca, è preferibile passare a una trasformazione join e utilizzare un cross join. In questo modo si eviteranno eventuali errori di prodotto cartesiano durante l'esecuzione.

## <a name="match--no-match"></a>Corrispondenza/nessuna corrispondenza

Dopo la trasformazione ricerca, è possibile utilizzare le trasformazioni successive per esaminare i risultati di ogni riga delle corrispondenze utilizzando la funzione Expression `isMatch()` per effettuare ulteriori scelte nella logica a seconda che la ricerca abbia restituito o meno una corrispondenza di riga.

## <a name="optimizations"></a>Ottimizzazioni

In Data Factory i flussi di dati vengono eseguiti in ambienti Spark con scalabilità orizzontale. Se il set di dati può rientrare nello spazio di memoria del nodo di lavoro, è possibile ottimizzare le prestazioni di ricerca.

![Join broadcast](media/data-flow/broadcast.png "Join broadcast")

### <a name="broadcast-join"></a>Join di trasmissione

Selezionare Left e/o right broadcast join per richiedere ad ADF di effettuare il push dell'intero set di dati da entrambi i lati della relazione di ricerca in memoria. Per i set di impostazioni più piccoli, questo può migliorare significativamente le prestazioni di ricerca.

### <a name="data-partitioning"></a>Partizionamento dei dati

È inoltre possibile specificare il partizionamento dei dati selezionando "imposta partizionamento" nella scheda Ottimizza della trasformazione Ricerca per creare set di dati che possono essere migliorati in memoria per ogni thread di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Le trasformazioni [join](data-flow-join.md) e [Exists](data-flow-exists.md) eseguono attività simili nei flussi di dati del mapping di ADF. Esaminare le trasformazioni seguenti.
