---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: 3613d190ef079d0e477d42b426a224d8e4dda7e6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400787"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Creare una risorsa Voce di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md?pivots=programming-language-python)
> * Assicurarsi di avere accesso a un microfono per l'acquisizione audio

## <a name="source-code"></a>Codice sorgente

Creare un file denominato *quickstart.py* e incollare il codice Python seguente al suo interno.

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/from-microphone/quickstart.py#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Spiegazione del codice

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Compilare ed eseguire l'app

A questo punto è possibile testare l'app e verificare la funzionalità di riconoscimento vocale con il servizio Voce.

1. **Avviare l'app**: dalla riga di comando digitare:
    ```bash
    python quickstart.py
    ```
2. **Avviare il riconoscimento**: verrà richiesto di pronunciare una frase in inglese. La voce viene inviata al servizio Voce, trascritta come testo e visualizzata nella console.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

