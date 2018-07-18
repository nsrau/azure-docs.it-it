---
title: Esempio per il riconoscimento finalità | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Viene riportato un esempio per il riconoscimento finalità.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 38f7f038a803546adb83245519efc5de0c0d1599
ms.sourcegitcommit: 0c490934b5596204d175be89af6b45aafc7ff730
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37045011"
---
# <a name="sample-for-intent-recognition"></a>Esempio per il riconoscimento finalità

> [!NOTE]
> Per istruzioni su come scaricare questo e altri esempi, vedere [Esempi per Speech SDK](samples.md).

> [!NOTE]
> Ottenere prima una chiave di sottoscrizione. A differenza di altri servizi supportati da Speech SDK di Servizi cognitivi, i servizi di riconoscimento finalità richiedono una chiave di sottoscrizione specifica. [Qui](https://www.luis.ai) sono disponibili altre informazioni sulla tecnologia di riconoscimento finalità e su come ottenere una chiave di sottoscrizione. Immettere la propria chiave di sottoscrizione, l'area del servizio e l'AppID del modello di finalità nella sezione appropriata degli esempi.

> [!NOTE]
> Per tutti gli esempi riportati di seguito devono essere presenti queste dichiarazioni di primo livello:
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="intent-recognition-using-microphone"></a>Riconoscimento finalità tramite microfono

Nel frammento di codice seguente viene illustrato come riconoscere la finalità dall'input del microfono nella lingua predefinita (`en-US`).

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

- - -

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Riconoscimento finalità tramite microfono in una lingua specificata

Nel frammento di codice seguente viene illustrato come riconoscere la finalità dall'input del microfono in una lingua specificata. In questo caso, in tedesco (`de-de`).

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

- - -

## <a name="intent-recognition-from-a-file"></a>Riconoscimento finalità da un file

Il frammento di codice seguente riconosce la finalità da un file audio nella lingua predefinita (`en-US`), il formato supportato è WAV/PCM a canale singolo (mono) con una frequenza di campionamento di 16 KHz.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-cpp[Intent Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentRecognitionWithFile)]

- - -

## <a name="intent-recognition-using-events"></a>Riconoscimento finalità tramite eventi

Nel frammento di codice viene illustrato come riconoscere la finalità in modo continuo. Questo codice consente di accedere a informazioni aggiuntive, ad esempio ai risultati intermedi. 

[!code-cpp[Intent Recognition Using Events](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/intent_recognition_samples.cpp#IntentContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Codice sorgente di esempio

Per il set di esempi più recente, vedere il [repository GitHub degli esempi di Speech SDK di Servizi cognitivi](https://aka.ms/csspeech/samples).

## <a name="next-steps"></a>Passaggi successivi

- [Riconoscimento vocale](./speech-to-text-sample.md)

- [Traduzione](./translation.md)
