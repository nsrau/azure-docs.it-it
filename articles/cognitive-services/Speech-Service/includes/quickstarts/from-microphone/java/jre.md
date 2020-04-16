---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/02/2020
ms.author: trbye
ms.openlocfilehash: c2b844b3b1aa7a848e2668b3dca5a1cac5b5baaf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400678"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Creare una risorsa Voce di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md?tabs=jre&pivots=programming-language-java)
> * Assicurarsi di avere accesso a un microfono per l'acquisizione audio

## <a name="source-code"></a>Codice sorgente

Per aggiungere una nuova classe vuota al progetto Java, selezionare **File** > **Nuovo** > **Classe**. Nella finestra **Nuova classe Java** immettere **speechsdk.quickstart** nel campo **Pacchetto** e **Main** nel campo **Nome** .

![Screenshot della procedura guidata Nuova classe Java](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-06-create-main-java.png)

Sostituire il contenuto del file *Main.java* con il frammento seguente:

[!code-java[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/java/jre/from-microphone/src/speechsdk/quickstart/Main.java#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Spiegazione del codice

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Compilare ed eseguire l'app

Premere <kbd>F11</kbd> o selezionare **Esegui** > **Debug**.
I successivi 15 secondi di input vocale dal microfono verranno riconosciuti e registrati nella finestra della console.

![Screenshot della console dopo il riconoscimento corretto](~/articles/cognitive-services/Speech-Service/media/sdk/qs-java-jre-07-console-output.png)

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]

