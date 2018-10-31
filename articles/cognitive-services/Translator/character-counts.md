---
title: Conteggio dei caratteri - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Modalità di conteggio dei caratteri dell'API Traduzione testuale.
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: 8e04158d34765b8a077fc56f2108ea6d7b4b03a6
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649195"
---
# <a name="how-the-translator-text-api-counts-characters"></a>Modalità di conteggio dei caratteri dell'API Traduzione testuale

L'API Traduzione testuale conta ogni carattere di input. Caratteri nel senso Unicode, non in byte. I caratteri surrogati Unicode vengono contati come due caratteri. Lo spazio e il markup vengono contati come caratteri. Lunghezza della risposta non ha importanza.

Le chiamate ai metodi Detect e BreakSentence non vengono conteggiate nel consumo dei caratteri. Tuttavia, è previsto che le chiamate ai metodi Detect e BreakSentence siano in proporzione ragionevole all'uso di altre funzioni che vengono conteggiate. Microsoft si riserva il diritto di iniziare il conteggio di Detect e di BreakSentence.

Altre informazioni sui conteggi di caratteri sono reperibili in [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx) (Domande frequenti su Microsoft Translator).
