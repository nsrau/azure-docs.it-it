<properties pageTitle="Come distribuire un sito Web di Azure" metaKeywords="Azure deploy publish web site" description="Learn what methods are available for deploying content to an Azure Website." metaCanonical="" services="web-sites" documentationCenter="" title="How to Deploy an Azure Website" authors="tdykstra" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/02/2014" ms.author="tdykstra" />

#Come distribuire un sito Web di Azure

Per la distribuzione dei contenuti personali su un sito Web di Azure sono disponibili molte opzioni. In questo argomento viene presentata una breve panoramica di ogni opzione e vengono forniti i collegamenti per visualizzare altre informazioni.

* [Distribuzione da un sistema di controllo del codice sorgente ospitato nel cloud](#cloud)
	* Visual Studio Online (VSO)
	* Siti Web di repository con Git
	* Siti Web di repository con Mercurial
	* Dropbox
* [Distribuzione da un IDE](#ide)
	* Visual Studio
	* WebMatrix
* [Distribuzione mediante un'utilità FTP](#ftp)
* [Distribuzione da un sistema di controllo del codice sorgente locale](#onpremises)
	* Team Foundation Server (TFS)
	* Repository Git o Mercurial locali
* [Uso degli strumenti da riga di comando e dell'API REST di gestione di Azure](#commandline)
	* MSBuild
	* Script FTP
	* Windows PowerShell
	* API di gestione .NET
	* Riga di comando multipiattaforma (xpat-cli)
	* Riga di comando di Distribuzione Web


##<a name="cloud"></a>Distribuzione da un sistema di controllo del codice sorgente ospitato nel cloud

Il miglior modo per distribuire un sito Web consiste nell'impostare un [flusso di lavoro di recapito continuo](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) integrato nel proprio [sistema di controllo del codice sorgente](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control). L'automazione rende non solo più efficiente il processo di sviluppo, ma anche più gestibili e affidabili i processi di backup e ripristino. 

Qualora non sia ancora stato impostato il controllo del codice sorgente, il modo più semplice per iniziare consiste nell'usare un sistema di controllo del codice sorgente ospitato sul cloud.

###<a name="vso"></a>Visual Studio Online (VSO)

[Visual Studio Online](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) (in precedenza Team Foundation Service) è la soluzione Microsoft basata sul cloud per il controllo del codice sorgente e la collaborazione in team. Il servizio è gratuito per un team composto da un massimo di cinque sviluppatori. È possibile configurare VSO per l'esecuzione del recapito continuo a un sito Web di Azure e il repository può usare [Git o TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs).

Per altre informazioni, vedere le risorse seguenti:

* [Recapito continuo in Azure mediante VSO e TFVC](http://www.visualstudio.com/it-it/learn/continuous-delivery-in-vs). Breve esercitazione dettagliata in cui viene illustrato come configurare il recapito continuo da VSO su un sito Web di Azure mediante TFVC. TFVC è l'opzione di controllo del codice sorgente in VSO, mentre Git è l'opzione di controllo del codice distribuito.
* [Recapito continuo in Azure mediante VSO e TFVC](/it-it/documentation/articles/cloud-services-continuous-delivery-use-vso/). Simile all'esercitazione precedente, ma include solo i passaggi per l'iscrizione a un account VSO e l'archiviazione di un progetto nel controllo del codice sorgente.
* [Recapito continuo in Azure mediante Visual Studio Online e Git](http://azure.microsoft.com/it-it/documentation/articles/cloud-services-continuous-delivery-use-vso-git/). Simile all'esercitazione precedente, ma usa Git anziché TFVC.

###<a name="git"></a>Siti Web di repository con Git

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) è un diffuso sistema di controllo del codice sorgente distribuito. Azure offre funzionalità incorporate che facilitano l'automazione della distribuzione in un sito Web di Azure a partire dai più noti siti di repository basati su Web in cui sono archiviati i repository Git, tra cui [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) e [BitBucket](https://bitbucket.org/). Uno dei vantaggi comportati dall'utilizzo di Git per la distribuzione consiste nel fatto che, semmai fosse necessario, è relativamente facile eseguire il rollback a una distribuzione precedente. 

Per altre informazioni, vedere le risorse seguenti:

* [Pubblicazione da controllo del codice sorgente in Siti Web di Azure](/it-it/documentation/articles/web-sites-publish-source-control/). Come usare Git per pubblicare direttamente dal computer locale in un sito Web di Azure. In Azure questo metodo di pubblicazione è chiamato Local Git. In questo articolo viene inoltre illustrato come abilitare il recapito continuo dei repository Git da GitHub, CodePlex o BitBucket.
* [Distribuzione in siti Web con GitHub mediante Kudu](/it-it/documentation/videos/deploying-to-azure-from-github/). Video di Scott Hanselman e David Ebbo che mostra come distribuire un sito Web direttamente da GitHub in un sito Web di Azure.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="mercurial"></a>Siti Web di repository con Mercurial

Se il sistema di controllo del codice sorgente in uso è [Mercurial](http://mercurial.selenic.com/) e il repository è archiviato in [CodePlex](http://www.codeplex.com/) o [BitBucket](https://bitbucket.org/), è possibile usare le funzionalità incorporate in Siti Web di Azure per distribuire automaticamente il contenuto.

Per informazioni su come eseguire la distribuzione con Mercurial vedere le risorse seguenti:

* [Pubblicazione da controllo del codice sorgente in Siti Web di Azure](/it-it/documentation/articles/web-sites-publish-source-control/). Benché questa esercitazione contenga informazioni su come pubblicare un repository Git, il processo per i repository Mercurial ospitati in CodePlex o BitBucket è simile.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="dropbox"></a>Dropbox

[Dropbox](https://www.dropbox.com/) non è un sistema di controllo del codice sorgente, ma se si archivia il proprio codice sorgente in Dropbox è possibile automatizzarne la distribuzione dal proprio account Dropbox.

* [Distribuzione in Microsoft Azure con Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Come usare il portale di gestione di Azure per configurare la distribuzione tramite Dropbox.
* [Dropbox e Siti Web di Azure](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). Questo video illustra il processo di connessione di una cartella di Dropbox a un sito Web di Azure. Il video mostra con quale rapidità è possibile rendere operativo un sito Web o curarne la manutenzione per mezzo di una semplice distribuzione a trascinamento.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).







##<a name="ide"></a>Distribuzione da un IDE

[Visual Studio](http://www.visualstudio.com/it-it/downloads/download-visual-studio-vs.aspx) e [WebMatrix](http://www.microsoft.com/web/webmatrix/) sono ambienti di sviluppo integrato IDE (Integrated Development Environment) di Microsoft che è possibile usare per lo sviluppo Web. Entrambi offrono funzionalità incorporate che facilitano la distribuzione in Siti Web di Azure.  Entrambi possono usare [Distribuzione Web](http://www.iis.net/downloads/microsoft/web-deploy) per automatizzare altre attività correlate alla distribuzione, ad esempio la distribuzione di database e le modifiche alla stringa di connessione. Entrambi possono inoltre eseguire la distribuzione mediante [FTP o FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol). 

WebMatrix è rapido da installare e facile da usare, ma Visual Studio offre molte più funzionalità per gestire Siti Web di Azure. Dall'IDE di Visual Studio è possibile creare, arrestare, avviare ed eliminare i siti Web di Azure, visualizzare i log creati in tempo reale, eseguire il debug in remoto e molto altro. Visual Studio si integra inoltre con i sistemi di controllo del codice sorgente come [Visual Studio Online](#vso), [Team Foundation Server](#tfs) e [Repository Git](#git).

###<a name="vs"></a>Visual Studio

Per informazioni su come eseguire la distribuzione in Siti Web di Azure da Visual Studio vedere le risorse seguenti:

* [Introduzione ad Azure e ASP.NET](/it-it/develop/net/tutorials/get-started/). Come creare e distribuire un semplice Progetto Applicazione Web MVC ASP.NET usando Visual Studio e Distribuzione Web.
* [Come distribuire i processi Web di Azure in Siti Web di Azure](/it-it/documentation/articles/websites-dotnet-deploy-webjobs/). Come configurare i progetti di applicazioni console in modo da distribuirli come processi Web.  
* [Distribuire un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure](/it-it/develop/net/tutorials/web-site-with-sql-database/). Come creare e distribuire un Progetto Applicazione Web MVC ASP.NET con un database SQL usando Visual Studio, Distribuzione Web e Migrazioni Code First di Entity Framework.
* [Panoramica della distribuzione di progetti di applicazioni Web per Visual Studio e ASP.NET](http://msdn.microsoft.com/it-it/library/dd394698.aspx). Presentazione di base della distribuzione Web con Visual Studio. È un articolo datato, che però include informazioni ancora rilevanti, tra cui una panoramica delle opzioni di distribuzione di un database assieme all'applicazione Web e un elenco delle ulteriori attività di distribuzione che potrebbe essere necessario eseguire oppure far eseguire a Visual Studio configurandolo manualmente. Questo argomento riguarda la distribuzione in generale, non solo la distribuzione in Siti Web di Azure.
* [Distribuzione Web ASP.NET con Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Una serie di esercitazioni in 12 parti in cui è trattata una gamma più completa di attività di distribuzione rispetto alle altre risorse in questo elenco. 
* Articolo relativo alla [distribuzione di un sito Web ASP.NET in Azure con Visual Studio 2012 direttamente da un repository Git](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). L'articolo contiene informazioni su come distribuire un progetto Web ASP.NET con Visual Studio, usando il plugin Git per eseguire il commit del codice a Git e connettere Azure al repository Git.

###<a name="webmatrix"></a>WebMatrix

Per informazioni su come eseguire la distribuzione in Siti Web di Azure da WebMatrix, vedere le risorse seguenti:

* [Sviluppare e distribuire un sito Web con Microsoft WebMatrix](http://www.windowsazure.com/it-it/documentation/articles/web-sites-dotnet-using-webmatrix/). Come creare un semplice sito Web di ASP.NET usando un modello di WebMatrix e distribuendolo in un sito Web di Azure tramite WebMatrix e Distribuzione Web.
* [Creare e distribuire un sito Web Node.js in Azure con WebMatrix](http://www.windowsazure.com/it-it/documentation/articles/web-sites-nodejs-use-webmatrix/).
* [Creare e distribuire un sito Web di Azure PHP-MySQL tramite WebMatrix](http://www.windowsazure.com/it-it/documentation/articles/web-sites-php-mysql-use-webmatrix/).
* [WebMatrix 3: Git integrato e distribuzione in Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Come usare WebMatrix per eseguire la distribuzione da un repository Git di controllo del codice sorgente.

##<a name="ftp"></a>Distribuzione mediante un'utilità FTP

Indipendentemente dall'IDE in uso, è possibile distribuire il contenuto nel sito usando [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) per copiare i file. È possibile creare facilmente credenziali FTP per un sito Web di Azure e usarle in qualsiasi applicazione che supporti il protocollo FTP, inclusi i browser come Internet Explorer e utilità gratuite complete come [FileZilla](https://filezilla-project.org/).  Siti Web di Azure supporta inoltre il protocollo FTPS più sicuro. 

Anche se è semplice copiare i file del proprio sito Web in Azure mediante le utilità FTP, queste non gestiscono né coordinano automaticamente le attività di distribuzione correlate, quali la distribuzione di un database o la modifica delle stringhe di connessione. Inoltre, molti strumenti FTP non confrontano i file di origine e di destinazione in modo da saltare la copia dei file che non hanno subito modifiche. Per i siti di grandi dimensioni, l'operazione di copia di tutti i file può causare tempi di distribuzione prolungati anche per gli aggiornamenti di entità minore.

Per altre informazioni, vedere le risorse seguenti:

* [Creare un sito Web di Azure PHP-MySQL e distribuirlo tramite FTP](/it-it/documentation/articles/web-sites-php-mysql-deploy-use-ftp/). 
* [Come gestire i siti Web](http://www.windowsazure.com/it-it/documentation/articles/web-sites-manage#ftp-credentials). Contiene informazioni aggiuntive non incluse nell'esercitazione su PHP riguardanti la modalità di configurazione delle credenziali FTP. 


##<a name="onpremises"></a>Distribuzione da un sistema di controllo del codice sorgente locale

Se si usa TFS, Git o Mercurial in un repository locale (non ospitato sul cloud) è possibile eseguire la distribuzione direttamente dal repository in Siti Web di Azure.

###<a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server è la soluzione Microsoft locale per il controllo del codice sorgente e la collaborazione con il team. È possibile configurare TFS per l'esecuzione del recapito continuo a un sito Web di Azure.

Per altre informazioni, vedere la risorsa seguente:

* [Recapito continuo per Servizi cloud in Azure](/it-it/develop/net/common-tasks/continuous-delivery/). Questo documento è relativo a un servizio cloud di Azure, ma alcuni dei contenuti si riferiscono a Siti Web di Azure.

###<a name="gitmercurial"></a>Repository Git o Mercurial locali

In Azure è possibile immettere l'URL di qualsiasi repository che utilizzi Git o Mercurial allo scopo di eseguire la distribuzione da tale posizione. È inoltre possibile effettuare il push direttamente da un repository Git in un sito Web di Azure. 

Per altre informazioni, vedere le risorse seguenti:

* [Pubblicazione da controllo del codice sorgente in Siti Web di Azure](/it-it/documentation/articles/web-sites-publish-source-control/). Come usare Git per pubblicare direttamente dal computer locale in un sito Web di Azure. In Azure questo metodo di pubblicazione è chiamato Local Git. In questo articolo viene inoltre illustrato come abilitare il recapito continuo dei repository Git da GitHub, CodePlex o BitBucket.
* [Post sulla pubblicazione in Siti Web di Azure da qualsiasi repository Git/HG](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). nel blog di David Ebbo, in cui è illustrata la funzione di "Repository esterno" in Siti Web di Azure.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).
* Post sulla [distribuzione di DUE siti Web in Azure da un solo repository Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). nel blog di Scott Hanselman.











##<a name="commandline"></a> Utilizzo degli strumenti da riga di comando e dell'API REST per la gestione di Azure

È sempre buona norma automatizzare il flusso di lavoro di sviluppo, ma qualora non fosse possibile farlo direttamente nel proprio sistema di controllo del codice sorgente è possibile configurarlo manualmente mediante gli strumenti da riga di comando. Ciò implica in genere l'utilizzo di più strumenti o framework, in quanto la distribuzione spesso richiede l'esecuzione di funzioni di gestione del sito oltre alla copia dei contenuti.

Azure semplifica le attività di gestione del sito che è necessario portare a termine per la distribuzione fornendo un'API REST per la gestione e diversi framework che semplificano l'elaborazione con l'API.

###<a name=msbuild></a>MSBuild

If you use the [Visual Studio IDE](#vs) for development, you can use [MSBuild](http://msbuildbook.com/) to automate anything you can do in your IDE. You can configure MSBuild to use either [Web Deploy](#webdeploy) or [FTP/FTPS](#ftp) to copy files. Web Deploy can also automate many other deployment-related tasks, such as deploying databases.

For more information about command-line deployment using MSBuild, see the following resources:

* [ASP.NET Web Deployment using Visual Studio: Command Line Deployment](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Tenth in a series of tutorials about deployment to Azure using Visual Studio. Shows how to use the command line to deploy after setting up publish profiles in Visual Studio.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/). Hard-copy book that includes chapters on how to use MSBuild for deployment.

###<a name="ftp2"></a>Script FTP

È possibile creare facilmente credenziali [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) per un sito Web di Azure per poi usarle con gli script batch FTP.

Per altre informazioni, vedere la risorsa seguente:

* [Uso di script batch FTP](http://support.microsoft.com/kb/96269).

###<a name="powershell"></a>Windows PowerShell

In [Windows PowerShell](http://msdn.microsoft.com/it-it/library/dd835506.aspx) è possibile eseguire funzioni di distribuzione MSBuild o FTP. In tal caso, è inoltre possibile usare una raccolta di cmdlet di Windows PowerShell che facilitano la chiamata dell'API di gestione RET di Azure.

Per altre informazioni, vedere la risorsa seguente:

* Capitolo dell'e-book relativo a come [automatizzare tutto e creare app per cloud reali con Azure](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything), in cui viene illustrato in che modo l'applicazione di esempio illustrata nell'e-book usa gli script di Windows PowerShell per creare un ambiente di testing Azure ed eseguirvi la distribuzione. Per collegamenti ad altri documenti su Azure PowerShell vedere la sezione relativa alle [risorse](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources).

###<a name="api"></a>API di gestione .NET

È possibile scrivere il codice C# per eseguire funzioni MSBuild o FTP per la distribuzione. In tal caso, sarà possibile accedere all'API REST per la gestione per eseguire funzioni di gestione del sito.

Per altre informazioni, vedere la risorsa seguente:

* Post del blog di Scott Hanselman sull'[automazione completa con le librerie di gestione di Azure e .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Contiene una presentazione dell'API di gestione .NET e collegamenti a ulteriore documentazione.

###<a name="cli"></a>Riga di comando multipiattaforma (xpat-cli)

È possibile usare la riga di comando nei computer Mac o Linux per eseguire la distribuzione con FTP. In tal caso, è inoltre possibile accedere all'API REST per la gestione di Azure usando l'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli). L'interfaccia xpat-cli può altresì essere usata nei computer Windows.

Per altre informazioni, vedere la risorsa seguente:

* [Strumenti da riga di comando di Azure](/it-it/downloads/#cmd-line-tools). Portal page in WindowsAzure.com for command line tool information.

###<a name="webdeploy"></a>Web Deploy command line

[Web Deploy](http://www.iis.net/downloads/microsoft/web-deploy) is Microsoft software for deployment to IIS that not only provides intelligent file sync features but also can perform or coordinate many other deployment-related tasks that can't be automated when you use FTP. For example, Web Deploy can deploy a new database or database updates along with your website. Web Deploy can also minimize the time required to update an existing site since it can intelligently copy only changed files. Microsoft WebMatrix, Visual Studio, Visual Studio Online, and Team Foundation Server have support for Web Deploy built-in, but you can also use Web Deploy directly from the command line to automate deployment. Web Deploy commands are very powerful but the learning curve can be steep.

For more information, see the following resource:

* [Web Deployment Tool](http://technet.microsoft.com/it-it/library/dd568996). Official documentation on the Microsoft TechNet site. Dated but still a good place to start.
* [Using Web Deploy](http://www.iis.net/learn/publish/using-web-deploy). Official documentation on the Microsoft IIS.NET site. Also dated but a good place to start.
* [StackOverflow](http://www.stackoverflow.com). The best place to go for more current information about how to use Web Deploy from the command line.
* [ASP.NET Web Deployment using Visual Studio: Command Line Deployment](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild is the build engine used by Visual Studio, and it can also be used from the command line to deploy web applications to Azure Websites. This tutorial is part of a series that is mainly about Visual Studio deployment.

##<a name="nextsteps"></a>Next Steps

In some scenarios you might want to be able to easily switch back and forth between a staging and a production version of your website. You can do this with the Azure Websites staged deployment feature. For more information, see [Staged Deployment on Microsoft Azure Web Sites](/it-it/documentation/articles/web-sites-staged-publishing/).

La definizione di un piano di backup e ripristino è una parte importante di un flusso di distribuzione. Per informazioni sulla funzionalità di backup e ripristino di Siti Web di Azure, vedere [Backup di Siti Web di Azure].(/it-it/documentation/articles/web-sites-backup/).  

Per altre informazioni sulla distribuzione, vedere la sezione specifica nella [documentazione di Siti Web di Azure](/it-it/documentation/services/web-sites/).
