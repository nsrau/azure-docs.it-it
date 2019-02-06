---
title: Domande frequenti - API Viso
titlesuffix: Azure Cognitive Services
description: Di seguito sono riportate le risposte alle domande più frequenti sul servizio API Viso.
services: cognitive-services
author: SteveMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: b4b2c09ef608da7c52d415d5f1f2215ddc31c41a
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223324"
---
# <a name="face-api-frequently-asked-questions"></a>Domande frequenti sull'API Viso

### <a name="if-you-cant-find-answers-to-your-questions-in-this-faq-try-asking-the-face-api-community-on-stackoverflowhttpsstackoverflowcomquestionstaggedproject-oxfordormicrosoft-cognitive-or-contact-help-and-support-on-uservoicehttpscognitiveuservoicecom"></a>Se non è possibile trovare risposte alle proprie domande in queste domande frequenti, provare a porre la domanda alla community dell'API Viso su [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) oppure contattare il [supporto tecnico da UserVoice](https://cognitive.uservoice.com/).

-----
**Domanda:** quali fattori possono ridurre la precisione dell'API Viso per le funzioni Recognition (Riconoscimento), Verification (Verifica) o Find Similar (Ricerca di simili)?

**Risposta:** in genere si tratta degli stessi casi in cui gli utenti hanno difficoltà a identificare qualcuno, tra cui:
* Ostacoli che limitano la vista da uno o da entrambi gli occhi
* Pessima illuminazione, ad esempio forte retroilluminazione
* Cambiamenti alla pettinatura o ai peli del volto
* Cambiamenti dovuti all'età
* Espressioni facciali estreme (ad esempio, urla)

In genere, l'API Viso riesce a gestire casi come questi anche se la precisione è ridotta. Per migliorare il riconoscimento e risolvere queste problematiche, usare foto che includono una varietà di angolazioni e illuminazioni.

-----
**Domanda:**  cosa significa quando si riceve l'errore "Immagine del viso non valida" durante il passaggio di dati immagine binari?

**Risposta:**  significa che l'algoritmo non è riuscito ad analizzare l'immagine. Le cause includono:
* I formati di immagine di input supportati includono JPEG, PNG, GIF (il primo frame), BMP.
* Le dimensioni del file di immagine selezionato devono essere inferiori a 4 MB
* L'intervallo delle dimensioni rilevabili del volto va da 36x36 a 4096x4096 pixel. I volti al di fuori di questo intervallo non vengono rilevati.
* È possibile che alcuni volti non vengano rilevati per problemi tecnici, ad esempio angoli del volto molto grandi (durante le pose) e grandi occlusioni. Le riprese frontali e quasi frontali producono i risultati migliori.

-----
