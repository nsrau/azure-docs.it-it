---
title: 'Guida introduttiva: Sintesi vocale, Python - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sulla sintesi vocale in Python con Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: 36e48a5d513daa951c1e92017ba9a2322b009703
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73505452"
---
## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, assicurarsi di:

> [!div class="checklist"]
> * [Creare una risorsa Voce di Azure](../../../../get-started.md)
> * [Configurare l'ambiente di sviluppo](../../../../quickstarts/setup-platform.md)
> * [Creare un progetto di esempio vuoto](../../../../quickstarts/create-project.md)
```

## Support and updates

Updates to the Speech SDK Python package are distributed via PyPI and announced in the [Release notes](~/articles/cognitive-services/Speech-Service/releasenotes.md).
If a new version is available, you can update to it with the command `pip install --upgrade azure-cognitiveservices-speech`.
Check which version is currently installed by inspecting the `azure.cognitiveservices.speech.__version__` variable.

If you have a problem, or you're missing a feature, see [Support and help options](~/articles/cognitive-services/Speech-Service/support.md).

## Create a Python application that uses the Speech SDK

### Run the sample

You can copy the [sample code](#sample-code) from this quickstart to a source file `quickstart.py` and run it in your IDE or in the console:

```sh
python quickstart.py
```

In alternativa è possibile scaricare questa esercitazione della guida introduttiva come notebook [Jupyter](https://jupyter.org) dal [repository degli esempi di Speech SDK](https://aka.ms/csspeech/samples) ed eseguirla come notebook.

### <a name="sample-code"></a>Codice di esempio

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/text-to-speech/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installare e usare Speech SDK con Visual Studio Code

1. Scaricare e installare una versione a 64 bit di [Python](https://www.python.org/downloads/), 3.5 o versione successiva, nel proprio computer.
1. Scaricare e installare [Visual Studio Code](https://code.visualstudio.com/Download).
1. Aprire Visual Studio Code e installare l'estensione Python. Selezionare **File** > **Preferenze** > **Estensioni** dal menu. Cercare **Python**.

   ![Installare l'estensione Python](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-extension.png)

1. Creare una cartella in cui archiviare il progetto. Un esempio consiste nell'usare Esplora risorse.
1. In Visual Studio Code selezionare l'icona **File**. Quindi, aprire la cartella creata.

   ![Aprire una cartella](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-open-folder.png)

1. Creare un nuovo file di origine Python `speechsdk.py`, selezionando l'icona Nuovo file.

   ![Creare un file](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-newfile.png)

1. Copiare, incollare e salvare il [codice Python](#sample-code) nel file appena creato.
1. Inserire le informazioni sulla sottoscrizione dei servizi Voce.
1. Se selezionato, un interprete Python verrà visualizzato sul lato sinistro della barra di stato nella parte inferiore della finestra.
   In caso contrario, visualizzare un elenco di interpreti Python disponibili. Usare il riquadro comandi (CTRL+MAIUSC+P) e immettere **Python: Select Interpreter** (Python: Seleziona interprete). Sceglierne uno appropriato.
1. È possibile installare il pacchetto Speech SDK per Python da Visual Studio Code. È possibile farlo se non è stato ancora installato per l'interprete Python selezionato.
   Per installare il pacchetto Speech SDK, aprire un terminale. Visualizzare di nuovo il riquadro comandi (CTRL+MAIUSC+P) e immettere **Terminal: Create New Integrated Terminal** (Crea nuovo terminale integrato).
   Nel terminale visualizzato immettere il comando `python -m pip install azure-cognitiveservices-speech` oppure il comando appropriato per il sistema.
1. Per eseguire il codice di esempio, fare doppio clic su un punto qualsiasi all'interno dell'editor. Selezionare **Run Python File in Terminal** (Esegui file Python nel terminale).
   Digitare il testo quando richiesto. L'audio sintetizzato viene riprodotto poco dopo.

   ![Eseguire un esempio](~/articles/cognitive-services/Speech-Service/media/sdk/qs-python-vscode-python-run-tts.png)

In caso di problemi durante l'uso di queste istruzioni, consultare la più completa [esercitazione su Python in Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Passaggi successivi

[!INCLUDE [footer](./footer.md)]

## <a name="see-also"></a>Vedere anche

- [Creare una voce personalizzata](~/articles/cognitive-services/Speech-Service/how-to-custom-voice-create-voice.md)
- [Registrare campioni vocali personalizzati](~/articles/cognitive-services/Speech-Service/record-custom-voice-samples.md)
