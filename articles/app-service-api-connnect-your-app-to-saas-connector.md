<properties 
	pageTitle="Connettere l'app a un connettore SaaS esistente" 
	description="Questo articolo descrive come connettersi a un connettore SaaS esistente" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="VinayaReddy" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="vinayr;tdykstra"/>

# Connettere l'app a un connettore SaaS esistente

## Informazioni generali

In questa esercitazione viene illustrato come usare un connettore SaaS in Azure Marketplace. 

Si eseguirà la procedura seguente:
- Eseguire il provisioning del connettore Dropbox.
- Configurare l'app per le API Dropbox.
- Configurare il gateway.
- Acquisire e memorizzare il token nell'archivio di token. 
- Chiamare l'app per le API per verificare il corretto funzionamento dell'accesso autenticato.

## Eseguire il provisioning del connettore Dropbox

1. Passare alla home page del portale di anteprima e fare clic su Marketplace.

	![](./media/app-service-api-connect-your-app-to-saas-connector/01-Marketplace.png)

2. Cercare Dropbox nella raccolta Marketplace.

	![](./media/app-service-api-connect-your-app-to-saas-connector/02-Marketplace-search.png)
 
3. Fare clic sull'icona Dropbox per effettuare il provisioning del connettore Dropbox. Fare clic sul pulsante "Crea" per effettuare il provisioning del connettore Dropbox. Assicurarsi di inserire il nome e i valori desiderati per tutti i campi prima di fare clic sul pulsante "Crea". 

	![](./media/app-service-api-connect-your-app-to-saas-connector/03-Dropbox-Connector-Blade.png) 

4. Esaminare il pannello del gruppo di risorse nel [portale di anteprima](https://portal.azure.com/). Sono visualizzati il connettore Dropbox e il gateway. Il provisioning dell'app per le API è stato eseguito in modo che l'app faccia parte di un gruppo di risorse. Quando il portale ha creato automaticamente il gruppo di risorse, ha creato anche un gateway. Un gateway è una speciale app Web che gestisce tutte le richieste destinate alle app per le API del gruppo di risorse.

	È possibile aggiungere app Web e altre app per le API allo stesso gruppo di risorse e ogni app per le API del gruppo di risorse può avere una delle tre impostazioni di accessibilità seguenti:

	* Pubblico (anonimo): chiunque può chiamare l'app per le API dall'esterno del gruppo di risorse senza aver effettuato l'accesso.
	* Pubblico (autenticato): solo agli utenti autenticati è consentito chiamare l'app per le API dall'esterno del gruppo di risorse.
	* Interno: solo alle altre app per le API o alle app Web dello stesso gruppo di risorse è consentito chiamare l'app per le API.

## Configurare l'app per le API Dropbox

Prima di tutto, configurare Dropbox in modo da accettare solo richieste autenticate.  Impostare l'accessibilità su **Pubblico (autenticato)** e configurare il gateway per richiedere l'autenticazione da un provider di Azure Active Directory, Google o Facebook.

1.	Nel [portale di anteprima di Azure](https://portal.azure.com/) fare clic su **Sfoglia > App per API**, e quindi fare clic sul nome dell'app per le API che si vuole proteggere.

	![](./media/app-service-api-connect-your-app-to-saas-connector/04-Browse-API-Apps.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/05-Dropbox-Connector.png) 
 
2.	Nel pannello App per le API fare clic su Impostazioni e quindi su Impostazioni applicazione.
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/06-Dropbox-connector-properties.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/07-dropbox-settings-dialog.png) 

3.	Nel pannello **Impostazioni applicazione** modificare **Livello di accesso** su **Pubblico (autenticato)**. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/08-public-auth-setting-blade.png) 

	Il connettore Dropbox ora è protetto dall'accesso non autenticato. Ora è possibile configurare l'autenticazione di Dropbox. Immettere i dettagli *ID Client* e "Segreto client* nella UI mostrata di seguito (è possibile connettere *Client ID* e "Segreto client* dall'[account per sviluppatore di dropbox](https://www.dropbox.com/developers/apps) ).

	![](./media/app-service-api-connect-your-app-to-saas-connector/09-Dropbox-authentication-settings.png) 

Ora è necessario configurare il gateway per specificare il provider di autenticazione da usare.

## Configurare il gateway

1. Tornare al pannello App per le API e quindi fare clic sul collegamento al gateway.
 
	![](./media/app-service-api-connect-your-app-to-saas-connector/10-dropbox-connector-gateway.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/11-gateway-all-properties.png)

	![](./media/app-service-api-connect-your-app-to-saas-connector/12-gateway-property-settings-blade.png) 

2. Scegliere il provider di identità che si vuole usare e attenersi alla procedura riportata nell'articolo corrispondente per configurare l'app per le API con tale provider. Questi articoli sono stati scritti per le app mobili, ma le procedure sono le stesse per le app per le API. Alcune delle procedure richiedono l'uso sia del [portale di gestione](https://manage.windowsazure.com/) che del [portale di anteprima](https://portal.azure.com/).
  
	- [Account Microsoft](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-microsoft-authentication-preview)
	- [Accesso Facebook](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-facebook-authentication-preview)
	- [Accesso Twitter](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-twitter-authentication-preview)
	- [Accesso Google](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-google-authentication-preview)
	- [Azure Active Directory](https://github.com/Azure/azure-content-pr/blob/release-app-services/app-service-mobile-how-to-configure-active-directory-authentication-preview)

	Consultare l'articolo "Proteggere un'app per le API: aggiungere l'autenticazione di Azure Active Directory o del provider di servizi di social networking" per istruzioni dettagliate su come effettuare la configurazione. 

## Acquisire e memorizzare il token OAuth nell'archivio di token.

1.	Nel browser andare all'URL di accesso: 

	È possibile acquisire l'URL dal pannello di setup di Active Directory del gateway.
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-01-gateway-login-URL-portal.png) 
	
	L'URL avrà il formato seguente...
	http://[resourcegroupname]gateway.azurewebsites.net/login/[providername]

	Ad esempio, se al gruppo di risorse è stato assegnato il nome "MyResource" e il gateway è stato configurato per l'autenticazione di Azure Active Directory, l'URL sarà uguale al seguente:

		http://Myresourcegateway.azurewebsites.net/login/aad
	
	Assicurarsi di avviare gli strumenti di sviluppo prima di immettere l'URL nel browser e premere INVIO. Dovrebbe apparire un messaggio simile ad "Accesso completato". 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-02-gateway-login-URL-Browser.png) 

	![](./media/app-service-api-connect-your-app-to-saas-connector/14-03-gateway-login-confirmation.png) 

	Acquisire il token e il valore di autorizzazione Zumo usando la scheda Rete (Chrome).
	![](./media/app-service-api-connect-your-app-to-saas-connector/14-04-Acquire-Zumo-Auth-Token.png) 

	Visualizzare l'estensione postman in Chrome. Immettere l'URL di autorizzazione del gateway e il token e il valore di autorizzazione Zumo (intestazione) ed effettuare la richiesta POST. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/15-login-to-the-connector.png) 
 
	Acquisire l'URL di reindirizzamento restituito dalla richiesta POST e verificare che l'URL di reindirizzamento funzioni. 

	![](./media/app-service-api-connect-your-app-to-saas-connector/16-redirect-url-validate.png)


	Se l'URL di reindirizzamento funziona significa che il token è valido. Tutte le chiamate effettuate al connettore SaaS useranno il token definito e verificato. Questo token deve essere incluso in tutte le richieste da origini esterne che passano attraverso il gateway. Quando si accede a un'API con un browser, il browser in genere archivia il token in un cookie e lo invia con tutte le chiamate successive all'API.

Lo stesso flusso è applicabile a tutti i connettori SaaS come Safesforce, Facebook e così via. 

## Chiamare l'app per le API per verificare il corretto funzionamento dell'accesso autenticato.

1. Tornare all'elenco di app per le API e selezionare il connettore Dropbox. 

1. Prendere nota dell'URL nella parte superiore.

2. Fare clic sul connettore Dropbox (come illustrato nell'immagine) per vedere tutte le API supportate.

	![](./media/app-service-api-connect-your-app-to-saas-connector/13-dropbox-api-app-operations.png) 

	Le operazioni supportate nell'app per le API sono indicate nel pannello Definizione API. È possibile scaricare swagger utilizzabili in Visual Studio per generare client fortemente tipizzati. Inoltre, è possibile scaricare swaddle utilizzabili in Sienna e PowerApp Studio. 

2. In una finestra del browser digitare l'URL copiato dal portale e aggiungere qualsiasi API supportata per accedere ai file o ad altri dettagli nel proprio account Dropbox. 

	Formato: `<URL>`/Operazione

	Ad esempio:

		https://dropboxconnector7b47555bd6784237ad3e7736da769ffc.azurewebsites.net/folder/photos
   
	Poiché è già stata stabilita l'autenticazione appropriata, la chiamata precedente ha esito positivo e consente di visualizzare i dettagli della cartella delle foto nel browser. 
	![](./media/app-service-api-connect-your-app-to-saas-connector/17-call-dropbox-method-from-browser.png) 

<!--todo<Copy the image of the browser>-->

## Passaggi successivi

Si è appreso come proteggere un'app per le API e configurare il gateway per accedere al connettore SaaS usando l'archivio dei token. Per altre informazioni, vedere [Cosa sono le app per le API?](app-service-api-apps-why-best-platform.md). 

<!--HONumber=49-->