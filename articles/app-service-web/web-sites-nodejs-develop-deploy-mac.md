<properties
	pageTitle="Creare un'app Web Node.js in Azure App Service | Microsoft Azure"
	description="Informazioni sulla creazione e distribuzione di un'app Web Node.js in Azure."
	services="app-service\web"
	documentationCenter="nodejs"
	authors="MikeWasson"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="nodejs"
	ms.topic="hero-article"
	ms.date="08/18/2015"
	ms.author="mwasson"/>

# Creare e distribuire un'app Web Node.js nel servizio app di Azure

Questa esercitazione illustra come creare un'applicazione [Node][nodejs.org] e come distribuirla nella [funzionalità App Web in Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) tramite [Git]. Le istruzioni di questa esercitazione possono essere eseguite in qualsiasi sistema operativo in grado di eseguire Node.

Di seguito è riportata una schermata dell'applicazione completata:

![Finestra del browser con il messaggio 'Hello World' visualizzato.][helloworld-completed]

##Creare un'app Web di Azure e abilitare la pubblicazione Git

Seguire questa procedura per creare un'app Web e abilitare la pubblicazione Git.

> [AZURE.NOTE]
> Per completare l'esercitazione, è necessario un account Microsoft Azure. Se non si dispone di un account, è possibile [attivare i benefici della sottoscrizione MSDN](/it-it/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F) oppure [iscriversi per ottenere una versione di valutazione gratuita](/it-it/pricing/free-trial/?WT.mc_id=A261C142F).

1. Accedere al [portale di Azure](https://portal.azure.com).

2. Fare clic sull'icona **+ NUOVO** nella parte superiore sinistra del portale.

3. Fare clic su **Web e dispositivi mobili** e quindi su **app Web**.

    ![][portal-quick-create]

4. Immettere un valore per **URL**.

5. Selezionare un piano di Servizi app da usare o crearne uno nuovo. Se si crea un nuovo piano, selezionare i piano tariffario, la posizione e altre opzioni.

    ![][portal-quick-create2]

6. Fare clic su **Crea**.

7. Quando lo stato cambia in **In esecuzione**, il portale apre automaticamente il pannello per l'app Web. È anche possibile accedere al pannello facendo clic su **Sfoglia**.

	![][go-to-dashboard]

8. Fare clic su **Distribuzione**. (potrebbe essere necessario scorrere lo schermo per visualizzare questa parte del pannello).

	![][deployment-part]

9. Fare clic su **Scegli origine**, quindi su **Repository Git locale** e infine su **OK**.

	![][setup-git-publishing]


10. Fare clic sulla parte relativa alle **credenziali di distribuzione** (evidenziata di seguito in rosso). Creare un nome utente e una password. Fare clic su **Salva**. Se è stata precedentemente abilitata la pubblicazione per un'app Web, non è necessario eseguire questo passaggio.

	![][deployment-credentials]


11. Per pubblicare occorre effettuare il push a un repository Git remoto. Individuare l'URL per il repository, fare clic su **Tutte le impostazioni** e quindi selezionare **Proprietà**. L'URL viene riportato sotto **URL GIT**.

	![][git-url]

##Creazione e test dell'applicazione in locale

In questa sezione verrà creato un file **server.js** contenente l'esempio 'Hello World' di [nodejs.org]. L'esempio è stato modificato rispetto all'originale con l'aggiunta di process.env.PORT come porta in cui rimanere in ascolto durante l'esecuzione in un'app Web di Azure.

1. Usare un editor di testo per creare un nuovo fine denominato **server.js** nella directory **helloworld**. Se la directory **helloworld** non esiste, crearla.

2. Aggiungere il codice seguente come contenuto del file **server.js**, quindi salvarlo:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Aprire la riga di comando e usare il comando seguente per avviare l'app Web in locale:

        node server.js

4. Aprire un Web browser e passare all'indirizzo http://localhost:1337. Verrà visualizzata una pagina Web con il messaggio "Hello World", come illustrato nella schermata seguente:

    ![Finestra del browser con il messaggio 'Hello World' visualizzato.][helloworld-localhost]

##Pubblicare l'applicazione

1. Dalla riga di comando passare alla directory **helloworld** e immettere il comando seguente per inizializzare un archivio Git locale.

		git init

	> [AZURE.NOTE]**Comando Git non disponibile?** 
	> [Git](http://git-scm.com/%20target="_blank) è un sistema di controllo delle versioni distribuite che è possibile usare per distribuire il sito Web di Azure. Per le istruzioni sull'installazione in una specifica piattaforma, vedere la [pagina di download di Git](http://git-scm.com/download%20target="_blank").

2. Utilizzare i comandi seguenti per aggiungere file all'archivio:

		git add .
		git commit -m "initial commit"

3. Aggiungere un sito Git remoto per effettuare il push degli aggiornamenti nell'app Web creata in precedenza, usando il comando seguente:

		git remote add azure [URL for remote repository]


4. Effettuare il push delle modifiche in Azure usando il comando seguente:

		git push azure master

	Verrà richiesto di specificare la password creata in precedenza. L'output dovrebbe essere simile al seguente:

		Counting objects: 3, done.
		Delta compression using up to 8 threads.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (3/3), 374 bytes, done.
		Total 3 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '5ebbe250c9'.
		remote: Preparing files for deployment.
		remote: Deploying Web.config to enable Node.js activation.
		remote: Deployment successful.
		To https://user@testsite.scm.azurewebsites.net/testsite.git
		 * [new branch]      master -> master


5. Per visualizzare l'app, fare clic sul pulsante **Sfoglia** nella parte **App Web** del portale di Azure.

##Pubblicazione delle modifiche apportate all'applicazione

1. Aprire il file **server.js** in un editor di testo e sostituire 'Hello World\\n' con 'Hello Azure\\n'. Salvare il file.
2. Dalla riga di comando passare alla directory **helloworld** ed eseguire i comandi seguenti:

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Verrà richiesto di specificare la password creata in precedenza.

3. Passare all'app facendo clic su **Sfoglia** e notare che gli aggiornamenti sono stati applicati.

	![Pagina Web in cui è visualizzato il messaggio 'Hello Azure'][helloworld-completed]

4. È possibile ripristinare la distribuzione precedente selezionandola in **Distribuzioni**.

>[AZURE.NOTE]Per iniziare a usare Azure App Service prima di registrarsi per ottenere un account Azure, passare alla pagina [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751). In questa pagina è possibile creare immediatamente un'app Web iniziale temporanea in App Service. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

##Passaggi successivi

Anche se nella procedura illustrata in questo articolo viene usato il portale di Azure per creare un'app Web, è anche possibile usare l'[interfaccia della riga di comando di Azure](../xplat-cli.md) per eseguire le stesse operazioni.

Node.js fornisce un ecosistema completo di moduli che è possibile usare nelle applicazioni. Per informazioni sull'uso di App Web con i moduli, vedere [Uso di moduli Node.js con applicazioni Azure](../nodejs-use-node-modules-azure-apps.md).

Per informazioni sulle versioni di Node.js fornite con Azure e su come specificare la versione da utilizzare con l'applicazione, vedere [Specifica di una versione di Node.js in un'applicazione Azure](../nodejs-specify-node-version-azure-apps.md).

Se si verificano problemi con l'applicazione dopo la distribuzione in Azure, vedere [Come eseguire il debug di un'app Web Node.js nel servizio app di Azure](web-sites-nodejs-debug.md) per informazioni su come diagnosticare l'errore.


##Risorse aggiuntive

* [Azure PowerShell](../install-configure-powershell.md)
* [Interfaccia della riga di comando di Azure](../xplat-cli.md)

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web ad App Service, vedere [Servizio app di Azure e i servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714).
* Per una guida relativa al passaggio dal portale precedente al nuovo portale, vedere [Informazioni di riferimento per l'esplorazione del portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715).


[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com


[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png

[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-quick-create2]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website2.png


[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png

[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png

[deployment-part]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-part.png

[deployment-credentials]: ./media/web-sites-nodejs-develop-deploy-mac/deployment-credentials.png


[git-url]: ./media/web-sites-nodejs-develop-deploy-mac/git-url.png

<!---HONumber=August15_HO9-->