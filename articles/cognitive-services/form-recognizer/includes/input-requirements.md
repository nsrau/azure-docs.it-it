---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 816fa4b0df2fab77bcde261f30d8bad584257fd7
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592627"
---
Il riconoscimento modulo funziona su documenti di input che soddisfano questi requisiti:

* Il formato deve essere JPG, PNG o PDF (testo o digitalizzato). I documenti PDF con testo incorporato sono i più adatti perché non vi è alcuna possibilità di errore nell'estrazione e individuazione dei caratteri.
* Le dimensioni del file devono essere inferiori a 4 megabyte (MB).
* Per le immagini, le dimensioni devono essere comprese tra 50 x 50 pixel e 4200 x 4200 pixel.
* Se digitalizzati da documenti cartacei, i moduli devono essere scansioni di alta qualità.
* Per il testo deve essere usato l'alfabeto latino (caratteri in lingua inglese).
* I dati devono essere stampati (non scritti a mano).
* I dati devono contenere chiavi e valori.
* Le chiavi possono essere visualizzate sopra o a sinistra dei valori, ma non sotto o a destra.

Il riconoscimento modulo attualmente non supporta questi tipi di dati di input:

* Tabelle complesse (tabelle annidate, intestazioni o celle unite e così via).
* Caselle di controllo o pulsanti di opzione.
* Documenti PDF con più di 50 pagine.