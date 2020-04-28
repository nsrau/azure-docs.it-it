---
title: Autenticazione in Microsoft Identity Platform | Azure
description: Informazioni sulle nozioni di base sull'autenticazione in Microsoft Identity Platform (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: d979745d9b5bb65bd08f69db86801156de2a489d
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/27/2020
ms.locfileid: "82161742"
---
# <a name="authentication-basics"></a>Nozioni di base sull'autenticazione

Questo articolo descrive molti dei concetti di autenticazione che è necessario comprendere per creare app Web protette, API Web o app che chiamano API Web protette. Se viene visualizzato un termine con cui non si ha familiarità, provare il [Glossario](developer-glossary.md) o i [video della piattaforma Microsoft Identity](identity-videos.md) che illustrano i concetti di base.

## <a name="authentication-vs-authorization"></a>Autenticazione e autorizzazione

L' **autenticazione** è il processo di dimostrazione dell'utente. In lingua inglese, il termine autenticazione viene talvolta abbreviato in AuthN. Microsoft Identity Platform implementa il protocollo [OpenID Connect](https://openid.net/connect/) per la gestione dell'autenticazione.

L' **autorizzazione** è l'atto di concedere a un'entità autenticata l'autorizzazione per eseguire un'operazione. Specifica i dati a cui si è autorizzati ad accedere e le operazioni che è possibile eseguire con tali dati. In lingua inglese, il termine autorizzazione viene talvolta abbreviato in AuthZ. Microsoft Identity Platform implementa il protocollo [OAuth 2,0](https://oauth.net/2/) per la gestione dell'autorizzazione.

Anziché creare app che gestiscono le proprie informazioni relative a nome utente e password, il che comporta un carico amministrativo elevato quando è necessario aggiungere o rimuovere utenti in più app, le app possono delegare tale responsabilità a un provider di identità centralizzato.

Azure Active Directory (Azure AD) è un provider di identità centralizzato nel cloud. La delega dell'autenticazione e dell'autorizzazione al servizio IT consente scenari come i criteri di accesso condizionale che richiedono a un utente di trovarsi in una posizione specifica, l'uso dell'autenticazione a più fattori, nonché l'accesso di un utente una sola volta e quindi l'accesso automatico a tutte le app Web che condividono la stessa directory centralizzata. Questa funzionalità è denominata **Single Sign-on (SSO)**.

La piattaforma di identità Microsoft semplifica l'autenticazione e l'autorizzazione per gli sviluppatori di applicazioni fornendo identità come servizio, con il supporto per protocolli standard del settore come OAuth 2,0 e OpenID Connect, nonché librerie open source per piattaforme diverse che consentono di iniziare rapidamente a scrivere codice. Consente agli sviluppatori di creare applicazioni che consentono di accedere a tutte le identità Microsoft, ottenere i token per chiamare [Microsoft Graph](https://developer.microsoft.com/graph/), altre API Microsoft o API create dagli sviluppatori. Per ulteriori informazioni, vedere [Evolution of Microsoft Identity Platform](about-microsoft-identity-platform.md).

## <a name="security-tokens"></a>Token di sicurezza

Un provider di identità centralizzato è particolarmente importante per le app che hanno utenti dislocati in tutto il mondo che non devono necessariamente accedere dalla rete aziendale. Microsoft Identity Platform autentica gli utenti e fornisce i token di sicurezza, ad esempio [token di accesso](developer-glossary.md#access-token), token di [aggiornamento](developer-glossary.md#refresh-token)e [token ID](developer-glossary.md#id-token), che consentono a un' [applicazione client](developer-glossary.md#client-application) di accedere alle risorse protette in un [server di risorse](developer-glossary.md#resource-server).

Un **token di accesso** è un token di sicurezza emesso da un server di autorizzazione. Contiene informazioni sull'utente e sull'app per cui è previsto il token. che può essere usato per accedere alle API Web e ad altre risorse protette. Per altre informazioni sul modo in cui la piattaforma di identità Microsoft rilascia i token di accesso, vedere [token di accesso](access-tokens.md).

I token di accesso sono validi solo per un breve periodo di tempo, quindi i server di autorizzazione emetteranno a volte un **token di aggiornamento** nello stesso momento in cui viene emesso il token di accesso. Quando necessario, l'applicazione client può scambiare questo token di aggiornamento per un nuovo token di accesso. Per altre informazioni sul modo in cui la piattaforma di identità Microsoft usa i token di aggiornamento per revocare le autorizzazioni, vedere revoca dei [token](access-tokens.md#token-revocation).

I **token ID** vengono inviati all'applicazione client come parte di un flusso di [OpenID Connect](v2-protocols-oidc.md) . Possono essere inviati insieme o in sostituzione di un token di accesso e vengono usati dal client per autenticare l'utente. Per altre informazioni sul modo in cui la piattaforma di identità Microsoft rilascia i token ID, vedere [token ID](id-tokens.md).

### <a name="validating-security-tokens"></a>Convalida di token di sicurezza

È l'app per cui è stato generato il token, l'app Web che ha eseguito l'accesso all'utente o l'API Web chiamata per convalidare il token. Il token è firmato dal server del token di sicurezza (STS) con una chiave privata. Il servizio token di stato pubblica la chiave pubblica corrispondente. Per convalidare un token, l'app verifica la firma usando la chiave pubblica STS per verificare che la firma sia stata creata usando la chiave privata.

I token sono validi solo per un periodo di tempo limitato. In genere, il servizio token di servizio fornisce una coppia di token:

* Un token di accesso per accedere all'applicazione o alla risorsa protetta e
* Token di aggiornamento usato per aggiornare il token di accesso quando il token di accesso è prossimo alla scadenza.

I token di accesso vengono passati a un'API Web come bearer token nell' `Authorization` intestazione. Un'app può fornire un token di aggiornamento al servizio token di accesso e, se l'accesso dell'utente all'app non è stato revocato, otterrà un nuovo token di accesso e un nuovo token di aggiornamento. Questo è il modo in cui viene gestito lo scenario di un utente che lascia l'organizzazione. Quando il servizio token di accesso riceve il token di aggiornamento, non emette un altro token di accesso valido se l'utente non è più autorizzato.

### <a name="json-web-tokens-jwts-and-claims"></a>Token Web JSON (token JWT) e attestazioni

La piattaforma di identità Microsoft implementa i token di sicurezza come token Web JSON (token JWT) che contengono attestazioni.

Un' [attestazione](developer-glossary.md#claim) fornisce asserzioni su un'entità, ad esempio un'applicazione client o un [proprietario della risorsa](developer-glossary.md#resource-owner), a un'altra entità, ad esempio un server di risorse.

Le attestazioni sono coppie nome/valore che inoltrano informazioni sull'oggetto del token. Ad esempio, un'attestazione può contenere fact sull'entità di sicurezza autenticata dal server di [autorizzazione](developer-glossary.md#authorization-server). Le attestazioni presenti in un determinato token dipendono da molti elementi, tra cui il tipo di token, il tipo di credenziale usato per autenticare l'oggetto, la configurazione dell'applicazione e così via.

Le applicazioni possono utilizzare attestazioni per diverse attività, ad esempio:

* Convalida del token
* Identificazione del tenant del soggetto del token
* Visualizzazione delle informazioni utente
* Determinazione dell'autorizzazione del soggetto

Un'attestazione è costituita da coppie chiave-valore che forniscono informazioni come:

* Server del token di sicurezza che ha generato il token
* Data di generazione del token
* Subject (ad esempio l'utente, ad eccezione dei daemon)
* Destinatari, ovvero l'app per cui è stato generato il token
* App (il client) che ha richiesto il token. Nel caso di app Web, questo potrebbe essere lo stesso dei destinatari

Per altre informazioni sul modo in cui la piattaforma di identità Microsoft implementa i token e le informazioni sulle attestazioni, vedere token di [accesso](access-tokens.md) e [token ID](id-tokens.md).

### <a name="how-each-flow-emits-tokens-and-codes"></a>Modo in cui ogni flusso emette token e codici

A seconda del modo in cui viene compilato il client, può usare uno o più dei flussi di autenticazione supportati dalla piattaforma di identità Microsoft. Questi flussi possono produrre un'ampia gamma di token (id_tokens, token di aggiornamento, token di accesso) e codici di autorizzazione e richiedono token diversi per consentirne il funzionamento. Questo grafico fornisce una panoramica:

|Flusso | Richiede | id_token | token di accesso | token di aggiornamento | codice di autorizzazione |
|-----|----------|----------|--------------|---------------|--------------------|
|[Flusso del codice di autorizzazione](v2-oauth2-auth-code-flow.md) | | x | x | x | x|
|[Flusso implicito](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Flusso OIDC ibrido](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Riscatto token di aggiornamento](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | token di aggiornamento | x | x | x| |
|[Flusso per conto di](v2-oauth2-on-behalf-of-flow.md) | token di accesso| x| x| x| |
|[Credenziali del client](v2-oauth2-client-creds-grant-flow.md) | | | x (solo app)| | |

I token emessi tramite la modalità implicita hanno una limitazione di lunghezza perché vengono passati di nuovo al browser tramite l'URL, `response_mode` dove `query` è `fragment`o.  Alcuni browser hanno un limite per le dimensioni dell'URL che possono essere inseriti nella barra del browser e hanno esito negativo quando è troppo lungo.  Pertanto, questi token non dispongono `groups` di attestazioni `wids` o.

## <a name="tenants"></a>Tenant

Un provider di identità cloud offre molte organizzazioni. Per evitare che gli utenti di organizzazioni diverse si separano, Azure AD viene partizionato in tenant, con un tenant per ogni organizzazione.

I tenant tengono traccia degli utenti e delle app associate. Microsoft Identity Platform supporta anche gli utenti che hanno accesso con account Microsoft personali.

Azure AD fornisce anche Azure Active Directory B2C in modo che le organizzazioni possano accedere agli utenti, in genere clienti, usando identità social come un account Google. Per ulteriori informazioni, vedere [Azure Active Directory B2C documentazione](https://docs.microsoft.com/azure/active-directory-b2c) .

Ora che si dispone di una panoramica delle nozioni di base, vedere per comprendere il modello e l'API dell'app Identity, informazioni sul funzionamento del provisioning nella piattaforma di identità Microsoft e collegamenti a informazioni dettagliate sugli scenari comuni supportati da Microsoft Identity Platform.

## <a name="application-model"></a>Modello applicativo

Le applicazioni possono accedere agli utenti stessi o delegare l'accesso a un provider di identità. Vedere [flussi di autenticazione e scenari di app](authentication-flows-app-scenarios.md) per informazioni sugli scenari di accesso supportati dalla piattaforma di identità Microsoft.

Affinché un provider di identità sappia che un utente ha accesso a una determinata app, sia l'utente che l'applicazione devono essere registrati con il provider di identità. Quando si registra l'applicazione con Azure AD, viene offerta una configurazione di identità per l'applicazione che consente l'integrazione con la piattaforma di identità Microsoft. La registrazione dell'app consente anche di:

* Personalizzare la personalizzazione dell'applicazione nella finestra di dialogo di accesso. Questo è importante perché si tratta della prima esperienza che un utente avrà con l'app.
* Decidere se si desidera consentire agli utenti di accedere solo se appartengono all'organizzazione. Si tratta di un'applicazione single-tenant. In alternativa, consentire agli utenti di accedere usando un account aziendale o dell'Istituto di istruzione. Si tratta di un'applicazione multi-tenant. È anche possibile consentire account Microsoft personali o un account di social networking da LinkedIn, Google e così via.
* Autorizzazioni dell'ambito della richiesta. È ad esempio possibile richiedere l'ambito "User. Read", che concede l'autorizzazione per leggere il profilo dell'utente che ha eseguito l'accesso.
* Definire gli ambiti che definiscono l'accesso all'API Web. In genere, quando un'app vuole accedere all'API, sarà necessario richiedere le autorizzazioni agli ambiti definiti.
* Condividere un segreto con la piattaforma di identità Microsoft per dimostrare l'identità dell'app.  Questo è rilevante nel caso in cui l'app sia un'applicazione client riservata. Un'applicazione client riservata è un'applicazione in grado di conservare le credenziali in modo sicuro. Per archiviare le credenziali è necessario un server back-end attendibile.

Una volta effettuata la registrazione, all'applicazione verrà assegnato un identificatore univoco che l'app condivide con la piattaforma di identità Microsoft quando richiede i token. Se l'app è un' [applicazione client riservata](developer-glossary.md#client-application), condividerà anche il segreto o la chiave pubblica *, a seconda che siano stati usati i certificati o i segreti.

Microsoft Identity Platform rappresenta le applicazioni che utilizzano un modello che soddisfa due funzioni principali:

* Identificare l'app in base ai protocolli di autenticazione supportati
* Specificare tutti gli identificatori, gli URL, i segreti e le informazioni correlate necessarie per l'autenticazione

Piattaforma di identità Microsoft:

* Include tutti i dati necessari per supportare l'autenticazione in fase di esecuzione
* Include tutti i dati per la decisione delle risorse a cui un'app potrebbe dover accedere e in quali circostanze deve essere soddisfatta una determinata richiesta
* Fornisce l'infrastruttura per implementare il provisioning delle app nel tenant dello sviluppatore di app e in qualsiasi altro tenant Azure AD
* Gestisce il consenso dell'utente durante il tempo di richiesta del token e facilita il provisioning dinamico delle app tra i tenant

Il consenso è il processo di un proprietario di risorse che concede l'autorizzazione a un'applicazione client per accedere alle risorse protette, in autorizzazioni specifiche, per conto del proprietario della risorsa. Piattaforma di identità Microsoft:

* Consente a utenti e amministratori di concedere o negare dinamicamente il consenso all'app di accedere alle risorse per loro conto.
* Consente agli amministratori di decidere quali app sono autorizzati a eseguire e quali utenti possono usare app specifiche, nonché la modalità di accesso alle risorse di directory.

In Microsoft Identity Platform, un [oggetto applicazione](developer-glossary.md#application-object) descrive un'applicazione. In fase di distribuzione, Microsoft Identity Platform usa l'oggetto applicazione come progetto per creare un' [entità servizio](developer-glossary.md#service-principal-object), che rappresenta un'istanza concreta di un'applicazione all'interno di una directory o di un tenant. L'entità servizio definisce le operazioni che l'app può effettivamente eseguire in una directory di destinazione specifica, chi può usarla, a quali risorse ha accesso e così via. Microsoft Identity Platform crea un'entità servizio da un oggetto applicazione tramite il **consenso**.

Il diagramma seguente illustra un flusso di provisioning di Microsoft Identity Platform semplificato basato su consenso. Mostra due tenant: *a* e *B*.

* Il *tenant A* è proprietario dell'applicazione.
* Il *tenant B* sta creando un'istanza dell'applicazione tramite un'entità servizio.

![Flusso di provisioning semplificato basato su consenso](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

In questo flusso di provisioning:

1. Un utente del tenant B prova ad accedere con l'app, l'endpoint di autorizzazione richiede un token per l'applicazione.
1. Le credenziali utente vengono acquisite e verificate per l'autenticazione.
1. All'utente viene richiesto di fornire il consenso affinché l'app ottenga l'accesso al tenant B.
1. Microsoft Identity Platform usa l'oggetto applicazione nel tenant A come progetto per la creazione di un'entità servizio nel tenant B.
1. L'utente riceve il token richiesto.

È possibile ripetere questo processo per tenant aggiuntivi. Il tenant A mantiene il progetto per l'app (oggetto applicazione). Gli utenti e gli amministratori di tutti gli altri tenant in cui l'app dispone del consenso continuano a controllare ciò che l'applicazione può eseguire tramite l'oggetto entità servizio corrispondente in ogni tenant. Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio in Microsoft Identity Platform](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-microsoft-identity-platform"></a>Flusso di accesso all'app Web con la piattaforma di identità Microsoft

Quando un utente accede al browser a un'app Web, si verifica quanto segue:

* L'app Web determina se l'utente è autenticato.
* Se l'utente non è autenticato, l'app Web delega a Azure AD per l'accesso dell'utente. Tale accesso sarà conforme ai criteri dell'organizzazione, che possono significare richiedere all'utente di immettere le proprie credenziali, usando l'autenticazione a più fattori o non usando una password, ad esempio usando Windows Hello.
* All'utente viene richiesto di fornire il consenso per l'accesso necessario all'app client. Questo è il motivo per cui le app client devono essere registrate con Azure AD, in modo che la piattaforma di identità Microsoft possa fornire token che rappresentano l'accesso a cui l'utente ha acconsentito.

Quando l'utente è stato autenticato correttamente:

* Microsoft Identity Platform Invia un token all'app Web.
* Un cookie viene salvato, associato al dominio Azure AD, che contiene l'identità dell'utente nel file jar del browser. La volta successiva che un'app usa il browser per passare all'endpoint di autorizzazione di Microsoft Identity Platform, il browser presenta il cookie in modo che l'utente non debba eseguire di nuovo l'accesso. Questo è anche il modo in cui si raggiunge SSO. Il cookie viene generato da Azure AD e può essere compreso solo da Azure AD.
* Il token viene quindi convalidato dall'app Web. Se la convalida ha esito positivo, l'app Web Visualizza la pagina protetta e salva un cookie di sessione nel file jar dei cookie del browser. Quando l'utente passa a un'altra pagina, l'app Web sa che l'utente è autenticato in base al cookie di sessione.

Il diagramma di sequenza seguente riepiloga questa interazione:

![processo di autenticazione dell'app Web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Modo in cui un'app Web determina se l'utente è autenticato

Gli sviluppatori di app Web possono indicare se tutte o solo determinate pagine richiedono l'autenticazione. Ad esempio, in ASP.NET/ASP.NET Core questa operazione viene eseguita aggiungendo l' `[Authorize]` attributo alle azioni del controller.

Questo attributo fa in modo che ASP.NET verifichi la presenza di un cookie di sessione contenente l'identità dell'utente. Se non è presente un cookie, ASP.NET reindirizza l'autenticazione al provider di identità specificato. Se il provider di identità è Azure AD, l'app Web reindirizza l'autenticazione a `https://login.microsoftonline.com`, che visualizza una finestra di dialogo di accesso.

### <a name="how-a-web-app-delegates-sign-in-to-microsoft-identity-platform-and-obtains-a-token"></a>Delega dell'accesso a Microsoft Identity Platform da un'app Web e ottenimento di un token

L'autenticazione utente avviene tramite il browser. Il protocollo OpenID utilizza messaggi di protocollo HTTP standard.

* L'app Web invia un HTTP 302 (Reindirizzamento) al browser per usare la piattaforma di identità Microsoft.
* Quando l'utente viene autenticato, la piattaforma Microsoft Identity invia il token all'app Web usando un reindirizzamento tramite il browser.
* Il reindirizzamento viene fornito dall'app Web sotto forma di URI di reindirizzamento. Questo URI di reindirizzamento viene registrato con l'oggetto applicazione Azure AD. Possono essere presenti diversi URI di reindirizzamento perché l'applicazione può essere distribuita in diversi URL. Quindi, anche l'app Web dovrà specificare l'URI di Reindirizzamento da usare.
* Azure AD verifica che l'URI di reindirizzamento inviato dall'app Web sia uno degli URI di reindirizzamento registrati per l'app.

## <a name="desktop-and-mobile-app-sign-in-flow-with-microsoft-identity-platform"></a>Flusso di accesso all'app desktop e per dispositivi mobili con la piattaforma di identità Microsoft

Il flusso descritto in precedenza si applica, con lievi differenze, alle applicazioni desktop e per dispositivi mobili.

Le applicazioni desktop e per dispositivi mobili possono utilizzare un controllo Web incorporato o un browser di sistema per l'autenticazione. Il diagramma seguente illustra il modo in cui un'app desktop o per dispositivi mobili USA Microsoft Authentication Library (MSAL) per acquisire i token di accesso e chiamare le API Web.

![App desktop come sembra](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL usa un browser per ottenere i token. Come per le app Web, l'autenticazione viene delegata alla piattaforma di identità Microsoft.

Poiché Azure AD Salva lo stesso cookie di identità nel browser come per le app Web, se l'app nativa o per dispositivi mobili usa il browser di sistema, riceverà immediatamente SSO con l'app Web corrispondente.

Per impostazione predefinita, MSAL usa il browser del sistema. L'eccezione è .NET Framework applicazioni desktop in cui viene usato un controllo incorporato per offrire un'esperienza utente più integrata.

## <a name="next-steps"></a>Passaggi successivi

* Per acquisire familiarità con i termini comuni, vedere il [Glossario per sviluppatori Microsoft Identity Platform](developer-glossary.md) .
* Vedere [flussi di autenticazione e scenari di app](authentication-flows-app-scenarios.md) per altre informazioni su altri scenari per l'autenticazione degli utenti supportati dalla piattaforma di identità Microsoft.
* Per informazioni sulle librerie Microsoft che consentono di sviluppare applicazioni che interagiscono con account Microsoft, Azure AD account e Azure AD B2C utenti in un unico modello di programmazione semplificato, vedere [librerie MSAL](msal-overview.md) .
* Vedere [integrare il servizio app con la piattaforma di identità Microsoft](/azure/app-service/configure-authentication-provider-aad) per informazioni su come configurare l'autenticazione per l'app del servizio app.
