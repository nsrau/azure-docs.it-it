---
title: 'Esercitazione: Autenticazione del servizio Azure SignalR con Funzioni di Azure'
description: Questa esercitazione spiega come autenticare i client del servizio Azure SignalR
author: sffamily
ms.service: signalr
ms.topic: tutorial
ms.custom: mvc
ms.date: 09/18/2018
ms.author: zhshang
ms.openlocfilehash: b66d4c9fb3bce03248f5696d5a7b2b7b62b7ffd3
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746372"
---
# <a name="tutorial-azure-signalr-service-authentication-with-azure-functions"></a>Esercitazione: Autenticazione del servizio Azure SignalR con Funzioni di Azure

Un'esercitazione dettagliata per creare una chat room con autenticazione e messaggistica privata usando Funzioni di Azure, Autenticazione servizio app e il servizio SignalR.

## <a name="introduction"></a>Introduzione

### <a name="technologies-used"></a>Tecnologie usate

* [Funzioni di Azure](https://azure.microsoft.com/services/functions/?WT.mc_id=serverlesschatlab-tutorial-antchu): API di back-end per l'autenticazione degli utenti e l'invio di messaggi di chat
* [Servizio Azure SignalR](https://azure.microsoft.com/services/signalr-service/?WT.mc_id=serverlesschatlab-tutorial-antchu): per la trasmissione di nuovi messaggi ai client di chat connessi
* [Archiviazione di Azure](https://azure.microsoft.com/services/storage/?WT.mc_id=serverlesschatlab-tutorial-antchu): per l'hosting del sito Web statico per l'interfaccia utente del client di chat

### <a name="prerequisites"></a>Prerequisiti

Per la compilazione in questa esercitazione è richiesto il software seguente.

* [Git](https://git-scm.com/downloads)
* [Node.js](https://nodejs.org/en/download/) (versione 10.x)
* [.NET SDK](https://www.microsoft.com/net/download) (versione 2.x, richiesta per le estensioni di Funzioni)
* [Strumenti di base di Funzioni di Azure](https://github.com/Azure/azure-functions-core-tools) (versione 2)
* [Visual Studio Code](https://code.visualstudio.com/) (VS Code) con le estensioni seguenti
  * [Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per usare Funzioni di Azure in VS Code
  * [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) per rendere disponibili le pagine Web in locale per il test

## <a name="sign-into-the-azure-portal"></a>Accedere al portale di Azure

Andare al [portale di Azure](https://portal.azure.com/) e accedere con le proprie credenziali.

## <a name="create-an-azure-signalr-service-instance"></a>Creare un'istanza del servizio Azure SignalR

L'app Funzioni di Azure verrà compilata e testata in locale. L'app accederà a un'istanza del servizio SignalR in Azure che deve essere già stata creata.

1. Fare clic sul pulsante **Crea una risorsa** (**+**) per creare una nuova risorsa di Azure.

1. Cercare il **servizio SignalR** e selezionarlo. Fare clic su **Create**(Crea).

    ![Nuovo servizio SignalR](media/signalr-authenticate-azure-functions/signalr-quickstart-new.png)

1. Immettere le seguenti informazioni.

    | NOME | Valore |
    |---|---|
    | Nome risorsa | Nome univoco per l'istanza del servizio SignalR |
    | Gruppo di risorse | Creare un nuovo gruppo di risorse |
    | Località | Selezionare una località vicina |
    | Piano tariffario | Gratuito |

1. Fare clic su **Create**(Crea).

## <a name="initialize-the-function-app"></a>Inizializzare l'app per le funzioni

### <a name="create-a-new-azure-functions-project"></a>Creare un nuovo progetto Funzioni di Azure

1. In una nuova finestra di VS Code usare `File > Open Folder` nel menu per creare e aprire una cartella vuota in un percorso appropriato. Questa sarà la cartella di progetto principale per l'applicazione da compilare.

1. Usando l'estensione Funzioni di Azure in VS Code, inizializzare un'app per le funzioni nella cartella di progetto principale.
    1. Aprire il riquadro comandi in VS Code selezionando **Visualizza > Riquadro comandi** dal menu (scelta rapida `Ctrl-Shift-P`, macOS `Cmd-Shift-P`).
    1. Cercare il comando **Funzioni di Azure: Crea nuovo progetto** e selezionarlo.
    1. Dovrebbe essere visualizzata la cartella di progetto principale. Selezionarla o usare "Sfoglia" per individuarla.
    1. Nel prompt per la scelta di un linguaggio selezionare **JavaScript**.

    ![Creare un'app per le funzioni](media/signalr-authenticate-azure-functions/signalr-create-vscode-app.png)

### <a name="install-function-app-extensions"></a>Installare le estensioni dell'app per le funzioni

Questa esercitazione usa le associazioni di Funzioni di Azure per interagire con il servizio Azure SignalR. Come la maggior parte delle altre associazioni, quelle del servizio SignalR sono disponibili come estensione da installare tramite l'interfaccia della riga di comando di Strumenti di base di Funzioni di Azure prima di poter essere usate.

1. Aprire un terminale in VS Code selezionando **Vista > Terminale integrato** dal menu (CTRL-\`).

1. Verificare che la cartella di progetto principale sia la directory corrente.

1. Installare l'estensione dell'app per le funzioni del servizio SignalR.

    ```bash
    func extensions install -p Microsoft.Azure.WebJobs.Extensions.SignalRService -v 1.0.0-preview1-10002
    ```

### <a name="configure-application-settings"></a>Configurare le impostazioni dell'applicazione

Quando si esegue e si sottopone a debug il runtime di Funzioni di Azure in locale, le impostazioni dell'applicazione vengono lette da **local.settings.json**. Aggiornare il file con la stringa di connessione dell'istanza del servizio SignalR creata in precedenza.

1. In VS Code selezionare **local.settings.json** nel riquadro Esplora per aprirlo.

1. Sostituire il contenuto del file con il seguente.

    ```json
    {
        "IsEncrypted": false,
        "Values": {
            "AzureSignalRConnectionString": "<signalr-connection-string>",
            "WEBSITE_NODE_DEFAULT_VERSION": "10.0.0",
            "FUNCTIONS_WORKER_RUNTIME": "node"
        },
        "Host": {
            "LocalHttpPort": 7071,
            "CORS": "*"
        }
    }
    ```

    * Immettere la stringa di connessione del servizio Azure SignalR in un'impostazione denominata `AzureSignalRConnectionString`. Ottenere il valore dalla pagina **Chiavi** nella risorsa del servizio Azure SignalR nel portale di Azure. Può essere usata la stringa di connessione primaria o secondaria.
    * L'impostazione `WEBSITE_NODE_DEFAULT_VERSION` non viene usata in locale, ma è obbligatoria per la distribuzione in Azure.
    * La sezione `Host` configura le impostazioni CORS e della porta per l'host di Funzioni locale (questa impostazione non ha effetto durante l'esecuzione in Azure).

    ![Ottenere la chiave del servizio SignalR](media/signalr-authenticate-azure-functions/signalr-get-key.png)

1. Salvare il file.

    ![Aggiornare le impostazioni locali](media/signalr-authenticate-azure-functions/signalr-update-local-settings.png)

## <a name="create-a-function-to-authenticate-users-to-signalr-service"></a>Creare una funzione per autenticare gli utenti per il servizio SignalR

Quando l'app di chat viene aperta per la prima volta nel browser, richiede credenziali di connessione valide per connettersi al servizio Azure SignalR. Si creerà una funzione attivata tramite HTTP denominata *SignalRInfo* nell'app per le funzioni per restituire queste informazioni di connessione.

1. Aprire il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`).

1. Cercare e selezionare il comando **Funzioni di Azure: Creare il comando Funzione** .

1. Quando richiesto, specificare le informazioni seguenti.

    | NOME | Valore |
    |---|---|
    | Cartella dell'app per le funzioni | Selezionare la cartella di progetto principale |
    | Modello | Trigger HTTP |
    | NOME | SignalRInfo |
    | Livello di autorizzazione | Anonima |

    Viene creata una cartella denominata **SignalRInfo** contenente la nuova funzione.

1. Aprire **SignalRInfo/function.json** per configurare le associazioni per la funzione. Modificare il contenuto del file come segue. In questo modo verrà aggiunta un'associazione di input che genera credenziali valide per la connessione di un client a un hub del servizio Azure SignalR denominato `chat`.

    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req"
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalRConnectionInfo",
                "name": "connectionInfo",
                "userId": "",
                "hubName": "chat",
                "direction": "in"
            }
        ]
    }
    ```

    La proprietà `userId` nell'associazione `signalRConnectionInfo` viene usata per creare una connessione autenticata al servizio SignalR. Lasciare vuota la proprietà per lo sviluppo locale. Verrà usata quando si distribuirà l'app per le funzioni in Azure.

1. Aprire **SignalRInfo/index.js** per visualizzare il corpo della funzione. Modificare il contenuto del file come segue.

    ```javascript
    module.exports = function (context, req, connectionInfo) {
        context.res = { body: connectionInfo };
        context.done();
    };
    ```

    Questa funzione accetta le informazioni di connessione a SignalR dall'associazione di input e le restituisce al client nel corpo della risposta HTTP.

## <a name="create-a-function-to-send-chat-messages"></a>Creare una funzione per inviare messaggi di chat

L'app Web richiede anche un'API HTTP per inviare i messaggi di chat. Si creerà una funzione attivata tramite HTTP denominata *SendMessage* che invia messaggi a tutti i client connessi tramite il servizio SignalR.

1. Aprire il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`).

1. Cercare e selezionare il comando **Funzioni di Azure: Creare il comando Funzione** .

1. Quando richiesto, specificare le informazioni seguenti.

    | NOME | Valore |
    |---|---|
    | Cartella dell'app per le funzioni | Selezionare la cartella di progetto principale |
    | Modello | Trigger HTTP |
    | NOME | SendMessage |
    | Livello di autorizzazione | Anonima |

    Viene creata una cartella denominata **SendMessage** contenente la nuova funzione.

1. Aprire **SendMessage/function.json** per configurare le associazioni per la funzione. Modificare il contenuto del file come segue.
    ```json
    {
        "disabled": false,
        "bindings": [
            {
                "authLevel": "anonymous",
                "type": "httpTrigger",
                "direction": "in",
                "name": "req",
                "route": "messages",
                "methods": [
                    "post"
                ]
            },
            {
                "type": "http",
                "direction": "out",
                "name": "res"
            },
            {
                "type": "signalR",
                "name": "signalRMessages",
                "hubName": "chat",
                "direction": "out"
            }
        ]
    }
    ```
    Vengono apportate due modifiche alla funzione originale:
    * La route viene modificata in `messages` e il trigger HTTP viene limitato al metodo HTTP **POST**.
    * Viene aggiunta un'associazione di output per il servizio SignalR che invia messaggi a tutti i client connessi a un hub del servizio SignalR denominato `chat`.

1. Salvare il file.

1. Aprire **SendMessage/index.js** per visualizzare il corpo della funzione. Modificare il contenuto del file come segue.

    ```javascript
    module.exports = function (context, req) {
        const message = req.body;
        message.sender = req.headers && req.headers['x-ms-client-principal-name'] || '';

        let recipientUserId = '';
        if (message.recipient) {
            recipientUserId = message.recipient;
            message.isPrivate = true;
        }

        context.bindings.signalRMessages = [{
            'userId': recipientUserId,
            'target': 'newMessage',
            'arguments': [ message ]
        }];
        context.done();
    };
    ```

    Questa funzione accetta il corpo della richiesta HTTP e lo invia ai client connessi al servizio SignalR, richiamando una funzione denominata `newMessage` in ogni client.

    La funzione può leggere l'identità del mittente e può accettare un valore *recipient* nel corpo del messaggio per consentire di inviare un messaggio privatamente a un singolo utente. Queste funzionalità verranno usate più avanti nell'esercitazione.

1. Salvare il file.

## <a name="create-and-run-the-chat-client-web-user-interface"></a>Creare ed eseguire l'interfaccia utente Web del client di chat

L'interfaccia utente dell'applicazione di chat è una semplice applicazione a pagina singola creata con il framework Vue JavaScript, che verrà ospitata separatamente dall'app per le funzioni. In locale, si eseguirà l'interfaccia Web usando l'estensione Live Server di VS Code.

1. In VS Code creare una nuova cartella denominata **content** alla radice della cartella di progetto principale.

1. Nella cartella **content** creare un nuovo file denominato **index.html**.

1. Copiare e incollare il contenuto di **[index.html](https://raw.githubusercontent.com/Azure-Samples/signalr-service-quickstart-serverless-chat/master/docs/demo/chat-with-auth/index.html)**.

1. Salvare il file.

1. Premere **F5** per eseguire l'app per le funzioni in locale e collegare un debugger.

1. Con **index.html** aperto, avviare Live Server aprendo il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`) e selezionando **Live Server: Apri con Live Server**. Live Server aprirà l'applicazione in un browser.

1. L'applicazione viene aperta. Immettere un messaggio nella casella di chat e premere INVIO. Aggiornare l'applicazione per vedere i nuovi messaggi. Non essendo stata configurata alcuna autenticazione, tutti i messaggi verranno inviati come "anonimi".

## <a name="deploy-to-azure-and-enable-authentication"></a>Distribuire in Azure e abilitare l'autenticazione

L'app per le funzioni e l'applicazione di chat sono state eseguite in locale. Ora verranno distribuite in Azure e si abiliteranno l'autenticazione e la messaggistica privata nell'applicazione.

### <a name="log-into-azure-with-vs-code"></a>Accedere ad Azure con VS Code

1. Aprire il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`).

1. Cercare e selezionare il comando **Azure: Sign in** (Azure: Accedi).

1. Seguire le istruzioni per completare il processo di accesso nel browser.

### <a name="configure-function-app-for-authentication"></a>Configurare l'app per le funzioni per l'autenticazione

Finora, l'app di chat ha funzionato in modo anonimo. In Azure si userà [Autenticazione servizio app](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization) per autenticare l'utente. L'ID utente o il nome utente dell'utente autenticato può essere passato all'associazione *SignalRConnectionInfo* per generare informazioni di connessione autenticate come utente.

Quando si invia un messaggio, l'app può decidere se inviarlo a tutti i client connessi o solo ai client che sono stati autenticati per un determinato utente.

1. In VS Code aprire **SignalRInfo/function.json**.

1. Inserire un'[espressione di associazione](https://docs.microsoft.com/azure/azure-functions/functions-triggers-bindings#binding-expressions-and-patterns) nella proprietà *userId* dell'associazione *SignalRConnectionInfo*: `{headers.x-ms-client-principal-name}`. Questa operazione imposta il valore sul nome utente dell'utente autenticato. L'aspetto dell'attributo dovrebbe essere simile al seguente.

    ```json
    {
        "type": "signalRConnectionInfo",
        "name": "connectionInfo",
        "userId": "{headers.x-ms-client-principal-name}",
        "hubName": "chat",
        "direction": "in"
    }
    ```

1. Salvare il file.

### <a name="deploy-function-app"></a>Distribuire l'app per le funzioni

1. Aprire il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`) e selezionare **Azure Functions: Deploy to Function App** (Funzioni di Azure: distribuisci nell'app per le funzioni).

1. Quando richiesto, specificare le informazioni seguenti.

    | NOME | Valore |
    |---|---|
    | Cartella per la distribuzione | Selezionare la cartella di progetto principale |
    | Sottoscrizione | Selezionare la propria sottoscrizione |
    | App per le funzioni | Selezionare **Crea nuova app per le funzioni** |
    | Nome dell'app per le funzioni | Immettere un nome univoco |
    | Gruppo di risorse | Selezionare lo stesso gruppo di risorse dell'istanza del servizio SignalR |
    | Account di archiviazione | Selezionare **Crea un nuovo account di archiviazione** |
    | Nome dell'account di archiviazione | Immettere un nome univoco (3-24 caratteri, solo alfanumerici) |
    | Località | Selezionare una località vicina |

    Viene creata una nuova app per le funzioni in Azure e la distribuzione ha inizio. Attendere il completamento della distribuzione.

### <a name="upload-function-app-local-settings"></a>Caricare le impostazioni locali dell'app per le funzioni

1. Aprire il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`).

1. Cercare e selezionare il comando **Funzioni di Azure: Comando Upload local settings** (Carica impostazioni locali).

1. Quando richiesto, specificare le informazioni seguenti.

    | NOME | Valore |
    |---|---|
    | File di impostazioni locali | local.settings.json |
    | Sottoscrizione | Selezionare la propria sottoscrizione |
    | App per le funzioni | Selezionare l'app per le funzioni distribuita in precedenza |
    | Nome dell'app per le funzioni | Immettere un nome univoco |

Le impostazioni locali vengono caricate nell'app per le funzioni in Azure. Se viene richiesto di sovrascrivere le impostazioni esistenti, selezionare **Sì a tutti**.

### <a name="enable-function-app-cross-origin-resource-sharing-cors"></a>Abilitare la condivisione di risorse tra le origini (CORS) per l'app per le funzioni

Anche se è disponibile un'impostazione CORS in **local.settings.json**, non è propagata all'app per le funzioni in Azure. È necessario impostarla separatamente.

1. Aprire il riquadro comandi di VS Code (`Ctrl-Shift-P`, macOS `Cmd-Shift-P`).

1. Cercare e selezionare il comando **Funzioni di Azure: Comando Open in portal** (Apri nel portale).

1. Selezionare la sottoscrizione e il nome dell'app per le funzioni per aprire l'app per le funzioni nel portale di Azure.

1. Nel portale che è stato aperto, nella scheda **Funzionalità della piattaforma** selezionare **CORS**.

    ![Trovare CORS](media/signalr-authenticate-azure-functions/signalr-find-cors.png)

1. Aggiungere una voce con il valore `*`.

1. Rimuovere tutte le altre voci esistenti.

1. Per salvare le impostazioni CORS, fare clic su **Salva**.

    ![Impostare CORS](media/signalr-authenticate-azure-functions/signalr-set-cors.png)

> [!NOTE]
> In un'applicazione reale, invece di consentire CORS in tutte le origini (`*`), un approccio più sicuro consiste nell'immettere voci specifiche per CORS per ogni dominio che lo richiede.

### <a name="update-the-web-app"></a>Aggiornare l'app Web

1. Nel portale di Azure passare alla pagina di panoramica dell'app per le funzioni.

1. Copiare l'URL dell'app per le funzioni.

    ![Ottenere l'URL](media/signalr-authenticate-azure-functions/signalr-get-url.png)

1. In VS Code aprire **index.html** e sostituire il valore di `apiBaseUrl` con l'URL dell'app per le funzioni.

1. L'applicazione può essere configurata con l'autenticazione usando Azure Active Directory, Facebook, Twitter, un account Microsoft o Google. Selezionare il provider di autenticazione da usare impostando il valore di `authProvider`.

1. Salvare il file.

### <a name="deploy-the-web-application-to-blob-storage"></a>Distribuire l'applicazione Web in Archiviazione BLOB

L'applicazione Web verrà ospitata usando la funzionalità dei siti Web statici di Archiviazione BLOB di Azure.

1. Fare clic sul pulsante **Nuova** (**+**) per creare una nuova risorsa di Azure.

1. In **Archiviazione** selezionare **Account di archiviazione**.

1. Immettere le seguenti informazioni.

    | NOME | Valore |
    |---|---|
    | NOME | Nome univoco per l'account di Archiviazione BLOB |
    | Tipo di account | Archiviazione V2 (utilizzo generico V2) |
    | Località | Selezionare la stessa area delle altre risorse |
    | Replica | Archiviazione con ridondanza locale |
    | Prestazioni | Standard |
    | Livello di accesso | Accesso frequente |
    | Gruppo di risorse | In questa esercitazione selezionare lo stesso gruppo di risorse delle altre risorse |

1. Fare clic su **Create**(Crea).

    ![Creazione dell'account di archiviazione](media/signalr-authenticate-azure-functions/signalr-create-storage.png)

1. Quando l'account di archiviazione viene creato, aprirlo nel portale di Azure.

1. Selezionare **Sito Web statico (anteprima)** nel riquadro di spostamento a sinistra.

1. Selezionare **Abilita**.

1. Immettere `index.html` come **Nome del documento di indice**.

1. Fare clic su **Save**.

    ![Configurare siti statici](media/signalr-authenticate-azure-functions/signalr-static-websites.png)

1. Fare clic sul collegamento **$web** nella pagina per aprire il contenitore BLOB.

1. Fare clic su **Carica** e caricare **index.html** nella cartella **content**.

1. Tornare alla pagina **Sito Web statico**. Prendere nota del valore di **Endpoint primario**. Si tratta dell'URL dell'applicazione Web.

### <a name="enable-app-service-authentication"></a>Abilitare l'autenticazione del servizio app

Autenticazione servizio app supporta l'autenticazione con Azure Active Directory, Facebook, Twitter, un account Microsoft e Google.

1. Con l'app per le funzioni ancora aperta nel portale individuare la scheda **Funzionalità della piattaforma** e selezionare **Autenticazione/Autorizzazione**.

1. **Abilitare** Autenticazione servizio app.

1. In **Azione da eseguire quando la richiesta non è autenticata** selezionare "Accedi con {provider di autenticazione selezionato in precedenza}".

1. In **URL di reindirizzamento esterni consentiti** immettere l'URL dell'endpoint Web primario dell'account di archiviazione annotato in precedenza.

1. Seguire la documentazione per il proprio provider di accesso per completare la configurazione.

    - [Azure Active Directory](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-aad)
    - [Facebook](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-facebook)
    - [Twitter](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-twitter)
    - [Account Microsoft](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-microsoft)
    - [Google](https://docs.microsoft.com/azure/app-service/configure-authentication-provider-google)

### <a name="try-the-application"></a>Provare l'applicazione

1. In un browser passare all'endpoint Web primario dell'account di archiviazione.

1. Selezionare **Accesso** per eseguire l'autenticazione con il provider di autenticazione scelto.

1. Inviare messaggi pubblici digitandoli nella casella di chat principale.

1. Inviare messaggi privati facendo clic su un nome utente nella cronologia delle chat. Solo il destinatario selezionato riceverà i messaggi.

Congratulazioni! È stata distribuita un'app di chat serverless in tempo reale.

![Demo](media/signalr-authenticate-azure-functions/signalr-serverless-chat.gif)

## <a name="clean-up-resources"></a>Pulire le risorse

Per pulire le risorse create in questa esercitazione, eliminare il gruppo di risorse usando il portale di Azure.

## <a name="next-steps"></a>Passaggi successivi

In questa esercitazione si è appreso come usare Funzioni di Azure con il servizio Azure SignalR. Sono disponibili altre informazioni sulla creazione di applicazioni serverless in tempo reale con le associazioni del servizio SignalR per Funzioni di Azure.

> [!div class="nextstepaction"]
> [Creare app in tempo reale con Funzioni di Azure](signalr-overview-azure-functions.md)
