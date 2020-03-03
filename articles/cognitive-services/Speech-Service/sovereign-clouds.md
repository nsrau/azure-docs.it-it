---
title: Cloud sovrani-servizio di riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Informazioni su come usare i cloud sovrani
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/02/2020
ms.locfileid: "78228084"
---
# <a name="speech-services-with-sovereign-clouds"></a>Servizi vocali con Cloud sovrani

## <a name="azure-government-united-states"></a>Azure per enti pubblici (Stati Uniti)

Solo le agenzie federali, statali, locali e tribali degli Stati Uniti e i rispettivi partner hanno accesso a questa istanza dedicata con operazioni controllate da cittadini selezionati degli Stati Uniti.
- Regioni: US Gov Virginia
- SR in SpeechSDK:*config. FromHost ("WSS://Virginia.STT.Speech.Azure.US", "\<\>chiave");*
- TTS in SpeechSDK: *config. FromHost ("https[]()://Virginia.TTS.Speech.Azure.US", "\<\>chiave");*
- Token di autenticazione: https[]()://Virginia.API.cognitive.Microsoft.US/STS/V1.0/IssueToken
- Portale di Azure: https://portal.azure.us  
- Portale di Riconoscimento vocale personalizzato: https://virginia.cris.azure.us/Home/CustomSpeech
- SKU disponibili: S0
- Funzionalità supportate:
  - Riconoscimento vocale
  - Riconoscimento vocale personalizzato (adattamento acustico/della lingua)
  - Sintesi vocale
  - Traduttore vocale
- Funzionalità non supportate
  - Voce personalizzata
  - Voci neurali per sintesi vocale
- Impostazioni locali supportate: Sono supportate le impostazioni locali per le lingue seguenti.
  - Arabo (AR-*)
  - Cinese (ZH-*)
  - Inglese (en-*)
  - Francese (FR-*)
  - Tedesco (de-*)
  - Hindi
  - Coreano
  - Russo
  - Spagnolo (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure Cina

Situato in Cina, un data center di Azure con accesso diretto a Cina Mobile, China Telecom, Cina Unicom e altre principali reti backbone di Carrier, per gli utenti cinesi per offrire un'esperienza di accesso alla rete locale ad alta velocità e stabile.
- Regioni: Cina orientale 2 (Shanghai)
- SR in SpeechSDK: *config. FromHost ("WSS://chinaeast2.STT.Speech.Azure.cn", "\<\>chiave");*
- TTS in SpeechSDK: *config. FromHost ("https[]()://chinaeast2.TTS.Speech.Azure.cn", "\<\>chiave");*
- Token di autenticazione: https[]()://chinaeast2.API.cognitive.Azure.CN/STS/V1.0/IssueToken
- Portale di Azure: https://portal.azure.cn
- Portale di Riconoscimento vocale personalizzato: https://speech.azure.cn/CustomSpeech
- SKU disponibili: S0
- Funzionalità supportate:
  - Riconoscimento vocale
  - Riconoscimento vocale personalizzato (adattamento acustico/della lingua)
  - Sintesi vocale
  - Traduttore vocale
- Funzionalità non supportate
  - Voce personalizzata
  - Voci neurali per sintesi vocale
- Impostazioni locali supportate: Sono supportate le impostazioni locali per le lingue seguenti.
  - Arabo (AR-*)
  - Cinese (ZH-*)
  - Inglese (en-*)
  - Francese (FR-*)
  - Tedesco (de-*)
  - Hindi
  - Coreano
  - Russo
  - Spagnolo (es-*)

