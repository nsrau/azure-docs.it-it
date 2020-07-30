---
title: Usare JavaScript per creare una chat room con Funzioni di Azure e il servizio SignalR
description: Una guida introduttiva per usare il servizio Azure SignalR e le Funzioni di Azure per la creazione di una chat room.
author: sffamily
ms.service: signalr
ms.devlang: javascript
ms.topic: quickstart
ms.date: 12/14/2019
ms.author: zhshang
ms.custom: devx-track-javascript
ms.openlocfilehash: 3f1051b81666a036203eb1c810875d837ef9816f
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2020
ms.locfileid: "87386878"
---
# <a name="quickstart-use-javascript-to-create-a-chat-room-with-azure-functions-and-signalr-service"></a>Guida introduttiva: Usare JavaScript per creare una chat room con Funzioni di Azure e il servizio SignalR

Il servizio Azure SignalR consente di aggiungere facilmente funzionalità in tempo reale all'applicazione e Funzioni di Azure è una piattaforma serverless che consente di eseguire il codice senza gestire alcuna infrastruttura. Questo argomento di avvio rapido illustra come usare JavaScript per creare un'applicazione serverless di chat in tempo reale con il Servizio SignalR e Funzioni di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Un editor di codice, ad esempio [Visual Studio Code](https://code.visualstudio.com/)
- Un account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Azure Functions Core Tools](https://github.com/Azure/azure-functions-core-tools#installing) versione 2 o successive. Usato per eseguire le app Funzioni di Azure in locale.
- [Node.js](https://nodejs.org/en/download/), versione 10.x

   > [!NOTE]
   > Gli esempi dovrebbero funzionare con altre versioni di Node.js; per altre informazioni, vedere la [documentazione sulle versioni di runtime di Funzioni di Azure](../azure-functions/functions-versions.md#languages).

> [!NOTE]
> Questa guida introduttiva può essere eseguita su macOS, Windows o Linux.

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

1. Nell'editor di codice aprire la cartella *src/chat/javascript* nel repository clonato.

1. Rinominare *local.settings.sample.json* in *local.settings.json*.

1. In **local.settings.json**, incollare la stringa di connessione nel valore dell'impostazione **AzureSignalRConnectionString**. Salvare il file.

1. Le funzioni JavaScript sono organizzate in cartelle. In ogni cartella ci sono due file: *function.json* definisce le associazioni usate nella funzione e *index.js* è il corpo della funzione. In questa app per le funzioni sono presenti due funzioni attivate tramite HTTP:

    - **negotiate**: usa l'associazione di input *SignalRConnectionInfo* per generare e restituire informazioni di connessione valide.
    - **messages**: riceve un messaggio di chat nel corpo della richiesta e usa l'associazione di output *SignalR* per trasmettere il messaggio a tutte le applicazioni client connesse.

1. Nel terminale assicurarsi di aver selezionato la cartella *src/chat/javascript*. Eseguire l'app per le funzioni.

    ```bash
    func start
    ```

    ![Creare il servizio SignalR](media/signalr-quickstart-azure-functions-javascript/signalr-quickstart-run-application.png)

[!INCLUDE [Run web application](includes/signalr-quickstart-run-web-application.md)]

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

## <a name="next-steps"></a>Passaggi successivi

In questo avvio reale è stata creata ed eseguita un'applicazione serverless in tempo reale in VS Code. Successivamente, si riceveranno altre informazioni su come distribuire le funzioni di Azure da VS Code.

> [!div class="nextstepaction"]
> [Distribuire le funzioni di Azure con VS Code](/azure/developer/javascript/tutorial-vscode-serverless-node-01)
