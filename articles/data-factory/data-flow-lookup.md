---
title: Trasformazione Ricerca per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Ricerca per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef72b7aed12afd1cee47b11bc7584d1e53bf2af5
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029335"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Trasformazione Ricerca per il flusso di dati di mapping di Azure Data Factory



Usare la trasformazione Ricerca per aggiungere dati di riferimento da un'altra origine al flusso di dati. La trasformazione Ricerca richiede un'origine definita che punta alla tabella di riferimento e corrisponde ai campi chiave.

![Trasformazione Ricerca](media/data-flow/lookup1.png "Ricerca")

Selezionare i campi chiave per individuare la corrispondenza tra i campi del flusso in ingresso e i campi dall'origine di riferimento. È necessario avere già creato una nuova origine nell'area di progettazione del flusso di dati da usare come lato destro per la ricerca.

Quando vengono trovate corrispondenze, le righe e le colonne risultanti dall'origine di riferimento verranno aggiunte al flusso di dati. È possibile scegliere i campi di interesse da includere nel sink alla fine del flusso di dati.

## <a name="match--no-match"></a>Corrispondenza/nessuna corrispondenza

Dopo la trasformazione ricerca, è possibile utilizzare le trasformazioni successive per esaminare i risultati di ogni riga delle corrispondenze utilizzando la funzione Expression `isMatch()` per effettuare ulteriori scelte nella logica a seconda che la ricerca abbia restituito o meno una corrispondenza di riga.

## <a name="optimizations"></a>Ottimizzazioni

In Data Factory i flussi di dati vengono eseguiti in ambienti Spark con scalabilità orizzontale. Se il set di dati può rientrare nello spazio di memoria del nodo di lavoro, è possibile ottimizzare le prestazioni di ricerca.

![](media/data-flow/broadcast.png "Join") broadcast join broadcast

### <a name="broadcast-join"></a>Join di trasmissione

Selezionare Left e/o right broadcast join per richiedere ad ADF di effettuare il push dell'intero set di dati da entrambi i lati della relazione di ricerca in memoria.

### <a name="data-partitioning"></a>Partizionamento dei dati

È inoltre possibile specificare il partizionamento dei dati selezionando "imposta partizionamento" nella scheda Ottimizza della trasformazione Ricerca per creare set di dati che possono essere migliorati in memoria per ogni thread di lavoro.

## <a name="next-steps"></a>Passaggi successivi

Le trasformazioni [join](data-flow-join.md) e [Exists](data-flow-exists.md) eseguono attività simili nei flussi di dati del mapping di ADF. Esaminare le trasformazioni seguenti.
