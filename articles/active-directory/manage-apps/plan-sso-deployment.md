---
title: Pianificare una Azure Active Directory single sign-on di distribuzione
description: Guida che consentono di pianificare, distribuire e gestire l'accesso SSO nell'organizzazione.
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
ms.openlocfilehash: 29569302d20e23c95b6508a5b58c7ed96e005885
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499250"
---
# <a name="plan-a-single-sign-on-deployment"></a>Pianificare una distribuzione di single sign-on

Accesso Single sign-on (SSO) significa che l'accesso a tutte le applicazioni e risorse di un utente deve effettuando l'accesso una sola volta con un singolo account utente. Con SSO, gli utenti possono accedere le applicazioni tutte le necessarie senza dover effettuare l'autenticazione di una seconda volta.

## <a name="benefits-of-sso"></a>Vantaggi dell'accesso SSO

Accesso Single sign-on (SSO) aggiunge sicurezza e praticità quando gli utenti accedano alle applicazioni in Azure Active Directory (Azure AD). 

Molte organizzazioni si basano sul software come un servizio (SaaS) le applicazioni, ad esempio Office 365, Box e Salesforce, per la produttività degli utenti finali. In passato, il personale IT doveva creare e aggiornare gli account utente in ciascuna applicazione SaaS e gli utenti dovevano ricordare una password per ognuna singolarmente.

Azure Marketplace ha oltre 3.000 applicazioni preintegrate connessioni di accesso SSO, rendendo più semplice integrarle nel tenant.

## <a name="licensing"></a>Licenze

- **Licenze di Azure AD** -SSO per applicazioni SaaS preintegrate è gratuito. Tuttavia, il numero di oggetti nella directory e le funzionalità che si desidera distribuire può richiedere licenze aggiuntive. Per un elenco completo dei requisiti di licenza, vedere [sui prezzi di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
- **Gestione delle licenze applicazione** -sarà necessario le licenze appropriate per le applicazioni SaaS in base alle esigenze aziendali. Rivolgersi al proprietario dell'applicazione per determinare se gli utenti assegnati all'applicazione hanno le licenze appropriate per i ruoli all'interno dell'applicazione. Se Azure AD gestisce il provisioning automatico basato sui ruoli, i ruoli assegnati in Azure AD devono essere allineati con il numero di licenze di proprietà all'interno dell'applicazione. Numero non corretto di licenze di proprietà dell'applicazione può causare errori durante il provisioning o nell'aggiornamento di un utente.

## <a name="plan-your-sso-team"></a>Pianificare il team SSO

- **Coinvolgere gli stakeholder a destra** : quando la tecnologia progetti hanno esito negativo, si tratta in genere non corrispondono alle aspettative in impatto, risultati e le responsabilità. Per evitare questi inconvenienti [assicurarsi che si sta coinvolgere gli stakeholder a destra](https://aka.ms/deploymentplans) e che gli stakeholder comprendano i relativi ruoli.
- **Pianificare le comunicazioni** -comunicazione è fondamentale per il successo di qualsiasi nuovo servizio. Comunicare in modo proattivo per gli utenti adiacenti in che modo l'esperienza cambieranno, quando verrà modificato e come ottenere il supporto tecnico se si verificano problemi. Esaminare le opzioni per [modo in cui gli utenti finali avranno accesso loro l'accesso SSO di applicazioni abilitate](end-user-experiences.md)e creare le comunicazioni in modo che corrisponda alla selezione. 

## <a name="plan-your-sso-protocol"></a>Pianificare il protocollo SSO

Migliora la sicurezza, affidabilità, un'implementazione di SSO basata su protocolli di federazione e utente finale esperienze ed è più facile da implementare. Molte applicazioni sono già integrate in Azure AD con [procedura dettagliata Guida disponibile](../saas-apps/tutorial-list.md). È possibile trovarli nel nostro [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Informazioni dettagliate su ogni metodo SSO sono reperibili nell'articolo [Single sign-on alle applicazioni in Azure Active Directory](what-is-single-sign-on.md).

Esistono due modi principali in cui è possibile abilitare gli utenti per l'accesso single sign-on alle App:

- **Con single sign-on federato** Azure AD autentica l'utente all'applicazione tramite il proprio account Azure AD. Questo metodo è supportato per le applicazioni che supporto protocolli quali SAML 2.0, WS-Federation oppure OpenID Connect ed è la modalità più ricca di single sign-on. È consigliabile usare l'accesso SSO federato con Azure AD quando un'applicazione la supporta, invece di SSO basato su password e ad FS.

- **Basato su password single sign-on** gli utenti accedere la prima volta, l'applicazione con un nome utente e una password che accedono ad esso. Agli accessi successivi sarà Azure AD a indicare il nome utente e la password all'applicazione. L'accesso Single Sign-On basato su password consente l'archiviazione e la riproduzione delle password delle applicazioni protette usando un'estensione del Web browser o un'app per dispositivi mobili. Questa opzione sfrutta il processo di accesso esistente fornito dall'applicazione, consente agli amministratori di gestire le password e non richiede all'utente di conoscere la password.

### <a name="considerations-for-federation-based-sso"></a>Considerazioni per l'accesso SSO basato su federazione

- **Usando OpenID Connect e OAuth** : se l'applicazione a cui ci si connette a supporta, usare il metodo OIDC/OAuth 2.0 per abilitare il SSO all'applicazione. Questo metodo richiede la configurazione in meno e consente un'esperienza utente. Per altre informazioni, vedere [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md), e [Guida per gli sviluppatori di Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Le configurazioni di endpoint per l'accesso SSO basato su SAML** -se si usa SAML, gli sviluppatori saranno necessario informazioni specifiche prima di configurare l'applicazione. Per altre informazioni, vedi [configurare le opzioni di base SAML](configure-single-sign-on-portal.md).
- **Certificato di gestione per l'accesso SSO basato su SAML** : quando si abilita l'accesso SSO federato per l'applicazione, Azure AD crea un certificato per impostazione predefinita valido per tre anni. È possibile personalizzare la data di scadenza per il certificato se necessario. Assicurarsi di avere i processi in grado di rinnovare i certificati prima della loro scadenza. Per altre informazioni, vedere [certificati di gestione di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Considerazioni per l'accesso SSO basato su password

Uso di Azure AD per l'accesso SSO basato su password richiede la distribuzione di un'estensione del browser in modo sicuro recuperare le credenziali e compilare i moduli di account di accesso. Definire un meccanismo per distribuire l'estensione su larga scala con [browser supportati](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Le opzioni includono:

- [Criteri di gruppo per Internet Explorer ](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [System Center Configuration Manager (SCCM) per Internet Explorer ](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [Utente basato su download e la configurazione per Chrome, Firefox, Microsoft Edge o Internet Explorer ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Per altre informazioni, vedere [modo in cui configurare password single sign-on](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Acquisizione dei metadati di form account di accesso per le applicazioni non presenti nella raccolta

Microsoft supporta l'acquisizione dei metadati su un'applicazione web per la password (acquisizione i campi nome utente e password) di archiviazione. Passare all'URL di accesso durante il processo di configurazione dell'applicazione per acquisire i metadati del form. Chiedere al proprietario dell'applicazione per l'URL di accesso esatto. Queste informazioni viene utilizzate durante il processo sign-on, il mapping di credenziali di Azure AD all'applicazione durante l'accesso.

Per altre informazioni, vedere [qual è l'accesso alle applicazioni e l'accesso SSO con Azure AD? – SSO basato su Password](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Le indicazioni che i metadati nei moduli devono liberare

Quando le applicazioni modificano il layout HTML, è necessario riacquisire i metadati in modo che le modifiche. Sintomi comuni che indicano che il layout HTML ha modifiche includono:

- Utenti report che facendo clic sull'applicazione ottiene "bloccato" nella pagina di accesso
- Utenti report che non venga compilata il nome utente o password

#### <a name="shared-accounts"></a>Account condivisi

Dalla prospettiva dell'accesso, le applicazioni con gli account condivisi non sono diverse da un'applicazione della raccolta che usa SSO basato su password per i singoli utenti. Tuttavia, esistono alcuni passaggi aggiuntivi necessari quando la pianificazione e configurazione di un'applicazione deve usare gli account condivisi:

1. Lavorare con gli utenti di business dell'applicazione per documentare quanto segue:
   1. Set di utenti dell'organizzazione che useranno l'applicazione
   1. Insieme di credenziali nell'applicazione associato al set di utenti esistente 
1. Per ogni combinazione di set di utenti e le credenziali, creare un gruppo di sicurezza nel cloud o in locale in base alle esigenze.
1. Reimpostare le credenziali condivise. Dopo che l'app viene distribuita in Azure AD, persone non necessaria la password dell'account condiviso. Poiché la password verrà archiviata da Azure AD, si consiglia di impostare che sia molto lunghe e complesse. 
1. Se l'applicazione la supporta, configurare il rollover automatico della password. In questo modo, non lo è nemmeno l'amministratore che ha eseguito la configurazione iniziale verrà conoscere la password dell'account condiviso. 

## <a name="plan-your-authentication-method"></a>Pianificare il metodo di autenticazione

La scelta del metodo di autenticazione appropriato è una prima decisione fondamentale nella configurazione di una soluzione ibrida di gestione delle identità di Azure AD. Implementare il metodo di autenticazione configurato mediante Azure AD Connect, che esegue anche il provisioning degli utenti nel cloud.

Per scegliere un metodo di autenticazione è necessario prendere in considerazione il tempo, l'infrastruttura esistente, la complessità e i costi di implementazione della propria scelta. Questi fattori variano in base all'organizzazione e possono cambiare nel corso del tempo. È consigliabile scegliere quello che corrisponde maggiormente allo scenario specifico. Per altre informazioni, vedere [scegliere il metodo di autenticazione appropriato per la soluzione di identità ibrida di Azure Active Directory](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).

## <a name="plan-your-security-and-governance"></a>Pianificare la sicurezza e governance 

Identità infatti nuovo pivot primario per la sicurezza attenzione e investimenti perimetri di rete sono diventati sempre più permeabili e meno efficace enorme diffusione di dispositivi BYOD e applicazioni cloud. 

### <a name="plan-access-reviews"></a>Pianificare le verifiche di accesso

[Le verifiche di accesso](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) consentono alle organizzazioni di gestire le appartenenze, accedere ad applicazioni aziendali e le assegnazioni di ruolo in modo efficiente. È consigliabile verificare l'accesso utente a intervalli regolari per assicurarsi che solo le persone giuste abbiano un accesso continuo.

Alcuni argomenti chiave pianificare durante l'impostazione delle verifiche di accesso includono:

1. Che identifica un ritmo adatto alle esigenze aziendali per le verifiche di accesso. Può trattarsi di frequente come una volta a settimana, ogni mese, anno o come un esercizio on demand.

1. Creare gruppi che rappresentano i revisori dei report di accesso app. È necessario assicurarsi che le parti interessate che conosce l'app agli utenti di destinazione e i relativi casi di utilizzo sono i partecipanti di verifiche di accesso

1. Completare una verifica di accesso include sottragga autorizzazioni di accesso delle app agli utenti che non è più necessario accedere. Pianificare la gestione di richieste di supporto potenziali da utenti negati. Un utente eliminato rimarrà eliminato in Azure AD per 30 giorni durante i quali possono essere ripristinati da un amministratore se necessario. Dopo 30 giorni l'utente verrà eliminato definitivamente. Tramite il portale di Azure Active Directory, un amministratore globale può in modo esplicito eliminare definitivamente un utente eliminato di recente prima che venga raggiunto quel periodo di tempo.

### <a name="plan-auditing"></a>Pianificare il controllo

Azure AD offre [report che contengono informazioni dettagliate di tecnici e aziendali](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). 

Sicurezza e report delle attività sono entrambe disponibili. I report di sicurezza mostrano gli utenti contrassegnati per il rischio e accessi a rischio. I report di attività consentono di comprendere il comportamento degli utenti nell'organizzazione mediante l'aggiunta di dettagli attività di accesso e audit trail di tutti gli accessi. È possibile usare i report per gestire i rischi, aumentare la produttività e monitorare la conformità.

| Tipo di report | Verifica di accesso | Report sulla sicurezza | Report sugli accessi |
|-------------|---------------|------------------|----------------|
| Consente di esaminare | Autorizzazioni applicazione e l'utilizzo. | Account potenzialmente compromesso | Chi accede le applicazioni |
| Impatto potenziale delle azioni | Controllo dell'accesso; REVOKE-autorizzazioni per | Revocare l'accesso; forzare la reimpostazione della sicurezza | Revocare l'accesso |

Azure AD mantiene la maggior parte dei dati di controllo per 30 giorni e rende i dati disponibili tramite il portale di amministrazione di Azure o tramite un'API per il download nei sistemi di analisi.

### <a name="consider-using-microsoft-cloud-application-security"></a>È consigliabile usare Microsoft Cloud Application Security

Microsoft Cloud App Security (MCAS) è una soluzione Cloud Access Security Service Broker (CASB). Ti offre visibilità nelle App cloud e servizi, offre sofisticati analitica per identificare e contrastare minacce informatiche e consente di controllare come i dati si sposti.

Distribuzione MCAS consente di:

- Usare Cloud Discovery per eseguire il mapping e identificare l'ambiente cloud e delle App cloud che nell'organizzazione vengono utilizzati.
- Approvazione e annullamento-approvare le app nel cloud
- Usare i connettori app facili da distribuire che si avvalgono di API del provider, per ottenere visibilità e governance delle App a cui ci si connette a
- Usare Conditional Access App Control protection per ottenere visibilità in tempo reale e il controllo degli accessi e le attività all'interno delle App cloud
- Consente di avere controllo continuo grazie all'impostazione e all'ottimizzazione costante, i criteri.

Controllo delle sessioni di Microsoft Cloud Application Security (MCAS) è disponibile per qualsiasi browser su qualunque piattaforma principale in qualsiasi sistema operativo. App per dispositivi mobili e App desktop bloccata o è possibile consentita. Eseguendo l'integrazione nativa con Azure AD, qualsiasi App che sono configurate con SAML o Openid Connect App con single sign-on di Azure AD possono essere supportate, compresi [diverse App in primo piano](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad).

Per informazioni su MCAS, vedere la [Panoramica di Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). MCAS è un servizio di sottoscrizione basata sull'utente. È possibile esaminare i dettagli sulle licenze nel [foglio dati di licenza MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Usare l'accesso condizionale

Grazie all'accesso condizionale, è possibile automatizzare le decisioni di controllo di accesso basato su criteri per le app cloud.

I criteri di accesso condizionale vengono applicati dopo il completamento dell'autenticazione con primo fattore. Pertanto, l'accesso condizionale non è come una prima linea di difesa per gli scenari, ad esempio attacchi di tipo denial of service (DoS), ma è possibile usare i segnali provenienti da questi eventi per determinare l'accesso. Ad esempio il livello di rischio di accesso, è utilizzabile percorso della richiesta e così via. Per altre informazioni sull'accesso condizionale, vedere [la panoramica](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) e il [piano di distribuzione](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Requisiti tecnici di Azure l'accesso SSO

La sezione seguente vengono illustrati i requisiti di configurazione specifiche dell'applicazione inclusi necessarie negli ambienti, gli endpoint, il mapping di attestazioni, gli attributi obbligatori, certificati e i protocolli usati. È necessario che queste informazioni per configurare SSO nel [portale di Azure AD](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Dettagli del meccanismo di autenticazione

Per tutte le app SaaS preintegrate, Microsoft fornisce un'esercitazione e non sarà necessario queste informazioni. Se l'applicazione non è presente nel nostro marketplace di applicazioni / Galleria, potrebbe essere necessario raccogliere le seguenti porzioni di dati:

- **Provider di identità corrente l'applicazione usa per l'accesso SSO, se applicabile** , ad esempio: AD FS, PingFederate, Okta
- **Protocolli supportati dall'applicazione di destinazione** , ad esempio SAML 2.0, OpenID Connect, OAuth, l'autenticazione basata su form, WS-Fed, WS-Trust
- **Protocollo da configurati con Azure AD** , ad esempio SAML 2.0 o 1.1, OpenID Connect, OAuth, basata su form, WS-Fed

### <a name="attribute-requirements"></a>Requisiti relativi agli attributi

È presente un set preconfigurato di attributi e mapping degli attributi tra gli oggetti utente di ogni app SaaS e gli oggetti utente di Azure AD. Alcune App gestiscono altri tipi di oggetti, ad esempio i gruppi. Pianificare il mapping degli attributi utente da Azure AD all'applicazione e [personalizzare il mapping di attributi predefiniti](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) in base alla tua azienda necessita.

### <a name="certificate-requirements"></a>Requisiti dei certificati

Il certificato per l'applicazione deve essere aggiornato oppure è un rischio per gli utenti non possano accedere all'applicazione. La maggior parte dei certificati delle applicazioni SaaS sono ideali per 36 mesi. Modificare la durata effettiva certificato nel pannello dell'applicazione. Assicurarsi di documentare la scadenza e sapere come gestire il rinnovo del certificato. 

Esistono due modi per gestire i certificati. 

- **Rollover automatico dei certificati** -Microsoft supporta [rollover della chiave di firma in Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Sebbene questo sia il metodo preferito per la gestione dei certificati, non tutti i ISV supporta questo scenario.

- **Aggiornamento manuale** -tutte le applicazioni con il proprio certificato con scadenza basato sul modo in cui è definito. Prima di scadenza del certificato dell'applicazione, creare un nuovo certificato e inviarlo al fornitore di software indipendente. Queste informazioni possono essere recuperate dai metadati della federazione. [Per altre informazioni sui metadati di federazione di seguito.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementare l'accesso SSO

Usare le fasi seguenti per pianificare e distribuire la soluzione all'interno dell'organizzazione:

### <a name="user-configuration-for-sso"></a>Configurazione utente per l'accesso SSO

- **Identificare gli utenti di test**

   Rivolgersi al proprietario dell'app e chiedere di creare un minimo di tre test utenti all'interno dell'applicazione. Verificare le informazioni che si userà come identificatore di primario vengano popolate in modo corretto e corrisponde a un attributo che è disponibile in Azure AD. Nella maggior parte dei casi questo verrà eseguito il mapping per l'elemento "NameID" per le applicazioni basate su SAML. Per i token JWT, è il "preferred_username".
   
   Creare l'utente in Azure AD sia manualmente con un account utente basati sul cloud o locale usando il motore di sincronizzazione Azure AD Connect all'utente di sincronizzazione. Verificare che le informazioni corrispondano le attestazioni inviate all'applicazione.

- **Configurare l'accesso SSO**

   Dal [elenco delle applicazioni](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), individuare e aprire l'esercitazione SSO per l'applicazione, quindi seguire i passaggi dell'esercitazione per configurare correttamente l'applicazione SaaS.

   Se non è possibile individuare l'applicazione, vedere [documentazione dell'applicazione personalizzata](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). Si illustrerà su come aggiungere un'applicazione che non si trova nella raccolta di Azure AD.

   Facoltativamente, è possibile usare le attestazioni rilasciate nel token SAML dell'applicazione aziendale mediante [documentazione di indicazioni di Microsoft](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Verificare che esegue il mapping a ciò che si prevede di ricevere nella risposta SAML per l'applicazione. Se si verificano problemi durante la configurazione, usare le istruzioni riportate in [come l'integrazione SSO di eseguire il Debug](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

Onboarding dell'applicazione personalizzata è una funzionalità di licenze Azure AD Premium P1 o P2.

### <a name="provide-sso-change-communications-to-end-users"></a>Fornire comunicazioni modifica SSO agli utenti finali

Implementare il piano di comunicazione. Assicurarsi che si sta comunica agli utenti finali che una modifica in ingresso, quando arriva, operazioni da eseguire a questo punto e come richiedere assistenza.

### <a name="verify-end-user-scenarios-for-sso"></a>Verificare gli scenari utente finale per l'accesso SSO

È possibile usare i seguenti test case per condurre test in azienda e dispositivi personali per assicurare le configurazioni SSO funzionino come previsto. Gli scenari seguenti presuppongono che un utente è passando a un URL di applicazione e passare attraverso un flusso di autenticazione avviato dal provider di servizi (SP-initiated Authentication flow).

| Scenario | Risultato previsto su SP-initiated Authentication flow dall'utente |
|----------|---------------------------------------------------|
| Account di accesso all'applicazione con Internet Explorer nella rete aziendale. | L'autenticazione integrata di Windows (IWA) si verifica alcun prompt aggiuntivi. |
| Account di accesso all'applicazione con Internet Explorer mentre fuori della rete aziendale con nuovo tentativo di accesso. | Richiesta basata su form al Server AD FS. Utente accede correttamente e browser richiede autenticazione a più fattori. |
| Account di accesso all'applicazione con Internet Explorer mentre fuori della rete aziendale con una sessione corrente e non ha mai eseguito l'autenticazione a più fattori. | L'utente non riceva prompt per primo fattore. L'utente riceve prompt dei comandi per MFA. |
| Account di accesso all'applicazione con Internet Explorer mentre fuori della rete aziendale con una sessione corrente e MFA ha già eseguito nella sessione corrente. | L'utente non riceva prompt per primo fattore. L'utente non riceva MFA. Sicurezza utente nell'applicazione. |
| Account di accesso all'applicazione con Firefox/Chrome/Safari mentre fuori della rete aziendale con una sessione corrente e MFA ha già eseguito nella sessione corrente. | L'utente non riceva prompt per primo fattore. L'utente non riceva MFA. Utente dell'accesso SSO nell'applicazione. |
| Account di accesso per applicazione con Firefox/Chrome/Safari mentre fuori della rete aziendale con nuovo tentativo di accesso. | Richiesta basata su form al Server AD FS. Utente accede correttamente e browser richiede autenticazione a più fattori. |
| Account di accesso all'applicazione con Chrome o Firefox mentre nella rete aziendale con una sessione corrente. | L'utente non riceva prompt per primo fattore. L'utente non riceva MFA. Utente dell'accesso SSO nell'applicazione. |
| Account di accesso all'applicazione con app per dispositivi mobili dell'applicazione con un nuovo tentativo di accesso. | Richiesta basata su form al Server AD FS. Utente accede correttamente e chiede client ADAL per MFA. |
| Utente non autorizzato prova ad accedere nell'applicazione con l'URL di accesso. | Richiesta basata su form al Server AD FS. Utente non riesce a eseguire l'accesso con fattore di prima. |
| Utente autorizzato tenta di eseguire l'accesso ma immette una password errata. | Utente passa all'URL dell'applicazione e riceve errori di nome utente/password errata. |
| Utente autorizzato fa clic sul collegamento nel messaggio di posta elettronica ed è già autenticato. | Utente fa clic sull'URL e viene eseguito l'accesso all'applicazione senza prompt aggiuntivi. |
| Utente autorizzato fa clic sul collegamento nel messaggio di posta elettronica e non è ancora autenticata. | Utente fa clic sull'URL ed è richiesta per l'autenticazione tramite il primo fattore. |
| Autorizzato utente esegue l'applicazione con app per dispositivi mobili dell'applicazione (avviato da SP) con un nuovo tentativo di accesso. | Richiesta basata su form al Server AD FS. Utente accede correttamente e chiede client ADAL per MFA. |
| Utente non autorizzato prova ad accedere nell'applicazione con l'URL di accesso (avviato da SP). | Richiesta basata su form al Server AD FS. Utente non riesce a eseguire l'accesso con fattore di prima. |
| Utente autorizzato tenta di eseguire l'accesso ma immette una password errata.| Utente passa all'URL dell'applicazione e riceve errori di nome utente/password errata. |
| Utente autorizzato esegue la disconnessione e quindi si riconnette. | Se viene configurato l'URL di disconnessione, l'utente è connesso all'esterno di tutti i servizi e prompt dei comandi per eseguire l'autenticazione. |
| Utente autorizzato esegue la disconnessione e quindi si riconnette. | Se l'URL di disconnessione non è configurato, utente verrà automaticamente registrato tramite token esistente dalla sessione del browser di Azure AD esistente. |
| Utente autorizzato fa clic sul collegamento nel messaggio di posta elettronica ed è già autenticato. | Utente fa clic sull'URL e viene eseguito l'accesso all'applicazione senza prompt aggiuntivi. |
| Utente autorizzato fa clic sul collegamento nel messaggio di posta elettronica e non è ancora autenticata. | Utente fa clic sull'URL ed è richiesta per l'autenticazione tramite il primo fattore. |

## <a name="manage-sso"></a>Gestire l'accesso SSO

Questa sezione vengono descritti i requisiti e suggerimenti per gestire l'accesso SSO.

### <a name="required-administrative-roles"></a>Ruoli amministrativi necessari

Usare sempre il ruolo con il minor numero di autorizzazioni disponibile per eseguire l'attività necessaria in Azure Active Directory. Microsoft consiglia [esaminare i diversi ruoli disponibili](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) e scegliere quello giusto per soddisfare le esigenze per ogni utente tipo per questa applicazione. Alcuni ruoli debba essere applicata temporaneamente e rimossi dopo la distribuzione è stata completata.

| Utente tipo| Ruoli | Ruolo di Azure AD (se richiesto) |
|--------|-------|-----------------------------|
| Amministratore di help desk | Supporto di livello 1 | Nessuna |
| Amministratore di identità | Configurare ed eseguire il debug quando l'impatto di problemi di Azure AD | Amministratore globale |
| Amministratore applicazione | Attestazione utente nell'applicazione, configurazione sugli utenti con autorizzazioni | Nessuna |
| Amministratori dell'infrastruttura | Proprietario del rollover del certificato | Amministratore globale |
| Proprietario/stakeholder aziendali | Attestazione utente nell'applicazione, configurazione sugli utenti con autorizzazioni | Nessuna |

È consigliabile usare [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) per gestire i ruoli per fornire altri controllo, controllo e accesso esaminare per gli utenti con autorizzazioni di directory.

### <a name="sso-certificate-lifecycle-management"></a>Gestione del ciclo di vita dei certificati SSO

È importante identificare i ruoli corretti e inviare tramite posta elettronica a liste di distribuzione il compito di gestire il ciclo di vita del certificato di firma tra Azure AD e l'applicazione che viene configurato con single sign-on. Ecco alcuni dei ruoli fondamentali che è consigliabile avere posto:

- Proprietario per l'aggiornamento delle proprietà utente nell'applicazione
- Proprietario chiamata per supporto in garanzia dell'applicazione
- Lista di distribuzione monitorato attentamente messaggio di posta elettronica per le notifiche delle modifiche relative ai certificati

La durata massima di un certificato è tre anni. È consigliabile stabilire un processo su come gestire una modifica di certificati tra Azure AD e l'applicazione. Ciò consente di impedire o ridurre al minimo un'interruzione a causa di un certificato in scadenza o forzare il rollover del certificato.

### <a name="rollback-process"></a>Processo di rollback

Dopo aver completato il test basato su test case, è possibile spostare nell'ambiente di produzione con l'applicazione. Il passaggio all'ambiente di produzione implica verrà implementare le configurazioni pianificate e testate nel tenant di produzione e distribuirlo agli utenti. Tuttavia, è importante pianificare operazioni da eseguire nel caso in cui la distribuzione non viene accettata come previsto. Se la configurazione di SSO ha esito negativo durante la distribuzione, è necessario capire come prevenire eventuali interruzioni e ridurre l'impatto per gli utenti.

La disponibilità dei metodi di autenticazione all'interno dell'applicazione è determineranno la strategia migliore. Assicurarsi sempre che si have documentazione dettagliata per i proprietari di app su esattamente come tornare allo stato di configurazione di account di accesso originale nel caso in cui viene eseguita la distribuzione dei problemi.

- **Se l'app supporta più provider di identità**, ad esempio LDAP e AD FS e Ping, non eliminare la configurazione di SSO esistente durante l'implementazione. Al contrario, disabilitarlo durante la migrazione nel caso in cui è necessario riportarlo indietro in un secondo momento. 

- **Se l'app non supporta più IDP** ma consente agli utenti di accedere usando l'autenticazione basata su form (nome utente/password), assicurarsi che gli utenti possono ricorrere a questo approccio nel caso in cui la nuova implementazione di configurazione SSO avrà esito negativo.

### <a name="access-management"></a>gestione degli accessi

È consigliabile scegliere un approccio con scalabilità quando gestiscono l'accesso alle risorse. Approcci comuni tra cui l'uso di gruppi locali tramite sincronizzazione con Azure AD Connect, [Creazione gruppi dinamici in Azure AD in base agli attributi utente](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal), o creando [gruppi self-service](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) in Azure AD gestito da un proprietario della risorsa.

### <a name="monitor-security"></a>Monitoraggio della protezione

È consigliabile configurare una cadenza regolare in cui si esaminano i diversi aspetti della sicurezza delle app SaaS ed eseguire eventuali azioni correttive necessarie.

### <a name="troubleshooting"></a>risoluzione dei problemi

I collegamenti seguenti presentano gli scenari di risoluzione dei problemi. È possibile creare una Guida specifica per il personale di supporto che incorpora questi scenari e i passaggi per risolvere il problema.

#### <a name="consent-issues"></a>Problemi di consenso

- [Errore di richiesta di consenso imprevista](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Errore di consenso utente](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problemi di accesso

- [Problemi di accesso da un portale personalizzato](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Problemi durante l'accesso dal riquadro di accesso](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Errore della pagina di accesso dell'applicazione](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problema durante l'accesso in un'applicazione di Microsoft](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Problemi di accesso SSO per le applicazioni elencate nella raccolta di applicazioni di Azure

- [Problema con SSO basato su password per le applicazioni elencate nella raccolta di applicazioni Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problema con SSO federato per le applicazioni elencate nella raccolta di applicazioni di Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Problemi di accesso SSO per le applicazioni non è elencate nella raccolta di applicazioni di Azure

- [Problema con SSO basato su password per le applicazioni non è elencato nella raccolta di applicazioni di Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problema con SSO federato per le applicazioni non è elencato nella raccolta di applicazioni di Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Passaggi successivi

[Eseguire il debug di Single Sign-On basato su SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Mapping di attestazioni per le app tramite PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Personalizzazione delle attestazioni rilasciate nel token SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protocollo per Single Sign-on SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Protocollo SAML per Single Sign-Out](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (per gli utenti esterni, ad esempio partner e fornitori)

[Accesso condizionale di Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Accesso SSO](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Esercitazione sull'accesso SSO di un'applicazione](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
