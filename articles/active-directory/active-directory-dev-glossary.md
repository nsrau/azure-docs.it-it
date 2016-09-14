<properties
   pageTitle="Glossario per gli sviluppatori di Azure Active Directory | Microsoft Azure"
   description="Elenco di termini relativi ai concetti e alle funzionalità per sviluppatori di uso comune di Azure Active Directory."
   services="active-directory"
   documentationCenter=""
   authors="bryanla"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/31/2016"
   ms.author="bryanla"/>

# Glossario per gli sviluppatori di Azure Active Directory
Questo articolo contiene le definizioni di alcuni dei concetti di base per gli sviluppatori di Azure Active Directory (AD), utili per imparare a sviluppare applicazioni per Azure AD.

## token di accesso 
Tipo di [token di sicurezza](#security-token) rilasciato da un [server di autorizzazione](#authorization-server) e usato da un'[applicazione client](#client-application) per accedere a un [server di risorse protette](#resource-server). Il token, in genere sotto forma di [token JSON Web (token JWT)][JWT], rappresenta l'autorizzazione concessa dal [proprietario delle risorse](#resource-owner) al client per un livello di accesso richiesto. Il token contiene tutte le [attestazioni](#claim) applicabili relative al soggetto e può essere usato dall'applicazione client come credenziale per accedere a una determinata risorsa. Il proprietario della risorsa non dovrà quindi esporre le credenziali al client.

I token di accesso sono talvolta definiti "utente + app" o "solo app", a seconda delle credenziali rappresentate. Ad esempio:

- Quando un'applicazione client usa la [concessione di autorizzazione "codice di autorizzazione"](#authorization-grant), l'utente finale esegue prima l'autenticazione come proprietario della risorsa, delegando al client l'autorizzazione ad accedere alla risorsa. Il client esegue successivamente l'autenticazione quando ottiene il token di accesso. Il token può talvolta essere definito più specificamente token "utente + app" perché rappresenta sia l'utente che ha autorizzato l'applicazione client che l'applicazione.
- Quando un'applicazione client usa la [concessione di autorizzazione "credenziali client"](#authorization-grant), il client fornisce la sola autenticazione e funziona senza l'autenticazione/autorizzazione del proprietario della risorsa. Il token può quindi essere definito talvolta token "solo app".

Per altri dettagli, vedere [Informazioni di riferimento sui token in Azure AD][AAD-Tokens-Claims].

## manifesto dell'applicazione  
Funzionalità offerta dal [portale di Azure classico][AZURE-classic-portal] che genera una rappresentazione JSON della configurazione di identità dell'applicazione e viene usata come meccanismo per aggiornare le entità [Application][AAD-Graph-App-Entity] e [ServicePrincipal][AAD-Graph-Sp-Entity] associate. Per altri dettagli, vedere [Informazioni sul manifesto dell'applicazione in Azure Active Directory][AAD-App-Manifest].

## oggetto applicazione  
Quando si registra/aggiorna un'applicazione nel [portale di Azure classico][AZURE-classic-portal], il portale crea/aggiorna sia un oggetto applicazione che un [oggetto entità servizio](#service-principal-object) corrispondente per il tenant. L'oggetto applicazione *definisce* la configurazione di identità dell'applicazione a livello globale (in tutti i tenant a cui ha accesso), offrendo un modello da cui *derivano* gli oggetti entità servizio corrispondenti usati in locale in fase di esecuzione (in uno specifico tenant).

Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory][AAD-App-SP-Objects].

## registrazione dell'applicazione  
Per consentire a un'applicazione l'integrazione con le funzioni di gestione delle identità e degli accessi e la relativa delega in Azure AD, è necessario che l'applicazione sia registrata in un [tenant](#tenant) di Azure AD. Quando si registra l'applicazione in Azure AD, si specifica una configurazione di identità per l'applicazione che ne consente l'integrazione con Azure AD e l'uso di funzionalità come le seguenti:

- Gestione affidabile dell'accesso Single Sign-On con l'implementazione del protocollo [OpenID Connect][OpenIDConnect] e la gestione delle identità di Azure AD
- Accesso negoziato a [risorse protette](#resource-server) da parte di [applicazioni client](#client-application), tramite l'implementazione del [server di autorizzazione](#authorization-server) OAuth 2.0 di Azure AD
- [Framework di consenso](#consent) per la gestione dell'accesso client alle risorse protette, in base all'autorizzazione del proprietario delle risorse

Per altri dettagli, vedere [Integrazione di applicazioni con Azure Active Directory][AAD-Integrating-Apps].

## autenticazione
Richiesta di credenziali legittime a una parte, che costituisce la base per la creazione di un'entità di sicurezza da usare per il controllo delle identità e di accesso. Durante una [concessione di autorizzazione OAuth2](#authorization-grant), ad esempio, la parte che esegue l'autenticazione svolge il ruolo di [proprietario delle risorse](#resource-owner) o di [applicazione client](#client-application) a seconda della concessione usata.

## autorizzazione
Concessione a un'entità di sicurezza autenticata dell'autorizzazione a eseguire determinate operazioni. Nel modello di programmazione di Azure AD esistono due casi d'uso principali.

- Durante un flusso di [concessione di autorizzazione](#authorization-grant) OAuth2: quando il [proprietario delle risorse](#resource-owner) concede l'autorizzazione all'[applicazione client](#client-application) che può così accedere alle risorse del proprietario.
- Durante l'accesso alle risorse da parte del client: l'implementazione viene eseguita dal [server di risorse](#resource-server), usando i valori [attestazione](#claim) presenti nel [token di accesso](#access-token) come base per le decisioni relative al controllo di accesso.

## codice di autorizzazione
"Token" di breve durata fornito a un'[applicazione client](#client-application) dall'[endpoint di autorizzazione](#authorization-endpoint) nell'ambito del flusso del "codice di autorizzazione". È una delle quattro [concessioni di autorizzazione](#authorization-grant) OAuth2. Il codice viene restituito all'applicazione client in risposta all'autenticazione di un [proprietario delle risorse](#resource-owner) e indica che il proprietario ha delegato l'autorizzazione ad accedere alle risorse richieste. Nell'ambito del flusso, il codice viene successivamente riscattato per un [token di accesso](#access-token).

## endpoint di autorizzazione
Uno degli endpoint implementati dal [server di autorizzazione](#authorization-server), usato per interagire con il [proprietario delle risorse](#resource-owner) per fornire una [concessione di autorizzazione](#authorization-grant) durante un flusso di concessione di autorizzazione OAuth2. A seconda del flusso di concessione di autorizzazione usato, l'effettiva concessione fornita può variare e includere un [codice di autorizzazione](#authorization-code) o un [token di sicurezza](#security-token).

Per altri dettagli, vedere le sezioni relative ai [tipi di concessione di autorizzazione][OAuth2-AuthZ-Grant-Types] e all'[endpoint di autorizzazione][OAuth2-AuthZ-Endpoint] della specifica OAuth2 e la [specifica OpenIDConnect][OpenIDConnect-AuthZ-Endpoint].

## concessione di autorizzazione
Credenziale che rappresenta l'[autorizzazione](#authorization) del [proprietario delle risorse](#resource-owner) ad accedere alle risorse protette, concessa a un'[applicazione client](#client-application). Per ottenere una concessione, un'applicazione client può usare uno dei [quattro tipi di concessione definiti dal framework di autorizzazione di OAuth2][OAuth2-AuthZ-Grant-Types], a seconda del tipo e dei requisiti del client: concessione del codice di autorizzazione, concessione di credenziali client, concessione implicita e concessione delle credenziali password del proprietario della risorsa. A seconda del tipo di concessione di autorizzazione usato, la credenziale restituita al client è un [token di accesso](#access-token) o un [codice di autorizzazione](#authorization-code) successivamente scambiato con un token di accesso.

## server di autorizzazione
In base alla definizione del [framework di autorizzazione di OAuth2][OAuth2-Role-Def], server responsabile del rilascio dei token di accesso al [client](#client-application) dopo che è stata completata l'autenticazione del [proprietario delle risorse](#resource-owner) e ne è stata ottenuta l'autorizzazione. Un'[applicazione client](#client-application) interagisce con il server di autorizzazione in fase di esecuzione tramite gli [endpoint di autorizzazione](#authorization-endpoint) e di [token](#token-endpoint), in conformità alle [concessioni di autorizzazione](#authorization-grant) definite da OAuth2.

In caso di integrazione di applicazioni di Azure AD, Azure AD implementa il ruolo del server di autorizzazione per le applicazioni di Azure AD e le API del servizio Microsoft, ad esempio le [API Microsoft Graph][Microsoft-Graph].

## attestazione
Un [token di sicurezza](#security-token) contiene attestazioni, che forniscono asserzioni su un'entità (come un'[applicazione client](#client-application) o un [proprietario delle risorse](#resource-owner)) a un'altra entità, ad esempio il [server di risorse](#resource-server). Le attestazioni sono coppie nome-valore che inoltrano fact relativi al soggetto del token (ad esempio, l'identità di sicurezza che è stata autenticata dal [server di autorizzazione](#authorization-server)). Le attestazioni presenti in un determinato token dipendono da diverse variabili, tra cui il tipo di token, il tipo di credenziale usato per autenticare il soggetto, la configurazione dell'applicazione e così via.

Per altri dettagli, vedere [Informazioni di riferimento sui token in Azure AD][AAD-Tokens-Claims].

## applicazione client  
In base alla definizione del [framework di autorizzazione di OAuth2][OAuth2-Role-Def], applicazione che effettua richieste di risorse protette per conto del [proprietario delle risorse](#resource-owner). Il termine "client" non implica particolari caratteristiche di implementazione a livello di hardware, ad esempio l'esecuzione dell'applicazione su un server, un PC desktop o altri dispositivi.

Un'applicazione client richiede l'[autorizzazione](#authorization) da un proprietario delle risorse a partecipare a un flusso di [concessione di autorizzazione OAuth2](#authorization-grant) e può accedere alle API e ai dati per conto del proprietario delle risorse. Il framework di autorizzazione di OAuth2 [definisce due tipi di client][OAuth2-Client-Types], "riservato" e "pubblico", in base alla possibilità di mantenere riservate le proprie credenziali. Le applicazioni possono implementare un [client Web (riservato)](#web-client) eseguito in un server Web, un [client nativo (pubblico)](#native-client) installato in un dispositivo o un [client basato su agente utente (pubblico)](#user-agent-based-client) eseguito nel browser di un dispositivo.

## consenso
Processo con cui un [proprietario delle risorse](#resource-owner) concede l'autorizzazione a un'[applicazione client](#client-application), con specifiche [autorizzazioni](#permissions) ad accedere alle risorse protette per conto del proprietario delle risorse. A seconda delle autorizzazioni richieste dal client, verrà chiesto a un amministratore o a un utente di dare il consenso per permettere l'accesso, rispettivamente, ai dati dell'organizzazione o individuali. Si noti che in uno scenario [multi-tenant](#multi-tenant-application), l'[entità servizio](#service-principal-object) dell'applicazione viene registrata anche nel tenant dell'utente che dà il consenso.

## token ID
[Token di sicurezza](#security-token) [OpenID Connect][OpenIDConnect-ID-Token] fornito dall'[endpoint di autorizzazione](#authorization-endpoint) di un [server di autorizzazione](#authorization-server) che contiene [attestazioni](#claim) relative all'autenticazione di un [proprietario delle risorse](#resource-owner) utente finale. Così come i token di accesso, i token ID sono rappresentati anche come [token JSON Web (token JWT)][JWT] con firma digitale. A differenza di un token di accesso, tuttavia, le attestazioni di un token ID non vengono usate per scopi correlati all'accesso alle risorse e specificamente al controllo di accesso.

Per altri dettagli, vedere [Informazioni di riferimento sui token in Azure AD][AAD-Tokens-Claims].

## applicazione multi-tenant
Classe di [applicazione client](#client-application) che consente l'accesso e il [consenso](#consent) da utenti di cui è stato effettuato il provisioning in qualsiasi [tenant](#tenant) di Azure AD, anche se diverso da quello in cui il client è stato registrato. Un'applicazione registrata come a tenant singolo, invece, consentirà accessi solo da account utente con provisioning nello stesso tenant in cui l'applicazione è stata registrata. Le applicazioni [client native](#native-client) sono multi-tenant per impostazione predefinita, mentre le applicazioni [client Web](#web-client) possono scegliere tra tenant singolo e multi-tenant.

Per altri dettagli, vedere [Come consentire l'accesso a qualsiasi utente di Azure Active Directory (AD) usando il modello di applicazione multi-tenant][AAD-Multi-Tenant-Overview].

## client nativo
Tipo di [applicazione client](#client-application) che è installato in modo nativo in un dispositivo. Poiché tutto il codice viene eseguito in un dispositivo, il client viene considerato "pubblico" perché non può eseguire l'archiviazione privata/riservata delle credenziali. Per altri dettagli, vedere [i profili e i tipi di client di OAuth2][OAuth2-Client-Types].

## autorizzazioni
Un'[applicazione client](#client-application) ottiene l'accesso a un [server di risorse](#resource-server) dichiarando richieste di autorizzazione. Sono disponibili due tipi:

- Autorizzazioni "delegate", che richiedono l'accesso [in base all'ambito](#scopes) con autorizzazione delegata dal [proprietario delle risorse](#resource-owner) connesso e vengono presentate alla risorsa in fase di esecuzione sotto forma di [attestazioni "scp"](#claim) nel [token di accesso](#access-token) del client.
- Autorizzazioni "applicazione", che richiedono l'accesso [in base al ruolo](#roles) con credenziali/identità dell'applicazione client e vengono presentate alla risorsa in fase di esecuzione sotto forma di [attestazioni "roles"](#claim) nel token di accesso del client.

Vengono presentate anche durante il processo di [consenso](#consent) per offrire all'amministratore o al proprietario delle risorse l'opportunità di concedere/negare l'accesso client alle risorse nel tenant.

Le richieste di autorizzazione vengono configurate nella scheda "Applicazioni"/"Configura" del [portale di Azure classico][AZURE-classic-portal] in "Autorizzazioni per altre applicazioni", selezionando le "Autorizzazioni delegate" e le "Autorizzazioni applicazione" desiderate (per il secondo tipo è necessario essere membri del ruolo Amministratore globale). Un [client pubblico](#client-application) non può gestire le credenziali e può quindi solo richiedere autorizzazioni delegate, mentre un [client riservato](#client-application) può richiedere autorizzazioni sia delegate che applicazione. Le autorizzazioni dichiarate vengono archiviate dall'[oggetto applicazione](#application-object) del client nella proprietà [requiredResourceAccess][AAD-Graph-App-Entity].

## proprietario delle risorse
In base alla definizione del [framework di autorizzazione di OAuth2][OAuth2-Role-Def], entità che può concedere l'accesso a una risorsa protetta. Quando il proprietario delle risorse è una persona, è definito utente finale. Quando un'[applicazione client](#client-application) vuole accedere alla cassetta postale di un utente tramite l'[API Microsoft Graph][Microsoft-Graph], ad esempio, richiede l'autorizzazione dal proprietario della risorsa della cassetta postale.

## server di risorse
In base alla definizione del [framework di autorizzazione di OAuth2][OAuth2-Role-Def], server che ospita risorse protette e può accettare e rispondere alle relative richieste effettuate da [applicazioni client](#client-application) che presentano un [token di accesso](#access-token). È detto anche server di risorse protette o applicazione della risorsa.

Un server di risorse espone le API e consente l'accesso alle proprie risorse protette tramite [ambiti](#scopes) e [ruoli](#roles), usando il framework di autorizzazione di OAuth 2.0. Gli esempi includono l'API Graph di Azure AD che consente di accedere ai dati dei tenant di Azure AD e le API di Office 365 che consentono di accedere a dati come posta e calendario. Entrambi sono accessibili anche tramite l'[API Graph di Microsoft][Microsoft-Graph].

Così come per un'applicazione client, la configurazione di identità dell'applicazione della risorsa viene definita tramite la [registrazione](#application-registration) in un tenant di Azure AD, con cui vengono specificati sia l'oggetto applicazione che l'oggetto entità servizio. Alcune API fornite da Microsoft, come l'API Graph di Azure AD, includono entità servizio preregistrate che vengono rese disponibili in tutti i tenant durante il provisioning.

## ruoli
Così come gli [ambiti](#scopes), i ruoli consentono a un [server di risorse](#resource-server) di controllare l'accesso alle proprie risorse protette. Ne esistono due tipi: il ruolo "utente" implementa il controllo degli accessi in base al ruolo per gli utenti e i gruppi che devono accedere alla risorsa, mentre il ruolo "applicazione" esegue la stessa implementazione per le [applicazioni client](#client-application) che devono accedere.

I ruoli sono stringhe definite a livello di risorsa, ad esempio "Expense approver", "Read-only", "Directory.ReadWrite.All", vengono gestiti nel [portale di Azure classico][AZURE-classic-portal] tramite il [manifesto dell'applicazione](#application-manifest) della risorsa e vengono archiviati nella [proprietà appRoles][AAD-Graph-Sp-Entity] della risorsa. Il portale di Azure classico viene usato anche per assegnare utenti a ruoli utente e configurare [autorizzazioni applicazione](#permissions) client per l'accesso a un ruolo applicazione.

Per una descrizione dettagliata dei ruoli applicazione esposti dall'API Graph di Azure AD, vedere [Ambiti di autorizzazione | Concetti relativi all'API Graph][AAD-Graph-Perm-Scopes]. Per un esempio dettagliato di implementazione, vedere [Role based access control in cloud applications using Azure AD][Duyshant-Role-Blog] \(Controllo degli accessi in base al ruolo nelle applicazioni cloud con Azure AD).

## ambiti
Così come i [ruoli](#roles), gli ambiti consentono a un [server di risorse](#resource-server) di controllare l'accesso alle proprie risorse protette. Gli ambiti vengono usati per implementare il controllo di accesso [in base all'ambito][OAuth2-Access-Token-Scopes] per un'[applicazione client](#client-application) che ha ottenuto l'accesso delegato alla risorsa dal relativo proprietario.

Gli ambiti sono stringhe definite a livello di risorsa, ad esempio "Mail.Read", "Directory.ReadWrite.All", vengono gestiti nel [portale di Azure classico][AZURE-classic-portal] tramite il [manifesto dell'applicazione](#application-manifest) della risorsa e vengono archiviati nella [proprietà oauth2Permissions][AAD-Graph-Sp-Entity] della risorsa. Il portale di Azure classico viene usato anche per configurare [autorizzazioni delegate](#permissions) dell'applicazione client per l'accesso a un ambito.

Come convenzione di denominazione, la procedura consigliata è usare il formato "risorsa.operazione.vincolo". Per una descrizione dettagliata degli ambiti esposti dall'API Graph di Azure AD, vedere [Ambiti di autorizzazione | Concetti relativi all'API Graph][AAD-Graph-Perm-Scopes]. Per informazioni sugli ambiti esposti dai servizi di Office 365, vedere [Office 365 API permissions reference][O365-Perm-Ref] \(Informazioni di riferimento sulle autorizzazioni delle API di Office 365).

## token di sicurezza
Documento firmato contenente attestazioni, come un token OAuth2 o un'asserzione SAML 2.0. Per una [concessione di autorizzazione](#authorization-grant) OAuth2, un [token di accesso](#access-token) (OAuth2) e un [token ID](OpenID Connect) costituiscono un tipo di token di sicurezza e vengono entrambi implementati come [token JSON Web (token JWT)][JWT].

## oggetto entità servizio
Quando si registra/aggiorna un'applicazione nel [portale di Azure classico][AZURE-classic-portal], il portale crea/aggiorna sia un [oggetto applicazione](#application-object) che un oggetto entità servizio corrispondente per il tenant. L'oggetto applicazione *definisce* la configurazione di identità dell'applicazione a livello globale, ovvero in tutti i tenant in cui all'applicazione associata è stato concesso l'accesso, ed è il modello da cui *derivano* gli oggetti entità servizio corrispondenti usati in locale in fase di esecuzione (in uno specifico tenant).

Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio in Azure Active Directory][AAD-App-SP-Objects].

## accesso
Processo con cui un'[applicazione client](#client-application) avvia l'autenticazione dell'utente finale e acquisisce il relativo stato, allo scopo di acquisire un [token di sicurezza](#security-token) e definire l'ambito della sessione dell'applicazione in base a tale stato. Lo stato può includere elementi come informazioni del profilo utente e informazioni derivate dalle attestazioni del token.

La funzione di accesso di un'applicazione viene in genere usata per implementare l'accesso Single Sign-On (SSO). Può anche essere preceduta da una funzione di "iscrizione" che rappresenta il punto di ingresso di un utente finale per accedere a un'applicazione (al primo accesso). La funzione di iscrizione viene usata per raccogliere e rendere persistente uno stato aggiuntivo specifico dell'utente e può richiedere il [consenso dell'utente](#consent).

## disconnessione
Processo con cui viene annullata l'autenticazione di un utente finale, rimuovendo lo stato utente associato alla sessione dell'[applicazione client](#client-application) durante l'[accesso](#sign-in)

## tenant
Un'istanza di una directory di Azure AD è definita tenant di Azure AD. Offre una serie di funzionalità, tra cui:

- un servizio di registro per applicazioni integrate
- autenticazione di account utente e applicazioni registrate
- endpoint REST necessari per il supporto di diversi protocolli tra cui OAuth2 e SAML, inclusi l'[endpoint di autorizzazione](#authorization-endpoint), l'[endpoint di token](#token-endpoint) e l'endpoint "comune" usato dalle [applicazioni multi-tenant](#multi-tenant-application).

Un tenant è anche associato a una sottoscrizione di Azure AD o Office 365 durante il provisioning della sottoscrizione, fornendo funzionalità di gestione delle identità e degli accessi per la sottoscrizione. Per informazioni dettagliate sui vari modi in cui è possibile ottenere l'accesso a un tenant, vedere [Come ottenere un tenant di Azure Active Directory][AAD-How-To-Tenant]. Per informazioni sulla relazione tra sottoscrizioni e un tenant di Azure AD, vedere [Associare le sottoscrizioni di Azure ad Azure Active Directory][AAD-How-Subscriptions-Assoc].

## endpoint di token
Uno degli endpoint implementati dal [server di autorizzazione](#authorization-server) per supportare le [concessioni di autorizzazione](#authorization-grant) OAuth2. A seconda della concessione, può essere usato per acquisire un [token di accesso](#access-token) (e un token di "aggiornamento" correlato) per un [client](#client-application) oppure un [token ID](#ID-token) quando viene usato insieme al protocollo [OpenID Connect][OpenIDConnect].

## client basato su agente utente
Tipo di [applicazione client](#client-application) che scarica il codice da un server Web e viene eseguita all'interno di un agente utente (come un Web browser), ad esempio un'applicazione a singola pagina. Poiché tutto il codice viene eseguito in un dispositivo, il client viene considerato "pubblico" perché non può eseguire l'archiviazione privata/riservata delle credenziali. Per altri dettagli, vedere [i profili e i tipi di client di OAuth2][OAuth2-Client-Types].

## entità utente
Analogamente a un oggetto entità servizio che viene usato per rappresentare un'istanza dell'applicazione, un oggetto entità utente è un altro tipo di entità di sicurezza che rappresenta un utente. L'[entità utente][AAD-Graph-User-Entity] di Azure AD Graph definisce lo schema per un oggetto utente, incluse le proprietà relative all'utente come nome e cognome, nome dell'entità utente, appartenenza a un ruolo della directory e così via. La configurazione dell'identità utente per Azure AD può così stabilire un'entità utente in fase di esecuzione. L'entità utente viene usata per rappresentare un utente autenticato per Single Sign-On, durante la registrazione della delega del [consenso](#consent), le decisioni di controllo di accesso e così via.

## client Web
Tipo di [applicazione client](#client-application) che esegue tutto il codice su un server Web e può funzionare come client "riservato" perché può eseguire l'archiviazione sicura delle credenziali sul server. Per altri dettagli, vedere [i profili e i tipi di client di OAuth2][OAuth2-Client-Types].

## Passaggi successivi
La [Guida per gli sviluppatori di Azure Active Directory][AAD-Dev-Guide] offre un portale da usare per tutti gli argomenti relativi allo sviluppo per Azure AD, ad esempio per una panoramica dell'[integrazione di applicazioni][AAD-How-To-Integrate] e le nozioni di base sull'[autenticazione in Azure AD e gli scenari di autenticazione supportati][AAD-Auth-Scenarios].

La sezione dei commenti Disqus di seguito consente di fornire commenti e suggerimenti utili per migliorare e organizzare i contenuti disponibili.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]: ./active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]: active-directory-howto-tenant.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Multi-Tenant-Overview]: active-directory-devhowto-multi-tenant-overview.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AZURE-classic-portal]: https://manage.windowsazure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://graph.microsoft.io
[O365-Perm-Ref]: https://msdn.microsoft.com/it-IT/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: http://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken

<!---HONumber=AcomDC_0831_2016-->