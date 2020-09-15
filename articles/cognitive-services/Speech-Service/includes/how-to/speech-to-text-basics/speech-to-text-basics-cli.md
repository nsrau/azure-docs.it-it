---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 09/08/2020
ms.author: trbye
ms.openlocfilehash: 9c2c46040dd741253e9a68855c4dca89c1dc9a9a
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/09/2020
ms.locfileid: "89570580"
---
[!INCLUDE [SPX Setup](../../spx-setup.md)]

## <a name="speech-to-text-from-microphone"></a>Riconoscimento vocale da microfono

Collegare e accendere il microfono del PC e disattivare eventuali app che potrebbero usare il microfono. Alcuni computer dispongono di un microfono incorporato, mentre altri richiedono la configurazione di un dispositivo Bluetooth.

A questo punto si è pronti per eseguire l'interfaccia della riga di comando vocale per Voce al fine di utilizzare il riconoscimento vocale dal microfono. Dalla riga di comando passare alla directory che contiene il file binario dell'interfaccia della riga di comando per Voce ed eseguire il comando seguente.

```bash
spx recognize --microphone
```

> [!NOTE]
> La lingua predefinita dell'interfaccia della riga di comando per Voce è l’inglese. È possibile scegliere una lingua diversa [dalla tabella Riconoscimento vocale](../../../../language-support.md).
> Ad esempio, aggiungere `--source de-DE` per il riconoscimento vocale in tedesco.

Parlare nel microfono e sarà possibile vedere la trascrizione delle parole in testo in tempo reale. L'interfaccia della riga di comando per Voce si arresterà dopo un periodo di inattività o quando si preme CTRL + C.

## <a name="speech-to-text-from-audio-file"></a>Riconoscimento vocale da file audio

L'interfaccia della riga di comando per Voce può eseguire il riconoscimento vocale in molti formati di file e linguaggi naturali. In questo esempio è possibile usare un file WAV (16 kHz o 8 kHz, a 16 bit e PCM mono) che contiene la voce in lingua inglese. In alternativa, se si vuole un esempio rapido, scaricare il file <a href="https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/whatstheweatherlike.wav" download="whatstheweatherlike" target="_blank">whatstheweatherlike.wav <span class="docon docon-download x-hidden-focus"></span></a> e copiarlo nella stessa directory del file binario dell'interfaccia della riga di comando di Voce.

A questo punto si è pronti per eseguire l'interfaccia della riga di comando di Voce per riconoscere la voce trovata nel file audio eseguendo il comando seguente.

```bash
spx recognize --file whatstheweatherlike.wav
```

> [!NOTE]
> La lingua predefinita dell'interfaccia della riga di comando per Voce è l’inglese. È possibile scegliere una lingua diversa [dalla tabella Riconoscimento vocale](../../../../language-support.md).
> Ad esempio, aggiungere `--source de-DE` per il riconoscimento vocale in tedesco.

L'interfaccia della riga di comando per Voce visualizzerà una trascrizione testuale della voce sullo schermo.