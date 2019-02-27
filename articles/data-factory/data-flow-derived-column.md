---
title: Trasformazione Colonna derivata del flusso di dati di Azure Data Factory
description: Trasformazione Colonna derivata del flusso di dati di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/08/2018
ms.openlocfilehash: aee005aed52563e214e24148af2563fd7869de76
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271182"
---
# <a name="azure-data-factory-data-flow-derived-column-transformation"></a>Trasformazione Colonna derivata del flusso di dati di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Usare la trasformazione Colonna derivata per generare nuove colonne nel flusso di dati o per modificare campi esistenti.

![Derivare colonne](media/data-flow/dc1.png "Colonna derivata")

È possibile eseguire più azioni Colonna derivata in una singola trasformazione Colonna derivata. Fare clic su "Add column" (Aggiungi colonna) per trasformare più di 1 colonna nel singolo passaggio di trasformazione.

Nel campo Column (Colonna) selezionare una colonna esistente per sovrascrivere con un nuovo valore derivato o fare clic su "Create New Column" (Crea nuova colonna) per generare una nuova colonna con il nuovo valore derivato.

Dalla casella di testo Expression (Espressione) verrà aperto il generatore di espressioni in cui è possibile compilare l'espressione per le colonne derivate usando funzioni di espressione.
