---
author: v-demjoh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/18/2020
ms.author: v-demjoh
ms.openlocfilehash: 4d548c1b7614503919de2725acb02d7f808ceb93
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806376"
---
## <a name="run-the-speech-cli"></a>Eseguire l'interfaccia della riga di comando per Voce

A questo punto si è pronti per eseguire l'interfaccia della riga di comando per Voce per tradurre la voce in testo in una lingua diversa.

Dalla riga di comando passare alla directory che contiene il file binario dell'interfaccia a riga di comando per Voce e digitare:

```bash
spx translate --microphone --target de-DE
```

L'interfaccia della riga di comando per Voce tradurrà l’inglese parlato in linguaggio naturale in testo stampato in tedesco.
Per arrestare lo strumento, premere INVIO.

> [!NOTE]
> La lingua predefinita dell'interfaccia della riga di comando per Voce è l’inglese. È possibile scegliere una lingua diversa [dalla tabella Riconoscimento vocale](../../../../language-support.md).
> Ad esempio, aggiungere `--source ja-JP` per il riconoscimento vocale in giapponese.
