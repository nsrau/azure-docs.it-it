---
title: Scenari di autenticazione per Azure AD | Documentazione Microsoft
description: "Panoramica dei cinque più comuni scenari di autenticazione per Azure Active Directory (AAD)"
services: active-directory
documentationcenter: dev-center-name
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
translationtype: Human Translation
ms.sourcegitcommit: 8c4e33a63f39d22c336efd9d77def098bd4fa0df
ms.openlocfilehash: 49ac46d8a04e31df1e5c31a60e387c8cd0b93037
ms.lasthandoff: 04/20/2017


---
# <a name="authentication-scenarios-for-azure-ad"></a>Scenari di autenticazione per Azure AD
Azure Active Directory (AD) semplifica l'autenticazione per gli sviluppatori fornendo le identità come servizio, con il supporto per protocolli standard del settore come OAuth 2.0 e OpenID Connect, nonché librerie open source per diverse piattaforme che permettono di iniziare rapidamente a creare codice. Questo documento include informazioni utili per comprendere i diversi scenari supportati da Azure AD, oltre a illustrare come iniziare. Il documento è suddiviso nelle sezioni seguenti:

* [Nozioni di base sull'autenticazione in Azure AD.](#basics-of-authentication-in-azure-ad)
* [Attestazioni nei token di sicurezza di Azure AD](#claims-in-azure-ad-security-tokens)
* [Nozioni di base sulla registrazione di un'applicazione in Azure AD](#basics-of-registering-an-application-in-azure-ad)
* [Scenari e tipi di applicazione](#application-types-and-scenarios)
  
  * [Da Web browser ad applicazione Web](#web-browser-to-web-application)
  * [Applicazione a singola pagina (SPA)](#single-page-application-spa)
  * [Da applicazione nativa ad API Web](#native-application-to-web-api)
  * [Da applicazione Web ad API Web](#web-application-to-web-api)
  * [Da daemon o applicazione server ad API Web](#daemon-or-server-application-to-web-api)

## <a name="basics-of-authentication-in-azure-ad"></a>Nozioni di base sull'autenticazione in Azure AD.
Se non si ha familiarità con i concetti di base dell'autenticazione in Azure AD, leggere questa sezione. In caso contrario, è possibile passare alla sezione [Scenari e tipi di applicazione](#application-types-and-scenarios).

Si consideri lo scenario più semplice in cui è necessaria l'identità: un utente deve eseguire l'autenticazione a un'applicazione Web da un Web browser. Questo scenario è descritto più dettagliatamente nella sezione [Da Web browser ad applicazione Web](#web-browser-to-web-application) , ma è un punto di partenza utile per illustrare le funzionalità di Azure AD e definire i concetti relativi al funzionamento dello scenario. Per questo scenario considerare il diagramma seguente:

![Panoramica dell'accesso all'applicazione Web](./media/active-directory-authentication-scenarios/basics_of_auth_in_aad.png)

Tenendo presente il diagramma riportato sopra, ecco cosa occorre sapere sui vari componenti:

* Azure AD è il provider di identità, che si occupa della verifica dell'identità degli utenti e delle applicazioni presenti in una directory aziendale e infine rilascia i token di sicurezza, se l'autenticazione di tali utenti e applicazioni riesce.
* Un'applicazione per la quale è previsto l'outsourcing dell'autenticazione ad Azure AD deve essere registrata in Azure AD, che registra e identifica in modo univoco l'app nella directory.
* Gli sviluppatori possono usare le librerie di autenticazione open source di Azure AD per semplificare l'autenticazione gestendo direttamente i dettagli del protocollo. Per altre informazioni, vedere [Librerie di autenticazione di Azure Active Directory](active-directory-authentication-libraries.md) .

• Dopo l'autenticazione di un utente, l'applicazione deve convalidare il token di sicurezza dell'utente per verificare che per l'autenticazione le parti previste sia riuscita. Gli sviluppatori possono usare le librerie di autenticazione fornite per gestire la convalida dei token da Azure AD, inclusi i token Web JSON (JWT) o SAML 2.0. Per eseguire manualmente la convalida, vedere la documentazione relativa al [Gestore dei token JWT](https://msdn.microsoft.com/library/dn205065.aspx) .

> [!IMPORTANT]
> Azure AD usa la crittografia a chiave pubblica per firmare i token e verificarne la validità. Per altre informazioni sulla logica richiesta nell'applicazione per assicurare che sia sempre aggiornata con le chiavi più recenti, vedere [Informazioni importanti sul rollover della chiave di firma in Azure AD](active-directory-signing-key-rollover.md) .
> 
> 

• Il flusso di richieste e risposte per il processo di autenticazione dipende dal protocollo di autenticazione in uso, ad esempio OAuth 2.0, OpenID Connect, WS-Federation o SAML 2.0. Questi protocolli sono descritti in dettaglio nell'argomento [Protocolli di autenticazione di Azure Active Directory](active-directory-authentication-protocols.md) e nelle sezioni successive.

> [!NOTE]
> Azure AD supporta gli standard OAuth 2.0 e OpenID Connect che fanno un uso intensivo dei token di connessione, inclusi quelli rappresentati come JWT Un token di connessione è un token di sicurezza leggero che consente al "portatore" di accedere a una risorsa protetta. In questo senso, per "portatore" si intende qualsiasi parte che sia in grado di presentare il token. Anche se il rilascio del token di connessione è condizionato dal completamento del processo di autenticazione in Azure AD, se non vengono adottate le misure necessarie per proteggere il token durante la trasmissione e l'archiviazione, è possibile che venga intercettato e usato da parti non autorizzate. Molti token di sicurezza hanno meccanismi integrati per prevenire l'uso non autorizzato, ma i token di connessione ne sono sprovvisti e devono essere trasportati su un canale protetto, ad esempio Transport Layer Security (HTTPS). Se un token di connessione viene trasmesso senza essere protetto, un utente malintenzionato potrebbe usare un attacco "man in the middle" per acquisire il token e usarlo per l'accesso non autorizzato a una risorsa protetta. Gli stessi principi di sicurezza si applicano quando un token di connessione viene archiviato o memorizzato nella cache per un uso futuro. Assicurarsi sempre che l'applicazione trasmetta ed archivi i token di connessione in modo sicuro. Per altre considerazioni sulla sicurezza dei token di connessione, vedere la [sezione 5 della specifica RFC 6750](http://tools.ietf.org/html/rfc6750).
> 
> 

Dopo avere delineato i concetti di base, è possibile passare alle sezioni seguenti per comprendere il funzionamento del provisioning in Azure AD e gli scenari comuni supportati da Azure AD.

## <a name="claims-in-azure-ad-security-tokens"></a>Attestazioni nei token di sicurezza di Azure AD
I token di sicurezza emessi da Azure AD contengono attestazioni o asserzioni di informazioni sull'oggetto autenticato. L'applicazione può usare tali attestazioni per varie attività. Ad esempio, possono essere usate per convalidare il token, identificare il tenant di directory dell'oggetto, visualizzare informazioni relative all'utente, determinare l'autorizzazione dell'oggetto e così via. Le attestazioni presenti in un determinato token di sicurezza dipendono dal tipo di token, dal tipo di credenziali usate per autenticare l'utente e dalla configurazione dell'applicazione. La tabella seguente fornisce una breve descrizione dei tipi di attestazione generati da Azure AD. Per altre informazioni, fare riferimento a [Token e tipi di attestazioni supportati](active-directory-token-and-claims.md).

| Attestazione | Descrizione |
| --- | --- |
| ID applicazione |Identifica l'applicazione che usa il token. |
| Destinatari |Identifica la risorsa di destinazione del token. |
| Riferimento alla classe contesto di autenticazione applicazione |Indica la modalità di autenticazione del client (client pubblico e client riservato). |
| Istante di autenticazione |Registra la data e l'ora in cui è avvenuta l'autenticazione. |
| Metodo di autenticazione |Indica la modalità di autenticazione dell'oggetto del token (password, certificato e così via). |
| Nome |Fornisce il nome dell'utente come è impostato in Azure AD. |
| Gruppi |Contiene gli ID oggetto dei gruppi di Azure AD di cui l'utente è membro. |
| Provider di identità |Registra il provider di identità che ha autenticato l'oggetto del token. |
| Issued At |Registra l'ora in cui il token è stato emesso. Spesso usata per l'aggiornamento del token. |
| Issuer |Identifica il servizio token di sicurezza che ha emesso il token, nonché il tenant di Azure AD. |
| Cognome |Fornisce il cognome dell'utente come è impostato in Azure AD. |
| Nome |Fornisce un valore leggibile che identifica l'oggetto del token. |
| ID oggetto |Contiene un identificatore univoco e non modificabile dell'oggetto in Azure AD. |
| Ruoli |Contiene i nomi descrittivi dei ruoli applicazione di Azure AD concessi all'utente. |
| Scope |Indica le autorizzazioni concesse all'applicazione client. |
| Oggetto |Indica l'entità su cui il token rilascia informazioni. |
| ID tenant |Contiene un identificatore univoco e non modificabile del tenant di directory che ha emesso il token. |
| Durata del token |Definisce l'intervallo di tempo entro il quale un token è valido. |
| Nome dell'entità utente |Contiene il nome dell'entità utente dell'oggetto. |
| Versione |Contiene il numero di versione del token. |

## <a name="basics-of-registering-an-application-in-azure-ad"></a>Nozioni di base sulla registrazione di un'applicazione in Azure AD 
Le applicazioni che usano l'outsourcing per l'autenticazione ad Azure AD devono essere registrate in una directory. Questo passaggio include la comunicazione ad Azure AD delle informazioni sull'applicazione, compresi l'URL in cui si trova, l'URL per l'invio delle risposte dopo l'autenticazione, l'URI per identificare l'applicazione e così via. Queste informazioni sono necessarie per alcune ragioni fondamentali:

* Azure AD ha bisogno delle coordinate per comunicare con l'applicazione durante la gestione dell'accesso o lo scambio di token, tra cui:
  
  * URI ID applicazione: l'identificatore di un'applicazione. Questo valore viene inviato ad Azure AD durante l'autenticazione per specificare le applicazioni per cui il chiamante richiede un token. Questo valore è inoltre incluso nel token per indicare all'applicazione che era la destinazione prevista.
  * URL di risposta e URI di reindirizzamento: nel caso di un'API Web o di un'applicazione Web, l'URL di risposta è il percorso a cui Azure AD invierà la risposta di autenticazione, compreso un token se l'autenticazione è riuscita. Nel caso di un'applicazione nativa, l'URI di reindirizzamento è un identificatore univoco a cui Azure AD reindirizzerà l'agente utente in una richiesta OAuth 2.0.
  * ID applicazione: l'ID di un'applicazione, che viene generato da Azure AD quando l'applicazione viene registrata. Quando viene richiesto un codice di autorizzazione o token, l'ID applicazione e la chiave vengono inviati ad Azure AD durante l'autenticazione.
  * Chiave: la chiave inviata insieme a un ID applicazione durante l'autenticazione ad Azure AD per chiamare un'API Web.
* Azure AD deve assicurarsi che l'applicazione disponga delle autorizzazioni necessarie per accedere ai dati della directory, ad altre applicazioni nell'organizzazione e così via.

Per comprendere meglio il provisioning, è utile chiarire che esistono due categorie di applicazioni che possono essere sviluppate e integrate con Azure AD:

* Applicazione con un singolo tenant: un'applicazione con un singolo tenant è destinata all'uso in una organizzazione. In genere si tratta di applicazioni line-of-business scritte da uno sviluppatore aziendale. Un'applicazione con un singolo tenant deve essere accessibile solo agli utenti di una directory e quindi è necessario eseguirne il provisioning in una sola directory. Queste applicazioni sono generalmente registrate da uno sviluppatore dell'organizzazione.
* Applicazione multi-tenant: un'applicazione multi-tenant è destinata all'uso in molte organizzazioni, non solo in una. Si tratta generalmente di applicazioni SaaS (Software-as-a-Service) scritte da un fornitore di software indipendente (ISV). È necessario eseguire il provisioning delle applicazioni multi-tenant in ogni directory in cui verranno usate ed è richiesto il consenso dell'utente o dell'amministratore per registrarle. Questo processo di consenso inizia quando un'applicazione viene registrata nella directory e può accedere all'API Graph o magari a un'altra API Web. Quando un utente o amministratore di un'altra organizzazione esegue l'accesso per usare l'applicazione, viene visualizzata una finestra di dialogo in cui sono indicate le autorizzazioni richieste dall'applicazione. L'utente o amministratore può quindi concedere il consenso all'applicazione, in modo da fornirle l'accesso ai dati indicati e infine di registrarla nella propria directory. Per altre informazioni, vedere [Panoramica del framework di consenso](active-directory-integrating-applications.md#overview-of-the-consent-framework).

Quando si sviluppa un'applicazione multi-tenant invece di un'applicazione con un singolo tenant, occorre considerare qualche altro aspetto. Ad esempio, se l'applicazione viene resa disponibile agli utenti in più directory, è necessario un meccanismo per determinare il tenant attivo. Un'applicazione con un singolo tenant deve solo cercare un utente nella propria directory, mentre un'applicazione multi-tenant deve identificare un utente specifico in tutte le directory in Azure AD. A questo scopo, Azure AD fornisce un endpoint di autenticazione comune in cui qualsiasi applicazione multi-tenant può indirizzare le richieste di accesso, invece di un endpoint specifico di un tenant. Questo endpoint è https://login.microsoftonline.com/common per tutte le directory in Azure AD, mentre un endpoint specifico del tenant potrebbe essere https://login.microsoftonline.com/contoso.onmicrosoft.com. L'endpoint comune deve essere valutato con particolare attenzione durante lo sviluppo di un'applicazione, perché occorrerà la logica necessaria per gestire più tenant durante l'accesso, la disconnessione e la convalida dei token.

Se si sviluppa un'applicazione con un singolo tenant, ma si intende metterla a disposizione di molte organizzazioni, è possibile modificare facilmente l'applicazione e la relativa configurazione in Azure AD per fare in modo che supporti più tenant. Azure AD usa inoltre la stessa chiave di firma per tutti i token in tutte le directory, a prescindere dal fatto che si fornisca l'autenticazione in un'applicazione con un singolo tenant o multi-tenant.

Ogni scenario indicati in questo documento include una sottosezione in cui sono descritti i relativi requisiti di provisioning. Per informazioni più dettagliate sul provisioning di un'applicazione in Azure AD e sulle differenze tra applicazioni con un singolo tenant e applicazioni multi-tenant, vedere [Applicazioni di integrazione con Azure Active Directory](active-directory-integrating-applications.md) . Di seguito sono illustrati gli scenari di applicazione più comuni in Azure AD.

## <a name="application-types-and-scenarios"></a>Scenari e tipi di applicazione
Tutti gli scenari descritti in questo documento possono essere sviluppati con vari linguaggi e piattaforme. Sono tutti supportati da esempi di codice completo disponibili nella [guida agli esempi di codice](active-directory-code-samples.md) o direttamente dal corrispondente [archivio di esempio GitHub](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=active-directory). Inoltre, se l'applicazione necessita di una parte o di un segmento specifico di uno scenario end-to-end, nella maggior parte dei casi è possibile aggiungere tale funzionalità in modo indipendente. Ad esempio, se si ha un'applicazione nativa che chiama un'API Web, è possibile aggiungere facilmente un'applicazione Web che chiama la stessa API Web. Il diagramma seguente illustra questi scenari e tipi di applicazione e viene indicato come aggiungere i vari componenti:

![Scenari e tipi di applicazione](./media/active-directory-authentication-scenarios/application_types_and_scenarios.png)

Azure AD supporta i cinque scenari di applicazione principali descritti di seguito:

* [Da Web browser ad applicazione Web](#web-browser-to-web-application): un'utente deve accedere a un'applicazione Web protetta da Azure AD.
* [Applicazione a pagina singola](#single-page-application-spa): un'utente deve accedere a un'applicazione a pagina singola protetta da Azure AD.
* [Da applicazione nativa ad API Web](#native-application-to-web-api): un'applicazione nativa in esecuzione su un telefono, tablet o PC deve autenticare un utente per ottenere risorse da un'API Web protetta da Azure AD.
* [Da applicazione Web ad API Web](#web-application-to-web-api): un'applicazione Web deve ottenere risorse da un'API Web protetta da Azure AD.
* [Da daemon o applicazione server ad API Web](#daemon-or-server-application-to-web-api): un'applicazione daemon o un'applicazione server priva di interfaccia utente Web deve ottenere risorse da un'API Web protetta da Azure AD.

### <a name="web-browser-to-web-application"></a>Da Web browser ad applicazione Web
Questa sezione descrive un'applicazione che autentica un utente in un Web browser per un'applicazione Web. In questo scenario l'applicazione Web indirizza il browser dell'utente per l'accesso ad Azure AD. Tramite il browser dell'utente Azure AD restituisce una risposta di accesso che contiene attestazioni sull'utente in un token di sicurezza. Questo scenario supporta l'accesso mediante i protocolli WS-Federation, SAML 2.0 e OpenID Connect.

#### <a name="diagram"></a>Diagramma
![Flusso di autenticazione da browser ad applicazione Web](./media/active-directory-authentication-scenarios/web_browser_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Descrizione del flusso del protocollo
1. Quando un utente visita l'applicazione e deve eseguire l'accesso, viene reindirizzato tramite una richiesta di accesso all'endpoint di autenticazione in Azure AD.
2. L'utente accede nella pagina di accesso.
3. Se l'autenticazione riesce, Azure AD crea un token di autenticazione e restituisce una risposta di accesso all'URL di risposta dell'applicazione configurato nel Portale di Azure. Per un'applicazione di produzione, questo URL di risposta deve essere HTTPS. Il token restituito include le attestazioni sull'utente e Azure AD richieste dall'applicazione per convalidare il token.
4. L'applicazione convalida il token usando le informazioni sulla chiave di firma pubblica e sull'autorità emittente disponibili nel documento metadati federazione per Azure AD. Dopo la convalida del token da parte dell'applicazione, Azure AD avvia una nuova sessione con l'utente. La sessione consente all'utente di accedere all'applicazione fino alla scadenza.

#### <a name="code-samples"></a>Esempi di codice
Vedere gli esempi di codice per gli scenari Da Web Browser ad applicazione Web. Consultare spesso questa pagina perché vengono aggiunti regolarmente nuovi esempi. [Da Web browser ad applicazione Web](active-directory-code-samples.md#web-browser-to-web-application).

#### <a name="registering"></a>Registrazione
* Singolo tenant: se si crea un'applicazione solo per la propria organizzazione, è necessario registrarla nella directory aziendale tramite il Portale di Azure.
* Multi-tenant: se si crea un'applicazione che può essere usata da utenti esterni all'organizzazione, è necessario registrarla nella directory aziendale, ma anche nella directory delle singole organizzazioni che useranno l'applicazione. Per rendere disponibile l'applicazione nella propria directory, è possibile includere un processo di accesso per i clienti che permetta loro di concedere il consenso all'applicazione. Al momento dell'iscrizione all'applicazione, viene visualizzata una finestra di dialogo in cui sono indicate le autorizzazioni richieste dall'applicazione e quindi viene presentata l'opzione per il consenso. A seconda delle autorizzazioni richieste, è possibile che il consenso debba essere fornito da un amministratore dell'altra organizzazione. Quando l'utente o l'amministratore acconsente, l'applicazione viene registrata nella directory. Per ulteriori informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Scadenza del token
La sessione dell'utente scade al termine del periodo di validità del token emesso da Azure AD. Se necessario, l'applicazione può abbreviare questo periodo di validità, ad esempio disconnettendo gli utenti dopo un determinato tempo di inattività. Quando la sessione scade, all'utente viene chiesto di accedere di nuovo.

### <a name="single-page-application-spa"></a>Applicazione a singola pagina (SPA)
Questa sezione descrive l'autenticazione per un'applicazione a pagina singola che utilizza Azure AD e la concessione di autorizzazione implicita OAuth 2.0 per proteggere il back-end dell'API Web. Le applicazioni a singola pagina sono in genere strutturate come un livello di presentazione JavaScript (front-end) eseguito nel browser e un back-end dell'API Web eseguito in un server e che implementa la logica di business dell'applicazione. Per ulteriori informazioni sulla concessione di autorizzazione implicita e per stabilire se sia adatta allo scenario della propria applicazione, vedere [Informazioni sul flusso di concessione implicita OAuth2 in Azure Active Directory](active-directory-dev-understanding-oauth2-implicit-grant.md).

In questo scenario, quando l'utente accede, il front-end JavaScript utilizza [Active Directory Authentication Library per JavaScript (ADAL.JS)](https://github.com/AzureAD/azure-activedirectory-library-for-js/tree/dev) e la concessione di autorizzazione implicita per ottenere un token ID (id_token) da Azure AD. Il token viene memorizzato nella cache e il client lo associa alla richiesta come token di connessione quando si effettuano chiamate al relativo back-end dell'API Web, protetto tramite il middleware OWIN. 

#### <a name="diagram"></a>Diagramma
![Diagramma Applicazione a singola pagina (SPA)](./media/active-directory-authentication-scenarios/single_page_app.png)

#### <a name="description-of-protocol-flow"></a>Descrizione del flusso del protocollo
1. L'utente passa all'applicazione Web.
2. L'applicazione restituisce al browser il front-end JavaScript (livello di presentazione).
3. L'utente avvia l'accesso, ad esempio facendo clic su un collegamento di accesso. Il browser invia una richiesta GET all'endpoint di autorizzazione di Azure AD per richiedere un token ID. Nei parametri di query di questa richiesta sono inclusi l'URL di risposta e l'ID applicazione.
4. Azure AD convalida l'URL di risposta confrontandolo con l'URL di risposta registrato, configurato nel Portale di Azure.
5. L'utente accede nella pagina di accesso.
6. Se l'autenticazione riesce, Azure AD crea un token ID e lo restituisce come frammento di URL (#) all'URL di risposta dell'applicazione. Per un'applicazione di produzione, questo URL di risposta deve essere HTTPS. Il token restituito include le attestazioni sull'utente e Azure AD richieste dall'applicazione per convalidare il token.
7. Il codice client di JavaScript in esecuzione nel browser estrae il token dalla risposta per usarlo nella protezione delle chiamate al back-end dell'API Web dell'applicazione.
8. Il browser chiama il back-end dell'API Web dell'applicazione con il token di accesso nell'intestazione dell'autorizzazione.

#### <a name="code-samples"></a>Esempi di codice
Vedere gli esempi di codice per gli scenari di applicazioni a singola pagina (SPA). Assicurarsi di consultare spesso questa pagina perché vengono aggiunti regolarmente nuovi esempi. [Applicazione a singola pagina (SPA)](active-directory-code-samples.md#single-page-application-spa).

#### <a name="registering"></a>Registrazione
* Singolo tenant: se si crea un'applicazione solo per la propria organizzazione, è necessario registrarla nella directory aziendale tramite il Portale di Azure.
* Multi-tenant: se si crea un'applicazione che può essere usata da utenti esterni all'organizzazione, è necessario registrarla nella directory aziendale, ma anche nella directory delle singole organizzazioni che useranno l'applicazione. Per rendere disponibile l'applicazione nella propria directory, è possibile includere un processo di accesso per i clienti che permetta loro di concedere il consenso all'applicazione. Al momento dell'iscrizione all'applicazione, viene visualizzata una finestra di dialogo in cui sono indicate le autorizzazioni richieste dall'applicazione e quindi viene presentata l'opzione per il consenso. A seconda delle autorizzazioni richieste, è possibile che il consenso debba essere fornito da un amministratore dell'altra organizzazione. Quando l'utente o l'amministratore acconsente, l'applicazione viene registrata nella directory. Per ulteriori informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](active-directory-integrating-applications.md).

Dopo la registrazione dell'applicazione, è necessario configurarla per l'uso del protocollo di concessione implicita OAuth 2.0. Per impostazione predefinita, questo protocollo è disabilitato per le applicazioni. Per abilitare il protocollo di concessione implicita OAuth2 per l'applicazione, modificare il manifesto dell'applicazione dal Portale di Azure e impostare il valore "oauth2AllowImplicitFlow" su vero. Per istruzioni dettagliare, vedere la sezione relativa all' [abilitazione della concessione implicita OAuth 2.0 per le applicazioni a singola pagina](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Scadenza del token
Quando si usa ADAL.js per gestire l'autenticazione con Azure AD, è possibile sfruttare i vantaggi offerti da varie funzionalità che facilitano l'aggiornamento di un token scaduto e consentono di ottenere token per risorse API Web aggiuntive che possono essere chiamate dall'applicazione. Se l'utente viene autenticato correttamente con Azure AD, per tale utente viene stabilita una sessione protetta da un cookie tra il browser e Azure AD. Tenere presente che la sessione esiste tra l'utente e Azure AD e non tra l'utente e l'applicazione Web in esecuzione sul server. Quando un token scade, ADAL.js usa questa sessione per ottenere automaticamente un altro token. Ciò avviene usando un iFrame nascosto per inviare e ricevere la richiesta tramite il protocollo di concessione implicita OAuth. ADAL.js può anche usare lo stesso meccanismo per ottenere automaticamente i token di accesso da Azure AD per altre risorse API Web chiamate dall'applicazione, purché queste risorse supportino la condivisione di risorse tra le origini (CORS), siano registrate nella directory dell'utente e sia stato assegnato dall'utente qualsiasi consenso richiesto durante l'accesso.

### <a name="native-application-to-web-api"></a>Da applicazione nativa ad API Web
Questa sezione descrive un'applicazione nativa che chiama un'API Web per conto di un utente. Questo scenario è basato sul tipo di concessione del codice di autorizzazione OAuth 2.0 con un client pubblico, come descritto nella sezione 4.1 della [specifica OAuth 2.0](http://tools.ietf.org/html/rfc6749). L'applicazione nativa ottiene un token di accesso per l'utente tramite il protocollo OAuth 2.0. Questo token di accesso viene quindi inviato nella richiesta all'API Web, che autorizza l'utente e restituisce la risorsa desiderata.

#### <a name="diagram"></a>Diagramma
![Diagramma Da applicazione nativa ad API Web](./media/active-directory-authentication-scenarios/native_app_to_web_api.png)

#### <a name="authentication-flow-for-native-application-to-api"></a>Flusso di autenticazione all'API per l'applicazione nativa
#### <a name="description-of-protocol-flow"></a>Descrizione del flusso del protocollo
Se si usano le librerie di autenticazione AD, la maggior parte dei dettagli del protocollo descritti di seguito viene gestita automaticamente, ad esempio i popup del browser, la memorizzazione dei token nella cache e la gestione dei token di aggiornamento.

1. Con un popup del browser l'applicazione nativa invia una richiesta all'endpoint di autorizzazione in Azure AD. Questa richiesta include l'ID applicazione e l'URI di reindirizzamento dell'applicazione nativa, come illustrato nel Portale di Azure, nonché l'URI ID dell'applicazione per l'API Web. Se l'utente non ha ancora eseguito l'accesso, verrà richiesto di accedere di nuovo.
2. Azure AD autentica l'utente. Se si tratta di un'applicazione multi-tenant il cui uso richiede il consenso, verrà chiesto all'utente di fornire il consenso, se non l'ha già fatto. Dopo che è stato fornito il consenso e l'autenticazione riesce, Azure AD restituisce un codice di autenticazione all'URI di reindirizzamento dell'applicazione client.
3. Quando Azure AD restituisce un codice di autorizzazione all'URI di reindirizzamento, l'applicazione interrompe l'interazione con il browser ed estrae il codice di autorizzazione dalla risposta. Con questo codice di autorizzazione l'applicazione client invia una richiesta all'endpoint token di Azure AD che include il codice di autorizzazione, dettagli sull'applicazione client (ID applicazione e URI di reindirizzamento) e la risorsa desiderata (URI ID applicazione per l'API Web).
4. Il codice di autorizzazione e le informazioni sull'applicazione client e l'API Web vengono convalidati da Azure AD. Se la convalida riesce, Azure AD restituisce tue token: un token di accesso JWT e un token di aggiornamento JWT. Azure AD restituisce anche informazioni di base sull'utente, ad esempio il nome visualizzato e l'ID tenant.
5. Su HTTPS l'applicazione client usa il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Bearer" nell'intestazione dell'autorizzazione della richiesta all'API Web. L'API Web convalida quindi il token JWT e, se la convalida riesce, restituisce la risorsa desiderata.
6. Alla scadenza del token di accesso l'applicazione client riceverà un errore che indica che l'utente deve ripetere il processo di autenticazione. Se l'applicazione ha un token di aggiornamento valido, può essere usato per acquisire un nuovo token di accesso senza richiedere all'utente di ripetere l'accesso. Se il token di aggiornamento scade, l'applicazione dovrà autenticare di nuovo l'utente in modo interattivo.

> [!NOTE]
> Il token di aggiornamento emesso da Azure AD può essere usato per accedere a più risorse. Ad esempio, se un'applicazione client è autorizzata a chiamare due API Web, il token di aggiornamento può essere usato per ottenere un token di accesso anche all'altra API Web.
> 
> 

#### <a name="code-samples"></a>Esempi di codice
Vedere gli esempi di codice per gli scenari Da applicazione nativa ad API Web. Consultare spesso questa pagina perché vengono aggiunti regolarmente nuovi esempi. [Da applicazione nativa ad API Web](active-directory-code-samples.md#native-application-to-web-api).

#### <a name="registering"></a>Registrazione
* Singolo tenant: l'applicazione nativa e l'API Web devono essere registrate nella stessa directory in Azure AD. L'API Web può essere configurata per esporre un set di autorizzazioni, che vengono usate per limitare l'accesso dell'applicazione nativa alle relative risorse. L'applicazione client seleziona quindi le autorizzazioni desiderate dal menu a discesa "Autorizzazioni per altre applicazioni" nel Portale di Azure.
* Multi-tenant: prima di tutto, l'applicazione nativa è stata registrata solo nella directory dello sviluppatore o dell'editore. Quindi, l'applicazione nativa viene configurata per indicare le autorizzazioni necessarie per il funzionamento. Questo elenco di autorizzazioni richieste viene visualizzato in una finestra di dialogo quando un utente o amministratore nella directory di destinazione concede il consenso all'applicazione, rendendola disponibile per la propria organizzazione. Alcune applicazioni richiedono solo autorizzazioni a livello utente, che possono essere concesse da qualsiasi utente dell'organizzazione. Altre applicazioni richiedono autorizzazioni a livello amministratore, che non possono essere concesse dagli utenti dell'organizzazione. Solo un amministratore di directory può concedere il consenso alle applicazioni che richiedono questo livello di autorizzazione. Quando l'utente o l'amministratore acconsente, solo l'API Web viene registrata nella directory. Per ulteriori informazioni, vedere [Integrazione di applicazioni con Azure Active Directory](active-directory-integrating-applications.md).

#### <a name="token-expiration"></a>Scadenza del token
Quando l'applicazione nativa usa il proprio codice di autorizzazione per ottenere un token di accesso JWT, riceve anche un token di aggiornamento JWT. Quando il token di accesso scade, è possibile usare il token di aggiornamento per autenticare di nuovo l'utente senza richiedere di ripetere l'accesso. Questo token di aggiornamento viene quindi usato per autenticare l'utente, con conseguente emissione di un nuovo token di accesso e un nuovo token di aggiornamento.

### <a name="web-application-to-web-api"></a>Da applicazione Web ad API Web
Questa sezione descrive un'applicazione Web che deve ottenere risorse da un'API Web. In questo scenario esistono due tipi di identità che l'applicazione Web può usare per autenticare e chiamare l'API Web: un'identità applicazione e un'identità utente delegato.

*Identità applicazione:* questo scenario usa la concessione di credenziali client OAuth 2.0 per l'autenticazione come applicazione e l'accesso all'API Web. Quando si usa un'identità applicazione, l'API Web può solo rilevare la chiamata dell'applicazione Web, perché non riceve informazioni relative all'utente. Se l'applicazione riceve informazioni relative all'utente, queste vengono inviate tramite il protocollo applicativo e non sono firmate da Azure AD. L'API Web confida che l'applicazione Web abbia autenticato l'utente. Per questo motivo il modello è definito sottosistema attendibile.

*Identità utente delegato:* lo scenario può essere eseguito in due modi, ovvero OpenID Connect e concessione di codice di autorizzazione OAuth 2.0 con un client riservato. L'applicazione Web ottiene un token di accesso per l'utente, dimostrando all'API Web che l'utente è stato autenticato nell'applicazione Web e che l'applicazione Web è riuscita a ottenere un'identità utente delegato per chiamare l'API Web. Questo token di accesso viene inviato nella richiesta all'API Web, che autorizza l'utente e restituisce la risorsa desiderata.

#### <a name="diagram"></a>Diagramma
![Diagramma Da applicazione Web ad API Web](./media/active-directory-authentication-scenarios/web_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Descrizione del flusso del protocollo
Nel flusso che segue sono illustrati i tipi di identità applicazione e identità utente delegato. La differenza fondamentale consiste nel fatto che l'identità utente delegato deve acquisire un codice di autorizzazione prima che l'utente possa ottenere l'accesso all'API Web.

##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identità applicazione con concessione delle credenziali client OAuth 2.0
1. Un utente esegue l'accesso ad Azure AD nell'applicazione Web (vedere la sezione precedente [Da Web browser ad applicazione Web](#web-browser-to-web-application) ).
2. L'applicazione Web deve acquisire un token di accesso per l'autenticazione nell'API Web e il recupero della risorsa desiderata. Esegue una richiesta all'endpoint token di Azure AD, fornendo credenziali, ID applicazione e URI ID applicazione dell'API Web.
3. Azure AD autentica l'applicazione e restituisce un token di accesso JWT usato per chiamare l'API Web.
4. Su HTTPS l'applicazione Web usa il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Bearer" nell'intestazione dell'autorizzazione della richiesta all'API Web. L'API Web convalida quindi il token JWT e, se la convalida riesce, restituisce la risorsa desiderata.

##### <a name="delegated-user-identity-with-openid-connect"></a>Identità utente delegato con OpenID Connect
1. Un utente esegue l'accesso all'applicazione Web usando Azure AD (vedere la sezione precedente [Da Web browser ad applicazione Web](#web-browser-to-web-application) ). Se l'utente dell'applicazione Web non ha ancora concesso il consenso perché l'applicazione Web chiami l'API Web per suo conto, dovrà acconsentire. L'applicazione visualizzerà le autorizzazioni richieste e, se si tratta di autorizzazione a livello amministratore, un utente normale della directory non potrà concedere il consenso. Questo processo di consenso è valido solo per le applicazioni multi-tenant, non per le applicazioni con un singolo tenant, perché l'applicazione avrà già le autorizzazioni necessarie. Quando l'utente ha eseguito l'accesso, l'applicazione Web ha ricevuto un token ID con le informazioni relative all'utente, nonché un codice di autorizzazione.
2. Con il codice di autorizzazione rilasciato da Azure AD, l'applicazione Web invia una richiesta all'endpoint token di Azure AD che include il codice di autorizzazione, dettagli sull'applicazione client (ID applicazione e URI di reindirizzamento) e la risorsa desiderata (URI ID applicazione per l'API Web).
3. Il codice di autorizzazione e le informazioni sull'applicazione Web e l'API Web vengono convalidati da Azure AD. Se la convalida riesce, Azure AD restituisce tue token: un token di accesso JWT e un token di aggiornamento JWT.
4. Su HTTPS l'applicazione Web usa il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Bearer" nell'intestazione dell'autorizzazione della richiesta all'API Web. L'API Web convalida quindi il token JWT e, se la convalida riesce, restituisce la risorsa desiderata.

##### <a name="delegated-user-identity-with-oauth-20-authorization-code-grant"></a>Identità utente delegato con concessione del codice di autorizzazione OAuth 2.0
1. Un utente ha già eseguito l'accesso all'applicazione Web, il cui meccanismo di autenticazione è indipendente da Azure AD.
2. L'applicazione Web richiede un codice di autorizzazione per acquisire un token di accesso, quindi invia una richiesta tramite il browser all'endpoint di autorizzazione di Azure AD, fornendo l'ID applicazione e l'URI di reindirizzamento per l'applicazione Web al termine dell'autenticazione. L'utente accede ad Azure AD.
3. Se l'utente dell'applicazione Web non ha ancora concesso il consenso perché l'applicazione Web chiami l'API Web per suo conto, dovrà acconsentire. L'applicazione visualizzerà le autorizzazioni richieste e, se si tratta di autorizzazione a livello amministratore, un utente normale della directory non potrà concedere il consenso. Questo processo di consenso è valido solo per le applicazioni multi-tenant, non per le applicazioni con un singolo tenant, perché l'applicazione avrà già le autorizzazioni necessarie.
4. Quando l'utente concede il consenso, l'applicazione Web riceve il codice di autorizzazione necessario per acquisire un token di accesso.
5. Con il codice di autorizzazione rilasciato da Azure AD, l'applicazione Web invia una richiesta all'endpoint token di Azure AD che include il codice di autorizzazione, dettagli sull'applicazione client (ID applicazione e URI di reindirizzamento) e la risorsa desiderata (URI ID applicazione per l'API Web).
6. Il codice di autorizzazione e le informazioni sull'applicazione Web e l'API Web vengono convalidati da Azure AD. Se la convalida riesce, Azure AD restituisce tue token: un token di accesso JWT e un token di aggiornamento JWT.
7. Su HTTPS l'applicazione Web usa il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Bearer" nell'intestazione dell'autorizzazione della richiesta all'API Web. L'API Web convalida quindi il token JWT e, se la convalida riesce, restituisce la risorsa desiderata.

#### <a name="code-samples"></a>Esempi di codice
Vedere gli esempi di codice per gli scenari Da applicazione Web ad API Web. Consultare spesso questa pagina perché vengono aggiunti regolarmente nuovi esempi. [Da applicazione Web ad API Web](active-directory-code-samples.md#web-application-to-web-api).

#### <a name="registering"></a>Registrazione
* Singolo tenant: in entrambi i casi (identità applicazione e identità utente delegato), l'applicazione Web e l'API Web devono essere registrate nella stessa directory in Azure AD. L'API Web può essere configurata per esporre un set di autorizzazioni, che vengono usate per limitare l'accesso dell'applicazione Web alle relative risorse. Se viene usato il tipo di identità utente delegato, l'applicazione Web deve selezionare le autorizzazioni desiderate dal menu a discesa "Autorizzazioni per altre applicazioni" nel Portale di Azure. Questo passaggio non è necessario se viene usato il tipo di identità applicazione.
* Multi-tenant: per prima cosa, l'applicazione Web viene configurata per indicare le autorizzazioni necessarie per il funzionamento. Questo elenco di autorizzazioni richieste viene visualizzato in una finestra di dialogo quando un utente o amministratore nella directory di destinazione concede il consenso all'applicazione, rendendola disponibile per la propria organizzazione. Alcune applicazioni richiedono solo autorizzazioni a livello utente, che possono essere concesse da qualsiasi utente dell'organizzazione. Altre applicazioni richiedono autorizzazioni a livello amministratore, che non possono essere concesse dagli utenti dell'organizzazione. Solo un amministratore di directory può concedere il consenso alle applicazioni che richiedono questo livello di autorizzazione. Quando l'utente o l'amministratore acconsente, l'applicazione Web e l'API Web vengono registrate nella directory.

#### <a name="token-expiration"></a>Scadenza del token
Quando l'applicazione Web usa il proprio codice di autorizzazione per ottenere un token di accesso JWT, riceve anche un token di aggiornamento JWT. Quando il token di accesso scade, è possibile usare il token di aggiornamento per autenticare di nuovo l'utente senza richiedere di ripetere l'accesso. Questo token di aggiornamento viene quindi usato per autenticare l'utente, con conseguente emissione di un nuovo token di accesso e un nuovo token di aggiornamento.

### <a name="daemon-or-server-application-to-web-api"></a>Da daemon o applicazione server ad API Web
Questa sezione descrive un'applicazione daemon o server che deve ottenere risorse da un'API Web. A questa sezione sono applicabili due sottoscenari: un'applicazione daemon che deve chiamare un'API Web, basata sul tipo di concessione di credenziali client OAuth 2.0, e un'applicazione server (ad esempio un'API Web) che deve chiamare un'API Web, basata sulla bozza di specifica OAuth 2.0 On-Behalf-Of.

Per lo scenario in cui un'applicazione daemon deve chiamare un'API Web, è importante comprendere alcuni aspetti. Prima di tutto, l'interazione utente non è possibile con un'applicazione daemon, che richiede che l'applicazione abbia una propria identità. Un esempio di applicazione daemon è un processo batch oppure un servizio del sistema operativo in esecuzione in background. Questo tipo di applicazione richiede un token di accesso usando la relativa identità applicazione e presentando l'ID applicazione, le credenziali (password o certificato) e l'URI ID dell'applicazione ad Azure AD. Dopo l'autenticazione, il daemon riceve un token di accesso da Azure AD, che viene quindi usato per chiamare l'API Web.

Per lo scenario in cui un'applicazione server deve chiamare un'API Web, è utile usare un esempio. Si supponga che un utente abbia eseguito l'autenticazione in un'applicazione nativa e che questa abbia l'esigenza di chiamare un'API Web. Azure AD rilascia un token di accesso JWT per chiamare l'API Web. Se l'API Web deve chiamare un'altra API Web a valle, può usare il flusso "on-behalf-of" per delegare l'identità dell'utente ed eseguire l'autenticazione nell'API Web di secondo livello.

#### <a name="diagram"></a>Diagramma
![Diagramma Da daemon o applicazione server ad API Web](./media/active-directory-authentication-scenarios/daemon_server_app_to_web_api.png)

#### <a name="description-of-protocol-flow"></a>Descrizione del flusso del protocollo
##### <a name="application-identity-with-oauth-20-client-credentials-grant"></a>Identità applicazione con concessione delle credenziali client OAuth 2.0
1. Per prima cosa, l'applicazione server deve eseguire l'autenticazione in Azure AD senza intervento dell'utente, ad esempio senza finestra di dialogo di accesso interattiva. Esegue una richiesta all'endpoint token di Azure AD, fornendo credenziali, ID applicazione e URI ID applicazione.
2. Azure AD autentica l'applicazione e restituisce un token di accesso JWT usato per chiamare l'API Web.
3. Su HTTPS l'applicazione Web usa il token di accesso JWT restituito per aggiungere la stringa JWT con una designazione "Bearer" nell'intestazione dell'autorizzazione della richiesta all'API Web. L'API Web convalida quindi il token JWT e, se la convalida riesce, restituisce la risorsa desiderata.

##### <a name="delegated-user-identity-with-oauth-20-on-behalf-of-draft-specification"></a>Identità utente delegato con la bozza di specifica OAuth 2.0 On-Behalf-Of
Il flusso descritto di seguito presuppone che un utente abbia eseguito l'autenticazione in un'altra applicazione (ad esempio un'applicazione nativa) e che la relativa identità utente sia stata usata per acquisire un token di accesso all'API Web di primo livello.

1. L'applicazione nativa invia il token di accesso all'API Web di primo livello.
2. L'API Web di primo livello invia una richiesta all'endpoint token di Azure AD, inviando l'ID applicazione e le credenziali, oltre al token di accesso dell'utente. Inoltre, la richiesta viene inviata con un parametro on_behalf_of che indica che l'API Web richiede nuovi token per chiamare un'API Web a valle per conto dell'utente originale.
3. Azure AD verifica che l'API Web di primo livello sia autorizzata ad accedere all'API Web di secondo livello e convalidi la richiesta, restituendo un token di accesso JWT e un token di aggiornamento JWT all'API Web di primo livello.
4. Su HTTPS, l'API Web di primo livello chiama l'API Web di secondo livello aggiungendo la stringa del token all'intestazione dell'autorizzazione nella richiesta. L'API Web di primo livello può continuare a chiamare l'API Web di secondo livello finché il token di accesso e i token di aggiornamento sono validi.

#### <a name="code-samples"></a>Esempi di codice
Vedere gli esempi di codice per gli scenari Da applicazione daemon o server ad API Web. Consultare spesso questa pagina perché vengono aggiunti regolarmente nuovi esempi. [Applicazione server o daemon ad API Web](active-directory-code-samples.md#server-or-daemon-application-to-web-api)

#### <a name="registering"></a>Registrazione
* Singolo tenant: in entrambi i casi (identità applicazione e identità utente delegato), l'applicazione daemon o server deve essere registrata nella stessa directory in Azure AD. L'API Web può essere configurata per esporre un set di autorizzazioni, che vengono usate per limitare l'accesso del daemon o del server alle relative risorse. Se viene usato il tipo di identità utente delegato, l'applicazione server deve selezionare le autorizzazioni desiderate dal menu a discesa "Autorizzazioni per altre applicazioni" nel Portale di Azure. Questo passaggio non è necessario se viene usato il tipo di identità applicazione.
* Multi-tenant: per prima cosa, l'applicazione daemon o server viene configurata per indicare le autorizzazioni necessarie per il funzionamento. Questo elenco di autorizzazioni richieste viene visualizzato in una finestra di dialogo quando un utente o amministratore nella directory di destinazione concede il consenso all'applicazione, rendendola disponibile per la propria organizzazione. Alcune applicazioni richiedono solo autorizzazioni a livello utente, che possono essere concesse da qualsiasi utente dell'organizzazione. Altre applicazioni richiedono autorizzazioni a livello amministratore, che non possono essere concesse dagli utenti dell'organizzazione. Solo un amministratore di directory può concedere il consenso alle applicazioni che richiedono questo livello di autorizzazione. Quando l'utente o l'amministratore acconsente, entrambe le API Web vengono registrate nella directory.

#### <a name="token-expiration"></a>Scadenza del token
Quando la prima applicazione usa il proprio codice di autorizzazione per ottenere un token di accesso JWT, riceve anche un token di aggiornamento JWT. Quando il token di accesso scade, è possibile usare il token di aggiornamento per autenticare di nuovo l'utente senza richiedere l'immissione delle credenziali. Questo token di aggiornamento viene quindi usato per autenticare l'utente, con conseguente emissione di un nuovo token di accesso e un nuovo token di aggiornamento.

## <a name="see-also"></a>Vedere anche
[Guida per gli sviluppatori di Azure Active Directory](active-directory-developers-guide.md)

[Esempi di codice di Azure Active Directory](active-directory-code-samples.md)

[Informazioni importanti sul rollover della chiave di firma in Azure AD](active-directory-signing-key-rollover.md)

[OAuth 2.0 in Azure AD](https://msdn.microsoft.com/library/azure/dn645545.aspx)


