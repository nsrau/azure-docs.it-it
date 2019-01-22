---
title: Esempi di codice - Content Moderator, .NET
description: Usare Content Moderator nelle applicazioni .NET tramite l'SDK.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 6911b9b718edfc84a7c219be448dee06f4a0f835
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/14/2019
ms.locfileid: "54259191"
---
# <a name="content-moderator-net-sdk-samples"></a>Esempi di .NET SDK di Content Moderator

L'elenco seguente include i collegamenti agli esempi di codice compilati usando Azure Content Moderator SDK per .NET.

- **Libreria helper**: [creare un client Content Moderator per l'uso in altri esempi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Vedere la [guida introduttiva](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Moderazione

- **Moderazione immagini**: [esaminare un'immagine per valutare la presenza di contenuto esplicito e spinto, testo e visi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Vedere la [guida introduttiva](image-moderation-quickstart-dotnet.md).
- **Immagini personalizzate**: [Eseguire la moderazione con elenchi personalizzati di immagini](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Vedere la [guida introduttiva](image-lists-quickstart-dotnet.md).

> [!NOTE]
> È previsto un limite massimo di **cinque elenchi di immagini** e ogni elenco **non può includere più di 10.000 immagini**.
>

- **Moderazione testo**: [individuare nel testo la presenza di contenuti volgari e informazioni personali (PII)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Vedere la [guida introduttiva](text-moderation-quickstart-dotnet.md).
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
