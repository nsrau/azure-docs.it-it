---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: bd48618a520f547c72bfba7ff04ae6a249d0a673
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806484"
---
## <a name="run-the-speech-cli"></a>Eseguire l'interfaccia della riga di comando per Voce

A questo punto si è pronti per eseguire l'interfaccia della riga di comando per Voce per sintetizzare la sintesi vocale da testo in un nuovo file audio.

Dalla riga di comando passare alla directory che contiene il file binario dell'interfaccia a riga di comando per Voce e digitare:

```bash
spx synthesize --text "The speech synthesizer greets you!" --audio output greetings.wav
```

L'interfaccia della riga di comando per Voce produrrà il linguaggio naturale in inglese nel file audio `greetings.wav`.
In Windows è possibile riprodurre il file audio immettendo `start greetings.wav`.
