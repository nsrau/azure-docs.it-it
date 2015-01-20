<properties urlDisplayName="SendGrid Email Service" pageTitle="Come usare il servizio di posta elettronica SendGrid (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Informazioni su come inviare messaggi con il servizio di posta elettronica SendGrid in Azure. Esempi di codice scritti in C# e che usano l'API .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="erikre" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/29/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />





# Come inviare messaggi di posta elettronica usando SendGrid con Azure

Ultimo aggiornamento: lunedì 27 ottobre 2014

In questa guida viene illustrato come eseguire attività di programmazione comuni con il servizio di posta elettronica SendGrid in Azure. Negli esempi, scritti in C#, viene usata l'API .NET. Gli scenari presentati includono **creazione di messaggi di posta elettronica**, **invio di messaggi di posta elettronica**, **aggiunta di allegati** e **uso di filtri**. Per altre informazioni su SendGrid e sull'invio di messaggi di posta elettronica, vedere la sezione [Passaggi successivi][].

<h2><a name="toc"></a>Sommario</h2>

[Informazioni sul servizio di posta elettronica SendGrid][]   
[Creare un account SendGrid][]   
[Fare riferimento alla libreria di classi .NET di SendGrid][]   
[Procedura: Creare un messaggio di posta elettronica][]   
[Procedura: Inviare un messaggio di posta elettronica][]   
[Procedura: Aggiungere un allegato][]   
[Procedura: Usare app per abilitare piè di pagina, monitoraggio e analisi][]   
[Procedura: Usare servizi aggiuntivi forniti da SendGrid][]   
[Passaggi successivi][]

<h2><a name="whatis"></a><span  class="short-header">Informazioni sul servizio di posta elettronica SendGrid</span></h2>

SendGrid è un [servizio di posta elettronica basato sul cloud] che offre [recapito affidabile di messaggi di posta elettronica transazionali], scalabilità e analisi in tempo reale, oltre ad API flessibili che agevolano l'integrazione personalizzata. Gli scenari di utilizzo comuni di SendGrid includono:

-   Invio automatico di ricevute ai clienti.
-   Amministrazione di liste di distribuzione per l'invio mensile ai clienti di volantini elettronici e offerte speciali.
-   Raccolta di metriche in tempo reale per elementi quali indirizzi di posta elettronica bloccati e velocità di risposta al cliente.
-   Generazione di report per agevolare l'identificazione delle tendenze.
-   Inoltro di richieste dei clienti.
-   Elaborazione di messaggi di posta elettronica in arrivo.

Per altre informazioni, vedere [https://sendgrid.com](https://sendgrid.com).

<h2><a name="createaccount"></a>Creare un account SendGrid</h2>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

<h2><a name="reference"></a>Fare riferimento alla libreria di classi .NET di SendGrid</h2>

Il [pacchetto NuGet di SendGrid](https://www.nuget.org/packages/Sendgrid) è il modo più semplice per recuperare l'API SendGrid e configurare l'applicazione con tutte le dipendenze. NuGet è un'estensione di Visual Studio inclusa in Microsoft Visual Studio 2012 che semplifica l'installazione e l'aggiornamento di librerie e strumenti. 

<div class="dev-callout">
<b>Nota</b>
<p>Per installare NuGet se si esegue una versione di Visual Studio precedente rispetto a Visual Studio 2012, visitare il sito <a href="http://www.nuget.org">http://www.nuget.org</a> e fare clic su <b>Install NuGet</b>.</p>
</div>

Per installare il pacchetto NuGet di SendGrid, eseguire le operazioni seguenti:

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti**, quindi scegliere **Gestisci pacchetti NuGet**.

2.  Nel riquadro sinistro della finestra di dialogo **Gestisci pacchetti NuGet** fare clic su **Online**.

3.  Cercare **SendGrid** e selezionare la voce **SendGrid** nell'elenco dei risultati (la versione corrente è 5.0.0).

    ![SendGrid NuGet package][SendGrid-NuGet-package]

4.  Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.

La libreria di classi .NET di SendGrid è denominata **SendGridMail**. Include gli spazi dei nomi seguenti:

-   **SendGridMail** per la creazione e l'uso degli elementi di posta elettronica.
-   **SendGridMail.Transport** per l'invio di posta elettronica tramite il protocollo **SMTP** o HTTP 1.1 con **Web/REST**.

Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si vuole accedere al servizio di posta elettronica SendGrid a livello di codice:
**System.Net** e **System.Net.Mail** sono spazi dei nomi di .NET Framework e sono inclusi perché contengono tipi usati normalmente con le API SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

<h2><a name="createemail"></a>Procedura: Creare un messaggio di posta elettronica</h2>

Usare il metodo statico **SendGrid.GetInstance** per creare un messaggio di posta elettronica di tipo **SendGrid**. Dopo aver creato il messaggio, è possibile usare le proprietà e i metodi di **SendGrid** per impostare i valori, inclusi il mittente, il destinatario, l'oggetto e il corpo del messaggio di posta elettronica.

Nell'esempio seguente viene illustrato come creare un oggetto di posta elettronica completamente popolato:

    // Create the email object first, then add the properties.
    var myMessage = new SendGridMessage();

    // Add the message properties.
    myMessage.From = new MailAddress("john@example.com");

    // Add multiple addresses to the To field.
    List<String> recipients = new List<String>
    {
        @"Jeff Smith <jeff@example.com>",
        @"Anna Lidman <anna@example.com>",
        @"Peter Saddow <peter@example.com>"
    };

    myMessage.AddTo(recipients);

    myMessage.Subject = "Testing the SendGrid Library";

    //Add the HTML and Text bodies
    myMessage.Html = "<p>Hello World!</p>";
    myMessage.Text = "Hello World plain text!";

Per altre informazioni su tutte le proprietà e i metodi supportati dal tipo **SendGrid**, vedere [sendgrid-csharp][] su GitHub.

<h2><a name="sendemail"></a>Procedura: Inviare un messaggio di posta elettronica</h2>

Dopo aver creato un messaggio di posta elettronica, è possibile inviarlo tramite SMTP o con l'API Web di SendGrid. In alternativa, è possibile usare la [libreria .NET integrata](https://sendgrid.com/docs/Code_Examples/csharp.html).

Per l'invio di messaggi di posta elettronica è necessario specificare le credenziali dell'account SendGrid (nome utente e password). Il codice che segue illustra come eseguire il wrapping delle credenziali in un oggetto **NetworkCredential**:
    
    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    /* Alternatively, you may store these credentials via your Azure portal
       by clicking CONFIGURE and adding the key/value pairs under "app settings".
       Then, you may access them as follows: 
       var username = System.Environment.GetEnvironmentVariable("SENDGRID_USER"); 
       var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASS");
       assuming you named your keys SENDGRID_USER and SENDGRID_PASS */

    var credentials = new NetworkCredential(username, pswd);

Gli esempi seguenti mostrano come inviare un messaggio con l'API Web.

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Create credentials, specifying your user name and password.
    var credentials = new NetworkCredential("username", "password");

    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

    // Send the email.
    // You can also use the **DeliverAsync** method, which returns an awaitable task.
    transportWeb.Deliver(myMessage);

<h2><a name="addattachment"></a>Procedura: Aggiungere un allegato</h2>

Per aggiungere allegati a un messaggio, chiamare il metodo **AddAttachment**  e specificare il nome e il percorso del file da allegare.
È possibile includere più allegati chiamando questo metodo una volta per ogni file che si desidera allegare. L'esempio seguente illustra come aggiungere un allegato a un messaggio:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");
    
È anche possibile aggiungere allegati dallo **Stream** dei dati. Per effettuare questa operazione, chiamare lo stesso metodo usato nell'esempio precedente, **AddAttachment**, ma passare lo Stream dei dati e il nome file che il metodo dovrà mostrare come nel messaggio. In questo caso occorre aggiungere la libreria di System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


<h2><a name="usefilters"></a><span  class="short-header">Procedura: Usare app per abilitare piè di pagina, monitoraggio e analisi</span></h2>

SendGrid fornisce funzionalità di posta elettronica aggiuntive attraverso l'uso di app. Si tratta di impostazioni che è possibile aggiungere a un messaggio di posta elettronica per abilitare funzionalità specifiche, ad esempio il monitoraggio dei clic, Google Analytics, il monitoraggio delle sottoscrizioni e così via. Per un elenco completo delle app, vedere [Impostazioni app][].

Per applicare le app ai messaggi di posta elettronica di **SendGrid**, usare i metodi implementati come parte della classe **SendGrid**.

Negli esempi seguenti vengono illustrati i filtri per abilitare il piè di pagina e per il monitoraggio dei clic:

### Piè di pagina

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### Monitoraggio dei clic

    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Html = "<p><a href=\"http://www.example.com\">Hello World Link!</a></p>";
    myMessage.Text = "Hello World!";
    
    // true indicates that links in plain text portions of the email 
    // should also be overwritten for link tracking purposes. 
    myMessage.EnableClickTracking(true);

<h2><a name="useservices"></a>Procedura: Usare servizi aggiuntivi forniti da SendGrid</h2>

SendGrid offre API basate sul Web e hook Web che è possibile usare per sfruttare altre funzionalità di SendGrid dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione dell'API SendGrid][].

<h2><a name="nextsteps"></a>Passaggi successivi</h2>

A questo punto, dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid, usare i collegamenti seguenti per altre informazioni.

* Repository della libreria C# di SendGrid: [sendgrid-csharp][]
*   Documentazione sull'API SendGrid: <https://sendgrid.com/docs>
*   Offerta speciale SendGrid per i clienti di Azure: [https://sendgrid.com](https://sendgrid.com)

  [Passaggi successivi]: #nextsteps
  [Informazioni sul servizio di posta elettronica SendGrid]: #whatis
  [Creare un account SendGrid]: #createaccount
  [Fare riferimento alla libreria di classi .NET di SendGrid]: #reference
  [Procedura: Creare un messaggio di posta elettronica]: #createemail
  [Procedura: Inviare un messaggio di posta elettronica]: #sendemail
  [Procedura: Aggiungere un allegato]: #addattachment
  [Procedura: Usare filtri per abilitare piè di pagina, monitoraggio e analisi]: #usefilters
  [Procedura: Usare servizi aggiuntivi forniti da SendGrid]: #useservices
  
  
  [offerta speciale]: https://www.sendgrid.com/windowsazure.html
  
  
  
  [Pacchetto NuGet di SendGrid]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [SMTP e API Web]: https://sendgrid.com/docs/Integrate/index.html
  [Impostazioni app]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [Documentazione sull'API SendGrid]: https://sendgrid.com/docs
  
  [servizio di posta elettronica basato sul cloud]: https://sendgrid.com/email-solutions
  [recapito transazionale di posta elettronica]: https://sendgrid.com/transactional-email

<!--HONumber=35.2-->
