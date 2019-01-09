---
title: 'Avvio rapido: Riconoscimento vocale in JavaScript in Node.js con Speech Service SDK'
titleSuffix: Azure Cognitive Services
description: Informazioni sul riconoscimento vocale in JavaScript in Node.js con Speech Service SDK
services: cognitive-services
author: fmegen
manager: cgronlun
ms.service: cognitive-services
ms.component: speech-service
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: fmegen
ms.openlocfilehash: 35652b169067bc545fa0d1fcc977bbaee79ec3aa
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/21/2018
ms.locfileid: "53724434"
---
# <a name="quickstart-recognize-speech-in-javascript-in-nodejs-using-the-speech-service-sdk"></a>Avvio rapido: Riconoscimento vocale in JavaScript in Node.js con Speech Service SDK

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo spiega come creare un progetto Node.js usando il binding JavaScript di Speech SDK di Servizi cognitivi per la trascrizione del riconoscimento vocale.
L'applicazione si basa su [Speech SDK di Servizi cognitivi](https://aka.ms/csspeech/npmpackage) Microsoft.

## <a name="prerequisites"></a>Prerequisiti

* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).
* Una versione corrente di [Node.js](https://nodejs.org).

## <a name="create-a-new-project-folder"></a>Creare una nuova cartella di progetto

Creare una nuova cartella vuota e inizializzarla come nuovo progetto JavaScript e Node.js.

```sh
npm init -f
```

In questo modo i file package.json verranno inizializzati con i valori predefiniti. Probabilmente si vorrà modificare questo file in un secondo momento.

## <a name="install-the-speech-sdk-for-javascript-into-that-folder"></a>Installare Speech SDK per JavaScript nella cartella

Aggiungere Speech SDK al progetto Node.js con `npm install microsoft-cognitiveservices-speech-sdk`.

L'ultima versione di Speech SDK con i prerequisiti necessari verrà scaricata e installata da npmjs. L'SDK verrà installato nella directory `node_modules` all'interno della cartella di progetto.

## <a name="using-the-speech-sdk"></a>Uso di Speech SDK

Creare un nuovo file nella cartella denominato `index.js` e aprirlo con un editor di testo.

> [!NOTE]
> Si noti che in Node.js Speech SDK non supporta il microfono o il tipo di dati File. Entrambi sono supportati solo nei browser. In alternativa, usare l'interfaccia Stream per Speech SDK, tramite `AudioInputStream.createPushStream()` o `AudioInputStream.createPullStream()`.

In questo esempio si userà l'interfaccia `PushAudioInputStream`.

Aggiungere il codice JavaScript seguente:

[!code-javascript[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/js-node/index.js#code)]

## <a name="running-the-sample-from-command-line"></a>Esecuzione dell'esempio dalla riga di comando

Per avviare l'app, adattare `YourSubscriptionKey`, `YourServiceRegion` e `YourAudioFile.wav` alla propria configurazione. È quindi possibile eseguirlo chiamando il comando seguente:

```sh
node index.js
```

Il comando attiverà un riconoscimento con il nome file specificato e presenterà il risultato nella console.

Di seguito è riportato l'output dell'esecuzione di `index.js` dopo l'aggiornamento della chiave di sottoscrizione, usando il file `whatstheweatherlike.wav`.

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

## <a name="running-the-sample-from-visual-studio-code"></a>Esecuzione dell'esempio da Visual Studio Code

È possibile eseguire l'esempio anche da Visual Studio Code. Seguire questi passaggi per installare, aprire ed eseguire il progetto di avvio rapido:

1. Avviare Visual Studio Code e fare clic su "Apri cartella", quindi passare alla cartella del progetto di avvio rapido

   ![Screenshot di Apri cartella](media/sdk/qs-js-node-01-open_project.png)

1. Aprire una finestra del terminale in Visual Studio Code

   ![Screenshot della finestra del terminale](media/sdk/qs-js-node-02_open_terminal.png)

1. Eseguire npm per installare le dipendenze

   ![Screenshot di npm install](media/sdk/qs-js-node-03-npm_install.png)

1. È ora possibile aprire `index.js`e impostare un punto di interruzione

   ![Screenshot di index.js con un punto di interruzione alla riga 16](media/sdk/qs-js-node-04-setup_breakpoint.png)

1. Per avviare il debug, premere F5 o selezionare Debug/Avvia debug dal menu

   ![Screenshot del menu di debug](media/sdk/qs-js-node-05-start_debugging.png)

1. Quando viene raggiunto un punto di interruzione, è possibile esaminare le variabili e lo stack di chiamate

   ![Screenshot del debugger](media/sdk/qs-js-node-06-hit_breakpoint.png)

1. L'output verrà visualizzato nella finestra della console di debug

   ![Screenshot della console di debug](media/sdk/qs-js-node-07-debug_output.png)

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Node.js su GitHub](https://aka.ms/csspeech/samples)
