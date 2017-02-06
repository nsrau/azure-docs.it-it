---
title: Sviluppare app Web Node.js per il servizio app di Azure | Documentazione Microsoft
description: Informazioni su come distribuire un&quot;applicazione Node.js in un&quot;app Web nel servizio app di Azure.
services: app-service\web
documentationcenter: nodejs
author: cephalin
manager: wpickett
editor: 
ms.assetid: fb2b90c8-02b6-4700-929b-5de9a35d67cc
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: get-started-article
ms.date: 12/16/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 763e956004f460f2f6d0fa7325a6f6b5fca3fd5a
ms.openlocfilehash: ab16eb45f86f81af719fcd0a4b65f19576f01f01


---
# <a name="develop-nodejs-web-apps-for-azure-app-service"></a>Sviluppare app Web Node.js per il servizio app di Azure
[!INCLUDE [tabs](../../includes/app-service-web-get-started-nav-tabs.md)]

Questa esercitazione illustra come creare una semplice applicazione [Node.js] e distribuirla nel [servizio app di Azure] da un ambiente di riga di comando come cmd.exe o bash. Le istruzioni di questa esercitazione possono essere eseguite in tutti i sistemi operativi che possono eseguire Node.js.

[!INCLUDE [app-service-linux](../../includes/app-service-linux.md)]

<a name="prereq"></a>

## <a name="cli-versions-to-complete-the-task"></a>Versioni dell'interfaccia della riga di comando per completare l'attività

È possibile completare l'attività usando una delle versioni seguenti dell'interfaccia della riga di comando:

- [Interfaccia della riga di comando di Azure 1.0](app-service-web-nodejs-get-started-cli-nodejs.md): l'interfaccia della riga di comando per i modelli di distribuzione classici e di gestione delle risorse
- [Interfaccia della riga di comando di Azure 2.0 (anteprima)](app-service-web-nodejs-get-started.md): l'interfaccia della riga di comando di nuova generazione per il modello di distribuzione di gestione delle risorse

## <a name="prerequisites"></a>Prerequisiti
* [Node.js]
* [Bower]
* [Yeoman]
* [Git]
* [Anteprima dell'interfaccia della riga di comando di Azure 2.0](/cli/azure/install-az-cli2)
* Un account Microsoft Azure. Se non si ha un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita] oppure [attivare i vantaggi per i sottoscrittori di Visual Studio].

> [!NOTE]
> È possibile [provare il servizio app](https://azure.microsoft.com/try/app-service/) senza avere un account Azure. Creare un'app iniziale e provarla per un'ora, senza impegno e senza dover usare la carta di credito.
> 
> 

## <a name="create-and-configure-a-simple-nodejs-app-for-azure"></a>Creare e configurare un'app Node.js semplice per Azure
1. Aprire il terminale della riga di comando scelto e installare il [generatore di Express per Yeoman].
   
        npm install -g generator-express

2. `CD` per passare a una directory di lavoro e generare un'app Express usando la sintassi seguente:
   
        yo express
   
    Scegliere le opzioni seguenti quando richiesto:  
   
    `? Would you like to create a new directory for your project?` **Yes**  
    `? Enter directory name` **{appname}**  
    `? Select a version to install:` **MVC**  
    `? Select a view engine to use:` **Jade**  
    `? Select a css preprocessor to use (Sass Requires Ruby):` **None**  
    `? Select a database to use:` **None**  
    `? Select a build tool to use:` **Grunt**

3. Eseguire `CD` per passare alla directory radice della nuova app e avviarla per verificare che venga eseguita nell'ambiente di sviluppo:
   
        npm start
   
    Nel browser passare a <http://localhost:3000> per verificare che sia possibile visualizzare la home page di Express. Dopo aver verificato la corretta esecuzione dell'app, usare `Ctrl-C` per arrestarla.

6. Aprire il file ./config/config.js dalla radice dell'applicazione e modificare la porta di produzione in `process.env.port`. La proprietà `production` nell'oggetto `config` dovrebbe essere simile all'esempio seguente:
   
        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }
   
    > [!NOTE] 
    > Per impostazione predefinita, il servizio app di Azure esegue le applicazioni Node.js con le variabili di ambiente `production` (`process.env.NODE_ENV="production"`.
    > La configurazione consente quindi alle app Node.js in Azure di rispondere alle richieste Web inviate alla porta predefinita su cui iisnode è in ascolto.
    >
    >

7. Aprire ./package.json e aggiungere la proprietà `engines` per [specificare la versione di Node.js desiderata](#version).
   
        "engines": {
            "node": "6.9.1"
        }, 

8. Salvare le modifiche, quindi inizializzare un repository Git nella radice dell'applicazione ed eseguire il commit del codice:
   
        git add .
        git add -f config
        git commit -m "{your commit message}"

## <a name="deploy-your-nodejs-app-to-azure"></a>Distribuire l'app Node.js in Azure

1. Accedere ad Azure (è necessaria l'[anteprima dell'interfaccia della riga di comando di Azure 2.0](#prereq)):
   
        az login
   
    Seguire le istruzioni per continuare l'accesso in un browser usando un account con la sottoscrizione di Azure.

3. Impostare l'utente di distribuzione per il servizio app. Si distribuirà il codice usando queste credenziali in un secondo momento.
   
        az appservice web deployment user set --user-name <username> --password <password>

3. Creare un nuovo [gruppo di risorse](../azure-resource-manager/resource-group-overview.md). Per questa esercitazione su node.js, non è strettamente necessario conoscerne tutte le caratteristiche e funzioni.

        az group create --location "<location>" --name my-nodejs-app-group

    Per visualizzare i possibili valori utilizzabili per `<location>`, usare il comando `az appservice list-locations` nell'interfaccia della riga di comando.

3. Creare un nuovo [piano di servizio app](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md) "GRATUITO". Per questa esercitazione su node.js, è sufficiente sapere che non sono previsti costi per le app Web in questo piano.

        az appservice plan create --name my-nodejs-appservice-plan --resource-group my-nodejs-app-group --sku FREE

4. Creare una nuova app Web con un nome univoco in `<app_name>`.

        az appservice web create --name <app_name> --resource-group my-nodejs-app-group --plan my-nodejs-appservice-plan

5. Configurare la distribuzione Git locale per la nuova app Web con il comando seguente:

        az appservice web source-control config-local-git --name <app_name> --resource-group my-nodejs-app-group

    Si otterrà un output JSON come questo, a indicare la configurazione del repository Git remoto:

        {
        "url": "https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git"
        }

6. Aggiungere l'URL in JSON come Git remoto per il repository locale (chiamato `azure` per motivi di semplicità).

        git remote add azure https://<deployment_user>@<app_name>.scm.azurewebsites.net/<app_name>.git
   
7. Distribuire il codice di esempio per l'elemento Git remoto `azure`. Quando richiesto, usare le credenziali di distribuzione configurate prima.

        git push azure master
   
    Il generatore di Express fornisce già un file con estensione gitignore, quindi il tentativo di caricare la directory node_modules/ da parte di `git push` non utilizza larghezza di banda.

9. Avviare infine l'app Azure attiva nel browser:
   
        az appservice web browse --name <app_name> --resource-group my-nodejs-app-group
   
    Dovrebbe essere possibile visualizzare l'app Web Node.js in esecuzione nel servizio app di Azure.
   
    ![Esempio di come passare all'applicazione distribuita.][deployed-express-app]

## <a name="update-your-nodejs-web-app"></a>Aggiornare l'app Web Node.js
Per aggiornare l'app Web Node.js in esecuzione nel servizio app, è sufficiente eseguire `git add`, `git commit` e `git push` come per la distribuzione iniziale dell'app Web.

## <a name="how-app-service-deploys-your-nodejs-app"></a>Distribuzione dell'app Node.js da parte del servizio app
Il servizio app di Azure usa [iisnode] per eseguire le app Node.js. L'anteprima dell'interfaccia della riga di comando di Azure 2.0 e il motore Kudu (distribuzione Git) collaborano per semplificare l'esperienza di sviluppo e distribuzione di app Node.js dalla riga di comando. 

* È possibile creare un file iisnode.yml nella directory radice e usarlo per personalizzare le proprietà iisnode. Tutte le impostazioni configurabili sono documentate [qui](https://github.com/tjanczuk/iisnode/blob/master/src/samples/configuration/iisnode.yml).
* In `git push azure master`, Kudu automatizza le attività di distribuzione seguenti:
  
  * Se il file package.json si trova nella radice del repository, viene eseguito `npm install --production`.
  * Generare un file Web.config per iisnode che punta allo script di avvio in package.json, ad esempio server.js o app.js.
  * Personalizzare Web.config per preparare l'app per il debug con Node-Inspector.

## <a name="use-a-nodejs-framework"></a>Usare un framework di Node.js
Se si usa un framework Node.js comune, ad esempio [Sails.js][SAILSJS] o [MEAN.js][MEANJS], per sviluppare app, queste potranno essere distribuite nel servizio app. I framework di Node.js più diffusi hanno caratteristiche non standard specifiche e le relative dipendenze dei pacchetti vengono aggiornate continuamente. Tuttavia, nel servizio app sono disponibili i log stdout e stderr che permettono di capire esattamente cosa sta facendo l'app e apportare le modifiche necessarie. Per altre informazioni, vedere [Ottenere log stdout e stderr da iisnode](#iisnodelog).

Le esercitazioni seguenti mostrano come usare un framework specifico nel servizio app:

* [Distribuire un'app Web Sails.js nel servizio app di Azure]
* [Creazione di un'applicazione di chat Node.js con Socket.IO in Servizio app di Azure]
* [Come utilizzare io.js con Azure applicazione servizio Web App]

<a name="version"></a>

## <a name="use-a-specific-nodejs-engine"></a>Usare un motore Node.js specifico
In un normale flusso di lavoro è possibile fare in modo che il servizio app usi un motore Node.js specifico, come accade in package.json.
Ad esempio:

    "engines": {
        "node": "6.9.1"
    }, 

Il motore di distribuzione Kudu determina quale motore Node.js usare nell'ordine seguente:

* Prima di tutto, viene verificato se in iisnode.yml è specificato `nodeProcessCommandLine`. Se viene specificato, è possibile usarlo.
* Successivamente, viene verificato se in package.json è specificato `"node": "..."` nell'oggetto `engines`. Se viene specificato, è possibile usarlo.
* Scegliere una versione predefinita di Node.js per impostazione predefinita.

Per l'elenco aggiornato di tutte le versioni supportate di Node.js/NPM nel servizio app di Azure, accedere all'URL seguente per l'app:

    https://<app_name>.scm.azurewebsites.net/api/diagnostics/runtime

> [!NOTE]
> È consigliabile definire in modo esplicito il motore Node.js desiderato. La versione di Node.js predefinita può cambiare ed è possibile che vengano visualizzati errori nell'app Web di Azure perché la versione di Node.js predefinita non è appropriata per l'app.
> 
> 

<a name="iisnodelog"></a>

## <a name="get-stdout-and-stderr-logs-from-iisnode"></a>Ottenere log stdout e stderr da iisnode
Per leggere i log di iisnode, seguire questa procedura.

> [!NOTE]
> Al termine della procedura, potrebbero non essere presenti file di log finché non si verifica un errore.
> 
> 

1. Aprire il file iisnode.yml indicato nell'anteprima dell'interfaccia della riga di comando di Azure 2.0.
2. Impostare i due parametri seguenti: 
   
        loggingEnabled: true
        logDirectory: iisnode
   
    Questi due parametri indicano a iisnode nel servizio app di inserire l'output di stdout e stderr nella directory D:\home\site\\**wwwrootiisnode*.
3. Salvare le modifiche ed eseguirne il push in Azure con i comandi Git seguenti:
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    Ora iisnode è configurato. I passaggi successivi illustrano come accedere a tali log.
4. Nel browser, accedere alla console di debug di Kudu per l'app, che si trova in:
   
        https://{appname}.scm.azurewebsites.net/DebugConsole 
   
    Questo URL si differenzia dall'URL dell'app Web per l'aggiunta di "*.scm.*" al nome DNS. Se si omette tale aggiunta all'URL, viene restituito un errore 404.
5. Passare a D:\home\site\wwwroot\iisnode
   
    ![Passaggio alla posizione dei file di log di iisnode.][iislog-kudu-console-find]
6. Fare clic sull'icona di **modifica** del log che si vuole leggere. È anche possibile fare clic sull'icona di **download** o di **eliminazione**.
   
    ![Apertura di un file di log di iisnode.][iislog-kudu-console-open]
   
    Ora è possibile visualizzare il log per eseguire il debug della distribuzione del servizio app.
   
    ![Esame di un file di log di iisnode.][iislog-kudu-console-read]

## <a name="debug-your-app-with-node-inspector"></a>Eseguire il debug dell'app con Node-Inspector
Se si usa Node-Inspector per eseguire il debug delle app Node.js, è possibile usarlo anche per l'app del servizio app attiva. Node-Inspector è preinstallato nell'installazione di iisnode per il servizio app. Se si esegue la distribuzione tramite Git, il file Web.config generato automaticamente da Kudu contiene già tutta la configurazione necessaria per abilitare Node-Inspector.

Per abilitare Node-Inspector, seguire questa procedura:

1. Aprire iisnode.yml nella radice del repository e specificare i parametri seguenti: 
   
        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll
2. Salvare le modifiche ed eseguirne il push in Azure con i comandi Git seguenti:
   
        git add .
        git commit -m "{your commit message}"
        git push azure master
3. A questo punto è sufficiente passare al file di avvio dell'app specificato dallo script di avvio in package.json, aggiungendo /debug all'URL. Ad esempio,
   
        http://{appname}.azurewebsites.net/server.js/debug
   
    Oppure
   
        http://{appname}.azurewebsites.net/app.js/debug

## <a name="more-resources"></a>Altre risorse
* [Specifica di una versione di Node.js in un'applicazione Azure](../nodejs-specify-node-version-azure-apps.md)
* [Procedure consigliate e guida alla risoluzione dei problemi per le applicazioni Node in App Web di Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
* [Come eseguire il debug di un'app Web Node.js nel servizio app di Azure](web-sites-nodejs-debug.md)
* [Utilizzo di moduli Node.js con le applicazioni Azure](../nodejs-use-node-modules-azure-apps.md)
* [Blog sulle app Web del servizio app di Azure: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
* [Centro per sviluppatori di Node. js](/develop/nodejs/)
* [Introduzione alle app Web in Servizio app di Azure](app-service-web-get-started.md)
* [Exploring the Super Secret Kudu Debug Console (Esplorazione della console segreta di debug di Kudu)]

<!-- URL List -->

[servizio app di Azure]: ../app-service/app-service-value-prop-what-is.md
[attivare i vantaggi per i sottoscrittori di Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
[Bower]: http://bower.io/
[Creazione di un'applicazione di chat Node.js con Socket.IO in Servizio app di Azure]: ./web-sites-nodejs-chat-app-socketio.md
[Distribuire un'app Web Sails.js nel servizio app di Azure]: ./app-service-web-nodejs-sails.md
[Exploring the Super Secret Kudu Debug Console (Esplorazione della console segreta di debug di Kudu)]: /documentation/videos/super-secret-kudu-debug-console-for-azure-web-sites/
[generatore di Express per Yeoman]: https://github.com/petecoop/generator-express
[Git]: http://www.git-scm.com/downloads
[Come utilizzare io.js con Azure applicazione servizio Web App]: ./web-sites-nodejs-iojs.md
[iisnode]: https://github.com/tjanczuk/iisnode/wiki
[MEANJS]: http://meanjs.org/
[Node.js]: http://nodejs.org
[SAILSJS]: http://sailsjs.org/
[iscriversi per ottenere una versione di valutazione gratuita]: http://go.microsoft.com/fwlink/?LinkId=623901
[web app]: ./app-service-web-overview.md
[Yeoman]: http://yeoman.io/

<!-- IMG List -->

[deployed-express-app]: ./media/app-service-web-nodejs-get-started/deployed-express-app.png
[iislog-kudu-console-find]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-navigate.png
[iislog-kudu-console-open]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-open.png
[iislog-kudu-console-read]: ./media/app-service-web-nodejs-get-started/iislog-kudu-console-read.png



<!--HONumber=Feb17_HO1-->


