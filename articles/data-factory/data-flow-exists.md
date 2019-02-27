---
title: Trasformazione Esiste per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Esiste per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/30/2019
ms.openlocfilehash: 9d21b304f55ec746da4b7b42194fe0d168261b53
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271272"
---
# <a name="azure-data-factory-mapping-data-flow-exists-transformation"></a>Trasformazione Esiste per il flusso di dati di mapping di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

La trasformazione Esiste è una trasformazione per filtrare le righe che consente o impedisce il passaggio avanti nel flusso a righe specifiche nei dati. La trasformazione Esiste è simile a ```SQL WHERE EXISTS``` e ```SQL WHERE NOT EXISTS```. Dopo la trasformazione Filtro, le righe risultanti dal flusso di dati includeranno tutte le righe in cui i valori di colonna dall'origine 1 esistono nell'origine 2 oppure non esistono nell'origine 2.

![Impostazioni di Esiste](media/data-flow/exsits.png "Esiste 1")

Scegliere la seconda origine per la trasformazione Esiste in modo che il flusso di dati possa confrontare i valori da flusso 1 con quelli in flusso 2.

Selezionare la colonna dall'origine 1 e dall'origine 2 per cui si vuole eseguire un controllo di esistenza dei valori.
