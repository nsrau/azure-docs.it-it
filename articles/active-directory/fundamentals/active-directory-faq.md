---
title: Domande frequenti (FAQ) - Azure Active Directory | Microsoft Docs
description: Domande frequenti su Azure e Azure Active Directory, sulla gestione delle password e sull'accesso alle applicazioni.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.component: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: lizross
ms.custom: it-pro, seodec18
ms.openlocfilehash: a3571ff24c36b7f2129f60348414d4d178ef30d1
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/08/2018
ms.locfileid: "53084094"
---
# <a name="frequently-asked-questions-about-azure-active-directory"></a>Domande frequenti su Azure Active Directory
Azure Active Directory (Azure AD) è una soluzione IDaaS (Identity as a Service) completa che si estende a tutti gli aspetti relativi a identità, gestione degli accessi e sicurezza.

Per altre informazioni, vedere [Informazioni su Azure Active Directory](active-directory-whatis.md).


## <a name="access-azure-and-azure-active-directory"></a>Accedere ad Azure e Azure Active Directory
**D: Perché viene visualizzato il messaggio "Non sono state trovate sottoscrizioni" quando si prova ad accedere ad Azure AD nel portale di Azure?**

**R:** Per accedere al portale di Azure, ogni utente deve avere autorizzazioni per una sottoscrizione di Azure. Se si ha una sottoscrizione di Office 365 o Azure AD a pagamento, accedere a [https://aka.ms/accessAAD](https://aka.ms/accessAAD) per un passaggio di attivazione una tantum. In caso contrario sarà necessario attivare un [account Azure](https://azure.microsoft.com/pricing/free-trial/) gratuito o una sottoscrizione a pagamento.

Per altre informazioni, vedere:

* [Associare le sottoscrizioni di Azure ad Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)

- - -
**D: Qual è la relazione tra Azure AD, Office 365 e Azure?**

**R:** Azure AD offre funzionalità comuni di gestione delle identità e dell'accesso in tutti i servizi Web. Se si usa Office 365, Microsoft Azure, Intune o altri servizi, si usa già Azure AD per abilitare la gestione dell'accesso per tutti questi servizi.

Tutti gli utenti abilitati all'uso dei servizi Web sono definiti come account utente in una o più istanze di Azure AD. È possibile configurare questi account per funzionalità di Azure AD gratuite come l'accesso alle applicazioni cloud.

I servizi a pagamento di Azure AD, ad esempio Enterprise Mobility + Security, sono complementari ad altri servizi Web come Office 365 e Microsoft Azure, con soluzioni di gestione e di sicurezza complete di scala aziendale.

- - -

**D:  Quali sono le differenze tra proprietario e amministratore globale?**

**R:** Per impostazione predefinita, la persona che esegue l'iscrizione a una sottoscrizione di Azure riceve il ruolo di Proprietario per le risorse di Azure. Il proprietario può usare un account Microsoft oppure un account aziendale o dell'istituto di istruzione della directory a cui è associata la sottoscrizione di Azure.  Questo ruolo è autorizzato a gestire i servizi nel portale di Azure.

Se altri utenti devono accedere ai servizi con la stessa sottoscrizione, è possibile assegnare loro il [ruolo predefinito](../../role-based-access-control/built-in-roles.md) appropriato. Per altre informazioni, vedere [Gestire l'accesso usando il controllo degli accessi in base al ruolo e il portale di Azure](../../role-based-access-control/role-assignments-portal.md).

Per impostazione predefinita, la persona che effettua l'iscrizione per una sottoscrizione di Azure riceve il ruolo Amministratore globale per la directory. L'amministratore globale ha accesso a tutte le funzionalità della directory di Azure AD. In Azure AD è invece disponibile un set di ruoli di amministratore diverso per gestire la directory e le funzionalità relative alle identità. Questi amministratori avranno accesso a diverse funzionalità nel portale di Azure. Il ruolo dell'amministratore determina le operazioni consentite, ad esempio creare o modificare gli utenti, assegnare ruoli amministrativi ad altri, reimpostare le password utente, gestire le licenze utente o gestire i domini.  Per altre informazioni sugli amministratori della directory di AD e i loro ruoli, vedere [Assegnare un utente ai ruoli di amministratore in Azure Active Directory](active-directory-users-assign-role-azure-portal.md) e [Autorizzazioni del ruolo di amministratore in Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md).

I servizi a pagamento di Azure AD, ad esempio Enterprise Mobility + Security, sono complementari ad altri servizi Web come Office 365 e Microsoft Azure, con soluzioni di gestione e di sicurezza complete di scala aziendale.

- - -
**D: È disponibile un report che indica la scadenza delle licenze utente di Azure AD?**

**R:**  No.  Non è attualmente disponibile.

- - -

## <a name="get-started-with-hybrid-azure-ad"></a>Introduzione alla gestione ibrida di Azure AD


**D: Come si abbandona un tenant quando si viene aggiunti come collaboratore?**

**R:** Quando si viene aggiunti a un altro tenant dell'organizzazione come collaboratore, è possibile usare la "selezione tenant" in alto a destra per passare da un tenant all'altro.  Non è attualmente possibile abbandonare l'organizzazione che ha emesso l'invito, ma Microsoft prevede di offrire questa funzionalità in futuro.  Fino a quando questa funzionalità non sarà disponibile, è possibile rivolgersi all'organizzazione che ha emesso l'invito per chiedere di essere rimossi dal tenant.
- - -
**D: Come si può connettere la directory locale ad Azure AD?**

**R:** È possibile connettere la directory locale ad Azure AD usando Azure AD Connect.

Per altre informazioni, vedere [Integrazione delle identità locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

- - -
**D: Come si configura l'accesso Single Sign-On tra la directory locale e le applicazioni cloud?**

**R:** È sufficiente configurare l'accesso Single Sign-On (SSO) tra la directory locale e Azure AD. Se si accede alle applicazioni cloud tramite Azure AD, il servizio richiederà automaticamente agli utenti di eseguire l'autenticazione corretta con le credenziali locali.

L'implementazione dell'accesso Single Sign-On dall'ambiente locale può essere ottenuta facilmente con soluzioni di federazione come Active Directory Federation Services (AD FS) o con la configurazione della sincronizzazione dell'hash delle password. È possibile distribuire con facilità entrambe le opzioni usando la configurazione guidata di Azure AD Connect.

Per altre informazioni, vedere [Integrazione delle identità locali con Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

- - -
**D: Azure AD offre un portale self-service per gli utenti dell'organizzazione?**

**R:** Sì, in Azure AD è disponibile il [pannello di accesso di Azure AD](https://myapps.microsoft.com) per l'accesso degli utenti alle funzionalità self-service e alle applicazioni. I clienti di Office 365 possono trovare molte funzionalità analoghe nel portale di Office 365.

Per altre informazioni, vedere [Introduzione al Pannello di accesso](../user-help/active-directory-saas-access-panel-introduction.md).

- - -
**D: Azure AD semplifica la gestione dell'infrastruttura locale?**

**R:** Sì. Azure AD Premium Edition include Azure AD Connect Health. Azure AD Connect Health consente di monitorare e ottenere informazioni dettagliate sull'infrastruttura di gestione delle identità locale e sui servizi di sincronizzazione.  

Per altre informazioni, vedere [Monitorare l'infrastruttura di gestione delle identità locale e i servizi di sincronizzazione nel cloud](../hybrid/whatis-hybrid-identity-health.md).  

- - -
## <a name="password-management"></a>Gestione delle password
**D: È possibile usare il writeback delle password di Azure AD senza la sincronizzazione password? In questo scenario è possibile usare la reimpostazione della password self-service (SSPR) di Azure AD con writeback delle password senza memorizzare le password nel cloud?**

**R:** Non è necessario sincronizzare le password di Active Directory con Azure AD per abilitare il writeback. In un ambiente federato, l'accesso Single Sign-On (SSO) di Azure AD si basa sulla directory locale per autenticare l'utente. Questo scenario non richiede la verifica della password locale in Azure AD.

- - -
**D: Quanto tempo è necessario per il writeback di una password nell'istanza locale di Active Directory?**

**R:** Il writeback delle password viene eseguito in tempo reale.

Per altre informazioni, vedere [Introduzione alla gestione delle password](../authentication/quickstart-sspr.md).

- - -
**D: È possibile usare il writeback delle password con password gestite da un amministratore?**

**R:** Sì. Se il writeback delle password è abilitato, le operazioni relative alle password eseguite da un amministratore vengono sottoposte a writeback nell'ambiente locale.  

Per altre risposte a domande relative alle password, vedere [Domande frequenti sulla gestione delle password](../authentication/active-directory-passwords-faq.md).
- - -
**D:  Cosa si può fare se non si ricorda la password di Office 365/Azure AD esistente durante il tentativo di modificare la password?**

**R:** In situazioni di questo tipo è possibile procedere in due modi.  Usare la reimpostazione password self-service (SSPR), se disponibile.  La disponibilità della funzione SSPR dipende dalla sua configurazione.  Per altre informazioni, vedere [Funzionamento del portale di reimpostazione delle password](../authentication/howto-sspr-deployment.md).

Per gli utenti di Office 365, l'amministratore può reimpostare la password seguendo la procedura descritta in [Reimpostare le password degli utenti](https://support.office.com/article/Admins-Reset-user-passwords-7A5D073B-7FAE-4AA5-8F96-9ECD041ABA9C?ui=en-US&rs=en-US&ad=US).

Per gli account Azure AD, gli amministratori possono reimpostare le password in uno dei modi seguenti:

- [Reimpostare gli account nel portale di Azure](active-directory-users-reset-password-azure-portal.md)
- [Tramite PowerShell](/powershell/module/msonline/set-msoluserpassword?view=azureadps-1.0)


- - -
## <a name="security"></a>Sicurezza
**D: Gli account vengono bloccati dopo un numero specifico di tentativi non riusciti o la strategia usata è più sofisticata?**

La strategia usata per bloccare gli account è più sofisticata e  si basa sull'indirizzo IP della richiesta e le password immesse. La durata del blocco aumenta anche in base alla probabilità che sia in corso un attacco.  

**D:  Alcune password (comuni) vengono rifiutate con messaggi indicanti che la password è stata usata molte volte; si fa riferimento alle password usate nell'istanza corrente di Active Directory?**

Si fa riferimento alle password comuni a livello globale, ad esempio tutte le varianti di "Password" e "123456".

**D: Una richiesta di accesso proveniente da origini sospette (botnet, endpoint tor) sarà bloccata in un tenant B2C o è necessario un tenant della Basic Edition o della Premium Edition?**

Esiste un gateway che filtra le richieste e fornisce un livello di protezione da botnet e che viene applicato per tutti i tenant B2C.

## <a name="application-access"></a>Accesso all'applicazione

**D: Dove si può trovare un elenco di applicazioni pre-integrate con Azure AD e delle rispettive funzionalità?**

**R:** Azure AD include più di 2.600 applicazioni pre-integrate di Microsoft, provider di servizi dell'applicazione o partner. Tutte le applicazioni preintegrate supportano l'accesso Single Sign-On, che consente di usare le credenziali aziendali per accedere alle app. Alcune applicazioni supportano anche il provisioning e il deprovisioning automatici.

Per un elenco completo delle applicazioni preintegrate, vedere il [Marketplace di Active Directory](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**D: Che cosa accade se l'applicazione cercata non è disponibile nel Marketplace di Azure AD?**

**R:** Azure AD Premium consente di aggiungere e configurare qualsiasi applicazione. In base alle funzionalità dell'applicazione e alle preferenze dell'utente, è possibile configurare l'accesso Single Sign-On e il provisioning automatico.  

Per altre informazioni, vedere:

* [Configurazione del servizio Single Sign-On in applicazioni non presenti nella raccolta di applicazioni di Azure Active Directory](../manage-apps/configure-federated-single-sign-on-non-gallery-applications.md)
* [Uso di SCIM per abilitare il provisioning automatico di utenti e gruppi da Azure Active Directory alle applicazioni](../manage-apps/use-scim-to-provision-users-and-groups.md)

- - -
**D: In che modo gli utenti possono accedere alle applicazioni tramite Azure AD?**

**R:** Azure AD consente agli utenti di visualizzare e accedere alle applicazioni in molti modi, ad esempio:

* Pannello di accesso di Azure AD
* Applicazione di avvio di Office 365
* Accesso diretto alle applicazioni federate
* Collegamenti diretti per applicazioni federate, basate su password o esistenti

Per altre informazioni, vedere [End user experiences for applications](../manage-apps/end-user-experiences.md) (Esperienza degli utenti finali per le applicazioni).

- - -
**D: Quali sono le diverse modalità usate da Azure AD per abilitare l'autenticazione e l'accesso Single Sign-On alle applicazioni?**

**R:** Azure AD supporta molti protocolli standardizzati per l'autenticazione e l'autorizzazione, ad esempio SAML 2.0, OpenID Connect, OAuth 2.0 e WS-Federation. Azure AD supporta anche funzionalità relative all'insieme di credenziali delle password e all'accesso automatico per le app che supportano solo l'autenticazione basata su form.  

Per altre informazioni, vedere:

* [Scenari di autenticazione per Azure AD](../develop/authentication-scenarios.md)
* [Protocolli di autenticazione di Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [Single Sign-On per le applicazioni in Azure AD](../manage-apps/what-is-single-sign-on.md)

- - -
**D: È possibile aggiungere applicazioni in esecuzione in locale?**

**R:** Azure Active Directory Application Proxy offre un accesso semplice e sicuro alle applicazioni Web locali scelte. È possibile accedere a queste applicazioni in modo analogo all'accesso alle app SaaS in Azure AD. Non è necessario avere una VPN o modificare l'infrastruttura di rete.  

Per altre informazioni, vedere [Come fornire l'accesso remoto sicuro alle applicazioni locali](../manage-apps/application-proxy.md).

- - -
**D: Come si richiede l'autenticazione a più fattori per gli utenti che accedono a un'applicazione specifica?**

**R:** L'accesso condizionale di Azure AD consente di assegnare criteri di accesso univoci per ogni applicazione. Nel criterio è possibile richiedere sempre l'autenticazione Multi-Factor Authentication o solo quando gli utenti non sono connessi alla rete locale.  

Per altre informazioni, vedere [Protezione dell'accesso a Office 365 e ad altre app connesse ad Azure Active Directory](../active-directory-conditional-access-azure-portal.md).

- - -
**D: Che cos'è il provisioning utenti automatizzato per app SaaS?**

**R:** Azure AD consente di automatizzare la creazione, la manutenzione e la rimozione delle identità utente in molte app cloud SaaS comuni.

Per altre informazioni, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

- - -
**D:  È possibile configurare una connessione LDAP sicura con Azure AD?**

**R:**   No. Azure AD non supporta il protocollo LDAP. Tuttavia, è possibile configurare un LDAP sicuro con Azure Active Directory Domain Services.
