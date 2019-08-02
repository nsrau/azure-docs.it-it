---
title: Funzionalità di sicurezza di Azure per la gestione delle identità | Documentazione Microsoft
description: " Questo articolo offre informazioni generali sulle funzionalità di sicurezza principali di Azure per la gestione delle identità. Le soluzioni Microsoft per la gestione delle identità e degli accessi consentono di proteggere l'accesso alle applicazioni e alle risorse nel data center aziendale e nel cloud, abilitando livelli aggiuntivi di convalida, ad esempio l'autenticazione a più fattori e l'accesso condizionale criteri. "
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 5aa0a7ac-8f18-4ede-92a1-ae0dfe585e28
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/19/2018
ms.author: terrylan
Customer intent: As an IT Pro or decision maker I am trying to learn about identity management capabilities in Azure
ms.openlocfilehash: 71531956fb129d6a85612e1b9b48f9ccb6e8a5c1
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68615499"
---
# <a name="azure-identity-management-security-overview"></a>Informazioni generali sulla sicurezza della gestione delle identità di Azure

 La gestione delle identità è il processo di autenticazione e autorizzazione delle [entità di sicurezza](/windows/security/identity-protection/access-control/security-principals). Comporta anche il controllo delle informazioni su tali entità (identità). Le entità di sicurezza (identità) possono includere servizi, applicazioni, utenti, gruppi e così via. Le soluzioni Microsoft di gestione degli accessi e delle identità consentono all'IT di proteggere l'accesso ad applicazioni e risorse nel data center aziendale e nel cloud, Tale protezione consente livelli di convalida aggiuntivi, ad esempio l'autenticazione a più fattori e i criteri di accesso condizionale. Il monitoraggio delle attività sospette tramite funzioni avanzate di report di sicurezza, controllo e avvisi consente di attenuare i potenziali problemi di sicurezza. [Azure Active Directory Premium](/azure/active-directory/active-directory-editions) offre un punto di accesso singolo (SSO) a migliaia di app software come un servizio (SaaS) cloud e alle app Web eseguite in locale.
 
Sfruttando i vantaggi della sicurezza di Azure Active Directory (AD) è possibile:

* Creare e gestire una singola identità per ogni utente in un'azienda ibrida, mantenendo sincronizzati utenti, gruppi e dispositivi. 
* Offrire l'accesso SSO alle applicazioni, incluse migliaia di app SaaS preintegrate.
* Abilitare la sicurezza dell'accesso alle applicazioni grazie all'autenticazione a più fattori basata su regole per applicazioni locali e cloud.
* Consentire l'accesso remoto sicuro ad applicazioni Web locali con il proxy di applicazione di Azure AD.

L'obiettivo di questo articolo è offrire informazioni generali sulle funzionalità di sicurezza principali di Azure per la gestione delle identità. Sono anche disponibili collegamenti ad articoli con informazioni dettagliate su ogni funzionalità.  

Questo articolo è incentrato sulle seguenti funzionalità principali di gestione delle identità di Azure:

* Single sign-on
* Proxy inverso
* Multi-Factor Authentication
* Controllo degli accessi in base al ruolo (RBAC)
* Monitoraggio della sicurezza, avvisi e report basati su Machine Learning
* Gestione delle identità e dell'accesso degli utenti
* Registrazione del dispositivo
* Privileged Identity Management
* Identity Protection
* Soluzione ibrida di gestione delle identità/Azure AD Connect
* Verifiche di accesso di Azure AD

## <a name="single-sign-on"></a>Single sign-on

L'accesso SSO consente di accedere a tutte le applicazioni e le risorse necessarie per le attività aziendali effettuando l'accesso una sola volta con un singolo account utente. Dopo aver effettuato l'accesso, è possibile accedere a tutte le applicazioni necessarie senza dover ripetere una seconda volta l'autenticazione (ad esempio, digitando una password).

Molte organizzazioni si basano su applicazioni software come un servizio, come Office 365, Box e Salesforce, per la produttività degli utenti. In passato, il personale IT doveva creare e aggiornare singoli account utente in ogni applicazione SaaS e gli utenti dovevano ricordare una password per ogni applicazione SaaS.

Azure AD estende gli ambienti locali di Active Directory nel cloud, consentendo agli utenti di usare il proprio account aziendale principale non solo per accedere ai dispositivi appartenenti a un dominio e alle risorse della società, ma anche a tutte le applicazioni Web e SaaS necessarie per svolgere il proprio lavoro.

Non solo gli utenti non devono più gestire diversi set di nomi utente e password, ma è anche possibile eseguire il provisioning o deprovisioning automatico dell'accesso alle applicazioni in base ai gruppi aziendali e al relativo stato di dipendente. Azure AD introduce controlli di governance per la sicurezza e l'accesso con cui è possibile gestire in modo centralizzato l'accesso degli utenti nelle applicazioni SaaS.

Altre informazioni:

* [Overview of Single Sign-On](https://azure.microsoft.com/documentation/videos/overview-of-single-sign-on/) (Informazioni generali su Single Sign-On)
* [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../../active-directory/manage-apps/what-is-single-sign-on.md)
* [Integrare i servizi Single Sign-On di Azure Active Directory nelle app SaaS](../../active-directory/manage-apps/configure-single-sign-on-portal.md)

## <a name="reverse-proxy"></a>Proxy inverso

Il proxy di applicazione di Azure AD consente di pubblicare applicazioni locali, ad esempio siti di [SharePoint](https://support.office.com/article/What-is-SharePoint-97b915e6-651b-43b2-827d-fb25777f446f?ui=en-US&rs=en-US&ad=US) e app basate su [Outlook Web App](https://technet.microsoft.com/library/jj657718.aspx) e [IIS](https://www.iis.net/), all'interno della rete privata e offre un accesso sicuro agli utenti esterni alla rete. Il proxy di applicazione offre l'accesso remoto e il punto di accesso singolo per molti tipi di applicazioni Web locali, con le migliaia di applicazioni SaaS supportate da Azure AD. I dipendenti possono accedere alle app dalla propria abitazione e con i propri dispositivi ed eseguire l'autenticazione tramite questo proxy basato sul cloud.

Altre informazioni:

* [Abilitazione del proxy di applicazione di Azure AD](/azure/active-directory/manage-apps/application-proxy-enable)
* [Pubblicare applicazioni mediante il proxy di applicazione AD Azure](/azure/active-directory/active-directory-application-proxy-publish)
* [Delega vincolata Kerberos per l'accesso Single Sign-On alle app con il proxy di applicazione](../../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
* [Utilizzo dell'accesso condizionale](../../active-directory/manage-apps/application-proxy-integrate-with-sharepoint-server.md)

## <a name="multi-factor-authentication"></a>Multi-Factor Authentication

Azure Multi-Factor Authentication è un metodo di autenticazione che richiede l'uso di più metodi di verifica e aggiunge un secondo livello di sicurezza critico agli accessi e alle transazioni degli utenti. Multi-Factor Authentication permette di proteggere l'accesso ai dati e alle applicazioni, garantendo al tempo stesso agli utenti una procedura di accesso semplice. Offre l'autenticazione avanzata tramite diverse opzioni di verifica, ad esempio una telefonata, un SMS, una notifica o un codice di verifica dell'app per dispositivi mobili e token OAuth di terze parti.

Altre informazioni:

* [Autenticazione a più fattori](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
* [Informazioni su Azure Multi-Factor Authentication](/azure/active-directory/authentication/multi-factor-authentication)
* [Come funziona Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)

## <a name="rbac"></a>Controllo degli accessi in base al ruolo

Il controllo degli accessi in base al ruolo è un sistema di autorizzazione basato su Azure Resource Manager che garantisce una gestione con granularità fine degli accessi alle risorse in Azure. Il controllo degli accessi in base al ruolo consente di controllare in modo granulare il livello di accesso da parte degli utenti. Ad esempio, è possibile definire che un utente può gestire solo le reti virtuali mentre un altro utente può gestire tutte le risorse di un gruppo. Azure include diversi ruoli predefiniti da usare. Di seguito sono elencati quattro fondamentali ruoli predefiniti. I primi tre si applicano a tutti i tipi di risorse.

Altre informazioni:

* [Che cos'è il controllo degli accessi in base al ruolo?](/azure/role-based-access-control/overview)
* [Ruoli predefiniti per le risorse di Azure](/azure/role-based-access-control/built-in-roles)

## <a name="security-monitoring-alerts-and-machine-learning-based-reports"></a>Monitoraggio della sicurezza, avvisi e report basati su Machine Learning

Monitoraggio della sicurezza, avvisi e report basati su Machine Learning che identificano i criteri di accesso non coerenti per contribuire alla protezione dell'azienda. È possibile usare i report di uso e accesso di Azure AD per ottenere informazioni sull'integrità e sicurezza della directory dell'organizzazione. Con queste informazioni un amministratore di directory può stabilire meglio dove potrebbero esserci possibili rischi per la sicurezza in modo da poterne pianificare adeguatamente l'attenuazione.

Nel portale di Azure i report rientrano nelle seguenti categorie:

* **Report anomalie**: contengono eventi di accesso individuati come anomali. L'obiettivo è rendere gli utenti consapevoli di tali attività e consentire di stabilire se un evento è sospetto.
* **Report applicazioni integrate**: offrono informazioni dettagliate sull'uso delle applicazioni cloud nell'organizzazione. Azure AD offre l'integrazione con migliaia di applicazioni cloud.
* **Report di errori**: segnalano gli errori che possono verificarsi durante il provisioning di account in applicazioni esterne.
* **Report specifici dell'utente**: visualizzano i dati relativi all'attività di accesso al dispositivo per un utente specifico.
* **Log attività**: contengono un record di tutti gli eventi controllati nelle ultime 24 ore, negli ultimi 7 giorni o negli ultimi 30 giorni, oltre alle modifiche alle attività del gruppo e alle attività di registrazione e di reimpostazione password.

Altre informazioni:

* [Visualizzare i report di accesso e utilizzo](/azure/active-directory/active-directory-view-access-usage-reports)
* [Introduzione ad Azure Active Directory Reporting](/azure/active-directory/active-directory-reporting-getting-started)
* [Guida alla creazione di report in Azure Active Directory](/azure/active-directory/active-directory-reporting-guide)

## <a name="consumer-identity-and-access-management"></a>Gestione delle identità e dell'accesso degli utenti

Azure Active Directory B2C è un servizio di gestione delle identità globale a disponibilità elevata per le applicazioni rivolte agli utenti, con scalabilità fino a centinaia di milioni di identità. Il servizio può essere integrato tra piattaforme mobili e Web. Gli utenti possono accedere a tutte le applicazioni attraverso esperienze personalizzabili usando gli account dei propri social network esistenti o creando nuove credenziali.

In passato, per ottenere la registrazione e l'accesso dei clienti alle applicazioni, gli sviluppatori di applicazioni scrivevano il proprio codice. E dovevano utilizzare database locali o sistemi per archiviare nomi utente e password. Azure AD B2C offre all'organizzazione un modo migliore per integrare la gestione delle identità degli utenti nelle applicazioni con una piattaforma sicura basata sugli standard e un set completo di criteri estendibili.

Con Azure AD B2C, gli utenti possono registrarsi alle applicazioni usando gli account di social networking esistenti (Facebook, Google, Amazon, LinkedIn) o creando nuove credenziali (indirizzo di posta elettronica e password o nome utente e password).

Altre informazioni:

* [Informazioni su Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/)
* [Azure Active Directory B2C (anteprima): Iscrizione e accesso degli utenti alle applicazioni](../../active-directory-b2c/active-directory-b2c-overview.md)
* [Azure Active Directory B2C (anteprima): Tipi di applicazioni](../../active-directory-b2c/active-directory-b2c-apps.md)

## <a name="device-registration"></a>Registrazione del dispositivo

Azure AD registrazione del dispositivo è la base per gli scenari di [accesso condizionale](/azure/active-directory/active-directory-conditional-access-device-registration-overview) basato su dispositivo. Quando un dispositivo viene registrato, Registrazione dispositivo Azure AD fornisce al dispositivo un'identità che viene usata per autenticare il dispositivo quando un utente esegue l'accesso. Il dispositivo autenticato e gli attributi del dispositivo possono quindi essere usati per applicare i criteri di accesso condizionale per le applicazioni ospitate nel cloud e in locale.

Con una soluzione di gestione di dispositivi mobili ad esempio Intune, gli attributi del dispositivo in Azure AD vengono aggiornati con informazioni aggiuntive sul dispositivo. È quindi possibile creare regole di accesso condizionale che impongono l'accesso da dispositivi per soddisfare gli standard di sicurezza e conformità.

Altre informazioni:

* [Introduzione a Registrazione dispositivo Azure Active Directory](/azure/active-directory/active-directory-conditional-access-device-registration-overview)
* [Registrazione automatica dei dispositivi con Azure Active Directory per i dispositivi Windows aggiunti a un dominio](/azure/active-directory/active-directory-conditional-access-automatic-device-registration)
* [Come configurare la registrazione automatica dei dispositivi Windows con Azure Active Directory aggiunti a un dominio](/azure/active-directory/active-directory-conditional-access-automatic-device-registration-setup)

## <a name="privileged-identity-management"></a>Privileged Identity Management

Con Azure Active Directory Privileged Identity Management è possibile gestire, controllare e monitorare le identità con privilegi e l'accesso alle risorse in Azure AD e in altri Servizi online di Microsoft Online Services, come Office 365 o Microsoft Intune.

In alcuni casi gli utenti hanno la necessità di eseguire operazioni privilegiate in risorse di Azure o Office 365 o in altre app SaaS. Per questa ragione è spesso necessario che le organizzazioni concedano agli utenti l'accesso con privilegi permanente in Azure AD. Questo accesso rappresenta un rischio di sicurezza crescente per le risorse ospitate nel cloud poiché le organizzazioni non sono in grado di monitorare completamente le operazioni eseguite dagli utenti con i privilegi amministrativi. Inoltre, se un account utente con accesso privilegiato è compromesso, tale singola violazione può compromettere la sicurezza dell'intero cloud di un'organizzazione. Azure AD Privileged Identity Management consente di attenuare questo rischio.

Con Azure AD Privileged Identity Management è possibile:

* Individuare gli utenti amministratori di Azure AD.
* Abilitare l'accesso come amministratore Just-In-Time su richiesta ai servizi Microsoft, ad esempio Office 365 e Intune.
* Ottenere report sulla cronologia degli accessi degli amministratori e sulle modifiche alle assegnazioni degli amministratori.
* Ricevere avvisi relativi all'accesso a un ruolo con privilegi.

Altre informazioni:

* [Che cos'è Azure AD Privileged Identity Management?](../../active-directory/privileged-identity-management/pim-configure.md)
* [Assegnare ruoli della directory di Azure AD in PIM](../../active-directory/privileged-identity-management/pim-how-to-add-role-to-user.md)

## <a name="identity-protection"></a>Identity Protection

Azure AD Identity Protection è un servizio di sicurezza che offre una visualizzazione consolidata degli eventi di rischio e delle potenziali vulnerabilità che interessano le identità dell'organizzazione. Identity Protection trae vantaggio dalle funzionalità di rilevamento anomalie di Azure AD, disponibili tramite i report di Anomalie dell'attività di Azure AD. Introduce anche nuovi tipi di eventi di rischio che permettono di rilevare le anomalie in tempo reale.

Altre informazioni:

* [Azure AD Identity Protection](/azure/active-directory/active-directory-identityprotection.md)
* [Channel 9: Azure AD and Identity Show: Identity Protection Preview](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## <a name="hybrid-identity-managementazure-ad-connect"></a>Soluzione ibrida di gestione delle identità/Azure AD Connect

Le soluzioni di gestione delle identità di Microsoft abbracciano funzionalità locali e basate sul cloud, creando una singola identità utente per l’autenticazione e l’autorizzazione per tutte le risorse, indipendentemente dalla loro ubicazione. Tutto questo viene chiamato Identità ibrida. Azure AD Connect è lo strumento di Microsoft progettato per soddisfare e raggiungere gli obiettivi relativi alla soluzione ibrida di gestione delle identità. Consente quindi di fornire agli utenti un'identità comune per le applicazioni di Office 365, Azure e SaaS integrate con Azure AD. Offre le funzionalità seguenti:

* Sincronizzazione
* Integrazione di AD FS e federazione
* Autenticazione pass-through
* Monitoraggio dello stato

Altre informazioni:

* [White paper sull'identità ibrida](https://download.microsoft.com/download/D/B/A/DBA9E313-B833-48EE-998A-240AA799A8AB/Hybrid_Identity_White_Paper.pdf)
* [Azure Active Directory](https://azure.microsoft.com/documentation/services/active-directory/)
* [Blog del team di Azure AD](https://blogs.technet.microsoft.com/ad/)

## <a name="azure-ad-access-reviews"></a>Verifiche di accesso di Azure AD

Le verifiche di accesso di Azure Active Directory (Azure AD) consentono alle organizzazioni di gestire in modo efficiente l'appartenenza a gruppi, l'accesso alle applicazioni aziendali e le assegnazioni di ruoli con privilegi.

Altre informazioni:

* [Verifiche di accesso di Azure AD](../../active-directory/governance/access-reviews-overview.md)
* [Gestire l'accesso utente con le verifiche di accesso di Azure AD](../../active-directory/governance/access-reviews-overview.md)
