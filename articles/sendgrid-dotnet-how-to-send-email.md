<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="" solutions="" manager="" editor="" />

Come inviare messaggi di posta elettronica utilizzando SendGrid con Azure
=========================================================================

In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio di posta elettronica SendGrid in Azure. Negli esempi, scritti in C\#, viene utilizzata l'API .NET. Gli scenari presentati includono **creazione di messaggi di posta elettronica**, **invio di messaggi di posta elettronica**, **aggiunta di allegati** e **utilizzo di filtri**. Per ulteriori informazioni su SendGrid e sull'invio di messaggi di posta elettronica, vedere la sezione [Passaggi successivi](#nextsteps).

Sommario
--------

[Informazioni sul servizio di posta elettronica SendGrid](#whatis)   
[Creazione di un account SendGrid](#createaccount)   
 [Riferimento alla libreria di classi .NET di SendGrid](#reference)   
 [Procedura: Creare un messaggio di posta elettronica](#createemail)   
 [Procedura: Inviare un messaggio di posta elettronica](#sendemail)   
 [Procedura: Aggiungere un allegato](#addattachment)   
 [Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi](#usefilters)   
 [Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid](#useservices)   
 [Passaggi successivi](#nextsteps)   

Informazioni sul servizio di posta elettronica SendGridInformazioni sul servizio di posta elettronica SendGrid
--------------------------------------------------------------------------------------------------------------

SendGrid è un [servizio di posta elettronica basato sul cloud](http://sendgrid.com/solutions) che offre [recapito affidabile di messaggi di posta elettronica transazionali](http://sendgrid.com/transactional-email), scalabilità e analisi in tempo reale, oltre ad API flessibili che agevolano l'integrazione personalizzata. Gli scenari di utilizzo comuni di SendGrid includono:

-   Invio automatico di ricevute ai clienti.
-   Amministrazione di liste di distribuzione per l'invio mensile ai clienti di volantini elettronici e offerte speciali.
-   Raccolta di metriche in tempo reale per elementi quali indirizzi di posta elettronica bloccati e velocità di risposta al cliente.
-   Generazione di report per agevolare l'identificazione delle tendenze.
-   Inoltro di richieste dei clienti.

Per ulteriori informazioni, visitare il sito <http://sendgrid.com>.

Creazione di un account SendGridCreazione di un account SendGrid
----------------------------------------------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

Riferimento alla libreria di classi .NET di SendGridRiferimento alla libreria di classi .NET di SendGrid
--------------------------------------------------------------------------------------------------------

Il pacchetto NuGet di SendGrid è il modo più semplice per recuperare l'API SendGrid e configurare l'applicazione con tutte le dipendenze. NuGet è un'estensione di Visual Studio inclusa in Microsoft Visual Studio 2012 che semplifica l'installazione e l'aggiornamento di librerie e strumenti.
**Nota**

Per installare NuGet se si esegue una versione di Visual Studio precedente rispetto a Visual Studio 2012, visitare il sito <http://www.nuget.org> e fare clic su **Install NuGet**.

Per installare il pacchetto NuGet di SendGrid, eseguire le operazioni seguenti:

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti**, quindi fare clic su **Manage NuGet Packages**.

2.  Nel riquadro sinistro della finestra di dialogo **Manage NuGet Packages** fare clic su **Online**.

3.  Cercare **SendGrid** e selezionare la voce **SendGrid** nell'elenco dei risultati.

    ![Pacchetto NuGet di SendGrid](./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png)

4.  Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.

La libreria di classi .NET di SendGrid è denominata **SendGridMail**. Include gli spazi dei nomi seguenti:

-   **SendGridMail** per la creazione e l'utilizzo degli elementi di posta elettronica.
-   **SendGridMail.Transport** per l'invio di posta elettronica tramite il protocollo **SMTP** o HTTP 1.1 con **Web/REST**.

Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C\# in cui si desidera accedere al servizio di posta elettronica SendGrid a livello di codice: **System.Net** e **System.Net.Mail** sono spazi dei nomi di .NET Framework e sono inclusi perché contengono tipi utilizzati normalmente con le API SendGrid.

    using System.Net;
    using System.Net.Mail;
    using SendGridMail;
    using SendGridMail.Transport;

Procedura: Creare un messaggio di posta elettronicaProcedura: Creare un messaggio di posta elettronica
------------------------------------------------------------------------------------------------------

Utilizzare il metodo statico **SendGrid.GetInstance** per creare un messaggio di posta elettronica di tipo **SendGrid**. Dopo aver creato il messaggio, è possibile utilizzare le proprietà e i metodi di **SendGrid** per impostare i valori, inclusi il mittente, il destinatario, l'oggetto e il corpo del messaggio di posta elettronica.

Nell'esempio seguente viene illustrato come creare un oggetto di posta elettronica completamente popolato:

    // Setup the email properties.
    var from = new MailAddress("john@contoso.com");
    var to   = new MailAddress[] { new MailAddress("jeff@contoso.com") };
    var cc   = new MailAddress[] { new MailAddress("anna@contoso.com") };
    var bcc  = new MailAddress[] { new MailAddress("peter@contoso.com") };
    var subject = "Testing the SendGrid Library";
    var html    = "<p>Hello World!</p>";
    var text = "Hello World plain text!";
    var transport = SendGridMail.TransportType.SMTP;

    // Create an email, passing in the eight properties as arguments.
    SendGrid myMessage = SendGrid.GetInstance(from, to, cc, bcc, subject, html, text, transport);

Nell'esempio seguente viene illustrato come creare un oggetto di posta elettronica vuoto:

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
     
    // Add the message properties.
    MailAddress sender = new MailAddress(@"John Smith <john@contoso.com>");
    myMessage.From = sender;
     
    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
     {
         @"Jeff Smith <jeff@contoso.com>",
        @"Anna Lidman <anna@contoso.com>",
        @"Peter Saddow <peter@contoso.com>"
     };
      
     foreach (string recipient in recipients)
     {
         myMessage.AddTo(recipient);
     }
      
     // Add a message body in HTML format.
    myMessage.Html = "<p>Hello World!</p>";

    // Add the subject.
    myMessage.Subject = "Testing the SendGrid Library";

Per ulteriori informazioni su tutte le proprietà e i metodi supportati dal tipo **SendGrid**, vedere [sendgrid-csharp](https://github.com/sendgrid/sendgrid-csharp) su GitHub.

Procedura: Inviare un messaggio di posta elettronicaProcedura: Inviare un messaggio di posta elettronica
--------------------------------------------------------------------------------------------------------

Dopo aver creato un messaggio di posta elettronica, è possibile inviarlo tramite SMTP o con l'API Web di SendGrid. Per informazioni dettagliate sui vantaggi e gli svantaggi di ogni API, vedere [il confronto tra SMTP e l'API Web](http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/) nella documentazione di SendGrid.

L'invio di messaggi di posta elettronica con uno dei due protocolli richiede l'immissione delle credenziali dell'account SendGrid (nome utente e password). Il codice che segue illustra come eseguire il wrapping delle credenziali in un oggetto **NetworkCredential**:

    // Create network credentials to access your SendGrid account.
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);

Per inviare un messaggio di posta elettronica, utilizzare il metodo **Deliver** sulla classe **SMTP**, che utilizza il protocollo SMTP, o sulla classe di trasporto **REST**, che chiama l'API Web di SendGrid. Negli esempi seguenti viene illustrato come inviare un messaggio sia tramite SMTP che con l'API Web.

### SMTP

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an SMTP transport for sending email.
    var transportSMTP = SMTP.GetInstance(credentials);

    // Send the email.
    transportSMTP.Deliver(myMessage);

### API Web

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create a REST transport for sending email.
    var transportREST = Web.GetInstance(credentials);

    // Send the email.
    transportREST.Deliver(myMessage);

Procedura: Aggiungere un allegatoProcedura: Aggiungere un allegato
------------------------------------------------------------------

Per aggiungere allegati a un messaggio, chiamare il metodo **AddAttachment** e specificare il nome e il percorso del file da allegare. È possibile includere più allegati chiamando questo metodo una volta per ogni file che si desidera allegare. Nell'esempio seguente viene illustrata l'aggiunta di un allegato a un messaggio:

    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisiProcedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi
----------------------------------------------------------------------------------------------------------------------------------------------------------------

SendGrid fornisce funzionalità di posta elettronica aggiuntive attraverso l'utilizzo di filtri. Si tratta di impostazioni che è possibile aggiungere a un messaggio di posta elettronica per abilitare funzionalità specifiche, ad esempio il monitoraggio dei clic, Google Analytics, il monitoraggio delle sottoscrizioni e così via. Per un elenco completo dei filtri, vedere [Impostazioni dei filtri](http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/).

Per applicare i filtri ai messaggi di posta elettronica di **SendGrid**, utilizzare i metodi implementati come parte della classe **SendGrid**. Prima di abilitare i filtri per un messaggio di posta elettronica, è necessario inizializzare l'elenco dei filtri disponibili chiamando il metodo **InitalizeFilters**.

Negli esempi seguenti vengono illustrati i filtri per abilitare il piè di pagina e per il monitoraggio dei clic:

### Piè di pagina

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### Monitoraggio dei clic

    // Create the email object first, then add the properties.
    SendGrid myMessage = SendGrid.GetInstance();
    myMessage.AddTo("anna@contoso.com");
    myMessage.From = new MailAddress("john@contoso.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.windowsazure.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";

    myMessage.InitializeFilters();
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);


Procedura: Utilizzare servizi aggiuntivi forniti da SendGridProcedura: Utilizzare servizi aggiuntivi forniti da SendGrid
------------------------------------------------------------------------------------------------------------------------

SendGrid offre API basate sul Web che è possibile utilizzare per sfruttare altre funzionalità di SendGrid dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione sull'API SendGrid](http://docs.sendgrid.com/documentation/api/).

Passaggi successiviPassaggi successivi
--------------------------------------

A questo punto, dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid, utilizzare i collegamenti seguenti per ulteriori informazioni.

-   Repository della libreria C\# di SendGrid: [sendgrid-csharp](https://github.com/sendgrid/sendgrid-csharp)
-   Documentazione sull'API SendGrid: <http://docs.sendgrid.com/documentation/api/>
-   Offerta speciale SendGrid per i clienti di Azure: <http://sendgrid.com>

