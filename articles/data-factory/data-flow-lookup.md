---
title: Trasformazione Ricerca per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Ricerca per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: ef473ea5f88b9108894787785fe1e9083fab1b0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61348176"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Trasformazione Ricerca per il flusso di dati di mapping di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Usare la trasformazione Ricerca per aggiungere dati di riferimento da un'altra origine al flusso di dati. La trasformazione Ricerca richiede un'origine definita che punta alla tabella di riferimento e corrisponde ai campi chiave.

![Trasformazione Ricerca](media/data-flow/lookup1.png "Ricerca")

Selezionare i campi chiave per individuare la corrispondenza tra i campi del flusso in ingresso e i campi dall'origine di riferimento. È necessario avere già creato una nuova origine nell'area di progettazione del flusso di dati da usare come lato destro per la ricerca.

Quando vengono trovate corrispondenze, le righe e le colonne risultanti dall'origine di riferimento verranno aggiunte al flusso di dati. È possibile scegliere i campi di interesse da includere nel sink alla fine del flusso di dati.

## <a name="optimizations"></a>Ottimizzazioni

In Data Factory, flussi di dati eseguito in ambienti di Spark con scalabilità orizzontale. Se il set di dati può essere inserito in uno spazio di memoria nodo di lavoro, è possibile ottimizzare le prestazioni di ricerca.

![Broadcast Join](media/data-flow/broadcast.png "Broadcast Join")

### <a name="broadcast-join"></a>Aggregazione della trasmissione

Selezionare a sinistra e/o sul lato destro broadcast join per richiedere l'ADF per eseguire il push l'intero set di dati da entrambi i lati della relazione di ricerca in memoria.

### <a name="data-partitioning"></a>Partizionamento dei dati

È anche possibile specificare il partizionamento dei dati, selezionando "Partizionamento del Set di" nella scheda Ottimizza della trasformazione ricerca per creare set di dati che è possono adattare meglio in memoria per ogni ruolo di lavoro.

## <a name="next-steps"></a>Passaggi successivi

[Partecipa](data-flow-join.md) e [Exists](data-flow-exists.md) trasformazioni eseguono attività simili in Azure Data factory di Mapping di flusso dei dati. Esaminiamo queste trasformazioni successivo.
