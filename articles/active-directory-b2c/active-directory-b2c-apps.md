<properties
	pageTitle="Anteprima di Azure AD B2C | Microsoft Azure"
	description="Tipi di applicazioni che è possibile creare nell'anteprima di Azure AD B2C."
	services="active-directory-b2c"
	documentationCenter=""
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/28/2015"
	ms.author="dastrock"/>

# Anteprima di Azure AD B2C: Tipi di applicazioni

Azure AD B2C supporta l'autenticazione di un'ampia gamma di architetture di app moderne, basate sui protocolli standard del settore [OAuth 2.0](active-directory-b2c-reference-protocols.md) e/o [OpenID Connect](active-directory-b2c-reference-protocols.md). Questo documento descrive brevemente i tipi di app che è possibile creare, indipendente dal linguaggio o dalla piattaforma preferita. Il documento consentirà di comprendere gli scenari di alto livello prima di [passare direttamente al codice](active-directory-b2c-overview.md#getting-started).

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

## Nozioni di base
Ogni applicazione che usa Azure AD B2C dovrà essere registrata nella [directory B2C](active-directory-b2c-get-started.md) tramite il [portale di anteprima di Azure](https://portal.azure.com). Il processo di registrazione raccoglie e assegna all'app alcuni valori:

- Un **ID applicazione** che identifica l'app in modo univoco
- Un **URI di reindirizzamento** che può essere usato per indirizzare le risposte all'app
- Altri valori specifici dello scenario Per informazioni dettagliate, consultare l'argomento relativo alla [registrazione di un'app](active-directory-b2c-app-registration.md).

Dopo la registrazione, l'app comunica con Azure AD inviando richieste all'endpoint Azure AD 2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Ogni richiesta inviata ad Azure AD B2C specifica dei **criteri** che controllano il comportamento di Azure AD e consentono di usare questi endpoint per creare un set personalizzabile di esperienze utente. Tra i criteri comuni sono inclusi quelli per l'iscrizione, l'accesso e la modifica del profilo. Se non si ha familiarità con i criteri, si consiglia di leggere l'argomento relativo al [framework di criteri estendibile](active-directory-b2c-reference-policies.md) di Azure AD B2C prima di proseguire.

Ogni interazione dell'app con l'endpoint 2.0 seguirà un modello di livello elevato simile al seguente:

1. L'app indirizza l'utente finale all'endpoint 2.0 per l'esecuzione dei [criteri](active-directory-b2c-reference-policies.md).
2. L'utente completa i criteri in base alla relativa definizione.
4. L'app riceve un token di sicurezza dall'endpoint 2.0.
5. L'app usa il token di sicurezza per accedere a informazioni protette o a una risorsa.
6. Il server di risorse convalida il token di sicurezza per garantire che l'accesso può essere concesso.
7. L'app aggiorna periodicamente il token di sicurezza.

<!-- TODO: Need a page for libraries to link to -->
Ognuno di questi passaggi varia leggermente in base al tipo di app che si sta creando e sono disponibili librerie open source che gestiscono i dettagli per conto dell'utente.

## App Web
Per le app Web (.NET, PHP, Java, Ruby, Python, Node e così via) ospitate in un server e accessibili tramite un browser, Azure AD B2C supporta [OpenID Connect](active-directory-b2c-reference-protocols.md) per tutte le esperienze utente, inclusi l'accesso, l'iscrizione e la gestione del profilo. Nell'implementazione di OpenID Connect in Azure AD B2C l'app Web avvia queste esperienze utente generando richieste di autenticazione ad Azure AD. Il risultato della richiesta è `id_token`, ossia un token di sicurezza che rappresenta l'identità dell'utente e fornisce informazioni sull'utente sotto forma di attestazioni:

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

Per informazioni su tutti i tipi di token e attestazioni disponibili per un'app, consultare il [riferimento al token B2C](active-directory-b2c-reference-tokens.md).

Nelle app Web ogni esecuzione di [criteri](active-directory-b2c-reference-policies.md) consiste nei passaggi di alto livello seguenti:

![Immagine di corsie di app Web](./media/active-directory-b2c-apps/webapp.png)

La convalida del token id\_token mediante una chiave di firma pubblica ricevuta da Azure AD è sufficiente per verificare l'identità dell'utente e impostare un cookie di sessione che può essere usato per identificare l'utente nelle richieste di pagina successive.

Per visualizzare questo scenario, provare uno degli esempi di codice di accesso dell'app Web nella sezione [introduttiva](active-directory-b2c-overview.md#getting-started).

Oltre al semplice accesso, un'app del server Web può anche avere bisogno di accedere a un servizio Web back-end. In questo caso l'app Web può seguire un [flusso di OpenID Connect](active-directory-b2c-reference-oidc.md) leggermente diverso e acquisire token tramite codici di autorizzazione e token di aggiornamento. Questo scenario è illustrato di seguito nella [sezione API Web](#web-apis).

<!--, and in our [WebApp-WebAPI Getting Started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## API Web
È possibile usare Azure AD B2C anche per proteggere i servizi Web, ad esempio le API Web RESTful dell'app. Le API Web possono usare OAuth 2.0 per proteggere i propri dati e autenticare le richieste HTTP in ingresso tramite token. Il chiamante di un'API Web aggiunge un token di accesso nell'intestazione dell'autorizzazione di una richiesta HTTP:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

L'API Web può quindi usare il token per verificare l'identità del chiamante dell'API ed estrarre informazioni su quest'ultimo dalle attestazioni codificate nel token. Per informazioni su tutti i tipi di token e attestazioni disponibili per un'app, consultare il [riferimento al token di Azure AD B2C](active-directory-b2c-reference-tokens.md).

> [AZURE.NOTE]L'anteprima di Azure AD B2C supporta attualmente solo le API Web che sono accessibili ai propri client noti. Ad esempio, l'app completa può includere un'app per iOS, un'app per Android e un'API Web di back-end. Questa architettura è completamente supportata. Non è invece supportato l'accesso alla stessa API Web da parte di client di terze parti, ad esempio un'altra app per iOS. In effetti, tutti i componenti dell'app completa devono condividere un singolo ID applicazione.

Un'API Web può ricevere token da tutti i tipi di client, tra cui app Web, desktop, per dispositivi mobili, a pagina singola, daemon sul lato server e anche altre API Web. A titolo di esempio, è possibile esaminare l'intero flusso di un'app Web che chiama un'API Web.

![Immagine di corsie di API Web di app Web](./media/active-directory-b2c-apps/webapi.png)

Per altre informazioni su codici di autorizzazione, token di aggiornamento e procedure dettagliate di recupero dei token, consultare l'articolo relativo al [protocollo OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

Per informazioni su come proteggere un'API Web con Azure AD B2C, consultare le esercitazioni relative alle API Web nella [sezione della Guida introduttiva](active-directory-b2c-overview.md#getting-started).
	
## App per dispositivi mobili e native
Le app installate in un dispositivo, ad esempio le app desktop e per dispositivi mobili, devono spesso accedere a servizi back-end o ad API Web per conto dell'utente. È possibile aggiungere esperienze personalizzate di gestione delle identità alle app native ed eseguire chiamate sicure ai servizi back-end usando Azure AD B2C e il [flusso del codice di autorizzazione OAuth 2.0](active-directory-b2c-reference-oauth-code.md).

In questo flusso l'app esegue [criteri](active-directory-b2c-reference-policies.md) e riceve un codice di autorizzazione da Azure AD dopo che l'utente ha completato i criteri. Questo codice rappresenta l'autorizzazione dell'app di chiamare servizi back-end per conto dell'utente che ha eseguito l'accesso. L'app può quindi scambiare il codice di autorizzazione in background con un token ID e un token di aggiornamento. L'app può usare il token ID per eseguire l'autenticazione a un'API Web di back-end e il token di aggiornamento per ottenere nuovi token ID quando i precedenti scadono.

> [AZURE.NOTE]L'anteprima di Azure AD B2C attualmente supporta solo l'acquisizione di token ID usati per accedere al servizio Web di back-end di un'app. Ad esempio, l'app completa può includere un'app per iOS, un'app per Android e un'API Web di back-end. Questa architettura è completamente supportata. Non è invece supportato l'accesso di un'app per iOS a un'API Web di terze parti tramite token di accesso OAuth 2.0. In effetti, tutti i componenti dell'app completa devono condividere un singolo ID applicazione.

![Immagine di corsie di app native](./media/active-directory-b2c-apps/native.png)

## Limitazioni correnti della versione di anteprima
Questi tipi di app non sono attualmente supportati dall'anteprima di Azure AD B2C, ma lo saranno per la fase di disponibilità generale. Ulteriori limitazioni e restrizioni per l'anteprima di Azure AD B2C sono descritte nell'articolo relativo alle [limitazioni](active-directory-b2c-limitations.md).

### App a pagina singola (JavaScript)
Molte app moderne dispongono di un front-end dell'app a pagina singola scritto principalmente in JavaScript e spesso tramite framework di app a pagina singola, ad esempio AngularJS, Ember.js, Durandal e così via. Il servizio Azure AD disponibile a livello generale supporta queste app mediante il flusso implicito di OAuth 2.0. Questo flusso non è tuttavia ancora disponibile in Azure AD B2C. Sarà disponibile a breve.

### App daemon e sul lato server
Anche le app che contengono processi a esecuzione prolungata o che funzionano senza la presenza di un utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web. Tali app possono autenticarsi e ottenere i token usando l'identità dell'app, anziché un'identità delegata dell'utente, mediante il flusso delle credenziali client di OAuth 2.0.

Questo flusso non è attualmente supportato da Azure AD B2C, ossia le app possono ottenere i token solo dopo che si è verificato un flusso utente interattivo. Il flusso delle credenziali client verrà aggiunto in futuro.

### Catene di API Web (On-Behalf-Of)
Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette da Azure AD B2C. Questo scenario è comune nei client nativi che dispongono di un back-end dell'API Web, che a sua volta chiama un servizio Microsoft Online come l'API Graph di Azure AD.

Questo scenario dell'API Web concatenata può essere supportato tramite la concessione delle credenziali di connessione JWT di OAuth 2.0, nota anche come flusso On-Behalf-Of. Tuttavia, il flusso On-Behalf-Of non è attualmente implementato nell'anteprima di Azure AD B2C.

<!---HONumber=Oct15_HO3-->