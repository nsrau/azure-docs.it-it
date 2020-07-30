---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 99540e8518aa31a2221844aa954ff665609d8217
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87375049"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md)
> * Assicurarsi di avere accesso a un microfono per l'acquisizione audio

## <a name="setup-your-environment"></a>Configurare l'ambiente

Aggiornare il file go.mod con la versione più recente dell'SDK aggiungendo questa riga
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.13.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Iniziare con un codice boilerplate
1. Sostituire il contenuto del file origine (ad esempio, `sr-quickstart.go`) con il seguente, che include:

- definizione del pacchetto "principale"
- importazione dei moduli necessari dall'SDK di Voce
- variabili per l'archiviazione delle informazioni sulla sottoscrizione che verranno sostituite più avanti in questo argomento di Avvio rapido
- implementazione semplice con il microfono per l'input audio
- gestori dell’evento per diversi eventi che si verificano durante un'interazione con il riconoscimento vocale

```sh
package recognizer

import (
    "bufio"
    "fmt"
    "os"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func recognizingHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognizing:", event.Result.Text)
}

func recognizedHandler(event speech.SpeechRecognitionEventArgs) {
    defer event.Close()
    fmt.Println("Recognized:", event.Result.Text)
}

func cancelledHandler(event speech.SpeechRecognitionCanceledEventArgs) {
    defer event.Close()
    fmt.Println("Received a cancellation: ", event.ErrorDetails)
}

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_SUBSCRIPTIONKEY_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := speech.NewSpeechConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    speechRecognizer, err := speech.NewSpeechRecognizerFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer speechRecognizer.Close()
    speechRecognizer.Recognizing(recognizingHandler)
    speechRecognizer.Recognized(recognizedHandler)
    speechRecognizer.Canceled(cancelledHandler)
    speechRecognizer.StartContinuousRecognitionAsync()
    defer speechRecognizer.StopContinuousRecognitionAsync()
    bufio.NewReader(os.Stdin).ReadBytes('\n')
}
```

Sostituire i valori `YOUR_SUBSCRIPTION_KEY` e `YOUR_SUBSCRIPTIONKEY_REGION` con i valori effettivi della risorsa Voce.

- Passare al portale di Azure e aprire la risorsa Voce
- In **Chiavi** a sinistra sono disponibili due chiavi di sottoscrizione
    - Usare una delle due in sostituzione del valore `YOUR_SUBSCRIPTION_KEY`
- In **Panoramica** a sinistra prendere nota dell'area e associarla all'identificatore di area
- Usare l'identificatore di area in sostituzione del valore `YOUR_SUBSCRIPTIONKEY_REGION`, ad esempio: `"westus"` per **Stati Uniti occidentali**

## <a name="code-explanation"></a>Spiegazione del codice
Per creare un oggetto di configurazione vocale sono necessarie la chiave e l'area della sottoscrizione di Voce. L'oggetto di configurazione è necessario per creare un'istanza di un oggetto riconoscimento vocale.

L'istanza del riconoscimento espone diversi modi per il riconoscimento vocale. In questo esempio, la voce viene riconosciuta in modo continuativo. Questa funzionalità consente al servizio Voce di rilevare che si sta inviando molte frasi per il riconoscimento e di interrompere il riconoscimento vocale una volta terminato il programma. Quando i risultati vengono restituiti, il codice li scrive nella console.

## <a name="build-and-run"></a>Compilazione ed esecuzione
A questo punto è possibile compilare il progetto e testare il riconoscimento vocale con il servizio Voce.
1. Compilare il progetto, ad esempio, **"passare a Compilazione"**
2. Eseguire il modulo e pronunciare una frase o un'espressione nel microfono del dispositivo. Il riconoscimento vocale viene trasmesso al Servizio di riconoscimento vocale e trascritto in formato testo, che appare nell’output.


> [!NOTE]
> Per impostazione predefinita, Speech SDK riconoscerà l'uso di en-us per la lingua. Per informazioni sulla scelta della lingua di origine, vedere [Specificare la lingua di origine per il riconoscimento vocale](../../../../how-to-specify-source-language.md).


## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
