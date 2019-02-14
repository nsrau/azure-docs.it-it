---
title: SDK ed esempi - Content Moderator, Python, Java, Node js e .NET
titlesuffix: Azure Cognitive Services
description: Ottenere SDK ed esempi per Content Moderator
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: 929655f257dced1e12645d4d751b1475e2497b49
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868362"
---
# <a name="content-moderator-sdks-and-samples"></a>SDK ed esempi di Content Moderator

## <a name="sdks-for-python-java-nodejs-and-net"></a>SDK per Python, Java, Node.js e .NET

- [Content Moderator SDK per Python](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [Content Moderator SDK per Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [Content Moderator SDK per Node.js](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [Content Moderator SDK per .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>Esempi di .NET SDK

L'elenco seguente include i collegamenti agli esempi di codice compilati usando Azure Content Moderator SDK per .NET.

- **Libreria helper**: [creare un client Content Moderator per l'uso in altri esempi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Vedere la [guida introduttiva](content-moderator-helper-quickstart-dotnet.md).

### <a name="moderation"></a>Moderazione 
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

### <a name="review"></a>Revisione
- **Processi di immagini**: [ avviare un processo di moderazione che analizza le immagini e crea revisioni](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Vedere la [guida introduttiva](moderation-jobs-quickstart-dotnet.md).
- **Revisioni di immagini**: [Creare revisioni human-in-the-loop](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Vedere la [guida introduttiva](moderation-reviews-quickstart-dotnet.md).
- **Revisioni di video**: [creare revisioni di video human-in-the-loop ](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Vedere la [guida introduttiva](video-reviews-quickstart-dotnet.md)
- **Revisioni di trascrizioni video**: [creare revisioni di trascrizioni video manuali](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Vedere la [guida introduttiva](video-reviews-quickstart-dotnet.md)

Vedere tutti gli esempi .NET negli [esempi .NET di Content Moderator in GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

## <a name="rest-api-samples-in-c"></a>Esempi di API REST in C#

- [Moderazione immagini](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [Moderazione testo](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [Moderazione video](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [Revisioni di immagini](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [Processi di immagini](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

Per le procedure dettagliate di questi esempi, vedere il [webinar su richiesta](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html).

## <a name="tutorials"></a>Esercitazioni
- [Moderazione del catalogo di e-commerce](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration). Vedere l'[esercitazione](ecommerce-retail-catalog-moderation.md).
- [Moderazione del contenuto di Facebook](https://github.com/MicrosoftContentModerator/samples-fbPageModeration). Vedere l'[esercitazione](facebook-post-moderation.md).
- [Soluzione di moderazione e revisione di video e trascrizioni](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) Vedere l'[esercitazione](video-transcript-moderation-review-tutorial-dotnet.md)

## <a name="on-demand-webinars"></a>Webinar on demand
- [Machine-assisted content moderation at scale with Content Moderator](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html) (Moderazione del contenuto con supporto di computer su larga scala con Content Moderator)
