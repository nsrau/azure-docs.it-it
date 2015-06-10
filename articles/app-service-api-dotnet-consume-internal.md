<properties 
	pageTitle="Usare un'app per le API interna nel servizio app di Azure da un client .NET" 
	description="Informazioni su come usare un'app per le API da un'app per le API .NET nello stesso gruppo di risorse con App Service SDK." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# Usare un'app per le API interna nel servizio app di Azure da un client .NET 

## Panoramica

Questa esercitazione illustra come scrivere codice per un'[app per le API](app-service-api-apps-why-best-platform.md) ASP.NET che chiama un'altra app per le API configurata per il livello di accesso **interno**. Entrambe le app per le API devono trovarsi nello stesso gruppo di risorse. È possibile usare lo stesso codice per chiamare un'app per le API interna da un'[app per dispositivi mobili](app-service-mobile-value-prop-preview.md).

Verrà compilata un'API Web ContactNames. Il metodo Get dell'API Web chiamerà un'app per le API ContactsList e restituirà solo i nomi senza le informazioni di contatto forniti dall'app per le API ContactsList. Di seguito è riportata la schermata dell'interfaccia utente di Swagger per una chiamata riuscita al metodo Get di ContactNames.

![](./media/app-service-api-dotnet-consume-internal/tryitout.png)

Per informazioni su come chiamare le app per le API configurate per i livelli di accesso **Pubblico (anonimo)** o **Pubblico (autenticato)**, vedere [Usare un'app per le API nel servizio app di Azure da un client .NET](app-service-api-dotnet-consume.md).

## Prerequisiti

Nell'esercitazione si presuppone che l'utente abbia familiarità con le procedure per creare progetti e aggiungervi codice in Visual Studio e per [gestire le app per le API nel portale di anteprima di Azure](app-service-api-apps-manage-in-portal.md).

Il progetto e gli esempi di codice mostrati in questo articolo sono basati sul progetto di app per le API. Per informazioni su come creare e distribuire questo progetto, vedere gli articoli seguenti:

- [Creare un'app per le API](app-service-dotnet-create-api-app.md)
- [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md)

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Per eseguire questa esercitazione, è necessario installare Azure SDK per .NET versione 2.6 o successive.

### Configurare l'app per le API di destinazione

1. Se necessario, seguire l'esercitazione fornita in [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md) per distribuire il progetto di esempio ContactsList in un'app per le API nella sottoscrizione di Azure.

2. Nel [portale di anteprima di Azure](https://portal.azure.com/), nel pannello **App per le API** dell'app per le API ContactsList distribuita in precedenza, fare clic su **Impostazioni > Impostazioni applicazione** e impostare **Livello di accesso** su **Interno**, quindi fare clic su **Salva**.

	![](./media/app-service-api-dotnet-consume-internal/setinternal.png)
 
## Creare una nuova app per le API che chiamerà l'app per le API esistente

- In Visual Studio creare un progetto app per le API denominato ContactNames usando il modello di progetto di app per le API di Azure.

	Si tratta dello stesso processo seguito in [Creare un'app per le API](app-service-dotnet-create-api-app.md), ma verrà aggiunto un codice diverso al progetto più avanti in questa esercitazione.
 
## Aggiungere il codice client generato da App Service SDK

I passaggi seguenti sono illustrati in modo più dettagliato in [Usare un'app per le API nel servizio app di Azure da un client .NET](app-service-api-dotnet-consume.md).

3. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto (non sulla soluzione) e quindi scegliere **Aggiungi > Client app per le API di Azure**. 

3. Nella finestra di dialogo **Aggiungi client app per le API di Azure** fare clic su **Scarica da app per le API di Azure**.

5. Nell'elenco a discesa selezionare l'app per le API da chiamare. Per questa esercitazione scegliere l'app per le API ContactsList creata in precedenza.

7. Fare clic su **OK**.

## Abilitare l'interfaccia utente di Swagger

Per impostazione predefinita, i progetti di app per le API sono abilitati con la generazione automatica dei metadati [Swagger](http://swagger.io/ "Informazioni ufficiali su Swagger"), ma il modello nuovo-progetto di app per le API di Azure disabilita la pagina di test dell'API. In questa sezione viene abilitata la pagina di test.

1. Aprire il file *App_Start/SwaggerConfig.cs* e cercare **EnableSwaggerUI**:

2. Rimuovere quindi i simboli di commento dalle righe di codice seguenti:

	        })
	    .EnableSwaggerUi(c =>
	        {

## Creare un controller

5. Fare clic con il pulsante destro del mouse sulla cartella **Controller** e quindi scegliere **Aggiungi > Controller**. 

6. Nella finestra di dialogo **Aggiungi scaffolding** selezionare l'opzione **Controller API Web 2 - Vuoto** e fare clic su **Aggiungi**.

7. Assegnare al controller il nome **ContactNamesController** e quindi fare clic su **Aggiungi**.

## Aggiungere il codice per chiamare l'app per le API

Per chiamare un'app per le API che è stata protetta impostando il livello di accesso su **Interno**, è necessario aggiungere le intestazioni di autenticazione interna alle richieste HTTP. Queste intestazioni informano l'app per le API di destinazione che l'origine della chiamata è un'app per le API peer che sta eseguendo la chiamata dall'interno dello stesso gruppo di risorse.

App Service SDK genera le classi client che semplificano il codice scritto per chiamare l'app per le API. Per chiamare un'app per le API con livello di accesso **Pubblico (anonimo)**, è sufficiente creare un oggetto client e chiamare i metodi su di esso, come illustrato nell'esempio seguente:

		var client = new ContactsList();
		var contacts = await client.Contacts.GetAsync();

Tuttavia, per aggiungere le intestazioni di autenticazione è necessario accedere all'oggetto `HttpRequestMessage` che però al momento non è presente. Per ottenere l'accesso alla richiesta e aggiungere le intestazioni, è necessario creare una classe `DelegatingHandler` e passare un'istanza della classe al costruttore del client generato.

1. Aggiungere al progetto un file di classe denominato *InternalCredentialHandler.cs* e sostituire il codice del modello con il codice seguente.

		using Microsoft.Azure.AppService.ApiApps.Service;
		using System.Net.Http;
		using System.Threading;
		using System.Threading.Tasks;
		
		namespace ContactNames
		{
		    public class InternalCredentialHandler : DelegatingHandler
		    {
		        protected override Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		        {
		            Runtime.FromAppSettings(request).SignHttpRequest(request);
		            return base.SendAsync(request, cancellationToken);
		        }
		    }
		}

	Questo codice chiama `SignHttpRequest` per aggiungere le intestazioni di autenticazione a ogni richiesta inviata dalla classe client generata:

		Runtime.FromAppSettings(this.Request).SignHttpRequest

1. In *ContactNamesController.cs* sostituire il codice del modello con il codice seguente.

		using ContactNames.Models;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactNames.Controllers
		{
		    public class ContactNamesController : ApiController
		    {
		        [HttpGet]
		        public async Task<IEnumerable<string> Get()
		        {
		            var names = new List<string>();

		            var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });
		            var contacts = await client.Contacts.GetAsync();
		
		            foreach (Contact contact in contacts)
		            {
		                names.Add(contact.Name);
		            }		
		            return names;
		        }
		    }
		}

	Questo codice passa il gestore al costruttore della classe client generata:

		var client = new ContactsList(new DelegatingHandler[] { new InternalCredentialHandler() });

### Distribuire

Non è possibile eseguire il test in locale. È necessario distribuire il codice ed eseguirlo in un'app per le API di Azure. In caso contrario, non sarà possibile aggiungere le intestazioni di autenticazione corrette e le chiamate verranno rifiutate.

I passaggi di distribuzione seguenti vengono illustrati in modo più dettagliato in [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md).

1. Creare un'app per le API ContactNames.

	* In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto (non sulla soluzione), quindi scegliere **Pubblica**. 

	* Scegliere la scheda **Profilo** e quindi fare clic su **App per le API di Microsoft Azure**.

	* Fare clic su **Nuovo** per effettuare il provisioning di una nuova app per le API nella sottoscrizione di Azure.

	* Nella finestra di dialogo **Crea un'app per le API** immettere ContactNames in **Nome app per le API**.

	* Per gli altri valori presenti nella finestra di dialogo **Crea un'app per le API** immettere gli stessi valori immessi in precedenza per l'esercitazione illustrata in [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md).

		Assicurarsi soprattutto di creare la nuova app per le API nello stesso gruppo di risorse dell'app per le API che si intende chiamare.

	* Fare clic su **OK**.

2. Distribuire il codice nella nuova app per le API.

	* Dopo avere effettuato il provisioning dell'app per le API, fare clic con il pulsante destro del mouse sul progetto in **Esplora soluzioni** e scegliere **Pubblica** per aprire nuovamente la finestra di dialogo di pubblicazione.

	* Nella finestra di dialogo **Pubblica sul Web** fare clic su **Pubblica** per avviare il processo di distribuzione.

### Test

In questa sezione è possibile usare l'interfaccia utente di Swagger per testare la nuova app per le API e verificare che possa chiamare l'app per le API creata in precedenza.

1. Aprire in un browser l'URL della nuova app per le API.

	Con le impostazioni di pubblicazione predefinite, Visual Studio apre automaticamente l'URL dell'app per le API in un browser al termine del processo di pubblicazione. Se ciò non avviene o se è stata chiusa la finestra del browser, seguire questi passaggi per accedere allo stesso URL:

	* Nel portale di anteprima di Azure passare al pannello App per le API relativo alla nuova app per le API ContactsName.

	* Fare clic su **URL**.

		![](./media/app-service-api-dotnet-consume-internal/clickurl.png)
  
5. Nella barra degli indirizzi del browser aggiungere `/swagger` alla fine dell'URL e premere INVIO.

	Ad esempio, l'URL risultante sarà simile al seguente:

		https://microsoft-apiapp214f26e673e5449a214f26e673e5449a.azurewebsites.net/swagger

1. Nella pagina dell'interfaccia utente di Swagger fare clic su **ContactNames > Get > Try it out!**

	![](./media/app-service-api-dotnet-consume-internal/tryitout.png)
  
	La pagina visualizza i nomi dei contatti nella sezione Response Body, che conferma che l'app per le API ContactNames ha recuperato correttamente i dati dall'app per le API ContactsList.

	Se si vuole verificare che l'impostazione **Interna** protegge l'app per le API ContactsList, impostare come commento la chiamata a `SignHttpRequest` in *ContactNamesController.cs*, ridistribuire, eseguire di nuovo Swagger **Try it now** e a questo punto verrà visualizzato un messaggio di errore.


## Aggiungere il codice per chiamare l'app per le API con HttpClient
 
App Service SDK dipende dalle definizioni delle API di Swagger per generare le classi client. Se si vuole chiamare un'app per le API che non ha implementato le definizioni delle API di Swagger, è possibile farlo usando `HttpClient`. In questo caso, è possibile comunque usare il metodo `SignHttpRequest`, chiamandolo però direttamente nell'oggetto `HttpRequestMessage`.

1. In *ContactNamesController.cs* aggiungere il codice seguente prima dell'istruzione `return names;` nel metodo `Get`.

		var httpClient = new HttpClient();
		HttpRequestMessage httpRequest = new HttpRequestMessage();
		httpRequest.Method = HttpMethod.Get;
		httpRequest.RequestUri = new Uri("https://{yourapiappurl}/api/contacts");
		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);
		var response = await httpClient.SendAsync(httpRequest); 
		var contacts2 = await response.Content.ReadAsAsync<List<Contact>();
		foreach (Contact contact in contacts2)
		{
		    names.Add(contact.Name);
		}

	Questo codice passa l'oggetto richiesta in ingresso al metodo `SignHttpRequest` per accedere all'oggetto della richiesta in uscita:

		Runtime.FromAppSettings(this.Request).SignHttpRequest(httpRequest);

2. Nel portale di anteprima di Azure passare al pannello App per le API relativo all'app per le API ContactsList e copiare l'URL.

	![](./media/app-service-api-dotnet-consume-internal/clurl.png)
 
4. Sostituire la stringa segnaposto "{yourapiappurl}" nel codice del controller con l'URL effettivo. Al termine, questa riga di codice sarà simile a quella illustrata nell'esempio seguente.

		httpRequest.RequestUri = new Uri("https://microsoft-apiappd99e684d99e684d99e684d99e684.azurewebsites.net/api/contacts");

### Distribuire e testare

1. Seguire la stessa procedura eseguita in precedenza per distribuire il codice dell'app per le API.

	**Suggerimento:** è possibile ignorare la finestra di dialogo **Pubblica sul Web** e ridistribuire facendo clic su un singolo pulsante nella barra degli strumenti. In Visual Studio fare clic su **Visualizza > Barre degli strumenti** e abilitare la barra degli strumenti **Sito Web - Pubblicazione con un clic**.
 
2. Seguire la procedura eseguita in precedenza per usare l'interfaccia utente di Swagger.

	Poiché si è lasciato il codice HttpClient, l'output questa volta mostra un set di nomi duplicato.

	![](./media/app-service-api-dotnet-consume-internal/dupnames.png)
  
## Passaggi successivi

Questo articolo ha illustrato come usare un'app per le API interna da un client .NET. Per informazioni su come usare le app per le API impostare sui livelli di accesso **Pubblico (anonimo)** e **Pubblico (autenticato)**, vedere [Usare un'app per le API nel servizio app di Azure da un client .NET](app-service-api-dotnet-consume.md).

Per altri esempi di codice per chiamare app per le API dai client .NET, scaricare l'applicazione di esempio [Azure Cards](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample).

<!---HONumber=58-->