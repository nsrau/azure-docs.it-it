---
title: 'Guida introduttiva: Riconoscimento vocale in JavaScript in un browser con Speech SDK di Servizi cognitivi'
titleSuffix: Microsoft Cognitive Services
description: Informazioni sul riconoscimento vocale in JavaScript in un browser con Speech SDK di Servizi cognitivi
services: cognitive-services
author: fmegen
ms.service: cognitive-services
ms.component: Speech
ms.topic: article
ms.date: 10/12/2018
ms.author: fmegen
ms.openlocfilehash: 2667d398141b53c87328eec7b608c4f613c15ea4
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/16/2018
ms.locfileid: "49340274"
---
# <a name="quickstart-recognize-speech-in-javascript-in-a-browser-using-the-cognitive-services-speech-sdk"></a>Guida introduttiva: Riconoscimento vocale in JavaScript in un browser con Speech SDK di Servizi cognitivi

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo spiega come creare un sito Web usando il binding JavaScript di Speech SDK di Servizi cognitivi per la trascrizione del riconoscimento vocale.
L'applicazione si basa su Speech SDK di Servizi cognitivi Microsoft ([Download della versione 1.0.1](https://aka.ms/csspeech/jsbrowserpackage)).

## <a name="prerequisites"></a>Prerequisiti

* Una chiave di sottoscrizione per il servizio di riconoscimento vocale. Vedere [Provare gratuitamente il Servizio di riconoscimento vocale](get-started.md).
* Un PC o Mac con un microfono funzionante.
* Un editor di testo.
* Una versione corrente di Microsoft Edge o Chrome.
* Facoltativamente, un server Web che supporti l'hosting di script PHP.

## <a name="create-a-new-website-folder"></a>Creare una nuova cartella per il sito Web

Creare una nuova cartella vuota. Se si vuole ospitare l'esempio in un server Web, assicurarsi che il server Web possa accedere alla cartella.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Decomprimere Speech SDK per JavaScript nella cartella

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Scaricare Speech SDK come [pacchetto con estensione zip](https://aka.ms/csspeech/jsbrowserpackage) e decomprimerlo nella nuova cartella. Verranno decompressi due file, `microsoft.cognitiveservices.speech.sdk.bundle.js` e `microsoft.cognitiveservices.speech.sdk.bundle.js.map`.
Quest'ultimo file è facoltativo e viene usato per consentire operazioni di debug nel codice SDK, se necessario.

## <a name="create-an-indexhtml-page"></a>Creare una pagina index.html

Creare un nuovo file nella cartella denominato `index.html` e aprirlo con un editor di testo.

1. Creare la struttura HTML seguente:

  ```html
  <html>
  <head>
      <title>Microsoft Cognitive Service Speech SDK JavaScript Quickstart</title>
  </head>
  <body>
    <!-- UI code goes here -->

    <!-- SDK reference goes here -->

    <!-- Optional authorization token request goes here -->

    <!-- Sample code goes here -->
  </body>
  </html>
  ```

1. Aggiungere il codice dell'interfaccia utente seguente al file, dopo il primo commento:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#uidiv)]

1. Aggiungere un riferimento a Speech SDK

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#speechsdkref)]

1. Collegare i gestori per il pulsante del riconoscimento, il risultato del riconoscimento e i campi correlati alla sottoscrizione definiti dal codice dell'interfaccia utente:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#quickstartcode)]

## <a name="create-the-token-source-optional"></a>Creare l'origine del token (facoltativo)

Se si vuole ospitare la pagina Web in un server Web, facoltativamente è possibile specificare un'origine del token per l'applicazione demo.
In questo modo la chiave di sottoscrizione non lascerà mai il server, consentendo agli utenti di usare le funzionalità di riconoscimento vocale senza dover immettere codici di autorizzazione.

1. Creare un file denominato `token.php`. In questo esempio si presuppone che il server Web supporti il linguaggio di scripting PHP. Immettere il codice seguente:

  [!code-php[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/token.php)]

1. Modificare il file `index.html` e aggiungere il codice seguente:

  [!code-html[](~/samples-cognitive-services-speech-sdk/quickstart/js-browser/index.html#authorizationfunction)]

> [!NOTE]
> I token di autorizzazione hanno una durata limitata.
> Questo esempio semplificato non illustra come aggiornare automaticamente i token di autorizzazione. Come utente, è possibile ricaricare la pagina manualmente o premere F5 per aggiornare.

## <a name="build-and-run-the-sample-locally"></a>Compilare ed eseguire l'esempio in locale

Per avviare l'app, fare doppio clic sul file index.html o aprirlo con il Web browser preferito. Presenterà una semplice interfaccia utente grafica, che consente di immettere la chiave di sottoscrizione e l'[area](regions.md) e attivare un riconoscimento tramite il microfono.

## <a name="build-and-run-the-sample-via-a-web-server"></a>Compilare ed eseguire l'esempio tramite un server Web

Per avviare l'app, aprire il Web browser preferito e passare all'URL pubblico in cui è ospitata, immettere l'[area](regions.md) e attivare un riconoscimento tramite il microfono. Se il comportamento è configurato, verrà acquisito un token dall'origine del token.

[!INCLUDE [Download the sample](../../../includes/cognitive-services-speech-service-speech-sdk-sample-download-h2.md)]
Ricercare questo esempio nella cartella `quickstart/js-browser`.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Scaricare gli esempi](speech-sdk.md#get-the-samples)
