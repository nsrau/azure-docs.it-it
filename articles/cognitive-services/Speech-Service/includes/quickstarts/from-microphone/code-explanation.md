---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: fdcb5bb57afe145b57cb1223242d4d5625a2dfb7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400672"
---
Per creare un oggetto di configurazione vocale sono necessarie la chiave e l'area della sottoscrizione della risorsa Voce. L'oggetto di configurazione è necessario per creare un'istanza di un oggetto riconoscimento vocale.

L'istanza del riconoscimento espone diversi modi per il riconoscimento vocale. In questo esempio, la voce viene riconosciuta una volta. Questa funzionalità consente al servizio Voce di rilevare che si sta inviando una singola frase per il riconoscimento e di interrompere il riconoscimento vocale una volta identificata la frase. Una volta restituito il risultato, il codice scriverà il motivo del riconoscimento nella console.

> [!TIP]
> Per impostazione predefinita, Speech SDK riconoscerà l'uso di `en-us` per la lingua. Per informazioni sulla scelta della lingua di origine, vedere [Specificare la lingua di origine per il riconoscimento vocale](../../../how-to-specify-source-language.md).