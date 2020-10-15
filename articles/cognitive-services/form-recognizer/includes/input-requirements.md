---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/27/2019
ms.author: pafarley
ms.openlocfilehash: 5de121a1a905a58f8b5eaf8e60c1f8da71ee8cd6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91276944"
---
Il riconoscimento modulo funziona su documenti di input che soddisfano questi requisiti:

* Il formato deve essere JPG, PNG, PDF (testo o digitalizzato) o TIFF. I documenti PDF con testo incorporato sono i più adatti perché non vi è alcuna possibilità di errore nell'estrazione e individuazione dei caratteri.
* Le dimensioni dei file devono essere minori di 20 MB.
* Per le immagini, le dimensioni devono essere comprese tra 50 x 50 pixel e 10000 x 10000 pixel.
* Le dimensioni dei PDF devono essere al massimo di 17 x 17 pollici, corrispondenti a formati della carta Legal o A3 e inferiori.
* Per PDF e TIFF, vengono elaborate solo le prime 200 pagine (con una sottoscrizione di livello gratuito, vengono elaborate solo le prime due pagine).
* Le dimensioni totali del training set non devono superare le 500 pagine.
* Se i file PDF sono bloccati da password, è necessario rimuovere il blocco prima di inviarli.
* Se digitalizzati da documenti cartacei, i moduli devono essere scansioni di alta qualità.
* Per il testo deve essere usato l'alfabeto latino (caratteri in lingua inglese).
* Per l'apprendimento non supervisionato (senza dati etichettati), i dati devono contenere chiavi e valori.
* Per l'apprendimento non supervisionato (senza dati etichettati), le chiavi devono essere visualizzate sopra o a sinistra dei valori; non possono essere visualizzate sotto o a destra.

Il riconoscimento modulo attualmente non supporta questi tipi di dati di input:

* Tabelle complesse (tabelle annidate, intestazioni o celle unite e così via).
* Caselle di controllo o pulsanti di opzione.
