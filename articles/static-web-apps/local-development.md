---
title: Configurare lo sviluppo locale per le app Web statiche di Azure
description: Informazioni su come configurare l'ambiente di sviluppo locale per le app Web statiche di Azure
services: static-web-apps
author: burkeholland
ms.service: static-web-apps
ms.topic: how-to
ms.date: 05/08/2020
ms.author: buhollan
ms.custom: devx-track-javascript
ms.openlocfilehash: d9988adea8e4d2dab39de64f9fd155a9d0b02cae
ms.sourcegitcommit: 0b8320ae0d3455344ec8855b5c2d0ab3faa974a3
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/30/2020
ms.locfileid: "87432975"
---
# <a name="set-up-local-development-for-azure-static-web-apps-preview"></a>Configurare lo sviluppo locale per App Web statiche di Azure (anteprima)

Un'istanza di App Web statiche di Azure è costituita da due tipi diversi di applicazioni. La prima è un'app Web per il contenuto statico. Le app Web vengono spesso create con framework e librerie front-end o con generatori di siti statici. Il secondo aspetto è l'API, ovvero un'app Funzioni di Azure che fornisce un ambiente di sviluppo back-end avanzato.

Quando sono in esecuzione nel cloud, le app Web statiche di Azure eseguono il mapping delle richieste alla route `api` dall'app Web all'app Funzioni di Azure senza richiedere la configurazione della condivisione di risorse tra le origini (CORS). A livello locale, è necessario configurare l'applicazione in modo da simulare questo comportamento.

In questo articolo vengono illustrate le procedure consigliate per lo sviluppo locale, inclusi i concetti seguenti:

- Configurazione dell'app Web per il contenuto statico
- Configurazione dell'app Funzioni di Azure per l'API dell'applicazione
- Debug ed esecuzione dell'applicazione
- Procedure consigliate per la struttura di file e cartelle dell'app

## <a name="prerequisites"></a>Prerequisiti

- [Visual Studio Code](https://code.visualstudio.com/)
- [Estensione Funzioni di Azure](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) per Visual Studio Code
- [Estensione Live Server](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer) per Visual Studio Code
  - Necessaria solo se non si usa un framework JavaScript front-end o l'interfaccia della riga di comando del generatore di siti statici

## <a name="run-projects-locally"></a>Eseguire i progetti in locale

L'esecuzione di un'app Web statica di Azure in locale comporta tre processi, a seconda che il progetto contenga o meno un'API.

- Esecuzione di un server Web locale
- Esecuzione dell'API
- Connessione del progetto Web all'API

A seconda di come è stato creato un sito Web, è possibile che un server Web locale non sia necessario per eseguire l'applicazione nel browser. Quando si usano framework JavaScript front-end e generatori di siti statici, questa funzionalità è incorporata nelle rispettive interfacce della riga di comando. I collegamenti seguenti indirizzano alle informazioni di riferimento sull'interfaccia della riga di comando per una selezione di framework, librerie e generatori.

### <a name="javascript-frameworks-and-libraries"></a>Framework e librerie JavaScript

- [Interfaccia della riga di comando di Angular](https://angular.io/cli)
- [Interfaccia della riga di comando di Vue](https://cli.vuejs.org/guide/creating-a-project.html)
- [Interfaccia della riga di comando di React](https://create-react-app.dev/)

### <a name="static-site-generators"></a>Generatori di siti statici

- [Interfaccia della riga di comando di Gatsby](https://www.gatsbyjs.org/docs/gatsby-cli/)
- [Hugo](https://gohugo.io/getting-started/quick-start/)
- [Jekyll](https://jekyllrb.com/docs/usage/)

Se si usa uno strumento dell'interfaccia della riga di comando per gestire il sito, è possibile passare alla sezione [Esecuzione dell'API](#run-api-locally).

### <a name="running-a-local-web-server-with-live-server"></a>Esecuzione di un server Web locale con Live Server

L'estensione Live Server per Visual Studio Code fornisce un server Web di sviluppo locale per la gestione del contenuto statico.

#### <a name="create-a-repository"></a>Creare un repository

1. Assicurarsi di essere connessi a GitHub e, passare a [https://github.com/staticwebdev/vanilla-api/generate](https://github.com/staticwebdev/vanilla-api/generate) e creare un nuovo progetto GitHub denominato **Vanilla-API**, usando questo modello.

    :::image type="content" source="media/local-development/vanilla-api.png" alt-text="Finestra nuovo repository GitHub":::

1. Aprire Visual Studio Code.

1. Premere **F1** per aprire il riquadro comandi.

1. Digitare **clone** nella casella di ricerca e selezionare **Git: Clone**.

    :::image type="content" source="media/local-development/command-palette-git-clone.png" alt-text="Opzione git clone in Visual Studio Code":::

1. Immettere il valore seguente per **URL del repository**.

   ```http
   git@github.com:<YOUR_GITHUB_ACCOUNT>/vanilla-api.git
   ```

1. Selezionare un percorso di cartella per il nuovo progetto.

1. Quando viene richiesto di aprire il repository clonato, selezionare **Apri**.

    :::image type="content" source="media/local-development/open-new-window.png" alt-text="Apri in un'altra finestra":::

Visual Studio Code apre il progetto clonato nell'editor.

### <a name="run-the-website-locally-with-live-server"></a>Eseguire il sito Web in locale con Live Server

1. Premere **F1** per aprire il riquadro comandi.

1. Digitare **Live Server** nella casella di ricerca e selezionare **Live Server: Open with Live Server** (Apri con Live Server)

    Verrà visualizzata una scheda del browser per visualizzare l'applicazione.

    :::image type="content" source="media/local-development/vanilla-api-site.png" alt-text="Sito statico semplice in esecuzione nel browser":::

    Questa applicazione invia una richiesta HTTP all'endpoint `api/message`. In questo momento, la richiesta ha esito negativo perché la parte API dell'applicazione deve essere avviata.

### <a name="run-api-locally"></a>Eseguire l'API in locale

Le API delle app Web statiche di Azure sono basate su Funzioni di Azure. Per informazioni dettagliate su come aggiungere un'API a un progetto di app Web statiche di Azure, vedere [Aggiungere un'API alle app Web statiche di Azure con Funzioni di Azure](add-api.md).

Come parte del processo di creazione dell'API, viene creata una configurazione di avvio per Visual Studio Code. Questa configurazione è disponibile nella cartella _.vscode_. Questa cartella contiene tutte le impostazioni necessarie per la compilazione e l'esecuzione dell'API in locale.

1. In Visual Studio Code premere **F5** per avviare l'API.

1. Verrà aperta una nuova finestra del terminale che mostra l'output del processo di compilazione dell'API.

    :::image type="content" source="media/local-development/terminal-api-debug.png" alt-text="API in esecuzione nel terminale in Visual Studio Code":::

   La barra di stato in Visual Studio Code è ora arancione. Questo colore indica che l'API è in esecuzione e che il debugger è collegato.

1. Premere quindi **CTRL/CMD** e fare clic sull'URL nel terminale per aprire una finestra del browser che chiama l'API.

    :::image type="content" source="media/local-development/hello-from-api-endpoint.png" alt-text="Risultato della chiamata API visualizzato nel browser":::

### <a name="debugging-the-api"></a>Debug dell'API

1. Aprire il file _api/GetMessage/index.js_ in Visual Studio Code.

1. Fare clic sul margine sinistro nella riga 2 per impostare un punto di interruzione. Viene visualizzato un punto rosso che indica che il punto di interruzione è impostato.

    :::image type="content" source="media/local-development/breakpoint-set.png" alt-text="Punto di interruzione in Visual Studio Code":::

1. Nel browser aggiornare la pagina in esecuzione in <http://127.0.0.1:7071/api/message>.

1. Il punto di interruzione viene raggiunto in Visual Studio Code e l'esecuzione del programma viene sospesa.

   :::image type="content" source="media/local-development/breakpoint-hit.png" alt-text="Punto di interruzione raggiunto in Visual Studio Code":::

   Un'[esperienza di debug completa è disponibile in Visual Studio Code](https://code.visualstudio.com/Docs/editor/debugging) per l'API.

1. Premere il pulsante **Continua** nella barra di debug per continuare l'esecuzione.

    :::image type="content" source="media/local-development/continue-button.png" alt-text="Pulsante Continua in Visual Studio Code":::

### <a name="calling-the-api-from-the-application"></a>Chiamata dell'API dall'applicazione

Quando distribuito, il servizio App Web statiche di Azure esegue automaticamente il mapping delle richieste agli endpoint nella cartella _api_. Questo mapping garantisce che le richieste dall'applicazione all'API abbiano un aspetto simile all'esempio seguente.

```javascript
let response = await fetch("/api/message");
```

A seconda del fatto che l'applicazione venga creata con un'interfaccia della riga di comando del framework JavaScript o meno, esistono due modi per configurare il percorso della route `api` durante l'esecuzione dell'applicazione in locale.

- File di configurazione dell'ambiente (scelta consigliata per framework e librerie JavaScript)
- Proxy locale

### <a name="environment-configuration-files"></a>File di configurazione dell'ambiente

Se si crea l'app con framework front-end che hanno un'interfaccia della riga di comando, è necessario usare i file di configurazione dell'ambiente. Ogni framework o libreria prevede un modo diverso per gestire i file di configurazione dell'ambiente. In genere si usa un file di configurazione per lo sviluppo quando l'applicazione è in esecuzione in locale e uno per la produzione quando l'applicazione è in esecuzione nell'ambiente di produzione. L'interfaccia della riga di comando per il framework JavaScript o il generatore di siti statici in uso determinerà automaticamente se usare il file di sviluppo in locale e il file di produzione quando l'app viene creata da App Web statiche di Azure.

Nel file di configurazione di sviluppo è possibile specificare il percorso dell'API, che punta al percorso locale `http:127.0.0.1:7071` in cui l'API per il sito è in esecuzione localmente.

```
API=http:127.0.0.1:7071/api
```

Nel file di configurazione di produzione specificare il percorso dell'API come `api`. In questo modo l'applicazione chiamerà l'API tramite "yoursite.com/api" durante l'esecuzione nell'ambiente di produzione.

```
API=api
```

È possibile fare riferimento a questi valori di configurazione come variabili di ambiente del nodo nel codice JavaScript dell'app Web.

```js
let response = await fetch(`${process.env.API}/message`);
```

Quando si usa l'interfaccia della riga di comando per eseguire il sito in modalità di sviluppo o per compilare il sito per la produzione, il valore `process.env.API` viene sostituito con il valore del file di configurazione appropriato.

Per altre informazioni sulla configurazione dei file di ambiente per le librerie e i framework front-end JavaScript, vedere questi articoli:

- [Variabili di ambiente Angular](https://angular.io/guide/build#configuring-application-environments)
- [React - Aggiunta di variabili di ambiente personalizzate](https://create-react-app.dev/docs/adding-custom-environment-variables/)
- [Vue - Modalità e variabili di ambiente](https://cli.vuejs.org/guide/mode-and-env.html)

[!INCLUDE [static-web-apps-local-proxy](../../includes/static-web-apps-local-proxy.md)]

##### <a name="restart-live-server"></a>Riavviare Live Server

1. Premere **F1** per aprire il riquadro comandi in Visual Studio Code.

1. Digitare **Live Server** e selezionare **Live Server: Stop Live Server** (Arresta Live Server).

    :::image type="content" source="media/local-development/stop-live-server.png" alt-text="Comando Arresta Live Server nel riquadro comandi di Visual Studio":::

1. Premere **F1** per aprire il riquadro comandi.

1. Digitare **Live Server** e selezionare **Live Server: Apri con Live Server**.

1. Aggiornare l'applicazione in esecuzione in `http://locahost:3000`. Il browser ora visualizza il messaggio restituito dall'API.

    :::image type="content" source="media/local-development/hello-from-api.png" alt-text="Messaggio Hello dall'API visualizzato nel browser":::

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Configurare le impostazioni dell'app](application-settings.md)
