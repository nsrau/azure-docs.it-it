<properties 
	pageTitle="Connettere un'app Web a un'app per le API nel servizio app di Azure" 
	description="Questa esercitazione illustra come usare un'app per le API da un'app Web ASP.NET ospitata nel servizio app di Azure." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="syntaxc4" 
	manager="yochayk" 
	editor="jimbe"/>

<tags
	ms.service="app-service-web"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="na" 
	ms.date="09/15/2015"
	ms.author="cfowler"/>

# Connettere un'app Web a un'app per le API nel servizio app di Azure

Questa esercitazione illustra come usare un'app per le API da un'app Web ASP.NET ospitata nel [servizio app](../app-service.md).

## Prerequisiti

Questa esercitazione si basa sulla serie di esercitazioni relative alle app per le API:

1. [Creare un'app per le API di Azure](../app-service-dotnet-create-api-app)
3. [Distribuire un'app per le API di Azure](../app-service-dotnet-deploy-api-app)
4. [Eseguire il debug di un'app per le API di Azure](../app-service-dotnet-remotely-debug-api-app)

## Rendere l'app per le API pubblicamente accessibile

Nel [portale di Azure](http://go.microsoft.com/fwlink/?LinkId=529715) selezionare l'app per le API. Fare clic sul pulsante **Impostazioni** nella barra dei comandi. Nel pannello **Impostazioni applicazione** modificare **Livello di accesso** su **Pubblico (anonimo)**.

![](./media/app-service-web-connect-web-app-to-saas-api/4-5-Change-Access-Level-To-Public.png)

## Creare un'applicazione ASP.NET MVC in Visual Studio

1. Aprire Visual Studio. Usare la finestra di dialogo **Nuovo progetto** per aggiungere una nuova **applicazione Web ASP.NET**. Fare clic su **OK**.

	![File > Nuovo > Web > Applicazione Web ASP.NET](./media/app-service-web-connect-web-app-to-saas-api/1-Create-New-MVC-App-For-Consumption.png)

1. Selezionare il modello **MVC**. Fare clic su **Modifica autenticazione**, selezionare **Nessuna autenticazione** e quindi fare clic su **OK** per due volte.

	![Nuova applicazione ASP.NET](./media/app-service-web-connect-web-app-to-saas-api/2-Change-Auth-To-No-Auth.png)

1. In Esplora soluzioni fare clic con il pulsante destro del mouse sul progetto di applicazione Web appena creato, quindi selezionare **Aggiungi riferimento ad app di Azure**.

	![Aggiungere un riferimento a un'app per le API di Azure...](./media/app-service-web-connect-web-app-to-saas-api/3-Add-Azure-API-App-SDK.png)

1. Nell'elenco a discesa **app per le API esistenti** selezionare l'app per le API a cui ci si vuole connettere.

	![Selezionare un'app per le API esistente](./media/app-service-web-connect-web-app-to-saas-api/4-Add-Azure-API-App-SDK-Dialog.png)

	>[AZURE.NOTE]Il codice client per la connessione all'app per le API verrà generato automaticamente da un endpoint API Swagger.

1. Per usare il codice API generato, aprire il file HomeController.cs e sostituire l'azione `Contact` con il codice seguente:

	    public async Task<ActionResult> Contact()
	    {
	        ViewBag.Message = "Your contact page.";
	
	        var contacts = new ContactsList();
	        var response = await contacts.Contacts.GetAsync();
	        var contactList = response.Body;
	
	        return View(contactList);
	    }

	![Aggiornamenti codice HomeController.cs](./media/app-service-web-connect-web-app-to-saas-api/5-Write-Code-Which-Leverages-Swagger-Generated-Code.png)

1. Aggiornare la visualizzazione `Contact` in modo che rifletta l'elenco dinamico di contatti con il codice seguente:
	<pre>// Add to the very top of the view file
	@model IList&lt;MyContactsList.Web.Models.Contact&gt;
	
	// Replace the default email addresses with the following
    &lt;h3&gt;Public Contacts&lt;/h3&gt;
    &lt;ul&gt;
        @foreach (var contact in Model)
        {
            &lt;li&gt;&lt;a href=&quot;mailto:@contact.EmailAddress&quot;&gt;@contact.Name &amp;lt;@contact.EmailAddress&amp;gt;&lt;/a&gt;&lt;/li&gt;
        }
    &lt;/ul&gt; 
	</pre>

	![Contact.cshtml Code Updates](./media/app-service-web-connect-web-app-to-saas-api/6-Update-View-To-Reflect-Changes.png)

## Distribuire l'applicazione Web nelle app Web del servizio app

Seguire le istruzioni fornite in [Come distribuire un'app Web di Azure](web-sites-deploy.md).

>[AZURE.NOTE]Per iniziare a usare il servizio app di Azure prima di registrarsi per ottenere un account Azure, andare a [Prova il servizio app](http://go.microsoft.com/fwlink/?LinkId=523751), dove è possibile creare un'app Web iniziale temporanea nel servizio app. Non è necessario fornire una carta di credito né impegnarsi in alcun modo.

## Modifiche apportate
* Per una guida relativa al passaggio da Siti Web al servizio app, vedere [Servizio app di Azure e impatto sui servizi di Azure esistenti](http://go.microsoft.com/fwlink/?LinkId=529714)
 

<!---HONumber=AcomDC_1203_2015-->