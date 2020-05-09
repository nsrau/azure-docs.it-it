---
title: 'Assistente vocale in Windows: introduzione'
titleSuffix: Azure Cognitive Services
description: Passaggi per iniziare lo sviluppo di un agente Windows Voice, incluso un riferimento alla Guida introduttiva al codice di esempio.
services: cognitive-services
author: cfogg6
manager: trrwilson
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: travisw
ms.openlocfilehash: 4ea2f88b02738645af3f8fc32d5fdb99168a1122
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 05/09/2020
ms.locfileid: "82997381"
---
# <a name="getting-started-with-voice-assistants-on-windows"></a>Introduzione agli assistenti vocali in Windows

Questa guida illustra i passaggi necessari per iniziare a sviluppare un assistente vocale in Windows.

## <a name="set-up-your-development-environment"></a>Configurazione dell'ambiente di sviluppo

Per iniziare a sviluppare un assistente vocale per Windows, è necessario assicurarsi di avere un ambiente di sviluppo appropriato.

- **Visual Studio:** è necessario installare [Microsoft Visual Studio 2017](https://visualstudio.microsoft.com/), Community Edition o versione successiva
- **Versione di Windows**: un PC con una build di Windows Insider Fast ring di Windows e la versione di Windows insider del Windows SDK.Questo codice di esempio viene verificato come funzionante in Windows Insider release build 19025. vb_release_analog .191112-1600 con Windows SDK 19018.Qualsiasi compilazione o SDK sopra le versioni specificate dovrebbe essere compatibile.
- **Strumenti di sviluppo UWP**: il carico di lavoro di sviluppo piattaforma UWP (Universal Windows Platform) in Visual Studio.Vedere la pagina UWP [get set up](https://docs.microsoft.com/windows/uwp/get-started/get-set-up) per preparare il computer per lo sviluppo di applicazioni UWP.
- **Un microfono funzionante e un output audio**

## <a name="obtain-resources-from-microsoft"></a>Ottenere risorse da Microsoft

Alcune risorse necessarie per un agente vocale completamente personalizzato in Windows richiederanno risorse da Microsoft. L' [esempio UWP Voice Assistant](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample) fornisce versioni di esempio di queste risorse per lo sviluppo e il test iniziali, quindi questa sezione non è necessaria per lo sviluppo iniziale.

- **Modello keyword:** per l'attivazione vocale è necessario un modello di parola chiave di Microsoft sotto forma di file con estensione bin. Il file con estensione bin fornito nell'esempio UWP Voice Assistant viene sottoposto a training sulla parola chiave "contoso".
- **Token della funzionalità di accesso limitato:** Poiché le API di ConversationalAgent forniscono l'accesso all'audio del microfono, sono protette da restrizioni sulle funzionalità di accesso limitato.Per usare una funzionalità di accesso limitato, è necessario ottenere un token di funzionalità di accesso limitato connesso all'identità del pacchetto dell'applicazione da Microsoft.

## <a name="establish-a-dialog-service"></a>Stabilire un servizio di dialogo

Per un'esperienza di Assistente vocale completa, l'applicazione dovrà disporre di un servizio di dialogo che

- Rilevare una parola chiave in un file audio specificato
- Ascoltare l'input dell'utente e convertirlo in testo
- Fornire il testo a un bot
- Tradurre la risposta testuale del bot in un output audio

Questi sono i requisiti per la creazione di un servizio di dialogo di base con la voce direct line.

- **Sottoscrizione di servizi vocali:** Sottoscrizione per i servizi di riconoscimento vocale cognitivi per le conversioni di sintesi vocale e sintesi vocale. Prova i servizi di riconoscimento vocale gratuitamente [qui](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started).
- Bot **Framework bot:**  Bot creato con bot Framework versione 4,2 o successiva che ha effettuato la sottoscrizione a [Direct Line Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/direct-line-speech) per abilitare l'input e l'output vocali. [Questa guida](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk) contiene istruzioni dettagliate per creare un "Echo bot" e sottoscriverlo per la comunicazione diretta con la linea vocale. È anche possibile fare clic [qui](https://blog.botframework.com/2018/05/07/build-a-microsoft-bot-framework-bot-with-the-bot-builder-sdk-v4/) per i passaggi per la creazione di un bot personalizzato, quindi seguire la [stessa procedura per](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk) sottoscriverlo per indirizzare il discorso alla riga, ma con il nuovo bot invece che con "Echo bot".

## <a name="try-out-the-sample-app"></a>Provare l'app di esempio

Con la chiave di sottoscrizione dei servizi vocali e l'ID bot di Echo bot, si è pronti per provare l' [esempio UWP Voice Assistant](windows-voice-assistants-faq.md#the-uwp-voice-assistant-sample). Seguire le istruzioni nel file Leggimi per eseguire l'app e immettere le credenziali.

## <a name="create-your-own-voice-assistant-for-windows"></a>Creazione di un assistente vocale personalizzato per Windows

Dopo aver ricevuto il token della funzionalità di accesso limitato e il file bin da Microsoft, è possibile iniziare con l'Assistente vocale in Windows.

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Leggi la Guida all'implementazione di Assistente vocale](windows-voice-assistants-implementation-guide.md)
