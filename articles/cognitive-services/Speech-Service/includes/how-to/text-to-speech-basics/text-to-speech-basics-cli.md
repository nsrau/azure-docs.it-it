---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: b68025c1b33a94f03e2d84693a7d6407a18abd88
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332469"
---
Questo argomento di avvio rapido illustra i modelli di progettazione comuni per eseguire la sintesi vocale con Speech SDK. Si inizia con la configurazione di base e la sintesi e si passa ad esempi più avanzati per lo sviluppo di applicazioni personalizzate, tra cui:

* Recupero di risposte come flussi in memoria
* Personalizzazione della frequenza di campionamento e della velocità in bit dell'output
* Invio di richieste di sintesi tramite SSML (Speech Synthesis Markup Language)
* Uso di voci neurali

## <a name="prerequisites"></a>Prerequisiti

Questo articolo presuppone che si abbia un account Azure e una sottoscrizione del servizio Voce. Se l'account e la sottoscrizione non sono disponibili, [provare il servizio Voce gratuitamente](../../../overview.md#try-the-speech-service-for-free).

[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="synthesize-speech-to-a-speaker"></a>Sintetizzare la voce in un altoparlante

A questo punto si è pronti per eseguire l'interfaccia della riga di comando per Voce per sintetizzare la sintesi vocale da testo. Dalla riga di comando passare alla directory che contiene il file binario dell'interfaccia della riga di comando per Voce. Quindi eseguire il comando seguente.

```bash
spx synthesize --text "The speech synthesizer greets you!"
```

L'interfaccia della riga di comando per Voce produrrà il linguaggio naturale in inglese attraverso l’altoparlante del computer.

## <a name="synthesize-speech-to-a-file"></a>Sintetizzare la voce in un file

Eseguire il comando seguente per sostituire l'output del relatore con un file `.wav`.

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

L'interfaccia della riga di comando per Voce produrrà il linguaggio naturale in inglese nel file audio `greetings.wav`.
In Windows è possibile riprodurre il file audio immettendo `start greetings.wav`.