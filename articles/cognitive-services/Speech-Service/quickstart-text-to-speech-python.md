---
title: 'Guida introduttiva: Sintesi vocale, Python - Servizio Voce'
titleSuffix: Azure Cognitive Services
description: Informazioni sulla sintesi vocale in Python con Speech SDK
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 09/14/2019
ms.author: yulili
ms.openlocfilehash: fed0dea1a562f5ddea05516125a38c690069f446
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803674"
---
# <a name="quickstart-synthesize-speech-with-the-speech-sdk-for-python"></a>Guida introduttiva: Sintesi vocale con Speech SDK per Python

Sono disponibili guide di avvio rapido anche per il [riconoscimento vocale](quickstart-python.md).

Questo articolo illustra come usare i servizi Voce tramite Speech SDK per Python. Viene illustrato come eseguire la sintesi vocale e riprodurre la voce con l'output audio predefinito.

## <a name="prerequisites"></a>Prerequisiti

* Una chiave di sottoscrizione di Azure per i servizi Voce. [È possibile ottenerne una gratuitamente](get-started.md).
* [Python 3.5 o versione successiva](https://www.python.org/downloads/).
* Il pacchetto Speech SDK per Python è disponibile per i sistemi operativi seguenti:
    * Windows 10: x64 e x86.
    * Mac: macOS X versione 10.12 o successiva.
    * Linux: Ubuntu 16.04, Ubuntu 18.04, Debian 9 su architetture x64.
* In Linux eseguire i comandi seguenti per installare i pacchetti richiesti:

  * In Ubuntu:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.0 libasound2
    ```

  * In Debian 9:

    ```sh
    sudo apt-get update
    sudo apt-get install build-essential libssl1.0.2 libasound2
    ```

* In Windows è necessaria la versione di [Microsoft Visual C++ Redistributable per Visual Studio 2019](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) per la piattaforma in uso.

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

Questa esercitazione non funzionerà con versioni dell'SDK precedenti alla 1.7.0.

Questo comando installa il pacchetto di Python da [PyPI](https://pypi.org/) per Speech SDK:

```sh
pip install azure-cognitiveservices-speech
```

## <a name="support-and-updates"></a>Supporto e aggiornamenti

Gli aggiornamenti del pacchetto Speech SDK per Python sono distribuiti tramite PyPI e annunciati nelle [Note sulla versione](./releasenotes.md).
Se è disponibile una nuova versione, è possibile eseguire l'aggiornamento ad essa con il comando `pip install --upgrade azure-cognitiveservices-speech`.
Controllare la versione attualmente installata esaminando la variabile `azure.cognitiveservices.speech.__version__`.

Se si verifica un problema o manca una funzionalità, vedere le [opzioni di supporto e assistenza](./support.md).

## <a name="create-a-python-application-that-uses-the-speech-sdk"></a>Creare un'applicazione Python che usi Speech SDK

### <a name="run-the-sample"></a>Eseguire l'esempio

È possibile copiare il [codice di esempio](#sample-code) da questa guida introduttiva in un file di origine `quickstart.py` ed eseguirlo nell'ambiente di sviluppo integrato o nella console:

```sh
python quickstart.py
```

In alternativa è possibile scaricare questa esercitazione della guida introduttiva come notebook [Jupyter](https://jupyter.org) dal [repository degli esempi di Speech SDK](https://aka.ms/csspeech/samples) ed eseguirla come notebook.

### <a name="sample-code"></a>Codice di esempio

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/text-to-speech/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installare e usare Speech SDK con Visual Studio Code

1. Scaricare e installare una versione a 64 bit di [Python](https://www.python.org/downloads/), 3.5 o versione successiva, nel proprio computer.
1. Scaricare e installare [Visual Studio Code](https://code.visualstudio.com/Download).
1. Aprire Visual Studio Code e installare l'estensione Python. Selezionare **File** > **Preferenze** > **Estensioni** dal menu. Cercare **Python**.

   ![Installare l'estensione Python](media/sdk/qs-python-vscode-python-extension.png)

1. Creare una cartella in cui archiviare il progetto. Un esempio consiste nell'usare Esplora risorse.
1. In Visual Studio Code selezionare l'icona **File**. Quindi, aprire la cartella creata.

   ![Aprire una cartella](media/sdk/qs-python-vscode-python-open-folder.png)

1. Creare un nuovo file di origine Python `speechsdk.py`, selezionando l'icona Nuovo file.

   ![Creare un file](media/sdk/qs-python-vscode-python-newfile.png)

1. Copiare, incollare e salvare il [codice Python](#sample-code) nel file appena creato.
1. Inserire le informazioni sulla sottoscrizione dei servizi Voce.
1. Se selezionato, un interprete Python verrà visualizzato sul lato sinistro della barra di stato nella parte inferiore della finestra.
   In caso contrario, visualizzare un elenco di interpreti Python disponibili. Usare il riquadro comandi (CTRL+MAIUSC+P) e immettere **Python: Select Interpreter** (Python: Seleziona interprete). Sceglierne uno appropriato.
1. È possibile installare il pacchetto Speech SDK per Python da Visual Studio Code. È possibile farlo se non è stato ancora installato per l'interprete Python selezionato.
   Per installare il pacchetto Speech SDK, aprire un terminale. Visualizzare di nuovo il riquadro comandi (CTRL+MAIUSC+P) e immettere **Terminal: Create New Integrated Terminal** (Crea nuovo terminale integrato).
   Nel terminale visualizzato immettere il comando `python -m pip install azure-cognitiveservices-speech` oppure il comando appropriato per il sistema.
1. Per eseguire il codice di esempio, fare doppio clic su un punto qualsiasi all'interno dell'editor. Selezionare **Run Python File in Terminal** (Esegui file Python nel terminale).
   Digitare il testo quando richiesto. L'audio sintetizzato viene riprodotto poco dopo.

   ![Eseguire un esempio](media/sdk/qs-python-vscode-python-run-tts.png)

In caso di problemi durante l'uso di queste istruzioni, consultare la più completa [esercitazione su Python in Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Python su GitHub](https://aka.ms/csspeech/samples)

## <a name="see-also"></a>Vedere anche

- [Personalizzare i carattere voce](how-to-customize-voice-font.md)
- [Registrare esempi vocali](record-custom-voice-samples.md)
