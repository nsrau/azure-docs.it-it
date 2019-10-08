---
title: Trasformazione colonna derivata nel flusso di dati di mapping-Azure Data Factory | Microsoft Docs
description: Informazioni su come trasformare i dati su larga scala in Azure Data Factory con la trasformazione colonna derivata flusso di dati mapping.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aacd6f1799f1813e168bd04e78f18cf60ad5243f
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72026844"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Trasformazione colonna derivata nel flusso di dati di mapping

Usare la trasformazione Colonna derivata per generare nuove colonne nel flusso di dati o per modificare campi esistenti.

## <a name="derived-column-settings"></a>Impostazioni colonna derivata

Per eseguire l'override di una colonna esistente, selezionarla tramite l'elenco a discesa colonna. In caso contrario, usare il campo di selezione della colonna come casella di testo e digitare il nome della nuova colonna. Per compilare l'espressione della colonna derivata, fare clic sulla casella "immettere l'espressione" per aprire il [Generatore di espressioni del flusso di dati](concepts-data-flow-expression-builder.md).

Impostazioni ![colonna derivata impostazioni]colonna(media/data-flow/dc1.png "derivata")

Per aggiungere altre colonne derivate, passare il mouse su una colonna derivata esistente e fare clic su' +'. Scegliere quindi ' Aggiungi colonna ' o ' Aggiungi modello di colonna '. I modelli di colonna possono essere utili se i nomi delle colonne sono variabili dalle origini. Per ulteriori informazioni, vedere [modelli di colonna](concepts-data-flow-column-pattern.md).

Nuova selezione ![colonna derivata](media/data-flow/columnpattern.png "nuova selezione colonna derivata")

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [linguaggio delle espressioni del flusso di dati di mapping](data-flow-expression-functions.md).
