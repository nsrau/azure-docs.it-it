---
title: Connettere il computer di sviluppo a un cluster AKS (anteprima)Connect your development computer to an AKS cluster (preview)
services: azure-dev-spaces
ms.date: 11/04/2019
ms.topic: conceptual
description: Informazioni su come connettere il computer di sviluppo a un cluster AKS con Azure Dev Spaces
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Servizio Azure Kubernetes, contenitori
ms.openlocfilehash: 772f221a8047a71902f36fa98ded6c24b5e02d27
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235013"
---
# <a name="connect-your-development-computer-to-an-aks-cluster-preview"></a>Connettere il computer di sviluppo a un cluster AKS (anteprima)Connect your development computer to an AKS cluster (preview)

Azure Dev Spaces consente di eseguire ed eseguire il debug di codice con o senza un contenitore nel computer di sviluppo, ancora connesso al cluster Kubernetes con il resto dell'applicazione o dei servizi. La connessione del computer di sviluppo al cluster consente di sviluppare rapidamente l'applicazione ed eseguire test end-to-end senza dover creare alcuna configurazione Docker o Kubernetes. È anche possibile connettersi al cluster AKS senza influire su altri carichi di lavoro o sugli utenti che potrebbero usare lo stesso cluster.

Azure Dev Spaces reindirizza il traffico tra il cluster AKS connesso e il computer di sviluppo. Questo reindirizzamento del traffico consente al codice nel computer di sviluppo e ai servizi in esecuzione nel cluster AKS di comunicare come se si trovassero nello stesso cluster AKS. Poiché il codice è in esecuzione nel computer di sviluppo, si ha anche la flessibilità negli strumenti di sviluppo utilizzati per eseguire ed eseguire il debug di tale codice. Azure Dev Spaces offre anche un modo per replicare le variabili di ambiente e i file montati disponibili per i pod nel cluster AKS nel computer di sviluppo.

In questa guida si apprenderà come:

* Configurare Azure Dev Spaces in un cluster Kubernetes gestito in Azure.
* Distribuire un'applicazione di grandi dimensioni con più microservizi in uno spazio di sviluppo.
* Usare Spazi di sviluppo di Azure per reindirizzare il traffico tra il cluster AKS e il codice in esecuzione nel computer di sviluppo.

> [!IMPORTANT]
> Questa funzionalità è attualmente in anteprima. Le anteprime vengono rese disponibili a condizione che l'utente accetti le [condizioni supplementari per l'utilizzo](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Alcuni aspetti di questa funzionalità potrebbero subire modifiche prima della disponibilità a livello generale.

## <a name="before-you-begin"></a>Prima di iniziare

Questa guida usa [l'applicazione di esempio Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) per illustrare la connessione del computer di sviluppo a un cluster AKS. Seguire le istruzioni nell'applicazione di [esempio README](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/README.md) di Azure Dev Spaces Bike Sharing per eseguire l'applicazione di esempio. In alternativa, se si dispone di una propria applicazione in un cluster AKS è comunque possibile seguire i passaggi riportati di seguito e utilizzare i nomi dei propri servizi e pod.

### <a name="limitations"></a>Limitazioni

* UDP non è supportato in questo momento.

### <a name="prerequisites"></a>Prerequisiti

* Una sottoscrizione di Azure. Se non si ha una sottoscrizione di Azure, è possibile creare un [account gratuito](https://azure.microsoft.com/free).
* [L'interfaccia della riga di comando di Azure installata][azure-cli].
* [Codice di Visual Studio][vs-code] con l'estensione [Azure Dev Spaces][azds-vs-code] installata e in esecuzione in MacOS o Windows 10.
* [L'applicazione di esempio Azure Dev Spaces Bike Sharing](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) o la propria applicazione in esecuzione in un cluster AKS.

## <a name="connect-your-development-computer"></a>Collegare il computer di sviluppo

Aprire *dev-spaces/samples/BikeSharingApp/Bikes* nel codice di Visual Studio e usare l'estensione Azure Dev Spaces per connettere il computer di sviluppo al cluster AKS.

Per usare l'estensione Azure Dev Spaces, aprire la tavolozza dei comandi nel codice di Visual Studio facendo clic su *Visualizza,* quindi su *Tavolozza comandi*. Iniziare `Azure Dev Spaces: Redirect` a digitare `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`e `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`fare clic su , , o .

![Comandi:](../media/how-to-connect/connect-commands.png)

### <a name="select-a-redirection-option"></a>Selezionare un'opzione di reindirizzamento

Se si `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]`esegue , viene chiesto di scegliere un servizio Kubernetes esistente:

![Scegliere Servizio](../media/how-to-connect/connect-choose-service.png)

Questa opzione reindirizza tutto il traffico nel cluster AKS per questo servizio alla versione dell'applicazione in esecuzione nel computer di sviluppo. Se questo servizio dispone di più pod in esecuzione nel cluster AKS, tutto il traffico per questo servizio viene instradato solo al computer di sviluppo. Azure Dev Spaces instrada anche tutto il traffico in uscita dall'applicazione al cluster AKS.

Se eseguite `Azure Dev Spaces: Redirect an existing Kubernetes pod to my machine [Preview]`, vi viene chiesto di scegliere un contenitore specifico:

![Scegli contenitore](../media/how-to-connect/connect-choose-pod.png)

Questa opzione si connette a un contenitore specifico. Questa opzione è utile per interagire con i pod che non inviano o ricevono traffico e replicano i pod terminati. Se il contenitore invia e riceve traffico, questa opzione `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` si comporta in modo simile a e reindirizza tutto il traffico nel cluster AKS per tutti i pod correlati al servizio del pod selezionato.

Se eseguite `Azure Dev Spaces: Redirect a new Kubernetes pod to my machine [Preview]`, non viene richiesto di selezionare un contenitore o un servizio esistente. Questa opzione reindirizza tutto il traffico in uscita dall'applicazione in esecuzione nel computer di sviluppo al cluster AKS.

Per questo esempio, scegliere `Azure Dev Spaces: Redirect an existing Kubernetes service to my machine [Preview]` e selezionare il servizio *biciclette.*

### <a name="select-a-connection-mode"></a>Selezionare una modalità di connessione

Dopo aver selezionato l'opzione di reindirizzamento, viene richiesto di scegliere la modalità di connessione *Sostituisci* o *Clona.*

![Sostituisci o Clona](../media/how-to-connect/connect-replace-clone.png)

L'opzione *Sostituisci* sostituisce il pod o il servizio corrente nel cluster AKS e reindirizza tutto il traffico per tale servizio al computer di sviluppo. Questa opzione può essere dannosa per altri servizi nel cluster AKS che interagiscono con il servizio che si sta reindirizzando potrebbe non funzionare fino a quando non si avvia l'applicazione nel computer di sviluppo. L'opzione *Clona* consente di scegliere uno spazio di sviluppo figlio esistente o di creare un nuovo spazio di sviluppo figlio per reindirizzare il traffico per un pod o un servizio al computer di sviluppo. Questa opzione consente di lavorare in isolamento e non interrompere altri servizi poiché solo il traffico verso tale spazio di sviluppo figlio verrà reindirizzato al computer di sviluppo. L'opzione *Clona* richiede che il cluster AKS abbia spazi di sviluppo di Azure abilitato.

Per questo esempio, scegliere *Sostituisci*.

> [!NOTE]
> Se il pod del servizio esistente dispone di più contenitori, viene richiesto anche di scegliere il contenitore dell'applicazione.

### <a name="select-a-port-for-your-application"></a>Selezionare una porta per l'applicazione

Dopo aver selezionato la modalità di connessione, viene richiesto di immettere la porta TCP dell'applicazione locale. Se l'applicazione apre più porte, separarle con una virgola, ad esempio *80,81*. Se l'applicazione non accetta richieste di rete, immettere *0*. Per questo esempio, immettere *3000*.

![Connect scegliere la porta](../media/how-to-connect/connect-choose-port.png)

### <a name="confirm-you-are-connected"></a>Verificare di essere connessi

Dopo aver selezionato la porta TCP dell'applicazione, Azure Dev Spaces stabilirà una connessione al cluster AKS. Azure Dev Spaces inserisce un agente nel cluster AKS per reindirizzare il traffico tra il cluster AKS e il computer di sviluppo. La creazione di questa connessione potrebbe richiedere alcuni minuti. Azure Dev Spaces richiederà inoltre l'accesso come amministratore per modificare il file *hosts* nel computer di sviluppo.

> [!IMPORTANT]
> Quando Azure Dev Spaces stabilisce una connessione al cluster AKS, gli altri servizi nel cluster AKS potrebbero non funzionare correttamente fino a quando non si avvia il servizio nel computer di sviluppo se si sceglie la modalità di connessione *Sostituisci.* È invece possibile scegliere la modalità di connessione *Clona* per creare uno spazio di sviluppo figlio per il reindirizzamento ed evitare interruzioni dello spazio padre. Inoltre, se il servizio ha una dipendenza che non è disponibile nel computer di sviluppo, potrebbe essere necessario modificare l'applicazione o fornire [una configurazione aggiuntiva](#additional-configuration)

Azure Dev Spaces apre una finestra terminale denominata *Connessione ADS - Bike* dopo aver stabilito una connessione al cluster AKS. Questa finestra del terminale contiene tutte le variabili di ambiente e le voci DNS configurate dal cluster AKS. Qualsiasi codice eseguito in questa finestra del terminale o tramite il debugger del codice di Visual Studio è connesso al cluster AKS.

![Terminale](../media/how-to-connect/connect-terminal.png)

Inoltre, Azure Dev Spaces crea una finestra denominata *Dev Spaces Connect* con tutto il relativo output.

![Output](../media/how-to-connect/connect-output.png)

Spazi di sviluppo di Azure include anche un elemento della barra di stato che mostra lo stato della connessione.

![Stato](../media/how-to-connect/connect-status.png)

Verificare che sulla barra di stato sia visualizzato *Spazi dev: connessi a dev/bike sulla porta locale 3000*.

### <a name="configure-your-application-on-your-development-computer"></a>Configurare l'applicazione nel computer di sviluppo

Aprire la finestra del *terminale A-DS Connect - Bikes* ed eseguire `npm install`:

```console
$ npm install

> fsevents@1.2.9 install ./dev-spaces/samples/BikeSharingApp/Bikes/node_modules/fsevents
> node install
...
```

Fare clic su *Debug,* quindi *su Apri configurazioni*. Se viene richiesto di selezionare un ambiente, scegliere *Node.js*. In questo `.vscode/launch.json` modo viene creato un file. Sostituire il contenuto di tale file con quanto segue:

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

Aprire package.json e aggiungere uno script di debug:Open [package.json](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/package.json) and add a debugging script:

```json
  "devDependencies": {
    "nodemon": "^1.18.10"
  },
  "scripts": {
    "debug": "node --nolazy --inspect-brk=9229 server.js"
  }
```

### <a name="start-your-application-on-your-development-computer"></a>Avviare l'applicazione nel computer di sviluppoStart your application on your development computer

Fare clic sull'icona *Debug* a sinistra e fare clic sul pulsante di avvio accanto a *Avvia tramite NPM* in alto.

![Lancio tramite NPM](../media/how-to-connect/launch-npm.png)

L'applicazione verrà avviata e Azure Dev Spaces reindirizza il traffico tra il cluster AKS e il computer di sviluppo. Nella Console di *debug*verranno visualizzati messaggi simili ai seguenti:

```console
/usr/local/bin/npm run-script debug 
...
Debugger attached.
Collection: bikes
MongoDB connection string: mongodb://databases-mongo
Connected to MongoDB
Listening on port 3000
```

Passare al servizio *bikesharingweb* facendo clic sulla barra di stato Spazi di sviluppo di Azure e scegliendo l'URL pubblico dell'applicazione. È inoltre possibile trovare l'URL pubblico dal `azds list-uris` comando eseguito in precedenza. Se non si usano Spazi di sviluppo di Azure nel cluster, usare l'indirizzo IP o l'URL dell'applicazione per lo spazio dei nomi in uso. Nell'esempio precedente l'URL pubblico per il servizio *bikesharingweb* è `http://dev.bikesharingweb.fedcab0987.eus.azds.io/`. Seleziona *Aurelia Briggs (cliente)* come utente, quindi seleziona una bicicletta da noleggiare.

### <a name="set-a-break-point"></a>Impostare un punto di interruzione

Aprire [server.js](https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/Bikes/server.js#L233) e fare clic da qualche parte sulla riga 233 per posizionare il cursore lì. Impostare un punto di interruzione premendo *F9* o facendo clic su *Debug,* quindi *su Attiva/disattiva punto di interruzione*.

Passare al servizio *bikesharingweb* aprendo l'URL pubblico. Seleziona *Aurelia Briggs (cliente)* come utente, quindi seleziona una bicicletta da noleggiare. Si noti che l'immagine per la bicicletta non viene caricata. Tornare al codice di Visual Studio e osservare la riga 233 è evidenziata. Il punto di interruzione impostato ha sospeso il servizio alla riga 233. Per riprendere il servizio, premere *F5* oppure fare clic su *Debug* e quindi su *Continua*. Torna al browser e verifica di vedere un'immagine segnaposto per la bicicletta.

Rimuovere il punto di interruzione posizionando `server.js` il cursore sulla riga 233 e premendo *F9*.

### <a name="update-your-application"></a>Aggiornare l'applicazione

Modifica `server.js` per rimuovere le linee 232 e 233:

```javascript
    // Hard code image url *FIX ME*
    theBike.imageUrl = "/static/logo.svg";
```

La sezione dovrebbe ora essere simile alla seguente:The section should now look like:

```javascript
    var theBike = result;
    theBike.id = theBike._id;
    delete theBike._id;
```

Salvare le modifiche e fare clic su *Debug,* quindi *su Riavvia debug*. Aggiornare il browser e verificare che non venga più visualizzata un'immagine segnaposto per la bicicletta.

Fare clic su *Debug* e quindi su *Arresta debug* per arrestare il debugger. Fare clic sulla barra di stato Spazi di sviluppo di Azure per disconnettersi dal cluster AKS.

## <a name="additional-configuration"></a>Configurazione aggiuntiva

Gli spazi di sviluppo di Azure possono gestire il traffico di routing e replicare le variabili di ambiente senza alcuna configurazione aggiuntiva. Se è necessario scaricare i file montati nel contenitore del cluster AKS, ad `azds-local.env` esempio un file ConfigMap, è possibile creare un oggetto per scaricarli nel computer di sviluppo.

Ecco un `azds-local.env`esempio :

```
# This downloads the "whitelist" volume from the container,
# saves it to a temporary directory on your development computer,
# and sets the full path to an environment variable called WHITELIST_PATH.

WHITELIST_PATH=${volumes.whitelist}/whitelist

# This downloads a file from the container's 'default-token-<any>' mount directory 
# to /var/run/secrets/kubernetes.io/serviceaccount on your development computer.

KUBERNETES_IN_CLUSTER_CONFIG_OVERWRITE=${volumes.default-token-*|/var/run/secrets/kubernetes.io/serviceaccount}


# This makes the myapp1 service available to your development computer
# regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP1_SERVICE_HOST.

# If the myapp1 service is made available in this way, 
# you can also access it using "myapp1" and "myapp1.svc.cluster.local"
# in addition to the IP in the MYAPP1_SERVICE_HOST environment variable.

MYAPP1_SERVICE_HOST=${services.myapp1}

# This makes the service myapp2 in namespace mynamespace available to your 
# development computer regardless of the AKS cluster you are connected to and
# sets the local IP to an environment variable called MYAPP2_SERVICE_HOST.

MYAPP2_SERVICE_HOST=${services.mynamespace.myapp2}
```

## <a name="using-logging-and-diagnostics"></a>Utilizzo della registrazione e della diagnostica

L'output della registrazione viene scritto nella finestra *Dev Spaces Connect* dopo aver connesso il computer di sviluppo al cluster AKS.

![Output](../media/how-to-connect/connect-output.png)

Fare clic sulla barra di stato Spazi di sviluppo di Azure e scegliere *Mostra informazioni di diagnostica*. Questo comando stampa le variabili di ambiente correnti e gli interi DNS nell'output di registrazione.

![Uscita con diagnostica](../media/how-to-connect/connect-output-diagnostics.png)

Inoltre, è possibile trovare i `Azure Dev Spaces` log di diagnostica nella directory [ *TEMP* del computer][azds-tmp-dir]di sviluppo.

## <a name="next-steps"></a>Passaggi successivi

Informazioni su come usare gli spazi di sviluppo di Azure e le azioni GitHub per testare le modifiche da una richiesta pull direttamente in AKS prima che la richiesta pull venga unita nel ramo principale del repository.

> [!div class="nextstepaction"]
> [Azioni GitHub & servizio Azure Kubernetes][gh-actions]

[azds-tmp-dir]: ../troubleshooting.md#before-you-begin
[azds-vs-code]: https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds
[azure-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[bike-sharing-github]: https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp
[gh-actions]: github-actions.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[team-quickstart]: ../quickstart-team-development.md
[vs-code]: https://code.visualstudio.com/download