---
title: Trasformazione colonna derivata di Mapping di flusso di dati di Azure Data Factory
description: Come trasformare i dati su larga scala con Azure Data Factory di Mapping dei dati del flusso trasformazione colonna derivata
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 6568e5ebf356bb0e6b4ac8ff6059cd093f8da821
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64917571"
---
# <a name="mapping-data-flow-derived-column-transformation"></a>Mapping di flusso di dati di trasformazione colonna derivata

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Usare la trasformazione Colonna derivata per generare nuove colonne nel flusso di dati o per modificare campi esistenti.

![Derivare colonne](media/data-flow/dc1.png "Colonna derivata")

È possibile eseguire più azioni Colonna derivata in una singola trasformazione Colonna derivata. Fare clic su "Add column" (Aggiungi colonna) per trasformare più di 1 colonna nel singolo passaggio di trasformazione.

Nel campo Column (Colonna) selezionare una colonna esistente per sovrascrivere con un nuovo valore derivato o fare clic su "Create New Column" (Crea nuova colonna) per generare una nuova colonna con il nuovo valore derivato.

Dalla casella di testo Expression (Espressione) verrà aperto il generatore di espressioni in cui è possibile compilare l'espressione per le colonne derivate usando funzioni di espressione.

## <a name="column-patterns"></a>Criteri delle colonne

Se i nomi di colonna sono variabili dalle origini, è consigliabile creare trasformazioni all'interno della colonna derivata usando modelli di colonna anziché colonne denominate. Vedere le [deviazione Schema](concepts-data-flow-schema-drift.md) per altri dettagli vedere l'articolo.

![criteri di ricerca colonna](media/data-flow/columnpattern.png "modelli di colonna")

## <a name="next-steps"></a>Passaggi successivi

Altre informazioni sul [linguaggio delle espressioni di Data Factory per le trasformazioni](https://aka.ms/dataflowexpressions) e il [generatore di espressioni](concepts-data-flow-expression-builder.md)
