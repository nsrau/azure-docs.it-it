---
title: Guida introduttiva al servizio Azure SignalR serverless - Java
description: Una guida introduttiva per usare il servizio Azure SignalR e le Funzioni di Azure per la creazione di una chat room.
author: sffamily
ms.service: signalr
ms.devlang: java
ms.topic: quickstart
ms.date: 03/04/2019
ms.author: zhshang
ms.openlocfilehash: 9e4e64b99a69e523547bae04146c7460d08bc1df
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59261174"
---
# <a name="quickstart-create-a-chat-room-with-azure-functions-and-signalr-service-using-java"></a>Avvio rapido: Creare una chat room con Funzioni di Azure e il servizio SignalR usando Java

Il servizio Azure SignalR consente di aggiungere facilmente funzionalità in tempo reale all'applicazione. Funzioni di Azure è una piattaforma serverless che consente di eseguire il codice senza gestire alcuna infrastruttura. Questa guida introduttiva fornisce informazioni su come usare il servizio SignalR e le funzioni per creare un'applicazione serverless di chat in tempo reale.

## <a name="prerequisites"></a>Prerequisiti

Questa guida introduttiva può essere eseguita su macOS, Windows o Linux.

Assicurarsi di disporre di un editor di codice installato, ad esempio [Visual Studio Code](https://code.visualstudio.com/).

Installare gli [Strumenti di base di Funzioni di Azure (v2)](https://github.com/Azure/azure-functions-core-tools#installing) per eseguire localmente le app per le funzioni di Azure.

> [!NOTE]
> Per usare le associazioni del servizio SignalR in Java, verificare di usare la versione 2.4.419 o versione successiva di Azure Functions Core Tools (versione host 2.0.12332).

Per installare le estensioni, gli strumenti di base di Funzioni di Azure richiedono attualmente che sia installato [.NET Core SDK](https://www.microsoft.com/net/download). Tuttavia, non è necessaria alcuna conoscenza di .NET per compilare le app per le funzioni di Azure per JavaScript.

Per sviluppare app per le funzioni con Java, è necessario che siano installati gli elementi seguenti:

* [Java Developer Kit](https://www.azul.com/downloads/zulu/), versione 8.
* [Apache Maven](https://maven.apache.org), versione 3.0 o successiva.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-azure"></a>Accedere ad Azure

Accedere al portale di Azure all'indirizzo <https://portal.azure.com/> con il proprio account Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

[!INCLUDE [Clone application](includes/signalr-quickstart-clone-application.md)]

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

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo avvio reale è stata creata ed eseguita un'applicazione serverless in tempo reale usando Maven. Nel prossimo articolo si apprenderanno altre informazioni sulla creazione di Funzioni di Azure Java da zero.

> [!div class="nextstepaction"]
> [Creare la prima funzione con Java e Maven](../azure-functions/functions-create-first-java-maven.md)