---
title: Criteri della colonna del flusso di dati di mapping di Azure Data Factory
description: I criteri della colonna del flusso di dati di mapping di Azure Data Factory vengono usati per creare criteri modello generalizzati per trasformare i campi in un flusso di dati senza considerare i metadati dello schema sottostante
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: dd397259ebcd6afe34a47a6f853d826a845a1244
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/13/2019
ms.locfileid: "56212658"
---
# <a name="azure-data-factory-mapping-data-flow-concepts"></a>Concetti del flusso di dati di mapping di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Diverse trasformazioni del flusso di dati di Azure Data Factory supportano il concetto di "Criteri delle colonne" in modo che sia possibile creare colonne modello basate su criteri anziché su nomi di colonna impostati come hardcoded. È possibile usare questa funzionalità all'interno del Generatore di espressioni per definire i criteri da associare alle colonne per la trasformazione invece di richiedere nomi di campo xact specifici. I criteri sono utili quando i campi sorgente in arrivo cambiano spesso, in particolare nel caso di cambio colonna nei file di testo o nei database NoSQL. Ciò è talvolta detto "Deviazione schema".

![criteri della colonna](media/data-flow/columnpattern2.png "Criteri della colonna")

I criteri della colonna sono utili per gestire sia gli scenari Deviazione schema sia gli scenari generici. Sono consigliabili per le condizioni in cui non è possibile conoscere tutti i nomi della colonna. È possibile creare criteri di corrispondenza sul nome della colonna e sul tipo di dati della colonna, che eseguirà l'operazione rispetto qualsiasi campo nel flusso di dati che corrisponde ai propri criteri `name` & `type`.

Quando si aggiunge un'espressione a una trasformazione che accetta i criteri, scegliere "Add Column Pattern" (Aggiungi un criterio della colonna). I criteri della colonna consentono di abbinare la colonna delle deviazioni schema ai criteri.

Durante la creazione dei criteri della colonna, usare `$$` nell'espressione per rappresentare un riferimento a ogni campo corrispondente da parte del flusso di dati di input.

Se si sceglie di usare una delle funzioni regex del Generatore di espressioni, successivamente sarà possibile usare $1, $2, $2, $3 ... per fare riferimento ai sottomodelli corrispondenti alla propria espressione regex.

Un esempio di scenario di criteri della colonna è l'uso di SUM con una serie di campi in ingresso. I calcoli di aggregazione SUM si trovano in Trasformazione aggregazione. È possibile quindi usare SUM per i valori in tutte le occorrenze dei tipi di campo che corrispondano a "integer" e quindi usare $$ per ogni corrispondenza nell'espressione di riferimento.
