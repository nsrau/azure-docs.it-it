---
title: Introduzione al pannello di accesso | Documentazione Microsoft
description: Informazioni su come usare le variazione del pannello di accesso (Web browser, app per Android e app per iPhone e iPad) per accedere alle app SaaS.
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
ms.date: 05/16/2017
ms.author: markvi
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 6ea03adaabc1cd9e62aa91d4237481d8330704a1
ms.openlocfilehash: c5a0f3f958d42d514a95477c1e39f02c78307d4d
ms.contentlocale: it-it
ms.lasthandoff: 04/06/2017

---
# <a name="what-is-the-access-panel"></a>Che cos'è il pannello di accesso?
Il pannello di accesso è un portale basato sul Web. Il pannello consente a un utente che dispone di un account aziendale o dell'istituto di istruzione in Azure Active Directory (Azure AD) di visualizzare e avviare applicazioni basate su cloud a cui l'amministratore di Azure AD ha concesso l'accesso. In aggiunta, un utente che dispone di edizioni di Azure AD può usufruire di funzionalità di gestione di gruppi self-service tramite il pannello di accesso.

Il pannello di accesso è separato dal portale di Azure e non richiede una sottoscrizione Azure.

![Pannello di accesso][1]

Il pannello di accesso consente agli utenti di modificare alcune delle impostazioni del proprio profilo, ad esempio:

* Cambiare la password associata al proprio account aziendale o dell'istituto di istruzione.
* Modificare le impostazioni di reimpostazione della password.
* Modificare le impostazioni di preferenze e contatti relative a Multi-Factor Authentication (per gli account per cui un amministratore ne ha richiesto l'uso).
* Visualizzare i dettagli dell'account, ad esempio l'ID utente, l'indirizzo di posta elettronica alternativo e i numeri del cellulare e dell'ufficio.
* Visualizzare e avviare applicazioni basate su cloud a cui l'amministratore di Azure AD ha concesso l'accesso. Per altre informazioni sul Pannello di accesso dal punto di vista dell'utente, vedere [Uso del pannello di accesso](https://msdn.microsoft.com/library/azure/dn756411.aspx).
* Gestire i gruppi in modalità self-service. In particolare, l'amministratore può creare e gestire gruppi di sicurezza e richiedere appartenenze a gruppi di sicurezza in Azure AD. Per altre informazioni, vedere [Gestione dei gruppi in modalità self-service per gli utenti in Azure AD](active-directory-accessmanagement-self-service-group-management.md) e [Gestione dei gruppi](active-directory-manage-groups.md).

## <a name="accessing-the-access-panel"></a>Apertura del pannello di accesso
Per usare il pannello di accesso, è necessario specificare il seguente URL in un Web browser:  <br>
**http://myapps.microsoft.com**

Se sono state configurate impostazioni di personalizzazione per la pagina di accesso, è possibile caricarle per impostazione predefinita aggiungendo il dominio dell'organizzazione alla fine dell'URL:  <br>
**http://myapps.microsoft.com/contosobuild.com**

In questo caso, è possibile usare qualsiasi nome di dominio attivo o verificato che sia stato configurato nella scheda **Domini** della directory nel portale di Azure, come illustrato nella schermata seguente:

![Nome di dominio di Wingtip Toys][2]  

Questo URL deve essere distribuito a tutti gli utenti che eseguiranno l'accesso ad applicazioni integrate con Azure AD.

## <a name="authentication"></a>Autenticazione
Per raggiungere il pannello di accesso, un utente deve essere autenticato in Azure AD con un account aziendale o dell'istituto di istruzione. Un utente può essere autenticato direttamente in Azure AD. In alternativa, se in un'organizzazione è stata configurata la federazione con Active Directory Federation Services (ADFS) o altre tecnologie, gli utenti possono essere autenticati da Windows Server Active Directory.

Se un utente ha una sottoscrizione Azure oppure Office 365 e ha usato il portale di Azure o un'applicazione di Office 365, visualizzerà l'elenco delle applicazioni senza dover eseguire di nuovo l'accesso. Gli utenti non autenticati dovranno accedere usando il nome utente e la password del proprio account in Azure AD. Se l'organizzazione ha configurato la federazione, sarà sufficiente digitare il nome utente.

Dopo l'autenticazione, gli utenti possono interagire con le applicazioni che sono state integrate con la directory dall'amministratore. Per informazioni su come integrare le applicazioni con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](active-directory-appssoaccess-whatis.md).

## <a name="web-browser-requirements"></a>Requisiti del Web browser
Per il pannello di accesso è previsto come requisito minimo del browser il supporto di JavaScript e l'abilitazione di CSS. Affinché gli utenti possano accedere alle applicazioni tramite il servizio Single Sign-On (SSO) basato su password, è necessario installare l'estensione del pannello di accesso nel browser degli utenti. Questa estensione viene scaricata automaticamente quando un utente seleziona un'applicazione configurata per il servizio Single Sign-On basato su password.

L'estensione del pannello di accesso è attualmente disponibile per i browser Internet Explorer 8 e versioni successive, Microsoft Edge, Chrome e Firefox.

## <a name="mobile-app-support"></a>Supporto per app per dispositivi mobili
Il team di Azure Active Directory pubblica l'app per dispositivi mobili App personali. Quando gli utenti installano l'app possono accedere alle applicazioni SSO basate su password da dispositivi iOS e Android.

> [!NOTE]
> Gli utenti possono accedere alle applicazioni che supportano la federazione con Azure AD (tra cui Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 e più di 70 altre applicazioni) praticamente in qualsiasi Web browser su qualsiasi dispositivo, senza richiedere un plug-in o un'app per dispositivi mobili. Anche la parte restante del [pannello di accesso](https://myapps.microsoft.com/) non richiede l'uso di App personali da un dispositivo mobile.
>
>

### <a name="my-apps-for-android"></a>App personali per Android
App personali per Android è supportata su qualsiasi dispositivo Android che esegue Android 4.1 e versioni successive. È attualmente disponibile in [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.myapps).

![Schermata di App personali per Android][3]   

### <a name="my-apps-for-iphone-and-ipad"></a>App personali per iPhone e iPad
App personali per iOS è supportata su qualsiasi iPhone o iPad che esegue iOS 7 e versioni successive. È attualmente disponibile nell'App Store di Apple.

![Schermata di App personali per iOS][4]    

## <a name="tips-for-testing-the-user-experience"></a>Suggerimenti per testare l'esperienza utente
Gli amministratori di Azure che hanno eseguito l'accesso al portale di Azure usando un account nella directory accederanno automaticamente al pannello di accesso con l'account amministratore corrente. In questo caso, potranno visualizzare tutte le applicazioni assegnate a tale account.

Per eseguire il test con un account utente *diverso*:

1. Fare clic sul menu utente nell'angolo superiore sinistro del portale di Azure o del pannello di accesso e selezionare **Esci**. L'utente viene disconnesso da Azure AD.
2. Passare al [pannello di accesso](http://myapps.microsoft.com).
3. Nella pagina di accesso immettere il nome utente e la password dell'account nella directory da testare.

## <a name="starting-applications"></a>Avvio delle applicazioni
Nel pannello di accesso possono essere visualizzati diversi tipi di applicazioni.

### <a name="office-365-applications"></a>Applicazioni di Office 365
Se un'organizzazione usa applicazioni di Office 365 e l'utente ha le relative licenze, tali applicazioni vengono visualizzate nel pannello di accesso dell'utente.

Quando fa clic sul riquadro di un'applicazione di Office 365, l'utente viene reindirizzato a tale applicazione e l'accesso viene eseguito automaticamente.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Applicazioni Microsoft e di terze parti configurate con il servizio Single Sign-On basato su federazione
L'amministratore può aggiungere applicazioni nella sezione Active Directory del portale di Azure con la modalità Single Sign-On impostata su **Single Sign-On di Microsoft Azure AD**. Un utente visualizzerà queste applicazioni solo se l'amministratore ha concesso all'utente l'accesso a tali applicazioni in modo esplicito.

Quando fa clic sul riquadro di una di queste applicazioni, l'utente viene reindirizzato a tale applicazione e l'accesso viene eseguito automaticamente.

### <a name="password-based-sso-without-identity-provisioning"></a>Single Sign-On basato su password senza provisioning delle identità
L'amministratore può aggiungere applicazioni nella sezione Active Directory del portale di Azure con la modalità Single Sign-On impostata su **Accesso Single Sign-On basato su password**. Tutti gli utenti della directory visualizzano tutte le applicazioni configurate in questa modalità.

La prima volta che fa clic sul riquadro di una di queste applicazioni, l'utente visualizza un messaggio in cui viene chiesto di installare il plug-in Single Sign-On basato su password per Internet Explorer o Chrome. L'installazione potrebbe richiedere all'utente di riavviare il Web browser. Quando torna nel pannello di accesso e fa clic di nuovo sul riquadro dell'applicazione, l'utente visualizza un messaggio in cui viene chiesto di specificare un nome utente e una password per l'applicazione. Dopo che l'utente immette il nome utente e password, queste credenziali vengono archiviate in Azure AD in modo sicuro e collegate all'account Azure AD dell'utente.

La volta successiva che l'utente fa clic sul riquadro dell'applicazione, verrà automaticamente connesso all'applicazione. L'utente non dovrà immettere nuovamente le credenziali e/o reinstallare il plug-in dell'accesso Single Sign-On basato su password.

Se le credenziali dell'utente sono cambiate nell'applicazione di terze parti di destinazione, l'utente deve aggiornare anche le credenziali archiviate in Azure AD. Per aggiornare le credenziali, un utente deve fare clic sull'icona nel riquadro dell'applicazione e selezionare l'opzione **Aggiorna credenziali** per immettere di nuovo il nome utente e la password per l'applicazione.

### <a name="password-based-sso-with-identity-provisioning"></a>Single Sign-On basato su password con provisioning delle identità
L'amministratore può aggiungere applicazioni nella sezione Active Directory del portale di Azure con la modalità Single Sign-On impostata su **Accesso Single Sign-On basato su password**, insieme al provisioning delle identità.

La prima volta che fa clic sul riquadro di una di queste applicazioni, l'utente visualizza un messaggio in cui viene chiesto di installare il plug-in Single Sign-On basato su password per Internet Explorer o Chrome. L'installazione potrebbe richiedere all'utente di riavviare il Web browser. Quando l'utente torna nel pannello di accesso e fa clic di nuovo sul riquadro dell'applicazione, l'accesso all'applicazione viene eseguito automaticamente.

È possibile che alcune applicazioni richiedano di cambiare la password al primo accesso. Se le credenziali dell'utente sono cambiate nell'applicazione di terze parti di destinazione, l'utente deve aggiornare anche le credenziali archiviate in Azure AD. Per aggiornare le credenziali, un utente deve fare clic sull'icona nel riquadro dell'applicazione e selezionare l'opzione **Aggiorna credenziali** per immettere di nuovo il nome utente e la password per l'applicazione.

### <a name="application-with-existing-sso-solutions"></a>Applicazione con soluzioni Single Sign-On esistenti
Quando si configura l'accesso Single Sign-On per un'applicazione, il portale di Azure fornisce una terza opzione di **Accesso Single Sign-On esistente**. Questa opzione consente all'amministratore di creare un collegamento a un'applicazione e di posizionarlo nel pannello di accesso per gli utenti selezionati.

Ad esempio, se un'applicazione è stata configurata per l'autenticazione degli utenti tramite Active Directory Federation Services 2.0, l'amministratore può usare l'opzione **Accesso Single Sign-On esistente** per creare un collegamento al pannello di accesso. Quando gli utenti accedono al collegamento, vengono autenticati tramite Active Directory Federation Services 2.0 o qualunque soluzione di accesso Single Sign-On esistente offerta dall'applicazione.

## <a name="related-articles"></a>Articoli correlati
* [Indice di articoli per la gestione di applicazioni in Azure Active Directory](active-directory-apps-index.md)
* [Elenco di esercitazioni pratiche sulla procedura di integrazione delle applicazioni SaaS](active-directory-saas-tutorial-list.md)
* [Introduzione a Single Sign-On e alla gestione dell'accesso alle App con Azure Active Directory](active-directory-appssoaccess-whatis.md)
* [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS](active-directory-saas-app-provisioning.md)

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/ic767166.png
[2]: ./media/active-directory-saas-access-panel-introduction/ic767167.png
[3]: ./media/active-directory-saas-access-panel-introduction/ic767168.png
[4]: ./media/active-directory-saas-access-panel-introduction/ic767169.png

