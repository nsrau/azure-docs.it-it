---
author: trrwilson
ms.service: cognitive-services
ms.topic: include
ms.date: 05/25/2020
ms.author: trrwilson
ms.openlocfilehash: 59e862fe04e7bf715e6e44c783f2cf9c0ecbc7c3
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91376464"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../overview.md#try-the-speech-service-for-free)
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md)
> * Creare un bot connesso al [canale Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech)
> * Assicurarsi di avere accesso a un microfono per l'acquisizione audio
>
  > [!NOTE]
  > Consultare [l'elenco delle aree supportate per gli assistenti vocali](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) e assicurarsi che le risorse vengano distribuite in una di queste aree.

## <a name="setup-your-environment"></a>Configurare l'ambiente

Aggiornare il file go.mod con la versione più recente dell'SDK aggiungendo questa riga
```sh
require (
    github.com/Microsoft/cognitive-services-speech-sdk-go v1.13.0
)
```

## <a name="start-with-some-boilerplate-code"></a>Iniziare con un codice boilerplate
Sostituire il contenuto del file origine (ad esempio, `quickstart.go`) con il seguente, che include:

- definizione del pacchetto "principale"
- importazione dei moduli necessari dall'SDK di Voce
- variabili per l'archiviazione delle informazioni bot che verranno sostituite più avanti in questo argomento di Avvio rapido
- implementazione semplice con il microfono per l'input audio
- gestori dell’evento per diversi eventi che si verificano durante un'interazione con il riconoscimento vocale

```sh
package main

import (
    "fmt"
    "time"

    "github.com/Microsoft/cognitive-services-speech-sdk-go/audio"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/dialog"
    "github.com/Microsoft/cognitive-services-speech-sdk-go/speech"
)

func main() {
    subscription :=  "YOUR_SUBSCRIPTION_KEY"
    region := "YOUR_BOT_REGION"

    audioConfig, err := audio.NewAudioConfigFromDefaultMicrophoneInput()
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer audioConfig.Close()
    config, err := dialog.NewBotFrameworkConfigFromSubscription(subscription, region)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer config.Close()
    connector, err := dialog.NewDialogServiceConnectorFromConfig(config, audioConfig)
    if err != nil {
        fmt.Println("Got an error: ", err)
        return
    }
    defer connector.Close()
    activityReceivedHandler := func(event dialog.ActivityReceivedEventArgs) {
        defer event.Close()
        fmt.Println("Received an activity.")
    }
    connector.ActivityReceived(activityReceivedHandler)
    recognizedHandle := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognized ", event.Result.Text)
    }
    connector.Recognized(recognizedHandle)
    recognizingHandler := func(event speech.SpeechRecognitionEventArgs) {
        defer event.Close()
        fmt.Println("Recognizing ", event.Result.Text)
    }
    connector.Recognizing(recognizingHandler)
    connector.ListenOnceAsync()
    <-time.After(10 * time.Second)
}
```

Sostituire i valori `YOUR_SUBSCRIPTION_KEY` e `YOUR_BOT_REGION` con i valori effettivi della risorsa Voce.

- Passare al portale di Azure e aprire la risorsa Voce
- In **Chiavi ed endpoint** a sinistra sono disponibili due chiavi di sottoscrizione
    - Usare una delle due in sostituzione del valore `YOUR_SUBSCRIPTION_KEY`
- In **Panoramica** a sinistra prendere nota dell'area e associarla all'identificatore di area
    - Usare l'identificatore di area in sostituzione del valore `YOUR_BOT_REGION`, ad esempio `"westus"` per **Stati Uniti occidentali**

   > [!NOTE]
   > Consultare [l'elenco delle aree supportate per gli assistenti vocali](~/articles/cognitive-services/speech-service/regions.md#voice-assistants) e assicurarsi che le risorse vengano distribuite in una di queste aree.

   > [!NOTE]
   > Per informazioni sulla configurazione del bot, vedere la documentazione di Bot Framework relativa al [canale Direct Line Speech](https://docs.microsoft.com/azure/bot-service/bot-service-channel-connect-directlinespeech).

## <a name="code-explanation"></a>Spiegazione del codice
Per creare un oggetto di configurazione vocale sono necessarie la chiave e l'area della sottoscrizione di Voce. L'oggetto di configurazione è necessario per creare un'istanza di un oggetto riconoscimento vocale.

L'istanza del riconoscimento espone diversi modi per il riconoscimento vocale. In questo esempio, la voce viene riconosciuta in modo continuativo. Questa funzionalità consente al servizio Voce di rilevare che si sta inviando molte frasi per il riconoscimento e di interrompere il riconoscimento vocale una volta terminato il programma. Quando i risultati vengono restituiti, il codice li scrive nella console.

## <a name="build-and-run"></a>Compilazione ed esecuzione
A questo punto è possibile configurare per compilare il progetto e testare l'assistente vocale personalizzato con il servizio Voce.
1. Compilare il progetto, ad esempio, **"passare a Compilazione"**
2. Eseguire il modulo e pronunciare una frase o un'espressione nel microfono del dispositivo. Il contenuto vocale viene trasmesso al canale Direct Line Speech e trascritto come testo che viene come output.


> [!NOTE]
> Per impostazione predefinita, Speech SDK riconoscerà l'uso di en-us per la lingua. Per informazioni sulla scelta della lingua di origine, vedere [Specificare la lingua di origine per il riconoscimento vocale](../../../../how-to-specify-source-language.md).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]
