---
title: Domande frequenti sul servizio API Viso | Microsoft Docs
description: Di seguito sono riportate le risposte alle domande più frequenti sul servizio API Viso.
services: cognitive-services
author: SteveMSFT
manager: corncar
ms.service: cognitive-services
ms.component: face-api
ms.topic: article
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: da2f75deef8a8beea3ba23b6a39eb6d2fe104b54
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35372972"
---
# <a name="face-api-frequently-asked-questions"></a>Domande frequenti sull'API Viso
### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Se non è possibile trovare risposte alle proprie domande in queste domande frequenti, provare a porre la domanda alla community dell'API Viso su [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) oppure contattare il [supporto tecnico da UserVoice](https://cognitive.uservoice.com/).

-----
**Domanda**: Quali fattori possono ridurre la precisione dell'API Viso per le funzioni Recognition (Riconoscimento), Verification (Verifica) o Find Similar (Ricerca di simili)?

**Risposta**: In genere si tratta degli stessi casi in cui gli utenti hanno difficoltà a identificare qualcuno, tra cui:
* Ostacoli che limitano la vista da uno o da entrambi gli occhi
* Pessima illuminazione, ad esempio forte retroilluminazione
* Cambiamenti alla pettinatura o ai peli del volto
* Cambiamenti dovuti all'età
* Espressioni facciali estreme (ad esempio, urla)

In genere, l'API Viso riesce a gestire casi come questi anche se la precisione è ridotta. Per migliorare il riconoscimento e risolvere queste problematiche, usare foto che includono una varietà di angolazioni e illuminazioni.

-----
**Domanda**: Sto passando i dati immagine binari ma ricevo un errore "Immagine del viso non valida".

**Risposta**: Significa che l'algoritmo non è riuscito ad analizzare l'immagine. Le cause includono:
* I formati di immagine di input supportati includono JPEG, PNG, GIF (il primo frame), BMP.
* Le dimensioni del file di immagine selezionato devono essere inferiori a 4 MB
* L'intervallo delle dimensioni rilevabili del volto va da 36x36 a 4096x4096 pixel. I volti al di fuori di questo intervallo non vengono rilevati.
* È possibile che alcuni volti non vengano rilevati per problemi tecnici, ad esempio angoli del volto molto grandi (durante le pose) e grandi occlusioni. Le riprese frontali e quasi frontali producono i risultati migliori.

-----
