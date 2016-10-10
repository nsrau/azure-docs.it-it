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
	ms.date="09/27/2016"
	ms.author="dastrock"/>

# Perché usare l'endpoint 2.0

Nella compilazione di applicazioni che si integrano con Azure Active Directory è necessario stabilire se l'endpoint 2.0 e i protocolli di autenticazione rispondono ai requisiti specifici. L'endpoint di Azure AD originale è ancora completamente supportato e, per alcuni aspetti, include più funzionalità della versione 2.0. L'endpoint 2.0, tuttavia, [introduce vantaggi importanti](active-directory-v2-compare.md) per gli sviluppatori che possono convincere a usare il nuovo modello di programmazione.

In questo momento, consigliamo quanto segue sull'uso dell'endpoint 2.0:

- Se si desidera supportare account Microsoft personali nell'applicazione, usare l'endpoint 2.0. Tuttavia, assicurarsi di comprendere le restrizioni elencate in questo articolo, specialmente quelle che riguardano gli account aziendali e dell'istituto di istruzione.
- Se l'applicazione richiede solo il supporto per account aziendali e dell'istituto di istruzione, è necessario usare [gli endpoint originali di Azure AD](active-directory-developers-guide.md).

Nel corso del tempo, l'endpoint 2.0 verrà migliorato per eliminare le restrizioni elencate di seguito, in modo da consentire l'uso esclusivo dell'endpoint 2.0. Nel frattempo, questo articolo permette di determinare se l'endpoint 2.0 risponde ai requisiti dell'applicazione. L'articolo verrà aggiornato periodicamente in base allo stato corrente dell'endpoint 2.0. Si consiglia di tornare a consultarlo di quando in quando per valutare le funzionalità della versione 2.0 rispetto ai requisiti specifici.

Se è disponibile un'app esistente con Azure AD che non usa l'endpoint 2.0, non è necessario iniziare da zero. In futuro verrà fornito un modo per abilitare le applicazioni Azure AD esistente all'uso con l'endpoint 2.0.

## Restrizioni relative alle app
I tipi di app riportati di seguito non sono attualmente supportati dall'endpoint 2.0. Per una descrizione dei tipi di app supportati, fare riferimento a [questo articolo](active-directory-v2-flows.md).

##### API Web autonome
L'endpoint 2.0 permette di [compilare un'API Web protetta con OAuth 2.0](active-directory-v2-flows.md#web-apis). Tuttavia, tale API Web potrà ricevere token unicamente da un'applicazione che condivide lo stesso ID applicazione. La compilazione di un'API Web accessibile da un client con un ID applicazione diverso non è supportata. Il client non potrà richiedere o ottenere autorizzazioni per l'API Web.

Per informazioni su come compilare un'API Web che accetti token da un client con lo stesso ID applicazione, vedere gli esempi di API Web dell'endpoint 2.0 in [Introduzione](active-directory-appmodel-v2-overview.md#getting-started).

##### Flusso On-Behalf-Of dell'API Web
Molte architetture includono un'API Web che deve chiamare un'altra API Web downstream, entrambe protette dall'endpoint v2.0. Questo scenario è comune nei client nativi che hanno un back-end dell'API Web, che a sua volta chiama un servizio online Microsoft o un'altra API Web personalizzata che supporta Azure AD.

Questo scenario può essere supportato usando la concessione delle credenziali di connessione JWT di OAuth 2.0, nota anche come flusso On-Behalf-Of. Tuttavia, il flusso On-Behalf-Of non è attualmente supportato per l'endpoint 2.0. Per verificare il funzionamento di questo flusso nel servizio Azure AD disponibile a livello generale, consultare l'[esempio di codice On-Behalf-Of su GitHub](https://github.com/AzureADSamples/WebAPI-OnBehalfOf-DotNet).

## Restrizioni relative alle registrazioni di app
Attualmente, per tutte le app da integrare con l'endpoint 2.0 è necessario creare una nuova registrazione in [apps.dev.microsoft.com](https://apps.dev.microsoft.com). Le eventuali app Azure AD o dell'account Microsoft esistenti non sono compatibili con l'endpoint 2.0, come anche le app registrate in portali diversi dal nuovo portale di registrazione delle app. Intendiamo fornire un modo per consentire che le applicazioni esistenti vengano usate come app 2.0. Al momento, tuttavia, non è disponibile la migrazione delle applicazioni verso l'endpoint 2.0.

Analogamente, le app registrate nel nuovo portale di registrazione delle app non funzionano con l'endpoint di autenticazione di Azure AD originale. Tuttavia, è possibile usare le app create nel portale di registrazione delle app per realizzare la piena integrazione con l'endpoint di autenticazione dell'account Microsoft, `https://login.live.com`.

Le registrazioni di app create in [apps.dev.microsoft.com](https://apps.dev.microsoft.com) hanno le condizioni seguenti:

- La proprietà **homepage**, nota anche come **URL di accesso** non è supportata. Senza una home page, queste applicazioni non verranno visualizzate nel pannello Mie app di Office.
- In questo momento, sono consentiti solo due segreti dell'app per ogni ID applicazione.
- La registrazione di un'app può essere visualizzata e gestita solo da un singolo account sviluppatore. Non può essere condiviso tra più sviluppatori.
- Esistono diverse restrizioni relative al formato consentito per l'URI di reindirizzamento. Per maggiori dettagli, vedere la sezione seguente.

## Limitazioni relative agli URI di reindirizzamento
Le app registrate nel nuovo portale di registrazione delle app sono attualmente limitate a un set ristretto di valori di URI di reindirizzamento. L'URI di reindirizzamento per le applicazioni e i servizi Web deve iniziare con lo schema `https` e tutti i valori degli URI di reindirizzamento devono condividere un singolo dominio DNS. Ad esempio, non è possibile registrare un'app Web con gli URI di reindirizzamento seguenti:

`https://login-east.contoso.com` `https://login-west.contoso.com`

Il sistema di registrazione confronta il nome DNS intero dell'URI di reindirizzamento esistente con il nome DNS dell'URI di reindirizzamento da aggiungere. La richiesta di aggiunta del nome DNS avrà esito negativo se vengono soddisfatte le condizioni seguenti:

- Se l'intero nome DNS del nuovo URI di reindirizzamento non corrisponde al nome DNS dell'URI di reindirizzamento esistente.
- Se l'intero nome DNS del nuovo URI di reindirizzamento non è un sottodominio dell'URI di reindirizzamento esistente.

Ad esempio, se l'URI di reindirizzamento corrente dell'applicazione è:

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

- L'app che ha richiesto il token. Un'app può acquisire un token di accesso per se stessa, se l'app per la logica è costituita da diversi componenti o livelli. Per vedere questo scenario, consultare le esercitazioni nella sezione [introduttiva](active-directory-appmodel-v2-overview.md#getting-started).
- Le API REST di Posta, Calendario e Contatti di Outlook che si trovano in https://outlook.office.com. Per informazioni su come scrivere un'app che accede a queste API, fare riferimento alle esercitazioni nella sezione [introduttiva di Office](https://www.msdn.com/office/office365/howto/authenticate-Office-365-APIs-using-v2).
- Le API Microsoft Graph. Per informazioni su Microsoft Graph e su tutti i dati disponibili, vedere [https://graph.microsoft.io](https://graph.microsoft.io).

Attualmente non sono supportati altri servizi. In futuro verranno aggiunti altri servizi online Microsoft nonché il supporto per le API Web e i servizi personalizzati.

## Restrizioni relative alle librerie e agli SDK
In questo momento, il supporto della libreria per l'endpoint 2.0 è piuttosto limitato. Per usare l'endpoint 2.0 in un'applicazione di produzione, sono disponibili le opzioni seguenti:

- Se si compila un'applicazione Web, è possibile usare il middleware lato server disponibile a livello generale per eseguire operazioni di accesso e convalida dei token. È incluso il middleware OpenID Connect OWIN per ASP.NET e il plug-in di Passport per NodeJS. Nella sezione [Introduzione](active-directory-appmodel-v2-overview.md#getting-started) sono disponibili anche esempi di codice che usano tale middleware.
- Per altre piattaforme e per le applicazioni native e per dispositivi mobili, è possibile realizzare l'integrazione con l'endpoint 2.0 anche con l'invio e la ricezione diretta di messaggi di protocollo nel codice dell'applicazione. I protocolli OAuth e OpenID Connect della versione 2.0 [sono stati documentati in modo esplicito](active-directory-v2-protocols.md) per consentire tale integrazione.
- Per l'integrazione con l'endpoint 2.0 è anche possibile usare librerie OpenID Connect e OAuth open source. Il protocollo della versione 2.0 dovrebbe essere compatibile con molte librerie di protocollo open source senza modifiche rilevanti. La disponibilità di tali librerie varia in base alla piattaforma e al linguaggio. Nei siti Web di [OpenID Connect](http://openid.net/connect/) e [OAuth 2.0](http://oauth.net/2/) sono disponibili elenchi delle implementazioni più diffuse. Per altre informazioni e per l'elenco delle librerie client open source, nonché per esempi testati con l'endpoint 2.0, vedere [Azure Active Directory (AD) 2.0 e librerie di autenticazione](active-directory-v2-libraries.md) .

Microsoft ha rilasciato anche un'anteprima iniziale di [Microsoft Authentication Library (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) solo per .NET. Si invitano gli utenti a provare questa libreria nelle applicazioni client e server .NET che tuttavia, in quanto libreria di anteprima, non dispone di supporto di qualità a livello generale.

## Restrizioni relative ai protocolli
L'endpoint 2.0 supporta unicamente i protocolli OpenID Connect e OAuth 2.0. Tuttavia, non tutte le funzionalità e caratteristiche dei vari protocolli sono state incorporate nell'endpoint 2.0, tra cui:

- `end_session_endpoint` di OpenID Connect, che consente a un'app terminare la sessione dell'utente con l'endpoint 2.0.
- I token ID emessi dall'endpoint 2.0 contengono solo un identificatore pairwise per l'utente. Ciò significa che due applicazioni diverse riceveranno un ID diverso per lo stesso utente. Si noti che, eseguendo una query sull'endpoint `/me` Microsoft Graph, sarà possibile ottenere un ID correlabile per l'utente da usare nelle diverse applicazioni.
- A questo punto, i token ID emessi dall'endpoint 2.0 non contengono un'attestazione `email` per l'utente, anche se si acquisisce l'autorizzazione da parte dell'utente per visualizzare la posta elettronica.
- L'endpoint di informazioni sull'utente di OpenID Connect. Attualmente, l'endpoint di informazioni sull'utente non è implementato nell'endpoint 2.0. Tuttavia, tutti i dati del profilo utente potenzialmente ricevuti sull'endpoint sono disponibili tramite l'endpoint `/me` di Microsoft Graph.
- Attestazioni di ruoli e gruppi. In questo momento, l'endpoint 2.0 non supporta l'emissione di attestazioni di ruoli o gruppi nei token ID.

Per comprendere meglio l'ambito della funzionalità del protocollo supportata nell'endpoint 2.0, vedere il [riferimento ai protocolli OpenID Connect e OAuth 2.0](active-directory-v2-protocols.md).

## Restrizioni per gli account di lavoro e dell'istituto di istruzione
Alcune funzionalità specifiche per gli utenti Microsoft aziendali non sono ancora supportate dall'endpoint 2.0.

##### Accesso condizionale basato su dispositivo, applicazioni native e per dispositivi mobili e Microsoft Graph
L'endpoint 2.0 non supporta ancora l'autenticazione del dispositivo per applicazioni native e per dispositivi mobili, ad esempio app native di iOS o Android. Questo potrebbe impedire a un'applicazione nativa di eseguire chiamate a Microsoft Graph per alcune organizzazioni. L'autenticazione del dispositivo è necessaria quando un amministratore imposta un criterio di accesso condizionale basato su dispositivo in un'applicazione. Per l'endpoint 2.0, la situazione più probabile per l'accesso condizionale basato su dispositivo è che un amministratore imposti un criterio in una risorsa di Microsoft Graph, ad esempio l'API di Outlook. Se un amministratore imposta questo criterio e l'applicazione nativa richiede un token di Microsoft Graph, la richiesta avrà esito negativo perché l'autenticazione del dispositivo non è ancora supportata. Le applicazioni Web che richiedono token a Microsoft Graph sono supportate se sono configurati criteri basati sul dispositivo. Nello scenario delle app Web l'autenticazione del dispositivo viene eseguita tramite il web browser dell'utente.

È possibile che gli sviluppatori non riescano ad avere il controllo sul momento in cui i criteri vengono impostati sulle risorse Microsoft Graph o addirittura che non sappiano che l'impostazione è avvenuta. Se si compila un'applicazione per utenti aziendali e dell'istituto di istruzione, è necessario usare [l'endpoint originale di Azure AD](active-directory-developers-guide.md) fino a quando l'endpoint 2.0 non supporta l'autenticazione del dispositivo. Per altre informazioni sull'accesso condizionale basato su dispositivo, consultare [questo articolo](active-directory-conditional-access.md#device-based-conditional-access).

##### Autenticazione integrata di Windows per tenant federati
Se si usa ADAL, e quindi l'endpoint originale di Azure AD, in applicazioni Windows, è possibile che si stia usando la cosiddetta autorizzazione all'asserzione SAML. Questa autorizzazione consente agli utenti dei tenant Azure AD federati di eseguire automaticamente l'autenticazione con l'istanza di Active Directory locale senza dover immettere le credenziali. L'autorizzazione per l'asserzione SAML non è supportata sull'endpoint 2.0.

<!---HONumber=AcomDC_0928_2016-->