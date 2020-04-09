---
title: Autenticazione in Microsoft Identity Platform | Azure
description: Informazioni sulle nozioni di base dell'autenticazione nella piattaforma Microsoft Identity (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: e78f822a88b093992f065a509c2250e6a5c0dec2
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885566"
---
# <a name="authentication-basics"></a>Nozioni di base sull'autenticazione

## <a name="what-is-authentication"></a>Che cos'è l'autenticazione

Questo articolo illustra molti dei concetti di autenticazione che è necessario comprendere per creare app Web protette, API Web o app che chiamano API Web protette. Se vedi un termine che non conosci, prova il nostro [glossario](developer-glossary.md) o i nostri video della piattaforma di [identità Microsoft](identity-videos.md) che trattano i concetti di base.

**L'autenticazione** è il processo che ti dimostra di essere chi dici di essere. In lingua inglese, il termine autenticazione viene talvolta abbreviato in AuthN.

**L'autorizzazione** è l'atto di concedere a una parte autenticata l'autorizzazione a eseguire un'operazione. Specifica i dati a cui è consentito accedere e le operazioni che è possibile eseguire con tali dati. In lingua inglese, il termine autorizzazione viene talvolta abbreviato in AuthZ.

Invece di creare app che gestiscono ognuna le proprie informazioni su nome utente e password, il che comporta un carico amministrativo elevato quando è necessario aggiungere o rimuovere utenti in più app, le app possono delegare tale responsabilità a un provider di identità centralizzato.

Azure Active Directory (Azure AD) è un provider di identità centralizzato nel cloud. La delega dell'autenticazione e dell'autorizzazione consente scenari quali criteri di accesso condizionale che richiedono che un utente si trova in una posizione specifica, l'uso dell'autenticazione a più fattori, nonché l'accesso di una sola volta e quindi di accedere automaticamente a tutte le app Web che condividono la stessa directory centralizzata. Questa funzionalità viene definita Single Sign-On (SSO).

Un provider di identità centralizzato è ancora più importante per le app con utenti in tutto il mondo che non necessariamente accedono dalla rete aziendale. Azure AD autentica gli utenti e fornisce i token di accesso. Un token di [accesso](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token) è un token di sicurezza emesso da un server di autorizzazione. Contiene informazioni sull'utente e sull'app a cui è destinato il token; che può essere utilizzato per accedere alle API Web e ad altre risorse protette.

La piattaforma di identità Microsoft semplifica l'autenticazione per gli sviluppatori di applicazioni fornendo identità come servizio, con il supporto per protocolli standard del settore come [OAuth 2.0](https://oauth.net/2/) e [OpenID Connect](https://openid.net/connect/), nonché librerie open source per piattaforme diverse che consentono di iniziare a codificare rapidamente. Consente agli sviluppatori di creare applicazioni che accedono a tutte le identità Microsoft, ottenere token per chiamare [Microsoft Graph,](https://developer.microsoft.com/graph/)altre API Microsoft o API create dagli sviluppatori. Per ulteriori informazioni, vedere [Evoluzione della piattaforma](about-microsoft-identity-platform.md)di identità Microsoft .

### <a name="tenants"></a>Tenant

Un provider di identità cloud serve molte organizzazioni. Per separare gli utenti di organizzazioni diverse, Azure AD viene partizionato in tenant e un tenant per organizzazione.

I tenant tengono traccia degli utenti e delle app associate. La piattaforma di identità Microsoft supporta anche gli utenti che accedono con account Microsoft personali.

Azure AD fornisce anche Azure Active Directory B2C in modo che le organizzazioni possano accedere agli utenti, in genere i clienti, usando identità social come un account Google.Azure AD also provides Azure Active Directory B2C so that organizations can sign in users, typically customers, using social identities like a Google account. Per altre informazioni, vedere la documentazione di [Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c) .

### <a name="security-tokens"></a>Token di sicurezza

I token di sicurezza contengono informazioni su utenti e app. Azure AD usa token basati su JSON (JWT) che contengono attestazioni.

Un'attestazione fornisce asserzioni su [un'entità,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application) ad esempio un'applicazione client o un proprietario di [risorsa,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner)a un'altra entità, ad esempio un server di [risorse.](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server)

Le attestazioni sono coppie nome/valore che inoltrano fatti sull'oggetto del token. Ad esempio, un'attestazione può contenere informazioni sull'entità di sicurezza autenticata dal server di [autorizzazione.](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server) Le attestazioni presenti in un determinato token dipendono da molti elementi, tra cui il tipo di token, il tipo di credenziale utilizzato per autenticare l'oggetto, la configurazione dell'applicazione e così via.

Le applicazioni possono utilizzare attestazioni per varie attività, ad esempio:Applications can use claims for various tasks such as:

* Convalida del tokenValidating the token
* Identificazione del tenant del soggetto del tokenIdentifying the token subject's tenant
* Visualizzazione delle informazioni utente
* Determinazione dell'autorizzazione del soggetto

Un'attestazione è costituita da coppie chiave-valore che forniscono informazioni quali:A claim consists of key-value pairs that provide information such as the:

* Server token di sicurezza che ha generato il tokenSecurity Token Server that generated the token
* Data in cui il token è stato generato
* Oggetto (ad esempio l'utente, ad eccezione dei daemons)
* Gruppo di destinatari, ovvero l'app per cui è stato generato il token
* App (client) che ha richiesto il token. Nel caso di applicazioni web, questo può essere lo stesso del pubblico

Per informazioni più dettagliate sulle [attestazioni,](access-tokens.md) vedere token di accesso e [token ID](id-tokens.md).

Spetta all'app per cui è stato generato il token, all'app Web che ha eseguito l'accesso all'utente o all'API Web chiamata per convalidare il token. Il token viene firmato dal Security Token Server (STS) con una chiave privata. Il servizio token di sicurezza pubblica la chiave pubblica corrispondente. Per convalidare un token, l'app verifica la firma utilizzando la chiave pubblica del servizio token di sicurezza per verificare che la firma sia stata creata usando la chiave privata.

I token sono validi solo per un periodo di tempo limitato. In genere il servizio token di sicurezza fornisce una coppia di token: un token di accesso per accedere all'applicazione o alla risorsa protetta e un token di aggiornamento usato per aggiornare il token di accesso quando il token di accesso sta per scadere.

I token di accesso vengono passati a un'API Web come token di connessione nell'intestazione. `Authorization` Un'app può fornire un token di aggiornamento al servizio token di sicurezza e, se l'accesso utente all'app non è stato revocato, otterrà un nuovo token di accesso e un nuovo token di aggiornamento. Questo è il modo in cui viene gestito lo scenario di qualcuno che lascia l'azienda. Quando il servizio token di sicurezza riceve il token di aggiornamento, non emetterà un altro token di accesso valido se l'utente non è più autorizzato.

### <a name="how-each-flow-emits-tokens-and-codes"></a>Come ogni flusso emette token e codici

A seconda della modalità di compilazione del client, può usare uno o più flussi di autenticazione supportati da Azure AD. Questi flussi possono produrre una varietà di token (id_tokens, token di aggiornamento, token di accesso) e codici di autorizzazione e richiedono token diversi per farli funzionare. Questo grafico fornisce una panoramica:This chart provides an overview:

|Flusso | Richiede | id_token | token di accesso | aggiornare il token | codice di autorizzazione | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Flusso del codice di autorizzazioneAuthorization code flow](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Flusso implicito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Flusso OIDC ibrido](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Riscatto della token di aggiornamento](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | aggiornare il token | x | x | x| |
|[Flusso on-behalf-of](v2-oauth2-on-behalf-of-flow.md) | token di accesso| x| x| x| |
|[Credenziali del client](v2-oauth2-client-creds-grant-flow.md) | | | x (solo app)| | |

I token emessi tramite la modalità implicita hanno un limite `response_mode` di `query` `fragment`lunghezza dovuto al ritorno al browser tramite l'URL (where is o ).  Alcuni browser hanno un limite alla dimensione dell'URL che può essere inserito nella barra del browser e non riescono quando è troppo lungo.  Pertanto, questi token `groups` non `wids` hanno o attestazioni. 

Ora che hai una panoramica delle nozioni di base, continua a leggere per comprendere il modello di app di identità e l'API, scopri come funziona il provisioning in Azure AD e ottieni collegamenti a informazioni dettagliate sugli scenari comuni supportati da Azure AD.

## <a name="application-model"></a>Modello di applicazione

Le applicazioni possono accedere agli utenti stessi o delegare l'accesso a un provider di identità. Vedere Flussi di [autenticazione e scenari](authentication-flows-app-scenarios.md) di app per informazioni sugli scenari di accesso supportati da Azure AD.

Affinché un provider di identità sappia che un utente ha accesso a una determinata app, sia l'utente che l'applicazione devono essere registrati con il provider di identità. Quando si registra l'applicazione con Azure AD, si fornisce una configurazione di identità per l'applicazione che ne consente l'integrazione con Azure AD. La registrazione dell'app consente inoltre di:

* Personalizzare la personalizzazione dell'applicazione nella finestra di dialogo di accesso. Questo è importante perché questa è la prima esperienza che un utente avrà con la tua app.
* Decidere se si desidera consentire agli utenti di accedere solo se appartengono all'organizzazione. Si tratta di un singolo'applicazione tenant. In alternativa, consentire agli utenti di accedere utilizzando qualsiasi account aziendale o dell'istituto di istruzione. Si tratta di un'applicazione multi-tenant. Puoi anche consentire account Microsoft personali o un account social da LinkedIn, Google e così via.
* Richiedere le autorizzazioni di ambito. Ad esempio, è possibile richiedere l'ambito "user.read", che concede l'autorizzazione per leggere il profilo dell'utente connesso.
* Definire gli ambiti che definiscono l'accesso all'API Web. In genere, quando un'app vuole accedere all'API, dovrà richiedere le autorizzazioni per gli ambiti definiti.
* Condividere un segreto con Azure AD che dimostra l'identità dell'app in Azure AD.  Ciò è rilevante nel caso in cui l'app sia un'applicazione client riservata. Un'applicazione client riservata è un'applicazione in grado di contenere le credenziali in modo sicuro. Per archiviare le credenziali è necessario un server back-end attendibile.

Una volta registrata, all'applicazione verrà assegnato un identificatore univoco condiviso dall'app con Azure AD quando richiede i token. Se l'app è [un'applicazione client riservata,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application)condividerà anche il segreto o la chiave pubblica, a seconda che siano stati utilizzati o meno certificati o segreti.

La piattaforma di identità Microsoft rappresenta le applicazioni utilizzando un modello che soddisfa due funzioni principali:The Microsoft identity platform represents applications using a model that fulfills two main functions:

* Identificare l'app in base ai protocolli di autenticazione supportati
* Fornire tutti gli identificatori, gli URL, i segreti e le informazioni correlate necessarie per l'autenticazione

La piattaforma di identità Microsoft:

* Contiene tutti i dati necessari per supportare l'autenticazione in fase di esecuzione
* Contiene tutti i dati per decidere a quali risorse potrebbe essere necessario accedere a un'app e in quali circostanze deve essere soddisfatta una determinata richiesta
* Fornisce l'infrastruttura per l'implementazione del provisioning delle app all'interno del tenant dello sviluppatore dell'app e a qualsiasi altro tenant di Azure AD
* Gestisce il consenso dell'utente durante il tempo di richiesta del token e facilita il provisioning dinamico delle app tra tenant

Il consenso è il processo di concessione dell'autorizzazione per un'applicazione client per accedere alle risorse protette, con autorizzazioni specifiche, per conto del proprietario della risorsa. La piattaforma di identità Microsoft:

* Consente a utenti e amministratori di concedere o negare dinamicamente il consenso all'app di accedere alle risorse per loro conto.
* Consente agli amministratori di decidere quali app sono autorizzati a eseguire e quali utenti possono usare app specifiche, nonché la modalità di accesso alle risorse di directory.

Nella piattaforma di identità Microsoft, un [oggetto applicazione](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object) descrive un'applicazione. In fase di distribuzione, la piattaforma di identità Microsoft utilizza l'oggetto applicazione come blueprint per creare [un'entità servizio,](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object)che rappresenta un'istanza concreta di un'applicazione all'interno di una directory o di un tenant. L'entità servizio definisce le operazioni che l'app può effettivamente eseguire in una directory di destinazione specifica, chi può usarla, a quali risorse ha accesso e così via. La piattaforma di identità Microsoft crea un'entità servizio da un oggetto applicazione tramite **il consenso**.

Il diagramma seguente illustra un flusso di provisioning di Microsoft Identity Platform semplificato basato su consenso. Mostra due tenant: A e B. Tenant A possiede l'applicazione. Il tenant B crea un'istanza dell'applicazione tramite un'entità servizio.  

![Flusso di provisioning semplificato basato su consenso](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

In questo flusso di provisioning:

1. Un utente del tenant B prova ad accedere con l'app, l'endpoint di autorizzazione richiede un token per l'applicazione.
1. Le credenziali utente vengono acquisite e verificate per l'autenticazione.
1. The user is prompted to provide consent for the app to gain access to tenant B.
1. The Microsoft identity platform uses the application object in tenant A as a blueprint for creating a service principal in tenant B.
1. L'utente riceve il token richiesto.

È possibile ripetere questo processo per altri tenant. Il tenant A mantiene il progetto per l'app (oggetto applicazione). Gli utenti e gli amministratori di tutti gli altri tenant in cui all'app viene concesso il consenso mantengono il controllo sulle operazioni consentite all'applicazione tramite l'oggetto entità servizio corrispondente in ogni tenant. Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio in Microsoft Identity Platform](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Flusso di accesso dell'app Web con Azure AD

Quando un utente passa nel browser a un'app Web, si verifica quanto segue:

* L'app Web determina se l'utente è autenticato.
* Se l'utente non è autenticato, l'app Web delega ad Azure AD per accedere all'utente. Tale accesso sarà conforme ai criteri dell'organizzazione, il che significa chiedere all'utente di immettere le proprie credenziali, utilizzare l'autenticazione a più fattori o non utilizzare affatto una password (ad esempio, utilizzando Windows Hello).
* All'utente viene chiesto di acconsentire all'accesso necessario all'app client. Questo è il motivo per cui le app client devono essere registrate con Azure AD, in modo che Azure AD possa fornire token che rappresentano l'accesso a cui l'utente ha acconsentito.

Quando l'utente è stato autenticato correttamente:

* Azure AD invia un token all'app Web.
* Viene salvato un cookie, associato al dominio di Azure AD, che contiene l'identità dell'utente nel file jar di cookie del browser. La volta successiva che un'app usa il browser per passare all'indirizzo finale dell'autorizzazione di Azure AD, il browser presenta il cookie in modo che l'utente non debba eseguire nuovamente l'accesso. Questo è anche il modo in cui viene raggiunto SSO. Il cookie viene prodotto da Azure AD e può essere compreso solo da Azure AD.
* L'app Web convalida quindi il token. Se la convalida ha esito positivo, l'app Web visualizza la pagina protetta e salva un cookie di sessione nel cookie jar del browser. Quando l'utente passa a un'altra pagina, l'app Web sa che l'utente viene autenticato in base al cookie di sessione.

Il diagramma di sequenza seguente riepiloga questa interazione:The following sequence diagram summarizes this interaction:

![Processo di autenticazione dell'app Web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Come un'app Web determina se l'utente è autenticato

Gli sviluppatori di app Web possono indicare se tutte o solo alcune pagine richiedono l'autenticazione. Ad esempio, in ASP.NET/ASP.NET Core, questa `[Authorize]` operazione viene eseguita aggiungendo l'attributo alle azioni del controller. 

Questo attributo fa ASP.NET verificare la presenza di un cookie di sessione contenente l'identità dell'utente. Se non è presente un cookie, ASP.NET reindirizza l'autenticazione al provider di identità specificato. Se il provider di identità è Azure `https://login.microsoftonline.com`AD, l'app Web reindirizza l'autenticazione a , che visualizza una finestra di dialogo di accesso.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Come un'app Web delega l'accesso ad Azure AD e ottiene un tokenHow a web app delegates sign-in to Azure AD and obtains a token

L'autenticazione dell'utente avviene tramite il browser. Il protocollo OpenID utilizza messaggi di protocollo HTTP standard.
* L'app Web invia un HTTP 302 (reindirizzamento) al browser per usare Azure AD.
* Quando l'utente viene autenticato, Azure AD invia il token all'app Web usando un reindirizzamento tramite il browser.
* Il reindirizzamento viene fornito dall'app Web sotto forma di URI di reindirizzamento. Questo URI di reindirizzamento viene registrato con l'oggetto applicazione Azure AD. Possono essere presenti diversi URI di reindirizzamento perché l'applicazione può essere distribuita in diversi URL. Pertanto, l'app Web dovrà specificare anche l'URI di reindirizzamento da utilizzare.
* Azure AD verifica che l'URI di reindirizzamento inviato dall'app Web sia uno degli URI di reindirizzamento registrati per l'app.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Flusso di accesso alle app desktop e per dispositivi mobili con Azure AD

Il flusso descritto in precedenza si applica, con lievi differenze, alle applicazioni desktop e mobili.

Le applicazioni desktop e mobili possono utilizzare un controllo Web incorporato, o un browser di sistema, per l'autenticazione. Il diagramma seguente mostra come un'app desktop o per dispositivi mobili usa la libreria di autenticazione Microsoft (MSAL) per acquisire token di accesso e chiamare API Web.

![App desktop come sembra essere](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL utilizza un browser per ottenere i token. Come per le app Web, l'autenticazione viene delegata ad Azure AD.

Poiché Azure AD salva nel browser lo stesso cookie di identità come per le app Web, se l'app nativa o per dispositivi mobili usa il browser di sistema otterrà immediatamente SSO con l'app Web corrispondente.

Per impostazione predefinita, MSAL utilizza il browser di sistema. L'eccezione è riguarda le applicazioni desktop .NET Framework in cui un controllo incorporato viene utilizzato per fornire un'esperienza utente più integrata.

## <a name="next-steps"></a>Passaggi successivi

* Consulta il [glossario per sviluppatori della piattaforma microsoft Identity](developer-glossary.md) per familiarizzare con i termini comuni.
* Per altre informazioni su altri scenari per l'autenticazione degli utenti supportati dalla piattaforma di identità Microsoft, vedere [Flussi di autenticazione e scenari](authentication-flows-app-scenarios.md) delle app.
* Vedere [Librerie MSAL](msal-overview.md) per informazioni sulle librerie Microsoft che consentono di sviluppare applicazioni che funzionano con gli account Microsoft, gli account di Azure AD e gli utenti B2C di Azure AD in un unico modello di programmazione semplificato.
* Vedere [Integrare il servizio app con](/azure/app-service/configure-authentication-provider-aad) la piattaforma di identità Microsoft per informazioni su come configurare l'autenticazione per l'app del servizio app.
