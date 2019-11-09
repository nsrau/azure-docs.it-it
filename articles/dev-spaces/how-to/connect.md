---
title: " Connettere il computer di sviluppo a un cluster AKS"
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 11/04/2019
ms.topic: conceptual
description: Informazioni su come connettere il computer di sviluppo a un cluster AKS con Azure Dev Spaces
keywords: Azure Dev Spaces, spazi di sviluppo, Docker, Kubernetes, Azure, AKS, servizio Kubernetes di Azure, contenitori
ms.openlocfilehash: 15e46fba19d92e08a9c87a63f6f01ec6b0836910
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888920"
---
# <a name="connect-your-development-machine-to-an-aks-cluster-preview"></a>Connettere il computer di sviluppo a un cluster AKS (anteprima)

Azure Dev Spaces consente di eseguire ed eseguire il debug del codice con o senza un contenitore nel computer di sviluppo, mentre si è ancora connessi al cluster Kubernetes con il resto dell'applicazione o dei servizi. La connessione del computer di sviluppo al cluster consente di sviluppare rapidamente l'applicazione ed eseguire test end-to-end senza dover creare una configurazione Docker o Kubernetes. È anche possibile connettersi al cluster AKS senza influire su altri carichi di lavoro o utenti che potrebbero usare lo stesso cluster.

Azure Dev Spaces reindirizza il traffico tra il cluster AKS connesso e il computer di sviluppo. Questo reindirizzamento del traffico consente il codice nel computer di sviluppo e i servizi in esecuzione nel cluster AKS per comunicare come se si trovassero nello stesso cluster AKS. Poiché il codice è in esecuzione nel computer di sviluppo, è anche possibile usare gli strumenti di sviluppo usati per eseguire il codice ed eseguirne il debug. Azure Dev Spaces fornisce anche un modo per replicare le variabili di ambiente e i file montati disponibili per i Pod nel cluster AKS nel computer di sviluppo.

In questa guida si apprenderà come:

* Configurare Azure Dev Spaces in un cluster Kubernetes gestito in Azure.
* Distribuire un'applicazione di grandi dimensioni con più microservizi in uno spazio di sviluppo.
* Usare Azure Dev Spaces per reindirizzare il traffico tra il cluster AKS e il codice in esecuzione nel computer di sviluppo.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili per l'utente a condizione che si accettino le [condizioni d'uso aggiuntive](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="before-you-begin"></a>Prima di iniziare

Questa guida usa l' [applicazione di esempio Azure Dev Spaces bike sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) per dimostrare la connessione del computer di sviluppo a un cluster AKS. Per eseguire l'applicazione di esempio, seguire le istruzioni nel [file Leggimi dell'applicazione di esempio Azure Dev Spaces bike sharing](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) . In alternativa, se si dispone di un'applicazione personalizzata in un cluster AKS, è comunque possibile seguire la procedura seguente e usare i nomi dei propri servizi e Pod.

### <a name="limitations"></a>Limitazioni

* Il protocollo UDP non è supportato in questo momento.

### <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
* [L'interfaccia della riga di comando di Azure installata][azure-cli].
* [Visual Studio Code][vs-code] con l'estensione [Azure Dev Spaces][azds-vs-code] installata e in esecuzione in MacOS o Windows 10.
* L' [applicazione di esempio Azure Dev Spaces bike sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) o l'applicazione in esecuzione in un cluster AKS.

## <a name="connect-your-development-machine"></a>Connettere il computer di sviluppo

Aprire *dev-Spaces/Samples/BikeSharingApp/Bikes* in Visual Studio Code e usare l'estensione Azure Dev Spaces per connettere il computer di sviluppo al cluster AKS.

Per usare l'estensione Azure Dev Spaces, aprire il riquadro comandi in Visual Studio Code facendo clic su *Visualizza* e quindi su *tavolozza comandi*. Iniziare a digitare `Azure Dev Spaces: Redirect` e fare clic su `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`o `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`.

![Comandi:](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Selezionare un'opzione di Reindirizzamento

Se si esegue `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`, viene richiesto di scegliere un servizio Kubernetes esistente:

![Scegli servizio](../media/how-to-connect/connect-choose-service.png)

Questa opzione reindirizza tutto il traffico nel cluster AKS per questo servizio alla versione dell'applicazione in esecuzione nel computer di sviluppo. Se il servizio dispone di più pod in esecuzione nel cluster AKS, tutto il traffico per questo servizio viene indirizzato solo al computer di sviluppo. Azure Dev Spaces instrada anche tutto il traffico in uscita dall'applicazione al cluster AKS.

Se si esegue `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`, viene richiesto di scegliere un pod specifico:

![Scegliere Pod](../media/how-to-connect/connect-choose-pod.png)

Questa opzione consente di connettersi a un pod specifico. Questa opzione è utile per interagire con i pod che non inviano o ricevono traffico e la replica di Pod terminati. Se il POD invia e riceve traffico, questa opzione si comporta in modo analogo a `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` e reindirizza tutto il traffico nel cluster AKS per tutti i pod correlati al servizio del Pod selezionato.

Se si esegue `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`, non viene richiesto di selezionare un pod o un servizio esistente. Questa opzione reindirizza tutto il traffico in uscita dall'applicazione in esecuzione nel computer di sviluppo al cluster AKS.

Per questo esempio, scegliere `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` e selezionare il servizio *Bikes* .

### <a name="select-a-connection-mode"></a>Selezionare una modalità di connessione

Dopo aver selezionato l'opzione di reindirizzamento, viene richiesto di scegliere la modalità di connessione *Sostituisci* o *clona* .

![Sostituisci o clona](../media/how-to-connect/connect-replace-clone.png)

L'opzione *Sostituisci* sostituisce il pod o il servizio corrente nel cluster AKS e reindirizza tutto il traffico per tale servizio al computer di sviluppo. Questa opzione può essere problematica per altri servizi nel cluster AKS che interagiscono con il servizio che si sta reindirizzando potrebbe non funzionare fino a quando non si avvia l'applicazione nel computer di sviluppo. L'opzione *Clone* consente di scegliere uno spazio di sviluppo figlio esistente o di creare un nuovo spazio dev figlio per il reindirizzamento del traffico per un pod o un servizio al computer di sviluppo. Questa opzione consente di lavorare in isolamento e non interferire con altri servizi perché solo il traffico verso tale spazio dev figlio verrà reindirizzato al computer di sviluppo. Per l'opzione *Clone* è necessario che il cluster AKS disponga di Azure Dev Spaces abilitata.

Per questo esempio, scegliere *Sostituisci*.

> [!NOTE]
> Se il pod del servizio esistente dispone di più contenitori, viene anche richiesto di scegliere il contenitore dell'applicazione.

### <a name="select-a-port-for-your-application"></a>Selezionare una porta per l'applicazione

Dopo aver selezionato la modalità di connessione, viene richiesto di immettere la porta TCP per l'applicazione locale. Se l'applicazione apre più porte, separarle con una virgola, ad esempio *80, 81*. Se l'applicazione non accetta richieste di rete, immettere *0*. Per questo esempio, immettere *3000*.

![Connetti scegliere la porta](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Confermare la connessione

Dopo aver selezionato la porta TCP dell'applicazione, Azure Dev Spaces stabilirà una connessione al cluster AKS. Azure Dev Spaces inserisce un agente nel cluster AKS per reindirizzare il traffico tra il cluster AKS e il computer di sviluppo. Il tentativo di stabilire questa connessione potrebbe richiedere alcuni minuti. Azure Dev Spaces richiederà anche l'accesso come amministratore per modificare il file *hosts* nel computer di sviluppo.

> [!IMPORTANT]
> Quando Azure Dev Spaces stabilisce una connessione al cluster AKS, gli altri servizi nel cluster AKS potrebbero non funzionare correttamente fino a quando non si avvia il servizio nel computer di sviluppo se si sceglie la modalità *Sostituisci* connessione. È possibile scegliere la modalità di connessione *Clone* per creare uno spazio di sviluppo figlio per il reindirizzamento ed evitare eventuali rotture dello spazio padre. Inoltre, se il servizio presenta una dipendenza che non è disponibile nel computer di sviluppo, potrebbe essere necessario modificare l'applicazione o fornire una [configurazione aggiuntiva](#additional-configuration)

Azure Dev Spaces apre una finestra del terminale denominata *AZDS Connect-Bikes* dopo aver stabilito una connessione al cluster AKS. Questa finestra del terminale contiene tutte le variabili di ambiente e le voci DNS configurate dal cluster AKS. Il codice eseguito in questa finestra del terminale o usando il Visual Studio Code debugger è connesso al cluster AKS.

![Terminal](../media/how-to-connect/connect-terminal.png)

Inoltre, Azure Dev Spaces crea una finestra denominata spazi di sviluppo che si *connettono* con tutti i relativi output.

![Output](../media/how-to-connect/connect-output.png)

Azure Dev Spaces dispone anche di un elemento della barra di stato che mostra lo stato della connessione.

![Stato](../media/how-to-connect/connect-status.png)

Verificare che la barra di stato indichi gli *spazi di sviluppo: connessi a dev/Bikes sulla porta locale 3000*.

### <a name="configure-your-application-on-your-development-machine"></a>Configurare l'applicazione nel computer di sviluppo

Aprire la finestra del terminale *AZDS Connect-Bikes* ed eseguire `npm install`:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```


Fare clic su *debug* e quindi *aprire configurazioni*. Se viene richiesto di selezionare un ambiente, scegliere *node. js*. Verrà creato un file di `.vscode/launch.json`. Sostituire il contenuto del file con il codice seguente:

```json
{
    "configurations": [
        {
            "type": "node",
            "request": "launch",
            "name": "Launch via NPM",
            "runtimeExecutable": "npm",
            "runtimeArgs": [
                "run-script",
                "debug"
            ],
            "port": 9229
        }
    ]
}
```

Aprire [Package. JSON](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) e aggiungere uno script di debug:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-machine"></a>Avviare l'applicazione nel computer di sviluppo

Fare clic sull'icona *debug* a sinistra e fare clic sul pulsante Start accanto a *avvia tramite NPM* nella parte superiore.

![Avvia tramite NPM](../media/how-to-connect/launch-npm.png)

L'applicazione viene avviata e Azure Dev Spaces reindirizza il traffico tra il cluster AKS e il computer di sviluppo. Nella *console di debug*vengono visualizzati messaggi simili ai seguenti:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Passare al servizio *bikesharingweb* facendo clic sulla barra di stato Azure Dev Spaces e scegliendo l'URL pubblico dell'applicazione. È anche possibile trovare l'URL pubblico dal comando `azds list-uris` eseguito in precedenza. Se non si usa Azure Dev Spaces nel cluster, usare l'indirizzo IP o l'URL per l'applicazione per lo spazio dei nomi in uso. Nell'esempio precedente l'URL pubblico per il servizio *bikesharingweb* è `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Selezionare *Aurelia Briggs (Customer)* come utente, quindi selezionare una bicicletta da affittare.

### <a name="set-a-break-point"></a>Imposta un punto di rottura

Aprire [Server. js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) e fare clic in un punto qualsiasi alla riga 233 per posizionare il cursore. Per impostare un punto di interruzione, premere *F9* oppure fare clic su *debug* e quindi su *Imposta/Rimuovi*punto

Passare al servizio *bikesharingweb* aprendo l'URL pubblico. Selezionare *Aurelia Briggs (Customer)* come utente, quindi selezionare una bicicletta da affittare. Si noti che l'immagine per la bicicletta non viene caricata. Tornare a Visual Studio Code e osservare che la riga 233 è evidenziata. Il punto di interruzione impostato ha sospeso il servizio alla riga 233. Per riprendere il servizio, premere *F5* oppure fare clic su *Debug* e quindi su *Continua*. Tornare al browser e verificare che venga visualizzata un'immagine segnaposto per la bicicletta.

Rimuovere il punto di interruzione inserendo il cursore alla riga 233 in `server.js` e premendo *F9*.

### <a name="update-your-application"></a>Aggiornare l'applicazione

Modificare `server.js` per rimuovere le righe 232 e 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

La sezione dovrebbe ora essere simile alla seguente:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Salvare le modifiche e fare clic su *debug* , quindi *riavviare il debug*. Aggiornare il browser e verificare che non sia più visibile un'immagine segnaposto per la bicicletta.

Fare clic su *Debug* e quindi su *Arresta debug* per arrestare il debugger. Fare clic sulla barra di stato Azure Dev Spaces per disconnettersi dal cluster AKS.

## <a name="additional-configuration"></a>Configurazione aggiuntiva

Azure Dev Spaces possibile gestire il routing del traffico e la replica delle variabili di ambiente senza alcuna configurazione aggiuntiva. Se è necessario scaricare i file montati nel contenitore del cluster AKS, ad esempio un file ConfigMap, è possibile creare un `azds-local.env` per scaricare tali file nel computer di sviluppo.

Di seguito è riportato un esempio `azds-local.env`:

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development machine,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development machine.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development machine
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

MYAPP1_SERVICE_HOST=${services.myapp1}

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.
```

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare le azioni Azure Dev Spaces e GitHub per testare le modifiche da una richiesta pull direttamente in AKS prima che la richiesta pull venga unita al ramo principale del repository.

> [!div class="nextstepaction"]
> [Azioni di GitHub & servizio Azure Kubernetes][gh-actions]

[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[supported-regions]: ../about.md#supported-regions-and-configurations
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download