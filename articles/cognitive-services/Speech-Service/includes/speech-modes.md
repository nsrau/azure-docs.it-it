---
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 01/22/2020
ms.author: trbye
ms.openlocfilehash: d97a1bf00d722414bedda4be79adc0c26d02c751
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/29/2020
ms.locfileid: "81422180"
---
## <a name="speech-modes"></a>Modalità sintesi vocale

**Interactive (Interattivo)**
- Progettato per scenari di comando e controllo.
- Ha un valore di timeout di segmentazione pari a X.
- Alla fine di un'espressione riconosciuta, il servizio interrompe l'elaborazione dell'audio dall'ID richiesta e termina il turno. Connessione non chiusa.
- Il limite massimo per il riconoscimento è 20s.
- La tipica chiamata a CO2 da `RecognizeOnceAsync`richiamare è.

**Conversazione**
- Progettato per riconoscimenti con esecuzione prolungata.
- Ha un valore di timeout segmentazione di Y. (Y! = X)
- Elaborerà più espressioni complete senza terminare il turno.
- Termina il turno per un troppo silenzio.
- Il carbonio continuerà con un nuovo ID richiesta e riprodurrà l'audio in base alle esigenze.
- Il servizio si disconnetterà forzatamente dopo 10 minuti di riconoscimento vocale.
- Il carbonio si riconnetterà e riprodurrà audio non riconosciuto.
- Richiamato in Carbon `StartContinuousRecognition`con.

**Dettatura**
- Consente agli utenti di specificare la punteggiatura parlando.
- Richiamato in Carbon specificando `EnableDictation` sull' `SpeechConfig` oggetto indipendentemente dalla chiamata API che avvia il riconoscimento.
- Il cluster<sup>di terze</sup> parti `speech.fragment` restituisce messaggi per i risultati intermedi,<sup>i 3</sup> messaggi `speech.hypothesis` restituiti da terze parti.