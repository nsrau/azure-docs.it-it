---
title: Panoramica della trasformazione del flusso di dati del mappingMapping data flow transformation overview
description: Panoramica delle diverse trasformazioni disponibili nel mapping del flusso di dati
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 94f7277c1b741fb5ef6d2807fabbb266c0109415
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79086932"
---
# <a name="mapping-data-flow-transformation-overview"></a>Panoramica della trasformazione del flusso di dati del mappingMapping data flow transformation overview

Di seguito è riportato un elenco delle trasformazioni attualmente supportate nel mapping del flusso di dati. Fare clic su ogni trasformazione per scoprirne i dettagli di configurazione.

| Nome | Category | Descrizione |
| ---- | -------- | ----------- |
| [Aggregazione](data-flow-aggregate.md) | Modificatore dello schema | Definire diversi tipi di aggregazioni, ad esempio SUM, MIN, MAX e COUNT raggruppati in base a colonne esistenti o calcolate. | 
| [Alter Row](data-flow-alter-row.md) | Modificatore di riga | Impostare i criteri di inserimento, eliminazione, aggiornamento e backup nelle righe. |
| [Divisione condizionale](data-flow-conditional-split.md) | Ingressi/uscite multipli | Instradare righe di dati a flussi diversi in base alle condizioni corrispondenti. |
| [Colonna derivata](data-flow-derived-column.md) | Modificatore dello schema | generare nuove colonne o modificare i campi esistenti utilizzando il linguaggio delle espressioni del flusso di dati. | 
| [Exists](data-flow-exists.md) | Ingressi/uscite multipli | Verificare se i dati sono presenti in un'altra origine o flusso. | 
| [Filtro](data-flow-filter.md) | Modificatore di riga | Filtrare una riga in base a una condizione. |
| [Appiattire](data-flow-flatten.md) | Modificatore dello schema |  Inserire i valori di matrice all'interno di strutture gerarchiche come JSON e srotolarli in singole righe. |
| [Aggiungi](data-flow-join.md) | Ingressi/uscite multipli |  Combinare i dati da due origini o flussi. |
| [Ricerca](data-flow-lookup.md) | Ingressi/uscite multipli | Fare riferimento ai dati da un'altra origine. |
| [Nuovo ramo](data-flow-new-branch.md) | Ingressi/uscite multipli | Applicare più set di operazioni e trasformazioni allo stesso flusso di dati. |
| [Perno](data-flow-pivot.md) | Modificatore dello schema | Aggregazione in cui una o più colonne di raggruppamento hanno i valori di riga distinti trasformati in singole colonne. |
| [Seleziona](data-flow-select.md) | Modificatore dello schema | Colonne alias e nomi di flusso ed eliminare o riordinare le colonne |
| [Sink](data-flow-sink.md) | - | Una destinazione finale per i tuoi dati |
| [Sorta](data-flow-sort.md) | Modificatore di riga | Ordinare le righe in ingresso nel flusso di dati corrente |
| [origine](data-flow-source.md) | - | Un'origine dati per il flusso di dati |
| [Chiave surrogata](data-flow-surrogate-key.md) | Modificatore dello schema | Aggiungere un valore di chiave arbitraria non aziendale crescenteAdd an incrementing non-business arbitrary key value |
| [Union](data-flow-union.md) | Ingressi/uscite multipli | Combinare più flussi di dati verticalmente |
| [Unpivot](data-flow-unpivot.md) | Modificatore dello schema | Ruotare le colonne in valori di riga |
| [Finestra](data-flow-window.md) | Modificatore dello schema |  Definire le aggregazioni di colonne basate su finestra nei flussi di dati. |
