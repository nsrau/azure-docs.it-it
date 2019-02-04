---
title: Informazioni sull'API Emozioni
titlesuffix: Azure Cognitive Services
description: Usare l'algoritmo di riconoscimento delle emozioni basato sul cloud per creare altre app personalizzate.
services: cognitive-services
author: anrothMSFT
manager: cgronlun
ms.service: cognitive-services
ms.subservice: emotion-api
ms.topic: overview
ms.date: 02/06/2017
ms.author: anroth
ROBOTS: NOINDEX
ms.openlocfilehash: 555b03be679b0d1ea61371d22ec9865e0e72b558
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215793"
---
# <a name="what-is-the-emotion-api"></a>Informazioni sull'API Emozioni

> [!IMPORTANT]
> L'API Emozioni verrà deprecata il 15 febbraio 2019. La funzionalità di riconoscimento delle emozioni è ora generalmente disponibile nell'ambito dell'[API Viso](https://docs.microsoft.com/azure/cognitive-services/face/). 

Benvenuto nell'API Emozioni di Microsoft, che consente di creare app maggiormente personalizzate con l'algoritmo di riconoscimento delle emozioni all'avanguardia basato sul cloud di Microsoft.

### <a name="emotion-recognition"></a>Riconoscimento delle emozioni

L'API Emozioni versione beta prende un'immagine come input e restituisce l'attendibilità in un set di emozioni per ogni viso nell'immagine, oltre a un riquadro per il viso dall'API Viso. Le emozioni rilevate sono felicità, tristezza, sorpresa, rabbia, paura, biasimo, disgusto o neutralità. Queste emozioni vengono comunicate indipendentemente dalla cultura e universalmente tramite le stesse espressioni facciali di base, che vengono identificate dall'API Emozioni.

**Interpretazione dei risultati:**

Nei risultati dell'interpretazione dell'API Emozioni, l'emozione rilevata deve essere interpretata come l'emozione con il punteggio più elevato, in quanto i punteggi vengono normalizzati per sommare a uno. Gli utenti possono scegliere di impostare una soglia di attendibilità più alta all'interno della propria applicazione, a seconda delle esigenze.

Per altre informazioni, vedere le informazioni di riferimento dell'API:
  * Basic: se un utente ha già richiamato l'API Viso, può inviare il rettangolo per il viso come input facoltativo e usare il livello basic. [Informazioni di riferimento sulle API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard: se un utente non invia un rettangolo per il viso, deve usare la modalità standard.  [Informazioni di riferimento sulle API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Per un esempio su come interpretare i video in streaming con l'API Emozioni, vedere [come analizzare video in tempo reale](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
