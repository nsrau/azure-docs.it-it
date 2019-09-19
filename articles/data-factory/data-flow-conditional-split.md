---
title: Trasformazione Suddivisione condizionale per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Suddivisione condizionale per il flusso di dati di Azure Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: dd51cc2d5f95598154b76b5da8e3fc9e4801100d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71104941"
---
# <a name="mapping-data-flow-conditional-split-transformation"></a>Trasformazione Suddivisione condizionale flusso di dati mapping

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

![casella degli strumenti Suddivisione condizionale](media/data-flow/conditionalsplit2.png "casella degli strumenti Suddivisione condizionale")

La trasformazione Suddivisione condizionale può indirizzare le righe di dati a flussi diversi a seconda del contenuto dei dati. L'implementazione della trasformazione Suddivisione condizionale è simile a una struttura decisionale CASE in un linguaggio di programmazione. La trasformazione valuta le espressioni e, in base ai risultati, indirizza la riga di dati al flusso specificato. Questa trasformazione prevede inoltre un output predefinito, in modo che se una riga non corrisponde ad alcuna espressione venga indirizzata all'output predefinito.

![Suddivisione condizionale](media/data-flow/conditionalsplit1.png "Opzioni di Suddivisione condizionale")

## <a name="multiple-paths"></a>Percorsi multipli

Per aggiungere altre condizioni, selezionare "Add Stream" (Aggiungi flusso) nel riquadro di configurazione in basso e fare clic nella casella di testo del generatore di espressioni per creare l'espressione.

![Suddivisione condizionale multifunzione](media/data-flow/conditionalsplit3.png "Suddivisione condizionale multifunzione")

## <a name="next-steps"></a>Passaggi successivi

Trasformazioni comuni del flusso di dati utilizzate con Suddivisione condizionale: [Trasformazione join](data-flow-join.md), [trasformazione Ricerca](data-flow-lookup.md), [selezione trasformazione](data-flow-select.md)
