<properties linkid="dev-net-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (.NET) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid .NET, Azure email .NET, Azure SendGrid C#, Azure email C#" description="Learn how send email with the SendGrid email service on Azure. Code samples written in C# and use the .NET API." metaCanonical="" services="" documentationCenter=".NET" title="How to Send Email Using SendGrid with Azure" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="carolz" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Come inviare messaggi di posta elettronica utilizzando SendGrid con Azure

Ultimo aggiornamento: 21 agosto 2014

Questa guida illustra come eseguire attività di programmazione comuni con il
servizio di posta elettronica SendGrid in Azure. Negli esempi, scritti in C#,
viene usata l'API .NET. Gli scenari presentati includono **creazione di
messaggi di posta elettronica**, **invio di messaggi di posta elettronica**,
**aggiunta di allegati** e **uso di filtri**. Per ulteriori informazioni su SendGrid
e sull'invio di messaggi di posta elettronica, vedere la sezione [Passaggi successivi][Passaggi successivi].

## <a name="toc"></a>Sommario

[Informazioni sul servizio di posta elettronica SendGrid][Informazioni sul servizio di posta elettronica SendGrid]
[Creazione di un account SendGrid][Creazione di un account SendGrid]
[Riferimento alla libreria di classi .NET di SendGrid][Riferimento alla libreria di classi .NET di SendGrid]
[Procedura: Creare un messaggio di posta elettronica][Procedura: Creare un messaggio di posta elettronica]
[Procedura: Inviare un messaggio di posta elettronica][Procedura: Inviare un messaggio di posta elettronica]
[Procedura: Aggiungere un allegato][Procedura: Aggiungere un allegato]
[Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi][Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi]
[Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid][Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid]
[Passaggi successivi][Passaggi successivi]

## <a name="whatis"></a><span class="short-header">Informazioni sul servizio di posta elettronica SendGrid</span>

SendGrid è un [servizio di posta elettronica basato sul cloud][servizio di posta elettronica basato sul cloud] che offre [recapito affidabile
di messaggi di posta elettronica transazionali][recapito affidabile
di messaggi di posta elettronica transazionali], scalabilità e analisi in tempo reale,
oltre ad API flessibili che agevolano l'integrazione personalizzata. Gli scenari di
utilizzo comuni di SendGrid includono:

-   Invio automatico di ricevute ai clienti.
-   Amministrazione di liste di distribuzione per l'invio mensile
    ai clienti di volantini elettronici e offerte speciali.
-   Raccolta di metriche in tempo reale, ad esempio per indirizzi di
    posta elettronica bloccati e velocità di risposta al cliente.
-   Generazione di report per agevolare l'identificazione delle tendenze.
-   Inoltro di richieste dei clienti.

Per altre informazioni, visitare il sito [][]<http://sendgrid.com></a>.

## <a name="createaccount"></a><span class="short-header">Creare un account SendGrid</span>

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"></a><span class="short-header">Riferimento alla libreria di classi .NET di SendGrid</span>Riferimento alla libreria di classi .NET di SendGrid

Il [pacchetto NuGet di SendGrid][pacchetto NuGet di SendGrid] è il modo più semplice per recuperare l'API
SendGrid e configurare l'applicazione con tutte le dipendenze. NuGet è
un'estensione di Visual Studio inclusa in Microsoft Visual Studio 2012 che semplifica l'installazione
e l'aggiornamento di librerie e strumenti.

<div class="dev-callout">
<b>Nota</b>
<p>Per
installare NuGet se si esegue una versione di Visual Studio precedente rispetto a Visual Studio 2012, visitare il sito <a href="http://www.nuget.org">http://www.nuget.org</a> e fare clic sul pulsante <b>Install
NuGet</b>.</p>
</div>

Per installare il pacchetto NuGet di SendGrid, eseguire le operazioni seguenti:

1.  In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti**, quindi fare clic su
    **Manage NuGet Packages**.

2.  Nel riquadro sinistro della finestra di dialogo **Manage NuGet Packages** fare clic su **Online**.

3.  Cercare **SendGrid** e selezionare la voce **SendGrid** nell'elenco
    dei risultati.

    ![Pacchetto NuGet di SendGrid][Pacchetto NuGet di SendGrid]

4.  Fare clic su **Installa** per completare l'installazione, quindi chiudere
    questa finestra di dialogo.

La libreria di classi .NET di SendGrid è denominata **SendGridMail**. Include gli
spazi dei nomi seguenti:

-   **SendGridMail** per la creazione e l'utilizzo degli elementi di posta elettronica.
-   **SendGridMail.Transport** per l'invio di posta elettronica tramite il protocollo
    **SMTP** o HTTP 1.1 con
    **Web/REST**.

Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio
del file C# in cui si desidera programmare l'accesso al servizio di posta elettronica SendGrid.
**System.Net** e **System.Net.Mail** sono spazi dei nomi di .NET Framework
che sono inclusi perché contengono tipi usati normalmente
con le API SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="createemail"></a><span class="short-header">Procedura: Creare un messaggio di posta elettronica</span>Procedura: Creare un messaggio di posta elettronica

Usare il metodo statico **SendGrid.GetInstance** per creare un
messaggio di posta elettronica di tipo **SendGrid**. Dopo aver creato il messaggio, è
possibile usare le proprietà e i metodi di **SendGrid** per impostare i valori, inclusi il
mittente, il destinatario, l'oggetto e il corpo del messaggio
di posta elettronica.

L'esempio seguente mostra come creare un oggetto di posta elettronica
completamente popolato:

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

Per ulteriori informazioni su tutte le proprietà e i metodi supportati dal tipo
**SendGrid**, vedere [sendgrid-csharp][sendgrid-csharp] su GitHub.

## <a name="sendemail"></a><span class="short-header">Procedura: Inviare un messaggio di posta elettronica</span>

Dopo aver creato un messaggio di posta elettronica, è possibile
inviarlo tramite SMTP o con l'API Web di SendGrid. In alternativa, è possibile [usare la libreria .NET integrata][usare la libreria .NET integrata].

Per l'invio di messaggi di posta elettronica
è necessario specificare le credenziali dell'account SendGrid (nome utente e password). Il codice seguente
illustra come eseguire il wrapping delle credenziali in un oggetto
**NetworkCredential**:

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

## <a name="addattachment"></a><span class="short-header">Procedura: Aggiungere un allegato</span>Procedura: Aggiungere un allegato

Per aggiungere allegati a un messaggio, chiamare il metodo **AddAttachment**
e specificare il nome e il percorso del file da allegare.
È possibile includere più allegati chiamando questo metodo una volta per ogni file che
si desidera allegare. L'esempio seguente illustra come aggiungere
un allegato a un messaggio:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

È inoltre possibile aggiungere allegati dallo **Stream** dei dati. Per effettuare questa operazione, chiamare lo stesso metodo usato nell'esempio precedente, **AddAttachment**, ma passare lo Stream dei dati e il nome del file che il metodo dovrà mostrare come nel messaggio.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }

## <a name="usefilters"></a><span class="short-header">Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi</span>

SendGrid fornisce funzionalità di posta elettronica aggiuntive mediante
l'uso di filtri. Si tratta di impostazioni che è possibile aggiungere a un messaggio
di posta elettronica per abilitare funzionalità specifiche, ad esempio il
monitoraggio dei clic, Google Analytics, il monitoraggio delle sottoscrizioni e così via. Per un elenco completo dei filtri, vedere
[Impostazioni dei filtri][Impostazioni dei filtri].

Per applicare i filtri ai messaggi di posta elettronica di **SendGrid** usare i metodi
implementati come parte della classe **SendGrid**.

Gli esempi seguenti illustrano i filtri per il piè di pagina e il monitoraggio
dei clic:

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

## <a name="useservices"></a><span class="short-header">Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid</span>

SendGrid offre API basate sul Web che è possibile usare per sfruttare
altre funzionalità di SendGrid dall'applicazione Azure. Per
informazioni dettagliate, vedere la [documentazione dell'API SendGrid][documentazione dell'API SendGrid].

## <a name="nextsteps"></a><span class="short-header">Passaggi successivi</span>Passaggi successivi

A questo punto, dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid, usare i
collegamenti seguenti per ulteriori informazioni.

-   Repository della libreria C# di SendGrid: [sendgrid-csharp][sendgrid-csharp]
-   Documentazione dell'API SendGrid: <http://docs.sendgrid.com/documentation/api/>
-   Offerta speciale SendGrid per i clienti di Azure: [][]<http://sendgrid.com></a>

  [Passaggi successivi]: #nextsteps
  [Informazioni sul servizio di posta elettronica SendGrid]: #whatis
  [Creazione di un account SendGrid]: #createaccount
  [Riferimento alla libreria di classi .NET di SendGrid]: #reference
  [Procedura: Creare un messaggio di posta elettronica]: #createemail
  [Procedura: Inviare un messaggio di posta elettronica]: #sendemail
  [Procedura: Aggiungere un allegato]: #addattachment
  [Procedura: Utilizzare filtri per abilitare piè di pagina, monitoraggio e analisi]: #usefilters
  [Procedura: Utilizzare servizi aggiuntivi forniti da SendGrid]: #useservices
  [servizio di posta elettronica basato sul cloud]: http://sendgrid.com/solutions
  []: http://sendgrid.com
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [pacchetto NuGet di SendGrid]: https://www.nuget.org/packages/Sendgrid
  [Pacchetto NuGet di SendGrid]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid01.png
  [sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
  [usare la libreria .NET integrata]: https://sendgrid.com/docs/Code_Examples/csharp.html
  [Impostazioni dei filtri]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [documentazione dell'API SendGrid]: http://docs.sendgrid.com/documentation/api/
