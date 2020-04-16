---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81422180"
---
## <a name="speech-modes"></a>Modalità di riconoscimento vocale

**Interattiva**
- Destinato agli scenari di comando e controllo.
- Ha un valore di timeout di segmentazione pari a X.
- Al termine di un'espressione riconosciuta, il servizio interrompe l'elaborazione dell'audio da tale ID richiesta e termina il turno. La connessione non viene chiusa.
- Il limite massimo per il riconoscimento è 20s.
- La tipica chiamata `RecognizeOnceAsync`Carbon a richiamare è .

**Conversazione**
- Pensato per riconoscimenti a esecuzione prolungata.
- Ha un valore di timeout di segmentazione pari a Y. (Y !
- Verranno elaborate più espressioni complete senza terminare il turno.
- Finirà il turno per troppo silenzio.
- Carbon continuerà con un nuovo ID richiesta e riprodurrà l'audio in base alle esigenze.
- Il servizio si disconnetterà con la voce dopo 10 minuti di riconoscimento vocale.
- Il carbonio riconnetterà e riprodurrà l'audio non riconosciuto.
- Richiamato in `StartContinuousRecognition`carbonio con .

**Dettatura**
- Consente agli utenti di specificare la punteggiatura pronunciandola.
- Richiamato in Carbon `EnableDictation` specificando `SpeechConfig` sull'oggetto indipendentemente dalla chiamata API che inizia il riconoscimento.
- Il<sup>st</sup> cluster di `speech.fragment` 1 st party restituisce messaggi `speech.hypothesis` per i risultati intermedi, i messaggi di ritorno di terze<sup>parti.</sup>