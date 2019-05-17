---
title: Trasformazione Suddivisione condizionale per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Suddivisione condizionale per il flusso di dati di Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: f9fd346d4c4eaed0797d564fe52dd44e9f0e240a
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/16/2019
ms.locfileid: "65795660"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Dati flusso del mapping condizionale trasformazione Suddivisione

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![Conditional split della casella degli strumenti](media/data-flow/conditionalsplit2.png "conditional split della casella degli strumenti")

La trasformazione Suddivisione condizionale può indirizzare le righe di dati a flussi diversi a seconda del contenuto dei dati. L'implementazione della trasformazione Suddivisione condizionale è simile a una struttura decisionale CASE in un linguaggio di programmazione. La trasformazione valuta le espressioni e, in base ai risultati, indirizza la riga di dati al flusso specificato. Questa trasformazione prevede inoltre un output predefinito, in modo che se una riga non corrisponde ad alcuna espressione venga indirizzata all'output predefinito.

![Suddivisione condizionale](media/data-flow/conditionalsplit1.png "conditional split opzioni")

## <a name="multiple-paths"></a>Percorsi multipli

Per aggiungere altre condizioni, selezionare "Add Stream" (Aggiungi flusso) nel riquadro di configurazione in basso e fare clic nella casella di testo del generatore di espressioni per creare l'espressione.

![Conditional split multi](media/data-flow/conditionalsplit3.png "conditional split multi")

## <a name="next-steps"></a>Passaggi successivi

Trasformazioni del flusso dei dati comuni usate con suddivisione condizionale: [Aggiungere transformation](data-flow-join.md), [trasformazione Loopup](data-flow-lookup.md), [selezionare trasformazione](data-flow-select.md)
