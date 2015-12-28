<properties
	pageTitle="Tipi di applicazioni Modello App v 2.0 | Microsoft Azure"
	description="Tipi di app e scenari supportati dall'anteprima pubblica di Modello app 2.0 di Azure AD."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/09/2015"
	ms.author="dastrock"/>

# Anteprima di Modello app 2.0: Tipi di app
Modello app 2.0 supporta l'autenticazione di un'ampia gamma di architetture di app moderne, basate sui protocolli standard del settore [OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow) e/o [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). Questo documento descrive brevemente i tipi di app che è possibile creare, indipendente dal linguaggio o dalla piattaforma preferita. Il documento consentirà di comprendere gli scenari di alto livello prima di [passare direttamente al codice](active-directory-appmodel-v2-overview.md#getting-started).

> [AZURE.NOTE]Queste informazioni fanno riferimento all'anteprima pubblica di Modello app 2.0. Per istruzioni su come eseguire l'integrazione con il servizio Azure AD disponibile a livello generale, consultare la [Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md).

## Nozioni di base
Ogni app che usa Modello app 2.0 dovrà essere registrata in [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Il processo di registrazione raccoglie e assegna all'app alcuni valori:

- Un **ID applicazione** che identifica l'app in modo univoco
- Un **URI di reindirizzamento** che può essere usato per indirizzare le risposte all'app
- Altri valori specifici dello scenario Per informazioni dettagliate, consultare l'argomento relativo alla [registrazione di un'app](active-directory-v2-app-registration.md).

Dopo la registrazione, l'app comunica con Azure AD inviando richieste all'endpoint 2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Ogni interazione dell'app con l'endpoint 2.0 seguirà un modello di livello elevato simile al seguente:

1. L'app indirizza l'utente finale all'endpoint 2.0 per l'accesso.
2. L'utente esegue l'autenticazione immettendo nome utente e password o altre credenziali di accesso.
3. L'utente autorizza l'app ad agire per suo conto, concedendo le autorizzazioni richieste dall'app.
4. L'app riceve un token di sicurezza dall'endpoint 2.0.
5. L'app usa il token di sicurezza per accedere a informazioni protette o a una risorsa.
6. Il server di risorse convalida il token di sicurezza per garantire che l'accesso può essere concesso.
7. L'app aggiorna periodicamente il token di sicurezza.

<!-- TODO: Need a page for libraries to link to -->
Ognuno di questi sette passaggi varia leggermente in base al tipo di app che si sta creando e sono disponibili librerie open source che gestiscono i dettagli per conto dell'utente. Per altre informazioni o esempi concreti, leggere l'argomento relativo ad [autorizzazioni, consenso e ambiti](active-directory-v2-scopes.md).

## App Web
Per le app Web (.NET, PHP, Java, Ruby, Python, Node e così via) accessibili tramite un browser, Modello app 2.0 supporta l'accesso utente mediante [OpenID Connect](active-directory-v2-protocols.md#openid-connect-sign-in-flow). In OpenID Connect l'app Web riceve un token `id_token`, ossia un token di sicurezza che verifica l'identità dell'utente e fornisce informazioni sull'utente sotto forma di attestazioni:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
	"name": "John Smith",
	"email": "john.smith@gmail.com",
	"oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
	...
}
```

Per informazioni su tutti i tipi di token e attestazioni disponibili per un'app, consultare il [riferimento al token 2.0](active-directory-v2-tokens.md).

Nelle app del server Web il flusso di autenticazione dell'accesso esegue i passaggi di alto livello seguenti:

![Immagine di corsie di app Web](../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

La convalida del token ID mediante una chiave di firma pubblica ricevuta dall'endpoint 2.0 è sufficiente per verificare l'identità dell'utente e impostare un cookie di sessione che può essere usato per identificare l'utente nelle richieste della pagina successiva.

Per visualizzare questo scenario, provare uno degli esempi di codice di accesso dell'app Web nella sezione [introduttiva](active-directory-appmodel-v2-overview.md#getting-started).

Oltre al semplice accesso, un'app del server Web può accedere ad altri servizi Web, ad esempio a un'API REST. In questo caso l'app del server Web può agire in un flusso di OpenID Connect e OAuth 2.0, mediante il [flusso del codice di autorizzazione OAuth 2.0](active-directory-v2-protocols.md#oauth2-authorization-code-flow). Questo scenario è illustrato più avanti nella sezione [API Web](#web-apis) e nell'argomento di [introduzione all'API Web dell'app Web](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## API Web
È possibile usare Modello app 2.0 anche per proteggere i servizi Web, ad esempio le API Web RESTful dell'app. Anziché i token ID e i cookie di sessione le API Web usano i token di accesso di OAuth 2.0 per proteggere i dati e autenticare le richieste in ingresso. Il chiamante di un'API Web aggiunge un token di accesso nell'intestazione dell'autorizzazione di una richiesta HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L'API Web può quindi usare il token di accesso per verificare l'identità del chiamante dell'API ed estrarre informazioni su quest'ultimo dalle attestazioni codificate nel token di accesso. Per informazioni su tutti i tipi di token e attestazioni disponibili per un'app, consultare il [riferimento al token 2.0](active-directory-v2-tokens.md).

Un'API Web può consentire agli utenti di fornire il consenso o rifiutare esplicitamente determinati dati o funzionalità esponendo le autorizzazioni, note anche come [ambiti](active-directory-v2-scopes.md). Affinché un'app chiamante acquisisca l'autorizzazione ad accedere a un ambito, l'utente deve fornire il consenso all'ambito durante un flusso. L'endpoint 2.0 si occuperà di chiedere l'autorizzazione all'utente e di registrare le autorizzazioni nei token di accesso ricevuti dall'API Web. L'API Web deve occuparsi semplicemente di convalidare i token di accesso ricevuti ad ogni chiamata e di eseguire i controlli dell'autorizzazione appropriati.

Un'API Web può ricevere token di accesso da tutti i tipi di app, tra cui app del server Web, desktop, per dispositivi mobili, a pagina singola, daemon sul lato server e anche altre API Web. A titolo di esempio, è possibile esaminare l'intero flusso di un'app del server Web che chiama un'API Web.

![Immagine di corsie di API Web di app Web](../media/active-directory-v2-flows/convergence_scenarios_webapp_webapi.png)

Per altre informazioni su codici di autorizzazione, token di aggiornamento e procedure dettagliate di recupero dei token di accesso, consultare il [protocollo OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

Per informazioni su come proteggere un'API Web con Modello app 2.0 e i token di accesso di OAuth 2.0, consultare gli esempi di codice dell'API Web nella sezione [introduttiva](active-directory-appmodel-v2-overview.md#getting-started).


## App per dispositivi mobili e native
Le app installate in un dispositivo, ad esempio app desktop e per dispositivi mobili, devono spesso accedere a servizi back-end o ad API Web che archiviano i dati ed eseguono diverse funzioni per conto dell'utente. Queste app possono aggiungere accesso e autorizzazioni ai servizi back-end mediante Modello app 2.0 e il [flusso del codice di autorizzazione OAuth 2.0](active-directory-v2-protocols-oauth-code.md).

In questo flusso, un'app riceve un codice di autorizzazione dall'endpoint 2.0 all'accesso dell'utente. Il codice rappresenta l'autorizzazione dell'app a chiamare i servizi back-end per conto dell'utente che ha eseguito l'accesso. L'app può quindi scambiare il codice di autorizzazione in background con un token di accesso di OAuth 2.0 e un token di aggiornamento. L'app può usare il token di accesso per l'autenticazione all'API Web nelle richieste HTTP e il token di aggiornamento per ottenere ulteriori token di accesso quando i precedenti scadono.

![Immagine di corsie di app native](../media/active-directory-v2-flows/convergence_scenarios_native.png)

## App a pagina singola (JavaScript)
Molte app moderne dispongono di un front-end dell'app a pagina singola scritto principalmente in JavaScript e spesso tramite framework di app a pagina singola, ad esempio AngularJS, Ember.js, Durandal e così via. Il modello app di Azure AD 2.0 supporta queste applicazioni tramite il [flusso implicito di OAuth 2.0](active-directory-v2-protocols-implicit.md).

In questo flusso, l'app riceve i token direttamente dalla versione 2.0 dell’endpoint di autorizzazione, senza eseguire scambi tra server di back-end e server. In questo modo tutta la logica di autenticazione e gestione della sessione avviene interamente nel client javascript, senza eseguire reindirizzamenti a pagina aggiuntive.

Per visualizzare questo scenario, provare uno degli esempi di codice di applicazione a pagina singola nella sezione [introduttiva](active-directory-appmodel-v2-overview.md#getting-started).

## Limitazioni correnti della versione di anteprima
Questi tipi di app non sono attualmente supportati dall'anteprima di Modello app 2.0, ma lo saranno per la fase di disponibilità generale. Ulteriori limitazioni e restrizioni per l'anteprima pubblica di Modello app 2.0 sono descritte nell'articolo relativo alle [limitazioni dell'anteprima 2.0](active-directory-v2-limitations.md).

### App daemon e sul lato server
Anche le app che contengono processi a esecuzione prolungata o che funzionano senza la presenza di un utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web. Tali app possono autenticarsi e ottenere i token usando l'identità dell'app, anziché un'identità delegata dell'utente, mediante il [flusso delle credenziali client di OAuth 2.0](active-directory-v2-protocols.md#oauth2-client-credentials-grant-flow).

Questo flusso non è attualmente supportato da Modello app 2.0, ossia le app possono ottenere i token solo dopo che si è verificato un flusso di accesso utente interattivo. Il flusso delle credenziali client verrà aggiunto in futuro. Se si desidera vedere il flusso delle credenziali client nel servizio Azure AD disponibile a livello generale, consultare l'[esempio Daemon su GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

### API Web concatenate (On-Behalf-Of)
Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette da Modello app 2.0. Questo scenario è comune nei client nativi che dispongono di un back-end dell'API Web, che a sua volta chiama un servizio Microsoft Online come Office 365 o l'API Graph.

Questo scenario dell'API Web concatenata può essere supportato tramite la concessione delle credenziali di connessione JWT di OAuth 2.0, nota anche come [flusso On-Behalf-Of](active-directory-v2-protocols.md#oauth2-on-behalf-of-flow). Tuttavia, il flusso On-Behalf-Of non è attualmente implementato nell'anteprima di Modello app 2.0. Per verificare il funzionamento di questo flusso nel servizio Azure AD disponibile a livello generale, consultare l'[esempio di codice On-Behalf-Of su GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

<!---HONumber=AcomDC_1217_2015-->