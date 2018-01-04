---
title: Eseguire la migrazione dal servizio di controllo di accesso di Azure | Documenti Microsoft
description: Opzioni per lo spostamento di applicazioni e servizi dal servizio di controllo di accesso di Azure
services: active-directory
documentationcenter: dev-center-name
author: dstrockis
manager: mtillman
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/14/2017
ms.author: dastrock
ms.openlocfilehash: f3de9016fe29a51ab2c7fb9e93fcd33af0f0e871
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 12/14/2017
---
# <a name="migrate-from-the-azure-access-control-service"></a>Eseguire la migrazione dal servizio di controllo di accesso di Azure

Access Control di Azure, un servizio di Azure Active Directory (Azure AD), verranno ritirati nel novembre 2018. Applicazioni e servizi che attualmente utilizzano il controllo di accesso devono eseguire la migrazione completa a un meccanismo di autenticazione diverso da allora. In questo articolo descrive le raccomandazioni per i clienti correnti, come si prevede di deprecare l'utilizzo del controllo di accesso. Se non si utilizza il controllo di accesso, è necessario intraprendere alcuna azione.


## <a name="overview"></a>Panoramica

Controllo di accesso è un servizio di autenticazione cloud che offre un modo semplice per autenticare e autorizzare gli utenti per l'accesso ai servizi e applicazioni web. Consente di molte funzionalità di autenticazione e autorizzazione al prenderle in considerazione fuori dal codice. Controllo di accesso viene utilizzato principalmente dagli sviluppatori e progettisti di client di Microsoft .NET, le applicazioni web ASP.NET e servizi web di Windows Communication Foundation (WCF).

Casi di utilizzo per il controllo di accesso possono essere suddivisi in tre categorie principali:

- L'autenticazione a determinati servizi cloud Microsoft, tra cui Azure Service Bus e Dynamics CRM. Le applicazioni client di ottenere i token da Access Control per l'autenticazione a questi servizi per eseguire varie azioni.
- Aggiunta di autenticazione per applicazioni web, predisporre (come SharePoint) e personalizzate. Tramite l'autenticazione "passivo" controllo di accesso, le applicazioni web possono supportare l'accesso con un account Microsoft (precedentemente Live ID) e con gli account di Google, Facebook, Yahoo!, Azure AD e Active Directory Federation Services (ADFS).
- La protezione dei servizi web personalizzati con i token rilasciati dal controllo di accesso. Tramite l'autenticazione "active", i servizi web possono verificare che consentono l'accesso solo ai client noti che sono stati autenticati con il controllo di accesso.

Ognuno di questi utilizzare case e la migrazione consigliata strategie sono descritte nelle sezioni seguenti. 

> [!WARNING]
> Nella maggior parte dei casi, sono necessarie modifiche significative al codice per eseguire la migrazione di applicazioni e servizi esistenti per le tecnologie più recenti. È consigliabile immediatamente iniziare la pianificazione ed eseguire la migrazione per evitare potenziali interruzioni o tempi di inattività.

Controllo di accesso include i componenti seguenti:

- Un servizio token di sicurezza (STS), che riceve le richieste di autenticazione e rilascia il token di sicurezza.
- Il portale di Azure classico, in cui si crea, Elimina e attivare e disattivare gli spazi dei nomi di controllo di accesso.
- Un controllo di accesso Gestione portale separato, in cui personalizzare e di configurare gli spazi dei nomi di controllo di accesso.
- Un servizio di gestione, è possibile utilizzare per automatizzare le funzioni dei portali.
- Una trasformazione dei token motore regole di business, che è possibile utilizzare per definire una logica complessa per modificare il formato di output di token che emette il controllo di accesso.

Per utilizzare questi componenti, è necessario creare uno o più spazi dei nomi di controllo di accesso. Oggetto *dello spazio dei nomi* è un'istanza dedicata di controllo di accesso che le applicazioni e servizi di comunicano con. Uno spazio dei nomi è isolata da tutti gli altri utenti di controllo di accesso. Altri clienti di controllo di accesso utilizzano spazi dei nomi specifici. Uno spazio dei nomi controllo di accesso dispone di un URL dedicato che è simile al seguente:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Tutte le comunicazioni con il servizio token di sicurezza e le operazioni di gestione vengono eseguite in corrispondenza dell'URL. Si utilizzano percorsi diversi per scopi diversi. Per determinare se le applicazioni o servizi utilizzano il controllo di accesso, verificare la presenza di qualsiasi tipo di traffico a https://\<dello spazio dei nomi\>. accesscontrol.windows.net. Tutto il traffico a questo URL viene gestito dal controllo di accesso e deve essere sospesa. 

L'eccezione a questo è tutto il traffico a https://accounts.accesscontrol.windows.net. Il traffico a questo URL è già gestito da un servizio diverso e non è interessato da questa deprecazione di controllo di accesso. 

È necessario anche accedere per il portale di Azure classico e il controllo per gli spazi dei nomi di controllo di accesso nelle sottoscrizioni a cui si è proprietari. Spazi dei nomi controllo di accesso sono elencate nella **spazi dei nomi controllo di accesso** nella scheda il **Active Directory** servizio.

Per ulteriori informazioni sul controllo di accesso, vedere [Access Control Service 2.0 (archiviati)](https://msdn.microsoft.com/library/hh147631.aspx).

## <a name="retirement-schedule"></a>Pianificazione di ritiro

A partire da novembre 2017, tutti i componenti di controllo di accesso sono completamente supportati e operativo. L'unica restrizione è che si [non è possibile creare nuovi spazi dei nomi di controllo di accesso tramite il portale di Azure classico](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Di seguito è riportata la pianificazione per la deprecazione di componenti di controllo di accesso:

- **Novembre 2017**: l'amministratore di Azure AD esperienza nel portale di Azure classico [viene ritirata](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). A questo punto, la gestione dello spazio dei nomi per il controllo di accesso è disponibile in un nuovo URL dedicato: http://manage.windowsazure.com?restoreClassic=true. Utilizzare questo URl per visualizzare gli spazi dei nomi esistente, abilitare e disabilitare gli spazi dei nomi ed eliminare gli spazi dei nomi, se si sceglie di.
- **2018 aprile**: gestione dello spazio dei nomi di controllo di accesso non è più disponibile all'indirizzo http://manage.windowsazure.com?restoreClassic=true dedicato. A questo punto, è Impossibile disabilitare o abilitare, eliminare o enumerare gli spazi dei nomi di controllo di accesso. Tuttavia, il portale di gestione di controllo di accesso sarà completamente funzionante e disponibile in https://\<dello spazio dei nomi\>. accesscontrol.windows.net. Tutti gli altri componenti di controllo di accesso continuano a funzionare normalmente.
- **2018 novembre**: componenti di tutti i controlli di accesso sono arrestare in modo permanente. Sono inclusi il portale di gestione di controllo di accesso, il servizio di gestione, servizio token di sicurezza e il motore regole di trasformazione dei token. A questo punto, tutte le richieste inviate al controllo di accesso (in \<dello spazio dei nomi\>. accesscontrol.windows.net) esito negativo. Deve avere la migrazione tutte le applicazioni e servizi esistenti e altre tecnologie prima di questo momento.


## <a name="migration-strategies"></a>Strategie di migrazione

Le sezioni seguenti descrivono le indicazioni generali per la migrazione dal controllo di accesso e altre tecnologie Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Client dei servizi cloud Microsoft

Ogni servizio cloud Microsoft che accetta i token rilasciati dal controllo di accesso ora supporta almeno un formato alternativo per l'autenticazione. Il meccanismo di autenticazione corretta varia per ogni servizio. Si consiglia di consultare la documentazione specifica per ogni servizio per linee guida ufficiali. Per comodità, ogni set di documentazione viene indicato di seguito:

| Service | Indicazioni |
| ------- | -------- |
| Bus di servizio di Azure | [Eseguire la migrazione a firme di accesso condiviso](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-migrate-acs-sas) |
| Inoltro del bus di servizio di Azure | [Eseguire la migrazione a firme di accesso condiviso](https://docs.microsoft.com/azure/service-bus-relay/relay-migrate-acs-sas) |
| Cache gestita di Azure | [Eseguire la migrazione a Cache Redis di Azure](https://docs.microsoft.com/azure/redis-cache/cache-faq#which-azure-cache-offering-is-right-for-me) |
| Azure DataMarket | [Eseguire la migrazione alle API Servizi cognitivi](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Servizi BizTalk | [Eseguire la migrazione della funzionalità di App per la logica del servizio App di Azure](https://docs.microsoft.com/azure/machine-learning/studio/datamarket-deprecation) |
| Servizi multimediali di Azure | [Eseguire la migrazione all'autenticazione di Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Backup di Azure | [Aggiornare l'agente di Backup di Azure](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: http://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->


### <a name="web-applications-that-use-passive-authentication"></a>Applicazioni Web che utilizzano l'autenticazione passiva

Per le applicazioni web che utilizzano il controllo di accesso per l'autenticazione utente, il controllo di accesso offre le seguenti caratteristiche e funzionalità agli architetti e sviluppatori di applicazioni web:

- Integrazione profonda con Windows Identity Foundation (WIF).
- Federazione con Facebook, Google, Yahoo, Azure Active Directory e AD FS e Microsoft.
- Supporto per i seguenti protocolli di autenticazione: Web Services Federation (WS-Federation), WS-Trust e OAuth 2.0 Draft 13.
- Supporto per i formati di token seguenti: JSON Web Token (JWT), SAML 1.1, SAML 2.0 e SWT Simple Web Token ().
- Un'esperienza di individuazione dell'area di autenticazione, integrata in WIF, che consente agli utenti di selezionare il tipo di account che hanno usato per accedere. Questa esperienza è ospitata dall'applicazione web di ed è completamente personalizzabile.
- Token di trasformazione che consente la personalizzazione avanzata delle attestazioni ricevuti dall'applicazione web da Access Control, tra cui:
    - Passare le attestazioni dai provider di identità.
    - Aggiunta di attestazioni personalizzate aggiuntive.
    - Logica if-then semplice per rilasciare le attestazioni in determinate condizioni.

Purtroppo, non è disponibile un servizio che offre tutte queste funzionalità equivalente. È necessario valutare le funzionalità di controllo di accesso necessarie e quindi scegliere tra l'utilizzo di [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C), o l'autenticazione di un altro cloud servizio.

#### <a name="migrate-to-azure-active-directory"></a>Eseguire la migrazione ad Azure Active Directory

Un percorso da considerare è l'integrazione di App e servizi direttamente con Azure AD. Azure AD è il provider di identità basato sul cloud per Microsoft di lavoro o scuola account. Azure AD è il provider di identità per Office 365, Azure e molto altro ancora. Fornisce simile federata le funzionalità di autenticazione per il controllo di accesso, ma non supporta tutte le funzionalità di controllo di accesso. 

L'esempio principale è costituito dalla federazione con i provider di identità di social networking, ad esempio Facebook, Google e Yahoo. Se si accede con questi tipi di credenziali, Azure AD non è la soluzione. 

Azure AD non necessariamente supporta inoltre i protocolli di autenticazione stesso esatto come controllo di accesso. Ad esempio, anche se sia il controllo di accesso e Azure AD supporta OAuth, esistono differenze minime tra ogni implementazione. Implementazioni diverse è necessario modificare il codice come parte di una migrazione.

Tuttavia, Azure AD offre numerosi vantaggi di potenziali clienti di controllo di accesso. È in modalità nativa supporta Microsoft o lavoro o scuola account ospitato nel cloud, che vengono comunemente utilizzate dai clienti di controllo di accesso. 

Un tenant di Azure AD può anche essere federato a uno o più istanze di locale di Active Directory tramite ADFS. In questo modo, l'app può autenticare gli utenti basati su cloud e gli utenti che sono ospitati in locale. Supporta inoltre il protocollo WS-Federation, che rende relativamente semplice per l'integrazione con un'applicazione web tramite WIF.

Nella tabella seguente vengono confrontate le funzionalità di controllo di accesso sono rilevanti per le applicazioni web con le funzionalità disponibili in Azure AD. 

In generale, *Azure Active Directory è probabilmente la scelta migliore per la migrazione, se si consente agli utenti di accedere in solo con Microsoft o lavoro o scuola account*.

| Funzionalità | Supporto per il controllo di accesso | Supporto tecnico di Azure AD |
| ---------- | ----------- | ---------------- |
| **Tipi di account** | | |
| Microsoft di lavoro o scuola account | Supportato | Supportato |
| Account di Windows Server Active Directory e AD FS |-Supportati tramite la federazione con tenant di Azure AD <br />-Supportate tramite federazione con ADFS | Supportato solo tramite la federazione con un tenant di Azure AD | 
| Account da altri sistemi di gestione delle identità aziendali |-Possibile tramite la federazione con tenant di Azure AD <br />-Supportati tramite il tipo di federazione | Possibile tramite la federazione con un tenant di Azure AD |
| Account Microsoft per uso personale | Supportato | Supportato tramite il protocollo OAuth versione 2.0 di Azure AD, ma non su altri protocolli | 
| Account Facebook, Google, Yahoo | Supportato | Non è supportato alcun tipo |
| **Protocolli e compatibilità SDK** | | |
| WIF | Supportato | Sono disponibili istruzioni supportate, ma limitate |
| WS-Federation | Supportato | Supportato |
| OAuth 2.0 | Supporto per la bozza 13 | Supporto per RFC 6749, la specifica di più recenti |
| WS-Trust | Supportato | Non supportate |
| **Formati di token** | | |
| Token JSON Web | Supportato nella versione Beta | Supportato |
| SAML 1.1 | Supportato | Preview |
| SAML 2.0 | Supportato | Supportato |
| Token Web semplice | Supportato | Non supportate |
| **Personalizzazioni** | | |
| Area di autenticazione principale personalizzabile dell'interfaccia utente di individuazione/account-prelievo | Codice scaricabile che può essere incorporato nelle app | Non supportate |
| Caricare i certificati di firma di token personalizzati | Supportato | Supportato |
| Personalizzare le attestazioni nei token |-Passare le attestazioni di input dai provider di identità<br />-Ottenere token di accesso da provider di identità come attestazione<br />: Consente di emettere attestazioni di output in base ai valori di attestazioni di input<br />: Consente di emettere attestazioni di output con valori di costanti |-Non è possibile passare le attestazioni dal provider di identità federativa<br />-Impossibile ottenere token di accesso da provider di identità come attestazione<br />-Non è possibile emettere attestazioni di output in base ai valori di attestazioni di input<br />-È possibile emettere attestazioni di output con valori di costanti<br />-Possono rilasciare attestazioni di output in base alle proprietà degli utenti sincronizzati con Azure AD |
| **Automazione** | | |
| Automatizzare le attività di configurazione e gestione | Supportati tramite il servizio di gestione di controllo di accesso | Supportato tramite Microsoft Graph e l'API Azure AD Graph |

Se si decide che Azure AD è il percorso di migrazione migliore per le applicazioni e servizi, è necessario essere consapevoli dei due modi per integrare l'app con Azure AD.

Per utilizzare WS-Federation o WIF per l'integrazione con Azure AD, è consigliabile l'approccio descritto in seguito [configurare single sign-on federato per un'applicazione non raccolta](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). L'articolo fa riferimento alla configurazione di Azure AD per basato su SAML single sign-on, ma funziona anche per la configurazione di WS-Federation. Questo approccio richiede una licenza Azure AD Premium. Questo approccio presenta due vantaggi:

- Si ottiene la flessibilità completa della personalizzazione dei token di Azure AD. È possibile personalizzare le attestazioni che vengono rilasciate da Azure AD per associare le attestazioni generate dal controllo di accesso. In particolare è inclusa l'attestazione di ID o identificatore di nome utente. Per continuare a ricevere gli identificatori utente coerente per gli utenti dopo aver modificato le tecnologie, verificare che gli ID utente emesso dalla corrispondenza di Azure AD quelli emessi da controllo di accesso.
- È possibile configurare un certificato di firma di token è specifico per l'applicazione e con una durata controllata.

<!--

Possible nameIdentifiers from Access Control (via AAD or AD FS):
- AD FS - Whatever AD FS is configured to send (email, UPN, employeeID, what have you)
- Default from AAD using App Registrations, or Custom Apps before ClaimsIssuance policy: subject/persistent ID
- Default from AAD using Custom apps nowadays: UPN
- Kusto can't tell us distribution, it's redacted

-->

> [!NOTE]
> Questo approccio richiede una licenza Azure AD Premium. Se si è un cliente di controllo di accesso ed è necessaria una licenza premium per la configurazione di single-sign-on per un'applicazione, contattare Microsoft. Saremo lieti di fornire licenze per sviluppatori per l'utilizzo.

Un approccio alternativo consiste nel seguire [in questo esempio di codice](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), che include istruzioni leggermente diverse per la configurazione di WS-Federation. L'esempio di codice non usa WIF, ma il middleware OWIN di ASP.NET 4.5. Tuttavia, le istruzioni per la registrazione dell'app sono valide per le app mediante WIF e non richiedono una licenza Azure AD Premium. 

Se si sceglie questo approccio, è necessario comprendere [rollover della chiave di firma in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Questo approccio usa la chiave di firma globale di Azure AD per rilasciare token. Per impostazione predefinita, WIF non aggiorna automaticamente le chiavi di firma. Quando Azure AD ruota le rispettive chiavi di firma globali, l'implementazione di WIF deve essere preparata per l'accettazione delle modifiche.

Se è possibile integrare con Azure AD tramite il protocollo OpenID Connect o OAuth, è consigliabile farlo. È disponibile un'ampia documentazione e istruzioni su come integrare l'applicazione web disponibile in Azure AD la [Guida per sviluppatori di Azure AD](http://aka.ms/aaddev).

<!-- TODO: If customers ask about authZ, let's put a blurb on role claims here -->

#### <a name="migrate-to-azure-active-directory-b2c"></a>Eseguire la migrazione a Azure Active Directory B2C

L'altro approccio da prendere in considerazione per la migrazione è relativo ad Azure AD B2C. Azure Active Directory B2C è un servizio di autenticazione cloud che, come controllo di accesso, consente agli sviluppatori di esternalizzare la logica di gestione identità e autenticazione a un servizio cloud. È un servizio a pagamento (con livelli gratuiti e premium) che è progettato per le applicazioni che potrebbero avere fino a milioni di utenti attivi per consumatori.

Come controllo di accesso, una delle funzionalità di Azure Active Directory B2C più interessante è che supporta molti tipi diversi di account. Con Azure Active Directory B2C, è possibile accedere gli utenti con account Microsoft o account Facebook, Google, LinkedIn, GitHub o Yahoo e altro ancora. Azure Active Directory B2C supporta anche "account locali", o nome utente e password che gli utenti di creare in modo specifico per l'applicazione. Azure Active Directory B2C fornisce inoltre l'estendibilità avanzati che è possibile utilizzare per personalizzare i flussi di accesso. 

Tuttavia, Azure Active Directory B2C non supporta la varietà di protocolli di autenticazione e il controllo di accesso potrebbero richiedere i clienti di formati di token. È inoltre possibile utilizzare Azure AD B2C per ottenere i token e query per ulteriori informazioni sull'utente dal provider di identità, Microsoft o in caso contrario.

Nella tabella seguente vengono confrontate le funzionalità di controllo di accesso sono rilevanti per le applicazioni web con quelle disponibili in Azure Active Directory B2C. A livello generale, *Azure AD B2C è probabilmente la scelta ottimale per la migrazione se l'applicazione è rivolta ai consumer o se supporta molti tipi diversi di account*.

| Funzionalità | Supporto per il controllo di accesso | Supporto tecnico di Azure Active Directory B2C |
| ---------- | ----------- | ---------------- |
| **Tipi di account** | | |
| Microsoft di lavoro o scuola account | Supportato | Supportato tramite criteri personalizzati  |
| Account di Windows Server Active Directory e AD FS | Supportato tramite federazione con ADFS | Supportato tramite federazione SAML usando criteri personalizzati |
| Account da altri sistemi di gestione delle identità aziendali | È supportata tramite la federazione diretta tramite WS-Federation | Supportato tramite federazione SAML usando criteri personalizzati |
| Account Microsoft per uso personale | Supportato | Supportato | 
| Account Facebook, Google, Yahoo | Supportato | Facebook e Google supportato in modalità nativa, Yahoo supportato tramite OpenID Connect federation usando criteri personalizzati |
| **Protocolli e compatibilità SDK** | | |
| Windows Identity Foundation (WIF) | Supportato | Non supportate |
| WS-Federation | Supportato | Non supportate |
| OAuth 2.0 | Supporto per la bozza 13 | Supporto per RFC 6749, la specifica di più recenti |
| WS-Trust | Supportato | Non supportate |
| **Formati di token** | | |
| Token JSON Web | Supportato nella versione Beta | Supportato |
| SAML 1.1 | Supportato | Non supportate |
| SAML 2.0 | Supportato | Non supportate |
| Token Web semplice | Supportato | Non supportate |
| **Personalizzazioni** | | |
| Area di autenticazione principale personalizzabile dell'interfaccia utente di individuazione/account-prelievo | Codice scaricabile che può essere incorporato nelle app | Interfaccia utente completamente personalizzabile tramite CSS personalizzati |
| Caricare i certificati di firma di token personalizzati | Supportato | Firme chiavi personalizzate, non di certificati, è supportate tramite i criteri personalizzati |
| Personalizzare le attestazioni nei token |-Passare le attestazioni di input dai provider di identità<br />-Ottenere token di accesso da provider di identità come attestazione<br />: Consente di emettere attestazioni di output in base ai valori di attestazioni di input<br />: Consente di emettere attestazioni di output con valori di costanti |-È possibile passare le attestazioni dal provider di identità. criteri personalizzati per alcune delle attestazioni<br />-Impossibile ottenere token di accesso da provider di identità come attestazione<br />-Possono rilasciare attestazioni di output in base ai valori di attestazioni di input tramite i criteri personalizzati<br />-È possibile emettere attestazioni di output con valori costanti tramite i criteri personalizzati |
| **Automazione** | | |
| Automatizzare le attività di configurazione e gestione | Supportati tramite il servizio di gestione di controllo di accesso |-Creazione di utenti consentiti tramite l'API Graph di Azure AD<br />-Impossibile creare tenant B2C, applicazioni o i criteri a livello di codice |

Se si decide che Azure Active Directory B2C è il percorso di migrazione migliore per le applicazioni e servizi, iniziare con le risorse seguenti:

- [Documentazione di Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview)
- [Criteri personalizzati di Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview-custom)
- [Prezzi di Azure Active Directory B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)


#### <a name="migrate-to-ping-identity-or-auth0"></a>Eseguire la migrazione a identità Ping o Auth0

In alcuni casi, è possibile che Azure AD e Azure AD B2C non è sufficiente sostituire il controllo di accesso nelle applicazioni web senza apportare modifiche al codice principale. Alcuni esempi comuni possono includere:

- Applicazioni Web che utilizzano WIF o WS-Federation per l'accesso con provider di identità di social networking, ad esempio Google o Facebook.
- Applicazioni Web che eseguono la federazione diretta di un'azienda identificano provider tramite il protocollo WS-Federation.
- Le applicazioni Web che richiedono un token di accesso rilasciato da un provider di identità di social networking (ad esempio Google o Facebook) come attestazione nei token emesso da Access Control.
- Applicazioni Web con le regole di trasformazione token complesse che, Azure Active Directory o Azure AD, non è possibile riprodurre B2C.
- Applicazioni web di multi-tenant che utilizzano ACS per la gestione centralizzata di federazione per molti provider di identità diversi

In questi casi, è necessario considerare la migrazione dell'applicazione web in un altro servizio di autenticazione cloud. Si consiglia di esplorare le opzioni seguenti. Ciascuna delle opzioni seguenti offrono funzionalità simili a controllo di accesso:



|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) è un servizio di identità cloud flessibile che ha creato [Guida alla migrazione di alto livello per i clienti di controllo di accesso](https://auth0.com/acs)e supporta quasi tutte le funzionalità che consente di ACS. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Identità ping](https://www.pingidentity.com) offre due soluzioni simili ad ACS. PingOne è un servizio di identità cloud che supporta molte delle stesse funzionalità di ACS e PingFederate è un prodotto di identità locale simile che offre una maggiore flessibilità. Fare riferimento a [informazioni aggiuntive sul ritiro di effettuare il Ping dell'ACS](https://www.pingidentity.com/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) per ulteriori informazioni sull'utilizzo di questi prodotti.  |

Il nostro obiettivo nell'utilizzo delle identità di Ping e Auth0 è per assicurare che tutti i clienti di controllo di accesso sono un percorso di migrazione per le applicazioni e servizi che riduce al minimo la quantità di lavoro necessario per passare dal controllo di accesso.

<!--

## Sharepoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Servizi Web che utilizzano l'autenticazione attiva

Per i servizi web sono protette con token emesso da Access Control, controllo di accesso offre le funzionalità seguenti:

- Consente di registrare uno o più *identità del servizio* nello spazio dei nomi di controllo di accesso. Identità del servizio possono essere usate per richiedere i token.
- Supporto di OAuth WRAP e OAuth 2.0 Draft 13 protocolli per la richiesta di token, utilizzando i seguenti tipi di credenziali:
    - Una password semplice creato per l'identità del servizio
    - SWT con segno con una chiave simmetrica o X509 certificato
    - Un token SAML rilasciato da un provider di identità attendibili (in genere, un'istanza di AD FS)
- Supporto per i formati di token seguenti: SWT, SAML 1.1, SAML 2.0 e JWT.
- Regole di trasformazione dei token semplice.

Identità del servizio controllo di accesso vengono in genere utilizzate per implementare l'autenticazione server a server.  

#### <a name="migrate-to-azure-active-directory"></a>Eseguire la migrazione ad Azure Active Directory

Per questo tipo di flusso di autenticazione è consigliabile eseguire la migrazione a [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Azure AD è il provider di identità basato sul cloud per Microsoft di lavoro o scuola account. Azure AD è il provider di identità per Office 365, Azure e molto altro ancora. 

Inoltre, è possibile utilizzare Azure AD per l'autenticazione server a server tramite l'implementazione di Azure AD della concessione di credenziali client OAuth. Nella tabella seguente confronta le funzionalità di controllo di accesso di autenticazione server a server con quelle disponibili in Azure AD.

| Funzionalità | Supporto per il controllo di accesso | Supporto tecnico di Azure AD |
| ---------- | ----------- | ---------------- |
| Come registrare un servizio Web | Creare una relying party nel portale di gestione di controllo di accesso | Creare un'applicazione web di Azure AD nel portale di Azure |
| Come registrare un client | Creare un'identità del servizio nel portale di gestione di controllo di accesso | Creare un'altra applicazione web di Azure AD nel portale di Azure |
| Protocollo usato |-Il protocollo OAuth WRAP<br />-Concessione di credenziali client OAuth 2.0 Draft 13 | Concessione di credenziali client OAuth 2.0 |
| Metodi di autenticazione client |-Password semplice<br />-SWT firmato<br />-Token SAML da un provider di identità federativa |-Password semplice<br />-JWT firmati |
| Formati del token |-JWT<br />-SAML 1.1<br />-SAML 2.0<br />-SWT<br /> | Solo token JSON Web |
| Trasformazione di token |-Aggiungere attestazioni personalizzate<br />-La logica di rilascio di attestazioni if-then semplice | Aggiungere attestazioni personalizzate | 
| Automatizzare le attività di configurazione e gestione | Supportati tramite il servizio di gestione di controllo di accesso | Supportato tramite Microsoft Graph e l'API Azure AD Graph |

Per linee guida sull'implementazione scenari server-server, vedere le risorse seguenti:

- La sezione Service to Service del [Guida per sviluppatori di Azure AD](https://aka.ms/aaddev)
- [Nell'esempio di codice daemon utilizzando le credenziali client di password semplici](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Nell'esempio di codice daemon utilizzando le credenziali del certificato client](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Eseguire la migrazione a identità Ping o Auth0

In alcuni casi, è possibile che le credenziali del client di Azure AD e OAuth concessione di implementazione non sono sufficienti per sostituire il controllo di accesso dell'architettura senza modifiche del codice principale. Alcuni esempi comuni possono includere:

- Autenticazione server a server utilizzando i formati dei token diverso da Jwt.
- Autenticazione server a server tramite un token di input fornito da un provider di identità esterna.
- Autenticazione server a server con regole di trasformazione dei token che non è possibile riprodurre Azure AD.

In questi casi, è possibile eseguire la migrazione dell'applicazione web in un altro servizio di autenticazione cloud. Si consiglia di esplorare le opzioni seguenti. Ciascuna delle opzioni seguenti offrono funzionalità simili a controllo di accesso:

|     |     | 
| --- | --- |
| ![Auth0](./media/active-directory-acs-migration/rsz_auth0.png) | [Auth0](https://auth0.com/acs) è un servizio di identità cloud flessibile che ha creato [Guida alla migrazione di alto livello per i clienti di controllo di accesso](https://auth0.com/acs)e supporta quasi tutte le funzionalità che consente di ACS. |
| ![Ping](./media/active-directory-acs-migration/rsz_ping.png) | [Identità ping](https://www.pingidentity.com) offre due soluzioni simili ad ACS. PingOne è un servizio di identità cloud che supporta molte delle stesse funzionalità di ACS e PingFederate è un prodotto di identità locale simile che offre una maggiore flessibilità. Fare riferimento a [informazioni aggiuntive sul ritiro di effettuare il Ping dell'ACS](https://www.pingidentity.com/company/blog/2017/11/20/migrating_from_microsoft_acs_to_ping_identity.html) per ulteriori informazioni sull'utilizzo di questi prodotti.  |

Il nostro obiettivo nell'utilizzo delle identità di Ping e Auth0 è per assicurare che tutti i clienti di controllo di accesso sono un percorso di migrazione per le applicazioni e servizi che riduce al minimo la quantità di lavoro necessario per passare dal controllo di accesso.

## <a name="questions-concerns-and-feedback"></a>Domande, problemi e suggerimenti

Siamo consapevoli che molti clienti di controllo di accesso non trovare un percorso di migrazione crittografato dopo aver letto questo articolo. Potrebbe essere necessario alcuni assistenza o linee guida per determinare il piano a destra. Se si desidera discutere gli scenari di migrazione e domande, lasciare un commento in questa pagina.
