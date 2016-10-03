<properties
	pageTitle="Limitazioni e restrizioni dell'endpoint 2.0 | Microsoft Azure"
	description="Elenco di limitazioni e restrizioni relative all'endpoint 2.0 di Azure AD."
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
	ms.date="09/16/2016"
	ms.author="dastrock"/>  

# Perché usare l'endpoint 2.0

Nella compilazione di applicazioni che si integrano con Azure Active Directory è necessario stabilire se i protocolli di autenticazione e l'endpoint 2.0 rispondono ai requisiti specifici. Il modello di app Azure AD originale è ancora completamente supportato e, per alcuni aspetti, include più funzionalità della versione 2.0. L'endpoint 2.0, tuttavia, [introduce vantaggi importanti](active-directory-v2-compare.md) per gli sviluppatori che possono convincere a usare il nuovo modello di programmazione. Nel tempo la versione 2.0 crescerà fino a includere tutte le funzionalità di Azure AD e verrà usato solo l'endpoint 2.0.

Attualmente, l'endpoint 2.0 offre due funzionalità di base:

- Accesso degli utenti con account personali e di lavoro.
- Chiamata dell'[API Outlook convergente](https://dev.outlook.com).

Entrambe queste funzionalità possono essere implementate in applicazioni Web, per dispositivi mobili e per PC. Se queste funzionalità relativamente limitate sono adatte all'applicazione che si sta compilando, è consigliabile usare l'endpoint 2.0. Se l'applicazione richiede funzionalità aggiuntive dei servizi Microsoft, è consigliabile continuare a usare gli endpoint già collaudati di Azure AD e dell'account Microsoft. In futuro l'endpoint 2.0 includerà sia Azure AD che l'account Microsoft e verranno fornite le istruzioni necessarie per la transizione all'endpoint 2.0.

Nel frattempo, questo articolo permette di determinare se l'endpoint 2.0 risponde ai requisiti dell'applicazione. L'articolo verrà aggiornato periodicamente in base allo stato corrente dell'endpoint 2.0. Si consiglia di tornare a consultarlo di quando in quando per valutare le funzionalità della versione 2.0 rispetto ai requisiti specifici.

Se è disponibile un'app esistente con Azure AD che non usa l'endpoint 2.0, non è necessario iniziare da zero. In futuro verrà fornito un modo per abilitare le applicazioni Azure AD esistente all'uso con l'endpoint 2.0.

## Restrizioni relative alle app
I tipi di app riportati di seguito non sono attualmente supportati dall'endpoint 2.0. Per una descrizione dei tipi di app supportati, fare riferimento a [questo articolo](active-directory-v2-flows.md).

##### API Web autonome
L'endpoint 2.0 permette di [compilare un'API Web protetta con OAuth 2.0](active-directory-v2-flows.md#web-apis). Tuttavia, tale API Web potrà ricevere token unicamente da un'applicazione che condivide lo stesso ID applicazione. La compilazione di un'API Web accessibile da un client con un ID applicazione diverso non è supportata. Il client non potrà richiedere o ottenere autorizzazioni per l'API Web.

Per informazioni su come compilare un'API Web che accetti token da un client con lo stesso ID applicazione, vedere gli esempi di API Web dell'endpoint 2.0 nella sezione [Introduzione](active-directory-appmodel-v2-overview.md#getting-started).

##### App daemon e sul lato server
Anche le app che contengono processi a esecuzione prolungata o che funzionano senza la presenza di un utente necessitano di un modo per accedere alle risorse protette, ad esempio le API Web. Tali app possono autenticarsi e ottenere i token usando l'identità dell'app, anziché un'identità delegata dell'utente, mediante il flusso delle credenziali client di OAuth 2.0.

Questo flusso non è attualmente supportato dall'endpoint 2.0. Vale a dire che le app possono ottenere i token solo dopo che si è verificato un flusso di accesso utente interattivo. Il flusso delle credenziali client verrà aggiunto in futuro. Per informazioni sul flusso delle credenziali client che usa l'endpoint di Azure AD originale, vedere l'[esempio di daemon in GitHub](https://github.com/AzureADSamples/Daemon-DotNet).

##### Flusso On-Behalf-Of dell'API Web
Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette dall'endpoint 2.0. Questo scenario è comune nei client nativi che hanno un back-end dell'API Web, che a sua volta chiama un servizio online Microsoft o un'altra API Web personalizzata che supporta Azure AD.

Questo scenario può essere supportato usando la concessione delle credenziali di connessione JWT di OAuth 2.0, nota anche come flusso On-Behalf-Of. Tuttavia, il flusso On-Behalf-Of non è attualmente supportato per l'endpoint 2.0. Per verificare il funzionamento di questo flusso nel servizio Azure AD disponibile a livello generale, consultare l'[esempio di codice On-Behalf-Of su GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## Restrizioni relative alle registrazioni di app
Attualmente, per tutte le app da integrare con l'endpoint 2.0 è necessario creare una nuova registrazione in [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Le eventuali app Azure AD o dell'account Microsoft esistenti non sono compatibili con l'endpoint 2.0, come anche le app registrate in portali diversi dal nuovo portale di registrazione delle app. In futuro verrà fornito un modo per abilitare le applicazioni esistenti all'uso come app della versione 2.0, ma attualmente non è disponibile alcun percorso di migrazione per le app nell'endpoint 2.0.

Analogamente, le app registrate nel nuovo portale di registrazione delle app non funzionano con l'endpoint di autenticazione di Azure AD originale. Tuttavia, è possibile usare le app create nel portale di registrazione delle app per realizzare la piena integrazione con l'endpoint di autenticazione dell'account Microsoft, `https://login.live.com`.

Le app registrate nel nuovo portale di registrazione delle app sono attualmente limitate a un set ristretto di valori di URI di reindirizzamento. L'URI di reindirizzamento per app e servizi Web deve iniziare con lo schema o `https`, mentre l'URI di reindirizzamento per tutte le altre piattaforme deve usare il valore hardcoded `urn:ietf:oauth:2.0:oob`.

## Limitazioni relative agli URI di reindirizzamento
Per le app Web, tutti i valori degli URI di reindirizzamento devono condividere un singolo dominio DNS. Ad esempio, non è possibile registrare un'app Web con gli URI di reindirizzamento seguenti:

`https://login-east.contoso.com` `https://login-west.contoso.com`

Il sistema di registrazione confronta il nome DNS intero dell'URI di reindirizzamento esistente con il nome DNS dell'URI di reindirizzamento da aggiungere. Se il nome DNS intero del nuovo URI di reindirizzamento non corrisponde esattamente al nome DNS dell'URI di reindirizzamento esistente o non è un suo sottodominio, la richiesta di aggiunta avrà esito negativo. Ad esempio, se l'URI di reindirizzamento corrente dell'applicazione è:

`https://login.contoso.com`

è possibile aggiungere:

`https://login.contoso.com/new`

che corrisponde esattamente al nome DNS, oppure:

`https://new.login.contoso.com`

che è un sottodominio DNS di login.contoso.com. Per fare in modo che un'applicazione abbia login-east.contoso.com e login-west.contoso.com come URI di reindirizzamento, è necessario aggiungere questi URI di reindirizzamento nell'ordine seguente:

`https://contoso.com` `https://login-east.contoso.com` `https://login-west.contoso.com`

È possibile aggiungere gli ultimi due perché si tratta di sottodomini del primo URI di reindirizzamento, contoso.com. Questa limitazione verrà rimossa in una versione futura.

Per informazioni su come registrare un'app nel nuovo portale di registrazione delle app, fare riferimento a [questo articolo](active-directory-v2-app-registration.md).

## Restrizioni relative ai servizi e alle API
L'endpoint 2.0 attualmente supporta l'accesso di qualsiasi app registrata nel nuovo portale di registrazione delle app, a condizione che rientri nell'elenco dei [flussi di autenticazione supportati](active-directory-v2-flows.md). Tuttavia, queste app saranno in grado di acquisire solo token di accesso di OAuth 2.0 per un set molto limitato di risorse. L'endpoint 2.0 rilascerà token di accesso solo per:

- L'app che ha richiesto il token. Un'app può acquisire un token di accesso per se stessa, se l'app logica è costituita da diversi componenti o livelli. Per vedere questo scenario, consultare le esercitazioni nella sezione [introduttiva](active-directory-appmodel-v2-overview.md#getting-started).
- Le API REST di Posta, Calendario e Contatti di Outlook che si trovano in https://outlook.office.com. Per informazioni su come scrivere un'app che accede a queste API, fare riferimento alle esercitazioni nella sezione [introduttiva di Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).
- Le API Microsoft Graph. Per informazioni su Microsoft Graph e su tutti i dati disponibili, vedere [https://graph.microsoft.io](https://graph.microsoft.io).

Attualmente non sono supportati altri servizi. In futuro verranno aggiunti altri servizi online Microsoft nonché il supporto per le API Web e i servizi personalizzati.

## Restrizioni relative alle librerie e agli SDK
È disponibile una versione sperimentale di Active Directory Authentication Library compatibile con l'endpoint 2.0 e che permette di fare una prova. Tuttavia, questa versione di ADAL è in anteprima, non è supportata e subirà modifiche significative nei prossimi mesi. Per ottenere rapidamente un'app in esecuzione con l'endpoint 2.0, nella sezione [Introduzione](active-directory-appmodel-v2-overview.md#getting-started) sono disponibili esempi di codice che usano ADAL per .NET, iOS, Android e JavaScript.

Per usare l'endpoint 2.0 in un'applicazione di produzione, sono disponibili le opzioni seguenti:

- Se si compila un'applicazione Web, è possibile usare il middleware lato server disponibile a livello generale per eseguire operazioni di accesso e convalida dei token. È incluso il middleware OpenID Connect OWIN per ASP.NET e il plug-in di Passport per NodeJS. Nella sezione [Introduzione](active-directory-appmodel-v2-overview.md#getting-started) sono disponibili anche esempi di codice che usano tale middleware.
- Per altre piattaforme e per le applicazioni native e per dispositivi mobili, è possibile realizzare l'integrazione con l'endpoint 2.0 anche con l'invio e la ricezione diretta di messaggi di protocollo nel codice dell'applicazione. I protocolli OAuth e OpenID Connect della versione 2.0 [sono stati documentati in modo esplicito](active-directory-v2-protocols.md) per consentire tale integrazione.
- Per l'integrazione con l'endpoint 2.0 è anche possibile usare librerie OpenID Connect e OAuth open source. Il protocollo della versione 2.0 dovrebbe essere compatibile con molte librerie di protocollo open source senza modifiche rilevanti. La disponibilità di tali librerie varia in base alla piattaforma e al linguaggio. Nei siti Web di [OpenID Connect](http://openid.net/connect/) e [OAuth 2.0](http://oauth.net/2/) sono disponibili elenchi delle implementazioni più diffuse. Di seguito sono elencate le librerie client open source e gli esempi che sono stati testati con l'endpoint 2.0.

  - [Java WSO2 Identity Server](https://docs.wso2.com/display/IS500/Introducing+the+Identity+Server)
  - [Java Gluu Federation](https://github.com/GluuFederation/oxAuth)
  - [Node.Js passport-openidconnect](https://www.npmjs.com/package/passport-openidconnect)
  - [PHP OpenID Connect Basic Client](https://github.com/jumbojett/OpenID-Connect-PHP)
  - [Libreria client OAuth2 per iOS](https://github.com/nxtbgthng/OAuth2Client)
  - [Libreria client OAuth2 per Android](https://github.com/wuman/android-oauth-client)
  - [Client OpenID Connect per Android](https://github.com/kalemontes/OIDCAndroidLib)

## Restrizioni relative ai protocolli
L'endpoint 2.0 supporta unicamente i protocolli OpenID Connect e OAuth 2.0. Tuttavia, non tutte le funzionalità e caratteristiche dei vari protocolli sono state incorporate nell'endpoint 2.0. Di seguito sono riportati alcuni esempi:

- Endpoint `end_sesssion_endpoint` di OpenID Connect
- Concessione delle credenziali client OAuth 2.0

Per comprendere meglio l'ambito della funzionalità del protocollo supportata nell'endpoint 2.0, vedere il [riferimento ai protocolli OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

## Funzionalità avanzate di Azure AD per sviluppatori
Nel servizio Azure Active Directory è disponibile un set di funzionalità per sviluppatori non ancora supportate per l'endpoint 2.0, tra cui:

- Attestazioni di gruppo per utenti di Azure AD
- Ruoli applicazione e attestazioni basate sui ruoli

<!---HONumber=AcomDC_0921_2016-->