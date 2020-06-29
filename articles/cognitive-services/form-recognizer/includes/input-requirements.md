---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 11783f8fac147e6fb2cf371ee1a588cf318a5e9c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "75379435"
---
Il riconoscimento modulo funziona su documenti di input che soddisfano questi requisiti:

* Il formato deve essere JPG, PNG, PDF (testo o digitalizzato) o TIFF. I documenti PDF con testo incorporato sono i più adatti perché non vi è alcuna possibilità di errore nell'estrazione e individuazione dei caratteri.
* Se i file PDF sono bloccati da password, è necessario rimuovere il blocco prima di inviarli.
* I documenti PDF e TIFF devono essere costituiti da un massimo di 200 pagine e le dimensioni totali del set di dati di training non devono superare le 500 pagine.
* Per le immagini, le dimensioni devono essere comprese tra 600 x 100 pixel e 4200 x 4200 pixel.
* Se digitalizzati da documenti cartacei, i moduli devono essere scansioni di alta qualità.
* Per il testo deve essere usato l'alfabeto latino (caratteri in lingua inglese).
* Per l'apprendimento non supervisionato (senza dati etichettati), i dati devono contenere chiavi e valori.
* Per l'apprendimento non supervisionato (senza dati etichettati), le chiavi devono essere visualizzate sopra o a sinistra dei valori; non possono essere visualizzate sotto o a destra.

Il riconoscimento modulo attualmente non supporta questi tipi di dati di input:

* Tabelle complesse (tabelle annidate, intestazioni o celle unite e così via).
* Caselle di controllo o pulsanti di opzione.
