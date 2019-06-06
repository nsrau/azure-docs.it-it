---
title: Criteri della colonna del flusso di dati di mapping di Azure Data Factory
description: Informazioni su come usare i modelli di Azure Data Factory di colonna nel flusso di dati di Mapping per creare modelli di modello generalizzato per la trasformazione dei campi in un flusso di dati senza considerare i metadati dello schema sottostante
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 08cdaafe00b7dc586ea75f6ff03fdb89107edee9
ms.sourcegitcommit: 087ee51483b7180f9e897431e83f37b08ec890ae
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/31/2019
ms.locfileid: "66430764"
---
# <a name="azure-data-factory-mapping-data-flows-column-patterns"></a>Modelli di colonna di flussi di dati di mapping di Azure data factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Diverse trasformazioni del flusso di dati di Azure Data Factory supportano il concetto di "Criteri delle colonne" in modo che sia possibile creare colonne modello basate su criteri anziché su nomi di colonna impostati come hardcoded. È possibile usare questa funzionalità all'interno del generatore di espressioni per definire i criteri per associare le colonne per la trasformazione invece di richiedere i nomi dei campi di specifica, esatto. I modelli sono utili se i campi di origine in ingresso cambiano spesso, in particolare nel caso di modifica delle colonne nel file di testo o i database NoSQL. Questa condizione è talvolta detta "Deviazione Schema".

![criteri della colonna](media/data-flow/columnpattern2.png "Criteri della colonna")

I criteri della colonna sono utili per gestire sia gli scenari Deviazione schema sia gli scenari generici. Sono consigliabili per le condizioni in cui non è possibile conoscere tutti i nomi della colonna. È possibile creare criteri di corrispondenza sul nome della colonna e sul tipo di dati della colonna, che eseguirà l'operazione rispetto qualsiasi campo nel flusso di dati che corrisponde ai propri criteri `name` & `type`.

Quando si aggiunge un'espressione a una trasformazione che accetta i criteri, scegliere "Add Column Pattern" (Aggiungi un criterio della colonna). I criteri della colonna consentono di abbinare la colonna delle deviazioni schema ai criteri.

Durante la creazione dei criteri della colonna, usare `$$` nell'espressione per rappresentare un riferimento a ogni campo corrispondente da parte del flusso di dati di input.

Se si sceglie di usare una delle funzioni di espressione regolare generatore di espressioni, è possibile quindi successivamente usare 1 $, $2, 3 dollari... Per fare riferimento a trovare un corrispondenza espressione regex sottomodelli.

Un esempio di scenario di criteri della colonna è l'uso di SUM con una serie di campi in ingresso. I calcoli di aggregazione SUM si trovano in Trasformazione aggregazione. È quindi possibile usare SUM su tutte le occorrenze dei tipi di campo che corrispondono a "integer" e quindi usare $$ per ogni corrispondenza nell'espressione di riferimento.
