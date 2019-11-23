---
title: Microsoft identity platform developer glossary | Azure
description: A list of terms for commonly used Microsoft identity platform developer concepts and features.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 551512df-46fb-4219-a14b-9c9fc23998ba
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/28/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: jmprieur, saeeda, jesakowi, nacanuma
ms.collection: M365-identity-device-management
ms.openlocfilehash: 221a491abad6c11ee12c75b1d69f1263f4abddc4
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/22/2019
ms.locfileid: "74322605"
---
# <a name="microsoft-identity-platform-developer-glossary"></a>Microsoft identity platform developer glossary

This article contains definitions for some of the core developer concepts and terminology, which are helpful when learning about application development using Microsoft identity platform.

## <a name="access-token"></a>token di accesso

Tipo di [token di sicurezza](#security-token) rilasciato da un [server di autorizzazione](#authorization-server) e usato da un'[applicazione client](#client-application) per accedere a un [server di risorse protette](#resource-server). Typically in the form of a [JSON Web Token (JWT)][JWT], the token embodies the authorization granted to the client by the [resource owner](#resource-owner), for a requested level of access. Il token contiene tutte le [attestazioni](#claim) applicabili relative al soggetto e può essere usato dall'applicazione client come credenziale per accedere a una determinata risorsa. Il proprietario della risorsa non dovrà quindi esporre le credenziali al client.

I token di accesso sono talvolta definiti "utente + app" o "solo app", a seconda delle credenziali rappresentate. Ad esempio:

* Quando un'applicazione client usa la [concessione di autorizzazione "codice di autorizzazione"](#authorization-grant), l'utente finale esegue prima l'autenticazione come proprietario della risorsa, delegando al client l'autorizzazione ad accedere alla risorsa. Il client esegue successivamente l'autenticazione quando ottiene il token di accesso. Il token può talvolta essere definito più specificamente token "utente + app" perché rappresenta sia l'utente che ha autorizzato l'applicazione client che l'applicazione.
* Quando un'applicazione client usa la [concessione di autorizzazione "credenziali client"](#authorization-grant), il client fornisce la sola autenticazione e funziona senza l'autenticazione/autorizzazione del proprietario della risorsa. Il token può quindi essere definito talvolta token "solo app".

See [Microsoft identity platform Token Reference][AAD-Tokens-Claims] for more details.

## <a name="application-id-client-id"></a>application ID (client ID)

Identificatore univoco generato da Azure AD per la registrazione di un'applicazione che identifica un'applicazione specifica e le configurazioni associate. This application ID ([client ID](https://tools.ietf.org/html/rfc6749#page-15)) is used when performing authentication requests and is provided to the authentication libraries in development time. The application ID (client ID) is not a secret.

## <a name="application-manifest"></a>manifesto dell'applicazione

A feature provided by the [Azure portal][AZURE-portal], which produces a JSON representation of the application's identity configuration, used as a mechanism for updating its associated [Application][AAD-Graph-App-Entity] and [ServicePrincipal][AAD-Graph-Sp-Entity] entities. See [Understanding the Azure Active Directory application manifest][AAD-App-Manifest] for more details.

## <a name="application-object"></a>oggetto applicazione

When you register/update an application in the [Azure portal][AZURE-portal], the portal creates/updates both an application object and a corresponding [service principal object](#service-principal-object) for that tenant. L'oggetto applicazione *definisce* la configurazione di identità dell'applicazione a livello globale (in tutti i tenant a cui ha accesso), offrendo un modello da cui *derivano* gli oggetti entità servizio corrispondenti usati in locale in fase di esecuzione (in uno specifico tenant).

For more information, see [Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="application-registration"></a>registrazione dell'applicazione

Per consentire a un'applicazione l'integrazione con le funzioni di gestione delle identità e degli accessi e la relativa delega in Azure AD, è necessario che l'applicazione sia registrata in un [tenant](#tenant)di Azure AD. Quando si registra l'applicazione in Azure AD, si specifica una configurazione di identità per l'applicazione che ne consente l'integrazione con Azure AD e l'uso di funzionalità come le seguenti:

* Robust management of Single Sign-On using Azure AD Identity Management and [OpenID Connect][OpenIDConnect] protocol implementation
* Brokered access to [protected resources](#resource-server) by [client applications](#client-application), via OAuth 2.0 [authorization server](#authorization-server)
* [Framework di consenso](#consent) per la gestione dell'accesso client alle risorse protette, in base all'autorizzazione del proprietario delle risorse

See [Integrating applications with Azure Active Directory][AAD-Integrating-Apps] for more details.

## <a name="authentication"></a>Autenticazione

Richiesta di credenziali legittime a una parte, che costituisce la base per la creazione di un'entità di sicurezza da usare per il controllo delle identità e di accesso. Durante una [concessione di autorizzazione OAuth2](#authorization-grant), ad esempio, la parte che esegue l'autenticazione svolge il ruolo di [proprietario delle risorse](#resource-owner) o di [applicazione client](#client-application) a seconda della concessione usata.

## <a name="authorization"></a>authorization

Concessione a un'entità di sicurezza autenticata dell'autorizzazione a eseguire determinate operazioni. Nel modello di programmazione di Azure AD esistono due casi d'uso principali.

* Durante un flusso di [concessione di autorizzazione](#authorization-grant) OAuth2: quando il [proprietario delle risorse](#resource-owner) concede l'autorizzazione all'[applicazione client](#client-application) che può così accedere alle risorse del proprietario.
* Durante l'accesso alle risorse da parte del client: l'implementazione viene eseguita dal [server di risorse](#resource-server), usando i valori [attestazione](#claim) presenti nel [token di accesso](#access-token) come base per le decisioni relative al controllo di accesso.

## <a name="authorization-code"></a>codice di autorizzazione

"Token" di breve durata fornito a un'[applicazione client](#client-application) dall'[endpoint di autorizzazione](#authorization-endpoint) nell'ambito del flusso del "codice di autorizzazione". È una delle quattro [concessioni di autorizzazione](#authorization-grant) OAuth2. Il codice viene restituito all'applicazione client in risposta all'autenticazione di un [proprietario delle risorse](#resource-owner) e indica che il proprietario ha delegato l'autorizzazione ad accedere alle risorse richieste. Nell'ambito del flusso, il codice viene successivamente riscattato per un [token di accesso](#access-token).

## <a name="authorization-endpoint"></a>endpoint di autorizzazione

Uno degli endpoint implementati dal [server di autorizzazione](#authorization-server), usato per interagire con il [proprietario delle risorse](#resource-owner) per fornire una [concessione di autorizzazione](#authorization-grant) durante un flusso di concessione di autorizzazione OAuth2. A seconda del flusso di concessione di autorizzazione usato, l'effettiva concessione fornita può variare e includere un [codice di autorizzazione](#authorization-code) o un [token di sicurezza](#security-token).

See the OAuth2 specification's [authorization grant types][OAuth2-AuthZ-Grant-Types] and [authorization endpoint][OAuth2-AuthZ-Endpoint] sections, and the [OpenIDConnect specification][OpenIDConnect-AuthZ-Endpoint] for more details.

## <a name="authorization-grant"></a>concessione di autorizzazione

Credenziale che rappresenta l'[autorizzazione](#resource-owner) del [proprietario delle risorse](#authorization) ad accedere alle risorse protette, concessa a un'[applicazione client](#client-application). A client application can use one of the [four grant types defined by the OAuth2 Authorization Framework][OAuth2-AuthZ-Grant-Types] to obtain a grant, depending on client type/requirements: "authorization code grant", "client credentials grant", "implicit grant", and "resource owner password credentials grant". A seconda del tipo di concessione di autorizzazione usato, la credenziale restituita al client è un [token di accesso](#access-token) o un [codice di autorizzazione](#authorization-code) successivamente scambiato con un token di accesso.

## <a name="authorization-server"></a>server di autorizzazione

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], the server responsible for issuing access tokens to the [client](#client-application) after successfully authenticating the [resource owner](#resource-owner) and obtaining its authorization. Un'[applicazione client](#client-application) interagisce con il server di autorizzazione in fase di esecuzione tramite gli [endpoint di autorizzazione](#authorization-endpoint) e di [token](#token-endpoint), in conformità alle [concessioni di autorizzazione](#authorization-grant) definite da OAuth2.

In the case of Microsoft identity platform application integration, Microsoft identity platform implements the authorization server role for Azure AD applications and Microsoft service APIs, for example [Microsoft Graph APIs][Microsoft-Graph].

## <a name="claim"></a>attestazione

Un [token di sicurezza](#security-token) contiene attestazioni, che forniscono asserzioni su un'entità (come un'[applicazione client](#client-application) o un [proprietario delle risorse](#resource-owner)) a un'altra entità, ad esempio il [server di risorse](#resource-server). Le attestazioni sono coppie nome-valore che inoltrano fact relativi al soggetto del token (ad esempio, l'identità di sicurezza che è stata autenticata dal [server di autorizzazione](#authorization-server)). Le attestazioni presenti in un determinato token dipendono da diverse variabili, tra cui il tipo di token, il tipo di credenziale usato per autenticare il soggetto, la configurazione dell'applicazione e così via.

See [Microsoft identity platform token reference][AAD-Tokens-Claims] for more details.

## <a name="client-application"></a>applicazione client

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], an application that makes protected resource requests on behalf of the [resource owner](#resource-owner). Il termine "client" non implica particolari caratteristiche di implementazione a livello di hardware, ad esempio l'esecuzione dell'applicazione su un server, un PC desktop o altri dispositivi.

Un'applicazione client richiede l'[autorizzazione](#authorization) da un proprietario delle risorse a partecipare a un flusso di [concessione di autorizzazione OAuth2](#authorization-grant) e può accedere alle API e ai dati per conto del proprietario delle risorse. The OAuth2 Authorization Framework [defines two types of clients][OAuth2-Client-Types], "confidential" and "public", based on the client's ability to maintain the confidentiality of its credentials. Le applicazioni possono implementare un [client Web (riservato)](#web-client) eseguito in un server Web, un [client nativo (pubblico)](#native-client) installato in un dispositivo o un [client basato su agente utente (pubblico)](#user-agent-based-client) eseguito nel browser di un dispositivo.

## <a name="consent"></a>consenso

Processo secondo cui un [proprietario di risorse](#resource-owner) concede l'autorizzazione a un'[applicazione client](#client-application) per accedere a risorse protette con specifiche [autorizzazioni](#permissions) per conto del proprietario delle risorse. A seconda delle autorizzazioni richieste dal client, verrà chiesto a un amministratore o a un utente di dare il consenso per permettere l'accesso, rispettivamente, ai dati dell'organizzazione o individuali. Si noti che in uno scenario [multi-tenant](#multi-tenant-application), l'[entità servizio](#service-principal-object) dell'applicazione viene registrata anche nel tenant dell'utente che dà il consenso.

Per altre informazioni, vedere [framework di consenso](consent-framework.md).

## <a name="id-token"></a>token ID

An [OpenID Connect][OpenIDConnect-ID-Token] [security token](#security-token) provided by an [authorization server's](#authorization-server) [authorization endpoint](#authorization-endpoint), which contains [claims](#claim) pertaining to the authentication of an end user [resource owner](#resource-owner). Like an access token, ID tokens are also represented as a digitally signed [JSON Web Token (JWT)][JWT]. A differenza di un token di accesso, tuttavia, le attestazioni di un token ID non vengono usate per scopi correlati all'accesso alle risorse e specificamente al controllo di accesso.

See [Microsoft identity platform token reference][AAD-Tokens-Claims] for more details.

## <a name="microsoft-identity-platform"></a>Microsoft Identity Platform

Microsoft Identity Platform è un'evoluzione della piattaforma per sviluppatori e del servizio di gestione delle identità di Azure Active Directory (Azure AD). Consente agli sviluppatori di creare applicazioni che supportano l'accesso per tutte le identità Microsoft e il recupero di token per chiamare Microsoft Graph, altre API Microsoft o API create dagli sviluppatori. È una piattaforma completa che include un servizio di autenticazione, librerie, la configurazione e la registrazione delle applicazioni, esempi di codice e una documentazione esaustiva e altri contenuti per gli sviluppatori. Microsoft Identity Platform supporta protocolli standard di settore come OAuth 2.0 e OpenID Connect. Per altre informazioni, vedere [Informazioni su Microsoft Identity Platform](about-microsoft-identity-platform.md).

## <a name="multi-tenant-application"></a>applicazione multi-tenant

Classe di applicazione che consente l'accesso e il [consenso](#consent) da utenti di cui è stato eseguito il provisioning in un qualsiasi [tenant](#tenant) di Azure AD, anche se diverso da quello in cui il client è registrato. Le applicazioni [cliente native](#native-client) sono multi-tenant per impostazione predefinita, mentre le applicazioni [client Web](#web-client) e [API/risorse Web](#resource-server) possono essere a tenant singolo o multi-tenant. Un'applicazione Web registrata come a tenant singolo, invece, consentirà accessi solo da account utente con provisioning nello stesso tenant in cui l'applicazione è stata registrata.

See [How to sign in any Azure AD user using the multi-tenant application pattern][AAD-Multi-Tenant-Overview] for more details.

## <a name="native-client"></a>client nativo

Tipo di [applicazione client](#client-application) che è installato in modo nativo in un dispositivo. Poiché tutto il codice viene eseguito in un dispositivo, il client viene considerato "pubblico" perché non può eseguire l'archiviazione privata/riservata delle credenziali. See [OAuth2 client types and profiles][OAuth2-Client-Types] for more details.

## <a name="permissions"></a>autorizzazioni

Un'[applicazione client](#client-application) ottiene l'accesso a un [server di risorse](#resource-server) dichiarando richieste di autorizzazione. Sono disponibili due tipi:

* Le autorizzazioni "delegate", che richiedono l'accesso [in base all'ambito](#scopes) con autorizzazione delegata dal [proprietario delle risorse](#resource-owner) connesso, vengono presentate alla risorsa in fase di esecuzione sotto forma di [attestazioni "scp"](#claim) nel [token di accesso](#access-token) del client.
* Le autorizzazioni "applicazione", che richiedono l'accesso [in base al ruolo](#roles) con credenziali/identità dell'applicazione client, vengono presentate alla risorsa in fase di esecuzione sotto forma di [attestazioni "roles"](#claim) nel token di accesso del client.

Vengono presentate anche durante il processo di [consenso](#consent) per offrire all'amministratore o al proprietario delle risorse l'opportunità di concedere/negare l'accesso client alle risorse nel tenant.

Permission requests are configured on the **API permissions** page for an application in the [Azure portal][AZURE-portal], by selecting the desired "Delegated Permissions" and "Application Permissions" (the latter requires membership in the Global Admin role). Dal momento che un [client pubblico](#client-application) non può gestire le credenziali in modo sicuro, può solo richiedere autorizzazioni delegate, mentre un [client riservato](#client-application) può richiedere autorizzazioni sia delegate che applicazione. The client's [application object](#application-object) stores the declared permissions in its [requiredResourceAccess property][AAD-Graph-App-Entity].

## <a name="resource-owner"></a>proprietario delle risorse

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], an entity capable of granting access to a protected resource. Quando il proprietario delle risorse è una persona, è definito utente finale. For example, when a [client application](#client-application) wants to access a user's mailbox through the [Microsoft Graph API][Microsoft-Graph], it requires permission from the resource owner of the mailbox.

## <a name="resource-server"></a>server di risorse

As defined by the [OAuth2 Authorization Framework][OAuth2-Role-Def], a server that hosts protected resources, capable of accepting and responding to protected resource requests by [client applications](#client-application) that present an [access token](#access-token). È detto anche server di risorse protette o applicazione della risorsa.

Un server di risorse espone le API e consente l'accesso alle proprie risorse protette tramite [ambiti](#scopes) e [ruoli](#roles), usando il framework di autorizzazione di OAuth 2.0. Gli esempi includono l'API Graph di Azure AD che consente di accedere ai dati dei tenant di Azure AD e le API di Office 365 che consentono di accedere a dati come posta e calendario. Both of these are also accessible via the [Microsoft Graph API][Microsoft-Graph].

Così come per un'applicazione client, la configurazione di identità dell'applicazione della risorsa viene definita tramite la [registrazione](#application-registration) in un tenant di Azure AD, con cui vengono specificati sia l'oggetto applicazione che l'oggetto entità servizio. Alcune API fornite da Microsoft, come l'API Graph di Azure AD, includono entità servizio preregistrate che vengono rese disponibili in tutti i tenant durante il provisioning.

## <a name="roles"></a>roles

Così come gli [ambiti](#scopes), i ruoli consentono a un [server di risorse](#resource-server) di controllare l'accesso alle proprie risorse protette. Ne esistono due tipi: il ruolo "utente" implementa il controllo degli accessi in base al ruolo per gli utenti e i gruppi che devono accedere alla risorsa, mentre il ruolo "applicazione" esegue la stessa implementazione per le [applicazioni client](#client-application) che devono accedere.

Roles are resource-defined strings (for example "Expense approver", "Read-only", "Directory.ReadWrite.All"), managed in the [Azure portal][AZURE-portal] via the resource's [application manifest](#application-manifest), and stored in the resource's [appRoles property][AAD-Graph-Sp-Entity]. Il portale di Azure viene usato anche per assegnare gli utenti ai ruoli utente e configurare le [autorizzazioni applicazione](#permissions) client per l'accesso a un ruolo applicazione.

For a detailed discussion of the application roles exposed by Azure AD's Graph API, see [Graph API Permission Scopes][AAD-Graph-Perm-Scopes]. For a step-by-step implementation example, see [Manage access using RBAC and the Azure portal][AAD-RBAC].

## <a name="scopes"></a>ambiti

Così come i [ruoli](#roles), gli ambiti consentono a un [server di risorse](#resource-server) di controllare l'accesso alle proprie risorse protette. Scopes are used to implement [scope-based][OAuth2-Access-Token-Scopes] access control, for a [client application](#client-application) that has been given delegated access to the resource by its owner.

Scopes are resource-defined strings (for example "Mail.Read", "Directory.ReadWrite.All"), managed in the [Azure portal][AZURE-portal] via the resource's [application manifest](#application-manifest), and stored in the resource's [oauth2Permissions property][AAD-Graph-Sp-Entity]. Il portale di Azure viene usato anche per configurare le [autorizzazioni delegate](#permissions) dell'applicazione client per l'accesso a un ambito.

Come convenzione di denominazione, la procedura consigliata è usare il formato "risorsa.operazione.vincolo". For a detailed discussion of the scopes exposed by Azure AD's Graph API, see [Graph API Permission Scopes][AAD-Graph-Perm-Scopes]. For scopes exposed by Office 365 services, see [Office 365 API permissions reference][O365-Perm-Ref].

## <a name="security-token"></a>token di sicurezza

Documento firmato contenente attestazioni, come un token OAuth2 o un'asserzione SAML 2.0. For an OAuth2 [authorization grant](#authorization-grant), an [access token](#access-token) (OAuth2) and an [ID Token](https://openid.net/specs/openid-connect-core-1_0.html#IDToken) are types of security tokens, both of which are implemented as a [JSON Web Token (JWT)][JWT].

## <a name="service-principal-object"></a>oggetto entità servizio

When you register/update an application in the [Azure portal][AZURE-portal], the portal creates/updates both an [application object](#application-object) and a corresponding service principal object for that tenant. L'oggetto applicazione *definisce* la configurazione di identità dell'applicazione a livello globale, ovvero in tutti i tenant in cui all'applicazione associata è stato concesso l'accesso, ed è il modello da cui *derivano* gli oggetti entità servizio corrispondenti usati in locale in fase di esecuzione (in uno specifico tenant).

For more information, see [Application and Service Principal Objects][AAD-App-SP-Objects].

## <a name="sign-in"></a>accesso

Processo con cui un'[applicazione client](#client-application) avvia l'autenticazione dell'utente finale e acquisisce il relativo stato, allo scopo di acquisire un [token di sicurezza](#security-token) e definire l'ambito della sessione dell'applicazione in base a tale stato. Lo stato può includere elementi come informazioni del profilo utente e informazioni derivate dalle attestazioni del token.

La funzione di accesso di un'applicazione viene in genere usata per implementare l'accesso Single Sign-On (SSO). Può anche essere preceduta da una funzione di "iscrizione" che rappresenta il punto di ingresso di un utente finale per accedere a un'applicazione (al primo accesso). La funzione di iscrizione viene usata per raccogliere e rendere persistente uno stato aggiuntivo specifico dell'utente e può richiedere il [consenso dell'utente](#consent).

## <a name="sign-out"></a>disconnessione

Processo con cui viene annullata l'autenticazione di un utente finale, rimuovendo lo stato utente associato alla sessione dell'[applicazione client](#client-application) durante l'[accesso](#sign-in)

## <a name="tenant"></a>tenant

Un'istanza di una directory di Azure AD è definita tenant di Azure AD. Fornisce diverse funzionalità, tra cui:

* un servizio di registro per applicazioni integrate
* autenticazione di account utente e applicazioni registrate
* endpoint REST necessari per il supporto di diversi protocolli tra cui OAuth2 e SAML, inclusi l'[endpoint di autorizzazione](#authorization-endpoint), l'[endpoint di token](#token-endpoint) e l'endpoint "comune" usato dalle [applicazioni multi-tenant](#multi-tenant-application).

I tenant di Azure AD vengono creati/associati alle sottoscrizioni di Azure e agli abbonamenti a Office 365 durante l'iscrizione, fornendo funzionalità di gestione delle identità e degli accessi per la sottoscrizione. Gli amministratori delle sottoscrizioni di Azure inoltre possono creare altri tenant di Azure AD tramite il portale di Azure. See [How to get an Azure Active Directory tenant][AAD-How-To-Tenant] for details on the various ways you can get access to a tenant. See [How Azure subscriptions are associated with Azure Active Directory][AAD-How-Subscriptions-Assoc] for details on the relationship between subscriptions and an Azure AD tenant.

## <a name="token-endpoint"></a>endpoint di token

Uno degli endpoint implementati dal [server di autorizzazione](#authorization-server) per supportare le [concessioni di autorizzazione](#authorization-grant) OAuth2. Depending on the grant, it can be used to acquire an [access token](#access-token) (and related "refresh" token) to a [client](#client-application), or [ID token](#id-token) when used with the [OpenID Connect][OpenIDConnect] protocol.

## <a name="user-agent-based-client"></a>client basato su agente utente

Tipo di [applicazione client](#client-application) che scarica il codice da un server Web e viene eseguita all'interno di un agente utente (come un Web browser), ad esempio un'applicazione a pagina singola. Poiché tutto il codice viene eseguito in un dispositivo, il client viene considerato "pubblico" perché non può eseguire l'archiviazione privata/riservata delle credenziali. For more information, see [OAuth2 client types and profiles][OAuth2-Client-Types].

## <a name="user-principal"></a>entità utente

Analogamente a un oggetto entità servizio che viene usato per rappresentare un'istanza dell'applicazione, un oggetto entità utente è un altro tipo di entità di sicurezza che rappresenta un utente. The Azure AD Graph [User entity][AAD-Graph-User-Entity] defines the schema for a user object, including user-related properties such as first and last name, user principal name, directory role membership, etc. This provides the user identity configuration for Azure AD to establish a user principal at run-time. L'entità utente viene usata per rappresentare un utente autenticato per Single Sign-On, durante la registrazione della delega del [consenso](#consent), le decisioni di controllo di accesso e così via.

## <a name="web-client"></a>client Web

Tipo di [applicazione client](#client-application) che esegue tutto il codice su un server Web e può funzionare come client "riservato" perché può eseguire l'archiviazione sicura delle credenziali sul server. For more information, see [OAuth2 client types and profiles][OAuth2-Client-Types].

## <a name="next-steps"></a>Passaggi successivi

The [Microsoft identity platform Developer's Guide][AAD-Dev-Guide] is the landing page to use for all Microsoft identity platform development-related topics, including an overview of [application integration][AAD-How-To-Integrate] and the basics of [Microsoft identity platform authentication and supported authentication scenarios][AAD-Auth-Scenarios]. È anche possibile trovare esempi di codice ed esercitazioni su come ottenere rapidamente in [GitHub](https://github.com/azure-samples?utf8=%E2%9C%93&q=active%20directory&type=&language=).

Usare la seguente sezione commenti per fornire commenti e suggerimenti per aiutarci a perfezionare e a definire questo contenuto del glossario, incluse le richieste di nuove definizioni o l'aggiornamento di quelle esistenti.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Manifest]:reference-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: /graph/permissions-reference
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-Subscriptions-Assoc]:../fundamentals/active-directory-how-subscriptions-associated-directory.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-How-To-Tenant]:quickstart-create-new-tenant.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Multi-Tenant-Overview]:howto-convert-app-to-be-multi-tenant.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AZURE-portal]: https://portal.azure.com
[AAD-RBAC]: ../../role-based-access-control/role-assignments-portal.md
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[Microsoft-Graph]: https://developer.microsoft.com/graph
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Endpoint]: https://tools.ietf.org/html/rfc6749#section-3.1
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-AuthZ-Endpoint]: https://openid.net/specs/openid-connect-core-1_0.html#AuthorizationEndpoint
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
