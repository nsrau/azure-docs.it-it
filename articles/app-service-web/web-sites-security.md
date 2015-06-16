<properties 
	pageTitle="Proteggere un'app Web in Azure App Service" 
	description="Informazioni su come proteggere un'app Web di Azure.&quot;" 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>


#Proteggere un'app Web in Azure App Service

Una delle sfide relative allo sviluppo di un'app Web riguarda l'offerta di un servizio sicuro e protetto ai clienti. In questo articolo vengono presentate le funzionalità di [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) in grado di proteggere l'app Web.

> [AZURE.NOTE]Una discussione completa sulle considerazioni relative alla sicurezza delle applicazioni basate su Web non rientra negli obiettivi di questo documento. Come punto di partenza per ulteriori informazioni sulla protezione delle applicazioni Web, vedere il sito [Open Web Application Security Project (OWASP)](https://www.owasp.org/index.php/Main_Page) e, in particolare, la sezione relativa al [progetto Top 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project) che elenca i 10 più gravi difetti di sicurezza correnti delle applicazioni Web individuati dai membri di OWASP.

##<a name="https"></a> Proteggere le comunicazioni

Se si utilizza il nome di dominio ***.azurewebsites.net** creato per l'app Web, è possibile utilizzare subito HTTPS, perché il certificato SSL viene fornito per tutti i nomi di dominio ***.azurewebsites.net**. Se il sito usa un [nome di dominio personalizzato](web-sites-custom-domain-name.md) è possibile caricare un certificato SSL per [abilitare HTTPS](web-sites-configure-ssl-certificate.md) per il dominio personalizzato.

##<a name="develop"></a>Proteggere lo sviluppo 

### Profili e impostazioni di pubblicazione

Quando si sviluppano le applicazioni, si eseguono attività di gestione o si automatizzano le attività usando utilità come **Visual Studio**, **Web Matrix**, **Azure PowerShell** o l'**interfaccia della riga di comando multipiattaforma** di Azure, è possibile usare un file di *impostazioni di pubblicazione* o un *profilo di pubblicazione*. Entrambi consentono di autenticarsi in Azure ed impediscono gli accessi non autorizzati.

* Un file di **impostazioni di pubblicazione** contiene

	* L'ID sottoscrizione di Azure

	* Un certificato di gestione che consente di eseguire attività di gestione per la sottoscrizione *senza dover inserire un nome o una password dell'account*.

* Un file **profilo di pubblicazione** contiene

	* Informazioni per la pubblicazione nell'app Web

Se si usa un'utilità basata sulle impostazioni o sul profilo di pubblicazione, importare il file contenente le impostazioni o il profilo di pubblicazione nell'utilità, quindi **eliminare** il file. Se si vuole tenere il file, ad esempio per condividerlo con gli altri che lavorano sul progetto, archiviarlo in un percorso sicuro, ad esempio una directory **crittografata** con autorizzazioni limitate.

Inoltre, assicurarsi che le credenziali importate siano protette. Ad esempio, **Azure PowerShell** e l'**interfaccia della riga di comando multipiattaforma di Azure** archiviano le informazioni importate nella **home directory** (*~* nei sistemi Linux o OS X e in */users/yourusername* nei sistemi Windows). Per una maggiore protezione è opportuno **crittografare** questi percorsi con gli strumenti di crittografia disponibili per il sistema operativo.

### Impostazioni di configurazione e stringhe di connessione
Di solito le stringhe di connessione, le credenziali di autenticazione e altre informazioni riservate vengono archiviate nei file di configurazione. Purtroppo questi file possono essere esposti nel sito Web o inseriti in un repository pubblico che ne espone le informazioni.

Azure App Service consente di archiviare le informazioni di configurazione come parte dell'ambiente di runtime delle app Web come **impostazioni app** e **stringhe di connessione**. I valori vengono esposti nell'applicazione al runtime mediante *variabili di ambiente* per la maggior parte dei linguaggi di programmazione. Per le applicazioni .NET questi valori vengono inseriti nella configurazione .NET al runtime.

Le **impostazioni app** e le **stringhe di connessione** possono essere configurate mediante il [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) o con utilità quali PowerShell o l'interfaccia della riga di comando multipiattaforma di Azure.

Per ulteriori informazioni sulle impostazioni delle app e sulle stringhe di connessione, vedere [Come configurare le app Web](web-sites-configure.md).

### FTPS

Azure fornisce un accesso FTP sicuro al file system per l'app Web mediante **FTPS**. Ciò consente di accedere in modo sicuro al codice dell'applicazione nell'app Web e ai log di diagnostica. Il collegamento FTPS per l'app Web è disponibile nella pagina **Dashboard** del [portale di gestione di Azure](https://manage.windowsazure.com).

Per altre informazioni su FTPS, vedere [File Transfer Protocol](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Passaggi successivi

Per altre informazioni sulla sicurezza della piattaforma Azure, informazioni su come segnalare un **uso improprio o un problema di sicurezza** o per segnalare a Microsoft che si sta per eseguire un **test di penetrazione** del sito, vedere la sezione sulla sicurezza del [Centro protezione Microsoft Azure](http://azure.microsoft.com/support/trust-center/security/).

Per ulteriori informazioni sui file **web.config** o **applicationhost.config** nelle app Web, vedere [opzioni di configurazione sbloccate nelle app Web in Azure App Service](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Per informazioni sulla registrazione per le app Web, utili per rilevare gli attacchi, vedere [Abilitare la registrazione diagnostica](web-sites-enable-diagnostic-log.md).

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)

* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=54--> 