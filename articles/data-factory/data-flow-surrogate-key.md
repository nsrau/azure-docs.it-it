---
title: Trasformazione della chiave surrogata nel mapping del flusso di datiSurrogate key transformation in mapping data flow
description: Come usare la trasformazione chiave surrogata del flusso di dati del flusso di dati di Azure Data Factory per generare valori di chiave sequenzialiHow to use Azure Data Factory's mapping data flow Surrogate Key Transformation to generate sequential key values
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: e5ac25c002da121be3adadf0eed978dd60ba26d9
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010637"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Trasformazione della chiave surrogata nel mapping del flusso di datiSurrogate key transformation in mapping data flow 

Utilizzare la trasformazione della chiave surrogata per aggiungere un valore di chiave crescente a ogni riga di dati. Ciò è utile quando si progettano tabelle delle dimensioni in un modello di dati analitici dello schema a stella. In uno schema a stella, ogni membro nelle tabelle delle dimensioni richiede una chiave univoca che sia una chiave non aziendale.

## <a name="configuration"></a>Configurazione

![Trasformazione chiave surrogataSurrogate Key Transform](media/data-flow/surrogate.png "Trasformazione chiave surrogataSurrogate Key Transformation")

**Colonna chiave:** Nome della colonna chiave surrogata generata.

**Valore iniziale:** Il valore di chiave più basso che verrà generato.

## <a name="increment-keys-from-existing-sources"></a>Incrementare le chiavi dalle origini esistenti

Per iniziare la sequenza da un valore presente in un'origine, usare una trasformazione di colonna derivata in seguito alla trasformazione della chiave surrogata per sommare i due valori:To start your sequence from a value that exists in a source, use a derived column transformation following your surrogate key transformation to add the two values together:

![SK aggiungere Max](media/data-flow/sk006.png "Trasformazione chiave surrogata Aggiungi MaxSurrogate Key Transformation Add Max")

### <a name="increment-from-existing-maximum-value"></a>Incremento dal valore massimo esistente

Per eseguire il seeding del valore della chiave con il valore massimo precedente, è possibile usare due tecniche in base alla posizione dei dati di origine.

#### <a name="database-sources"></a>Origini del database

Utilizzare un'opzione di query SQL per selezionare MAX() dall'origine. Per esempio`Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Query chiave surrogata](media/data-flow/sk002.png "Query di trasformazione chiave surrogataSurrogate Key Transformation Query")

#### <a name="file-sources"></a>Origini file

Se il valore massimo precedente è `max()` in un file, usare la funzione nella trasformazione di aggregazione per ottenere il valore massimo precedente:If your previous max value is in a file, use the function in the aggregate transformation to get the previous max value:

![File di chiave surrogatoSurrogate Key File](media/data-flow/sk008.png "File di chiave surrogatoSurrogate Key File")

In entrambi i casi, è necessario unire i nuovi dati in ingresso con l'origine che contiene il valore massimo precedente.

![Unione chiave surrogata](media/data-flow/sk004.png "Unione chiave surrogata")

## <a name="data-flow-script"></a>Script del flusso di dati

### <a name="syntax"></a>Sintassi

```
<incomingStream> 
    keyGenerate(
        output(<surrogateColumnName> as long),
        startAt: <number>L
    ) ~> <surrogateKeyTransformationName>
```

### <a name="example"></a>Esempio

![Trasformazione chiave surrogataSurrogate Key Transform](media/data-flow/surrogate.png "Trasformazione chiave surrogataSurrogate Key Transformation")

Lo script del flusso di dati per la configurazione della chiave surrogata precedente si trova nel frammento di codice riportato di seguito.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Passaggi successivi

In questi esempi vengono utilizzate le trasformazioni [Join](data-flow-join.md) e [Colonna derivata.](data-flow-derived-column.md)
