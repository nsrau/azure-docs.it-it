---
title: Guida introduttiva alla trascrizione di conversazioni in tempo reale-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare la trascrizione delle conversazioni in tempo reale con l'SDK di riconoscimento vocale. La trascrizione delle conversazioni consente di trascrivere riunioni e altre conversazioni con la possibilità di aggiungere, rimuovere e identificare più partecipanti tramite lo streaming di audio al servizio di riconoscimento vocale.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: trbye
zone_pivot_groups: acs-js-csharp
ms.openlocfilehash: ae3d7b329c55ce0be101cee73e1fc7674a2d75aa
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/21/2020
ms.locfileid: "95026540"
---
# <a name="get-started-with-real-time-conversation-transcription"></a>Inizia a usare la trascrizione in tempo reale delle conversazioni

L'API **ConversationTranscriber** dell'SDK vocale consente di trascrivere riunioni e altre conversazioni con la possibilità di aggiungere, rimuovere e identificare più partecipanti tramite lo streaming di audio al servizio di riconoscimento vocale usando `PullStream` o `PushStream` . È innanzitutto necessario creare firme vocali per ogni partecipante usando l'API REST e quindi usare le firme vocali con l'SDK per trascrivere le conversazioni. Per ulteriori informazioni, vedere la [Panoramica](conversation-transcription.md) della trascrizione delle conversazioni.

## <a name="limitations"></a>Limitazioni

* Disponibile solo nelle aree di sottoscrizione seguenti: `centralus` , `eastasia` , `eastus` , `westeurope`
* Richiede una matrice multimicrofono circolare 7-mic con un flusso di riferimento per la riproduzione. La matrice di microfoni deve soddisfare le [specifiche](./speech-devices-sdk-microphone.md).
* [Speech Devices SDK](speech-devices-sdk.md) fornisce dispositivi appropriati e un'app di esempio che illustra la trascrizione delle conversazioni.

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](overview.md#try-the-speech-service-for-free).

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/conversation-transcription/real-time-javascript.md)]
::: zone-end

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/conversation-transcription/real-time-csharp.md)]
::: zone-end

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> Trascrizione di conversazioni [asincrone](how-to-async-conversation-transcription.md) 
>  Codice di esempio del [dispositivo ROOBO](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Android/Speech%20Devices%20SDK%20Starter%20App/example/app/src/main/java/com/microsoft/cognitiveservices/speech/samples/sdsdkstarterapp/ConversationTranscription.java) 
>  [Codice di esempio di Azure Kinect Dev Kit](https://github.com/Azure-Samples/Cognitive-Services-Speech-Devices-SDK/blob/master/Samples/Java/Windows_Linux/SampleDemo/src/com/microsoft/cognitiveservices/speech/samples/Cts.java)