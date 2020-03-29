---
title: Nuvole sovrane - Servizio vocale
titleSuffix: Azure Cognitive Services
description: Scopri come utilizzare le Nubi Sovrane
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228084"
---
# <a name="speech-services-with-sovereign-clouds"></a>Servizi vocali con cloud sovrani

## <a name="azure-government-united-states"></a>Azure Government (United States)

Solo i governi federali, statali, locali e tribali degli Stati Uniti e i loro partner hanno accesso a questa istanza dedicata con operazioni controllate da cittadini statunitensi sottoposti a screening.
- Regioni: US Gov Virginia
- SR in SpeechSDK:*config. FromHost("wss://virginia.stt.speech.azure.us",\<" your-key\>");*
- Sintesi vocale in SpeechSDK: *config. FromHost("https://virginia.tts.speech.azure.us",[]()\<" your-key\>");*
- Token di autenticazione: https[]()://virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken
- Portale di Azure: https://portal.azure.us  
- Portale vocale personalizzato:https://virginia.cris.azure.us/Home/CustomSpeech
- SKU disponibili: S0
- Funzionalità supportate:
  - Riconoscimento vocale
  - Riconoscimento vocale personalizzato (adattamento acustico/linguistico)
  - Sintesi vocale
  - Traduttore vocale
- Funzionalità non supportate
  - Voce personalizzata
  - Voci neurali per la sintesi vocale
- Impostazioni locali supportate: sono supportate le impostazioni locali per le lingue seguenti.
  - Arabo (ar-z)
  - Cinese (zh-z)
  - Italiano (en-z)
  - Francese (fr-z)
  - Tedesco (de-z)
  - Hindi
  - Coreano
  - Russo
  - Spagnolo (es-z)

## <a name="microsoft-azure-china"></a>Microsoft Azure Cina

Situato in Cina, un data center di Azure con accesso diretto a China Mobile, China Telecom, China Unicom e altre importanti reti backbone carrier, per gli utenti cinesi per offrire agli utenti cinesi un'esperienza di accesso alla rete locale stabile e ad alta velocità.
- Regioni: Cina Orientale 2 (Shanghai)
- SR in SpeechSDK: *config. FromHost("wss://chinaeast2.stt.speech.azure.cn",\<" your-key\>");*
- Sintesi vocale in SpeechSDK: *config. FromHost("https[]()://chinaeast2.tts.speech.azure.cn",\<"\>your-key ");*
- Token di autenticazione: https[]()://chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken
- Portale di Azure: https://portal.azure.cn
- Portale vocale personalizzato:https://speech.azure.cn/CustomSpeech
- SKU disponibili: S0
- Funzionalità supportate:
  - Riconoscimento vocale
  - Riconoscimento vocale personalizzato (adattamento acustico/linguistico)
  - Sintesi vocale
  - Traduttore vocale
- Funzionalità non supportate
  - Voce personalizzata
  - Voci neurali per la sintesi vocale
- Impostazioni locali supportate: sono supportate le impostazioni locali per le lingue seguenti.
  - Arabo (ar-z)
  - Cinese (zh-z)
  - Italiano (en-z)
  - Francese (fr-z)
  - Tedesco (de-z)
  - Hindi
  - Coreano
  - Russo
  - Spagnolo (es-z)

