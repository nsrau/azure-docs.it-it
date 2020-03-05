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
ms.date: 02/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cf12c28dc04d0dbb8a680d45c8d8f5f5faa2d82
ms.sourcegitcommit: d45fd299815ee29ce65fd68fd5e0ecf774546a47
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/04/2020
ms.locfileid: "78274186"
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory

>Consente di ricevere notifiche su quando rivisitare la pagina per gli aggiornamenti copiando e incollando questo URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` nell'icona del lettore del feed RSS ![](./media/whats-new/feed-icon-16x16.png) lettore di feed.

Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente. Se si cercano informazioni precedenti agli ultimi sei mesi, è possibile trovarli in [Archivio per le novità in Azure Active Directory](whats-new-archive.md).

---

## <a name="february-2020"></a>Febbraio 2020
 
### <a name="identity-secure-score---mfa-improvement-action-updates"></a>Punteggio sicuro identità-aggiornamenti dell'azione di miglioramento dell'autenticazione a più fattori

**Tipo:** Modifica pianificata  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Per riflettere la necessità per le aziende di garantire la massima sicurezza durante l'applicazione di criteri che interagiscono con l'azienda, Microsoft Secure Score sta eliminando tre azioni di miglioramento incentrate sull'autenticazione a più fattori (multi-factor authentication) e sull'aggiunta di due.

Verranno rimosse le seguenti azioni di miglioramento:

- Registrare tutti gli utenti per l'autenticazione a più fattori
- Richiedere l'autenticazione a più fattori per tutti gli utenti
- Richiedi autenticazione a più fattori per Azure AD ruoli con privilegi

Verranno aggiunte le seguenti azioni di miglioramento:

- Assicurarsi che tutti gli utenti possano completare l'autenticazione a più fattori per l'accesso sicuro
- Richiedi autenticazione a più fattori per ruoli amministrativi

Queste nuove azioni di miglioramento richiedono la registrazione degli utenti o degli amministratori per l'autenticazione a più fattori nella directory e la definizione del giusto set di criteri che soddisfano le esigenze dell'organizzazione. L'obiettivo principale è quello di garantire flessibilità garantendo che tutti gli utenti e gli amministratori possano eseguire l'autenticazione con più fattori o richieste di verifica dell'identità basate sul rischio. Questo può assumere la forma di impostazione delle impostazioni predefinite di sicurezza che consentono a Microsoft di decidere quando richiedere agli utenti l'autenticazione a più fattori o avere più criteri che applicano decisioni con ambito. Nell'ambito di questi aggiornamenti dell'azione di miglioramento, i criteri di protezione di base non verranno più inclusi nei calcoli di assegnazione dei punteggi. Scopri di [più sui vantaggi di Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide).
 
---

### <a name="azure-ad-domain-services-sku-selection"></a>Selezione SKU Azure AD Domain Services

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services
 
Abbiamo sentito un feedback che Azure AD Domain Services clienti desiderano maggiore flessibilità nella selezione dei livelli di prestazioni per le istanze. A partire dal 1 ° febbraio 2020, è stato passato da un modello dinamico, in cui Azure AD determina il livello di prestazioni e il piano tariffario in base al conteggio degli oggetti, a un modello di selezione automatica. Ora i clienti possono scegliere un livello di prestazioni corrispondente all'ambiente. Questa modifica consente anche di abilitare nuovi scenari come le foreste di risorse e le funzionalità Premium come i backup giornalieri. Il conteggio oggetti è ora illimitato per tutti gli SKU, ma continuerà a offrire suggerimenti per il conteggio degli oggetti per ogni livello.

**Non è richiesta alcuna azione immediata da parte del cliente.** Per i clienti esistenti, il livello dinamico utilizzato il 1 ° febbraio 2020 determina il nuovo livello predefinito. Il risultato di questa modifica non prevede alcun effetto sui prezzi o sulle prestazioni. In futuro, i clienti di Azure AD DS dovranno valutare i requisiti di prestazioni in base alle dimensioni della directory e alle caratteristiche del carico di lavoro. Il passaggio tra i livelli di servizio continuerà a essere un'operazione senza tempi di inattività e i clienti non verranno più spostati automaticamente in nuovi livelli in base alla crescita della directory. Inoltre, non vi saranno aumenti di prezzo e i nuovi prezzi saranno allineati con il modello di fatturazione corrente. Per ulteriori informazioni, vedere la [documentazione relativa agli sku Azure AD DS](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus) e la pagina relativa ai [prezzi Azure ad Domain Services](https://azure.microsoft.com/pricing/details/active-directory-ds/).


---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2020 febbraio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel febbraio 2020 abbiamo aggiunto le 31 nuove app con supporto federativo per la raccolta di app: 

[IamIP Patent Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO for Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABA Reporting](https://myaba.co.uk/client-access/signin/auth/msad), [in caso di crisi-portale online](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC cloud design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [apicoltore Azure ad Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korn Ferry assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess), [Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [New Relic (Limited release)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Tulio](https://admin.thulium.com/login/instance), [ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [template Selezione per Teams](https://links.officeatwork.com/templatechooser-download-teams), [BEEY](https://www.beesy.me/index.php/site/login), [Health Support System](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [Mural](https://app.mural.co/signup), [hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview), [Wakelet](https://wakelet.com/login), [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [Thinglink for teachers and Schools](https://www.thinglink.com/), [coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WeDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [ristampas desk-article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2020 febbraio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per queste app appena integrate:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud di Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatizzato degli account utente, vedere automatizzare il [provisioning degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Supporto Azure AD per le chiavi di sicurezza FIDO2 in ambienti ibridi

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
È stata annunciata l'anteprima pubblica del supporto Azure AD per le chiavi di sicurezza FIDO2 in ambienti ibridi. Gli utenti possono ora usare le chiavi di sicurezza di FIDO2 per accedere ai dispositivi Windows 10 aggiunti a Azure AD ibrido e ottenere accesso facile alle risorse locali e cloud. Il supporto per gli ambienti ibridi è stato la funzionalità più richiesta tra i nostri clienti, perché inizialmente è stata avviata l'anteprima pubblica per il supporto di FIDO2 in Azure AD dispositivi aggiunti. L'autenticazione senza password con tecnologie avanzate come biometria e crittografia a chiave pubblica/privata fornisce praticità e facilità d'uso, garantendo al tempo stesso la sicurezza. Con questa anteprima pubblica è ora possibile usare l'autenticazione moderna, ad esempio le chiavi di sicurezza FIDO2 per accedere alle risorse Active Directory tradizionali. Per altre informazioni, vedere [SSO to locale Resources](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises). 

Per iniziare, vedere [abilitare le chiavi di sicurezza di FIDO2 per il tenant](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key) per istruzioni dettagliate. 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>La nuova esperienza dell'account è ora disponibile a livello generale 

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Profilo/account  
**Funzionalità del prodotto:** Esperienze degli utenti finali
 
Il mio account, l'unico punto di interruzione per tutte le esigenze di gestione degli account dell'utente finale, è ora disponibile a livello generale. Gli utenti finali possono accedere al nuovo sito tramite URL o nell'intestazione della nuova esperienza app personali. Scopri di più su tutte le funzionalità self-service offerte dalla nuova esperienza nella [Panoramica del portale](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)per gli account.

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>Aggiornamento dell'URL del sito personale a myaccount.microsoft.com

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Profilo/account  
**Funzionalità del prodotto:** Esperienze degli utenti finali
 
La nuova esperienza dell'utente finale per l'account verrà aggiornata per https://myaccount.microsoft.com nel mese successivo. Altre informazioni sull'esperienza e tutte le funzionalità self-service per gli account offerte dagli utenti finali sono disponibili nella [Guida del portale](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)per gli account.

---
 
## <a name="january-2020"></a>Gennaio 2020
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>Il nuovo portale app personali è ora disponibile a livello generale

**Tipo:** Modifica pianificata  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Esperienze degli utenti finali
 
Aggiornare l'organizzazione al nuovo portale app personali, ora disponibile a livello generale. Per altre informazioni sul nuovo portale e sulle raccolte, vedere [creare raccolte nel portale app personali](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Le aree di lavoro in Azure AD sono state rinominate in raccolte

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App personali   
**Funzionalità del prodotto:** Esperienze degli utenti finali
 
Le aree di lavoro, ovvero i filtri che gli amministratori possono configurare per organizzare le app degli utenti, verranno ora definite raccolte. Per altre informazioni su come configurarle, vedere [create Collections nel portale app personali](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections).

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>Azure AD B2C l'iscrizione e l'accesso tramite il telefono con criteri personalizzati (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
Con l'iscrizione e l'accesso ai numeri di telefono, gli sviluppatori e le aziende possono consentire ai clienti di iscriversi e accedere usando una password monouso inviata al numero di telefono dell'utente tramite SMS. Questa funzionalità consente inoltre al cliente di modificare il numero di telefono se perde l'accesso al telefono. Grazie alla potenza dei criteri personalizzati, l'iscrizione e l'accesso tramite telefono consentono a sviluppatori e aziende di comunicare il proprio marchio tramite la personalizzazione delle pagine. Informazioni su come [configurare l'iscrizione e l'accesso tramite telefono con criteri personalizzati in Azure ad B2C](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication).
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2020 gennaio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per queste app appena integrate:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatizzato degli account utente, vedere automatizzare il [provisioning degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2020 gennaio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel gennaio 2020 sono state aggiunte le nuove app 33 con supporto federativo per la raccolta di app: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [fastly Edge cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), [di bonifica Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://1030-review-develop-3zknud.netlogistik.com/), [SkyKick cloud backup per Office 365, con la](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access)massima rapidità [,](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial) [LeaveBot](https://leavebot.io/#home), [Datacamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [tripattione](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [Smarts](https://www.intumit.com/english/SmartWork.html), [dotcom-monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [SSOGEN-Azure ad SSO gateway per Oracle e-Business Suite-EBS, PeopleSoft e JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [Operating Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB per Microsoft teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial) [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient comportamento Manager software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), PortalTalk [365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [squelch cloud Office365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), PrinterLogic [SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), Sandwai [,](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial) [EZRentOut, AssetSonar](https://app.sandwai.com/), [Akari Assistente virtuale](https://akari.io/akari-virtual-assistant/)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Due nuovi rilevamenti di Identity Protection

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione delle identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Sono stati aggiunti due nuovi tipi di rilevamento collegato per l'accesso a Identity Protection: regole di manipolazione della posta in arrivo sospette e viaggi non possibili. Questi rilevamenti offline vengono individuati da Microsoft Cloud App Security (MCAS) e influiscono sull'utente e sul rischio di accesso in Identity Protection. Per altre informazioni su questi rilevamenti, vedere i [tipi di rischio di accesso](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Modifica di rilievo: i frammenti URI non verranno trasferiti tramite il reindirizzamento di accesso

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
A partire dall'8 febbraio 2020, quando una richiesta viene inviata a login.microsoftonline.com per l'accesso a un utente, il servizio aggiunge un frammento vuoto alla richiesta.  Ciò impedisce a una classe di attacchi di reindirizzamento assicurando che il browser cancelli tutti i frammenti esistenti nella richiesta. Nessuna applicazione deve avere una dipendenza da questo comportamento. Per ulteriori informazioni, vedere [modifiche di rilievo](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) nella documentazione della piattaforma Microsoft Identity.

---

## <a name="december-2019"></a>Dicembre 2019

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>Integrare il provisioning di SAP SuccessFactors in Azure AD e AD locale (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

È ora possibile integrare SAP SuccessFactors come origine autorevole delle identità in Azure AD. Questa integrazione consente di automatizzare il ciclo di vita delle identità end-to-end, tra cui l'uso di eventi basati su HR, come nuove assunzioni o interruzioni, per controllare il provisioning degli account Azure AD.

Per altre informazioni su come configurare il provisioning in ingresso di SAP SuccessFactors in Azure AD, vedere l'esercitazione sulla [configurazione del provisioning automatico di SAP SuccessFactors](https://aka.ms/SAPSuccessFactorsInboundTutorial) .

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Supporto per i messaggi di posta elettronica personalizzati in Azure AD B2C (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile usare Azure AD B2C per creare messaggi di posta elettronica personalizzati quando gli utenti si iscrivono per usare le app. Usando DisplayControls (attualmente in anteprima) e un provider di posta elettronica di terze parti (ad esempio, [SendGrid](https://sendgrid.com/), [SPARKPOST](https://sparkpost.com/)o un'API REST personalizzata), è possibile usare il modello di posta elettronica, l'indirizzo e il testo dell'oggetto, nonché la localizzazione del supporto e le impostazioni **della** password monouso (OTP) personalizzate.

Per ulteriori informazioni, vedere [Verifica della posta elettronica personalizzata in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-email).

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>Sostituzione dei criteri di base con le impostazioni predefinite di sicurezza

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Come parte di un modello sicuro per impostazione predefinita per l'autenticazione, i criteri di protezione di base esistenti vengono rimossi da tutti i tenant. Questa rimozione è destinata al completamento alla fine di febbraio. La sostituzione di questi criteri di protezione di base è la sicurezza predefinita. Se sono stati usati i criteri di protezione di base, è necessario pianificare il passaggio ai nuovi criteri predefiniti di sicurezza o all'accesso condizionale. Se non sono stati usati questi criteri, non è necessario eseguire alcuna azione.

Per ulteriori informazioni sulle nuove impostazioni predefinite di sicurezza, vedere [che cosa sono le impostazioni predefinite della sicurezza?](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) Per altre informazioni sui criteri di accesso condizionale, vedere [criteri di accesso condizionale comuni](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common).

---

## <a name="november-2019"></a>Novembre 2019

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>Supporto per l'attributo navigava sullostesso sito e Chrome 80

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Come parte di un modello sicuro per impostazione predefinita per i cookie, il browser Chrome 80 sta cambiando il modo in cui tratta i cookie senza l'attributo `SameSite`. Tutti i cookie che non specificano l'attributo `SameSite` verranno considerati come se fossero impostati su `SameSite=Lax`, il che comporterebbe il blocco di determinati scenari di condivisione di cookie tra domini da cui può dipendere l'app. Per mantenere il comportamento precedente di Chrome, è possibile usare l'attributo `SameSite=None` e aggiungere un attributo `Secure` aggiuntivo, in modo che i cookie tra siti possano essere accessibili solo tramite connessioni HTTPS. Chrome è pianificato per completare questa modifica entro il 4 febbraio 2020.

Si consiglia a tutti gli sviluppatori di testare le proprie app seguendo questa guida:

- Impostare il valore predefinito per l'impostazione **usa cookie sicuro** su **Sì**.

- Impostare il valore predefinito per l'attributo **navigava sullostesso sito** su **None**.

- Aggiungere un attributo `SameSite` aggiuntivo di **Secure**.

Per altre informazioni, vedere [future modifiche ai cookie di navigava sullostesso sito in ASP.NET e ASP.NET Core](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) e [potenziali rotture per i siti Web dei clienti e i prodotti e servizi Microsoft in Chrome versione 79 e successive](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79).

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Nuovo hotfix per Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2)

**Tipo:** Correzione  
**Categoria di servizio:** Microsoft Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

È disponibile un pacchetto hotfix rollup (Build 4.6.34.0) per Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Questo pacchetto di rollup risolve i problemi e aggiunge miglioramenti descritti nella sezione "problemi risolti e miglioramenti aggiunti in questo aggiornamento".

Per ulteriori informazioni e per scaricare il pacchetto di hotfix, vedere [Microsoft Identity Manager 2016 Service Pack 2 (Build 4.6.34.0) Update Rollup è disponibile](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r).

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>Nuovo report attività app AD FS per facilitare la migrazione delle app ai Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Usare il nuovo report attività app Active Directory Federation Services (AD FS), nella portale di Azure, per identificare le app idonee per la migrazione a Azure AD. Il report valuta tutte le app AD FS per la compatibilità con Azure AD, verifica la presenza di eventuali problemi e fornisce indicazioni sulla preparazione di singole app per la migrazione.

Per ulteriori informazioni, vedere [utilizzare il ad FS report attività applicazione per eseguire la migrazione di applicazioni a Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity).

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>Nuovo flusso di lavoro che consente agli utenti di richiedere il consenso dell'amministratore (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Controllo di accesso

Il nuovo flusso di lavoro di consenso dell'amministratore offre agli amministratori un modo per concedere l'accesso alle app che richiedono l'approvazione dell'amministratore. Se un utente tenta di accedere a un'app, ma non è in grado di fornire il consenso, ora può inviare una richiesta per l'approvazione dell'amministratore. La richiesta viene inviata tramite posta elettronica e inserita in una coda accessibile dal portale di Azure a tutti gli amministratori che sono stati designati come revisori. Dopo che un revisore ha eseguito un'azione su una richiesta in sospeso, gli utenti che eseguono la richiesta riceveranno una notifica dell'azione.

Per altre informazioni, vedere [configurare il flusso di lavoro di consenso dell'amministratore (anteprima)](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow).

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>Nuova esperienza di configurazione dei token di registrazione App Azure AD per la gestione di attestazioni facoltative (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** esperienza di sviluppo

Il nuovo pannello di **configurazione del token di registrazione del app Azure ad** nel portale di Azure ora Mostra agli sviluppatori di app un elenco dinamico di attestazioni facoltative per le app. Questa nuova esperienza consente di semplificare le migrazioni di Azure AD app e di ridurre al minimo le configurazioni facoltative delle attestazioni.

Per altre informazioni, vedere [fornire attestazioni facoltative all'app Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims).

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Nuovo flusso di lavoro di approvazione in due fasi in Azure AD gestione dei diritti (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Gestione dei diritti

È stato introdotto un nuovo flusso di lavoro di approvazione in due fasi che consente di richiedere a due responsabili approvazione di approvare la richiesta di un utente a un pacchetto di accesso. Ad esempio, è possibile impostarlo in modo che il responsabile dell'utente richiedente debba prima approvare, quindi è anche possibile richiedere un proprietario della risorsa per approvare. Se uno dei responsabili approvazione non approva, l'accesso non viene concesso.

Per ulteriori informazioni, vedere [modifica delle impostazioni di richiesta e approvazione per un pacchetto di accesso in Azure ad gestione dei diritti](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy).

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Aggiornamenti alla pagina App personali insieme alle nuove aree di lavoro (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

È ora possibile personalizzare il modo in cui gli utenti dell'organizzazione visualizzano e accedono all'esperienza aggiornata delle app personali. Questa nuova esperienza include anche la nuova funzionalità aree di lavoro, che consente agli utenti di trovare e organizzare app in modo più semplice.

Per altre informazioni sulla nuova esperienza app personali e sulla creazione di aree di lavoro, vedere [creare aree di lavoro nel portale app personali](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Supporto di Google Social ID per la collaborazione B2B di Azure AD (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** Autenticazione utente

Il nuovo supporto per l'uso di Google Social ID (account Gmail) in Azure AD contribuisce a semplificare la collaborazione per utenti e partner. Non è più necessario che i partner creino e gestiscano un nuovo account specifico Microsoft. Microsoft teams ora supporta completamente gli utenti di Google in tutti i client e negli endpoint di autenticazione comuni e correlati al tenant.

Per altre informazioni, vedere [aggiungere Google come provider di identità per gli utenti Guest B2B](https://docs.microsoft.com/azure/active-directory/b2b/google-federation).

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Supporto per dispositivi mobili Microsoft Edge per l'accesso condizionale e Single Sign-on (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Azure AD per Microsoft Edge in iOS e Android supporta ora Azure AD Single Sign-on e l'accesso condizionale:

- **Microsoft Edge Single Sign-on (SSO):** Single Sign-on è ora disponibile in client nativi, ad esempio Microsoft Outlook e Microsoft Edge, per tutte le app connesse a Azure AD.

- **Accesso condizionale di Microsoft Edge:** Tramite i criteri di accesso condizionale basati su applicazione, gli utenti devono usare browser protetti da Microsoft Intune, ad esempio Microsoft Edge.

Per altre informazioni sull'accesso condizionale e SSO con Microsoft Edge, vedere il post di Blog [relativo al supporto di Microsoft Edge per dispositivi mobili per l'accesso condizionale e Single Sign-on ora disponibile](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179) a livello generale. Per altre informazioni su come configurare le app client usando l'accesso [condizionale basato su app](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access) o [l'accesso condizionale basato su dispositivo](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices), vedere [gestire l'accesso Web usando un browser protetto da criteri Microsoft Intune](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser).

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Gestione dei diritti di Azure AD (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Gestione dei diritti

Azure AD la gestione dei diritti è una nuova funzionalità di governance delle identità, che consente alle organizzazioni di gestire il ciclo di vita delle identità e degli accessi Questa nuova funzionalità consente di automatizzare i flussi di lavoro delle richieste di accesso, le assegnazioni di accesso, le revisioni e la scadenza tra i gruppi, le app e i siti di SharePoint Online.

Con Azure AD gestione dei diritti, è possibile gestire in modo più efficiente l'accesso sia per i dipendenti sia per gli utenti esterni all'organizzazione che necessitano dell'accesso a tali risorse.

Per ulteriori informazioni, vedere [che cos'è Azure ad gestione dei diritti?](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste app SaaS appena supportate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti  

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per queste app appena integrate:

[SAP Cloud Platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial), [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial), [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial), [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial), [infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial), [OfficeSpace software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial), [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatizzato degli account utente, vedere automatizzare il [provisioning degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2019 novembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel novembre 2019 abbiamo aggiunto le 21 nuove app con supporto federativo per la raccolta di app:

[Transtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access per Members (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign-on (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [BEEDLE](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [autenticazione utente Netskope](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) , [Registrazione JISC Student voter](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial), [e4enable](https://portal.e4enable.com/)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>Raccolta di applicazioni Azure AD nuove e migliorate

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

La raccolta di applicazioni Azure AD è stata aggiornata per semplificare la ricerca di app preintegrate che supportano il provisioning, OpenID Connect e SAML nel tenant di Azure Active Directory.

Per altre informazioni, vedere [aggiungere un'applicazione al tenant di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal).

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>Aumento del limite di lunghezza per la definizione del ruolo app da 120 a 240 caratteri

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

I clienti hanno sentito che il limite di lunghezza per il valore di definizione del ruolo app in alcune app e servizi è troppo breve a 120 caratteri. In risposta, la lunghezza massima della definizione del valore del ruolo è stata aumentata a 240 caratteri.

Per altre informazioni sull'uso di definizioni di ruolo specifiche dell'applicazione, vedere [aggiungere i ruoli dell'app nell'applicazione e riceverli nel token](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps).

---

## <a name="october-2019"></a>Ottobre 2019

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>Deprecazione dell'API identityRiskEvent per i rilevamenti dei rischi Azure AD Identity Protection  

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Protezione delle identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

In risposta al feedback degli sviluppatori, gli abbonati Azure AD Premium P2 possono ora eseguire query complesse sui dati di rilevamento dei rischi Azure AD Identity Protection usando la nuova API riskDetection per Microsoft Graph. La versione beta dell'API [identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) esistente smetterà di restituire i dati circa il **10 gennaio 2020**. Se l'organizzazione usa l'API identityRiskEvent, è necessario passare alla nuova API di riskDetection.

Per ulteriori informazioni sulla nuova API riskDetection, vedere la [documentazione di riferimento dell'API di rilevamento dei rischi](https://aka.ms/RiskDetectionsAPI).

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>Supporto del proxy di applicazione per l'attributo navigava sullostesso sito e Chrome 80

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Due settimane prima della versione del browser Chrome 80, si prevede di aggiornare il modo in cui i cookie del proxy applicazione gestiscono l'attributo **navigava sullostesso sito** . Con la versione di Chrome 80, qualsiasi cookie che non specifica l'attributo **navigava sullostesso sito** verrà considerato come se fosse stato impostato su `SameSite=Lax`.

Per evitare potenziali conseguenze negative a causa di questa modifica, stiamo aggiornando l'accesso al proxy di applicazione e i cookie di sessione per:

- Impostando il valore predefinito per l'impostazione **usa cookie sicuro** su **Sì**.

- Impostazione del valore predefinito per l'attributo **navigava sullostesso sito** su **None**.

    >[!NOTE]
    > I cookie di accesso del proxy di applicazione sono sempre stati trasmessi esclusivamente su canali protetti. Queste modifiche si applicano solo ai cookie di sessione.

Per altre informazioni sulle impostazioni dei cookie del proxy di applicazione, vedere [impostazioni dei cookie per l'accesso alle applicazioni locali in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>Registrazioni app (legacy) e la gestione delle app convergenti dal portale di registrazione delle applicazioni (apps.dev.microsoft.com) non saranno più disponibili

**Tipo:** Modifica pianificata  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** esperienza di sviluppo

Nel prossimo futuro, gli utenti con account Azure AD non saranno più in grado di registrare e gestire applicazioni convergenti tramite il portale di registrazione delle applicazioni (apps.dev.microsoft.com) o di registrare e gestire le applicazioni nel Registrazioni app (legacy) esperienza nella portale di Azure.

Per ulteriori informazioni sulla nuova esperienza di Registrazioni app, vedere l' [registrazioni app nella Guida alla formazione portale di Azure](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md).

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>Non è più necessario ripetere la registrazione degli utenti durante la migrazione dall'autenticazione a più fattori per utente all'autenticazione a più fattori basata sull'accesso condizionale

**Tipo:** Correzione  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

È stato risolto un problema noto per cui gli utenti dovevano ripetere la registrazione se sono stati disabilitati per l'autenticazione a più fattori Multi-Factor Authentication (multi-factor authentication) e quindi abilitati per l'autenticazione a più fattori tramite criteri di accesso condizionale.

Per richiedere agli utenti di ripetere la registrazione, è possibile selezionare l'opzione **necessaria per ripetere la registrazione** dell'autenticazione a più fattori nei metodi di autenticazione dell'utente nel portale di Azure ad. Per altre informazioni sulla migrazione degli utenti dall'autenticazione a più fattori per utente all'autenticazione a più fattori basata sull'accesso condizionale, vedere [convertire gli utenti dall'autenticazione a più fattori per ogni utente in](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)multi-factor authentication based.

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>Nuove funzionalità per trasformare e inviare attestazioni nel token SAML

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Sono state aggiunte funzionalità aggiuntive che consentono di personalizzare e inviare attestazioni nel token SAML. Queste nuove funzionalità includono:

- Funzioni aggiuntive per la trasformazione delle attestazioni che consentono di modificare il valore inviato nell'attestazione.

- Possibilità di applicare più trasformazioni a una singola attestazione.

- Possibilità di specificare l'origine dell'attestazione, in base al tipo di utente e al gruppo a cui appartiene l'utente.

Per informazioni dettagliate su queste nuove funzionalità, ad esempio su come usarle, vedere [personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Nuova pagina di accesso per gli utenti finali in Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Monitoraggio e creazione report

È stata aggiunta una nuova pagina di **accesso** (https://mysignins.microsoft.com) per consentire agli utenti dell'organizzazione di visualizzare la cronologia di accesso recente per verificare la presenza di attività insolite. Questa nuova pagina consente agli utenti di vedere:

- Se qualcuno sta provando a indovinare la password.

- Se un utente malintenzionato ha eseguito l'accesso al proprio account e dalla posizione.

- App a cui l'utente malintenzionato ha provato ad accedere.

Per ulteriori informazioni, vedere gli [utenti possono ora verificare la cronologia di accesso per il Blog di attività insolite](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066) .

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Migrazione di Azure AD Domain Services (Azure AD DS) dal modello classico a quello Azure Resource Manager reti virtuali

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

Per i clienti che si sono bloccati nelle reti virtuali classiche, abbiamo ottime notizie. È ora possibile eseguire una migrazione una sola volta da una rete virtuale classica a una rete virtuale Gestione risorse esistente. Dopo il passaggio alla rete virtuale Gestione risorse, sarà possibile sfruttare le funzionalità aggiuntive e aggiornate, ad esempio i criteri granulari per le password, le notifiche tramite posta elettronica e i log di controllo.

Per altre informazioni, vedere [Preview-migrate Azure ad Domain Services dal modello di rete virtuale classica a gestione risorse](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet).

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Aggiornamenti al layout del contratto di pagina Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

Sono state introdotte alcune nuove modifiche alla versione 1.2.0 del contratto di pagina per Azure AD B2C. In questa versione aggiornata è ora possibile controllare l'ordine di caricamento per gli elementi, che può anche essere utile per arrestare lo sfarfallio che si verifica quando viene caricato il foglio di stile (CSS).

Per un elenco completo delle modifiche apportate al contratto di pagina, vedere il log delle modifiche della [versione](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120).

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>Eseguire l'aggiornamento alla pagina App personali insieme alle nuove aree di lavoro (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** Controllo di accesso

È ora possibile personalizzare il modo in cui gli utenti dell'organizzazione visualizzano e accedono alla nuova esperienza di app personali, incluso l'uso della nuova funzionalità aree di lavoro per semplificare la ricerca delle app. La funzionalità nuove aree di lavoro funge da filtro per le app a cui gli utenti dell'organizzazione hanno già accesso.

Per altre informazioni su come implementare la nuova esperienza app personali e creare aree di lavoro, vedere [creare aree di lavoro nel portale app personali (anteprima)](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces).

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>Supporto per il modello di fatturazione mensile attivo basato sull'utente (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C

Azure AD B2C supporta ora la fatturazione mensile di utenti attivi (MAU). La fatturazione di MAU si basa sul numero di utenti univoci con attività di autenticazione durante un mese di calendario. I clienti esistenti possono passare a questo nuovo metodo di fatturazione in qualsiasi momento.

A partire dal 1 ° novembre 2019, tutti i nuovi clienti verranno addebitati automaticamente utilizzando questo metodo. Questo metodo di fatturazione consente ai clienti di sfruttare i vantaggi in termini di costi e la possibilità di pianificare in anticipo.

Per altre informazioni, vedere [aggiornare il modello di fatturazione di utenti attivi mensili](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Nuove app federate disponibili nella raccolta di App Azure AD-ottobre 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel 2019 ottobre sono state aggiunte le nuove app 35 con supporto federativo per la raccolta di app:

[In caso di crisi-mobile](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial), [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial), [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial), [tatto](https://tact.ai/assistant/), [OpusCapita Cash Management](http://cm1.opuscapita.com/tenantname), [Salestim](https://prd.salestim.io/forms), [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial), [dynaTrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial), [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process), [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial), [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial), [ShipHazmat](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial), [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial), [contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial), [Bindtuning](https://bindtuning.com/login), [HireVue Coordinate-eu](https://www.hirevue.com/), HireVue coordinata [-](https://www.hirevue.com/)USOnly, [HireVue coordinata-US](https://www.hirevue.com/), [WittyParrot Knowledge box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup), [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial), [visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial), [Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup), [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial), [mail fortuna!](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial), [teamie](https://theteamie.com/), [velocità per Teams](https://velocity.peakup.org/teams/login), [SIGNL4](https://account.signl4.com/manage), EAB [Navigate impl](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial), [ScreenMeet](https://console.screenmeet.com/), [Omega Point](https://pi.ompnt.com/), [indirizzo di posta elettronica per Intune (iPhone)](https://speaking.email/FAQ/98/email-access-via-microsoft-intune), [indirizzo di posta elettronica per Office 365 Direct (iPhone/Android)](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct), [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial), [iHealthHome care Navigation System](https://ihealthnav.com/account/signin), [ Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Voce di menu sicurezza consolidata nel portale Azure AD

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Protezione delle identità  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

È ora possibile accedere a tutte le funzionalità di sicurezza Azure AD disponibili dalla nuova voce di menu **sicurezza** e dalla barra di **ricerca** nella portale di Azure. Inoltre, la nuova pagina di destinazione della **sicurezza** , denominata **Security-Getting Started**, fornirà collegamenti alla documentazione pubblica, alle linee guida per la sicurezza e alla guida alla distribuzione.

Il nuovo menu **sicurezza** include:

- Accesso condizionale
- Identity Protection
- Centro sicurezza
- Identity Secure Score
- Metodi di autenticazione
- MFA
- Report di rischio-utenti a rischio, accessi a rischio, rilevamento dei rischi
- E altro...

Per ulteriori informazioni, vedere [Security-Getting Started](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted).

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Criteri di scadenza dei gruppi di Office 365 migliorati con il rinnovo automatico

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

I criteri di scadenza dei gruppi di Office 365 sono stati migliorati per rinnovare automaticamente i gruppi che sono attivamente usati dai relativi membri. I gruppi verranno rinnovati in base all'attività dell'utente in tutte le app di Office 365, inclusi Outlook, SharePoint e teams.

Questa funzionalità avanzata consente di ridurre le notifiche di scadenza del gruppo e di garantire che i gruppi attivi continuino a essere disponibili. Se si dispone già di un criterio di scadenza attivo per i gruppi di Office 365, non è necessario eseguire alcuna operazione per attivare la nuova funzionalità.

Per altre informazioni, vedere [configurare i criteri di scadenza per i gruppi di Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle).

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>Esperienza di creazione Azure AD Domain Services (Azure AD DS) aggiornata

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

È stato aggiornato Azure AD Domain Services (Azure AD DS) per includere un'esperienza di creazione nuova e migliorata, che consente di creare un dominio gestito con pochi clic. Inoltre, è ora possibile caricare e distribuire Azure AD DS da un modello.

Per altre informazioni, vedere [esercitazione: creare e configurare un'istanza di Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance).

---

## <a name="september-2019"></a>Settembre 2019

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>Piano per la modifica: deprecazione dei pacchetti di contenuto Power BI

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

A partire dal 1 ° ottobre 2019, Power BI inizierà a deprecare tutti i pacchetti di contenuto, incluso il pacchetto di contenuto Azure AD Power BI. In alternativa a questo pacchetto di contenuto, è possibile usare Azure AD cartelle di lavoro per ottenere informazioni approfondite sui servizi correlati a Azure AD. Sono in arrivo cartelle di lavoro aggiuntive, incluse le cartelle di lavoro sui criteri di accesso condizionale in modalità solo report, informazioni dettagliate basate sul consenso delle app e altro ancora.

Per ulteriori informazioni sulle cartelle di lavoro di, vedere [come utilizzare le cartelle di lavoro di monitoraggio di Azure per Azure Active Directory report](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks). Per altre informazioni sulla deprecazione dei pacchetti di contenuto, vedere il post di Blog relativo all' [annuncio della disponibilità generale delle app](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/) per i modelli di Power bi.

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>Il profilo è stato rinominato e integrato nella pagina dell'account Microsoft Office

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Profilo/account  
**Funzionalità del prodotto:** Collaborazione

A partire da ottobre, l'esperienza di profilo personale diventerà il mio account. Come parte di questa modifica, ovunque si trovi attualmente, il **mio profilo** cambierà nel **mio account**. Oltre alla modifica dei nomi e ad alcuni miglioramenti della progettazione, l'esperienza aggiornata offrirà integrazione aggiuntiva con la pagina account Microsoft Office. In particolare, sarà possibile accedere alle installazioni e alle sottoscrizioni di Office dalla pagina **Panoramica dell'account** , insieme alle preferenze di contatto relative a Office dalla pagina **privacy** .

Per altre informazioni sull'esperienza my profile (anteprima), vedere [Cenni preliminari sul portale My Profile (Preview)](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview).

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>Gestire in blocco i gruppi e i membri usando i file CSV nel portale di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Siamo lieti di annunciare la disponibilità dell'anteprima pubblica delle esperienze di gestione dei gruppi in blocco nel portale di Azure AD. È ora possibile usare un file CSV e il portale di Azure AD per gestire i gruppi e gli elenchi di membri, tra cui:

- Aggiunta o rimozione di membri da un gruppo.

- Download dell'elenco di gruppi dalla directory.

- Download dell'elenco dei membri del gruppo per un gruppo specifico.

Per ulteriori informazioni, vedere l'elenco [bulk Add members](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members), [bulk Remove members](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members), [bulk download members](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)e bulk [download groups](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download).

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>Il consenso dinamico è ora supportato tramite un nuovo endpoint di consenso dell'amministratore

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

È stato creato un nuovo endpoint di consenso dell'amministratore per supportare il consenso dinamico, utile per le app che vogliono usare il modello di consenso dinamico sulla piattaforma di identità Microsoft.

Per altre informazioni su come usare questo nuovo endpoint, vedere [uso dell'endpoint di consenso dell'amministratore](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2019 settembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel settembre 2019 sono state aggiunte le 29 nuove app con supporto federativo per la raccolta di app:

[ScheduleLook](https://schedulelook.bbsonlineservices.net/), [accesso SSO di Microsoft Azure per Ethidex Compliance per Office™-Single Sign-on](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial), [portale di IServer](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial), [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial), coordinate [Travel and Expense](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial), [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial), [YeeFlow](https://apps.yeeflow.com/), [Arc Centres](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial), [Luware Stratus team](https://stratus.emea.luware.cloud/login), [Wide Ideas](https://wideideas.online/wideideas/), [Prism cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [JDLT client Hub](https://clients.jdlt.co.uk/login), [RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial), [SealPath Secure Browser](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive), [Prisme cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial), [Penno](https://app.penneo.com/), [Hiretual](https://app.testhtm.com/settings/email-integration), [Cintoo cloud](https://aec.cintoo.com/login), [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial), [Hosted Heritage online SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial), [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial), [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial), [bis](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial), [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/), [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial), [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial), [Discovery benefits SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial), [Amelio](https://app.amelio.co/), [ITask](https://itask.yipinapp.com/)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-global-reader-role"></a>Nuovo ruolo lettore globale Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** RBAC  
**Funzionalità del prodotto:** Controllo di accesso

A partire dal 24 settembre 2019, verrà avviato il rollup di un nuovo ruolo di Azure Active Directory (AD) denominato Global Reader. Questa implementazione inizierà con la produzione e i clienti cloud globali (GCC), completando il mondo nel corso di ottobre.

Il ruolo di lettore globale è la controparte di sola lettura dell'amministratore globale. Gli utenti con questo ruolo possono leggere le impostazioni e le informazioni amministrative tra Microsoft 365 servizi, ma non possono eseguire azioni di gestione. Il ruolo di lettore globale è stato creato per ridurre il numero di amministratori globali dell'organizzazione. Poiché gli account di amministratore globale sono potenti e vulnerabili ad attacchi, è consigliabile disporre di meno di cinque amministratori globali. È consigliabile usare il ruolo di lettore globale per la pianificazione, i controlli o le indagini. Si consiglia inoltre di utilizzare il ruolo di lettore globale in combinazione con altri ruoli di amministratore limitati, ad esempio amministratore di Exchange, per facilitare il lavoro senza richiedere il ruolo di amministratore globale.

Il ruolo di lettore globale funziona con il nuovo centro di amministrazione di Microsoft 365, l'interfaccia di amministrazione di Exchange, l'interfaccia di amministrazione dei team, il Centro sicurezza, il centro di conformità, l'interfaccia di amministrazione di Azure AD e l'interfaccia di amministrazione Gestione dispositivi.

>[!NOTE]
> All'inizio della versione di anteprima pubblica, il ruolo di lettore globale non funzionerà con: SharePoint, Privileged Access Management, Customer Lockbox, etichette di riservatezza, ciclo di vita dei team, creazione di report per i team & analisi delle chiamate, gestione dei dispositivi telefonici IP e app Teams Catalogo. Tutti questi servizi sono destinati a funzionare con il ruolo in futuro.

Per ulteriori informazioni, vedere [autorizzazioni per i ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles).

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>Accedere a un server di report locale dall'app Power BI mobile usando Azure Active Directory Application Proxy

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Una nuova integrazione tra il Power BI app per dispositivi mobili e Azure AD proxy di applicazione consente di accedere in modo sicuro all'app per dispositivi mobili Power BI e di visualizzare i report dell'organizzazione ospitati nell'Server di report di Power BI locale.

Per informazioni sull'app per dispositivi mobili Power BI, inclusa la posizione in cui scaricare l'app, vedere il [sito Power bi](https://powerbi.microsoft.com/mobile/). Per altre informazioni su come configurare l'app per dispositivi mobili Power BI con Azure AD proxy di applicazione, vedere [abilitare l'accesso remoto a Power bi dispositivi mobili con Azure ad proxy di applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi).

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>È disponibile una nuova versione del modulo AzureADPreview di PowerShell

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Directory

Sono stati aggiunti nuovi cmdlet al modulo AzureADPreview per semplificare la definizione e l'assegnazione di ruoli personalizzati in Azure AD, tra cui:

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Nuova versione di Azure AD Connect

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Directory

È stata rilasciata una versione aggiornata di Azure AD Connect per i clienti con aggiornamento automatico. Questa nuova versione include diverse nuove funzionalità, miglioramenti e correzioni di bug. Per ulteriori informazioni su questa nuova versione, vedere [Azure ad Connect: cronologia delle versioni](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250).

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>Azure Multi-Factor Authentication Server (multi-factor authentication), versione 8.0.2 è ora disponibile

**Tipo:** Correzione  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Se si è un cliente esistente, che ha attivato il server di autenticazione a più fattori prima del 1 ° luglio 2019, è ora possibile scaricare la versione più recente di multi-factor authentication server (versione 8.0.2). In questa nuova versione viene:

- Correzione di un problema. quando Azure AD Sync modifica un utente da disabilitato ad abilitato, viene inviato un messaggio di posta elettronica all'utente.

- È stato risolto un problema per consentire ai clienti di eseguire l'aggiornamento, continuando a usare la funzionalità tag.

- È stato aggiunto il codice paese Kosovo (+ 383).

- Aggiunta della registrazione di controllo bypass monouso a MultiFactorAuthSvc. log.

- Miglioramento delle prestazioni per l'SDK del servizio Web.

- Correzione di altri bug secondari.

A partire dal 1 ° luglio 2019, Microsoft ha smesso di offrire server di autenticazione a più fattori per nuove distribuzioni. I nuovi clienti che richiedono l'autenticazione a più fattori devono usare Azure Multi-Factor Authentication basato sul cloud. Per altre informazioni, vedere [pianificazione di una distribuzione di Azure multi-factor authentication basata sul cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---
