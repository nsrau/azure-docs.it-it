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
ms.openlocfilehash: 5cc8a4508ceeda245fbc10a81e16f3ecf05284c7
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95013622"
---
# <a name="detect-adult-content"></a>Rileva contenuto per adulti

Visione artificiale possibile rilevare il materiale per adulti nelle immagini in modo che gli sviluppatori possano limitare la visualizzazione di queste immagini nel software. Vengono applicati flag di contenuto con un punteggio compreso tra 0 e 1, in modo che gli sviluppatori possano interpretare i risultati in base alle proprie preferenze.

> [!NOTE]
> Gran parte di questa funzionalità è offerta dal servizio [content moderator di Azure](../content-moderator/overview.md) . Vedere questa alternativa per soluzioni per scenari più rigorosi di moderazione del contenuto, ad esempio per flussi di lavoro di moderazione del testo e di revisione umana.

## <a name="content-flag-definitions"></a>Definizioni dei flag di contenuto

All'interno della classificazione "Adult" sono disponibili diverse categorie:

- Le immagini **adulte** sono definite in modo esplicito per natura sessuale e spesso rappresentano la nudità e gli atti sessuali.
- Le immagini per la **democrazia** sono definite come immagini che hanno una natura sessuale e spesso contengono contenuto esplicitamente meno sessuale rispetto alle immagini contrassegnate come **adulte**.
- Le immagini **cruente** sono definite come quelle che descrivono Gore.

## <a name="use-the-api"></a>Usare l'API

È possibile rilevare il contenuto per adulti con l'API [analizza immagine](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Quando si aggiunge il valore di `Adult` al parametro di query **visualFeatures** , l'API restituisce tre proprietà booleane &mdash; `isAdultContent` , `isRacyContent` e `isGoryContent` &mdash; nella relativa risposta JSON. Il metodo restituisce anche le proprietà corrispondenti &mdash; `adultScore` , `racyScore` e `goreScore` &mdash; che rappresentano i punteggi di confidenza tra zero e uno per ogni rispettiva categoria.

- [Guida introduttiva: analizzare un'immagine (.NET SDK)](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
- [Guida introduttiva: analizzare un'immagine (API REST)](./quickstarts/csharp-analyze.md)