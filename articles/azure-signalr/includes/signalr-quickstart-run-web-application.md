---
title: File di inclusione
description: File di inclusione
services: signalr
author: anthonychu
ms.service: signalr
ms.topic: include
ms.date: 09/14/2018
ms.author: antchu
ms.custom: include file
ms.openlocfilehash: 133fa1063a08303bfe4b4a973801b6bc22766ac3
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 09/24/2018
ms.locfileid: "47006287"
---
## <a name="run-the-web-application"></a>Eseguire l'applicazione Web

1. È disponibile un'applicazione Web a singola pagina di esempio ospitata in GitHub per comodità. Aprire il browser per [https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat/](https://azure-samples.github.io/signalr-service-quickstart-serverless-chat/demo/chat/).

    > [!NOTE]
    > L'origine del file HTML è disponibile all'indirizzo [/docs/demo/chat/index.html](https://github.com/Azure-Samples/signalr-service-quickstart-serverless-chat/blob/master/docs/demo/chat/index.html).

1. Quando viene richiesto l'URL di base dell'app per le funzioni, immettere *http://localhost:7071*.

1. Immettere un nome utente quando richiesto.

1. L'applicazione Web chiama la funzione *GetSignalRInfo* nell'app per le funzioni per recuperare le informazioni di connessione per connettersi al servizio Azure SignalR. Dopo aver completato la connessione, viene visualizzata la finestra di input dei messaggi chat.

1. Digitare un messaggio e premere invio. L'applicazione invia il messaggio alla funzione *SendMessage* nell'app per le funzioni di Azure, che usa quindi l'associazione di output di SignalR per trasmettere il messaggi a tutti i client connessi. Se tutto funziona correttamente, verrà visualizzato il messaggio nell'applicazione.

    ![Eseguire l'applicazione](../media/signalr-quickstart-azure-functions-csharp/signalr-quickstart-run-application.png)

1. Aprire un'altra istanza dell'applicazione Web in un'altra finestra del browser. Si noterà che tutti i messaggi inviati verranno visualizzati in tutte le istanze dell'applicazione.