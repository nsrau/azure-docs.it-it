<properties
	pageTitle="Anteprima di AD B2C di Azure | Microsoft Azure"
	description="Come creare app direttamente mediante i protocolli supportati dall'anteprima AD B2C di Azure."
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
	ms.date="09/22/2015"
	ms.author="dastrock"/>

# Anteprima AD B2C Azure: Protocolli di autenticazione

AD B2C di Azure fornisce l'identità come servizio per le app grazie al supporto di due protocolli standard del settore, OpenID Connect e OAuth 2.0. Anche se il servizio è conforme agli standard, possono esistere sottili differenze tra le implementazioni di questi protocolli. Le informazioni in questo argomento sono utili se si sceglie di scrivere codice inviando e gestendo direttamente le richieste HTTP, anziché usando una delle librerie open source. Si consiglia di leggere le brevi informazioni in questa pagina prima di entrare nel dettaglio di ciascun protocollo specifico, ma se si ha già familiarità con AD B2C di Azure è possibile passare direttamente alle [guide di riferimento del protocollo](#protocols).

<!-- TODO: Need link to libraries above -->

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]
	
## Nozioni di base
Ogni applicazione che utilizza AD B2C di Azure dovrà essere registrata nella directory B2C nel [portale di Azure](https://portal.azure.com). Il processo di registrazione raccoglie e assegna all'app alcuni valori:

- Un **ID applicazione** che identifica l'app in modo univoco
- Un **URI di reindirizzamento** o un **identificatore di pacchetto** che possono essere usati per indirizzare le risposte all'app
- Altri valori specifici dello scenario Per informazioni dettagliate, consultare l'argomento relativo alla [registrazione di un'app](active-directory-b2c-app-registration.md).

Dopo la registrazione, l'app comunica con Azure AD inviando richieste all'endpoint v2.0:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

In quasi tutti i flussi di OAuth e OpenID Connect, sono coinvolte nello scambio quattro parti:

![Ruoli di OAuth 2.0](./media/active-directory-b2c-reference-protocols/protocols_roles.png)

- Il **server di autorizzazione** è l'endpoint v2.0 di Azure AD. Garantisce l'identità dell'utente, concede e revoca l'accesso alle risorse e rilascia i token. Agisce anche come provider di identità: gestisce in modo sicuro le informazioni dell'utente, l'accesso e le relazioni di trust tra le parti di un flusso.
- Il **proprietario della risorsa** è in genere l'utente finale. È l'entità che possiede i dati e consente a terze parti di accedere a tali dati o risorse.
- Il **client OAuth** è l'app, identificata dall'ID applicazione. In genere è l'entità con cui interagisce l'utente finale e che richiede i token dal server di autorizzazione. Per accedere alla risorsa, il client deve ottenere l'autorizzazione da parte del proprietario della risorsa.
- Il **server della risorsa** è la posizione in cui si trova la risorsa o i dati. Consente al server di autorizzazione di autenticare e autorizzare il client OAuth in modo sicuro e usa i token di accesso Bearer per assicurarsi che sia garantito l'accesso a una risorsa.

## Criteri

Indubbiamente, i **criteri** di AD B2C di Azure sono la caratteristica più importante del servizio. AD B2C di Azure estende i protocolli standard OAuth 2.0 e OpenID Connect con l'introduzione di criteri, che consentono a AD B2C di Azure di eseguire molto di più della semplice autenticazione o autorizzazione. I criteri descrivono completamente le esperienze di identità utente, ad esempio iscrizione, accesso o modifica del profilo. Possono essere definiti in un'interfaccia utente amministrativo ed eseguiti utilizzando un parametro di query speciale nelle richieste di autenticazione HTTP. I criteri non sono una funzionalità standard di OAuth 2.0 e OpenID Connect, pertanto è consigliabile comprenderli. Per ulteriori informazioni, leggere la [Guida di riferimento dei criteri AD B2C di Azure](active-directory-b2c-reference-policies.md).


## Tokens
L'implementazione AD B2C di Azure di OAuth 2.0 e OpenID Connect fa un uso intensivo dei token di connessione, inclusi quelli rappresentati come JWT (Token Web JSON). Un token di connessione è un token di sicurezza leggero che consente al "portatore" di accedere a una risorsa protetta. In questo senso, per "portatore" si intende qualsiasi parte che sia in grado di presentare il token. Anche se il rilascio del token di connessione è condizionato dal completamento del processo di autenticazione in Azure AD, se non vengono adottate le misure necessarie per proteggere il token durante la trasmissione e l'archiviazione, è possibile che venga intercettato e usato da parti non autorizzate. Molti token di sicurezza hanno meccanismi integrati per prevenire l'uso non autorizzato, ma i token di connessione ne sono sprovvisti e devono essere trasportati su un canale protetto, ad esempio Transport Layer Security (HTTPS). Se un token di connessione viene trasmesso senza essere protetto, un utente malintenzionato potrebbe usare un attacco "man in the middle" per acquisire il token e usarlo per l'accesso non autorizzato a una risorsa protetta. Gli stessi principi di sicurezza si applicano quando un token di connessione viene archiviato o memorizzato nella cache per un uso futuro. Assicurarsi sempre che l'app trasmetta e archivi i token di connessione in modo sicuro. Per altre considerazioni sulla sicurezza dei token di connessione, vedere la [sezione 5 della specifica RFC 6750](http://tools.ietf.org/html/rfc6750).

Ulteriori dettagli sui diversi tipi di token utilizzati in AD B2C Azure sono disponibile nel [riferimento al token di Azure AD](active-directory-b2c-reference-tokens.md).

## Protocolli

Se si è pronti vedere alcuni esempi di richieste, iniziare con una delle esercitazioni di seguito. Ognuna corrisponde a uno scenario di autenticazione specifico. Se è necessario determinare quale sia il flusso di destra per l’utente, consultare [i tipi di app che è possibile compilare con AD B2C di Azure](active-directory-b2c-apps.md).

- [Creazione di un’applicazione Mobile e Nativa con OAuth 2.0](active-directory-b2c-reference-oauth-code.md)
- [Creazione di Web App con collegamento ID Open](active-directory-b2c-reference-oidc.md)
- Creazione di app a pagina singola con OAuth 2.0 flusso implicito (presto disponibile)
- Compilare daemon o processi sul lato Server con il OAuth 2.0 Client flusso di credenziali (presto disponibile)
- Ottenere i token utilizzando un nome utente e password con la OAuth 2.0 risorse del proprietario della password flusso di credenziali (presto disponibile)
- Ottenere i token in un'API Web con OAuth 2.0 per conto del flusso (presto disponibile)

<!-- [Call the Azure AD Graph API using the OAuth 2.0 Client Credentials Flow](active-directory-reference-graph.md) -->

<!---HONumber=Oct15_HO1-->