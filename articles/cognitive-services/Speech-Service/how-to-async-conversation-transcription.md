---
title: Trascrizione di conversazioni asincrone-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare la trascrizione di conversazioni asincrone usando il servizio di riconoscimento vocale. Disponibile solo per Java e C#.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: amishu
zone_pivot_groups: programming-languages-set-twenty-one
ms.openlocfilehash: 65a6fd7e0e1e64a459a46c0f97a2afdbc03573a9
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/28/2020
ms.locfileid: "87284217"
---
# <a name="asynchronous-conversation-transcription"></a>Trascrizione conversazione asincrona

In questo articolo viene illustrata la trascrizione asincrona delle conversazioni usando l'API **RemoteConversationTranscriptionClient** . Se è stata configurata la trascrizione della conversazione per eseguire la trascrizione asincrona e si dispone di un `conversationId` , è possibile ottenere la trascrizione associata a tale operazione `conversationId` usando l'API **RemoteConversationTranscriptionClient** .

## <a name="asynchronous-vs-real-time--asynchronous"></a>Confronto asincrono e in tempo reale + asincrono

Con la trascrizione asincrona è possibile trasmettere l'audio della conversazione, ma non è necessaria una trascrizione restituita in tempo reale. Al contrario, dopo l'invio dell'audio, utilizzare la `conversationId` di `Conversation` per eseguire una query per lo stato della trascrizione asincrona. Quando la trascrizione asincrona è pronta, viene ottenuto un `RemoteConversationTranscriptionResult` .

Con il tempo reale più asincrono, si ottiene la trascrizione in tempo reale, ma si ottiene anche la trascrizione eseguendo una query con `conversationId` (simile allo scenario asincrono).

Per completare la trascrizione asincrona sono necessari due passaggi. Il primo passaggio consiste nel caricare l'audio, scegliendo solo asincrono o in tempo reale più asincrono. Il secondo passaggio consiste nell'ottenere i risultati della trascrizione.

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/remote-conversation/java/examples.md)]
::: zone-end


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esplorare gli esempi su GitHub](https://aka.ms/csspeech/samples)
