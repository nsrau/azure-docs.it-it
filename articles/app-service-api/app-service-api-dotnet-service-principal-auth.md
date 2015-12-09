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
	ms.date="11/28/2015"
	ms.author="tdykstra"/>

# Autenticazione dell'entità servizio per app per le API nel servizio app di Azure

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Panoramica

Questa esercitazione illustra come usare le funzionalità di autenticazione e autorizzazione del servizio app di Azure per proteggere un'app per le API e come utilizzare un'app per le API protetta per conto di un account del servizio. Un account del servizio è noto anche come *entità servizio* e l'autenticazione che usa un account di questo tipo è nota anche come scenario *da servizio a servizio*. In questa esercitazione viene protetta un'app per le API in uno scenario da servizio a servizio usando Azure Active Directory per l'autenticazione e utilizzando l'API da un client .NET.

L'esercitazione usa l'API Web ASP.NET sia per il client chiamante che per l'API chiamata, ma le tecniche illustrate si applicano anche ad altri linguaggi e framework supportati dal servizio app di Azure. Il codice client riportato di seguito è il codice standard di Azure Active Directory per ottenere e passare un token di connessione per un account del servizio. Non è necessario alcun codice speciale di Azure, come quello usato per la gestione del token zumo dei servizi mobili.

Questa è la quarta di una serie di esercitazioni che illustrano come usare le app per le API nel servizio app di Azure. Per informazioni sulla serie, vedere la prima esercitazione: [Introduzione alle app per le API e ad ASP.NET nel servizio app di Azure](app-service-api-dotnet-get-started.md). Per informazioni sull'autenticazione e l'autorizzazione nel servizio app di Azure, vedere l'esercitazione precedente della serie: [Autenticazione utente per app per le API nel servizio app di Azure](app-service-api-dotnet-user-principal-auth.md).

## Altre opzioni per l'autenticazione da servizio a servizio

Per gestire uno scenario da servizio a servizio senza l'autenticazione e l'autorizzazione del servizio app, ad esempio usando certificati client, vedere la sezione [Passaggi successivi](#next-steps).

## Progetto di esempio CompanyUsers.API

In questa esercitazione verranno usati i progetti di esempio scaricati nella [prima esercitazione di questa serie](app-service-api-dotnet-get-started.md) e le risorse di Azure (app per le API e app Web) create nelle esercitazioni precedenti.

Il progetto CompanyUsers.API è un semplice progetto di API Web contenente un metodo Get che restituisce un elenco di contatti hardcoded. Per illustrare uno scenario da servizio a servizio, il metodo Get in ContactsList.API chiama il metodo Get in CompanyContacts.API e aggiunge i contatti ottenuti a tutti i valori presenti nel relativo archivio dati, quindi restituisce l'elenco combinato.

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

L'oggetto client per CompanyContacts.API è una modifica del codice client dell'app per le API generato che aggiunge un token alla richiesta HTTP.

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

4. Nella finestra di dialogo del servizio app, scegliere la **sottoscrizione** di Azure da usare e quindi fare clic su **Nuovo**.

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
 
4. Nel metodo Get rimuovere il commento dal blocco di codice che usa l'oggetto client restituito da `CompanyContactsAPIClientWithAuth`.

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

3. Nella procedura guidata **Pubblicazione del sito Web** fare clic su **Pubblica**.

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

15. Nella parte inferiore della pagina fare clic su **Gestisci manifesto > Scarica manifesto** e salvare il file in un percorso in cui è possibile modificarlo.

16. Nel file manifesto scaricato cercare la proprietà `oauth2AllowImplicitFlow`. Modificare il valore di questa proprietà da `false` a `true` e quindi salvare il file.

16. Fare clic su **Gestisci manifesto > Carica manifesto** e caricare il file aggiornato nel passaggio precedente.

17. Tenere aperta questa pagina per poter copiare e incollare i valori da qui e aggiornare i valori nella pagina nei passaggi successivi dell'esercitazione.

## Aggiornare le impostazioni nell'app per le API che esegue il codice del progetto ContactsList.API

1. Nel [portale di Azure](https://portal.azure.com/) passare al pannello App per le API dell'app per le API in cui è stato distribuito il progetto ContactsList.API. Questa è la l'app per le API chiamante, non quella chiamata, appena creata in questa esercitazione.

2. Fare clic su **Impostazioni > Impostazioni applicazione**.

	Qui vanno aggiunte alcune impostazioni, ma è necessario ottenerle da un'altra pagina del portale di Azure classico.

3. Nel [portale di Azure classico](https://manage.windowsazure.com/) passare alla scheda **Configura** dell'applicazione AAD creata per l'app per le API ContactsList.API.

5. In **chiavi** selezionare **1 anno** dall'elenco a discesa **Seleziona durata**.

6. Fare clic su **Save**.

	![](./media/app-service-api-dotnet-service-principal-auth/genkey.png)


7. Copiare il valore della chiave.

	![](./media/app-service-api-dotnet-service-principal-auth/genkeycopy.png)

3. Nel pannello **Impostazioni applicazione** del portale di Azure, sezione **Impostazioni app**, aggiungere una chiave denominata ida:ClientSecret e nel campo valore incollare la chiave appena creata.

3. Nel portale di Azure classico passare alla scheda **Configura** dell'applicazione AAD creata per l'app per le API CompanyContacts.API.

4. Copiare l'ID client.

3. Nel pannello **Impostazioni applicazione** del portale di Azure, sezione **Impostazioni app**, aggiungere una chiave denominata ida:Resource e nel campo valore incollare l'ID client appena copiato.

4. Aggiungere una chiave denominata CompanyContactsAPIUrl e nel campo valore immettere https://{nome dell'app per le API}.azurewebsites.net, ad esempio: https://companycontactsapi.azurewebsites.net.

6. Fare clic su Salva.

	![](./media/app-service-api-dotnet-service-principal-auth/appsettings.png)

## Testare in Azure

1. Passare all'URL dell'app Web in cui è stato distribuito il progetto ContactsList.Angular.AAD.

2. Fare clic sulla scheda **Contatti** ed eseguire l'accesso.

	Viene visualizzata la pagina Contatti con i contatti aggiuntivi recuperati dall'app per le API CompanyContacts.API.

	![](./media/app-service-api-dotnet-service-principal-auth/contactspagewithdavolio.png)

## Passaggi successivi

Questa è l'ultima esercitazione della serie introduttiva alle app per le API. Questa sezione contiene suggerimenti aggiuntivi per ottenere altre informazioni sull'uso delle app per le API.

* Altri modi di utilizzare un'app per le API protetta dall'autorizzazione e dall'autenticazione del servizio app di Azure.

	Questo articolo illustra come proteggere un'app per API e chiamarla dal codice in esecuzione in un'altra app per le API. Per informazioni su come chiamare un'app per le API protetta da un'app per la logica, vedere [Uso dell'API personalizzata ospitata nel servizio app con App per la logica](../app-service-logic/app-service-logic-custom-hosted-api.md).

* Altri modi per proteggere un'app per le API per scenari da servizio a servizio

	Come alternativa all'autenticazione e autorizzazione del servizio app, è possibile proteggere un'app per le API usando i certificati client o l'autenticazione di base. Per informazioni sui certificati client in Azure, vedere [Come configurare l'autenticazione reciproca TLS per un'app Web](../app-service-web/app-service-web-configure-tls-mutual-auth.md).

* Altri modi per distribuire un'app del servizio app

	Per informazioni su altre modalità di distribuzione di progetti Web in app Web usando Visual Studio o [automatizzando la distribuzione](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/continuous-integration-and-continuous-delivery) da un [sistema di controllo del codice sorgente](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control), vedere [Come distribuire un'app Web di Azure](web-sites-deploy.md).

	Visual Studio consente anche di generare script di Windows PowerShell per automatizzare la distribuzione. Per ulteriori informazioni, vedere l'articolo relativo a come [automatizzare tutto e creare app per cloud reali con Azure](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/automate-everything).

* Come risolvere i problemi di un'app del servizio app

	In Visual Studio sono disponibili funzionalità che consentono di visualizzare facilmente i log di Azure in tempo reale, mentre vengono generati. È inoltre possibile attivare la modalità di debug in remoto. Per altre informazioni, vedere [Risoluzione dei problemi delle app Web di Azure in Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

* Come aggiungere un nome di dominio personalizzato e SSL

	Per informazioni su come usare SSL e il dominio personalizzato (ad esempio www.contoso.com invece di contoso.azurewebsites.net), vedere le risorse seguenti:

	* [Configurare un nome di dominio personalizzato nel servizio app di Azure](web-sites-custom-domain-name.md)
	* [Abilitare HTTPS per un sito Web di Azure](web-sites-configure-ssl-certificate.md)

<!---HONumber=AcomDC_1203_2015-->