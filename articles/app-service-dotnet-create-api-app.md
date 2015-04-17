<properties 
	pageTitle="Creare un'app per le API del servizio app di Azure" 
	description="Questo articolo descrive come usare Visual Studio 2013 per creare un'app per le API nel servizio app di Azure" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Creare un'app per le API del servizio app di Azure

## Informazioni generali

Questa è la prima di una serie di tre esercitazioni che consentirà di iniziare a usare subito le app per le API nel servizio app di Azure.

1. In questa esercitazione verrà creata una nuova app per le API, che verrà quindi preparata per essere distribuita nella sottoscrizione di Azure. 
* Nell'esercitazione sulla [distribuzione di un'app per le API](app-service-dotnet-create-api-app.md) l'app per le API viene distribuita nella sottoscrizione di Azure.
* Nell'esercitazione sul [debug di un'app per le API](app-service-dotnet-remotely-debug-api-app.md) viene usato Visual Studio per eseguire in modalità remota il debug del codice mentre questo è in esecuzione in Azure.

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Creare la prima app per le API ##

Aprire Visual Studio 2013 e selezionare **File > Nuovo progetto**. Selezionare il modello **Applicazione Web ASP.NET**.  Assegnare al progetto il nome *ContactsList*, quindi fare clic su **OK**.

![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

Selezionare il modello di progetto **Azure API App** e quindi fare clic su **OK**.

![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

Fare clic con il pulsante destro del mouse sulla cartella **Models** nel progetto di API Web e quindi selezionare **Aggiungi > Classe** nel menu di scelta rapida. 

![](./media/app-service-dotnet-create-api-app/03-add-new-class-v3.png) 

Assegnare al nuovo file il nome *Contact.cs* e quindi fare clic su **Aggiungi**. 

![](./media/app-service-dotnet-create-api-app/0301-add-new-class-dialog-v3.png) 

Sostituire l'intero contenuto del file con il codice seguente. 

	namespace ContactsList.Models
	{
		public class Contact
		{
			public int Id { get; set; }
			public string Name { get; set; }
			public string EmailAddress { get; set; }
		}
	}

Fare clic con il pulsante destro del mouse sulla cartella **Controllers** e quindi selezionare **Aggiungi > Controller** dal menu contestuale. 

![](./media/app-service-dotnet-create-api-app/05-new-controller-v3.png)

Nella finestra di dialogo **Aggiungi scaffolding** selezionare l'opzione **Controller API Web 2 - Vuoto** e fare clic su **Aggiungi**. 

![](./media/app-service-dotnet-create-api-app/06-new-controller-dialog-v3.png)

Assegnare al controller il nome **ContactsController** e quindi fare clic su **Aggiungi**. 

![](./media/app-service-dotnet-create-api-app/07-new-controller-name-v2.png)

Sostituire il codice in questo file con il codice seguente. 

	using ContactsList.Models;
	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Net;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Web.Http;
	
	namespace ContactsList.Controllers
	{
	    public class ContactsController : ApiController
	    {
	        [HttpGet]
	        public IEnumerable<Contact> Get()
	        {
	            return new Contact[]{
					new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
					new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
                	new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
	            };
	        }
	    }
	}

I progetti di app per le API sono abilitati con la generazione di metadati [Swagger](http://swagger.io/ "Official Swagger information") automatica e con una pagina di test dell'API. Per impostazione predefinita, la pagina di test dell'API è disattivata. Per abilitare questa pagina, aprire il file *App_Start/SwaggerConfig.cs*. Cercare **EnableSwaggerUI**:

![](./media/app-service-dotnet-create-api-app/12-enable-swagger-ui-with-box.png)

Rimuovere quindi i simboli di commento dalle righe di codice seguenti:

        })
    .EnableSwaggerUi(c =>
        {

Una volta completato, il file dovrebbe avere il seguente aspetto in Visual Studio 2013.

![](./media/app-service-dotnet-create-api-app/13-enable-swagger-ui-with-box.png)

Per visualizzare la pagina di test dell'API, eseguire l'applicazione localmente e passare a `/swagger`. 

![](./media/app-service-dotnet-create-api-app/14-swagger-ui.png)

Fare clic sul pulsante **Prova**; si noterà che l'API funziona e restituisce il risultato previsto. 

![](./media/app-service-dotnet-create-api-app/15-swagger-ui-post-test.png)

## Passaggi successivi

L'app per le API è ora pronta per essere distribuita e a questo scopo è possibile seguire l'esercitazione relativa alla [distribuzione di un'app per le API](app-service-dotnet-deploy-api-app.md).

Per altre informazioni, vedere [Cosa sono le app per le API?](app-service-api-apps-why-best-platform.md)

<!--HONumber=49-->