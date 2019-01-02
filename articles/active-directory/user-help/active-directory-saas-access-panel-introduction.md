---
title: Cos'è il portale delle app personali in Azure Active Directory? | Microsoft Docs
description: Informazioni su come usare le varianti del portale delle app personali, come Web browser, app per Android e app per iPhone e iPad, per accedere alle app SaaS.
services: active-directory
author: eross-msft
manager: mtillman
ms.assetid: c0252d01-7e6e-4f79-a70e-600479577dfd
ms.service: active-directory
ms.component: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: lizross
ms.reviewer: asteen
ms.openlocfilehash: 129f77ec9ea4d4e2633e1ea0b02795bc0df26433
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: it-IT
ms.lasthandoff: 12/04/2018
ms.locfileid: "52834707"
---
# <a name="what-is-the-myapps-portal"></a>Che cos'è il portale App personali?

Se si ha un account aziendale o dell'istituto di istruzione in Azure Active Directory (Azure AD), è possibile usare il portale Web delle app personali per visualizzare e avviare le applicazioni basate sul cloud a cui un amministratore di Azure AD ha concesso l'accesso. Tramite il portale delle app personali è anche possibile usare le funzionalità di gestione self-service di gruppi e app.

Il portale delle app personali è separato dal portale di Azure. Non è necessario avere una sottoscrizione di Azure.

![Portale delle app personali][1] Con il portale delle app personali è possibile modificare alcune impostazioni del profilo ed eseguire le operazioni seguenti:

- Cambiare la password associata al proprio account aziendale o dell'istituto di istruzione.

- Modificare le impostazioni di reimpostazione della password.

- Modificare le impostazioni di preferenze e contatti relative a Multi-Factor Authentication (per gli account per cui un amministratore ne ha richiesto l'uso).

- Visualizzare i dettagli dell'account, ad esempio l'ID utente, l'indirizzo di posta elettronica alternativo, i numeri del cellulare e dell'ufficio e i dispositivi.

- Visualizzare e avviare applicazioni basate su cloud a cui l'amministratore di Azure AD ha concesso l'accesso. 

- Gestire i gruppi in modalità self-service. Gli amministratori possono creare e gestire gruppi di sicurezza e richiedere appartenenze a gruppi di sicurezza in Azure AD. Per altre informazioni, vedere [Gestione dei gruppi in modalità self-service per gli utenti in Azure AD](../users-groups-roles/groups-self-service-management.md) e [Gestione dei gruppi](../fundamentals/active-directory-manage-groups.md).

## <a name="access-the-myapps-portal"></a>Accedere al portale delle app personali

Per accedere al portale delle app personali passare a `https://myapps.microsoft.com`.

Se sono state configurate impostazioni di personalizzazione per la pagina di accesso, è possibile caricarle aggiungendo il dominio dell'organizzazione all'URL ad esempio `https://myapps.microsoft.com/<your domain>.com`.

È possibile usare qualsiasi nome di dominio attivo o verificato che sia stato configurato nel portale di Azure, come illustrato di seguito: ![Nome di dominio di Wingtip Toys][2]  

Distribuire l'URL a tutti gli utenti che eseguiranno l'accesso ad applicazioni integrate con Azure AD.

## <a name="authentication"></a>Authentication

Per raggiungere il portale delle app personali è necessario essere autenticati in Azure AD con un account aziendale o dell'istituto di istruzione. È possibile essere autenticati direttamente in Azure AD. In alternativa, se un'organizzazione ha configurato la federazione con Active Directory Federation Services (AD FS) o altre tecnologie, è possibile essere autenticati con Windows Server Active Directory.

Se si ha una sottoscrizione di Azure oppure un abbonamento a Office 365 ed è stato usato il portale di Azure o un'applicazione di Office 365, è possibile visualizzare l'elenco di applicazioni senza dover eseguire di nuovo l'accesso. Se non si è autenticati, viene chiesto di accedere usando il nome utente e la password dell'account in Azure AD. Se l'organizzazione ha configurato la federazione, sarà sufficiente digitare il nome utente.

Dopo l'autenticazione, è possibile interagire con le applicazioni che l'amministratore ha integrato con la directory. Per informazioni su come integrare le applicazioni con Azure AD, vedere [Informazioni sull'accesso alle applicazioni e Single Sign-On con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="web-browser-requirements"></a>Requisiti del Web browser

Come minimo, per il portale delle app personali è necessario un browser che supporti JavaScript e in cui sia abilitato CSS. Per poter accedere alle applicazioni tramite l'accesso Single Sign-On (SSO) basato su password, è necessario installare l'estensione del portale delle app personali nel browser. Questa estensione viene scaricata automaticamente quando si seleziona un'applicazione configurata per l'accesso SSO basato su password.

Il programma di installazione è specifico dell'architettura. Facendo clic sul collegamento di download, si otterrà solo il programma di installazione per l'architettura del sistema operativo attualmente in esecuzione. Se si è un amministratore della distribuzione dell'applicazione, assicurarsi di visitare il collegamento di download da un dispositivo a 32 bit e a 64 bit per ottenere entrambi i programmi di installazione.


L'estensione del portale delle app personali è attualmente disponibile per:
- **Microsoft Edge**: su Windows 10 Anniversary Edition o versioni successive. 
- **Chrome**: in Windows 7 o versioni successive e in MacOS X o versioni successive.
- **Firefox 26.0 o versioni successive**: in Windows XP SP2 o versioni successive e in Mac OS X 10.6 o versioni successive.
- **Internet Explorer 11**: in Windows 7 o versioni successive (supporto limitato).

## <a name="my-apps-secure-sign-in-extension"></a>Estensione per l'accesso sicuro alle app personali
Per usare l'accesso Single Sign-On basato su password, è necessario usare l'estensione. Dopo aver installato l'estensione, è possibile accedervi per abilitare le funzionalità aggiuntive selezionando **Accedere per iniziare**. 

- È possibile accedere direttamente a un'app usando l'**URL di accesso** dell'app. Quando si usa l'URL dell'app, l'estensione rileva l'azione e consente di accedere dall'estensione.
- È possibile avviare qualsiasi app dal portale delle app personali usando la funzionalità di *ricerca rapida* dell'estensione. 
- L'estensione mostra le ultime tre applicazioni che sono state avviate nella sezione **Usati di recente**.
- È possibile usare URL aziendali interne in remoto tramite [Proxy dell'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-get-started)

> [!NOTE]
> Sono disponibili funzionalità aggiuntive per Microsoft Edge, Chrome e Firefox.
>
È possibile scaricare l'estensione direttamente dai siti seguenti:
- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)
- [Firefox](https://go.microsoft.com/fwlink/?linkid=866366)

Se si usa un URL delle app personali diverso da `https://myapps.microsoft.com`, configurare l'URL predefinito seguendo questa procedura:
1. *Senza* accedere all'estensione, fare clic con il pulsante destro del mouse sull'icona dell'estensione.
2. Nel menu selezionare **My Apps URL** (URL app personali).
3. Selezionare l'URL predefinito.
4. Selezionare l'icona dell'estensione.
5. Selezionare **Accedere per iniziare**.

Per usare gli URL aziendali interni in remoto con l'estensione, procedere come segue:
1. [Configurare il proxy dell'applicazione](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable) nel tenant.
2. [Pubblicare l'applicazione](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal) e l'URL con il proxy dell'applicazione.
3. Installare l'estensione e accedervi selezionando Accedi per iniziare.
4. È ora possibile passare all'URL aziendale interno anche in remoto.

> [!NOTE]
> È anche possibile disattivare il reindirizzamento automatico agli URL aziendali selezionando l'icona dell'ingranaggio delle impostazioni nel menu principale, quindi selezionando **Disattivato** come l'opzione di reindirizzamento dell'URL interno aziendale.


## <a name="mobile-app-support"></a>Supporto per app per dispositivi mobili

Il team di Azure Active Directory pubblica l'app per dispositivi mobili App personali. Quando si installa l'app, è possibile accedere alle applicazioni SSO basate su password da dispositivi iOS e Android.

> [!NOTE]
> È possibile accedere alle applicazioni che supportano la federazione con Azure AD (tra cui Salesforce, Google Apps, Dropbox, Box, Concur, Workday, Office 365 e più di 70 altre applicazioni) praticamente in qualsiasi Web browser, su qualsiasi dispositivo, senza necessità di un plug-in o di un'app per dispositivi mobili. Tutte le altre [esperienze del portale delle app personali](https://myapps.microsoft.com/) non richiedono l'uso dell'app per dispositivi mobili App personali in un dispositivo mobile.

### <a name="my-apps-for-iphone-and-ipad"></a>App personali per iPhone e iPad

App personali per iOS è supportata in qualsiasi iPhone o iPad che esegue iOS 7 o versioni successive.  

È disponibile nell'[App Store di Apple](https://itunes.apple.com/us/app/my-apps-azure-active-directory/id824048653?mt=8).

![App personali per iOS][4]    


## <a name="intune-managed-browser-for-my-apps"></a>Intune Managed Browser per App personali

App personali si integra anche in Intune Managed Browser. Intune Managed Browser per dispositivi iOS e Android consente di visualizzare e spostarsi tra le pagine Web che potrebbero contenere informazioni aziendali in modo più sicuro, offrendo una maggiore sicurezza dell'esperienza di esplorazione del Web.  

È possibile accedere ad App personali sia dall'home page di Intune Managed Browser che dai segnalibri, ovvero sono necessari meno clic per raggiungere le proprie app.

Intune Managed Browser è disponibile nell'[App Store di Apple](https://itunes.apple.com/us/app/microsoft-intune-managed-browser/id943264951?mt=8) e in [Google Play Store](https://play.google.com/store/apps/details?id=com.microsoft.intune.mam.managedbrowser).

![Managed browser per App personali][5]    


## <a name="tips-for-testing-the-user-experience"></a>Suggerimenti per testare l'esperienza utente

Gli amministratori di Azure che hanno eseguito l'accesso al portale di Azure usando un account nella directory accedono automaticamente al portale delle app personali con l'account corrente. Questa vista mostra tutte le applicazioni assegnate all'utente.

Per eseguire il test con un account utente *diverso*, eseguire le operazioni seguenti:

1. Nell'angolo superiore sinistro del portale di Azure o del portale delle app personali selezionare **Esci**. 
2. Accedere al [portale delle app personali](https://myapps.microsoft.com).
3. Nella pagina di accesso immettere il nome utente e la password dell'account nella directory da testare.


## <a name="starting-applications"></a>Avvio delle applicazioni

Questa sezione illustra diversi tipi di applicazioni che possono essere visualizzati nel portale delle app personali.

### <a name="office-365-applications"></a>Applicazioni di Office 365

Se l'organizzazione usa applicazioni di Office 365 di cui si ha la licenza, tali applicazioni vengono visualizzate nel portale delle app personali.

Quando si seleziona il riquadro di un'applicazione di Office 365, si viene reindirizzati a tale applicazione e l'accesso viene eseguito automaticamente.

### <a name="microsoft-and-third-party-applications-configured-with-federation-based-sso"></a>Applicazioni Microsoft e di terze parti configurate con il servizio Single Sign-On basato su federazione

L'amministratore può aggiungere applicazioni nella sezione Active Directory del portale di Azure con la modalità SSO impostata su **Single Sign-On di Microsoft Azure AD**. È possibile visualizzare le applicazioni solo se l'amministratore ha concesso l'accesso a tali applicazioni in modo esplicito.

Quando si seleziona un riquadro per un'applicazione, si viene reindirizzati a tale applicazione e l'accesso viene eseguito automaticamente.

### <a name="password-based-sso-without-identity-provisioning"></a>Single Sign-On basato su password senza provisioning delle identità

L'amministratore può aggiungere applicazioni nella sezione Active Directory del portale di Azure con la modalità SSO impostata su **Accesso Single Sign-On basato su password**. Tutti gli utenti della directory possono visualizzare tutte le applicazioni configurate in questa modalità.

La prima volta che si seleziona un riquadro di un'applicazione, viene chiesto di installare il plug-in per l'accesso Single Sign-On basato su password per Internet Explorer o Chrome. L'installazione potrebbe richiedere il riavvio del Web browser. Quando si torna al portale delle app personali e si seleziona di nuovo il riquadro dell'applicazione, viene chiesto di specificare un nome utente e una password per l'applicazione. Dopo l'immissione di nome utente e password, le credenziali vengono archiviate in modo sicuro e collegate all'account in Azure AD.

La volta successiva che si seleziona il riquadro dell'applicazione, l'accesso all'applicazione viene eseguito automaticamente.  

Non è necessario immettere nuovamente le credenziali e/o installare il plug-in per l'accesso SSO basato su password.

Se le credenziali sono cambiate nell'applicazione di destinazione di terze parti, è necessario aggiornare anche le credenziali archiviate in Azure AD. 

Per aggiornare le credenziali, eseguire le operazioni seguenti:

1. Selezionare l'icona nel riquadro dell'applicazione.
2. Selezionare **Aggiorna credenziali** per immettere di nuovo nome utente e password per l'applicazione.


### <a name="password-based-sso-with-identity-provisioning"></a>Single Sign-On basato su password con provisioning delle identità

L'amministratore può aggiungere applicazioni nella sezione Active Directory del portale di Azure con la modalità SSO impostata su **Accesso Single Sign-On basato su password**, insieme al provisioning delle identità.

La prima volta che si seleziona un riquadro di un'applicazione, viene chiesto di installare il plug-in per l'accesso Single Sign-On basato su password per Internet Explorer o Chrome. L'installazione potrebbe richiedere il riavvio del Web browser.  

Quando si torna nel portale delle app personali e si seleziona di nuovo il riquadro dell'applicazione, l'accesso all'applicazione viene eseguito automaticamente.

Alcune applicazioni potrebbero richiedere di cambiare la password al primo accesso. Se le credenziali sono cambiate nell'applicazione di destinazione di terze parti, è necessario aggiornare anche le credenziali archiviate in Azure AD. 

Per aggiornare le credenziali, eseguire le operazioni seguenti:

1. Selezionare l'icona nel riquadro dell'applicazione.
2. Selezionare **Aggiorna credenziali** per immettere di nuovo nome utente e password per l'applicazione.


### <a name="application-with-existing-sso-solutions"></a>Applicazione con soluzioni Single Sign-On esistenti

Per configurare l'accesso SSO per un'applicazione, il portale di Azure fornisce una terza opzione, ovvero Accesso Single Sign-On esistente. Questa opzione consente all'amministratore di creare un collegamento a un'applicazione e di inserirlo nel portale delle app personali per gli utenti selezionati.

Se, ad esempio, un'applicazione è configurata per l'autenticazione degli utenti tramite AD FS 2.0, l'amministratore può usare l'opzione Accesso Single Sign-On esistente per creare un collegamento nel portale delle app personali. Quando si accede al collegamento, viene eseguita l'autenticazione tramite AD FS 2.0 o qualunque soluzione SSO fornita dall'applicazione.


## <a name="next-steps"></a>Passaggi successivi

- Per altre informazioni sulla gestione delle applicazioni, vedere [Gestione di applicazioni con Azure Active Directory](../manage-apps/what-is-application-management.md).
 
- Per informazioni su come integrare un'app SaaS con Azure AD, vedere l'[elenco di esercitazioni sull'integrazione di app SaaS](../saas-apps/tutorial-list.md).
 
- Per altre informazioni sulla gestione di app con Azure AD, vedere l'[introduzione a Single Sign-On e alla gestione dell'accesso alle app con Azure Active Directory](../manage-apps/what-is-single-sign-on.md).
 
- Per altre informazioni sul provisioning degli utenti, vedere [Automatizzare il provisioning e il deprovisioning utenti in applicazioni SaaS con Azure Active Directory](../manage-apps/user-provisioning.md).

<!--Image references-->
[1]: ./media/active-directory-saas-access-panel-introduction/01.png
[2]: ./media/active-directory-saas-access-panel-introduction/02.png
[4]: ./media/active-directory-saas-access-panel-introduction/04.png
[5]: ./media/active-directory-saas-access-panel-introduction/05.png
