<properties
	pageTitle="Introduzione alle app Web Node.js nel servizio app di Azure"
	description="Informazioni su come distribuire un'applicazione Node.js in un'app Web nel servizio app di Azure."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="cephalin"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="get-started-article"
	ms.date="07/01/2016"
	ms.author="cephalin"/>

# Introduzione alle app Web Node.js nel servizio app di Azure

[AZURE.INCLUDE [schede](../../includes/app-service-web-get-started-nav-tabs.md)]

Questa esercitazione illustra come creare una semplice applicazione [Node.js] e distribuirla nel [servizio app di Azure] da un ambiente di riga di comando come cmd.exe o bash. Le istruzioni di questa esercitazione possono essere eseguite in tutti i sistemi operativi che possono eseguire Node.js.

<a name="prereq"></a>
## Prerequisiti

- [Node.JS]
- [Bower]
- [Yeoman]
- [Git]
- [Interfaccia della riga di comando di Azure]
- Un account Microsoft Azure. Se non si ha un account, è possibile [iscriversi per ottenere una versione di valutazione gratuita] oppure [attivare i vantaggi della sottoscrizione di Visual Studio].

## Creare e distribuire una semplice app Web Node.js

1. Aprire il terminale della riga di comando scelto e installare il [generatore di Express per Yeoman].

        npm install -g generator-express

2. Eseguire `CD` per passare a una directory di lavoro e generare un'app Express usando la sintassi seguente:

        yo express
        
    Scegliere le opzioni seguenti quando richiesto:

    `? Would you like to create a new directory for your project?` **Yes** `? Enter directory name` **{appname}** `? Select a version to install:` **MVC** `? Select a view engine to use:` **Jade** `? Select a css preprocessor to use (Sass Requires Ruby):` **None** `? Select a database to use:` **None** `? Select a build tool to use:` **Grunt**

3. Eseguire `CD` per passare alla directory radice della nuova app e avviarla per verificare che venga eseguita nell'ambiente di sviluppo:

        npm start

    Nel browser passare a <http://localhost:3000> per verificare che sia possibile visualizzare la home page di Express. Dopo aver verificato la corretta esecuzione dell'app, usare `Ctrl-C` per arrestarla.
    
1. Passare alla modalità ASM e accedere ad Azure (è necessaria l'[interfaccia della riga di comando di Azure](#prereq)):

        azure config mode asm
        azure login

    Seguire le istruzioni per continuare l'accesso in un browser usando un account con la sottoscrizione di Azure.

2. Verificare di essere ancora nella directory radice dell'app e quindi con il comando successivo creare la risorsa dell'app del servizio app con un nome di app univoco. Ad esempio: http://{appname}.azurewebsites.net

        azure site create --git {appname}

    Seguire le istruzioni e selezionare un'area di Azure in cui eseguire la distribuzione. Se non sono mai state configurate credenziali di distribuzione Git/FTP per la sottoscrizione di Azure, verrà chiesto di crearle.

3. Aprire il file ./config/config.js dalla radice dell'applicazione e modificare la porta di produzione in `process.env.port`. La proprietà `production` nell'oggetto `config` dovrebbe essere simile all'esempio seguente:

        production: {
            root: rootPath,
            app: {
                name: 'express1'
            },
            port: process.env.port,
        }

    In questo modo l'app Node.js può rispondere alle richieste Web inviate alla porta predefinita su cui iisnode è in ascolto.
    
4. Aprire ./package.json e aggiungere la proprietà `engines` per [specificare la versione di Node.js desiderata](#version).

        "engines": {
            "node": "6.6.0"
        }, 

4. Salvare le modifiche e usare Git per distribuire l'app in Azure:

        git add .
        git commit -m "{your commit message}"
        git push azure master

    Il generatore di Express fornisce già un file con estensione gitignore, quindi il tentativo di caricare la directory node\_modules/ da parte di `git push` non utilizza larghezza di banda.

5. Avviare infine l'app Azure attiva nel browser:

        azure site browse

    Dovrebbe essere possibile visualizzare l'app Web Node.js in esecuzione nel servizio app di Azure.
    
    ![Esempio di come passare all'applicazione distribuita.][deployed-express-app]

## Aggiornare l'app Web Node.js

Per aggiornare l'app Web Node.js in esecuzione nel servizio app, è sufficiente eseguire `git add`, `git commit` e `git push` come per la distribuzione iniziale dell'app Web.
     
## Distribuzione dell'app Node.js da parte del servizio app

Il servizio app di Azure usa [iisnode] per eseguire le app Node.js. L'interfaccia della riga di comando di Azure e il motore Kudu (distribuzione Git) collaborano per semplificare l'esperienza di sviluppo e distribuzione di app Node.js dalla riga di comando.

- `azure site create --git` riconosce il modello Node.js comune di server.js o app.js e crea nella directory radice un file iisnode.yml, che può essere usato per personalizzare iisnode.
- In `git push azure master`, Kudu automatizza le attività di distribuzione seguenti:

    - Se il file package.json si trova nella radice del repository, viene eseguito `npm install --production`.
    - Generare un file Web.config per iisnode che punta allo script di avvio in package.json, ad esempio server.js o app.js.
    - Personalizzare Web.config per preparare l'app per il debug con Node-Inspector.
    
## Usare un framework di Node.js

Se si usa un framework Node.js comune, ad esempio [Sails.js][SAILSJS] o [MEAN.js][MEANJS], per sviluppare app, queste potranno essere distribuite nel servizio app. I framework di Node.js più diffusi hanno caratteristiche non standard specifiche e le relative dipendenze dei pacchetti vengono aggiornate continuamente. Tuttavia, nel servizio app sono disponibili i log stdout e stderr che permettono di capire esattamente cosa sta facendo l'app e apportare le modifiche necessarie. Per altre informazioni, vedere [Ottenere log stdout e stderr da iisnode](#iisnodelog).

Le esercitazioni seguenti mostrano come usare un framework specifico nel servizio app:

- [Distribuire un'app Web Sails.js nel servizio app di Azure]
- [Creazione di un'applicazione di chat Node.js con Socket.IO in Servizio app di Azure]
- [Come utilizzare io.js con Azure applicazione servizio Web App]

<a name="version"></a>
## Usare un motore Node.js specifico

In un normale flusso di lavoro è possibile fare in modo che il servizio app usi un motore Node.js specifico, come accade in package.json. Ad esempio:

    "engines": {
        "node": "6.6.0"
    }, 

Il motore di distribuzione Kudu determina quale motore Node.js usare nell'ordine seguente:

- Prima di tutto, viene verificato se in iisnode.yml è specificato `nodeProcessCommandLine`. Se viene specificato, è possibile usarlo.
- Successivamente, viene verificato se in package.json è specificato `"node": "..."` nell'oggetto `engines`. Se viene specificato, è possibile usarlo.
- Scegliere una versione predefinita di Node.js per impostazione predefinita.

>[AZURE.NOTE] È consigliabile definire in modo esplicito il motore Node.js desiderato. La versione di Node.js predefinita può cambiare ed è possibile che vengano visualizzati errori nell'app Web di Azure perché la versione di Node.js predefinita non è appropriata per l'app.

<a name="iisnodelog"></a>
## Ottenere log stdout e stderr da iisnode

Per leggere i log di iisnode, seguire questa procedura.

> [AZURE.NOTE] Al termine della procedura, potrebbero non essere presenti file di log finché non si verifica un errore.

1. Aprire il file iisnode.yml indicato nell'interfaccia della riga di comando di Azure.

2. Impostare i due parametri seguenti:

        loggingEnabled: true
        logDirectory: iisnode
    
    Questi due parametri indicano a iisnode nel servizio app di inserire l'output di stdout e stderr nella directory D:\\home\\site\\wwwroot**iisnode**.

3. Salvare le modifiche ed eseguirne il push in Azure con i comandi Git seguenti:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
    Ora iisnode è configurato. I passaggi successivi illustrano come accedere a tali log.
     
4. Nel browser, accedere alla console di debug di Kudu per l'app, che si trova in:

        https://{appname}.scm.azurewebsites.net/DebugConsole 

    Questo URL si differenzia dall'URL dell'app Web per l'aggiunta di "*.scm.*" al nome DNS. Se si omette tale aggiunta all'URL, viene restituito un errore 404.

5. Passare a D:\\home\\site\\wwwroot\\iisnode

    ![Passaggio alla posizione dei file di log di iisnode.][iislog-kudu-console-find]

6. Fare clic sull'icona di **modifica** del log che si vuole leggere. È anche possibile fare clic sull'icona di **download** o di **eliminazione**.

    ![Apertura di un file di log di iisnode.][iislog-kudu-console-open]

    Ora è possibile visualizzare il log per eseguire il debug della distribuzione del servizio app.
    
    ![Esame di un file di log di iisnode.][iislog-kudu-console-read]

## Eseguire il debug dell'app con Node-Inspector

Se si usa Node-Inspector per eseguire il debug delle app Node.js, è possibile usarlo anche per l'app del servizio app attiva. Node-Inspector è preinstallato nell'installazione di iisnode per il servizio app. Se si esegue la distribuzione tramite Git, il file Web.config generato automaticamente da Kudu contiene già tutta la configurazione necessaria per abilitare Node-Inspector.

Per abilitare Node-Inspector, seguire questa procedura:

1. Aprire iisnode.yml nella radice del repository e specificare i parametri seguenti:

        debuggingEnabled: true
        debuggerExtensionDll: iisnode-inspector.dll

3. Salvare le modifiche ed eseguirne il push in Azure con i comandi Git seguenti:

        git add .
        git commit -m "{your commit message}"
        git push azure master
   
4. A questo punto è sufficiente passare al file di avvio dell'app specificato dallo script di avvio in package.json, aggiungendo /debug all'URL. Ad esempio,

        http://{appname}.azurewebsites.net/server.js/debug
    
    Oppure
    
        http://{appname}.azurewebsites.net/app.js/debug

## Altre risorse

- [Specifica di una versione di Node.js in un'applicazione Azure](../nodejs-specify-node-version-azure-apps.md)
- [Procedure consigliate e guida alla risoluzione dei problemi per le applicazioni Node in App Web di Azure](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md)
- [Come eseguire il debug di un'app Web Node.js nel servizio app di Azure](web-sites-nodejs-debug.md)
- [Utilizzo di moduli Node.js con le applicazioni Azure](../nodejs-use-node-modules-azure-apps.md)
- [Blog sulle app Web del servizio app di Azure: Node.js](http://blogs.msdn.com/b/silverlining/archive/2012/06/14/windows-azure-websites-node-js.aspx)
- [Centro per sviluppatori di Node. js](/develop/nodejs/)
- [Introduzione alle app Web in Servizio app di Azure](app-service-web-get-started.md)
- [Exploring the Super Secret Kudu Debug Console (Esplorazione della console segreta di debug di Kudu)]

<!-- URL List -->

[Interfaccia della riga di comando di Azure]: ../xplat-cli-install.md
[servizio app di Azure]: ../app-service/app-service-value-prop-what-is.md
[attivare i vantaggi della sottoscrizione di Visual Studio]: http://go.microsoft.com/fwlink/?LinkId=623901
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

<!---HONumber=AcomDC_0928_2016-->