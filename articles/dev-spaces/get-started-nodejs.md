---
title: Creare un ambiente di sviluppo Node.js Kubernetes nel cloud con VS Code | Documentazione Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: ghogen
ms.author: ghogen
ms.date: 05/11/2018
ms.topic: tutorial
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, contenitori
manager: douge
ms.openlocfilehash: 0507208e58323fd31bb7c6cdb3a293ec0179cabe
ms.sourcegitcommit: 3017211a7d51efd6cd87e8210ee13d57585c7e3b
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 06/06/2018
ms.locfileid: "34823912"
---
# <a name="get-started-on-azure-dev-spaces-with-nodejs"></a>Guida introduttiva ad Azure Dev Spaces con Node.js

[!INCLUDE[](includes/learning-objectives.md)]

[!INCLUDE[](includes/see-troubleshooting.md)]

È ora possibile creare un ambiente di sviluppo basato su Kubernetes in Azure.

[!INCLUDE[](includes/portal-aks-cluster.md)]

## <a name="install-the-azure-cli"></a>Installare l'interfaccia della riga di comando di Azure
Azure Dev Spaces richiede un'installazione minima nel computer locale. La maggior parte della configurazione dell'ambiente di sviluppo viene archiviata nel cloud ed è condivisibile con altri utenti. Per iniziare, scaricare ed eseguire l'[interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

> [!IMPORTANT]
> Se l'interfaccia della riga di comando di Azure è già installata, assicurarsi di usare la versione 2.0.33 o una successiva.

[!INCLUDE[](includes/sign-into-azure.md)]

[!INCLUDE[](includes/use-dev-spaces.md)]

[!INCLUDE[](includes/install-vscode-extension.md)]

Mentre si attende che venga creato il cluster, si può iniziare a scrivere il codice.

## <a name="create-a-nodejs-container-in-kubernetes"></a>Creare un contenitore Node.js in Kubernetes

In questa sezione si creerà un'app Web Node.js e si eseguirà tale app in un contenitore in Kubernetes.

### <a name="create-a-nodejs-web-app"></a>Creare un'app Web Node.js
Scaricare il codice da GitHub passando a https://github.com/Azure/dev-spaces e selezionando **Clone or Download** (Clona o scarica) per scaricare il repository GitHub nell'ambiente locale. Il codice per questa guida è disponibile in `samples/nodejs/getting-started/webfrontend`.

[!INCLUDE[](includes/azds-prep.md)]

[!INCLUDE[](includes/build-run-k8s-cli.md)]

### <a name="update-a-content-file"></a>Aggiornare un file di contenuto
Azure Dev Spaces consente non solo di eseguire codice in Kubernetes, ma anche di visualizzare in modo rapido e iterativo l'applicazione delle modifiche apportate al codice in un ambiente Kubernetes nel cloud.

1. Individuare il file `./public/index.html` e apportare una modifica al codice HTML. Ad esempio, modificare il colore di sfondo della pagina impostandolo su una sfumatura di blu:

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

2. Salvare il file. Poco dopo, nella finestra del terminale verrà visualizzato un messaggio che informa che un file nel contenitore in esecuzione è stato aggiornato.
1. Passare al browser e aggiornare la pagina. Verrà visualizzato l'aggiornamento del colore.

Che cosa è successo? Le modifiche ai file di contenuto, come HTML e CSS, non richiedono il riavvio del processo Node.js, di conseguenza un comando `azds up` attivo sincronizzerà automaticamente qualsiasi file di contenuto modificato direttamente nel contenitore in esecuzione in Azure, offrendo così un modo rapido per visualizzare le modifiche apportate al contenuto.

### <a name="test-from-a-mobile-device"></a>Eseguire test da un dispositivo mobile
Se si apre l'app Web su un dispositivo mobile, si noterà che l'interfaccia utente non viene visualizzata correttamente su un dispositivo di piccole dimensioni.

Per risolvere il problema, si aggiungerà un tag META `viewport`:
1. Aprire il file `./public/index.html`
1. Aggiungere un tag META `viewport` nell'elemento `head` esistente:

    ```html
    <head>
        <!-- Add this line -->
        <meta name="viewport" content="width=device-width, initial-scale=1">
    </head>
    ```

1. Salvare il file.
1. Aggiornare il browser del dispositivo. Il rendering dell'app Web dovrebbe ora essere visualizzato correttamente. 

Questo è un esempio di come alcuni problemi non siano rilevabili finché non si eseguono test sui dispositivi in cui è previsto che l'app venga usata. Con Azure Dev Spaces, è possibile eseguire rapidamente l'iterazione del codice e convalidare le modifiche sui dispositivi di destinazione.

### <a name="update-a-code-file"></a>Aggiornare un file di codice
L'aggiornamento di file di codice sul lato server richiede alcune operazioni aggiuntive, perché un'app Node.js deve essere riavviata.

1. Nella finestra del terminale premere `Ctrl+C` per arrestare `azds up`.
1. Aprire il file di codice denominato `server.js` e modificare il messaggio Hello del servizio: 

    ```javascript
    res.send('Hello from webfrontend running in Azure!');
    ```

3. Salvare il file.
1. Eseguire `azds up` nella finestra del terminale. 

In questo modo viene ricompilata l'immagine del contenitore e viene ridistribuito il chart Helm. Ricaricare la pagina del browser per visualizzare l'applicazione delle modifiche apportate al codice.

Esiste tuttavia un *metodo ancora più rapido* per lo sviluppo di codice, che verrà esaminato nella sezione successiva. 

## <a name="debug-a-container-in-kubernetes"></a>Eseguire il debug di un contenitore in Kubernetes

[!INCLUDE[](includes/debug-intro.md)]

[!INCLUDE[](includes/init-debug-assets-vscode.md)]

### <a name="select-the-azds-debug-configuration"></a>Selezionare la configurazione di debug di AZDS
1. Per aprire la visualizzazione Debug, fare clic sull'icona Debug nella **barra attività** sul lato di VS Code.
1. Selezionare **Launch Program (AZDS)** (Avvia programma - AZDS) come configurazione di debug attiva.

![](media/get-started-node/debug-configuration-nodejs.png)

> [!Note]
> Se nel riquadro comandi non vengono visualizzati comandi di Azure Dev Spaces, assicurarsi di aver [installato l'estensione di VS Code per Azure Dev Spaces](get-started-nodejs.md#get-kubernetes-debugging-for-vs-code).

### <a name="debug-the-container-in-kubernetes"></a>Eseguire il debug del contenitore in Kubernetes
Premere **F5** per eseguire il debug del codice in Kubernetes.

Analogamente a quanto avviene con il comando `up`, all'avvio del debug viene sincronizzato il codice con l'ambiente di sviluppo e viene compilato e distribuito un contenitore in Kubernetes. In questo caso, il debugger è associato al contenitore remoto.

[!INCLUDE[](includes/tip-vscode-status-bar-url.md)]

Impostare un punto di interruzione in un file di codice sul lato server, ad esempio `app.get('/api'...` in `server.js`. Aggiornare la pagina del browser oppure fare clic sul pulsante "Say It Again" (Ripeti). Si raggiungerà il punto di interruzione e sarà possibile scorrere il codice.

Le informazioni di debug, come stack di chiamate, variabili locali, informazioni sulle eccezioni e così via, sono completamente accessibili come in caso di esecuzione del codice in locale.

### <a name="edit-code-and-refresh-the-debug-session"></a>Modificare il codice e aggiornare la sessione di debug
Con il debugger attivo, apportare una modifica al codice. Ad esempio, modificare di nuovo il messaggio Hello:

```javascript
app.get('/api', function (req, res) {
    res.send('**** Hello from webfrontend running in Azure! ****');
});
```

Salvare il file e quindi nel **riquadro delle azioni di debug** fare clic sul pulsante **Aggiorna**. 

![](media/get-started-node/debug-action-refresh-nodejs.png)

Invece di ricompilare e ridistribuire una nuova immagine del contenitore ogni volta che vengono apportate modifiche al codice, operazione che spesso richiede una notevole quantità di tempo, Azure Dev Spaces riavvierà il processo Node.js tra le sessioni di debug in modo da velocizzare il ciclo di modifica/debug.

Aggiornare l'app Web nel browser oppure fare clic sul pulsante *Say It Again* (Ripeti). Nell'interfaccia utente verrà visualizzato il messaggio personalizzato.

### <a name="use-nodemon-to-develop-even-faster"></a>Usare nodemon per velocizzare ulteriormente lo sviluppo
*nodemon* è uno strumento diffuso usato dagli sviluppatori Node.js per lo sviluppo rapido. Invece di riavviare manualmente il processo Node ogni volta che viene apportata una modifica al codice sul lato server, gli sviluppatori spesso configurano il progetto Node in modo che *nodemon* monitori le modifiche ai file e riavvii automaticamente il processo server. Con questo stile di lavoro, lo sviluppatore dopo aver apportato una modifica al codice si limita ad aggiornare il browser.

Con Azure Dev Spaces è possibile usare molti dei flussi di lavoro di sviluppo usati nello sviluppo in locale. Per illustrare questo aspetto, il progetto `webfrontend` di esempio è stato configurato per l'uso di *nodemon* (configurato come dipendenza di sviluppo in `package.json`).

Attenersi alla procedura seguente:
1. Arrestare il debugger di VS Code.
1. Fare clic sull'icona Debug nella **barra attività** sul lato di VS Code. 
1. Selezionare **Attach (AZDS)** (Associa - AZDS) come configurazione di debug attiva.
1. Premere F5.

In questa configurazione, il contenitore è configurato per avviare *nodemon*. Quando vengono apportate modifiche al codice del server, *nodemon* riavvia automaticamente il processo Node esattamente come in caso di sviluppo in locale. 
1. Modificare di nuovo il messaggio Hello in `server.js` e salvare il file.
1. Aggiornare il browser oppure fare clic sul pulsante *Say It Again* (Ripeti) per visualizzare l'applicazione delle modifiche.

**È ora possibile usare questo metodo per eseguire rapidamente l'iterazione e il debug di codice direttamente in Kubernetes.** Successivamente, si vedrà come è possibile creare e chiamare un secondo contenitore.

## <a name="call-a-service-running-in-a-separate-container"></a>Chiamare un servizio in esecuzione in un contenitore separato

In questa sezione si creerà un secondo servizio, `mywebapi`, al quale `webfrontend` assegnerà un nome. Ogni servizio viene eseguito in contenitori separati. Verrà quindi eseguito il debug in entrambi i contenitori.

![](media/common/multi-container.png)

### <a name="open-sample-code-for-mywebapi"></a>Aprire il codice di esempio per *mywebapi*
Il codice di esempio `mywebapi` per questa guida dovrebbe già essere disponibile in una cartella denominata `samples` (in caso contrario, passare a https://github.com/Azure/dev-spaces e selezionare **Clone or Download**, Clona o scarica, per scaricare il repository GitHub). Il codice per questa sezione è disponibile in `samples/nodejs/getting-started/mywebapi`.

### <a name="run-mywebapi"></a>Eseguire *mywebapi*
1. Aprire la cartella `mywebapi` in una *finestra di VS Code separata*.
1. Premere F5 e attendere la compilazione e la distribuzione del servizio. Il servizio è pronto quando viene visualizzata la barra di debug di VS Code.
1. Prendere nota dell'URL dell'endpoint che sarà simile a http://localhost:\<portnumber\>. **Suggerimento: la barra di stato di VS Code visualizza un URL selezionabile.** Potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito nell'ambiente di sviluppo in Azure. Il motivo dell'indirizzo localhost è che `mywebapi` non ha definito alcun endpoint pubblico ed è accessibile solo dall'interno dell'istanza di Kubernetes. Per praticità e per agevolare l'interazione con il servizio privato nel computer locale, Azure Dev Spaces crea un tunnel SSH temporaneo al contenitore in esecuzione in Azure.
1. Quando `mywebapi` è pronto, aprire il browser all'indirizzo localhost. Dovrebbe venire visualizzata una risposta dal servizio `mywebapi` ("Salve da mywebapi").


### <a name="make-a-request-from-webfrontend-to-mywebapi"></a>Creare una richiesta da *webfrontend* a *mywebapi*
Ora scriviamo il codice in `webfrontend` che crea una richiesta a `mywebapi`.
1. Passare alla finestra di VS Code per `webfrontend`.
1. Aggiungere queste righe di codice nella parte superiore di `server.js`:
    ```javascript
    var request = require('request');
    ```

3. *Sostituire* il codice per il gestore GET `/api`. Quando si gestisce una richiesta, questa a sua volta effettua una chiamata a `mywebapi`, quindi restituisce i risultati da entrambi i servizi.

    ```javascript
    app.get('/api', function (req, res) {
       request({
          uri: 'http://mywebapi',
          headers: {
             /* propagate the dev space routing header */
             'azds-route-as': req.headers['azds-route-as']
          }
       }, function (error, response, body) {
           res.send('Hello from webfrontend and ' + body);
       });
    });
    ```

L'esempio di codice precedente inoltra l'intestazione `azds-route-as` dalla richiesta in ingresso alla richiesta in uscita. Più avanti si apprenderà come ciò aiuti i team nello sviluppo collaborativo.

### <a name="debug-across-multiple-services"></a>Eseguire il debug tra più servizi
1. A questo punto `mywebapi` dovrebbe ancora essere in esecuzione con il debugger collegato. In caso contrario, premere F5 nel progetto `mywebapi`.
1. Impostare un punto di interruzione nel gestore GET `/` predefinito.
1. Nel progetto `webfrontend`, impostare un punto di interruzione prima che venga inviata una richiesta GET a `http://mywebapi`.
1. Premere F5 nel progetto `webfrontend`.
1. Aprire l'app Web e scorrere il codice in entrambi i servizi. Nell'app Web dovrebbe essere visualizzato un messaggio concatenato dai due servizi: "Salve da webfrontend e Salve da mywebapi".

Ecco fatto! È ora disponibile un'applicazione multicontenitore in cui ogni contenitore può essere sviluppato e distribuito separatamente.

## <a name="learn-about-team-development"></a>Informazioni sui team di sviluppo

[!INCLUDE[](includes/team-development-1.md)]

Provalo:
1. Passare alla finestra di VS Code per `mywebapi` e apportare una modifica al codice per il gestore GET `/` predefinito, ad esempio:

    ```javascript
    app.get('/', function (req, res) {
        res.send('mywebapi now says something new');
    });
    ```

[!INCLUDE[](includes/team-development-2.md)]

[!INCLUDE[](includes/well-done.md)]

[!INCLUDE[](includes/clean-up.md)]




