---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72e4318bea7245e440db4c7d95bf7f1f38bbe268
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095475"
---
### <a name="send-a-test-notification"></a>Invio di una notifica di prova

1. Aprire una nuova scheda in [Postman](https://www.postman.com/downloads/).

1. Impostare la richiesta su **POST** e immettere l'indirizzo seguente:

    ```xml
    https://<app_name>.azurewebsites.net/api/notifications/requests
    ```

1. Se si è scelto di completare la sezione [Autenticare i client usando una chiave API](#authenticate-clients-using-an-api-key-optional), assicurarsi di configurare le intestazioni della richiesta in modo da includere il valore di **apikey**.

   | Chiave                            | valore                          |
   | ------------------------------ | ------------------------------ |
   | apikey                         | <chiave_api>                 |

   > [!NOTE]
   > Questo valore si trova in **Config.local_secrets.cs** nel progetto **PushDemo**.

1. Scegliere l'opzione **raw** (non elaborato) per il **corpo**, quindi scegliere **JSON** dall'elenco delle opzioni di formato e includere del contenuto **JSON** segnaposto:

    ```json
    {
        "text": "Message from Postman!",
        "action": "action_a"
    }
    ```

1. Selezionare il pulsante **Code** (Codice), che si trova sotto il pulsante **Save** (Salva) nella parte superiore destra della finestra. La richiesta dovrebbe avere un aspetto simile all'esempio seguente quando viene visualizzata per **HTML** (a seconda che sia stata inclusa un'intestazione **apikey**):

    ```html
    POST /api/notifications/requests HTTP/1.1
    Host: https://<app_name>.azurewebsites.net
    apikey: <your_api_key>
    Content-Type: application/json

    {
        "text": "Message from backend service",
        "action": "action_a"
    }
    ```

1. Eseguire l'applicazione **PushDemo** in una o entrambe le piattaforme di destinazione (**Android** e **iOS**).

    > [!NOTE]
    > Se si esegue il test in **Android**, assicurarsi di non essere in modalità **Debug** o, se l'app è stata distribuita eseguendo l'applicazione, forzare la chiusura dell'app e avviarla di nuovo dall'utilità di avvio.

1. Nell'app **PushDemo** toccare il pulsante **Register** (Registra).

1. Tornare in **[Postman](https://www.postman.com/downloads)** , chiudere la finestra **Generate Code Snippets** (Genera frammenti di codice), se non lo si è già fatto, quindi fare clic sul pulsante **Send** (Invia).

1. Verificare di ricevere una risposta **200 OK** in **[Postman](https://www.postman.com/downloads)** e che nell'app venga visualizzato l'avviso **ActionA action received** (Azione ActionA ricevuta).  

1. Chiudere l'app **PushDemo**, quindi fare di nuovo clic sul pulsante **Send** (Invia) in **[Postman](https://www.postman.com/downloads)** .

1. Verificare di ricevere di nuovo una risposta **200 OK** in **[Postman](https://www.postman.com/downloads)** . Verificare che nell'area di notifica venga visualizzata una notifica per l'app **PushDemo** con il messaggio corretto.

1. Toccare la notifica per verificare che apra l'app con l'avviso **ActionA action received** (Azione ActionA ricevuta) visualizzato.

1. Tornare in **[Postman](https://www.postman.com/downloads)** , modificare il corpo della richiesta precedente per inviare una notifica automatica che specifica *action_b* anziché *action_a* per il valore **action**.

    ```json
    {
        "action": "action_b",
        "silent": true
    }
    ```

1. Con l'app ancora aperta, fare clic sul pulsante **Send** (Invia) in **[Postman](https://www.postman.com/downloads)** .

1. Verificare di ricevere una risposta **200 OK** in **[Postman](https://www.postman.com/downloads)** e che nell'app venga visualizzato l'avviso **ActionB action received** (Azione ActionB ricevuta) invece di **ActionA action received** (Azione ActionA ricevuta).

1. Chiudere l'app **PushDemo**, quindi fare di nuovo clic sul pulsante **Send** (Invia) in **[Postman](https://www.postman.com/downloads)** .

1. Verificare di ricevere una risposta **200 OK** in **[Postman](https://www.postman.com/downloads)** e che la notifica automatica non venga visualizzata nell'area di notifica.
