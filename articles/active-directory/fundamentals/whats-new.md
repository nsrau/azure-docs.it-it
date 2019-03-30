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
ms.date: 02/28/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b56ad57255f07c93c1bf5f981db07ad4ba57806d
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58665662"
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory

>Ricevere notifiche sulle situazioni in cui visualizzare nuovamente questa pagina per gli aggiornamenti da copiare e incollare questo URL: `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` nella ![icona lettore del feed RSS](./media/whats-new/feed-icon-16x16.png) lettore di feed.

Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente. Se si cercano informazioni precedenti agli ultimi sei mesi, è possibile trovarli in [Archivio per le novità in Azure Active Directory](whats-new-archive.md).

---

## <a name="march-2019"></a>Marzo 2019

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Framework dell'esperienza di identità e criteri personalizzati supporto in Azure Active Directory B2C è ora disponibile (GA)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile creare i criteri personalizzati in Azure AD B2C, incluse le attività seguenti, che sono supportate su larga scala e nel nostro contratto di servizio di Azure:

- Creare e caricare percorsi utente di autenticazione personalizzati usando criteri personalizzati.

- Descrivere in modo dettagliato i percorsi utente come scambi tra provider di attestazioni.

- Definire la diramazione condizionale nei percorsi utente.

- Trasformare ed eseguire il mapping delle attestazioni per prendere decisioni in tempo reale e le comunicazioni.

- Utilizzare i servizi abilitati per API REST nei percorsi utente di autenticazione personalizzato. Ad esempio, con i provider di posta elettronica, durevolezza e sistemi di autorizzazione proprietario.

- Attuare la federazione con provider di identità che sono conformi con il protocollo openid Connect. Ad esempio, con multi-tenant di Azure AD, provider di account basati su social network o provider di verifica a due fattori.

Per altre informazioni sulla creazione di criteri personalizzati, vedere [note degli sviluppatori per i criteri personalizzati in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom) e leggere [post di blog di Alex Simon, inclusi i case study](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - marzo 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti

In marzo 2019, sono state aggiunte che queste 14 nuove app con la federazione di supporto per la raccolta di app:

[Delegato di Exchange per dispositivi mobili ISEC7](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial), [Spiegazione in base al sistema di controllo](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial), [Lean](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool prestazioni questioni](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit orizzonti](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [VITÀ](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Nuovo Zscaler e Atlassian nella raccolta di Azure AD - marzo 2019 i connettori di provisioning

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti

Automatizzare la creazione, aggiornamento ed eliminazione di account utente per le app seguenti:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](http://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler Two](http://aka.ms/ZscalerTwoProvisioning), [Zscaler tre](http://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud ](http://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](http://aka.ms/atlassianCloudProvisioning)

Per altre informazioni su come proteggere l'organizzazione tramite il provisioning degli account utente automatico, vedere [automatizza il provisioning utenti in applicazioni SaaS con Azure AD](http://aka.ms/ProvisioningDocumentation).

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>Ripristinare e gestire i gruppi di Office 365 eliminati nel portale di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Gestione di gruppi  
**Funzionalità del prodotto:** Collaborazione

È ora possibile visualizzare e gestire i gruppi di Office 365 eliminati dal portale di Azure AD. Questa modifica consente di visualizzare i gruppi a cui sono disponibili per il ripristino, insieme a cui è possibile in modo permanente, eliminare tutti i gruppi necessari dall'organizzazione.

Per altre informazioni, vedere [ripristino scaduto o i gruppi eliminati](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore).

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>Accesso Single sign-on è ora disponibile per le app protette con Azure AD SAML in locale tramite il Proxy di applicazione (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy dell'app  
**Funzionalità del prodotto:** Controllo dell’accesso

È ora possibile fornire un'esperienza single sign-on (SSO) in locale, le app con autenticazione SAML, oltre all'accesso remoto a queste App tramite il Proxy di applicazione. Per altre informazioni sull'impostazione di SAML SSO con le App in locale, vedere [SAML single sign-on per applicazioni locali con il Proxy di applicazione (anteprima)](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps).

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>Le app client in cicli di richiesta verranno interrotto per migliorare l'affidabilità e l'esperienza utente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione dell'utente

Le app client in modo non corretto possono rilasciare centinaia di richieste di accesso stesso in un breve periodo di tempo. Queste richieste, sia che si ha esito positivo o non, contribuiscono a un'esperienza utente misera e cogliere nuove opportunità di carichi di lavoro per il provider di identità, aumentare la latenza per tutti gli utenti e ridurre la disponibilità del provider di identità.

Questo aggiornamento invia un' `invalid_grant` errori: `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` alle App client che inviano richieste di duplicazione più volte in un breve periodo di tempo, esula dall'ambito del normale funzionamento. Le app client che si verificano questo problema dovrebbe essere visualizzato un prompt interattivo, richiedendo all'utente di accedere di nuovo. Per altre informazioni su questa modifica e su come correggere l'app se si verifica questo errore, vedere [nuove funzionalità per l'autenticazione?](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted).

---

### <a name="new-audit-logs-user-experience-now-available"></a>Nuova esperienza utente i log di controllo è ora disponibile

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Abbiamo creato un nuovo Azure AD **log di controllo** pagina per contribuire a migliorare la leggibilità e come cercare le informazioni. Per vedere le nuove **log di controllo** pagina, selezionare **log di controllo** nel **attività** sezione di Azure Active Directory.

![Nuova pagina di log di controllo, con informazioni di esempio](media/whats-new/audit-logs-page.png)

Per altre informazioni sulla nuova **log di controllo** pagina, vedere [report di controllo attività nel portale di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs).

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>Nuovi avvisi e indicazioni per consentire di evitare il blocco amministratore accidentale da criteri di accesso condizionale configurati in modo errati

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Per evitare che gli amministratori che bloccano accidentalmente i propri tenant mediante criteri di accesso condizionale configurati in modo errati, abbiamo creato indicazioni aggiornate e nuovi avvisi nel portale di Azure. Per altre informazioni sulle nuove informazioni, vedere [quali sono le dipendenze dei servizi nell'accesso condizionale di Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies).

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>Condizioni per l'utente finale migliorata delle esperienze di utilizzo nei dispositivi mobili

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance

Sono state aggiornate le condizioni esistenti delle esperienze di utilizzo per contribuire a migliorare come rivedere e fornire il consenso alle condizioni d'uso in un dispositivo mobile. È ora possibile eseguire lo zoom avanti e indietro, tornare indietro, scaricare le informazioni e selezionare i collegamenti ipertestuali. Per altre informazioni sui termini di utilizzo aggiornati, vedere [Azure Active Directory condizioni per l'uso funzionalità](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#what-terms-of-use-looks-like-for-users).

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>Nuovi log attività di Azure AD di download disponibili

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

È ora possibile scaricare grandi quantità di log attività direttamente dal portale di Azure. Questo aggiornamento consente di:

- Scaricare fino a 250.000 righe.

- Ricevi una notifica al completamento del download.

- Personalizzare il nome del file.

- Determinare il formato di output, JSON o CSV.

Per altre informazioni su questa funzionalità, vedere [Guida introduttiva: Scaricare un report di controllo nel portale di Azure](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>Modifica che causa un'interruzione: Aggiornamenti alla valutazione della condizione da Exchange ActiveSync (EAS)

**Tipo:** Modifica prevista  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Controllo dell’accesso

È in corso l'aggiornamento come Exchange ActiveSync (EAS) valuta le condizioni seguenti:

- Posizione dell'utente, basato sul paese, area o indirizzo IP

- Rischio di accesso

- Piattaforma del dispositivo.

Se è stata usata in precedenza queste condizioni nei criteri di accesso condizionale, tenere presente che il comportamento di condizione potrebbero subire modifiche. Ad esempio, se la condizione di percorso utente è stato usato in precedenza in un criterio, si noterà il criterio ora verrà ignorato in base alla posizione dell'utente.

---

## <a name="february-2019"></a>Febbraio 2019

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>Crittografia del token SAML di Azure AD configurabile (anteprima pubblica) 

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

È ora possibile configurare qualsiasi app SAML supportati per ricevere i token SAML crittografati. Quando configurato e usato con un'app, Azure AD consente di crittografare le asserzioni SAML generate usando una chiave pubblica ottenuta da un certificato archiviato in Azure AD.

Per altre informazioni sulla configurazione di crittografia di token SAML, vedere [crittografia di token SAML in Azure AD configurare](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption).

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>Creazione di una verifica di accesso per gruppi o app con Verifiche di accesso di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Verifiche di accesso  
**Funzionalità del prodotto:** Governance

È ora possibile includere più gruppi o le App in un singolo di Azure AD di accedere alle revisione per l'appartenenza al gruppo o l'assegnazione di app. Verifiche di accesso con più gruppi o le app vengono configurate usando le stesse impostazioni e tutti i revisori inclusi vengono informati allo stesso tempo.

Per altre informazioni su come creare una verifica di accesso usando le verifiche di accesso di Azure AD, vedere [creare una verifica di accesso di gruppi o applicazioni nelle verifiche di accesso di Azure AD](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Febbraio 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel febbraio 2019, sono state aggiunte che supportano questi 27 nuove app con la federazione alla raccolta di app:

[Passport Euromonitor](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [accidentali](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [ IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Qmlativ Skyward](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [SSO Soloinsight CloudGate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial), Autorizzazione clic [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial), [radicali ](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial), [Condivisione un sogno](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [WebMethod Cloud Integration](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [Knowledge ovunque LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial), [OU Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial), [dati Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial), [portale Netop](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial), [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial), [PureCloud da Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial), [ClickUp piattaforma di produttività](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="enhanced-combined-mfasspr-registration"></a>Miglioramento della registrazione combinata per MFA/Reimpostazione della password self-service

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Reimpostazione della password self-service  
**Funzionalità del prodotto:** Autenticazione dell'utente
 
In risposta ai commenti dei clienti, è stato migliorato l'esperienza di anteprima di registrazione MFA/reimpostazione combinato, consentendo agli utenti di registrare più rapidamente le tue info di sicurezza per MFA e per SSPR. 

**Per attivare subito l'esperienza ottimizzata per gli utenti, seguire questa procedura:**

1. Come amministratore globale o amministratore dell'utente, accedere al portale di Azure e passare a **Azure Active Directory > impostazioni utente > gestire le impostazioni per le funzionalità di anteprima di Pannello di accesso**. 

2. Nel **gli utenti che possono usare l'anteprima funzionalità per la registrazione e la gestione delle tue info di sicurezza: aggiornare** opzione, scegliere di attivare le funzionalità per un **gruppo selezionato di utenti** o per **tutti gli utenti** .

Nelle poche settimane, verrà rimosso la possibilità di attivare il vecchio combinato MFA/reimpostazione anteprima esperienza di registrazione per i tenant che ha già attivato.

**Per vedere se il controllo verrà rimossa per il tenant, seguire questa procedura:**

1. Come amministratore globale o amministratore dell'utente, accedere al portale di Azure e passare a **Azure Active Directory > impostazioni utente > gestire le impostazioni per le funzionalità di anteprima di Pannello di accesso**.  

2.  Se il **utenti che possono usare le funzionalità di anteprima per la registrazione e la gestione delle tue info di sicurezza** opzione è impostata su **None**, l'opzione verrà rimosso dal tenant.

Indipendentemente dal fatto se ha attivato in precedenza la precedente registrazione MFA/reimpostazione combinata funzionalità di anteprima per gli utenti o non, verrà disattivata la vecchia esperienza in una data futura. Per questo motivo, è consigliabile che passare alla nuova e migliorata esperienza appena possibile.

Per altre informazioni sull'esperienza di registrazione avanzata, vedere la [miglioramenti ad accesso sporadico ad Azure AD combinati autenticazione a più fattori e l'esperienza di registrazione di reimpostazione della password](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271).

---

### <a name="updated-policy-management-experience-for-user-flows"></a>Aggiornamento dell'esperienza di gestione dei criteri per i flussi utente

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

Abbiamo aggiornato il processo di creazione e la gestione dei criteri per i flussi degli utenti (in precedenza incorporati, noti come criteri) più semplice. Questa nuova esperienza è ora il valore predefinito per tutti i tenant di Azure AD.

È possibile fornire altri commenti e suggerimenti usando smile o Invia faccia imbronciata icone nel **inviare commenti e suggerimenti** area nella parte superiore della schermata del portale.

Per altre informazioni sulla nuova esperienza di gestione dei criteri, vedere la [Azure AD B2C dispone ora di personalizzazione di JavaScript e molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>Scelta di versioni specifiche degli elementi della pagina fornite da Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile scegliere una versione specifica di elementi della pagina fornite da Azure AD B2C. Se si seleziona una versione specifica, è possibile testare gli aggiornamenti prima vengono visualizzati in una pagina ed è possibile ottenere un comportamento prevedibile. Inoltre, è possibile ora acconsentire per applicare versioni pagina specifica per consentire le personalizzazioni di JavaScript. Per attivare questa funzionalità, vedere la **proprietà** pagina nei flussi utente.

Per altre informazioni sulla scelta di versioni specifiche di elementi della pagina, vedere la [Azure AD B2C dispone ora di personalizzazione di JavaScript e molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595) blog.

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>Requisiti configurabili per le password degli utenti finali per B2C (disponibilità generale)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

È ora possibile impostare la complessità delle password nell'organizzazione per gli utenti finali, anziché utilizzare il nativo dei criteri password di Azure AD. Dal **delle proprietà** pannello dell'utente di flussi (precedentemente noti come i criteri predefiniti), è possibile scegliere la complessità delle password di **semplice** o **sicuro**, oppure è possibile creare un **Custom** set di requisiti.

Per altre informazioni sulla configurazione del requisito complessità password, vedere [configurare i requisiti di complessità delle password in Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity).

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>Nuovi modelli predefiniti per esperienze di autenticazione personalizzate

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Consumer Identity Management  
**Funzionalità del prodotto:** B2B/B2C

È possibile usare i nuovi modelli predefiniti, che si trova nel **il layout di pagina** pannello dei flussi utente (precedentemente noto come criteri predefiniti), per creare un oggetto personalizzato con marchio di esperienza di autenticazione per gli utenti.

Per altre informazioni sull'uso dei modelli, vedere [Azure AD B2C dispone ora di personalizzazione di JavaScript e molte altre nuove funzionalità](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595).

---

## <a name="january-2019"></a>Gennaio 2019

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>Collaborazione B2B di Active Directory tramite l'autenticazione con passcode monouso (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C

È stata introdotta l'autenticazione con passcode monouso (OTP) per autenticare gli utenti guest B2B che non possono essere autenticati tramite altri mezzi quali Azure AD, un account Microsoft (account del servizio gestito) o la federazione di Google. Con questo nuovo metodo di autenticazione, gli utenti guest non devono creare un nuovo account Microsoft. Durante il riscatto di un invito o l'accesso a una risorsa condivisa, un utente guest può invece richiedere l'invio di un codice temporaneo a un indirizzo di posta elettronica. Usando questo codice temporaneo, l'utente guest può continuare ad accedere.

Per altre informazioni, vedere [Autenticazione con passcode monouso tramite indirizzo di posta elettronica (anteprima)](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode) e il blog [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949) (Azure AD semplifica le condivisioni e le collaborazioni per qualsiasi utente con qualsiasi account).

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>Nuove impostazioni dei cookie di Azure AD Application Proxy

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy dell'app  
**Funzionalità del prodotto:** Controllo dell’accesso

Sono state introdotte tre nuove impostazioni dei cookie, disponibili per le app pubblicate tramite il proxy di applicazione:

- **Usa un cookie di tipo solo HTTP.** Consente di impostare il flag **HTTPOnly** nei cookie di accesso e di sessione per il proxy di applicazione. L'attivazione di questa impostazione offre vantaggi aggiuntivi per la sicurezza, tra cui la possibilità di evitare la copia o la modifica dei cookie tramite script lato client. È consigliabile attivare questo flag (scegliere **Sì**) per ottenere i vantaggi aggiuntivi.

- **Usa un cookie sicuro.** Consente di impostare il flag **Secure** nei cookie di accesso e di sessione per il proxy di applicazione. L'attivazione di questa impostazione offre vantaggi aggiuntivi per la sicurezza, assicurando che i cookie vengano trasmessi solo su canali TLS sicuri, ad esempio HTTPS. È consigliabile attivare questo flag (scegliere **Sì**) per ottenere i vantaggi aggiuntivi.

- **Usa un cookie permanente.** Consente di impedire la scadenza dei cookie di accesso alla chiusura del Web browser. Questi cookie rimangono validi per l'intera durata del token di accesso. I cookie vengono tuttavia reimpostati se viene raggiunta la scadenza o se l'utente elimina manualmente il cookie. È consigliabile mantenere l'impostazione predefinita **No**, attivando l'impostazione solo per app meno recenti che non condividono cookie tra i processi.

Per altre informazioni sui nuovi cookie, vedere [Impostazioni dei cookie per l'accesso alle applicazioni locali in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Gennaio 2019

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di gennaio 2019, alla raccolta di app sono state aggiunte queste 35 nuove app con il supporto della federazione:

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial), [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial), [Talent tavolozza](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial), [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial), [Cisco ombrello](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial), [Zscaler Amministratore di accesso Internet](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial), [promemoria di scadenza](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial), [InstaVR Visualizzatore](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial), [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial), [verbo](https://app.verb.net/login), [OpenLattice](https://openlattice.com/agora), [TheOrgWiki](https://www.theorgwiki.com/signup), [Pavaso Digital chiudere](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial), [GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial), [ Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial), [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial), [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial), [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial), [CallPlease](https://webapp.callplease.com/create-account/create-account.html), [Sistema SSO GTNexus](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial), [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial), [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial), [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial), [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial), [Are for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial), [K2 per Office 365](https://www.k2.com/O365), [Xledger](https://www.xledger.net/), [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial), [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial), [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial), [Korn portare ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial), [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial), [Adoddle cSaas piattaforma](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>Nuovi miglioramenti ad Azure AD Identity Protection (anteprima pubblica)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

All'offerta di anteprima pubblica di Azure AD Identity Protection sono stati aggiunti i miglioramenti seguenti:

- Interfaccia utente aggiornata e più integrata

- API aggiuntive

- Valutazione migliorata dei rischi tramite Machine Learning

- Allineamento a livello di prodotto per utenti e accessi rischiosi

Per altre informazioni sui miglioramenti, vedere [Informazioni su Azure Active Directory Identity Protection (versione aggiornata)](https://aka.ms/IdentityProtectionDocs). È possibile ottenere informazioni aggiuntive e condividere le proprie opinioni tramite i prompt nel prodotto.

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>Nuova funzionalità di blocco dell'app per l'app Microsoft Authenticator in dispositivi iOS e Android

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App Microsoft Authenticator  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Per rendere più sicuri i passcode monouso, le informazioni sull'app e le impostazioni dell'app, è possibile attivare la funzionalità di blocco dell'app nell'app Microsoft Authenticator. Attivando il blocco dell'app verrà chiesto di eseguire l'autenticazione usando il PIN o la biometria ogni volta che si apre l'app Microsoft Authenticator.

Per altre informazioni, vedere [Domande frequenti sull'app Microsoft Authenticator](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq).

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>Miglioramenti alle funzionalità di esportazione di Azure AD Privileged Identity Management (PIM)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management

Gli amministratori di Privileged Identity Management (PIM) possono ora esportare tutte le assegnazioni di ruolo attive e idonee per una risorsa specifica, incluse le assegnazioni di ruolo per tutte le risorse figlio. In precedenza risultava difficile per gli amministratori ottenere un elenco completo di assegnazioni di ruolo per una sottoscrizione ed era necessario esportare le assegnazioni di ruolo per ogni risorsa specifica.

Per altre informazioni, vedere [Visualizzare la cronologia delle attività e dei controlli per i ruoli delle risorse di Azure in PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac).

---

## <a name="novemberdecember-2018"></a>Novembre/Dicembre 2018

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>Gli utenti non rimossi dall'ambito di sincronizzazione non passano più ad account di tipo solo cloud

**Tipo:** Correzione  
**Categoria di servizio:** User Management  
**Funzionalità del prodotto:** Directory

>[!Important]
>Microsoft ha ascoltato e compreso i commenti di insoddisfazione dei clienti in merito a questa correzione. Ha quindi ripristinato lo stato antecedente alla modifica finché non sarà più facile per i clienti implementare questa correzione nella loro organizzazione.

È stato risolto un bug a causa del quale il flag DirSyncEnabled di un utente viene erroneamente impostato su **False** quando l'oggetto Active Directory Domain Services viene escluso dall'ambito di sincronizzazione e quindi spostato nel Cestino in Azure AD durante il ciclo di sincronizzazione successivo. In seguito a questa correzione, se l'utente viene escluso dall'ambito di sincronizzazione e successivamente ripristinato dal Cestino di Azure AD, l'account utente resta sincronizzato da AD locale come previsto e non può essere gestito nel cloud poiché la relativa origine di autorità rimane AD locale.

Prima di questa correzione si verificava un problema quando il flag DirSyncEnabled veniva impostato su False. Veniva data l'erronea l'impressione che questi account fossero stati convertiti in oggetti solo cloud e potessero essere gestiti solo nel cloud. Tuttavia, gli account mantenevano l'origine di autorità come locale e tutte le proprietà sincronizzate (attributi shadow) provenienti da AD locale. Questa condizione causava vari problemi in Azure AD e in altri carichi di lavoro cloud, ad esempio Exchange Online, che prevedevano di gestire questi account come sincronizzati da AD, ma che si comportavano ora come account solo cloud.

Attualmente, l'unico modo per convertire realmente un account sincronizzato da AD in un account solo cloud è disabilitare DirSync a livello di tenant, attivando così un'operazione di back-end per trasferire l'origine di autorità. Questo tipo di modifica dell'origine di autorità richiede, tra l'altro, la pulizia di tutti gli attributi locali correlati (ad esempio LastDirSyncTime e gli attributi shadow) e l'invio di un segnale ad altri carichi di lavoro cloud perché convertano anch'essi il rispettivo oggetto in un account solo cloud.

Questa correzione, di conseguenza, consente di evitare gli aggiornamenti diretti dell'attributo ImmutableID di un utente sincronizzato da AD, che in precedenza erano necessari in alcuni scenari. Per impostazione predefinita, l'attributo ImmutableID di un oggetto in Azure AD, come suggerisce il nome, deve essere immutabile. Per questi scenari sono disponibili nuove funzionalità implementate in Azure AD Connect Health e nel client di sincronizzazione di Azure AD Connect:

- **Aggiornamento di ImmutableID su larga scala per molti utenti in un approccio a fasi**
  
  Ad esempio, è necessario eseguire una lunga migrazione tra foreste di Active Directory Domain Services. Soluzione: Usare Azure AD Connect per **Configurare l'ancoraggio di origine** e, quando viene completata la migrazione dell'utente, copiare i valori di ImmutableID esistenti da Azure AD nell'attributo ms-DS-Consistency-Guid dell'utente di Active Directory Domain Services locale della nuova foresta. Per altre informazioni, vedere [Uso di ms-DS-ConsistencyGuid come sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor).

- **Aggiornamenti di ImmutableID su larga scala per molti utenti in un unico passaggio**

  Ad esempio, se durante l'implementazione di Azure AD Connect si commette un errore ed è necessario modificare l'attributo SourceAnchor. Soluzione: Disabilitare DirSync a livello di tenant e cancellare tutti i valori di ImmutableID non validi. Per altre informazioni, vedere [Disabilitare la sincronizzazione della directory per Office 365](/office365/enterprise/turn-off-directory-synchronization).

- **Abbinare di nuovo un utente locale e un utente esistente in Azure AD** Ad esempio, un utente che è stato ricreato in Active Directory Domain Services genera un duplicato nell'account Azure AD anziché essere abbinato di nuovo a un account Azure AD esistente (oggetto orfano). Soluzione: Usare Azure AD Connect Health nel portale di Azure per eseguire di nuovo il mapping di ancoraggio di origine o ImmutableID. Per altre informazioni, vedere [Scenario con oggetto orfano](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario).

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>Modifica di rilievo: aggiornamenti apportati allo schema dei log di controllo e di accesso tramite Monitoraggio di Azure

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione di report

Sono attualmente in fase di pubblicazione flussi di log di controllo e accesso tramite Monitoraggio di Azure, per poter integrare uniformemente i file di log con gli strumenti di informazioni di sicurezza e gestione degli eventi o con Log Analytics. In base ai commenti e suggerimenti degli utenti e in preparazione all'annuncio della disponibilità generale di questa funzionalità, Microsoft sta apportando le modifiche seguenti allo schema. Queste modifiche dello schema e i relativi aggiornamenti alla documentazione verranno completati entro la prima settimana di gennaio.

#### <a name="new-fields-in-the-audit-schema"></a>Nuovi campi nello schema di controllo
Sta per essere aggiunto il nuovo campo **Tipo di operazione**, che permetterà di specificare il tipo di operazione eseguita sulla risorsa. Ad esempio, **aggiunta**, **aggiornamento** o **eliminazione**.

#### <a name="changed-fields-in-the-audit-schema"></a>Campi modificati nello schema di controllo
È in corso la modifica dei campi seguenti nello schema di controllo:

|Nome campo|Cosa è cambiato|Valori precedenti|Nuovi valori|
|----------|------------|----------|----------|
|Categoria|Campo **Nome servizio** nelle versioni precedenti. Si tratta ora del campo **Audit Categories** (Categorie di controllo). Il campo **Nome del servizio** è stato rinominato in **loggedByService**.|<ul><li>Provisioning degli account</li><li>Directory principale</li><li>Reimpostazione password self-service</li></ul>|<ul><li>User Management</li><li>Gestione di gruppi</li><li>Gestione app</li></ul>|
|targetResources|Include **TargetResourceType** al livello superiore.|&nbsp;|<ul><li>Policy</li><li>App</li><li>Utente</li><li>Gruppo</li></ul>|
|loggedByService|Specifica il nome del servizio che ha generato il log di controllo.|Null|<ul><li>Provisioning degli account</li><li>Directory principale</li><li>Reimpostazione della password self-service</li></ul>|
|Risultato|Indica il risultato dei log di controllo. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li></ul>|<ul><li>Success</li><li>Esito negativo</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>Campi modificati nello schema di accesso
È in corso la modifica dei campi seguenti nello schema di accesso:

|Nome campo|Cosa è cambiato|Valori precedenti|Nuovi valori|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|Campo **conditionalaccessPolicies** nelle versioni precedenti. Si tratta ora del campo **appliedConditionalAccessPolicies**.|Nessuna modifica|Nessuna modifica|
|conditionalAccessStatus|Indica il risultato dello stato dei criteri di accesso condizionale al momento dell'accesso. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>Esito negativo</li><li>Non applicato</li><li>Disabled</li></ul>|
|appliedConditionalAccessPolicies: risultato|Indica il risultato del singolo stato dei criteri di accesso condizionale al momento dell'accesso. Nelle versioni precedenti il campo contiene un'enumerazione, mentre ora viene visualizzato l'effettivo valore.|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>Esito negativo</li><li>Non applicato</li><li>Disabled</li></ul>|

Per altre informazioni sullo schema, vedere [Interpretare lo schema dei log di controllo di Azure AD in Monitoraggio di Azure (anteprima)](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>Miglioramenti relativi a Identity Protection apportati al modello di apprendimento automatico supervisionato e al motore dei punteggi di rischio

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Identity Protection  
**Funzionalità del prodotto:** Punteggi di rischio

I miglioramenti apportati al motore di valutazione dei punteggi di rischio utente e di accesso in relazione a Identity Protection ottimizzano la copertura e la precisione del rischio utente. Gli amministratori potrebbero notare che il livello di rischio utente non è più direttamente collegato al livello di rischio di rilevamenti specifici e che vi è stato un aumento nel numero e nel livello di eventi di accesso rischiosi.

I rilevamenti del rischio vengono ora valutati dal modello di apprendimento automatico supervisionato, che calcola il rischio utente tramite funzionalità aggiuntive degli accessi dell'utente e un modello dei rilevamenti. Attraverso questo modello l'amministratore può individuare gli utenti con punteggi di rischio elevato, anche se i rilevamenti associati all'utente indicano un rischio basso o medio. 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>Possibilità per gli amministratori di reimpostare la propria password tramite l'app Microsoft Authenticator (anteprima pubblica)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Reimpostazione della password self-service  
**Funzionalità del prodotto:** Autenticazione dell'utente

Gli amministratori di Azure AD possono ora reimpostare la propria password seguendo le notifiche dell'app Microsoft Authenticator o tramite un codice da qualsiasi app di autenticazione per dispositivi mobili o token hardware. Per reimpostare la propria password, gli amministratori possono ora usare due metodi diversi:

- Notifica dell'app Microsoft Authenticator

- Altro codice di token hardware/app di autenticazione per dispositivi mobili

- Email

- Chiamata telefonica

- SMS

Per altre informazioni sull'uso dell'app Microsoft Authenticator per reimpostare le password, vedere [Reimpostazione della password self-service di Azure AD - App per dispositivi mobili e SSPR (anteprima)](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>Nuovo ruolo Amministratore dispositivo cloud di Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Registrazione e gestione dei dispositivi  
**Funzionalità del prodotto:** Controllo di accesso

Gli amministratori possono assegnare agli utenti il nuovo ruolo Amministratore dispositivo cloud per l'esecuzione di attività di amministrazione dei dispositivi cloud. Gli utenti assegnati al ruolo Amministratore dispositivo cloud possono abilitare, disabilitare ed eliminare dispositivi in Azure AD, nonché leggere chiavi BitLocker di Windows 10 (se presenti) nel portale di Azure.

Per altre informazioni sui ruoli e sulle autorizzazioni, vedere [Assegnazione di ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>Gestione dei dispositivi tramite il nuovo timestamp di attività in Azure AD (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Registrazione e gestione dei dispositivi  
**Funzionalità del prodotto:** Gestione del ciclo di vita dei dispositivi

Con il passare del tempo, sarà necessario aggiornare e ritirare i dispositivi dell'organizzazione in Azure AD, per evitare la presenza di dispositivi non aggiornati nell'ambiente. Per semplificare questo processo, Azure AD aggiorna ora i dispositivi con un nuovo timestamp di attività, che permette di gestirne il ciclo di vita.

Per altre informazioni su come ottenere e usare questo timestamp, vedere [Procedura: Gestire i dispositivi non aggiornati in Azure AD](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>Possibilità per gli amministratori di chiedere agli utenti di accettare le condizioni per l'utilizzo in ogni dispositivo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance
 
Gli amministratori possono ora attivare l'opzione **Require users to consent on every device** (Richiedi il consenso degli utenti in ogni dispositivo) per chiedere agli utenti di accettare le condizioni per l'utilizzo su ogni dispositivo usato nel tenant.

Per altre informazioni, vedere [Funzionalità Condizioni per l'utilizzo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#per-device-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>Possibilità per gli amministratori di configurare la scadenza delle condizioni per l'utilizzo in base a una pianificazione ricorrente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance
 

Gli amministratori possono ora attivare l'opzione **Scadenza consensi** per impostare una scadenza delle condizioni per l'utilizzo per tutti gli utenti in base alla pianificazione ricorrente specificata. La pianificazione può essere annuale, biennale, trimestrale o mensile. Quando le condizioni d'uso scadono, gli utenti devono accettare.

Per altre informazioni, vedere la [sezione Aggiungere le condizioni per l'utilizzo nell'articolo Funzionalità Condizioni per l'utilizzo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>Possibilità per gli amministratori di configurare la scadenza delle condizioni per l'utilizzo in base alla pianificazione di ogni utente

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance

Gli amministratori possono ora specificare un periodo di tempo dopo il quale un utente deve riaccettare le condizioni per l'utilizzo. Ad esempio, gli amministratori possono specificare che gli utenti devono riaccettare le condizioni per l'utilizzo ogni 90 giorni.

Per altre informazioni, vedere la [sezione Aggiungere le condizioni per l'utilizzo nell'articolo Funzionalità Condizioni per l'utilizzo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/governance/active-directory-tou#add-terms-of-use).
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Nuovi messaggi di posta elettronica di Azure AD Privileged Identity Management (PIM) per ruoli di Azure Active Directory

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
I clienti che usano Azure AD Privileged Identity Management (PIM) possono ora ricevere un messaggio di posta elettronica di riepilogo settimanale, che include le informazioni seguenti per gli ultimi sette giorni:

- Panoramica delle assegnazioni di ruoli permanenti e idonei principali

- Numero di utenti che attivano ruoli

- Numero di utenti assegnati a ruoli in PIM

- Numero di utenti assegnati a ruoli al di fuori di PIM

- Numero di utenti "resi permanenti" in PIM

Per altre informazioni su PIM e sulle notifiche di posta elettronica disponibili, vedere [Notifiche tramite posta elettronica in PIM](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications).

---

### <a name="group-based-licensing-is-now-generally-available"></a>Licenze basate sui gruppi ora disponibili a livello generale

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altri  
**Funzionalità del prodotto:** Directory

Le licenze basate sui gruppi sono ora in anteprima pubblica e disponibili a livello generale. Nell'ambito della disponibilità generale questa funzionalità è stata resa più scalabile e sono state aggiunte la possibilità di rielaborare assegnazioni di licenze basate sui gruppi per un singolo utente e quella di usare licenze basate sui gruppi con licenze di Office 365 E3/A3.

Per altre informazioni sulle licenze basate sui gruppi, vedere [Che cosa sono le licenze basate sui gruppi in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Novembre 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con soluzioni di terze parti
 
Nel mese di novembre 2018 sono state aggiunte alla raccolta di app queste 26 nuove app con supporto per la federazione:

[CoreStack](https://cloud.corestack.io/site/login), [HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial), [GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial), [Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial), [eHour](https://getehour.com/try-now), [Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial), [Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial), [DriveDollar](https://www.drivedollar.com/Account/Login), [Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial), [Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial), [Alaya](https://alayagood.com/en/demo/), [HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial), [Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial), [Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial), [Zenegy for Business Central 365](https://accounting.zenegy.com/), [Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial), [IDEO](https://profile.ideo.com/users/sign_up), [Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial), [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial), [Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial), [Plex Apps - Classic Test](https://test.plexonline.com/signon), [Plex Apps - Classic](https://www.plexonline.com/signon), [Plex Apps - UX Test](https://test.cloud.plex.com/sso), [Plex Apps - UX](https://cloud.plex.com/sso), [Plex Apps - IAM](https://accounts.plex.com/), [CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

## <a name="october-2018"></a>Ottobre 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Log di Azure AD ora funziona con Azure Log Analytics (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione di report

Siamo lieti di annunciare che è ora possibile inoltrare i log di Azure AD ad Azure Log Analytics. Questa funzionalità molto richiesta consente di fornire un accesso ancora migliore ad analisi per l'azienda, operazioni e sicurezza, nonché un modo per monitorare l'infrastruttura. Per altre informazioni, consultare il blog [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) (Log attività di Azure Active Directory in Azure Log Analytics ora disponibile).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Ottobre 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con soluzioni di terze parti
 
Nel mese di ottobre 2018 sono state aggiunte 14 nuove app con il supporto della federazione alla raccolta di app:

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notifiche tramite posta elettronica di Azure AD Domain Services

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Servizi di dominio Azure Active Directory  
**Funzionalità del prodotto:** Servizi di dominio Azure Active Directory

Azure AD Domain Services offre la funzionalità di avvisi relativi ad errori o problemi di configurazione sul portale di Azure. Questi avvisi includono le procedure dettagliate per tentare di risolvere i problemi senza dover contattare l'assistenza.

A partire da ottobre, sarà possibile personalizzare le impostazioni di notifica per il dominio gestito, in modo che quando vengono generati nuovi avvisi, viene inviato un messaggio di posta elettronica a un gruppo di utenti specificato, eliminando la necessità di controllare continuamente il portale per verificare la presenza di aggiornamenti.

Per altre informazioni, vedere [Impostazioni di notifica in Azure AD Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications).

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Il portale di Azure AD supporta l'uso dell'API di dominio ForceDelete per eliminare i domini personalizzati 

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione directory  
**Funzionalità del prodotto:** Directory

Siamo lieti di annunciare che è ora possibile usare l'API di dominio ForceDelete per eliminare i nomi di dominio personalizzati tramite la ridenominazione asincrona dei riferimenti, ad esempio utenti, gruppi e app dal nome di dominio personalizzato (contoso.com) al nome di dominio predefinito iniziale (contoso.onmicrosoft.com).

Questa modifica consente di eliminare in modo più rapido i nomi di dominio personalizzati se l'organizzazione non usa più il nome o se deve usare il nome di dominio in un'altra directory di Azure AD.

Per altre informazioni, vedere [Delete a custom domain name](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name) (Eliminare un nome di dominio personalizzato).

---

## <a name="september-2018"></a>Settembre 2018
 
### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>Autorizzazioni del ruolo di amministratore aggiornate per i gruppi dinamici

**Tipo:** Correzione  
**Categoria di servizio:** Gestione di gruppi  
**Funzionalità del prodotto:** Collaborazione

È stato risolto un problema e ora i ruoli di amministratore specifici possono creare e aggiornare le regole di appartenenza dinamica, senza la necessità di essere il proprietario del gruppo.

I ruoli sono:

- Amministratore globale

- Amministratore di Intune

- Amministratore utenti

Per altre informazioni, vedere [Creare un gruppo dinamico e controllare lo stato](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>Impostazioni di configurazione di Single Sign-On (SSO) semplificate per alcune app di terze parti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

È risaputo che la configurazione di Single Sign-On (SSO) per le app software come un servizio (SaaS, Software as a Service) può risultare complessa a causa della natura unica della configurazione di ogni app. È stata creata un'esperienza di configurazione semplificata per popolare automaticamente le impostazioni di configurazione di SSO per le app SaaS di terze parti seguenti:

- Zendesk

- ArcGis Online

- Jamf Pro

Per iniziare a usare questa esperienza con un clic, passare al **portale di Azure** >  pagina **SSO configuration** (Configurazione SSO) per l'app. Per altre informazioni, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>Azure Active Directory - Pagina Dove vengono archiviati i tuoi dati?

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altri  
**Funzionalità del prodotto:** GoLocal

Selezionare l'area della società nella pagina **Azure Active Directory - Dove vengono archiviati i tuoi dati** per visualizzare il data center di Azure che ospita i dati inattivi di Azure AD per tutti i servizi di Azure AD. È possibile filtrare le informazioni in base ai servizi di Azure AD specifici per l'area della società.

Per accedere a questa funzionalità e per altre informazioni, vedere [Azure Active Directory - Dove vengono archiviati i tuoi dati](https://aka.ms/AADDataMap).

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>Nuovo piano di distribuzione disponibile per il pannello di accesso App personali

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO

Vedere il nuovo piano di distribuzione disponibile per il pannello di accesso App personali (https://aka.ms/deploymentplans).
Il pannello di accesso App personali fornisce agli utenti un'unica posizione per trovare le app e accedervi. Questo portale offre agli utenti anche l'opportunità di eseguire attività self-service, ad esempio richiedere l'accesso ad app e gruppi o gestire l'accesso a queste risorse per conto di altri.

Per altre informazioni, vedere [Informazioni sul portale delle app personali](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction).

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Nuova scheda Risoluzione dei problemi e supporto nella pagina Accessi del portale di Azure

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione di report

La nuova scheda **Risoluzione dei problemi e supporto** nella pagina **Accessi** del portale di Azure ha lo scopo di semplificare la risoluzione dei problemi relativi agli accessi ad Azure AD per amministratori e tecnici del supporto. Questa nuova scheda indica il codice errore, il messaggio di errore ed eventuali consigli per la correzione per risolvere il problema. Se il problema non è risolvibile, è anche possibile creare un ticket di supporto usando l'esperienza **Copia negli Appunti**, che popola i campi **ID richiesta** e **Data (UTC)** per il file di log nel ticket di supporto.  

![Log di accesso che mostra la nuova scheda](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Supporto migliorato per le proprietà di estensione personalizzate usate per creare regole di appartenenza dinamica

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione di gruppi  
**Funzionalità del prodotto:** Collaborazione

Con questo aggiornamento, è ora possibile fare clic sul collegamento **Ottieni proprietà di estensione personalizzate** nel generatore di regole di assegnazione dei gruppi utenti dinamica, immettere l'ID app univoco e ricevere l'elenco completo di proprietà di estensione personalizzate da usare quando si crea una regola di appartenenza dinamica per gli utenti. È anche possibile aggiornare questo elenco per ottenere tutte le nuove proprietà di estensione personalizzate per l'app.

Per altre informazioni sull'uso delle proprietà di estensione personalizzate per le regole di appartenenza dinamica, vedere [Proprietà di estensione e proprietà di estensione personalizzate](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure AD

**Tipo:** Modifica prevista  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Sicurezza e protezione delle identità

Le app seguenti sono nell'elenco delle [app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Per altre informazioni, vedere:

- [Accesso condizionale basato su app di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nuovo supporto per la reimpostazione della password self-service dalla schermata di blocco di Windows 7/8/8.1

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** SSPR  
**Funzionalità del prodotto:** Autenticazione dell'utente

Dopo aver configurato questa nuova funzionalità, gli utenti visualizzeranno un collegamento per reimpostare la password dalla schermata **Blocco** di un dispositivo che esegue Windows 7, Windows 8 o Windows 8.1. Facendo clic su tale collegamento, viene avviato lo stesso flusso di reimpostazione della password del Web browser.

Per altre informazioni, vedere [Come abilitare la reimpostazione della password da Windows 7, 8 e 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Avviso di modifica: codici di autorizzazione non più disponibili per il riutilizzo 

**Tipo:** Modifica prevista  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione dell'utente

A partire dal 15 novembre 2018, Azure AD non accetterà più i codici di autenticazione usati in precedenza per le app. Questa modifica di sicurezza consente di allineare Azure AD con la specifica OAuth e verrà applicata a entrambi gli endpoint v1 e v2.

Se l'app usa nuovamente i codici di autorizzazione per ottenere token per più risorse, è consigliabile usare il codice per ottenere un token di aggiornamento che consenta di acquisire token aggiuntivi per altre risorse. I codici di autorizzazione possono essere usati solo una volta, mentre quelli di aggiornamento possono essere usati più volte su più risorse. Un'app che provi a usare di nuovo un codice di autenticazione durante il flusso del codice OAuth genererà un errore invalid_grant.

Per questa e altre modifiche relative ai protocolli, vedere [l'elenco completo delle novità nell'ambito dell'autenticazione](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Settembre 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con soluzioni di terze parti
 
Nel mese di settembre 2018 sono state aggiunte 16 nuove app con il supporto della federazione alla raccolta di app:

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial), [Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial), [Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial), [ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial), [Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial), [JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial), [Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial), NavigoCloud, [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial), join.me, [ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial), [Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial), Riverbed Xirrus EasyPass, [Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial), Enlyft SSO for Azure, SurveyMonkey, [Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial), [dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="support-for-additional-claims-transformations-methods"></a>Supporto per i metodi aggiuntivi di trasformazione delle attestazioni

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Sono stati introdotti nuovi metodi di trasformazione delle attestazioni, ToLower() e ToUpper(), che possono essere applicati ai token SAML dalla pagina **Configurazione Single Sign-on** basata su SAML.

Per altre informazioni, vedere [Come personalizzare le attestazioni rilasciate nel token SAML per le applicazioni aziendali in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>Interfaccia utente di configurazione delle app basate su SAML aggiornata (anteprima)

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO

Caratteristiche dell'interfaccia utente di configurazione delle app basate su SAML aggiornata:

- Esperienza aggiornata per la configurazione delle app basate su SAML.

- Maggiore visibilità sugli elementi mancanti o non corretti nella configurazione.

- Possibilità di aggiungere più indirizzi di posta elettronica per la notifica della scadenza dei certificati.

- Nuovi metodi di trasformazione delle attestazioni, ToLower () e ToUpper (), e altro ancora.

- Possibilità di caricare il proprio certificato per la firma di token per le app aziendali.

- Possibilità di impostare il formato di NameID per le app SAML e di impostare il valore di NameID come estensioni della directory.

Per attivare questa visualizzazione aggiornata, fare clic sul collegamento **Prova la nuova esperienza** nella parte superiore della pagina **Single Sign-On**. Per altre informazioni, vedere [Esercitazione: Configurare l'accesso Single Sign-On basato su SAML per un'applicazione con Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-portal).