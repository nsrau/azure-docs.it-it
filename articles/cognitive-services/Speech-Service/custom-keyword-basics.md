---
title: Creazione di parole chiave personalizzate-servizio riconoscimento vocale
titleSuffix: Azure Cognitive Services
description: Il dispositivo è sempre in ascolto di una parola chiave (o frase). Quando l'utente dice la parola chiave, il dispositivo invia tutti i file audio successivi al cloud, fino a quando l'utente non smette di parlare. Personalizzare la parola chiave è un modo efficace per distinguere il dispositivo e rafforzare la personalizzazione.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/20/2019
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 188824b9c43387cf106a3cdd53a09fd6bcb198b8
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360668"
---
# <a name="custom-keyword-basics"></a>Informazioni di base sulle parole chiave personalizzate

Questo articolo illustra le nozioni di base per l'uso di parole chiave personalizzate, con speech studio e l'SDK di riconoscimento vocale. Una parola chiave è una parola o una frase breve che consente di attivare la voce del prodotto. È possibile creare modelli di parole chiave in speech studio, quindi esportare un file di modello usato con l'SDK di riconoscimento vocale nelle applicazioni.

## <a name="prerequisites"></a>Prerequisiti

I passaggi descritti in questo articolo richiedono una sottoscrizione vocale e l'SDK vocale. Se non si ha già una sottoscrizione, [provare il servizio di riconoscimento vocale gratuitamente](overview.md#try-the-speech-service-for-free). Per ottenere l'SDK, vedere la [Guida all'installazione](quickstarts/setup-platform.md) per la piattaforma in uso.

## <a name="create-a-keyword-in-speech-studio"></a>Creare una parola chiave in speech studio

Prima di poter usare una parola chiave personalizzata, è necessario creare una parola chiave usando la pagina delle [parole chiave personalizzata](https://aka.ms/sdsdk-wakewordportal) in [speech studio](https://aka.ms/sdsdk-speechportal). Dopo aver fornito una parola chiave, viene prodotto un `.table` file che è possibile usare con l'SDK di riconoscimento vocale.

> [!IMPORTANT]
> I modelli di parole chiave personalizzate e i file risultanti `.table` possono essere creati **solo** in speech studio.
> Non è possibile creare parole chiave personalizzate dall'SDK o con chiamate REST.

1. Passare a [speech studio](https://aka.ms/sdsdk-speechportal) e **accedere** oppure, se non si dispone ancora di una sottoscrizione vocale, scegliere [**Crea una sottoscrizione**](https://go.microsoft.com/fwlink/?linkid=2086754).

1. Nella pagina della [parola chiave Custom](https://aka.ms/sdsdk-wakewordportal) creare un **nuovo progetto**. 

1. Immettere un **nome**, una **Descrizione**facoltativa e selezionare la lingua. È necessario un progetto per ogni lingua e il supporto è attualmente limitato al `en-US` linguaggio.

    ![Descrivere il progetto di parola chiave](media/custom-keyword/custom-kws-portal-new-project.png)

1. Selezionare il progetto dall'elenco. 

    ![Selezionare il progetto di parola chiave](media/custom-keyword/custom-kws-portal-project-list.png)

1. Per creare un nuovo modello di parola chiave, fare clic su **Train Model**.

1. Immettere un **nome** per il modello, una **Descrizione**facoltativa e la **parola chiave** desiderata, quindi fare clic su **Avanti**. Vedere le [linee guida](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) sulla scelta di una parola chiave valida.

    ![Immettere la parola chiave](media/custom-keyword/custom-kws-portal-new-model.png)

1. Il portale crea le pronunce candidate per la parola chiave. Ascoltare ogni candidato facendo clic sui pulsanti di riproduzione e rimuovere i controlli accanto a tutte le pronunce non corrette. Una volta selezionate solo le pronunce valide, fare clic su **Train** per iniziare a generare il modello di parola chiave. 

    ![Esaminare la parola chiave](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Potrebbero essere necessari fino a trenta minuti per la generazione del modello. L'elenco delle parole chiave verrà modificato da **elaborazione** **a completata** quando il modello è completo. È quindi possibile scaricare il file.

    ![Esaminare la parola chiave](media/custom-keyword/custom-kws-portal-download-model.png)

1. Il file scaricato è un `.zip` archivio. Estrarre l'archivio. viene visualizzato un file con `.table` estensione. Si tratta del file usato con l'SDK nella sezione successiva, quindi assicurarsi di annotarne il percorso. il nome del file rispecchia il nome della parola chiave, ad esempio una parola chiave **Activate Device** ha il nome file `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>Usare un modello di parola chiave con l'SDK

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

## <a name="next-steps"></a>Passaggi successivi

Testare la parola chiave personalizzata con la [Guida introduttiva all'SDK dei dispositivi vocali](https://aka.ms/sdsdk-quickstart).
