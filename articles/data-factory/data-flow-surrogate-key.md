---
title: Trasformazione Chiave surrogata per il flusso di dati di mapping di Azure Data Factory
description: Come utilizzare la trasformazione della chiave surrogata del flusso di dati di Azure Data Factory per generare valori di chiave sequenziale
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: 45e2d35a3b0a3f3c89913bbe70d7c43c17cbcee0
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029182"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Trasformazione della chiave surrogata del flusso di dati mapping



Usare la trasformazione Chiave surrogata per aggiungere un valore di chiave arbitrario non business incrementale al set di righe del flusso di dati. Ciò è utile quando si progettano le tabelle delle dimensioni in un modello di dati analitici con schema star in cui ogni membro nelle tabelle delle dimensioni deve avere una chiave univoca non business, parte della metodologia di data warehousing Kimball.

![Trasformazione Chiave surrogata](media/data-flow/surrogate.png "Trasformazione Chiave surrogata")

"Key Column" (Colonna chiave) è il nome che verrà assegnato alla nuova colonna chiave surrogata.

"Start Value" (Valore iniziale) è il punto di inizio del valore incrementale.

## <a name="increment-keys-from-existing-sources"></a>Incrementa le chiavi da origini esistenti

Se si vuole avviare la sequenza da un valore presente in un'origine, è possibile usare una trasformazione colonna derivata immediatamente dopo la trasformazione chiave surrogata e aggiungere i due valori insieme:

Aggiunta della trasformazione ![massima](media/data-flow/sk006.png "chiave surrogata") SK aggiunta max

Per eseguire il seeding del valore della chiave con il valore max precedente, sono disponibili due tecniche che è possibile usare:

### <a name="database-sources"></a>Origini database

Usare l'opzione "query" per selezionare MAX () dall'origine usando la trasformazione di origine:

(media/data-flow/sk002.png "Query di trasformazione chiave surrogata") ![query chiave]surrogata

### <a name="file-sources"></a>Origini file

Se il valore max precedente si trova in un file, è possibile utilizzare la trasformazione origine insieme a una trasformazione aggregazione e utilizzare la funzione espressione MAX () per ottenere il valore massimo precedente:

File di chiave(media/data-flow/sk008.png "surrogato") del ![file di chiave surrogato]

In entrambi i casi, è necessario aggiungere i nuovi dati in ingresso insieme all'origine che contiene il valore max precedente:

![](media/data-flow/sk004.png "Join") chiave surrogata join chiave surrogata

## <a name="next-steps"></a>Passaggi successivi

In questi esempi vengono utilizzate le trasformazioni [join](data-flow-join.md) e [colonna derivata](data-flow-derived-column.md) .
