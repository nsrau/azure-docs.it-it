---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 11/03/2020
ms.author: trbye
ms.openlocfilehash: 040ffea69f76255dcb1bfc6787cad45a95baa904
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2020
ms.locfileid: "93305888"
---
Innanzitutto, caricare il file del modello di parole chiave utilizzando la `FromFile()` funzione statica, che restituisce `KeywordRecognitionModel` . Usare il percorso del `.table` file scaricato da speech studio. Inoltre, è possibile creare un `AudioConfig` usando il microfono predefinito, quindi creare un'istanza di un nuovo `KeywordRecognizer` usando la configurazione audio.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Successivamente, l'esecuzione del riconoscimento delle parole chiave viene eseguita con una chiamata a `RecognizeOnceAsync()` passando l'oggetto modello. Viene avviata una sessione di riconoscimento delle parole chiave che dura fino a quando non viene riconosciuta la parola chiave. In questo modo, in genere si usa questo schema progettuale nelle applicazioni multithread o nei casi di utilizzo in cui è possibile che si sia in attesa di una parola di riattivazione per un periodo illimitato.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> Nell'esempio riportato di seguito viene usato il riconoscimento delle parole chiave locale, poiché non richiede un `SpeechConfig` oggetto per il contesto di autenticazione e non contatta il back-end. Tuttavia, è possibile eseguire sia il riconoscimento delle parole chiave che [la verifica utilizzando una connessione back-end continua](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword).