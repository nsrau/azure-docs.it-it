<properties 
	pageTitle="Sviluppo e distribuzione di un'app Web con Microsoft WebMatrix." 
	description="Informazioni su come sviluppare e distribuire un'applicazione web ASP.NET per applicazioni di Azure applicazione servizio Web con Microsoft WebMatrix." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/21/2015" 
	ms.author="tomfitz"/>


# Sviluppo e distribuzione di un'app Web con Microsoft WebMatrix.

## Panoramica

In questa guida viene descritto come usare Microsoft WebMatrix per creare e distribuire un sito Web .NET in App Web in [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714). Verrà utilizzata un'applicazione di esempio da un modello di sito WebMatrix.

Si apprenderà come:

* Accedere ad Azure da WebMatrix
* Creare un sito usando un modello predefinito con WebMatrix 
* Distribuire il sito Web personalizzato direttamente da WebMatrix in Azure

[AZURE.INCLUDE [create-account-and-websites-note](../includes/create-account-and-websites-note.md)]

>[AZURE.NOTE]Per iniziare a usare Servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Accesso ad Azure

1. Avviare WebMatrix.
2. Se è la prima volta che si utilizza WebMatrix 3, verrà richiesto di effettuare l'accesso ad Azure. In caso contrario, è possibile fare clic sul pulsante **Sign In** e scegliere **Add Account**. Scegliere **Sign in** utilizzando il proprio account Microsoft.

	![Aggiunta di un account][addaccount]

3. Se è stata effettuata l'iscrizione per un account Azure, è possibile accedere con il proprio account Microsoft:

	![Accesso ad Azure][signin]


## Creazione di un sito utilizzando un modello predefinito per Azure

1. Nella schermata Start fare clic sul pulsante **New** e scegliere **Template Gallery** per creare un nuovo sito dalla raccolta modelli:

	![Nuovo sito dalla raccolta modelli][sitefromtemplate]

2. La raccolta modelli contiene un elenco dei modelli disponibili che è possibile eseguire in locale in Azure. Selezionare **Bakery** nell'elenco di modelli, immettere **bakerysample** per **Site Name** e fare clic su **Avanti**.

	![Creazione del sito da modello][sitefromtemplatedetails]

3. Se è stato effettuato l'accesso ad Azure, a questo punto è possibile creare un sito Web di Azure per il sito locale. Scegliere un nome univoco e selezionare il data center in cui si desidera venga creata l'istanza di servizio app per app Web:

	![Creazione del sito in Azure][sitefromtemplateazure]

	Dopo la creazione del sito locale e l'istanza di applicazioni Web in Azure, viene visualizzato l'IDE di WebMatrix:

	![IDE WebMatrix][howtowebmatrixide]

## Configurare la posta elettronica

L'esempio bakery include un modulo d'ordine simulato che invia un messaggio di posta elettronica con l'elemento ordinato. Si userà il servizio di posta elettronica SendGrid in Azure per inviare messaggi di posta elettronica dal sito.

1. Attenersi alla procedura riportata nell'esercitazione [Come inviare messaggi di posta elettronica usando SendGrid con Azure][sendgridexample] per configurare un account SendGrid e recuperare le informazioni di connessione. Non è necessario eseguire l'intera esercitazione. Basterà arrivare al punto relativo alle informazioni di connessione.

2. Aggiungere il pacchetto NuGet di SendGrid al progetto WebMatrix. Prima fare clic sul pulsante NuGet.

    ![Aggiungere SendGrid][addsendgrid]

    Cercare SendGrid e installarlo.

    ![Installare SendGrid][installsendgrid]

    Al termine dell'installazione del pacchetto, si noti che sono stati aggiunti gli assembly di SendGrid a bin.

    ![Aggiunta di SendGrid][binsendgrid]

3. Aprire la pagina *Order.cshtml* facendo doppio clic sul nome file.

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

1. In WebMatrix fare clic su **Publish** sulla barra multifunzione **Home** per visualizzare la finestra di dialogo **Publish Preview** relativa al sito Web.

	![Anteprima della pubblicazione in WebMatrix][howtopublishpreview]

2. Fare clic per selezionare la casella di controllo accanto a bakery.sdf e quindi fare clic su **Continue**. Quando la pubblicazione viene completata, nella parte inferiore dell'IDE WebMatrix viene visualizzato l'URL del sito Web aggiornato in Azure.

	![Pubblicazione completata][publishcomplete]

3. Fare clic sul collegamento per aprire il sito Web (un'istanza di applicazioni Web in Azure) nel browser:

	![Sito di esempio Bakery][bakerysample]

	L'URL per l'istanza di applicazioni Web sono disponibili anche nel [portale Azure](https://portal.azure.com) facendo clic su **Sfoglia** > **applicazioni Web** per visualizzare tutte le istanze di applicazioni Web per la sottoscrizione, quindi selezionare un'applicazione web. Viene visualizzato l'URL per l'applicazione web blade dell'applicazione web.

## Modificare il sito Web e ripubblicarlo per applicazioni Web

È possibile utilizzare WebMatrix per modificare il sito e ripubblicarlo nel sito Web di Azure. Nella procedura seguente verrà aggiunta una casella di controllo per indicare che l'ordine è un regalo.

1. Aprire la pagina *Order.cshtml*.

2. Individuare la definizione del modulo della classe "shiping". Inserire il codice seguente dopo il blocco <li>.
		
		<li class="gift">
		    <div class="fieldcontainer" data-role="fieldcontain">
		        <label for="isGift">This is a gift</label>           
		        <div>@Html.CheckBox("isGift")</div>
		    </div>
		</li>

	![][modify5]

3. Individuare la riga "var shipping = Request["orderShipping"];" nel file e inserire subito dopo la riga di codice seguente.

		var gift = Request["isGift"];

4. Dopo il blocco di codice che convalida che il campo dell'indirizzo di spedizione non è vuoto, inserire il frammento di codice seguente.

		if(gift != null) {
			body += "This is a gift." + "<br/>";
		}

	L'aspetto del file *order.cshtml* dovrebbe essere simile al seguente.

	![][modify6]

5. Salvare il file, eseguire il sito in locale e inviare a se stessi un ordine di test. Assicurarsi di testare la nuova casella di controllo.

	![][modify7]

6. Ripubblicare il sito facendo clic su **Publish** sulla barra multifunzione **Home**.

7. Nella finestra di dialogo **Publish Preview** assicurarsi che il file Order.cshtml sia selezionato e fare clic su Continue.

8. Fare clic sul collegamento per aprire il sito Web nel browser e testare l'aggiornamento nel sito Web di Azure.

## Passaggi successivi

* [Sito Web di WebMatrix](http://www.microsoft.com/click/services/Redirect2.ashx?CR_CC=200106398)

## Modifiche apportate
* Per una Guida per la modifica di siti Web al servizio App vedere: [servizio App Azure e il relativo impatto sui servizi di Azure esistente](http://go.microsoft.com/fwlink/?LinkId=529714)
* Per una Guida per la modifica del portale precedente per il nuovo portale, vedere: [riferimento per lo spostamento tra il portale di anteprima](http://go.microsoft.com/fwlink/?LinkId=529715)




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

<!--HONumber=54-->