Il [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714) supporta la distribuzione continua ad App Web da strumenti di repository e controllo del codice sorgente come BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial e TFS. È possibile usare questi strumenti per gestire il contenuto e il codice dell'applicazione e quindi effettuare il push delle modifiche all'app Web di Azure in modo rapido e immediato.

Questo articolo illustra l'uso di Git per pubblicare direttamente dal computer locale in App Web. In Azure questo metodo di pubblicazione è detto **Git locale**. Si apprenderà anche come abilitare la distribuzione continua da siti di repository, come BitBucket, CodePlex, Dropbox, GitHub o Mercurial. Per informazioni sull'utilizzo di TFS per la distribuzione continua, vedere [Distribuzione continua in Azure tramite Visual Studio Team Services].

> [AZURE.NOTE]Molti comandi Git descritti in questo articolo vengono eseguiti automaticamente quando si crea un'app Web usando gli [strumenti da riga di comando di Azure per Mac e Linux](/develop/nodejs/how-to-guides/command-line-tools/).

## <a id="Step1"></a>Passaggio 1: Installazione di Git

I passaggi necessari per installare Git variano a seconda del sistema operativo. Vedere la sezione [Installazione di Git] per indicazioni specifiche del sistema operativo relative a distribuzioni e installazione.

> [AZURE.NOTE]In alcuni sistemi operativi sono disponibili versioni di Git sia da riga di comando che tramite GUI. Nelle istruzioni fornite in questo articolo si utilizza la versione da riga di comando.

## <a id="Step2"></a>Passaggio 2: Creare un repository Git

Per creare un nuovo repository Git, eseguire le operazioni seguenti.

1. Creare una directory con nome MyGitRepository, in cui includere il repository Git e i file dell'app Web.

2. Aprire uno strumento da riga di comando, ad esempio **GitBash** (Windows) o **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione **Terminale**.

3. Dalla riga di comando passare alla directory MyGitRepository.

		cd MyGitRepository

4. Eseguire il comando seguente per inizializzare un nuovo repository Git:

		git init

	Verrà restituito un messaggio analogo al seguente: **Inizializzazione di un archivio Git vuoto in [percorso]**.

## <a id="Step3"></a>Passaggio 3: Aggiungere una pagina Web

In App Web sono supportate applicazioni create in diversi linguaggi di programmazione. Per questo esempio verrà usato un file statico con estensione html.

1. Con un editor di testo creare un nuovo file denominato **index.html** nella radice dell'archivio Git (la directory MyGitRepository creata in precedenza).

2. Aggiungere il testo seguente come contenuto del file index.html, quindi salvare il file.

		Hello Git!

3. Dalla riga di comando verificare che sia selezionata la radice del repository Git. Utilizzare quindi il comando seguente per aggiungere il file **index.html** all'archivio:

		git add index.html 

	> [AZURE.NOTE]Per visualizzare informazioni della Guida relative a qualsiasi comando git, digitare -help o --help dopo il comando. Ad esempio, per opzioni relative ai parametri per il comando add, digitare 'git add -help' per la guida della riga di comando oppure 'git add --help' per informazioni dettagliate.

4. In seguito, eseguire il commit delle modifiche al repository con il comando seguente:

		git commit -m "Adding index.html to the repository"

	L'output dovrebbe essere simile al seguente:

		[master (root-commit) 369a79c] Adding index.html to the repository
		 1 file changed, 1 insertion(+)
		 create mode 100644 index.html

## <a id="Step4"></a>Abilitare il repository dell’app Web

Eseguire la procedura seguente per abilitare un repository Git per l'app Web.

1. Accedere al [Portale di Azure].

2. Nel pannello dell'app Web fare clic su **Impostazioni > Distribuzione continua**. Fare clic su **Scegli origine**, quindi su **Repository Git locale** e infine su **OK**.

	![Repository Git locale](./media/publishing-with-git/azure1-local-git.png)

4. Se si tratta della prima impostazione di un repository in Azure, è necessario creare le credenziali di accesso, che verranno usate per accedere al repository di Azure e per effettuare il push delle modifiche dal repository Git locale. Dal pannello dell'app Web fare clic su **Impostazioni > Credenziali distribuzione**, quindi configurare il nome utente e la password per la distribuzione. Al termine, fare clic su **OK**.

	![](./media/publishing-with-git/azure2-credentials.png)

## <a id="Step5"></a>Distribuzione del progetto

* [Push di file locali in Azure (Git locale)](#Step6)
* [Distribuzione di file da un sito Web di repository come BitBucket, CodePlex, Dropbox, GitHub o Mercurial](#Step7)
* [Distribuzione di una soluzione di Visual Studio da BitBucket, CodePlex, Dropbox, GitHub o Mercurial](#Step75)

Eseguire la procedura seguente per pubblicare l’app Web in Azure utilizzando Git locale.

1. Nel pannello dell'app Web fare clic su **Impostazioni > Proprietà** per **URL Git**.

	![](./media/publishing-with-git/azure3-repo-details.png)

	**URL Git** è il riferimento remoto in cui eseguire la distribuzione dal repository locale. Questo URL verrà utilizzato nella procedura seguente.

1. Usando la riga di comando, verificare che sia selezionata la radice del repository Git locale contenente il file index.html creato in precedenza.

2. Utilizzare `git remote` per aggiungere il riferimento remoto elencato in **URL Git** dal passaggio 1. Il comando sarà simile al seguente:

		git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [AZURE.NOTE]Il comando **remote** consente di aggiungere un riferimento denominato a un repository remoto. In questo esempio viene creato un riferimento denominato 'azure' per il repository dell'app Web.

1. Usare il comando seguente dalla riga di comando per effettuare il push del contenuto corrente dal repository locale al repository 'azure' remoto:

		git push azure master

	Verrà richiesto di inserire la password creata in precedenza durante la reimpostazione delle credenziali di distribuzione nel portale. Immettere la password. Si noti che Gitbash non ripete gli asterischi nella console mentre si digita la password. L'output dovrebbe essere simile al seguente:

		Counting objects: 6, done.
		Compressing objects: 100% (2/2), done.
		Writing objects: 100% (6/6), 486 bytes, done.
		Total 6 (delta 0), reused 0 (delta 0)
		remote: New deployment received.
		remote: Updating branch 'master'.
		remote: Preparing deployment for commit id '369a79c929'.
		remote: Preparing files for deployment.
		remote: Deployment successful.
		To https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
		* [new branch]		master -> master

	> [AZURE.NOTE]Il repository creato per l'app Web prevede che le richieste push siano destinate al ramo <strong>master</strong> del relativo repository, che verrà quindi usato come contenuto dell'app Web.

2. Tornare al pannello dell'app Web nel portale di Azure. Il messaggio **Nessuna distribuzione trovata** dovrebbe essere diventato **Distribuzione attiva** con una voce di log del push più recente.

	![](./media/publishing-with-git/azure4-deployed.png)

2. Fare clic sul collegamento sotto **URL** nella parte superiore del pannello dell’app Web per verificare che **index.html** sia stato distribuito. Verrà visualizzata una pagina contenente il messaggio 'Hello Git!'.

	![Pagina Web contenente 'Hello Git!'][hello-git]

3. Con un editor di testo modificare il file **index.html** in modo che contenga la stringa 'Yay!', quindi salvarlo.

4. Usare i comandi seguenti dalla riga di comando per aggiungere le modifiche, eseguirne il commit e quindi effettuare il push nel repository remoto:

		git add index.html
		git commit -m "Celebration"
		git push azure master

	Al termine del comando **push**, aggiornare il browser. Può essere necessario premere CTRL+F5 per il corretto aggiornamento. A questo punto notare che il contenuto della pagina rispecchia la modifica più recente sottoposta a commit.

### <a id="Step7"></a>Distribuire file da un sito di repository come BitBucket, CodePlex, Dropbox, GitHub o Mercurial

Il push di file locali in Azure tramite Git locale consente di effettuare manualmente il push degli aggiornamenti da un progetto locale all'app Web di Azure, mentre la distribuzione da BitBucket, CodePlex, Dropbox, GitHub o Mercurial genera un processo di distribuzione continuo in cui Azure recupererà tramite pull gli aggiornamenti più recenti dal progetto.

Anche se entrambi i metodi consentono di distribuire il progetto in App Web, la distribuzione continua risulta utile quando molti utenti lavorano a un progetto e ci si vuole assicurare che venga sempre pubblicata la versione più recente indipendentemente da chi ha apportato l'ultimo aggiornamento. La distribuzione continua è inoltre utile se si usa uno degli strumenti citati in precedenza come repository centrale per l'applicazione.

Per distribuire file da GitHub, CodePlex o BitBucket, è necessario aver pubblicato il progetto locale in uno di questi servizi. Per ulteriori informazioni sulla pubblicazione di progetti in questi servizi, vedere gli articoli relativi a [creazione di archivi (GitHub)], [utilizzo di Git con CodePlex], [creazione di archivi (BitBucket)], [utilizzo di Dropbox per la condivisione di archivi Git] oppure [guida introduttiva di Mercurial].

1. Inserire innanzitutto i file dell'app Web nel repository selezionato da utilizzare per la distribuzione continua.

2. Nel pannello dell'app Web nel portale, fare clic su **Impostazioni > Recapito continuo**. Fare clic su **Scegli origine** e quindi su **GitHub**, ad esempio.

	![](./media/publishing-with-git/azure6-setup-github.png)
	
2. Nel pannello **Distribuzione continua** fare clic su **Autorizzazione** e quindi su **Autorizza**. Il portale di Azure eseguirà il reindirizzamento al sito di repository per completare il processo di autorizzazione.

4. Al termine, tornare al portale di Azure e fare clic su **OK** nel pannello **Autorizzazione**.

5. Nel pannello **Distribuzione continua** scegliere l'organizzazione, il progetto e il ramo da cui eseguire la distribuzione. Al termine, fare clic su **OK**.
  
	![](./media/publishing-with-git/azure7-setup-github-configure.png)

	> [AZURE.NOTE]Se si abilita la distribuzione continua con GitHub o BitBucket, verranno visualizzati progetti sia pubblici che privati.

In Azure viene creata un'associazione con il repository selezionato e viene effettuato il pull dei file dal ramo specificato. Al termine del processo, nella sezione **Distribuzione** del pannello dell'app Web viene visualizzato un messaggio **Distribuzione attiva** che indica l'esito positivo della distribuzione.

7. A questo punto, il progetto è stato distribuito dal repository selezionato all'app Web. Per verificare che l'app Web sia attiva, fare clic sul collegamento **URL** nella parte superiore del portale. Il browser dovrebbe passare all'app Web.

8. Per verificare che la distribuzione continua sia in corso dal repository scelto, eseguire il push di una modifica al repository. L'app Web dovrebbe aggiornarsi in base alle modifica poco dopo il completamento del push nel repository. Per verificare che sia stato eseguito il pull nell'aggiornamento, esaminare il pannello **Distribuzioni** dell'app Web.

### <a id="Step75"></a>Distribuzione di una soluzione di Visual Studio da BitBucket, CodePlex, Dropbox, GitHub o Mercurial

Il push di una soluzione di Visual Studio ad App Web nel servizio app di Azure è paragonabile al push di un semplice file index.html. Il processo di distribuzione di App Web semplifica tutti i dettagli, inclusi il ripristino delle dipendenze di NuGet e la compilazione dei file binari dell'applicazione. È possibile seguire le procedure consigliate per il controllo del codice sorgente riguardanti la gestione del codice solo nel repository Git, lasciando che tutte le altre operazioni siano effettuate tramite la distribuzione di App Web.

La procedura per il push della soluzione di Visual Studio in App Web corrisponde a quella descritta nella [sezione precedente](#Step7), purché la soluzione e il repository siano configurati come indicato di seguito:

-	Nella radice del repository aggiungere un file `.gitignore` e quindi specificare tutti i file e le cartelle da escludere dal repository, ad esempio le cartelle `Obj`, `Bin` e `packages`. Per informazioni sulla formattazione, vedere la [documentazione di gitignore](http://git-scm.com/docs/gitignore). Ad esempio:

		[Oo]bj/
		[Bb]in/
		*.user
		/TestResults
		*.vspscc
		*.vssscc
		*.suo
		*.cache
		*.csproj.user
		packages/*
		App_Data/
		/apps
		msbuild.log
		_app/
		nuget.exe

	>[AZURE.NOTE]Se si usa GitHub, quando si crea il repository è possibile generare un file con estensione gitignore specifico di Visual Studio contenente tutti i file temporanei comuni, i risultati della compilazione e così via. Il file potrà quindi essere personalizzato in base a esigenze specifiche.

-	Aggiungere al repository l'intero albero di directory della soluzione, con il file con estensione sln nella radice del repository.

-	Nella soluzione di Visual Studio [abilitare il ripristino del pacchetto NuGet](http://docs.nuget.org/Consume/Package-Restore) per fare in modo che Visual Studio ripristini automaticamente i pacchetti mancanti.

Dopo avere impostato il repository come descritto e avere configurato l'app Web in Azure per la pubblicazione continua da uno dei repository Git online, sarà possibile sviluppare l'applicazione ASP.NET localmente in Visual Studio e distribuire continuamente il codice con il semplice push delle modifiche al repository Git online.

## Disabilitare la distribuzione continua

La distribuzione continua può essere disabilitata dal pannello **Distribuzioni**. Dal pannello dell'app Web fare clic su **Impostazioni > Distribuzione continua**. e quindi su **Disconnetti**.

![git-DisconnectFromGitHub](./media/publishing-with-git/azure5-disconnect.png)

Dopo aver risposto **Sì** al messaggio di conferma, è possibile tornare al pannello dell'app Web e fare clic su **Impostazioni > Distribuzione continua** se si desidera impostare la pubblicazione da un'altra origine.

## <a id="Step8"></a>Risoluzione dei problemi

Di seguito sono riportati gli errori o i problemi che si verificano comunemente durante l'utilizzo di Git per la pubblicazione in un'app Web di Azure:

****

**Sintomo**: non è possibile accedere a '[siteURL]'. La connessione a [scmAddress] non è riuscita.

**Causa**: questo errore può verificarsi se l'app Web non è in esecuzione.

**Soluzione**: avviare l'app Web nel portale di Azure. La distribuzione Git non funziona se l'app Web non è in esecuzione.


****

**Sintomo**: non è possibile risolvere il nome dell'host 'nomehost'.

**Causa**: questo errore può verificarsi se le informazioni sull'indirizzo immesse durante la creazione del repository remoto 'azure' non sono corrette.

**Soluzione**: usare il comando `git remote -v` per elencare tutti i repository remoti, insieme agli URL associati. Verificare che l'URL del repository remoto 'azure' sia corretto. Se necessario, rimuovere e ricreare questo repository remoto usando l'URL corretto.

****

**Sintomo**: non sono stati trovati riferimenti in comune e non ne sono stati specificati. Non viene eseguita alcuna operazione. Forse è necessario specificare un ramo, ad esempio 'master'.

**Causa**: questo errore può verificarsi se non si specifica un ramo quando si effettua un'operazione push in Git e non è stato impostato il valore push.default usato da Git.

**Soluzione**: ripetere l'operazione push, specificando il ramo master. Ad esempio:

	git push azure master

****

**Sintomo**: non sono state trovate corrispondenze per src refspec [nomeramo].

**Causa**: questo errore può verificarsi se si tenta di effettuare il push in un ramo diverso dal master nel repository remoto 'azure'.

**Soluzione**: ripetere l'operazione push, specificando il ramo master. Ad esempio:

	git push azure master

****

**Sintomo**: errore. Le modifiche vengono sottoposte a commit nel repository remoto ma l’app Web non viene aggiornata.

**Causa**: questo errore può verificarsi se si distribuisce un'applicazione Node.js contenente un file package.json che specifica altri moduli necessari.

**Soluzione**: prima di questo errore dovrebbero essere registrati altri messaggi 'npm ERR!' ed è possibile ottenere contesto aggiuntivo sul problema. Di seguito sono riportate le cause note di questo errore e del corrispondente messaggio 'npm ERR!':

* **File package.json in formato non corretto**: npm ERR! Non è stato possibile leggere le dipendenze.

* **Modulo nativo senza una distribuzione binaria per Windows**:

	* npm ERR! `cmd "/c" "node-gyp rebuild"` failed with 1

		OPPURE

	* npm ERR! [modulename@version] preinstall: `make || gmake`


## Risorse aggiuntive

* [Come usare PowerShell per Azure]
* [Come usare gli strumenti da riga di comando di Azure per Mac e Linux]
* [Documentazione su Git]
* [Progetto Kudu](https://github.com/projectkudu/kudu/wiki)

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)

[Azure Developer Center]: http://azure.microsoft.com/develop/overview/
[Portale di Azure]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installazione di Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Come usare PowerShell per Azure]: ../articles/install-configure-powershell.md
[Come usare gli strumenti da riga di comando di Azure per Mac e Linux]: ../articles/xplat-cli-install.md
[Documentazione su Git]: http://git-scm.com/documentation

[portal-select-website]: ./media/publishing-with-git/git-select-website.png
[git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
[git-instructions]: ./media/publishing-with-git/git-instructions.png
[portal-deployment-credentials]: ./media/publishing-with-git/git-deployment-credentials.png

[git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
[hello-git]: ./media/publishing-with-git/git-hello-git.png
[yay]: ./media/publishing-with-git/git-yay.png
[git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
[git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
[git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
[creazione di archivi (GitHub)]: https://help.github.com/articles/create-a-repo
[utilizzo di Git con CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[creazione di archivi (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[guida introduttiva di Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
[utilizzo di Dropbox per la condivisione di archivi Git]: https://gist.github.com/trey/2722927
[Distribuzione continua in Azure tramite Visual Studio Team Services]: ../articles/cloud-services/cloud-services-continuous-delivery-use-vso.md

<!---HONumber=AcomDC_1217_2015-->