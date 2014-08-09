<properties linkid="manage-services-how-to-deploy-websites" urlDisplayName="How to deploy an Azure Web Site" pageTitle="How to deploy an Azure Web Site" metaKeywords="Azure deploy publish web site" description="Learn what methods are available for deploying content to an Azure Web Site." metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Web Site" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

Come distribuire un sito Web di Azure
=====================================

Per la distribuzione dei contenuti personali su un sito Web di Azure sono disponibili molte opzioni. In questo argomento vengono forniti una breve panoramica di ogni opzione e i collegamenti per visualizzare ulteriori informazioni.

-   [Distribuzione da un sistema di controllo del codice sorgente ospitato nel cloud](#cloud)
    -   Visual Studio Online (VSO)
    -   Siti Web di repository con Git
    -   Siti Web di repository con Mercurial
    -   Dropbox
-   [Distribuzione da un IDE](#ide)
    -   Visual Studio
    -   WebMatrix
-   [Distribuzione mediante un'utilità FTP](#ftp)
-   [Distribuzione da un sistema di controllo del codice sorgente locale](#onpremises)
    -   Team Foundation Server (TFS)
    -   Repository Git o Mercurial locali
-   [Utilizzo degli strumenti da riga di comando e dell'API REST per la gestione di Azure](#commandline)
    -   MSBuild
    -   Script FTP
    -   Windows PowerShell
    -   API di gestione .NET
    -   Riga di comando multipiattaforma (xpat-cli)
    -   Riga di comando di Distribuzione Web

Distribuzione da un sistema di controllo del codice sorgente ospitato nel cloud
-------------------------------------------------------------------------------

Il miglior modo per distribuire un sito Web consiste nell'impostare un [flusso di lavoro di recapito continuo](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) integrato nel proprio [sistema di controllo del codice sorgente](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control). L'automazione rende non solo più efficiente il processo di sviluppo, ma anche più gestibili e affidabili i processi di backup e ripristino.

Qualora non sia ancora stato impostato il controllo del codice sorgente, il modo più semplice per iniziare consiste nell'utilizzare un sistema di controllo del codice sorgente ospitato sul cloud.

### Visual Studio Online (VSO)

[Visual Studio Online](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) (in precedenza Team Foundation Service) è la soluzione Microsoft basata sul cloud per il controllo del codice sorgente e la collaborazione con il team. Il servizio è gratuito per un team composto da un massimo di cinque sviluppatori. È possibile configurare VSO per l'esecuzione del recapito continuo a un sito Web di Azure e l'archivio può utilizzare [Git o TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs).

Per ulteriori informazioni, vedere le risorse seguenti:

-   [Distribuire in modo continuo](http://www.visualstudio.com/en-us/learn/continuous-delivery-in-vs). Esercitazione dettagliata in cui viene illustrato come configurare il recapito continuo da VSO su un sito Web di Azure con TFVC. TFVC è l'opzione di controllo del codice sorgente in VSO, mentre Git è l'opzione di controllo del codice distribuito.
-   [Recapito continuo in Azure utilizzando Visual Studio Online](/it-it/documentation/articles/cloud-services-continuous-delivery-use-vso/). Come iscriversi a VSO, archiviare un progetto nel controllo del codice sorgente e configurarlo per il recapito continuo (distribuzione automatica) in Azure. Scritto per i servizi cloud, ma gran parte del processo di configurazione della distribuzione in Visual Studio Online è simile per Siti Web di Azure.

### Siti Web di repository con Git

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) è un popolare sistema di controllo del codice sorgente distribuito. Azure offre funzionalità incorporate che facilitano l'automazione della distribuzione in un sito Web di Azure a partire dai popolari siti di repository basati su Web in cui sono archiviati i repository Git [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) e [BitBucket](https://bitbucket.org/). Uno dei vantaggi comportati dall'utilizzo di Git per la distribuzione consiste nel fatto che, semmai fosse necessario, è relativamente facile eseguire il rollback a una distribuzione precedente.

Per ulteriori informazioni, vedere le risorse seguenti:

-   [Pubblicazione da controllo del codice sorgente in Siti Web di Azure](/it-it/documentation/articles/web-sites-publish-source-control/). Come utilizzare Git per pubblicare direttamente dal computer locale in un sito Web di Azure. In Azure questo metodo di pubblicazione è denominato Local Git. In questo articolo viene inoltre illustrato come abilitare il recapito continuo dei repository Git da GitHub, CodePlex o BitBucket.
-   [Forum di Azure per Git, Mercurial e DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

### Siti Web di repository con Mercurial

Se il sistema di controllo del codice sorgente in uso è [Mercurial](http://mercurial.selenic.com/) e l'archivio è archiviato in [CodePlex](http://www.codeplex.com/) o [BitBucket](https://bitbucket.org/), è possibile utilizzare le funzionalità incorporata in Siti Web di Azure per distribuire automaticamente il contenuto.

Per informazioni su come eseguire la distribuzione con Mercurial vedere le risorse seguenti:

-   [Pubblicazione da controllo del codice sorgente in Siti Web di Azure](/it-it/documentation/articles/web-sites-publish-source-control/). Benché questa esercitazione contenga informazioni su come pubblicare un repository Git, il processo per i repository Mercurial ospitati in CodePlex o BitBucket è simile.
-   [Forum di Azure per Git, Mercurial e DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

### DropBox

[DropBox](https://www.dropbox.com/) non è un sistema di controllo del codice sorgente, ma se si archivia il proprio codice sorgente in DropBox è possibile automatizzarne la distribuzione dal proprio account DropBox.

-   [Distribuzione in Windows Azure con Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Come utilizzare il portale di gestione di Azure per configurare la distribuzione di DropBox.
-   [DropBox e Siti Web di Azure](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). In questo video viene illustrato il processo di connessione di una cartella di DropBox a un sito Web di Azure. Il video dimostra con quale rapidità è possibile rendere operativo un sito Web o curarne la manutenzione per mezzo di una semplice distribuzione a trascinamento.
-   [Forum di Azure per Git, Mercurial e DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

Distribuzione da un IDE
-----------------------

[Visual Studio](http://www.visualstudio.com/en-us/downloads/download-visual-studio-vs.aspx) e [WebMatrix](http://www.microsoft.com/web/webmatrix/) sono ambienti di sviluppo integrato IDE (Integrated Development Environment) di Microsoft che è possibile utilizzare per lo sviluppo Web. Entrambi offrono funzionalità incorporate che facilitano la distribuzione nei siti Web di Azure. Entrambi sono in grado di utilizzare [Distribuzione Web](http://www.iis.net/downloads/microsoft/web-deploy) per automatizzare ulteriori attività di distribuzione correlate, ad esempio la distribuzione di database e le modifiche alla stringa di connessione. Entrambi possono inoltre eseguire la distribuzione mediante [FTP o FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

WebMatrix è rapido da installare e facile da utilizzare, ma Visual Studio offre molte più funzionalità per lavorare con Siti Web di Azure. Dall'IDE di Visual Studio è possibile creare, arrestare, avviare ed eliminare i siti Web di Azure, visualizzare i log creati in tempo reale, eseguire il debug in remoto e molto altro. Visual Studio si integra inoltre con i sistemi di controllo del codice sorgente come [Visual Studio Online](#vso), [Team Foundation Server](#tfs) e [Repository Git](#git).

### Visual Studio

Per informazioni su come eseguire la distribuzione nei siti Web di Azure da Visual Studio vedere le risorse seguenti:

-   [Introduzione ad Azure e ASP.NET](/en-us/develop/net/tutorials/get-started/). Come creare e distribuire un semplice Progetto Applicazione Web MVC ASP.NET utilizzando Visual Studio e Distribuzione Web.
-   [Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure](/en-us/develop/net/tutorials/web-site-with-sql-database/). Come creare e distribuire un Progetto Applicazione Web MVC ASP.NET con un database SQL utilizzando Visual Studio, Distribuzione Web e Migrazioni Code First di Entity Framework.
-   [Panoramica di Distribuzione Web per Visual Studio e ASP.NET](http://msdn.microsoft.com/it-it/library/dd394698.aspx). Presentazione di base della distribuzione Web con Visual Studio. È un articolo datato, che però include informazioni ancora rilevanti, tra cui una panoramica delle opzioni di distribuzione di un database assieme all'applicazione Web e un elenco delle ulteriori attività di distribuzione che potrebbe essere necessario eseguire oppure far eseguire a Visual Studio configurandolo manualmente. Questo argomento riguarda la distribuzione in generale, non solo la distribuzione nei siti Web di Azure.
-   [Distribuzione Web ASP.NET con Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Una serie di esercitazioni in 12 parti in cui è trattata una gamma più completa di attività di distribuzione rispetto alle altre risorse in questo elenco.
-   Articolo relativo alla [distribuzione di un sito Web ASP.NET in Azure con Visual Studio 2012 direttamente da un repository Git](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). L'articolo contiene informazioni su come distribuire un progetto Web ASP.NET con Visual Studio, utilizzando il plugin Git per eseguire il commit del codice a Git e connettere Azure al repository Git.

### WebMatrix

Per informazioni su come eseguire la distribuzione nei siti Web di Azure da WebMatrix vedere le risorse seguenti:

-   [Sviluppo e distribuzione di un sito Web con Microsoft WebMatrix](http://www.windowsazure.com/it-it/documentation/articles/web-sites-dotnet-using-webmatrix/). Come creare un semplice sito Web di ASP.NET utilizzando un modello di WebMatrix e distribuendolo in un sito Web di Azure tramite WebMatrix e Distribuzione Web.
-   [Creazione e distribuzione di un sito Web Node.js in Azure con WebMatrix](http://www.windowsazure.com/it-it/documentation/articles/web-sites-nodejs-use-webmatrix/).
-   [Creazione e distribuzione di un sito Web di Azure PHP-MySQL tramite WebMatrix](http://www.windowsazure.com/it-it/documentation/articles/web-sites-php-mysql-use-webmatrix/).
-   [WebMatrix 3: Git integrato e distribuzione in Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Come utilizzare WebMatrix per eseguire la distribuzione da un repository Git di controllo del codice sorgente.

Distribuzione mediante un'utilità FTP
-------------------------------------

Indipendentemente dall'IDE in uso, è possibile distribuire il contenuto nel sito utilizzando [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) per copiare i file. È facile creare credenziali FTP per un sito Web di Azure e utilizzarle in qualsiasi applicazione che funzioni con il protocollo FTP, inclusi i browser come Internet Explorer e le utilità complete come [FileZilla](https://filezilla-project.org/). Siti Web di Azure supporta inoltre il più sicuro protocollo FTPS.

Benché sia semplice copiare i file del proprio sito Web in Azure mediante le utilità FTP, queste non gestiscono né coordinano automaticamente le attività di distribuzione correlate, quali la distribuzione di un database o la modifica delle stringhe di connessione. Inoltre, molti strumenti FTP non confrontano i file di origine e di destinazione in modo da saltare la copia dei file che non hanno subito modifiche. Per i siti di grandi dimensioni, l'operazione di copia di tutti i file può causare tempi di distribuzione prolungati anche per gli aggiornamenti di entità minore.

Per ulteriori informazioni, vedere le risorse seguenti:

-   [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite FTP](/it-it/documentation/articles/web-sites-php-mysql-deploy-use-ftp/).
-   [Come gestire i siti Web](http://www.windowsazure.com/it-it/documentation/articles/web-sites-manage/). Contiene informazioni aggiuntive non incluse nell'esercitazione su PHP riguardanti la modalità di configurazione delle credenziali FTP. Per informazioni su come ottenere le credenziali che non sono presenti nel documento stesso, vedere i commenti a fondo pagina.

Distribuzione da un sistema di controllo del codice sorgente locale
-------------------------------------------------------------------

Se si utilizza TFS, Git o Mercurial in un repository locale (non ospitato sul cloud) è possibile eseguire la distribuzione direttamente dal repository nei siti Web di Azure.

### Team Foundation Server (TFS)

Team Foundation Server è la soluzione Microsoft locale per il controllo del codice sorgente e la collaborazione con il team. È possibile configurare TFS per l'esecuzione del recapito continuo a un sito Web di Azure.

Per ulteriori informazioni, vedere la risorsa seguente:

-   [Recapito continuo per Servizi cloud in Azure](/en-us/develop/net/common-tasks/continuous-delivery/). Questo documento è relativo a un servizio cloud di Azure, ma alcuni dei contenuti si riferiscono a Siti Web di Azure.

### Repository Git o Mercurial locali

In Azure è possibile immettere l'URL di qualsiasi repository che utilizzi Git o Mercurial allo scopo di eseguire la distribuzione da tale posizione. È inoltre possibile effettuare il push direttamente da un repository Git in un sito Web di Azure.

Per ulteriori informazioni, vedere le risorse seguenti:

-   [Pubblicazione da controllo del codice sorgente in Siti Web di Azure](/it-it/documentation/articles/web-sites-publish-source-control/). Come utilizzare Git per pubblicare direttamente dal computer locale in un sito Web di Azure. In Azure questo metodo di pubblicazione è denominato Local Git. In questo articolo viene inoltre illustrato come abilitare il recapito continuo dei repository Git da GitHub, CodePlex o BitBucket.
-   Post sulla [pubblicazione in Siti Web di Azure da qualsiasi repository Git/HG](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html) nel blog di David Ebbo, in cui è illustrata la funzione di "Repository esterno" in Siti Web di Azure.
-   [Forum di Azure per Git, Mercurial e DropBox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).
-   Post sulla [distribuzione di DUE siti Web in Azure da un solo repository Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx) nel blog di Scott Hanselman.

Utilizzo degli strumenti da riga di comando e dell'API REST per la gestione di Azure
------------------------------------------------------------------------------------

È sempre buona norma automatizzare il flusso di lavoro di sviluppo, ma qualora non fosse possibile farlo direttamente nel proprio sistema di controllo del codice sorgente è possibile configurarlo manualmente mediante gli strumenti da riga di comando. Ciò implica in genere l'utilizzo di più strumenti o framework, in quanto la distribuzione spesso richiede l'esecuzione di funzioni di gestione del sito oltre alla copia dei contenuti.

Azure semplifica le attività di gestione del sito che è necessario portare a termine per la distribuzione fornendo un'API REST per la gestione e diversi framework che semplificano l'elaborazione con l'API.

### <a name=msbuild></a>MSBuild

Se si utilizza l'[IDE di Visual Studio](#vs) per lo sviluppo è possibile servirsi di [MSBuild](http://msbuildbook.com/) per automatizzare qualsiasi operazione nell'IDE. È possibile configurare MSBuild in modo da utilizzare [Distribuzione Web](#webdeploy) oppure [FTP/FTPS](#ftp) per copiare i file. Distribuzione Web consente inoltre di automatizzare molte altre attività relative alla distribuzione, ad esempio la distribuzione dei database.

Per ulteriori informazioni sulla distribuzione da riga di comando, vedere le risorse seguenti:

-   [Distribuzione Web ASP.NET con Visual Studio: Distribuzione da riga di comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Decima di una serie di esercitazioni sulla distribuzione in Azure con Visual Studio. Contiene informazioni su come utilizzare la riga di comando per eseguire la distribuzione dopo aver configurato i profili di pubblicazione in Visual Studio.
-   Libro [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/). Libro paperback che contiene alcuni capitoli su come utilizzare MSBuild per la distribuzione.

### Script FTP

È facile creare credenziali [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) per un sito Web di Azure per poi utilizzarle con gli script batch di FTP.

Per ulteriori informazioni, vedere la risorsa seguente:

-   [Utilizzo di script Batch FTP](http://support.microsoft.com/kb/96269).

### Windows PowerShell

In [Windows PowerShell](http://msdn.microsoft.com/it-it/library/dd835506.aspx) è possibile eseguire funzioni MSBuild o FTP. In tal caso, è inoltre possibile utilizzare una raccolta di cmdlet di Windows PowerShell che facilitano la chiamata dell'API di gestione RET di Azure.

Per ulteriori informazioni, vedere la risorsa seguente:

-   Capitolo dell'e-book relativo a come [automatizzare tutto e creare app per cloud reali con Azure](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything), in cui viene illustrato in che modo l'applicazione di esempio illustrata nell'e-book utilizza gli script di Windows PowerShell per creare un ambiente di testing Azure ed eseguirvi la distribuzione. Per ottenere collegamenti a ulteriori documenti su Azure PowerShell vedere la sezione relativa alle [risorse](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources).

### API di gestione .NET

È possibile scrivere il codice C\# per eseguire funzioni MSBuild o FTP per la distribuzione. In tal caso, sarà possibile accedere all'API REST per la gestione per eseguire funzioni di gestione del sito.

Per ulteriori informazioni, vedere la risorsa seguente:

-   Post del blog di Scott Hanselman sull'[automazione completa con le librerie di gestione di Azure e .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Contiene una presentazione dell'API di gestione .NET e collegamenti a ulteriore documentazione.

### Riga di comando multipiattaforma (xpat-cli)

È possibile utilizzare la riga di comando nei computer Mac o Linux per eseguire la distribuzione con FTP. In tal caso, è inoltre possibile accedere all'API REST per la gestione di Azure utilizzando l'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli). L'interfaccia xpat-cli può altresì essere utilizzata nei computer Windows.

Per ulteriori informazioni, vedere la risorsa seguente:

-   [Strumenti da riga di comando di Azure](/en-us/downloads/#cmd-line-tools). Pagina del portale su WindowsAzure.com per le informazioni sullo strumento da riga di comando.

### Riga di comando di Distribuzione Web

[Distribuzione Web](http://www.iis.net/downloads/microsoft/web-deploy) è il software Microsoft per la distribuzione in IIS che non solo offre funzioni intelligenti di sincronizzazione dei file, ma può anche eseguire o coordinare molte altre attività relative alla distribuzione che non è possibile automatizzare quando si utilizza il trasferimento FTP. Ad esempio, con Distribuzione Web è possibile distribuire un nuovo database oppure gli aggiornamenti al database assieme al sito Web. Distribuzione Web consente inoltre di ridurre al minimo i tempi richiesti per l'aggiornamento di un sito esistente dal momento che è in grado di copiare in maniera intelligente solo i file modificati. Il supporto per Distribuzione Web è incorporato in Microsoft WebMatrix, Visual Studio, Visual Studio Online e Team Foundation Server, ma è inoltre possibile utilizzare Distribuzione Web direttamente dalla riga di comando per automatizzare la distribuzione. I comandi di Distribuzione Web sono molti potenti, ma la curva di apprendimento può rivelarsi ripida.

Per ulteriori informazioni, vedere la risorsa seguente:

-   Documentazione ufficiale sullo [strumento di distribuzione Web](http://technet.microsoft.com/en-us/library/dd568996) sul sito Microsoft TechNet. Articolo datato, ma è ancora un buon punto di partenza.
-   Documentazione ufficiale relativa all'[utilizzo di Distribuzione Web](http://www.iis.net/learn/publish/using-web-deploy) sul sito Microsoft IIS.NET. Anche questo articolo è datato, ma è un buon punto di partenza.
-   [StackOverflow](http://www.stackoverflow.com). La migliore fonte di informazioni aggiornate su come utilizzare Distribuzione Web dalla riga di comando.
-   [Distribuzione Web ASP.NET con Visual Studio: Distribuzione da riga di comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild è il motore della compilazione utilizzato da Visual Studio e può inoltre essere utilizzato dalla riga di comando per la distribuzione di applicazioni Web nei siti Web di Azure. Questa esercitazione fa parte di una serie dedicata principalmente alla distribuzione con Visual Studio.

Passaggi successivi
-------------------

Per ulteriori informazioni, vedere la sezione relativa alla distribuzione nella [documentazione di Siti Web di Azure](/it-it/documentation/services/web-sites/).

