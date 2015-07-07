<properties 
	pageTitle="Usare un'app per le API nel servizio app di Azure da un client .NET" 
	description="Informazioni su come usare un'app per le API da un client .NET con App Service SDK." 
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
	ms.date="05/16/2015" 
	ms.author="tdykstra"/>

# Usare un'app per le API nel servizio app di Azure da un client .NET 

## Panoramica

Questa esercitazione descrive come usare App Service SDK per scrivere codice che chiama un'[app per le API](app-service-api-apps-why-best-platform.md) configurata per il livello di accesso **Pubblico (anonimo)** o **Pubblico (autenticato)**. Questo articolo illustra gli scenari di esempio seguenti:

- Chiamare un'app per le API con livello di accesso **Pubblico (anonimo)** da un'applicazione console
- Chiamare un'app per le API con livello di accesso **Pubblico (anonimo)** da un'applicazione desktop di Windows 

Le sezioni dell'esercitazione sono indipendenti: è possibile seguire le istruzioni per il secondo scenario senza aver completato i passaggi per il primo.

Per informazioni su come chiamare un'app per le API **interna**, vedere [Utilizzare un'app per le API interna da un client .NET](app-service-api-dotnet-consume-internal.md).

## Prerequisiti

Nell'esercitazione si presuppone che l'utente abbia familiarità con le procedure per creare progetti e aggiungervi codice in Visual Studio e per [gestire le app per le API nel portale di anteprima di Azure](app-service-api-apps-manage-in-portal.md).

Il progetto e gli esempi di codice mostrati in questo articolo sono basati sul progetto di app per le API. Per informazioni su come creare, distribuire e proteggere questo progetto, vedere:

- [Creare un'app per le API](app-service-dotnet-create-api-app.md)
- [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md)
- [Proteggere un'app per le API](../app-service-dotnet-add-authentication.md)

[AZURE.INCLUDE [install-sdk-2013-only](../../includes/install-sdk-2013-only.md)]

Per eseguire questa esercitazione, è necessario installare Azure SDK per .NET versione 2.6 o successive.

## Chiamata non autenticata da un'applicazione console

In questa sezione verrà creato un progetto di applicazione console e vi si aggiungerà il codice che chiama un'app per le API che non richiede l'autenticazione.

### Configurare l'app per le API e creare il progetto

1. Se necessario, seguire le istruzioni fornite in [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md) per distribuire il progetto di esempio ContactsList in un'app per le API nella sottoscrizione di Azure.

	Nell'esercitazione viene indicato di impostare il livello di accesso nella finestra di dialogo Pubblica di Visual Studio su **Disponibile per chiunque**, che corrisponde all'opzione **Pubblico (anonimo)** nel portale. Se, tuttavia, è stata eseguita l'esercitazione [Proteggere un'app per le API](../app-service-dotnet-add-authentication.md), il livello di accesso è stato impostato su **Pubblico (autenticato)**. In questo caso, sarà necessario cambiare l'impostazione come illustrato nel passaggio seguente.

2. Nel [portale di anteprima di Azure](https://portal.azure.com/), nel pannello **App per le API** dell'app per le API da chiamare, passare a **Impostazioni > Impostazioni applicazione** e impostare **Livello di accesso** su **Pubblico (anonimo)**.

	![](./media/app-service-api-dotnet-consume/setpublicanon.png)
 
2. In Visual Studio creare un nuovo progetto di Applicazione console.
 
### Aggiungere il codice client generato da App Service SDK

3. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto (non sulla soluzione) e quindi scegliere **Aggiungi > Client app per le API di Azure**. 

	![](./media/app-service-api-dotnet-consume/03-add-azure-api-client-v3.png)
	
3. Nella finestra di dialogo **Aggiungi client app per le API di Azure** fare clic su **Scarica da app per le API di Azure**.

5. Nell'elenco a discesa selezionare l'app per le API da chiamare.

7. Fare clic su **OK**.

	![Schermata di generazione](./media/app-service-api-dotnet-consume/04-select-the-api-v3.png)

	La procedura guidata scaricherà il file di metadati dell'API e genererà un'interfaccia tipizzata per richiamare l'app per le API.

	![Generazione in corso](./media/app-service-api-dotnet-consume/05-metadata-downloading-v3.png)

	Al termine della generazione di codice, in **Esplora soluzioni** verrà visualizzata una nuova cartella con il nome dell'app per le API. Questa cartella contiene il codice che implementa le classi client e i modelli di dati.

	![Generazione completata](./media/app-service-api-dotnet-consume/06-code-gen-output-v3.png)

### Aggiungere il codice per chiamare l'app per le API

Per chiamare l'app per le API, è sufficiente creare un oggetto client e chiamare i metodi su di esso, come illustrato nell'esempio seguente:

        var client = new ContactList();
        var contacts = client.Contacts.Get();

1. Aprire *Program.cs* e aggiungere al metodo `Main` il codice seguente.

		var client = new ContactsList();
		
		// Send GET request.
		var contacts = client.Contacts.Get();
		foreach (var c in contacts)
		{
		    Console.WriteLine("{0}: {1} {2}",
		        c.Id, c.Name, c.EmailAddress);
		}
		
		// Send POST request.
		client.Contacts.Post(new Models.Contact
		{
		    EmailAddress = "lkahn@contoso.com",
		    Name = "Loretta Kahn",
		    Id = 4
		});
		Console.WriteLine("Finished");
		Console.ReadLine();

3. Premere CTRL+F5 per eseguire l'applicazione.

	![Generazione completata](./media/app-service-api-dotnet-consume/consoleappoutput.png)

## Chiamata autenticata da un'applicazione desktop di Windows

In questa sezione verrà creato un progetto di applicazione desktop e vi si aggiungerà il codice che chiama un'app per le API che richiede l'autenticazione. Questo codice implementa il *flusso di autenticazione server* Oauth 2, affinché sia il gateway dell'app per le API, invece dell'applicazione client, a ottenere il token dal provider di autenticazione.

Le app per le API di Azure supportano anche il flusso di autenticazione client. In futuro, a questa esercitazione verrà aggiunto anche lo scenario relativo al flusso di autenticazione client.

### Configurare l'app per le API e creare il progetto

1. Seguire i passaggi descritti nell'esercitazione [Proteggere un'app per le API](../app-service-dotnet-add-authentication.md) per configurare un'app per le API con il livello di accesso **Pubblico (autenticato)**.

1. In Visual Studio creare un progetto desktop Windows Form.

2. In Progettazione Windows Form aggiungere i controlli seguenti:

	* Controllo pulsante
	* Controllo casella di testo
	* Controllo Web browser

3. Impostare il controllo casella di testo su multilinea.

	Verrà visualizzato un form simile al seguente:

	![](./media/app-service-api-dotnet-consume/form.png)

### Aggiungere il codice client generato da App Service SDK

3. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto (non sulla soluzione) e quindi scegliere **Aggiungi > Client app per le API di Azure**. 

3. Nella finestra di dialogo **Aggiungi client app per le API di Azure** fare clic su **Scarica da app per le API di Azure**.

5. Nell'elenco a discesa selezionare l'app per le API da chiamare e quindi fare clic su **OK**.

### Aggiungere il codice per chiamare l'app per le API

5. Nel portale di anteprima di Azure copiare l'URL del gateway dell'app per le API. Questo valore verrà usato nel passaggio successivo.

	![](./media/app-service-api-dotnet-consume/gatewayurl.png)

4. Nel codice sorgente *Form1.cs* aggiungere il codice seguente prima del costruttore `Form1()`, sostituendo il valore di GATEWAY_URL con il valore copiato nel passaggio precedente. Assicurarsi di includere la barra finale (/).

		private const string GATEWAY_URL = "https://resourcegroupnameb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string URL_TOKEN = "#token=";

4. In Progettazione Windows Form fare doppio clic sul pulsante per aggiungere un gestore di eventi clic, quindi nel metodo del gestore aggiungere il codice per passare all'URL di accesso per il gateway, ad esempio:

		webBrowser1.Navigate(string.Format(@"{0}login/[authprovider]", GATEWAY_URL));

	Sostituire "[authprovider]" con il codice per il provider del servizio di gestione delle identità configurato nel gateway, ad esempio, "aad", "twitter", "google", "microsoftaccount" o "facebook". Ad esempio:

		webBrowser1.Navigate(string.Format(@"{0}login/aad", GATEWAY_URL));

7. Aggiungere un gestore eventi `DocumentCompleted` per il controllo Web browser e aggiungere il codice seguente al metodo del gestore.

		if (e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		{
		    var encodedJson = e.Url.AbsoluteUri.Substring(e.Url.AbsoluteUri.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		    var decodedJson = Uri.UnescapeDataString(encodedJson);
		    var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		    string userId = result.user.userId;
		    string userToken = result.authenticationToken;
		
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    appServiceClient.SetCurrentUser(userId, userToken);
		
		    var contactsListClient = appServiceClient.CreateContactsList();
		    var contacts = contactsListClient.Contacts.Get();
		    foreach (Contact contact in contacts)
		    {
		        textBox1.Text += contact.Name + " " + contact.EmailAddress + System.Environment.NewLine;
		    }
		    //appServiceClient.Logout();
		    //webBrowser1.Navigate(string.Format(@"{0}login/aad", GW_URL));
		}

	Il codice aggiunto viene eseguito dopo che l'utente completa la procedura di accesso tramite il controllo Web browser. Se l'accesso ha esito positivo, l'URL di risposta contiene l'ID utente e la password. Il codice estrae questi valori dall'URL, li fornisce all'oggetto client del servizio app e quindi usa l'oggetto per creare un oggetto client app per le API. È quindi possibile chiamare l'API chiamando i metodi su questo oggetto client app per le API.

8. Premere CTRL+F5 per eseguire l'applicazione.

9. Fare clic sul pulsante e quando il controllo browser visualizza una pagina di accesso immettere le credenziali di un utente autorizzato a chiamare l'app per le API.

	Azure autentica l'utente, l'applicazione chiama l'app per le API e visualizza la risposta.

	![](./media/app-service-api-dotnet-consume/formaftercall.png)

## Passaggi successivi

In questo articolo è stato illustrato come usare un'app per le API da un client .NET per le app per le API impostate sui livelli di accesso **Pubblico (autenticato)** e **Pubblico (anonimo)**

Per altri esempi di codice per chiamare app per le API dai client .NET, scaricare l'applicazione di esempio [Azure Cards](https://github.com/Azure-Samples/API-Apps-DotNet-AzureCards-Sample).
 

<!---HONumber=62-->