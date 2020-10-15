---
ms.openlocfilehash: 7b9954930c668e338cc289e948a5591c09ec2654
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/09/2020
ms.locfileid: "76694321"
---
## <a name="provide-feedback-to-the-user"></a>Fornire feedback all'utente

È possibile scrivere codice per gestire l'evento di aggiornamento della sessione. Questo evento viene generato ogni volta che la sessione migliora il riconoscimento dell'ambiente circostante. In questo modo è possibile:

- Usare la classe `UserFeedback` per fornire feedback all'utente quando il dispositivo viene spostato e la sessione aggiorna il riconoscimento dell'ambiente. A tale scopo, seguire questa procedura:
- Determinare in quale punto sono disponibili dati spaziali rilevati sufficienti per creare ancoraggi nello spazio. Per questa operazione si usa `ReadyForCreateProgress` o `RecommendedForCreateProgress`. Quando il valore di `ReadyForCreateProgress` è maggiore di 1, i dati disponibili sono sufficienti per salvare un ancoraggio nello spazio cloud, anche se è consigliabile attendere fino a quando il valore di `RecommendedForCreateProgress` non diventa maggiore di 1.
