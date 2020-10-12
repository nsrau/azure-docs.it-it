---
title: Trasformazione della chiave surrogata nel flusso di dati di mapping
description: Come utilizzare la trasformazione della chiave surrogata del flusso di dati di Azure Data Factory per generare valori di chiave sequenziale
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/08/2020
ms.openlocfilehash: ade2fd6011bbcdaed4ce31ce70bfb4235429bb0d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "81606304"
---
# <a name="surrogate-key-transformation-in-mapping-data-flow"></a>Trasformazione della chiave surrogata nel flusso di dati di mapping 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Utilizzare la trasformazione chiave surrogata per aggiungere un valore di chiave di incremento a ogni riga di dati. Questa operazione è utile quando si progettano tabelle delle dimensioni in un modello di dati analitici con schema a stella. In uno schema a stella, ogni membro nelle tabelle delle dimensioni richiede una chiave univoca che non sia business Key.

## <a name="configuration"></a>Configurazione

![Trasformazione chiave surrogata](media/data-flow/surrogate.png "Trasformazione chiave surrogata")

**Colonna chiave:** Nome della colonna chiave surrogata generata.

**Valore iniziale:** Valore di chiave più basso che verrà generato.

## <a name="increment-keys-from-existing-sources"></a>Incrementa le chiavi da origini esistenti

Per avviare la sequenza da un valore presente in un'origine, utilizzare una trasformazione colonna derivata dopo la trasformazione della chiave surrogata per aggiungere i due valori:

![SK Aggiungi max](media/data-flow/sk006.png "Trasformazione chiave surrogata-Aggiungi max")

### <a name="increment-from-existing-maximum-value"></a>Incremento da un valore massimo esistente

Per eseguire il seeding del valore della chiave con il numero massimo precedente, sono disponibili due tecniche che è possibile usare in base alla posizione dei dati di origine.

#### <a name="database-sources"></a>Origini database

Usare un'opzione di query SQL per selezionare MAX () dall'origine. Per esempio `Select MAX(<surrogateKeyName>) as maxval from <sourceTable>`/

![Query della chiave surrogata](media/data-flow/sk002.png "Query trasformazione chiave surrogata")

#### <a name="file-sources"></a>Origini file

Se il valore max precedente si trova in un file, usare la `max()` funzione nella trasformazione aggregazione per ottenere il valore max precedente:

![File di chiave surrogata](media/data-flow/sk008.png "File di chiave surrogata")

In entrambi i casi, è necessario aggiungere i nuovi dati in ingresso insieme all'origine che contiene il valore massimo precedente.

![Join chiave surrogata](media/data-flow/sk004.png "Join chiave surrogata")

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

![Trasformazione chiave surrogata](media/data-flow/surrogate.png "Trasformazione chiave surrogata")

Lo script del flusso di dati per la configurazione della chiave surrogata precedente si trova nel frammento di codice riportato di seguito.

```
AggregateDayStats
    keyGenerate(
        output(key as long),
        startAt: 1L
    ) ~> SurrogateKey1
```

## <a name="next-steps"></a>Passaggi successivi

In questi esempi vengono utilizzate le trasformazioni [join](data-flow-join.md) e [colonna derivata](data-flow-derived-column.md) .
