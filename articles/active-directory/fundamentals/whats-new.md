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
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c58c99faa83d6f04ac37d03789202e111f1b06d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611161"
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

Le unità amministrative consentono di concedere autorizzazioni di amministratore limitate a un reparto, un'area o un altro segmento dell'organizzazione definito dall'utente. È possibile utilizzare le unità amministrative per delegare le autorizzazioni agli amministratori regionali o per impostare criteri a livello granulare. Ad esempio, un amministratore dell'account utente potrebbe aggiornare le informazioni sul profilo, reimpostare le password e assegnare le licenze per gli utenti solo nell'unità amministrativa.

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

**Amministratore della stampante**: gli utenti con questo ruolo possono registrare le stampanti e gestire tutti gli aspetti di tutte le configurazioni della stampante nella soluzione di stampa universale Microsoft, incluse le impostazioni del connettore di stampa universale. Possono concedere il consenso a tutte le richieste di autorizzazione di stampa delegate. Gli amministratori della stampante possono anche accedere ai report di stampa. 

**Tecnico della stampante**: gli utenti con questo ruolo possono registrare le stampanti e gestire lo stato della stampante nella soluzione di stampa universale Microsoft. Possono anche leggere tutte le informazioni sul connettore. Le attività principali che un tecnico di stampa non può eseguire sono impostare le autorizzazioni utente sulle stampanti e condividere stampanti. [Altre informazioni.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>Ruolo predefinito amministratore identità ibrida

**Tipo:** Nuova funzionalità

**Categoria di servizio:** RBAC

**Funzionalità del prodotto:** Controllo di accesso

Gli utenti con questo ruolo possono abilitare, configurare e gestire i servizi e le impostazioni relative all'abilitazione dell'identità ibrida in Azure AD. Questo ruolo concede la possibilità di configurare Azure AD a uno dei tre metodi di autenticazione supportati&#8212;la sincronizzazione dell'hash delle password (pH), l'autenticazione pass-through (PTA) o la Federazione (AD FS o provider federativo di terze parti) &#8212;e di distribuire l'infrastruttura locale correlata per abilitarli. L'infrastruttura locale include il provisioning e gli agenti PTA. Questo ruolo concede la possibilità di abilitare l'accesso Single Sign-on facile (S-SSO) per consentire l'autenticazione semplice nei dispositivi non Windows 10 o nei computer non Windows Server 2016. Inoltre, questo ruolo concede la possibilità di visualizzare i log di accesso e di accedere a integrità e analisi per il monitoraggio e la risoluzione dei problemi. [Altre informazioni.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>Ruolo predefinito amministratore di rete

**Tipo:** Nuova funzionalità

**Categoria di servizio:** RBAC

**Funzionalità del prodotto:** Controllo di accesso

Gli utenti con questo ruolo possono esaminare le raccomandazioni relative all'architettura perimetrale di rete di Microsoft basate sui dati di telemetria di rete delle proprie sedi utente. Prestazioni di rete per Office 365 si basa su un'attenta architettura perimetrale della rete del cliente aziendale, che in genere è specifica della posizione utente. Questo ruolo consente di modificare le posizioni utente individuate e la configurazione dei parametri di rete per tali percorsi per semplificare le misurazioni di telemetria migliorate e le indicazioni di progettazione. [Altre informazioni.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

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

Siamo lieti di condividere che abbiamo implementato l'esperienza di aggiornamento [Azure ad Identity Protection](https://aka.ms/IdentityProtectionDocs) nel [portale di Microsoft Azure per enti pubblici](https://portal.azure.us/). Per altre informazioni, vedere il [post di Blog](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)relativo all'annuncio.

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>Ripristino di emergenza: scaricare e archiviare la configurazione del provisioning

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
Il servizio di provisioning Azure AD offre un set completo di funzionalità di configurazione. I clienti devono essere in grado di salvare la configurazione in modo da potervi fare riferimento in un secondo momento o eseguire il rollback a una versione funzionante nota. È stata aggiunta la possibilità di scaricare la configurazione di provisioning come file JSON e caricarla quando è necessario. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration).

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (reimpostazione della password self-service) richiede ora due Gate per gli amministratori in Microsoft Azure gestiti da 21Vianet (Azure China 21Vianet) 

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Reimpostazione della password self-service  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Nelle versioni precedenti di Microsoft Azure gestite da 21Vianet (Azure China 21Vianet), gli amministratori che usano la reimpostazione della password self-service (SSPR) per reimpostare le proprie password necessitavano solo di una "Gate" (Challenge) per dimostrare la propria identità. In cloud pubblici e in altri cloud nazionali, gli amministratori in genere devono usare due controlli per dimostrare la propria identità quando usano SSPR. Tuttavia, dal momento che non sono stati supportati SMS o telefonate in Azure China 21Vianet, è stata consentita la reimpostazione della password di un solo Gate da parte degli amministratori

È in corso la creazione della parità di funzionalità SSPR tra Azure China 21Vianet e il cloud pubblico. In futuro, è necessario che gli amministratori usino due controlli quando usano SSPR. Verranno supportate le notifiche e i codici delle app di autenticazione e SMS, le chiamate telefoniche e i codici. [Altre informazioni](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences).

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

Per offrire ai clienti un modo più flessibile per creare gruppi a livello di directory che soddisfino al meglio le proprie esigenze, l'opzione **gruppi a livello di directory** è stata sostituita dalle impostazioni**generali** dei **gruppi** > nel portale di Azure con un collegamento alla [documentazione del gruppo dinamico](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership). È stata migliorata la documentazione per includere altre istruzioni, in modo che gli amministratori possano creare tutti i gruppi di utenti che includono o escludono gli utenti guest.

---

## <a name="february-2020"></a>Febbraio 2020

### <a name="upcoming-changes-to-custom-controls"></a>Modifiche imminenti ai controlli personalizzati

**Tipo:** Modifica pianificata  
**Categoria di servizio:** AMF  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Stiamo pianificando di sostituire l'anteprima dei controlli personalizzati corrente con un approccio che consente alle funzionalità di autenticazione fornite dai partner di funzionare senza interruzioni con il Azure Active Directory amministratore e l'esperienza dell'utente finale. Attualmente, le soluzioni di autenticazione a più fattori di partner affrontano le limitazioni seguenti: funzionano solo dopo l'immissione di una password; non vengono usati come multi-factor authentication per l'autenticazione step-up in altri scenari chiave. e non si integrano con le funzioni di gestione delle credenziali amministrative e degli utenti finali. La nuova implementazione consentirà ai fattori di autenticazione forniti dai partner di operare insieme a fattori predefiniti per gli scenari principali, ad esempio registrazione, utilizzo, attestazione dell'autenticazione a più fattori, autenticazione, creazione di report e registrazione. 

I controlli personalizzati continueranno a essere supportati in anteprima insieme alla nuova progettazione fino a quando non raggiunge la disponibilità generale. A questo punto, si fornirà ai clienti il tempo per eseguire la migrazione alla nuova progettazione. A causa delle limitazioni dell'approccio corrente, non verranno caricati nuovi provider fino a quando non sarà disponibile la nuova progettazione. Stiamo lavorando a stretto contatto con clienti e provider e la cronologia verrà comunicata Man mano che ci avviciniamo. [Altre informazioni](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#).

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
 
È ora possibile automatizzare la creazione, l'aggiornamento e l'eliminazione degli account utente per queste app appena integrate:

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
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

Come parte di un modello sicuro per impostazione predefinita per i cookie, il browser Chrome 80 sta cambiando il modo in cui tratta i `SameSite` cookie senza l'attributo. Tutti i cookie che non specificano l' `SameSite` attributo verranno considerati come se fossero impostati su `SameSite=Lax`, il che comporterebbe il blocco di determinati scenari di condivisione di cookie tra domini da cui può dipendere l'app. Per mantenere il comportamento precedente di Chrome, è possibile usare `SameSite=None` l'attributo e aggiungere un `Secure` attributo aggiuntivo, in modo che i cookie tra siti possano essere accessibili solo tramite connessioni HTTPS. Chrome è pianificato per completare questa modifica entro il 4 febbraio 2020.

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

[Transtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial), [HootSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial), [Blue Access per Members (BAM)](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial), [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial), [Riva](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial), [ResLife Portal](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=), [NegometrixPortal Single Sign-on (SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial), [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279), [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial), [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial), [BlueMail](https://loginself1.bluemail.me/), [BEEDLE](https://teams-web.beedle.co/#/), [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial), [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial), [Foko Retail](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial), [Qmarkets idea & Innovation Management](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial), [Netskope User Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial), [uniFLOW online](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial), [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial), [JISC Student voter](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial) [, e4enable](https://portal.e4enable.com/)

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
