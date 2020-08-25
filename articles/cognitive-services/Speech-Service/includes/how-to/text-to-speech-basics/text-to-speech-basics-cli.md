---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 08/11/2020
ms.author: trbye
ms.openlocfilehash: bee28c946242ecf227287fb36b5b03aa6defb1c2
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 08/13/2020
ms.locfileid: "88170140"
---
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