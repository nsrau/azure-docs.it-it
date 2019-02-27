---
title: Trasformazione Suddivisione condizionale per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Suddivisione condizionale per il flusso di dati di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 9fe650465160ab837d8c8c191887d0f952976682
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271176"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Trasformazione Suddivisione condizionale per il flusso di dati di mapping di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione Suddivisione condizionale può indirizzare le righe di dati a flussi diversi a seconda del contenuto dei dati. L'implementazione della trasformazione Suddivisione condizionale è simile a una struttura decisionale CASE in un linguaggio di programmazione. La trasformazione valuta le espressioni e, in base ai risultati, indirizza la riga di dati al flusso specificato. Questa trasformazione prevede inoltre un output predefinito, in modo che se una riga non corrisponde ad alcuna espressione venga indirizzata all'output predefinito.

![suddivisione condizionale](media/data-flow/cd1.png "suddivisione condizionale")

Per aggiungere altre condizioni, selezionare "Add Stream" (Aggiungi flusso) nel riquadro di configurazione in basso e fare clic nella casella di testo del generatore di espressioni per creare l'espressione.
