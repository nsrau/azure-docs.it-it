---
title: Accesso Single Sign-On alle applicazioni - Azure Active Directory | Microsoft Docs
description: Informazioni su come scegliere un metodo di accesso Single Sign-On quando si configurano le applicazioni in Azure Active Directory (Azure AD). Usare il metodo di accesso Single Sign-On in modo che gli utenti non debbano ricordare le password per ogni applicazione e per semplificare l'amministrazione della gestione degli account.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: barbkess
ms.reviewer: arvindh
ms.openlocfilehash: 627bd114504de20517abcd05f45dc6c58fe80117
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/13/2018
ms.locfileid: "53322705"
---
# <a name="single-sign-on-to-applications-in-azure-active-directory"></a>Accesso Single Sign-On alle applicazioni in Azure Active Directory
Informazioni su come scegliere il metodo di accesso Single Sign-On appropriato quando si configurano le applicazioni in Azure Active Directory (Azure AD). 

- **Con l'accesso Single Sign-On**, gli utenti eseguono l'accesso una sola volta con un account a tutti i dispositivi aggiunti a un dominio, alle risorse aziendali, alle applicazioni SaaS (software distribuito come servizio) e alle applicazioni Web. Dopo aver eseguito l'accesso, l'utente potrà avviare le applicazioni dal portale di Office 365 o dal pannello di accesso MyApps di Azure AD. Gli amministratori possono centralizzare la gestione degli account utente e aggiungere o rimuovere automaticamente l'accesso utente alle applicazioni in base all'appartenenza a gruppi. 

- **Senza l'accesso Single Sign-On**, gli utenti devono ricordare le password specifiche dell'applicazione e accedere a ognuna di esse. Il personale IT deve creare e aggiornare gli account utente per ogni applicazione, ad esempio Office 365, Box e Salesforce. Gli utenti devono ricordare le password, oltre a impiegare il tempo necessario per accedere a ogni applicazione.

Questo articolo descrive i metodi di accesso Single Sign-On e consente di scegliere il metodo ideale per le applicazioni.

## <a name="choosing-a-single-sign-on-method"></a>Scelta di un metodo di accesso Single Sign-On

È possibile configurare un'applicazione per l'accesso Single Sign-On in vari modi. La scelta di un metodo di accesso Single Sign-On per un'applicazione dipende dal modo in cui è configurata per l'autenticazione. Tutti i metodi di accesso Single Sign-On, ad eccezione del metodo disabilitato, eseguono l'accesso automatico degli utenti alle applicazioni senza richiedere un secondo accesso.  

- Le applicazioni cloud possono usare il metodo SAML, basato su password, collegato o disabilitato per l'accesso Single Sign-On. SAML è il metodo di accesso Single Sign-On più sicuro.
- Le applicazioni locali possono usare il metodo basato su password o intestazione, collegato, disabilitato oppure l'autenticazione integrata di Windows per l'accesso Single Sign-On. Le scelte locali sono valide se le applicazioni sono configurate per Application Proxy. 

Questo diagramma di flusso aiuta a decidere quale metodo di accesso Single Sign-On è più idoneo al proprio scenario. 

![Scegliere il metodo di accesso Single Sign-On](./media/what-is-single-sign-on/choose-single-sign-on-method.png)

La tabella seguente include un riepilogo dei metodi di accesso Single Sign-On e collegamenti per maggiori dettagli. 

| Metodo di accesso Single Sign-On | Tipi di applicazioni | Quando usare le autorizzazioni |
| :------ | :------- | :----- |
| [SAML](#saml-sso) | Solo nel cloud | Usare SAML laddove possibile. SAML funziona quando le app sono configurate per usare uno dei protocolli SAML.|
| [Basato su password](#password-based-sso) | Cloud e locale | Da usare quando l'applicazione esegue l'autenticazione con nome utente e password. L'accesso Single Sign-On basato su password consente l'archiviazione e la riproduzione delle password delle applicazioni protette usando un'estensione del Web browser o un'app per dispositivi mobili. Questo metodo usa il processo di accesso esistente fornito dall'applicazione, ma consente all'amministratore di gestire le password. |
| [Collegato](#linked-sso) | Cloud e locale | Usare l'accesso Single Sign-On collegato quando l'applicazione è configurata per Single Sign-On in un altro servizio di provider di identità. Questa opzione non aggiunge l'accesso Single Sign-On all'applicazione. L'applicazione potrebbe comunque avere già implementato l'accesso Single Sign-On usando un altro servizio, ad esempio Active Directory Federation Services.|
| [Disabilitato](#disabled-sso) | Cloud e locale | Usare l'accesso Single Sign-On disabilitato se l'app non è pronta per essere configurata per Single Sign-On. Gli utenti devono immettere il nome utente e la password ogni volta che avviano l'applicazione.|
| [Autenticazione integrata di Windows](#integrated-windows-authentication-iwa-sso) | Solo in locale | Usare questo metodo di accesso Single Sign-On per le applicazioni che usano l'[autenticazione integrata di Windows](/aspnet/web-api/overview/security/integrated-windows-authentication) o le applicazioni che riescono a riconoscere le attestazioni. I connettori Application Proxy usano la delega vincolata Kerberos per autenticare gli utenti con l'applicazione. | 
| [Basato su intestazione](#header-based-sso) | Solo in locale | Usare l'accesso Single Sign-On basato su intestazione quando l'applicazione usa le intestazioni per l'autenticazione. L'accesso Single Sign-On basato su intestazione richiede PingAccess per Azure Active Directory. Application Proxy usa Azure AD per autenticare l'utente e quindi passa il traffico attraverso il servizio del connettore.  | 

## <a name="saml-sso"></a>SAML SSO
Con l'**accesso Single Sign-On SAML**, Azure AD esegue l'autenticazione all'applicazione usando l'account Azure AD dell'utente. Azure AD comunica le informazioni di accesso all'applicazione tramite un protocollo di connessione. Con l'accesso Single Sign-On basato su SAML è possibile eseguire il mapping degli utenti a ruoli specifici dell'applicazione in base alle regole definite nelle attestazioni SAML

L'accesso Single Sign-On basato su SAML è:

- Più sicuro dell'accesso Single Sign-On basato su password e di tutti gli altri metodi di accesso.
- Il metodo consigliato per Single Sign-On.

L'accesso Single Sign-On basato su SAML è supportato per le applicazioni che usano uno di questi protocolli:

- SAML 2.0
- WS-Federation

Per configurare un'applicazione per l'accesso Single Sign-On basata su SAML, vedere [Configurare l'accesso Single Sign-On basato su SAML](configure-single-sign-on-portal.md). Molte applicazioni presentano anche [esercitazioni specifiche dell'applicazione](../saas-apps/tutorial-list.md) di supporto nella configurazione dell'accesso Single Sign-On basato su SAML per applicazioni specifiche. 

Per altre informazioni sul funzionamento del protocollo SAML, vedere [Protocollo SAML per Single Sign-On](../develop/single-sign-on-saml-protocol.md).

## <a name="password-based-sso"></a>SSO basato su password
Con l'accesso basato su password, gli utenti finali eseguono la procedura di accesso all'applicazione con nome utente e password solo al primo accesso. Agli accessi successivi sarà Azure Active Directory a indicare il nome utente e la password all'applicazione. 

L'accesso Single Sign-On basato su password usa il processo di autenticazione esistente specificato dall'applicazione. Quando si abilita l'accesso Single Sign-On basato su password per un'applicazione, Azure AD raccoglie e archivia in modo sicuro i nomi utente e le password per l'applicazione. Le credenziali utente vengono archiviate in formato crittografato nella directory. 

Usare l'accesso Single Sign-On basato su password quando:

- Un'applicazione non supporta il protocollo di accesso Single Sign-On SAML.
- Un'applicazione esegue l'autenticazione con un nome utente e una password anziché usando intestazioni e token di accesso.

L'accesso Single Sign-On basato su password è supportato per qualunque applicazione basata su cloud con pagina di accesso basata su HTML. L'utente può usare uno dei browser seguenti:

- Internet Explorer 11 su Windows 7 o versioni successive
- Microsoft Edge su Windows 10 Anniversary Edition o versioni successive 
- Chrome in Windows 7 o versione successiva e in MacOS X o versione successiva
- Firefox 26.0 o versione successiva in Windows XP SP2 o versione successiva e in Mac OS X 10.6 o versione successiva

Per configurare un'applicazione cloud per l'accesso Single Sign-On basato su password, vedere [Configurare l'applicazione per un accesso Single Sign-On basato su password](application-sign-in-problem-password-sso-gallery.md#configure-the-application-for-password-single-sign-on).

Per configurare un'applicazione in locale per l'accesso Single Sign-On tramite Application Proxy, vedere [Insieme di credenziali delle password per l'accesso Single Sign-On con il proxy dell'applicazione](application-proxy-configure-single-sign-on-password-vaulting.md)

### <a name="managing-credentials-for-password-based-sso"></a>Gestione delle credenziali per SSO basato su password

Per l'autenticazione di un utente a un'applicazione, Azure AD recupera le credenziali dell'utente dalla directory e le immette nella pagina di accesso dell'applicazione.  Azure AD passa in modo sicuro le credenziali dell'utente tramite un'estensione del Web browser o un'app per dispositivi mobili. Questo processo consente agli amministratori di gestire le credenziali dell'utente senza che quest'ultimo debba ricordare le password.

> [!IMPORTANT]
> Le credenziali vengono offuscate all'utente finale durante il processo di accesso automatico, ma sono individuabili usando strumenti di debug Web. Gli utenti e gli amministratori devono seguire gli stessi criteri di sicurezza come se le credenziali fossero immesse direttamente dall'utente.

Le password per ogni applicazione possono essere gestite dall'amministratore di Azure AD o dagli utenti.

Quando l'amministratore di Azure AD gestisce le credenziali:  

- L'utente non deve reimpostare o ricordare il nome utente e la password. L'utente può accedere all'applicazione selezionandola nel pannello di accesso oppure tramite un collegamento.
- L'amministratore può eseguire attività di gestione sulle credenziali. Può ad esempio aggiornare l'accesso alle applicazioni in base alle appartenenze a gruppi di utenti e allo stato dei dipendenti.
- L'amministratore può usare credenziali amministrative per specificare l'accesso alle applicazioni condivise tra più utenti. Può ad esempio consentire a tutti gli utenti che possono accedere a un'applicazione di accedere a un social network o a un documento che condivide l'applicazione.

Quando l'utente finale gestisce le credenziali:

- Gli utenti possono gestire le password aggiornandole o eliminandole in base alle proprie esigenze. 
- Gli amministratori sono comunque in grado di impostare nuove credenziali per l'applicazione.


## <a name="linked-sso"></a>SSO collegato
L'accesso Single Sign-On collegato consente ad Azure AD di fornire l'accesso Single Sign-On a un'applicazione già configurata per l'SSO in un altro servizio. L'applicazione collegata può essere visualizzata dagli utenti finali nel portale di Office 365 o nel portale MyApps di Azure AD. Un utente può ad esempio avviare un'applicazione configurata per l'accesso Single Sign-On in Active Directory Federation Services 2.0 (AD FS) dal portale di Office 365. Sono anche disponibili report aggiuntivi per le applicazioni collegate che vengono avviate dal portale di Office 365 o dal portale MyApps di Azure AD. 

Usare l'accesso Single Sign-On collegato per:

- Garantire un'esperienza utente coerente mentre si esegue la migrazione delle applicazioni in un periodo di tempo. Se si esegue la migrazione di applicazioni ad Azure Active Directory, è possibile usare l'accesso Single Sign-On collegato per pubblicare rapidamente i collegamenti a tutte le applicazioni da migrare.  Gli utenti possono trovare tutti i collegamenti nel [portale MyApps](../user-help/active-directory-saas-access-panel-introduction.md) o nell'[applicazione di avvio di Office 365](https://support.office.com/article/meet-the-office-365-app-launcher-79f12104-6fed-442f-96a0-eb089a3f476a). Gli utenti non si accorgeranno di accedere a un'applicazione collegata o sottoposta migrazione.  

Dopo che un utente ha eseguito l'autenticazione con un'applicazione collegata, dovrà essere creato un record di account prima che all'utente finale venga concesso l'accesso Single Sign-On. Il provisioning di questo record di account può verificarsi automaticamente o può essere effettuato manualmente da un amministratore.

## <a name="disabled-sso"></a>SSO disabilitato

Con la modalità disabilitata, l'accesso Single Sign-On non viene usato per l'applicazione. Quando l'accesso Single Sign-On è disabilitato, gli utenti potrebbero doversi autenticare due volte. In primo luogo, gli utenti eseguono l'autenticazione ad Azure AD e quindi accedono all'applicazione. 

Usare la modalità di accesso Single Sign-On disabilitato:

- Se non si è pronti a integrare questa applicazione con l'accesso Single Sign-On di Azure AD
- In fase di test di altri aspetti dell'applicazione
- Come livello di sicurezza per un'applicazione locale che non richiede l'autenticazione degli utenti. Con la modalità disabilitata, l'utente deve eseguire l'autenticazione. 

## <a name="integrated-windows-authentication-iwa-sso"></a>SSO con autenticazione integrata di Windows

Azure AD Application Proxy offre l'accesso Single Sign-On (SSO) alle applicazioni che usano l'[autenticazione integrata di Windows](/aspnet/web-api/overview/security/integrated-windows-authentication) o alle applicazioni che riescono a riconoscere le attestazioni. Se l'applicazione usa l'autenticazione integrata di Windows, Application Proxy esegue l'autenticazione dell'utente all'applicazione mediante la delega vincolata Kerberos. Per le applicazioni con riconoscimento delle attestazione che considerano attendibile Azure Active Directory, l'accesso Single Sign-On funziona perché l'utente è già stato autenticato da Azure AD.

Usare la modalità Single Sign-On con autenticazione integrata di Windows:

- Per concedere l'accesso Single Sign-On a un'app in locale che usa l'autenticazione integrata di Windows. 

Per configurare un'app locale per l'autenticazione integrata di Windows, vedere [Kerberos Constrained Delegation for single sign-on to your applications with Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md) (Delega vincolata Kerberos per l'accesso Single Sign-On alle applicazioni con Application Proxy). 

### <a name="how-single-sign-on-with-kcd-works"></a>Funzionamento di Single Sign-On con KCD
Questo diagramma illustra il flusso quando un utente accede a un'applicazione locale che usa l'autenticazione integrata di Windows.

![Diagramma del flusso di autenticazione di Microsoft AAD](./media/application-proxy-configure-single-sign-on-with-kcd/AuthDiagram.png)

1. L'utente immette l'URL per accedere all'applicazione locale tramite il proxy di applicazione.
2. Il proxy di applicazione reindirizza la richiesta ai servizi di autenticazione di Azure AD per la preautenticazione. A questo punto, Azure AD applica gli eventuali criteri di autenticazione e autorizzazione appropriati, ad esempio l'autenticazione a più fattori. Se l'utente viene convalidato, Azure AD crea un token e lo invia all'utente.
3. L'utente passa il token al proxy di applicazione.
4. Application Proxy convalida il token e recupera il nome dell'entità utente (UPN) dal token. Invia poi la richiesta, l'UPN e il nome dell'entità servizio (SPN) al connettore tramite un canale protetto doppiamente autenticato.
5. Il connettore usa la negoziazione della delega vincolata Kerberos con l'istanza di Active Directory locale, rappresentando l'utente per ottenere un token Kerberos per l'applicazione.
6. Active Directory invia il token Kerberos per l'applicazione al connettore.
7. Il connettore invia la richiesta originale al server dell'applicazione, usando il token Kerberos ricevuto da Active Directory.
8. L'applicazione invia la risposta al connettore, che viene quindi restituita al servizio Application Proxy e infine all'utente.

## <a name="header-based-sso"></a>SSO basato su intestazione

L'accesso Single Sign-On basato su intestazione è valido per le applicazioni che usano le intestazioni HTTP per l'autenticazione. Questo metodo di accesso usa un servizio di autenticazione di terze parti chiamato PingAccess. Un utente deve solo eseguire l'autenticazione ad Azure AD. 

Usare l'accesso Single Sign-On basato su intestazione quando:

- Application Proxy e PingAccess sono configurati per l'applicazione

Per configurare l'autenticazione basata su intestazione, vedere [Autenticazione basata su intestazione per l'accesso Single Sign-On con il proxy di applicazione](application-proxy-configure-single-sign-on-with-ping-access.md). 

### <a name="what-is-pingaccess-for-azure-ad"></a>Che cos'è PingAccess per Azure AD?

Grazie a PingAccess per Azure AD, gli utenti possono eseguire l'accesso e l'accesso Single Sign-On alle applicazioni che usano intestazioni per l'autenticazione. Application Proxy tratta queste applicazioni come qualsiasi altra, usando Azure AD per autenticare l'accesso e quindi passando il traffico attraverso il servizio del connettore. Dopo l'autenticazione, il servizio PingAccess convertirà il token di accesso di Azure AD in un formato di intestazione inviato all'applicazione.

Gli utenti non noteranno nulla di diverso quando eseguono l'accesso per usare le applicazioni aziendali. Possono comunque lavorare da qualsiasi luogo e dispositivo. I connettori di Application Proxy indirizzano il traffico remoto a tutte le applicazioni e continueranno a bilanciare il carico automaticamente.

### <a name="how-do-i-get-a-license-for-pingaccess"></a>Come ottenere una licenza per PingAccess?

Poiché questo scenario è il risultato di una partnership fra Azure Active Directory e PingAccess, sono necessarie le licenze per entrambi i servizi. Le sottoscrizioni Premium di Azure Active Directory, tuttavia, includono una licenza PingAccess di base che copre fino a 20 applicazioni. Se è necessario pubblicare più di 20 applicazioni basate su intestazione, è possibile acquistare una licenza aggiuntiva da PingAccess. 

Per altre informazioni, vedere [Edizioni di Azure Active Directory](../fundamentals/active-directory-whatis.md).

## <a name="related-articles"></a>Articoli correlati
* [Esercitazioni per l'integrazione di applicazioni SaaS con Azure Active Directory](../saas-apps/tutorial-list.md)
* [Esercitazione per configurare l'accesso Single Sign-On](configure-single-sign-on-portal.md)
* [Introduzione alla gestione dell'accesso alle applicazioni](what-is-access-management.md)
* Collegamento di download: [piano di distribuzione di Single Sign-On](https://aka.ms/SSODeploymentPlan).


