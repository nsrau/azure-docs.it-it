---
title: Contenuti per adulti, crudi e cruissimi - Computer Vision
titleSuffix: Azure Cognitive Services
description: Concetti relativi al rilevamento di contenuti per adulti nelle immagini utilizzando l'APi di Visione artificiale.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "71718523"
---
# <a name="detect-adult-content"></a>Rilevare contenuti per adulti

Computer Vision è in grado di rilevare materiale per adulti nelle immagini in modo che gli sviluppatori possano limitare la visualizzazione di queste immagini nel software. Vengono applicati flag di contenuto con un punteggio compreso tra 0 e 1, in modo che gli sviluppatori possano interpretare i risultati in base alle proprie preferenze.

> [!NOTE]
> Gran parte di questa funzionalità è offerta dal servizio [Azure Content Moderator.Much](https://docs.microsoft.com/azure/cognitive-services/content-moderator/overview) of this functionality is offered by the Azure Content Moderator service. Vedere questa alternativa per soluzioni per scenari più rigorosi di moderazione del contenuto, ad esempio per flussi di lavoro di moderazione del testo e di revisione umana.

## <a name="content-flag-definitions"></a>Definizioni dei flag di contenuto

All'interno della classificazione "adulto" ci sono diverse categorie:

- **Le** immagini per adulti sono definite come quelle che sono esplicitamente di natura sessuale e spesso raffigurano nudità e atti sessuali.
- Le immagini **filanti** sono definite come immagini sessualmente allusivi in natura e spesso contengono contenuti meno sessualmente espliciti rispetto alle immagini contrassegnate come **Adult**.
- Le immagini **crueresono** sono definite come quelle che raffigurano il gore.

## <a name="use-the-api"></a>Usare l'API

È possibile rilevare contenuti per adulti con l'API [Analizza immagine.](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa) Quando si aggiunge `Adult` il valore di al parametro di&mdash;`isAdultContent`query `isRacyContent` **visualFeatures,** l'API restituisce tre proprietà booleane , e `isGoryContent` &mdash;nella relativa risposta JSON. Il metodo restituisce&mdash;`adultScore` `racyScore`anche `goreScore` &mdash;le proprietà corrispondenti , , e che rappresentano punteggi di confidenza compresi tra zero e uno per ogni rispettiva categoria.

- [Guida introduttiva: Analizzare un'immagine (.NET SDK)Quickstart: Analyze an image (.NET SDK)](./quickstarts-sdk/csharp-analyze-sdk.md)
- [Guida introduttiva: Analizzare un'immagine (API REST)Quickstart: Analyze an image (REST API)](./quickstarts/csharp-analyze.md)
