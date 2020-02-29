---
title: Mapping della trasformazione ricerca flusso di dati
description: Trasformazione ricerca flusso di dati mapping Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/26/2020
ms.openlocfilehash: 2216e1bf058eef486dbfefba24d52bdc6bdb232f
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164679"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Trasformazione ricerca flusso di dati mapping Azure Data Factory

Usare la trasformazione Ricerca per aggiungere dati di riferimento da un'altra origine al flusso di dati. La trasformazione Ricerca richiede un'origine definita che punta alla tabella di riferimento e corrisponde ai campi chiave.

![Trasformazione Ricerca](media/data-flow/lookup1.png "Ricerca")

Selezionare i campi chiave per individuare la corrispondenza tra i campi del flusso in ingresso e i campi dall'origine di riferimento. È necessario avere già creato una nuova origine nell'area di progettazione del flusso di dati da usare come lato destro per la ricerca.

Quando vengono trovate corrispondenze, le righe e le colonne risultanti dall'origine di riferimento verranno aggiunte al flusso di dati. È possibile scegliere i campi di interesse da includere nel sink alla fine del flusso di dati. In alternativa, usare una trasformazione seleziona dopo la ricerca per eliminare l'elenco dei campi in modo da mantenere solo i campi da entrambi i flussi che si vuole mantenere.

La trasformazione Ricerca esegue l'equivalente di un left outer join. Quindi, verranno visualizzate tutte le righe dell'origine sinistra combinate con le corrispondenze dal lato destro. Se si dispone di più valori corrispondenti nella ricerca o se si desidera personalizzare l'espressione di ricerca, è preferibile passare a una trasformazione join e utilizzare un cross join. In questo modo si eviteranno eventuali errori di prodotto cartesiano durante l'esecuzione.

## <a name="match--no-match"></a>Corrispondenza/nessuna corrispondenza

Dopo la trasformazione ricerca, è possibile utilizzare le trasformazioni successive per esaminare i risultati di ogni riga delle corrispondenze utilizzando la funzione Expression `isMatch()` per eseguire ulteriori scelte nella logica a seconda che la ricerca abbia restituito o meno una corrispondenza di riga.

![Modello di ricerca](media/data-flow/lookup111.png "Modello di ricerca")

Dopo aver utilizzato la trasformazione ricerca, è possibile aggiungere una suddivisione della trasformazione Suddivisione condizionale sulla funzione ```isMatch()```. Nell'esempio precedente, le righe corrispondenti passano attraverso il flusso superiore e le righe non corrispondenti passano attraverso il flusso di ```NoMatch```.

## <a name="first-or-last-value"></a>Primo o ultimo valore

La trasformazione ricerca viene implementata come left outer join. Quando si dispone di più corrispondenze dalla ricerca, può essere utile ridurre le più righe corrispondenti selezionando la prima riga corrispondente, l'ultima corrispondenza o qualsiasi riga casuale.

### <a name="option-1"></a>Opzione 1

![Ricerca su riga singola](media/data-flow/singlerowlookup.png "Ricerca su riga singola")

* Corrisponde a più righe: lasciare vuoto per restituire una corrispondenza con riga singola
* Corrispondenza in: selezionare prima, ultima o qualsiasi corrispondenza
* Condizioni di ordinamento: se si seleziona primo o ultimo, ADF richiede che i dati siano ordinati in modo che sia presente la logica alla base della prima e dell'ultima

> [!NOTE]
> Usare la prima o l'ultima opzione nel selettore di riga singola se è necessario controllare il valore da restituire dalla ricerca. L'utilizzo di ricerche "any" o più righe sarà più rapido.

### <a name="option-2"></a>Opzione 2

Questa operazione può essere eseguita anche utilizzando una trasformazione aggregazione dopo la ricerca. In questo caso, viene usata una trasformazione aggregazione denominata ```PickFirst``` per selezionare il primo valore dalle corrispondenze di ricerca.

![Aggregazione ricerca](media/data-flow/lookup333.png "Aggregazione ricerca")

![Ricerca prima](media/data-flow/lookup444.png "Ricerca prima")

## <a name="optimizations"></a>Ottimizzazioni

In Data Factory i flussi di dati vengono eseguiti in ambienti Spark con scalabilità orizzontale. Se il set di dati può rientrare nello spazio di memoria del nodo di lavoro, è possibile ottimizzare le prestazioni di ricerca.

![Join broadcast](media/data-flow/broadcast.png "Join broadcast")

### <a name="broadcast-join"></a>Join di trasmissione

Selezionare Left e/o right broadcast join per richiedere ad ADF di effettuare il push dell'intero set di dati da entrambi i lati della relazione di ricerca in memoria. Per i set di impostazioni più piccoli, questo può migliorare significativamente le prestazioni di ricerca.

### <a name="data-partitioning"></a>Partizionamento dei dati

È inoltre possibile specificare il partizionamento dei dati selezionando "imposta partizionamento" nella scheda Ottimizza della trasformazione Ricerca per creare set di dati che possono essere migliorati in memoria per ogni thread di lavoro.

## <a name="next-steps"></a>Passaggi successivi

* Le trasformazioni [join](data-flow-join.md) e [Exists](data-flow-exists.md) eseguono attività simili nei flussi di dati del mapping di ADF. Esaminare le trasformazioni seguenti.
* Usare una [Suddivisione condizionale](data-flow-conditional-split.md) con ```isMatch()``` per suddividere le righe in valori corrispondenti e non corrispondenti
