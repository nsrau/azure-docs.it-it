# Pubblicazione da controllo del codice sorgente in Siti Web di Azure

Siti Web di Azure supporta la distribuzione continua da strumenti di
controllo del codice sorgente e di archivio come BitBucket, CodePlex,
Dropbox, Git, GitHub, Mercurial e TFS. È possibile utilizzare questi
strumenti per gestire il contenuto e il codice del sito Web, quindi
effettuare il push delle modifiche al sito in modo rapido e immediato
quando è necessario.

In questo articolo verrà illustrato l'utilizzo di Git per pubblicare
direttamente dal computer locale in un sito Web di Azure. In Azure
questo metodo di pubblicazione è denominato **Local Git**. Verrà inoltre
descritto come abilitare la distribuzione continua da siti Web di
archivio come BitBucket, CodePlex, DropBox, GitHub o Mercurial. Per
informazioni sull'utilizzo di TFS per la distribuzione continua, vedere
[Distribuzione continua in Azure tramite Visual Studio Online][1].

> [WACOM.NOTE] Molti comandi di Git descritti in questo articolo
> vengono eseguiti automaticamente quando si crea un sito Web
> utilizzando gli [strumenti da riga di comando di Azure per Mac e
> Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/).

Questa attività include i passaggi seguenti:

* [Installazione di Git](#Step1)
* [Creazione di un archivio locale](#Step2)
* [Aggiunta di una pagina Web](#Step3)
* [Abilitazione dell'archivio del sito Web](#Step4)
* [Distribuzione del progetto](#Step5)
  * [Push di file locali in Azure (Git locale)](#Step6)
  * [Distribuzione di file da un sito Web di archivio come BitBucket,
    CodePlex, Dropbox, GitHub o Mercurial](#Step7)

* [Risoluzione dei problemi](#Step8)

<h2><a  id="Step2" ></a>Installazione di Git</h2>


I passaggi necessari per installare Git variano a seconda del sistema
operativo. Vedere la sezione [Installazione di Git][2] per indicazioni
specifiche del sistema operativo relative a distribuzioni e
installazione.

> [WACOM.NOTE] In alcuni sistemi operativi sono disponibili versioni
> di Git sia da riga di comando che tramite GUI. Nelle istruzioni
> fornite in questo articolo si utilizza la versione da riga di comando.

<h2><a  id="Step2" ></a>Creazione di un archivio locale</h2>


Per creare un nuovo archivio Git, eseguire le operazioni seguenti.

1.  Creare una directory con nome MyGitRepository, in cui includere
    l'archivio Git e i file del sito Web.

2.  Aprire una riga di comando, ad esempio **GitBash** (Windows) o
    **Bash** (shell Unix). Nei sistemi OS X è possibile accedere alla
    riga di comando tramite l'applicazione **Terminale**.

3.  Dalla riga di comando passare alla directory MyGitRepository.
    
         cd MyGitRepository

4.  Eseguire il comando seguente per inizializzare un nuovo archivio
    Git:
    
         git init
    
    Verrà restituito un messaggio analogo al seguente:
    **Inizializzazione di un archivio Git vuoto in [percorso]**.

<h2><a  id="Step3" ></a>Aggiunta di una pagina Web</h2>


In Siti Web di Azure sono supportate applicazioni create in un'ampia
varietà di linguaggi di programmazione. Per questo esempio verrà
utilizzato un file statico con estensione html. Per informazioni sulla
pubblicazione di siti Web in Azure in altri linguaggi di programmazione,
vedere il [Centro per sviluppatori Azure][3].

1.  Con un editor di testo creare un nuovo file denominato
    **index.html** nella radice dell'archivio Git (la directory
    MyGitRepository creata in precedenza).

2.  Aggiungere il testo seguente come contenuto del file index.html,
    quindi salvare il file.
    
         Hello Git!

3.  Dalla riga di comando verificare che sia selezionata la radice
    dell'archivio Git. Utilizzare quindi il comando seguente per
    aggiungere il file **index.html** all'archivio:
    
         git add index.html 
    
    > [WACOM.NOTE] Per visualizzare informazioni della Guida relative
    > a qualsiasi comando git, digitare -help o --help dopo il comando.
    > Ad esempio, per opzioni relative ai parametri per il comando aff,
    > digitare `git add -help` per la guida a riga di comando oppure
    > `git add --help` per visualizzare informazioni dettagliate.

4.  In seguito, eseguire il commit delle modifiche all'archivio con il
    comando seguente:
    
         git commit -m "Adding index.html to the repository"
    
    L'output dovrebbe essere simile al seguente:
    
         [master (root-commit) 369a79c] Adding index.html to the repository
          1 file changed, 1 insertion(+)
          create mode 100644 index.html

<h2><a  id="Step4" ></a>Abilitazione dell'archivio del sito Web</h2>


Eseguire la procedura seguente per abilitare l'archivio Git per il sito
Web tramite il portale Azure:

1.  Eseguire l'accesso al [portale Azure][4].

2.  Nella pagina sinistra selezionare **Web Sites**, quindi selezionare
    il sito Web per cui abilitare l'archivio.
    
    ![Immagine di un sito Web
    selezionato](./media/publishing-with-git/git-select-website.png)

3.  Selezionare la scheda **DASHBOARD**.

4.  Nella sezione **quick glance** selezionare **Set up deployment from
    source control**. Verrà visualizzata la finestra di dialogo **SET UP
    DEPLOYMENT** seguente.
    
    ![git-WhereIsYourSourceCode](./media/publishing-with-git/git-WhereIsYourSourceCode.png)

5.  Scegliere **Local Git**, quindi fare clic sulla freccia **Next**.

6.  Dopo una breve attesa, dovrebbe essere visualizzato un messaggio
    indicante che l'archivio è pronto.
    
    ![git-instructions](./media/publishing-with-git/git-instructions.png)

<h2><a  id="Step5" ></a>Distribuzione del progetto</h2>


<h3><a  id="Step6" ></a>Push di file locali in Azure (Git locale)</h3>


A questo punto nel portale vengono visualizzate le istruzioni per
l'inizializzazione di un archivio locale e per l'aggiunta di file.
Tali operazioni sono già state eseguite nei passaggi precedenti in
questo argomento. Se tuttavia non sono state configurate le credenziali
di distribuzione, sarà necessario eseguire il passaggio 3 delle
istruzioni. Verrà richiesto di selezionare un collegamento con etichetta
**Reset your deployment credentials**.

Eseguire la procedura seguente per pubblicare il sito Web in Azure
utilizzando Local Git:

1.  Utilizzando la riga di comando, verificare che sia selezionata la
    radice dell'archivio Git locale, contenente il file index.html
    creato in precedenza.

2.  Copiare il comando git remote add indicato nel passaggio 3 delle
    istruzioni visualizzate nel portale. Avrà un aspetto analogo a
    quello del comando seguente:
    
         git remote add azure https://username@needsmoregit.scm.azurewebsites.net:443/NeedsMoreGit.git
    
    > [WACOM.NOTE] Il comando **remote** consente di aggiungere un
    > riferimento denominato a un archivio remoto. In questo esempio
    > crea un riferimento denominato 'azure' per l'archivio del sito
    > Web di Azure.

3.  Utilizzare il comando seguente dalla riga di comando per effettuare
    il push del contenuto corrente dall'archivio locale all'archivio
    'azure' remoto:
    
        git push azure master
    
    Verrà richiesto di inserire la password creata in precedenza durante
    la reimpostazione delle credenziali di distribuzione nel portale.
    Immettere la password. Si noti che Gitbash non ripete gli asterischi
    nella console mentre si digita la password. L'output dovrebbe
    essere simile al seguente:
    
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
    
    > [WACOM.NOTE] L'archivio creato per il sito Web di Azure prevede
    > che le richieste push siano destinate al ramo **master** del relativo archivio, che verrà quindi
    > utilizzato come contenuto del sito Web.

4.  Fare clic sul collegamento **BROWSE** nella parte inferiore del
    portale per verificare che il file **index.html** sia stato
    distribuito. Verrà visualizzata una pagina contenente il messaggio
    'Hello Git!'.
    
    ![Pagina Web contenente 'Hello
    Git!'](./media/publishing-with-git/git-hello-git.png)

5.  Con un editor di testo modificare il file **index.html** in modo che
    contenga la stringa 'Yay!', quindi salvarlo.

6.  Utilizzare i comandi seguenti dalla riga di comando per **aggiungere**
    le modifiche, eseguirne il **commit** e quindi effettuare il **push**
    nell'archivio remoto:
    
         git add index.html
         git commit -m "Celebration"
         git push azure master
    
    Al termine del comando **push**, aggiornare il browser. Può essere
    necessario premere CTRL+F5 per il corretto aggiornamento. A questo
    punto notare che il contenuto della pagina rispecchia la modifica
    più recente sottoposta a commit.
    
    ![Pagina Web contenente
    'Yay!'](./media/publishing-with-git/git-yay.png)

<h3><a  id="Step7" ></a>Distribuzione di file da un sito Web di archivio come BitBucket, CodePlex, Dropbox, GitHub o Mercurial</h3>


Il push di file locali in Azure tramite Git locale consente di
effettuare manualmente il push degli aggiornamenti da un progetto locale
al sito Web di Azure, mentre la distribuzione da BitBucket, CodePlex,
Dropbox, GitHub o Mercurial genera un processo di distribuzione continuo
in cui Azure recupererà tramite pull gli aggiornamenti più recenti dal
progetto.

Anche se entrambi i metodi consentono di distribuire il progetto in un
sito Web di Azure, la distribuzione continua risulta utile quando molti
utenti lavorano a un progetto e si desidera assicurarsi che venga sempre
pubblicata la versione più recente indipendentemente da chi ha apportato
l'ultimo aggiornamento. La distribuzione continua è inoltre utile se si
utilizza uno degli strumenti citati in precedenza come archivio centrale
per l'applicazione.

Per distribuire file da GitHub, CodePlex o BitBucket, è necessario aver
pubblicato il progetto locale in uno di questi servizi. Per ulteriori
informazioni sulla pubblicazione di progetti in questi servizi, vedere
gli articoli relativi a [creazione di archivi (GitHub)][5], [utilizzo di
Git con CodePlex][6], [creazione di archivi (BitBucket)][7], [utilizzo
di Dropbox per la condivisione di archivi Git][8] oppure [guida
introduttiva di Mercurial][9].

1.  Inserire innanzitutto i file del sito Web nell'archivio selezionato
    da utilizzare per la distribuzione continua.

2.  Nel portale di Azure per il sito Web passare alla scheda
    **DASHBOARD**. Nella sezione **quick glance** selezionare **Set up
    deployment from source control**. Verrà visualizzata la finestra di
    dialogo **Set Up Deployment** con la domanda **Where is your source
    code?**.

3.  Scegliere il metodo di controllo del codice sorgente da utilizzare
    per la distribuzione continua.

4.  Quando richiesto, immettere le credenziali per il servizio
    selezionato.

5.  Dopo aver autorizzato l'accesso di Azure al proprio account, verrà
    richiesto di scegliere un archivio da un elenco.
    
    ![git-ChooseARepositoryToDeploy](./media/publishing-with-git/git-ChooseARepositoryToDeploy.png)

6.  Selezionare l'archivio da associare al sito Web di Azure. Fare clic
    sul segno di spunta per continuare.
    
    > [WACOM.NOTE] Se si abilita la distribuzione continua con GitHub
    > o BitBucket, verranno visualizzati progetti sia pubblici che
    > privati.

7.  In Azure viene creata un'associazione con l'archivio selezionato e
    viene effettuato il pull dei file dal ramo master. Al termine del
    processo, nella sezione **deployment history** della pagina
    **Deployments** verrà visualizzato un messaggio relativo ad **Active
    Deployment** simile al seguente:
    
    ![git-githubdeployed](./media/publishing-with-git/git-GitHubDeployed.png)

8.  A questo punto, il progetto è stato distribuito dall'archivio
    selezionato al sito Web di Azure. Per verificare che il sito sia
    attivo, fare clic sul collegamento **Browse** nella parte inferiore
    del portale. Il browser dovrebbe passare al sito Web.

9.  Per verificare se è in corso la distribuzione continua, apportare
    una modifica al progetto e quindi effettuare il push
    dell'aggiornamento nell'archivio associato al sito Web. Il sito
    Web dovrebbe aggiornarsi in base alle modifica poco dopo il
    completamento del push nell'archivio. Per verificare che il pull
    dell'aggiornamento sia stato effettuato, esaminare la pagina
    **Deployments** del sito Web.
    
  ![git-GitHubDeployed-Updated][git-GitHubDeployed-Updated]

</a><h4>Funzionamento della distribuzione continua</h4>


La distribuzione continua funziona fornendo il valore di **DEPLOYMENT
TRIGGER URL** disponibile nella sezione **deployments** della scheda
**Configure** del sito.

  ![git-DeploymentTrigger](./media/publishing-with-git/git-DeploymentTrigger.png)

Quando vengono apportati aggiornamenti all'archivio, all'URL viene
inviata una richiesta POST, che notifica al sito Web di Azure che
l'archivio è stato aggiornato. A questo punto l'aggiornamento viene
recuperato e distribuito al sito Web.

<h4>Specifica del ramo da utilizzare</h4>


Quando si abilita la distribuzione continua, per impostazione
predefinita verrà utilizzato il ramo **master** dell'archivio. Se si
desidera utilizzare un ramo diverso, eseguire la procedura seguente:

1.  Nel portale selezionare il sito Web e quindi **CONFIGURE**.

2.  Nella sezione **deployments** della pagina immettere il ramo da
    utilizzare nel campo **BRANCH TO DEPLOY** e premere INVIO. Infine,
    fare clic su **SAVE**.
    
    L'aggiornamento dovrebbe iniziare immediatamente in base alle
    modifiche apportate al nuovo ramo.

<h4>Disabilitazione della distribuzione continua</h4>


La distribuzione continua può essere disabilitata dal **Dashboard** di
Azure. Nella sezione **quick glance** scegliere l'opzione per
disconnettersi dall'archivio utilizzato:

![git-DisconnectFromGitHub](./media/publishing-with-git/git-DisconnectFromGitHub.png)

Dopo aver risposto **Yes** al messaggio di conferma, è possibile tornare
nella sezione **quick glance** e fare clic su **Set up deployment from
source control** se si desidera impostare la pubblicazione da un'altra
origine.

<h2><a  id="Step8" ></a>Risoluzione dei problemi</h2>


Di seguito sono riportati gli errori o i problemi che si verificano
comunemente durante l'utilizzo di Git per la pubblicazione in un sito
Web di Azure:

* * *

**Sintomo**: non è possibile risolvere il nome dell'host

**Causa**: questo errore può verificarsi se le informazioni
sull'indirizzo immesse durante la creazione dell'archivio remoto
'azure' non sono corrette.

**Risoluzione**: utilizzare il comando `git remote -v` per elencare
tutti gli archivi remoti, insieme agli URL associati. Verificare che
l'URL dell'archivio remoto 'azure' sia corretto. Se necessario,
rimuovere e ricreare questo archivio remoto utilizzando l'URL corretto.

* * *

**Sintomo**: non sono stati trovati riferimenti in comune e non ne sono
stati specificati. Non è stata effettuata alcuna azione. Forse è
necessario specificare un ramo, ad esempio 'master'.

**Causa**: questo errore può verificarsi se non si specifica un ramo
quando si effettua un'operazione push in Git e non è stato impostato il
valore push.default utilizzato da Git.

**Risoluzione**: effettuare di nuovo l'operazione push, specificando
il ramo master, ad esempio:

    git push azure master

* * *

**Sintomo**: non sono state trovate corrispondenze per src refspec
[nomeramo].

**Causa**: questo errore può verificarsi se si tenta di effettuare il
push in un ramo diverso dal master nell'archivio remoto 'azure'.

**Risoluzione**: effettuare di nuovo l'operazione push, specificando
il ramo master, ad esempio:

    git push azure master

* * *

**Sintomo**: le modifiche vengono sottoposte a commit nell'archivio
remoto ma il sito Web non viene aggiornato.

**Causa**: questo errore può verificarsi se si distribuisce
un'applicazione Node.js contenente un file package.json che specifica
altri moduli necessari.

**Risoluzione**: prima di questo errore dovrebbero essere registrati
altri messaggi 'npm ERR!' ed è possibile ottenere contesto aggiuntivo
sul problema. Di seguito sono riportate le cause note di questo errore e
del corrispondente messaggio 'npm ERR!':

* **File package.json in formato non corretto**: npm ERR! Non è stato
  possibile leggere le dipendenze.

* **Modulo nativo senza una distribuzione binaria per Windows**:
  
  * npm ERR! Si è verificato un errore di \`cmd "/c" "node-gyp
    rebuild"\` con 1
    
    OPPURE
  
  * npm ERR! [nomemodulo@versione] preinstall: \`make \|\| gmake\`
## Risorse aggiuntive

* [Come utilizzare PowerShell per Azure][10]
* [Come utilizzare gli strumenti da riga di comando di Azure per Mac e
  Linux](/en-us/develop/nodejs/how-to-guides/command-line-tools/)
* [Documentazione su Git][11]



[1]: http://www.windowsazure.com/en-us/develop/net/common-tasks/publishing-with-tfs/
[2]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[3]: http://www.windowsazure.com/en-us/develop/overview/
[4]: http://manage.windowsazure.com
[5]: https://help.github.com/articles/create-a-repo
[6]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[7]: https://confluence.atlassian.com/display/BITBUCKET/Create+an+Account+and+a+Git+Repo
[8]: https://gist.github.com/trey/2722927
[9]: http://mercurial.selenic.com/wiki/QuickStart
[10]: http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/powershell-cmdlets/
[11]: http://git-scm.com/documentation
[git-GitHubDeployed-Updated]: ./media/publishing-with-git/git-GitHubDeployed-Updated.png
