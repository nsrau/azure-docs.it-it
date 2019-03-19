---
title: Allineamento delle parole - API Traduzione testuale
titlesuffix: Azure Cognitive Services
description: Ricevere informazioni sull'allineamento delle parole dall'API Traduzione testuale.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.custom: seodec18
ms.openlocfilehash: bdb52e48b6a962c5b38e837f1e2bc0064539951e
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 02/23/2019
ms.locfileid: "56734592"
---
# <a name="how-to-receive-word-alignment-information"></a>Come ricevere informazioni sull'allineamento delle parole

## <a name="receiving-word-alignment-information"></a>Ricevere informazioni sull'allineamento delle parole
Per ricevere informazioni sull'allineamento, usare il metodo Translate e includere il parametro facoltativo includeAlignment.

## <a name="alignment-information-format"></a>Formato informazioni di allineamento
L'allineamento viene restituito come un valore di stringa nel formato seguente per ogni parola dell'origine. Le informazioni per ogni parola sono separate da uno spazio, incluse le lingue non separate da spazi (script), ad esempio il cinese:

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

Stringa di allineamento di esempio: "0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21".

In altre parole, i due punti separano l'indice iniziale e finale, il trattino separa le lingue e uno spazio separa le parole. Una parola può essere allineata con nessuna, una o più parole nell'altra lingua e le parole allineate potrebbero non essere contigue. Quando non è disponibile alcuna informazione di allineamento, l'elemento Allineamento sarà vuoto. In questo caso il metodo non restituisce alcun errore.

## <a name="restrictions"></a>Restrizioni
L'allineamento viene restituito solo per un subset delle coppie di lingua in questi casi:
* dall'inglese verso qualsiasi altra lingua;
* da qualsiasi altra lingua verso l'inglese, a eccezione del cinese semplificato, cinese tradizionale e lettone verso l'inglese
* dal giapponese al coreano o dal coreano al giapponese. Non si riceveranno le informazioni di allineamento se la frase è una traduzione predefinita. Esempio di una traduzione predefinita è "Questo è un test", "Ti amo" e altre frasi frequentemente usate.

## <a name="example"></a>Esempio

JSON di esempio

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
