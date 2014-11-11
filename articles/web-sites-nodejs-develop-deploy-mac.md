<properties linkid="develop-node-create-a-website-mac" urlDisplayName="Website" pageTitle="Create a Node.js website on Mac - Azure tutorials" metaKeywords="Azure create website Node, Azure deploy website Node, website Node.js, Node website" description="Learn how to build and deploy a Node.js website in Azure. Sample code is written in Java." metaCanonical="" services="web-sites" documentationCenter="nodejs" title="Build and deploy a Node.js website to Azure" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />

# Creazione e distribuzione di un sito Web Node.js in Azure

Questa esercitazione illustra come creare un'applicazione [Node][Node] e distribuirla in un sito Web di Azure tramite [Git][Git]. Le istruzioni di questa esercitazione possono essere eseguite in qualsiasi sistema operativo in grado di eseguire Node.

Se si preferisce guardare un video, nel clip a destra vengono eseguiti gli stessi passaggi dell'esercitazione.

Di seguito è riportata una schermata dell'applicazione completata:

![Finestra del browser con il messaggio 'Hello World' visualizzato.][Finestra del browser con il messaggio 'Hello World' visualizzato.]

## Creazione di un sito Web di Azure e abilitazione della pubblicazione Git

Per creare un sito Web di Azure e quindi abilitare la pubblicazione Git per tale sito, attenersi alla procedura seguente.

<div class="dev-callout">

**Nota**
Per completare l'esercitazione, è necessario un account Azure. Se non si dispone di un account, è possibile creare un account di valutazione gratuita in pochi minuti. Per informazioni dettagliate, vedere la pagina relativa alla [versione di valutazione gratuita di Azure][versione di valutazione gratuita di Azure].

</div>

1.  Accedere al [portale di gestione di Azure][portale di gestione di Azure].

2.  Fare clic sull'icona **+NEW** nella parte inferiore sinistra del portale.

    ![Portale Azure con il collegamento +NEW evidenziato.][Portale Azure con il collegamento +NEW evidenziato.]

3.  Fare clic su **SITO WEB** e quindi su **CREAZIONE RAPIDA**. Immettere un valore per **URL** e selezionare il data center per il sito Web dall'elenco a discesa **AREA**. Fare clic sul segno di spunta nella parte inferiore della finestra di dialogo.

    ![Finestra di dialogo Quick Create][Finestra di dialogo Quick Create]

4.  Quando lo stato del sito Web diventa **In esecuzione**, fare clic sul nome del sito Web per accedere al **Dashboard**.

    ![Apertura del dashboard del sito Web][Apertura del dashboard del sito Web]

5.  Nella parte inferiore destra della pagina Quickstart selezionare **Set up a deployment from source control**.

    ![Configurazione della pubblicazione Git][Configurazione della pubblicazione Git]

6.  Quando viene visualizzata la domanda "Where is your source code?", selezionare **Local Git repository**, quindi fare clic sulla freccia.

    ![Posizione del codice][Posizione del codice]

7.  Per abilitare la pubblicazione Git, è necessario specificare un nome utente e una password. Se la pubblicazione è già stata abilitata per un sito Web di Azure, non verrà chiesto di specificare il nome utente e la password. Verrà invece creato un archivio Git con il nome utente e la password indicati in precedenza. Prendere nota del nome utente e della password, poiché verranno usati per la pubblicazione Git in tutti i siti Web di Azure creati.

    ![Finestra di dialogo in cui viene chiesto di specificare nome utente e password.][Finestra di dialogo in cui viene chiesto di specificare nome utente e password.]

8.  Quando l'archivio Git è pronto, verranno visualizzate le istruzioni sui comandi Git da utilizzare per configurare un archivio locale e quindi effettuare il push dei file in Azure.

    ![Istruzioni sulla distribuzione Git restituite dopo la creazione di un archivio per il sito Web.][Istruzioni sulla distribuzione Git restituite dopo la creazione di un archivio per il sito Web.]

## Creazione e test dell'applicazione in locale

In questa sezione verrà creato un file **server.js** contenente l'esempio 'hello world' di [nodejs.org][Node]. L'esempio è stato modificato rispetto all'originale con l'aggiunta di process.env.PORT come porta in cui rimanere in ascolto durante l'esecuzione in un sito Web di Azure.

1.  Utilizzare un editor di testo per creare un nuovo fine denominato **server.js** nella directory **helloworld**. Se la directory **helloworld** non esiste, crearla.
2.  Aggiungere il codice seguente come contenuto del file **server.js**, quindi salvarlo:

        var http = require('http')
        var port = process.env.PORT || 1337;
        http.createServer(function(req, res) {
          res.writeHead(200, { 'Content-Type': 'text/plain' });
          res.end('Hello World\n');
        }).listen(port);

3.  Aprire la riga di comando e utilizzare il comando seguente per avviare la pagina Web in locale:

        node server.js

4.  Aprire il Web browser e passare all'indirizzo <http://localhost:1337>. Verrà visualizzata una pagina Web con il messaggio "Hello World", come illustrato nella schermata seguente:

    ![Finestra del browser con il messaggio 'Hello World' visualizzato.][1]

## Pubblicare l'applicazione

1.  Dalla riga di comando passare alla directory **helloworld** e immettere i comandi seguenti per inizializzare un archivio Git locale.

        git init

    <div class="dev-callout">

    **Comando Git non disponibile**
    [Git][2] è un sistema di controllo delle versioni distribuite che è possibile usare per distribuire il sito Web di Azure. Per le istruzioni sull'installazione in una specifica piattaforma, vedere la [pagina di download di Git][pagina di download di Git].

    </div>

2.  Utilizzare i comandi seguenti per aggiungere file all'archivio:

        git add .
        git commit -m "initial commit"

3.  Aggiungere un sito Git remoto per effettuare il push degli aggiornamenti nel sito Web di Azure creato in precedenza, usando il comando seguente:

        git remote add azure [URL for remote repository]

    ![Istruzioni sulla distribuzione Git restituite dopo la creazione di un archivio per il sito Web.][Istruzioni sulla distribuzione Git restituite dopo la creazione di un archivio per il sito Web.]

4.  Effettuare il push delle modifiche in Azure utilizzando il comando seguente:

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

    ![Stato della distribuzione Git nel portale][Stato della distribuzione Git nel portale]

5.  Passare al sito usando il pulsante **Sfoglia** nella pagina del sito Web di Azure all'interno del portale di gestione.

## Pubblicazione delle modifiche apportate all'applicazione

1.  Aprire il file **server.js** in un editor di testo e sostituire 'Hello World\\n' con 'Hello Azure\\n'. Salvare il file.
2.  Dalla riga di comando passare alla directory **helloworld** ed eseguire i comandi seguenti.

        git add .
        git commit -m "changing to hello azure"
        git push azure master

    Verrà richiesto di specificare la password creata in precedenza. Se si passa alla scheda Distribuzioni del sito Web di Azure nel portale di gestione, verrà visualizzata la cronologia della distribuzione aggiornata:

    ![Stato aggiornato della distribuzione Git nel portale][Stato aggiornato della distribuzione Git nel portale]

3.  Passare al sito utilizzando il pulsante **Browse** e notare che gli aggiornamenti sono stati applicati.

    ![Pagina Web in cui è visualizzato il messaggio 'Hello Azure'][Finestra del browser con il messaggio 'Hello World' visualizzato.]

4.  È possibile ripristinare la distribuzione precedente selezionandola nella scheda Distribuzioni del sito Web di Azure all'interno del portale di gestione e usando il pulsante **Ridistribuisci**.

## Passaggi successivi

Anche se nella procedura illustrata in questo articolo viene usato il portale di Azure per creare un sito Web, è anche possibile usare gli [strumenti da riga di comando di Azure per Mac e Linux][strumenti da riga di comando di Azure per Mac e Linux] per eseguire le stesse operazioni.

Node.js fornisce un ecosistema completo di moduli che è possibile utilizzare nelle applicazioni. Per informazioni sull'utilizzo di Siti Web di Azure con i moduli, vedere [Utilizzo di moduli Node.js con applicazioni Azure][Utilizzo di moduli Node.js con applicazioni Azure].

Per informazioni sulle versioni di Node.js fornite con Azure e su come specificare la versione da utilizzare con l'applicazione, vedere [Specifica di una versione di Node.js in un'applicazione Azure][Specifica di una versione di Node.js in un'applicazione Azure].

Se si verificano problemi con l'applicazione dopo la distribuzione in Azure, vedere [Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure][Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure] per informazioni su come diagnosticare l'errore.

## Risorse aggiuntive

-   [Azure PowerShell][Azure PowerShell]
-   [Strumenti da riga di comando di Azure per Mac e Linux][strumenti da riga di comando di Azure per Mac e Linux]

  [Node]: http://nodejs.org
  [Git]: http://git-scm.com
  [Finestra del browser con il messaggio 'Hello World' visualizzato.]: ./media/web-sites-nodejs-develop-deploy-mac/helloazure.png
  [versione di valutazione gratuita di Azure]: http://www.windowsazure.com/it-it/pricing/free-trial/?WT.mc_id=A7171371E
  [portale di gestione di Azure]: http://manage.windowsazure.com
  [Finestra di dialogo Quick Create]: ./media/web-sites-nodejs-develop-deploy-mac/create-quick-website.png
  [Apertura del dashboard del sito Web]: ./media/web-sites-nodejs-develop-deploy-mac/go_to_dashboard.png
  [Configurazione della pubblicazione Git]: ./media/web-sites-nodejs-develop-deploy-mac/setup_git_publishing.png
  [Posizione del codice]: ./media/web-sites-nodejs-develop-deploy-mac/where_is_code.png
  [Finestra di dialogo in cui viene chiesto di specificare nome utente e password.]: ./media/web-sites-nodejs-develop-deploy-mac/git-deployment-credentials.png
  [Istruzioni sulla distribuzione Git restituite dopo la creazione di un archivio per il sito Web.]: ./media/web-sites-nodejs-develop-deploy-mac/git-instructions.png
  [1]: ./media/web-sites-nodejs-develop-deploy-mac/helloworldlocal.png
  [2]: http://git-scm.com/
  [pagina di download di Git]: http://git-scm.com/download
  [Stato della distribuzione Git nel portale]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_first.png
  [Stato aggiornato della distribuzione Git nel portale]: ./media/web-sites-nodejs-develop-deploy-mac/git_deployments_second.png
  [strumenti da riga di comando di Azure per Mac e Linux]: /it-it/documentation/articles/xplat-cli/
  [Utilizzo di moduli Node.js con applicazioni Azure]: /it-it/documentation/articles/nodejs-use-node-modules-azure-apps/
  [Specifica di una versione di Node.js in un'applicazione Azure]: /it-it/documentation/articles/nodejs-specify-node-version-azure-apps/
  [Come eseguire il debug di un'applicazione Node.js in Siti Web di Azure]: /it-it/documentation/articles/web-sites-nodejs-debug/
  [Azure PowerShell]: /it-it/documentation/articles/install-configure-powershell/
