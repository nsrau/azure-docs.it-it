---
title: Trasformazione colonna nel Mapping di flusso di dati - Azure Data Factory derivata | Microsoft Docs
description: Informazioni su come trasformare i dati su larga scala in Azure Data Factory con il Mapping dei dati del flusso trasformazione colonna derivata.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: 941c629fd8359edc7fc1cf364a6735314044d95e
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/21/2019
ms.locfileid: "67312212"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>Trasformazione colonna derivata nel Mapping di flusso di dati

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Usare la trasformazione Colonna derivata per generare nuove colonne nel flusso di dati o per modificare campi esistenti.

## <a name="derived-column-settings"></a>Impostazioni di colonna derivate

Per ignorare una colonna esistente, selezionarlo tramite l'elenco a discesa di colonne. In caso contrario, usare il campo di selezione della colonna come una casella di testo e immettere il nome della nuova colonna. Per compilare l'espressione della colonna derivata, selezionare la casella "Immettere l'espressione" per aprire la [generatore di espressioni del flusso di dati](concepts-data-flow-expression-builder.md).

![Impostazioni di colonna derivato](media/data-flow/dc1.png "derivato impostazioni colonna")

Per aggiungere ulteriori colonne derivate, passare il mouse su un oggetto esistente deriva colonna e fare clic su '+'. Quindi, scegliere 'Aggiungi colonna' o 'Aggiungi criteri di ricerca colonna'. Modelli di colonna possono rivelarsi utili se i nomi di colonna sono variabili dalle origini. Per altre informazioni, vedere [modelli di colonna](concepts-data-flow-column-pattern.md).

![Nuovo derivato di selezione della colonna](media/data-flow/columnpattern.png "nuovo derivato di selezione delle colonne")

## <a name="next-steps"></a>Passaggi successivi

- Altre informazioni sul [Mapping di flusso di dati linguaggio delle espressioni](data-flow-expression-functions.md).
