---
title: Novità Note sulla versione - Azure Active Directory | Microsoft Docs
description: Informazioni sulle novità di Azure Active Directory, tra cui le note sulla versione più aggiornate, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti.
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f547d67dcb2880b0b51088bc17c43eb9436a0903
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "80369623"
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory

>Ricevere una notifica su quando rivedere questa pagina per gli `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` aggiornamenti ![copiando e](./media/whats-new/feed-icon-16x16.png) incollando questo URL: nel lettore di feed di feed RSS.

Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente. Se si cercano informazioni precedenti agli ultimi sei mesi, è possibile trovarli in [Archivio per le novità in Azure Active Directory](whats-new-archive.md).

---

## <a name="march-2020"></a>Marzo 2020

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Gli utenti con il ruolo di accesso predefinito saranno nell'ambito del provisioningUsers with the default access role will be in scope for provisioning

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Provisioning di app  
**Capacità del prodotto:** Gestione del ciclo di vita delle identitàIdentity Lifecycle Management
 
Storicamente, gli utenti con il ruolo di accesso predefinito non sono stati nell'ambito per il provisioning. Abbiamo ricevuto feedback sul fatto che i clienti desiderano che gli utenti con questo ruolo rientrino nell'ambito del provisioning. Stiamo lavorando alla distribuzione di una modifica in modo che tutte le nuove configurazioni di provisioning consentano agli utenti con il ruolo di accesso predefinito di essere provisioning. A poco a poco, cambieremo il comportamento per le configurazioni di provisioning esistenti per supportare il provisioning degli utenti con questo ruolo. Non è richiesta alcuna azione da parte del cliente. Pubblicheremo un aggiornamento alla nostra [documentazione](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) una volta che questa modifica sarà in atto.

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>La collaborazione B2B di Azure AD sarà disponibile in Microsoft Azure gestito da tenant 21Vianet (Azure China 21Vianet)

**Tipo:** Modifica pianificata  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
Le funzionalità di collaborazione B2B di Azure AD saranno rese disponibili in Microsoft Azure gestito da tenant 21Vianet (Azure China 21Vianet), consentendo agli utenti di un tenant di Azure China 21Vianet di collaborare senza problemi con gli utenti di altri tenant di Azure China 21Vianet. [Altre informazioni sulla collaborazione B2B di Azure AD](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Riprogettazione del messaggio di posta elettronica di invito di Azure AD B2B CollaborationAzure AD B2B Collaboration invitation redesign

**Tipo:** Modifica pianificata  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
I messaggi di [posta elettronica](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) inviati dal servizio di invito alla collaborazione B2B di Azure AD per invitare gli utenti alla directory verranno riprogettati per rendere chiare le informazioni sull'invito e i passaggi successivi dell'utente.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Le modifiche ai criteri HomeRealmDiscovery verranno visualizzate nei registri di controllo

**Tipo:** Correzione  
**Categoria di servizio:** revisione  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
È stato corretto un bug in cui le modifiche [ai criteri HomeRealmDiscovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) non sono state incluse nei log di controllo. Ora sarete in grado di vedere quando e come la politica è stata modificata, e da chi. 

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Collaborazione B2B di Azure AD disponibile nei tenant di Azure per enti pubbliciAzure AD B2B Collaboration available in Azure Government tenants

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
Le funzionalità di collaborazione B2B di Azure AD sono ora disponibili tra alcuni tenant di Azure per enti pubblici.  Per scoprire se il tenant è in grado di usare queste funzionalità, seguire le istruzioni in Come è possibile stabilire se la [collaborazione B2B è disponibile nel tenant di Azure US per enti pubblici?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure Monitor integration for Azure Logs is now available in Azure Government

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
L'integrazione di Monitoraggio di Azure con i log di Azure AD è ora disponibile in Azure per enti pubblici. È possibile instradare i log di Azure AD (registri di controllo e accesso) a un account di archiviazione, hub eventi e Log Analytics.You can route Azure AD Logs (Audit and Sign-in Logs) to a storage account, Event Hub and Log Analytics. Consultare la [documentazione dettagliata](https://aka.ms/aadlogsinamd) e i piani di [distribuzione per la creazione di report e il monitoraggio](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) per gli scenari di Azure AD.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Aggiornamento della protezione dell'identità in Azure per enti pubbliciIdentity Protection Refresh in Azure Government

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione dell'identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Siamo entusiasti di condividere che abbiamo ora implementato l'esperienza aggiornata di [Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs) nel [portale di Microsoft Azure per enti pubblici](https://portal.azure.us/). Per ulteriori informazioni, vedere il post di blog di [annuncio](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667).

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Ripristino di emergenza: scaricare e archiviare la configurazione del provisioningDisaster recovery: Download and store your provisioning configuration

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Capacità del prodotto:** Gestione del ciclo di vita delle identitàIdentity Lifecycle Management
 
Il servizio di provisioning di Azure AD offre un set completo di funzionalità di configurazione. I clienti devono essere in grado di salvare la propria configurazione in modo da potervi fare riferimento in un secondo momento o eseguire il rollback a una versione valida nota. Abbiamo aggiunto la possibilità di scaricare la configurazione di provisioning come file JSON e caricarla quando ne hai bisogno. [Scopri di più](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (self-service password reset) ora richiede due gate per gli amministratori in Microsoft Azure gestito da 21Vianet (Azure China 21Vianet) 

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Reimpostazione password self-service  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Precedentemente in Microsoft Azure gestito da 21Vianet (Azure China 21Vianet), gli amministratori che utilizzano la reimpostazione della password self-service (SSPR) per reimpostare le proprie password necessarie solo un "gate" (sfida) per dimostrare la propria identità. Nei cloud pubblici e in altri cloud nazionali, gli amministratori in genere devono utilizzare due gate per dimostrare la propria identità quando si usa SSPR. Ma poiché non sono supportate le chiamate SMS o telefoniche in Azure China 21Vianet, è stata consentita la reimpostazione della password di un solo gate da parte degli amministratori.

Stiamo creando la parità delle funzionalità SSPR tra Azure China 21Vianet e il cloud pubblico. In futuro, gli amministratori devono utilizzare due gate quando si usa SSPR. Saranno supportati SMS, telefonate e notifiche e codici dell'app Authenticator. [Scopri di più](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

---

### <a name="password-length-is-limited-to-256-characters"></a>La lunghezza della password è limitata a 256 caratteri

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Per garantire l'affidabilità del servizio Azure AD, le password utente sono ora limitate a 256 caratteri. Agli utenti con password più lunghe verrà chiesto di modificare la password al successivo accesso, contattando l'amministratore o utilizzando la funzionalità di reimpostazione della password self-service.

Questa modifica è stata abilitata il 13 marzo 2020, alle 10:00 PST (18:00 UTC) e l'errore è AADSTS 50052, InvalidPasswordExceedsMaxLength. Vedere [l'avviso](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) di modifica di rilievo per ulteriori dettagli.

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>I log di accesso di Azure AD sono ora disponibili per tutti i tenant gratuiti tramite il portale di AzureAzure AD sign-in logs are now available for all free tenants through the Azure portal

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
A partire da ora, i clienti che dispongono di tenant gratuiti possono accedere ai log di accesso di [Azure AD dal portale](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) di Azure per un massimo di 7 giorni. In precedenza, i log di accesso erano disponibili solo per i clienti con licenze di Azure Active Directory Premium.Previously, sign-in logs were available only for customers with Azure Active Directory Premium licenses. Con questa modifica, tutti i tenant possono accedere a questi log tramite il portale.

> [!NOTE]
> I clienti hanno ancora bisogno di una licenza Premium (Azure Active Directory Premium P1 o P2) per accedere ai log di accesso tramite Microsoft Graph API e Azure Monitor.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Opzione Deprecata dei gruppi a livello di directory da Impostazioni generali gruppi nel portale di AzureDeprecation of Directory-wide groups option from Groups General Settings on Azure portal

**Tipo:** Deprecato  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Per fornire ai clienti un modo più flessibile per creare gruppi a livello di directory che meglio soddisfano le loro esigenze, è stata sostituita l'opzione Gruppi a **livello** di directory dalle impostazioni**generali** dei **gruppi** > nel portale di Azure con un collegamento alla documentazione dei [gruppi dinamici.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership) Abbiamo migliorato la documentazione per includere più istruzioni in modo che gli amministratori possano creare tutti i gruppi di utenti che includono o escludono gli utenti guest.

---

## <a name="february-2020"></a>Febbraio 2020

### <a name="upcoming-changes-to-custom-controls"></a>Modifiche imminenti ai controlli personalizzati

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Stiamo pianificando di sostituire l'anteprima dei controlli personalizzati correnti con un approccio che consente alle funzionalità di autenticazione fornite dai partner di funzionare senza problemi con l'amministratore di Azure Active Directory e le esperienze utente finali. Oggi, le soluzioni partner MFA devono affrontare le seguenti limitazioni: funzionano solo dopo l'immissione di una password; non fungono da autenticazione a più fattori per l'autenticazione step-up in altri scenari chiave; e non si integrano con le funzioni di gestione delle credenziali dell'utente finale o amministrativo. La nuova implementazione consentirà ai fattori di autenticazione forniti dai partner di funzionare insieme ai fattori predefiniti per gli scenari chiave, tra cui la registrazione, l'utilizzo, le attestazioni MFA, l'autenticazione con i passaggi, la creazione di report e la registrazione. 

I controlli personalizzati continueranno a essere supportati in anteprima insieme alla nuova progettazione fino a raggiungere la disponibilità generale. A questo punto, daremo ai clienti il tempo di migrare al nuovo progetto. A causa delle limitazioni dell'approccio attuale, non entreremo a bordo di nuovi fornitori fino a quando il nuovo design non sarà disponibile. Stiamo lavorando a stretto contatto con clienti e fornitori e comunicheremo la tempistica man mano che ci avviciniamo. [Scopri di più](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Punteggio sicuro dell'identità - Aggiornamenti delle azioni di miglioramento dell'autenticazione a più fattoriIdentity Secure Score - MFA improvement action updates

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Per riflettere la necessità per le aziende di garantire la massima sicurezza durante l'applicazione di criteri che funzionano con la propria azienda, Microsoft Secure Score rimuove tre azioni di miglioramento incentrate sull'autenticazione a più fattori (MFA) e l'aggiunta di due.

Le seguenti azioni di miglioramento verranno rimosse:

- Registrare tutti gli utenti per l'autenticazione a più fattoriRegister all users for MFA
- Richiedere l'autenticazione a più fattori per tutti gli utenti
- Richiedere l'autenticazione a più fattori per i ruoli con privilegi di Azure ADRequire MFA for Azure AD privileged roles

Verranno aggiunte le seguenti azioni di miglioramento:

- Assicurarsi che tutti gli utenti possano completare l'autenticazione a più fattori per un accesso sicuroEnsure all users can complete MFA for secure access
- Richiedere l'autenticazione a più fattori per i ruoli amministrativiRequire MFA for administrative roles

Queste nuove azioni di miglioramento richiederanno la registrazione degli utenti o degli amministratori per l'autenticazione a più fattori nella directory e la definizione del set di criteri più adatto alle esigenze dell'organizzazione. L'obiettivo principale è quello di avere flessibilità garantendo al tempo stesso che tutti gli utenti e gli amministratori possano eseguire l'autenticazione con più fattori o richieste di verifica dell'identità basate sul rischio. Questo può assumere la forma di impostazione di impostazioni predefinite di sicurezza che consentono a Microsoft di decidere quando sfidare gli utenti per l'autenticazione a più fattori o avere più criteri che applicano decisioni con ambito. Come parte di questi aggiornamenti delle azioni di miglioramento, i criteri di protezione di base non saranno più inclusi nei calcoli dei punteggi. [Per saperne di più su ciò che sta arrivando in Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).

---

### <a name="azure-ad-domain-services-sku-selection"></a>Selezione SKU di Servizi di dominio Azure ADAzure AD Domain Services SKU selection

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services
 
Abbiamo sentito il feedback che i clienti di Servizi di dominio Azure AD desiderano maggiore flessibilità nella selezione dei livelli di prestazioni per le istanze. A partire dal 1 febbraio 2020, è passato da un modello dinamico (in cui Azure AD determina le prestazioni e il piano tariffario in base al numero di oggetti) a un modello di selezione automatica. Ora i clienti possono scegliere un livello di prestazioni che corrisponda al proprio ambiente. Questa modifica consente inoltre di abilitare nuovi scenari come Foreste di risorse e funzionalità Premium come i backup giornalieri. Il numero di oggetti è ora illimitato per tutti gli SKU, ma continueremo a offrire suggerimenti sul numero di oggetti per ogni livello.

**Non è richiesta alcuna azione immediata da parte del cliente.** Per i clienti esistenti, il livello dinamico in uso il 1 febbraio 2020 determina il nuovo livello predefinito. Non vi è alcun impatto sui prezzi o sulle prestazioni come risultato di questa modifica. In futuro, i clienti di Servizi di dominio Azure AD dovranno valutare i requisiti di prestazioni in base alle dimensioni della directory e alle caratteristiche del carico di lavoro. Il passaggio da un livello di servizio all'altro continuerà a essere un'operazione senza tempi di inattività e i clienti non verranno più spostati automaticamente a nuovi livelli in base alla crescita della directory. Inoltre, non ci saranno aumenti di prezzo e i nuovi prezzi saranno allineati con il nostro modello di fatturazione attuale. Per altre informazioni, vedere la [documentazione relativa alle SKU](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) di Servizi di dominio Active Directory di Azure ACTIVE Directory e la pagina dei prezzi di [Servizi di dominio Azure AD.](https://azure.microsoft.com/pricing/details/active-directory-ds/)

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - febbraio 2020New Federated Apps available in Azure AD App gallery - Febbraio 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel febbraio 2020 abbiamo aggiunto queste 31 nuove app con il supporto della federazione alla galleria di app: 

[Piattaforma brevetto IamIP](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO per Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABa Reporting](https://myaba.co.uk/client-access/signin/auth/msad), In Case of Crisis - Portale [Online](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), BIC [Cloud Design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [Beekeeper Azure AD Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korn Ferry Assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), New [Relic (Limited Release)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Thulium](https://admin.thulium.com/login/instance), [Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [Template Scelta dei team](https://links.officeatwork.com/templatechooser-download-teams), [Beesy](https://www.beesy.me/index.php/site/login), [Health Support System](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [MURAL](https://app.mural.co/signup), [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [ThingLink for Teachers and Schools](https://www.thinglink.com/), [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [Reprints Desk - Article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni di Azure AD - febbraio 2020New provisioning connectors in the Azure AD Application Gallery - Febbraio 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Ora puoi automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste nuove app integrate:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatico degli account utente, vedere Automatizzare il provisioning degli utenti nelle [applicazioni SaaS con Azure AD.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Supporto di Azure AD per le chiavi di sicurezza FIDO2 in ambienti ibridiAzure AD support for FIDO2 security keys in hybrid environments

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Stiamo annunciando l'anteprima pubblica del supporto di Azure AD per le chiavi di sicurezza FIDO2 negli ambienti ibridi. Gli utenti possono ora usare le chiavi di sicurezza FIDO2 per accedere ai dispositivi Windows 10 aggiunti ad Azure AD ibrido e ottenere l'accesso trasparente alle risorse locali e cloud. Il supporto per gli ambienti ibridi è stata la funzionalità più richiesta dai nostri clienti senza password da quando abbiamo inizialmente lanciato l'anteprima pubblica per il supporto FIDO2 nei dispositivi aggiunti ad Azure AD. L'autenticazione senza password che utilizza tecnologie avanzate come la biometria e la crittografia a chiave pubblica/privata offre comodità e facilità d'uso pur essendo sicura. Con questa anteprima pubblica, è ora possibile utilizzare l'autenticazione moderna come le chiavi di protezione FIDO2 per accedere alle risorse di Active Directory tradizionali. Per ulteriori informazioni, visitare [SSO per le risorse locali.](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises) 

Per iniziare, visitare [l'abilitazione](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) delle chiavi di sicurezza FIDO2 per il tenant per istruzioni dettagliate. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>La nuova esperienza Account personale è ora disponibile in generale

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Il mio profilo/account  
**Capacità del prodotto:** Esperienze dell'utente finale
 
Il mio account, lo sportello unico per tutte le esigenze di gestione degli account degli utenti finali, è ora disponibile in generale! Gli utenti finali possono accedere a questo nuovo sito tramite URL o nell'intestazione della nuova esperienza Di App personali. Per ulteriori informazioni su tutte le funzionalità self-service offerte dalla nuova esperienza, vedere [My Account Portal Overview](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Aggiornamento dell'URL del sito dell'account personale myaccount.microsoft.com

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Il mio profilo/account  
**Capacità del prodotto:** Esperienze dell'utente finale
 
La nuova esperienza utente finale dell'account `https://myaccount.microsoft.com` personale aggiornerà il proprio URL nel mese successivo. Ulteriori informazioni sull'esperienza e su tutte le funzionalità self-service dell'account offerte agli utenti finali sono disponibili nella Guida del [portale Account personale](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview).

---
 
## <a name="january-2020"></a>Gennaio 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Il nuovo portale My Apps è ora disponibile in generale

**Tipo:** Modifica pianificata  
**Categoria di servizio:** App personali  
**Capacità del prodotto:** Esperienze dell'utente finale
 
Aggiornare l'organizzazione al nuovo portale App personali che è ora disponibile in genere. Per ulteriori informazioni sul nuovo portale e sulle nuove raccolte, fare [clic su Creare raccolte nel portale app personali](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Le aree di lavoro in Azure AD sono state rinominate in raccolte

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App personali   
**Capacità del prodotto:** Esperienze dell'utente finale
 
Le aree di lavoro, i filtri che gli amministratori possono configurare per organizzare le app degli utenti, verranno ora definite raccolte. Per altre informazioni su come configurarle, vedere [Creare raccolte nel portale App personali.](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C Phone sign-up and sign-in using custom policy (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
Con l'iscrizione e l'accesso al numero di telefono, gli sviluppatori e le aziende possono consentire ai clienti di iscriversi e accedere utilizzando una password monouso inviata al numero di telefono dell'utente tramite SMS. Questa funzione consente inoltre al cliente di modificare il proprio numero di telefono se perde l'accesso al proprio telefono. Con la potenza dei criteri personalizzati, l'iscrizione e l'accesso telefonico consentono agli sviluppatori e alle aziende di comunicare il proprio marchio tramite la personalizzazione della pagina. Informazioni su come [configurare l'iscrizione e l'accesso al telefono con criteri personalizzati in Azure AD B2C.](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni di Azure AD - gennaio 2020New provisioning connectors in the Azure AD Application Gallery - January 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Ora puoi automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste nuove app integrate:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatico degli account utente, vedere Automatizzare il provisioning degli utenti nelle [applicazioni SaaS con Azure AD.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Gennaio 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel gennaio 2020, abbiamo aggiunto queste 33 nuove app con il supporto della federazione alla galleria di app: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik) `https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access`, , [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial), [LeaveBot](https://leavebot.io/#home), [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [SmartWork](https://www.intumit.com/english/SmartWork.html), [Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN - Azure AD SSO Gateway for Oracle E-Business Suite - EBS, PeopleSoft e JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), `https://sites.lumapps.com/login` [Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [ Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB per Microsoft Teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient Conduct Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), [PortalTalk 365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), `https://app.sandwai.com/` [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), , [E-RentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Due nuovi rilevamenti di Identity Protection

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione dell'identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Abbiamo aggiunto due nuovi tipi di rilevamento collegato di accesso a Identity Protection: regole di manipolazione della posta in arrivo sospette e viaggi impossibili. Questi rilevamenti offline vengono individuati da Microsoft Cloud App Security (MCAS) e influenzano il rischio di accesso e utente in Identity Protection. Per ulteriori informazioni su questi rilevamenti, vedere i [tipi di rischio di accesso](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Interruzione della modifica: i frammenti URI non verranno eseguiti tramite il reindirizzamento dell'account di accessoBreaking Change: URI Fragments will not be carried through the login redirect

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
A partire dall'8 febbraio 2020, quando viene inviata una richiesta a login.microsoftonline.com per accedere a un utente, il servizio aggiungerà un frammento vuoto alla richiesta.  In questo modo si evita una classe di attacchi di reindirizzamento assicurando che il browser elimini qualsiasi frammento esistente nella richiesta. Nessuna applicazione deve avere una dipendenza da questo comportamento. Per altre informazioni, vedere [Modifiche di rilievo](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) nella documentazione della piattaforma di identità Microsoft.For more information, see Breaking changes in the Microsoft identity platform documentation.

---

## <a name="december-2019"></a>Dicembre 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrare il provisioning di SAP SuccessFactors in Azure AD e AD locale (anteprima pubblica)Integrate SAP SuccessFactors provisioning into Azure AD and on-premises AD (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Capacità del prodotto:** Gestione del ciclo di vita delle identitàIdentity Lifecycle Management

È ora possibile integrare SAP SuccessFactors come origine di identità autorevole in Azure AD. Questa integrazione consente di automatizzare il ciclo di vita delle identità end-to-end, incluso l'utilizzo di eventi basati sulle risorse umane, ad esempio nuove assunzioni o terminazioni, per controllare il provisioning degli account azure AD.

Per altre informazioni su come configurare il provisioning in ingresso di SAP SuccessFactors in Azure AD, vedere l'esercitazione Configurare il [provisioning automatico di SAP SuccessFactors.For](https://aka.ms/SAPSuccessFactorsInboundTutorial) more information about how to set up SAP SuccessFactors inbound provisioning to Azure AD, see the Configure SAP SuccessFactors automatic provisioning tutorial.

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Supporto per messaggi di posta elettronica personalizzati in Azure AD B2C (anteprima pubblica)Support for customized emails in Azure AD B2C (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile usare Azure AD B2C per creare messaggi di posta elettronica personalizzati quando gli utenti si iscrivono per usare le app. Utilizzando DisplayControls (attualmente in anteprima) e un provider di posta elettronica di terze parti (ad esempio, [SendGrid](https://sendgrid.com/), [SparkPost](https://sparkpost.com/)o un'API REST personalizzata), è possibile utilizzare il proprio modello di messaggio di posta elettronica, l'indirizzo **del** mittente e il testo dell'oggetto, nonché supportare la localizzazione e le impostazioni OTP (One-Time Password) personalizzate.

Per altre informazioni, vedere [Verifica della posta elettronica personalizzata in Azure Active Directory B2C.](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Sostituzione dei criteri di base con le impostazioni predefinite di sicurezza

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Come parte di un modello sicuro per impostazione predefinita per l'autenticazione, vengono rimostati i criteri di protezione di base esistenti da tutti i tenant. Questa rimozione è destinata al completamento alla fine di febbraio. La sostituzione di questi criteri di protezione di base è la sicurezza predefinita. Se si utilizzano i criteri di protezione di base, è necessario pianificare il passaggio al nuovo criterio predefinito di sicurezza o a Accesso condizionale. Se questi criteri non sono stati utilizzati, non è possibile eseguire alcuna azione.

Per ulteriori informazioni sulle nuove impostazioni predefinite di sicurezza, vedere [Che cosa sono le impostazioni predefinite di sicurezza?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Per ulteriori informazioni sui criteri di accesso condizionale, vedere Criteri di [accesso condizionale comuni](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>Novembre 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Supporto per l'attributo SameSite e Chrome 80

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Come parte di un modello sicuro per impostazione predefinita per i cookie, il `SameSite` browser Chrome 80 sta cambiando il modo in cui tratta i cookie senza l'attributo. Qualsiasi cookie che non `SameSite` specifica l'attributo verrà considerato `SameSite=Lax`come se fosse stato impostato su , il che comporterà il blocco di alcuni scenari di condivisione dei cookie tra domini da cui l'app potrebbe dipendere. Per mantenere il comportamento precedente di `SameSite=None` Chrome, puoi `Secure` utilizzare l'attributo e aggiungere un attributo aggiuntivo, in modo che i cookie tra siti siano accessibili solo tramite connessioni HTTPS. È prevista la sospensione della modifica di Chrome entro il 4 febbraio 2020.

Ti consigliamo a tutti i nostri sviluppatori di testare le loro app usando queste linee guida:

- Impostare il valore predefinito per l'impostazione **Usa cookie protetto** su **Sì**.

- Impostare il valore predefinito per l'attributo **SameSite** su **Nessuno**.

- Aggiungere un `SameSite` attributo aggiuntivo **Secure**.

Per ulteriori informazioni, vedere [Prossime modifiche ai cookie SameSite in ASP.NET e ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) e potenziali interruzioni dei siti Web dei clienti e dei prodotti e servizi Microsoft in Chrome versione [79 e successive.](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nuovo hotfix per Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Tipo:** Correzione  
**Categoria di servizio:** Gestione identità Microsoft  
**Capacità del prodotto:** Gestione del ciclo di vita delle identitàIdentity Lifecycle Management

Un pacchetto cumulativo di hotfix (build 4.6.34.0) è disponibile per Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Questo pacchetto cumulativo risolve i problemi e aggiunge miglioramenti descritti nella sezione "Problemi risolti e miglioramenti aggiunti in questo aggiornamento".

Per ulteriori informazioni e per scaricare l'hotfix, vedere [Microsoft Identity Manager 2016 Service Pack 2 (build 4.6.34.0) aggiornamento cumulativo è disponibile](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nuovo report attività app ADFS per facilitare la migrazione delle app in Azure AD (anteprima pubblica)New AD FS app activity report to help migrate apps to Azure AD (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Usare il nuovo report attività dell'app Active Directory Federation Services (ADFS) nel portale di Azure per identificare quali app possono essere migrate in Azure AD. Il report valuta la compatibilità di tutte le app ADFS con Azure AD, verifica la presenza di eventuali problemi e fornisce indicazioni sulla preparazione di singole app per la migrazione.

Per altre informazioni, vedere [Usare il report attività dell'applicazione ADFS per eseguire la migrazione delle applicazioni in Azure AD.](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nuovo flusso di lavoro per gli utenti di richiedere il consenso dell'amministratore (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Controllo di accesso

Il nuovo flusso di lavoro di consenso dell'amministratore offre agli amministratori un modo per concedere l'accesso alle app che richiedono l'approvazione dell'amministratore. Se un utente tenta di accedere a un'app, ma non è in grado di fornire il consenso, può ora inviare una richiesta per l'approvazione dell'amministratore. La richiesta viene inviata tramite posta elettronica e inserita in una coda accessibile dal portale di Azure a tutti gli amministratori designati come revisori. Dopo che un revisore esegue un'azione su una richiesta in sospeso, gli utenti richiedenti vengono informati dell'azione.

Per ulteriori informazioni, vedere Configurare il flusso di lavoro di [consenso dell'amministratore (anteprima).](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nuova esperienza di configurazione del token di registrazione delle app di Azure AD per la gestione delle attestazioni facoltative (anteprima pubblica)New Azure AD App Registrations Token configuration experience for managing optional claims (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** esperienza di sviluppo

Il nuovo pannello di configurazione del **token di registrazione delle registrazioni** di Azure AD nel portale di Azure ora mostra agli sviluppatori di app un elenco dinamico di attestazioni facoltative per le app. Questa nuova esperienza consente di semplificare le migrazioni delle app di Azure AD e di ridurre al minimo le configurazioni errate delle attestazioni facoltative.

Per altre informazioni, vedere [Fornire attestazioni facoltative all'app Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nuovo flusso di lavoro di approvazione in due fasi nella gestione dei diritti di Azure AD (anteprima pubblica)New two-stage approval workflow in Azure AD entitlement management (Public Preview)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Capacità del prodotto:** Gestione dei diritti

È stato introdotto un nuovo flusso di lavoro di approvazione in due fasi che consente di richiedere a due approvatori di approvare la richiesta di un utente a un pacchetto di accesso. Ad esempio, è possibile impostarlo in modo che il responsabile dell'utente richiedente deve prima approvare e quindi è anche necessario richiedere l'approvazione di un proprietario della risorsa. Se uno degli approvatori non approva, l'accesso non viene concesso.

Per altre informazioni, vedere Impostazioni di [richiesta e approvazione](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)delle modifiche per un pacchetto di accesso nella gestione dei diritti di Azure AD.

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aggiornamenti alla pagina App personali insieme a nuove aree di lavoro (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

È ora possibile personalizzare il modo in cui gli utenti dell'organizzazione visualizzano e accedono all'esperienza aggiornata delle app personali. Questa nuova esperienza include anche la nuova funzionalità delle aree di lavoro, che semplifica la ricerca e l'organizzazione delle app da parte degli utenti.

Per ulteriori informazioni sulla nuova esperienza App personali e sulla creazione di aree di lavoro, vedere [Creare aree di lavoro nel portale App personali](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Google social ID support for Azure AD B2B collaboration (General Availability)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** Autenticazione utente

Il nuovo supporto per l'uso degli ID social di Google (account Gmail) in Azure AD consente di semplificare la collaborazione per utenti e partner. Non è più necessario che i partner creino e gestiscano un nuovo account specifico di Microsoft. Microsoft Teams ora supporta completamente gli utenti Google su tutti i client e su endpoint di autenticazione comuni e relativi ai tenant.

Per ulteriori informazioni, consultate Aggiungere Google come provider di [identità per gli utenti guest B2B.](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Supporto per Microsoft Edge Mobile per l'accesso condizionale e Single Sign-On (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Azure AD for Microsoft Edge on iOS and Android now supports Azure AD Single Sign-On and Conditional Access:

- **Single Sign-On (SSO) di Microsoft Edge:** Single Sign-On è ora disponibile tra i client nativi (ad esempio Microsoft Outlook e Microsoft Edge) per tutte le app connesse ad Azure AD.

- **Accesso condizionale di Microsoft Edge:** Tramite i criteri di accesso condizionale basati sull'applicazione, gli utenti devono usare browser protetti da Microsoft Intune, ad esempio Microsoft Edge.Through application-based conditional access policies, your users must use Microsoft Intune-protected browsers, such as Microsoft Edge.

Per ulteriori informazioni sull'accesso condizionale e SSO con Microsoft Edge, vedere il post di blog [Microsoft Edge Mobile Support for Conditional Access and Single Sign-on Now Generally Available.](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) Per altre informazioni su come configurare le app client usando [l'accesso condizionale basato su app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) o l'accesso [condizionale basato](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)su dispositivo, vedere [Gestire l'accesso Web tramite un browser protetto da criteri](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser)di Microsoft Intune.

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD entitlement management (General Availability)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Capacità del prodotto:** Gestione dei diritti

La gestione dei diritti di Azure AD è una nuova funzionalità di governance delle identità, che consente alle organizzazioni di gestire il ciclo di vita di identità e accesso su larga scala. Questa nuova funzionalità consente di automatizzare i flussi di lavoro delle richieste di accesso, le assegnazioni di accesso, le revisioni e la scadenza tra gruppi, app e siti di SharePoint Online.

Con la gestione dei diritti di Azure AD, è possibile gestire in modo più efficiente l'accesso sia per i dipendenti che anche per gli utenti esterni all'organizzazione che devono accedere a tali risorse.

Per altre informazioni, vedere [Che cos'è la gestione dei diritti](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements) di Azure AD?

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste nuove app SaaS supportate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti  

Ora puoi automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste nuove app integrate:

[SAP Cloud Platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), Priority [Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatico degli account utente, vedere Automatizzare il provisioning degli utenti nelle [applicazioni SaaS con Azure AD.](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Novembre 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

A novembre 2019, abbiamo aggiunto queste 21 nuove app con il supporto della federazione alla galleria di app:

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access for Members (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [Portale ResLife](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign On (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), `https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279` [, Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [Beedle](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets Idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [UniFLOW Online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [Jisc Registrazione degli elettori degli studenti](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Raccolta di applicazioni di Azure AD nuova e migliorataNew and improved Azure AD application gallery

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

La raccolta di applicazioni di Azure AD è stata aggiornata per semplificare l'individuazione di app preintegrate che supportano il provisioning, OpenID Connect e SAML nel tenant di Azure Active Directory.We've updated the Azure AD application gallery to make it easier for you to find pre-integrated apps that support provisioning, OpenID Connect, and SAML on your Azure Active Directory tenant.

Per altre informazioni, vedere [Aggiungere un'applicazione al tenant](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)di Azure Active Directory.For more information, see Add an application to your Azure Active Directory tenant.

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Aumento del limite di lunghezza della definizione del ruolo dell'app da 120 a 240 caratteri

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Abbiamo sentito dai clienti che il limite di lunghezza per il valore della definizione del ruolo dell'app in alcune app e servizi è troppo breve a 120 caratteri. In risposta, la lunghezza massima della definizione del valore del ruolo è stata aumentata a 240 caratteri.

Per altre informazioni sull'uso di definizioni di ruolo specifiche dell'applicazione, vedere [Aggiungere ruoli dell'app nell'applicazione e riceverli nel token.](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)

---

## <a name="october-2019"></a>Ottobre 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Deprecazione dell'API identityRiskEvent per i rilevamenti dei rischi di Azure AD Identity Protection  

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Protezione dell'identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

In risposta ai commenti degli sviluppatori, i sottoscrittori di Azure AD Premium P2 possono ora eseguire query complesse sui dati di rilevamento dei rischi di Azure AD Identity Protection usando la nuova API riskDetection per Microsoft Graph. La versione beta dell'API [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) esistente interromperà la restituzione dei dati intorno al **10 gennaio 2020.** Se l'organizzazione utilizza l'API identityRiskEvent, è necessario passare alla nuova API riskDetection.

Per ulteriori informazioni sulla nuova API riskDetection, vedere la documentazione di [riferimento sull'API di rilevamento dei rischi](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Supporto del proxy di applicazione per l'attributo SameSite e Chrome 80

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Un paio di settimane prima del rilascio del browser Chrome 80, prevediamo di aggiornare il modo in cui i cookie del proxy di applicazione trattano l'attributo **SameSite.** Con il rilascio di Chrome 80, qualsiasi cookie che non specifica l'attributo **SameSite** verrà considerato come se fosse impostato su `SameSite=Lax`.

Per evitare impatti potenzialmente negativi a causa di questa modifica, stiamo aggiornando l'accesso al proxy di applicazione e i cookie di sessione:

- Impostazione del valore predefinito per l'impostazione **Usa cookie protetto** su **Sì**.

- Impostazione del valore predefinito per l'attributo **SameSite** su **Nessuno**.

    >[!NOTE]
    > I cookie di accesso del proxy di applicazione sono sempre stati trasmessi esclusivamente su canali sicuri. Queste modifiche si applicano solo ai cookie di sessione.

Per altre informazioni sulle impostazioni dei cookie del proxy di applicazione, vedere Impostazioni dei cookie per l'accesso alle applicazioni locali in Azure Active Directory.For more information about the Application Proxy cookie settings, see [Cookie settings for accessing on-premises applications in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Le registrazioni di app (legacy) e la gestione convergente delle app dal portale di registrazione delle applicazioni (apps.dev.microsoft.com) non saranno più disponibili

**Tipo:** Modifica pianificata  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** esperienza di sviluppo

Nel prossimo futuro, gli utenti con account Azure AD non saranno più in grado di registrare e gestire le applicazioni convergenti utilizzando il portale di registrazione delle applicazioni (apps.dev.microsoft.com) o di registrare e gestire le applicazioni nelle registrazioni delle app (legacy) nel portale di Azure.

Per altre informazioni sulla nuova esperienza di registrazione delle app, vedere la guida Per le registrazioni delle app nella Guida alla formazione sul portale di [Azure.](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Gli utenti non sono più necessari per eseguire nuovamente la registrazione durante la migrazione dall'autenticazione a più fattori per utente all'autenticazione a più fattori basata sull'accesso condizionaleUsers are no longer required to re-register during migration from per-user MFA to Conditional Access-based MFA

**Tipo:** Correzione  
**Categoria di servizio:** Mfa  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

È stato risolto un problema noto per cui quando gli utenti sono stati tenuti a registrare nuovamente se sono stati disabilitati per l'autenticazione a più fattori per utente (MFA) e quindi abilitato per l'autenticazione a più fattori tramite un criterio di accesso condizionale.

Per richiedere agli utenti di eseguire nuovamente la registrazione, è possibile selezionare l'opzione Registrazione a più autenticazione **obbligatoria** dai metodi di autenticazione dell'utente nel portale di Azure AD. Per ulteriori informazioni sulla migrazione degli utenti dall'autenticazione a più fattori per utente all'autenticazione a più fattori basata sull'accesso condizionale, vedere Convertire gli utenti dall'autenticazione a più fattori per [utente all'autenticazione a più fattori basata sull'accesso condizionale](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa).

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nuove funzionalità per trasformare e inviare attestazioni nel token SAML

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Sono state aggiunte funzionalità aggiuntive che consentono di personalizzare e inviare attestazioni nel token SAML. Queste nuove funzionalità includono:

- Funzioni aggiuntive di trasformazione delle attestazioni, che consentono di modificare il valore inviato nell'attestazione.

- Possibilità di applicare più trasformazioni a una singola attestazione.

- Possibilità di specificare l'origine dell'attestazione, in base al tipo di utente e al gruppo a cui appartiene l'utente.

Per informazioni dettagliate su queste nuove funzionalità, incluso il modo di utilizzarle, vedere [Personalizzare le attestazioni rilasciate nel token SAML per](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)le applicazioni aziendali.

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nuova pagina Accesso personali per gli utenti finali in Azure ADNew My Sign-ins page for end users in Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Monitoraggio e creazione report

È stata aggiunta una nuova pagina Ihttps://mysignins.microsoft.com) miei **inquisigli** (per consentire agli utenti dell'organizzazione di visualizzare la cronologia di accesso recente per verificare la presenza di eventuali attività insolite. Questa nuova pagina consente agli utenti di visualizzare:

- Se qualcuno sta tentando di indovinare la propria password.

- Se un utente malintenzionato ha eseguito correttamente l'accesso al proprio account e da quale posizione.

- Quali applicazioni l'utente malintenzionato ha tentato di accedere.

Per altre informazioni, vedere il blog Degli utenti che possono controllare la [cronologia di accesso per individuare attività insolite.](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migrazione di Servizi di dominio Azure AD (Azure AD DS) dalle reti virtuali classiche a reti virtuali di Azure Resource ManagerMigration of Azure AD Domain Services (Azure AD DS) from classic to Azure Resource Manager virtual networks

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

Ai nostri clienti che sono stati bloccati sulle reti virtuali classiche - abbiamo grandi notizie per voi! È ora possibile eseguire una migrazione una tantera da una rete virtuale classica a una rete virtuale di Resource Manager esistente. Dopo il passaggio alla rete virtuale di Resource Manager, sarà possibile sfruttare le funzionalità aggiuntive e aggiornate, ad esempio criteri granulari per le password, notifiche tramite posta elettronica e log di controllo.

Per altre informazioni, vedere Anteprima - Eseguire la migrazione di Servizi di dominio Azure AD dal modello di [rete virtuale classica a Gestione risorse.](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Aggiornamenti al layout del contratto di pagina B2C di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

Sono state introdotte alcune nuove modifiche alla versione 1.2.0 del contratto di pagina per Azure AD B2C. In questa versione aggiornata, è ora possibile controllare l'ordine di caricamento per gli elementi, che può anche aiutare a fermare lo sfarfallio che si verifica quando viene caricato il foglio di stile (CSS).

Per un elenco completo delle modifiche apportate al contratto di pagina, vedere il [log delle modifiche](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)della versione .

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aggiornamento alla pagina App personali insieme alle nuove aree di lavoro (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Controllo di accesso

È ora possibile personalizzare il modo in cui gli utenti dell'organizzazione visualizzano e accedono alla nuovissima esperienza di App personali, incluso l'uso della nuova funzionalità delle aree di lavoro per semplificare la ricerca delle app. La nuova funzionalità delle aree di lavoro funge da filtro per le app a cui gli utenti dell'organizzazione hanno già accesso.

Per ulteriori informazioni sull'implementazione della nuova esperienza App personali e sulla creazione di aree di lavoro, vedere [Creare aree di lavoro nel portale App personali (anteprima).](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Supporto per il modello di fatturazione mensile attivo basato sull'utente (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

Azure AD B2C supporta ora la fatturazione mensile degli utenti attivi (MAU). La fatturazione MAU si basa sul numero di utenti univoci con attività di autenticazione durante un mese di calendario. I clienti esistenti possono passare a questo nuovo metodo di fatturazione in qualsiasi momento.

A partire dal 1 novembre 2019, tutti i nuovi clienti verranno fatturati automaticamente utilizzando questo metodo. Questo metodo di fatturazione offre vantaggi ai clienti attraverso i vantaggi in termini di costi e la possibilità di pianificare in anticipo.

Per ulteriori informazioni, vedere Aggiornamento al modello di [fatturazione degli utenti attivi mensili](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Ottobre 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

In ottobre 2019, abbiamo aggiunto queste 35 nuove app con il supporto della federazione alla galleria di app:

[In caso di crisi – Mobile , Juno](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial) [Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [Tact](https://tact.ai/assistant/), [OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [Contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate – Europa](https://www.hirevue.com/), [HireVue Coordinate - USOnly](https://www.hirevue.com/), [HireVue Coordinate - US](https://www.hirevue.com/), [ WittyParrot Knowledge Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Portale Cambium Xirrus EasyPass](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [Mail Luck!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [Teamie](https://theteamie.com/), [Velocity for Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), [EAB Navigate IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), Speaking Email for Intune [(iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [Speaking Email for Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome Care Navigation System ](https://ihealthnav.com/account/signin), [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Voce di menu Sicurezza consolidata nel portale di Azure AD

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Protezione dell'identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

È ora possibile accedere a tutte le funzionalità di sicurezza di Azure AD disponibili dalla nuova voce di menu Sicurezza e dalla barra **di ricerca** nel portale di Azure.You can now access all of the available Azure AD security features from the new **Security** menu item, and from the Search bar, in the Azure portal. Inoltre, la nuova pagina di destinazione **Sicurezza,** denominata **Sicurezza - Guida introduttiva**, fornirà collegamenti alla documentazione pubblica, indicazioni sulla sicurezza e guide alla distribuzione.

Il nuovo menu **Sicurezza** include:

- Accesso condizionale
- Identity Protection
- Centro sicurezza
- Identity Secure Score
- Metodi di autenticazione
- Mfa
- Rapporti sui rischi - Utenti rischiosi, invii rischiosi, rilevamenti dei rischi
- E molto altro ancora.

Per ulteriori informazioni, vedere [Sicurezza - Guida introduttiva](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Criteri di scadenza dei gruppi di Office 365 migliorati con il rinnovo automatico

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione gruppi  
**Capacità del prodotto:** Gestione del ciclo di vita delle identitàIdentity Lifecycle Management

I criteri di scadenza dei gruppi di Office 365 sono stati migliorati per rinnovare automaticamente i gruppi che sono attivamente utilizzati dai relativi membri. I gruppi verranno rinnovati automaticamente in base all'attività degli utenti in tutte le app di Office 365, inclusi Outlook, SharePoint e Teams.

Questo miglioramento consente di ridurre le notifiche di scadenza dei gruppi e consente di assicurarsi che i gruppi attivi continuino a essere disponibili. Se si dispone già di un criterio di scadenza attivo per i gruppi di Office 365, non è necessario eseguire alcuna operazione per attivare questa nuova funzionalità.

Per ulteriori informazioni, vedere Configurare i criteri di scadenza per i gruppi di [Office 365.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Esperienza di creazione di Servizi di dominio Azure AD aggiornata (Azure AD DS)Updated Azure AD Domain Services (Azure AD DS) creation experience

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

Abbiamo aggiornato Servizi di dominio Azure AD (Azure AD DS) per includere un'esperienza di creazione nuova e migliorata, aiutandoti a creare un dominio gestito in soli tre clic! Inoltre, è ora possibile caricare e distribuire Servizi di dominio Active Directory di Azure da un modello.

Per altre informazioni, vedere [Esercitazione: Creare e configurare un'istanza](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)di Servizi di dominio Azure Active Directory.For more information, see Tutorial: Create and configure an Azure Active Directory Domain Services instance .

---
