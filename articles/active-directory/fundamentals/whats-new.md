---
title: Novità Note sulla versione - Azure Active Directory | Microsoft Docs
description: Informazioni sulle novità con Azure Active Directory; quali le note sulla versione più recenti, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti.
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/28/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9bcc356835fcfc080bd381043552d6e8868cc7f
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 09/29/2020
ms.locfileid: "91446617"
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory

>Per ricevere notifiche che indicano che occorre visitare di nuovo questa pagina per visualizzare gli aggiornamenti, copiare e incollare questo URL `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` nel lettore di feed ![icona del lettore di feed RSS](./media/whats-new/feed-icon-16x16.png).

Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente. Se si stanno cercando elementi più vecchi di sei mesi, è possibile trovarli in [Archivio per le novità di Azure Active Directory](whats-new-archive.md).

---

## <a name="september-2020"></a>Settembre 2020

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---september-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2020 settembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Coda](../saas-apps/coda-provisioning-tutorial.md)
- [Cofense Recipient Sync](../saas-apps/cofense-provision-tutorial.md)
- [InVision](../saas-apps/invision-provisioning-tutorial.md)
- [myday](../saas-apps/myday-provision-tutorial.md)
- [SAP Analytics Cloud](../saas-apps/sap-analytics-cloud-provisioning-tutorial.md)
- [Sensibilizzazione sulla sicurezza di Webroot](../saas-apps/webroot-security-awareness-training-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).
 
---

### <a name="audited-bitlocker-recovery-in-azure-ad---public-preview"></a>Ripristino di BitLocker controllato in Azure AD-anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione dell'accesso ai dispositivi  
**Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi
 
Quando gli amministratori IT o gli utenti finali leggono le chiavi di ripristino di BitLocker a cui hanno accesso, Azure Active Directory ora genera un log di controllo che acquisisce chi ha eseguito l'accesso alla chiave di ripristino. Lo stesso controllo fornisce i dettagli del dispositivo a cui è stata associata la chiave BitLocker.

Gli utenti finali possono [accedere alle chiavi di ripristino tramite il mio account](../user-help/my-account-portal-devices-page.md#view-a-bitlocker-key). Gli amministratori IT possono accedere alle chiavi di ripristino tramite l' [API della chiave di ripristino di BitLocker in beta](https://docs.microsoft.com/graph/api/resources/bitlockerrecoverykey?view=graph-rest-beta,) o tramite il portale di Azure ad. Per altre informazioni, vedere [visualizzare o copiare le chiavi BitLocker nel portale di Azure ad](../devices/device-management-azure-portal.md#view-or-copy-bitlocker-keys).

---

### <a name="teams-devices-administrator-built-in-role"></a>Ruolo predefinito amministratore dispositivi Teams

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** RBAC  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti con il ruolo di [amministratore dei dispositivi team](../users-groups-roles/directory-assign-admin-roles.md#teams-devices-administrator) possono gestire i [dispositivi certificati](https://www.microsoft.com/microsoft-365/microsoft-teams/across-devices/devices) dai team dall'interfaccia di amministrazione dei team. 

Questo ruolo consente all'utente di visualizzare tutti i dispositivi a colpo d'occhio, con la possibilità di cercare e filtrare i dispositivi. L'utente può anche controllare i dettagli di ogni dispositivo, inclusi l'account connesso e la marca e il modello del dispositivo. L'utente può modificare le impostazioni del dispositivo e aggiornare le versioni del software. Questo ruolo non concede le autorizzazioni per controllare l'attività dei team e la qualità della chiamata del dispositivo.
 
---

### <a name="advanced-query-capabilities-for-directory-objects"></a>Funzionalità di query avanzate per oggetti directory

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Grafico MS  
**Funzionalità del prodotto:** esperienza di sviluppo
 
Tutte le nuove funzionalità di query introdotte per gli oggetti directory in API Azure AD sono ora disponibili nell'endpoint v 1.0 e pronto per la produzione. Gli sviluppatori possono contare, cercare, filtrare e ordinare gli oggetti directory e i collegamenti correlati usando gli operatori OData standard.

Per altre informazioni, vedere la documentazione [qui](https://aka.ms/BlogPostMezzoGA)ed è anche possibile inviare commenti e suggerimenti con questo [breve sondaggio](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR_yN8EPoGo5OpR1hgmCp1XxUMENJRkNQTk5RQkpWTE44NEk2U0RIV0VZRy4u).
 
---

### <a name="public-preview-continuous-access-evaluation-for-tenants-who-configured-conditional-access-policies"></a>Anteprima pubblica: valutazione di accesso continuo per i tenant che hanno configurato i criteri di accesso condizionale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
La versione di valutazione dell'accesso continuo (CAE) è ora disponibile in anteprima pubblica per Azure AD tenant con criteri di accesso condizionale. Con CAE, gli eventi e i criteri di sicurezza critici vengono valutati in tempo reale. Sono incluse la disabilitazione dell'account, la reimpostazione della password e la modifica della posizione. Per altre informazioni, vedere [valutazione dell'accesso continuo](../conditional-access/concept-continuous-access-evaluation.md).

---

### <a name="ask-users-requesting-an-access-package-additional-questions-to-improve-approval-decisions"></a>Chiedere agli utenti che richiedono un pacchetto di accesso ulteriori domande per migliorare le decisioni relative all'approvazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione degli accessi degli utenti  
**Funzionalità del prodotto:** Gestione dei diritti
 
Gli amministratori possono ora richiedere che gli utenti che richiedono un pacchetto di accesso rispondano a domande aggiuntive oltre alla giustificazione aziendale nel portale di accesso personale della gestione dei diritti Azure AD. Le risposte degli utenti verranno quindi visualizzate agli approvatori per consentire loro di prendere una decisione di approvazione dell'accesso più accurata. Per ulteriori informazioni, vedere [raccogliere ulteriori informazioni sul richiedente per l'approvazione (anteprima)](../governance/entitlement-management-access-package-approval-policy.md#collect-additional-requestor-information-for-approval-preview).
 
---

### <a name="public-preview-enhanced-user-management"></a>Anteprima pubblica: gestione avanzata degli utenti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione utenti  
**Funzionalità del prodotto:** Gestione utenti
 

Il portale Azure AD è stato aggiornato per semplificare la ricerca degli utenti nelle pagine tutti gli utenti e utenti eliminati. Le modifiche nell'anteprima includono: 
- Proprietà utente più visibili, tra cui ID oggetto, stato di sincronizzazione della directory, tipo di creazione ed emittente di identità.
- La ricerca consente ora la ricerca combinata di nomi, messaggi di posta elettronica e ID oggetto.
- Filtro migliorato per tipo di utente (membro, Guest e nessuno), stato di sincronizzazione della directory, tipo di creazione, nome della società e nome di dominio.
- Nuove funzionalità di ordinamento per proprietà quali nome, nome dell'entità utente e data di eliminazione.
- Un nuovo numero totale di utenti che viene aggiornato con qualsiasi ricerca o filtro.

Per ulteriori informazioni, vedere la pagina relativa ai miglioramenti apportati [alla gestione degli utenti (anteprima) in Azure Active Directory](../users-groups-roles/users-search-enhanced.md).

---

### <a name="new-notes-field-for-enterprise-applications"></a>Nuovo campo note per le applicazioni aziendali

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Funzionalità del **prodotto** app aziendali: SSO

È possibile aggiungere note di testo gratuite alle applicazioni aziendali. È possibile aggiungere tutte le informazioni rilevanti che consentiranno di dirigere le applicazioni in applicazioni aziendali. Per altre informazioni, vedere [Guida introduttiva: configurare le proprietà di un'applicazione nel tenant di Azure Active Directory (Azure ad)](../manage-apps/add-application-portal-configure.md). 

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---september-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2020 settembre

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel settembre 2020 abbiamo aggiunto le seguenti 34 nuove applicazioni nella raccolta di app con supporto federativo:

[VMware Horizon-Unified Access Gateway](), [PULSe Secure PCS](../saas-apps/vmware-horizon-unified-access-gateway-tutorial.md), [Inventory360](../saas-apps/pulse-secure-pcs-tutorial.md), [Frontitude](https://services.enteksystems.de/sso/microsoft/signup), [BookWidgets](https://www.bookwidgets.com/sso/office365), [ZVD_SERVER](https://zaas.zenmutech.com/user/signin), [HashData for business](https://hashdata.app/login.xhtml), [SecureLogin](https://securelogin.securelogin.nu/sso/azure/login), [CyberSolutions MAILBASEΣ/CMSS](../saas-apps/cybersolutions-mailbase-tutorial.md), [CyberSolutions CYBERMAILΣ](../saas-apps/cybersolutions-cybermail-tutorial.md), [LimbleCMMS](https://auth.limblecmms.com/), [luccichi Inc](../saas-apps/glint-inc-tutorial.md), [zeroheight](../saas-apps/zeroheight-tutorial.md), [Gender fitness](https://app.genderfitness.com/), [Coeo Portal](https://my.coeo.com/), [grammatical](../saas-apps/grammarly-tutorial.md), [Fivetran](../saas-apps/fivetran-tutorial.md), [Kumolus](../saas-apps/kumolus-tutorial.md), [RSA Archer Suite](../saas-apps/rsa-archer-suite-tutorial.md), [TeamzSkill](../saas-apps/teamzskill-tutorial.md), [raumfürraum](../saas-apps/raumfurraum-tutorial.md), Saviynt [, BizMerlinHR,](https://marketplace.bizmerlin.net/bmone/signup) [mobile Locker](../saas-apps/mobile-locker-tutorial.md), [ZEngine](../saas-apps/zengine-tutorial.md), [CloudCADI](https://app.cloudcadi.com/login), [Simfoni Analytics](https://simfonianalytics.com/accounts/microsoft/login/), [priva Identity & Access Management](https://my.priva.com/), [Nitro Pro](https://www.gonitro.com/nps/product-details/downloads), [Eventfinity](../saas-apps/eventfinity-tutorial.md), [FEXA](../saas-apps/fexa-tutorial.md), [firma protetta Enterprise Portal](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [firma protetta Enterprise Portal installazione di AAD](https://www.securedsigning.com/aad/Auth/ExternalLogin/AdminPortal), [WISTEC online](https://wisteconline.com/auth/oidc), [Oracle PeopleSoft-protetto da F5 Big-IP APM](../saas-apps/oracle-peoplesoft-protected-by-f5-big-ip-apm-tutorial.md) [Saviynt](../saas-apps/saviynt-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui: https://aka.ms/AppsTutorial .

Per elencare l'applicazione nella raccolta di app Azure AD, leggere i dettagli qui: https://aka.ms/AzureADAppRequest .

---

### <a name="new-delegation-role-in-azure-ad-entitlement-management-access-package-assignment-manager"></a>Nuovo ruolo di delega nella gestione dei diritti Azure AD: Access Package Assignment Manager

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione degli accessi degli utenti  
**Funzionalità del prodotto:** Gestione dei diritti
 
Un nuovo ruolo gestione assegnazione pacchetti di accesso è stato aggiunto in Azure AD gestione dei diritti per fornire autorizzazioni granulari per gestire le assegnazioni. È ora possibile delegare le attività a un utente in questo ruolo, che può delegare le assegnazioni di gestione di un pacchetto di accesso a un proprietario dell'azienda. Tuttavia, un gestore di assegnazione dei pacchetti di accesso non può modificare i criteri del pacchetto di accesso o altre proprietà impostate dagli amministratori. 

Con questo nuovo ruolo, si traggono vantaggio dai privilegi minimi necessari per delegare la gestione delle assegnazioni e mantenere il controllo amministrativo su tutte le altre configurazioni dei pacchetti di accesso. Per altre informazioni, vedere [ruoli di gestione dei diritti](../governance/entitlement-management-delegate.md#entitlement-management-roles).
 
---

### <a name="changes-to-privileged-identity-managements-onboarding-flow"></a>Modifiche al flusso di onboarding di Privileged Identity Management

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
In precedenza, l'onboarding in Privileged Identity Management (PIM) richiedeva il consenso dell'utente e un flusso di onboarding nel pannello di PIM che includeva la registrazione nell'autenticazione a più fattori di Azure. Con la recente integrazione dell'esperienza PIM nel pannello ruoli e amministratori Azure AD, questa esperienza verrà rimossa. Qualsiasi tenant con licenza P2 valida verrà caricato automaticamente in PIM.

Il caricamento in PIM non ha alcun effetto negativo diretto sul tenant. È possibile prevedere le seguenti modifiche:
- Opzioni di assegnazione aggiuntive, ad esempio Active vs. idonee con l'ora di inizio e di fine quando si effettua un'assegnazione nel pannello ruoli e amministratori PIM o Azure AD. 
- Meccanismi di ambito aggiuntivi, come le unità amministrative e i ruoli personalizzati, introdotti direttamente nell'esperienza di assegnazione. 
- Se si è un amministratore globale o un amministratore del ruolo con privilegi, è possibile iniziare a ricevere alcuni messaggi di posta elettronica aggiuntivi come il digest settimanale di PIM. 
- È anche possibile vedere l'entità servizio MS-PIM nel log di controllo relativa all'assegnazione di ruolo. Questa modifica prevista non influirà sul normale flusso di lavoro.

 Per altre informazioni, vedere [iniziare a usare Privileged Identity Management](../privileged-identity-management/pim-getting-started.md).

---

### <a name="azure-ad-entitlement-management-the-select-pane-of-access-package-resources-now-shows-by-default-the-resources-currently-in-the-selected-catalog"></a>Gestione dei diritti di Azure AD: il riquadro Seleziona delle risorse del pacchetto di accesso ora Visualizza per impostazione predefinita le risorse attualmente presenti nel catalogo selezionato

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione degli accessi degli utenti  
**Funzionalità del prodotto:** Gestione dei diritti
 

Nel flusso di creazione del pacchetto di accesso, nella scheda ruoli risorsa, il comportamento di selezione riquadro è in corso di modifica. Attualmente, il comportamento predefinito prevede la visualizzazione di tutte le risorse di proprietà dell'utente e delle risorse aggiunte al catalogo selezionato. 

Questa esperienza verrà modificata per visualizzare solo le risorse attualmente aggiunte al catalogo per impostazione predefinita, in modo che gli utenti possano selezionare facilmente le risorse dal catalogo. L'aggiornamento aiuterà a individuare le risorse da aggiungere ai pacchetti di accesso e a ridurre il rischio di aggiungere inavvertitamente le risorse di proprietà dell'utente che non fanno parte del catalogo. Per altre informazioni, vedere [creare un nuovo pacchetto di accesso in Azure ad gestione dei diritti](../governance/entitlement-management-access-package-create.md#resource-roles).
 
---

## <a name="august-2020"></a>Agosto 2020 
 
### <a name="updates-to-azure-multi-factor-authentication-server-firewall-requirements"></a>Aggiornamenti ai requisiti del firewall di Azure server Multi-Factor Authentication

**Tipo:** Modifica pianificata  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
A partire dall'1 ottobre 2020, i requisiti del firewall del server Azure multi-factor authentication richiedono intervalli IP aggiuntivi.

Se nell'organizzazione sono presenti regole del firewall in uscita, aggiornare le regole in modo che i server multi-factor authentication possano comunicare con tutti gli intervalli di indirizzi IP necessari. Gli intervalli IP sono documentati in [Azure server multi-factor authentication requisiti del firewall](../authentication/howto-mfaserver-deploy.md#azure-multi-factor-authentication-server-firewall-requirements).

---

### <a name="upcoming-changes-to-user-experience-in-identity-secure-score"></a>Modifiche imminenti all'esperienza utente nel punteggio di identità sicure

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Funzionalità del **prodotto** Identity Protection: protezione & della sicurezza delle identità

Si sta aggiornando il portale Identity Secure score per allinearsi alle modifiche introdotte nella [nuova versione](/microsoft-365/security/mtp/microsoft-secure-score-whats-new?view=o365-worldwide)di Microsoft Secure score. 

La versione di anteprima con le modifiche sarà disponibile all'inizio di settembre. Le modifiche apportate alla versione di anteprima includono:
- "Punteggio sicuro identità" rinominato "Punteggio sicuro per l'identità" per l'allineamento del marchio con Microsoft Secure Score
- Punti normalizzati alla scala standard e segnalati in percentuali anziché punti

In questa versione di anteprima i clienti possono passare dall'esperienza esistente alla nuova esperienza. Questa anteprima durerà fino alla fine del 2020 novembre. Al termine dell'anteprima, i clienti verranno automaticamente indirizzati alla nuova esperienza UX.

---

### <a name="new-restricted-guest-access-permissions-in-azure-ad---public-preview"></a>Nuove autorizzazioni di accesso guest con restrizioni in Azure AD-anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Controllo di accesso   
**Funzionalità del prodotto:** Gestione utenti

Sono state aggiornate le autorizzazioni a livello di directory per gli utenti guest. Queste autorizzazioni consentono agli amministratori di richiedere ulteriori restrizioni e controlli per l'accesso utente Guest esterno. Gli amministratori possono ora aggiungere ulteriori restrizioni per l'accesso degli utenti esterni ai profili e alle informazioni di appartenenza. Con questa funzionalità di anteprima pubblica, i clienti possono gestire l'accesso degli utenti esterni su larga scala offuscando le appartenenze ai gruppi, inclusa la limitazione degli utenti guest nella visualizzazione delle appartenenze ai gruppi in cui si trovano.

Per altre informazioni, vedere autorizzazioni di [accesso Guest limitate](../users-groups-roles/users-restrict-guest-permissions.md) e [autorizzazioni predefinite per gli utenti](./users-default-permissions.md).
 
---

### <a name="general-availability-of-delta-queries-for-service-principals"></a>Disponibilità generale delle query Delta per le entità servizio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Grafico MS  
**Funzionalità del prodotto:** esperienza di sviluppo
 
Microsoft Graph query Delta supporta ora il tipo di risorsa nella versione 1.0:
- Entità servizio

Ora i client possono tenere traccia delle modifiche apportate alle risorse in modo efficiente e offrono la soluzione migliore per sincronizzare le modifiche apportate a tali risorse con un archivio dati locale. Per informazioni su come configurare queste risorse in una query, vedere [usare una query Delta per tenere traccia delle modifiche nei dati Microsoft Graph](/graph/delta-query-overview).
 
---

### <a name="general-availability-of-delta-queries-for-oauth2permissiongrant"></a>Disponibilità generale delle query Delta per oAuth2PermissionGrant

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Grafico MS  
**Funzionalità del prodotto:** esperienza di sviluppo

Microsoft Graph query Delta supporta ora il tipo di risorsa nella versione 1.0:
- OAuth2PermissionGrant

I client possono ora tenere traccia delle modifiche apportate alle risorse in modo efficiente e offrono la soluzione migliore per sincronizzare le modifiche apportate a tali risorse con un archivio dati locale. Per informazioni su come configurare queste risorse in una query, vedere [usare una query Delta per tenere traccia delle modifiche nei dati Microsoft Graph](/graph/delta-query-overview).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---august-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-agosto 2020

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel 2020 agosto sono state aggiunte le 25 nuove applicazioni seguenti nella raccolta di app con supporto federativo:

[Backup365](https://portal.backup365.io/login), [pulpito](https://app.soapboxhq.com/create?step=auth&provider=azure-ad2-oauth2), [Alma SIS](https://almau.getalma.com/), [connettore Enlyft Dynamics 365](http://enlyft.com/), [soluzioni software per l'utilizzo dello spazio Serraview](../saas-apps/serraview-space-utilization-software-solutions-tutorial.md), notality, [visibilmente](../saas-apps/visibly-tutorial.md) [,](https://web.uniq.app/) [Zylo](../saas-apps/zylo-tutorial.md), Edmentum-corsi di [valutazione esatti percorso](https://auth.edmentum.com/elf/login), [CyberLAB](https://cyberlab.evolvesecurity.com/#/welcome), [Altamira HRM](../saas-apps/altamira-hrm-tutorial.md), [WireWheel](../saas-apps/wirewheel-tutorial.md), [Zix Compliance and Capture](https://sminstall.zixcorp.com/teams/teams.php?install_request=true&tenant_id=common), la piattaforma di [controllo business aziendale di GreenLight](../saas-apps/greenlight-enterprise-business-controls-platform-tutorial.md), [Genetec clearance](https://www.clearance.network/), [ISAMs](../saas-apps/isams-tutorial.md) [, VeraSMART, amiko](../saas-apps/verasmart-tutorial.md) [, twingate](https://amiko.web.rivero.app/) [,](https://auth.twingate.com/signup) [imbuto leasing](https://nestiolistings.com/sso/oidc/azure/authorize/), [Scalefusion](https://scalefusion.com/users/sign_in/) [, Bpanda,](https://goto.bpanda.com/login) [Vivun Calendar Connect](https://app.vivun.com/dashboard/calendar/connect) [,](https://www.wandera.com/) [FortiGate SSL VPN](../saas-apps/fortigate-ssl-vpn-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta di app Azure AD, vedere qui i dettagli https://aka.ms/AzureADAppRequest

---

### <a name="resource-forests-now-available-for-azure-ad-ds"></a>Foreste di risorse ora disponibili per Azure AD DS 

**Tipo:** Nuova **Categoria servizio funzionalità:** Azure ad Domain Services   
**Funzionalità del prodotto:** Azure AD Domain Services
 
La funzionalità delle foreste di risorse in Azure AD Domain Services è ora disponibile a livello generale. È ora possibile abilitare l'autorizzazione senza sincronizzazione dell'hash delle password per usare Azure AD Domain Services, inclusa l'autorizzazione per smart card. Per altre informazioni, vedere [la pagina relativa ai concetti e alle funzionalità dei set di repliche per Azure Active Directory Domain Services (anteprima)](../../active-directory-domain-services/concepts-replica-sets.md).
 
---

### <a name="regional-replica-support-for-azure-ad-ds-managed-domains-now-available"></a>È ora disponibile il supporto della replica a livello di area per Azure AD domini gestiti di DS

**Tipo:** Nuova funzionalità   
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services
 
È possibile espandere un dominio gestito in modo che ogni tenant di Azure AD contenga più di un set di repliche. I set di repliche possono essere aggiunti a qualsiasi rete virtuale con peering in qualsiasi area di Azure che supporta Azure AD Domain Services. I set di replica aggiuntivi in aree di Azure diverse forniscono il ripristino di emergenza geografico per le applicazioni legacy se un'area di Azure passa offline. Per altre informazioni, vedere [la pagina relativa ai concetti e alle funzionalità dei set di repliche per Azure Active Directory Domain Services (anteprima)](../../active-directory-domain-services/concepts-replica-sets.md).

---

### <a name="general-availability-of-azure-ad-my-sign-ins"></a>Disponibilità generale degli accessi Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Esperienze degli utenti finali
 
Azure AD gli accessi sono una nuova funzionalità che consente agli utenti aziendali di esaminare la cronologia di accesso per verificare eventuali attività insolite. Inoltre, questa funzionalità consente agli utenti finali di segnalare "questo non è stato" o "questo è stato me" nelle attività sospette. Per altre informazioni sull'uso di questa funzionalità, vedere [visualizzare e cercare l'attività di accesso recente dalla pagina di accesso personale](../user-help/my-account-portal-sign-ins-page.md#confirm-unusual-activity).
 
---

### <a name="sap-successfactors-hr-driven-user-provisioning-to-azure-ad-is-now-generally-available"></a>Il provisioning utenti basato su SAP SuccessFactors HR per Azure AD è ora disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
È ora possibile integrare SAP SuccessFactors come origine autorevole di identità con Azure AD e automatizzare il ciclo di vita delle identità end-to-end usando eventi HR come nuovi assunzioni e terminazioni per eseguire il provisioning e il deprovisioning degli account in Azure AD. 

Per altre informazioni su come configurare il provisioning in ingresso di SAP SuccessFactors in Azure AD, vedere l'esercitazione [configurare SAP SuccessFactors per Active Directory il provisioning degli utenti](../saas-apps/sap-successfactors-inbound-provisioning-tutorial.md).
 
---

### <a name="custom-open-id-connect-ms-graph-api-support-for-azure-ad-b2c"></a>Supporto di MS API Graph per la connessione Open ID personalizzato per Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
In precedenza, i provider di connessione Open ID personalizzati potevano essere aggiunti o gestiti solo tramite il portale di Azure. Ora i Azure AD B2C clienti possono aggiungerli e gestirli tramite Microsoft Graph versione beta di API. Per informazioni su come configurare questa risorsa con le API, vedere [tipo di risorsa IdentityProvider](/graph/api/resources/identityprovider?view=graph-rest-beta).
 
---

### <a name="assign-azure-ad-built-in-roles-to-cloud-groups"></a>Assegnare Azure AD ruoli predefiniti ai gruppi cloud

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso

È ora possibile assegnare Azure AD ruoli predefiniti ai gruppi cloud con questa nuova funzionalità. Ad esempio, è possibile assegnare il ruolo di amministratore di SharePoint a Contoso_SharePoint_Admins gruppo. È anche possibile usare PIM per rendere il gruppo un membro idoneo del ruolo, anziché concedere l'accesso permanente. Per informazioni su come configurare questa funzionalità, vedere [usare i gruppi di cloud per gestire le assegnazioni di ruolo in Azure Active Directory (anteprima)](../users-groups-roles/roles-groups-concept.md).
 
---

### <a name="insights-business-leader-built-in-role-now-available"></a>Il ruolo predefinito del leader aziendale di Insights è ora disponibile

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti nel ruolo di leader aziendale di Insights possono accedere a un set di dashboard e informazioni dettagliate tramite l' [applicazione M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Questo include l'accesso completo a tutti i dashboard e le funzionalità di esplorazione dei dati e delle informazioni fornite. Tuttavia, gli utenti con questo ruolo non hanno accesso alle impostazioni di configurazione del prodotto, che è responsabilità del ruolo di amministratore di Insights. Per ulteriori informazioni su questo ruolo, vedere [autorizzazioni per i ruoli di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#insights-business-leader)
 
---

### <a name="insights-administrator-built-in-role-now-available"></a>Il ruolo predefinito amministratore di Insights è ora disponibile

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso
 
Gli utenti nel ruolo di amministratore di Insights possono accedere al set completo di funzionalità amministrative nell' [applicazione M365 Insights](https://www.microsoft.com/microsoft-365/partners/workplaceanalytics). Un utente con questo ruolo può leggere le informazioni della directory, monitorare l'integrità dei servizi, i ticket di supporto file e accedere agli aspetti delle impostazioni dell'amministratore di Insights. Per ulteriori informazioni su questo ruolo, vedere [autorizzazioni per i ruoli di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md#insights-administrator)
 
--- 

### <a name="application-admin-and-cloud-application-admin-can-manage-extension-properties-of-applications"></a>L'amministratore dell'applicazione e l'amministratore di applicazioni cloud possono gestire le proprietà di estensione delle applicazioni

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso
 
In precedenza, solo l'amministratore globale poteva gestire la [proprietà di estensione](/graph/api/application-post-extensionproperty?view=graph-rest-beta&tabs=http). Questa funzionalità è ora abilitata anche per l'amministratore dell'applicazione e l'amministratore di applicazioni cloud.
 
---

### <a name="mim-2016-sp2-hotfix-462630-and-connectors-1113010"></a>4.6.263.0 hotfix MIM 2016 SP2 e connettori 1.1.1301.0

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Microsoft Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

È disponibile un [pacchetto hotfix rollup (Build 4.6.263.0)](https://support.microsoft.com/help/4576473/hotfix-rollup-package-build-4-6-263-0-is-available-for-microsoft-ident) per Microsoft Identity Manager (MIM) 2016 Service Pack 2 (SP2). Questo pacchetto di rollup contiene gli aggiornamenti per i componenti Gestione certificati MIM, Gestione sincronizzazione MIM e PAM. Inoltre, i connettori generici MIM Build 1.1.1301.0 include aggiornamenti per il connettore Graph.

---
 
## <a name="july-2020"></a>Luglio 2020

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>In qualità di amministratore IT, voglio destinare le app client usando l'accesso condizionale

**Tipo:** Modifica pianificata   
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Con la versione GA della condizione delle app client nell'accesso condizionale, i nuovi criteri vengono ora applicati per impostazione predefinita a tutte le applicazioni client. Sono inclusi i client di autenticazione legacy. I criteri esistenti rimarranno invariati, ma l'interruttore *configura sì/no* verrà rimosso dai criteri esistenti per individuare facilmente le app client a cui vengono applicati i criteri. 

Quando si creano nuovi criteri, assicurarsi di escludere gli utenti e gli account di servizio che utilizzano ancora l'autenticazione legacy. in caso contrario, verranno bloccati. [Altre informazioni](https://aka.ms/caclientapps)
 
---

### <a name="upcoming-scim-compliance-fixes"></a>Correzioni imminenti per la conformità di SCIM

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il servizio di provisioning Azure AD sfrutta lo standard SCIM per l'integrazione con le applicazioni. L'implementazione dello standard SCIM è in continua evoluzione e si prevede di apportare modifiche al comportamento in merito alla modalità di esecuzione delle operazioni di PATCH, nonché di impostare la proprietà "Active" su una risorsa. [Altre informazioni](../app-provisioning/application-provisioning-config-problem-scim-compatibility.md)
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>L'impostazione del proprietario del gruppo nel portale di amministrazione di Azure verrà modificata

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

È possibile configurare le impostazioni del proprietario nella pagina di impostazioni generale per limitare i privilegi di assegnazione dei proprietari a un gruppo limitato di utenti nel portale di amministrazione di Azure e nel pannello di accesso. Sarà presto possibile assegnare i privilegi del proprietario del gruppo non solo su questi due portali UX, ma anche applicare i criteri nel back-end per garantire un comportamento coerente tra gli endpoint, ad esempio PowerShell e Microsoft Graph. 

Si inizierà a disabilitare l'impostazione corrente per i clienti che non lo usano e offrirà un'opzione per definire l'ambito degli utenti per i privilegi del proprietario del gruppo nei prossimi mesi. Per istruzioni sull'aggiornamento delle impostazioni di gruppo, vedere Modificare le informazioni sul gruppo usando [Azure Active Directory](./active-directory-groups-settings-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context).

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Il servizio di registrazione Azure Active Directory sta terminando il supporto per TLS 1,0 e 1,1

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Registrazione e gestione dei dispositivi  
**Funzionalità del prodotto:** Piattaforma

Transport Layer Security (TLS) 1,2 e i server e i client di aggiornamento comunicheranno presto con Registrazione dispositivo Azure Active Directory servizio. Il supporto per TLS 1,0 e 1,1 per la comunicazione con Registrazione dispositivo Azure AD servizio viene ritirato:
- Il 31 agosto 2020, in tutti i cloud sovrani (GCC High, DoD e così via)
- Il 30 ottobre 2020, in tutti i cloud commerciali

[Altre](../devices/reference-device-registration-tls-1-2.md) informazioni su TLS 1,2 per il servizio di registrazione Azure ad.

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Accesso a Windows Hello for business visibile nei log di accesso Azure AD

**Tipo:** Correzione  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
Windows Hello for business consente agli utenti finali di accedere ai computer Windows con un movimento, ad esempio un PIN o una biometrica. Azure AD gli amministratori potrebbero voler distinguere gli accessi di Windows Hello for business da altri accessi Windows come parte del percorso di un'organizzazione per l'autenticazione senza password. 

Gli amministratori possono ora verificare se un'autenticazione di Windows ha usato Windows Hello for business controllando la scheda Dettagli autenticazione per un evento di accesso di Windows nel pannello Azure AD accessi nel portale di Azure. Le autenticazioni di Windows Hello for business includeranno "WindowsHelloForBusiness" nel campo metodo di autenticazione. Per ulteriori informazioni sull'interpretazione dei log di accesso, vedere la [documentazione relativa ai log di accesso](../reports-monitoring/concept-sign-ins.md).
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>Correzioni per il comportamento di eliminazione del gruppo e miglioramenti delle prestazioni

**Tipo:** Correzione  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
In precedenza, quando un gruppo è stato modificato da "in-scope" a "out-of-scope" e un amministratore ha fatto clic su Restart prima del completamento della modifica, l'oggetto gruppo non è stato eliminato. A questo punto l'oggetto gruppo verrà eliminato dall'applicazione di destinazione quando esce dall'ambito (disabilitato, eliminato, non assegnato o non ha superato il filtro di ambito). [Altre informazioni](../app-provisioning/how-provisioning-works.md#incremental-cycles)
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>Anteprima pubblica: gli amministratori possono ora aggiungere contenuto personalizzato nel messaggio di posta elettronica ai revisori durante la creazione di una verifica di accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance delle identità
 
Quando viene creata una nuova verifica di accesso, il revisore riceve un messaggio di posta elettronica che richiede di completare la verifica di accesso. Molti clienti hanno chiesto di aggiungere contenuto personalizzato al messaggio di posta elettronica, ad esempio le informazioni di contatto, o altri contenuti di supporto aggiuntivi per guidare il revisore. 

Ora disponibile in versione di anteprima pubblica, gli amministratori possono specificare contenuto personalizzato nel messaggio di posta elettronica inviato ai revisori aggiungendo contenuto nella sezione "avanzata" delle verifiche di accesso Azure AD. Per istruzioni sulla creazione [di verifiche di accesso, vedere creare una verifica di accesso di gruppi e applicazioni in Azure ad verifiche di accesso](../governance/create-access-review.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>Flusso del codice di autorizzazione per le app a singola pagina disponibili

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** esperienza di sviluppo
 
A causa delle restrizioni dei cookie di terze parti del browser moderni, ad esempio Safari ITP, le Spa dovranno usare il flusso del codice di autorizzazione anziché il flusso implicito per mantenere SSO e MSAL.js v 2. x supporterà il flusso del codice di autorizzazione. 

Sono disponibili aggiornamenti corrispondenti per il portale di Azure, quindi è possibile aggiornare la SPA in modo che sia di tipo "Spa" e usare il flusso del codice di autenticazione. Vedere [utenti di accesso e ottenere un token di accesso in una spa JavaScript usando il flusso del codice di autenticazione](../develop/quickstart-v2-javascript-auth-code.md) per ulteriori indicazioni.
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD proxy di applicazione supporta ora il client Web Servizi Desktop remoto

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Azure AD proxy di applicazione supporta ora il client Web di Servizi Desktop remoto (RDS). Il client Web RDS consente agli utenti di accedere a Desktop remoto infrastruttura tramite qualsiasi browser che supporta HTLM5, ad esempio Microsoft Edge, Internet Explorer 11, Google Chrome e così via. Gli utenti possono interagire con le app Remote o con i desktop, come avviene con un dispositivo locale da qualsiasi luogo. Con Azure AD proxy di applicazione è possibile aumentare la sicurezza della distribuzione di Servizi Desktop remoto applicando i criteri di pre-autenticazione e di accesso condizionale per tutti i tipi di app rich client. Per informazioni aggiuntive, vedere [pubblicare desktop remoto con Azure ad proxy di applicazione](../manage-apps/application-proxy-integrate-with-remote-desktop-services.md).
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>Prossima generazione Azure AD B2C flussi utente in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
L'esperienza di flusso utente semplificata offre la parità di funzionalità con funzionalità di anteprima ed è la casa per tutte le nuove funzionalità. Gli utenti saranno in grado di abilitare nuove funzionalità nello stesso flusso utente, riducendo la necessità di creare più versioni con ogni nuova versione della funzionalità. Infine, la nuova UX intuitiva semplifica la selezione e la creazione dei flussi utente. Provare subito a [creare un flusso utente](../../active-directory-b2c/tutorial-create-user-flows.md). 

Per ulteriori informazioni sui flussi degli utenti, vedere la pagina relativa [alle versioni dei flussi utente in Azure Active Directory B2C](../../active-directory-b2c/user-flow-versions.md#:~:text=    User flow  ,account. Usi ...  1 more rows ).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2020 luglio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel luglio 2020 abbiamo aggiunto le seguenti 55 nuove applicazioni nella raccolta di app con supporto federativo:

[Clap Your Hands](http://www.rmit.com.ar/), [Appreiz](https://microsoftteams.appreiz.com/), [Inextor Vault](https://inexto.com/inexto-suite/inextor), [Beekast](https://my.beekast.com/), [Templafy OpenID Connect](https://app.templafy.com/), [PeterConnects receptionist](https://msteams.peterconnects.com/), [AlohaCloud](https://appfusions.alohacloud.com/auth), [Control Tower](https://bpm.tnxcorp.com/sso/microsoft), [COCOOM](https://start.cocoom.com/), [Coins costruzioni cloud](https://sso.coinsconstructioncloud.com/#login/), [Medxnote mt](https://task.teamsmain.medx.im/authorization), [Reflekt](https://reflekt.konsolute.com/login), [rever](https://app.reverscore.net/access), [MyCompanyArchive](https://login.mycompanyarchive.com/) [, GREMINDERS,](https://app.greminders.com/o365-oauth) [Titanfile](../saas-apps/titanfile-tutorial.md), [Wootric](../saas-apps/wootric-tutorial.md), [SolarWinds Orion](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US),  [OpenText Directory Services](../saas-apps/opentext-directory-services-tutorial.md), [DataSite](../saas-apps/datasite-tutorial.md), [Blogin](../saas-apps/blogin-tutorial.md) [, IntSights, kpifire](../saas-apps/intsights-tutorial.md) [,](../saas-apps/kpifire-tutorial.md) [TextLine](../saas-apps/textline-tutorial.md), [Cloud Academy-SSO](../saas-apps/cloud-academy-sso-tutorial.md), [community Spark](../saas-apps/community-spark-tutorial.md), [chat](../saas-apps/chatwork-tutorial.md), [CloudSign](../saas-apps/cloudsign-tutorial.md), [C3M Cloud Control](../saas-apps/c3m-cloud-control-tutorial.md), [SmartHR](https://smarthr.jp/), [NumlyEngage™](../saas-apps/numlyengage-tutorial.md), [Single Sign-on di hub dati Michigan, in](../saas-apps/michigan-data-hub-single-sign-on-tutorial.md) [uscita](../saas-apps/egress-tutorial.md), [SendSafely](../saas-apps/sendsafely-tutorial.md), [Eletive](https://app.eletive.com/), [right-hand Cybersecurity ADI](https://right-hand.ai/), [Fyde Enterprise Authentication](https://enterprise.fyde.com/), [verme](../saas-apps/verme-tutorial.md), [lenses.io](../saas-apps/lensesio-tutorial.md), [momenta](../saas-apps/momenta-tutorial.md), [Rise](https://app.uprise.co/sign-in), [Q](https://q.moduleq.com/login), [CloudCords](../saas-apps/cloudcords-tutorial.md), addicate [bot](https://tellme365liteweb.azurewebsites.net/), [Inspire](https://app.inspiresoftware.com/), [Maverics Identity Orchestrator SAML Connector](https://www.strata.io/identity-fabric/), [Smartschool (School Management System)](https://smart-schoolapp.com/frmLoginForm), [Zepto-Intelligent cronometraggio](https://user.zepto-ai.com/signin), [studi.ly](https://studi.ly/), [Trackplan](http://www.trackplanfm.com/), [Skedda](../saas-apps/skedda-tutorial.md), [WhosOnLocation](../saas-apps/whos-on-location-tutorial.md), [Coggle](../saas-apps/coggle-tutorial.md), [KEMP LoadMaster](https://kemptechnologies.com/cloud-load-balancer/), [BrowserStack Single Sign-on](../saas-apps/browserstack-single-sign-on-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial

Per elencare l'applicazione nella raccolta di app Azure AD, vedere qui i dettagli https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2020 luglio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per la nuova app integrata [LinkedIn Learning](../saas-apps/linkedin-learning-provisioning-tutorial.md).

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>Visualizzare le assegnazioni di ruolo in tutti gli ambiti e la possibilità di scaricarli in un file CSV

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Ruoli di Azure AD  
**Funzionalità del prodotto:** Controllo di accesso
 
È ora possibile visualizzare le assegnazioni di ruolo in tutti gli ambiti per un ruolo nella scheda "ruoli e amministratori" del portale di Azure AD. È anche possibile scaricare le assegnazioni di ruolo per ogni ruolo in un file CSV. Per istruzioni sulla visualizzazione e l'aggiunta di assegnazioni di ruolo, vedere [visualizzare e assegnare ruoli di amministratore in Azure Active Directory](../users-groups-roles/directory-manage-roles-portal.md).
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure Multi-Factor Authentication Software Development (Azure AMF SDK) deprecato

**Tipo:** Deprecato  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Azure Multi-Factor Authentication Software Development (Azure AMF SDK) ha raggiunto la fine del ciclo di vita il 14 novembre 2018, come annunciato per la prima volta nel novembre 2017. Microsoft arresterà il servizio SDK in vigore il 30 settembre 2020. Tutte le chiamate effettuate all'SDK avranno esito negativo.

Se l'organizzazione USA Azure multi-factor authentication SDK, è necessario eseguire la migrazione entro il 30 settembre 2020:
- Azure multi-factor authentication SDK per MIM: se si usa l'SDK con MIM, è necessario eseguire la migrazione al server Azure multi-factor authentication e attivare Privileged Access Management (PAM) seguendo queste [istruzioni](/microsoft-identity-manager/working-with-mfaserver-for-mim).   
- Azure multi-factor authentication SDK per app personalizzate: è consigliabile integrare l'app in Azure AD e usare l'accesso condizionale per applicare l'autenticazione a più fattori. Per iniziare, vedere questa [pagina](../manage-apps/plan-an-application-integration.md). 

---

## <a name="june-2020"></a>Giugno 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Condizione di rischio utente nei criteri di accesso condizionale

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Il supporto dei rischi per gli utenti in Azure AD i criteri di accesso condizionale consente di creare più criteri basati sul rischio utente. Per utenti e app diversi possono essere necessari livelli di rischio utente minimi diversi. In base al rischio utente, è possibile creare criteri per bloccare l'accesso, richiedere l'autenticazione a più fattori, modificare la password sicura o reindirizzare a Microsoft Cloud App Security per applicare i criteri di sessione, ad esempio il controllo aggiuntivo.

La condizione di rischio utente richiede Azure AD Premium P2 perché usa Azure Identity Protection, un'offerta P2. per altre informazioni sull'accesso condizionale, vedere [Azure ad documentazione sull'accesso condizionale](../conditional-access/index.yml).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO supporta ora le app che richiedono l'impostazione di SPNameQualifier quando richiesto

**Tipo:** Correzione  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
Per alcune applicazioni SAML è necessario che SPNameQualifier venga restituito nell'oggetto dell'asserzione quando richiesto. A questo punto Azure AD risponde correttamente quando viene richiesto un SPNameQualifier nel criterio NameID della richiesta. Questa operazione funziona anche per l'accesso avviato da SP e l'accesso avviato da IdP verrà eseguito.  Per ulteriori informazioni sul protocollo SAML in Azure Active Directory, vedere [il protocollo SAML per Single Sign-on](../develop/single-sign-on-saml-protocol.md).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD collaborazione B2B supporta l'invito di utenti MSA e Google in tenant di Azure per enti pubblici

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 

I tenant di Azure per enti pubblici che usano le funzionalità di collaborazione B2B possono ora invitare gli utenti con un account Microsoft o Google. Per sapere se il tenant può usare queste funzionalità, seguire le istruzioni in [come è possibile stabilire se la collaborazione B2B è disponibile nel tenant di Azure US Government?](../external-identities/current-limitations.md#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>L'oggetto utente in MS Graph V1 include ora le proprietà externalUserState e externalUserStateChangedDateTime

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 

Le proprietà externalUserState e externalUserStateChangedDateTime possono essere usate per trovare i guest B2B invitati che non hanno accettato gli inviti ma anche l'automazione della compilazione, ad esempio l'eliminazione di utenti che non hanno accettato gli inviti dopo un certo numero di giorni. Queste proprietà sono ora disponibili in MS Graph V1. Per informazioni sull'uso di queste proprietà, vedere [tipo di risorsa utente](/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Gestire le sessioni di autenticazione in Azure AD accesso condizionale è ora disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Le funzionalità di gestione delle sessioni di autenticazione consentono di configurare la frequenza con cui gli utenti devono fornire le credenziali di accesso e se devono fornire le credenziali dopo aver chiuso e riaperto i browser per offrire maggiore sicurezza e flessibilità nell'ambiente in uso.
 
Inoltre, la gestione delle sessioni di autenticazione usata per l'autenticazione del primo fattore si applica solo ai dispositivi Azure AD aggiunti, Azure AD ibrido Uniti e Azure AD registrati. La gestione della sessione di autenticazione verrà ora applicata anche a multi-factor authentication. Per altre informazioni, vedere [configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](../conditional-access/howto-conditional-access-session-lifetime.md).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2020 giugno

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel giugno 2020 abbiamo aggiunto le 29 nuove applicazioni seguenti nella raccolta di app con supporto federativo:

[Shopify Plus](../saas-apps/shopify-plus-tutorial.md), [Ekarda](../saas-apps/ekarda-tutorial.md), [MailGates](../saas-apps/mailgates-tutorial.md), [BullseyeTDP](../saas-apps/bullseyetdp-tutorial.md), [Raketa](../saas-apps/raketa-tutorial.md), [Segment](../saas-apps/segment-tutorial.md), [ai auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.io](../saas-apps/proto.io-tutorial.md), [Gatekeeper](https://www.gatekeeperhq.com/), [Hub Planner](../saas-apps/hub-planner-tutorial.md), [ansira-partner go-to-Market casella degli strumenti](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation su cloud](../saas-apps/ibm-digital-business-automation-on-cloud-tutorial.md), [Kisi Physical Security](../saas-apps/kisi-physical-security-tutorial.md), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](../saas-apps/intelligencebank-tutorial.md), [pymetrics](../saas-apps/pymetrics-tutorial.md), [zero](https://www.teamzero.com/), [Instation](https://instation.invillia.com/), [EDX for business SAML 2,0 Integration](../saas-apps/edx-for-business-saml-integration-tutorial.md), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](../saas-apps/smartkargo-tutorial.md), [PKIsigning Platform](https://platform.pkisigning.nl/), [SiteIntel](../saas-apps/siteintel-tutorial.md), [Field ID](../saas-apps/field-id-tutorial.md), [curriculas SAML](../saas-apps/curricula-saml-tutorial.md), [Perforce Core-Helix Authentication Service](../saas-apps/perforce-helix-core-tutorial.md), [concompliance cloud](https://cloud.metacompliance.com/), [Smallstep](https://smallstep.com/sso-ssh/)  

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial . Per elencare l'applicazione nella raccolta di app Azure AD, leggere i dettagli qui: https://aka.ms/AzureADAppRequest .

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>I connettori API per l'iscrizione self-service di identità esterne sono ora disponibili in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
I connettori API delle identità esterne consentono di sfruttare le API Web per integrare l'iscrizione self-service con i sistemi cloud esterni. Ciò significa che è ora possibile richiamare le API Web come passaggi specifici in un flusso di iscrizione per attivare flussi di lavoro personalizzati basati sul cloud. Ad esempio, è possibile usare i connettori API per:

- Eseguire l'integrazione con un flusso di lavoro di approvazione personalizzato.
- Esegui la correzione dell'identità
- Convalida dati di input utente
- Sovrascrivi attributi utente
- Eseguire la logica di business personalizzata

Per altre informazioni su tutte le esperienze possibili con i connettori API, vedere [usare i connettori API per personalizzare ed estendere l'iscrizione self-service](../external-identities/api-connectors-overview.md)o [personalizzare le identità esterne self-service iscrizione con le integrazioni dell'API Web](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>Esegui il provisioning su richiesta e ottieni utenti nelle tue app in pochi secondi

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il servizio di provisioning Azure AD attualmente funziona su base ciclica. Il servizio viene eseguito ogni 40 minuti. La [funzionalità di provisioning su richiesta](https://aka.ms/provisionondemand) consente di selezionare un utente ed eseguirne il provisioning in pochi secondi. Questa funzionalità consente di risolvere rapidamente i problemi di provisioning, senza dover eseguire un riavvio per forzare l'avvio del ciclo di provisioning. 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>Nuova autorizzazione per l'uso di Azure AD gestione dei diritti in Graph

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Gestione dei diritti
 
Una nuova autorizzazione delegata EntitlementManagement. Read. All è ora disponibile per l'uso con l'API di gestione dei diritti in Microsoft Graph beta. Per altre informazioni sulle API disponibili, vedere [uso dell'API di gestione dei diritti Azure ad](/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>API di Identity Protection disponibili nella versione 1.0

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Le API Microsoft Graph riskyUsers e riskDetections sono ora disponibili a livello generale. Ora che sono disponibili nell'endpoint v 1.0, viene invitato a usarli nell'ambiente di produzione. Per ulteriori informazioni, consultare la documentazione di [Microsoft Graph](/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Le etichette di riservatezza per applicare i criteri ai gruppi di Microsoft 365 sono ora disponibili a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione
 

È ora possibile creare etichette di riservatezza e usare le impostazioni delle etichette per applicare i criteri a gruppi di Microsoft 365, inclusi i criteri di accesso utente esterno (pubblico o privato). È possibile creare un'etichetta con l'informativa sulla privacy come privata e i criteri di accesso utente esterno per non consentire l'aggiunta di utenti guest. Quando un utente applica questa etichetta a un gruppo, il gruppo sarà privato e nessun utente Guest potrà essere aggiunto al gruppo. 

Le etichette di riservatezza sono importanti per proteggere i dati cruciali per l'azienda e consentono di gestire i gruppi su larga scala, in modo conforme e sicuro. Per informazioni sull'uso delle etichette di riservatezza, vedere [assegnare etichette di riservatezza ai gruppi di Microsoft 365 in Azure Active Directory (anteprima)](../users-groups-roles/groups-assign-sensitivity-labels.md).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Aggiornamenti per il supporto di Microsoft Identity Manager per clienti Azure AD Premium

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Microsoft Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il supporto di Azure è ora disponibile per i componenti di integrazione Azure AD di Microsoft Identity Manager 2016, fino alla fine del supporto esteso per Microsoft Identity Manager 2016. Per altre informazioni, vedere l' [aggiornamento del supporto per Azure ad Premium clienti che usano Microsoft Identity Manager](/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>L'uso di condizioni di appartenenza a gruppi nella configurazione delle attestazioni SSO è aumentato

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
In precedenza, il numero di gruppi che è possibile usare quando si modificano le attestazioni in modo condizionale in base all'appartenenza al gruppo all'interno di una singola configurazione di applicazione era limitato a 10. L'uso di condizioni di appartenenza a gruppi nella configurazione delle attestazioni SSO è ora aumentato fino a un massimo di 50 gruppi. Per ulteriori informazioni sulla configurazione delle attestazioni, vedere la pagina relativa alla [configurazione delle attestazioni SSO delle applicazioni aziendali](../develop/active-directory-saml-claims-customization.md#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Abilitazione della formattazione di base sul componente testo della pagina di accesso in informazioni personalizzate distintive dell'azienda.

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
La funzionalità di personalizzazione dell'azienda nell'esperienza di accesso Azure AD/Microsoft 365 è stata aggiornata per consentire al cliente di aggiungere collegamenti ipertestuali e formattazione semplice, inclusi il tipo di carattere, la sottolineatura e il corsivo in grassetto. Per istruzioni sull'uso di questa funzionalità, vedere [aggiungere informazioni personalizzate alla pagina di accesso Azure Active Directory dell'organizzazione](./customize-branding.md).

---

### <a name="provisioning-performance-improvements"></a>Miglioramenti delle prestazioni del provisioning

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il servizio di provisioning è stato aggiornato per ridurre il tempo necessario per il completamento di un [ciclo incrementale](../app-provisioning/how-provisioning-works.md#incremental-cycles) . Questo significa che gli utenti e i gruppi verranno sottoposti a provisioning nelle applicazioni più velocemente rispetto a quanto precedentemente fatto. Tutti i nuovi processi di provisioning creati dopo 6/10/2020 trarranno automaticamente vantaggio dai miglioramenti delle prestazioni. Tutte le applicazioni configurate per il provisioning prima del 6/10/2020 dovranno essere riavviate una volta dopo 6/10/2020 per sfruttare i miglioramenti delle prestazioni. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Annunciare la deprecazione di ADAL e MS Graph parità

**Tipo:** Deprecato  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi

Ora che sono disponibili le librerie di autenticazione Microsoft (MSAL), non verranno più aggiunte nuove funzionalità alle librerie di autenticazione Azure Active Directory (ADAL) e le patch di sicurezza verranno terminate il 30 giugno 2022. Per ulteriori informazioni su come eseguire la migrazione a MSAL, vedere [eseguire la migrazione di applicazioni a Microsoft Authentication Library (MSAL)](../develop/msal-migration.md).

Abbiamo inoltre completato il lavoro per rendere disponibili tutte le funzionalità di Azure AD Graph tramite MS Graph. Quindi, Azure AD API Graph riceveranno solo bugfix e correzioni per la sicurezza fino al 30 giugno 2022. Per altre informazioni, vedere [aggiornare le applicazioni per l'uso di Microsoft Authentication Library e dell'API Microsoft Graph](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
 
## <a name="may-2020"></a>Maggio 2020

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>Ritiro delle proprietà nelle API accesso, riskyUsers e riskDetections

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Attualmente, i tipi enumerati vengono usati per rappresentare la proprietà riskType nell'API riskDetections e in riskyUserHistoryItem (in anteprima). I tipi enumerati vengono usati anche per la proprietà riskEventTypes nell'API accesso. In futuro, queste proprietà verranno rappresentate come stringhe. 

I clienti devono passare alla proprietà riskEventType nell'API beta riskDetections e riskyUserHistoryItem e riskEventTypes_v2 proprietà nell'API beta accesso del 9 settembre 2020. A tale data, verranno ritirate le proprietà riskType e riskEventTypes correnti. Per altre informazioni, vedere [le modifiche alle proprietà degli eventi di rischio e alle API di Identity Protection in Microsoft Graph](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/).

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Deprecazione della proprietà riskEventTypes nell'API accesso v 1.0 su Microsoft Graph

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

I tipi enumerati passeranno ai tipi stringa quando rappresentano le proprietà degli eventi di rischio in Microsoft Graph settembre 2020. Oltre ad avere un effetto sulle API di anteprima, questa modifica influirà anche sull'API accesso in produzione.

Per l'API accesso v 1.0 è stata introdotta una nuova proprietà riskEventsTypes_v2 (String). La proprietà riskEventTypes (enum) corrente viene ritirata l'11 giugno 2022 in base ai criteri di Microsoft Graph deprecazione. I clienti devono passare alla proprietà riskEventTypes_v2 nell'API v 1.0 accesso dell'11 giugno 2022. Per ulteriori informazioni, vedere la pagina [relativa alla deprecazione della proprietà riskEventTypes nell'API accesso v 1.0 in Microsoft Graph](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//).

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>Modifiche imminenti alle notifiche tramite posta elettronica

**Tipo:** Modifica pianificata  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Sono state apportate le modifiche seguenti alle notifiche tramite posta elettronica per l'autenticazione a più fattori cloud:

Le notifiche tramite posta elettronica verranno inviate dall'indirizzo seguente: azure-noreply@microsoft.com e msonlineservicesteam@microsoftonline.com . Stiamo aggiornando il contenuto dei messaggi di posta elettronica di avviso di illecito per indicare meglio i passaggi necessari per sbloccare gli usi.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Nuova iscrizione self-service per gli utenti di domini federati che non possono accedere ai team Microsoft perché non sono sincronizzati con Azure Active Directory.

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 

Attualmente, gli utenti che si trovano in domini federati in Azure AD, ma che non sono sincronizzati nel tenant, non possono accedere ai team. A partire dalla fine di giugno, questa nuova funzionalità consentirà di eseguire questa operazione estendendo la funzionalità di iscrizione verificata tramite posta elettronica esistente. Ciò consentirà agli utenti che possono accedere a un IdP federato, ma che non dispongono ancora di un oggetto utente in Azure ID, di creare automaticamente un oggetto utente e di essere autenticato per i team. Il relativo oggetto utente sarà contrassegnato come "iscrizione self-service". Si tratta di un'estensione della funzionalità esistente per eseguire l'iscrizione automatica verificata tramite posta elettronica che gli utenti nei domini gestiti possono eseguire e possono essere controllati con lo stesso flag. Questa modifica verrà completata durante i due mesi seguenti. Per informazioni sugli aggiornamenti della documentazione, vedere [qui](../users-groups-roles/directory-self-service-signup.md).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Correzione imminente: è in corso l'aggiornamento del documento di individuazione OIDC per il cloud di Azure per enti pubblici in modo che faccia riferimento agli endpoint grafici corretti.

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Cloud sovrani  
**Funzionalità del prodotto:** Autenticazione utente
 
A partire da giugno, il documento di individuazione OIDC [Microsoft Identity Platform e il protocollo OpenID Connect](../develop/v2-protocols-oidc.md) nell'endpoint [cloud di Azure per enti pubblici](../develop/authentication-national-cloud.md) (login.microsoftonline.US), inizieranno a restituire l'endpoint di [cloud Graph nazionale](/graph/deployments) corretto ( https://graph.microsoft.us o https://dod-graph.microsoft.us) , in base al tenant fornito.  Attualmente fornisce il campo "msgraph_host" dell'endpoint grafico errato (graph.microsoft.com).  

Questa correzione di bug verrà implementata gradualmente per circa 2 mesi.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Gli utenti di Azure per enti pubblici non saranno più in grado di accedere a login.microsoftonline.com

**Tipo:** Pianificare la modifica  
**Categoria di servizio:** Cloud sovrani  
**Funzionalità del prodotto:** Autenticazione utente
 
Il 1 giugno 2018, l'autorità ufficiale Azure Active Directory (Azure AD) per Azure per enti pubblici è cambiata da https://login-us.microsoftonline.com a https://login.microsoftonline.us . Se si è proprietari di un'applicazione in un tenant di Azure per enti pubblici, è necessario aggiornare l'applicazione per consentire agli utenti di accedere all'endpoint. US.

A partire dal 5 maggio, Azure AD inizierà a applicare la modifica dell'endpoint, bloccando l'accesso degli utenti di Azure Government alle app ospitate nei tenant di Azure per enti pubblici usando l'endpoint pubblico (microsoftonline.com). Le app interessate inizieranno a visualizzare un errore AADSTS900439-USGClientNotSupportedOnPublicEndpoint. 

Verrà apportata una distribuzione graduale di questa modifica con l'imposizione prevista per il completamento in tutte le app 2020 giugno. Per altri dettagli, vedere il [post del Blog di Azure per enti pubblici](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/).

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>La richiesta di disconnessione singola SAML ora Invia NameID nel formato corretto

**Tipo:** Correzione  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Quando un utente fa clic su disconnessione (ad esempio, nel portale di app Web), Azure AD invia un messaggio di disconnessione SAML singolo a ogni app che è attiva nella sessione utente ed è configurato un URL di disconnessione. Questi messaggi contengono un NameID in un formato permanente.

Se il token di accesso SAML originale usava un formato diverso per NameID (ad esempio, email/UPN), l'app SAML non può correlare il NameID nel messaggio di disconnessione a una sessione esistente (poiché il NameIDs usato in entrambi i messaggi è diverso), che ha causato l'eliminazione del messaggio di disconnessione da parte dell'app SAML e l'utente può rimanere connesso. Questa correzione rende il messaggio di disconnessione coerente con il NameID configurato per l'applicazione.

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>Il ruolo di amministratore di identità ibrido è ora disponibile con il provisioning cloud

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di Azure AD cloud  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Gli amministratori IT possono iniziare a usare il nuovo ruolo "amministratore ibrido" come ruolo con privilegi minimi per la configurazione del provisioning cloud ADConnect di Azure. Con questo nuovo ruolo, non è più necessario usare il ruolo di amministratore globale per configurare e configurare il provisioning del cloud. [Altre informazioni](../users-groups-roles/roles-delegate-by-task.md#connect)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2020 maggio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel 2020 maggio sono state aggiunte le seguenti 36 nuove applicazioni nella raccolta di app con supporto federativo:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [ASC Recording Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-Engaged](https://b-engaged.se/), [Competella Contact Center Workgroup](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft Identity](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [insuite](../saas-apps/insuite-tutorial.md), [Change Process Management](../saas-apps/change-process-management-tutorial.md), [Cyara CX Assurance Platform](../saas-apps/cyara-cx-assurance-platform-tutorial.md), [Smart Global Governance](../saas-apps/smart-global-governance-tutorial.md) [,](../saas-apps/vyond-tutorial.md) [prezi](../saas-apps/prezi-tutorial.md), [MapBox](../saas-apps/mapbox-tutorial.md), [datava Enterprise Service Platform](../saas-apps/datava-enterprise-service-platform-tutorial.md), [Stravagance](../saas-apps/whimsical-tutorial.md), [Trelica](../saas-apps/trelica-tutorial.md), [EasySSO for confluenza](../saas-apps/easysso-for-confluence-tutorial.md), [EasySSO for bitbucket](../saas-apps/easysso-for-bitbucket-tutorial.md), EasySSO [for Bamboo](../saas-apps/easysso-for-bamboo-tutorial.md), [Torii](../saas-apps/torii-tutorial.md), [Axiad cloud](../saas-apps/axiad-cloud-tutorial.md), [humany](../saas-apps/humanage-tutorial.md), [ColorTokens ZTNA](../saas-apps/colortokens-ztna-tutorial.md) [, Tagetik, ShareVault](../saas-apps/cch-tagetik-tutorial.md) [, Vyond](../saas-apps/sharevault-tutorial.md)di [lavoro, all](../saas-apps/textexpander-tutorial.md) [Home CRM](../saas-apps/anyone-home-crm-tutorial.md), [TextExpander](../saas-apps/askspoke-tutorial.md), [Ice Contact Center](../saas-apps/ice-contact-center-tutorial.md)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial .

Per elencare l'applicazione nella raccolta di app Azure AD, vedere qui i dettagli https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>La modalità di sola segnalazione per l'accesso condizionale è ora disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

La [modalità di sola segnalazione per Azure ad accesso condizionale](../conditional-access/concept-conditional-access-report-only.md) consente di valutare il risultato di un criterio senza applicare i controlli di accesso. È possibile testare i criteri di solo report nell'organizzazione e comprenderne l'effetto prima di abilitarli, rendendo la distribuzione più sicura e semplice. Negli ultimi mesi, abbiamo visto l'adozione avanzata della modalità solo report: gli utenti di 26M sono già inclusi nell'ambito di un criterio di sola segnalazione. Con l'annuncio odierno, i nuovi criteri di accesso condizionale di Azure AD verranno creati in modalità solo report per impostazione predefinita. Ciò significa che è possibile monitorare l'effetto dei criteri dal momento in cui sono stati creati. E per coloro che usano le API Graph di Microsoft, è possibile gestire anche i [criteri di solo report a livello di codice](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Iscrizione self-service per utenti Guest

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
Con le identità esterne in Azure AD, è possibile consentire agli utenti esterni all'organizzazione di accedere alle app e alle risorse, consentendo al contempo di accedere usando qualsiasi identità preferita. Durante la condivisione di un'applicazione con utenti esterni, è possibile sapere sempre in anticipo chi dovrà accedere all'applicazione. Con l' [iscrizione self-service](../external-identities/self-service-sign-up-overview.md), è possibile consentire agli utenti guest di iscriversi e ottenere un account Guest per le app line-of-business (LOB). Il flusso di iscrizione può essere creato e personalizzato per supportare Azure AD e le identità di social networking. È anche possibile raccogliere informazioni aggiuntive sull'utente durante l'iscrizione.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Le informazioni dettagliate sull'accesso condizionale e la cartella di lavoro report sono disponibili a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

La [cartella di lavoro Insights e Reporting](../conditional-access/howto-conditional-access-insights-reporting.md) offre agli amministratori una visualizzazione riepilogativa del Azure ad l'accesso condizionale nel tenant. Con la possibilità di selezionare un singolo criterio, gli amministratori possono comprendere meglio quali sono i criteri e monitorare le modifiche in tempo reale. La cartella di lavoro trasmette i dati archiviati in monitoraggio di Azure, che è possibile configurare in pochi minuti [seguendo queste istruzioni](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Per rendere il dashboard più individuabile, è stato spostato nella nuova scheda Insights and Reporting nel menu di Azure AD accesso condizionale.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Il pannello dei dettagli dei criteri per l'accesso condizionale è in versione di anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Il pannello nuovi [Dettagli criteri](../conditional-access/troubleshoot-conditional-access.md) Visualizza le assegnazioni, le condizioni e i controlli soddisfatti durante la valutazione dei criteri di accesso condizionale. È possibile accedere al pannello selezionando una riga nelle schede accesso condizionale o solo report dei dettagli di accesso.

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Nuove funzionalità di query per gli oggetti directory in Microsoft Graph sono disponibili in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Funzionalità del **prodotto** MS Graph: esperienza dello sviluppatore

Sono state introdotte nuove funzionalità per le API di Microsoft Graph oggetti directory, abilitando le operazioni di conteggio, ricerca, filtro e ordinamento. Ciò consentirà agli sviluppatori di eseguire rapidamente query sugli oggetti directory senza soluzioni alternative, ad esempio l'ordinamento e il filtro in memoria. Scopri di più in questo [post di Blog](https://aka.ms/CountFilterMSGraphAAD).

Siamo attualmente in anteprima pubblica, cercando commenti e suggerimenti. Invia i tuoi commenti con questo [breve sondaggio](https://aka.ms/MsGraphAADSurveyDocs).

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>Configurare Single Sign-On basati su SAML usando l'API Microsoft Graph (beta)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
È ora disponibile il supporto per la creazione e la configurazione di un'applicazione dalla raccolta di Azure AD usando le API di MS Graph in beta. Se è necessario configurare Single Sign-On basati su SAML per più istanze di un'applicazione, risparmiare tempo usando le API Microsoft Graph per [automatizzare la configurazione dei Single Sign-on basati su SAML](/graph/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2020 maggio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

* [8X8](../saas-apps/8x8-provisioning-tutorial.md)
* [Juno Journey](../saas-apps/juno-journey-provisioning-tutorial.md)
* [MediusFlow](../saas-apps/mediusflow-provisioning-tutorial.md)
* [New Relic by Organization](../saas-apps/new-relic-by-organization-provisioning-tutorial.md)
* [Oracle Cloud Infrastructure Console](../saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial.md)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](../app-provisioning/user-provisioning.md).

---

### <a name="saml-token-encryption-is-generally-available"></a>La crittografia del token SAML è disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
La [crittografia del token SAML](../manage-apps/howto-saml-token-encryption.md) consente di configurare le applicazioni in modo da ricevere asserzioni SAML crittografate. La funzionalità è ora disponibile a livello generale in tutti i cloud.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Il nome del gruppo attestazioni nei token dell'applicazione è disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
Le attestazioni di gruppo rilasciate in un token possono ora essere limitate solo ai gruppi assegnati all'applicazione.  Questo è particolarmente importante quando gli utenti sono membri di un numero elevato di gruppi e si è verificato un rischio di superamento dei limiti delle dimensioni dei token. Grazie a questa nuova funzionalità, la possibilità di [aggiungere nomi di gruppo ai token](../hybrid/how-to-connect-fed-group-claims.md) è disponibile a livello generale.
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Il writeback giorni lavorativi supporta ora l'impostazione degli attributi del numero di telefono lavoro

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
È stata migliorata l'app di provisioning del writeback dei giorni lavorativi per supportare il writeback del numero di telefono dell'ufficio e degli attributi dei numeri cellulari. Oltre alla posta elettronica e al nome utente, ora è possibile configurare l'app per il provisioning writeback dei giorni lavorativi in modo da Azure AD alla giornata lavorativa. Per altri dettagli su come configurare il writeback dei numeri di telefono, vedere l'esercitazione sull'app [writeback del giorno lavorativo](https://aka.ms/WorkdayWriteback) . 

---

### <a name="publisher-verification-preview"></a>Verifica dell'editore (anteprima)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** esperienza di sviluppo
 
La verifica dell'autore (anteprima) consente agli amministratori e agli utenti finali di controllare l'autenticità degli sviluppatori di applicazioni che si integrano con Microsoft Identity Platform. Per informazioni dettagliate, vedere [Verifica dell'editore (anteprima)](../develop/publisher-verification-overview.md).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Flusso del codice di autorizzazione per le app a singola pagina

**Tipo:** Categoria del **servizio** funzionalità modificata: **funzionalità del prodotto** di autenticazione: esperienza dello sviluppatore

A causa delle restrizioni dei cookie di terze parti del browser moderni, [ad esempio Safari ITP](../develop/reference-third-party-cookies-spas.md), le Spa dovranno usare il flusso del codice di autorizzazione anziché il flusso implicito per mantenere SSO. MSAL.js v 2. x supporterà ora il flusso del codice di autorizzazione. Ci sono aggiornamenti corrispondenti per il portale di Azure, quindi è possibile aggiornare la SPA in modo che sia di tipo "Spa" e usare il flusso del codice di autenticazione. Per istruzioni, vedere [Guida introduttiva: accedere agli utenti e ottenere un token di accesso in una spa JavaScript usando il flusso del codice di autenticazione](../develop/quickstart-v2-javascript-auth-code.md).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Un filtro migliorato per i dispositivi è in versione di anteprima pubblica

**Tipo:** Funzionalità modificata   
**Categoria di servizio:** Funzionalità del **prodotto** gestione dei dispositivi: gestione del ciclo di vita dei dispositivi
 
In precedenza, gli unici filtri utilizzabili erano "Enabled" e "Activity date". È ora possibile [filtrare l'elenco dei dispositivi in altre proprietà](../devices/device-management-azure-portal.md#device-list-filtering-preview), tra cui tipo di sistema operativo, tipo di join, conformità e altro ancora. Queste aggiunte dovrebbero semplificare l'individuazione di un dispositivo specifico.

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>La nuova esperienza di Registrazioni app per Azure AD B2C è ora disponibile a livello generale

**Tipo:** Funzionalità modificata   
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
La nuova esperienza Registrazioni app per Azure AD B2C è ora disponibile a livello generale. 

In precedenza, era necessario gestire le applicazioni B2C rivolte agli utenti, indipendentemente dal resto delle app, usando l'esperienza "applicazioni" legacy. Ciò significava diverse esperienze di creazione di app in posizioni diverse in Azure.

La nuova esperienza Mostra tutte le registrazioni di app B2C e Azure AD le registrazioni dell'app in un'unica posizione e fornisce un modo coerente per gestirle. Se è necessario gestire un'app per i clienti o un'app che ha accesso a Microsoft Graph per gestire le risorse Azure AD B2C a livello di codice, è sufficiente apprendere un solo modo per eseguire le operazioni.

È possibile raggiungere la nuova esperienza passando al servizio Azure AD B2C e selezionando il pannello Registrazioni app. L'esperienza è accessibile anche dal servizio Azure Active Directory.

L'esperienza Azure AD B2C Registrazioni app si basa sull'esperienza di [registrazione delle app](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) generale per Azure ad tenant, ma è adattata per Azure ad B2C. L'esperienza "applicazioni" Legacy sarà deprecata in futuro.

Per ulteriori informazioni, visitare [la nuova esperienza di registrazione delle app per Azure ad B2C](https://aka.ms/b2cappregtraining).

---

## <a name="april-2020"></a>Aprile 2020

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>L'esperienza di registrazione delle informazioni di sicurezza combinata è ora disponibile a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Autenticazioni (accessi)

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

L'esperienza di registrazione combinata per Multi-Factor Authentication (multi-factor authentication) e la reimpostazione della password self-service (SSPR) è ora disponibile a livello generale. Questa nuova esperienza di registrazione consente agli utenti di registrarsi per l'autenticazione a più fattori e SSPR in un unico processo dettagliato. Quando si distribuisce la nuova esperienza per l'organizzazione, gli utenti possono registrarsi in minor tempo e con meno problemi. Vedere il post di Blog [qui](https://bit.ly/3etiRyQ).

---

### <a name="continuous-access-evaluation"></a>Valutazione dell'accesso continuo

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Autenticazioni (accessi)

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

La valutazione dell'accesso continuo è una nuova funzionalità di sicurezza che consente di applicare in tempo quasi reale i criteri alle relying party che utilizzano Azure AD token di accesso quando si verificano eventi in Azure AD, ad esempio l'eliminazione di account utente. Questa funzionalità è stata implementata per prima per i team e i client Outlook. Per ulteriori informazioni, leggere il [Blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) e la  [documentazione](./concept-fundamentals-continuous-access-evaluation.md).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Accesso SMS: i prima riga Worker possono accedere alle applicazioni con supporto di Azure AD con il numero di telefono e nessuna password

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Autenticazioni (accessi)

**Funzionalità del prodotto:** Autenticazione utente

Office sta avviando una serie di app aziendali per dispositivi mobili che soddisfano le organizzazioni non tradizionali e i dipendenti di organizzazioni di grandi dimensioni che non usano la posta elettronica come metodo di comunicazione principale. Queste app sono destinate a dipendenti di Frontline, lavoratori senza scrivania, agenti di campo o dipendenti al dettaglio che potrebbero non ricevere un indirizzo di posta elettronica dal proprio datore di lavoro, avere accesso a un computer o a esso. Questo progetto consente ai dipendenti di accedere alle applicazioni aziendali immettendo un numero di telefono e Roundtripping un codice. Per altri dettagli, vedere la [documentazione amministrativa](../authentication/howto-authentication-sms-signin.md) e la [documentazione per gli utenti finali](../user-help/sms-sign-in-explainer.md).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Invitare gli utenti interni a usare la collaborazione B2B

**Tipo:** Nuova funzionalità

**Categoria di servizio:** B2B

**Funzionalità del prodotto:**

Stiamo espandendo la funzionalità di invito B2B per consentire agli account interni esistenti di essere invitati a usare le credenziali di collaborazione B2B in futuro. Questa operazione viene eseguita passando l'oggetto utente all'API invite, oltre ai parametri tipici come l'indirizzo di posta elettronica invitato. L'ID oggetto, l'UPN, l'appartenenza al gruppo, l'assegnazione dell'app e così via sono intatti, ma in futuro utilizzeranno B2B per l'autenticazione con le credenziali del tenant principale, anziché le credenziali interne utilizzate prima dell'invito. Per informazioni dettagliate, vedere la [documentazione](../external-identities/invite-internal-users.md)di.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>La modalità di sola segnalazione per l'accesso condizionale è ora disponibile a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Accesso condizionale

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

La [modalità di sola segnalazione per Azure ad accesso condizionale](../conditional-access/concept-conditional-access-report-only.md) consente di valutare il risultato di un criterio senza applicare i controlli di accesso. È possibile testare i criteri di solo report nell'organizzazione e comprenderne l'effetto prima di abilitarli, rendendo la distribuzione più sicura e semplice. Negli ultimi mesi, abbiamo visto l'adozione avanzata della modalità solo report, con gli utenti di 26M già nell'ambito di un criterio di sola segnalazione. Con questo annuncio, i nuovi criteri di accesso condizionale di Azure AD verranno creati in modalità solo report per impostazione predefinita. Ciò significa che è possibile monitorare l'effetto dei criteri dal momento in cui sono stati creati. Per coloro che usano le API Graph di Microsoft, è inoltre possibile gestire i [criteri di solo report a livello di codice](/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Le informazioni dettagliate sull'accesso condizionale e la cartella di lavoro report sono disponibili a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Accesso condizionale

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

La cartella di [lavoro Insights e report](../conditional-access/howto-conditional-access-insights-reporting.md) di accesso condizionale offre agli amministratori una visualizzazione riepilogativa di Azure ad accesso condizionale nel tenant. Con la possibilità di selezionare un singolo criterio, gli amministratori possono comprendere meglio quali sono i criteri e monitorare le modifiche in tempo reale. La cartella di lavoro trasmette i dati archiviati in monitoraggio di Azure, che è possibile configurare in pochi minuti [seguendo queste istruzioni](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). Per rendere il dashboard più individuabile, è stato spostato nella nuova scheda Insights and Reporting nel menu di Azure AD accesso condizionale.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Il pannello dei dettagli dei criteri per l'accesso condizionale è in versione di anteprima pubblica

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Accesso condizionale

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Il pannello nuovi [Dettagli criteri](../conditional-access/troubleshoot-conditional-access.md) Visualizza le assegnazioni, le condizioni e i controlli soddisfatti durante la valutazione dei criteri di accesso condizionale. È possibile accedere al pannello selezionando una riga nelle schede **accesso condizionale** o **solo report** dei dettagli di accesso.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2020 aprile

**Tipo:** Nuova funzionalità

**Categoria di servizio:** App aziendali

**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel 2020 aprile sono state aggiunte le 31 nuove app con supporto federativo per la raccolta di app: 

[App SincroPool](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](../saas-apps/float-tutorial.md), [LMS365](https://lms.365.systems/), [IWT Procurement Suite](../saas-apps/iwt-procurement-suite-tutorial.md), [Lunni](https://lunni.fi/), [EasySSO per JIRA](../saas-apps/easysso-for-jira-tutorial.md), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), dashboard di [Meraki](../saas-apps/meraki-dashboard-tutorial.md), [Microsoft 365 Mover](https://app.mover.io/login), [speaker Engage](https://speakerengage.com/login.php), [onestamente](../saas-apps/honestly-tutorial.md), [Ally](../saas-apps/ally-tutorial.md), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia, gr8](../saas-apps/alertmedia-tutorial.md) [people](../saas-apps/gr8-people-tutorial.md), [Pending](../saas-apps/pendo-tutorial.md), [HighGround](../saas-apps/highground-tutorial.md), [Harmony](../saas-apps/harmony-tutorial.md), [orari Solutions](../saas-apps/timetabling-solutions-tutorial.md), il [fatto che fai clic su](../saas-apps/synchronet-click-tutorial.md)Empower, potenzia, [rafforza](https://www.made-in-office.com/en/) [cloud](../saas-apps/fortes-change-cloud-tutorial.md), [tornasole](../saas-apps/litmus-tutorial.md), [GroupTalk](https://recorder.grouptalk.com/), [Frontify](../saas-apps/frontify-tutorial.md), [MongoDB cloud](../saas-apps/mongodb-cloud-tutorial.md), [TickitLMS Learn](../saas-apps/tickitlms-learn-tutorial.md), [Coco](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [Nitro Productivity Suite](../saas-apps/nitro-productivity-suite-tutorial.md) , [Trend Micro Web Security (TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Supporto delle query Microsoft Graph Delta per oAuth2PermissionGrant disponibile per l'anteprima pubblica

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Grafico MS

**Funzionalità del prodotto:** esperienza di sviluppo

La query Delta per oAuth2PermissionGrant è disponibile per l'anteprima pubblica. È ora possibile tenere traccia delle modifiche senza dover eseguire continuamente il polling di Microsoft Graph. [Altre informazioni.](/graph/api/oAuth2PermissionGrant-delta?tabs=http&view=graph-rest-beta)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Supporto delle query Delta Microsoft Graph per il contatto aziendale disponibile a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Grafico MS

**Funzionalità del prodotto:** esperienza di sviluppo

La query Delta per i contatti dell'organizzazione è disponibile a livello generale. È ora possibile tenere traccia delle modifiche nelle app di produzione senza dover eseguire continuamente il polling Microsoft Graph. Sostituire il codice esistente che esegue continuamente il polling dei dati di orgContact per query Delta per migliorare significativamente le prestazioni. [Altre informazioni.](/graph/api/orgcontact-delta?tabs=http&view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Supporto delle query Delta Microsoft Graph per l'applicazione disponibile a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Grafico MS

**Funzionalità del prodotto:** esperienza di sviluppo

La query Delta per le applicazioni è disponibile a livello generale. È ora possibile tenere traccia delle modifiche nelle app di produzione senza dover eseguire continuamente il polling Microsoft Graph. Sostituire il codice esistente che esegue continuamente il polling dei dati delle applicazioni tramite una query Delta per migliorare significativamente le prestazioni. [Altre informazioni.](/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Supporto delle query Delta Microsoft Graph per le unità amministrative disponibili per l'anteprima pubblica

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Grafico MS

**Funzionalità del prodotto:** Per la versione di anteprima pubblica è disponibile la query differenziale esperienza sviluppatore per unità amministrative. È ora possibile tenere traccia delle modifiche senza dover eseguire continuamente il polling di Microsoft Graph. [Altre informazioni.](/graph/api/administrativeunit-delta?tabs=http&view=graph-rest-beta)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Gestisci i numeri di telefono per l'autenticazione e altro ancora nelle nuove API Microsoft Graph beta

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Grafico MS

**Funzionalità del prodotto:** esperienza di sviluppo

Queste API sono uno strumento chiave per la gestione dei metodi di autenticazione degli utenti. È ora possibile pre-registrare e gestire a livello di codice gli autenticatori usati per l'autenticazione a più fattori e la reimpostazione della password self-service (SSPR). Questa è una delle funzionalità più richieste negli spazi di autenticazione a più fattori di Azure, SSPR e Microsoft Graph. Le nuove API rilasciate in questa fase offrono la possibilità di:

- Leggere, aggiungere, aggiornare e rimuovere i telefoni di autenticazione di un utente
- Reimpostare la password di un utente
- Attivare e disattivare SMS-Sign-in

Per ulteriori informazioni, vedere [Cenni preliminari sull'API dei metodi di autenticazione Azure ad](/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Anteprima pubblica unità amministrative

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Ruoli di Azure AD

**Funzionalità del prodotto:** Controllo di accesso

Le unità amministrative consentono di concedere autorizzazioni di amministratore limitate a un reparto, un'area o un altro segmento definito dell'organizzazione. È possibile usare le unità amministrative per delegare le autorizzazioni agli amministratori regionali o per impostare criteri a livello dettagliato. Ad esempio, un amministratore di account utente potrebbe aggiornare le informazioni sul profilo, reimpostare le password e assegnare le licenze agli utenti solo nella propria unità amministrativa.

Usando le unità amministrative, un amministratore centrale può:

- Creare un'unità amministrativa per la gestione decentralizzata delle risorse
- Assegnare un ruolo con autorizzazioni amministrative solo Azure AD utenti in un'unità amministrativa
- Popola le unità amministrative con utenti e gruppi in base alle esigenze

Per ulteriori informazioni, vedere [gestione delle unità amministrative in Azure Active Directory (anteprima)](https://aka.ms/AdminUnitsDocs).

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>Ruoli predefiniti dell'amministratore della stampante e del tecnico della stampante

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Ruoli di Azure AD

**Funzionalità del prodotto:** Controllo di accesso

**Amministratore della stampante**: gli utenti con questo ruolo possono registrare le stampanti e gestire tutti gli aspetti di tutte le configurazioni della stampante nella soluzione di stampa universale Microsoft, incluse le impostazioni del connettore di stampa universale. Possono concedere il consenso a tutte le richieste di autorizzazione di stampa delegate. Gli amministratori stampante possono anche accedere ai report di stampa. 

**Tecnico della stampante**: gli utenti con questo ruolo possono registrare le stampanti e gestire lo stato della stampante nella soluzione di stampa universale Microsoft. Possono anche leggere tutte le informazioni sul connettore. Le attività principali che un tecnico di stampa non può eseguire sono impostare le autorizzazioni utente sulle stampanti e condividere stampanti. [Altre informazioni.](../users-groups-roles/directory-assign-admin-roles.md#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Ruolo predefinito amministratore identità ibrida

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Ruoli di Azure AD

**Funzionalità del prodotto:** Controllo di accesso

Gli utenti con questo ruolo possono abilitare, configurare e gestire i servizi e le impostazioni relative all'abilitazione dell'identità ibrida in Azure AD. Questo ruolo concede la possibilità di configurare Azure AD a uno dei tre metodi di autenticazione supportati&#8212;la sincronizzazione dell'hash delle password (pH), l'autenticazione pass-through (PTA) o la Federazione (AD FS o provider federativo di terze parti) &#8212;e di distribuire l'infrastruttura locale correlata per abilitarli. L'infrastruttura locale include il provisioning e gli agenti PTA. Questo ruolo concede la possibilità di abilitare l'accesso Single Sign-On facile (S-SSO) per consentire l'autenticazione semplice nei dispositivi non Windows 10 o nei computer non Windows Server 2016. Inoltre, questo ruolo concede la possibilità di visualizzare i log di accesso e di accedere a integrità e analisi per il monitoraggio e la risoluzione dei problemi. [Altre informazioni.](../users-groups-roles/directory-assign-admin-roles.md#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Ruolo predefinito amministratore di rete

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Ruoli di Azure AD

**Funzionalità del prodotto:** Controllo di accesso

Gli utenti con questo ruolo possono esaminare le raccomandazioni relative all'architettura perimetrale di rete di Microsoft basate sui dati di telemetria di rete delle proprie sedi utente. Prestazioni di rete per Microsoft 365 si basa su un'attenta architettura perimetrale della rete del cliente aziendale, che in genere è specifica della posizione utente. Questo ruolo consente di modificare le posizioni utente individuate e la configurazione dei parametri di rete per tali posizioni per ottenere misurazioni di telemetria e raccomandazioni di progettazione migliori. [Altre informazioni.](../users-groups-roles/directory-assign-admin-roles.md#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Attività e download bulk nell'esperienza del portale di amministrazione di Azure AD

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Gestione utenti

**Funzionalità del prodotto:** Directory

È ora possibile eseguire attività bulk su utenti e gruppi in Azure AD caricando un file CSV nell'esperienza Azure AD portale di amministrazione. È possibile creare utenti, eliminare utenti e invitare utenti guest. È possibile aggiungere e rimuovere membri da un gruppo.

È anche possibile scaricare elenchi di risorse Azure AD dall'esperienza Azure AD portale di amministrazione. È possibile scaricare l'elenco di utenti nella directory, l'elenco dei gruppi nella directory e i membri di un gruppo specifico.

Per ulteriori informazioni, vedere gli argomenti seguenti:

- [Creare utenti](../users-groups-roles/users-bulk-add.md) o [invitare utenti Guest](../external-identities/tutorial-bulk-invite.md)
- [Eliminare utenti](../users-groups-roles/users-bulk-delete.md) o [ripristinare utenti eliminati](../users-groups-roles/users-bulk-restore.md)
- [Scarica l'elenco di utenti](../users-groups-roles/users-bulk-download.md) o [Scarica l'elenco di gruppi](../users-groups-roles/groups-bulk-download.md)
- [Aggiungere (importare) membri](../users-groups-roles/groups-bulk-import-members.md) o [rimuovere membri](../users-groups-roles/groups-bulk-remove-members.md) o [scaricare un elenco di membri](../users-groups-roles/groups-bulk-download-members.md) per un gruppo

---

### <a name="my-staff-delegated-user-management"></a>Gestione utenti delegati personale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Gestione utenti

**Funzionalità del prodotto:**

Il personale Abilita i Manager prima riga, ad esempio un responsabile del negozio, per garantire che i membri del personale siano in grado di accedere ai propri account Azure AD. Anziché affidarsi a un helpdesk centrale, le organizzazioni possono delegare attività comuni, come la reimpostazione delle password o la modifica dei numeri di telefono, a un prima riga Manager. Con il personale, un utente che non può accedere al proprio account può ottenere di nuovo l'accesso con pochi clic, senza richiedere supporto tecnico o personale IT. Per altre informazioni, vedere [gestire gli utenti con personale (anteprima)](https://aka.ms/MyStaffAdminDocs) e [delegare la gestione degli utenti con il personale (anteprima)](https://aka.ms/MyStaffUserDocs).

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>Esperienza dell'utente finale aggiornata nelle verifiche di accesso

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Verifiche di accesso

**Funzionalità del prodotto:** Governance delle identità

È stata aggiornata l'esperienza revisore per Azure AD le verifiche di accesso nel portale app personali. Alla fine di aprile, i revisori che hanno eseguito l'accesso al Azure AD esperienza di revisore delle verifiche di accesso visualizzeranno un banner che consentirà loro di provare l'esperienza aggiornata nell'accesso. Si noti che l'esperienza di verifica dell'accesso aggiornata offre le stesse funzionalità dell'esperienza attuale, ma con un'interfaccia utente migliorata sulle nuove funzionalità per consentire agli utenti di essere produttivi. [Altre informazioni sull'esperienza aggiornata sono disponibili qui](../governance/perform-access-review.md). Questa anteprima pubblica durerà fino alla fine del 2020 luglio. Alla fine di luglio, i revisori che non hanno optato per l'esperienza di anteprima verranno automaticamente indirizzati al mio accesso per eseguire le verifiche di accesso. Se si vuole che i revisori passino in modo permanente all'esperienza di anteprima ora in accesso personale, [effettuare una richiesta qui](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Il provisioning utenti in ingresso e le app writeback del giorno lavorativo supportano ora le versioni più recenti dell'API dei servizi Web della giornata lavorativa

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Provisioning di app

**Funzionalità del prodotto:** 

In base ai commenti e suggerimenti dei clienti, è stata aggiornata la giornata lavorativa per il provisioning utenti in ingresso e le app writeback nella raccolta di app aziendali per supportare le versioni più recenti dell'API dei servizi Web per la giornata lavorativa (WWS). Con questa modifica, i clienti possono specificare la versione dell'API WWS che si desidera utilizzare nella stringa di connessione. Ciò offre ai clienti la possibilità di recuperare più attributi HR disponibili nelle versioni della giornata lavorativa. L'app writeback dei giorni lavorativi USA ora il servizio Web Change_Work_Contact_Info giorni lavorativi consigliato per superare le limitazioni di Maintain_Contact_Info.

Se nella stringa di connessione non è specificata alcuna versione, per impostazione predefinita, le app di provisioning in ingresso per la giornata lavorativa continueranno a usare WWS v 21.1 per passare alle API della giornata lavorativa più recente per il provisioning degli utenti in ingresso, i clienti devono aggiornare la stringa di connessione come descritto [nell'esercitazione](../saas-apps/workday-inbound-tutorial.md#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) e aggiornare anche gli XPath usati per gli attributi [di giornata](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30)lavorativa 

Per usare la nuova API per il writeback, non sono necessarie modifiche nell'app di provisioning del writeback dei giorni lavorativi. Sul lato della giornata lavorativa, verificare che l'account dell'utente del sistema di integrazione lavorativa (ISU) disponga delle autorizzazioni per richiamare il processo di business Change_Work_Contact come illustrato nella sezione esercitazione [configurare le autorizzazioni dei criteri di sicurezza del processo di business](../saas-apps/workday-inbound-tutorial.md#configuring-business-process-security-policy-permissions). 

La [Guida dell'esercitazione](../saas-apps/workday-inbound-tutorial.md) è stata aggiornata in modo da riflettere il supporto della nuova versione dell'API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Gli utenti con ruolo di accesso predefinito sono ora inclusi nell'ambito per il provisioning

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Provisioning di app

**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

In passato, gli utenti con il ruolo di accesso predefinito non rientrano nell'ambito del provisioning. Il feedback dei clienti desidera che gli utenti con questo ruolo siano nell'ambito del provisioning. A partire dal 16 aprile 2020, tutte le nuove configurazioni di provisioning consentono di effettuare il provisioning degli utenti con il ruolo di accesso predefinito. Gradualmente si modificherà il comportamento per le configurazioni di provisioning esistenti per supportare il provisioning degli utenti con questo ruolo. [Altre informazioni.](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md)

---

### <a name="updated-provisioning-ui"></a>Interfaccia utente di provisioning aggiornata

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Provisioning di app

**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

L'esperienza di provisioning è stata aggiornata per creare una vista di gestione più mirata. Quando si passa al pannello di provisioning per un'applicazione aziendale che è già stata configurata, sarà possibile monitorare facilmente lo stato di avanzamento del provisioning e gestire le azioni, ad esempio l'avvio, l'arresto e il riavvio del provisioning. [Altre informazioni.](../app-provisioning/configure-automatic-user-provisioning-portal.md)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>La convalida della regola di gruppo dinamica è ora disponibile per l'anteprima pubblica

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Gestione gruppi

**Funzionalità del prodotto:** Collaborazione

Azure Active Directory (Azure AD) ora fornisce i mezzi per convalidare le regole dinamiche del gruppo. Nella scheda **Convalida regole** è possibile convalidare la regola dinamica rispetto ai membri del gruppo di esempio per confermare che la regola funziona come previsto. Quando si creano o si aggiornano le regole dinamiche del gruppo, gli amministratori desiderano sapere se un utente o un dispositivo sarà membro del gruppo. Questo consente di valutare se un utente o un dispositivo soddisfano i criteri di regola e gli aiuti per la risoluzione dei problemi quando l'appartenenza non è prevista.

Per altre informazioni, vedere [convalida di una regola di appartenenza a un gruppo dinamico (anteprima)](../users-groups-roles/groups-dynamic-rule-validation.md).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Punteggio sicuro identità-impostazioni predefinite di sicurezza e aggiornamenti dell'azione di miglioramento dell'autenticazione a più fattori

**Tipo:** Funzionalità modificata

**Categoria di servizio:** N/D

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

**Supporto delle impostazioni predefinite di sicurezza per le azioni di miglioramento Azure ad:** Microsoft Secure Score aggiornerà le azioni di miglioramento per supportare i [valori predefiniti di sicurezza in Azure ad](./concept-fundamentals-security-defaults.md), che semplificano la protezione dell'organizzazione con impostazioni di sicurezza preconfigurate per gli attacchi comuni. Questo influirà sulle azioni di miglioramento seguenti:

- Assicurarsi che tutti gli utenti siano in grado di completare l'autenticazione a più fattori per l'accesso sicuro
- Richiedi autenticazione a più fattori per ruoli amministrativi
- Abilitare i criteri per bloccare l'autenticazione legacy
 
**Aggiornamenti dell'azione di miglioramento** dell'autenticazione a più fattori: Per riflettere la necessità per le aziende di garantire la massima sicurezza durante l'applicazione di criteri che interagiscono con la propria azienda, Microsoft Secure Score ha rimosso tre azioni di miglioramento incentrate su autenticazione a più fattori e ne ha aggiunto due.

Azioni di miglioramento rimosse:

- Registrare tutti gli utenti per l'autenticazione a più fattori
- Richiedere l'autenticazione a più fattori per tutti gli utenti
- Richiedi autenticazione a più fattori per Azure AD ruoli con privilegi

Aggiunta di azioni di miglioramento:

- Assicurarsi che tutti gli utenti siano in grado di completare l'autenticazione a più fattori per l'accesso sicuro
- Richiedi autenticazione a più fattori per ruoli amministrativi

Queste nuove azioni di miglioramento richiedono la registrazione degli utenti o degli amministratori per l'autenticazione a più fattori (multi-factor authentication) nella directory e la definizione del giusto set di criteri che soddisfano le esigenze dell'organizzazione. L'obiettivo principale è quello di garantire flessibilità garantendo che tutti gli utenti e gli amministratori possano eseguire l'autenticazione con più fattori o richieste di verifica dell'identità basate sul rischio. Questo può assumere la forma di avere più criteri che applicano le decisioni con ambito o di impostare le impostazioni predefinite di sicurezza (a partire dal 16 marzo) che consentono a Microsoft di decidere quando sfidare gli utenti per l'autenticazione a più fattori. Scopri di [più sulle novità di Microsoft Secure Score](/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---
