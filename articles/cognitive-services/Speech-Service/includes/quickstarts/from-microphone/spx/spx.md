---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 05/13/2020
ms.author: v-demjoh
ms.openlocfilehash: a9beaea70dd3c8ce852344b11a50c5f20e5f8ca2
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83806224"
---
## <a name="enable-microphone"></a>Abilitare il microfono

Collegare e accendere il microfono del PC e disattivare eventuali app che potrebbero usare il microfono. Alcuni computer dispongono di un microfono incorporato, mentre altri richiedono la configurazione di un dispositivo Bluetooth.

## <a name="run-the-speech-cli"></a>Eseguire l'interfaccia della riga di comando per Voce

A questo punto si è pronti per eseguire l'interfaccia della riga di comando vocale per Voce al fine di utilizzare il riconoscimento vocale dal microfono.

1. **Avviare l'app**: dalla riga di comando passare alla directory che contiene il file binario dell'interfaccia a riga di comando per Voce e digitare:
    ```bash
    spx recognize --microphone
    ```

    > [!NOTE]
    > La lingua predefinita dell'interfaccia della riga di comando per Voce è l’inglese. È possibile scegliere una lingua diversa [dalla tabella Riconoscimento vocale](../../../../language-support.md).
    > Ad esempio, aggiungere `--source de-DE` per il riconoscimento vocale in tedesco.

2. **Avviare il riconoscimento**: parlare nel microfono. La trascrizione delle parole sarà visualizzata in testo in tempo reale. L'interfaccia della riga di comando per Voce si arresterà dopo un periodo di inattività o quando si preme CTRL + C.
