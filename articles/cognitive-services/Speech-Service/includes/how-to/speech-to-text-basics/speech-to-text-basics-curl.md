---
author: v-jaswel
ms.service: cognitive-services
ms.topic: include
ms.date: 10/09/2020
ms.author: v-jawe
ms.openlocfilehash: 36d12b29054f736b65af5ac411adbc26d870b982
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92014147"
---
Questa guida di avvio rapido descrive come eseguire la conversione della voce in testo scritto con il servizio Voce e cURL.

Per informazioni generali sui concetti di Riconoscimento vocale, vedere l'articolo [Panoramica](../../../speech-to-text.md).

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

## <a name="convert-speech-to-text"></a>Convertire il parlato in testo

Al prompt dei comandi, eseguire questo comando. Sarà necessario inserire i valori seguenti nel comando.
- La chiave di sottoscrizione al Servizio di riconoscimento vocale dell'utente.
- L'area del servizio Voce.
- Il percorso del file audio di input. È possibile generare file audio tramite la [sintesi vocale](../../../get-started-text-to-speech.md).

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="request":::

Verrà restituita una risposta simile alla seguente.

:::code language="curl" source="~/cognitive-services-quickstart-code/curl/speech/speech-to-text.sh" id="response":::

Per altre informazioni, vedere la [documentazione di riferimento dell'API REST di riconoscimento vocale](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text).
