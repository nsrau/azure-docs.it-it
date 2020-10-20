---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 029cb1e59876eb01c609ef8b7ba0412ed35a2a1b
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91940745"
---
Questa guida di avvio rapido descrive come eseguire la sintesi vocale con il servizio Voce e cURL.

Per informazioni generali sui concetti di sintesi vocale, vedere l'articolo [Panoramica](../../../text-to-speech.md).

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-text-to-speech"></a>Sintesi vocale

Al prompt dei comandi, eseguire questo comando. Sarà necessario inserire i valori seguenti nel comando.
- La chiave di sottoscrizione al Servizio di riconoscimento vocale dell'utente.
- L'area del servizio Voce.

È anche possibile modificare i valori seguenti.
- Il valore dell'intestazione `X-Microsoft-OutputFormat`, che controlla il formato di output audio. Un elenco di formati di output audio supportati è disponibile nelle [Informazioni di riferimento sull'API REST di sintesi vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-text-to-speech#audio-outputs).
- La voce di output. Per ottenere un elenco di voci disponibili per l'endpoint Voce, vedere la sezione successiva.
- File di output. In questo esempio la risposta del server viene indirizzata a un file denominato `output.wav`.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>Elencare le voci disponibili per l'endpoint Voce

Per elencare le voci disponibili per l'endpoint Voce, eseguire questo comando.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

Verrà restituita una risposta simile alla seguente.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::
