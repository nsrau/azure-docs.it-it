---
title: Panoramica dell'API Emozioni | Microsoft Docs
description: Usare l'algoritmo di riconoscimento delle emozioni all'avanguardia basato sul cloud di Microsoft per creare app maggiormente personalizzate con l'API Emozioni in Servizi cognitivi.
services: cognitive-services
author: anrothMSFT
manager: corncar
ms.service: cognitive-services
ms.component: emotion-api
ms.topic: article
ms.date: 02/06/2017
ms.author: anroth
ms.openlocfilehash: 8383370cba3f78060e809f444f4ad3dab7380f4e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/23/2018
ms.locfileid: "35374313"
---
# <a name="what-is-emotion-api"></a>Informazioni sull'API Emozioni

> [!IMPORTANT]
> API Emozioni è stata deprecata il 30 ottobre 2017. La funzionalità fa ora parte dell'[API Viso](https://docs.microsoft.com/en-us/azure/cognitive-services/face/).

Benvenuto nell'API Emozioni di Microsoft, che consente di creare app maggiormente personalizzate con l'algoritmo di riconoscimento delle emozioni all'avanguardia basato sul cloud di Microsoft.

### <a name="emotion-recognition"></a>Riconoscimento delle emozioni

L'API Emozioni versione beta prende un'immagine come input e restituisce l'attendibilità in un set di emozioni per ogni viso nell'immagine, oltre a un riquadro per il viso dall'API Viso. Le emozioni rilevate sono felicità, tristezza, sorpresa, rabbia, paura, biasimo, disgusto o neutralità. Queste emozioni vengono comunicate indipendentemente dalla cultura e universalmente tramite le stesse espressioni facciali di base, che vengono identificate dall'API Emozioni. 

**Interpretazione dei risultati:** 

Nei risultati dell'interpretazione dell'API Emozioni, l'emozione rilevata deve essere interpretata come l'emozione con il punteggio più elevato, in quanto i punteggi vengono normalizzati per sommare a uno. Gli utenti possono scegliere di impostare una soglia di attendibilità più alta all'interno della propria applicazione, a seconda delle esigenze. 

Per altre informazioni, vedere le informazioni di riferimento dell'API: 
  * Basic: se un utente ha già richiamato l'API Viso, può inviare il rettangolo per il viso come input facoltativo e usare il livello basic. [Informazioni di riferimento sulle API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/56f23eb019845524ec61c4d7)
  * Standard: Se un utente non invia un rettangolo per il viso, deve usare la modalità standard.  [Informazioni di riferimento sulle API](https://westus.dev.cognitive.microsoft.com/docs/services/5639d931ca73072154c1ce89/operations/563b31ea778daf121cc3a5fa)

Per un esempio su come interpretare i video in streaming con l'API Emozioni, vedere [come analizzare video in tempo reale](https://docs.microsoft.com/azure/cognitive-services/emotion/emotion-api-how-to-topics/howtoanalyzevideo_emotion).
