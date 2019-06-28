---
title: Nel Mapping di flusso di dati - Azure Data Factory trasformazione aggregazione | Microsoft Docs
description: Informazioni su come aggregare i dati su larga scala in Azure Data Factory con il Mapping dei dati del flusso trasformazione aggregazione.
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 21135b26d4bc840b3fcb091e675e5e6bd24d8548
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312101"
---
# <a name="aggregate-transformation-in-mapping-data-flow"></a>Trasformazione aggregazione nel flusso di dati di Mapping 

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione Aggregazione è la posizione in cui verranno definite le aggregazioni delle colonne nei flussi di dati. Usa il generatore di espressioni, è possibile definire diversi tipi di aggregazioni, ad esempio SUM, MIN, MAX e COUNT che possono essere raggruppati per colonne calcolate o esistente.

## <a name="group-by"></a>Raggruppa per
Selezionare una colonna esistente o crearne una nuova colonna calcolata da utilizzare come un gruppo dalla clausola per l'aggregazione. Per utilizzare una colonna esistente, selezionare la colonna desiderata dall'elenco a discesa. Per creare una nuova colonna calcolata, la clausola del mouse e fare clic su 'Colonna calcolata'. Si apre la [generatore di espressioni del flusso di dati](concepts-data-flow-expression-builder.md). Dopo aver creato la colonna calcolata, immettere il nome della colonna di output sotto il campo 'Nome'. Se si vuole aggiungere un gruppo aggiuntivo dalla clausola, passare il mouse su una clausola esistente e fare clic su '+'.

![Impostazioni di raggruppamento trasformazione aggregazione](media/data-flow/agg.png "Raggruppa trasformazione aggregazione per le impostazioni")

> [!NOTE]
> Una clausola group by è facoltativo in una trasformazione aggregazione.

## <a name="aggregate-column"></a>Colonna di aggregazione 
Scegliere la scheda 'Aggregazioni' per la compilazione di espressioni di aggregazione. È possibile selezionare una colonna esistente e sovrascrivere il valore con l'aggregazione o creare un nuovo campo con un nuovo nome. L'espressione di aggregazione viene immesso nella casella a destra accanto al selettore di nome di colonna. Per modificare l'espressione, fare clic sulla casella di testo per aprire il generatore di espressioni. Per aggiungere un'aggregazione aggiuntiva, passare il mouse su un'espressione esistente e fare clic su '+' per creare una nuova colonna di aggregazione o [criteri di ricerca colonna](concepts-data-flow-column-pattern.md).

![Impostazioni aggregate trasformazione aggregazione](media/data-flow/agg2.png "impostazioni aggregazione trasformazione aggregazione")

> [!NOTE]
> Ogni espressione di aggregazione deve contenere almeno una funzione di aggregazione.

> [!NOTE]
> In modalità Debug, il generatore di espressioni non può produrre le anteprime dei dati con funzioni di aggregazione. Per visualizzare le anteprime dei dati per le trasformazioni aggregazione, chiudere il generatore di espressioni e visualizzare i dati tramite la scheda 'Anteprima dei dati'.

## <a name="next-steps"></a>Passaggi successivi

: Consente di definire aggregazioni basate su finestra usando il [trasformazione finestra](data-flow-window.md)