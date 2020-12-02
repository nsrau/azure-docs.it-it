---
title: Avvio rapido per il servizio Azure SignalR serverless - Python
description: Argomento di avvio rapido per usare il Servizio Azure SignalR e Funzioni di Azure per la creazione di una chat room con Python.
author: anthonychu
ms.service: signalr
ms.devlang: python
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: antchu
ms.custom: devx-track-python
ms.openlocfilehash: aaaf9011d38e7ec02e83db63757c434329b835e0
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94960292"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-python"></a>Guida introduttiva: Creare una chat room con Funzioni di Azure e il servizio SignalR usando Python

Il servizio Azure SignalR consente di aggiungere facilmente funzionalità in tempo reale all'applicazione. Funzioni di Azure è una piattaforma serverless che consente di eseguire il codice senza gestire alcuna infrastruttura. Questa guida introduttiva fornisce informazioni su come usare il servizio SignalR e le funzioni per creare un'applicazione serverless di chat in tempo reale.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva può essere eseguita su macOS, Windows o Linux.

Assicurarsi di disporre di un editor di codice installato, ad esempio [Visual Studio Code](https://code.visualstudio.com/).

Installare [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) (versione 2.7.1505 o successiva) per eseguire le app per le funzioni di Azure Python in locale.

Funzioni di Azure richiede [Python 3.6 o 3.7](https://www.python.org/downloads/).

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qspython).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo <https://portal.azure.com/> con il proprio account Azure.

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qspython).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qspython).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qspython).

## <a name="configure-and-run-the-azure-function-app"></a>Configurare ed eseguire l'app per le funzioni di Azure

1. Nel browser in cui è aperto il portale di Azure, verificare che l'istanza del servizio SignalR distribuita in precedenza sia stata creata correttamente eseguendo una ricerca del nome nella casella di ricerca nella parte superiore del portale. Selezionare l'istanza per aprirla.

    ![Cercare l'istanza del servizio SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selezionare **Chiavi** per visualizzare le stringhe di connessione per l'istanza del servizio SignalR.

1. Selezionare e copiare la stringa di connessione primaria.

    ![Selezionare e copiare la stringa di connessione primaria.](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Nell'editor di codice aprire la cartella *src/chat/python* nel repository clonato.

1. Per sviluppare e testare funzioni Python in locale, è necessario lavorare in un ambiente Python 3.6 o 3.7. Eseguire i comandi seguenti per creare e attivare un ambiente virtuale denominato `.venv`.

    **Linux o macOS:**

    ```bash
    python3.7 -m venv .venv
    source .venv/bin/activate
    ```

    **Windows:**

    ```powershell
    py -3.7 -m venv .venv
    .venv\scripts\activate
    ```

1. Rinominare *local.settings.sample.json* in *local.settings.json*.

1. In **local.settings.json**, incollare la stringa di connessione nel valore dell'impostazione **AzureSignalRConnectionString**. Salvare il file.

1. Le funzioni Python sono organizzate in cartelle. In ogni cartella ci sono due file: *function.json* definisce le associazioni usate nella funzione e *\_\_init\_\_.py* è il corpo della funzione. In questa app per le funzioni sono presenti due funzioni attivate tramite HTTP:

    - **negotiate**: usa l'associazione di input *SignalRConnectionInfo* per generare e restituire informazioni di connessione valide.
    - **messages**: riceve un messaggio di chat nel corpo della richiesta e usa l'associazione di output *SignalR* per trasmettere il messaggio a tutte le applicazioni client connesse.

1. Nel terminale in cui è attivato l'ambiente virtuale, verificare di trovarsi nella cartella *src/chat/python*. Installare i pacchetti Python necessari con PIP.

    ```bash
    python -m pip install -r requirements.txt
    ```

1. Eseguire l'app per le funzioni.

    ```bash
    func start
    ```

    ![Eseguire l'app per le funzioni](media/signalr-quickstart-azure-functions-python/signalr-quickstart-run-application.png)
    
Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qspython).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qspython).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qspython).

## <a name="next-steps"></a>Passaggi successivi

In questo avvio reale è stata creata ed eseguita un'applicazione serverless in tempo reale in VS Code. Successivamente, si riceveranno altre informazioni su come distribuire le funzioni di Azure da VS Code.

> [!div class="nextstepaction"]
> [Distribuire Funzioni di Azure con VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)

