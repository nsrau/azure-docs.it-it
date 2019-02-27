---
title: Trasformazione Ricerca per il flusso di dati di mapping di Azure Data Factory
description: Trasformazione Ricerca per il flusso di dati di mapping di Azure Data Factory
author: kromerm
ms.author: makromer
ms.reviewer: douglasl
ms.service: data-factory
ms.topic: conceptual
ms.date: 02/03/2019
ms.openlocfilehash: 28c8f2b0005641934f7fcd9549f1cf004b206d80
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/14/2019
ms.locfileid: "56271163"
---
# <a name="azure-data-factory-mapping-data-flow-lookup-transformation"></a>Trasformazione Ricerca per il flusso di dati di mapping di Azure Data Factory

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

Usare la trasformazione Ricerca per aggiungere dati di riferimento da un'altra origine al flusso di dati. La trasformazione Ricerca richiede un'origine definita che punta alla tabella di riferimento e corrisponde ai campi chiave.

![Trasformazione Ricerca](media/data-flow/lookup1.png "Ricerca")

Selezionare i campi chiave per individuare la corrispondenza tra i campi del flusso in ingresso e i campi dall'origine di riferimento. È necessario avere già creato una nuova origine nell'area di progettazione del flusso di dati da usare come lato destro per la ricerca.

Quando vengono trovate corrispondenze, le righe e le colonne risultanti dall'origine di riferimento verranno aggiunte al flusso di dati. È possibile scegliere i campi di interesse da includere nel sink alla fine del flusso di dati.
