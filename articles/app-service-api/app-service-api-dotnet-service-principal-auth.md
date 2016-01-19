<properties
	pageTitle="Autenticazione dell'entità servizio per app per le API nel servizio app di Azure | Microsoft Azure"
	description="Informazioni su come proteggere un'app per le API nel servizio app di Azure per scenari da servizio a servizio."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Autenticazione dell'entità servizio per app per le API nel servizio app di Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Panoramica

Questa esercitazione illustra come usare le funzionalità di autenticazione e autorizzazione del servizio app di Azure per proteggere un'app per le API e come utilizzare un'app per le API protetta per conto di un account del servizio. Il provider di autenticazione illustrato nell'esercitazione è Azure Active Directory. Sia il client e che l'API sono API Web ASP.NET in esecuzione in app per le API.

## Autenticazione e autorizzazione nel servizio app

Per un'introduzione alle funzionalità di autenticazione usate in questa esercitazione, vedere l'esercitazione precedente della serie [Autenticazione e autorizzazione per app per le API nel servizio app di Azure](app-service-api-authentication.md).

## Come seguire questa esercitazione

Questa esercitazione si basa su un'applicazione di esempio per cui viene scaricata e creata un'app per le API nella [prima esercitazione della serie introduttiva alle app per le API e ASP.NET](app-service-api-dotnet-get-started.md).

## Progetto di esempio CompanyUsers.API

Nell'[applicazione di esempio ContactsList](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list) il progetto CompanyUsers.API è un semplice progetto di API Web contenente un metodo Get che restituisce un elenco di contatti hardcoded. Per illustrare uno scenario da servizio a servizio, il metodo Get in ContactsList.API chiama il metodo Get in CompanyContacts.API e aggiunge i contatti ottenuti a tutti i valori presenti nel relativo archivio dati, quindi restituisce l'elenco combinato.

Questo è il metodo Get in CompanyUsers.API.

		public async Task<IEnumerable<Contact>> Get()
		{
		    var contacts = new Contact[]{
		                new Contact { Id = 1, EmailAddress = "nancy@contoso.com", Name = "Nancy Davolio"},
		                new Contact { Id = 2, EmailAddress = "alexander@contoso.com", Name = "Alexander Carson"}
		            };
		
		    return contacts;
		}


E questo è il metodo Get in ContactsList.API, che chiama CompanyContacts.API e aggiunge i risultati al relativo risultato. Parte del codice viene omessa qui per maggiore chiarezza.

		private async Task<IEnumerable<Contact>> GetContacts()
		{
		    var contacts = await _storage.Get(FILENAME);
		
		    var contactsList = contacts.ToList<Contact>();
		    using (var client = CompanyContactsAPIClientWithAuth())
		    {
		        var results = await client.Contacts.GetAsync();
		        foreach (Contact c in results)
		        {
		            contactsList.Add(c);
		        }
		    }
		
		    return contactsList;
		}

L'oggetto client restituito da `CompanyContactsAPIClientWithAuth()` nel codice precedente si basa sul codice client generato, ma aggiunge un token di autorizzazione per le richieste HTTP.

		private static CompanyContactsAPI CompanyContactsAPIClientWithAuth()
		{
		    var client = new CompanyContactsAPI(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		    client.HttpClient.DefaultRequestHeaders.Authorization =
		        new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		    return client;
		}

## Creare un'app per le API in Azure e distribuire il progetto CompanyContacts.API nell'app

1. In **Esplora soluzioni** fare clic con il pulsante destro del mouse sul progetto CompanyContacts.API e quindi scegliere **Pubblica**.

3.  Nel passaggio **Profilo** della procedura guidata **Pubblica sito Web** fare clic su **Servizio app di Microsoft Azure**.

	![](./media/app-service-api-dotnet-service-principal-auth/selectappservice.png)

4. Accedere al proprio account Azure, se non è già stato fatto, o aggiornare le credenziali se sono scadute.

4. Nella finestra di dialogo del **servizio app**, scegliere la **sottoscrizione** di Azure da usare e quindi fare clic su **Nuovo**.

	![](./media/app-service-api-dotnet-service-principal-auth/clicknew.png)

3. Nella scheda **Hosting** della finestra di dialogo **Crea servizio app** fare clic su **Tipo di modifica** e quindi su **App per le API**.

4. Immettere un **Nome app per le API** che sia univoco nel dominio *azurewebsites.net*.

6. Nell'elenco a discesa **Gruppo di risorse** selezionare il gruppo di risorse usato per queste esercitazioni.

4. Nell'elenco a discesa **Piano di servizio app** selezionare il piano usato per queste esercitazioni.

7. Fare clic su **Crea**.

	![](./media/app-service-api-dotnet-service-principal-auth/createappservice.png)

	Visual Studio crea l'app per le API e un profilo di pubblicazione che include tutte le impostazioni necessarie per la nuova app per le API.

8. Nella scheda **Connessione** della procedura guidata **Pubblica sito Web** fare clic su **Pubblica**.

	![](./media/app-service-api-dotnet-service-principal-auth/conntab.png)

	Visual Studio distribuisce il progetto alla nuova app per le API e apre un browser all'URL dell'app per le API. Viene visualizzata una pagina che informa che l'operazione di creazione è riuscita.

9. Chiudere il browser.

## Aggiornare il codice client generato nel progetto ContactsList.API

Il progetto ContactsList.API include già il codice client generato, ma è necessario eliminarlo e rigenerarlo dall'app per le API.

1. In **Esplora soluzioni** di Visual Studio nel progetto ContactsList.API eliminare la cartella *CompanyContactsAPI*.

2. Fare clic con il pulsante destro del mouse sul progetto ContactsList.API e quindi scegliere **Aggiungi > Client API REST**.

3. Nella finestra di dialogo **Aggiungi client API REST** fare clic su **Scarica dall'app per le API di Microsoft Azure** e quindi su **Sfoglia**.

8. Nella finestra di dialogo **Servizio app** espandere il gruppo di risorse usato per questa esercitazione e quindi selezionare l'app per le API appena creata.

	Se l'app per le API non viene visualizzata nell'elenco, durante la creazione dell'app per le API il tipo potrebbe non essere stato modificato da app Web in app per le API. In tal caso, è possibile creare una nuova app per le API ripetendo i passaggi eseguiti in precedenza. È necessario scegliere un nome diverso per l'app per le API, a meno che prima non venga eliminata l'app Web dal portale.

10. Fare clic su **OK**.

9. Nella finestra di dialogo **Aggiungi client API REST** fare clic su **OK**.

	Visual Studio crea una cartella con il nome dell'app per le API e genera classi client.

## Aggiornare il codice in ContactsList.API e distribuire il progetto

Il codice in ContactsList.API che chiama CompanyContacts.API è stato commentato per le esercitazioni precedenti. In questa sezione viene rimosso il commento al codice e viene distribuita l'app.

1. Nel progetto ContactsList.API aprire *Controllers/ContactsController.cs*.

2. All'inizio della classe `ContactsController`, nel codice che usa la classe client generata per aggiungere un token di autorizzazione, sostituire il nome della classe `CompanyContactsAPI` con il nome della classe generata dall'app per le API.

	Ad esempio, se l'app per le API è denominata CompanyContactsAPI3, il codice sarà simile al seguente:

		 private static CompanyContactsAPI3 CompanyContactsAPIClientWithAuth()
		 {
		     var client = new CompanyContactsAPI3(new Uri(ConfigurationManager.AppSettings["CompanyContactsAPIUrl"]));
		     client.HttpClient.DefaultRequestHeaders.Authorization =
		         new AuthenticationHeaderValue("Bearer", ServicePrincipal.GetS2SAccessTokenForProdMSA().AccessToken);
		     return client;
		 }
 
4. Nel metodo `Get` rimuovere il commento dal blocco di codice che chiama CompanyContacts.API.

		using (var client = CompanyContactsAPIClientWithAuth())
		{
		    var results = await client.Contacts.GetAsync();
		    foreach (Contact c in results)
		    {
		        contactsList.Add(c);
		    }
		}

2. Fare clic con il pulsante destro del mouse sul progetto ContactsList.API e quindi scegliere **Pubblica**.

	Verrà visualizzata la procedura guidata **Pubblica sito Web** per il profilo di pubblicazione usato in precedenza.

3. Nella procedura guidata **Pubblica sito Web** fare clic su **Pubblica**.

	Visual Studio distribuisce il progetto e apre una finestra del browser all'URL di base dell'app per le API. Chiudere la finestra del browser.

## Configurare l'autenticazione e l'autorizzazione in Azure per la nuova app per le API

1. Nel [portale di Azure](https://portal.azure.com/) passare al pannello App per le API dell'app per le API creata in questa esercitazione per il progetto CompanyContacts.API e quindi fare clic su **Impostazioni**.

2. Trovare la sezione **Funzionalità** e quindi fare clic su **Autenticazione/Autorizzazione**.

3. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Sì**.

4. Nell'elenco a discesa **Azione da eseguire quando la richiesta non è autenticata** selezionare **Accedi con Azure Active Directory**.

5. In **Provider di autenticazione** fare clic su **Azure Active Directory**.

6. Nel pannello **Impostazioni di Azure Active Directory** fare clic su **Rapide**.

	Azure creerà automaticamente un'applicazione AAD nel tenant AAD. Prendere nota del nome della nuova applicazione AAD, perché sarà necessario selezionarlo più avanti quando si passerà al portale di Azure classico per ottenere il relativo ID client.

7. Fare clic su **OK**.

10. Nel pannello **Autenticazione/Autorizzazione** fare clic su **Salva**.
 
11. Nel [portale di Azure classico](https://manage.windowsazure.com/) passare a **Azure Active Directory**.

12. Nella scheda **Directory** fare clic sul tenant AAD.

14. Fare clic su **Applicazioni > Applicazioni di proprietà dell'azienda** e quindi fare clic sul segno di spunta.

15. Nell'elenco delle applicazioni fare clic sul nome di quella creata automaticamente da Azure quando è stata abilitata l'autenticazione per l'app per le API.

16. Fare clic su **Configure**.

17. Tenere aperta questa pagina per poter copiare e incollare i valori da qui e aggiornare i valori nella pagina nei passaggi successivi dell'esercitazione.

## Aggiornare le impostazioni nell'app per le API che esegue il codice del progetto ContactsList.API

3. Accedere al [portale di Azure classico](https://manage.windowsazure.com/) in un'altra finestra del browser e quindi passare alla scheda **Configura** dell'applicazione AAD creata per l'app per le API ContactsList.API.

5. In **chiavi** selezionare **1 anno** dall'elenco a discesa **Seleziona durata**.

6. Fare clic su **Save**.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)

7. Copiare il valore della chiave.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

1. Accedere al [portale di Azure](https://portal.azure.com/) in un'altra finestra del browser e quindi passare al pannello App per le API dell'app per le API in cui è stato distribuito il progetto ContactsList.API. Si tratta dell'app per le API chiamante, non di quella chiamata: ContactsList.API, non CompanyContacts.API.

2. Fare clic su **Impostazioni > Impostazioni applicazione**.

3. Nella sezione **Impostazioni app**, aggiungere una chiave denominata ida:ClientSecret e nel campo valore incollare la chiave appena creata.

3. Aggiungere una chiave denominata "ida:ClientId" e nel campo valore incollare l'ID client dalla stessa pagina **Configura** di AAD.

4. Aggiungere una chiave denominata "ida:Authority" e, nel campo valore immettere "https://login.windows.net/{tenant}". Ad esempio, "https://login.windows.net/contoso.onmicrosoft.com".

3. Nel portale di Azure classico passare alla scheda **Configura** dell'applicazione AAD creata per l'app per le API CompanyContacts.API.

4. Copiare l'ID client.

3. Nel pannello **Impostazioni applicazione** del portale di Azure, sezione **Impostazioni app**, aggiungere una chiave denominata ida:Resource e nel campo valore incollare l'ID client appena copiato.

4. Aggiungere una chiave denominata "CompanyContactsAPIUrl" e nel campo valore immettere "https://{nome dell'app per le API}.azurewebsites.net". Ad esempio: "https://companycontactsapi.azurewebsites.net".

6. Fare clic su Salva.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## Testare in Azure

1. Passare all'URL dell'app Web in cui è stato distribuito il progetto ContactsList.Angular.AAD.

2. Fare clic sulla scheda **Contatti** ed eseguire l'accesso.

	Viene visualizzata la pagina Contatti con i contatti aggiuntivi recuperati dall'app per le API CompanyContacts.API.

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

Come nell'esercitazione precedente, è anche possibile configurare i progetti di Visual Studio con URL SSL localhost ed eseguire l'applicazione in locale. In tal caso, è possibile archiviare nel file Web.config le impostazioni archiviate in Azure per l'esecuzione in Azure (ID client, segreto client e così via). Si consiglia, tuttavia, di non archiviare nel controllo del codice sorgente un file Web.config che contenga informazioni riservate, ad esempio il segreto client. Per altre informazioni, vedere l'articolo [Procedure consigliate per la distribuzione di password e altri dati sensibili in ASP.NET e nel servizio app di Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure).

## Proteggere l'app per le API dall'accesso dal browser

Per questa esercitazione viene usata l'opzione Rapide del portale di Azure per configurare l'autenticazione AAD per l'app per le API a cui accedere con l'autenticazione dell'entità servizio. Per impostazione predefinita, il servizio App configura la nuova applicazione AAD in modo da consentire all'utente di passare all'URL dell'app per le API in un browser ed eseguire l'accesso. Ciò significa che anche un utente finale può accedere all'API, non solo un'entità servizio. Per fare in modo che sia solo l'entità servizio ad avere accesso all'API, è possibile impedire l'accesso dal browser modificando l'**URL di risposta** nell'applicazione AAD in perché sia diverso dall'URL di base dell'app per le API.

### Verificare il funzionamento dell'accesso dal browser

1. In una nuova finestra del browser passare all'URL HTTPS dell'app per le API creata per il progetto CompanyContacts.API.

	Il browser visualizza una schermata di accesso.
	
2. Accedere con le credenziali per un utente incluso nel tenant AAD.

3. Il browser visualizza una schermata di conferma della creazione dell'app per le API.

	Se l'interfaccia utente Swagger fosse abilitata, sarebbe possibile visualizzare l'URL `/swagger` e chiamare l'API. Per chiamare l'API dal browser, aggiungere `/api/contacts/get` all'URL.

### Disabilitare l'accesso dal browser

1. Nella scheda **Configura** del portale classico per l'applicazione AAD creata per il progetto CompanyContacts.API, modificare il valore del campo **URL di risposta** in modo che sia un URL valido ma non l'URL dell'app per le API.
 
2. Fare clic su **Save**.

### Verificare che non sia più possibile accedere dal browser

1. In una nuova finestra del browser passare nuovamente all'URL dell'app per le API.

2. Eseguire l'accesso quando viene richiesto di farlo.

3. L'accesso ha esito positivo, ma viene visualizzata una pagina di errore.

	È comunque possibile accedere all'app per le API con un token dell'entità servizio, ma gli utenti nel tenant AAD non possono accedere all'API da un browser.

## Passaggi successivi

Questa è l'ultima esercitazione della serie introduttiva alle app per le API. Questa sezione contiene suggerimenti aggiuntivi per ottenere altre informazioni sull'uso delle app per le API.

* Altri modi per distribuire un'app del servizio app

	Per informazioni su altre modalità di distribuzione di progetti Web in app Web tramite Visual Studio o [automatizzando la distribuzione](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) da un [sistema di controllo del codice sorgente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), vedere [Come distribuire un'app Web di Azure](web-sites-deploy.md).

	Visual Studio consente anche di generare script di Windows PowerShell per automatizzare la distribuzione. Per ulteriori informazioni, vedere l'articolo relativo a come [automatizzare tutto e creare app per cloud reali con Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Come risolvere i problemi di un'app del servizio app

	In Visual Studio sono disponibili funzionalità che consentono di visualizzare facilmente i log di Azure in tempo reale, mentre vengono generati. È inoltre possibile attivare la modalità di debug in remoto. Per altre informazioni, vedere [Risoluzione dei problemi delle app Web di Azure in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* Come aggiungere un nome di dominio personalizzato e SSL

	Per informazioni su come usare SSL e il dominio personalizzato (ad esempio www.contoso.com invece di contoso.azurewebsites.net), vedere le risorse seguenti:

	* [Configurare un nome di dominio personalizzato nel servizio app di Azure](web-sites-custom-domain-name.md)
	* [Abilitare HTTPS per un sito Web di Azure](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_0114_2016-->