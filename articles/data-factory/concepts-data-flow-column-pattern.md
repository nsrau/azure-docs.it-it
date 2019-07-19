---
title: Criteri della colonna del flusso di dati di mapping di Azure Data Factory
description: Creare modelli di trasformazione dei dati generalizzati usando Azure Data Factory modelli di colonna nel mapping dei flussi di dati
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: d24988dfd5cbaf20e92c5afbbc39dc0c78e3ef6a
ms.sourcegitcommit: da0a8676b3c5283fddcd94cdd9044c3b99815046
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/18/2019
ms.locfileid: "68314891"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Modelli di colonna di flussi di dati di Azure data factory mapping

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Diverse trasformazioni del flusso di dati di Azure Data Factory supportano il concetto di "Criteri delle colonne" in modo che sia possibile creare colonne modello basate su criteri anziché su nomi di colonna impostati come hardcoded. Questa funzionalità può essere utilizzata all'interno del generatore di espressioni per definire i modelli corrispondenti alle colonne per la trasformazione, anziché richiedere nomi di campo esatti e specifici. I modelli sono utili se i campi di origine in ingresso cambiano spesso, in particolare in caso di modifica delle colonne nei file di testo o nei database NoSQL. Questa condizione viene a volte definita "Drift schema".

![criteri della colonna](media/data-flow/columnpattern2.png "Criteri della colonna")

I criteri della colonna sono utili per gestire sia gli scenari Deviazione schema sia gli scenari generici. Sono consigliabili per le condizioni in cui non è possibile conoscere tutti i nomi della colonna. È possibile creare criteri di corrispondenza sul nome della colonna e sul tipo di dati della colonna, che eseguirà l'operazione rispetto qualsiasi campo nel flusso di dati che corrisponde ai propri criteri `name` & `type`.

Quando si aggiunge un'espressione a una trasformazione che accetta i criteri, scegliere "Add Column Pattern" (Aggiungi un criterio della colonna). I criteri della colonna consentono di abbinare la colonna delle deviazioni schema ai criteri.

Durante la creazione dei criteri della colonna, usare `$$` nell'espressione per rappresentare un riferimento a ogni campo corrispondente da parte del flusso di dati di input.

Se si sceglie di usare una delle funzioni regex del Generatore di espressioni, successivamente sarà possibile usare $1, $2, $2, $3 ... per fare riferimento ai sottomodelli corrispondenti alla propria espressione regex.

Un esempio di scenario di criteri della colonna è l'uso di SUM con una serie di campi in ingresso. I calcoli di aggregazione SUM si trovano in Trasformazione aggregazione. È quindi possibile usare SUM per ogni corrispondenza dei tipi di campo che corrispondono a "Integer" e quindi usare $ $ per fare riferimento a ogni corrispondenza nell'espressione.

## <a name="match-columns"></a>Corrisponde a colonne
![tipi di criteri di colonna](media/data-flow/pattern2.png "Tipi di modello")

Per compilare modelli basati su colonne, è possibile trovare la corrispondenza con il nome della colonna, il tipo, il flusso o la posizione e usare qualsiasi combinazione di quelli con le funzioni di espressione e le espressioni regolari.

![posizione colonna](media/data-flow/position.png "Posizione colonna")

## <a name="next-steps"></a>Passaggi successivi
Altre informazioni sul [linguaggio delle espressioni](http://aka.ms/dataflowexpressions) del flusso di dati del mapping di ADF per le trasformazioni dei dati
