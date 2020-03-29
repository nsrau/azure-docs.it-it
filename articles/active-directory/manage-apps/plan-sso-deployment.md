---
title: Pianificare una distribuzione Single Sign-On di Azure Active DirectoryPlan an Azure Active Directory single sign-on deployment
description: Guida alla pianificazione, alla distribuzione e alla gestione di SSO nell'organizzazione.
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
ms.openlocfilehash: 92496fa572c5c1cae4588f82ac61c18de3024045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512828"
---
# <a name="plan-a-single-sign-on-deployment"></a>Pianificare una distribuzione di Single Sign-On

Single Sign-On (SSO) significa accedere a tutte le applicazioni e le risorse di cui un utente ha bisogno effettuando l'accesso una sola volta utilizzando un singolo account utente. Con SSO, gli utenti possono accedere a tutte le applicazioni necessarie senza dover eseguire l'autenticazione una seconda volta.

## <a name="benefits-of-sso"></a>Vantaggi di SSO

Single Sign-On (SSO) aggiunge sicurezza e praticità quando gli utenti accedono alle applicazioni in Azure Active Directory (Azure AD). 

Molte organizzazioni si affidano alle applicazioni SaaS (Software as a Service), ad esempio Office 365, Box e Salesforce, per la produttività degli utenti finali. Storicamente, il personale IT doveva creare e aggiornare individualmente gli account utente in ogni applicazione SaaS e gli utenti dovevano ricordare una password per ciascuno di essi.

Azure Marketplace include oltre 3000 applicazioni con connessioni SSO preintegrate, semplificando l'integrazione nel tenant.

## <a name="licensing"></a>Gestione delle licenze

- **Licenze di Azure AD:** SSO per le applicazioni SaaS preintegrate è gratuito. Tuttavia, il numero di oggetti nella directory e le funzionalità che si desidera distribuire potrebbero richiedere licenze aggiuntive. Per un elenco completo dei requisiti di licenza, vedere Prezzi di [Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licenze per** le applicazioni: sono necessarie le licenze appropriate per le applicazioni SaaS per soddisfare le esigenze aziendali. Collaborare con il proprietario dell'applicazione per determinare se gli utenti assegnati all'applicazione dispongono delle licenze appropriate per i ruoli all'interno dell'applicazione. Se Azure AD gestisce il provisioning automatico in base ai ruoli, i ruoli assegnati in Azure AD devono essere allineati al numero di licenze di proprietà dell'applicazione. Un numero non corretto di licenze possedute nell'applicazione può causare errori durante il provisioning/aggiornamento di un utente.

## <a name="plan-your-sso-team"></a>Pianifica il tuo team SSO

- **Coinvolgi le parti interessate giuste:** quando i progetti tecnologici falliscono, in genere è dovuto a aspettative non corrispondenti su impatto, risultati e responsabilità. Per evitare queste insidie, [assicurati di coinvolgere le parti interessate giuste](https://aka.ms/deploymentplans) e di comprendere i loro ruoli.
- **Pianificare le comunicazioni** - La comunicazione è fondamentale per il successo di qualsiasi nuovo servizio. Comunicare in modo proattivo agli utenti come cambierà la loro esperienza, quando cambierà e come ottenere supporto in caso di problemi. Esaminare le opzioni relative all'accesso degli utenti finali alle [applicazioni abilitate per SSO](end-user-experiences.md)e creare le comunicazioni in modo che corrispondano alla selezione effettuata. 

## <a name="plan-your-sso-protocol"></a>Pianificare il protocollo SSO

Un'implementazione SSO basata su protocolli di federazione migliora la sicurezza, l'affidabilità e le esperienze degli utenti finali ed è più facile da implementare. Molte applicazioni sono preintegrate in Azure AD con [guide dettagliate disponibili.](../saas-apps/tutorial-list.md) È possibile trovarli in [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Informazioni dettagliate su ogni metodo SSO sono disponibili nell'articolo [Single Sign-On per le applicazioni in Azure Active Directory](what-is-single-sign-on.md).

Esistono due modi principali in cui è possibile consentire agli utenti di eseguire l'accesso Single Sign-On alle app:

- **Con Single Sign-On federato** Azure AD autentica l'utente nell'applicazione usando il proprio account Azure AD. Questo metodo è supportato per le applicazioni che supportano protocolli quali SAML 2.0, WS-Federation o OpenID Connect ed è la modalità più ricca di Single Sign-On. È consigliabile usare SSO federato con Azure AD quando un'applicazione lo supporta, anziché SSO e ADFS basati su password.

- Con gli utenti **Single Sign-On basati** su password accedono all'applicazione con un nome utente e una password la prima volta, vi accedono. Agli accessi successivi sarà Azure AD a indicare il nome utente e la password all'applicazione. L'accesso Single Sign-On basato su password consente l'archiviazione e la riproduzione delle password delle applicazioni protette usando un'estensione del Web browser o un'app per dispositivi mobili. Questa opzione sfrutta il processo di accesso esistente fornito dall'applicazione, consente a un amministratore di gestire le password e non richiede all'utente di conoscere la password.

### <a name="considerations-for-federation-based-sso"></a>Considerazioni per SSO basato sulla federazioneConsiderations for federation-based SSO

- **Utilizzando OpenID Connect e OAuth:** se l'applicazione a cui ci si connette lo supporta, utilizzare il metodo OIDC/OAuth 2.0 per abilitare il SSO a tale applicazione. Questo metodo richiede meno configurazione e consente un'esperienza utente più completa. Per altre informazioni, vedere [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)e la guida per gli sviluppatori di [Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)
- **Configurazioni degli endpoint per SSO basato su SAML:** se si utilizza SAML, gli sviluppatori avranno bisogno di informazioni specifiche prima di configurare l'applicazione. Per ulteriori informazioni, consultate [Modificare la configurazione SAML di base.](configure-single-sign-on-non-gallery-applications.md)
- **Gestione dei certificati per SSO basato su SAML:** quando si abilita SSO federato per l'applicazione, Azure AD crea un certificato valido per impostazione predefinita per tre anni. Se necessario, è possibile personalizzare la data di scadenza del certificato. Assicurarsi di disporre di processi per rinnovare i certificati prima della loro scadenza. Per altre informazioni, vedere [Gestione dei certificati](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)di Azure AD .

### <a name="considerations-for-password-based-sso"></a>Considerazioni per l'accesso SSO basato su passwordConsiderations for password-based SSO

L'uso di Azure AD per SSO basato su password richiede la distribuzione di un'estensione del browser che recuperi in modo sicuro le credenziali e compili i moduli di accesso. Definire un meccanismo per distribuire l'estensione su larga scala con [i browser supportati](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Le opzioni includono:

- [Criteri di gruppo per Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Gestione configurazione per Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Download e configurazione guidati dall'utente per Chrome, Firefox, Microsoft Edge o IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Per ulteriori informazioni, vedere [Come configurare l'accesso Single Sign-On password](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Acquisizione dei metadati dei moduli di accesso per le applicazioni non presenti nella raccoltaCapturing login forms metadata for applications that aren't in the gallery

Microsoft supporta l'acquisizione di metadati in un'applicazione Web per l'archiviazione delle password (acquisizione dei campi nome utente e password). Passare all'URL di accesso durante il processo di configurazione dell'applicazione per l'acquisizione dei metadati del modulo. Chiedere al proprietario dell'applicazione l'URL di accesso esatto. Queste informazioni vengono usate durante il processo di accesso, eseguendo il mapping delle credenziali di Azure AD all'applicazione durante l'accesso.

Per altre informazioni, vedere [Che cos'è l'accesso alle applicazioni e SSO con Azure AD? – SSO basato su password](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Indicazioni che i metadati nei moduli devono essere acquisiti nuovamente

Quando le applicazioni modificano il layout HTML, potrebbe essere necessario acquisire nuovamente i metadati per adattarsi alle modifiche. I sintomi comuni che indicano che il layout HTML è stato modificato includono:

- Gli utenti segnalano che facendo clic sull'applicazione viene "bloccato" nella pagina di accesso
- Utenti che segnalano che il nome utente o la password non sono popolati

#### <a name="shared-accounts"></a>Shared accounts (Account condivisi)

Dal punto di vista dell'accesso, le applicazioni con account condivisi non sono diverse da un'applicazione della raccolta che utilizza L'accesso SSO con password per i singoli utenti. Tuttavia, esistono alcuni passaggi aggiuntivi necessari per la pianificazione e la configurazione di un'applicazione destinata all'utilizzo di account condivisi:However, there are some additional steps required when planning and configuring an application meant to use shared accounts:

1. Collaborare con gli utenti aziendali delle applicazioni per documentare quanto segue:
   1. Set di utenti nell'organizzazione che utilizzeranno l'applicazione
   1. Set di credenziali esistente nell'applicazione associata al set di utenti 
1. Per ogni combinazione di set di utenti e credenziali, creare un gruppo di sicurezza nel cloud o in locale in base alle proprie esigenze.
1. Reimpostare le credenziali condivise. Dopo aver distribuito l'app in Azure AD, i singoli utenti non necessitano della password dell'account condiviso. Poiché Azure AD archivierà la password, è consigliabile impostarla in modo che sia molto lunga e complessa. 
1. Configurare il rollover automatico della password se l'applicazione la supporta. In questo modo, nemmeno l'amministratore che ha eseguito la configurazione iniziale conoscerà la password dell'account condiviso. 

## <a name="plan-your-authentication-method"></a>Pianificare il metodo di autenticazione

La scelta del metodo di autenticazione appropriato è una prima decisione fondamentale nella configurazione di una soluzione ibrida di gestione delle identità di Azure AD. Implementare il metodo di autenticazione configurato mediante Azure AD Connect, che esegue anche il provisioning degli utenti nel cloud.

Per scegliere un metodo di autenticazione è necessario prendere in considerazione il tempo, l'infrastruttura esistente, la complessità e i costi di implementazione della propria scelta. Questi fattori variano in base all'organizzazione e possono cambiare nel corso del tempo. È consigliabile scegliere quello che corrisponde maggiormente allo scenario specifico. Per altre informazioni, vedere Scegliere il metodo di autenticazione corretto per la soluzione di identità ibrida di Azure Active Directory.For more information, see [Choose the right authentication method for your Azure Active Directory hybrid identity solution.](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn)

## <a name="plan-your-security-and-governance"></a>Pianificare la sicurezza e la governance 

L'identità è il nuovo perno principale per l'attenzione alla sicurezza e gli investimenti perché i perimetri di rete sono diventati sempre più porosi e meno efficaci con l'esplosione dei dispositivi BYOD e delle applicazioni cloud. 

### <a name="plan-access-reviews"></a>Pianificare le verifiche di accesso

[Le verifiche di accesso](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) consentono alle organizzazioni di gestire in modo efficiente le appartenenze ai gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruolo. Si dovrebbe pianificare di controllare l'accesso degli utenti su base regolare per assicurarsi che solo le persone giuste hanno accesso continuo.

Alcuni degli argomenti chiave da pianificare durante l'impostazione delle verifiche di accesso includono:

1. Identificazione di una cadenza per le verifiche di accesso che si adatta alle vostre esigenze aziendali. Questo può essere frequente come una volta alla settimana, mensile, annuale o come un esercizio su richiesta.

1. Creare gruppi che rappresentano i revisori dei report di accesso all'app. Dovrai assicurarti che le parti interessate abbiano maggiore familiarità con l'app e i suoi utenti e i casi d'uso di destinazione siano partecipanti alle tue verifiche di accesso

1. Il completamento di una verifica di accesso include l'estensione delle autorizzazioni di accesso alle app per gli utenti che non hanno più bisogno di accedere. Pianificare la gestione di potenziali richieste di supporto da parte di utenti negati. Un utente eliminato rimarrà eliminato in Azure AD per 30 giorni durante i quali può essere ripristinato da un amministratore, se necessario. Dopo 30 giorni l'utente verrà eliminato definitivamente. Usando il portale di Azure Active Directory, un amministratore globale può eliminare in modo esplicito definitivamente un utente eliminato di recente prima che venga raggiunto tale periodo di tempo.

### <a name="plan-auditing"></a>Pianificare il controllo

Azure AD fornisce [report contenenti informazioni tecniche e aziendali.](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/) 

Sono disponibili sia i rapporti sulla sicurezza che sull'attività. I report sulla sicurezza mostrano gli utenti contrassegnati per i rischi e gli accessi rischiosi. I report attività consentono di comprendere il comportamento degli utenti nell'organizzazione descrivendo in dettaglio l'attività di accesso e fornendo audit trail di tutti gli accessi. È possibile utilizzare i report per gestire i rischi, aumentare la produttività e monitorare la conformità.

| Tipo di report | Verifica di accesso | Report sulla sicurezza | Report di accesso |
|-------------|---------------|------------------|----------------|
| Utilizzare per la revisione | Autorizzazioni e utilizzo dell'applicazione. | Account potenzialmente compromessi | Chi accede alle applicazioni |
| Azioni potenziali | Controllare l'accesso; revoca delle autorizzazioni | Revoca dell'accesso; forzare la reimpostazione della sicurezza | revoca dell'accesso |

Azure AD mantiene la maggior parte dei dati di controllo per 30 giorni e li rende disponibili tramite il portale di amministrazione di Azure o tramite un'API da scaricare nei sistemi di analisi.

### <a name="consider-using-microsoft-cloud-application-security"></a>Considerare l'utilizzo di Microsoft Cloud Application Security

Microsoft Cloud App Security (MCAS) è una soluzione CASB (Cloud Access Security Broker). Ti dà visibilità sulle tue app e servizi cloud, fornisce analisi sofisticate per identificare e combattere le minacce informatiche e ti consente di controllare il modo in cui viaggiano i tuoi dati.

La distribuzione di MCAS consente di:

- Usare Cloud Discovery per mappare e identificare l'ambiente cloud e le app cloud usate dall'organizzazione.
- Sanzionare e annullare la sanzione delle app nel cloud
- Usare connettori di app facili da distribuire che sfruttano le API del provider, per la visibilità e la governance delle app a cui ci si connette
- Usa la protezione del controllo delle app con accesso condizionale per ottenere visibilità e controllo in tempo reale sull'accesso e sulle attività all'interno delle tue app cloud
- Consente di avere un controllo continuo impostando e quindi perfezionando continuamente i criteri.

Il controllo sessione Microsoft Cloud Application Security (MCAS) è disponibile per qualsiasi browser su qualsiasi piattaforma principale su qualsiasi sistema operativo. È anche possibile bloccare o consentire le app per dispositivi mobili e le app desktop. Con l'integrazione nativa con Azure AD, è possibile supportarle tutte le app configurate con SAML o le app Open ID Connect con Single Sign-On in Azure AD, incluse [le diverse app in primo piano.](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

Per informazioni su MCAS, vedere [Panoramica](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security)di Microsoft Cloud App Security . MCAS è un servizio di sottoscrizione basato sull'utente. È possibile esaminare i dettagli delle licenze nel foglio dati sulle [licenze MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Usare l'accesso condizionale

Con l'accesso condizionale, è possibile automatizzare le decisioni di controllo degli accessi basate su criteri per le app cloud.

I criteri di accesso condizionale vengono applicati dopo il completamento dell'autenticazione con primo fattore. Pertanto, l'accesso condizionale non è inteso come una difesa di prima riga per scenari come gli attacchi DoS (Denial of Service), ma può utilizzare i segnali provenienti da questi eventi per determinare l'accesso. Ad esempio, è possibile utilizzare il livello di rischio di accesso, la posizione della richiesta e così via. Per ulteriori informazioni sull'accesso condizionale, vedere [la panoramica](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) e il piano di [distribuzione](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Requisiti tecnici di Azure SSOAzure SSO technical requirements

Nella sezione seguente vengono descritti in dettaglio i requisiti per configurare l'applicazione specifica, inclusi gli ambienti necessari, gli endpoint, il mapping delle attestazioni, gli attributi obbligatori, i certificati e i protocolli utilizzati. Queste informazioni sono necessarie per configurare SSO nel portale di [Azure AD.](https://portal.azure.com/)

### <a name="authentication-mechanism-details"></a>Dettagli del meccanismo di autenticazione

Per tutte le app SaaS preintegrate, Microsoft offre un'esercitazione e queste informazioni non sono necessarie. Se l'applicazione non è nel nostro marketplace di applicazioni / galleria, potrebbe essere necessario raccogliere i seguenti pezzi di dati:

- Provider di **identità corrente utilizzato dall'applicazione per SSO se applicabile** - Ad esempio: ADFS, PingFederate, Okta
- **Protocolli supportati dall'applicazione** di destinazione: ad esempio, SAML 2.0, OpenID Connect, OAuth, autenticazione basata su moduli, WS-Fed, WS-Trust
- **Protocollo configurato con Azure AD:** ad esempio, SAML 2.0 o 1.1, OpenID Connect, OAuth, basato su moduli, WS-Fed

### <a name="attribute-requirements"></a>Requisiti degli attributi

Esiste un set preconfigurato di attributi e mapping di attributi tra gli oggetti utente di Azure AD e gli oggetti utente di ogni app SaaS. Alcune app gestiscono altri tipi di oggetti, ad esempio i gruppi. Pianificare il mapping degli attributi utente da Azure AD all'applicazione e [personalizzare i mapping](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) degli attributi predefiniti in base alle esigenze aziendali.

### <a name="certificate-requirements"></a>Requisiti del certificato

Il certificato per l'applicazione deve essere aggiornato o esiste il rischio che gli utenti non siano in grado di accedere all'applicazione. La maggior parte dei certificati di applicazione SaaS sono buoni per 36 mesi. Modificare la durata del certificato nel pannello dell'applicazione. Assicurarsi di documentare la scadenza e sapere come si gestirà il rinnovo del certificato. 

Esistono due modi per gestire i certificati. 

- **Rollover automatico dei certificati:** Microsoft supporta il rollover della chiave di firma [in Azure AD.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover) Anche se questo è il nostro metodo preferito per la gestione dei certificati, non tutti gli ISV supportano questo scenario.

- **Aggiornamento manuale:** ogni applicazione ha un proprio certificato che scade in base a come viene definita. Prima della scadenza del certificato dell'applicazione, creare un nuovo certificato e inviarlo all'ISV. Queste informazioni possono essere estratte dai metadati federativi. [Ulteriori informazioni sui metadati federativi sono disponibili qui.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementare SSO

Utilizzare le fasi seguenti per pianificare e distribuire la soluzione nell'organizzazione:

### <a name="user-configuration-for-sso"></a>Configurazione utente per SSO

- **Identificare gli utenti di test**

   Contattare il proprietario dell'app e chiedere di creare almeno tre utenti di test all'interno dell'applicazione. Verificare che le informazioni che verranno usate come identificatore primario vengano popolate correttamente e corrispondano a un attributo disponibile in Azure AD. Nella maggior parte dei casi verrà eseguito il mapping al "NameID" per le applicazioni basate su SAML. Per i token JWT, è il "preferred_username".
   
   Creare manualmente l'utente in Azure AD come utente basato su cloud o sincronizzare l'utente in locale usando il motore di sincronizzazione di Azure AD Connect.Create the user in Azure AD either manually as a cloud-based user or sync the user from on-premises using the Azure AD Connect sync engine. Verificare che le informazioni corrispondano a quelli inviati all'applicazione.

- **Configurare SSO**

   [Dall'elenco delle applicazioni](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), individuare e aprire l'esercitazione SSO per l'applicazione, quindi seguire i passaggi dell'esercitazione per configurare correttamente l'applicazione SaaS.

   Se non è possibile individuare l'applicazione, vedere [la documentazione relativa all'applicazione personalizzata](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Verrà illustrato come aggiungere un'applicazione che non si trova nella raccolta di Azure AD.

   Facoltativamente, è possibile utilizzare le attestazioni rilasciate nel token SAML per l'applicazione aziendale utilizzando [la documentazione di guida di Microsoft.](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Assicurarsi che venga eseguito il mapping a ciò che si prevede di ricevere nella risposta SAML per l'applicazione. Se si verificano problemi durante la configurazione, utilizzare le indicazioni su [come eseguire il debug dell'integrazione SSO](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

L'onboarding di applicazioni personalizzate è una funzionalità di licenza di Azure AD Premium P1 o P2.Custom application onboarding is an Azure AD Premium P1 or P2 licenses feature.

### <a name="provide-sso-change-communications-to-end-users"></a>Fornire comunicazioni di modifica SSO agli utenti finali

Implementare il piano di comunicazione. Assicurati di informare gli utenti finali che una modifica sta arrivando, quando è arrivata, cosa fare ora e come richiedere assistenza.

### <a name="verify-end-user-scenarios-for-sso"></a>Verificare gli scenari degli utenti finali per SSOVerify end user scenarios for SSO

È possibile utilizzare i test case seguenti per eseguire test su dispositivi aziendali e personali per assicurarsi che le configurazioni SSO funzionino come previsto. Gli scenari seguenti presuppongono che un utente stia passando all'URL di un'applicazione e stia passando attraverso un flusso di autenticazione avviato dal provider di servizi (flusso di autenticazione avviato da SP).

| Scenario | Risultato previsto nel flusso di autenticazione avviato da SP per utente |
|----------|---------------------------------------------------|
| Accedi all'applicazione con IE mentre sei su corpnet. | L'autenticazione integrata di Windows (IWA) si verifica senza ulteriori richieste. |
| Accedi all'applicazione con IE mentre sei fuori corpnet con un nuovo tentativo di accesso. | Prompt basato su form nel server ADFS. L'utente accede correttamente e il browser richiede l'autenticazione a più fattori. |
| Accedere all'applicazione con IE mentre è fuori corpnet con una sessione corrente e non ha mai eseguito l'autenticazione a più fattori. | L'utente non riceve la richiesta per il primo fattore. L'utente riceve la richiesta di autenticazione a più fattori. |
| Accedere all'applicazione con IE mentre è fuori corpnet con una sessione corrente e ha già eseguito l'autenticazione a più fattori in questa sessione. | L'utente non riceve la richiesta per il primo fattore. L'utente non riceve l'autenticazione a più fattori. SSO utente nell'applicazione. |
| Accedi all'applicazione con Chrome/Firefox/Safari mentre era fuori corpnet con una sessione corrente e ha già eseguito l'autenticazione a più fattori in questa sessione. | L'utente non riceve la richiesta per il primo fattore. L'utente non riceve l'autenticazione a più fattori. SSO dell'utente nell'applicazione. |
| Accedi all'applicazione con Chrome/Firefox/Safari mentre sei fuori corpnet con un nuovo tentativo di accesso. | Prompt basato su form nel server ADFS. L'utente accede correttamente e il browser richiede l'autenticazione a più fattori. |
| Accedi all'applicazione con Chrome/Firefox mentre sei in rete aziendale con una sessione corrente. | L'utente non riceve la richiesta per il primo fattore. L'utente non riceve l'autenticazione a più fattori. SSO dell'utente nell'applicazione. |
| Accedi all'applicazione con l'applicazione mobile app con un nuovo tentativo di accesso. | Prompt basato su form nel server ADFS. L'utente accede correttamente e il client ADAL richiede l'autenticazione a più fattori. |
| Un utente non autorizzato tenta di accedere all'applicazione con l'URL di accesso. | Prompt basato su form nel server ADFS. L'utente non riesce ad accedere con il primo fattore. |
| L'utente autorizzato tenta di accedere ma immette una password errata. | L'utente passa all'URL dell'applicazione e riceve un errore di nome utente/password non valido. |
| L'utente autorizzato fa clic sul collegamento in un messaggio di posta elettronica ed è già autenticato. | L'utente fa clic sull'URL e ha eseguito l'accesso all'applicazione senza ulteriori richieste. |
| L'utente autorizzato fa clic sul collegamento in un messaggio di posta elettronica e non è ancora autenticato. | L'utente fa clic sull'URL e viene richiesto di eseguire l'autenticazione con il primo fattore. |
| L'utente autorizzato accede all'applicazione con l'applicazione mobile (avvio di SP) con un nuovo tentativo di accesso. | Prompt basato su form nel server ADFS. L'utente accede correttamente e il client ADAL richiede l'autenticazione a più fattori. |
| Utente non autorizzato tenta di accedere all'applicazione con l'URL di accesso (avviato da SP). | Prompt basato su form nel server ADFS. L'utente non riesce ad accedere con il primo fattore. |
| L'utente autorizzato tenta di accedere ma immette una password errata.| L'utente passa all'URL dell'applicazione e riceve un errore di nome utente/password non valido. |
| L'utente autorizzato si disconnette e quindi accede di nuovo. | Se l'URL di disconnessione è configurato, l'utente è disconnesso da tutti i servizi e richiede di eseguire l'autenticazione. |
| L'utente autorizzato si disconnette e quindi accede di nuovo. | Se l'URL di disconnessione non è configurato, l'utente verrà registrato automaticamente usando il token esistente dalla sessione del browser di Azure AD esistente. |
| L'utente autorizzato fa clic sul collegamento in un messaggio di posta elettronica ed è già autenticato. | L'utente fa clic sull'URL e ha eseguito l'accesso all'applicazione senza ulteriori richieste. |
| L'utente autorizzato fa clic sul collegamento in un messaggio di posta elettronica e non è ancora autenticato. | L'utente fa clic sull'URL e viene richiesto di eseguire l'autenticazione con il primo fattore. |

## <a name="manage-sso"></a>Gestire SSO

In questa sezione vengono descritti i requisiti e i consigli per gestire correttamente SSO.

### <a name="required-administrative-roles"></a>Ruoli amministrativi necessari

Usare sempre il ruolo con il minor numero di autorizzazioni disponibili per eseguire l'attività richiesta in Azure Active Directory.Always use the role with the few few permissions available to accomplish the required task within Azure Active Directory. Microsoft consiglia di [esaminare i diversi ruoli disponibili](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e scegliere quello giusto per risolvere le proprie esigenze per ogni persona per questa applicazione. Alcuni ruoli potrebbero essere necessario applicare temporaneamente e rimuovere dopo il completamento della distribuzione.

| Persona| Ruoli | Ruolo di Azure AD (se necessario)Azure AD role (if required) |
|--------|-------|-----------------------------|
| Amministratore dell'help desk | Supporto per il livello 1 | nessuno |
| Amministratore identità | Configurare ed eseguire il debug quando i problemi influiscono su Azure ADConfigure and debug when issues impact Azure AD | Amministratore globale |
| Amministratore dell'applicazione | Attestazione dell'utente nell'applicazione, configurazione per gli utenti con autorizzazioni | nessuno |
| Amministratori dell'infrastruttura | Proprietario rollover del certificatoCert rollover owner | Amministratore globale |
| Imprenditore/stakeholder | Attestazione dell'utente nell'applicazione, configurazione per gli utenti con autorizzazioni | nessuno |

È consigliabile utilizzare [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) per gestire i ruoli al base di fornire ulteriori revisioni di controllo, controllo e accesso per gli utenti con autorizzazioni di directory.

### <a name="sso-certificate-lifecycle-management"></a>Gestione del ciclo di vita dei certificati SSO

È importante identificare i ruoli e le liste di distribuzione di posta elettronica corretti con la gestione del ciclo di vita del certificato di firma tra Azure AD e l'applicazione configurata con Single Sign-On. Ecco alcuni dei ruoli chiave che consigliamo di avere sul posto:

- Proprietario per l'aggiornamento delle proprietà utente nell'applicazione
- Proprietario on-Call per il supporto di interruzione dell'applicazione/correzione
- Lista di distribuzione della posta elettronica strettamente monitorata per le notifiche di modifica relative ai certificati

La durata massima di un certificato è di tre anni. È consigliabile stabilire un processo su come gestire una modifica del certificato tra Azure AD e l'applicazione. In questo modo è possibile evitare o ridurre al minimo un'interruzione dovuta alla scadenza di un certificato o forzare il rollover del certificato.

### <a name="rollback-process"></a>Processo di rollback

Dopo aver completato i test in base ai test case, è il momento di passare all'ambiente di produzione con l'applicazione. Il passaggio alla produzione significa che implementerai le configurazioni pianificate e testate nel tenant di produzione e le distribuirai agli utenti. Tuttavia, è importante pianificare le operazioni da eseguire nel caso in cui la distribuzione non vada come pianificato. Se la configurazione SSO non riesce durante la distribuzione, è necessario comprendere come ridurre eventuali interruzioni e ridurre l'impatto sugli utenti.

La disponibilità dei metodi di autenticazione all'interno dell'applicazione determinerà la strategia migliore. Assicurarsi sempre di avere una documentazione dettagliata per i proprietari di app su esattamente come tornare allo stato di configurazione dell'accesso originale nel caso in cui la distribuzione si verificano problemi.

- **Se l'app supporta più provider di identità,** ad esempio LDAP e ADFS e Ping, non eliminare la configurazione SSO esistente durante l'implementazione. Disabilitarlo invece durante la migrazione nel caso in cui sia necessario tornare in un secondo momento. 

- **Se l'app non supporta più IDP** ma consente agli utenti di accedere usando l'autenticazione basata su moduli (nome utente/password), assicurati che gli utenti possano eseguire il rollback a questo approccio nel caso in cui la nuova implementazione della configurazione SSO non riesca.

### <a name="access-management"></a>Gestione degli accessi

È consigliabile scegliere un approccio con scalabilità quando si gestisce l'accesso alle risorse. Gli approcci comuni includono l'utilizzo di gruppi locali tramite la sincronizzazione tramite Azure AD Connect, la creazione di [gruppi dinamici in Azure AD in base agli attributi utente](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)o la creazione di gruppi [self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) in Azure AD gestiti da un proprietario della risorsa.

### <a name="monitor-security"></a>Monitora la sicurezza

Ti consigliamo di impostare una cadenza regolare in base alla quale esaminare i diversi aspetti della sicurezza delle app SaaS ed eseguire le azioni correttive necessarie.

### <a name="troubleshooting"></a>Risoluzione dei problemi

I collegamenti seguenti presentano scenari di risoluzione dei problemi. È possibile creare una guida specifica per il personale di supporto che incorpora questi scenari e i passaggi per risolverli.

#### <a name="consent-issues"></a>Problemi di consenso

- [Errore di consenso imprevisto](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Errore di consenso utente](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problemi di accesso

- [Problemi di accesso da un portale personalizzato](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problemi durante l'accesso dal pannello di accesso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Errore della pagina di accesso dell'applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problema di accesso a un'applicazione Microsoft](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>SSO issues for applications listed in the Azure Application Gallery

- [Problema con password SSO per le applicazioni elencate nella raccolta di applicazioni di Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problema con SSO federato per le applicazioni elencate nella raccolta di applicazioni di AzureProblem with federated SSO for applications listed in the Azure Application Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>SSO issues for applications NOT listed in the Azure Application Gallery

- [Problema con password SSO per le applicazioni NON elencate nella raccolta di applicazioni di Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problema con SSO federato per le applicazioni NON elencate nella raccolta di applicazioni di AzureProblem with federated SSO for applications NOT listed in the Azure Application Gallery](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Passaggi successivi

[Eseguire il debug di Single Sign-On basato su SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Mapping delle attestazioni per le app tramite PowerShellClaim mapping for Apps via PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Personalizzazione delle attestazioni emesse nel token SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protocollo SAML Single Sign-On](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Protocollo SAML Single Sign-Out](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (per utenti esterni, ad esempio partner e fornitori)Azure AD B2B (for external users such as partners and vendors)

[Azure AD Conditional Access](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Protezione delle identità di AzureAzure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Accesso SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Esercitazione SSO dell'applicazione](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
