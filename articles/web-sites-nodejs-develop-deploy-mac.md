<properties 
	pageTitle="Creare un sito Web Node.js su Mac - Esercitazioni di Azure" 
	description="Informazioni sulla creazione e sulla distribuzione di un sito Web Node.js in Azure. I codici di esempio sono scritti in Java." 
	services="web-sites" 
	documentationCenter="nodejs" 
	authors="blackmist" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>






# Creazione e distribuzione di un sito Web Node.js in Azure

Questa esercitazione illustra come creare un'applicazione [Node] [nodejs.org] e distribuirla in un sito Web di Azure tramite [Git]. Le istruzioni di questa esercitazione possono essere eseguite in qualsiasi sistema operativo in grado di eseguire Node.

Se si preferisce guardare un video, nel clip a destra vengono eseguiti gli stessi passaggi dell'esercitazione.
[AZURE.VIDEO create-a-nodejs-site-deploy-from-github]
 
Di seguito è riportata una schermata dell'applicazione completata:

![A browser displaying the 'Hello World' message.][helloworld-completed]

##Creazione di un sito Web di Azure e abilitazione della pubblicazione Git

Per creare un sito Web di Azure e quindi abilitare la pubblicazione Git per tale sito, attenersi alla procedura seguente.

> [AZURE.NOTE]
> Per completare l'esercitazione, è necessario un account Azure. Se non si ha un account, è possibile creare un account di valutazione gratuito in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A7171371E" target="_blank">versione di valutazione gratuita di Azure</a>.
> 
> Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/?language=nodejs">https://trywebsites.azurewebsites.net</a>, in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

1. Accedere al [portale di gestione di Azure].

2. Fare clic sull'icona **+ NUOVO** nella parte inferiore sinistra del portale.

    ![The Azure Portal with the +NEW link highlighted.][portal-new-website]

3. Fare clic su **SITO WEB** e quindi su **CREAZIONE RAPIDA**. Immettere un valore per **URL** e selezionare il data center per il sito Web dall'elenco a discesa **AREA**. Fare clic sul segno di spunta nella parte inferiore della finestra di dialogo.

    ![The Quick Create dialog][portal-quick-create]

4. Quando lo stato del sito Web diventa **In esecuzione**, fare clic sul nome del sito Web per accedere al **Dashboard**.

	![Open web site dashboard][go-to-dashboard]

6. Nella parte inferiore destra della pagina Avvio rapido selezionare **Imposta distribuzione dal controllo del codice sorgente**.

	![Set up Git publishing][setup-git-publishing]

6. Quando viene visualizzata la domanda "Dove è il codice sorgente?", selezionare **Archivio Git locale**, quindi fare clic sulla freccia.

	![where is your source code][where-is-code]

7. Per abilitare la pubblicazione Git, è necessario specificare un nome utente e una password. Se la pubblicazione è già stata abilitata per un sito Web di Azure, non verrà chiesto di specificare il nome utente e la password. Verrà invece creato un archivio Git con il nome utente e la password indicati in precedenza. Prendere nota del nome utente e della password, poiché verranno usati per la pubblicazione Git in tutti i siti Web di Azure creati.

	![The dialog prompting for user name and password.][portal-git-username-password]

8. Quando l'archivio Git è pronto, verranno visualizzate le istruzioni sui comandi Git da usare per configurare un archivio locale e quindi effettuare il push dei file in Azure.

	![Git deployment instructions returned after creating a repository for the website.][git-instructions]

##Creazione e test dell'applicazione in locale

In questa sezione verrà creato un file **server.js** contenente l'esempio 'hello world' di [nodejs.org]. L'esempio è stato modificato rispetto all'originale con l'aggiunta di process.env.PORT come porta in cui rimanere in ascolto durante l'esecuzione in un sito Web di Azure.

1. Usare un editor di testo per creare un nuovo fine denominato **server.js** nella directory **helloworld**. Se la directory **helloworld** non esiste, crearla.
2. Aggiungere il codice seguente come contenuto del file **server.js**, quindi salvarlo:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3. Aprire la riga di comando e usare il comando seguente per avviare la pagina Web in locale:

        node server.js

4. Aprire il Web browser e passare all'indirizzo http://localhost:1337. Verrà visualizzata una pagina Web con il messaggio "Hello World", come illustrato nella schermata seguente:

    ![A browser displaying the 'Hello World' message.][helloworld-localhost]

##Pubblicare l'applicazione

1. Dalla riga di comando passare alla directory **helloworld** e immettere i comandi seguenti per inizializzare un archivio Git locale. 

		git init

	> [AZURE.NOTE] **Comando Git non disponibile**
	[Git](http://git-scm.com/%20target="_blank) è un sistema di controllo delle versioni distribuite che è possibile usare per distribuire il sito Web di Azure. Per le istruzioni sull'installazione in una specifica piattaforma, vedere la [pagina di download di Git](http://git-scm.com/download%20target="_blank").

2. Usare i comandi seguenti per aggiungere file all'archivio:

		git add .
		git commit -m "initial commit"

3. Aggiungere un sito Git remoto per effettuare il push degli aggiornamenti nel sito Web di Azure creato in precedenza, usando il comando seguente:

		git remote add azure [URL for remote repository]

    ![Git deployment instructions returned after creating a repository for the web site.][git-instructions]
 
4. Effettuare il push delle modifiche in Azure mediante il comando seguente:

		git push azure master

	Verrà richiesto di specificare la password creata in precedenza e verrà visualizzato l'output seguente.

		Password for 'testsite.scm.azurewebsites.net':
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
    
	Se si passa alla scheda Distribuzioni del sito Web di Azure nel portale di gestione, verrà visualizzata la prima distribuzione nella cronologia:

	![Git deployment status on the portal][git-deployments-first] 

5. Passare al sito usando il pulsante **Sfoglia** nella pagina del sito Web di Azure all'interno del portale di gestione.

##Pubblicazione delle modifiche apportate all'applicazione

1. Aprire il file **server.js** in un editor di testo e modificare 'Hello World\n' in 'Hello Azure\n'. Salvare il file.
2. Dalla riga di comando passare alla directory **helloworld** ed eseguire i comandi seguenti.

		git add .
		git commit -m "changing to hello azure"
		git push azure master

	Verrà richiesto di specificare la password creata in precedenza. Se si passa alla scheda Distribuzioni del sito Web di Azure nel portale di gestione, verrà visualizzata la cronologia della distribuzione aggiornata:
	
	![Git deployment status updated on the portal][git-deployments-second]

3. Passare al sito usando il pulsante **Sfoglia** e notare che gli aggiornamenti sono stati applicati.

	![A web page displaying 'Hello Azure'][helloworld-completed]

4. È possibile ripristinare la distribuzione precedente selezionandola nella scheda Distribuzioni del sito Web di Azure all'interno del portale di gestione e usando il pulsante **Ridistribuisci**.

##Passaggi successivi

Anche se nella procedura illustrata in questo articolo viene usato il portale Azure per creare un sito Web, è anche possibile usare gli [strumenti da riga di comando di Azure per Mac e Linux] per eseguire le stesse operazioni.

Node.js fornisce un ecosistema completo di moduli che è possibile usare nelle applicazioni. Per informazioni sull'uso di Siti Web di Azure con i moduli, vedere [Uso di moduli Node.js con applicazioni Azure](/it-it/documentation/articles/nodejs-use-node-modules-azure-apps/).

Per informazioni sulle versioni di Node.js fornite con Azure e su come specificare la versione da usare con l'applicazione, vedere [Specifica di una versione di Node.js in un'applicazione Azure](/it-it/documentation/articles/nodejs-specify-node-version-azure-apps/).

Se si verificano problemi con l'applicazione dopo la distribuzione in Azure, vedere [Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure](/it-it/documentation/articles/web-sites-nodejs-debug/) for information on diagnosing the problem.


##Risorse aggiuntive

* [Azure PowerShell]
* [Strumenti da riga di comando di Azure per Mac e Linux]

[Azure PowerShell]: /it-it/documentation/articles/install-configure-powershell/

[nodejs.org]: http://nodejs.org
[Git]: http://git-scm.com

[Portale di gestione di Azure]: http://manage.windowsazure.com
[Strumenti da riga di comando di Azure per Mac e Linux]: /it-it/documentation/articles/xplat-cli/

[helloworld-completed]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
[helloworld-localhost]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
[portal-new-website]: ./media/web-sites-nodejs-develop-deploy-mac/plus-new.png
[portal-quick-create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png

[portal-git-username-password]: ./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png
[git-instructions]: ./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png

[git-deployments-first]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png
[git-deployments-second]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png

[setup-git-publishing]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
[go-to-dashboard]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
[where-is-code]: ./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png


<!--HONumber=42-->
