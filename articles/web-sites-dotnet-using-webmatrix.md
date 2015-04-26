<properties 
	pageTitle="Sito Web .NET con WebMatrix - Esercitazioni di Azure" 
	description="Informazioni su come sviluppare e distribuire un sito Web di Azure con WebMatrix." 
	services="web-sites" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/27/2014" 
	ms.author="tomfitz"/>





#Sviluppare e distribuire un sito Web con Microsoft WebMatrix
In questa guida viene descritto come usare Microsoft WebMatrix per creare e distribuire un sito Web in Azure.  Verrà usata un'applicazione di esempio da un modello di sito WebMatrix.

Si apprenderà come:

* Accedere ad Azure da WebMatrix
* Creare un sito usando un modello predefinito con WebMatrix 
* Distribuire il sito Web personalizzato direttamente da WebMatrix in Azure

> [AZURE.NOTE]
> Per completare l'esercitazione, è necessario un account Azure. È possibile <a href="http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/">attivare i benefici della sottoscrizione MSDN</a> o <a href="http://azure.microsoft.com/pricing/free-trial/">iscriversi per una versione di valutazione gratuita</a>.
> Per iniziare a usare Siti Web di Azure prima di iscriversi per ottenere un account, visitare la pagina all'indirizzo <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>, in cui è possibile creare immediatamente e gratuitamente un sito di base ASP.NET temporaneo in Siti Web di Azure. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Accesso ad Azure

1. Avviare WebMatrix.
2. Se è la prima volta che si usa WebMatrix 3, verrà richiesto di effettuare l'accesso ad Azure.  In caso contrario, è possibile fare clic sul pulsante **Accedi** e scegliere **Aggiungi account**.  Scegliere **Accedi** usando il proprio account Microsoft.

	![Add Account][addaccount]

3. Se è stata effettuata l'iscrizione per un account Azure, è possibile accedere con il proprio account Microsoft:

	![Sign into Azure][signin]	


## Creazione di un sito usando un modello predefinito per Azure

1. Nella schermata Start fare clic sul pulsante **Nuovo** e scegliere **Raccolta modelli** per creare un nuovo sito dalla raccolta modelli:

	![New site from Template Gallery][sitefromtemplate]

2. La raccolta modelli contiene un elenco dei modelli disponibili che è possibile eseguire in locale in Azure.  Selezionare **Bakery** nell'elenco di modelli, immettere **bakerysample** per **Site Name** e fare clic su **Next**.

	![Create Site from Template][sitefromtemplatedetails]

3. Se è stato effettuato l'accesso ad Azure, a questo punto è possibile creare un sito Web di Azure per il sito locale.  Scegliere un nome univoco e selezionare il data center in cui si desidera venga creato il sito: 

	![Create site on Azure][sitefromtemplateazure]

	Dopo la creazione del sito Web con WebMatrix, verrà visualizzato l'ambiente IDE WebMatrix:

	![WebMatrix IDE][howtowebmatrixide] 

## Configurare la posta elettronica

L'esempio bakery include un modulo d'ordine simulato che invia un messaggio di posta elettronica con l'elemento ordinato. Si userà il servizio di posta elettronica SendGrid in Azure per inviare messaggi di posta elettronica dal sito.

1. Attenersi alla procedura riportata nell'esercitazione [Come inviare messaggi di posta elettronica usando SendGrid con Azure][sendgridexample] per configurare un account SendGrid e recuperare le informazioni di connessione. Non è necessario eseguire l'intera esercitazione. Basterà arrivare al punto relativo alle informazioni di connessione.

2. Aggiungere il pacchetto NuGet di SendGrid al progetto WebMatrix. Prima fare clic sul pulsante NuGet.

    ![Add SendGrid][addsendgrid]

    Cercare SendGrid e installarlo.

    ![Install SendGrid][installsendgrid]

    Al termine dell'installazione del pacchetto, si noti che sono stati aggiunti gli assembly di SendGrid a bin.

    ![SendGrid added][binsendgrid]

3. Aprire la pagina  *Order.cshtml* facendo doppio clic sul nome file.

	![][modify2]

4. All'inizio del file aggiungere il codice seguente:

        @using SendGrid;
        @using System.Net.Mail;	

4. Trovare il commento //SMTP Configuration for Hotmail ed eliminare o impostare come commento tutto il codice per l'uso della posta sul Web.

        /*
        //SMTP Configuration for Hotmail
        WebMail.SmtpServer = "smtp.live.com";
        WebMail.SmtpPort = 25;
        WebMail.EnableSsl = true;

        //Enter your Hotmail credentials for UserName/Password and a "From" address for the e-mail
        WebMail.UserName = "";
        WebMail.Password = "";
        WebMail.From = "";

        if (WebMail.UserName.IsEmpty() || WebMail.Password.IsEmpty() || WebMail.From.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        } 
        else {
            try {
                WebMail.Send(to: customerEmail, subject: "Fourth Coffee - New Order", body: body);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }*/


5. Aggiungere il codice per usare SendGrid invece della posta sul Web per l'invio di messaggi di posta elettronica. Aggiungere il seguente codice al posto del codice eliminato nel passaggio precedente.

		 if (email.IsEmpty()) {
            Response.Redirect("~/OrderSuccess?NoEmail=1");
        }
        else {
            // Create the email object first, then add the properties.
            SendGridMessage myMessage = new SendGridMessage();
            myMessage.AddTo(email);
            myMessage.From = new MailAddress("FourthCoffee@example.com", "Fourth Coffee");
            myMessage.Subject = "Fourth Coffee - New Order";
            myMessage.Text = body;

            // Create credentials, specifying your user name and password.
            var credentials = new NetworkCredential("[your user name", "[your password]");

            // Create an Web transport for sending email.
            var transportWeb = new Web(credentials);

            // Send the email.
            try {
                transportWeb.Deliver(myMessage);
                Response.Redirect("~/OrderSuccess");
            } catch {
                ModelState.AddFormError("There was an error and your order could not be processed at this time");
            }
        }


6. Sulla barra multifunzione di WebMatrix fare clic su **Run** per testare il sito.

	![][modify4]

7. Fare clic su **Order Now** per uno dei prodotti e inviare un ordine a se stessi.

8. Controllare la posta elettronica e assicurarsi di ricevere la conferma dell'ordine. Se si riscontrano difficoltà nell'invio di posta elettronica, vedere la sezione relativa ai [problemi con l'invio di posta elettronica][sendmailissues] nella guida alla risoluzione dei problemi delle pagine Web ASP.NET (Razor).
 

## Distribuire il sito Web personalizzato da WebMatrix in Azure

1. In WebMatrix fare clic su **Publish** sulla barra multifunzione **Home** per visualizzare la finestra di dialogo **Publish Preview** per il sito Web.

	![WebMatrix Publish Preview][howtopublishpreview]

2. Fare clic per selezionare la casella di controllo accanto a bakery.sdf e quindi fare clic su **Continue**.  Al termine della pubblicazione, nella parte inferiore dell'IDE WebMatrix viene visualizzato l'URL del sito Web aggiornato in Azure.  

	![Publishing Complete][publishcomplete]

3. Fare clic sul collegamento per aprire il sito Web nel browser:

	![Bakery Sample Site][bakerysample]

	Per trovare l'URL del sito Web, è anche possibile fare clic su **Siti Web** nel portale Azure per visualizzare tutti i siti Web disponibili per la sottoscrizione. L'URL per ciascun sito Web è visualizzato nella colonna URL della pagina Siti Web.

## Modificare il sito Web e ripubblicarlo nel sito Web di Azure

È possibile usare WebMatrix per modificare il sito e ripubblicarlo nel sito Web di Azure. Nella procedura seguente verrà aggiunta una casella di controllo per indicare che l'ordine è un regalo.

1. Aprire la pagina  *Order.cshtml*.

2. Individuare la definizione del modulo della classe "shiping". Inserire il seguente codice dopo il blocco &lt;li&gt;.
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. Individuare la riga "var shipping = Request["orderShipping"];"  nel file e inserire subito dopo la seguente riga di codice.

		var gift = Request["isGift"];

4. Dopo il blocco di codice che convalida che il campo dell'indirizzo di spedizione non è vuoto, inserire il frammento di codice seguente.

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	L'aspetto del file  *order.cshtml* dovrebbe essere simile al seguente.

	![][modify6]

5. Salvare il file, eseguire il sito in locale e inviare a se stessi un ordine di test. Assicurarsi di testare la nuova casella di controllo.

	![][modify7]

6. Ripubblicare il sito facendo clic su **Publish** sulla barra multifunzione **Home**.

7. Nella finestra di dialogo **Publish Preview** assicurarsi che il file Order.cshtml sia selezionato e fare clic su Continue.

8. Fare clic sul collegamento per aprire il sito Web nel browser e testare l'aggiornamento nel sito Web di Azure.

# Passaggi successivi

Si è appreso come creare e distribuire un sito Web da WebMatrix in Azure. Per altre informazioni su WebMatrix, fare riferimento alle risorse seguenti:

* [WebMatrix per Azure](http://go.microsoft.com/fwlink/?LinkID=253622&clcid=0x409)

* [Sito Web di WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)





[howtowebmatrixide]: ./media/web-sites-dotnet-using-webmatrix/howtowebmatrixide2.png
[howtopublishpreview]: ./media/web-sites-dotnet-using-webmatrix/howtopublishpreview.png
[addsendgrid]: ./media/web-sites-dotnet-using-webmatrix/addsendgridpackage.png
[installsendgrid]: ./media/web-sites-dotnet-using-webmatrix/installsendgrid.png
[binsendgrid]: ./media/web-sites-dotnet-using-webmatrix/sendgridbin.png

[publishcomplete]: ./media/web-sites-dotnet-using-webmatrix/howtopublished2.png
[bakerysample]: ./media/web-sites-dotnet-using-webmatrix/howtobakerysamplesite.png
[addaccount]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-add-account.png
[signin]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-sign-in.png
[sitefromtemplate]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template.png
[sitefromtemplatedetails]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-details.png
[sitefromtemplateazure]: ./media/web-sites-dotnet-using-webmatrix/webmatrix-site-from-template-azure.png

[modify1]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-1.png
[modify2]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-2.png
[modify3]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-3.png
[modify4]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-4.png
[modify5]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-5.png
[modify6]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-6.png
[modify7]: ./media/web-sites-dotnet-using-webmatrix/website-with-webmatrix-sample-mod-1-7.png



[sendmailissues]: http://go.microsoft.com/fwlink/?LinkId=253001#email
[sendgridexample]: http://azure.microsoft.com/documentation/articles/sendgrid-dotnet-how-to-send-email/



<!--HONumber=42-->
