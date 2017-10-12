---
title: "Che cos'è il pannello di accesso in Azure Active Directory? | Microsoft Docs"
description: Informazioni su come usare le varianti del pannello di accesso (Web browser, app per Android e app per iPhone e iPad) per accedere alle app SaaS.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bd9066c251188c0f18fe1a9403baa2beaeeb987c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-the-access-panel"></a>Che cos'è il pannello di accesso?

Il pannello di accesso è un portale basato sul Web. Il pannello consente a un utente con un account aziendale o dell'istituto di istruzione in Azure Active Directory di visualizzare e avviare applicazioni basate sul cloud a cui l'amministratore di Azure AD ha concesso l'accesso. Tramite il pannello di accesso è anche possibile usare le funzionalità di gestione self-service di gruppi e app.

Il pannello di accesso è separato dal portale di Azure e non richiede una sottoscrizione di Azure.

![Pannello di accesso][1]

Il pannello di accesso consente di modificare alcune impostazioni del profilo, ad esempio:

- Cambiare la password associata a un account aziendale o dell'istituto di istruzione

- Modificare le impostazioni di ripristino password

- Modificare le impostazioni di preferenze e contatti relative a Multi-Factor Authentication (per gli account per cui un amministratore ne ha richiesto l'uso)

- Visualizzare i dettagli dell'account, ad esempio l'ID utente, l'indirizzo di posta elettronica alternativo, i numeri del cellulare e dell'ufficio e i dispositivi

- Visualizzare e avviare applicazioni basate su cloud a cui l'amministratore di Azure AD ha concesso l'accesso. Per altre informazioni sul pannello di accesso dal punto di vista dell'utente, vedere Uso del pannello di accesso. 

- Gestire i gruppi in modalità self-service. In particolare, l'amministratore può creare e gestire gruppi di sicurezza e richiedere appartenenze a gruppi di sicurezza in Azure AD. Per altre informazioni, vedere [Gestione dei gruppi in modalità self-service per gli utenti in Azure AD](active-directory-accessmanagement-self-service-group-management.md) e [Gestione dei gruppi](active-directory-manage-groups.md).




## <a name="accessing-the-access-panel"></a>Apertura del pannello di accesso

Per usare il pannello di accesso, aprire l'URL seguente in un Web browser: `http://myapps.microsoft.com`

Se sono state configurate impostazioni di personalizzazione per la pagina di accesso, è possibile caricarle aggiungendo il dominio dell'organizzazione alla fine dell'URL: `http://myapps.microsoft.com/<your domain>.com`

In questo caso, è possibile usare qualsiasi nome di dominio attivo o verificato che sia stato configurato nel portale di Azure.

![Nome di dominio di Wingtip Toys][2]  

Questo URL deve essere distribuito a tutti gli utenti che eseguiranno l'accesso ad applicazioni integrate con Azure AD.

## <a name="authentication"></a>Autenticazione

Per raggiungere il pannello di accesso, è necessario essere autenticati in Azure AD con un account aziendale o dell'istituto di istruzione. È possibile essere autenticati direttamente in Azure AD. In alternativa, se un'organizzazione ha configurato la federazione con Active Directory Federation Services (AD FS) o altre tecnologie, è possibile essere autenticati con Windows Server Active Directory.

Se si ha una sottoscrizione di Azure oppure un abbonamento a Office 365 ed è stato usato il portale di Azure o un'applicazione di Office 365, è possibile visualizzare l'elenco di applicazioni senza dover eseguire di nuovo l'accesso. Se non si è autenticati, viene chiesto di accedere usando il nome utente e la password dell'account in Azure AD. Se l'organizzazione ha configurato la federazione, sarà sufficiente digitare il nome utente.

Dopo l'autenticazione, è possibile interagire con le applicazioni che l'amministratore ha integrato con la directory. Per informazioni su come integrare le applicazioni con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Requisiti del Web browser

Il pannello di accesso richiede almeno un browser che supporti JavaScript e abbia CSS abilitato. Affinché gli utenti possano accedere alle applicazioni tramite l'accesso Single Sign-On (SSO) basato su password, è necessario installare l'estensione del pannello di accesso nel browser. Questa estensione viene scaricata automaticamente quando si seleziona un'applicazione configurata per l'accesso SSO basato su password.

L'estensione del pannello di accesso è attualmente disponibile per i browser Internet Explorer 8 e versioni successive, Microsoft Edge, Chrome e Firefox.

## <a name="mobile-app-support"></a>Supporto per app per dispositivi mobili

Il team di Azure Active Directory pubblica l'app per dispositivi mobili App personali. Quando si installa l'app, è possibile accedere alle applicazioni SSO basate su password da dispositivi iOS e Android.

> [!NOTE]
> È possibile accedere alle applicazioni che supportano la federazione con Azure AD (tra cui Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 e più di 70 altre applicazioni) praticamente in qualsiasi Web browser, su qualsiasi dispositivo, senza necessità di un plug-in o di un'app per dispositivi mobili. Tutte le altre [esperienze del pannello di accesso](https://myapps.microsoft.com/) non richiedono l'uso dell'app per dispositivi mobili App personali in un dispositivo mobile.
>
>

### <a name="my-apps-for-android"></a>App personali per Android

App personali per Android è supportata su qualsiasi dispositivo Android che esegue Android 4.1 e versioni successive.  
È disponibile in [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![App personali per Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>App personali per iPhone e iPad

App personali per iOS è supportata su qualsiasi iPhone o iPad che esegue iOS 7 e versioni successive.  
È disponibile nell'[App Store di Apple](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![App personali per iOS][4]    



## <a name="managed-browser-for-my-apps"></a>Managed browser per App personali

App personali si integra anche in Intune Managed Browser. Intune Managed Browser per dispositivi iOS e Android svolge un ruolo fondamentale nel garantire la sicurezza dei dati nei dispositivi mobili. Consente di visualizzare ed esplorare in tutta sicurezza pagine Web che potrebbero contenere informazioni aziendali e garantisce un'esperienza di esplorazione Web sicura.  
È possibile accedere rapidamente ad App personali nella home page di Managed Browser e dai segnalibri, per poter aprire qualsiasi applicazione desiderata con un numero minore di clic.

Questo strumento è disponibile nell'[App Store di Apple](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) e in [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser&hl=en).

![Managed Browser per App personali][5]    





## <a name="tips-for-testing-the-user-experience"></a>Suggerimenti per testare l'esperienza utente

Gli amministratori di Azure che hanno eseguito l'accesso al portale di Azure usando un account nella directory accedono automaticamente al pannello di accesso con l'account corrente. In questo caso, potranno visualizzare tutte le applicazioni che sono state loro assegnate.

**Per testare un account utente *diverso*:**

1. Fare clic sul menu utente nell'angolo superiore destro del portale di Azure o del pannello di accesso e selezionare **Disconnetti**. 
2. Passare al [pannello di accesso](http://myapps.microsoft.com).
3. Nella pagina di accesso immettere il nome utente e la password dell'account nella directory da testare.


## <a name="starting-applications"></a>Avvio delle applicazioni

Nel pannello di accesso possono essere visualizzati diversi tipi di applicazioni.

### <a name="office-365-applications"></a>Applicazioni di Office 365

Se l'organizzazione usa applicazioni di Office 365 di cui si ha la licenza, tali applicazioni vengono visualizzate nel pannello di accesso.

Quando si fa clic sul riquadro di un'applicazione di Office 365, si viene reindirizzati a tale applicazione e l'accesso viene eseguito automaticamente.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Applicazioni Microsoft e di terze parti configurate con il servizio Single Sign-On basato su federazione

L'amministratore può aggiungere applicazioni nella sezione Active Directory del portale di Azure con la modalità SSO impostata su **Single Sign-On di Microsoft Azure AD**. È possibile visualizzare le applicazioni solo se l'amministratore ha concesso l'accesso a tali applicazioni in modo esplicito.

Quando si fa clic su un riquadro di una di queste applicazioni, si viene reindirizzati a tale applicazione e l'accesso viene eseguito automaticamente.

### <a name="password-based-sso-without-identity-provisioning"></a>Single Sign-On basato su password senza provisioning delle identità

L'amministratore può aggiungere applicazioni nella sezione Active Directory del portale di Azure con la modalità SSO impostata su **Accesso Single Sign-On basato su password**. Tutti gli utenti della directory possono visualizzare tutte le applicazioni configurate in questa modalità.

La prima volta che si fa clic su un riquadro di una di queste applicazioni, viene chiesto di installare il plug-in per l'accesso Single Sign-On basato su password per Internet Explorer o Chrome. L'installazione potrebbe richiedere il riavvio del Web browser. Quando si torna al pannello di accesso e si fa clic di nuovo sul riquadro dell'applicazione, viene chiesto di specificare un nome utente e una password per l'applicazione. Dopo l'immissione di nome utente e password, queste credenziali vengono archiviate in modo sicuro e collegate all'account in Azure AD.

La volta successiva che si fa clic sul riquadro dell'applicazione, l'accesso all'applicazione viene eseguito automaticamente.  
Non è necessario immettere nuovamente le credenziali e/o installare il plug-in per l'accesso SSO basato su password.

Se le credenziali sono cambiate nell'applicazione di destinazione di terze parti, è necessario aggiornare anche le credenziali archiviate in Azure AD. 

**Per aggiornare le credenziali:**

1. Selezionare l'icona nel riquadro dell'applicazione.
2. Selezionare **Aggiorna credenziali** per immettere di nuovo nome utente e password per l'applicazione.


### <a name="password-based-sso-with-identity-provisioning"></a>Single Sign-On basato su password con provisioning delle identità

L'amministratore può aggiungere applicazioni nella sezione **Active Directory** del portale di Azure con la modalità SSO impostata su **Accesso Single Sign-On basato su password**, insieme al provisioning delle identità.

La prima volta che si fa clic su un riquadro di una di queste applicazioni, viene chiesto di installare il **plug-in per l'accesso SSO basato su password per Internet Explorer o Chrome**. L'installazione potrebbe richiedere il riavvio del Web browser.  
Quando si torna nel pannello di accesso e si fa clic di nuovo sul riquadro dell'applicazione, l'accesso all'applicazione viene eseguito automaticamente.

Alcune applicazioni potrebbero richiedere di cambiare la password al primo accesso. Se le credenziali sono cambiate nell'applicazione di destinazione di terze parti, è necessario aggiornare anche le credenziali archiviate in Azure AD. 

**Per aggiornare le credenziali:**

1. Selezionare l'icona nel riquadro dell'applicazione.
2. Selezionare **Aggiorna credenziali** per immettere di nuovo nome utente e password per l'applicazione.


### <a name="application-with-existing-sso-solutions"></a>Applicazione con soluzioni Single Sign-On esistenti

Per configurare l'accesso SSO per un'applicazione, il portale di Azure fornisce una terza opzione, ovvero **Accesso Single Sign-On esistente**. Questa opzione consente all'amministratore di creare un collegamento a un'applicazione e di inserirlo nel pannello di accesso per gli utenti selezionati.

Se, ad esempio, un'applicazione è configurata per l'autenticazione degli utenti tramite AD FS 2.0, l'amministratore può usare l'opzione **Accesso Single Sign-On esistente** per creare un collegamento nel pannello di accesso. Quando si accede al collegamento, viene eseguita l'autenticazione tramite AD FS 2.0 o qualunque soluzione SSO fornita dall'applicazione.


## <a name="next-steps"></a>Passaggi successivi

- Per visualizzare un elenco di tutti gli argomenti relativi alla gestione delle applicazioni, vedere [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md).
 
- Per informazioni su come integrare un'app SaaS in Azure AD, vedere l'[elenco di esercitazioni sull'integrazione di app SaaS](active-directory-saas-tutorial-list.md).
 
- Per altre informazioni sulla gestione di app con Azure AD, vedere l'[introduzione a Single Sign-On e alla gestione dell'accesso alle app con Azure Active Directory](active-directory-appssoaccess-whatis.md).
 
- Per altre informazioni sul provisioning degli utenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[3]: ./media/active-directory-saas-access-panel-introduction/03.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
