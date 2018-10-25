---
title: Domande frequenti - API Emozioni
titlesuffix: Azure Cognitive Services
description: Ottenere risposte a domande frequenti relative all'API Emozioni.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 6c1c4b8e5c2701f3c419a58bc3fdc33f7e629bbd
ms.sourcegitcommit: 1981c65544e642958917a5ffa2b09d6b7345475d
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/03/2018
ms.locfileid: "48238538"
---
# <a name="emotion-api-frequently-asked-questions"></a>Domande frequenti sull'API Emozioni

> [!IMPORTANT]
> L'API Emozioni verrà deprecata il 15 febbraio 2019. La funzionalità di riconoscimento delle emozioni è ora generalmente disponibile nell'ambito dell'[API Viso](https://docs.microsoft.com/azure/cognitive-services/face/).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Se non è possibile trovare risposte alle proprie domande in queste domande frequenti, provare a porre la domanda alla community dell'API Emozioni su [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) oppure contattare il [supporto tecnico da UserVoice](https://cognitive.uservoice.com/).  

-----

**Domanda**: *Quali tipi di immagini ottengono i risultati migliori dall'API Emozioni?*

**Risposta**: Per ottenere risultati ottimali, usare immagini del viso in posizione frontale e non nascosto. L'affidabilità diminuisce con immagini del viso parzialmente frontale e l'API Emozioni potrebbe non riconoscere le emozioni nelle immagini in cui il viso è ruotato di oltre 45 gradi.

-----

**Domanda**: *Quante emozioni può identificate l'API Emozioni?*

**Risposta**: L'API Emozioni riconosce otto diverse emozioni universalmente riconosciute:
* Felicità
* Tristezza
* Sorpresa
* Rabbia
* Paura
* Biasimo
* Disgusto
* Neutralità

-----

**Domanda**: *Esiste un modo per passare all'API un video in diretta streaming e ricevere contemporaneamente il risultato?*

**Risposta**: Usare l'API Emozioni basata su immagini e chiamarla su ogni frame o ignorare i frame per le prestazioni.  Sono disponibili esempi di analisi fotogramma per fotogramma di video.

-----

**Domanda**: *Sto passando i dati immagine binari ma ricevo un errore "Immagine del viso non valida".**

**Risposta**: Questo messaggio significa che l'algoritmo non è riuscito ad analizzare l'immagine.  
* I formati di immagine di input supportati includono JPEG, PNG, GIF (il primo frame), BMP
* La dimensione del file di immagine selezionato deve essere minore di 4 MB
* L'intervallo di dimensioni del volto rilevabili va da 36 x 36 a 4096 x 4096 pixel. I volti al di fuori di questo intervallo non vengono rilevati
* È possibile che alcuni volti non vengano rilevati per problemi tecnici, ad esempio angoli del volto grandi (posa della testa) e grandi occlusioni. Le riprese frontali e quasi frontali producono i risultati migliori.

-----
