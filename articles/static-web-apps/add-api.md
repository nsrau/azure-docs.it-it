---
title: Aggiungere un'API ad App Web statiche di Azure con Funzioni di Azure
description: Per iniziare a usare il sito App Web statiche di Azure, aggiungere un'API serverless all'app Web statica usando Funzioni di Azure.
services: static-web-apps
author: manekinekko
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/29/2020
ms.author: wachegha
ms.custom: devx-track-js
ms.openlocfilehash: 53be2ee9b6b3ab46f081747aec58b9c99fc54f0d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96349957"
---
# <a name="add-an-api-to-azure-static-web-apps-preview-with-azure-functions"></a>Aggiungere un'API ad App Web statiche di Azure (anteprima) con Funzioni di Azure

È possibile aggiungere API serverless al sito App Web statiche di Azure tramite l'integrazione con Funzioni di Azure. Questo articolo illustra come aggiungere e distribuire un'API in un sito App Web statiche di Azure.

## <a name="prerequisites"></a>Prerequisiti

- Account Azure con una sottoscrizione attiva.
  - Se non si dispone di un account, è possibile [crearne uno](https://azure.microsoft.com/free)gratuitamente.
- [Visual Studio Code](https://code.visualstudio.com/)
- [Estensione Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code
- Estensione [Live Server per Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer).
- [Node.js](https://nodejs.org/download/) eseguire l'app per le API in locale

## <a name="create-a-git-repository"></a>Creare un repository Git

La procedura seguente illustra come creare un nuovo repository e clonare i file nel computer.

1. Assicurarsi di aver effettuato l'accesso a GitHub e passare a [https://github.com/staticwebdev/vanilla-basic/generate](https://github.com/login?return_to=/staticwebdev/vanilla-basic/generate) per creare un nuovo repository.
1. Nella casella _Nome repository_ digitare **my-vanilla-api**.
1. Fare clic su **Create repository from template** (Crea repository da modello).

   :::image type="content" source="media/add-api/create-repository.png" alt-text="Creare un nuovo repository da vanilla-basic":::

Una volta creato il progetto, copiare l'URL nel browser per il nuovo repository. Questo URL viene usato in Visual Studio Code per clonare il repository git.

1. Premere **F1** per aprire il comando nel riquadro comandi.
1. Incollare l'URL nel prompt _Git: Clone_ e premere **INVIO**.

   :::image type="content" source="media/add-api/vscode-git-0.png" alt-text="Clonare un progetto GitHub usando Visual Studio Code":::

    Seguire le istruzioni per selezionare un percorso del repository per clonare il progetto.

## <a name="create-the-api"></a>Creare l'API

Successivamente, creare un progetto di funzioni di Azure come API dell'applicazione. 

1. All'interno del progetto _my-vanilla-api_ creare una sottocartella denominata **api**.
1. Premere **F1** per aprire il riquadro comandi
1. Digitare **Funzioni di Azure: Crea nuovo progetto...**
1. Premere **INVIO**
1. Scegliere **Sfoglia**
1. Selezionare la cartella **api** come directory per l'area di lavoro del progetto
1. Scegliere **Seleziona**

   :::image type="content" source="media/add-api/create-azure-functions-vscode-1.png" alt-text="Screenshot mostra la cartella A P I e il pulsante Seleziona.":::

1. Quando richiesto, immettere le informazioni seguenti:

    - _Seleziona una lingua_: scegli **JavaScript**
    - _Selezionare un modello per la prima funzione del progetto_: Scegliere **Trigger HTTP**
    - _Specificare un nome di funzione_: immettere **GetMessage**
    - _Livello di autorizzazione_: Scegliere **Anonymous**, che consente a chiunque di chiamare l'endpoint della funzione.
        - Per informazioni sui livelli di autorizzazione, vedere [Chiavi di autorizzazione](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys).

Visual Studio Code genera un progetto di funzioni di Azure con una funzione attivata tramite HTTP.

L'app dispone ora di una struttura di progetto simile all'esempio seguente.

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

Successivamente, si modificherà la `GetMessage` funzione per restituire un messaggio al front-end.

1. Aggiornare la `GetMessage` funzione in _API/GetMessage/index.js_ con il codice seguente.

    ```javascript
    module.exports = async function (context, req) {
      context.res = {
        body: {
          text: "Hello from the API"
        }
      };
    };
    ```

1. Aggiornare la configurazione di `GetMessage` in `api/GetMessage/function.json` con le impostazioni seguenti.

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

- Attivato quando viene effettuata una richiesta HTTP alla funzione
- Disponibile per tutte le richieste indipendentemente dallo stato di autenticazione
- Esposto tramite la route _/api/message_

## <a name="run-the-api-locally"></a>Eseguire l'API localmente

Visual Studio Code si integra con [Azure Functions Core Tools](../azure-functions/functions-run-local.md) per consentire l'esecuzione di questo progetto nel computer di sviluppo locale prima della pubblicazione in Azure.

> [!TIP]
> Assicurarsi di disporre di tutte le risorse elencate nella sezione [prerequisiti](#prerequisites) installata prima di procedere.

1. Eseguire la funzione premendo **F5** per avviare l'app funzioni.

1. Se Azure Functions Core Tools non è ancora stato installato, selezionare **Installa** quando richiesto.

    Gli strumenti di base mostrano l'output dell'applicazione in esecuzione nel pannello del _terminale_ . Come parte dell'output è possibile visualizzare l'endpoint dell'URL della funzione attivata tramite HTTP in esecuzione in locale.

    :::image type="content" source="media/add-api/create-azure-functions-vscode-2.png" alt-text="Screenshot mostra la scheda terminale in cui è possibile visualizzare U R L.":::

1. Con gli strumenti di base in esecuzione, passare all'URL seguente per verificare che l'API venga eseguita correttamente: `http://localhost:7071/api/message` .

   La risposta nel browser dovrebbe essere simile all'esempio seguente:

   :::image type="content" source="media/add-api/create-azure-functions-vscode-3.png" alt-text="Alt textScreenshot Mostra una risposta di testo in un browser.":::

1. Premere **MAIUSC + F5** per arrestare la sessione di debug.

### <a name="call-the-api-from-the-application"></a>Chiamare l'API dall'applicazione

Quando viene distribuita in Azure, le richieste all'API vengono indirizzate automaticamente all'app per le funzioni per le richieste inviate alla `api` Route. Lavorare localmente, è necessario configurare le impostazioni dell'applicazione per le richieste proxy all'API locale.

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

#### <a name="update-html-files-to-access-the-api"></a>Aggiornare i file HTML per accedere all'API

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
       <p>Loading content from the API: <b id="name">...</b></p>
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

1. Premere **F5** per avviare il progetto API.

1. Premere **F1** e scegliere **Live Server: Apri con Live Server**.

    A questo punto verrà visualizzato il messaggio API nella pagina Web.

   :::image type="content" source="media/add-api/create-azure-functions-vscode-4.png" alt-text="Screenshot Visualizza il messaggio a P I in un browser.":::

   > [!NOTE]
   > È possibile usare altri server o proxy HTTP per gestire il file `index.html`. L'accesso a `index.html` da `file:///` non funzionerà.

1. Premere **MAIUSC + F5** per arrestare il progetto API.

### <a name="commit-and-push-your-changes-to-github"></a>Eseguire il commit e il push delle modifiche in GitHub

Con Visual Studio Code, eseguire il commit e il push delle modifiche nel repository GIT remoto.

1. Premere **F1** per aprire il riquadro comandi
1. Digitare **Git: Esegui commit di tutto**
1. Aggiungere un messaggio di commit e premere **invio**
1. Premere **F1**
1. Digitare **git: push** e premere **invio**

## <a name="create-a-static-web-app"></a>Creare un'app Web statica

1. Passare al [portale di Azure](https://portal.azure.com)
1. Fare clic su **Crea una risorsa**
1. Cerca **app Web statica**
1. Fare clic su **app Web statica (anteprima)**
1. Fare clic su **Crea**

Aggiungere quindi le impostazioni specifiche dell'app.

1. Selezionare la _sottoscrizione di Azure_
1. Selezionare un _Gruppo di risorse_ o crearne uno nuovo
1. Assegnare all'app il nome **my-vanilla-api**.
1. Selezionare l'_Area_ più vicina
1. Selezionare lo **SKU** _Gratuito_
1. Fare clic sul pulsante **Sign-in with GitHub** (Accedi con GitHub) ed eseguire l'autenticazione con GitHub
1. Selezionare un valore per _Organizzazione_
1. Selezionare **my-vanilla-api** dall'elenco a discesa _Repository_
1. Selezionare **master** dall'elenco a discesa _Ramo_
1. Selezionare il Framework di propria scelta per la configurazione della compilazione dall'elenco a discesa _set di impostazioni di compilazione_

 > Questi campi riflettono la struttura del progetto predefinita del tipo di app. Modificare i valori per adattarli all'app.

Aggiungere quindi i dettagli di compilazione seguenti.

1. Immettere **/** per il _percorso dell'app_.
1. Immettere **api** nella casella _Percorso API_.
1. Cancellare il valore predefinito dal campo _Percorso artefatto app_ e lasciare vuota la casella.
1. Fare clic su **Rivedi e crea**.
1. Fare clic sul pulsante **Crea**

    Quando si fa clic sul pulsante _Create (crea_ ), Azure esegue due operazioni. In primo luogo, i servizi cloud sottostanti vengono creati per supportare l'app. Successivamente, un processo in background inizia a compilare e distribuire l'applicazione.

1. Fare clic sul pulsante **Vai alla risorsa per visualizzare** la pagina _Panoramica_ dell'app Web.

    Quando l'app viene compilata in background, è possibile fare clic sul banner contenente un collegamento per visualizzare lo stato della compilazione.

    :::image type="content" source="media/add-api/github-action-flag.png" alt-text="Flusso di lavoro GitHub":::

1. Al termine della distribuzione, l'unità organizzativa può passare all'app Web facendo clic sul collegamento _URL_ visualizzato nella pagina _Panoramica_ .

    :::image type="content" source="media/add-api/static-app-url-from-portal.png" alt-text="Accedere all'URL dell'app statica dal portale di Azure":::

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
