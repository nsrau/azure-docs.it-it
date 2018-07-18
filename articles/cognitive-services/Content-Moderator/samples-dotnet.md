---
title: Esempi di codice di Content Moderator di Azure | Microsoft Docs
description: Usare Content Moderator nelle applicazioni
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2018
ms.author: sajagtap
ms.openlocfilehash: a7f5b0a7433631e303de47667871cc1354053c08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374321"
---
# <a name="net-sdk-samples"></a>Esempi di .NET SDK

L'elenco seguente include collegamenti a esempi di codice compilati usando Content Moderator SDK di Azure per .NET.

- **Libreria helper**: [per creare un client Content Moderator per l'uso in altri esempi](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Vedere l'[Avvio rapido](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Moderazione

- **Moderazione immagini**: [per valutare un'immagine per adulti e il contenuto, il testo e le immagini audaci](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Vedere l'[Avvio rapido](image-moderation-quickstart-dotnet.md).
- **Immagini personalizzate**: [per moderare con elenchi personalizzati di immagini](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Vedere l'[Avvio rapido](image-lists-quickstart-dotnet.md).

> [!NOTE]
> È previsto un limite massimo di **cinque elenchi di immagini** e ogni elenco **non può includere più di 10.000 immagini**.
>

- **Moderazione testo**: [testo di schermate con contenuti volgari e informazioni personali (PII)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Vedere l'[Avvio rapido](text-moderation-quickstart-dotnet.md).
- **Termini personalizzati**: [per moderare con elenchi personalizzati di termini](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Vedere l'[Avvio rapido](term-lists-quickstart-dotnet.md).

> [!NOTE]
> È previsto un limite massimo di **cinque elenchi di termini** e ogni elenco **non può includere più di 10.000 termini**.
>

- **Moderazione video**: [ per analizzare un video per adulti e il contenuto audace e ottenere risultati](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Vedere l'[Avvio rapido](video-moderation-api.md).

## <a name="review"></a>Revisione

- **Processi di immagini**: [per avviare un processo di moderazione che analizza e crea revisioni](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Vedere l'[Avvio rapido](moderation-jobs-quickstart-dotnet.md).
- **Revisioni immagini**: [per creare revisioni manuali](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Vedere l'[Avvio rapido](moderation-reviews-quickstart-dotnet.md).
- **Revisioni video**: [per creare revisioni video manuali](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Vedere l'[Avvio rapido](video-reviews-quickstart-dotnet.md)
- **Verifiche di trascrizioni video**: [per creare verifiche di trascrizioni video manuali](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Vedere l'[Avvio rapido](video-reviews-quickstart-dotnet.md)

Vedere tutti gli esempi di .NET in [Content Moderator .NET samples on GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator) (Esempi .NET di Content Moderator su GitHub).
