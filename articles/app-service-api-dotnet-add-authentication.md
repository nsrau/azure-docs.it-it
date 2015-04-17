<properties 
	pageTitle="Proteggere un'app per le API di Azure" 
	description="Informazioni su come proteggere un'app per le API di Azure con Visual Studio." 
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
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Proteggere un'app per le API: aggiungere l'autenticazione di Azure Active Directory o del provider di servizi di social networking

## Informazioni generali

Nell'esercitazione [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md), si è distribuita un'app per le API con livello di accesso **Disponibile per chiunque**. Questa esercitazione mostra come proteggere un'app per le API in modo che solo gli utenti autenticati possano accedervi.

Si eseguirà la procedura seguente:

- Chiamare l'app per le API per verificare che sia funzionante.
- Applicare regole di autenticazione all'app per le API.
- Chiamare ancora l'app per le API per verificare che rifiuti le richieste non autenticate.
- Accedere al provider configurato.
- Chiamare ancora l'app per le API per verificare che l'accesso autenticato funzioni.

## Prerequisiti

Questa esercitazione usa l'app per le API creata in [Creare un'app per le API](app-service-dotnet-create-api-app.md) e distribuita in [Distribuire un'app per le API](app-service-dotnet-deploy-api-app.md).

## Usare il browser per chiamare l'app per le API 

Il modo più semplice per verificare che l'app per le API sia accessibile pubblicamente è chiamarla da un browser.

1. Nel browser andare al [Portale di Azure].

3. Dalla home page fare clic su **Sfoglia > App per le API** e quindi fare clic sul nome dell'app per le API che si vuole proteggere.

	![Browse](./media/app-service-api-dotnet-add-authentication/browse.png)

	![Select API app](./media/app-service-api-dotnet-add-authentication/select.png)

3. Nel pannello **App per le API** fare clic sull'**URL** per aprire una finestra del browser da cui chiamare l'app per le API.

	![API App blade](./media/app-service-api-dotnet-add-authentication/chooseapiappurl.png)

2. Aggiungere `/api/contacts/get/` all'URL nella barra degli indirizzi del browser.

	Ad esempio, se l'URL dell'app per le API è:

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/

	L'URL completo sarà:

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/api/contacts/get/

	Ogni browser gestisce le chiamate API in modo diverso. L'immagine mostra una chiamata riuscita da un browser Chrome.

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)

2. Salvare l'URL usato che dovrà essere usato di nuovo più avanti nell'esercitazione.

## Proteggere l'app per le API

L'app per le API è stata distribuita in un gruppo di risorse. È possibile aggiungere app Web e altre app per le API allo stesso gruppo di risorse e ogni app per le API del gruppo di risorse può avere una delle tre impostazioni di accessibilità seguenti:
<!--todo: diagram showing different accessibility settings-->

- **Pubblico (anonimo)**: chiunque può chiamare l'app per le API dall'esterno del gruppo di risorse senza aver effettuato l'accesso.
- **Pubblico (autenticato)**: solo agli utenti autenticati è consentito chiamare l'app per le API dall'esterno del gruppo di risorse.
- **Interno**: solo alle altre app per le API o alle app Web dello stesso gruppo di risorse è consentito chiamare l'app per le API.

Quando Visual Studio ha creato automaticamente il gruppo di risorse, ha creato anche un *gateway*.  Un gateway è una speciale app Web che gestisce tutte le richieste destinate alle app per le API del gruppo di risorse.

Quando si passa al pannello del gruppo di risorse nel [Portale di Azure],è possibile vedere l'app per le API e il gateway nel diagramma.

![Resource group diagram](./media/app-service-api-dotnet-add-authentication/rgdiagram.png)

Per configurare l'app per le API in modo che accetti solo le richieste autenticate, sarà necessario impostarne l'accessibilità su **Pubblico (autenticato)** e configurare il gateway in modo che richieda l'autenticazione a un provider, ad esempio Azure Active Directory, Google o Facebook.

1. Tornare al pannello **App per le API** per l'app per le API che si vuole proteggere.

2. Nel pannello **App per le API** fare clic su **Impostazioni** e quindi su **Impostazioni applicazione**.

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettings.png)

	![Click Application settings](./media/app-service-api-dotnet-add-authentication/clickbasicsettings.png)

3. Nel pannello **Impostazioni applicazione** impostare **Livello di accesso** su **Pubblico (autenticato)** e quindi fare clic su **Salva**.

	![Click Basic settings](./media/app-service-api-dotnet-add-authentication/setpublicauth.png)

	L'app per le API ora è protetta da accesso non autenticato. Ora è necessario configurare il gateway per specificare il provider di autenticazione da usare.

4. Scorrere di nuovo a sinistra fino al pannello **App per le API** e quindi fare clic sul collegamento al gateway.

	![Click gateway](./media/app-service-api-dotnet-add-authentication/gateway.png)

7. Nel pannello **Gateway** fare clic su **Impostazioni** e quindi su **Identità**.

	![Click Settings](./media/app-service-api-dotnet-add-authentication/clicksettingsingateway.png)

	![Click Identity](./media/app-service-api-dotnet-add-authentication/clickidentity.png)

	Dal pannello **Identità** è possibile passare ad altri pannelli per configurare l'autenticazione con Azure Active Directory e diversi altri provider.

	![Identity blade](./media/app-service-api-dotnet-add-authentication/identityblade.png)
  
3. Scegliere il provider di identità che si vuole usare e attenersi alla procedura riportata nell'articolo corrispondente per configurare l'app per le API con tale provider. Questi articoli sono stati scritti per le app mobili, ma le procedure sono le stesse per le app per le API. Per alcune procedure è necessario usare il [portale precedente]. 

 - [Account Microsoft](app-service-mobile-how-to-configure-microsoft-authentication-preview.md)
 - [Accesso Facebook](app-service-mobile-how-to-configure-facebook-authentication-preview.md)
 - [Accesso Twitter](app-service-mobile-how-to-configure-twitter-authentication-preview.md)
 - [Accesso Google](app-service-mobile-how-to-configure-google-authentication-preview.md)
 - [Azure Active Directory](app-service-mobile-how-to-configure-active-directory-authentication-preview.md)

Ad esempio, le schermate seguenti mostrano cosa verrà visualizzato nelle pagine del [portale precedente] e nei pannelli del [Portale di Azure] dopo aver configurato l'autenticazione di Azure Active Directory.

Nel portale di Azure, il pannello **Azure Active Directory** contiene l'**ID client** dell'applicazione creata nella scheda Azure Active Directory del portale precedente e in **Tenant consentiti** è visualizzato il tenant di Azure Active Directory (ad esempio, "contoso.onmicrosoft.com").

![Azure Active Directory blade](./media/app-service-api-dotnet-add-authentication/tdinaadblade.png)

Nel portale precedente, la scheda **Configura** dell'applicazione creata nella scheda **Azure Active Directory** contiene l'**URL accesso**, l'**URI ID app** e l'**URL di risposta** del pannello **Azure Active Directory** del portale di Azure.

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal1.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal2.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal3.png)

![Old portal AAD](./media/app-service-api-dotnet-add-authentication/oldportal4.png)

L'URL di risposta nell'immagine è ripetuto due volte, una volta con `http:` e una volta con `https:`.

## Verificare che l'autenticazione funzioni 

1. Aprire una finestra del browser e nella barra degli indirizzi immettere l'URL che chiama il metodo `Get` dell'app per le API, come in precedenza.

	Questa volta il tentativo di accedere all'app per le API restituisce un messaggio di errore.

	![Chrome Get response fail](./media/app-service-api-dotnet-add-authentication/chromegetfail.png)

2. Nel browser andare all'URL di accesso: 

    	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	Ad esempio, se al gruppo di risorse è stato assegnato il nome myfirstrg e il gateway è stato configurato per l'autenticazione di Azure Active Directory, l'URL sarà uguale al seguente:

    	http://myfirstrggateway.azurewebsites.net/login/aad

	Si noti che, a differenza dell'URL precedente, questo non include il nome dell'app per le API:  il gateway sta autenticando l'utente e non l'app per le API.  Il gateway gestisce l'autenticazione per tutte le app per le API del gruppo di risorse.

	In caso di problemi di accesso, provare ad aprire una finestra privata o in incognito.

3. Immettere le credenziali quando il browser visualizza una pagina di accesso. 
 
	Se si è configurato l'accesso di Azure Active Directory, usare uno degli utenti elencati nella scheda **Utenti** dell'applicazione creata nella scheda Azure Active Directory del [portale precedente], ad esempio admin@contoso.onmicrosoft.com.

	![AAD users](./media/app-service-api-dotnet-add-authentication/aadusers.png)

	![Login page](./media/app-service-api-dotnet-add-authentication/ffsignin.png)

4. Quando appare un messaggio simile ad "Accesso completato", immettere di nuovo l'URL del metodo Get dell'app per le API.

	Questa volta l'utente è stato autenticato e quindi la chiamata riesce. Il gateway riconosce l'utente autenticato e passa la richiesta all'app per le API.

	![Login completed](./media/app-service-api-dotnet-add-authentication/logincomplete.png)

	![Chrome Get response](./media/app-service-api-dotnet-add-authentication/chromeget.png)
	<!--todo:replace with image showing fictional names-->

## Usare Postman per inviare una richiesta Post

Quando si accede al gateway, il gateway invia un token di autenticazione.  Questo token deve essere incluso in tutte le richieste da origini esterne che passano attraverso il gateway. Quando si accede a un'API con un browser, il browser in genere archivia il token in un cookie e lo invia con tutte le chiamate successive all'API.

In questo modo è possibile vedere cosa accade in background. In questa sezione viene usato uno strumento del browser per creare e inviare una richiesta Post. Il token di autorizzazione viene ricevuto dal cookie e incluso in un'intestazione HTTP.

Queste istruzioni mostrano come usare lo strumento Postman nel browser Chrome, ma è possibile eseguire la stessa procedura con qualsiasi strumento client REST e strumento di sviluppo browser.

1. In una finestra del browser Chrome seguire i passaggi della sezione precedente per eseguire l'autenticazione e quindi aprire gli strumenti di sviluppo (F12).

	![Go to Resources tab](./media/app-service-api-dotnet-add-authentication/resources.png)

3. Nella scheda **Resources** degli strumenti di sviluppo di Chrome trovare i cookie per il gateway e fare tre volte clic sul valore del cookie **x-zumo-auth** per selezionarne tutti gli elementi.

	**Nota:** assicurarsi di selezionare tutti gli elementi del valore del cookie. Se si fa doppio clic, si selezionerà solo la prima parte.

5. Fare clic con il pulsante destro del mouse sull'opzione **Value** del cookie **x-zumo-auth** e quindi scegliere **Copy**.

	![Copy auth token](./media/app-service-api-dotnet-add-authentication/copyzumotoken.png)

4. Installare l'estensione Postman nel browser Chrome, se non è ancora stata installata.

6. Aprire l'estensione Postman.

7. Nel campo Request URL immettere l'URL del metodo Get dell'app per le API usato in precedenza, ma omettere `get/` alla fine.
 
		http://[apiappurl]/api/contacts
    
8. Fare clic su **Headers** e quindi aggiungere un'intestazione *x-zumo-auth*. Incollare il valore del token dagli Appunti al campo **Value**.

9. Aggiungere un'intestazione *Content-Type* con il valore *application/json*.

10. Fare clic su **form-data** e quindi aggiungere una chiave *contact* con il valore seguente:

		{   "Id": 0,   "Name": "Li Yan",   "EmailAddress": "yan@contoso.com" }

11. Fare clic su Send.

	L'app per le API restituisce la risposta *201 Created*.

	![Add headers and body](./media/app-service-api-dotnet-add-authentication/addcontact.png)

12. Per verificare che questa richiesta non può funzionare senza il token di autenticazione, eliminare l'intestazione dell'autenticazione e fare di nuovo clic su Send.

	Si ottiene una risposta *403 Forbidden*.

	![403 Forbidden response](./media/app-service-api-dotnet-add-authentication/403forbidden.png)

## Passaggi successivi

È stato appreso come proteggere un'app per le API di Azure richiedendo l'autenticazione di Azure Active Directory o del provider di servizi di social networking. Per altre informazioni, vedere [Cosa sono le app per le API?](app-service-api-apps-why-best-platform.md). 

[portale precedente]: https://manage.windowsazure.com/
[portale di Azure]: https://portal.azure.com/


<!--HONumber=49-->