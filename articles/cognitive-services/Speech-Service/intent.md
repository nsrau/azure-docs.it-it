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
ms.date: 07/16/2018
ms.author: wolfma
ms.openlocfilehash: 3e9afc990d6bfa73eb045e7ed76dfd194df309c6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/23/2018
ms.locfileid: "39213200"
---
# <a name="sample-for-intent-recognition"></a>Esempio per il riconoscimento finalità

Ottenere prima una chiave di sottoscrizione. A differenza di altri servizi supportati da Speech SDK di Servizi cognitivi, i servizi di riconoscimento finalità richiedono una chiave di sottoscrizione specifica. [Qui](https://www.luis.ai) sono disponibili altre informazioni sulla tecnologia di riconoscimento finalità e su come ottenere una chiave di sottoscrizione. Immettere la propria chiave di sottoscrizione Language Understanding, l'[area della sottoscrizione](regions.md) e l'AppID del modello di finalità nella sezione appropriata degli esempi.

## <a name="top-level-declarations"></a>Dichiarazioni di primo livello

Per tutti gli esempi riportati di seguito devono essere presenti queste dichiarazioni di primo livello:

[!code-csharp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#toplevel)]

[!code-cpp[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#toplevel)]

[!code-java[Top-level declarations](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#toplevel)]

## <a name="intent-recognition-using-microphone"></a>Riconoscimento finalità tramite microfono

Nel frammento di codice seguente viene illustrato come riconoscere la finalità dall'input del microfono nella lingua predefinita (`en-US`).

[!code-csharp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithMicrophone)]

[!code-cpp[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithMicrophone)]

[!code-java[Intent Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithMicrophone)]

## <a name="intent-recognition-using-microphone-in-a-specified-language"></a>Riconoscimento finalità tramite microfono in una lingua specificata

Nel frammento di codice seguente viene illustrato come riconoscere la finalità dall'input del microfono in una lingua specificata. In questo caso, in tedesco (`de-de`).

[!code-csharp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentRecognitionWithLanguage)]

[!code-cpp[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentRecognitionWithLanguage)]

[!code-java[Intent Recognition Using Microphone In A Specified Language](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentRecognitionWithLanguage)]

## <a name="intent-recognition-from-a-file-using-events"></a>Riconoscimento finalità da un file, con eventi

Nel frammento di codice viene illustrato come riconoscere la finalità nella lingua predefinita (`en-US`) in modo continuo. Questo codice consente di accedere a informazioni aggiuntive, ad esempio ai risultati intermedi. L'input viene acquisito da un file audio, il formato supportato è WAV singolo canale (mono)/PCM con una frequenza di campionamento di KHz 16.

[!code-csharp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/csharp/sharedcontent/console/intent_recognition_samples.cs#intentContinuousRecognitionWithFile)]

[!code-cpp[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/cpp/windows/console/samples/intent_recognition_samples.cpp#IntentContinuousRecognitionWithFile)]

[!code-java[Intent Recognition Using Events From a File](~/samples-cognitive-services-speech-sdk/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/IntentRecognitionSamples.java#IntentContinuousRecognitionWithFile)]

[!include[Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]

## <a name="next-steps"></a>Passaggi successivi

- [Riconoscimento vocale](./speech-to-text-sample.md)

- [Traduzione](./translation.md)
