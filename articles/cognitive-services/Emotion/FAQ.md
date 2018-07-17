---
title: Domande frequenti sull'API Emozioni | Microsoft Docs
description: Ottenere risposte alle domande frequenti sull'API Emozioni in Servizi cognitivi.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 01/26/2017
ms.author: anroth
ms.openlocfilehash: 8532d7c00fd8d7b01d84b5e55cb9bbc60241789c
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372996"
---
# <a name="emotion-api-frequently-asked-questions"></a>Domande frequenti sull'API Emozioni
 
> [!IMPORTANT]
> L'anteprima dell'API Video terminerà il 30 ottobre 2017. La nuova [anteprima dell'API Video Indexer](https://azure.microsoft.com/services/cognitive-services/video-indexer/) consente di estrarre facilmente informazioni dettagliate dai video e di ottimizzare le esperienze di individuazione del contenuto, ad esempio i risultati della ricerca, tramite il rilevamento di testo parlato, visi, caratteri ed emozioni. [Altre informazioni](https://docs.microsoft.com/azure/cognitive-services/video-indexer/video-indexer-overview).

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-emotion-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Se non è possibile trovare risposte alle proprie domande in queste domande frequenti, provare a porre la domanda alla community dell'API Emozioni su [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) oppure contattare il [supporto tecnico da UserVoice](https://cognitive.uservoice.com/).  

-----

**Domanda**: *Quali tipi di immagini ottengono i risultati migliori dall'API Emozioni?*

**Risposta**: Per ottenere risultati ottimali, usare immagini del viso in posizione frontale e non nascosto. L'affidabilità diminuisce con immagini del viso parzialmente frontale e l'API Emozioni potrebbe non riconoscere le emozioni nelle immagini in cui il viso è ruotato di 45 o più gradi.

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

**Domanda**: *: Sto passando i dati immagine binari ma ricevo un errore "Immagine del viso non valida".**

**Risposta**: Significa che l'algoritmo non è riuscito ad analizzare l'immagine.  
* I formati di immagine di input supportati includono JPEG, PNG, GIF (il primo frame), BMP. 
* Le dimensioni del file di immagine selezionato devono essere inferiori a 4 MB
* L'intervallo delle dimensioni rilevabili del volto va da 36x36 a 4096x4096 pixel. I volti al di fuori di questo intervallo non vengono rilevati.
* È possibile che alcuni volti non vengano rilevati per problemi tecnici, ad esempio angoli del volto molto grandi (durante le pose) e grandi occlusioni. Le riprese frontali e quasi frontali producono i risultati migliori.

-----
