---
title: Novità Note sulla versione - Azure Active Directory | Microsoft Docs
description: Informazioni sulle novità di Azure Active Directory, tra cui le note sulla versione più aggiornate, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti.
services: active-directory
author: msaburnley
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 30c6be98eeca4433798ad8ed2bacb9338331b112
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170446"
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory

>Consente di ricevere notifiche su quando rivisitare la pagina per gli aggiornamenti copiando e incollando questo URL: `https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us` nel ![ lettore di feed dell'icona del lettore feed RSS ](./media/whats-new/feed-icon-16x16.png) .

Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente. Se si cercano informazioni precedenti agli ultimi sei mesi, è possibile trovarli in [Archivio per le novità in Azure Active Directory](whats-new-archive.md).

---

## <a name="june-2020"></a>Giugno 2020 

### <a name="user-risk-condition-in-conditional-access-policy"></a>Condizione di rischio utente nei criteri di accesso condizionale

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 

Il supporto dei rischi per gli utenti in Azure AD i criteri di accesso condizionale consente di creare più criteri basati sul rischio utente. Per utenti e app diversi possono essere necessari livelli di rischio utente minimi diversi. In base al rischio utente, è possibile creare criteri per bloccare l'accesso, richiedere l'autenticazione a più fattori, modificare la password sicura o reindirizzare a Microsoft Cloud App Security per applicare i criteri di sessione, ad esempio il controllo aggiuntivo.

La condizione di rischio utente richiede Azure AD Premium P2 perché usa Azure Identity Protection, un'offerta P2. per altre informazioni sull'accesso condizionale, vedere [Azure ad documentazione sull'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/).

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO supporta ora le app che richiedono l'impostazione di SPNameQualifier quando richiesto

**Tipo:** Correzione  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
Per alcune applicazioni SAML è necessario che SPNameQualifier venga restituito nell'oggetto dell'asserzione quando richiesto. A questo punto Azure AD risponde correttamente quando viene richiesto un SPNameQualifier nel criterio NameID della richiesta. Questa operazione funziona anche per l'accesso avviato da SP e l'accesso avviato da IdP verrà eseguito.  Per ulteriori informazioni sul protocollo SAML in Azure Active Directory, vedere [il protocollo SAML per Single Sign-on](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol).

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD collaborazione B2B supporta l'invito di utenti MSA e Google in tenant di Azure per enti pubblici

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 

I tenant di Azure per enti pubblici che usano le funzionalità di collaborazione B2B possono ora invitare gli utenti con un account Microsoft o Google. Per sapere se il tenant può usare queste funzionalità, seguire le istruzioni in [come è possibile stabilire se la collaborazione B2B è disponibile nel tenant di Azure US Government?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>L'oggetto utente in MS Graph V1 include ora le proprietà externalUserState e externalUserStateChangedDateTime

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 

Le proprietà externalUserState e externalUserStateChangedDateTime possono essere usate per trovare i guest B2B invitati che non hanno accettato gli inviti ma anche l'automazione della compilazione, ad esempio l'eliminazione di utenti che non hanno accettato gli inviti dopo un certo numero di giorni. Queste proprietà sono ora disponibili in MS Graph V1. Per informazioni sull'uso di queste proprietà, vedere [tipo di risorsa utente](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0).
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>Gestire le sessioni di autenticazione in Azure AD accesso condizionale è ora disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Le funzionalità di gestione delle sessioni di autenticazione consentono di configurare la frequenza con cui gli utenti devono fornire le credenziali di accesso e se devono fornire le credenziali dopo aver chiuso e riaperto i browser per offrire maggiore sicurezza e flessibilità nell'ambiente in uso.
 
Inoltre, la gestione delle sessioni di autenticazione usata per l'autenticazione del primo fattore si applica solo ai dispositivi Azure AD aggiunti, Azure AD ibrido Uniti e Azure AD registrati. La gestione della sessione di autenticazione verrà ora applicata anche a multi-factor authentication. Per altre informazioni, vedere [configurare la gestione delle sessioni di autenticazione con l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime).

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2020 giugno

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel giugno 2020 abbiamo aggiunto le 29 nuove applicazioni seguenti nella raccolta di app con supporto federativo:

[Shopify Plus](https://docs.microsoft.com/azure/active-directory/saas-apps/shopify-plus-tutorial), [Ekarda](https://docs.microsoft.com/azure/active-directory/saas-apps/ekarda-tutorial), [MailGates](https://docs.microsoft.com/azure/active-directory/saas-apps/mailgates-tutorial), [BullseyeTDP](https://docs.microsoft.com/azure/active-directory/saas-apps/bullseyetdp-tutorial), [Raketa](https://docs.microsoft.com/azure/active-directory/saas-apps/raketa-tutorial), [Segment](https://docs.microsoft.com/azure/active-directory/saas-apps/segment-tutorial), [ai auditor](https://www.mindbridge.ai/products/ai-auditor/), [Pobuca Connect](https://app.pobu.ca/), [proto.io](https://docs.microsoft.com/azure/active-directory/saas-apps/proto.io-tutorial), [Gatekeeper](https://www.gatekeeperhq.com/), [Hub Planner](https://docs.microsoft.com/azure/active-directory/saas-apps/hub-planner-tutorial), [ansira-partner go-to-Market casella degli strumenti](https://ansira.com/technology/channel-engagement), [IBM Digital Business Automation su cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/ibm-digital-business-automation-on-cloud-tutorial), [Kisi Physical Security](https://docs.microsoft.com/azure/active-directory/saas-apps/kisi-physical-security-tutorial), [ViewpointOne](https://team.viewpoint.com/), [IntelligenceBank](https://docs.microsoft.com/azure/active-directory/saas-apps/intelligencebank-tutorial), [pymetrics](https://docs.microsoft.com/azure/active-directory/saas-apps/pymetrics-tutorial), [zero](https://www.teamzero.com/), [Instation](https://instation.invillia.com/), [EDX for business SAML 2,0 Integration](https://docs.microsoft.com/azure/active-directory/saas-apps/edx-for-business-saml-integration-tutorial), [MOOC Office 365](https://mooc.office365-training.com/en/), [SmartKargo](https://docs.microsoft.com/azure/active-directory/saas-apps/smartkargo-tutorial), [PKIsigning Platform](https://platform.pkisigning.nl/), [SiteIntel](https://docs.microsoft.com/azure/active-directory/saas-apps/siteintel-tutorial), [Field ID](https://docs.microsoft.com/azure/active-directory/saas-apps/field-id-tutorial), [curriculas SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/curricula-saml-tutorial), [Perforce Core-Helix Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/perforce-helix-core-tutorial), [concompliance cloud](https://cloud.metacompliance.com/), [Smallstep](https://smallstep.com/sso-ssh/)  

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

Per altre informazioni su tutte le esperienze possibili con i connettori API, vedere [usare i connettori API per personalizzare ed estendere l'iscrizione self-service](https://docs.microsoft.com/azure/active-directory/b2b/api-connectors-overview)o [personalizzare le identità esterne self-service iscrizione con le integrazioni dell'API Web](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin).
 
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
 
Una nuova autorizzazione delegata EntitlementManagement. Read. All è ora disponibile per l'uso con l'API di gestione dei diritti in Microsoft Graph beta. Per altre informazioni sulle API disponibili, vedere [uso dell'API di gestione dei diritti Azure ad](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta).

---

### <a name="identity-protection-apis-available-in-v10"></a>API di Identity Protection disponibili nella versione 1.0

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Le API Microsoft Graph riskyUsers e riskDetections sono ora disponibili a livello generale. Ora che sono disponibili nell'endpoint v 1.0, viene invitato a usarli nell'ambiente di produzione. Per ulteriori informazioni, consultare la documentazione di [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0).
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>Le etichette di riservatezza per applicare i criteri ai gruppi di Microsoft 365 sono ora disponibili a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione
 

È ora possibile creare etichette di riservatezza e usare le impostazioni delle etichette per applicare i criteri a gruppi di Microsoft 365, inclusi i criteri di accesso utente esterno (pubblico o privato). È possibile creare un'etichetta con l'informativa sulla privacy come privata e i criteri di accesso utente esterno per non consentire l'aggiunta di utenti guest. Quando un utente applica questa etichetta a un gruppo, il gruppo sarà privato e nessun utente Guest potrà essere aggiunto al gruppo. 

Le etichette di riservatezza sono importanti per proteggere i dati cruciali per l'azienda e consentono di gestire i gruppi su larga scala, in modo conforme e sicuro. Per informazioni sull'uso delle etichette di riservatezza, vedere [assegnare etichette di riservatezza ai gruppi di Office 365 in Azure Active Directory (anteprima)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels).
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>Aggiornamenti per il supporto di Microsoft Identity Manager per clienti Azure AD Premium

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Microsoft Identity Manager  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il supporto di Azure è ora disponibile per i componenti di integrazione Azure AD di Microsoft Identity Manager 2016, fino alla fine del supporto esteso per Microsoft Identity Manager 2016. Per altre informazioni, vedere l' [aggiornamento del supporto per Azure ad Premium clienti che usano Microsoft Identity Manager](https://docs.microsoft.com/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers).

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>L'uso di condizioni di appartenenza a gruppi nella configurazione delle attestazioni SSO è aumentato

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
In precedenza, il numero di gruppi che è possibile usare quando si modificano le attestazioni in modo condizionale in base all'appartenenza al gruppo all'interno di una singola configurazione di applicazione era limitato a 10. L'uso di condizioni di appartenenza a gruppi nella configurazione delle attestazioni SSO è ora aumentato fino a un massimo di 50 gruppi. Per ulteriori informazioni sulla configurazione delle attestazioni, vedere la pagina relativa alla [configurazione delle attestazioni SSO delle applicazioni aziendali](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#emitting-claims-based-on-conditions). 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>Abilitazione della formattazione di base sul componente testo della pagina di accesso in informazioni personalizzate distintive dell'azienda.

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
La funzionalità di personalizzazione dell'azienda nell'esperienza di accesso Azure AD/Microsoft 365 è stata aggiornata per consentire al cliente di aggiungere collegamenti ipertestuali e formattazione semplice, inclusi il tipo di carattere, la sottolineatura e il corsivo in grassetto. Per istruzioni sull'uso di questa funzionalità, vedere [aggiungere informazioni personalizzate alla pagina di accesso Azure Active Directory dell'organizzazione](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="provisioning-performance-improvements"></a>Miglioramenti delle prestazioni del provisioning

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il servizio di provisioning è stato aggiornato per ridurre il tempo necessario per il completamento di un [ciclo incrementale](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles) . Questo significa che gli utenti e i gruppi verranno sottoposti a provisioning nelle applicazioni più velocemente rispetto a quanto precedentemente fatto. Tutti i nuovi processi di provisioning creati dopo 6/10/2020 trarranno automaticamente vantaggio dai miglioramenti delle prestazioni. Tutte le applicazioni configurate per il provisioning prima del 6/10/2020 dovranno essere riavviate una volta dopo 6/10/2020 per sfruttare i miglioramenti delle prestazioni. 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>Annunciare la deprecazione di ADAL e MS Graph parità

**Tipo:** Deprecato  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi

Ora che sono disponibili le librerie di autenticazione Microsoft (MSAL), non verranno più aggiunte nuove funzionalità alle librerie di autenticazione Azure Active Directory (ADAL) e le patch di sicurezza verranno terminate il 30 giugno 2022. Per ulteriori informazioni su come eseguire la migrazione a MSAL, vedere [eseguire la migrazione di applicazioni a Microsoft Authentication Library (MSAL)](https://docs.microsoft.com/azure/active-directory/develop/msal-migration).

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
 

Sono state apportate le modifiche seguenti alle notifiche di posta elettronica per l'autenticazione a più fattori:

Le notifiche tramite posta elettronica verranno inviate dall'indirizzo seguente: azure-noreply@microsoft.com e msonlineservicesteam@microsoftonline.com . Stiamo aggiornando il contenuto dei messaggi di posta elettronica di avviso di illecito per indicare meglio i passaggi necessari per sbloccare gli usi.

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>Nuova iscrizione self-service per gli utenti di domini federati che non possono accedere ai team Microsoft perché non sono sincronizzati con Azure Active Directory.

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 

Attualmente, gli utenti che si trovano in domini federati in Azure AD, ma che non sono sincronizzati nel tenant, non possono accedere ai team. A partire dalla fine di giugno, questa nuova funzionalità consentirà di eseguire questa operazione estendendo la funzionalità di iscrizione verificata tramite posta elettronica esistente. Ciò consentirà agli utenti che possono accedere a un IdP federato, ma che non dispongono ancora di un oggetto utente in Azure ID, di creare automaticamente un oggetto utente e di essere autenticato per i team. Il relativo oggetto utente sarà contrassegnato come "iscrizione self-service". Si tratta di un'estensione della funzionalità esistente per eseguire l'iscrizione automatica verificata tramite posta elettronica che gli utenti nei domini gestiti possono eseguire e possono essere controllati con lo stesso flag. Questa modifica verrà completata durante i due mesi seguenti. Per informazioni sugli aggiornamenti della documentazione, vedere [qui](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup).
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>Correzione imminente: è in corso l'aggiornamento del documento di individuazione OIDC per il cloud di Azure per enti pubblici in modo che faccia riferimento agli endpoint grafici corretti.

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Cloud sovrani  
**Funzionalità del prodotto:** Autenticazione utente
 
A partire da giugno, il documento di individuazione OIDC [Microsoft Identity Platform e il protocollo OpenID Connect](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) nell'endpoint [cloud di Azure per enti pubblici](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud) (login.microsoftonline.US), inizieranno a restituire l'endpoint di [cloud Graph nazionale](https://docs.microsoft.com/graph/deployments) corretto ( https://graph.microsoft.us o https://dod-graph.microsoft.us) , in base al tenant fornito.  Attualmente fornisce il campo "msgraph_host" dell'endpoint grafico errato (graph.microsoft.com).  

Questa correzione di bug verrà implementata gradualmente per circa 2 mesi.  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Gli utenti di Azure per enti pubblici non saranno più in grado di accedere a login.microsoftonline.com

**Tipo:** Pianificare la modifica  
**Categoria di servizio:** Cloud sovrani  
**Funzionalità del prodotto:** Autenticazione utente
 
Il 1 giugno 2018, l'autorità ufficiale Azure Active Directory (AAD) per Azure Government è cambiata da https://login-us.microsoftonline.com a https://login.microsoftonline.us . Se si è proprietari di un'applicazione in un tenant di Azure per enti pubblici, è necessario aggiornare l'applicazione per consentire agli utenti di accedere all'endpoint. US.

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
 
Gli amministratori IT possono iniziare a usare il nuovo ruolo "amministratore ibrido" come ruolo con privilegi minimi per la configurazione del provisioning cloud ADConnect di Azure. Con questo nuovo ruolo, non è più necessario usare il ruolo di amministratore globale per configurare e configurare il provisioning del cloud. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-by-task#connect)
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Nuove app federate disponibili nella raccolta di applicazioni Azure AD-2020 maggio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel 2020 maggio sono state aggiunte le seguenti 36 nuove applicazioni nella raccolta di app con supporto federativo:

[Moula](https://moula.com.au/pay/merchants), [Surveypal](https://www.surveypal.com/app), [Kbot365](https://www.konverso.ai/virtual-assistant-for-digital-workplace/), [TackleBox](http://www.tacklebox.app/), [Powell teams](https://powell-software.com/en/powell-teams-en/), [Talentsoft Assistant](https://msteams.talent-soft.com/), [ASC Recording Insights](https://teams.asc-recording.app/product), [GO1](https://www.go1.com/), [B-Engaged](https://b-engaged.se/), [Competella Contact Center Workgroup](http://www.competella.com/), [Asite](http://www.asite.com/), [ImageSoft Identity](https://identity.imagesoftinc.com/), [My IBISWorld](https://identity.imagesoftinc.com/), [insuite](https://docs.microsoft.com/azure/active-directory/saas-apps/insuite-tutorial), [Change Process Management](https://docs.microsoft.com/azure/active-directory/saas-apps/change-process-management-tutorial), [Cyara CX Assurance Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/cyara-cx-assurance-platform-tutorial), [Smart Global Governance](https://docs.microsoft.com/azure/active-directory/saas-apps/smart-global-governance-tutorial) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/vyond-tutorial) [prezi](https://docs.microsoft.com/azure/active-directory/saas-apps/prezi-tutorial), [MapBox](https://docs.microsoft.com/azure/active-directory/saas-apps/mapbox-tutorial), [datava Enterprise Service Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/datava-enterprise-service-platform-tutorial), [Stravagance](https://docs.microsoft.com/azure/active-directory/saas-apps/whimsical-tutorial), [Trelica](https://docs.microsoft.com/azure/active-directory/saas-apps/trelica-tutorial), [EasySSO for confluenza](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-confluence-tutorial), [EasySSO for bitbucket](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bitbucket-tutorial), EasySSO [for Bamboo](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bamboo-tutorial), [Torii](https://docs.microsoft.com/azure/active-directory/saas-apps/torii-tutorial), [Axiad cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/axiad-cloud-tutorial), [humany](https://docs.microsoft.com/azure/active-directory/saas-apps/humanage-tutorial), [ColorTokens ZTNA](https://docs.microsoft.com/azure/active-directory/saas-apps/colortokens-ztna-tutorial) [, Tagetik, ShareVault](https://docs.microsoft.com/azure/active-directory/saas-apps/cch-tagetik-tutorial) [, Vyond](https://docs.microsoft.com/azure/active-directory/saas-apps/sharevault-tutorial)di [lavoro, all](https://docs.microsoft.com/azure/active-directory/saas-apps/textexpander-tutorial) [Home CRM](https://docs.microsoft.com/azure/active-directory/saas-apps/anyone-home-crm-tutorial), [TextExpander](https://docs.microsoft.com/azure/active-directory/saas-apps/askspoke-tutorial), [Ice Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/ice-contact-center-tutorial)

È anche possibile trovare la documentazione di tutte le applicazioni da qui https://aka.ms/AppsTutorial .

Per elencare l'applicazione nella raccolta di app Azure AD, vedere qui i dettagli https://aka.ms/AzureADAppRequest .

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>La modalità di sola segnalazione per l'accesso condizionale è ora disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

La [modalità di sola segnalazione per Azure ad accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) consente di valutare il risultato di un criterio senza applicare i controlli di accesso. È possibile testare i criteri di solo report nell'organizzazione e comprenderne l'effetto prima di abilitarli, rendendo la distribuzione più sicura e semplice. Negli ultimi mesi, abbiamo visto l'adozione avanzata della modalità solo report: gli utenti di 26M sono già inclusi nell'ambito di un criterio di sola segnalazione. Con l'annuncio odierno, i nuovi criteri di accesso condizionale di Azure AD verranno creati in modalità solo report per impostazione predefinita. Ciò significa che è possibile monitorare l'effetto dei criteri dal momento in cui sono stati creati. E per coloro che usano le API Graph di Microsoft, è possibile gestire anche i [criteri di solo report a livello di codice](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta) . 

---

### <a name="self-service-sign-up-for-guest-users"></a>Iscrizione self-service per utenti Guest

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
Con le identità esterne in Azure AD, è possibile consentire agli utenti esterni all'organizzazione di accedere alle app e alle risorse, consentendo al contempo di accedere usando qualsiasi identità preferita. Durante la condivisione di un'applicazione con utenti esterni, è possibile sapere sempre in anticipo chi dovrà accedere all'applicazione. Con l' [iscrizione self-service](https://docs.microsoft.com/azure/active-directory/b2b/self-service-sign-up-overview), è possibile consentire agli utenti guest di iscriversi e ottenere un account Guest per le app line-of-business (LOB). Il flusso di iscrizione può essere creato e personalizzato per supportare Azure AD e le identità di social networking. È anche possibile raccogliere informazioni aggiuntive sull'utente durante l'iscrizione.

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Le informazioni dettagliate sull'accesso condizionale e la cartella di lavoro report sono disponibili a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

La [cartella di lavoro Insights e Reporting](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) offre agli amministratori una visualizzazione riepilogativa del Azure ad l'accesso condizionale nel tenant. Con la possibilità di selezionare un singolo criterio, gli amministratori possono comprendere meglio il funzionamento di ogni criterio e monitorare le modifiche in tempo reale. La cartella di lavoro trasmette i dati archiviati in monitoraggio di Azure, che è possibile configurare in pochi minuti [seguendo queste istruzioni](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Per rendere il dashboard più individuabile, è stato spostato nella nuova scheda Insights and Reporting nel menu di Azure AD accesso condizionale.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Il pannello dei dettagli dei criteri per l'accesso condizionale è in versione di anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Il pannello nuovi [Dettagli criteri](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) Visualizza le assegnazioni, le condizioni e i controlli soddisfatti durante la valutazione dei criteri di accesso condizionale. È possibile accedere al pannello selezionando una riga nelle schede accesso condizionale o solo report dei dettagli di accesso.

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
 
È ora disponibile il supporto per la creazione e la configurazione di un'applicazione dalla raccolta di Azure AD usando le API di MS Graph in beta. Se è necessario configurare Single Sign-On basati su SAML per più istanze di un'applicazione, risparmiare tempo usando le API Microsoft Graph per [automatizzare la configurazione dei Single Sign-on basati su SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/application-saml-sso-configure-api).
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2020 maggio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

* [8X8](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8-provisioning-tutorial)
* [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-provisioning-tutorial)
* [MediusFlow](https://docs.microsoft.com/azure/active-directory/saas-apps/mediusflow-provisioning-tutorial)
* [New Relic by Organization](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-by-organization-provisioning-tutorial)
* [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="saml-token-encryption-is-generally-available"></a>La crittografia del token SAML è disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
La [crittografia del token SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption) consente di configurare le applicazioni in modo da ricevere asserzioni SAML crittografate. La funzionalità è ora disponibile a livello generale in tutti i cloud.
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>Il nome del gruppo attestazioni nei token dell'applicazione è disponibile a livello generale

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
Le attestazioni di gruppo rilasciate in un token possono ora essere limitate solo ai gruppi assegnati all'applicazione.  Questo è particolarmente importante quando gli utenti sono membri di un numero elevato di gruppi e si è verificato un rischio di superamento dei limiti delle dimensioni dei token. Grazie a questa nuova funzionalità, la possibilità di [aggiungere nomi di gruppo ai token](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims) è disponibile a livello generale.
 
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
 
La verifica dell'editore (anteprima) consente agli amministratori e agli utenti finali di comprendere l'autenticità degli sviluppatori di applicazioni che si integrano con la piattaforma di identità Microsoft. Per informazioni dettagliate, vedere [Verifica dell'editore (anteprima)](https://docs.microsoft.com/azure/active-directory/develop/publisher-verification-overview).
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>Flusso del codice di autorizzazione per le app a singola pagina

**Tipo:** Categoria del **servizio** funzionalità modificata: **funzionalità del prodotto** di autenticazione: esperienza dello sviluppatore

A causa delle restrizioni dei cookie di terze parti del browser moderni, [ad esempio Safari ITP](https://docs.microsoft.com/azure/active-directory/develop/reference-third-party-cookies-spas), le Spa dovranno usare il flusso del codice di autorizzazione anziché il flusso implicito per mantenere SSO. MSAL.js v 2. x supporterà ora il flusso del codice di autorizzazione. Ci sono aggiornamenti corrispondenti per il portale di Azure, quindi è possibile aggiornare la SPA in modo che sia di tipo "Spa" e usare il flusso del codice di autenticazione. Per istruzioni, vedere [Guida introduttiva: accedere agli utenti e ottenere un token di accesso in una spa JavaScript usando il flusso del codice di autenticazione](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code).

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>Un filtro migliorato per i dispositivi è in versione di anteprima pubblica

**Tipo:** Funzionalità modificata   
**Categoria di servizio:** Funzionalità del **prodotto** gestione dei dispositivi: gestione del ciclo di vita dei dispositivi
 
In precedenza, gli unici filtri utilizzabili erano "Enabled" e "Activity date". È ora possibile [filtrare l'elenco dei dispositivi in altre proprietà](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#device-list-filtering-preview), tra cui tipo di sistema operativo, tipo di join, conformità e altro ancora. Queste aggiunte dovrebbero semplificare l'individuazione di un dispositivo specifico.

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

La valutazione dell'accesso continuo è una nuova funzionalità di sicurezza che consente di applicare in tempo quasi reale i criteri alle relying party che utilizzano Azure AD token di accesso quando si verificano eventi in Azure AD, ad esempio l'eliminazione di account utente. Questa funzionalità è stata implementata per prima per i team e i client Outlook. Per ulteriori informazioni, leggere il [Blog](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933) e la [documentazione](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation).

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>Accesso SMS: i prima riga Worker possono accedere alle applicazioni con supporto di Azure AD con il numero di telefono e nessuna password

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Autenticazioni (accessi)

**Funzionalità del prodotto:** Autenticazione utente

Office sta avviando una serie di app aziendali per dispositivi mobili che soddisfano le organizzazioni non tradizionali e i dipendenti di organizzazioni di grandi dimensioni che non usano la posta elettronica come metodo di comunicazione principale. Queste app sono destinate a dipendenti di Frontline, lavoratori senza scrivania, agenti di campo o dipendenti al dettaglio che potrebbero non ricevere un indirizzo di posta elettronica dal proprio datore di lavoro, avere accesso a un computer o a esso. Questo progetto consente ai dipendenti di accedere alle applicazioni aziendali immettendo un numero di telefono e Roundtripping un codice. Per altri dettagli, vedere la [documentazione amministrativa](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin) e la [documentazione per gli utenti finali](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer).

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>Invitare gli utenti interni a usare la collaborazione B2B

**Tipo:** Nuova funzionalità

**Categoria di servizio:** B2B

**Funzionalità del prodotto:**

Stiamo espandendo la funzionalità di invito B2B per consentire agli account interni esistenti di essere invitati a usare le credenziali di collaborazione B2B in futuro. Questa operazione viene eseguita passando l'oggetto utente all'API invite, oltre ai parametri tipici come l'indirizzo di posta elettronica invitato. L'ID oggetto, l'UPN, l'appartenenza al gruppo, l'assegnazione dell'app e così via sono intatti, ma in futuro utilizzeranno B2B per l'autenticazione con le credenziali del tenant principale, anziché le credenziali interne utilizzate prima dell'invito. Per informazioni dettagliate, vedere la [documentazione](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users)di.

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>La modalità di sola segnalazione per l'accesso condizionale è ora disponibile a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Accesso condizionale

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

La [modalità di sola segnalazione per Azure ad accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only) consente di valutare il risultato di un criterio senza applicare i controlli di accesso. È possibile testare i criteri di solo report nell'organizzazione e comprenderne l'effetto prima di abilitarli, rendendo la distribuzione più sicura e semplice. Negli ultimi mesi, abbiamo visto l'adozione avanzata della modalità solo report, con gli utenti di 26M già nell'ambito di un criterio di sola segnalazione. Con questo annuncio, i nuovi criteri di accesso condizionale di Azure AD verranno creati in modalità solo report per impostazione predefinita. Ciò significa che è possibile monitorare l'effetto dei criteri dal momento in cui sono stati creati. Per coloro che usano le API Graph di Microsoft, è inoltre possibile gestire i [criteri di solo report a livello di codice](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta). 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>Le informazioni dettagliate sull'accesso condizionale e la cartella di lavoro report sono disponibili a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Accesso condizionale

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

La cartella di [lavoro Insights e report](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting) di accesso condizionale offre agli amministratori una visualizzazione riepilogativa di Azure ad accesso condizionale nel tenant. Con la possibilità di selezionare un singolo criterio, gli amministratori possono comprendere meglio quali sono i criteri e monitorare le modifiche in tempo reale. La cartella di lavoro trasmette i dati archiviati in monitoraggio di Azure, che è possibile configurare in pochi minuti [seguendo queste istruzioni](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics). Per rendere il dashboard più individuabile, è stato spostato nella nuova scheda Insights and Reporting nel menu di Azure AD accesso condizionale.

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>Il pannello dei dettagli dei criteri per l'accesso condizionale è in versione di anteprima pubblica

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Accesso condizionale

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Il pannello nuovi [Dettagli criteri](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access) Visualizza le assegnazioni, le condizioni e i controlli soddisfatti durante la valutazione dei criteri di accesso condizionale. È possibile accedere al pannello selezionando una riga nelle schede **accesso condizionale** o **solo report** dei dettagli di accesso.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2020 aprile

**Tipo:** Nuova funzionalità

**Categoria di servizio:** App aziendali

**Funzionalità del prodotto:** Integrazione con app di terze parti

Nel 2020 aprile sono state aggiunte le 31 nuove app con supporto federativo per la raccolta di app: 

[App SincroPool](https://www.sincropool.com/), [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/), [float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial), [LMS365](https://lms.365.systems/), [IWT Procurement Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial), [Lunni](https://lunni.fi/), [EasySSO per JIRA](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial), [Virtual Training Academy](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft), [Meraki dashboard](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial), [Office 365 Mover](https://app.mover.io/login), [speaker Engage](https://speakerengage.com/login.php), [Honeste](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial), [Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial), [DutyFlow](https://app.dutyflow.nl/), [AlertMedia, gr8](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial) [people](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial), [Pending](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial), [HighGround](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial), [Harmony](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial), [orari Solutions](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial), consentirà di [fare clic su](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial), [Empower](https://www.made-in-office.com/en/), [forti change Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial), [tornasole](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial), [GroupTalk](https://recorder.grouptalk.com/), [Frontify](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial), [MongoDB cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial), [TickitLMS Learn](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial), [Coco](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/), [Nitro Productivity Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) , [Trend Micro Web Security](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>Supporto delle query Microsoft Graph Delta per oAuth2PermissionGrant disponibile per l'anteprima pubblica

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Grafico MS

**Funzionalità del prodotto:** esperienza di sviluppo

La query Delta per oAuth2PermissionGrant è disponibile per l'anteprima pubblica. È ora possibile tenere traccia delle modifiche senza dover eseguire continuamente il polling di Microsoft Graph. [Altre informazioni.](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>Supporto delle query Delta Microsoft Graph per il contatto aziendale disponibile a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Grafico MS

**Funzionalità del prodotto:** esperienza di sviluppo

La query Delta per i contatti dell'organizzazione è disponibile a livello generale. È ora possibile tenere traccia delle modifiche nelle app di produzione senza dover eseguire continuamente il polling Microsoft Graph. Sostituire il codice esistente che esegue continuamente il polling dei dati di orgContact per query Delta per migliorare significativamente le prestazioni. [Altre informazioni.](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>Supporto delle query Delta Microsoft Graph per l'applicazione disponibile a livello generale

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Grafico MS

**Funzionalità del prodotto:** esperienza di sviluppo

La query Delta per le applicazioni è disponibile a livello generale. È ora possibile tenere traccia delle modifiche nelle app di produzione senza dover eseguire continuamente il polling Microsoft Graph. Sostituire il codice esistente che esegue continuamente il polling dei dati delle applicazioni tramite una query Delta per migliorare significativamente le prestazioni. [Altre informazioni.](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>Supporto delle query Delta Microsoft Graph per le unità amministrative disponibili per l'anteprima pubblica

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Grafico MS

**Funzionalità del prodotto:** Per la versione di anteprima pubblica è disponibile la query differenziale esperienza sviluppatore per unità amministrative. È ora possibile tenere traccia delle modifiche senza dover eseguire continuamente il polling di Microsoft Graph. [Altre informazioni.](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>Gestisci i numeri di telefono per l'autenticazione e altro ancora nelle nuove API Microsoft Graph beta

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Grafico MS

**Funzionalità del prodotto:** esperienza di sviluppo

Queste API sono uno strumento chiave per la gestione dei metodi di autenticazione degli utenti. È ora possibile pre-registrare e gestire a livello di codice gli autenticatori usati per l'autenticazione a più fattori e la reimpostazione della password self-service (SSPR). Questa è una delle funzionalità più richieste negli spazi di autenticazione a più fattori di Azure, SSPR e Microsoft Graph. Le nuove API rilasciate in questa fase offrono la possibilità di:

- Leggere, aggiungere, aggiornare e rimuovere i telefoni di autenticazione di un utente
- Reimpostare la password di un utente
- Attivare e disattivare SMS-Sign-in

Per ulteriori informazioni, vedere [Cenni preliminari sull'API dei metodi di autenticazione Azure ad](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta).

---

### <a name="administrative-units-public-preview"></a>Anteprima pubblica unità amministrative

**Tipo:** Nuova funzionalità

**Categoria di servizio:** RBAC

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

**Categoria di servizio:** RBAC

**Funzionalità del prodotto:** Controllo di accesso

**Amministratore della stampante**: gli utenti con questo ruolo possono registrare le stampanti e gestire tutti gli aspetti di tutte le configurazioni della stampante nella soluzione di stampa universale Microsoft, incluse le impostazioni del connettore di stampa universale. Possono concedere il consenso a tutte le richieste di autorizzazione di stampa delegate. Gli amministratori stampante possono anche accedere ai report di stampa. 

**Tecnico della stampante**: gli utenti con questo ruolo possono registrare le stampanti e gestire lo stato della stampante nella soluzione di stampa universale Microsoft. Possono anche leggere tutte le informazioni sul connettore. Le attività principali che un tecnico di stampa non può eseguire sono impostare le autorizzazioni utente sulle stampanti e condividere stampanti. [Altre informazioni.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Ruolo predefinito amministratore identità ibrida

**Tipo:** Nuova funzionalità

**Categoria di servizio:** RBAC

**Funzionalità del prodotto:** Controllo di accesso

Gli utenti con questo ruolo possono abilitare, configurare e gestire i servizi e le impostazioni relative all'abilitazione dell'identità ibrida in Azure AD. Questo ruolo concede la possibilità di configurare Azure AD a uno dei tre metodi di autenticazione supportati&#8212;la sincronizzazione dell'hash delle password (pH), l'autenticazione pass-through (PTA) o la Federazione (AD FS o provider federativo di terze parti) &#8212;e di distribuire l'infrastruttura locale correlata per abilitarli. L'infrastruttura locale include il provisioning e gli agenti PTA. Questo ruolo concede la possibilità di abilitare l'accesso Single Sign-On facile (S-SSO) per consentire l'autenticazione semplice nei dispositivi non Windows 10 o nei computer non Windows Server 2016. Inoltre, questo ruolo concede la possibilità di visualizzare i log di accesso e di accedere a integrità e analisi per il monitoraggio e la risoluzione dei problemi. [Altre informazioni.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Ruolo predefinito amministratore di rete

**Tipo:** Nuova funzionalità

**Categoria di servizio:** RBAC

**Funzionalità del prodotto:** Controllo di accesso

Gli utenti con questo ruolo possono esaminare le raccomandazioni relative all'architettura perimetrale di rete di Microsoft basate sui dati di telemetria di rete delle proprie sedi utente. Prestazioni di rete per Office 365 si basa su un'attenta architettura perimetrale della rete del cliente aziendale, che in genere è specifica della posizione utente. Questo ruolo consente di modificare le posizioni utente individuate e la configurazione dei parametri di rete per tali posizioni per ottenere misurazioni di telemetria e raccomandazioni di progettazione migliori. [Altre informazioni.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Attività e download bulk nell'esperienza del portale di amministrazione di Azure AD

**Tipo:** Nuova funzionalità

**Categoria di servizio:** Gestione utenti

**Funzionalità del prodotto:** Directory

È ora possibile eseguire attività bulk su utenti e gruppi in Azure AD caricando un file CSV nell'esperienza Azure AD portale di amministrazione. È possibile creare utenti, eliminare utenti e invitare utenti guest. È possibile aggiungere e rimuovere membri da un gruppo.

È anche possibile scaricare elenchi di risorse Azure AD dall'esperienza Azure AD portale di amministrazione. È possibile scaricare l'elenco di utenti nella directory, l'elenco dei gruppi nella directory e i membri di un gruppo specifico.

Per ulteriori informazioni, vedere gli argomenti seguenti:

- [Creare utenti](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add) o [invitare utenti Guest](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [Eliminare utenti](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete) o [ripristinare utenti eliminati](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [Scarica l'elenco di utenti](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download) o [Scarica l'elenco di gruppi](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- [Aggiungere (importare) membri](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members) o [rimuovere membri](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members) o [scaricare un elenco di membri](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members) per un gruppo

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

È stata aggiornata l'esperienza revisore per Azure AD le verifiche di accesso nel portale app personali. Alla fine di aprile, i revisori che hanno eseguito l'accesso al Azure AD esperienza di revisore delle verifiche di accesso visualizzeranno un banner che consentirà loro di provare l'esperienza aggiornata nell'accesso. Si noti che l'esperienza di verifica dell'accesso aggiornata offre le stesse funzionalità dell'esperienza attuale, ma con un'interfaccia utente migliorata sulle nuove funzionalità per consentire agli utenti di essere produttivi. [Altre informazioni sull'esperienza aggiornata sono disponibili qui](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review). Questa anteprima pubblica durerà fino alla fine del 2020 luglio. Alla fine di luglio, i revisori che non hanno optato per l'esperienza di anteprima verranno automaticamente indirizzati al mio accesso per eseguire le verifiche di accesso. Se si vuole che i revisori passino in modo permanente all'esperienza di anteprima ora in accesso personale, [effettuare una richiesta qui](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u).

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Il provisioning utenti in ingresso e le app writeback del giorno lavorativo supportano ora le versioni più recenti dell'API dei servizi Web della giornata lavorativa

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Provisioning di app

**Funzionalità del prodotto:** 

In base ai commenti e suggerimenti dei clienti, è stata aggiornata la giornata lavorativa per il provisioning utenti in ingresso e le app writeback nella raccolta di app aziendali per supportare le versioni più recenti dell'API dei servizi Web per la giornata lavorativa (WWS). Con questa modifica, i clienti possono specificare la versione dell'API WWS che si desidera utilizzare nella stringa di connessione. Ciò offre ai clienti la possibilità di recuperare più attributi HR disponibili nelle versioni della giornata lavorativa. L'app writeback dei giorni lavorativi USA ora il servizio Web Change_Work_Contact_Info giorni lavorativi consigliato per superare le limitazioni di Maintain_Contact_Info.

Se nella stringa di connessione non è specificata alcuna versione, per impostazione predefinita, le app di provisioning in ingresso per la giornata lavorativa continueranno a usare WWS v 21.1 per passare alle API della giornata lavorativa più recente per il provisioning degli utenti in ingresso, i clienti devono aggiornare la stringa di connessione come descritto [nell'esercitazione](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles) e aggiornare anche gli XPath usati per gli attributi [di giornata](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30)lavorativa 

Per usare la nuova API per il writeback, non sono necessarie modifiche nell'app di provisioning del writeback dei giorni lavorativi. Sul lato della giornata lavorativa, verificare che l'account dell'utente del sistema di integrazione lavorativa (ISU) disponga delle autorizzazioni per richiamare il processo di business Change_Work_Contact come illustrato nella sezione esercitazione [configurare le autorizzazioni dei criteri di sicurezza del processo di business](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions). 

La [Guida dell'esercitazione](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial) è stata aggiornata in modo da riflettere il supporto della nuova versione dell'API.

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>Gli utenti con ruolo di accesso predefinito sono ora inclusi nell'ambito per il provisioning

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Provisioning di app

**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

In passato, gli utenti con il ruolo di accesso predefinito non rientrano nell'ambito del provisioning. Il feedback dei clienti desidera che gli utenti con questo ruolo siano nell'ambito del provisioning. A partire dal 16 aprile 2020, tutte le nuove configurazioni di provisioning consentono di effettuare il provisioning degli utenti con il ruolo di accesso predefinito. Gradualmente si modificherà il comportamento per le configurazioni di provisioning esistenti per supportare il provisioning degli utenti con questo ruolo. [Altre informazioni.](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>Interfaccia utente di provisioning aggiornata

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Provisioning di app

**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

L'esperienza di provisioning è stata aggiornata per creare una vista di gestione più mirata. Quando si passa al pannello di provisioning per un'applicazione aziendale che è già stata configurata, sarà possibile monitorare facilmente lo stato di avanzamento del provisioning e gestire le azioni, ad esempio l'avvio, l'arresto e il riavvio del provisioning. [Altre informazioni.](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>La convalida della regola di gruppo dinamica è ora disponibile per l'anteprima pubblica

**Tipo:** Funzionalità modificata

**Categoria di servizio:** Gestione gruppi

**Funzionalità del prodotto:** Collaborazione

Azure Active Directory (Azure AD) ora fornisce i mezzi per convalidare le regole dinamiche del gruppo. Nella scheda **Convalida regole** è possibile convalidare la regola dinamica rispetto ai membri del gruppo di esempio per confermare che la regola funziona come previsto. Quando si creano o si aggiornano le regole dinamiche del gruppo, gli amministratori desiderano sapere se un utente o un dispositivo sarà membro del gruppo. Questo consente di valutare se un utente o un dispositivo soddisfano i criteri di regola e gli aiuti per la risoluzione dei problemi quando l'appartenenza non è prevista.

Per altre informazioni, vedere [convalida di una regola di appartenenza a un gruppo dinamico (anteprima)](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation).

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>Punteggio sicuro identità-impostazioni predefinite di sicurezza e aggiornamenti dell'azione di miglioramento dell'autenticazione a più fattori

**Tipo:** Funzionalità modificata

**Categoria di servizio:** N/D

**Funzionalità del prodotto:** Protezione e sicurezza delle identità

**Supporto delle impostazioni predefinite di sicurezza per le azioni di miglioramento Azure ad:** Microsoft Secure Score aggiornerà le azioni di miglioramento per supportare i [valori predefiniti di sicurezza in Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), che semplificano la protezione dell'organizzazione con impostazioni di sicurezza preconfigurate per gli attacchi comuni. Questo influirà sulle azioni di miglioramento seguenti:

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

Queste nuove azioni di miglioramento richiedono la registrazione degli utenti o degli amministratori per l'autenticazione a più fattori (multi-factor authentication) nella directory e la definizione del giusto set di criteri che soddisfano le esigenze dell'organizzazione. L'obiettivo principale è quello di garantire flessibilità garantendo che tutti gli utenti e gli amministratori possano eseguire l'autenticazione con più fattori o richieste di verifica dell'identità basate sul rischio. Questo può assumere la forma di avere più criteri che applicano le decisioni con ambito o di impostare le impostazioni predefinite di sicurezza (a partire dal 16 marzo) che consentono a Microsoft di decidere quando sfidare gli utenti per l'autenticazione a più fattori. Scopri di [più sulle novità di Microsoft Secure Score](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new).

---

## <a name="march-2020"></a>Marzo 2020

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>Account di Azure Active Directory non gestiti nell'aggiornamento B2B per il 2021 marzo

**Tipo:** Modifica pianificata  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
**A partire dal 31 marzo 2021**, Microsoft non supporterà più il riscatto degli inviti creando account di Azure Active Directory non gestiti (Azure ad) e tenant per gli scenari di collaborazione B2B. In tal caso, si consiglia di acconsentire esplicitamente all' [autenticazione con il codice di posta elettronica](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)monouso.

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>Gli utenti con il ruolo di accesso predefinito saranno nell'ambito del provisioning

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
In passato, gli utenti con il ruolo di accesso predefinito non rientrano nell'ambito del provisioning. Il feedback dei clienti desidera che gli utenti con questo ruolo siano nell'ambito del provisioning. Si sta lavorando alla distribuzione di una modifica in modo che tutte le nuove configurazioni di provisioning consentano di eseguire il provisioning degli utenti con il ruolo di accesso predefinito. Gradualmente, si modificherà il comportamento per le configurazioni di provisioning esistenti per supportare il provisioning degli utenti con questo ruolo. Non è richiesta alcuna azione da parte del cliente. Una volta apportata la modifica, verrà pubblicato un aggiornamento alla [documentazione](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned) .

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD collaborazione B2B sarà disponibile in Microsoft Azure gestiti da tenant 21Vianet (Azure Cina 21Vianet)

**Tipo:** Modifica pianificata  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
Le funzionalità di collaborazione B2B Azure AD saranno rese disponibili in Microsoft Azure gestite da tenant 21Vianet (Azure Cina 21Vianet), consentendo agli utenti in un tenant di Azure China 21Vianet di collaborare senza difficoltà con gli utenti di altri tenant di Azure Cina 21Vianet. [Scopri di più su Azure ad collaborazione B2B](https://docs.microsoft.com/azure/active-directory/b2b/).

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Riprogettazione della posta elettronica di invito per la collaborazione B2B Azure AD

**Tipo:** Modifica pianificata  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
I [messaggi di posta elettronica](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) inviati dall'Azure ad servizio di invito per la collaborazione B2B per invitare gli utenti nella directory verranno riprogettati per rendere le informazioni di invito e i passaggi successivi dell'utente più chiari.

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Le modifiche ai criteri di HomeRealmDiscovery verranno visualizzate nei log di controllo

**Tipo:** Correzione  
**Categoria di servizio:** Audit  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
È stato corretto un bug in cui le modifiche apportate al [criterio HomeRealmDiscovery](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal) non sono state incluse nei log di controllo. A questo punto sarà possibile vedere quando e come il criterio è stato modificato e da chi. 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2020 marzo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel 2020 marzo sono state aggiunte le nuove app 51 con supporto federativo per la raccolta di app: 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect), [Zoho One China](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial), [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/), [profit.co SAML App](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial), [provider di Servizi Ipoint](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial), [contexxt.ai SPHERE](https://contexxt-sphere.com/login), [Wisdom by Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial), [flare Digital Signing](https://spark-dev.pixelnebula.com/login), [LOGZ.io-cloud osservance for Engineers](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial), [Spectrum](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial), [BizzContact](https://bizzcontact.app/), [Elqano SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial), [MARKETSIGNSHARE](http://www.signshare.com/), [CrossKnowledge Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial), [Netvision Comps](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial), [FCM Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial), [Rib A/S Byggeweb mobile](https://apps.apple.com/us/app/docia/id529058757), GoLinks [, Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial), [zScaler B2B User Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial), [Lift](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial), [PlanView Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial), [WatchTeams](https://www.devfinition.com/), [Aster](https://demo.asterapp.io/login), [Skills Workflow](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial), [node Insight](https://admin.nodeinsight.com/AADLogin.aspx), [piattaforma IP](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial), [Invision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial), [PipeDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial), [Showcase workshop](https://app.showcaseworkshop.com/), [piattaforma di integrazione GreenLight](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial), [gestione dell'accesso conforme a GreenLight](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial), [Grok Learning](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial), [Miradore online](https://login.online.miradore.com/), [Khoros care](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial), [AskYourTeam](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial), [TruNarrative](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial), [Smartwaiver](https://www.smartwaiver.com/m/user/sw_login.php?wms_login), [BizAgi Studio per l'automazione dei processi digitali](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial), [insuiteX](https://www.insuite.jp/), [Sybo](https://www.systexsoftware.com.tw/), [brity](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial), [WhosOffice](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial), [E-Days](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial) [Datadog](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial) [, Kollective](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial) [Sdn](https://portal.kollective.app/login), [Witivio](https://app.witivio.com/) [,](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial) [Playvox](https://my.playvox.com/login), [Korn Ferry 360](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial), [Campus Café](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD collaborazione B2B disponibile nei tenant di Azure per enti pubblici

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C
 
Le funzionalità di collaborazione B2B di Azure AD sono ora disponibili tra alcuni tenant di Azure per enti pubblici.  Per sapere se il tenant è in grado di usare queste funzionalità, seguire le istruzioni in [come è possibile stabilire se la collaborazione B2B è disponibile nel tenant di Azure per enti pubblici degli Stati Uniti?](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant).

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>L'integrazione di monitoraggio di Azure per i log di Azure è ora disponibile in Azure per enti pubblici

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
L'integrazione di monitoraggio di Azure con i log di Azure AD è ora disponibile in Azure per enti pubblici. È possibile instradare log di Azure AD (log di controllo e accesso) a un account di archiviazione, a un hub eventi e a Log Analytics. Vedere la [documentazione dettagliata](https://aka.ms/aadlogsinamd) , oltre ai [piani di distribuzione per la creazione di report e il monitoraggio](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting) per gli scenari Azure ad.

---

### <a name="identity-protection-refresh-in-azure-government"></a>Aggiornamento di Identity Protection in Azure per enti pubblici

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Siamo lieti di condividere che abbiamo implementato l'esperienza di aggiornamento [Azure ad Identity Protection](https://aka.ms/IdentityProtectionDocs)   nel [portale di Microsoft Azure per enti pubblici](https://portal.azure.us/). Per altre informazioni, vedere il [post di Blog](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)relativo all'annuncio.

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Ripristino di emergenza: scaricare e archiviare la configurazione del provisioning

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il servizio di provisioning Azure AD offre un set completo di funzionalità di configurazione. I clienti devono essere in grado di salvare la configurazione in modo da potervi fare riferimento in un secondo momento o eseguire il rollback a una versione funzionante nota. È stata aggiunta la possibilità di scaricare la configurazione di provisioning come file JSON e caricarla quando è necessario. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration)

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (reimpostazione della password self-service) richiede ora due Gate per gli amministratori in Microsoft Azure gestiti da 21Vianet (Azure China 21Vianet) 

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Reimpostazione della password self-service  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Nelle versioni precedenti di Microsoft Azure gestite da 21Vianet (Azure China 21Vianet), gli amministratori che usano la reimpostazione della password self-service (SSPR) per reimpostare le proprie password necessitavano solo di una "Gate" (Challenge) per dimostrare la propria identità. In cloud pubblici e in altri cloud nazionali, gli amministratori in genere devono usare due controlli per dimostrare la propria identità quando usano SSPR. Tuttavia, dal momento che non sono stati supportati SMS o telefonate in Azure China 21Vianet, è stata consentita la reimpostazione della password di un solo Gate da parte degli amministratori

È in corso la creazione della parità di funzionalità SSPR tra Azure China 21Vianet e il cloud pubblico. In futuro, è necessario che gli amministratori usino due controlli quando usano SSPR. Verranno supportate le notifiche e i codici delle app di autenticazione e SMS, le chiamate telefoniche e i codici. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences)

---

### <a name="password-length-is-limited-to-256-characters"></a>La lunghezza della password è limitata a 256 caratteri

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Per garantire l'affidabilità del servizio Azure AD, le password utente hanno ora una lunghezza limitata a 256 caratteri. Agli utenti con password più lunghe verrà richiesto di modificare la password all'accesso successivo, contattando l'amministratore o usando la funzionalità di reimpostazione della password self-service.

Questa modifica è stata abilitata il 13 marzo 2020, alle 10.00 PST (18:00 UTC) e l'errore è AADSTS 50052, InvalidPasswordExceedsMaxLength. Per ulteriori informazioni, vedere l'avviso relativo alla [modifica di rilievo](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters) .

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>I log di accesso Azure AD sono ora disponibili per tutti i tenant gratuiti tramite il portale di Azure

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
A questo punto, i clienti che hanno tenant gratuiti possono accedere ai registri di accesso [Azure ad dal portale di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins) per un massimo di 7 giorni. In precedenza, i log di accesso erano disponibili solo per i clienti con licenze Azure Active Directory Premium. Con questa modifica, tutti i tenant possono accedere a questi log tramite il portale.

> [!NOTE]
> I clienti hanno ancora bisogno di una licenza Premium (Azure Active Directory Premium P1 o P2) per accedere ai log di accesso tramite Microsoft Graph API e monitoraggio di Azure.

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>Deprecazione dell'opzione gruppi a livello di directory da impostazioni generali nei gruppi portale di Azure

**Tipo:** Deprecato  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Per offrire ai clienti un modo più flessibile per creare gruppi a livello di directory che soddisfino al meglio le proprie esigenze, l'opzione **gruppi a livello di directory** è stata sostituita dalle impostazioni generali dei **gruppi**  >  **General** nel portale di Azure con un collegamento alla [documentazione del gruppo dinamico](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). È stata migliorata la documentazione per includere altre istruzioni, in modo che gli amministratori possano creare tutti i gruppi di utenti che includono o escludono gli utenti guest.

---

## <a name="february-2020"></a>Febbraio 2020

### <a name="upcoming-changes-to-custom-controls"></a>Modifiche imminenti ai controlli personalizzati

**Tipo:** Modifica pianificata  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Stiamo pianificando di sostituire l'anteprima dei controlli personalizzati corrente con un approccio che consente alle funzionalità di autenticazione fornite dai partner di funzionare senza interruzioni con il Azure Active Directory amministratore e l'esperienza dell'utente finale. Attualmente, le soluzioni di autenticazione a più fattori di partner affrontano le limitazioni seguenti: funzionano solo dopo l'immissione di una password; non vengono usati come multi-factor authentication per l'autenticazione step-up in altri scenari chiave. e non si integrano con le funzioni di gestione delle credenziali amministrative e degli utenti finali. La nuova implementazione consentirà ai fattori di autenticazione forniti dai partner di operare insieme a fattori predefiniti per gli scenari principali, ad esempio registrazione, utilizzo, attestazione dell'autenticazione a più fattori, autenticazione, creazione di report e registrazione. 

I controlli personalizzati continueranno a essere supportati in anteprima insieme alla nuova progettazione fino a quando non raggiunge la disponibilità generale. A questo punto, si fornirà ai clienti il tempo per eseguire la migrazione alla nuova progettazione. A causa delle limitazioni dell'approccio corrente, non verranno caricati nuovi provider fino a quando non sarà disponibile la nuova progettazione. Stiamo lavorando a stretto contatto con clienti e provider e la cronologia verrà comunicata Man mano che ci avviciniamo. [Altre informazioni](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#)

---

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

[IamIP Patent Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial), [Experience Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial), [NS1 SSO for Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial), [Barracuda Email Security Service](https://ess.barracudanetworks.com/sso/azure), [ABA Reporting](https://myaba.co.uk/client-access/signin/auth/msad), [in caso di crisi-portale online](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial), [BIC cloud design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial), [apicoltore Azure ad Data Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial), [Korn Ferry assessments](https://www.kornferry.com/solutions/kf-digital/kf-assess), [comando Verkada](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial), [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial), [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial), [EAB Navigate](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial), [New Relic (versione limitata)](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial), [Tulio](https://admin.thulium.com/login/instance), [ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial), [selezione modelli per Teams](https://links.officeatwork.com/templatechooser-download-teams), [BEEY](https://www.beesy.me/index.php/site/login), [Health Support System](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial), [Mural](https://app.mural.co/signup), [hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial), [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [, Wakelet](https://wakelet.com/login), Firmex [VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial), [Thinglink for teachers and Schools](https://www.thinglink.com/), [coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial), [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product), [WeDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial), [InvitePeople](https://invitepeople.com/login), [ristampas desk-article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial), [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Nuovi connettori di provisioning nella raccolta di applicazioni Azure AD-2020 febbraio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

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
 
La nuova esperienza utente finale dell'account verrà aggiornata al `https://myaccount.microsoft.com` mese successivo. Altre informazioni sull'esperienza e tutte le funzionalità self-service per gli account offerte dagli utenti finali sono disponibili nella [Guida del portale](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)per gli account.

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
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione di account utente per queste app integrate di recente:

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

Per altre informazioni su come migliorare la sicurezza dell'organizzazione usando il provisioning automatico degli account utente, vedere [Automatizzare il provisioning utenti nelle applicazioni SaaS con Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2020 gennaio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel gennaio 2020 sono state aggiunte le nuove app 33 con supporto federativo per la raccolta di app: 

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial), [fastly Edge cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial), di [bonifica Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial), [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial), [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik), SkyKick [,](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial) [LeaveBot](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access), [SSOGEN](https://leavebot.io/#home), [Datacamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial), [tripattioni](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial), [Smarts](https://www.intumit.com/english/SmartWork.html), [dotcom-monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial), [-Azure ad SSO gateway per Oracle E-Business Suite-EBS, PeopleSoft e JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial), [Hosted MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial), [Yuhu Property Management Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial), [LumApps](https://sites.lumapps.com/login), [overwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial), [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial), [SmartDB for Microsoft teams](http://teams.smartdb.jp/login/), [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial), [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial), [Maxient Management Manager software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial), [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial), PortalTalk [365](https://www.portaltalk.com/), [CoreView](https://portal.coreview.com/), [squelch cloud Office365 Connector](https://laxmi.squelch.io/login), [PingFlow Authentication](https://app-staging.pingview.io/), [PrinterLogic Saas](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial), Taskize [Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial), [Sandwai, EZRentOut](https://app.sandwai.com/) [, AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial), [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/) [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="two-new-identity-protection-detections"></a>Due nuovi rilevamenti di Identity Protection

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Sono stati aggiunti due nuovi tipi di rilevamento collegato per l'accesso a Identity Protection: regole di manipolazione della posta in arrivo sospette e viaggi non possibili. Questi rilevamenti offline vengono individuati da Microsoft Cloud App Security (MCAS) e influiscono sull'utente e sul rischio di accesso in Identity Protection. Per altre informazioni su questi rilevamenti, vedere i [tipi di rischio di accesso](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk).
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>Modifica di rilievo: i frammenti URI non verranno trasferiti tramite il reindirizzamento di accesso

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
A partire dall'8 febbraio 2020, quando una richiesta viene inviata a login.microsoftonline.com per l'accesso a un utente, il servizio aggiunge un frammento vuoto alla richiesta.  Ciò impedisce a una classe di attacchi di reindirizzamento assicurando che il browser cancelli tutti i frammenti esistenti nella richiesta. Nessuna applicazione deve avere una dipendenza da questo comportamento. Per ulteriori informazioni, vedere [modifiche di rilievo](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020) nella documentazione della piattaforma Microsoft Identity.

