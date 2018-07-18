---
title: API Traduzione testuale Microsoft - Conteggio dei caratteri | Microsoft Docs
description: Come l'API Traduzione testuale di Microsoft conta i caratteri.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: ebe3e3606a0413730e1fbfd704a6403f77275f89
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374340"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Come l'API Traduzione testuale di Microsoft conta i caratteri

Microsoft Translator conta ogni carattere di input. Caratteri nel senso Unicode, non in byte. I caratteri surrogati Unicode vengono contati come due caratteri. Lo spazio e il markup vengono contati come caratteri. Lunghezza della risposta non ha importanza.

Le chiamate ai metodi Detect e BreakSentence non vengono conteggiate nel consumo dei caratteri. Tuttavia, è previsto che le chiamate ai metodi Detect e BreakSentence siano in proporzione ragionevole all'uso di altre funzioni che vengono conteggiate. Microsoft si riserva il diritto di iniziare il conteggio di Detect e di BreakSentence. 

La traduzione offre il rilevamento automatico se si omette il parametro di lingua From. 

Altre informazioni sui conteggi di caratteri sono reperibili in [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx) (Domande frequenti su Microsoft Translator).
