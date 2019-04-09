---
title: Trasformazione Esiste per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Esiste per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 6ce27ba699ae766ed4d2428f67d91379464bb9f1
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006148"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Trasformazione Esiste per il flusso di dati di mapping di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione Esiste è una trasformazione per filtrare le righe che consente o impedisce il passaggio avanti nel flusso a righe specifiche nei dati. La trasformazione Esiste è simile a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```. Dopo la trasformazione Filtro, le righe risultanti dal flusso di dati includeranno tutte le righe in cui i valori di colonna dall'origine 1 esistono nell'origine 2 oppure non esistono nell'origine 2.

![Impostazioni di Esiste](media/data-flow/exsits.png "Esiste 1")

Scegliere la seconda origine per la trasformazione Esiste in modo che il flusso di dati possa confrontare i valori da flusso 1 con quelli in flusso 2.

Selezionare la colonna dall'origine 1 e dall'origine 2 per cui si vuole eseguire un controllo di esistenza dei valori.

## <a name="multiple-exists-conditions"></a>Le condizioni di esiste più

Accanto a ogni riga nelle proprie condizioni di colonna per disponibile, si noterà un + sign disponibili quando passa il mouse sulla riga raggiungere. In questo modo sarà possibile aggiungere più righe per le condizioni di Exists.

## <a name="next-steps"></a>Passaggi successivi

