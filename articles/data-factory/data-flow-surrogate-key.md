---
title: Mapping data flow Surrogate Key Transformation
description: Come usare la trasformazione chiave surrogata del flusso di dati del flusso di dati di Azure Data Factory per generare valori di chiave sequenzialiHow to use Azure Data Factory's mapping data flow Surrogate Key Transformation to generate sequential key values
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/12/2019
ms.openlocfilehash: bab48aa9079c1b8020bb828a6bb91bd244a78cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74930210"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mapping data flow Surrogate Key Transformation



Usare la trasformazione Chiave surrogata per aggiungere un valore di chiave arbitrario non business incrementale al set di righe del flusso di dati. Ciò è utile quando si progettano le tabelle delle dimensioni in un modello di dati analitici con schema star in cui ogni membro nelle tabelle delle dimensioni deve avere una chiave univoca non business, parte della metodologia di data warehousing Kimball.

![Trasformazione chiave surrogataSurrogate Key Transform](media/data-flow/surrogate.png "Trasformazione chiave surrogataSurrogate Key Transformation")

"Key Column" (Colonna chiave) è il nome che verrà assegnato alla nuova colonna chiave surrogata.

"Start Value" (Valore iniziale) è il punto di inizio del valore incrementale.

## <a name="increment-keys-from-existing-sources"></a>Incrementare le chiavi dalle origini esistenti

Se si desidera iniziare la sequenza da un valore presente in un'origine, è possibile usare una trasformazione Colonna derivata immediatamente dopo la trasformazione Chiave surrogata e sommare i due valori:

![SK aggiungere Max](media/data-flow/sk006.png "Trasformazione chiave surrogata Aggiungi MaxSurrogate Key Transformation Add Max")

Per eseguire il seedotto del valore della chiave con il valore massimo precedente, è possibile utilizzare due tecniche:To seed the key value with the previous max, there are two techniques that you can use:

### <a name="database-sources"></a>Origini del database

Utilizzare l'opzione "Query" per selezionare MAX() dall'origine utilizzando la trasformazione Origine:

![Query chiave surrogata](media/data-flow/sk002.png "Query di trasformazione chiave surrogataSurrogate Key Transformation Query")

### <a name="file-sources"></a>Origini file

Se il valore massimo precedente è in un file, è possibile usare la trasformazione Source con una trasformazione Aggregazione e la funzione di espressione MAX() per ottenere il valore massimo precedente:

![File di chiave surrogatoSurrogate Key File](media/data-flow/sk008.png "File di chiave surrogatoSurrogate Key File")

In entrambi i casi, è necessario unire i nuovi dati in ingresso con l'origine che contiene il valore massimo precedente:In both cases, you must Join your incoming new data together with your source that contains the previous max value:

![Unione chiave surrogata](media/data-flow/sk004.png "Unione chiave surrogata")

## <a name="next-steps"></a>Passaggi successivi

In questi esempi vengono utilizzate le trasformazioni [Join](data-flow-join.md) e [Colonna derivata.](data-flow-derived-column.md)
