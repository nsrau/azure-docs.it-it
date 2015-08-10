<properties 
	pageTitle="Autenticazione nelle app per API e dispositivi mobili del servizio app di Azure" 
	description="Informazioni su come configurare e usare l'autenticazione relativa alle app per API e dispositivi mobili nel servizio app di Azure." 
	services="app-service" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="tdykstra"/>

# Autenticazione per le app per API e dispositivi mobili nel servizio app di Azure

## Panoramica

Questo articolo illustra le funzionalità di autenticazione incorporate per le [app per API](../app-service-api/app-service-api-apps-why-best-platform.md) e le [app per dispositivi mobili](../app-service-mobile/app-service-mobile-value-prop-preview.md).

La sezione [Passaggi successivi](#next-steps) alla fine dell'articolo offre collegamenti alla documentazione sulle procedure correlate.

## Gateway del servizio app di Azure

Il servizio app di Azure offre servizi di autenticazione incorporati che implementano [OAuth 2.0](#oauth) e [OpenID Connect](#oauth) e funzionano con più *provider di identità*. Un provider di identità è un servizio esterno, considerato attendibile dal servizio app di Azure, che autentica gli utenti dell'app. Il servizio app supporta i provider di identità più diffusi:

* Azure Active Directory
* Account Microsoft
* Google
* Twitter
* Facebook

### Architettura del gateway

Tutti i gruppi di risorse di Azure che contengono app per API o dispositivi mobili includono un *gateway*. Il gateway è una risorsa di Azure eseguita in un'app Web, che gestisce attività amministrative tra cui l'autenticazione relativa alle app per API e dispositivi mobili nel gruppo di risorse.

Il gateway gestisce le procedure di accesso e i token e impedisce chiamate non autenticate alle app per le API, [configurate per richiedere l'accesso autenticato](../app-service-api/app-service-api-dotnet-add-authentication.md#protect-the-api-app). Il gateway è in grado di controllare l'accesso alle app per le API perché tutte le richieste HTTP in arrivo destinate a tali app nel gruppo di risorse vengono indirizzate attraverso il gateway.

Il diagramma seguente illustra queste funzioni del gateway.

![](./media/app-service-authentication-overview/gateway.png)

### Funzionalità del gateway

I servizi di autenticazione del gateway offrono diversi vantaggi rispetto all'esecuzione di un'implementazione autonoma di OAuth 2.0:

* Microsoft mette a disposizione SDK che consentono di eseguire attività di autenticazione e autorizzazione usando una sintassi semplificata.

* Poiché il servizio app gestisce ulteriori attività di autenticazione, il tempo di sviluppo e testing è ridotto e si evitano quasi tutte le ripercussioni causate da modifiche nelle implementazioni del provider di OAuth.

* Se sono presenti più app da proteggere collocate in un unico gruppo di risorse, è sufficiente un solo client ID e un segreto client per ogni provider di autenticazione, poiché esiste un solo URL di reindirizzamento per il gateway.

* Il monitoraggio e la risoluzione dei problemi sono più semplici, perché è possibile monitorare il traffico correlato all'autenticazione per un intero gruppo di risorse monitorando il gateway.

* Il debug è più semplice poiché è possibile configurare un programma che usi il gateway durante l'esecuzione locale in modalità di debug e non è necessario modificare gli URL di reindirizzamento nell'account del provider di identità.

## Flusso server e flusso client a confronto

Il gateway del servizio app offre due modalità di autenticazione dei client: *flusso client* e *flusso server*. In entrambi i flussi, l'applicazione client invia le credenziali dell'utente (normalmente, nome utente e password) direttamente al provider di identità. In entrambi i flussi, né il gateway né l'applicazione riceve le credenziali utente.

### Flusso client

Nel flusso client, l'applicazione client comunica direttamente con il provider di identità per ottenere il token di accesso del provider. L'applicazione client invia il token di accesso del provider al gateway. Il gateway crea e invia al client un token del contesto utente. Tale token è noto anche come token Zumo, dal nome del codice originale dei [servizi mobili di Azure](/documentation/services/mobile-services/) (i servizi di autenticazione per le app per API e dispositivi mobili sono basati sulla stessa architettura originariamente sviluppata per i servizi mobili).

Il diagramma seguente illustra questo flusso.

![](./media/app-service-authentication-overview/clientflow.png)

Il client mette quindi a disposizione il token Zumo nelle richieste HTTP quando chiama app per API o dispositivi mobili protette. Il gateway archivia i token del provider e tiene traccia di quelli associati ai token Zumo.

### Flusso server

Nel flusso server, l'applicazione client fa affidamento al gateway per comunicare con il provider di identità in modo da attivare l'accesso. Il browser client passa a un URL del gateway e il gateway reindirizza la richiesta alla pagina di accesso del provider di identità. Dopo l'accesso dell'utente, il gateway ottiene il token del provider di identità, crea il token Zumo e lo invia al client.

![](./media/app-service-authentication-overview/serverflow.png)

Le interazioni successive tra l'applicazione client e le app per API o dispositivi mobili protette vengono gestite come nel flusso client: il client mette a disposizione il token Zumo nelle richieste HTTP.

### Come scegliere tra flusso client e flusso server

Il flusso client è solitamente la scelta migliore se il provider di identità che si desidera usare dispone di un SDK per la piattaforma client che si desidera supportare. Il flusso client offre la migliore esperienza utente, perché riduce il numero di immissioni delle credenziali da parte dell'utente. Ad esempio, se l'utente dispone di un dispositivo Android, dispone probabilmente di un account Google associato al dispositivo, pertanto se può usare tale account senza dover immettere nuovamente il nome utente e la password, l'esperienza utente risulta migliorata. Lo stesso vale per un account Facebook in un dispositivo Android, iOS o Windows Phone o un account Microsoft in un desktop Windows o un Windows Phone.

In altri scenari il flusso server può rappresentare una scelta migliore:

- Non esiste alcun SDK client nativo per il provider di identità e la piattaforma client che si desidera supportare.

- Si desidera avviare rapidamente la produzione e migliorare l'esperienza utente in un secondo momento, tempo permettendo. Il flusso server consente al servizio app di Azure di eseguire la maggior parte del lavoro di autenticazione, riducendo i tempi di sviluppo e testing.

## Chiamate in uscita "on-behalf-of" a piattaforme SaaS

È possibile scrivere codice per eseguire chiamate in uscita a piattaforme Software-as-a-Service (SaaS) per conto dell'utente connesso oppure è possibile usare un'[app per le API del connettore](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md). Ad esempio, per pubblicare un tweet dall'account Twitter dell'utente è possibile usare [un SDK Twitter](https://dev.twitter.com/overview/api/twitter-libraries) o eseguire il provisioning di un [connettore Twitter](../app-service-mobile/app-service-logic-connector-twitter.md) nella sottoscrizione di Azure e chiamarlo. Questa sezione si occupa dell'accesso a una piattaforma SaaS dal codice eseguito in un'app per API o dispositivi mobili.

### <a id="obotoidprovider"></a> Uso del token del provider di identità 

Il gateway gestisce un *archivio token* in cui si associa un token Zumo a uno o più token di accesso e token di aggiornamento del provider di identità. Quando si riceve una richiesta HTTP con un token Zumo valido, il gateway sa quali token del provider di identità sono relativi a tale utente.
  
Quando il codice in esecuzione nell'app per API o dispositivi mobili deve eseguire una chiamata a una risorsa protetta per conto dell'utente connesso, può recuperare e usare il token del provider di identità dall'archivio di token del gateway, come illustrato nel diagramma seguente. Nel diagramma si presuppone che il client sia già stato autenticato con il gateway e disponga del token Zumo.

![](./media/app-service-authentication-overview/idprovidertoken.png)

Si supponga, ad esempio, che il provider di identità sia Azure Active Directory (AAD) e che l'app per le API desideri usare il token di accesso AAD per chiamare l'API Graph di AAD o richiedere l'accesso a un sito SharePoint per il quale l'utente dispone di autorizzazioni. È possibile inviare una richiesta al gateway per recuperare il token AAD, quindi usare il token AAD per chiamare l'API Graph o ottenere un token di accesso per il sito SharePoint.

### <a id="obotosaas"></a>Ottenere il consenso dell'utente per accedere ad altre risorse

Il gateway dispone anche di funzionalità incorporate per ottenere il consenso dell'utente quando si desidera accedere a risorse protette da un provider diverso dal provider di identità originale. Ad esempio, per un utente che accede mediante Azure Active Directory, è possibile accedere ai file nell'account Dropbox dell'utente.

Il gateway del servizio app include supporto incorporato per ottenere il consenso dell'utente per questo tipo di accesso dai provider seguenti:

* Box
* DropBox
* Facebook
* Google
* Office365
* OneDrive
* QuickBooks
* Salesforce
* SharePointOnline
* Twitter
* Yammer
* Azure Active Directory
* Account Microsoft

Per tali provider, il gateway conserva i token di accesso e li associa al token Zumo, come avviene per il token di accesso del provider di identità. Il diagramma seguente illustra il processo tramite il quale si ottiene il consenso dell'utente e la chiamata a una piattaforma SaaS. Nel diagramma si presuppone che il client sia già stato autenticato con il gateway e disponga del token Zumo.

![](./media/app-service-authentication-overview/saastoken.png)

Con il supporto di runtime e gli SDK del servizio app, è relativamente semplice scrivere codice che accede alle risorse protette da uno di questi provider. Un passaggio preliminare non è illustrato nel diagramma: è necessario impostare un account con il provider e configurare l'ID client del provider e le impostazioni del segreto client nel servizio app di Azure.

Per questi e molti altri provider è anche possibile accedere alle risorse protette mediante un'[app per le API del connettore](../app-service-mobile/app-service-logic-what-are-biztalk-api-apps.md) predefinita.

## Disponibilità degli SDK

Nel caso di app per le API, l'SDK per .NET offre funzionalità di autenticazione:

* [Microsoft.Azure.AppService](http://www.nuget.org/packages/Microsoft.Azure.AppService): da usare in un client di un'app per le API.  
* [Microsoft.Azure.AppService.ApiApps.Service](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/): da usare in un progetto API Web eseguito in un'app per le API.
 
Visual Studio può anche generare automaticamente codice che funziona con l'SDK per .NET, per semplificare ulteriormente il codice scritto per chiamare l'app per le API. Per altre informazioni, vedere [Usare un'app per le API nel servizio app di Azure da un client .NET](../app-service-api/app-service-api-dotnet-consume.md).

Nel caso di app per dispositivi mobili, sono disponibili SDK per le piattaforme seguenti:

- [.NET Server](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) 
- [iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-preview.md)
- [Xamarin iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md)
- [Xamarin Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-preview.md)
- [Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md)
- JavaScript (esercitazione in fase di sviluppo)

## Metodi di autenticazione alternativi

Se i servizi di autenticazione del gateway non soddisfano le esigenze dell'app, è possibile gestire l'autenticazione in proprio o usare il servizio Gestione API di Azure.

### <a id="doityourself"></a>Autenticazione gestita in proprio

È possibile eseguire un framework di autenticazione come [ASP.NET Identity](http://www.asp.net/identity) o [Thinktecture](http://www.thinktecture.com/identityAndAccessControl) in Azure. Questo consente di controllare il funzionamento complessivo, tuttavia richiede più tempo per lo sviluppo e il testing della funzionalità di autenticazione. Inoltre, se si dispone di più app da proteggere con più URL di reindirizzamento, è necessario configurare più ID client e segreti client con provider di autenticazione di terze parti, ad esempio Facebook, Google e Twitter.

Attualmente il servizio app non supporta l'uso di una soluzione gestita in proprio unitamente all'autenticazione gateway, come avviene in [Servizi mobili](mobile-services-dotnet-backend-get-started-custom-authentication.md).

### <a id="apim"></a>Gestione API di Azure

Se si dispone di API esistenti che si desidera proteggere con l'autenticazione, è possibile farlo con il servizio Gestione API di Azure. Per informazioni sull'uso di Gestione API con le app per le API, vedere il post seguente sul blog di Panos Kefalidis: [Sfruttare la Gestione API per le app per le API](http://www.kefalidis.me/2015/06/taking-advantage-of-api-management-for-api-apps/)

## Passaggi successivi

Questo articolo illustra i servizi di autenticazione offerti dal servizio app di Azure per le app per API e dispositivi mobili. Di seguito sono riportati alcuni collegamenti a risorse che offrono informazioni sui protocolli di autenticazione impiegati e documentazione su come usare le funzionalità di autenticazione del servizio app.

* [OAuth 2.0, OpenID Connect e JSON Web Tokens (JWT)](#oauth)
* Risorse delle app per le API
	* [Flusso client delle app per le API](#apiaclient)
	* [Flusso server delle app per le API](#apiaserver)
	* [Chiamate "on-behalf-of" delle app per le API](#apiaobo)
* Risorse delle app per dispositivi mobili
	* [Flusso client delle app per dispositivi mobili](#maclient)
	* [Flusso server delle app per dispositivi mobili](#maserver)
	* [Chiamate "on-behalf-of" delle app per dispositivi mobili](#maobo)

### <a id="oauth"></a>OAuth 2.0, OpenID Connect e JSON Web Tokens (JWT)

* [Introduzione a OAuth 2.0](http://shop.oreilly.com/product/0636920021810.do "Introduzione a OAuth 2.0") 
* [Introduzione a OAuth2, OpenID Connect e JSON Web Tokens (JWT) - corso PluralSight](http://www.pluralsight.com/courses/oauth2-json-web-tokens-openid-connect-introduction) 
* [Creazione e protezione di un'API RESTful per più client in ASP.NET - corso PluralSight](http://www.pluralsight.com/courses/building-securing-restful-api-aspdotnet)

### <a id="apiaclient"></a>Flusso client delle app per le API

* [Proteggere un'app per le API](../app-service-api/app-service-api-dotnet-add-authentication.md): la parte relativa alla configurazione dell'app per le API è valida sia per il flusso client sia per il flusso server, ma la parte relativa al test nel browser illustra il flusso server.
* [Utilizzare un'app per le API nel servizio app di Azure da un client .NET](../app-service-api/app-service-api-dotnet-consume.md): l'app di esempio per una chiamata autenticata illustra il flusso server, ma è seguita da una sezione di [flusso client](../app-service-api/app-service-api-dotnet-consume.md#client-flow) con il codice di esempio.

### <a id="apiaserver"></a>Flusso server delle app per le API

* [Proteggere un'app per le API](../app-service-api/app-service-api-dotnet-add-authentication.md): la parte relativa alla configurazione dell'app per le API è valida sia per il flusso client sia per il flusso server e la parte relativa al test nel browser illustra il flusso server.
* [Usare un'app per le API nel servizio app di Azure da un client .NET](../app-service-api/app-service-api-dotnet-consume.md): il codice di esempio per una chiamata autenticata illustra il flusso server. 

### <a id="apiaobo"></a>Chiamate "on-behalf-of" delle app per le API

* [Distribuire e configurare un'app per le API di un connettore SaaS nel servizio app di Azure](../app-service-api/app-service-api-connnect-your-app-to-saas-connector.md): illustra come eseguire il provisioning di un'app per le API del connettore predefinita, come configurarla e come chiamarla tramite gli strumenti del browser.
* [Connettersi a una piattaforma SaaS da un'app per le API ASP.NET nel servizio app di Azure](../app-service-api/app-service-api-dotnet-connect-to-saas.md): illustra come scrivere il connettore, ovvero come effettuare il provisioning, configurare e scrivere il codice per un'app per le API personalizzata che esegue chiamate "on-behalf-of" a un provider SaaS.

### <a id="maclient"></a>Flusso client delle app per dispositivi mobili

* [Aggiungere Single Sign-On di Azure Active Directory all'app iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-aad-sso-preview.md)

### <a id="maserver"></a>Flusso server delle app per dispositivi mobili

* [Aggiungere l'autenticazione all'app iOS](../app-service-mobile/app-service-mobile-dotnet-backend-ios-get-started-users-preview.md)
* [Aggiungere l'autenticazione all'app Xamarin.iOS](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-ios-get-started-users-preview.md)
* [Aggiungere l'autenticazione all'app Xamarin.Android](../app-service-mobile/app-service-mobile-dotnet-backend-xamarin-android-get-started-users-preview.md)
* [Aggiungere l'autenticazione all'app Windows](../app-service-mobile/app-service-mobile-dotnet-backend-windows-store-get-started-users-preview.md)

### <a id="maobo"></a>Chiamate "on-behalf-of" delle app per dispositivi mobili a risorse protette

* [Ottenere un token di accesso e chiamare l'API SharePoint in un'app per dispositivi mobili](../app-service-mobile/app-service-mobile-dotnet-backend-get-started-connect-to-enterprise.md#obtain-token)

<!---HONumber=July15_HO5-->