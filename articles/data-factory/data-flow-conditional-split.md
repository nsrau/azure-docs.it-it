---
title: Trasformazione Suddivisione condizionale per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Suddivisione condizionale per il flusso di dati di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: a4ea79e05165dfae4f79aa6473a07151ba7c00fc
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60627631"
---
# <a name="azure-data-factory-mapping-data-flow-conditional-split-transformation"></a>Trasformazione Suddivisione condizionale per il flusso di dati di mapping di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione Suddivisione condizionale può indirizzare le righe di dati a flussi diversi a seconda del contenuto dei dati. L'implementazione della trasformazione Suddivisione condizionale è simile a una struttura decisionale CASE in un linguaggio di programmazione. La trasformazione valuta le espressioni e, in base ai risultati, indirizza la riga di dati al flusso specificato. Questa trasformazione prevede inoltre un output predefinito, in modo che se una riga non corrisponde ad alcuna espressione venga indirizzata all'output predefinito.

![suddivisione condizionale](media/data-flow/cd1.png "suddivisione condizionale")

Per aggiungere altre condizioni, selezionare "Add Stream" (Aggiungi flusso) nel riquadro di configurazione in basso e fare clic nella casella di testo del generatore di espressioni per creare l'espressione.
