---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 8a877e1773431053c5ad7344209076cb868a0ee3
ms.sourcegitcommit: 17b36b13857f573639d19d2afb6f2aca74ae56c1
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/10/2020
ms.locfileid: "94424707"
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
- Il valore dell'intestazione `X-Microsoft-OutputFormat`, che controlla il formato di output audio. Un elenco di formati di output audio supportati è disponibile nelle [Informazioni di riferimento sull'API REST di sintesi vocale](../../../rest-text-to-speech.md#audio-outputs).
- La voce di output. Per ottenere un elenco di voci disponibili per l'endpoint Voce, vedere la sezione successiva.
- File di output. In questo esempio la risposta del server viene indirizzata a un file denominato `output.wav`.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/text-to-speech.sh":::

## <a name="list-available-voices-for-your-speech-endpoint"></a>Elencare le voci disponibili per l'endpoint Voce

Per elencare le voci disponibili per l'endpoint Voce, eseguire questo comando.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="request":::

Verrà restituita una risposta simile alla seguente.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/get-voices.sh" id="response":::