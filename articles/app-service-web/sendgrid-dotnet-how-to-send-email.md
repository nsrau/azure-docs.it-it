---
title: Come usare il servizio e-mail SendGrid (.NET) | Microsoft Docs
description: Informazioni su come inviare messaggi di posta elettronica con il servizio di posta elettronica SendGrid disponibile in Azure. Gli esempi di codice sono scritti in C# mediante l&quot;API .NET.
services: app-service\web
documentationcenter: .net
author: thinkingserious
manager: erikre
editor: 
ms.assetid: 21bf4028-9046-476b-9799-3d3082a0f84c
ms.service: app-service-web
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/15/2017
ms.author: dx@sendgrid.com
translationtype: Human Translation
ms.sourcegitcommit: 9e62ed235d872738bc1a99ad33d977745c8b2d08
ms.openlocfilehash: 417ea0aa6315683f72239fafed0caad5c71ad2d6
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-send-email-using-sendgrid-with-azure"></a>Come inviare messaggi di posta elettronica usando SendGrid con Azure
## <a name="overview"></a>Panoramica
Questa guida illustra come eseguire attività di programmazione comuni con il servizio di posta elettronica SendGrid in Azure. Gli esempi sono scritti in C\#
e supportano .NET Standard 1.3. Gli scenari presentati includono la creazione di email, l'invio di email, l'aggiunta di allegati e l'abilitazione di diverse impostazioni di posta elettronica e rilevamento. Per altre informazioni su SendGrid e sull'invio di email vedere la sezione [Passaggi successivi][Next steps].

## <a name="what-is-the-sendgrid-email-service"></a>Informazioni sul servizio di posta elettronica SendGrid
SendGrid è un [servizio di posta elettronica basato sul cloud] che offre [recapito affidabile di messaggi di posta elettronica transazionali], scalabilità e analisi in tempo reale, oltre ad API flessibili che agevolano l'integrazione personalizzata. I casi d'uso comuni di SendGrid includono:

* Invio automatico di ricevute o conferme di acquisto ai clienti.
* Amministrazione di liste di distribuzione per inviare mensilmente volantini e promozioni ai clienti.
* Raccolta di metriche in tempo reale per elementi quali email bloccate ed engagement del cliente.
* Inoltro di richieste dei clienti.
* Elaborazione di messaggi di posta elettronica in arrivo.

Per altre informazioni visitare [https://sendgrid.com](https://sendgrid.com) o il repository GitHub [libreria C#][sendgrid-csharp] di SendGrid.

## <a name="create-a-sendgrid-account"></a>Creazione di un account SendGrid
[!INCLUDE [sendgrid-sign-up](../../includes/sendgrid-sign-up.md)]

## <a name="reference-the-sendgrid-net-class-library"></a>Fare riferimento alla libreria di classi .NET di SendGrid
Il [pacchetto NuGet di SendGrid](https://www.nuget.org/packages/Sendgrid) è il modo più semplice per recuperare l'API SendGrid e configurare l'applicazione con tutte le dipendenze. NuGet è un'estensione di Visual Studio inclusa in Microsoft Visual Studio 2015 e versioni successive che semplifica l'installazione e l'aggiornamento di librerie e strumenti.

> [!NOTE]
> Per installare NuGet se si esegue una versione di Visual Studio precedente rispetto a Visual Studio 2015, visitare il sito [http://www.nuget.org](http://www.nuget.org)e fare clic su **Install NuGet** .
>
>

Per installare il pacchetto NuGet di SendGrid, eseguire le operazioni seguenti:

1. Creare un **nuovo progetto** e selezionare un **modello**.

   ![Creare un nuovo progetto][create-new-project]
2. In **Esplora soluzioni** fare clic con il pulsante destro del mouse su **Riferimenti** e scegliere **Gestisci pacchetti NuGet**.

   ![pacchetto NuGet di SendGrid][SendGrid-NuGet-package]
3. Cercare **SendGrid** e selezionare la voce **SendGrid** nell'elenco dei risultati.
4. Selezionare la più recente versione stabile del pacchetto NuGet nell'elenco a discesa della versione per poter usare il modello a oggetti e le API indicati in questo articolo.

   ![Pacchetto SendGrid][sendgrid-package]
5. Fare clic su **Installa** per completare l'installazione, quindi chiudere questa finestra di dialogo.

La libreria di classe .NET di SendGrid è denominata **SendGrid**. Include gli spazi dei nomi seguenti:

* **SendGrid** per la comunicazione con l'API di SendGrid.
* **SendGrid.Helpers.Mail** per i metodi helper per creare facilmente oggetti SendGridMessage che specificano come inviare email.

Aggiungere le seguenti dichiarazioni dello spazio dei nomi del codice all'inizio del file C# in cui si desidera accedere al servizio di posta elettronica SendGrid a livello di codice:

    using SendGrid;
    using SendGrid.Helpers.Mail

## <a name="how-to-create-an-email"></a>Procedura: Creare un messaggio di posta elettronica
Usare l'oggetto **SendGridMessage** per creare un messaggio di posta elettronica. Dopo aver creato l'oggetto, è possibile impostare proprietà e metodi, inclusi il mittente, il destinatario, l'oggetto e il corpo del messaggio di posta elettronica.

Nell'esempio seguente viene illustrato come creare un oggetto di posta elettronica completamente popolato:

    var msg = new SendGridMessage();

    msg.SetFrom(new EmailAddress("dx@example.com", "SendGrid DX Team"));

    var recipients = new List<EmailAddress>
    {
        new EmailAddress(){ "jeff@example.com", "Jeff Smith" },
        new EmailAddress(){ "anna@example.com", "Anna Lidman" },
        new EmailAddress(){ "peter@example.com", "Peter Saddow" }
    };
    msg.AddTos(recipients);

    msg.SetSubject("Testing the SendGrid C# Library");

    msg.AddContent(MimeType.Text, "Hello World plain text!");
    msg.AddContent(MimeType.Html, "<p>Hello World!</p>");

Per altre informazioni su tutte le proprietà e i metodi supportati dal tipo **SendGrid**, vedere [sendgrid-csharp][sendgrid-csharp] in GitHub.

## <a name="how-to-send-an-email"></a>Procedura: Inviare un messaggio di posta elettronica
Dopo aver creato un'email, è possibile inviarla tramite l'API di SendGrid. In alternativa è possibile usare la [libreria integrata di .NET][NET-library].

Per inviare email, è necessario specificare la chiave API di SendGrid. Per informazioni dettagliate su come configurare le chiavi API, consultare la [documentazione][documentation] sulle chiavi API di SendGrid.

È possibile archiviare queste credenziali tramite il Portale di Azure facendo clic su Impostazioni applicazione e aggiungendo le coppie chiave-valore nella sezione Impostazioni app.

 ![Impostazioni app di Azure][azure_app_settings]

 Quindi, è possibile accedervi come indicato di seguito:

    var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
    var client = new SendGridClient(apiKey);

Gli esempi seguenti mostrano come inviare un messaggio con l'API Web.

    using System;
    using System.Threading.Tasks;
    using SendGrid;
    using SendGrid.Helpers.Mail;

    namespace Example
    {
        internal class Example
        {
            private static void Main()
            {
                Execute().Wait();
            }

            static async Task Execute()
            {
                var apiKey = System.Environment.GetEnvironmentVariable("SENDGRID_APIKEY");
                var client = new SendGridClient(apiKey);
                var msg = new SendGridMessage()
                {
                    From = new EmailAddress("test@example.com", "DX Team"),
                    Subject = "Hello World from the SendGrid CSharp SDK!",
                    PlainTextContent = "Hello, Email!",
                    HtmlContent = "<strong>Hello, Email!</strong>"
                };
                msg.AddTo(new EmailAddress("test@example.com", "Test User"));
                var response = await client.SendEmailAsync(msg);
            }
        }
    }

## <a name="how-to-add-an-attachment"></a>Procedura: Aggiungere un allegato
Per aggiungere allegati a un messaggio, chiamare il metodo **AddAttachment** e specificare almeno il nome del file e il contenuto con codifica Base64 da allegare. È possibile includere più allegati chiamando questo metodo una volta per ogni file che si desidera allegare o usando il metodo **AddAttachments**. Nell'esempio seguente viene illustrata l'aggiunta di un allegato a un messaggio:

    var banner2 = new Attachment()
    {
        Content = Convert.ToBase64String(raw_content),
        Type = "image/png",
        Filename = "banner2.png",
        Disposition = "inline",
        ContentId = "Banner 2"
    };
    msg.AddAttachment(banner2);

## <a name="how-to-use-mail-settings-to-enable-footers-tracking-and-analytics"></a>Procedura: Usare le impostazioni della posta elettronica per abilitare piè di pagina, rilevamento e analisi
SendGrid offre funzionalità email aggiuntive tramite l'uso di impostazioni della posta elettronica e di rilevamento. Si tratta di impostazioni che è possibile aggiungere a un' email per abilitare funzionalità specifiche come il rilevamento dei clic, Google Analytics, il rilevamento delle sottoscrizioni e così via. Per un elenco completo delle app, vedere la [documentazione sulle impostazioni][settings-documentation].

Per applicare le app alle email di **SendGrid**, usare i metodi implementati come parte della classe **SendGridMessage**. Negli esempi seguenti vengono illustrati i filtri per abilitare il piè di pagina e per il monitoraggio dei clic:

Negli esempi seguenti vengono illustrati i filtri per abilitare il piè di pagina e per il monitoraggio dei clic:

### <a name="footer"></a>Piè di pagina
    msg.SetFooterSetting(
                         true,
                         "Some Footer HTML",
                         "<strong>Some Footer Text</strong>");

### <a name="click-tracking"></a>Monitoraggio dei clic
    msg.SetClickTracking(true);

## <a name="how-to-use-additional-sendgrid-services"></a>Procedura: Usare servizi aggiuntivi forniti da SendGrid
SendGrid offre diverse API e webhook che si possono usare per sfruttare altre funzionalità dell'applicazione Azure. Per maggiori dettagli, vedere il [riferimento all'API SendGrid][SendGrid API documentation].

## <a name="next-steps"></a>Passaggi successivi
A questo punto, dopo aver appreso le nozioni di base del servizio di posta elettronica SendGrid, usare i collegamenti seguenti per altre informazioni.

* Repository della libreria C\# di SendGrid: [sendgrid-csharp][sendgrid-csharp]
* Documentazione relativa all'API SendGrid: <https://sendgrid.com/docs>

[Next steps]: #next-steps
[What is the SendGrid Email Service?]: #whatis
[Create a SendGrid Account]: #createaccount
[Reference the SendGrid .NET Class Library]: #reference
[How to: Create an Email]: #createemail
[How to: Send an Email]: #sendemail
[How to: Add an Attachment]: #addattachment
[How to: Use Filters to Enable Footers, Tracking, and Analytics]: #usefilters
[How to: Use Additional SendGrid Services]: #useservices

[create-new-project]: ./media/sendgrid-dotnet-how-to-send-email/new-project.png
[SendGrid-NuGet-package]: ./media/sendgrid-dotnet-how-to-send-email/reference.png
[sendgrid-package]: ./media/sendgrid-dotnet-how-to-send-email/sendgrid-package.png
[azure_app_settings]: ./media/sendgrid-dotnet-how-to-send-email/azure-app-settings.png
[sendgrid-csharp]: https://github.com/sendgrid/sendgrid-csharp
[SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
[App Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
[SendGrid API documentation]: https://sendgrid.com/docs/API_Reference/api_v3.html
[NET-library]: https://sendgrid.com/docs/Integrate/Code_Examples/v2_Mail/csharp.html#-Using-NETs-Builtin-SMTP-Library
[documentation]: https://sendgrid.com/docs/Classroom/Send/api_keys.html
[settings-documentation]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html

[servizio di posta elettronica basato sul cloud]: https://sendgrid.com/solutions
[recapito affidabile di messaggi di posta elettronica transazionali]: https://sendgrid.com/use-cases/transactional-email


