<properties
	pageTitle="Distribuzione di un'app nel servizio app di Azure"
	description="Informazioni sui metodi disponibili per la distribuzione di contenuti nelle app Web."
	services="app-service\web"
	documentationCenter=""
	authors="tdykstra"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service-web"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/24/2015"
	ms.author="tdykstra"/>

#Distribuzione di un'app nel servizio app di Azure

## Panoramica

Sono disponibili numerose opzioni per distribuire i contenuti nel [servizio app per app Web](http://go.microsoft.com/fwlink/?LinkId=529714). In questo argomento vengono forniti una breve panoramica di ogni opzione e i collegamenti per visualizzare ulteriori informazioni.


###<a name="cloud"></a>Distribuire da un sistema di controllo del codice sorgente ospitato nel cloud

Il miglior modo per distribuire un'app Web consiste nell'impostare un [flusso di lavoro di recapito continuo](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) integrato nel proprio [sistema di controllo del codice sorgente](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control). L'automazione rende non solo più efficiente il processo di sviluppo, ma anche più gestibili e affidabili i processi di backup e ripristino.

Qualora non sia ancora stato impostato il controllo del codice sorgente, il modo più semplice per iniziare consiste nell'utilizzare un sistema di controllo del codice sorgente ospitato sul cloud.

* [Visual Studio Online](#vso)
* [Siti Web di repository con Git](#git)
* [Siti Web di repository con Mercurial](#mercurial)
* [Dropbox](#dropbox)

###<a name="ide"></a>Distribuzione da un IDE

[Visual Studio](http://www.visualstudio.com/) e [WebMatrix](http://www.microsoft.com/web/webmatrix/) sono ambienti di sviluppo integrato IDE (Integrated Development Environment) di Microsoft che è possibile utilizzare per lo sviluppo Web. Entrambi offrono funzionalità incorporate che facilitano la distribuzione nelle app Web. Entrambi sono in grado di utilizzare [Distribuzione Web](http://www.iis.net/downloads/microsoft/web-deploy) per automatizzare ulteriori attività di distribuzione correlate, ad esempio la distribuzione di database e le modifiche alla stringa di connessione. Entrambi possono inoltre eseguire la distribuzione mediante [FTP o FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

WebMatrix è rapido da installare e facile da usare, ma Visual Studio offre molte più funzionalità per gestire le app Web. Dall'IDE di Visual Studio è possibile creare, arrestare, avviare ed eliminare le app Web, visualizzare i log creati in tempo reale, eseguire il debug in remoto e molto altro. Visual Studio si integra inoltre con i sistemi di controllo del codice sorgente come [Visual Studio Online](#vso), [Team Foundation Server](#tfs) e [Repository Git](#git).

* [Visual Studio](#vs)
* [WebMatrix](#webmatrix)

###<a name="ftp"></a>Distribuire mediante un'utilità FTP

Indipendentemente dall'IDE in uso, è possibile distribuire il contenuto nell'app utilizzando un'utilità [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) per copiare i file. È facile creare credenziali FTP per un'app Web e usarle in qualsiasi applicazione che funzioni con il protocollo FTP, inclusi i browser come Internet Explorer e le utilità complete e gratuite come [FileZilla](https://filezilla-project.org/). App Web supporta anche il protocollo FTPS, più sicuro.

Sebbene sia semplice copiare i file dell'app Web in Azure mediante le utilità FTP, queste non gestiscono né coordinano automaticamente le attività di distribuzione correlate, quali la distribuzione di un database o la modifica delle stringhe di connessione. Inoltre, molti strumenti FTP non confrontano i file di origine e di destinazione in modo da saltare la copia dei file che non hanno subito modifiche. Per le app di grandi dimensioni, l'operazione di copia di tutti i file può causare tempi di distribuzione prolungati anche per gli aggiornamenti di entità minore, dal momento che vengono copiati tutti i file.

###<a name="onpremises"></a>Distribuzione da un sistema di controllo del codice sorgente locale

Se si utilizza TFS, Git o Mercurial in un repository locale (non ospitato sul cloud), è possibile eseguire la distribuzione direttamente dal repository nell'app Web.

* [Team Foundation Server (TFS)](#tfs)
* [Repository Git o Mercurial locali](#onpremises)

###<a name="commandline"></a>Distribuire utilizzando gli strumenti della riga di comando e l'API REST per la gestione di Azure

È sempre buona norma automatizzare il flusso di lavoro di sviluppo, ma qualora non fosse possibile farlo direttamente nel proprio sistema di controllo del codice sorgente è possibile configurarlo manualmente mediante gli strumenti da riga di comando. Ciò implica in genere l'utilizzo di più strumenti o framework, in quanto la distribuzione spesso richiede l'esecuzione di funzioni di gestione del sito oltre alla copia dei contenuti.

Azure semplifica le attività di gestione del sito che è necessario portare a termine per la distribuzione fornendo un'API REST per la gestione e diversi framework che semplificano l'elaborazione con l'API.

* [FTP](#ftp)
* [MSBuild](#msbuild)
* [Script FTP](#ftp2)
* [Windows PowerShell](#powershell)
* [API di gestione .NET](#api)
* [Riga di comando multipiattaforma (xplat-cli)](#cli)
* [Riga di comando di Distribuzione Web](#webdeploy)

##<a name="vso"></a>Visual Studio Online

[Visual Studio Online](http://www.visualstudio.com/) (in precedenza Team Foundation Service) è la soluzione Microsoft basata sul cloud per il controllo del codice sorgente e la collaborazione con il team. Il servizio è gratuito per un team composto da un massimo di cinque sviluppatori. È possibile eseguire il recapito continuo a un'app Web del servizio app e il repository può utilizzare sia [Git che TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs).

Per ulteriori informazioni, vedere le risorse seguenti:

* [Recapito continuo ad Azure tramite Visual Studio Online e TFVC](../cloud-services-continuous-delivery-use-vso.md). Esercitazione guidata che illustra la procedura di configurazione del recapito continui da Visual Studio Online a un'app Web tramite TFVC. TFVC è l'opzione di controllo del codice sorgente, mentre Git è l'opzione di controllo del codice distribuito.
* [Recapito continuo in Azure mediante Visual Studio Online e Git](../cloud-services-continuous-delivery-use-vso-git.md). Simile all'esercitazione precedente, ma usa Git anziché TFVC.

##<a name="git"></a>Siti Web di repository con Git

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) è un popolare sistema di controllo del codice sorgente distribuito. Azure offre funzionalità incorporate che facilitano l'automazione della distribuzione in un'app Web a partire dai popolari siti di repository basati su Web in cui sono archiviati i repository Git, ad esempio, [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) e [BitBucket](https://bitbucket.org/). Uno dei vantaggi comportati dall'utilizzo di Git per la distribuzione consiste nel fatto che, semmai fosse necessario, è relativamente facile eseguire il rollback a una distribuzione precedente.

Per ulteriori informazioni, vedere le risorse seguenti:

* [Pubblicazione dal controllo del codice sorgente ad App Web con Git](web-sites-publish-source-control.md). Nell'articolo viene descritto come usare Git per pubblicare direttamente dal computer locale ad App Web; in Azure, questo metodo di pubblicazione è denominato Git locale. In questo articolo viene inoltre illustrato come abilitare il recapito continuo dei repository Git da GitHub, CodePlex o BitBucket.
* [Distribuzione in App Web con GitHub tramite Kudu](/documentation/videos/deploying-to-azure-from-github/). Video di Scott Hanselman e David Ebbo che spiega come distribuire un'app Web in modo diretto da GitHub ad App Web.
* [Pulsante Distribuisci su Azure per App Web](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Blog riguardante un metodo per attivare la distribuzione da un repository Git.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

##<a name="mercurial"></a>Siti Web di repository con Mercurial

Se il sistema di controllo del codice sorgente in uso è [Mercurial](http://mercurial.selenic.com/) e il repository è archiviato in [CodePlex](http://www.codeplex.com/) o [BitBucket](https://bitbucket.org/), è possibile usare le funzionalità integrate del servizio app di Azure per distribuire automaticamente i contenuti.

Per informazioni su come eseguire la distribuzione con Mercurial vedere le risorse seguenti:

* [Pubblicazione dal controllo del codice sorgente ad App Web con Git](web-sites-publish-source-control.md). Benché questa esercitazione contenga informazioni su come pubblicare un repository Git, il processo per i repository Mercurial ospitati in CodePlex o BitBucket è simile.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

##<a name="dropbox"></a>Dropbox

[Dropbox](https://www.dropbox.com/) non è un sistema di controllo del codice sorgente, ma se si archivia il proprio codice sorgente in Dropbox è possibile automatizzarne la distribuzione dal proprio account Dropbox.

* [Eseguire la distribuzione in App Web da Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Come utilizzare il [Portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) per configurare la distribuzione tramite Dropbox.
* [Dropbox Deployment to Web App (Distribuzione di Dropbox ad App Web](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). In questo video viene illustrato il processo di connessione di una cartella di DropBox a un'app Web. Il video dimostra con quale rapidità è possibile rendere operativo un'app Web o gestirla per mezzo di una semplice distribuzione a trascinamento.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

##<a name="vs"></a>Visual Studio

Per informazioni su come eseguire la distribuzione in App Web da Visual Studio, vedere le risorse seguenti:

* [Introduzione ad Azure e ASP.NET](web-sites-dotnet-get-started.md). Come creare e distribuire un semplice Progetto Applicazione Web MVC ASP.NET utilizzando Visual Studio e Distribuzione Web.
* [Come eseguire la distribuzione nei processi Web di Azure utilizzando Visual Studio](websites-dotnet-deploy-webjobs.md). Come configurare i progetti di applicazioni console in modo da distribuirli come processi Web.  
* [Distribuire un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in App Web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Come creare e distribuire un Progetto Applicazione Web MVC ASP.NET con un database SQL utilizzando Visual Studio, Distribuzione Web e Migrazioni Code First di Entity Framework.
* [Panoramica di Distribuzione Web per Visual Studio e ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Presentazione di base della distribuzione Web con Visual Studio. È un articolo datato, che però include informazioni ancora rilevanti, tra cui una panoramica delle opzioni di distribuzione di un database assieme all'applicazione Web e un elenco delle ulteriori attività di distribuzione che potrebbe essere necessario eseguire oppure far eseguire a Visual Studio configurandolo manualmente. Questo articolo riguarda la distribuzione in generale e non soltanto la distribuzione in App Web.
* [Distribuzione Web ASP.NET con Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Una serie di esercitazioni in 12 parti in cui è trattata una gamma più completa di attività di distribuzione rispetto alle altre risorse in questo elenco. Sono state aggiunte alcune funzionalità di distribuzione di Azure dal momento in cui è stata creata l'esercitazione. Tuttavia, le note aggiunte successivamente descrivono gli elementi mancanti.
* Articolo relativo alla [distribuzione di un sito Web ASP.NET in Azure con Visual Studio 2012 direttamente da un repository Git](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). L'articolo contiene informazioni su come distribuire un progetto Web ASP.NET con Visual Studio, utilizzando il plugin Git per eseguire il commit del codice a Git e connettere Azure al repository Git. A partire da Visual Studio 2013, il supporto per Git è integrato; pertanto, non è più necessario installare un plug-in.

##<a name="webmatrix"></a>WebMatrix

Per informazioni su come eseguire la distribuzione in App Web da WebMatrix, vedere le risorse seguenti:

* [Sviluppo e distribuzione di un'app Web con Microsoft WebMatrix](web-sites-dotnet-using-webmatrix.md). Come creare un'app ASP.NET semplice utilizzando un modello WebMatrix e distribuendola in App Web usando WebMatrix e Distribuzione Web.
* [Creazione e distribuzione di un sito Web Node.js in Azure con WebMatrix](web-sites-nodejs-use-webmatrix.md).
* [Creazione e distribuzione di un'app Web PHP-MySQL tramite WebMatrix](web-sites-php-mysql-use-webmatrix.md).
* [WebMatrix 3: Git integrato e distribuzione in Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Come utilizzare WebMatrix per eseguire la distribuzione da un repository Git di controllo del codice sorgente.

Per ulteriori informazioni, vedere le risorse seguenti:

* [Creazione di un'app Web PHP-MySQL e distribuzione tramite FTP](web-sites-php-mysql-deploy-use-ftp.md).
* [Come gestire le App Web](web-sites-manage.md#ftp-credentials). Contiene informazioni aggiuntive non incluse nell'esercitazione su PHP riguardanti la modalità di configurazione delle credenziali FTP.

##<a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server è la soluzione Microsoft locale per il controllo del codice sorgente e la collaborazione con il team. È possibile configurare TFS per l'esecuzione del recapito continuo a un'app Web.

Per ulteriori informazioni, vedere la risorsa seguente:

* [Recapito continuo per Servizi cloud in Azure](../cloud-services-dotnet-continuous-delivery.md). Questo documento è relativo a un servizio cloud di Azure, ma alcuni dei contenuti si riferiscono ad App Web.

##<a name="gitmercurial"></a>Repository Git o Mercurial locali

In Azure è possibile immettere l'URL di qualsiasi repository che utilizzi Git o Mercurial allo scopo di eseguire la distribuzione da tale posizione. Inoltre, è possibile effettuare il push direttamente dal repository Git locale a un'app Web.

Per ulteriori informazioni, vedere le risorse seguenti:

* [Pubblicazione dal controllo del codice sorgente ad App Web con Git](web-sites-publish-source-control.md). Nell'articolo viene descritto come usare Git per pubblicare direttamente dal computer locale a un'app Web; in Azure, questo metodo di pubblicazione è denominato Git locale.. In questo articolo viene inoltre illustrato come abilitare il recapito continuo dei repository Git da GitHub, CodePlex o BitBucket.
* [Pubblicazione in App Web da qualsiasi repository Git/Hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog in cui viene illustrata la funzionalità "Repository esterno" di App Web.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).
* Post sulla [distribuzione di DUE siti Web in Azure da un solo repository Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx) nel blog di Scott Hanselman.


##<a name=msbuild></a>MSBuild

Se si utilizza l'[IDE di Visual Studio](#vs) per lo sviluppo è possibile servirsi di [MSBuild](http://msbuildbook.com/) per automatizzare qualsiasi operazione nell'IDE. È possibile configurare MSBuild in modo da utilizzare [Distribuzione Web](#webdeploy) oppure [FTP/FTPS](#ftp) per copiare i file. Distribuzione Web consente inoltre di automatizzare molte altre attività relative alla distribuzione, ad esempio la distribuzione dei database.

Per ulteriori informazioni sulla distribuzione da riga di comando, vedere le risorse seguenti:

* [Distribuzione Web ASP.NET con Visual Studio: distribuzione da riga di comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Decima di una serie di esercitazioni sulla distribuzione in Azure con Visual Studio. Contiene informazioni su come utilizzare la riga di comando per eseguire la distribuzione dopo aver configurato i profili di pubblicazione in Visual Studio.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/). Libro paperback che contiene alcuni capitoli su come utilizzare MSBuild per la distribuzione.

##<a name="ftp2"></a>Script FTP

È facile creare credenziali [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) per un'app Web e usarle con gli script batch FTP.

Per ulteriori informazioni, vedere la risorsa seguente:

* [Utilizzo di script Batch FTP](http://support.microsoft.com/kb/96269).

##<a name="powershell"></a>Windows PowerShell

In [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx) è possibile eseguire funzioni di distribuzione MSBuild o FTP. In tal caso, è inoltre possibile utilizzare una raccolta di cmdlet di Windows PowerShell che facilitano la chiamata dell'API di gestione RET di Azure.

Per ulteriori informazioni, vedere la risorsa seguente:

* Capitolo dell'e-book relativo a come [automatizzare tutto e creare app per cloud reali con Azure](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything), in cui viene illustrato in che modo l'applicazione di esempio illustrata nell'e-book utilizza gli script di Windows PowerShell per creare un ambiente di testing Azure ed eseguirvi la distribuzione. Per ottenere collegamenti a ulteriori documenti su Azure PowerShell vedere la sezione relativa alle [risorse](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources).
* [Utilizzo degli script di Windows PowerShell per la pubblicazione in ambienti di sviluppo e test](http://msdn.microsoft.com/library/dn642480.aspx). Come usare gli script di distribuzione di Windows PowerShell generati da Visual Studio.

##<a name="api"></a>API di gestione .NET

È possibile scrivere il codice C# per eseguire funzioni MSBuild o FTP per la distribuzione. In tal caso, sarà possibile accedere all'API REST per la gestione per eseguire funzioni di gestione del sito.

Per ulteriori informazioni, vedere la risorsa seguente:

* Post del blog di Scott Hanselman sull'[automazione completa con le librerie di gestione di Azure e .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Contiene una presentazione dell'API di gestione .NET e collegamenti a ulteriore documentazione.

##<a name="cli"></a>Riga di comando multipiattaforma (xplat-cli)

È possibile utilizzare la riga di comando nei computer Mac o Linux per eseguire la distribuzione con FTP. In tal caso, è inoltre possibile accedere all'API REST per la gestione di Azure usando l'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli). L'interfaccia xplat-cli può essere usata anche nei computer Windows.

Per ulteriori informazioni, vedere la risorsa seguente:

* [Strumenti da riga di comando di Azure](/downloads/#cmd-line-tools). Pagina del portale su Azure.com per le informazioni sullo strumento da riga di comando.

##<a name="webdeploy"></a>Riga di comando di Distribuzione Web

[Distribuzione Web](http://www.iis.net/downloads/microsoft/web-deploy) è il software Microsoft per la distribuzione in IIS che non solo offre funzioni intelligenti di sincronizzazione dei file, ma può anche eseguire o coordinare molte altre attività relative alla distribuzione che non è possibile automatizzare quando si utilizza il trasferimento FTP. Ad esempio, con Distribuzione Web è possibile distribuire un nuovo database oppure gli aggiornamenti al database assieme all'app Web. Distribuzione Web consente inoltre di ridurre al minimo i tempi richiesti per l'aggiornamento di un sito esistente dal momento che è in grado di copiare in maniera intelligente solo i file modificati. Il supporto per Distribuzione Web è incorporato in Microsoft WebMatrix, Visual Studio, Visual Studio Online e Team Foundation Server, ma è inoltre possibile utilizzare Distribuzione Web direttamente dalla riga di comando per automatizzare la distribuzione. I comandi di Distribuzione Web sono molti potenti, ma la curva di apprendimento può rivelarsi ripida.

Per ulteriori informazioni, vedere la risorsa seguente:

* [Simple Web Apps: Deployment](http://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog di David Ebbo riguardante uno strumento che ha creato per facilitare l'uso di Distribuzione Web.
* Documentazione ufficiale sullo [strumento di distribuzione Web](http://technet.microsoft.com/library/dd568996) sul sito Microsoft TechNet. Articolo datato, ma è ancora un buon punto di partenza.
* Documentazione ufficiale relativa all'[utilizzo di Distribuzione Web](http://www.iis.net/learn/publish/using-web-deploy) sul sito Microsoft IIS.NET. Anche questo articolo è datato, ma è un buon punto di partenza.
* [StackOverflow](http://www.stackoverflow.com). La migliore fonte di informazioni aggiornate su come utilizzare Distribuzione Web dalla riga di comando.
* [Distribuzione Web ASP.NET con Visual Studio: distribuzione da riga di comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild è il motore di compilazione usato da Visual Studio e può inoltre essere usato dalla riga di comando per la distribuzione di applicazioni Web in App Web. Questa esercitazione fa parte di una serie dedicata principalmente alla distribuzione con Visual Studio.

##<a name="nextsteps"></a>Passaggi successivi

In alcuni scenari può essere necessario passare da una versione temporanea dell'app Web a una versione di produzione e viceversa. Per ulteriori informazioni, vedere [Distribuzione temporanea su App Web](web-sites-staged-publishing.md).

La definizione di un piano di backup e ripristino è una parte importante di un flusso di distribuzione. Per informazioni sulle funzionalità di backup e ripristino di App Web, vedere [Backup di App Web](web-sites-backup.md).

Per informazioni su come utilizzare il controllo degli accessi in base al ruolo di Azure al fine di gestire l'accesso alla distribuzione di App Web, vedere [Controllo degli accessi in base al ruolo e pubblicazione di App Web](http://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing).

Per informazioni su altri argomenti relativi alla distribuzione, vedere la relativa sezione in [Documentazione di App Web](/documentation/services/web-sites/).

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=54--> 