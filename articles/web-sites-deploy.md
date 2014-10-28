<properties linkid="manage-services-how-to-deploy-websites" pageTitle="How to deploy an Azure Website" metaKeywords="Azure deploy publish web site" description="Learn what methods are available for deploying content to an Azure Website." metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Website" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/02/2014" ms.author="tdykstra"></tags>

# Come distribuire un sito Web di Azure

Per la distribuzione dei contenuti personali su un sito Web di Azure sono disponibili molte opzioni. In questo argomento viene presentata una breve panoramica di ogni opzione e vengono forniti i collegamenti per visualizzare altre informazioni.

-   [Distribuzione da un sistema di controllo del codice sorgente ospitato nel cloud][Distribuzione da un sistema di controllo del codice sorgente ospitato nel cloud]

    -   Visual Studio Online (VSO)
    -   Siti Web di repository con Git
    -   Siti Web di repository con Mercurial
    -   Dropbox
-   [Distribuzione da un IDE][Distribuzione da un IDE]

    -   Visual Studio
    -   WebMatrix
-   [Distribuzione mediante un'utilità FTP][Distribuzione mediante un'utilità FTP]
-   [Distribuzione da un sistema di controllo del codice sorgente locale][Distribuzione da un sistema di controllo del codice sorgente locale]

    -   Team Foundation Server (TFS)
    -   Repository Git o Mercurial locali
-   [Utilizzo degli strumenti da riga di comando e dell'API REST per la gestione di Azure][Utilizzo degli strumenti da riga di comando e dell'API REST per la gestione di Azure]

    -   MSBuild
    -   Script FTP
    -   Windows PowerShell
    -   API di gestione .NET
    -   Riga di comando multipiattaforma (xpat-cli)
    -   Riga di comando di Distribuzione Web

## <a name="cloud"></a>Distribuzione da un sistema di controllo del codice sorgente ospitato nel cloud

Il miglior modo per distribuire un sito Web consiste nell'impostare un [flusso di lavoro di recapito continuo][flusso di lavoro di recapito continuo] integrato nel proprio [sistema di controllo del codice sorgente][sistema di controllo del codice sorgente]. L'automazione rende non solo più efficiente il processo di sviluppo, ma anche più gestibili e affidabili i processi di backup e ripristino.

Qualora non sia ancora stato impostato il controllo del codice sorgente, il modo più semplice per iniziare consiste nell'utilizzare un sistema di controllo del codice sorgente ospitato sul cloud.

### <a name="vso"></a>Visual Studio Online (VSO)

[Visual Studio Online][Visual Studio Online] (in precedenza Team Foundation Service) è la soluzione Microsoft basata sul cloud per il controllo del codice sorgente e la collaborazione con il team. Il servizio è gratuito per un team composto da un massimo di cinque sviluppatori. È possibile configurare VSO per l'esecuzione del recapito continuo a un sito Web di Azure e il repository può usare [Git o TFVC][Visual Studio Online].

Per altre informazioni, vedere le risorse seguenti:

-   [Recapito continuo in Azure mediante VSO e TFVC][Recapito continuo in Azure mediante VSO e TFVC]. Breve esercitazione dettagliata in cui viene illustrato come configurare il recapito continuo da VSO su un sito Web di Azure mediante TFVC. TFVC è l'opzione di controllo del codice sorgente in VSO, mentre Git è l'opzione di controllo del codice distribuito.
-   [Recapito continuo in Azure mediante VSO][Recapito continuo in Azure mediante VSO]. Simile all'esercitazione precedente, ma include solo i passaggi per l'iscrizione a un account VSO e l'archiviazione di un progetto nel controllo del codice sorgente.
-   [Recapito continuo in Azure mediante Visual Studio Online e Git][Recapito continuo in Azure mediante Visual Studio Online e Git]. Simile all'esercitazione precedente, ma usa Git anziché TFVC.

### <a name="git"></a>Siti Web di repository con Git

[Git][Visual Studio Online] è un popolare sistema di controllo del codice sorgente distribuito. Azure offre funzionalità incorporate che facilitano l'automazione della distribuzione in un sito Web di Azure a partire dai popolari siti di repository basati su Web in cui sono archiviati i repository Git [GitHub][GitHub], [CodePlex][CodePlex] e [BitBucket][BitBucket]. Uno dei vantaggi comportati dall'utilizzo di Git per la distribuzione consiste nel fatto che, semmai fosse necessario, è relativamente facile eseguire il rollback a una distribuzione precedente.

Per altre informazioni, vedere le risorse seguenti:

-   [Pubblicazione da controllo del codice sorgente in Siti Web di Azure][Pubblicazione da controllo del codice sorgente in Siti Web di Azure]. Come usare Git per pubblicare direttamente dal computer locale in un sito Web di Azure. In Azure questo metodo di pubblicazione è chiamato Local Git. In questo articolo viene inoltre illustrato come abilitare il recapito continuo dei repository Git da GitHub, CodePlex o BitBucket.
-   [Distribuzione di siti Web con GitHub mediante Kudu][Distribuzione di siti Web con GitHub mediante Kudu]. Video di Scott Hanselman e David Ebbo che mostra come distribuire un sito Web direttamente da GitHub in un sito Web di Azure.
-   [Forum di Azure per Git, Mercurial e Dropbox][Forum di Azure per Git, Mercurial e Dropbox].

### <a name="mercurial"></a>Siti Web di repository con Mercurial

Se il sistema di controllo del codice sorgente in uso è [Mercurial][Mercurial] e il repository è archiviato in [CodePlex][CodePlex] o [BitBucket][BitBucket], è possibile usare le funzionalità incorporata in Siti Web di Azure per distribuire automaticamente il contenuto.

Per informazioni su come eseguire la distribuzione con Mercurial vedere le risorse seguenti:

-   [Pubblicazione da controllo del codice sorgente in Siti Web di Azure][Pubblicazione da controllo del codice sorgente in Siti Web di Azure]. Benché questa esercitazione contenga informazioni su come pubblicare un repository Git, il processo per i repository Mercurial ospitati in CodePlex o BitBucket è simile.
-   [Forum di Azure per Git, Mercurial e Dropbox][Forum di Azure per Git, Mercurial e Dropbox].

### <a name="dropbox"></a>Dropbox

[Dropbox][Dropbox] non è un sistema di controllo del codice sorgente, ma se si archivia il proprio codice sorgente in Dropbox è possibile automatizzarne la distribuzione dal proprio account Dropbox.

-   [Distribuzione in Windows Azure con Dropbox][Distribuzione in Windows Azure con Dropbox]. Come usare il portale di gestione di Azure per configurare la distribuzione di Dropbox.
-   [Dropbox e Siti Web di Azure][Dropbox e Siti Web di Azure]. Questo video illustra il processo di connessione di una cartella di Dropbox a un sito Web di Azure. Il video mostra con quale rapidità è possibile rendere operativo un sito Web o curarne la manutenzione per mezzo di una semplice distribuzione a trascinamento.
-   [Forum di Azure per Git, Mercurial e Dropbox][Forum di Azure per Git, Mercurial e Dropbox].

## <a name="ide"></a>Distribuzione da un IDE

[Visual Studio][Visual Studio] e [WebMatrix][WebMatrix] sono ambienti di sviluppo integrato IDE (Integrated Development Environment) di Microsoft che è possibile utilizzare per lo sviluppo Web. Entrambi offrono funzionalità incorporate che facilitano la distribuzione in Siti Web di Azure. Entrambi sono in grado di utilizzare [Distribuzione Web][Distribuzione Web] per automatizzare ulteriori attività di distribuzione correlate, ad esempio la distribuzione di database e le modifiche alla stringa di connessione. Entrambi possono inoltre eseguire la distribuzione mediante [FTP o FTPS][FTP o FTPS].

WebMatrix è rapido da installare e facile da usare, ma Visual Studio offre molte più funzionalità per gestire Siti Web di Azure. Dall'IDE di Visual Studio è possibile creare, arrestare, avviare ed eliminare i siti Web di Azure, visualizzare i log creati in tempo reale, eseguire il debug in remoto e molto altro. Visual Studio si integra inoltre con i sistemi di controllo del codice sorgente come [Visual Studio Online][1], [Team Foundation Server][Team Foundation Server] e [Repository Git][Repository Git].

### <a name="vs"></a>Visual Studio

Per informazioni su come eseguire la distribuzione in Siti Web di Azure da Visual Studio vedere le risorse seguenti:

-   [Introduzione ad Azure e ASP.NET][Introduzione ad Azure e ASP.NET]. Come creare e distribuire un semplice Progetto Applicazione Web MVC ASP.NET utilizzando Visual Studio e Distribuzione Web.
-   [Come distribuire i processi Web di Azure ai siti Web di Azure][Come distribuire i processi Web di Azure ai siti Web di Azure]. Come configurare i progetti di applicazioni console in modo da distribuirli come processi Web.
-   [Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure][Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure]. Come creare e distribuire un Progetto Applicazione Web MVC ASP.NET con un database SQL utilizzando Visual Studio, Distribuzione Web e Migrazioni Code First di Entity Framework.
-   [Panoramica della distribuzione di progetti di applicazioni Web per Visual Studio e ASP.NET][Panoramica della distribuzione di progetti di applicazioni Web per Visual Studio e ASP.NET]. Presentazione di base della distribuzione Web con Visual Studio. È un articolo datato, che però include informazioni ancora rilevanti, tra cui una panoramica delle opzioni di distribuzione di un database assieme all'applicazione Web e un elenco delle ulteriori attività di distribuzione che potrebbe essere necessario eseguire oppure far eseguire a Visual Studio configurandolo manualmente. Questo argomento riguarda la distribuzione in generale, non solo la distribuzione in Siti Web di Azure.
-   [Distribuzione Web ASP.NET con Visual Studio][Distribuzione Web ASP.NET con Visual Studio]. Una serie di esercitazioni in 12 parti in cui è trattata una gamma più completa di attività di distribuzione rispetto alle altre risorse in questo elenco.
-   Articolo relativo alla [distribuzione di un sito Web ASP.NET in Azure con Visual Studio 2012 direttamente da un repository Git][distribuzione di un sito Web ASP.NET in Azure con Visual Studio 2012 direttamente da un repository Git]. L'articolo contiene informazioni su come distribuire un progetto Web ASP.NET con Visual Studio, utilizzando il plugin Git per eseguire il commit del codice a Git e connettere Azure al repository Git.

### <a name="webmatrix"></a>WebMatrix

Per informazioni su come eseguire la distribuzione in Siti Web di Azure da WebMatrix, vedere le risorse seguenti:

-   [Sviluppo e distribuzione di un sito Web con Microsoft WebMatrix][Sviluppo e distribuzione di un sito Web con Microsoft WebMatrix]. Come creare un semplice sito Web di ASP.NET usando un modello di WebMatrix e distribuendolo in un sito Web di Azure tramite WebMatrix e Distribuzione Web.
-   [Creazione e distribuzione di un sito Web Node.js in Azure con WebMatrix][Creazione e distribuzione di un sito Web Node.js in Azure con WebMatrix].
-   [Creazione e distribuzione di un sito Web di Azure PHP-MySQL tramite WebMatrix][Creazione e distribuzione di un sito Web di Azure PHP-MySQL tramite WebMatrix].
-   [WebMatrix 3: Git integrato e distribuzione in Azure][WebMatrix 3: Git integrato e distribuzione in Azure]. Come utilizzare WebMatrix per eseguire la distribuzione da un repository Git di controllo del codice sorgente.

## <a name="ftp"></a>Distribuzione mediante un'utilità FTP

Indipendentemente dall'IDE in uso, è possibile distribuire il contenuto nel sito utilizzando [FTP][FTP o FTPS] per copiare i file. È facile creare credenziali FTP per un sito Web di Azure e usarle in qualsiasi applicazione che funzioni con il protocollo FTP, inclusi i browser come Internet Explorer e le utilità complete come [FileZilla][FileZilla]. Siti Web di Azure supporta inoltre il protocollo FTPS più sicuro.

Anche se è semplice copiare i file del proprio sito Web in Azure mediante le utilità FTP, queste non gestiscono né coordinano automaticamente le attività di distribuzione correlate, quali la distribuzione di un database o la modifica delle stringhe di connessione. Inoltre, molti strumenti FTP non confrontano i file di origine e di destinazione in modo da saltare la copia dei file che non hanno subito modifiche. Per i siti di grandi dimensioni, l'operazione di copia di tutti i file può causare tempi di distribuzione prolungati anche per gli aggiornamenti di entità minore.

Per altre informazioni, vedere le risorse seguenti:

-   [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite FTP][Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite FTP].
-   [Come gestire i siti Web][Come gestire i siti Web]. Contiene informazioni aggiuntive non incluse nell'esercitazione su PHP riguardanti la modalità di configurazione delle credenziali FTP.

## <a name="onpremises"></a>Distribuzione da un sistema di controllo del codice sorgente locale

Se si usa TFS, Git o Mercurial in un repository locale (non ospitato sul cloud) è possibile eseguire la distribuzione direttamente dal repository in Siti Web di Azure.

### <a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server è la soluzione Microsoft locale per il controllo del codice sorgente e la collaborazione con il team. È possibile configurare TFS per l'esecuzione del recapito continuo a un sito Web di Azure.

Per ulteriori informazioni, vedere la risorsa seguente:

-   [Recapito continuo per Servizi cloud in Azure][Recapito continuo per Servizi cloud in Azure]. Questo documento è relativo a un servizio cloud di Azure, ma alcuni dei contenuti si riferiscono a Siti Web di Azure.

### <a name="gitmercurial"></a>Repository Git o Mercurial locali

In Azure è possibile immettere l'URL di qualsiasi repository che utilizzi Git o Mercurial allo scopo di eseguire la distribuzione da tale posizione. È inoltre possibile effettuare il push direttamente da un repository Git in un sito Web di Azure.

Per altre informazioni, vedere le risorse seguenti:

-   [Pubblicazione da controllo del codice sorgente in Siti Web di Azure][Pubblicazione da controllo del codice sorgente in Siti Web di Azure]. Come usare Git per pubblicare direttamente dal computer locale in un sito Web di Azure. In Azure questo metodo di pubblicazione è chiamato Local Git. In questo articolo viene inoltre illustrato come abilitare il recapito continuo dei repository Git da GitHub, CodePlex o BitBucket.
-   Post sulla [pubblicazione in Siti Web di Azure da qualsiasi repository Git/HG][pubblicazione in Siti Web di Azure da qualsiasi repository Git/HG] nel blog di David Ebbo, in cui è illustrata la funzione di "Repository esterno" in Siti Web di Azure.
-   [Forum di Azure per Git, Mercurial e Dropbox][Forum di Azure per Git, Mercurial e Dropbox].
-   Post sulla [distribuzione di DUE siti Web in Azure da un solo repository Git][distribuzione di DUE siti Web in Azure da un solo repository Git] nel blog di Scott Hanselman.

## <a name="commandline"></a> Utilizzo degli strumenti da riga di comando e dell'API REST per la gestione di Azure

È sempre buona norma automatizzare il flusso di lavoro di sviluppo, ma qualora non fosse possibile farlo direttamente nel proprio sistema di controllo del codice sorgente è possibile configurarlo manualmente mediante gli strumenti da riga di comando. Ciò implica in genere l'utilizzo di più strumenti o framework, in quanto la distribuzione spesso richiede l'esecuzione di funzioni di gestione del sito oltre alla copia dei contenuti.

Azure semplifica le attività di gestione del sito che è necessario portare a termine per la distribuzione fornendo un'API REST per la gestione e diversi framework che semplificano l'elaborazione con l'API.

### <a name="msbuild"></a>MSBuild

Se si utilizza l'[IDE di Visual Studio][IDE di Visual Studio] per lo sviluppo è possibile servirsi di [MSBuild][MSBuild] per automatizzare qualsiasi operazione nell'IDE. È possibile configurare MSBuild in modo da utilizzare [Distribuzione Web][2] oppure [FTP/FTPS][Distribuzione mediante un'utilità FTP] per copiare i file. Distribuzione Web consente inoltre di automatizzare molte altre attività relative alla distribuzione, ad esempio la distribuzione dei database.

Per ulteriori informazioni sulla distribuzione da riga di comando, vedere le risorse seguenti:

-   [Distribuzione Web ASP.NET con Visual Studio: Distribuzione da riga di comando][Distribuzione Web ASP.NET con Visual Studio: Distribuzione da riga di comando]. Decima di una serie di esercitazioni sulla distribuzione in Azure con Visual Studio. Contiene informazioni su come utilizzare la riga di comando per eseguire la distribuzione dopo aver configurato i profili di pubblicazione in Visual Studio.
-   Libro [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build][MSBuild]. Libro paperback che contiene alcuni capitoli su come utilizzare MSBuild per la distribuzione.

### <a name="ftp2"></a>Script FTP

È facile creare credenziali [FTP/FTPS][FTP o FTPS] per un sito Web di Azure per poi usarle con gli script batch FTP.

Per ulteriori informazioni, vedere la risorsa seguente:

-   [Utilizzo di script Batch FTP][Utilizzo di script Batch FTP].

### <a name="powershell"></a>Windows PowerShell

In [Windows PowerShell][Windows PowerShell] è possibile eseguire funzioni di distribuzione MSBuild o FTP. In tal caso, è inoltre possibile utilizzare una raccolta di cmdlet di Windows PowerShell che facilitano la chiamata dell'API di gestione RET di Azure.

Per ulteriori informazioni, vedere la risorsa seguente:

-   Capitolo dell'e-book relativo a come [automatizzare tutto e creare app per cloud reali con Azure][automatizzare tutto e creare app per cloud reali con Azure], in cui viene illustrato in che modo l'applicazione di esempio illustrata nell'e-book utilizza gli script di Windows PowerShell per creare un ambiente di testing Azure ed eseguirvi la distribuzione. Per ottenere collegamenti a ulteriori documenti su Azure PowerShell vedere la sezione relativa alle [risorse][risorse].

### <a name="api"></a>API di gestione .NET

È possibile scrivere il codice C# per eseguire funzioni MSBuild o FTP per la distribuzione. In tal caso, sarà possibile accedere all'API REST per la gestione per eseguire funzioni di gestione del sito.

Per ulteriori informazioni, vedere la risorsa seguente:

-   Post del blog di Scott Hanselman sull'[automazione completa con le librerie di gestione di Azure e .NET][automazione completa con le librerie di gestione di Azure e .NET]. Contiene una presentazione dell'API di gestione .NET e collegamenti a ulteriore documentazione.

### <a name="cli"></a>Riga di comando multipiattaforma (xpat-cli)

È possibile utilizzare la riga di comando nei computer Mac o Linux per eseguire la distribuzione con FTP. In tal caso, è inoltre possibile accedere all'API REST per la gestione di Azure utilizzando l'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli). L'interfaccia xpat-cli può altresì essere utilizzata nei computer Windows.

Per ulteriori informazioni, vedere la risorsa seguente:

-   [Strumenti da riga di comando di Azure][Strumenti da riga di comando di Azure]. Pagina del portale su WindowsAzure.com per le informazioni sullo strumento da riga di comando.

### <a name="webdeploy"></a>Riga di comando di Distribuzione Web

[Distribuzione Web][Distribuzione Web] è il software Microsoft per la distribuzione in IIS che non solo offre funzioni intelligenti di sincronizzazione dei file, ma può anche eseguire o coordinare molte altre attività relative alla distribuzione che non è possibile automatizzare quando si utilizza il trasferimento FTP. Ad esempio, con Distribuzione Web è possibile distribuire un nuovo database oppure gli aggiornamenti al database insieme al sito Web. Distribuzione Web consente inoltre di ridurre al minimo i tempi richiesti per l'aggiornamento di un sito esistente dal momento che è in grado di copiare in maniera intelligente solo i file modificati. Il supporto per Distribuzione Web è incorporato in Microsoft WebMatrix, Visual Studio, Visual Studio Online e Team Foundation Server, ma è inoltre possibile utilizzare Distribuzione Web direttamente dalla riga di comando per automatizzare la distribuzione. I comandi di Distribuzione Web sono molti potenti, ma la curva di apprendimento può rivelarsi ripida.

Per ulteriori informazioni, vedere la risorsa seguente:

-   Documentazione ufficiale sullo [strumento di distribuzione Web][strumento di distribuzione Web] sul sito Microsoft TechNet. Articolo datato, ma è ancora un buon punto di partenza.
-   Documentazione ufficiale relativa all'[utilizzo di Distribuzione Web][utilizzo di Distribuzione Web] sul sito Microsoft IIS.NET. Anche questo articolo è datato, ma è un buon punto di partenza.
-   [StackOverflow][StackOverflow]. La migliore fonte di informazioni aggiornate su come utilizzare Distribuzione Web dalla riga di comando.
-   [Distribuzione Web ASP.NET con Visual Studio: Distribuzione da riga di comando][Distribuzione Web ASP.NET con Visual Studio: Distribuzione da riga di comando]. MSBuild è il motore di compilazione usato da Visual Studio e può inoltre essere usato dalla riga di comando per la distribuzione di applicazioni Web in Siti Web di Azure. Questa esercitazione fa parte di una serie dedicata principalmente alla distribuzione con Visual Studio.

## <a name="nextsteps"></a>Passaggi successivi

In alcuni scenari può essere necessario passare da una versione temporanea del sito Web a una versione di produzione e viceversa. È possibile eseguire questa operazione con la funzionalità di distribuzione temporanea di Siti Web di Azure. Per altre informazioni, vedere [Distribuzione temporanea su Siti Web di Microsoft Azure][Distribuzione temporanea su Siti Web di Microsoft Azure].

La definizione di un piano di backup e ripristino è una parte importante di un flusso di distribuzione. Per informazioni sulla funzionalità di backup e ripristino di Siti Web di Azure, vedere [Backup di Siti Web di Azure][Backup di Siti Web di Azure].

Per altre informazioni sulla distribuzione, vedere la sezione specifica nella [documentazione di Siti Web di Azure][documentazione di Siti Web di Azure].

  [Distribuzione da un sistema di controllo del codice sorgente ospitato nel cloud]: #cloud
  [Distribuzione da un IDE]: #ide
  [Distribuzione mediante un'utilità FTP]: #ftp
  [Distribuzione da un sistema di controllo del codice sorgente locale]: #onpremises
  [Utilizzo degli strumenti da riga di comando e dell'API REST per la gestione di Azure]: #commandline
  [flusso di lavoro di recapito continuo]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery
  [sistema di controllo del codice sorgente]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control
  [Visual Studio Online]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs
  [Recapito continuo in Azure mediante VSO e TFVC]: http://www.visualstudio.com/it-it/learn/continuous-delivery-in-vs
  [Recapito continuo in Azure mediante VSO]: /it-it/documentation/articles/cloud-services-continuous-delivery-use-vso/
  [Recapito continuo in Azure mediante Visual Studio Online e Git]: http://azure.microsoft.com/it-it/documentation/articles/cloud-services-continuous-delivery-use-vso-git/
  [GitHub]: http://www.github.com
  [CodePlex]: http://www.codeplex.com/
  [BitBucket]: https://bitbucket.org/
  [Pubblicazione da controllo del codice sorgente in Siti Web di Azure]: /it-it/documentation/articles/web-sites-publish-source-control/
  [Distribuzione di siti Web con GitHub mediante Kudu]: /it-it/documentation/videos/deploying-to-azure-from-github/
  [Forum di Azure per Git, Mercurial e Dropbox]: http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit
  [Mercurial]: http://mercurial.selenic.com/
  [Dropbox]: https://www.dropbox.com/
  [Distribuzione in Windows Azure con Dropbox]: http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx
  [Dropbox e Siti Web di Azure]: http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites
  [Visual Studio]: http://www.visualstudio.com/it-it/downloads/download-visual-studio-vs.aspx
  [WebMatrix]: http://www.microsoft.com/web/webmatrix/
  [Distribuzione Web]: http://www.iis.net/downloads/microsoft/web-deploy
  [FTP o FTPS]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
  [1]: #vso
  [Team Foundation Server]: #tfs
  [Repository Git]: #git
  [Introduzione ad Azure e ASP.NET]: /it-it/develop/net/tutorials/get-started/
  [Come distribuire i processi Web di Azure ai siti Web di Azure]: /it-it/documentation/articles/websites-dotnet-deploy-webjobs/
  [Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure]: /it-it/develop/net/tutorials/web-site-with-sql-database/
  [Panoramica della distribuzione di progetti di applicazioni Web per Visual Studio e ASP.NET]: http://msdn.microsoft.com/it-it/library/dd394698.aspx
  [Distribuzione Web ASP.NET con Visual Studio]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction
  [distribuzione di un sito Web ASP.NET in Azure con Visual Studio 2012 direttamente da un repository Git]: http://www.dotnetcurry.com/ShowArticle.aspx?ID=881
  [Sviluppo e distribuzione di un sito Web con Microsoft WebMatrix]: http://www.windowsazure.com/it-it/documentation/articles/web-sites-dotnet-using-webmatrix/
  [Creazione e distribuzione di un sito Web Node.js in Azure con WebMatrix]: http://www.windowsazure.com/it-it/documentation/articles/web-sites-nodejs-use-webmatrix/
  [Creazione e distribuzione di un sito Web di Azure PHP-MySQL tramite WebMatrix]: http://www.windowsazure.com/it-it/documentation/articles/web-sites-php-mysql-use-webmatrix/
  [WebMatrix 3: Git integrato e distribuzione in Azure]: http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD
  [FileZilla]: https://filezilla-project.org/
  [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite FTP]: /it-it/documentation/articles/web-sites-php-mysql-deploy-use-ftp/
  [Come gestire i siti Web]: http://www.windowsazure.com/it-it/documentation/articles/web-sites-manage#ftp-credentials
  [Recapito continuo per Servizi cloud in Azure]: /it-it/develop/net/common-tasks/continuous-delivery/
  [pubblicazione in Siti Web di Azure da qualsiasi repository Git/HG]: http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html
  [distribuzione di DUE siti Web in Azure da un solo repository Git]: http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx
  [IDE di Visual Studio]: #vs
  [MSBuild]: http://msbuildbook.com/
  [2]: #webdeploy
  [Distribuzione Web ASP.NET con Visual Studio: Distribuzione da riga di comando]: http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment
  [Utilizzo di script Batch FTP]: http://support.microsoft.com/kb/96269
  [Windows PowerShell]: http://msdn.microsoft.com/it-it/library/dd835506.aspx
  [automatizzare tutto e creare app per cloud reali con Azure]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything
  [risorse]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources
  [automazione completa con le librerie di gestione di Azure e .NET]: http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx
  [Strumenti da riga di comando di Azure]: /it-it/downloads/#cmd-line-tools
  [strumento di distribuzione Web]: http://technet.microsoft.com/it-it/library/dd568996
  [utilizzo di Distribuzione Web]: http://www.iis.net/learn/publish/using-web-deploy
  [StackOverflow]: http://www.stackoverflow.com
  [Distribuzione temporanea su Siti Web di Microsoft Azure]: /it-it/documentation/articles/web-sites-staged-publishing/
  [Backup di Siti Web di Azure]: /it-it/documentation/articles/web-sites-backup/
  [documentazione di Siti Web di Azure]: /it-it/documentation/services/web-sites/
