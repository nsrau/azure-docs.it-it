<properties title="Securing an Azure Website" pageTitle="Protezione di un sito Web di Azure" description="Informazioni sulla sicurezza di un sito Web di Azure." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr" />


#Protezione di un'applicazione Web in un sito Web di Azure

Una delle sfide relative allo sviluppo di un'applicazione Web riguarda l'offerta di un servizio sicuro e protetto ai clienti. In questo articolo vengono presentate le funzionalità dei siti Web di Azure in grado di proteggere l'applicazione Web.

> [WACOM.NOTE] Una discussione completa sulle considerazioni relative alla sicurezza delle applicazioni basate su Web non rientra negli obiettivi di questo documento. Come punto di partenza per altre informazioni sulla protezione delle applicazioni Web, vedere il sito [Open Web Application Security Project (OWASP)]( https://www.owasp.org/index.php/Main_Page) e, in particolare, la sezione relativa al [progetto Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) che elenca i 10 più gravi difetti di sicurezza correnti delle applicazioni Web individuati dai membri di OWASP.

###Sommario

* [Proteggere le comunicazioni](#https)
* [Proteggere lo sviluppo](#develop)
* [Passaggi successivi](#next)
 
##<a name="https"></a>Proteggere le comunicazioni

Se si usa il nome di dominio ***.azurewebsites.net** creato per il sito Web, è possibile usare subito HTTPS perché il certificato SSL viene fornito per tutti i nomi di dominio ***.azurewebsites.net**. Se il sito usa un [nome di dominio personalizzato](http://azure.microsoft.com/it-it/documentation/articles/web-sites-custom-domain-name/), è possibile caricare un certificato SSL per abilitare HTTPS per il dominio personalizzato.

##<a name="develop"></a>Proteggere lo sviluppo 

###Profili e impostazioni di pubblicazione

Quando si sviluppano le applicazioni, si eseguono attività di gestione o si automatizzano le attività usando utilità come **Visual Studio**, **Web Matrix**, **Azure PowerShell** o l'**interfaccia della riga di comando multipiattaforma** di Azure, è possibile usare un file di impostazioni di pubblicazione o un profilo di pubblicazione. Entrambi consentono di autenticarsi in Azure ed impediscono gli accessi non autorizzati.

* Un file di **impostazioni di pubblicazione** contiene

	* L'ID sottoscrizione di Azure

	* Un certificato di gestione che consente di eseguire attività di gestione per la sottoscrizione *senza dover inserire un nome o una password dell'account*.

* Un file **profilo di pubblicazione** contiene

	* Informazioni per la pubblicazione del sito Web di Azure

Se si usa un'utilità basata sulle impostazioni o sul profilo di pubblicazione, importare il file contenente le impostazioni o il profilo di pubblicazione nell'utilità, quindi **eliminare** il file. Se si vuole tenere il file, ad esempio per condividerlo con gli altri che lavorano sul progetto, archiviarlo in un percorso sicuro, ad esempio una directory **crittografata** con autorizzazioni limitate.

Inoltre, assicurarsi che le credenziali importate siano protette. Ad esempio, **Azure PowerShell** e l'**interfaccia della riga di comando multipiattaforma di Azure** archiviano le informazioni importate nella **home directory** (*~* nei sistemi Linux o OS X e */users/yourusername* nei sistemi Windows). Per una maggiore protezione è opportuno **crittografare** questi percorsi con gli strumenti di crittografia disponibili per il sistema operativo.

###Impostazioni di configurazione e stringhe di connessione
Di solito le stringhe di connessione, le credenziali di autenticazione e altre informazioni riservate vengono archiviate nei file di configurazione. Purtroppo questi file possono essere esposti nel sito Web o inseriti in un repository pubblico che ne espone le informazioni.

I siti Web di Azure consentono di archiviare le informazioni di configurazione nell'ambiente di runtime dei siti Web come **impostazioni app** e **stringhe di connessione**. I valori vengono esposti nell'applicazione al runtime mediante *variabili di ambiente* per la maggior parte dei linguaggi di programmazione. Per le applicazioni .NET questi valori vengono inseriti nella configurazione .NET al runtime.

Le **impostazioni app** e le **stringhe di connessione** possono essere configurate nel portale di gestione di Azure o con utilità come PowerShell o l'interfaccia della riga di comando multipiattaforma di Azure.

Per altre informazioni sulle impostazioni app e le stringhe di connessione, vedere [Come configurare i siti Web].(/it-it/documentation/articles/web-sites-configure/).

###FTPS

Azure fornisce un accesso FTP sicuro al file system per i siti Web mediante **FTPS**. Ciò consente di accedere in modo sicuro al codice dell'applicazione nel sito Web e ai log di diagnostica. Il collegamento FTPS per il sito Web è disponibile nel **Dashboard** del sito nel [portale di gestione di Azure](https://manage.windowsazure.com).

Per altre informazioni su FTPS, vedere [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

##Passaggi successivi

Per altre informazioni sulla sicurezza della piattaforma Azure, informazioni su come segnalare un **uso improprio o un problema di sicurezza** o per segnalare a Microsoft che si sta per eseguire un **test di penetrazione** del sito, vedere la sezione sulla sicurezza del [Centro protezione Microsoft Azure](/it-it/support/trust-center/security/).

Per altre informazioni sui file **web.config** o **applicationhost.config** nei siti Web di Azure, vedere il blog relativo alle [opzioni di configurazione sbloccate nei siti Web di Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Per informazioni sulla registrazione per i siti Web di Azure, utile per rilevare gli attacchi, vedere [Abilitazione della registrazione diagnostica](/it-it/documentation/articles/web-sites-enable-diagnostic-log/).
