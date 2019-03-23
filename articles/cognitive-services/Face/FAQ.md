---
title: Domande frequenti - API Viso
titlesuffix: Azure Cognitive Services
description: Di seguito sono riportate le risposte alle domande più frequenti sul servizio API Viso.
services: cognitive-services
author: SteveMSFT
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 01/26/2017
ms.author: sbowles
ms.openlocfilehash: 47ce80e1b0cefc01752d2445b751ebe1c2d65d08
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351330"
---
# <a name="face-api-frequently-asked-questions"></a>Domande frequenti sull'API Viso

> [!TIP]
> Se non è possibile trovare risposte alle proprie domande in queste domande frequenti, provare a porre la domanda alla community dell'API Viso su [StackOverflow](https://stackoverflow.com/questions/tagged/project-oxford+or+microsoft-cognitive) oppure contattare il [supporto tecnico da UserVoice](https://cognitive.uservoice.com/).

-----
**Domanda:** Quali fattori può ridurre la precisione dell'API viso per il riconoscimento, la verifica o trovare simile?

**Risposta:** È in genere gli stessi case in cui gli esseri umani hanno difficoltà a identificare un utente tra cui:
* Ostacoli che limitano la vista da uno o da entrambi gli occhi
* Illuminazione harsh (ad esempio, retroilluminazione)
* Cambiamenti alla pettinatura o ai peli del volto
* Cambiamenti dovuti all'età
* Espressioni del viso Extreme (ad esempio, inserire il testo)

API viso ha spesso esito positivo in complessi casi come quello precedente, ma è possibile ridurre la precisione. Per migliorare il riconoscimento e risolvere queste problematiche, usare foto che includono una varietà di angolazioni e illuminazioni.

-----
**Domanda:**  cosa significa quando si riceve l'errore "Immagine del viso non valida" durante il passaggio di dati immagine binari?

**Risposta:**  Questo errore indica che l'algoritmo verificato un problema di analisi dell'immagine. Le cause includono:
* I formati di immagine di input supportati includono JPEG, PNG, GIF (il primo frame), BMP.
* La dimensione del file di immagine selezionato deve essere minore di 4 MB
* L'intervallo delle dimensioni rilevabili del volto va da 36x36 a 4096x4096 pixel. I volti al di fuori di questo intervallo non vengono rilevati
* Alcuni volti non possono essere rilevati a causa di problemi tecnici, ad esempio angoli del volto grandi (head-posa) e grandi occlusioni. Le riprese frontali e quasi frontali producono i risultati migliori.

-----
