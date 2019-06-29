---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 690219347782aad2140b0a1b0541590c5426c568
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67459735"
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