---
title: Esempi di codice - Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare la funzionalità Content Moderator di Servizi cognitivi di Azure nelle applicazioni .NET tramite l'SDK.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: a746a4baf172bcb6728c06f2a31b460139377e46
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912923"
---
# <a name="content-moderator-net-sdk-samples"></a>Esempi di .NET SDK di Content Moderator

L'elenco seguente include i collegamenti agli esempi di codice compilati usando Azure Content Moderator SDK per .NET.

## <a name="moderation"></a>Moderazione

- **Moderazione immagini** : [esaminare un'immagine per valutare la presenza di contenuto esplicito e spinto, testo e visi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Vedere l'[avvio rapido di .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Immagini personalizzate** : [Eseguire la moderazione con elenchi personalizzati di immagini](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Vedere l'[avvio rapido di .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

> [!NOTE]
> È previsto un limite massimo di **cinque elenchi di immagini** e ogni elenco **non può includere più di 10.000 immagini** .
>

- **Moderazione testo** : [individuare nel testo la presenza di contenuti volgari e informazioni personali](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Vedere l'[avvio rapido di .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Termini personalizzati** : [eseguire la moderazione con elenchi personalizzati di termini](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Vedere l'[avvio rapido di .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

> [!NOTE]
> È previsto un limite massimo di **cinque elenchi di termini** e ogni elenco **non può includere più di 10.000 termini** .
>

- **Moderazione video** : [analizzare un video per ottenere risultati relativi a contenuto esplicito e spinto](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Vedere la [guida introduttiva](video-moderation-api.md).

## <a name="review"></a>Verifica

- **Processi di immagini** : [ avviare un processo di moderazione che analizza le immagini e crea revisioni](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Vedere la [guida introduttiva](moderation-jobs-quickstart-dotnet.md).
- **Revisioni di immagini** : [Creare revisioni human-in-the-loop](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Vedere la [guida introduttiva](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Revisioni di video** : [creare revisioni di video human-in-the-loop](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Vedere la [guida introduttiva](video-reviews-quickstart-dotnet.md)
- **Revisioni di trascrizioni video** : [creare revisioni di trascrizioni video manuali](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Vedere la [guida introduttiva](video-reviews-quickstart-dotnet.md)

Vedere tutti gli esempi .NET negli [esempi .NET di Content Moderator in GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).