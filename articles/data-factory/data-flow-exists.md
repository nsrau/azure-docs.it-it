---
title: Trasformazione del flusso di dati di mapping Azure Data Factory esistente
description: Come verificare la presenza di righe esistenti utilizzando data factory il mapping di flussi di dati con la trasformazione exists
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 8b488a079b2da1bcf0dd064025ed251a1dc25213
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/08/2019
ms.locfileid: "72029399"
---
# <a name="mapping-data-flow-exists-transformation"></a>Trasformazione del flusso di dati di mapping esistente



La trasformazione Esiste è una trasformazione per filtrare le righe che consente o impedisce il passaggio avanti nel flusso a righe specifiche nei dati. La trasformazione Esiste è simile a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```. Dopo la trasformazione exists, le righe risultanti dal flusso di dati includeranno tutte le righe in cui i valori di colonna dell'origine 1 esistono nell'origine 2 o non esistono nell'origine 2.

![Impostazioni di Esiste](media/data-flow/exists.png "Esiste 1")

Scegliere la seconda origine per la trasformazione Esiste in modo che il flusso di dati possa confrontare i valori da flusso 1 con quelli in flusso 2.

Selezionare la colonna dall'origine 1 e dall'origine 2 per cui si vuole eseguire un controllo di esistenza dei valori.

## <a name="multiple-exists-conditions"></a>Più condizioni exists

Accanto a ogni riga delle condizioni della colonna per EXISTS è disponibile un segno + quando si passa il mouse sulla riga REACH. Ciò consentirà di aggiungere più righe per le condizioni exists. Ogni condizione aggiuntiva è "and".

## <a name="custom-expression"></a>Espressione personalizzata

![Exists Custom Settings](media/data-flow/exists1.png "Exists Custom")

È possibile fare clic su "espressione personalizzata" per creare invece un'espressione in formato libero come condizione EXISTS o not-exists. Se si seleziona questa casella, sarà possibile digitare l'espressione come condizione.

## <a name="next-steps"></a>Passaggi successivi

Le trasformazioni simili sono [ricerca](data-flow-lookup.md) e [join](data-flow-join.md).
