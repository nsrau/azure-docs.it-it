# Pubblicazione in Siti Web di Azure con Git

Siti Web di Azure supporta la distribuzione continua da strumenti di controllo del codice sorgente e di repository come BitBucket, CodePlex, Dropbox, Git, GitHub, Mercurial e TFS. È possibile usare questi strumenti per gestire il contenuto e il codice del sito Web, quindi effettuare il push delle modifiche al sito in modo rapido e immediato quando è necessario.

In questo articolo verrà illustrato l'uso di Git per pubblicare direttamente dal computer locale in un sito Web di Azure. In Azure questo metodo di pubblicazione è detto **Git locale**. Si apprenderà anche come abilitare la distribuzione continua da siti Web di repository, come BitBucket, CodePlex, Dropbox, GitHub o Mercurial. Per informazioni sull'uso di TFS per la distribuzione continua, vedere [Distribuzione continua in Azure tramite Visual Studio Online][Distribuzione continua in Azure tramite Visual Studio Online].

> [WACOM.NOTE] Molti comandi Git descritti in questo articolo vengono eseguiti automaticamente quando si crea un sito Web usando gli [strumenti da riga di comando di Azure per Mac e Linux][strumenti da riga di comando di Azure per Mac e Linux].

Questa attività include i passaggi seguenti:

-   [Installazione di Git][Installazione di Git]
-   [Creazione di un repository locale][Creazione di un repository locale]
-   [Aggiunta di una pagina Web][Aggiunta di una pagina Web]
-   [Abilitazione del repository del sito Web][Abilitazione del repository del sito Web]
-   [Distribuzione del progetto][Distribuzione del progetto]
    -   [Push di file locali in Azure (Git locale)][Push di file locali in Azure (Git locale)]
    -   [Distribuzione di file da un sito Web di repository come BitBucket, CodePlex, Dropbox, GitHub o Mercurial][Distribuzione di file da un sito Web di repository come BitBucket, CodePlex, Dropbox, GitHub o Mercurial]
    -   [Distribuzione di una soluzione di Visual Studio da BitBucket, CodePlex, Dropbox, GitHub o Mercurial][Distribuzione di una soluzione di Visual Studio da BitBucket, CodePlex, Dropbox, GitHub o Mercurial]
-   [Risoluzione dei problemi][Risoluzione dei problemi]

## <span id="Step2"></span></a>Installazione di Git

I passaggi necessari per installare Git variano a seconda del sistema operativo. Vedere la sezione [Installazione di Git][1] per linee guida specifiche del sistema operativo relative a distribuzioni e installazione.

> [WACOM.NOTE] In alcuni sistemi operativi sono disponibili versioni di Git sia da riga di comando che tramite GUI. Nelle istruzioni fornite in questo articolo si usa la versione da riga di comando.

## <span id="Step2"></span></a>Creazione di un repository locale

Per creare un nuovo repository Git, eseguire le operazioni seguenti.

1.  Creare una directory con nome MyGitRepository in cui includere il repository Git e i file del sito Web.

2.  Aprire una riga di comando, ad esempio **GitBash** (Windows) o **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla riga di comando tramite l'applicazione **Terminale**.

3.  Dalla riga di comando passare alla directory MyGitRepository.

        cd MyGitRepository

4.  Eseguire il comando seguente per inizializzare un nuovo repository Git:

        git init

    Verrà restituito un messaggio analogo al seguente: **Inizializzazione di un repository Git vuoto in [percorso]**.

## <span id="Step3"></span></a>Aggiunta di una pagina Web

In Siti Web di Azure sono supportate applicazioni create in diversi linguaggi di programmazione. Per questo esempio verrà usato un file statico con estensione html. Per informazioni sulla pubblicazione di siti Web in Azure in altri linguaggi di programmazione, vedere il [Centro per sviluppatori Azure][Centro per sviluppatori Azure].

1.  Con un editor di testo creare un nuovo file denominato **index.html** nella radice del repository Git (la directory MyGitRepository creata in precedenza).

2.  Aggiungere il testo seguente come contenuto del file index.html, quindi salvare il file.

        Hello Git!

3.  Dalla riga di comando verificare che sia selezionata la radice del repository Git. Usare quindi il comando seguente per aggiungere il file **index.html** al repository:

        git add index.html 

    > [WACOM.NOTE] Per visualizzare informazioni della Guida relative a qualsiasi comando git, digitare -help o --help dopo il comando. Ad esempio, per opzioni relative ai parametri per il comando add, digitare 'git add -help' per la guida della riga di comando oppure 'git add --help' per informazioni dettagliate.

4.  In seguito, eseguire il commit delle modifiche al repository con il comando seguente:

        git commit -m "Adding index.html to the repository"

    L'output dovrebbe essere simile al seguente:

        [master (root-commit) 369a79c] Adding index.html to the repository
         1 file changed, 1 insertion(+)
         create mode 100644 index.html

## <span id="Step4"></span></a>Abilitazione del repository del sito Web

Eseguire la procedura seguente per abilitare il repository Git per il sito Web tramite il portale di Azure:

1.  Eseguire l'accesso al [portale di Azure][portale di Azure].

2.  Fare clic sul pulsante NUOVO per creare un nuovo sito Web per il quale si abiliterà un repository.

3.  Attendere che venga completato il processo di creazione del sito Web nella visualizzazione **Siti Web** e quindi selezionare il sito Web.

    ![Immagine di un sito Web selezionato][Immagine di un sito Web selezionato]

4.  Selezionare la scheda **DASHBOARD**.

5.  Nella sezione **riepilogo rapido** selezionare **Imposta distribuzione dal controllo del codice sorgente**. Verrà visualizzata la finestra di dialogo **IMPOSTA DISTRIBUZIONE** seguente.

    ![git-WhereIsYourSourceCode][git-WhereIsYourSourceCode]

6.  Scegliere **Git locale** e quindi fare clic sulla freccia **Avanti**.

7.  Se si tratta della prima impostazione di un repository in Azure, è necessario creare le credenziali di accesso, che verranno usate per accedere al repository di Azure e per effettuare il push delle modifiche dal repository Git locale.

    ![][]

8.  Dopo una breve attesa, dovrebbe essere visualizzato un messaggio indicante che il repository è pronto.

    ![git-instructions][git-instructions]

## <span id="Step5"></span></a>Distribuzione del progetto

### <span id="Step6"></span></a>Push di file locali in Azure (Git locale)

A questo punto nel portale vengono visualizzate le istruzioni per l'inizializzazione di un repository locale e per l'aggiunta di file. Tali operazioni sono già state eseguite nei passaggi precedenti in questo argomento. Se tuttavia le credenziali di distribuzione non sono state impostate, è necessario tornare alla scheda **DASHBOARD** nel portale e fare clic su **Reimposta le credenziali di distribuzione**.

Eseguire la procedura seguente per pubblicare il sito Web in Azure tramite Git locale:

1.  Usando la riga di comando, verificare che sia selezionata la radice del repository Git locale contenente il file index.html creato in precedenza.

2.  Copiare il comando git remote add indicato nel passaggio 3 delle istruzioni visualizzate nel portale. Avrà un aspetto analogo a quello del comando seguente:

        git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git

    > [WACOM.NOTE] Il comando **remote** consente di aggiungere un riferimento denominato a un repository remoto. In questo esempio crea un riferimento denominato 'azure' per il repository del sito Web di Azure.

3.  Usare il comando seguente dalla riga di comando per effettuare il push del contenuto corrente dal repository locale al repository 'azure' remoto:

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
        * [new branch]      master -> master

    > [WACOM.NOTE] Il repository creato per il sito Web di Azure prevede che le richieste push siano destinate al ramo **master** del relativo repository, che verrà quindi usato come contenuto del sito Web.

4.  Fare clic sul collegamento **SFOGLIA** nella parte inferiore del portale per verificare che il file **index.html** sia stato distribuito. Verrà visualizzata una pagina contenente il messaggio 'Hello Git!'.

    ![Pagina Web contenente 'Hello Git!'][Pagina Web contenente 'Hello Git!']

5.  Con un editor di testo modificare il file **index.html** in modo che contenga la stringa 'Yay!', quindi salvarlo.

6.  Usare i comandi seguenti dalla riga di comando per aggiungere le modifiche, eseguirne il commit e quindi effettuare il push nel repository remoto:

        git add index.html
        git commit -m "Celebration"
        git push azure master

    Al termine del comando **push**, aggiornare il browser. Può essere necessario premere CTRL+F5 per il corretto aggiornamento. A questo punto notare che il contenuto della pagina rispecchia la modifica più recente sottoposta a commit.

    ![Pagina Web contenente 'Yay!'][Pagina Web contenente 'Yay!']

### <span id="Step7"></span></a>Distribuzione di file da un sito Web di repository come BitBucket, CodePlex, Dropbox, GitHub o Mercurial

Il push di file locali in Azure tramite Git locale consente di effettuare manualmente il push degli aggiornamenti da un progetto locale al sito Web di Azure, mentre la distribuzione da BitBucket, CodePlex, Dropbox, GitHub o Mercurial genera un processo di distribuzione continuo in cui Azure recupererà tramite pull gli aggiornamenti più recenti dal progetto.

Anche se entrambi i metodi consentono di distribuire il progetto in un sito Web di Azure, la distribuzione continua risulta utile quando molti utenti lavorano a un progetto e si desidera assicurarsi che venga sempre pubblicata la versione più recente indipendentemente da chi ha apportato l'ultimo aggiornamento. La distribuzione continua è inoltre utile se si usa uno degli strumenti citati in precedenza come repository centrale per l'applicazione.

Per distribuire file da GitHub, CodePlex o BitBucket, è necessario aver pubblicato il progetto locale in uno di questi servizi. Per altre informazioni sulla pubblicazione di progetti in questi servizi, vedere gli articoli relativi a [creazione di repository (GitHub)][creazione di repository (GitHub)], [uso di Git con CodePlex][uso di Git con CodePlex], [creazione di repository (BitBucket)][creazione di repository (BitBucket)], [uso di Dropbox per la condivisione di repository Git][uso di Dropbox per la condivisione di repository Git] oppure [guida introduttiva di Mercurial][guida introduttiva di Mercurial].

1.  Inserire innanzitutto i file del sito Web nel repository selezionato da usare per la distribuzione continua.

2.  Nel portale di Azure per il sito Web passare alla scheda **DASHBOARD**. Nella sezione **riepilogo rapido** selezionare **Imposta distribuzione dal controllo del codice sorgente**. Verrà visualizzata la finestra di dialogo **Imposta distribuzione** con la domanda **Dove è il codice sorgente?**.

3.  Scegliere il metodo di controllo del codice sorgente da usare per la distribuzione continua.

4.  Quando richiesto, immettere le credenziali per il servizio selezionato.

5.  Dopo aver autorizzato l'accesso di Azure al proprio account, verrà richiesto di scegliere un repository da un elenco.

    ![git-ChooseARepositoryToDeploy][git-ChooseARepositoryToDeploy]

6.  Selezionare il repository da associare al sito Web di Azure. Fare clic sul segno di spunta per continuare.

    > [WACOM.NOTE] Se si abilita la distribuzione continua con GitHub o BitBucket, verranno visualizzati progetti sia pubblici che privati.

7.  In Azure viene creata un'associazione con il repository selezionato e viene effettuato il pull dei file dal ramo master. Al termine del processo, nella sezione **cronologia di distribuzione** della pagina **Distribuzioni** verrà visualizzato un messaggio relativo a **Distribuzione attiva** simile al seguente:

    ![git-githubdeployed][git-githubdeployed]

8.  A questo punto, il progetto è stato distribuito dal repository selezionato al sito Web di Azure. Per verificare che il sito sia attivo, fare clic sul collegamento **Sfoglia** nella parte inferiore del portale. Il browser dovrebbe passare al sito Web.

9.  Per verificare se è in corso la distribuzione continua, apportare una modifica al progetto e quindi effettuare il push dell'aggiornamento nel repository associato al sito Web. Il sito Web dovrebbe aggiornarsi in base alle modifica poco dopo il completamento del push nel repository. Per verificare che il pull dell'aggiornamento sia stato effettuato, esaminare la pagina **Distribuzioni** del sito Web.

    ![git-GitHubDeployed-Updated][git-GitHubDeployed-Updated]

### <span id="Step75"></span></a>Distribuzione di una soluzione di Visual Studio da BitBucket, CodePlex, Dropbox, GitHub o Mercurial

Il push di una soluzione di Visual Studio a un sito Web di Azure è paragonabile al push di un semplice file index.html. Il processo di distribuzione di Siti Web di Azure semplifica tutti i dettagli, inclusi il ripristino delle dipendenze di NuGet e la compilazione dei file binari dell'applicazione. È possibile seguire le procedure consigliate per il controllo del codice sorgente riguardanti la gestione del codice solo nel repository Git, lasciando che tutte le altre operazioni siano effettuate tramite la distribuzione di Siti web di Azure.

La procedura per il push della soluzione di Visual Studio in un sito Web di Azure è la stessa descritta nella [sezione precedente][Distribuzione di file da un sito Web di repository come BitBucket, CodePlex, Dropbox, GitHub o Mercurial], purché la soluzione e il repository siano configurati come indicato di seguito:

-   Nella radice del repository aggiungere un file `.gitignore`, quindi specificare tutti i file e le cartelle da escludere dal repository, ad esempio le cartelle `Obj`, `Bin` e `packages`. Per informazioni sulla formattazione, vedere la [documentazione di gitignore][documentazione di gitignore]. Ad esempio:

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

    > [WACOM.NOTE] Se si usa GitHub, quando si crea il repository è possibile generare un file con estensione gitignore specifico di Visual Studio contenente tutti i file temporanei comuni, i risultati della compilazione e così via. Il file potrà quindi essere personalizzato in base a esigenze specifiche.

-   Aggiungere al repository l'intero albero di directory della soluzione, con il file con estensione sln nella radice del repository.

-   Nella soluzione di Visual Studio [abilitare il ripristino del pacchetto NuGet][abilitare il ripristino del pacchetto NuGet] per fare in modo che Visual Studio ripristini automaticamente i pacchetti mancanti.

Dopo avere impostato il repository come descritto e avere configurato il sito Web di Azure per la pubblicazione continua da uno dei repository Git online, sarà possibile sviluppare l'applicazione ASP.NET localmente in Visual Studio e distribuire continuamente il codice con il semplice push delle modifiche al repository Git online.

#### Funzionamento della distribuzione continua

La distribuzione continua funziona fornendo il valore di **URL TRIGGER DISTRIBUZIONE** disponibile nella sezione **Distribuzioni** della scheda **Configura** del sito.

![git-DeploymentTrigger][git-DeploymentTrigger]

Quando vengono apportati aggiornamenti al repository, viene inviata una richiesta POST all'URL per notificare al sito Web di Azure l'aggiornamento del repository. A questo punto l'aggiornamento viene recuperato e distribuito al sito Web.

Per altre informazioni sul motore di base del processo di distribuzione Git per Siti Web di Azure, vedere l'articolo relativo al [progetto Kudu][progetto Kudu].

#### Specifica del ramo da usare

Quando si abilita la distribuzione continua, per impostazione predefinita verrà usato il ramo **master** del repository. Se si desidera usare un ramo diverso, eseguire la procedura seguente:

1.  Nel portale selezionare il sito Web e quindi **CONFIGURA**.

2.  Nella sezione **distribuzioni** della pagina immettere il ramo da usare nel campo **RAMO DA DISTRIBUIRE** e premere INVIO. Infine, fare clic su **SALVA**.

    L'aggiornamento dovrebbe iniziare immediatamente in base alle modifiche apportate al nuovo ramo.

#### Disabilitazione della distribuzione continua

La distribuzione continua può essere disabilitata dal **Dashboard** di Azure. Nella sezione **riepilogo rapido** scegliere l'opzione per disconnettersi dal repository usato:

![git-DisconnectFromGitHub][git-DisconnectFromGitHub]

Dopo aver risposto **Sì** al messaggio di conferma, è possibile tornare nella sezione **riepilogo rapido** e fare clic su **Imposta distribuzione dal controllo del codice sorgente** se si desidera impostare la pubblicazione da un'altra origine.

## <span id="Step8"></span></a>Risoluzione dei problemi

Di seguito sono riportati gli errori o i problemi che si verificano comunemente durante l'uso di Git per la pubblicazione in un sito Web di Azure:

------------------------------------------------------------------------

**Sintomo**: Impossibile accedere a '[URLsito]': Impossibile connettersi a [IndirizzoScm]

**Causa**: questo errore può verificarsi se il sito Web non è in esecuzione.

**Risoluzione**: Avviare il sito Web nel portale di Azure. La distribuzione Git non funziona se il sito Web non è in esecuzione.

------------------------------------------------------------------------

**Sintomo**: Non è possibile risolvere il nome dell'host 'nomehost'

**Causa**: questo errore può verificarsi se le informazioni sull'indirizzo immesse durante la creazione del repository remoto 'azure' non sono corrette.

**Risoluzione**: usare il comando `git remote -v` per elencare tutti i repository remoti, insieme agli URL associati. Verificare che l'URL del repository remoto 'azure' sia corretto. Se necessario, rimuovere e ricreare questo repository remoto usando l'URL corretto.

------------------------------------------------------------------------

**Sintomo**: non sono stati trovati riferimenti in comune e non ne sono stati specificati. Non è stata effettuata alcuna azione. Forse è necessario specificare un ramo, ad esempio 'master'.

**Causa**: questo errore può verificarsi se non si specifica un ramo quando si effettua un'operazione push in Git e non è stato impostato il valore push.default usato da Git.

**Risoluzione**: effettuare di nuovo l'operazione push, specificando il ramo master. Ad esempio:

    git push azure master

------------------------------------------------------------------------

**Sintomo**: non sono state trovate corrispondenze per src refspec [nomeramo].

**Causa**: questo errore può verificarsi se si tenta di effettuare il push in un ramo diverso dal master nel repository remoto 'azure'.

**Risoluzione**: effettuare di nuovo l'operazione push, specificando il ramo master. Ad esempio:

    git push azure master

------------------------------------------------------------------------

**Sintomo**: le modifiche vengono sottoposte a commit nel repository remoto ma il sito Web non viene aggiornato.

**Causa**: questo errore può verificarsi se si distribuisce un'applicazione Node.js contenente un file package.json che specifica altri moduli necessari.

**Risoluzione**: prima di questo errore dovrebbero essere registrati altri messaggi 'npm ERR!' ed è possibile ottenere contesto aggiuntivo sul problema. Di seguito sono riportate le cause note di questo errore e del corrispondente messaggio 'npm ERR!':

-   **File package.json in formato non corretto**: npm ERR! Non è stato possibile leggere le dipendenze.

-   **Modulo nativo senza una distribuzione binaria per Windows**:

    -   npm ERR! Si è verificato un errore di \`cmd "/c" "node-gyp rebuild"\` con 1

        OPPURE

    -   npm ERR![<modulename@version>] preinstall: \`make || gmake\`

## Risorse aggiuntive

-   [Come usare PowerShell per Azure][Come usare PowerShell per Azure]
-   [Come usare gli strumenti da riga di comando di Azure per Mac e Linux][strumenti da riga di comando di Azure per Mac e Linux]
-   [Documentazione su Git][Documentazione su Git]
-   [Progetto Kudu][progetto Kudu]

  [Distribuzione continua in Azure tramite Visual Studio Online]: http://www.windowsazure.com/it-it/develop/net/common-tasks/publishing-with-tfs/
  [strumenti da riga di comando di Azure per Mac e Linux]: /it-it/develop/nodejs/how-to-guides/command-line-tools/
  [Installazione di Git]: #Step1
  [Creazione di un repository locale]: #Step2
  [Aggiunta di una pagina Web]: #Step3
  [Abilitazione del repository del sito Web]: #Step4
  [Distribuzione del progetto]: #Step5
  [Push di file locali in Azure (Git locale)]: #Step6
  [Distribuzione di file da un sito Web di repository come BitBucket, CodePlex, Dropbox, GitHub o Mercurial]: #Step7
  [Distribuzione di una soluzione di Visual Studio da BitBucket, CodePlex, Dropbox, GitHub o Mercurial]: #Step75
  [Risoluzione dei problemi]: #Step8
  [1]: http://git-scm.com/book/en/Getting-Started-Installing-Git
  [Centro per sviluppatori Azure]: http://www.windowsazure.com/it-it/develop/overview/
  [portale di Azure]: http://manage.windowsazure.com
  [Immagine di un sito Web selezionato]: ./media/publishing-with-git/git-select-website.png
  [git-WhereIsYourSourceCode]: ./media/publishing-with-git/git-WhereIsYourSourceCode.png
  []: ./media/publishing-with-git/git_credentials.png
  [git-instructions]: ./media/publishing-with-git/git-instructions.png
  [Pagina Web contenente 'Hello Git!']: ./media/publishing-with-git/git-hello-git.png
  [Pagina Web contenente 'Yay!']: ./media/publishing-with-git/git-yay.png
  [creazione di repository (GitHub)]: https://help.github.com/articles/create-a-repo
  [uso di Git con CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
  [creazione di repository (BitBucket)]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
  [uso di Dropbox per la condivisione di repository Git]: https://gist.github.com/trey/2722927
  [guida introduttiva di Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
  [git-ChooseARepositoryToDeploy]: ./media/publishing-with-git/git-ChooseARepositoryToDeploy.png
  [git-githubdeployed]: ./media/publishing-with-git/git-GitHubDeployed.png
  [git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
  [documentazione di gitignore]: http://git-scm.com/docs/gitignore
  [abilitare il ripristino del pacchetto NuGet]: http://docs.nuget.org/docs/workflows/using-nuget-without-committing-packages
  [git-DeploymentTrigger]: ./media/publishing-with-git/git-DeploymentTrigger.png
  [progetto Kudu]: https://github.com/projectkudu/kudu/wiki
  [git-DisconnectFromGitHub]: ./media/publishing-with-git/git-DisconnectFromGitHub.png
  [Come usare PowerShell per Azure]: http://www.windowsazure.com/it-it/develop/nodejs/how-to-guides/powershell-cmdlets/
  [Documentazione su Git]: http://git-scm.com/documentation
