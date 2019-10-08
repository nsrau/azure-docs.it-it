---
title: Criteri della colonna del flusso di dati di mapping di Azure Data Factory
description: Creare modelli di trasformazione dei dati generalizzati usando Azure Data Factory modelli di colonna nel mapping dei flussi di dati
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 535656f315f65ffb7aa241618fe9e73b8246b71f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027856"
---
# <a name="mapping-data-flows-column-patterns"></a>Mapping dei flussi di dati modelli di colonna



Diverse trasformazioni del flusso di dati di Azure Data Factory supportano il concetto di "Criteri delle colonne" in modo che sia possibile creare colonne modello basate su criteri anziché su nomi di colonna impostati come hardcoded. Questa funzionalità può essere utilizzata all'interno del generatore di espressioni per definire i modelli corrispondenti alle colonne per la trasformazione, anziché richiedere nomi di campo esatti e specifici. I modelli sono utili se i campi di origine in ingresso cambiano spesso, in particolare in caso di modifica delle colonne nei file di testo o nei database NoSQL. Questa condizione viene a volte definita "Drift schema".

Questa gestione "Schema flessibile" è attualmente disponibile nella colonna derivata e nelle trasformazioni di aggregazione, nonché nelle trasformazioni selezione e sink come "mapping basato su regole".

![criteri della colonna](media/data-flow/columnpattern2.png "Criteri della colonna")

## <a name="column-patterns"></a>Criteri delle colonne
I criteri della colonna sono utili per gestire sia gli scenari Deviazione schema sia gli scenari generici. Sono consigliabili per le condizioni in cui non è possibile conoscere tutti i nomi della colonna. È possibile creare criteri di corrispondenza sul nome della colonna e sul tipo di dati della colonna, che eseguirà l'operazione rispetto qualsiasi campo nel flusso di dati che corrisponde ai propri criteri `name` & `type`.

Quando si aggiunge un'espressione a una trasformazione che accetta i criteri, scegliere "Add Column Pattern" (Aggiungi un criterio della colonna). I criteri della colonna consentono di abbinare la colonna delle deviazioni schema ai criteri.

Durante la creazione dei criteri della colonna, usare `$$` nell'espressione per rappresentare un riferimento a ogni campo corrispondente da parte del flusso di dati di input.

Se si sceglie di usare una delle funzioni regex del Generatore di espressioni, successivamente sarà possibile usare $1, $2, $2, $3 ... per fare riferimento ai sottomodelli corrispondenti alla propria espressione regex.

Un esempio di scenario di criteri della colonna è l'uso di SUM con una serie di campi in ingresso. I calcoli di aggregazione SUM si trovano in Trasformazione aggregazione. È quindi possibile usare SUM per ogni corrispondenza dei tipi di campo che corrispondono a "Integer" e quindi usare $ $ per fare riferimento a ogni corrispondenza nell'espressione.

## <a name="match-columns"></a>Corrisponde a colonne
![](media/data-flow/pattern2.png "tipi di criteri") di ricerca colonna

Per compilare modelli basati su colonne, è possibile trovare la corrispondenza con il nome della colonna, il tipo, il flusso o la posizione e usare qualsiasi combinazione di quelli con le funzioni di espressione e le espressioni regolari.

posizione(media/data-flow/position.png "colonna") ![posizione]colonna

## <a name="rule-based-mapping"></a>Mapping basato su regole
Quando si esegue il mapping delle colonne nell'origine e si selezionano le trasformazioni, sarà possibile scegliere "mapping fisso" o "mapping basato su regole". Quando si conosce lo schema dei dati e si prevede che le colonne specifiche del set di dati di origine corrispondano sempre a nomi statici specifici, è possibile usare il mapping fisso. Tuttavia, quando si utilizzano schemi flessibili, utilizzare il mapping basato sulle regole. Sarà possibile creare una corrispondenza dei modelli usando le regole descritte in precedenza.

(media/data-flow/rule2.png "mapping basato su regole") di ![mapping basato su regole]

Compilare le regole usando il generatore di espressioni. Le espressioni restituiranno un valore booleano per trovare la corrispondenza con le colonne (true) o escludere le colonne (false).

## <a name="pattern-matching-special-columns"></a>Colonne speciali di criteri di ricerca

* `$$` convertirà il nome di ogni corrispondenza in fase di progettazione in modalità di debug e al momento dell'esecuzione in fase di esecuzione
* `name` rappresenta il nome di ogni colonna in ingresso
* `type` rappresenta il tipo di dati di ogni colonna in ingresso
* `stream` rappresenta il nome associato a ogni flusso o trasformazione nel flusso
* `position` è la posizione ordinale delle colonne nel flusso di dati

## <a name="next-steps"></a>Passaggi successivi
* Altre informazioni sul [linguaggio delle espressioni](https://aka.ms/dataflowexpressions) del flusso di dati del mapping di ADF per le trasformazioni dei dati
* Usare i modelli di colonna nella [trasformazione sink](data-flow-sink.md) e [selezionare trasformazione](data-flow-select.md) con mapping basato su regole
