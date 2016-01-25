<properties
	pageTitle="Distribuire l'app nel servizio app di Azure"
	description="Informazioni su come distribuire l'app nel servizio app di Azure"
	services="app-service"
	documentationCenter=""
	authors="cephalin"
	manager="wpickett"
	editor="mollybos"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/07/2016"
	ms.author="cephalin;tdykstra"/>
    
# Distribuire l'app nel servizio app di Azure

Questo articolo aiuta a determinare l'opzione migliore per distribuire i file per l'app Web, il back-end dell'app per dispositivi mobili o l'app per le API nel [servizio app di Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Include quindi i collegamenti agli articoli e ai blog appropriati con istruzioni sulle procedure specifiche per l'opzione scelta.

## <a name="overview"></a>Panoramica dei processi di distribuzione

Non appena si crea o si effettua il provisioning di un'app nel servizio app di Azure, prima della distribuzione del codice, il servizio app di Azure gestisce automaticamente il framework applicazioni, ad esempio ASP.NET, PHP, Node.js e così via. Alcuni framework sono abilitati per impostazione predefinita, mentre altri, come Java e Python, possono richiedere una semplice configurazione con segni di spunta per abilitarli. È anche possibile personalizzare il framework applicazioni, ad esempio la versione PHP o il numero di bit del proprio runtime. Per altre informazioni, vedere [Configurare le app Web nel servizio app di Azure](web-sites-configure.md).

Non essendo necessario preoccuparsi del server Web o del framework applicazioni, la distribuzione dell'app al servizio app si limita alla distribuzione di codice, file binari, file di contenuto e della rispettiva struttura di directory nella directory [**/site/wwwroot** in Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) o nella directory **/Data/Jobs** per i processi Web. Il servizio App supporta i tre processi di distribuzione seguenti.

- [FTP o FTPS](https://en.wikipedia.org/wiki/File_Transfer_Protocol): usare lo strumento abilitato per FTP o FTPS preferito per spostare i file in Azure da [FileZilla](https://filezilla-project.org) a IDE completi, come [NetBeans](https://netbeans.org). Si tratta esclusivamente di un processo di caricamento di file. Non vengono forniti altri servizi dal servizio app, ad esempio controllo della versione, gestione della struttura di file e così via. 
- [Kudu (Git/Mercurial)](https://github.com/projectkudu/kudu/wiki/Deployment): è il [motore di distribuzione](https://github.com/projectkudu/kudu/wiki) nel servizio app. Effettuare il push del codice direttamente in Kudu da qualsiasi repository. Kudu fornisce anche altri servizi ogni volta che viene effettuato il push di codice, inclusi controllo della versione, ripristino del pacchetto, MSBuild e [webhook](https://github.com/projectkudu/kudu/wiki/Web-hooks) per la distribuzione continua e altre attività di automazione. Tutti questi servizi sono personalizzabili e attivati 
    - ogni volta che si esegue **git push** da un repository Git configurato,
	- ogni volta che si esegue **hg push** da un repository Mercurial configurato oppure 
    - ogni volta che un servizio di archiviazione cloud collegato, come Dropbox o OneDrive, viene sincronizzato con il servizio app. 
- [Distribuzione Web](http://www.iis.net/learn/publish/using-web-deploy/introduction-to-web-deploy): lo stesso strumento che automatizza la distribuzione ai server IIS. Consente di distribuire il codice al servizio app direttamente dagli strumenti Microsoft preferiti, ad esempio Visual Studio, WebMatrix e Visual Studio Team Services. Questo strumento supporta la distribuzione solo delle differenze, la creazione di database, le trasformazioni delle stringhe di connessione e così via. Distribuzione Web si differenzia da Kudu perché i file binari dell'applicazione vengono compilati prima della distribuzione in Azure. Come FTP, non vengono forniti servizi aggiuntivi dal servizio app.

Gli strumenti di sviluppo Web più diffusi supportano uno o più di questi processi di distribuzione. Mentre lo strumento scelto determina i processi di distribuzione che è possibile sfruttare, l'effettiva funzionalità DevOps disponibile dipende dalla combinazione del processo di distribuzione e degli strumenti specifici scelti. Ad esempio, se si esegue Distribuzione Web da [Visual Studio con Azure SDK](#vspros), anche se non si usufruisce l'automazione da Kudu, si ottengono il ripristino del pacchetto e l'automazione di MSBuild in Visual Studio. Azure SDK fornisce anche una procedura guidata facile da usare per creare le risorse di Azure necessarie direttamente dall'interfaccia di Visual Studio.

>[AZURE.NOTE]Questi processi di distribuzione in realtà non [effettuano il provisioning delle risorse di Azure](resource-group-portal) che l'applicazione potrebbe richiedere, ad esempio il piano di servizio app, l'app del servizio app e il database SQL. La maggior parte degli articoli sulle procedure collegati mostra come effettuare il provisioning dell'app E distribuire il codice end-to-end. È anche possibile trovare altre opzioni per il provisioning delle risorse di Azure nella sezione [Automatizzare la distribuzione con gli strumenti da riga di comando](#automate).

## <a name="ftp"></a>Distribuire copiando manualmente i file in Azure
Se si è abituati a copiare manualmente il contenuto Web nei provider di servizi di hosting Web, un flusso di lavoro comune per gli sviluppatori PHP, è possibile usare un'utilità [FTP](http://en.wikipedia.org/wiki/File_Transfer_Protocol) per copiare i file, ad esempio Esplora risorse o [FileZilla](https://filezilla-project.org/).

L'operazione di copia manuale dei file usa il protocollo FTP per la distribuzione. Vedere [Panoramica dei processi di distribuzione](#overview).

Ecco i vantaggi della copia manuale dei file:

- Familiarità con gli strumenti FTP. 
- Conoscenza esatta della destinazione dei file.
- Maggiore sicurezza con FTPS.
- Soluzione di distribuzione ideale se si preferisce uno strumento semplice per lo sviluppo Web, ad esempio sviluppare app Web con il Blocco note.

Ecco gli svantaggi della copia manuale dei file:

- Si è responsabili della distribuzione dei file nelle directory corrette del servizio App.
- Nessun controllo della versione per il ripristino dello stato precedente quando si verificano errori.
- Molti strumenti FTP non forniscono la copia solo delle differenze e copiano semplicemente tutti i file. Per le app di grandi dimensioni questo comporta tempi di distribuzione prolungati anche per gli aggiornamenti secondari.

### <a name="howtoftp"></a>Come distribuire copiando manualmente i file in Azure
La copia dei file in Azure comporta alcuni semplici passaggi:

1. Creare le credenziali di distribuzione per l'app nel [portale di Azure](https://portal.azure.com). A questo scopo, nel pannello dell'app fare clic su **Impostazioni** > **Credenziali per la distribuzione**.
2. Dopo aver configurato le credenziali per la distribuzione, è possibile ottenere le informazioni di connessione FTP tramite **Impostazioni** > **Proprietà** e quindi copiare i valori per **Utente FTP/distribuzione**, **Nome host FTP** e **Nome host FTPS**.
3. Dal client FTP, usare le informazioni di connessione raccolte per connettersi all'app.
4. Copiare i file e la struttura di directory corrispondente nella directory [**/site/wwwroot** in Azure](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) o nella directory **/Data/Jobs** per i processi Web.
5. Passare all'URL dell'app per verificare che l'applicazione venga eseguita correttamente. 

Per altre informazioni, vedere le seguenti risorse:

* [Creare un'app Web PHP-MySQL e distribuirla tramite FTP](web-sites-php-mysql-deploy-use-ftp.md).
* [Utilizzo di script Batch FTP](http://support.microsoft.com/kb/96269).

## <a name="dropbox"></a>Distribuire tramite la sincronizzazione con una cartella nel cloud
Una valida alternativa alla [copia manuale dei file](#ftp) è la sincronizzazione di file e cartelle con il servizio app da un servizio di archiviazione cloud, ad esempio OneDrive e Dropbox. Nel [portale di Azure](https://portal.azure.com) è possibile configurare una cartella speciale nel servizio di archiviazione cloud, utilizzare il codice e il contenuto dell'applicazione disponibile in tale cartella e procedere alla sincronizzazione con il servizio app con un semplice clic del mouse.

La sincronizzazione con una cartella nel cloud utilizza il processo Kudu per la distribuzione. Vedere [Panoramica dei processi di distribuzione](#overview).

Ecco i vantaggi della sincronizzazione con una cartella nel cloud:

- Semplicità di distribuzione. Servizi come OneDrive e Dropbox forniscono client di sincronizzazione desktop, quindi la directory di lavoro locale è anche la directory di distribuzione.
- Distribuzione con un clic.
- Tutte le funzionalità di Kudu sono disponibili, ad esempio controllo delle versioni di distribuzione, ripristino dello stato precedente, ripristino del pacchetto, automazione.
- Soluzione di distribuzione ideale se si preferisce uno strumento semplice per lo sviluppo Web.

Ecco gli svantaggi della sincronizzazione con una cartella nel cloud:

- Non è una soluzione ideale per un progetto team.

### <a name="howtodropbox"></a>Come distribuire tramite la sincronizzazione con una cartella nel cloud
 
* [Eseguire la distribuzione in App Web da Dropbox](http://blogs.msdn.com/b/windowsazure/archive/2013/03/19/new-deploy-to-windows-azure-web-sites-from-dropbox.aspx). Come utilizzare il [Portale di Azure](https://portal.azure.com) per configurare la distribuzione tramite Dropbox.
* [Distribuzione di Dropbox ad App Web](http://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Dropbox-Deployment-to-Windows-Azure-Web-Sites). In questo video viene illustrato il processo di connessione di una cartella di DropBox a un'app Web. Il video dimostra con quale rapidità è possibile rendere operativo un'app Web o gestirla per mezzo di una semplice distribuzione a trascinamento.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

## Distribuire tramite un IDE
Se si usa già [Visual Studio](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) con un [Azure SDK](https://azure.microsoft.com/downloads/) o WebMatrix, o altri gruppi di IDE come [Xcode](https://developer.apple.com/xcode/) e [Eclipse](https://www.eclipse.org), è possibile eseguire la distribuzione in Azure direttamente dall'interno dell'IDE. Questa opzione è ideale per un singolo sviluppatore.

Visual Studio supporta tutte e tre i processi di distribuzione, ovvero FTP, Git e Distribuzione Web, secondo le proprie preferenze, mentre altri IDE possono eseguire la distribuzione nel servizio app se includono l'integrazione con FTP o Git. Vedere [Panoramica dei processi di distribuzione](#overview).

Ecco i vantaggi della distribuzione con un IDE:

- Ridurre potenzialmente gli strumenti per il ciclo di vita dell'applicazione end-to-end. Sviluppare, eseguire il debug, tenere traccia e distribuire l'app in Azure senza uscire dall'IDE. 

Ecco gli svantaggi della distribuzione con un IDE:

- Complessità aggiuntiva degli strumenti.
- Richiede comunque un sistema di controllo del codice sorgente per un progetto team.

<a name="vspros"></a> Altri vantaggi della distribuzione tramite Visual Studio con Azure SDK:

- Azure SDK rende le risorse di Azure elementi di primaria importanza in Visual Studio. Creare, eliminare, modificare, avviare e arrestare app, eseguire query sul database SQL di back-end, live-debug applicazione Azure e molto altro ancora. 
- Modifica in tempo reale dei file di codice in Azure.
- Debug in tempo reale di app in Azure.
- Azure Explorer integrato.
- Distribuzione solo delle differenze. 

###<a name="vs"></a>Come distribuire direttamente da Visual Studio

* [Introduzione ad Azure e ASP.NET](web-sites-dotnet-get-started.md). Come creare e distribuire un semplice Progetto Applicazione Web MVC ASP.NET utilizzando Visual Studio e Distribuzione Web.
* [Come eseguire la distribuzione nei processi Web di Azure utilizzando Visual Studio](websites-dotnet-deploy-webjobs.md). Come configurare i progetti di applicazioni console in modo da distribuirli come processi Web.  
* [Distribuire un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in App Web](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md). Come creare e distribuire un Progetto Applicazione Web MVC ASP.NET con un database SQL utilizzando Visual Studio, Distribuzione Web e Migrazioni Code First di Entity Framework.
* [Panoramica di Distribuzione Web per Visual Studio e ASP.NET](http://msdn.microsoft.com/library/dd394698.aspx). Presentazione di base della distribuzione Web con Visual Studio. È un articolo datato, che però include informazioni ancora rilevanti, tra cui una panoramica delle opzioni di distribuzione di un database assieme all'applicazione Web e un elenco delle ulteriori attività di distribuzione che potrebbe essere necessario eseguire oppure far eseguire a Visual Studio configurandolo manualmente. Questo articolo riguarda la distribuzione in generale e non soltanto la distribuzione in App Web.
* [Distribuzione Web ASP.NET con Visual Studio](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/introduction). Una serie di esercitazioni in 12 parti in cui è trattata una gamma più completa di attività di distribuzione rispetto alle altre risorse in questo elenco. Sono state aggiunte alcune funzionalità di distribuzione di Azure dal momento in cui è stata creata l'esercitazione. Tuttavia, le note aggiunte successivamente descrivono gli elementi mancanti.
* Articolo relativo alla [distribuzione di un sito Web ASP.NET in Azure con Visual Studio 2012 direttamente da un repository Git](http://www.dotnetcurry.com/ShowArticle.aspx?ID=881). L'articolo contiene informazioni su come distribuire un progetto Web ASP.NET con Visual Studio, utilizzando il plugin Git per eseguire il commit del codice a Git e connettere Azure al repository Git. A partire da Visual Studio 2013, il supporto per Git è integrato, pertanto non è più necessario installare un plug-in.

###<a name="webmatrix"></a>Come distribuire direttamente da WebMatrix

* [Creazione e distribuzione di un sito Web Node.js in Azure con WebMatrix](web-sites-nodejs-use-webmatrix.md).
* [Creazione e distribuzione di un'app Web PHP-MySQL tramite WebMatrix](web-sites-php-mysql-use-webmatrix.md).
* [WebMatrix 3: Git integrato e distribuzione in Azure](http://www.codeproject.com/Articles/577581/Webmatrixplus3-3aplusIntegratedplusGitplusandplusD). Come utilizzare WebMatrix per eseguire la distribuzione da un repository Git di controllo del codice sorgente.

## <a name="onprem"></a>Distribuire da un sistema di controllo del codice sorgente locale

Se si lavora in un team di sviluppo di qualsiasi dimensione e si usa un sistema di gestione del codice sorgente locale, ad esempio [Team Foundation Server](https://www.visualstudio.com/products/tfs-overview-vs.aspx) (TFS), [Git](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#gittfs) o [Mercurial](http://mercurial.selenic.com/), è possibile configurare il servizio app per l'integrazione con il repository ed eseguire la distribuzione direttamente nel servizio app nel flusso di lavoro di controllo del codice sorgente. Se si usa TFS, è possibile configurarne la distribuzione continua nel servizio app.

TFS usa Distribuzione Web per distribuire il servizio app, mentre la distribuzione dal repository Git o Mercurial usa Kudu. Vedere [Panoramica dei processi di distribuzione](#overview).

Ecco i vantaggi della distribuzione da un sistema di controllo del codice sorgente locale:

- Supporto per la distribuzione dal framework di qualsiasi linguaggio o client Git o Mercurial, tra cui [Xcode](https://developer.apple.com/xcode/) ed [Eclipse](https://www.eclipse.org).
- Distribuzione specifica del ramo, consente di distribuire versioni diverse a [slot](web-sites-staged-publishing) diversi.
- Ideale per i team di sviluppo di qualsiasi dimensione.

Ecco gli svantaggi della distribuzione da un sistema di controllo del codice sorgente locale

- È richiesta la conoscenza del sistema di gestione del codice sorgente scelto.
- Può fornire più funzionalità e caratteristiche del necessario.
- Mancanza di soluzioni pronte all'uso per la distribuzione continua e specifica del ramo dagli strumenti client Git e Mercurial. 

Ecco altri vantaggi della distribuzione con TFS:

- Integrazione continua per compilazioni, test e distribuzione.
- Strumenti di collaborazione predefiniti che funzionano con l'IDE o l'editor esistente.
- Supporto di Git per il controllo della versione distribuito o il controllo della versione di Team Foundation (TFVC) per il controllo della versione centralizzato. 
- Strumenti avanzati per la distribuzione agile.
- Integrazioni pronte all'uso per [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [bus di servizio di Azure](/services/service-bus/) e molto altro ancora. 
- [Team Foundation Server Express](https://www.microsoft.com/download/details.aspx?id=48259) è gratuito per un team composto da un massimo di 5 sviluppatori.

###<a name="tfs"></a>Come distribuire continuamente con TFS

* [Recapito continuo per Servizi cloud in Azure](../cloud-services-dotnet-continuous-delivery.md). Questo documento è relativo a un servizio cloud di Azure, ma alcuni dei contenuti si riferiscono ad App Web.

###<a name="gitmercurial"></a>Come distribuire da un repository Git o Mercurial locale

* [Pubblicazione dal controllo del codice sorgente ad App Web con Git](web-sites-publish-source-control.md). Nell'articolo viene descritto come usare Git per pubblicare direttamente dal computer locale a un'app Web; in Azure, questo metodo di pubblicazione è denominato Git locale.. In questo articolo viene inoltre illustrato come abilitare il recapito continuo dei repository Git da GitHub, CodePlex o BitBucket.
* [Pubblicazione in App Web da qualsiasi repository Git/Hg](http://blog.davidebbo.com/2013/04/publishing-to-azure-web-sites-from-any.html). Blog in cui viene illustrata la funzionalità "Repository esterno" di App Web.
* [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).
* Post sulla [distribuzione di DUE siti Web in Azure da un solo repository Git](http://www.hanselman.com/blog/DeployingTWOWebsitesToWindowsAzureFromOneGitRepository.aspx) nel blog di Scott Hanselman.

## Distribuire da un servizio di controllo del codice sorgente basato sul cloud
Se si lavora in un team di sviluppo di qualsiasi dimensione e si usa un sistema di gestione del codice sorgente basato sul cloud, ad esempio [Visual Studio Team Services](http://www.visualstudio.com/) (in precedenza Visual Studio Online), [GitHub](https://www.github.com), [GitLab](https://gitlab.com), [BitBucket](https://bitbucket.org/), [CodePlex](https://www.codeplex.com/), [Codebase](https://www.codebasehq.com) e [Kiln](https://www.fogcreek.com/kiln/), è possibile configurare il servizio app per l'integrazione con il repository e la distribuzione continua.

Visual Studio Team Services usa Distribuzione Web per distribuire il servizio app, mentre la distribuzione da repository online usa Kudu. Vedere [Panoramica dei processi di distribuzione](#overview).

Ecco i vantaggi della distribuzione da un servizio di controllo del codice sorgente basato sul cloud:

- Supporto per il framework di qualsiasi linguaggio.
- Distribuzione continua senza configurazione per i repository Git e Mercurial. 
- Distribuzione specifica del ramo, consente di distribuire rami diversi a [slot](web-sites-staged-publishing) diversi.
- Ideale per i team di sviluppo di qualsiasi dimensione.

Ecco gli svantaggi della distribuzione da un servizio di controllo del codice sorgente basato sul cloud:

- È richiesta la conoscenza del servizio di gestione del codice sorgente scelto.
- Può fornire più funzionalità e caratteristiche del necessario.

Ecco altri vantaggi della distribuzione tramite Visual Studio Team Services:

- Gratuito per un team composto da un massimo di 5 sviluppatori.
- Integrazione continua per compilazioni, test e distribuzione.
- Strumenti di collaborazione predefiniti che funzionano con l'IDE o l'editor esistente.
- Supporto di Git per il controllo della versione distribuito o il controllo della versione di Team Foundation (TFVC) per il controllo della versione centralizzato. 
- Strumenti avanzati per la distribuzione agile.
- Integrazioni pronte all'uso per [Jenkins](https://jenkins-ci.org), [Slack](https://slack.com), [ZenDesk](https://www.zendesk.com), [Trello](https://trello.com), [bus di servizio di Azure](/services/service-bus/) e molto altro ancora. 
- Dashboard avanzati per facilitare la creazione di report con [connessione a Power BI](https://www.visualstudio.com/get-started/report/report-on-vso-with-power-bi-vs).

###<a name="vsts"></a>Come eseguire la distribuzione continua con Visual Studio Team Services

- [Recapito continuo in Azure tramite Visual Studio Team Services e TFVC](../cloud-services-continuous-delivery-use-vso.md). Esercitazione guidata che illustra la procedura di configurazione del recapito continui da Visual Studio Team Services a un'app Web tramite TFVC. 
- [Recapito continuo in Azure tramite Visual Studio Team Services e Git](../cloud-services-continuous-delivery-use-vso-git.md). Simile all'esercitazione precedente, ma usa Git anziché TFVC.

###<a name="cloudgitmercurial"></a>Come distribuire da un repository Git o Mercurial basato sul cloud

- [Pubblicazione dal controllo del codice sorgente ad App Web con Git](web-sites-publish-source-control.md). Come abilitare la distribuzione continua di repository da GitHub, CodePlex o BitBucket. Benché questa esercitazione contenga informazioni su come pubblicare un repository Git, il processo per i repository Mercurial ospitati in CodePlex o BitBucket è simile.
- [Distribuzione in App Web con GitHub tramite Kudu](http://azure.microsoft.com/documentation/videos/deploying-to-azure-from-github/). Video di Scott Hanselman e David Ebbo che spiega come distribuire un'app Web direttamente da GitHub nel servizio app.
- [Pulsante Distribuisci su Azure per App Web](http://azure.microsoft.com/blog/2014/11/13/deploy-to-azure-button-for-azure-websites-2/). Blog riguardante un metodo per attivare la distribuzione da un repository Git.
- [Forum di Azure per Git, Mercurial e Dropbox](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azuregit).

Per ulteriori informazioni, vedere le risorse seguenti:

- [Pubblicazione dal controllo del codice sorgente ad App Web con Git](web-sites-publish-source-control.md). Nell'articolo viene descritto come usare Git per pubblicare direttamente dal computer locale ad App Web; in Azure, questo metodo di pubblicazione è denominato Git locale. 

## <a name="automate"></a>Automatizzare la distribuzione con strumenti da riga di comando

* [Automatizzare la distribuzione con MSBuild](#msbuild)
* [Copiare i file con strumenti e script FTP](#ftp)
* [Automatizzare la distribuzione con Windows PowerShell](#powershell)
* [Automatizzare la distribuzione con API di gestione .NET](#api)
* [Distribuire dall'interfaccia della riga di comando di Azure](#cli)
* [Distribuire dalla riga di comando di distribuzione Web](#webdeploy)
* [Utilizzo di script Batch FTP](http://support.microsoft.com/kb/96269).
 
Un'altra opzione di distribuzione consiste nell'utilizzare un servizio basato su cloud, ad esempio [Distribuzione Octopus](http://en.wikipedia.org/wiki/Octopus_Deploy). Per altre informazioni, vedere l'articolo relativo alla [distribuzione di applicazioni ASP.NET in Siti Web di Azure](https://octopusdeploy.com/blog/deploy-aspnet-applications-to-azure-websites).

###<a name="msbuild"></a>Automatizzare la distribuzione con MSBuild

Se si utilizza l'[IDE di Visual Studio](#vs) per lo sviluppo è possibile servirsi di [MSBuild](http://msbuildbook.com/) per automatizzare qualsiasi operazione nell'IDE. È possibile configurare MSBuild in modo da utilizzare [Distribuzione Web](#webdeploy) oppure [FTP/FTPS](#ftp) per copiare i file. Distribuzione Web consente inoltre di automatizzare molte altre attività relative alla distribuzione, ad esempio la distribuzione dei database.

Per ulteriori informazioni sulla distribuzione da riga di comando, vedere le risorse seguenti:

* [Distribuzione Web ASP.NET con Visual Studio: distribuzione da riga di comando](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/command-line-deployment). Decima di una serie di esercitazioni sulla distribuzione in Azure con Visual Studio. Contiene informazioni su come utilizzare la riga di comando per eseguire la distribuzione dopo aver configurato i profili di pubblicazione in Visual Studio.
* [Inside the Microsoft Build Engine: Using MSBuild and Team Foundation Build](http://msbuildbook.com/). Libro paperback che contiene alcuni capitoli su come utilizzare MSBuild per la distribuzione.

###<a name="powershell"></a>Automatizzare la distribuzione con Windows PowerShell

In [Windows PowerShell](http://msdn.microsoft.com/library/dd835506.aspx) è possibile eseguire funzioni di distribuzione MSBuild o FTP. In tal caso, è inoltre possibile utilizzare una raccolta di cmdlet di Windows PowerShell che facilitano la chiamata dell'API di gestione RET di Azure.

Per altre informazioni, vedere le seguenti risorse:

* [Distribuire un'app Web collegata a un repository GitHub](app-service-web-arm-from-github-provision.md)
* [Eseguire il provisioning di un'app Web con un database SQL](app-service-web-arm-with-sql-database-provision.md)
* [Effettuare il provisioning di microservizi e distribuirli in modo prevedibile in Azure](app-service-deploy-complex-application-predictably.md)
* Capitolo dell'e-book relativo a come [automatizzare tutto e creare app per cloud reali con Azure](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything), in cui viene illustrato in che modo l'applicazione di esempio illustrata nell'e-book utilizza gli script di Windows PowerShell per creare un ambiente di testing Azure ed eseguirvi la distribuzione. Per ottenere collegamenti a ulteriori documenti su Azure PowerShell vedere la sezione relativa alle [risorse](http://asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything#resources).
* [Utilizzo degli script di Windows PowerShell per la pubblicazione in ambienti di sviluppo e test](http://msdn.microsoft.com/library/dn642480.aspx). Come usare gli script di distribuzione di Windows PowerShell generati da Visual Studio.

###<a name="api"></a>Automatizzare la distribuzione con API di gestione .NET

È possibile scrivere il codice C# per eseguire funzioni MSBuild o FTP per la distribuzione. In tal caso, sarà possibile accedere all'API REST per la gestione per eseguire funzioni di gestione del sito.

Per altre informazioni, vedere la risorsa seguente:

* Post del blog di Scott Hanselman sull'[automazione completa con le librerie di gestione di Azure e .NET](http://www.hanselman.com/blog/PennyPinchingInTheCloudAutomatingEverythingWithTheWindowsAzureManagementLibrariesAndNET.aspx). Contiene una presentazione dell'API di gestione .NET e collegamenti a ulteriore documentazione.

###<a name="cli"></a>Distribuire dall'interfaccia della riga di comando di Azure

È possibile usare la riga di comando nei computer Windows, Mac o Linux per eseguire distribuzioni tramite FTP. In tal caso, è anche possibile accedere all'API REST per la gestione di Azure usando l'interfaccia della riga di comando di Azure.

Per altre informazioni, vedere la risorsa seguente:

* [Strumenti da riga di comando di Azure](/downloads/#cmd-line-tools). Pagina del portale su Azure.com per le informazioni sullo strumento da riga di comando.

###<a name="webdeploy"></a>Distribuzione dalla riga di comando di distribuzione Web

[Distribuzione Web](http://www.iis.net/downloads/microsoft/web-deploy) è il software Microsoft per la distribuzione in IIS che non solo offre funzioni intelligenti di sincronizzazione dei file, ma può anche eseguire o coordinare molte altre attività relative alla distribuzione che non è possibile automatizzare quando si utilizza il trasferimento FTP. Ad esempio, con Distribuzione Web è possibile distribuire un nuovo database oppure gli aggiornamenti al database assieme all'app Web. Distribuzione Web consente inoltre di ridurre al minimo i tempi richiesti per l'aggiornamento di un sito esistente dal momento che è in grado di copiare in maniera intelligente solo i file modificati. Il supporto per Distribuzione Web è incorporato in Microsoft WebMatrix, Visual Studio, Visual Studio Team Services e Team Foundation Server, ma è inoltre possibile utilizzare Distribuzione Web direttamente dalla riga di comando per automatizzare la distribuzione. I comandi di Distribuzione Web sono molti potenti, ma la curva di apprendimento può rivelarsi ripida.

Per altre informazioni, vedere la risorsa seguente:

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
 

<!---HONumber=AcomDC_0114_2016-->