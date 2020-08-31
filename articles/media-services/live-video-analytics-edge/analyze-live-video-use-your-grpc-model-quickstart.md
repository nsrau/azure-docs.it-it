---
title: Analizzare un video live con un modello gRPC personalizzato - Azure
description: Questo argomento di avvio rapido illustra come applicare la visione artificiale all'analisi del feed di video live proveniente da una videocamera IP simulata.
ms.topic: quickstart
ms.date: 08/14/2020
zone_pivot_groups: ams-lva-edge-programming-languages
ms.openlocfilehash: ac11ced68ab8463da26b9978a5b0b02c4cd1a402
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687279"
---
# <a name="quickstart-analyze-live-video-by-using-your-own-grpc-model"></a>Avvio rapido: Analizzare un video live con un modello gRPC personalizzato

Questo avvio rapido illustra come usare Analisi video live in IoT Edge per analizzare un feed video live proveniente da una videocamera IP simulata. Si vedrà come applicare un modello di visione artificiale per rilevare oggetti. Viene inviato un sottoinsieme di fotogrammi del feed video live a un servizio di inferenza. I risultati vengono inviati all'hub di IoT Edge.

::: zone pivot="programming-language-csharp"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/csharp/header.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [header](includes/analyze-live-video-your-grpc-model-quickstart/python/header.md)]
::: zone-end

## <a name="prerequisites"></a>Prerequisiti

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/analyze-live-video-your-grpc-model-quickstart/python/prerequisites.md)]
::: zone-end

## <a name="review-the-sample-video"></a>Esaminare il video di esempio

::: zone pivot="programming-language-csharp"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/csharp/review-sample-video.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [review sample video](includes/analyze-live-video-your-grpc-model-quickstart/python/review-sample-video.md)]
::: zone-end

## <a name="overview"></a>Panoramica

::: zone pivot="programming-language-csharp"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/csharp/overview.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [overview](includes/analyze-live-video-your-grpc-model-quickstart/python/overview.md)]
::: zone-end

## <a name="create-and-deploy-the-media-graph"></a>Creare e distribuire il grafico multimediale

::: zone pivot="programming-language-csharp"
[!INCLUDE [create and deply the media graph](includes/analyze-live-video-your-grpc-model-quickstart/csharp/create-deploy-media-graph.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [create and deply the media graph](includes/analyze-live-video-your-grpc-model-quickstart/python/create-deploy-media-graph.md)]
::: zone-end

## <a name="interpret-results"></a>Interpretare i risultati

::: zone pivot="programming-language-csharp"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/csharp/interpret-results.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [interpret results](includes/analyze-live-video-your-grpc-model-quickstart/python/interpret-results.md)]
::: zone-end

## <a name="clean-up-resources"></a>Pulire le risorse

Se si intende provare altri argomenti di avvio rapido, mantenere le risorse create. In caso contrario, passare al portale di Azure, passare ai gruppi di risorse, selezionare il gruppo di risorse in cui è stato eseguito l'argomento di avvio rapido ed eliminare tutte le risorse.

## <a name="next-steps"></a>Passaggi successivi

Provare a eseguire diverse topologie del grafo multimediale usando il protocollo gRPC.
