---
title: Esempio di riconoscimento vocale | Microsoft Docs
titleSuffix: Microsoft Cognitive Services
description: Ecco un esempio di riconoscimento vocale.
services: cognitive-services
author: wolfma61
manager: onano
ms.service: cognitive-services
ms.technology: Speech
ms.topic: article
ms.date: 06/07/2018
ms.author: wolfma
ms.openlocfilehash: 2a1850e6a4f3c8eebd1b947aabe1374bdaab3fc8
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/27/2018
ms.locfileid: "37030251"
---
# <a name="sample-for-speech-to-text"></a>Esempio di riconoscimento vocale

> [!NOTE]
> Per istruzioni su come scaricare questo e altri esempi, vedere [Esempi per Speech SDK](samples.md).

[!include[Get a Subscription Key](includes/get-subscription-key.md)]

> [!NOTE]
> Per tutti gli esempi riportati di seguito devono essere presenti queste dichiarazioni di primo livello:
>
> [!code-csharp[](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#toplevel)]
>
> [!code-cpp[](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#toplevel)]
>
> - - -

## <a name="speech-recognition-using-the-microphone"></a>Riconoscimento vocale tramite il microfono

Nel frammento di codice seguente viene illustrato come riconoscere input vocale dal microfono nella lingua predefinita (`en-US`).

[!code-csharp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionWithMicrophone)]

[!code-cpp[Speech Recognition Using Microphone](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionWithMicrophone)]

- - -

## <a name="speech-recognition-from-a-file"></a>Riconoscimento vocale da un file

Il frammento di codice seguente riconosce input vocale da un file audio nella lingua predefinita (`en-US`), il formato supportato è WAV/PCM a canale singolo (mono) con una frequenza di campionamento di 16 KHz.

[!include[Sample Audio](includes/sample-audio.md)]

[!code-csharp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs?name=recognitionFromFile)]

[!code-cpp[Speech Recognition From a File](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp?name=SpeechRecognitionWithFile)]

- - -

## <a name="speech-recognition-using-a-customized-model"></a>Riconoscimento vocale tramite un modello personalizzato

Il [Servizio di riconoscimento vocale personalizzato](https://www.cris.ai/) (CRIS) consente la personalizzazione del motore di riconoscimento vocale Microsoft per l'applicazione. Il frammento di codice seguente illustra come riconoscere input vocale da un microfono usando un modello CRIS personalizzato. Prima di eseguirlo, immettere la chiave di sottoscrizione del servizio CRIS e l'ID della propria distribuzione.

[!code-csharp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionCustomized)]

[!code-cpp[Speech Recognition Using a Customized Model](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechRecognitionUsingCustomizedModel)]

- - -

## <a name="continuous-speech-recognition"></a>Riconoscimento vocale continuo

[!code-csharp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/csharp_samples/speech_recognition_samples.cs#recognitionContinuous)]

[!code-cpp[Continuous Speech Recognition](~/samples-cognitive-services-speech-sdk/Windows/cxx_samples/speech_recognition_samples.cpp#SpeechContinuousRecognitionUsingEvents)]

- - -

## <a name="sample-source-code"></a>Codice sorgente di esempio

La versione più recente degli esempi ed esempi ancora più avanzati sono disponibili in un [repository GitHub](https://github.com/Azure-Samples/cognitive-services-speech-sdk) dedicato.

## <a name="next-steps"></a>Passaggi successivi

- [Riconoscimento finalità](./intent.md)

- [Traduzione](./translation.md)
