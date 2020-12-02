---
title: Usare Java per creare una chat room con Funzioni di Azure e il servizio SignalR
description: Argomento di avvio rapido per usare il Servizio Azure SignalR e Funzioni di Azure per la creazione di una chat room con Java.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.custom: devx-track-java
ms.openlocfilehash: aa300122ab6c448862d46579d6e9ce1e5fc06a79
ms.sourcegitcommit: 642988f1ac17cfd7a72ad38ce38ed7a5c2926b6c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2020
ms.locfileid: "94874407"
---
# <a name="quickstart-use-java-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Guida introduttiva: Usare Java per creare una chat room con Funzioni di Azure e il servizio SignalR

Il servizio Azure SignalR consente di aggiungere facilmente funzionalità in tempo reale all'applicazione e Funzioni di Azure è una piattaforma serverless che consente di eseguire il codice senza gestire alcuna infrastruttura. Questo argomento di avvio rapido illustra come usare Java per creare un'applicazione serverless di chat in tempo reale con il Servizio SignalR e Funzioni di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un editor di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/)
- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing). Usato per eseguire le app Funzioni di Azure in locale.

   > [!NOTE]
   > Le associazioni del servizio SignalR richieste in Java sono supportate solo in Azure Function Core Tools versione 2.4.419 (versione host 2.0.12332) o versione successiva.

   > [!NOTE]
   > Per installare le estensioni, Azure Functions Core Tools richiede che sia installato [.NET Core SDK](https://www.microsoft.com/net/download). Tuttavia, non è necessaria alcuna conoscenza di .NET per compilare le app per le funzioni di Azure per JavaScript.

- [Java Developer Kit](https://www.azul.com/downloads/zulu/), versione 8
- [Apache Maven](https://maven.apache.org), versione 3.0 o successive

> [!NOTE]
> Questa guida introduttiva può essere eseguita su macOS, Windows o Linux.

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsjava).

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo <https://portal.azure.com/> con il proprio account Azure.

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsjava).

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsjava).

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsjava).

## <a name="configure-and-run-the-azure-function-app"></a>Configurare ed eseguire l'app per le funzioni di Azure

1. Nel browser in cui è aperto il portale di Azure, verificare che l'istanza del servizio SignalR distribuita in precedenza sia stata creata correttamente eseguendo una ricerca del nome nella casella di ricerca nella parte superiore del portale. Selezionare l'istanza per aprirla.

    ![Cercare l'istanza del servizio SignalR](media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-search-instance.png)

1. Selezionare **Chiavi** per visualizzare le stringhe di connessione per l'istanza del servizio SignalR.

1. Selezionare e copiare la stringa di connessione primaria.

    ![Creare il servizio SignalR](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-keys.png)

1. Nell'editor di codice aprire la cartella *src/chat/java* nel repository clonato.

1. Rinominare *local.settings.sample.json* in *local.settings.json*.

1. In **local.settings.json**, incollare la stringa di connessione nel valore dell'impostazione **AzureSignalRConnectionString**. Salvare il file.

1. Il file principale che contiene le funzioni si trova in *src/chat/java/src/main/java/com/function/Functions.java*:

    - **negotiate**: usa l'associazione di input *SignalRConnectionInfo* per generare e restituire informazioni di connessione valide.
    - **sendMessage**: riceve un messaggio di chat nel corpo della richiesta e usa l'associazione di output *SignalR* per trasmettere il messaggio a tutte le applicazioni client connesse.

1. Nel terminale assicurarsi di aver selezionato la cartella *src/chat/java*. Compilare l'app per le funzioni.

    ```bash
    mvn clean package
    ```

1. Eseguire l'app per le funzioni in locale.

    ```bash
    mvn azure-functions:run
    ```
    
Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsjava).

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsjava).

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Problemi? Vedere la [guida alla risoluzione dei problemi](signalr-howto-troubleshoot-guide.md) oppure [segnalarli](https://aka.ms/asrs/qsjava).

## <a name="next-steps"></a>Passaggi successivi

In questo avvio reale è stata creata ed eseguita un'applicazione serverless in tempo reale usando Maven. Nel prossimo articolo si apprenderanno altre informazioni sulla creazione di Funzioni di Azure Java da zero.

> [!div class="nextstepaction"]
> [Creare la prima funzione con Java e Maven](../azure-functions/functions-create-first-azure-function-azure-cli.md?pivots=programming-language-java%252cprogramming-language-java)
