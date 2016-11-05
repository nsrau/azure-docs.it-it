---
title: Indice di articoli per la gestione di applicazioni in Azure Active Directory | Microsoft Docs
description: Informazioni su come personalizzare la data di scadenza per i certificati di federazione e su come rinnovare i certificati con scadenza imminente.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: femila
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/02/2016
ms.author: markvi

---
# Indice di articoli per la gestione di applicazioni in Azure Active Directory
Questa pagina fornisce un elenco completo di tutti i documenti scritti sulle diverse funzionalità relative alle applicazioni in Azure Active Directory (Azure AD).

Include anche una breve introduzione per ogni area relativa a una funzionalità principale, oltre a indicazioni sugli articoli da leggere in base alle informazioni cercate.

## Articoli generali
Gli articoli seguenti sono un ottimo punto di partenza per chi vuole semplicemente una breve spiegazione delle funzionalità di gestione delle applicazioni di Azure AD.

| Guida agli articoli |  |
|:---:| --- |
| Panoramica dei problemi di gestione delle applicazioni risolti da Azure AD. |[Gestione di applicazioni con Azure Active Directory](active-directory-enable-sso-scenario.md) |
| Panoramica delle diverse funzionalità di Azure AD correlate all'abilitazione dell'accesso Single Sign-On, alla definizione degli utenti autorizzati ad accedere alle app e al modo in cui gli utenti avviano le app. |[Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md) |
| Panoramica delle diverse procedure necessarie per l'integrazione di app in Azure AD. |[Integrazione di Azure Active Directory con le applicazioni](active-directory-integrating-applications-getting-started.md)<br /><br />[Abilitazione dell'accesso Single Sign-On alle app SaaS](active-directory-sso-integrate-saas-apps.md)<br /><br />[Gestione dell'accesso alle app](active-directory-managing-access-to-apps.md) |
| Spiegazione tecnica del modo in cui le app vengono rappresentate in Azure AD. |[Come vengono aggiunte le applicazioni in Azure AD e perché](active-directory-how-applications-are-added.md) |

## Articoli sulla risoluzione dei problemi
Questa sezione fornisce un accesso rapido alle guide rilevanti per la risoluzione dei problemi. Altre informazioni su ogni area di funzionalità sono disponibili nel resto della pagina.

| Area di funzionalità |  |
|:---:| --- |
| Single Sign-On federato |[Risoluzione dei problemi dell'accesso Single Sign-On basato su SAML](active-directory-saml-debugging.md) |
| Single Sign-On basato su password |[Risoluzione dei problemi dell’estensione del pannello di accesso per Internet Explorer](active-directory-saas-ie-troubleshooting.md) |
| Proxy dell'applicazione |[Guida alla risoluzione dei problemi del proxy di applicazione](active-directory-application-proxy-troubleshoot.md) |
| Single Sign-On tra AD locale e Azure AD |[Risoluzione dei problemi di sincronizzazione delle password](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshooting-password-synchronization)<br /><br />[Risoluzione dei problemi di writeback delle password](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Appartenenze dinamiche a gruppi |[Risoluzione dei problemi di appartenenza dinamica ai gruppi](active-directory-accessmanagement-troubleshooting.md) |

## Accesso Single Sign-On (SSO)
### Single Sign-On federato: accesso a più app mediante una sola identità
L'accesso Single Sign-On consente agli utenti di accedere a diverse app e diversi servizi usando solo un set di credenziali. La federazione è un metodo che consente di abilitare l'accesso Single Sign-On. Quando gli utenti provano ad accedere ad app federate, vengono reindirizzati alla pagina di accesso ufficiale dell'organizzazione sottoposta a rendering da Azure Active Directory e vengono quindi reindirizzati di nuovo all'app dopo il completamento dell'autenticazione.

| Guida agli articoli |  |
|:---:| --- |
| Introduzione alla federazione e ad altri tipi di accesso. |[Single Sign-On con Azure AD](active-directory-appssoaccess-whatis.md) |
| Migliaia di app SaaS preintegrate con Azure AD con procedure di configurazione semplificate per l'accesso Single Sign-On. |[Iniziare a usare la raccolta di applicazioni di Azure AD](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery)<br /><br />[Elenco completo di app preintegrate che supportano la federazione](http://aka.ms/aadfederatedapps)<br /><br />[Come aggiungere l'app nella raccolta di applicazioni Azure AD](active-directory-app-gallery-listing.md) |
| Più di 150 esercitazioni sulle app per configurare l'accesso Single Sign-On per app quali [Salesforce](active-directory-saas-salesforce-tutorial.md), [ServiceNow](active-directory-saas-servicenow-tutorial.md), [Google Apps](active-directory-saas-google-apps-tutorial.md), [Workday](active-directory-saas-workday-tutorial.md) e così via. |[Elenco di esercitazioni sulla procedura di integrazione delle app SaaS con Azure Active Directory](active-directory-saas-tutorial-list.md) |
| Come impostare e personalizzare manualmente la configurazione dell'accesso Single Sign-On. |[Come configurare il servizio Single Sign-On federato in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](active-directory-saas-custom-apps.md)<br /><br />[Personalizzazione delle attestazioni rilasciate nel token SAML per le app preintegrate](active-directory-saml-claims-customization.md) |
| Guida alla risoluzione dei problemi per app federate che usano il protocollo SAML. |[Risoluzione dei problemi dell'accesso Single Sign-On basato su SAML](active-directory-saml-debugging.md) |
| Come configurare la data di scadenza del certificato dell'app e come rinnovare i certificati. |[Gestione di certificati per Single Sign-On federato in Azure Active Directory](active-directory-sso-certs.md) |

L'accesso Single Sign-On federato è disponibile per tutte le edizioni di Azure AD fino a un massimo di dieci app per utente. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) supporta un numero illimitato di applicazioni. Se l'organizzazione dispone di [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), è possibile [usare i gruppi per assegnare l'accesso alle applicazioni federate](#managing-access-to-applications).

### Accesso Single Sign-On basato su password: condivisione di account e accesso Single Sign-On per app non federate
Per abilitare l'accesso Single Sign-On per le app che non supportano la federazione, Azure AD offre funzionalità di gestione delle password in grado di archiviare in modo sicuro le password per le app SaaS ed effettuare automaticamente l'accesso degli utenti a queste app. È possibile distribuire con facilità le credenziali per gli account appena creati e condividere account del team con più persone. Non è necessario che gli utenti conoscano le credenziali per gli account a cui sono autorizzati ad accedere.

| Guida agli articoli |  |
|:---:| --- |
| Introduzione al funzionamento dell'accesso Single Sign-On basato su password e breve panoramica tecnica. |[Single Sign-On basato su password con Azure AD](active-directory-appssoaccess-whatis.md#password-based-single-sign-on) |
| Riepilogo degli scenari correlati alla condivisione degli account e del modo in cui questi problemi vengono risolti da Azure AD. |[Condivisione di account con Azure AD](active-directory-sharing-accounts.md) |
| Modifica automatica della password per determinate app a intervalli regolari. |[Rollover automatizzato delle password (anteprima)](https://blogs.technet.microsoft.com/enterprisemobility/2015/02/20/azure-ad-automated-password-roll-over-for-facebook-twitter-and-linkedin-now-in-preview/) |
| Guide alla distribuzione e alla risoluzione dei problemi per la versione per Internet Explorer dell'estensione per la gestione delle password di Azure AD. |[Come distribuire Access Panel Extension per Internet Explorer con Criteri di gruppo](active-directory-saas-ie-group-policy.md)<br /><br />[Risoluzione dei problemi di Access Panel Extension per Internet Explorer](active-directory-saas-ie-troubleshooting.md) |

L'accesso Single Sign-On basato su password è disponibile per tutte le edizioni di Azure AD fino a un massimo di dieci app per utente. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) supporta un numero illimitato di applicazioni. Se l'organizzazione dispone di [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), è possibile [usare i gruppi per assegnare l'accesso alle applicazioni](#managing-access-to-applications). Il rollover automatizzato delle password è una funzionalità di [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

### Proxy di app: accesso Single Sign-On e accesso remoto alle applicazioni locali
Se nella rete privata sono presenti applicazioni a cui devono accedere utenti e dispositivi esterni alla rete, è possibile usare il proxy di applicazione di Azure AD per abilitare l'accesso sicuro e remoto a queste app.

| Guida agli articoli |  |
|:---:| --- |
| Panoramica del proxy di applicazione di Azure AD e del suo funzionamento. |[Garantire accesso remoto sicuro alle applicazioni locali](active-directory-application-proxy-get-started.md) |
| Esercitazioni sulla configurazione del proxy di applicazione e su come pubblicare la prima app. |[Come configurare il proxy di applicazione di Azure AD](active-directory-application-proxy-enable.md)<br /><br />[Come eseguire un'installazione invisibile all'utente del connettore del proxy di applicazione di Azure AD](active-directory-application-proxy-silent-installation.md)<br /><br />[Pubblicare applicazioni mediante il proxy di applicazione](active-directory-application-proxy-publish.md)<br /><br />[Come usare un nome di dominio personalizzato](active-directory-application-proxy-custom-domains.md) |
| Come abilitare l'accesso Single Sign-On e l'accesso condizionale per le app pubblicate con il proxy di applicazione. |[Accesso Single Sign-On con il proxy di applicazione](active-directory-application-proxy-sso-using-kcd.md)<br /><br />[Accesso condizionale e proxy di applicazione](active-directory-application-proxy-conditional-access.md) |
| Indicazioni sull'uso del proxy di applicazione per gli scenari seguenti. |[Come supportare le applicazioni client native](active-directory-application-proxy-native-client.md)<br /><br />[Come supportare le applicazioni in grado di riconoscere attestazioni](active-directory-application-proxy-claims-aware-apps.md)<br /><br />[Come supportare applicazioni pubblicate in reti e posizioni separate](active-directory-application-proxy-connectors.md) |
| Guida alla risoluzione dei problemi relativi al proxy di applicazione. |[Guida alla risoluzione dei problemi del proxy di applicazione](active-directory-application-proxy-troubleshoot.md) |

Il proxy di applicazione è disponibile per tutte le edizioni di Azure AD fino a un massimo di dieci app per utente. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) supporta un numero illimitato di applicazioni. Se l'organizzazione dispone di [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), è possibile [usare i gruppi per assegnare l'accesso alle applicazioni](#managing-access-to-applications).

È possibile che si sia interessati anche a [Servizi di dominio Azure AD](../active-directory-domain-services/active-directory-ds-overview.md), che consente di eseguire la migrazione delle applicazioni locali in Azure, continuando comunque a soddisfare le esigenze relative alle identità per queste applicazioni.

### Abilitazione dell'accesso Single Sign-On tra Azure AD e l'istanza locale di AD
Se l'organizzazione ha un'istanza locale di Windows Server Active Directory oltre ad Azure Active Directory sul cloud, è possibile che si voglia abilitare l'accesso Single Sign-On tra i due sistemi. Azure AD Connect, lo strumento che integra questi due sistemi, offre più opzioni per la configurazione dell'accesso Single Sign-On, ovvero stabilire una federazione con AD FS o un altro provider di federazione oppure abilitare la sincronizzazione password.

| Guida agli articoli |  |
|:---:| --- |
| Panoramica delle opzioni relative all'accesso Single Sign-On disponibili in Azure AD Connect, oltre a informazioni sulla gestione di ambienti ibridi. |[Opzioni di accesso utente di Azure AD Connect](active-directory-aadconnect-user-signin.md) |
| Indicazioni generali per la gestione di ambienti con Active Directory locale e Azure Active Directory. |[Considerazioni di progettazione dell'identità ibrida di Azure Active Directory](active-directory-hybrid-identity-design-considerations-overview.md)<br /><br />[Integrazione delle identità locali con Azure Active Directory](active-directory-aadconnect.md) |
| Indicazioni sull'uso della sincronizzazione delle password per abilitare l'accesso Single Sign-On |[Implementare la sincronizzazione password con Azure AD Connect](active-directory-aadconnectsync-implement-password-synchronization.md)<br /><br />[Risolvere i problemi di sincronizzazione delle password](https://support.microsoft.com/it-IT/kb/2855271) |
| Indicazioni sull'uso del writeback delle password per abilitare l'accesso Single Sign-On |[Introduzione alla gestione delle password in Azure AD](active-directory-passwords-getting-started.md)<br /><br />[Risolvere i problemi relativi al writeback delle password](active-directory-passwords-troubleshoot.md#troubleshoot-password-writeback) |
| Indicazioni sull'uso di provider di identità di terze parti per abilitare l'accesso Single Sign-On |[Elenco di provider di identità di terze parti compatibili che possono essere usati per abilitare l'accesso Single Sign-On](https://aka.ms/ssoproviders) |
| Informazioni su come gli utenti di Windows 10 possono sfruttare i vantaggi dell'accesso Single Sign-On tramite l'aggiunta ad Azure AD. |[Estensione delle funzionalità del cloud ai dispositivi Windows 10 tramite l'aggiunta ad Azure Active Directory](active-directory-azureadjoin-overview.md) |

Azure AD Connect è disponibile per [tutte le edizioni di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/). Reimpostazione password self-service di Azure AD è disponibile per [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) e [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). Il writeback delle password per le istanze locali di AD è una funzionalità di [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

### Accesso condizionale: applicare requisiti di sicurezza aggiuntivi per app a rischio elevato
Dopo la configurazione dell'accesso Single Sign-On per le app e le risorse, è possibile proteggere ulteriormente le applicazioni sensibili applicando requisiti di sicurezza specifici per ogni accesso all'app. Ad esempio, è possibile usare Azure AD per fare in modo che tutti gli accessi a un'app specifica richiedano l'autenticazione a più fattori, indipendentemente dal fatto che l'app supporti in modo nativo tale funzionalità. Un altro esempio comune di accesso condizionale consiste nel richiedere che gli utenti siano connessi alla rete attendibile dell'organizzazione per potere accedere a un'applicazione particolarmente sensibile.

| Guida agli articoli |  |
|:---:| --- |
| Introduzione alle funzionalità di accesso condizionale disponibili in Azure AD, Office365 e Intune. |[Gestione dei rischi con l'accesso condizionale](active-directory-conditional-access.md) |
| Come abilitare l'accesso condizionale per i tipi di risorse seguenti. |[Accesso condizionale per app SaaS](active-directory-conditional-access-azuread-connected-apps.md)<br /><br />[Accesso condizionale per i servizi di Office 365](active-directory-conditional-access-device-policies.md)<br /><br />[Accesso condizionale per applicazioni locali](active-directory-conditional-access-on-premises-setup.md)<br /><br />[Accesso condizionale per applicazioni locali pubblicate tramite proxy di app di Azure AD](active-directory-application-proxy-conditional-access.md) |
| Come registrare dispositivi in Azure Active Directory per abilitare i criteri di accesso condizionale basati su dispositivo. |[Introduzione a Registrazione dispositivo Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)<br /><br />[Registrazione automatica dei dispositivi con Azure Active Directory per i dispositivi Windows aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration.md)<br /> - [Configurare la registrazione automatica per i dispositivi Windows 8.1 aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)<br /> - [Configurare la registrazione automatica per i dispositivi Windows 7 aggiunti a un dominio](active-directory-conditional-access-automatic-device-registration-windows7.md) |
| Come usare la versione Android dell'app Azure Authenticator per i criteri correlati all'autenticazione a più fattori. |[Azure Authenticator per Android](active-directory-conditional-access-azure-authenticator-app.md) |

L'accesso condizionale è una funzionalità di [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

## App e Azure AD
### Cloud App Discovery: individuare le app SaaS usate nell'organizzazione
Cloud App Discovery aiuta i reparti IT a individuare le app SaaS usate nell'organizzazione. Può misurare l'utilizzo e la popolarità di un'app, per consentire al reparto IT di determinare per quali app potrebbero risultare vantaggiosi il controllo diretto da parte del reparto IT e l'integrazione con Azure AD.

| Guida agli articoli |  |
|:---:| --- |
| Panoramica generale del funzionamento. |[Ricerca di applicazioni cloud non autorizzate con Cloud App Discovery](active-directory-cloudappdiscovery-whatis.md) |
| Approfondimento del funzionamento e risposte alle domande sulla privacy. |[Considerazioni sulla sicurezza e sulla privacy](active-directory-cloudappdiscovery-security-and-privacy-considerations.md) |
| Domande frequenti |[Domande frequenti per Cloud App Discovery](http://social.technet.microsoft.com/wiki/contents/articles/24037.cloud-app-discovery-frequently-asked-questions.aspx) |
| Esercitazioni per la distribuzione di Cloud App Discovery. |[Guida alla distribuzione di Criteri di gruppo](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx)<br /><br />[Guida alla distribuzione di System Center](http://social.technet.microsoft.com/wiki/contents/articles/30968.cloud-app-discovery-system-center-deployment-guide.aspx)<br /><br />[Installazione nei server proxy con porte personalizzate](active-directory-cloudappdiscovery-registry-settings-for-proxy-services.md) |
| Log delle modifiche per gli aggiornamenti all'agente di Cloud App Discovery. |[Log delle modifiche](http://social.technet.microsoft.com/wiki/contents/articles/24616.cloud-app-discovery-agent-changelog.aspx) |

Cloud App Discovery è una funzionalità di [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

### Provisioning e deprovisioning automatici degli account utente nelle app SaaS
Automatizzazione delle operazioni di creazione, gestione e rimozione delle identità utente nelle applicazioni SaaS come Dropbox, Salesforce, ServiceNow e così via. È possibile trovare corrispondenze e sincronizzare le identità esistenti tra Azure AD e le app SaaS e controllare l'accesso disabilitando automaticamente gli account quando gli utenti lasciano l'organizzazione.

| Guida agli articoli |  |
|:---:| --- |
| Altre informazioni sul funzionamento e risposte alle domande comuni. |[Automatizzare il provisioning e il deprovisioning utenti in app SaaS](active-directory-saas-app-provisioning.md) |
| Configurare il mapping delle informazioni tra Azure AD e l'app SaaS. |[Personalizzazione dei mapping degli attributi](active-directory-saas-customizing-attribute-mappings.md)<br><br>[Scrittura di espressioni per il mapping degli attributi](active-directory-saas-writing-expressions-for-attribute-mappings.md) |
| Come abilitare il provisioning automatico per le app che supportano il protocollo SCIM. |[Configurare il provisioning utenti automatico per le app abilitate a SCIM](active-directory-scim-provisioning.md) |
| Ottenere notifiche per gli errori di provisioning. |[Notifiche relative al provisioning](active-directory-saas-account-provisioning-notifications.md) |
| Limitare gli utenti sottoposti a provisioning per un'applicazione in base ai valori dei rispettivi attributi. |[Filtri per la definizione dell'ambito](active-directory-saas-scoping-filters.md) |

Il provisioning utenti automatico è disponibile per tutte le edizioni di Azure AD fino a un massimo di dieci app per utente. [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/) supporta un numero illimitato di applicazioni. Se l'organizzazione dispone di [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) o [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/), è possibile [usare i gruppi per gestire gli utenti da sottoporre a provisioning](#managing-access-to-applications).

### Sviluppo di applicazioni integrate con Azure AD
Se l'organizzazione sviluppa o gestisce applicazioni LoB (Line-of-Business) o se si sviluppano app per clienti che usano Azure Active Directory, le esercitazioni seguenti sono utili per integrare le applicazioni con Azure AD.

| Guida agli articoli |  |
|:---:| --- |
| Indicazioni per professionisti IT e sviluppatori di applicazioni per l'integrazione di app con Azure AD. |[Guida per i professionisti IT allo sviluppo di applicazioni per Azure AD](active-directory-applications-guiding-developers-for-lob-applications.md)<br /><br />[Guida per gli sviluppatori per Azure Active Directory](active-directory-developers-guide.md) |
| Informazioni sul modo in cui i fornitori possono aggiungere le proprie app alla raccolta di app di Azure AD. |[Inserimento dell'applicazione nella raccolta di applicazioni Azure Active Directory](active-directory-app-gallery-listing.md) |
| Come gestire l'accesso alle applicazioni sviluppate usando Azure Active Directory. |[Come abilitare l'assegnazione utente per le applicazioni sviluppate](active-directory-applications-guiding-developers-requiring-user-assignment.md)<br /><br />[Assegnazione di utenti all'app](active-directory-applications-guiding-developers-assigning-users.md)<br /><br />[Assegnazione di un gruppo all'app](active-directory-applications-guiding-developers-assigning-groups.md) |

Se si sviluppano applicazioni rivolte ai consumatori, è possibile che si sia interessati all'uso di [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/), in modo da non dovere sviluppare un proprio sistema di gestione delle identità per gestire gli utenti. [Altre informazioni](../active-directory-b2c/active-directory-b2c-overview.md)

## Gestione dell'accesso alle applicazioni
### Uso dei gruppi e delle funzionalità self-service per gestire l'accesso alle app da parte degli utenti
Per semplificare la gestione degli utenti autorizzati ad accedere a risorse specifiche, Azure Active Directory consente di configurare le assegnazioni e le autorizzazioni su larga scala usando i gruppi. Il reparto IT può scegliere di abilitare le funzionalità self-service in modo che gli utenti possano semplicemente richiedere un'autorizzazione quando necessario.

| Guida agli articoli |  |
|:---:| --- |
| Panoramica delle funzionalità di gestione dell'accesso di Azure AD. |[Gestione dell'accesso alle app](active-directory-managing-access-to-apps.md)<br /><br />[Funzionamento della gestione dell'accesso in Azure AD](active-directory-manage-groups.md)<br /><br />[Come usare i gruppi per gestire l'accesso ad applicazioni SaaS](active-directory-accessmanagement-group-saasapps.md) |
| Abilitare le funzionalità self-service per la gestione di app e gruppi. |[Gestione self-service delle applicazioni](active-directory-self-service-application-access.md)<br /><br />[Gestione self-service dei gruppi](active-directory-accessmanagement-self-service-group-management.md) |
| Istruzioni per la configurazione dei gruppi in Azure AD. |[Come creare i gruppi di sicurezza](active-directory-accessmanagement-manage-groups.md)<br /><br />[Come designare i proprietari per un gruppo](active-directory-accessmanagement-managing-group-owners.md)<br /><br />[Come usare il gruppo "Tutti gli utenti"](active-directory-accessmanagement-dedicated-groups.md) |
| Usare i gruppi dinamici per popolare automaticamente l'appartenenza ai gruppi usando le regole di appartenenza basate su attributi. |[Appartenenza dinamica a gruppi: regole avanzate](active-directory-accessmanagement-groups-with-advanced-rules.md)<br /><br />[Risoluzione dei problemi di appartenenza dinamica ai gruppi](active-directory-accessmanagement-troubleshooting.md) |

La gestione dell'accesso basata sui gruppi è disponibile per [Azure AD Basic](https://azure.microsoft.com/pricing/details/active-directory/) e [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/). La gestione self-service dei gruppi, la gestione self-service delle applicazioni e i gruppi dinamici sono funzionalità di [Azure AD Premium](https://azure.microsoft.com/pricing/details/active-directory/).

### Collaborazione B2B: abilitare l'accesso partner alle applicazioni
Se la propria azienda collabora con altre aziende partner, è probabile che sia necessario gestire l'accesso partner alle applicazioni aziendali. Collaborazione B2B di Azure Active Directory offre un modo semplice e sicuro per condividere le app con i partner. Questa funzionalità è attualmente in anteprima.

| Guida agli articoli |  |
|:---:| --- |
| Panoramica delle diverse funzionalità di Azure AD utili per gestire utenti esterni quali partner, clienti e così via. |[Confronto tra le funzionalità per la gestione di identità esterne con Azure AD](active-directory-b2b-compare-external-identities.md) |
| Introduzione all'anteprima di Collaborazione B2B e attività iniziali. |[Integrazione cloud dei partner semplice e sicura con Azure AD](active-directory-b2b-what-is-azure-ad-b2b.md)<br /><br />[Collaborazione B2B di Azure Active Directory](active-directory-b2b-collaboration-overview.md) |
| Approfondimento su Collaborazione B2B di Azure AD e su come usare questo servizio. |[Collaborazione B2B: funzionamento](active-directory-b2b-how-it-works.md)<br /><br />[Limitazioni correnti della versione di anteprima di Collaborazione B2B di Azure AD](active-directory-b2b-current-preview-limitations.md)<br /><br />[Procedura dettagliata di uso della versione di anteprima di Collaborazione B2B di Azure AD](active-directory-b2b-detailed-walkthrough.md) |
| Articoli di riferimento con dettagli tecnici sul funzionamento di Collaborazione B2B di Azure AD. |[Formato file CSV per l'aggiunta di utenti partner](active-directory-b2b-references-csv-file-format.md)<br /><br />[Attributi utente interessati da Collaborazione B2B di Azure AD](active-directory-b2b-references-external-user-object-attribute-changes.md)<br /><br />[Formato del token utente per utenti partner](active-directory-b2b-references-external-user-token-format.md) |

L'anteprima di Collaborazione B2B è attualmente disponibile per [tutte le edizioni di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### Pannello di accesso: un portale per l'accesso alle app e alle funzionalità self-service
Il pannello di accesso di Azure AD consente agli utenti finali di avviare le proprie app e di accedere alle funzionalità self-service per la gestione delle proprie app e delle proprie appartenenze a gruppi. Oltre al pannello di accesso, l'elenco seguente include altre opzioni per l'accesso alle app abilitate per Single Sign-On.

| Guida agli articoli |  |
|:---:| --- |
| Confronto tra le diverse opzioni disponibili per la distribuzione di app Single Sign-On agli utenti. |[Distribuzione di applicazioni integrate di Azure AD agli utenti](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) |
| Panoramica del pannello di accesso e dell'equivalente App personali per dispositivi mobili. |[Introduzione al Pannello di accesso e ad App personali](active-directory-saas-access-panel-introduction.md)<br /> - [iOS](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8)<br /> - [Android](https://play.google.com/store/apps/details?id=com.microsoft.myapps) |
| Come accedere alle app di Azure AD dal sito Web di Office 365 |[Uso dell'icona di avvio delle app di Office 365](https://support.office.com/it-IT/article/Meet-the-Office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a) |
| Come accedere alle app di Azure AD dall'app per dispositivi mobili Intune Managed Browser |[Intune Managed Browser](https://technet.microsoft.com/it-IT/library/dn878029.aspx)<br /> - [iOS](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8)<br /> - [Android](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser) |
| Come accedere alle app di Azure AD usando i collegamenti diretti per avviare l'accesso Single Sign-On. |[Ottenere collegamenti di accesso diretto per le app](active-directory-appssoaccess-whatis.md#direct-sign-on-links-for-federated-password-based-or-existing-apps) |

Il pannello di accesso è disponibile per [tutte le edizioni di Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).

### Report: controllare con facilità le modifiche all'accesso alle app e monitorare gli accessi alle app
Azure Active Directory offre diversi report e avvisi per semplificare il monitoraggio dell'accesso dell'organizzazione alle applicazioni. È possibile ricevere avvisi per accessi anomali alle app e tenere traccia del momento e dei motivi per cui è stato modificato l'accesso di un utente a un'applicazione.

| Guida agli articoli |  |
|:---:| --- |
| Panoramica delle funzionalità di creazione di report in Azure Active Directory. |[Introduzione alla creazione di report di Azure AD](active-directory-reporting-getting-started.md) |
| Come monitorare gli accessi e l'utilizzo delle app da parte degli utenti. |[Visualizzare i report di accesso e uso](active-directory-view-access-usage-reports.md) |
| Tenere traccia delle modifiche apportate agli utenti autorizzati ad accedere a un'applicazione specifica. |[Eventi del report di controllo di Azure Active Directory](active-directory-reporting-audit-events.md) |
| Esportare i dati di questi report nei propri strumenti preferito mediante l'API di creazione di report. |[Introduzione all'API di creazione report di Azure AD](active-directory-reporting-api-getting-started.md) |

Per visualizzare i report inclusi nelle diverse edizioni di Azure Active Directory, [fare clic qui](active-directory-view-access-usage-reports.md#report-editions).

## Vedere anche
[Informazioni su Azure Active Directory](active-directory-whatis.md)

[Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)

[Servizi di dominio Azure Active Directory](https://azure.microsoft.com/services/active-directory-ds/)

[Azure Multi-Factor Authentication](https://azure.microsoft.com/services/multi-factor-authentication/)

<!---HONumber=AcomDC_0907_2016-->