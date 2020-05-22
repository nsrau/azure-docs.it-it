---
title: Aggiungere un'API ad App Web statiche di Azure con Funzioni di Azure
description: Per iniziare a usare il sito App Web statiche di Azure, aggiungere un'API serverless all'app Web statica usando Funzioni di Azure.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: wachegha
ms.openlocfilehash: a6aee5c8049e03a43c547f419f6c6646617e651c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83596150"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Aggiungere un'API ad App Web statiche di Azure (anteprima) con Funzioni di Azure

È possibile aggiungere API serverless al sito App Web statiche di Azure tramite l'integrazione con Funzioni di Azure. Questo articolo illustra come aggiungere e distribuire un'API in un sito App Web statiche di Azure.

Per informazioni su come proteggere le route delle API, vedere la [guida al routing](routes.md).

## <a name="prerequisites"></a>Prerequisiti

- Account Azure con una sottoscrizione attiva. [Creare un account gratuitamente](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/)
- [Estensione Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code
- Estensione [Live Server per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer).

## <a name="create-a-git-repository"></a>Creare un repository GIT 

La procedura seguente illustra come creare un nuovo repository e clonare i file nel computer.

1. Passare a https://github.com/staticwebdev/vanilla-basic/generate per creare un nuovo repository.
1. Nella casella _Nome repository_ digitare **my-vanilla-api**.
1. Fare clic su **Create repository from template** (Crea repository da modello).

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Creare un nuovo repository da vanilla-basic":::

Una volta creato il progetto, è possibile usare Visual Studio Code per clonare il repository GIT.

1. Premere **F1** per aprire il comando nel riquadro comandi.
1. Incollare l'URL nel prompt _Git: Clone_ e premere **INVIO**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Clonare un progetto GitHub usando Visual Studio Code":::

   :::image type="content" source="media/add-api/github-clone-url.png" alt-text="Clonare un progetto GitHub usando Visual Studio Code":::


## <a name="create-your-local-project"></a>Creare il progetto locale

In questa sezione si userà Visual Studio Code per creare un progetto di Funzioni di Azure locale. In seguito si pubblicherà l'app per le funzioni in Azure.

1. All'interno del progetto _my-vanilla-api_ creare una sottocartella denominata **api**.

   > [!NOTE]
   > È possibile assegnare a questa cartella un nome qualsiasi. In questo esempio viene utilizzato `api`. 

2. Premere **F1** per aprire il riquadro comandi
3. Digitare **Funzioni di Azure: Crea nuovo progetto...**
4. Premere **INVIO**
5. Scegliere **Sfoglia**
6. Selezionare la cartella **api** come directory per l'area di lavoro del progetto
7. Scegliere **Seleziona**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Creare una nuova funzione di Azure con Visual Studio Code":::

8. Quando richiesto, immettere le informazioni seguenti:

    - _Selezionare un linguaggio per il progetto di funzione_: Scegliere **JavaScript**
    - _Selezionare un modello per la prima funzione del progetto_: Scegliere **Trigger HTTP**
    - _Specificare un nome di funzione_: Digitare **GetMessage**
    - _Livello di autorizzazione_: Scegliere **Anonymous**, che consente a chiunque di chiamare l'endpoint della funzione.
        - Per informazioni sui livelli di autorizzazione, vedere [Chiavi di autorizzazione](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

9. Usando queste informazioni, Visual Studio Code genera un progetto di Funzioni di Azure con un trigger HTTP.
    - È possibile visualizzare i file del progetto locale nella finestra Esplora risorse di Visual Studio Code.
    - Per altre informazioni sui file che vengono creati, vedere [File di progetto generati](https://docs.microsoft.com/azure/azure-functions/functions-develop-vs-code#generated-project-files).

10. A questo punto l'app dovrebbe avere una struttura di progetto simile a questo esempio.

    ```files
    ├── api
    │   ├── GetMessage
    │   │   ├── function.json
    │   │   ├── index.js
    │   │   └── sample.dat
    │   ├── host.json
    │   ├── local.settings.json
    │   ├── package.json
    │   └── proxies.json
    ├── index.html
    ├── readme.md
    └── styles.css
    ```

11. Aggiornare quindi la funzione `GetMessage` in _api/GetMessage/index.js_ con il codice seguente.

    ```JavaScript
    module.exports = async function (context, req) {
      context.res = {
        body: { 
          text: "Hello from the API" 
        }
      };
    };
    ```

12. Aggiornare la configurazione di `GetMessage` in `api/GetMessage/function.json` con le impostazioni seguenti.

    ```json
    {
      "bindings": [
        {
          "authLevel": "anonymous",
          "type": "httpTrigger",
          "direction": "in",
          "name": "req",
          "methods": [
            "get"
          ],
          "route": "message"
        },
        {
          "type": "http",
          "direction": "out",
          "name": "res"
        }
      ]
    }
    ```
    
Con le impostazioni precedenti, l'endpoint API è:

- Attivato quando viene inviata una richiesta HTTP alla funzione
- Disponibile per tutte le richieste indipendentemente dallo stato di autenticazione
- Esposto tramite la route _/api/message_

## <a name="run-the-function-locally"></a>Eseguire la funzione in locale

Visual Studio Code si integra con [Azure Functions Core Tools](https://docs.microsoft.com/azure/azure-functions/functions-run-local) per consentire l'esecuzione di questo progetto nel computer di sviluppo locale prima della pubblicazione in Azure.

1. Eseguire la funzione premendo **F5** per avviare l'app per le funzioni. L'output di Core Tools viene visualizzato nel pannello _Terminale_.

2. Se Azure Functions Core Tools non è ancora stato installato, selezionare **Installa** quando richiesto.

    Dopo aver installato Core Tools, l'app viene avviata nel pannello _Terminale_. Come parte dell'output è possibile visualizzare l'endpoint dell'URL della funzione attivata tramite HTTP in esecuzione in locale.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Creare una nuova funzione di Azure con Visual Studio Code":::

3. Con Core Tools in esecuzione, passare all'URL seguente per eseguire una richiesta `GET`.

   <http://localhost:7071/api/message>

   Viene restituita una risposta simile alla seguente nel browser:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Creare una nuova funzione di Azure con Visual Studio Code":::

Dopo aver verificato che la funzione viene eseguita correttamente, è possibile chiamare l'API dall'applicazione JavaScript.

### <a name="call-the-api-from-the-application"></a>Chiamare l'API dall'applicazione

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]


#### <a name="update-files-to-access-the-api"></a>Aggiornare i file per accedere all'API

1. Aggiornare quindi il contenuto del file _index. html_ con il codice seguente per recuperare il testo dalla funzione dell'API e visualizzarlo sullo schermo:

   ```html
   <!DOCTYPE html>
   <html lang="en">

   <head>
     <meta charset="UTF-8">
     <meta name="viewport" content="width=device-width, initial-scale=1.0">
     <link rel="stylesheet" href="styles.css">
     <title>Vanilla JavaScript App</title>
   </head>

   <body>
     <main>
       <h1>Vanilla JavaScript App</h1>
       <p>Loading message from the API: <b id="name">...</b></p>
     </main>

     <script>
       (async function() {
         let { text } = await( await fetch(`/api/message`)).json();
         document.querySelector('#name').textContent = text;
       }())
     </script>
   </body>

   </html>
   ```

   Con Core Tools in esecuzione, usare l'estensione [Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) per Visual Studio Code per gestire il file _index.html_ e aprirlo nel browser. 

2. Premere **F1** e scegliere **Live Server: Apri con Live Server**.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Creare una nuova funzione di Azure con Visual Studio Code":::

   > [!NOTE]
   > È possibile usare altri server o proxy HTTP per gestire il file `index.html`. L'accesso a `index.html` da `file:///` non funzionerà.

### <a name="commit-and-push-your-changes-to-github"></a>Eseguire il commit e il push delle modifiche in GitHub

Con Visual Studio Code, eseguire il commit e il push delle modifiche nel repository GIT remoto.

1. Premere **F1** per aprire il riquadro comandi
1. Digitare **Git: Esegui commit di tutto**
1. Aggiungere un messaggio di commit
1. Digitare **Git: push**

## <a name="create-a-static-web-app"></a>Creare un'app Web statica

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-1.png" alt-text="Creare un'app statica nel portale di Azure - schermata 1":::

1. Passare al [portale di Azure](https://portal.azure.com)
1. Fare clic su **Crea una risorsa**
1. Cercare **App Web statiche**
1. Fare clic su **App Web statiche (anteprima)**
1. Fare clic su **Crea**
1. Selezionare la _Sottoscrizione di Azure_
1. Selezionare un _Gruppo di risorse_ o crearne uno nuovo
1. Assegnare all'app il nome **my-vanilla-api**.
1. Selezionare l'_Area_ più vicina
1. Selezionare lo **SKU** _Gratuito_
1. Fare clic sul pulsante **Sign-in with GitHub** (Accedi con GitHub) ed eseguire l'autenticazione con GitHub
1. Selezionare un valore per _Organizzazione_
1. Selezionare **my-vanilla-api**dall'elenco a discesa _Repository_
1. Selezionare **master** dall'elenco a discesa _Ramo_
1. Fare clic sul pulsante **Avanti: Compilazione >** per modificare la configurazione della build

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-2.png" alt-text="Creare un'app statica nel portale di Azure - schermata 2":::

Aggiungere quindi i dettagli della build seguenti.

1. Immettere **./** per _Percorso app_.

1. Immettere **api** nella casella _Percorso API_.

   Si tratta del nome della cartella API creata nel passaggio precedente.
   
1. Cancellare il valore predefinito dal campo _Percorso artefatto app_ e lasciare vuota la casella.

1. Fare clic su **Rivedi e crea**.

| Impostazione | Descrizione             | Obbligatoria |
| -------- | ----------------------- |
|  Percorso app | Percorso del codice sorgente dell'applicazione statica | Sì |
|  Percorso API | Percorso del back-end dell'API, che punta alla cartella radice del progetto di app Funzioni di Azure | No |
|  Percorso artefatto app | Percorso della cartella di output di compilazione. Alcuni framework JavaScript front-end prevedono un'istruzione di compilazione che colloca i file di produzione in una cartella. Questa impostazione punta alla cartella di output di compilazione. | No |

:::image type="content" source="media/add-api/create-static-app-on-azure-portal-3.png" alt-text="Creare un'app statica nel portale di Azure - schermata 3":::

1. Fare clic su **Crea**
1. Attendere il completamento della distribuzione (l'operazione potrebbe richiedere un minuto)
1. Passare a `https://github.com/<YOUR_GITHUB_ACCOUNT>/my-vanilla-api/actions?query=workflow%3A"Azure+Pages+CI%2FCD"`
1. Assicurarsi che la compilazione abbia esito positivo

:::image type="content" source="media/add-api/github-workflow-0.png" alt-text="Flusso di lavoro GitHub":::

L'API distribuita sarà disponibile in `https://<STATIC_APP_NAME>.azurestaticapps.net/api/<FUNCTION_OR_ROUTE_NAME>`.

:::image type="content" source="media/add-api/github-workflow-1.png" alt-text="Flusso di lavoro GitHub":::

È anche possibile trovare l'URL dell'applicazione nel portale di Azure:

:::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Accedere all'URL dell'app statica dal portale di Azure":::

In alternativa, è possibile accedere direttamente all'app Web statica di Azure in `https://<STATIC_APP_NAME>.azurestaticapps.net` e controllare il risultato nel browser.

## <a name="clean-up-resources"></a>Pulire le risorse

Se non si intende usare l'applicazione in futuro, seguire questa procedura per eliminare l'app Web statica di Azure e le risorse correlate.

1. Passare al [portale di Azure](https://portal.azure.com)
1. Nella barra di ricerca superiore digitare **Gruppi di risorse**
1. Fare clic su **Gruppi di risorse** 
1. Selezionare **MyResourceGroup**
1. Nella pagina _myResourceGroup_ assicurarsi che le risorse elencate siano quelle da eliminare.
1. Selezionare **Elimina**
1. Digitare **myResourceGroup** nella casella di testo
1. Selezionare **Elimina**.


## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare le impostazioni dell'app](./application-settings.md)
