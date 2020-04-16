---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/03/2020
ms.author: trbye
ms.openlocfilehash: db87f31f4702d3487a11a6d833755105455d74c8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81400712"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare:

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Creare una risorsa Voce di Azure<span class="docon docon-navigate-external x-hidden-focus"></span></a>
> * [Configurare l'ambiente di sviluppo e creare un progetto vuoto](../../../../quickstarts/setup-platform.md?tabs=windows&pivots=programming-language-cpp)
> * Assicurarsi di avere accesso a un microfono per l'acquisizione audio

## <a name="source-code"></a>Codice sorgente

Creare un file di origine in C++ denominato *helloworld.cpp* e incollare il codice seguente al suo interno.

[!code-cpp[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/cpp/windows/from-microphone/helloworld/helloworld.cpp#code)]

[!INCLUDE [replace key and region](../replace-key-and-region.md)]

## <a name="code-explanation"></a>Spiegazione del codice

[!INCLUDE [code explanation](../code-explanation.md)]

## <a name="build-and-run-app"></a>Compilare ed eseguire l'app

1. Dalla barra dei menu scegliere **Compila** > **Compila soluzione** per compilare l'applicazione. Il codice dovrebbe ora risultare compilato senza errori.

1. Scegliere **Debug** > **Avvia debug** o premere <kbd>F5</kbd> per avviare l'applicazione **helloworld**.

1. Pronunciare una frase o un'espressione in inglese. L'applicazione trasmette il parlato al servizio Voce, che effettua la trascrizione in testo e lo invia nuovamente all'applicazione per la visualizzazione.

   ![Output della console dopo il riconoscimento corretto](~/articles/cognitive-services/Speech-Service/media/sdk/qs-cpp-windows-08-console-output-release.png)

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [Speech recognition basics](../../speech-to-text-next-steps.md)]