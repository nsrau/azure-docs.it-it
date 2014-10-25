<properties title="Securing an Azure Website" pageTitle="Securing an Azure Website." description="Learn how to secure an Azure Website." metaKeywords="Azure web site security, azure web site https, azure web site ftps, azure web site ssl, azure web site ssl rewrite" services="web-sites" solutions="" documentationCenter="web" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="larryfr"></tags>

## Protezione di un'applicazione Web in un sito Web di Azure

Una delle sfide relative allo sviluppo di un'applicazione Web riguarda l'offerta di un servizio sicuro e protetto ai clienti. In questo articolo vengono presentate le funzionalità dei siti Web di Azure in grado di proteggere l'applicazione Web.

> [WACOM.NOTE] Una discussione completa sulle considerazioni relative alla sicurezza delle applicazioni basate su Web non rientra negli obiettivi di questo documento. Come punto di partenza per ulteriori informazioni sulla protezione delle applicazioni Web, vedere il sito [Open Web Application Security Project (OWASP)][Open Web Application Security Project (OWASP)] e, in particolare, la sezione relativa al [progetto Top 10][progetto Top 10] che elenca i 10 più gravi difetti di sicurezza correnti delle applicazioni Web individuati dai membri di OWASP.

### Sommario

-   [Proteggere le comunicazioni][Proteggere le comunicazioni]
-   [Proteggere lo sviluppo][Proteggere lo sviluppo]
-   [Passaggi successivi][Passaggi successivi]

## <a name="https"></a>Proteggere le comunicazioni

Se si usa il nome di dominio ***.azurewebsites.net** creato per il sito Web, è possibile usare subito HTTPS perché il certificato SSL viene fornito per tutti i nomi di dominio* **.azurewebsites.net**. Se il sito usa un [nome di dominio personalizzato][nome di dominio personalizzato] è possibile caricare un certificato SSL per abilitare HTTPS per il dominio personalizzato.

Per altre informazioni, vedere [Abilitare HTTPS per un sito Web di Azure][Abilitare HTTPS per un sito Web di Azure].

### Applicazione di HTTPS

I siti Web di Azure *non* applicano HTTPS. I visitatori possono comunque accedere al sito usando HTTP che, però, potrebbe esporre le informazioni riservate. Per applicare HTTPS usare il modulo**Riscrittura URL**. Il modulo Riscrittura URL è incluso nei siti Web di Azure e consente di definire le regole applicate alle richieste in ingresso prima che queste vengano passate all'applicazione. Può essere usato per le applicazioni scritte in qualsiasi linguaggio di programmazione supportato dai siti Web di Azure.

> [WACOM.NOTE] Le applicazioni .NET MVC devono usare il filtro [RequireHttps][RequireHttps] invece di Riscrittura URL. Per altre informazioni sull'uso di RequireHttps, vedere [Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure][Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure].
>
> Per informazioni sul reindirizzamento delle richieste a livello di codice usando altri linguaggi e framework di programmazione, consultare la documentazione relativa a queste tecnologie.

Le regole di Riscrittura URL sono definite in un file **web.config** archiviato nella radice dell'applicazione. Il seguente esempio contiene una regole di Riscrittura URL di base che forza l'uso di HTTPS per tutto il traffico in ingresso.

<a name="example"></a>**Esempio Web.Config di Riscrittura URL**

    <?xml version="1.0" encoding="UTF-8"?>
    <configuration>
      <system.webServer>
        <rewrite>
          <rules>
            <rule name="Force HTTPS" enabled="true">
              <match url="(.*)" ignoreCase="false" />
              <conditions>
                <add input="{HTTPS}" pattern="off" />
              </conditions>
              <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
            </rule>
          </rules>
        </rewrite>
      </system.webServer>
    </configuration>

Il funzionamento di questa regola prevede la restituzione di un codice di stato HTTP 301 (reindirizzamento permanente) quando l'utente richiede una pagina mediante HTTP. Il codice 301 reindirizza la richiesta allo stesso URL richiesto dal visitatore, ma sostituisce la parte HTTP della richiesta con HTTPS. Ad esempio, <HTTP://contoso.com> viene reindirizzato a <HTTPS://contoso.com>.

> [WACOM.NOTE] Se l'applicazione è scritta in **Node.js**, **PHP**, **Python Django** o **Java**, è probabile che non includa un file web.config. **Node.js**, **Python Django** e **Java** usano comunque il file web.config quando sono ospitati nei siti Web di Azure perché Azure crea automaticamente il file durante distribuzione, anche se l'utente non lo visualizza. Se si include un file nell'applicazione, questo sostituisce quello generato automaticamente da Azure.

### .NET

Per le applicazioni .NET modificare il file web.config per l'applicazione e aggiungere la sezione **\<rewrite\>** dall'[esempio][esempio] alla sezione **\<system.WebServer\>**.

Se il file web.config include già una sezione **\<rewrite\>**, aggiungere **\<rule\>** dall'[esempio][esempio] come prima voce nella sezione **\<rules\>**.

### PHP

Per le applicazioni PHP salvare semplicemente l'[esempio][esempio] come file web.config nella radice dell'applicazione, quindi ridistribuire l'applicazione nel sito Web di Azure.

### Node.js, Python Django e Java

Un file web.config viene creato automaticamente per le app Node.js, Python Django e Java se non è già disponibile, ma esiste solo sul server perché viene creato durante la distribuzione. Il file generato automaticamente contiene le impostazioni che indicano ad Azure come ospitare l'applicazione.

Per recuperare e modificare il file autogenerato dal sito Web, usare i seguenti passaggi.

1.  Scaricare il file tramite FTP (vedere la pagina relativa al [caricamento/download dei file tramite FTP e alla raccolta dei log di diagnostica][caricamento/download dei file tramite FTP e alla raccolta dei log di diagnostica]).

2.  Aggiungerlo alla radice dell'applicazione.

3.  Aggiungere le regole di riscrittura usando le seguenti informazioni.

    -   **Node.js e Python Django**

        Il file web.config generato per le applicazioni Node.js e Python Django ha già una sezione **\<rewrite\>** contenente le voci **\<rule\>** necessarie per il corretto funzionamento del sito. Per forzare l'uso di HTTPS nel sito aggiungere **\<rule\>** dall'esempio come prima voce nella sezione **\<rules\>**. In questo modo viene forzato HTTPS, mentre le altre regole restano intatte.

    -   **Java**

        Il file web.config per applicazioni Java con Apache Tomcat non contiene una sezione **\<rewrite\>** quindi è necessario aggiungere la sezione **\<rewrite\>** dell'esempio alla sezione **\<system.webServer\>**.

4.  Ridistribuire il progetto (incluso il file web.config aggiornato) in Azure

Dopo aver distribuito un file web.config con una regola di riscrittura per forzare HTTPS, la regola viene applicata immediatamente e tutte le richieste vengono reindirizzate a HTTPS.

Per altre informazioni sul modulo IIS Riscrittura URL, vedere la documentazione [Riscrittura URL][Riscrittura URL].

## <a name="develop"></a>Proteggere lo sviluppo

### Profili e impostazioni di pubblicazione

Quando si sviluppano le applicazioni, si eseguono attività di gestione o si automatizzano le attività usando utilità come **Visual Studio**, **Web Matrix**, **Azure PowerShell** o l'**interfaccia della riga di comando multipiattaforma** di Azure, è possibile usare un file di *impostazioni di pubblicazione* o un *profilo di pubblicazione*. Entrambi consentono di autenticarsi in Azure ed impediscono gli accessi non autorizzati.

-   Un file di **impostazioni di pubblicazione** contiene

    -   L'ID sottoscrizione di Azure

    -   Un certificato di gestione che consente di eseguire attività di gestione per la sottoscrizione *senza dover inserire un nome o una password dell'account*.

-   Un file **profilo di pubblicazione** contiene

    -   Informazioni per la pubblicazione del sito Web di Azure

Se si usa un'utilità basata sulle impostazioni o sul profilo di pubblicazione, importare il file contenente le impostazioni o il profilo di pubblicazione nell'utilità, quindi **eliminare** il file. Se si vuole tenere il file, ad esempio per condividerlo con gli altri che lavorano sul progetto, archiviarlo in un percorso sicuro, ad esempio una directory **crittografata** con autorizzazioni limitate.

Inoltre, assicurarsi che le credenziali importate siano protette. Ad esempio, **Azure PowerShell** e l'**interfaccia della riga di comando multipiattaforma di Azure** archiviano le informazioni importate nella **home directory** (*~* nei sistemi Linux o OS X e in */users/yourusername* nei sistemi Windows). Per una maggiore protezione è opportuno **crittografare** questi percorsi con gli strumenti di crittografia disponibili per il sistema operativo.

### Impostazioni di configurazione e stringhe di connessione

Di solito le stringhe di connessione, le credenziali di autenticazione e altre informazioni riservate vengono archiviate nei file di configurazione. Purtroppo questi file possono essere esposti nel sito Web o inseriti in un repository pubblico che ne espone le informazioni.

I siti Web di Azure consentono di archiviare le informazioni di configurazione nell'ambiente di runtime dei siti Web come **impostazioni app** e **stringhe di connessione**. I valori vengono esposti nell'applicazione al runtime mediante *variabili di ambiente* per la maggior parte dei linguaggi di programmazione. Per le applicazioni .NET questi valori vengono inseriti nella configurazione .NET al runtime.

Le **impostazioni app** e le **stringhe di connessione** possono essere configurate nel portale di gestione di Azure o con utilità come PowerShell o l'interfaccia della riga di comando multipiattaforma di Azure.

Per altre informazioni sulle impostazioni app e le stringhe di connessione, vedere [Come configurare i siti Web][Come configurare i siti Web].

### FTPS

Azure fornisce un accesso FTP sicuro al file system per i siti Web mediante **FTPS**. Ciò consente di accedere in modo sicuro al codice dell'applicazione nel sito Web e ai log di diagnostica. Il collegamento FTPS per il sito Web è disponibile nel **Dashboard** del sito nel [portale di gestione di Azure][portale di gestione di Azure].

Per altre informazioni su FTPS, vedere [File Transfer Protocol][File Transfer Protocol].

## Passaggi successivi

Per altre informazioni sulla sicurezza della piattaforma Azure, informazioni su come segnalare un **uso improprio o un problema di sicurezza** o per segnalare a Microsoft che si sta per eseguire un **test di penetrazione** del sito, vedere la sezione sulla sicurezza del [Centro protezione Microsoft Azure][Centro protezione Microsoft Azure].

Per altre informazioni sui file **web.config** o **applicationhost.config** nei siti Web di Azure, vedere il blog relativo alle [opzioni di configurazione sbloccate nei siti Web di Azure][opzioni di configurazione sbloccate nei siti Web di Azure].

Per informazioni sulla registrazione per i siti Web di Azure, utile per rilevare gli attacchi, vedere [Abilitazione della registrazione diagnostica][Abilitazione della registrazione diagnostica].

  [Open Web Application Security Project (OWASP)]: https://www.owasp.org/index.php/Main_Page
  [progetto Top 10]: https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project
  [Proteggere le comunicazioni]: #https
  [Proteggere lo sviluppo]: #develop
  [Passaggi successivi]: #next
  [nome di dominio personalizzato]: http://azure.microsoft.com/it-it/documentation/articles/web-sites-custom-domain-name/
  [Abilitare HTTPS per un sito Web di Azure]: /it-it/documentation/articles/web-sites-configure-ssl-certificate/
  [RequireHttps]: http://msdn.microsoft.com/en-us/library/system.web.mvc.requirehttpsattribute.aspx
  [Distribuzione di un'app ASP.NET MVC 5 sicura con appartenenza, OAuth e database SQL in un sito Web di Azure]: /it-it/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/
  [esempio]: #example
  [caricamento/download dei file tramite FTP e alla raccolta dei log di diagnostica]: http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx
  [Riscrittura URL]: http://www.iis.net/downloads/microsoft/url-rewrite
  [Come configurare i siti Web]: /it-it/documentation/articles/web-sites-configure/
  [portale di gestione di Azure]: https://manage.windowsazure.com
  [File Transfer Protocol]: http://en.wikipedia.org/wiki/File_Transfer_Protocol
  [Centro protezione Microsoft Azure]: /en-us/support/trust-center/security/
  [opzioni di configurazione sbloccate nei siti Web di Azure]: http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/
  [Abilitazione della registrazione diagnostica]: /it-it/documentation/articles/web-sites-enable-diagnostic-log/
