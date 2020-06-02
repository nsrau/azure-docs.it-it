---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: 398dfe36314f86614515168986023146d695e69b
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806550"
---
## <a name="find-a-file-that-contains-speech"></a>Trovare un file che contiene voce

L'interfaccia della riga di comando per Voce può eseguire il riconoscimento vocale in molti formati di file e linguaggi naturali. Per questo avvio rapido è possibile usare un file WAV (16 kHz o 8 kHz, a 16 bit e mono PCM) che contiene la voce in lingua inglese.

1. Scaricare il <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a>.
2. Copiare il file di `whatstheweatherlike.wav` nella stessa directory del file binario dell'interfaccia della riga di comando per Voce.

## <a name="run-the-speech-cli"></a>Eseguire l'interfaccia della riga di comando per Voce

A questo punto si è pronti per eseguire l'interfaccia della riga di comando vocale per Voce per riconoscere la voce trovata nel file audio.

Dalla riga di comando passare alla directory che contiene il file binario dell'interfaccia a riga di comando per Voce e digitare:

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> La lingua predefinita dell'interfaccia della riga di comando per Voce è l’inglese. È possibile scegliere una lingua diversa [dalla tabella Riconoscimento vocale](../../../../language-support.md).
> Ad esempio, aggiungere `--source de-DE` per il riconoscimento vocale in tedesco.

L'interfaccia della riga di comando per Voce visualizzerà una trascrizione testuale della voce sullo schermo. Quindi l'interfaccia della riga di comando per Voce si chiuderà.
