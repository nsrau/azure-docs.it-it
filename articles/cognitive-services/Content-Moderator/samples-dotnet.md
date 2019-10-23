---
title: Esempi di codice - Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Usare Content Moderator nelle applicazioni .NET tramite l'SDK.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 23ef7b515f5391a30f6d1fa9fce1dcb856643c69
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/22/2019
ms.locfileid: "72757272"
---
# <a name="content-moderator-net-sdk-samples"></a>Esempi di .NET SDK di Content Moderator

L'elenco seguente include i collegamenti agli esempi di codice compilati usando Azure Content Moderator SDK per .NET.

## <a name="moderation"></a>Moderazione

- **Moderazione immagini**: [esaminare un'immagine per valutare la presenza di contenuto esplicito e spinto, testo e visi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Vedere la [guida introduttiva](image-moderation-quickstart-dotnet.md).
- **Immagini personalizzate**: [Eseguire la moderazione con elenchi personalizzati di immagini](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Vedere la [guida introduttiva](image-lists-quickstart-dotnet.md).

> [!NOTE]
> È previsto un limite massimo di **cinque elenchi di immagini** e ogni elenco **non può includere più di 10.000 immagini**.
>

- **Moderazione testo**: [individuare nel testo la presenza di contenuti volgari e informazioni personali](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Vedere la [guida introduttiva](text-moderation-quickstart-dotnet.md).
- **Termini personalizzati**: [eseguire la moderazione con elenchi personalizzati di termini](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Vedere la [guida introduttiva](term-lists-quickstart-dotnet.md).

> [!NOTE]
> È previsto un limite massimo di **cinque elenchi di termini** e ogni elenco **non può includere più di 10.000 termini**.
>

- **Moderazione video**: [analizzare un video per ottenere risultati relativi a contenuto esplicito e spinto](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Vedere la [guida introduttiva](video-moderation-api.md).

## <a name="review"></a>Revisione

- **Processi di immagini**: [ avviare un processo di moderazione che analizza le immagini e crea revisioni](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Vedere la [guida introduttiva](moderation-jobs-quickstart-dotnet.md).
- **Revisioni di immagini**: [Creare revisioni human-in-the-loop](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Vedere la [guida introduttiva](moderation-reviews-quickstart-dotnet.md).
- **Revisioni di video**: [creare revisioni di video human-in-the-loop ](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Vedere la [guida introduttiva](video-reviews-quickstart-dotnet.md)
- **Revisioni di trascrizioni video**: [creare revisioni di trascrizioni video manuali](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Vedere la [guida introduttiva](video-reviews-quickstart-dotnet.md)

Vedere tutti gli esempi .NET negli [esempi .NET di Content Moderator in GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
