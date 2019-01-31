---
title: 'Guida introduttiva: Riconoscimento vocale, Python: servizi Voce'
titleSuffix: Azure Cognitive Services
description: Usare questa guida per creare un'applicazione console di riconoscimento vocale con Speech SDK per Python. Al termine, sarà possibile usare il microfono nel computer per trascrivere contenuti vocali in tempo reale.
services: cognitive-services
author: chlandsi
manager: cgronlun
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 1/16/2019
ms.author: chlandsi
ms.openlocfilehash: 1c7287b919c46ead4f961aff769da5c8bf68cc6f
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 01/29/2019
ms.locfileid: "55226571"
---
# <a name="quickstart-recognize-speech-with-the-speech-sdk-for-python"></a>Avvio rapido: Riconoscimento vocale con Speech SDK per Python

[!INCLUDE [Selector](../../../includes/cognitive-services-speech-service-quickstart-selector.md)]

Questo articolo illustra come usare il servizio Voce tramite Speech SDK per Python e spiega come riconoscere contenuti vocali dall'input del microfono.

## <a name="prerequisites"></a>Prerequisiti

Prima di iniziare, ecco un elenco di prerequisiti:

* Una chiave di sottoscrizione di Azure per il servizio Voce. [È possibile ottenerne una gratuitamente](get-started.md).
* [Python 3.5 (a 64 bit)](https://www.python.org/downloads/) o versione successiva.
* Il pacchetto Speech SDK per Python è disponibile per Windows (x64), Mac (macOS X 10.12 o versione successiva) e Linux (Ubuntu 16.04 o 18.04 su x64).
* In Ubuntu eseguire i comandi seguenti per l'installazione dei pacchetti necessari:

  ```sh
  sudo apt-get update
  sudo apt-get install build-essential libssl1.0.0 libcurl3 libasound2 wget
  ```

* In Windows è anche necessario la versione di [Microsoft Visual C++ Redistributable per Visual Studio 2017](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads) per la piattaforma in uso.

## <a name="install-the-speech-sdk"></a>Installare Speech SDK

[!INCLUDE [License Notice](../../../includes/cognitive-services-speech-service-license-notice.md)]

È possibile installare il pacchetto Speech SDK di Servizi cognitivi per Python da [PyPI](https://pypi.org/) eseguendo il comando seguente sulla riga di comando:

```sh
pip install azure-cognitiveservices-speech
```

La versione corrente di Speech SDK di Servizi cognitivi è `1.2.0`.

## <a name="support-and-updates"></a>Supporto e aggiornamenti

Gli aggiornamenti del pacchetto Speech SDK per Python verranno distribuiti tramite PyPI e annunciati nella pagina [Note sulla versione](./releasenotes.md).
Se è disponibile una nuova versione, è possibile eseguire l'aggiornamento ad essa con il comando `pip install --upgrade azure-cognitiveservices-speech`.
È possibile controllare la versione attualmente installata esaminando la variabile `azure.cognitiveservices.speech.__version__`.

Se si verifica un problema o manca una funzionalità, controllare la [pagina del supporto](./support.md).

## <a name="create-a-python-application-using-the-speech-sdk"></a>Creare un'applicazione Python con Speech SDK

### <a name="run-the-sample"></a>Eseguire l'esempio

È possibile copiare il [codice](#quickstart-code) da questa guida introduttiva a un file di origine `quickstart.py` ed eseguirlo nell'ambiente di sviluppo integrato o nella console:

```sh
python quickstart.py
```

In alternativa è possibile scaricare questa esercitazione della guida introduttiva come notebook [Jupyter](https://jupyter.org) dal [repository degli esempi di Voce di Servizi cognitivi](https://github.com/Azure-Samples/cognitive-services-speech-sdk/) ed eseguirla come notebook.

### <a name="sample-code"></a>Codice di esempio

[!code-python[Quickstart Code](~/samples-cognitive-services-speech-sdk/quickstart/python/quickstart.py#code)]

### <a name="install-and-use-the-speech-sdk-with-visual-studio-code"></a>Installare e usare Speech SDK con Visual Studio Code

1. [Scaricare](https://www.python.org/downloads/) e installare una versione a 64 bit (3.5 o versione successiva) di Python nel computer in uso.
1. [Scaricare](https://code.visualstudio.com/Download) e installare Visual Studio Code.
1. Aprire Visual Studio Code e installare l'estensione Python selezionando **File** > **Preferenze** > **Estensioni** dal menu e quindi cercare "Python".
   ![Installare l'estensione Python](media/sdk/qs-python-vscode-python-extension.png)
1. Creare una cartella in cui archiviare il progetto, ad esempio con Esplora risorse.
1. In Visual Studio Code fare clic sull'icona **File** e quindi aprire la cartella creata.
   ![Aprire la cartella](media/sdk/qs-python-vscode-python-open-folder.png)
1. Creare un nuovo file di origine Python `speechsdk.py`, facendo clic sull'icona per Nuovo file.
   ![Creare il file](media/sdk/qs-python-vscode-python-newfile.png)
1. Copiare, incollare e salvare il [codice Python](#quickstart-code) nel file appena creato.
1. Inserire le informazioni sulla sottoscrizione del servizio Voce.
1. Se è già stato selezionato un interprete Python, questo verrà visualizzato sul lato sinistro della barra di stato nella parte inferiore della finestra.
   In caso contrario, per visualizzare un elenco di interpreti Python disponibili, aprire il **riquadro comandi** (`Ctrl+Shift+P`) e digitare **Python: Select Interpreter** (Python: Seleziona interprete) e quindi sceglierne uno appropriato.
1. Se il pacchetto Speech SDK per Python non è stato ancora installato per l'interprete Python selezionato, è possibile eseguire facilmente questa operazione all'interno di Visual Studio Code.
   Per installare il pacchetto Speech SDK, aprire un terminale attivando nuovamente il riquadro comandi (`Ctrl+Shift+P`) e digitando **Terminal: Create New Integrated Terminal** (Crea nuovo terminale integrato).
   Nel terminale visualizzato immettere il comando `python -m pip install azure-cognitiveservices-speech` oppure il comando appropriato per il sistema.
1. Per eseguire il codice di esempio, fare doppio clic su un punto qualsiasi all'interno dell'editor e selezionare **Run Python File in Terminal** (Esegui file Python nel terminale).
   Pronunciare alcune parole quando richiesto. Il testo trascritto dovrebbe essere visualizzato dopo qualche istante.
   ![Eseguire l'esempio](media/sdk/qs-python-vscode-python-run.png)

In caso di problemi durante l'uso di queste istruzioni, fare riferimento alla più completa [esercitazione su Python in Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial).

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Esaminare gli esempi di codice Python su GitHub](https://aka.ms/csspeech/samples)
