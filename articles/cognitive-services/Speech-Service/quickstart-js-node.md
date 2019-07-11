---
title: 'Guida introduttiva: Riconoscimento vocale, Node.js: servizi Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per creare un'applicazione console di riconoscimento vocale con Speech SDK per Node.js. Al termine, sarà possibile usare il microfono nel computer per trascrivere contenuti vocali in tempo reale.
services: cognitive-services
author: fmegen
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 07/05/2019
ms.author: fmegen
ms.openlocfilehash: 9d233de8a9cdd4b9a3637edcd1c6196b4ad16fd2
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67605122"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-nodejs"></a>Guida introduttiva: Riconoscimento vocale con Speech SDK per Node.js

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo illustra come creare un progetto Node.js usando il binding JavaScript di Speech SDK per i Servizi cognitivi di Azure per la trascrizione del riconoscimento vocale.
L'applicazione si basa su [Speech SDK per JavaScript](https://aka.ms/csspeech/npmpackage).

## <a name="prerequisites"></a>Prerequisiti

* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).
* Una versione corrente di [Node.js](https://nodejs.org).

## <a name="create-a-new-project"></a>Creare un nuovo progetto

Creare una nuova cartella e inizializzare il progetto:

```sh
npm init -f
```

Questo comando inizializza i file **package.json** con i valori predefiniti. Probabilmente si vorrà modificare questo file in un secondo momento.

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

Aggiungere Speech SDK al progetto Node.js:

```
npm install microsoft-cognitiveservices-speech-sdk
```

Questo comando scarica e installa l'ultima versione di Speech SDK con i prerequisiti necessari da **npmjs**. L'SDK viene installato nella directory `node_modules` all'interno della cartella di progetto.

## <a name="use-the-speech-sdk"></a>Usare Speech SDK

Creare un nuovo file nella cartella denominato `index.js` e aprirlo con un editor di testo.

> [!NOTE]
> In Node.js, Speech SDK non supporta il microfono o il tipo di dati **File**. Entrambi sono supportati solo nei browser. In alternativa, usare l'interfaccia **Stream** per Speech SDK, tramite `AudioInputStream.createPushStream()` o `AudioInputStream.createPullStream()`.

In questo esempio si usa l'interfaccia `PushAudioInputStream`.

Aggiungere questo codice JavaScript:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="run-the-sample"></a>Eseguire l'esempio

Per aprire l'app, adattare `YourSubscriptionKey`, `YourServiceRegion` e `YourAudioFile.wav` alla propria configurazione. Quindi eseguirla chiamando questo comando:

```sh
node index.js
```

Il comando attiva un riconoscimento con il nome del file specificato. Quindi, presenta l'output nella console.

Questo esempio è l'output che si ottiene quando si esegue `index.js` dopo aver aggiornato la chiave di sottoscrizione e usato il file `whatstheweatherlike.wav`:

```json
SpeechRecognitionResult {
  "privResultId": "9E30EEBD41AC4571BB77CF9164441F46",
  "privReason": 3,
  "privText": "What's the weather like?",
  "privDuration": 15900000,
  "privOffset": 300000,
  "privErrorDetails": null,
  "privJson": {
    "RecognitionStatus": "Success",
    "DisplayText": "What's the weather like?",
    "Offset": 300000,
    "Duration": 15900000
  },
  "privProperties": null
}
```

## <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installare e usare Speech SDK con Visual Studio Code

È anche possibile eseguire l'esempio da Visual Studio Code. Seguire questi passaggi per installare, aprire ed eseguire il progetto di avvio rapido:

1. Avviare Visual Studio Code. Selezionare **Apri cartella**. Cercare quindi la cartella di avvio rapido.

   ![Aprire la cartella](media/sdk/qs-js-node-01-open_project.png)

1. Aprire una finestra del terminale in Visual Studio Code.

   ![Finestra del terminale](media/sdk/qs-js-node-02_open_terminal.png)

1. Eseguire `npm` per installare le dipendenze.

   ![npm install](media/sdk/qs-js-node-03-npm_install.png)

1. È ora possibile aprire `index.js`e impostare un punto di interruzione.

   ![index.js con un punto di interruzione alla riga 16](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Per avviare il debug, premere F5 o selezionare **Debug/Avvia debug** dal menu.

   ![Menu di debug](media/sdk/qs-js-node-05-start_debugging.png)

1. Quando viene raggiunto un punto di interruzione, è possibile esaminare le variabili e lo stack di chiamate.

   ![Debugger](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. L'output viene visualizzato nella finestra della console di debug.

   ![Console di debug](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Node.js su GitHub](https://aka.ms/csspeech/samples)
