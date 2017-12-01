---
title: Migrazione dal Servizio di controllo di accesso di Azure
description: Opzioni per lo spostamento di app e servizi dal Servizio di controllo di accesso di Azure | Microsoft Azure
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: e32cac7feda929a63c4a80fc0078b221117eb2b5
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/18/2017
---
# <a name="migrating-from-azure-access-control-service-acs"></a>Migrazione dal Servizio di controllo di accesso di Azure

Il Servizio di controllo di accesso di Microsoft Azure Active Directory, noto anche come Servizio di controllo di accesso, verrà deprecato nel mese di novembre 2018.  Le applicazioni e i servizi che usano attualmente il Servizio di controllo di accesso devono eseguire la migrazione completa a un meccanismo di autenticazione prima di tale data. Questo documento illustra i consigli per i clienti correnti che pianificano la deprecazione dell'uso del Servizio di controllo di accesso. Se non si usa attualmente il Servizio di controllo di accesso, non è necessario eseguire alcuna azione.


## <a name="brief-acs-overview"></a>Breve panoramica del Servizio di controllo di accesso

Il Servizio di controllo di accesso è un servizio di autenticazione cloud che semplifica le procedure di autenticazione e autorizzazione degli utenti affinché venga loro concesso l'accesso a servizi e applicazioni Web, consentendo al contempo il factoring delle molte funzionalità di autenticazione e autorizzazione al di fuori del codice. Viene usato principalmente da sviluppatori e progettisti di client .NET, applicazioni Web ASP.NET e servizi Web WCF.

I casi d'uso per il Servizio di controllo di accesso possono essere suddivisi in tre categorie principali:

- Autenticazione per determinati servizi cloud Microsoft, tra cui bus di servizio di Azure, Dynamics CRM e altro ancora. Le applicazioni client possono ottenere token dal Servizio di controllo di accesso che può essere usato per l'autenticazione in questi servizi per eseguire diverse azioni.
- Aggiunta di autenticazione alle applicazioni Web, per le soluzioni personalizzate e per le soluzioni predefinite, ad esempio Sharepoint. Usando un'autenticazione "passiva" del Servizio di controllo di accesso, le applicazioni Web possono supportare l'accesso con account da Google, Facebook, Yahoo, Microsoft (Live ID), Azure Active Directory e AD FS.
- Protezione dei servizi Web personalizzati con token rilasciati dal Servizio di controllo di accesso. Usando un'autenticazione "attiva", i servizi Web possono assicurarsi che l'accesso sia consentito solo dai client noti che hanno eseguito l'autenticazione con il Servizio di controllo di accesso.

Questi casi d'uso e le rispettive strategie di migrazione consigliate vengono illustrati nelle sezioni seguenti. Nella maggior parte dei casi sono necessarie modifiche significative al codice per la migrazione delle app e dei servizi esistenti in tecnologie più recenti. È consigliabile iniziare a pianificare ed eseguire la migrazione immediatamente, per evitare qualsiasi rischio di interruzione o di tempo di inattività.

> [!WARNING]
> Nella maggior parte dei casi sono necessarie modifiche significative al codice per la migrazione delle app e dei servizi esistenti in tecnologie più recenti. È consigliabile iniziare a pianificare ed eseguire la migrazione immediatamente, per evitare qualsiasi rischio di interruzione o di tempo di inattività.

A livello di architettura il Servizio di controllo di accesso è costituito interamente dai componenti seguenti:

- Un servizio token di sicurezza, che riceve richieste di autenticazione e restituisce token di sicurezza.
- Il portale di Azure classico, che viene usato per la creazione, l'eliminazione e l'abilitazione/disabilitazione degli spazi dei nomi del Servizio di controllo di accesso.
- Un portale di gestione separato per il Servizio di controllo di accesso, che viene usato per la personalizzazione e la configurazione del comportamento di uno spazio dei nomi del Servizio di controllo di accesso.
- Un servizio di gestione, che può essere usato per automatizzare le funzioni dei portali.
- Un motore di regole di trasformazione dei token, che può essere usato per definire logica complessa per la manipolazione del formato di output dei token rilasciati dal Servizio di controllo di accesso.

Per usare questi componenti, è necessario creare uno o più **spazi dei nomi** del Servizio di controllo di accesso. Uno spazio dei nomi è un'istanza dedicata del Servizio di controllo di accesso con cui comunicano le applicazioni e i servizi. È isolato da tutti gli altri clienti del Servizio di controllo di accesso, che usano i propri spazi dei nomi. Uno spazio dei nomi nel Servizio di controllo di accesso ha un URL dedicato, ad esempio:

```HTTP
https://mynamespace.accesscontrol.windows.net
```

Tutte le comunicazioni con il servizio token di sicurezza e tutte le operazioni di gestione vengono eseguite in corrispondenza di questo URL, con percorsi diversi per le diverse finalità. Per determinare se le applicazioni o i servizi usano il Servizio di controllo di accesso, monitorare eventuale traffico verso `https://{namespace}.accesscontrol.windows.net`.  Il traffico verso questo URL è il traffico gestito dal Servizio di controllo di accesso e deve essere sospeso.  L'unica eccezione è costituita da eventuale traffico verso `https://accounts.accesscontrol.windows.net`. Il traffico verso questo URL è già gestito da un servizio diverso e non è interessato dalla deprecazione del Servizio di controllo di accesso.  Occorre assicurarsi di accedere al portale di Azure classico e verificare se sono presenti spazi dei nomi del Servizio di controllo di accesso nelle sottoscrizioni di cui si è proprietari.  Gli spazi dei nomi del Servizio di controllo di accesso sono elencati nel servizio **Active Directory** nella scheda **Spazi dei nomi di Controllo di accesso**.

Per altre informazioni sul Servizio di controllo di accesso, vedere [questa documentazione archiviata su MSDN](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="retirement-schedule"></a>Pianificazione del ritiro

A partire dal mese di novembre 2017, tutti i componenti del Servizio di controllo di accesso sono completamente supportati e operativi. L'unica restrizione è costituita dal fatto che i [nuovi spazi dei nomi del Servizio di controllo di accesso non possono essere creati tramite il portale di Azure classico](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

La sequenza temporale per la deprecazione di questi componenti segue questa pianificazione:

- **Novembre 2017**: l'esperienza di amministrazione di Azure AD nel portale di Azure classico [è stata ritirata](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). A questo punto la gestione degli spazi dei nomi per il Servizio di controllo di accesso sarà disponibile presso il nuovo URL dedicato `http://manage.windowsazure.com?restoreClassic=true`. Questo approccio consente di visualizzare gli spazi dei nomi esistenti, abilitarli/disabilitarli ed eliminarli interamente, se necessario.
- **Aprile 2018**: la gestione degli spazi dei nomi del Servizio di controllo di accesso non è più disponibile tramite questo URL dedicato. A questo punto non è possibile disabilitare/abilitare, eliminare o enumerare gli spazi dei nomi del Servizio di controllo di accesso. Il portale di gestione del Servizio di controllo di accesso, tuttavia, è completamente funzionante e disponibile all'indirizzo `https://{namespace}.accesscontrol.windows.net`. Anche tutti gli altri componenti del Servizio di controllo di accesso continuano a funzionare normalmente.
- **Novembre 2018**: tutti i componenti del Servizio di controllo di accesso vengono arrestati in modo definitivo, inclusi il portale di gestione del Servizio di controllo di accesso, il servizio di gestione, il servizio token di sicurezza e il motore di regole di trasformazione dei token. A questo punto eventuali richieste inviate al Servizio di controllo di accesso, all'indirizzo `{namespace}.accesscontrol.windows.net`, hanno esito negativo. È necessario eseguire la migrazione di tutte le app e di tutti i servizi esistenti in altre tecnologie molto prima di questo periodo di tempo.


## <a name="migration-strategies"></a>Strategie di migrazione

Le sezioni seguenti illustrano i consigli generali per la migrazione dal Servizio di controllo di accesso verso altre tecnologie Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Client dei servizi cloud Microsoft

Ogni servizio cloud Microsoft che accetta token rilasciati dal Servizio di controllo di accesso supporta ora almeno una forma alternativa di autenticazione. Il meccanismo di autenticazione corretto dipende da ogni servizio. È quindi consigliabile vedere la documentazione specifica di ogni servizio per ottenere indicazioni ufficiali. Per comodità, ogni set di documentazione viene indicato di seguito:

| Service | Indicazioni |
| ------- | -------- |
| Bus di servizio di Azure | [Eseguire la migrazione alle firme di accesso condiviso](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Servizio di inoltro di Azure | [Eseguire la migrazione alle firme di accesso condiviso](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Cache di Azure | [Eseguire la migrazione a Cache Redis di Azure](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure Data Market | [Eseguire la migrazione alle API Servizi cognitivi](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Servizi BizTalk | [Eseguire la migrazione alle app per la logica di Azure](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Servizi multimediali di Azure | [Eseguire la migrazione all'autenticazione di Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Backup di Azure | [Eseguire l'aggiornamento di Azure Backup Agent](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-using-passive-authentication"></a>Applicazioni Web che usano l'autenticazione passiva

Per le applicazioni Web che usano il Servizio di controllo di accesso per l'autenticazione degli utenti, nel Servizio di controllo di accesso erano disponibili le funzionalità seguenti per sviluppatori e progettisti di applicazioni Web:

- Integrazione profonda con Windows Identity Foundation (WIF).
- Federazione con account Google, Facebook, Yahoo, Microsoft (Live ID), Azure Active Directory e AD FS.
- Supporto per i protocolli di autenticazione seguenti: OAuth 2.0 bozza 13, Ws-Trust e Ws-Federation.
- Supporto per i formati di token seguenti: token JSON Web (JWT), SAML 1.1, SAML 2.0 e token Web semplice (SWT).
- Un'esperienza di individuazione dell'area di autenticazione principale, integrata in WIF, che consentiva agli utenti di scegliere il tipo di account da usare per l'accesso. Questa esperienza era ospitata dall'applicazione Web e completamente personalizzabile.
- Trasformazione dei token che consentiva la personalizzazione avanzata delle attestazioni ricevute dall'applicazione Web dal Servizio di controllo di accesso, tra cui:
    - Passaggio di attestazione dai provider di identità
    - Aggiunta di attestazioni personalizzate aggiuntive
    - Semplice logica if-then per il rilascio di attestazioni in determinate condizioni

Sfortunatamente non è disponibile un unico servizio che fornisca tutte queste funzionalità equivalenti.  È consigliabile valutare le funzionalità del Servizio di controllo di accesso effettivamente necessarie e quindi scegliere tra [**Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/) (Azure AD), [**Azure AD B2C**](https://azure.microsoft.com/services/active-directory-b2c/) o un altro servizio di autenticazione cloud.

#### <a name="migrating-to-azure-active-directory"></a>Migrazione ad Azure Active Directory

Un approccio da prendere in considerazione prevede l'integrazione delle app e dei servizi direttamente in Azure Active Directory. Azure AD è il provider di identità basato sul cloud per account Microsoft aziendali e dell'istituto di istruzione usato per Office 365, Azure e altro ancora. Offre funzionalità di autenticazione federata simili a quelle del Servizio di controllo di accesso, ma non supporta tutte le funzionalità del Servizio di controllo di accesso. L'esempio principale è costituito dalla federazione con i provider di identità di social networking, ad esempio Facebook, Google e Yahoo. Se gli utenti accedono con questi tipi di credenziali, Azure AD è sconsigliato. Non supporta inoltre necessariamente gli stessi protocolli di autenticazione del Servizio di controllo di accesso. Anche se il Servizio di controllo di accesso e Azure AD supportano OAuth, ad esempio, esistono differenze minime tra ogni implementazione che richiedono la modifica del codice come parte della migrazione.

Azure AD offre tuttavia alcuni vantaggi potenziali ai clienti del Servizio di controllo di accesso. Supporta in modalità nativa gli account Microsoft aziendali e dell'istituto di istruzione ospitati sul cloud, usati in genere dai clienti del Servizio di controllo di accesso.  È anche possibile federare un tenant di Azure AD con una o più istanze locali di Active Directory tramite AD FS, consentendo all'app di eseguire l'autenticazione di utenti basati sul cloud e di utenti ospitati in locale.  Supporta inoltre il protocollo Ws-Federation, che rende relativamente semplice l'integrazione con un'applicazione Web tramite Windows Identity Foundation (WIF).

La tabella seguente confronta le funzionalità del Servizio di controllo di accesso rilevanti per le applicazioni Web con le funzionalità disponibili in Azure AD. A livello generale, **Azure Active Directory è probabilmente la scelta migliore per la migrazione se si consente agli utenti di accedere solo con i rispettivi account Microsoft aziendali e dell'istituto di istruzione**.

| Funzionalità | Supporto tecnico per il Servizio di controllo di accesso | Supporto tecnico per Azure AD |
| ---------- | ----------- | ---------------- |
| **Tipi di account** | | |
| Account Microsoft aziendali e dell'istituto di istruzione | Supportato | Supportato |
| Account da Active Directory di Windows Server e AD FS | Supportato tramite federazione con un tenant di Azure AD <br /> Supportato tramite federazione diretta con AD FS | Supportato solo tramite la federazione con un tenant di Azure AD | 
| Account da altri sistemi di gestione delle identità aziendali | Possibile tramite la federazione con un tenant di Azure AD <br />Supportato tramite federazione diretta | Possibile tramite la federazione con un tenant di Azure AD |
| Account Microsoft per uso personale (Windows Live ID) | Supportato | Supportato tramite il protocollo OAuth v2.0 di Azure AD, ma non tramite altri protocolli | 
| Account Facebook, Google, Yahoo | Supportato | Non supportato in alcun modo |
| **Compatibilità con protocolli e SDK** | | |
| Windows Identity Foundation (WIF) | Supportato | Supportato ma sono disponibili istruzioni limitate |
| Ws-Federation | Supportato | Supportato |
| OAuth 2.0 | Supporto per la bozza 13 | Supporto per RFC 6749, la specifica più moderna |
| Ws-Trust | Supportato | Non supportate |
| **Formati del token** | | |
| Token JSON Web (JWT) | Supportato nella versione Beta | Supportato |
| Token SAML 1.1 | Supportato | Supportato |
| Token SAML 2.0 | Supportato | Supportato |
| Token Web semplici (SWT) | Supportato | Non supportato |
| **Personalizzazioni** | | |
| Individuazione dell'area di autenticazione principale/interfaccia utente per la scelta dell'account personalizzabili | Codice scaricabile che può essere incorporato nelle app | Non supportate |
| Caricare certificati per la firma di token personalizzati | Supportato | Supportato |
| Personalizzare le attestazioni nei token | Pass-through delle attestazioni di input dai provider di identità<br />Ottenere un token di accesso dal provider di identità come attestazione<br />Rilasciare attestazioni di output in base ai valori delle attestazioni di input<br />Rilasciare attestazioni di output con valori costanti | Non è possibile eseguire il pass-through di attestazioni da provider di identità federati<br />Non è possibile ottenere un token di accesso dal provider di identità come attestazione<br />Non è possibile rilasciare attestazioni di output in base ai valori delle attestazioni di input<br />È possibile rilasciare attestazioni di output con valori costanti<br />È possibile rilasciare attestazioni di output in base alle proprietà degli utenti sincronizzati con Azure AD |
| **Automazione** | | |
| Automatizzare le attività di configurazione/gestione | Supportato tramite il servizio di gestione del Servizio di controllo di accesso | Supportato tramite Microsoft Graph e l'API Graph di Azure AD. |

Se si decide che Azure AD è l'approccio ottimale per le applicazioni e i servizi in uso, è necessario conoscere le due modalità disponibili per l'integrazione dell'app con Azure AD.

Per usare Ws-Federation/WIF per l'integrazione con Azure AD, è consigliabile seguire [l'approccio illustrato in questo articolo](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Questo articolo fa riferimento alla configurazione di Azure AD per l'accesso Single Sign-On basato su SAML ma è applicabile anche alla configurazione di Ws-Federation. Questo approccio richiede una licenza di Azure AD Premium, ma offre due vantaggi:

- Si ottiene la flessibilità completa della personalizzazione dei token di Azure AD. Ciò consente di personalizzare le attestazioni rilasciate da Azure AD in modo che corrispondano a quelle rilasciate dal Servizio di controllo di accesso, in particolare per quanto riguarda l'ID utente o l'attestazione Identificatore nome. È necessario assicurarsi che gli ID utente rilasciati da Azure AD corrispondano a quelli rilasciati dal Servizio di controllo di accesso, in modo che sia possibile continuare a ricevere identificatori utente coerenti per gli utenti anche dopo il passaggio a tecnologie diverse.
- È possibile configurare un certificato per la firma di token specifico per l'applicazione e controllarne la durata.

<!--

Possible nameIdentifiers from ACS (via AAD or ADFS):
- ADFS - Whatever ADFS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> L'uso di questo approccio richiede una licenza di Azure AD Premium. Se si è un cliente del Servizio di controllo di accesso e si necessita di una licenza Premium per la configurazione dell'accesso Single Sign-On per un'applicazione, contattare Microsoft per ottenere una licenza per sviluppatori.

Un approccio alternativo consiste nel seguire [questo esempio di codice](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), che fornisce istruzioni leggermente diverse sulla configurazione di Ws-Federation. L'esempio di codice non usa WIF, ma il middleware OWIN di ASP.NET 4.5. Le istruzioni per la registrazione dell'app, tuttavia, sono valide per le app che usano WIF e non richiedono una licenza di Azure AD Premium.  Se si sceglie questo approccio, è necessario comprendere il [rollover della chiave di firma in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Questo approccio usa la chiave di firma globale di Azure AD per rilasciare token. Per impostazione predefinita, WIF non aggiorna automaticamente le chiavi di firma. Quando Azure AD ruota le rispettive chiavi di firma globali, l'implementazione di WIF deve essere preparata per l'accettazione delle modifiche.

Se si riesce a eseguire l'integrazione con Azure AD tramite OpenID Connect o i protocolli OAuth, è consigliabile scegliere questo approccio.  Documentazione e indicazioni complete sull'integrazione di Azure AD nell'applicazione Web sono disponibili nella [Guida per gli sviluppatori di Azure AD](http://aka.ms/aaddev).

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrating-to-azure-ad-b2c"></a>Migrazione ad Azure AD B2C

L'altro approccio da prendere in considerazione per la migrazione è relativo ad Azure AD B2C.  B2C è un servizio di autenticazione cloud che, analogamente al Servizio di controllo di accesso, consente agli sviluppatori di eseguire l'outsourcing della logica di autenticazione e di gestione delle identità in un servizio cloud.  Si tratta di un servizio a pagamento, con un livello gratuito e Premium, progettato per applicazioni rivolte ai clienti che possono avere fino a milioni di utenti attivi.

Analogamente al Servizio di controllo di accesso, una delle funzionalità più attraenti di B2C è costituita dal supporto di molti tipi di account diversi. Con B2C è possibile consentire agli utenti di accedere con i rispettivi account Facebook, Google, Microsoft, LinkedIn, GitHub, Yahoo e altro ancora. B2C supporta inoltre "account locali" o nome utente e password creati in modo specifico dagli utenti per l'applicazione. Azure AD B2C offre anche un'estendibilità avanzata, che consente di personalizzare i flussi di accesso. Non supporta tuttavia tutti i protocolli di autenticazione e i formati di token che potrebbero essere richiesti dai clienti del Servizio di controllo di accesso. Non è inoltre possibile usarlo per ottenere token ed eseguire query per ottenere informazioni aggiuntive sull'utente dal provider di identità, Microsoft o altro.

La tabella seguente confronta le funzionalità del Servizio di controllo di accesso rilevanti per le applicazioni Web con le funzionalità disponibili in Azure AD B2C. A livello generale, **Azure AD B2C è probabilmente la scelta ottimale per la migrazione se l'applicazione è rivolta ai consumer o se supporta molti tipi diversi di account**.

| Funzionalità | Supporto tecnico per il Servizio di controllo di accesso | Supporto per Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Tipi di account** | | |
| Account Microsoft aziendali e dell'istituto di istruzione | Supportato | Supportato tramite criteri personalizzati  |
| Account da Active Directory di Windows Server e AD FS | Supportato tramite federazione diretta con AD FS | Supportato tramite federazione SAML con criteri personalizzati |
| Account da altri sistemi di gestione delle identità aziendali | Supportato tramite federazione diretta su Ws-Federation | Supportato tramite federazione SAML con criteri personalizzati |
| Account Microsoft per uso personale (Windows Live ID) | Supportato | Supportato | 
| Account Facebook, Google, Yahoo | Supportato | Facebook e Google supportati in modalità nativa, Yahoo supportato tramite la federazione di OpenID Connect con criteri personalizzati |
| **Compatibilità con protocolli e SDK** | | |
| Windows Identity Foundation (WIF) | Supportato | Non supportati. |
| Ws-Federation | Supportato | Non supportati. |
| OAuth 2.0 | Supporto per la bozza 13 | Supporto per RFC 6749, la specifica più moderna |
| Ws-Trust | Supportato | Non supportati. |
| **Formati del token** | | |
| Token JSON Web (JWT) | Supportato nella versione Beta | Supportato |
| Token SAML 1.1 | Supportato | Non supportate |
| Token SAML 2.0 | Supportato | Non supportate |
| Token Web semplici (SWT) | Supportato | Non supportate |
| **Personalizzazioni** | | |
| Individuazione dell'area di autenticazione principale/interfaccia utente per la scelta dell'account personalizzabili | Codice scaricabile che può essere incorporato nelle app | Interfaccia utente completamente personalizzabile tramite CSS personalizzati. |
| Caricare certificati per la firma di token personalizzati | Supportato | Chiavi di accesso personalizzate, non certificati, supportati tramite criteri personalizzati |
| Personalizzare le attestazioni nei token | Pass-through delle attestazioni di input dai provider di identità<br />Ottenere un token di accesso dal provider di identità come attestazione<br />Rilasciare attestazioni di output in base ai valori delle attestazioni di input<br />Rilasciare attestazioni di output con valori costanti | È possibile eseguire il pass-through di attestazione dai provider di identità. Criteri personalizzati obbligatori per alcune attestazioni.<br />Non è possibile ottenere un token di accesso dal provider di identità come attestazione<br />È possibile rilasciare attestazioni di output in base ai valori delle attestazioni di input tramite criteri personalizzati<br />È possibile rilasciare attestazioni di output con valori costanti tramite criteri personalizzati |
| **Automazione** | | |
| Automatizzare le attività di configurazione/gestione | Supportato tramite il servizio di gestione del Servizio di controllo di accesso | Creazione di utenti consentita tramite l'API Graph di Azure AD. Non è possibile creare tenant, applicazioni o criteri di B2C a livello di codice. |

Se si decide che Azure AD B2C è l'approccio ottimale per le applicazioni e i servizi, iniziare a vedere le risorse seguenti:

- [Documentazione di Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Criteri personalizzati di Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Prezzi di Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="other-migration-options"></a>Altre opzioni di migrazione

Se Azure AD o Azure AD B2C non soddisfano le esigenze dell'applicazione Web, contattare Microsoft per ottenere informazioni utili per identificare l'approccio ottimale.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD nor Azure AD B2C is sufficient to replace ACS in your web applications without making major code changes.  Some common examples might include:

- web applications using WIF and/or WS-Federation for sign-in with social identity providers such as Google or Facebook.
- web applications performing direct federation to an enterprise IDP over the Ws-Federation protocol.
- web applications that require the access token issued by a social identity provider (such as Google or Facebook) as a claim in the tokens issued by ACS.
- web applications with complex token transformation rules that Azure AD or Azure AD B2C cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

<!--

## Sharepoint 2010, 2013, 2016

TODO: AAD only, use AAD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-using-active-authentication"></a>Servizi Web con autenticazione attiva

Per i servizi Web protetti con i token rilasciati dal Servizio di controllo di accesso, erano disponibili le funzionalità seguenti:

- Possibilità di registrare una o più **identità del servizio** nello spazio dei nomi del Servizio di controllo di accesso, che possono essere usate per richiedere token.
- Supporto per i protocolli OAuth WRAP e OAuth 2.0 bozza 13 per la richiesta di token tramite i tipi di credenziali seguenti:
    - Semplice password creata per l'identità del servizio
    - Token Web semplice firmato che usa una chiave simmetrica o un certificato X509
    - Token SAML rilasciato da un provider di identità attendibile (in genere un'istanza di AD FS)
- Supporto per i formati di token seguenti: token JSON Web (JWT), SAML 1.1, SAML 2.0 e token Web semplice (SWT).
- Regole di trasformazione dei token semplici

Le identità del servizio nel Servizio di controllo di accesso sono in genere usate per l'implementazione dell'autenticazione di tipo S2S (server-to-server).  

#### <a name="migrating-to-azure-active-directory"></a>Migrazione ad Azure Active Directory

Per questo tipo di flusso di autenticazione è consigliabile eseguire la migrazione ad [**Azure Active Directory**](https://azure.microsoft.com/develop/identity/signin/) (Azure AD). Azure AD è il provider di identità basato sul cloud per account Microsoft aziendali e dell'istituto di istruzione usato per Office 365, Azure e altro ancora. Può essere usato anche per l'autenticazione S2S (server-to-server) tramite l'implementazione di Azure AD della concessione delle credenziali client OAuth.  La tabella seguente confronta le funzionalità del Servizio di controllo di accesso per l'autenticazione S2S (server-to-server) con quelle disponibili in Azure AD.

| Funzionalità | Supporto tecnico per il Servizio di controllo di accesso | Supporto tecnico per Azure AD |
| ---------- | ----------- | ---------------- |
| Come registrare un servizio Web | Creare un relying party nel portale di gestione del Servizio di controllo di accesso. | Creare un'applicazione Web di Azure AD nel portale di Azure. |
| Come registrare un client | Creare un'identità del servizio nel portale di gestione del Servizio di controllo di accesso. | Creare un'altra applicazione Web di Azure AD nel portale di Azure. |
| Protocollo usato | Protocollo OAuth WRAP<br />Concessione di credenziali client OAuth 2.0 bozza 13 | Concessione di credenziali client OAuth 2.0 |
| Metodi di autenticazione client | Password semplice<br />Token Web semplice firmato<br />Token SAML da IDP federato | Password semplice<br />Token JSON Web firmato |
| Formati del token | Token JSON Web<br />SAML 1.1<br />SAML 2.0<br />Token Web semplice<br /> | Solo token JSON Web |
| Trasformazione di token | Aggiungere attestazioni personalizzate<br />Logica semplice di emissione di attestazioni if-then | Aggiungere attestazioni personalizzate | 
| Automatizzare le attività di configurazione/gestione | Supportato tramite il servizio di gestione del Servizio di controllo di accesso | Supportato tramite Microsoft Graph e l'API Graph di Azure AD. |

Per indicazioni sull'implementazione di scenari S2S (server-to-server), vedere le risorse seguenti:

- [Sezione da servizio a servizio della Guida per sviluppatori di Azure AD](https://aka.ms/aaddev).
- [Esempio di codice Daemon che usa credenziali client semplici per la password](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Esempio di codice Daemon che usa credenziali client per il certificato](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="other-migration-options"></a>Altre opzioni di migrazione

Se Azure AD non soddisfa le esigenze del servizio Web, inviare un commento per ottenere assistenza nell'identificazione del piano ottimale per il caso specifico.

<!--

#### Migrating to Ping Identity or Auth0

In some cases, you may find that neither Azure AD's client credentials OAuth grant implementation is not sufficient to replace ACS in your architecture without major code changes.  Some common examples might include:

- server-to-server authentication using token formats other than JWTs.
- server-to-server authentication using an input token provided by an external identity provider.
- server-to-server authentication with token transformation rules that Azure AD cannot reproduce.

In these cases, you may want to consider migrating your web application to another cloud authentication service. We recommend exploring the following options, as each provides capabilities similar to ACS:

- [Auth0](https://auth0.com/) has created [high-level migration guidance for customers of ACS](https://auth0.com/blog/windows-azure-acs-alternative-replacement/), and provides a feature-by-feature comparison of ACS vs. Auth0.
- Enterprise customers should consider [Ping Identity](https://www.pingidentity.com) as well. Reach out to us and we can connect you with a representative from Ping who is prepared to help identify potential solutions.

Our aim in working with Ping Identity & Auth0 is to ensure that all ACS customers have a path forward for their apps & services that minimizes the amount of work required to move off of ACS.

-->

## <a name="questions-concerns--feedback"></a>Domande, dubbi e commenti

È possibile che molti clienti del Servizio di controllo di accesso non riescano a trovare un approccio chiaro per la migrazione tramite questo articolo e che potrebbero necessitare di assistenza o indicazioni per determinare il piano ottimale. In caso di domande sugli scenari di migrazione, inserire un commento in questa pagina.
