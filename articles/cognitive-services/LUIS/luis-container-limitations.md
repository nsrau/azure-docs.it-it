---
title: Limitazioni del contenitore-LUIS
titleSuffix: Azure Cognitive Services
description: Linguaggi del contenitore LUIS supportati.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: bd8a7a63113bcf4e972ab08655aa58b35ddff03d
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507858"
---
# <a name="language-understanding-luis-container-limitations"></a>Limitazioni del contenitore Language Understanding (LUIS)

I contenitori LUIS presentano alcune limitazioni rilevanti. Da dipendenze non supportate, a un sottoinsieme di lingue supportate, questo articolo illustra in dettaglio queste restrizioni.

## <a name="supported-dependencies-for-latest-container"></a>Dipendenze supportate per `latest` contenitore

Il più recente contenitore LUIS, rilasciato a [conferenza//build/2019](https://news.microsoft.com/build2019/), supporterà:

* [Nuovi domini predefiniti](luis-reference-prebuilt-domains.md): questi domini aziendali includono entità, espressioni di esempio e modelli. Estendere questi domini per uso personale.

## <a name="unsupported-dependencies-for-latest-container"></a>Dipendenze non supportate per il contenitore di `latest`

Per [esportare per il contenitore](luis-container-howto.md#export-packaged-app-from-luis), è necessario rimuovere le dipendenze non supportate dall'app Luis. Quando si tenta di esportare per il contenitore, il portale LUIS segnala le funzionalità non supportate che è necessario rimuovere.

È possibile usare un'applicazione LUIS se **non include** le dipendenze seguenti:

Configurazioni dell'app non supportate|Dettagli|
|--|--|
|Impostazioni cultura del contenitore non supportate| Olandese (`nl-NL`)<br>Giapponese (`ja-JP`)<br>Il tedesco è supportato solo con la [Tokenizer 1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Entità non supportate per tutte le impostazioni cultura|Entità [KeyPhrase](luis-reference-prebuilt-keyphrase.md) predefinita per tutte le impostazioni cultura|
|Entità non supportate per la lingua inglese (`en-US`)|Entità [GeographyV2](luis-reference-prebuilt-geographyV2.md) predefinite|
|Priming del riconoscimento vocale|Le dipendenze esterne non sono supportate nel contenitore.|
|Analisi del sentiment|Le dipendenze esterne non sono supportate nel contenitore.|
|Controllo ortografico Bing|Le dipendenze esterne non sono supportate nel contenitore.|

## <a name="languages-supported"></a>Lingue supportate

I contenitori LUIS supportano un subset di [lingue supportate](luis-language-support.md#languages-supported) da Luis proper. I contenitori LUIS sono in grado di comprendere le espressioni nelle seguenti lingue:

| Lingua | Impostazioni locali | Dominio predefinito | Entità predefinita | Raccomandazioni elenco frasi | **[Analisi del testo](../text-analytics/language-support.md)<br>(Valutazione e<br>parole chiave)|
|--|--|:--:|:--:|:--:|:--:|
| Inglese americano | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[Cinese](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| Francese (Francia) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Francese (Canada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Tedesco |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindi | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italiano |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Coreano |`ko-KR` | ✔️ | ❌ | ❌ | Solo *frase chiave* |
| Portoghese (Brasile) |`pt-BR` | ✔️ | ✔️ | ✔️ | non tutte le impostazioni cultura secondarie |
| Spagnolo (Spagna) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Spagnolo (Messico)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Turco | `tr-TR` |✔️| ❌ | ❌ | Solo *sentimento* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]