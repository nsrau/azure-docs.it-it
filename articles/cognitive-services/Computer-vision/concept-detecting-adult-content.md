---
title: Contenuti per adulti, audace e cruenti Visione artificiale
titleSuffix: Azure Cognitive Services
description: Concetti relativi al rilevamento di contenuto per adulti nelle immagini tramite il API Visione artificiale.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: 5d5961ecae2fbc154ae6f1acd74df2bb74024fa1
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/02/2020
ms.locfileid: "96532619"
---
# <a name="detect-adult-content"></a>Rileva contenuto per adulti

Visione artificiale possibile rilevare il materiale per adulti nelle immagini in modo che gli sviluppatori possano limitare la visualizzazione di queste immagini nel software. I flag di contenuto vengono applicati con un punteggio compreso tra zero e uno, in modo che gli sviluppatori possano interpretare i risultati in base alle proprie preferenze.

> [!NOTE]
> Gran parte di questa funzionalità è offerta dal servizio [content moderator di Azure](../content-moderator/overview.md) . Vedere questa alternativa per soluzioni per scenari più rigorosi di moderazione del contenuto, ad esempio per flussi di lavoro di moderazione del testo e di revisione umana.

## <a name="content-flag-definitions"></a>Definizioni dei flag di contenuto

La classificazione "Adult" contiene diverse categorie:

- Le immagini **per adulti** sono di natura sessuale e spesso mostrano nudità e atti sessuali.
- Le immagini in chiaro sono di natura sessuale e spesso contengono contenuti meno sessualmente espliciti rispetto **alle immagini** contrassegnate come **adulte**.
- Le immagini **cruente** mostrano Blood/Gore.

## <a name="use-the-api"></a>Usare l'API

È possibile rilevare il contenuto per adulti con l'API [analizza immagine](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b) . Quando si aggiunge il valore di `Adult` al parametro di query **visualFeatures** , l'API restituisce tre proprietà booleane &mdash; `isAdultContent` , `isRacyContent` e `isGoryContent` &mdash; nella relativa risposta JSON. Il metodo restituisce anche le proprietà corrispondenti &mdash; `adultScore` , `racyScore` e `goreScore` &mdash; che rappresentano i punteggi di confidenza tra zero e uno per ogni rispettiva categoria.

- [Guida introduttiva: Visione artificiale API REST o librerie client](./quickstarts-sdk/client-library.md?pivots=programming-language-csharp)
