---
title: Come usare il servizio e-mail SendGrid (.NET) | Microsoft Docs
description: Informazioni su come inviare messaggi di posta elettronica con il servizio di posta elettronica SendGrid disponibile in Azure. Gli esempi di codice sono scritti in C# mediante l&quot;API .NET.
services: app-service\web
documentationcenter: .net
author: thinkingserious
manager: dwrede
editor: 
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/14/2016
ms.author: team-pi@sendgrid.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ae05e89346fe6bb85408cd560505dab3c03837cc


---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Come inviare messaggi di posta elettronica usando SendGrid con Azure
## <a name="overview"></a>Panoramica
Questa guida illustra come eseguire attività di programmazione comuni con il servizio di posta elettronica SendGrid in Azure. Negli esempi, scritti in C\#
, viene usata l'API .NET. Gli scenari presentati includono **creazione di e-mail**, **invio di e-mail**, **aggiunta di allegati** e **uso di filtri**. Per altre informazioni su SendGrid e sull'invio di e-mail, vedere la sezione [Passaggi successivi][Passaggi successivi] .

## <a name="what-is-the-sendgrid-email-service"></a>Informazioni sul servizio di posta elettronica SendGrid
SendGrid è un [servizio di posta elettronica basato sul cloud] che offre [recapito affidabile di messaggi di posta elettronica transazionali], scalabilità e analisi in tempo reale, oltre ad API flessibili che agevolano l'integrazione personalizzata. Gli scenari di utilizzo comuni di SendGrid includono:

* Invio automatico di ricevute ai clienti.
* Amministrazione di liste di distribuzione per l'invio mensile ai clienti di volantini elettronici e offerte speciali.
* Raccolta di metriche in tempo reale per elementi quali indirizzi di posta elettronica bloccati e velocità di risposta al cliente.
* Generazione di report per agevolare l'identificazione delle tendenze.
* Inoltro di richieste dei clienti.
* Elaborazione di messaggi di posta elettronica in arrivo.

Per altre informazioni, vedere [https://sendgrid.com](https://sendgrid.com) o la [libreria C#][sendgrid-csharp]

## <a name="create-a-sendgrid-account"></a>Creare un account SendGrid
[!INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Fare riferimento alla libreria di classi .NET di SendGrid
Il [pacchetto NuGet di SendGrid](https://www.nuget.org/packages/Sendgrid) è il modo più semplice per recuperare l'API SendGrid e configurare l'applicazione con tutte le dipendenze. NuGet è un'estensione di Visual Studio inclusa in Microsoft Visual Studio 2015 che semplifica l'installazione e l'aggiornamento di librerie e strumenti. 

> [!NOTE]
> Per installare NuGet se si esegue una versione di Visual Studio precedente rispetto a Visual Studio 2015, visitare il sito [http://www.nuget.org](http://www.nuget.org)e fare clic su **Install NuGet** .
> 
> 

Per installare il pacchetto NuGet di SendGrid, eseguire le operazioni seguenti:

1. Creato un nuovo progetto.
   
   ![Creare un nuovo progetto][create-new-project]
2. Selezionare un modello.
   
   ![Selezionare un modello:][select-a-template]
3. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Gestisci pacchetti NuGet**.
4. Cercare **SendGrid** e selezionare la voce **SendGrid** nell'elenco dei risultati.
   
   ![pacchetto NuGet di SendGrid][SendGrid-NuGet-package]
5. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.

La libreria di classi .NET di SendGrid è denominata **SendGridMail**. Include gli spazi dei nomi seguenti:

* **SendGridMail** per la creazione e l'utilizzo degli elementi di posta elettronica.
* **SendGridMail.Transport** per l'invio di e-mail tramite il protocollo **SMTP** o HTTP 1.1 con **Web/REST**.

Aggiungere le dichiarazioni seguenti dello spazio dei nomi del codice all'inizio del file C\# in cui si desidera accedere al servizio e-mail SendGrid in modo programmatico.
**System.Net** e **System.Net.Mail** sono spazi dei nomi di .NET Framework e sono inclusi perché contengono tipi usati normalmente con le API SendGrid.

    using System;
    using System.Net;
    using System.Net.Mail;
    using SendGrid;

## <a name="how-to-create-an-email"></a>Procedura: Creare un messaggio di posta elettronica
Usare l'oggetto **SendGridMessage** per creare un messaggio di posta elettronica. Dopo aver creato l'oggetto, è possibile impostare proprietà e metodi, inclusi il mittente, il destinatario, l'oggetto e il corpo del messaggio di posta elettronica.

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

Per altre informazioni su tutti i metodi e le proprietà supportati dal tipo **SendGrid**, vedere [sendgrid-csharp][sendgrid-csharp] su GitHub.

## <a name="how-to-send-an-email"></a>Procedura: Inviare un messaggio di posta elettronica
Dopo aver creato un messaggio di posta elettronica, è possibile inviarlo tramite SMTP o con l'API Web di SendGrid. In alternativa, è possibile [usare la libreria .NET integrata](https://sendgrid.com/docs/Code_Examples/csharp.html).

Per l'invio di messaggi di posta elettronica è necessario specificare le credenziali dell'account SendGrid (nome utente e password) o la chiave API di SendGrid. La Chiave API è il metodo preferito. Per informazioni dettagliate su come configurare le chiavi API, visitare la [documentazione](https://sendgrid.com/docs/Classroom/Send/api_keys.html)

È possibile archiviare le credenziali tramite il Portale di Azure facendo clic su CONFIGURA e aggiungere le coppie chiave/valore nella sezione "impostazioni app".

 ![Impostazioni app di Azure][azure_app_settings]

 Quindi, è possibile accedervi come indicato di seguito: 

    var username = System.Environment.GetEnvironmentVariable("SENDGRID_USERNAME"); 
    var pswd = System.Environment.GetEnvironmentVariable("SENDGRID_PASSWORD");
    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");

Utilizzo delle credenziali:

    // Create network credentials to access your SendGrid account
    var username = "your_sendgrid_username";
    var pswd = "your_sendgrid_password";

    var credentials = new NetworkCredential(username, pswd);
    // Create an Web transport for sending email.
    var transportWeb = new Web(credentials);

Utilizzo della chiave API:

    var apiKey = "your_sendgrid_api_key";  
    // create a Web transport, using API Key
    var transportWeb = new Web(apiKey);


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

    // Send the email, which returns an awaitable task.
    transportWeb.DeliverAsync(myMessage);

    // If developing a Console Application, use the following
    // transportWeb.DeliverAsync(mail).Wait();

## <a name="how-to-add-an-attachment"></a>Procedura: Aggiungere un allegato
Per aggiungere allegati a un messaggio, chiamare il metodo **AddAttachment** e specificare il nome e il percorso del file da allegare.
È possibile includere più allegati chiamando questo metodo una volta per ogni file che si desidera allegare. Nell'esempio seguente viene illustrata l'aggiunta di un allegato a un messaggio:

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    myMessage.AddAttachment(@"C:\file1.txt");

È inoltre possibile aggiungere allegati dallo **Stream**dei dati. Per effettuare questa operazione, chiamare lo stesso metodo usato nell'esempio precedente, **AddAttachment**, ma passare lo Stream dei dati e il nome del file che il metodo dovrà mostrare come nel messaggio. In questo caso occorre aggiungere la libreria di System.IO.

    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    using (var attachmentFileStream = new FileStream(@"C:\file.txt", FileMode.Open))
    {
        myMessage.AddAttachment(attachmentFileStream, "My Cool File.txt");
    }


## <a name="how-to-use-apps-to-enable-footers-tracking-and-analytics"></a>Procedura: Usare app per abilitare piè di pagina, monitoraggio e analisi
SendGrid fornisce funzionalità di posta elettronica aggiuntive attraverso l'uso di app. Si tratta di impostazioni che è possibile aggiungere a un messaggio di posta elettronica per abilitare funzionalità specifiche, ad esempio il monitoraggio dei clic, Google Analytics, il monitoraggio delle sottoscrizioni e così via. Per un elenco completo delle app, vedere [Impostazioni app][Impostazioni app] (Impostazioni delle app).

Per applicare le app alle e-mail di **SendGrid**, usare i metodi implementati come parte della classe **SendGrid**.

Negli esempi seguenti vengono illustrati i filtri per abilitare il piè di pagina e per il monitoraggio dei clic:

### <a name="footer"></a>Piè di pagina
    // Create the email object first, then add the properties.
    SendGridMessage myMessage = new SendGridMessage();
    myMessage.AddTo("anna@example.com");
    myMessage.From = new MailAddress("john@example.com", "John Smith");
    myMessage.Subject = "Testing the SendGrid Library";
    myMessage.Text = "Hello World!";

    // Add a footer to the message.
    myMessage.EnableFooter("PLAIN TEXT FOOTER", "<p><em>HTML FOOTER</em></p>");

### <a name="click-tracking"></a>Monitoraggio dei clic
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

## <a name="how-to-use-additional-sendgrid-services"></a>Procedura: Usare servizi aggiuntivi forniti da SendGrid
SendGrid offre API basate sul Web e hook Web che è possibile usare per sfruttare altre funzionalità di SendGrid dall'applicazione Azure. Per informazioni dettagliate, vedere la [documentazione sull'API SendGrid][documentazione sull'API SendGrid].

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid, usare i collegamenti seguenti per altre informazioni.

* Repository della libreria C\# di SendGrid: [sendgrid-csharp][sendgrid-csharp]
* Documentazione relativa all'API SendGrid: <https://sendgrid.com/docs>
* Offerta speciale SendGrid per i clienti di Azure: [https://sendgrid.com](https://sendgrid.com)

[Passaggi successivi]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[Procedura: Usare filtri per abilitare piè di pagina, monitoraggio e analisi]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[offerta speciale]: https://www.sendgrid.com/windowsazure.html

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/create_new_project.png
[select-a-template]: ./media/sendgrid-dotnet-how-to-send-email/select_a_template.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid_nuget.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/app_settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[Impostazioni app]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[documentazione sull'API SendGrid]: https://sendgrid.com/docs

[servizio di posta elettronica basato sul cloud]: https://sendgrid.com/email-solutions
[recapito affidabile di messaggi di posta elettronica transazionali]: https://sendgrid.com/transactional-email




<!--HONumber=Nov16_HO3-->


