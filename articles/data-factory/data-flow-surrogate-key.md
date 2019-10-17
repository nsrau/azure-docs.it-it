---
title: Trasformazione della chiave surrogata del flusso di dati Azure Data Factory mapping
description: Come utilizzare la trasformazione della chiave surrogata del flusso di dati di Azure Data Factory per generare valori di chiave sequenziale
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 144d6298a13d35d94a68b35c443a3a47cefcfc2a
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2019
ms.locfileid: "72387172"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Trasformazione della chiave surrogata del flusso di dati mapping



Usare la trasformazione Chiave surrogata per aggiungere un valore di chiave arbitrario non business incrementale al set di righe del flusso di dati. Ciò è utile quando si progettano le tabelle delle dimensioni in un modello di dati analitici con schema star in cui ogni membro nelle tabelle delle dimensioni deve avere una chiave univoca non business, parte della metodologia di data warehousing Kimball.

![Trasformazione chiave surrogata](media/data-flow/surrogate.png "Trasformazione chiave surrogata")

"Key Column" (Colonna chiave) è il nome che verrà assegnato alla nuova colonna chiave surrogata.

"Start Value" (Valore iniziale) è il punto di inizio del valore incrementale.

## <a name="increment-keys-from-existing-sources"></a>Incrementa le chiavi da origini esistenti

Se si vuole avviare la sequenza da un valore presente in un'origine, è possibile usare una trasformazione colonna derivata immediatamente dopo la trasformazione chiave surrogata e aggiungere i due valori insieme:

![SK Aggiungi max](media/data-flow/sk006.png "Trasformazione chiave surrogata-Aggiungi max")

Per eseguire il seeding del valore della chiave con il valore max precedente, sono disponibili due tecniche che è possibile usare:

### <a name="database-sources"></a>Origini database

Usare l'opzione "query" per selezionare MAX () dall'origine usando la trasformazione di origine:

![Query della chiave surrogata](media/data-flow/sk002.png "Query trasformazione chiave surrogata")

### <a name="file-sources"></a>Origini file

Se il valore max precedente si trova in un file, è possibile utilizzare la trasformazione origine insieme a una trasformazione aggregazione e utilizzare la funzione espressione MAX () per ottenere il valore massimo precedente:

![File di chiave surrogata](media/data-flow/sk008.png "File di chiave surrogata")

In entrambi i casi, è necessario aggiungere i nuovi dati in ingresso insieme all'origine che contiene il valore max precedente:

![Join chiave surrogata](media/data-flow/sk004.png "Join chiave surrogata")

## <a name="next-steps"></a>Passaggi successivi

In questi esempi vengono utilizzate le trasformazioni [join](data-flow-join.md) e [colonna derivata](data-flow-derived-column.md) .
