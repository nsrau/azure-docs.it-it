---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: f26f0ab6da398dcdee307f89b27cca780d08af85
ms.sourcegitcommit: 374d1533ea2f2d9d3f8b6e6a8e65c6a5cd4aea47
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/01/2020
ms.locfileid: "85838814"
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

A questo punto è possibile testare il riconoscimento vocale con il servizio Voce. 

Se si esegue il test in macOS e questa è la prima app Python creata che usa un microfono, probabilmente sarà necessario concedere l'accesso al terminale al microfono. Aprire **Impostazioni di sistema** e selezionare **Sicurezza e privacy**. Selezionare quindi **Privacy** e individuare **Microfono** nell'elenco. Infine, selezionare **Terminale** e salvare. 

1. **Avviare l'app**: dalla riga di comando digitare:
    ```bash
    python quickstart.py
    ```
2. **Avviare il riconoscimento**: verrà richiesto di pronunciare una frase in inglese. La voce viene inviata al servizio Voce, trascritta come testo e visualizzata nella console.

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]
