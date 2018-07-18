---
title: Esempio per la traduzione | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Di seguito viene illustrato un esempio per la traduzione vocale.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 1151628ed695e74e2196c20b08e33fa5eaf33282
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37028388"
---
# <a name="sample-for-translation"></a>Esempio per la traduzione

> [!NOTE]
> Per istruzioni su come scaricare questo e altri esempi, vedere [Esempi per Voce SDK](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Per tutti gli esempi riportati di seguito devono essere presenti queste dichiarazioni di primo livello:
>
> [!code-csharp [Using Statements](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#toplevel)]
>
> - - -

## <a name="translation-using-the-microphone"></a>Traduzione con uso del microfono

Il frammento di codice riportato di seguito illustra come eseguire una traduzione vocale dall'inglese al tedesco e come ottenere l'output vocale del testo tradotto. Viene usato il microfono.

[!code-csharp[Translation Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithMicrophoneAsync)]

- - -

## <a name="translation-using-file-input"></a>Traduzione con input di file

Il frammento di codice riportato di seguito illustra come eseguire una traduzione vocale dall'inglese al tedesco e al francese.
Viene usato un file come input.

[!code-csharp[Translation Using File Input](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/translation_samples.cs#TranslationWithFileAsync)]

- - -

## <a name="sample-source-code"></a>Codice sorgente di esempio

La versione più recente degli esempi ed esempi ancora più avanzati sono disponibili in un [repository GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk) dedicato.

## <a name="next-steps"></a>Passaggi successivi

- [Riconoscimento vocale](./speech-to-text-sample.md)

- [Riconoscimento finalità](./intent.md)
