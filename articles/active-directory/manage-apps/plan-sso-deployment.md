---
title: Pianificare una distribuzione di Azure Active Directory Single Sign-on
description: Guida per la pianificazione, la distribuzione e la gestione dell'accesso SSO nell'organizzazione.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 733b0d7650d68bddae60cf524947590c2b689968
ms.sourcegitcommit: 6cbf5cc35840a30a6b918cb3630af68f5a2beead
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 08/05/2019
ms.locfileid: "68779371"
---
# <a name="plan-a-single-sign-on-deployment"></a>Pianificare una distribuzione di Single Sign-On

Single Sign-on (SSO) consente di accedere a tutte le applicazioni e risorse necessarie per un utente effettuando l'accesso una sola volta con un singolo account utente. Con SSO, gli utenti possono accedere a tutte le applicazioni necessarie senza che sia necessario eseguire l'autenticazione una seconda volta.

## <a name="benefits-of-sso"></a>Vantaggi di SSO

Single Sign-on (SSO) consente di aggiungere sicurezza e praticità quando gli utenti possono accedere alle applicazioni in Azure Active Directory (Azure AD). 

Molte organizzazioni si basano su applicazioni SaaS (Software as a Service), ad esempio Office 365, box e Salesforce, per la produttività degli utenti finali. In passato, il personale IT doveva creare e aggiornare singolarmente gli account utente in ciascuna applicazione SaaS e gli utenti dovevano ricordare una password per ognuna.

Azure Marketplace ha più di 3000 applicazioni con connessioni SSO pre-integrate, semplificando l'integrazione nel tenant.

## <a name="licensing"></a>Licenze

- **Azure ad Licensing** -SSO per le applicazioni SaaS pre-integrate è gratuito. Tuttavia, il numero di oggetti nella directory e le funzionalità che si desidera distribuire potrebbero richiedere licenze aggiuntive. Per un elenco completo dei requisiti di licenza, vedere [Azure Active Directory prezzi](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licenze** per le applicazioni: per soddisfare le esigenze aziendali è necessario disporre delle licenze appropriate per le applicazioni SaaS. Collaborare con il proprietario dell'applicazione per determinare se gli utenti assegnati all'applicazione hanno le licenze appropriate per i rispettivi ruoli all'interno dell'applicazione. Se Azure AD gestisce il provisioning automatico basato sui ruoli, i ruoli assegnati in Azure AD devono essere allineati con il numero di licenze possedute nell'applicazione. Il numero di licenze non corrette di proprietà dell'applicazione può causare errori durante il provisioning o l'aggiornamento di un utente.

## <a name="plan-your-sso-team"></a>Pianificare il team SSO

- **Coinvolgere gli stakeholder più appropriati** : quando i progetti di tecnologia hanno esito negativo, in genere è dovuto a una mancata corrispondenza delle aspettative in merito a conseguenze, risultati e responsabilità. Per evitare questi problemi, [assicurarsi di coinvolgere le parti interessate giuste](https://aka.ms/deploymentplans) e che le parti interessate siano in grado di comprendere i loro ruoli.
- **Pianificare le comunicazioni** : la comunicazione è fondamentale per il successo di un nuovo servizio. Comunicare in modo proattivo agli utenti le modifiche apportate alla loro esperienza, quando verranno modificate e come ottenere supporto in caso di problemi. Esaminare le opzioni per il modo in cui [gli utenti finali accedono alle applicazioni abilitate per SSO](end-user-experiences.md)e creare le comunicazioni corrispondenti alla selezione. 

## <a name="plan-your-sso-protocol"></a>Pianificare il protocollo SSO

Un'implementazione SSO basata sui protocolli federativi migliora la sicurezza, l'affidabilità e l'esperienza degli utenti finali ed è più facile da implementare. Molte applicazioni sono pre-integrate in Azure AD con [guide dettagliate disponibili](../saas-apps/tutorial-list.md). È possibile trovarli in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Informazioni dettagliate su ogni metodo SSO sono disponibili nell'articolo [accesso Single Sign-on alle applicazioni in Azure Active Directory](what-is-single-sign-on.md).

Esistono due modi principali in cui è possibile abilitare gli utenti per l'accesso Single Sign-on alle app:

- **Con Single Sign-on federato** Azure AD autentica l'utente nell'applicazione utilizzando il relativo account Azure AD. Questo metodo è supportato per le applicazioni che supportano protocolli quali SAML 2,0, WS-Federation o OpenID Connect ed è la modalità di accesso Single Sign-on più ricca. Si consiglia di utilizzare SSO federato con Azure AD quando un'applicazione la supporta, anziché SSO basato su password e ADFS.

- **Con l'accesso Single Sign-on basato su password** , l'utente accede all'applicazione con un nome utente e una password per la prima volta. Agli accessi successivi sarà Azure AD a indicare il nome utente e la password all'applicazione. L'accesso Single Sign-On basato su password consente l'archiviazione e la riproduzione delle password delle applicazioni protette usando un'estensione del Web browser o un'app per dispositivi mobili. Questa opzione sfrutta il processo di accesso esistente fornito dall'applicazione, consente a un amministratore di gestire le password e non richiede che l'utente conosca la password.

### <a name="considerations-for-federation-based-sso"></a>Considerazioni per SSO basato su Federazione

- **Usando OpenID Connect e OAuth** : se l'applicazione a cui ci si connette supporta, usare il metodo OIDC/OAuth 2,0 per abilitare l'accesso SSO a tale applicazione. Questo metodo richiede un minor numero di configurazioni e offre un'esperienza utente più completa. Per ulteriori informazioni, vedere [OAuth 2,0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1,0](../develop/v2-protocols-oidc.md)e [Azure Active Directory Guida per gli sviluppatori](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Configurazioni di endpoint per SSO basato su SAML** : se si usa SAML, gli sviluppatori dovranno disporre di informazioni specifiche prima di configurare l'applicazione. Per altre informazioni, vedere [modificare la configurazione SAML di base](configure-single-sign-on-non-gallery-applications.md).
- **Gestione dei certificati per SSO basato su SAML** : quando si Abilita SSO federato per l'applicazione, Azure ad crea un certificato che per impostazione predefinita è valido per tre anni. Se necessario, è possibile personalizzare la data di scadenza del certificato. Assicurarsi di disporre di processi per rinnovare i certificati prima della scadenza. Per altre informazioni, vedere [Azure ad gestione dei certificati](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Considerazioni per SSO basato su password

L'utilizzo di Azure AD per l'accesso SSO basato su password richiede la distribuzione di un'estensione del browser che recupererà in modo sicuro le credenziali e compilerà i form di accesso. Definire un meccanismo per distribuire l'estensione su larga scala con i [browser supportati](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Le opzioni includono:

- [Criteri di gruppo per Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [System Center Configuration Manager (SCCM) per Internet Explorer](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [Download e configurazione gestiti dall'utente per Chrome, Firefox, Microsoft Edge o IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Per ulteriori informazioni, vedere [come configurare la password Single Sign-on](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Acquisizione dei metadati dei form di accesso per le applicazioni che non si trovano nella raccolta

Microsoft supporta l'acquisizione di metadati in un'applicazione Web per l'insieme di credenziali delle password (acquisizione dei campi nome utente e password). Passare all'URL di accesso durante il processo di configurazione dell'applicazione per acquisire i metadati dei moduli. Chiedere al proprietario dell'applicazione l'URL di accesso esatto. Queste informazioni vengono usate durante il processo di accesso, eseguendo il mapping delle credenziali Azure AD all'applicazione durante l'accesso.

Per altre informazioni, vedere [che cos'è l'accesso alle applicazioni e SSO con Azure ad?-SSO basato su password](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Indicazioni per la riacquisizione dei metadati nei moduli

Quando le applicazioni modificano il layout HTML, potrebbe essere necessario riacquisire i metadati per adattarli alle modifiche. I sintomi comuni che indicano che il layout HTML è costituito da modifiche includono:

- Gli utenti che fanno clic sull'applicazione vengono bloccati nella pagina di accesso
- Utenti che segnalano che il nome utente o la password non sono popolati

#### <a name="shared-accounts"></a>Account condivisi

Dal punto di vista dell'accesso, le applicazioni con account condivisi non sono diverse da un'applicazione della raccolta che usa l'accesso Single Sign-on basato su password per i singoli utenti. Tuttavia, sono necessari alcuni passaggi aggiuntivi per la pianificazione e la configurazione di un'applicazione per l'utilizzo di account condivisi:

1. Collaborare con gli utenti aziendali dell'applicazione per documentare quanto segue:
   1. Set di utenti dell'organizzazione che utilizzeranno l'applicazione
   1. Set di credenziali esistente nell'applicazione associata al set di utenti 
1. Per ogni combinazione di set di utenti e credenziali, creare un gruppo di sicurezza nel cloud o in locale in base ai propri requisiti.
1. Reimpostare le credenziali condivise. Una volta distribuita l'app in Azure AD, gli utenti non necessitano della password dell'account condiviso. Poiché in Azure AD verrà archiviata la password, è consigliabile impostarla in modo che sia molto lungo e complessa. 
1. Configurare il rollover automatico della password se l'applicazione la supporta. In questo modo, non anche l'amministratore che ha eseguito la configurazione iniziale saprà la password dell'account condiviso. 

## <a name="plan-your-authentication-method"></a>Pianificare il metodo di autenticazione

La scelta del metodo di autenticazione appropriato è una prima decisione fondamentale nella configurazione di una soluzione ibrida di gestione delle identità di Azure AD. Implementare il metodo di autenticazione configurato mediante Azure AD Connect, che esegue anche il provisioning degli utenti nel cloud.

Per scegliere un metodo di autenticazione è necessario prendere in considerazione il tempo, l'infrastruttura esistente, la complessità e i costi di implementazione della propria scelta. Questi fattori variano in base all'organizzazione e possono cambiare nel corso del tempo. È consigliabile scegliere quella più adatta per lo scenario specifico. Per altre informazioni, vedere [scegliere il metodo di autenticazione appropriato per la soluzione di identità ibrida Azure Active Directory](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn).

## <a name="plan-your-security-and-governance"></a>Pianificare sicurezza e governance 

L'identità è il nuovo perno principale per l'attenzione e gli investimenti di sicurezza perché i perimetri di rete sono diventati sempre più porosi e meno efficaci con l'esplosione dei dispositivi BYOD e delle applicazioni cloud. 

### <a name="plan-access-reviews"></a>Pianificare le verifiche di accesso

Le verifiche di [accesso](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) consentono alle organizzazioni di gestire in modo efficiente l'appartenenza ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. È consigliabile pianificare la verifica dell'accesso degli utenti a intervalli regolari per assicurarsi che solo le persone giuste abbiano accesso continuo.

Di seguito sono elencati alcuni degli argomenti chiave da pianificare durante la configurazione delle verifiche di accesso:

1. Identificazione di una cadenza per le verifiche di accesso che soddisfi le esigenze aziendali. Questa operazione può essere frequente come una volta alla settimana, ogni mese, ogni anno o come esercizio su richiesta.

1. Creare gruppi che rappresentano i revisori dei report di accesso all'app. È necessario assicurarsi che le parti interessate che hanno familiarità con l'app e i relativi utenti di destinazione e i casi d'uso siano partecipanti alle verifiche di accesso

1. Il completamento di una verifica di accesso include l'acquisizione delle autorizzazioni di accesso alle app agli utenti che non necessitano più dell'accesso. Pianificare la gestione delle richieste di supporto potenziali da parte di utenti non autorizzati. Un utente eliminato rimarrà eliminato in Azure AD per 30 giorni durante i quali può essere ripristinato da un amministratore, se necessario. Dopo 30 giorni l'utente verrà eliminato definitivamente. Usando il portale di Azure Active Directory, un amministratore globale può eliminare in modo esplicito un utente eliminato di recente prima del raggiungimento di tale periodo di tempo.

### <a name="plan-auditing"></a>Pianificare il controllo

Azure AD fornisce [report contenenti informazioni tecniche e aziendali](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). 

Sono disponibili sia report di sicurezza che di attività. I report di sicurezza mostrano gli utenti contrassegnati per il rischio e gli accessi a rischio. I report delle attività consentono di comprendere il comportamento degli utenti dell'organizzazione, fornendo informazioni dettagliate sulle attività di accesso e fornendo audit trail di tutti gli account di accesso. È possibile utilizzare i report per gestire i rischi, aumentare la produttività e monitorare la conformità.

| Tipo di report | Verifica di accesso | Report sulla sicurezza | Report di accesso |
|-------------|---------------|------------------|----------------|
| Usare per esaminare | Autorizzazioni e utilizzo dell'applicazione. | Account potenzialmente compromessi | Utenti che accedono alle applicazioni |
| Azioni potenziali | Controllare l'accesso; revoca autorizzazioni | Revoca l'accesso; forza reimpostazione della sicurezza | Revoca l'accesso |

Azure AD mantiene la maggior parte dei dati di controllo per 30 giorni e rende disponibili i dati tramite il portale di amministrazione di Azure o tramite un'API da scaricare nei sistemi di analisi.

### <a name="consider-using-microsoft-cloud-application-security"></a>Prendere in considerazione l'uso di Microsoft Cloud sicurezza delle applicazioni

Microsoft Cloud App Security (MCAS) è una soluzione CASB (cloud Access Security Broker). Consente di visualizzare le app e i servizi cloud, fornisce analisi sofisticate per identificare e combattere Cyberthreats e consente di controllare il modo in cui i dati vengono trasmessi.

La distribuzione di MCAS consente di:

- Usare Cloud Discovery per eseguire il mapping e identificare l'ambiente cloud e le app Cloud usate dall'organizzazione.
- Approvazione e annullamento dell'approvazione delle app nel cloud
- Usare i connettori app di facile distribuzione che sfruttano le API del provider, per la visibilità e la governance delle app a cui ci si connette
- Usa la protezione Controllo app per l'accesso condizionale per ottenere visibilità e controllo in tempo reale dell'accesso e delle attività nelle app Cloud
- Consente di avere un controllo continuo impostando e quindi ottimizzando costantemente i criteri.

Il controllo della sessione MCAS (Microsoft Cloud Application Security) è disponibile per qualsiasi browser su qualsiasi piattaforma principale in qualsiasi sistema operativo. Le app per dispositivi mobili e le app desktop possono anche essere bloccate o consentite. Grazie all'integrazione nativa con Azure AD, è possibile supportare tutte le app configurate con SAML o le app Open ID Connect con Single Sign-on in Azure AD, incluse [diverse app in primo piano](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad).

Per informazioni su MCAS, vedere la [Panoramica di Microsoft cloud app Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). MCAS è un servizio di sottoscrizione basato su utente. È possibile esaminare i dettagli relativi alle licenze nel [foglio dati di licenza MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Usare l'accesso condizionale

Con l'accesso condizionale è possibile automatizzare le decisioni relative al controllo degli accessi in base ai criteri per le app cloud.

I criteri di accesso condizionale vengono applicati dopo il completamento dell'autenticazione con primo fattore. L'accesso condizionale non è quindi concepito come una difesa di prima linea per scenari come gli attacchi Denial of Service (DoS), ma può usare i segnali di questi eventi per determinare l'accesso. È ad esempio possibile usare il livello di rischio di accesso, il percorso della richiesta e così via. Per ulteriori informazioni sull'accesso condizionale, vedere [la panoramica](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) e il [piano di distribuzione](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Requisiti tecnici di Azure SSO

La sezione seguente illustra in dettaglio i requisiti per configurare l'applicazione specifica, inclusi gli ambienti, gli endpoint, il mapping delle attestazioni, gli attributi obbligatori, i certificati e i protocolli necessari usati. Queste informazioni sono necessarie per configurare l'accesso SSO nel [portale di Azure ad](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Dettagli del meccanismo di autenticazione

Per tutte le app SaaS pre-integrate, Microsoft fornisce un'esercitazione e non è necessario disporre di queste informazioni. Se l'applicazione non si trovi nel Marketplace o nella raccolta di applicazioni, potrebbe essere necessario raccogliere i dati seguenti:

- **Provider di identità corrente usato dall'applicazione per SSO, se applicabile** , ad esempio: AD FS, PingFederate, okta
- **Protocolli supportati dall'applicazione di destinazione** , ad esempio SAML 2,0, OpenID Connect, OAuth, l'autenticazione basata su form, WS-Fed, WS-Trust
- **Protocollo configurato con Azure ad** , ad esempio SAML 2,0 o 1,1, OpenID Connect, OAuth, basata su form, WS-Fed

### <a name="attribute-requirements"></a>Requisiti per gli attributi

Esiste un set preconfigurato di attributi e mapping di attributi tra Azure AD oggetti utente e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti, ad esempio i gruppi. Pianificare il mapping degli attributi utente da Azure AD all'applicazione e [personalizzare i mapping](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) degli attributi predefiniti in base alle esigenze aziendali.

### <a name="certificate-requirements"></a>Requisiti del certificato

Il certificato per l'applicazione deve essere aggiornato o il rischio che gli utenti non siano in grado di accedere all'applicazione. La maggior parte dei certificati di applicazioni SaaS è buona per 36 mesi. Modificare la durata del certificato nel pannello dell'applicazione. Assicurarsi di documentare la scadenza e di comprendere come si gestirà il rinnovo del certificato. 

Esistono due modi per gestire i certificati. 

- **Rollover automatico dei certificati** : Microsoft supporta il [rollover della chiave di firma in Azure ad](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Sebbene questo sia il metodo preferito per la gestione dei certificati, questo scenario non è supportato da tutti gli ISV.

- **Aggiornamento manuale** : ogni applicazione dispone di un proprio certificato che scade in base alla modalità di definizione. Prima della scadenza del certificato dell'applicazione, creare un nuovo certificato e inviarlo all'ISV. Queste informazioni possono essere estratte dai metadati della Federazione. [Altre informazioni sui metadati di federazione sono disponibili qui.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementa SSO

Usare le fasi seguenti per pianificare e distribuire la soluzione nell'organizzazione:

### <a name="user-configuration-for-sso"></a>Configurazione utente per SSO

- **Identificazione degli utenti di test**

   Contattare il proprietario dell'app e chiedere di creare almeno tre utenti di test all'interno dell'applicazione. Verificare che le informazioni che verranno usate come identificatore primario vengano popolate correttamente e corrispondano a un attributo disponibile in Azure AD. Nella maggior parte dei casi viene mappato a "NameID" per le applicazioni basate su SAML. Per i token JWT, è "preferred_username".
   
   Creare l'utente in Azure AD manualmente come utente basato sul cloud o sincronizzare l'utente da locale usando il motore di sincronizzazione Azure AD Connect. Verificare che le informazioni corrispondano alle attestazioni inviate all'applicazione.

- **Configura SSO**

   Dall' [elenco di applicazioni](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), individuare e aprire l'esercitazione SSO per l'applicazione, quindi seguire i passaggi dell'esercitazione in per configurare correttamente l'applicazione SaaS.

   Se non è possibile individuare l'applicazione, vedere la [documentazione dell'applicazione personalizzata](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Verrà illustrato come aggiungere un'applicazione che non si trova nella raccolta di Azure AD.

   Facoltativamente, è possibile usare le attestazioni rilasciate nel token SAML per l'applicazione aziendale usando la [documentazione di Microsoft](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Assicurarsi che questo sia mappato a ciò che si prevede di ricevere nella risposta SAML per l'applicazione. Se si verificano problemi durante la configurazione, attenersi alle indicazioni su [come eseguire il debug dell'integrazione SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

Il caricamento dell'applicazione personalizzata è una funzionalità di licenze Azure AD Premium P1 o P2.

### <a name="provide-sso-change-communications-to-end-users"></a>Fornire le comunicazioni di modifica SSO agli utenti finali

Implementare il piano di comunicazione. Assicurarsi che gli utenti finali sappiano che è stata apportata una modifica, quando è arrivato, cosa fare ora e come richiedere assistenza.

### <a name="verify-end-user-scenarios-for-sso"></a>Verifica scenari utente finale per SSO

È possibile utilizzare i seguenti test case per eseguire test sui dispositivi personali e di proprietà dell'azienda per assicurarsi che le configurazioni SSO funzionino come previsto. Gli scenari seguenti presuppongono che un utente stia passando a un URL dell'applicazione e attraversi un flusso di autenticazione avviato dal provider di servizi (flusso di autenticazione avviato da SP).

| Scenario | Risultato previsto nel flusso di autenticazione avviato da SP dall'utente |
|----------|---------------------------------------------------|
| Accedere all'applicazione con IE mentre si è in Corpnet. | Autenticazione integrata di Windows (IWA) si verifica senza ulteriori richieste. |
| Accedere a un'applicazione con IE mentre è disattivato corpnet con un nuovo tentativo di accesso. | Richiesta basata su form in AD FS server. L'utente accede correttamente e Visualizza i prompt del browser per l'autenticazione a più fattori. |
| Consente di accedere all'applicazione con IE mentre è disattivato corpnet con una sessione corrente e non ha mai eseguito l'autenticazione a più fattori. | L'utente non riceve la richiesta per il primo fattore. L'utente riceve la richiesta di autenticazione a più fattori. |
| Consente di accedere all'applicazione con IE mentre è disattivato corpnet con una sessione corrente ed è già stata eseguita l'autenticazione a più fattori in questa sessione. | L'utente non riceve la richiesta per il primo fattore. L'utente non riceve l'autenticazione a più fattori. Utente SSOs nell'applicazione. |
| Accedere all'applicazione con Chrome/Firefox/Safari mentre è disattivato corpnet con una sessione corrente ed è già stata eseguita l'autenticazione a più fattori in questa sessione. | L'utente non riceve la richiesta per il primo fattore. L'utente non riceve l'autenticazione a più fattori. Utente SSO nell'applicazione. |
| Accedere a in un'applicazione con Chrome/Firefox/Safari mentre è disattivato corpnet con un nuovo tentativo di accesso. | Richiesta basata su form in AD FS server. L'utente accede correttamente e Visualizza i prompt del browser per l'autenticazione a più fattori. |
| Accedere all'applicazione con Chrome/Firefox mentre si è in rete aziendale con una sessione corrente. | L'utente non riceve la richiesta per il primo fattore. L'utente non riceve l'autenticazione a più fattori. Utente SSO nell'applicazione. |
| Accedere all'applicazione con l'app per dispositivi mobili dell'applicazione con un nuovo tentativo di accesso. | Richiesta basata su form in AD FS server. L'utente accede e ADAL i prompt client per l'autenticazione a più fattori. |
| Utenti non autorizzati tentano di accedere all'applicazione con l'URL di accesso. | Richiesta basata su form in AD FS server. L'utente non riesce ad accedere con il primo fattore. |
| L'utente autorizzato tenta di accedere, ma immette una password errata. | L'utente passa all'URL dell'applicazione e riceve un errore di nome utente/password non valido. |
| L'utente autorizzato fa clic sul collegamento in un messaggio di posta elettronica ed è già autenticato. | L'utente fa clic su URL ed è connesso all'applicazione senza ulteriori richieste. |
| L'utente autorizzato fa clic sul collegamento in un messaggio di posta elettronica e non è ancora autenticato. | L'utente fa clic su URL e viene richiesto di eseguire l'autenticazione con il primo fattore. |
| L'utente autorizzato accede all'applicazione con l'app per dispositivi mobili dell'applicazione (avviata da SP) con un nuovo tentativo di accesso. | Richiesta basata su form in AD FS server. L'utente accede e ADAL i prompt client per l'autenticazione a più fattori. |
| Utenti non autorizzati tentano di accedere all'applicazione con l'URL di accesso (avviata da SP). | Richiesta basata su form in AD FS server. L'utente non riesce ad accedere con il primo fattore. |
| L'utente autorizzato tenta di accedere, ma immette una password errata.| L'utente passa all'URL dell'applicazione e riceve un errore di nome utente/password non valido. |
| L'utente autorizzato si disconnette e quindi esegue di nuovo l'accesso. | Se è configurato l'URL di disconnessione, l'utente viene disconnesso da tutti i servizi e viene richiesto di eseguire l'autenticazione. |
| L'utente autorizzato si disconnette e quindi esegue di nuovo l'accesso. | Se l'URL di disconnessione non è configurato, l'utente verrà automaticamente connesso usando il token esistente dalla sessione esistente del browser Azure AD. |
| L'utente autorizzato fa clic sul collegamento in un messaggio di posta elettronica ed è già autenticato. | L'utente fa clic su URL ed è connesso all'applicazione senza ulteriori richieste. |
| L'utente autorizzato fa clic sul collegamento in un messaggio di posta elettronica e non è ancora autenticato. | L'utente fa clic su URL e viene richiesto di eseguire l'autenticazione con il primo fattore. |

## <a name="manage-sso"></a>Gestisci SSO

In questa sezione vengono descritti i requisiti e le indicazioni per gestire correttamente SSO.

### <a name="required-administrative-roles"></a>Ruoli amministrativi richiesti

Usare sempre il ruolo con il minor numero di autorizzazioni disponibili per eseguire l'attività necessaria entro Azure Active Directory. Microsoft consiglia di [esaminare i diversi ruoli disponibili](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e scegliere quello più adatto a soddisfare le proprie esigenze per ogni utente di questa applicazione. È possibile che alcuni ruoli debbano essere applicati temporaneamente e rimossi dopo il completamento della distribuzione.

| Utente tipo| Ruoli | Ruolo Azure AD (se necessario) |
|--------|-------|-----------------------------|
| Amministratore del supporto tecnico | Supporto di livello 1 | Nessuna |
| Amministratore identità | Configurare ed eseguire il debug quando i problemi hanno effetto Azure AD | Amministratore globale |
| Amministratore applicazione | Attestazione utente nell'applicazione, configurazione sugli utenti con autorizzazioni | Nessuna |
| Amministratori dell'infrastruttura | Proprietario del rollover del certificato | Amministratore globale |
| Proprietario/stakeholder aziendale | Attestazione utente nell'applicazione, configurazione sugli utenti con autorizzazioni | Nessuna |

È consigliabile usare [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) per gestire i ruoli per fornire controllo, controllo e verifica di accesso aggiuntivi per gli utenti con autorizzazioni di directory.

### <a name="sso-certificate-lifecycle-management"></a>Gestione del ciclo di vita dei certificati SSO

È importante identificare i ruoli corretti e le liste di distribuzione di posta elettronica con il compito di gestire il ciclo di vita del certificato di firma tra Azure AD e l'applicazione configurata con Single Sign-on. Di seguito sono riportati alcuni dei ruoli chiave consigliati:

- Proprietario per l'aggiornamento delle proprietà utente nell'applicazione
- Proprietario su richiesta per il supporto di interruzioni/correzioni di applicazioni
- Lista di distribuzione di posta elettronica strettamente monitorata per le notifiche di modifica correlate ai certificati

La durata massima di un certificato è di tre anni. Si consiglia di stabilire un processo per la gestione di una modifica del certificato tra Azure AD e l'applicazione. In questo modo è possibile prevenire o ridurre al minimo un'interruzione a causa di un certificato in scadenza o forzare il rollover del certificato.

### <a name="rollback-process"></a>Rollback processo

Dopo aver completato il test in base ai test case, è possibile passare all'ambiente di produzione con l'applicazione. Il passaggio alla produzione consente di implementare le configurazioni pianificate e testate nel tenant di produzione e di distribuirle agli utenti. Tuttavia, è importante pianificare le operazioni da eseguire nel caso in cui la distribuzione non venga pianificata. Se la configurazione SSO ha esito negativo durante la distribuzione, è necessario comprendere come attenuare eventuali interruzioni e ridurre l'effetto sugli utenti.

La disponibilità dei metodi di autenticazione all'interno dell'applicazione determinerà la strategia migliore. Assicurarsi sempre di avere a disposizione una documentazione dettagliata per i proprietari delle app in modo da tornare allo stato di configurazione dell'account di accesso originale in caso di problemi di distribuzione.

- **Se l'app supporta più provider di identità**, ad esempio LDAP e ad FS e ping, non eliminare la configurazione SSO esistente durante l'implementazione. Disabilitare invece questa opzione durante la migrazione nel caso in cui sia necessario riportarla in un secondo momento. 

- **Se l'app non supporta più IDP** ma consente agli utenti di accedere usando l'autenticazione basata su form (nome utente/password), assicurarsi che gli utenti possano eseguire il fallback a questo approccio nel caso in cui la nuova implementazione della configurazione SSO non riesca.

### <a name="access-management"></a>gestione degli accessi

È consigliabile scegliere un approccio ridimensionato per la gestione dell'accesso alle risorse. Gli approcci comuni includono l'uso di gruppi locali tramite la sincronizzazione tramite Azure AD Connect, la [creazione di gruppi dinamici in Azure ad in base agli attributi utente](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)o la creazione di [gruppi self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) in Azure ad gestiti da un proprietario di risorse.

### <a name="monitor-security"></a>Monitorare la sicurezza

Si consiglia di configurare una cadenza regolare in cui si esaminano i diversi aspetti della sicurezza delle app SaaS e si eseguono le azioni correttive necessarie.

### <a name="troubleshooting"></a>risoluzione dei problemi

I collegamenti seguenti presentano scenari di risoluzione dei problemi. Potrebbe essere necessario creare una guida specifica per il personale di supporto che incorpora questi scenari e i passaggi per correggerli.

#### <a name="consent-issues"></a>Problemi di consenso

- [Errore di consenso imprevisto](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Errore di consenso utente](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problemi di accesso

- [Problemi di accesso da un portale personalizzato](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problemi durante l'accesso dal riquadro di accesso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Errore della pagina di accesso dell'applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problemi di accesso a un'applicazione Microsoft](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Problemi di SSO per le applicazioni elencate nella raccolta di applicazione Azure

- [Problema con l'accesso SSO basato su password per le applicazioni elencate nella raccolta di applicazione Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problema con SSO federato per le applicazioni elencate nella raccolta di applicazione Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Problemi di SSO per le applicazioni non elencate nella raccolta di applicazione Azure

- [Problema con la password SSO per le applicazioni non elencate nella raccolta di applicazione Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problema con SSO federato per le applicazioni non elencate nella raccolta di applicazione Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Passaggi successivi

[Eseguire il debug di Single Sign-On basato su SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Mapping delle attestazioni per le app tramite PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Personalizzazione delle attestazioni rilasciate nel token SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protocollo SAML per Single Sign-on](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Protocollo SAML per Single Sign-Out](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure ad B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (per utenti esterni, ad esempio partner e fornitori)

[Accesso condizionale di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Accesso SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Esercitazione su Application SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
