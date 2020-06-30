---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 343fef941870b6deaad777fac9b9d258d454aa2b
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095478"
---
#### <a name="no-response-from-the-backend-service"></a>Nessuna risposta dal servizio back-end

Durante i test in locale assicurarsi che il servizio back-end sia in esecuzione e che usi la porta corretta.

Se si eseguono i test con l'**app per le API di Azure**, controllare che il servizio sia in esecuzione, che sia stato distribuito e che sia stato avviato senza errori.

Assicurarsi di aver specificato correttamente l'indirizzo di base in **[Postman](https://www.postman.com/downloads)** o nella configurazione dell'app per dispositivi mobili durante i test tramite il client. L'indirizzo di base deve essere indicativamente `https://<api_name>.azurewebsites.net/` o `https://localhost:5001/` quando si eseguono test in locale.

#### <a name="not-receiving-notifications-on-android-after-starting-or-stopping-a-debug-session"></a>Mancata ricezione delle notifiche in Android dopo l'avvio o l'arresto di una sessione di debug

Assicurarsi di eseguire di nuovo la registrazione dopo l'avvio o l'arresto di una sessione di debug. Il debugger causerà la generazione di un nuovo token **Firebase**. È necessario aggiornare anche l'installazione dell'hub di notifica.

#### <a name="receiving-a-401-status-code-from-the-backend-service"></a>Ricezione di un codice di stato 401 dal servizio back-end

Verificare che si stia impostando l'intestazione della richiesta **apikey** e che questo valore corrisponda a quello configurato per il servizio back-end.

Se questo errore viene visualizzato durante i test in locale, assicurarsi che il valore della chiave definito nella configurazione del client corrisponda al valore dell'impostazione utente **Authentication:ApiKey** usato dall'[API](#create-the-api-app).

Se si eseguono test con un'**app per le API**, assicurarsi che il valore della chiave nel file di configurazione client corrisponda all'impostazione dell'applicazione **Authentication:ApiKey** usata nell'[app per le API](#create-the-api-app).

> [!NOTE]
> Se questa impostazione è stata creata o modificata dopo la distribuzione del servizio back-end, è necessario riavviare il servizio per renderla effettiva.

Se si è scelto di non completare la sezione [Autenticare i client usando una chiave API](#authenticate-clients-using-an-api-key-optional), assicurarsi di non aver applicato l'attributo **Authorize** alla classe **NotificationsController**.

#### <a name="receiving-a-404-status-code-from-the-backend-service"></a>Ricezione di un codice di stato 404 dal servizio back-end

Verificare che il metodo dell'endpoint e della richiesta HTTP sia corretto. Gli endpoint, ad esempio, devono essere indicativamente:

- **[PUT]** `https://<api_name>.azurewebsites.net/api/notifications/installations`
- **[DELETE]** `https://<api_name>.azurewebsites.net/api/notifications/installations/<installation_id>`
- **[POST]** `https://<api_name>.azurewebsites.net/api/notifications/requests`

Oppure durante i test in locale:

- **[PUT]** `https://localhost:5001/api/notifications/installations`
- **[DELETE]** `https://localhost:5001/api/notifications/installations/<installation_id>`
- **[POST]** `https://localhost:5001/api/notifications/requests`

Quando si specifica l'indirizzo di base nell'app client, assicurarsi che termini con `/`. L'indirizzo di base deve essere indicativamente `https://<api_name>.azurewebsites.net/` o `https://localhost:5001/` quando si eseguono test in locale.

#### <a name="unable-to-register-and-a-notification-hub-error-message-is-displayed"></a>Non è possibile eseguire la registrazione e viene visualizzato un messaggio di errore dell'hub di notifica

Verificare che il dispositivo di test abbia connettività di rete. Determinare quindi il codice di stato della risposta HTTP impostando un punto di interruzione per esaminare il valore della proprietà **StatusCode** in **HttpResponse**.

Esaminare i suggerimenti precedenti per la risoluzione dei problemi, se applicabili in base al codice di stato.

Impostare un punto di interruzione sulle righe che restituiscono questi codici di stato specifici per la rispettiva API. Provare quindi a chiamare il servizio back-end durante il debug in locale.

Verificare che il servizio back-end funzioni come previsto tramite **[Postman](https://www.postman.com/downloads)** usando il payload appropriato. Usare il payload effettivo creato dal codice client per la piattaforma in questione.

Esaminare le sezioni di configurazione specifiche della piattaforma per assicurarsi di aver eseguito tutti i passaggi. Verificare le variabili `installation id` e `token` vengano sostituite dai valori corretti per la piattaforma.

#### <a name="unable-to-resolve-an-id-for-the-device-error-message-is-displayed"></a>Visualizzazione del messaggio di errore che informa che non è possibile risolvere un ID per il dispositivo

Esaminare le sezioni di configurazione specifiche della piattaforma per assicurarsi di aver eseguito tutti i passaggi.
