---
title: Trasformazione Chiave surrogata per il flusso di dati di mapping di Azure Data Factory
description: Come usare Mapping di trasformazione di Azure Data Factory dei dati del flusso surrogato chiave per generare valori di chiave sequenziali
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/12/2019
ms.openlocfilehash: eaa1c577f7e208400d3430222b006e0dbbd7956a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61350630"
---
# <a name="mapping-data-flow-surrogate-key-transformation"></a>Mapping di trasformazione del chiave surrogata flusso di dati

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Usare la trasformazione Chiave surrogata per aggiungere un valore di chiave arbitrario non business incrementale al set di righe del flusso di dati. Ciò è utile quando si progettano le tabelle delle dimensioni in un modello di dati analitici con schema star in cui ogni membro nelle tabelle delle dimensioni deve avere una chiave univoca non business, parte della metodologia di data warehousing Kimball.

![Trasformazione Chiave surrogata](media/data-flow/surrogate.png "Trasformazione Chiave surrogata")

"Key Column" (Colonna chiave) è il nome che verrà assegnato alla nuova colonna chiave surrogata.

"Start Value" (Valore iniziale) è il punto di inizio del valore incrementale.

## <a name="increment-keys-from-existing-sources"></a>Chiavi di incremento da origini esistenti

Se si desidera avviare la sequenza da un valore esistente in un'origine, è possibile utilizzare una trasformazione colonna derivata subito dopo la trasformazione chiave surrogata e sommare i due valori:

![SK aggiungere Max](media/data-flow/sk006.png "surrogato chiave trasformazione aggiungere Max")

Per inizializzare il valore della chiave con il numero massimo precedente, esistono due tecniche che è possibile usare:

### <a name="database-sources"></a>Origini di database

Usare l'opzione "Query" per selezionare max () dell'origine mediante la trasformazione origine:

![Query principali di surrogati](media/data-flow/sk002.png "chiave trasformazione Query di surrogati")

### <a name="file-sources"></a>Origini file

Se il valore massimo precedente è in un file, è possibile utilizzare la trasformazione di origine insieme a una trasformazione aggregazione e usare la funzione di espressione max () per ottenere il valore massimo precedente:

![File di chiave di surrogati](media/data-flow/sk008.png "surrogati File di chiave")

In entrambi i casi, è necessario aggiungere i nuovi dati in ingresso con l'origine che contiene il valore massimo precedente:

![Join con chiave di surrogati](media/data-flow/sk004.png "surrogati Join con chiave")

## <a name="next-steps"></a>Passaggi successivi

Questi esempi usano il [partecipare](data-flow-join.md) e [colonna derivata](data-flow-derived-column.md) trasformazioni.
