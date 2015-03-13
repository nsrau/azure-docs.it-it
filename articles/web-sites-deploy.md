<properties 
	pageTitle="Come distribuire un sito Web di Azure" 
	description="Informazioni sui metodi disponibili per la distribuzione di contenuti in un sito Web di Azure." 
	services="web-sites" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/07/2015" 
	ms.author="tdykstra"/>

#Come distribuire un sito Web di Azure

Per la distribuzione dei contenuti personali su un sito Web di Azure sono disponibili molte opzioni. Questo argomento fornisce informazioni generali su ogni opzione e i collegamenti per visualizzare altre informazioni.

* [Distribuzione da un sistema di controllo del codice sorgente ospitato nel cloud](#cloud)
	* Visual Studio Online
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
* [Uso degli strumenti da riga di comando e dell'API REST per la gestione di Azure](#commandline)
	* MSBuild
	* Script FTP
	* Windows PowerShell
	* API di gestione .NET
	* Riga di comando multipiattaforma (xpat-cli)
	* Riga di comando di Distribuzione Web


##<a name="cloud"></a>Distribuzione da un sistema di controllo del codice sorgente ospitato nel cloud

Il miglior modo per distribuire un sito Web consiste nell'impostare un [flusso di lavoro di recapito continuo](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) integrato nel proprio [sistema di controllo del codice sorgente](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control). L'automazione rende non solo più efficiente il processo di sviluppo, ma anche più gestibili e affidabili i processi di backup e ripristino. 

Qualora non sia ancora stato impostato il controllo del codice sorgente, il modo più semplice per iniziare consiste nell'usare un sistema di controllo del codice sorgente ospitato nel cloud.

###<a name="vso"></a>Visual Studio Online

[Visual Studio Online](http://www.visualstudio.com/) (in precedenza Team Foundation Service) è la soluzione Microsoft basata sul cloud per il controllo del codice sorgente e la collaborazione con il team. Il servizio è gratuito per un team composto da un massimo di cinque sviluppatori. È possibile configurare VSO per l'esecuzione del recapito continuo a un sito Web di Azure e il repository può usare [Git o TFVC](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs).

Per altre informazioni, vedere le risorse seguenti:

* [Recapito continuo in Azure mediante Visual Studio Online e TFVC](../cloud-services-continuous-delivery-use-vso/). Esercitazione dettagliata che illustra come configurare il recapito continuo da Visual Studio Online a un sito Web di Azure mediante TFVC. TFVC è l'opzione di controllo del codice sorgente centralizzato, mentre Git è l'opzione di controllo del codice sorgente distribuito.
* [Recapito continuo in Azure tramite Visual Studio Online e Git](../cloud-services-continuous-delivery-use-vso-git/). Simile all'esercitazione precedente, ma usa Git anziché TFVC.

###<a name="git"></a>Siti Web di repository con Git

[Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) è un popolare sistema di controllo del codice sorgente distribuito. Azure offre funzionalità incorporate che facilitano l'automazione della distribuzione in un sito Web di Azure a partire da noti siti di repository basati sul Web in cui sono archiviati i repository Git, tra cui [GitHub](http://www.github.com), [CodePlex](http://www.codeplex.com/) e [BitBucket](https://bitbucket.org/). Uno dei vantaggi comportati dall'utilizzo di Git per la distribuzione consiste nel fatto che, semmai fosse necessario, è relativamente facile eseguire il rollback a una distribuzione precedente. 

Per altre informazioni, vedere le risorse seguenti:

* [Pubblicazione in Siti Web di Azure con Git ](../web-sites-publish-source-control/). Come usare Git per pubblicare direttamente dal computer locale in un sito Web di Azure. In Azure questo metodo di pubblicazione è chiamato Local Git. In questo articolo viene inoltre illustrato come abilitare il recapito continuo dei repository Git da GitHub, CodePlex o BitBucket.
* [Distribuzione in siti Web con GitHub mediante Kudu](/it-it/documentation/videos/deploying-to-azure-from-github/). Video di Scott Hanselman e David Ebbo che mostra come distribuire un sito Web direttamente da GitHub in un sito Web di Azure.
* [Distribuzione tramite pulsante Azure per siti Web di Azure](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Blog relativo a un metodo per attivare la distribuzione da un repository Git.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="mercurial"></a>Siti Web di repository con Mercurial

Se il sistema di controllo del codice sorgente in uso è [Mercurial](http://mercurial.selenic.com/) e il repository è archiviato in [CodePlex](http://www.codeplex.com/) o [BitBucket](https://bitbucket.org/), è possibile usare le funzionalità incorporate in Siti Web di Azure per distribuire automaticamente il contenuto.

Per informazioni su come eseguire la distribuzione con Mercurial vedere le risorse seguenti:

* [Pubblicazione in Siti Web di Azure con Git ](../web-sites-publish-source-control/). Benché questa esercitazione contenga informazioni su come pubblicare un repository Git, il processo per i repository Mercurial ospitati in CodePlex o BitBucket è simile.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).

###<a name="dropbox"></a>Dropbox

[Dropbox](https://www.dropbox.com/) non è un sistema di controllo del codice sorgente, ma se si archivia il proprio codice sorgente in Dropbox, è possibile automatizzarne la distribuzione dal proprio account Dropbox.

* [Distribuzione in siti Web di Azure da Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Come usare il portale di gestione di Azure per configurare la distribuzione di Dropbox.
* [Distribuzione di Dropbox in siti Web di Azure](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). Questo video illustra il processo di connessione di una cartella di Dropbox a un sito Web di Azure. Il video mostra con quale rapidità è possibile rendere operativo un sito Web o curarne la manutenzione per mezzo di una semplice distribuzione a trascinamento.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).







##<a name="ide"></a>Distribuzione da un IDE

[Visual Studio](http://www.visualstudio.com/) e [WebMatrix](http://www.microsoft.com/web/webmatrix/) sono ambienti di sviluppo integrato IDE (Integrated Development Environment) di Microsoft che è possibile usare per lo sviluppo Web. Entrambi offrono funzionalità incorporate che facilitano la distribuzione in Siti Web di Azure.  Entrambi sono in grado di usare [Distribuzione Web](http://www.iis.net/downloads/microsoft/web-deploy) per automatizzare ulteriori attività di distribuzione correlate, ad esempio la distribuzione di database e le modifiche alla stringa di connessione. Entrambi possono inoltre eseguire la distribuzione mediante [FTP o FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol)). 

WebMatrix è rapido da installare e facile da usare, ma Visual Studio offre molte più funzionalità per gestire Siti Web di Azure. Dall'IDE di Visual Studio è possibile creare, arrestare, avviare ed eliminare i siti Web di Azure, visualizzare i log creati in tempo reale, eseguire il debug in remoto e molto altro. Visual Studio si integra inoltre con i sistemi di controllo del codice sorgente come [Visual Studio Online](#vso), [Team Foundation Server](#tfs) e [repository Git](#git).

###<a name="vs"></a>Visual Studio

Per informazioni su come eseguire la distribuzione in Siti Web di Azure da Visual Studio vedere le risorse seguenti:

* [Introduzione ad Azure e ASP.NET](../web-sites-dotnet-get-started/). Come creare e distribuire un semplice progetto Web MVC ASP.NET usando Visual Studio e Distribuzione Web.
* [Come distribuire i processi Web di Azure ai siti Web di Azure](../websites-dotnet-deploy-webjobs/). Come configurare i progetti di applicazioni console in modo da distribuirli come processi Web.  
* [Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure](../web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/). Come creare e distribuire un progetto Web MVC ASP.NET con un database SQL usando Visual Studio, Distribuzione Web e Migrazioni Code First di Entity Framework.
* [Informazioni generali sulla distribuzione Web per Visual Studio e ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Presentazione di base della distribuzione Web con Visual Studio. È un articolo datato, che però include informazioni ancora rilevanti, tra cui una panoramica delle opzioni di distribuzione di un database assieme all'applicazione Web e un elenco delle ulteriori attività di distribuzione che potrebbe essere necessario eseguire oppure far eseguire a Visual Studio configurandolo manualmente. Questo argomento riguarda la distribuzione in generale, non solo la distribuzione in Siti Web di Azure.
* [Distribuzione Web ASP.NET con Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Una serie di esercitazioni in 12 parti in cui è trattata una gamma più completa di attività di distribuzione rispetto alle altre risorse in questo elenco. Dopo che l'esercitazione è stata scritta, sono state inserite alcune funzionalità di distribuzione di Azure, ma le note aggiunte successivamente illustrano gli elementi mancanti. 
* [Distribuzione di un sito Web ASP.NET in Azure con Visual Studio 2012 direttamente da un repository Git](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). L'articolo contiene informazioni su come distribuire un progetto Web ASP.NET con Visual Studio, usando il plug-in Git per eseguire il commit del codice a Git e connettere Azure al repository Git. A partire da Visual Studio 2013, il supporto di Git è incorporato e non richiede l'installazione di un plug-in.

###<a name="webmatrix"></a>WebMatrix

Per informazioni su come eseguire la distribuzione in Siti Web di Azure da WebMatrix, vedere le risorse seguenti:

* [Sviluppo e distribuzione di un sito Web con Microsoft WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-dotnet-using-webmatrix/). Come creare un semplice sito Web di ASP.NET usando un modello di WebMatrix e distribuendolo in un sito Web di Azure tramite WebMatrix e Distribuzione Web.
* [Creazione e distribuzione di un sito Web Node.js in Azure con WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-nodejs-use-webmatrix/).
* [Creazione e distribuzione di un sito Web di Azure PHP-MySQL tramite WebMatrix](http://azure.microsoft.com/documentation/articles/web-sites-php-mysql-use-webmatrix/).
* [WebMatrix 3: Git integrato e distribuzione in Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Come usare WebMatrix per eseguire la distribuzione da un repository Git di controllo del codice sorgente.

##<a name="ftp"></a>Distribuzione mediante un'utilità FTP

Indipendentemente dall'IDE in uso, è possibile distribuire il contenuto nel sito usando [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) per copiare i file. È facile creare credenziali FTP per un sito Web di Azure e usarle in qualsiasi applicazione che funzioni con il protocollo FTP, inclusi i browser come Internet Explorer e le utilità gratuite complete come [FileZilla](https://filezilla-project.org/).  Siti Web di Azure supporta inoltre il protocollo FTPS più sicuro. 

Anche se è semplice copiare i file del proprio sito Web in Azure mediante le utilità FTP, queste non gestiscono né coordinano automaticamente le attività di distribuzione correlate, quali la distribuzione di un database o la modifica delle stringhe di connessione. Inoltre, molti strumenti FTP non confrontano i file di origine e di destinazione in modo da saltare la copia dei file che non hanno subito modifiche. Per i siti di grandi dimensioni, l'operazione di copia di tutti i file può causare tempi di distribuzione prolungati anche per gli aggiornamenti di entità minore.

Per altre informazioni, vedere le risorse seguenti:

* [Creazione di un sito Web di Azure PHP-MySQL e distribuzione tramite FTP](../web-sites-php-mysql-deploy-use-ftp/). 
* [Come gestire i siti Web](../web-sites-manage/#ftp-credentials). Contiene informazioni aggiuntive non incluse nell'esercitazione su PHP riguardanti la modalità di configurazione delle credenziali FTP. 


##<a name="onpremises"></a>Distribuzione da un sistema di controllo del codice sorgente locale

Se si usa TFS, Git o Mercurial in un repository locale (non ospitato sul cloud) è possibile eseguire la distribuzione direttamente dal repository in Siti Web di Azure.

###<a name="tfs"></a>Team Foundation Server (TFS)

Team Foundation Server è la soluzione Microsoft locale per il controllo del codice sorgente e la collaborazione con il team. È possibile configurare TFS per l'esecuzione del recapito continuo a un sito Web di Azure.

Per ulteriori informazioni, vedere la seguente risorsa:

* [Recapito continuo per Servizi cloud in Azure](../cloud-services-dotnet-continuous-delivery/). Questo documento è relativo a un servizio cloud di Azure, ma alcuni dei contenuti si riferiscono a Siti Web di Azure.

###<a name="gitmercurial"></a>Repository Git o Mercurial locali

In Azure è possibile immettere l'URL di qualsiasi repository che utilizzi Git o Mercurial allo scopo di eseguire la distribuzione da tale posizione. È inoltre possibile effettuare il push direttamente da un repository Git in un sito Web di Azure. 

Per altre informazioni, vedere le risorse seguenti:

* [Pubblicazione in Siti Web di Azure con Git ](../web-sites-publish-source-control/). Come usare Git per pubblicare direttamente dal computer locale in un sito Web di Azure. In Azure questo metodo di pubblicazione è chiamato Local Git. In questo articolo viene inoltre illustrato come abilitare il recapito continuo dei repository Git da GitHub, CodePlex o BitBucket.
* [Pubblicazione in Siti Web di Azure da qualsiasi repository Git/HG](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog che illustra la funzionalità "Repository esterno" in Siti Web di Azure.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azuregit).
* [Distribuzione di DUE siti Web in Azure da un solo repository Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx). nel blog di Scott Hanselman.











##<a name="commandline"></a>Uso degli strumenti da riga di comando e dell'API REST per la gestione di Azure

È sempre buona norma automatizzare il flusso di lavoro di sviluppo, ma qualora non fosse possibile farlo direttamente nel proprio sistema di controllo del codice sorgente è possibile configurarlo manualmente mediante gli strumenti da riga di comando. Ciò implica in genere l'utilizzo di più strumenti o framework, in quanto la distribuzione spesso richiede l'esecuzione di funzioni di gestione del sito oltre alla copia dei contenuti.

Azure semplifica le attività di gestione del sito che è necessario portare a termine per la distribuzione fornendo un'API REST per la gestione e diversi framework che semplificano l'elaborazione con l'API.

###<a name=msbuild></a>MSBuild

Se si usa l'[IDE di Visual Studio](#vs) per lo sviluppo, è possibile servirsi di  [MSBuild](http://msbuildbook.com/) per automatizzare qualsiasi operazione nell'IDE. È possibile configurare MSBuild in modo da usare [Distribuzione Web](#webdeploy) oppure [FTP/FTPS](#ftp) per copiare i file. Distribuzione Web consente inoltre di automatizzare molte altre attività relative alla distribuzione, ad esempio la distribuzione dei database.

Per ulteriori informazioni sulla distribuzione da riga di comando, vedere le risorse seguenti:

* [Distribuzione Web ASP.NET con Visual Studio: Distribuzione da riga di comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Decima di una serie di esercitazioni sulla distribuzione in Azure con Visual Studio. Contiene informazioni su come utilizzare la riga di comando per eseguire la distribuzione dopo aver configurato i profili di pubblicazione in Visual Studio.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/). Libro paperback che contiene alcuni capitoli su come utilizzare MSBuild per la distribuzione.

###<a name="ftp2"></a>Script FTP

È facile creare credenziali [FTP/FTPS](http://en.wikipedia.org/wiki/File_Transfer_Protocol) per un sito Web di Azure per poi usarle con gli script batch FTP.

Per ulteriori informazioni, vedere la seguente risorsa:

* [Utilizzo di script batch FTP](http://support.microsoft.com/kb/96269).

###<a name="powershell"></a>Windows PowerShell

In [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx) è possibile eseguire funzioni di distribuzione MSBuild o FTP. In tal caso, è inoltre possibile usare una raccolta di cmdlet di Windows PowerShell che facilitano la chiamata dell'API di gestione REST di Azure.

Per ulteriori informazioni, vedere la seguente risorsa:

* [Capitolo relativo a come automatizzare tutto e creare app per cloud reali con Azure](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything). Capitolo che spiega in che modo l'applicazione di esempio illustrata nell'e-book usa gli script di Windows PowerShell per creare un ambiente di testing Azure ed eseguirvi la distribuzione. Per ottenere collegamenti ad altra documentazione su Azure PowerShell, vedere la sezione relativa alle [risorse](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources).
* [Uso degli script di Windows PowerShell per la pubblicazione in ambienti di sviluppo e test](http://msdn.microsoft.com/library/dn642480.aspx). Come usare gli script di distribuzione di Windows PowerShell generati da Visual Studio.

###<a name="api"></a>API di gestione .NET

È possibile scrivere il codice C# per eseguire funzioni MSBuild o FTP per la distribuzione. In tal caso, sarà possibile accedere all'API REST per la gestione per eseguire funzioni di gestione del sito.

Per ulteriori informazioni, vedere la seguente risorsa:

* [Post sull'automazione completa con le librerie di gestione di Azure e .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Contiene una presentazione dell'API di gestione .NET e collegamenti a ulteriore documentazione.

###<a name="cli"></a>Riga di comando multipiattaforma (xpat-cli)

È possibile usare la riga di comando nei computer Mac o Linux per eseguire la distribuzione con FTP. In tal caso, è inoltre possibile accedere all'API REST per la gestione di Azure usando l'interfaccia della riga di comando multipiattaforma di Azure (xplat-cli). L'interfaccia xpat-cli può anche essere usata nei computer Windows.

Per ulteriori informazioni, vedere la seguente risorsa:

* [Strumenti da riga di comando](/it-it/downloads/#cmd-line-tools). Pagina del portale su WindowsAzure.com per le informazioni sullo strumento da riga di comando.

###<a name="webdeploy"></a>Riga di comando di Distribuzione Web

[Distribuzione Web](http://www.iis.net/downloads/microsoft/web-deploy) è il software Microsoft per la distribuzione in IIS che non solo offre funzionalità intelligenti di sincronizzazione dei file, ma può anche eseguire o coordinare molte altre attività relative alla distribuzione che non è possibile automatizzare quando si usa FTP. Ad esempio, con Distribuzione Web è possibile distribuire un nuovo database oppure gli aggiornamenti al database insieme al sito Web. Distribuzione Web consente inoltre di ridurre al minimo i tempi richiesti per l'aggiornamento di un sito esistente dal momento che è in grado di copiare in maniera intelligente solo i file modificati. Il supporto per Distribuzione Web è incorporato in Microsoft WebMatrix, Visual Studio, Visual Studio Online e Team Foundation Server, ma è inoltre possibile utilizzare Distribuzione Web direttamente dalla riga di comando per automatizzare la distribuzione. I comandi di Distribuzione Web sono molti potenti, ma la curva di apprendimento può rivelarsi ripida.

Per ulteriori informazioni, vedere la seguente risorsa:

* [Blog sui siti Web di Azure semplici: distribuzione](http://azure.microsoft.com/blog/2014/07/28/simple-azure-websites-deployment/). Blog di David Ebbo relativo a uno strumento che ha creato per semplificare l'uso di Distribuzione Web.
* [Documentazione ufficiale sullo strumento di distribuzione Web](http://technet.microsoft.com/library/dd568996) sul sito Microsoft TechNet. Articolo datato, ma è ancora un buon punto di partenza.
* [Documentazione ufficiale sull'uso di Distribuzione Web](http://www.iis.net/learn/publish/using-web-deploy) sul sito Microsoft IIS.NET. Anche questo articolo è datato, ma è un buon punto di partenza.
* [StackOverflow](http://www.stackoverflow.com). La migliore fonte di informazioni aggiornate su come utilizzare Distribuzione Web dalla riga di comando.
* [Distribuzione Web ASP.NET con Visual Studio: Distribuzione da riga di comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). MSBuild è il motore di compilazione usato da Visual Studio e può inoltre essere usato dalla riga di comando per la distribuzione di applicazioni Web in Siti Web di Azure. Questa esercitazione fa parte di una serie dedicata principalmente alla distribuzione con Visual Studio.

##<a name="nextsteps"></a>Passaggi successivi

In alcuni scenari può essere necessario passare da una versione temporanea del sito Web a una versione di produzione e viceversa. È possibile eseguire questa operazione con la funzionalità di distribuzione temporanea di Siti Web di Azure. Per altre informazioni, vedere [Distribuzione temporanea su Siti Web di Microsoft Azure](../web-sites-staged-publishing/).

La definizione di un piano di backup e ripristino è una parte importante di un flusso di distribuzione. Per informazioni sulla funzionalità di backup e ripristino di Siti Web di Azure, vedere [Backup di Siti Web di Azure](../web-sites-backup/).  

Per informazioni su come usare il controllo degli accessi in base al ruolo di Azure per gestire l'accesso alla distribuzione dei siti Web, vedere l'articolo relativo a questo tipo di [controllo e pubblicazione dei siti Web di Azure](http://azure.microsoft.com/blog/2015/01/05/rbac-and-azure-websites-publishing).

Per altre informazioni sulla distribuzione, vedere la sezione specifica nella [documentazione di Siti Web di Azure](/it-it/documentation/services/web-sites/).


<!--HONumber=42-->
