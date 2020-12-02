---
title: 'Avvio rapido: Configurazione della piattaforma per JavaScript (browser) con Speech SDK - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per configurare la piattaforma per l'uso di JavaScript (browser) con il servizio Speech SDK.
services: cognitive-services
author: markamos
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 10/15/2020
ms.author: erhopf
ms.custom: devx-track-js
ms.openlocfilehash: ec73394e184e3c16fbc4eebf2a1090c9e52ddee4
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96188280"
---
Questa guida spiega come installare [Speech SDK](~/articles/cognitive-services/speech-service/speech-sdk.md) per JavaScript per l'uso con una pagina Web.

[!INCLUDE [License Notice](~/includes/cognitive-services-speech-service-license-notice.md)]

## <a name="create-a-new-website-folder"></a>Creare una nuova cartella per il sito Web

Creare una nuova cartella vuota. Se si vuole ospitare l'esempio in un server Web, assicurarsi che il server Web possa accedere alla cartella.

## <a name="unpack-the-speech-sdk-for-javascript-into-that-folder"></a>Decomprimere Speech SDK per JavaScript nella cartella

Scaricare Speech SDK come [pacchetto con estensione zip](https://aka.ms/csspeech/jsbrowserpackage) e decomprimerlo nella nuova cartella. Il risultato è la decompressione di cinque file:
* `microsoft.cognitiveservices.speech.sdk.bundle.js` - Una versione leggibile di Speech SDK.
* `microsoft.cognitiveservices.speech.sdk.bundle.js.map` - Un file di mapping usato per il debug del codice dell'SDK.
* `microsoft.cognitiveservices.speech.sdk.bundle.d.ts` - Definizioni degli oggetti da usare con TypeScript
* `microsoft.cognitiveservices.speech.sdk.bundle-min.js` - Una versione minimizzata di Speech SDK.
* `speech-processor.js` - Codice per migliorare le prestazioni in alcuni browser.

## <a name="create-an-indexhtml-page"></a>Creare una pagina index.html

Creare un nuovo file nella cartella denominato `index.html` e aprirlo con un editor di testo.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [windows](../quickstart-list.md)]