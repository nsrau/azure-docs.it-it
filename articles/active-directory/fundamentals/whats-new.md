---
title: Novità Note sulla versione - Azure Active Directory | Microsoft Docs
description: Informazioni sulle novità di Azure Active Directory, tra cui le note sulla versione più aggiornate, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti.
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50f2f1f7f5d8509a28557e9704bc178be465b1ee
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648667"
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory

>Consente di ricevere notifiche su quando rivisitare la pagina per gli aggiornamenti copiando e incollando questo `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` URL: ![nel lettore di feed](./media/whats-new/feed-icon-16x16.png) dell'icona del lettore feed RSS.

Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente. Se si cercano informazioni precedenti agli ultimi sei mesi, è possibile trovarli in [Archivio per le novità in Azure Active Directory](whats-new-archive.md).

---

## <a name="august-2019"></a>Agosto 2019

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET ignora la cache condivisa MSAL.NET per gli scenari per conto di

**Tipo:** Correzione  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

A partire da Azure AD Authentication Library (ADAL.NET) versione 5.0.0-Preview, gli sviluppatori [di app devono serializzare una cache per ogni account per le app Web e le API Web](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api). In caso contrario, alcuni scenari che usano il [flusso per conto di](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow), insieme ad alcuni casi d'uso `UserAssertion`specifici di, possono determinare un'elevazione dei privilegi. Per evitare questa vulnerabilità, ADAL.NET ignora ora la cache condivisa di Microsoft Authentication Library per DotNet (MSAL.NET) per gli scenari per conto di.

Per altre informazioni su questo problema, vedere [Azure Active Directory vulnerabilità dell'elevazione dei privilegi della libreria di autenticazione](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258).

---

## <a name="july-2019"></a>2019 luglio

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>Pianificare la modifica: Aggiornamento del servizio proxy di applicazione per supportare solo TLS 1,2

**Tipo:** Modifica prevista  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Per fornire la crittografia più avanzata, l'accesso al servizio proxy di applicazione verrà limitato solo ai protocolli TLS 1,2. Questa limitazione verrà inizialmente implementata per i clienti che usano già i protocolli TLS 1,2, quindi non sarà possibile vedere l'effetto. La deprecazione completa dei protocolli TLS 1,0 e TLS 1,1 verrà completata il 31 agosto 2019. I clienti che usano ancora TLS 1,0 e TLS 1,1 riceveranno una notifica avanzata per prepararsi a questa modifica.

Per mantenere la connessione al servizio proxy di applicazione in tutte le modifiche, è consigliabile assicurarsi che le combinazioni client-server e browser-server siano aggiornate per l'uso di TLS 1,2. È inoltre consigliabile assicurarsi di includere tutti i sistemi client usati dai dipendenti per accedere alle app pubblicate tramite il servizio proxy di applicazione.

Per altre informazioni, vedere [aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application).

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>Pianificare la modifica: Gli aggiornamenti della progettazione sono in arrivo per la raccolta di applicazioni

**Tipo:** Modifica prevista  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Nuove modifiche all'interfaccia utente verranno apportate alla progettazione dell' **aggiunta dall'area raccolta** del pannello **Aggiungi un'applicazione** . Queste modifiche consentiranno di trovare più facilmente le app che supportano il provisioning automatico, OpenID Connect, Security Assertion Markup Language (SAML) e Single Sign-on (SSO) per le password.

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>Pianificare la modifica: Rimozione dell'indirizzo IP del server multi-factor authentication dall'indirizzo IP di Office 365

**Tipo:** Modifica prevista  
**Categoria di servizio:** MFA  
**Funzionalità del prodotto:** Identity Security e Protection

È in corso la rimozione dell'indirizzo IP del server multi-factor authentication dall' [indirizzo IP di Office 365 e dal servizio Web URL](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service). Se attualmente si fa affidamento su queste pagine per aggiornare le impostazioni del firewall, è necessario assicurarsi di includere anche l'elenco degli indirizzi IP documentati nella sezione **requisiti del firewall del server Azure** a più fattori dell' [Introduzione con l'articolo server Azure Multifactor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements) .

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>I token solo app richiedono ora che l'app client esista nel tenant delle risorse

**Tipo:** Correzione  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Il 26 luglio 2019 è stato modificato il modo in cui vengono forniti i token solo app tramite la [concessione delle credenziali client](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow). In precedenza, le app potevano ottenere i token per chiamare altre app, indipendentemente dal fatto che l'app client si trovasse nel tenant. Questo comportamento è stato aggiornato in modo che le risorse a tenant singolo, talvolta chiamate API Web, possano essere chiamate solo da app client presenti nel tenant delle risorse.

Se l'app non si trova nel tenant delle risorse, viene visualizzato un messaggio `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` di errore che indica che per risolvere il problema è necessario creare l'entità servizio dell'app client nel tenant, usando l'endpoint di consenso dell' [amministratore](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint) o [tramite PowerShell ](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), che garantisce che il tenant abbia concesso all'app l'autorizzazione per operare nel tenant.

Per ulteriori informazioni, vedere [What ' s New for Authentication?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant).

> [!NOTE]
> Il consenso esistente tra il client e l'API continua a non essere necessario. Le app devono ancora eseguire controlli di autorizzazione personalizzati.

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>Nuovo accesso senza password per Azure AD usando le chiavi di sicurezza FIDO2

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Azure AD i clienti possono ora impostare i criteri per gestire le chiavi di sicurezza FIDO2 per gli utenti e i gruppi dell'organizzazione. Gli utenti finali possono anche registrare autonomamente le proprie chiavi di sicurezza, usare le chiavi per accedere ai propri account Microsoft nei siti Web, mentre nei dispositivi che supportano FIDO, e accedere ai dispositivi Windows 10 aggiunti a Azure AD.

Per altre informazioni, vedere [abilitare l'accesso senza password per Azure ad (anteprima)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-enable) per le informazioni relative all'amministratore e [configurare le informazioni di sicurezza per l'uso di una chiave di sicurezza (anteprima)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) per le informazioni relative all'utente finale.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Nuove app federate disponibili nella raccolta di App Azure AD-2019 luglio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con soluzioni di terze parti

Nel 2019 luglio sono state aggiunte le 18 nuove app con supporto federativo per la raccolta di app:

[Ungerboeck software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial), [Bright pattern omnicanale Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial), [clever Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access per Ethidex Compliance Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso), [hype](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [ Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial), [ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial), [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html), [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial), [TwineSocial](https://twinesocial.com/), [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial), [iperanna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial), [PharmID WasteWitness](https://www.pharmid.com/), [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/), [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste app SaaS appena supportate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e report

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per queste app appena integrate:

- [Tastierino](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Directory federata](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatizzato degli account utente, vedere automatizzare il provisioning [degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>Nuovo tag del servizio Azure AD Domain Services per il gruppo di sicurezza di rete

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

Se si è stanchi di gestire lunghi elenchi di indirizzi IP e intervalli, è possibile usare il nuovo tag del servizio di rete **AzureActiveDirectoryDomainServices** nel gruppo di sicurezza di rete di Azure per proteggere il traffico in ingresso verso la Azure ad Domain Services virtuale subnet di rete.

Per ulteriori informazioni su questo nuovo tag di servizio, vedere [gruppi di sicurezza di rete per Azure ad Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/network-considerations#default-network-service-group).

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nuovi controlli di sicurezza per Azure AD Domain Services (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

Siamo lieti di annunciare il rilascio di Azure AD controllo della sicurezza del servizio del dominio per l'anteprima pubblica. Il controllo della sicurezza consente di fornire informazioni importanti sui servizi di autenticazione tramite lo streaming di eventi di controllo di sicurezza a risorse mirate, tra cui archiviazione di Azure, aree di lavoro di Azure Log Analytics e hub eventi di Azure, usando il servizio del dominio Azure AD Portale.

Per ulteriori informazioni, vedere [Enable Security Audits for Azure ad Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>Nuovi metodi di autenticazione utilizzo & Insights (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Reimpostazione password self-service  
**Funzionalità del prodotto:** Monitoraggio e report

I nuovi metodi di autenticazione & i report di Insights possono essere utili per comprendere in che modo le funzionalità come Azure autenticazione a più fattori e la reimpostazione della password self-service vengono registrate e usate nell'organizzazione, incluso il numero di registrato utenti per ogni funzionalità, frequenza con cui viene usata la reimpostazione della password self-service per reimpostare le password e con quale metodo viene eseguita la reimpostazione.

Per altre informazioni, vedere [metodi di autenticazione utilizzo & Insights (anteprima)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights).

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>Sono disponibili nuovi report di sicurezza per tutti gli amministratori di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Identity Security e Protection

Tutti gli amministratori Azure AD ora possono selezionare il banner nella parte superiore dei report di sicurezza esistenti, ad esempio il report **utenti contrassegnati per il rischio** , per iniziare a usare la nuova esperienza di sicurezza, come illustrato in **utenti a rischio** e report sugli accessi a **rischio** . Nel corso del tempo, tutti i report sulla sicurezza passano dalle versioni precedenti alle nuove versioni, con i nuovi report che forniscono le funzionalità aggiuntive seguenti:

- Filtro e ordinamento avanzati

- Azioni bulk, ad esempio la dismissing del rischio utente

- Conferma di entità compromesse o sicure

- Stato di rischio, copertura: A rischio, eliminato, risolto e confermato

Per altre informazioni, vedere [report utenti a rischio](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-users-report) e [report](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-sign-ins-report)sugli accessi a rischio.

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Nuovi controlli di sicurezza per Azure AD Domain Services (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

Siamo lieti di annunciare il rilascio di Azure AD controllo della sicurezza del servizio del dominio per l'anteprima pubblica. Il controllo della sicurezza consente di fornire informazioni importanti sui servizi di autenticazione tramite lo streaming di eventi di controllo di sicurezza a risorse mirate, tra cui archiviazione di Azure, aree di lavoro di Azure Log Analytics e hub eventi di Azure, usando il servizio del dominio Azure AD Portale.

Per ulteriori informazioni, vedere [Enable Security Audits for Azure ad Domain Services (Preview)](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events).

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>Nuova federazione diretta B2B con SAML/WS-Fed (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C

La Federazione diretta aiuta a semplificare l'utilizzo dei partner la cui soluzione di gestione delle identità gestita da IT non è Azure AD, usando sistemi di identità che supportano gli standard SAML o WS-Fed. Dopo aver configurato una relazione di federazione diretta con un partner, qualsiasi nuovo utente Guest invitato da tale dominio potrà collaborare con l'utente usando il proprio account aziendale esistente, rendendo l'esperienza utente per i guest più trasparente.

Per ulteriori informazioni, vedere [Federazione diretta con ad FS e provider di terze parti per utenti Guest (anteprima)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste app SaaS appena supportate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e report

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per queste app appena integrate:

- [Tastierino](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Directory federata](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatizzato degli account utente, vedere automatizzare il provisioning [degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>Nuovo controllo per i nomi di gruppi duplicati nel portale di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppo  
**Funzionalità del prodotto:** Collaborazione

A questo punto, quando si crea o si aggiorna un nome di gruppo dal portale di Azure AD, verrà eseguito un controllo per verificare se si sta duplicando un nome di gruppo esistente nella risorsa. Se si determina che il nome è già in uso da parte di un altro gruppo, verrà chiesto di modificarne il nome.

Per altre informazioni, vedere [gestire i gruppi nel portale di Azure ad](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context).

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>Azure AD ora supporta i parametri di query statici negli URI di risposta (Reindirizzamento)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Azure ad app possono ora registrare e usare URI di risposta (Reindirizzamento) con parametri di query statici (ad `https://contoso.com/oauth2?idp=microsoft`esempio,) per le richieste OAuth 2,0. Il parametro della query statica è soggetto alle stringhe corrispondenti per gli URI di risposta, esattamente come qualsiasi altra parte dell'URI di risposta. Se non è presente alcuna stringa registrata corrispondente all'URI di reindirizzamento con decodifica URL, la richiesta viene rifiutata. Se viene trovato l'URI di risposta, l'intera stringa viene utilizzata per reindirizzare l'utente, incluso il parametro della query statica.

Gli URI di risposta dinamici sono ancora proibiti perché rappresentano un rischio per la sicurezza e non possono essere usati per mantenere le informazioni sullo stato attraverso una richiesta di autenticazione. A questo scopo, usare il `state` parametro.

Attualmente, le schermate di registrazione delle app del portale di Azure continuano a bloccare i parametri della query. Tuttavia, è possibile modificare manualmente il manifesto dell'applicazione per aggiungere e testare i parametri di query nell'app. Per ulteriori informazioni, vedere [What ' s New for Authentication?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters).

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>I log attività (API Graph) per Azure AD sono ora disponibili tramite i cmdlet di PowerShell

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Report  
**Funzionalità del prodotto:** Monitoraggio e report

Siamo lieti di annunciare che Azure AD log attività (report di controllo e accesso) sono ora disponibili tramite il modulo di Azure AD PowerShell. In precedenza, era possibile creare script personalizzati usando gli endpoint MS API Graph e ora abbiamo esteso questa funzionalità ai cmdlet di PowerShell.

Per ulteriori informazioni sull'utilizzo di questi cmdlet, vedere [Azure ad cmdlet di PowerShell per la creazione di report](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting).

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>Controlli filtro aggiornati per i log di controllo e accesso in Azure AD

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Report  
**Funzionalità del prodotto:** Monitoraggio e report

I report di controllo e accesso sono stati aggiornati per poter applicare diversi filtri senza doverli aggiungere come colonne nelle schermate dei report. Inoltre, è ora possibile decidere il numero di filtri che si desidera visualizzare sullo schermo. Questi aggiornamenti interagiscono tra loro per semplificare la lettura dei report e l'ambito delle proprie esigenze.

Per altre informazioni su questi aggiornamenti, vedere [filtrare i log di controllo](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs) e le [attività di accesso al filtro](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities).

---

## <a name="june-2019"></a>Giugno 2019

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>Nuova API riskDetections per Microsoft Graph (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Identity Security e Protection

Siamo lieti di annunciare che la nuova API riskDetections per Microsoft Graph è ora disponibile in anteprima pubblica. È possibile usare questa nuova API per visualizzare un elenco dei rilevamenti dei rischi di accesso e dell'utente correlati alla protezione delle identità dell'organizzazione. È anche possibile usare questa API per eseguire query in modo più efficiente sui rilevamenti dei rischi, inclusi i dettagli sul tipo di rilevamento, lo stato, il livello e altro ancora.

Per ulteriori informazioni, vedere la [documentazione di riferimento dell'API di rilevamento dei rischi](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD-2019 giugno

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con soluzioni di terze parti

Nel giugno 2019 sono state aggiunte le 22 nuove app con supporto federativo per la raccolta di app:

[Azure ad SAML Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial), [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial), [Anaqua](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial), [client VPN di Azure](https://portal.azure.com/), [expensein](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial), [Helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)helper, [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial), [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial), [Mercedes-Benz in-Car Office](https://me.secure.mercedes-benz.com/), [Skore](https://app.justskore.it/), [Oracle Cloud Infrastructure console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), Oracle Access Manager per Oracle Merchandising al dettaglio, Oracle Access Manager per Oracle E-Business Suite, Oracle IDC per E-Business Suite, Oracle IDC per PeopleSoft, Oracle IDC per JD Edwards

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>Automatizzare il provisioning degli account utente per queste app SaaS appena supportate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e report

È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per queste app appena integrate:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

Per altre informazioni su come proteggere meglio l'organizzazione usando il provisioning automatizzato degli account utente, vedere automatizzare il provisioning [degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>Visualizzare lo stato di avanzamento in tempo reale del servizio di provisioning Azure AD

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

L'esperienza di provisioning Azure AD è stata aggiornata in modo da includere un nuovo indicatore di stato che mostra la distanza del processo di provisioning degli utenti. Questa esperienza aggiornata fornisce anche informazioni sul numero di utenti di cui è stato effettuato il provisioning durante il ciclo corrente, nonché sul numero di utenti di cui è stato effettuato il provisioning fino a oggi.

Per altre informazioni, vedere [controllare lo stato del](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)provisioning dell'utente.

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>Le informazioni personalizzate distintive dell'azienda vengono visualizzate nelle schermate di disconnessione e di errore

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Il Azure AD è stato aggiornato in modo che la personalizzazione dell'azienda venga ora visualizzata nelle schermate di disconnessione e di errore, nonché nella pagina di accesso. Non è necessario eseguire alcuna operazione per attivare questa funzionalità, Azure AD usa semplicemente gli asset già configurati nell'area di **personalizzazione della società** del portale di Azure.

Per ulteriori informazioni sulla configurazione della personalizzazione dell'azienda, vedere [la pagina relativa all'aggiunta della personalizzazione alle pagine di Azure Active Directory dell'organizzazione](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding).

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Il server Azure multi-factor authentication (AMF) non è più disponibile per le nuove distribuzioni

**Tipo:** Funzionalità deprecate  
**Categoria di servizio:** MFA  
**Funzionalità del prodotto:** Identity Security e Protection

A partire dal 1 ° luglio 2019, Microsoft non offrirà più il server multi-factor authentication per le nuove distribuzioni. I nuovi clienti che vogliono richiedere l'autenticazione a più fattori nella propria organizzazione devono ora usare Azure a più fattori basato sul cloud. I clienti che hanno attivato il server di autenticazione a più fattori prima del 1 ° luglio non vedranno una modifica. Sarà comunque possibile scaricare la versione più recente, ottenere gli aggiornamenti futuri e generare le credenziali di attivazione.

Per altre informazioni, vedere [Introduzione al server Azure Multifactor Authentication](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy). Per ulteriori informazioni sull'autenticazione a più fattori basata sul cloud, vedere [pianificazione di una distribuzione di Azure a più fattori basata sul cloud](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted).

---

## <a name="may-2019"></a>Maggio 2019

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>Modifica del servizio: Supporto futuro per solo i protocolli TLS 1,2 nel servizio proxy di applicazione

**Tipo:** Modifica prevista  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Per fornire la crittografia migliore per i clienti, è possibile limitare l'accesso solo ai protocolli TLS 1,2 nel servizio proxy di applicazione. Questa modifica viene implementata gradualmente per i clienti che usano già i protocolli TLS 1,2, pertanto non è possibile visualizzare alcuna modifica.

La deprecazione di TLS 1,0 e TLS 1,1 si verifica il 31 agosto 2019, ma verrà fornita una notifica avanzata aggiuntiva, per cui si avrà il tempo necessario per prepararsi a questa modifica. Per prepararsi a questa modifica, verificare che le combinazioni client-server e server browser, inclusi i client usati dagli utenti per accedere alle app pubblicate tramite il proxy di applicazione, siano aggiornate in modo da usare il protocollo TLS 1,2 per mantenere la connessione all'applicazione Servizio proxy. Per altre informazioni, vedere [aggiungere un'applicazione locale per l'accesso remoto tramite il proxy di applicazione in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin).

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>Usare il report utilizzo e informazioni dettagliate per visualizzare i dati di accesso correlati all'app

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e report

È ora possibile usare il report Usage and Insights, disponibile nell'area **applicazioni aziendali** del portale di Azure, per ottenere una visualizzazione incentrata sull'applicazione dei dati di accesso, incluse informazioni su:

- App principali usate per la tua organizzazione

- App con gli accessi più non riusciti

- Errori di accesso principali per ogni app

Per altre informazioni su questa funzionalità, vedere [report sull'utilizzo e informazioni dettagliate nel portale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>Automatizzare il provisioning degli utenti nelle app cloud usando Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Monitoraggio e report

Seguire queste nuove esercitazioni per usare il servizio di provisioning Azure AD per automatizzare la creazione, l'eliminazione e l'aggiornamento degli account utente per le app basate sul cloud seguenti:

- [Del](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

È anche possibile seguire questa nuova [esercitazione su Dropbox](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), che fornisce informazioni su come eseguire il provisioning di oggetti gruppo.

Per altre informazioni su come proteggere meglio l'organizzazione tramite il provisioning automatizzato degli account utente, vedere automatizzare il provisioning [degli utenti in applicazioni SaaS con Azure ad](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning).

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>Il Punteggio di sicurezza identità è ora disponibile in Azure AD (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Identity Security e Protection

È ora possibile monitorare e migliorare il comportamento di sicurezza delle identità usando la funzionalità di assegnazione del Punteggio Identity Secure in Azure AD. La funzionalità di assegnazione del Punteggio Identity Secure usa un unico dashboard che consente di:

- Misurare oggettivamente il comportamento di sicurezza delle identità, in base a un punteggio compreso tra 1 e 223.

- Pianificare i miglioramenti della sicurezza delle identità

- Verifica il successo dei miglioramenti della sicurezza

Per ulteriori informazioni sulla funzionalità di assegnazione dei punteggi di sicurezza Identity, vedere la pagina relativa [al Punteggio di identità sicuro in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score).

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>È ora disponibile una nuova esperienza Registrazioni app (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Esperienza di sviluppo

La nuova esperienza [registrazioni app](https://aka.ms/appregistrations) è ora disponibile a livello generale. Questa nuova esperienza include tutte le funzionalità chiave con cui si ha familiarità dall'portale di Azure e dal portale di registrazione delle applicazioni e migliora le operazioni tramite:

- **Migliore gestione delle app.** Invece di visualizzare le app in portali diversi, è ora possibile visualizzare tutte le app in un'unica posizione.

- **Registrazione semplificata dell'app.** Dall'esperienza di navigazione migliorata all'esperienza di selezione delle autorizzazioni rinnovata, ora è più semplice registrare e gestire le app.

- **Informazioni più dettagliate.** È possibile trovare altri dettagli sull'app, incluse le guide introduttive e altro ancora.

Per ulteriori informazioni, vedere la pagina relativa alla [piattaforma Microsoft Identity](https://docs.microsoft.com/azure/active-directory/develop/) e l' [esperienza registrazioni app è ora disponibile a livello generale.](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) annuncio del Blog.

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>Nuove funzionalità disponibili nell'API utenti a rischio per la protezione delle identità

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Identity Security e Protection

Siamo lieti di annunciare che è ora possibile usare l'API Users rischiosa per recuperare la cronologia dei rischi degli utenti, eliminare gli utenti a rischio e verificare che gli utenti siano compromessi. Questa modifica consente di aggiornare in modo più efficiente lo stato dei rischi degli utenti e di comprendere la cronologia dei rischi.

Per ulteriori informazioni, vedere la [documentazione di riferimento dell'API utenti rischiosi](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD-2019 maggio

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con soluzioni di terze parti

Nel 2019 maggio sono state aggiunte le 21 nuove app con supporto federativo per la raccolta di app:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/), [Simple Sign](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo Sales Engage](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial), [Quantum Workplace](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial), [Cobalt](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>Miglioramento della creazione e della gestione dei gruppi nel portale Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppo  
**Funzionalità del prodotto:** Collaborazione

Sono stati apportati miglioramenti alle esperienze correlate ai gruppi nel portale di Azure AD. Questi miglioramenti consentono agli amministratori di gestire più facilmente elenchi di gruppi, elenchi di membri e fornire opzioni di creazione aggiuntive.

I miglioramenti includono:

- Filtro di base per tipo di appartenenza e tipo di gruppo.

- Aggiunta di nuove colonne, ad esempio origine e indirizzo di posta elettronica.

- Possibilità di selezionare più gruppi, membri e elenchi di proprietari per un'eliminazione semplificata.

- Possibilità di scegliere un indirizzo di posta elettronica e di aggiungere proprietari durante la creazione del gruppo.

Per altre informazioni, vedere [creare un gruppo di base e aggiungere membri con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>Configurare i criteri di denominazione per i gruppi di Office 365 nel portale di Azure AD (disponibilità generale)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione gruppo  
**Funzionalità del prodotto:** Collaborazione

Gli amministratori possono ora configurare un criterio di denominazione per i gruppi di Office 365 usando il portale di Azure AD. Questa modifica consente di applicare convenzioni di denominazione coerenti per i gruppi di Office 365 creati o modificati dagli utenti dell'organizzazione.

È possibile configurare i criteri di denominazione per i gruppi di Office 365 in due modi diversi:

- Definire prefissi o suffissi, che vengono aggiunti automaticamente al nome di un gruppo.

- Caricare un set personalizzato di parole bloccate per l'organizzazione, che non sono consentite nei nomi dei gruppi (ad esempio, "CEO, Payroll, HR").

Per altre informazioni, vedere [applicare criteri di denominazione per i gruppi di Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Sono ora disponibili endpoint API Microsoft Graph per Azure AD log attività (disponibilità generale)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Report  
**Funzionalità del prodotto:** Monitoraggio e report

Siamo lieti di annunciare la disponibilità generale del supporto per gli endpoint dell'API Microsoft Graph per Azure AD log attività. Con questa versione è ora possibile usare la versione 1,0 dei log di controllo Azure AD, oltre alle API dei log di accesso.

Per altre informazioni, vedere [Azure ad Panoramica dell'API del registro di controllo](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0).

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>Gli amministratori possono ora usare l'accesso condizionale per il processo di registrazione combinato (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Identity Security e Protection  

Gli amministratori possono ora creare criteri di accesso condizionale per l'uso nella pagina di registrazione combinata. Ciò include l'applicazione di criteri per consentire la registrazione se:

- Gli utenti si trovano in una rete attendibile.

- Il rischio di accesso degli utenti è basso.

- Gli utenti si trovano su un dispositivo gestito.

- Gli utenti accettano le condizioni per l'utilizzo (TOU) dell'organizzazione.

Per altre informazioni sull'accesso condizionale e sulla reimpostazione della password, vedere il [post di Blog relativo all'accesso condizionale per l'esperienza di registrazione con autenticazione a più fattori e reimpostazione della password di Azure ad](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348) Per ulteriori informazioni sui criteri di accesso condizionale per il processo di registrazione combinato, vedere [criteri di accesso condizionale per la registrazione combinata](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration). Per ulteriori informazioni sulla funzionalità Azure AD condizioni per l'utilizzo, vedere [Azure Active Directory le condizioni](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)per l'utilizzo.

---

## <a name="april-2019"></a>Aprile 2019

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>Nuovo Azure AD rilevamento Intelligence per le minacce è ora disponibile come parte di Azure AD Identity Protection

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Identity Protection  
**Funzionalità del prodotto:** Identity Security e Protection

Azure AD rilevamento Intelligence per le minacce è ora disponibile come parte della funzionalità Azure AD Identity Protection aggiornata. Questa nuova funzionalità consente di indicare un'attività insolita dell'utente per un utente o un'attività specifica, coerente con i modelli di attacco noti basati sulle fonti di intelligence per le minacce interne ed esterne di Microsoft.

Per ulteriori informazioni sulla versione aggiornata di Azure AD Identity Protection, vedere i [quattro miglioramenti principali Azure ad Identity Protection sono ora disponibili in anteprima pubblica](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935) Blog e gli [elementi Azure Active Directory Identity Protection (aggiornati)?](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) articolo. Per ulteriori informazioni sul rilevamento Azure AD Intelligence per le minacce, vedere l'articolo relativo agli [eventi di rischio Azure Active Directory Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence) .

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>È ora disponibile la gestione dei diritti Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Identity Governance  
**Funzionalità del prodotto:** Identity Governance

Azure AD la gestione dei diritti, ora disponibile in anteprima pubblica, consente ai clienti di delegare la gestione dei pacchetti di accesso, che definisce il modo in cui i dipendenti e i partner commerciali possono richiedere l'accesso, che devono approvare e il tempo di accesso. I pacchetti di Access possono gestire l'appartenenza a gruppi di Azure AD e Office 365, assegnazioni di ruolo in applicazioni aziendali e assegnazioni di ruolo per i siti di SharePoint Online. Per altre informazioni sulla gestione dei diritti, vedere [Panoramica della gestione dei diritti Azure ad](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview). Per ulteriori informazioni sull'ampia gamma di funzionalità Azure AD Identity Governance, tra cui Privileged Identity Management, verifiche di accesso e condizioni per l'utilizzo, vedere [che cos'è Azure ad Identity governance?](../governance/identity-governance-overview.md).

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>Configurare i criteri di denominazione per i gruppi di Office 365 nel portale di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppo  
**Funzionalità del prodotto:** Collaborazione

Gli amministratori possono ora configurare un criterio di denominazione per i gruppi di Office 365 usando il portale di Azure AD. Questa modifica consente di applicare convenzioni di denominazione coerenti per i gruppi di Office 365 creati o modificati dagli utenti dell'organizzazione.

È possibile configurare i criteri di denominazione per i gruppi di Office 365 in due modi diversi:

- Definire prefissi o suffissi, che vengono aggiunti automaticamente al nome di un gruppo.

- Caricare un set personalizzato di parole bloccate per l'organizzazione, che non sono consentite nei nomi dei gruppi (ad esempio, "CEO, Payroll, HR").

Per altre informazioni, vedere [applicare criteri di denominazione per i gruppi di Office 365](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy).

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>I log attività di Azure AD sono ora disponibili in monitoraggio di Azure (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Report  
**Funzionalità del prodotto:** Monitoraggio e report

Per risolvere i commenti e i suggerimenti sulle visualizzazioni con i log attività Azure AD, viene introdotta una nuova funzionalità Insights in Log Analytics. Questa funzionalità consente di ottenere informazioni approfondite sulle risorse di Azure AD usando i modelli interattivi, denominati cartelle di lavoro. Queste cartelle di lavoro predefinite possono fornire dettagli per app o utenti e includono:

- **Accessi.** Fornisce informazioni dettagliate per app e utenti, tra cui la posizione di accesso, il sistema operativo in uso o il client e la versione del browser e il numero di accessi riusciti o non riusciti.

- **Autenticazione legacy e accesso condizionale.** Fornisce informazioni dettagliate per le app e gli utenti che usano l'autenticazione legacy, tra cui l'utilizzo dell'autenticazione a più fattori attivata dai criteri di accesso condizionale, le app che usano i criteri di accesso condizionale e così via.

- **Analisi degli errori di accesso.** Consente di determinare se gli errori di accesso si verificano a causa di un'azione dell'utente, dei problemi relativi ai criteri o dell'infrastruttura.

- **Report personalizzati.** È possibile creare nuove cartelle di lavoro o modificare quelle esistenti per personalizzare la funzionalità Insights per l'organizzazione.

Per ulteriori informazioni, vedere [come utilizzare le cartelle di lavoro di monitoraggio di Azure per Azure Active Directory report](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD-aprile 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con soluzioni di terze parti

Nel 2019 aprile sono state aggiunte le 21 nuove app con supporto federativo per la raccolta di app:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks Single Sign-On](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial), [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial), [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial), [Alibaba Cloud (Role-based SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial), [Certent Equity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial), [Sectigo Certificate Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial), [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial), [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial), [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial), [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial), [Indiggo](https://indiggolead.com/)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>Nuova opzione Frequency revisioni di accesso e selezione di più ruoli

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Identity Governance

I nuovi aggiornamenti in Azure AD le verifiche di accesso consentono di:

- Modificare la frequenza delle verifiche di accesso in modo **semestrale**, oltre alle opzioni precedentemente esistenti di ogni settimana, ogni mese, ogni trimestre e ogni anno.

- Quando si crea una singola verifica di accesso, selezionare più Azure AD e ruoli delle risorse di Azure. In questa situazione, tutti i ruoli vengono configurati con le stesse impostazioni e tutti i revisori ricevono una notifica allo stesso tempo.

Per altre informazioni su come creare una verifica di accesso, vedere [creare una verifica di accesso di gruppi o applicazioni in Azure ad verifiche di accesso](https://docs.microsoft.com/azure/active-directory/governance/create-access-review).

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect i sistemi di avvisi tramite posta elettronica sono in fase di transizione e inviano nuove informazioni sul mittente di posta elettronica per alcuni clienti

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** AD Sync  
**Funzionalità del prodotto:** Piattaforma

Azure AD Connect è in corso la transizione dei sistemi di avviso di posta elettronica, potenzialmente mostrando ad alcuni clienti un nuovo mittente di posta elettronica. Per risolvere questo problema, è necessario `azure-noreply@microsoft.com` aggiungere all'elenco Consenti dell'organizzazione oppure non sarà possibile continuare a ricevere avvisi importanti dai servizi di Office 365, Azure o Sync.

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>Le modifiche del suffisso UPN hanno ora esito positivo tra domini federati in Azure AD Connect

**Tipo:** Correzione  
**Categoria di servizio:** AD Sync  
**Funzionalità del prodotto:** Piattaforma

È ora possibile modificare correttamente il suffisso UPN di un utente da un dominio federato a un altro dominio federato in Azure AD Connect. Questa correzione significa che non si dovrebbe più riscontrare il messaggio di errore FederatedDomainChangeError durante il ciclo di sincronizzazione o ricevere un messaggio di posta elettronica di notifica, "Impossibile aggiornare questo oggetto in Azure Active Directory, perché l'attributo [ FederatedUser. UserPrincipalName], non è valido. Aggiornare il valore nei servizi directory locale ".

Per ulteriori informazioni, vedere [risoluzione degli errori durante la sincronizzazione](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror).

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>Maggiore sicurezza usando i criteri di accesso condizionale basato sulla protezione delle app in Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Identity Security e Protection

L'accesso condizionale basato sulla protezione delle app è ora disponibile usando il Richiedi criterio di **protezione delle app** . Questo nuovo criterio consente di aumentare la sicurezza dell'organizzazione evitando:

- Gli utenti ottengono l'accesso alle app senza una licenza Microsoft Intune.

- Gli utenti non sono in grado di ottenere i criteri di protezione delle app Microsoft Intune.

- Utenti che accedono alle app senza un criterio di protezione delle app configurato Microsoft Intune.

Per altre informazioni, vedere [come richiedere criteri di protezione delle app per l'accesso alle app cloud con accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access).

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Nuovo supporto per Azure AD Single Sign-on e l'accesso condizionale in Microsoft Edge (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Identity Security e Protection

Il supporto Azure AD per Microsoft Edge è stato migliorato, inclusa la fornitura di un nuovo supporto per Azure AD Single Sign-on e l'accesso condizionale. Se in precedenza è stato usato Microsoft Intune Managed Browser, ora è possibile usare Microsoft Edge.

Per altre informazioni sulla configurazione e la gestione di dispositivi e app usando l'accesso condizionale, vedere [richiedere i dispositivi gestiti per l'accesso alle app cloud con accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices) e [richiedere app client approvate per l'accesso alle app cloud con accesso condizionale ](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). Per altre informazioni su come gestire l'accesso tramite Microsoft Edge con i criteri di Microsoft Intune, vedere [gestire l'accesso a Internet tramite un browser protetto da criteri Microsoft Intune](https://docs.microsoft.com/intune/app-configuration-managed-browser).

---

## <a name="march-2019"></a>Marzo 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Framework dell'esperienza di identità e supporto dei criteri personalizzati in Azure Active Directory B2C è ora disponibile (GA)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile creare criteri personalizzati in Azure AD B2C, incluse le attività seguenti, che sono supportate su vasta scala e con il contratto di licenza di Azure:

- Creare e caricare percorsi utente di autenticazione personalizzati usando criteri personalizzati.

- Descrivere in modo dettagliato i percorsi utente come scambi tra provider di attestazioni.

- Definire la diramazione condizionale nei percorsi utente.

- Trasformare ed eseguire il mapping delle attestazioni per l'uso in decisioni e comunicazioni in tempo reale.

- Usare i servizi abilitati per l'API REST nei percorsi utente di autenticazione personalizzati. Ad esempio, con provider di posta elettronica, dispongono e sistemi di autorizzazione proprietari.

- Federazione con provider di identità che sono conformi al protocollo OpenIDConnect. Ad esempio, con Azure AD multi-tenant, provider di account di social networking o provider di verifica a due fattori.

Per altre informazioni sulla creazione di criteri personalizzati, vedere [Note per gli sviluppatori per i criteri personalizzati in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) e leggere [il post di Blog di Alex Simon, inclusi i case study](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nuove app federate disponibili nella raccolta di app Azure AD-2019 marzo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con soluzioni di terze parti

Nel 2019 marzo sono state aggiunte le 14 nuove app con supporto federativo per la raccolta di app:

[ISEC7 mobile Exchange delegate](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [sistema di controllo basato su spiegazione](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [PowerSchool performance Matters](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode ](https://cinode.com/), [Intranet Iris](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit Horizons](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nuovi connettori di provisioning di zScaler e Atlassian nella raccolta di Azure AD-2019 marzo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con soluzioni di terze parti

Automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per le app seguenti:

[ZScaler](https://aka.ms/ZscalerProvisioning), [zScaler beta](https://aka.ms/ZscalerBetaProvisioning), [zScaler One](https://aka.ms/ZscalerOneProvisioning), [zScaler Two](https://aka.ms/ZscalerTwoProvisioning), [zScaler Three](https://aka.ms/ZscalerThreeProvisioning), [zScaler](https://aka.ms/ZscalerZSCloudProvisioning)ZSCloud, [Atlassian cloud](https://aka.ms/atlassianCloudProvisioning)

Per altre informazioni su come proteggere meglio l'organizzazione tramite il provisioning automatizzato degli account utente, vedere automatizzare il provisioning [degli utenti in applicazioni SaaS con Azure ad](https://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Ripristinare e gestire i gruppi di Office 365 eliminati nel portale di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione gruppo  
**Funzionalità del prodotto:** Collaborazione

È ora possibile visualizzare e gestire i gruppi di Office 365 eliminati dal portale di Azure AD. Questa modifica consente di visualizzare i gruppi disponibili per il ripristino, oltre a consentire l'eliminazione definitiva dei gruppi che non sono necessari per l'organizzazione.

Per ulteriori informazioni, vedere la pagina relativa al [ripristino di gruppi scaduti o eliminati](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Single Sign-on è ora disponibile per Azure AD app locali protette con SAML tramite il proxy di applicazione (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

È ora possibile fornire un'esperienza Single Sign-on (SSO) per le app locali con autenticazione SAML, oltre ad accedere in remoto a queste app tramite il proxy di applicazione. Per altre informazioni su come configurare l'accesso SSO SAML con le app locali, vedere [SAML Single Sign-on for on-premises application proxy (anteprima)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Le app client nei cicli di richiesta verranno interrotte per migliorare l'affidabilità e l'esperienza utente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

Le app client possono emettere in modo errato centinaia delle stesse richieste di accesso in un breve periodo di tempo. Queste richieste, a prescindere dal fatto che abbiano esito positivo o negativo, contribuiscono a una scarsa esperienza utente e a carichi di lavoro accresciuti per l'IDP, aumentando la latenza per tutti gli utenti e riducendo la disponibilità dell'IDP.

Questo aggiornamento Invia un `invalid_grant` errore: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` alle app client che rilasciano richieste duplicate più volte in un breve periodo di tempo, oltre l'ambito del normale funzionamento. Le app client che rilevano questo problema dovrebbero visualizzare un prompt interattivo, richiedendo all'utente di eseguire di nuovo l'accesso. Per ulteriori informazioni su questa modifica e su come correggere l'applicazione se si verifica questo errore, vedere [What ' s New for Authentication?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>È ora disponibile una nuova esperienza utente per i log di controllo

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Report  
**Funzionalità del prodotto:** Monitoraggio e report

È stata creata una nuova pagina di **log di controllo** Azure ad per contribuire a migliorare la leggibilità e la modalità di ricerca delle informazioni. Per visualizzare la pagina nuovi **log di controllo** , selezionare log di **controllo** nella sezione **attività** di Azure ad.

![Pagina nuovi log di controllo con informazioni di esempio](media/whats-new/audit-logs-page.png)

Per ulteriori informazioni sulla nuova pagina **log di controllo** , vedere [report attività di controllo nel portale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nuovi avvisi e indicazioni per impedire il blocco accidentale degli amministratori da criteri di accesso condizionale non configurati correttamente

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Identity Security e Protection

Per impedire agli amministratori di bloccarsi accidentalmente dai propri tenant tramite criteri di accesso condizionale configurati in modo non conforme, sono stati creati nuovi avvisi e indicazioni aggiornate nella portale di Azure. Per ulteriori informazioni sulle nuove linee guida, vedere [che cosa sono le dipendenze del servizio in Azure Active Directory l'accesso condizionale](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Miglioramento delle condizioni per l'utilizzo degli utenti finali nei dispositivi mobili

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance

Sono state aggiornate le condizioni per l'utilizzo esistenti per contribuire a migliorare la modalità di revisione e consenso alle condizioni per l'utilizzo in un dispositivo mobile. È ora possibile eseguire lo zoom avanti e indietro, tornare indietro, scaricare le informazioni e selezionare collegamenti ipertestuali. Per ulteriori informazioni sulle condizioni per l'utilizzo aggiornate, vedere [Azure Active Directory la funzionalità condizioni](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)per l'utilizzo.

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nuova esperienza di download dei log attività Azure AD disponibile

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Report  
**Funzionalità del prodotto:** Monitoraggio e report

È ora possibile scaricare grandi quantità di log attività direttamente dalla portale di Azure. Questo aggiornamento consente di:

- Scaricare fino a 250.000 righe.

- Ricevere una notifica al termine del download.

- Personalizzare il nome del file.

- Determinare il formato di output, ovvero JSON o CSV.

Per ulteriori informazioni su questa funzionalità, vedere [Guida introduttiva: Scaricare un report di controllo utilizzando il portale di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Modifica che causa un'interruzione: Aggiornamenti alla valutazione della condizione di Exchange ActiveSync (EAS)

**Tipo:** Modifica prevista  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Controllo di accesso

È in corso l'aggiornamento del modo in cui Exchange ActiveSync (EAS) valuta le condizioni seguenti:

- Località utente, in base a paese, area geografica o indirizzo IP

- Rischio di accesso

- Piattaforma del dispositivo

Se queste condizioni sono state usate in precedenza nei criteri di accesso condizionale, tenere presente che il comportamento della condizione potrebbe cambiare. Ad esempio, se in precedenza è stata usata la condizione posizione utente in un criterio, è possibile che i criteri vengano ignorati in base alla posizione dell'utente.

---

## <a name="february-2019"></a>Febbraio 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Crittografia del token SAML di Azure AD configurabile (anteprima pubblica) 

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

È ora possibile configurare qualsiasi app SAML supportata per ricevere i token SAML crittografati. Se configurata e usata con un'app, Azure AD crittografa le asserzioni SAML emesse usando una chiave pubblica ottenuta da un certificato archiviato nel Azure AD.

Per altre informazioni sulla configurazione della crittografia del token SAML, vedere [configurare Azure ad crittografia dei token SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Creazione di una verifica di accesso per gruppi o app con Verifiche di accesso di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance

È ora possibile includere più gruppi o app in una singola Azure AD verifica di accesso per l'appartenenza al gruppo o l'assegnazione di app. Le verifiche di accesso con più gruppi o app sono configurate con le stesse impostazioni e tutti i revisori inclusi ricevono una notifica allo stesso tempo.

Per altre informazioni su come creare una verifica di accesso usando le verifiche di accesso Azure AD, vedere [creare una verifica di accesso di gruppi o applicazioni in verifiche di accesso Azure ad](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Febbraio 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con soluzioni di terze parti
 
Nel febbraio 2019 sono state aggiunte le 27 nuove app con supporto federativo per la raccolta di app:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Permission Click, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [Seismic](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Share A Dream](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Knowledge Anywhere LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [Periscope Data](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [Netop Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp Productivity Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Miglioramento della registrazione combinata per MFA/Reimpostazione della password self-service

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Reimpostazione password self-service  
**Funzionalità del prodotto:** Autenticazione utente

In risposta ai commenti dei clienti, è stata migliorata l'esperienza combinata di anteprima della registrazione di multi-factor authentication/SSPR, che consente agli utenti di registrare più rapidamente le info di sicurezza per l'autenticazione a più fattori e SSPR. 

**Per abilitare l'esperienza avanzata per gli utenti, seguire questa procedura:**

1. In qualità di amministratore globale o Amministratore utenti, accedere al portale di Azure e passare a **Azure Active Directory > impostazioni utente > Gestisci impostazioni per le funzionalità di anteprima del pannello di accesso**. 

2. Nell'opzione **utenti che possono usare le funzionalità di anteprima per la registrazione e la gestione delle informazioni di sicurezza-Aggiorna** scegliere di attivare le funzionalità per un **gruppo selezionato di utenti** o per **tutti gli utenti**.

Nelle prossime settimane verrà rimossa la possibilità di abilitare l'esperienza di anteprima della registrazione di autenticazione a più fattori per i tenant che non hanno già attivato.

**Per verificare se il controllo verrà rimosso per il tenant, attenersi alla procedura seguente:**

1. In qualità di amministratore globale o Amministratore utenti, accedere al portale di Azure e passare a **Azure Active Directory > impostazioni utente > Gestisci impostazioni per le funzionalità di anteprima del pannello di accesso**.  

2. Se gli **utenti che possono usare l'opzione Anteprima funzionalità per la registrazione e la gestione delle informazioni di sicurezza** sono impostati su **nessuno**, l'opzione verrà rimossa dal tenant.

A prescindere dal fatto che in precedenza sia stata attivata l'esperienza di anteprima della registrazione di multi-factor authentication/SSPR precedente per gli utenti, l'esperienza precedente verrà disattivata in una data futura. Per questo motivo, è consigliabile passare alla nuova esperienza migliorata il prima possibile.

Per ulteriori informazioni sull'esperienza di registrazione avanzata, vedere i miglioramenti apportati [alla Azure ad esperienza combinata di registrazione a più fattori e reimpostazione della password](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Aggiornamento dell'esperienza di gestione dei criteri per i flussi utente

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

È stato aggiornato il processo di creazione e gestione dei criteri per i flussi utente (noti in precedenza come criteri predefiniti). Questa nuova esperienza è ora l'impostazione predefinita per tutti i tenant di Azure AD.

È possibile fornire commenti e suggerimenti aggiuntivi usando le Icone Smile o cipiglio nell'area **inviare commenti** e suggerimenti nella parte superiore della schermata del portale.

Per ulteriori informazioni sulla nuova esperienza di gestione dei criteri, vedere il [Azure ad B2C ora dispone di personalizzazione JavaScript e di molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) Blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Scelta di versioni specifiche degli elementi della pagina fornite da Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile scegliere una versione specifica degli elementi della pagina forniti da Azure AD B2C. Selezionando una versione specifica, è possibile testare gli aggiornamenti prima che vengano visualizzati in una pagina ed è possibile ottenere un comportamento prevedibile. Inoltre, è ora possibile acconsentire esplicitamente a applicare versioni specifiche della pagina per consentire le personalizzazioni di JavaScript. Per attivare questa funzionalità, passare alla pagina delle **Proprietà** nei flussi utente.

Per ulteriori informazioni sulla scelta di versioni specifiche degli elementi della pagina, vedere il [Azure ad B2C ora dispone di personalizzazione JavaScript e di molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) Blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisiti configurabili per le password degli utenti finali per B2C (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile configurare la complessità delle password dell'organizzazione per gli utenti finali, anziché usare i criteri di Azure AD password nativi. Dal pannello delle **Proprietà** dei flussi utente, noti in precedenza come criteri predefiniti, è possibile scegliere una complessità della password **semplice** o **affidabile**oppure creare un set **personalizzato** di requisiti.

Per ulteriori informazioni sulla configurazione dei requisiti di complessità delle password, vedere [configurare i requisiti di complessità per le password in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nuovi modelli predefiniti per esperienze di autenticazione personalizzate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

È possibile usare i nuovi modelli predefiniti, situati nel pannello **layout pagina** dei flussi utente (noti in precedenza come criteri predefiniti), per creare un'esperienza di autenticazione personalizzata per gli utenti.

Per ulteriori informazioni sull'utilizzo dei modelli, vedere [Azure ad B2C ora dispone di personalizzazione JavaScript e di molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---
