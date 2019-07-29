---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 17dc32f8948387b90229d3c4c07102cff98e3018
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562673"
---
Il riconoscimento modulo funziona su documenti di input che soddisfano questi requisiti:

* Il formato deve essere JPG, PNG o PDF (testo o digitalizzato). I documenti PDF con testo incorporato sono i più adatti perché non vi è alcuna possibilità di errore nell'estrazione e individuazione dei caratteri.
* Se i file PDF sono bloccati da password, è necessario rimuovere il blocco prima di inviarli.
* Le dimensioni dei file devono essere minori di 4 MB.
* Per le immagini, le dimensioni devono essere comprese tra 50 x 50 pixel e 4200 x 4200 pixel.
* Se digitalizzati da documenti cartacei, i moduli devono essere scansioni di alta qualità.
* Per il testo deve essere usato l'alfabeto latino (caratteri in lingua inglese).
* I dati devono contenere chiavi e valori.
* Le chiavi possono essere visualizzate sopra o a sinistra dei valori, ma non sotto o a destra.

Il riconoscimento modulo attualmente non supporta questi tipi di dati di input:

* Tabelle complesse (tabelle annidate, intestazioni o celle unite e così via).
* Caselle di controllo o pulsanti di opzione.
* Documenti PDF con più di 50 pagine.