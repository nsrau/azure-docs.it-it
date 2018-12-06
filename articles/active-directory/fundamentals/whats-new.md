---
title: Novità Note sulla versione per Azure AD | Microsoft Docs
description: Informazioni sulle novità di Azure Active Directory (Azure AD), come le note sulla versione più recenti, i problemi noti, le correzioni di bug, le funzionalità deprecate e le modifiche imminenti.
services: active-directory
author: eross-msft
manager: mtillman
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: lizross
ms.reviewer: dhanyahk
ms.openlocfilehash: dc4e421808ab0f79070224edea4b75f527affaf0
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 11/27/2018
ms.locfileid: "52426238"
---
# <a name="whats-new-in-azure-active-directory"></a>Novità di Azure Active Directory

>Quando si torna a visitare questa pagina per informazioni sugli aggiornamenti, è possibile ricevere notifiche copiando e incollando l'URL `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` nel lettore di feed ![icona RSS](./media/whats-new/feed-icon-16x16.png).

Azure AD viene regolarmente migliorato. Per stare al passo con gli sviluppi più recenti, questo articolo fornisce informazioni sugli argomenti seguenti:

- Versioni più recenti
- Problemi noti
- Correzioni di bug
- Funzionalità deprecate
- Modifiche pianificate

Questa pagina viene aggiornata ogni mese, si consiglia pertanto di consultarla regolarmente. Se si cercano elementi precedenti agli ultimi sei mesi, è possibile trovarli in [Archivio per le novità in Azure Active Directory](whats-new-archive.md).

---
## <a name="october-2018"></a>Ottobre 2018

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Log di Azure AD ora funziona con Azure Log Analytics (anteprima pubblica)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

Siamo lieti di annunciare che è ora possibile inoltrare i log di Azure AD ad Azure Log Analytics. Questa funzionalità molto richiesta consente di fornire un accesso ancora migliore ad analisi per l'azienda, operazioni e sicurezza, nonché un modo per monitorare l'infrastruttura. Per altre informazioni, consultare il blog [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843) (Log attività di Azure Active Directory in Azure Log Analytics ora disponibile).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Ottobre 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di ottobre 2018 sono state aggiunte 14 nuove app con il supporto della federazione alla raccolta di app:

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial), [Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial), ambyint, [MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial), [BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial), Dialpad, [ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial), [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial), [Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial), [Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial), [Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial), [Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial), [Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial), [Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="azure-ad-domain-services-email-notifications"></a>Notifiche tramite posta elettronica di Azure AD Domain Services

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Azure AD Domain Services  
**Funzionalità del prodotto:** Azure AD Domain Services

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
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

È stato risolto un problema e ora i ruoli di amministratore specifici possono creare e aggiornare le regole di appartenenza dinamica, senza la necessità di essere il proprietario del gruppo.

I ruoli sono:

- Amministratore globale o ruolo con autorizzazioni di scrittura nella società

- Amministratore del servizio Intune

- Amministratore account utente

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
**Categoria di servizio:** Altro  
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
**Funzionalità del prodotto:** Monitoraggio e creazione report

La nuova scheda **Risoluzione dei problemi e supporto** nella pagina **Accessi** del portale di Azure ha lo scopo di semplificare la risoluzione dei problemi relativi agli accessi ad Azure AD per amministratori e tecnici del supporto. Questa nuova scheda indica il codice errore, il messaggio di errore ed eventuali consigli per la correzione per risolvere il problema. Se il problema non è risolvibile, è anche possibile creare un ticket di supporto usando l'esperienza **Copia negli Appunti**, che popola i campi **ID richiesta** e **Data (UTC)** per il file di log nel ticket di supporto.  

![Log degli accessi con la nuova scheda](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>Supporto migliorato per le proprietà di estensione personalizzate usate per creare regole di appartenenza dinamica

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Gestione gruppi  
**Funzionalità del prodotto:** Collaborazione

Con questo aggiornamento, è ora possibile fare clic sul collegamento **Ottieni proprietà di estensione personalizzate** nel generatore di regole di assegnazione dei gruppi utenti dinamica, immettere l'ID app univoco e ricevere l'elenco completo di proprietà di estensione personalizzate da usare quando si crea una regola di appartenenza dinamica per gli utenti. È anche possibile aggiornare questo elenco per ottenere tutte le nuove proprietà di estensione personalizzate per l'app.

Per altre informazioni sull'uso delle proprietà di estensione personalizzate per le regole di appartenenza dinamica, vedere [Proprietà di estensione e proprietà di estensione personalizzate](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>Nuove app client approvate per l'accesso condizionale basato su app di Azure AD

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Accesso condizionale  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità

Le app seguenti sono nell'elenco delle [app client approvate](https://docs.microsoft.com/azure/active-directory/conditional-access/technical-reference#approved-client-app-requirement):

- Microsoft To-Do

- Microsoft Stream

Per altre informazioni, vedere:

- [Accesso condizionale basato su app di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Nuovo supporto per la reimpostazione della password self-service dalla schermata di blocco di Windows 7/8/8.1

**Tipo:** Nuova funzionalità  
**Categoria del servizio:** reimpostazione password self-service  
**Funzionalità del prodotto:** Autenticazione utente

Dopo aver configurato questa nuova funzionalità, gli utenti visualizzeranno un collegamento per reimpostare la password dalla schermata **Blocco** di un dispositivo che esegue Windows 7, Windows 8 o Windows 8.1. Facendo clic su tale collegamento, viene avviato lo stesso flusso di reimpostazione della password del Web browser.

Per altre informazioni, vedere [Come abilitare la reimpostazione della password da Windows 7, 8 e 8.1](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Avviso di modifica: i codici di autorizzazione non sono più disponibili per essere usati nuovamente 

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

A partire dal 15 novembre 2018, Azure AD non accetterà più i codici di autenticazione usati in precedenza per le app. Questa modifica di sicurezza consente di allineare Azure AD con la specifica OAuth e verrà applicata a entrambi gli endpoint v1 e v2.

Se l'app usa nuovamente i codici di autorizzazione per ottenere token per più risorse, è consigliabile usare il codice per ottenere un token di aggiornamento che consenta di acquisire token aggiuntivi per altre risorse. I codici di autorizzazione possono essere usati solo una volta, mentre quelli di aggiornamento possono essere usati più volte su più risorse. Un'app che provi a usare di nuovo un codice di autenticazione durante il flusso del codice OAuth genererà un errore invalid_grant.

Per questa e altre modifiche relative ai protocolli, vedere [l'elenco completo delle novità nell'ambito dell'autenticazione](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Settembre 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
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

---

## <a name="august-2018"></a>Agosto 2018

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>Modifiche agli intervalli di indirizzi IP di Azure Active Directory

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Piattaforma

È in corso l'introduzione di intervalli IP più ampi in Azure AD. Ciò indica che se gli intervalli di indirizzi IP di Azure AD per i firewall, i router o i gruppi di sicurezza di rete sono già stati configurati, è necessario aggiornarli. Questo aggiornamento ha lo scopo di evitare altre modifiche alla configurazione degli intervalli IP dei firewall, dei router o dei gruppi di sicurezza di rete quando Azure AD aggiunge nuovi endpoint. 

Il traffico di rete passerà a questi nuovi intervalli nel corso dei prossimi due mesi. Per garantirsi la continuità del servizio, è necessario aggiungere questi valori aggiornati agli indirizzi IP prima del 10 settembre 2018:

- 20.190.128.0/18 

- 40.126.0.0/18 

È consigliabile rimuovere gli intervalli di indirizzi IP precedenti solo dopo che tutto il traffico di rete è passato ai nuovi intervalli. Per aggiornamenti sul passaggio e per informazioni su quando rimuovere gli intervalli precedenti, vedere [Office 365 URLs and IP address ranges](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) (URL e intervalli di indirizzi IP di Office 365).

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>Avviso di modifica: i codici di autorizzazione non sono più disponibili per essere usati nuovamente 

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente

A partire dal 15 novembre 2018, Azure AD non accetterà più i codici di autenticazione usati in precedenza per le app. Questa modifica di sicurezza consente di allineare Azure AD con la specifica OAuth e verrà applicata a entrambi gli endpoint v1 e v2.

Se l'app usa nuovamente i codici di autorizzazione per ottenere token per più risorse, è consigliabile usare il codice per ottenere un token di aggiornamento che consenta di acquisire token aggiuntivi per altre risorse. I codici di autorizzazione possono essere usati solo una volta, mentre quelli di aggiornamento possono essere usati più volte su più risorse. Un'app che provi a usare di nuovo un codice di autenticazione durante il flusso del codice OAuth genererà un errore invalid_grant.

Per questa e altre modifiche relative ai protocolli, vedere [l'elenco completo delle novità nell'ambito dell'autenticazione](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes).
 
---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>Gestione convergente delle informazioni di sicurezza per la reimpostazione self-service della password e l'autenticazione MFA (Multi-Factor Authentication)

**Tipo:** Nuova funzionalità  
**Categoria del servizio:** reimpostazione password self-service  
**Funzionalità del prodotto:** Autenticazione utente
 
Questa nuova funzionalità consente agli utenti di gestire le informazioni di sicurezza (ad esempio numero di telefono, app per dispositivi mobili e così via) per la reimpostazione password self-service e Multi-Factor Authentication in un'unica posizione e con un'unica esperienza, a differenza della situazione precedente in cui l'operazione veniva eseguita in due posizioni diverse.

Questa esperienza convergente si applica anche agli utenti che usano la reimpostazione password self-service o Multi-Factor Authentication. Se l'organizzazione non prevede la registrazione per Multi-Factor Authentication o la reimpostazione password self-service, gli utenti possono comunque registrare qualsiasi metodo correlato alle informazioni di sicurezza consentito dall'organizzazione nel portale App personali.

Si tratta di una funzionalità in anteprima pubblica con consenso esplicito. Gli amministratori possono attivare la nuova esperienza (se necessario) per tutti gli utenti di un tenant o solo per un gruppo selezionato. Per altre informazioni sull'esperienza convergente, vedere il blog sull'[esperienza convergente](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Nuova impostazione HTTP-Only cookies (Cookie solo HTTP) nelle app del proxy di applicazione di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Nelle app del proxy di applicazione è presente una nuova impostazione denominata **HTTP-Only Cookies** (Cookie solo HTTP). Questa impostazione consente di fornire sicurezza aggiuntiva includendo il flag HTTPOnly nell'intestazione della risposta HTTP per entrambi i cookie di sessione e di accesso al proxy di applicazione, interrompendo l'accesso al cookie da uno script lato client ed evitando ulteriormente azioni come la copia o la modifica del cookie. Anche se questo flag non è stato usato in precedenza, i cookie sono sempre stati crittografati e trasmessi tramite una connessione SSL per consentire la protezione da modifiche non corrette.

Questa impostazione non è compatibile con le app che usano i controlli ActiveX, ad esempio Desktop remoto. In questo caso è consigliabile disattivare l'impostazione.

Per altre informazioni sull'impostazione HTTP-Only Cookies (Cookie solo HTTP), vedere [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal).

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Privileged Identity Management (PIM) per le risorse di Azure supporta i tipi di risorsa dei gruppi di gestione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
Le impostazioni JIT e di assegnazione possono ora essere applicate ai tipi di risorsa dei gruppi di gestione, in modo analogo a come accade per sottoscrizioni, gruppi di risorse e risorse (ad esempio macchine virtuali, Servizi app e altro ancora). Tutti gli utenti con un ruolo che consente a un amministratore di accedere a un gruppo di gestione possono individuare e gestire tale risorsa in PIM.

Per altre informazioni su PIM e sulle risorse di Azure, vedere [Individuare e gestire le risorse di Azure usando Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)
 
---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>L'accesso alle applicazioni (in anteprima) consente di accedere più rapidamente al portale di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Privileged Identity Management  
**Funzionalità del prodotto:** Privileged Identity Management
 
L'attivazione di un ruolo tramite PIM può richiedere ora oltre 10 minuti prima che le autorizzazioni diventino effettive. Se si sceglie di usare l'accesso alle applicazioni, ora in versione di anteprima pubblica, gli amministratori possono accedere al portale di Azure AD non appena viene completata la richiesta di attivazione.

L'accesso alle applicazioni supporta attualmente solo l'esperienza del portale di Azure AD e le risorse di Azure. Per altre informazioni su PIM e l'accesso alle applicazioni, vedere [Che cos'è Azure AD Privileged Identity Management?](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)
 
---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Agosto 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di agosto 2018 sono state aggiunte 16 nuove app con il supporto della federazione alla raccolta di app:

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial), [Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial), [Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial), [Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial), [Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial), [Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial), [ProMaster (by Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial), SchoolBooking, [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial), [Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial), [N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial), [Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial), [SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial), [ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial), [eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial), [Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial).

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Il supporto nativo di Tableau è ora disponibile nel proxy di applicazione di Azure AD

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Proxy app  
**Funzionalità del prodotto:** Controllo di accesso

Con l'aggiornamento dal protocollo OpenID Connect al protocollo OAuth 2.0 Code Grant per il nostro protocollo di autenticazione, non è più necessario eseguire alcuna configurazione aggiuntiva per usare Tableau con il proxy di applicazione. Questa modifica al protocollo consente anche al proxy di applicazione di migliorare il supporto di app più moderne usando solo reindirizzamenti HTTP, supportati in genere in tag JavaScript e HTML.

Per altre informazioni sul supporto nativo per Tableau, vedere [Azure AD Application Proxy now with native Tableau support](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support) (Proxy di applicazione di Azure AD con il supporto nativo di Tableau).

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>Nuovo supporto per aggiungere Google come provider di identità per utenti guest B2B in Azure Active Directory (anteprima)

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C

Se si configura la federazione con Google nell'organizzazione, è possibile consentire agli utenti Gmail invitati di accedere alle app e alle risorse condivise tramite i propri account Google esistenti senza la necessità di creare un account Microsoft personale o un account Azure AD.

Si tratta di una funzionalità in anteprima pubblica con consenso esplicito. Per altre informazioni sulla federazione di Google, vedere [Add Google as an identity provider for B2B guest users](https://docs.microsoft.com/azure/active-directory/b2b/google-federation) (Aggiungere Google come provider di identità per utenti guest B2B).

---

## <a name="july-2018"></a>Luglio 2018

### <a name="improvements-to-azure-active-directory-email-notifications"></a>Miglioramenti alle notifiche di posta elettronica di Azure Active Directory

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
I messaggi di posta elettronica di Azure Active Directory (Azure AD) ora presentano una struttura grafica aggiornata, nonché modifiche all'indirizzo di posta elettronica e al nome visualizzato del mittente, quando vengono inviati dai servizi seguenti:
 
- Verifiche di accesso di Azure AD
- Azure AD Connect Health 
- Azure AD Identity Protection 
- Gestione identità con privilegi di Azure AD
- Notifiche di certificati in scadenza di app aziendali
- Notifiche del servizio di provisioning di app aziendali
 
Le notifiche di posta elettronica verranno inviate dall'indirizzo di posta elettronica e dal nome visualizzato seguenti:

- Indirizzo di posta elettronica: azure-noreply@microsoft.com
- Nome visualizzato: Microsoft Azure
 
Per esempi della nuova struttura grafica dei messaggi di posta elettronica e per altre informazioni, vedere [Email notifications in Azure AD PIM](https://go.microsoft.com/fwlink/?linkid=2005832) (Notifiche di posta elettronica in Azure AD PIM).

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>I log attività di Azure AD ora sono disponibili tramite Monitoraggio di Azure

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

I log attività di Azure AD ora sono disponibili in anteprima pubblica per Monitoraggio di Azure (il servizio monitoraggio di Azure a livello di piattaforma). Monitoraggio di Azure offre funzionalità di conservazione a lungo termine e un'integrazione senza problemi, oltre ai miglioramenti seguenti:

- Conservazione a lungo termine tramite il routing dei file di log all'account di Archiviazione di Azure.

- Completa integrazione con i sistemi SIEM, senza che sia necessario scrivere o gestire script personalizzati.

- Completa integrazione con soluzioni personalizzate, strumenti di analisi o soluzioni di gestione degli eventi imprevisti.

Per altre informazioni sulle nuove funzionalità, vedere il blog relativo alla [disponibilità in anteprima pubblica dei log attività di Azure AD nella diagnostica di Monitoraggio di Azure](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/) e la documentazione [Log attività di Azure Active Directory in Monitoraggio di Azure (anteprima)](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview).

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>Sono state aggiunte informazioni sull'accesso condizionale al report sugli accessi di Azure AD

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Questo aggiornamento consente di visualizzare i criteri che vengono valutati quando un utente esegue l'accesso, insieme al risultato dei criteri. Inoltre, il report ora include il tipo di app client usato dall'utente, quindi è possibile identificare il traffico dei protocolli legacy. È inoltre possibile cercare tra le voci del report un ID di correlazione, che è disponibile nel messaggio di errore per l'utente finale e può essere usato per identificare e risolvere i problemi della richiesta di accesso corrispondente.

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>Visualizzazione delle autenticazioni legacy tramite i log attività di accesso

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
Con l'introduzione del campo **App client** nei log attività di accesso, i clienti possono ora vedere gli utenti che usano autenticazioni legacy. I clienti potranno accedere a queste informazioni usando l'API Microsoft Graph per gli accessi o tramite i log attività di accesso nel portale di Azure AD, in cui è possibile usare il controllo **App client** per filtrare le autenticazioni legacy. Per altre informazioni, vedere la documentazione.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Luglio 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di luglio 2018 sono state aggiunte queste 16 nuove app con il supporto della federazione alla raccolta di app:

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial), [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial), [Certain Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial), PSUC Staging, [iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial), [Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial), PowerSchool Unified Classroom, [Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial), [Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial), [Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial), [Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial), [Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial), [SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial), [Kanbanize](https://review.docs.microsoft.com/azure/active-directory/saas-apps/kanbanize-tutorial), [SmartLPA](https://review.docs.microsoft.com/azure/active-directory/saas-apps/smartlpa-tutorial), [Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://aka.ms/azureadapprequest).

---
 
### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>Nuove integrazioni di app SaaS per il provisioning utenti - Luglio 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Azure AD permette di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in applicazioni SaaS, come Dropbox, Salesforce, ServiceNow e altre. A luglio 2018 è stato aggiunto il supporto per il provisioning utenti per le applicazioni seguenti nella raccolta di app Azure AD:

- [Cisco Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-spark-provisioning-tutorial)

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

Per un elenco di tutte le applicazioni che supportano il provisioning utenti nella raccolta di Azure AD, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync: un modo più semplice per risolvere gli errori di sincronizzazione di attributi orfani e duplicati

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** AD Connect  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
Azure AD Connect Health introduce funzionalità di correzione in modalità self-service che consentono di evidenziare e correggere gli errori di sincronizzazione. Questa funzionalità consente di risolvere gli errori di sincronizzazione degli attributi duplicati e di correggere gli oggetti resi orfani da Azure AD. Questa diagnostica offre i vantaggi seguenti:

- Consente di limitare gli errori di sincronizzazione degli attributi duplicati, fornendo correzioni specifiche

- Applica una correzione per gli scenari di Azure AD dedicati, consentendo la risoluzione degli errori in un unico passaggio

- Per attivare e usare questa funzionalità, non sono richiesti aggiornamenti o configurazioni

Per altre informazioni, vedere [Diagnosticare e correggere gli errori di sincronizzazione di attributi duplicati](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>Aggiornamenti dell'interfaccia utente per le esperienze di accesso ad Azure AD e con account del servizio gestito

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Azure AD  
**Funzionalità del prodotto:** Autenticazione utente

È stata aggiornata l'interfaccia utente per l'esperienza di accesso dei servizi online di Microsoft, ad esempio per Office 365 e Azure. Questa modifica rende le schermate più semplici e lineari. Per altre informazioni su questa modifica, vedere il blog [Upcoming improvements to the Azure AD sign-in experience](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/) (Prossimi miglioramenti all'esperienza di accesso di AD Azure).

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Nuova versione di Azure AD Connect - luglio 2018

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità

La versione più recente di Azure AD Connect include: 

- Correzioni di bug e aggiornamenti relativi al supporto 

- Disponibilità generale dell'integrazione di PingFederate

- Aggiornamenti del client più recente di SQL 2012 

Per altre informazioni su questo aggiornamento, vedere [Azure AD Connect: Cronologia delle versioni](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-tou-end-user-ui"></a>Aggiornamenti dell'interfaccia utente delle Condizioni per l'utilizzo

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance

Stiamo aggiornando la stringa di accettazione nell'interfaccia utente delle Condizioni per l'utilizzo.

**Testo corrente.** Per accedere alle risorse di [NomeTenant], è necessario accettare le condizioni per l'utilizzo.<br>**Nuovo testo.** Per accedere alla risorsa di [NomeTenant], è necessario leggere le condizioni per l'utilizzo.

**Testo corrente:** Scegliendo di accettare, l'utente accetta tutte le condizioni per l'utilizzo indicate.<br>**Nuovo testo:** Fare clic su Accetto per confermare di avere letto e compreso le condizioni per l'utilizzo.

---
 
### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>L'autenticazione pass-through supporta le applicazioni e i protocolli legacy

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
L'autenticazione pass-through ora supporta le app e i protocolli legacy. Le limitazioni seguenti ora sono completamente supportate:

- Accessi utente ad applicazioni client legacy di Office (Office 2010 e Office 2013) senza che sia necessaria l'autenticazione moderna.

- Accesso alla condivisione del calendario e alle informazioni sulla disponibilità negli ambienti ibridi di Exchange solo in Office 2010.

- Accessi utente per applicazioni client Skype for Business senza che sia necessaria l'autenticazione moderna.

- Accessi utente a PowerShell versione 1.0.

- Apple Device Enrollment Program (Apple DEP) con l'Assistente configurazione di iOS. 

---
 
### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>Gestione convergente delle informazioni di sicurezza per la reimpostazione della password self-service e Multi-Factor Authentication

**Tipo:** Nuova funzionalità  
**Categoria del servizio:** reimpostazione password self-service  
**Funzionalità del prodotto:** Autenticazione utente

Questa nuova funzionalità consente agli utenti di gestire le informazioni di sicurezza (ad esempio, numero di telefono, indirizzo di posta elettronica, app per dispositivi mobili e così via) per la reimpostazione della password self-service e Multi-Factor Authentication in una singola esperienza. Gli utenti non dovranno più registrare le stesse informazioni di sicurezza per la reimpostazione della password self-service e Multi-Factor Authentication in due diverse esperienze. Questa nuova esperienza si applica anche agli utenti che dispongono della reimpostazione della password self-service o di Multi-Factor Authentication.

Se un'organizzazione non impone la registrazione per Multi-Factor Authentication o la reimpostazione della password self-service, gli utenti possono registrare le proprie informazioni di sicurezza tramite il portale **App personali**. Da qui, gli utenti possono eseguire la registrazione per qualsiasi metodo abilitato per MFA o SSPR. 

Si tratta di una funzionalità in anteprima pubblica con consenso esplicito. Gli amministratori possono attivare la nuova esperienza (se necessario) per un gruppo selezionato di utenti o tutti gli utenti in un tenant.

---
 
### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>Usare l'app Microsoft Authenticator per verificare la propria identità durante la reimpostazione della password

**Tipo:** Funzionalità modificata  
**Categoria del servizio:** reimpostazione password self-service  
**Funzionalità del prodotto:** Autenticazione utente

Questa funzionalità consente agli utenti non amministratori di verificare la propria identità durante la reimpostazione di una password tramite una notifica o un codice di Microsoft Authenticator (o qualsiasi altra app di autenticazione). Dopo che gli amministratori attivano questo metodo di reimpostazione della password self-service, gli utenti che hanno eseguito la registrazione di un'app per dispositivi mobili tramite aka.ms/mfasetup o aka.ms/setupsecurityinfo possono usare l'app per dispositivi mobili come metodo di verifica durante la reimpostazione della password.

Le notifiche dell'app per dispositivi mobili possono essere attivate solo come parte di un criterio che richiede due metodi di reimpostazione della password.

---

## <a name="june-2018"></a>Giugno 2018

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>Notifica di modifica: correzione della sicurezza per il flusso di autorizzazioni delegate per le app che usano l'API Log attività di Azure AD

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report

A causa dell'applicazione della sicurezza più avanzata, è stato necessario apportare una modifica alle autorizzazioni per le app che usano un flusso di autorizzazioni delegate per accedere alle [API Log attività di Azure AD](https://aka.ms/aadreportsapi). Questa modifica verrà applicata entro il **26 giugno 2018**.

Se una delle app usa l'API Log attività di Azure AD, seguire questi passaggi per verificare che l'app non venga interrotta dopo l'applicazione della modifica.

**Per aggiornare le autorizzazioni delle app**

1. Accedere al portale di Azure, selezionare **Azure Active Directory** e quindi selezionare **Registrazioni per l'app**.
2. Selezionare l'app che usa l'API Log attività di Azure AD, selezionare **Impostazioni**, quindi **Autorizzazioni necessarie** e infine selezionare l'API di **Windows Azure Active Directory**.
3. Nell'area **Autorizzazioni delegate** del pannello **Abilita accesso** selezionare la casella accanto a **Lettura dati directory** e quindi selezionare **Salva**.
4. Selezionare **Concedi autorizzazioni** e quindi selezionare **Sì**.
    
    >[!Note]
    >È necessario essere un amministratore globale per concedere le autorizzazioni all'app.

Per altre informazioni, vedere l'area [Concedere le autorizzazioni](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions) dell'articolo sui prerequisiti per l'accesso all'API di creazione report di Azure AD.

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>Configurare le impostazioni TLS per connettersi ai servizi di Azure AD per la conformità PCI DSS

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** N/D  
**Funzionalità del prodotto:** Piattaforma

Transport Layer Security (TLS) è un protocollo che fornisce riservatezza e integrità dei dati tra due applicazioni in comunicazione ed è il protocollo di sicurezza più usato oggi.

Il [PCI Security Standards Council](https://www.pcisecuritystandards.org/) ha determinato che le versioni precedenti di TLS e Secure Sockets Layer (SSL) devono essere disabilitate a favore dell'abilitazione di protocolli di app nuovi e più sicuri, la cui conformità è prevista a partire dal **30 giugno 2018**. Questo cambiamento comporta che, se ci si connette ai servizi di Azure AD e si richiede la conformità PCI DSS, è necessario disabilitare TLS 1.0. Sono disponibili più versioni di TLS, ma TLS 1.2 è la versione più recente disponibile per i servizi di Azure Active Directory. Si consiglia di passare direttamente a TLS 1.2 per le combinazioni sia browser-server sia client-server.

I browser non aggiornati potrebbero non supportare le versioni più recenti di TLS, ad esempio TLS 1.2. Per vedere quali versioni di TLS sono supportate dal browser, accedere al sito [Qualys SSL Labs](https://www.ssllabs.com/) e fare clic su **Test your browser** (Verifica browser). È consigliabile eseguire l'aggiornamento alla versione più recente del Web browser e preferibilmente abilitare solo TLS 1.2.

**Per abilitare TLS 1.2 nei diversi browser**

- **Microsoft Edge e Internet Explorer (si configurano entrambi usando Internet Explorer)**

    1. Aprire Internet Explorer, selezionare **Strumenti** > **Opzioni Internet** > **Avanzate**.
    2. Nell'area **Sicurezza** selezionare **Usa TLS 1.2** e quindi fare clic su **OK**.
    3. Chiudere tutte le finestre del browser e riavviare Internet Explorer. 

- **Google Chrome**

    1. Aprire Google Chrome, digitare *chrome://settings/* nella barra degli indirizzi e premere **Invio**.
    2. Espandere le opzioni **Avanzate**, passare all'area **Sistema** e selezionare **Apri le impostazioni proxy**.
    3. Nella casella **Proprietà Internet** selezionare la scheda **Avanzate**, passare all'area **Sicurezza**, selezionare **Usa TLS 1.2** e fare clic su **OK**.
    4. Chiudere tutte le finestre del browser e riavviare Google Chrome.

- **Mozilla Firefox**

    1. Aprire Firefox, digitare *about:config* nella barra degli indirizzi e premere **Invio**.
    2. Cercare il termine *TLS* e quindi selezionare la voce **security.tls.version.max**.
    3. Impostare il valore su **3** per forzare il browser a usare TLS fino alla versione 1.2, quindi selezionare **OK**.

        >[!NOTE]
        >Firefox versione 60.0 supporta TLS 1.3, perciò è possibile impostare il valore security.tls.version.max anche su **4**.

    4. Chiudere tutte le finestre del browser e riavviare Mozilla Firefox.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Giugno 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di giugno 2018 sono state aggiunte queste 15 nuove app con il supporto della federazione alla raccolta di app:

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial), [Settling music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial), [SAML 1.1 Token enabled LOB App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial), [Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial), [Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial), [Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial), Smartway2, [TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial), [Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial), [Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial), [SharePoint on-premises](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial), [ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial), [Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial), [ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial). Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing). 

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>La protezione delle password di AD Azure è disponibile in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Protezione delle identità  
**Funzionalità del prodotto:** Autenticazione utente

Usare la protezione delle password di Azure Active Directory per eliminare le password facilmente individuabili dall'ambiente. Eliminando queste password è possibile diminuire il rischio di compromissione da attacco password spraying.

In particolare, la protezione delle password di Azure AD aiuta a:

- Proteggere gli account dell'organizzazione sia in Azure AD che in Windows Server Active Directory (AD). 
- Impedisce agli utenti di usare le password di un elenco di più di 500 delle password utilizzate più di frequente e oltre 1 milione di varianti di tali password con sostituzione di caratteri.
- Amministrare la protezione delle password di Azure AD da un'unica posizione nel portale di Azure AD sia per Azure AD che per Windows Server Active Directory locale.

Per altre informazioni sulla protezione della password di Azure AD, vedere [Eliminare le password non appropriate nell'organizzazione](https://aka.ms/aadpasswordprotectiondocs).

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Nuovo modello di criteri di accesso condizionale "tutti i guest" creato durante la creazione delle Condizioni per l'utilizzo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance

Durante la creazione delle Condizioni per l'utilizzo viene anche creato un nuovo modello di criteri di accesso condizionale per "tutti i guest" e "tutte le app". Questo nuovo modello di criteri si applica alle Condizioni per l'utilizzo appena create, semplificando il processo di creazione e applicazione per i guest.

Per altre informazioni, vedere [Funzionalità Condizioni per l'utilizzo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-tou-creation"></a>Nuovo modello di criteri di accesso condizionale "personalizzato" creato durante la creazione delle Condizioni per l'utilizzo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** Governance

Durante la creazione delle Condizioni per l'utilizzo viene anche creato un nuovo modello di criteri di accesso condizionale "custom". Questo nuovo modello di criteri consente di creare le Condizioni per l'utilizzo e quindi raggiungere immediatamente il pannello di creazione dei criteri di accesso condizionale, senza dover passare manualmente tramite il portale.

Per altre informazioni, vedere [Funzionalità Condizioni per l'utilizzo di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-tou).

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>Nuova guida completa su come distribuire Azure Multi-Factor Authentication

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Protezione e sicurezza delle identità
 
Sono state rilasciate nuove istruzioni dettagliate su come distribuire Azure Multi-Factor Authentication (MFA) nell'organizzazione.

Per visualizzare la guida alla distribuzione di Multi-Factor Authentication, visitare il repository [Identity Deployment Guides](https://aka.ms/DeploymentPlans) (Guide alla distribuzione di identità) in GitHub. Per fornire commenti e suggerimenti sulle guide alla distribuzione, usare il [modulo di feedback per il piano di distribuzione](https://aka.ms/deploymentplanfeedback). In caso di domande sulle guide alla distribuzione, contattare Microsoft all'indirizzo [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>I ruoli di gestione delle app delegati di Azure Active Directory sono in anteprima pubblica

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Controllo di accesso

Gli amministratori ora possono delegare le attività di gestione delle app senza assegnare il ruolo di amministratore globale. I nuovi ruoli e funzionalità sono:

- **Nuovi ruoli amministrativi standard di Azure AD:**

    - **Amministratore di applicazioni.** Concede la capacità di gestire tutti gli aspetti di tutte le app, fra cui registrazione, impostazioni SSO, assegnazioni di app e licenze, impostazioni proxy di applicazione e consenso (tranne che per le risorse di Azure AD).

    - **Amministratore di applicazioni cloud.** Concede tutte le capacità di Amministratore di applicazioni, eccetto Proxy di app perché non fornisce l'accesso locale.

    - **Sviluppatore di applicazioni.** Concede la capacità di creare registrazioni delle app, anche se l'opzione **consentire agli utenti di registrare le app** è disattivata.

- **La proprietà, configurata per registrazione di app e per app aziendale, analogamente al processo di proprietà del gruppo:**
 
    - **Proprietario della registrazione delle app.** Concede la capacità di gestire tutti gli aspetti della registrazione delle app di proprietà, inclusi il manifesto dell'app e l'aggiunta di altri proprietari.

    - **Proprietario di app aziendali.** Concede la capacità di gestire numerosi aspetti delle app aziendali di proprietà, fra cui impostazioni SSO, assegnazioni di app e consenso (tranne che per le risorse di Azure AD).

Per altre informazioni sull'anteprima pubblica, vedere il blog [Azure AD delegated application management roles are in public preview!](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) (I ruoli di gestione delle app delegati di Azure AD sono in anteprima pubblica) . Per altre informazioni sui ruoli e le autorizzazioni, vedere [Assegnazione dei ruoli di amministratore in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal).

---

## <a name="may-2018"></a>Maggio 2018

### <a name="expressroute-support-changes"></a>Modifiche al supporto di ExpressRoute

**Tipo:** Modifica pianificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Piattaforma  

Le offerte di software come un servizio, ad esempio Azure Active Directory (Azure AD), sono progettate per ottenere la massima efficienza accedendo direttamente a Internet, senza la necessità di ExpressRoute o di altri tunnel VPN privati. Per questo motivo, l'**1 agosto 2018**, verrà interrotto il supporto di ExpressRoute per i servizi di Azure AD che usano il peering pubblico di Azure e le community di Azure nel peering Microsoft. Tutti i servizi interessati da questa modifica potrebbero riscontrare il graduale passaggio del traffico di Azure AD da ExpressRoute a Internet.

Anche se la modifica al supporto è in corso, è risaputo che esistono ancora situazioni in cui potrebbe essere necessario usare un set di circuiti dedicato per il traffico di autenticazione. Per questo motivo, Azure AD continuerà a supportare le restrizioni per gli intervalli IP in base al tenant usando ExpressRoute e i servizi già inclusi nel peering Microsoft con la community "Altri servizi Online di Office 365". Se i servizi in uso sono interessati, ma è richiesto ExpressRoute, è necessario seguire questa procedura:

- **Se si è nel peering pubblico di Azure.** Passare al peering Microsoft e iscriversi alla community **Altri servizi online di Office 365 (12076:5100)**. Per altre informazioni su come passare dal peering pubblico di Azure al peering Microsoft, vedere l'articolo [Spostare un peering pubblico nel peering Microsoft](https://docs.microsoft.com/azure/expressroute/how-to-move-peering).

- **Se si è nel peering Microsoft.** Iscriversi alla community **Altri servizi online di Office 365 (12076:5100)**. Per altre informazioni sui requisiti per il routing, vedere la [sezione Supporto per le community BGP](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp) dell'articolo Requisiti per il routing di ExpressRoute.

Se è necessario continuare a usare circuiti dedicati, sarà necessario contattare il team degli account Microsoft per informazioni su come ottenere l'autorizzazione per usare la community **Altri servizi online di Office 365 (12076:5100)**. Il Review Board gestito da MS Office verificherà se tali circuiti sono necessari e illustrerà le implicazioni di tecniche che ne seguiranno mantenendoli. Le sottoscrizioni non autorizzate che proveranno a creare filtri della route per Office 365 riceveranno un messaggio di errore. 
 
---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>API Microsoft Graph per scenari amministrativi relativi alle Condizioni per l'utilizzo

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Condizioni per l'utilizzo  
**Funzionalità del prodotto:** esperienza di sviluppo
 
Sono state aggiunte API Microsoft Graph per l'amministrazione delle Condizioni per l'utilizzo di Azure AD. È possibile creare, aggiornare ed eliminare l'oggetto Condizioni per l'utilizzo.

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>Aggiunta di un endpoint multi-tenant di Azure AD come provider di identità in Azure AD B2C

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2C - Gestione delle identità degli utenti  
**Funzionalità del prodotto:** B2B/B2C
 
Usando criteri personalizzati, è ora possibile aggiungere l'endpoint comune di Azure AD come provider di identità in Azure AD B2C. In questo modo, si otterrà un singolo punto di ingresso per tutti gli utenti di Azure AD che accedono alle applicazioni. Per altre informazioni, vedere [Azure Active Directory B2C: consentire agli utenti di accedere a un provider di identità di Azure AD multi-tenant mediante criteri personalizzati](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom).

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>Uso di URL interni per accedere alle app da qualsiasi posizione con l'estensione My Apps Sign-in e Azure AD Application Proxy

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App personali  
**Funzionalità del prodotto:** SSO
 
Gli utenti possono ora accedere alle applicazioni tramite URL interni quando si trovano all'esterno della rete aziendale usando l'estensione My Apps Secure Sign-in per Azure AD. Questa estensione funziona con tutte le applicazioni pubblicate tramite Azure AD Application Proxy su qualsiasi browser in cui è anche installata l'estensione del browser per il pannello di accesso. La funzionalità di reindirizzamento degli URL viene automaticamente abilitata quando un utente accede all'estensione. L'estensione è disponibile per il download in [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176), [Chrome](https://go.microsoft.com/fwlink/?linkid=866367) e [Firefox](https://go.microsoft.com/fwlink/?linkid=866366).

---
 
### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - Dati in Europa per i clienti europei

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** GoLocal

I dati dei clienti in Europa devono restare in Europa e non possono essere replicati al di fuori di data center europei ai fini della conformità alle leggi europee e sulla privacy. Questo [articolo](https://go.microsoft.com/fwlink/?linkid=872328) include i dettagli specifici su quali informazioni sulle identità verranno archiviate in Europa e fornisce anche informazioni sui dati che verranno archiviati al di fuori dei data center europei. 

---
 
### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>Nuove integrazioni di app SaaS per il provisioning utenti - Maggio 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Provisioning di app  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Azure AD permette di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in applicazioni SaaS, come Dropbox, Salesforce, ServiceNow e altre. A maggio 2018 è stato aggiunto il supporto per il provisioning utenti per le applicazioni seguenti nella raccolta di app Azure AD:

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

Per un elenco di tutte le applicazioni che supportano il provisioning utenti nella raccolta di Azure AD, vedere [https://aka.ms/appstutorial](https://aka.ms/appstutorial).

---
 
### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>Le verifiche di accesso per l'accesso di app e gruppi in Azure AD includono ora controlli ricorrenti

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** verifiche di accesso  
**Funzionalità del prodotto:** Governance
 
La verifica di accesso di gruppi e app è in genere disponibile come parte di Azure AD Premium P2.  Gli amministratori potranno configurare le verifiche di accesso delle appartenenze ai gruppi e le assegnazioni delle applicazioni in modo che ricorrano a intervalli regolari, ad esempio ogni mese o ogni trimestre.

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>Log attività di Azure AD (accessi e controllo) ora disponibili tramite Microsoft Graph

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Creazione di report  
**Funzionalità del prodotto:** Monitoraggio e creazione report
 
I log attività di Azure AD, che includono log degli accessi e di controllo, sono ora disponibili tramite Microsoft Graph. Sono stati esposti due endpoint tramite Microsoft Graph per permettere l'accesso a questi log. Per iniziare, fare riferimento ai [documenti](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal) per l'accesso a livello di codice alle API di creazione report di Azure AD. 

---
 
### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>Miglioramenti all'esperienza di riscatto B2B e di uscita da un'organizzazione

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** B2B  
**Funzionalità del prodotto:** B2B/B2C

**Riscatto JIT:** dopo aver condiviso una risorsa con un utente guest tramite l'API B2B, non è necessario inviare un messaggio di posta elettronica di invito speciale. Nella maggior parte dei casi l'utente guest può accedere alla risorsa per poi essere guidato nell'esperienza di riscatto JIT. Di conseguenza, non vi è alcun impatto a causa di messaggi di posta elettronica non ricevuti. Inoltre, non è più necessario chiedere agli utenti guest se hanno fatto clic sul collegamento per la procedura di riscatto inviato dal sistema. Questo significa che quando l'azienda del provider di servizi usa la gestione degli inviti, i file cloud allegati possono avere lo stesso URL canonico per tutti gli utenti, interni ed esterni, in qualsiasi stato di riscatto.

**Esperienza di riscatto moderna:** non è più necessario usare una pagina di destinazione per il riscatto con schermata divisa. Gli utenti vedranno un'esperienza di consenso moderna, che include l'informativa sulla privacy dell'organizzazione che ha emesso l'invito, proprio come avviene con le app di terze parti.

**Gli utenti guest possono lasciare l'organizzazione:** al termine della relazione di un utente con l'organizzazione, l'utente può gestire in modo autonomo l'uscita dall'organizzazione. L'utente non deve più chiamare l'amministratore dell'organizzazione che ha emesso l'invito per chiedere di essere rimosso né è più necessario generare un ticket di supporto.

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Nuove app federate disponibili nella raccolta di app di Azure AD - Maggio 2018

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** Integrazione con app di terze parti
 
Nel mese di maggio 2018 sono state aggiunte queste 18 nuove app con il supporto della federazione alla raccolta di app:

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial), Infogix Data3Sixty Govern, [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial), [Jamf Pro](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial), [KnowledgeOwl](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial), [Envi MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial), [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial), [Adobe Captivate Prime](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial), [Montage Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial), [まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial), OpenReel, [Arc Publishing - SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial), [PlanGrid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial), [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial), [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial), [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial), [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial), [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

Per altre informazioni sulle app, vedere [Integrazione dell'applicazione SaaS con Azure Active Directory](https://aka.ms/appstutorial).

Per altre informazioni su come inserire l'applicazione nella raccolta di app di Azure AD, vedere [Inserire l'applicazione nella raccolta di applicazioni di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing).

---
 
### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Nuove guide dettagliate alla distribuzione per Azure Active Directory

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** Altro  
**Funzionalità del prodotto:** Directory
 
Nuovo materiale sussidiario dettagliato su come distribuire Azure Active Directory (Azure AD), fra cui reimpostazione della password self-service (SSPR), Single Sign-On (SSO), accesso condizionale (CA), proxy di app, provisioning, da Active Directory Federation Services (ADFS) all'autenticazione pass-through (PTA) e da ADFS alla sincronizzazione dell'hash delle password (PHS).

Per visualizzare le guide alla distribuzione, visitare il repository [Identity Deployment Guides](https://aka.ms/DeploymentPlans) (Guide alla distribuzione di identità) in GitHub. Per fornire commenti e suggerimenti sulle guide alla distribuzione, usare il [modulo di feedback per il piano di distribuzione](https://aka.ms/deploymentplanfeedback). In caso di domande sulle guide alla distribuzione, contattare Microsoft all'indirizzo [IDGitDeploy](mailto:idgitdeploy@microsoft.com).

---

### <a name="enterprise-applications-search---load-more-apps"></a>Ricerca di applicazioni aziendali - Caricamento di altre app

**Tipo:** Nuova funzionalità  
**Categoria di servizio:** App aziendali  
**Funzionalità del prodotto:** SSO
 
Per risolvere i problemi di individuazione di applicazioni/entità di servizio, è stata aggiunta la possibilità di caricare altre applicazioni nell'elenco di tutte le applicazioni aziendali. Per impostazione predefinita, vengono visualizzate 20 applicazioni. È ora possibile fare clic su **Carica altro** per visualizzare applicazioni aggiuntive. 

---
 
### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>La versione di maggio di AADConnect contiene un'anteprima pubblica dell'integrazione con PingFederate, importanti aggiornamenti della sicurezza, numerose correzioni di bug e nuovi eccezionali strumenti per la risoluzione dei problemi. 

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** AD Connect  
**Funzionalità del prodotto:** Gestione del ciclo di vita delle identità
 
La versione di maggio di AADConnect contiene un'anteprima pubblica dell'integrazione con PingFederate, importanti aggiornamenti della sicurezza, numerose correzioni di bug e nuovi eccezionali strumenti per la risoluzione dei problemi. Le note sulla versione sono disponibili [qui](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190).

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Verifiche di accesso di Azure AD: applicazione automatica

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** verifiche di accesso  
**Funzionalità del prodotto:** Governance

Le verifiche di accesso di gruppi e app sono in genere disponibili come parte di Azure AD Premium P2. Un amministratore può configurare l'ambiente per l'applicazione automatica delle modifiche da apportare al gruppo o all'app al termine delle verifiche di accesso. L'amministratore può anche specificare che cosa succederà all'accesso continuo dell'utente se i revisori non rispondono, rimuovere l'accesso, mantenere l'accesso o eseguire azioni consigliate relative al sistema. 

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-responsemode-for-new-apps"></a>I token ID non possono più essere restituiti tramite response_mode query per le nuove app. 

**Tipo:** Funzionalità modificata  
**Categoria di servizio:** Autenticazioni (accessi)  
**Funzionalità del prodotto:** Autenticazione utente
 
Le app create a partire dal 25 aprile 2018 non potranno più richiedere **id_token** tramite il response_mode **query**.  Questa novità allinea Azure AD alle specifiche OIDC e contribuisce a ridurre la superficie di attacco delle app.  Alle app create prima del 25 aprile 2018 non viene impedito di usare il response_mode **query** con response_type **id_token**.  L'errore restituito quando si richiede un valore id_token da AAD è **AADSTS70007** e indica che "query" non è un valore supportato per "response_mode" quando si richiede un token.

I valori di response_mode **fragment**e **form_post** continuano a funzionare quando si creano nuovi oggetti applicazione, ad esempio per l'utilizzo del proxy di app. Assicurarsi di usare uno dei due prima di creare una nuova applicazione.  

---