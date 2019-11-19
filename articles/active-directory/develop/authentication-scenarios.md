---
title: Autenticazione in Microsoft Identity Platform | Azure
description: Informazioni sulle nozioni di base sull'autenticazione nella piattaforma Microsoft Identity (v 2.0).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/15/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.collection: M365-identity-device-management
ms.openlocfilehash: 317efa17b294e859ef8a092451aca70b5b836fe7
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74167810"
---
# <a name="authentication-basics"></a>Nozioni di base sull'autenticazione

## <a name="what-is-authentication"></a>Informazioni sull'autenticazione

Questo articolo descrive molti dei concetti di autenticazione che è necessario comprendere per creare app Web protette, API Web o app che chiamano API Web protette.

L' **autenticazione** è il processo di dimostrazione dell'utente. In lingua inglese, il termine autenticazione viene talvolta abbreviato in AuthN.

L' **autorizzazione** è l'atto di concedere a un'entità autenticata l'autorizzazione per eseguire un'operazione. Specifica i dati a cui si è autorizzati ad accedere e le operazioni che è possibile eseguire con tali dati. In lingua inglese, il termine autorizzazione viene talvolta abbreviato in AuthZ.

Anziché creare app che gestiscono le proprie informazioni relative a nome utente e password, il che comporta un carico amministrativo elevato quando è necessario aggiungere o rimuovere utenti in più app, le app possono delegare tale responsabilità a un provider di identità centralizzato.

Azure Active Directory (Azure AD) è un provider di identità centralizzato nel cloud. Delegare l'autenticazione e l'autorizzazione al servizio IT consente scenari come i criteri di accesso condizionale che richiedono che un utente si trovi in una posizione specifica, l'uso dell'autenticazione a più fattori, nonché l'abilitazione di un utente per l'accesso una sola volta e quindi automaticamente accesso a tutte le app Web che condividono la stessa directory centralizzata. Questa funzionalità è denominata Single Sign-on (SSO).

Un provider di identità centralizzato è ancora più importante per le app che hanno utenti dislocati in tutto il mondo che non devono necessariamente accedere dalla rete aziendale. Azure AD autentica gli utenti e fornisce i token di accesso. Un token di accesso è un token di sicurezza emesso da un server di autorizzazione. Contiene informazioni sull'utente e sull'app per cui è previsto il token, che può essere usato per accedere alle API Web e ad altre risorse protette.

La piattaforma di identità Microsoft semplifica l'autenticazione per gli sviluppatori di applicazioni fornendo identità come servizio, con il supporto per protocolli standard del settore come OAuth 2,0 e OpenID Connect, nonché librerie open source per diverse piattaforme che consentono di iniziare rapidamente a scrivere codice. Consente agli sviluppatori di creare applicazioni che supportano l'accesso per tutte le identità Microsoft e il recupero di token per chiamare Microsoft Graph, altre API Microsoft o API create dagli sviluppatori. Per ulteriori informazioni, vedere [Evolution of Microsoft Identity Platform](about-microsoft-identity-platform.md).

### <a name="tenants"></a>Tenant

Un provider di identità cloud offre molte organizzazioni. Per evitare che gli utenti di organizzazioni diverse si separano, Azure AD viene partizionato in tenant, con un tenant per ogni organizzazione.

I tenant tengono traccia degli utenti e delle app associate. La piattaforma di identità Microsoft supporta anche gli utenti che hanno accesso con account Microsoft personali.

Azure AD fornisce anche Azure Active Directory B2C in modo che le organizzazioni possano accedere agli utenti, in genere clienti, usando identità social come un account Google. Per ulteriori informazioni, vedere [Azure Active Directory B2C documentazione](https://docs.microsoft.com/azure/active-directory-b2c) .

### <a name="security-tokens"></a>Token di sicurezza

I token di sicurezza contengono informazioni su utenti e app. Azure AD USA token basati su JSON (token JWT) che contengono attestazioni. Un'attestazione fornisce asserzioni relative a un'entità a un'altra. Le applicazioni possono utilizzare attestazioni per diverse attività, ad esempio:

* Convalida del token
* Identificazione del tenant di directory dell'oggetto
* Visualizzazione delle informazioni utente
* Determinazione dell'autorizzazione del soggetto

Un'attestazione è costituita da coppie chiave-valore che forniscono informazioni come:

- Server del token di sicurezza che ha generato il token.
- Data di generazione del token.
- oggetto, ad esempio l'utente (eccetto daemons).
- destinatari, ovvero l'app per cui è stato generato il token.
- l'app (il client) che ha richiesto il token. Nel caso di app Web, questo potrebbe essere lo stesso dei destinatari.

Per informazioni più dettagliate sulle attestazioni, vedere token di [accesso](access-tokens.md) e [token ID](id-tokens.md).

È l'app per cui è stato generato il token, l'app Web che ha eseguito l'accesso all'utente o l'API Web chiamata per convalidare il token. Il token è firmato dal server del token di sicurezza (STS) con una chiave privata. Il servizio token di stato pubblica la chiave pubblica corrispondente. Per convalidare un token, l'app verifica la firma usando la chiave pubblica STS per verificare che la firma sia stata creata usando la chiave privata.

I token sono validi solo per un periodo di tempo limitato. In genere il servizio token di protezione fornisce una coppia di token: un token di accesso per accedere all'applicazione o alla risorsa protetta e un token di aggiornamento usato per aggiornare il token di accesso quando il token di accesso è prossimo alla scadenza. 

I token di accesso vengono passati a un'API Web come bearer token nell'intestazione `Authorization`. Un'app può fornire un token di aggiornamento al servizio token di accesso e, se l'accesso dell'utente all'app non è stato revocato, otterrà un nuovo token di accesso e un nuovo token di aggiornamento. Questo è il modo in cui viene gestito lo scenario di un utente che lascia l'organizzazione. Quando il servizio token di accesso riceve il token di aggiornamento, non emette un altro token di accesso valido se l'utente non è più autorizzato.

## <a name="application-model"></a>Modello di applicazione

Le applicazioni possono accedere agli utenti stessi o delegare l'accesso a un provider di identità. Per informazioni sugli scenari di accesso supportati da Azure AD, vedere [flussi di autenticazione e scenari di app](authentication-flows-app-scenarios.md) .

Affinché un provider di identità sappia che un utente ha accesso a una determinata app, sia l'utente che l'applicazione devono essere registrati con il provider di identità. Quando si registra l'applicazione con Azure AD, viene offerta una configurazione di identità per l'applicazione che consente l'integrazione con Azure AD. La registrazione dell'app consente anche di:

- personalizzare la personalizzazione dell'applicazione nella finestra di dialogo di accesso. Questo è importante perché si tratta della prima esperienza che un utente avrà con l'app.
- decidere se si desidera consentire agli utenti di accedere solo se appartengono all'organizzazione. Si tratta di un'applicazione single-tenant. In alternativa, consentire agli utenti di accedere usando un account aziendale o dell'Istituto di istruzione. Si tratta di un'applicazione multi-tenant. È anche possibile consentire account Microsoft personali o un account di social networking da LinkedIn, Google e così via.
- autorizzazioni dell'ambito della richiesta. È ad esempio possibile richiedere l'ambito "User. Read", che concede l'autorizzazione per leggere il profilo dell'utente che ha eseguito l'accesso.
- definire gli ambiti che definiscono l'accesso all'API Web. In genere, quando un'app vuole accedere all'API, sarà necessario richiedere le autorizzazioni agli ambiti definiti.
- condividere un segreto con Azure AD che dimostra l'identità dell'app per Azure AD.  Questo è rilevante nel caso in cui l'app sia un'applicazione client riservata. Un'applicazione client riservata è un'applicazione in grado di conservare le credenziali in modo sicuro. Per archiviare le credenziali è necessario un server back-end attendibile.

Una volta eseguita la registrazione, all'applicazione verrà assegnato un GUID condiviso dall'app con Azure AD quando richiede token. Se l'app è un'applicazione client riservata, condividerà anche il segreto o la chiave pubblica, a seconda che siano stati usati i certificati o i segreti.

La piattaforma Microsoft Identity rappresenta le applicazioni che utilizzano un modello che soddisfa due funzioni principali:

Identificare l'app mediante i protocolli di autenticazione supportati e fornire tutti gli identificatori, gli URL, i segreti e le informazioni correlate necessarie per l'autenticazione.
Piattaforma di identità Microsoft:

* Include tutti i dati necessari per supportare l'autenticazione in fase di esecuzione.
* Include tutti i dati per la decisione delle risorse a cui un'app potrebbe dover accedere e in quali circostanze deve essere soddisfatta una determinata richiesta.
* Fornisce l'infrastruttura per implementare il provisioning delle app nel tenant dello sviluppatore di app e in qualsiasi altro tenant Azure AD.
* Gestisce il consenso dell'utente durante il tempo di richiesta del token e facilita il provisioning dinamico delle app tra i tenant

Il consenso è il processo di un proprietario di risorse che concede l'autorizzazione a un'applicazione client per accedere alle risorse protette, in autorizzazioni specifiche, per conto del proprietario della risorsa. Piattaforma di identità Microsoft:

* Consente a utenti e amministratori di concedere o negare dinamicamente il consenso all'app di accedere alle risorse per loro conto.
* Consente agli amministratori di decidere quali app sono autorizzati a eseguire e quali utenti possono usare app specifiche, nonché la modalità di accesso alle risorse di directory.

Nella piattaforma di identità Microsoft, un **oggetto applicazione** descrive un'applicazione come entità astratta. In fase di distribuzione, la piattaforma Microsoft Identity usa l'oggetto applicazione come progetto per creare un' **entità servizio**, che rappresenta un'istanza concreta di un'applicazione all'interno di una directory o di un tenant. L'entità servizio definisce le operazioni che l'app può effettivamente eseguire in una directory di destinazione specifica, chi può usarla, a quali risorse ha accesso e così via. La piattaforma Microsoft Identity crea un'entità servizio da un oggetto applicazione tramite il **consenso**.

Il diagramma seguente illustra un flusso di provisioning di Microsoft Identity Platform semplificato basato su consenso. Mostra due tenant (A e B). Il tenant A è proprietario dell'applicazione. Il tenant B sta creando un'istanza dell'applicazione tramite un'entità servizio.  

![Flusso di provisioning semplificato basato su consenso](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

In questo flusso di provisioning:

1. Un utente del tenant B prova ad accedere con l'app, l'endpoint di autorizzazione richiede un token per l'applicazione.
1. Le credenziali utente vengono acquisite e verificate per l'autenticazione.
1. All'utente viene richiesto di fornire il consenso affinché l'app ottenga l'accesso al tenant B.
1. La piattaforma Microsoft Identity usa l'oggetto applicazione nel tenant A come progetto per la creazione di un'entità servizio nel tenant B.
1. L'utente riceve il token richiesto.

È possibile ripetere questo processo per tenant aggiuntivi. Il tenant A mantiene il progetto per l'app (oggetto applicazione). Gli utenti e gli amministratori di tutti gli altri tenant in cui l'app dispone del consenso continuano a controllare ciò che l'applicazione può eseguire tramite l'oggetto entità servizio corrispondente in ogni tenant. Per altre informazioni, vedere [Oggetti applicazione e oggetti entità servizio in Microsoft Identity Platform](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Flusso di accesso all'app Web con Azure AD

Quando un utente accede al browser a un'app Web, si verifica quanto segue:

- L'app Web determina se l'utente è autenticato.
- Se l'utente non è autenticato, l'app Web delega a Azure AD per l'accesso dell'utente. Tale accesso sarà conforme ai criteri dell'organizzazione, che possono significare richiedere all'utente di immettere le proprie credenziali, usando l'autenticazione a più fattori o non usando una password, ad esempio usando Windows Hello.
- All'utente viene richiesto di fornire il consenso per l'accesso necessario all'app client. Questo è il motivo per cui le app client devono essere registrate con Azure AD, in modo che Azure AD possibile recapitare i token che rappresentano l'accesso che l'utente ha acconsentito.

Quando l'utente è stato autenticato correttamente:

- Azure AD invia un token all'app Web.
- Un cookie viene salvato, associato al dominio Azure AD, che contiene l'identità dell'utente nel file jar del browser. Al successivo uso del browser da parte di un'app per passare all'endpoint di autorizzazione Azure AD, il browser presenta il cookie in modo che l'utente non debba eseguire di nuovo l'accesso. Questo è anche il modo in cui si raggiunge SSO. Il cookie viene generato da Azure AD e può essere compreso solo da Azure AD.
- Il token viene quindi convalidato dall'app Web. Se la convalida ha esito positivo, l'app Web Visualizza la pagina protetta e salva un cookie di sessione nel file jar dei cookie del browser. Quando l'utente passa a un'altra pagina, l'app Web sa che l'utente è autenticato in base al cookie di sessione.

Il diagramma di sequenza seguente riepiloga questa interazione:

![processo di autenticazione dell'app Web](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Modo in cui un'app Web determina se l'utente è autenticato

Gli sviluppatori di app Web possono indicare se tutte o solo determinate pagine richiedono l'autenticazione. Ad esempio, in ASP.NET/ASP.NET Core questa operazione viene eseguita aggiungendo l'attributo `[Authorize]` alle azioni del controller. 

Questo attributo fa in modo che ASP.NET verifichi la presenza di un cookie di sessione contenente l'identità dell'utente. Se non è presente un cookie, ASP.NET reindirizza l'autenticazione al provider di identità specificato. Se il provider di identità è Azure AD, l'app Web reindirizza l'autenticazione a https://login.microsoftonline.com, che visualizza una finestra di dialogo di accesso.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Delega dell'accesso a un'app Web per Azure AD e ottenere un token

L'autenticazione utente avviene tramite il browser. Il protocollo OpenID utilizza messaggi di protocollo HTTP standard.
- L'app Web invia un HTTP 302 (Reindirizzamento) al browser per usare Azure AD.
- Quando l'utente viene autenticato, Azure AD invia il token all'app Web usando un reindirizzamento tramite il browser.
- Il reindirizzamento viene fornito dall'app Web sotto forma di URI di reindirizzamento. Questo URI di reindirizzamento viene registrato con l'oggetto applicazione Azure AD. Possono essere presenti diversi URI di reindirizzamento perché l'applicazione può essere distribuita in diversi URL. Quindi, anche l'app Web dovrà specificare l'URi di Reindirizzamento da usare.
- Azure AD verifica che l'URI di reindirizzamento inviato dall'app Web sia uno degli URI di reindirizzamento registrati per l'app.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Flusso di accesso all'app desktop e per dispositivi mobili con Azure AD

Il flusso descritto in precedenza si applica, con lievi differenze, alle applicazioni desktop e per dispositivi mobili.

Le applicazioni desktop e per dispositivi mobili possono utilizzare un controllo Web incorporato o un browser di sistema per l'autenticazione. Il diagramma seguente illustra il modo in cui un'app desktop o per dispositivi mobili USA Microsoft Authentication Library (MSAL) per acquisire i token di accesso e chiamare le API Web.

![App desktop come sembra](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL usa un browser per ottenere i token e, come per le app Web, delega l'autenticazione ai Azure AD.

Poiché Azure AD Salva lo stesso cookie di identità nel browser come per le app Web, se l'app nativa o per dispositivi mobili usa il browser di sistema, riceverà immediatamente SSO con l'app Web corrispondente.

Per impostazione predefinita, MSAL usa il browser di sistema ad eccezione di .NET Framework applicazioni desktop in cui viene usato un controllo incorporato per offrire un'esperienza utente più integrata.

## <a name="next-steps"></a>Passaggi successivi

Per acquisire familiarità con i termini comuni, vedere il [Glossario per sviluppatori Microsoft Identity Platform](developer-glossary.md) .
Vedere [flussi di autenticazione e scenari di app](authentication-flows-app-scenarios.md) per altre informazioni su altri scenari per l'autenticazione degli utenti supportati dalla piattaforma di identità Microsoft.
Per informazioni sulle librerie Microsoft che consentono di sviluppare applicazioni che interagiscono con account Microsoft, Azure AD account e Azure AD B2C utenti in un unico modello di programmazione semplificato, vedere [librerie MSAL](msal-overview.md) .
