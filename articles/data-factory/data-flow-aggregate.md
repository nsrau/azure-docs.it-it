---
title: Trasformazione Aggregazione per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Aggregazione per il flusso di dati di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 7b488b243c0520befb6b5470598f460b5a759fed
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61467378"
---
# <a name="azure-data-factory-mapping-data-flow-aggregate-transformation"></a>Trasformazione Aggregazione per il flusso di dati di mapping di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione Aggregazione è la posizione in cui verranno definite le aggregazioni delle colonne nei flussi di dati. Nel generatore di espressioni è possibile definire diversi tipi di aggregazioni (ad esempio, SUM, MIN, MAX, COUNT e così via) e creare un nuovo campo nell'output che include queste aggregazioni con campi di raggruppamento facoltativi.

![Opzioni della trasformazione Aggregazione](media/data-flow/agg.png "Aggregazione 1")

## <a name="group-by"></a>Raggruppa per
(Facoltativo) Scegliere una clausola di raggruppamento per l'aggregazione e usare il nome di una colonna esistente o un nuovo nome. Usare "Add Column" (Aggiungi colonna) e altre clausole di raggruppamento e fare clic nella casella di testo accanto al nome di colonna per avviare il generatore di espressioni per selezionare solo una colonna esistente, una combinazione di colonne o espressioni per il raggruppamento.

## <a name="the-aggregate-column-tab"></a>Scheda Aggregate Column (Colonna di aggregazione) 
(Obbligatorio) Scegliere la scheda Aggregate Column (Colonna di aggregazione) per creare le espressioni di aggregazione. È possibile scegliere una colonna esistente per sovrascrivere il valore con l'aggregazione o creare un nuovo campo con il nuovo nome per l'aggregazione. L'espressione che si vuole usare per l'aggregazione verrà immessa nella casella a destra accanto al selettore di nome di colonna. Fare clic in tale casella di testo per aprire il generatore di espressioni.

![Opzioni della trasformazione Aggregazione](media/data-flow/agg2.png "Aggregatore")

## <a name="data-preview-in-expression-builder"></a>Anteprima dei dati nel generatore di espressioni

In modalità Debug, il generatore di espressioni non può produrre anteprime dei dati con le funzioni di aggregazione. Per visualizzare le anteprime dei dati per le trasformazioni di aggregazione, chiudere il generatore di espressioni e visualizzare il profilo dei dati dalla finestra di progettazione del flusso dei dati.
