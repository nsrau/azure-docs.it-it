---
title: Creare uno spazio di sviluppo Kubernetes nel cloud | Microsoft Docs
titleSuffix: Azure Dev Spaces
author: zr-msft
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
ms.author: zarhoads
ms.date: 09/26/2018
ms.topic: quickstart
description: Sviluppo rapido Kubernetes con contenitori e microservizi in Azure
keywords: Docker, Kubernetes, Azure, servizio Azure Kubernetes, contenitori
ms.openlocfilehash: 52cca29392f226aa0f33081b9303a8c5d1f41036
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413337"
---
# <a name="quickstart-create-a-kubernetes-dev-space-with-azure-dev-spaces-nodejs"></a>Guida introduttiva: Creare uno spazio di sviluppo Kubernetes con Azure Dev Spaces (Node.js)

In questa guida si apprenderà come:

- Configurare Azure Dev Spaces con un cluster Kubernetes gestito in Azure.
- Sviluppare codice in modo iterativo nei contenitori con VS Code e la riga di comando.
- Eseguire il debug del codice in esecuzione nel cluster.

> [!Note]
> **In caso di problemi** in qualsiasi momento, vedere la sezione [Risoluzione dei problemi](troubleshooting.md) o inserire un commento in questa pagina. È anche possibile provare l'[esercitazione](get-started-nodejs.md) più dettagliata.

## <a name="prerequisites"></a>Prerequisiti

- Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
- [Visual Studio Code](https://code.visualstudio.com/download).
- [Interfaccia della riga di comando di Azure](/cli/azure/install-azure-cli?view=azure-cli-latest) versione 2.0.43 o successiva.
- Un cluster Kubernetes che esegue Kubernetes 1.9.6 o versione successiva nell'area EastUS, EastUS2, CentralUS, WestUS2, WestEurope, SoutheastAsia, CanadaCentral o CanadaEast con **Routing di applicazioni HTTP** abilitato.

    ```cmd
    az group create --name MyResourceGroup --location <region>
    az aks create -g MyResourceGroup -n myAKS --location <region> --kubernetes-version 1.10.9 --enable-addons http_application_routing --generate-ssh-keys
    ```

## <a name="set-up-azure-dev-spaces"></a>Configurare Azure Dev Spaces

L'interfaccia della riga di comando di Azure e l'estensione Azure Dev Spaces possono essere installate ed eseguite nei computer Windows, Mac o Linux. Per Linux, sono supportate le distribuzioni seguenti: Ubuntu (18.04, 16.04, and 14.04), Debian 8 and 9, RHEL 7, Fedora 26+, CentOS 7, openSUSE 42.2, and SLES 12.

Seguire questa procedura per configurare Azure Dev Spaces:

1. Configurare Dev Spaces nel cluster AKS: `az aks use-dev-spaces -g MyResourceGroup -n MyAKS`
1. Scaricare l'[estensione Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds) per Visual Studio Code. Fare clic su Installa nella pagina del Marketplace dell'estensione e di nuovo in VS Code.

## <a name="build-and-run-code-in-kubernetes"></a>Compilare ed eseguire codice in Kubernetes

1. Scaricare il codice di esempio da GitHub: [https://github.com/Azure/dev-spaces](https://github.com/Azure/dev-spaces) 
1. Passare alla cartella webfrontend: `cd dev-spaces/samples/nodejs/getting-started/webfrontend`
1. Generare gli asset dei chart Docker e Helm: `azds prep --public`
1. Compilare ed eseguire il codice in AKS. Dalla **cartella webfrontend** nella finestra del terminale eseguire questo comando: `azds up`
1. Analizzare l'output della console per informazioni sull'URL che è stato creato con il comando `up`. Sarà nel formato: 

   ```output
   (pending registration) Service 'webfrontend' port 'http' will be available at <url>
   Service 'webfrontend' port 80 (TCP) is available at http://localhost:<port>
   ```

   Aprire l'URL in una finestra del browser; si dovrebbe visualizzare il caricamento dell'applicazione Web. Durante l'esecuzione del contenitore, l'output `stdout` e `stderr` vengono trasmessi nella finestra del terminale.
   
   > [!Note]
   > Alla prima esecuzione, possono essere necessari alcuni minuti prima che il DNS pubblico sia pronto. Se l'URL pubblico non viene risolto, è possibile usare l'URL http://localhost:<portnumber> alternativo visualizzato nell'output della console. Se si usa l'URL localhost, potrebbe sembrare che il contenitore sia in esecuzione in locale, ma in realtà viene eseguito in AKS. Per motivi di praticità e per semplificare l'interazione con il servizio nel computer locale, Azure Dev Spaces crea un tunnel SSH temporaneo al contenitore in esecuzione in Azure. È possibile tornare in seguito per tentare di usare l'URL pubblico quando il record DNS sarà pronto.

### <a name="update-a-content-file"></a>Aggiornare un file di contenuto
Azure Dev Spaces consente non solo di eseguire codice in Kubernetes, ma anche di visualizzare in modo rapido e iterativo l'applicazione delle modifiche apportate al codice in un ambiente Kubernetes nel cloud.

1. Individuare il file `./public/index.html` e apportare una modifica al codice HTML. Ad esempio, modificare il colore di sfondo della pagina impostandolo su una sfumatura di blu:

    ```html
    <body style="background-color: #95B9C7; margin-left:10px; margin-right:10px;">
    ```

1. Salvare il file. Poco dopo, nella finestra del terminale verrà visualizzato un messaggio che informa che un file nel contenitore in esecuzione è stato aggiornato.
1. Passare al browser e aggiornare la pagina. Verrà visualizzato l'aggiornamento del colore.

Che cosa è successo? Le modifiche ai file di contenuto, come HTML e CSS, non richiedono il riavvio del processo Node.js, di conseguenza un comando `azds up` attivo sincronizzerà automaticamente qualsiasi file di contenuto modificato direttamente nel contenitore in esecuzione in Azure, offrendo così un modo rapido per visualizzare le modifiche apportate al contenuto.

### <a name="test-from-a-mobile-device"></a>Eseguire test da un dispositivo mobile
Aprire l'app Web in un dispositivo mobile usando l'URL pubblico per webfrontend. È possibile copiare e inviare l'URL dal desktop al dispositivo, per evitare l'immissione dell'indirizzo lungo. Se l'app Web viene caricata in un dispositivo mobile di piccole dimensioni, si noterà che l'interfaccia utente non viene visualizzata correttamente.

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

In questa sezione si userà VS Code per eseguire direttamente il debug del contenitore in esecuzione in Azure. Si apprenderà anche come eseguire più rapidamente un ciclo modifica-esecuzione-test.

![](./media/common/edit-refresh-see.png)

### <a name="initialize-debug-assets-with-the-vs-code-extension"></a>Inizializzare gli asset di debug con l'estensione VS Code
Per prima cosa è necessario configurare il progetto di codice in modo che Visual Studio Code comunichi con lo spazio di sviluppo in Azure. L'estensione Visual Studio Code per Azure Dev Spaces offre un comando helper per l'impostazione della configurazione di debug. 

Aprire il **riquadro comandi** (usando il menu **Visualizza | Riquadro comandi**) e usare il completamento automatico per digitare e selezionare questo comando: `Azure Dev Spaces: Prepare configuration files for Azure Dev Spaces`.

In questo modo viene aggiunta la configurazione di debug per Azure Dev Spaces nella cartella `.vscode`. Questo comando non deve essere confuso con il comando `azds prep` che configura il progetto per la distribuzione.

![](./media/common/command-palette.png)

### <a name="select-the-azds-debug-configuration"></a>Selezionare la configurazione di debug di AZDS
1. Per aprire la visualizzazione Debug, fare clic sull'icona Debug nella **barra attività** sul lato di VS Code.
1. Selezionare **Launch Program (AZDS)** (Avvia programma - AZDS) come configurazione di debug attiva.

![](media/get-started-node/debug-configuration-nodejs2.png)

> [!Note]
> Se nel riquadro comandi non vengono visualizzati comandi di Azure Dev Spaces, assicurarsi di aver installato l'estensione di VS Code per Azure Dev Spaces.

### <a name="debug-the-container-in-kubernetes"></a>Eseguire il debug del contenitore in Kubernetes
Premere **F5** per eseguire il debug del codice in Kubernetes.

Analogamente a quanto avviene con il comando `up`, all'avvio del debug il codice viene sincronizzato con lo spazio di sviluppo e viene compilato e distribuito un contenitore in Kubernetes. In questo caso, il debugger è associato al contenitore remoto.

> [!Tip]
> La barra di stato di VS Code visualizza un URL selezionabile.

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

Il progetto di esempio `webfrontend` è stato configurato per usare [nodemon](https://nodemon.io/), un noto strumento che accelera lo sviluppo di Node.js ed è completamente compatibile con Azure Dev Spaces.

Attenersi alla procedura seguente:
1. Arrestare il debugger di VS Code.
1. Fare clic sull'icona Debug nella **barra attività** sul lato di VS Code. 
1. Selezionare **Attach (AZDS)** (Associa - AZDS) come configurazione di debug attiva.
1. Premere F5.

In questa configurazione, il contenitore è configurato per avviare *nodemon*. Quando vengono apportate modifiche al codice del server, *nodemon* riavvia automaticamente il processo Node esattamente come in caso di sviluppo in locale. 
1. Modificare di nuovo il messaggio Hello in `server.js` e salvare il file.
1. Aggiornare il browser oppure fare clic sul pulsante *Say It Again* (Ripeti) per visualizzare l'applicazione delle modifiche.

**È ora possibile usare questo metodo per eseguire rapidamente l'iterazione e il debug di codice direttamente in Kubernetes.**

## <a name="next-steps"></a>Passaggi successivi

> [!div class="nextstepaction"]
> [Uso di più contenitori e sviluppo in team](team-development-nodejs.md)
