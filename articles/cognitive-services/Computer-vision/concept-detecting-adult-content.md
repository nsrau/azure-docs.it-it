---
title: Contenuti per adulti, audace e cruenti Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi al rilevamento di contenuto per adulti nelle immagini tramite l'APi Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ee18916a59bb081d65494f46e7aba7c29c7177cc
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718523"
---
# <a name="detect-adult-content"></a>Rileva contenuto per adulti

Visione artificiale possibile rilevare il materiale per adulti nelle immagini in modo che gli sviluppatori possano limitare la visualizzazione di queste immagini nel software. Vengono applicati flag di contenuto con un punteggio compreso tra 0 e 1, in modo che gli sviluppatori possano interpretare i risultati in base alle proprie preferenze.

> [!NOTE]
> Gran parte di questa funzionalità è offerta dal servizio [content moderator di Azure](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) . Vedere questa alternativa per soluzioni per scenari più rigorosi di moderazione del contenuto, ad esempio per flussi di lavoro di moderazione del testo e di revisione umana.

## <a name="content-flag-definitions"></a>Definizioni dei flag di contenuto

All'interno della classificazione "Adult" sono disponibili diverse categorie:

- Le immagini **adulte** sono definite in modo esplicito per natura sessuale e spesso rappresentano la nudità e gli atti sessuali.
- Le immagini **audaci** sono immagini di natura sessualmente allusiva in cui sono presenti contenuti a sfondo sessuale meno espliciti rispetto alle immagini contrassegnate come per **adulti**.
- Le immagini **cruente** sono definite come quelle che descrivono Gore.

## <a name="use-the-api"></a>Usare l'API

È possibile rilevare il contenuto per adulti con l'API [analizza immagine](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) . Quando si aggiunge il valore di `Adult` al parametro di query **visualFeatures** , l'API restituisce tre proprietà booleane&mdash;`isAdultContent`, `isRacyContent`e `isGoryContent`&mdash;nella risposta JSON. Il metodo restituisce anche proprietà corrispondenti&mdash;`adultScore`, `racyScore`e `goreScore`&mdash;che rappresentano i punteggi di confidenza tra zero e uno per ogni categoria corrispondente.

- [Guida introduttiva: analizzare un'immagine (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Guida introduttiva: analizzare un'immagine (API REST)](./quickstarts/csharp-analyze.md)
