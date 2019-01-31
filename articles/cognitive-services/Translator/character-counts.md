---
title: Conteggio dei caratteri - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Modalità di conteggio dei caratteri dell'API Traduzione testuale.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c56622ee5e25fc422d02cec6ecfaa1f847e9e769
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226435"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Modalità di conteggio dei caratteri dell'API Traduzione testuale

L'API Traduzione testuale conta ogni carattere di input. Caratteri nel senso Unicode, non in byte. I caratteri surrogati Unicode vengono contati come due caratteri. Lo spazio e il markup vengono contati come caratteri. Lunghezza della risposta non ha importanza.

Le chiamate ai metodi Detect e BreakSentence non vengono conteggiate nel consumo dei caratteri. Tuttavia, è previsto che le chiamate ai metodi Detect e BreakSentence siano in proporzione ragionevole all'uso di altre funzioni che vengono conteggiate. Microsoft si riserva il diritto di iniziare il conteggio di Detect e di BreakSentence.

Altre informazioni sui conteggi di caratteri sono reperibili in [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx) (Domande frequenti su Microsoft Translator).
