---
title: includere file
description: includere file
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 03/04/2019
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 2af9c1dfd13d3aeafa7cf1ac76537117ecc15aff
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/05/2020
ms.locfileid: "84317474"
---
## <a name="run-the-web-application"></a>Eseguire l'applicazione Web

1. Per semplificare i test dei client, aprire il browser all'indirizzo dell'applicazione Web a pagina singola di esempio [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat-v2/). 

    > [!NOTE]
    > Il codice sorgente del file HTML è disponibile all'indirizzo [/docs/demo/chat-v2/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat-v2/index.html). Se si vuole ospitare il codice HTML, avviare un server HTTP locale, ad esempio [http-server](https://www.npmjs.com/package/http-server) nella directory */docs/demo/chat-v2*. Assicurarsi che l'origine venga aggiunta all'impostazione `CORS` in *local.settings.json*, in modo simile all'esempio.
    > 
    > ```javascript
    > "Host": {
    >  "LocalHttpPort": 7071,
    >  "CORS": "http://localhost:8080,https://azure-samples.github.io",
    >  "CORSCredentials": true
    > }
    >
    > ```

1. Quando viene richiesto l'URL di base dell'app per le funzioni, immettere `http://localhost:7071`.

1. Immettere un nome utente quando richiesto.

1. L'applicazione Web chiama la funzione *GetSignalRInfo* nell'app per le funzioni per recuperare le informazioni di connessione per connettersi al servizio Azure SignalR. Dopo aver completato la connessione, viene visualizzata la finestra di input dei messaggi chat.

1. Digitare un messaggio e premere invio. L'applicazione invia il messaggio alla funzione *SendMessage* nell'app per le funzioni di Azure, che usa quindi l'associazione di output di SignalR per trasmettere il messaggi a tutti i client connessi. Se tutto funziona correttamente, verrà visualizzato il messaggio nell'applicazione.

    ![Eseguire l'applicazione](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Aprire un'altra istanza dell'applicazione Web in un'altra finestra del browser. Si noterà che tutti i messaggi inviati verranno visualizzati in tutte le istanze dell'applicazione.
